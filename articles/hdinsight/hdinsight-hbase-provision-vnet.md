<properties
	pageTitle="Bereitstellen von HBase-Clustern in einem virtuellen Netzwerk | Microsoft Azure"
	description="Erste Schritte mit HBase in Azure HDInsight. Erfahren Sie, wie Sie HDInsight HBase-Cluster in Azure Virtual Network erstellen können."
	keywords=""
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
   ms.date="08/12/2015"
   ms.author="jgao"/>

# Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk 

Erfahren Sie, wie Sie Azure HDInsight HBase-Cluster in einem [Azure Virtual Network][1] erstellen können.

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk](hdinsight-hbase-provision-vnet-v1.md)

Mit der Integration in virtuelle Netzwerke können HBase-Cluster im selben virtuellen Netzwerk bereitgestellt werden wie Ihre Anwendungen, sodass Anwendungen direkt mit HBase kommunizieren können. Es ergeben sich folgende Vorteile:

- Direkte Konnektivität der Webanwendung mit den Knoten des HBase-Clusters, wodurch die Kommunikation über HBase Java-RPC-APIs (Remoteprozeduraufrufe) ermöglicht wird.
- Optimierte Leistung, da Datenverkehr nicht über mehrere Gateways und Lastenausgleichsmodule fließt.
- Möglichkeit einer höheren Sicherheit bei der Verarbeitung sensibler Informationen ohne exponierten öffentlichen Endpunkt.

##Voraussetzungen
Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Eine Arbeitsstation mit Azure PowerShell**. Siehe [Install and use Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/) (Installieren und Verwenden von Azure PowerShell, in englischer Sprache). Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md). Um Azure PowerShell-Skripts ausführen zu können, müssen Sie Azure PowerShell als Administrator ausführen und die Ausführungsrichtlinie auf *RemoteSigned* setzen. Siehe [Verwenden des Cmdlet „Set-ExecutionPolicy“][2].

	Stellen Sie vor dem Ausführen von Azure PowerShell-Skripts mithilfe des folgenden Cmdlets sicher, dass eine Verbindung mit Ihrem Azure-Abonnement besteht:

		Add-AzureAccount

	Wenn Sie mehrere Azure-Abonnements haben, legen Sie das aktuelle Abonnement mit dem folgenden Cmdlet fest:

		Select-AzureSubscription <AzureSubscriptionName>


## Bereitstellen von HBase-Clustern in einem virtuellen Netzwerk

Anwendungen bestehen normalerweise aus vielen Komponenten. In diesem Tutorial verwenden Sie Folgendes:

- ein virtuelles Azure-Netzwerk
- ein Azure-Speicherkonto
- einen Azure HDInsight HBase-Cluster
- (optional) einen virtuellen Azure-Computer, der als DNS-Server fungiert

Mit dem Azure-Ressourcen-Manager können Sie mit den Ressourcen in Ihrer Anwendung als Gruppe arbeiten. Sie können alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen, aktualisieren oder löschen. Sie verwenden eine Vorlage für die Bereitstellung, die für unterschiedliche Umgebungen geeignet sein kann, z. B. Testing, Staging und Produktion. Sie können die Abrechnung für Ihre Organisation vereinfachen, indem Sie die zusammengefassten Kosten für die gesamte Gruppe anzeigen.

**So erstellen Sie eine Ressourcengruppe**

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) an.
2. Klicken Sie auf **NEU**, auf **Verwaltung** und anschließend auf **Ressourcengruppe**.
3. Geben Sie folgende Werte ein bzw. wählen diese aus:

	- **Ressourcengruppenname**: Geben Sie einen Namen für die Ressourcengruppe ein.
	- **Abonnement**: Wählen Sie das für diese Ressourcengruppe verwendete Azure-Abonnement aus.
	- **Ressourcengruppenstandort**: Wählen Sie ein Azure-Rechenzentrum aus. Dieser Standort muss nicht mit dem Standort des HDInsight-Clusters identisch sein.

4. Klicken Sie auf **Erstellen**.

Vor der Bereitstellung eines HBase-Clusters müssen Sie ein virtuelles Azure-Netzwerk erstellen.

**So erstellen Sie ein virtuelles Netzwerk im Azure-Portal**

