<properties
   pageTitle="Laden von Daten mit Azure Data Factory | Microsoft Azure"
   description="Informationen zum Laden von Daten mit Azure Data Factory"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>
<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="02/16/2016"
   ms.author="lodipalm;barbkess;sonyama"/>

# Laden von Daten mit Azure Data Factory

> [AZURE.SELECTOR]
- [Data Factory](sql-data-warehouse-get-started-load-with-azure-data-factory.md)
- [PolyBase](sql-data-warehouse-get-started-load-with-polybase.md)
- [BCP](sql-data-warehouse-load-with-bcp.md)

 In diesem Tutorial wird gezeigt, wie Sie eine Pipeline in Azure Data Factory erstellen, mit deren Hilfe Daten aus Azure Storage-Blobs in ein SQL Data Warehouse verschoben werden. Mit den folgenden Schritten können Sie folgende Aktionen ausführen:

+ Einrichten der Beispieldaten in einem Azure Storage-Blob.
+ Verbinden von Ressourcen mit Azure Data Factory.
+ Erstellen einer Pipeline zum Verschieben von Daten aus Storage-Blobs in SQL Data Warehouse.

>[AZURE.VIDEO loading-azure-sql-data-warehouse-with-azure-data-factory]


## Voraussetzungen

Machen Sie sich unter [Einführung in Azure Data Factory](../data-factory/data-factory-introduction.md) mit Azure Data Factory vertraut.

### Erstellen oder Identifizieren von Ressourcen

