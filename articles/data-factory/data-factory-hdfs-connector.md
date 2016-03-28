<properties 
	pageTitle="Verschieben von Daten aus einem lokalem HDFS mithilfe von Azure Data Factory | Azure Data Factory" 
	description="Erfahren Sie, wie Sie Daten aus einem lokalem HDFS mithilfe von Azure Data Factory verschieben." 
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
	ms.date="01/19/2016" 
	ms.author="spelluru"/>

# Verschieben von Daten aus einem lokalem HDFS mithilfe von Azure Data Factory
Dieser Artikel beschreibt, wie Sie die Kopieraktivität in einer Azure Data Factory verwenden können, um Daten aus einem lokalen HDFS (Hadoop Distributed File System) in einen anderen Datenspeicher zu verschieben. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit Kopieraktivität und unterstützten Datenspeicherkombinationen bietet.

Data Factory unterstützt derzeit nur das Verschieben von Daten aus einem lokalen HDFS in andere Datenspeicher, aber nicht das Verschieben aus anderen Datenspeichern in ein lokales HDFS.


## Herstellen der Verbindung
Der Data Factory-Dienst unterstützt das Herstellen einer Verbindung mit einem lokalen HDFS über das Datenverwaltungsgateway. Im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) erfahren mehr zum Datenverwaltungsgateway und erhalten eine schrittweise Anleitung zum Einrichten des Gateways. Sie müssen das Gateway nutzen, um eine Verbindung mit dem HDFS herzustellen, auch wenn das HDFS einer Azure-IaaS-VM gehostet wird.

Grundsätzlich ist es zwar möglich, das Gateway auf dem gleichen lokalen Computer bzw. auf dem gleichen virtuellen Azure-Computer zu installieren, auf dem sich auch das HDFS befindet. Aus Leistungsgründen und zur Vermeidung von Ressourcenkonflikten wird jedoch empfohlen, das Gateway auf einem separaten Computer oder auf einer separaten Azure-IaaS-VM zu installieren. Wenn Sie das Gateway auf einem separaten Computer installieren, muss dieser Computer auf den Computer mit dem HDFS zugreifen können.

## Beispiel: Kopieren von Daten aus einem lokalen HDFS in ein Azure-Blob

