<properties
   pageTitle="Erstellen von bedarfsgesteuerten Linux-basierten Hadoop-Clustern in HDInsight mit Azure Data Factory | Microsoft Azure"
   	description="Erfahren Sie, wie Sie bedarfsgesteuerte HDInsight-Cluster mit Azure Data Factory erstellen."
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
   ms.date="03/16/2016"
   ms.author="jgao"/>

# Erstellen von bedarfsgesteuerten Linux-basierten Hadoop-Clustern in HDInsight mit Azure Data Factory

[AZURE.INCLUDE [Auswahl](../../includes/hdinsight-selector-create-clusters.md)]

[Azure Data Factory](../data-factory/data-factory-introduction.md) ist ein cloudbasierter Datenintegrationsdienst, der das Verschieben und Transformieren von Daten orchestriert und automatisiert. In diesem Artikel erfahren Sie, wie Sie Azure Data Factory zum Erstellen eines [bedarfsgesteuerten verknüpften Azure HDInsight-Diensts](../data-factory/data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) verwenden und wie Sie den Cluster zum Ausführen eines Hive-Auftrags verwenden. Der allgemeine Ablauf sieht wie folgt aus:

1. Erstellen Sie bedarfsgesteuert einen HDInsight-Cluster.
2. Führen Sie einen Hive-Auftrag zum Lesen der Webprotokollrohdaten eines Quell-Blobspeicherkontos aus, transformieren Sie die Daten, und schreiben Sie die Ausgabe in ein Ziel-Blobspeicherkonto. 
3. Löschen Sie den Cluster auf Grundlage der Einstellung für die Gültigkeitsdauer.

Die Hive-Aktivität, die in der Data Factory-Pipeline definiert ist, ruft ein vordefiniertes HiveQL-Skript auf. Das Skript erstellt eine externe Tabelle, die auf die Webprotokollrohdaten im Azure-Blobspeicher verweist, und partitioniert die Rohdaten dann nach Jahr und Monat.

Hier sind die Beispielzeilen für jeden Monat in der Eingabedatei.

    2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871 
    2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
    2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871

Das Skript erstellt auf Grundlage der vorherigen Eingabe drei Ausgabeordner. Jeder Ordner enthält eine Datei mit den Einträgen aus jedem Monat.

    adfgetstarted/partitioneddata/year=2014/month=1/000000_0
    adfgetstarted/partitioneddata/year=2014/month=2/000000_0
    adfgetstarted/partitioneddata/year=2014/month=3/000000_0

Eine Liste der Data Factory-Datentransformationsaktivitäten zusätzlich zur Hive-Aktivität finden Sie unter [Transformation und Analyse mit Azure Data Factory](../data-factory/data-factory-data-transformation-activities.md).

Es gibt viele Vorteile bei der Verwendung von HDInsight mit Data Factory:

- HDInsight-Cluster werden stündlich abgerechnet, unabhängig davon, ob Sie sie verwenden oder nicht. Mit Data Factory werden die Cluster bei Bedarf erstellt. Zudem werden die Cluster automatisch gelöscht, wenn die Aufträge abgeschlossen sind. Sie bezahlen also nur für die Ausführungszeit von Aufträgen und eine kurze Leerlaufzeit (Gültigkeitsdauer).
- Sie können einen Workflow mit einer Data Factory-Pipeline erstellen.
- Sie können rekursive Aufträge planen.  

##Voraussetzungen:

Bevor Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

