<properties 
	pageTitle="Abfragen großer Datenmengen aus Hadoop-kompatiblen Blob-Speicher | Azure" 
	description="HDInsight verwendet Hadoop-kompatiblen Blob-Speicher als Big Data-Speicher für HDFS. Erfahren Sie, wie vom Blob-Speicher abfragen und die Ergebnisse der Analyse speichern." 
	services="hdinsight,storage" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="jgao"/>


#Verwenden von Azure Blob-Speicher mit HDInsight

Kostengünstiger Azure-Blob-Speicher stellt eine robuste, universelle Hadoop-kompatible Speicherlösung dar, die problemlos mit HDInsight integriert werden kann. Über eine HDFS-Schnittstelle (Hadoop Distributed File System) können sämtliche Komponenten in HDInsight direkt mit den Daten im Azure-Blob-Speicher arbeiten. In diesem Lernprogramm erfahren Sie, wie Sie einen Container für Blob-Speicher einrichten und dann die darin enthaltenen Daten adressieren.

Die Speicherung von Daten in Azure-Blob-Speicher sorgt dafür, dass die HDInsight-Cluster, die für Berechnungen verwendet werden, sicher gelöscht werden können, ohne Benutzerdaten zu verlieren.

> [AZURE.NOTE] In der HDInsight-Clusterversion 3.0 wird keine *asv://*-Syntax unterstützt. Dies bedeutet, dass alle an einen HDInsight-Cluster der Version 3.0 übermittelten Aufträge, die explizit die "asv://"-Syntax verwenden, fehlschlagen. Stattdessen sollte die *wasb://*-Syntax verwendet werden. An HDInsight-Cluster der Version 3.0 gesendete Aufträge, die mithilfe eines vorhandenen Metastores erstellt wurden, der explizite Verweise auf Ressourcen mit der "asv://"-Syntax enthält, schlagen ebenfalls fehl. Diese Metastores müssen mit der "wasb://"-Syntax neu erstellt werden, um die Ressourcen zu adressieren.

> HDInsight unterstützt aktuell nur Blockblobs.

> Die meisten HDFS-Befehle (wie z. B. <b>ls</b>, <b>copyFromLocal</b> und <b>mkdir</b>) funktionieren weiterhin wie erwartet. Nur die für die native HDFS-Implementierung (als DFS bezeichnet) spezifischen Befehle, wie <b>fschk</b> und <b>dfsadmin</b> zeigen in Azure-Blob-Speicher ein anderes Verhalten.

Informationen zur Bereitstellung eines HDInsight-Clusters finden Sie unter [Erste Schritte mit HDInsight][hdinsight-get-started] oder [Bereitstellen von HDInsight-Clustern][hdinsight-provision].


##<a id="architecture"></a>HDInsight-Speicherarchitektur
Das folgende Diagramm bietet einen zusammenfassenden Überblick über die HDInsight-Speicherarchitektur.

![Hadoop-Cluster in HDInsight bieten Zugriff auf große Datenmengen in kostengünstigen und skalierbaren Hadoop-kompatiblen Azure-Blob-Speichern in der Cloud und können diese speichern.](./media/hdinsight-use-blob-storage/HDI.ASVArch.png "HDInsight-Speicherarchitektur")
  
HDInsight bietet Zugang zum verteilten Dateisystem, das lokal an die Rechenknoten angefügt ist. Auf dieses Dateisystem kann über den vollqualifizierten URI zugegriffen werden, z. B.:

	hdfs://<namenodehost>/<path>

Zusätzlich bietet HDInsight die Möglichkeit, auf die in Azure-Blob-Speicher gespeicherten Daten zuzugreifen. Die Syntax ist:

	wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


Hadoop unterstützt eine Art Standarddateisystem. Das Standarddateisystem impliziert ein Standardschema und eine Standardautorität. Es kann auch zur Auflösung relativer Pfade verwendet werden. Während des Prozesses zur Bereitstellung von HDInsight werden ein Azure-Speicherkonto und ein bestimmter Azure-Blob-Speichercontainer aus diesem Konto als Standarddateisystem festgelegt.

Zusätzlich zu diesem Speicherkonto können Sie während des Bereitstellungsprozesses weitere Speicherkonten aus demselben Azure-Abonnement oder aus anderen Azure-Abonnements hinzufügen. Informationen zum Hinzufügen zusätzlicher Speicherkonten finden Sie unter [Bereitstellen von HDInsight-Clustern][hdinsight-provision].

