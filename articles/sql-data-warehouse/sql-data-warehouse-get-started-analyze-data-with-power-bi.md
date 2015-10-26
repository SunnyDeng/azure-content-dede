<properties
    pageTitle="Analysieren von SQL Data Warehouse-Daten mit Power BI | Microsoft Azure"
    description="Analysieren von SQL Data Warehouse-Daten mit Power BI"
    services="sql-data-warehouse"
    documentationCenter="NA"
    authors="lodipalm"
    manager="barbkess"
    editor=""/>

<tags
    ms.service="sql-data-warehouse"
    ms.devlang="NA"
    ms.topic="article"
    ms.tgt_pltfrm="NA"
    ms.workload="data-services"
    ms.date="10/07/2015"
    ms.author="lodipalm"/>

# Analysieren von Daten mit Power BI
In diesem Lernprogramm erfahren Sie, wie Sie Power BI zum Herstellen einer Verbindung mit SQL Data Warehouse verwenden und einige einfache Visualisierungen erstellen.

> [AZURE.NOTE]Zum Durchführen dieses Lernprogramms benötigen Sie eine SQL Data Warehouse-Datenbank, die mit der AdventureWorksDW-Beispieldatenbank vorab geladen wird. Unter [Erstellen eines SQL Data Warehouse](sql-data-warehouse-get-started-provision.md) finden Sie Informationen zur Erstellung.
> 
> Wenn Sie bereits über eine SQL Data Warehouse-Datenbank verfügen, aber noch keine Beispieldaten haben, können Sie [Beispieldaten manuell laden][].


## Herstellen einer Verbindung mit der SQL Data Warehouse-Datenbank

Gehen Sie wie folgt vor, um Power BI zu öffnen und eine Verbindung mit der AdventureWorksDW-Datenbank herzustellen:

1. Melden Sie sich am [Azure-Vorschauportal][] an.
2. Klicken Sie auf **SQL-Datenbanken**, und wählen Sie Ihre AdventureWorks SQL Data Warehouse-Datenbank aus. 

    ![Datenbank suchen][1]

3. Klicken Sie auf die Schaltfläche „Open in Power BI“.

    ![Power BI-Schaltfläche][2]

4. Nun sollte die Seite mit der SQL Data Warehouse-Verbindung und ihren Datenbankinformationen angezeigt werden. Geben Sie Ihr Kennwort ein. Es wird eine vollständige Verbindung mit Ihrer SQL Data Warehouse-Datenbank hergestellt.


## Analysieren von Daten mit Power BI

Sie können Power BI jetzt zum Analysieren Ihrer AdventureWorksDW-Beispieldaten verwenden. Zum Durchführen der Analyse verfügt AdventureWorksDW über die Ansicht „AggregateSales“. Diese Ansicht enthält einige wichtige Metriken zum Analysieren des Umsatzes des Unternehmens.

1. Klicken Sie zum Erstellen einer Karte mit den Umsatzbeträgen nach Postleitzahl auf die Ansicht „AggregateSales“, um sie zu erweitern, und klicken Sie dann auf die Spalten „PostalCode“ und „SalesAmount“. Power BI erkennt dies automatisch als geografische Daten und fügt sie in eine Karte ein.

    ![Power BI-Karte][3]

2. Klicken Sie zum Erstellen eines Balkendiagramms mit den Umsätzen einfach auf die Spalte „SalesAmount“. Zum Hinzufügen von Details ziehen Sie das Diagramm „CustomerIncome“ auf das Feld „Axis“ links von „AggregateSale“, um den Umsatz nach Kundeneinkommen anzuzeigen.

    ![Power BI-Balkendiagramm][4]

3. Klicken Sie zum Erstellen einer Umsatzzeitachse auf „SalesAmount“, „OrderDate“ und „Liniendiagramm“. Dies ist das erste Symbol in der zweiten Zeile unter „Visualisierungen“.

    ![Power BI-Liniendiagramm][5]

Sie können den Fortschritt zu einem beliebigen Zeitpunkt speichern, indem Sie auf die Schaltfläche **Speichern** in der oberen linken Ecke klicken und die Visualisierung als Bericht speichern.

## Nächste Schritte
Nachdem Sie sich schon etwas mit den Beispieldaten vertraut gemacht haben, können Sie sich nun mit der [Entwicklung][], dem [Laden][] oder dem [Migrieren][] befassen.

<!--Image references-->
[1]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-find-database.png
[2]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-button.png
[3]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-map.png
[4]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-bar.png
[5]: ./media/sql-data-warehouse-get-started-analyze-data-with-power-bi/pbi-line.png

<!--Article references-->
[Migrieren]: sql-data-warehouse-overview-migrate.md
[Entwicklung]: sql-data-warehouse-overview-develop.md
[Laden]: sql-data-warehouse-overview-load.md
[Beispieldaten manuell laden]: sql-data-warehouse-get-started-manually-load-samples.md
[Azure-Vorschauportal]: https://portal.azure.com/
[Power BI]: http://www.powerbi.com/
[connecting to SQL Data Warehouse]: sql-data-warehouse-integrate-power-bi.md
[Create a SQL Data Warehouse]: sql-data-warehouse-get-started-provision.md

<!---HONumber=Oct15_HO3-->