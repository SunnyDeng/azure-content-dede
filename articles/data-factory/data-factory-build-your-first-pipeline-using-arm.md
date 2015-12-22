<properties
	pageTitle="Erstellen der ersten Azure Data Factory-Pipeline mit Azure PowerShell"
	description="In diesem Tutorial erstellen Sie eine Azure Data Factory-Beispielpipeline mithilfe von Azure PowerShell."
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
	ms.topic="hero-article"
	ms.date="12/15/2015"
	ms.author="spelluru"/>

# Erstellen der ersten Azure Data Factory-Pipeline mit Azure PowerShell
> [AZURE.SELECTOR]
- [Tutorial Overview](data-factory-build-your-first-pipeline.md)
- [Using Data Factory Editor](data-factory-build-your-first-pipeline-using-editor.md)
- [Using PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Using Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Using Resource Manager Template](data-factory-build-your-first-pipeline-using-arm.md)


In diesem Artikel erfahren Sie, wie Sie mithilfe von Azure-Ressourcen-Manager-Vorlagen Ihre erste Pipeline erstellen. Dieses Tutorial umfasst die folgenden Schritte:

1.	Erstellen der Data Factory
2.	Erstellen der verknüpften Dienste (Datenspeicher, Compute-Instanzen) und Datasets
3.	Erstellen der Pipeline

 

## Voraussetzungen
Neben den im Thema „Tutorial – Übersicht“ aufgeführten vorausgesetzten Komponenten müssen Sie Folgendes installieren:

- **Installieren Sie Azure PowerShell**. Befolgen Sie die Anweisungen im Artikel [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md) zum Installieren der neuesten Version von Azure PowerShell auf Ihrem Computer.
- Dieser Artikel bietet keine grundlegende Übersicht über den Azure Data Factory-Dienst. Eine ausführliche Übersicht über den Dienst finden Sie unter [Einführung in Azure Data Factory](data-factory-introduction.md). 
- Informationen zu Azure-Ressourcen-Manager-Vorlagen (ARM-Vorlagen) finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md). 
 

## Schritt 1: Erstellen der ARM-Vorlage

Erstellen Sie eine JSON-Datei mit dem Namen **ADFTutorialARM.json** im Ordner **C:\\ADFGetStarted** mit dem folgenden Inhalt:

> [AZURE.IMPORTANT]Ändern Sie die Werte für die Variablen **storageAccountName** und **storageAccountKey**. Ändern Sie auch **dataFactoryName**, denn dieser Name muss eindeutig sein.

	{
	    "contentVersion": "1.0.0.0",
	    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	    "parameters": {
	    },
	    "variables": {
	        "dataFactoryName":  "TutorialDataFactoryARM",
	        "storageAccountName":  "<stroage account name>" ,
	        "storageAccountKey":  "<storage account key>",
	        "apiVersion": "2015-10-01",
	        "storageLinkedServiceName": "StorageLinkedService",
	        "hdInsightOnDemandLinkedServiceName": "HDInsightOnDemandLinkedService",
	        "blobOutputDataset": "AzureBlobOutput",
	        "singleQuote": "'"
	    },
	    "resources": [
	        {
	            "name": "[variables('dataFactoryName')]",
	            "apiVersion": "[variables('apiVersion')]",
	            "type": "Microsoft.DataFactory/datafactories",
	            "location": "westus",
	            "resources": [
	                {
	                    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]" ],
	                    "type": "linkedservices",
	                    "name": "[variables('storageLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "AzureStorage",
	                        "typeProperties": {
	                            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',variables('storageAccountName'),';AccountKey=',variables('storageAccountKey'))]"
	                        }
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "linkedservices",
	                    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                    "properties": {
	                        "type": "HDInsightOnDemand",
	                        "typeProperties": {
	                            "version": "3.2",
	                            "clusterSize": 1,
	                            "timeToLive": "00:30:00",
	                            "linkedServiceName": "StorageLinkedService"
	                        }
	                    }
	                },
	                {
	                    "dependsOn": [
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                        "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
	                    ],
	                    "type": "datasets",
	                    "name": "[variables('blobOutputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
	                      "properties": {
	                        "type": "AzureBlob",
	                        "linkedServiceName": "StorageLinkedService",
	                        "typeProperties": {
	                          "folderPath": "data/partitioneddata",
	                          "format": {
	                            "type": "TextFormat",
	                            "columnDelimiter": ","
	                          }
	                        },
	                        "availability": {
	                          "frequency": "Month",
	                          "interval": 1
	                        }
	                      }
	                    },
	                    {
	                        "dependsOn": [
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
	                        ],
	                        "type": "datapipelines",
	                        "name": "[variables('dataFactoryName')]",
	                        "apiVersion": "[variables('apiVersion')]",
	                        "properties": {
	                            "description": "My first Azure Data Factory pipeline using ARM",
	                            "activities": [
	                              {
	                                "type": "HDInsightHive",
	                                "typeProperties": {
	                                  "scriptPath": "script/partitionweblogs.hql",
	                                  "scriptLinkedService": "StorageLinkedService",
	                                  "defines": {
                                        "partitionedtable": "[concat('wasb://data@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"	                                  }
	                                },
	                                "outputs": [
	                                  {
	                                    "name": "[variables('blobOutputDataset')]"
	                                  }
	                                ],
	                                "scheduler": {
	                                    "frequency": "Month",
	                                    "interval": 1
	                                },
	                                "policy": {
	                                  "concurrency": 1,
	                                  "retry": 3
	                                },
	                                "name": "RunSampleHiveActivity",
	                                "linkedServiceName": "HDInsightOnDemandLinkedService"
	                              }
	                            ],
	                            "start": "2014-01-01",
	                            "end": "2014-01-02"
	                        }
	                    }
	            ]
	        }
	    ]
	}


