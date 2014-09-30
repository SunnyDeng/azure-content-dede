<properties linkid="manage-services-hdinsight-hbase-provision-on-vnet" urlDisplayName="Provision HBase clusters on Azure Virtual Network" pageTitle="Provision HBase clusters on Azure Virtual Network | Azure" metaKeywords="" description="Learn how to create HDInsight clusters on Azure Virtual Network." metaCanonical="" services="hdinsight" documentationCenter="" title="Provision HBase clusters on Azure Virtual Network" authors="jgao" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="jgao"></tags>

# Bereitstellen von HBase-Clustern im virtuellen Azure-Netzwerk

Erfahren Sie, wie Sie HDInsight-Cluster im virtuellen Azure-Netzwerk erstellen können.

Mit der Integration in virtuelle Netzwerke können HBase-Cluster im selben virtuellen Netzwerk bereitgestellt werden wie Ihre Anwendungen, sodass Anwendungen direkt mit HBase kommunizieren können. Es ergeben sich folgende Vorteile:

-   Direkte Konnektivität der Webanwendungen mit den Knoten des HBase-Clusters, was Kommunikation unter Verwendung von HBase Java RPC APIs ermöglicht
-   Optimierte Leistung, da Datenverkehr nicht über mehrere Gateways und Lastenausgleiche fließt
-   Mehr Sicherheit bei der Verarbeitung sensibler Informationen ohne exponierten öffentlichen Endpunkt

> [WACOM.NOTE] Aktuell können nur HBase-Cluster in virtuellen Azure-Netzwerken bereitgestellt werden. Hadoop-Cluster werden nicht unterstützt.

## Themen in diesem Artikel

-   [Voraussetzungen][]
-   [Bereitstellen von HBase-Clustern in einem virtuellen Netzwerk][]
-   [Herstellen einer Verbindung mit dem im virtuellen Netzwerk bereitgestellten HBase-Cluster unter Verwendung von HBase Java RPC APIs][]
-   [Nächste Schritte][]

## <span id="prerequisites"></span></a>Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   **Ein Azure-Abonnement**. Azure ist eine abonnementbasierte Plattform. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter [Kaufoptionen][], [Spezielle Angebote][] oder [Kostenlose Testversion][].

-   **Eine Arbeitsstation, auf der Azure PowerShell installiert und konfiguriert ist**. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][]. Um PowerShell-Skripts ausführen zu können, müssen Sie Azure PowerShell als Administrator ausführen und die Ausführungsrichtlinie auf *RemoteSigned* setzen. Siehe [Ausführen von Windows PowerShell-Skripts][].

    Stellen Sie vor dem Ausführen von PowerShell-Skripts mithilfe des folgenden Cmdlets sicher, dass Sie mit Ihrem Azure-Abonnement verbunden sind:

        Add-AzureAccount

    Wenn Sie mehrere Azure-Abonnements haben, legen Sie das aktuelle Abonnement mit dem folgenden Cmdlet fest:

        Select-AzureSubscription <AzureSubscriptionName>

## <span id="hbaseprovision"></span></a>Bereitstellen eines HBase-Clusters in einem virtuellen Netzwerk

**So erstellen Sie ein virtuelles Netzwerk im Verwaltungsportal:**

1.  Melden Sie sich beim [Azure-Verwaltungsportal][] an.
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

> [WACOM.NOTE] HDInsight-Cluster nutzen den Azure-Blob-Speicher für das Speichern von Daten. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight][]. Sie benötigen ein Speicherkonto und einen Blob-Speichercontainer. Der Speicherort des Speicherkontos muss dem Speicherort des virtuellen Netzwerks und des Clusters entsprechen.

**So erstellen Sie ein Azure-Speicherkonto und einen Blob-Speichercontainer:**

1.  Melden Sie sich beim [Azure-Verwaltungsportal][] an.
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

**So stellen Sie einen HBase-Cluster mit Azure PowerShell bereit:**

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

Sie können die Abläufe aus [Erste Schritte mit HBase mit Hadoop in HDInsight][] verwenden, um den neuen HBase-Cluster zu testen.

## <span id="connect"></span></a>Herstellen einer Verbindung mit dem im virtuellen Netzwerk bereitgestellten HBase-Cluster unter Verwendung von HBase Java RPC APIs

