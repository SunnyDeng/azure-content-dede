<properties
   pageTitle="Verwenden von bcp zum Laden von Daten in SQL Data Warehouse | Microsoft Azure"
   description="Erfahren Sie, was bcp ist und wie es in Data Warehouse-Szenarios verwendet wird."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="TwoUnder"
   manager="barbkess"
   editor=""/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/07/2016"
   ms.author="mausher;barbkess;sonyama"/>


# Laden von Daten mit bcp

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)


**[bcp][]** ist ein Befehlszeilen-Dienstprogramm zum Massenladen, das es Ihnen ermöglicht, Daten zwischen SQL Server, Datendateien und SQL Data Warehouse zu kopieren. Verwenden sie bcp zum Importieren großer Mengen an neuen Zeilen in SQL Data Warehouse-Tabellen oder zum Exportieren von Daten aus SQL Server-Tabellen in Datendateien. bcp erfordert außer bei Verwendung mit der queryout-Option keine Kenntnisse von Transact-SQL.

bcp bietet eine schnelle und einfache Möglichkeit, um kleinere DataSets in und aus einer SQL Data Warehouse-Datenbank zu verschieben. Die genaue Empfehlung für die mit bcp zu ladende/extrahierte Datenmenge hängt von der Netzwerkverbindung mit dem Azure-Rechenzentrum ab. Im Allgemeinen können Dimensionstabellen geladen und extrahiert werden, relativ große Faktentabellen können jedoch viel Zeit zum Laden oder Extrahieren benötigen.

Mit bcp haben Sie folgende Möglichkeiten:

- Verwenden eines einfachen Befehlszeilenprogramms zum Laden von Daten in SQL Data Warehouse.
- Verwenden eines einfachen Befehlszeilenprogramms zum Extrahieren von Daten aus SQL Data Warehouse.

In diesem Lernprogramm lernen Sie Folgendes:

- Importieren von Daten in eine Tabelle mithilfe des „bcp in“-Befehls
- Exportieren von Daten aus einer Tabelle mithilfe des „bcp out“-Befehls

>[AZURE.VIDEO loading-data-into-azure-sql-data-warehouse-with-bcp]

## Voraussetzungen

Für dieses Lernprogramm ist Folgendes erforderlich:

- Eine SQL Data Warehouse-Datenbank
- Eine Installation des bcp-Befehlszeilenprogramms
- Eine Installation des SQLCMD-Befehlszeilenprogramms

>[AZURE.NOTE]Sie können die Dienstprogramme bcp und sqlcmd im [Microsoft Download Center][] herunterladen.

## Importieren von Daten in SQL Data Warehouse

In diesem Lernprogramm erstellen Sie eine Tabelle in Azure SQL Data Warehouse und importieren Daten in die Tabelle.

### Schritt 1: Erstellen einer Tabelle in Azure SQL Data Warehouse

Verbinden Sie über eine Eingabeaufforderung die Instanz mit dem folgenden Befehl (ersetzen Sie die Werte nach Bedarf):

```
sqlcmd.exe -S <server name> -d <database name> -U <username> -P <password> -I
```
Nachdem die Verbindung hergestellt wurde, kopieren Sie das folgende Tabellenskript in die sqlcmd-Eingabeaufforderung, und drücken Sie dann die EINGABETASTE:

```
CREATE TABLE DimDate2 
(
    DateId INT NOT NULL,
    CalendarQuarter TINYINT NOT NULL,
    FiscalQuarter TINYINT NOT NULL
)
WITH 
(
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
);
GO
```
>[AZURE.NOTE]Weitere Informationen zu den verfügbaren Optionen für die WITH-Klausel finden Sie unter dem Thema [Tabellenentwurf][] in der Entwicklungsgruppe der Themen.

### Schritt 2: Erstellen einer Quelldatendatei

Öffnen Sie Editor, und kopieren Sie die folgenden Datenzeilen in eine neue Datei.

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

Speichern Sie diese im lokalen temporären Verzeichnis unter "C:\\Temp\\DimDate2.txt".

> [AZURE.NOTE]Es ist wichtig, daran zu denken, dass bcp.exe die UTF-8-Codierung nicht unterstützt. Verwenden Sie ASCII-codierte Dateien oder die UTF-16-Codierung für Ihre Dateien, wenn Sie bcp.exe verwenden.

