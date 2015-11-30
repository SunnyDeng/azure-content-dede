<properties
   pageTitle="Bereitstellen einer vorhandenen Anwendung in Azure Service Fabric | Microsoft Azure"
   description="Exemplarische Vorgehensweise beim Packen einer vorhandenen Anwendung, um diese in einem Azure Service Fabric-Cluster bereitzustellen"
   services="service-fabric"
   documentationCenter=".net"
   authors="bmscholl"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/09/2015"
   ms.author="bscholl"/>

# Bereitstellen einer vorhandenen Anwendung in Service Fabric

Sie können beliebige vorhandene Anwendungen, z. B. Node.js-, Java- oder systemeigene Anwendungen, in Service Fabric ausführen. Service Fabric behandelt diese Anwendungen wie zustandslose Dienste und platziert sie auf Knoten in einem Cluster basierend auf Verfügbarkeit und anderen Metriken. In diesem Artikel wird das Packen und Bereitstellen einer vorhandenen Anwendung in einem Service Fabric-Cluster beschrieben.

## Vorteile der Ausführung einer vorhandenen Anwendung in Service Fabric

Das Ausführen der Anwendung in einem Service Fabric-Cluster bietet mehrere Vorteile:

- Hohe Verfügbarkeit: Anwendungen, die in Service Fabric ausgeführt werden, sind standardmäßig hoch verfügbar. Service Fabric stellt sicher, dass stets eine Instanz einer Anwendung ausgeführt wird.
- Statusüberwachung: Die standardmäßige Service Fabric-Statusüberwachung erkennt, ob die Anwendung ausgeführt wird, und bietet bei Fehlern Diagnose-Informationen.   
- Verwaltung des Anwendungslebenszyklus: Bei Upgrades kommt es zu keinen Ausfallzeiten. Außerdem ermöglicht Service Fabric das Zurücksetzen auf die Vorversion, sollte während eines Upgrades ein Problem auftreten.    
- Dichte: Sie können mehrere Anwendungen im Cluster ausführen, sodass jede Anwendung nicht mehr auf eigener Hardware ausgeführt werden muss.

In diesem Artikel werden die grundlegenden Schritte zum Packen einer vorhandenen Anwendung und ihre Bereitstellung in Service Fabric beschrieben.


## Kurzübersicht über die Anwendungs- und Dienstmanifestdatei

Bevor wir in Bezug auf die Bereitstellung einer vorhandenen Anwendung ins Detail gehen, ist es hilfreich, das Service Fabric-Modell für das Packen und Bereitstellen zu verstehen. Das Service Fabric-Packmodell beruht hauptsächlich auf zwei Dateien:


* **Anwendungsmanifest**

  Das Anwendungsmanifest beschreibt die Anwendung und listet neben den Diensten, aus denen sie besteht, noch weitere Parameter auf (z. B. die Anzahl der Instanzen, anhand derer definiert wird, wie die Dienste bereitgestellt werden sollen). In der Service Fabric-Terminologie ist eine Anwendung eine „upgradefähige Einheit“. Eine Anwendung kann als einzelne Einheit aktualisiert werden, wenn mögliche Fehler (und mögliche Rollbacks) von der Plattform verwaltet werden, um sicherzustellen, dass der Upgradevorgang entweder vollständig erfolgreich ist oder die Anwendung bei Fehlern nicht in einem unbekannten/instabilen Zustand bleibt.


* **Dienstmanifest**

  Das Dienstmanifest beschreibt die Komponenten eines Diensts. Es enthält Daten wie Name und Typ des Diensts (Informationen, mit denen Service Fabric den Dienst verwaltet), den entsprechenden Code, Konfiguration und Datenkomponenten sowie einige zusätzliche Parameter, die nach der Bereitstellung zum Konfigurieren des Diensts verwendet werden können. Wir werden nicht detailliert auf all die unterschiedlichen Parameter eingehen, die im Dienstmanifest zur Verfügung stehen, sondern uns nur mit der Teilmenge beschäftigen, die zum Ausführen einer vorhandenen Anwendung in der Service Fabric erforderlich ist.

