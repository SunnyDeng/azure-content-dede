<properties
   	pageTitle="Erstellen von Hadoop-, HBase-, Storm oder Spark-Clustern unter Linux in HDInsight | Microsoft Azure"
   	description="Erfahren Sie, wie Hadoop-, HBase-, Storm- oder Spark-Cluster unter Linux für HDInsight mithilfe eines Browsers, der Azure-Befehlszeilenschnittstelle, Azure PowerShell, REST oder durch ein SDK erstellt werden."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="12/08/2015"
   	ms.author="nitinme"/>


#Erstellen von Linux-basierten Hadoop-Clustern in HDInsight

[AZURE.INCLUDE [Auswahl](../../includes/hdinsight-create-linux-cluster-selector.md)]

In diesem Dokument lernen Sie die verschiedenen Methoden zum Erstellen eines Linux-basierten HDInsight-Clusters in Azure sowie verschiedene optionale Konfigurationen kennen, die für Ihren Cluster verwendet werden können. HDInsight stellt Apache Hadoop, Apache Storm und Apache HBase als Dienste auf der Azure-Cloudplattform bereit.

> [AZURE.NOTE] Dieses Dokument beschreibt die verschiedenen Möglichkeiten zum Erstellen eines Clusters. Wenn Sie schnell in der Lage sein möchten, einen Cluster zu erstellen, lesen Sie [Erste Schritte mit Azure HDInsight unter Linux](../hdinsight-hadoop-linux-get-started.md).

## Was ist ein HDInsight-Cluster?

Ein Hadoop-Cluster besteht aus mehreren virtuellen Computern (Knoten), die zur verteilten Verarbeitung von Aufgaben im Cluster verwendet werden. Azure abstrahiert die Implementierungsdetails der Installation und Konfiguration einzelner Knoten, sodass Sie nur allgemeine Konfigurationsinformationen bereitstellen müssen.

###Clustertypen

Es stehen verschiedene Typen von HDInsight zur Verfügung:

| Clustertyp | Anforderung |
| ------------ | ----------------------------- |
| Hadoop | Abfragen und Analysen (Batchaufträge) |
| HBase | NoSQL-Datenspeicher |
| Storm | Ereignisverarbeitung in Echtzeit |
| Spark (Vorschau) | Arbeitsspeicherinterne Verarbeitung, interaktive Abfragen, Microbatch-Datenstromverarbeitung |

