<properties
   pageTitle="Bereitstellen einer vorhandenen Anwendung in Azure Service Fabric"
   description="Exemplarische Vorgehensweise beim Packen einer vorhandenen Anwendung, um diese in einem Azure Service Fabric-Cluster bereitzustellen"
   services="service-fabric"
   documentationCenter=".net"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="07/06/2015"
   ms.author="claudioc"/>

# Bereitstellen einer vorhandenen Anwendung in Azure Service Fabric

Azure Service Fabric kann zum Bereitstellen vorhandener Anwendungen verwendet werden. Anwendungen, die derzeit zum Beispiel unter der Azure-Webrolle oder Azure-Workerrolle ausgeführt werden, können so "gepackt" werden, dass sie in einem Service Fabric-Cluster bereitgestellt werden können. Vorhandene Anwendungen, die in einem Service Fabric-Cluster ausgeführt werden, profitieren von Features wie Systemüberwachung und ALM (Application Lifecycle Management) – also ein wichtiges Szenario, das vollständig unterstützt wird. In diesem Lernprogramm werden die grundlegenden Konzepte beim Auswählen und Packen einer vorhandenen Anwendung sowie der entsprechende Prozess erläutert. Diesem Artikel, der eine Übersicht über den Prozess enthält, folgen spezielle Beispiele, die verdeutlichen, wie eine vorhandene Anwendung (z. B. node.js- oder Java-Anwendung) ausgewählt und gepackt wird, damit sie in einem Service Fabric-Cluster gehostet werden kann.

## Kurzübersicht über Anwendungs- und Dienstmanifestdatei

Bevor Sie beginnen und erfahren, wie eine vorhandene Anwendung gepackt wird, muss das Service Fabric-Bereitstellungsmodell in aller Kürze vorgestellt werden. Das Service Fabric-Bereitstellungsmodell beruht hauptsächlich auf zwei Dateien:


* Das Anwendungsmanifest. Das Anwendungsmanifest beschreibt die Anwendung und listet neben den Diensten, aus denen es besteht, noch weitere Parameter auf (z. B. die Anzahl der Instanzen, anhand denen definiert wird, wie der Dienst bzw. die Dienste bereitgestellt werden sollen). In der Service Fabric-Welt ist eine Anwendung eine "Einheit mit Upgrade-Möglichkeit". Eine Anwendung kann als einzelne Einheit upgegraded werden, wenn mögliche Fehler (und mögliche Rollbacks) von der Plattform verwaltet werden, um sicherzustellen, dass der Upgradevorgang entweder vollständig erfolgreich ist oder die Anwendung bei Fehlern nicht in einem unbekannten/instabilen Zustand bleibt.

Hier ein Beispiel für ein Anwendungsmanifest:

	

	<?xml version="1.0" encoding="utf-8"?>
	<ApplicationManifest ApplicationTypeName="actor2Application" ApplicationTypeVersion="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric" xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	  <ServiceManifestImport>
		<ServiceManifestRef ServiceManifestName="actor2Pkg" ServiceManifestVersion="1.0.0.0" />
		<ConfigOverrides />
	  </ServiceManifestImport>
	  <DefaultServices>
		<Service Name="actor2">
		  <StatelessService ServiceTypeName="actor2Type">
			<SingletonPartition />
		  </StatelessService>
		</Service>
	  </DefaultServices>
	</ApplicationManifest>

* Das Dienstmanifest. Das Dienstmanifest beschreibt die Komponenten eines Diensts. Es enthält Daten wie Name und Typ des Diensts (Informationen, mit denen Service Fabric den Dienst verwaltet), den entsprechenden Code, Konfiguration und Datenkomponenten sowie einige zusätzliche Parameter, die nach der Bereitstellung zum Konfigurieren des Diensts verwendet werden können. Wir werden nicht detailliert auf all die unterschiedlichen Parameter eingehen, die im Dienstmanifest zur Verfügung stehen, sondern uns nur mit der Teilmenge beschäftigen, die zum Ausführen einer vorhandenen Anwendung in der Service Fabric erforderlich ist.

Hier ein Beispiel für ein Dienstmanifest:

	<?xml version="1.0" encoding="utf-8"?>
	<ServiceManifest Name="actor2Pkg"
	                 Version="1.0.0.0"
	                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
	                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
	                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	  <ServiceTypes>
	    <StatelessServiceType ServiceTypeName="actor2Type" />
	  </ServiceTypes>
	
	  <CodePackage Name="Code" Version="1.0.0.0">
	    <EntryPoint>
	      <ExeHost>
	        <Program>actor2.exe</Program>
	      </ExeHost>
	    </EntryPoint>
	  </CodePackage>
	
	  <ConfigPackage Name="Config" Version="1.0.0.0" />
	
	  <Resources>
	    <Endpoints>
	      <Endpoint Name="ServiceEndpoint" />
	    </Endpoints>
	  </Resources>
	</ServiceManifest>
	
