<properties
	pageTitle="Verwenden von Skriptaktionen zum Installieren von Solr in Linux-basiertem HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Sie Solr auf Linux-basierten HDInsight Hadoop-Clustern mit Skriptaktionen installieren."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/05/2016"
	ms.author="larryfr"/>

# Installieren und Verwenden von Solr in HDInsight Hadoop-Clustern

In diesem Thema wird beschrieben, wie Sie Solr in Azure HDInsight mithilfe der Funktion "Skriptaktion" installieren. Solr ist eine leistungsfähige Suchplattform und bietet Suchfunktionen der Unternehmensklasse für von Hadoop verwaltete Daten. Sobald Sie Solr in einem HDInsight-Cluster installiert haben, erfahren Sie auch, wie Sie Daten mithilfe von Solr suchen können.

> [AZURE.NOTE] Die Schritte in diesem Dokument erfordern einen Linux-basierten HDInsight-Cluster. Informationen zur Verwendung von Solr mit einem Windows-basierten Cluster finden Sie unter [Installieren und Verwenden von Solr in HDinsight Hadoop-Clustern (Windows)](hdinsight-hadoop-solr-install.md).

Das in diesem Thema verwendete Beispielskript erstellt einen Solr-Cluster mit einer bestimmten Konfiguration. Wenn Sie den Solr-Cluster mit anderen Auflistungen, Shards, Schemas, Replikaten usw. konfigurieren möchten, müssen Sie das Skript und die Solr-Binärdateien entsprechend ändern.

## <a name="whatis"></a>Was ist Solr?

