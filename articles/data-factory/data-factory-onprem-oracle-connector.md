<properties 
	pageTitle="Verschieben von Daten aus Oracle | Azure Data Factory" 
	description="Informationen zum Verschieben von Daten in und aus einer Oracle-Datenbank, die lokal oder mithilfe von Azure Data Factory gehostet wird." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/01/2016" 
	ms.author="spelluru"/>

# Verschieben von Daten aus lokalem Oracle mithilfe von Azure Data Factory 

Dieser Artikel beschreibt, wie Sie die Data Factory-Kopieraktivität zum Verschieben von Daten aus Oracle in einen anderen Datenspeicher verwenden können. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit Kopieraktivität und unterstützten Datenspeicherkombinationen bietet.

## Installation 
Damit der Azure Data Factory-Dienst eine Verbindung mit Ihrer lokalen Oracle-Datenbank herstellen kann, muss Folgendes installiert werden:

- Datenverwaltungsgateway auf dem Computer, der die Datenbank hostet, oder auf einem separaten Computer, um zu vermeiden, dass der Computer mit der Datenbank um Ressourcen konkurriert. Datenverwaltungsgateway ist eine Software, die lokale Datenquellen mit Cloud-Diensten auf sichere, verwaltete Weise verbindet. Weitere Informationen zum Datenverwaltungsgateway finden Sie im Artikel [Verschieben von Daten zwischen lokalen Quellen und der Cloud](data-factory-move-data-between-onprem-and-cloud.md). 
- [Oracle Data Access Components (ODAC) für Windows](http://www.oracle.com/technetwork/topics/dotnet/downloads/index.html). Diese Komponente muss auf dem Hostcomputer installiert werden, auf dem das Gateway installiert ist.

> [AZURE.NOTE] Unter [Problembehandlung bei Gateways](data-factory-move-data-between-onprem-and-cloud.md#gateway-troubleshooting) finden Sie Tipps zur Behandlung von Verbindungs- bzw. Gatewayproblemen.

## Beispiel: Kopieren von Daten aus Oracle in Azure-Blob
In diesem Beispiel wird gezeigt, wie Sie Daten aus einer lokalen Oracle-Datenbank in einen Azure-BLOB-Speicher kopieren. Daten können jedoch mithilfe der Kopieraktivität in Azure Data Factory **direkt** in die [hier](data-factory-data-movement-activities.md#supported-data-stores) aufgeführten Senken kopiert werden.
 
Das Beispiel enthält die folgenden Data Factory-Entitäten:

1.	Einen verknüpften Dienst des Typs [OnPremisesOracle](data-factory-onprem-oracle-connector.md#oracle-linked-service-properties)
2.	Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
3.	Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [OracleTable](data-factory-onprem-oracle-connector.md#oracle-dataset-type-properties) 
4.	Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)
5.	Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [OracleSource](data-factory-onprem-oracle-connector.md#oracle-copy-activity-type-properties) als Quelle und [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) als Senke verwendet

Das Beispiel kopiert Daten stündlich aus einer Tabelle in einer lokalen Oracle-Datenbank in ein Blob. Weitere Informationen zu den verschiedenen Eigenschaften, die im folgenden Beispiel verwendet werden, werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit Oracle verknüpfter Dienst:**

	{
	  "name": "OnPremisesOracleLinkedService",
	  "properties": {
	    "type": "OnPremisesOracle",
	    "typeProperties": {
	      "ConnectionString": "data source=<data source>;User Id=<User Id>;Password=<Password>;",
	      "gatewayName": "<gateway name>"
	    }
	  }
	}

**Mit Azure-Blobspeicher verknüpfter Dienst:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<Account key>"
	    }
	  }
	}

**Oracle-Eingabedataset:**

Im Beispiel wird vorausgesetzt, dass Sie die Tabelle "MyTable" in Oracle erstellt haben, die für Zeitreihendaten eine Spalte namens "timestampcolumn" enthält.

Durch Festlegen von "external" auf "true" und Angeben der Richtlinie "externalData" wird der Data Factory angegeben, dass dies eine Tabelle ist, die für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

	{
	    "name": "OracleInput",
	    "properties": {
	        "type": "OracleTable",
	        "linkedServiceName": "OnPremisesOracleLinkedService",
	        "typeProperties": {
	            "tableName": "MyTable"
	        },
	           "external": true,
	        "availability": {
	            "offset": "01:00:00",
	            "interval": "1",
	            "anchorDateTime": "2014-02-27T12:00:00",
	            "frequency": "Hour"
	        },
	      "policy": {     
	           "externalData": {        
	                "retryInterval": "00:01:00",    
	                "retryTimeout": "00:10:00",       
	                "maximumRetry": 3       
	            }     
	          }
	    }
	}


**Azure-Blob-Ausgabedataset:**

Daten werden stündlich in ein neues Blob geschrieben ("frequency": "hour", "interval": 1). Ordnerpfad und Dateiname des Blobs werden basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.
	
	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	      "partitionedBy": [
	        {
	          "name": "Year",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "yyyy"
	          }
	        },
	        {
	          "name": "Month",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%M"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%d"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "%H"
	          }
	        }
	      ],
	      "format": {
	        "type": "TextFormat",
	        "columnDelimiter": "\t",
	        "rowDelimiter": "\n"
	      }
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}


