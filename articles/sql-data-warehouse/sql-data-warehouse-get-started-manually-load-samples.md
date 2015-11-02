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
   ms.date="10/21/2015"
   ms.author="lodipalm;barbkess"/>

#Laden von Beispieldaten in SQL Data Warehouse

Nachdem Sie eine SQL Data Warehouse-Instanz eingerichtet haben, können Sie auf einfache Weise einige Beispieldaten in die Instanz laden. Die folgenden Informationen helfen Ihnen beim Erstellen eines Datasets mit dem Namen AdventureWorksPDW2012 in Ihrer Datenbank. Dieses Dataset modelliert eine Data Warehouse-Beispielstruktur für ein fiktives Unternehmen namens AdventureWorks. Beachten Sie, dass für die folgenden Schritte BCP installiert sein muss. Wenn BCP derzeit nicht installiert ist, installieren Sie die [Microsoft-Befehlszeilenprogramme für SQL Server][].

1. Klicken Sie zunächst [hier][], um die Beispieldatenskripts herunterzuladen.

2. Nachdem Sie die Datei heruntergeladen haben, extrahieren Sie den Inhalt der Datei "AdventureWorksPDW2012.zip", und öffnen Sie den neuen Ordner "AdventureWorksPDW2012".

3. Bearbeiten Sie die Datei „aw\_create.bat“, und legen Sie am Anfang der Datei die folgenden Werte fest:

   a. **Server**: Der vollqualifizierte Name des Servers, auf dem sich SQL Data Warehouse befindet

   b. **User**: Der Benutzer für den oben angegebenen Server
   
   c. **Password**: Das Kennwort für die Anmeldung beim angegebenen Server
   
   d. **Database**: Der Name der SQL Data Warehouse-Instanz, in die Daten geladen werden sollen
   
   Stellen Sie sicher, dass sich zwischen „=“ und diesen Parametern kein Leerzeichen befindet.
   

4. Führen Sie "aw\_create.bat" aus dem Verzeichnis aus, in dem sich die Datei befindet. Dadurch wird das Schema erstellt und Daten werden mit BCP in alle Tabellen geladen.


## Herstellen einer Verbindung mit und Abfragen der Beispieldatenbank

Wie in der Dokumentation zum [Herstellen der Verbindung][] beschrieben, können Sie mit Visual Studio und SSDT eine Verbindung mit dieser Datenbank herstellen. Nachdem Sie einige Beispieldaten in SQL Data Warehouse geladen haben, können Sie als Einsteig schnell einige Abfragen ausführen.

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
[Migrieren]: ./sql-data-warehouse-overview-migrate.md
[Entwicklung]: ./sql-data-warehouse-overview-develop.md
[Laden]: ./sql-data-warehouse-overview-load.md
[Herstellen der Verbindung]: ./sql-data-warehouse-get-started-connect.md
[Migrieren von Code]: ./sql-data-warehouse-migrate-code.md

<!--MSDN references-->
[Microsoft-Befehlszeilenprogramme für SQL Server]: http://www.microsoft.com/download/details.aspx?id=36433/

<!--Other Web references-->
[hier]: https://migrhoststorage.blob.core.windows.net/sqldwsample/AdventureWorksPDW2012.zip/

<!---HONumber=Oct15_HO4-->