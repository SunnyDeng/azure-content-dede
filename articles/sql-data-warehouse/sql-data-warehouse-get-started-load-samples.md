<properties
   pageTitle="Laden von Beispieldaten in SQL Data Warehouse | Microsoft Azure"
   description="Laden von Beispieldaten in SQL Data Warehouse"
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
   ms.date="09/23/2015"
   ms.author="lodipalm;barbkess"/>

#Laden von Beispieldaten in SQL Data Warehouse

Während Sie eine SQL Data Warehouse-Instanz erstellen, können Sie auch auf einfache Weise einige Beispieldaten in die Instanz laden. Wenn Sie diesen Schritt während der Bereitstellung verpasst haben, können Sie [Beispieldaten auch manuell laden][].

Im Folgenden erhalten Sie einen kurzen Überblick darüber, wie AdventureWorksDW in die Datenbank geladen werden kann. Dieses Dataset modelliert eine Data Warehouse-Beispielstruktur für ein fiktives Unternehmen namens AdventureWorks mit Daten, die Umsätze und Kunden für das Unternehmen darstellen.

## Hinzufügen von Beispieldaten während der Erstellung
Sie können sicherstellen, dass diese Daten während der Bereitstellung in das SQL Data Warehouse geladen werden, indem Sie folgende Schritte ausführen:

1. Starten Sie den Erstellungsprozess, indem Sie SQL Data Warehouse im [Azure-Portal][] durch Klicken auf „+Neu“ und anschließend „Daten und Speicher“ suchen oder im Marketplace, indem Sie nach „SQL Data Warehouse“ suchen. 
 
2. Sobald der Prozess gestartet wurde, müssen Sie auf die Option „Quelle auswählen“ klicken und sie dann auf „Beispiel“ festlegen. Wenn Sie keinen neuen Server erstellen, werden Sie ebenfalls dazu aufgefordert, die Anmeldung für den Server anzugeben, den Sie zur Erstellung verwenden.


> [AZURE.NOTE]Um Beispieldaten in Ihre Instanz zu laden, müssen Sie zum Zugriff auf den Server die Azure-Dienste aktivieren (dies sollte standardmäßig der Fall sein, wenn ein neuer Server erstellt wird). Wenn dies nicht erfolgt, dann schlägt das Laden fehl, wobei Sie immer noch [Beispieldaten manuell laden][].


##Verwenden von Power BI zur Analyse von Adventureworks

Die Nutzung des Beispieldatensatzes ist eine gute Möglichkeit, erste Schritte mit Power BI durchzuführen. Nach dem Laden der Beispieldaten können Sie eine Verbindung mit SQL Data Warehouse entweder durch Klicken auf die Schaltfläche „Öffnen in Power BI“ im Azure-Portal oder durch Wechseln auf [Power BI][] und [Herstellen einer Verbindung mit SQL Data Warehouse][] öffnen. Nachdem die Verbindung hergestellt ist, sollte ein neues Dataset mit dem gleichen Namen wie das Data Warehouse erstellt werden. Zum Vereinfachen der Analyse haben wir eine Ansicht namens „AggregateSales“ mit einigen der Metriken erstellt, die entscheidend für die Analyse des Umsatzes vom Unternehmen sind. Klicken Sie auf den Namen in dieser Ansicht, um sie zu erweitern, und sehen Sie die enthaltenen Spalten, sodass Sie mithilfe der folgenden Schritte einige schnelle Visualisierungen erstellen können:

1. Zum Einstieg können wir einfach eine Übersicht über all unsere Umsätze durch Klicken auf die Spalten „PostalCode“ und „SalesAmount“ erstellen. Power BI erkennt dies sogar automatisch als geografische Daten und fügt Sie in einer Karte hinzu. 

