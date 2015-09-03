<properties
	pageTitle="Bereitstellen von HBase-Clustern in einem virtuellen Netzwerk | Microsoft Azure"
	description="Erste Schritte mit HBase in Azure HDInsight. Erfahren Sie, wie Sie HDInsight HBase-Cluster in Azure Virtual Network erstellen können."
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
   ms.date="08/07/2015"
   ms.author="jgao"/>

# Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk

Erfahren Sie, wie Sie Azure HDInsight HBase-Cluster in einem [Azure Virtual Network][1] erstellen können.

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk](hdinsight-hbase-provision-vnet.md)

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


##Bereitstellen von HBase-Clustern in einem virtuellen Netzwerk

Vor der Bereitstellung eines HBase-Clusters müssen Sie ein virtuelles Azure-Netzwerk erstellen.

**So erstellen Sie ein virtuelles Netzwerk im Azure-Portal**

1. Melden Sie sich beim [Azure-Portal][azure-portal] an.
2. Klicken Sie links unten auf **NEU**, klicken Sie auf **NETWORK SERVICES**, auf **VIRTUAL NETWORK** und dann auf **SCHNELLERFASSUNG**.
3. Geben Sie die folgenden Werte ein, oder wählen Sie sie aus:

	- **Name** – Name des virtuellen Netzwerks.
	- **Adressraum** – Wählen Sie einen Adressraum für das virtuelle Netzwerk, der groß genug ist, um Adressen für alle Knoten im Cluster zur Verfügung zu stellen. Andernfalls schlägt die Bereitstellung fehl. Für dieses Lernprogramm haben Sie die Wahl zwischen den drei verfügbaren Optionen.
	- **Maximale Anzahl virtueller Computer** – Wählen Sie einen Wert für die maximale Anzahl virtueller Computer aus. Dieser Wert bestimmt die Anzahl möglicher Hosts (VM), die in dem Adressbereich erstellt werden können. Für dieses Lernprogramm ist **4096 [CIDR: /20]** ausreichend.
	- **Speicherort** – Der Speicherort muss derselbe sein wie der des zu erstellenden HBase-Clusters.
	- **DNS-Server** – In diesem Lernprogramm wird ein interner von Azure bereitgestellter DNS-Server (Domain Name System) verwendet. Daher können Sie **Kein** auswählen. Erweiterte Netzwerkkonfigurationen mit benutzerdefinierten DNS-Servern werden ebenfalls unterstützt. Ausführliche Anleitungen finden Sie unter [Namensauflösung (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx).
4. Klicken Sie unten rechts auf **VIRTUELLES NETZWERK ERSTELLEN**. Der Name des neuen virtuellen Netzwerks wird in der Liste angezeigt. Warten Sie, bis in der Statusspalte **Created** angezeigt wird.
5. Klicken Sie im Hauptbereich auf das virtuelle Netzwerk, das Sie gerade erstellt haben.
6. Klicken Sie oben auf der Seite auf **DASHBOARD**.
7. Notieren Sie sich die ID des virtuellen Netzwerks. Sie finden diese unter **Auf einen Blick**. Sie benötigen die ID, wenn Sie den HBase-Cluster bereitstellen.
8. Klicken Sie oben auf der Seite auf **KONFIGURIEREN**.
9. Unten auf der Seite finden Sie den Namen des Standardsubnetzes. Dieser lautet **Subnet-1**. Sie können das Subnetz optional umbenennen oder ein neues Subnetz für den HBase-Cluster hinzufügen. Notieren Sie sich den Namen des Subnetzes. Sie benötigen ihn bei der Bereitstellung des Clusters.
10. Prüfen Sie den Wert unter **CIDR(ANZAHL VON ADRESSEN)** für das Subnetz, das für den Cluster verwendet wird. Die Anzahl der Adressen muss größer als die Anzahl der Workerknoten plus sieben sein (Gateway: 2, Hauptknoten: 2, Zookeeper: 3). Wenn Sie zum Beispiel einen HBase-Cluster mit 10 Knoten benötigen, muss die Anzahl der Adressen für das Subnetz größer als 17 (10 + 7) sein. Andernfalls schlägt die Bereitstellung fehl.
11. Klicken Sie unten auf der Seite auf **Speichern**, wenn Sie die Werte für das Subnetz aktualisiert haben.


**So fügen Sie dem virtuellen Netzwerk einen virtuellen Computer als DNS-Server hinzu**

Ein DNS-Server ist optional, aber in einigen Fällen erforderlich. Die Vorgehensweise wird unter [Konfigurieren von DNS zwischen zwei virtuellen Netzwerken in Azure][hdinsight-hbase-replication-dns] beschrieben. Im Wesentlichen müssen Sie die folgenden Schritte ausführen:

1. Hinzufügen eines virtuellen Azure-Computers zum virtuellen Netzwerk
2. Festlegen einer statischen IP-Adresse für den virtuellen Computer
3. Hinzufügen der DNS-Serverrolle zum virtuellen Computer
4. Zuweisen des DNS-Servers zum virtuellen Netzwerk


**So erstellen Sie ein Azure-Speicherkonto und einen Blobspeichercontainer zur Verwendung im Cluster**

> [AZURE.NOTE]HDInsight-Cluster nutzen den Azure-BLOB-Speicher zum Speichern von Daten. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight](../hdinsight-use-blob-storage.md). Sie benötigen ein Speicherkonto und einen Blob-Speichercontainer. Der Speicherort des Speicherkontos muss dem Speicherort des virtuellen Netzwerks und des Clusters entsprechen.

