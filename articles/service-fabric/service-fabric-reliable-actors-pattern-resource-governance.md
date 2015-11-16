<properties
   pageTitle="Entwurfsmuster für Ressourcenkontrolle | Microsoft Azure"
   description="Entwurfsmuster zur Verwendung von Service Fabric Reliable Actors zum Modellieren von Anwendungsanforderungen, die zentral hochskaliert werden sollen, jedoch beschränkte Ressourcen verwenden."
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
   ms.date="08/11/2015"
   ms.author="vturecek"/>

# Reliable Actors-Entwurfsmuster: Ressourcenkontrolle

Dieses Muster und verwandte Szenarien sind leicht erkennbar für Entwickler – in Unternehmen oder anderswo – die über beschränkte Ressourcen auf lokaler Ebene oder in der Cloud verfügen, die sie nicht sofort skalieren können, oder die große Anwendungen und Daten in die Cloud senden möchten.

In Unternehmen werden diese beschränkten Ressourcen, wie z. B. Datenbanken, auf hochskalierbarer Hardware ausgeführt. Wer über eine lange Unternehmenserfahrung verfügt, weiß, dass dies eine gängige Situation in Unternehmen ist. Selbst auf Cloud-Ebene kann diese Situation auftreten, wenn ein Cloud-Dienst versucht, das TCP-Verbindungslimit von 64 K zwischen einem Adresse-Port-Tupel zu cloundbasiert oder eine Verbindung zu einer Cloud-basierten Datenbank herzustellen, die die Anzahl gleichzeitiger Verbindungen beschränkt.

In der Vergangenheit wurde dies in der Regel mithilfe von Drosselung durch nachrichtenbasierte Middleware oder kundenspezifische Pooling- und Fassadenmechanismen gelöst. Diese sind schwer zu realisieren, insbesondere wenn die mittlere Ebene skaliert werden soll, dabei jedoch die korrekte Verbindungsanzahl beibehalten werden soll. Es ist einfach anfällig und komplex.

Wie das Muster des intelligenten Cache erstreckt sich auch dieses Muster über mehrere Szenarien und Kunden, die bereits über funktionierende Systeme mit beschränkten Ressourcen verfügen. Sie bauen Systeme auf, bei denen nicht lediglich die Dienste horizontal hochskaliert werden müssen, sondern auch deren Status im Arbeitsspeicher und der persistente Status im stabilen Speicher.

Das folgende Diagramm zeigt dieses Szenario:

![][1]

## Modellieren von Cache-Szenarien mit Actors

Im Wesentlichen wird der Zugriff auf Ressourcen als ein oder mehrere Actors modelliert, die als Proxys (also beispielsweise Verbindung) für eine Ressource oder eine Gruppe von Ressourcen fungieren. Die Ressource kann dann entweder direkt über einzelne Actors oder über einen Koordinierung-Actor verwaltet werden, der wiederum die Ressourcen-Actors steuert. Künftig werden wir uns vor allem mit der häufig aus Leistungs- und Skalierbarkeitsgründen entstehenden Notwendigkeit befassen, mit einer partinierten (oder auch fragmentierten) Speicherebene zu arbeiten. Die erste Option ist ziemlich grundlegend: Es ist möglich, eine statische Funktion zum Zuordnen und Auflösen der Actors in nachgeschalteten Ressourcen zu verwenden. Eine solche Funktion kann z. B. eine Verbindungszeichenfolge mit gegebener Eingabe zurückgeben. Es steht uns vollkommen frei, wie diese Funktion implementiert wird. Natürlich hat auch dieser Ansatz seine Nachteile, wie z. B. statische Affinität, die die Neupartitionierung von Ressourcen oder die Neuzuordnung eines Actors sehr schwierig macht. Hier ist ein sehr einfaches Beispiel – wir wenden Modulo-Arithmetik an, um den Datenbanknamen mit der Benutzer-ID zu bestimmen, und verwenden die Region, um den Datenbankserver zu identifizieren.

## Codebeispiel für Ressourcenkontrolle – Statische Auflösung

```csharp
private static string _connectionString = "none";

private static string ResolveConnectionString(long userId, int region)
{
    if (_connectionString == "none")
    {
        // an example of static mapping
        _connectionString = String.Format("Server=SERVER_{0};Database=DB_{0}", region, userId % 4);
    }
    return _connectionString;
}
```

