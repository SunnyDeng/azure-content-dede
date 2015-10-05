<properties 
   pageTitle="Benutzerdefiniertes Bereitstellen von Hadoop-Clustern in HDInsight | Microsoft Azure" 
   description="Erfahren Sie, wie Sie Cluster für Azure HDInsight mithilfe von Azure-Portal, Azure PowerShell, über die Befehlszeile oder ein .NET SDK benutzerdefiniert bereitstellen." 
   services="hdinsight" 
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
   ms.date="09/21/2015"
   ms.author="jgao"/>

#Bereitstellen von Hadoop-Clustern in HDInsight

Informationen zur Planung der Bereitstellung von HDInsight-Clustern.

[AZURE.INCLUDE [hdinsight-azure-portal](../../includes/hdinsight-azure-portal.md)]

* [Bereitstellen von Hadoop-Clustern in HDInsight](hdinsight-provision-clusters.md) 

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
 
- **Betriebssystem**

	Sie können HDInsight-Cluster unter einem der folgenden beiden Betriebssysteme bereitstellen: – **HDInsight für Windows (Windows Server 2012 R2 Datacenter)**: – **HDInsight unter Linux (Ubuntu 12.04 LTS für Linux) (Vorschau)**: HDInsight bietet die Möglichkeit, Linux-Cluster in Azure zu konfigurieren. Konfigurieren Sie einen Linux-Cluster, wenn Sie mit Linux oder Unix und der Migration von einer vorhandenen Linux-basierten Hadoop-Lösung vertraut sind, oder Sie eine einfache Integration mit Komponenten des Hadoop-Systems wünschen, die für Linux konzipiert sind. Weitere Informationen finden Sie unter [Erste Schritte mit Hadoop unter Linux in HDInsight](hdinsight-hadoop-linux-get-started.md).


- **HDInsight-Version**

	Dient zum Ermitteln der Version von HDInsight, die für diesen Cluster verwendet werden soll. Weitere Informationen finden Sie unter [Hadoop-Clusterversionen und -komponenten in HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409).

- **Clustertyp** und **Clustergröße (auch Datenknoten)**

	Mit HDInsight können Kunden verschiedene Clustertypen für unterschiedliche Datenanalyseworkloads bereitstellen. Aktuell angebotene Clustertypen sind:
	
	- Hadoop-Cluster: für Abfrage- und Analyseworkloads
	- HBase-Cluster: für NoSQL-Workloads
	- Storm-Cluster: für Workloads für die Ereignisverarbeitung in Echtzeit
	- Spark-Cluster (Vorschau): für die Verarbeitung im Arbeitsspeicher, interaktive Abfragen, Streaming und Workloads für maschinelles Lernen.

	![HDInsight-Cluster](./media/hdinsight-provision-clusters-v1/hdinsight.clusters.png)
 
	> [AZURE.NOTE]*Azure HDInsight-Cluster* werden auch als *Hadoop-Cluster in HDInsight* oder *HDInsight-Cluster* bezeichnet. Mitunter werden diese Bezeichnungen synonym mit *Hadoop-Cluster* verwendet. Alle beziehen sich auf Hadoop-Cluster, die in der Microsoft Azure-Umgebung gehostet werden.

	Innerhalb eines bestimmten Clustertyps übernehmen die verschiedenen Knoten unterschiedliche Rollen, die es Kunden ermöglichen, die Knoten in einer bestimmten Rolle entsprechend ihres jeweiligen Workloads zu dimensionieren. Beispielsweise können bei einem Hadoop-Cluster die Workerknoten mit großem Arbeitsspeicher bereitgestellt werden, wenn die durchzuführenden Analysen speicherintensiv sind.

	![HDInsight Hadoop-Clusterrollen](./media/hdinsight-provision-clusters-v1/HDInsight.Hadoop.roles.png)

	Hadoop-Cluster für HDInsight werden mit zwei Rollen bereitgestellt:

	- Hauptknoten (2 Knoten)
	- Datenknoten (mindestens 1 Knoten)

	![HDInsight Hadoop-Clusterrollen](./media/hdinsight-provision-clusters-v1/HDInsight.HBase.roles.png)

	HBase-Cluster für HDInsight werden mit drei Rollen bereitgestellt: – Hauptserver (2 Knoten) – Regionsserver (mindestens 1 Knoten) – Master-/Zookeeper-Knoten (3 Knoten)
	
	![HDInsight Hadoop-Clusterrollen](./media/hdinsight-provision-clusters-v1/HDInsight.Storm.roles.png)

	Storm-Cluster für HDInsight werden mit drei Rollen bereitgestellt: – Nimbus-Knoten (2 Knoten) – Supervisor-Server (mindestens 1 Knoten) – Zookeeper-Knoten (3 Knoten)
	

	![HDInsight Hadoop-Clusterrollen](./media/hdinsight-provision-clusters-v1/HDInsight.Spark.roles.png)

	Spark-Cluster für HDInsight werden mit drei Rollen bereitgestellt: – Hauptknoten (2 Knoten) – Workerknoten (mindestens 1 Knoten) – Zookeeper-Knoten (3 Knoten) (für Zookeeper vom Typ "A1" kostenlos)

	Kunden wird die Nutzung dieser Knoten für die Laufzeit des Clusters in Rechnung gestellt. Die Abrechnung beginnt, sobald ein Cluster erstellt wurde, und endet, wenn der Cluster gelöscht wird (bei Clustern ist kein Aufheben der Zuweisung oder Anhalten möglich). Die Clustergröße wirkt sich auf die Clusterkosten aus. Zu Lernzwecken wird empfohlen, einen Datenknoten zu verwenden. Weitere Informationen zu den Preisen von HDInsight finden Sie unter [HDInsight – Preise](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


	>[AZURE.NOTE]Die Begrenzung der Clustergröße variiert je nach Azure-Abonnement. Wenden Sie sich an den Azure-Abrechnungssupport, um diese Begrenzung zu erhöhen.
	
- **Region/virtuelles Netzwerk (auch Speicherort genannt)**

	![Azure-Regionen](./media/hdinsight-provision-clusters-v1/Azure.regions.png)

	Um eine Liste der unterstützten Regionen zu erhalten, klicken Sie in der Dropdownliste **Region** auf [HDInsight-Preise](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Knotengröße**

	![VM-Größen für HDInsight-Knoten](./media/hdinsight-provision-clusters-v1/hdinsight.node.sizes.png)

	Wählen Sie die VM-Größe für die Knoten aus. Weitere Informationen finden Sie unter [Größen für Clouddienste](cloud-services-sizes-specs.md).

	Je nach Wahl der VMs können Ihre Kosten variieren. HDInsight verwendet für Clusterknoten VMs mit Standardtarif. Informationen über die Auswirkungen der VM-Größe auf Ihre Kosten finden Sie unter <a href="http://azure.microsoft.com/pricing/details/hdinsight/" target="_blank">HDInsight-Preise</a>.


- **HDInsight-Benutzer**

	Die HDInsight-Cluster ermöglichen Ihnen, während der Bereitstellung zwei Benutzerkonten zu konfigurieren:

	- HTTP-Benutzer. Der Standardbenutzername bei der grundlegenden Konfiguration im Azure-Portal ist "admin".
	- RDP-Benutzer (Windows-Cluster): Dient zum Herstellen einer Verbindung mit dem Cluster über RDP. Wenn Sie das Konto erstellen, müssen Sie ein Ablaufdatum festlegen, das maximal 90 Tage hinter dem aktuellen Datum liegt. 
	- SSH-Benutzer (Linux-Cluster): Dient zum Herstellen einer Verbindung mit dem Cluster über SSH. Sie können zusätzliche SSH-Benutzerkonten erstellen, nachdem der Cluster erstellt wurde, indem Sie die Schritte unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md) ausführen.
  
 

- **Azure-Speicherkonto**


	Das ursprüngliche HDFS nutzt viele lokale Datenträger im Cluster. HDInsight verwendet zur Datenspeicherung stattdessen Azure-Blobspeicher. Azure-Blobspeicher stellt eine robuste, universelle Speicherlösung dar, die problemlos mit HDInsight integriert werden kann. Über eine HDFS-Schnittstelle (Hadoop Distributed File System) können sämtliche Komponenten in HDInsight direkt mit strukturierten oder unstrukturierten Daten im Blobspeicher arbeiten. Die Speicherung von Daten im Blobspeicher sorgt dafür, dass die HDInsight-Cluster, die für Berechnungen verwendet werden, sicher gelöscht werden können, ohne Benutzerdaten zu verlieren.
	
	Bei der Konfiguration müssen Sie ein Azure-Speicherkonto und einen Azure-Blobspeichercontainer für dieses Konto angeben. Bei einigen Bereitstellungsprozessen müssen das Azure-Speicherkonto und der Blobspeichercontainer zuvor erstellt werden. Der Blobspeichercontainer wird vom Cluster als Standardspeicherort verwendet. Optional können Sie zusätzliche Azure-Speicherkonten (verknüpften Speicher) angeben, auf die der Cluster zugreifen kann. Darüber hinaus kann der Cluster auch auf alle Blobcontainer zugreifen, die mit vollständig öffentlichem Lesezugriff oder nur für Blobs öffentlichem Lesezugriff konfiguriert sind. Weitere Informationen zum Einschränken des Zugriffs finden Sie unter [Verwalten des Zugriffs auf Azure-Speicherressourcen](storage-manage-access-to-resources.md).

	![HDInsight-Speicher](./media/hdinsight-provision-clusters-v1/HDInsight.storage.png)
	
	>[AZURE.NOTE]Ein Blobspeichercontainer stellt eine Gruppierung von Blobs bereit (siehe die folgende Abbildung):
	
	![Azure-Blobspeicher](./media/hdinsight-provision-clusters-v1/Azure.blob.storage.jpg)
	  
	
	>[AZURE.WARNING]Geben Sie keinen Blob-Speichercontainer für mehrere Cluster frei. Dies wird nicht unterstützt.
	
	Weitere Informationen zu sekundären Blobspeichern finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](hdinsight-use-blob-storage.md).