2. Wenn Sie ein Balkendiagramm der Umsätze erstellen möchten, klicken Sie einfach auf die Spalte „SalesAmount“, und Power BI erstellt das Diagramm automatisch für Sie. Sie können ihm zusätzliche Tiefe verleihen, indem Sie das Diagramm „CustomerIncome“ auf das Feld „Achse“ auf der linken Seite von „AggregateSales“ ziehen, um die Umsätze nach Kundeneinkommen anzuzeigen.

3. Wenn Sie abschließend eine Zeitachse der Umsätze erstellen möchten, müssen Sie nur auf „SalesAmount“, „OrderDate“ und „Liniendiagramm“ (das erste Symbol in der zweiten Zeile unter „Visualisierungen“) klicken.

Sie können den Fortschritt zu einem beliebigen Zeitpunkt speichern, indem Sie auf die Schaltfläche „Speichern“ in der oberen linken Ecke klicken und die Visualisierung als Bericht speichern.

## Herstellen einer Verbindung mit und Abfragen der Beispieldatenbank

Sie können die Beispieldaten auch mit herkömmlichen Mitteln analysieren. Wie in der Dokumentation zum [Herstellen der Verbindung und Abfragen][] beschrieben, können Sie mithilfe von SQL Server Data Tool in Visual Studio eine Verbindung mit dieser Datenbank herstellen. Nachdem Sie einige Beispieldaten in SQL Data Warehouse geladen haben, können Sie als Einsteig schnell einige Abfragen ausführen.

Sie können eine einfache SELECT-Anweisung ausführen, um alle Informationen zu den Mitarbeitern abzurufen:

	SELECT * FROM DimEmployee;

Sie können auch eine komplexere Abfrage mithilfe von Konstrukten wie GROUP BY ausführen, um den Gesamtbetrag für alle Verkäufe an jedem Tag anzuzeigen:

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

Sie können sogar die WHERE-Klausel verwenden, um Aufträge vor einem bestimmten Datum herauszufiltern:

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	WHERE OrderDateKey > '20020801'
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

SQL Data Warehouse unterstützt fast alle T-SQL-Konstrukte, die auch von SQL Server unterstützt werden. Einige der Unterschiede sind in der Dokumentation zum [Migrieren von Code][] angegeben.



## Nächste Schritte
Nachdem Sie sich schon etwas mit den Beispieldaten vertraut gemacht haben, können Sie sich nun mit der [Entwicklung][], dem [Laden][] oder dem [Migrieren][] befassen.

<!--Image references-->

<!--Article references-->
[Migrieren]: https://azure.microsoft.com/DE-DE/documentation/articles/sql-data-warehouse-overview-migrate/
[Entwicklung]: https://azure.microsoft.com/DE-DE/documentation/articles/sql-data-warehouse-overview-develop/
[Laden]: https://azure.microsoft.com/DE-DE/documentation/articles/sql-data-warehouse-overview-load/
[Herstellen der Verbindung und Abfragen]: https://azure.microsoft.com/DE-DE/documentation/articles/sql-data-warehouse-get-started-connect-query/
[Migrieren von Code]: https://azure.microsoft.com/DE-DE/documentation/articles/sql-data-warehouse-migrate-code/
[Beispieldaten auch manuell laden]: https://azure.microsoft.com/DE-DE/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[Beispieldaten manuell laden]: https://azure.microsoft.com/DE-DE/documentation/articles/sql-data-warehouse-get-started-manually-load-samples/
[Azure-Portal]: portal.azure.com
[Power BI]: www.powerbi.com
[Herstellen einer Verbindung mit SQL Data Warehouse]: https://azure.microsoft.com/DE-DE/documentation/articles/sql-data-warehouse-integrate-power-bi/

<!--MSDN references-->
[Microsoft Command Line Utilities for SQL Server]: http://www.microsoft.com/DE-DE/download/details.aspx?id=36433

<!--Other Web references-->
[Sample Data Scripts]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip

<!---HONumber=Sept15_HO4-->