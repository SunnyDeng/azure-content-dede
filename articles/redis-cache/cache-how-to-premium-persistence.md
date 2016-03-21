<properties 
	pageTitle="Konfigurieren von Datenpersistenz für Azure Redis Cache vom Typ ";Premium";" 
	description="Erfahren Sie, wie Sie die Datenpersistenz für Ihren Premium Azure Redis Cache mit Premium-Tarif konfigurieren und verwalten." 
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

# Konfigurieren von Datenpersistenz für Azure Redis Cache vom Typ "Premium"

Für Azure Redis Cache stehen verschiedene Cacheangebote bereit, die Flexibilität bei der Auswahl von Cachegröße und -features bieten, einschließlich des neuen Premium-Tarifs.

Der Premium-Tarif für Azure Redis Cache umfasst Clustering, Persistenz und Unterstützung des virtuellen Netzwerks. In diesem Artikel wird erläutert, wie die Persistenz in einer Azure Redis Cache-Instanz vom Typ "Premium" konfiguriert wird.

Informationen zu anderen Funktionen des Premium-Caches finden Sie unter [Konfigurieren von Clustern für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-clustering.md) und [Konfigurieren der Unterstützung virtueller Netzwerke für Azure Redis Cache vom Typ "Premium"](cache-how-to-premium-vnet.md).

## Was ist Datenpersistenz?
Mithilfe der Redis-Persistenz können Sie die in Redis gespeicherten Daten dauerhaft speichern. Sie können zudem Momentaufnahmen erstellen und die Daten sichern, die Sie dann bei einem Hardwarefehler laden können. Dies ist ein großer Vorteil gegenüber dem Basic- oder Standard-Tarif, bei denen alle Daten im Arbeitsspeicher gespeichert sind, sodass bei einem Fehler, bei dem Cacheknoten ausfallen, möglicherweise Daten verloren gehen.

Azure Redis Cache bietet mit dem [RDB-Modell](http://redis.io/topics/persistence) Redis-Persistenz, bei der die Daten in einem Azure-Speicherkonto gespeichert werden. Wenn Persistenz konfiguriert ist, speichert Azure Redis Cache basierend auf einer wählbaren Sicherungshäufigkeit eine Momentaufnahme des Redis-Caches in einem binären Redis-Format dauerhaft auf dem Datenträger. Bei einem schwerwiegenden Fehler, bei dem der primäre sowie der Replikatcache deaktiviert werden, wird der Cache mithilfe der neuesten Momentaufnahme wiederhergestellt.

Persistenz kann auf dem Blatt **Neuer Redis-Cache** während der Erstellung des Caches sowie für vorhandene Premium-Caches auf dem Blatt **Einstellungen** konfiguriert werden.

## Erstellen eines Premium-Caches

Um einen Cache zu erstellen und zu konfigurieren, melden Sie sich beim [Azure-Portal](https://portal.azure.com) an, und klicken Sie auf **Neu** > **Daten und Speicher** > **Redis Cache**.

![Erstellen eines Redis-Cache][redis-cache-new-cache-menu]

Zum Konfigurieren der Persistenz wählen Sie zunächst auf dem Blatt **Wählen Sie Ihren Tarif** eine der **Premium**-Optionen für den Cache aus.

![Tarif auswählen][redis-cache-premium-pricing-tier]

Wenn Sie einen Premium-Tarif ausgewählt haben, klicken Sie auf **Redis persistence**.

![Redis-Persistenz][redis-cache-persistence]

Die Schritte im folgenden Abschnitt beschreiben, wie Sie Redis-Persistenz für Ihren neuen Premium-Cache konfigurieren. Nachdem Redis-Persistenz konfiguriert wurde, klicken Sie auf **Erstellen**, um den neuen Premium-Cache mit Redis-Persistenz zu erstellen.

## Konfigurieren von Redis-Persistenz

Redis-Persistenz wird auf dem Blatt **Redis-Datenpersistenz** konfiguriert. Bei einem neuen Cache wird während der Erstellung des Caches auf dieses Blatt zugegriffen, wie im vorherigen Abschnitt beschrieben. Für vorhandene Caches erfolgt der Zugriff auf das Blatt **Redis-Datenpersistenz** über das Blatt **Einstellungen** für Ihren Cache.

![Redis-Einstellungen][redis-cache-settings]

Klicken Sie auf **Aktiviert**, um die Redis-Datenbanksicherung zu aktivieren. Um Redis-Persistenz für einen zuvor aktivierten Premium-Cache zu deaktivieren, klicken Sie auf **Deaktiviert**.

Zum Konfigurieren des Sicherungsintervalls wählen Sie in der Dropdownliste eine **Sicherungshäufigkeit** aus. Zur Auswahl stehen **15 Minuten**, **30 Minuten**, **60 Minuten**, **6 Stunden**, **12 Stunden** und **24 Stunden**. Dieses Intervall wird ab dem Moment rückwärts gezählt, an dem der vorherige Sicherungsvorgang erfolgreich abgeschlossen wird. Wenn das Intervall abgelaufen ist, wird eine neue Sicherung gestartet.

Klicken Sie auf **Speicherkonto**, um das Speicherkonto auszuwählen, und wählen Sie entweder **Primärschlüssel** oder **Sekundärschlüssel** aus der Dropdownliste **Speicherschlüssel** aus. Sie müssen ein Speicherkonto auswählen, das aus der gleichen Region wie der Cache stammt, und wir empfehlen ein **Storage Premium**-Konto, da dieses einen höheren Durchsatz aufweist.

>[AZURE.IMPORTANT] Wenn der Speicherschlüssel für Ihr Persistenzkonto neu generiert wird, müssen Sie den gewünschten Schlüssel erneut in der Dropdownliste **Speicherschlüssel** auswählen.

![Redis-Persistenz][redis-cache-persistence-selected]

Klicken Sie auf **OK**, um die Persistenzkonfiguration zu speichern.

Die nächste Sicherung (oder erste Sicherung bei neuen Caches) wird gestartet, sobald das Intervall für die Sicherungshäufigkeit abgelaufen ist.



## Persistenz – häufig gestellte Fragen

Die folgende Liste enthält Antworten auf häufig gestellte Fragen zur Persistenz in Azure Redis Cache.

## Kann ich die Persistenz für einen bereits erstellten Cache aktivieren?

Ja, die Redis-Persistenz kann sowohl bei der Erstellung des Caches als auch für vorhandene Premium-Caches konfiguriert werden.

## Kann ich die Sicherungshäufigkeit ändern, nachdem ich den Cache erstellt habe?

Ja, Sie können die Sicherungshäufigkeit auf dem Blatt **Redis-Datenpersistenz** ändern. Anweisungen dazu finden Sie unter [Konfigurieren von Redis-Persistenz](#configure-redis-persistence).

## Warum verstreichen mehr als 60 Minuten zwischen den Sicherungen, wenn ich eine Sicherungshäufigkeit von 60 Minuten festgelegt habe?

Das Intervall für die Sicherungshäufigkeit beginnt erst, nachdem der vorherige Sicherungsvorgang erfolgreich abgeschlossen wurde. Wenn für die Sicherungshäufigkeit 60 Minuten festgelegt sind und der Sicherungsvorgang nach 15 Minuten erfolgreich beendet wird, wird der nächste Sicherungsvorgang 75 Minuten nach der Startzeit des vorherigen Sicherungsvorgangs gestartet.

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

[redis-cache-settings]: ./media/cache-how-to-premium-persistence/redis-cache-settings.png

<!---HONumber=AcomDC_0309_2016-->