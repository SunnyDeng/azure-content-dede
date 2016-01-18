<properties
   pageTitle="Überwachen und Verwalten von HDInsight-Clustern mithilfe der Apache Ambari-REST-API | Microsoft Azure"
   description="Erfahren Sie, wie Sie Ambari zum Überwachen und Verwalten von Linux-basierten HDInsight-Clustern verwenden. In diesem Dokument erfahren Sie, wie Sie die in HDInsight-Clustern enthaltene Ambari-REST-API verwenden."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="01/06/2015"
   ms.author="larryfr"/>

#Verwalten von HDInsight-Clustern mithilfe der Ambari-REST-API

[AZURE.INCLUDE [ambari-selector](../../includes/hdinsight-ambari-selector.md)]

Apache Ambari vereinfacht die Verwaltung und Überwachung von Hadoop-Clustern durch die Bereitstellung einer benutzerfreundlichen Webbenutzeroberfläche und REST-API. Ambari ist in Linux-basierten HDInsight-Clustern enthalten und wird verwendet, um den Cluster zu überwachen und Konfigurationsänderungen vorzunehmen. Dieses Dokument vermittelt Ihnen die Grundlagen für das Arbeiten mit der Ambari-REST-API. Hierzu führen Sie allgemeine Aufgaben aus, z. B. das Suchen des vollqualifizierten Domänennamens der Clusterknoten oder das Suchen des vom Cluster verwendeten Standardspeicherkontos.

> [AZURE.NOTE]Die Informationen in diesem Artikel gelten nur für Linux-basierte HDInsight-Cluster. Für Windows-basierte HDInsight-Cluster ist nur ein Teil der Überwachungsfunktionen über die Ambari-REST-API verfügbar. Weitere Informationen finden Sie unter [Überwachen von Hadoop in HDInsight mit der Ambari-API](hdinsight-monitor-use-ambari-api.md).

##Voraussetzungen

