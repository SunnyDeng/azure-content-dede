<properties 
	pageTitle="Abfragen großer Datenmengen aus Hadoop-kompatiblen Blob-Speicher | Azure" 
	description="HDInsight verwendet Hadoop-kompatiblen Blob-Speicher als Big Data-Speicher für HDFS. Erfahren Sie, wie vom Blob-Speicher abfragen und die Ergebnisse der Analyse speichern." 
	services="storage, hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="mollybos"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/12/2014" 
	ms.author="jgao"/>


# Abfragen großer Datenmengen aus Hadoop-kompatiblen Blob-Speicher zur Analyse in HDInsight

Kostengünstiger Blob-Speicher stellt eine robuste, universelle Hadoop-kompatible Azure-Speicherlösung dar, die problemlos mit HDInsight integriert werden kann. Über eine HDFS-Schnittstelle (Hadoop Distributed File System) können sämtliche Komponenten in HDInsight direkt mit den Daten im Blob-Speicher arbeiten. In diesem Lernprogramm erfahren Sie, wie Sie einen Container für Blob-Speicher einrichten und dann die darin enthaltenen Daten adressieren.

Die Speicherung von Daten in Blob-Speicher sorgt dafür, dass die HDInsight-Cluster, die für Berechnungen verwendet werden, sicher gelöscht werden können, ohne Benutzerdaten zu verlieren. 

> [AZURE.NOTE]	Die *asv://*-Syntax wird in HDInsight-Clustern der Version 3.0 und auch in späteren Versionen nicht unterstützt. Dies bedeutet, dass alle an einen HDInsight-Cluster der Version 3.0 übermittelten Aufträge, die die "asv://"-Syntax verwenden, fehlschlagen werden. Stattdessen sollte die *wasb://*-Syntax verwendet werden. An HDInsight-Cluster der Version 3.0 gesendete Jobs, die mithilfe eines vorhandenen Metastores erzeugt wurden, der explizite Verweise auf Ressourcen mit der asv://-Syntax enthält, schlagen ebenfalls fehl. Diese Metastores müssen mit der wasb://-Syntax neu erstellt werden, um die Ressourcen zu adressieren.

> HDInsight unterstützt aktuell nur Blockblobs.

> Die meisten HDFS-Befehle wie z. B. <b>ls</b>, <b>copyFromLocal</b>, <b>mkdir</b> usw. funktionieren weiterhin wie erwartet. Nur die für die native HDFS-Implementierung (als DFS bezeichnet) spezifischen Befehle wie <b>fschk</b> und <b>dfsadmin</b> zeigen in Azure BLOB-Speicher ein anderes Verhalten.

Informationen zur Bereitstellung eines HDInsight-Clusters finden Sie unter [Erste Schritte mit HDInsight][hdinsight-get-started] oder [Bereitstellen von HDInsight-Clustern][hdinsight-provision].

## Themen in diesem Artikel