## Dateistruktur des Anwendungspakets
Damit eine Anwendung z. B. mit Powershell-Cmdlets bereitgestellt werden kann, muss die Anwendung einer vordefinierten Verzeichnisstruktur folgen.

\\applicationmanifest.xml \\Mein Dienstpaket \\servicemanifest.xml \\code \\config \\data

Das Stammverzeichnis enthält die Datei "applicationmanifest.xml", welche die Anwendung definiert. Für jeden Dienst, der in der Anwendung enthalten ist, gibt es ein Unterverzeichnis, das alle für den Dienst erforderlichen Artefakte enthält: die Datei "servicemanifest.xml" und in der Regel drei Verzeichnisse:

* "code" enthält den Code des Diensts.
* "config" enthält die Datei "settings.xml" (sowie andere Dateien, falls erforderlich), auf die der Dienst zur Laufzeit zugreifen kann, um bestimmte Konfigurationseinstellungen abzurufen.
* "data" ist ein zusätzliches Verzeichnis zum Speichern zusätzlicher lokaler Daten, die der Dienst möglicherweise benötigt. Hinweis: Das Verzeichnis "data" sollte nur zum Speichern flüchtiger Daten verwendet werden. Service Fabric kopiert/repliziert keine Änderungen am Verzeichnis "data", wenn der Dienst z. B. bei einem Failover verschoben werden muss. 

Hinweis: Für "code", "config" und "data" können Sie einen beliebigen Verzeichnisnamen verwenden . Sie müssen jedoch sicherstellen, dass Sie in der Anwendungsmanifestdatei den gleichen Wert verwenden.

## Prozess zum Packen einer vorhandenen Anwendung
Der Prozess zum Packen einer vorhandenen Anwendung basiert auf folgenden Schritten:

* Erstellen der Verzeichnisstruktur des Pakets
* Hinzufügen von Anwendungscode und Konfigurationsdateien
* Aktualisieren der Dienstmanifestdatei
* Aktualisieren des Anwendungsmanifests


### Erstellen der Verzeichnisstruktur des Pakets
Sie können zunächst die Verzeichnisstruktur wie oben beschrieben erstellen. Hier wurde ein Verzeichnis erstellt, und Anwendungs- und Dienstmanifest wurden aus einem vorhandenen Projekt kopiert, das zuvor in Visual Studio erstellt wurde.

![][1] ![][2]


### Hinzufügen von Anwendungscode und Konfigurationsdateien
Nachdem Sie die Verzeichnisstruktur erstellt haben, können Sie den Anwendungscode und die Konfigurationsdateien im Verzeichnis "code" und "config" hinzufügen. Unter den Verzeichnissen "code" oder "config" können Sie auch weitere Verzeichnisse bzw. Unterverzeichnisse erstellen. Service Fabric erstellt eine XCopy des Inhalts des Stammverzeichnisses der Anwendung. Außer dem Erstellen der beiden übergeordneten Verzeichnisse "code" und "settings" (für die Sie auch andere Namen verwenden können; mehr dazu im nächsten Abschnitt), gibt es also keine vordefinierte Struktur, die verwendet werden muss.

!Azure-Hinweis: Stellen Sie sicher, dass Sie alle Dateien/Abhängigkeiten einbeziehen, die für die Anwendung erforderlich sind. Service Fabric kopiert den Inhalt des Anwendungspakets auf alle Knoten im Cluster, auf denen die Anwendungsdienste bereitgestellt werden sollen. Das Paket muss den gesamten Code enthalten, den die Anwendung zur Ausführung benötigt. Es ist nicht empfehlenswert anzunehmen, dass die Abhängigkeiten bereits installiert sind.

### Bearbeiten der Dienstmanifestdatei
Im nächsten Schritt wird die Dienstmanifestdatei so bearbeitet, dass sie folgende Informationen enthält: * Name des Diensttyps. Diese "ID" verwendet Service Fabric zum Identifizieren eines Diensts * Der Befehl zum Starten der Anwendung (ExeHost) * Ein beliebiges Skript, das zum Einrichten/Konfigurieren der Anwendung (SetupEntryPoint) ausgeführt werden muss.

