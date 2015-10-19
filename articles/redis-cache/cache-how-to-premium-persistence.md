<properties 
	pageTitle="Konfigurieren von Datenpersistenz für Azure Redis Cache vom Typ ";Premium";" 
	description="Erfahren Sie, wie Sie die Datenpersistenz für Ihren Premium Azure Redis Cache mit Premium-Tarif konfigurieren und verwalten." 
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
	ms.date="10/01/2015" 
	ms.author="sdanie"/>

# Konfigurieren von Datenpersistenz für Azure Redis Cache vom Typ "Premium"

Für Azure Redis Cache stehen verschiedene Cacheangebote bereit, die Flexibilität bei der Auswahl von Cachegröße und -funktionen bieten, einschließlich des neuen Premium-Tarifs, der sich derzeit in der Vorschauphase befindet.

Der Premium-Tarif für Azure Redis Cache umfasst Clustering, Persistenz und Unterstützung des virtuellen Netzwerks. In diesem Artikel wird erläutert, wie die Persistenz in einer Azure Redis Cache-Instanz vom Typ "Premium" konfiguriert wird.

Informationen zu anderen Funktionen des Premium-Caches finden Sie unter [Konfigurieren von Clustern für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-clustering.md) und [Konfigurieren der Unterstützung virtueller Netzwerke für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-vnet.md).

>[AZURE.NOTE]Der Premium-Tarif von Azure Redis Cache befindet sich derzeit in der Vorschauphase. Während der Vorschauphase kann Persistenz nicht in Verbindung mit Clustern oder virtuellen Netzwerken verwendet werden.

## Was ist Datenpersistenz?
Mithilfe der Redis-Persistenz können Sie die in Redis gespeicherten Daten dauerhaft speichern. Sie können zudem Momentaufnahmen erstellen und die Daten sichern, die Sie dann bei einem Hardwarefehler laden können. Dies ist ein großer Vorteil gegenüber dem Basic- oder Standard-Tarif, bei denen alle Daten im Arbeitsspeicher gespeichert sind, sodass bei einem Fehler, bei dem Cacheknoten ausfallen, möglicherweise Daten verloren gehen.

Azure Redis Cache bietet Redis-Persistenz, bei der die Daten in einem Azure-Speicherkonto gespeichert werden. In der öffentlichen Vorschau werden demnächst das [RDB-Modell](http://redis.io/topics/persistence) und [AOF](http://redis.io/topics/persistence) unterstützt.

## Datenpersistenz
Wenn Persistenz konfiguriert ist, speichert Azure Redis Cache basierend auf einer wählbaren Sicherungshäufigkeit eine Momentaufnahme des Redis-Caches in einem binären Redis-Format dauerhaft auf dem Datenträger. Bei einem schwerwiegenden Fehler, bei dem der primäre sowie der Replikatcache deaktiviert werden, wird der Cache mithilfe der neuesten Momentaufnahme wiederhergestellt.

Die Persistenz wird während der Erstellung des Caches auf dem Blatt **Neuer Redis-Cache** konfiguriert. Um einen Cache zu erstellen, melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) an, und klicken Sie auf **Neu** > **Daten und Speicher** > **Redis Cache**.

![Erstellen eines Redis-Cache][redis-cache-new-cache-menu]

Zum Konfigurieren der Persistenz wählen Sie zunächst auf dem Blatt **Wählen Sie Ihren Tarif** eine der **Premium**-Optionen für den Cache aus.

![Tarif auswählen][redis-cache-premium-pricing-tier]

Wenn Sie einen Premium-Tarif ausgewählt haben, klicken Sie auf **Redis persistence**.

![Redis-Persistenz][redis-cache-persistence]

Klicken Sie auf **Aktiviert**, um die RDB-Sicherung (Redis-Datenbank) zu aktivieren.

Wählen Sie in der Dropdownliste **Sicherungshäufigkeit** einen Wert aus. Zur Auswahl stehen **60 Minuten**, **6 Stunden**, **12 Stunden** und **24 Stunden**. Dieses Intervall wird ab dem Moment rückwärts gezählt, an dem der vorherige Sicherungsvorgang erfolgreich abgeschlossen wird. Wenn das Intervall abgelaufen ist, wird eine neue Sicherung gestartet.

