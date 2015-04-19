<properties
   pageTitle="Erweitern von HDInsight mit Azure Virtual Network | VNet" metaKeywords="virtual network, vnet, azure, hdinsight" description="Erfahren Sie, wie Sie HDInsight mithilfe von Azure Virtual Network mit anderen Cloud-Ressourcen oder Ressourcen in Ihrem Rechenzentrum verbinden."
   services="hdinsight"
   documentationCenter=""
   authors="blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/2/2015"
   ms.author="larryfr"/>


# Erweitern der HDInsight-Funktionen mit Azure Virtual Network

Mit Azure Virtual Network können Sie Ihre Hadoop-Lösungen erweitern, um sie auf lokalen Ressourcen wie SQL Server zu integrieren oder um sichere private Netzwerke zwischen Ressourcen in der Cloud zu erstellen.

> [AZURE.NOTE] HDInsight unterstützt derzeit kein affinitätsbasiertes Azure Virtual Network. Bei der Verwendung von HDInsight müssen Sie standortbasierte virtuelle Netzwerke verwenden.

## <a id="whatis"></a>Was ist Azure Virtual Network?

[Virtual Azure Network](/documentation/services/virtual-network/) bietet ein sicheres, persistentes Netzwerk mit allen Ressourcen, die Sie für Ihre Lösung benötigen. Virtuelle Netzwerke ermöglichen Folgendes:

* Verbinden von Cloud-Ressourcen in einem privaten Netzwerk (Nur-Cloud)

	![diagram of cloud-only configuration](.\media\hdinsight-provision-clusters\cloud-only.png)

	Die Verwendung von Azure Virtual Network zum Verknüpfen von Azure Services mit HDInsight ermöglicht die folgenden Szenarien.

	* **Aufrufen von HDInsight-Diensten oder -Aufträgen** über Azure-Websites oder Dienste, die auf virtuellen Azure-Computern ausgeführt werden.

	* **Direkte Übertragung von Daten** zwischen HDInsight und einer SQL-Datenbank, SQL Server oder einer anderen Datenspeicherlösung, die auf einem virtuellen Computer ausgeführt wird.

	* **Kombination mehrerer HDInsight-Server** zu einer einzelnen Lösung. Beispielsweise die Verwendung eines HDInsight Storm-Servers zum Verarbeiten eingehender Daten mit anschließender Speicherung der verarbeiteten Daten auf einem HDInsight HBase-Server. Die Rohdaten können auch für eine zukünftige Analyse mit MapReduce auf einem HDInsight Hadoop-Server gespeichert werden.

* Verbinden Ihrer Cloud-Ressourcen mit dem Netzwerk in Ihrem lokalen Rechenzentrum (Standort-zu-Standort oder Punkt-zu-Standort) mithilfe eines virtuellen privaten Netzwerks (VPN)

	Mit einer Standort-zu-Standort-Konfiguration können Sie mehrere Ressourcen aus Ihrem Rechenzentrum mit dem virtuellen Azure-Netzwerk über ein Hardware-VPN oder den Routing- und RAS-Dienst verbinden

	![diagram of site-to-site configuration](.\media\hdinsight-provision-clusters\site-to-site.png)

	Mit einer Punkt-zu-Standort-Konfiguration können Sie eine bestimmte Ressource über ein Software-VPN mit dem virtuellen Azure-Netzwerk verbinden

	![diagram of point-to-site configuration](.\media\hdinsight-provision-clusters\point-to-site.png)

	Die Verknüpfung von Cloud und Rechenzentrum mithilfe des virtuellen Netzwerks ermöglicht ähnliche Szenarien für die Nur-Cloud-Konfiguration, aber anstatt die Arbeit auf Ressourcen in der Cloud einzuschränken, können Sie auch mit Ressourcen in Ihrem Rechenzentrum arbeiten.

	* **Direkte Übertragung von Daten** zwischen HDInsight und Ihrem Rechenzentrum. Beispielsweise die Übertragung von Daten mithilfe von Sqoop zu oder von SQL Server oder das Lesen von Daten, die von einer Branchenanwendung generiert wurden.

	* **Aufrufen von HDInsight-Diensten oder -Aufträgen** aus einer Branchenanwendung. Beispielsweise die Verwendung von HBase Java-APIs zum Speichern und Abrufen von Daten aus einem HDInsight HBase-Cluster.

