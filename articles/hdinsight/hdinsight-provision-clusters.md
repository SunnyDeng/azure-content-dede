<properties
   pageTitle="Erstellen Windows-basierter Hadoop-Cluster in HDInsight | Microsoft Azure"
   	description="Erfahren Sie, wie Sie Cluster für Azure HDInsight erstellen."
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
   ms.date="02/12/2016"
   ms.author="jgao"/>

# Erstellen Windows-basierter Hadoop-Cluster in HDInsight

[AZURE.INCLUDE [Auswahl](../../includes/hdinsight-selector-create-clusters.md)]

Sie erhalten Informationen zur Planung der Erstellung von HDInsight-Clustern.

###Voraussetzungen:

Bevor Sie die Anweisungen in diesem Artikel ausführen können, benötigen Sie Folgendes:

- Ein Azure-Abonnement. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).


## Grundlegende Konfigurationsoptionen

Folgende sind die grundlegenden Konfigurationsoptionen zum Erstellen eines HDInsight-Clusters.

- **Clustername**

	Der Clustername wird verwendet, um einen Cluster zu identifizieren. Für den Clusternamen gelten die folgenden Richtlinien:

	- Das Feld muss eine Zeichenfolge mit 3 bis 63 Zeichen enthalten.
	- Das Feld darf nur Buchstaben, Zahlen und Bindestriche enthalten.

- **Abonnementname**

	Ein HDInsight-Cluster ist an ein Azure-Abonnement gebunden.

- **Ressourcengruppenname**

	Mit dem Azure-Ressourcen-Manager (ARM) können Sie mit den Ressourcen in Ihrer Anwendung als Gruppe arbeiten, was als Azure-Ressourcengruppe bezeichnet wird. Sie können alle Ressourcen für Ihre Anwendung in einem einzigen, koordinierten Vorgang bereitstellen, aktualisieren, überwachen oder löschen. Weitere Informationen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md).
	
