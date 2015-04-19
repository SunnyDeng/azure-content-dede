<properties
   pageTitle="Was Sie über Hadoop auf Linux-basiertem HDInsight wissen müssen | Azure"
   description="Auf Linux basierende HDInsight-Cluster stellen Hadoop in einer vertrauten Linux-Umgebung bereit, die in der Azure-Cloud ausgeführt wird."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="paulettm"
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang=""
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Arbeiten mit HDInsight unter Linux (Vorschau)

Auf Linux basierende HDInsight-Cluster stellen Hadoop in einer vertrauten Linux-Umgebung bereit, die in der Azure-Cloud ausgeführt wird. Es sollte sich größtenteils genauso wie jede andere Installation von Hadoop unter Linux verhalten. In diesem Dokument werden bestimmte Unterschiede aufgeführt, die Sie kennen sollten.

## Domänennamen

Der vollqualifizierte Domänenname (FQDN) für die Verbindung zum Cluster lautet **&lt;Clustername>.azurehdinsight.net** oder (nur für SSH) **&lt;Clustername>.aurehdinsight.net**.

## Remote verfügbare Dienste

* **Ambari (Web)** - https://&lt;clustername>.azurehdinsight.net

	> [AZURE.NOTE] Authentifizierung mit Benutzername und Kennwort des Clusteradministrators mit anschließender Anmeldung bei Ambari. Hierbei werden ebenfalls Benutzername und Kennwort des Clusteradministrators verwendet.
	> 
	> Die Authentifizierung erfolgt als Nur-Text - immer HTTPS verwenden, um eine sichere Verbindung zu gewährleisten.

	Während Ambari für Ihren Cluster direkt über das Internet zugänglich ist, benötigen einige Funktionen den Knotenzugriff über den internen Domänennamen, der vom Cluster verwendet wird. Da dies ein interner und somit nicht öffentlicher Domänenname ist, erhalten Sie entsprechende Fehler, dass der Server nicht gefunden wurde, wenn Sie versuchen, auf einige Features über das Internet zuzugreifen.

	Um dieses Problem zu umgehen, verwenden Sie einen SSH-Tunnel, um den Webdatenverkehr an den Stammknoten des Clusters weiterzuleiten. Verwenden Sie die folgenden Artikel, um von einem Port des lokalen Computers einen SSH-Tunnel zum Cluster einzurichten.

	* <a href="../hdinsight-hadoop-linux-use-ssh-unix/#tunnel" target="_blank">Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix, oder OS X</a> - Schritte zum Erstellen eines SSH-Tunnels mit dem `ssh`-Befehl.

	* <a href="../hdinsight-hadoop-linux-use-ssh-windows/#tunnel" target="_blank">Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows</a> - Schritte zum Verwenden von Putty zum Erstellen eines SSH-Tunnels

* **Ambari (REST)** - https://&lt;clustername>.azurehdinsight.net/ambari

	> [AZURE.NOTE] Authentifizierung mit Benutzername und Kennwort des Clusteradministrators.
	> 
	> Die Authentifizierung erfolgt als Nur-Text - immer HTTPS verwenden, um eine sichere Verbindung zu gewährleisten.

* **WebHCat (Templeton)** - https://&lt;clustername>.azurehdinsight.net/templeton

	> [AZURE.NOTE] Authentifizierung mit Benutzername und Kennwort des Clusteradministrators.
	> 
	> Die Authentifizierung erfolgt als Nur-Text - immer HTTPS verwenden, um eine sichere Verbindung zu gewährleisten.

* **SSH** - &lt;Clustername>-ssh.azurehdinsight.net an Port 22

	> [AZURE.NOTE] Sie können nur über SSH von einem Clientcomputer auf den Stammknoten des Clusters zugreifen. Nachdem die Verbindung hergestellt ist, können Sie vom Stammknoten aus über SSH auf die Arbeitsknoten zugreifen.

## Dateispeicherorte

Zu Hadoop zugehörige Dateien befinden sich auf den Clusterknoten in  `/usr/hdp/current`.

Beispieldaten und JAR-Dateien befinden sich auf HDFS (WASB) unter "/example" oder "wasb:///example".

## Bewährte Methoden für HDFS, WASB und Speicher

