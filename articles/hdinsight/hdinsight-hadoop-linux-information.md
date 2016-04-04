<properties
   pageTitle="Tipps zur Verwendung von Hadoop unter Linux-basiertem HDInsight | Microsoft Azure"
   description="Hier erhalten Sie Implementierungstipps für die Verwendung von Linux-basierten HDInsight (Hadoop)-Clustern in einer vertrauten Linux-Umgebung, die in der Azure-Cloud ausgeführt wird."
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
   ms.date="03/18/2016"
   ms.author="larryfr"/>

# Informationen zur Verwendung von HDInsight unter Linux

Auf Linux basierende Azure HDInsight-Cluster stellen Hadoop in einer vertrauten Linux-Umgebung bereit, die in der Azure-Cloud ausgeführt wird. Die Lösung sollte sich größtenteils genauso wie jede andere Installation von Hadoop unter Linux verhalten. In diesem Dokument werden bestimmte Unterschiede aufgeführt, die Sie kennen sollten.

## Domänennamen

Der vollqualifizierte Domänenname (FQDN) für die Verbindung zum Cluster aus dem Internet lautet **&lt;Clustername>.azurehdinsight.net** oder (nur für SSH) **&lt;Clustername>-ssh.azurehdinsight.net**.

Intern verfügt jeder Knoten im Cluster über einen Namen, der während der Konfiguration des Clusters zugewiesen wird. Um die Clusternamen zu suchen, besuchen Sie die Seite __Hosts__ auf der Ambari-Webbenutzeroberfläche, oder verwenden Sie Folgendes, um eine Liste der Hosts über die Ambari REST-API mit [cURL](http://curl.haxx.se/) und [jq](https://stedolan.github.io/jq/) zurückzugeben:

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/hosts" | jq '.items[].Hosts.host_name'

Ersetzen Sie dabei __PASSWORD__ durch das Kennwort des Administratorkontos und __CLUSTERNAME__ durch den Namen des Clusters. Hiermit wird ein JSON-Dokument mit einer Liste der Hosts im Cluster zurückgegeben, dann ruft jq den Wert des `host_name`-Elements für die einzelnen Hosts im Cluster ab.

Wenn Sie den Namen des Knotens für einen bestimmten Dienst suchen müssen, können Sie Ambari nach dieser Komponente abfragen. Beispiel: Um die Hosts für den HDFS-Namensknoten zu suchen, verwenden Sie Folgendes.

    curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/services/HDFS/components/NAMENODE" | jq '.host_components[].HostRoles.host_name'

Hiermit wird ein JSON-Dokument mit einer Beschreibung des Diensts zurückgegeben, dann ruft jq nur den `host_name`-Wert für die Hosts ab.

## Remotezugriff auf Dienste

* **Ambari (Web)** - https://&lt;clustername>.azurehdinsight.net

	Authentifizieren Sie sich mit dem Benutzernamen und Kennwort des Clusteradministrators, und melden Sie sich anschließend bei Ambari an. Hierbei werden ebenfalls Benutzername und Kennwort des Clusteradministrators verwendet.

	Die Authentifizierung erfolgt unverschlüsselt. Verwenden Sie immer HTTPS, um eine sichere Verbindung zu gewährleisten.

	> [AZURE.IMPORTANT] Während Ambari für Ihren Cluster direkt über das Internet zugänglich ist, benötigen einige Funktionen den Knotenzugriff über den internen Domänennamen, der vom Cluster verwendet wird. Da dies ein interner und somit nicht öffentlicher Domänenname ist, erhalten Sie die Fehlermeldung, dass der Server nicht gefunden wurde, wenn Sie versuchen, auf einige Features über das Internet zuzugreifen.
	>
	> Damit Sie die Funktionalität der Ambari-Webbenutzeroberfläche vollständig nutzen können, verwenden Sie einen SSH-Tunnel, um den Webdatenverkehr per Proxy an den Clusterhauptknoten weiterzuleiten. Weitere Informationen finden Sie unter [Verwenden von SSH-Tunneling zum Zugriff auf die Ambari-Webbenutzeroberfläche, ResourceManager, JobHistory, NameNode, Oozie und andere Webbenutzeroberflächen](hdinsight-linux-ambari-ssh-tunnel.md).

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE] Authentifizieren Sie sich mit dem Benutzernamen und Kennwort des Clusteradministrators.
	>
	> Die Authentifizierung erfolgt unverschlüsselt. Verwenden Sie immer HTTPS, um eine sichere Verbindung zu gewährleisten.

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE] Authentifizieren Sie sich mit dem Benutzernamen und Kennwort des Clusteradministrators.
	>
	> Die Authentifizierung erfolgt unverschlüsselt. Verwenden Sie immer HTTPS, um eine sichere Verbindung zu gewährleisten.

