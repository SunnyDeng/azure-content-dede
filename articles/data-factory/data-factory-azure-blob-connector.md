<properties 
	pageTitle="Verschieben von Daten in einen und aus einem Azure-Blob | Azure Data Factory" 
	description="Erfahren Sie, wie Daten mithilfe von Azure Data Factory in einen und aus einem Azure-Blob-Speicher verschoben werden." 
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
	ms.date="11/03/2015" 
	ms.author="spelluru"/>

# Verschieben von Daten in einen und aus einem Azure-Blob mithilfe von Azure Data Factory
Dieser Artikel beschreibt die Verwendung der Kopieraktivität in einer Azure Data Factory, um Daten aus einem anderen Datenspeicher in ein Azure-Blob und aus einem Azure-Blob in einen anderen Datenspeicher zu verschieben. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität und den unterstützten Datenspeicherkombinationen bietet.

## Beispiel: Kopieren von Daten aus einem Azure-Blob in Azure SQL
Das nachstehende Beispiel zeigt Folgendes:

1.	Einen verknüpften Dienst des Typs [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties)
2.	Einen verknüpften Dienst des Typs [AzureStorage](#azure-storage-linked-service-properties)
3.	Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](#azure-blob-dataset-type-properties)
4.	Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties)
4.	Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [BlobSource](#azure-blob-copy-activity-type-properties) und [SqlSink](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties) verwendet

Im Beispiel werden Daten, die zu einer Zeitreihe aus einem Azure-Blob gehören, stündlich in eine Tabelle in einer Azure SQL-Datenbank kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit Azure SQL verknüpfter Dienst:**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Mit Azure Storage verknüpfter Dienst:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure-Blob-Eingabedataset:**

Daten werden stündlich aus einem neuen Blob übernommen ("frequency": "hour", "interval": 1). Ordnerpfad und Dateiname des Blobs werden basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Der Ordnerpfad verwendet die Bestandteile Jahr, Monat und Tag der Startzeit, und der Dateiname verwendet die Stunde der Startzeit. Die Festlegung von "external" auf "true" teilt dem Data Factory-Dienst mit, dass diese Tabelle für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/",
	      "fileName": "{Hour}.csv",
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
	        "columnDelimiter": ",",
	        "rowDelimiter": "\n"
	      }
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
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

**Azure SQL-Ausgabedataset:**

Das Beispiel kopiert Daten in eine Tabelle namens "MyTable" in einer Azure SQL-Datenbank. Sie sollten die Tabelle in der Azure SQL-Datenbank mit der gleichen Anzahl von Spalten erstellen, die Sie in der Blob-CSV-Datei erwarten. Neue Zeilen werden der Tabelle stündlich hinzugefügt.

	{
	  "name": "AzureSqlOutput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyOutputTable"
	    },
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
	    }
	  }
	}

