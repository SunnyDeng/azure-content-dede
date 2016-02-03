<properties
   pageTitle="Verteilte Netzwerke und Diagrammmuster | Microsoft Azure"
   description="Entwurfsmuster für die Verwendung von Service Fabric Reliable Actors zum Modellieren der Anwendung als verteilte Netzwerke und Diagramme."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/29/2015"
   ms.author="claudioc"/>

# Reliable Actors-Entwurfsmuster: Verteilte Netzwerke und Diagramme
Das Azure Service Fabric Reliable Actors-Programmiermodell ist ideal geeignet für die Modellierung komplexer Lösungen mit Beziehungen und für die Modellierung dieser Beziehungen als Objekte.

![Modellierung von Azure Service Fabric Reliable Actors][1]

Wie das obige Diagramm veranschaulicht, ist es einfach, einen Benutzer als Actor-Instanz (Knoten im Netzwerk) zu modellieren. Zum Beispiel können Benutzer über den „Freunde-Feed“ (manchmal auch als „Follower-Problem“ bezeichnet) Statusaktualisierungen von Personen anzeigen, mit denen sie verbunden sind, ähnlich wie bei Facebook und Twitter.

Das Reliable Actors-Modell bietet einen flexiblen Ansatz für das Materialisierungsproblem. Es ist möglich, den Freunde-Feed jederzeit aufzufüllen und den Freunde-Feed aller Freunde zu dem Zeitpunkt zu aktualisieren, zu dem ein Update bereitgestellt wird, wie unten dargestellt:

![Das Reliable Actors-Modell und Auffüllen des Freunde-Feeds][2]


## Codebeispiel für intelligenten Cache – Freunde-Feed in sozialen Netzwerken (Ereigniszeit)

Beispielcode zum Auffüllen des Freunde-Feeds:

```csharp
public interface ISocialPerson : IActor
{
    Task AddFriend(long person);
    Task RemoveFriend(long person);
    Task<List<SocialStatus>> GetFriendsFeed();
    Task<SocialStatus> GetStatus();
    Task<List<SocialStatus>> GetMyFeed();
    Task UpdateStatus(string status);
    Task UpdateFriendFeedAsync(SocialStatus status);
}

[DataContract]
Public class SocialPersonState
{
    [DataMember]
    public string _name; // my name
    [DataMember]
    public List<long> _friends; // list of my friends' IDs
    [DataMember]
    public List<SocialStatus> _friendsFeed; // my friends feeds
    [DataMember]
    public List<SocialStatus> _myFeed; // this is my feed, all my status updates
    [DataMember]
    public SocialStatus _lastStatus; // this is my last update
}

public class SocialPerson : StatefulActor<SocialPersonState>, ISocialPerson
{
    public override Task ActivateAsync()
    {
        CreateOrRestoreState();
        return base.ActivateAsync();
    }

    public Task AddFriend(long person)
    {
        State._friends.Add(person);
        return TaskDone.Done;
    }

    public Task RemoveFriend(long person)
    {
        State._friends.Remove(person);
        return TaskDone.Done;
    }

    public Task<List<SocialStatus>> GetFriendsFeed()
    {
        return Task.FromResult(State._friendsFeed);
    }

    public Task UpdateStatus(string status)
    {
        State._lastStatus = new SocialStatus()
        {
            Name = _name,
            Status = status,
            Timestamp = DateTime.UtcNow
        };
        State._myFeed.Add(_lastStatus);

        var taskList = new List<Task>();

        foreach(var friendId in _friends)
        {
            var friend = ActorProxy.Create<ISocialPerson>(friendId);
            taskList.Add(friend.UpdateFriendFeedAsync(_lastStatus));
        }

        return Task.WhenAll(taskList);
    }

    public Task UpdateFriendFeed(SocialStatus status)
    {
        State._friendsFeed.Add(status);

        return TaskDone.Done;
    }

    public Task<SocialStatus> GetStatus()
    {
        return Task.FromResult(State._lastStatus);
    }

    public Task<List<SocialStatus>> GetMyFeed()
    {
        return Task.FromResult(State._myFeed);
    }
}
```