* **SSH** - &lt;Custername>-ssh.azurehdinsight.net an Port 22 oder 23. Port 22 dient zum Herstellen einer Verbindung mit Hauptknoten 0, während Port 23 zum Herstellen einer Verbindung mit Hauptknoten 1 verwendet wird. Weitere Informationen zu Hauptknoten finden Sie unter [Verfügbarkeit und Zuverlässigkeit von Hadoop-Clustern in HDInsight](hdinsight-high-availability-linux.md).

	> [AZURE.NOTE] Sie können auf einem Clientcomputer nur über SSH auf die Hauptknoten des Clusters zugreifen. Nachdem die Verbindung hergestellt ist, können Sie vom Hauptknoten aus über SSH auf die Workerknoten zugreifen.

## Dateispeicherorte

Zu Hadoop zugehörige Dateien befinden sich auf den Clusterknoten in `/usr/hdp`. Dieses Verzeichnis enthält die folgenden Unterverzeichnisse:

* __2.2.4.9-1__: Dieses Verzeichnis wird gemäß der Version von Hortonworks Data Platform benannt, die von HDInsight verwendet wird. Die Zahl für Ihren Cluster kann sich daher von der hier angegebenen Zahl unterscheiden.
* __current__: Dieses Verzeichnis enthält Links zu Verzeichnissen im Verzeichnis __2.2.4.9-1__. Dadurch müssen Sie nicht bei jedem Dateizugriff eine Versionsnummer eingeben (die sich außerdem ändern kann).

Beispieldaten und JAR-Dateien für Hadoop Distributed File System (HDFS) oder Azure-Blobspeicher finden Sie unter '/example' oder 'wasb:///example'.

## Bewährte Methoden für HDFS, Azure-Blobspeicher und Speicher

In den meisten Hadoop-Distributionen wird HDFS auf den Computern im Cluster durch lokalen Speicher gesichert. Obwohl dies effizient ist, kann es für eine Cloud-basierte Lösung mit zeitabhängiger Abrechnung für Serverressourcen kostenintensiv sein.

HDInsight verwendet Azure-Blobspeicher als Standardspeicher, der folgende Vorteile bietet:

* Kostengünstige langfristige Speicherung

* Zugriff über externe Dienste wie Websites, Hilfsprogramme zum Hochladen/Herunterladen von Dateien, SDKs für verschiedene Sprachen und Webbrowser

Da dies der Standardspeicher für HDInsight ist, sind für seine Verwendung in der Regel keine weiteren Maßnahmen erforderlich. Der folgende Befehl listet z. B. Dateien im Ordner **/example/data** auf, der in Azure-Blobspeicher gespeichert wird:

	hadoop fs -ls /example/data

Bei einigen Befehlen müssen Sie möglicherweise angeben, dass Sie Blobspeicher verwenden. Diesen Befehle können Sie die Zeichenfolge "****WASB://**" voranstellen.

Mit HDInsight können Sie auch mehrere Blobspeicherkonten einem Cluster zuordnen. Für den Zugriff auf Daten in einem nicht standardmäßigen Blobspeicherkonto können Sie das Format **WASB://&lt;container-name>@&lt;Kontoname>.blob.core.windows.net/** verwenden. Der folgende Befehl listet z. B. den Inhalt des Verzeichnisses **/example/data** für den angegebenen Container und das Blobspeicherkonto auf:

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

### Welchen Blobspeicher verwendet der Cluster?

Während der Clustererstellung haben Sie entweder die Verwendung eines vorhandenen Azure-Speicherkontos und -containers ausgewählt, oder Sie erstellen diese neu. Dann haben Sie diese möglicherweise wieder vergessen. Sie können das Speicherkonto und den Container mithilfe der Ambari-REST-API finden.

