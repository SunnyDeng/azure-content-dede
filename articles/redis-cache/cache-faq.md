<properties 
	pageTitle="Azure Redis Cache – häufig gestellte Fragen" 
	description="In diesem Artikel erhalten Sie Antworten auf häufig gestellte Fragen sowie Informationen zu Mustern und Best Practices für Azure Redis Cache." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/20/2016" 
	ms.author="sdanie"/>

# Azure Redis Cache – häufig gestellte Fragen

In diesem Artikel erhalten Sie Antworten auf häufig gestellte Fragen sowie Informationen zu Mustern und Best Practices für Azure Redis Cache.

<a name="cache-size"></a>
## Welches Redis Cache-Angebot und welche Redis Cache-Größe sollte ich verwenden?
Jedes Azure Redis Cache-Angebot bietet unterschiedliche Optionen in Bezug auf **Größe**, **Bandbreite**, **Hochverfügbarkeit** und **SLA**.

Nachfolgend sind verschiedene Aspekte aufgeführt, die Ihnen bei der Wahl helfen können.

-	**Speicher**: Der Basic-Tarif und der Standard-Tarif bieten 250 MB bis 53 GB. Der Premium-Tarif bietet bis zu 530 GB, die [auf Anfrage](mailto:wapteams@microsoft.com?subject=Redis%20Cache%20quota%20increase) aufgestockt werden können. Weitere Informationen finden Sie unter [Azure Redis Cache Preise](https://azure.microsoft.com/pricing/details/cache/).
-	**Netzwerkleistung**: Bei einer Workload, die einen hohen Durchsatz erfordert, bietet der Premium-Tarif im Vergleich zum Standard- oder Basic-Tarif eine größere Bandbreite. Zudem haben die größeren Caches aufgrund des zugrunde liegenden virtuellen Computers, der den Cache hostet, bei jedem Tarif eine höhere Bandbreite. Ausführliche Informationen finden Sie in der [Tabelle weiter unten](#cache-performance).
-	**Durchsatz**: Der Premium-Tarif bietet den maximal verfügbaren Durchsatz. Wenn Cacheserver oder -clients die Bandbreitengrenzwerte erreichen, kommt es zu Timeouts auf Clientseite. Ausführliche Informationen finden Sie in der Tabelle weiter unten.
-	**Hohe Verfügbarkeit/SLA**: Azure Redis Cache garantiert, dass ein Standard-/Premium-Cache mindestens 99,9 % der Zeit zur Verfügung steht. Weitere Informationen zu unserer SLA finden Sie unter [Azure Redis Cache Preise](https://azure.microsoft.com/support/legal/sla/cache/v1_0/). Die SLA deckt nur die Konnektivität zu den Cache-Endpunkten ab. Sie bezieht sich dagegen nicht auf Schutz vor Datenverlusten. Es wird empfohlen, das Redis-Feature für Datenpersistenz im Premium-Tarif zu verwenden, um den Schutz vor Datenverlusten zu erhöhen.
-	**Redis-Datenpersistenz**: Der Premium-Tarif ermöglicht die Persistenz der Cachedaten in einem Azure-Speicherkonto. In einem Basic- oder Standard-Cache werden alle Daten nur im Arbeitsspeicher gespeichert. Bei Problemen mit der zugrunde liegenden Infrastruktur kann es zu Datenverlusten kommen. Es wird empfohlen, das Redis-Feature für Datenpersistenz im Premium-Tarif zu verwenden, um den Schutz vor Datenverlusten zu erhöhen. Azure Redis Cache bietet RDB- und AOF-Optionen (demnächst verfügbar) bei der Redis-Persistenz. Weitere Informationen finden Sie unter [Konfigurieren von Persistenz für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-persistence.md).
-	**Redis Cluster**: Wenn Sie Caches mit einer Größe über 53 GB erstellen oder Daten über mehrere Redis-Knoten horizontal partitionieren möchten, können Sie das im Premium-Tarif verfügbare Redis-Clustering verwenden. Für die hohe Verfügbarkeit besteht jeder Knoten aus einem Paar aus primärem Cache und Replikatcache. Weitere Informationen finden Sie unter [Konfigurieren von Clustern für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-clustering.md).
-	**Erhöhte Sicherheit und Isolation**: Die Bereitstellung von Azure Virtual Network (VNET) bietet verbesserte Sicherheit und Isolation für den Azure Redis Cache sowie Subnetze, Zugriffssteuerungsrichtlinien und andere Features, mit denen der Zugriff weiter eingeschränkt wird. Weitere Informationen finden Sie unter [Konfigurieren der Unterstützung virtueller Netzwerke für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-vnet.md).
-	**Konfigurieren von Redis**: Sowohl im Standard- als auch im Premium-Tarif können Sie Redis für Keyspace-Benachrichtigungen konfigurieren.
-	**Maximale Anzahl von Clientverbindungen**: Der Premium-Tarif bietet die maximale Anzahl von Clients, die eine Verbindung mit Redis herstellen können, mit einer größeren Anzahl an Verbindungen für größere Caches. [Angaben zu den Preisen finden Sie in der Preisübersicht](https://azure.microsoft.com/pricing/details/cache/).
-	**Dedizierter Kern für Redis Server**: Im Premium-Tarif verfügen alle Cachegrößen über einen dedizierten Kern für Redis. Im Basic- oder Standard-Tarif verfügen alle Cachegrößen ab C1 über einen dedizierten Kern für Redis-Server.
-	**Redis verwendet Single-Threading**, deshalb bietet der Einsatz von mehr als zwei Kernen keine zusätzlichen Vorteile gegenüber der Verwendung von nur zwei Kernen. Größere virtuelle Computer besitzen jedoch i. d. R. mehr Bandbreite als kleine virtuelle Computer. Wenn Cacheserver oder -client die Bandbreitengrenzwerte erreichen, kommt es zu Timeouts auf Clientseite.
-	**Leistungsoptimierungen**: Caches im Premium-Tarif werden auf Hardware mit schnelleren Prozessoren bereitgestellt, die im Vergleich zu den Tarifen Basic oder Standard eine bessere Leistung bieten. Caches im Premium-Tarif erreichen einen höheren Durchsatz und geringere Wartezeiten.

<a name="cache-performance"></a>In der folgenden Tabelle sind die maximalen Bandbreitenwerte beim Testen verschiedener Standard- und Premium-Cachegrößen bei Verwendung von `redis-benchmark.exe` aus einem virtuellen IaaS-Computer mit dem Azure Redis Cache-Endpunkt angegeben. Beachten Sie, dass diese Werte nicht garantiert werden und dass keine SLA für diese Werte bereitgestellt wird. Die Werte sollten jedoch typischerweise erreicht werden. Führen Sie Auslastungstests für Ihre Anwendung durch, um die geeignete Cachegröße für Ihre Anwendung zu ermitteln.

Aus dieser Tabelle können folgende Schlussfolgerungen gezogen werden.

-	Der Durchsatz für Cache derselben Größe ist im Tarif "Premium" im Vergleich zum Tarif "Standard" höher. Bei einem Cache mit 6 GB liegt der Durchsatz von P1 bei 140.000 RPS im Vergleich zu 49.000 RPS bei C3.
-	Mit dem Redis-Clustering steigt der Durchsatz linear, je mehr Shards (Knoten) Sie im Cluster verwenden. Wenn Sie z. B. einen P4-Cluster mit 10 Shards erstellen, beträgt der verfügbare Durchsatz 250 KB * 10 = 2,5 Millionen RPS.
-	Der Durchsatz für größere Schlüsselgrößen ist im Premium-Tarif höher als im Standard-Tarif.

| Tarif | Größe | Verfügbare Bandbreite | 1 KB Schlüsselgröße |
|----------------------|--------|----------------------------|--------------------------------|
| **Standard-Cachegröße** | &nbsp; |**Megabits pro Sekunde (Mbit/s)** | **Anforderungen pro Sekunde (RPS)** |
| C0 | 250 MB | 5 | 600 |
| C1 | 1 GB | 100 | 12\.200 |
| C2 | 2,5 GB | 200 | 24\.000 |
| C3 | 6 GB | 400 | 49\.000 |
| C4 | 13 GB | 500 | 61\.000 |
| C5 | 26 GB | 1000 | 115\.000 |
| C6 | 53 GB | 2000 | 150\.000 |
| **Premium-Cachegröße** | &nbsp; | &nbsp; | **Anforderungen pro Sekunde (RPS), pro Shard** |
| P1 | 6 GB | 1000 | 140\.000 |
| P2 | 13 GB | 2000 | 220\.000 |
| P3 | 26 GB | 2000 | 220\.000 |
| P4 | 53 GB | 4000 | 250\.000 |


Anweisungen zum Herunterladen von Redis-Tools wie beispielsweise `redis-benchmark.exe` finden Sie im Abschnitt [Wie führe ich Redis-Befehle aus?](#cache-commands).

<a name="cache-region"></a>
## Ich welcher Region sollte ich meinen Cache platzieren?

Um eine optimale Leistung und die niedrigste Latenz zu erzielen, sollten Sie Ihren Azure Redis Cache in derselben Region platzieren wie Ihre Cacheclientanwendung.

<a name="cache-billing"></a>
## Wie wird Azure Redis Cache abgerechnet?

Die Preise von Azure Redis Cache finden Sie [hier](http://azure.microsoft.com/pricing/details/cache/). Auf der Seite mit den Preisen sind die Kosten als Stundensatz ausgewiesen. Caches werden auf Minutenbasis ab dem Zeitpunkt der Erstellung des Caches bis zu dem Zeitpunkt abgerechnet, an dem ein Cache gelöscht wird. Es gibt keine Möglichkeit, die Abrechnung eines Caches zu beenden oder anzuhalten.

<a name="cache-timeouts"></a>
## Warum kommt es zu Timeouts?

Timeouts treten auf dem Client auf, der mit Redis kommuniziert. In der Regel kommt es auf dem Redis-Server nicht zu Timeouts. Wenn ein Befehl an den Redis-Server gesendet wird, wird dieser in die Warteschlange eingereiht, bis der Redis-Server den Befehl auswählt und ausführt. Auf dem Client kann es bei diesem Vorgang zu einem Timeout kommen. In diesem Fall wird auf der aufrufenden Seite eine Ausnahme ausgelöst. Weitere Informationen zur Behandlung von Timeoutproblemen finden Sie unter [Untersuchen von Timeoutausnahmen in "StackExchange.Redis" für Azure Redis Cache](http://azure.microsoft.com/blog/2015/02/10/investigating-timeout-exceptions-in-stackexchange-redis-for-azure-redis-cache/).

<a name="cache-monitor"></a>
## Wie überwache ich die Integrität und Leistung meines Caches?

Microsoft Azure Redis Cache-Instanzen können im [Azure-Portal](https://portal.azure.com) überwacht werden. Sie können Metriken anzeigen, Metrikdiagramme an das Startmenü anheften, Daten- und Zeitbereiche für Überwachungsdiagramme anpassen, Metriken aus Diagrammen hinzufügen und entfernen sowie Warnungen festlegen, die ausgelöst werden, wenn bestimmte Bedingungen erfüllt sind. Diese Tools ermöglichen es Ihnen, die Integrität Ihrer Azure Redis Cache-Instanzen zu überwachen und unterstützen Sie beim Verwalten Ihrer Cachinganwendungen. Weitere Informationen zum Überwachen Ihrer Caches finden Sie unter [Überwachen von Azure Redis Cache](https://msdn.microsoft.com/library/azure/dn763945.aspx).

<a name="cache-disconnect"></a>
## Warum wurde mein Client vom Cache getrennt?

Nachfolgend werden einige Gründe dafür aufgeführt, warum ein Cache getrennt wird.

-	Gründe auf Clientseite
	-	Die Clientanwendung wurde neu bereitgestellt.
	-	Die Clientanwendung hat eine Skalierung ausgeführt.
		-	Bei Cloud Services oder Web-Apps kann dies aufgrund einer automatischen Skalierung der Fall sein.
	-	Die Netzwerkschicht auf Clientseite wurde geändert.
	-	Vorübergehende Fehler auf dem Client oder in den Netzwerkknoten zwischen Client und Server.
	-	Die Bandbreitenschwellenwerte wurden erreicht.
	-	Die Ausführung CPU-bezogener Vorgänge hat zu viel Zeit in Anspruch genommen.
-	Gründe auf Serverseite
	-	Im Standard-Cacheangebot initiiert der Azure Redis Cache-Dienst ein Failover vom primären Knoten auf den sekundären Knoten.
	-	Azure hat ein Patching der Instanz durchgeführt, auf dem der Cache bereitgestellt wurde.
		-	Dies kann bei Redis-Serverupdates oder im Rahmen der allgemeinen VM-Wartung der Fall sein.

<a name="cache-configuration"></a>
## Was bewirken die Konfigurationsoptionen für "StackExchange.Redis"?

Für "StackExchange.Redis" stehen zahlreiche Optionen zur Verfügung. In diesem Abschnitt werden einige der gängigsten Optionen erläutert. Ausführlichere Informationen zu den StackExchange.Redis-Optionen finden Sie unter [StackExchange.Redis-Konfiguration](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Configuration.md).

ConfigurationOptions|Beschreibung|Empfehlung
---|---|---
AbortOnConnectFail|Wenn Sie diese Option auf "true" setzen, wird die Verbindung nach einem Netzwerkausfall nicht wiederhergestellt.|Bei Festlegung auf "false" kann "StackExchange.Redis" die Verbindung automatisch wiederherstellen.
ConnectRetry|Die Anzahl von Versuchen für die Verbindungsherstellung bei der anfänglichen Verbindung.||
ConnectTimeout|Timeout in Millisekunden für Verbindungsvorgänge.|

In den meisten Fällen reichen die Standardwerte des Clients aus. Sie können die Optionen basierend auf Ihrer Workload optimieren.

-	Wiederholungsversuche
	-	Für "ConnectRetry" und "ConnectTimeout" lautet die allgemeine Empfehlung, nach einem Fehler schnell abzubrechen und den Vorgang zu wiederholen. Dies hängt von Ihrer Workload und davon ab, wie lange es auf Ihrem Client durchschnittlich dauert, einen Redis-Befehl auszugeben und eine Antwort zu empfangen.
	-	Ermöglichen Sie eine automatische Neuverbindung durch "StackExchange.Redis", statt die Prüfung des Verbindungsstatus und die erneute Verbindungsherstellung selbst zu prüfen. **Vermeiden Sie die Verwendung der ConnectionMultiplexer.IsConnected-Eigenschaft**.
	-	Schneeballeffekt – Gelegentlich kann es zu Problemen kommen, bei denen Wiederholungsversuche zu nicht behebbaren, immer wiederkehrenden Problemen führen. In diesem Fall sollten Sie die Verwendung eines exponentiellen Backoff/Retry-Algorithmus in Erwägung ziehen, wie er im Artikel [Allgemeine Hinweise zu Wiederholungsversuchen](https://github.com/mspnp/azure-guidance/blob/master/Retry-General.md) der Microsoft Patterns & Practices-Gruppe beschrieben wird (in englischer Sprache).
-	Timeoutwerte
	-	Berücksichtigen Sie Ihre Workload, und legen Sie die Werte entsprechend fest. Wenn Sie große Werte speichern, legen Sie die Timeouteinstellung auf einen höheren Wert fest.
		-	Legen Sie "ABortOnConnectFail" auf "false" fest, und überlassen Sie "StackExchange.Redis" die erneute Verbindungsherstellung.
-	Verwenden Sie eine einzelne ConnectionMultiplexer-Instanz für die Anwendung. Sie können "LazyConnection" verwenden, um eine einzelne Instanz zu erstellen, die über eine Connection-Eigenschaft zurückgegeben wird, wie gezeigt in [Verbindungsherstellung mit dem Cache unter Verwendung der ConnectionMultiplexer-Klasse](https://msdn.microsoft.com/library/azure/dn690521.aspx#Connect).
-	Legen Sie die `ConnectionMultiplexer.ClientName`-Eigenschaft zu Diagnosezwecken auf einen eindeutigen App-Instanznamen fest.
-	Verwenden Sie für benutzerdefinierte Workloads mehrere `ConnectionMultiplexer`-Instanzen.
	-	Sie können diesem Modell folgen, wenn die Last in Ihrer Anwendung variiert. Beispiel:
		-	Sie können einen Multiplexer zur Verarbeitung großer Schlüssel verwenden. 
		-	Sie können einen Multiplexer zur Verarbeitung kleiner Schlüssel verwenden. 
		-	Sie können verschiedene Werte für Verbindungstimeouts und eine retry-Logik für jede verwendete ConnectionMultiplexer-Instanz verwenden.
		-	Legen Sie die `ClientName`-Eigenschaft für jeden Multiplexer fest, um die Diagnose zu vereinfachen. 
		-	Dies führt zu einer optimierten Latenz pro `ConnectionMultiplexer`.

<a name="threadpool"></a>
## Wichtige Details zum Threadpool-Wachstum

Der CLR-Threadpool enthält zwei Typen von Threads: Workerthreads und E/A-Abschlussportthreads (oder IOCP-Threads, I/O Completion Port-Threads).

-	Workerthreads werden für Vorgänge wie das Verarbeiten der Methoden `Task.Run(…)` oder `ThreadPool.QueueUserWorkItem(…)` verwendet. Diese Threads werden auch von verschiedenen Komponenten in der CLR verwendet, wenn Arbeitsvorgänge in einem Hintergrundthread ausgeführt werden müssen.
-	IOCP-Threads werden verwendet, wenn asynchrone E/A-Vorgänge ausgeführt werden (z. B. das Lesen aus dem Netzwerk).  

Der Threadpool stellt neue Workerthreads oder E/A-Abschlussthreads nach Bedarf (und ohne Drosselung) bereit, bis die Einstellung für das Minimum des jeweiligen Threadtyps erreicht ist. Standardmäßig entspricht die minimale Anzahl von Threads der Anzahl von Prozessoren in einem System.

Wenn die Anzahl der vorhandenen (ausgelasteten) Threads die minimale Anzahl von Threads erreicht, drosselt der Threadpool die Rate, mit der er neue Threads hinzufügt, auf einen Thread pro 500 Millisekunden. Das bedeutet: Wenn bei Ihrem System eine Arbeitsspitze eingeht, die einen IOCP-Thread benötigt, werden diese Arbeitsvorgänge sehr schnell verarbeitet. Wenn für die Arbeitsspitze jedoch mehr Threads erforderlich sind als in der konfigurierten Einstellung für das Minimum vorgesehen, tritt eine gewisse Verzögerung bei der Verarbeitung einiger Arbeitsvorgänge auf. Der Threadpool wartet darauf, dass zwei Dinge geschehen:

1. Ein vorhandener Thread wird frei, um die Arbeitsvorgänge zu verarbeiten.
1. Es wird 500 Millisekunden lang kein vorhandener Thread frei, und daraufhin wird ein neuer Thread erstellt.

Im Grunde genommen heißt dass: Wenn die Anzahl von ausgelasteten Threads größer ist als die minimale Anzahl von Threads, muss wahrscheinlich eine Verzögerung von 500 ms in Kauf genommen werden, bevor der Netzwerkdatenverkehr von der Anwendung verarbeitet wird. Außerdem gilt es zu beachten, dass ein Thread nach einem längeren Leerlauf als 15 Sekunden (soweit ich mich erinnere) bereinigt wird. Danach kann der Zyklus aus Wachstum und Schrumpfung erneut beginnen.

Betrachten wir eine Beispielfehlermeldung aus StackExchange.Redis (Build 1.0.450 oder höher). Sie sehen, dass nun Threadpool-Statistiken ausgegeben werden (siehe die Details für IOCP und WORKER unten).

	System.TimeoutException: Timeout performing GET MyKey, inst: 2, mgr: Inactive, 
	queue: 6, qu: 0, qs: 6, qc: 0, wr: 0, wq: 0, in: 0, ar: 0, 
	IOCP: (Busy=6,Free=994,Min=4,Max=1000), 
	WORKER: (Busy=3,Free=997,Min=4,Max=1000)

Im obigen Beispiel sehen Sie, dass bei den IOCP-Threads sechs Threads ausgelastet sind und dass das System für ein Minimum von vier Threads konfiguriert ist. In diesem Fall wird der Client wahrscheinlich zwei Verzögerungen von 500 ms hingenommen haben, weil 6 größer als 4 ist.

Beachten Sie, dass für StackExchange.Redis ein Timout eintreten kann, wenn IOCP- oder WORKER-Threads gedrosselt werden.

### Empfehlung

Vor dem Hintergrund dieser Informationen empfehlen wir dringend, als minimalen Wert für IOCP- und WORKER-Threads einen höheren Wert als den Standardwert zu konfigurieren. Wir können keine allgemeingültigen Richtlinien zur Größe dieses Werts geben, denn der richtige Wert für eine Anwendung kann für eine andere Anwendung zu hoch oder zu niedrig sein. Diese Einstellung kann sich auch auf die Leistung von anderen Teilen komplexer Anwendungen auswirken. Daher muss jeder Kunde diese Einstellung gemäß seinen eigenen Anforderungen optimieren. Ein guter Ausgangspunkt sind 200 oder 300 Threads. Testen Sie diese Einstellung, und passen Sie sie nach Bedarf an.

So konfigurieren Sie diese Einstellung:

-	Verwenden Sie in ASP.NET in „web.config“ unter dem Konfigurationselement `<processModel>` die [Konfigurationseinstellung „minIoThreads“][]. Wenn die Ausführung in Azure Websites stattfindet, steht diese Einstellung nicht in den Konfigurationsoptionen zur Verfügung. Sie sollten dennoch in der Lage sein, sie programmgesteuert über die „Application\_Start“-Methode in „global.asax.cs“ vorzunehmen (siehe unten).

> **Wichtiger Hinweis:** Der in diesem Konfigurationselement angegebene Wert gilt als Einstellung *pro Kern*. Wenn Ihr Computer z. B. über vier Kerne verfügt und Sie eine Einstellung von minimal 200 E/A-Threads zur Laufzeit festlegen möchten, müssen Sie `<processModel minIoThreads="50"/>` verwenden.

-	Außerhalb von ASP.NET verwenden Sie die [ThreadPool.SetMinThreads(…)](https://msdn.microsoft.com/library/system.threading.threadpool.setminthreads.aspx)-API.

<a name="cache-redis-commands"></a>
## Was muss bei der Verwendung gängiger Redis-Befehle beachtet werden?

-	Sie sollten bestimmte Redis-Befehle mit langer Ausführungszeit nur dann einsetzen, wenn Sie deren Auswirkung verstehen.
	-	Führen Sie beispielsweise den [KEYS](http://redis.io/commands/keys)-Befehl nicht in der Produktion aus, da die Rückgabe in Abhängigkeit von der Anzahl von Schlüsseln sehr viel Zeit in Anspruch nehmen kann. Redis ist ein Server mit Single-Threading, d. h. Befehle werden nacheinander verarbeitet. Wenn Sie nach KEYS weitere Befehle ausgeben, werden diese erst ausgeführt, wenn Redis den KEYS-Befehl verarbeitet hat.
-	Schlüsselgrößen – sollte ich kleine Schlüssel/Werte oder große Schlüssel/Werte verwenden? Dies hängt im Allgemeinen vom Szenario ab. Wenn Ihre Szenarios größere Schlüssel erfordern, können Sie die ConnectionTimeout- und retry-Werte sowie die Logik für erneute Verbindungsversuche anpassen. In Bezug auf den Redis-Server führen kleinere Werte zu einer besseren Leistung.
	-	Dies bedeutet jedoch nicht, dass Sie in Redis keine größeren Werte speichern können – Sie müssen sich lediglich der folgenden Punkte bewusst sein. Latenzen sind höher. Wenn Sie über einen größeren und einen kleineren Datensatz verfügen, können Sie mehrere ConnectionMultiplexer-Instanzen verwenden und jede mit einem anderen Satz an Werten für Timeout und erneute Verbindungsherstellung konfigurieren – wie im vorangegangenen Abschnitt [Was bewirken die StackExchange.Redis-Konfigurationsoptionen?](#cache-configuration) beschrieben.


<a name="cache-ssl"></a>
## Wann sollte ich den Nicht-SSL-Port für die Verbindungsherstellung mit Redis verwenden?

Der Redis-Server bietet keine integrierte SSL-Unterstützung, Azure Redis Cache hingegen schon. Wenn Sie eine Verbindung mit Azure Redis Cache herstellen und Ihr Client SSL unterstützt (z. B. StackExchange.Redis), dann sollten Sie SSL verwenden.

Beachten Sie, dass der Nicht-SSL-Port für neue Azure Redis Cache-Instanzen standardmäßig deaktiviert ist. Wenn Ihr Client keine SSL-Unterstützung bietet, müssen Sie den Nicht-SSL-Port mithilfe der Anweisungen im Abschnitt [Zugriffsports](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) des Artikels [Konfigurieren eines Caches in Azure Redis Cache](https://msdn.microsoft.com/library/azure/dn793612.aspx) aktivieren.

Redis-Tools wie z. B. `redis-cli` funktionieren nicht mit dem SSL-Port, aber Sie können ein Hilfsprogramm wie `stunnel` verwenden, um eine sichere Verbindung zwischen den Tools und dem SSL-Port herzustellen. Anweisungen hierzu finden Sie im Blogbeitrag [Announcing ASP.NET Session State Provider for Redis Preview Release](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Anweisungen zum Herunterladen der Redis-Tools finden Sie im Abschnitt [Wie führe ich Redis-Befehle aus?](#cache-commands).

<a name="cache-benchmarking"></a>
## Wie kann ich die Leistung meines Caches messen und testen?

-	[Aktivieren Sie die Cachediagnose](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics), damit Sie die Integrität Ihres Caches [überwachen](https://msdn.microsoft.com/library/azure/dn763945.aspx) können. Sie können die Metriken im Azure-Portal anzeigen und anschließend mit einem Tool Ihrer Wahl [herunterladen und prüfen](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring).
-	Mit "redis-benchmark.exe" können Sie Auslastungstests für Ihren Redis-Server durchführen.
	-	Stellen Sie sicher, dass sich Client und Redis Cache für die Auslastungstests in derselben Region befinden.
-	Verwenden Sie "redis-cli.exe", und überwachen Sie den Cache unter Verwendung des INFO-Befehls.
	-	Wenn Ihre Datenlast zu einer hohen Arbeitsspeicherfragmentierung führt, sollte Sie eine zentrale Skalierung auf einen größeren Cache durchführen.
-	Anweisungen zum Herunterladen der Redis-Tools finden Sie im Abschnitt [Wie führe ich Redis-Befehle aus?](#cache-commands).

<a name="cache-commands"></a>
## Wie führe ich Redis-Befehle aus?

Sie können alle aufgelisteten [Redis-Befehle](http://redis.io/commands#) mit Ausnahme der Befehle verwenden, die unter [Redis-Befehle, die in Azure Redis Cache nicht unterstützt werden](cache-configure.md#redis-commands-not-supported-in-azure-redis-cache) aufgeführt sind. Für die Ausführung von Redis-Befehlen haben Sie mehrere Optionen.

-	Wenn Sie über einen Standard- oder Premium-Cache verfügen, können Sie Redis-Befehle über die [Redis-Konsole](cache-configure.md#redis-console) ausführen. Dies ist eine sichere Methode zum Ausführen von Redis-Befehlen im Azure-Portal.
-	Sie können auch die Redis-Befehlszeilentools verwenden. Führen Sie hierzu die folgenden Schritte aus:
	-	Laden Sie die [Redis-Befehlszeilentools](https://github.com/MSOpenTech/redis/releases/download/win-2.8.19.1/redis-2.8.19.zip) herunter.
	-	Stellen Sie über `redis-cli.exe` eine Verbindung mit dem Cache her. Übergeben Sie den Cacheendpunkt mithilfe des Schalters "-h" und dem Schlüssel mit Verwendung von "-a", wie im folgenden Beispiel gezeigt.
		-	`redis-cli -h <your cache name>.redis.cache.windows.net -a <key>`
	-	Beachten Sie, dass die Redis-Befehlszeilentools nicht mit dem SSL-Port funktionieren, aber Sie können ein Hilfsprogramm wie `stunnel` verwenden, um eine sichere Verbindung zwischen den Tools und dem SSL-Port herzustellen. Anweisungen hierzu finden Sie im Blogbeitrag [Announcing ASP.NET Session State Provider for Redis Preview Release](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

<a name="cache-common-patterns"></a>
## Welche gängigen Cachemuster gibt es, und was muss berücksichtigt werden?

-	Microsoft Patterns & Practices bietet die folgenden Anleitungen (in englischer Sprache):
	-	[Anleitung zum Caching](https://github.com/mspnp/azure-guidance/blob/master/Caching.md).
	-	[Anleitung zu Design und Implementierung von Azure-Cloudanwendungen](https://github.com/mspnp/azure-guidance)
-	[Allgemeine Cachemuster mit Azure Redis Cache](cache-howto-common-cache-patterns.md)

<a name="cache-reference"></a>
## Warum gibt es für Azure Redis Cache keinen MSDN-Klassenbibliothekverweis wie für einige andere Azure-Dienste?

Microsoft Azure Redis Cache basiert auf dem populären quelloffenen Redis Cache und bietet Ihnen Zugriff auf einen sicheren, dedizierten Redis Cache, der von Microsoft verwaltet wird. Es stehen verschiedene [Redis-Clients](http://redis.io/clients) für zahlreiche Programmiersprachen zur Verfügung. Jeder Client verfügt über eine eigene API, die unter Verwendung von [Redis-Befehlen](http://redis.io/commands) Aufrufe an den Redis Cache sendet.

Da jeder Client anders ist, gibt es nicht nur einen zentralen Klassenverweis in MSDN. Stattdessen verwaltet jeder Client seine eigene Verweisdokumentation. Zusätzlich zur Verweisdokumentation gibt es mehrere Lernprogramme auf "Azure.com". Diese stehen auf der Seite [Redis Cache-Dokumentation](http://azure.microsoft.com/documentation/services/redis-cache/) zur Verfügung und erleichtern Ihnen den Einstieg in die Verwendung von Azure Redis Cache mit verschiedenen Sprachen und Cacheclients.


## Welches Azure-Cache-Angebot ist das Richtige für mich?

>[AZURE.IMPORTANT]Microsoft empfiehlt, für alle neuen Entwicklungen Azure Redis Cache zu verwenden.

Azure Cache verfügt derzeit über drei Angebote:

-	Azure Redis Cache
-	Azure Managed Cache Service
-	Azure In-Role Cache

>[AZURE.IMPORTANT]Wir geben die Deaktivierung der Dienste Azure Managed Cache Service und Azure In-Role Cache am 30. November 2016 bekannt. Sie sollten zur Vorbereitung für die Einstellung dieser Dienste zu Azure Redis Cache migrieren.
>
>Azure Redis Cache wird in Azure als Cachelösung empfohlen, seit der Dienst allgemein verfügbar ist, und ist jetzt in allen Azure-Regionen verfügbar, auch in China und für die US-Regierungsbehörden. Aufgrund dieser Verfügbarkeit kündigen wir die bevorstehende Einstellung von Managed Cache Service und In-Role Cache-Dienst an.
>
>Managed Cache Service und In-Role Cache-Dienst bleiben für Bestandskunden maximal 12 Monate ab dem Datum dieser Ankündigung am 30. November 2015 verfügbar – das Dienstendedatum für beide ist der 30. November 2016. Nach diesem Datum wird Managed Cache Service abgeschaltet, und der In-Role Cache-Dienst wird nicht mehr unterstützt.
>
>Wir entfernen die Unterstützung für das Erstellen neuer In-Role Caches aus der ersten Azure SDK-Version, die nach dem 1. Februar 2016 herauskommt. Kunden werden vorhandene Projekte öffnen können, die über In-Role Caches verfügen.
>
>Wir empfehlen allen Managed Cache Service- und In-Role Cache-Dienst-Kunden, während dieses Zeitraums zu Azure Redis Cache zu migrieren. Azure Redis Cache bietet mehr Funktionen und ist insgesamt höherwertig. Weitere Informationen zur Migration finden Sie auf der Dokumentationswebseite [Migrieren von Managed Cache Service zu Azure Redis Cache](cache-migrate-to-redis.md).
>
>Sollten Sie weitere Fragen haben, [kontaktieren Sie uns](https://azure.microsoft.com/support/options/?WT.mc_id=azurebg_email_Trans_933).

### Azure Redis Cache
Azure Redis Cache ist allgemein verfügbar in Größen bis zu 53 GB mit einer Verfügbarkeits-SLA von 99,9 %. Der neue [Premium-Tarif](cache-premium-tier-intro.md) bietet Größen bis zu 530 GB und unterstützt Clustering, VNET und Persistenz mit einer SLA von 99,9 %.

Azure Redis Cache ermöglicht den Kunden die Verwendung eines gesicherten, dedizierten Redis Cache, der von Microsoft verwaltet wird. Mit diesem Angebot können Sie die umfangreichen Features und die Umgebung von Redis nutzen und dabei von zuverlässigem Hosting und Überwachung durch Microsoft profitieren.

Im Gegensatz zu traditionellen Caches, die nur mit Schlüssel-Wert-Paaren arbeiten, wird Redis besonders für seine äußerst leistungsfähigen Datentypen geschätzt. Redis unterstützt außerdem die Ausführung atomarer Vorgänge für diese Typen, z. B. das Anhängen an eine Zeichenfolge, das Erhöhen des Werts in einem Hash, das Einfügen per Push in eine Liste, das Berechnen der Schnittmenge, der Gesamtmenge und der Differenz oder das Abrufen des Elements mit der höchsten Rangfolge in einem sortierten Satz. Weitere Funktionen sind unter anderem die Unterstützung von Transaktionen, Pub/Sub, Lua-Skripts, Schlüssel mit begrenzter Lebensdauer und Konfigurationseinstellungen, mit denen sich Redis wie ein herkömmlicher Cache verhält.

Ein weiterer wichtiger Aspekt für den Erfolg von Redis ist das gesunde, lebendige Open Source-Ökosystem. Dies spiegelt sich in den verschiedenen verfügbaren Redis-Clients in mehreren Sprachen wider. Dadurch kann er für fast jede Workload verwendet werden, die Sie innerhalb von Azure erstellen können.

Weitere Informationen zu den ersten Schritten mit Azure Redis Cache finden Sie unter [Verwenden von Azure Redis Cache](cache-dotnet-how-to-use-azure-redis-cache.md) und in der [Azure Redis Cache-Dokumentation](https://azure.microsoft.com/documentation/services/redis-cache/).

### Managed Cache Service
Managed Cache Service wird am 30. November 2016 außer Betrieb gesetzt.

### In-Role Cache
In-Role Cache wird am 30. November 2016 außer Betrieb gesetzt.

[Konfigurationseinstellung „minIoThreads“]: https://msdn.microsoft.com/library/vstudio/7w2sway1(v=vs.100).aspx

<!---HONumber=AcomDC_0121_2016-->