1. Melden Sie sich beim [Vorschauportal](https://portal.azure.com) an.
2. Klicken Sie auf **NEU**, auf **Netzwerk** und anschließend auf **Virtuelles Netzwerk**.
3. Wählen Sie unter **Bereitstellungsmodell auswählen** die Option **Klassisch** aus, und klicken Sie dann auf **Erstellen**.

	> [AZURE.NOTE]Sie können kein virtuelles Azure-Netzwerk der Version 1 (klassisch) mit HDInsight verwenden. Das virtuelle Netzwerk muss Version 2 aufweisen (Azure-Ressourcen-Manager), damit es beim Erstellen des Clusters im Azure-Vorschauportal als Option angezeigt wird bzw. beim Erstellen eines Clusters über die Azure-Befehlszeilenschnittstelle oder Azure PowerShell verwendet werden kann.
> 
> Wenn Ressourcen in einem v1-Netzwerk vorhanden sind und diese Ressourcen über ein virtuelles Netzwerk direkt auf HDInsight zugreifen können sollen, finden Sie unter [Herstellen einer Verbindung zwischen klassischen VNets und neuen VNets](../virtual-network/virtual-networks-arm-asm-s2s.md) Informationen zum Erstellen einer Verbindung zwischen einem virtuellen v2-Netzwerk und einem virtuellen v1-Netzwerk. Nachdem diese Verbindung eingerichtet wurde, können Sie den HDInsight-Cluster im virtuellen v2-Netzwerk erstellen.

4. Geben Sie die folgenden Werte ein, oder wählen Sie sie aus:

	- **Name**: Name des virtuellen Netzwerks.
	- **Adressraum**: Wählen Sie einen Adressraum für das virtuelle Netzwerk, der groß genug ist, um Adressen für alle Knoten im Cluster zur Verfügung zu stellen. Andernfalls schlägt die Bereitstellung fehl. Für dieses Tutorial können Sie die Standardwerte verwenden. Klicken Sie zum Speichern der Änderungen auf **OK**.
	- **Ressourcengruppe**: Wählen Sie die Ressourcengruppe aus, die Sie zuvor in diesem Tutorial erstellt haben.
	- **Abonnement**: Wählen Sie das Azure-Abonnement aus, das Sie für dieses virtuelle Netzwerk verwenden möchten.
	- **Speicherort** – Der Speicherort muss derselbe sein wie der des zu erstellenden HBase-Clusters.

5. Klicken Sie auf **Erstellen**.

Standardmäßig verwendet das virtuelle Netzwerk einen von Azure bereitgestellten internen Domain Name System (DNS)-Server. Erweiterte Netzwerkkonfigurationen mit benutzerdefinierten DNS-Servern werden ebenfalls unterstützt. Ausführliche Anleitungen finden Sie unter [Namensauflösung (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

**(Optional) So fügen Sie dem virtuellen Netzwerk einen virtuellen Computer als DNS-Server hinzu**

Ein DNS-Server ist optional, aber in einigen Fällen erforderlich. Die Vorgehensweise wird unter [Konfigurieren von DNS zwischen zwei virtuellen Netzwerken in Azure][hdinsight-hbase-replication-dns] beschrieben. Im Wesentlichen müssen Sie die folgenden Schritte ausführen:

1. Hinzufügen eines virtuellen Azure-Computers zum virtuellen Netzwerk
2. Festlegen einer statischen IP-Adresse für den virtuellen Computer
3. Hinzufügen der DNS-Serverrolle zum virtuellen Computer
4. Zuweisen des DNS-Servers zum virtuellen Netzwerk

**So stellen Sie einen HBase-Cluster im Azure-Portal bereit**

> [AZURE.NOTE]Informationen zum Bereitstellen eines neuen HBase-Clusters mit Azure PowerShell finden Sie unter [Bereitstellen eines HBase-Clusters mit Azure PowerShell](#powershell).


**So erstellen Sie einen HDInsight-Cluster**

1. Melden Sie sich beim [Azure-Vorschauportal](https://portal.azure.com) an.
2. Klicken Sie auf **NEU**, auf **Datenanalyse** und anschließend auf **HDInsight**.

    ![Erstellen eines neuen Clusters im Azure-Vorschauportal](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Erstellen eines neuen Clusters im Azure-Vorschauportal")

3. Geben Sie folgende Werte ein bzw. wählen diese aus:

  - **Clustername**: Geben Sie einen Namen für den Cluster ein. Wenn der Clustername verfügbar ist, wird neben dem Namen ein grünes Häkchen angezeigt.
  - **Clustertyp**: Wählen Sie **HBase** aus.
  - **Clusterbetriebssystem**: Wählen Sie **Windows Server 2012 R2 Datacenter** aus.
  - **Abonnement**: Wählen Sie das Azure-Abonnement aus, das für die Bereitstellung des Clusters verwendet werden soll.
  - **Ressourcengruppe**: Wählen Sie die Ressourcengruppe aus, die Sie zuvor in diesem Tutorial erstellt haben.
  - **Anmeldeinformationen**: Konfigurieren Sie den Benutzernamen und das Kennwort für den Hadoop-Benutzer (HTTP-Benutzer). Wenn Sie Remotedesktop für den Cluster aktivieren, müssen Sie den Benutzernamen und das Kennwort für den Remotedesktopbenutzer konfigurieren und ein Ablaufdatum für das Konto festlegen. Klicken Sie unten auf **Auswählen**, um die Änderungen zu speichern.
  - **Datenquelle**: Wählen Sie ein vorhandenes Azure-Speicherkonto aus, oder erstellen Sie ein neues Azure-Speicherkonto, das als Standarddateisystem für den Cluster verwendet werden soll. Der standardmäßige Name für den Standardcontainer ist der Clustername. Der Standort des Speicherkontos bestimmt auch den Standort des Clusters.
  - **Knotentarife**: Wählen Sie zu Übungszwecken oder zur Auswertung einen Knoten mit einer Region aus, um die Kosten zu minimieren.

  	- **Auswahlmethode**: Wählen Sie **Aus allen Abonnements** aus, damit Speicherkonten aus all Ihren Abonnements durchsucht werden können. Wählen Sie **Zugriffsschlüssel** aus, wenn Sie den **Speichernamen** und **Zugriffsschlüssel** eines vorhandenen Speicherkontos eingeben möchten.
  	- **Speicherkonto auswählen/Neu erstellen**: Klicken Sie auf **Speicherkonto auswählen**, um ein vorhandenes Speicherkonto auszuwählen, das Sie dem Cluster zuordnen möchten. Alternativ können Sie auf **Neu erstellen** klicken, um ein neues Speicherkonto zu erstellen. Geben Sie den Namen des Speicherkontos in das angezeigte Feld ein. Wenn der Name verfügbar ist, wird ein grünes Häkchen angezeigt.
    - **Standardcontainer auswählen**: Geben Sie den Namen des Standardcontainers für den Cluster ein. Sie können einen beliebigen Namen eingeben, es wird jedoch empfohlen, den Namen des Clusters zu verwenden. So können Sie leichter erkennen, dass es sich um den Container für diesen spezifischen Cluster handelt.
  	- **Standort**: Die geografische Region, in der sich das Speicherkonto befindet bzw. in der es erstellt wird. Dieser Standort bestimmt auch den Clusterstandort. Der Cluster und das zugeordnete Standardspeicherkonto müssen sich im selben Azure-Rechenzentrum befinden.

  - **Knotentarife**: Legen Sie die Anzahl von Workerknoten fest, die Sie für den Cluster benötigen. Die vorkalkulierten Kosten für den Cluster werden auf dem Blatt angezeigt.
	- **Optionale Konfiguration**: Für dieses Tutorial müssen Sie nur **Virtual Network** konfigurieren. Wählen Sie das virtuelle Netzwerk aus, das Sie zuvor im Tutorial erstellt haben. Denken Sie daran, ein Subnetz auszuwählen.

4. Klicken Sie auf **Erstellen**.


Führen Sie die Schritte unter [Erste Schritte mit HBase mit Hadoop in HDInsight](../hdinsight-hbase-get-started.md) aus, um mit Ihrem neu erstellten HBase-Cluster zu arbeiten.

##Herstellen einer Verbindung mit dem im virtuellen Netzwerk bereitgestellten HBase-Cluster unter Verwendung von HBase Java RPC-APIs

1.	Stellen Sie einen virtuellen IaaS-Computer (Infrastructure-as-a-Service) im gleichen virtuellen Azure-Netzwerk und im gleichen Subnetz bereit. Dadurch verwenden der virtuelle Computer und der HBase-Cluster denselben internen DNS-Server für die Auflösung von Hostnamen. Hierfür müssen Sie die Option **Aus Katalog** und das virtuelle Netzwerk statt eines Datencenters auswählen. Anweisungen dazu finden Sie unter [Erstellen eines virtuellen Windows Server-Computers](../virtual-machines-windows-tutorial.md). Ein standardmäßiges Windows Server 2012-Image mit kleinem virtuellen Computer ist ausreichend.

2.	Für Remote-Verbindungen zwischen Java-Anwendungen und HBase müssen Sie den vollqualifizierten Domänennamen (FQDN) verwenden. Rufen Sie hierzu das verbindungsspezifische DNS-Suffix des HBase-Clusters ab. Dazu können Sie entweder Ambari mit Curl abfragen oder per Remotedesktop eine Verbindung mit dem Cluster herstellen.

	* **Curl** – Verwenden Sie den folgenden Befehl:

			curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

		Suchen Sie in den zurückgegebenen JSON-Daten (JavaScript Object Notation) den Eintrag "host\_name". Dieser Eintrag enthält den vollqualifizierten Domänennamen (FQDN) für die Knoten im Cluster. Beispiel:

			...
			"host_name": "wordkernode0.<clustername>.b1.cloudapp.net
			...

		Der Teil des Domänennamens, der mit dem Clusternamen beginnt, ist das DNS-Suffix. Zum Beispiel: mycluster.b1.cloudapp.net.

	* **Azure PowerShell** – Führen Sie das folgende Azure PowerShell-Skript aus, um die **Get-ClusterDetail**-Funktion zu registrieren, mit der Sie das DNS-Suffix zurückgeben können:

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
			     Displays information to facilitate an HDInsight cluster-to-cluster scenario within the same virtual network.
				.Description
				 This command shows the following 4 properties of an HDInsight cluster:
				 1. ZookeeperQuorum (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.quorum".
				 2. ZookeeperClientPort (supports only HBase type cluster)
					Shows the value of HBase property "hbase.zookeeper.property.clientPort".
				 3. HBaseRestServers (supports only HBase type cluster)
					Shows a list of host FQDNs that run the HBase REST server.
				 4. FQDNSuffix (supports all cluster types)
					Shows the FQDN suffix of hosts in the cluster.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperQuorum
			     This command shows the value of HBase property "hbase.zookeeper.quorum".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName ZookeeperClientPort
			     This command shows the value of HBase property "hbase.zookeeper.property.clientPort".
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName HBaseRestServers
			     This command shows a list of host FQDNs that run the HBase REST server.
			    .EXAMPLE
			     Get-ClusterDetail -ClusterDnsName {clusterDnsName} -Username {username} -Password {password} -PropertyName FQDNSuffix
			     This command shows the FQDN suffix of hosts in the cluster.
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

		Verwenden Sie nach der Ausführung des Azure PowerShell-Skripts den folgenden Befehl, um das DNS-Suffix mithilfe der **Get-ClusterDetail**-Funktion zurückzugeben. Geben Sie den Namen Ihres HDInsight HBase-Clusters sowie den Admin-Namen und das Admin-Kennwort an, wenn Sie diesen Befehl ausführen.

			Get-ClusterDetail -ClusterDnsName <yourclustername> -PropertyName FQDNSuffix -Username <clusteradmin> -Password <clusteradminpassword>

		Daraufhin wird das DNS-Suffix zurückgegeben. Zum Beispiel: **yourclustername.b4.internal.cloudapp.net**.

	> [AZURE.NOTE]Sie können auch per Remotedesktop eine Verbindung mit dem HBase-Cluster herstellen (wobei Sie eine Verbindung mit dem Hauptknoten herstellen) und **ipconfig** in einer Eingabeaufforderung ausführen, um das DNS-Suffix abzurufen. Anweisungen zum Aktivieren des Remotedesktopprotokolls (RDP) und zum Herstellen einer Verbindung mit dem Cluster mithilfe des RDP finden Sie unter [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Portal][hdinsight-admin-portal].
	>
	> ![hdinsight.hbase.dns.surffix][img-dns-surffix]


<!--
3.	Change the primary DNS suffix configuration of the virtual machine. This enables the virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to workernode0 of the HBase cluster.

	To make the configuration change:

	1. RDP into the virtual machine.
	2. Open **Local Group Policy Editor**. The executable is gpedit.msc.
	3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.
	- Set **Primary DNS Suffix** to the value obtained in step 2:

		![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]
	4. Click **OK**.
	5. Reboot the virtual machine.
-->

Führen Sie den Befehl `ping headnode0.<dns suffix>` auf dem virtuellen Computer aus, um zu überprüfen, ob der virtuelle Computer mit dem HBase-Cluster kommunizieren kann. Beispiel: ping headnode0.mycluster.b1.cloudapp.net.

Führen Sie die unter [Verwenden von Maven zur Entwicklung von Java-Anwendungen, die HBase mit HDInsight (Hadoop) nutzen](hdinsight-hbase-build-java-maven.md) beschriebenen Schritte aus, um diese Informationen in einer Java-Anwendung zu verwenden. Wenn die Anwendung eine Verbindung mit einem HBase-Remoteserver herstellen soll, müssen Sie den FQDN für Zookeeper in der Datei **hbase-site.xml** eintragen. Beispiel:

	<property>
    	<name>hbase.zookeeper.quorum</name>
    	<value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
	</property>

> [AZURE.NOTE]Weitere Informationen zur Namensauflösung in virtuellen Azure-Netzwerken und zur Verwendung eigener DNS-Server finden Sie unter [Namensauflösung (DNS)](../virtual-network/virtual-networks-name-resolution-for-vms-and-role-instances.md).

##Bereitstellen von HBase-Clustern mit Azure PowerShell

**So stellen Sie einen HBase-Cluster mit Azure PowerShell bereit**

1. Öffnen Sie die Azure PowerShell Integrated Scripting Environment (ISE).
2. Kopieren Sie den folgenden Code, und fügen Sie ihn in den Skriptbereich ein:

		$hbaseClusterName = "<HBaseClusterName>"
		$hadoopUserName = "<HBaseClusterUsername>"
		$hadoopUserPassword = "<HBaseClusterUserPassword>"
		$location = "<HBaseClusterLocation>"  #i.e. "West US"
		$clusterSize = <HBaseClusterSize>  
		$resourceGroup = "<AzureResourceGroupName>"
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"
		$storageAccountName = "<AzureStorageAccountName>" # Do not use the full name here
		$storageAccountKey = "<AzureStorageAccountKey>"
		$storageContainerName = "<AzureBlobStorageContainer>"

		$password = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ($hadoopUserName, $password)

		New-AzureHDInsightCluster -ResourceGroupName $resourceGroup `
		                          -ClusterName $hbaseClusterName `
				                    	-ClusterType HBase `
				                    	-Location $location `
				                    	-ClusterSizeInNodes $clusterSize `
		                          -HttpCredential $creds `
				                    	-VirtualNetworkId $vnetID `
				                    	-SubnetName $subNetName `
				                    	-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
				                    	-DefaultStorageAccountKey $storageAccountKey `
		                          -DefaultStorageContainer $storageContainerName


3. Klicken Sie auf **Skript ausführen** oder drücken Sie **F5**.
4. Wenn Sie den Cluster überprüfen möchten, können Sie ihn entweder über das Azure-Portal prüfen oder im unteren Bereich das folgende Azure PowerShell-Cmdlet ausführen:

	Get-AzureHDInsightCluster

##Nächste Schritte

In diesem Lernprogramm haben Sie erfahren, wie Sie einen HBase-Cluster bereitstellen. Weitere Informationen finden Sie unter:

- [Erste Schritte mit HDInsight](../hdinsight-get-started.md)
- [Konfigurieren der HBase-Replikation in HDInsight](hdinsight-hbase-geo-replication.md)
- [Bereitstellen von Hadoop-Clustern in HDInsight](hdinsight-provision-clusters.md)
- [Erste Schritte mit HBase mit Hadoop in HDInsight](../hdinsight-hbase-get-started.md)
- [Analysieren der Twitter-Stimmungen mit HBase in HDInsight](../hdinsight-hbase-twitter-sentiment.md)
- [Virtuelle Netzwerke im Überblick][vnet-overview]


[1]: http://azure.microsoft.com/services/virtual-network/
[2]: http://technet.microsoft.com/library/ee176961.aspx
[3]: http://technet.microsoft.com/library/hh847889.aspx

[hbase-get-started]: ../hdinsight-hbase-get-started.md
[hbase-twitter-sentiment]: ../hdinsight-hbase-twitter-sentiment.md
[vnet-overview]: ../virtual-network/virtual-networks-overview.md
[vm-create]: ../virtual-machines-windows-tutorial.md

[azure-portal]: https://portal.azure.com
[azure-create-storageaccount]: ../storage-create-storage-account.md
[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-admin-portal]: hdinsight-administer-use-management-portal.md#rdp

[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx


[twitter-streaming-api]: https://dev.twitter.com/docs/streaming-apis
[twitter-statuses-filter]: https://dev.twitter.com/docs/api/1.1/post/statuses/filter


[powershell-install]: ../install-configure-powershell.md


[hdinsight-customize-cluster]: hdinsight-hadoop-customize-cluster.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-storage-powershell]: ../hdinsight-use-blob-storage.md#powershell
[hdinsight-analyze-flight-delay-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md
[hdinsight-hive-odbc]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-hbase-replication-dns]: hdinsight-hbase-geo-replication-configure-DNS.md

[img-dns-surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
[img-primary-dns-suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
[img-provision-cluster-page1]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png "Bereitstellen von Details für den neuen HBase-Cluster"
[img-provision-cluster-page5]: ./media/hdinsight-hbase-provision-vnet/hbasewizard5.png "Verwenden von Skriptaktionen zum Anpassen eines HBase-Clusters"

[azure-preview-portal]: https://portal.azure.com

<!---HONumber=Oct15_HO3-->