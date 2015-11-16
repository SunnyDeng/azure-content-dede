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
   ms.date="11/02/2015"
   ms.author="lodipalm;barbkess"/>

#Laden von Beispieldaten in SQL Data Warehouse

Sobald Sie [eine Instanz der SQL Data Warehouse-Datenbank erstellt haben][create a SQL Data Warehouse database instance], besteht der nächste Schritt darin, einige Tabellen zu erstellen und zu laden. Sie können die Adventure Works-Beispielskripts verwenden, die wir für SQL Data Warehouse erstellt haben, um Tabellen für das fiktive Unternehmen namens Adventure Works zu erstellen und zu laden. Diese Skripts verwenden „sqlcmd“ zum Ausführen von SQL und „bcp“, um Daten zu laden. Wenn Sie diese Tools bereits installiert haben, folgen Sie diesen Links, um [bcp][] und [sqlcmd][] zu installieren.

Befolgen Sie diese einfachen Schritte, um die Adventure Works-Beispieldatenbank in SQL DW zu laden ...

1. Laden Sie die [Adventure Works-Beispielskripts für die SQL Data Warehouse][] herunter.

2. Extrahieren Sie die Dateien aus der heruntergeladenen ZIP-Datei in ein Verzeichnis auf Ihrem lokalen Computer.

3. Bearbeiten Sie die extrahierte Datei „aw\_create.bat“, und legen Sie am Anfang der Datei die folgenden Variablen fest: Entfernen Sie alle Leerzeichen zwischen dem „=“ und dem Parameter. Im Folgenden finden Sie Beispiele für die Bearbeitung.

    	server=mylogicalserver.database.windows.net
    	user=mydwuser
    	password=Mydwpassw0rd
    	database=mydwdatabase

4. Führen Sie über die Windows-Befehlszeile die bearbeitete Datei „aw\_create.bat“ aus. Stellen Sie sicher, dass Sie sich in dem Verzeichnis befinden, in dem Sie die bearbeitete Version von „aw\_create.bat“ gespeichert haben. Mit diesem Skript werden folgende Vorgänge durchgeführt:
	* Löschen von Adventure Works-Tabellen oder -Ansichten, die bereits in der Datenbank vorhanden sind
	* Erstellen von Adventure Works-Tabellen und -Ansichten
	* Laden der einzelnen Adventure Works-Tabellen mithilfe von bcp
	* Überprüfen der Zeilenanzahl für jede Adventure Works-Tabelle
	* Erfassen von Statistiken jeder Spalte für jede Adventure Works-Tabelle


##Abfragen von Beispieldaten

Nachdem Sie einige Beispieldaten in SQL Data Warehouse geladen haben, können Sie schnell einige Abfragen ausführen. Stellen Sie zum Ausführen einer Abfrage eine Verbindung zur neu erstellten Adventure Works-Datenbank in Azure SQL DW mit Visual Studio und SSDT her, wie im [Verbindungsdokument][] beschrieben.

Beispiel einer einfachen Select-Anweisung, um alle Informationen zu den Mitarbeitern abzurufen:

	SELECT * FROM DimEmployee;

Beispiel einer komplexeren Abfrage mithilfe von Konstrukten wie GROUP BY, um den Gesamtbetrag für alle Verkäufe an jedem Tag anzuzeigen:

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

Beispiel einer SELECT-Anweisung mit einer WHERE-Klausel, um Aufträge vor einem bestimmten Datum herauszufiltern:

	SELECT OrderDateKey, SUM(SalesAmount) AS TotalSales
	FROM FactInternetSales
	WHERE OrderDateKey > '20020801'
	GROUP BY OrderDateKey
	ORDER BY OrderDateKey;

SQL Data Warehouse unterstützt fast alle T-SQL-Konstrukte, die SQL Server unterstützt. Alle Unterschiede sind in unserer Dokumentation zum [Migrieren von Code][] dokumentiert.

## Nächste Schritte
Da Sie jetzt Gelegenheit hatten, einige Abfragen mit Beispieldaten auszuprobieren, sehen Sie sich nun an, wie Sie für SQL Data Warehouse [entwickeln][], [laden][] oder [migrieren][] können.

<!--Image references-->

<!--Article references-->
[migrieren]: ./sql-data-warehouse-overview-migrate.md
[entwickeln]: ./sql-data-warehouse-overview-develop.md
[laden]: ./sql-data-warehouse-overview-load.md
[Verbindungsdokument]: ./sql-data-warehouse-get-started-connect.md
[Migrieren von Code]: ./sql-data-warehouse-migrate-code.md
[create a SQL Data Warehouse database instance]: ./sql-data-warehouse-get-started-provision.md
[bcp]: ./sql-data-warehouse-load-with-bcp.md
[sqlcmd]: ./sql-data-warehouse-get-started-connect-query-sqlcmd.md

<!--Other Web references-->
[Adventure Works-Beispielskripts für die SQL Data Warehouse]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksSQLDW2012.zip

<!---HONumber=Nov15_HO2-->