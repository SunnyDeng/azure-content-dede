<properties
   pageTitle="Verwenden der Service Fabric-Plattform durch Fabric Actors"
   description="Dieser Artikel beschreibt, wie Fabric Actors die Features der Service Fabric-Plattform verwenden. Es werden Konzepte der Service Fabric-Plattform aus der Sicht des Actor-Entwicklers behandelt."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/23/2015"
   ms.author="abhisram"/>

# Verwenden der Service Fabric-Plattform durch Fabric Actors

## Service Fabric-Anwendungsmodellkonzepte für Actors
Actors verwenden das Service Fabric-Anwendungsmodell zum Verwalten des Lebenszyklus der Anwendung. Jeder Actor-Typ ist einem Service Fabric-[Diensttyp](service-fabric-application-model.md#describe-a-service) zugeordnet. Der Actor-Code wird als Service Fabric-Anwendung [gepackt](service-fabric-application-model.md#package-an-application) und für das Cluster [bereitgestellt](service-fabric-deploy-remove-applications.md#deploy-an-application).

Nehmen wir das Beispiel eines [mit Visual Studio erstellten](service-fabric-reliable-actors-get-started.md) Projekt-Actors, um einige der oben aufgeführten Konzepte zu veranschaulichen.

Das Anwendungsmanifest, das Dienstmanifest und die Konfigurationsdatei "Settings.xml" werden dem Projekt für den Actor-Dienst hinzugefügt, wenn die Projektmappe in Visual Studio erstellt wird. Dies zeigt der nachfolgende Screenshot.

![][1]

Anwendungstyp und die Version der Anwendung, in die der Actor gepackt wird, sind in dem Anwendungsmanifest enthalten, das in das Projekt für den Actor-Dienst eingefügt wird. Der folgende Ausschnitt aus einem Anwendungsmanifest ist ein Beispiel dafür.

~~~
<ApplicationManifest xmlns:xsd="http://www.w3.org/2001/XMLSchema"
                     xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
                     ApplicationTypeName="VoiceMailBoxApplication"
                     ApplicationTypeVersion="1.0.0.0"
                     xmlns="http://schemas.microsoft.com/2011/01/fabric">
~~~

Der Diensttyp, dem der Actor-Typ zuordnet ist, ist in dem Dienstmanifest enthalten, das in das Projekt für den Actor-Dienst eingefügt wird. Der folgende Ausschnitt aus einem Dienstmanifest ist ein Beispiel dafür.

~~~
<StatefulServiceType ServiceTypeName="VoiceMailBoxActorServiceType" HasPersistedState="true">
~~~

Wenn das Anwendungspaket mit Visual Studio erstellt wird, geben die Protokolle im Buildausgabe-Fenster den Speicherort des Anwendungspakets an. Beispiel:

    -------- Package started: Project: VoiceMailBoxApplication, Configuration: Debug x64 ------
    VoiceMailBoxApplication -> C:\samples\Samples\Actors\VS2015\VoiceMailBox\VoiceMailBoxApplication\pkg\Debug

Nachfolgend eine teilweise Auflistung des oben genannten Speicherorts (auf die vollständige Auflistung wurde der Kürze halber verzichtet):

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
    │   │   └───de-de
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
            └───de-de
                    System.Fabric.Common.Internal.Strings.resources.dll

Die obige Auflistung zeigt die Assemblys, die den VoicemailBox-Actor implementieren und in das Code-Paket, das im Dienstpaket und dieses wiederum im Anwendungspaket enthalten ist, eingefügt werden.

Die Visual Studio-Projektmappe enthält die PowerShell-Skripts, die zum Bereitstellen der Anwendung auf dem Cluster und zum Entfernen der Anwendung aus dem Cluster verwendet werden. Im nachfolgenden Screenshot sind die Skripts durch einen Kreis gekennzeichnet.

![][2]

Nachfolgende Verwaltung (d. h. Upgrades und endgültiges Löschen) der Anwendung erfolgt ebenfalls mithilfe Mechanismen der Anwendungsverwaltung in Service Fabric. Weitere Informationen finden Sie unter den Themen zu [Anwendungsmodell](service-fabric-application-model.md), [Anwendungsbereitstellung und Entfernung](service-fabric-deploy-remove-applications.md), und [Anwendungsupgrade](service-fabric-application-upgrade.md).

## Skalierbarkeit für Actor-Dienste
Clusteradministratoren können einen oder mehrere Actor-Dienste der einzelnen Dienst-Typen im Cluster erstellen. Jeder dieser Actor-Dienste kann eine oder mehrere Partitionen besitzen (ähnlich wie jeder andere Service Fabric-Dienst). Die Fähigkeit, mehrere Dienste eines Diensttyps zu erstellen (der einem Actor-Typ zugeordnet wird), sowie die Möglichkeit zum Erstellen mehrerer Partitionen für einen Dienst ermöglichen es, dass die Actor-Anwendung skaliert werden kann. Weitere Informationen finden Sie im Artikel über [Skalierbarkeit](service-fabric-concepts-scalability.md).

> [AZURE.NOTE]Zustandslose Actor-Dienste sind für eine [Instanz](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services)anzahl von 1 erforderlich. Mehr als eine Instanz eines zustandslosen Actor-Diensts in einer Partition wird nicht unterstützt. Aus diesem Grund haben zustandslose Actor-Dienste nicht die Möglichkeit, die Anzahl der Instanzen zum Erreichen der Skalierbarkeit zu erhöhen. Sie müssen die Skalierbarkeitsoptionen verwenden, die im [Skalierbarkeitsartikel](service-fabric-concepts-scalability.md) beschrieben werden.

## Service Fabric-Partitionskonzepte für Actors
Die Actor-ID eines Actors ist einer Partition eines Actor-Dienstes zugeordnet. Der Actor wird innerhalb der Partition erstellt, der die Actor-ID zugeordnet ist. Beim Erstellen eines Actors schreibt die Laufzeit ein [EventSource-Ereignis](service-fabric-reliable-actors-diagnostics.md#eventsource-events), das angibt, in welcher Partition der Actor erstellt wird. Im Folgenden finden Sie ein Beispiel für dieses Ereignis, das angibt, dass ein Actor mit der ID `-5349766044453424161` innerhalb von Partition `0583c745-1bed-43b2-9545-29d7e3448156` des Dienstes `fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService`, Anwendung `fabric:/VoicemailBoxAdvancedApplication` erstellt wurde.

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
        "replicaOrInstanceId": "130745418574851853",
        "partitionId": "0583c745-1bed-43b2-9545-29d7e3448156",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

Ein weiterer Actor mit der ID `-4952641569324299627` wurde innerhalb einer anderen Partition `c146fe53-16d7-4d96-bac6-ef54613808ff` desselben Dienstes erstellt, wie von dem Ereignis unten angegeben.

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
        "partitionId": "c146fe53-16d7-4d96-bac6-ef54613808ff",
        "serviceName": "fabric:/VoicemailBoxAdvancedApplication/VoicemailBoxActorService",
        "applicationName": "fabric:/VoicemailBoxAdvancedApplication",
      }
    }

