<properties
	pageTitle="Abfragen von Daten aus HDFS-kompatiblen Blobspeicher | Microsoft Azure"
	description="HDInsight verwendet Blobspeicher als Big Data-Speicher für HDFS. Erfahren Sie, wie Sie Daten aus Blobspeicher abfragen und die Ergebnisse der Analyse speichern."
	keywords="blob storage,hdfs,structured data,unstructured data"
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
	ms.date="06/10/2015"
	ms.author="jgao"/>


#Verwenden des HDFS-kompatiblen Azure-Blobspeichers mit Hadoop in HDInsight

In diesem Lernprogramm erfahren Sie, wie Sie kostengünstigen Azure-Blobspeicher mit HDInsight verwenden, Azure-Speicherkonten und Blobspeichercontainer erstellen und diese dann mit Daten füllen.

Azure-Blobspeicher stellt eine robuste, universelle Speicherlösung dar, die problemlos mit HDInsight integriert werden kann. Über eine HDFS-Schnittstelle (Hadoop Distributed File System) können sämtliche Komponenten in HDInsight direkt mit strukturierten oder unstrukturierten Daten im Blobspeicher arbeiten.

Die Speicherung von Daten im Blobspeicher sorgt dafür, dass die HDInsight-Cluster, die für Berechnungen verwendet werden, sicher gelöscht werden können, ohne Benutzerdaten zu verlieren.

> [AZURE.NOTE]Die *asv://*-Syntax wird in HDInsight-Clustern der Version 3.0 nicht unterstützt. Dies bedeutet, dass alle an einen HDInsight-Cluster der Version 3.0 übermittelten Aufträge, die explizit die *asv://*-Syntax verwenden, fehlschlagen. Stattdessen sollte die *wasb://*-Syntax verwendet werden. An HDInsight-Cluster der Version 3.0 gesendete Aufträge, die mithilfe eines vorhandenen Metastores erstellt wurden, der explizite Verweise auf Ressourcen mit der asv://-Syntax enthält, schlagen ebenfalls fehl. Diese Metastores müssen mit der wasb://-Syntax neu erstellt werden, um die Ressourcen zu adressieren.

> HDInsight unterstützt aktuell nur Blockblobs.

> Die meisten HDFS-Befehle (z. B. <b>ls</b>, <b>copyFromLocal</b> und <b>mkdir</b>) funktionieren weiterhin wie erwartet. Nur die für die native HDFS-Implementierung (als DFS bezeichnet) spezifischen Befehle wie <b>fschk</b> und <b>dfsadmin</b> zeigen im Azure-Blobspeicher ein anderes Verhalten.

Informationen zur Bereitstellung eines HDInsight-Clusters finden Sie unter [Erste Schritte mit HDInsight][hdinsight-get-started] oder [Bereitstellen von HDInsight-Clustern][hdinsight-provision].


##<a id="architecture"></a>HDInsight-Speicherarchitektur
Das folgende Diagramm bietet einen zusammenfassenden Überblick über die HDInsight-Speicherarchitektur.

![Hadoop-Cluster verwenden die HDFS-API, um auf strukturierte und unstrukturierte Daten im Blobspeicher zuzugreifen und diese zu speichern.](./media/hdinsight-hadoop-use-blob-storage/HDI.WASB.Arch.png "HDInsight-Speicherarchitektur")

HDInsight bietet Zugang zum verteilten Dateisystem, das lokal an die Rechenknoten angefügt ist. Auf dieses Dateisystem kann über den vollständig qualifizierten URI zugegriffen werden. Zum Beispiel:

	hdfs://<namenodehost>/<path>

Zusätzlich bietet HDInsight die Möglichkeit, auf die im Azure-Blobspeicher gespeicherten Daten zuzugreifen. Die Syntax ist:

	wasb[s]://<containername>@<accountname>.blob.core.windows.net/<path>


Hadoop unterstützt eine Variante des Standarddateisystems. Das Standarddateisystem gibt ein Standardschema und eine Standardautorität vor. Es kann auch zur Auflösung relativer Pfade verwendet werden. Während des Prozesses zur Bereitstellung von HDInsight werden ein Azure-Speicherkonto und ein bestimmter Azure-Blobspeichercontainer aus diesem Konto als Standarddateisystem festgelegt.

Zusätzlich zu diesem Speicherkonto können Sie während des Bereitstellungsprozesses weitere Speicherkonten aus demselben oder einem anderen Azure-Abonnements hinzufügen. Informationen zum Hinzufügen zusätzlicher Speicherkonten finden Sie unter [Bereitstellen von HDInsight-Clustern][hdinsight-provision].

