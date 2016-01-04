<properties
   pageTitle="Muster für verteilte Berechnung | Microsoft Azure"
   description="Service Fabric Reliable Actors eignen sich sehr gut für parallele asynchrone Nachrichten, leicht zu verwaltende verteilte Zustände sowie Parallelberechnungen."
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
   ms.date="11/14/2015"
   ms.author="vturecek"/>

# Reliable Actors-Entwurfsmuster: Verteilte Berechnung
Dieses Beispiel verdanken wir zum Teil dem Umstand, dass wir einen realen Kunden dabei beobachten konnten, wie er eine Finanzberechnung in Service Fabric Reliable Actors innerhalb verblüffend kurzer Zeit durchführte – eine Monte-Carlo-Simulation für die Risikoberechnung, um genau zu sein.

Zunächst mag die Behandlung dieser Art von Arbeitslast durch Azure Service Fabric im Vergleich zu eher herkömmlichen Ansätzen wie Zuordnen/Reduzieren oder MPI nicht offensichtlich sein, insbesondere nicht für diejenigen, die über keine domänenspezifischen Kenntnisse verfügen.

Aber es zeigt sich, dass Azure Service Fabric sehr gut geeignet ist für parallele asynchrone Nachrichten, leicht zu verwaltenden verteilten Status sowie Parallelberechnung, wie das folgende Diagramm veranschaulicht:

![][1]

Im folgenden Beispiel wird Pi einfach mithilfe einer Monte Carlo-Simulation berechnet. Gegeben sind die folgenden Actors:

* Prozessor für die Berechnung von Pi mithilfe von PoolTask-Actors.

* PoolTask für die Monte Carlo-Simulation und zum Senden von Ergebnissen an den Aggregator.

* Aggregator zum, wie bereits der Name sagt, Aggregieren von Ergebnissen und Senden dieser Ergebnisse an den Finaliser.

* Finaliser für die Berechnung des Endergebnisses und für die Bildschirmausgabe.

## Codebeispiel für verteilte Berechnung – Monte Carlo-Simulation

```csharp
public interface IProcessor : IActor
{
    Task ProcessAsync(int tries, int seed, int taskCount);
}

public class Processor : StatelessActor, IProcessor
{
    public Task ProcessAsync(int tries, int seed, int taskCount)
    {
        var tasks = new List<Task>();
        ActorId aggregatorId = null;
        for (int i = 0; i < taskCount; i++)
        {
            var task = ActorProxy.Create<IPooledTask>(ActorId.NewId()); // stateless
            if (i % 2 == 0) // new aggregator for every 2 pooled actors
               aggregatorId = ActorId.NewId();
            tasks.Add(task.CalculateAsync(tries, seed, aggregatorId));
        }
        return Task.WhenAll(tasks);
    }
}

public interface IPooledTask : IActor
{
    Task CalculateAsync(int tries, int seed, ActorId aggregatorId);
}

public class PooledTask : StatelessActor, IPooledTask
{
    public Task CalculateAsync(int tries, int seed, ActorId aggregatorId)
    {
        var pi = new Pi()
        {
            InCircle = 0,
            Tries = tries
        };

        var random = new Random(seed);
        double x, y;
        for (int i = 0; i < tries; i++)
        {
            x = random.NextDouble();
            y = random.NextDouble();
            if (Math.Sqrt(x * x + y * y) <= 1)
                pi.InCircle++;
        }

        var agg = ActorProxy.Create<IAggregator>(aggregatorId);
        return agg.AggregateAsync(pi);
    }
}
```

Eine gängige Methode zum Aggregieren von Ergebnissen in Azure Service Fabric ist die Verwendung von Timern. Statusfreie Actors werden aus zwei Gründen verwendet: Die Laufzeit ermittelt dynamisch, wie viele Aggregatoren benötigt werden, und bietet daher bedarfsgerechte Skalierung. Außerdem instanziiert sie diese Actors "lokal" – das heißt im selben Silo wie der aufrufende Actor, wodurch Netzwerk-Hops reduziert werden. Aggregator und Finaliser sehen wie folgt aus:

## Codebeispiel für verteilte Berechnung – Aggregator

```csharp
public interface IAggregator : IActor
{
    Task AggregateAsync(Pi pi);
}

[DataContract]
class AggregatorState
{
    [DataMember]
    public Pi _pi;
    [DataMember]
    public bool _pending;
}

public class Aggregator : StatefulActor<AggregatorState>, IAggregator
{
    protected override Task OnActivateAsync()
    {
        State._pi = new Pi() { InCircle = 0, Tries = 0 };
        State._pending = false;

        this.RegisterTimer(
            ProcessAsync,
            null,
            TimeSpan.FromSeconds(5),
            TimeSpan.FromSeconds(5));

        return base.OnActivateAsync();
    }

    private async Task ProcessAsync(object obj)
    {
        if (false == _pending)
            return;

        var finaliser = ActorProxy.Create<IFinaliser>(0);
        await finaliser.FinaliseAsync(_pi);
        State._pending = false;
        State._pi.InCircle = 0;
        State._pi.Tries = 0;
    }

    public Task AggregateAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        State._pending = true;
        return TaskDone.Done;
    }
}

public interface IFinaliser : IActor
{
    Task FinaliseAsync(Pi pi);
}

[DataContract]
class FinalizerState
{
    [DataMember]
    public Pi _pi;
}

public class Finaliser : StatefulActor<FinalizerState>, IFinaliser
{
    public override Task OnActivateAsync()
    {
        State._pi = new Pi()
        {
            InCircle = 0,
            Tries = 0
        };

        return base.OnActivateAsync();
    }

    public Task FinaliseAsync(Pi pi)
    {
        State._pi.InCircle += pi.InCircle;
        State._pi.Tries += pi.Tries;
        Console.WriteLine(" Pi = {0:N9}  T = {1:N0}, {2}",(double)State._pi.InCircle / (double)State._pi.Tries * 4.0, State._pi.Tries, State._pi.InCircle);

        return Task.FromResult(true);
    }
}
```

An diesem Punkt sollte klar sein, wie das Leaderboard-Beispiel durch einen Aggregator im Hinblick auf Skalierung und Leistung potenziell verbessert werden könnte.

Es soll hier keineswegs behauptet werden, Azure Service Fabric sei ein Drop-in Replacement für die weitere verteilte Berechnung von Big-Data-Frameworks oder Hochleistungs-Computing. Es ist aber so konzipiert, dass es einfach einiges besser kann. Es ist möglich, Workflows und verteilte parallele Berechnung in Azure Service Fabric zu modellieren und gleichzeitig von den Einfachheitsvorteilen zu profitieren, die es bietet.

## Nächste Schritte
[Muster: Intelligenter Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Muster: Verteilte Netzwerke und Diagramme](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Muster: Ressourcenkontrolle](service-fabric-reliable-actors-pattern-resource-governance.md)

[Muster: Komposition zustandsbehafteter Dienste](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Muster: Internet der Dinge](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Beispiele für Antimuster](service-fabric-reliable-actors-anti-patterns.md)

[Einführung in Service Fabric Actors](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-distributed-computation/distributed-computation-1.png

<!---HONumber=Nov15_HO4-->