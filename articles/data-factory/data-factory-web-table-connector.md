<properties 
	pageTitle="Verschieben von Daten aus einer Webtabelle | Azure Data Factory" 
	description="Erfahren Sie, wie Sie Daten aus einer lokalen Tabelle mithilfe von Azure Data Factory auf eine Webseite verschieben." 
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
	ms.date="02/16/2016" 
	ms.author="spelluru"/>

# Verschieben von Daten aus einer Webtabelle mithilfe von Azure Data Factory
Dieser Artikel beschreibt, wie Sie die Kopieraktivität in einer Azure Data Factory verwenden können, um Daten aus einer Tabelle in einer Webseite in einen anderen Datenspeicher zu verschieben. Dieser Artikel baut auf dem Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) auf, der eine allgemeine Übersicht zur Datenverschiebung mit Kopieraktivität und unterstützten Datenspeicherkombinationen bietet.

Data Factory unterstützt derzeit nur das Verschieben von Daten aus einer Webtabelle in andere Datenspeicher, aber nicht das Verschieben aus anderen Datenspeichern in eine Webtabelle.

## Beispiel: Kopieren von Daten aus einer Webtabelle in ein Azure-Blob

Das nachstehende Beispiel zeigt Folgendes:

1.	Einen verknüpften Dienst des Typs [Web](#web-linked-service-properties).
2.	Einen verknüpften Dienst des Typs [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service-properties)
3.	Ein [Eingabedataset](data-factory-create-datasets.md) des Typs [WebTabe](#WebTable-dataset-properties)
4.	Ein [Ausgabedataset](data-factory-create-datasets.md) des Typs [AzureBlob](data-factory-azure-blob-connector.md#azure-blob-dataset-type-properties)
4.	Eine [Pipeline](data-factory-create-pipelines.md) mit Kopieraktivität, die [WebSource](#websource-copy-activity-type-properties) und [BlobSink](data-factory-azure-blob-connector.md#azure-blob-copy-activity-type-properties) verwendet

Im Beispiel werden stündlich Daten aus einer Webtabelle in ein Azure-Blob kopiert. Die bei diesen Beispielen verwendeten JSON-Eigenschaften werden in den Abschnitten beschrieben, die auf die Beispiele folgen.

In diesem Beispiel wird gezeigt, wie Sie Daten aus einer Webtabelle in ein Azure-Blob kopieren. Daten können jedoch mithilfe der Kopieraktivität in Azure Data Factory direkt in die im Artikel [Datenverschiebungsaktivitäten](data-factory-data-movement-activities.md) aufgeführten Senken kopiert werden.

**Verknüpfter Webdienst** In diesem Beispiel wird der verknüpfte Webdienst mit anonymer Authentifizierung verwendet. Informationen zu den verschiedenen Authentifizierungstypen finden Sie im Abschnitt [Eigenschaften des Verknüpften Webdiensts](#web-linked-service-properties).

	{
	    "name": "WebLinkedService",
	    "properties":
	    {
	        "type": "Web",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "url" : "http://finance.yahoo.com/q/"
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

**WebTable-Eingabedataset** Durch Festlegen von **external** auf **true** und (optionales) Angeben der Richtlinie **externalData** wird dem Data Factory-Dienst mitgeteilt, dass dies eine Tabelle ist, die für die Data Factory extern ist und nicht durch eine Aktivität in der Data Factory erzeugt wird.

> [AZURE.NOTE] Im Abschnitt [Abrufen des Indexes einer Tabelle auf einer HTML-Seite](#get-index-of-a-table-in-an-html-page) werden die Schritte zum Abrufen des Indexes einer Tabelle auf einer HTML-Seite beschrieben.

	
	{
	    "name": "WebTableInput",
	    "properties": {
	        "type": "WebTable",
	        "linkedServiceName": "WebLinkedService",
	        "typeProperties": {
	            "index": 4,
	            "path": "hp?s=MSFT+Historical+Prices"
	        },
	        "external": true,
	        "availability": {
	            "frequency": "Hour",
	            "interval":  1
	        }
	    }
	}



**Azure-Blob-Ausgabedataset**

Daten werden stündlich in ein neues Blob geschrieben ("frequency": "hour", "interval": 1).

	{
	    "name": "AzureBlobOutput",
	    "properties":
	    {
	        "type": "AzureBlob",
	        "linkedServiceName": "AzureStorageLinkedService",
	        "typeProperties":
	        {
	            "folderPath": "MSFTHistoricalPrices"
	        },
	        "availability":
	        {
	            "frequency": "Hour",
	            "interval": 1
	        }
	    }
	}




**Pipeline mit Kopieraktivität**

Die Pipeline enthält eine Kopieraktivität, die für das Verwenden der oben genannten Ein- und Ausgabedatasets und für eine stündliche Ausführung konfiguriert ist. In der JSON-Definition der Pipeline ist der Typ **source** auf **WebSource** und der Typ **sink** auf **BlobSink** festgelegt.

Unter [WebSource-Typeigenschaften](#websource-copy-activity-type-properties) finden Sie die Liste der Eigenschaften, die von WebSource unterstützt werden.
	
	{  
	    "name":"SamplePipeline",
	    "properties":{  
	    "start":"2014-06-01T18:00:00",
	    "end":"2014-06-01T19:00:00",
	    "description":"pipeline with copy activity",
	    "activities":[  
	      {
	        "name": "WebTableToAzureBlob",
	        "description": "Copy from a Web table to an Azure blob",
	        "type": "Copy",
	        "inputs": [
	          {
	            "name": "WebTableInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "AzureBlobOutput"
	          }
	        ],
	        "typeProperties": {
	          "source": {
	            "type": "WebSource"
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


## Eigenschaften des Verknüpften Webdiensts

Die folgende Tabelle enthält eine Beschreibung der JSON-Elemente, die für den verknüpften Webdienst spezifisch sind.

| Eigenschaft | Beschreibung | Erforderlich |
| -------- | ----------- | -------- | 
| Typ | Die Typeigenschaft muss auf **Web** festgelegt sein. | Ja | 
| Url | URL der Webquelle | Ja |
| userName | Benutzername für die Standardauthentifizierung. | Ja (für die Standardauthentifizierung)
| Kennwort | Kennwort für die Standardauthentifizierung. | Ja (für die Standardauthentifizierung)
| authenticationType | „Anonymous“, „Basic“ oder „WebAPI“. | Ja |
| apiKey | „ApiKey“ für die WebAPI-Authentifizierung. | Ja (für WebAPI-Authentifizierung)|   

### Verwenden der anonymen Authentifizierung

	{
	    "name": "web",
	    "properties":
	    {
	        "type": "Web",
	        "typeProperties":
	        {
	            "authenticationType": "Anonymous",
	            "url" : "http://finance.yahoo.com/q/"
	        }
	    }
	}


### Verwenden der Standardauthentifizierung
	
	{
	    "name": "web",
	    "properties":
	    {
	        "type": "Web",
	        "typeProperties":
	        {
	            "authenticationType": "basic",
	            "url" : "http://myit.mycompany.com/",
	            "userName": "Administrator",
	            "password": "password"
	        }
	    }
	}


## WebTable-Dataset-Eigenschaften

Eine vollständige Liste der Abschnitte und Eigenschaften, die zum Definieren von Datasets zur Verfügung stehen, finden Sie im Artikel [Erstellen von Datasets](data-factory-create-datasets.md). Abschnitte wie "structure", "availability" und "policy" des JSON-Codes eines Datasets sind bei allen Typen von Datasets (Azure SQL, Azure-Blob, Azure-Tabelle usw.) ähnlich.

Der Abschnitt **typeProperties** unterscheidet sich bei jedem Typ von Dataset und bietet Informationen zum Speicherort der Daten im Datenspeicher. Der Abschnitt „typeProperties“ für ein Dataset des Typs **WebTable** hat die folgenden Eigenschaften.

Eigenschaft | Beschreibung | Erforderlich
:-------- | :----------- | :--------
Typ | Der Typ des Datasets muss auf **WebTable** festgelegt werden. | Ja
path | Eine relative URL zu der Ressource, die die Tabelle enthält. | Nein. Wenn der Pfad nicht angegeben ist, wird nur die URL verwendet, die in der Definition des verknüpften Diensts angegeben ist. 
index | Der Index der Tabelle in der Ressource. Im Abschnitt [Abrufen des Indexes einer Tabelle auf einer HTML-Seite](#get-index-of-a-table-in-an-html-page) werden die Schritte zum Abrufen des Indexes einer Tabelle auf einer HTML-Seite beschrieben. | Ja


**Beispiel:**

	{
	    "name": "WebTableInput",
	    "properties": {
	        "type": "WebTable",
	        "linkedServiceName": "WebLinkedService",
	        "typeProperties": {
	            "index": 4,
	            "path": "hp?s=MSFT+Historical+Prices"
	        },
	        "external": true,
	        "availability": {
	            "frequency": "Hour",
	            "interval":  1
	        }
	    }
	}

## WebSource – Typeigenschaften der Kopieraktivität

Eine vollständige Liste der Abschnitte und Eigenschaften zum Definieren von Aktivitäten finden Sie im Artikel [Erstellen von Pipelines](data-factory-create-pipelines.md). Eigenschaften wie Name, Beschreibung, Eingabe- und Ausgabetabellen, verschiedene Richtlinien usw. sind für alle Arten von Aktivitäten verfügbar.

Im Abschnitt "typeProperties" der Aktivität verfügbare Eigenschaften variieren hingegen bei jedem Aktivitätstyp. Bei der Kopieraktivität variieren sie je nach Typ der Quellen und Senken.

Wenn bei der Kopieraktivität die Quelle vom Typ **WebSource** ist, werden zu diesem Zeitpunkt keine zusätzlichen Eigenschaften unterstützt.

## Abrufen des Indexes einer Tabelle auf einer HTML-Seite

1. Starten Sie **Excel 2016** und wechseln Sie zur Registerkarte **Daten**.  
2. Klicken Sie in der Symbolleiste auf **Neue Abfrage**, zeigen Sie auf **Aus anderen Quellen**, und klicken Sie auf **Aus dem Web**.
	
	![Power Query-Menü](./media/data-factory-web-table-connector/PowerQuery-Menu.png) 
3. Geben Sie im Dialogfeld **Aus dem Web** die **URL**, die Sie im verknüpften Dienst JSON verwenden würden (z. B.: https://en.wikipedia.org/wiki/) zusammen mit den Pfad ein, den Sie für das Dataset angeben würden (z. B.: AFI%27s\_100\_Years... 100\_Movies), und klicken Sie auf **OK**. 

	![Aus dem Web (Dialogfeld)](./media/data-factory-web-table-connector/FromWeb-DialogBox.png)

	In diesem Beispiel verwendete URL: https://en.wikipedia.org/wiki/AFI%27s_100_Years...100_Movies 
4.  Wenn Sie das Dialogfeld **Webinhalt aufrufen** sehen, wählen Sie rechts **URL**, **Authentifizierung**, und klicken Sie auf **Verbinden**. 

	![Webinhalt aufrufen (Dialogfeld)](./media/data-factory-web-table-connector/AccessWebContentDialog.png)
5.  Klicken Sie in der Strukturansicht auf ein **Tabellenelement**, um Inhalt aus der Tabelle anzuzeigen, und klicken Sie dann unten auf **Bearbeiten**.  

	![Navigator (Dialogfeld)](./media/data-factory-web-table-connector/Navigator-DialogBox.png)

5. Klicken Sie im **Abfrage-Editor**-Fenster auf der Symbolleiste auf die Schaltfläche **Erweiterter Editor**.

	![Erweiterter Editor (Schaltfläche)](./media/data-factory-web-table-connector/QueryEditor-AdvancedEditorButton.png)

6. Im Dialogfeld „Erweiterter Editor“ ist die Zahl neben „Quelle“ der Index.

	![Erweiterter Editor – Index](./media/data-factory-web-table-connector/AdvancedEditor-Index.png)


Wenn Sie Excel 2013 verwenden, rufen Sie den Index mit [Microsoft Power Query für Excel](https://www.microsoft.com/download/details.aspx?id=39379) ab. Nähere Informationen finden Sie im Artikel [Verbinden Sie mit einer Webseite (Power Query)](https://support.office.com/article/Connect-to-a-web-page-Power-Query-b2725d67-c9e8-43e6-a590-c0a175bd64d8). Die Schritte sind ähnlich wie bei Verwendung von [Microsoft Power BI Desktop](https://powerbi.microsoft.com/desktop/).

[AZURE.INCLUDE [data-factory-column-mapping](../../includes/data-factory-column-mapping.md)]

[AZURE.INCLUDE [data-factory-structure-for-rectangular-datasets](../../includes/data-factory-structure-for-rectangualr-datasets.md)]

<!---HONumber=AcomDC_0224_2016-->