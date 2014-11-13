<properties urlDisplayName="Provision HBase clusters on Azure Virtual Network" pageTitle="Bereitstellen von HBase-Clustern in einem virtuellen Azure-Netzwerk | Azure" metaKeywords="" description="Erfahren Sie, wie Sie HDInsight-Cluster im virtuellen Azure-Netzwerk erstellen k&ouml;nnen." metaCanonical="" services="hdinsight" documentationCenter="" title="Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao" />

# Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk

Erfahren Sie, wie Sie HDInsight-Cluster im virtuellen Azure-Netzwerk erstellen können.

Mit der Integration in virtuelle Netzwerke können HBase-Cluster im selben virtuellen Netzwerk bereitgestellt werden wie Ihre Anwendungen, sodass Anwendungen direkt mit HBase kommunizieren können. Es ergeben sich folgende Vorteile:

-   Direkte Konnektivität der Webanwendungen mit den Knoten des HBase-Clusters, was Kommunikation unter Verwendung von HBase Java RPC APIs ermöglicht
-   Optimierte Leistung, da Datenverkehr nicht über mehrere Gateways und Lastenausgleiche fließt
-   Mehr Sicherheit bei der Verarbeitung sensibler Informationen ohne exponierten öffentlichen Endpunkt

## Themen in diesem Artikel

-   [Voraussetzungen][Voraussetzungen]
-   [Bereitstellen von HBase-Clustern in einem virtuellen Netzwerk][Bereitstellen von HBase-Clustern in einem virtuellen Netzwerk]
-   [Herstellen einer Verbindung mit dem im virtuellen Netzwerk bereitgestellten HBase-Cluster unter Verwendung von HBase Java RPC APIs][Herstellen einer Verbindung mit dem im virtuellen Netzwerk bereitgestellten HBase-Cluster unter Verwendung von HBase Java RPC APIs]
-   [Bereitstellen eines HBase-Clusters mit PowerShell][Bereitstellen eines HBase-Clusters mit PowerShell]
-   [Nächste Schritte][Nächste Schritte]

## <span id="prerequisites"></span></a>Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   **Ein Azure-Abonnement**. Azure ist eine abonnementbasierte Plattform. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][Kaufoptionen], [Spezielle Angebote][Spezielle Angebote] oder [Kostenlose Testversion][Kostenlose Testversion].

-   **Eine Arbeitsstation, auf der Azure PowerShell installiert und konfiguriert ist**. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][Installieren und Konfigurieren von Azure PowerShell]. Um PowerShell-Skripts ausführen zu können, müssen Sie Azure PowerShell als Administrator ausführen und die Ausführungsrichtlinie auf *RemoteSigned* setzen. Siehe [Ausführen von Windows PowerShell-Skripts][Ausführen von Windows PowerShell-Skripts].

    Stellen Sie vor dem Ausführen von PowerShell-Skripts mithilfe des folgenden Cmdlets sicher, dass Sie mit Ihrem Azure-Abonnement verbunden sind:

        Add-AzureAccount

    Wenn Sie mehrere Azure-Abonnements haben, legen Sie das aktuelle Abonnement mit dem folgenden Cmdlet fest:

        Select-AzureSubscription <AzureSubscriptionName>

## <span id="hbaseprovision"></span></a>Bereitstellen eines HBase-Clusters in einem virtuellen Netzwerk

