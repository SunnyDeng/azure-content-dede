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
	ms.date="01/19/2016"
	ms.author="spelluru"/>

# Verschieben von Daten in und aus Azure Data Lake-Speicher mithilfe von Azure Data Factory
Dieser Artikel beschreibt die Verwendung der Kopieraktivität in Azure Data Factory, um Daten aus dem Azure Data Lake-Speicher in einen anderen Datenspeicher und umgekehrt zu verschieben. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit der Kopieraktivität und den unterstützten Datenspeicherkombinationen bietet.

> [AZURE.NOTE]
Sie müssen ein Azure Data Lake-Speicherkonto anlegen, bevor Sie eine Pipeline mit einer Kopieraktivität zum Verschieben von Daten in einen bzw. aus einem Azure Data Lake-Speicher erstellen können. Weitere Informationen zu Azure Data Lake-Speicher finden Sie unter [Erste Schritte mit Azure Data Lake-Speicher](../data-lake-store/data-lake-store-get-started-portal.md).
>  
> Im Tutorial [Erstellen Ihrer ersten Pipeline](data-factory-build-your-first-pipeline.md) finden Sie detaillierte Anweisungen zum Erstellen von Data Factorys, verknüpften Diensten, Datasets und Pipelines. Verwenden Sie die JSON-Codeausschnitte mit Data Factory-Editor, Visual Studio oder Azure PowerShell, um die Data Factory-Entitäten zu erstellen.