Während der Konfiguration wählen Sie einen dieser Typen für den Cluster aus. Sie können diesen grundlegenden Typen mithilfe von [Skriptaktionen](#scriptaction) weitere Technologien wie Hue oder R hinzufügen.

Jeder Clustertyp verfügt über eine eigene Terminologie für Knoten im Cluster, sowie über eine eigene Anzahl von Knoten und eine eigene VM-Standardgröße für jeden Knotentyp:

> [AZURE.IMPORTANT] Wenn Sie mehr als 32 Workerknoten planen, entweder bei Erstellung des Clusters oder durch eine Skalierung des Clusters nach der Erstellung, müssen Sie eine Hauptknotengröße von mindestens 8 Kernen und 14 GB Arbeitsspeicher (RAM) auswählen.

![HDInsight-Hadoop-Clusterknoten](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

Hadoop-Cluster für HDInsight verfügen über zwei Knotentypen:

- Hauptknoten (2 Knoten)
- Datenknoten (mindestens 1 Knoten)

![HDInsight-HBase-Clusterknoten](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

HBase-Cluster für HDInsight verfügen über drei Knotentypen: – Hauptserver (2 Knoten) – Regionsserver (mindestens 1 Knoten) – Master-/Zookeeperknoten (3 Knoten)

![HDInsight-Storm-Clusterknoten](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

Storm-Cluster für HDInsight verfügen über drei Knotentypen: – Nimbusknoten (2 Knoten) – Supervisorserver (mindestens 1 Knoten) – Zookeeperknoten (3 Knoten)


![HDInsight-Spark-Clusterknoten](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

Spark-Cluster für HDInsight verfügen über drei Knotentypen: – Hauptknoten (2 Knoten) – Workerknoten (mindestens 1 Knoten) – Zookeeperknoten (3 Knoten) (für Zookeeper-VM-Größen vom Typ "A1" kostenlos)

###Azure Storage für HDInsight

Jeder Clustertyp verfügt auch über mindestens ein Azure Storage-Konto, das dem Cluster zugeordnet ist. HDInsight verwendet Azure-Blobs aus diesen Speicherkonten als Datenspeicher für Ihren Cluster. Indem Sie die Daten separat vom Cluster speichern, können Sie Cluster löschen, wenn sie nicht benötigt werden, und dennoch Ihre Daten beibehalten. Sie können das gleiche Speicherkonto für einen neuen Cluster verwenden, wenn Sie weitere Analysen durchführen müssen. Weitere Informationen finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](../hdinsight-use-blob-storage.md).

## <a id="configuration"></a>Grundlegende Konfigurationsoptionen

In den folgenden Abschnitten werden die erforderlichen Konfigurationsoptionen beschrieben, die bei der Erstellung eines HDInsight-Clusters zur Verfügung stehen.

###Clustername

Der Clustername stellt einen eindeutigen Bezeichner für den Cluster bereit und wird als Teil des Domänennamens verwendet, um Zugriff auf den Cluster über das Internet zu ermöglichen. Auf einen Cluster mit dem Namen _mycluster_ kann beispielsweise über das Internet unter der Adresse _mycluster_.azurehdinsight.net zugegriffen werden.

Für den Clusternamen gelten die folgenden Richtlinien:

- Das Feld muss eine Zeichenfolge mit 3 bis 63 Zeichen enthalten.
- Das Feld darf nur Buchstaben, Zahlen und Bindestriche enthalten.

###Clustertyp

Über den Clustertyp können Sie spezielle Konfigurationen für den Cluster auswählen. Folgende Typen sind für Linux-basierte HDInsight-Cluster verfügbar:

| Clustertyp | Anforderung |
| ------------ | ----------------------------- |
| Hadoop | Abfragen und Analysen (Batchaufträge) |
| HBase | NoSQL-Datenspeicher |
| Storm | Ereignisverarbeitung in Echtzeit |
| Spark (Vorschau) | Arbeitsspeicherinterne Verarbeitung, interaktive Abfragen, Microbatch-Datenstromverarbeitung |

Sie können diesen grundlegenden Typen mithilfe von [Skriptaktionen](#scriptaction) weitere Technologien wie Hue oder R hinzufügen.

###Clusterbetriebssystem

Sie können HDInsight-Cluster unter einem der beiden folgenden Betriebssysteme bereitstellen:

- **HDInsight unter Windows (Windows Server 2012 R2 Datacenter)**: Wählen Sie diese Option, wenn eine Integration in Windows-basierte Dienste und Technologien erforderlich ist, die im Hadoop-Cluster ausgeführt werden, oder wenn Sie eine Migration von einer vorhandenen Windows-basierten Hadoop-Distribution durchführen.

- **HDInsight unter Linux (Ubuntu 12.04 LTS für Linux)**: Wählen Sie diese Lösung, wenn Sie mit Linux oder Unix vertraut sind, eine Migration von einer vorhandenen Linux-basierten Hadoop-Lösung durchführen oder wenn Sie eine einfache Integration mit Komponenten des Hadoop-Systems wünschen, die für Linux konzipiert wurden. Weitere Informationen finden Sie unter [Erste Schritte mit Hadoop unter Linux in HDInsight](hdinsight-hadoop-linux-get-started.md).

> [AZURE.NOTE] Bei den Informationen in diesem Artikel wird angenommen, dass Sie einen Linux-basierten HDInsight-Cluster verwenden. Spezifische Informationen für Windows-basierte Cluster finden Sie unter [Erstellen von Windows-basierten Hadoop-Clustern in HDInsight](hdinsight-provision-clusters.md).

###Abonnementname

Wenn Sie über mehrere Azure-Abonnements verfügen, wählen Sie mithilfe dieser Option das Abonnement aus, das Sie zur Erstellung des HDInsight-Clusters verwenden möchten.

###Ressourcengruppe

Anwendungen bestehen normalerweise aus vielen Komponenten, z. B. Web-App, Datenbank, Datenbankserver, Speicher und Drittanbieterdiensten. Mit dem Azure-Ressourcen-Manager (ARM) können Sie mit den Ressourcen in Ihrer Anwendung als Gruppe arbeiten, was als Azure-Ressourcengruppe bezeichnet wird. Sie können alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen, aktualisieren, überwachen oder löschen. Sie verwenden eine Vorlage für die Bereitstellung, die für unterschiedliche Umgebungen geeignet sein kann, z. B. Testing, Staging und Produktion. Sie können die Abrechnung für Ihre Organisation vereinfachen, indem Sie die zusammengefassten Kosten für die gesamte Gruppe anzeigen. Weitere Informationen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md).

###Anmeldeinformationen

Bei HDInsight-Clustern werden zwei Arten der Authentifizierung verwendet:

* __Administrator__- oder __HTTPs__-Benutzer: Das Administratorkonto für einen Cluster wird primär für den Zugriff auf Web- oder REST-Dienste verwendet, die vom Cluster offengelegt werden. Es kann nicht für die direkte Anmeldung beim Cluster verwendet werden.

* __SSH-Benutzername__: Ein SSH-Benutzerkonto wird für den Remotezugriff auf einen Cluster über einen [Secure Shell](https://en.wikipedia.org/wiki/Secure_Shell)-Client verwendet. Am häufigsten wird es verwendet, um befehlszeilengesteuerten Remotezugriff auf die Clusterhauptknoten bereitzustellen.

Das Administratorkonto ist kennwortgeschützt, und sämtliche über dieses Konto durchgeführte Webkommunikation mit dem Cluster sollte über eine sichere HTTPS-Verbindung erfolgen.

Der SSH-Benutzer kann mit einem Kennwort oder einem Zertifikat authentifiziert werden. Die Authentifizierung per Zertifikat ist die sicherste Option, erfordert jedoch die Erstellung und Wartung eines Paars aus einem öffentlichen und einem privaten Zertifikat.

Weitere Informationen zur Verwendung von SSH mit HDInsight, beispielsweise zur Erstellung und Verwendung von SSH-Schlüsseln finden Sie in den folgenden Artikeln:

* [Verwenden von SSH mit HDInsight auf Linux-, Unix- oder Mac OS X-Clients](hdinsight-hadoop-linux-use-ssh-unix.md)
* [Verwenden von SSH mit HDInsight auf Windows-Clients](hdinsight-hadoop-linux-use-ssh-windows.md)

###Datenquelle

HDInsight verwendet Azure-Blobspeicher als zugrunde liegenden Speicher für den Cluster. Intern wird dieser Speicher für Hadoop und andere Softwareanwendungen im Cluster als HDFS-kompatibles System (Hadoop Distributed File System) angezeigt.

Beim Erstellen eines neuen Clusters müssen Sie entweder ein neues Azure Storage-Konto erstellen oder ein vorhandenes verwenden.

> [AZURE.IMPORTANT] Der geografische Standort, den Sie für das Speicherkonto auswählen, bestimmt den Standort des HDInsight-Clusters, da der Cluster sich im gleichen Datencenter befinden muss wie das Standardspeicherkonto.
>
> Um eine Liste der unterstützten Regionen zu erhalten, klicken Sie in der Dropdownliste **Region** auf [HDInsight-Preise](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

####Standardspeichercontainer

HDInsight erstellt auch einen _Standardspeichercontainer_ im Speicherkonto. Dies ist der Standardspeicher für den HDInsight-Cluster.

Dieser Container weist standardmäßig denselben Namen auf wie der Cluster. Weitere Informationen zur Funktionsweise von HDInsight mit Azure-Blobspeichern finden Sie unter [Verwenden des HDFS-kompatiblen Azure-Blobspeichers mit Hadoop in HDInsight](hdinsight-hadoop-use-blob-storage.md).

>[AZURE.WARNING]Geben Sie keinen Container für mehrere Cluster frei. Dies wird nicht unterstützt.

###Knotengröße

Sie können die Größe der Computeressourcen auswählen, die vom Cluster genutzt werden sollen. Wenn Sie z. B. wissen, dass Sie Vorgänge mit hohem Arbeitsspeicherbedarf ausführen werden, sollten Sie eine Computeressource mit großem Arbeitsspeicher auswählen.

Unterschiedliche Clustertypen weisen verschiedene Knotentypen, eine unterschiedliche Anzahl von Knoten sowie verschiedene Knotengrößen auf. Ein Hadoop-Cluster beispielsweise besitzt zwei _Hauptknoten_ sowie standardmäßig vier _Datenknoten_. Ein Storm-Cluster dagegen weist zwei _Nimbusknoten_, drei _Zookeeperknoten_ und standardmäßig vier _Supervisorknoten_ auf.

> [AZURE.IMPORTANT] Wenn Sie mehr als 32 Workerknoten planen, entweder bei Erstellung des Clusters oder durch eine Skalierung des Clusters nach der Erstellung, müssen Sie eine Hauptknotengröße von mindestens 8 Kernen und 14 GB Arbeitsspeicher (RAM) auswählen.

Wenn Sie das Azure-Vorschauportal zum Konfigurieren des Clusters verwenden, ist die Knotengröße auf dem Blatt __Knotentarif__ verfügbar. Hier werden auch die Kosten für die verschiedenen Knotengrößen angezeigt.

> [AZURE.IMPORTANT] Die Abrechnung beginnt, sobald ein Cluster erstellt wurde, und endet erst, wenn der Cluster gelöscht wird. Weitere Informationen zu den Preisen finden Sie unter [HDInsight – Preise](https://azure.microsoft.com/pricing/details/hdinsight/).

##<a id="optionalconfiguration"></a>Optionale Konfiguration

In den folgenden Abschnitten werden optionale Konfigurationsoptionen sowie Szenarien beschrieben, in denen diese Konfigurationen erforderlich sind.

### HDInsight-Version

Verwenden Sie diese Option zum Ermitteln der HDInsight-Version, die für diesen Cluster verwendet wird. Weitere Informationen finden Sie unter [Hadoop-Clusterversionen und -komponenten in HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409).

### Verwenden virtueller Azure-Netzwerke

Ein [virtuelles Azure-Netzwerk](http://azure.microsoft.com/documentation/services/virtual-network/) ermöglicht das Erstellen eines sicheren, persistenten Netzwerks mit allen Ressourcen, die Sie für Ihre Lösung benötigen. Virtuelle Netzwerke ermöglichen Folgendes:

* Verbinden von Cloudressourcen in einem privaten Netzwerk (nur in der Cloud)

	![Diagramm der Nur-Cloud-Konfiguration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-cloud-only.png)

* Verbinden Ihrer Cloudressourcen mithilfe eines virtuellen privaten Netzwerks (VPN) mit dem Netzwerk in Ihrem lokalen Datencenter (Standort-zu-Standort oder Punkt-zu-Standort).

    | Site-to-Site-Konfiguration | Point-to-Site-Konfiguration |
    | -------------------------- | --------------------------- |
    | In einer Site-to-Site-Konfiguration können Sie mehrere Ressourcen aus Ihrem Datencenter über ein Hardware-VPN oder den Routing- und RAS-Dienst mit dem virtuellen Azure-Netzwerk verbinden.<br />![Diagramm der Standort-zu-Standort-Konfiguration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-site-to-site.png) | In einer Point-to-Site-Konfiguration können Sie eine bestimmte Ressource über ein Software-VPN mit dem virtuellen Azure-Netzwerk verbinden.<br />![Diagramm der Punkt-zu-Standort-Konfiguration](./media/hdinsight-hadoop-provision-linux-clusters/hdinsight-vnet-point-to-site.png) |

Weitere Informationen zu Features, Vorteilen und Funktionen von virtuellen Netzwerken finden Sie unter [Überblick über virtuelle Azure-Netzwerke](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [AZURE.NOTE] Sie müssen das virtuelle Azure-Netzwerk erstellen, bevor Sie einen Cluster erstellen. Weitere Informationen finden Sie unter [Erstellen eines virtuellen Netzwerks](virtual-networks-create-vnet.md).
>
> Azure HDInsight unterstützt nur standortbasierte virtuelle Netzwerke und kann momentan nicht mit affinitätsgruppenbasierten virtuellen Netzwerken verwendet werden. Verwenden Sie das Azure PowerShell-Cmdlet "Get-AzureVNetConfig", um zu prüfen, ob ein vorhandenes virtuelles Netzwerk in Azure standortbasiert ist. Wenn das virtuelle Netzwerk nicht standortbasiert ist, haben Sie die folgenden Optionen:
>
> - Exportieren Sie die vorhandene virtuelle Netzwerkkonfiguration, und erstellen Sie ein neues virtuelles Netzwerk. Alle neuen virtuellen Netzwerke sind standardmäßig standortbasiert.
> - Migrieren Sie zu einem standortbasierten virtuellen Netzwerk. Siehe [Migrieren vorhandener Dienste in einen Regionsbereich](http://azure.microsoft.com/blog/2014/11/26/migrating-existing-services-to-regional-scope/).
>
> Sie sollten unbedingt ein einziges Subnetz pro Cluster verwenden.
>
> Derzeit (25.8.2015) können Sie nur einen Linux-basierten Cluster in einem virtuellen Azure-Netzwerk bereitstellen.
>
> Sie können kein virtuelles Azure-Netzwerk der Version 1 (klassisch) mit Linux-basiertem HDInsight verwenden. Das virtuelle Netzwerk muss Version 2 aufweisen (Azure-Ressourcen-Manager), damit es beim Erstellen des Clusters im Azure-Vorschauportal als Option angezeigt wird bzw. beim Erstellen eines Clusters über die Azure-Befehlszeilenschnittstelle oder Azure PowerShell verwendet werden kann.
>
> Wenn Sie über Ressourcen in einem v1-Netzwerk verfügen und möchten, dass diese Ressourcen über ein virtuelles Netzwerk direkt auf HDInsight zugreifen können, finden Sie unter [Herstellen einer Verbindung zwischen klassischen VNets und neuen VNets](../virtual-network/virtual-networks-arm-asm-s2s.md) Informationen zum Verbinden eines virtuellen v2-Netzwerks mit einem virtuellen v1-Netzwerk. Nachdem diese Verbindung eingerichtet wurde, können Sie den HDInsight-Cluster im virtuellen v2-Netzwerk erstellen.

### Metastore

Metastore enthält Hive- und Oozie-Metadaten, wie z. B. Informationen zu Hive-Tabellen, -Partitionen, -Schemata und -Spalten. Mithilfe von Metastore können Sie Ihre Hive- und Oozie-Metadaten beibehalten, sodass Sie Hive-Tabellen oder Oozie-Aufträge nicht neu erstellen müssen, wenn Sie einen neuen Cluster erstellen.

Mit der Metastore-Konfigurationsoption können Sie einen externen Metastore angeben, der eine SQL-Datenbank verwendet. Auf diese Weise bleiben die Metadateninformationen erhalten, wenn Sie einen Cluster löschen, da diese extern in der Datenbank gespeichert sind. Informationen zum Erstellen einer SQL-Datenbank in Azure finden Sie unter [Erstellen einer ersten Azure SQL-Datenbank](sql-database-get-started.md).

> [AZURE.NOTE] Für HBase-Clustertypen ist keine Metastore-Konfiguration verfügbar.

###<a id="scriptaction"></a>Skriptaktion

Mithilfe von Skripts können Sie während der Bereitstellung zusätzliche Komponenten installieren oder die Clusterkonfiguration anpassen. Diese Skripts werden per **Skriptaktion** aufgerufen. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

> [AZURE.IMPORTANT] Das Hinzufügen zusätzlicher Komponenten nach der Erstellung eines Clusters wird nicht unterstützt, da diese Komponenten nach dem Reimaging eines Clusterknotens nicht verfügbar sind. Durch Skriptaktionen installierte Komponenten werden im Rahmen des Reimagingvorgangs neu installiert.

### Zusätzlicher Speicher

In manchen Fällen ist es wünschenswert, dem Cluster zusätzlichen Speicher hinzufügen zu können. Dies kann beispielsweise dann der Fall sein, wenn Sie über mehrere Azure Storage-Konten in verschiedenen geografischen Regionen oder für verschiedene Dienste verfügen und all diese Konten mit HDInsight analysieren möchten.

Weitere Informationen zu sekundären Blobspeichern finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](../hdinsight-use-blob-storage.md).

##<a id="nextsteps"></a><a id="options"></a> Erstellungsmethoden

In diesem Artikel haben Sie grundlegende Informationen zum Erstellen eines Linux-basierten HDInsight-Clusters erhalten. In der folgenden Tabelle finden Sie spezifische Informationen zum Erstellen von Clustern mit der Methode, die Ihren Anforderungen am besten entspricht:

| Clustererstellung | Webbrowser | Befehlszeile | REST-API | SDK | Linux, Mac OS X oder Unix | Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Azure-Vorschauportal](hdinsight-hadoop-create-linux-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure-Befehlszeilenschnittstelle](hdinsight-hadoop-create-linux-clusters-azure-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [.NET SDK](hdinsight-hadoop-create-linux-clusters-dotnet.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |



[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-sdk-documentation]: http://msdn.microsoft.com/library/dn479185.aspx


[hdinsight-customize-cluster]: ../hdinsight-hadoop-customize-cluster/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/

[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx

[azure-management-portal]: https://manage.windowsazure.com/

[azure-command-line-tools]: ../xplat-cli/
[azure-create-storageaccount]: ../storage-create-storage-account/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[hdi-remote]: http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp


[Powershell-install-configure]: ../install-configure-powershell/

[image-hdi-customcreatecluster]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.png
[image-hdi-customcreatecluster-clusteruser]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.ClusterUser.png
[image-hdi-customcreatecluster-storageaccount]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.StorageAccount.png
[image-hdi-customcreatecluster-addonstorage]: ./media/hdinsight-get-started/HDI.CustomCreateCluster.AddOnStorage.png

[azure-preview-portal]: https://portal.azure.com


[image-cli-account-download-import]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIAccountDownloadImport.png
[image-cli-clustercreation]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreation.png
[image-cli-clustercreation-config]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIClusterCreationConfig.png
[image-cli-clusterlisting]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.CLIListClusters.png "Auflisten und Anzeigen von Clustern"

[image-hdi-ps-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.provision.png
[image-hdi-ps-config-provision]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.ps.config.provision.png

[img-hdi-cluster]: ./media/hdinsight-hadoop-provision-linux-clusters/HDI.Cluster.png

  [89e2276a]: /documentation/articles/hdinsight-use-sqoop/ "Verwenden von Sqoop mit HDInsight"

<!---HONumber=AcomDC_0107_2016-->