Hier ein Beispiel für die Datei `servicemanifest.xnml` mit einer gepackten node.js-Anwendung :

	<?xml version="1.0" encoding="utf-8"?>
	<ServiceManifest Name="VisualObjectsNodejsWebServicePkg"
	                 Version="1.0.0.0"
	                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
	                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
	                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">
	  <ServiceTypes>
	    <StatelessServiceType ServiceTypeName="VisualObjectsNodejsWebServiceType" UseImplicitHost="true" />
	  </ServiceTypes>
	  <CodePackage Name="Code" Version="1.0.0.0">
	    <EntryPoint>
	      <ExeHost>
	        <Program>node.exe</Program>
	        <Arguments>server.js</Arguments>
	        <WorkingFolder>CodeBase</WorkingFolder>
	      </ExeHost>
	    </EntryPoint>
	  </CodePackage>
	  <ConfigPackage Name="Config" Version="1.0.0.0"/>
	  <Resources>
	    <Endpoints>
	      <Endpoint Name="ServiceEndpoint" />
	    </Endpoints>
	  </Resources>
	</ServiceManifest>


Sehen wir uns nun den anderen Teil der Datei an, den Sie aktualisieren müssen:

### ServiceTypes:

```
<ServiceTypes>
<StatelessServiceType ServiceTypeName="VisualObjectsNodejsWebServiceType" UseImplicitHost="true" />
</ServiceTypes>
```

* Als `ServiceTypeName` können Sie jeden beliebigen Namen angeben; der Wert wird in der Datei `applicationmanifest.xml` zum Identifizieren des Diensts verwendet.
* Sie müssen `UserImplicitHost = "true"` angeben. Dieses Attribut informiert Service Fabric, dass der Dienst auf einer eigenständigen Anwendung beruht und Service Fabric ihn lediglich als Prozess starten und seine Integrität überwachen muss.


### CodePackage 
CodePackage gibt den Speicherplatz (und Version) des Codes für den Dienst an.


	<CodePackage Name="Code" Version="1.0.0.0">


Mit dem Element `Name` wird der Name des Verzeichnisses im Anwendungspaket angeben, das den Code des Diensts enthält. `CodePackage` verfügt außerdem über das Attribut `version`, mit dem Sie die Version des Codes angeben und ggf. den Code des Diensts über die Application LifeCycle Management-Infrastruktur von Service Fabric aktualisieren können.


### EntryPoint


	<EntryPoint>
	  <ExeHost>
	    <Program>node.exe</Program>
	    <Arguments>server.js</Arguments>
	    <WorkingFolder>CodeBase</WorkingFolder>
	  </ExeHost>
	</EntryPoint>


Mit dem Element `Entrypoint` in der Dienstmanifestdatei wird angegeben, wie den Dienst gestartet werden soll. Das Element `ExeHost` gibt die ausführbare Datei (und die Argumente) an, die zum Starten des Diensts verwendet werden soll.

* `Program` gibt den Namen der ausführbaren Datei an, die zum Starten des Diensts ausgeführt werden soll. 
* `Arguments` gibt die Argumente an, die an die ausführbare Datei übergeben werden sollen. Das kann eine Liste von Parametern mit Argumenten sein.
* `WorkingFolder` gibt das Arbeitsverzeichnis für den Prozess an, der gestartet werden soll. Sie können zwei Werte angeben:
	* `CodeBase`: Als Arbeitsverzeichnis wird das Verzeichnis "code" im Anwendungspaket festgelegt (Verzeichnis `Code` in der abgebildeten Struktur).
	* `CodePackage`: Als Arbeitsverzeichnis wird das Stammverzeichnis des Anwendungspakets (`MyServicePkg`) festgelegt. Das Element `WorkingDirectory` ist hilfreich, um das richtige Arbeitsverzeichnis festzulegen, damit die Anwendung (oder Initialierungsskripts) relative Pfade verwenden kann. Es gibt auch einen anderen Wert, den Sie für das Element `WorkingFolder` angeben können (`Work`). Dies ist aber nicht sehr hilfreich bei der Verwendung einer vorhandenen Anwendung.


```

\applicationmanifest.xml
\MyServicePkg

			\servicemanifest.xml
			\code
				 \bin
				  \ ...
			\config
			\data
			\...

```


#### SetupEntryPoint

	<SetupEntryPoint>
	    <ExeHost>
	        <Program>scripts\myAppsetup.cmd</Program>
	    </ExeHost>
	</SetupEntryPoint>