In den meisten Hadoop-Distributionen wird das HDFS (Hadoop Distributed File System) auf den Computern im Cluster durch einen lokalen Speicher gesichert. Obwohl dies effizient ist, kann es für eine Cloud-basierte Lösung mit zeitabhängiger Abrechnung für Serverressourcen kostenintensiv sein.

HDInsight verwendet Azure Blob-Speicher als Standardspeicher, der folgende Vorteile bietet:

* Kostengünstige langfristige Speicherung

* Zugriff über externe Dienste wie Websites, Dienstprogramme zum Hochladen/Herunterladen von Dateien, SDKs für verschiedene Sprachen und Webbrowser

Da dies der Standardspeicher für HDInsight ist, sind für seine Verwendung in der Regel keine weiteren Maßnahmen erforderlich. Der folgende Befehl listet z. B. Dateien im Ordner **/example/data** auf, der auf Azure Blob-Speicher gespeichert wird.

	hadoop fs -ls /example/data

Bei einigen Befehlen müssen Sie möglicherweise angeben, dass Sie Blob-Speicher verwenden. Für diese Befehle können Sie die Zeichenfolge **WASB://** voranstellen.

Mit HDInsight können Sie auch mehrere Blob-Speicherkonten zu einem Cluster zuordnen. Für den Zugriff auf Daten in einem nicht standardmäßigen Blob-Speicherkonto können Sie das Format **WASB://&lt;Containername>@&lt;Kontoname>.blob.core.windows.net/** verwenden. Folgendes listet z. B. die Inhalte des Verzeichnisses **/example/data** für den angegebenen Container und das Speicherkonto auf.

	hadoop fs -ls wasb://mycontainer@mystorage.blob.core.windows.net/example/data

### Welchen Blob-Speicher verwendet der Cluster?

Während der Clustererstellung wählen Sie entweder die Verwendung eines vorhandenen Speicherkontos und Containers aus, oder Sie erstellen diese neu. Dann haben Sie diese möglicherweise wieder vergessen. Sie können das Speicherkonto und den Container mithilfe der folgenden Methoden finden.

**Azure-Portal**

1. Wählen Sie im <a href="https://manage.windowsazure.com/" target="_blank">Azure-Verwaltungsportal</a> Ihr HDInsight-Cluster.

2. Wählen Sie oben auf der Seite die Option **Dashboard** aus.

3. Die Speicherkonten und Container werden auf der Seite im Abschnitt **Verknüpfte Ressourcen** aufgelistet.

	![linked resources](./media/hdinsight-hadoop-linux-information/storageportal.png)

**Plattformübergreifende Azure-Befehlszeilenschnittstelle**

*In Kürze verfügbar*

### Wie kann ich auf Blob-Speicher zugreifen?

Neben dem vom Cluster aus gestarteten Hadoop-Befehl stehen eine Vielzahl von Möglichkeiten für den Zugriff auf Blobs zur Verfügung:

* <a href="http://azure.microsoft.com/documentation/articles/xplat-cli/" target="_blank">Plattformübergreifende Azure-Befehlszeilenschnittstelle </a> - Informationen zum Verwenden des Speichers oder  `azure blob` für Blob-bezogene Befehle nach Installation finden Sie unter  `azure storage`.

* Eine Vielzahl von SDKs:

	* <a href="https://github.com/Azure/azure-sdk-for-java" target="_blank">Java</a>

	* <a href="https://github.com/Azure/azure-sdk-for-node" target="_blank">Node.js</a>

	* <a href="https://github.com/Azure/azure-sdk-for-php" target="_blank">PHP</a>

	* <a href="https://github.com/Azure/azure-sdk-for-python" target="_blank">Python</a>

	* <a href="https://github.com/Azure/azure-sdk-for-ruby" target="_blank">Ruby</a>

	* <a href="https://github.com/Azure/azure-sdk-for-net" target="_blank">.NET</a>

* <a href="https://msdn.microsoft.com/library/azure/dd135733.aspx" target="_blank">Speicher-REST-API</a>


## Nächste Schritte

* [Verwenden von Hive mit HDInsight](hdinsight-use-hive.md)
* [Verwenden von Pig mit HDInsight](hdinsight-use-pig.md)
* [Verwenden von MapReduce-Aufträgen mit HDInsight](hdinsight-use-mapreduce.md)


<!--HONumber=47-->
