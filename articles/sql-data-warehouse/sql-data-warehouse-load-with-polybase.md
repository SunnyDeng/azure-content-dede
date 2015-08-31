<properties
   pageTitle="Lernprogramm zur Verwendung von PolyBase in SQL Data Warehouse | Microsoft Azure"
   description="Informationen zu PolyBase und Hinweise zur Verwendung in Data Warehouse-Szenarios."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor="jrowlandjones"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="05/09/2015"
   ms.author="sahajs;barbkess"/>


# Laden von Daten mit PolyBase
Mit der PolyBase-Technologie können Sie mithilfe von Transact-SQL-Befehlen Daten aus mehreren Quellen abfragen und zusammenführen.

Mit PolyBase können Sie mit den folgenden Schritten Daten abfragen, die im Azure-Blob-Speicher gespeichert sind, und in die SQL Data Warehouse-Datenbank laden:

- Erstellen des Hauptschlüssels und der Anmeldeinformationen für die Datenbank
- Erstellen der PolyBase-Objekte: externe Datenquelle, externes Dateiformat und externe Tabelle 
- Abfragen von im Azure-Blob-Speicher gespeicherten Daten
- Laden von Daten aus dem Azure-Blob-Speicher in SQL Data Warehouse


## Voraussetzungen
Für dieses Lernprogramm ist Folgendes erforderlich:

- Azure-Speicherkonto
- Ihre Daten müssen im Azure-Blob-Speicher als durch Trennzeichen getrennte Textdateien gespeichert sein.

Als Erstes erstellen Sie die Objekte, die in PolyBase zum Herstellen einer Verbindung mit dem Azure-Blob-Speicher und zum Abfragen von Daten in diesem erforderlich sind.

## Erstellen des Datenbank-Hauptschlüssels
Stellen Sie eine Verbindung mit der Benutzerdatenbank auf dem Server her, um einen Datenbank-Hauptschlüssel zu erstellen. Dieser Schlüssel wird im nächsten Schritt zum Verschlüsseln des geheimen Schlüssels für Ihre Anmeldeinformationen verwendet.

```
-- Creating master key
CREATE MASTER KEY;
```

Referenzthema: [CREATE MASTER KEY (Transact-SQL)][].

## Erstellen der datenbankbezogenen Anmeldeinformationen
Um auf den Azure-Blob-Speicher zugreifen zu können, müssen Sie datenbankbezogene Anmeldeinformationen erstellen, in denen Authentifizierungsinformationen für Ihr Azure-Speicherkonto gespeichert sind. Stellen Sie eine Verbindung mit Ihrer Data Warehouse-Datenbank her, und erstellen Sie jeweils die datenbankbezogenen Anmeldeinformationen für alle Azure-Speicherkonten, auf die Sie zugreifen möchten. Geben Sie einen Identitätsnamen und Ihren Azure-Speicherkontoschlüssel als geheimen Schlüssel an. Der Identitätsname hat keine Auswirkungen auf die Authentifizierung für Azure Storage.

Um festzustellen, ob im Datenbankbereich bereits Anmeldeinformationen vorhanden sind, verwenden Sie „sys.database\_credentials“, nicht „sys.credentials“, da hier lediglich die Serveranmeldeinformationen angezeigt werden.

```
-- Check for existing database-scoped credentials.
SELECT * FROM sys.database_credentials;

-- Create a database scoped credential
CREATE DATABASE SCOPED CREDENTIAL ASBSecret 
WITH IDENTITY = 'joe'
,    Secret = 'myazurestoragekey=='
;
```

Referenzthema: [CREATE CREDENTIAL (Transact-SQL)][].

