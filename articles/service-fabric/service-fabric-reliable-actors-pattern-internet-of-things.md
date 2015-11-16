
<properties
   pageTitle="Reliable Actors für das Internet der Dinge | Microsoft Azure"
   description="Service Fabric Reliable Actors ist der Schlüsselbaustein in einem System, das ein Messagingsystem-Front-End mit Unterstützung mehrerer Übertragungstypen, wie z. B. HTTPS, MQTT oder AMQP, kombiniert und dann mit Akteuren kommuniziert, die einzelne Geräte darstellen."
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
   ms.date="08/05/2015"
   ms.author="vturecek"/>

# Reliable Actors-Entwurfsmuster: Internet der Dinge
Seitdem das Internet der Dinge zusammen mit dem technologischen Fortschritt bei Geräten und Cloud-Diensten zum aktuellen Trend wurde, suchen Entwickler nach Schlüsselbausteinen für die Entwicklung ihrer Systeme. Das folgende Diagramm zeigt die wichtigsten Szenarien, die mithilfe von Service Fabric Reliable Actors erreicht wurden:

![][1]

Service Fabric Reliable Actors ist der Schlüsselbaustein (als mittlere Ebene) in einem System, das ein Messaging-System-Front-End mit Unterstützung mehrerer Übertragungstypen, wie z. B. HTTPS, MQTT oder AMQP, kombiniert und dann mit Actors kommuniziert, die einzelne Geräte darstellen. Da die Actors ihren Status beibehalten können, sind Modellierungsstreams – insbesondere statusbehaftete Datenstromverarbeitung – und Aggregation pro Gerät einfach. Wenn die Daten beibehalten werden müssen, kann problemlos eine Leerung bei Bedarf oder auf einem Timer durchgeführt werden, während gleichzeitig die aktuellsten N-Bit-Daten für schnelle Abfragen in einer anderen Variablen beibehalten werden können. Beachten Sie, dass wir in unseren Beispielen absichtlich die Details der Ereignis-/Messaging-Ebene ausgelassen haben, die Actors die Kommunikation mit Geräten ermöglichen,um den Fokus auf dem Actor-Modell zu halten. Es gibt im Wesentlichen zwei Szenarien, die in der Regel miteinander kombiniert werden:

* *Erfassen von Telemetrie- und Statusdaten aus einem einzelnen Gerät oder einer Gerätegruppe und Beibehalten ihres Status*. Stellen Sie sich Zehntausende von Mausefallen vor (ja, es handelt sich um ein reales Kundenszenarien), die so elementare Daten senden wie beispielsweise, ob in der Vorrichtung ein lästigen Nager gefangen wurde oder nicht. Die Daten werden nach Region aggregiert, und wenn in einer Region genug Mäuse gefangen wurden, wird ein Techniker beauftragt, die Geräte zu leeren. Eine Mausefalle als Actor? Absolut. Ein Gruppen-Actor pro Region als der Aggregator? Aber sicher.

* *Übertragen von Geräteverhalten und -konfiguration mithilfe von Push auf ein einzelnes oder eine Gruppe von Geräten*. Denken Sie an Solarstromgeräte für Privatkunden, bei denen der Anbieter unterschiedliche Konfigurationen basierend auf Verbrauch und Saisonabhängigkeit propagiert.

## Telemetriedaten und Gerätegruppierung

Betrachten wir zuerst den Fall, bei dem Geräte, nämlich zehntausend, zusammen gruppiert werden und Telemetriedaten an ihre zugeordnete Gruppe senden. Im folgenden Beispiel hat der Kunde Geräte in jeder Region bereitgestellt. Wenn das Gerät eingeschaltet wird, wird als Erstes eine ActivateMe-Meldung mit relevanten Informationen wie Standort, Version usw. gesendet. Der dem Gerät (über die Geräte-ID) zugeordnete Actor wiederum richtet den Anfangsstatus für das Gerät ein, wie z. B. lokale Speicherung des Status (könnte auch persistente sein) und Registrierung eines Gruppen-Actors. In diesem Fall ordnen wir eine zufällige Gruppe für unsere Simulation zu.

