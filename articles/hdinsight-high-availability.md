<properties urlDisplayName="HDInsight High Availability" pageTitle="Verfügbarkeit von Hadoop-Clustern in HDInsight | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight deploys highly available and reliable clusters." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Availability of Hadoop clusters in HDInsight" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="multiple" ms.topic="article" ms.date="11/10/2014" ms.author="bradsev" />


#Verfügbarkeit und Zuverlässigkeit von Hadoop-Clustern in HDInsight

## Einführung
Ein zweiter Stammknoten wurde zu den Hadoop-Clustern in HDInsight hinzugefügt, um die Verfügbarkeit und Zuverlässigkeit des Diensts für die Verarbeitung von Arbeitslasten zu verbessern. Standardimplementierungen von Hadoop-Clustern verfügen in der Regel nur über einen Hauptknoten. Diese Cluster dienen zur reibungslosen Verwaltung ausgefallener Arbeiterknoten. Bei einem Ausfall eines Master-Diensts auf dem Stammknoten würde jedoch das komplette Cluster ausfallen. 

![](http://i.imgur.com/jrUmrH4.png)

HDInsight eliminiert diesen kritischen Ausfallpunkt durch die Einführung eines zweiten Stammknotens (Head Node1). [ZooKeeper][zookeeper]-Knoten (ZKs) wurden hinzugefügt, führen eine sogenannte Leader Election der Stammknoten durch und teilen Arbeiterknoten und Gateways (GWs) mit, wann diese auf den sekundären Stammknoten (Head Node1) umschalten müssen, wenn der aktive Stammknoten (HeadNode0) inaktiv wird.


## Überprüfen des Dienststatus auf dem aktiven Stammknoten
Um festzustellen, welcher Stammknoten aktiv ist und den Status der dort laufenden Dienste zu prüfen, müssen Sie sich über das Remotedesktopprotokoll (RDP) mit dem Hadoop-Cluster verbinden. Remoteverbindungen zum Cluster sind in Azure standardmäßig deaktiviert und müssen zunächst aktiviert werden. Anweisungen zu den erforderlichen Schritten im Portal finden Sie unter [Herstellen einer Verbindung zu HDInsight-Clustern mit RDP].(../hdinsight-administer-use-management-portal/#rdp)
Sobald Sie eine Remoteverbindung zum Cluster hergestellt haben, doppelklicken Sie auf das Symbol **Hadoop-Dienst - Verfügbarkeitsstatus** auf dem Desktop, um zu erfahren, auf welchem Stammknoten die Dienste, Jobtracker, Templeton, Oozieservice, Metastore und Hiveserver2 bzw. im Fall von HDI 3.0 die Dienste Namenode, Resource Manager, History Server, Templeton, Oozieservice, Metastore und Hiveserver2 ausgeführt werden.

![](http://i.imgur.com/MYTkCHW.png)


## Zugreifen auf Protokolldateien auf dem sekundären Stammknoten

Für den Zugriff auf Auftragsprotokolle auf dem sekundären Stammknoten können Sie weiterhin die JobTracker-Benutzeroberfläche verwenden, ebenso wie Sie dies für den aktiven primären Knoten tun würden. Um auf den Job Tracker zuzugreifen, müssen Sie sich wie im vorigen Abschnitt beschrieben per Remotedesktopprotokoll (RDP) mit dem Hadoop-Cluster verbinden. Sobald Sie mit dem Cluster verbunden sind, doppelklicken Sie auf das Symbol **Hadoop-Namensknoten** auf dem Desktop, und klicken Sie auf die **NameNode-Protokolle**, um das Protokollverzeichnis auf dem sekundären Stammknoten zu öffnen.

![](http://i.imgur.com/eL6jzgB.png)


## Konfigurieren der Größe des Stammknotens
Stammknoten werden standardmäßig als große VMs eingerichtet. Diese Größe ist angemessen für die Verwaltung der meisten im Cluster ausgeführten Hadoop-Jobs. Manche Szenarien benötigen jedoch sehr große virtuelle Computer für ihre Stammknoten. Dies ist z. B. dann der Fall, wenn das Cluster eine große Anzahl kleiner Oozie-Jobs verwalten muss. 

Sehr große VMs können entweder über Azure PowerShell-Cmdlets oder mit dem HDInsight SDK konfiguriert werden.

Die Erstellung und Bereitstellung eines Clusters mithilfe von PowerShell ist unter [Verwalten von HDInsight mit PowerShell] dokumentiert(../hdinsight-administer-use-powershell/). Um einen sehr großen Stammknoten zu konfigurieren, müssen Sie den "-HeadNodeVMSize ExtraLarge"-Parameter dem in diesem Code verwendeten Cmdlet "New-AzureHDInsightcluster" hinzufügen.

    # Create a new HDInsight cluster in Azure PowerShell
	# Configured with an ExtraLarge Headnode VM
    New-AzureHDInsightCluster -Name $clusterName -Location $location -HeadNodeVMSize ExtraLarge -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

Das Verfahren für das SDK ist ähnlich. Die Erstellung und Bereitstellung eines Clusters mithilfe des SDK ist unter [Verwenden des HDInsight .NET SDK] dokumentiert(../hdinsight-provision-clusters/#sdk). Um einen sehr großen Stammknoten zu konfigurieren, müssen Sie den "HeadNodeSize = NodeVMSize.ExtraLarge"-Parameter der in diesem Code verwendeten "ClusterCreateParameters()"-Methode hinzufügen.

    # Create a new HDInsight cluster with the HDInsight SDK
	# Configured with an ExtraLarge Headnode VM
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

- [ZooKeeper][zookeeper]
- [Herstellen einer Verbindung zu HDInsight-Clustern mit RDP](../hdinsight-administer-use-management-portal/#rdp)
- [Verwenden des HDInsight .NET SDK](../hdinsight-provision-clusters/#sdk) 


[zookeeper]: http://zookeeper.apache.org/ 







<!--HONumber=35.1-->
