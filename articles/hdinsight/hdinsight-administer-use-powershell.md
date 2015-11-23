<properties
	pageTitle="Verwalten von Hadoop-Clustern in HDInsight mit Azure PowerShell | Microsoft Azure"
	description="Hier erfahren Sie, wie Sie administrative Aufgaben für Hadoop-Cluster in HDInsight mit Azure PowerShell ausführen."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	tags="azure-portal"
	authors="mumian"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/04/2015"
	ms.author="jgao"/>

# Verwalten von Hadoop-Clustern in HDInsight mit Azure PowerShell

[AZURE.INCLUDE [Auswahl](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Workloads in Azure steuern und automatisieren können. In diesem Artikel erfahren Sie, wie Sie Hadoop-Cluster in Azure HDInsight mit einer lokalen Azure PowerShell-Konsole über Windows PowerShell verwalten. Eine Liste der HDInsight PowerShell-Cmdlets finden Sie unter [HDInsight-Cmdlet-Referenz][hdinsight-powershell-reference].



**Voraussetzungen**

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Install and use Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/) (Installieren und Verwenden von Azure PowerShell, in englischer Sprache).

	> [AZURE.NOTE]Die in diesem Artikel bereitgestellten PowerShell-Skripts verwenden den Azure-Ressourcen-Manager-Modus. Laden Sie mit dem Microsoft-Webplattform-Installer die neueste Azure PowerShell-Version herunter, um sicherzustellen, dass die Beispiele funktionieren.

##Erstellen von Clustern

Ein HDInsight-Cluster erfordert eine Azure-Ressourcengruppe und einen Blobcontainer in einem Azure-Speicherkonto:

- Eine Azure-Ressourcengruppe ist ein logischer Container für Azure-Ressourcen. Die Azure-Ressourcengruppe und der HDInsight-Cluster müssen sich nicht am gleichen Speicherort befinden. Weitere Informationen finden Sie unter [Verwenden von Azure PowerShell mit dem Azure-Ressourcen-Manager](powershell-azure-resource-manager.md).
- HDInsight verwendet einen Blobcontainer eines Azure-Speicherkontos als Standarddateisystem. Sie benötigen ein Azure-Speicherkonto und einen Speichercontainer, bevor Sie ein HDInsight-Cluster erstellen können. Das Standardspeicherkonto und der HDInsight-Cluster müssen sich am gleichen Speicherort befinden.

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**So stellen Sie eine Verbindung mit Azure her**

	Login-AzureRmAccount
	Get-AzureRmSubscription  # list your subscriptions and get your subscription ID
	Select-AzureRmSubscription -SubscriptionId "<Your Azure Subscription ID>"

**Select-AzureRMSubscription** wird aufgerufen, wenn Sie über mehrere Azure-Abonnements verfügen.
	
**So erstellen Sie eine neue Ressourcengruppe**

	New-AzureRmResourceGroup -name <New Azure Resource Group Name> -Location "<Azure Location>"  # For example, "EAST US 2"

**So erstellen Sie ein Azure-Speicherkonto**

	New-AzureRmStorageAccount -ResourceGroupName <Azure Resource Group Name> -Name <Azure Storage Account Name> -Location "<Azure Location>" -Type <AccountType> # account type example: Standard_LRS for zero redundancy storage
	
	Don't use **Standard_ZRS** because it deson't support Azure Table.  HDInsight uses Azure Table to logging. For a full list of the storage account types, see [https://msdn.microsoft.com/library/azure/hh264518.aspx](https://msdn.microsoft.com/library/azure/hh264518.aspx).

[AZURE.INCLUDE [Datencenter-Liste](../../includes/hdinsight-pricing-data-centers-clusters.md)]


Informationen zum Erstellen eines Azure-Speicherkontos im Azure-Vorschauportal finden Sie unter [Informationen zu Azure-Speicherkonten](storage-create-storage-account.md).

Falls Sie bereits ein Speicherkonto haben, jedoch den Kontonamen und den Kontoschlüssel nicht kennen, können Sie diese Informationen mit den folgenden Befehlen abrufen:

	# List Storage accounts for the current subscription
	Get-AzureRmStorageAccount
	# List the keys for a Storage account
	Get-AzureRmStorageAccountKey -ResourceGroupName <Azure Resource Group Name> -name $storageAccountName <Azure Storage Account Name>

Ausführliche Informationen zum Abrufen dieser Informationen über das Vorschauportal finden Sie unter [Informationen zu Azure-Speicherkonten](storage-create-storage-account.md) im Abschnitt „Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln“.

**So erstellen Sie einen Azure-Speichercontainer**

Mit Azure PowerShell kann während der Erstellung des HDInsight-Clusters kein Blobcontainer erstellt werden. Sie können einen Container mit dem folgenden Skript erstellen:

	$resourceGroupName = "<AzureResoureGroupName>"
	$storageAccountName = "<Azure Storage Account Name>"
	$storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
	$containerName="<AzureBlobContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**So erstellen Sie einen Cluster**

Sobald Sie Ihr Speicherkonto und Ihren Blobcontainer vorbereitet haben, können Sie einen Cluster erstellen.

	$resourceGroupName = "<AzureResoureGroupName>"

	$storageAccountName = "<Azure Storage Account Name>"
	$containerName = "<AzureBlobContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<AzureDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the Storage account key
	$storageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }

	# Create a new HDInsight cluster
	New-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		-DefaultStorageAccountKey $storageAccountKey `
		-DefaultStorageContainer $containerName  `
		-ClusterSizeInNodes $clusterNodes

##Auflisten von Clustern
Verwenden Sie den folgenden Befehl, um alle Cluster des aktuellen Abonnements aufzulisten:

	Get-AzureRmHDInsightCluster

##Anzeigen von Clustern

Verwenden Sie den folgenden Befehl, um Details zu einem bestimmten Cluster des aktuellen Abonnements anzuzeigen:

	Get-AzureRmHDInsightCluster -ClusterName <Cluster Name>

##Löschen von Clustern
Mit dem folgenden Befehl können Sie ein Cluster löschen:

	Remove-AzureRmHDInsightCluster -ClusterName <Cluster Name>

##Skalieren von Clustern
Mithilfe der Clusterskalierung können Sie die Anzahl der von einem in Azure HDInsight ausgeführten Cluster verwendeten Workerknoten ändern, ohne den Cluster neu erstellen zu müssen.

>[AZURE.NOTE]Es werden nur Cluster mit HDInsight-Versionen ab 3.1.3 unterstützt. Überprüfen Sie ggf. auf der Seite „Eigenschaften“ die Version Ihres Clusters. Siehe [Einführung in die Portaloberfläche für Cluster](hdinsight-adminster-use-management-portal/#Get-familiar-with-the-cluster-portal-interface).

Auswirkungen der Änderung der Anzahl von Datenknoten für die von HDInsight unterstützten Clustertypen:

- Hadoop

	Sie können die Anzahl der Workerknoten in einem aktiven Hadoop-Cluster problemlos ohne Auswirkungen auf ausstehende oder aktive Aufträge erhöhen. Neue Aufträge können auch während des Vorgangs gesendet werden. Fehler bei einer Skalierung werden ordnungsgemäß behandelt, sodass der Cluster immer in einem funktionsfähigen Zustand verbleibt.

	Wenn ein Hadoop-Cluster durch Verringern der Anzahl der Datenknoten zentral herunterskaliert wird, werden einige der Dienste im Cluster neu gestartet. Dies führt beim Abschluss des Skalierungsvorgangs bei allen aktiven und ausstehenden Aufträgen zu einem Fehler. Sie können die Aufträge jedoch nach Abschluss des Vorgangs erneut senden.

- HBase

	Sie können Knoten reibungslos Ihrem HBase-Cluster hinzufügen oder aus diesem entfernen, während er aktiv ist. Regionale Server werden innerhalb weniger Minuten nach Abschluss des Skalierungsvorgangs automatisch ausgeglichen. Allerdings können Sie die regionalen Server auch manuell ausgleichen, indem Sie sich am Hauptknoten des Clusters anmelden und in einem Eingabeaufforderungsfenster die folgenden Befehle ausführen:

		>pushd %HBASE_HOME%\bin
		>hbase shell
		>balancer

	Weitere Informationen zur Verwendung der HBase-Shell finden Sie unter
- Storm

	Sie können Datenknoten übergangslos zum Storm-Cluster hinzufügen oder aus diesem entfernen, während er aktiv ist. Nach erfolgreichen Abschluss des Skalierungsvorgangs müssen Sie die Topologie neu ausgleichen.

	Es stehen zwei Methoden für den erneuten Ausgleich zur Verfügung:

	* Storm-Webbenutzeroberfläche
	* Befehlszeilenschnittstelle (CLI)

	Weitere Informationen finden Sie in der Dokumentation zu [Apache Storm](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

	Die Storm-Webbenutzeroberfläche ist für den HDInsight-Cluster verfügbar:

	![HDInsight Storm-Skalierung ausgleichen](./media/hdinsight-administer-use-management-portal/hdinsight.portal.scale.cluster.storm.rebalance.png)

	Es folgt ein Beispiel, wie die Storm-Topologie mithilfe des CLI-Befehls neu ausgeglichen werden kann:

		## Reconfigure the topology "mytopology" to use 5 worker processes,
		## the spout "blue-spout" to use 3 executors, and
		## the bolt "yellow-bolt" to use 10 executors

		$ storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10

Führen Sie den folgenden Befehl auf einem Clientcomputer aus, um die Hadoop-Clustergröße mithilfe von Azure PowerShell zu ändern:

	Set-AzureRmHDInsightClusterSize -ClusterName <Cluster Name> -TargetInstanceCount <NewSize>
	

##Gewähren/Entziehen des Zugriffs

In HDInsight-Clustern stehen die folgenden HTTP-Webdienste zur Verfügung (alle mit RESTful-Endpunkten):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Der Zugriff auf diese Dienste wird standardmäßig gewährt. Sie können den Zugriff widerrufen/gewähren. Zum Widerrufen:

	Revoke-AzureRmHDInsightHttpServicesAccess -ClusterName <Cluster Name>

Zum Gewähren:

	$clusterName = "<HDInsight Cluster Name>"

	# Credential option 1
	$hadoopUserName = "admin"
	$hadoopUserPassword = "Pass@word123"
	$hadoopUserPW = ConvertTo-SecureString -String $hadoopUserPassword -AsPlainText -Force
	$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$hadoopUserPW)

	# Credential option 2
	#$credential = Get-Credential -Message "Enter the HTTP username and password:" -UserName "admin"
	
	Grant-AzureRmHDInsightHttpServicesAccess -ClusterName $clusterName -HttpCredential $credential

>[AZURE.NOTE]Durch Gewähren/Widerrufen des Zugriffs werden der Benutzername und das Kennwort des Clusterbenutzers zurückgesetzt.

Dies kann auch über das Vorschauportal erfolgen. Informationen finden Sie unter [Verwalten von HDInsight mit dem Azure-Vorschauportal][hdinsight-admin-portal].

##Aktualisieren von HTTP-Anmeldeinformationen

Das Verfahren ist mit dem [Gewähren/Widerrufen von HTTP-Zugriff](#grant/revoke-access) identisch. Wenn dem Cluster der HTTP-Zugriff gewährt wurde, müssen Sie dies zunächst widerrufen. Gewähren Sie anschließend den Zugriff mit den neuen HTTP-Anmeldeinformationen.


##Suchen des Standardspeicherkontos

Das folgende PowerShell-Skript veranschaulicht, wie der Name und Schlüssel des Standardspeicherkontos für einen Cluster abgerufen werden.

	$clusterName = "<HDInsight Cluster Name>"
	
	$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
	$resourceGroupName = $cluster.ResourceGroup
	$defaultStorageAccountName = ($cluster.DefaultStorageAccount).Replace(".blob.core.windows.net", "")
	$defaultBlobContainerName = $cluster.DefaultStorageContainer
	$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccountName |  %{ $_.Key1 }
	$defaultStorageAccountContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccountName -StorageAccountKey $defaultStorageAccountKey 

##Suchen der Ressourcengruppe

Im ARM-Modus gehört jeder HDInsight-Cluster einer Azure-Ressourcengruppe an. So suchen Sie die Ressourcengruppe:

	$clusterName = "<HDInsight Cluster Name>"
	
	$cluster = Get-AzureRmHDInsightCluster -ClusterName $clusterName
	$resourceGroupName = $cluster.ResourceGroup


##Übermitteln von Aufträgen

**So übermitteln Sie MapReduce-Aufträge**

Siehe [Ausführen von Hadoop MapReduce-Beispielen in Windows-basiertem HDInsight](hdinsight-run-samples.md).

**So übermitteln Sie Hive-Aufträge**

Siehe [Ausführen von Hive-Abfragen mit PowerShell](hdinsight-hadoop-use-hive-powershell.md).

**So übermitteln Sie Pig-Aufträge**

Siehe [Ausführen von Pig-Aufträgen mit PowerShell](hdinsight-hadoop-use-pig-powershell.md).

**So übermitteln Sie Sqoop-Aufträge**

Weitere Informationen finden Sie unter [Verwenden von Sqoop mit HDInsight](hdinsight-use-sqoop.md).

**So übermitteln Sie Oozie-Aufträge**

Siehe [Verwenden von Oozie mit Hadoop zum Definieren und Ausführen eines Workflows in HDInsight](hdinsight-use-oozie.md).

##Hochladen von Daten in Azure-Blobspeicher
Siehe [Hochladen von Daten in HDInsight][hdinsight-upload-data].


## Weitere Informationen
* [Referenzdokumentation zum HDInsight-Cmdlet][hdinsight-powershell-reference]
* [Verwalten von HDInsight mit dem Azure-Vorschauportal][hdinsight-admin-portal]
* [Verwalten von HDInsight über eine Befehlszeilenschnittstelle][hdinsight-admin-cli]
* [Erstellen von HDInsight-Clustern][hdinsight-provision]
* [Hochladen von Daten zu HDInsight][hdinsight-upload-data]
* [Programmgesteuerte Übermittlung von Hadoop-Aufträgen][hdinsight-submit-jobs]
* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-provision-custom-options]: hdinsight-provision-clusters.md#configuration
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md

[hdinsight-admin-cli]: hdinsight-administer-use-command-line.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-flight]: hdinsight-analyze-flight-delay-data.md

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png

<!---HONumber=Nov15_HO3-->