Detaillierte Informationen zum Format des Service Fabric-Packformats finden Sie [hier](service-fabric-develop-your-service-index.md).

## Dateistruktur des Anwendungspakets
Damit eine Anwendung in Service Fabric bereitgestellt werden kann, muss die Anwendung einer vordefinierten Verzeichnisstruktur folgen. Es folgt ein Beispiel dieser Struktur.

```
|-- AppplicationPackage
	|-- code
		|-- existingapp.exe
	|-- config
		|--Settings.xml
    |--data    
    |-- ServiceManifest.xml
|-- ApplicationManifest.xml
```

Das Stammverzeichnis enthält die Datei „applicationmanifest.xml“, welche die Anwendung definiert. Für jeden Dienst, der in der Anwendung enthalten ist, gibt es ein Unterverzeichnis, das alle für den Dienst erforderlichen Artefakte enthält: die Datei „servicemanifest.xml“ und in der Regel drei Verzeichnisse:

- *code* enthält den Code des Diensts.
- *config* enthält die Datei „settings.xml“ (sowie andere Dateien, falls erforderlich), auf die der Dienst zur Laufzeit zugreifen kann, um bestimmte Konfigurationseinstellungen abzurufen.
- *data* ist ein zusätzliches Verzeichnis zum Speichern zusätzlicher lokaler Daten, die der Dienst möglicherweise benötigt. Hinweis: Das Verzeichnis "data" sollte nur zum Speichern flüchtiger Daten verwendet werden. Service Fabric kopiert/repliziert keine Änderungen am Verzeichnis "data", wenn der Dienst z. B. bei einem Failover verschoben werden muss.

Hinweis: Sie müssen die Verzeichnisse `config` und `data` nur erstellen, falls Sie sie benötigen.

## Prozess zum Packen einer vorhandenen Anwendung

Der Prozess zum Packen einer vorhandenen Anwendung basiert auf folgenden Schritten:

- Erstellen der Verzeichnisstruktur des Pakets
- Hinzufügen von Anwendungscode und Konfigurationsdateien
- Aktualisieren der Dienstmanifestdatei
- Aktualisieren des Anwendungsmanifests

