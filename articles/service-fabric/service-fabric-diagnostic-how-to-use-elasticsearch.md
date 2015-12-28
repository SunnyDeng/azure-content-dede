<properties
   pageTitle="Verwenden von ElasticSearch als Service Fabric-Anwendungsüberwachungsspeicher | Microsoft Azure"
   description="Beschreibt, wie Service Fabric-Anwendungen ElasticSearch und Kibana für die Speicherung, Indizierung und Suche auf der Grundlage von Anwendungsablaufverfolgungen (Protokolle) verwenden können."
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
   ms.date="11/02/2015"
   ms.author="karolz@microsoft.com"/>

# Verwenden von ElasticSearch als Service Fabric-Anwendungsüberwachungsspeicher
## Einführung
Dieser Artikel beschreibt, wie [Service Fabric](http://azure.microsoft.com/documentation/services/service-fabric/)-Anwendungen **ElasticSearch** und **Kibana** als Anwendungsüberwachungsspeicher sowie zur Indizierung und Suche verwenden können. [ElasticSearch](https://www.elastic.co/guide/index.html) ist ein quelloffenes, verteiltes und skalierbares Modul für Such- und Analysevorgänge in Echtzeit, das sich hervorragend für diese Aufgabe eignet und auf virtuellen Windows- oder Linux-Computern mit Microsoft Azure installiert werden kann. ElasticSearch zeichnet sich durch eine überaus effiziente Verarbeitung *strukturierter* Ablaufverfolgungen aus, die von Technologien wie **Ereignisablaufverfolgung für Windows (ETW)** generiert werden.

ETW wird von der Service Fabric-Laufzeit zum Abrufen von Diagnoseinformationen (Ablaufverfolgungen) verwendet und ist zugleich die empfohlene Methode für das Abrufen von Diagnoseinformationen durch Service Fabric-Anwendungen. Dies ermöglicht die Korrelation zwischen Ablaufverfolgungen der Laufzeit und Ablaufverfolgungen der Anwendung, was die Problembehandlung erleichtert. Die Service Fabric-Projektvorlagen in Visual Studio enthalten eine (auf der Klasse **EventSource** basierende) Protokollierungs-API, die standardmäßig ETW-Ablaufverfolgungen ausgibt. Eine allgemeine Übersicht über die Service Fabric-Anwendungsnachverfolgung mit ETW finden Sie [in diesem Artikel](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md).

Damit die Ablaufverfolgungsdaten in ElasticSearch angezeigt werden können, müssen sie in Echtzeit (also während der Anwendungsausführung) an den Service Fabric-Clusterknoten erfasst und an den ElasticSearch-Endpunkt gesendet werden. Für die Erfassung von Ablaufverfolgungen stehen zwei Hauptoptionen zur Verfügung:

+ **In-Process-Ablaufverfolgungserfassung**: Die Diagnosedaten werden von der Anwendung (genauer gesagt: vom Dienstprozess) an den Ablaufverfolgungsspeicher (ElasticSearch) gesendet.

+ **Out-of-Process-Ablaufverfolgungserfassung**: Die Ablaufverfolgungen von Dienstprozessen werden von einem separaten Agent erfasst und an den Ablaufverfolgungsspeicher gesendet.

Im weiteren Verlauf des Artikels erfahren Sie, wie ElasticSearch in Azure eingerichtet wird, welche Vor- und Nachteile die beiden Erfassungsoptionen haben und wie Sie einen Fabric-Dienst für das Senden von Daten an ElasticSearch konfigurieren.


## Einrichten von ElasticSearch für Azure
Am einfachsten lässt sich der ElasticSearch-Dienst für Azure mit [**Azure ARM-Vorlagen**](../resource-group-overview.md) einrichten. Eine umfassende [ARM-Schnellstartvorlage für ElasticSearch](https://github.com/Azure/azure-quickstart-templates/tree/master/elasticsearch) steht im Repository für Azure-Schnellstartvorlagen zur Verfügung. Diese Vorlage verwendet separate Speicherkonten für Skalierungseinheiten (Knotengruppen) und kann separate Client- und Serverknoten mit unterschiedlichen Konfigurationen und unterschiedlicher Anzahl von angefügten Datenträgern bereitstellen.

In diesem Artikel verwenden wir allerdings eine Vorlage namens **ES-MultiNode** aus der [ELK-Verzweigung von Microsoft Patterns & Practices](https://github.com/mspnp/semantic-logging/tree/elk/). Diese Vorlage ist etwas einfacher zu verwenden und erstellt einen ElasticSearch-Cluster, der standardmäßig mittels HTTP-Standardauthentifizierung geschützt ist. Laden Sie bei GitHub zunächst das [ELK-Repository von Microsoft P & P](https://github.com/mspnp/semantic-logging/tree/elk/) auf Ihren Computer herunter. Klonen Sie hierzu entweder das Repository, oder laden Sie eine ZIP-Datei herunter. Die Vorlage „ES-MultiNode“ befindet sich im gleichnamigen Ordner.
>[AZURE.NOTE]„ES-MultiNode“ und die dazugehörigen Skripts unterstützen momentan Version 1.7 von ElasticSearch. Unterstützung für ElasticSearch 2.0 wird zu einem späteren Zeitpunkt hinzugefügt.

### Vorbereiten eines Computers für die Ausführung von ElasticSearch-Installationsskripts
Am einfachsten lässt sich die Vorlage „ES-MultiNode“ über das bereitgestellte PowerShell-Skript namens `CreateElasticSearchCluster` verwenden. Für die Verwendung dieses Skripts müssen Sie Azure PowerShell-Module und ein Tool namens „openssl“ installieren. Letzteres wird zum Erstellen eines SSH-Schlüssels benötigt, der die Remoteverwaltung Ihres ElasticSearch-Clusters ermöglicht.

Hinweis: Das Skript `CreateElasticSearchCluster` vereinfacht die Verwendung der Vorlage „ES-MultiNode“ auf einem Windows-Computer. Die Vorlage kann zwar auch auf einem Windows-fremden Computer verwendet werden, ein solches Szenario ist jedoch nicht Gegenstand dieses Artikels.

1. Installieren Sie die [**Azure PowerShell-Module**](http://go.microsoft.com/fwlink/p/?linkid=320376), sofern sie noch nicht installiert sind. Klicken Sie in der Nachfrage auf „Ausführen“ und dann auf „Installieren“.
>[AZURE.NOTE]In Version 1.0 von Azure PowerShell gibt es eine wichtige Veränderung. „CreateElasticSearchCluster“ ist für Azure PowerShell 0.9.8 konzipiert. Die Vorschauversion von Azure PowerShell 1.0 wird noch nicht unterstützt. Ein mit Azure PowerShell 1.0 kompatibles Skript wird zu einem späteren Zeitpunkt bereitgestellt.

2. Das Tool **openssl** ist in der Distribution von [**Git für Windows**](http://www.git-scm.com/downloads) enthalten. Installieren Sie jetzt [Git für Windows](http://www.git-scm.com/downloads) mit den standardmäßigen Installationsoptionen, sofern noch nicht geschehen.

3. Falls Git bereits installiert wurde, aber nicht im Systempfad enthalten ist, führen Sie im Fenster von Microsoft Azure PowerShell die folgenden Befehle aus:

    ```powershell
    $ENV:PATH += ";<Git installation folder>\usr\bin"
    $ENV:OPENSSL_CONF = "<Git installation folder>\usr\ssl\openssl.cnf"
    ```

    Ersetzen Sie `<Git installation folder>` durch den Speicherort von Git auf Ihrem Computer (standardmäßig *C:\\Programme\\Git*). Beachten Sie das Semikolon am Anfang des ersten Pfads.

4. Vergewissern Sie sich, dass Sie bei Azure angemeldet sind (über das Cmdlet [*Add-AzureAccount*](https://msdn.microsoft.com/library/azure/dn790372.aspx)) und dass Sie das Abonnement ausgewählt haben, das zum Erstellen Ihres ElasticSearch-Clusters verwendet werden soll ([*Select-AzureSubscription*](https://msdn.microsoft.com/library/azure/dn790367.aspx)).

5. Ändern Sie das aktuelle Verzeichnis in den Ordner „ES-MultiNode“, sofern noch nicht geschehen.

### Ausführen des Skripts „CreateElasticSearchCluster“
Öffnen Sie vor dem Ausführen des Skripts die Datei `azuredeploy-parameters.json`, und überprüfen Sie die Werte für die Skriptparameter, bzw. geben Sie entsprechende Werte an. Folgende Parameter stehen zur Verfügung:

|Parametername |Beschreibung|
|-----------------------  |--------------------------|
|dnsNameForLoadBalancerIP |Dies ist der Name, der zum Erstellen des öffentlich sichtbaren DNS-Namens für den ElasticSearch-Cluster verwendet wird. Dabei wird die Azure-Regionsdomäne an den angegebenen Namen angehängt. Wenn der Parameterwert also beispielsweise „MyBigCluster“ lautet und als Azure-Region „West US“ ausgewählt ist, erhält der Cluster den DNS-Namen „myBigCluster.westus.cloudapp.azure.com“. <br /><br />Dieser Name fungiert auch als Namensstamm für viele Artefakte des ElasticSearch-Clusters (etwa für Datenknotennamen).|
|storageAccountPrefix |Das Präfix für die Speicherkonten, die für den ElasticSearch-Cluster erstellt werden. <br /><br /> Die aktuelle Version der Vorlage verwendet ein gemeinsam genutztes Speicherkonto. Dies kann sich aber später noch ändern.|
|adminUsername |Der Name des Administratorkontos für die Verwaltung des ElasticSearch-Clusters. (Entsprechende SSH-Schlüssel werden automatisch generiert.)|
|dataNodeCount |Die Anzahl von Knoten im ElasticSearch-Cluster. Bei der aktuellen Version des Skripts wird nicht zwischen Daten- und Abfrageknoten unterschieden. Alle Knoten übernehmen beide Rollen.|
|dataDiskSize |Die Datenträgergröße (in GB), die für die einzelnen Datenknoten reserviert wird. Jeder Knoten erhält vier Datenträger, die ausschließlich für den ElasticSearch-Dienst vorgesehen sind.|
|region |Der Name der Azure-Region, in der sich der ElasticSearch-Cluster befinden soll.|
|esClusterName |Der interne Name des ElasticSearch-Clusters. <br /><br />Der Standardwert muss nur geändert werden, wenn Sie mehrere ElasticSearch-Cluster im gleichen virtuellen Netzwerk verwenden möchten. Dies wird von der Vorlage „ES-MultiNode“ momentan allerdings nicht unterstützt.|
|esUserName esPassword |Die Anmeldeinformationen für den Benutzer, der für den Zugriff auf den ES-Cluster (mit HTTP-Standardauthentifizierung) konfiguriert wird.|

Nun können Sie das Skript ausführen. Verwenden Sie den folgenden Befehl: ```powershell
CreateElasticSearchCluster -ResourceGroupName <es-group-name>
```. Dabei ist `<es-group-name>` der Name der Azure-Ressourcengruppe mit allen Clusterressourcen.

>[AZURE.NOTE]Falls das Cmdlet „Test-AzureResourceGroup“ eine Ausnahme vom Typ „NullReferenceException“ zurückgibt, haben Sie vergessen, sich bei Azure anzumelden (`Add-AzureAccount`).

Tritt bei der Skriptausführung ein Fehler auf, der auf einen falschen Vorlagenparameterwert zurückzuführen ist, korrigieren Sie die Parameterdatei, und führen Sie das Skript mit einem anderen Ressourcengruppennamen erneut aus. Sie können auch den gleichen Ressourcengruppennamen verwenden und angeben, dass das Skript die alte Version bereinigen soll. Hierzu müssen Sie dem Skriptaufruf den Parameter `-RemoveExistingResourceGroup` hinzufügen.

### Ergebnis der Ausführung des Skripts „CreateElasticSearchCluster“
Nach der Ausführung des Skripts `CreateElasticSearchCluster` werden die folgenden Hauptartefakte erstellt. Aus Gründen der Übersichtlichkeit wird davon ausgegangen, dass als Parameterwert für `dnsNameForLoadBalancerIP` die Zeichenfolge „myBigCluster“ verwendet und der Cluster im Westen der USA erstellt wurde.

|Artefakt|Name, Ort und Anmerkungen|
|----------------------------------|----------------------------------|
|SSH-Schlüssel für die Remoteverwaltung |Datei „myBigCluster.key“ (in dem Verzeichnis, in dem „CreateElasticSearchCluster“ ausgeführt wurde). <br /><br />Mit diesem Schlüssel kann eine Verbindung mit dem Verwaltungsknoten und (über den Verwaltungsknoten) mit Datenknoten im Cluster hergestellt werden.|
|Verwaltungsknoten |myBigCluster-admin.westus.cloudapp.azure.com <br /><br />Hierbei handelt es sich um einen dedizierten virtuellen Computer für die Remoteverwaltung des ElasticSearch-Clusters (der einzige, der externe SSH-Verbindungen zulässt). Er wird im gleichen virtuellen Netzwerk ausgeführt wie die ElasticSearch-Clusterknoten, führt aber keine ElasticSearch-Dienste aus.|
|Datenknoten |myBigCluster1 … myBigCluster*N* <br /><br />Datenknoten, die ElasticSearch- und Kibana-Dienste ausführen. Sie können über SSH eine Verbindung mit den einzelnen Knoten herstellen (allerdings nur über den Verwaltungsknoten).|
|ElasticSearch-Cluster |http://myBigCluster.westus.cloudapp.azure.com/es/ <br /><br />Bei der obigen Angabe handelt es sich um den primären Endpunkt für den ElasticSearch-Cluster. (Beachten Sie das Suffix „/es“.) Dieser ist durch die Standard-HTTP-Authentifizierung geschützt. Die Anmeldeinformationen wurden mithilfe der Parameter „esUserName“/„esPassword“ der Vorlage „ES-MultiNode“ angegeben. Zur grundlegenden Clusterverwaltung ist für den Cluster auch das Head-Plug-In installiert (http://myBigCluster.westus.cloudapp.azure.com/es/_plugin/head).|
|Kibana-Dienst |http://myBigCluster.westus.cloudapp.azure.com <br /><br />Der Kibana-Dienst wird für die Anzeige von Daten aus dem erstellten ElasticSearch-Cluster eingerichtet und ist durch die gleichen Anmeldeinformationen für die Authentifizierung geschützt wie der Cluster selbst.|

## Gegenüberstellung von In-Process- und Out-of-Process-Ablaufverfolgungserfassung
In der Einführung haben wir zwei grundlegende Arten für die Erfassung von Diagnosedaten angesprochen: In-Process und Out-of-Process. Beide Arten haben Vor- und Nachteile.

Vorteile der **In-Process-Ablaufverfolgungserfassung**:

1. *Einfache Konfiguration und Bereitstellung*

    * Die Konfiguration der Erfassung von Diagnosedaten ist Teil der Anwendungskonfiguration und lässt sich problemlos mit der restlichen Anwendung synchronisieren.

    * Eine anwendungs- oder dienstspezifische Konfiguration ist problemlos möglich.

    * Für die Out-of-Process-Ablaufverfolgungserfassung ist in der Regel eine separate Bereitstellung und Konfiguration des Diagnose-Agents erforderlich. Dies bedeutet eine zusätzliche Verwaltungsaufgabe und eine potenzielle Fehlerquelle. Aufgrund der Agent-Technologie kann häufig pro virtuellem Computer (Knoten) nur eine einzelne Instanz des Agents verwendet werden. Das bedeutet, dass die Konfiguration für die Sammlung der Diagnosekonfiguration von allen Programmen und Diensten verwendet wird, die auf diesem Knoten ausgeführt werden.

2. *Flexibilität*

    * Die Anwendung kann die Daten an ein beliebiges Ziel senden – vorausgesetzt, es ist eine Clientbibliothek vorhanden, die das gewünschte Datenspeichersystem unterstützt. Bei Bedarf können neue Senken hinzugefügt werden.

    * Komplexe Erfassungs-, Filter- und Datenaggregierungsregeln können implementiert werden.

    * Die Out-of-Process-Ablaufverfolgungserfassung wird häufig durch die vom Agent unterstützten Datensenken beschränkt. Einige Agents sind erweiterbar.

3. *Zugriff auf interne Anwendungsdaten und Kontext*

    * Das Diagnosesubsystem innerhalb des Anwendungs-/Dienstprozesses kann die Ablaufverfolgungen problemlos mit Kontextinformationen ergänzen.

    * Bei der Out-of-Process-Methode müssen die Daten über einen prozessübergreifenden Kommunikationsmechanismus wie etwa die Ereignisablaufverfolgung für Windows (ETW) an einen Agent gesendet werden. Dies hat unter Umständen weitere Einschränkungen zur Folge.

Vorteile der **Out-of-Process-Ablaufverfolgungserfassung**:

1. *Überwachung der Anwendung und Sammlung von Absturzabbildern*

    * Die In-Process-Ablaufverfolgungserfassung ist unter Umständen nicht erfolgreich, wenn die Anwendung abstürzt oder nicht startet. Ein unabhängiger Agent kann hier deutlich besser entscheidende Problembehandlungsinformationen erfassen.<br /><br />

2. *Ausgereiftheit, Stabilität und bewährte Leistung*

    * Ein von einem Plattformanbieter entwickelter Agent (etwa der Microsoft Azure-Diagnose-Agent) wurde ausgiebig getestet und in der Praxis optimiert.

    * Bei der In-Process-Ablaufverfolgungserfassung muss darauf geachtet werden, dass das Senden von Diagnosedaten aus einem Anwendungsprozess nicht die Hauptaufgaben der Anwendung beeinträchtigt oder zu Timing- oder Leistungsproblemen führt. Ein unabhängig ausgeführter Agent ist hier weniger anfällig und in der Regel speziell darauf ausgelegt, die Auswirkungen auf das System möglichst gering zu halten.

Natürlich lassen sich die Methoden auch kombinieren. Das dürfte häufig auch die beste Lösung sein.

In diesem Artikel verwenden wir die Bibliothek **Microsoft.Diagnostic.Listeners** und die In-Process-Ablaufverfolgungserfassung, um Daten aus einer Service Fabric-Anwendung an den ElasticSearch-Cluster zu senden.

## Senden von Diagnosedaten an ElasticSearch mithilfe der Listener-Bibliothek
Die Bibliothek „Microsoft.Diagnostic.Listeners“ ist Teil der Party Cluster-Fabric-Beispielanwendung. Gehen Sie zur Verwendung wie folgt vor:

1. Laden Sie das [Party Cluster-Beispiel](https://github.com/Azure-Samples/service-fabric-dotnet-management-party-cluster) von GitHub herunter.

2. Kopieren Sie jeweils den gesamten Ordner der Projekte „Microsoft.Diagnostics.Listeners“ und „Microsoft.Diagnostics.Listeners.Fabric“ aus dem Verzeichnis des Party Cluster-Beispiels in den Projektmappenordner der Anwendung, die die Daten an ElasticSearch senden soll.

3. Öffnen Sie die Ziellösung, klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektmappenknoten, und wählen Sie „Vorhandenes Projekt hinzufügen“ aus. Fügen Sie der Projektmappe das Projekt „Microsoft.Diagnostics.Listeners“ hinzu. Wiederholen Sie den Vorgang für das Projekt „Microsoft.Diagnostics.Listeners.Fabric“.

4. Fügen Sie einen Projektverweis von Ihren Dienstprojekten zu den beiden hinzugefügten Projekten hinzu. (Jeder Dienst, der Daten an ElasticSearch senden soll, muss auf „Microsoft.Diagnostics.EventListeners“ und „Microsoft.Diagnostics.EventListeners.Fabric“ verweisen.)

    ![Projektverweise auf die Bibliotheken „Microsoft.Diagnostics.EventListeners“ und „Microsoft.Diagnostics.EventListeners.Fabric“][1]

### Vorschauversion von Service Fabric (November 2015) und Paket „Microsoft.Diagnostics.Tracing NuGet“
Anwendungen, die mit der Vorschauversion von Service Fabric (November 2015) erstellt wurden, sind auf **.NET Framework 4.5.1** ausgerichtet, da dies die höchste Version von .NET Framework ist, die Azure zum Zeitpunkt der Veröffentlichung der Vorschauversion unterstützt. Leider fehlen bei dieser Framework-Version bestimmte EventListener-APIs, die für die Bibliothek „Microsoft.Diagnostics.Listeners“ benötigt werden. Da „EventSource“ (die Komponente, die die Grundlage für Protokollierungs-APIs in Fabric-Anwendung bildet) und „EventListener“ eng zusammenhängen, muss jedes Projekt, das die Bibliothek „Microsoft.Diagnostics.Listeners“ verwendet, eine alternative Implementierung von „EventSource“ verwenden, die durch das von Microsoft erstellte Paket **Microsoft.Diagnostics.Tracing NuGet** bereitgestellt wird. Dieses Paket ist vollständig mit „EventSource“ aus dem Framework abwärtskompatibel, sodass außer einer Änderung der referenzierten Namespaces keine weiteren Codeänderungen erforderlich sein sollten.

Führen Sie für jedes Dienstprojekt, das Daten an ElasticSearch senden soll, die folgenden Schritte aus, um mit der Verwendung der Microsoft.Diagnostics.Tracing-Implementierung der Klasse „EventSource“ zu beginnen:

1. Klicken Sie mit der rechten Maustaste auf den Dienst, und wählen Sie „NuGet-Pakete verwalten“ aus.

2. Wechseln Sie zur Paketquelle „nuget.org“ (sofern nicht bereits ausgewählt), und suchen Sie nach „Microsoft.Diagnostics.Tracing“.

3. Installieren Sie das Paket `Microsoft.Diagnostics.Tracing.EventSource` (einschließlich Abhängigkeiten).

4. Öffnen Sie in Ihrem Dienstprojekt die Datei „ServiceEventSource.cs“ oder „ActorEventSource.cs“, und ersetzen Sie die Direktive `using System.Diagnostics.Tracing` am Dateianfang durch die Direktive `using Microsoft.Diagnostics.Tracing`.

Sobald **.NET Framework 4.6** von Microsoft Azure unterstützt wird, sind diese Schritte nicht mehr nötig.

### Instanziierung und Konfiguration des ElasticSearch-Listeners
Im letzten Schritt zum Senden von Diagnosedaten an ElasticSearch muss eine Instanz von `ElasticSearchListener` erstellt und mit ElasticSearch-Verbindungsdaten konfiguriert werden. Der Listener erfasst automatisch alle Ereignisse, die über die im Dienstprojekt definierten EventSource-Klassen ausgelöst werden. Da er während der Lebensdauer des Diensts aktiv sein muss, wird er am besten im Initialisierungscode des Diensts erstellt. Hier sehen Sie ein Beispiel für den Initialisierungscode eines zustandslosen Diensts nach den erforderlichen Änderungen (Ergänzungen sind in den Kommentaren mit `****` gekennzeichnet):

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

Die ElasticSearch-Verbindungsdaten müssen in einem separaten Abschnitt der Dienstkonfigurationsdatei (PackageRoot\\Config\\Settings.xml) platziert werden. Der Abschnittsname muss dem an den Konstruktor `FabricConfigurationProvider` übergebenen Wert entsprechen. Beispiel:

```xml
<Section Name="ElasticSearchEventListener">
  <Parameter Name="serviceUri" Value="http://myBigCluster.westus.cloudapp.azure.com/es/" />
  <Parameter Name="userName" Value="(ES user name)" />
  <Parameter Name="password" Value="(ES user password)" />
  <Parameter Name="indexNamePrefix" Value="myapp" />
</Section>
```
Bei den Werten `serviceUri`, `userName` und `password` handelt es sich um die Endpunktadresse des ElasticSearch-Clusters bzw. um den ElasticSearch-Benutzernamen und das Kennwort. `indexNamePrefix` ist das Präfix für ElasticSearch-Indizes. Die Bibliothek „Microsoft.Diagnostics.Listeners“ erstellt täglich einen neuen Index für die Daten.

### Überprüfung
Geschafft! Wenn der Dienst nun ausgeführt wird, sendet er Ablaufverfolgungen an den in der Konfiguration angegebenen ElasticSearch-Dienst. Öffnen Sie zum Überprüfen die Kibana-UI für die entsprechende ElasticSearch-Zielinstanz (in unserem Beispiel: http://myBigCluster.westus.cloudapp.azure.com/), und vergewissern Sie sich, dass tatsächlich Indizes mit dem für die Instanz `ElasticSearchListener` gewählten Namenspräfix erstellt und mit Daten gefüllt werden.

![Kibana mit PartyCluster-Anwendungsereignissen][2]

## Nächste Schritte
- [Weitere Informationen zur Diagnose und Überwachung eines Service Fabric-Diensts](service-fabric-diagnostics-how-to-monitor-and-diagnose-services-locally.md)

<!--Image references-->
[1]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/listener-lib-references.png
[2]: ./media/service-fabric-diagnostics-how-to-use-elasticsearch/kibana.png

<!---HONumber=AcomDC_1217_2015-->