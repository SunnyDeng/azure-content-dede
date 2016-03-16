<properties
	pageTitle="Erstellen der ersten Data Factory (ARM-Vorlage) | Microsoft Azure"
	description="In diesem Tutorial erstellen Sie eine Azure Data Factory-Beispielpipeline mithilfe einer Azure-Ressourcen-Manager-Vorlage."
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
	ms.date="03/03/2016"
	ms.author="spelluru"/>

# Tutorial: Erstellen der ersten Azure Data Factory mit einer Azure Resource Manager-Vorlage
> [AZURE.SELECTOR]
- [Übersicht über das Tutorial](data-factory-build-your-first-pipeline.md)
- [Verwenden des Data Factory-Editors](data-factory-build-your-first-pipeline-using-editor.md)
- [Mithilfe von PowerShell](data-factory-build-your-first-pipeline-using-powershell.md)
- [Verwenden von Visual Studio](data-factory-build-your-first-pipeline-using-vs.md)
- [Verwenden der Resource Manager-Vorlage](data-factory-build-your-first-pipeline-using-arm.md)


In diesem Artikel erfahren Sie, wie Sie mithilfe einer Azure-Ressourcen-Manager-Vorlage (ARM) Ihre erste Azure Data Factory erstellen.


## Voraussetzungen
Neben den im Thema „Tutorial – Übersicht“ aufgeführten vorausgesetzten Komponenten müssen Sie Folgendes installieren:

- Sie **müssen** den Artikel [Übersicht über das Tutorial](data-factory-build-your-first-pipeline.md) lesen und die erforderlichen Schritte abschließen, bevor Sie fortfahren. 
- **Installieren Sie Azure PowerShell**. Befolgen Sie die Anweisungen im Artikel [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md) zum Installieren der neuesten Version von Azure PowerShell auf Ihrem Computer.
- Dieser Artikel bietet keine grundlegende Übersicht über den Azure Data Factory-Dienst. Eine ausführliche Übersicht über den Dienst finden Sie unter [Einführung in Azure Data Factory](data-factory-introduction.md). 
- Informationen zu Azure-Ressourcen-Manager-Vorlagen (ARM-Vorlagen) finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md). 


## Schritt 1: Erstellen der ARM-Vorlage

Erstellen Sie eine JSON-Datei mit dem Namen **ADFTutorialARM.json** im Ordner **C:\\ADFGetStarted** mit dem folgenden Inhalt:

> [AZURE.IMPORTANT] Ändern Sie die Werte für die Variablen **storageAccountName** und **storageAccountKey**. Ändern Sie auch **dataFactoryName**, denn dieser Name muss eindeutig sein.

Mit der Vorlage können Sie die folgenden Data Factory-Entitäten erstellen.

1. Eine **Data Factory** namens **TutorialDataFactoryARM**. Eine Data Factory kann eine oder mehrere Aktivitäten aufweisen. Eine Pipeline kann eine oder mehrere Aktivitäten aufweisen. Beispielsweise eine Kopieraktivität zum Kopieren von Daten aus einer Quelle in einen Zieldatenspeicher und eine HDInsight-Hive-Aktivität zum Ausführen eines Hive-Skripts zum Transformieren von Eingabedaten in Produktausgabedaten. 
2. Zwei **verknüpfte Dienste**: **StorageLinkedService** und **HDInsightOnDemandLinkedService**. Diese verknüpften Dienste verknüpfen Ihr Azure Storage-Konto und einen bedarfsgesteuerten Azure HDInsight-Cluster mit Ihrer Data Factory. Das Azure Storage-Konto enthält in diesem Beispiel die Ein- und Ausgabedaten für die Pipeline. Der verknüpfte HDInsight-Dienst wird verwendet, um das in der Aktivität der Pipeline in diesem Beispiel angegebene Hive-Skript auszuführen. Sie müssen identifizieren, welche Datenspeicher-/Computedienste in Ihrem Szenario verwendet werden, und diese Dienste mit der Data Factory verknüpfen, indem Sie verknüpfte Dienste erstellen. 
3. Zwei **Datasets** (für Eingabe/Ausgabe): **AzureBlobInput** und **AzureBlobOutput**. Diese Datasets stellen die Eingabe- und Ausgabedaten für die Hive-Verarbeitung dar. Diese Datasets verweisen auf den **StorageLinkedService**, den Sie zuvor in diesem Tutorial erstellt haben. Der verknüpfte Dienst weist auf ein Azure Storage-Konto, und Datasets geben Container, Ordner und Dateiname in dem Speicher an, der Eingabe- und Ausgabedaten enthält.   