1.  Stellen Sie einen virtuellen IaaS-Computer im selben virtuellen Azure-Netzwerk und im selben Subnetz bereit. Dadurch verwenden der virtuelle Computer und der HBase-Cluster denselben internen DNS-Server für die Auflösung von Hostnamen. Hierfür müssen Sie die Option "Aus Galerie" auswählen und das virtuelle Netzwerk statt eines Rechenzentrums wählen. Anweisungen dazu finden Sie unter [Erstellen eines virtuellen Windows Server-Computers][]. Ein standardmäßiges Windows Server 2012-Image mit kleinem virtuellen Computer ist ausreichend.

2.  Rufen Sie das verbindungsspezifische DNS-Suffix des HBase-Clusters ab. Integrieren Sie hierfür das RDP in den HBase-Cluster (es wird eine Verbindung zum Hauptknoten hergestellt) und führen Sie **ipconfig** aus einem Eingabeaufforderungsfenster aus. Anweisungen zu Aktivierung des RDP und zur Verbindung des Clusters mittels des RDP finden sie unter [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Verwaltungsportal][].

    ![hdinsight.hbase.dns.surffix][]

3.  Ändern Sie die Konfiguration des primären DNS-Suffixes des virtuellen Computers. Dadurch kann der virtuelle Computer automatisch den Hostnamen des HBase-Clusters auflösen, ohne das Suffix explizit angeben zu müssen. Beispielsweise wird der Hostname *workernode0* korrekt in den workernode0 des HBase-Clusters aufgelöst.
    So ändern Sie die Konfiguration:

    1.  Integrieren Sie das RDP in den virtuellen Computer.
    2.  Öffnen Sie den **Lokalen Gruppenrichtlinien-Editor**. Die ausführbare Datei heißt gpedit.msc.
    3.  Erweitern Sie **Computerkonfiguration**, dann **Administrative Vorlagen**, dann**Netzwerk**, und klicken Sie danach auf **DNS-Client**.
    4.  Legen Sie für **Primäres DNS-Suffix** den Wert fest, den Sie in Schritt 2 abgerufen haben:

        ![hdinsight.hbase.primary.dns.suffix][]

    1.  Klicken Sie auf **OK**.
    2.  Starten Sie den virtuellen Computer neu.

    Jetzt ist der virtuelle Computer bereit, mit dem HBase-Cluster zu kommunizieren. Testen Sie die Verbindung, indem Sie "ping headnode0" auf dem virtuellen Computer ausführen.

## <span id="nextsteps"></span></a>Nächste Schritte

In diesem Artikel haben Sie erfahren, wie Sie einen HBase-Cluster bereitstellen können. Weitere Informationen finden Sie unter:

-   [Erste Schritte mit HDInsight][]
-   [Bereitstellen von Hadoop-Clustern in HDInsight][]
-   [Erste Schritte mit HBase mit Hadoop in HDInsight][]
-   [Analysieren von Stimmungen in Twitter mit HBase in HDInsight][]
-   [Virtuelle Netzwerke im Überblick][].

  [Voraussetzungen]: #prerequisites
  [Bereitstellen von HBase-Clustern in einem virtuellen Netzwerk]: #hbaseprovision
  [Herstellen einer Verbindung mit dem im virtuellen Netzwerk bereitgestellten HBase-Cluster unter Verwendung von HBase Java RPC APIs]: #connect
  [Nächste Schritte]: #nextsteps
  [Kaufoptionen]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [Spezielle Angebote]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [Kostenlose Testversion]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell
  [Ausführen von Windows PowerShell-Skripts]: http://technet.microsoft.com/en-us/library/ee176949.aspx
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com
  []: http://msdn.microsoft.com/library/azure/jj156088.aspx
  [Verwenden von Azure Blob-Speicher mit HDInsight]: ../hdinsight-use-blob-storage/
  [Erste Schritte mit HBase mit Hadoop in HDInsight]: ../hdinsight-hbase-get-started/
  [Erstellen eines virtuellen Windows Server-Computers]: ../virtual-machines-windows-tutorial/
  [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Verwaltungsportal]: ../hdinsight-administer-use-management-portal/#rdp
  [hdinsight.hbase.dns.surffix]: ./media/hdinsight-hbase-provision-vnet/DNSSuffix.png
  [hdinsight.hbase.primary.dns.suffix]: ./media/hdinsight-hbase-provision-vnet/PrimaryDNSSuffix.png
  [Erste Schritte mit HDInsight]: ../hdinsight-get-started/
  [Bereitstellen von Hadoop-Clustern in HDInsight]: ../hdinsight-provision-clusters/
  [Analysieren von Stimmungen in Twitter mit HBase in HDInsight]: ../hdinsight-hbase-twitter-sentiment/
  [Virtuelle Netzwerke im Überblick]: http://msdn.microsoft.com/library/azure/jj156007.aspx
