<properties 
	pageTitle="Verwalten von Hadoop-Clustern in HDInsight mit Azure PowerShell | Microsoft Azure" 
	description="Hier erfahren Sie, wie Sie administrative Aufgaben für Hadoop-Cluster in HDInsight mit Azure PowerShell ausführen." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="mumian" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="jgao"/>

# Verwalten von Hadoop-Clustern in HDInsight mit Azure PowerShell


Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. In diesem Artikel erfahren Sie, wie Sie Hadoop-Cluster in Azure HDInsight mit einer lokalen Azure PowerShell-Konsole über Windows PowerShell verwalten. Eine Liste der HDInsight PowerShell-Cmdlets finden Sie unter [HDInsight-Cmdlet-Referenz][hdinsight-powershell-reference].

##Voraussetzungen

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Installieren und Verwenden von Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).


##Bereitstellen von HDInsight-Clustern
HDInsight verwendet Azure-Blobspeichercontainer als Standarddateisystem. Sie benötigen ein Azure-Speicherkonto und einen Speichercontainer, bevor Sie ein HDInsight-Cluster erstellen können.

[AZURE.INCLUDE [provisioningnote](../../includes/hdinsight-provisioning.md)]

**So erstellen Sie ein Azure-Speicherkonto**

Nachdem Sie die publishsettings-Datei importiert haben, können Sie mit dem folgenden Befehl ein Speicherkonto erstellen:

	# Create an Azure Storage account
	$storageAccountName = "<StorageAcccountName>"
	$location = "<Microsoft data center>"           # For example, "West US"

	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location


[AZURE.INCLUDE [Datencenter-Liste](../../includes/hdinsight-pricing-data-centers-clusters.md)]


Informationen zum Erstellen von Azure-Speicherkonten im Azure-Portal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos](../storage-create-storage-account/).

Falls Sie bereits ein Speicherkonto haben, jedoch den Kontonamen und den Kontoschlüssel nicht kennen, können Sie diese Informationen mit den folgenden Befehlen abrufen:

	# List Storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a Storage account
	Get-AzureStorageKey <StorageAccountName>

Ausführliche Informationen zum Abrufen dieser Informationen über das Azure-Portal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos](../storage-create-storage-account/) im Abschnitt "Anzeigen, Kopieren und Neuerstellen von Speicherzugriffsschlüsseln".

**So erstellen Sie einen Azure-Speichercontainer**

Während der HDInsight-Bereitstellung kann von Azure PowerShell kein Blobcontainer erstellt werden. Sie können einen Container mit dem folgenden Skript erstellen:

	$storageAccountName = "<StorageAccountName>"
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$containerName="<ContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**So stellen Sie einen Cluster bereit**

Sobald Sie Ihr Speicherkonto und Ihren Blobcontainer vorbereitet haben, können Sie ein Cluster erstellen.
		
	$storageAccountName = "<StorageAccountName>"
	$containerName = "<ContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<MicrosoftDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the Storage account key
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

	# Create a new HDInsight cluster
	New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes


Der folgende Screenshot zeigt die Skriptausführung:

![HDI.PS.Bereitstellen][image-hdi-ps-provision]




##Auflisten von Clusterdetails
Verwenden Sie den folgenden Befehl, um alle Cluster des aktuellen Abonnements aufzulisten:

	Get-AzureHDInsightCluster 

Verwenden Sie den folgenden Befehl, um Details zu einem bestimmten Cluster des aktuellen Abonnements anzuzeigen:

	Get-AzureHDInsightCluster -Name <ClusterName> 

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

Dies kann auch über das Azure-Portal durchgeführt werden. Siehe [Verwalten von HDInsight mit dem Azure-Portal][hdinsight-admin-portal]

##Skalieren von Clustern
Siehe [Skalieren von Hadoop-Clustern in HDInsight](hdinsight-hadoop-cluster-scaling.md).

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
* [Verwalten von HDInsight mit dem Azure-Portal][hdinsight-admin-portal]
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


 

<!---HONumber=July15_HO4-->