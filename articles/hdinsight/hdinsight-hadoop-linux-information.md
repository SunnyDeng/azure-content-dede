<properties
   pageTitle="Tipps zur Verwendung von Hadoop unter Linux-basiertem HDInsight | Microsoft Azure"
   description="Hier erhalten Sie Implementierungstipps für die Verwendung von Linux-basierten HDInsight (Hadoop)-Clustern in einer vertrauten Linux-Umgebung, die in der Azure-Cloud ausgeführt wird."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="05/27/2015"
   ms.author="larryfr"/>

# Informationen zur Verwendung von HDInsight unter Linux (Vorschau)

Auf Linux basierende Azure HDInsight-Cluster stellen Hadoop in einer vertrauten Linux-Umgebung bereit, die in der Azure-Cloud ausgeführt wird. Die Lösung sollte sich größtenteils genauso wie jede andere Installation von Hadoop unter Linux verhalten. In diesem Dokument werden bestimmte Unterschiede aufgeführt, die Sie kennen sollten.

## Domänennamen

Der vollqualifizierte Domänenname (FQDN) für die Verbindung zum Cluster lautet **&lt;Clustername>.azurehdinsight.net** oder (nur für SSH) **&lt;Clustername-ssh>.azurehdinsight.net**.

## Remotezugriff auf Dienste

* **Ambari (Web)** - https://&lt;clustername>.azurehdinsight.net

	> [AZURE.NOTE]Authentifizieren Sie sich mit dem Benutzernamen und Kennwort des Clusteradministrators, und melden Sie sich anschließend bei Ambari an. Hierbei werden ebenfalls Benutzername und Kennwort des Clusteradministrators verwendet.
	>
	> Die Authentifizierung erfolgt unverschlüsselt. Verwenden Sie immer HTTPS, um eine sichere Verbindung zu gewährleisten.

	Während Ambari für Ihren Cluster direkt über das Internet zugänglich ist, benötigen einige Funktionen den Knotenzugriff über den internen Domänennamen, der vom Cluster verwendet wird. Da dies ein interner und somit nicht öffentlicher Domänenname ist, erhalten Sie die Fehlermeldung, dass der Server nicht gefunden wurde, wenn Sie versuchen, auf einige Features über das Internet zuzugreifen.

	Um dieses Problem zu umgehen, verwenden Sie einen SSH-Tunnel, um den Webdatenverkehr an den Stammknoten des Clusters weiterzuleiten. Befolgen Sie im folgenden Artikel den Abschnitt **SSH-Tunnel**, um von einem Port des lokalen Computers einen SSH-Tunnel zum Cluster einzurichten:

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix, oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md) – Schritte zum Erstellen eines SSH-Tunnels mit dem Befehl `ssh`.

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows) – Anleitung zum Verwenden von Putty zum Erstellen eines SSH-Tunnels.

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE]Authentifizieren Sie sich mit dem Benutzernamen und Kennwort des Clusteradministrators.
	>
	> Die Authentifizierung erfolgt unverschlüsselt. Verwenden Sie immer HTTPS, um eine sichere Verbindung zu gewährleisten.

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE]Authentifizieren Sie sich mit dem Benutzernamen und Kennwort des Clusteradministrators.
	>
	> Die Authentifizierung erfolgt unverschlüsselt. Verwenden Sie immer HTTPS, um eine sichere Verbindung zu gewährleisten.

* **SSH** - &lt;Clustername>-ssh.azurehdinsight.net an Port 22

	> [AZURE.NOTE]Sie können auf einem Clientcomputer nur über SSH auf den Hauptknoten des Clusters zugreifen. Nachdem die Verbindung hergestellt ist, können Sie vom Hauptknoten aus über SSH auf die Workerknoten zugreifen.

## Dateispeicherorte

Zu Hadoop zugehörige Dateien befinden sich auf den Clusterknoten in `/usr/hdp/current`.

Beispieldaten und JAR-Dateien für Hadoop Distributed File System (HDFS) oder Azure-Blobspeicher finden Sie unter '/example' oder 'wasb:///example'.

## Bewährte Methoden für HDFS, Azure-Blobspeicher und Speicher

In den meisten Hadoop-Distributionen wird HDFS auf den Computern im Cluster durch lokalen Speicher gesichert. Obwohl dies effizient ist, kann es für eine Cloud-basierte Lösung mit zeitabhängiger Abrechnung für Serverressourcen kostenintensiv sein.

HDInsight verwendet Azure-Blobspeicher als Standardspeicher, der folgende Vorteile bietet:

* Kostengünstige langfristige Speicherung

* Zugriff über externe Dienste wie Websites, Hilfsprogramme zum Hochladen/Herunterladen von Dateien, SDKs für verschiedene Sprachen und Webbrowser

Da dies der Standardspeicher für HDInsight ist, sind für seine Verwendung in der Regel keine weiteren Maßnahmen erforderlich. Der folgende Befehl listet z. B. Dateien im Ordner **/example/data** auf, der in Azure-Blobspeicher gespeichert wird:

	hadoop fs -ls /example/data

