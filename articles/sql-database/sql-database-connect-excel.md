<properties
	pageTitle="Verbinden von Excel mit SQL-Datenbank | Microsoft Azure"
	description="Erfahren Sie, wie Sie Microsoft Excel mit Azure SQL-Datenbank in der Cloud verbinden. Importieren Sie Daten zwecks Berichterstellung und Untersuchung in Excel."
	services="sql-database"
	keywords="Verbinden von Excel mit SQL, Importieren von Daten in Excel"
	documentationCenter=""
	authors="joseidz"
	manager="jeffreyg"
	editor="jeffreyg"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="02/04/2016"
	ms.author="joseidz"/>


# Verbinden von Excel mit einer Azure SQL-Datenbank und Erstellen eines Berichts 

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Erfahren Sie, wie Sie Excel mit einer SQL-Datenbank verbinden, um Daten in Excel zu importieren. Erstellen Sie anschließend einen Bericht zu den Daten.

Sie benötigen zuerst eine Azure SQL-­Datenbank. Wenn Sie keine haben, finden Sie unter [Erstellen Ihrer ersten SQL-Datenbank](sql-database-get-started.md) eine Datenbank mit Beispieldaten, die in wenigen Minuten startklar ist. In diesem Artikel importieren Sie Beispieldaten aus diesem Artikel in Excel, doch Sie können ähnliche Schritte auch mit Ihren eigenen Daten befolgen.

Sie benötigen auch eine Kopie von Excel. In diesem Artikel wird [Microsoft Excel 2016](https://products.office.com/de-DE/) verwendet.

## Verbinden von Excel mit einer SQL-Datenbank und Erstellen eines Berichts

1.	Zum Verbinden von Excel mit Azure SQL-Datenbank öffnen Sie Excel und erstellen dann eine neue Arbeitsmappe. Oder öffnen Sie eine vorhandene Excel-Arbeitsmappe, die Sie mit SQL-Datenbank verbinden möchten.

2.	Klicken Sie auf der Menüleiste am oberen Rand der Seite auf **Daten**. Klicken Sie auf **Aus anderen Quellen**, und klicken Sie dann auf **Von SQL Server**.

	![Auswählen einer Datenquelle: Verbinden Sie Excel mit SQL-Datenbank.](./media/sql-database-connect-excel/excel_data_source.png)

	Der Datenverbindungs-Assistent wird geöffnet.

3.	Geben Sie im Dialogfeld **Zum Datenbankserver verbinden** im Feld **Servername** den Namen des Servers, der den logischen Server hostet, im Format **<*Servername*>.database.windows.net** ein. Beispiel: **adventureserver.database.windows.net**.

4.	Klicken Sie im Abschnitt **Anmeldeinformationen** auf **Benutzername und Kennwort verwenden**. Geben Sie den **Benutzernamen** und das **Kennwort** wie beim Erstellen des Azure SQL-Datenbank-Servers ein, und klicken Sie dann auf **Weiter**.

	> [AZURE.TIP] Die Excel-Add-Ins [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) und [Power Query](https://www.microsoft.com/download/details.aspx?id=39379) verfügen über ähnliche Funktionen.

5. Wählen Sie im Dialogfeld **Datenbank und Tabelle wählen** im Pulldownmenü die Datenbank **AdventureWorks** aus und **vGetAllCategories** in der Liste der Tabellen und Ansichten aus. Klicken Sie anschließend auf **Weiter**.

	![Wählen Sie eine Datenbank und Tabelle aus.][5]

6. Klicken Sie im Dialogfeld **Datenverbindungsdatei speichern und fertig stellen** auf **Fertig stellen**.

7. Klicken Sie im Dialogfeld **Daten importieren** auf **PivotChart**, und klicken Sie dann auf **OK**.

	![Importieren von Daten in Excel: Wählen Sie im Dialogfeld „Importieren“ die Option „PivotChart“ aus.][2]

8. Wählen Sie im Dialogfeld **PivotChart-Felder** die folgende Konfiguration zum Erstellen eines Berichts für die Anzahl der Produkte pro Kategorie aus.

	![Konfigurieren Sie den Datenbankbericht.][3]

	Bei einer erfolgreichen Durchführung sieht das Ergebnis wie folgt aus:

	![Erfolg: Excel ist mit SQL-Datenbank verbunden.][4]

## Nächste Schritte

Wenn Sie ein SaaS-Entwickler (Software-as-a-Service) sind, erfahren Sie mehr über [elastische Datenbankpools](sql-database-elastic-pool.md). Sie können problemlos große Sammlungen von Datenbanken mithilfe von [Aufträgen für die elastische Datenbank](sql-database-elastic-jobs-overview.md) verwalten.

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=AcomDC_0204_2016-->