- **Hive/Oozie-Metastore**

	Metastore enthält Hive- und Oozie-Metadaten, wie z. B. Hive-Tabellen, Partitionen, Schemata und Spalten. Mithilfe von Metastore können Sie Ihre Hive und Oozie-Metadaten beibehalten, damit Sie nicht Hive-Tabellen oder Oozie-Aufträge neu erstellen müssen, wenn Sie einen neuen Cluster bereitstellen. Standardmäßig speichert Hive diese Informationen in einer eingebetteten Azure SQL-Datenbank. Die eingebettete Datenbank kann die Metadaten nicht beibehalten, wenn der Cluster gelöscht wird. Angenommen, Sie haben einen Cluster mit einem Hive-Metastore bereitgestellt. Sie haben einige Hive-Tabellen erstellt. Nachdem Sie den Cluster gelöscht und mit demselben Hive-Metastore wiederhergestellt haben, werden dieselben Tabellen angezeigt, die Sie im ursprünglichen Cluster erstellt haben.

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

Die Cluster können die Änderungen aufgrund des Re-Imagings nicht beibehalten. Weitere Informationen finden Sie unter [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) (Neustart von Rolleninstanzen aufgrund von Betriebssystemupdates – in englischer Sprache). Um die Änderungen für die Laufzeit des Clusters beizubehalten, können Sie während des Bereitstellungsprozesses die HDInsight-Clusteranpassung nutzen.

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
	
Weitere Informationen finden Sie im Blog von Azim Uddin mit dem Titel [Customizing HDInsight Cluster provisioning](http://blogs.msdn.com/b/bigdatasupport/archive/2014/04/15/customizing-hdinsight-cluster-provisioning-via-powershell-and-net-sdk.aspx) (Anpassen der Bereitstellung von HDInsight-Clustern; in englischer Sprache).




### Anpassen von Clustern mithilfe von Skriptaktionen

Sie können zusätzliche Komponenten installieren oder die Clusterkonfiguration mithilfe von Skripts während der Bereitstellung anpassen. Diese Skripts werden mithilfe der Konfigurationsoption **Skriptaktion** aufgerufen, die vom Azure-Verwaltungsportal, von HDInsight Windows PowerShell-Cmdlets oder dem HDInsight .NET SDK verwendet werden kann. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster.md).


### Verwenden virtueller Azure-Netzwerke