[Apache Solr](http://lucene.apache.org/solr/features.html) ist eine Unternehmensplattform für die leistungsstarke Volltextsuche in Daten. Während Hadoop das Speichern und Verwalten von großen Datenmengen ermöglicht, bietet Apache Solr die Suchfunktionen, um schnell Daten abzurufen. In diesem Thema wird beschrieben, wie Sie ein HDInsight-Cluster zum Installieren von Solr anpassen können.

## Funktion des Skripts

Mit diesem Skript werden die folgenden Änderungen am HDInsight-Cluster vorgenommen:

* Installiert Solr auf `/usr/hdp/current/solr`.
* Erstellt einen neuen Benutzer, __Solrusr__, der zum Ausführen des Solr-Dienstes verwendet wird.
* Legt __Solruser__ als Besitzer von `/usr/hdp/current/solr` fest.
* Fügt eine [Upstart](http://upstart.ubuntu.com/)-Konfiguration hinzu, die Solr startet, wenn ein Clusterknoten neu gestartet wird. Solr wird außerdem nach der Installation automatisch auf den Clusterknoten gestartet.

## <a name="install"></a>Installation von Solr mithilfe von Skriptaktionen

Ein Beispielskript zum Installieren von Solr in einem HDInsight-Cluster steht in einem schreibgeschützten Azure-Speicherblob unter [https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh) zur Verfügung. Dieser Abschnitt enthält Anweisungen zur Verwendung des Beispielskripts während der Bereitstellung des Clusters mithilfe des Azure-Portals.

> [AZURE.NOTE] Sie können auch Azure PowerShell oder das HDInsight .NET SDK zum Erstellen eines Clusters mit diesem Skript verwenden. Weitere Informationen zur Verwendung dieser Methoden finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

1. Beginnen Sie die Bereitstellung eines Clusters anhand der Schritte in [Bereitstellen Linux-basierter HDInsight-Cluster](hdinsight-provision-linux-clusters.md#portal), schließen Sie sie jedoch nicht ab.

2. Wählen Sie auf dem Blatt **Optionale Konfiguration** die Option **Skriptaktionen**, und geben Sie die folgenden Informationen an:

	* __NAME__: Geben Sie einen Anzeigenamen für die Skriptaktion ein.
	* __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxsolrconfigactionv01/solr-installer-v01.sh
	* __HEAD__: Aktivieren Sie diese Option.
	* __WORKER__: Aktivieren Sie diese Option.
	* __ZOOKEEPER__: Aktivieren Sie diese Option für die Installation auf dem Zookeeper-Knoten.
	* __PARAMETERS__: Lassen Sie dieses Feld leer.

3. Verwenden Sie am unteren Rand der **Skriptaktionen** die Schaltfläche **Auswählen**, um die Konfiguration zu speichern. Verwenden Sie schließlich die Schaltfläche **Auswählen** am unteren Rand des Blatts **Optionale Konfiguration**, um die optionalen Konfigurationsinformationen zu speichern.

4. Setzen Sie die Bereitstellung des Clusters wie unter [Bereitstellen Linux-basierter HDInsight-Cluster](hdinsight-hadoop-create-linux-clusters-portal.md) beschrieben fort.

## <a name="usesolr"></a>Wie verwende ich Solr in HDInsight?

### Indizieren von Daten

Sie müssen mit der Indizierung von Solr mit einigen Datendateien beginnen. Sie können dann Solr verwenden, um Suchabfragen der indizierten Daten auszuführen. Verwenden Sie die folgenden Schritte, um einige Beispieldaten zu Solr hinzuzufügen und diese dann abzufragen:

1. Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie in den folgenden Artikeln:

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

	> [AZURE.IMPORTANT] Die späteren Schritte in diesem Dokument verwenden einen SSL-Tunnel, um mit der Solr-Webbenutzeroberfläche eine Verbindung herzustellen. Um diese Schritte ausführen zu können, müssen Sie einen SSL-Tunnel einrichten, und Ihren Browser dann zu dessen Verwendung einrichten.
	>
	> Weitere Informationen finden Sie unter [Verwenden von SSH-Tunneling zum Zugriff auf die Ambari-Webbenutzeroberfläche, ResourceManager, JobHistory, NameNode, Oozie und andere Webbenutzeroberflächen](hdinsight-linux-ambari-ssh-tunnel.md).

2. Verwenden Sie die folgenden Befehle, mit denen Solr Beispieldaten indiziert:

		cd /usr/hdp/current/solr/example/exampledocs
		java -jar post.jar solr.xml monitor.xml

	In der Konsole wird die folgende Ausgabe angezeigt:

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	Das Hilfsprogramm "post.jar" indiziert Solr mit zwei Beispieldokumenten: **solr.xml** und **monitor.xml**. Diese werden in __collection1__ innerhalb von Solr gespeichert.

3. Verwenden Sie folgenden Befehl, um die von Solr verfügbar gemachte REST-API abzufragen:

		curl "http://localhost:8983/solr/collection1/select?q=*%3A*&wt=json&indent=true"

	Dadurch wird eine Abfrage gegen __collection1__ für alle Dokumente ausgestellt, die __*:*__ (codiert als *% 3A* in der Abfragezeichenfolge) entsprechen. Die Antwort sollte im JSON-Format zurückgegeben werden. Die Antwort sollte in etwa wie folgt aussehen:

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }

### Verwenden des Solr-Dashboards

Das Solr-Dashboard ist eine Webbenutzeroberfläche, mit dem Sie über Ihren Webbrowser mit Solr arbeiten können. Das Solr-Dashboard wir nicht direkt im Internet aus Ihrem HDInsight-Cluster verfügbar gemacht. Der Zugriff muss mithilfe eines SSH-Tunnels stattfinden. Weitere Informationen zum Verwenden eines SSH-Tunnels finden Sie unter [Verwenden von SSH-Tunneling zum Zugriff auf die Ambari-Webbenutzeroberfläche, ResourceManager, JobHistory, NameNode, Oozie und andere Webbenutzeroberflächen](hdinsight-linux-ambari-ssh-tunnel.md).

Wenn Sie einen SSH-Tunnel eingerichtet haben, gehen Sie folgendermaßen vor, um das Solr-Dashboard zu verwenden:

1. Bestimmen Sie den Hostnamen für den Hauptknoten:

    1. Wechseln Sie in einem Browser zu https://CLUSTERNAME.azurehdinsight.net. Wenn Sie aufgefordert werden, verwenden Sie den Admin-Benutzername und das Kennwort zur Authentifizierung auf der Website.
    
    2. Wählen im Menü oben auf der Seite __Hosts__ aus.
    
    3. Wählen Sie den Eintrag aus, der mit __hn0__ beginnt. Beim Öffnen der Seite wird oben der Hostname angezeigt. Das Format des Hostnamens ist __hn0 PARTOFCLUSTERNAME.randomcharacters.cx.internal.cloudapp.net__. Dieser Hostname muss bei der Verbindung mit dem Solr-Dashboard verwendet werden.
    
1. Stellen Sie in Ihrem Browser eine Verbindung mit \_\___http://HOSTNAME:8983/solr/#/__ her, wobei __HOSTNAME__ der Name ist, die Sie in den vorherigen Schritten bestimmt haben.

    Die Anfrage sollte über den SSH-Tunnel auf den Hauptknoten für Ihren HDInsight-Cluster weitergeleitet werden. Eine Seite ähnlich der folgenden wird angezeigt:

	![Bild des Solr-Dashboards](./media/hdinsight-hadoop-solr-install-linux/solrdashboard.png)

2. Wählen Sie im linken Bereich in der Dropdownliste **Core Selector** den Eintrag **collection1** aus. Mehrere Einträge sollten unterhalb von __collection1__ erscheinen.

3. Wählen Sie aus den Einträgen unter __collection1__ __Abfrage__ aus. Verwenden Sie die folgenden Werte zum Auffüllen der Suchseite:

	* Geben Sie im Textfeld **q** Folgendes ein: ***:***. Dadurch werden alle Dokumente zurückgegeben, die in Solr indiziert sind. Wenn Sie nach einer bestimmten Zeichenfolge innerhalb der Dokumente suchen möchten, können diese Zeichenfolge hier eingeben.

	* Wählen Sie im Textfeld **wt** das Ausgabeformat aus. Der Standardwert ist **json**.

	Klicken Sie abschließend am unteren Rand der Suchseite auf die Schaltfläche **Abfrage ausführen**.

	![Anpassen eines Clusters mit "Skriptaktion"](./media/hdinsight-hadoop-solr-install-linux/hdi-solr-dashboard-query.png)

	Die Ausgabe gibt die beiden Dokumente zurück, die wir zur Indizierung von Solr verwendet haben. Die Ausgabe sieht ungefähr so aus:

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }

### Starten und Anhalten von Solr

Wenn Sie Solr manuell beenden oder starten müssen, verwenden Sie die folgenden Befehle:

	sudo stop solr

	sudo start solr

## Sichern indizierter Daten

Bewährt hat sich auch das Sichern der indizierten Daten auf den Solr-Clusterknoten in Azure-Blobspeicher. Führen Sie dazu die folgenden Schritte aus:

1. Stellen Sie über SSH eine Verbindung mit dem Cluster her, und verwenden Sie dann den folgenden Befehl, um den Hostnamen für den Hauptknoten zu erhalten.

        hostname -f
        
2. Erstellen Sie folgendermaßen eine Momentaufnahme der indizierten Daten. Ersetzen Sie __HOSTNAME__ mit dem aus dem vorherigen Befehl zurückgegebenen Namen:

		curl http://HOSTNAME:8983/solr/replication?command=backup

	Sie sollte eine Rückgabe wie diese erhalten:

		<?xml version="1.0" encoding="UTF-8"?>
		<response>
		  <lst name="responseHeader">
		    <int name="status">0</int>
		    <int name="QTime">9</int>
		  </lst>
		  <str name="status">OK</str>
		</response>

2. Als Nächstes wechseln Sie zum Verzeichnis __/usr/hdp/current/solr/example/solr__. Dort ist ein Unterverzeichnis für jede Sammlung vorhanden. Jedes Sammlungsverzeichnis enthält ein __Datenverzeichnis__, in dem sich die Momentaufnahme für diese Sammlung befindet.

	Wenn Sie beispielsweise die Schritte oben zum Indizieren der Beispieldokumente verwendet haben, sollte das Verzeichnis __/usr/hdp/current/solr/example/solr/collection1/data__ jetzt ein Verzeichnis namens __snapshot.###########__ enthalten, in denen die „#“-Zeichen das Datum und die Uhrzeit der Momentaufnahme darstellen.

3. Erstellen Sie ein komprimiertes Archiv des Momentaufnahmenordnders, indem Sie einen Befehl ähnlich dem folgenden verwenden:

		tar -zcf snapshot.20150806185338855.tgz snapshot.20150806185338855

	Dadurch entsteht ein neues Archiv mit dem Namen __snapshot.20150806185338855.tgz__, das den Inhalt des Verzeichnisses __snapshot.20150806185338855__ enthält.

3. Sie können das Archiv dann im primären Speicher des Clusters mit dem folgenden Befehl speichern:

	hadoop fs -copyFromLocal snapshot.20150806185338855.tgz /example/data

	> [AZURE.NOTE] Möglicherweise möchten Sie ein dediziertes Verzeichnis für Solr-Momentaufnahmen erstellen. Beispiel: `hadoop fs -mkdir /solrbackup`.

Weitere Informationen zum Arbeiten mit Solr-Backups und -Wiederherstellungen finden Sie unter [Making and restoring backups of SolrCores](https://cwiki.apache.org/confluence/display/solr/Making+and+Restoring+Backups+of+SolrCores) (auf Englisch).


## Weitere Informationen

- [Installieren und Verwenden von Hue in HDInsight-Clustern](hdinsight-hadoop-hue-linux.md) Hue ist eine Webbenutzeroberfläche, die das Erstellen, Ausführen und Speichern von Pig- und Hive-Aufträgen sowie das Durchsuchen des Standardspeichers für Ihre HDInsight-Cluster vereinfacht.

- [Installieren und Verwenden von Spark in HDInsight-Clustern][hdinsight-install-spark]. Verwenden Sie die Clusteranpassung, um Spark in HDInsight Hadoop-Clustern zu installieren. Spark ist ein Open-Source-Framework für die Parallelverarbeitung, das die arbeitsspeicherinterne Verarbeitung unterstützt, um die Leistung von Anwendungen zur Analyse von Big Data zu steigern.

- [Installieren und Verwenden von R in HDInsight-Clustern][hdinsight-install-r]. Verwenden Sie die Clusteranpassung, um R in HDInsight Hadoop-Clustern zu installieren. R ist eine Open-Source-Sprache und -Umgebung für statistische Berechnungen. Sie bietet Hunderte integrierter Statistikfunktionen und eine eigene Programmiersprache, die Aspekte der funktionalen und objektorientierten Programmierung kombiniert. Darüber hinaus werden umfangreiche Grafikfunktionen geboten.

- [Installieren von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install-linux.md). Verwenden Sie die Clusteranpassung, um Giraph in HDInsight Hadoop-Clustern zu installieren. Giraph ermöglicht Ihnen, mithilfe von Hadoop Graphen zu verarbeiten, und kann mit Azure HDInsight eingesetzt werden.

- [Installieren von Hue in HDInsight-Clustern](hdinsight-hadoop-hue-linux.md) Verwenden Sie die Clusteranpassung, um Hue in HDInsight Hadoop-Clustern zu installieren. Bei Hue handelt es sich um einen Satz von Webanwendungen zur Interaktion mit einem Hadoop-Cluster.



[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

<!---HONumber=AcomDC_0211_2016-->