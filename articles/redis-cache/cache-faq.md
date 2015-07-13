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
	ms.date="05/01/2015" 
	ms.author="sdanie"/>

# Azure Redis Cache – häufig gestellte Fragen

In diesem Artikel erhalten Sie Antworten auf häufig gestellte Fragen sowie Informationen zu Mustern und Best Practices für Azure Redis Cache.

<a name="cache-size"></a>
## Welches Redis Cache-Angebot und welche Redis Cache-Größe sollte ich verwenden?

Jedes Azure Redis Cache-Angebot bietet unterschiedliche Optionen in Bezug auf **Größe**, **Bandbreite**, **Hochverfügbarkeit** und **SLA**.

-	Basic-SKU – Einzelner Knoten, keine Replikation oder SLA, Cachegrößen von 250 MB bis 53 GB.
-	Standard-SKU – Primäre/sekundäre Knoten mit automatischer Replikation, SLA mit 99,9 %, Cachegrößen von 250 MB bis 53 GB.

Wenn Sie eine höhere Verfügbarkeit benötigen, wählen Sie das Standard-Angebot, das eine SLA mit 99,9 % bietet. Für Entwicklung und Prototyperstellung oder für Szenarien, in denen keine SLA erforderlich ist, reicht das Basic-Angebot aus.

Cachegrößen und Bandbreite entsprechen ungefähr den Größen und Bandbreiten der virtuellen Computer, die den Cache hosten. Die Cachegröße von 250 MB wird im Basic- und Standard-Angebot auf einem virtuellen Computer der Stufe "Extra Small (A0)" gehostet. Hierbei erfolgt das Hosting über gemeinsam genutzte Kerne, während bei den anderen Größen dedizierte Kerne verwendet werden. Die Cachegröße von 1 GB wird auf einem virtuellen Computer der Stufe "Small (A1)" gehostet. Dieser verfügt über 1 dedizierten virtuellen Kern, der sowohl das Betriebssystem als auch Redis Cache bedient. Größere Cachegrößen werden auf größeren VM-Instanzen mit mehreren dedizierten virtuellen Kernen gehostet.

Wenn Ihr Cache einen hohen Durchsatz aufweist, wählen Sie 1 GB oder mehr, damit Ihr Cache mit dedizierten Kernen ausgeführt wird. Der 1-GB-Cache wird auf einem virtuellen Computer mit 1 Kern ausgeführt. Dieser Kern bedient sowohl das Betriebssystem als auch den Cache. Cachegrößen von mehr als 1 GB werden auf virtuellen Computern mit mehreren Kernen ausgeführt, und Redis Cache verwendet einen dedizierten Kern, der nicht gemeinsam mit dem Betriebssystem verwendet wird.

**Redis verwendet Single-Threading**, deshalb bietet der Einsatz von mehr als zwei Kernen keine zusätzlichen Vorteile gegenüber der Verwendung von nur zwei Kernen. **Größere VMs bieten jedoch typischerweise mehr Bandbreite als kleine VMs.** Wenn Cacheserver oder -client die Bandbreitengrenzwerte erreichen, kommt es zu Timeouts auf Clientseite.

Die folgende Tabelle zeigt die maximalen Bandbreitenwerte beim Testen verschiedener Azure Redis Cache-Größen bei Verwendung von `redis-benchmark.exe` aus einer IaaS-VM mit dem Azure Redis Cache-Endpunkt. Beachten Sie, dass diese Werte nicht garantiert werden und keine SLA für diese Werte bereitgestellt. Die Werte sollten jedoch typischerweise erreicht werden. Führen Sie Auslastungstests für Ihre Anwendung durch, um die geeignete Cachegröße für Ihre Anwendung zu ermitteln.

