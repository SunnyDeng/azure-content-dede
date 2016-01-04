<properties
	pageTitle="Behandeln von Problemen bei der Datenbankleistung mithilfe von Azure SQL-Datenbank"
	description="Kurzanleitung zur Behandlung von Problemen mit der Datenbankleistung."
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="v-shysun"/>

# Behandeln von Problemen bei der Datenbankleistung mithilfe von Azure SQL-Datenbank
Sie können jederzeit den [Tarif](sql-database-service-tiers.md) einer Einzeldatenbank ändern oder die Anzahl der eDTUs eines Pools für elastische Datenbanken erhöhen, um die Leistung zu verbessern. Doch zuvor sollten Sie zunächst Möglichkeiten zum Verbessern und Optimieren der Abfrageleistung bestimmen. Fehlende Indizes und falsch optimierte Abfragen sind häufige Ursachen einer schlechten Datenbankleistung.

## Schritte zum Bewerten und Optimieren der Datenbankleistung
1.	Klicken Sie im [Azure-Portal](https://portal.azure.com) auf **SQL-Datenbanken**, wählen Sie die Datenbank aus, und suchen Sie dann mithilfe des Diagramms „Überwachung“ Ressourcen, die sich ihrem Maximum annähern. „DTU-Verbrauch“ wird standardmäßig angezeigt. Klicken Sie auf **Bearbeiten**, um den Zeitraum und die angezeigten Werte zu ändern.
2.	Verwenden Sie [Query Performance Insight](sql-database-query-performance.md) zum Auswerten der Abfragen mittels DTUs und dann [Index Advisor](sql-database-index-advisor.md) zum Empfehlen und Erstellen von Indizes.
3.	Mithilfe von dynamischen Verwaltungsansichten (DMVs), erweiterten Ereignissen (Xevents) und des Abfragespeichers in SSMS können Sie Leistungsparameter in Echtzeit abrufen. Im [Leistungsleitfaden](sql-database-performance-guidance.md) finden Sie detaillierte Tipps zur Überwachung und Optimierung.

## Schritte zur Verbesserung der Datenbankleistung mit mehr Ressourcen
1.	Für Einzeldatenbanken können Sie die [Diensttarife bedarfsgesteuert ändern](sql-database-scale-up.md), um die Datenbankleistung zu steigern.
2.	Ziehen Sie bei mehreren Datenbanken [Pool für elastische Datenbanken](sql-database-elastic-pool-guidance.md) in Betracht, um Ressourcen automatisch zu skalieren.

Wenn die Leistungsprobleme anhalten, wenden Sie sich an den Support, um ein Supportticket zu öffnen.

<!---HONumber=AcomDC_1217_2015-->