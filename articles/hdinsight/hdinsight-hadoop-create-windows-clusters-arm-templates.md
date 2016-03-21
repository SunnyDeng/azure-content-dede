<properties
   pageTitle="Erstellen Windows-basierter Hadoop-Cluster in HDInsight mithilfe von ARM-Vorlagen | Microsoft Azure"
   	description="Erfahren Sie, wie Sie Cluster für Azure HDInsight mithilfe von Azure-ARM-Vorlagen erstellen."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/08/2016"
   ms.author="jgao"/>

# Erstellen Windows-basierter Hadoop-Cluster in HDInsight mithilfe von ARM-Vorlagen

[AZURE.INCLUDE [Auswahl](../../includes/hdinsight-selector-create-clusters.md)]

Erfahren Sie mehr über das Erstellen von HDInsight-Clustern mithilfe von Azure-Ressourcen-Manager-Vorlagen (ARM). Weitere Informationen finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](../resource-group-template-deploy.md). Andere Tools und Features zur Clustererstellung finden Sie, indem Sie oben auf dieser Seite auf die Registerkartenauswahl klicken, oder unter [Methoden zur Clustererstellung](hdinsight-provision-clusters.md#cluster-creation-methods).

##Voraussetzungen:

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]


Bevor Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

- [Azure-Abonnement](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Azure PowerShell](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater) und/oder [Azure CLI](../xplat-cli-install.md). 

## ARM-Vorlagen

ARM-Vorlagen können HDInsight-Cluster, ihre abhängigen Ressourcen (z. B. das standardmäßige Speicherkonto) und andere Ressourcen (z. B. Azure SQL-Datenbank zur Verwendung von Apache Sqoop) für die Anwendung ganz einfach in einem einzigen, koordinierten Vorgang erstellt werden. In der Vorlage müssen Sie die Ressourcen definieren, die für die Anwendung erforderlich sind, und Bereitstellungsparameter für die Eingabe von Werten für unterschiedliche Umgebungen angeben. Die Vorlage besteht aus JSON und Ausdrücken, mit denen Sie Werte für die Bereitstellung erstellen können.