>[AZURE.NOTE]Wir bieten ein Packtool, mit dem Sie das Anwendungspaket automatisch erstellen können. Das Tool befindet sich derzeit in der Vorschauphase. Weitere Informationen finden Sie [hier](http://aka.ms/servicefabricpacktool).

### Erstellen der Verzeichnisstruktur des Pakets
Sie können zunächst die Verzeichnisstruktur wie zuvor beschrieben erstellen.

### Hinzufügen von Anwendungscode und Konfigurationsdateien
Nachdem Sie die Verzeichnisstruktur erstellt haben, können Sie den Anwendungscode und die Konfigurationsdateien den Verzeichnissen „code“ und „config“ hinzufügen. Unter den Verzeichnissen „code“ und „config“ können Sie auch weitere Verzeichnisse bzw. Unterverzeichnisse erstellen. Service Fabric erstellt eine XCopy des Inhalts des Stammverzeichnisses der Anwendung. Außer dem Erstellen der beiden übergeordneten Verzeichnisse „code“ und „settings“ (für die Sie auch andere Namen verwenden können; mehr dazu im nächsten Abschnitt) gibt es also keine vordefinierte Struktur, die verwendet werden muss.

>[AZURE.NOTE]Stellen Sie sicher, dass Sie alle Dateien/Abhängigkeiten einbeziehen, die für die Anwendung erforderlich sind. Service Fabric kopiert den Inhalt des Anwendungspakets auf alle Knoten im Cluster, auf denen die Anwendungsdienste bereitgestellt werden sollen. Das Paket muss den gesamten Code enthalten, den die Anwendung zur Ausführung benötigt. Es ist nicht empfehlenswert anzunehmen, dass die Abhängigkeiten bereits installiert sind.

### Bearbeiten der Dienstmanifestdatei
Im nächsten Schritt wird die Dienstmanifestdatei so bearbeitet, dass sie folgende Informationen enthält:

- Den Namen des Diensttyps. Dies ist eine „Id“, die von Service Fabric zum Identifizieren eines Diensts verwendet wird.
- Den Befehl zum Starten der Anwendung (ExeHost).
- Alle Skripts, die ausgeführt werden müssen, um die Anwendung einzurichten oder zu konfigurieren (SetupEntrypoint).

Nachstehend sehen Sie ein Beispiel von `ServiceManifest.xml`:

```xml
<?xml version="1.0" encoding="utf-8"?>
<ServiceManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" Name="NodeApp" Version="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceTypes>
      <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true"/>
   </ServiceTypes>
   <CodePackage Name="code" Version="1.0.0.0">
      <SetupEntryPoint>
         <ExeHost>
             <Program>scripts\launchConfig.cmd</Program>
         </ExeHost>
      </SetupEntryPoint>
      <EntryPoint>
         <ExeHost>
            <Program>node.exe</Program>
            <Arguments>bin/www</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
         </ExeHost>
      </EntryPoint>
   </CodePackage>
   <Resources>
      <Endpoints>
         <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
   </Resources>
</ServiceManifest>
```

Sehen wir uns nun den anderen Teil der Datei an, den Sie aktualisieren müssen:

### ServiceTypes

```xml
<ServiceTypes>
  <StatelessServiceType ServiceTypeName="NodeApp" UseImplicitHost="true" />
</ServiceTypes>
```

- Für `ServiceTypeName` können Sie jeden beliebigen Namen angeben. Der Wert wird in der Datei `ApplicationManifest.xml` zum Identifizieren des Diensts verwendet.
- Sie müssen `UseImplicitHost="true"` angeben. Dieses Attribut informiert Service Fabric, dass der Dienst auf einer eigenständigen Anwendung beruht und Service Fabric ihn lediglich als Prozess starten und seine Integrität überwachen muss.

### CodePackage
CodePackage gibt den Speicherplatz (und Version) des Codes für den Dienst an.

```xml
<CodePackage Name="Code" Version="1.0.0.0">
```

Mit dem Element `Name` wird der Name des Verzeichnisses im Anwendungspaket angeben, das den Code des Diensts enthält. `CodePackage` verfügt außerdem über das Attribut `version`, mit dem Sie die Version des Codes angeben und ggf. den Code des Diensts über die ALM-Infrastruktur von Service Fabric aktualisieren können.
### SetupEntryPoint

```xml
<SetupEntryPoint>
   <ExeHost>
       <Program>scripts\launchConfig.cmd</Program>
   </ExeHost>
</SetupEntryPoint>
```
Der „SetupEntryPoint“ wird verwendet, um eine ausführbare Datei oder Batchdatei anzugeben, die vor dem Starten des Dienstcodes ausgeführt werden soll. Dies ist ein optionales Element, das nicht angegeben werden muss, wenn keine Initialisierung/kein Setup erforderlich ist. Der „SetupEntryPoint“ wird bei jedem Neustart des Diensts ausgeführt. Es gibt nur ein Element des Typs „SetupEntryPoint“. Daher müssen Setup-/Konfigurationsskripts in einer Batchdatei gebündelt werden, wenn für das Setup bzw. die Konfiguration der Anwendung mehrere Skripts erforderlich sind. Wie das Element „EntryPoint“ kann auch das Element „SetupEntrypoint“ jeden beliebigen Dateityp ausführen: ausführbare Datei, Batchdatei, PowerShell-Cmdlet. Im obigen Beispiel basiert „SetupEntrypoint“ auf der Batchdatei „launchConfig.cmd“, die sich im Unterverzeichnis `scripts` des Verzeichnisses „code“ befindet (sofern das „WorkingDirectory“-Element auf „Code“ festgelegt ist).

### EntryPoint

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
  </ExeHost>
</EntryPoint>
```

Mit dem Element `Entrypoint` in der Dienstmanifestdatei wird angegeben, wie der Dienst gestartet werden soll. Das Element `ExeHost` gibt die ausführbare Datei (und die Argumente) an, die zum Starten des Diensts verwendet werden soll.

- `Program` gibt den Namen der ausführbaren Datei an, die zum Starten des Diensts ausgeführt werden soll.
- `Arguments` gibt die Argumente an, die an die ausführbare Datei übergeben werden sollen. Dies kann eine Liste von Parametern mit Argumenten sein.
- `WorkingFolder` gibt das Arbeitsverzeichnis für den Prozess an, der gestartet werden soll. Sie können zwei Werte angeben:
	- `CodeBase`: Als Arbeitsverzeichnis wird das Verzeichnis „code“ im Anwendungspaket festgelegt (Verzeichnis `Code` in der abgebildeten Struktur).
	- `CodePackage`: Das Arbeitsverzeichnis wird auf das Stammverzeichnis des Anwendungspakets (`MyServicePkg`) festgelegt.
- Das Element `WorkingDirectory` ist nützlich, um das richtige Arbeitsverzeichnis festzulegen, damit von der Anwendung oder von Initialisierungsskripts relative Pfade verwendet werden können.

### Endpunkte

```xml
<Endpoints>
   <Endpoint Name="NodeAppTypeEndpoint" Protocol="http" Port="3000" Type="Input" />
</Endpoints>

```
Das `Endpoint`-Element gibt die Endpunkte an, an denen die Anwendung lauschen kann. In diesem Beispiel lauscht die Node.js-Anwendung an Port 3000.

## Anwendungsmanifestdatei

Nach der Konfiguration der Datei `servicemanifest.xml` müssen Sie einige Änderungen an der Datei `ApplicationManifest.xml` vornehmen, um sicherzustellen, dass der richtige Diensttyp und der richtige Name verwendet werden.

```xml
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeAppType" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
</ApplicationManifest>
```

### ServiceManifestImport

In `ServiceManifestImport` können Sie einen oder mehrere Dienste angeben, die in der Anwendung enthalten sein sollen. `ServiceManifestName` verweist auf die Dienste. Dieses Element gibt den Namen des Verzeichnisses an, in dem sich die Datei `ServiceManifest.xml` befindet.

```xml
<ServiceManifestImport>
  <ServiceManifestRef ServiceManifestName="NodeApp" ServiceManifestVersion="1.0.0.0" />
</ServiceManifestImport>
```

### Einrichten der Protokollierung
Bei vorhandenen Anwendungen ist es äußerst praktisch, Konsolenprotokolle anzeigen zu können, um sicherzustellen, dass für die Anwendung und Konfigurationskripts keine Fehler angezeigt werden. In der Datei `ServiceManifest.xml` kann mit dem Element `ConsoleRedirection` eine Konsolenumleitung konfiguriert werden.

```xml
<EntryPoint>
  <ExeHost>
    <Program>node.exe</Program>
    <Arguments>bin/www</Arguments>
    <WorkingFolder>CodeBase</WorkingFolder>
    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
  </ExeHost>
</EntryPoint>
```

* Mit `ConsoleRedirection` kann die Konsolenausgabe (stdout und stderr) in ein Arbeitsverzeichnis umgeleitet werden, um sicherzustellen, dass bei der Konfiguration oder Ausführung der Anwendung im Service Fabric-Cluster keine Fehler aufgetreten sind.

	* `FileRetentionCount` legt fest, wie viele Dateien im Arbeitsverzeichnis gespeichert werden. Der Wert 5 zum Beispiel bedeutet, dass die Protokolldateien für die letzten fünf Ausführungsvorgänge im Arbeitsverzeichnis gespeichert werden.
	* `FileMaxSizeInKb` gibt die maximale Größe der Protokolldateien an.

Die Protokolldateien werden in einem der Arbeitsverzeichnisse für den Dienst gespeichert. Um zu bestimmen, wo sich die Dateien befinden, müssen Sie den Service Fabric-Explorer verwenden und feststellen, auf welchem Knoten der Dienst ausgeführt und welches Arbeitsverzeichnis aktuell verwendet wird. Dies wird weiter unten in diesem Artikel erläutert.

### Bereitstellung
Der letzte Schritt ist das Bereitstellen der Anwendung. Das folgende PowerShell-Skript veranschaulicht, wie Sie die Anwendung im lokalen Entwicklungscluster bereitstellen und einen neuen Service Fabric-Dienst starten.

```Powershell

Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath 'C:\Dev\MulitpleApplications' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStoreShare' -ApplicationPackagePathInImageStore 'Store\nodeapp'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'Store\nodeapp'

New-ServiceFabricApplication -ApplicationName 'fabric:/nodeapp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0

New-ServiceFabricService -ApplicationName 'fabric:/nodeapp' -ServiceName 'fabric:/nodeapp/nodeappservice' -ServiceTypeName 'NodeApp' -Stateless -PartitionSchemeSingleton -InstanceCount 1

```
Ein Service Fabric-Dienst kann in verschiedenen "Konfigurationen" bereitgestellt werden: Eine Instanz, mehrere Instanzen oder eine Instanz des Diensts auf jedem Knoten des Service Fabric-Clusters, um nur einige Beispiele zu nennen.

Der `InstanceCount`-Parameter des Cmdlets `New-ServiceFabricService` wird verwendet, um anzugeben, wie viele Instanzen des Diensts im Service Fabric-Cluster gestartet werden sollen. Sie können den Wert `InstanceCount` nach der Art der bereitzustellenden Anwendung festlegen. Die beiden häufigsten Szenarien: `InstanCount = "1"`: In diesem Fall wird nur eine Instanz des Diensts im Cluster bereitgestellt. Der Service Fabric-Planer bestimmt den Knoten, auf dem der Dienst bereitgestellt werden soll.

* `InstanceCount ="-1"`: In diesem Fall wird eine Instanz des Diensts auf jedem Knoten im Service Fabric-Cluster bereitgestellt. Das Endergebnis ist eine (und nur eine) Instanz des Diensts für jeden Knoten im Cluster. Dies ist eine praktische Konfiguration für Front-End-Anwendungen (z. B. REST-Endpunkt), da Client-Anwendungen nur eine Verbindung zu einem Knoten im Cluster herstellen müssen, um den Endpunkt zu verwenden. Diese Konfiguration kann z. B. auch verwendet werden, wenn alle Knoten des Service Fabric-Clusters mit einem Lastenausgleichsmodul verbunden sind, damit der Datenverkehr der Clients über den Dienst verteilt werden kann, der auf allen Knoten im Cluster ausgeführt wird.

### Überprüfen der ausgeführten Anwendung

Bestimmen Sie im Service Fabric-Explorer den Knoten, auf dem der Dienst ausgeführt wird. In diesem Beispiel ist es „Node1“:

![Ausgeführte Anwendung](./media/service-fabric-deploy-existing-app/runningapplication.png)

Wenn Sie zum Knoten navigieren und zur Anwendung wechseln, sehen Sie die wesentlichen Knoteninformationen einschließlich des Speicherorts auf dem Datenträger.

![Speicherort auf Datenträger](./media/service-fabric-deploy-existing-app/locationondisk.png)

Wenn Sie in Server-Explorer zum Verzeichnis wechseln, sehen Sie, wie nachstehend gezeigt, das Arbeitsverzeichnis und den Protokollordner des Diensts.

![Speicherort auf Datenträger](./media/service-fabric-deploy-existing-app/loglocation.png)


## Nächste Schritte
In diesem Artikel wurden das Packen einer vorhandenen Anwendung und ihre Bereitstellung in Service Fabric beschrieben. Im nächsten Schritt können Sie weitere Informationen zu diesem Thema erfahren.

- Beispiel für das Packen und Bereitstellen einer vorhandenen Anwendung auf [Github](https://github.com/bmscholl/servicefabric-samples/tree/comingsoon/samples/RealWorld/Hosting/SimpleApplication), einschließlich Vorabversion des Packtools
- Beispiel für das Packen mehrerer Anwendungen auf [Github](https://github.com/bmscholl/servicefabric-samples/tree/comingsoon/samples/RealWorld/Hosting/SimpleApplication)
- Erste Schritte mit dem [Erstellen Ihrer ersten Service Fabric-Anwendung in Visual Studio](service-fabric-create-your-first-application-in-visual-studio.md)

<!---HONumber=Nov15_HO4-->