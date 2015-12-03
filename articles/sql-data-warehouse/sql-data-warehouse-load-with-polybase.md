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
   ms.date="11/04/2015"
   ms.author="sahajs;barbkess"/>


# Laden von Daten mit PolyBase
Mit der PolyBase-Technologie können Sie mithilfe von Transact-SQL-Befehlen Daten aus mehreren Quellen abfragen und zusammenführen.

Mit PolyBase können Sie mit den folgenden Schritten Daten abfragen, die im Azure-Blob-Speicher gespeichert sind, und in die SQL Data Warehouse-Datenbank laden:

- Erstellen des Hauptschlüssels und der Anmeldeinformationen für die Datenbank
- Erstellen der PolyBase-Objekte: externe Datenquelle, externes Dateiformat und externe Tabelle 
- Abfragen von im Azure-Blob-Speicher gespeicherten Daten
- Laden von Daten aus dem Azure-Blob-Speicher in SQL Data Warehouse
- Exportieren von Daten aus SQL Data Warehouse in einen Azure-Blob-Speicher


## Voraussetzungen
Für dieses Lernprogramm ist Folgendes erforderlich:

- Azure-Speicherkonto
- Ihre Daten müssen im Azure-Blob-Speicher als durch Trennzeichen getrennte Textdateien gespeichert sein.

Als Erstes erstellen Sie die Objekte, die in PolyBase zum Herstellen einer Verbindung mit dem Azure-Blob-Speicher und zum Abfragen von Daten in diesem erforderlich sind.

> [AZURE.IMPORTANT]Von PolyBase werden die folgenden Azure-Speicherkontotypen unterstützt:
> 
> + Lokal redundanter Standardspeicher (Standard-LRS)
> + Georedundanter Standardspeicher (Standard-GRS)
> + Georedundanter Standardspeicher mit Lesezugriff (Standard-RAGRS)
>
> Die Kontotypen „Zonenredundanter Standardspeicher (Standard-ZRS)“ und „lokal redundanter Storage Premium (Premium-LRS)“ werden von PolyBase NICHT unterstützt. Wenn Sie ein neues Azure-Speicherkonto erstellen, müssen Sie in "Tarif" einen von PolyBase unterstützten Speicherkontotyp auswählen.

## Schritt 1: Speichern von Anmeldeinformationen in Ihrer Datenbank
Um auf den Azure-Blob-Speicher zugreifen zu können, müssen Sie datenbankbezogene Anmeldeinformationen erstellen, in denen Authentifizierungsinformationen für Ihr Azure-Speicherkonto gespeichert sind. Führen Sie diese Schritte aus, um Anmeldeinformationen in Ihrer Datenbank zu speichern.

1. Stellen Sie eine Verbindung mit der SQL Data Warehouse-Datenbank her.
2. Erstellen Sie mithilfe von [CREATE MASTER KEY (Transact-SQL)][] einen Hauptschlüssel für Ihre Datenbank. Wenn Ihre Datenbank bereits über einen Hauptschlüssel verfügt, müssen Sie keinen weiteren Schlüssel erstellen. Dieser Schlüssel wird im nächsten Schritt zum Verschlüsseln des geheimen Schlüssels für Ihre Anmeldeinformationen verwendet.

    ```
    -- Create a E master key
    CREATE MASTER KEY;
    ```

1. Überprüfen Sie, ob bereits Datenbankanmeldeinformationen vorhanden sind. Verwenden Sie hierzu die Systemansicht „sys.database\_credentials“ und nicht „sys.credentials“, da diese Ansicht nur die Serveranmeldeinformationen enthält.

    ```
    -- Check for existing database-scoped credentials.
    SELECT * FROM sys.database_credentials;
    ```

3. Erstellen Sie mit [CREATE CREDENTIAL (Transact-SQL)][] datenbankbezogene Anmeldeinformationen für alle Azure-Speicherkonten, auf die Sie zugreifen möchten. In diesem Beispiel steht IDENTITY für den Anzeigenamen für die Anmeldeinformationen. Er hat keine Auswirkungen auf die Authentifizierung für den Azure-Speicher. SECRET ist der Azure-Speicherkontoschlüssel.

    -- Create a database scoped credential CREATE DATABASE SCOPED CREDENTIAL ASBSecret WITH IDENTITY = 'joe' , Secret = '<azure_storage_account_key>' ; ```

1. Verwenden Sie zum Löschen datenbankbezogener Anmeldeinformationen [DROP CREDENTIAL (Transact-SQL)][]\:

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret
;
```

