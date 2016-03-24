<properties 
	pageTitle="Gewusst wie: Konfigurieren von Azure Redis Cache"
	description="Grundlagen der Redis-Standardkonfiguration für Azure Redis Cache und Informationen zur Konfiguration Ihrer Azure Redis Cache-Instanzen"
	services="redis-cache"
	documentationCenter="na"
	authors="steved0x"
	manager="erikre"
	editor="tysonn" />
<tags 
	ms.service="cache"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="cache-redis"
	ms.workload="tbd"
	ms.date="03/04/2016"
	ms.author="sdanie" />

# Gewusst wie: Konfigurieren von Azure Redis Cache

In diesem Thema wird beschrieben, wie Sie die Konfiguration für Ihre Azure Redis Cache-Instanzen überprüfen und aktualisieren. Außerdem wird die standardmäßige Redis-Serverkonfiguration für Azure Redis Cache-Instanzen beschrieben.

>[AZURE.NOTE] Weitere Informationen zur Konfiguration und Verwendung von Premium-Cache-Features finden Sie unter [Konfigurieren von Persistenz für Azure Redis Cache vom Typ „Premium“](cache-how-to-premium-persistence.md), [Konfigurieren von Clustern für Azure Redis Cache vom Typ „Premium“](cache-how-to-premium-clustering.md) und [Konfigurieren der Unterstützung virtueller Netzwerke für Azure Redis Cache vom Typ „Premium“](cache-how-to-premium-vnet.md).

## Konfigurieren von Redis Cache-Einstellungen

