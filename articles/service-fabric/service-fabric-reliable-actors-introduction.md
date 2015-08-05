<properties
   pageTitle="Azure Service Fabric Actors – Überblick"
   description="Einführung in das Programmiermodell Azure Service Fabric Actors"
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
   ms.date="07/10/2015"
   ms.author="claudioc"/>

# Einführung in Azure Service Fabric Actors
Die Reliable Actor-API ist eines von zwei High-Level-Frameworks, das [Service Fabric](service-fabric-technical-overview.md) neben der [Reliable Services-API](service-fabric-reliable-services-introduction.md) bereitstellt.

Auf Grundlage des Actor-Musters stellt die Reliable Actors-API ein asynchrones Singlethread-Programmiermodell bereit, das nicht nur den Code vereinfacht, sondern sich auch die Skalierbarkeit und Zuverlässigkeit zunutze macht, die Service Fabric gewährleistet.

## Actors
Actors sind isolierte Singlethread-Komponenten, die Status und Verhalten einkapseln. Sie sind mit .NET-Objekten vergleichbar und bieten daher ein natürliches Programmiermodell. Jeder Actor ist eine Instanz eines Actor-Typs, ähnlich wie ein .NET-Objekt eine Instanz eines .NET-Typs ist. Beispielsweise kann es einen Actor-Typ geben, der die Funktionalität eines Rechners implementiert, und es kann viele Actors dieses Typs geben, die auf verschiedenen Knoten in einem Cluster verteilt sind. Jeder solche Actor wird durch eine Actor-ID eindeutig identifiziert.

## Definieren und Implementieren von Actor-Schnittstellen

Actors interagieren mit dem Rest des Systems und auch mit anderen Actors, indem sie asynchrone Nachrichten mithilfe eines Anforderung-Antwort-Musters übergeben. Diese Aktivitäten werden in einer Schnittstelle als asynchrone Methoden definiert. Beispielsweise kann die Schnittstelle für einen Actor-Typ, der die Funktionalität eines Rechners implementiert, wie folgt definiert werden:

```csharp
public interface ICalculatorActor : IActor
{
    Task<double> AddAsync(double valueOne, double valueTwo);
    Task<double> SubtractAsync(double valueOne, double valueTwo);
}
```

Ein Actor-Typ kann die obige Schnittstelle wie folgt implementieren:

```csharp
public class CalculatorActor : Actor, ICalculatorActor
{
    public Task<double> AddAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne + valueTwo);
    }

    public Task<double> SubtractAsync(double valueOne, double valueTwo)
    {
        return Task.FromResult(valueOne - valueTwo);
    }
}
```

Da Methodenaufrufe und die entsprechenden Antworten letztendlich zu Netzwerkanforderungen im Cluster führen, müssen die Argumente und der Ergebnistyp der zurückgegebenen Task von der Plattform serialisierbar sein. Sie müssen vor allem [Datenvertrag-serialisierbar](service-fabric-reliable-actors-notes-on-actor-type-serialization.md) sein.