* [HDInsight-Speicherarchitektur](#architecture)
* [Vorteile von Azure-BLOB-Speicher](#benefits)
* [Vorbereiten eines Containers für BLOB-Speicher](#preparingblobstorage)
* [Adressdateien in BLOB-Speicher](#addressing)
* [Zugreifen auf BLOB über PowerShell](#powershell)
* [Nächste Schritte](#nextsteps)

## <a id="architecture"></a>HDInsight-Speicherarchitektur
Das folgende Diagramm bietet einen zusammenfassenden Überblick über die HDInsight-Speicherarchitektur.

![Hadoop clusters in HDInsight access and store big data in cost-effective, scalable Hadoop-compatible Azure Blob storage in the cloud.](./media/hdinsight-use-blob-storage/HDI.ASVArch.png "HDInsight Storage Architecture")
  
HDInsight bietet Zugang zum verteilten Dateisystem, das lokal an die Rechenknoten angefügt ist. Auf dieses Dateisystem kann über den vollqualifizierten URI zugegriffen werden. Beispiel: 

	hdfs://<namenodehost>/<path>

Zusätzlich bietet HDInsight die Möglichkeit, auf die in Blob-Speicher gespeicherten Daten zuzugreifen. Die Syntax für den Zugriff auf Blob-Speicher lautet:

	wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


Hadoop unterstützt eine Art Standard-Dateisystem. Zu diesem gehören ein Standard-Schema und eine Standard-Berechtigung; es kann auch zur Auflösung relativer Pfade verwendet werden. Während des Prozesses zur Bereitstellung von HDInsight werden ein Azure-Speicherkonto und ein bestimmter Blob-Speichercontainer aus diesem Konto als Standard-Dateisystem festgelegt.

Zusätzlich zu diesem Speicherkonto können Sie während des Bereitstellungsprozesses weitere Speicherkonten aus demselben Azure-Abonnement oder anderen Azure-Abonnements hinzufügen. Informationen zum Hinzufügen zusätzlicher Speicherkonten finden Sie unter [Bereitstellen von HDInsight-Clustern][hdinsight-provision]. 

- **Container in mit einem Cluster verbundenen Speicherkonten:  ** Da der Kontoname und der Kontoschlüssel in der Datei *core-site.xml* gespeichert sind, haben Sie vollständigen Zugriff auf die BLOBs in diesen Containern.
- **Öffentliche Container oder öffentliche BLOBs in Speicherkonten, die NICHT mit einem Cluster verbunden sind:** Sie haben schreibgeschützten Zugriff auf die BLOBs in den Containern.

	> [AZURE.NOTE]
        > Öffentliche Container erlauben das Abrufen einer Liste aller im Container verfügbaren Blobs und der Container-Metadaten. Auf öffentliche BLOBs haben  Sie nur Zugriff, wenn Sie die exakte URL kennen. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/de-de/library/windowsazure/dd179354.aspx">Einschränken des Zugriffs auf Container und Blobs</a>.

- **Private Container in Speicherkonten, die NICHT mit einem Cluster verbunden sind:** Sie können nicht auf die BLOBs in den Containern zugreifen, es sei denn Sie definieren beim Senden der WebHCat-Aufträge das Speicherkonto.


Das beim Einrichtungsprozess definierte Speicherkonto und die Schlüssel werden in %HADOOP_HOME%/conf/core-site.xml gespeichert.  Standardmäßig verwendet HDInsight die in der Datei core-site.xml definierten Speicherkonten. Die Datei core-site.xml sollte nicht manuell bearbeitet werden, da der Cluster-Stammknoten (Master) jederzeit neu aus einem Abbild erstellt oder migriert werden kann, und die Änderungen an den Dateien in diesem Fall verloren gehen.

Verschiedene WebHCat-Aufträge, darunter Hive, MapReduce, Hadoop Streaming und Pig, können eine Beschreibung von Speicherkonten und Metadaten enthalten (funktioniert momentan nur für Pig und Speicherkonten, nicht für Metadaten.) Im Abschnitt [Zugreifen auf BLOBs mit PowerShell](#powershell) dieses Artikels befindet sich ein Beispiel zu dieser Funktion. Weitere Informationen finden Sie unter [Verwenden eines HDInsight-Clusters mit alternativen Speicherkonten und Metastores](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

In Blob-Speichercontainern werden Daten als Schlüssel/Wert-Paare gespeichert, und es gibt keine Verzeichnishierarchie. Allerdings kann im Schlüsselnamen das Zeichen '/' verwendet werden, damit es so aussieht, als wäre eine Datei in einer Verzeichnisstruktur gespeichert. Der Schlüssel eines BLOB kann z. B. *input/log1.txt* sein. Das Verzeichnis *input* existiert zwar nicht, wegen des Zeichens "/" im Schlüsselnamen sieht es jedoch so aus, als gäbe es einen Dateipfad.










## <a id="benefits"></a>Vorteile von Azure Blob-Speicher
Der Leistungsaufwand, der damit verbunden ist, dass die Berechnung und die Speicherung nicht am selben Ort erfolgen, wird dadurch abgeschwächt, dass die Cluster nahe an den Speicherkontoressourcen im Azure-Datencenter bereitgestellt werden, wo das Hochgeschwindigkeits-Netzwerk den Zugriff auf die Daten im Blob-Speicher für die Rechenknoten sehr effizient macht.

Die Speicherung von Daten in Blob-Speicher statt in HDFS hat mehrere Vorteile:

* **Wiederverwenden und Freigeben von Daten:** Die Daten im HDFS befinden sich im Rechencluster. Nur die Anwendungen mit Zugriff auf die Rechencluster können die Daten mit der HDFS-API verwenden. Auf die Daten im BLOB-Speicher können Sie über die HDFS-APIs oder über die [BLOB-Speicher-REST-APIs][blob-storage-restAPI] zugreifen. Daher können mehr Anwendungen (darunter andere HDInsight-Cluster) und Tools verwendet werden, um die Daten zu generieren und zu konsumieren.
* **Datenarchivierung:** Die Speicherung von Daten im BLOB-Speicher sorgt dafür, dass die HDInsight-Cluster, die für Berechnungen verwendet werden, sicher gelöscht werden können, ohne Benutzerdaten zu verlieren. 
* **Datenspeicherkosten:** Die langfristige Speicherung von Daten in DFS ist teurer als das Speichern von Daten im BLOB-Speicher, da die Kosten eines Rechenclusters höher sind als die Kosten für einen BLOB-Speichercontainer. Darüber hinaus sparen Sie auch beim Laden von Daten, da die Daten nicht bei jeder Rechenclustererstellung neu geladen werden müssen.
* **Flexible Skalierung:** Während HDFS Ihnen ein Dateisystem mit horizontaler Skalierung bietet, ist der Umfang der Skalierung von der Anzahl der für den Cluster bereitgestellten Knoten abhängig. Die Änderung der Skalierung kann komplizierter sein, als sich auf die flexiblen Skalierungsmöglichkeiten eines BLOB-Speichers zu verlassen, die Sie automatisch erhalten.
* **Georeplikation:** Der BLOB-Speicher kann über das Azure-Portal georepliziert werden. Auf diese Weise erhalten Sie eine geografische Wiederherstellungs- und Datenredundanz. Ein Failover auf den georeplizierten Standort beeinträchtigt jedoch die Leistung maßgeblich und verursacht u. U. zusätzliche Kosten. Es wird empfohlen, die Georeplikation nur nach sorgfältiger Überlegung anzuwenden und nur, wenn der Wert der Daten die zusätzlichen Kosten rechtfertigt.

Bestimmte MapReduce-Jobs und -Pakete können zu Zwischenergebnissen führen, die Sie nicht wirklich im Blob-Speichercontainer speichern möchten. In diesem Falle können Sie sich immer noch entscheiden, die Dateien im lokalen HDFS zu speichern. Tatsächlich verwendet HDInsight DFS für einige dieser Zwischenergebnisse in Hive-Jobs und anderen Prozessen. 





## <a id="preparingblobstorage"></a>Vorbereiten eines Containers für Blob-Speicher
Zum Verwenden von BLOBs erstellen Sie zuerst ein [Azure-Speicherkonto][azure-storage-create]. Dabei geben Sie ein Azure-Datencenter an, in dem die mithilfe dieses Kontos erstellten Objekte gespeichert werden. Der Cluster und das Speicherkonto müssen im selben Datencenter gehostet werden. (Hive-Metastore-SQL-Datenbanken und Oozie-Metastore-SQL-Datenbanken müssen sich ebenfalls im selben Datencenter befinden.) Ein Blob gehört unabhängig davon, wo es sich befindet, stets zu einem Container in Ihrem Speicherkonto. Dieser Container kann ein außerhalb von HDInsight erstellter Blob-Speichercontainer sein, oder es handelt sich um einen Container, der für einen HDInsight-Cluster erstellt wird. 



### Erstellen eines Blob-Containers für HDInsight mithilfe des Verwaltungsportals

Beim Bereitstellen eines HDInsight-Clusters über das Azure-Verwaltungsportal gibt es zwei Optionen: *quick create* und *custom create*. Für die Schnellerstellung muss das Azure-Speicherkonto bereits vorher erstellt worden sein.  Anweisungen hierzu finden Sie unter [Erstellen eines Speicherkontos][azure-storage-create]. 

Wenn Sie die Option "Schnellerstellung" verwenden, können Sie ein vorhandenes Speicherkonto auswählen. Während des Bereitstellungsprozesses wird ein neuer Container mit dem Namen des HDInsight-Clusters erstellt. Wenn ein Container mit demselben Namen bereits vorhanden ist, wird <clusterName>-<x> verwendet. Beispiel: myHDIcluster-1. Dieser Container wird als Standard-Dateisystem verwendet.

![Using Quick Create for a new Hadoop cluster in HDInsight in the Azure portal.][img-hdi-quick-create]
 
Mit der benutzerdefinierten Erstellung können Sie für das Standard-Speicherkonto eine der folgenden Optionen auswählen:

- Vorhandenen Speicher verwenden
- Neuen Speicher erstellen
- Speicher aus anderem Abonnement verwenden.

Sie haben außerdem die Auswahl, einen neuen Blob-Container zu erstellen oder einen vorhandenen zu verwenden.
 
![Option to use an existing storage account for your HDInsight cluster.][img-hdi-custom-create-storage-account]
  




### Erstellen eines Containers mit Azure PowerShell.
[Azure PowerShell][powershell-install] kann zur Erstellung von BLOB-Containern verwendet werden. Nachfolgend ein PowerShell-Beispielskript:

	$subscriptionName = "<SubscriptionName>"	# Azure subscription name
	$storageAccountName = "<AzureStorageAccountName>" # The storage account that you will create
	$containerName="<BlobContainerToBeCreated>" # The Blob container name that you will create

	# Connect to your Azure account and selec the current subscription
	Add-AzureAccount # The connection will expire in 12 hours.
	Select-AzureSubscription $subscriptionName #only required if you have multiple subscriptions
	
	# Create a storage context object
	$storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext 


## <a id="addressing"></a>Adressdateien in Blob-Speicher

Das URI-Schema für den Dateizugriff im Blob-Speicher ist: 

	wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [AZURE.NOTE] Die Syntax zum Ansprechen der Dateien im Speicheremulator (ausgeführt im HDInsight-Emulator) lautet <i>wasb://&lt;Containername&gt;@storageemulator</i>.



Das URI-Schema bietet sowohl unverschlüsselten Zugriff mit dem Präfix *wasb:* als auch SSL-verschlüsselten Zugriff mit *wasbs*. Wir empfehlen die Verwendung von *wasbs*, selbst für den Zugriff auf Daten, die sich im selben Azure-Rechenzentrum befinden.
	
&lt;BlobStorageContainerName&gt; ist der Name des BLOB-Speichercontainers.
&lt;StorageAccountName&gt; ist der Name des Azure-Speicherkontos. Ein vollqualifizierter Domänenname (FQDN) ist erforderlich.
	
Wenn weder &lt;BlobStorageContainerName&gt; noch &lt;StorageAccountName&gt; angegeben ist, wird das Standarddateisystem verwendet. Für die Dateien im Standard-Dateisystem können Sie entweder relative oder absolute Pfade verwenden. Auf die Datei hadoop-mapreduce-examples.jar, die sich in HDInsight-Clustern befindet, kann z. B. mit einem der folgenden Befehle verwiesen werden:

	wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	wasb:///example/jars/hadoop-mapreduce-examples.jar
	/example/jars/hadoop-mapreduce-examples.jar
	
> [AZURE.NOTE] Der Dateiname lautet <i>hadoop-examples.jar</i> (auf HDInsight-Clustern der Version 1.6 und 2.1).


&lt;path&gt; ist der HDFS-Pfadname der Datei oder des Verzeichnisses. Da Blob-Speichercontainer nur ein Schlüssel/Wert-Paar sind, gibt es kein wirkliches hierarchisches Dateisystem. Ein "/" in einem Blob-Schlüssel wird als Verzeichnis-Trennzeichen interpretiert. Der BLOB-Name für *hadoop-mapreduce-examples.jar* lautet z. B.:

	example/jars/hadoop-mapreduce-examples.jar
	

## <a id="powershell"></a>Blob-Zugang über Azure PowerShell

Informationen zur Installation und Konfiguration von Azure PowerShell auf Ihrer Arbeitsstation finden Sie unter [Installieren und konfigurieren von Azure PowerShell][powershell-install]. Sie können das Azure PowerShell-Konsolenfenster oder PowerShell-ISE verwenden, um PowerShell-Cmdlets auszuführen. 

Verwenden Sie den folgenden Befehl, um die Blob-bezogenen Cmdlets aufzulisten:

	Get-Command *blob*

![List of Blob-related PowerShell cmdlets.][img-hdi-powershell-blobcommands]


**PowerShell-Beispiel für das Hochladen einer Datei**

Anweisungen hierzu finden Sie unter [Hochladen von Daten in HDInsight][hdinsight-upload-data].

**PowerShell-Beispiel für das Herunterladen einer Datei**

Mit dem folgenden Skript wird ein Blockblob in das aktuelle Verzeichnis heruntergeladen. Wechseln Sie vor der Ausführung des Skripts in ein Verzeichnis, für das Sie Schreibberechtigungen haben. 


	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "example/data/sample.log" # The name of the blob to be downloaded.
	
	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours
	
	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	Write-Host "Download the blob ..." -ForegroundColor Green
	Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
	
	Write-Host "List the downloaded file ..." -ForegroundColor Green
	cat "./$blob"

**PowerShell-Beispiel für das Löschen einer Datei**

Das folgende Skript löscht eine Datei.
	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blob = "example/data/sample.log" # The name of the blob to be downloaded.
	
	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours
	
	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
	
	Write-Host "Delete the blob ..." -ForegroundColor Green
	Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob 
	

**PowerShell-Beispiel für das Auflisten von Dateien in einem Ordner**

Das folgende Skript listet die Dateien in einem "Ordner" auf. Das nächste Beispiel ruft das Invoke-Hive-Cmdlet auf, um den Befehl "dfs ls" zum Auflisten eines Ordners auszuführen.

	$storageAccountName = "<AzureStorageAccountName>"   # The storage account used for the default file system specified at provision.
	$containerName = "<BlobStorageContainerName>"  # The default file system container has the same name as the cluster.
	$blobPrefix = "example/data/"
	
	# Use Add-AzureAccount if you haven't connected to your Azure subscription
	#Add-AzureAccount # The connection is good for 12 hours
	
	# Use these two commands if you have multiple subscriptions
	#$subscriptionName = "<SubscriptionName>"       
	#Select-AzureSubscription $subscriptionName
	
	Write-Host "Create a context object ... " -ForegroundColor Green
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	Write-Host "List the files in $blobPrefix ..."
	Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix $blobPrefix

**PowerShell-Beispiel für den Zugriff auf ein undefiniertes Speicherkonto**
	
Dieses Beispiel listet einen Ordner aus einem Speicherkonto auf, das nicht beim Einrichtungsprozess definiert wurde.
	$clusterName = "<HDInsightClusterName>"
	
	$undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
	$undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"
	
	$undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }
	
	Use-AzureHDInsightCluster $clusterName
	
	$defines = @{}
	$defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

	Invoke-Hive -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"
 
## <a id="nextsteps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Blob-Speicher in HDInsight verwenden können und dass Blob-Speicherung ein fundamentaler Bestandteil von HDInsight ist. Dadurch können Sie skalierbare Datenerfassungslösungen mit langfristiger Archivierung in Azure Blob-Speicher aufbauen und HDInsight verwenden, um die Informationen innerhalb der gespeicherten Daten zu entsperren.

Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Hochladen von Daten in HDInsight][hdinsight-upload-data]
* [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
* [Verwenden von Pig mit HDInsight][hdinsight-use-pig]

[Powershell-install]: ../install-configure-powershell/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/

[Powershell-install]: ../install-configure-powershell/
[blob-storage-restAPI]: http://msdn.microsoft.com/de-de/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage-create-storage-account/

[img-hdi-powershell-blobcommands]: ./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png 
[img-hdi-quick-create]: ./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png  

<!--HONumber=42-->
