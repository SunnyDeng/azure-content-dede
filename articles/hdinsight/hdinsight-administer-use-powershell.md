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
	ms.date="07/28/2015"
	ms.author="jgao"/>

# Verwalten von Hadoop-Clustern in HDInsight mit Azure PowerShell

[AZURE.INCLUDE [Auswahl](../../includes/hdinsight-portal-management-selector.md)]

Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Workloads in Azure steuern und automatisieren können. In diesem Artikel erfahren Sie, wie Sie Hadoop-Cluster in Azure HDInsight mit einer lokalen Azure PowerShell-Konsole über Windows PowerShell verwalten. Eine Liste der HDInsight PowerShell-Cmdlets finden Sie unter [HDInsight-Cmdlet-Referenz][hdinsight-powershell-reference].



**Voraussetzungen**

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Install and use Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/) (Installieren und Verwenden von Azure PowerShell, in englischer Sprache).

	> [AZURE.NOTE]Die in diesem Artikel bereitgestellten PowerShell-Skripts verwenden den Azure-Ressourcen-Manager-Modus. Laden Sie mit dem Microsoft-Webplattform-Installer die neueste Azure PowerShell-Version herunter, um sicherzustellen, dass die Beispiele funktionieren.

##Bereitstellen von HDInsight-Clustern

HDInsight-Cluster erfordern eine Azure-Ressourcengruppe und einen Blobcontainer in einem Azure-Speicherkonto:

- Eine Azure-Ressourcengruppe ist ein logischer Container für Azure-Ressourcen. Die Azure-Ressourcengruppe und der HDInsight-Cluster müssen sich nicht am gleichen Speicherort befinden. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager](powershell-azure-resource-manager.md).
- HDInsight verwendet einen Blobcontainer eines Azure-Speicherkontos als Standarddateisystem. Sie benötigen ein Azure-Speicherkonto und einen Speichercontainer, bevor Sie ein HDInsight-Cluster erstellen können. Das Standardspeicherkonto und der HDInsight-Cluster müssen sich am gleichen Speicherort befinden.

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**So erstellen Sie eine Azure-Ressourcengruppe**

1. Stellen Sie sicher, dass der Azure-Ressourcenmodus aktiviert ist:

		Switch-AzureMode -Name AzureResourceManager

2. Verbinden Sie Ihr Azure-Konto, und wählen Sie ein Abonnement aus (falls Sie über mehrere Abonnements verfügen).

		Add-AzureAccount
		Select-AzureSubscription

3. Erstellen Sie eine neue Ressourcengruppe:

	New-AzureResourceGroup -name <AzureResourceGroupName> -Location <AzureDataCente> #  Beispiel: USA Westen

	[AZURE.INCLUDE [Datencenter-Liste](../../includes/hdinsight-pricing-data-centers-clusters.md)]

**So erstellen Sie ein Azure-Speicherkonto**

	New-AzureStorageAccount -ResourceGroupName <AzureResourceGroupName> -Name <AzureStorageAccountName> -Location <AzureDataCneter> -Type <AccountType> # account type example: Standard_ZRS for zero redundancy storage

	For a full list of the storage account types, see [https://msdn.microsoft.com/de-de/library/azure/hh264518.aspx](https://msdn.microsoft.com/de-de/library/azure/hh264518.aspx).


Informationen zum Erstellen von Azure-Speicherkonten mit dem Azure-Vorschauportal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos](storage-create-storage-account.md).

Falls Sie bereits ein Speicherkonto haben, jedoch den Kontonamen und den Kontoschlüssel nicht kennen, können Sie diese Informationen mit den folgenden Befehlen abrufen:

	# List Storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a Storage account
	Get-AzureStorageAccountKey -ResourceGroupName <AzureResourceGroupName> -name $storageAccountName <AzureStorageAccountName>

Ausführliche Informationen zum Abrufen dieser Informationen über das Azure-Vorschauportal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos](storage-create-storage-account.md) im Abschnitt „Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln“.

**So erstellen Sie einen Azure-Speichercontainer**

Während der HDInsight-Bereitstellung kann von Azure PowerShell kein Blobcontainer erstellt werden. Sie können einen Container mit dem folgenden Skript erstellen:

	$resourceGroupName = "<AzureResoureGroupName>"
	$storageAccountName = "<AzureStorageAccountName>"
	$storageAccountKey = Get-AzureStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }
	$containerName="<AzureBlobContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**So stellen Sie einen Cluster bereit**