Klicken Sie auf **Speicherkonto**, um das zu verwendende Speicherkonto auszuwählen. Der **Speicherschlüssel** wird automatisch eingetragen. Wenn der Schlüssel für das Speicherkonto jedoch neu generiert wird, können Sie ihn hier aktualisieren. Als Speicherkonto empfiehlt sich ein **Premium-Speicherkonto**, da Storage Premium einen höheren Durchsatz bietet.

>[AZURE.NOTE]AOF steht während der Vorschauphase des Premium-Tarifs nicht zur Verfügung, das Cache-Team arbeitet aber daran, dass diese Funktion hinzugefügt werden kann. Weitere Informationen zu RDB und AOF und zu den jeweiligen Vorteilen finden Sie unter [Redis Persistence](http://redis.io/topics/persistence) (in englischer Sprache).

![Redis-Persistenz][redis-cache-persistence-selected]

Klicken Sie auf **OK**, um die Persistenzkonfiguration zu speichern.

Nach der Erstellung des Caches wird die erste Sicherung gestartet, wenn das Intervall für die Sicherung abgelaufen ist.

## Persistenz – häufig gestellte Fragen

Die folgende Liste enthält Antworten auf häufig gestellte Fragen zur Persistenz in Azure Redis Cache.

## Kann ich die Persistenz für einen bereits erstellten Cache aktivieren?

Während der Vorschauphase können Sie die Persistenz nur bei der Erstellung eines Premium-Caches aktivieren und konfigurieren. Während der öffentlichen Vorschauphase wird der Wechsel von Basic oder Standard zu Premium nicht unterstützt. Dies wird aber bald möglich sein.

## Kann ich die Sicherungshäufigkeit ändern, nachdem ich den Cache erstellt habe?

Während der Vorschauphase können Sie die Persistenz nur bei der Erstellung des Caches konfigurieren. Um die Persistenzkonfiguration zu ändern, löschen Sie den Cache. Erstellen Sie dann einen neuen Cache mit der gewünschten Persistenzkonfiguration. Dies ist lediglich eine Einschränkung in der Vorschauphase. Diese Funktion ist bald verfügbar.

## Warum verstreichen mehr als 60 Minuten zwischen den Sicherungen, wenn ich eine Sicherungshäufigkeit von 60 Minuten festgelegt habe?

Das Intervall für die Sicherungshäufigkeit beginnt erst, nachdem der vorherige Sicherungsvorgang erfolgreich abgeschlossen wurde. Wenn für die Sicherungshäufigkeit 60 Minuten festgelegt sind und der Sicherungsvorgang nach 15 Minuten erfolgreich beendet wird, wird der nächste Sicherungsvorgang 75 Minuten nach der Startzeit des vorherigen Sicherungsvorgangs gestartet.

## Was geschieht mit den alten Sicherungen, wenn eine neue Sicherung durchgeführt wird?

Alle Sicherungen, mit Ausnahme der jeweils letzten Sicherung, werden automatisch gelöscht. Dieser Löschvorgang wird möglicherweise nicht sofort durchgeführt, ältere Sicherungen werden jedoch nicht dauerhaft gespeichert.

## Nächste Schritte
Informationen zur Verwendung weiterer Funktionen des Premium-Caches finden Sie in den folgenden Artikeln.

-	[Konfigurieren von Clustern für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-clustering.md)
-	[Konfigurieren der Unterstützung virtueller Netzwerke für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-vnet.md)
  
<!-- IMAGES -->

[redis-cache-new-cache-menu]: ./media/cache-how-to-premium-persistence/redis-cache-new-cache-menu.png

[redis-cache-premium-pricing-tier]: ./media/cache-how-to-premium-persistence/redis-cache-premium-pricing-tier.png

[redis-cache-persistence]: ./media/cache-how-to-premium-persistence/redis-cache-persistence.png

[redis-cache-persistence-selected]: ./media/cache-how-to-premium-persistence/redis-cache-persistence-selected.png

<!---HONumber=Oct15_HO2-->