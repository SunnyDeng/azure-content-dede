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
   ms.date="11/13/2015"
   ms.author="vturecek"/>

# Reliable Actors-Entwurfsmuster: Ressourcengovernance

Dieses Muster und die zugehörigen Szenarien sind Entwicklern – in Unternehmen oder anderswo – vertraut, die auf lokaler Ebene oder in der Cloud über beschränkte Ressourcen verfügen, die sie nicht sofort skalieren können. Sie sind außerdem Entwicklern bekannt, die umfangreiche Anwendungen und Daten in die Cloud senden möchten.

In Unternehmen werden diese beschränkten Ressourcen, wie z. B. Datenbanken, auf hoch skalierbarer Hardware ausgeführt. Wer über eine lange Unternehmenserfahrung verfügt, weiß, dass dies eine gängige Situation in Unternehmen ist. Selbst in der Cloud tritt diese Situation auf, wenn ein Clouddienst versucht, die TCP-Beschränkung von 64.000 Verbindungen zwischen einem Adressen-/Port-Tupel zu überschreiten. Dies geschieht auch bei dem Verbindungsversuch mit einer cloudbasierten Datenbank, die die Anzahl der gleichzeitigen Verbindungen beschränkt.

In der Vergangenheit wurde dies in der Regel mithilfe von Drosselung durch nachrichtenbasierte Middleware oder kundenspezifische Pooling- und Fassadenmechanismen gelöst. Diese sind jedoch schwer zu realisieren, insbesondere wenn die mittlere Ebene skaliert werden, dabei jedoch die korrekte Verbindungsanzahl beibehalten werden soll. Diese Lösung ist anfällig und komplex.

Wie das Muster des intelligenten Caches erstreckt sich auch dieses Muster über mehrere Szenarien und Kunden, die bereits über funktionierende Systeme mit beschränkten Ressourcen verfügen. Bei Ihren Systemen müssen nicht nur Dienste horizontal hochskaliert werden, sondern auch deren Status im Arbeitsspeicher und der persistente Status im stabilen Speicher.

Das folgende Diagramm zeigt dieses Szenario:

![Statusfreie Actors, Partitionierung und eingeschränkte Ressourcen][1]

## Modellieren von Cacheszenarien mit Actors

Der Zugriff auf Ressourcen kann als ein oder mehrere Actors modelliert werden, die als Proxys für eine Ressource oder eine Gruppe von Ressourcen fungieren (also beispielsweise eine Verbindung). Die Ressource kann dann entweder direkt über einzelne Actors oder über einen Koordinierungs-Actor verwaltet werden, der wiederum die Ressourcen-Actors verwaltet.

Künftig werden wir uns vor allem mit der häufig aus Leistungs- und Skalierbarkeitsgründen entstehenden Notwendigkeit befassen, mit einer partitionierten (Shard-) Speicherebene zu arbeiten. Die erste Option ist relativ einfach. Es ist möglich, eine statische Funktion zum Zuordnen und Auflösen der Actors in nachgeschalteten Ressourcen zu verwenden. Eine solche Funktion kann z. B. eine Verbindungszeichenfolge mit einer bestimmten Eingabe zurückgeben. Es steht Ihnen vollkommen frei, wie diese Funktion implementiert wird. Dieser Ansatz hat auch seine Nachteile, wie z. B. statische Affinität, die die Neupartitionierung von Ressourcen oder die Neuzuordnung eines Actors schwierig macht.

Hier ist ein einfaches Beispiel. Wir wenden modulare Arithmetik an, um den Datenbanknamen anhand der **userId** zu bestimmen, und verwenden die **region**, um den Datenbankserver zu identifizieren.

### Codebeispiel für Ressourcengovernance: Statische Auflösung

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

Dieser Ansatz ist einfach, aber er ist nicht sehr flexibel. Betrachten wir nun einen fortgeschritteneren und nützlicheren Ansatz.

Als Erstes modellieren wir die Affinität zwischen physischen Ressourcen und Actors. Dazu verwenden wir einen Actor namens **Resolver**. Er kennt die Zuordnung zwischen Benutzern, logischen Partitionen und physischen Ressourcen. Der Resolver verwaltet seine Daten in einem permanenten Speicher. Sie werden jedoch zwischengespeichert, sodass sie problemlos abgerufen werden können. Wie in dem [Wechselkursbeispiel im Muster des intelligenten Caches](service-fabric-reliable-actors-pattern-smart-cache.md) zu sehen war, kann ein Resolver die neuesten Informationen mithilfe eines Timers auf proaktive Weise abrufen. Wenn der Benutzer-Actor die Ressource, die er verwenden muss, auflöst, speichert er sie in einer lokalen Variablen namens **\_resolution** zwischen und verwendet sie während ihrer Lebensdauer.