Zum Löschen datenbankbezogener Anmeldeinformationen verwenden Sie einfach die folgende Syntax:

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret
;
```

Referenzthema: [DROP CREDENTIAL (Transact-SQL)][].

## Erstellen einer externen Datenquelle
Die externe Datenquelle ist ein Datenbankobjekt, in dem der Speicherort der Daten des Azure-Blob-Speichers und Ihre Zugriffsinformationen gespeichert sind. Sie müssen eine externe Datenquelle für jeden Azure-Speichercontainer definieren, auf den Sie zugreifen möchten.

```
-- Creating external data source (Azure Blob Storage) 
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH
(
    TYPE = HADOOP
,   LOCATION ='wasbs://mycontainer@ test.blob.core.windows.net/path'
,   CREDENTIAL = ASBSecret
)
;
```

Referenzthema: [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][].

Die Syntax zum Löschen der externen Datenquelle ist wie folgt:

```
-- Dropping external data source
DROP EXTERNAL DATA SOURCE azure_storage
;
```

Referenzthema: [DROP EXTERNAL DATA SOURCE (Transact-SQL)][].

## Erstellen eines externen Dateiformats
Das externe Dateiformat ist ein Datenbankobjekt, das das Format der externen Daten angibt. In diesem Beispiel enthält die Textdatei unkomprimierte Daten und Felder, die durch den senkrechten Strich ("|") getrennt sind.

```
-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(   
    FORMAT_TYPE = DELIMITEDTEXT 
,	FORMAT_OPTIONS  (
                        FIELD_TERMINATOR ='|'
                    ,   USE_TYPE_DEFAULT = TRUE
                    )
)
;
```

In PolyBase können komprimierte und unkomprimierte Daten im durch Trennzeichen getrennten Textformat sowie im Hive RCFILE- und HIVE ORC-Format bearbeitet werden.

Referenzthema: [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][].

Die Syntax zum Löschen eines externen Dateiformats ist wie folgt:

```
-- Dropping external file format...
DROP EXTERNAL FILE FORMAT text_file_format
;
```
Referenzthema: [DROP EXTERNAL FILE FORMAT (Transact-SQL)][].

## Erstellen einer externen Tabelle

Die Definition einer externen Tabelle ähnelt der Definition einer relationalen Tabelle. Der Hauptunterschied liegt im Speicherort und im Format der Daten. Die Definition der externen Tabelle wird in der SQL Data Warehouse-Datenbank gespeichert. Die Daten werden in dem durch die Datenquelle angegebenen Speicherort gespeichert.

Die Option LOCATION gibt den Pfad für die Daten aus dem Stammverzeichnis der Datenquelle an. In diesem Beispiel befinden sich die Daten unter "wasbs://mycontainer@ test.blob.core.windows.net/path/Demo/". Alle Dateien für eine Tabelle müssen sich im gleichen logischen Ordner im Azure-Blob befinden.

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] 
(
     [SensorKey]     int    NOT NULL 
,    [CustomerKey]   int    NOT NULL 
,    [GeographyKey]  int        NULL 
,    [Speed]         float  NOT NULL 
,    [YearMeasured]  int    NOT NULL
)
WITH 
(
    LOCATION    = '/Demo/'
,   DATA_SOURCE = azure_storage
,   FILE_FORMAT = text_file_format      
)
;
```

> [AZURE.NOTE]Beachten Sie, dass zum gegenwärtigen Zeitpunkt keine Statistik für eine externe Tabelle erstellt werden kann.

Referenzthema: [CREATE EXTERNAL TABLE (Transact-SQL)][].

Die soeben erstellten Objekte werden in der SQL Data Warehouse-Datenbank gespeichert. Sie können sie im Objekt-Explorer von SQL Server Data Tools (SSDT) anzeigen.

Zum Löschen einer externen Tabelle müssen Sie die folgende Syntax verwenden:

```
--Dropping external table
DROP EXTERNAL TABLE [ext].[CarSensor_Data]
;
```

> [AZURE.NOTE]Wenn Sie eine externe Tabelle löschen, müssen Sie `DROP EXTERNAL TABLE` verwenden. `DROP TABLE` **kann nicht** verwendet werden.

Referenzthema: [DROP EXTERNAL TABLE (Transact-SQL)][].

Es ist auch zu erwähnen, dass externe Tabellen sowohl in `sys.tables`- als auch insbesondere in `sys.external_tables`-Katalogsichten angezeigt werden.

## Wechseln von Speicherschlüsseln

Von Zeit zu Zeit sollten Sie den Zugriffsschlüssel für den Blob-Speicher aus Sicherheitsgründen ändern.

Die eleganteste Möglichkeit zum Ausführen dieser Aufgabe bietet ein Prozess, der als "Wechseln der Schlüssel" bezeichnet wird. Sie haben wahrscheinlich bemerkt, dass Sie über zwei Speicherschlüssel für Ihr Blob-Speicherkonto verfügen, sodass Sie wechseln können.

Das Wechseln Ihrer Azure-Speicherkontoschlüssel erfolgt in drei einfachen Schritten:

1. Erstellen Sie zweite datenbankbezogene Anmeldeinformationen basierend auf dem sekundären Speicherzugriffsschlüssel.
2. Erstellen Sie eine zweite externe Datenquelle basierend auf diesen neuen Anmeldeinformationen.
3. Löschen und erstellen Sie die externen Tabellen, die auf die neue externe Datenquelle verweisen.

Wenn Sie alle Ihre externen Tabellen zur neuen externen Datenquelle migriert haben, können Sie die Bereinigungsaufgaben ausführen:
 
1. Löschen Sie die erste externe Datenquelle.
2. Löschen Sie die ersten datenbankbezogenen Anmeldeinformationen basierend auf dem primären Speicherzugriffsschlüssel.
3. Melden Sie sich bei Azure an, und generieren Sie den primären Zugriffsschlüssel neu, sodass er für das nächste Mal bereitsteht.

## Abfragen von Daten im Azure-Blob-Speicher
Bei Abfragen für externe Tabellen wird einfach der Tabellenname verwendet, als handle es sich um relationale Tabellen.

Hier sehen Sie eine Ad-hoc-Abfrage, in der Daten für Versicherungskunden, die in SQL Data Warehouse gespeichert sind, mit Daten für Kfz-Sensoren zusammengeführt werden, die im Azure-Blob-Speicher gespeichert sind. Das Ergebnis zeigt die Fahrer, die schneller als andere Fahrer fahren.