> [AZURE.TIP]Die Fabric Actors-Laufzeit gibt einige [Ereignisse und Leistungsindikatoren im Zusammenhang mit Actor-Methoden aus](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Sie sind hilfreich bei der Diagnose und Leistungsüberwachung.

Die folgenden Regeln zu Actor-Schnittstellenmethoden sind erwähnenswert: – Actor-Schnittstellenmethoden können nicht überladen werden. – Actor-Schnittstellenmethoden dürfen keine out-, ref- oder optionalen Parameter aufweisen.

## Actor-Kommunikation
### Actor-Proxy
Die Actor-Client-API ermöglicht die Kommunikation zwischen einer Actor-Instanz und einem Actor-Client. Für die Kommunikation mit einem Actor erstellt ein Client ein Actor-Proxy-Objekt, das die Actor-Schnittstelle implementiert. Der Client interagiert mit dem Actor , indem er Methoden auf dem Proxy-Objekt aufruft. Der Actor-Proxy kann für die Client-Actor- sowie für die Actor-Actor-Kommunikation verwendet werden. Wenn wir mit unserem Rechnerbeispiel fortfahren, könnte der Clientcode für einen Rechner-Actor wie nachfolgend dargestellt geschrieben werden:

```csharp
ActorId actorId = ActorId.NewId();
string applicationName = "fabric:/CalculatorActorApp";
ICalculatorActor calculatorActor = ActorProxy.Create<ICalculatorActor>(actorId, applicationName);
double result = calculatorActor.AddAsync(2, 3).Result;
```

Beachten Sie die beiden Informationen, die zum Erstellen des Actor-Proxyobjekts verwendet werden: die Actor-ID und der Name der Anwendung. Die Actor-ID ist eine Kennung, die den Actor eindeutig identifiziert, während der Anwendungsname die [Service Fabric-Anwendung](service-fabric-reliable-actors-platform.md#service-fabric-application-model-concepts-for-actors) bezeichnet, in welcher der Actor bereitgestellt wird.

### Actor-Lebensdauer

Service Fabric-Actors sind virtuell. Daher ist ihre Lebensdauer nicht an die Darstellung im Arbeitsspeicher gebunden. Sie brauchen daher nicht explizit erstellt oder zerstört zu werden. Die Actors-Laufzeit aktiviert automatisch einen Actor, wenn sie zum ersten Mal eine Anforderung für diesen Actor empfängt. Wenn ein Actor eine bestimmte Zeit nicht verwendet wurde, führt die Actors-Laufzeit eine Garbage Collection für das Objekt im Arbeitsspeicher aus, merkt sich aber gleichzeitig das Vorhandensein des Actors, falls er später erneut aktiviert werden muss. Weitere Informationen finden Sie unter [Actor-Lebenszyklus und Garbage Collection](service-fabric-reliable-actors-lifecycle.md).

### Transparenz des Standorts und automatischer Failover

Um hohe Skalierbarkeit und Zuverlässigkeit zu gewährleisten, verteilt Service Fabric die Actors im gesamten Cluster und migriert sie bei Bedarf automatisch von fehlerhaften zu fehlerfreien Knoten. Die Klasse `ActorProxy` auf dem Client nimmt die erforderliche Auflösung vor, um den Actor nach ID-[Partition](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) zu suchen und einen Kommunikationskanal mit ihm zu öffnen. Außerdem versucht die Klasse `ActorProxy` bei Kommunikationsfehlern und Failover, die Verbindung wieder herzustellen. Dadurch wird sichergestellt, dass Nachrichten trotz Fehlern zuverlässig zugestellt werden. Andererseits bedeutet es aber auch, dass bei einer Actor-Implementierung doppelte Nachrichten vom gleichen Client abgerufen werden.

## Parallelität
### Turn-basierte Parallelität

Die Actors-Laufzeit bietet einfache Turn-basierte Parallelität für Actor-Methoden. Dies bedeutet, dass zu jedem beliebigen Zeitpunkt nur ein Thread innerhalb des Actor-Codes aktiv sein kann.

Ein Turn umfasst die vollständige Ausführung einer Actor-Methode als Antwort auf die Anforderung von anderen Actors oder Clients, oder die vollständige Ausführung eines [Timer-/Erinnerungs](service-fabric-reliable-actors-timers-reminders.md)-Rückrufs. Obwohl diese Methoden und Rückrufe asynchron sind, tritt keine Überschneidung mit der Actors-Laufzeit auf. Ein neuer Turn ist erst dann zulässig, wenn der aktuelle Turn vollständig abgeschlossen wurde. Oder anders ausgedrückt: Ein aktuell ausgeführter Actor-Methoden- oder Timer-/Erinnerungs-Rückruf muss vollständig abgeschlossen sein, bevor ein neuer Methodenaufruf oder ein Rückruf ausgeführt werden kann. Eine Methode oder ein Rückruf wird als abgeschlossen betrachtet, wenn die Ausführung von der Methode oder dem Rückruf zurückgegeben wurde, und die von der Methode oder dem Rückruf zurückgegebene Aufgabe abgeschlossen wurde. Es sollte hervorgehoben werden, dass Turn-basierte Parallelität selbst über verschiedene Methoden, Timer und Rückrufe hinweg eingehalten wird.

Die Actors-Laufzeit erzwingt Turn-basierte Parallelität durch Einsetzen einer Sperre pro Actor am Anfang des Turns und Aufheben der Sperre am Ende des Turns. Turn-basierte Parallelität wird daher auf einer Pro-Actor-Basis und nicht für alle Actors erzwungen. Actor-Methoden und Timer-/Erinnerungs-Rückrufe können für unterschiedliche Actors gleichzeitig ausgeführt werden.

Im folgenden Beispiel werden die obigen Konzepte veranschaulicht. Betrachten Sie einen Actor-Typ, der zwei asynchrone Methoden implementiert (z. B. *Method1* und *Method2*), einen Timer und eine Erinnerung. Das folgende Diagramm zeigt das Beispiel einer Zeitachse für die Ausführung dieser Methoden und Rückrufe für zwei Actors, *ActorId1* und *ActorId2*, die zu diesem Actor-Typ gehören.

![][1]

Im obigen Diagramm wurden folgende Konventionen befolgt:

- Jede vertikale Linie zeigt den logischen Fluss der Ausführung einer Methode oder eines Rückrufs für einen bestimmten Actor.
- Die in jeder vertikalen Zeile gekennzeichneten Ereignisse treten in chronologischer Reihenfolge auf, wobei neuere Ereignisse unter älteren angegeben werden.
- Für die Zeitachsen werden unterschiedliche Farben verwendet, die unterschiedlichen Actors entsprechen.
- Durch Hervorhebung wird die Dauer angegeben, während der die Sperre pro Actor für eine Methode oder einen Rückruf bestehen bleibt.

Die folgenden Punkte zu dem obigen Diagramm sollten besonders erwähnt werden:

- Bei der Ausführung von *Method1* für *ActorId2* als Antwort auf Clientanforderung *xyz789* trifft eine weitere Clientanforderung *abc123* ein, die ebenfalls die Ausführung von *Method1* für *ActorId2* erfordert. Diese Ausführung von *Method1* beginnt jedoch erst, nachdem die vorherige Ausführung abgeschlossen wurde. Analog gilt: Eine von *ActorId2* registrierte Erinnerung wird ausgelöst, während *Method1* als Antwort auf die Clientanforderung *xyz789* ausgeführt wird. Der Erinnerungs-Rückruf wird erst ausgeführt, nachdem beide Ausführungen von *Method1* abgeschlossen sind. All dies basiert auf der für *ActorId2* erzwungenen Turn-basierten Parallelität.
- Analog dazu wird Turn-basierte Parallelität auch für *ActorId1* erzwungen, wie die serielle Ausführung von *Method1*, *Method2* und des Timer-Rückrufs für *ActorId1* zeigt.
- Die Ausführung von *Method1* für *ActorId1* überschneidet sich mit deren Ausführung für *ActorId2*. Der Grund dafür ist, dass Turn-basierte Parallelität nur innerhalb eines Actors und nicht Actor-übergreifend erzwungen wird.
- Bei einigen Ausführungen von Methode/Rückruf wird der von der Methode/dem Rückruf zurückgegebene `Task` nach der Rückkehr der Methode abgeschlossen. Bei einigen anderen ist der `Task` bereits zum Zeitpunkt der Rückkehr der Methode/des Rückrufs abgeschlossen. In beiden Fällen wird die Sperre pro Actor erst aufgehoben, nachdem beides erfolgt ist, d. h. nachdem die Methode/der Rückruf zurückgekehrt ist und der `Task` abgeschlossen ist.

### Eintrittsinvarianz

Die Actors-Laufzeit lässt standardmäßig Eintrittsinvarianz zu. Dies bedeutet: Wenn eine Actor-Methode von Actor A eine Methode auf Actor B aufruft, die wiederum eine andere Methode auf Actor A aufruft, wird die Ausführung dieser Methode erlaubt, da sie zum selben logischen Kontext der Aufrufkette gehört. Alle Timer- und Erinnerungs-Aufrufe beginnen mit dem neuen logischen Aufrufkontext. Ausführlichere Informationen finden Sie im Abschnitt [Eintrittsinvarianz](service-fabric-reliable-actors-reentrancy.md).

### Umfang der Parallelitätsgarantien

Die Actors-Laufzeit bietet diese Parallelitätsgarantien in Situationen, in denen sie den Aufruf dieser Methoden steuert. Beispielsweise bietet sie diese Garantien für die Methodenaufrufe, die als Antwort auf den Empfang einer Clientanforderung erfolgen, sowie für Timer- und Erinnerungs-Rückrufe . Wenn jedoch der Actor-Code diese Methoden außerhalb der von der Actors-Laufzeit bereitgestellten Mechanismen direkt aufruft, kann die Laufzeit keine Parallelitätsgarantien bieten. Wenn z. B. die Methode im Kontext einer Aufgabe aufgerufen wird, die nicht mit dem von den Actor-Methoden zurückgegebenen Task verknüpft ist, oder wenn sie von einem Thread aufgerufen wird, den der Actor selbst erstellt, kann die Laufzeitumgebung keine Parallelitätsgarantien bieten. Zum Durchführen von Hintergrundoperationen sollten Actors daher [Actor-Timer oder Actor-Erinnerungen](service-fabric-reliable-actors-timers-reminders.md) verwenden, welche Turn-basierte Parallelität berücksichtigen.

> [AZURE.TIP]Die Fabric Actors-Laufzeit gibt einige [Ereignisse und Leistungsindikatoren im Zusammenhang mit Parallelität aus](service-fabric-reliable-actors-diagnostics.md#concurrency-events-and-performance-counters). Sie sind hilfreich bei der Diagnose und Leistungsüberwachung.

## Actor-Zustandsverwaltung
Mithilfe von Fabric Actors können Sie Actors erstellen, die entweder statusfrei oder statusbehaftet sind.

### Statusfreie Actors
Statusfreie Actors, die von der Basisklasse ``Actor`` abgeleitet werden, müssen keinen Status haben, der von der Actors-Laufzeit verwaltet wird. Die Member-Variablen bleiben wie jeder andere .NET-Typ während ihres gesamten Lebenszyklus im Arbeitsspeicher erhalten. Wenn nach einem bestimmten Zeitraum der Inaktivität eine Garbage Collection durchgeführt wird, geht ihr Status verloren. Auf ähnliche Weise kann der Status aufgrund eines Failovers verloren gehen, der bei Upgrades, Ressourcenausgleichsvorgängen oder infolge von Fehlern im Actor-Prozess oder in seinem Host-Knoten auftritt.

Es folgt ein Beispiel für einen statusfreien Actor.

```csharp
class HelloActor : Actor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

### Statusbehaftete Actors
Statusbehaftete Actors besitzen einen Status, der über Garbage Collections und Failover hinweg erhalten bleiben muss. Sie werden von der Basisklasse `Actor<TState>` abgeleitet, wobei `TState` der Statustyp ist, der erhalten bleiben muss. Auf den Status kann in den Actor-Methoden über die Eigenschaft `State` in der Basisklasse zugegriffen werden.

Es folgt ein Beispiel für einen statusbehafteten Actor, der auf den Status zugreift.

```csharp
class VoicemailBoxActor : Actor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

Der Actor-Status bleibt über Garbage Collections und Failover hinweg erhalten, indem er auf dem Datenträger gespeichert und über mehrere Knoten im Cluster repliziert wird. Das bedeutet, dass der Actor-Statustyp ähnlich wie Methodenargumente und Rückgabewerte [Datenvertrag-serialisierbar](service-fabric-reliable-actors-notes-on-actor-type-serialization.md) sein muss.

> [AZURE.NOTE]Ausführliche Informationen zum Modus für die Definition von Schnittstellen und Actor-Statustypen finden Sie im Artikel [Reliable Actors – Hinweise zur Serialisierung](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

#### Actor-Status-Anbieter
Das Speichern und Abrufen des Status wird von einem Actor-Status-Anbieter ermöglicht. Der Status-Anbieter kann pro Actor oder für alle Actors innerhalb einer Assembly durch das spezifische Attribut des Status-Anbieters konfiguriert werden. Beim Aktivieren eines Actors wird sein Status in den Arbeitsspeicher geladen. Beim Abschluss einer Actor-Methode wird der geänderte Status automatisch von der Actors-Laufzeit gespeichert, indem eine Methode auf dem Status-Anbieter aufgerufen wird. Wenn beim Speichervorgang Fehler auftreten, erstellt die Actors-Laufzeit eine neue Actor-Instanz und lädt den letzten konsistenten Status vom Status-Anbieter.

Standardmäßig verwenden statusbehaftete Actors den Schlüsselwertspeicher des Actor-Status-Anbieters, der auf dem verteilten Schlüsselwertspeicher aufbaut, der von der Service Fabric-Plattform bereitgestellt wird. Weitere Informationen finden Sie in dem Thema über [Statusbehaftete Actors](service-fabric-reliable-actors-platform.md#actor-state-provider-choices).

> [AZURE.TIP]Die Actors-Laufzeit gibt einige [Ereignisse und Leistungsindikatoren im Zusammenhang mit der Actor-Statusverwaltung](service-fabric-reliable-actors-diagnostics.md#actor-state-management-events-and-performance-counters) aus. Sie sind hilfreich bei der Diagnose und Leistungsüberwachung.

#### Schreibschutzmethoden
Die Actors-Laufzeit speichert standardmäßig den Actor-Status nach Abschluss eines Actor-Methodenaufrufs, Timer-Rückrufs oder Erinnerungsrückrufs. Es ist kein anderer Actor-Aufruf zulässig, bis der Speicherstatus abgeschlossen ist.

Es kann Actor-Methoden geben, die den Status nicht ändern. In diesem Fall kann sich der zusätzliche Zeitaufwand für die Speicherung des Status auf den Gesamtdurchsatz des Systems auswirken. Um dies zu vermeiden, können Sie die Methoden und Timer-Rückrufe, die den Status nicht ändern, als schreibgeschützt markieren.

Das folgende Beispiel zeigt, wie eine Actor-Methode mithilfe von Attribut `Readonly` als schreibgeschützt markiert werden kann.

```csharp
public interface IVoicemailBoxActor : IActor
{
    [Readonly]
    Task<List<Voicemail>> GetMessagesAsync();
}
```

Timer-Rückrufe können mit Attribut `Readonly` auf ähnliche Weise markiert werden. Bei Erinnerungen wird das Nur-Lese-Flag als Argument an die `RegisterReminder` -Methode übergeben, die zum Registrieren der Erinnerung aufgerufen wird.

## Nächste Schritte
### Konzepte
[Actor-Lebenszyklus und Garbage Collection](service-fabric-reliable-actors-lifecycle.md)

[Actor-Timer und -Erinnerungen](service-fabric-reliable-actors-timers-reminders.md)

[Actor-Ereignisse](service-fabric-reliable-actors-events.md)

[Actor-Eintrittsinvarianz](service-fabric-reliable-actors-reentrancy.md)

[Verwendung der Service Fabric-Plattform durch Fabric Actors](service-fabric-reliable-actors-platform.md)

[Konfigurieren des KVSActorStateProvider-Actors](../Service-Fabric/service-fabric-reliable-actors-KVSActorstateprovider-configuration.md)

[Actor-Diagnose und -Leistungsüberwachung](service-fabric-reliable-actors-diagnostics.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png

<!---HONumber=July15_HO4-->