Klicken Sie auf die Registerkarte **Verwenden des Data Factory-Editors**, um zu dem Artikel mit näheren Informationen zu JSON-Eigenschaften, die in dieser Vorlage verwendet werden, zu wechseln.

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
	        "blobInputDataset": "AzureBlobInput",
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
	                    "name": "[variables('blobInputDataset')]",
	                    "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "type": "AzureBlob",
						        "linkedServiceName": "StoraegLinkedService",
						        "typeProperties": {
						            "fileName": "input.log",
						            "folderPath": "adfgetstarted/inputdata",
						            "format": {
						                "type": "TextFormat",
						                "columnDelimiter": ","
						            }
						        },
						        "availability": {
						            "frequency": "Month",
						            "interval": 1
						        },
						        "external": true,
						        "policy": {}
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
						        "published": false,
						        "type": "AzureBlob",
						        "linkedServiceName": "StorageLinkedService",
						        "typeProperties": {
						            "folderPath": "adfgetstarted/partitioneddata",
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
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobInputDataset'))]",
	                            "[concat('Microsoft.DataFactory/dataFactories/', variables('dataFactoryName'), '/datasets/', variables('blobOutputDataset'))]"
	                        ],
	                        "type": "datapipelines",
	                        "name": "[variables('dataFactoryName')]",
	                        "apiVersion": "[variables('apiVersion')]",
						    "properties": {
						        "description": "My first Azure Data Factory pipeline",
						        "activities": [
						            {
						                "type": "HDInsightHive",
						                "typeProperties": {
						                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
						                    "scriptLinkedService": "StorageLinkedService",
						                    "defines": {
		                        				"inputtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/inputdata')]",
		                        				"partitionedtable": "[concat('wasb://adfgetstarted@', variables('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
						                    }
						                },
						                "inputs": [
						                    {
						                        "name": "AzureBlobInput"
						                    }
						                ],
						                "outputs": [
						                    {
						                        "name": "AzureBlobOutput"
						                    }
						                ],
						                "policy": {
						                    "concurrency": 1,
						                    "retry": 3
						                },
						                "scheduler": {
						                    "frequency": "Month",
						                    "interval": 1
						                },
						                "name": "RunSampleHiveActivity",
						                "linkedServiceName": "HDInsightOnDemandLinkedService"
						            }
						        ],
						        "start": "2014-02-01T00:00:00Z",
						        "end": "2014-02-02T00:00:00Z",
						        "isPaused": false
						    }
	                    }
	            ]
	        }
	    ]
	}


Beachten Sie Folgendes:

