<properties
   pageTitle="Service Fabric-Anwendungsmodell"
   description="Modellieren von Anwendungen in Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="alexwun"
   manager="timlt"
   editor="mani-ramaswamy"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/23/2015"
   ms.author="alexwun"/>

# Modellieren von Anwendungen in Service Fabric

Dieser Artikel bietet eine Übersicht über das Service Fabric-Anwendungsmodell und erläutert, wie eine Anwendung und ein Dienst über Manifestdateien definiert werden und die Anwendung für die Bereitstellung gepackt und vorbereitet wird.

## Informationen zum Anwendungsmodell

Eine Anwendung ist eine Sammlung von einzelnen Diensten, die eine bzw. mehrere bestimmte Funktionen ausführen. Ein Dienst führt eine vollständige und eigenständige Funktion aus (Dienste können unabhängig von anderen Diensten gestartet und ausgeführt werden) und besteht aus Code, Konfiguration und Daten. Für jeden Dienst besteht der Code aus den ausführbaren Binärdateien; die Konfiguration umfasst Diensteinstellungen, die zur Laufzeit geladen werden können; und die Daten bestehen aus beliebigen statischen Daten, die vom Dienst verarbeitet werden. Jede Komponente in diesem hierarchischen Anwendungsmodell kann unabhängig mit einer Versionsangabe versehen und aktualisiert werden.

![][1]


Ein Anwendungstyp ist eine Kategorisierung einer Anwendung und besteht aus einem Bündel von Diensttypen. Ein Diensttyp ist eine Kategorisierung eines Diensts, bei dem die Einstellungen und Konfigurationen variieren können, die Kernfunktionen jedoch gleich bleiben. Die Instanzen eines Diensts sind die verschiedenen Dienstkonfigurationsvarianten desselben Diensttyps.

Klassen (oder "Typen") von Anwendungen und Diensten werden mit XML-Dateien (Anwendungsmanifeste und Dienstmanifeste) beschrieben, bei denen es sich um die Vorlagen handelt, mit denen Anwendungen instanziiert werden können. Die Codes für verschiedene Anwendungsinstanzen werden als separate Prozesse ausgeführt, auch wenn sie im gleichen Service Fabric-Knoten gehostet werden. Darüber hinaus kann der Lebenszyklus jeder Anwendungsinstanz unabhängig verwaltet (d. h. aktualisiert) werden. Die folgende schematische Darstellung zeigt die Gliederung von Anwendungstypen in Diensttypen, die wiederum aus Code, Konfiguration und Paketen bestehen.

![Anwendungs- und Diensttypen in Service Fabric][Image1]

Anwendungen und Dienste werden mit zwei verschiedenen Manifestdateien beschrieben: dem Anwendungsmanifest und dem Dienstmanifest, die in den folgenden Abschnitten ausführlich behandelt werden.

Im Cluster können eine oder mehrere Instanzen eines Diensttyps aktiv sein. Zustandsbehaftete Dienstinstanzen oder Replikate erzielen beispielsweise eine hohe Zuverlässigkeit durch die Replikation des Zustands zwischen Replikaten, die sich im Cluster in verschiedenen Knoten befinden (und gewährleisten im Wesentlichen die Redundanz für den Dienst, sodass dieser auch bei Ausfall eines Knotens in einem Cluster verfügbar ist). Bei einem [partitionierten Dienst](service-fabric-concepts-partitioning.md) unterteilt sich der Zustand (und die Zugriffsmuster auf diesen Zustand) noch weiter auf verschiedene Knoten im Cluster.

Die folgende schematische Darstellung zeigt die Beziehung zwischen Anwendungen und Dienstinstanzen, Partitionen und Replikaten.

![Partitionen und Replikate in einem Dienst][Image2]


## Beschreiben eines Diensts

Das Dienstmanifest definiert den Diensttyp und die Version deklarativ und gibt Dienstmetadaten wie z. B. den Diensttyp, Integritätseigenschaften, Lastenausgleichsmetriken sowie die Dienstbinärdateien und Konfigurationsdateien an. Anders ausgedrückt: Es beschreibt den Code, die Konfiguration und die Datenpakete, die ein Dienstpaket bilden, mit dem ein oder mehrere Diensttypen unterstützt werden. Hier ein einfaches Beispiel eines Dienstmanifests:

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ServiceManifest Name="MyServiceManifest" Version="SvcManifestVersion1" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example service manifest</Description>
  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="MyServiceType" />
  </ServiceTypes>
  <CodePackage Name="MyCode" Version="CodeVersion1">
    <SetupEntryPoint>
      <ExeHost>
        <Program>MySetup.bat</Program>
      </ExeHost>
    </SetupEntryPoint>
    <EntryPoint>
      <ExeHost>
        <Program>MyServiceHost.exe</Program>
      </ExeHost>
    </EntryPoint>
  </CodePackage>
  <ConfigPackage Name="MyConfig" Version="ConfigVersion1" />
  <DataPackage Name="MyData" Version="DataVersion1" />
</ServiceManifest>
~~~

