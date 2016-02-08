<properties
   pageTitle="Verwenden von Elasticsearch als Service Fabric-Anwendungsüberwachungsspeicher | Microsoft Azure"
   description="Beschreibt, wie Service Fabric-Anwendungen Elasticsearch und Kibana für die Speicherung, Indizierung und Suche auf der Grundlage von Anwendungsablaufverfolgungen (Protokollen) verwenden können."
   services="service-fabric"
   documentationCenter=".net"
   authors="karolz-ms"
   manager="adegeo"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="01/20/2016"
   ms.author="karolz@microsoft.com"/>

# Verwenden von Elasticsearch als Service Fabric-Anwendungsüberwachungsspeicher
## Einführung
Dieser Artikel beschreibt, wie [Azure Service Fabric](https://azure.microsoft.com/documentation/services/service-fabric/)-Anwendungen **Elasticsearch** und **Kibana** als Anwendungsüberwachungsspeicher sowie zur Indizierung und Suche verwenden können. [Elasticsearch](https://www.elastic.co/guide/index.html) ist ein verteiltes und skalierbares Open Source-Modul für Such- und Analysevorgänge in Echtzeit, das sich hervorragend für diese Aufgabe eignet. Es kann auf virtuellen, unter Microsoft Azure ausgeführten Windows- oder Linux-Computern installiert werden. ElasticSearch zeichnet sich durch eine überaus effiziente Verarbeitung *strukturierter* Ablaufverfolgungen aus, die von Technologien wie **Ereignisablaufverfolgung für Windows (Event Tracing for Windows, ETW)** generiert werden.

ETW wird während der Service Fabric-Laufzeit verwendet, um Diagnoseinformationen (Ablaufverfolgungen) zu erfassen. Es ist auch die empfohlene Methode für Service Fabric-Anwendungen zum Erfassen ihrer Diagnoseinformationen. Dies ermöglicht die Korrelation zwischen Ablaufverfolgungen der Laufzeit und Ablaufverfolgungen der Anwendung, was die Problembehandlung erleichtert. Die Service Fabric-Projektvorlagen in Visual Studio enthalten eine (auf der Klasse **EventSource** basierende) Protokollierungs-API, die standardmäßig ETW-Ablaufverfolgungen ausgibt. Eine allgemeine Übersicht der Service Fabric-Anwendungsablaufverfolgung mit ETW finden Sie unter [Überwachen und Diagnostizieren von Diensten in einer Entwicklungsumgebung auf einem lokalen Computer](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

Damit die Ablaufverfolgungsdaten in Elasticsearch angezeigt werden können, müssen sie in Echtzeit (also während der Anwendungsausführung) an den Service Fabric-Clusterknoten erfasst und an den Elasticsearch-Endpunkt gesendet werden. Für die Erfassung von Ablaufverfolgungen stehen zwei Hauptoptionen zur Verfügung:

+ **In-Process-Ablaufverfolgungserfassung**: Die Diagnosedaten werden von der Anwendung (genauer gesagt: vom Dienstprozess) an den Ablaufverfolgungsspeicher (Elasticsearch) gesendet.

+ **Out-of-Process-Ablaufverfolgungserfassung**: Die Ablaufverfolgungen vom Dienstprozess bzw. den Prozessen werden von einem separaten Agenten erfasst und an den Ablaufverfolgungsspeicher gesendet.

Im weiteren Verlauf wird beschrieben, wie Elasticsearch in Azure eingerichtet wird, welche Vor- und Nachteile die beiden Erfassungsoptionen haben, und wie Sie einen Service Fabric-Dienst für das Senden von Daten an Elasticsearch konfigurieren.


## Einrichten von Elasticsearch für Azure
Am einfachsten lässt sich der Elasticsearch-Dienst für Azure mit [**Azure-Ressourcen-Manager-Vorlagen**](../resource-group-overview.md) einrichten. Eine umfassende [Azure-Ressourcen-Manager-Schnellstartvorlage für Elasticsearch](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) steht im Repository für Azure-Schnellstartvorlagen zur Verfügung. Diese Vorlage verwendet separate Speicherkonten für Skalierungseinheiten (Gruppen von Knoten). Sie kann auch separate Client- und Serverknoten mit unterschiedlichen Konfigurationen und unterschiedlicher Anzahl angehängter Datenträger bereitstellen.

Hier verwenden wir allerdings eine andere Vorlage namens **ES-MultiNode** aus der [ELK-Verzweigung von Microsoft Patterns and Practices](https://github.com/mspnp/semantic-logging/tree/elk/). Diese Vorlage ist etwas einfacher zu verwenden und erstellt einen Elasticsearch-Cluster, der standardmäßig mittels HTTP-Standardauthentifizierung geschützt ist. Bevor Sie fortfahren, laden Sie bitte das [ELK-Repository der Microsoft Patterns and Practices](https://github.com/mspnp/semantic-logging/tree/elk/) von GitHub auf Ihren Computer (entweder durch Klonen des Repositorys oder Herunterladen einer Zipdatei). Die Vorlage „ES-MultiNode“ befindet sich im gleichnamigen Ordner.
>[AZURE.NOTE] Die Vorlage „ES-MultiNode“ und die dazugehörigen Skripts unterstützen momentan Version 1.7 von Elasticsearch. Unterstützung für Elasticsearch 2.0 wird zu einem späteren Zeitpunkt hinzugefügt.

### Vorbereiten eines Computers für die Ausführung von Elasticsearch-Installationsskripts
Am einfachsten lässt sich die Vorlage „ES-MultiNode“ über das bereitgestellte Azure PowerShell-Skript namens `CreateElasticSearchCluster` verwenden. Für die Verwendung dieses Skripts müssen Sie PowerShell-Module und ein Tool namens **openssl** installieren. Letzteres wird zum Erstellen eines SSH-Schlüssels benötigt, der die Remoteverwaltung Ihres Elasticsearch-Clusters ermöglicht.

Beachten Sie, dass das Skript `CreateElasticSearchCluster` die Verwendung der Vorlage „ES-MultiNode“ auf einem Windows-Computer vereinfacht. Die Vorlage kann zwar auch auf einem Windows-fremden Computer verwendet werden, ein solches Szenario ist jedoch nicht Gegenstand dieses Artikels.

1. Installieren Sie die [**Azure PowerShell-Module**](http://go.microsoft.com/fwlink/p/?linkid=320376), sofern sie noch nicht installiert sind. Klicken Sie bei Aufforderung auf **Ausführen** und dann auf **Installieren**.
>[AZURE.NOTE] In Version 1.0 von Azure PowerShell gibt es eine wichtige Veränderung. „CreateElasticSearchCluster“ ist für Azure PowerShell 0.9.8 konzipiert. Die Vorschauversion von Azure PowerShell 1.0 wird nicht unterstützt. Ein mit Azure PowerShell 1.0 kompatibles Skript wird zu einem späteren Zeitpunkt bereitgestellt.

2. Das Tool **openssl** ist in der Distribution von [**Git für Windows**](http://www.git-scm.com/downloads) enthalten. Installieren Sie jetzt [Git für Windows](http://www.git-scm.com/downloads), sofern noch nicht geschehen. (Die Standardinstallationsoptionen sind OK.)

3. Falls Git bereits installiert wurde, aber nicht im Systempfad enthalten ist, führen Sie in einem Fenster von Microsoft Azure PowerShell die folgenden Befehle aus:

    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```

    Ersetzen Sie `<Git installation folder>` durch den Speicherort von Git auf Ihrem Computer (standardmäßig **C:\\Programme\\Git**). Beachten Sie das Semikolon am Anfang des ersten Pfads.

4. Vergewissern Sie sich, dass Sie bei Azure angemeldet sind (über das Cmdlet [**Add-AzureAccount**](https://msdn.microsoft.com/library/azure/dn790372.aspx)), und dass Sie das Abonnement ausgewählt haben, das zum Erstellen Ihres Elasticsearch-Clusters verwendet werden soll ([**Select-AzureSubscription**](https://msdn.microsoft.com/library/azure/dn790367.aspx)).

5. Ändern Sie das aktuelle Verzeichnis in den Ordner „ES-MultiNode“, sofern noch nicht geschehen.

### Ausführen des Skripts „CreateElasticSearchCluster“
Öffnen Sie vor dem Ausführen des Skripts die Datei `azuredeploy-parameters.json`, und überprüfen Sie die Werte für die Skriptparameter, bzw. geben Sie entsprechende Werte an. Folgende Parameter stehen zur Verfügung:

|Parametername |Beschreibung|
|-----------------------  |--------------------------|
|dnsNameForLoadBalancerIP |Dies ist der Name, der zum Erstellen des öffentlich sichtbaren Domänennamensystem-Namens (DNS) für den Elasticsearch-Cluster verwendet wird (dabei wird die Azure-Regionsdomäne dem angegebenen Namen angehängt.) Wenn der Parameterwert also beispielsweise „myBigCluster“ lautet, und als Azure-Region „USA, Westen“ ausgewählt ist, erhält der Cluster den DNS-Namen **myBigCluster.westus.cloudapp.azure.com**. <br /><br />Dieser Name fungiert auch als Namensstamm für viele Artefakte des Elasticsearch-Clusters (etwa für Datenknotennamen).|
|storageAccountPrefix |Das Präfix für die Speicherkonten, die für den Elasticsearch-Cluster erstellt werden. <br /><br /> Die aktuelle Version der Vorlage verwendet ein gemeinsam genutztes Speicherkonto. Dies kann sich aber zukünftig noch ändern.|
|adminUsername |Der Name des Administratorkontos für die Verwaltung des Elasticsearch-Clusters (entsprechende SSH-Schlüssel werden automatisch generiert.)|
|dataNodeCount |Die Anzahl von Knoten im Elasticsearch-Cluster. Bei der aktuellen Version des Skripts wird nicht zwischen Daten- und Abfrageknoten unterschieden. Alle Knoten übernehmen beide Rollen.|
|dataDiskSize |Die Datenträgergröße (in Gigabyte), die für die einzelnen Datenknoten reserviert wird. Jeder Knoten erhält vier Datenträger, die ausschließlich für den Elasticsearch-Dienst vorgesehen sind.|
|region |Der Name der Azure-Region, in der sich der Elasticsearch-Cluster befinden soll.|
|esClusterName |Der interne Name des Elasticsearch-Clusters. <br /><br />Dieser Wert muss vom Standardwert abweichen, es sei denn, Sie planen, mehrere Elasticsearch-Cluster im gleichen virtuellen Netzwerk auszuführen. Dies wird derzeit nicht von der Vorlage „ES-MultiNode“ unterstützt.|
|esUserName esPassword |Die Anmeldeinformationen für den Benutzer, der für den Zugriff auf den Elasticsearch-Cluster (mit HTTP-Standardauthentifizierung) konfiguriert wird.|

Nun können Sie das Skript ausführen. Verwenden Sie den folgenden Befehl: ```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name>
```. Dabei ist `<es-group-name>` der Name der Azure-Ressourcengruppe mit allen Clusterressourcen.

>[AZURE.NOTE] Falls das Cmdlet „Test-AzureResourceGroup“ eine Ausnahme vom Typ „NullReferenceException“ zurückgibt, haben Sie vergessen, sich bei Azure anzumelden (`Add-AzureAccount`).

Tritt bei der Skriptausführung ein Fehler auf, der auf einen falschen Vorlagenparameterwert zurückzuführen ist, korrigieren Sie die Parameterdatei, und führen Sie das Skript mit einem anderen Ressourcengruppennamen erneut aus. Sie können auch den gleichen Ressourcengruppennamen verwenden und angeben, dass das Skript die alte Version bereinigen soll. Hierzu müssen Sie dem Skriptaufruf den Parameter `-RemoveExistingResourceGroup` hinzufügen.

### Ergebnis der Ausführung des Skripts „CreateElasticSearchCluster“
Nach der Ausführung des Skripts `CreateElasticSearchCluster` werden die folgenden Hauptartefakte erstellt. Aus Gründen der Übersichtlichkeit wird davon ausgegangen, dass als Parameterwert für `dnsNameForLoadBalancerIP` die Zeichenfolge „myBigCluster“ verwendet und der Cluster im Westen der USA erstellt wurde.

|Artefakt|Name, Ort und Anmerkungen|
|----------------------------------|----------------------------------|
|SSH-Schlüssel für die Remoteverwaltung |Datei „myBigCluster.key“ (in dem Verzeichnis, in dem „CreateElasticSearchCluster“ ausgeführt wurde). <br /><br />Mit diesem Schlüssel kann eine Verbindung mit dem Verwaltungsknoten und (über den Verwaltungsknoten) mit Datenknoten im Cluster hergestellt werden.|
|Verwaltungsknoten |myBigCluster-admin.westus.cloudapp.azure.com <br /><br />Hierbei handelt es sich um einen dedizierten virtuellen Computer für die Remoteverwaltung des Elasticsearch-Clusters – den einzigen, der externe SSH-Verbindungen zulässt. Er wird im gleichen virtuellen Netzwerk ausgeführt wie die Elasticsearch-Clusterknoten, führt aber keine Elasticsearch-Dienste aus.|
|Datenknoten |myBigCluster1 … myBigCluster*N* <br /><br />Datenknoten, die Elasticsearch- und Kibana-Dienste ausführen. Sie können über SSH eine Verbindung mit den einzelnen Knoten herstellen (allerdings nur über den Verwaltungsknoten).|
|Elasticsearch-Cluster |http://myBigCluster.westus.cloudapp.azure.com/es/ <br /><br />Bei der obigen Angabe handelt es sich um den primären Endpunkt für den Elasticsearch-Cluster (beachten Sie das Suffix „/es“.) Dieser ist durch die Standard-HTTP-Authentifizierung geschützt (die Anmeldeinformationen wurden mithilfe der Parameter „esUserName“/„esPassword“ der Vorlage „ES-MultiNode“ angegeben). Zur grundlegenden Clusterverwaltung ist für den Cluster auch das Head-Plug-In (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head) installiert.|
|Kibana-Dienst |http://myBigCluster.westus.cloudapp.azure.com <br /><br />Der Dienst Kibana wird zum Anzeigen von Daten aus dem erstellten Elasticsearch-Cluster eingerichtet. Er wird durch die gleichen Authentifizierungsanmeldeinformationen wie der Cluster selbst geschützt.|

## Gegenüberstellung von In-Process- und Out-of-Process-Ablaufverfolgungserfassung
In der Einführung haben wir zwei grundlegende Arten für die Erfassung von Diagnosedaten angesprochen: In-Process und Out-of-Process. Beide Arten haben Vor- und Nachteile.

Vorteile der **In-Process-Ablaufverfolgungserfassung**:

1. *Einfache Konfiguration und Bereitstellung*

    * Die Konfiguration der Erfassung von Diagnosedaten ist Teil der Anwendungskonfiguration. Es ist einfach, sie stets mit dem Rest der Anwendung „synchron“ zu halten.

    * Eine anwendungs- oder dienstspezifische Konfiguration ist problemlos möglich.

    * Für die Out-of-Process-Ablaufverfolgungserfassung ist in der Regel eine separate Bereitstellung und Konfiguration des Diagnose-Agents erforderlich. Dies bedeutet eine zusätzliche Verwaltungsaufgabe und eine potenzielle Fehlerquelle. Die jeweilige Agent-Technologie lässt häufig nur eine Instanz des Agents pro virtuellem Computer (Knoten) zu. Dies bedeutet, dass die Konfiguration der Erfassung der Diagnosedaten von allen Anwendungen und Diensten, die auf diesem Knoten ausgeführt werden, gemeinsam verwendet wird.

2. *Flexibilität*

    * Die Anwendung kann die Daten an ein beliebiges Ziel senden – vorausgesetzt, es ist eine Clientbibliothek vorhanden, die das gewünschte Datenspeichersystem unterstützt. Bei Bedarf können neue Senken hinzugefügt werden.

    * Komplexe Erfassungs-, Filter- und Datenaggregierungsregeln können implementiert werden.

    * Die Out-of-Process-Ablaufverfolgungserfassung wird häufig durch die vom Agent unterstützten Datensenken beschränkt. Einige Agents sind erweiterbar.

3. *Zugriff auf interne Anwendungsdaten und Kontext*

    * Das Diagnosesubsystem innerhalb des Anwendungs-/Dienstprozesses kann die Ablaufverfolgungen problemlos mit Kontextinformationen ergänzen.

    * Bei der Out-of-Process-Methode müssen die Daten über einen prozessübergreifenden Kommunikationsmechanismus wie etwa die Ereignisablaufverfolgung für Windows an einen Agent gesendet werden. Dies hat unter Umständen weitere Einschränkungen zur Folge.

Vorteile der **Out-of-Process-Ablaufverfolgungserfassung**:

1. *Die Möglichkeit der Überwachung der Anwendung und Sammlung von Absturzabbildern*

    * Die In-Process-Ablaufverfolgungserfassung ist unter Umständen nicht erfolgreich, wenn die Anwendung abstürzt oder nicht startet. Ein unabhängiger Agent kann hier deutlich besser entscheidende Problembehandlungsinformationen erfassen.<br /><br />

2. *Ausgereiftheit, Stabilität und bewährte Leistung*

    * Ein von einem Plattformanbieter entwickelter Agent (etwa der Microsoft Azure-Diagnose-Agent) wurde ausgiebig getestet und in der Praxis optimiert.

    * Bei der In-Process-Ablaufverfolgungserfassung muss darauf geachtet werden, dass das Senden von Diagnosedaten aus einem Anwendungsprozess nicht die Hauptaufgaben der Anwendung beeinträchtigt bzw. zu Timing- oder Leistungsproblemen führt. Ein unabhängig ausgeführter Agent ist hier weniger anfällig und in der Regel speziell darauf ausgelegt, die Auswirkungen auf das System möglichst gering zu halten.

Natürlich ist es möglich, beide Ansätze zu kombinieren und von Ihnen zu profitieren. In der Tat ist dies möglicherweise die beste Lösung für viele Anwendungen.

Hier verwenden wir die Bibliothek **Microsoft.Diagnostic.Listeners** und die In-Process-Ablaufverfolgungserfassung, um Daten aus einer Service Fabric-Anwendung an den Elasticsearch-Cluster zu senden.

## Senden von Diagnosedaten an Elasticsearch mithilfe der Listeners-Bibliothek
Die Bibliothek „Microsoft.Diagnostic.Listeners“ ist Teil der PartyCluster-Service Fabric-Beispielanwendung. Gehen Sie zur Verwendung wie folgt vor:

1. Laden Sie das [PartyCluster-Beispiel](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) von GitHub herunter.

2. Kopieren Sie jeweils den gesamten Ordner der Projekte „Microsoft.Diagnostics.Listeners“ und „Microsoft.Diagnostics.Listeners.Fabric“ aus dem Verzeichnis des PartyCluster-Beispiels in den Projektmappenordner der Anwendung, die die Daten an Elasticsearch senden soll.

3. Öffnen Sie die Ziellösung, klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektmappenknoten, und wählen Sie **Vorhandenes Projekt hinzufügen** aus. Fügen Sie der Projektmappe das Projekt „Microsoft.Diagnostics.Listeners“ hinzu. Wiederholen Sie den Vorgang für das Projekt „Microsoft.Diagnostics.Listeners.Fabric“.

4. Fügen Sie einen Projektverweis von Ihren Dienstprojekten zu den beiden hinzugefügten Projekten hinzu. (Jeder Dienst, der Daten an Elasticsearch senden soll, muss auf „Microsoft.Diagnostics.EventListeners“ und „Microsoft.Diagnostics.EventListeners.Fabric“ verweisen.)

    ![Projektverweise auf die Bibliotheken „Microsoft.Diagnostics.EventListeners“ und „Microsoft.Diagnostics.EventListeners.Fabric“][1]

### Vorschauversion von Service Fabric (November 2015) und Paket „Microsoft.Diagnostics.Tracing NuGet“
Anwendungen, die mit der Vorschauversion von November 2015 des Service Fabric-Ziels **.NET Framework 4.5.1** erstellt wurden. Dies ist die höchste Version von .NET Framework, die von Azure zum Zeitpunkt der Vorschauveröffentlichung unterstützt wurde. Leider fehlen bei dieser Framework-Version bestimmte EventListener-APIs, die für die Bibliothek „Microsoft.Diagnostics.Listeners“ benötigt werden. Da „EventSource“ (die Komponente, die die Grundlage für Protokollierungs-APIs in Fabric-Anwendungen bildet) und „EventListener“ eng zusammenhängen, muss jedes Projekt, das die Bibliothek „Microsoft.Diagnostics.Listeners“ verwendet, eine alternative Implementierung von „EventSource“ verwenden. Diese wird durch das von Microsoft erstellte **Paket Microsoft.Diagnostics.Tracing NuGet** bereitgestellt. Dieses Paket ist vollständig mit „EventSource“ aus dem Framework abwärtskompatibel, sodass außer einer Änderung der referenzierten Namespaces keine weiteren Codeänderungen erforderlich sein sollten.

Führen Sie für jedes Dienstprojekt, das Daten an Elasticsearch senden soll, die folgenden Schritte aus, um mit der Verwendung der Microsoft.Diagnostics.Tracing-Implementierung der Klasse „EventSource“ zu beginnen:

1. Klicken Sie mit der rechten Maustaste auf das Dienstprojekt, und wählen Sie **NuGet-Pakete verwalten** aus.

2. Wechseln Sie zur Paketquelle „nuget.org“ (sofern nicht bereits ausgewählt), und suchen Sie nach **Microsoft.Diagnostics.Tracing**.

3. Installieren Sie das Paket `Microsoft.Diagnostics.Tracing.EventSource` (einschließlich Abhängigkeiten).

4. Öffnen Sie in Ihrem Dienstprojekt die Datei **ServiceEventSource.cs** oder **ActorEventSource.cs**, und ersetzen Sie die Direktive `using System.Diagnostics.Tracing` am Dateianfang durch die Direktive `using Microsoft.Diagnostics.Tracing`.

Sobald **.NET Framework 4.6** von Microsoft Azure unterstützt wird, sind diese Schritte nicht mehr nötig.

### Instanziierung und Konfiguration des Elasticsearch-Listeners
Im letzten Schritt zum Senden von Diagnosedaten an Elasticsearch wird eine Instanz von `ElasticSearchListener` erstellt und mit Elasticsearch-Verbindungsdaten konfiguriert. Der Listener erfasst automatisch alle Ereignisse, die über die im Dienstprojekt definierten EventSource-Klassen ausgelöst werden. Da er während der Lebensdauer des Diensts aktiv sein muss, wird er am besten im Initialisierungscode des Diensts erstellt. Hier sehen Sie ein Beispiel für den Initialisierungscode eines zustandslosen Diensts nach den erforderlichen Änderungen (Ergänzungen sind in den Kommentaren mit `****` gekennzeichnet):

```csharp
using System;
using System.Diagnostics;
using System.Fabric;
using System.Threading;

// **** Add the following directives
using Microsoft.Diagnostics.EventListeners;
using Microsoft.Diagnostics.EventListeners.Fabric;

namespace Stateless1
{
    public class Program
    {
        public static void Main(string[] args)
        {
            try
            {
                using (FabricRuntime fabricRuntime = FabricRuntime.Create())
                {

                    // **** Instantiate ElasticSearchListener
                    var configProvider = new FabricConfigurationProvider("ElasticSearchEventListener");
                    ElasticSearchListener esListener = null;
                    if (configProvider.HasConfiguration)
                    {
                        esListener = new ElasticSearchListener(configProvider);
                    }

                    // This is the name of the ServiceType that is registered with FabricRuntime.
                    // This name must match the name defined in the ServiceManifest. If you change
                    // this name, please change the name of the ServiceType in the ServiceManifest.
                    fabricRuntime.RegisterServiceType("Stateless1Type", typeof(Stateless1));

                    ServiceEventSource.Current.ServiceTypeRegistered(
						Process.GetCurrentProcess().Id,
						typeof(Stateless1).Name);

                    Thread.Sleep(Timeout.Infinite);

                    // **** Ensure that the ElasticSearchListner instance is not garbage-collected prematurely
                    GC.KeepAlive(esListener);

                }
            }
            catch (Exception e)
            {
                ServiceEventSource.Current.ServiceHostInitializationFailed(e);
                throw;
            }
        }
    }
}
```

Die Elasticsearch-Verbindungsdaten müssen in einem separaten Abschnitt der Dienstkonfigurationsdatei (**PackageRoot\\Config\\Settings.xml**) platziert werden. Der Abschnittsname muss dem an den Konstruktor `FabricConfigurationProvider` übergebenen Wert entsprechen. Beispiel:

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
Bei den Werten `serviceUri`, `userName` und `password` handelt es sich um die Endpunktadresse des Elasticsearch-Clusters bzw. um den Elasticsearch-Benutzernamen und das Kennwort. `indexNamePrefix` ist das Präfix für Elasticsearch-Indizes. Die Bibliothek „Microsoft.Diagnostics.Listeners“ erstellt täglich einen neuen Index für die Daten.

### Überprüfung
Das ist alles! Wenn der Dienst nun ausgeführt wird, sendet er Ablaufverfolgungen an den in der Konfiguration angegebenen Elasticsearch-Dienst. Öffnen Sie zum Überprüfen die Kibana-UI für die entsprechende Elasticsearch-Zielinstanz (in unserem Beispiel wäre die Seitenadresse http://myBigCluster.westus.cloudapp.azure.com/), und vergewissern Sie sich, dass tatsächlich Indizes mit dem für die Instanz `ElasticSearchListener` gewählten Namenspräfix erstellt und mit Daten gefüllt wurden.

![Kibana mit PartyCluster-Anwendungsereignissen][2]

## Nächste Schritte
- [Weitere Informationen zur Diagnose und Überwachung eines Service Fabric-Diensts](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png

<!---HONumber=AcomDC_0128_2016-->