<properties 
	pageTitle="Überwachen von Azure Redis Cache" 
	description="Erfahren Sie, wie Sie die Stabilität und Integrität Ihrer Azure Redis Cache-Instanzen überwachen." 
	services="redis-cache" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="cache" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="cache-redis" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="sdanie"/>

# Überwachen von Azure Redis Cache

Azure Redis Cache bietet verschiedene Optionen zum Überwachen Ihrer Cache-Instanzen. Sie können Metriken anzeigen, Metrikdiagramme an das Startmenü anheften, Daten- und Zeitbereiche für Überwachungsdiagramme anpassen, Metriken aus Diagrammen hinzufügen und entfernen sowie Warnungen festlegen, die ausgelöst werden, wenn bestimmte Bedingungen erfüllt sind. Diese Tools ermöglichen es Ihnen, die Integrität Ihrer Azure Redis Cache-Instanzen zu überwachen und unterstützen Sie beim Verwalten Ihrer Cachinganwendungen.

Wenn die Cachediagnose aktiviert ist, werden ca. alle 30 Sekunden Metriken für Azure Redis Cache-Instanzen gesammelt und gespeichert, sodass sie in den Metrikdiagrammen angezeigt und durch Warnungsregeln ausgewertet werden können.

Cachemetriken werden mithilfe des Redis-Befehls [INFO](http://redis.io/commands/info) gesammelt. Weitere Informationen zu den verschiedenen für die Cachemetriken verwendeten INFO-Befehlen finden Sie unter [Verfügbare Metriken und Berichtsintervalle](#available-metrics-and-reporting-intervals).

Zum Anzeigen von Cachemetriken klicken Sie auf [Durchsuchen](cache-configure.md) im [Azure-Portal](https://portal.azure.com), um zu Ihrer Cache-Instanz zu navigieren. Sie können auf dem Blatt **Redis Cache** auf Metriken für Azure Redis Cache-Instanzen zugreifen.

![Überwachen][redis-cache-monitor-overview]

>[AZURE.IMPORTANT] Wenn im Azure-Portal die folgende Meldung angezeigt wird, befolgen Sie die Anweisungen im Abschnitt [Aktivieren der Cachediagnose](#enable-cache-diagnostics), um die Cachediagnose zu aktivieren.
>
>`Monitoring may not be enabled. Click here to turn on Diagnostics.`

Das Blatt **Redis Cache** bietet Diagramme für die **Überwachung** und die **Nutzung**, in denen Cachemetriken angezeigt werden. Sie können jedes Diagramm benutzerdefiniert anpassen, indem Sie Metriken hinzufügen oder entfernen oder das Berichtsintervall ändern. Das Blatt **Redis Cache** verfügt auch über einen Abschnitt **Vorgänge**, in dem **Ereignisse** und **Warnungsregeln** für den Cache angezeigt werden.

## Aktivieren der Cachediagnose

Azure Redis Cache ermöglicht Ihnen das Speichern von Diagnosedaten in einem Speicherkonto, sodass Sie mit einem Tool Ihrer Wahl auf die Daten zugreifen und diese direkt verarbeiten können. Damit Cachediagnosedaten gesammelt, gespeichert und im Azure-Portal angezeigt werden können, muss ein Speicherkonto konfiguriert werden. Caches innerhalb derselben Region und desselben Abonnements nutzen ein gemeinsames Speicherkonto für Diagnosedaten. Wenn die Konfiguration dieses Kontos geändert wird, gilt diese Änderung für alle Caches dieses Abonnements in dieser Region.

Um die Cachediagnose zu aktivieren und zu konfigurieren, navigieren Sie zum Blatt **Redis Cache** für Ihre Cache-Instanz. Wenn die Diagnose noch nicht aktiviert ist, wird eine Meldung anstelle eines Diagnosediagramms angezeigt.

![Aktivieren der Cachediagnose][redis-cache-enable-diagnostics]

Klicken Sie auf ein Überwachungsdiagramm, z. B. **Treffer und Fehler**, um das Blatt **Metrik** anzuzeigen, und klicken Sie auf **Diagnoseeinstellungen**, um die Diagnoseeinstellungen für die Cachedienstinstanz zu aktivieren und zu konfigurieren.

![Diagnoseeinstellungen][redis-cache-diagnostic-settings]

![Konfigurieren der Diagnose][redis-cache-configure-diagnostics]

Klicken Sie auf die Schaltfläche **Ein**, um die Cachediagnose zu aktivieren und die Diagnosekonfiguration anzuzeigen.

Klicken Sie auf den Pfeil rechts neben **Speicherkonto**, um ein Speicherkonto auszuwählen, in dem die Diagnosedaten gespeichert werden sollen. Um eine optimale Leistung zu erzielen, wählen Sie ein Speicherkonto in der gleichen Region aus, in der sich auch Ihr Cache befindet.

Wenn die Diagnoseeinstellungen konfiguriert sind, klicken Sie auf **Speichern**, um die Konfiguration zu speichern. Beachten Sie, dass es einen Moment dauern kann, bis die Änderungen wirksam werden.

>[AZURE.IMPORTANT] Caches innerhalb derselben Region und desselben Abonnements nutzen ein gemeinsames Speicherkonto für Diagnosedaten. Wenn die Konfiguration dieses Kontos geändert wird, gilt diese Änderung für alle Caches dieses Abonnements in dieser Region.

Um die gespeicherten Metriken anzuzeigen, untersuchen Sie die Tabellen in Ihrem Speicherkonto, deren Namen mit `WADMetrics` beginnen. Weitere Informationen zum Zugriff auf die gespeicherten Metriken außerhalb des Azure-Portals finden Sie im Beispiel [Access Redis Cache Monitoring data](https://github.com/rustd/RedisSamples/tree/master/CustomMonitoring) (in englischer Sprache).

>[AZURE.NOTE] Im Azure-Portal werden nur Metriken angezeigt, die im ausgewählten Speicherkonto gespeichert sind. Wenn Sie das Speicherkonto ändern, sind die Daten im zuvor konfigurierten Speicherkonto weiterhin verfügbar, werden im Azure-Portal jedoch nicht angezeigt.

## Verfügbare Metriken und Berichtsintervalle

Cachemetriken werden in verschiedenen Berichtsintervallen gemeldet, einschließlich **Letzte Stunde**, **Heute**, **Letzte Woche** und**Benutzerdefiniert**. Das Blatt **Metrik** für jedes Metrikdiagramm zeigt die Durchschnitts-, Minimal- und Maximalwerte für jede Metrik im Diagramm an. Einige Metriken zeigen einen Gesamtwert für das Berichtsintervall an.

Jede Metrik umfasst zwei Versionen. Eine Metrik misst die Leistung für den gesamten Cache. Eine zweite Version der Metrik, deren Name `(Shard 0-9)` enthält, misst für Caches, die [Clustering](cache-how-to-premium-clustering.md) verwenden, die Leistung für einen einzelnen Shard in einem Cache. Wenn z. B. ein Cache 4 Shards enthält, ist `Cache Hits` der Gesamtbetrag der Treffer für den gesamten Cache, und `Cache Hits (Shard 3)` gibt lediglich die Treffer für dieses Shard des Caches an.

>[AZURE.NOTE] Selbst wenn keine der mit dem Cache verbundenen Clientanwendungen aktiv ist, wird möglicherweise Cacheaktivität angezeigt, wie z. B. verbundene Clients, Arbeitsspeicherauslastung und Vorgänge, die gerade ausgeführt werden. Diese Aktivität ist beim Betrieb einer Azure Redis Cache-Instanz normal.

| Metrik | Beschreibung |
|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Cachetreffer | Die Anzahl der erfolgreichen Schlüsselsuchen während des angegebenen Berichtsintervalls. Dieser Wert ist dem Redis-INFO-Befehl `keyspace_hits command` zugeordnet. |
| Cachefehler | Die Anzahl der fehlerhaften Schlüsselsuchen während des angegebenen Berichtsintervalls. Dieser Wert ist dem Redis-INFO-Befehl `keyspace_misses` zugeordnet. Cachefehler bedeuten nicht unbedingt, dass ein Problem mit dem Cache vorhanden ist. Bei Verwendung des cachefremden Programmierschemas sucht eine Anwendung zuerst im Cache nach einem Element. Wenn das gesuchte Element nicht vorhanden ist (Cachefehler), wird es aus der Datenbank abgerufen und dem Cache für das nächste Mal hinzugefügt. Cachefehler sind ein normales Verhalten für das cachefremde Programmierschema. Wenn die Anzahl der Cachefehler höher ist als erwartet, untersuchen Sie die Anwendungslogik, die den Cache auffüllt und aus dem Cache liest. Wenn Elemente aufgrund von ungenügendem Arbeitsspeicher aus dem Cache entfernt werden, können Cachefehler auftreten. Zur Überwachung der Arbeitsspeicherauslastung eignen sich jedoch die Metriken `Used Memory` oder `Evicted Keys` besser. |
| Verbundene Clients | Die Anzahl von Clientverbindungen mit dem Cache während des angegebenen Berichtsintervalls. Dieser Wert ist dem Redis-INFO-Befehl `connected_clients` zugeordnet. Der Grenzwert für verbundene Clients liegt bei 10.000. Wenn dieser Grenzwert erreicht ist, treten bei weiteren Verbindungsversuchen mit dem Cache Fehler auf. Beachten Sie: Auch wenn keine Clientanwendung aktiv ist, können aufgrund interner Prozesse und Verbindungen dennoch einige Instanzen verbundener Clients vorhanden sein. |
| Entfernte Schlüssel | Die Anzahl von Elementen, die aufgrund des `maxmemory`-Grenzwerts während des angegebenen Berichtsintervalls aus dem Cache entfernt wurden. Dieser Wert ist dem Redis-INFO-Befehl `evicted_keys` zugeordnet. |
| Abgelaufene Schlüssel | Die Anzahl von Elementen, die während des angegebenen Berichtsintervalls im Cache abgelaufen sind. Dieser Wert ist dem Redis-INFO-Befehl `expired_keys` zugeordnet. |
| get-Vorgänge | Die Anzahl von get-Vorgängen im Cache während des angegebenen Berichtsintervalls. Dieser Wert ist die Summe der folgenden Werte aus dem Redis-INFO-Befehl "all": `cmdstat_get`, `cmdstat_hget`, `cmdstat_hgetall`, `cmdstat_hmget`, `cmdstat_mget`, `cmdstat_getbit` und `cmdstat_getrange` und entspricht der Summe aller Cachetreffer und Cachefehler während des angegebenen Berichtsintervalls. |
| Redis-Serverlast | Der Prozentsatz der Zyklen, in denen der Redis-Server mit der Verarbeitung beschäftigt ist und nicht auf Nachrichten wartet. Wenn dieser Zähler den Wert 100 erreicht, bedeutet dies, dass der Redis-Server die Leistungsobergrenze erreicht hat und die CPU nicht schneller arbeiten kann. Wenn eine hohe Redis-Serverlast angezeigt wird, bedeutet dies auch Timeoutausnahmen auf dem Client. In diesem Fall sollten Sie erwägen, den Cache zentral hochzuskalieren oder Ihre Daten in mehrere Caches zu partitionieren. |
| set-Vorgänge | Die Anzahl von set-Vorgängen im Cache während des angegebenen Berichtsintervalls. Dieser Wert ist die Summe der folgenden Werte aus dem Redis-INFO-Befehl "all": `cmdstat_set`, `cmdstat_hset`, `cmdstat_hmset`, `cmdstat_hsetnx`, `cmdstat_lset`, `cmdstat_mset`, `cmdstat_msetnx`, `cmdstat_setbit`, `cmdstat_setex`, `cmdstat_setrange` und `cmdstat_setnx`. |
| Vorgänge gesamt | Die Gesamtzahl aller Befehle, die während des angegebenen Berichtsintervalls vom Cacheserver verarbeitet wurden. Dieser Wert ist dem Redis-INFO-Befehl `total_commands_processed` zugeordnet. Beachten Sie: Wenn Azure Redis Cache ausschließlich für pub/sub-Vorgänge verwendet wird, sind keine Metriken für `Cache Hits`, `Cache Misses`, `Gets` oder `Sets` vorhanden. Stattdessen sind `Total Operations`-Metriken vorhanden, die die Cachenutzung für pub/sub-Vorgänge widerspiegeln. |
| Verwendeter Arbeitsspeicher | Der Betrag von Cachearbeitsspeicher in MB, der während des angegebenen Berichtsintervalls für Schlüssel-Wert-Paare im Cache verwendet wird. Dieser Wert ist dem Redis-INFO-Befehl `used_memory` zugeordnet. Metadaten und Fragmentierung sind hierin nicht enthalten. |
| Verwendeter Arbeitsspeicher (RSS) | Die Menge an verwendetem Cachearbeitsspeicher in MB während des angegebenen Berichtsintervalls, einschließlich Fragmentierung und Metadaten. Dieser Wert ist dem Redis-INFO-Befehl `used_memory_rss` zugeordnet. |
| CPU | Die CPU-Auslastung des Azure Redis Cache-Servers in Prozent während des angegebenen Berichtsintervalls. Dieser Wert ist dem Betriebssystem-Leistungsindikator `\Processor(_Total)\% Processor Time` zugeordnet, |
| Cache-Lesevorgänge | Die Menge an Daten in KB/s, die während des angegebenen Berichtsintervalls im Cache gelesen wurden. Dieser Wert wird von den Netzwerkschnittstellenkarten für die virtuelle Maschine abgeleitet, die den Cache hostet, und ist nicht Redis-spezifisch. |
| Cache-Schreibvorgänge | Die Menge an Daten in KB/s, die während des angegebenen Berichtsintervalls in den Cache geschrieben wurden. Dieser Wert wird von den Netzwerkschnittstellenkarten für die virtuelle Maschine abgeleitet, die den Cache hostet, und ist nicht Redis-spezifisch. |

## Überwachungsdiagramme

Der Abschnitt **Überwachung** bietet die Diagramme **Treffer und Fehler**, **get- und set-Vorgänge**, **Verbindungen** und **Befehle gesamt**.

![Überwachungsdiagramme][redis-cache-monitoring-part]

Die Diagramme **Überwachung** zeigen folgende Metriken an:

| Überwachungsdiagramm | Cachemetriken |
|------------------|-------------------|
| Treffer und Fehler | Cachetreffer |
| | Cachefehler |
| get- und set-Vorgänge | get-Vorgänge |
| | set-Vorgänge |
| Verbindungen | Verbundene Clients |
| Befehle gesamt | Vorgänge gesamt |

Informationen zum Anzeigen der Metriken und zum Anpassen der einzelnen Diagramme in diesem Abschnitt finden Sie im Abschnitt [Anzeigen von Metriken und Anpassen von Metrikdiagrammen](#how-to-view-metrics-and-customize-charts).

## Nutzungsdiagramme

Der Abschnitt **Nutzung** umfasst die Diagramme **Redis-Serverlast**, **Speicherauslastung**, **Netzwerkbandbreite** und **CPU-Auslastung** und zeigt auch den **Tarif** für die Cache-Instanz an.

![Nutzungsdiagramme][redis-cache-usage-part]

Der Bereich **Tarif** zeigt den Tarif für den Cache an und kann zum [Skalieren](cache-how-to-scale.md) des Caches in einen anderen Tarif verwendet werden.

Die Diagramme **Nutzung** zeigen folgende Metriken an:

| Nutzungsdiagramm | Cachemetriken |
|-------------------|---------------|
| Redis-Serverlast | Serverlast |
| Speicherauslastung | Verwendeter Arbeitsspeicher |
| Netzwerkbandbreite | Cache-Schreibvorgänge |
| CPU-Auslastung | CPU |

Informationen zum Anzeigen der Metriken und zum Anpassen der einzelnen Diagramme in diesem Abschnitt finden Sie im Abschnitt [Anzeigen von Metriken und Anpassen von Metrikdiagrammen](#how-to-view-metrics-and-customize-charts).

## Anzeigen von Metriken und Anpassen von Metrikdiagrammen

Sie können eine Übersicht über die Metriken auf dem Blatt **Redis Cache** in den Diagrammen **Überwachung** und **Nutzung** anzeigen, wie in den vorherigen Abschnitten beschrieben. Um eine detailliertere Ansicht der Metriken in einem bestimmten Diagramm zu erhalten und das Diagramm anzupassen, klicken Sie auf dem Blatt **Redis Cache** auf das Diagramm. Dadurch wird das Blatt **Metrik** für dieses Diagramm angezeigt.

![Blatt "Metrik"][redis-cache-metric-blade]

Alle Warnungen, die für die von einem Diagramm angezeigten Metriken festgelegt wurden, werden unten auf dem Blatt **Metrik** für dieses Diagramm aufgelistet.

Um Metriken hinzufügen oder zu entfernen oder das Berichtsintervall zu ändern, klicken Sie mit der rechten Maustaste auf das Diagramm und wählen **Diagramm bearbeiten** aus. Sie können Diagramme auch direkt im Blatt **Redis Cache** bearbeiten, indem Sie mit der rechten Maustaste auf das gewünschte Diagramm klicken und **Diagramm bearbeiten** auswählen.

Um Metriken zum Diagramm hinzuzufügen oder daraus zu entfernen, klicken Sie auf das Kontrollkästchen neben dem Metriknamen. Um das Berichtsintervall zu ändern, klicken Sie auf das gewünschte Intervall. Um den **Diagrammtyp** zu ändern, klicken Sie auf den gewünschten Typ. Wenn Sie alle gewünschten Änderungen vorgenommen haben, klicken Sie auf **Speichern**.

![Bearbeiten eines Diagramms][redis-cache-edit-chart]

Wenn Sie auf **Speichern** klicken, bleiben Ihre Änderungen erhalten, bis Sie das Blatt **Metrik** verlassen. Wenn Sie später zurückkehren, werden wieder die ursprünglichen Metriken und der ursprüngliche Zeitbereich angezeigt. Weitere Informationen zum Anpassen von Diagrammen finden Sie unter [Anpassen der Überwachung](../azure-portal/insights-how-to-customize-monitoring.md).

Wenn Sie die Metriken für einen bestimmten Zeitraum in einem Diagramm anzeigen möchten, halten Sie den Mauszeiger über einen bestimmten Balken oder Punkt im Diagramm, der dem gewünschten Zeitpunkt entspricht, und die Metriken für dieses Intervall werden angezeigt.

![Anzeigen von Diagrammdetails][redis-cache-view-chart-details]

## Überwachen eines Premium-Caches mithilfe des Clusterings

Premium-Caches, für die [Clustering](cache-how-to-premium-clustering.md) aktiviert ist, lassen bis zu 10 Shards zu. Jeder Shard hat eine eigene Metriken, und diese Metriken werden aggregiert, um Metriken für den Cache als Ganzes zu bieten. Jede Metrik umfasst zwei Versionen. Eine Metrik misst die Leistung des gesamten Caches. Eine zweite Version der Metrik, deren Name `(Shard 0-9)` enthält, misst die Leistung eines einzelnen Shards in einem Cache. Wenn z. B. ein Cache 3 Shards enthält, ist `Cache Hits` die Gesamtmenge der Treffer im gesamten Cache, und `Cache Hits (Shard 2)` gibt lediglich die Treffer für den jeweiligen Shard des Caches an.

In jedem Überwachungsdiagramm werden die Metriken auf oberster Ebene für den Cache sowie die Metriken für jeden Shard im Cache angezeigt.

![Überwachen][redis-cache-premium-monitor]

Bei Bewegen der Maus über die Datenpunkte werden die Details für den jeweiligen Zeitpunkt angezeigt.

![Überwachen][redis-cache-premium-point-summary]

Größere Werte sind in der Regel die Aggregatwerte für den Cache, während die kleineren Werte die einzelnen Metriken für den Shard darstellen. Beachten Sie, dass es bei diesem Beispiel drei Shards gibt und dass die Cachetreffer gleichmäßig auf die Shards verteilt sind.

![Überwachen][redis-cache-premium-point-shard]

Zum Anzeigen weiterer Details klicken Sie auf das Diagramm, um auf dem Blatt **Metrik** eine erweiterte Ansicht einzublenden.

![Überwachen][redis-cache-premium-chart-detail]

Standardmäßig zeigt jedes Diagramm den Cacheleistungsindikator der obersten Ebene sowie die Leistungsindikatoren für die einzelnen Shards. Sie können diese auf dem Blatt **Diagramm bearbeiten** anpassen.

![Überwachen][redis-cache-premium-edit]

Weitere Informationen zu den verfügbaren Leistungsindikatoren finden Sie unter [Verfügbare Metriken und Berichtsintervalle](#available-metrics-and-reporting-intervals).


## Vorgänge und Warnungen

Der Abschnitt **Vorgänge** enthält die Abschnitte **Ereignisse** und **Warnungsregeln**.

![Vorgänge][redis-cache-operations-events]

Zum Anzeigen einer Liste der neuesten Cachevorgänge klicken Sie auf das Diagramm **Ereignisse**, um das Blatt **Ereignisse** zu öffnen. Vorgänge umfassen beispielsweise das Abrufen und Neugenerieren von Zugriffsschlüsseln sowie die Aktivierung und Auflösung von Warnungsregeln. Um weitere Informationen zu jedem Ereignis zu erhalten, klicken Sie auf dem Blatt **Ereignisse** auf das betreffende Ereignis.

Weitere Informationen zu Ereignissen finden Sie unter [Anzeigen von Ereignissen und Überwachungsprotokollen](../azure-portal/insights-debugging-with-events.md).

Der Abschnitt **Warnungsregeln** zeigt die Anzahl der Warnungen für die Cache-Instanz an. Mit einer Warnungsregel können Sie Ihre Cache-Instanz überwachen und eine E-Mail-Nachricht erhalten, wenn ein bestimmter Metrikwert den in der Regel definierten Schwellenwert erreicht hat.

Warnungsregeln werden ca. alle fünf Minuten ausgewertet, und wenn eine Warnungsregel aktiviert ist, werden die entsprechenden konfigurierten Nachrichten gesendet. Aktivierungen und Benachrichtigungen in Zusammenhang mit Warnungsregeln werden nicht sofort verarbeitet; es kann zu einer Verzögerung von mehreren Minuten kommen, bevor eine Warnungsregel aktiviert wird und Benachrichtigungen gesendet werden.

Sie können Warnungsregeln auf dem Blatt **Metrik** für ein bestimmtes Überwachungsdiagramm oder auf dem Blatt **Warnungsregeln** anzeigen und festlegen.

Warnungsregeln verfügen über folgenden Eigenschaften:

| Eigenschaft der Warnungsregel | Beschreibung |
|-------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Ressource | Die von der Warnungsregel ausgewertete Ressource. Wenn eine Warnungsregel für einen Redis-Cache erstellt wird, ist der Cache die Ressource. |
| Name | Der Name, der die Warnungsregel innerhalb der aktuellen Cache-Instanz eindeutig identifiziert. |
| Beschreibung | Eine optionale Beschreibung der Warnungsregel. |
| Metrik | Die Metrik, die durch die Warnungsregel überwacht werden soll. Eine Liste der Cachemetriken finden Sie unter "Verfügbare Metriken und Berichtsintervalle". |
| Bedingung | Der Bedingungsoperator für die Warnungsregel. Mögliche Optionen: größer als, größer oder gleich, kleiner als, kleiner oder gleich. |
| Schwellenwert | Der Wert, mit dem die Metrik verglichen wird, unter Verwendung des durch die Bedingungseigenschaft angegebenen Operators. Je nach Metrik kann es sich bei diesem Wert um Bytes/Sekunde, Bytes, % oder einen Zähler handeln. |
| Zeitraum | Gibt den Zeitraum an, während dessen der Durchschnittswert der Metrik für den Warnungsregelvergleich verwendet wird. Wenn der Zeitraum z. B. "Während der letzten Stunde" lautet, wird der Durchschnittswert der Metrik während der letzten Stunde für den Vergleich verwendet. Wenn Sie eine Benachrichtigung erhalten möchten, sobald der Schwellenwert aufgrund einer Aktivitätsspitze erreicht wird, sollte ein kürzerer Zeitraum angegeben werden. Wenn Sie benachrichtigt werden möchten, wenn dauerhaft Aktivität oberhalb des Schwellenwerts vorhanden ist, geben Sie einen längeren Zeitraum an. |
| E-Mail an Dienst- und Co-Administrator | Wenn diese Einstellung auf "true" festgelegt ist, erhalten der Dienstadministrator und der Co-Administrator bei Aktivierung der Warnung eine E-Mail. |
| E-Mail an weiteren Administrator | Optionale E-Mail-Adresse für einen zusätzlichen Administrator, der bei Aktivierung der Warnung benachrichtigt werden soll. |

Pro Aktivierung der Warnungsregel wird nur eine Benachrichtigung gesendet. Sobald der Schwellenwert für eine Regel überschritten und eine Benachrichtigung gesendet wurde, wird die Regel erst dann erneut ausgewertet, wenn die Metrik den Schwellenwert unterschreitet. Wenn die Metrik danach den Schwellenwert wieder überschreitet, wird die Warnung erneut aktiviert und es wird eine neue Benachrichtigung gesendet.

Um alle Warnungsregeln für Ihre Cache-Instanz anzuzeigen, klicken Sie auf das Detail **Warnungsregeln** auf dem Blatt **Redis Cache**. Um nur die Warnungsregeln anzuzeigen, die eine bestimmte Metrik verwenden, navigieren Sie zum Blatt **Metrik** für das Diagramm, das diese Metrik enthält.

![Warnungsregeln][redis-cache-alert-rules]

Um eine Warnungsregel hinzuzufügen, klicken Sie entweder auf dem Blatt **Metrik** oder auf dem Blatt **Warnungsregeln** auf **Warnung hinzufügen**.

Geben Sie die gewünschten Regelkriterien auf dem Blatt **Warnung hinzufügen** ein, und klicken Sie auf **OK**.

![Hinzufügen einer Warnungsregel][redis-cache-add-alert]

>[AZURE.NOTE] Wenn Sie eine Warnungsregel durch Klicken auf **Warnung hinzufügen** auf dem Blatt **Metrik** erstellen, sind nur die Metriken, die auf dem Diagramm auf diesem Blatt angezeigt werden, in der Dropdownliste **Metrik** enthalten. Wenn Sie eine Warnungsregel durch Klicken auf **Warnung hinzufügen** auf dem Blatt **Warnungsregeln** hinzufügen, stehen alle Cachemetriken in der Dropdownliste **Metrik** zur Verfügung.

Sobald eine Warnungsregel gespeichert wurde, wird sie sowohl auf dem Blatt **Warnungsregel** als auch auf dem Blatt **Metrik** für alle Diagramme angezeigt, die die in der Warnungsregel verwendete Metrik anzeigen. Klicken Sie zum Bearbeiten einer Warnungsregel auf die Regel, um das Blatt **Regel bearbeiten** zu öffnen. Auf dem Blatt **Regel bearbeiten** können Sie die Eigenschaften der Regel bearbeiten, die Regel löschen oder deaktivieren bzw. sie reaktivieren, falls sie zuvor deaktiviert war.

>[AZURE.NOTE] Es dauert einen Moment, bis die Änderungen, die Sie an den Eigenschaften der Regel vorgenommen haben, auf den Blättern **Warnungsregeln** oder **Metrik** widergespiegelt werden.

Wenn eine Warnungsregel aktiviert wird, wird je nach Konfiguration der Regel eine E-Mail gesendet, und es wird ein Warnsymbol im Detail **Warnungsregeln** auf dem Blatt **Redis Cache** angezeigt.

Eine Warnungsregel gilt als aufgelöst, wenn die Warnungsbedingung nicht mehr als "true" ausgewertet wird. Sobald die Warnungsregelbedingung aufgelöst ist, wird statt des Warnsymbols ein Häkchen angezeigt. Um Details zu Warnungsaktivierungen und -auflösungen zu erhalten, klicken Sie auf dem Blatt **Redis Cache** auf das Detail **Ereignisse**, um die Ereignisse auf dem Blatt **Ereignisse** anzuzeigen.

Weitere Informationen zu Warnungen in Azure finden Sie unter [Empfangen von Warnbenachrichtigungen](../azure-portal/insights-receive-alert-notifications.md).
  
<!-- IMAGES -->
[redis-cache-monitor-overview]: ./media/cache-how-to-monitor/redis-cache-monitor-overview.png

[redis-cache-enable-diagnostics]: ./media/cache-how-to-monitor/redis-cache-enable-diagnostics.png

[redis-cache-diagnostic-settings]: ./media/cache-how-to-monitor/redis-cache-diagnostic-settings.png

[redis-cache-configure-diagnostics]: ./media/cache-how-to-monitor/redis-cache-configure-diagnostics.png

[redis-cache-monitoring-part]: ./media/cache-how-to-monitor/redis-cache-monitoring-part.png

[redis-cache-usage-part]: ./media/cache-how-to-monitor/redis-cache-usage-part.png

[redis-cache-metric-blade]: ./media/cache-how-to-monitor/redis-cache-metric-blade.png

[redis-cache-edit-chart]: ./media/cache-how-to-monitor/redis-cache-edit-chart.png

[redis-cache-view-chart-details]: ./media/cache-how-to-monitor/redis-cache-view-chart-details.png

[redis-cache-operations-events]: ./media/cache-how-to-monitor/redis-cache-operations-events.png

[redis-cache-alert-rules]: ./media/cache-how-to-monitor/redis-cache-alert-rules.png

[redis-cache-add-alert]: ./media/cache-how-to-monitor/redis-cache-add-alert.png

[redis-cache-premium-monitor]: ./media/cache-how-to-monitor/redis-cache-premium-monitor.png

[redis-cache-premium-edit]: ./media/cache-how-to-monitor/redis-cache-premium-edit.png

[redis-cache-premium-chart-detail]: ./media/cache-how-to-monitor/redis-cache-premium-chart-detail.png

[redis-cache-premium-point-summary]: ./media/cache-how-to-monitor/redis-cache-premium-point-summary.png

[redis-cache-premium-point-shard]: ./media/cache-how-to-monitor/redis-cache-premium-point-shard.png

<!---HONumber=AcomDC_0309_2016-->