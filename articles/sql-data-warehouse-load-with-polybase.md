<properties
   pageTitle="Lernprogramm zur Verwendung von PolyBase in SQL Data Warehouse | Microsoft Azure"
   description="Informationen zu PolyBase und Hinweise zur Verwendung in Data Warehouse-Szenarios."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="barbkess"
   manager="jhubbard"
   editor=""/>

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
Stellen Sie eine Verbindung mit der Masterdatenbank auf dem Server her, um einen Datenbank-Hauptschlüssel zu erstellen. Dieser Schlüssel wird im nächsten Schritt zum Verschlüsseln des geheimen Schlüssels für Ihre Anmeldeinformationen verwendet.

```
-- Creating master key
CREATE MASTER KEY ENCRYPTION BY PASSWORD = 'S0me!nfo';
```

Referenzthema: [CREATE MASTER KEY (Transact-SQL)][].

## Erstellen der datenbankbezogenen Anmeldeinformationen
Um auf den Azure-Blob-Speicher zugreifen zu können, müssen Sie datenbankbezogene Anmeldeinformationen erstellen, in denen Authentifizierungsinformationen für Ihr Azure-Speicherkonto gespeichert sind. Stellen Sie eine Verbindung mit Ihrer Data Warehouse-Datenbank her, und erstellen Sie jeweils die datenbankbezogenen Anmeldeinformationen für alle Azure-Speicherkonten, auf die Sie zugreifen möchten. Geben Sie einen Identitätsnamen und Ihren Azure-Speicherkontoschlüssel als geheimen Schlüssel an. Der Identitätsname hat keine Auswirkungen auf die Authentifizierung für Azure Storage.

```
-- Creating credential
CREATE DATABASE SCOPED CREDENTIAL ASBSecret WITH IDENTITY = 'joe', 
	Secret = 'myazurestoragekey==';
```

Referenzthema: [CREATE CREDENTIAL (Transact-SQL)][].

Wenn Sie Ihre Azure-Speicherkontoschlüssel turnusmäßig ändern, müssen Sie die Anmeldeinformationen löschen und mit dem neuen Schlüssel als geheimen Schlüssel neu erstellen.

```
-- Dropping credential
DROP DATABASE SCOPED CREDENTIAL ASBSecret;
```

Referenzthema: [DROP CREDENTIAL (Transact-SQL)][].


## Erstellen einer externen Datenquelle
Die externe Datenquelle ist ein Datenbankobjekt, in dem der Speicherort der Daten des Azure-Blob-Speichers und Ihre Zugriffsinformationen gespeichert sind. Sie müssen eine externe Datenquelle für jeden Azure-Speichercontainer definieren, auf den Sie zugreifen möchten.

```
-- Creating external data source (Azure Blob Storage) 
CREATE EXTERNAL DATA SOURCE azure_storage 
WITH (
	TYPE = HADOOP, 
       LOCATION ='wasbs://mycontainer@ test.blob.core.windows.net/path’,
      CREDENTIAL = ASBSecret
);
```

Referenzthema: [CREATE EXTERNAL DATA SOURCE (Transact-SQL)][].

## Erstellen eines externen Dateiformats
Das externe Dateiformat ist ein Datenbankobjekt, das das Format der externen Daten angibt. In diesem Beispiel enthält die Textdatei unkomprimierte Daten und Felder, die durch den senkrechten Strich ("|") getrennt sind.

```
-- Creating external file format (delimited text file)
CREATE EXTERNAL FILE FORMAT text_file_format 
WITH (
	FORMAT_TYPE = DELIMITEDTEXT, 
	FORMAT_OPTIONS (
		FIELD_TERMINATOR ='|', 
		USE_TYPE_DEFAULT = TRUE
	)
);
```

In PolyBase können komprimierte und unkomprimierte Daten im durch Trennzeichen getrennten Textformat sowie im Hive RCFILE- und HIVE ORC-Format bearbeitet werden.

Referenzthema: [CREATE EXTERNAL FILE FORMAT (Transact-SQL)][].

## Erstellen einer externen Tabelle

Die Definition einer externen Tabelle ähnelt der Definition einer relationalen Tabelle. Der Hauptunterschied liegt im Speicherort und im Format der Daten. Die Definition der externen Tabelle wird in der SQL Data Warehouse-Datenbank gespeichert. Die Daten werden in dem durch die Datenquelle angegebenen Speicherort gespeichert.

Die Option LOCATION gibt den Pfad für die Daten aus dem Stammverzeichnis der Datenquelle an. In diesem Beispiel befinden sich die Daten unter "wasbs://mycontainer@ test.blob.core.windows.net/path/Demo/".

