
<properties
   pageTitle="Reliable Actors für das Internet der Dinge | Microsoft Azure"
   description="Service Fabric Reliable Actors bilden den Grundbaustein in einem System, das ein Messagingsystem-Front-End mit Unterstützung mehrerer Übertragungstypen, z. B. HTTPS, MQTT oder AMQP, kombiniert."
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

# Reliable Actors-Entwurfsmuster: Internet der Dinge
Seitdem das Internet der Dinge zusammen mit dem technologischen Fortschritt bei Geräten und Clouddiensten zum aktuellen Trend wurde, suchen Entwickler nach Schlüsselbausteinen für die Entwicklung ihrer Systeme. Das folgende Diagramm zeigt die wichtigsten Szenarien, die mithilfe von Azure Service Fabric Reliable Actors erreicht wurden:

![Wichtige Szenarien in Service Fabric Reliable Actors][1]

Service Fabric Reliable Actors ist der Grundbaustein (als mittlere Ebene) in einem System, das das Front-End eines Messagingsystems mit Unterstützung für mehrere Übertragungstypen (z. B. HTTPS, MQTT oder AMQP) und die Kommunikation mit Actors kombiniert, die einzelne Geräte darstellen. Da die Actors ihren Status beibehalten können, sind Modellierungsdatenströme – insbesondere die statusbehaftete Datenstromverarbeitung – und Aggregation pro Gerät einfach umzusetzen. Wenn die Daten dauerhaft gespeichert werden müssen, können sie bei Bedarf oder mit einem Timer auf einfache Weise auf die Festplatte geleert werden. Dies ist möglich, während Sie gleichzeitig die letzten N Bits der Daten zur schnellen Abfrage in einer anderen Variablen verwalten.

Beachten Sie, dass wir in unseren Beispielen absichtlich die Details der Ereignis-/Messaging-Ebene ausgelassen haben, die Actors die Kommunikation mit Geräten ermöglicht, um den Fokus auf dem Actor-Modell zu halten. Es gibt im Wesentlichen zwei Szenarien, die in der Regel miteinander kombiniert werden:

* **Erfassen von Telemetrie- und Statusdaten von einem einzelnen Gerät oder einer Gerätegruppe und Beibehalten ihres Status**. Stellen Sie sich etwa Zehntausende von Mausefallen vor, die Daten senden. (Dies ist ein reales Kundenszenario.) Dabei handelt es sich um ganz einfache Daten, nämlich, ob das Gerät einen Schädling gefangen hat oder nicht. Die Daten werden nach Region aggregiert, und wenn in einer Region genügend Mäuse gefangen wurden, wird ein Techniker beauftragt, die Geräte zu leeren. Eine Mausefalle als Actor? Ja. Ein Gruppen-Actor pro Region als der Aggregator? Absolut.

* **Übertragen von Geräteverhalten und -konfiguration mithilfe von Push auf ein einzelnes Gerät oder eine Gruppe von Geräten**. Denken Sie an Solarstromgeräte für Privatkunden, bei denen der Anbieter unterschiedliche Konfigurationen basierend auf Verbrauch und Saisonabhängigkeit propagiert.

## Telemetriedaten und Gerätegruppierung

Betrachten wir zuerst den Fall, bei dem Geräte (in fünfstelliger Größenordnung) zusammen gruppiert werden und Telemetriedaten an ihre zugeordnete Gruppe senden. Im folgenden Beispiel hat der Kunde Geräte in jeder Region bereitgestellt. Wenn das Gerät eingeschaltet wird, wird als Erstes eine **ActivateMe**-Meldung mit relevanten Informationen wie Standort und Version gesendet. Der dem Gerät (über die Geräte-ID) zugeordnete Actor richtet den Anfangsstatus für das Gerät ein, wie z. B. lokale Speicherung des Status (kann auch dauerhaft gespeichert werden) und Registrierung eines Gruppen-Actors. In diesem Fall haben wir für unsere Simulation eine zufällige Gruppe zugeordnet.

Im Rahmen der Initialisierung kann das Gerät durch Abrufen von Konfigurationsdaten aus einem Gruppen-Actor oder einem anderen Agent konfiguriert werden. Auf diese Weise können Geräte, die anfangs kaum Informationen besitzen, bei der Initialisierung mit Daten versorgt werden. Nachdem dies erledigt ist, sind Gerät und Actor zum Senden und Verarbeiten von Telemetriedaten bereit.

Alle Geräte senden ihre Telemetriedaten in regelmäßigen Abständen an die entsprechenden Actors. Wenn ein Actor bereits aktiviert ist, wird dieser Actor verwendet. Andernfalls wird er aktiviert. An diesem Punkt kann der Status aus einem stabilen Speicher wiederhergestellt werden, falls erforderlich. Wenn der Actor Telemetriedaten empfängt, speichert er sie in einer lokalen Variablen.