[Azure Virtual Network](http://azure.microsoft.com/documentation/services/virtual-network/) ermöglicht das Erstellen eines sicheren, beständigen Netzwerk mit allen Ressourcen, die Sie für Ihre Lösung benötigen. Virtuelle Netzwerken ermöglichen Folgendes:

* Verbinden von Cloudressourcen in einem privaten Netzwerk (nur in der Cloud)

	![Diagramm der Nur-Cloud-Konfiguration](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-cloud-only.png)

* Verbinden Ihrer Cloudressourcen mithilfe eines virtuellen privaten Netzwerks (VPN) mit dem Netzwerk in Ihrem lokalen Datencenter (Standort-zu-Standort oder Punkt-zu-Standort).

	In einer Standort-zu-Standort-Konfiguration können Sie mehrere Ressourcen aus Ihrem Rechenzentrum über ein Hardware-VPN oder den Routing- und RAS-Dienst mit dem virtuellen Azure-Netzwerk verbinden.

	![Diagramm der Standort-zu-Standort-Konfiguration](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-site-to-site.png)

	In einer Punkt-zu-Standort-Konfiguration können Sie eine bestimmte Ressource über ein Software-VPN mit dem virtuellen Azure-Netzwerk verbinden.

	![Diagramm der Punkt-zu-Standort-Konfiguration](./media/hdinsight-provision-clusters-v1/hdinsight-vnet-point-to-site.png)

Weitere Informationen zu Features, Vorteilen und Funktionen von virtuellen Netzwerken finden Sie unter [Überblick über virtuelle Azure-Netzwerke](../virtual-network/virtual-networks-overview.md).

> [AZURE.NOTE]Sie müssen das virtuelle Azure-Netzwerk erstellen, bevor Sie einen HDInsight-Cluster bereitstellen. Weitere Informationen finden Sie unter [Konfigurationsaufgaben für virtuelle Netzwerke](../services/virtual-machines/).
>
> Azure HDInsight unterstützt nur standortbasierte virtuelle Netzwerke und kann momentan nicht mit affinitätsgruppenbasierten virtuellen Netzwerken verwendet werden. Verwenden Sie das Azure PowerShell-Cmdlet "Get-AzureVNetConfig", um zu prüfen, ob ein vorhandenes virtuelles Netzwerk in Azure standortbasiert ist. Wenn das virtuelle Netzwerk nicht standortbasiert ist, haben Sie die folgenden Optionen:
>
> - Exportieren Sie die vorhandene virtuelle Netzwerkkonfiguration, und erstellen Sie ein neues virtuelles Netzwerk. Alle neuen virtuellen Netzwerke sind standardmäßig standortbasiert.
> - Migrieren Sie zu einem standortbasierten virtuellen Netzwerk. Siehe [Migrating Existing Services to Regional Scope](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/) (in englischer Sprache).
>
> Sie sollten unbedingt ein einziges Subnetz pro Cluster verwenden.

## Bereitstellungstools

- Das Azure-Portal
- Azure PowerShell
- .NET SDK
- Befehlszeilenschnittstelle (CLI)

### Verwenden des Azure-Portals

Erläuterung der Felder finden Sie in den [grundlegenden Konfigurationsoptionen] und [erweiterten Konfigurationsoptionen] .

**So erstellen Sie einen HDInsight-Cluster mit der benutzerdefinierten Erstellungsoption**

1. Melden Sie sich beim [Azure-Portal][azure-management-portal] an.
2. Klicken Sie im unteren Seitenbereich auf **+ NEW**, anschließend auf **DATA SERVICES**, auf **HDINSIGHT**und zuletzt auf **CUSTOM CREATE**.
3. Geben Sie auf der Seite **Clusterdetails** die folgenden Daten ein:

	- Clustername
	- Abonnementname
	- Clustertyp
	- Betriebssystem
	- HDInsight-Version

4. Geben Sie auf der Seite **Cluster konfigurieren** die folgenden Daten ein:

	- Datenknoten
	- Region/virtuelles Netzwerk:
	- Größe des Hauptknotens
	- Datenknotengröße

5. Geben Sie auf der Seite **Clusterbenutzer konfigurieren** die folgenden Daten ein:

	- HTTP-Benutzername
	- HTTP-Kennwort/Kennwort bestätigen
	- Remotedesktop für Cluster aktivieren
	- Hive-/Oozie-Metastore eingeben

6. Wenn Sie auf dem vorherigen Bildschirm den Hive/Oozie-Metastore eingegeben haben, geben Sie auf der Seite **Hive/Oozie-Metastore konfigurieren** die folgenden Daten ein:

	- Hive-Metastore-Datenbank
	- Datenbankbenutzer
	- Kennwort des Datenbankbenutzers
	- Oozie-Metastore-Datenbank
	- Datenbankbenutzer
	- Kennwort des Datenbankbenutzers

7. Geben Sie auf der Seite **Speicherkonto** die folgenden Werte ein:

	- Speicherkonto
	- Kontoname
	- Standardcontainer
	- Zusätzliche Speicherkonten

8. Klicken Sie auf der Seite **Skriptaktionen** auf **Skriptaktion hinzufügen**, um Einzelheiten zum benutzerdefinierten Skript anzugeben, das Sie zum Anpassen eines Clusters bei seiner Erstellung ausführen möchten. Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster.md).

	- Name: Geben Sie einen Namen für die Skriptaktion an.
	- Skript-URI: Geben Sie den Uniform Resource Identifier (URI) für das Skript an, das aufgerufen wird, um den Cluster anzupassen.
	- Knotentyp: Gibt die Knoten an, auf denen das Anpassungsskript ausgeführt wird. Sie können <b>Alle Knoten</b>, <b>Nur Hauptknoten</b> oder <b>Nur Datenknoten</b> auswählen.
	- Parameter: Geben Sie die Parameter an, wenn dies für das Skript erforderlich ist.</td></tr>

	Sie können dem Cluster mehr als eine Skriptaktion zum Installieren von mehreren Komponenten hinzufügen. Nachdem Sie die Skripts hinzugefügt haben, klicken Sie auf das Häkchen, um die Bereitstellung des Clusters zu starten.

