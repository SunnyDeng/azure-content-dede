<properties 
	pageTitle="Bereitstellen von HBase-Clustern in einem virtuellen Azure-Netzwerk | Azure" 
	description="Erfahren Sie, wie Sie HDInsight-Cluster im virtuellen Azure-Netzwerk erstellen können." 
	services="hdinsight" 
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
	ms.date="12/19/2014" 
	ms.author="jgao"/>

# Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk

Erfahren Sie, wie Sie HDInsight HBase-Cluster im [virtuellen Azure-Netzwerk][1] erstellen können. 

Mit der Integration in virtuelle Netzwerke können HBase-Cluster im selben virtuellen Netzwerk bereitgestellt werden wie Ihre Anwendungen, sodass Anwendungen direkt mit HBase kommunizieren können. Es ergeben sich folgende Vorteile:

- Direkte Konnektivität der Webanwendungen mit den Knoten des HBase-Clusters, was Kommunikation unter Verwendung von HBase Java RPC APIs ermöglicht
- Optimierte Leistung, da Datenverkehr nicht über mehrere Gateways und Lastenausgleiche fließt 
- Mehr Sicherheit bei der Verarbeitung sensibler Informationen ohne exponierten öffentlichen Endpunkt


##Themen in diesem Artikel

- [Voraussetzungen](#prerequisites)
- [Bereitstellen von HBase-Clustern in einem virtuellen Netzwerk](#hbaseprovision)
- [Herstellen einer Verbindung mit dem im virtuellen Netzwerk bereitgestellten HBase-Cluster unter Verwendung von HBase Java RPC APIs](#connect)
- [Bereitstellen eines HBase-Clusters mit PowerShell](#powershell)
- [Nächste Schritte](#nextsteps)

##<a id="prerequisites"></a>Voraussetzungen
Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Azure ist eine abonnementbasierte Plattform. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][azure-purchase-options], [Angebote für Mitglieder][azure-member-offers] oder [Kostenlose Testversion][azure-free-trial].

- **Eine Arbeitsstation, auf der Azure PowerShell installiert und konfiguriert ist**. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install]. Um PowerShell-Skripts ausführen zu können, müssen Sie Azure PowerShell als Administrator ausführen und die Ausführungsrichtlinie auf *RemoteSigned* setzen. Siehe [Verwenden des Cmdlet "Set-ExecutionPolicy"][2].

	Stellen Sie vor dem Ausführen von PowerShell-Skripts mithilfe des folgenden Cmdlets sicher, dass Sie mit Ihrem Azure-Abonnement verbunden sind:

		Add-AzureAccount

	Wenn Sie mehrere Azure-Abonnements haben, legen Sie das aktuelle Abonnement mit dem folgenden Cmdlet fest:

		Select-AzureSubscription <AzureSubscriptionName>


##<a id="hbaseprovision"></a>Bereitstellen eines HBase-Clusters in einem virtuellen Netzwerk 

**So erstellen Sie ein virtuelles Netzwerk im Verwaltungsportal:**

1. Melden Sie sich beim [Azure-Verwaltungsportal][azure-portal] an.
2. Klicken Sie links unten auf **NEU**, zeigen Sie auf **NETZWERKDIENSTE** und dann auf **VIRTUELLES NETZWERK**, und klicken Sie anschließend auf **SCHNELLERFASSUNG**.
3. Geben Sie folgende Werte ein bzw. wählen diese aus:

	- **Name**: Der Name des virtuellen Netzwerks.
	- **Adressraum**: Wählen Sie einen Adressraum für das virtuelle Netzwerk, der groß genug ist, um Adressen für alle Knoten im Cluster zur Verfügung zu stellen. Andernfalls schlägt die Bereitstellung fehl. Für dieses Lernprogramm haben Sie die Wahl zwischen den drei verfügbaren Optionen. 
	- **Maximale Anzahl virtueller Computer**: Wählen Sie eine maximale Anzahl an virtuellen Computern aus. Dieser Wert bestimmt die Anzahl möglicher Hosts (VM), die in dem Adressbereich erstellt werden können. Für dieses Lernprogramm ist **4096 [CIDR: /20]** ausreichend. 
	- **Speicherort**: Der Speicherort muss derselbe sein wie der des HBase-Clusters, den Sie erstellen.
	- **DNS-Server**: In diesem Artikel werden interne DNS-Server verwendet, die von Azure bereitgestellt werden. Daher können Sie **Keine** auswählen. Erweiterte Netzwerkkonfiguration mit benutzerdefinierten DNS-Servern wird ebenfalls unterstützt. Detaillierte Anleitungen finden Sie unter [Namensauflösung (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx).
4. Klicken Sie auf **VIRTUELLES NETZWERK ERSTELLEN**. Der Name des neuen virtuellen Netzwerks wird in der Liste angezeigt. Warten Sie, bis in der Statusspalte **Erstellt** angezeigt wird.
5. Klicken Sie im Hauptbereich auf das virtuelle Netzwerk, das Sie gerade erstellt haben.
6. Klicken Sie oben auf der Seite auf **DASHBOARD**.
7. Notieren Sie sich die **ID DES VIRTUELLEN NETZWERKS**. Sie finden diese unter **Auf einen Blick**. Sie benötigen die ID, wenn Sie den HBase-Cluster bereitstellen.
8. Klicken Sie am oberen Seitenrand auf **KONFIGURIEREN**.
9. Unten auf der Seite finden Sie den Namen des Standardsubnetzes. Dieser lautet **Subnet-1**. Sie können das Subnetz optional umbenennen oder ein neues Subnetz für den HBase-Cluster hinzufügen. Notieren Sie sich den Namen des Subnetzes. Sie benötigen ihn bei der Bereitstellung des Clusters.
10. Prüfen Sie den Wert unter **CIDR(ANZAHL VON ADRESSEN)** für das Subnetz, das für den Cluster verwendet wird. Die Anzahl der Adressen muss größer als die Anzahl der Workerknoten plus Sieben sein (Gateway: 2, Hauptknoten: 2, Zookeeper: 3). Wenn Sie zum Beispiel einen HBase-Cluster mit 10 Knoten benötigen, muss die Anzahl der Adressen für das Subnetz größer als 17 (10 + 7) sein. Andernfalls schlägt die Bereitstellung fehl.

	> [AZURE.NOTE] Sie sollten unbedingt ein einziges Subnetz pro Cluster verwenden. 

11. Klicken Sie unten auf der Seite auf **Speichern**, wenn Sie die Werte für das Subnetz aktualisiert haben.



> [AZURE.NOTE] HDInsight-Cluster nutzen den Azure-BLOB-Speicher zum Speichern von Daten. Weitere Informationen finden Sie unter [Verwenden von Azure BLOB-Speicher mit Hadoop in HDInsight][hdinsight-storage]. Sie benötigen ein Speicherkonto und einen Blob-Speichercontainer. Der Speicherort des Speicherkontos muss dem Speicherort des virtuellen Netzwerks und des Clusters entsprechen.

**So erstellen Sie ein Azure-Speicherkonto und einen Blob-Speichercontainer**

1. Melden Sie sich beim [Azure-Verwaltungsportal][azure-portal] an.
2. Klicken Sie links unten auf **NEU**, zeigen Sie auf **DATENDIENSTE**, auf **SPEICHER**, und klicken Sie anschließend auf **SCHNELLERFASSUNG**.

3. Geben Sie folgende Werte ein bzw. wählen diese aus:

	- **URL**: Der Name des Speicherkontos.
	- **SPEICHERORT**: Der Speicherort des Speicherkontos. Stellen Sie sicher, dass er dem Speicherort des virtuellen Netzwerks entspricht. Affinitätsgruppen werden nicht unterstützt.
	- **REPLIKATION**: Nutzen Sie zu Testzwecken **Lokal redundant**, um Kosten zu sparen.

4. Klicken Sie auf **SPEICHERKONTO ERSTELLEN**.  Sie werden das neue Speicherkonto in der Speicherliste sehen. 
5. Warten Sie, bis sich der **STATUS** des neuen Speicherkontos in **Online** geändert hat.
6. Klicken Sie in der Liste auf das neue Speicherkonto, und wählen Sie es aus.
7. Klicken Sie am unteren Seitenrand auf **ZUGRIFFSSCHLÜSSEL VERWALTEN**.
8. Notieren Sie sich die Werte unter **SPEICHERKONTONAME** und **PRIMÄRER ZUGRIFFSSCHLÜSSEL** (oder **SEKUNDÄRER ZUGRIFFSSCHLÜSSEL**.  Beide können verwendet werden).  Sie werden diese später im Lernprogramm benötigen.
9. Klicken Sie oben auf der Seite auf **CONTAINER**.
10. Klicken Sie unten auf der Seite auf **HINZUFÜGEN**.
11. Geben Sie den Containernamen ein.  Dieser Container wird als Standardcontainer für den HBase-Cluster verwendet. Standardmäßig entspricht der Standard-Containername dem Clusternamen. Wählen Sie für das Feld **ZUGRIFF** die Einstellung **Privat** aus.  
12. Aktivieren Sie das Kontrollkästchen, um den Container zu erstellen.

**So stellen Sie ein HBase-Cluster im Azure-Portal bereit**

> [AZURE.NOTE] Weitere Informationen zum Bereitstellen eines neuen HBase-Clusters mit PowerShell finden Sie unter [Bereitstellen eines HBase-Clusters mit PowerShell](#powershell).

1. Melden Sie sich beim [Azure-Verwaltungsportal][azure-portal] an.

2. Klicken Sie unten links auf **NEU**, zeigen Sie auf **DATENDIENSTE** und **HDINSIGHT**, und klicken Sie dann auf **BENUTZERDEFINIERT ERSTELLEN**.

3. Geben Sie einen CLUSTERNAMEN ein, wählen als CLUSTERTYP "HBase" aus, und wählen Sie dann die HDINSIGHT-Version aus.

	![Provide details for the HBase cluster][img-provision-cluster-page1]

	Klicken Sie auf die rechte Taste.

4. Geben Sie folgende Werte ein, bzw. wählen Sie diese aus:

	- **DATENKNOTEN**: Geben Sie die Anzahl der Datenknoten für den HBase-Cluster ein. 
	- **REGION/VIRTUELLES NETZWERK**: Wählen Sie das virtuelle Azure-Netzwerk aus, das Sie erstellt haben.
	- **VIRTUELLES NETZWERK - SUBNETZE**: Wählen Sie ein Subnetz aus. Der Standardname ist **Subnet-1**.

	Klicken Sie auf die rechte Taste.

5. Geben Sie für den Hadoop-Benutzer **BENUTZERNAME** und **KENNWORT** ein, die für diesen Cluster verwendet werden sollen, und klicken Sie dann mit der rechten Maustaste.
6. Wählen Sie aus, ob ein neues Speicherkonto oder ein bestehendes Speicherkonto als Standardspeicherkonto für den Cluster verwendet werden soll, und klicken Sie dann mit der rechten Maustaste.

7. Klicken Sie auf der Seite **Skriptaktionen** auf **Skriptaktion hinzufügen**, um Einzelheiten zum benutzerdefinierten Skript anzugeben, das Sie zum Anpassen eines Clusters bei seiner Erstellung ausführen möchten. Sie können beispielsweise Skriptaktion verwenden, um ein Cluster zum Installieren von <a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a>zu verwenden. Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen][hdinsight-customize-cluster]. 
	
	![Configure Script Action to customize an HDInsight HBase cluster][img-provision-cluster-page5]

	<table border='1'>
		<tr><th>Eigenschaft</th><th>Wert</th></tr>
		<tr><td>Name</td>
			<td>Geben Sie einen Namen für die Skriptaktion an.</td></tr>
		<tr><td>Skript-URI</td>
			<td>Geben Sie den URI für das Skript an, das aufgerufen wird, um den Cluster anzupassen.</td></tr>
		<tr><td>Knotentyp</td>
			<td>Gibt die Knoten an, auf denen das Anpassungsskript ausgeführt wird. Sie können <b>Alle Knoten</b>, <b>Nur Hauptknoten</b> oder <b>Nur Workerknoten</b> auswählen.
		<tr><td>Parameter</td>
			<td>Geben Sie die Parameter an, wenn dies für das Skript erforderlich ist.</td></tr>
	</table>

 Sie können dem Cluster mehr als eine Skriptaktion zum Installieren von mehreren Komponenten hinzufügen. Nachdem Sie die Skripts hinzugefügt haben, klicken Sie auf das Häkchen, um die Bereitstellung des Clusters zu starten.

 Führen Sie die Schritte unter [Erste Schritte mit HBase mit Hadoop in HDInsight][hbase-get-started] aus, um mit Ihrem neu erstellten HBase-Cluster zu arbeiten.

##<a id="connect"></a>Herstellen einer Verbindung mit dem im virtuellen Netzwerk bereitgestellten HBase-Cluster unter Verwendung von HBase Java RPC APIs

1.	Stellen Sie einen virtuellen IaaS-Computer im selben virtuellen Azure-Netzwerk und im selben Subnetz bereit. Dadurch verwenden der virtuelle Computer und der HBase-Cluster denselben internen DNS-Server für die Auflösung von Hostnamen. Hierfür müssen Sie die Option "Aus Galerie" auswählen und das virtuelle Netzwerk statt eines Rechenzentrums wählen. Anweisungen dazu finden Sie unter [Erstellen eines virtuellen Windows Server-Computers][vm-create]. Ein standardmäßiges Windows Server 2012-Image mit kleinem virtuellen Computer ist ausreichend.
	
2.	Für Remote-Verbindungen zwischen Java-Anwendungen und HBase müssen Sie den vollqualifizierten Domänennamen (FQDN) verwenden. Rufen Sie hierzu das verbindungsspezifische DNS-Suffix des HBase-Clusters ab. Dazu können Sie entweder Ambari mit Curl abfragen oder sich per Remotedesktop mit dem Cluster verbinden.

	* **Curl** - Verwenden Sie den folgenden Befehl:

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		Suchen Sie im zurückgegebenen JSON nach dem Eintrag "host_name". Dieser Eintrag enthält den vollqualifizierten Domänennamen (FQDN) für die Knoten im Cluster. Beispiel:

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		Der Teil des Domänennamens, der mit dem Clusternamen beginnt, ist das DNS-Suffix. Zum Beispiel: mycluster.b1.cloudapp.net.

	* **PowerShell** - Führen Sie das folgende PowerShell-Skript aus, um die **Get-ClusterDetail**-Funktion zu registrieren, mit der Sie das DNS-Suffix zurückgeben können.

			function Get-ClusterDetail(
			    [String]
			    [Parameter( Position=0, Mandatory=$true )]
			    $ClusterDnsName,
				[String]
			    [Parameter( Position=1, Mandatory=$true )]
			    $Username,
				[String]
			    [Parameter( Position=2, Mandatory=$true )]
			    $Password,
			    [String]
			    [Parameter( Position=3, Mandatory=$true )]
			    $PropertyName
				)
			{
			<#
			    .SYNOPSIS 
			     Displays information to facilitate HDInsight cluster to cluster scinario within same virtual network.
				.Description
				 This command shows following 4 properties of an HDInsight cluster.
				 1. ZookeeperQuorum (only support HBase type cluster)
					Shows the value of hbase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (only support HBase type cluster)
					Shows the value of hbase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (only support HBase type cluster)
					Shows a list of host FQDNs which run HBase rest server.
				 4. FQDNSuffix (support all type cluster)
					Shows FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of hbase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of hbase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs which run HBase rest server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows FQDN suffix of hosts in the cluster.
			#>
			
				$DnsSuffix = ".azurehdinsight.net"
				
				$ClusterFQDN = $ClusterDnsName + $DnsSuffix
				$webclient = new-object System.Net.WebClient
				$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
			
				if($PropertyName -eq "ZookeeperQuorum")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'
				}
				if($PropertyName -eq "ZookeeperClientPort")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.property.clientPort"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					Write-host $JsonObject.items[0].properties.'hbase.zookeeper.property.clientPort'
				}
				if($PropertyName -eq "HBaseRestServers")
				{
					$Url1 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.rest.port"
					$Response1 = $webclient.DownloadString($Url1)
					$JsonObject1 = $Response1 | ConvertFrom-Json
					$PortNumber = $JsonObject1.items[0].properties.'hbase.rest.port'
					
					$Url2 = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/hbase/components/hbrest"
					$Response2 = $webclient.DownloadString($Url2)
					$JsonObject2 = $Response2 | ConvertFrom-Json
					foreach ($host_component in $JsonObject2.host_components)
					{
						$ConnectionString = $host_component.HostRoles.host_name + ":" + $PortNumber
						Write-host $ConnectionString
					}
				}
				if($PropertyName -eq "FQDNSuffix")
				{
					$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/components/resourcemanager"
					$Response = $webclient.DownloadString($Url)
					$JsonObject = $Response | ConvertFrom-Json
					$FQDN = $JsonObject.host_components[0].HostRoles.host_name
					$pos = $FQDN.IndexOf(".")
					$Suffix = $FQDN.Substring($pos + 1)
					Write-host $Suffix
				}
			}

   Verwenden Sie nach der Ausführung des PowerShell-Skripts den folgenden Befehl, um das DNS-Suffix mithilfe der Get-ClusterDetail-Funktion zurückzugeben. Geben Sie den Namen Ihres HDInsight HBase-Clusters sowie den Admin-Namen und das Admin-Kennwort an, wenn Sie diesen Befehl ausführen.

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

   Daraufhin wird das DNS-Suffix zurückgegeben. Beispiel: **yourclustername.b4.internal.cloudapp.net**.

 > [AZURE.NOTE] Sie können auch per Remotedesktop eine Verbindung mit dem HBase-Cluster herstellen (wobei Sie mit dem Stammknoten verbunden werden) und **ipconfig** in einer Eingabeaufforderung ausführen, um das DNS-Suffix abzurufen. Anweisungen zur Aktivierung von RDP und zur Verbindung des Clusters mittels RDP finden Sie unter [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Verwaltungsportal][hdinsight-admin-portal].
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!-- 
3.	Ändern Sie die Konfiguration des primären DNS-Suffixes des virtuellen Computers. Dadurch kann der virtuelle Computer automatisch den Hostnamen des HBase-Clusters auflösen, ohne das Suffix explizit angeben zu müssen. Beispielsweise wird der Hostname  *workernode0* ordnungsgemäß in den workernode0 des HBase-Clusters aufgelöst. 

	So ändern Sie die Konfiguration

	1. Integrieren Sie das RDP in den virtuellen Computer. 
	2. Öffnen Sie den **Lokalen Gruppenrichtlinien-Editor**. Die ausführbare Datei heißt gpedit.msc.
	3. Erweitern Sie **Computerkonfiguration**, **Administrative Vorlagen**, **Netzwerk**, und klicken Sie dann auf **DNS-Client**. 
	- Legen Sie für **Primäres DNS-Suffix** den Wert fest, den Sie in Schritt 2 abgerufen haben: 

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]

	4. Klicken Sie auf **OK**. 
	5. Starten Sie den virtuellen Computer neu.
-->

Führen Sie den Befehl `ping headnode0.<dns suffix>` auf dem virtuellen Computer aus, um zu überprüfen, ob der virtuelle Computer mit dem HBase-Cluster kommunizieren kann. Zum Beispiel: ping headnode0.mycluster.b1.cloudapp.net

Führen Sie die unter [Verwenden von Maven zum Erstellen von Java-Anwendungen, die HBase mit HDInsight (Hadoop) nutzen](azure.microsoft.com/de-de/documentation/articles/hdinsight-hbase-build-java-maven/) beschriebenen Schritte aus, um diese Informationen in einer Java-Anwendung zu verwenden. Wenn sich die Anwendung mit einem Remote-HBase-Server verbinden soll, müssen Sie den FQDN für ZooKeeper in der Datei **hbase-site.xml** eintragen. Beispiel:

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE] Weitere Informationen zur Namensauflösung in virtuellen Azure-Netzwerken und zur Verwendung eigener DNS-Server finden Sie unter [Namensauflösung (DNS)](http://msdn.microsoft.com/de-de/library/azure/jj156088.aspx).

##<a id="powershell"></a>Bereitstellen eines HBase-Clusters mit Azure PowerShell

**So stellen Sie einen HBase-Cluster mit Azure PowerShell bereit**

1. Öffnen Sie PowerShell ISE.
2. Kopieren Sie den folgenden Code, und fügen Sie ihn in den Skriptbereich ein.

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"
		
		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password) 
		
		New-AzureHDInsightCluster -Name $hbaseClusterName `
		                          -ClusterType HBase `
		                          -Version 3.1 `
		                          -Location $location `
		                          -ClusterSizeInNodes $clusterSize `
		                          -Credential $creds `
		                          -VirtualNetworkId $vnetID `
		                          -SubnetName $subNetName `
		                          -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
		                          -DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainerName $storageContainerName