Dies geschieht hier, um das Beispiel zu vereinfachen. In einer realen Implementierung würden wir sie wahrscheinlich in einem externen Speicher speichern. Dadurch könnten Geräteintegrität und Leistung in den Vorgängen überwacht und diagnostiziert werden. Abschließend werden Telemetriedaten per Push an den Gruppen-Actor übertragen, zu dem der Geräte-Actor logisch gehört.

## Codebeispiel für Internet der Dinge: Telemetrie

```csharp
public interface IThing : IActor
{
    Task ActivateMe(string region, int version);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingState
{
	[DataMember]
	public List<ThingTelemetry> _telemetry;
	[DataMember]
	public ThingInfo _deviceInfo;
	[DataMember]
	long _deviceGroupId;
}

public class Thing : StatefulActor<ThingState>, IThing
{

    protected override Task OnActivateAsync()
    {
        State._telemetry = new List<ThingTelemetry>();
        State._deviceGroupId = -1; // not activated
        return base.OnActivateAsync();
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        State._telemetry.Add(telemetry); // saving data at the device level
        if (State._deviceGroupId != -1)
        {
            var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
            return deviceGroup.SendTelemetryAsync(telemetry); // sending telemetry data for aggregation
        }
        return Task.FromResult(true);
    }

    public Task ActivateMe(string region, int version)
    {
        State._deviceInfo = new ThingInfo()
        {
            DeviceId = this.GetPrimaryKeyLong(),
            Region = region,
            Version = version
        };

        // based on the info, assign a group... for demonstration we are assigning a random group
        State._deviceGroupId = new Random().Next(10, 12);

        var deviceGroup = ActorProxy.Create<IThingGroup>(State._deviceGroupId);
        return deviceGroup.RegisterDevice(State._deviceInfo);
    }
}
```

Auf Gruppenebene besteht das Ziel in unserem Beispiel darin, die Geräte in der Gruppe zu überwachen und Telemetriedaten zu aggregieren, um Warnungen für Techniker zu erzeugen. In diesem Fall möchte unser Kunde Techniker in bestimmte Regionen entsenden, in denen eine bestimmte Anzahl von fehlerhaften Geräten vorhanden ist. Natürlich möchte der Kunde außerdem die Kosten senken, indem der Reisezeitaufwand für die Techniker minimiert wird. Aus diesem Grund behält jeder Gruppen-Actor einen aggregierter Status von fehlerhaften Geräten pro Region bei. Wenn diese Anzahl einen Schwellenwert erreicht, sendet der Kunde einen Techniker in die Region, um die fehlerhaften Geräte zu reparieren oder auszutauschen.

Lassen Sie uns betrachten, wie dies geschieht:

## Codebeispiel für Internet der Dinge: Gruppierung und Aggregation

```csharp
public interface IThingGroup : IActor
{
    Task RegisterDevice(ThingInfo deviceInfo);
    Task UnregisterDevice(ThingInfo deviceInfo);
    Task SendTelemetryAsync(ThingTelemetry telemetry);
}

[DataContract]
class ThingGroupState
{
    [DataMember]
    public List<ThingInfo> _devices;
    [DataMember]
    public Dictionary<string, int> _faultsPerRegion;
    [DataMember]
    public List<ThingInfo> _faultyDevices;
}

public class ThingGroup : StatefulActor<ThingGroupState>, IThingGroup
{

    protected override Task OnActivateAsync()
    {
        State._devices = new List<ThingInfo>();
        State._faultsPerRegion = new Dictionary<string, int>();
        State._faultyDevices = new List<ThingInfo>();

        return base.OnActivateAsync();
    }

    public Task RegisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Add(deviceInfo);
        return Task.FromResult(true);
    }

    public Task UnregisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Remove(deviceInfo);
        return Task.FromResult(true);
    }

    public Task SendTelemetryAsync(ThingTelemetry telemetry)
    {
        if (telemetry.DevelopedFault)
        {
            if (false == _faultsPerRegion.ContainsKey(telemetry.Region))
            {
                State._faultsPerRegion[telemetry.Region] = 0;
            }
            State._faultsPerRegion[telemetry.Region]++;
            State._faultyDevices.Add(_devices.Where(d => d.DeviceId == telemetry.DeviceId).FirstOrDefault());

            if (State._faultsPerRegion[telemetry.Region] > _devices.Count(d => d.Region == telemetry.Region) / 3)
            {
                Console.WriteLine("Sending an engineer to repair/replace devices in {0}", telemetry.Region);
                foreach(var device in State._faultyDevices.Where(d => d.Region == telemetry.Region).ToList())
                {
                    Console.WriteLine("\t{0}", device);
                }
            }
        }

        return Task.FromResult(true);
    }
}
```