**Pipeline mit Kopieraktivität:**

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **RelationalSource** und der Typ **sink** auf **BlobSink** festgelegt. Die für die **oracleReaderQuery**-Eigenschaft angegebene SQL-Abfrage wählt die aus der letzten Stunde zu kopierenden Daten aus.

	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": " OracleInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "OracleSource",
	            "oracleReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "BlobSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 0,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

## Eigenschaften des mit Oracle verknüpften Diensts

Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit Oracle verknüpften Dienst spezifisch sind.

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
Typ | Die "type"-Eigenschaft muss auf **OnPremisesOracle** festgelegt sein. | Ja
connectionString | Geben Sie Informationen, die zur Verbindung mit der Oracle Databaseinstanz erforderlich sind, für die Eigenschaft "connectionString" an. | Ja 
gatewayName | Name des Gateways, das zum Herstellen einer Verbindung mit dem lokalen Oracle-Server verwendet wird | Ja

Ausführliche Informationen zum Festlegen von Anmeldeinformationen für eine lokale Oracle-Datenquelle finden Sie unter [Festlegen von Anmeldeinformationen und Sicherheit](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security).
## Eigenschaften des Dataset-Typs „Oracle“

Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie Struktur, Verfügbarkeit und Richtlinie einer Dataset-JSON sind bei allen Dataset-Typen (Oracle, Azure-Blob, Azure-Tabelle usw.) ähnlich.
 
Der Abschnitt "typeProperties" unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt "typeProperties" für ein Dataset vom Typ "OracleTable" hat die folgenden Eigenschaften.

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
tableName | Name der Tabelle in der Oracle Database, auf die der verknüpfte Dienst verweist. | Nein (wenn **OracleReaderQuery** von **SqlSource** angegeben ist)

## Eigenschaften des Oracle-Kopieraktivitätstyps

Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen, verschiedene Richtlinien usw. sind für alle Arten von Aktivitäten verfügbar.

**Hinweis**: Die Kopieraktivität verwendet nur eine Eingabe und erzeugt nur eine Ausgabe.

Im Abschnitt "typeProperties" der Aktivität verfügbare Eigenschaften variieren hingegen bei jedem Aktivitätstyp. Bei der Kopieraktivität variieren sie je nach Typ der Quellen und Senken.

Wenn bei der Kopieraktivität „source“ den Typ **OracleSource** hat, sind im Abschnitt **typeProperties** die folgenden Eigenschaften verfügbar:

Eigenschaft | Beschreibung |Zulässige Werte | Erforderlich
-------- | ----------- | ------------- | --------
oracleReaderQuery | Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. | SQL-Abfragezeichenfolge. 
Beispiel: select * from MyTable <p>Falls nicht angegeben, die SQL-Anweisung, die ausgeführt wird: select * from MyTable</p> | Nein (wenn **tableName** von **Dataset** angegeben ist)