Alternativ können Sie Actors für die Verbreitung und Kompilierung des Freunde-Feeds auf dem Abfragetimer modellieren, wenn der Benutzer den Freunde-Feed anfordert. Sie können den Freunde-Feed auch mit einem Timer (z. B. alle fünf Minuten) materialisieren. Sie können das Modell jedoch auch optimieren und die Verarbeitung sowohl zur Ereigniszeit als auch zur Abfragezeit mit einem Timer-basierten Modell kombinieren, das auf Benutzergewohnheiten basiert, z. B. wie häufig eine Anmeldung erfolgt oder ein Update gepostet wird.

Beim Modellieren eines Actors in einem sozialen Netzwerk sollten auch „Superuser“, also Benutzer mit Millionen von Followern, berücksichtigt werden. Entwickler sollten Status und Verhalten solcher Benutzer anders modellieren, um den Anforderungen zu entsprechen.

Analog gilt, wenn eine Aktivität modelliert werden soll, die viele Benutzer-Actors mit einem einzelnen Aktivitäten-Actor (Hub and Spoke) verbindet, so ist dies ebenfalls möglich. Gruppenchat- und Gamehosting sind zwei Beispiele. Sehen wir uns das Beispiel des Gruppenchats an. Eine Reihe von Teilnehmern erstellt einen Gruppenchat-Actor, der Nachrichten von einem Teilnehmer an die Gruppe verteilen kann. Dies wird im unten gezeigten Beispiel dargestellt:

## Codebeispiel für intelligenten Cache – Gruppenchat

```csharp
public interface IGroupChat : IActor
{
    Task PublishMessageAsync(long participantId, string message);
    Task<List<GroupChatMessage>> GetMessagesAsync();
    Task AddParticipantAsync(long participantId);
    Task RemoveParticipantAsync(long partitipantId);
}

[DataContract]
public class GroupChatParticipantState
{
    [DataMember]
    Public long _groupChatId;
    [DataMember]
    public List<GroupChatMessage> _messages;
}

public class GroupChatParticipant : StatefulActor<GroupChatParticipantState>, IGroupParticipant
{
    public Task SendMessageAsync(string message)
    {
        if (State._groupChatId != -1)
        {
            var groupChat = ActorProxy.Create<IGroupChat>(State._groupChatId);
            return groupChat.PublishMessageAsync(this.Id, message);
        }

        return TaskDone.Done;
    }

    ...
}

[DataContract]
public class GroupChatState
{
    [DataMember]
    Public List<long> _participants;
    [DataMember]
    Public List<GroupChatMessage> _messages;
}


public class GroupChat : StatefulActor<GroupChatState>, IGroupChat
{

public Task PublishMessageAsync(long participantId, string message)
{
    var chatMessage = new GroupChatMessage()
    {
        ParticipantId = participantId,
        Message = message,
        Timestamp = DateTime.Now
    };

    State._messages.Add(chatMessage);

    var taskList = new List<Task>();

    foreach(var id in State._participants)
    {
        if (id != participantId)
        {
            var participant = ActorProxy.Create<IGroupParticipant>.Create(id);
            taskList.Add(participant.ReceiveMessageAsync(chatMessage));
        }
    }
    return Task.WhenAll(taskList);
}

...

}
```

Dieser Ansatz nutzt die Fähigkeit des Reliable Actors-Modells, jedem Actor das Ansprechen jedes anderen Actors im Cluster nach ID zu erlauben. Sie können kommunizieren, ohne sich über Ort, Adressierung, Zwischenspeichern, Messaging, Serialisierung oder Routing Gedanken zu machen.

## Nächste Schritte
[Muster: Intelligenter Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Muster: Ressourcengovernance](service-fabric-reliable-actors-pattern-resource-governance.md)

[Muster: Zusammenstellung statusbehafteter Dienste](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Muster: Internet der Dinge](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Muster: Verteilte Berechnung](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Einige Antimuster](service-fabric-reliable-actors-anti-patterns.md)

[Einführung in Service Fabric Actors](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch2.png

<!---HONumber=AcomDC_0121_2016-->