Eine ARM-Vorlage zum Erstellen eines HDInsight-Clusters und des abhängigen Azure-Speicherkontos finden Sie in [Anhang A](#appx-a-arm-template). Verwenden Sie einen Text-Editor, um die Vorlage in einer Datei auf Ihrer Arbeitsstation zu speichern. Sie erfahren, wie die Vorlage mit verschiedenen Tools aufgerufen werden kann.

Weitere Informationen zu ARM-Vorlagen finden Sie unter

- [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md)
- [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](../resource-group-template-deploy.md)


## Bereitstellen mit PowerShell

Gehen Sie wie folgt vor, um den HDInsight-Cluster zu erstellen.

**So stellen Sie ein Cluster mithilfe einer ARM-Vorlage bereit**

1. Speichern Sie die JSON-Datei aus [Anhang A](#appx-a-arm-template) auf Ihrer Arbeitsstation.
2. Legen Sie bei Bedarf die Parameter fest.
3. Führen Sie die Vorlage mithilfe des folgenden PowerShell-Skripts aus:

        ####################################
        # Set these variables
        ####################################
        #region - used for creating Azure service names
        $nameToken = "<Enter an Alias>" 
        $templateFile = "C:\HDITutorials-ARM\hdinsight-arm-template.json"
        #endregion

        ####################################
        # Service names and varialbes
        ####################################
        #region - service names
        $namePrefix = $nameToken.ToLower() + (Get-Date -Format "MMdd")

        $resourceGroupName = $namePrefix + "rg"
        $hdinsightClusterName = $namePrefix + "hdi"
        $defaultStorageAccountName = $namePrefix + "store"
        $defaultBlobContainerName = $hdinsightClusterName

        $location = "East US 2"

        $armDeploymentName = $namePrefix
        #endregion

        ####################################
        # Connect to Azure
        ####################################
        #region - Connect to Azure subscription
        Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
        try{Get-AzureRmContext}
        catch{Login-AzureRmAccount}
        #endregion

        # Create a resource group
        New-AzureRmResourceGroup -Name $resourceGroupName -Location $Location

        # Create cluster and the dependent storage accounge
        $parameters = @{clusterName="$hdinsightClusterName";clusterStorageAccountName="$defaultStorageAccountName"}

        New-AzureRmResourceGroupDeployment `
            -Name $armDeploymentName `
            -ResourceGroupName $resourceGroupName `
            -TemplateFile $templateFile `
            -TemplateParameterObject $parameters

        # List cluster
        Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $hdinsightClusterName 

	Mit dem PowerShell-Skript werden nur den Namen des Clusters und des Speicherkontos konfiguriert. Sie können in der ARM-Vorlage andere Werte festlegen.
	
Weitere Informationen finden Sie unter [Bereitstellen mit PowerShell](../resource-group-template-deploy.md#deploy-with-powershell).

## Bereitstellen über die Azure-Befehlszeilenschnittstelle

Im folgende Beispiel werden ein Cluster und dessen abhängiges Speicherkonto und der zugehörige Container durch Aufrufen einer ARM-Vorlage erstellt:

	azure login
	azure config mode arm
    azure group create -n hdi1229rg -l "East US 2"
    azure group deployment create "hdi1229rg" "hdi1229" --template-file "C:\HDITutorials-ARM\hdinsight-arm-template.json" -p "{"clusterName":{"value":"hdi1229win"},"clusterStorageAccountName":{"value":"hdi1229store"},"location":{"value":"East US 2"},"clusterLoginPassword":{"value":"Pass@word1"}}"





## Bereitstellen über die REST-API

Informationen hierzu finden Sie unter [Bereitstellen mit der REST-API](../resource-group-template-deploy.md#deploy-with-the-rest-api).

## Bereitstellen mit Visual Studio 2013

Mit Visual Studio können Sie über die Benutzeroberfläche ein Ressourcengruppenprojekt erstellen und in Azure bereitstellen. Wählen Sie den Typ der Ressourcen aus, die in das Projekt aufgenommen werden sollen, und diese Ressourcen werden der Ressourcen-Manager-Vorlage automatisch hinzugefügt. Das Projekt enthält auch ein PowerShell-Skript zum Bereitstellen der Vorlage.

Eine Einführung in die Verwendung von Visual Studio mit Ressourcengruppen finden Sie unter [Erstellen und Bereitstellen von Azure-Ressourcengruppen über Visual Studio](../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

##Nächste Schritte
In diesem Artikel haben Sie mehrere Möglichkeiten zum Erstellen von HDInsight-Clustern kennengelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

- Ein Beispiel für die Bereitstellung von Ressourcen über die .NET-Clientbibliothek finden Sie unter [Bereitstellen von Ressourcen mithilfe von .NET-Bibliotheken und einer Vorlage](../virtual-machines/arm-template-deployment.md).
- Ein ausführliches Beispiel für die Bereitstellung einer Anwendung finden Sie unter [Vorhersagbares Bereitstellen von Microservices in Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
- Informationen zum Bereitstellen der Lösung in andere Umgebungen finden Sie unter [Entwicklungs- und Testumgebungen in Microsoft Azure](../solution-dev-test-environments.md).
- Informationen zu den Abschnitten der Azure Resource Manager-Vorlage finden Sie unter [Erstellen von Vorlagen](../resource-group-authoring-templates.md).
- Unter [Vorlagenfunktionen](../resource-group-template-functions.md) finden Sie eine Liste der Funktionen, die Sie in einer Azure Resource Manager-Vorlage verwenden können.


##Anhang A: ARM-Vorlage

Die folgende Azure-Ressourcen-Manager-Vorlage erstellt einen Windows-basierten Hadoop-Cluster mit abhängigem Azure-Speicherkonto.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
    "parameters": {
        "location": {
        "type": "string",
        "defaultValue": "East US 2",
        "allowedValues": [
            "Central US",
            "North Europe",
            "East US",
            "East US 2",
            "North Central US",
            "South Central US",
            "West US",
            "North Europe",
            "West Europe",
            "East Asia",
            "Southeast Asia",
            "Japan East",
            "Japan West",
            "Brizil South",
            "Australia East",
            "Australia Southeast",
            "Central India"
        ],
        "metadata": {
            "description": "The location where all azure resources will be deployed."
        }
        },
        "clusterName": {
        "type": "string",
        "metadata": {
            "description": "The name of the HDInsight cluster to create."
        }
        },
        "clusterLoginUserName": {
        "type": "string",
        "defaultValue": "admin",
        "metadata": {
            "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
        }
        },
        "clusterLoginPassword": {
        "type": "securestring",
        "metadata": {
            "description": "The password for the cluster login."
        }
        },
        "clusterStorageAccountName": {
        "type": "string",
        "metadata": {
            "description": "The name of the storage account to be created and be used as the cluster's storage."
        }
        },
        "clusterStorageType": {
        "type": "string",
        "defaultValue": "Standard_LRS",
        "allowedValues": [
            "Standard_LRS",
            "Standard_GRS",
            "Standard_ZRS"
        ]
        },
        "clusterWorkerNodeCount": {
        "type": "int",
        "defaultValue": 4,
        "metadata": {
            "description": "The number of nodes in the HDInsight cluster."
        }
        }
    },
        "variables": {},
        "resources": [
            {
            "name": "[parameters('clusterStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "2015-05-01-preview",
            "dependsOn": [],
            "tags": {},
            "properties": {
                "accountType": "[parameters('clusterStorageType')]"
            }
            },
            {
            "name": "[parameters('clusterName')]",
            "type": "Microsoft.HDInsight/clusters",
            "location": "[parameters('location')]",
            "apiVersion": "2015-03-01-preview",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
            ],
            "tags": {},
            "properties": {
                "clusterVersion": "3.2",
                "osType": "Windows",
                "clusterDefinition": {
                "kind": "hadoop",
                "configurations": {
                    "gateway": {
                    "restAuthCredential.isEnabled": true,
                    "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
                    "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
                    }
                }
                },
                "storageProfile": {
                "storageaccounts": [
                    {
                    "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
                    "isDefault": true,
                    "container": "[parameters('clusterName')]",
                    "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
                    }
                ]
                },
                "computeProfile": {
                "roles": [
                    {
                    "name": "headnode",
                    "targetInstanceCount": "1",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    },
                    {
                    "name": "workernode",
                    "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
                    "hardwareProfile": {
                        "vmSize": "Large"
                    }
                    }
                ]
                }
            }
            }
        ],
        "outputs": {
            "cluster": {
            "type": "object",
            "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
            }
        }
    }

<!---HONumber=AcomDC_0309_2016-->