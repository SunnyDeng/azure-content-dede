<properties
	pageTitle="Funktionen für hohe Verfügbarkeit bei Linux-basiertem HDInsight (Hadoop) | Microsoft Azure"
	description="Erfahren Sie, wie Linux-basierte HDInsight-Cluster durch die Verwendung eines zusätzlichen Hauptknotens eine höhere Zuverlässigkeit und Verfügbarkeit bieten. Sie erfahren, wie dies Hadoop-Dienste wie Ambari und Hive beeinflusst und wie eine Verbindung mit jedem Hauptknoten über SSH hergestellt wird."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="Blackmist"
	documentationCenter=""
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="11/03/2015"
	ms.author="larryfr"/>

#Verfügbarkeit und Zuverlässigkeit von Hadoop-Clustern in HDInsight

Von Azure HDInsight bereitgestellte Linux-basierte Hadoop-Cluster verfügen über einen zweiten Hauptknoten. Dies verbessert die Verfügbarkeit und Zuverlässigkeit von Hadoop-Diensten und -Aufträgen, die in Azure ausgeführt werden.

> [AZURE.NOTE]Die Schritte in diesem Dokument gelten für Linux-basierte HDInsight-Cluster. Wenn Sie einen Windows-basierten Cluster verwenden, finden Sie unter [Verfügbarkeit und Zuverlässigkeit von Windows-basierten Hadoop-Clustern in HDInsight](hdinsight-high-availability.md) entsprechende Informationen für Windows.

##Grundlegendes zu Hauptknoten

In einigen Implementierungen von Hadoop hostet ein einzelner Hauptknoten Dienste und Komponenten, die zur reibungslosen Verwaltung ausgefallener Datenknoten (Workerknoten) dienen. Bei einem Ausfall eines Masterdiensts auf dem Hauptknoten würde jedoch der komplette Cluster ausfallen.

HDInsight-Cluster verfügen über einen sekundären Hauptknoten, sodass Masterdienste und -komponenten bei einem Ausfall des primären Knotens weiterhin auf dem sekundären Knoten ausgeführt werden können.

> [AZURE.IMPORTANT]Beide Hauptknoten sind aktiv und werden gleichzeitig innerhalb des Clusters ausgeführt. Einige Dienste (z. B. HDFS oder YARN) sind jeweils nur auf einem Hauptknoten „aktiv“ (und auf dem anderen Hauptknoten im „Standby“). Andere Dienste (z. B. HiveServer2 oder Hive-MetaStore) sind gleichzeitig auf beiden Hauptknoten aktiv.

