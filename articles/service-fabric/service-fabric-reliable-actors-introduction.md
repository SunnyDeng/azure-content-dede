<properties
   pageTitle="Übersicht über Service Fabric Reliable Actors | Microsoft Azure"
   description="Einführung in das Programmiermodell Service Fabric Reliable Actors"
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

# Einführung in Service Fabric Reliable Actors
Die Reliable Actors-API ist eines von zwei High-Level-Frameworks, das von [Azure Service Fabric](service-fabric-technical-overview.md) neben der [Reliable Services-API](service-fabric-reliable-services-introduction.md) bereitgestellt wird.

Auf Grundlage des Actor-Musters stellt die Reliable Actors-API ein asynchrones Singlethread-Programmiermodell bereit, das nicht nur den Code vereinfacht, sondern sich auch die Skalierbarkeit und Zuverlässigkeit zunutze macht, die Service Fabric gewährleistet.

## Actors
Actors sind isolierte Singlethread-Komponenten, die Status und Verhalten einkapseln. Sie sind mit .NET-Objekten vergleichbar und bieten daher ein natürliches Programmiermodell. Jeder Actor ist eine Instanz eines Actor-Typs, ähnlich wie ein .NET-Objekt eine Instanz eines .NET-Typs ist. Beispielsweise kann ein Actor-Typ die Funktionalität eines Rechners implementieren, und viele Actors dieses Typs können auf verschiedene Knoten in einem Cluster verteilt sein. Jeder solche Actor wird durch eine Actor-ID eindeutig identifiziert.

## Definieren und Implementieren von Actor-Schnittstellen

Actors interagieren mit dem Rest des Systems und auch mit anderen Actors, indem sie asynchrone Nachrichten mithilfe eines Anforderung-Antwort-Musters übergeben. Diese Aktivitäten werden in einer Schnittstelle als asynchrone Methoden definiert. Beispielsweise kann die Schnittstelle für einen Actor-Typ, der die Funktionalität eines Rechners implementiert, wie folgt definiert werden:

```csharp
public interface ICalculatorActor : IActor
{
    Task<double> AddAsync(double valueOne, double valueTwo);
    Task<double> SubtractAsync(double valueOne, double valueTwo);
}
```

Ein Actor-Typ kann diese Schnittstelle wie folgt implementieren:

```csharp
public class CalculatorActor : StatelessActor, ICalculatorActor
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

Da Methodenaufrufe und die entsprechenden Antworten letztendlich zu Netzwerkanforderungen im Cluster führen, müssen die Argumente und die Ergebnistypen der zurückgegebenen Tasks von der Plattform serialisierbar sein. Sie müssen vor allem [Datenvertrag-serialisierbar](service-fabric-reliable-actors-notes-on-actor-type-serialization.md) sein.

> [AZURE.TIP] Die Service Fabric Actors-Laufzeit gibt einige [Ereignisse und Leistungsindikatoren im Zusammenhang mit Actor-Methoden aus](service-fabric-reliable-actors-diagnostics.md#actor-method-events-and-performance-counters). Sie sind hilfreich bei der Diagnose und Leistungsüberwachung.

Die folgenden Regeln zu Actor-Schnittstellenmethoden sind erwähnenswert:
- Actor-Schnittstellenmethoden können nicht überladen werden.
- Actor-Schnittstellenmethoden dürfen keine out-, ref- oder optionalen Parameter aufweisen.

## Actor-Kommunikation
### Actor-Proxy
Die Reliable Actors-Client-API ermöglicht die Kommunikation zwischen einer Actor-Instanz und einem Actor-Client. Für die Kommunikation mit einem Actor erstellt ein Client ein Actor-Proxy-Objekt, das die Actor-Schnittstelle implementiert. Der Client interagiert mit dem Actor , indem er Methoden auf dem Proxy-Objekt aufruft. Der Actor-Proxy kann für die Client-zu-Actor- sowie für die Actor-zu-Actor-Kommunikation verwendet werden. Wenn wir mit unserem Rechnerbeispiel fortfahren, könnte der Clientcode für einen Rechner-Actor wie folgt geschrieben werden:

```csharp
ActorId actorId = ActorId.NewId();
string applicationName = "fabric:/CalculatorActorApp";
ICalculatorActor calculatorActor = ActorProxy.Create<ICalculatorActor>(actorId, applicationName);
double result = calculatorActor.AddAsync(2, 3).Result;
```

Beachten Sie die beiden Informationen, die zum Erstellen des Actor-Proxyobjekts verwendet werden: die Actor-ID und der Name der Anwendung. Die Actor-ID dient zur eindeutigen Identifikation des Actors, während der Anwendungsname die [Service Fabric-Anwendung](service-fabric-reliable-actors-platform.md#service-fabric-application-model-concepts-for-actors) bezeichnet, in welcher der Actor bereitgestellt wird.

### Actor-Lebensdauer

Service Fabric-Actors sind virtuell. Daher ist ihre Lebensdauer nicht an die Darstellung im Arbeitsspeicher gebunden. Sie brauchen daher nicht explizit erstellt oder zerstört zu werden. Die Actors-Laufzeit aktiviert automatisch einen Actor, wenn sie zum ersten Mal eine Anforderung für diesen Actor empfängt. Wenn ein Actor für einen bestimmten Zeitraum nicht verwendet wird, erfasst die Actors-Laufzeit das In-Memory-Objekt in der Garbage Collection. Sie verwaltet außerdem Informationen zur Existenz des Actors, falls dieser später erneut aktiviert werden muss. Weitere Informationen finden Sie unter [Actor-Lebenszyklus und Garbage Collection](service-fabric-reliable-actors-lifecycle.md).

### Transparenz des Standorts und automatisches Failover

Um hohe Skalierbarkeit und Zuverlässigkeit zu gewährleisten, verteilt Service Fabric die Actors im gesamten Cluster und migriert sie bei Bedarf automatisch von fehlerhaften zu fehlerfreien Knoten. Die Klasse `ActorProxy` auf dem Client nimmt die erforderliche Auflösung vor, um den Actor nach ID-[Partition](service-fabric-reliable-actors-platform.md#service-fabric-partition-concepts-for-actors) zu suchen und einen Kommunikationskanal mit ihm zu öffnen. Außerdem versucht die Klasse `ActorProxy` bei Kommunikationsfehlern und Failovern, den Actor zu finden. Dadurch wird sichergestellt, dass Nachrichten trotz Fehlern zuverlässig zugestellt werden. Dies bedeutet jedoch auch, dass eine Actor-Implementierung Nachrichten von demselben Client doppelt empfangen kann.

## Parallelität
### Rundenbasierter Zugriff

Die Actors-Laufzeit stellt ein einfaches rundenbasiertes Modell für den Zugriff auf Actor-Methoden bereit. Dies bedeutet, dass zu jedem beliebigen Zeitpunkt nur ein Thread innerhalb des Actor-Codes aktiv sein kann.

Ein Turn umfasst die vollständige Ausführung einer Actor-Methode als Antwort auf eine Anforderung von anderen Actors oder Clients oder die vollständige Ausführung eines [Timer-/Erinnerungs](service-fabric-reliable-actors-timers-reminders.md)-Rückrufs. Obwohl diese Methoden und Rückrufe asynchron sind, tritt keine Überschneidung mit der Actors-Laufzeit auf. Ein Turn muss vollständig abgeschlossen sein, bevor ein neuer Turn ausgeführt werden kann. Oder anders ausgedrückt: Ein aktuell ausgeführter Actor-Methoden- oder Timer-/Erinnerungs-Rückruf muss vollständig abgeschlossen sein, bevor ein neuer Methodenaufruf oder ein Rückruf ausgeführt werden kann. Eine Methode oder ein Rückruf wird als abgeschlossen betrachtet, wenn die Ausführung von der Methode oder dem Rückruf zurückgegeben wurde und die von der Methode oder dem Rückruf zurückgegebene Aufgabe abgeschlossen wurde. Es sollte hervorgehoben werden, dass Turn-basierte Parallelität selbst über verschiedene Methoden, Timer und Rückrufe hinweg eingehalten wird.

Die Actors-Laufzeit erzwingt Turn-basierte Parallelität durch Einsetzen einer Sperre pro Actor am Anfang des Turns und Aufheben der Sperre am Ende des Turns. Turn-basierte Parallelität wird daher auf einer Pro-Actor-Basis und nicht für alle Actors erzwungen. Actor-Methoden und Timer-/Erinnerungs-Rückrufe können für unterschiedliche Actors gleichzeitig ausgeführt werden.

Im folgenden Beispiel werden die obigen Konzepte veranschaulicht. Stellen Sie sich einen Actor-Typ vor, bei dem zwei asynchrone Methoden (z.B. *Method1* und *Method2*), ein Timer und eine Erinnerung implementiert werden. Das folgende Diagramm zeigt ein Zeitachsenbeispiel für die Ausführung dieser Methoden und Rückrufe für zwei Actors (*ActorId1* und *ActorId2*), die zu diesem Actor-Typ gehören.

![Turn-basierte Parallelität und Zugriff in der Reliable Actors-Laufzeit][1]

Im obigen Diagramm werden folgende Konventionen befolgt:

- Jede vertikale Linie zeigt den logischen Fluss der Ausführung einer Methode oder eines Rückrufs für einen bestimmten Actor.
- Die in jeder vertikalen Zeile gekennzeichneten Ereignisse treten in chronologischer Reihenfolge auf, wobei neuere Ereignisse unter den älteren angegeben werden.
- Für die Zeitachsen werden unterschiedliche Farben verwendet, die unterschiedlichen Actors entsprechen.
- Durch Hervorhebung wird die Dauer angegeben, während der die Sperre pro Actor für eine Methode oder einen Rückruf bestehen bleibt.

Die folgenden Punkte zu dem obigen Diagramm sollten besonders erwähnt werden:

- Bei der Ausführung von *Method1* für *ActorId2* als Antwort auf Clientanforderung *xyz789* trifft eine weitere Clientanforderung (*abc123*) ein, für die ebenfalls die Ausführung von *Method1* für *ActorId2* erforderlich ist. Die zweite Ausführung von *Method1* beginnt aber erst, nachdem die vorherige Ausführung abgeschlossen wurde. Analog gilt: Eine von *ActorId2* registrierte Erinnerung wird ausgelöst, während *Method1* als Antwort auf die Clientanforderung *xyz789* ausgeführt wird. Der Erinnerungs-Rückruf wird erst ausgeführt, nachdem beide Ausführungen von *Method1* abgeschlossen sind. All dies basiert auf der für *ActorId2* erzwungenen Turn-basierten Parallelität.
- Analog dazu wird Turn-basierte Parallelität auch für *ActorId1* erzwungen, wie die serielle Ausführung von *Method1*, *Method2* und des Timer-Rückrufs für *ActorId1* zeigt.
- Die Ausführung von *Method1* für *ActorId1* überschneidet sich mit deren Ausführung für *ActorId2*. Der Grund dafür ist, dass Turn-basierte Parallelität nur innerhalb eines Actors und nicht Actor-übergreifend erzwungen wird.
- Bei einigen Ausführungen von Methode/Rückruf wird der von der Methode/dem Rückruf zurückgegebene `Task` nach der Rückkehr der Methode abgeschlossen. Bei einigen anderen ist der `Task` bereits zum Zeitpunkt der Rückkehr der Methode/des Rückrufs abgeschlossen. In beiden Fällen wird die Sperre pro Actor erst freigegeben, nachdem sowohl die Methode/der Rückruf als auch der `Task` abgeschlossen sind.

### Eintrittsinvarianz

Die Actors-Laufzeit lässt standardmäßig Eintrittsinvarianz zu. Dies bedeutet: Wenn eine Actor-Methode von *Actor A* eine Methode auf *Actor B* aufruft, die wiederum eine andere Methode auf *Actor A* aufruft, wird die Ausführung dieser Methode erlaubt. Der Grund dafür ist, dass sie zum selben logischen Kontext der Aufrufkette gehört. Alle Timer- und Erinnerungs-Aufrufe beginnen mit dem neuen logischen Aufrufkontext. Weitere Details finden Sie unter [Reliable Actors-Eintrittsinvarianz](service-fabric-reliable-actors-reentrancy.md).

### Umfang der Parallelitätsgarantien

Die Actors-Laufzeit bietet diese Parallelitätsgarantien in Situationen, in denen sie den Aufruf dieser Methoden steuert. Beispielsweise bietet sie diese Garantien für die Methodenaufrufe, die als Antwort auf eine Clientanforderung erfolgen, sowie für Timer- und Erinnerungsrückrufe. Wenn jedoch der Actor-Code diese Methoden außerhalb der von der Actors-Laufzeit bereitgestellten Mechanismen direkt aufruft, kann die Laufzeit keine Parallelitätsgarantien bieten. Wenn z. B. die Methode im Kontext eines Tasks aufgerufen wird, der nicht mit dem von den Actor-Methoden zurückgegebenen Task verknüpft ist, kann die Laufzeitumgebung keine Parallelitätsgarantien bieten. Auch wenn die Methode von einem Thread aufgerufen wird, den der Actor selbst erstellt, kann die Laufzeit keine Parallelitätsgarantien bieten. Zum Durchführen von Hintergrundoperationen sollten Actors daher [Actor-Timer oder Actor-Erinnerungen](service-fabric-reliable-actors-timers-reminders.md) verwenden, bei denen die Turn-basierte Parallelität berücksichtigt wird.

> [AZURE.TIP] Die Service Fabric Actors-Laufzeit gibt einige [Ereignisse und Leistungsindikatoren im Zusammenhang mit Parallelität](service-fabric-reliable-actors-diagnostics.md#concurrency-events-and-performance-counters) aus. Sie sind hilfreich bei der Diagnose und Leistungsüberwachung.

## Actor-Zustandsverwaltung
Mithilfe von Service Fabric können Sie Actors erstellen, die entweder statusfrei oder statusbehaftet sind.

### Statusfreie Actors
Statusfreie Actors, die von der Basisklasse `StatelessActor` abgeleitet werden, müssen keinen Status haben, der von der Actors-Laufzeit verwaltet wird. Ihre Membervariablen bleiben wie jeder andere .NET-Typ während ihres gesamten Lebenszyklus im Arbeitsspeicher erhalten. Wenn nach einem bestimmten Zeitraum der Inaktivität eine Garbage Collection durchgeführt wird, geht ihr Status verloren. Auf ähnliche Weise kann der Status aufgrund von Failovern verloren gehen, die bei Upgrades, Ressourcenausgleichsvorgängen oder infolge von Fehlern im Actor-Prozess oder in seinem Hostknoten auftreten können.

Es folgt ein Beispiel für einen statusfreien Actor:

```csharp
class HelloActor : StatelessActor, IHello
{
    public Task<string> SayHello(string greeting)
    {
        return Task.FromResult("You said: '" + greeting + "', I say: Hello Actors!");
    }
}
```

### Statusbehaftete Actors
Statusbehaftete Actors besitzen einen Status, der über Garbage Collections und Failover hinweg erhalten bleiben muss. Sie werden von `StatefulActor<TState>` abgeleitet, wobei `TState` der Statustyp ist, der erhalten bleiben muss. Auf den Status kann in den Actor-Methoden über die `State`-Eigenschaft in der Basisklasse zugegriffen werden.

Es folgt ein Beispiel für einen statusbehafteten Actor, der auf den Status zugreift:

```csharp
class VoicemailBoxActor : StatefulActor<VoicemailBox>, IVoicemailBoxActor
{
    public Task<List<Voicemail>> GetMessagesAsync()
    {
        return Task.FromResult(State.MessageList);
    }
    ...
}
```

Der Actor-Status bleibt über Garbage Collections und Failover hinweg erhalten, indem er auf dem Datenträger gespeichert und über mehrere Knoten im Cluster repliziert wird. Dies bedeutet, dass der Actor-Statustyp ähnlich wie Methodenargumente und Rückgabewerte [Datenvertrag-serialisierbar](service-fabric-reliable-actors-notes-on-actor-type-serialization.md) sein muss.

> [AZURE.NOTE] Ausführliche Informationen zur Definition von Schnittstellen und Actor-Statustypen finden Sie im Artikel [Hinweise zur Typserialisierung von Service Fabric Reliable Actors](service-fabric-reliable-actors-notes-on-actor-type-serialization.md).

#### Actor-Status-Anbieter
Das Speichern und Abrufen des Status wird von einem Actor-Statusanbieter ermöglicht. Der Status-Anbieter kann pro Actor oder für alle Actors innerhalb einer Assembly durch das spezifische Attribut des Status-Anbieters konfiguriert werden. Beim Aktivieren eines Actors wird sein Status in den Arbeitsspeicher geladen. Beim Abschluss einer Actor-Methode wird der geänderte Status automatisch von der Actors-Laufzeit gespeichert, indem eine Methode auf dem Statusanbieter aufgerufen wird. Wenn beim **Speichervorgang** Fehler auftreten, erstellt die Actors-Laufzeit eine neue Actor-Instanz und lädt den letzten konsistenten Status vom Zustandsanbieter.

Standardmäßig verwenden statusbehaftete Actors den Schlüsselwertspeicher des Actor-Status-Anbieters, der auf dem verteilten Schlüsselwertspeicher aufbaut, der von der Service Fabric-Plattform bereitgestellt wird. Weitere Informationen finden Sie im Thema zu [Zustandsanbieter-Optionen](service-fabric-reliable-actors-platform.md#actor-state-provider-choices).

> [AZURE.TIP] Die Actors-Laufzeit gibt einige [Ereignisse und Leistungsindikatoren im Zusammenhang mit der Actor-Statusverwaltung](service-fabric-reliable-actors-diagnostics.md#actor-state-management-events-and-performance-counters) aus. Sie sind hilfreich bei der Diagnose und Leistungsüberwachung.

#### Schreibgeschützte Methoden
Die Actors-Laufzeit speichert standardmäßig den Actor-Status nach Abschluss eines Actor-Methodenaufrufs, Timer-Rückrufs oder Erinnerungsrückrufs. Es ist kein anderer Actor-Aufruf zulässig, bis der Speicherstatus abgeschlossen ist.

Es gibt jedoch möglicherweise Actor-Methoden, die den Status nicht ändern. In diesem Fall kann sich der zusätzliche Zeitaufwand für die Speicherung des Status auf den Gesamtdurchsatz des Systems auswirken. Um dies zu vermeiden, können Sie die Methoden und Timer-Rückrufe, die den Status nicht ändern, als schreibgeschützt markieren.

Dieses Beispiel zeigt, wie eine Actor-Methode mithilfe des Attributs `Readonly` als schreibgeschützt markiert werden kann:

```csharp
public interface IVoicemailBoxActor : IActor
{
    [Readonly]
    Task<List<Voicemail>> GetMessagesAsync();
}
```

Timer-Rückrufe können mit Attribut `Readonly` auf ähnliche Weise markiert werden. Bei Erinnerungen wird das Nur-Lese-Flag als Argument an die `RegisterReminder` -Methode übergeben, die zum Registrieren der Erinnerung aufgerufen wird.

## Nächste Schritte
[Actor-Lebenszyklus und Garbage Collection](service-fabric-reliable-actors-lifecycle.md)

[Actor-Timer und -Erinnerungen](service-fabric-reliable-actors-timers-reminders.md)

[Actor-Ereignisse](service-fabric-reliable-actors-events.md)

[Actor-Eintrittsinvarianz](service-fabric-reliable-actors-reentrancy.md)

[Verwendung der Service Fabric-Plattform durch Reliable Actors](service-fabric-reliable-actors-platform.md)

[Konfigurieren des KVSActorStateProvider-Actors](service-fabric-reliable-actors-kvsactorstateprovider-configuration.md)

[Actor-Diagnose und -Leistungsüberwachung](service-fabric-reliable-actors-diagnostics.md)

<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-introduction/concurrency.png

<!---HONumber=AcomDC_0323_2016-->