`SetupEntrypoint` wird verwendet, um eine ausführbare Datei oder Batchdatei anzugeben, die vor dem Starten des Dienstcodes ausgeführt werden soll. Dies ist ein optionales Element, das nicht angegeben werden muss, wenn keine Initialisierung/kein Setup erforderlich ist. Der Einstiegspunkt wird bei jedem Neustart des Diensts ausgeführt. Es gibt nur ein Element des Typs "SetupEntryPoint". Daher müssen Setup-/Konfigurationsskripts in einer Batchdatei gebündelt werden, wenn für das Setup/die Konfiguration der Anwendung mehrere Skripts erforderlich sind. Wie das Element `Entrypoint` kann auch das Element `SetupEntrypoint` jeden beliebigen Dateityp ausführen: ausführbare Datei, Batchdatei, Powershell-Cmdlet. Im obigen Beispiel basiert `SetupEntrypoint` auf der Batchdatei "myAppsetup.cmd", die sich im Unterverzeichnis "scripts" des Verzeichnisses "code" befindet (sofern das Element `WorkingDirectory` auf `Code` eingestellt ist).

## Anwendungsmanifestdatei

Nach der Konfiguration der Datei `servicemanifest.xml` müssen Sie einige Änderungen an der Datei `applicationmanifest.xml` vornehmen, um sicherzustellen, dass der richtige Diensttyp und der richtige Name verwendet werden.

	<ServiceManifestImport>
		<ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0.0" />
	</ServiceManifestImport>
	<DefaultServices>
	<Service Name="actor2">
	  <StatelessService ServiceTypeName="MyServiceType" InstanceCount = "1">
	  </StatelessService>
	</Service>
	</DefaultServices>

### ServiceManifestImport

In `ServiceManifestImport` können Sie einen oder mehrere Dienste angeben, die in der Anwendung enthalten sein sollen. `ServiceManifestName` verweist auf die Dienste. Dieses Element gibt den Namen des Verzeichnisses an, in dem sich die Datei `servicemanifest.xml` befindet.

	<ServiceManifestImport>
		<ServiceManifestRef ServiceManifestName="MyServicePkg" ServiceManifestVersion="1.0.0.0" />
	</ServiceManifestImport>

### DefaultServices

Das Element `DefaultServices` in der Anwendungsmanifestdatei wird verwendet, um einige Diensteigenschaften zu definieren.

	<DefaultServices>
	<Service Name="actor2">
	  <StatelessService ServiceTypeName="MyServiceType" InstanceCount="1">
	  </StatelessService>
	</Service>


* `ServiceTypeName` wird als "ID" für den Dienst verwendet. Im Zusammenhang mit dem Portieren einer vorhandenen Anwendung muss `ServiceTypeName` lediglich eine eindeutige Kennung für Ihren Dienst sein.
* `StatelessService`: Service Fabric unterstützt Dienste mit und ohne Status ("stateful" und "stateless"). Beim Portieren einer vorhandenen Anwendung hat der Dienst keinen Status. Daher sollte stets `StatelessService` verwendet werden.

Ein Service Fabric-Dienst kann in verschiedenen "Konfigurationen" bereitgestellt werden: Eine Instanz, mehrere Instanzen oder eine Instanz des Diensts auf jedem Knoten des Service Fabric-Clusters, um nur einige Beispiele zu nennen. In der Datei `applicationmanifest.xml` können Sie angeben, wie die Anwendung bereitgestellt werden soll.

* `InstanceCount` wird verwendet, um anzugeben, wie viele Instanzen des Diensts im Service Fabric-Cluster gestartet werden sollen. Sie können den Wert `InstanceCount` nach der Art der bereitzustellenden Anwendung festlegen. Die beiden häufigsten Szenarien:
	* `InstanCount = "1"`: In diesem Fall wird nur eine Instanz des Dienstes im Cluster bereitgestellt. Der Service Fabric-Planer bestimmt den Knoten, auf dem der Dienst bereitgestellt werden soll. Eine einzelne Instanz ist auch für Anwendungen sinnvoll, die bei der Ausführung mit mehreren Instanzen eine andere Konfiguration benötigen. In diesem Fall ist es einfacher, mehrere Dienste in der gleichen Anwendungsmanifestdatei zu definieren und `InstanceCount = "1"` zu verwenden. Im Endergebnis haben Sie mehrere Instanzen desselben Dienstes, aber jeweils mit einer anderen Konfiguration. Für `InstanceCount` einen Wert größer als eins festzulegen ist nur dann sinnvoll, wenn das Ziel besteht darin, mehrere Instanzen mit exakt derselben Konfiguration bereitzustellen.
	* `InstanceCount ="-1"`: In diesem Fall wird eine Instanz des Diensts auf jedem Knoten im Service Fabric-Cluster bereitgestellt. Das Endergebnis ist eine (und nur eine) Instanz des Diensts für jeden Knoten im Cluster. Dies ist eine praktische Konfiguration für Front-End-Anwendungen (z. B. REST-Endpunkt), da Client-Anwendungen nur eine Verbindung zu einem Knoten im Cluster herstellen müssen, um den Endpunkt zu verwenden. Diese Konfiguration kann z. B. auch verwendet werden, wenn alle Knoten des Service Fabric-Clusters mit einem Lastenausgleichsmodul verbunden sind, damit der Datenverkehr der Clients über den Dienst verteilt werden kann, der auf allen Knoten im Cluster ausgeführt wird.