Wie andere HDInsight-Cluster erfordert ein HBase-Cluster ein Azure-Speicherkonto und einen Blobspeichercontainer als Standarddateisystem. Der Speicherort des Speicherkontos muss dem Speicherort des virtuellen Netzwerks und des Clusters entsprechen. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][hdinsight-storage]. Wenn Sie einen HBase-Cluster bereitstellen, können Sie entweder ein neues Speicherkonto und einen neuen Blobspeichercontainer erstellen oder ein vorhandenes Speicherkonto und einen vorhandenen Blobspeichercontainer verwenden. Mit den folgenden Schritten wird veranschaulicht, wie Sie ein Speicherkonto und einen Blobspeichercontainer im Azure-Portal erstellen.

1. Melden Sie sich beim [Azure-Portal][azure-portal] an.
2. Klicken Sie links unten auf **NEU**, zeigen Sie auf **DATA SERVICES**, auf **SPEICHER**, und klicken Sie anschließend auf **SCHNELLERFASSUNG**.

3. Geben Sie die folgenden Werte ein, oder wählen Sie sie aus:

	- **URL** – Der Name des Speicherkontos.
	- **SPEICHERORT** – Der Speicherort des Speicherkontos. Stellen Sie sicher, dass er dem Speicherort des virtuellen Netzwerks entspricht. Affinitätsgruppen werden nicht unterstützt.
	- **REPLIKATION** – Nutzen Sie zu Testzwecken **Lokal redundant**, um Kosten zu sparen.

4. Klicken Sie auf **SPEICHERKONTO ERSTELLEN**. Sie werden das neue Speicherkonto in der Speicherliste sehen.
5. Warten Sie, bis sich der **STATUS** des neuen Speicherkontos in **Online** geändert hat.
6. Klicken Sie in der Liste auf das neue Speicherkonto, und wählen Sie es aus.
7. Klicken Sie am Seitenende auf **ZUGRIFFSSCHLÜSSEL VERWALTEN**.
8. Notieren Sie sich den Namen des Speicherkontos und den primären Zugriffsschlüssel (oder den sekundären Zugriffsschlüssel – beide Schlüssel funktionieren). Sie werden diese später im Lernprogramm benötigen.
9. Klicken Sie oben auf der Seite auf **CONTAINER**.
10. Klicken Sie unten auf der Seite auf **HINZUFÜGEN**.
11. Geben Sie den Containernamen ein. Dieser Container wird als Standardcontainer für den HBase-Cluster verwendet. Standardmäßig entspricht der Standard-Containername dem Clusternamen. Wählen Sie für das Feld **ACCESS** die Einstellung **Private** aus.  
12. Klicken Sie auf das Häkchen, um den Container zu erstellen.

**So stellen Sie einen HBase-Cluster im Azure-Portal bereit**