In den folgenden Beispielen wird veranschaulicht, wie Sie Daten in und aus Azure Data Lake-Speicher und Azure-BLOB-Speicher kopieren. Daten können jedoch mithilfe der Kopieraktivität in Azure Data Factory **direkt** aus beliebigen Quellen in die [hier](data-factory-data-movement-activities.md#supported-data-stores) aufgeführten Senken kopiert werden.


## Beispiel: Kopieren von Daten aus einem Azure-Blob in Azure Data Lake-Speicher
Das nachstehende Beispiel zeigt Folgendes:

1.	Einen verknüpften Dienst des Typs [AzureStorage](#azure-storage-linked-service-properties)
2.	Einen verknüpften Dienst des Typs [AzureDataLakeStore](#azure-data-lake-linked-service-properties)
3.	Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](#azure-blob-dataset-type-properties)
4.	Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureDataLakeStore](#azure-data-lake-dataset-type-properties)
4.	Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [BlobSource](#azure-blob-copy-activity-type-properties) und [AzureDataLakeStoreSink](#azure-data-lake-copy-activity-type-properties) verwendet

Im Beispiel werden zu einer Zeitreihe gehörende Daten aus Azure Blob Storage stündlich in Azure Data Lake-Speicher kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.


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
	            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

### Erstellen eines mit Azure Data Lake verknüpften Diensts mit Data Factory-Editor
Das folgende Verfahren enthält Schritte zum Erstellen eines mit Azure Data Lake-Speicher verknüpften Diensts mit dem Data Factory-Editor.

1. Klicken Sie auf der Befehlsleiste auf **Neuer Datenspeicher**, und wählen Sie **Azure Data Lake-Speicher** aus.
2. Geben Sie im JSON-Editor für die **datalakeUri**-Eigenschaft den URI für die Data Lake-Instanz ein.
3. Klicken Sie auf der Befehlsleiste auf die Schaltfläche **Autorisieren**. Ein Popupfenster wird angezeigt.

	![Schaltfläche „Autorisieren“](./media/data-factory-azure-data-lake-connector/authorize-button.png)

4. Melden Sie sich mit Ihren Anmeldeinformationen an. Der **authorization**-Eigenschaft in der JSON sollte nun ein Wert zugewiesen sein.
5. (Optional) Geben Sie Werte für optionale Parameter wie z. B. **accountName**, **subscriptionID** und **resourceGroupName** in der JSON-Datei an, (oder) löschen Sie diese Eigenschaften aus der JSON-Datei.
6. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.

> [AZURE.IMPORTANT] Der von Ihnen mithilfe der Schaltfläche **Autorisieren** generierte Autorisierungscode läuft nach einer gewissen Zeit ab. Wenn das **Token abläuft**, müssen Sie mithilfe der Schaltfläche **Autorisieren** eine **erneute Autorisierung** ausführen und den verknüpften Dienst erneut bereitstellen. Weitere Informationen finden Sie im Abschnitt [Mit Azure Data Lake-Speicher verknüpfter Dienst](#azure-data-lake-store-linked-service-properties).



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

## Beispiel: Kopieren von Daten aus Azure Data Lake-Speicher in ein Azure-Blob
Das nachstehende Beispiel zeigt Folgendes:

1.	Einen verknüpften Dienst des Typs [AzureDataLakeStore](#azure-data-lake-linked-service-properties)
2.	Einen verknüpften Dienst des Typs [AzureStorage](#azure-storage-linked-service-properties)
3.	Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [AzureDataLakeStore](#azure-data-lake-dataset-type-properties)
4.	Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](#azure-blob-dataset-type-properties)
5.	Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [AzureDataLakeStoreSource](#azure-data-lake-copy-activity-type-properties) und [BlobSink](#azure-blob-copy-activity-type-properties) verwendet

Im Beispiel werden zu einer Zeitreihe gehörende Daten aus einem Azure Data Lake-Speicher stündlich in ein Azure-Blob kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit Azure Data Lake-Speicher verknüpfter Dienst:**

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

> [AZURE.NOTE] Siehe die Schritte im vorherigen Beispiel zum Abrufen der Autorisierungs-URL.

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

Durch das Festlegen von **"external": true** und das Angeben der Richtlinie **externalData** wird dem Azure Data Factory-Dienst mitgeteilt, dass diese Tabelle für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

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
| :-------- | :----------- | :-------- |
| type | Die type-Eigenschaft muss auf **AzureDataLakeStore** festgelegt sein. | Ja |
| dataLakeUri | Geben Sie Informationen zum Azure Data Lake-Speicherkonto an. Der URI hat das folgende Format: https://<Azure Data Lake account name>.azuredatalakestore.net/webhdfs/v1. | Ja |
| authorization | Klicken Sie im **Data Factory-Editor** auf die Schaltfläche **Autorisieren**, und geben Sie Ihre Anmeldeinformationen ein, wodurch die automatisch generierte Autorisierungs-URL dieser Eigenschaft zugewiesen wird. | Ja |
| sessionId | OAuth-Sitzungs-ID aus der OAuth-Autorisierungssitzung. Jede Sitzungs-ID ist eindeutig und darf nur einmal verwendet werden. Sie wird automatisch generiert, wenn Sie den Data Factory-Editor verwenden. | Ja |  
| accountName | Data Lake-Kontoname | Nein |
| subscriptionId | Azure-Abonnement-ID | Nein (falls nicht angegeben, wird das Abonnement der Data Factory verwendet). |
| ResourceGroupName | Azure-Ressourcengruppenname | Nein (falls nicht angegeben, wird die Ressourcengruppe der Data Factory verwendet). |

Der von Ihnen mithilfe der Schaltfläche **Autorisieren** generierte Autorisierungscode läuft nach einer gewissen Zeit ab. Die Zeiten bis zum Ablaufen der Autorisierungscodes für die verschiedenen Benutzerkonten finden Sie in der folgenden Tabelle. Unter Umständen wird Ihnen nach dem **Ablauf des Tokens** folgende Fehlermeldung angezeigt: „Fehler beim Anmeldevorgang: invalid\_grant – AADSTS70002: Fehler beim Überprüfen der Anmeldeinformationen. AADSTS70008: Die angegebene Zugriffserteilung ist abgelaufen oder wurde widerrufen. Ablaufverfolgungs-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelations-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Zeitstempel: 2015-12-15 21-09-31Z“.


| Benutzertyp | Läuft ab nach |
| :-------- | :----------- | 
| Nicht-AAD-Benutzer(@hotmail.com, @live.com usw.) | 12 Stunden |
| AAD-Benutzer, und die OAuth-basierte Quelle befindet sich in einem anderen [Mandanten](https://msdn.microsoft.com/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant) als dem für die Data Factory | 12 Stunden |
| AAD-Benutzer, und die OAuth-basierte Quelle befindet sich im gleichen Mandanten wie dem für die Data Factory. | 14 Tage |

Um diesen Fehler zu vermeiden oder zu beheben, müssen Sie bei **Ablauf des Tokens** mithilfe der Schaltfläche **Autorisieren** eine erneute Autorisierung ausführen und den verknüpften Dienst erneut bereitstellen. Sie können auch programmgesteuert Werte für die Eigenschaften **sessionId** und **authorization** generieren. Verwenden Sie dazu den im folgenden Abschnitt bereitgestellten Code.

### So generieren Sie programmgesteuert Werte für „sessionId“ und „authorization“ 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Details zu den im Code verwendeten Data Factory-Klassen finden Sie in den Themen [AzureDataLakeStoreLinkedService-Klasse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService-Klasse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) und [AuthorizationSessionGetResponse-Klasse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Sie müssen für die WindowsFormsWebAuthenticationDialog-Klasse einen Verweis hinzufügen auf: „Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll“.
 

## Eigenschaften von Datasets des Typs „Azure Data Lake“

Eine vollständige Liste der JSON-Abschnitte und -Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie "structure", "availability" und "policy" des JSON-Codes eines Datasets sind bei allen Typen von Datasets (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt **typeProperties** unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort, Format usw. der Daten im Datenspeicher. Der Abschnitt „typeProperties“ für ein Dataset vom Typ **AzureDataLakeStore** hat die folgenden Eigenschaften.

| Eigenschaft | Beschreibung | Erforderlich |
| :-------- | :----------- | :-------- |
| folderPath | Der Pfad zum Container und Ordner im Azure Data Lake-Speicher. | Ja |
| fileName | <p>Der Name der Datei im Azure Data Lake-Speicher. „fileName“ ist optional. </p><p>Wenn Sie einen Dateinamen angeben, funktioniert die Aktivität (einschließlich Kopieren) für die jeweilige Datei.</p><p>Wenn „fileName„ nicht angegeben ist, umfasst das Kopieren alle Dateien in „folderPath“ für das Eingabedataset.</p><p>Wenn „fileName“ für ein Ausgabedataset nicht angegeben ist, hat der Name der generierten Datei folgendes Format: Data.<Guid>.txt (z. B.: Data.0a405f8a-93ff-4c6f-b3be-f69616f1df7a.txt</p> | Nein |
| partitionedBy | "partitionedBy" ist eine optionale Eigenschaft. "partitionedBy" kann genutzt werden, um einen dynamischen Wert für "folderPath" oder "fileName" für Zeitreihendaten anzugeben. Beispiel: "folderPath" kann für jedes stündliche Datenaufkommen parametrisiert werden. Im Abschnitt „Nutzen der partitionedBy-Eigenschaft“ unten finden Sie Details und Beispiele. | Nein |
| Format | Zwei Typen von Formaten werden unterstützt: **TextFormat** und **AvroFormat**. Sie müssen die type-Eigenschaft unter "format" auf einen dieser Werte festlegen. Wenn das Format auf "TextFormat" festgelegt ist, können Sie zusätzliche optionale Eigenschaften für das Format angeben. Im Abschnitt [Angeben von "TextFormat"](#specifying-textformat) unten finden Sie weitere Details. | Nein |
| Komprimierung | Geben Sie den Typ und den Grad der Komprimierung für die Daten an. Folgende Typen werden unterstützt: "GZip", "Deflate" und "BZip2". Folgende Komprimierungsgrade werden unterstützt: "Optimal" und "Schnellste". Weitere Einzelheiten finden Sie im Abschnitt [Komprimierungsunterstützung](#compression-support). | Nein |

### Nutzen der Eigenschaft "partitionedBy"
Wie bereits erwähnt, können Sie die dynamischen Werte "folderPath" und "fileName" für Zeitreihendaten mit dem Abschnitt **partitionedBy**, mit Data Factory-Makros und mit den Systemvariablen "SliceStart" und "SliceEnd" angeben, die die Start- und Endzeit für einen bestimmten Slice festlegen.

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
| columnDelimiter | Das Zeichen, das als Spaltentrennzeichen in einer Datei verwendet wird. Derzeit ist nur ein Zeichen zulässig. Dieses Tag ist optional. Der Standardwert ist das Komma (,). | Nein |
| rowDelimiter | Das Zeichen, das als Zeilentrennzeichen in einer Datei verwendet wird. Derzeit ist nur ein Zeichen zulässig. Dieses Tag ist optional. Der Standardwert ist einer der Folgenden: ["\\r\\n", "\\r", "\\n"]. | Nein |
| escapeChar | <p>Das Sonderzeichen, das als Escapezeichen für das Spaltentrennzeichen im Inhalt dient. Dieses Tag ist optional. Kein Standardwert. Sie dürfen nicht mehr als ein Zeichen für diese Eigenschaft angeben.</p><p>Beispiel: Wenn Sie das Komma (,) als Spaltentrennzeichen gewählt haben, das Kommazeichen jedoch im Text (Beispiel: "Hello, world") verwenden möchten, können Sie "$" als Escapezeichen definieren und die Zeichenfolge "Hello$, world" in der Quelle verwenden.</p><p>Beachten Sie, dass Sie nicht sowohl "escapeChar" als auch "quoteChar" für eine Tabelle angeben können.</p> | Nein | 
| quoteChar | <p>Das Sonderzeichen, das als Anführungszeichen für einen Zeichenfolgenwert dient. Die Spalten- und Zeilentrennzeichen innerhalb der Anführungszeichen werden als Teil des Zeichenfolgenwerts behandelt. Dieses Tag ist optional. Kein Standardwert. Sie dürfen nicht mehr als ein Zeichen für diese Eigenschaft angeben.</p><p>Beispiel: Wenn Sie das Komma (,) als Spaltentrennzeichen gewählt haben, das Kommazeichen jedoch im Text (Beispiel: <Hello  world>) verwenden möchten, können Sie '"' als Anführungszeichen definieren und die Zeichenfolge <"Hello, world"> in der Quelle verwenden. Diese Eigenschaft gilt für Eingabe- und Ausgabetabellen.</p><p>Beachten Sie, dass Sie nicht sowohl "escapeChar" als auch "quoteChar" für eine Tabelle angeben können.</p> | Nein |
| nullValue | <p>Die Zeichen, die zur Darstellung von NULL-Werten im Blobdateiinhalt dienen. Dieses Tag ist optional. Der Standardwert ist "\\N".</p><p>Beispielsweise wird gemäß oben genanntem Beispiel "NaN" im Blob beim Kopieren, z. B. in SQL Server, als NULL-Wert übersetzt.</p> | Nein |
| encodingName | Geben Sie den Codierungsnamen an. Eine Liste der gültigen Codierungsnamen finden Sie unter [Encoding.EncodingName-Eigenschaft](https://msdn.microsoft.com/library/system.text.encoding.aspx). Beispiel: Windows-1250 oder Shift-JIS. Der Standardwert lautet "UTF-8". | Nein | 

#### Beispiele
Im folgenden Beispiel werden einige der Formateigenschaften für "TextFormat" gezeigt.

	"typeProperties":
	{
	    "folderPath": "mycontainer/myfolder",
	    "fileName": "myfilename"
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


### Unterstützung für die Komprimierung  
Die Verarbeitung großer Datenmengen kann zu E/A- und Netzwerkengpässen führen. Aus diesem Grund kann die Komprimierung von Daten in Speichern nicht nur die Datenübertragung im Netzwerk beschleunigen und Speicherplatz sparen, sondern auch erhebliche Leistungssteigerungen bei der Verarbeitung von Big Data bewirken. Zu diesem Zeitpunkt wird die Komprimierung für dateibasierte Datenspeicher wie etwa Azure-Blobs oder das lokale Dateisystem unterstützt.

Um die Komprimierung für ein Dataset anzugeben, verwenden Sie im JSON-Dataset die Eigenschaft für die **Komprimierung** wie im folgenden Beispiel:

	{  
		"name": "AzureDatalakeStoreDataSet",  
	  	"properties": {  
	    	"availability": {  
	    		"frequency": "Day",  
	    	  	"interval": 1  
	    	},  
	    	"type": "AzureDatalakeStore",  
	    	"linkedServiceName": "DataLakeStoreLinkedService",  
	    	"typeProperties": {  
	    		"fileName": "pagecounts.csv.gz",  
	    	  	"folderPath": "compression/file/",  
	    	  	"compression": {  
	    	    	"type": "GZip",  
	    	    	"level": "Optimal"  
	    	  	}  
    		}  
	  	}  
	}  
 
Beachten Sie, dass der Abschnitt für die **Komprimierung** zwei Eigenschaften enthält:
  
- **Typ:** Der Komprimierungscodec, z. B. **GZIP**, **Deflate** oder **BZIP2**.  
- **Ebene:** Das Komprimierungsverhältnis, z. B. **Optimal** oder **Schnellstes**. 
	- **Schnellstes:** Der Komprimierungsvorgang wird schnellstmöglich abgeschlossen, auch wenn die resultierende Datei nicht optimal komprimiert ist. 
	- **Optimal**: Die Daten sollten optimal komprimiert sein, auch wenn der Vorgang eine längere Zeit in Anspruch nimmt. 
	
	Weitere Informationen finden Sie im Thema [Komprimierungsstufe](https://msdn.microsoft.com/library/system.io.compression.compressionlevel.aspx).

Angenommen, das obige Beispieldataset wird als Ausgabe einer Kopieraktivität verwendet. Die Kopieraktivität komprimiert die Ausgabedaten mit dem GZIP-Codec bei optimalem Verhältnis und schreibt die komprimierten Daten anschließend in die Datei „pagecounts.csv.gz“ im Azure Data Lake-Speicher.

Wenn Sie die Komprimierungseigenschaft in einem JSON-Eingabedataset angeben, kann die Pipeline komprimierte Daten aus der Quelle lesen. Bei Angabe der Eigenschaft in einem JSON-Ausgabedataset kann der Kopiervorgang komprimierte Daten am Ziel schreiben. Es folgen einige Beispielszenarios:

- Sie lesen GZIP-komprimierte Daten aus einem Azure Data Lake-Speicher, dekomprimieren sie und schreiben die resultierenden Daten in eine Azure SQL-Datenbank. In diesem Fall definieren Sie die Azure Data Lake-Speichereingabedatasets mit der JSON-Komprimierungseigenschaft. 
- Sie lesen Daten aus einer Nur-Text-Datei aus einem lokalen Dateisystem, komprimieren sie mithilfe des GZIP-Formats und schreiben die komprimierten Daten in einen Azure Data Lake-Speicher. In diesem Fall definieren Sie ein Azure Data Lake-Ausgabedataset mit der JSON-Komprimierungseigenschaft.  
- Sie lesen mit GZIP komprimierte Daten aus einem Azure Data Lake-Speicher, dekomprimieren sie, komprimieren sie mit BZIP2 und schreiben die resultierenden Daten in einen Azure Data Lake-Speicher. In diesem Fall definieren Sie das Azure Data Lake-Speichereingabedataset mit dem Komprimierungstyp GZIP und das Ausgabedataset mit dem Komprimierungstyp BZIP2.   


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
| copyBehavior | Gibt das Kopierverhalten an. | <p>**PreserveHierarchy:** Behält die Dateihierarchie im Zielordner bei, d. h., der relative Pfad zum Quellordner ist mit dem relativen Pfad der Zieldatei zum Zielordner identisch.</p><p>**FlattenHierarchy:** Alle Dateien aus dem Quellordner befinden sich auf der ersten Ebene des Zielordners. Die Namen der Zieldateien werden automatisch generiert.</p><p>**MergeFiles:** Führt alle Dateien aus dem Quellordner in einer Datei zusammen. Wenn der Datei-/Blob-Name angegeben wurde, entspricht der Name der zusammengeführten Datei dem angegebenen Namen, andernfalls wird ein automatisch generierter Dateiname verwendet.</p> | Nein |


[AZURE.INCLUDE [data-factory-structure-for-rectangular-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

[AZURE.INCLUDE [data-factory-type-conversion-sample](../../includes/data-factory-type-conversion-sample.md)]

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

<!---HONumber=AcomDC_0204_2016-->