<properties 
   pageTitle="Konfigurieren der HBase-Replikation zwischen zwei Datencentern | Microsoft Azure" 
   description="Erfahren Sie, wie Sie die HBase-Replikation zwischen zwei Datencentern konfigurieren, und lernen Sie Anwendungsfälle für die Clusterreplikation kennen." 
   services="hdinsight,virtual-network" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="07/08/2015"
   ms.author="jgao"/>

# Konfigurieren von HBase-Georeplikation in HDInsight

> [AZURE.SELECTOR]
- [Configure VPN connectivity](../hdinsight-hbase-geo-replication-configure-VNETs.md)
- [Configure DNS](hdinsight-hbase-geo-replication-configure-DNS.md)
- [Configure HBase replication](hdinsight-hbase-geo-replication.md) 
 
Erfahren Sie, wie Sie HBase-Replikation zwischen zwei Rechenzentren konfigurieren. Einige Anwendungsfälle für Clusterreplikation umfassen:

- Backup und Notfallwiederherstellung
- Daten-Aggregation
- Geografische Datenverteilung
- Erfassung von Onlinedaten kombiniert mit Offline-Datenanalysen

Clusterreplikation verwendet eine Source-Push-Methodologie. Ein HBase-Cluster kann eine Quelle oder ein Ziel sein oder beide Rollen gleichzeitig erfüllen. Replikation ist asynchron und das Ziel der Replikation ist letztendliche Konsistenz. Wenn die Quelle eine Bearbeitung auf eine Spaltenfamilie mit aktivierter Replikation erhält, wird diese Bearbeitung an alle Ziel-Cluster weitergegeben. Wenn Daten von einem Cluster auf einen anderen repliziert werden, werden der Source-Cluster und alle Cluster, die bereits Daten genutzt haben, nachverfolgt, um Replikationsschleifen zu verhindern. Für weitere Informationen werden Sie in diesem Lernprogramm eine Quelle-Ziel-Replikation konfigurieren. Andere Clustertopologien finden Sie unter [Apache HBase-Referenzhandbuch](http://hbase.apache.org/book.html#_cluster_replication).

Dies ist der dritte Teil der Serie:

- [Konfigurieren einer VPN-Konnektivität zwischen zwei virtuellen Netzwerken][hdinsight-hbase-replication-vnet]
- [Konfigurieren von DNS für virtuelle Netzwerke][hdinsight-hbase-replication-dns]
- Konfigurieren von HBase-Georeplikation (dieses Lernprogramm)

Das folgende Diagramm veranschaulicht die beiden virtuellen Netzwerke und die Netzwerkverbindung, die Sie in [Konfigurieren Sie eine VPN-Konnektivität zwischen zwei virtuellen Netzwerken][hdinsight-hbase-geo-replication-vnet] und [Konfigurieren von DNS für die virtuellen Netzwerke][hdinsight-hbase-replication-dns] erstellt haben:

![HDInsight HBase Replikation virtuelles Netzwerkdiagramm][img-vnet-diagram]

## <a id="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Installieren und Verwenden von Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/). Um PowerShell-Skripts ausführen zu können, müssen Sie Azure PowerShell als Administrator ausführen und die Ausführungsrichtlinie auf *RemoteSigned* setzen. Siehe Verwenden des Cmdlet "Set-ExecutionPolicy".

- **Zwei virtuelle Netzwerke in Azure mit VPN-Konnektivität und konfiguriertem DNS**. Anweisungen hierzu finden Sie unter [Konfigurieren einer VPN-Verbindung zwischen zwei virtuellen Netzwerken in Azure][hdinsight-hbase-replication-vnet] und [Konfigurieren von DNS zwischen zwei virtuellen Netzwerken in Azure][hdinsight-hbase-replication-dns].


	Stellen Sie vor dem Ausführen von PowerShell-Skripts mithilfe des folgenden Cmdlets sicher, dass Sie mit Ihrem Azure-Abonnement verbunden sind:

		Add-AzureAccount

	Wenn Sie mehrere Azure-Abonnements haben, legen Sie das aktuelle Abonnement mit dem folgenden Cmdlet fest:

		Select-AzureSubscription <AzureSubscriptionName>



## Bereitstellen von HBase-Clustern in HDInsight

In [Konfigurieren einer VPN-Verbindung zwischen zwei virtuellen Netzwerken in Azure][hdinsight-hbase-replication-vnet] haben Sie ein virtuelles Netzwerk in einem Rechenzentrum in Europa und ein virtuelles Netzwerks in einem US-Rechenzentrum erstellt. Die beiden virtuellen Netzwerke sind über VPN verbunden. In dieser Sitzung werden Sie einen HBase-Cluster in den einzelnen virtuellen Netzwerken bereitstellen. Später in diesem Lernprogramm erstellen Sie eines der HBase-Cluster, um das andere HBase-Cluster zu replizieren.

Das Azure-Portal unterstützt die Bereitstellung von HDInsight-Clustern mit benutzerdefinierten Konfigurationsoptionen nicht. Stellen Sie z. B. *hbase.replication* auf *true* ein. Wenn Sie nach der Bereitstellung eines Clusters den Wert in der Konfigurationsdatei festgelegen, verlieren Sie die Einstellung nach dem Reimaging des Clusters. Weitere Informationen finden Sie unter [Bereitstellung eines Hadoop-Clusters in HDInsight][hdinsight-provision]. Eine der Optionen zum Bereitstellen von HDInsight-Clustern mit benutzerdefinierten Optionen ist die Verwendung von Azure PowerShell.


**Bereitstellen eines HBase-Clusters in Contoso-VNet-EU**

1. Öffnen Sie von Ihrer Arbeitsstation aus Windows PowerShell ISE.
2. Stellen Sie die Variablen am Anfang des Skripts ein und führen Sie das Skript aus.

		# create hbase cluster with replication enabled
		
		$azureSubscriptionName = "[AzureSubscriptionName]"
		
		$hbaseClusterName = "Contoso-HBase-EU" # This is the HBase cluster name to be used.
		$hbaseClusterSize = 1   # You must provision a cluster that contains at least 3 nodes for high availability of the HBase services.
		$hadoopUserLogin = "[HadoopUserName]"
		$hadoopUserpw = "[HadoopUserPassword]"
		
		$vNetName = "Contoso-VNet-EU"  # This name must match your Europe virtual network name.
		$subNetName = 'Subnet-1' # This name must match your Europe virtual network subnet name.  The default name is "Subnet-1".
		
		$storageAccountName = 'ContosoStoreEU'  # The script will create this storage account for you.  The storage account name doesn't support hyphens. 
		$storageAccountName = $storageAccountName.ToLower() #Storage account name must be in lower case.
		$blobContainerName = $hbaseClusterName.ToLower()  #Use the cluster name as the default container name.
		
		#connect to your Azure subscription
		Add-AzureAccount 
		Select-AzureSubscription $azureSubscriptionName
		
		# Create a storage account used by the HBase cluster
		$location = Get-AzureVNetSite -VNetName $vNetName | %{$_.Location} # use the virtual network location
		New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location
		
		# Create a blob container used by the HBase cluster
		$storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{$_.Primary}
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $blobContainerName -Context $storageContext
		
		# Create provision configuration object
		$hbaseConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHBaseConfiguration'
		    
		$hbaseConfigValues.Configuration = @{ "hbase.replication"="true" } #this modifies the hbase-site.xml file
		
		# retrieve vnet id based on vnetname
		$vNetID = Get-AzureVNetSite -VNetName $vNetName | %{$_.id}
		
		$config = New-AzureHDInsightClusterConfig `
		                -ClusterSizeInNodes $hbaseClusterSize `
		                -ClusterType HBase `
		                -VirtualNetworkId $vNetID `
		                -SubnetName $subNetName `
		            | Set-AzureHDInsightDefaultStorage `
		                -StorageAccountName $storageAccountName `
		                -StorageAccountKey $storageAccountKey `
		                -StorageContainerName $blobContainerName `
		            | Add-AzureHDInsightConfigValues `
		                -HBase $hbaseConfigValues
		
		# provision HDInsight cluster
		$hadoopUserPassword = ConvertTo-SecureString -String $hadoopUserpw -AsPlainText -Force     
		$credential = New-Object System.Management.Automation.PSCredential($hadoopUserLogin,$hadoopUserPassword)
		
		$config | New-AzureHDInsightCluster -Name $hbaseClusterName -Location $location -Credential $credential



**Bereitstellen eines HBase-Clusters in Contoso-VNet-US**

- Verwenden Sie das gleiche Skript mit den folgenden Werten:

		$hbaseClusterName = "Contoso-HBase-US" # This is the HBase cluster name to be used.
		$vNetName = "Contoso-VNet-US"  # This name must match your Europe virtual network name.
		$storageAccountName = 'ContosoStoreUS'	

	Da Sie bereits mit Ihrem Azure-Konto verbunden haben, müssen Sie die folgenden Comlets nicht mehr ausführen:

		Add-AzureAccount 
		Select-AzureSubscription $azureSubscriptionName




## Konfigurieren der bedingten DNS-Weiterleitungen

In [Konfigurieren von DNS für virtuelle Netzwerke][hdinsight-hbase-replication-dns] wurden DNS-Server für zwei Netzwerke konfiguriert. Die HBase-Cluster haben unterschiedliche Domänenendungen. Daher müssen Sie zusätzliche bedingte DNS-Weiterleitungen konfigurieren.

Um bedingte Weiterleitung zu konfigurieren, müssen Sie die Domänenendungen der beiden HBase-Cluster kennen.

**Finden der Domänenendungen der beiden HBase-Cluster**

1. RDP in **Contoso-HBase-EU-**. Anweisungen finden Sie in [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Verwaltungsportal][hdinsight-manage-portal] Tatsächlich handelt es sich um headnode0 des Clusters.
2. Öffnen Sie eine Windows PowerShell-Konsole oder eine Eingabeaufforderung.
3. Führen Sie **Ipconfig** aus und notieren Sie sich **verbindungsspezifische DNS-Endungen**.
4. Bitte schließen Sie die RDP-Sitzung nicht. Sie benötigen diese später, um die Auflösung des Domänennamens zu testen.
5. Wiederholen Sie dieselben Schritte, um die **verbindungsspezifische DNS-Endung** von **Contoso-HBase-US** herauszufinden.


**Konfigurieren von DNS-Weiterleitungen**
 
1.	RDP in **Contoso-DNS-EU**. 
2.	Klicken Sie auf die Windowstaste in der linken unteren Ecke.
2.	Klicken Sie auf **Verwaltungstools**.
3.	Klicken Sie auf **DNS**.
4.	Erweitern Sie im linken Fensterbereich **DSN**, **Contoso-DNS-EU**.
5.	Klicken Sie mit der rechten Maustaste auf **Bedingte Weiterleitungen** und klicken Sie dann auf **Neue bedingte Weiterleitung**. 
5.	Geben Sie Folgendes ein:
	- **DNS-Domäne**: Geben Sie das DNS-Suffix von Contoso-HBase-US ein. Zum Beispiel: Contoso-HBase-US.f5.internal.cloudapp.net.
	- **IP-Adressen der Masterserver**: Geben Sie 10.2.0.4 ein, dabei handelt es sich um die IP-Adresse von Contoso-DNS-US. Überprüfen Sie die IP. Ihr DNS-Server kann eine andere IP-Adresse haben.
6.	Klicken Sie auf **ENTER** und dann auf **OK**. Sie können nun die IP-Adresse von Contoso-DNS-US von der von Contoso-DNS-EU auflösen.
7.	Wiederholen Sie die Schritte zum Hinzufügen einer bedingten DNS-Weiterleitung an den DNS-Dienst auf dem virtuellen Computer von Contoso-DNS-US mit den folgenden Werten:
	- **DNS-Domäne**: Geben Sie das DNS-Suffix von Contoso-HBase-EU ein. 
	- **IP-Adressen der Masterserver**: Geben Sie 10.2.0.4 ein, dabei handelt es sich um die IP-Adresse von Contoso-DNS-EU.

**Testen der Auflösung des Domänennamens**

1. Wechseln Sie zum Contoso-HBase-EU-RDP-Fenster.
2. Öffnen Sie eine Eingabeaufforderung.
3. Führen Sie den Ping-Befehl aus:

		ping headnode0.[DNS suffix of Contoso-HBase-US]

	Das ICM-Protokoll ist auf die Worker-Knoten der Cluster HBase aktiviert.

4. Schließen Sie die RDP-Sitzung nicht. Sie benötigen sie später im Lernprogramm.
5. Wiederholen Sie dieselben Schritte, um headnode0 der Contoso-HBase-EU von Contoso-HBase-US zu pingen.

>[AZURE.IMPORTANT]DNS muss funktionieren, bevor Sie mit den nächsten Schritten fortfahren können.

## Aktivieren der Replikation zwischen HBase-Tabellen

Sie können nun eine HBase-Beispieltabelle erstellen, Replikation aktivieren und dann mit einigen Daten zu testen. Die Beispieltabelle , die Sie verwenden werden, verfügt über zwei Spaltenfamilien: Persönlich und Office.

In diesem Lernprogramm verwenden Sie den Europa HBase Cluster als Quell-Cluster und US HBase-Cluster als Ziel-Cluster.

Erstellen Sie HBase-Tabellen mit dem gleichen Namen und den gleichen Spaltenfamilien in den Quell- und Ziel-Clustern, sodass der Ziel-Cluster weiß, wo die Daten gespeichert werden sollen, die es empfängt. Weitere Informationen zur Verwendung von HBase-Shell finden Sie unter [Erste Schritte mit Apache HDInsight][hdinsight-hbase-get-started].

**Erstellen Sie eine HBase-Tabelle auf Contoso-HBase-EU**

1. Wechseln Sie zum **Contoso-HBase-EU** RDP-Fenster.
2. Klicken Sie auf dem Desktop auf **Hadoop Command Line**.
2. Wechseln Sie in das HBase-Stammverzeichnis:

		cd %HBASE_HOME%\bin
3. Öffnen Sie die HBase-Befehlszeile:

		hbase shell
4. Erstellen einer HBase-Tabelle:

		create 'Contacts', 'Personal', 'Office'
5. Schließen Sie weder die RDP-Sitzung noch das Hadoop-Befehlszeilenfenster. Sie werden diese später noch im Lernprogramm benötigen.
	
**Erstellen Sie eine HBase-Tabelle auf Contoso-HBase-US**

- Wiederholen Sie die gleichen Schritte zum Erstellen derselben Tabelle auf Contoso-HBase-US.


**Hinzufügen von Contoso-HBase-US als Replikation-Peer**

1. Wechseln Sie zum **Contso-HBase_EU** RDP-Fenster.
2. Fügen Sie aus dem HBase-Shell-Fenster den Zielcluster (Contoso-HBase-US) als Peer hinzu, zum Beispiel:

		add_peer '1', 'zookeeper0.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper1.contoso-hbase-us.d4.internal.cloudapp.net,zookeeper2.contoso-hbase-us.d4.internal.cloudapp.net:2181:/hbase'

	Im Beispiel ist die Domänenendung *contoso-hbase-us.d4.internal.cloudapp.net*. Sie müssen sie aktualisieren, damit diese der Domänenendung des US HBase Clusters entspricht. Stellen Sie sicher, dass keine Leerzeichen zwischen den Hostnamen bestehen.

**Konfiguration jeder Spaltenfamilie für die Replikation auf dem Quell-Cluster**

1. Konfigurieren Sie aus dem HBase-Shell-Fenster der **Contso-HBase-EU-** RDP-Sitzung jede Spaltenfamilie, die repliziert werden soll:

		disable 'Contacts'
		alter 'Contacts', {NAME => 'Personal', REPLICATION_SCOPE => '1'}
		alter 'Contacts', {NAME => 'Office', REPLICATION_SCOPE => '1'}
		enable 'Contacts'

**Massenupload von Daten in HBase-Tabelle**

Eine Beispieldatendatei wurde auf einem öffentlichen Azure-Blob-Container mit der folgenden URL hochgeladen:

		wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

Inhalt der Datei:

		8396	Calvin Raji	230-555-0191	5415 San Gabriel Dr.
		16600	Karen Wu	646-555-0113	9265 La Paz
		4324	Karl Xie	508-555-0163	4912 La Vuelta
		16891	Jonathan Jackson	674-555-0110	40 Ellis St.
		3273	Miguel Miller	397-555-0155	6696 Anchor Drive
		3588	Osarumwense Agbonile	592-555-0152	1873 Lion Circle
		10272	Julia Lee	870-555-0110	3148 Rose Street
		4868	Jose Hayes	599-555-0171	793 Crawford Street
		4761	Caleb Alexander	670-555-0141	4775 Kentucky Dr.
		16443	Terry Chander	998-555-0171	771 Northridge Drive

Sie können dieselbe Datendatei in Ihr HBase Cluster hochladen und von dort Daten importieren.

1. Wechseln Sie zum **Contoso-HBase-EU** RDP-Fenster.
2. Klicken Sie auf dem Desktop auf **Hadoop Command Line**.
3. Wechseln Sie in das HBase-Stammverzeichnis:

		cd %HBASE_HOME%\bin

4. Hochladen der Daten:

		hbase org.apache.hadoop.hbase.mapreduce.ImportTsv -Dimporttsv.columns="HBASE_ROW_KEY,Personal:Name, Personal:HomePhone, Office:Address" -Dimporttsv.bulk.output=/tmpOutput Contacts wasb://hbasecontacts@hditutorialdata.blob.core.windows.net/contacts.txt

		hbase org.apache.hadoop.hbase.mapreduce.LoadIncrementalHFiles /tmpOutput Contacts


## Überprüfen Sie, ob die Datenreplikation stattfindet

Sie können überprüfen, ob die Replikation erfolgt, indem Sie die Tabellen aus den beiden Clustern mit den folgenden HBase-Shell-Befehlen durchsuchen:

		Scan 'Contacts'


## Nächste Schritte

In diesem Lernprogramm haben Sie gelernt, HBase-Replikation über zwei Rechenzentren zu konfigurieren. Weitere Informationen zu HDInsight und HBase finden Sie unter:

- [HDInsight Serviceseite](http://azure.microsoft.com/services/hdinsight/)
- [HDInsight-Dokumentation](http://azure.microsoft.com/documentation/services/hdinsight/)
- [Erste Schritte mit Apache HBase in HDInsight][hdinsight-hbase-get-started]
- [Überblick HDInsight HBase][hdinsight-hbase-overview]
- [Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk][hdinsight-hbase-provision-vnet]
- [Analyse der Twitter-Stimmungen in Echtzeit mit HBase][hdinsight-hbase-twitter-sentiment]
- [Analyse von Sensordaten mit Storm und HBase in HDInsight (Hadoop)][hdinsight-sensor-data]

[hdinsight-hbase-geo-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[hdinsight-hbase-geo-replication-dns]: ../hdinsight-hbase-geo-replication-configure-VNet.md


[img-vnet-diagram]: ./media/hdinsight-hbase-geo-replication/HDInsight.HBase.Replication.Network.diagram.png

[powershell-install]: ../install-configure-powershell.md
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started.md
[hdinsight-manage-portal]: hdinsight-administer-use-management-portal.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-hbase-replication-vnet]: hdinsight-hbase-geo-replication-configure-VNets.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md
[hdinsight-hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md
[hdinsight-sensor-data]: hdinsight-storm-sensor-data-analysis.md
[hdinsight-hbase-overview]: hdinsight-hbase-overview.md
[hdinsight-hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md
[hdinsight-hbase-get-started]: ../hdinsight-hbase-get-started.md

<!---HONumber=July15_HO4-->