- **Container in Speicherkonten, die zu einem Cluster verbunden sind:** Da dem Cluster Kontoname und Schlüssel bei der Bereitstellung zugeordnet werden, haben Sie vollständigen Zugriff auf die Blobs in diesen Containern.

- **Öffentliche Container oder öffentliche Blobs in Speicherkonten, die NICHT mit einem Cluster verbunden sind:** Sie haben schreibgeschützten Zugriff auf die Blobs in den Containern.

	> [AZURE.NOTE]> Öffentliche Container erlauben das Abrufen einer Liste aller im Container verfügbaren Blobs und der Container-Metadaten. Auf öffentliche Blobs haben Sie nur Zugriff, wenn Sie die exakte URL kennen. Weitere Informationen finden Sie unter <a href="http://msdn.microsoft.com/library/windowsazure/dd179354.aspx">Beschränken des Zugriffs auf Container und Blobs</a>.

- **Private Container in Speicherkonten, die NICHT mit einem Cluster verbunden sind:** Sie können nicht auf die Blobs in den Containern zugreifen, es sei denn Sie definieren beim Senden der WebHCat-Aufträge das Speicherkonto. Dies wird weiter unten im Artikel erläutert.


Das beim Einrichtungsprozess definierte Speicherkonto und die Schlüssel werden in %HADOOP_HOME%/conf/core-site.xml auf den Clusterknoten gespeichert. Standardmäßig verwendet HDInsight die in der Datei core-site.xml definierten Speicherkonten. Die Datei core-site.xml sollte nicht manuell bearbeitet werden, da der Cluster-Stammknoten (Master) jederzeit neu aus einem Abbild erstellt oder migriert werden kann, und die Änderungen an den Dateien in diesem Fall verloren gehen.