- **Container in Speicherkonten, die mit dem Cluster verbunden sind:** Da der Kontoname und der Kontoschlüssel in *core-site.xml* gespeichert sind, haben Sie vollständigen Zugriff auf die Blobs in diesen Containern.
- **Öffentliche Container oder öffentliche Blobs in Speicherkonten, die NICHT mit einem Cluster verbunden sind:** Sie haben schreibgeschützten Zugriff auf die Blobs in den Containern.

	> [AZURE.NOTE] Öffentliche Container erlauben das Abrufen einer Liste aller im Container verfügbaren Blobs und der Container-Metadaten. Auf öffentliche Blobs haben Sie nur Zugriff, wenn Sie die exakte URL kennen. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">Beschränken des Zugriffs auf Container und Blobs</a>.

- **Private Container in Speicherkonten, die NICHT mit einem Cluster verbunden sind:** Sie können nicht auf die Blobs in den Containern zugreifen, es sei denn, Sie definieren beim Senden der WebHCat-Aufträge das Speicherkonto. Dies wird weiter unten im Artikel erläutert.


Das beim Einrichtungsprozess definierte Speicherkonto und die Schlüssel werden in "%HADOOP_HOME%/conf/core-site.xml" gespeichert. Standardmäßig verwendet HDInsight die in der Datei core-site.xml definierten Speicherkonten. Die Datei "core-site.xml" sollte nicht manuell bearbeitet werden, da der Cluster-Hauptknoten (Master) jederzeit neu aus einem Abbild erstellt oder migriert werden kann und die Änderungen an den Dateien in diesem Fall verloren gehen.

