<properties 
	pageTitle="Verschieben von Daten in und aus dem Dateisystem | Azure Data Factory" 
	description="Erfahren Sie, wie Daten mithilfe von Azure Data Factory in das und aus dem lokalen Dateisystem verschoben werden." 
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
	ms.date="11/09/2015" 
	ms.author="spelluru"/>

# Verschieben von Daten in das und aus dem lokalen Dateisystem mithilfe von Azure Data Factory

Dieser Artikel beschreibt, wie Sie die Data Factory-Kopieraktivität zum Verschieben von Daten in das und aus dem lokalen Dateisystem verwenden können. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit Kopieraktivität und unterstützten Datenspeicherkombinationen bietet.

Data Factory unterstützt das Herstellen einer Verbindung mit dem lokalen Dateisystem mithilfe des Datenverwaltungsgateways. Im Artikel [Verschieben von Daten zwischen lokalen Standorten und Cloud](data-factory-move-data-between-onprem-and-cloud.md) erfahren mehr zum Datenverwaltungsgateway und erhalten eine schrittweise Anleitung zum Einrichten des Gateways.

**Hinweis:** Abgesehen vom Datenverwaltungsgateway müssen keine anderen Binärdateien installiert werden, um die Kommunikation mit dem lokalen Dateisystem zu ermöglichen.

## Linux-Dateifreigabe 

Führen Sie die folgenden beiden Schritte aus, um eine Linux-Dateifreigabe mit dem Dienst zu verwenden, der mit dem Dateiserver verknüpft ist:

- Installieren Sie [Samba](https://www.samba.org/) auf dem Linux-Server.
- Installieren und konfigurieren Sie das Datenverwaltungsgateway auf einem Windows-Server. Das Installieren des Gateways auf einem Linux-Server wird nicht unterstützt. 
 
## Beispiel: Kopieren von Daten aus dem lokalen Dateisystem in ein Azure-Blob

Das nachstehende Beispiel zeigt Folgendes:

1.	Einen verknüpften Dienst des Typs [OnPremisesFileServer](data-factory-onprem-file-system-connector.md#onpremisesfileserver-linked-service-properties)
2.	Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
3.	Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [FileShare](data-factory-onprem-file-system-connector.md#on-premises-file-system-dataset-type-properties)
4.	Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)
4.	Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [FileSystemSource](data-factory-onprem-file-system-connector.md#file-share-copy-activity-type-properties) und [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) verwendet 

Im folgenden Beispiel werden Daten, die zu einer Zeitreihe gehören, aus dem lokalen Dateisystem stündlich in ein Azure-Blob kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

Als ersten Schritt richten Sie das Datenverwaltungsgateway gemäß den Anweisungen im Artikel [Verschieben von Daten zwischen lokalen Quellen und der Cloud](data-factory-move-data-between-onprem-and-cloud.md) ein.

**Mit lokalem Dateiserver verknüpfter Dienst:**

	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\\Contosogame-Asia.<region>.corp.<company>.com",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}

Für „host“ können Sie **Local** oder **localhost** angeben, wenn die Dateifreigabe auf dem Gatewaycomputer selbst erfolgt. Außerdem sollten Sie die Eigenschaft **encryptedCredential** anstelle der Eigenschaften **userid** und **password** verwenden. Weitere Informationen zu diesem verknüpften Dienst finden Sie unter [Eigenschaften des verknüpften Diensts „OnPremisesFileServer“](#onpremisesfileserver-linked-service-properties) .

**Mit Azure-Blobspeicher verknüpfter Dienst:**

	{
	  "name": "StorageLinkedService",
	  "properties": {
	    "type": "AzureStorage",
	    "typeProperties": {
	      "connectionString": "DefaultEndpointsProtocol=https;AccountName=<accountname>;AccountKey=<accountkey>"
	    }
	  }
	}

**Eingabedataset aus dem lokalem Dateisystem:**

Daten werden stündlich in eine neue Datei kopiert, wobei der Pfad und Dateiname jeweils Datum und Uhrzeit mit stündlicher Genauigkeit angeben.

Durch Festlegen von "external" auf "true" und Angeben der Richtlinie "externalData" wird dem Azure Data Factory-Dienst angegeben, dass dies eine Tabelle ist, die für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

	{
	  "name": "OnpremisesFileSystemInput",
	  "properties": {
	    "type": " FileShare",
	    "linkedServiceName": " OnPremisesFileServerLinkedService ",
	    "typeProperties": {
	      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
	      ]
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
	      "folderPath": "mycontainer/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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
	            "format": "%HH"
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

**Kopieraktivität:**

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **FileSystemSource** und der Typ **sink** auf **BlobSink** festgelegt.
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-06-01T18:00:00",
	    "end":"2015-06-01T19:00:00",
	    "description":"Pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "OnpremisesFileSystemtoBlob",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "OnpremisesFileSystemInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "FileSystemSource"
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

##Beispiel: Kopieren von Daten aus Azure SQL in ein lokales Dateisystem 

Das nachstehende Beispiel zeigt Folgendes:

1.	Einen verknüpften Dienst des Typs "AzureSqlDatabase"
2.	Einen verknüpften Dienst des Typs "OnPremisesFileServer"
3.	Ein Eingabedataset des Typs "AzureSqlTable" 
3.	Ein Ausgabedataset des Typs "FileShare"
4.	Eine Pipeline mit Kopieraktivität, die "SqlSource" und "FileSystemSink" verwendet

Im Beispiel werden Daten, die zu einer Zeitreihe gehören, stündlich aus einer Tabelle in einer Azure SQL-Datenbank in das lokale Dateisystem kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

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

**Mit lokalem Dateiserver verknüpfter Dienst:**

	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\\Contosogame-Asia.<region>.corp.<company>.com",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}

Für „host“ können Sie **Local** oder **localhost** angeben, wenn die Dateifreigabe auf dem Gatewaycomputer selbst erfolgt. Außerdem sollten Sie die Eigenschaft **encryptedCredential** anstelle der Eigenschaften **userid** und **password** verwenden. Weitere Informationen zu diesem verknüpften Dienst finden Sie unter [Eigenschaften des verknüpften Diensts „OnPremisesFileServer“](#onpremisesfileserver-linked-service-properties) .

**Azure SQL-Eingabedataset:**

Im Beispiel wird vorausgesetzt, dass Sie die Tabelle "MyTable" in Azure SQL erstellt haben, die für Zeitreihendaten eine Spalte namens "timestampcolumn" enthält.

Durch Festlegen von "external" auf "true" und Angeben der Richtlinie "externalData" wird dem Data Factory-Dienst angegeben, dass dies eine Tabelle ist, die für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

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

**Ausgabedataset aus dem lokalem Dateisystem:**

Daten werden stündlich in eine neue Datei kopiert, wobei der Pfad des Blobs jeweils Datum und Uhrzeit mit stündlicher Genauigkeit angibt.

	{
	  "name": "OnpremisesFileSystemOutput",
	  "properties": {
	    "type": "FileShare",
	    "linkedServiceName": " OnPremisesFileServerLinkedService ",
	    "typeProperties": {
	      "folderPath": "mysharedfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
	            "format": "%HH"
	          }
	        }
	      ]
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

**Pipeline mit Kopieraktivität**: Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **SqlSource** und der Typ **sink** auf **FileSystemSink** festgelegt. Die für die **SqlReaderQuery**-Eigenschaft angegebene SQL-Abfrage wählt die Daten der letzten Stunde zum Kopieren aus.

	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2015-06-01T18:00:00",
	    "end":"2015-06-01T20:00:00",
	    "description":"pipeline for copy activity",
	    "activities":[  
	      {
	        "name": "AzureSQLtoOnPremisesFile",
	        "description": "copy activity",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "AzureSQLInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "OnpremisesFileSystemOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "SqlSource",
	            "SqlReaderQuery": "$$Text.Format('select * from MyTable where timestampcolumn >= \\'{0:yyyy-MM-dd}\\' AND timestampcolumn < \\'{1:yyyy-MM-dd}\\'', WindowStart, WindowEnd)"
	          },
	          "sink": {
	            "type": "FileSystemSink"
	          }
	        },
	       "scheduler": {
	          "frequency": "Hour",
	          "interval": 1
	        },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "OldestFirst",
	          "retry": 3,
	          "timeout": "01:00:00"
	        }
	      }
	     ]
	   }
	}

## Eigenschaften des verknüpften Diensts "OnPremisesFileServer"

Sie können ein lokales Dateisystem mithilfe eines verknüpften Dienst des Typs "Lokaler Dateiserver" mit einer Azure Data Factory verknüpfen. Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit dem lokalen Dateiserver verknüpften Dienst spezifisch sind.

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
Typ | Die "type"-Eigenschaft muss auf **OnPremisesFileServer** festgelegt sein. | Ja 
host | Hostname des Servers. Verwenden Sie "\" als Escapezeichen (siehe das folgende Beispiel): Wenn Ihre Freigabe "\\servername" heißt, geben Sie "\\\servername" an.<p>Wenn das Dateisystem für den Gatewaycomputer lokal ist, verwenden Sie "Local" oder "localhost". Wenn sich das Dateisystem auf einem anderen Server als dem Gatewaycomputer befindet, verwenden Sie "\\\servername".</p> | Ja
userid | Geben Sie die ID des Benutzers an, der auf dem Server zugreifen darf. | Nein (wenn Sie "encryptedCredential" auswählen)
password | Geben Sie das Kennwort für das Benutzerkonto (userid) an. | Nein (wenn Sie "encryptedCredential" auswählen) 
encryptedCredential | Geben Sie die verschlüsselten Anmeldeinformationen an, die Sie durch Ausführen des Cmdlets „New-AzureRmDataFactoryEncryptValue“ erhalten.<p>**Hinweis**: Sie müssen mindestens die Azure PowerShell-Version 0.8.14 verwenden, um mit Cmdlets wie „New-AzureRmDataFactoryEncryptValue“ zu arbeiten, bei denen der type-Parameter auf „OnPremisesFileSystemLinkedService“ festgelegt ist.</p> | Nein (wenn Sie "userid" und "password" unverschlüsselt angeben)
gatewayName | Der Name des Gateways, das der Data Factory-Dienst zum Verbinden mit dem lokalen Dateiserver verwenden soll. | Ja

Ausführliche Informationen zum Festlegen von Anmeldeinformationen für eine Datenquelle des lokalen Dateisystems finden Sie unter [Festlegen von Anmeldeinformationen und Sicherheit](data-factory-move-data-between-onprem-and-cloud.md#setting-credentials-and-security).

**Beispiel: Mit "username" und "password" im Nur-Text-Format**
	
	{
	  "Name": "OnPremisesFileServerLinkedService",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "\\\Contosogame-Asia",
	      "userid": "Admin",
	      "password": "123456",
	      "gatewayName": "mygateway"
	    }
	  }
	}
	
**Beispiel: Verwenden von "encryptedcredential"**

	{
	  "Name": " OnPremisesFileServerLinkedService ",
	  "properties": {
	    "type": "OnPremisesFileServer",
	    "typeProperties": {
	      "host": "localhost",
	      "encryptedCredential": "WFuIGlzIGRpc3Rpbmd1aXNoZWQsIG5vdCBvbmx5IGJ5xxxxxxxxxxxxxxxxx",
	      "gatewayName": "mygateway"
	    }
	  }
	}

## Eigenschaften des Dataset-Typs "Lokales Dateisystem"

Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie "structure", "availability" und "policy" des JSON-Codes eines Datasets sind bei allen Typen von Datasets (Azure SQL, Azure-Blob, Azure-Tabelle, lokales Dateisystem usw.) ähnlich.

Der Abschnitt "typeProperties" unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort, Format usw. der Daten im Datenspeicher. Der Abschnitt "typeProperties" für ein Dataset vom Typ **FileShare** hat die folgenden Eigenschaften.

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
folderPath | Pfad zum Ordner. Beispiel: myfolder<p>Verwenden Sie für Sonderzeichen in der Zeichenfolge das Escapezeichen "\". Geben Sie für „folder\\subfolder“ z. B. „folder\\\subfolder“ und für „d:\\samplefolder“ „d:\\\samplefolder“ an.</p><p>Sie können dies mit **partitionBy** kombinieren, um Ordnerpfade auf der Datum-/Uhrzeitangabe für Anfangs- und Endwert von Slices basieren zu lassen.</p> | Ja
fileName | Geben Sie den Namen der Datei in **folderPath** an, wenn die Tabelle auf eine bestimmte Datei im Ordner verweisen soll. Wenn Sie keine Werte für diese Eigenschaft angeben, verweist die Tabelle auf alle Dateien im Ordner.<p>Wenn "fileName" für ein Ausgabedataset nicht angegeben wird, hat der Name der generierten Datei das folgende Format: </p><p>Data.<Guid>.txt (Beispiel: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt)</p> | Nein
partitionedBy | "partitionedBy" kann genutzt werden, um einen dynamischen Wert für "folderPath" oder "filename" für Zeitreihendaten anzugeben. Beispiel: "folderPath" als Parameter für jedes Stunde mit Daten. | Nein
Format | Zwei Typen von Formaten werden unterstützt: **TextFormat** und **AvroFormat**. Sie müssen die "type"-Eigenschaft unter "format" auf einen dieser Werte festlegen. Wenn die "type"-Eigenschaft auf "TextFormat" festgelegt ist, können Sie zusätzliche optionale Eigenschaften für das Format angeben. Im Formatabschnitt unten finden Sie weitere Einzelheiten. **Die Formateigenschaft wird für lokale Dateisysteme momentan nicht unterstützt. Es wird wie hier dokumentiert in Kürze aktiviert.** | Nein
fileFilter | Geben Sie einen Filter zur Auswahl einer Teilmenge der Dateien in "folderPath" statt alle Dateien an. <p>Zulässige Werte sind: * (mehrere Zeichen) und ? (einzelnes Zeichen).</p><p>Beispiel 1: "fileFilter": "*.log"</p>Beispiel 2: "fileFilter": 2014-1-?.txt"</p><p>** Hinweis**: "fileFilter" eignet sich für ein Eingabedataset des Typs "FileShare".</p> | Nein
| Komprimierung | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Folgende Typen werden unterstützt: "GZip", "Deflate" und "BZip2". Folgende Komprimierungsgrade werden unterstützt: "Optimal" und "Schnellste". Weitere Einzelheiten finden Sie im Abschnitt [Komprimierungsunterstützung](#compression-support). | Nein |

> [AZURE.NOTE]"filename" und "fileFilter" können nicht gleichzeitig verwendet werden.

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

Wenn das Format auf **TextFormat** festgelegt ist, können Sie die folgenden **optionalen** Eigenschaften im Abschnitt **Format** innerhalb des Abschnitts **typeProperties** angeben.

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
columnDelimiter | Die Zeichen, die als Spaltentrennzeichen in einer Datei verwendet werden. Der Standardwert ist das Komma (,). | Nein
rowDelimiter | Die Zeichen, die als Zeilentrennzeichen in einer Datei verwendet werden. Der Standardwert ist einer der Folgenden: ["\\r\\n", "\\r", "\\n"]. | Nein
escapeChar | Das Sonderzeichen, das als Escapezeichen für das Spaltentrennzeichen im Inhalt dient. Kein Standardwert. Sie dürfen nicht mehr als ein Zeichen für diese Eigenschaft angeben.<p>Beispiel: Wenn Sie das Komma (,) als Spaltentrennzeichen gewählt haben, das Kommazeichen jedoch im Text (Beispiel: "Hello, world") verwenden möchten, können Sie "$" als Escapezeichen definieren und die Zeichenfolge "Hello$, world" in der Quelle verwenden.</p><p>Beachten Sie, dass Sie nicht sowohl "escapeChar" als auch "quoteChar" für eine Tabelle angeben können.</p> | Nein
quoteChar | Das Sonderzeichen, das als Anführungszeichen für einen Zeichenfolgenwert dient. Die Spalten- und Zeilentrennzeichen innerhalb der Anführungszeichen werden als Teil des Zeichenfolgenwerts behandelt. Kein Standardwert. Sie dürfen nicht mehr als ein Zeichen für diese Eigenschaft angeben.<p>Beispiel: Wenn Sie das Komma (,) als Spaltentrennzeichen gewählt haben, das Kommazeichen jedoch im Text (Beispiel: <Hello  world>) verwenden möchten, können Sie '"' als Anführungszeichen definieren und die Zeichenfolge <"Hello, world"> in der Quelle verwenden. Diese Eigenschaft gilt für Eingabe- und Ausgabetabellen.</p><p>Beachten Sie, dass Sie nicht sowohl "escapeChar" als auch "quoteChar" für eine Tabelle angeben können.</p> | Nein
nullValue | Die Zeichen, die zur Darstellung von NULL-Werten im Blobdateiinhalt dienen. Der Standardwert ist "\\N".> | Nein
encodingName | Geben Sie den Codierungsnamen an. Eine Liste der gültigen Codierungsnamen finden Sie unter "Encoding.EncodingName-Eigenschaft". <p>Beispiel: "windows-1250" oder "shift\_jis". Der Standardwert lautet "UTF-8".</p> | Nein

#### Beispiele:

Im folgenden Beispiel werden einige der Formateigenschaften für **TextFormat** gezeigt.

	"typeProperties":
	{
	    "folderPath": "MyFolder",
	    "fileName": "MyFileName"
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

Wenn das Format auf **AvroFormat** festgelegt ist, müssen Sie im Abschnitt "Format" innerhalb des Abschnitts "typeProperties" keine Eigenschaften angeben. Beispiel:

	"format":
	{
	    "type": "AvroFormat",
	}
	
Um das Avro-Format in einer nachfolgenden Hive-Tabelle zu verwenden, sehen Sie sich zuvor das [Apache Hive-Tutorial](https://cwiki.apache.org/confluence/display/Hive/AvroSerDe) an.

[AZURE.INCLUDE [data-factory-compression](../../includes/data-factory-compression.md)]

## Eigenschaften von Kopieraktivitätstyp "Dateifreigabe"

**FileSystemSource** unterstützt die folgenden Eigenschaften:

| Eigenschaft | Beschreibung | Zulässige Werte | Erforderlich |
| -------- | ----------- | -------------- | -------- |
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. | True/False (Standardwert)| Nein | 

**FileSystemSink** unterstützt die folgenden Eigenschaften:

| Eigenschaft | Beschreibung | Zulässige Werte | Erforderlich |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Definiert das Verhalten beim Kopieren, wenn die Quelle "BlobSource" oder "FileSystem" ist. | <p>Es gibt drei mögliche Werte für die copyBehavior-Eigenschaft. </p><ul><li>**PreserveHierarchy:** Behält die Dateihierarchie im Zielordner bei, d. h., der relative Pfad zum Quellordner ist mit dem relativen Pfad der Zieldatei zum Zielordner identisch.</li><li>**FlattenHierarchy:** Alle Dateien aus dem Quellordner befinden sich auf der ersten Ebene des Zielordners. Die Namen der Zieldateien werden automatisch generiert. </li><li>**MergeFiles:** Führt alle Dateien aus dem Quellordner in einer Datei zusammen. Wenn der Datei-/Blob-Name angegeben wurde, entspricht der Name dem angegebenen Namen, andernfalls dem automatisch generierten Dateinamen.</li></ul> | Nein |

### Beispiele für "recursive" und "copyBehavior"
Dieser Abschnitt beschreibt das resultierende Verhalten des Kopiervorgangs für verschiedene Kombinationen von rekursiven und CopyBehavior-Werten.

recursive | copyBehavior | Resultierendes Verhalten
--------- | ------------ | --------
true | preserveHierarchy | <p>Für einen Quellordner „Ordner1“ mit der folgenden Struktur:</p> <p>Ordner1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei2<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Unterordner1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei3<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei4<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei5</p>der Zielordner „Ordner1“ wird dieselbe Struktur wie der Quellordner aufweisen<p>Ordner1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei2<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Unterordner1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei3<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei4<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei5</p>.  
true | flattenHierarchy | <p>Für einen Quellordner „Ordner1“ mit der folgenden Struktur:</p> <p>Ordner1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei2<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Unterordner1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei3<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei4<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei5</p>der Zielordner „Ordner1“ wird die folgende Struktur haben: <p>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch erstellter Name für Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch erstellter Name für Datei2<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch erstellter Name für Datei3<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch erstellter Name für Datei4<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch erstellter Name für Datei5</p>
true | mergeFiles | <p>Für einen Quellordner „Ordner1“ mit der folgenden Struktur:</p> <p>Ordner1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei2<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Unterordner1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei3<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei4<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei5</p>der Zielordner „Ordner1“ wird die folgende Struktur haben: <p>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Inhalte von Datei1 + Datei2 + Datei3 + Datei4 + Datei5 werden in einer Datei mit einem automatisch erstellten Namen zusammengeführt</p>
false | preserveHierarchy | <p>Für einen Quellordner „Ordner1“ mit der folgenden Struktur:</p> <p>Ordner1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei2<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Unterordner1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei3<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei4<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei5</p>der Zielordner „Ordner1“ wird die folgende Struktur haben:<p>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Datei2<br/></p><p>Unterordner1 mit Datei3, Datei4, und Datei5 werden nicht übernommen.</p>.
false | flattenHierarchy | <p>Für einen Quellordner „Ordner1“ mit der folgenden Struktur:</p> <p>Ordner1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei2<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Unterordner1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei3<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei4<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei5</p>der Zielordner „Ordner1“ wird die folgende Struktur haben:<p>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch erstellter Name für Datei1<br/>&nbsp;&nbsp;&nbsp;&nbsp;automatisch erstellter Name für Datei2<br/></p><p>Unterordner1 mit Datei3, Datei4, und Datei5 werden nicht übernommen.</p>.
false | mergeFiles | <p>Für einen Quellordner „Ordner1“ mit der folgenden Struktur:</p> <p>Ordner1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei2<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; Unterordner1<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei3<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei4<br/>& Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; & Nbsp; Datei5</p>der Zielordner „Ordner1“ wird die folgende Struktur haben:<p>Ordner1<br/>&nbsp;&nbsp;&nbsp;&nbsp;Inhalte von Datei1 + Datei2 werden in einer Datei mit einem automatisch erstellten Namen zusammengeführt. automatisch erstellter Name für Datei1</p><p>Unterordner1 mit Datei3, Datei4, und Datei5 werden nicht übernommen.</p>.


[AZURE.INCLUDE [data-factory-structure-for-rectangular-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]








 

<!---HONumber=AcomDC_1223_2015-->