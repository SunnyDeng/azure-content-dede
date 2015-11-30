<properties
   pageTitle="Bereitstellen einer Node.js-Anwendung mit MongoDB | Microsoft Azure"
   description="Exemplarische Vorgehensweise des Packens einer vorhandenen Anwendung, um sie in einem Azure Service Fabric-Cluster bereitzustellen"
   services="service-fabric"
   documentationCenter=".net"
   authors="bscholl"
   manager=""
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/09/2015"
   ms.author="bscholl"/>


# Bereitstellen mehrerer vorhandener Anwendungen

In diesem Artikel wird das Packen mehrerer Anwendungen und ihre Bereitstellung in Service Fabric mithilfe der Vorschauversion des Service Fabric-Packtools erläutert, die unter http://aka.ms/servicefabricpacktool verfügbar ist.

Informationen zum manuellen Erstellen eines Service Fabric-Pakets finden Sie im Artikel [Bereitstellen einer vorhandenen Anwendung in Azure Service Fabric](service-fabric-deploy-existing-app.md).

Wenngleich in dieser exemplarischen Vorgehensweise das Bereitstellen einer Anwendung mit einem Node.js-Front-End gezeigt wird, die MongoDB als Datenspeicher nutzt, gelten diese Schritte für alle Anwendungen mit Abhängigkeiten von einer anderen Anwendung.

## Packen der Node.js-Anwendung

Dieser Artikel setzt voraus, dass Node.js nicht auf den Knoten im Service Fabric-Cluster installiert ist. Daher müssen Sie vor dem Packen „node.exe“ dem Stammverzeichnis Ihrer Node.js-Anwendung hinzufügen. Die Verzeichnisstruktur der Node.js-Anwendung (mit dem Webframework Express und Vorlagenmodul Jade) sollte wie folgt aussehen:

```
|-- NodeApplication
	|-- bin
        |-- www
	|-- node_modules
        |-- .bin
        |-- express
        |-- jade
        |-- etc.
	|-- public
        |-- images
        |-- etc.
	|-- routes
        |-- index.js
        |-- users.js
    |-- views
        |-- index.jade
        |-- etc.
    |-- app.js
    |-- package.json
    |-- node.exe
```

Im nächsten Schritt erstellen Sie ein Anwendungspaket für die Node.js-Anwendung. Der folgende Code erstellt ein Service Fabric-Anwendungspaket, das die Node.js-Anwendung enthält.

```
.\ServiceFabricAppPackageUtil.exe /source:'[yourdirectory]\MyNodeApplication' /target:'[yourtargetdirectory] /appname:NodeService /exe:'node.exe' /ma:'bin/www' /AppType:NodeAppType
```

Es folgt eine Beschreibung der verwendeten Parameter:

- **/source**: Zeigt auf das Verzeichnis der Anwendung, das gepackt werden soll.
- **/target**: Definiert das Verzeichnis, in dem das Paket erstellt werden soll. Dieses Verzeichnis muss sich vom Zielverzeichnis unterscheiden.
- **/appname**: Gibt den Anwendungsnamen der vorhandenen Anwendung an. Wichtig ist der Hinweis, dass dieser Name in den Dienstnamen im Manifest und nicht in den Service Fabric-Anwendungsnamen übersetzt wird.
- **/exe**: Bestimmt die ausführbare Datei, die Service Fabric starten soll, in diesem Fall `node.exe`.
- **/ma**: Gibt das Argument an, das verwendet wird, um die ausführbare Datei zu starten. Da Node.js nicht installiert ist, muss Service Fabric den Node.js-Webserver durch Ausführen von `node.exe bin/www` starten. `/ma:'bin/www'` weist das Packtool an, `bin/ma` als Argument für „node.exe“ zu verwenden.
- **/AppType**: Bestimmt den Namen des Service Fabric-Anwendungstyps.  

Wenn Sie zum Verzeichnis zu navigieren, das im „/target“-Parameter angegeben wurde, sehen Sie, dass das Tool ein voll funktionsfähiges Service Fabric-Paket erstellt hat (siehe unten):

```
|--[yourtargetdirectory]
    |-- NodeApplication
        |-- C
		      |-- bin
              |-- data
              |-- node_modules
              |-- public
              |-- routes
              |-- views
              |-- app.js
              |-- package.json
              |-- node.exe
        |-- config
		      |--Settings.xml
	    |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Die generierte Datei „ServiceManifest.xml“ hat nun einen Abschnitt, der beschreibt, wie der Node.js-Webserver gestartet werden soll, was im folgenden Codeausschnitt gezeigt wird:

```xml
<CodePackage Name="C" Version="1.0">
    <EntryPoint>
        <ExeHost>
            <Program>node.exe</Program>
            <Arguments>'bin/www'</Arguments>
            <WorkingFolder>CodePackage</WorkingFolder>
        </ExeHost>
    </EntryPoint>
</CodePackage>
```
In diesem Beispiel lauscht der Node.js-Webserver an Port 3000. Deshalb müssen Sie die Endpunktinformationen in der Datei „ServiceManifest.xml“ wie unten dargestellt aktualisieren.

```xml
<Resources>
      <Endpoints>
     	<Endpoint Name="NodeServiceEndpoint" Protocol="http" Port="3000" Type="Input" />
      </Endpoints>