Bevor Sie mit diesem Tutorial beginnen, müssen Sie über die folgenden Ressourcen verfügen.

   + **Azure Storage-Blob**: In diesem Tutorial wird Azure Storage-Blob als Datenquelle für die Azure Data Factory-Pipeline verwendet, daher benötigen Sie eines zum Speichern der Beispieldaten. Wenn Sie noch keines haben, finden Sie unter [Erstellen eines Speicherkontos](../storage/storage-create-storage-account/#create-a-storage-accoun/) weitere Informationen. 

   + **SQL Data Warehouse**: In diesem Tutorial werden die Daten aus dem Azure Storage-Blob in SQL Data Warehouse verschoben. Daher muss ein Data Warehouse online sein, in das die AdventureWorksDW-Beispieldaten geladen wurden. Wenn Sie noch kein Data Warehouse haben, erfahren Sie hier, wie [eines bereitstellt wird](sql-data-warehouse-get-started-provision.md). Wenn Sie ein Data Warehouse haben, jedoch die Beispieldaten nicht darin bereitgestellt haben, können Sie es [manuell laden](sql-data-warehouse-get-started-manually-load-samples.md).

   + **Azure Data Factory**: Azure Data Factory schließt die tatsächliche Last ab, daher müssen Sie über eine verfügen, die Sie zum Erstellen der Datenverschiebungspipeline verwenden können. Wenn Sie noch keine haben, erfahren Sie In Schritt 1 von [Erste Schritte mit Azure Data Factory (Data Factory Edition)](../data-factory/data-factory-build-your-first-pipeline-using-editor.md), wie Sie eine erstellen.

   + **AZCopy**: Sie benötigen AZCopy, um die Beispieldaten von Ihrem lokalen Client in Ihr Azure Storage-Blob zu kopieren. Anweisungen zur Installation finden Sie in der [AZCopy-Dokumentation](../storage/storage-use-azcopy.md).

## Schritt 1: Kopieren von Beispieldaten in Azure Storage-Blob

Sobald alle Bestandteile bereitstehen, können Sie Beispieldaten in Ihr Azure Storage-Blob kopieren.

1. [Laden Sie Beispieldaten herunter](https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv). Mit diesen Daten werden Ihren AdventureWorksDW-Beispieldaten Umsatzdaten aus drei weiteren Jahren hinzugefügt.

2. Verwenden Sie diesen AZCopy-Befehl, um die Daten aus drei Jahren in Ihr Azure Storage-Blob zu kopieren.

````
AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv
````


## Schritt 2: Verbinden von Ressourcen mit Azure Data Factory

Da die Daten jetzt vorhanden ist, können wir die Azure Data Factory-Pipeline zum Verschieben der Daten aus dem Azure-Blobspeicher in SQL Data Warehouse erstellen.

Öffnen Sie zuerst das [Azure-Portal](https://portal.azure.com/), und wählen Sie im Menü auf der linken Seite Ihre Data Factory aus.

### Schritt 2.1: Erstellen eines verknüpften Dienstes

Verknüpfen Sie Ihr Azure-Speicherkonto und SQL Data Warehouse mit Ihrer Data Factory.

1. Starten Sie zunächst den Registrierungsvorgang, indem Sie auf den Abschnitt „Verknüpfte Dienste“ Ihrer Data Factory und dann auf „Neuer Datenspeicher“ klicken. Wählen Sie einen Namen aus, unter dem Sie Ihren Azure-Speicher registrieren möchten, wählen Sie Azure Storage als Typ aus, und geben Sie dann Ihren Kontonamen und Kontoschlüssel ein.

2. Zum Registrieren von SQL Data Warehouse wechseln Sie zum Abschnitt „Erstellen und Bereitstellen“ und wählen dort „Neuer Datenspeicher“ und dann „SQL Azure Data Warehouse“ aus. Kopieren Sie die Informationen, und fügen Sie sie in diese Vorlage ein. Geben Sie anschließend Ihre jeweiligen Informationen ein.

    ````
    {
        "name": "<Linked Service Name>",
	    "properties": {
	        "description": "",
		    "type": "AzureSqlDW",
		    "typeProperties": {
		         "connectionString": "Data Source=tcp:<server name>.database.windows.net,1433;Initial Catalog=<server name>;Integrated Security=False;User ID=<user>@<servername>;Password=<password>;Connect Timeout=30;Encrypt=True"
	         }
        }
    }
    ````

### Schritt 2.2: Definieren des Datasets

Nach dem Erstellen der verknüpften Dienste müssen Sie die Datasets definieren. Dies bedeutet in diesem Fall, dass die Struktur der Daten definiert wird, die aus dem Speicher in das Data Warehouse verschoben werden. Informationen zur Erstellung

1. Um diesen Prozess zu starten, wechseln Sie zum Abschnitt „Erstellen und Bereitstellen“ Ihrer Data Factory.

2. Klicken Sie auf „Neues Dataset“ und dann auf „Azure-Blobspeicher“, um Ihren Speicher mit Ihrer Data Factory zu verknüpfen. Mit dem unten aufgeführten Skript können Sie Daten im Azure-Blobspeicher definieren:

    ````
	{
	    "name": "<Dataset Name>",
		"properties": {
		    "type": "AzureBlob",
			"linkedServiceName": "<linked storage name>",
			"typeProperties": {
			    "folderPath": "<containter name>",
				"fileName": "FactInternetSales.csv",
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
    ````


3. Jetzt definieren wir auch unser Dataset für SQL Data Warehouse. Wir beginnen auf die gleiche Weise, indem wir auf „Neues Dataset“ und dann auf „Azure SQL Data Warehouse“ klicken.

    ````
    {
	    "name": "DWDataset",
		"properties": {
		    "type": "AzureSqlDWTable",
		    "linkedServiceName": "AzureSqlDWLinkedService",
		    "typeProperties": {
			    "tableName": "FactInternetSales"
			},
		    "availability": {
		        "frequency": "Hour",
			    "interval": 1
	        }
        }
    }
    ````

## Schritt 3: Erstellen und Ausführen der Pipeline

Zuletzt richten wir die Pipeline in Azure Data Factory ein und führen sie aus. Dies ist der Vorgang, durch den die eigentliche Datenverschiebung abgeschlossen wird. [Hier](../data-factory/data-factory-azure-sql-data-warehouse-connector.md) finden Sie eine vollständige Ansicht der Vorgänge, die Sie mit SQL Data Warehouse und Azure Data Factory durchführen können.

Klicken Sie im Abschnitt „Erstellen und Bereitstellen“ nun auf „Weitere Befehle“ und dann auf „Neue Pipeline“. Nachdem Sie die Pipeline erstellt haben, können Sie die Daten mit dem folgenden Code in das Data Warehouse übertragen:

````
{
    "name": "<Pipeline Name>",
    "properties": {
        "description": "<Description>",
        "activities": [ 
          {
            "type": "Copy",
    		"typeProperties": {
    		    "source": {
	    		    "type": "BlobSource",
	    			"skipHeaderLineCount": 1
	    	    },
	    		"sink": {
	    		    "type": "SqlDWSink",
	    		    "writeBatchSize": 0,
	    			"writeBatchTimeout": "00:00:10"
	    		}
	    	},
	    	"inputs": [
	    	  {
	    		"name": "<Storage Dataset>"
	    	  }
	    	],
	    	"outputs": [
	    	  {
	    	    "name": "<Data Warehouse Dataset>"
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
	    	"name": "Sample Copy",
	    	"description": "Copy Activity"
	      }
	    ],
	    "start": "<Date YYYY-MM-DD>",
	    "end": "<Date YYYY-MM-DD>",
	    "isPaused": false
    }
}
````

## Nächste Schritte

Weitere Informationen finden Sie unter:

- [Azure Data Factory-Lernpfad](https://azure.microsoft.com/documentation/learning-paths/data-factory/)
- [Azure SQL Data Warehouse-Connector](../data-factory/data-factory-azure-sql-data-warehouse-connector.md). Dies ist das wichtigste Referenzthema zur die Verwendung von Azure Data Factory mit Azure SQL Data Warehouse.


Diese Themen enthalten ausführliche Informationen zu Azure Data Factory. Darin werden Azure SQL-Datenbank oder HDinsight behandelt, die Informationen gelten jedoch auch für Azure SQL Data Warehouse.

- [Tutorial: Erste Schritte mit Azure Data Factory](../data-factory/data-factory-build-your-first-pipeline.md) Dies ist das wichtigste Tutorial zur Datenverarbeitung mit Azure Data Factory. In diesem Tutorial erstellen Sie Ihre erste Pipeline, die mithilfe von HDInsight monatlich Webprotokolle transformiert und analysiert. Beachten Sie, dass in diesem Tutorial keine Kopieraktivität erfolgt.
- [Tutorial: Kopieren von Daten aus einem Azure Storage-Blob in die Azure SQL-Datenbank](../data-factory/data-factory-get-started.md) In diesem Tutorial erstellen Sie in Azure Data Factory eine Pipeline, um Daten aus einem Azure Storage-Blob in Azure SQL-Datenbank zu kopieren.
- [Tutorial zu realistischen Szenarien](../data-factory/data-factory-tutorial.md) Dies ist ein detailliertes Tutorial zur Verwendung von Azure Data Factory.

<!---HONumber=AcomDC_0302_2016-->