Verschiedene WebHCat-Aufträge, darunter Hive, MapReduce, Hadoop Streaming und Pig, können eine Beschreibung von Speicherkonten und Metadaten enthalten. (Dies funktioniert momentan nur für Pig und Speicherkonten, nicht für Metadaten.) Im Abschnitt [Blobzugriff über Azure PowerShell](#powershell) dieses Artikels finden Sie ein Beispiel für dieses Feature. Weitere Informationen finden Sie unter [Verwenden eines HDInsight-Clusters mit alternativen Speicherkonten und Metastores](http://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Blobspeicher kann für strukturierte und unstrukturierte Daten verwendet werden. In Blob-Speichercontainern werden Daten als Schlüssel/Wert-Paare gespeichert, und es gibt keine Verzeichnishierarchie. Allerdings kann im Schlüsselnamen der Schrägstrich (/) verwendet werden, damit es so aussieht, als wäre eine Datei in einer Verzeichnisstruktur gespeichert. Der Schlüssel eines Blobs kann z. B. *input/log1.txt* heißen. Das Verzeichnis *input* existiert zwar nicht, wegen des Schrägstrichs (/) im Schlüsselnamen sieht es jedoch so aus, als gäbe es einen Dateipfad.

###<a id="benefits"></a>Vorteile von Blobspeicher
Der Leistungsaufwand, der damit verbunden ist, dass die Rechencluster und Speicherressourcen nicht am selben Ort vorliegen, wird dadurch abgeschwächt, dass die Cluster nahe an den Speicherkontoressourcen im Azure-Datencenter bereitgestellt werden, wo das Hochgeschwindigkeitsnetzwerk den Zugriff auf die Daten im Azure-Blobspeicher für die Rechenknoten sehr effizient macht.

Die Speicherung von Daten im Azure-Blobspeicher statt im HDFS hat mehrere Vorteile:

* **Datenfreigabe und -wiederverwendung:** Die Daten im HDFS befinden sich innerhalb des Rechenclusters. Nur die Anwendungen, die Zugriff auf den Rechencluster haben, können die Daten über die HDFS-API verwenden. Auf die Dateien im Azure-Blobspeicher kann entweder über die HDFS-APIs oder über die [Blobspeicher-REST-APIs][blob-storage-restAPI] zugegriffen werden. Somit kann eine größere Menge von Anwendungen (darunter andere HDInsight-Cluster) und Tools verwendet werden, um die Daten zu produzieren und abzurufen.
* **Datenarchivierung:** Die Speicherung von Daten im Azure-Blobspeicher sorgt dafür, dass die HDInsight-Cluster, die für Berechnungen verwendet werden, sicher gelöscht werden können, ohne Benutzerdaten zu verlieren.
* **Datenspeicherkosten:**Die langfristige Datenspeicherung in DFS ist kostspieliger als die Datenspeicherung im Azure-Blobspeicher, da die Kosten eines Rechenclusters höher liegen als diejenigen eines Azure-Blobspeichercontainers. Da die Daten nicht für jede Erzeugung eines neues Rechenclusters neu geladen werden, sparen Sie auch Kosten für das Laden von Daten.
* **Elastische horizontale Skalierung:** Obwohl Ihnen HDFS ein horizontal skaliertes Dateisystem bietet, wird die Skalierung durch die Anzahl der Knoten bestimmt, die Sie für Ihren Cluster bereitstellen. Eine Änderung der Skalierung kann weitaus schwieriger werden, als auf die flexiblen Speicherkapazitäten zu vertrauen, die Ihnen der Azure-Blobspeicher automatisch bietet.
* **Georeplikation:** Ihre Azure Blobspeichercontainer können über das Azure-Portal georepliziert werden. Obwohl Sie dadurch von geographischer Wiederherstellung und Datenredundanz profitieren, wirkt sich ein Ausfall des georeplizierten Standorts schwer auf Ihre Leistung aus und kann zusätzliche Kosten nach sich ziehen. Deshalb empfehlen wir, die Georeplikation mit Bedacht auszuwählen und nur dann anzuwenden, wenn der Wert der Daten die zusätzlichen Kosten rechtfertigt.

Bestimmte MapReduce-Jobs und -Pakete können zu Zwischenergebnissen führen, die Sie nicht wirklich im Azure-Blobspeicher speichern möchten. In diesem Fall können Sie die Dateien auch im lokalen HDFS speichern. Tatsächlich verwendet HDInsight DFS für einige dieser Zwischenergebnisse in Hive-Jobs und anderen Prozessen.



##<a id="preparingblobstorage"></a>Erstellen eines Blobcontainers

Um Blobs zu verwenden, erstellen Sie zuerst ein [Azure-Speicherkonto][azure-storage-create]. Dabei geben Sie ein Azure-Datencenter an, in dem die mithilfe dieses Kontos erstellten Objekte gespeichert werden. Der Cluster und das Speicherkonto müssen sich im gleichen Datencenter befinden. Die Hive-Metastore-SQL Server-Datenbank und Oozie-Metastore-SQL Server-Datenbank müssen sich ebenfalls im gleichen Datencenter befinden.

Ein Blob gehört unabhängig davon, wo es sich befindet, stets zu einem Container Ihres Azure-Speicherkontos. Dieser Container kann ein außerhalb von HDInsight erstelltes Blob sein, oder es handelt sich um einen Container, der für einen HDInsight-Cluster erstellt wird.

Geben Sie einen Standardspeichercontainer nicht für mehrere HDInsight-Cluster frei. Wenn Sie einen freigegebenen Container benötigen, um Zugriff auf Daten für mehrere HDInsight-Cluster bereitzustellen, sollten Sie ihn der Clusterkonfiguration als zusätzliches Speicherkonto hinzufügen. Weitere Informationen finden Sie unter [Bereitstellen von HDInsight-Clustern][hdinsight-provision]. Einen Standardspeichercontainer können Sie jedoch auch wiederverwenden, wenn der ursprüngliche HDInsight-Cluster gelöscht wurde. Bei HBase-Clustern können Sie das HBase-Tabellenschema sowie die darin enthaltenen Daten sogar beibehalten, indem Sie einen neuen HBase-Cluster mit dem Standard-Blobspeichercontainer bereitstellen, der von einem gelöschten HBase-Cluster verwendet wurde.


###Verwenden des Azure-Portals

Zur Bereitstellung eines HDInsight-Clusters im Azure-Portal haben Sie zwei Optionen: **Schnellerstellung** und **Benutzerdefinierte Erstellung**. Für die Schnellerstellung muss das Azure-Speicherkonto bereits vorher erstellt worden sein. Informationen dazu finden Sie unter [Erstellen eines Speicherkontos][azure-storage-create].

Wenn Sie die Option "Schnellerstellung" verwenden, können Sie ein vorhandenes Speicherkonto auswählen. Während des Bereitstellungsprozesses wird ein neuer Container mit dem Namen des HDInsight-Clusters erstellt. Wenn ein Container mit demselben Namen bereits vorhanden ist, wird <clusterName>-<x> verwendet. Beispiel: *myHDIcluster-1*. Dieser Container wird als Standard-Dateisystem verwendet.

![Verwenden der Schnellerstellung für einen neuen Hadoop-Cluster in HDInsight im Azure-Portal.][img-hdi-quick-create]

Bei der benutzerdefinierten Erstellung können Sie für das Standardspeicherkonto eine der folgenden Optionen auswählen:

- Vorhandenen Speicher verwenden
- "Neuen Speicher erstellen"
- "Speicher aus anderem Abonnement verwenden"

Sie haben außerdem die Auswahl, einen neuen Container zu erstellen oder einen vorhandenen zu verwenden.

![Möglichkeit der Verwendung eines vorhandenen Speicherkontos für Ihren HDInsight-Cluster.][img-hdi-custom-create-storage-account]

###Verwenden der Azure-Befehlszeilenschnittstelle

Wenn Sie [die Befehlszeilenschnittstelle von Azure (Azure-CLI) installiert und konfiguriert](../xplat-cli.md) haben, können Sie zur Erstellung eines Speicherkontos und Containers den folgenden Befehl verwenden.

	azure storage account create <storageaccountname> --type LRS

> [AZURE.NOTE]Der Parameter `--type` gibt an, wie das Speicherkonto repliziert wird. Weitere Informationen finden Sie unter [Azure-Speicherreplikation](../storage-redundancy.md).

Sie werden zur Angabe der geografischen Region des Speicherkontos aufgefordert. Das Speicherkonto sollte in derselben Region erstellt werden, in der der HDInsight-Cluster erstellt werden soll.

Nach der Erstellung des Speicherkontos können Sie den Schlüssel des Speicherkontos mit dem folgenden Befehl abrufen:

	azure storage account keys list <storageaccountname>

Zum Erstellen eines Containers verwenden Sie den folgenden Befehl:

	azure storage container create <containername> --account-name <storageaccountname> --account-key <storageaccountkey>

###Verwenden von Azure PowerShell

Nach der [Installation und Konfiguration von Azure PowerShell][powershell-install] können Sie Speicherkonto und Container wie folgt an der Azure PowerShell-Eingabeaufforderung erstellen:

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


##<a id="addressing"></a>Adressdateien im Blobspeicher

Das URI-Schema für den Zugriff auf Dateien im Blobspeicher aus HDInsight ist:

	wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>


> [AZURE.NOTE]Die Syntax für den Zugriff auf die Dateien im Speicheremulator (ausgeführt im HDInsight-Emulator) lautet <i>wasb://&lt;ContainerName&gt;@storageemulator</i>.



Das URI-Schema bietet unverschlüsselten Zugriff (mit dem Präfix *wasb:*) wie auch SSL-verschlüsselten Zugriff (mit *wasbs*). Wir empfehlen die Verwendung von *wasbs*, selbst für den Zugriff auf Daten, die sich in Azure im selben Datencenter befinden.

&lt;BlobStorageContainerName&gt; ist der Name des Containers im Azure-Blobspeicher. &lt;StorageAccountName&gt; ist der Name des Azure-Speicherkontos. Ein vollqualifizierter Domänenname (FQDN) ist erforderlich.

Wenn weder &lt;BlobStorageContainerName&gt; noch &lt;StorageAccountName&gt; angegeben ist, wird das Standarddateisystem verwendet. Für die Dateien im Standarddateisystem können Sie relative oder absolute Pfade verwenden. Auf die Datei *hadoop-mapreduce-examples.jar*, die sich in HDInsight-Clustern befindet, kann z. B. mit einem der folgenden Befehle verwiesen werden:

	wasb://mycontainer@myaccount.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
	wasb:///example/jars/hadoop-mapreduce-examples.jar
	/example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE]In HDInsight-Clustern der Version 2.1 und 1.6 lautet der Dateiname <i>hadoop-examples.jar</i>.