Wie Sie sehen können, ist es relativ unkompliziert. Nach der Ausführung einfacher Tests sieht das Ergebnis wie folgt aus:

```
Sending an engineer to repair/replace devices in Richmond
    Device = 33 Region = Richmond Version = 4
    Device = 79 Region = Richmond Version = 5
    Device = 89 Region = Richmond Version = 3
    Device = 63 Region = Richmond Version = 2
    Device = 85 Region = Richmond Version = 4
```

Vielleicht denken Sie, dass es besser gewesen wäre, Geräte nach Region zu gruppieren. Es ist völlig Ihnen überlassen, wie Sie Geräte gruppieren und/oder partitionieren, ob nach geografischem Standort, Gerätetyp, Version, Mandant oder anderen Kriterien.

Im Hinblick auf den Gerätestatus im Vergleich zu Berichterstellung und Analyse ist jedoch Vorsicht geboten: Auffächerungsabfragen an Actors zum Erstellen von Berichterstellungs-Actors sollten Sie unbedingt vermeiden. Dadurch können unnötige Instanziierungen und Leistungsprobleme entstehen, um nur zwei Nachteile zu nennen. Aus diesem Grund wurde das Muster explizit veranschaulicht. Für die Berichterstellung werden zwei Ansätze empfohlen:

* Verwenden Sie einen Actor auf Gruppenebene, wie z. B. einen Aggregator, um eine Ansicht für die Gruppe zu verwalten. Lassen Sie jeden Actor per Push nur relevante Daten auf proaktive Weise an diesen Actor übermitteln. Dieser Actor auf Gruppenebene kann dazu verwendet werden, den Status der Geräte in der Gruppe anzuzeigen.

* Beibehaltung eines expliziten Speichers, der für die Berichterstellung bestimmt ist. Ein Aggregator kann Daten puffern und in regelmäßigen Abständen per Push an einen Berichtsspeicher für weitere Abfragen und Analysen übertragen.

## Gerätevorgang
So weit, so gut. Aber was ist mit den Vorgängen auf diesen Geräten? Wie bei Geräten können Actors operative Schnittstellen verfügbar machen, sodass ein Administrator Vorgänge auf Geräten durchführen kann. Ein Administrator möchte beispielsweise eine neue Konfiguration per Push an eine Gruppe von Solarstromgeräten für Privatkunden basierend auf saisonalen und regionalen Änderungen übertragen (ein weiteres Szenario aus dem wahren Leben).

Die Hauptidee hierbei ist eine präzise Kontrolle über jedes Gerät mithilfe von Thing-Actors sowie die Kontrolle über Gruppenvorgänge mithilfe von ThingGroup-Actors. Dies gilt unabhängig davon, ob Sie Daten wie z. B. Telemetriedaten von Geräten aggregieren oder Daten wie z. B. Konfigurationsdaten an eine große Anzahl von Geräten senden. Die Plattform übernimmt sowohl die Verteilung der Geräte-Actors als auch das Messaging zwischen den Actors. Dieses Verhalten ist für den Entwickler völlig transparent.

Bei der Kommunikation zwischen Computern (M2M) haben sich das Hub-and-Spoke-Muster, das im [Abschnitt zu verteilten Netzwerken und Diagrammen](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md) behandelt wurde, sowie die direkte Interaktion zwischen Actors gut bewährt. Für M2M-Szenarien könnten Sie einen „Verzeichnis/Index“-Actor für eine Gruppe von Geräten modellieren, der es diesen Geräten ermöglicht, Meldungen zu erkennen und einander zu senden, wie unten dargestellt:

![Modell eines „Verzeichnis/Index“-Actors für eine Gruppe von Geräten][2]

Das Service Fabric Reliable Actors-Modell kümmert sich auch um die Lebensdauer der Actors. Sehen Sie es so: Wenn Sie sowohl immer betriebsbereite Geräte als auch gelegentlich verbundene Geräte verwenden, warum sollten Sie einen Actor im Arbeitsspeicher behalten, der sich um ein Gerät kümmert, das nur alle 14 Stunden eine Verbindung herstellt? Mit Service Fabric können Sie den Status eines Geräts speichern und wiederherstellen, wann und wo immer Sie möchten.

Immer mehr Kunden ziehen Service Fabric Reliable Actors als Grundbausteine für ihre Implementierungen des Internets der Dinge in Betracht.

## Nächste Schritte
[Muster: Intelligenter Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Muster: Verteilte Netzwerke und Diagramme](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Muster: Ressourcengovernance](service-fabric-reliable-actors-pattern-resource-governance.md)

[Muster: Zusammenstellung statusbehafteter Dienste](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Muster: Verteilte Berechnung](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Einige Antimuster](service-fabric-reliable-actors-anti-patterns.md)

[Einführung in Service Fabric Actors](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-2.png

<!---HONumber=AcomDC_0121_2016-->