### Testen
Bei vorhandenen Anwendungen ist es äußerst praktisch, Konsolenprotokolle anzeigen zu können, um sicherzustellen, dass für Anwendung und Konfigurationskripts keine Fehler angezeigt werden. In der Datei `servicemanifest.xml` kann mit dem Element `ConsoleRedirection` eine Konsolenumleitung konfiguriert werden.

	<EntryPoint>
	  <ExeHost>
	    <Program>node.exe</Program>
	    <Arguments>server.js</Arguments>
	    <WorkingFolder></WorkingFolder>
	    <ConsoleRedirection FileRetentionCount="5" FileMaxSizeInKb="2048"/>
	  </ExeHost>
	</EntryPoint>


* Mit `ConsoleRedirection` kann die Konsolenausgabe (stdout und stderr) in ein Arbeitsverzeichnis umgeleitet werden, um sicherzustellen, dass bei der Konfiguration oder Ausführung der Anwendung im Service Fabric-Cluster keine Fehler aufgetreten sind.
	* `FileRetentionCount` legt fest, wie viele Dateien im Arbeitsverzeichnis gespeichert werden. Der Wert 5 zum Beispiel bedeutet, dass die Protokolldateien für die letzten fünf Ausführungsvorgänge im Arbeitsverzeichnis gespeichert werden.
	* `FileMaxSizeInKb` gibt die maximale Größe der Protokolldateien an. 

Die Protokolldateien werden in einem der Arbeitsverzeichnisse für den Dienst gespeichert. Um zu bestimmen, wo sich die Dateien befinden, müssen Sie den Service Fabric-Explorer verwenden und feststellen, auf welchem Knoten der Dienst ausgeführt und welches Arbeitsverzeichnis aktuell verwendet wird.

Identifizieren Sie im Service Fabric-Explorer den Knoten, auf dem der Dienst ausgeführt wird.

![][3]

Wenn Sie den Knoten kennen, auf dem der Dienst aktuell ausgeführt wird, können Sie feststellen, welches Arbeitsverzeichnis verwendet wird.

![][4]

Wenn Sie den Namen des Diensts auswählen, können Sie im rechten Bereich sehen, wo Dienstcode und Einstellungen gespeichert sind.

![][5]

Wenn Sie auf den Link im Feld "Speicherort" klicken, können Sie auf das Verzeichnis für den ausgeführten Dienst zugreifen.

![][6]

Das Protokollverzeichnis enthält alle Protokolldateien.

Hinweis: Dieses Beispiel enthält einen Dienst, der als einzelne Instanz im Cluster ausgeführt wird. Wenn mehrere Instanzen vorhanden sind, müssen Sie die Protokolldatei auf allen Knoten überprüfen, auf denen der Dienst ausgeführt wird.

 
## Nächste Schritte
Wir arbeiten an der Entwicklung eines Tools, mit dem eine vorhandene Anwendung einfach mithilfe des Mauszeigers im Stammverzeichnis der Verzeichnisstruktur der Anwendung ausgewählt und gepackt werden kann. Das Tool kümmert sich um die Erzeugung der Manifestdateien und die Konfiguration der Grundeinstellungen, die für die "Transformation" der Anwendung in einen Service Fabric-Dienst erforderlich sind.


<!--Image references-->
[1]: ./media/service-fabric-deploy-an-existing-app/directory-structure-1.PNG
[2]: ./media/service-fabric-deploy-an-existing-app/directory-structure-2.PNG
[3]: ./media/service-fabric-deploy-an-existing-app/service-node-1.PNG
[4]: ./media/service-fabric-deploy-an-existing-app/service-node-2.PNG
[5]: ./media/service-fabric-deploy-an-existing-app/service-node-3.PNG
[6]: ./media/service-fabric-deploy-an-existing-app/service-node-4.PNG

	

<!---HONumber=August15_HO6-->