**Pipeline mit Kopieraktivität:**

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **BlobSource** und der Typ **sink** auf **SqlSink** festgelegt.

	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "AzureBlobtoSQL",
	        "description": "Copy Activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureBlobInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureSqlOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "BlobSource",
	            "blobColumnSeparators": ","
	          },
	          "sink": {
	            "type": "SqlSink"
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

## Beispiel: Kopieren von Daten aus Azure SQL in ein Azure-Blob
Das nachstehende Beispiel zeigt Folgendes:

1.	Einen verknüpften Dienst des Typs [AzureSqlDatabase](data-factory-azure-sql-connector.md#azure-sql-linked-service-properties)
2.	Einen verknüpften Dienst des Typs [AzureStorage](#azure-storage-linked-service-properties)
3.	Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AzureSqlTable](data-factory-azure-sql-connector.md#azure-sql-dataset-type-properties)
4.	Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](#azure-blob-dataset-type-properties)
4.	Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [SqlSource](data-factory-azure-sql-connector.md#azure-sql-copy-activity-type-properties) und [BlobSink](#azure-blob-copy-activity-type-properties) verwendet


Im Beispiel werden Daten, die zu einer Zeitreihe gehören, stündlich aus einer Tabelle in einer Azure SQL-Datenbank in ein Blob kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit Azure SQL verknüpfter Dienst:**

	{
	  "name": "AzureSqlLinkedService",
	  "properties": {
	    "type": "AzureSqlDatabase",
	    "typeProperties": {
	      "connectionString": "Server=tcp:<servername>.database.windows.net,1433;Database=<databasename>;User ID=<username>@<servername>;Password=<password>;Trusted_Connection=False;Encrypt=True;Connection Timeout=30"
	    }
	  }
	}

**Mit Azure Storage verknüpfter Dienst:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Azure SQL-Eingabedataset:**

Im Beispiel wird vorausgesetzt, dass Sie die Tabelle "MyTable" in Azure SQL erstellt haben, die für Zeitreihendaten eine Spalte namens "timestampcolumn" enthält.

Durch Festlegen von "external" auf "true" und Angeben der Richtlinie "externalData" wird dem Azure Data Factory-Dienst angegeben, dass diese Tabelle für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

	{
	  "name": "AzureSqlInput",
	  "properties": {
	    "type": "AzureSqlTable",
	    "linkedServiceName": "AzureSqlLinkedService",
	    "typeProperties": {
	      "tableName": "MyTable"
	    },
	    "external": true,
	    "availability": {
	      "frequency": "Hour",
	      "interval": 1
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

Daten werden stündlich in ein neues Blob geschrieben ("frequency": "hour", "interval": 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.
	
	{
	  "name": "AzureBlobOutput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "folderPath": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}/",
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

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **SqlSource** und der Typ **sink** auf **BlobSink** festgelegt. Die für die **SqlReaderQuery**-Eigenschaft angegebene SQL-Abfrage wählt die Daten der letzten Stunde zum Kopieren aus.


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
		    	        "name": "AzureSQLInput"
		    	      }
		    	    ],
		    	    "outputs": [
		    	      {
		    	        "name": "AzureBlobOutput"
		    	      }
		    	    ],
		    	    "typeProperties": {
		    	    	"source": {
		            		"type": "SqlSource",
			            	"SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd HH:mm}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd HH:mm}\\'', WindowStart, WindowEnd)"
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

## Eigenschaften des mit Azure Storage verknüpften Diensts

Sie können einen mit Azure Storage verknüpften Dienst verwenden, um ein Azure-Speicherkonto mit einer Azure Data Factory zu verknüpfen. Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit Azure Storage verknüpften Dienst spezifisch sind.

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- |
| Typ | Die type-Eigenschaft muss auf **AzureStorage** festgelegt sein. | Ja |
| connectionString | Geben Sie Informationen, die zur Verbindung mit dem Azure-Speicher erforderlich sind, für die connectionString-Eigenschaft ein. Sie können die connectionString-Eigenschaft für den Azure-Speicher aus dem Azure-Portal abrufen. | Ja |

## Eigenschaften des Dataset-Typs "Azure-Blob"

Eine vollständige Liste der JSON-Abschnitte und -Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie "structure", "availability" und "policy" des JSON-Codes eines Datasets sind bei allen Typen von Datasets (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt **typeProperties** unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort, Format usw. der Daten im Datenspeicher. Der Abschnitt "typeProperties" für ein Dataset vom Typ **AzureBlob** hat die folgenden Eigenschaften.

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- | 
| folderPath | Der Pfad zum Container und Ordner im Blobspeicher. Beispiel: myblobcontainer\\myblobfolder\\ | Ja |
| fileName | <p>Der Name des Blobs. "fileName" ist optional. </p><p>Wenn Sie einen Dateinamen angeben, funktioniert die Aktivität (einschließlich Kopieren) für das jeweilige Blob.</p><p>Wenn "fileName" nicht angegeben ist, umfasst das Kopieren alle Blobs in "folderPath" für das Eingabedataset.</p><p>Wenn "fileName" für ein Ausgabedataset nicht angegeben ist, hat der Name der generierten Datei folgendes Format: Data.<Guid>.txt (z. B.: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt</p> | Nein |
| partitionedBy | "partitionedBy" ist eine optionale Eigenschaft. "partitionedBy" kann genutzt werden, um einen dynamischen Wert für "folderPath" oder "fileName" für Zeitreihendaten anzugeben. Beispiel: "folderPath" kann für jedes stündliche Datenaufkommen parametrisiert werden. Im Abschnitt [Nutzen der Eigenschaft "partitionedBy"](#Leveraging-partitionedBy-property) unten finden Sie Details und Beispiele. | Nein
| Format | Zwei Typen von Formaten werden unterstützt: **TextFormat** und **AvroFormat**. Sie müssen die type-Eigenschaft unter "format" auf einen dieser Werte festlegen. Wenn das Format auf "TextFormat" festgelegt ist, können Sie zusätzliche optionale Eigenschaften für das Format angeben. Im Abschnitt [Angeben von "TextFormat"](#specifying-textformat) unten finden Sie weitere Details. | Nein
| Komprimierung | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Folgende Typen werden unterstützt: "GZip", "Deflate" und "BZip2". Folgende Komprimierungsgrade werden unterstützt: "Optimal" und "Schnellste". Weitere Einzelheiten finden Sie im Abschnitt [Komprimierungsunterstützung](#compression-support). | Nein |

### Nutzen der Eigenschaft "partitionedBy"
Wie bereits erwähnt, können Sie die dynamischen Werte "folderPath" und "fileName" für Zeitreihendaten mit dem Abschnitt **partitionedBy**, mit Data Factory-Makros und mit den Systemvariablen "SliceStart" und "SliceEnd" angeben, die die Start- und Endzeit für einen bestimmten Slice festlegen.

Weitere Informationen über Data Factory-Systemvariablen und -Funktionen, die Sie im Abschnitt "partitionedBy" verwenden können, finden Sie unter [Data Factory-Systemvariablen](data-factory-scheduling-and-execution.md#data-factory-system-variables) und [Referenz zu Data Factory-Funktionen](data-factory-scheduling-and-execution.md#data-factory-functions-reference).

In den Artikeln [Erstellen von Datasets](data-factory-create-datasets.md) und [Planung und Ausführung](data-factory-scheduling-and-execution.md) finden Sie weitere Details zu Zeitreihen-Datasets, Planung und Slices.

#### Beispiel 1

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy": 
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

Im obigen Beispiel wird {Slice} durch den Wert der Data Factory-Systemvariablen "SliceStart" ersetzt, die im Format (JJJJMMTTHH) angegeben wird. "SliceStart" bezieht sich auf die Startzeit des Slices. "folderPath" unterscheidet sich für jeden Slice. Beispiel: "wikidatagateway/wikisampledataout/2014100103" oder "wikidatagateway/wikisampledataout/2014100104".

#### Beispiel 2

	"folderPath": "wikidatagateway/wikisampledataout/{Year}/{Month}/{Day}",
	"fileName": "{Hour}.csv",
	"partitionedBy": 
	 [
	    { "name": "Year", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyy" } },
	    { "name": "Month", "value": { "type": "DateTime", "date": "SliceStart", "format": "MM" } }, 
	    { "name": "Day", "value": { "type": "DateTime", "date": "SliceStart", "format": "dd" } }, 
	    { "name": "Hour", "value": { "type": "DateTime", "date": "SliceStart", "format": "hh" } } 
	],

Im Beispiel oben werden Jahr, Monat, Tag und Uhrzeit von "SliceStart" in separate Variablen extrahiert, die von den Eigenschaften "folderPath" und "fileName" verwendet werden.

### Angeben von "TextFormat"

Wenn das Format auf **TextFormat** festgelegt ist, können Sie die folgenden **optionalen** Eigenschaften im Abschnitt **Format** angeben.

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- |
| columnDelimiter | Die Zeichen, die als Spaltentrennzeichen in einer Datei verwendet werden. Dieses Tag ist optional. Der Standardwert ist das Komma (,). | Nein |
| rowDelimiter | Die Zeichen, die als Zeilentrennzeichen in einer Datei verwendet werden. Dieses Tag ist optional. Der Standardwert ist einer der Folgenden: ["\\r\\n", "\\r", "\\n"]. | Nein |
| escapeChar | <p>Das Sonderzeichen, das als Escapezeichen für das Spaltentrennzeichen im Inhalt dient. Dieses Tag ist optional. Kein Standardwert. Sie dürfen nicht mehr als ein Zeichen für diese Eigenschaft angeben.</p><p>Beispiel: Wenn Sie das Komma (,) als Spaltentrennzeichen gewählt haben, das Kommazeichen jedoch im Text (Beispiel: "Hello, world") verwenden möchten, können Sie "$" als Escapezeichen definieren und die Zeichenfolge "Hello$, world" in der Quelle verwenden.</p><p>Beachten Sie, dass Sie nicht sowohl "escapeChar" als auch "quoteChar" für eine Tabelle angeben können.</p> | Nein | 
| quoteChar | <p>Das Sonderzeichen, das als Anführungszeichen für einen Zeichenfolgenwert dient. Die Spalten- und Zeilentrennzeichen innerhalb der Anführungszeichen werden als Teil des Zeichenfolgenwerts behandelt. Dieses Tag ist optional. Kein Standardwert. Sie dürfen nicht mehr als ein Zeichen für diese Eigenschaft angeben.</p><p>Beispiel: Wenn Sie das Komma (,) als Spaltentrennzeichen gewählt haben, das Kommazeichen jedoch im Text (Beispiel: <Hello  world>) verwenden möchten, können Sie '"' als Anführungszeichen definieren und die Zeichenfolge <"Hello, world"> in der Quelle verwenden. Diese Eigenschaft gilt für Eingabe- und Ausgabetabellen.</p><p>Beachten Sie, dass Sie nicht sowohl "escapeChar" als auch "quoteChar" für eine Tabelle angeben können.</p> | Nein |
| nullValue | <p>Die Zeichen, die zur Darstellung von NULL-Werten im Blobdateiinhalt dienen. Dieses Tag ist optional. Der Standardwert ist "\\N".</p><p>Beispielsweise wird gemäß oben genanntem Beispiel "NaN" im Blob beim Kopieren, z. B. in SQL Server, als NULL-Wert übersetzt.</p> | Nein |
| encodingName | Geben Sie den Codierungsnamen an. Eine Liste der gültigen Codierungsnamen finden Sie unter [Encoding.EncodingName-Eigenschaft](https://msdn.microsoft.com/library/system.text.encoding.aspx). Beispiel: Windows-1250 oder Shift-JIS. Der Standardwert lautet "UTF-8". | Nein | 

#### Beispiele
Im folgenden Beispiel werden einige der Formateigenschaften für "TextFormat" gezeigt.

	"typeProperties":
	{
	    "folderPath": "mycontainer/myfolder",
	    "fileName": "myblobname"
	    "format":
	    {
	        "type": "TextFormat",
	        "columnDelimiter": ",",
	        "rowDelimiter": ";",
	        "quoteChar": """,
	        "NullValue": "NaN"
	    }
	},

Um "escapeChar" anstelle von "quoteChar" zu verwenden, ersetzen Sie die Zeile mit "quoteChar" durch Folgendes:

	"escapeChar": "$",

### Angeben von "AvroFormat"
Wenn das Format auf "AvroFormat" festgelegt ist, müssen Sie im Abschnitt "Format" innerhalb des Abschnitts "typeProperties" keine Eigenschaften angeben. Beispiel:

	"format":
	{
	    "type": "AvroFormat",
	}

Um das Avro-Format in einer Hive-Tabelle zu verwenden, sehen Sie sich zuvor das [Apache Hive-Tutorial](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe) an.

[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]


## Eigenschaften des Azure-Blob-Kopieraktivitätstyps  
Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen, verschiedene Richtlinien usw. sind für alle Arten von Aktivitäten verfügbar.

Im Abschnitt "typeProperties" der Aktivität verfügbare Eigenschaften variieren hingegen bei jedem Aktivitätstyp. Bei der Kopieraktivität variieren sie je nach Typ der Quellen und Senken.

**BlobSource** unterstützt die folgenden Eigenschaften im Abschnitt **typeProperties**:

| Eigenschaft | Beschreibung | Zulässige Werte | Erforderlich |
| -------- | ----------- | -------------- | -------- | 
| treatEmptyAsNull | Gibt an, ob Null oder eine leere Zeichenfolge als NULL-Wert behandelt wird. | TRUE<br/>FALSE | Nein |
| skipHeaderLineCount | Gibt an, wie viele Zeilen übersprungen werden müssen. Nur anwendbar, wenn das Eingabedataset **TextFormat** verwendet. | Ganzzahl zwischen "0" und "Max". | Nein | 
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. | True (Standardwert), False | Nein | 


**BlobSink** unterstützt die folgenden Eigenschaften im Abschnitt **typeProperties**:

| Eigenschaft | Beschreibung | Zulässige Werte | Erforderlich |
| -------- | ----------- | -------------- | -------- |
| blobWriterAddHeader | Gibt an, ob der Header der Spaltendefinitionen hinzugefügt werden soll. | TRUE<br/>FALSE (Standard) | Nein |
| copyBehavior | Definiert das Verhalten beim Kopieren, wenn die Quelle "BlobSource" oder "FileSystem" ist. | <p>Es gibt drei mögliche Werte für die copyBehavior-Eigenschaft. </p><ul><li>**PreserveHierarchy:** Behält die Dateihierarchie im Zielordner bei, d. h., der relative Pfad zum Quellordner ist mit dem relativen Pfad der Zieldatei zum Zielordner identisch.</li><li>**FlattenHierarchy:** Alle Dateien aus dem Quellordner befinden sich auf der ersten Ebene des Zielordners. Die Namen der Zieldateien werden automatisch generiert. </li><li>**MergeFiles:** Führt alle Dateien aus dem Quellordner in einer Datei zusammen. Wenn der Datei-/Blob-Name angegeben wurde, entspricht der Name dem angegebenen Namen, andernfalls dem automatisch generierten Dateinamen.</li></ul> | Nein |

### Beispiele für "recursive" und "copyBehavior"
Dieser Abschnitt beschreibt das resultierende Verhalten des Kopiervorgangs für verschiedene Kombinationen von rekursiven und CopyBehavior-Werten.

recursive | copyBehavior | Resultierendes Verhalten
--------- | ------------ | --------
true | preserveHierarchy | <p>Für einen Quellordner „Ordner1“ mit der folgenden Struktur:</p> <p>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5</p>wird der Zielordner "Ordner1" dieselbe Struktur wie der Quellordner aufweisen<p>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5</p>.  
true | flattenHierarchy | <p>Für einen Quellordner „Ordner1“ mit der folgenden Struktur:</p> <p>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5</p>wird der Zielordner "Ordner1" die folgende Struktur aufweisen: <p>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch erstellter Name für Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch erstellter Name für Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch erstellter Name für Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch erstellter Name für Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch erstellter Name für Datei5</p>
true | mergeFiles | <p>Für einen Quellordner „Ordner1“ mit der folgenden Struktur:</p> <p>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5</p>wird der Zielordner "Ordner1" die folgende Struktur aufweisen: <p>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Inhalte von Datei1 + Datei2 + Datei3 + Datei4 + Datei5 werden in einer Datei mit einem automatisch erstellten Dateinamen zusammengeführt</p>
false | preserveHierarchy | <p>Für einen Quellordner „Ordner1“ mit der folgenden Struktur:</p> <p>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5</p>wird der Zielordner "Ordner1" die folgende Struktur aufweisen:<p>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/></p><p>Unterordner1 mit Datei3, Datei4 und Datei5 werden nicht übernommen.</p>.
false | flattenHierarchy | <p>Für einen Quellordner „Ordner1“ mit der folgenden Struktur:</p> <p>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5</p>wird der Zielordner "Ordner1" die folgende Struktur aufweisen:<p>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch erstellter Name für Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch erstellter Name für Datei2<br/></p><p>Unterordner1 mit Datei3, Datei4 und Datei5 werden nicht übernommen.</p>.
false | mergeFiles | <p>Für einen Quellordner „Ordner1“ mit der folgenden Struktur:</p> <p>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;Unterordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;Datei5</p>wird der Zielordner "Ordner1" die folgende Struktur aufweisen:<p>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Inhalte von Datei1 + Datei2 werden in einer Datei mit einem automatisch erstellten Dateinamen zusammengeführt. automatisch erstellter Name für Datei1</p><p>Unterordner1 mit Datei3, Datei4 und Datei5 werden nicht übernommen.</p>.

  


[AZURE.INCLUDE [data-factory-structure-for-rectangular-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=Nov15_HO3-->