**So erstellen Sie ein virtuelles Netzwerk im Verwaltungsportal:**

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie unten auf der Seite auf **NEU**, **NETWORK SERVICES**, **VIRTUELLES NETZWERK** und dann auf **SCHNELLERFASSUNG**.
3.  Geben Sie folgende Werte ein bzw. wählen diese aus:

    -   **Name**: Der Name des virtuellen Netzwerks.
    -   **Adressraum**: Wählen Sie einen Adressraum für das virtuelle Netzwerk, der groß genug ist, um Adressen für alle Knoten im Cluster zur Verfügung zu stellen. Andernfalls schlägt die Bereitstellung fehl.
    -   **Maximale Anzahl virtueller Computer**: Wählen Sie eine maximale Anzahl an virtuellen Computern aus.
    -   **Speicherort**: Der Speicherort muss derselbe sein wie der des HBase-Clusters, den Sie erstellen.
    -   **DNS-Server**: In diesem Artikel werden interne DNS-Server verwendet, die von Azure bereitgestellt werden. Daher können Sie **None** auswählen. Erweiterte Netzwerkkonfiguration mit benutzerdefinierten DNS-Servern wird ebenfalls unterstützt. Eine genaue Anleitung finden Sie unter [][]<http://msdn.microsoft.com/library/azure/jj156088.aspx></a>.

4.  Klicken Sie auf **CREATE A VIRTUAL NETWORK**. Der Name des neuen virtuellen Netzwerks wird in der Liste angezeigt. Warten Sie, bis in der Statusspalte **Created** angezeigt wird.
5.  Klicken Sie im Hauptbereich auf das virtuelle Netzwerk, das Sie gerade erstellt haben.
6.  Klicken Sie oben auf der Seite auf **DASHBOARD**.
7.  Notieren Sie sich die **VIRTUAL NETWORK ID**. Sie finden diese unter **Schnelleinsicht**. Sie benötigen die ID, wenn Sie den HBase-Cluster bereitstellen.
8.  Klicken Sie oben auf der Seite auf **KONFIGURIEREN**.
9.  Unten auf der Seite finden Sie den Namen des Standardsubnetzes. Dieser lautet **Subnet-1**. Sie können das Subnetz optional umbenennen oder ein neues Subnetz für den HBase-Cluster hinzufügen. Notieren Sie sich den Namen des Subnetzes. Sie benötigen ihn bei der Bereitstellung des Clusters.
10. Prüfen Sie den **CIDR(ANZAHL VON ADRESSEN)** für das Subnetz für die Verwendung im Cluster. Die Anzahl der Adressen muss größer als die Anzahl der Workerknoten plus Sieben sein (Gateway: 2, Hauptknoten: 2, Zookeeper: 3). Wenn Sie zum Beispiel einen HBase-Cluster mit 10 Knoten benötigen, muss die Anzahl der Adressen für das Subnetz größer als 17 (10 + 7) sein. Andernfalls schlägt die Bereitstellung fehl.

    > [WACOM.NOTE] Es wird dringend empfohlen, ein einzelnes Subnetz einem einzelnen Cluster zuzuordnen.

11. Klicken Sie unten auf der Seite auf **Speichern**, wenn Sie die Werte für das Subnetz aktualisiert haben.

> [WACOM.NOTE] HDInsight-Cluster nutzen den Azure-Blob-Speicher für das Speichern von Daten. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][Verwenden von Azure Blob-Speicher mit HDInsight]. Sie benötigen ein Speicherkonto und einen Blob-Speichercontainer. Der Speicherort des Speicherkontos muss dem Speicherort des virtuellen Netzwerks und des Clusters entsprechen.

**So erstellen Sie ein Azure-Speicherkonto und einen Blob-Speichercontainer:**

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie links unten auf **NEU**, zeigen Sie auf **DATA SERVICES**, auf **SPEICHERN**, und klicken Sie anschließend auf **SCHNELLERFASSUNG**.

3.  Geben Sie folgende Werte ein bzw. wählen diese aus:

    -   **URL**: Der Name des Speicherkontos.
    -   **SPEICHERORT**: Der Speicherort des Speicherkontos. Stellen Sie sicher, dass er dem Speicherort des virtuellen Netzwerks entspricht. Affinitätsgruppen werden nicht unterstützt.
    -   **REPLIKATION**: Nutzen Sie zu Testzwecken **Lokal redundant**, um Kosten zu sparen.