<table>
  <tr>
    <th>Cachename</th>
    <th>Cachegröße</th>
    <th>GET/s (einfache GET-Aufrufe mit 1-KB-Werten)</th>
    <th>Bandbreite (MBit/s)</th>
  </tr>
  <tr>
    <td>C0</td>
    <td>250 MB</td>
    <td>610</td>
    <td>5</td>
  </tr>
  <tr>
    <td>C1</td>
    <td>1 GB</td>
    <td>12.200</td>
    <td>100</td>
  </tr>
  <tr>
    <td>C2</td>
    <td>2,5 GB</td>
    <td>24.300</td>
    <td>200</td>
  </tr>
  <tr>
    <td>C3</td>
    <td>6 GB</td>
    <td>48.875</td>
    <td>400</td>
  </tr>
  <tr>
    <td>C4</td>
    <td>13 GB</td>
    <td>61.350</td>
    <td>500</td>
  </tr>
  <tr>
    <td>C5</td>
    <td>26 GB</td>
    <td>112.275</td>
    <td>1.000</td>
  </tr>
  <tr>
    <td>C6</td>
    <td>53 GB</td>
    <td>153.219</td>
    <td>mehr als 1.000</td>
  </tr>
</table>

Anweisungen zum Herunterladen von Redis-Tools wie beispielsweise `redis-benchmark.exe` finden Sie im Abschnitt [Wie führe ich Redis-Befehle aus?](#cache-commands).

<a name="cache-region"></a>
## Ich welcher Region sollte ich meinen Cache platzieren?

Um eine optimale Leistung und die niedrigste Latenz zu erzielen, sollten Sie Ihren Azure Redis Cache in derselben Region platzieren wie Ihre Cacheclientanwendung.

<a name="cache-timeouts"></a>
## Warum kommt es zu Timeouts?

Timeouts treten auf dem Client auf, der mit Redis kommuniziert. In der Regel kommt es auf dem Redis-Server nicht zu Timeouts. Wenn ein Befehl an den Redis-Server gesendet wird, wird dieser in die Warteschlange eingereiht, bis der Redis-Server den Befehl auswählt und ausführt. Auf dem Client kann es bei diesem Vorgang zu einem Timeout kommen. In diesem Fall wird auf der aufrufenden Seite eine Ausnahme ausgelöst. Weitere Informationen zur Behandlung von Timeoutproblemen finden Sie unter [Untersuchen von Timeoutausnahmen in "StackExchange.Redis" für Azure Redis Cache](http://azure.microsoft.com/blog/2015/02/10/investigating-timeout-exceptions-in-stackexchange-redis-for-azure-redis-cache/).

<a name="cache-monitor"></a>
## Wie überwache ich die Integrität und Leistung meines Caches?

Microsoft Azure Redis Cache-Instanzen können im [Azure-Vorschauportal](https://portal.azure.com) überwacht werden. Sie können Metriken anzeigen, Metrikdiagramme an das Startmenü anheften, Daten- und Zeitbereiche für Überwachungsdiagramme anpassen, Metriken aus Diagrammen hinzufügen und entfernen sowie Warnungen festlegen, die ausgelöst werden, wenn bestimmte Bedingungen erfüllt sind. Diese Tools ermöglichen es Ihnen, die Integrität Ihrer Azure Redis Cache-Instanzen zu überwachen und unterstützen Sie beim Verwalten Ihrer Cachinganwendungen. Weitere Informationen zum Überwachen Ihrer Caches finden Sie unter [Überwachen von Azure Redis Cache](https://msdn.microsoft.com/library/azure/dn763945.aspx).

<a name="cache-disconnect"></a>
## Warum wurde mein Client vom Cache getrennt?

Nachfolgend werden einige Gründe dafür aufgeführt, warum ein Cache getrennt wird.

-	Gründe auf Clientseite
	-	Der Client wurde neu bereitgestellt.
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

<table>
  <tr>
    <th>ConfigurationOptions</th>
    <th>Beschreibung</th>
    <th>Empfehlung</th>
  </tr>
  <tr>
    <td>AbortOnConnectFail</td>
    <td>Wenn Sie diese Option auf "true" setzen, wird die Verbindung nach einem Netzwerkausfall nicht wiederhergestellt.</td>
    <td>Bei Festlegung auf "false" kann "StackExchange.Redis" die Verbindung automatisch wiederherstellen.</td>
  </tr>
  <tr>
    <td>ConnectRetry</td>
    <td>Die Anzahl von Versuchen für die Verbindungsherstellung bei der anfänglichen Verbindung.</td>
    <td></td>
  </tr>
  <tr>
    <td>ConnectTimeout</td>
    <td>Timeout in Millisekunden für Verbindungsvorgänge.</td>
    <td></td>
  </tr>
</table>

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

<a name="cache-redis-commands"></a>
## Was muss bei der Verwendung gängiger Redis-Befehle beachtet werden?

-	Sie sollten bestimmte Redis-Befehle mit langer Ausführungszeit nur dann einsetzen, wenn Sie deren Auswirkung verstehen.
	-	Führen Sie beispielsweise den [KEYS](http://redis.io/commands/keys)-Befehl nicht in der Produktion aus, da die Rückgabe in Abhängigkeit von der Anzahl von Schlüsseln sehr viel Zeit in Anspruch nehmen kann. Redis ist ein Server mit Single-Threading, d. h. Befehle werden nacheinander verarbeitet. Wenn Sie nach KEYS weitere Befehle ausgeben, werden diese erst ausgeführt, wenn Redis den KEYS-Befehl verarbeitet hat.
-	Schlüsselgrößen – sollte ich kleine Schlüssel/Werte oder große Schlüssel/Werte verwenden? Dies hängt im Allgemeinen vom Szenario ab. Wenn Ihre Szenarien größere Schlüssel erfordern, können Sie die ConnectionTimeout- und retry-Werte sowie die Logik für erneute Verbindungsversuche anpassen. In Bezug auf den Redis-Server führen kleinere Werte zu einer besseren Leistung.
	-	Dies bedeutet jedoch nicht, dass Sie in Redis keine größeren Werte speichern können – Sie müssen sich lediglich der folgenden Punkte bewusst sein. Latenzen sind höher. Wenn Sie über einen größeren und einen kleineren Datensatz verfügen, können Sie mehrere ConnectionMultiplexer-Instanzen verwenden und jede mit einem anderen Satz an Werten für Timeout und erneute Verbindungsherstellung konfigurieren – wie im vorangegangenen Abschnitt [Was bewirken die StackExchange.Redis-Konfigurationsoptionen?](#cache-configuration) beschrieben.


<a name="cache-ssl"></a>
## Wann sollte ich den Nicht-SSL-Port für die Verbindungsherstellung mit Redis verwenden?

Der Redis-Server bietet keine integrierte SSL-Unterstützung, Azure Redis Cache hingegen schon. Wenn Sie eine Verbindung mit Azure Redis Cache herstellen und Ihr Client SSL unterstützt (z. B. StackExchange.Redis), dann sollten Sie SSL verwenden.

Beachten Sie, dass der Nicht-SSL-Port für neue Azure Redis Cache-Instanzen standardmäßig deaktiviert ist. Wenn Ihr Client keine SSL-Unterstützung bietet, müssen Sie den Nicht-SSL-Port mithilfe der Anweisungen im Abschnitt [Zugriffsports](https://msdn.microsoft.com/library/azure/dn793612.aspx#AccessPorts) des Artikels [Konfigurieren eines Caches in Azure Redis Cache](https://msdn.microsoft.com/library/azure/dn793612.aspx) aktivieren.

Redis-Tools wie z. B. `redis-cli` funktionieren nicht mit dem SSL-Port, aber Sie können ein Hilfsprogramm wie `stunnel` verwenden, um eine sichere Verbindung zwischen den Tools und dem SSL-Port herzustellen. Anweisungen hierzu finden Sie im Blogbeitrag [Announcing ASP.NET Session State Provider for Redis Preview Release](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

Anweisungen zum Herunterladen der Redis-Tools finden Sie im Abschnitt [Wie führe ich Redis-Befehle aus?](#cache-commands).

<a name="cache-benchmarking"></a>
## Wie kann ich die Leistung meines Caches messen und testen?

-	[Aktivieren Sie die Cachediagnose](https://msdn.microsoft.com/library/azure/dn763945.aspx#EnableDiagnostics), damit Sie die Integrität Ihres Caches [überwachen](https://msdn.microsoft.com/library/azure/dn763945.aspx) können. Sie können die Metriken im Portal anzeigen und sie anschließend mit einem Tool Ihrer Wahl [herunterladen und prüfen](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring).
-	Mit "redis-benchmark.exe" können Sie Auslastungstests für Ihren Redis-Server durchführen.
	-	Stellen Sie sicher, dass sich Client und Redis Cache für die Auslastungstests in derselben Region befinden.
-	Verwenden Sie "redis-cli.exe", und überwachen Sie den Cache unter Verwendung des INFO-Befehls.
	-	Wenn Ihre Datenlast zu einer hohen Arbeitsspeicherfragmentierung führt, sollte Sie eine zentrale Skalierung auf einen größeren Cache durchführen.
-	Anweisungen zum Herunterladen der Redis-Tools finden Sie im Abschnitt [Wie führe ich Redis-Befehle aus?](#cache-commands).

<a name="cache-commands"></a>
## Wie führe ich Redis-Befehle aus?

Sie können einen beliebigen der unter [Redis-Befehle](http://redis.io/commands#) aufgeführten Befehle verwenden. Zum Ausführen dieser Befehle können Sie die folgenden Tools verwenden.

-	Laden Sie die [Redis-Befehlszeilentools](https://github.com/MSOpenTech/redis/releases/download/win-2.8.19.1/redis-2.8.19.zip) herunter.
-	Stellen Sie über `redis-cli.exe` eine Verbindung mit dem Cache her. Übergeben Sie den Cacheendpunkt mithilfe des Schalters "-h" und dem Schlüssel mit Verwendung von "-a", wie im folgenden Beispiel gezeigt.
	-	`redis-cli -h <your cache name>.redis.cache.windows.net -a <key>`
-	Beachten Sie, dass die Redis-Befehlszeilentools nicht mit dem SSL-Port funktionieren, aber Sie können ein Hilfsprogramm wie `stunnel` verwenden, um eine sichere Verbindung zwischen den Tools und dem SSL-Port herzustellen. Anweisungen hierzu finden Sie im Blogbeitrag [Announcing ASP.NET Session State Provider for Redis Preview Release](http://blogs.msdn.com/b/webdev/archive/2014/05/12/announcing-asp-net-session-state-provider-for-redis-preview-release.aspx).

<a name="cache-common-patterns"></a>
## Welche gängigen Cachemuster gibt es, und was muss berücksichtigt werden?

-	Microsoft Patterns & Practices bietet die folgenden Anleitungen (in englischer Sprache):
	-	[Anleitung zum Caching](https://github.com/mspnp/azure-guidance/blob/master/Caching.md).
	-	[Anleitung zu Design und Implementierung von Azure-Cloudanwendungen](https://github.com/mspnp/azure-guidance)
-	[Allgemeine Cachemuster mit Azure Redis Cache](cache-howto-common-cache-patterns/)

<a name="cache-reference"></a>
## Warum gibt es für Azure Redis Cache keinen MSDN-Klassenbibliothekverweis wie für einige andere Azure-Dienste?

Microsoft Azure Redis Cache basiert auf dem populären quelloffenen Redis Cache und bietet Ihnen Zugriff auf einen sicheren, dedizierten Redis Cache, der von Microsoft verwaltet wird. Es stehen verschiedene [Redis-Clients](http://redis.io/clients) für zahlreiche Programmiersprachen zur Verfügung. Jeder Client verfügt über eine eigene API, die unter Verwendung von [Redis-Befehlen](http://redis.io/commands) Aufrufe an den Redis Cache sendet.

Da jeder Client anders ist, gibt es nicht nur einen zentralen Klassenverweis in MSDN. Stattdessen verwaltet jeder Client seine eigene Verweisdokumentation. Zusätzlich zur Verweisdokumentation gibt es mehrere Lernprogramme auf "Azure.com". Diese stehen auf der Seite [Redis Cache-Dokumentation](http://azure.microsoft.com/documentatgion/services/redis-cache/) zur Verfügung und erleichtern Ihnen den Einstieg in die Verwendung von Azure Redis Cache mit verschiedenen Sprachen und Cacheclients.

<!---HONumber=July15_HO1-->