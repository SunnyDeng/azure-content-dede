<properties
   pageTitle="Reliable Actors: Entwurfsmuster für intelligenten Cache"
   description="Entwurfsmuster für die Verwendung von Reliable Actors als Caching-Infrastruktur in webbasierten Anwendungen"
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
   ms.date="08/05/2015"
   ms.author="claudioc"/>


# Reliable Actors-Entwurfsmuster: Intelligenter Cache
Die Kombination von Webebene, Caching-Ebene, Speicherebene und gelegentlich auch Worker-Ebene sind im Großen und Ganzen die standardmäßigen Teile heutiger Anwendungen. Die Caching-Ebene ist in der Regel von entscheidender Bedeutung für die Leistung und kann wiederum selbst mehrere Ebenen umfassen. Viele Caches sind einfache Schlüsselwertpaare, während andere Systeme wie [Redis](http://redis.io), die als Cache verwendet werden, eine reichhaltigere Semantik bieten. Dennoch ist jede spezielle Caching-Ebene semantisch beschränkt, und noch wichtiger, es ist eine weitere Ebene, die verwaltet werden muss. Was, wenn anstatt Objekten nur der Status in lokalen Variablen gespeichert wird, und von diesen Objekten automatisch Snapshots erstellt oder sie in einem permanenten Speicher verschoben werden können? Außerdem werden umfassende Auflistungen, wie z. B. Listen, sortierte Sätze, Warteschlangen sowie auch jeder andere benutzerdefinierte Typ, einfach als Membervariablen und Methoden modelliert.

![][1]

## Das Leaderboard-Beispiel
Nehmen Sie Leaderboards als Beispiel – ein Leaderboard-Objekt muss eine sortierte Spielerliste und die dazugehörigen Punkte verwalten, damit Abfragen durchgeführt werden können. Um beispielsweise die "100 besten Spielern" zu suchen, oder um die Position eines Spielers relativ zu +-N-Spielern vor oder nach ihm im Leaderboard zu ermitteln. Bei einer typischen Lösung mit herkömmlichen Tools müsste das Leaderboard-Objekt (Auflistung, die das Einfügen eines neuen Tuples<Player  Points> namens "Score" unterstützt) mit GET abgerufen, sortiert und schließlich mit PUT wieder an den Cache übermittelt werden. Wahrscheinlich würden wir das Leaderboard-Objekt aus Gründen der Konsistenz mit LOCK (GETLOCK PUTLOCK) sperren. Nehmen wir nun eine Actor-basierte Lösung, bei der Status und Verhalten nicht getrennt ist. Es gibt zwei Optionen:

* Implementieren der Leaderboard-Auflistung als Teil des Actors
* oder Verwenden des Actors als Schnittstelle für die Auflistung, die in einer Membervariablen gespeichert werden kann. Schauen wir zunächst, wie die Schnittstelle aussehen könnte:

## Codebeispiel für intelligenten Cache – Leaderboard-Schnittstelle

```
public interface ILeaderboard : IActor
{
    // Updates the leaderboard with the score - player, points
    Task UpdateLeaderboard(Score score);

    // Returns the Top [count] from the leaderboard e.g., Top 10
    Task<List<Score>> GetLeaderboard(int count);

    // Returns the specific position of the player relative to other players
    Task<List<Score>> GetPosition(long player, int range);
}

```

Als Nächstes implementieren wir diese Schnittstelle. Dann verwenden wir die zweite Option und kapseln das Verhalten der Auflistung in den Actor ein:

## Codebeispiel für intelligenten Cache – Leaderboard-Actor

```
public class Leaderboard : Actor<LeaderboardCollection>, ILeaderboard
{
    // Specialised collection, could be part of the actor

    public Task UpdateLeaderboard(Score score)
    {
        State.UpdateLeaderboard(score);
        return TaskDone.Done;
    }

    public Task<List<Score>> GetLeaderboard(int count)
    {
        // Return top N from Leaderboard
        return Task.FromResult(State.GetLeaderboard(count));
    }

    public Task<List<Score>> GetPosition(long player, int range)
    {
        // Return player position and other players in range from Leaderboard
        return Task.FromResult(State.FindPosition(player, range));
    }
}

```

Der Status-Member der Klasse stellt den Status des Actors bereit, im obigen Beispielcode stellt er außerdem Methoden zum Lesen/Schreiben von Daten bereit.

## Codebeispiel für intelligenten Cache – LeaderboardCollection

```
[DataContract]
public class LeaderboardCollection
{
    // Specialised collection, could be part of the actor
    [DataMember]
    Private List<score> _leaderboard = new List<score>();

    public void UpdateLeaderboard(Score score)
    {
        _leaderboard.add(score);
    }

    public List<Score> GetLeaderboard(int count)
    {
        …
    }

    public List<Score> GetPosition(long player, int range)
    {
        …
    }
}

```

Kein Datenversand, keine Sperren, nur die Bearbeitung von Remote-Objekten in einer verteilten Laufzeit, die mehrere Clients bedienen, als wären sie einzelne Objekte in einer einzelnen Anwendung, die nur einen einzelnen Client bedient. Hier der Beispiel-Client:

## Codebeispiel für intelligenten Cache – Aufrufen das Leaderboard-Actors

```
// Get reference to Leaderboard
const string appName = "fabric:/FunnyGameApp";
var leaderboard = ActorProxy.Create<ILeaderboard>(1001, appName);

// Update Leaderboard with dummy players and scores
await leaderboard.UpdateLeaderboard(new Score() { Player = 1, Points = 500 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 2, Points = 100 });
await leaderboard.UpdateLeaderboard(new Score() { Player = 3, Points = 1500 });

// Finally, Get the Leaderboard. 0 represents ALL, any other number > 0 represents TOP N
var result = await leaderboard.GetLeaderboard(0);
```

Die Ausgabe sieht wie folgt aus:

```
Player = 3 Points = 1500
Player = 1 Points = 500
Player = 2 Points = 100
```

## Skalierung der Architektur
Es sieht möglicherweise so aus, als könnte im obigen Beispiel ein Engpass in der Leaderboard-Instanz erstellt werden. Was, wenn wir beispielsweise planen, Hunderte und Tausende von Spielern zu unterstützen? Eine Möglichkeit dazu wäre, statusfreie Aggregatoren einzuführen, die wie ein Puffer fungieren – also Teilergebnisse (d. h. Untersummen) speichern und diese dann in regelmäßigen Abständen an den Leaderboard-Actor senden, der das endgültige Leaderboard verwaltet. Dieses "Aggregationsverfahren" wird zu einem späteren Zeitpunkt noch ausführlicher erörtert. Es müssen auch nicht Mutexe, Semaphoren oder andere Parallelitätskonstrukte berücksichtigt werden, wie dies traditionell bei sich korrekt verhaltenden parallelen Programmen erforderlich ist. Es folgt ein weiteres Cache-Beispiel, das die umfangreiche Semantik veranschaulicht, die mit Actors implementiert werden kann. Dieses Mal implementieren wir die Logik der Prioritätswarteschlange (Reduzierung der Anzahl, Erhöhung der Priorität) als Teil der Actor-Implementierung. Die Schnittstelle für IJobQueue sieht wie folgt aus:

## Codebeispiel für intelligenten Cache – Auftragswarteschlangen-Schnittstelle

```
public interface IJobQueue : IActor
{
    Task Enqueue(Job item);
    Task<Job> Dequeue();
    Task<Job> Peek();
    Task<int> GetCount();
}
```

Wir müssen auch das Auftragselement definieren:

## Codebeispiel für intelligenten Cache – Auftrag

```
public class Job : IComparable<Job>
{
    public double Priority { get; set; }
    public string Name { get; set; }

    public override string ToString()
    {
        return string.Format("Job = {0} Priority = {1}", Name, Priority);
    }

    public int CompareTo(Job other)
    {
        return Priority.CompareTo(other.Priority);
    }
}
```

Zuletzt implementieren wir die IJobQueue-Schnittstelle auf Grain-Ebene. Beachten Sie, dass wir die Implementierungsdetails der Prioritätswarteschlange hier aus Gründen der Übersichtlichkeit weggelassen haben. Eine Beispielimplementierung finden Sie in den begleitenden Beispielen.

## Codebeispiel für intelligenten Cache – Auftragswarteschlange

```
public class JobQueue : Actor<List<Jobs>>, IJobQueue
{

    public override Task OnActivateAsync()
    {
        State = new List<Job>();
    }

    public Task Enqueue(Job item)
    {
        // this is where we add to the queue

        ...

        return TaskDone.Done;
    }

    public Task<Job> Dequeue()
    {
        // this is where we remove from the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<Job> Peek()
    {
        // this is where we peek at the head of the queue

        ...

        return Task.FromResult(frontItem);
    }

    public Task<int> GetCount()
    {

        // this is where we return the number of items in the queue

        return Task.FromResult(data.Count);
    }
}

```

Die Ausgabe sieht wie folgt aus:

```
Job = 2 Priority = 0.0323341116459733
Job = 3 Priority = 0.125596747792138
Job = 4 Priority = 0.208425460480352
Job = 0 Priority = 0.304352047063574
Job = 8 Priority = 0.415597594070992
Job = 7 Priority = 0.477669881413537
Job = 5 Priority = 0.525898784178262
Job = 9 Priority = 0.921959541701693
Job = 6 Priority = 0.962653734238191
Job = 1 Priority = 0.97444181375878
```

## Actors bieten Flexibilität
In den obigen Beispielen, Leaderboard und JobQueue, wurden zwei verschiedene Verfahren verwendet:

* Im Leaderboard-Beispiel haben wir ein Leaderboard-Objekt als private Membervariable in den Actor eingekapselt und nur eine einzelne Schnittstelle für dieses Objekt bereitgestellt – für den Status und die Funktionalität.

* Andererseits haben wir im JobQueue-Beispiel den Actor als Prioritätswarteschlange selbst implementiert, anstatt auf ein an anderer Stelle definiertes Element zu verweisen.

Actors bieten Entwicklern die Flexibilität, umfangreiche Objektstrukturen als Teil der Actors zu definieren oder auf Objektdiagramme außerhalb der Actors zu verweisen. In Caching-Begriffen ausgedrückt, können Actors Write-behind- oder Write-through verwenden, oder wir können unterschiedliche Techniken auf Membervariablen-Granularität verwenden. Anders ausgedrückt: Wir besitzen die vollständige Kontrolle darüber, was persistent beibehalten werden soll und wann es beibehalten werden soll. Ein transienter Status oder ein Status, der anhand eines gespeicherten Status erstellt werden kann, muss nicht beibehalten werden. Und wie sieht es dann mit dem Auffüllen der Caches dieser Actors aus? Es gibt eine Reihe von Möglichkeiten, dies zu erreichen. Actors bieten virtuelle Methoden, OnActivateAsync() und OnDectivateAsync(), die aufgerufen werden, um uns darüber zu informieren, wann eine Instanz des Actors aktiviert oder deaktiviert wird. Beachten Sie, dass der Actor bei Bedarf aktiviert wird, wenn eine erste Anforderung an ihn gesendet wird. Wir können OnActivateAsync() verwenden, um den Status bei Bedarf wie beim Read-through, vielleicht aus einem externen stabilen Speicher, auszufüllen. Oder der Status kann auf einem Timer ausgefüllt werden, z. B. einem Wechselkurs-Actor, der die Konvertierungsfunktion basierend auf den aktuellen Wechselkursen bereitstellt. Dieser Actor kann seinen Status in regelmäßigen Abständen über einen externen Dienst ausfüllen, z. B. alle 5 Sekunden, und den Status für die Konvertierungsfunktion verwenden. Betrachten Sie das folgende Beispiel:

## Codebeispiel für intelligenten Cache – Ratenkonvertierung

```
...

private List<ExchangeRate> _rates;
private IActorTimer _timer;

public Task Activate()
{
    // registering a timer that will live as long as the actor...
    _timer = this.RegisterTimer((obj) =>
    {
        Console.WriteLine("Refreshing rates...");
        return this.RefreshRates(); // call to external service/source to retrieve exchange rates
    },
    null,
    TimeSpan.FromSeconds(0), // start immediately
    TimeSpan.FromSeconds(5)); // refresh every 5 seconds

    return TaskDone.Done;
}

public Task RefreshRates()
{
    // this is where we will make an external call and populate rates
    return TaskDone.Done;
}

```

Im Wesentlichen bietet der Smart Cache Folgendes:

* Hoher Durchsatz/niedrige Latenz bei Dienstanforderungen aus dem Arbeitsspeicher.
* Einzelinstanz-Routing und Singlethread-Serialisierung von Anforderungen an ein Element ohne Konflikt im persistenten Speicher.
* Semantische Vorgänge, wie z. B. Enqueue(Auftragselement).
* Einfach zu implementierende Write-through- oder Write-Behind-Technik.
* Automatische Entfernung von LRU (Last Recently Used)-Elementen (Ressourcenverwaltung).
* Automatische Elastizität und Zuverlässigkeit.


## Nächste Schritte
[Muster: Verteilte Netzwerke und Diagramme](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Muster: Ressourcenkontrolle](service-fabric-reliable-actors-pattern-resource-governance.md)

[Muster: Komposition zustandsbehafteter Dienste](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Muster: Internet der Dinge](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Muster: Verteilte Berechnung](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Beispiele für Antimuster](service-fabric-reliable-actors-anti-patterns.md)

[Einführung in Service Fabric Actors](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-smart-cache/smartcache-arch.png

<!---HONumber=August15_HO6-->