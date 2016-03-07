<properties 
	pageTitle="Verschieben von Daten aus OData-Quellen | Azure Data Factory" 
	description="Erfahren Sie, wie Sie Daten aus OData-Quellen mithilfe von Azure Data Factory verschieben." 
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
	ms.date="02/18/2016" 
	ms.author="spelluru"/>

# Verschieben von Daten aus einer OData-Quelle mithilfe von Azure Data Factory
Dieser Artikel beschreibt, wie Sie die Kopieraktivität in einer Azure Data Factory verwenden können, um Daten aus einer OData-Quelle in einen anderen Datenspeicher zu verschieben. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit Kopieraktivität und unterstützten Datenspeicherkombinationen bietet.

## Beispiel: Kopieren von Daten aus einer OData-Quelle in Azure Blob

In diesem Beispiel wird gezeigt, wie Sie Daten aus einer OData-Quelle in Azure Blob Storage kopieren. Daten können jedoch mithilfe der Kopieraktivität in Azure Data Factory **direkt** in die [hier](data-factory-data-movement-activities.md#supported-data-stores) aufgeführten Senken kopiert werden.
 
Das Beispiel enthält die folgenden Data Factory-Entitäten:

1.	Einen verknüpften Dienst des Typs [OData](#odata-linked-service-properties)
2.	Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
3.	Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [ODataResource](#odata-dataset-type-properties)
4.	Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)
4.	Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [RelationalSource](#odata-copy-activity-type-properties) und [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) verwendet

Das Beispiel kopiert stündlich Daten durch Abfragen einer OData-Quelle in ein Azure-Blob. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

**Mit OData verknüpfter Dienst**. Bei diesem Beispiel wird die Standardauthentifizierung verwendet. Informationen zu den verwendbaren Authentifizierungstypen finden Sie im Abschnitt [Mit OData verknüpfter Dienst](#odata-linked-service-properties).

    {
		"name": "ODataLinkedService",
       	"properties": 
        {
        	"type": "OData",
           	"typeProperties": 
            {
            	"url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
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

**OData-Eingabedataset:**

Durch Festlegen von "external" auf "true" und Angeben der Richtlinie "externalData" wird dem Data Factory-Dienst angegeben, dass dies eine Tabelle ist, die für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.
	
    {
    	"name": "ODataDataset",
		"properties": 
		{
    		"type": "ODataResource",
    		"typeProperties": 
			{
     			"path": "Products" 
    		},
    		"linkedServiceName": "ODataLinkedService",
    		"structure": [],
    		"availability": {
        		"frequency": "Hour",
        		"interval": 1
    		},
			"external": true,
    		"policy": {
            	"retryInterval": "00:01:00",
                "retryTimeout": "00:10:00",
	            "maximumRetry": 3				
			}
		}
	}

Beachten Sie, dass die Angabe von **path** in der Datasetdefinition optional ist.


**Azure-Blob-Ausgabedataset**

Daten werden stündlich in ein neues Blob geschrieben ("frequency": "hour", "interval": 1). Der Ordnerpfad des Blobs wird basierend auf der Startzeit des Slices, der verarbeitet wird, dynamisch ausgewertet. Im Ordnerpfad werden Jahr, Monat, Tag und die Stundenteile der Startzeit verwendet.

	{
	    "name": "AzureBlobODataDataSet",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties": {
	            "folderPath": "mycontainer/odata/yearno={Year}/monthno={Month}/dayno={Day}/hourno={Hour}",
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

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **RelationalSource** und der Typ **sink** auf **BlobSink** festgelegt. Die SQL-Abfrage für die **query**-Eigenschaft wählt die letzten (neuesten) Daten aus der OData-Quelle aus.
	
	{
	    "name": "CopyODataToBlob",
	    "properties": {
	        "description": "pipeline for copy activity",
	        "activities": [
	            {
	                "type": "Copy",
	                "typeProperties": {
	                    "source": {
	                        "type": "RelationalSource",
	                        "query": "?$select=Name, Description&$top=5",
	                    },
	                    "sink": {
	                        "type": "BlobSink",
	                        "writeBatchSize": 0,
	                        "writeBatchTimeout": "00:00:00"
	                    }
	                },
	                "inputs": [
	                    {
	                        "name": "ODataDataSet"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "AzureBlobODataDataSet"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1
	                },
	                "scheduler": {
	                    "frequency": "Hour",
	                    "interval": 1
	                },
	                "name": "ODataToBlob"
	            }
	        ],
	        "start": "2016-02-01T18:00:00Z",
	        "end": "2016-02-03T19:00:00Z"
	    }
	}


Beachten Sie, dass die Angabe von **query** in der Pipelinedefinition optional ist. Die **URL**, die der Data Factory-Dienst zum Abrufen der Daten verwendet, ergibt sich aus: URL, die im verknüpften Dienst (erforderlich) angegeben wurde + im Dataset (optional) angegebener Pfad + Abfrage in der Pipeline (optional).

## Eigenschaften des mit OData verknüpften Diensts

Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den mit OData verknüpften Dienst spezifisch sind.

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- | 
| Typ | Die type-Eigenschaft muss auf **OData** festgelegt sein. | Ja |
| url| Die URL des OData-Diensts. | Ja |
| authenticationType | Typ der Authentifizierung für die Verbindung mit der OData-Quelle. Mögliche Werte: „Anonymous“ und „Basic“. | Ja | 
| username | Geben Sie den Benutzernamen an, wenn Sie die Standardauthentifizierung (Basic) verwenden. | Ja (nur bei Verwendung der Standardauthentifizierung) | 
| Kennwort | Geben Sie das Kennwort für das Benutzerkonto an, das Sie für den Benutzernamen angegeben haben. | Ja (nur bei Verwendung der Standardauthentifizierung) | 

### Verwenden der Standardauthentifizierung

    {
        "name": "inputLinkedService",
       "properties": 
        {
            "type": "OData",
           	"typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Basic",
                "username": "username",
               "password": "password"
           }
       }
    }

### Verwenden der anonymen Authentifizierung
	
    {
		"name": "ODataLinkedService",
       	"properties": 
        {
            "type": "OData",
           "typeProperties": 
            {
               "url": "http://services.odata.org/OData/OData.svc",
               "authenticationType": "Anonymous"
           }
       }
    }



## Eigenschaften des Datasettyps „OData“

Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie "structure", "availability" und "policy" des JSON-Codes eines Datasets sind bei allen Typen von Datasets (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt **typeProperties** unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt typeProperties für ein Dataset vom Typ **ODataResource** (wozu ein OData-Dataset gehört) hat die folgenden Eigenschaften:

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- |
| path | Pfad zu der OData-Ressource | Nein | 

## Eigenschaften des OData-Kopieraktivitätstyps

Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen, verschiedene Richtlinien usw. sind für alle Arten von Aktivitäten verfügbar.

Im Abschnitt "typeProperties" der Aktivität verfügbare Eigenschaften variieren hingegen bei jedem Aktivitätstyp. Bei der Kopieraktivität variieren sie je nach Typ der Quellen und Senken.

Wenn bei der Kopieraktivität „source“ den Typ **RelationalSource** hat (zu dem OData gehört), sind im Abschnitt typeProperties die folgenden Eigenschaften verfügbar:

| Eigenschaft | Beschreibung | Beispiel | Erforderlich |
| -------- | ----------- | -------------- | -------- |
| query | Verwendet die benutzerdefinierte Abfrage zum Lesen von Daten. | "?$select=Name, Beschreibung&$top=5" | Nein | 

[AZURE.INCLUDE [data-factory-structure-for-rectangular-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

### Typzuordnung für OData

Wie im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) beschrieben, führt die Kopieraktivität automatische Typkonvertierungen von Quelltypen in Senkentypen mithilfe des folgenden aus zwei Schritten bestehenden Ansatzes durch:

1. Konvertieren von systemeigenen Quelltypen in den .NET-Typ
2. Konvertieren vom .NET-Typ in systemeigenen Senkentyp

Beim Verschieben von Daten aus OData-Datenspeichern werden die OData-Datentypen .NET-Typen zugeordnet.


[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-type-repeatability-for-relational-sources](../../includes/data-factory-type-repeatability-for-relational-sources.md)]

<!---HONumber=AcomDC_0224_2016-->