### Schritt 3: Herstellen einer Verbindung und Importieren von Daten
Sie können mit bcp eine Verbindung herstellen und die Daten mit dem folgenden Befehl importieren (ersetzen Sie die Werte nach Bedarf):

```
bcp DimDate2 in C:\Temp\DimDate2.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t  ','
```

Sie können überprüfen, ob die Daten geladen wurden, indem Sie wie zuvor eine Verbindung mit sqlcmd herstellen und den folgenden TSQL-Befehl ausführen:

```
SELECT * FROM DimDate2 ORDER BY 1;
GO
```

Daraufhin sollten Sie folgende Ergebnisse erhalten:

DateId |CalendarQuarter |FiscalQuarter
----------- |--------------- |-------------
20150101 |1 |3
20150201 |1 |3
20150301 |1 |3
20150401 |2 |4
20150501 |2 |4
20150601 |2 |4
20150701 |3 |1
20150801 |3 |1
20150801 |3 |1
20151001 |4 |2
20151101 |4 |2
20151201 |4 |2

### Schritt 4: Erstellen von Statistiken für die neu geladenen Daten 

Azure SQL Data Warehouse bietet noch keine Unterstützung für die automatische Erstellung oder die automatische Aktualisierung von Statistiken. Um die beste Leistung bei Abfragen zu erhalten, ist es wichtig, dass die Statistiken für alle Spalten aller Tabellen nach dem ersten Laden oder nach allen wesentlichen Datenänderungen erstellt werden. Eine ausführliche Erläuterung der Statistik finden Sie im Thema [Statistiken][] in der Entwicklungsgruppe der Themen. Es folgt ein kurzes Beispiel, wie Sie Statistiken für die in diesem Beispiel geladene Tabelle erstellen können.

Führen Sie die folgenden CREATE STATISTICS-Anweisungen über eine sqlcmd-Eingabeaufforderung aus:

```
create statistics [DateId] on [DimDate2] ([DateId]);
create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
GO
```

## Exportieren von Daten aus SQL Data Warehouse
In diesem Lernprogramm erstellen Sie eine Datendatei aus einer Tabelle in SQL Data Warehouse. Wir werden die oben erstellten Daten in eine neue Datendatei namens "DimDate2\_export.txt" exportieren.

### Schritt 1: Exportieren der Daten

Sie können mit dem Dienstprogramm bcp eine Verbindung herstellen und die Daten mit dem folgenden Befehl exportieren (ersetzen Sie die Werte nach Bedarf):

```
bcp DimDate2 out C:\Temp\DimDate2_export.txt -S <Server Name> -d <Database Name> -U <Username> -P <password> -q -c -t ','
```
Sie können überprüfen, ob die Daten ordnungsgemäß exportiert wurden, indem Sie die neue Datei öffnen. Die Daten in der Datei sollten mit folgendem Text übereinstimmen:

```
20150301,1,3
20150501,2,4
20151001,4,2
20150201,1,3
20151201,4,2
20150801,3,1
20150601,2,4
20151101,4,2
20150401,2,4
20150701,3,1
20150901,3,1
20150101,1,3
```

>[AZURE.NOTE]Aufgrund der Struktur verteilter Systeme unterscheidet sich die Reihenfolge der Daten in den SQL Data Warehouse-Datenbanken möglicherweise. Sie können optional den queryout-Parameter verwenden, um anzugeben, welche Transact-SQL-Abfrage ausgeführt werden soll.

## Nächste Schritte
Eine Übersicht über das Laden finden Sie unter [Laden von Daten in SQL Data Warehouse][]. Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht für SQL Data Warehouse][].

<!--Image references-->

<!--Article references-->

[Laden von Daten in SQL Data Warehouse]: ./sql-data-warehouse-overview-load.md
[Entwicklungsübersicht für SQL Data Warehouse]: ./sql-data-warehouse-overview-develop.md
[Tabellenentwurf]: ./sql-data-warehouse-develop-table-design.md
[Statistiken]: ./sql-data-warehouse-develop-statistics.md


<!--MSDN references-->
[bcp]: https://msdn.microsoft.com/library/ms162802.aspx


<!--Other Web references-->
[Microsoft Download Center]: http://www.microsoft.com/download/details.aspx?id=36433

<!---HONumber=AcomDC_0114_2016-->