### Verwenden von Azure PowerShell
Azure PowerShell ist eine leistungsstarke Skriptumgebung, mit der Sie die Bereitstellung und Verwaltung Ihrer Arbeitsauslastungen in Azure steuern und automatisieren können. Dieser Abschnitt enthält Anweisungen für die Bereitstellung eines HDInsight-Clusters mit Azure PowerShell. Weitere Informationen zum Konfigurieren einer Arbeitsstation für die Ausführung von HDInsight PowerShell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md). Weitere Informationen zum Verwenden von Azure PowerShell mit HDInsight finden Sie unter [Verwalten von HDInsight mit PowerShell](hdinsight-administer-use-powershell.md). Eine Liste der HDInsight Windows PowerShell-Cmdlets finden Sie unter [HDInsight-Cmdlet-Referenz](https://msdn.microsoft.com/library/azure/dn858087.aspx).

> [AZURE.NOTE]Die Skripts in diesem Abschnitt dienen zum Konfigurieren eines HDInsight-Clusters in einem virtuellen Azure-Netzwerk, jedoch nicht zur Erstellung des virtuellen Azure-Netzwerks. Informationen zur Erstellung von virtuellen Azure-Netzwerken finden Sie unter [Aufgaben bei der Konfiguration virtueller Netzwerke](../services/virtual-machines/).

Die folgenden Prozeduren müssen für die Bereitstellung eines HDInsight-Clusters mithilfe von Azure PowerShell ausgeführt werden:

- Erstellen eines Azure-Speicherkontos
- Erstellen eines Azure-Blobcontainers
- Erstellen eines HDInsight-Clusters

Sie können die Windows PowerShell-Konsole oder Windows PowerShell Integrated Scripting Environment (ISE) zum Ausführen der Skripts verwenden.

HDInsight verwendet Azure-Blobspeichercontainer als Standarddateisystem. Sie benötigen ein Speicherkonto und einen Speichercontainer, um einen HDInsight-Cluster erstellen zu können. Das Speicherkonto muss sich im selben Datencenter wie der HDInsight-Cluster befinden.

**Verbinden mit Ihrem Azure-Konto**

	Add-AzureAccount

Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

**Erstellen eines Azure-Speicherkontos**

	$storageAccountName = "<StorageAcccountName>"	# Provide a Storage account name
	$location = "<MicrosoftDataCenter>"				# For example, "West US"

	# Create an Azure Storage account
	New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

Falls Sie bereits ein Speicherkonto haben und den Kontonamen und den Kontoschlüssel nicht kennen, können Sie diese Informationen mit den folgenden Windows PowerShell-Befehlen abrufen:

	# List Storage accounts for the current subscription
	Get-AzureStorageAccount

	# List the keys for a Storage account
	Get-AzureStorageKey "<StorageAccountName>"

**Erstellen eines Azure-Blobspeichercontainers**

	$storageAccountName = "<StorageAccountName>"	# Provide the Storage account name
	$containerName="<ContainerName>"				# Provide a container name

	# Create a storage context object
	$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
	$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName
	                                       -StorageAccountKey $storageAccountKey  

	# Create a Blob storage container
	New-AzureStorageContainer -Name $containerName -Context $destContext

Sobald Sie Ihr Speicherkonto und Ihren Blobcontainer vorbereitet haben, können Sie einen Cluster erstellen.

**So stellen Sie einen HDInsight-Cluster bereit**

> [AZURE.NOTE]Die Azure PowerShell-Cmdlets sind die einzige empfohlene Methode zum Ändern von Konfigurationsvariablen in einem HDInsight-Cluster. Änderungen an Hadoop-Konfigurationsdateien, während Sie per Remotedesktop mit dem Cluster verbunden sind, können bei einem Patchvorgang des Clusters überschrieben werden. Die per Azure PowerShell festgelegten Konfigurationswerte bleiben erhalten, wenn das Cluster gepatcht wird.

- Führen Sie die folgenden Befehle in einem Azure PowerShell-Konsolenfenster aus:

		$subscriptionName = "<AzureSubscriptionName>"	  # The Azure subscription used for the HDInsight cluster to be created
	
		$storageAccountName = "<AzureStorageAccountName>" # HDInsight cluster requires an existing Azure Storage account to be used as the default file system
	
		$clusterName = "<HDInsightClusterName>"			  # The name for the HDInsight cluster to be created
		$clusterNodes = <ClusterSizeInNodes>              # The number of nodes in the HDInsight cluster
		$hadoopUserName = "<HadoopUserName>"              # User name for the Hadoop user. You will use this account to connect to the cluster and run jobs.
		$hadoopUserPassword = "<HadoopUserPassword>"
	
		$secPassword = ConvertTo-SecureString $hadoopUserPassword -AsPlainText -Force
		$credential = New-Object System.Management.Automation.PSCredential($hadoopUserName,$secPassword)
	
		# Get the storage primary key based on the account name
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$containerName = $clusterName				# Azure Blob container that is used as the default file system for the HDInsight cluster
	
		# The location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$location = Get-AzureStorageAccount -StorageAccountName $storageAccountName | %{$_.Location}
	
		# Create a new HDInsight cluster
		New-AzureHDInsightCluster -Name $clusterName -Credential $credential -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes -ClusterType Hadoop

	>[AZURE.NOTE]Die Befehle $hadoopUserName und $hadoopUserPassword werden zum Erstellen des Hadoop-Benutzerkontos für den Cluster verwendet. Sie verwenden dieses Konto, um die Verbindung mit dem Cluster herzustellen und Aufträge auszuführen.. Wenn Sie im Azure-Portal die Option "Schnellerfassung" verwenden, um einen Cluster bereitzustellen, lautet der Hadoop-Standardbenutzername "admin". Sie dürfen dieses Konto nicht mit dem Benutzerkonto für das Remotedesktopprotokoll (RDP) verwechseln. Das RDP-Benutzerkonto muss sich vom Hadoop-Benutzerkonto unterscheiden. Weitere Informationen finden Sie unter "Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Verwaltungsportal".

	Die Bereitstellung des Clusters kann einige Minuten dauern.

	![HDI.CLI.Provision][image-hdi-ps-provision]

**Bereitstellen eines HDInsight-Clusters mit benutzerdefinierten Konfigurationsoptionen**

Bei der Bereitstellung eines Clusters können Sie andere Konfigurationsoptionen verwenden, z. B. Verbinden mit mehreren Azure-Speichercontainern, Verwenden eines virtuellen Netzwerks oder Verwenden einer Azure SQL-Datenbank für Hive- und Oozie-Metastores. Auf diese Weise können Sie die Lebensdauer Ihrer Daten und Metadaten von der Lebensdauer des Clusters trennen.

> [AZURE.NOTE]Die Windows PowerShell-Cmdlets sind die einzige empfohlene Methode zum Ändern von Konfigurationsvariablen in einem HDInsight-Cluster. Änderungen an Hadoop-Konfigurationsdateien, während Sie per Remotedesktop mit dem Cluster verbunden sind, können bei einem Patchvorgang des Clusters überschrieben werden. Die per Azure PowerShell festgelegten Konfigurationswerte bleiben erhalten, wenn das Cluster gepatcht wird.

- Führen Sie die folgenden Befehle in einem Windows PowerShell-Fenster aus:

		$subscriptionName = "<SubscriptionName>"
		$clusterName = "<ClusterName>"
		$location = "<MicrosoftDataCenter>"
		$clusterNodes = <ClusterSizeInNodes>

		$storageAccountName_Default = "<DefaultFileSystemStorageAccountName>"
		$containerName_Default = "<DefaultFileSystemContainerName>"

		$storageAccountName_Add1 = "<AdditionalStorageAccountName>"

		$hiveSQLDatabaseServerName = "<SQLDatabaseServerNameForHiveMetastore>"
		$hiveSQLDatabaseName = "<SQLDatabaseDatabaseNameForHiveMetastore>"
		$oozieSQLDatabaseServerName = "<SQLDatabaseServerNameForOozieMetastore>"
		$oozieSQLDatabaseName = "<SQLDatabaseDatabaseNameForOozieMetastore>"

		# Get the virtual network ID and subnet name
		$vnetID = "<AzureVirtualNetworkID>"
		$subNetName = "<AzureVirtualNetworkSubNetName>"

		# Get the Storage account keys
		Select-AzureSubscription $subscriptionName
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$storageAccountKey_Add1 = Get-AzureStorageKey $storageAccountName_Add1 | %{ $_.Primary }

		$oozieCreds = Get-Credential -Message "Oozie metastore"
		$hiveCreds = Get-Credential -Message "Hive metastore"

		# Create a Blob storage container
		$dest1Context = New-AzureStorageContext -StorageAccountName $storageAccountName_Default -StorageAccountKey $storageAccountKey_Default  
		New-AzureStorageContainer -Name $containerName_Default -Context $dest1Context

		# Create a new HDInsight cluster
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Add1.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Add1 |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$hiveSQLDatabaseServerName.database.windows.net" -DatabaseName $hiveSQLDatabaseName -Credential $hiveCreds -MetastoreType HiveMetastore |
		    Add-AzureHDInsightMetastore -SqlAzureServerName "$oozieSQLDatabaseServerName.database.windows.net" -DatabaseName $oozieSQLDatabaseName -Credential $oozieCreds -MetastoreType OozieMetastore |
		        New-AzureHDInsightCluster -Name $clusterName -Location $location -VirtualNetworkId $vnetID -SubnetName $subNetName

	>[AZURE.NOTE]Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Dashboard der Azure SQL-Datenbank mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Azure Services** den Wert **Ja** aus, und klicken Sie auf **Speichern**.

**So listen Sie HDInsight-Cluster auf**

- Führen Sie den folgenden Befehl in einer Azure PowerShell-Konsole aus, um den HDInsight-Cluster aufzulisten und zu überprüfen, ob der Cluster erfolgreich bereitgestellt wurde:

		Get-AzureHDInsightCluster -Name <ClusterName>


### Verwenden der Azure-CLI

> [AZURE.NOTE]Zum Zeitpunkt der Erstellung dieses Dokuments am 29.8.2014 war der aktuelle Stand, dass die Azure-Befehlszeilenschnittstelle nicht verwendet werden kann, um Windows Azure einen Cluster zuzuordnen.

Eine weitere Möglichkeit zur Bereitstellung von HDInsight-Clustern ist die Azure-Befehlszeilenschnittstelle (Azure-CLI). Die Azure-Befehlszeilenschnittstelle ist in Node.js implementiert. Das Tool kann auf allen Plattformen verwendet werden, die Node.js unterstützen, einschließlich Windows, Mac und Linux. Sie können die Befehlszeilenschnittstelle an den folgenden Speicherorten installieren:

- **Node.js SDK** – <a href="https://www.npmjs.com/package/azure-mgmt-hdinsight" target="_blank">https://www.npmjs.com/package/azure-mgmt-hdinsight</a>
- **Azure CLI** – <a href="https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz" target="_blank">https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz</a>  

Eine allgemeine Anleitung für die Befehlszeilenschnittstelle finden Sie unter [Azure-CLI für Mac, Linux und Windows](../xplat-cli.md).

Im Folgenden finden Sie Anweisungen zum Installieren der plattformübergreifenden Befehlszeilenschnittstelle unter Linux und Windows und zum Verwenden der Befehlszeilenschnittstelle zum Bereitstellen eines Clusters.

- [Einrichten der Azure-Befehlszeilenschnittstelle für Linux](#clilin)
- [Einrichten der Azure-Befehlszeilenschnittstelle für Windows](#cliwin)
- [Bereitstellen von HDInsight-Clustern mithilfe der Azure-Befehlszeilenschnittstelle](#cliprovision)

#### <a id="clilin"></a>Einrichten der Azure-Befehlszeilenschnittstelle für Linux

Führen Sie die folgenden Verfahren zum Einrichten des Linux-Computers aus, um die Azure-Befehlszeilenschnittstelle (Azure-CLI) zu verwenden:

- Installieren der Azure-CLI mithilfe von Node.js-Paketmanager (NPM)
- Verbinden mit Ihrem Azure-Abonnement

**So installieren Sie die Azure-CLI mithilfe von NPM**

1.	Öffnen Sie auf dem Linux-Computer ein Terminalfenster, und führen Sie den folgenden Befehl aus:

		sudo npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

2.	Führen Sie den folgenden Befehl aus, um die Installation zu überprüfen:

		azure hdinsight -h

	Sie können den Parameter *-h* auf verschiedenen Ebenen verwenden, um Hilfeinformationen anzuzeigen. Zum Beispiel:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**Verbinden mit Ihrem Azure-Abonnement**

Bevor Sie die Azure-Befehlszeilenschnittstelle verwenden können, müssen Sie die Konnektivität zwischen Ihrer Arbeitsstation und Azure konfigurieren. Die Azure-Befehlszeilenschnittstelle verwendet Ihre Azure-Abonnementinformationen, um sich mit Ihrem Konto zu verbinden. Sie finden diese Informationen in Azure in einer Einstellungsveröffentlichungsdatei. Die Einstellungsveröffentlichungsdatei kann anschließend als persistente lokale Konfigurationseinstellung importiert werden, die von der Azure-Befehlszeilenschnittstelle in nachfolgenden Vorgängen verwendet wird. Sie müssen Ihre Veröffentlichungseinstellungen nur einmal importieren.

> [AZURE.NOTE]Die Einstellungsveröffentlichungsdatei enthält vertrauliche Daten. Microsoft empfiehlt, dass Sie die Datei löschen oder weitere Schritte unternehmen, um den Benutzerordner zu verschlüsseln, der die Datei enthält. Ändern Sie unter Windows die Ordnereigenschaften, oder verwenden Sie die BitLocker-Laufwerkverschlüsselung.


1.	Öffnen Sie ein Terminalfenster.
2.	Führen Sie den folgenden Befehl aus, um sich bei Ihrem Azure-Abonnement anzumelden:

		azure account download

	![HDI.Linux.CLIAccountDownloadImport](./media/hdinsight-provision-clusters/HDI.Linux.CLIAccountDownloadImport.png)

	Dieser Befehl öffnet die Webseite, von der Sie die Datei mit den Veröffentlichungseinstellungen herunterladen können. Wenn die Webseite nicht geöffnet wird, klicken Sie auf den Link im Terminalfenster, um die Browserseite zu öffnen und sich beim Portal anzumelden.

3.	Laden Sie die Datei mit den Veröffentlichungseinstellungen auf den Computer herunter.
5.	Führen Sie im Eingabeaufforderungsfenster den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei zu importieren:

		azure account import <path/to/the/file>


#### <a id="cliwin"></a>Einrichten der Azure-Befehlszeilenschnittstelle für Windows

Führen Sie die folgenden Verfahren zum Einrichten des Windows-Computers für die Verwendung der Azure-Befehlszeilenschnittstelle aus:

- Installieren der Azure-Befehlszeilenschnittstelle (mithilfe von NPM oder Windows Installer)
- Download und Import der Veröffentlichungseinstellungen für das Azure-Konto


Sie können die Azure-Befehlszeilenschnittstelle entweder über NPM oder Windows Installer installieren. Microsoft empfiehlt, dass Sie nur eine der beiden Optionen für die Installation verwenden.

**So installieren Sie die Azure-CLI mithilfe von NPM**

1.	Öffnen Sie die Webseite **www.nodejs.org**.
2.	Klicken Sie auf **INSTALL**, und befolgen Sie die Anweisungen unter Beibehaltung der Standardeinstellungen.
3.	Öffnen Sie die **Eingabeaufforderung** (bzw. *Azure-Eingabeaufforderung* oder *Developer-Eingabeaufforderung für VS2012*) auf Ihrer Arbeitsstation.
4.	Geben Sie im Eingabeaufforderungsfenster den folgenden Befehl ein.

		npm install -g https://github.com/azure/azure-xplat-cli/archive/hdinsight-February-18-2015.tar.gz

	> [AZURE.NOTE]Wenn die Fehlermeldung angezeigt wird, dass der NPM-Befehl nicht gefunden wurde, überprüfen Sie, ob sich die folgenden Pfade in der Path-Umgebungsvariablen PATH befinden: <i>C:\\Program Files (X 86) \\nodejs; C:\\Users[Benutzername]\\AppData\\Roaming\\npm</i> oder <i>C:\\Program Files\\nodejs; C:\\Users[Benutzername]\\AppData\\Roaming\\npm</i>

5.	Führen Sie den folgenden Befehl aus, um die Installation zu überprüfen:

		azure hdinsight -h

	Sie können den Parameter *-h* auf verschiedenen Ebenen verwenden, um Hilfeinformationen anzuzeigen. Beispiel:

		azure -h
		azure hdinsight -h
		azure hdinsight cluster -h
		azure hdinsight cluster create -h

**So installieren Sie die Azure-Befehlszeilenschnittstelle mit Windows Installer**

1.	Navigieren Sie zu ****http://azure.microsoft.com/downloads/**.
2.	Führen Sie einen Bildlauf nach unten zum Abschnitt **Befehlszeilentools** durch, und klicken Sie dann auf **Azure-Befehlszeilenschnittstelle**, und führen Sie den Assistenten für den Webplattform-Installer aus.

**Herunterladen und Importieren der Veröffentlichungseinstellungen**

Bevor Sie die Azure-Befehlszeilenschnittstelle verwenden können, müssen Sie die Konnektivität zwischen Ihrer Arbeitsstation und Azure konfigurieren. Die Azure-Befehlszeilenschnittstelle verwendet Ihre Azure-Abonnementinformationen, um sich mit Ihrem Konto zu verbinden. Sie finden diese Informationen in Azure in einer Einstellungsveröffentlichungsdatei. Die Einstellungsveröffentlichungsdatei kann anschließend als persistente lokale Konfigurationseinstellung importiert werden, die von der Azure-Befehlszeilenschnittstelle in nachfolgenden Vorgängen verwendet wird. Sie müssen Ihre Veröffentlichungseinstellungen nur einmal importieren.

> [AZURE.NOTE]Die Einstellungsveröffentlichungsdatei enthält vertrauliche Daten. Microsoft empfiehlt, dass Sie die Datei löschen oder weitere Schritte unternehmen, um den Benutzerordner zu verschlüsseln, der die Datei enthält. Windows: Ändern Sie die Ordnereigenschaften, oder verwenden Sie BitLocker.


1.	Öffnen Sie eine **Eingabeaufforderung**.
2.	Führen Sie den folgenden Befehl aus, um die Datei mit den Veröffentlichungseinstellungen herunterzuladen:

		azure account download

	![HDI.CLIAccountDownloadImport][image-cli-account-download-import]

	Dieser Befehl öffnet die Webseite, von der Sie die Datei mit den Veröffentlichungseinstellungen herunterladen können.

3.	Klicken Sie in der Aufforderung auf **Speichern** und geben Sie einen Speicherort für die Datei an.
5.	Führen Sie im Eingabeaufforderungsfenster den folgenden Befehl aus, um die Einstellungsveröffentlichungsdatei zu importieren:

		azure account import <path/to/the/file>

#### <a id="cliprovision"></a>Bereitstellen eines HDInsight-Clusters mithilfe der plattformübergreifenden Azure-Befehlszeilenschnittstelle

Die folgenden Verfahren werden benötigt, um einen HDInsight-Cluster mit der Azure-Befehlszeilenschnittstelle bereitzustellen:

- Erstellen eines Azure-Speicherkontos
- Bereitstellen eines Clusters

**So erstellen Sie ein Azure-Speicherkonto**

HDInsight verwendet Azure-Blob-Speichercontainer als Standarddateisystem. Sie benötigen ein Azure-Speicherkonto, um einen HDInsight-Cluster erstellen zu können. Das Speicherkonto muss sich im selben Rechenzentrum befinden.

- Führen Sie den folgenden Befehl in einem Eingabeaufforderungsfenster aus, um ein Azure-Speicherkonto zu erstellen:

		azure storage account create [options] <StorageAccountName>

	Wählen Sie einen geeigneten Ort für die Bereitstellung des HDInsight-Clusters aus, wenn Sie dazu aufgefordert werden. Der Speicher muss sich am gleichen Standort befinden wie der HDInsight-Cluster. HDInsight-Cluster sind momentan nur in den Regionen **Ostasien**, **Südostasien**, **Nordeuropa**, **Westeuropa**, **Osten USA**, **Westen USA**, **USA Nord Mitte** und **USA Süd Mitte** verfügbar.

Informationen zum Erstellen von Azure-Speicherkonten im Azure-Portal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos](../storage-create-storage-account.md).

Falls Sie bereits ein Speicherkonto haben, aber dessen Kontonamen und Kontoschlüssel nicht kennen, können Sie diese Daten mit den folgenden Befehlen abrufen:

	-- Lists Storage accounts
	azure storage account list

	-- Shows information for a Storage account
	azure storage account show <StorageAccountName>

	-- Lists the keys for a Storage account
	azure storage account keys list <StorageAccountName>

Ausführliche Informationen zum Abrufen dieser Informationen im Azure-Portal finden Sie unter [Erstellen, Verwalten oder Löschen eines Speicherkontos](../storage-create-storage-account.md) im Abschnitt *Anzeigen, Kopieren und Neuerstellen von Speicherzugriffsschlüsseln*.

Für einen HDInsight-Cluster benötigen Sie außerdem einen Container innerhalb des Speicherkontos. Falls das angegebene Speicherkonto noch keinen Container enthält, werden Sie vom Befehl *azure hdinsight cluster create* zur Eingabe eines Containernamens aufgefordert, und der Container wird erstellt. Sie können den folgenden Befehl verwenden, um den Container vorab zu erstellen:

	azure storage container create --account-name <StorageAccountName> --account-key <StorageAccountKey> [ContainerName]

Sobald Sie Ihr Speicherkonto und Ihren Blobcontainer vorbereitet haben, können Sie einen Cluster erstellen.

**Bereitstellen eines HDInsight-Clusters**

- Geben Sie im Eingabeaufforderungsfenster den folgenden Befehl ein:

		azure hdinsight cluster create --clusterName <ClusterName> --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey <storageAccountKey> --storageContainer <StorageContainerName> --dataNodeCount <NumberOfNodes> --location <DataCenterLocation> --userName <HDInsightClusterUsername> --password <HDInsightClusterPassword> --osType windows

	![HDI.CLIClusterCreation][image-cli-clustercreation]


**Bereitstellen eines HDInsight-Clusters mit einer Konfigurationsdatei**

Normalerweise erstellen Sie einen HDInsight-Cluster, führen Ihre Jobs aus und löschen den Cluster anschließend, um Kosten zu sparen. In der Azure-Befehlszeilenschnittstelle können Sie die Konfigurationen in einer Datei speichern, um diese bei zukünftigen Bereitstellungen von Clustern wiederverwenden zu können.

- Führen Sie im Eingabeaufforderungsfenster die folgenden Befehle aus:


		#Create the config file
		azure hdinsight cluster config create <file>

		#Add commands to create a basic cluster
		azure hdinsight cluster config set <file> --clusterName <ClusterName> --dataNodeCount <NumberOfNodes> --location "<DataCenterLocation>" --storageAccountName "<StorageAccountName>.blob.core.windows.net" --storageAccountKey "<StorageAccountKey>" --storageContainer "<BlobContainerName>" --userName "<Username>" --password "<UserPassword>" --osType windows

		#If required, include commands to use additional Blob storage with the cluster
		azure hdinsight cluster config storage add <file> --storageAccountName "<StorageAccountName>.blob.core.windows.net"
		       --storageAccountKey "<StorageAccountKey>"

		#If required, include commands to use a SQL database as a Hive metastore
		azure hdinsight cluster config metastore set <file> --type "hive" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<HiveDatabaseName>" --user "<Username>" --metastorePassword "<UserPassword>"

		#If required, include commands to use a SQL database as an Oozie metastore
		azure hdinsight cluster config metastore set <file> --type "oozie" --server "<SQLDatabaseName>.database.windows.net"
		       --database "<OozieDatabaseName>" --user "<SQLUsername>" --metastorePassword "<SQLPassword>"

		#Run this command to create a cluster by using the config file
		azure hdinsight cluster create --config <file>

	>[AZURE.NOTE]Die als Metastore verwendete Azure SQL-Datenbank muss für die Konnektivität mit anderen Azure-Diensten konfiguriert sein, inklusive Azure HDInsight. Klicken Sie im Dashboard der Azure SQL-Datenbank mit der rechten Maustaste auf den Servernamen. Dies ist der Server, auf dem die SQL-Datenbankinstanz läuft. Öffnen Sie die Serveransicht, klicken Sie auf **Konfigurieren**, wählen Sie unter **Azure Services** den Wert **Ja** aus, und klicken Sie auf **Speichern**.


	![HDI.CLIClusterCreationConfig][image-cli-clustercreation-config]


**Auflisten und Anzeigen von Clusterdetails**

- Mit den folgenden Befehlen können Sie Clusterdetails auflisten und anzeigen:

		azure hdinsight cluster list
		azure hdinsight cluster show <ClusterName>

	![HDI.CLIListCluster][image-cli-clusterlisting]


**Löschen eines Clusters**

- Mit dem folgenden Befehl können Sie ein Cluster löschen:

		azure hdinsight cluster delete <ClusterName>



### Verwenden des HDInsight .NET SDK
Das HDInsight .NET SDK enthält .NET-Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in einer .NET Framework-Anwendung.

Führen Sie die folgenden Schritte aus, um einen HDInsight-Cluster mithilfe des SDK bereitzustellen:

- Installieren des HDInsight .NET SDK
- Erstellen eines selbstsignierten Zertifikats
- Erstellen einer Konsolenanwendung
- Ausführen der Anwendung


**Installieren des HDInsight .NET SDK**

Sie können die neueste veröffentlichte Version des SDK von [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) herunterladen und installieren. Die Anweisungen dazu werden im nächsten Verfahren erläutert.

**So erstellen Sie ein selbstsigniertes Zertifikat**

Erstellen Sie ein selbstsigniertes Zertifikat, installieren Sie es auf Ihrer Arbeitsstation und laden Sie es in Ihr Azure-Abonnement hoch. Weitere Hinweise hierzu finden Sie unter [Erstellen eines selbstsignierten Zertifikats](http://go.microsoft.com/fwlink/?LinkId=511138).


**Erstellen einer Visual Studio-Konsolenanwendung**

1. Öffnen Sie Visual Studio 2013 oder 2015.

2. Klicken Sie im Menü **Datei** auf **Neu** und dann auf **Projekt**.

3. Unter **Neues Projekt** können Sie die folgenden Werte eingeben bzw. auswählen:

	|Eigenschaft|Wert|
	|--------|-----|
	|Vorlage|Vorlagen/Visual C#/Windows/Konsolenanwendung|
	|Name|CreateHDICluster|

4. Klicken Sie auf **OK**, um das Projekt zu erstellen.

5. Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**.

6. Führen Sie den folgenden Befehl in der Konsole aus, um die Pakete zu installieren:

		Install-Package Microsoft.Azure.Common.Authentication -pre
		Install-Package Microsoft.Azure.Management.HDInsight -Pre

	Diese Befehle installieren .NET-Bibliotheken und fügen Verweise zum aktuellen Visual Studio-Projekt hinzu.

7. Doppelklicken Sie im Projektmappen-Explorer auf **Program.cs**, um die Datei zu öffnen.
8. Ersetzen Sie den Code durch den folgenden Code:

		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;

		namespace CreateHDICluster
		{
		    internal class Program
		    {
		        private static HDInsightManagementClient _hdiManagementClient;
		
		        private static Guid SubscriptionId = new Guid("<AZURE SUBSCRIPTION ID>");
		        private const string ResourceGroupName = "<AZURE RESOURCEGROUP NAME>";

		        private const string NewClusterName = "<HDINSIGHT CLUSTER NAME>";
		        private const int NewClusterNumNodes = <NUMBER OF NODES>;
		        private const string NewClusterLocation = "<LOCATION>";  // Must match the Azure Storage account location
		        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
		        private const OSType NewClusterOSType = OSType.Windows;
		        private const string NewClusterVersion = "3.2";

		        private const string NewClusterUsername = "admin";
		        private const string NewClusterPassword = "<HTTP USER PASSWORD>";
		        private const string ExistingStorageName = "<STORAGE ACCOUNT NAME>.blob.core.windows.net";
		        private const string ExistingStorageKey = "<STORAGE ACCOUNT KEY>";
		        private const string ExistingContainer = "<DEFAULT CONTAINER NAME>"; 
		
		
		        private static void Main(string[] args)
		        {
		            var tokenCreds = GetTokenCloudCredentials();
		            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);
		
		            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);
		
		            CreateCluster();
		        }
		
		        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
		        {
		            var authFactory = new AuthenticationFactory();
		
		            var account = new AzureAccount { Type = AzureAccount.AccountType.User };
		
		            if (username != null && password != null)
		                account.Id = username;
		
		            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];
		
		            var accessToken =
		                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
		                    .AccessToken;
		
		            return new TokenCloudCredentials(accessToken);
		        }
		
		        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
		        {
		            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
		        }
		
		
		        private static void CreateCluster()
		        {
		            var parameters = new ClusterCreateParameters
		            {
		                ClusterSizeInNodes = NewClusterNumNodes,
		                Location = NewClusterLocation,
		                ClusterType = NewClusterType,
		                OSType = NewClusterOSType,
		                Version = NewClusterVersion,

		                UserName = NewClusterUsername,
		                Password = NewClusterPassword,

		                DefaultStorageAccountName = ExistingStorageName,
		                DefaultStorageAccountKey = ExistingStorageKey,
		                DefaultStorageContainer = ExistingContainer
		            };
		
		            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
		        }
			}
		}
		
10. Ersetzen Sie Klassenmemberwerte.

**So führen Sie die Anwendung aus**

Öffnen Sie die Anwendung in Visual Studio und drücken Sie **F5**, um die Anwendung auszuführen. In einem Konsolenfenster wird der Status der Anwendung angezeigt. Die Erstellung eines HDInsight-Clusters kann mehrere Minuten in Anspruch nehmen.


##<a id="nextsteps"></a> Nächste Schritte
In diesem Artikel haben Sie mehrere Möglichkeiten zu Bereitstellung von HDInsight-Clustern kennen gelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit Azure HDInsight](hdinsight-get-started.md) – Erfahren Sie, wie Sie die Arbeit mit Ihrem HDInsight-Cluster aufnehmen können.
* [Verwenden von Sqoop mit HDInsight](hdinsight-use-sqoop.md) – Erfahren Sie, wie Sie Daten zwischen HDInsight und der SQL-Datenbank oder SQL Server kopieren können.
* [Verwalten von HDInsight mit PowerShell](hdinsight-administer-use-powershell.md) – Erfahren Sie, wie Sie mit HDInsight unter Verwendung von Azure PowerShell arbeiten können.
* [Programmgesteuerte Übermittlung von Hadoop-Jobs](hdinsight-submit-hadoop-jobs-programmatically.md) – Erfahren Sie, wie Sie Aufträge programmgesteuert an HDInsight übermitteln können.
* [Dokumentation zum Azure HDInsight SDK][hdinsight-sdk-documentation] – Machen Sie sich mit dem HDInsight SDK vertraut.


[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx
[azure-management-portal]: https://manage.windowsazure.com

<!---HONumber=Sept15_HO4-->