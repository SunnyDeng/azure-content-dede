<properties
   pageTitle="Erstellen von Hadoop-Clustern in HDInsight | Microsoft Azure"
   	description="Hier erfahren Sie, wie Sie Cluster für Azure HDInsight im Azure-Vorschauportal, mit Azure PowerShell, über die Befehlszeile oder mit einem .NET-SDK erstellen."
   services="hdinsight"
   documentationCenter=""
   tags="azure-portal"
   authors="mumian"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="09/29/2015"
   ms.author="jgao"/>

# Erstellen von Hadoop-Clustern in HDInsight

Sie erhalten Informationen zur Planung der Erstellung von HDInsight-Clustern.

[AZURE.INCLUDE [Auswahl](../../includes/hdinsight-portal-management-selector.md)]

* [Erstellen von Hadoop-Clustern in HDInsight](hdinsight-provision-clusters-v1.md)

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Erstellen von Hadoop-Clustern in HDInsight](hdinsight-provision-clusters-v1.md)

**Voraussetzungen:**

Bevor Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

- Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


## Grundlegende Konfigurationsoptionen


- **Clustername**

	Der Clustername wird verwendet, um einen Cluster zu identifizieren. Für den Clusternamen gelten die folgenden Richtlinien:

	- Das Feld muss eine Zeichenfolge mit 3 bis 63 Zeichen enthalten.
	- Das Feld darf nur Buchstaben, Zahlen und Bindestriche enthalten.

- **Abonnementname**

	Ein HDInsight-Cluster ist an ein Azure-Abonnement gebunden.

- **Ressourcengruppenname**

	Anwendungen bestehen normalerweise aus vielen Komponenten, z. B. Web-App, Datenbank, Datenbankserver, Speicher und Drittanbieterdiensten. Mit dem Azure-Ressourcen-Manager (ARM) können Sie mit den Ressourcen in Ihrer Anwendung als Gruppe arbeiten, was als Azure-Ressourcengruppe bezeichnet wird. Sie können alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen, aktualisieren, überwachen oder löschen. Sie verwenden eine Vorlage für die Bereitstellung, die für unterschiedliche Umgebungen geeignet sein kann, z. B. Testing, Staging und Produktion. Sie können die Abrechnung für Ihre Organisation vereinfachen, indem Sie die zusammengefassten Kosten für die gesamte Gruppe anzeigen. Weitere Informationen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md).	
- **Betriebssystem**

	Sie können HDInsight-Cluster unter einem der folgenden beiden Betriebssysteme erstellen:
	- **HDInsight für Windows (Windows Server 2012 R2 Datacenter)**:
	- **HDInsight unter Linux (Ubuntu 12.04 LTS für Linux) (Vorschau)**: HDInsight bietet die Möglichkeit, Linux-Cluster in Azure zu konfigurieren. Konfigurieren Sie einen Linux-Cluster, wenn Sie mit Linux oder Unix und der Migration von einer vorhandenen Linux-basierten Hadoop-Lösung vertraut sind, oder Sie eine einfache Integration mit Komponenten des Hadoop-Systems wünschen, die für Linux konzipiert sind. Weitere Informationen finden Sie unter [Erste Schritte mit Hadoop unter Linux in HDInsight](hdinsight-hadoop-linux-get-started.md).


- **HDInsight-Version**

	Dient zum Ermitteln der Version von HDInsight, die für diesen Cluster verwendet werden soll. Weitere Informationen finden Sie unter [Hadoop-Clusterversionen und -komponenten in HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409).