1. Verwenden Sie den folgenden Befehl, um HDFS-Konfigurationsinformationen über „curl“ abzurufen, und filtern Sie sie mit [jq](https://stedolan.github.io/jq/):

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'
    
    > [AZURE.NOTE] Hierdurch wird die erste auf den Server angewendete Konfiguration (`service_config_version=1`) zurückgegeben, die diese Information enthält. Wenn Sie einen Wert abrufen, der nach der Erstellung des Clusters geändert wurde, müssen Sie möglicherweise die Konfigurationsversionen auflisten und die letzte Version abrufen.

    Dadurch wird ein ähnlicher Wert wie der folgende zurückgegeben, wobei __CONTAINER__ der Standardcontainer und __ACCOUNTNAME__ der Name des Azure-Speicherkontos ist:

        wasb://CONTAINER@ACCOUNTNAME.blob.core.windows.net

1. Rufen Sie die Ressourcengruppe für das Speicherkonto über die [Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md) ab. Ersetzen Sie im folgenden Befehl __ACCOUNTNAME__ durch den aus Ambari abgerufenen Namen des Speicherkontos:

        azure storage account list --json | jq '.[] | select(.name=="ACCOUNTNAME").resourceGroup'
    
    Hierdurch wird der Ressourcengruppenname für das Konto zurückgegeben.
    
    > [AZURE.NOTE] Wenn von diesem Befehl nichts zurückgegeben wird, müssen Sie die Azure-Befehlszeilenschnittstelle eventuell in den Azure-Ressourcen-Manager-Modus ändern und den Befehl erneut ausführen. Wechseln Sie mit dem folgenden Befehl in den Azure-Ressourcen-Manager-Modus:
    >
    > `azure config mode arm`
    
2. Rufen Sie den Schlüssel für das Speicherkonto ab. Ersetzen Sie __GROUPNAME__ durch den Namen der Ressourcengruppe aus dem vorherigen Schritt. Ersetzen Sie __ACCOUNTNAME__ durch den Namen des Speicherkontos:

        azure storage account keys list -g GROUPNAME ACCOUNTNAME --json | jq '.storageAccountKeys.key1'

    Hierdurch wird der Primärschlüssel für das Konto zurückgegeben.

Sie können die Speicherinformationen auch über das Azure-Portal finden:

1. Wählen Sie im [Azure-Portal](https://portal.azure.com/) Ihren HDInsight-Cluster aus.

2. Klicken Sie im Abschnitt __Zusammenfassung__ auf __Alle Einstellungen__.

3. Wählen Sie unter __Einstellungen__ die Option __Azure-Speicherschlüssel__ aus.

4. Wählen Sie unter __Azure-Speicherschlüssel__ eines der aufgeführten Speicherkonten aus. Damit werden Informationen zum Speicherkonto angezeigt.

5. Wählen Sie das Schlüsselsymbol aus. Damit werden die Schlüssel für dieses Speicherkonto angezeigt.

### Wie kann ich auf Blobspeicher zugreifen?

Neben dem vom Cluster aus gestarteten Hadoop-Befehl stehen eine Vielzahl von Möglichkeiten für den Zugriff auf Blobs zur Verfügung:

* [Azure-CLI für Mac, Linux und Windows](../xplat-cli-install.md): Befehlszeilenschnittstelle für die Arbeit mit Azure. Verwenden Sie nach der Installation den Befehl `azure storage`, um Hilfe zur Speicherung abzurufen, oder `azure blob` für blobspezifische Befehle.

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): Python-Skript zum Arbeiten mit Blobs im Azure-Speicher.

* Eine Vielzahl von SDKs:

	* [Java](https://github.com/Azure/azure-sdk-for-java)

	* [Node.js](https://github.com/Azure/azure-sdk-for-node)

	* [PHP](https://github.com/Azure/azure-sdk-for-php)

	* [Python](https://github.com/Azure/azure-sdk-for-python)

	* [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

	* [.NET](https://github.com/Azure/azure-sdk-for-net)

* [Storage-REST-API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

##<a name="scaling"></a>Skalieren des Clusters

Mithilfe der Clusterskalierung können Sie die Anzahl der von einem in Azure HDInsight ausgeführten Cluster verwendeten Datenknoten ändern, ohne den Cluster löschen und neu erstellen zu müssen.

Sie können Skalierungsvorgänge ausführen, während andere Aufträge oder Prozesse auf einem Cluster ausgeführt werden.

Folgende Clustertypen sind von der Skalierung betroffen:

* __Hadoop__: Wenn Sie die Anzahl von Knoten in einem Cluster zentral herunterskalieren, werden einige Dienste im Cluster neu gestartet. Dies kann beim Abschluss des Skalierungsvorgangs bei aktiven und ausstehenden Aufträgen zu einem Fehler führen. Sie können die Aufträge nach Abschluss des Vorgangs erneut senden.

* __HBase__: Regionale Server werden innerhalb weniger Minuten nach Abschluss des Skalierungsvorgangs automatisch ausgeglichen. Um regionale Server manuell auszugleichen, führen Sie folgende Schritte aus:

	1. Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her. Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie in den folgenden Dokumenten:

		* [Verwenden von SSH mit HDInsight unter Linux, Unix und Mac OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

		* [Verwenden von SSH mit HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

	1. Starten Sie die HBase-Shell wie folgt:

			hbase shell

	2. Sobald die HBase-Shell geladen ist, führen Sie folgende Schritte aus, um die regionalen Server manuell auszugleichen:

			balancer

* __Storm__: Sie sollten alle ausgeführten Storm-Topologien erneut ausgleichen, nachdem ein Skalierungsvorgang durchgeführt wurde. So kann die Topologie die Parallelitätseinstellungen an die neue Anzahl von Knoten im Cluster anpassen. Verwenden Sie eine der folgenden Optionen, um ausgeführte Topologien erneut auszugleichen:

	* __SSH__: Stellen Sie eine Verbindung zum Server her, und verwenden Sie folgenden Befehl, um eine Topologie erneut auszugleichen:

			storm rebalance TOPOLOGYNAME

		Sie können auch Parameter angeben, um die ursprünglich von der Topologie bereitgestellten Parallelitätshinweise zu überschreiben. Der Befehl `storm rebalance mytopology -n 5 -e blue-spout=3 -e yellow-bolt=10` z. B. konfiguriert die Topologie neu mit 5 Workerprozessen, 3 Executors für die Komponente "blue-spout" und 10 Executors für die Komponente "yellow-bolt".

	* __Storm-Benutzeroberfläche__: Führen Sie die folgenden Schritte aus, um eine Topologie mithilfe der Storm-Benutzeroberfläche erneut auszugleichen.

		1. Öffnen Sie \_\___https://CLUSTERNAME.azurehdinsight.net/stormui__ in einem Webbrowser, wobei CLUSTERNAME der Name Ihres Storm-Clusters ist. Geben Sie bei entsprechender Aufforderung den Namen und das Kennwort des HDInsight-Clusteradministrators ein, die Sie beim Erstellen des Clusters festgelegt haben.

		3. Wählen Sie die Topologie aus, die Sie erneut ausgleichen möchten, und klicken Sie dann auf die Schaltfläche __Neu ausgleichen__. Geben Sie die Verzögerung ein, bevor der Neuausgleich ausgeführt wird.

Detaillierte Informationen zum Skalieren von HDInsight-Clustern finden Sie hier:

* [Verwalten von Hadoop-Clustern in HDInsight mit dem Azure-Portal](hdinsight-administer-use-portal-linux.md#scaling)

* [Verwalten von Hadoop-Clustern in HDInsight mit Azure PowerShell](hdinsight-administer-use-command-line.md#scaling)

## Wie installiere ich Hue (oder eine andere Hadoop-Komponente)?

HDInsight ist ein verwalteter Dienst – dies bedeutet, dass Knoten in einem Cluster von Azure automatisch gelöscht und erneut bereitgestellt werden können, wenn ein Problem erkannt wird. Aus diesem Grund empfiehlt es sich nicht, Komponenten manuell auf den Clusterknoten zu installieren. Verwenden Sie stattdessen [HDInsight-Skriptaktionen](hdinsight-hadoop-customize-cluster.md), wenn Sie Folgendes installieren müssen:

* Einen Dienst oder eine Website, z. B. Spark oder Hue.
* Eine Komponente, für die Konfigurationsänderungen auf mehreren Knoten im Cluster erforderlich sind. Z. B. eine erforderliche Umgebungsvariable, Erstellen eines Protokollierungsverzeichnisses oder Erstellen einer Konfigurationsdatei.

Bei Skriptaktionen handelt es sich um Bash-Skripts, die während der Clusterbereitstellung ausgeführt werden und zur Installation und Konfiguration zusätzlicher Komponenten auf dem Cluster verwendet werden können. Zur Installation der folgenden Komponenten werden Beispielskripts bereitgestellt:

* [Hue](hdinsight-hadoop-hue-linux.md)
* [Giraph](hdinsight-hadoop-giraph-install-linux.md)
* [R](hdinsight-hadoop-r-scripts-linux.md)
* [Solr](hdinsight-hadoop-solr-install-linux.md)

Informationen zum Entwickeln eigener Skriptaktionen finden Sie unter [Entwickeln von Skriptaktionen mit HDInsight](hdinsight-hadoop-script-actions-linux.md).

###JAR-Dateien

Einige Hadoop-Technologien werden in eigenständigen JAR-Dateien mit Funktionen bereitgestellt, die als Teil eines MapReduce-Auftrags oder aus Pig oder Hive verwendet werden. Diese können mit Skriptaktionen erstellt werden, häufig ist jedoch kein Setup erforderlich und sie können nach der Bereitstellung einfach in den Cluster hochgeladen und direkt verwendet werden. Wenn Sie sicherstellen möchten, dass die Komponente bei einem Reimaging des Clusters nicht beschädigt wird, können Sie die JAR-Datei im WASB speichern.

Wenn Sie beispielsweise die neueste Version von [DataFu](http://datafu.incubator.apache.org/) verwenden möchten, können Sie eine JAR-Datei mit dem Projekt herunterladen und in den HDInsight-Cluster hochladen. Führen Sie dann die Anweisungen in der DataFu-Dokumentation aus, um sie aus Pig oder Hive zu verwenden.

> [AZURE.IMPORTANT] Einige Komponenten, bei denen es sich um eigenständige JAR-Dateien handelt, werden mit HDInsight bereitgestellt, sind jedoch nicht im Pfad vorhanden. Wenn Sie eine bestimmte Komponente suchen, können Sie sie hiermit im Cluster suchen:
>
> ```find / -name *componentname*.jar 2>/dev/null```
>
> Dadurch wird den Pfad aller entsprechenden JAR-Dateien zurückgegeben.

Wenn der Cluster bereits eine Version einer Komponente als eigenständige JAR-Datei enthält, Sie jedoch eine andere Version verwenden möchten, können Sie eine neue Version der Komponente in den Cluster hochladen und versuchen, sie in Ihren Aufträgen zu verwenden.

> [AZURE.WARNING] Komponenten, die mit dem HDInsight-Cluster bereitgestellt werden, werden vollständig unterstützt, und Microsoft Support hilft Ihnen, Probleme im Zusammenhang mit diesen Komponenten zu isolieren und zu beheben.
>
> Für benutzerdefinierte Komponenten steht kommerziell angemessener Support für eine weiterführende Behebung des Problems zur Verfügung. Auf diese Weise kann das Problem behoben werden, ODER Sie werden aufgefordert, verfügbare Kanäle für Open-Source-Technologien in Anspruch zu nehmen, die über umfassende Kenntnisse für diese Technologien verfügen. So können z. B. viele Communitywebsites verwendet werden, wie: das [MSDN-Forum für HDInsight](https://social.msdn.microsoft.com/Forums/azure/de-DE/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Für Apache-Projekte gibt es Projektwebsites auf [http://apache.org](http://apache.org), zum Beispiel: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).

## Nächste Schritte

* [Verwenden von Hive mit HDInsight](hdinsight-use-hive.md)
* [Verwenden von Pig mit HDInsight](hdinsight-use-pig.md)
* [Verwenden von MapReduce-Aufträgen mit HDInsight](hdinsight-use-mapreduce.md)

<!---HONumber=AcomDC_0323_2016-->