3. Klicken Sie auf **Skript ausführen**, oder drücken Sie **F5**.
4. Wenn Sie den Cluster prüfen möchten, können Sie ihn entweder über das Verwaltungsportal prüfen oder das folgende PowerShell-Cmdlet aus dem unteren Bereich ausführen:

	Get-AzureHDInsightCluster 

##<a id="nextsteps"></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie einen HBase-Cluster bereitstellen können. Weitere Informationen finden Sie unter:

- [Erste Schritte mit HDInsight][hdinsight-get-started]
- [Bereitstellen von Hadoop-Clustern in HDInsight][hdinsight-provision] 
- [Erste Schritte mit HBase mit Hadoop in HDInsight][hbase-get-started]
- [Analysieren der Twitter-Stimmungen mit HBase in HDInsight][hbase-twitter-sentiment]
- [Virtuelle Netzwerke im Überblick][vnet-overview]


[1]: http://azure.microsoft.com/de-de/services/virtual-network/
[2]: http://technet.microsoft.com/de-de/library/ee176961.aspx
[3]: http://technet.microsoft.com/de-de/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started/
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment/
[vnet-overview]: http://msdn.microsoft.com/library/azure/jj156007.aspx
[vm-create]: ../virtual-machines-windows-tutorial/

[azure-portal]: https://manage.windowsazure.com
[azure-create-storageaccount]: ../storage-create-storage-account/ 
[azure-purchase-options]: http://azure.microsoft.com/de-de/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/de-de/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/de-de/pricing/free-trial/

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/#rdp

[hdinsight-powershell-reference]: http://msdn.microsoft.com/de-de/library/windowsazure/dn479228.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../install-configure-powershell


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage/#powershell
[hdinsight-analyze-flight-delay-data]: ../hdinsight-analyze-flight-delay-data/
[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-use-sqoop]: ../hdinsight-use-sqoop/
[hdinsight-power-query]: ../hdinsight-connect-excel-power-query/
[hdinsight-hive-odbc]: ../hdinsight-connect-excel-hive-ODBC-driver/

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Provision details for the new HBase cluster"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Use Script Action to customize an HBase cluster"
<!--HONumber=42-->