Sie können im [Azure-Portal](https://portal.azure.com) über das Blatt **Durchsuchen** auf Caches zugreifen.

![Azure Redis Cache: Blatt „Durchsuchen“](./media/cache-configure/IC796920.png)

Klicken Sie auf **Redis Caches**, um Ihre Caches anzuzeigen.

![Azure Redis Cache: Cacheliste durchsuchen](./media/cache-configure/IC796921.png)

Wählen Sie den gewünschten Cache aus, um die Eigenschaften für den Cache anzuzeigen.

![Redis Cache: Alle Einstellungen](./media/cache-configure/IC808312.png)

Klicken Sie auf **Einstellungen** oder **Alle Einstellungen**, um den Cache anzuzeigen und zu konfigurieren.

![Redis Cache: Einstellungen](./media/cache-configure/IC808313.png)

## Eigenschaften

Klicken Sie auf **Eigenschaften**, um Informationen zu Ihrem Cache anzuzeigen, z. B. den Endpunkt und die Ports des Caches.

![Redis Cache: Eigenschaften](./media/cache-configure/IC808314.png)

## Zugriffsschlüssel

Klicken Sie auf **Zugriffsschlüssel**, um die Zugriffsschlüssel für Ihren Cache anzuzeigen oder neu zu generieren. Diese Schlüssel werden von den Clients, die eine Verbindung mit Ihrem Cache herstellen, zusammen mit dem Hostnamen und den Ports aus dem Blatt **Eigenschaften** verwendet.

![Redis Cache: Zugriffsschlüssel](./media/cache-configure/IC808315.png)

## Zugriffsports

Der Zugriff ohne SSL ist für neue Caches standardmäßig deaktiviert. Klicken Sie zum Aktivieren des Nicht-SSL-Ports auf das Blatt **Zugriffsports** und dann auf **Nein**.

![Redis Cache: Zugriffsports](./media/cache-configure/IC808316.png)

## Tarif

Klicken Sie auf **Tarif**, um den Tarif für Ihren Cache anzuzeigen oder zu ändern. Weitere Informationen zur Skalierung finden Sie unter [Skalieren von Azure Redis Cache](cache-how-to-scale.md).

![Redis-Cache: Tarif](./media/cache-configure/pricing-tier.png)

## Diagnose

Klicken Sie auf **Diagnose**, um das Speicherkonto zu konfigurieren, das zum Speichern von Cachediagnosedaten verwendet wird.

![Redis Cache: Diagnose](./media/cache-configure/IC808317.png)

Weitere Informationen finden Sie unter [Überwachen von Azure Redis Cache](cache-how-to-monitor.md).

## maxmemory-policy und maxmemory-reserved

Klicken Sie auf **Maxmemory-Richtlinie**, um die Arbeitsspeicherrichtlinien für den Cache zu konfigurieren. Mit der Einstellung **maxmemory-policy** wird die Entfernungsrichtlinie für den Cache konfiguriert, und mit **maxmemory-reserved** wird der Arbeitsspeicher konfiguriert, der für andere Prozesse als Cacheprozesse reserviert ist.

![Redis Cache: Maxmemory-Richtlinie](./media/cache-configure/IC808318.png)

Unter **Maxmemory-Richtlinie** können Sie unter den folgenden Entfernungsrichtlinien wählen:

-	volatile-lru (Standardeinstellung)
-	allkeys-lru
-	volatile-random
-	allkeys-random
-	volatile-ttl
-	noeviction

Weitere Informationen zu Maxmemory-Richtlinien finden Sie unter [Entfernungsrichtlinien](http://redis.io/topics/lru-cache#eviction-policies).

Mit der Einstellung **maxmemory-reserved** wird die Arbeitsspeichermenge in MB konfiguriert, die für andere Prozesse als Cacheprozesse reserviert ist, z. B. die Replikation während eines Failovers. Sie kann auch verwendet werden, wenn Sie über ein hohes Fragmentierungsverhältnis verfügen. Mit dem Festlegen dieses Werts können Sie dafür sorgen, dass Sie bei wechselnden Auslastungen eine konsistentere Redis-Servererfahrung erzielen. Dieser Wert sollte für Workloads, die mit hohem Schreibaufwand verbunden sind, höher gewählt werden. Wenn Arbeitsspeicher für Vorgänge dieser Art reserviert ist, ist er nicht für die Speicherung zwischengespeicherter Daten verfügbar.

>[AZURE.IMPORTANT] Die Einstellung **maxmemory-reserved** ist nur für Standard- und Premium-Caches verfügbar.

## Keyspacebenachrichtigungen (Erweiterte Einstellungen)

Klicken Sie auf **Erweiterte Einstellungen**, um Redis-Keyspacebenachrichtigungen zu konfigurieren. Mit Keyspacebenachrichtigungen können Clients Benachrichtigungen empfangen, wenn bestimmte Ereignisse eintreten.

![Redis Cache: Erweiterte Einstellungen](./media/cache-configure/IC808319.png)

>[AZURE.IMPORTANT] Keyspacebenachrichtigungen und die Einstellung **notify-keyspace-events** sind nur für Caches vom Typ "Standard" und "Premium" verfügbar.

Weitere Informationen finden Sie unter [Redis-Keyspacebenachrichtigungen](http://redis.io/topics/notifications). Beispielcode finden Sie in der Datei [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) im [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld)-Beispiel.

## Redis-Datenpersistenz

Klicken Sie auf **Redis-Datenpersistenz** zum Aktivieren, Deaktivieren oder Konfigurieren der Datenpersistenz für den Premium-Cache konfigurieren.

![Redis-Datenpersistenz](./media/cache-configure/redis-cache-persistence-settings.png)

Klicken Sie auf **Aktiviert**, um die Redis-Datenbanksicherung zu aktivieren. Klicken Sie auf **Deaktiviert**, um die Redis-Persistenz zu deaktivieren.

Zum Konfigurieren des Sicherungsintervalls, wählen Sie eine **Sicherungshäufigkeit** aus der Dropdownliste. Zur Auswahl stehen **15 Minuten**, **30 Minuten**, **60 Minuten**, **6 Stunden**, **12 Stunden** und **24 Stunden**. Dieses Intervall wird ab dem Moment rückwärts gezählt, an dem der vorherige Sicherungsvorgang erfolgreich abgeschlossen wird. Wenn das Intervall abgelaufen ist, wird eine neue Sicherung gestartet.

Klicken Sie auf **Speicherkonto**, um das Speicherkonto auszuwählen, und wählen Sie entweder **Primärschlüssel** oder **Sekundärschlüssel** aus der Dropdownliste **Speicherschlüssel** aus. Sie müssen ein Speicherkonto auswählen, das aus der gleichen Region wie der Cache stammt, und wir empfehlen ein **Storage Premium**-Konto, da dieses einen höheren Durchsatz aufweist. Wenn der Speicherschlüssel für Ihr Persistenzkonto neu generiert wird, müssen Sie den gewünschten Schlüssel erneut aus der Dropdownliste **Speicherschlüssel** auswählen.

Klicken Sie auf **OK**, um die Persistenzkonfiguration zu speichern.

>[AZURE.IMPORTANT] Redis-Datenpersistenz ist nur für Premium-Caches verfügbar. Weitere Informationen finden Sie unter [Konfigurieren von Persistenz für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-persistence.md).

<a name="cluster-size"></a>
## Redis-Clustergröße

Klicken Sie zum Ändern der Clustergröße aus einem ausgeführten Premium-Cache mit aktivierter Clusterunterstützung auf **(PREVIEW) Redis Cluster Size**.

>[AZURE.NOTE] Beachten Sie, dass sich trotz allgemeiner Verfügbarkeit der Azure Redis Cache in der Premium-Stufe das Feature Redis-Clustergröße derzeit in der Vorschau befindet.

![Redis-Clustergröße](./media/cache-configure/redis-cache-redis-cluster-size.png)

Um die Clustergröße zu ändern, verwenden Sie den Schieberegler oder geben Sie eine Zahl zwischen 1 und 10 im Textfeld **Shardanzahl** ein und klicken Sie zum Speichern auf **OK**.

>[AZURE.IMPORTANT] Redis-Clustering ist nur für Premium-Caches verfügbar. Weitere Informationen finden Sie unter [Konfigurieren von Clustern für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-clustering.md).


## Benutzer und Tags

![Redis Cache: Benutzer und Tags](./media/cache-configure/IC808320.png)

Der Abschnitt **Benutzer** bietet Unterstützung für die rollenbasierte Access Control (RBAC) über das Azure-Portal, damit Organisationen ihre Zugriffsverwaltungsanforderungen einfach und präzise erfüllen können. Weitere Informationen finden Sie unter [Rollenbasierte Zugriffssteuerung im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=512803).

Im Abschnitt **Tags** können Sie Ihre Ressourcen organisieren. Weitere Informationen finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](../resource-group-using-tags.md).

## Standardmäßige Redis-Serverkonfiguration

Neue Azure Redis Cache-Instanzen werden mit den folgenden standardmäßigen Redis-Konfigurationswerten konfiguriert.

>[AZURE.NOTE] Die Einstellungen in diesem Abschnitt können mit der `StackExchange.Redis.IServer.ConfigSet`-Methode nicht geändert werden. Wenn diese Methode mit einem der Befehle in diesem Abschnitt aufgerufen wird, wird eine Ausnahme ausgelöst, die in etwa wie folgt lautet:
>
>`StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
>  
>Alle Werte, die konfigurierbar sind, z. B. **max-memory-policy**, können über das Azure-Portal oder Befehlszeilenverwaltungstools wie die Azure-Befehlszeilenschnittstelle oder PowerShell konfiguriert werden.

|Einstellung|Standardwert|Beschreibung|
|---|---|---|
|Datenbanken|16|Die Standarddatenbank ist DB 0. Sie können für jede Verbindung eine andere Datenbank auswählen, indem Sie „connection.GetDataBase(dbid)“ verwenden. Hierbei steht „dbid“ für eine Zahl zwischen 0 und 15.|
|maxclients|Abhängig vom Tarif<sup>1</sup>|Dies ist die maximale Anzahl von verbundenen Clients, die gleichzeitig zulässig sind. Sobald der Grenzwert erreicht ist, schließt Redis alle neuen Verbindungen und sendet den Fehler „max number of clients reached“ (Maximale Anzahl von Clients erreicht).|
|maxmemory-policy|volatile-lru|Mit der Einstellung „maxmemory-policy“ wählt Redis aus, was entfernt werden soll, wenn der Wert für „maxmemory“ (Größe des Cacheangebots, die Sie beim Erstellen des Caches ausgewählt haben) erreicht ist. Bei Azure Redis Cache lautet die Standardeinstellung „volatile-lru“. Hierbei werden die Schlüssel anhand eines Ablaufverfahrens mit LRU-Algorithmus entfernt. Diese Einstellung kann im Azure-Portal konfiguriert werden. Weitere Informationen finden Sie unter [maxmemory-policy und maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved).|
|maxmemory-samples|3|LRU- und minimale TTL-Algorithmen sind keine präzisen Algorithmen, sondern angenäherte Algorithmen (um Arbeitsspeicher zu sparen). Sie können also auch die Größe der zu prüfenden Stichproben auswählen. Standardmäßig werden von Redis beispielsweise drei Schlüssel geprüft, und es wird der Schlüssel ausgewählt, der vor längerer Zeit verwendet wurde.|
|lua-time-limit|5\.000|Maximale Ausführungszeit eines Lua-Skripts in Millisekunden. Wenn die maximale Ausführungszeit erreicht wird, wird von Redis protokolliert, dass ein Skript nach der maximal zulässigen Ausführungszeit weiterhin ausgeführt wird. Es wird dann damit begonnen, auf Abfragen mit einem Fehler zu antworten.|
|lua-event-limit|500|Dies ist die maximale Größe der Skriptereigniswarteschlange.|
|client-output-buffer-limit normalclient-output-buffer-limit pubsub|0 0 032mb 8mb 60|Die Clientausgabepuffer-Grenzwerte können verwendet werden, um die Verbindungstrennung für Clients zu erzwingen, die aus einem bestimmten Grund Daten nicht schnell genug vom Server lesen. (Ein häufiger Grund ist, dass ein Pub/Sub-Client Nachrichten nicht so schnell verarbeiten kann, wie sie von der veröffentlichenden Stelle produziert werden.) Weitere Informationen finden Sie unter [http://redis.io/topics/clients](http://redis.io/topics/clients).|

<sup>1</sup>`maxclients` unterscheidet sich für jeden Azure Redis Cache-Tarif.

-	Caches vom Typ "Basic" und "Standard"
	-	Cache C0 (250 MB) – bis zu 256 Verbindungen
	-	Cache C1 (1 GB) – bis zu 1.000 Verbindungen
	-	Cache C2 (2,5 GB) – bis zu 2.000 Verbindungen
	-	Cache C3 (6 GB) – bis zu 5.000 Verbindungen
	-	Cache C4 (13 GB) – bis zu 10.000 Verbindungen
	-	Cache C5 (26 GB) – bis zu 15.000 Verbindungen
	-	Cache C6 (53 GB) – bis zu 20.000 Verbindungen
-	Premium-Caches
	-	P1 (6 GB - 60 GB) – bis zu 7.500 Verbindungen
	-	P2 (13 GB - 130 GB) – bis zu 15.000 Verbindungen
	-	P3 (26 GB - 260 GB) – bis zu 30.000 Verbindungen
	-	P4 (53 GB - 530 GB) – bis zu 40.000 Verbindungen

## Redis-Befehle, die in Azure Redis Cache nicht unterstützt werden.

>[AZURE.IMPORTANT] Da die Konfiguration und Verwaltung von Azure Redis Cache-Instanzen von Microsoft verwaltet wird, sind die folgenden Befehle deaktiviert. Wenn Sie versuchen, sie aufzurufen, erhalten Sie etwa folgende Fehlermeldung: `"(error) ERR unknown command"`.
>
>-	BGREWRITEAOF
>-	BGSAVE
>-	CONFIG
>-	DEBUG
>-	MIGRATE
>-	SAVE
>-	SHUTDOWN
>-	SLAVEOF

Weitere Informationen zu Redis-Befehlen finden Sie unter [http://redis.io/commands](http://redis.io/commands).

## Redis-Konsole

Über die **Redis-Konsole**, die für Caches vom Typ "Standard" und "Premium" zur Verfügung steht, können Sie Befehle sicher auf Ihre Azure Redis Cache-Instanzen anwenden.

>[AZURE.IMPORTANT] Die Redis-Konsole kann nicht mit VNET oder Clustering verwendet werden.
>
>-	[VNET](cache-how-to-premium-vnet.md): Wenn der Cache Teil eines VNET ist, haben nur Clients im VNET Zugriff auf den Cache. Da die Redis-Konsole den Client "Redis-cli.exe" verwendet, der auf virtuellen Computern gehostet wird, die nicht Teil des VNET sind, kann sie keine Verbindung mit dem Cache herstellen.
>-	[Clustering](cache-how-to-premium-clustering.md): Die Redis-Konsole verwendet den Client "Redis-cli.exe", der Clustering derzeit nicht unterstützt. Das Hilfsprogramm redis-cli in der [unstable](http://redis.io/download)-Verzweigung des Redis-Repositorys auf GitHub implementiert grundlegende Unterstützung, wenn es mit dem Switch `-c` gestartet wird. Weitere Informationen finden Sie im [Redis Cluster Tutorial](http://redis.io/topics/cluster-tutorial) unter [Playing with the Cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) (in englischer Sprache) auf [http://redis.io](http://redis.io).

Um auf die Redis-Konsole zuzugreifen, klicken Sie auf dem Blatt **Redis-Cache** auf **Konsole**.

![Redis-Konsole](./media/cache-configure/redis-console-menu.png)

Zum Anwenden von Befehlen auf Ihre Cache-Instanz geben Sie einfach den gewünschten Befehl in die Konsole ein.

![Redis-Konsole](./media/cache-configure/redis-console.png)

Eine Liste der Redis-Befehle, die für Azure Redis Cache deaktiviert sind, finden Sie im vorherigen Abschnitt [Redis-Befehle, die in Azure Redis Cache nicht unterstützt werden](#redis-commands-not-supported-in-azure-redis-cache). Weitere Informationen zu Redis-Befehlen finden Sie unter [http://redis.io/commands](http://redis.io/commands).

## Nächste Schritte
-	Weitere Informationen zum Verwenden von Redis-Befehlen finden Sie unter [Wie führe ich Redis-Befehle aus?](cache-faq.md#how-can-i-run-redis-commands).

<!---HONumber=AcomDC_0309_2016-->