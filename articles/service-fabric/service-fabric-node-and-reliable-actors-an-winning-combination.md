<properties
   pageTitle="„node.js“ und Reliable Actors | Microsoft Azure"
   description="Enthält eine exemplarische Vorgehensweise zur Erstellung einer node.js-Expressanwendung, für die Reliable Actors verwendet werden und die zusätzlich zur Azure Service Fabric-Plattform ausgeführt wird."
   services="service-fabric"
   documentationCenter="nodejs"
   authors="clca"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="nodejs"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/17/2015"
   ms.author="claudioc"/>


# „node.js“ und Reliable Actors: eine erfolgreiche Kombination
Dieser Artikel enthält eine Übersicht darüber, wie Sie eine Anwendung erstellen können, für die „node.js“ und „Reliable Actors“ (zuverlässige Akteure) verwendet werden. Die endgültige Lösung ist eine Mischung aus JavaScript-Code, der hauptsächlich zum Bereitstellen der Front-End-Seite der App (Web/REST-APIs) verwendet wird, und C# für komplexere Berechnungen. Durch den Einsatz des Service Fabric-Programmiermodells ist die Anwendung sofort skalierbar und zuverlässig. Der Prozess basiert auf den folgenden Schritten:

1. Erstellen eines neuen zustandslosen oder zustandsbehafteten Service Fabric-Actor-Projekts mit Service Fabric-Tools für Visual Studio
2. Erstellen eines „node.js“-Projekts, z. B. mit [node.js-Tools für Visual Studio](https://github.com/Microsoft/nodejstools/releases/tag/v1.1.RC) 
3. Hinzufügen eines node.js-Projekts (z. B. eine einfache Express 4-App) zur Service Fabric-Projektmappe. Sie können einfach „Projektmappe/Vorhandenes Projekt hinzufügen“ verwenden, um das node.js-Projekt hinzuzufügen. 
4. Verpacken der node.js-App, damit sie mit VS-Tools für Service Fabric bereitgestellt werden kann

## Erstellen des node.js-Projekts
Nachdem Sie das node.js-Projekt erstellt und die Abhängigkeiten hinzugefügt haben, müssen Sie die Verzeichnisstruktur des Projekts so ändern, dass sie der Struktur der Service Fabric-Tools für Visual Studio entspricht. Sie ist erforderlich, um die App wie andere Service Fabric-Dienste verpacken und bereitstellen zu können. Das Ziel besteht darin, die Änderungen an der Verzeichnisstruktur nur einmal vorzunehmen. Sie profitieren dann davon, dass Sie den Bereitstellungsprozess in Visual Studio verwenden und Ihre node.js-App zusammen mit anderen Diensten in der Projektmappe bereitstellen können. An der Verzeichnisstruktur müssen folgende Änderungen vorgenommen werden:

1. Erstellen eines des Verzeichnisses „PackageRoot“
2. Erstellen des Verzeichnisses „Code“ unter „PackageRoot“
3. Verschieben aller Dateien und Verzeichnisse in das Verzeichnis „Code“

Wenn Sie fertig sind, sollte die Projektstruktur in Visual Studio wie folgt aussehen:

![][8]

Sie sehen, dass sich der gesamte node.js-Code unter dem Verzeichnis „PackageRoot/Code“ befindet. Service Fabric trifft keine Annahmen in Bezug auf die Anwendungen/Bibliotheken, die auf dem Knoten installiert sind, auf dem die Anwendung bereitgestellt werden soll. Sie müssen also alle Abhängigkeiten einschließen. Im Fall von „node.js“ müssen Sie „node.exe“ einbinden, damit Service Fabric den Code ausführen kann (Sie finden „node.exe“ im Verzeichnis „Programme\\nodejs“).

![][3]

## Hinzufügen der Metadatendatei „servicemanifest.xml“
Zum Bereitstellen der node.js-Anwendung müssen wir eine Metadatendatei hinzufügen, die benötigt wird, um einige Eigenschaften anzugeben. Diese werden von Service Fabric verwendet, um zu ermitteln, wie die Anwendung bereitgestellt und gestartet werden soll.

Dies ist ein Beispiel dafür, wie die Datei „servicemanifest.xml“ aussehen sollte. [Dieser Artikel](service-fabric-deploy-existing-app.md) enthält weitere Details zu den wichtigen Elementen, die aktualisiert werden müssen. Normalerweise muss Folgendes aktualisiert werden:

* Name (ServiceManifest-Element)
* ServiceTypeName (StatelessServiceType-Element)
* Argumente (ExeHost-Element) zum Angeben der JS-Datei, die zum Starten der App verwendet werden soll


```xml

<?xml version="1.0" encoding="utf-8"?>

<ServiceManifest Name="NodejsFrontendPkg"
                 Version="1.0.0.0"
                 xmlns="http://schemas.microsoft.com/2011/01/fabric"
                 xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                 xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance">

  <ServiceTypes>
    <StatelessServiceType ServiceTypeName="NodejsFrontendType" UseImplicitHost="true" />
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
  <ConfigPackage Name="Config" Version="1.0.0.0" />
  <Resources>
    <Endpoints>
      <Endpoint Name="ServiceEndpoint" />
    </Endpoints>
  </Resources>
</ServiceManifest>

```

> [AZURE.NOTE]Beachten Sie, dass eine der Optionen von „servicemanifest.xml“ unter Umständen mit „node.js“ nicht funktioniert. In einigen Fällen funktioniert `ConsoleRedirection` nicht, wenn ein node.js-Modul (z. B. Express) voraussetzt, dass fd für stdout und stderr die Werte 1 und 2 aufweist.

Nachdem die Datei `servicemanifest.xml` dem node.js-Projekt hinzugefügt wurde, sollte die Struktur des Projekts wie folgt aussehen:

![][4]

## Hinzufügen von Service Fabric-Binärdateien
Als Nächstes werden die Service Fabric-Binärdateien hinzugefügt. Sie werden verwendet, um eine Verbindung mit den Akteuren (Actors) herzustellen, die im Service Fabric-Cluster ausgeführt werden. Wie Sie im Beispiel „Edge.js“ sehen, sind Verweise auf Assemblys vorhanden. Um diese Assemblys für „edge.js“ verfügbar zu machen, müssen sie zusammen mit dem node.js-Code kopiert werden. Die einfachste Möglichkeit besteht darin, die Binärdateien aus einem vorhandenen Projekt zu kopieren. Folgende Dateien müssen im Verzeichnis „Code“ enthalten sein (und werden von „edge.js“ verwendet):

```
Microsoft.ServiceFabric.Actors.dll
Microsoft.ServiceFabric.Collections.dll
Microsoft.ServiceFabric.Data.dll
Microsoft.ServiceFabric.Data.Log.dll
Microsoft.ServiceFabric.ReplicatedStore.dll
Microsoft.ServiceFabric.Replicator.dll
Microsoft.ServiceFabric.Services.dll
ServiceFabricServiceModel.dll
System.Fabric.Common.Internal.dll
System.Fabric.Common.Internal.Strings.resources.dll
System.Fabric.dll
```

Nachdem Sie die Binärdateien dem Projekt hinzugefügt haben, sollte die Projektstruktur wie folgt aussehen:

![][5]

## Hinzufügen eines Verweises auf das node.js-Projekt in der applicationmanifest-Datei
Als Nächstes wird dem Anwendungsmanifest der node.js-Dienst hinzugefügt, um die Bereitstellung mit Visual Studio-Tools für Service Fabric zu ermöglichen. Dies ist erforderlich, weil für das node.js-Projekt keine Integration in Service Fabric-Tools für Visual Studio vorhanden ist. Daher muss das Hinzufügen manuell erfolgen.

In der Datei `applicationmanifest.xml` müssen Sie die folgenden Elemente hinzufügen:

* `ServiceManifestImport`
* `Service`

> [AZURE.NOTE]Achten Sie darauf, dass Sie die gleichen Namen wie in `servicemanifest.xml` verwenden, um `ServiceName` und `ServiceTypeName` anzugeben. Die Datei `applicationmanifest.xml` sollte Folgendem ähneln:

```
<?xml version="1.0" encoding="utf-8"?>
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance" ApplicationTypeName="NodeServiceFabricSampleApplication" ApplicationTypeVersion="1.0.0.0" xmlns="http://schemas.microsoft.com/2011/01/fabric">
    <ServiceManifestImport>
      <ServiceManifestRef ServiceManifestName="NodeServiceFabricSamplePkg" ServiceManifestVersion="1.0.0.0" />
   </ServiceManifestImport>
   <ServiceManifestImport>
       <ServiceManifestRef ServiceManifestName="VisualObjectsNodejsWebServicePkg" ServiceManifestVersion="1.0.0.0" />
       </ServiceManifestImport>
   <DefaultServices>
      <Service Name="NodeServiceFabricSampleActorService">
         <StatelessService ServiceTypeName="NodeServiceFabricSampleActorServiceType">
            <UniformInt64Partition PartitionCount="9" LowKey="-9223372036854775808" HighKey="9223372036854775807" />
         </StatelessService>
      </Service>
     <Service Name="NodejsFrontendPkg">
       <StatelessService ServiceTypeName="NodejsFrontendType">
         <SingletonPartition />
       </StatelessService>
     </Service>
   </DefaultServices>
</ApplicationManifest>

```

## Verwenden von Reliable Actors in der Node.js-App
Nachdem die Projektstruktur festgelegt wurde, können wir uns um den Code kümmern, mit dem die node.js-Anwendung eine Verbindung mit Reliable Actors im Cluster herstellen kann, wie dies auch mit einer .NET-Anwendung möglich ist. Das gewünschte Szenario ist die Erstellung einer node.js-Anwendung, die als Front-End/Gateway für die Clientanwendung fungiert, und das Verfügbarmachen einer webbasierten Anwendung oder einer Gruppe von REST-APIs zur Verwendung durch eine Client-App. „node.js“ stellt das Front-End der App bereit, während Reliable Actors die skalierbare und zuverlässige „Anwendungsserverebene“ der App bereitstellen. In Service Fabric können Reliable Actors mit der [ActorProxy-Klasse](service-fabric-reliable-actors-introduction.md#actor-communication) aufgerufen werden. Glücklicherweise gibt es ein gutes node.js-Modul, das zum Aufrufen von .NET-Code verwendet werden kann: [Edge.js](https://github.com/tjanczuk/edge). Sie können die Anleitungen im GitHub-Repository nutzen, um sich darüber zu informieren, wie Sie Edge auf Ihrem Computer installieren.

 
![][2]

Nachdem Sie Edge entweder per NPM oder mit der NPM-UI in Visual Studio installiert haben, müssen Sie das neu installierte Modul in das Unterverzeichnis „node\_modules“ des Verzeichnisses „Code“ verschieben (die Struktur des node.js-Projekts wurde geändert, und der gesamte Code wurde in das Verzeichnis „PackageRoot/Code“ verschoben). Gute Beispiele dazu, wie Sie Edge zum Aufrufen von .NET-Code verwenden können, finden Sie im edge.js-Repository bei GitHub. Unten ist ein einfaches Beispiel für das Aussehen des Codes angegeben, wenn Sie Reliable Actors mit der ActorProxy-Klasse aufrufen müssen.

```javascript

var getActorStatus = edge.func(function () {

/*

    #r "Microsoft.ServiceFabric.Actors.dll"
    #r "System.Globalization.dll"
    #r "System.dll"
    #r  "visualobjects.interfaces.dll"
    
    using Microsoft.ServiceFabric.Actors;
    using System.Globalization;
    using VisualObjects.Interfaces;
    using System.Threading.Tasks;
    using System; 


public class Startup
    {
         public async Task<object> Invoke(dynamic input)
        {

            var objectId = (string) input.actorId;
            var serviceUri = (string) input.serviceUri;
            var actId = new ActorId(objectId);
            var serId = new Uri(serviceUri);
            var actorProxy = ActorProxy.Create<IVisualObjectActor>(actId, serId);
            var buffer = await actorProxy.GetStateAsJsonAsync();
            if (!string.IsNullOrEmpty(buffer))
                return buffer;
                else 
                return "null";
         }
  }
 
 */ 

});

```

> [AZURE.NOTE]Für ActorProxy wird die Actor-Schnittstelle verwendet, um ermitteln zu können, mit welchem Actor eine Verbindung hergestellt werden soll. Damit der verwaltete Code, der im node.js-Prozess ausgeführt wird, diese Klasse instanziieren kann, muss die Actor-Schnittstellenassembly in das Verzeichnis „Code“ kopiert werden, wie die anderen Service Fabric-DLLs auch. Hinweis: Sie müssen die DLL jedes Mal kopieren (oder in VS eine Post-Build-Aktion festlegen), wenn Sie Änderungen an den Methoden/Parametern der Schnittstelle vornehmen.

![][6]

## Bereitstellung
An diesem Punkt kann das Projekt mit den Service Fabric-Tools für Visual Studio bereitgestellt werden. Der letzte Schritt des Prozesses ist das Verweisen auf das Projekt im Service Fabric-Anwendungsprojekt, damit es in das Anwendungspaket eingeschlossen und im Cluster bereitgestellt werden kann.

![][9]
 
Sie können die Anwendung (in der die node.js-App enthalten ist) bereitstellen, indem Sie beispielsweise das Kontextmenü der Projektmappe verwenden.

![][10]

## Nächste Schritte
* Erfahren Sie mehr über [Reliable Actors](service-fabric-reliable-actors-introduction.md).
* Erfahren Sie mehr über den [Service Fabric-Anwendungslebenszyklus](service-fabric-application-lifecycle.md).
* Erfahren Sie mehr über das Aktualisieren einer [Service Fabric-Anwendung](service-fabric-application-upgrade.md). 

<!-- images -->
[1]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure.PNG
[2]: ./media/service-fabric-node-and-reliable-actors-app/edge-js.PNG
[3]: ./media/service-fabric-node-and-reliable-actors-app/node-exe.PNG
[4]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-manifest.PNG
[5]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-with-dlls.PNG
[6]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-interface-dll.PNG
[7]: ./media/service-fabric-node-and-reliable-actors-app/project-structure-config.PNG
[8]: ./media/service-fabric-node-and-reliable-actors-app/nodejs-project-structure1.PNG
[9]: ./media/service-fabric-node-and-reliable-actors-app/application-project-reference.PNG
[10]: ./media/service-fabric-node-and-reliable-actors-app/solution-deploy.PNG

<!---HONumber=Sept15_HO3-->