Einfach, jedoch nicht sehr flexibel. Betrachten wir nun einen erweiterten und hilfreichen Ansatz. Als erstes modellieren wir die Affinität zwischen physischen Ressourcen und Actors. Dies erfolgt durch einen Actor namens Resolver, der die Zuordnung zwischen Benutzern, logischen Partitionen und physischen Ressourcen erkennt. Resolver verwaltet seine Daten in einem persistenten Speicher, sie werden jedoch für eine schnelle Suche zwischengespeichert. Wie in dem Wechselkurs-Beispiel weiter oben im Muster des intelligenten Cache zu sehen war, kann Resolver die neuesten Informationen mithilfe eines Timers auf proaktive Weise abrufen. Wenn der Benutzer-Actor die Ressource, die er verwenden muss, auflöst, nimmt er ihre Zwischenspeicherung in einer lokalen Variablen mit dem Namen "\_resolution" vor und verwendet sie während seiner Lebensdauer. Einer suchbasierten Auflösung (unten dargestellt) wurde der Vorzug gegeben gegenüber einfachem Hashing oder Bereich-Hashing, und zwar aufgrund der Flexibilität, die sie bei Vorgängen wie Herunter-/Hochskalieren oder Verschieben eines Benutzers von einer Ressource in eine andere bietet.

![][2]

In der obigen Abbildung ist zu erkennen, dass Actor B23 zuerst seine Ressource – DB1 auflöst (aka Auflösung) und sie dann zwischenspeichert. Nachfolgende Vorgänge können nun die zwischengespeicherte Auflösung verwenden, um auf die beschränkte Ressource zuzugreifen. Da die Actors Singlethread-Ausführung unterstützen, müssen Entwickler sich keine Gedanken mehr über Parallelzugriff auf die Ressource machen. Die Benutzer- und Resolver-Actors sehen wie folgt aus:

## Codebeispiel für Ressourcenkontrolle – Resolver

```csharp
public interface IUser : IActor
{
    Task UpdateProfile(string name, string country, int age);
}

[DataContract]
public class UserState
{
    [DataMember]
    private long _userId;
    [DataMember]
    private string _name;
    [DataMember]
    private string _country;
    [DataMember]
    private int _age;
    [DataMember]
    private Resolution _resolution;
}


public class User : Actor<UserState>, IUser
{
    public override async Task ActivateAsync()
    {
        State._userId = this.GetPrimaryKeyLong();
        var resolver = ActorProxy.Create<IResolver>(0);
        State._resolution = await resolver.ResolveAsync(State._userId);
        await base.ActivateAsync();
    }

    public Task UpdateProfile(string name, string country, int age)
    {
        Console.WriteLine("Using {0}", State._resolution.Resource.ConnectionString);
        // this is where we use the resource...
        return TaskDone.Done;
    }
}
```

Ressourcenkontrolle – Resolver-Beispiel

```csharp
public interface IResolver : IActor
{
    Task<Resolution> ResolveAsync(long entity);
}

[DataContract]
public class ResolverState
{
    ...
}

public class Resolver : Actor<ResolverState>, IResolver
{
    ...

    public Task<Resolution> ResolveAsync(long entityKey)
    {
        if (State._resolutionCache.ContainsKey(entityKey))
            return Task.FromResult(_resolutionCache[entityKey]); // return from cache

        var partitionKey = State._entityPartitions[entityKey]; // resolve partition;
        var resourceKey = State._partitionResources[partitionKey]; // resolve resource;
        var resolution =
            new Resolution()
            {
                Entity = State._entities[entityKey],
                Partition = State._partitions[partitionKey],
                Resource = State._resources[resourceKey]
            }; // create resolution

        State._resolutionCache.Add(entityKey, resolution); // cache the resolution

        return Task.FromResult(resolution);
    }

    ...
}
```

## Zugreifen auf Ressourcen mit begrenzter Funktion

Betrachten wir nun ein weiteres Beispiel. Exklusiver Zugriff auf wertvolle Ressourcen, wie z. B. Datenbanken, Speicherkonten und Dateisysteme mit begrenzter Durchsatzfunktion. Das Szenario ist wie folgt: Die Verarbeitung von Ereignissen soll mit einem Actor namens EventProcessor durchgeführt werden, der für die Verarbeitung und Persistenz des Ereignisses, aus Gründen der Einfachheit in diesem Fall in eine CSV-Datei, verantwortlich ist. Wir können zwar den weiter oben erläuterten Partitionierungsansatz zum horizontalen Hochskalieren unserer Ressourcen befolgen, müssen uns jedoch noch weiter mit Parallelitätsproblemen beschäftigen. Daher wurde ein dateibasiertes Beispiel gewählt, um diesen bestimmten Punkt zu verdeutlichen – das Schreiben aus mehreren Actors in eine einzelne Datei führt zu Parallelitätsproblemen. Um dieses Problem zu beheben, wird ein weiterer Actor namens EventWriter eingeführt, der über die exklusiven Besitzrechte an den eingeschränkten Ressourcen verfügt. Dieses Szenario ist im Folgenden dargestellt:

![][3]

EventProcessor-Actors werden als "Statusfreie Worker" gekennzeichnet und können dadurch von der Laufzeit über den Cluster hinweg nach Bedarf skaliert werden. Daher wurden in der obigen Abbildung keine Bezeichner für diese Actors verwendet. Anders ausgedrückt: Statusfreie Actors stellen einen Pool von Workern dar, der von der Laufzeit verwaltet wird. Im folgenden Beispielcode führt der EventProcessor-Actor zwei Schritte aus: Er entscheidet, welcher EventWriter (daher Ressource) verwendet werden soll, und ruft den ausgewählten Actor zum Schreiben des verarbeiteten Ereignisses auf. Aus Gründen der Einfachheit wird "Ereignistyp" als Bezeichner für den EventWriter-Actor ausgewählt. Anders ausgedrückt: Es gibt ausschließlich einen einzigen EventWriter für diesen Ereignistyp, der Singlethread- und exklusiven Zugriff auf die Ressource ermöglicht.

## Codebeispiel für Ressourcenkontrolle – Ereignisprozessor

```csharp
public interface IEventProcessor : IActor
{
    Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

public class EventProcessor : Actor, IEventProcessor
{
    public Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        // This where we write to constrained resource...
        var eventWriterKey = ResolveWriter(eventType, eventTime);
        var eventWriter = ActorProxy.Create<IEventWriter>(eventWriterKey);

        return eventWriter.WriteEventAsync(eventId, eventType, eventTime, payload);
    }

    private long ResolveWriter(long eventType, DateTime eventTime)
    {
        // To simplify, we are returning event type as to identify the event writer actor.
        return eventType;
    }
}
```

Betrachten wir nun den EventWriter-Actor. Seine Funktion beschränkt sich eigentlich darauf, den exklusiven Zugriff auf die beschränkte Ressource, in diesem Fall die Datei, zu steuern und Ereignisse in die Datei zu schreiben.
## Codebeispiel für Ressourcenkontrolle – Ereignisschreiber

```csharp
public interface IEventWriter : IActor
{
    Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
    Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

[DataContract]
public class EventWriterState
{
    [DataMember]
    public string _filename;
}

public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;

    public override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);

        return base.OnActivateAsync();
    }

    public override Task OnDeactivateAsync()
    {
        _writer.Close();
        return base.OnDeactivateAsync();
    }

    public async Task WriteEventAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var text = string.Format("{0}, {1}, {2}, {3}", eventId, eventType, eventTime, payload);
        await _writer.WriteLineAsync(text);
        await _writer.FlushAsync();
    }
 }
```

Da ein einzelner Actor für die Ressource verantwortlich ist, können Funktionen, wie z. B. Pufferung, hinzugefügt werden. Eingehende Ereignisse können gepuffert und in regelmäßigen Abständen mithilfe eines Timers, oder wenn unser Puffer voll ist, geschrieben werden. Hier ein einfaches Timer-basiertes Beispiel:
## Codebeispiel für Ressourcenkontrolle – Ereignisschreiber mit Puffer

```csharp
[DataMember]
public class EventWriterState
{
    [DataMember]
    public string _filename;
    [DataMember]
    public Queue<CustomEvent> _buffer;
}

public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;
    private IActorTimer _timer;

    public override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (State._buffer.Count == 0)
            return;

        while (State._buffer.Count > 0)
        {
            var customEvent = State._buffer.Dequeue();
            await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
        }
    }

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        return TaskDone.Done;
    }
}
```

Der obige Code funktioniert zwar einwandfrei, jedoch wissen Clients nicht, ob ihr Ereignis in den zugrunde liegenden Speicher gelangt ist. Um Pufferung zu erlauben und Clients wissen zu lassen, was mit ihrer Anforderung geschieht, wird der folgende Ansatz eingeführt, um Clients warten zu lassen, bis ihr Ereignis in die .CSV-Datei geschrieben wurde:

## Codebeispiel für Ressourcenkontrolle – Asynchrone Batchverarbeitung