Sobald Sie Ihr Speicherkonto und Ihren Blobcontainer vorbereitet haben, können Sie einen Cluster erstellen.

	$resourceGroupName = "<AzureResoureGroupName>"

	$storageAccountName = "<AzureStorageAccountName>"
	$containerName = "<AzureBlobContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<AzureDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the Storage account key
	$storageAccountKey = Get-AzureStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }

	# Create a new HDInsight cluster
	New-AzureHDInsightCluster -ResourceGroupName $resourceGroupName `
		-ClusterName $clusterName `
		-Location $location `
		-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		-DefaultStorageAccountKey $storageAccountKey `
		-DefaultStorageContainer $containerName  `
		-ClusterSizeInNodes $clusterNodes

##Auflisten von Clusterdetails
Verwenden Sie den folgenden Befehl, um alle Cluster des aktuellen Abonnements aufzulisten:

	Get-AzureHDInsightCluster

Verwenden Sie den folgenden Befehl, um Details zu einem bestimmten Cluster des aktuellen Abonnements anzuzeigen:

	Get-AzureHDInsightCluster -ResourceGroupName <ResouceGroupName> -ClusterName <ClusterName>

##Löschen von Clustern
Mit dem folgenden Befehl können Sie ein Cluster löschen:

	Remove-AzureHDInsightCluster -Name <ClusterName>



##Gewähren/Widerrufen von Zugriff auf HTTP-Dienste

In HDInsight-Clustern stehen die folgenden HTTP-Webdienste zur Verfügung (alle mit RESTful-Endpunkten):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Der Zugriff auf diese Dienste wird standardmäßig gewährt. Sie können den Zugriff widerrufen/gewähren. Hier ein Beispiel:

	Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

In diesem Beispiel ist <i>hdiv2</i> der Name eines HDInsight-Clusters.

>[AZURE.NOTE]Durch Gewähren/Widerrufen des Zugriffs werden der Benutzername und das Kennwort des Clusterbenutzers zurückgesetzt.

Dies kann auch über das Vorschauportal erfolgen. Informationen finden Sie unter [Verwalten von HDInsight mit dem Azure-Vorschauportal][hdinsight-admin-portal].

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

	Set-AzureHDInsightClusterSize -Name <ClusterName> -ClusterSizeInNodes <NewSize>



##Übermitteln von MapReduce-Jobs
In der HDInsight-Clusterbereitstellung sind einige MapReduce-Beispiele enthalten. Eines dieser Beispiele dient zum Zählen von Worthäufigkeiten in Quelldateien.

**So übermitteln Sie einen MapReduce-Auftrag**

Mit dem folgenden Azure PowerShell-Skript wird der Beispielauftrag zur Wortzählung übermittelt:

	$clusterName = "<HDInsightClusterName>"

	# Define the MapReduce job
	$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	# Run the job and show the standard error
	$wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}

Informationen über das Präfix **wasb** finden Sie unter [Verwenden von Azure-Blobspeicher für HDInsight][hdinsight-storage].

**So laden Sie die Ausgabe des MapReduce-Auftrags herunter**

Mit dem folgenden Azure PowerShell-Skript wird die Ausgabe des MapReduce-Auftrags aus dem letzten Verfahren abgerufen:

	$storageAccountName = "<StorageAccountName>"
	$containerName = "<ContainerName>"

	# Create the Storage account context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	# Download the output to local computer
	Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	# Display the output
	cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

Weitere Informationen zum Entwickeln und Ausführen von MapReduce-Jobs finden Sie unter [Verwenden von MapReduce mit HDInsight][hdinsight-use-mapreduce].






































##Übermitteln von Hive-Jobs
Die HDInsight-Clusterbereitstellung enthält eine Hive-Beispieltabelle mit dem Namen *hivesampletable*. Mit dem HiveQL-Befehl **SHOW TABLES** können Sie die Hive-Tabellen in einem Cluster auflisten.

**So übermitteln Sie einen Hive-Auftrag**

Mit dem folgenden Skript wird ein Hive-Auftrag übermittelt, um die Hive-Tabellen aufzulisten:

	$clusterName = "<HDInsightClusterName>"

	# HiveQL query
	$querystring = @"
		SHOW TABLES;
		SELECT * FROM hivesampletable
			WHERE Country='United Kingdom'
			LIMIT 10;
	"@

	Use-AzureHDInsightCluster -Name $clusterName
	Invoke-Hive $querystring

Der Hive-Auftrag zeigt zuerst die im Cluster erstellten Hive-Tabellen und dann die aus der Tabelle "hivesampletable" zurückgegebenen Daten an.

Weitere Informationen über Hive finden Sie unter [Verwenden von Hive mit HDInsight][hdinsight-use-hive].


##Hochladen von Daten in Azure-Blobspeicher
Siehe [Hochladen von Daten in HDInsight][hdinsight-upload-data].

##Herunterladen der Auftragsausgabe von Azure-Blobspeicher
Informationen finden Sie im Abschnitt [Übermitteln von MapReduce-Aufträgen](#mapreduce) in diesem Artikel.

## Weitere Informationen
* [Referenzdokumentation zum HDInsight-Cmdlet][hdinsight-powershell-reference]
* [Verwalten von HDInsight mit dem Azure-Vorschauportal][hdinsight-admin-portal]
* [Verwalten von HDInsight über eine Befehlszeilenschnittstelle][hdinsight-admin-cli]
* [Bereitstellen von HDInsight-Clustern][hdinsight-provision]
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

<!---HONumber=August15_HO8-->