4.  Klicken Sie auf **SPEICHERKONTO ERSTELLEN**. Sie werden das neue Speicherkonto in der Speicherliste sehen.
5.  Warten Sie, bis sich der **STATUS** des neuen Speicherkontos auf **Online** geändert hat.
6.  Klicken Sie in der Liste auf das neue Speicherkonto, und wählen Sie es aus.
7.  Klicken Sie am Seitenende auf **ZUGRIFFSSCHLÜSSEL VERWALTEN**.
8.  Notieren Sie sich den **NAMEN DES SPEICHERKONTOS** und den **PRIMÄREN ZUGRIFFSSCHLÜSSEL** (oder den **SEKUNDÄREN ZUGRIFFSSCHLÜSSEL**. Beide können verwendet werden). Sie werden diese später im Lernprogramm benötigen.
9.  Klicken Sie oben auf der Seite auf **CONTAINER**.
10. Klicken Sie unten auf der Seite auf **HINZUFÜGEN**.
11. Geben Sie den Containernamen ein. Dieser Container wird als Standardcontainer für den HBase-Cluster verwendet. Standardmäßig entspricht der Standard-Containername dem Clusternamen. Wählen Sie für das Feld **ACCESS** die Einstellung **Private** aus.
12. Aktivieren Sie das Kontrollkästchen, um den Container zu erstellen.

**So stellen Sie ein HBase-Cluster im Azure-Portal bereit:**

> [WACOM.NOTE] Weitere Informationen zum Bereitstellen eines neuen HBase-Clusters mit PowerShell finden Sie unter [Bereitstellen eines HBase-Clusters mit PowerShell][Bereitstellen eines HBase-Clusters mit PowerShell].

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an.
2.  Klicken Sie unten links auf **NEU**, zeigen Sie auf **DATA SERVICES**, dann auf **HDINSIGHT** und klicken Sie auf **BENUTZERDEFINIERTE ERSTELLUNG**.
3.  Geben Sie einen **CLUSTERNAMEN** ein und wählen Sie eine **HDINSIGHT-VERSION** für das Cluster aus.

    ![Clustername und Version][Clustername und Version]

4.  Wählen Sie die Anzahl der zu erstellenden **DATENKNOTEN** für das Cluster und die Region bzw. das virtuelle Netzwerk (**REGION/VIRTUELLES NETZWERK**) für das Cluster aus.

    ![Anzahl der Knoten und Region][Anzahl der Knoten und Region]

5.  Geben Sie **BENUTZERNAME** und **KENNWORT** für den Administrator des Clusters ein.

    ![Administratorname und Kennwort][Administratorname und Kennwort]

6.  Wählen Sie aus, ob Sie ein neues oder ein vorhandenes Speicherkonto verwenden möchten. Geben Sie **KONTONAME** und **STANDARDCONTAINER** ein, falls Sie ein neues Speicherkonto verwenden möchten. Markieren Sie zuletzt das Häkchen, um das Cluster zu erstellen.

    ![Speicherkonto-Auswahl][Speicherkonto-Auswahl]

Führen Sie die Schritte unter [Erste Schritte mit HBase mit Hadoop in HDInsight][Erste Schritte mit HBase mit Hadoop in HDInsight] aus, um mit Ihrem neu erstellten HBase-Cluster zu arbeiten.

## <span id="connect"></span></a>Herstellen einer Verbindung mit dem im virtuellen Netzwerk bereitgestellten HBase-Cluster unter Verwendung von HBase Java RPC APIs

1.  Stellen Sie einen virtuellen IaaS-Computer im selben virtuellen Azure-Netzwerk und im selben Subnetz bereit. Dadurch verwenden der virtuelle Computer und der HBase-Cluster denselben internen DNS-Server für die Auflösung von Hostnamen. Hierfür müssen Sie die Option "Aus Galerie" auswählen und das virtuelle Netzwerk statt eines Rechenzentrums wählen. Anweisungen dazu finden Sie unter [Erstellen eines virtuellen Windows Server-Computers][Erstellen eines virtuellen Windows Server-Computers]. Ein standardmäßiges Windows Server 2012-Image mit kleinem virtuellen Computer ist ausreichend.