&lt;path&gt; ist der HDFS-Pfadname der Datei oder des Verzeichnisses. Da es sich bei Containern im Azure-Blobspeicher lediglich um Schlüssel/Wert-Paare handelt, gibt es kein wirkliches hierarchisches Dateisystem. Ein Schrägstrich (/) in einem Blobschlüssel wird als Verzeichnistrennzeichen interpretiert. Der Blob-Name für *hadoop-mapreduce-examples.jar* ist z. B.:

	example/jars/hadoop-mapreduce-examples.jar

> [AZURE.NOTE]Wenn Blobs außerhalb von HDInsight verwendet werden, wird das WASB-Format von den meisten Dienstprogrammen nicht erkannt. Diese erwartet vielmehr ein grundlegendes Pfadformat wie `example/jars/hadoop-mapreduce-examples.jar`.

##<a id="azurecli"></a>Zugriff auf Blobs über die Azure-CLI

Verwenden Sie den folgenden Befehl, um die Blob-bezogenen Befehle aufzulisten:

	azure storage blob

**Beispiel für die Verwendung der Azure-CLI zum Hochladen einer Datei**

	azure storage blob upload <sourcefilename> <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Beispiel für die Verwendung der Azure-CLI zum Herunterladen einer Datei**

	azure storage blob download <containername> <blobname> <destinationfilename> --account-name <storageaccountname> --account-key <storageaccountkey>