*Hinweis:* Einige Felder des oben aufgeführten Ereignisse wurden der Kürze halber weggelassen.

Die Partitions-ID kann verwendet werden, um weitere Informationen über die Partition abzurufen. Zum Beispiel kann das [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)-Tool zum Anzeigen von Informationen über die Partition sowie den Dienst und die Anwendung, zu der er gehört, verwendet werden. Der folgende Screenshot zeigt Informationen über Partition `c146fe53-16d7-4d96-bac6-ef54613808ff`, die im obigen Beispiel den Actor mit ID `-4952641569324299627` enthalten hat.

![][3]

Actors können die Partitions-ID, den Dienstnamen, den Anwendungsnamen sowie andere für die Service Fabric-Plattform spezifische Informationen über die Member `Host.ActivationContext` und `Host.StatelessServiceInitialization` oder `Host.StatefulServiceInitializationParameters` der Basisklasse, von der der Actor-Typ abgeleitet ist, auf programmgesteuerte Weise abrufen. Der folgende Codeausschnitt zeigt ein Beispiel:

```csharp
public void ActorMessage<TState>(Actor<TState> actor, string message, params object[] args)
{
    string finalMessage = string.Format(message, args);
    this.ActorMessage(
        actor.GetType().ToString(),
        actor.Id.ToString(),
        actor.Host.ActivationContext.ApplicationTypeName,
        actor.Host.ActivationContext.ApplicationName,
        actor.Host.StatefulServiceInitializationParameters.ServiceTypeName,
        actor.Host.StatefulServiceInitializationParameters.ServiceName.ToString(),
        actor.Host.StatefulServiceInitializationParameters.PartitionId,
        actor.Host.StatefulServiceInitializationParameters.ReplicaId,
        FabricRuntime.GetNodeContext().NodeName,
        finalMessage);
}
```

