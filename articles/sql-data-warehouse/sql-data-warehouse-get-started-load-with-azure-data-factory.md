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
   ms.date="01/07/2016"
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

## Ressourcen

Für dieses Tutorial benötigen Sie folgende Ressourcen:

   + **Azure Storage-Blob**: Ihr Azure Storage-Blob dient als Quelle der Daten für die Pipeline. Sie können ein vorhandenes Blob verwenden oder [ein neues bereitstellen](../storage/storage-create-storage-account/).

   + **SQL Data Warehouse**: In diesem Tutorial verschieben Sie Daten nach SQL Data Warehouse. Wenn Sie noch keine Instanz eingerichtet haben, erhalten Sie [hier](sql-data-warehouse-get-started-provision.md) Informationen zur Vorgehensweise. Darüber hinaus muss die Instanz mit unserem AdventureWorks DW-Dataset eingerichtet werden. Wenn Sie das Data Warehouse mit den Beispieldaten nicht bereitgestellt haben, können Sie es [manuell laden](sql-data-warehouse-get-started-manually-load-samples.md).

   + **Azure Data Factory**: Azure Data Factory führt den eigentlichen Ladevorgang durch. Wenn Sie weitere Informationen zum Einstellen von Azure Data Factory oder zum Erstellen von Pipelines benötigen, finden Sie diese [hier](../data-factory/data-factory-build-your-first-pipeline-using-editor/).

Sobald alle Komponenten bereit stehen, können Sie Ihre Daten vorbereiten und Ihre Azure Data Factory-Pipeline erstellen.

## Beispieldaten

Zusätzlich zu den einzelnen Teilen der Pipeline benötigen wir auch einige Beispieldaten, mit denen Sie das Laden von Daten in Azure Data Factory üben können.

1. Laden Sie als erstes die [Beispieldaten](https://migrhoststorage.blob.core.windows.net/adfsample/FactInternetSales.csv) herunter. Diese Daten funktionieren in Verbindung mit den Beispieldaten, die sich bei den vorhandenen Beispieldaten befinden, und stellen weitere Vertriebsdaten für drei Jahre bereit.

2. Sobald die Daten heruntergeladen wurden, können Sie sie in Ihren Blobspeicher verschieben, indem Sie das folgende Skript in AZCopy ausführen:

        AzCopy /Source:<Sample Data Location>  /Dest:https://<storage account>.blob.core.windows.net/<container name> /DestKey:<storage key> /Pattern:FactInternetSales.csv

	Weitere Informationen zum Installieren von und Arbeiten mit AZCopy finden Sie in der [AZCopy-Dokumentation](../storage/storage-use-azcopy/).

Nun stehen unsere Daten bereit, und wir können zu Data Factory wechseln und die Pipeline erstellen, mit der die Daten aus Ihrem Speicherkonto in Ihr SQL Data Warehouse verschoben werden.

## Verwenden von Azure Data Factory

Nachdem wir alle Teile eingerichtet haben, können wir mit dem Einrichten der Pipeline beginnen, indem wir im Azure-Portal zu Ihrer Azure Data Factory-Instanz wechseln. Wechseln Sie hierfür zum [klassischen Azure-Portal](portal.azure.com), und wählen Sie im Menü auf der linken Seite Ihre Data Factory aus.

Von hier aus sind drei Schritte zur Einrichtung einer Azure Data Factory-Pipeline erforderlich, um Daten in Ihr Data Warehouse zu übertragen: das Verknüpfen Ihrer Dienste, das Definieren Ihrer Datasets und das Erstellen der Pipeline.

### Erstellen von verknüpften Diensten

Der erste Schritt besteht darin, das Azure-Speicherkonto und SQL Data Warehouse mit Ihrer Data Factory zu verknüpfen.

1. Starten Sie zunächst den Registrierungsvorgang, indem Sie auf den Abschnitt „Verknüpfte Dienste“ Ihrer Data Factory und dann auf „Neuer Datenspeicher“ klicken. Wählen Sie dann einen Namen aus, unter dem Sie Ihren Azure-Speicher registrieren möchten, wählen Sie Azure Storage als Typ aus, und geben Sie dann Ihren Kontonamen und Kontoschlüssel ein.

2. Zum Registrieren von SQL Data Warehouse müssen Sie zum Abschnitt „Erstellen und Bereitstellen“ wechseln und „Neuer Datenspeicher“ und dann „SQL Azure Data Warehouse“ wählen. Füllen Sie dann die unten aufgeführte Vorlage aus:

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

### Registrieren von Datasets

Nach dem Erstellen der verknüpften Dienste müssen Sie die Datasets definieren. Dies bedeutet in diesem Fall, dass die Struktur der Daten definiert wird, die aus dem Speicher in das Data Warehouse verschoben werden. Informationen zur Erstellung

1. Um diesen Prozess zu starten, wechseln Sie zum Abschnitt „Erstellen und Bereitstellen“ Ihrer Data Factory.

2. Klicken Sie auf „Neues Dataset“ und dann auf „Azure-Blobspeicher“, um Ihren Speicher mit Ihrer Data Factory zu verknüpfen. Mit dem unten aufgeführten Skript können Sie Daten im Azure-Blobspeicher definieren:

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



3. Jetzt definieren wir auch unser Dataset für SQL Data Warehouse. Wir beginnen auf die gleiche Weise, indem wir auf „Neues Dataset“ und dann auf „Azure SQL Data Warehouse“ klicken.

		{
		  "name": "<dataset name>",
		  "properties": {
		    "type": "AzureSqlDWTable",
		    "linkedServiceName": "<linked data warehouse name>",
		    "typeProperties": {
		      "tableName": "FactInternetSales"
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

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

### Einrichten Ihrer Pipeline

Zuletzt richten wir die Pipeline in Azure Data Factory ein und führen sie aus. Dies ist der Vorgang, durch den die eigentliche Datenverschiebung abgeschlossen wird. [Hier](../data-factory/data-factory-azure-sql-data-warehouse-connector/) finden Sie eine vollständige Ansicht der Vorgänge, die Sie mit SQL Data Warehouse und Azure Data Factory durchführen können.

Klicken Sie im Abschnitt „Erstellen und Bereitstellen“ nun auf „Weitere Befehle“ und dann auf „Neue Pipeline“. Nachdem Sie die Pipeline erstellt haben, können Sie die Daten mit dem folgenden Code in das Data Warehouse übertragen:

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
	

<!---HONumber=AcomDC_0114_2016-->