Einer suchbasierten Auflösung (unten dargestellt) wurde der Vorzug gegeben gegenüber einfachem Hashing oder Bereich-Hashing, und zwar aufgrund der Flexibilität, die sie bei Vorgängen bietet. Dazu gehören beispielsweise das zentrale Herunter- oder Hochskalieren und das Verschieben eines Benutzers aus einer Ressource in eine andere.

![Eine suchbasierte Resolver-Lösung][2]

In der obigen Abbildung ist zu erkennen, dass Actor B23 zuerst seine Ressource **DB1** auflöst (Auflösung) und sie zwischenspeichert. Nachfolgende Vorgänge können nun die zwischengespeicherte Auflösung verwenden, um auf die beschränkte Ressource zuzugreifen. Da die Actors Singlethread-Ausführung unterstützen, müssen Entwickler sich keine Gedanken mehr über Parallelzugriff auf die Ressource machen. Sehen Sie sich im folgenden Codebeispiel den Benutzer- und den Resolver-Actor an.

### Codebeispiel für Ressourcengovernance: Resolver

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


public class User : StatefulActor<UserState>, IUser
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

#### Ressourcengovernance – Resolver-Beispiel

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

public class Resolver : StatefulActor<ResolverState>, IResolver
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

## Zugriff auf Ressourcen mit begrenzter Funktionalität

Betrachten wir nun ein weiteres Beispiel. Exklusiver Zugriff auf wertvolle Ressourcen, wie z. B. Datenbanken, Speicherkonten und Dateisysteme mit begrenzter Durchsatzfunktion. In diesem Szenario möchten wir mit einem Actor namens „EventProcessor“ Ereignisse verarbeiten. Dieser Actor ist verantwortlich für die Verarbeitung und Persistenz des Ereignisses, in diesem Fall der Einfachheit halber in einer CSV-Datei. Wir können den weiter oben erläuterten Partitionierungsansatz zum horizontalen Hochskalieren unserer Ressourcen befolgen, müssen uns jedoch dennoch mit Parallelitätsproblemen beschäftigen. Wir haben ein dateibasiertes Beispiel gewählt, um diesen Punkt zu verdeutlichen, weil das Schreiben aus mehreren Actors in eine einzelne Datei zu Parallelitätsproblemen führt. Um dieses Problem zu beheben, wird ein weiterer Actor namens EventWriter eingeführt, der über die exklusiven Besitzrechte an den eingeschränkten Ressourcen verfügt. Dieses Szenario ist im Folgenden dargestellt:

![Schreiben und Verarbeiten von Ereignissen mithilfe von EventWriter und EventProcessor][3]

EventProcessor-Actors werden als „statusfreie Worker“ gekennzeichnet und können dadurch von der Laufzeit über den Cluster hinweg nach Bedarf skaliert werden. Beachten Sie, dass in der obigen Abbildung keine Bezeichner für diese Actors verwendet wurden. Statusfreie Actors stellen einen Pool von Workern dar, der von der Laufzeit verwaltet wird.

Im folgenden Beispielcode führt der EventProcessor-Actor zwei Schritte durch. Als Erstes entscheidet er, welcher EventWriter (und somit welche Ressource) verwendet werden soll, und ruft dann den ausgewählten Actor zum Schreiben des verarbeiteten Ereignisses auf. Der Einfachheit halber wurde der Ereignistyp als Bezeichner für den EventWriter-Actor ausgewählt. Es gibt somit nur einen einzigen EventWriter für diesen Ereignistyp, der Singlethread- und exklusiven Zugriff auf die Ressource ermöglicht.

### Codebeispiel für Ressourcengovernance: EventProcessor