### Service Fabric-Partitionskonzepte für statusfreie Actors
Statusfreie Actors werden innerhalb einer Partition eines statusfreien Service Fabric-Dienstes erstellt. Die Actor-ID bestimmt, unter welcher Partitionierung der Actor erstellt wird. Die [Instanz](service-fabric-availability-services.md#availability-of-service-fabric-stateless-services)anzahl für einen statusfreien Actor-Dienst muss 1 sein. Das Ändern die Anzahl der Instanzen auf einen anderen Wert wird nicht unterstützt. Daher wird der Actor innerhalb der einzelnen Dienstinstanz in der Partition erstellt.

> [AZURE.TIP]Die Fabric Actors-Laufzeit gibt einige [Ereignisse im Zusammenhang mit statusfreien Actor-Instanzen](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateless-actor-instances) aus. Sie sind hilfreich bei der Diagnose und Leistungsüberwachung.

Beim Erstellen eines statusfreien Actors schreibt die Laufzeit ein [EventSource-Ereignis](service-fabric-reliable-actors-diagnostics.md#eventsource-events), das angibt, in welcher Partition und Instanz der Actor erstellt wird. Im Folgenden finden Sie ein Beispiel für dieses Ereignis, das angibt, dass ein Actor mit der ID `abc` innerhalb von Instanz `130745709600495974` von Partition `8c828833-ccf1-4e21-b99d-03b14d4face3`, von Dienst `fabric:/HelloWorldApplication/HelloWorldActorService`, Anwendung `fabric:/HelloWorldApplication` erstellt wurde.

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

*Hinweis:* Einige Felder des oben aufgeführten Ereignisses wurden der Kürze halber weggelassen.

### Service Fabric-Partitionskonzepte für statusbehaftete Actors
Statusbehaftete Actors werden innerhalb einer Partition des statusbehafteten Service Fabric-Dienstes erstellt. Die Actor-ID bestimmt, unter welcher Partitionierung der Actor erstellt wird. Jede Partition des Dienstes kann über ein oder mehrere [Replikate](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services) verfügen, die sich auf verschiedenen Knoten im Cluster befinden. Über mehrere Replikate zu verfügen, bietet Zuverlässigkeit für den Actor-Status. Der Ressourcen-Manager optimiert die Platzierung basierend auf den verfügbaren Fehler- und Upgradedomänen im Cluster. Es werden nie zwei Replikate einer Partition auf demselben Knoten platziert. Die Actors werden immer im primären Replikat der Partition erstellt, der ihre Actor-ID zugeordnet ist.

> [AZURE.TIP]Die Fabric Actors-Laufzeit gibt einige [Ereignisse im Zusammenhang mit statusbehafteten Actor-Replikaten](service-fabric-reliable-actors-diagnostics.md#events-related-to-stateful-actor-replicas) aus. Sie sind hilfreich bei der Diagnose und Leistungsüberwachung.

Denken Sie daran, dass in dem [VoiceMailBoxActor-Beispiel weiter oben](#service-fabric-partition-concepts-for-actors) der Actor mit der ID `-4952641569324299627` innerhalb von Partition `c146fe53-16d7-4d96-bac6-ef54613808ff` erstellt wurde. Das EventSource-Ereignis aus diesem Beispiel gab außerdem an, dass der Actor in Replikat `130745418574851853` dieser Partition erstellt wurde. Dies war das primäre Replikat der Partition zum Zeitpunkt der Erstellung des Actors. Der folgende Service Fabric-Explorer-Screenshot bestätigt dies.

![][4]

## Actor-Statusanbieter-Optionen
Es gibt einige standardmäßige Actor-Status-Anbieter, die in der Actors-Laufzeit enthalten sind. Um einen entsprechenden Statusanbieter für einen Actor-Dienst auszuwählen, ist es erforderlich zu verstehen, wie die Status-Anbieter die zugrunde liegenden Features der Service Fabric-Plattform nutzen, um den Actor-Status hochverfügbar zu machen.

Standardmäßig verwendet ein statusbehafteter Actor einen Actor-Status-Anbieter mit Schlüsselwertspeicher. Dieser Statuts-Anbieter basiert auf dem verteilten Schlüsselwertspeicher, der von der Service Fabric-Plattform bereitgestellt wird. Der Status wird auf dem lokalen Datenträger des Knotens mit dem primären [Replikat](service-fabric-availability-services.md#availability-of-service-fabric-stateful-services) dauerhaft gespeichert sowie repliziert und auf den lokalen Datenträgern der Knoten mit den sekundären Replikaten dauerhaft gespeichert. Die Statussicherung ist erst abgeschlossen, nachdem ein Quorum von Replikaten den Status auf deren lokalen Datenträgern dauerhaft gespeichert hat. Der Schlüsselwertspeicher verfügt über erweiterte Funktionen zum Erkennen von Inkonsistenzen, wie z. B. falscher Fortschritt, und für ihre automatische Korrektur.

Die Actors-Laufzeit enthält auch einen `VolatileActorStateProvider`. Dieser Status-Anbieter repliziert den Status in Replikate, der Status bleibt jedoch im Arbeitsspeicher auf dem Replikat. Wenn ein Replikat ausfällt und wieder hoch kommt, wird sein Status anhand des anderen Replikats erneut erstellt. Wenn jedoch alle Replikate (Kopien des Status) gleichzeitig ausfallen, gehen die Statusdaten verloren. Daher eignet sich dieser Status-Anbieter für Anwendungen, bei denen die Daten das Ausfallen einiger Replikate sowie geplante Failover, wie z. B. Upgrades, überleben können. Wenn alle Replikate (Kopien) verloren gegangen sind, müssen die Daten mithilfe von Mechanismen außerhalb von Service Fabric neu erstellt werden. Sie können den statusbehafteten Actor für die Verwendung eines flüchtigen Actor-Status-Anbieters konfigurieren, indem Sie das Attribut `VolatileActorStateProvider` der Actor-Klasse oder einem Attribut auf Assemblyebene hinzufügen.

Der folgende Codeausschnitt zeigt, wie alle Actors in der Assembly geändert werden, die über kein explizites Status-Anbieter-Attribut für die Verwendung von `VolatileActorStateProvider` verfügt.

```csharp
[assembly:Microsoft.ServiceFabric.Actors.VolatileActorStateProvider]
```

Der folgende Codeausschnitt zeigt, wie der Status-Anbieter für einen bestimmten Actor-Typ, in diesem Fall `VoicemailBox`, in `VolatileActorStateProvider` geändert wird.

```csharp
[VolatileActorStateProvider]
public class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

Bitte beachten Sie, dass bei Änderungen des Status-Anbieters der Actor-Dienst neu erstellt werden muss. Status-Anbieter können nicht als Teil des Anwendungsupgrades geändert werden.

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-platform/manifests-in-vs-solution.png
[2]: ./media/service-fabric-reliable-actors-platform/app-deployment-scripts.png
[3]: ./media/service-fabric-reliable-actors-platform/actor-partition-info.png
[4]: ./media/service-fabric-reliable-actors-platform/actor-replica-role.png
 

<!---HONumber=July15_HO2-->