Verschiedene WebHCat-Aufträge, darunter Hive, MapReduce, Hadoop Streaming und Pig, können eine Beschreibung von Speicherkonten und Metadaten enthalten. (Dies funktioniert momentan nur für Pig und Speicherkonten, nicht für Metadaten.) Im Abschnitt [Blob-Zugriff über PowerShell](#powershell) dieses Artikels finden Sie ein Beispiel für dieses Feature. Weitere Informationen finden Sie unter [Verwenden eines HDInsight-Clusters mit alternativen Speicherkonten und Metastores](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

In Blob-Speichercontainern werden Daten als Schlüssel/Wert-Paare gespeichert, und es gibt keine Verzeichnishierarchie. Allerdings kann im Schlüsselnamen ein Schrägstrich (/) verwendet werden, damit es so aussieht, als wäre eine Datei in einer Verzeichnisstruktur gespeichert. Der Schlüssel eines Blobs kann z. B. *input/log1.txt* heißen. Es existiert zwar in Wirklichkeit kein Verzeichnis namens *input*, aber aufgrund des Schrägstrichs im Schlüsselnamen sieht dieser aus wie ein Dateipfad.










##<a id="benefits"></a>Vorteile von Azure-Blob-Speicher
Der Leistungsaufwand, der damit verbunden ist, dass die Rechencluster- und Speicherressourcen sich nicht am selben Ort befinden, wird dadurch abgeschwächt, dass die Cluster nahe an den Speicherkontoressourcen im Azure-Datencenter bereitgestellt werden, wo das Hochgeschwindigkeits-Netzwerk den Zugriff auf die Daten im Azure-Blob-Speicher für die Rechenknoten sehr effizient macht.

Die Speicherung von Daten in Azure-Blob-Speicher statt in HDFS hat mehrere Vorteile:

* **Datenfreigabe und -wiederverwendung:** Die Daten in HDFS befinden sich innerhalb des Rechenclusters. Nur die Anwendungen, die Zugriff auf den Rechencluster haben, können die Daten über die HDFS-APIs verwenden. Auf die Dateien in Azure-Blob-Speicher kann entweder über die HDFS-APIs oder über die [Blob-Speicher-REST-APIs][blob-storage-restAPI] zugegriffen werden. Somit kann eine größere Menge von Anwendungen (darunter andere HDInsight-Cluster) und Tools verwendet werden, um die Daten zu produzieren und abzurufen.
* **Datenarchivierung:** Die Speicherung von Daten im Azure-Blob-Speicher sorgt dafür, dass die HDInsight-Cluster, die für Berechnungen verwendet werden, sicher gelöscht werden können, ohne Benutzerdaten zu verlieren. 
* **Datenspeicherkosten:** Die langfristige Datenspeicherung in HDFS ist kostspieliger als die Datenspeicherung in Azure-Blob-Speicher, da die Kosten eines Rechenclusters höher liegen als diejenigen eines Azure-Blob-Speichercontainers. Da die Daten nicht für jede Erzeugung eines neuen Rechenclusters neu geladen werden, sparen Sie auch Kosten für das Laden von Daten.
* **Flexible horizontale Skalierung:** Auch wenn HDFS ein horizontal skaliertes Dateisystem bietet, wird die Skalierung durch die Anzahl der Knoten bestimmt, die Sie für Ihren Cluster bereitstellen. Eine Änderung der Skalierung kann weitaus schwieriger werden, als auf die flexiblen Speicherkapazitäten von Azure-Blob-Speicher zu vertrauen, über die Sie automatisch verfügen.
* **Georeplikation:** Ihre Azure-Blob-Speichercontainer können über das Azure-Portal georepliziert werden. Obwohl Sie dadurch von geografischer Wiederherstellung und Datenredundanz profitieren, wirkt sich ein Ausfall des georeplizierten Standorts stark auf Ihre Leistung aus und kann zusätzliche Kosten nach sich ziehen. Daher empfehlen wir, die Georeplikation mit Bedacht auszuwählen und nur dann anzuwenden, wenn der Wert der Daten die zusätzlichen Kosten rechtfertigt.

Bestimmte MapReduce-Aufträge und -Pakete können zu Zwischenergebnissen führen, die Sie eher nicht im Azure-Blob-Speicher speichern möchten. In diesem Fall können Sie sich entscheiden, die Dateien im lokalen HDFS zu speichern. Tatsächlich verwendet HDInsight DFS für einige dieser Zwischenergebnisse in Hive-Jobs und anderen Prozessen.





##<a id="preparingblobstorage"></a>Vorbereiten eines Containers für Azure-Blob-Speicher
Um Blobs zu verwenden, erstellen Sie zuerst ein [Azure-Speicherkonto][azure-storage-create]. Dabei geben Sie ein Azure-Datencenter an, in dem die mithilfe dieses Kontos erstellten Objekte gespeichert werden. Der Cluster und das Speicherkonto müssen im selben Datencenter gehostet werden. Die Hive-Metastore-SQL Server-Datenbank und Oozie-Metastore-SQL Server-Datenbank müssen sich ebenfalls im gleichen Datencenter befinden.

Ein Blob gehört unabhängig davon, wo es sich befindet, stets zu einem Container in Ihrem Azure-Speicherkonto. Dieser Container kann ein außerhalb von HDInsight erstelltes Blob sein, oder es handelt sich um einen Container, der für einen HDInsight-Cluster erstellt wird.

Geben Sie einen Standard-Speichercontainer nicht für mehrere HDInsight-Cluster frei. Wenn Sie einen freigegebenen Container verwenden müssen, um Zugriff auf Daten für mehrere HDInsight-Cluster bereitstellen zu können, sollten Sie diesen als zusätzliches Speicherkonto in der Clusterkonfiguration hinzufügen. Weitere Informationen finden Sie unter [Bereitstellen von HDInsight-Clustern][hdinsight-provision]. Sie können jedoch einen Standard-Speichercontainer wiederverwenden, nachdem der ursprüngliche HDInsight-Cluster gelöscht wurde. Für HBase-Cluster können Sie das HBase-Tabellenschema und die Daten beibehalten, indem Sie einen neuen HBase-Cluster mithilfe des Standard-Blob-Speichercontainers bereitstellen, der von einem gelöschten HBase-Cluster verwendet wird.


###Erstellen eines Blob-Containers für HDInsight mithilfe des Azure-Portals

Beim Bereitstellen eines HDInsight-Clusters über das Azure-Portal gibt es zwei Optionen: **Schnellerfassung** und **Benutzerdefiniert erstellen**. Für die Schnellerfassung muss das Azure-Speicherkonto bereits vorher erstellt worden sein. Informationen dazu finden Sie unter [Erstellen eines Speicherkontos][azure-storage-create].

Wenn Sie die Option "Schnellerfassung" verwenden, können Sie ein vorhandenes Speicherkonto auswählen. Während des Bereitstellungsprozesses wird ein neuer Container mit dem Namen des HDInsight-Clusters erstellt. Wenn ein Container mit demselben Namen bereits vorhanden ist, wird <clusterName>-<x> verwendet. Beispiel: *myHDIcluster-1*. Dieser Container wird als Standard-Dateisystem verwendet.

![Verwenden der Schnellerfassung für einen neuen Hadoop-Cluster in HDInsight im Azure-Portal][img-hdi-quick-create]
 
Mit der benutzerdefinierten Erstellung können Sie für das Standard-Speicherkonto eine der folgenden Optionen auswählen:

- Vorhandenen Speicher verwenden
- Neuen Speicher erstellen
- "Speicher aus anderem Abonnement verwenden"

Sie haben außerdem die Auswahl, einen neuen Blob-Container zu erstellen oder einen vorhandenen zu verwenden.
 
![Option zum Verwenden eines vorhandenen Speicherkontos für Ihr HDInsight-Cluster][img-hdi-custom-create-storage-account]
  




### Erstellen eines Containers mit Azure PowerShell
[Azure PowerShell][powershell-install] kann zur Erstellung von Containern verwendet werden. Das folgende PowerShell-Skript zeigt, wie Sie einen Blob-Container für ein vorhandenes Azure-Speicherkonto erstellen:

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


##<a id="addressing"></a>Adressdateien in Azure-Blob-Speicher

Das URI-Schema für den Dateizugriff im Azure-Blob-Speicher lautet:

	wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [AZURE.NOTE]Die Syntax zum Ansprechen der Dateien im Speicheremulator (ausgeführt im HDInsight-Emulator) lautet <i>wasb://&lt;ContainerName&gt;@storageemulator</i>.



Das URI-Schema bietet sowohl unverschlüsselten Zugriff (mit dem Präfix *wasb:*) als auch SSL-verschlüsselten Zugriff (mit *wasbs*). Wir empfehlen die Verwendung von *wasbs*, wann immer es möglich ist, selbst für den Zugriff auf Daten, die sich im selben Azure-Datencenter befinden.
	
&lt;BlobStorageContainerName&gt; ist der Name des Containers im Azure-Blob-Speicher. &lt;StorageAccountName&gt; ist der Name des Azure-Speicherkontos. Ein vollqualifizierter Domänenname (FQDN) ist erforderlich.
	
Wenn weder &lt;BlobStorageContainerName&gt; noch &lt;StorageAccountName&gt; angegeben ist, wird das Standarddateisystem verwendet. Für die Dateien im Standarddateisystem können Sie einen relativen oder einen absoluten Pfad verwenden. Auf die Datei *hadoop-mapreduce-examples.jar*, die sich in HDInsight-Clustern befindet, kann z. B. mit einem der folgenden Befehle verwiesen werden:

	wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	wasb:///example/jars/hadoop-mapreduce-examples.jar
	/example/jars/hadoop-mapreduce-examples.jar
	
> [AZURE.NOTE]In HDInsight-Clustern der Version 2.1 und 1.6 lautet der Dateiname <i>hadoop-examples.jar</i>.


&lt;path&gt; ist der HDFS-Pfadname der Datei oder des Verzeichnisses. Da Container im Azure-Blob-Speicher nur ein Schlüssel-Wert-Paar sind, gibt es kein echtes hierarchisches Dateisystem. Ein Schrägstrich (/) in einem Blob-Schlüssel wird als Verzeichnis-Trennzeichen interpretiert. Der Blob-Name für *hadoop-mapreduce-examples.jar* ist z. B.:

	example/jars/hadoop-mapreduce-examples.jar
	

##<a id="powershell"></a>Blob-Zugang über Azure PowerShell

Informationen zur Installation und Konfiguration von Azure PowerShell auf Ihrer Arbeitsstation finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install] Sie können das Windows PowerShell-Konsolenfenster oder Windows PowerShell-ISE verwenden, um Azure PowerShell-Cmdlets auszuführen.

