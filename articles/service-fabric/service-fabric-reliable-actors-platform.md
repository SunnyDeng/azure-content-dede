<properties
   pageTitle="Reliable Actors in Service Fabric | Microsoft Azure"
   description="Beschreibt, wie das Reliable Actors-Modell die Features der Service Fabric-Plattform nutzt, wobei Konzepte aus Sicht von Entwicklern von Actors behandelt werden."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="03/15/2016"
   ms.author="amanbha"/>

# Verwendung der Service Fabric-Plattform durch Reliable Actors

Actors verwenden das Azure Service Fabric-Anwendungsmodell zum Verwalten des Lebenszyklus der Anwendung. Jeder Actor-Typ ist einem Service Fabric-[Diensttyp](service-fabric-application-model.md#describe-a-service) zugeordnet. Der Actor-Code wird als Service Fabric-Anwendung [gepackt](service-fabric-application-model.md#package-an-application) und für das Cluster [bereitgestellt](service-fabric-deploy-remove-applications.md#deploy-an-application).

## Beispiel eines Anwendungsmodellkonzepts für Akteure

Nehmen wir das Beispiel eines [mit Visual Studio erstellten](service-fabric-reliable-actors-get-started.md) Actor-Projekts, um einige der oben aufgeführten Konzepte zu veranschaulichen.

Das Anwendungsmanifest, das Dienstmanifest und die Konfigurationsdatei „Settings.xml“ werden dem Projekt für den Actor-Dienst hinzugefügt, wenn die Lösung in Visual Studio erstellt wird. Dies zeigt der nachfolgende Screenshot.

![Mithilfe von Visual Studio erstelltes Projekt][1]

Den Anwendungstyp und die Version der Anwendung, in die der Actor gepackt wird, finden Sie in dem Anwendungsmanifest, das im Projekt für den Actor-Dienst enthalten ist. Der folgende Ausschnitt aus einem Anwendungsmanifest ist ein Beispiel dafür.

~~~
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                     ApplicationTypeName="VoiceMailBoxApplication"
                     ApplicationTypeVersion="1.0.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric">
~~~

Den Diensttyp, dem der Actor-Typ zugeordnet ist, finden Sie in dem Dienstmanifest, das im Projekt für den Actor-Dienst enthalten ist. Der folgende Ausschnitt aus einem Dienstmanifest ist ein Beispiel dafür.

~~~
<StatefulServiceType ServiceTypeName="VoiceMailBoxActorServiceType" HasPersistedState="true">
~~~

Wenn das Anwendungspaket mit Visual Studio erstellt wird, geben die Protokolle im Buildausgabe-Fenster den Speicherort des Anwendungspakets an. Beispiel:

    -------- Package started: Project: VoiceMailBoxApplication, Configuration: Debug x64 ------
    VoiceMailBoxApplication -> C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug

Nachfolgend sehen Sie eine teilweise Auflistung des oben genannten Speicherorts (auf die vollständige Auflistung wurde der Kürze halber verzichtet):

    C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug>tree /f
    Folder PATH listing
    Volume serial number is 303F-6F91
    C:.
    │   ApplicationManifest.xml
    │
    ├───VoiceMailBoxActorServicePkg
    │   │   ServiceManifest.xml
    │   │
    │   ├───Code
    │   │   │   Microsoft.ServiceFabric.Actors.dll
    │   │   │       :
    │   │   │   Microsoft.ServiceFabric.Services.dll
    │   │   │   ServiceFabricServiceModel.dll
    │   │   │   System.Fabric.Common.Internal.dll
    │   │   │   System.Fabric.Common.Internal.Strings.dll
    │   │   │   VoiceMailBox.exe
    │   │   │   VoiceMailBox.exe.config
    │   │   │   VoiceMailBox.Interfaces.dll
    │   │   │
    │   │   └───de-DE
    │   │           System.Fabric.Common.Internal.Strings.resources.dll
    │   │
    │   └───Config
    │           Settings.xml
    │
    └───VoicemailBoxWebServicePkg
        │   ServiceManifest.xml
        │
        └───Code
            │   Microsoft.Owin.dll
            │       :
            │   Microsoft.ServiceFabric.Services.dll
            │       :
            │   System.Fabric.Common.Internal.dll
            │   System.Fabric.Common.Internal.Strings.dll
            │       :
            │   VoiceMailBox.Interfaces.dll
            │   VoicemailBoxWebService.exe
            │   VoicemailBoxWebService.exe.config
            │
            └───de-DE
                    System.Fabric.Common.Internal.Strings.resources.dll

Die obige Auflistung zeigt die Assemblys, die den VoicemailBox-Actor implementieren, der im Codepaket innerhalb des Dienstpakets, das sich im Anwendungspaket befindet, enthalten ist.

Nachfolgende Verwaltung (d. h. Upgrades und endgültiges Löschen) der Anwendung erfolgt ebenfalls mit Mechanismen der Anwendungsverwaltung in Service Fabric. Weitere Informationen finden Sie unter den Themen zu [Anwendungsmodell](service-fabric-application-model.md), [Anwendungsbereitstellung und -entfernung](service-fabric-deploy-remove-applications.md) und [Anwendungsupgrade](service-fabric-application-upgrade.md).

## Skalierbarkeit für Actor-Dienste
Clusteradministratoren können einen oder mehrere Actor-Dienste der einzelnen Dienst-Typen im Cluster erstellen. Jeder dieser Actor-Dienste kann eine oder mehrere Partitionen besitzen (ähnlich wie jeder andere Service Fabric-Dienst). Die Fähigkeit, mehrere Dienste eines Diensttyps zu erstellen (der einem Actor-Typ zugeordnet wird), sowie die Möglichkeit zum Erstellen mehrerer Partitionen für einen Dienst ermöglichen es, dass die Actor-Anwendung skaliert werden kann. Weitere Informationen finden Sie im Artikel über [Skalierbarkeit](service-fabric-concepts-scalability.md).

> [AZURE.NOTE] Zustandslose Actor-Dienste sind für eine [Instanz](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services)anzahl von 1 erforderlich. Mehr als eine Instanz eines zustandslosen Actor-Diensts in einer Partition wird nicht unterstützt. Aus diesem Grund haben zustandslose Actor-Dienste nicht die Möglichkeit, die Anzahl der Instanzen zum Erreichen der Skalierbarkeit zu erhöhen. Sie müssen die Skalierbarkeitsoptionen verwenden, die im [Skalierbarkeitsartikel](service-fabric-concepts-scalability.md) beschrieben werden.

## Service Fabric-Partitionskonzepte für Actors
Die ID eines Actors ist einer Partition eines Actor-Diensts zugeordnet. Der Actor wird innerhalb der Partition erstellt, der die Actor-ID zugeordnet ist. Beim Erstellen eines Actors schreibt die Laufzeit ein [EventSource-Ereignis](service-fabric-reliable-actors-diagnostics.md#eventsource-events), das angibt, in welcher Partition der Actor erstellt wird. Im Folgenden finden Sie ein Beispiel für dieses Ereignis, das angibt, dass ein Actor mit der ID `-5349766044453424161` innerhalb von Partition `b6afef61-be9a-4492-8358-8f473e5d2487` des Dienstes `fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService`, Anwendung `fabric:/VoicemailBoxAdvancedApplication` erstellt wurde.

    {
      "Timestamp": "2015-04-26T10:12:20.2485941-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -5349766044453424161, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: 0583c745-1bed-43b2-9545-29d7e3448156.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-5349766044453424161",
        "isStateful": "True",
        "replicaOrInstanceId": "130906628008120392",
        "partitionId": "b6afef61-be9a-4492-8358-8f473e5d2487",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

Ein weiterer Actor mit der ID `-4952641569324299627` wurde innerhalb einer anderen Partition (`5405d449-2da6-4d9a-ad75-0ec7d65d1a2a`) desselben Diensts erstellt, wie von dem Ereignis unten angegeben.

    {
      "Timestamp": "2015-04-26T15:06:56.93882-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor, actor ID: -4952641569324299627, stateful: True, replica/instance ID: 130,745,418,574,851,853, partition ID: c146fe53-16d7-4d96-bac6-ef54613808ff.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "Microsoft.Azure.Service.Fabric.Samples.VoicemailBox.VoiceMailBoxActor",
        "actorId": "-4952641569324299627",
        "isStateful": "True",
        "replicaOrInstanceId": "130745418574851853",
        "partitionId": "5405d449-2da6-4d9a-ad75-0ec7d65d1a2a",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

> [AZURE.NOTE] Einige Felder der oben aufgeführten Ereignisse wurden der Kürze halber weggelassen.

Die Partitions-ID kann verwendet werden, um weitere Informationen über die Partition abzurufen. Zum Beispiel kann das [Service Fabric-Explorer](service-fabric-visualizing-your-cluster.md)-Tool zum Anzeigen von Informationen über die Partition sowie den Dienst und die Anwendung, zu der er gehört, verwendet werden. Der folgende Screenshot zeigt Informationen über Partition `5405d449-2da6-4d9a-ad75-0ec7d65d1a2a`, die im obigen Beispiel den Actor mit ID `-4952641569324299627` enthalten hat.

![Informationen zu einer Partition in Service Fabric-Explorer][3]

Actors können die Partitions-ID, den Dienstnamen, den Anwendungsnamen sowie andere für die Service Fabric-Plattform spezifische Informationen über die Member `Host.ActivationContext` und `Host.StatelessServiceInitialization` oder `Host.StatefulServiceInitializationParameters` der Basisklasse, von der der Actor-Typ abgeleitet ist, auf programmgesteuerte Weise abrufen. Der folgende Codeausschnitt zeigt ein Beispiel.

```csharp
public void ActorMessage(StatefulActorBase actor, string message, params object[] args)
{
    if (this.IsEnabled())
    {
        string finalMessage = string.Format(message, args);
        ActorMessage(
            actor.GetType().ToString(),
            actor.Id.ToString(),
            actor.ActorService.ServiceInitializationParameters.CodePackageActivationContext.ApplicationTypeName,
            actor.ActorService.ServiceInitializationParameters.CodePackageActivationContext.ApplicationName,
            actor.ActorService.ServiceInitializationParameters.ServiceTypeName,
            actor.ActorService.ServiceInitializationParameters.ServiceName.ToString(),
            actor.ActorService.ServiceInitializationParameters.PartitionId,
            actor.ActorService.ServiceInitializationParameters.ReplicaId,
            FabricRuntime.GetNodeContext().NodeName,
            finalMessage);
    }
}
```

### Service Fabric-Partitionskonzepte für statusfreie Actors
Statusfreie Actors werden innerhalb einer Partition eines statusfreien Service Fabric-Dienstes erstellt. Die Actor-ID bestimmt, unter welcher Partitionierung der Actor erstellt wird. Die [Instanz](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services)anzahl für einen statusfreien Actor-Dienst muss 1 sein. Das Ändern die Anzahl der Instanzen auf einen anderen Wert wird nicht unterstützt. Daher wird der Actor innerhalb der einzelnen Dienstinstanz in der Partition erstellt.

> [AZURE.TIP] Die Fabric Actors-Laufzeit gibt einige [Ereignisse im Zusammenhang mit statusfreien Actor-Instanzen](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateless-actor-instances) aus. Sie sind hilfreich bei der Diagnose und Leistungsüberwachung.

Beim Erstellen eines statusfreien Actors schreibt die Laufzeit ein [EventSource-Ereignis](service-fabric-reliable-actors-diagnostics.md#eventsource-events), das angibt, in welcher Partition und Instanz der Actor erstellt wird. Nachstehend sehen Sie ein Beispiel dieses Ereignisses. Es gibt an, dass ein Actor mit der ID `abc` innerhalb von Instanz `130745709600495974` von Partition `8c828833-ccf1-4e21-b99d-03b14d4face3`, von Dienst `fabric:/HelloWorldApplication/HelloWorldActorService`, Anwendung `fabric:/HelloWorldApplication` erstellt wurde.

    {
      "Timestamp": "2015-04-26T18:17:46.1453113-07:00",
      "ProviderName": "Microsoft-ServiceFabric-Actors",
      "Id": 5,
      "Message": "Actor activated. Actor type: HelloWorld.HelloWorld, actor ID: abc, stateful: False, replica/instance ID: 130,745,709,600,495,974, partition ID: 8c828833-ccf1-4e21-b99d-03b14d4face3.",
      "EventName": "ActorActivated",
      "Payload": {
        "actorType": "HelloWorld.HelloWorld",
        "actorId": "abc",
        "isStateful": "False",
        "replicaOrInstanceId": "130745709600495974",
        "partitionId": "8c828833-ccf1-4e21-b99d-03b14d4face3",
        "serviceName": "fabric:/HelloWorldApplication/HelloWorldActorService",
        "applicationName": "fabric:/HelloWorldApplication",
      }
    }

> [AZURE.NOTE] Einige Felder des oben aufgeführten Ereignisses wurden der Kürze halber weggelassen.

### Service Fabric-Partitionskonzepte für statusbehaftete Actors
Statusbehaftete Actors werden innerhalb einer Partition des statusbehafteten Service Fabric-Dienstes erstellt. Die Actor-ID bestimmt, unter welcher Partitionierung der Actor erstellt wird. Jede Partition des Dienstes kann über ein oder mehrere [Replikate](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services) verfügen, die sich auf verschiedenen Knoten im Cluster befinden. Über mehrere Replikate zu verfügen, bietet Zuverlässigkeit für den Actor-Status. Der Azure-Ressourcen-Manager optimiert die Platzierung basierend auf den verfügbaren Fehler- und Upgradedomänen im Cluster. Es werden nie zwei Replikate einer Partition auf demselben Knoten platziert. Die Actors werden immer im primären Replikat der Partition erstellt, der ihre Actor-ID zugeordnet ist.

> [AZURE.TIP] Die Fabric Actors-Laufzeit gibt einige [Ereignisse im Zusammenhang mit statusbehafteten Actor-Replikaten](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateful-actor-replicas) aus. Sie sind hilfreich bei der Diagnose und Leistungsüberwachung.

Denken Sie daran, dass in dem [VoiceMailBoxActor-Beispiel weiter oben](#service-fabric-partition-concepts-for-actors) der Actor mit der ID `-4952641569324299627` innerhalb von Partition `5405d449-2da6-4d9a-ad75-0ec7d65d1a2a` erstellt wurde. Das EventSource-Ereignis aus diesem Beispiel gab außerdem an, dass der Actor in Replikat `130745418574851853` dieser Partition erstellt wurde. Dies war das primäre Replikat der Partition zum Zeitpunkt der Erstellung des Actors. Der folgende Service Fabric-Explorer-Screenshot bestätigt dies.

![Ein primäres Replikat in Service Fabric-Explorer][4]

## Actor-Statusanbieter-Optionen
Es gibt einige standardmäßige Actor-Zustandsanbieter, die in der Actors-Laufzeit enthalten sind. Um einen entsprechenden Statusanbieter für einen Actor-Dienst auszuwählen, ist es erforderlich zu verstehen, wie die Status-Anbieter die zugrunde liegenden Features der Service Fabric-Plattform nutzen, um den Actor-Status hochverfügbar zu machen.

Standardmäßig verwendet ein statusbehafteter Actor einen Actor-Zustandsanbieter aus dem Schlüsselwertspeicher. Dieser Statutsanbieter basiert auf dem verteilten Schlüsselwertspeicher, der von der Service Fabric-Plattform bereitgestellt wird. Der Status wird dauerhaft auf dem lokalen Datenträger des Knotens gespeichert, der das primäre [Replikat](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services) hostet. Der Status wird auch repliziert und dauerhaft auf den lokalen Datenträgern der Knoten gespeichert, die die sekundären Replikate hosten. Die Statussicherung ist erst abgeschlossen, nachdem ein Quorum von Replikaten den Status auf seinen lokalen Datenträgern gespeichert hat. Der Schlüsselwertspeicher verfügt über erweiterte Funktionen zum Erkennen von Inkonsistenzen, wie z. B. falscher Fortschritt, und für ihre automatische Korrektur.

Die Actors-Laufzeit enthält auch `VolatileActorStateProvider`. Dieser Zustandsanbieter repliziert den Status in Replikate (Kopien des Status), der Status bleibt jedoch bei jedem Replikat im Arbeitsspeicher. Wenn ein Replikat ausfällt und wieder hoch kommt, wird sein Status anhand des anderen Replikats erneut erstellt. Wenn jedoch alle Replikate gleichzeitig ausfallen, gehen die Statusdaten verloren. Daher eignet sich dieser Zustandsanbieter für Anwendungen, bei denen die Daten das Ausfallen einiger Replikate sowie geplante Failover, wie z. B. Upgrades, überleben können. Wenn alle Replikate verloren gegangen sind, müssen die Daten mithilfe von Mechanismen außerhalb von Service Fabric neu erstellt werden. Sie können den statusbehafteten Actor für die Verwendung eines flüchtigen Actor-Status-Anbieters konfigurieren, indem Sie das Attribut `VolatileActorStateProvider` der Actor-Klasse oder einem Attribut auf Assemblyebene hinzufügen.

Der folgende Codeausschnitt zeigt, wie alle Actors in der Assembly geändert werden, die über kein explizites Zustandsanbieterattribut für die Verwendung von `VolatileActorStateProvider` verfügt.

```csharp
[assembly:Microsoft.ServiceFabric.Actors.VolatileActorStateProvider]
```

Der folgende Codeausschnitt zeigt, wie der Zustandsanbieter für einen bestimmten Actor-Typ, in diesem Fall `VoicemailBox`, in `VolatileActorStateProvider` geändert wird.

```csharp
[VolatileActorStateProvider]
public class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

Beachten Sie, dass bei Änderungen des Zustandsanbieters der Actor-Dienst neu erstellt werden muss. Status-Anbieter können nicht als Teil des Anwendungsupgrades geändert werden.

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/manifests-in-vs-solution.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png

<!---HONumber=AcomDC_0316_2016-->