## Schritt 2: Bereitstellen von Data Factory-Entitäten mit der ARM-Vorlage

1. Starten Sie Azure PowerShell, und führen Sie den folgenden Befehl aus. Lassen Sie Azure PowerShell bis zum Ende dieses Tutorials geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie die Befehle erneut ausführen.
	- Führen Sie **Login-AzureRmAccount** aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie bei der Anmeldung beim Azure-Portal verwendet haben.  
	- Führen Sie **Get-AzureSubscription** aus, um alle Abonnements für dieses Konto anzuzeigen.
	- Führen Sie **Select-AzureSubscription SubscriptionName** aus, um das Abonnement auszuwählen, mit dem Sie arbeiten möchten. Dieses Abonnement sollte dasselbe sein, das Sie im Azure-Portal verwendet haben.
1. Führen Sie den folgenden Befehl aus, um die Data Factory-Entitäten bereitzustellen, die Sie in Schritt 1 mit der ARM-Vorlage erstellt haben. 

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## Überwachen der Pipeline
 
1.	Klicken Sie nach dem Anmeldung beim [Azure-Portal](http://portal.azure.com/) auf **Durchsuchen**, und wählen Sie **Data Factorys** aus. ![Durchsuchen -> Data Factorys](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	Klicken Sie im Blatt **Data Factorys** auf die von Ihnen erstellte Data Factory **TutorialFactoryARM**.	
2.	Klicken Sie im Blatt **Data Factory** für Ihre Data Factory auf **Diagramm**. ![Kachel „Diagramm“](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	In der **Diagrammansicht** sehen Sie eine Übersicht über die in diesem Tutorial verwendeten Pipelines und Datasets.
	
	![Diagrammansicht](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. Doppelklicken Sie in der Diagrammansicht auf das Dataset **AzureBlobOutput**. Sie sehen den Slice, der gerade verarbeitet wird.

	![Datensatz](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. Wenn die Verarbeitung abgeschlossen ist, wird der Slice mit dem Zustand **Bereit** angezeigt. Beachten Sie, dass die bedarfsgesteuerte Erstellung eines HDInsight-Clusters in der Regel einige Zeit dauert. 

	![Datensatz](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)	
10. Sobald der Slice den Status **Bereit** hat, überprüfen Sie den Ordner **partitioneddata** im Container **data** in Ihrem BLOB-Speicher auf die Ausgabedaten.  
 

<!---HONumber=AcomDC_1217_2015-->