- [Azure-Abonnement](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- [Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md) oder [Azure PowerShell](hdinsight-administer-use-powershell.md#install-azure-powershell-10-and-greater). 

##Vorbereiten des Speicherkontos

Sie können in diesem Szenario bis zu drei Speicherkonten verwenden:

- Standardspeicherkonto für den HDInsight-Cluster
- Speicherkonto für die Eingabedaten
- Speicherkonto für die Ausgabedaten

Zur Vereinfachung des Tutorials verwenden Sie ein Speicherkonto für die drei Aufgaben. Die Beispielskripts für die Azure-Befehlszeilenschnittstelle und Azure PowerShell aus diesem Abschnitt führen die folgenden Schritte aus:

1. Anmelden bei Azure.
2. Erstellen Sie eine Azure-Ressourcengruppe.
3. Erstellen eines Azure-Speicherkontos.
4. Erstellen eines Blobcontainers im Speicherkonto.
5. Kopieren der folgenden beiden Dateien in den Blobcontainer:

    - Eingabedatendatei: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
    - HiveQL-Skript: [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

    Beide Dateien werden in einem öffentlichen Blobcontainer gespeichert.

>[AZURE.IMPORTANT] Notieren Sie sich den Ressourcengruppennamen, den Speicherkontonamen und den Speicherkontoschlüssel, die im Skript verwendet werden. Sie benötigen diese Angaben im nächsten Abschnitt.

**So bereiten Sie den Speicher vor und kopieren die Dateien mithilfe der Azure-Befehlszeilenschnittstelle**

    azure login

    azure group create --name "<Azure Resource Group Name>" --location "East US 2"

    azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

    azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
    azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 
    azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted" 

Der Containername lautet *adfgetstarted*. Ändern Sie ihn nicht. Andernfalls müssen Sie die ARM-Vorlage aktualisieren.

Wenn Sie Hilfe mit diesem Skript der Befehlszeilenschnittstelle benötigen, lesen Sie [Verwenden der Azure-Befehlszeilenschnittstelle mit Azure Storage](../storage/storage-azure-cli.md).

**So bereiten Sie den Speicher vor und kopieren die Dateien mithilfe von Azure PowerShell**

    $resourceGroupName = "<Azure Resource Group Name>"
    $storageAccountName = "<Azure Storage Account Name>"
    $location = "East US 2"

    $sourceStorageAccountName = "hditutorialdata"  
    $sourceContainerName = "adfhiveactivity"

    $destStorageAccountName = $storageAccountName
    $destContainerName = "adfgetstarted" # don't change this value.

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    ####################################
    # Create a resource group, storage, and container
    ####################################

    #region - create Azure resources
    Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

    New-AzureRmResourceGroup -Name $resourceGroupName -Location $location 
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $destStorageAccountName `
        -type Standard_LRS `
        -Location $location 

    $destStorageAccountKey = Get-AzureRmStorageAccountKey `
        -ResourceGroupName $resourceGroupName `
        -Name $destStorageAccountName |  %{ $_.Key1 }

    $sourceContext = New-AzureStorageContext `
        -StorageAccountName $sourceStorageAccountName `
        -Anonymous
    $destContext = New-AzureStorageContext `
        -StorageAccountName $destStorageAccountName `
        -StorageAccountKey $destStorageAccountKey

    New-AzureStorageContainer -Name $destContainerName -Context $destContext
    #endregion

    ####################################
    # Copy files
    ####################################
    #region - copy files
    Write-Host "`nCopying files ..." -ForegroundColor Green

    $blobs = Get-AzureStorageBlob `
        -Context $sourceContext `
        -Container $sourceContainerName 

    $blobs|Start-AzureStorageBlobCopy `
        -DestContext $destContext `
        -DestContainer $destContainerName

    Write-Host "`nCopied files ..." -ForegroundColor Green
    Get-AzureStorageBlob -Context $destContext -Container $destContainerName 
    #endregion

    Write-host "`nYou will use the following values:" -ForegroundColor Green
    write-host "`nResource group name: $resourceGroupName"
    Write-host "Storage Account Name: $destStorageAccountName"
    write-host "Storage Account Key: $destStorageAccountKey"

    Write-host "`nScript completed" -ForegroundColor Green

Wenn Sie Hilfe mit diesem PowerShell-Skript benötigen, lesen Sie [Verwenden von Azure PowerShell mit Azure Storage](../storage/storage-powershell-guide-full.md).

**So untersuchen Sie das Speicherkonto und den Inhalt**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie im linken Bereich auf **Ressourcengruppen**.
3. Doppelklicken Sie auf den Namen der Ressourcengruppe, die Sie im Skript der Befehlszeilenschnittstelle oder im PowerShell-Skript erstellt haben. Verwenden Sie den Filter, wenn zu viele Ressourcengruppen aufgeführt werden. 
4. Auf der Kachel **Ressourcen** sollte eine Ressource aufgeführt sein, wenn Sie die Ressourcengruppe nicht für andere Projekte freigegeben haben. Das ist das Speicherkonto mit dem Namen, den Sie zuvor angegeben haben. Klicken Sie auf den Namen des Speicherkontos.
5. Klicken Sie auf die Kachel **Blobs**.
6. Klicken Sie auf den Container **adfgetstarted**. Es werden zwei Ordner angezeigt: **input data** und **script**.
7. Öffnen Sie die Ordner, und überprüfen Sie die Dateien in den Ordnern.
 
## Erstellen einer Data Factory

Wenn das Speicherkonto, die Eingabedaten und das HiveQL-Skript vorbereitet sind, können Sie eine Azure Data Factory erstellen. Es gibt mehrere Methoden zum Erstellen einer Data Factory. In diesem Tutorial verwenden Sie das Azure-Portal zum Aufrufen einer benutzerdefinierten ARM-Vorlage. Sie können die ARM-Vorlage auch über die [Azure-Befehlszeilenschnittstelle](../resource-group-template-deploy.md#deploy-with-azure-cli-for-mac-linux-and-windows) und [Azure PowerShell](../resource-group-template-deploy.md#deploy-with-powershell) aufrufen. Andere Methoden für die Erstellung einer Data Factory finden Sie unter [Tutorial: Erstellen Ihrer ersten Data Factory](../data-factory/data-factory-build-your-first-pipeline.md).

Die ARM-Vorlage der obersten Ebene enthält:

    {
        "contentVersion": "1.0.0.0",
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "parameters": { ...
        },
        "variables": { ...
        },
        "resources": [
            {
                "name": "[parameters('dataFactoryName')]",
                "apiVersion": "[variables('apiVersion')]",
                "type": "Microsoft.DataFactory/datafactories",
                "location": "westus",
                "resources": [
                    { ... },
                    { ... },
                    { ... },
                    { ... }
                ]
            }
        ]
    }

Sie enthält eine Data Factory-Ressource mit dem Namen *hdinsight-hive-on-demand*. (Der Name ist im Screenshot nicht dargestellt.) Data Factory wird derzeit nur in den Regionen „USA, Westen“ und „Europa, Norden“ unterstützt.

Die Ressource *hdinsight-hive-on-demand* enthält vier Ressourcen:

- Einen verknüpften Dienst für das Speicherkonto, das als Standardspeicherkonto, Eingabedatenspeicher und Ausgabedatenspeicher für HDInsight verwendet wird.
- Einen verknüpften Dienst für den zu erstellenden HDInsight-Cluster:

        {
            "dependsOn": [ ... ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]"
                }
            }
        },

    Auch wenn es nicht angegeben ist, wird der Cluster in der gleichen Region wie das Speicherkonto erstellt.
    
    Beachten Sie die Einstellung *timeToLive*. Die Data Factory löscht den Cluster automatisch, nachdem der Cluster für 30 Minuten im Leerlauf war.
- Ein Dataset für die Eingabedaten. Der Dateiname und der Ordnername werden hier definiert:

        "fileName": "input.log",
        "folderPath": "adfgetstarted/inputdata",
        
- Ein Dataset für die Ausgabedaten und dann die Pipeline für die Datenverarbeitung. Der Ausgabepfad wird hier definiert:
        
        "folderPath": "adfgetstarted/partitioneddata",

    Die Einstellung für die [Datasetverfügbarkeit](../data-factory/data-factory-create-datasets.md#Availability) lautet wie folgt:
    
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        },

    In Azure Data Factory steuert die Verfügbarkeit des Ausgabedatasets die Pipeline. Dies bedeutet, dass der Slice monatlich am letzten Tag des Monats erzeugt wird. Weitere Informationen finden Sie unter [Planung und Ausführung mit Data Factory](../data-factory/data-factory-scheduling-and-execution.md).

    Die Definition der Pipeline lautet wie folgt:
    
        {
            "dependsOn": [ ... ],
            "type": "datapipelines",
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
                "activities": [
                    { ...}
                ],
                "start": "2016-01-01T00:00:00Z",
                "end": "2016-01-31T00:00:00Z",
                "isPaused": false
            }
        }
                
    Sie enthält eine Aktivität. *start* und *end* der Aktivität haben ein Datum in der Vergangenheit. Dies bedeutet, dass es nur einen Slice gibt. Wenn „end“ ein Datum in der Zukunft ist, erstellt die Data Factory einen weiteren Slice, sobald der Zeitpunkt erreicht ist. Weitere Informationen finden Sie unter [Planung und Ausführung mit Data Factory](../data-factory/data-factory-scheduling-and-execution.md).

    Im Folgenden finden die Definition der Aktivität:
    
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
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
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
    
    Die Eingaben, Ausgaben und der Skriptpfad werden definiert.
    
So erstellen Sie eine **Data Factory**

1. Klicken Sie auf die folgende Abbildung, um sich bei Azure anzumelden und die ARM-Vorlage im Azure-Portal zu öffnen. Die Vorlage befindet sich unter https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json. 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/de-DE/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>

2. Geben Sie **DATAFACTORYNAME**, **STORAGEACCOUNTNAME** und **STORAGEACCOUNTKEY** für das Konto ein, das Sie im vorherigen Abschnitt erstellt haben, und klicken Sie dann auf **OK**. Der Data Factory-Name muss global eindeutig sein.
3. Wählen Sie unter **Ressourcengruppe** dieselbe Ressourcengruppe aus, die Sie im vorherigen Abschnitt verwendet haben.
4. Klicken Sie auf **Rechtliche Bedingungen** und dann auf **Erstellen**.
5. Klicken Sie auf **Erstellen**. Eine Kachel mit dem Namen **Vorlagenbereitstellung bereitstellen** wird auf dem Dashboard angezeigt. Warten Sie, bis sich der Kacheltext in den Namen der Ressourcengruppe ändert. Die Erstellung eines HDInsight-Clusters dauert etwa 20 Minuten.
6. Klicken Sie auf die Kachel, um die Ressourcengruppe zu öffnen. Jetzt sollte neben der Speicherkontoressource eine weitere Data Factory-Ressource aufgeführt werden.
7. Klicken Sie auf **hdinsight-hive-on-demand**.
8. Klicken Sie auf die Kachel **Diagramm**. Das Diagramm zeigt eine Aktivität mit einem Eingabedataset und einem Ausgabedataset:

    ![Azure Data Factory – Diagramm der bedarfsgesteuerten HDInsight Hive-Aktivitätspipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)
    
    Die Namen werden in der ARM-Vorlage definiert.
9. Doppelklicken Sie auf **AzureBlobOutput**.
10. Unter **Zuletzt aktualisierte Slices** sollte ein Slice angezeigt werden. Wenn der Status **In Bearbeitung** ist, warten Sie, bis er in **Bereit** geändert wird.

**So überprüfen Sie die Data Factory-Ausgabe**

1. Verwenden Sie das gleiche Verfahren in der letzten Sitzung, um den Inhalt des Containers „adfgetstarted“ zu überprüfen. Es gibt zwei neue Container zusätzlich zu **adfgetsarted**:

    - adfhdinsight-hive-on-demand-hdinsightondemandlinked-xxxxxxxxxxxxx: Dies ist der Standardcontainer für den HDInsight-Cluster. Namen für Standardcontainer basieren auf dem Muster: „adf>ihrdatafactoryname>-nameverknüpfterdienst-datumuhrzeitstempel“. 
    - adfjobs: Dies ist der Container für die ADF-Auftragsprotokolle.
    
    Die Data Factory-Ausgabe wird in „afgetstarted“ gespeichert, wie in der ARM-Vorlage konfiguriert. 
2. Klicken Sie auf **adfgetstarted**.
3. Doppelklicken Sie auf **partitioneddata**. Ein Ordner **year=2014** wird angezeigt, da alle Webprotokolle ein Datum aus dem Jahr 2014 haben. 

    ![Azure Data Factory – Ausgabe der bedarfsgesteuerten HDInsight Hive-Aktivitätspipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    Wenn Sie in der Liste einen Drilldown ausführen, sollten Sie drei Ordner für Januar, Februar und März sehen. Und es gibt ein Protokoll für jeden Monat.

    ![Azure Data Factory – Ausgabe der bedarfsgesteuerten HDInsight Hive-Aktivitätspipeline](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

##Bereinigen des Tutorials

Beim bedarfsgesteuerten verknüpften HDInsight-Dienst wird jedes Mal ein HDInsight-Cluster erstellt, wenn ein Slice verarbeitet werden muss – es sei denn, ein aktiver Cluster (timeToLive) ist vorhanden und wird gelöscht, nachdem die Verarbeitung abgeschlossen ist. Für jeden Cluster erstellt Azure Data Factory einen Azure-Blobspeicher, der als Standarddateisystem für den Cluster verwendet wird. Auch wenn der HDInsight-Cluster gelöscht wird, werden der standardmäßige Blobspeichercontainer und das zugehörige Speicherkonto nicht gelöscht. Dies ist beabsichtigt. Wenn immer mehr Slices verarbeitet werden, enthält Azure Blob Storage viele Container. Falls Sie diese für die Problembehandlung der Aufträge nicht benötigen, sollten Sie sie ggf. löschen, um die Speicherkosten zu verringern. Der Name dieser Container basiert auf dem folgenden Muster: adfihrdatafactoryname-nameverknüpfterdienst-datumuhrzeitstempel.

[Azure Resource Manager](../resource-group-overview.md) dient zum Bereitstellen, Verwalten und Überwachen Ihrer Lösung als Gruppe. Durch das Löschen einer Ressourcengruppe werden alle Komponenten in der Gruppe gelöscht.

**So löschen Sie die Ressourcengruppe**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie im linken Bereich auf **Ressourcengruppen**.
3. Doppelklicken Sie auf den Namen der Ressourcengruppe, die Sie im Skript der Befehlszeilenschnittstelle oder im PowerShell-Skript erstellt haben. Verwenden Sie den Filter, wenn zu viele Ressourcengruppen aufgeführt werden. Damit wird die Ressourcengruppe auf einem neuen Blatt geöffnet.
4. Auf der Kachel **Ressourcen** sollten das Standardspeicherkonto und die Data Factory aufgeführt sein, wenn Sie die Ressourcengruppe nicht für andere Projekte freigegeben haben.
5. Klicken Sie oben auf dem Blatt auf **Löschen**. Auf diese Weise werden auch das Speicherkonto und die Daten im Speicherkonto gelöscht.
6. Geben Sie den Namen der Ressourcengruppe ein, und klicken Sie dann auf **Löschen**.

Für den Fall, dass Sie das Speicherkonto nicht löschen möchten, wenn Sie die Ressourcengruppe löschen, können Sie den folgenden Architekturentwurf in Betracht ziehen und die Geschäftsdaten vom Standardspeicherkonto trennen. In diesem Fall verfügen Sie über eine Ressourcengruppe für das Speicherkonto mit den Geschäftsdaten und eine andere Ressourcengruppe für das Standardspeicherkonto und die Data Factory. Wenn Sie die zweite Ressourcengruppe löschen, hat dies keinen Einfluss auf das Speicherkonto für Geschäftsdaten. Gehen Sie dazu wie folgt vor:

- Fügen Sie Folgendes der Ressourcengruppe der obersten Ebene zusammen mit der Ressource „Microsoft.DataFactory/datafactories“ in der ARM-Vorlage hinzu. Es wird ein neues Speicherkonto erstellt:

        {
            "name": "[parameters('defaultStorageAccountName')]",
            "type": "Microsoft.Storage/storageAccounts",
            "location": "[parameters('location')]",
            "apiVersion": "[variables('defaultApiVersion')]",
            "dependsOn": [ ],
            "tags": {

            },
            "properties": {
                "accountType": "Standard_LRS"
            }
        },

- Fügen Sie dem neuen Speicherkonto einen neuen verknüpften Dienstpunkt hinzu:

        {
            "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
            "type": "linkedservices",
            "name": "[variables('defaultStorageLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "AzureStorage",
                "typeProperties": {
                    "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
                }
            }
        },
    
- Konfigurieren Sie den bedarfsgesteuerten verknüpften HDInsight-Dienst mit zusätzlichen dependsOn- und additionalLinkedServiceNames-Elementen:

        {
            "dependsOn": [
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
                "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
                
            ],
            "type": "linkedservices",
            "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
            "apiVersion": "[variables('apiVersion')]",
            "properties": {
                "type": "HDInsightOnDemand",
                "typeProperties": {
                    "osType": "linux",
                    "version": "3.2",
                    "clusterSize": 1,
                    "sshUserName": "myuser",                            
                    "sshPassword": "MyPassword!",
                    "timeToLive": "00:30:00",
                    "linkedServiceName": "[variables('storageLinkedServiceName')]",
                    "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
                }
            }
        },            

##Nächste Schritte
In diesem Artikel haben Sie gelernt, wie Sie mit Azure Data Factory einen bedarfsgesteuerten HDInsight-Cluster zum Verarbeiten von Hive-Aufträgen erstellen. Weitere Informationen:

- [Hadoop-Tutorial: Erste Schritte bei der Verwendung von Linux-basiertem Hadoop in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md)
- [Erstellen von Linux-basierten Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
- [HDInsight-Dokumentation](https://azure.microsoft.com/documentation/services/hdinsight/)
- [Data Factory-Dokumentation](https://azure.microsoft.com/documentation/services/data-factory/)

<!---HONumber=AcomDC_0316_2016-->