- **Clustertyp** und **Clustergröße (auch Datenknoten)**

	Mit HDInsight können Kunden verschiedene Clustertypen für unterschiedliche Datenanalyseworkloads bereitstellen. Aktuell angebotene Clustertypen sind:

	- Hadoop-Cluster: für Abfrage- und Analyseworkloads
	- HBase-Cluster: für NoSQL-Workloads
	- Storm-Cluster: für Workloads für die Ereignisverarbeitung in Echtzeit
	- Spark-Cluster (Vorschau): für die Verarbeitung im Arbeitsspeicher, interaktive Abfragen, Streaming und Workloads für maschinelles Lernen.

	![HDInsight-Cluster](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

	> [AZURE.NOTE]*Azure HDInsight-Cluster* werden auch als *Hadoop-Cluster in HDInsight* oder *HDInsight-Cluster* bezeichnet. Mitunter werden diese Bezeichnungen synonym mit *Hadoop-Cluster* verwendet. Alle beziehen sich auf Hadoop-Cluster, die in der Microsoft Azure-Umgebung gehostet werden.

	Innerhalb eines bestimmten Clustertyps übernehmen die verschiedenen Knoten unterschiedliche Rollen, die es Kunden ermöglichen, die Knoten in einer bestimmten Rolle entsprechend ihres jeweiligen Workloads zu dimensionieren. Beispielsweise können bei einem Hadoop-Cluster die Workerknoten mit großem Arbeitsspeicher erstellt werden, wenn die durchzuführenden Analysen speicherintensiv sind.

	![HDInsight Hadoop-Clusterrollen](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

	Hadoop-Cluster für HDInsight werden mit zwei Rollen bereitgestellt:

	- Hauptknoten (2 Knoten)
	- Datenknoten (mindestens 1 Knoten)

	![HDInsight Hadoop-Clusterrollen](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

	HBase-Cluster für HDInsight werden mit drei Rollen bereitgestellt: – Hauptserver (2 Knoten) – Regionsserver (mindestens 1 Knoten) – Master-/Zookeeper-Knoten (3 Knoten)

	![HDInsight Hadoop-Clusterrollen](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

	Storm-Cluster für HDInsight werden mit drei Rollen bereitgestellt: – Nimbus-Knoten (2 Knoten) – Supervisor-Server (mindestens 1 Knoten) – Zookeeper-Knoten (3 Knoten)


	![HDInsight Hadoop-Clusterrollen](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

	Spark-Cluster für HDInsight werden mit drei Rollen bereitgestellt:
	- Hauptknoten (2 Knoten)
	- Workerknoten (mindestens 1 Knoten)
	- Zookeeper-Knoten (3 Knoten) (für Zookeeper vom Typ "A1" kostenlos)

	Kunden wird die Nutzung dieser Knoten für die Laufzeit des Clusters in Rechnung gestellt. Die Abrechnung beginnt, sobald ein Cluster erstellt wurde, und endet, wenn der Cluster gelöscht wird (bei Clustern ist kein Aufheben der Zuweisung oder Anhalten möglich). Die Clustergröße wirkt sich auf die Clusterkosten aus. Zu Lernzwecken wird empfohlen, einen Datenknoten zu verwenden. Weitere Informationen zu den Preisen von HDInsight finden Sie unter [HDInsight – Preise](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


	>[AZURE.NOTE]Die Begrenzung der Clustergröße variiert je nach Azure-Abonnement. Wenden Sie sich an den Azure-Abrechnungssupport, um diese Begrenzung zu erhöhen.

- **Region/virtuelles Netzwerk (auch Speicherort genannt)**

	![Azure-Regionen](./media/hdinsight-provision-clusters/Azure.regions.png)

	Um eine Liste der unterstützten Regionen zu erhalten, klicken Sie in der Dropdownliste **Region** auf [HDInsight-Preise](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Knotengröße**

	![VM-Größen für HDInsight-Knoten](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	Wählen Sie die VM-Größe für die Knoten aus. Weitere Informationen finden Sie unter [Größen für Clouddienste](cloud-services-sizes-specs.md).

	Je nach Wahl der VMs können Ihre Kosten variieren. HDInsight verwendet für Clusterknoten VMs mit Standardtarif. Informationen über die Auswirkungen der VM-Größe auf Ihre Kosten finden Sie unter <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight-Preise</a>.


- **HDInsight-Benutzer**

	Die HDInsight-Cluster ermöglichen Ihnen, während der Bereitstellung zwei Benutzerkonten zu konfigurieren:

	- HTTP-Benutzer. Der Standardbenutzername bei der Basiskonfiguration im Azure-Vorschauportal ist „admin“.
	- RDP-Benutzer (Windows-Cluster): Dient zum Herstellen einer Verbindung mit dem Cluster über RDP. Wenn Sie das Konto erstellen, müssen Sie ein Ablaufdatum festlegen, das maximal 90 Tage hinter dem aktuellen Datum liegt.
	- SSH-Benutzer (Linux-Cluster): Dient zum Herstellen einer Verbindung mit dem Cluster über SSH. Sie können zusätzliche SSH-Benutzerkonten erstellen, nachdem der Cluster erstellt wurde, indem Sie die Schritte unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md) ausführen.



- **Azure-Speicherkonto**

	Das ursprüngliche HDFS nutzt viele lokale Datenträger im Cluster. HDInsight verwendet zur Datenspeicherung stattdessen Azure-Blobspeicher. Azure-Blobspeicher stellt eine robuste, universelle Speicherlösung dar, die problemlos mit HDInsight integriert werden kann. Über eine HDFS-Schnittstelle (Hadoop Distributed File System) können sämtliche Komponenten in HDInsight direkt mit strukturierten oder unstrukturierten Daten im Blobspeicher arbeiten. Die Speicherung von Daten im Blobspeicher sorgt dafür, dass die HDInsight-Cluster, die für Berechnungen verwendet werden, sicher gelöscht werden können, ohne Benutzerdaten zu verlieren.

	Bei der Konfiguration müssen Sie ein Azure-Speicherkonto und einen Azure-Blobspeichercontainer für dieses Konto angeben. Bei einigen Erstellungsprozessen müssen das Azure-Speicherkonto und der Blobspeichercontainer vorher erstellt werden. Der Blobspeichercontainer wird vom Cluster als Standardspeicherort verwendet. Optional können Sie zusätzliche Azure-Speicherkonten (verknüpften Speicher) angeben, auf die der Cluster zugreifen kann. Darüber hinaus kann der Cluster auch auf alle Blobcontainer zugreifen, die mit vollständig öffentlichem Lesezugriff oder nur für Blobs öffentlichem Lesezugriff konfiguriert sind. Weitere Informationen zum Einschränken des Zugriffs finden Sie unter [Verwalten des Zugriffs auf Azure-Speicherressourcen](storage-manage-access-to-resources.md).

	![HDInsight-Speicher](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE]Ein Blobspeichercontainer stellt eine Gruppierung von Blobs bereit (siehe die folgende Abbildung):

	![Azure-Blobspeicher](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING]Geben Sie keinen Blob-Speichercontainer für mehrere Cluster frei. Dies wird nicht unterstützt.

	Weitere Informationen zu sekundären Blobspeichern finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](hdinsight-use-blob-storage.md).

- **Hive/Oozie-Metastore**

	Metastore enthält Hive- und Oozie-Metadaten, wie z. B. Hive-Tabellen, Partitionen, Schemata und Spalten. Mithilfe von Metastore können Sie Ihre Hive- und Oozie-Metadaten beibehalten, damit Sie nicht Hive-Tabellen oder Oozie-Aufträge neu erstellen müssen, wenn Sie einen neuen Cluster erstellen. Standardmäßig speichert Hive diese Informationen in einer eingebetteten Azure SQL-Datenbank. Die eingebettete Datenbank kann die Metadaten nicht beibehalten, wenn der Cluster gelöscht wird. Angenommen, Sie haben einen Cluster mit einem Hive-Metastore erstellt. Sie haben einige Hive-Tabellen erstellt. Nachdem Sie den Cluster gelöscht und mit demselben Hive-Metastore wiederhergestellt haben, werden dieselben Tabellen angezeigt, die Sie im ursprünglichen Cluster erstellt haben.

## Erweiterte Konfigurationsoptionen

>[AZURE.NOTE]Dieser Abschnitt gilt derzeit nur für auf Windows basierende HDInsight-Cluster.

### Anpassen von Clustern mithilfe der HDInsight-Clusteranpassung

Mitunter möchten Sie die Konfigurationsdateien bearbeiten. Hier einige Beispiele:

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

Die Cluster können die Änderungen aufgrund des Re-Imagings nicht beibehalten. Weitere Informationen finden Sie unter [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) (Neustart von Rolleninstanzen aufgrund von Betriebssystemupdates – in englischer Sprache). Um die Änderungen für die Laufzeit des Clusters beizubehalten, können Sie während des Erstellungsprozesses die HDInsight-Clusteranpassung nutzen.

Es folgt ein Azure PowerShell-Skriptbeispiel der Anpassung einer Hive-Konfiguration:

	# hive-site.xml configuration
	$hiveConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightHiveConfiguration'
	$hiveConfigValues.Configuration = @{ "hive.metastore.client.socket.timeout"="90" } #default 60

	$config = New-AzureHDInsightClusterConfig `
	            -ClusterSizeInNodes $clusterSizeInNodes `
	            -ClusterType $clusterType `
	          | Set-AzureHDInsightDefaultStorage `
	            -StorageAccountName $defaultStorageAccount `
	            -StorageAccountKey $defaultStorageAccountKey `
	            -StorageContainerName $defaultBlobContainer `
	          | Add-AzureHDInsightConfigValues `
	            -Hive $hiveConfigValues

	New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $credential -OSType Windows -Config $config

Weitere Beispiele zum Anpassen anderer Konfigurationsdateien:

	# hdfs-site.xml configuration
	$HdfsConfigValues = @{ "dfs.blocksize"="64m" } #default is 128MB in HDI 3.0 and 256MB in HDI 2.1

	# core-site.xml configuration
	$CoreConfigValues = @{ "ipc.client.connect.max.retries"="60" } #default 50

	# mapred-site.xml configuration
	$MapRedConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightMapReduceConfiguration'
	$MapRedConfigValues.Configuration = @{ "mapreduce.task.timeout"="1200000" } #default 600000

	# oozie-site.xml configuration
	$OozieConfigValues = new-object 'Microsoft.WindowsAzure.Management.HDInsight.Cmdlet.DataObjects.AzureHDInsightOozieConfiguration'
	$OozieConfigValues.Configuration = @{ "oozie.service.coord.normal.default.timeout"="150" }  # default 120

Weitere Informationen finden Sie im Blog von Azim Uddin mit dem Titel [Customizing HDInsight Cluster creation](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx).




### Anpassen von Clustern mithilfe von Skriptaktionen

Sie können zusätzliche Komponenten installieren oder die Clusterkonfiguration mithilfe von Skripts während der Erstellung anpassen. Diese Skripts werden mit der Konfigurationsoption **Skriptaktion** aufgerufen, die im Azure-Vorschauportal, in HDInsight Windows PowerShell-Cmdlets oder im HDInsight .NET-SDK verwendet werden kann. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster.md).


### Verwenden virtueller Azure-Netzwerke

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) ermöglicht das Erstellen eines sicheren, beständigen Netzwerk mit allen Ressourcen, die Sie für Ihre Lösung benötigen. Virtuelle Netzwerken ermöglichen Folgendes:

* Verbinden von Cloudressourcen in einem privaten Netzwerk (nur in der Cloud)

	![Diagramm der Nur-Cloud-Konfiguration](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* Verbinden Ihrer Cloudressourcen mithilfe eines virtuellen privaten Netzwerks (VPN) mit dem Netzwerk in Ihrem lokalen Datencenter (Standort-zu-Standort oder Punkt-zu-Standort).

	In einer Standort-zu-Standort-Konfiguration können Sie mehrere Ressourcen aus Ihrem Rechenzentrum über ein Hardware-VPN oder den Routing- und RAS-Dienst mit dem virtuellen Azure-Netzwerk verbinden.

	![Diagramm der Standort-zu-Standort-Konfiguration](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	In einer Punkt-zu-Standort-Konfiguration können Sie eine bestimmte Ressource über ein Software-VPN mit dem virtuellen Azure-Netzwerk verbinden.

	![Diagramm der Punkt-zu-Standort-Konfiguration](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Weitere Informationen zu Features, Vorteilen und Funktionen von virtuellen Netzwerken finden Sie unter [Überblick über virtuelle Azure-Netzwerke](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE]Sie müssen das virtuelle Azure-Netzwerk erstellen, bevor Sie einen HDInsight-Cluster bereitstellen. Weitere Informationen finden Sie unter [Erstellen eines Hadoop-Clusters in einem virtuellen Netzwerk](hdinsight-hbase-provision-vnet.md#provision-an-hbase-cluster-into-a-virtual-network).
>
> Azure HDInsight unterstützt nur standortbasierte virtuelle Netzwerke und kann momentan nicht mit affinitätsgruppenbasierten virtuellen Netzwerken verwendet werden. Verwenden Sie das Azure PowerShell-Cmdlet "Get-AzureVNetConfig", um zu prüfen, ob ein vorhandenes virtuelles Netzwerk in Azure standortbasiert ist. Wenn das virtuelle Netzwerk nicht standortbasiert ist, haben Sie die folgenden Optionen:
>
> - Exportieren Sie die vorhandene virtuelle Netzwerkkonfiguration, und erstellen Sie ein neues virtuelles Netzwerk. Alle neuen virtuellen Netzwerke sind standardmäßig standortbasiert.
> - Migrieren Sie zu einem standortbasierten virtuellen Netzwerk. Siehe [Migrieren vorhandener Dienste in einen Regionsbereich](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/).
>
> Sie sollten unbedingt ein einziges Subnetz pro Cluster verwenden.

## Erstellen mit dem Vorschauportal

Erläuterung der Felder finden Sie in den [grundlegenden Konfigurationsoptionen](#basic-configuration-options) und [erweiterten Konfigurationsoptionen](#advanced-configuration-options) .

**So erstellen Sie einen HDInsight-Cluster**

1. Melden Sie sich beim [Azure-Vorschauportal][azure-preview-portal] an.
2. Klicken Sie auf **NEU**, auf **Datenanalyse** und anschließend auf **HDInsight**.

    ![Erstellen eines neuen Clusters im Azure-Vorschauportal](./media/hdinsight-provision-clusters/HDI.CreateCluster.1.png "Erstellen eines neuen Clusters im Azure-Vorschauportal")

3. Geben Sie folgende Werte ein bzw. wählen diese aus:

  * **Clustername**: Geben Sie einen Namen für den Cluster ein. Wenn der Clustername verfügbar ist, wird neben dem Namen ein grünes Häkchen angezeigt.
  * **Clustertyp**: Wählen Sie **Hadoop** aus.
  * **Clusterbetriebssystem**: Wählen Sie **Windows Server 2012 R2 Datacenter** aus.
  * **Abonnement**: Wählen Sie das Azure-Abonnement aus, das für die Erstellung des Clusters verwendet werden soll.
  * **Ressourcengruppe**: Wählen Sie eine vorhandene Ressourcengruppe aus, oder erstellen Sie eine neue Ressourcengruppe. Dieser Eintrag ist standardmäßig auf eine Ihrer vorhandenen Ressourcengruppen festgelegt (sofern verfügbar).
  * **Anmeldeinformationen**: Konfigurieren Sie den Benutzernamen und das Kennwort für den Hadoop-Benutzer (HTTP-Benutzer). Wenn Sie Remotedesktop für den Cluster aktivieren, müssen Sie den Benutzernamen und das Kennwort für den Remotedesktopbenutzer konfigurieren und ein Ablaufdatum für das Konto festlegen. Klicken Sie unten auf **Auswählen**, um die Änderungen zu speichern.

	   	![Provide cluster credentials](./media/hdinsight-provision-clusters/HDI.CreateCluster.3.png "Provide cluster credentials")

  * **Datenquelle**: Erstellen Sie ein neues Azure-Speicherkonto, oder wählen Sie ein vorhandenes Azure-Speicherkonto aus, das als Standarddateisystem für den Cluster verwendet werden soll.

   		![Data source blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.4.png "Provide data source configuration")

  		* **Auswahlmethode**: Wählen Sie **Aus allen Abonnements** aus, damit Speicherkonten aus all Ihren Abonnements durchsucht werden können. Wählen Sie **Zugriffsschlüssel** aus, wenn Sie den **Speichernamen** und **Zugriffsschlüssel** eines vorhandenen Speicherkontos eingeben möchten.
  		* **Speicherkonto auswählen/Neu erstellen**: Klicken Sie auf **Speicherkonto auswählen** um ein vorhandenes Speicherkonto auszuwählen, das Sie dem Cluster zuordnen möchten. Alternativ können Sie auf **Neu erstellen** klicken, um ein neues Speicherkonto zu erstellen. Geben Sie den Namen des Speicherkontos in das angezeigte Feld ein. Wenn der Name verfügbar ist, wird ein grünes Häkchen angezeigt.
  		* **Standardcontainer auswählen**: Geben Sie den Namen des Standardcontainers für den Cluster ein. Sie können einen beliebigen Namen eingeben, es wird jedoch empfohlen, den Namen des Clusters zu verwenden. So können Sie leichter erkennen, dass es sich um den Container für diesen spezifischen Cluster handelt.
  		* **Standort**: Die geografische Region, in der sich das Speicherkonto befindet bzw. in der es erstellt wird. Dieser Standort bestimmt auch den Clusterstandort.  Der Cluster und das zugeordnete Standardspeicherkonto müssen sich im selben Azure-Rechenzentrum befinden.
  	
  * **Knotenpreistarife**: Legen Sie die Anzahl von Workerknoten fest, die Sie für den Cluster benötigen. Die vorkalkulierten Kosten für den Cluster werden auf dem Blatt angezeigt.
  

		![Node pricing tiers blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.5.png "Specify number of cluster nodes")


  * Klicken Sie auf **Optionale Konfiguration**, um die Clusterversion auszuwählen und andere optionale Einstellungen zu konfigurieren. Sie können den Cluster z. B. einem **virtuellen Netzwerk** hinzufügen, einen **externen Metastore** zum Speichern von Daten für Hive und Oozie einrichten, einen Cluster mithilfe von Skriptaktionen zum Installieren von benutzerdefinierten Komponenten anpassen oder zusätzliche Speicherkonten für einen Cluster angeben.

  		* **HDInsight-Version**: Wählen Sie die Version aus, die Sie für den Cluster verwenden möchten. Weitere Informationen finden Sie unter [HDInsight-Clusterversionen](hdinsight-component-versioning.md).
  		* **Virtuelles Netzwerk**: Wählen Sie ein virtuelles Azure-Netzwerk und das Subnetz aus, wenn Sie den Cluster in einem virtuellen Netzwerk platzieren möchten.  

			![Virtual network blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.6.png "Specify virtual network details")

			>[AZURE.NOTE] Windows-basierte HDInsight-Cluster können nur in einem klassischen virtuellen Netzwerk bereitgestellt werden.
  

  		
		* **Externe Metastores**: Geben Sie eine Azure SQL-Datenbank an, die zum Speichern von Hive- und Oozie-Metadaten für den Cluster verwendet werden soll.
 

			![Custom metastores blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.7.png "Specify external metastores")


			Klicken Sie für **Vorhandene SQL-Datenbank für Hive-Metadaten verwenden** auf **Ja**, wählen Sie eine SQL-­Datenbank aus, und geben Sie dann den Benutzernamen und das Kennwort für die Datenbank ein. Wiederholen Sie diese Schritte ggf. für **Vorhandene SQL-Datenbank für Oozie-Metadaten verwenden**. Klicken Sie auf **Auswählen**, bis wieder das Blatt **Optionale Konfiguration** angezeigt wird.


			>[AZURE.NOTE] Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Dashboard der Azure SQL-Datenbank mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Azure Services** den Wert **Ja** aus, und klicken Sie auf **Speichern**.
		
  		* Klicken Sie auf **Skriptaktionen**, wenn Sie einen Cluster bei seiner Erstellung mit einem benutzerdefinierten Skript anpassen möchten. Weitere Informationen zu Skriptaktionen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster.md). Geben Sie auf dem Blatt „Skriptaktionen“ die Details wie im Screenshot dargestellt ein.
  	

			![Script action blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.8.png "Specify script action")


    	* **Azure-Speicherschlüssel**: Geben Sie weitere Speicherkonten an, die dem Cluster zugeordnet werden sollen. Klicken Sie auf dem Blatt **Azure-Speicherschlüssel** auf **Speicherschlüssel hinzufügen**, und wählen Sie ein vorhandenes Speicherkonto aus, oder erstellen Sie ein neues Konto.
    

			![Additional storage blade](./media/hdinsight-provision-clusters/HDI.CreateCluster.9.png "Specify additional storage accounts")


4. Klicken Sie auf **Erstellen**. Wenn Sie **An Startmenü anheften** auswählen, wird dem Startmenü Ihres Vorschauportals eine Kachel für den Cluster hinzugefügt. Das Symbol zeigt an, dass der Cluster erstellt wird. Sobald die Erstellung abgeschlossen ist, ändert es sich in das HDInsight-Symbol.


	| Während der Erstellung | Erstellung abgeschlossen |
	| ------------------ | --------------------- |
	| ![Bereitstellungsanzeige im Startmenü](./media/hdinsight-provision-clusters/provisioning.png) | ![Kachel für einen bereitgestellten Cluster](./media/hdinsight-provision-clusters/provisioned.png) |


	
	> [AZURE.NOTE]Die Erstellung des Clusters dauert in der Regel ca. 15 Minuten. Sie können den Status des Bereitstellungsprozesses auf der Kachel im Startmenü oder im linken Bereich der Seite unter **Benachrichtigungen** überprüfen.
	

5. Klicken Sie nach Abschluss der Erstellung im Startmenü auf die Kachel für den Cluster, um das Clusterblatt zu öffnen. Auf dem Clusterblatt werden grundlegende Informationen zum Cluster angezeigt, z. B. der Name, die zugehörige Ressourcengruppe, der Standort, das Betriebssystem, die URL für das Cluster-Dashboard usw.


	![Clusterblatt](./media/hdinsight-provision-clusters/HDI.Cluster.Blade.png "Clustereigenschaften")


	Im Folgenden werden die Symbole oben auf diesem Blatt und im Abschnitt **Zusammenfassung** erläutert:


	* **Einstellungen** und **Alle Einstellungen**: Zeigt das Blatt **Einstellungen** für den Cluster an, über das Sie auf detaillierte Konfigurationsinformationen für den Cluster zugreifen können.
	* **Dashboard**, **Cluster-Dashboard** und **URL**: Über diese Optionen können Sie auf das Cluster-Dashboard zugreifen, ein Webportal für die Ausführung von Aufträgen im Cluster.
	* **Remotedesktop**: Dient zum Aktivieren/Deaktivieren von Remotedesktop für den Clusterknoten.
	* **Cluster skalieren**: Dient zum Ändern der Anzahl von Workerknoten für den Cluster.
	* **Löschen**: Löscht den HDInsight-Cluster.
	* **Schnellstart** (![Cloud- und Blitzsymbol = Schnellstart](./media/hdinsight-provision-clusters/quickstart.png)): Zeigt hilfreiche Informationen für die ersten Schritte mit HDInsight an.
	* **Benutzer** (![Benutzersymbol](./media/hdinsight-provision-clusters/users.png)): Dient zum Festlegen der Berechtigungen für die _Portalverwaltung_ dieses Clusters für andere Benutzer in Ihrem Azure-Abonnement.
	

		> [AZURE.IMPORTANT]Die hier vorgenommenen Einstellungen betreffen _nur_ den Zugriff und die Berechtigungen für diesen Cluster im Vorschauportal und haben keine Auswirkung darauf, wer eine Verbindung mit dem HDInsight-Cluster herstellen oder Aufträge übermitteln kann.
		
	* **Tags** (![Tagsymbol](./media/hdinsight-provision-clusters/tags.png)): Mithilfe von Tags können Sie Schlüssel-Wert-Paare festlegen, um eine benutzerdefinierte Taxonomie für Ihre Clouddienste zu definieren. Sie können z. B. einen Schlüssel mit dem Namen __Projekt__ erstellen und dann einen gemeinsamen Wert für alle mit einem bestimmten Projekt verknüpften Dienste verwenden.


## Erstellen mit der Azure-Ressourcen-Manager-Vorlage

Die Azure-Ressourcen-Manager (ARM)-Vorlage erleichtert die Bereitstellung und erneute Bereitstellung von Clustern. Das folgende Verfahren erstellt einen Hadoop-Cluster auf dem Linux-Betriebssystem im Rechenzentrum Nordeuropa mit 4 Workerknoten.

**So stellen Sie ein Cluster mithilfe einer ARM-Vorlage bereit**

1. Speichern Sie die JSON-Datei im Anhang A auf Ihrer Arbeitsstation.
2. Erstellen Sie bei Bedarf die Parameter.
3. Führen Sie die Vorlage mithilfe des folgenden PowerShell-Skripts aus:

		$resourceGroupName = "<ResourceGroupName>"
		$Location = "<ResourceGroupLocation>"
		
		$armDeploymentName = "<ARMDeploymentName>"
		$clusterName = "<ClusterName>"
		$clusterStorageAccountName = "<DefaultStorageAccountName>"
		
		# Connect to Azure
		Switch-AzureMode -Name AzureResourceManager
		Add-AzureAccount
		
		# Create a resource group
		New-AzureResourceGroup -Name $resourceGroupName -Location $Location
		
		# Create cluster and the dependent storage accounge
		$parameters = @{clusterName="$clusterName";clusterStorageAccountName="$clusterStorageAccountName"}
		
		New-AzureResourceGroupDeployment `
		    -Name $armDeploymentName `
		    -ResourceGroupName $resourceGroupName `
		    -TemplateFile E:\Tutorials\HDIARMTemplates\ARMTemplate-create-hadoop-cluster-with-storage.json `
		    -TemplateParameterObject $parameters
		
		# List cluster
		Get-AzureHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName

Informationen zum Bereitstellen einer ARM-Vorlage mit anderen Methoden finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](resource-group-template-deploy.md).


## Erstellen mithilfe von Azure PowerShell
Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. Dieser Abschnitt enthält Anweisungen für die Bereitstellung eines HDInsight-Clusters mit Azure PowerShell. Weitere Informationen zum Konfigurieren einer Arbeitsstation für die Ausführung von HDInsight PowerShell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md). Weitere Informationen zum Verwenden von Azure PowerShell mit HDInsight finden Sie unter [Verwalten von HDInsight mit PowerShell](hdinsight-administer-use-powershell.md). Eine Liste der HDInsight Windows PowerShell-Cmdlets finden Sie unter [HDInsight-Cmdlet-Referenz](https://msdn.microsoft.com/library/azure/dn858087.aspx).


Die folgenden Prozeduren müssen für die Erstellung eines HDInsight-Clusters mithilfe von Azure PowerShell ausgeführt werden:

- Erstellen einer Azure-Ressourcengruppe
- Erstellen eines Azure-Speicherkontos
- Erstellen eines Azure-Blobcontainers
- Erstellen eines HDInsight-Clusters


		# Sign in
		Add-AzureAccount

		###########################################
		# Create required items, if none exist
		###########################################

		# Select the subscription to use
		$subscriptionName = "<SubscriptionName>"        # Provide your Subscription Name
		Select-AzureSubscription -SubscriptionName $subscriptionName

		# Create an Azure Resource Group
		$resourceGroupName = "<ResourceGroupName>"      # Provide a Resource Group name
		$location = "<Location>"                        # For example, "West US"
		New-AzureResourceGroup -Name $resourceGroupName -Location $location

		# Create an Azure Storage account
		$storageAccountName = "<StorageAcccountName>"   # Provide a Storage account name
		New-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName -Location $location -Type Standard_GRS

		# Create an Azure Blob Storage container
		$containerName = "<ContainerName>"              # Provide a container name
		$storageAccountKey = Get-AzureStorageAccountKey -ResourceGroupName $resourceGroupName -Name $storageAccountName | %{ $_.Key1 }
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
		New-AzureStorageContainer -Name $containerName -Context $destContext

		###########################################
		# Create an HDInsight Cluster
		###########################################

		# Skip these variables if you just created them
		$resourceGroupName = "<ResourceGroupName>"      # Provide the Resource Group name
		$storageAccountName = "<StorageAcccountName>"   # Provide the Storage account name
		$containerName = "<ContainerName>"              # Provide the container name
		$storageAccountKey = Get-AzureStorageAccountKey -Name $storageAccountName -ResourceGroupName $resourceGroupName | %{ $_.Key1 }

		# Set these variables
		$clusterName = $containerName           		# As a best practice, have the same name for the cluster and container
		$clusterNodes = <ClusterSizeInNodes>    		# The number of nodes in the HDInsight cluster
		$credentials = Get-Credential

		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$location = Get-AzureStorageAccount -ResourceGroupName $resourceGroupName -StorageAccountName $storageAccountName | %{$_.Location}

		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -ResourceGroupName $resourceGroupName `
									-ClusterName $clusterName `
									-Location $location `
									-ClusterSizeInNodes $clusterNodes `
									-ClusterType Hadoop `
									-OSType Windows `
									-HttpCredential $credentials `
									-DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" `
									-DefaultStorageAccountKey $storageAccountKey `
									-DefaultStorageContainer $containerName  

	![HDI.CLI.Provision](./media/hdinsight-provision-clusters/HDI.ps.provision.png)

## Erstellen des HDInsight .NET SDK
Das HDInsight .NET SDK enthält .NET-Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in einer .NET Framework-Anwendung. Gehen Sie wie im Folgenden beschrieben vor, um ein Visual Studio-Konsolenanwendungsprojekt zu erstellen und den Code zum Erstellen eines Clusters einzufügen.

**Erstellen einer Visual Studio-Konsolenanwendung**

1. Erstellen Sie eine neue C#-Konsolenanwendung in Visual Studio.
2. Führen Sie den folgenden Nuget-Befehl in der Verwaltungskonsole des Nuget-Pakets aus.

		Install-Package Microsoft.Azure.Management.HDInsight -Pre

6. Doppelklicken Sie im Projektmappen-Explorer auf die Datei **Program.cs**, um sie zu öffnen, fügen Sie den folgenden Code ein, und geben Sie Werte für die Variablen an:


        using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Linq;
		using System.Security;
		using System.Text;
		using System.Threading.Tasks;
		using Hyak.Common;
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Job;
		using Microsoft.Azure.Management.HDInsight.Job.Models;
		using Microsoft.Azure.Management.HDInsight.Models;
		using Newtonsoft.Json;

		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static ProfileClient _profileClient;
		        private static SubscriptionCloudCredentials _cloudCredentials;
		        private static HDInsightManagementClient _hdiManagementClient;

		        private static Guid SubscriptionId = new Guid("<SubscriptionID>");
		        private const string ResourceGroupName = "<ResourceGroupName>";
		        private const string ExistingStorageName = "<storageaccountname>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<account key>";
		        private const string ExistingContainer = "<container name>";
		        private const string NewClusterName = "<cluster name>";
		        private const int NewClusterNumNodes = <number of nodes>;
		        private const string NewClusterLocation = "<location>";		//must be same as the storage account
		        private const OSType NewClusterOsType = OSType.Windows;
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const string NewClusterVersion = "3.2";
		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<password>";

		        private static void Main(string[] args)
		        {
		            System.Console.WriteLine("Start cluster creation");

		            _profileClient = GetProfile();
		            _cloudCredentials = GetCloudCredentials();
		            _hdiManagementClient = new HDInsightManagementClient(_cloudCredentials);

		            System.Console.WriteLine(String.Format("Creating the cluster {0}...", NewClusterName));
		            CreateCluster();
		            System.Console.WriteLine("Done. Press any key to continue.");
		            System.Console.ReadKey(true);
		        }

		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,
		                Location = NewClusterLocation,
		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOsType
		            };

		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }

		        private static ProfileClient GetProfile(string username = null, SecureString password = null)
		        {
		            var profileClient = new ProfileClient(new AzureProfile());
		            var env = profileClient.GetEnvironmentOrDefault(EnvironmentName.AzureCloud);
		            var acct = new AzureAccount { Type = AzureAccount.AccountType.User };

		            if (username != null && password != null)
		                acct.Id = username;

		            profileClient.AddAccountAndLoadSubscriptions(acct, env, password);

		            return profileClient;
		        }

		        private static SubscriptionCloudCredentials GetCloudCredentials()
		        {
		            var sub = _profileClient.Profile.Subscriptions.Values.FirstOrDefault(s => s.Id.Equals(SubscriptionId));

		            Debug.Assert(sub != null, "subscription != null");
		            _profileClient.SetSubscriptionAsDefault(sub.Id, sub.Account);

		            return AzureSession.AuthenticationFactory.GetSubscriptionCloudCredentials(_profileClient.Profile.Context);
		        }

		    }
		}

7. Drücken Sie **F5**, um die Anwendung auszuführen. In einem Konsolenfenster wird der Status der Anwendung angezeigt. Sie werden auch zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert. Die Erstellung eines HDInsight-Clusters kann mehrere Minuten in Anspruch nehmen.


## Erstellen eines Azure HDInsight-Clusters mithilfe lokaler SQL Server Integration Services

Sie können mit den SQL Server Integration Services (SSIS) einen HDInsight-Cluster auch erstellen oder löschen. Das Azure Feature Pack für SSIS bietet die folgenden Komponenten, die mit HDInsight-Clustern funktionieren.


- [Erstellen einer Clusteraufgabe in Azure HDInsight][ssisclustercreate]
- [Löschen einer Clusteraufgabe in Azure HDInsight][ssisclusterdelete]
- [Verbindungs-Manager für Azure-Abonnements][connectionmanager]

Greifen Sie [hier][ssispack] auf weitere Informationen zum Azure Feature Pack für SSIS zu.


##<a id="nextsteps"></a> Nächste Schritte
In diesem Artikel haben Sie mehrere Möglichkeiten der Erstellung von HDInsight-Clustern kennengelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit Azure HDInsight](hdinsight-get-started.md) – Erfahren Sie, wie Sie die Arbeit mit Ihrem HDInsight-Cluster aufnehmen können.
* [Verwenden von Sqoop mit HDInsight](hdinsight-use-sqoop.md) – Erfahren Sie, wie Sie Daten zwischen HDInsight und der SQL-Datenbank oder SQL Server kopieren können.
* [Verwalten von HDInsight mit PowerShell](hdinsight-administer-use-powershell.md) – Erfahren Sie, wie Sie mit HDInsight unter Verwendung von Azure PowerShell arbeiten können.
* [Programmgesteuerte Übermittlung von Hadoop-Jobs](hdinsight-submit-hadoop-jobs-programmatically.md) – Erfahren Sie, wie Sie Aufträge programmgesteuert an HDInsight übermitteln können.
* [Dokumentation zum Azure HDInsight SDK][hdinsight-sdk-documentation] – Machen Sie sich mit dem HDInsight SDK vertraut.



##Anhang A – ARM-Vorlage

Die folgende Azure-Ressourcen-Manager-Vorlage erstellt einen Hadoop-Cluster mit abhängigem Azure-Speicherkonto.

	{
	  "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
	  "contentVersion": "1.0.0.0",
	  "parameters": {
	    "location": {
	      "type": "string",
	      "defaultValue": "North Europe",
	      "allowedValues": [
	        "North Europe"
	      ],
	      "metadata": {
	        "description": "The location where all azure resources will be deployed."
	      }
	    },
	    "clusterName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the HDInsight cluster to create."
	      }
	    },
	    "clusterLoginUserName": {
	      "type": "string",
	      "defaultValue": "admin",
	      "metadata": {
	        "description": "These credentials can be used to submit jobs to the cluster and to log into cluster dashboards."
	      }
	    },
	    "clusterLoginPassword": {
	      "type": "securestring",
	      "metadata": {
	        "description": "The password for the cluster login."
	      }
	    },
	    "sshUserName": {
	      "type": "string",
	      "defaultValue": "hdiuser",
	      "metadata": {
	        "description": "These credentials can be used to remotely access the cluster and the edge node virtual machine."
	      }
	    },
	    "sshPassword": {
	      "type": "securestring",
	      "metadata": {
	        "description": "The password for the ssh user."
	      }
	    },
	    "clusterStorageAccountName": {
	      "type": "string",
	      "metadata": {
	        "description": "The name of the storage account to be created and be used as the cluster's storage."
	      }
	    },
	    "clusterStorageType": {
	      "type": "string",
	      "defaultValue": "Standard_LRS",
	      "allowedValues": [
	        "Standard_LRS",
	        "Standard_GRS",
	        "Standard_ZRS"
	      ]
	    },
	    "clusterWorkerNodeCount": {
	      "type": "int",
	      "defaultValue": 4,
	      "metadata": {
	        "description": "The number of nodes in the HDInsight cluster."
	      }
	    }
	  },
	  "variables": {},
	  "resources": [
	    {
	      "name": "[parameters('clusterStorageAccountName')]",
	      "type": "Microsoft.Storage/storageAccounts",
	      "location": "[parameters('location')]",
	      "apiVersion": "2015-05-01-preview",
	      "dependsOn": [],
	      "tags": {},
	      "properties": {
	        "accountType": "[parameters('clusterStorageType')]"
	      }
	    },
	    {
	      "name": "[parameters('clusterName')]",
	      "type": "Microsoft.HDInsight/clusters",
	      "location": "[parameters('location')]",
	      "apiVersion": "2015-03-01-preview",
	      "dependsOn": [
	        "[concat('Microsoft.Storage/storageAccounts/',parameters('clusterStorageAccountName'))]"
	      ],
	      "tags": {},
	      "properties": {
	        "clusterVersion": "3.2",
	        "osType": "Linux",
	        "clusterDefinition": {
	          "kind": "hadoop",
	          "configurations": {
	            "gateway": {
	              "restAuthCredential.isEnabled": true,
	              "restAuthCredential.username": "[parameters('clusterLoginUserName')]",
	              "restAuthCredential.password": "[parameters('clusterLoginPassword')]"
	            }
	          }
	        },
	        "storageProfile": {
	          "storageaccounts": [
	            {
	              "name": "[concat(parameters('clusterStorageAccountName'),'.blob.core.windows.net')]",
	              "isDefault": true,
	              "container": "[parameters('clusterName')]",
	              "key": "[listKeys(resourceId('Microsoft.Storage/storageAccounts', parameters('clusterStorageAccountName')), '2015-05-01-preview').key1]"
	            }
	          ]
	        },
	        "computeProfile": {
	          "roles": [
	            {
	              "name": "headnode",
	              "targetInstanceCount": "1",
	              "hardwareProfile": {
	                "vmSize": "Large"
	              },
	              "osProfile": {
	                "linuxOperatingSystemProfile": {
	                  "username": "[parameters('sshUserName')]",
	                  "password": "[parameters('sshPassword')]"
	                }
	              }
	            },
	            {
	              "name": "workernode",
	              "targetInstanceCount": "[parameters('clusterWorkerNodeCount')]",
	              "hardwareProfile": {
	                "vmSize": "Large"
	              },
	              "osProfile": {
	                "linuxOperatingSystemProfile": {
	                  "username": "[parameters('sshUserName')]",
	                  "password": "[parameters('sshPassword')]"
	                }
	              }
	            }
	          ]
	        }
	      }
	    }
	  ],
	  "outputs": {
	    "cluster": {
	      "type": "object",
	      "value": "[reference(resourceId('Microsoft.HDInsight/clusters',parameters('clusterName')))]"
	    }
	  }
	}


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-preview-portal]: https://manage.windowsazure.com
[connectionmanager]: http://msdn.microsoft.com/de-DE/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/de-DE/library/mt146770(v=sql.120).aspx
[ssisclustercreate]: http://msdn.microsoft.com/de-DE/library/mt146774(v=sql.120).aspx
[ssisclusterdelete]: http://msdn.microsoft.com/de-DE/library/mt146778(v=sql.120).aspx

<!---HONumber=Nov15_HO2-->