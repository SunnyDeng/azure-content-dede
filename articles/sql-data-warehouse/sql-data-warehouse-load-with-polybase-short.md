<properties
   pageTitle="Laden von Daten mit PolyBase – Tutorial | Microsoft Azure"
   description="Erfahren Sie, wie Sie PolyBase zum Laden von Daten in SQL Data Warehouse verwenden."
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="sahajs"
   manager="jhubbard"
   editor="sahajs"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="11/04/2015"
   ms.author="sahajs;barbkess"/>


# Laden von Daten mit PolyBase

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-load-with-polybase-short.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

In diesem Tutorial erfahren Sie, wie Sie Daten in Azure SQL Data Warehouse laden, indem Sie PolyBase verwenden. Weitere Informationen zu PolyBase finden Sie unter [PolyBase in SQL Data Warehouse – Tutorial][].


## Voraussetzungen
Für dieses Tutorial ist Folgendes erforderlich:

- SQL Data Warehouse-Datenbank
- Azure-Speicherkonto


## Schritt 1: Erstellen einer Quelldatendatei
Öffnen Sie Editor, und kopieren Sie die folgenden Datenzeilen in eine neue Datei. Speichern Sie diese im lokalen temporären Verzeichnis unter "C:\\Temp\\DimDate2.txt".

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


## Schritt 2: Migrieren von Daten zum Azure-BLOB-Speicher

- Laden Sie die [neueste Version von AzCopy][] herunter.
- Öffnen Sie ein Befehlsfenster, und navigieren Sie zum Installationsverzeichnis von AzCopy auf Ihrem Computer, in dem sich die Datei „AzCopy.exe“ befindet. Standardmäßig befindet sich das Installationsverzeichnis auf einem 64-Bit-Windows-Computer unter „%Programme(x86)%\\Microsoft SDKs\\Azure\\AzCopy\\“.
- Führen Sie den folgenden Befehl aus, um die Datei hochzuladen. Geben Sie Ihren Azure-Speicherkontoschlüssel für „/DestKey“ an.

```
.\AzCopy.exe /Source:C:\Temp\ /Dest:https://pbdemostorage.blob.core.windows.net/datacontainer/datedimension/ /DestKey:<azure_storage_account_key> /Pattern:DimDate2.txt
```

Weitere Informationen zu AzCopy finden Sie unter [Erste Schritte mit dem Befehlszeilenprogramm AzCopy][].


## Schritt 3: Erstellen von externen Tabellen

Als Nächstes müssen Sie externe Tabellen in SQL Data Warehouse erstellen, um auf die Daten im Azure-BLOB-Speicher zu verweisen. Verwenden Sie die folgenden Schritte, um eine externe Tabelle zu erstellen:

- [Create Master Key][]\: Dient zum Verschlüsseln des geheimen Schlüssels Ihrer datenbankbezogenen Anmeldeinformationen.
- [Create Database Scoped Credential]\: Dient zum Angeben der Authentifizierungsinformationen für Ihr Azure-Speicherkonto.
- [Create External Data Source]\: Dient zum Angeben des Speicherorts Ihres Azure-BLOB-Speichers.
- [Create External File Format]\: Dient zum Angeben des Layouts Ihrer Daten.
- [Create External Table]\: Dient zum Verweisen auf die Azure-Speicherdaten.