## Schritt 2: Erstellen einer externen Datenquelle
Die externe Datenquelle ist ein Datenbankobjekt, in dem der Speicherort der Daten des Azure-Blob-Speichers und Ihre Zugriffsinformationen gespeichert sind. Definieren Sie mithilfe von [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][] eine externe Datenquelle für alle Azure-Speicherblobs, auf die Sie zugreifen möchten.

    ```
    -- Create an external data source for an Azure storage blob
    CREATE EXTERNAL DATA SOURCE azure_storage 
    WITH
    (
        TYPE = HADOOP,
        LOCATION ='wasbs://mycontainer@test.blob.core.windows.net',
        CREDENTIAL = ASBSecret
    )
    ;
    ```

Wenn Sie die externe Tabelle löschen müssen, verwenden Sie [DROP EXTERNAL DATA SOURCE][]:

    ```
    -- Drop an external data source
    DROP EXTERNAL DATA SOURCE azure_storage
    ;
    ```

## Schritt 3: Erstellen eines externen Dateiformats
Das externe Dateiformat ist ein Datenbankobjekt, das das Format der externen Daten angibt. In PolyBase können komprimierte und unkomprimierte Daten im durch Trennzeichen getrennten Textformat sowie im Hive RCFILE- und HIVE ORC-Format bearbeitet werden.

Erstellen Sie mithilfe von [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][] das externe Dateiformat. Dieses Beispiel gibt an, dass die Textdatei unkomprimierte Daten sowie Felder enthält, die durch einen senkrechten Strich (|) getrennt sind.

```
-- Create an external file format for a text-delimited file.
-- Data is uncompressed and fields are separated with the
-- pipe character.
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH 
(   
    FORMAT_TYPE = DELIMITEDTEXT, 
    FORMAT_OPTIONS  
    (
        FIELD_TERMINATOR ='|',
        USE_TYPE_DEFAULT = TRUE
    )
)
;
```

Verwenden Sie zum Löschen eines externen Dateiformats [DROP EXTERNAL FILE FORMAT]:

```
-- Dropping external file format
DROP EXTERNAL FILE FORMAT text_file_format
;
```

## Erstellen einer externen Tabelle

Die Definition einer externen Tabelle ähnelt der Definition einer relationalen Tabelle. Der Hauptunterschied liegt im Speicherort und im Format der Daten.

- Die Definition der externen Tabelle wird in Form von Metadaten in der SQL Data Warehouse-Datenbank gespeichert. 
- Die Daten werden an dem durch die Datenquelle angegebenen externen Speicherort gespeichert.

Verwenden Sie zum Definieren der externen Tabelle [CREATE EXTERNAL TABLE (Transact-SQL)][].

Die Option LOCATION gibt den Pfad für die Daten aus dem Stammverzeichnis der Datenquelle an. In diesem Beispiel befinden sich die Daten unter „wasbs://mycontainer@test.blob.core.windows.net/path/Demo/“. Alle Dateien für eine Tabelle müssen sich im gleichen logischen Ordner im Azure-Blobspeicher befinden.

Sie haben auch die Option, Ablehnungsoptionen anzugeben (REJECT\_TYPE, REJECT\_VALUE, REJECT\_SAMPLE\_VALUE), die bestimmen, wie PolyBase fehlerhafte Datensätze verarbeitet, die von der externen Datenquelle empfangen werden.

```
-- Creating an external table for data in Azure blob storage.
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] 
(
     [SensorKey]     int    NOT NULL,
     [CustomerKey]   int    NOT NULL,
     [GeographyKey]  int        NULL,
     [Speed]         float  NOT NULL,
     [YearMeasured]  int    NOT NULL,
)
WITH 
(
    LOCATION    = '/Demo/',
    DATA_SOURCE = azure_storage,
    FILE_FORMAT = text_file_format      
)
;
```

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


```

-- Query Azure storage resident data via external table. 
SELECT * FROM [ext].[CarSensor_Data]
;

```

> [AZURE.NOTE]Eine Abfrage für eine externe Tabelle kann mit folgendem Fehler fehlschlagen: *Abfrage abgebrochen – der maximale Ablehnungsgrenzwert wurde beim Lesen aus einer externen Quelle erreicht*. Dies weist darauf hin, dass die externen Daten *fehlerhafte* Datensätze enthalten. Ein Datensatz gilt als fehlerhaft, wenn die tatsächlichen Datentypen/Anzahl der Spalten nicht den Spaltendefinitionen der externen Tabelle entsprechen, oder wenn die Daten nicht im angegebenen Format der externen Datei vorliegen. Um dieses Problem zu beheben, stellen Sie sicher, dass die Formatdefinitionen der externen Tabelle und Datei richtig und Ihre externen Daten diesen Definitionen entsprechen. Für den Fall, dass eine Teilmenge der Datensätze für externe Daten fehlerhaft sind, können Sie diese Datensätze für Ihre Abfragen mit den Ablehnungsoptionen in „CREATE EXTERNAL TABLE DDL“ ablehnen.