**Version**-Attribute sind unstrukturierte Zeichenfolgen und werden im System nicht analysiert. Sie werden dazu verwendet, jeder Komponente für Upgrades mit Versionsangaben zu versehen.

**ServiceTypes** deklariert, welche Diensttypen von den **CodePackages** in diesem Manifest unterstützt werden. Wenn ein Dienst mit einem dieser Diensttypen instanziiert wird, werden alle Codepakete, die in diesem Manifest deklariert sind, durch Ausführen ihrer Einstiegspunkte aktiviert. Die resultierenden Prozesse registrieren die unterstützten Diensttypen zur Laufzeit. Beachten Sie, dass Diensttypen auf Ebene der Manifeste und nicht auf Ebene der Codepakete deklariert werden. Wenn mehrere Codepakete vorhanden sind, werden sie daher alle aktiviert, wenn im System nach einem der deklarierten Diensttypen gesucht wird.

**SetupEntryPoint** ist ein privilegierter Einstiegspunkt, der mit den gleichen Anmeldeinformationen wie Service Fabric (i. d. R. das *LocalSystem*-Konto) vor jedem anderen Einstiegspunkt ausgeführt wird. Die ausführbare Datei, die von **EntryPoint** angegeben wird, ist i. d. R. der Diensthost mit langer Laufzeit. Durch Festlegen eines separaten Setupeinstiegspunkts wird vermieden, dass der Diensthost mit erhöhten Rechten über längere Zeiträume hinweg ausgeführt werden muss. Die von **EntryPoint** angegebene ausführbare Datei wird ausgeführt, nachdem **SetupEntryPoint** erfolgreich beendet wurde. Der resultierende Prozess wird überwacht und neu gestartet (und beginnt wieder mit **SetupEntryPoint**), sofern er beendet wird oder abstürzen sollte.

**DataPackage** deklariert einen Ordner, der durch das **Name**-Attribut benannt wird und beliebige statische Daten enthält, die zur Laufzeit vom Prozess verarbeitet werden sollen.

**ConfigPackage** deklariert einen Ordner, der durch das **Name**-Attribut benannt wird und die Datei *Settings.xml* enthält. Diese Datei enthält Abschnitte mit benutzerdefinierten Schlüssel-Wert-Paar-Einstellungen, die der Prozess zur Laufzeit einlesen kann. Wenn sich während des Upgrades nur die **ConfigPackage**-**Version** geändert hat, wird der ausgeführte Prozess nicht neu gestartet. Stattdessen benachrichtigt ein Rückruf den Prozess, dass sich Konfigurationseinstellungen geändert haben, sodass sie dynamisch neu geladen werden können. Hier ein Beispiel der Datei *Settings.xml*:

~~~
<Settings xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/2011/01/fabric">
  <Section Name="MyConfigurationSecion">
    <Parameter Name="MySettingA" Value="Foo" />
    <Parameter Name="MySettingB" Value="Bar" />
  </Section>
</Settings>
~~~

> [AZURE.NOTE]Ein Dienstmanifest kann mehrere Code-, Konfigurations- und Datenpakete enthalten, die jeweils unabhängig voneinander mit Versionsangabe versehen werden können.

<!--
For more information about other features supported by service manifests, refer to the following articles:

*TODO: LoadMetrics, PlacementConstraints, ServicePlacementPolicies
*TODO: Resources
*TODO: Health properties
*TODO: Trace filters
*TODO: Configuration overrides
-->

## Beschreiben einer Anwendung


Das Anwendungsmanifest beschreibt den Anwendungstyp und die Version deklarativ und gibt die Dienstzusammensetzungs-Metadaten wie z. B. dauerhafte Namen, Partitionierungsschema, Anzahl der Instanzen/Replikationsfaktor, Richtlinie für Sicherheit/Isolation, Platzierungseinschränkungen, Konfigurationsüberschreibungen und zugehörige Diensttypen an. Zudem werden die Lastenausgleichsdomänen beschrieben, in denen die Anwendung eingefügt wird. Somit beschreibt ein Anwendungsmanifest Elemente auf Anwendungsebene, verweist auf ein oder mehrere Dienstmanifeste und bildet so einen Anwendungstyp. Hier ein einfaches Beispiel eines Anwendungsmanifests:

~~~
<?xml version="1.0" encoding="utf-8" ?>
<ApplicationManifest
      ApplicationTypeName="MyApplicationType"
      ApplicationTypeVersion="AppManifestVersion1"
      xmlns="http://schemas.microsoft.com/2011/01/fabric"
      xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
  <Description>An example application manifest</Description>
  <ServiceManifestImport>
    <ServiceManifestRef ServiceManifestName="MyServiceManifest" ServiceManifestVersion="SvcManifestVersion1"/>
  </ServiceManifestImport>
  <DefaultServices>
     <Service Name="MyService">
         <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
             <SingletonPartition/>
         </StatelessService>
     </Service>
  </DefaultServices>
</ApplicationManifest>
~~~

Wie bei Dienstmanifesten sind **Version**-Attribute unstrukturierte Zeichenfolgen und werden im System nicht analysiert. Sie werden auch dazu verwendet, jeder Komponente für Upgrades mit Versionsangaben zu versehen.