Im Rahmen der Initialisierung kann das Gerät durch Abrufen von Konfigurationsdaten aus einem Gruppen-Actor oder einem anderen Agent konfiguriert werden. Auf diese Weise die Geräte können anfangs wenig intelligente Geräte bei der Initialisierung sehr viel "smarter" werden. Nachdem dies erledigt, ist, sind Gerät und Actor zum Senden und Verarbeiten von Telemetriedaten bereit.

Alle Geräte senden ihre Telemetriedaten in regelmäßigen Abständen an den entsprechenden Actor. Wenn der Actor bereits aktiviert ist, wird dieser Actor verwendet. Andernfalls wird er aktiviert. An diesem Punkt kann der Status aus einem stabilen Speicher wiederhergestellt werden, falls erforderlich. Wenn der Actor Telemetriedaten empfängt, speichert er sie in einer lokalen Variablen. Dies geschieht hier, um das Beispiel zu vereinfachen. Bei einer realen Implementierung würden die Daten wahrscheinlich in einem externen Speicher gespeichert werden, um Vorgänge zum Überwachen und Diagnostizieren der Geräteintegrität und Leistung zu ermöglichen. Abschließen werden Telemetriedaten per Push an den Gruppen-Actor übertragen, zu dem der Geräte-Actor logisch gehört.

## Codebeispiel für Internet der Dinge – Telemetrie

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

public class Thing : Actor<ThingState>, IThing
{

    public override Task OnActivateAsync()
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
        return TaskDone.Done;
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

Auf Gruppenebene, wie in unserem Beispiel, besteht unser Ziel darin, die Geräte in der Gruppe zu überwachen und Telemetriedaten zu aggregieren, um Warnungen für Techniker zu erzeugen. In diesem Fall möchte unser Kunde Techniker in bestimmte Regionen entsenden, in denen eine bestimmte Anzahl von fehlerhaften Geräten vorhanden ist. Natürlich möchte der Kunde die Kosten senken, indem der Reisezeitaufwand für die Techniker minimiert wird. Aus diesem Grund behält jeder Gruppen-Actor einen aggregierter Status von fehlerhaften Geräten pro Region bei. Wenn diese Anzahl einen Schwellenwert erreicht, sendet der Kunde einen Techniker in die Region, um diese Geräte zu ersetzen bzw. zu reparieren. Lassen Sie uns betrachten, wie dies geschieht:

## Codebeispiel für Internet der Dinge – Gruppierung und Aggregation

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

public class ThingGroup : Actor<ThingGroupState>, IThingGroup
{

    public override Task OnActivateAsync()
    {
        State._devices = new List<ThingInfo>();
        State._faultsPerRegion = new Dictionary<string, int>();
        State._faultyDevices = new List<ThingInfo>();

        return base.OnActivateAsync();
    }

    public Task RegisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Add(deviceInfo);
        return TaskDone.Done;
    }

    public Task UnregisterDevice(ThingInfo deviceInfo)
    {
        State._devices.Remove(deviceInfo);
        return TaskDone.Done;
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

        return TaskDone.Done;
    }
}
```

Wie zu sehen ist, ist das ist Ganze ziemlich einfach. Nach der Ausführung einfacher Tests sieht das Ergebnis wie folgt aus:

```
Sending an engineer to repair/replace devices in Richmond
    Device = 33 Region = Richmond Version = 4
    Device = 79 Region = Richmond Version = 5
    Device = 89 Region = Richmond Version = 3
    Device = 63 Region = Richmond Version = 2
    Device = 85 Region = Richmond Version = 4