* [cURL](http://curl.haxx.se/): cURL ist ein plattformübergreifendes Hilfsprogramm, das zum Arbeiten mit REST-APIs über die Befehlszeile verwendet werden kann. In diesem Dokument wird es für die Kommunikation mit der Ambari-REST-API verwendet.
* [jq](https://stedolan.github.io/jq/): jq ist ein plattformübergreifendes Befehlszeilenprogramm zum Arbeiten mit JSON-Dokumenten. In diesem Dokument wird es zum Analysieren der von der Ambari REST-API zurückgegebenen JSON-Dokumente verwendet.

##<a id="whatis"></a>Was ist Ambari?

<a href="http://ambari.apache.org" target="_blank">Apache Ambari</a> vereinfacht die Hadoop-Verwaltung durch die Bereitstellung einer benutzerfreundlichen Webbenutzeroberfläche, die zum Bereitstellen, Verwalten und Überwachen von Hadoop-Clustern verwendet werden kann. Entwickler können diese Funktionen mithilfe der <a href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md" target="_blank">Ambari-REST-APIs</a> in ihre Anwendungen integrieren.

Ambari wird standardmäßig mit Linux-basierten Clustern bereitgestellt.

##REST-API

Der Basis-URI für die Ambari REST-API in HDInsight lautet https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME, wobei __CLUSTERNAME__ der Name des Clusters ist.

> [AZURE.IMPORTANT]Zum Herstellen einer Verbindung mit Ambari in HDInsight ist HTTPS erforderlich. Sie müssen sich bei Ambari auch mit dem Administratorkontonamen (standardmäßig __Admin__) und dem Kennwort authentifizieren, die Sie bei der Erstellung des Clusters angegeben haben.

Im folgenden Beispiel wird cURL zum Ausführen einer GET-Anforderung für die REST-API verwendet:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME"
    
Wenn Sie diesen Befehl ausführen und __PASSWORD__ durch das Administratorkennwort sowie __CLUSTERNAME__ durch den Clusternamen ersetzen, erhalten Sie ein JSON-Dokument, das mit Informationen wie etwa den folgenden beginnt:

    {
    "href" : "http://10.0.0.10:8080/api/v1/clusters/CLUSTERNAME",
    "Clusters" : {
        "cluster_id" : 2,
        "cluster_name" : "CLUSTERNAME",
        "health_report" : {
        "Host/stale_config" : 0,
        "Host/maintenance_state" : 0,
        "Host/host_state/HEALTHY" : 7,
        "Host/host_state/UNHEALTHY" : 0,
        "Host/host_state/HEARTBEAT_LOST" : 0,
        "Host/host_state/INIT" : 0,
        "Host/host_status/HEALTHY" : 7,
        "Host/host_status/UNHEALTHY" : 0,
        "Host/host_status/UNKNOWN" : 0,
        "Host/host_status/ALERT" : 0

Da es sich um JSON handelt, ist es in der Regel einfacher, zum Abrufen der Daten einen JSON-Parser zu verwenden. Wenn Sie z. B. die Anzahl der Warnungen (im Element __"Host/host\_status/ALERT"__) abrufen möchten, können Sie mit der folgenden Anweisung direkt auf den Wert zugreifen:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME" | jq '.Clusters.health_report."Host/host_status/ALERT"'
    
Hiermit wird das JSON-Dokument abgerufen und dann die Ausgabe an jq übergeben. `'.Clusters.health_report."Host/host_status/ALERT"'` gibt das Element im JSON-Dokument an, das Sie abrufen möchten.

> [AZURE.NOTE]Das Element __Host\_status/Host/Warnung__ ist in Anführungszeichen eingeschlossen, um anzugeben, dass "/" Teil des Elementnamens ist. Weitere Informationen zur Verwendung von jq finden Sie auf der [jq-Website](https://stedolan.github.io/jq/).

##Beispiel: Abrufen des FQDN von Clusterknoten

Bei der Arbeit mit HDInsight müssen Sie möglicherweise den vollqualifizierten Domänennamen (Fully Qualified Domain Name, FQDN) eines Clusterknotens kennen. Sie können den FQDN für die verschiedenen Knoten im Cluster einfach wie folgt abrufen:

* __Hauptknoten__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'`
* __Workerknoten__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/DATANODE" | jq '.host_components[].HostRoles.host_name'`
* __Zookeeper-Knoten__: `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/ZOOKEEPER/components/ZOOKEEPER_SERVER" | jq '.host_components[].HostRoles.host_name'`

Beachten Sie, dass jede dieser Anweisungen nach dem gleichen Muster erfolgt: Abrufen einer Komponente, von der bekannt ist, dass sie auf diesen Knoten ausgeführt wird, und dann Abrufen der `host_name`-Elemente, die den FQDN für diese Knoten enthalten.

Das `host_components`-Element des Rückgabedokuments enthält mehrere Elemente. Durch Verwendung von `.host_components[]` und Angabe eines Pfads im Element werden die einzelnen Elemente durchlaufen und der Wert aus dem jeweiligen Pfad abgerufen. Wenn Sie nur einen Wert, z. B. den ersten FQDN-Eintrag, erhalten möchten, können Sie die Elemente als Auflistung zurückgeben und dann einen bestimmten Eintrag auswählen:

    jq '[.host_components[].HostRoles.host_name][0]'

Mit dieser Anweisung wird der erste FQDN aus der Auflistung zurückgegeben.

##Beispiel: Abrufen des Standardspeicherkontos und -containers

Wenn Sie einen HDInsight-Cluster erstellen, müssen Sie ein Azure-Speicherkonto und einen Azure-Blob-Container als Standardspeicher für den Cluster verwenden. Sie können diese Informationen mit Ambari abrufen, nachdem der Cluster erstellt wurde. Dies bietet sich zum Beispiel an, wenn Sie Daten programmgesteuert direkt in den Container schreiben möchten.

Mit der folgenden Anweisung wird der WASB-URI des Clusterstandardspeichers abgerufen:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
> [AZURE.NOTE]Hierdurch wird die erste auf den Server angewendete Konfiguration (`service_config_version=1`) zurückgegeben, die diese Information enthält. Wenn Sie einen Wert abrufen, der nach der Erstellung des Clusters geändert wurde, müssen Sie möglicherweise die Konfigurationsversionen auflisten und die letzte Version abrufen.

Dadurch wird ein ähnlicher Wert wie der folgende zurückgegeben, wobei __CONTAINER__ der Standardcontainer und __ACCOUNTNAME__ der Name des Azure-Speicherkontos ist:

    wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net

Sie können dann diese Information mit der [Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md) verwenden, um Daten in den Container hochzuladen oder aus diesem herunterzuladen. Beispiel:

1. Rufen Sie die Ressourcengruppe für das Speicherkonto ab. Ersetzen Sie __ACCOUNTNAME__ durch den aus Ambari abgerufenen Namen des Speicherkontos:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Hierdurch wird der Ressourcengruppenname für das Konto zurückgegeben.
    
    > [AZURE.NOTE]Wenn von diesem Befehl nichts zurückgegeben wird, müssen Sie die Azure-Befehlszeilenschnittstelle eventuell in den Azure-Ressourcen-Manager-Modus ändern und den Befehl erneut ausführen. Wechseln Sie mit dem folgenden Befehl in den Azure-Ressourcen-Manager-Modus:
    >
    > `azure config mode arm`
    
2. Rufen Sie den Schlüssel für das Speicherkonto ab. Ersetzen Sie __GROUPNAME__ durch den Namen der Ressourcengruppe aus dem vorherigen Schritt. Ersetzen Sie __ACCOUNTNAME__ durch den Namen des Speicherkontos:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    Hierdurch wird der Primärschlüssel für das Konto zurückgegeben.
    
3. Verwenden Sie den upload-Befehl, um eine Datei im Container zu speichern:

        azure storage blob upload -a ACCOUNTNAME -k ACCOUNTKEY -f FILEPATH --container __CONTAINER__ -b BLOBPATH
        
    Ersetzen Sie __ACCOUNTNAME__ durch den Namen des Speicherkontos. Ersetzen Sie __ACCOUNTKEY__ durch den zuvor abgerufenen Schlüssel. __FILEPATH__ ist der Pfad zu der Datei, die Sie hochladen möchten, und __BLOBPATH__ ist der Pfad im Container.

    Wenn beispielsweise die Datei in HDInsight in wasb://example/data/filename.txt angegeben werden soll, lautet __BLOBPATH__ `example/data/filename.txt`.

##Nächste Schritte

Eine vollständige Referenz der REST-API finden Sie unter [Referenz zur Ambari-API V1](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md).

> [AZURE.NOTE]Einige Ambari-Funktionen sind deaktiviert, da sie vom HDInsight-Clouddienst verwaltet werden, z. B. Hinzufügen oder Entfernen von Hosts im Cluster oder Hinzufügen neuer Dienste.

<!---HONumber=AcomDC_0107_2016-->