<properties
	pageTitle="Anpassen von HDInsight-Clustern mithilfe von Bootstrap | Microsoft Azure"
	description="Erfahren Sie, wie Sie mit Bootstrap HDInsight-Cluster anpassen können."
	services="hdinsight"
	documentationCenter=""
	authors="mumian"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="jgao"/>

# Anpassen von HDInsight-Clustern mithilfe von Bootstrap

Mitunter möchten Sie die Konfigurationsdateien bearbeiten, die im Folgenden aufgeführt sind:

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

Die Cluster können die Änderungen aufgrund des Re-Imagings nicht beibehalten. Weitere Informationen zum Re-Imaging finden Sie unter [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx). Um die Änderungen für die Laufzeit des Clusters beizubehalten, können Sie während des Erstellungsprozesses die HDInsight-Clusteranpassung nutzen. Dies ist die empfohlene Methode zum Ändern der Konfiguration eines Clusters, die über die Neustartereignisse des Azure-Reimaging hinweg beibehalten werden. Diese Konfigurationsänderungen werden vor dem Starten des Diensts angewendet, sodass Dienste nicht neu gestartet werden müssen.

Es gibt drei Methoden zum Verwenden von Bootstrap:

- Verwenden von Azure PowerShell
- Verwenden von .NET SDK
- Verwenden von ARM-Vorlagen

Informationen zum Installieren zusätzlicher Komponenten in HDInsight-Clustern während ihrer Erstellung finden Sie unter:

- [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen (Linux)](hdinsight-hadoop-customize-cluster-linux.md)
- [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen | Azure](hdinsight-hadoop-customize-cluster.md)

## Verwenden von Azure PowerShell

Der folgende PowerShell-Code passt eine Hive-Konfiguration an:

	# hive-site.xml configuration
	$hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
	
	$config = New-AzureRmHDInsightClusterConfig `
		| Set-AzureRmHDInsightDefaultStorage `
			-StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
			-StorageAccountKey $defaultStorageAccountKey `
		| Add-AzureRmHDInsightConfigValues `
			-HiveSite $hiveConfigValues 
	
	New-AzureRmHDInsightCluster `
		-ResourceGroupName $existingResourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-ClusterSizeInNodes $clusterSizeInNodes `
		-ClusterType Hadoop `
		-OSType Windows `
		-Version "3.2" `
		-HttpCredential $httpCredential `
		-Config $config 

