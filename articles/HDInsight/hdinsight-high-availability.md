<properties 
	pageTitle="Verfügbarkeit von Hadoop-Clustern in HDInsight | Microsoft Azure" 
	description="HDInsight stellt hochverfügbare und zuverlässige Cluster mit einem zusätzlichen Hauptknoten bereit." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="bradsev" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/19/2014" 
	ms.author="bradsev"/>


#Verfügbarkeit und Zuverlässigkeit von Hadoop-Clustern in HDInsight


Ein zweiter Hauptknoten wurde zu den von Azure HDInsight bereitgestellten Hadoop-Clustern hinzugefügt, um die Verfügbarkeit und Zuverlässigkeit des Diensts für die Verarbeitung von Arbeitsauslastungen zu verbessern. Standardimplementierungen von Hadoop-Clustern verfügen in der Regel nur über einen Hauptknoten. Diese Cluster dienen zur reibungslosen Verwaltung ausgefallener Workerknoten. Bei einem Ausfall eines Master-Diensts auf dem Hauptknoten würde jedoch das komplette Cluster ausfallen.

![Diagramm der äußerst zuverlässigen Hauptknoten in der HDInsight Hadoop-Implementierung.](http://i.imgur.com/jrUmrH4.png)

HDInsight eliminiert diese einzelne Fehlerquelle durch die Einführung eines zweiten Hauptknotens (Head Node1). [ZooKeeper](http://zookeeper.apache.org/)-Knoten (ZKs) wurden hinzugefügt, führen eine sogenannte Leader Election der Hauptknoten durch und teilen Workerknoten und Gateways (GWs) mit, wann diese auf den sekundären Hauptknoten (Head Node1) umschalten müssen, wenn der aktive Hauptknoten (Node0) inaktiv wird.


## Überprüfen des Dienststatus auf dem aktiven Hauptknoten ##
Um festzustellen, welcher Hauptknoten aktiv ist und den Status der dort laufenden Dienste zu prüfen, müssen Sie sich unter Verwendung des Remotedesktopprotokolls (RDP) mit dem Hadoop-Cluster verbinden. Da Remoteverbindungen zum Cluster in Azure standardmäßig deaktiviert sind, müssen sie zunächst aktiviert werden. Anweisungen zu den erforderlichen Schritten im Portal finden Sie unter [Herstellen einer Verbindung zu HDInsight-Clustern mit RDP](hdinsight-administer-use-management-portal.md#rdp). Sobald Sie eine Remoteverbindung zum Cluster hergestellt haben, doppelklicken Sie auf das Symbol **Hadoop-Dienst – Verfügbarkeitsstatus** auf dem Desktop, um zu erfahren, auf welchem Hauptknoten die Dienste Namenode, Jobtracker, Templeton, Oozieservice, Metastore und Hiveserver2 bzw. im Fall von HDI 3.0 die Dienste Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore und Hiveserver2 ausgeführt werden.

![](http://i.imgur.com/MYTkCHW.png)


## Zugreifen auf Protokolldateien auf dem sekundären Hauptknoten \\

Wenn der sekundäre Hauptknoten der aktive Hauptknoten ist, können Sie für den Zugriff auf Auftragsprotokolle auf dem sekundären Hauptknoten weiterhin die JobTracker-Benutzeroberfläche verwenden, ebenso wie Sie dies für den aktiven primären Knoten tun würden. Um auf JobTracker zuzugreifen, müssen Sie, wie im vorigen Abschnitt beschrieben, unter Verwendung von RDP eine Verbindung mit dem Hadoop-Cluster herstellen. Sobald Sie die Remoteverbindung mit dem Cluster hergestellt haben, doppelklicken Sie auf dem Desktop auf das Symbol **Hadoop-Namensknoten**, und klicken Sie auf die **NameNode-Protokolle**, um das Protokollverzeichnis auf dem sekundären Hauptknoten zu öffnen.

![](http://i.imgur.com/eL6jzgB.png)


## Konfigurieren der Größe des Hauptknotens ##
Hauptknoten werden standardmäßig als große VMs eingerichtet. Diese Größe ist angemessen für die Verwaltung der meisten im Cluster ausgeführten Hadoop-Jobs. In manchen Szenarien werden jedoch sehr große virtuelle Computer für die Hauptknoten benötigt. Dies ist z. B. dann der Fall, wenn das Cluster eine große Anzahl kleiner Oozie-Jobs verwalten muss.

Sehr große VMs können entweder mit Azure PowerShell-Cmdlets oder mit dem HDInsight SDK konfiguriert werden.

Die Erstellung und Bereitstellung eines Clusters mithilfe von Azure PowerShell ist unter [Verwalten von HDInsight mit PowerShell](hdinsight-administer-use-powershell.md) dokumentiert. Um einen sehr großen Hauptknoten zu konfigurieren, müssen Sie den `-HeadNodeVMSize ExtraLarge`-Parameter zu dem in diesem Code verwendeten `New-AzureHDInsightcluster`-Cmdlet hinzufügen.

    # Create a new HDInsight cluster in Azure PowerShell
	# Configured with an ExtraLarge head-node VM
    New-AzureHDInsightCluster -Name $clusterName -Location $location -HeadNodeVMSize ExtraLarge -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

Das Verfahren für das SDK ist ähnlich. Die Erstellung und Bereitstellung eines Clusters mithilfe des SDK ist unter [Verwenden von HDInsight mit dem .NET SDK](hdinsight-provision-clusters.md#sdk) dokumentiert. Um einen sehr großen Hauptknoten zu konfigurieren, müssen Sie den `HeadNodeSize = NodeVMSize.ExtraLarge`-Parameter der in diesem Code verwendeten `ClusterCreateParameters()`-Methode hinzufügen.

    # Create a new HDInsight cluster with the HDInsight SDK
	# Configured with an ExtraLarge head-node VM
    ClusterCreateParameters clusterInfo = new ClusterCreateParameters()
    {
    Name = clustername,
    Location = location,
    HeadNodeSize = NodeVMSize.ExtraLarge,
    DefaultStorageAccountName = storageaccountname,
    DefaultStorageAccountKey = storageaccountkey,
    DefaultStorageContainer = containername,
    UserName = username,
    Password = password,
    ClusterSizeInNodes = clustersize
    };


**Referenzen**

- [ZooKeeper](http://zookeeper.apache.org/)
- [Herstellen einer Verbindung zu HDInsight-Clustern mit RDP](hdinsight-administer-use-management-portal.md#rdp)
- [Verwenden des HDInsight .NET SDK](hdinsight-provision-clusters.md#sdk) 







 

<!---HONumber=62-->