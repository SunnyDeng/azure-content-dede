<properties
   pageTitle="Entwurfsmuster in Azure Service Fabric Actors für verteilte Netzwerke und Diagramme"
   description="Entwurfsmuster für die Verwendung von Service Fabric Actors zum Modellieren der Anwendung als verteilte Netzwerke und Diagramme"
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
   ms.date="03/17/2015"
   ms.author="claudioc"/>

# Entwurfsmuster in Service Fabric Actors: Verteilte Netzwerke und Diagramme
Azure Fabric Service Actors ist ideal geeignet für die Modellierung von komplexen Lösungen mit Beziehungen und für die Modellierung dieser Beziehungen als Objekte.

![][1]

Wie das Diagramm veranschaulicht, ist es einfach, einen Benutzer als Actor-Instanz (Knoten im Netzwerk) zu modellieren. Zum Beispiel können Benutzer über den "Freunde-Feed" (manchmal auch als "Follower-Problem" bezeichnet) Statusaktualisierungen von Personen anzeigen, mit denen sie verbunden sind, ähnlich wie bei Facebook und Twitter. Das Actor-Modell bietet die Flexibilität, um das Materialisierungsproblem anzugehen. Es ist möglich, den Freunde-Feed jederzeit zu befüllen und den Freunde-Feed aller meiner Freunde zu dem Zeitpunkt zu aktualisieren, zu dem ein Update bereitgestellt wird, wie unten dargestellt:

![][2]


## Code-Beispiel für intelligenten Cache – Freunde-Feed sozialer Netzwerke (Ereigniszeit)

Beispielcode zum Befüllen des Freunde-Feeds:

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

public class SocialPerson : Actor, ISocialPerson
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

Alternativ können wir unsere Actors für die Erweiterung und Kompilierung des Freunde-Feeds auf dem Abfrage-Timer modellieren, mit anderen Worten, wann der Benutzer seinen Freunde-Feed anfordert. Eine weitere Methode, die verwendet werden kann, ist die Materialisierung des Freunde-Feeds auf einem Timer, z. B. alle 5 Minuten. Oder wir können das Modell optimieren, und Ereigniszeit- sowie Abfragezeitverarbeitung mit einem Timer-basierten Modell kombinieren, das auf Benutzergewohnheiten basiert, z. B. wie häufig eine Anmeldung erfolgt oder ein Update gepostet wird. Beim Modellieren eines Actors in einem sozialen Netzwerk sollten auch "Superuser", Benutzer mit Millionen von Followern, berücksichtigt werden. Entwickler sollten Status und Verhalten solcher Benutzer unterschiedlich modellieren, um den Anforderungen zu entsprechen. Analog gilt, wenn eine Aktivität modelliert werden soll, die viele Benutzer-Actors mit einem einzelnen Aktivitäten-Actor (Hub and Spoke) verbindet, so kann dies ebenfalls durchgeführt werden. Gruppenchat- oder Spiele-Hosting-Szenarien sind zwei Beispiele. Nehmen wir das Gruppenchat-Beispiel. Eine Reihe von Teilnehmern erstellt einen Gruppenchat-Actor, der Nachrichten von einem Teilnehmer an die Gruppe verteilen kann, siehe das folgende Beispiel:

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

public class GroupChatParticipant : Actor<GroupChatParticipantState>, IGroupParticipant
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


public class GroupChat : Actor<GroupChatState>, IGroupChat
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

Eigentlich wird lediglich die Fähigkeit von Reliable Actors genutzt, jeden anderen Actor im Cluster über die ID zu adressieren und mit ihm zu kommunizieren, ohne sich um Platzierung, Adressierung, Zwischenspeichern, Messaging, Serialisierung oder Routing Gedanken machen zu müssen.

## Nächste Schritte
[Muster: Intelligenter Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Muster: Ressourcenkontrolle](service-fabric-reliable-actors-pattern-resource-governance.md)

[Muster: Komposition zustandsbehafteter Dienste](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Muster: Internet der Dinge](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Muster: Verteilte Berechnung](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Beispiele für Antimuster](service-fabric-reliable-actors-anti-patterns.md)

[Einführung in Service Fabric Actors](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-distributed-networks-and-graphs/distributedNetworks_arch2.png
 

<!---HONumber=July15_HO2-->