Bei einigen Befehlen müssen Sie möglicherweise angeben, dass Sie Blobspeicher verwenden. Für diese Befehle können Sie die Zeichenfolge **WASB://** voranstellen.

Mit HDInsight können Sie auch mehrere Blobspeicherkonten einem Cluster zuordnen. Für den Zugriff auf Daten in einem nicht standardmäßigen Blobspeicherkonto können Sie das Format **WASB://&lt;container-name>@&lt;Kontoname>.blob.core.windows.net/** verwenden. Der folgende Befehl listet z. B. den Inhalt des Verzeichnisses **/example/data** für den angegebenen Container und das Blobspeicherkonto auf:

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

### Welchen Blobspeicher verwendet der Cluster?

Während der Clustererstellung haben Sie entweder die Verwendung eines vorhandenen Azure-Speicherkontos und -containers ausgewählt, oder Sie erstellen diese neu. Dann haben Sie diese möglicherweise wieder vergessen. Sie können das Speicherkonto und den Container mithilfe der folgenden Methoden finden.

**Ambari-API**

1. Verwenden Sie den folgenden Befehl aus, um HDFS-Konfigurationsinformationen abzurufen:

        curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"

2. Suchen Sie in den zurückgegebenen JSON-Daten nach dem Eintrag `fs.defaultFS`. Dieser enthält den Namen des Standardcontainers und Speicherkontos in einem Format wie dem folgenden:

        wasb://CONTAINTERNAME@STORAGEACCOUNTNAME.blob.core.windows.net

	> [AZURE.TIP]Wenn Sie [Jq](http://stedolan.github.io/jq/) installiert haben, können Sie folgenden Befehl verwenden, um lediglich den Eintrag `fs.defaultFS` zurückzugeben:
	>
	> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties["fs.defaultFS"] | select(. != null)'`
	
3. Zur Ermittlung des Schlüssels für die Authentifizierung beim Speicherkonto oder zur Ermittlung sekundärer Speicherkonten des Clusters führen Sie die folgenden Schritte aus:

		curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1"
		
4. Suchen Sie in den zurückgegebenen JSON-Daten nach Einträgen, die mit `fs.azure.account.key` beginnen. Der verbleibende Teil dieser Einträge ist der Name eines Speicherkontos. Beispiel: `fs.azure.account.key.mystorage.blob.core.windows.net`. Der Wert dieses Eintrags ist der Schlüssel für die Authentifizierung beim Speicherkonto.

	> [AZURE.TIP]Wenn Sie [jq](http://stedolan.github.io/jq/) installiert haben, können Sie wie folgt eine Liste der Schlüssel und Werte zurückgeben:
	>
	> `curl -u admin:PASSWORD -G "https://CLUSTERNAME.azurehdinsight.net/api/v1/clusters/CLUSTERNAME/configurations/service_config_versions?service_name=HDFS&service_config_version=1" | jq '.items[].configurations[].properties as $in | $in | keys[] | select(. | contains("fs.azure.account.key.")) as $item | $item | ltrimstr("fs.azure.account.key.") | { storage_account: ., storage_account_key: $in[$item] }'`


**Azure-Portal**

1. Wählen Sie im [Azure-Portal](https://manage.windowsazure.com/) Ihren HDInsight-Cluster aus.

2. Wählen Sie oben auf der Seite **Dashboard** aus.

3. Die Speicherkonten und Container werden auf der Seite im Abschnitt **Verknüpfte Ressourcen** aufgelistet.

	![Verknüpfte Ressourcen](./media/hdinsight-hadoop-linux-information/storageportal.png)

### Wie kann ich auf Blobspeicher zugreifen?

Neben dem vom Cluster aus gestarteten Hadoop-Befehl stehen eine Vielzahl von Möglichkeiten für den Zugriff auf Blobs zur Verfügung:

* [Azure-CLI für Mac, Linux und Windows](../xplat-cli.md): Befehlszeilenschnittstelle für die Arbeit mit Azure. Verwenden Sie nach der Installation den Befehl `azure storage`, um Hilfe zur Speicherung abzurufen, oder `azure blob` für blobspezifische Befehle.

* [blobxfer.py](https://github.com/Azure/azure-batch-samples/tree/master/Python/Storage): Python-Skript zum Arbeiten mit Blobs im Azure-Speicher.

* Eine Vielzahl von SDKs:

	* [Java](https://github.com/Azure/azure-sdk-for-java)

	* [Node.js](https://github.com/Azure/azure-sdk-for-node)

	* [PHP](https://github.com/Azure/azure-sdk-for-php)

	* [Python](https://github.com/Azure/azure-sdk-for-python)

	* [Ruby](https://github.com/Azure/azure-sdk-for-ruby)

	* [.NET](https://github.com/Azure/azure-sdk-for-net)

* [Storage-REST-API](https://msdn.microsoft.com/library/azure/dd135733.aspx)

## Nächste Schritte

* [Verwenden von Hive mit HDInsight](hdinsight-use-hive.md)
* [Verwenden von Pig mit HDInsight](hdinsight-use-pig.md)
* [Verwenden von MapReduce-Aufträgen mit HDInsight](hdinsight-use-mapreduce.md)
 

<!---HONumber=July15_HO2-->