In diesem Beispiel wird gezeigt, wie Sie Daten aus einem lokalen HDFS in einen Azure-BLOB-Speicher kopieren. Daten können jedoch mithilfe der Kopieraktivität in Azure Data Factory **direkt** in die [hier](data-factory-data-movement-activities.md#supported-data-stores) aufgeführten Senken kopiert werden.
 
Das Beispiel enthält die folgenden Data Factory-Entitäten:

1.	Einen verknüpften Dienst des Typs [OnPremisesHdfs](#hdfs-linked-service-properties)
2.	Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
3.	Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [FileShare](#hdfs-dataset-type-properties)
4.	Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)
4.	Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [FileSystemSource](#hdfs-copy-activity-type-properties) und [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) verwendet

Das Beispiel kopiert stündlich Daten aus einem Abfrageergebnis in einem lokalen HDFS in ein Blob. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

Als ersten Schritt richten Sie das Datenverwaltungsgateway gemäß den Anweisungen im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) ein.

**Mit HDFS verknüpfter Dienst**. In diesem Beispiel wird die Windows-Authentifizierung verwendet. Informationen zu den verwendbaren Authentifizierungstypen finden Sie im Abschnitt [Mit HDFS verknüpfter Dienst](#hdfs-linked-service-properties).

	{
	    "name": "HDFSLinkedService",
	    "properties":
	    {
	        "type": "Hdfs",
	        "typeProperties":
	        {
	            "authenticationType": "Windows",
	            "userName": "Administrator",
	            "password": "password",
	            "url" : "http://<machine>:50070/webhdfs/v1/",
	            "gatewayName": "mygateway"
	        }
	    }
	}

**Mit Azure Storage verknüpfter Dienst**

	{
	  "name": "AzureStorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**HDFS-Eingabedataset**. Dieses Dataset verweist auf den HDFS-Ordner „DataTransfer/UnitTest/“. Die Pipeline kopiert alle Dateien in diesem Ordner an das Ziel.

Durch Festlegen von „external“ auf „true“ und (optionales) Angeben der Richtlinie „externalData“ wird dem Data Factory-Dienst mitgeteilt, dass dies eine Tabelle ist, die für die Data Factory extern ist und die nicht durch eine Aktivität in der Data Factory erzeugt wird.
	
	{
	    "name": "InputDataset",
	    "properties": {
	        "type": "FileShare",
	        "linkedServiceName": "HDFSLinkedService",
	        "typeProperties": {
	            "folderPath": "DataTransfer/UnitTest/"
	        },
	        "external": true,
	        "availability": {
	            "frequency": "Hour",
	            "interval":  1
	        }
	    }
	}




**Azure-Blob-Ausgabedataset**

Daten werden stündlich in ein neues Blob geschrieben ("frequency": "hour", "interval": 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

	{
	    "name": "OutputDataset",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/hdfs/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
	            "format": {
	                "type": "TextFormat",
	                "rowDelimiter": "\n",
	                "columnDelimiter": "\t"
	            },
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
	            ]
	        },
	        "availability": {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}



**Pipeline mit Kopieraktivität**

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **FileSystemSource** und der Typ **sink** auf **BlobSink** festgelegt. Die für die **query**-Eigenschaft angegebene SQL-Abfrage wählt die aus der letzten Stunde zu kopierenden Daten aus.
	
	{
	    "name": "pipeline",
	    "properties":
	    {
	        "activities":
	        [
	            {
	                "name": "HdfsToBlobCopy",
	                "inputs": [ {"name": "InputDataset"} ],
	                "outputs": [ {"name": "OutputDataset"} ],
	                "type": "Copy",
	                "typeProperties":
	                {
	                    "source":
	                    {
	                        "type": "FileSystemSource"
	                    },
	                    "sink":
	                    {
	                        "type": "BlobSink"
	                    }
	                },
	                "policy":
	                {
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1,
	                    "timeout": "00:05:00"
	                }
	            }
	        ],
	        "start": "2014-06-01T18:00:00Z",
	        "end": "2014-06-01T19:00:00Z"
	    }
	}



## Eigenschaften des mit HDFS verknüpften Diensts

Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit HDFS verknüpften Dienst spezifisch sind.

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- | 
| type | Die type-Eigenschaft muss auf **Hdfs** festgelegt sein. | Ja | 
| Url | Die URL für das HDFS. | Ja |
| encryptedCredential | Die Ausgabe der Anmeldeinformationen von [New-AzureRMDataFactoryEncryptValue](https://msdn.microsoft.com/library/mt603802.aspx) für den Zugriff | Nein |
| userName | Der Benutzername für die Windows-Authentifizierung. | Ja (für die Windows-Authentifizierung)
| password | Das Kennwort für die Windows-Authentifizierung. | Ja (für die Windows-Authentifizierung)
| authenticationType | Windows oder Anonymous. | Ja |
| gatewayName | Der Name des Gateways, das der Data Factory-Dienst zum Verbinden mit dem HDFS verwenden soll. | Ja |   

Ausführliche Informationen zum Festlegen von Anmeldeinformationen für ein lokales HDFS finden Sie unter [Festlegen von Anmeldeinformationen und Sicherheit](data-factory-move-data-between-onprem-and-cloud.md#set-credentials-and-security).

### Verwenden der anonymen Authentifizierung

	{
	    "name": "hdfs",
	    "properties":
	    {
	        "type": "Hdfs",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "userName": "hadoop",
	            "url" : "http://<machine>:50070/webhdfs/v1/",
	            "gatewayName": "mygateway"
	        }
	    }
	}


### Verwenden der Windows-Authentifizierung
	
	{
	    "name": "hdfs",
	    "properties":
	    {
	        "type": "Hdfs",
	        "typeProperties":
	        {
	            "authenticationType": "Windows",
	            "userName": "Administrator",
	            "password": "password",
	            "url" : "http://<machine>:50070/webhdfs/v1/",
	            "gatewayName": "mygateway"
	        }
	    }
	}
 


## Eigenschaften des Dataset-Typs „HDFS“

Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie "structure", "availability" und "policy" des JSON-Codes eines Datasets sind bei allen Typen von Datasets (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt **typeProperties** unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt „typeProperties“ für ein Dataset vom Typ **FileShare** (wozu ein HDFS-Dataset gehört) hat die folgenden Eigenschaften:

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
folderPath | Pfad zum Ordner. Beispiel: myfolder<br/><br/>Verwenden Sie für Sonderzeichen in der Zeichenfolge das Escapezeichen "\". Geben Sie für "folder\\subfolder" z. B. "folder\\\subfolder" und für "d:\\samplefolder" die Syntax "d:\\\samplefolder" an.<br/><br/>Sie können dies mit **partitionBy** kombinieren, damit Ordnerpfade auf der Datum-/Uhrzeitangabe für Anfangs- und Endwert von Slices basieren. | Ja
fileName | Geben Sie den Namen der Datei in **folderPath** an, wenn die Tabelle auf eine bestimmte Datei im Ordner verweisen soll. Wenn Sie keine Werte für diese Eigenschaft angeben, verweist die Tabelle auf alle Dateien im Ordner.<br/><br/>Wenn "fileName" für ein Ausgabedataset nicht angegeben wird, hat der Name der generierten Datei das folgende Format: <br/><br/>Data.<Guid>.txt (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt) | Nein
partitionedBy | "partitionedBy" kann genutzt werden, um einen dynamischen Wert für "folderPath" oder "filename" für Zeitreihendaten anzugeben. Beispiel: "folderPath" als Parameter für jedes Stunde mit Daten. | Nein
fileFilter | Geben Sie einen Filter zur Auswahl einer Teilmenge der Dateien in "folderPath" statt alle Dateien an. <br/><br/>Zulässige Werte sind: * (mehrere Zeichen) und ? (einzelnes Zeichen).<br/><br/>Beispiel 1: "fileFilter": "*.log"<br/>Beispiel 2: "fileFilter": 2014-1-?.txt"<br/><br/>** Hinweis**: "fileFilter" eignet sich für ein Eingabedataset des Typs "FileShare". | Nein
| Komprimierung | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Folgende Typen werden unterstützt: **GZip**, **Deflate** und **BZip2**. Folgende Komprimierungsgrade werden unterstützt: **Optimal** und **Schnellste**. Beachten Sie, dass für Daten im **AvroFormat** zurzeit keine Komprimierungseinstellungen unterstützt werden. Weitere Einzelheiten finden Sie im Abschnitt [Komprimierungsunterstützung](#compression-support). | Nein |
| Format | Drei Formattypen werden unterstützt: **TextFormat**, **AvroFormat** und **JsonFormat**. Sie müssen die type-Eigenschaft unter "format" auf einen dieser Werte festlegen. Wenn das Format auf "TextFormat" festgelegt ist, können Sie zusätzliche optionale Eigenschaften für das Format angeben. Im Abschnitt [Angeben von "TextFormat"](#specifying-textformat) unten finden Sie weitere Details. Gehen Sie zum Abschnitt [JsonFormat angeben](#specifying-jsonformat), wenn Sie JsonFormat verwenden. | Nein 


> [AZURE.NOTE] "filename" und "fileFilter" können nicht gleichzeitig verwendet werden.


### Nutzen der "partionedBy"-Eigenschaft

Wie zuvor erwähnt, kann "partitionedBy" genutzt werden, um einen dynamischen Wert für "folderPath" oder "filename" für Zeitreihendaten anzugeben. Sie können dazu die Data Factory-Makros und Systemvariablen "SliceStart" und "SliceEnd" verwenden, die den logischen Zeitraum für einen bestimmten Datenslice angeben.

In den Artikeln [Erstellen von Datasets](data-factory-create-datasets.md), [Planung und Ausführung](data-factory-scheduling-and-execution.md) und [Erstellen von Pipelines](data-factory-create-pipelines.md) finden Sie weitere Details zu Zeitreihendatasets, Planung und Slices.

#### Beispiel 1:

	"folderPath": "wikidatagateway/wikisampledataout/{Slice}",
	"partitionedBy": 
	[
	    { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } },
	],

Im obigen Beispiel wird {Slice} durch den Wert der Data Factory-Systemvariablen "SliceStart" ersetzt, die im Format (JJJJMMTTHH) angegeben wird. "SliceStart" bezieht sich auf die Startzeit des Slices. "folderPath" unterscheidet sich für jeden Slice. Beispiel: "wikidatagateway/wikisampledataout/2014100103" oder "wikidatagateway/wikisampledataout/2014100104".

#### Beispiel 2:

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
| columnDelimiter | Das Zeichen, das als Spaltentrennzeichen in einer Datei verwendet wird. Derzeit ist nur ein Zeichen zulässig. Dieses Tag ist optional. Der Standardwert ist das Komma (,). | Nein |
| rowDelimiter | Das Zeichen, das als Zeilentrennzeichen in einer Datei verwendet wird. Derzeit ist nur ein Zeichen zulässig. Dieses Tag ist optional. Der Standardwert ist einer der Folgenden: ["\\r\\n", "\\r", "\\n"]. | Nein |
| escapeChar | Das Sonderzeichen, das als Escapezeichen für das Spaltentrennzeichen im Inhalt dient. Dieses Tag ist optional. Kein Standardwert. Sie dürfen maximal ein Zeichen für diese Eigenschaft angeben.<br/><br/>Beispiel: Wenn Sie das Komma (,) als Spaltentrennzeichen gewählt haben, es jedoch im Text (Beispiel: „Hello, world“) verwenden möchten, können Sie „$“ als Escapezeichen definieren und die Zeichenfolge „Hello$, world“ in der Quelle verwenden.<br/><br/>Beachten Sie, dass Sie escapeChar und quoteChar nicht gleichzeitig für eine Tabelle angeben können. | Nein | 
| quoteChar | Das Sonderzeichen, das als Anführungszeichen für einen Zeichenfolgenwert dient. Die Spalten- und Zeilentrennzeichen innerhalb der Anführungszeichen werden als Teil des Zeichenfolgenwerts behandelt. Dieses Tag ist optional. Kein Standardwert. Sie dürfen nicht mehr als ein Zeichen für diese Eigenschaft angeben.<br/><br/>Beispiel: Wenn Sie das Komma (,) als Spaltentrennzeichen gewählt haben, das Kommazeichen jedoch im Text (Beispiel: <Hello  world>) verwenden möchten, können Sie '"' als Anführungszeichen definieren und die Zeichenfolge <"Hello, world"> in der Quelle verwenden. Diese Eigenschaft gilt für Eingabe- und Ausgabetabellen.<br/><br/>Beachten Sie, dass Sie escapeChar und quoteChar nicht gleichzeitig für eine Tabelle angeben können. | Nein |
| nullValue | Die Zeichen, die zur Darstellung von NULL-Werten im Blobdateiinhalt dienen. Dieses Tag ist optional. Der Standardwert ist „\\N“.<br/><br/>Beispielsweise wird gemäß dem oben genannten Beispiel „NaN“ im Blob beim Kopieren (z. B. in SQL Server) als NULL-Wert übersetzt. | Nein |
| encodingName | Geben Sie den Codierungsnamen an. Eine Liste der gültigen Codierungsnamen finden Sie unter [Encoding.EncodingName-Eigenschaft](https://msdn.microsoft.com/library/system.text.encoding.aspx). Beispiel: Windows-1250 oder Shift-JIS. Der Standardwert lautet "UTF-8". | Nein | 

#### TextFormat-Beispiel
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

[AZURE.INCLUDE [data-factory-json-format](../../includes/data-factory-json-format.md)]

[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## Eigenschaften von HDFS-Kopieraktivitätstyp

Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen, verschiedene Richtlinien usw. sind für alle Arten von Aktivitäten verfügbar.

Im Abschnitt "typeProperties" der Aktivität verfügbare Eigenschaften variieren hingegen bei jedem Aktivitätstyp. Bei der Kopieraktivität variieren sie je nach Typ der Quellen und Senken.

Wenn die Quelle bei der Kopieraktivität vom Typ **FileSystemSource** ist, sind im Abschnitt typeProperties die folgenden Eigenschaften verfügbar:

**FileSystemSource** unterstützt die folgenden Eigenschaften:

| Eigenschaft | Beschreibung | Zulässige Werte | Erforderlich |
| -------- | ----------- | -------------- | -------- |
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. | True/False (Standardwert)| Nein |

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangular-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

<!---HONumber=AcomDC_0316_2016-->