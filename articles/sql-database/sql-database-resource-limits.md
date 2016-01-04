<properties
	pageTitle="Ressourceneinschränkungen für Azure SQL-Datenbanken"
	description="Diese Seite beschreibt einige allgemeine Ressourceneinschränkungen für Azure SQL-Datenbanken."
	services="sql-database"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar" />


<tags
	ms.service="sql-database"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="data-management"
	ms.date="11/03/2015"
	ms.author="jroth" />


# Ressourceneinschränkungen für Azure SQL-Datenbanken

## Übersicht

Azure SQL-Datenbank verwaltet die für eine Datenbank verfügbaren Ressourcen mithilfe zweier verschiedener Mechanismen: **Ressourcenkontrolle** und **Durchsetzung von Grenzen**. Dieser Artikel beschreibt diese zwei Hauptbereiche der Ressourcenverwaltung.

## Ressourcenkontrolle
Eines der Entwurfsziele der Tarife Basic, Standard und Premium ist es, dass sich die Azure SQL-Datenbank so verhält, als ob sie auf einem eigenen Computer vollständig isoliert von anderen Datenbanken ausgeführt wird. Die Ressourcenkontrolle emuliert dieses Verhalten. Wenn die aggregierte Ressourcenverwendung die maximal verfügbaren Ressourcen an CPU, Arbeitsspeicher, Protokoll-E/A und Daten-E/A erreicht, die der Datenbank zugeordnet sind, reiht die Ressourcenkontrolle die Ausführung von Abfragen in eine Warteschlange ein und ordnet den Abfragen in der Warteschlange Ressourcen zu, während sie nach und nach freigegeben werden.

Wie bei einem dedizierten Computer führt das Nutzen aller verfügbarer Ressourcen zu einer längeren Ausführung der derzeit ausgeführten Abfragen, was Befehlstimeouts auf dem Client zur Folge haben kann. Anwendungen mit aggressiver Wiederholungslogik und solche, die Abfragen für die Datenbank mit hoher Frequenz ausführen, können bei dem Versuch auf Fehlermeldungen stoßen, neue Abfragen auszuführen, wenn das Limit gleichzeitiger Anforderungen bereits erreicht wurde.

### Empfehlungen:
Überwachen Sie die Ressourcenverwendung sowie die durchschnittlichen Antwortzeiten für Abfragen, wenn die maximale Auslastung einer Datenbank fast erreicht wurde. Falls längere Abfragewartezeiten auftreten, haben Sie in der Regel drei Optionen:

1.	Reduzieren Sie die Menge der eingehenden Anfragen in der Datenbank, um einen Timeout und das Anhäufen von Anfragen zu verhindern.

2.	Weisen Sie der Datenbank eine höhere Leistungsstufe zu.

3.	Optimieren Sie Abfragen, um die Ressourcenverwendung für jede Abfrage zu reduzieren. Weitere Informationen finden Sie im Abschnitt "Abfrageoptimierung/Abfragehinweise" im Artikel "Leitfaden zur Azure SQL-Datenbankleistung".

## Durchsetzung von Grenzwerten
Durch das Verweigern neuer Anforderungen bei Erreichen der Limits werden andere Ressourcen als CPU, Arbeitsspeicher, Protokoll-E/A und Daten-E/A durchgesetzt. Clients erhalten abhängig von der erreichten Grenze eine [Fehlermeldung](sql-database-develop-error-messages.md).

Z. B. wird die Anzahl der Verbindungen mit einer SQL-Datenbank sowie die Anzahl der gleichzeitigen Anforderungen, die verarbeitet werden können, beschränkt. Mit einer SQL-Datenbank kann die Anzahl der Verbindungen mit der Datenbank größer als die Anzahl der gleichzeitigen Anforderungen sein, um Verbindungspooling zu unterstützen. Während die Anzahl der verfügbaren Verbindungen einfach von der Anwendung gesteuert werden kann, ist die Anzahl paralleler Anforderungen oft schwieriger zu schätzen und zu steuern. Insbesondere bei Spitzenbelastungen können Fehler auftreten, wenn die Anwendung entweder zu viele Anforderungen sendet oder die Datenbank seine Ressourcengrenzen erreicht und anfängt, Workerthreads aufgrund einer längeren Ausführungszeit von Abfragen anzuhäufen.

## Tarife und Leistungsebenen

Für eine einzelne Datenbank sind deren Einschränkungen durch die Dienstebene und Leistungsstufe der Datenbank definiert. In der folgenden Tabelle sind die Merkmale von Basic-, Standard- und Premium-Datenbanken für unterschiedliche Leistungsstufen beschrieben.

[AZURE.INCLUDE [Tarife für SQL-Datenbank](../../includes/sql-database-service-tiers-table.md)]

[Elastische Datenbankpools](sql-database-elastic-pool.md) nutzen Ressourcen gemeinsam für die Datenbanken im Pool. In der folgenden Tabelle sind die Merkmale von elastischen Basic-, Standard- und Premium-Datenbankpools beschrieben.

[AZURE.INCLUDE [Tabelle der SQL-Datenbank-Dienstebenen für elastische Datenbanken](../../includes/sql-database-service-tiers-table-elastic-db-pools.md)]

Eine erweiterte Definition der einzelnen Ressourcen, die in den vorangehenden Tabellen aufgeführt werden, können Sie der Beschreibungen in [Funktionen und Grenzen der Serviceebenen](sql-database-performance-guidance.md#service-tier-capabilities-and-limits) entnehmen. Eine Übersicht über die Dienstebenen finden Sie unter [Dienstebenen und Leistungsstufen für Azure SQL-Datenbanken](sql-database-service-tiers.md).

## DTU-Kontingent pro Server

Azure SQL-Datenbank verfügt über ein DTU-Kontingent pro logischen Server von derzeit 15000 DTUs. Dieses Kontingent stellt die DTUs dar, die ein logischer Server hosten kann, basierend auf der Summe der DTUs der Leistungsstufe der einzelnen Datenbanken auf dem Server. Beispielsweise hat ein Server mit 5 Basic-Datenbanken (maximal 5 x 5 DTUs), 2 Standard-S1-Datenbanken (maximal 2 x 20 DTUs) und 3 Premium-P1-Datenbanken (bis zu 3 x 100 DTUs) insgesamt 365 DTUs des DTU-Kontingents von 15000 verbraucht.

>[AZURE.NOTE] [Wenden Sie sich an den Support](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/), um dieses Kontingent zu erhöhen.

## Weitere Einschränkungen für SQL-Datenbanken

| Bereich | Begrenzung | Beschreibung |
|---|---|---|
| Datenbanken mit automatisiertem Export pro Abonnement | 10 | Automatisierter Export ermöglicht es Ihnen, einen benutzerdefinierten Zeitplan für die Sicherung Ihrer SQL-Datenbanken zu erstellen. Weitere Informationen finden Sie unter [SQL-Datenbanken: Support für automatisierte SQL-Datenbankexporte](http://weblogs.asp.net/scottgu/windows-azure-july-updates-sql-database-traffic-manager-autoscale-virtual-machines).|

## Ressourcen

[Einschränkungen für Azure-Abonnements und Dienste, Kontingente und Einschränkungen](../azure-subscription-service-limits.md)

[Dienst- und Leistungsebenen für Azure SQL-Datenbanken](sql-database-service-tiers.md)

[Fehlermeldungen für Clientprogramme der SQL-Datenbank](sql-database-develop-error-messages.md)

<!----HONumber=Nov15_HO2-->