[AZURE.INCLUDE [data-factory-structure-for-rectangular-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Typzuordnung für Oracle

Wie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) beschrieben, führt die Kopieraktivität automatische Typkonvertierungen von Quelltypen in Senkentypen mithilfe des folgenden aus zwei Schritten bestehenden Ansatzes durch:

1. Konvertieren von systemeigenen Quelltypen in den .NET-Typ
2. Konvertieren vom .NET-Typ in systemeigenen Senkentyp

Beim Verschieben von Daten aus Oracle werden die folgenden Zuordnungen zwischen dem Datentyp "Oracle" und dem Typ ".NET" verwendet und umgekehrt.

Datentyp "Oracle" | Datentyp ".NET Framework"
---------------- | ------------------------
BFILE | Byte
BLOB | Byte
CHAR | String
CLOB | String
DATE | DateTime
FLOAT | Decimal
INTEGER | Decimal
INTERVAL YEAR TO MONTH | Int32
INTERVAL DAY TO SECOND | TimeSpan
LONG | String
LONG RAW | Byte
NCHAR | String
NCLOB | String
NUMBER | Decimal
NVARCHAR2 | String
RAW | Byte
ROWID | String
TIMESTAMP | DateTime
TIMESTAMP WITH LOCAL TIME ZONE | DateTime
TIMESTAMP WITH TIME ZONE | DateTime
UNSIGNED INTEGER | Number
VARCHAR2 | String
XML | String

## Tipps zur Problembehandlung

**** Problem: ** Folgende **Fehlermeldung** wird angezeigt: Bei der Kopieraktivität wurden ungültige Parameter gefunden: UnknownParameterName. Detaillierte Meldung: Der angeforderte .Net Framework-Datenprovider kann nicht gefunden werden. Er ist ggf. nicht installiert.

**Mögliche Ursachen**

1. Der .NET Framework-Datenanbieter für Oracle wurde nicht installiert.
2. Der .NET Framework-Datenanbieter für Oracle wurde für .NET Framework 2.0 installiert und in den Ordnern für .NET Framework 4.0 nicht gefunden. 

**Lösung/Problemumgehung**

1. Falls Sie den .NET Framework-Datenanbieter für Oracle nicht installiert haben, [Installieren Sie ihn](http://www.oracle.com/technetwork/topics/dotnet/utilsoft-086879.html), und wiederholen Sie anschließend das Szenario. 
2. Falls Sie die Fehlermeldung auch nach der Installation des Anbieters erhalten, führen Sie folgende Schritte aus: 
	1. Öffnen Sie die Computerkonfiguration von .NET 2.0 im folgenden Ordner: <system disk>:\\Windows\\Microsoft.NET\\Framework64\\v2.0.50727\\CONFIG\\machine.config.
	2. Suchen Sie nach **Oracle-Datenanbieter für .NET**. Daraufhin sollte unter **system.data** -> **DbProviderFactories** ein Eintrag wie der Folgende angezeigt werden: „<add name="Oracle Data Provider for .NET" invariant="Oracle.DataAccess.Client" description="Oracle Data Provider for .NET" type="Oracle.DataAccess.Client.OracleClientFactory, Oracle.DataAccess, Version=2.112.3.0, Culture=neutral, PublicKeyToken=89b483f429c47342" />“
2.	Kopieren Sie diesen Eintrag in die Datei „machine.config“ im v4.0-Ordner „<system disk>:\\Windows\\Microsoft.NET\\Framework64\\v4.0.30319\\Config\\machine.config“, und ändern Sie die Version in „4.xxx.x.x“.
3.	Führen Sie „gacutil /i [Anbieterpfad]“ aus, um „<ODP.NET Installed Path>\\11.2.0\\client\_1\\odp.net\\bin\\4\\Oracle.DataAccess.dll“ im globalen Assemblycache (GAC) zu installieren.



[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=AcomDC_0204_2016-->