```
-- Creating external table pointing to file stored in Azure Storage
CREATE EXTERNAL TABLE [ext].[CarSensor_Data] (
    [SensorKey] int NOT NULL, 
    [CustomerKey] int NOT NULL, 
    [GeographyKey] int NULL, 
    [Speed] float NOT NULL, 
    [YearMeasured] int NOT NULL
)
WITH (LOCATION='/Demo/',
      DATA_SOURCE = azure_storage,
      FILE_FORMAT = text_file_format,      
);
```

> [AZURE.NOTE]Beachten Sie, dass zum gegenwärtigen Zeitpunkt keine Statistik für eine externe Tabelle erstellt werden kann.

Alle Dateien für eine Tabelle müssen sich im gleichen logischen Ordner im Azure-Blob befinden. Unterteilen Sie als bewährte Methode Ihre Daten im Azure-Speicher möglichst in Dateien mit einer Größe von nicht mehr als 1 GB, um die parallele Verarbeitung mit SQL Data Warehouse sicherzustellen.

Referenzthema: [CREATE EXTERNAL TABLE (Transact-SQL)][].

Die soeben erstellten Objekte werden in der SQL Data Warehouse-Datenbank gespeichert. Sie können sie im Objekt-Explorer von SQL Server Data Tools (SSDT) anzeigen.


## Abfragen von Daten im Azure-Blob-Speicher
Bei Abfragen für externe Tabellen wird einfach der Tabellenname verwendet, als handle es sich um relationale Tabellen.

Hier sehen Sie eine Ad-hoc-Abfrage, in der Daten für Versicherungskunden, die in SQL Data Warehouse gespeichert sind, mit Daten für Kfz-Sensoren zusammengeführt werden, die im Azure-Blob-Speicher gespeichert sind. Das Ergebnis zeigt die Fahrer, die schneller als andere Fahrer fahren.

```
-- Join SQL Data Warehouse relational data with Azure storage data. 
SELECT 
    [Insured_Customers].[FirstName],
    [Insured_Customers].[LastName],
    [Insured_Customers].[YearlyIncome],
    [CarSensor_Data].[Speed]
FROM [dbo].[Insured_Customers] INNER JOIN [ext].[CarSensor_Data]
ON [Insured_Customers].[CustomerKey] = [CarSensor_Data].[CustomerKey]
WHERE [CarSensor_Data].[Speed] > 60 
ORDER BY [CarSensor_Data].[Speed] desc;
```

## Laden von Daten aus dem Azure-Blob-Speicher
In diesem Beispiel werden Daten aus dem Azure-Blob-Speicher in die SQL Data Warehouse-Datenbank geladen.

Durch direktes Speichern der Daten entfällt bei Abfragen die für die Datenübertragung verwendete Zeit. Durch Speichern der Daten mit einem Columnstore-Index verbessert sich die Abfrageleistung bei Analyseabfragen um das maximal 10-Fache.

In diesem Beispiel werden die Daten mit der CREATE TABLE AS SELECT-Anweisung geladen. Die neue Tabelle erbt die in der Abfrage benannten Spalten. Sie erbt die Datentypen dieser Spalten aus der Definition der externen Tabelle.

CREATE TABLE AS SELECT ist eine leistungsstarke Transact-SQL-Anweisung, die INSERT...SELECT ersetzt. Sie wurde ursprünglich für das Massively Parallel Processing (MPP)-Modul in Analytics Platform System entwickelt und wird jetzt in SQL Data Warehouse verwendet.

```
-- Load data from Azure blob storage to SQL Data Warehouse 

CREATE TABLE [dbo].[Customer_Speed]
WITH (
	CLUSTERED COLUMNSTORE INDEX
	DISTRIBUTION = HASH([CarSensor_Data].[CustomerKey])
	)
AS SELECT * from [ext].[CarSensor_Data];
```

Siehe [CREATE TABLE AS SELECT (Transact-SQL)][].


## Einschränkungen
Beim Laden von Daten mit PolyBase wird nur die UTF-8-Codierung unterstützt. Bei anderen Codierungsformaten, z. B. UTF-16, können Sie Daten mit SSIS, Azure Data Factory oder dem Befehlszeilenprogramm bcp in SQL Data Warehouse laden.

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
[CREATE TABLE AS SELECT (Transact-SQL)]: https://msdn.microsoft.com/library/mt204041.aspx
[CREATE MASTER KEY (Transact-SQL)]: https://msdn.microsoft.com/de-de/library/ms174382.aspx
[CREATE CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/de-de/library/ms189522.aspx
[DROP CREDENTIAL (Transact-SQL)]: https://msdn.microsoft.com/de-de/library/ms189450.aspx

<!---HONumber=July15_HO1-->