- **Betriebssystem**

	Sie können HDInsight-Cluster unter einem der folgenden beiden Betriebssysteme erstellen: - **HDInsight für Windows (Windows Server 2012 R2 Datacenter)**: - **HDInsight unter Linux (Ubuntu 12.04 LTS für Linux)**: HDInsight bietet die Möglichkeit, Linux-Cluster in Azure zu konfigurieren. Konfigurieren Sie einen Linux-Cluster, wenn Sie mit Linux oder Unix und der Migration von einer vorhandenen Linux-basierten Hadoop-Lösung vertraut sind, oder Sie eine einfache Integration mit Komponenten des Hadoop-Systems wünschen, die für Linux konzipiert sind. Weitere Informationen finden Sie unter [Erste Schritte mit Hadoop unter Linux in HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

- **Clustertyp** und **Clustergröße (auch Datenknoten)**

	Mit HDInsight können Kunden verschiedene Clustertypen für unterschiedliche Datenanalyseworkloads bereitstellen. Aktuell angebotene Clustertypen sind:

	- Hadoop-Cluster: für Abfrage- und Analyseworkloads
	- HBase-Cluster: für NoSQL-Workloads
	- Storm-Cluster: für Workloads für die Ereignisverarbeitung in Echtzeit
	- Spark-Cluster: für die Verarbeitung im Arbeitsspeicher, interaktive Abfragen, Streaming und Machine Learning-Workloads.

	![HDInsight-Cluster](./media/hdinsight-provision-clusters/hdinsight.clusters.png)

	> [AZURE.NOTE] *Azure HDInsight-Cluster* werden auch als *Hadoop-Cluster in HDInsight* oder *HDInsight-Cluster* bezeichnet. Mitunter werden diese Bezeichnungen synonym mit *Hadoop-Cluster* verwendet. Alle beziehen sich auf Hadoop-Cluster, die in der Microsoft Azure-Umgebung gehostet werden.

	Innerhalb eines bestimmten Clustertyps übernehmen die verschiedenen Knoten unterschiedliche Rollen, die es Kunden ermöglichen, die Knoten in einer bestimmten Rolle entsprechend ihres jeweiligen Workloads zu dimensionieren. Beispielsweise können bei einem Hadoop-Cluster die Workerknoten mit großem Arbeitsspeicher erstellt werden, wenn die durchzuführenden Analysen speicherintensiv sind.

	![HDInsight Hadoop-Clusterrollen](./media/hdinsight-provision-clusters/HDInsight.Hadoop.roles.png)

	Hadoop-Cluster für HDInsight werden mit zwei Rollen bereitgestellt:

	- Hauptknoten (2 Knoten)
	- Datenknoten (mindestens 1 Knoten)

	![HDInsight Hadoop-Clusterrollen](./media/hdinsight-provision-clusters/HDInsight.HBase.roles.png)

	HBase-Cluster für HDInsight werden mit drei Rollen bereitgestellt: – Hauptserver (2 Knoten) – Regionsserver (mindestens 1 Knoten) – Master-/Zookeeper-Knoten (3 Knoten)

	![HDInsight Hadoop-Clusterrollen](./media/hdinsight-provision-clusters/HDInsight.Storm.roles.png)

	Storm-Cluster für HDInsight werden mit drei Rollen bereitgestellt: – Nimbus-Knoten (2 Knoten) – Supervisor-Server (mindestens 1 Knoten) – Zookeeper-Knoten (3 Knoten)


	![HDInsight Hadoop-Clusterrollen](./media/hdinsight-provision-clusters/HDInsight.Spark.roles.png)

	Spark-Cluster für HDInsight werden mit drei Rollen bereitgestellt: – Hauptknoten (2 Knoten) – Workerknoten (mindestens 1 Knoten) – Zookeeper-Knoten (3 Knoten) (für Zookeeper vom Typ "A1" kostenlos)

	Kunden wird die Nutzung dieser Knoten für die Laufzeit des Clusters in Rechnung gestellt. Die Abrechnung beginnt, sobald ein Cluster erstellt wurde, und endet, wenn der Cluster gelöscht wird (bei Clustern ist kein Aufheben der Zuweisung oder Anhalten möglich). Die Clustergröße wirkt sich auf die Clusterkosten aus. Zu Lernzwecken wird empfohlen, einen Datenknoten zu verwenden. Weitere Informationen zu den Preisen von HDInsight finden Sie unter [HDInsight – Preise](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).


	>[AZURE.NOTE] Die Begrenzung der Clustergröße variiert je nach Azure-Abonnement. Wenden Sie sich an den Azure-Abrechnungssupport, um diese Begrenzung zu erhöhen.

- **HDInsight-Version**

	Dient zum Ermitteln der Version von HDInsight, die für diesen Cluster verwendet werden soll. Weitere Informationen finden Sie unter [Hadoop-Clusterversionen und -komponenten in HDInsight](https://go.microsoft.com/fwLink/?LinkID=320896&clcid=0x409).


- **Standort (Region)**

	Der HDInsight-Cluster und das zugeordnete Standardspeicherkonto müssen sich am selben Azure-Standort befinden.
	
	![Azure-Regionen](./media/hdinsight-provision-clusters/Azure.regions.png)

	Um eine Liste der unterstützten Regionen zu erhalten, klicken Sie in der Dropdownliste **Region** auf [HDInsight-Preise](https://go.microsoft.com/fwLink/?LinkID=282635&clcid=0x409).

- **Knotengröße**

	![VM-Größen für HDInsight-Knoten](./media/hdinsight-provision-clusters/hdinsight.node.sizes.png)

	Wählen Sie die VM-Größe für die Knoten aus. Weitere Informationen finden Sie unter [Größen für Clouddienste](cloud-services-sizes-specs.md). Sie können die Größe der Computeressourcen auswählen, die vom Cluster genutzt werden sollen. Wenn Sie z. B. wissen, dass Sie Vorgänge mit hohem Arbeitsspeicherbedarf ausführen werden, sollten Sie eine Computeressource mit großem Arbeitsspeicher auswählen.

	>[AZURE.NOTE] Die von Ihrem Cluster verwendeten Knoten zählen nicht als virtuelle Computer, da die VM-Images, die für die Knoten verwendet werden, ein Implementierungsdetail des HDInsight-Diensts sind. Die von den Knoten verwendeten Computekerne zählen jedoch zur Gesamtanzahl von Computekernen, die für Ihr Abonnement verfügbar sind. Sie können die Anzahl der Kerne, die vom Cluster verwendet werden sollen, sowie die Anzahl der verfügbaren Kerne beim Erstellen eines HDInsight-Clusters auf dem Blatt „Knotenpreisstufen“ im Abschnitt „Zusammenfassung“ anzeigen.

	Unterschiedliche Clustertypen weisen verschiedene Knotentypen, eine unterschiedliche Anzahl von Knoten sowie verschiedene Knotengrößen auf. Ein Hadoop-Cluster beispielsweise besitzt zwei _Hauptknoten_ sowie standardmäßig vier _Datenknoten_. Ein Storm-Cluster dagegen weist zwei _Nimbusknoten_, drei _Zookeeperknoten_ und standardmäßig vier _Supervisorknoten_ auf.

	> [AZURE.IMPORTANT] Wenn Sie mehr als 32 Workerknoten planen – entweder bei Erstellung des Clusters oder durch Skalierung des Clusters nach der Erstellung – müssen Sie eine Hauptknotengröße von mindestens 8 Kernen und 14 GB RAM auswählen.

	Wenn Sie das Azure-Portal zum Konfigurieren des Clusters verwenden, ist die Knotengröße auf dem Blatt __Knotentarif__ verfügbar. Hier werden auch die Kosten für die verschiedenen Knotengrößen angezeigt.

	> [AZURE.IMPORTANT] Die Abrechnung beginnt, sobald ein Cluster erstellt wurde, und endet erst, wenn der Cluster gelöscht wird. Weitere Informationen zu den Preisen finden Sie unter [HDInsight – Preise](https://azure.microsoft.com/pricing/details/hdinsight/).


- **HDInsight-Benutzer**

	Die HDInsight-Cluster ermöglichen Ihnen, während der Bereitstellung zwei Benutzerkonten zu konfigurieren:

	- HTTP-Benutzer. Der Standardbenutzername bei der grundlegenden Konfiguration im Azure-Portal ist „admin“.
	- RDP-Benutzer (Windows-Cluster): Dient zum Herstellen einer Verbindung mit dem Cluster über RDP. Wenn Sie das Konto erstellen, müssen Sie ein Ablaufdatum festlegen, das maximal 90 Tage hinter dem aktuellen Datum liegt.
	- SSH-Benutzer (Linux-Cluster): Dient zum Herstellen einer Verbindung mit dem Cluster über SSH. Sie können zusätzliche SSH-Benutzerkonten erstellen, nachdem der Cluster erstellt wurde, indem Sie die Schritte unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md) ausführen.



- **Azure-Speicherkonto**

	Das ursprüngliche HDFS nutzt viele lokale Datenträger im Cluster. HDInsight verwendet zur Datenspeicherung stattdessen Azure-Blobspeicher. Azure-Blobspeicher stellt eine robuste, universelle Speicherlösung dar, die problemlos mit HDInsight integriert werden kann. Über eine HDFS-Schnittstelle (Hadoop Distributed File System) können sämtliche Komponenten in HDInsight direkt mit strukturierten oder unstrukturierten Daten im Blobspeicher arbeiten. Die Speicherung von Daten im Blobspeicher sorgt dafür, dass die HDInsight-Cluster, die für Berechnungen verwendet werden, sicher gelöscht werden können, ohne Benutzerdaten zu verlieren.

	Bei der Konfiguration müssen Sie ein Azure-Speicherkonto und einen Azure-Blobspeichercontainer für dieses Konto angeben. Bei einigen Erstellungsprozessen müssen das Azure-Speicherkonto und der Blobspeichercontainer vorher erstellt werden. Der Blobspeichercontainer wird vom Cluster als Standardspeicherort verwendet. Optional können Sie zusätzliche Azure-Speicherkonten (verknüpften Speicher) angeben, auf die der Cluster zugreifen kann. Darüber hinaus kann der Cluster auch auf alle Blobcontainer zugreifen, die mit vollständig öffentlichem Lesezugriff oder nur für Blobs öffentlichem Lesezugriff konfiguriert sind. Weitere Informationen zum Einschränken des Zugriffs finden Sie unter [Verwalten des Zugriffs auf Azure-Speicherressourcen](storage-manage-access-to-resources.md).

	![HDInsight-Speicher](./media/hdinsight-provision-clusters/HDInsight.storage.png)

	>[AZURE.NOTE] Ein Blobspeichercontainer stellt eine Gruppierung von Blobs bereit (siehe die folgende Abbildung):

	![Azure-Blobspeicher](./media/hdinsight-provision-clusters/Azure.blob.storage.jpg)


	>[AZURE.WARNING] Geben Sie keinen Blob-Speichercontainer für mehrere Cluster frei. Dies wird nicht unterstützt.

	Weitere Informationen zu sekundären Blobspeichern finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](hdinsight-hadoop-use-blob-storage.md).

- **Hive/Oozie-Metastore**

	Metastore enthält Hive- und Oozie-Metadaten, wie z. B. Hive-Tabellen, Partitionen, Schemata und Spalten. Mithilfe von Metastore können Sie Ihre Hive- und Oozie-Metadaten beibehalten, damit Sie nicht Hive-Tabellen oder Oozie-Aufträge neu erstellen müssen, wenn Sie einen neuen Cluster erstellen. Standardmäßig speichert Hive diese Informationen in einer eingebetteten Azure SQL-Datenbank. Die eingebettete Datenbank kann die Metadaten nicht beibehalten, wenn der Cluster gelöscht wird. Angenommen, Sie haben einen Cluster mit einem Hive-Metastore erstellt. Sie haben einige Hive-Tabellen erstellt. Nachdem Sie den Cluster gelöscht und mit demselben Hive-Metastore wiederhergestellt haben, werden dieselben Tabellen angezeigt, die Sie im ursprünglichen Cluster erstellt haben.
    
    > [AZURE.NOTE] Für HBase-Clustertypen ist keine Metastore-Konfiguration verfügbar.

## Anpassen von Clustern mithilfe der HDInsight-Clusteranpassung (Bootstrap)

Mitunter möchten Sie die Konfigurationsdateien bearbeiten:

- core-site.xml
- hdfs-site.xml
- mapred-site.xml
- yarn-site.xml
- hive-site.xml
- oozie-site.xml

Die Cluster können die Änderungen aufgrund des Re-Imagings nicht beibehalten. Weitere Informationen finden Sie unter [Role Instance Restarts Due to OS Upgrades](http://blogs.msdn.com/b/kwill/archive/2012/09/19/role-instance-restarts-due-to-os-upgrades.aspx) (Neustart von Rolleninstanzen aufgrund von Betriebssystemupdates – in englischer Sprache). Um die Änderungen für die Laufzeit des Clusters beizubehalten, können Sie während des Erstellungsprozesses die HDInsight-Clusteranpassung nutzen. Dies ist die empfohlene Methode zum Ändern der Konfiguration eines Clusters, die über die Neustartereignisse des Azure-Reimaging hinweg beibehalten werden. Diese Konfigurationsänderungen werden vor dem Starten des Diensts angewendet, sodass Dienste nicht neu gestartet werden müssen.

Ein Beispiel finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Bootstrap](hdinsight-hadoop-customize-cluster-bootstrap.md).

## Anpassen von Clustern mithilfe von Skriptaktionen

Sie können zusätzliche Komponenten installieren oder die Clusterkonfiguration mithilfe von Skripts während der Erstellung anpassen. Diese Skripts werden mithilfe der Konfigurationsoption **Skriptaktion** aufgerufen, die vom Portal, von Windows PowerShell-Cmdlets für HDInsight oder dem .NET-SDK für HDInsight verwendet werden kann. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster.md).


## Verwenden virtueller Azure-Netzwerke

[Azure Virtual Network](https://azure.microsoft.com/documentation/services/virtual-network/) ermöglicht das Erstellen eines sicheren, beständigen Netzwerk mit allen Ressourcen, die Sie für Ihre Lösung benötigen. Virtuelle Netzwerken ermöglichen Folgendes:

* Verbinden von Cloudressourcen in einem privaten Netzwerk (nur in der Cloud)

	![Diagramm der Nur-Cloud-Konfiguration](./media/hdinsight-provision-clusters/hdinsight-vnet-cloud-only.png)

* Verbinden Ihrer Cloudressourcen mithilfe eines virtuellen privaten Netzwerks (VPN) mit dem Netzwerk in Ihrem lokalen Datencenter (Standort-zu-Standort oder Punkt-zu-Standort).

	In einer Standort-zu-Standort-Konfiguration können Sie mehrere Ressourcen aus Ihrem Rechenzentrum über ein Hardware-VPN oder den Routing- und RAS-Dienst mit dem virtuellen Azure-Netzwerk verbinden.

	![Diagramm der Standort-zu-Standort-Konfiguration](./media/hdinsight-provision-clusters/hdinsight-vnet-site-to-site.png)

	In einer Punkt-zu-Standort-Konfiguration können Sie eine bestimmte Ressource über ein Software-VPN mit dem virtuellen Azure-Netzwerk verbinden.

	![Diagramm der Punkt-zu-Standort-Konfiguration](./media/hdinsight-provision-clusters/hdinsight-vnet-point-to-site.png)

Informationen zur Verwendung von HDInsight mit einem virtuellen Netzwerk, einschließlich spezifischer Konfigurationsanforderungen für das virtuelle Netzwerk, finden Sie unter [Erweitern der HDInsight-Funktionen mit Azure Virtual Network](hdinsight-extend-hadoop-virtual-network.md).

## Methoden zur Clustererstellung

In diesem Artikel haben Sie grundlegende Informationen zum Erstellen eines Windows-basierten HDInsight-Clusters erhalten. In der folgenden Tabelle finden Sie spezifische Informationen zum Erstellen von Clustern mit der Methode, die Ihren Anforderungen am besten entspricht:

| Clustererstellung | Webbrowser | Befehlszeile | REST-API | SDK | Linux, Mac OS X oder Unix | Windows |
| ------------------------------- |:----------------------:|:--------------------:|:------------------:|:------------:|:-----------------------------:|:------------:|
| [Azure-Portal](hdinsight-hadoop-create-windows-clusters-portal.md) | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure-Befehlszeilenschnittstelle](hdinsight-hadoop-create-windows-clusters-cli.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |
| [Azure PowerShell](hdinsight-hadoop-create-windows-clusters-powershell.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | &nbsp; | ✔ |
| [cURL](hdinsight-hadoop-create-linux-clusters-curl-rest.md) | &nbsp; | ✔ | ✔ | &nbsp; | ✔ | ✔ |
| [.NET SDK](hdinsight-hadoop-create-windows-clusters-dotnet-sdk.md) | &nbsp; | &nbsp; | &nbsp; | ✔ | ✔ | ✔ |
| [ARM-Vorlagen](hdinsight-hadoop-create-windows-clusters-arm-templates.md) | &nbsp; | ✔ | &nbsp; | &nbsp; | ✔ | ✔ |

<!---HONumber=AcomDC_0224_2016-->