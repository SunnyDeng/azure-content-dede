<properties
   pageTitle="Entwurfsmuster für intelligenten Cache | Microsoft Azure"
   description="Entwerfen Sie Muster zum Verwenden des Service Fabric-Programmiermodells „Reliable Actors“ zum Erstellen einer Cache-Infrastruktur für webbasierte Anwendungen."
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
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Reliable Actors-Entwurfsmuster: Intelligenter Cache

Die Kombination von Webebene, Cachingebene, Speicherebene und gelegentlich auch Workerebene sind im Großen und Ganzen die standardmäßigen Teile heutiger Anwendungen. Die Cachingebene ist in der Regel von entscheidender Bedeutung für die Leistung und kann wiederum selbst mehrere Ebenen umfassen. Viele Caches sind einfache Schlüssel-Wert-Paare. Andere Systeme, wie z. B. [Redis](http://redis.io), dienen als Cache, bieten aber auch eine reichhaltigere Semantik. Jede spezielle Cachingebene besitzt jedoch eine begrenzte Semantik. Darüber hinaus ist sie noch eine weitere Ebene, die verwaltet werden muss.

Objekte können stattdessen nur den Status in lokalen Variablen speichern, und von diesen Objekten können automatisch Momentaufnahmen erstellt werden, oder sie können in einen permanenten Speicher verschoben werden. Außerdem können umfassende Auflistungen, wie z. B. Listen, sortierte Sätze, Warteschlangen und andere benutzerdefinierte Typen, einfach als Membervariablen und Methoden modelliert werden.

![Actors und Caching][1]

## Untersuchen des Leaderboard-Beispiels

Sehen wir uns Leaderboards als Beispiel an. Ein Leaderboard-Objekt muss eine sortierte Spielerliste und die dazugehörigen Punkte verwalten, damit Abfragen durchgeführt werden können. Eine Abfrage kann die ersten 100 Spieler finden. Sie kann auch die Position eines Spielers im Leaderboard relativ zu einer angegebenen Anzahl von Spielern über und unter ihm finden. Bei einer herkömmlichen Lösung müsste das Leaderboard-Objekt (eine Auflistung, die das Einfügen eines neuen Tupels `<Player, Points>` namens **Score** unterstützt) mit GET abgerufen, sortiert und schließlich mit PUT wieder an den Cache übermittelt werden. Wahrscheinlich würden wir das Leaderboard-Objekt aus Gründen der Konsistenz mit LOCK (GETLOCK PUTLOCK) sperren. Nehmen wir nun eine Actor-basierte Lösung, bei der Status und Verhalten nicht getrennt sind. Es gibt zwei Optionen:

* Implementieren der Leaderboard-Auflistung als Teil des Actors.
* Verwenden des Actors als Schnittstelle für die Auflistung, die in einer Membervariablen gespeichert werden kann.

Das folgende Codebeispiel zeigt, wie die Schnittstelle aussehen könnte.

### Codebeispiel für intelligenten Cache: Leaderboard-Schnittstelle

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

Als Nächstes können wir diese Schnittstelle implementieren, indem wir die zweite Option verwenden und das Verhalten der Auflistung in den Actor kapseln:

### Codebeispiel für intelligenten Cache: Leaderboard-Actor

```
public class Leaderboard : StatefulActor<LeaderboardCollection>, ILeaderboard
{
    // Specialised collection, could be part of the actor

    public Task UpdateLeaderboard(Score score)
    {
        State.UpdateLeaderboard(score);
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

Der Statusmember der Klasse stellt den Status des Actors bereit. Im oben dargestellten Beispielcode stellt er auch Methoden zum Lesen und Schreiben von Daten bereit.

### Codebeispiel für intelligenten Cache: LeaderboardCollection

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

Bei diesem Ansatz gibt es keine Sperren und keinen Datenversand. Es werden nur Remoteobjekte in einer verteilten Laufzeit bearbeitet, die mehrere Clients bedient, als wären sie einzelne Objekte in einer einzelnen Anwendung, die nur einen einzelnen Client bedient. Das folgende Codebeispiel konzentriert sich auf den Beispielclient.

### Codebeispiel für intelligenten Cache: Aufrufen des Leaderboard-Actors

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

## Skalieren der Architektur
Es sieht möglicherweise so aus, als könnte das obige Beispiel zu einem Engpass in der Leaderboard-Instanz führen. Was geschieht beispielsweise, wenn Sie beabsichtigen, Tausende von Spielern zu unterstützen? Eine Möglichkeit wäre in diesem Fall, statusfreie Aggregatoren einzuführen, die als Puffer fungieren. Diese Aggregatoren würden partielle Ergebnisse (Teilergebnisse) speichern und sie dann in regelmäßigen Abständen an den Leaderboard-Actor senden, der das endgültige Leaderboard verwalten würde. Dieses Verfahren wird zu einem späteren Zeitpunkt noch ausführlicher erörtert. Es müssen auch nicht Mutexe, Semaphoren oder andere Parallelitätskonstrukte berücksichtigt werden, wie dies traditionell bei sich korrekt verhaltenden parallelen Programmen erforderlich ist.

Es folgt ein weiteres Cache-Beispiel, das die umfangreiche Semantik veranschaulicht, die mit Actors implementiert werden kann. Dieses Mal implementieren wir die Logik der Prioritätswarteschlange (je niedriger die Nummer, desto höher die Priorität) als Teil der Actor-Implementierung. Das folgende Codebeispiel bietet einen Überblick über die Schnittstelle für **IJobQueue**.

### Codebeispiel für intelligenten Cache: Auftragswarteschlangen-Schnittstelle

```
public interface IJobQueue : IActor
{
    Task Enqueue(Job item);
    Task<Job> Dequeue();
    Task<Job> Peek();
    Task<int> GetCount();
}
```

Wir müssen auch das **Job**-Element definieren:

### Codebeispiel für intelligenten Cache: Auftrag

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

Zuletzt implementieren wir die IJobQueue-Schnittstelle im Actor. Beachten Sie, dass wir die Implementierungsdetails der Prioritätswarteschlange hier aus Gründen der Übersichtlichkeit weggelassen haben. Eine Implementierung finden Sie in den begleitenden Beispielen.

### Codebeispiel für intelligenten Cache: Auftragswarteschlange

```
public class JobQueue : StatefulActor<List<Jobs>>, IJobQueue
{

    public override Task OnActivateAsync()
    {
        State = new List<Job>();
    }

    public Task Enqueue(Job item)
    {
        // this is where we add to the queue

        ...

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

## Verwenden von Actors zum Bereitstellen von Flexibilität
In den obigen Beispielen zu Leaderboard und Auftragswarteschlange wurden zwei verschiedene Verfahren verwendet:

* Im Leaderboard-Beispiel haben wir ein Leaderboard-Objekt als eine private Membervariable in den Actor gekapselt. Wir haben dann lediglich eine Schnittstelle für dieses Objekt bereitgestellt, sowohl zu dessen Status als auch zu dessen Funktionalität.

* Im Beispiel der Auftragswarteschlange haben wir stattdessen den Actor als Prioritätswarteschlange selbst implementiert, anstatt auf ein an anderer Stelle definiertes Objekt zu verweisen.

Actors bieten Entwicklern die Flexibilität, umfangreiche Objektstrukturen als Teil der Actors zu definieren oder auf Objektdiagramme außerhalb der Actors zu verweisen. In Cachingterminologie ausgedrückt, können Actors Write-Behind oder Write-Through verwenden, oder sie können unterschiedliche Techniken für die Granularität von Membervariablen einsetzen. Sie besitzen die vollständige Kontrolle darüber, was persistent beibehalten werden soll und wann es beibehalten werden soll. Ein transienter Status oder ein Status, der anhand eines gespeicherten Status erstellt werden kann, muss nicht beibehalten werden.

Wie werden die Caches von diese Actors aufgefüllt? Es gibt eine Reihe von Möglichkeiten, dies zu erreichen. Actors informieren Benutzer mithilfe der virtuellen Methoden **OnActivateAsync()** und **OnDeactivateAsync()**, wenn eine Instanz des Actors aktiviert oder deaktiviert wird. Beachten Sie, dass der Actor bei Bedarf aktiviert wird, wenn erstmals eine Anforderung an ihn gesendet wird.

Sie können „OnActivateAsync()“ verwenden, um den Status bei Bedarf wie beim Read-Through, vielleicht aus einem externen stabilen Speicher, aufzufüllen. Der Status kann auch nach einem Timer aufgefüllt werden, z. B. mit einem Wechselkurs-Actor, der die Konvertierungsfunktion basierend auf den aktuellen Wechselkursen bereitstellt. Dieser Actor kann seinen Status in regelmäßigen Abständen über einen externen Dienst auffüllen, z. B. alle fünf Sekunden, und den Status für die Konvertierungsfunktion verwenden. Im folgenden Codebeispiel wird gezeigt, wie dies funktioniert.

### Codebeispiel für intelligenten Cache: Ratenkonvertierung

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

}

public Task RefreshRates()
{
    // this is where we will make an external call and populate rates
}

```

Im Wesentlichen bietet der Ansatz des intelligenten Caches folgende Vorteile:

* Hoher Durchsatz/niedrige Latenz bei Dienstanforderungen aus dem Arbeitsspeicher.
* Einzelinstanz-Routing und Singlethread-Serialisierung von Anforderungen an ein Element ohne Konflikt in einem persistenten Speicher.
* Semantische Vorgänge, wie z. B. **Enqueue(Auftragselement)**.
* Einfach zu implementierendes Write-Through oder Write-Behind.
* Automatisches Entfernen von LRU-Elementen (Last Recently Used) (Ressourcenverwaltung).
* Automatische Elastizität und Zuverlässigkeit.


## Nächste Schritte

[Muster: Verteilte Netzwerke und Diagramme](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Muster: Ressourcengovernance](service-fabric-reliable-actors-pattern-resource-governance.md)

[Muster: Zusammenstellung statusbehafteter Dienste](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Muster: Internet der Dinge](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Muster: Verteilte Berechnung](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Einige Antimuster](service-fabric-reliable-actors-anti-patterns.md)

[Einführung in Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-smart-cache/smartcache-arch.png

<!---HONumber=AcomDC_0121_2016-->