<properties
	pageTitle="Verschieben von Daten in und aus Azure Data Lake-Speicher | Azure Data Factory"
	description="Erfahren Sie, wie Daten mithilfe von Azure Data Factory in und aus Azure Data Lake-Speicher verschoben werden."
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
	ms.date="10/13/2015"
	ms.author="spelluru"/>

# Verschieben von Daten in und aus Azure Data Lake-Speicher mithilfe von Azure Data Factory
Dieser Artikel beschreibt die Verwendung der Kopieraktivität in einer Azure Data Factory, um Daten aus einem anderen Datenspeicher in Azure Data Lake-Speicher und aus Azure Data Lake-Speicher in einen anderen Datenspeicher zu verschieben. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität und den unterstützten Datenspeicherkombinationen bietet.

> [AZURE.NOTE]Sie müssen ein Azure Data Lake-Speicherkonto anlegen, bevor Sie eine Pipeline mit einer Kopieraktivität zum Verschieben von Daten in einen bzw. aus einem Azure Data Lake-Speicher erstellen können. Weitere Informationen zu Azure Data Lake-Speicher finden Sie unter [Erste Schritte mit Azure Data Lake-Speicher](../data-lake-store/data-lake-store-get-started-portal.md).
>  
> Im Tutorial [Erstellen Ihrer ersten Pipeline](data-factory-build-your-first-pipeline.md) finden Sie detaillierte Anweisungen zum Erstellen einer Data Factory, von verknüpften Diensten, Datasets und einer Pipeline. Verwenden Sie die JSON-Codeausschnitte in Data Factory-Editor, Visual Studio oder Azure PowerShell, um die Data Factory-Entitäten zu erstellen.

## Beispiel: Kopieren von Daten aus einem Azure-Blob in Azure Data Lake-Speicher
Das nachstehende Beispiel zeigt Folgendes:

1.	Einen verknüpften Dienst des Typs [AzureStorage](#azure-storage-linked-service-properties)
2.	Einen verknüpften Dienst des Typs [AzureDataLakeStore](#azure-data-lake-linked-service-properties)
3.	Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](#azure-blob-dataset-type-properties)
4.	Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureDataLakeStore](#azure-data-lake-dataset-type-properties)
4.	Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [BlobSource](#azure-blob-copy-activity-type-properties) und [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties) verwendet

Im Beispiel werden zu einer Zeitreihe gehörende Daten aus einem Azure-Blobspeicher stündlich in Azure Data Lake-Speicher kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.


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

**Mit Azure Data Lake verknüpfter Dienst:**

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeUri": "https://<accountname>.azuredatalake.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

### Erstellen eines mit Azure Data Lake verknüpften Diensts mit Data Factory-Editor
Das folgende Verfahren enthält Schritte zum Erstellen eines mit Azure Data Lake-Speicher verknüpften Diensts mit dem Data Factory-Editor.

1. Klicken Sie auf der Befehlsleiste auf **Neuer Datenspeicher**, und wählen Sie **Azure Data Lake-Speicher** aus.
2. Geben Sie im JSON-Editor für die **datalakeUri**-Eigenschaft den URI für den Data Lake ein.
3. Klicken Sie auf der Befehlsleiste auf die Schaltfläche auf **Autorisieren**. Ein Popupfenster sollte geöffnet werden.

	![Schaltfläche „Autorisieren“](./media/data-factory-azure-data-lake-connector/authorize-button.png)
4. Melden Sie sich mit Ihren Anmeldeinformationen an. Der **authorization**-Eigenschaft in der JSON sollte nun ein Wert zugewiesen sein.
5. (Optional) Geben Sie Werte für optionale Parameter an, z. B. **accountName**, **subscriptionID** und **resourceGroupName**, in der JSON an, (oder) löschen Sie diese Eigenschaften aus der JSON.
6. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.


**Azure-Blob-Eingabedataset:**

Daten werden stündlich aus einem neuen Blob übernommen ("frequency": "hour", "interval": 1). Ordnerpfad und Dateiname des Blobs werden basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Der Ordnerpfad verwendet die Bestandteile Jahr, Monat und Tag der Startzeit, und der Dateiname verwendet die Stunde der Startzeit. Die Festlegung von "external" auf "true" teilt dem Data Factory-Dienst mit, dass diese Tabelle für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

	{
	  "name": "AzureBlobInput",
	  "properties": {
	    "type": "AzureBlob",
	    "linkedServiceName": "StorageLinkedService",
	    "typeProperties": {
	      "Path": "mycontainer/myfolder/yearno={Year}/monthno={Month}/dayno={Day}",
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
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
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


**Azure Data Lake-Ausgabedataset:**

Im Beispiel werden Daten in einen Azure Data Lake-Speicher kopiert. Neue Daten werden stündlich in den Data Lake-Speicher kopiert.

	{
		"name": "AzureDataLakeStoreOutput",
	  	"properties": {
			"type": "AzureDataLakeStore",
		    "linkedServiceName": " AzureDataLakeStoreLinkedService",
		    "typeProperties": {
				"folderPath": "datalake/output/"
		    },
	    	"availability": {
	      		"frequency": "Hour",
	      		"interval": 1
	    	}
	  	}
	}



**Pipeline mit Kopieraktivität:**

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **BlobSource** und der Typ **sink** auf **AzureDataLakeStoreSink** festgelegt.

	{  
	    "name":"SamplePipeline",
	    "properties":
		{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline with copy activity",
	    	"activities":
			[  
	      		{
	        		"name": "AzureBlobtoDataLake",
		        	"description": "Copy Activity",
		        	"type": "Copy",
		        	"inputs": [
		          	{
			            "name": "AzureBlobInput"
		          	}
		        	],
		        	"outputs": [
		          	{
			            "name": "AzureDataLakeStoreOutput"
		          	}
		        	],
		        	"typeProperties": {
			        	"source": {
		            		"type": "BlobSource",
			    			"treatEmptyAsNull": true,
		            		"blobColumnSeparators": ","
		          		},
		          		"sink": {
		            		"type": "AzureDataLakeStoreSink"
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

## Beispiel: Kopieren von Daten aus Azure Data Lake-Speicher in einen Azure-Blob
Das nachstehende Beispiel zeigt Folgendes:

1.	Einen verknüpften Dienst des Typs [AzureDataLakeStore](#azure-data-lake-linked-service-properties)
2.	Einen verknüpften Dienst des Typs [AzureStorage](#azure-storage-linked-service-properties)
3.	Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AzureDataLakeStore](#azure-data-lake-dataset-type-properties)
4.	Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](#azure-blob-dataset-type-properties)
5.	Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) und [BlobSink](#azure-blob-copy-activity-type-properties) verwendet

Im Beispiel werden zu einer Zeitreihe gehörende Daten aus einem Azure Data Lake-Speicher stündlich in einen Azure-Blob kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit Azure Data Lake-Speicher verknüpfter Dienst:**

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeUri": "https://<accountname>.azuredatalake.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

> [AZURE.NOTE]Siehe die Schritte im vorherigen Beispiel zum Abrufen der Autorisierungs-URL.

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

**Azure Data Lake-Eingabedataset:**

Durch Festlegen von **„external“: „true“** und Angeben der Richtlinie **externalData** wird dem Azure Data Factory-Dienst angegeben, dass diese Tabelle für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

	{
		"name": "AzureDataLakeStoreInput",
	  	"properties":
		{
	    	"type": "AzureDataLakeStore",
	    	"linkedServiceName": "AzureDataLakeStoreLinkedService",
		    "typeProperties": {
				"folderPath": "datalake/input/",
            	"fileName": "SearchLog.tsv",
            	"format": {
                	"type": "TextFormat",
	                "rowDelimiter": "\n",
    	            "columnDelimiter": "\t"
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

**Azure-Blob-Ausgabedataset:**

Daten werden stündlich in ein neues Blob geschrieben ("frequency": "hour", "interval": 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

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
	            "format": "MM"
	          }
	        },
	        {
	          "name": "Day",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "dd"
	          }
	        },
	        {
	          "name": "Hour",
	          "value": {
	            "type": "DateTime",
	            "date": "SliceStart",
	            "format": "HH"
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

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **AzureDataLakeStoreSource** und der Typ **sink** auf **BlobSink** festgelegt.


	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    	"start":"2014-06-01T18:00:00",
	    	"end":"2014-06-01T19:00:00",
	    	"description":"pipeline for copy activity",
	    	"activities":[  
	      		{
	        		"name": "AzureDakeLaketoBlob",
		    	    "description": "copy activity",
		    	    "type": "Copy",
		    	    "inputs": [
		    	      {
		    	        "name": "AzureDataLakeStoreInput"
		    	      }
		    	    ],
		    	    "outputs": [
		    	      {
		    	        "name": "AzureBlobOutput"
		    	      }
		    	    ],
		    	    "typeProperties": {
		    	    	"source": {
		            		"type": "AzureDataLakeStoreSource",
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


## Eigenschaften des mit Azure Data Lake-Speicher verknüpften Diensts

Sie können einen mit Azure Storage verknüpften Dienst verwenden, um ein Azure-Speicherkonto mit einer Azure Data Factory zu verknüpfen. Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit Azure Storage verknüpften Dienst spezifisch sind.

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- |
| Typ | Die „type“-Eigenschaft muss auf **AzureDataLakeStore** festgelegt sein. | Ja |
| dataLakeUri | Geben Sie Informationen zum Azure Data Lake-Speicherkonto an. Es hat das folgende Format: https://<Azure Data Lake account name>.azuredatalake.net/webhdfs/v1 | Ja |
| authorization | Klicken Sie im **Data Factory-Editor** auf die Schaltfläche **Autorisieren**, und geben Sie Ihre Anmeldeinformationen ein, wodurch die automatisch generierte Autorisierungs-URL dieser Eigenschaft zugewiesen wird. | Ja |
| sessionid | OAuth-Sitzungs-ID aus der Oauth-Autorisierungssitzung. Jede Sitzung-ID ist eindeutig und darf nur einmal verwendet werden. Sie wird automatisch generiert, wenn Sie den Data Factory-Editor verwenden. | Ja |  
| accountName | Data Lake-Kontoname | Nein |
| subscriptionId | Azure-Abonnement-ID. | Nein (falls nicht angegeben, wird das Abonnement der Data Factory verwendet). |
| resourceGroupName | Azure-Ressourcengruppenname | Nein (falls nicht angegeben, wird die Ressourcengruppe der Data Factory verwendet). |


## Eigenschaften von Datasets des Typs „Azure Data Lake“

Eine vollständige Liste der JSON-Abschnitte und -Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie "structure", "availability" und "policy" des JSON-Codes eines Datasets sind bei allen Typen von Datasets (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt **typeProperties** unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort, Format usw. der Daten im Datenspeicher. Der Abschnitt „typeProperties“ für ein Dataset vom Typ **AzureDataLakeStore** hat die folgenden Eigenschaften.

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- |
| folderPath | Der Pfad zum Container und Ordner im Azure Data Lake-Speicher. | Ja |
| fileName | <p>Der Name der Datei im Azure Data Lake-Speicher. „fileName“ ist optional. </p><p>Wenn Sie einen Dateinamen angeben, funktioniert die Aktivität (einschließlich Kopieren) für die jeweilige Datei.</p><p>Wenn „fileName„ nicht angegeben ist, umfasst das Kopieren alle Dateien in „folderPath“ für das Eingabedataset.</p><p>Wenn „fileName“ für ein Ausgabedataset nicht angegeben ist, hat der Name der generierten Datei folgendes Format: Data.<Guid>.txt (z. B.: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt</p> | Nein |
| partitionedBy | "partitionedBy" ist eine optionale Eigenschaft. "partitionedBy" kann genutzt werden, um einen dynamischen Wert für "folderPath" oder "fileName" für Zeitreihendaten anzugeben. Beispiel: "folderPath" kann für jedes stündliche Datenaufkommen parametrisiert werden. Im Abschnitt „Nutzen der partitionedBy-Eigenschaft“ unten finden Sie Details und Beispiele. | Nein |

### Nutzen der Eigenschaft "partitionedBy"
Wie bereits erwähnt, können Sie die dynamischen Werte „folderPath“ und „fileName“ für Zeitreihendaten mit dem Abschnitt **partitionedBy**, mit Data Factory-Makros und mit den Systemvariablen „SliceStart“ und „SliceEnd“ angeben, die die Start- und Endzeit für einen bestimmten Slice festlegen.

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

## Eigenschaften von Azure Data Lake-Kopieraktivitätstypen  
Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen, verschiedene Richtlinien usw. sind für alle Arten von Aktivitäten verfügbar.

Im Abschnitt "typeProperties" der Aktivität verfügbare Eigenschaften variieren hingegen bei jedem Aktivitätstyp. Bei der Kopieraktivität variieren sie je nach Typ der Quellen und Senken.

**AzureDataLakeStoreSource** unterstützt die folgenden Eigenschaften im Abschnitt **typeProperties**:

| Eigenschaft | Beschreibung | Zulässige Werte | Erforderlich |
| -------- | ----------- | -------------- | -------- |
| recursive | Gibt an, ob die Daten rekursiv aus den Unterordnern oder nur aus dem angegebenen Ordner gelesen werden. | True (Standardwert), False | Nein |



**AzureDataLakeStoreSink** unterstützt die folgenden Eigenschaften im Abschnitt **typeProperties**:

| Eigenschaft | Beschreibung | Zulässige Werte | Erforderlich |
| -------- | ----------- | -------------- | -------- |
| copyBehavior | Gibt das Kopierverhalten an. | <p>PreserveHierarchy: Behält die Dateihierarchie im Zielordner bei, d. h., der relative Pfad zum Quellordner ist mit dem relativen Pfad der Zieldatei zum Zielordner identisch.</p><p>FlattenHierarchy: Alle Dateien aus dem Quellordner befinden sich auf der ersten Ebene des Zielordners. Die Namen der Zieldateien werden automatisch generiert. </p><p>MergeFiles: Führt alle Dateien aus dem Quellordner in einer Datei zusammen. Wenn der Datei-/Blobname angegeben wurde, entspricht der Name dem angegebenen Namen, andernfalls dem automatisch generierten Dateinamen.</p> | Nein |


[AZURE.INCLUDE [data-factory-structure-for-rectangular-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=Nov15_HO1-->