Verwenden Sie den folgenden Befehl, um die Blob-bezogenen Cmdlets aufzulisten:

	Get-Command *blob*

![Liste der Blob-bezogenen PowerShell-Cmdlets][img-hdi-powershell-blobcommands]


**Beispiel für die Verwendung von Azure PowerShell zum Hochladen einer Datei**

Siehe [Hochladen von Daten in HDInsight][hdinsight-upload-data].

**Beispiel für die Verwendung von Azure PowerShell zum Herunterladen einer Datei**

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

**Beispiel für die Verwendung von Azure PowerShell zum Löschen einer Datei**

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
	

**Beispiel für die Verwendung von Azure PowerShell zum Auflisten der Dateien in einem Ordner**

Das folgende Skript listet die Dateien in einem Ordner auf. (Das nächste Beispiel ruft das **Invoke-Hive**-Cmdlet auf, um den Befehl **dfs ls** zum Auflisten eines Ordners auszuführen.)

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

**Beispiel für die Verwendung von Azure PowerShell zum Zugreifen auf ein nicht definiertes Speicherkonto**
	
Dieses Beispiel listet einen Ordner aus einem Speicherkonto auf, der nicht beim Einrichtungsprozess definiert wurde. $clusterName = "<HDInsightClusterName>"
	
	$undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
	$undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"
	
	$undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }
	
	Use-AzureHDInsightCluster $clusterName
	
	$defines = @{}
	$defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

	Invoke-Hive -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"
 
##<a id="nextsteps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie Azure-Blob-Speicher in HDInsight verwenden können und dass Azure-Blob-Speicherung ein fundamentaler Bestandteil von HDInsight ist. Dadurch können Sie skalierbare Datenerfassungslösungen mit langfristiger Archivierung in Azure-Blob-Speicher aufbauen und HDInsight verwenden, um die Informationen innerhalb der gespeicherten Daten zu entsperren.

Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Hochladen von Daten in HDInsight][hdinsight-upload-data]
* [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
* [Verwenden von Pig mit HDInsight][hdinsight-use-pig]

[Powershell-install]: install-configure-powershell.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-get-started.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[Powershell-install]: install-configure-powershell.md
[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png

<!--HONumber=54-->