**ServiceManifestImport** enthält Verweise auf Dienstmanifeste zum Erstellen des entsprechenden Anwendungstyps. Importierte Dienstmanifeste legen fest, welche Diensttypen innerhalb des Anwendungstyps gültig sind.

**DefaultServices** deklariert Dienstinstanzen, die automatisch erstellt werden, wenn eine Anwendung mit diesem Anwendungstyp instanziiert wird. Standarddienste dienen lediglich der Benutzerfreundlichkeit und verhalten sich nach der Erstellung in jeder Hinsicht wie normale Dienste. Sie werden zusammen mit allen anderen Diensten in der Anwendungsinstanz aktualisiert und können auch entfernt werden.

> [AZURE.NOTE]Ein Anwendungsmanifest kann mehrere Dienstmanifestimporte und Standarddienste enthalten. Jedes Dienstmanifest kann unabhängig mit Versionsangaben versehen werden.

<!--
For more information about other features supported by application manifests, refer to the following articles:

*TODO: Application parameters
*TODO: Security, Principals, RunAs, SecurityAccessPolicy
*TODO: Service Templates
-->

## Packen einer Anwendung

### Paketlayout

Anwendungsmanifest, Dienstmanifeste und andere erforderliche Paketdateien müssen in einem bestimmten Layout für die Bereitstellung in einem Service Fabric-Cluster angeordnet werden. Die Beispielmanifeste in diesem Artikel müssen in der folgenden Verzeichnisstruktur angeordnet werden:

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat
~~~

Die Ordner werden nach den **Name**-Attributen der jeweils zugehörigen Elemente benannt. Wenn das Dienstmanifest beispielsweise zwei Codepakete mit dem Namen **MyCodeA** und **MyCodeB** enthält, müssen zwei Ordner mit denselben Namen vorhanden sein, die die jeweils erforderlichen Binärdateien für jedes Codepaket enthalten.

### Erstellen eines Pakets mit Visual Studio

Wenn Sie Ihre Anwendung mithilfe von Visual Studio 2015 erstellen, können Sie über den Befehl "Paket" automatisch ein Paket erstellen, das dem oben beschriebenen Layout entspricht.

Klicken Sie zum Erstellen eines Pakets im Projektmappen-Explorer einfach mit der rechten Maustaste auf das Anwendungsprojekt, und wählen Sie wie unten gezeigt den Befehl "Paket" aus:

![][2]

Nach Abschluss der Paketerstellung wird der Speicherort des Pakets im Ausgabefenster angezeigt. Beachten Sie, dass die Paketerstellung automatisch erfolgt, wenn Sie die Anwendung in Visual Studio bereitstellen oder debuggen.

### Testen des Pakets

Die Paketstruktur kann lokal über PowerShell mit dem Befehl **Test-ServiceFabricApplicationPackage** überprüft werden, mit dem Probleme bei der Manifestanalyse und alle Verweise überprüft werden. Beachten Sie, dass dieser Befehl nur die strukturelle Richtigkeit der Verzeichnisse und Dateien im Paket überprüft. Über die Überprüfung, ob alle erforderlichen Dateien vorhanden sind, hinaus werden die Inhalte des Code- oder Datenpakets nicht weiter geprüft.

~~~
PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
False
Test-ServiceFabricApplicationPackage : The EntryPoint MySetup.bat is not found.
FileName: C:\Users\servicefabric\AppData\Local\Temp\TestApplicationPackage_7195781181\nrri205a.e2h\MyApplicationType\MyServiceManifest\ServiceManifest.xml
~~~

Dieser Fehler gibt an, dass die Datei *MySetup.bat*, auf die im Dienstmanifest **SetupEntryPoint** verwiesen wird, im Codepaket fehlt. Nach dem Hinzufügen der fehlenden Datei wird die Überprüfung der Anwendung erfolgreich abgeschlossen:

~~~
PS D:\temp> tree /f .\MyApplicationType

D:\TEMP\MYAPPLICATIONTYPE
│   ApplicationManifest.xml
│
└───MyServiceManifest
    │   ServiceManifest.xml
    │
    ├───MyCode
    │       MyServiceHost.exe
    │       MySetup.bat
    │
    ├───MyConfig
    │       Settings.xml
    │
    └───MyData
            init.dat

PS D:\temp> Test-ServiceFabricApplicationPackage .\MyApplicationType
True
PS D:\temp>
~~~

Nachdem die Anwendung ordnungsgemäß gepackt und die Überprüfung erfolgreich abgeschlossen wurde, kann die Anwendung bereitgestellt werden.

## Nächste Schritte

[Deploy an application][10] (in englischer Sprache)

<!--Image references-->
[1]: ./media/service-fabric-application-model/application-model.jpg
[2]: ./media/service-fabric-application-model/vs-package-command.png
[Image1]: media/service-fabric-application-model/Service1.jpg
[Image2]: media/service-fabric-application-model/Service2.jpg

<!--Link references--In actual articles, you only need a single period before the slash-->
[10]: service-fabric-deploy-remove-applications.md

 

<!---HONumber=July15_HO4-->