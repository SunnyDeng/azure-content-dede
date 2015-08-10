<properties
	pageTitle="Herstellen einer Verbindung mit einer Azure SQL-Datenbank mit Excel"
	description="Excel-Arbeitsblatt in Azure SQL-Datenbank für die Berichterstellung und die Untersuchung von Daten."
	services="sql-database"
	documentationCenter=""
	authors="joseidz"
	manager="joseidz"
	editor="joseidz"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="joseidz"/>


# Herstellen einer Verbindung mit einer Azure SQL-Datenbank mit Excel
Verbinden Sie Excel mit einer Azure SQL-Datenbank und Erstellen Sie einen Bericht zu den Daten in der Datenbank.

## Voraussetzungen
- Eine bereitgestellte und ausgeführte Azure SQL-Datenbank. Informationen zum Erstellen einer neuen SQL-Datenbank finden Sie unter [Erste Schritte mit Microsoft Azure SQL-Datenbank](sql-database-get-started.md).
- [Microsoft Excel 2013](https://products.office.com/en-US/) \(oder Microsoft Excel 2010\)

## Verbinden mit der SQL-Datenbank und Erstellen eines Berichts
1.	Öffnen Sie Excel.
2.	Klicken Sie in der Menüleiste am oberen Rand der Seite auf **Daten**.
3.	Klicken Sie auf **Aus anderen Quellen**, und dann auf **Von SQL Server**. Der **Datenverbindungs-Assistent** wird geöffnet.

	![Assistent für Datenverbindung][1]
4.	Geben Sie im Feld **Servername** den Namen für den Azure SQL-Datenbankserver ein. Beispiel:

	 	adventureserver.database.windows.net
5.	Wählen Sie im Abschnitt **Anmeldeinformationen** **Verwenden Sie folgende Kombination aus Benutzername und Kennwort**, und geben Sie dann die entsprechenden Anmeldeinformationen für den SQL-Datenbankserver ein. Klicken Sie auf **Next**.

	Hinweis: Die beiden Excel-Add-Ins [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) und [Power Query](https://www.microsoft.com/download/details.aspx?id=39379) verfügen über sehr ähnliche Funktionen.

6. Wählen Sie im Dialogfeld **Datenbank und Tabelle wählen** die Datenbank **AdventureWorks** aus dem Pulldownmenü aus, und wählen Sie **vGetAllCategories** in der Liste der Tabellen und Ansichten. Klicken Sie anschließend auf **Weiter**.

	![Datenbank und Tabelle auswählen][5]
7. Klicken Sie im Dialogfeld **Datenverbindungsdatei speichern und fertig stellen** auf **Fertig stellen**.
8. Klicken Sie im Dialogfeld **Daten importieren** auf **PivotChart**, und klicken Sie dann auf **OK**.

	![„Daten importieren“ auswählen][2]
9. Wählen Sie im Dialogfeld **PivotChart-Felder** die folgende Konfiguration zum Erstellen eines Berichts für die Anzahl der Produkte pro Kategorie aus.

	![Konfiguration][3]
10.	Bei einer erfolgreichen Durchführung sieht das Ergebnis wie folgt aus:

	![Erfolg][4]

## Nächste Schritte

Wenn Sie ein Software als Service \(SaaS\)-Entwickler sind, erfahren Sie mehr über [elastische Datenbankpools](sql-database-elastic-pool.md). Sie können problemlos große Sammlungen von Datenbanken mithilfe von [Aufträgen für die elastische Datenbank](sql-database-elastic-jobs-overview.md) verwalten.

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=July15_HO5-->