2.  Für Remote-Verbindungen zwischen Java-Anwendungen und HBase müssen Sie den vollqualifizierten Domänennamen (FQDN) verwenden. Rufen Sie hierzu das verbindungsspezifische DNS-Suffix des HBase-Clusters ab. Dazu können Sie entweder Ambari mit Curl abfragen oder sich per Remotedesktop mit dem Cluster verbinden.

    -   **Curl** - führen Sie den folgenden Befehl aus:

            curl -u <username>:<password> -k https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/hbase/components/hbrest

        Suchen Sie im zurückgegebenen JSON nach dem Eintrag "host\_name". Dieser Eintrag enthält den vollqualifizierten Domänennamen (FQDN) für die Knoten im Cluster. Beispiel:

            ...
            "host_name": "wordkernode0.<clustername>.b1.cloudapp.net
            ...

        Der Teil des Domänennamens, der mit dem Clusternamen beginnt, ist das DNS-Suffix. Zum Beispiel: mycluster.b1.cloudapp.net.

    -   **PowerShell** - Führen Sie das folgende PowerShell-Skript aus, um die **Get-ClusterDetail**-Funktion zu registrieren, mit der Sie das DNS-Suffix zurückgeben können.

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

        Daraufhin wird das DNS-Suffix zurückgegeben. Zum Beispiel: **yourclustername.b4.internal.cloudapp.net**.

    > [WACOM.NOTE] Sie können sich auch per Remotedesktop mit dem HBase-Cluster verbinden (Sie werden mit dem Stammknoten verbunden) und **ipconfig** in einer Eingabeaufforderung ausführen, um das DNS-Suffix abzurufen. Anweisungen zu Aktivierung des RDP und zur Verbindung des Clusters mittels des RDP finden sie unter [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Verwaltungsportal][Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Verwaltungsportal].
    >
    > ![hdinsight.hbase.dns.surffix][hdinsight.hbase.dns.surffix]

<!--  3.  Change the Primary DNS Suffix configuration of the virtual machine. This enables virtual machine to automatically resolve the host name of the HBase cluster without explicit specification of the suffix. For example, the *workernode0* host name will be correctly resolved to the workernode0 of the HBase cluster.       To make the configuration change:      1. RDP into the virtual machine.      2. Open **Local Group Policy Editor**. The executable is gpedit.msc.     3. Expand **Computer Configuration**, expand **Administrative Templates**, expand **Network**, and then click **DNS Client**.      - Set **Primary DNS Suffix** to the value obtained in the step 2:           ![hdinsight.hbase.primary.dns.suffix][img-primary-dns-suffix]     4. Click **OK**.      5. Reboot the virtual machine. -->

Führen Sie den Befehl `ping headnode0.<dns suffix>` auf dem virtuellen Computer aus, um zu überprüfen, ob der virtuelle Computer mit dem HBase-Cluster kommunizieren kann. Zum Beispiel: ping headnode0.mycluster.b1.cloudapp.net

Führen Sie die unter [Verwenden von Maven zur Entwicklung von Java-Anwendungen, die HBase mit HDInsight (Hadoop) nutzen][Verwenden von Maven zur Entwicklung von Java-Anwendungen, die HBase mit HDInsight (Hadoop) nutzen] beschriebenen Schritte aus, um diese Informationen in einer Java-Anwendung zu verwenden. Wenn sich die Anwendung mit einem Remote-HBase-Server verbinden soll, müssen Sie den FQDN für ZooKeeper in der Datei **hbase-site.xml** eintragen. Beispiel:

    <property>
        <name>hbase.zookeeper.quorum</name>
        <value>zookeeper0.<dns suffix>,zookeeper1.<dns suffix>,zookeeper2.<dns suffix></value>
    </property>

> [WACOM.NOTE] Weitere Informationen zur Namensauflösung in virtuellen Azure-Netzwerken und zur Verwendung eigener DNS-Server finden Sie unter [Namensauflösung (DNS)][Namensauflösung (DNS)].

## <span id="powershell"></span></a>Bereitstellen eines HBase-Clusters mit Azure PowerShell:\*\*