```

Möglicherweise denken Sie, dass es besser gewesen wäre, Geräte nach Region zu gruppieren. Selbstverständlich können Geräte nach Belieben gruppiert/partitioniert werden – sei es nach geografischem Standort, Gerätetyp, Version, Mandant usw. Hier ist Vorsicht angezeigt: Gerätestatus vs. Berichterstellung/Analyse. Aus diesem Grund wurde das Muster explizit veranschaulicht. Auffächerungsabfragen an Actors zum Erstellen von Berichterstellungs-Actors müssen vermieden werden. Unnötige Instanziierungen und Leistung sind nur zwei Nachteile, die genannt werden sollen. Für die Berichterstellung werden zwei Ansätze empfohlen:

* Verwendung eines Actors auf Gruppenebene, wie z. B. ein Aggregator, um eine Ansicht für die Gruppe beizubehalten. Lassen Sie jeden Actor per Push nur relevante Daten auf proaktive Weise an diesen Actor übermitteln. Dann kann dieser Actor auf Gruppenebene dazu verwendet werden, den Status der Geräte in der Gruppe anzuzeigen.

* Beibehaltung eines expliziten Speichers, der für die Berichterstellung bestimmt ist. Ein Aggregator kann Daten puffern und in regelmäßigen Abständen per Push an einen Berichtsspeicher für weitere Abfragen und Analysen übertragen.

## Gerätevorgang
So weit so gut, aber was ist mit den Vorgängen auf diesen Geräten? Wie bei Geräten können Actors operative Schnittstellen verfügbar machen, sodass ein Administrator Vorgänge auf Geräten durchführen kann. Ein Administrator möchte beispielsweise eine neue Konfiguration per Push an eine Gruppe von Solarstromgeräten für Privatkunden basierend auf saisonalen/regionalen Änderungen übertragen (ja, ein weiteres Szenario aus dem realen Leben).

Die Schlüsselidee hierbei ist, granulare Kontrolle über jedes Gerät mithilfe von "Thing"-Actors sowie Gruppenvorgänge mithilfe von "ThingGroup"-Actors zu haben – unabhängig davon, ob Daten von Geräten, wie z. B. Telemetriedaten, aggregiert oder Daten, wie z. B. Konfigurationsdaten, an eine große Anzahl von Geräten gesendet werden. Die Plattform sorgt für die Verteilung der Geräte-Actors und das Messaging zwischen ihnen. Dies ist vollkommen transparent für den Entwickler.

Wenn es um Computer zu Computer-Kommunikation (M2M) geht, funktioniert das Hub-and-Spoke-Muster (siehe Abschnitt "Verteilte Netzwerke und Diagramme") sowie die direkte Actor-Actor-Interaktion gut. Für M2M-Szenarien wäre es möglich, einen "Verzeichnis/Index"-Actor für eine Gruppe von Geräten zu modellieren, der es diesen ermöglicht, Meldungen zu erkennen und aneinander zu senden, wie unten dargestellt:

![][2]

Azure Service Fabric Actors kümmert sich auch um die Lebensdauer der Actors. Es ist zu bedenken, dass es Geräte im Dauerbetrieb gibt, und Geräte, die nur gelegentlich angeschlossen werden. Warum behalten wir den Actor, der für das Gerät zuständig ist, das alle 14 Stunden eine Verbindung im Arbeitsspeicher herstellt? Azure Service Fabric ermöglicht das Speichern und Wiederherstellen des Gerätestatus – wann und wo immer er benötigt wird.

Abschließend sei bemerkt, dass immer mehr Kunden Azure Service Fabric Actors als Schlüsselbaustein für ihre Implementierungen für das Internet der Dinge betrachten.

## Nächste Schritte
[Muster: Intelligenter Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Muster: Verteilte Netzwerke und Diagramme](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Muster: Ressourcenkontrolle](service-fabric-reliable-actors-pattern-resource-governance.md)

[Muster: Komposition zustandsbehafteter Dienste](service-fabric-reliable-actors-pattern-stateful-service-composition.md)

[Muster: Verteilte Berechnung](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Beispiele für Antimuster](service-fabric-reliable-actors-anti-patterns.md)

[Einführung in Service Fabric Actors](service-fabric-reliable-actors-introduction.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-internet-of-things/internet-of-things-2.png

<!---HONumber=Nov15_HO2-->