Weitere Informationen zu Features, Vorteilen und Funktionen von virtuellen Netzwerken finden Sie unter [Virtuelle Netzwerke im Überblick](http://msdn.microsoft.com/library/azure/jj156007.aspx).

> [WACOM.NOTE] Sie müssen das virtuelle Azure-Netzwerk erstellen, bevor Sie einen HDInsight-Cluster bereitstellen. Weitere Informationen finden Sie unter [Aufgaben bei der Konfiguration virtueller Netzwerke](http://msdn.microsoft.com/library/azure/jj156206.aspx).
>
> Azure HDInsight unterstützt nur standortbasierte virtuelle Netzwerke und kann momentan nicht mit affinitätsgruppenbasierten virtuellen Netzwerken verwendet werden.
>
> Sie sollten unbedingt ein einziges Subnetz pro Cluster verwenden.

Weitere Informationen zur Bereitstellung eines HDInsight-Clusters in einem virtuellen Netzwerk finden Sie unter [Bereitstellen von Hadoop-Clustern in HDInsight](hdinsight-provision-clusters.md).

## <a id="tasks"></a>Aufgaben und Informationen

Dieser Abschnitt enthält Informationen zu allgemeinen Aufgaben und Informationen, die möglicherweise bei der Verwendung von HDInsight mit einem virtuellen Netzwerk erforderlich sind.

### Ermitteln des vollqualifizierten Domänennamens

Der HDInsight-Cluster wird einen bestimmten FQDN für die virtuelle Netzwerkschnittstelle zugewiesen. Hierbei handelt es sich um die Adresse, die Sie von anderen Ressourcen im virtuellen Netzwerk aus beim Herstellen der Verbindung zum Cluster verwenden sollten. Verwenden Sie die folgende URL zum Abfragen des Ambari-Verwaltungsdiensts, um den FQDN zu ermitteln.

	https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/<servicename>/components/<componentname>

> [AZURE.NOTE] Weitere Informationen zur Verwendung von Ambari mit HDInsight finden Sie unter [Überwachen von Hadoop-Clustern in HDInsight mit der Ambari API](hdinsight-monitor-use-ambari-api.md).

Sie müssen den Clusternamen und einen Dienst sowie eine Komponente angeben, die auf dem Cluster ausgeführt werden, z. B. den YARN Resource Manager.

> [AZURE.NOTE] Die Daten werden als JSON-Dokument zurückgegeben, das viele Informationen zur Komponente enthält. Um nur den FQDN zu extrahieren, verwenden Sie einen JSON-Parser zum Abrufen des  `host_components[0].HostRoles.host_name`-Werts.

Wenn Sie z. B. den FQDN von einem HDInsight Hadoop-Cluster zurückgegeben möchten, können Sie die Daten für den YARN Resource Manager mithilfe einer der folgenden Methoden abrufen.

* [Azure PowerShell](install-configure-powershell.md)

		$ClusterDnsName = <clustername>
		$Username = <cluster admin username>
		$Password = <cluster admin password>
		$DnsSuffix = ".azurehdinsight.net"
		$ClusterFQDN = $ClusterDnsName + $DnsSuffix
		
		$webclient = new-object System.Net.WebClient
		$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)
		
		$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/services/yarn/		components/resourcemanager"
		$Response = $webclient.DownloadString($Url)
		$JsonObject = $Response | ConvertFrom-Json
		$FQDN = $JsonObject.host_components[0].HostRoles.host_name
		Write-host $FQDN

* [Curl](http://curl.haxx.se/) und [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/services/yarn/components/resourcemanager | jq .host_components[0].HostRoles.host_name

### Verbinden mit HBase

Damit Sie mithilfe der Java-API eine Remoteverbindung zu HBase herstellen können, müssen Sie die Zookeeper-Quorumadressen für den HBase-Cluster ermitteln und diese in Ihrer Anwendung angeben.

Verwenden Sie eine der folgenden Methoden zum Abfragen des Ambari-Verwaltungsdiensts, um die Zookeeper-Quorumadresse abzurufen.

* [Azure PowerShell](install-configure-powershell.md)

		$ClusterDnsName = <clustername>
		$Username = <cluster admin username>
		$Password = <cluster admin password>
		$DnsSuffix = ".azurehdinsight.net"
		$ClusterFQDN = $ClusterDnsName + $DnsSuffix

		$webclient = new-object System.Net.WebClient
		$webclient.Credentials = new-object System.Net.NetworkCredential($Username, $Password)

		$Url = "https://" + $ClusterFQDN + "/ambari/api/v1/clusters/" + $ClusterFQDN + "/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum"
        $Response = $webclient.DownloadString($Url)
        $JsonObject = $Response | ConvertFrom-Json
        Write-host $JsonObject.items[0].properties.'hbase.zookeeper.quorum'

* [Curl](http://curl.haxx.se/) und [jq](http://stedolan.github.io/jq/)

		curl -G -u <username>:<password> "https://<clustername>.azurehdinsight.net/ambari/api/v1/clusters/<clustername>.azurehdinsight.net/configurations?type=hbase-site&tag=default&fields=items/properties/hbase.zookeeper.quorum" | jq .items[0].properties[]

> [AZURE.NOTE] Weitere Informationen zur Verwendung von Ambari mit HDInsight finden Sie unter [Überwachen von Hadoop-Clustern in HDInsight mit der Ambari API](hdinsight-monitor-use-ambari-api.md).

Nachdem die Quoruminformationen vorliegen, verwenden Sie diese in der Clientanwendung.

Beispielsweise eine Java-Anwendung, die die HBase-API verwendet. Sie würden die Datei **hbase-site.xml** zum Projekt hinzufügen und die Quoruminformationen wie folgt in der Datei angeben.

```
<configuration>
  <property>
    <name>hbase.cluster.distributed</name>
    <value>true</value>
  </property>
  <property>
    <name>hbase.zookeeper.quorum</name>
    <value>zookeeper0.address,zookeeper1.address,zookeeper2.address</value>
  </property>
  <property>
    <name>hbase.zookeeper.property.clientPort</name>
    <value>2181</value>
  </property>
</configuration>
```

### Überprüfen der Netzwerkkonnektivität

Einige Dienste wie SQL Server können eingehende Netzwerkverbindungen begrenzen. Dadurch wird verhindert, dass HDInsight mit diesen Diensten erfolgreich arbeiten kann.

Wenn beim Zugriff auf einen Dienst von HDInsight Probleme auftreten, ziehen Sie die zum Dienst gehörende Dokumentation zu Rate, um sicherzustellen, dass Sie den Netzwerkzugriff aktiviert haben. Sie können den Netzwerkzugriff auch dadurch überprüfen, dass Sie einen virtuellen Azure-Computer in demselben virtuellen Netzwerk erstellen und Clientdienstprogramme zur Überprüfung verwenden, dass der virtuelle Computer über das virtuelle Netzwerk eine Verbindung zum Dienst herstellen kann.

## <a id="nextsteps"></a>Nächste Schritte

In den folgenden Beispielen wird die Verwendung von HDInsight mit Azure Virtual Network veranschaulicht.

* [Analysieren von Sensordaten mit Storm und HBase in HDInsight](hdinsight-storm-sensor-data-analysis.md) - Veranschaulicht die Konfiguration eines Storm- und HBase-Clusters in einem virtuellen Netzwerk sowie das Remote-Schreiben von Daten von Storm zu HBase.

* [Bereitstellen von HBase-Clustern in Azure Virtual Network](hdinsight-hbase-provision-vnet.md) - Enthält Informationen zum Bereitstellen eines HBase-Clustern in Azure Virtual Network.

* [Bereitstellen von Hadoop-Clustern in HDInsight](hdinsight-provision-clusters.md) - Bietet Informationen zur Bereitstellung von Hadoop-Cluster, einschließlich der Informationen zur Verwendung von Azure Virtual Network.

* [Verwenden von Sqoop mit Hadoop in HDInsight](hdinsight-use-sqoop.md)  - Bietet Informationen zur Verwendung von Sqoop zum Übertragen von Daten mit SQL Server über ein virtuelles Netzwerk.

Weitere Informationen zu Azure Virtual Network finden Sie unter [Überblick über Azure Virtual Network](http://msdn.microsoft.com/library/azure/jj156007.aspx).

<!--HONumber=47-->