```
-- Join SQL Data Warehouse relational data with Azure storage data. 
SELECT 
      [Insured_Customers].[FirstName]
,     [Insured_Customers].[LastName]
,     [Insured_Customers].[YearlyIncome]
,     [CarSensor_Data].[Speed]
FROM  [dbo].[Insured_Customers] 
JOIN  [ext].[CarSensor_Data]         ON [Insured_Customers].[CustomerKey] = [CarSensor_Data].[CustomerKey]
WHERE [CarSensor_Data].[Speed] > 60 
ORDER BY [CarSensor_Data].[Speed] DESC
;
```

## Laden von Daten aus dem Azure-Blob-Speicher
In diesem Beispiel werden Daten aus dem Azure-Blob-Speicher in die SQL Data Warehouse-Datenbank geladen.

Durch direktes Speichern der Daten entfällt bei Abfragen die für die Datenübertragung verwendete Zeit. Durch Speichern der Daten mit einem Columnstore-Index verbessert sich die Abfrageleistung bei Analyseabfragen um das maximal 10-Fache.

In diesem Beispiel werden die Daten mit der CREATE TABLE AS SELECT-Anweisung geladen. Die neue Tabelle erbt die in der Abfrage benannten Spalten. Sie erbt die Datentypen dieser Spalten aus der Definition der externen Tabelle.

CREATE TABLE AS SELECT ist eine leistungsstarke Transact-SQL-Anweisung, die INSERT...SELECT ersetzt. Sie wurde ursprünglich für das Massively Parallel Processing (MPP)-Modul in Analytics Platform System entwickelt und wird jetzt in SQL Data Warehouse verwendet.

```
-- Load data from Azure blob storage to SQL Data Warehouse 

CREATE TABLE [dbo].[Customer_Speed]
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX
,	DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
)
AS 
SELECT * 
FROM   [ext].[CarSensor_Data]
;
```

Siehe [CREATE TABLE AS SELECT (Transact-SQL)][].


## Umgehen der UTF-8-Anforderung von PolyBase
Derzeit unterstützt PolyBase das Laden von Datendateien mit UTF-8-Codierung. Da UTF-8 die gleiche Zeichencodierung wie ASCII-verwendet, unterstützt PolyBase auch das Laden ASCII-codierter Daten. PolyBase unterstützt jedoch keine andere Zeichencodierung, wie z. B. UTF-16/Unicode- oder erweiterte ASCII-Zeichen. Erweiterte ASCII-Zeichen umfassen auch Zeichen mit Akzenten, z. B. die im Deutschen häufig verwendeten Umlaute.

Die beste Möglichkeit zum Umgehen dieser Anforderung ist das erneute Schreiben mit UTF-8-Codierung.

Dazu gibt es verschiedene Möglichkeiten. Nachfolgend sind zwei Methoden angegeben, bei denen Powershell verwendet wird:

### Einfaches Beispiel für kleine Dateien

Nachfolgend sehen Sie ein einfaches einzeiliges Powershell-Skript, das die Datei erstellt.
 
```
Get-Content <input_file_name> -Encoding Unicode | Set-Content <output_file_name> -Encoding utf8
```

Obwohl dies eine einfache Methode zum erneuten Codieren der Daten darstellt, ist sie keinesfalls die effizienteste. Das folgende Beispiel für E/A-Streaming ist sehr viel schneller und erzielt dasselbe Ergebnis.

### E/A-Streaming-Beispiel für größere Dateien

Das folgende Codebeispiel ist komplexer, doch da die Datenzeilen aus der Quelle in das Ziel gestreamt werden, ist es wesentlich effizienter. Verwenden Sie diese Methode für größere Dateien.

```
#Static variables
$ascii = [System.Text.Encoding]::ASCII
$utf16le = [System.Text.Encoding]::Unicode
$utf8 = [System.Text.Encoding]::UTF8
$ansi = [System.Text.Encoding]::Default
$append = $False

#Set source file path and file name
$src = [System.IO.Path]::Combine("C:\input_file_path","input_file_name.txt")

#Set source file encoding (using list above)
$src_enc = $ansi

#Set target file path and file name
$tgt = [System.IO.Path]::Combine("C:\output_file_path","output_file_name.txt")

#Set target file encoding (using list above)
$tgt_enc = $utf8

$read = New-Object System.IO.StreamReader($src,$src_enc)
$write = New-Object System.IO.StreamWriter($tgt,$append,$tgt_enc)

while ($read.Peek() -ne -1)
{
    $line = $read.ReadLine();
    $write.WriteLine($line);
}
$read.Close()
$read.Dispose()
$write.Close()
$write.Dispose()
```

## Nächste Schritte
Weitere Hinweise zur Entwicklung finden Sie in der [Entwicklungsübersicht][].

<!--Image references-->

<!--Article references-->
[Load data with bcp]: sql-data-warehouse-load-with-bcp.md
[Load with PolyBase]: sql-data-warehouse-load-with-polybase.md
[solution partners]: sql-data-warehouse-solution-partners.md
[Entwicklungsübersicht]: sql-data-warehouse-overview-develop.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/de-de/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/de-de/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/de-de/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/de-de/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/de-de/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/de-de/library/ms189450.aspx

<!---HONumber=August15_HO8-->