## Laden von Daten aus dem Azure-Blob-Speicher
In diesem Beispiel werden Daten aus dem Azure-Blob-Speicher in die SQL Data Warehouse-Datenbank geladen.

Durch direktes Speichern der Daten entfällt bei Abfragen die für die Datenübertragung verwendete Zeit. Durch Speichern der Daten mit einem Columnstore-Index verbessert sich die Abfrageleistung bei Analyseabfragen um das maximal 10-Fache.

In diesem Beispiel werden die Daten mit der CREATE TABLE AS SELECT-Anweisung geladen. Die neue Tabelle erbt die in der Abfrage benannten Spalten. Sie erbt die Datentypen dieser Spalten aus der Definition der externen Tabelle.

CREATE TABLE AS SELECT ist eine sehr leistungsfähige Transact-SQL-Anweisung, mit der die Daten parallel auf alle Compute-Knoten Ihres SQL Data Warehouse geladen werden. Sie wurde ursprünglich für das Massively Parallel Processing (MPP)-Modul in Analytics Platform System entwickelt und wird jetzt in SQL Data Warehouse verwendet.

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

## Erstellen von Statistiken für die neu geladenen Daten

Azure SQL Data Warehouse bietet noch keine Unterstützung für die automatische Erstellung oder die automatische Aktualisierung von Statistiken. Um die beste Leistung bei Abfragen zu erhalten, ist es wichtig, dass die Statistiken für alle Spalten aller Tabellen nach dem ersten Laden oder nach allen wesentlichen Datenänderungen erstellt werden. Eine ausführliche Erläuterung der Statistik finden Sie im Thema [Statistiken][] in der Entwicklungsgruppe der Themen. Es folgt ein kurzes Beispiel, wie Sie Statistiken für die in diesem Beispiel geladene Tabelle erstellen können.

```
create statistics [SensorKey] on [Customer_Speed] ([SensorKey]);
create statistics [CustomerKey] on [Customer_Speed] ([CustomerKey]);
create statistics [GeographyKey] on [Customer_Speed] ([GeographyKey]);
create statistics [Speed] on [Customer_Speed] ([Speed]);
create statistics [YearMeasured] on [Customer_Speed] ([YearMeasured]);
```

## Exportieren von Daten in Azure-BLOB-Speicher
In diesem Abschnitt wird veranschaulicht, wie Sie Daten aus SQL Data Warehouse in Azure-BLOB-Speicher exportieren. In diesem Beispiel wird CREATE EXTERNAL TABLE AS SELECT verwendet. Dies ist eine sehr leistungsfähige Transact-SQL-Anweisung, mit der die Daten parallel von allen Compute-Knoten exportiert werden.

Im folgenden Beispiel wird die externe Tabelle „Weblogs2014“ erstellt, indem Spaltendefinitionen und Daten aus der Tabelle „dbo.Weblogs“ verwendet werden. Die Definition der externen Tabelle wird in SQL Data Warehouse gespeichert, und die Ergebnisse der SELECT-Anweisung werden in das Verzeichnis „/archive/log2014/“ unter dem BLOB-Container exportiert, der von der Datenquelle angegeben wird. Die Daten werden im angegebenen Textdateiformat exportiert.

```
CREATE EXTERNAL TABLE Weblogs2014 WITH
(
    LOCATION='/archive/log2014/',
    DATA_SOURCE=azure_storage,
    FILE_FORMAT=text_file_format
)
AS
SELECT
    Uri,
    DateRequested
FROM
    dbo.Weblogs
WHERE
    1=1
    AND DateRequested > '12/31/2013'
    AND DateRequested < '01/01/2015';
```


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
[Statistiken]: sql-data-warehouse-develop-statistics.md

<!--MSDN references-->
[supported source/sink]: https://msdn.microsoft.com/library/dn894007.aspx
[copy activity]: https://msdn.microsoft.com/library/dn835035.aspx
[SQL Server destination adapter]: https://msdn.microsoft.com/library/ms141095.aspx
[SSIS]: https://msdn.microsoft.com/library/ms141026.aspx


<!-- External Links -->
[CREATE EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[CREATE EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[CREATE EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx

[DROP EXTERNAL DATA SOURCE (Transact-SQL)]: https://msdn.microsoft.com/de-DE/library/mt146367.aspx
[DROP EXTERNAL FILE FORMAT (Transact-SQL)]: https://msdn.microsoft.com/de-DE/library/mt146379.aspx
[DROP EXTERNAL TABLE (Transact-SQL)]: https://msdn.microsoft.com/de-DE/library/mt130698.aspx

[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/de-DE/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/de-DE/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/de-DE/library/ms189450.aspx

<!---HONumber=Nov15_HO3-->