```csharp
public interface IEventProcessor : IActor
{
    Task ProcessEventAsync(long eventId, long eventType, DateTime eventTime, string payload);
}

public class EventProcessor : StatelessActor, IEventProcessor
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

### Codebeispiel für Ressourcengovernance: EventWriter

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

public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;

    protected override Task OnActivateAsync()
    {
        State._filename = string.Format(@"C:\{0}.csv", this.Id);
        _writer = new StreamWriter(_filename);

        return base.OnActivateAsync();
    }

    protected override Task OnDeactivateAsync()
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

Da ein einzelner Actor für die Ressource verantwortlich ist, können Sie Funktionen, wie z. B. Pufferung, hinzufügen. Eingehende Ereignisse können gepuffert und in regelmäßigen Abständen mithilfe eines Timers oder immer dann geschrieben werden, wenn der Puffer voll ist. Der folgende Code enthält ein einfaches Timer-basiertes Beispiel.

### Codebeispiel für Ressourcengovernance: EventWriter mit Puffer

```csharp
[DataMember]
public class EventWriterState
{
    [DataMember]
    public string _filename;
    [DataMember]
    public Queue<CustomEvent> _buffer;
}

public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    private StreamWriter _writer;
    private IActorTimer _timer;

    protected override Task OnActivateAsync()
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

Der obige Code funktioniert einwandfrei, jedoch wissen Clients nicht, ob ihr Ereignis in den zugrunde liegenden Speicher gelangt ist. Um Pufferung zu erlauben und Clients Informationen über ihre Anforderung bereitzustellen, lässt der folgende Ansatz Clients warten, bis ihr Ereignis in die CSV-Datei geschrieben wurde.

### Codebeispiel für Ressourcengovernance: Asynchrone Batchverarbeitung

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

Wir verwenden diese Klasse zum Erstellen und Verwalten einer Liste der nicht abgeschlossenen Aufgaben (zum Blockieren von Clients). Wir werden diese in einem einzigen Durchlauf durchführen, nachdem wir die gepufferten Ereignisse in den Speicher geschrieben haben.

In der EventWriter-Klasse müssen drei Schritte ausgeführt werden: Kennzeichnen der Actor-Klasse als „eintrittsinvariant“, Zurückgeben des Ergebnisses von **SubmitNext()** und Leeren des Timers. Sehen Sie sich unten den geänderten Code an.

### Codebeispiel für Ressourcengovernance: Pufferung mit asynchroner Batchverarbeitung

```csharp
public class EventWriter : StatefulActor<EventWriterState>, IEventWriter
{
    protected override Task OnActivateAsync()
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

Hinter der Einfachheit dieses Ansatzes verbirgt sich Leistungsfähigkeit für das Unternehmen. Diese Architektur bietet Ihnen folgende Vorteile:

* Speicherortunabhängige Ressourcenadressierung.
* Abstimmbare Poolgröße einfach durch Ändern der Anzahl der Actors, die im Auftrag einer Ressource agieren.
* Clientseitige (wie dargestellt) oder serverseitige koordinierte Poolnutzung (stellen Sie sich vor jedem dieser Pools im Bild einen einzelnen Actor vor).
* Skalierbares Hinzufügen von Pools (fügen Sie Actors hinzu, die die neue Ressource darstellen).
* Actors, die die Ergebnisse aus Back-End-Ressourcen bei Bedarf oder über einen Timer im vorgeschalteten Cache zwischenspeichern können, wie zuvor gezeigt. Dadurch reduziert sich die Notwendigkeit, Back-End-Ressourcen zu erreichen.
* Effizientes asynchrones Senden.
* Eine Programmierumgebung, die jedem Entwickler bekannt ist, nicht nur Middleware-Spezialisten.

Dieses Muster ist sehr gängig in Szenarien, in denen Entwickler über beschränkte Ressourcen verfügen, die sie bei ihren Entwicklungen berücksichtigen müssen. Es ist auch üblich bei Entwicklern, die umfangreiche Scale-Out-Systeme erstellen.


## Nächste Schritte

[Muster: Intelligenter Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Muster: Verteilte Netzwerke und Diagramme](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Muster: Zusammenstellung statusbehafteter Dienste](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Muster: Internet der Dinge](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Muster: Verteilte Berechnung](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Einige Antimuster](service-fabric-reliable-actors-anti-patterns.md)

[Einführung in Service Fabric Reliable Actors](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch1.png
[2]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch2.png
[3]: ./media/service-fabric-reliable-actors-pattern-resource-governance/resourcegovernance_arch3.png

<!---HONumber=AcomDC_0121_2016-->