Ein vollständiges funktionierendes PowerShell-Skript finden Sie in [Anhang A](#hdinsight-hadoop-customize-cluster-bootstrap.md/appx-a:-powershell-sample).

**So überprüfen Sie die Änderung:**

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie im linken Bereich auf **Durchsuchen**, und klicken Sie dann auf **HDInsight-Cluster**.
3. Klicken Sie auf den Cluster, den Sie gerade mit dem PowerShell-Skript erstellt haben.
4. Klicken Sie oben im Blatt auf **Dashboard**, um die Ambari-Benutzeroberfläche zu öffnen.
5. Klicken Sie im linken Menü auf **Hive**.
6. Klicken Sie unter **Summary** auf **HiveServer2**.
7. Klicken Sie auf die Registerkarte **Configs**.
8. Klicken Sie im linken Menü auf **Hive**.
9. Klicken Sie auf die Registerkarte **Advanced**.
10. Scrollen Sie nach unten, und erweitern Sie dann **Advanced hive-site**.
11. Suchen Sie im Abschnitt nach **hive.metastore.client.socket.timeout**.

Weitere Beispiele zum Anpassen anderer Konfigurationsdateien:

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

	# mapred-site.xml configuration
	$MapRedConfigValues = @{ "mapreduce.task.timeout"="1200000" } #default 600000

	# oozie-site.xml configuration
	$OozieConfigValues = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

Weitere Informationen finden Sie im Blog von Azim Uddin mit dem Titel [Customizing HDInsight Cluster creation](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx) (Anpassen der Erstellung von HDInsight-Clustern; in englischer Sprache).

## Verwenden von .NET SDK

Informationen hierzu finden Sie unter [Erstellen von Linux-basierten Clustern in HDInsight mit dem .NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet-sdk#use-bootstrap).

## Verwenden von Azure-Ressourcen-Manager-Vorlagen

Sie können Bootstrap in ARM-Vorlagen verwenden:

    "configurations": {
        …
        "hive-site": {
            "hive.metastore.client.connect.retry.delay": "5",
            "hive.execution.engine": "mr",
            "hive.security.authorization.manager": "org.apache.hadoop.hive.ql.security.authorization.DefaultHiveAuthorizationProvider"
        }
    }


![hdinsight hadoop customize cluster bootstrap arm template](./media/hdinsight-hadoop-customize-cluster-bootstrap/hdinsight-customize-cluster-bootstrap-arm.png)



## Weitere Informationen

- Unter [Erstellen von Hadoop-Clustern in HDInsight][hdinsight-provision-cluster] finden Sie Anweisungen zum Erstellen eines HDInsight-Clusters mit anderen benutzerdefinierten Optionen.
- [Entwickeln von Script Action-Skripts für HDInsight][hdinsight-write-script]
- [Installieren und Verwenden von Spark in HDInsight-Clustern][hdinsight-install-spark]
- [Installieren und Verwenden von R in HDInsight-Clustern][hdinsight-install-r]
- [Installieren und Verwenden von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install.md)
- [Installieren und Verwenden von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install.md)

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: powershell-install-configure.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Phasen während der Clustererstellung"

## Anhang A: Das PowerShell-Beispiel

Dieses PowerShell-Skript erstellt einen HDInsight-Cluster und passt eine Hive-Einstellung an:

    ####################################
    # Set these variables
    ####################################
    #region - used for creating Azure service names
    $nameToken = "<ENTER AN ALIAS>" 
    #endregion

    #region - cluster user accounts
    $httpUserName = "admin"  #HDInsight cluster username
    $httpPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"

    $sshUserName = "sshuser" #HDInsight ssh user name
    $sshPassword = "<ENTER A PASSWORD>" #"<Enter a Password>"
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
    #endregion

    # Treat all errors as terminating
    $ErrorActionPreference = "Stop"

    ####################################
    # Connect to Azure
    ####################################
    #region - Connect to Azure subscription
    Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
    try{Get-AzureRmContext}
    catch{Login-AzureRmAccount}
    #endregion

    #region - Create an HDInsight cluster
    ####################################
    # Create dependent components
    ####################################
    Write-Host "Creating a resource group ..." -ForegroundColor Green
    New-AzureRmResourceGroup `
        -Name  $resourceGroupName `
        -Location $location

    Write-Host "Creating the default storage account and default blob container ..."  -ForegroundColor Green
    New-AzureRmStorageAccount `
        -ResourceGroupName $resourceGroupName `
        -Name $defaultStorageAccountName `
        -Location $location `
        -Type Standard_GRS

    $defaultStorageAccountKey = Get-AzureRmStorageAccountKey `
                                    -ResourceGroupName $resourceGroupName `
                                    -Name $defaultStorageAccountName |  %{ $_.Key1 }
    $defaultStorageContext = New-AzureStorageContext `
                                    -StorageAccountName $defaultStorageAccountName `
                                    -StorageAccountKey $defaultStorageAccountKey
    New-AzureStorageContainer `
        -Name $defaultBlobContainerName `
        -Context $defaultStorageContext #use the cluster name as the container name

    ####################################
    # Create a configuration object
    ####################################
    $hiveConfigValues = @{ "hive.metastore.client.socket.timeout"="90" }
        
    $config = New-AzureRmHDInsightClusterConfig `
        | Set-AzureRmHDInsightDefaultStorage `
            -StorageAccountName "$defaultStorageAccountName.blob.core.windows.net" `
            -StorageAccountKey $defaultStorageAccountKey `
        | Add-AzureRmHDInsightConfigValues `
            -HiveSite $hiveConfigValues 

    ####################################
    # Create an HDInsight cluster
    ####################################
    $httpPW = ConvertTo-SecureString -String $httpPassword -AsPlainText -Force
    $httpCredential = New-Object System.Management.Automation.PSCredential($httpUserName,$httpPW)

    $sshPW = ConvertTo-SecureString -String $sshPassword -AsPlainText -Force
    $sshCredential = New-Object System.Management.Automation.PSCredential($sshUserName,$sshPW)

    New-AzureRmHDInsightCluster `
        -ResourceGroupName $resourceGroupName `
        -ClusterName $hdinsightClusterName `
        -Location $location `
        -ClusterSizeInNodes 1 `
        -ClusterType Hadoop `
        -OSType Linux `
        -Version "3.2" `
        -HttpCredential $httpCredential `
        -SshCredential $sshCredential `
        -Config $config

    ####################################
    # Verify the cluster
    ####################################
    Get-AzureRmHDInsightCluster -ClusterName $hdinsightClusterName

    #endregion

<!---HONumber=AcomDC_0309_2016-->