```csharp
public class AsyncBatchExecutor
{
    private readonly List<TaskCompletionSource<bool>> actionPromises;

    public AsyncBatchExecutor()
    {
        this.actionPromises = new List<TaskCompletionSource<bool>>();
    }

    public int Count
    {
        get
        {
            return actionPromises.Count;
        }
    }

    public Task SubmitNext()
    {
        var resolver = new TaskCompletionSource<bool>();
        actionPromises.Add(resolver);
        return resolver.Task;
    }

    public void Flush()
    {
        foreach (var tcs in actionPromises)
        {
            tcs.TrySetResult(true);

        }
        actionPromises.Clear();
    }
}
```

Diese Klasse wird verwendet, um eine Liste der nicht abgeschlossenen Tasks (zum Blockieren von Clients) zu erstellen und zu verwalten und diese in einem Durchgang abzuschliessen, nachdem die gepufferten Ereignisse in den Speicher geschrieben wurden. In der EventWriter-Klasse müssen drei Schritte ausgeführt werden: Kennzeichnen der Actor-Klasse als "Eintrittsinvariant", Zurückgeben des Ergebnisses von SubmitNext() und Leeren des Timers. Der geänderte Code lautet wie folgt:

## Codebeispiel für Ressourcenkontrolle – Pufferung mit asynchroner Batchverarbeitung

```csharp
public class EventWriter : Actor<EventWriterState>, IEventWriter
{
    public override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.GetPrimaryKeyLong());
        _writer = new StreamWriter(_filename);
        State._buffer = new Queue<CustomEvent>();
        _batchExecuter = new AsyncBatchExecutor();

        this.RegisterTimer(
            ProcessBatchAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessBatchAsync(object obj)
    {
        if (_batchExecuter.Count > 0)
        {
            // take snapshot of the batch tasks
            var batchSnapshot = _batchExecuter;
            _batchExecuter = new AsyncBatchExecutor();

            if (State._buffer.Count == 0)
                return;

            while (State._buffer.Count > 0)
            {
                var customEvent = State._buffer.Dequeue();
                await this.WriteEventAsync(customEvent.EventId, customEvent.EventType, customEvent.EventTime, customEvent.Payload);
            }

            _batchExecuter.Flush();
        }
    }
    ...

    public Task WriteEventBufferAsync(long eventId, long eventType, DateTime eventTime, string payload)
    {
        var customEvent = new CustomEvent()
        {
            EventId = eventId,
            EventType = eventType,
            EventTime = eventTime,
            Payload = payload
        };

        State._buffer.Enqueue(customEvent);

        // we are adding an incomplete task to batch executer and returning this task.
        // this will block until task is completed when we call Flush();
        return _batchExecuter.SubmitNext();
    }
}
```

Scheint einfach zu sein? Das ist es auch. Aber hinter der Einfachheit verbirgt sich Leistungsfähigkeit für das Unternehmen. Diese Architektur bietet Folgendes:

* Speicherortunabhängige Ressourcenadressierung.
* Abstimmbare Poolgröße einfach durch Ändern der Anzahl der Actors, die im Auftrag einer Ressource agieren.
* Clientseitige koordinierte Poolnutzung (wie dargestellt) oder serverseitige (stellen Sie sich einen einzelnen Actor vor jedem dieser Pools im Bild vor).
* Skalierbare Pool-Hinzufügung (fügen Sie Actors hinzu, die die neue Ressource darstellen).
* Ein Actor kann (wie bereits dargestellt) Ergebnisse aus einer Back-End-Ressource bei Bedarf in den Zwischenspeicher stellen, oder mithilfe eines Timers in einen vorgeschalteten Zwischenspeicher, sodass die Notwendigkeit des Zugriffs auf die Backend-Ressource reduziert wird.
* Effizientes asynchrones Senden.
* Eine Programmierumgebung, die jedem Entwickler bekannt ist, nicht nur Middleware-Spezialisten.

Dieses Muster ist sehr gängig in Szenarien, in denen Entwickler entweder über beschränkte Ressourcen verfügen, die sie bei ihren Entwicklungen berücksichtigen müssen, oder für das Erstellen großer Scale-out-Systeme.


## Nächste Schritte

[Muster: Intelligenter Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Muster: Verteilte Netzwerke und Diagramme](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Muster: Komposition zustandsbehafteter Dienste](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Muster: Internet der Dinge](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Muster: Verteilte Berechnung](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Beispiele für Antimuster](service-fabric-reliable-actors-anti-patterns.md)

[Einführung in Service Fabric Actors](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch2.png
[3]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch3.png

<!---HONumber=Nov15_HO2-->