- Die Data Factory erstellt mit dem obigen JSON-Code einen **Windows-basierten** HDInsight-Cluster für Sie. Sie können sich auch für die Erstellung eines **Linux-basierten** HDInsight-Clusters entscheiden. Ausführliche Informationen finden Sie unter [Bedarfsgesteuerter verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service). 
- Anstelle eines bedarfsgesteuerten HDInsight-Clusters können Sie auch **Ihren eigenen HDInsight-Cluster** verwenden. Ausführliche Informationen finden Sie unter [Verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-linked-service).
- Der HDInsight-Cluster erstellt einen **Standardcontainer** im Blobspeicher, den Sie im JSON-Code angegeben haben (**linkedServiceName**). HDInsight löscht diesen Container nicht, wenn der Cluster gelöscht wird. Dies ist beabsichtigt. Beim bedarfsgesteuerten verknüpften HDInsight-Dienst wird jedes Mal ein HDInsight-Cluster erstellt, wenn ein Slice verarbeitet werden muss – es sei denn, ein aktiver Cluster (**timeToLive**) ist vorhanden und wird gelöscht, nachdem die Verarbeitung abgeschlossen ist.

	Wenn immer mehr Slices verarbeitet werden, enthält Azure Blob Storage viele Container. Falls Sie diese für die Problembehandlung der Aufträge nicht benötigen, sollten Sie sie ggf. löschen, um die Speicherkosten zu verringern. Der Name dieser Container basiert auf dem folgenden Muster: adf**ihrdatafactoryname**-**nameverknüpfterdienst**-datumuhrzeitstempel. Verwenden Sie Tools wie [Microsoft Storage-Explorer](http://storageexplorer.com/), um Container in Ihrer Azure Blob Storage-Instanz zu löschen.

Ausführliche Informationen finden Sie unter [Bedarfsgesteuerter verknüpfter HDInsight-Dienst](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service).

> [AZURE.NOTE] Ein weiteres Beispiel einer ARM-Vorlage zum Erstellen einer Azure Data Factory finden Sie auf [GitHub](https://github.com/Azure/azure-quickstart-templates/blob/master/101-data-factory-blob-to-sql/azuredeploy.json).

## Schritt 2: Bereitstellen von Data Factory-Entitäten mit der ARM-Vorlage

1. Starten Sie Azure PowerShell, und führen Sie den folgenden Befehl aus. Lassen Sie Azure PowerShell bis zum Ende dieses Tutorials geöffnet. Wenn Sie PowerShell schließen und erneut öffnen, müssen Sie die Befehle erneut ausführen.
	- Führen Sie **Login-AzureRmAccount** aus, und geben Sie den Benutzernamen und das Kennwort ein, den bzw. das Sie bei der Anmeldung beim Azure-Portal verwendet haben.  
	- Führen Sie **Get-AzureSubscription** aus, um alle Abonnements für dieses Konto anzuzeigen.
	- Führen Sie **Select-AzureSubscription SubscriptionName** aus, um das Abonnement auszuwählen, mit dem Sie arbeiten möchten. Dieses Abonnement sollte dasselbe sein, das Sie im Azure-Portal verwendet haben.
1. Führen Sie den folgenden Befehl aus, um die Data Factory-Entitäten bereitzustellen, die Sie in Schritt 1 mit der ARM-Vorlage erstellt haben. 

		New-AzureRmResourceGroupDeployment -Name MyARMDeployment -ResourceGroupName ADFTutorialResourceGroup -TemplateFile C:\ADFGetStarted\ADFTutorialARM.json

## Überwachen der Pipeline
 
1.	Klicken Sie nach der Anmeldung beim [Azure-Portal](https://portal.azure.com/) auf **Durchsuchen**, und wählen Sie **Data Factorys** aus. ![Durchsuchen -> Data Factorys](./media/data-factory-build-your-first-pipeline-using-arm/BrowseDataFactories.png)
2.	Klicken Sie im Blatt **Data Factorys** auf die von Ihnen erstellte Data Factory (**TutorialFactoryARM**).	
2.	Klicken Sie im Blatt **Data Factory** für Ihre Data Factory auf **Diagramm**. ![Kachel „Diagramm“](./media/data-factory-build-your-first-pipeline-using-arm/DiagramTile.png)
4.	In der **Diagrammansicht** sehen Sie eine Übersicht über die in diesem Tutorial verwendeten Pipelines und Datasets.
	
	![Diagrammansicht](./media/data-factory-build-your-first-pipeline-using-arm/DiagramView.png) 
8. Doppelklicken Sie in der Diagrammansicht auf das Dataset **AzureBlobOutput**. Sie sehen den Slice, der gerade verarbeitet wird.

	![Datensatz](./media/data-factory-build-your-first-pipeline-using-arm/AzureBlobOutput.png)
9. Wenn die Verarbeitung abgeschlossen ist, wird der Slice mit dem Zustand **Bereit** angezeigt. Beachten Sie, dass die bedarfsgesteuerte Erstellung eines HDInsight-Clusters in der Regel einige Zeit dauert (etwa 20 Minuten). 

	![Datensatz](./media/data-factory-build-your-first-pipeline-using-arm/SliceReady.png)	
10. Sobald der Slice den Status **Bereit** hat, überprüfen Sie, ob die Ausgabedaten sich in Ihrem Blobspeicher im Ordner **partitioneddata** im Container **adfgetstarted** befinden.  
 

<!---HONumber=AcomDC_0309_2016-->