```

-- A: Create Master Key
-- Required to encrypt the credential secret in the next step.
CREATE MASTER KEY;


-- B: Create Database Scoped Credential
-- Provide the Azure storage account key. The identity name does not affect authentication to Azure storage.
CREATE DATABASE SCOPED CREDENTIAL AzureStorageCredential 
WITH IDENTITY = 'user', 
SECRET = '<azure_storage_account_key>';


-- C: Create External Data Source
-- Specify location and credential to access your Azure blob storage.
CREATE EXTERNAL DATA SOURCE AzureStorage 
WITH (	
		TYPE = Hadoop, 
		LOCATION = 'wasbs://datacontainer@pbdemostorage.blob.core.windows.net',
		CREDENTIAL = AzureStorageCredential
); 


-- D: Create External File format 
-- Specify the layout of data stored in Azure storage blobs. 
CREATE EXTERNAL FILE FORMAT TextFile 
WITH (FORMAT_TYPE = DelimitedText, 
FORMAT_OPTIONS (FIELD_TERMINATOR = ','));


-- E: Create External Table
-- To reference data stored in Azure blob storage.
CREATE EXTERNAL TABLE dbo.DimDate2External (
	DateId INT NOT NULL, 
	CalendarQuarter TINYINT NOT NULL, 
	FiscalQuarter TINYINT NOT NULL
)
WITH (
		LOCATION='datedimension/', 
		DATA_SOURCE=AzureStorage, 
		FILE_FORMAT=TextFile
);


-- Run a query on external table to confirm that the Azure Storage data can be referenced.
SELECT count(*) FROM dbo.DimDate2External;

```



## Schritt 4: Laden von Daten in SQL Data Warehouse

- Führen Sie die Anweisung [CREATE TABLE AS SELECT (Azure SQL Data Warehouse)][] aus, um die Daten in eine neue Tabelle zu laden. Die neue Tabelle erbt die in der Abfrage benannten Spalten. Sie erbt die Datentypen dieser Spalten aus der Definition der externen Tabelle. 
- Verwenden Sie die Anweisung INSERT...SELECT, um die Daten in eine vorhandene Tabelle zu laden.  


```

-- Load the data from Azure blob storage to SQL Data Warehouse

CREATE TABLE dbo.DimDate2
WITH 
(   
    CLUSTERED COLUMNSTORE INDEX,
    DISTRIBUTION = ROUND_ROBIN
)
AS 
SELECT * 
FROM   [dbo].[DimDate2External];

```


## Schritt 5: Erstellen von Statistiken für die neu geladenen Daten 

Azure SQL Data Warehouse bietet noch keine Unterstützung für die automatische Erstellung oder die automatische Aktualisierung von Statistiken. Um die beste Leistung bei Abfragen zu erhalten, ist es wichtig, dass die Statistiken für alle Spalten aller Tabellen nach dem ersten Laden oder nach allen wesentlichen Datenänderungen erstellt werden. Eine ausführliche Erläuterung der Statistik finden Sie im Thema [Statistiken][] in der Entwicklungsgruppe der Themen. Es folgt ein kurzes Beispiel, wie Sie Statistiken für die in diesem Beispiel geladene Tabelle erstellen können.


```
create statistics [DateId] on [DimDate2] ([DateId]);
create statistics [CalendarQuarter] on [DimDate2] ([CalendarQuarter]);
create statistics [FiscalQuarter] on [DimDate2] ([FiscalQuarter]);
```

<!--Article references-->
[PolyBase in SQL Data Warehouse – Tutorial]: sql-data-warehouse-load-with-polybase.md


<!-- External Links -->
[neueste Version von AzCopy]: http://aka.ms/downloadazcopy
[Erste Schritte mit dem Befehlszeilenprogramm AzCopy]: https://azure.microsoft.com/documentation/articles/storage-use-azcopy/

[Create External Data Source]: https://msdn.microsoft.com/library/dn935022(v=sql.130).aspx
[Create External File Format]: https://msdn.microsoft.com/library/dn935026(v=sql.130).aspx
[Create External Table]: https://msdn.microsoft.com/library/dn935021(v=sql.130).aspx
[Create Master Key]: https://msdn.microsoft.com/library/ms174382.aspx
[Create Database Scoped Credential]: https://msdn.microsoft.com/library/mt270260.aspx
[CREATE TABLE AS SELECT (Azure SQL Data Warehouse)]: https://msdn.microsoft.com/library/mt204041.aspx


<!--Article references-->

[Statistiken]: ./sql-data-warehouse-develop-statistics.md

<!---HONumber=AcomDC_1210_2015-->