**Beispiel für die Verwendung der Azure-CLI zum Löschen einer Datei**

	azure storage blob delete <containername> <blobname> --account-name <storageaccountname> --account-key <storageaccountkey>

**Beispiel für die Verwendung der Azure-CLI zum Auflisten von Dateien**

	azure storage blob list <containername> <blobname|prefix> --account-name <storageaccountname> --account-key <storageaccountkey>

##<a id="powershell"></a>Zugriff auf Blobs über Azure PowerShell

> [AZURE.NOTE]In diesem Abschnitt wird anhand eines einfachen Beispiels gezeigt, wie Sie mit PowerShell-Befehlen auf in Blobs gespeicherte Daten zugreifen. Ein komplexeres, speziell auf HDInsight zugeschnittenes Beispiel, das mehr Funktionen verwendet, finden Sie unter [HDInsight-Tools](https://github.com/Blackmist/hdinsight-tools).

Verwenden Sie den folgenden Befehl, um die Blob-bezogenen Cmdlets aufzulisten:

	Get-Command *blob*

![Liste der Blob-bezogenen PowerShell-Cmdlets.][img-hdi-powershell-blobcommands]


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


**Beispiel für die Verwendung von Azure PowerShell zum Auflisten der Dateien eines Ordners**

Das folgende Skript zeigt, wie die Dateien eines Ordners aufgelistet werden. (Das nächste Beispiel ruft das Cmdlet **Invoke-Hive** auf, mit dem der Befehl **dfs ls** zum Auflisten des Inhalts eines Ordners ausgeführt wird.)

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

**Beispiel für die Verwendung von Azure PowerShell zum Ausführen einer Hive-Abfrage mit einem nicht definierten Speicherkonto**

Dieses Beispiel zeigt, wie der Inhalt eines Ordners eines Speicherkontos aufgelistet wird, der beim Bereitstellungsprozess nicht definiert wurde. $clusterName = "<HDInsightClusterName>"

	$undefinedStorageAccount = "<UnboundedStorageAccountUnderTheSameSubscription>"
	$undefinedContainer = "<UnboundedBlobContainerAssociatedWithTheStorageAccount>"

	$undefinedStorageKey = Get-AzureStorageKey $undefinedStorageAccount | %{ $_.Primary }

	Use-AzureHDInsightCluster $clusterName

	$defines = @{}
	$defines.Add("fs.azure.account.key.$undefinedStorageAccount.blob.core.windows.net", $undefinedStorageKey)

	Invoke-Hive -Defines $defines -Query "dfs -ls wasb://$undefinedContainer@$undefinedStorageAccount.blob.core.windows.net/;"

##<a id="nextsteps"></a>Nächste Schritte

In diesem Artikel haben Sie gelernt, wie Sie HDFS-kompatiblen Azure-Blobspeicher in HDInsight verwenden und dass Azure-Blobspeicher ein fundamentaler Bestandteil von HDInsight ist. Dadurch können Sie skalierbare Datenerfassungslösungen mit langfristiger Archivierung im Azure-Blobspeicher aufbauen und HDInsight verwenden, um die Informationen innerhalb der gespeicherten strukturierten und unstrukturierten Daten zu entsperren.

Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
* [Hochladen von Daten in HDInsight][hdinsight-upload-data]
* [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
* [Verwenden von Pig mit HDInsight][hdinsight-use-pig]

[powershell-install]: ../install-configure-powershell.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: hdinsight-hadoop-tutorial-get-started-windows.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[blob-storage-restAPI]: http://msdn.microsoft.com/library/windowsazure/dd135733.aspx
[azure-storage-create]: ../storage-create-storage-account.md

[img-hdi-powershell-blobcommands]: ./media/hdinsight-hadoop-use-blob-storage/HDI.PowerShell.BlobCommands.png
[img-hdi-quick-create]: ./media/hdinsight-hadoop-use-blob-storage/HDI.QuickCreateCluster.png
[img-hdi-custom-create-storage-account]: ./media/hdinsight-hadoop-use-blob-storage/HDI.CustomCreateStorageAccount.png
 

<!---HONumber=62-->