1.  Öffnen Sie PowerShell ISE.
2.  Kopieren Sie den folgenden Code, und fügen Sie ihn in den Skriptbereich ein.

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

3.  Klicken Sie auf **Skript ausführen** oder drücken Sie **F5**.
4.  Wenn Sie den Cluster prüfen möchten, können Sie ihn entweder über das Verwaltungsportal prüfen oder das folgende PowerShell-Cmdlet aus dem unteren Bereich ausführen:

    Get-AzureHDInsightCluster

## <span id="nextsteps"></span></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie einen HBase-Cluster bereitstellen können. Weitere Informationen finden Sie unter:

-   [Erste Schritte mit HDInsight][Erste Schritte mit HDInsight]
-   [Bereitstellen von Hadoop-Clustern in HDInsight][Bereitstellen von Hadoop-Clustern in HDInsight]
-   [Erste Schritte mit HBase mit Hadoop in HDInsight][Erste Schritte mit HBase mit Hadoop in HDInsight]
-   [Analysieren von Stimmungen in Twitter mit HBase in HDInsight][Analysieren von Stimmungen in Twitter mit HBase in HDInsight]
-   [Virtuelle Netzwerke im Überblick][Virtuelle Netzwerke im Überblick].

  [Voraussetzungen]: #prerequisites
  [Bereitstellen von HBase-Clustern in einem virtuellen Netzwerk]: #hbaseprovision
  [Herstellen einer Verbindung mit dem im virtuellen Netzwerk bereitgestellten HBase-Cluster unter Verwendung von HBase Java RPC APIs]: #connect
  [Bereitstellen eines HBase-Clusters mit PowerShell]: #powershell
  [Nächste Schritte]: #nextsteps
  [Kaufoptionen]: http://azure.microsoft.com/de-de/pricing/purchase-options/
  [Spezielle Angebote]: http://azure.microsoft.com/de-de/pricing/member-offers/
  [Kostenlose Testversion]: http://azure.microsoft.com/de-de/pricing/free-trial/
  [Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell
  [Ausführen von Windows PowerShell-Skripts]: http://technet.microsoft.com/de-de/library/ee176949.aspx
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com
  []: http://msdn.microsoft.com/library/azure/jj156088.aspx
  [Verwenden von Azure Blob-Speicher mit HDInsight]: ../hdinsight-use-blob-storage/
  [Clustername und Version]: ./media/hdinsight-hbase-provision-vnet/hbasewizard1.png
  [Anzahl der Knoten und Region]: ./media/hdinsight-hbase-provision-vnet/hbasewizard2.png
  [Administratorname und Kennwort]: ./media/hdinsight-hbase-provision-vnet/hbasewizard3.png
  [Speicherkonto-Auswahl]: ./media/hdinsight-hbase-provision-vnet/hbasewizard4.png
  [Erste Schritte mit HBase mit Hadoop in HDInsight]: ../hdinsight-hbase-get-started/
  [Erstellen eines virtuellen Windows Server-Computers]: ../virtual-machines-windows-tutorial/
  [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Verwaltungsportal]: ../hdinsight-administer-use-management-portal/#rdp
  [hdinsight.hbase.dns.surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
  [Verwenden von Maven zur Entwicklung von Java-Anwendungen, die HBase mit HDInsight (Hadoop) nutzen]: azure.microsoft.com/de-de/documentation/articles/hdinsight-hbase-build-java-maven/
  [Namensauflösung (DNS)]: http://msdn.microsoft.com/de-de/library/azure/jj156088.aspx
  [Erste Schritte mit HDInsight]: ../hdinsight-get-started/
  [Bereitstellen von Hadoop-Clustern in HDInsight]: ../hdinsight-provision-clusters/
  [Analysieren von Stimmungen in Twitter mit HBase in HDInsight]: ../hdinsight-hbase-twitter-sentiment/
  [Virtuelle Netzwerke im Überblick]: http://msdn.microsoft.com/library/azure/jj156007.aspx