> [AZURE.NOTE]Informationen zum Bereitstellen eines neuen HBase-Clusters mit Azure PowerShell finden Sie unter [Bereitstellen eines HBase-Clusters mit Azure PowerShell](#powershell).

1. Melden Sie sich beim [Azure-Portal][azure-portal] an.

2. Klicken Sie unten links auf **NEU**, zeigen Sie auf **DATA SERVICES** und auf **HDINSIGHT**, und klicken Sie dann auf **BENUTZERDEFINIERT ERSTELLEN**.

3. Geben Sie einen Namen für den Cluster ein, wählen Sie "HBase" als Clustertyp aus, wählen Sie das Betriebssystem Windows Server 2012 sowie die HDInsight-Version aus, und klicken Sie dann auf die rechte Schaltfläche.

	![Angeben von Details für den HBase-Cluster][img-provision-cluster-page1]


	> [AZURE.NOTE]Für einen HBase-Cluster ist lediglich "Windows Server" als Option für das Betriebssystem verfügbar.

4. Auf der Seite **Cluster konfigurieren** können Sie die folgenden Werte auswählen bzw. eingeben:

	![Angeben von Details für den HBase-Cluster](./media/hdinsight-hbase-provision-vnet/hbasewizard2.png)

	<table border='1'>
	<tr><th>Eigenschaft</th><th>Wert</th></tr>
	<tr><td>Datenknoten</td><td>Wählen Sie die Anzahl der Datenknoten, die Sie bereitstellen möchten. Erstellen Sie zu Testzwecken einen Cluster mit nur einem Knoten. <br />Die Größenbegrenzung für die Cluster variiert in Azure-Abonnements. Wenden Sie sich an das Azure-Abrechnungssupportteam, um diese Begrenzung zu erhöhen.</td></tr>
	<tr><td>Region/virtuelles Netzwerk:</td><td><p>Wählen Sie eine Region oder ein virtuelles Netzwerk in Azure, wenn Sie bereits eines erstellt haben. Wählen Sie für dieses Lernprogramm das zuvor erstellte Netzwerk und dann ein entsprechendes Subnetz aus. Der Standardname lautet <b>Subnet-1</b>.</p></td></tr>
	<tr><td>Größe des Hauptknotens</td><td><p>Wählen Sie eine VM-Größe für den Hauptknoten aus.</p></td></tr>
	<tr><td>Datenknotengröße</td><td><p>Wählen Sie eine VM-Größe für die Datenknoten aus.</p></td></tr>
	<tr><td>Zookeeper-Größe</td><td><p>Wählen Sie eine VM-Größe für den Zookeeper-Knoten aus.</p></td></tr>
	</table>

	>[AZURE.NOTE]Je nach Wahl der VMs können Ihre Kosten variieren. HDInsight verwendet für Clusterknoten VMs mit Standardtarif. Informationen über die Auswirkungen der VM-Größe auf Ihre Kosten finden Sie unter <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight-Preise</a>.

	Klicken Sie auf die rechte Taste.

5. Geben Sie den Hadoop-Benutzernamen und das Kennwort für diesen Cluster ein, und klicken Sie dann auf die rechte Schaltfläche.

	![Bereitstellen eines Speicherkontos für Hadoop HDInsight-Cluster](./media/hdinsight-hbase-provision-vnet/hbasewizard3.png)

	<table border='1'>
	<tr><th>Eigenschaft</th><th>Wert</th></tr>
	<tr><td>HTTP-Benutzername</td>
		<td>Geben Sie den Benutzernamen für den HDInsight-Cluster an.</td></tr>
	<tr><td>HTTP-Kennwort/Kennwort bestätigen</td>
		<td>Geben Sie das Kennwort für den HDInsight-Cluster an.</td></tr>
	<tr><td>Remotedesktop für Cluster aktivieren</td>
		<td>Aktivieren Sie dieses Kontrollkästchen, um einen Benutzernamen, ein Kennwort und ein Ablaufdatum für einen Remotedesktopbenutzer einzugeben, der nach der Bereitstellung per Remotezugriff auf die Clusterknoten zugreifen kann. Sie können Remotedesktop auch noch nach der Bereitstellung des Clusters aktivieren. Anweisungen hierzu finden Sie unter <a href="hdinsight-administer-use-management-portal/#rdp" target="_blank">Herstellen einer Verbindung mit HDInsight-Clustern mit RDP</a>.</td></tr>
	</table>

6. Geben Sie auf der Seite **Speicherkonto** die folgenden Werte ein:

    ![Bereitstellen eines Speicherkontos für Hadoop HDInsight-Cluster](./media/hdinsight-hbase-provision-vnet/hbasewizard4.png)

<table border='1'>
	<tr><th>Eigenschaft</th><th>Wert</th></tr>
	<tr><td>Speicherkonto</td>
		<td>Geben Sie das Azure-Speicherkonto an, das als Standard-Dateisystem für das HDInsight-Cluster verwendet werden soll. Sie haben drei Möglichkeiten:
		<ul>
			<li><strong>Vorhandenen Speicher verwenden</strong></li>
			<li><strong>Neuen Speicher erstellen</strong></li>
			<li><strong>Speicher aus anderem Abonnement verwenden</strong></li>
		</ul>
		</td></tr>
	<tr><td>Kontoname</td>
		<td><ul>
			<li>Wenn Sie einen vorhandenen Speicher verwenden, wählen Sie unter <strong>Kontoname</strong> ein vorhandenes Speicherkonto aus. Die Dropdownliste enthält nur die Storage-Konten in dem Datencenter, in dem Sie auch den Cluster eingerichtet haben.</li>
			<li>Wenn Sie <strong>Neuen Speicher erstellen</strong> oder <strong>Speicher aus einem anderem Abonnement verwenden</strong> ausgewählt haben, müssen Sie den Namen des Storage-Kontos angeben.</li>
		</ul></td></tr>
	<tr><td>Kontoschlüssel</td>
		<td>Geben Sie den Speicherschlüssel für das entsprechende Konto ein, falls Sie <strong>Speicher aus anderem Abonnement verwenden</strong> ausgewählt haben.</td></tr>
	<tr><td>Standardcontainer</td>
		<td><p>Gibt den Standardcontainer im Speicherkonto an, der als Standarddateisystem für den HDInsight-Cluster verwendet werden soll. Wenn Sie <strong>Vorhandenen Speicher verwenden</strong> für das Feld <strong>Speicherkonto</strong> wählen und in dem betreffenden Konto keine Container vorhanden sind, wird der Container standardmäßig mit demselben Namen wie der des Clusters erstellt. Falls bereits ein Container mit dem Namen des Clusters existiert, wird eine Sequenznummer an den Containernamen angehängt. Zum Beispiel mycontainer1, mycontainer2 und so weiter. Sie können jedoch auch Container im vorhandenen Speicherkonto verwenden, die einen anderen Namen als der Cluster haben.</p>
        <p>Falls Sie einen neuen Speicher erstellen oder einen Speicher aus einem anderen Azure-Abonnement verwenden, müssen Sie den Namen des Standardcontainers angeben.</p>
    </td></tr>
	<tr><td>Zusätzliche Speicherkonten</td>
		<td>Geben Sie bei Bedarf weitere Storage-Konten für den Cluster an. HDInsight unterstützt mehrere Speicherkonten. Es gibt keine Beschränkung in Bezug auf die zusätzlichen Speicherkonten, die von einem Cluster verwendet werden können. Wenn Sie den Cluster jedoch im Azure-Portal erstellen, können Sie aufgrund von Einschränkungen der Benutzeroberfläche maximal sieben Speicherkonten einrichten. Für jedes angegebene Storage-Konto wird eine zusätzliche Seite <strong>Speicherkonto</strong> im Assistenten hinzugefügt, auf der Sie die Kontoinformationen angeben können. Im oben abgebildeten Screenshot wurde z. B. kein zusätzliches Speicherkonto hinzugefügt. Daher wurde dem Assistenten auch keine zusätzliche Seite hinzugefügt.</td></tr>
</table>
Klicken Sie auf den Pfeil nach rechts.

7. Klicken Sie auf der Seite **Skriptaktionen** unten rechts auf das Häkchen. Klicken Sie nicht auf die Schaltfläche **Skriptaktion hinzufügen**, da in diesem Lernprogramm kein angepasstes Clustersetup erforderlich ist.

	![Konfigurieren von Skriptaktionen zum Anpassen eines HDInsight HBase-Clusters][img-provision-cluster-page5]

	> [AZURE.NOTE]Diese Seite kann zum Anpassen des Clusters während des Setups verwendet werden. Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster.md).

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

> [AZURE.NOTE]Weitere Informationen zur Namensauflösung in virtuellen Azure-Netzwerken und zur Verwendung eigener DNS-Server finden Sie unter [Namensauflösung (DNS)](http://msdn.microsoft.com/library/azure/jj156088.aspx).

##Bereitstellen von HBase-Clustern mit Azure PowerShell

**So stellen Sie einen HBase-Cluster mit Azure PowerShell bereit**

1. Öffnen Sie die Azure PowerShell Integrated Scripting Environment (ISE).
2. Kopieren Sie den folgenden Code, und fügen Sie ihn in den Skriptbereich ein:

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
[vnet-overview]: http://msdn.microsoft.com/library/azure/jj156007.aspx
[vm-create]: ../virtual-machines-windows-tutorial.md

[azure-portal]: https://management.windowsazure.com
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

<!-----HONumber=August15_HO8-->