[ZooKeeper](http://zookeeper.apache.org/)-Knoten (ZKs) wählen eine übergeordnete Instanz von Masterdiensten auf Hauptknoten aus und teilen Diensten, Datenknoten (Workerknoten) und Gateways mit, auf welchem Hauptknoten ein Masterdienst aktiv ist.

## Zugreifen zu Hauptknoten

Im Allgemeinen hat die Verwendung mehrerer Hauptknoten keinen Einfluss auf den Clusterzugriff über die öffentlichen Gateways (Ambari Web und REST-APIs). Die Anforderung wird an den aktiven Hauptknoten weitergeleitet und wie erforderlich verarbeitet.

Beim Zugriff auf den Cluster über SSH wird bei Verwendung von Port 22 (Standardport für SSH) eine Verbindung mit „Hauptknoten 0“ hergestellt und bei Verwendung von Port 23 eine Verbindung mit „Hauptknoten 1“.

### Interne vollqualifizierte Domänennamen (FQDN)

Knoten in einem HDInsight-Cluster besitzen eine interne IP-Adresse und einen internen FQDN, auf die nur über den Cluster zugegriffen werden kann (z. B. eine SSH-Sitzung mit dem Hauptknoten oder ein im Cluster ausgeführter Auftrag). Wenn Sie mit dem internen FQDN oder der internen IP-Adresse auf Dienste im Cluster zugreifen, sollten Sie die zu verwendende IP-Adresse oder den FQDN mithilfe von Ambari überprüfen.

Der Oozie-Dienst kann z. B. nur auf einem Hauptknoten ausgeführt werden, und zur Verwendung des `oozie`-Befehls in einer SSH-Sitzung ist die URL des Diensts erforderlich. Die URL kann mit dem folgenden Befehl aus Ambari abgerufen werden:

	curl -u admin:PASSWORD "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations?type=oozie-site&tag=TOPOLOGY_RESOLVED" | grep oozie.base.url

Der zurückgegebene Wert enthält die mit dem `oozie`-Befehl zu verwendende interne URL und sieht in etwa wie folgt aus:

	"oozie.base.url": "http://hn0-CLUSTERNAME-randomcharacters.cx.internal.cloudapp.net:11000/oozie"

## Überprüfen eines Dienststatus

Der Status von Diensten, die auf dem Hauptknoten ausgeführt werden, kann mit der Ambari-Webbenutzeroberfläche oder der Ambari-REST-API überprüft werden.

###Ambari-REST-API

Sie können den folgenden Befehl verwenden, um den Status eines Diensts über die Ambari-REST-API zu überprüfen:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICENAME?fields=ServiceInfo/state

* Ersetzen Sie **PASSWORD** durch Kontokennwort des HTTP-Benutzers (Administrator).

* Ersetzen Sie **CLUSTERNAME** durch den Namen des Clusters.

* Ersetzen Sie **SERVICENAME** durch den Namen des Diensts, dessen Status Sie überprüfen möchten.

Wenn Sie z. B. den Status des **HDFS**-Diensts in einem Cluster mit dem Namen **mycluster** überprüfen möchten und das Kennwort **password** lautet, würden Sie folgenden Befehl verwenden:

	curl -u admin:password https://mycluster.azurehdinsight.net/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state

Die Antwort sieht in etwa wie folgt aus:

	{
	  "href" : "http://hn0-CLUSTERNAME.randomcharacters.cx.internal.cloudapp.net:8080/api/v1/clusters/mycluster/services/HDFS?fields=ServiceInfo/state",
	  "ServiceInfo" : {
	    "cluster_name" : "mycluster",
	    "service_name" : "HDFS",
	    "state" : "STARTED"
	  }
	}

Der URL können Sie entnehmen, dass der Dienst zurzeit auf **Hauptknoten 0** ausgeführt wird.

Der Statuswert (state) gibt an, dass der Dienst zurzeit ausgeführt wird bzw. sich im Status **STARTED** befindet.

Falls Sie nicht wissen, welche Dienste auf dem Cluster installiert sind, können Sie mit dem folgenden Befehl eine Liste abrufen:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services

####Dienstkomponenten

Dienste können Komponenten enthalten, deren Status Sie einzeln überprüfen möchten. HDFS enthält z. B. die NameNode-Komponente. Der Befehl zum Anzeigen von Informationen zu einer Komponente lautet wie folgt:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

Falls Sie nicht wissen, welche Komponenten von einem Dienst bereitgestellt werden, können Sie mit dem folgenden Befehl eine Liste abrufen:

	curl -u admin:PASSWORD https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/SERVICE/components/component

###Ambari-Webbenutzeroberfläche

Die Ambari-Webbenutzeroberfläche ist unter https://CLUSTERNAME.azurehdinsight.net verfügbar. Ersetzen Sie **CLUSTERNAME** durch den Namen Ihres Clusters. Wenn Sie dazu aufgefordert werden, geben Sie die HTTP-Anmeldeinformationen für Ihren Cluster ein. Der standardmäßige HTTP-Benutzername ist **admin**, und das Kennwort ist das Kennwort, das Sie beim Erstellen des Clusters eingegeben haben.

Die installierten Dienste werden im linken Bereich der Ambari-Seite aufgeführt.

![Installierte Dienste](./media/hdinsight-high-availability-linux/services.png)

Neben den Diensten werden möglicherweise Statussymbole angezeigt. Alle Warnungen in Zusammenhang mit einem Dienst können über den Link **Warnungen** oben auf der Seite angezeigt werden. Sie können einen Dienst auswählen, um weitere Informationen anzuzeigen.

Auf der Dienstseite werden zwar Informationen zum Status und zur Konfiguration jedes Diensts angezeigt, aber keine Informationen dazu, auf welchem Hauptknoten der Dienst ausgeführt wird. Diese Informationen können über den Link **Hosts** oben auf der Seite aufgerufen werden. Wenn Sie auf den Link klicken, werden die Hosts innerhalb des Clusters angezeigt, einschließlich der Hauptknoten.

![Liste der Hosts](./media/hdinsight-high-availability-linux/hosts.png)

Wenn Sie auf den Link für einen der Hauptknoten klicken, werden die auf diesem Knoten ausgeführten Dienste und Komponenten angezeigt.

![Komponentenstatus](./media/hdinsight-high-availability-linux/nodeservices.png)

## Zugreifen auf Protokolldateien auf dem sekundären Hauptknoten

###SSH

Wenn Sie über SSH mit einem Hauptknoten verbunden sind, sind die Protokolldateien unter **/Var/Log** verfügbar. **/var/log/hadoop-yarn/yarn** enthält z. B. Protokolle für YARN.

Da für jeden Hauptknoten spezifische Protokolleinträge vorhanden sein können, sollten Sie die Protokolle auf beiden Hauptknoten überprüfen.

###Ambari

> [AZURE.NOTE]Für den Zugriff auf Protokolldateien über Ambari ist ein SSH-Tunnel erforderlich, da die Websites für die einzelnen Dienste nicht öffentlich im Internet verfügbar sind. Informationen zum Verwenden eines SSH-Tunnels finden Sie unter [Verwenden von SSH-Tunneling zum Zugriff auf die Ambari-Webbenutzeroberfläche, ResourceManager, JobHistory, NameNode, Oozie und andere Webbenutzeroberflächen](hdinsight-linux-ambari-ssh-tunnel.md).

Wählen Sie in der Ambari-Webbenutzeroberfläche den Dienst aus, für den Sie Protokolle anzeigen möchten (z. B. YARN), und wählen Sie dann mithilfe von **Quicklinks** den gewünschten Hauptknoten aus.

![Verwenden von Quicklinks zum Anzeigen von Protokollen](./media/hdinsight-high-availability-linux/viewlogs.png)

## Konfigurieren der Größe des Hauptknotens ##

Die Größe des Hauptknotens kann nur beim Erstellen des Clusters ausgewählt werden. Die Standardgröße für Hauptknoten ist **A3**, d. h. vier Kerne, 7 GB Arbeitsspeicher und 285 GB lokaler Speicher. Eine Liste der verschiedenen VM-Größen, die für HDInsight verfügbar sind (einschließlich Kerne, Arbeitsspeicher und lokaler Speicher), finden Sie auf der Seite [HDInsight-Preisdetails](http://azure.microsoft.com/pricing/details/hdinsight/).

Beim Erstellen eines neuen Clusters können Sie die Größe der Knoten angeben. Die Größe kann wie folgt im [Azure-Vorschauportal][preview-portal], mit [Azure PowerShell][azure-powershell] und mit der [Azure-Befehlszeilenschnittstelle][azure-cli] festgelegt werden:

* **Azure-Vorschauportal**: Beim Erstellen eines neuen Clusters haben Sie die Möglichkeit, die Größe (Tarif) der Haupt- und Datenknoten (Workerknoten) für den Cluster festzulegen:

	![Screenshot des Assistenten zur Clustererstellung mit der Auswahl der Knotengröße](./media/hdinsight-high-availability-linux/headnodesize.png)

* **Azure-Befehlszeilenschnittstelle**: Die Größe des Hauptknotens kann bei Verwendung des Befehls `azure hdinsight cluster create` mit dem `--headNodeSize`-Parameter angegeben werden.

* **Azure PowerShell**: Die Größe des Hauptknotens kann bei Verwendung des Cmdlets `New-AzureRmHDInsightCluster` mit dem `-HeadNodeVMSize`-Parameter angegeben werden.

##Nächste Schritte

In diesem Dokument haben Sie erfahren, wie Azure HDInsight hohe Verfügbarkeit für Hadoop bietet. In den folgenden Artikeln finden Sie weiterführende Informationen.

- [Ambari-REST-Referenz](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md)

- [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md)

- [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md)

- [Verwalten von HDInsight mit Ambari](hdinsight-hadoop-manage-ambari.md)

- [Bereitstellen Linux-basierter HDInsight-Cluster](hdinsight-hadoop-provision-linux-clusters.md)

[preview-portal]: https://portal.azure.com/
[azure-powershell]: ../powershell-install-configure.md
[azure-cli]: ../xplat-cli-install.md

<!---HONumber=Nov15_HO2-->