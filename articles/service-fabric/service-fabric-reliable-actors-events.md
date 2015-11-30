<properties
   pageTitle="Reliable Actors-Ereignisse"
   description="Einführung in Ereignisse für Service Fabric Reliable Actors."
   services="service-fabric"
   documentationCenter=".net"
   authors="jessebenson"
   manager="timlt"
   editor="vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="11/14/2015"
   ms.author="amanbha"/>


# Actor-Ereignisse
Actor-Ereignisse bieten eine Möglichkeit zum Senden von Best-Effort-Nachrichten vom Actor an die Clients. Actor-Ereignisse dienen der Actor-Client-Kommunikation und sollten NICHT für die Actor-Actor-Kommunikation verwendet werden.

Die folgenden Codeausschnitte zeigen, wie Actor-Ereignisse in Ihrer Anwendung verwenden werden.

Definieren Sie eine Schnittstelle, die die vom Actor veröffentlichten Ereignisse beschreibt. Diese Schnittstelle muss von der `IActorEvents`-Schnittstelle abgeleitet werden. Die Argumente der Methoden müssen [Datenvertrag-serialisierbar](service-fabric-reliable-actors-notes-on-actor-type-serialization.md) sein. Die Methoden müssen "void" zurückgeben, da Ereignisbenachrichtigungen unidirektional und vom Typ "best effort " sind.

```csharp
public interface IGameEvents : IActorEvents
{
    void GameScoreUpdated(Guid gameId, string currentScore);
}
```

Deklarieren Sie die vom Actor in der Actor-Schnittstelle veröffentlichten Ereignisse.

```csharp
public interface IGameActor : IActor, IActorEventPublisher<IGameEvents>
{
    Task UpdateGameStatus(GameStatus status);

    [Readonly]
    Task<string> GetGameScore();
}
```

Implementieren Sie auf der Clientseite den Ereignishandler.

```csharp
class GameEventsHandler : IGameEvents
{
    public void GameScoreUpdated(Guid gameId, string currentScore)
    {
        Console.WriteLine(@"Updates: Game: {0}, Score: {1}", gameId, currentScore);
    }
}
```

Erstellen Sie auf dem Client einen Proxy für den Actor, der das Ereignis veröffentlicht, und abonnieren Sie diese Ereignisse.

```csharp
var proxy = ActorProxy.Create<IGameActor>(
                    new ActorId(Guid.Parse(arg)), ApplicationName);
proxy.SubscribeAsync(new GameEventsHandler()).Wait();
```

Im Failover-Fall kann ein Failover des Actors zu einem anderen Prozess oder Knoten erfolgen. Der Actor-Proxy verwaltet die aktiven Abonnements und verlängert automatisch deren Abonnement. Sie können das Intervall für die Abonnementverlängerung über die API `ActorProxyEventExtensions.SubscribeAsync<TEvent>` steuern. Zum Kündigen des Abonnements verwenden Sie die API `ActorProxyEventExtensions.UnsubscribeAsync<TEvent>`.

Veröffentlichen Sie auf dem Actor die Ereignisse einfach in der Reihenfolge ihres Auftretens. Wenn Abonnenten vorhanden sind, die das Ereignis abonniert haben, sendet die Actors-Laufzeit ihnen die Benachrichtigung.

```csharp
var ev = GetEvent<IGameEvents>();
ev.GameScoreUpdated(Id.GetGuidId(), State.Status.Score);
```

<!---HONumber=Nov15_HO4-->