</Resources>
```
Nachdem Sie das Node.js-Anwendungspaket erstellt haben, können Sie nun das MongoDB-Paket erstellen. Wie bereits erwähnt, sind die Schritte, die Sie jetzt durchlaufen, nicht für Node.js und MongoDB spezifisch, sondern gelten für alle Anwendungen, die zusammen als eine Service Fabric-Anwendung gepackt werden sollen.

Für das MongoDB-Paket müssen Sie „mongod.exe“ und „mongo.exe“ packen. Beide Binärdateien befinden sich im Verzeichnis `bin` des MongoDB-Installationsverzeichnisses. Die Verzeichnisstruktur sieht ähnlich wie folgt aus.

```
|-- MongoDB
	|-- bin
        |-- mongod.exe
        |-- mongo.exe
        |-- etc.
```
Service Fabric muss MongoDB mit einem Befehl wie dem folgenden starten. Deshalb müssen Sie den `/ma`-Parameter beim Packen von MongoDB verwenden.

```
mongod.exe --dbpath [path to data]
```
> [AZURE.NOTE]Die Daten werden nicht bei einem Ausfall eines Knotens beibehalten, wenn Sie das MongoDB-Datenverzeichnis in das lokale Verzeichnis des Knotens einfügen. Sie müssen entweder beständigen Speicher verwenden oder ein MongoDB ReplicaSet implementieren, um Datenverluste zu vermeiden.

Über PowerShell oder die Befehlsshell führen wir das Packtool mit den folgenden Parametern aus:

```
.\ServiceFabricAppPackageUtil.exe /source: [yourdirectory]\MongoDB' /target:'[yourtargetdirectory]' /appname:MongoDB /exe:'bin\mongod.exe' /ma:'--dbpath [path to data]' /AppType:NodeAppType
```

Um MongoDB Ihrem Service Fabric-Anwendungspaket hinzuzufügen, müssen Sie sicherstellen, dass der „/target“-Parameter auf dasselbe Verzeichnis verweist, das bereits das Anwendungsmanifest und die Node.js-Anwendung enthält und dass Sie denselben Namen für „ApplicationType“ verwenden.

Lassen Sie uns zum Verzeichnis wechseln und untersuchen, was das Tool erstellt hat.

```
|--[yourtargetdirectory]
    |-- MyNodeApplication
    |-- MongoDB
        |-- C
            |--bin
                |-- mongod.exe
                |-- mongo.exe
                |-- etc.
        |-- config
		    |--Settings.xml
	    |-- ServiceManifest.xml
    |-- ApplicationManifest.xml
```
Wie Sie sehen, hat das Tool einen neuen Ordner namens „MongoDB“ dem Verzeichnis hinzugefügt, das die MongoDB-Binärdateien enthält. Wenn Sie die Datei `ApplicationManifest.xml` öffnen, sehen Sie, dass das Paket jetzt sowohl die Node.js-Anwendung als auch MongoDB enthält. Der folgende Code zeigt den Inhalt des Anwendungsmanifests.

```xml
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="MyNodeApp" ApplicationTypeVersion="1.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="MongoDB" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeService" ServiceManifestVersion="1.0" />
   </ServiceManifestImport>
   <DefaultServices>
      <Service Name="MongoDBService">
         <StatelessService ServiceTypeName="MongoDB">
            <SingletonPartition />
         </StatelessService>
      </Service>
      <Service Name="NodeServiceService">
         <StatelessService ServiceTypeName="NodeService">
            <SingletonPartition />
         </StatelessService>
      </Service>
   </DefaultServices>
</ApplicationManifest>  
```

Der letzte Schritt ist das Veröffentlichen der Anwendung im lokalen Service Fabric-Cluster mithilfe des folgenden PowerShell-Skripts:

```
Connect-ServiceFabricCluster localhost:19000

Write-Host 'Copying application package...'
Copy-ServiceFabricApplicationPackage -ApplicationPackagePath '[yourtargetdirectory]' -ImageStoreConnectionString 'file:C:\SfDevCluster\Data\ImageStore' -ApplicationPackagePathInImageStore 'Store\NodeAppType'

Write-Host 'Registering application type...'
Register-ServiceFabricApplicationType -ApplicationPathInImageStore 'Store\NodeAppType'

New-ServiceFabricApplication -ApplicationName 'fabric:/NodeApp' -ApplicationTypeName 'NodeAppType' -ApplicationTypeVersion 1.0  
```

Nach der erfolgreichen Veröffentlichung der Anwendung im lokalen Cluster können Sie auf die Node.js-Anwendung an dem Port zugreifen, den Sie in das Dienstmanifest der Node.js-Anwendung eingegeben haben, z. B.: http://localhost:3000.

In diesem Tutorial haben Sie erfahren, wie einfach das Packen zweier vorhandener Anwendungen zu einer Service Fabric-Anwendung und deren Bereitstellung in Service Fabric ist. Diese kann anschließend von verschiedenen Service Fabric-Features wie hohe Verfügbarkeit und Systemintegration profitieren.

Weitere Informationen finden Sie in den folgenden Themen:

[Service Fabric-Packformat](service-fabric-deploy-existing-app.md)

<!---HONumber=Nov15_HO4-->