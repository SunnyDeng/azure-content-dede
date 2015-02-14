<properties 
	pageTitle="Verwalten von Hadoop-Clustern in HDInsight mit Azure PowerShell | Azure" 
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
	ms.date="11/21/2014" 
	ms.author="jgao"/>

# Verwalten von Hadoop-Clustern in HDInsight mit Azure PowerShell

Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. In diesem Artikel erfahren Sie, wie Sie Hadoop-Cluster in HDInsight mit einer lokalen Azure PowerShell-Konsole über Windows PowerShell verwalten. Eine Liste der HDInsight-PowerShell-Cmdlets finden Sie in der [HDInsight-Cmdlet-Referenz][hdinsight-powershell-reference].

**Voraussetzungen:**

Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- Ein Azure-Abonnement. Azure ist eine abonnementbasierte Plattform. Die HDInsight PowerShell-Cmdlets führen Aufgaben für Ihr Abonnement aus. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][azure-purchase-options], [Spezielle Angebote][azure-member-offers] oder [kostenlose Testversion][azure-free-trial].

- Eine Arbeitsstation mit Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Powershell-install-configure].

			
	

## Themen in diesem Artikel

* [Bereitstellen eines Clusters](#provision)
* [Auflisten und Anzeigen von Clustern](#listshow)
* [Löschen eines Clusters](#delete)
* [Gewähren/Widerrufen des Zugriffs auf HTTP-Dienste](#httpservices)
* [Übermitteln von MapReduce-Jobs](#mapreduce)
* [Übermitteln von Hive-Jobs](#hive)
* [Hochladen von Daten in den Blob-Speicher](#upload)
* [Herunterladen der MapReduce-Ausgabe aus dem Blob-Speicher](#download)


##<a id="provision"></a> Bereitstellen eines HDInsight-Clusters
HDInsight verwendet Azure-Blob-Speichercontainer als Standarddateisystem. Sie benötigen ein Speicherkonto und einen Speichercontainer, um einen HDInsight-Cluster erstellen zu können. 

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

**So erstellen Sie ein Azure-Speicherkonto**

Nachdem Sie die Datei mit Veröffentlichungseinstellungen importiert haben, können Sie mit dem folgenden Befehl ein Speicherkonto erstellen:

	# Create an Azure storage account
	$storageAccountName = "<StorageAcccountName>"
	$location = "<Microsoft data center>"           # For example, "West US"

	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

> [AZURE.NOTE] Das Speicherkonto muss sich im selben Rechenzentrum wie der HDInsight-Cluster befinden. Derzeit können Sie HDInsight-Cluster nur in folgenden Rechenzentren bereitstellen:

><ul>
<li>Südostasien</li>
<li>Nordeuropa</li>
<li>Westeuropa</li>
<li>USA (Ost)</li>
<li>USA (West)</li>
</ul>



Informationen zum Erstellen von Azure-Speicherkonten mithilfe des Verwaltungsportals finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos](../storage-create-storage-account/).

Falls Sie bereits ein Speicherkonto haben und den Kontonamen und den Kontoschlüssel nicht kennen, können Sie diese Informationen mit den folgenden Befehlen abrufen:

	# List storage accounts for the current subscription
	Get-AzureStorageAccount
	# List the keys for a storage account
	Get-AzureStorageKey <StorageAccountName>

Weitere Informationen zum Abrufen dieser Informationen mithilfe des Verwaltungsportals finden Sie im Abschnitt *Vorgehensweise: Anzeigen, Kopieren und erneutes Generieren von Speicherzugriffsschlüsseln* unter [Erstellen, Verwalten oder Löschen eines Speicherkontos].(../storage-create-storage-account/).

**So erstellen Sie einen Azure-Speichercontainer**

Während der HDInsight-Bereitstellung kann von PowerShell kein Blob-Container erstellt werden. Sie können einen Container mit dem folgenden Skript erstellen:

	$storageAccountName = "<StorageAccountName>"
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$containerName="<ContainerName>"

	# Create a storage context object
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
	                                       -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

**So stellen Sie einen Cluster bereit**

Sobald Sie Ihr Speicherkonto und Ihren Blob-Container vorbereitet haben, können Sie einen Cluster erstellen.    
		
	$storageAccountName = "<StorageAccountName>"
	$containerName = "<ContainerName>"

	$clusterName = "<HDInsightClusterName>"
	$location = "<MicrosoftDataCenter>"
	$clusterNodes = <ClusterSizeInNodes>

	# Get the storage account key
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

	# Create a new HDInsight cluster
	New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes


Der folgende Screenshot zeigt die Skriptausführung:

![HDI.PS.Provision][image-hdi-ps-provision]




##<a id="listshow"></a> Auflisten und Anzeigen von Clusterdetails
Mit den folgenden Befehlen können Sie Clusterdetails auflisten und anzeigen:

**So listen Sie alle Cluster im aktuellen Abonnement auf**

	Get-AzureHDInsightCluster 

**So zeigen Sie die Details eines bestimmten Clusters im aktuellen Abonnement an**

	Get-AzureHDInsightCluster -Name <ClusterName> 

##<a id="delete"></a> Löschen eines Clusters
Mit dem folgenden Befehl können Sie einen Cluster löschen:

	Remove-AzureHDInsightCluster -Name <ClusterName> 

##<a id="httpservice"></a> Gewähren/Widerrufen von Zugriff auf HTTP-Dienste

In HDInsight-Clustern stehen die folgenden HTTP-Webdienste zur Verfügung (alle mit RESTful-Endpunkten):

- ODBC
- JDBC
- Ambari
- Oozie
- Templeton


Der Zugriff auf diese Dienste wird standardmäßig gewährt. Sie können den Zugriff widerrufen/gewähren.  Hier ein Beispiel:

	Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

Im Beispiel ist <i>hdiv2</i> der Name eines HDInsight-Clusters.

>[AZURE.NOTE] Durch Gewähren/Widerrufen des Zugriffs werden der Benutzername und das Kennwort des Clusterbenutzers zurückgesetzt.

Diese Aufgabe kann auch im Azure-Verwaltungsportal ausgeführt werden. Weitere Informationen finden Sie unter [Verwalten von HDInsight mit dem Verwaltungsportal][hdinsight-admin-portal].

##<a id="mapreduce"></a> Übermitteln von MapReduce-Jobs
In der HDInsight-Clusterbereitstellung sind einige MapReduce-Beispiele enthalten. Eines dieser Beispiele dient zum Zählen von Worthäufigkeiten in Quelldateien.

**So übermitteln Sie einen MapReduce-Job**

Mit dem folgenden PowerShell-Skript wird der Beispieljob zur Wortzählung übermittelt: 
	
	$clusterName = "<HDInsightClusterName>"            
	
	# Define the MapReduce job
	$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
	
	# Run the job and show the standard error 
	$wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}
	
> [AZURE.NOTE] *hadoop-examples.jar* wird mit HDInsight-Clustern der Version 2.1 ausgeliefert. Die Datei wurde bei HDInsight-Clustern der Version 3.0 umbenannt in *hadoop-mapreduce.jar*.

Informationen über das Präfix WASB finden Sie unter [Verwenden von Azure Blob-Speicher für HDInsight][hdinsight-
storage].

**So laden Sie die Ausgabe des MapReduce-Jobs herunter**

Mit dem folgenden PowerShell-Skript wird die Ausgabe des MapReduce-Jobs aus dem letzten Beispiel abgerufen:

	$storageAccountName = "<StorageAccountName>"   
	$containerName = "<ContainerName>"             
		
	# Create the storage account context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Download the output to local computer
	Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
	
	# Display the output
	cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

Weitere Informationen zum Entwickeln und Ausführen von MapReduce-Jobs finden Sie unter [Verwenden von MapReduce mit HDInsight][hdinsight-use-mapreduce].






































##<a id="hive"></a> Übermitteln von Hive-Jobs
Die HDInsight-Clusterbereitstellung wird mit einer Hive-Beispieltabelle namens *hivesampletable* ausgeliefert. Mit der HiveQL-Abfrage "show tables;" können Sie die Hive-Tabellen in einem Cluster auflisten.

**So übermitteln Sie einen Hive-Job**

Mit dem folgenden Skript wird ein Hive-Job zum Auflisten der Hive-Tabellen übermittelt:
	
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

Der Hive-Job zeigt zuerst die im Cluster erstellten Hive-Tabellen und dann die aus der hivesampletable zurückgegebenen Daten an.

Weitere Informationen zur Verwendung von Hive finden Sie unter [Verwenden von Hive mit HDInsight][hdinsight-use-hive].


##<a id="upload"></a>Hochladen von Daten in den Blob-Speicher
Weitere Informationen finden Sie unter [Hochladen von Daten in HDInsight][hdinsight-upload-data].

##<a id="download"></a>Herunterladen der MapReduce-Ausgabe aus dem Blob-Speicher
Weitere Informationen finden Sie in der Sitzung [Übermitteln von MapReduce-Jobs](#mapreduce) in diesem Artikel.

## Weitere Informationen finden Sie außerdem in der
* [HDInsight-Cmdlet-Referenzdokumentation][hdinsight-powershell-reference]
* [Verwalten von HDInsight mit dem Verwaltungsportal][hdinsight-admin-portal]
* [Verwalten von HDInsight mit der Befehlszeilen-Schnittstelle][hdinsight-admin-cli]
* [Bereitstellen von HDInsight-Clustern][hdinsight-provision]
* [Hochladen von Daten in HDInsight][hdinsight-upload-data]
* [Programmgesteuerte Übermittlung von Hadoop-Jobs][hdinsight-submit-jobs]
* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]


[azure-purchase-options]: http://azure.microsoft.com/de-de/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/de-de/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/de-de/pricing/free-trial/

[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-provision]: ../hdinsight-provision-clusters/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/

[hdinsight-admin-cli]: ../hdinsight-administer-use-command-line/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-upload-data]: ../hdinsight-upload-data/

[hdinsight-powershell-reference]: http://msdn.microsoft.com/de-de/library/windowsazure/dn479228.aspx

[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-ps-provision]: ./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png



<!--HONumber=42-->
