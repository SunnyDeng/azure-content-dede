<properties
	pageTitle="Verwenden von Skriptaktionen zum Installieren von Solr in einem Hadoop-Cluster | Microsoft Azure"
	description="Erfahren Sie, wie Sie HDInsight-Cluster mit Solr anpassen. Sie verwenden eine Script Action-Konfigurationsoption, um mithilfe eines Skripts Solr zu installieren."
	services="hdinsight"
	documentationCenter=""
	authors="nitinme"
	manager="paulettm"
	editor="cgronlun"
	tags="azure-portal"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/07/2015"
	ms.author="nitinme"/>

# Installieren und Verwenden von Solr in HDInsight Hadoop-Clustern

Sie können Solr in einem beliebigen Clustertyp in Hadoop in Azure HDInsight mithilfe einer Clusteranpassung vom Typ **Skriptaktion** installieren. Mit "Skriptaktion" können Sie nur beim Erstellen eines Clusters Skripts ausführen, um ein Cluster anzupassen. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mithilfe von Skriptaktionen][hdinsight-cluster-customize].

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Installieren von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install.md)

In diesem Thema wird beschrieben, wie Sie Solr mithilfe der Funktion "Skriptaktion" installieren. Solr ist eine leistungsfähige Suchplattform und bietet Suchfunktionen der Unternehmensklasse für von Hadoop verwaltete Daten. Sobald Sie Solr in einem HDInsight-Cluster installiert haben, erfahren Sie auch, wie Sie Daten mithilfe von Solr suchen können.

> [AZURE.NOTE]Das in diesem Thema verwendete Beispielskript erstellt einen Windows-basierten Solr-Cluster mit einer speziellen Konfiguration. Wenn Sie den Solr-Cluster mit anderen Auflistungen, Shards, Schemas, Replikaten usw. konfigurieren möchten, müssen Sie das Skript und die Solr-Binärdateien entsprechend ändern.
>
> Informationen zur Verwendung von Solr mit einem Linux-basierten Cluster finden Sie unter [Installieren und Verwenden von R in HDinsight Hadoop-Clustern (Linux)](hdinsight-hadoop-solr-install-linux.md).

## <a name="whatis"></a>Was ist Solr?

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> ist eine Unternehmensplattform für die leistungsstarke Volltextsuche in Daten. Während Hadoop das Speichern und Verwalten von großen Datenmengen ermöglicht, bietet Apache Solr die Suchfunktionen, um schnell Daten abzurufen. In diesem Thema wird beschrieben, wie Sie ein HDInsight-Cluster zum Installieren von Solr anpassen können.

## <a name="install"></a>Wie installiere ich Solr?

Ein Beispielskript zum Installieren von Solr in einem HDInsight-Cluster steht in einem schreibgeschützten Azure-Speicherblob unter [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1) zur Verfügung. Dieser Abschnitt enthält Anweisungen zur Verwendung des Beispielskripts während der Bereitstellung des Clusters mithilfe des Azure-Portals.


> [AZURE.NOTE]Das Beispielskript funktioniert nur mit HDInsight-Clustern der Version 3.1. Weitere Informationen zu HDInsight-Clusterversionen finden Sie unter [HDInsight-Clusterversionen](hdinsight-component-versioning.md).


1. Beginnen Sie die Bereitstellung eines Clusters mit der Option **BENUTZERDEFINIERT ERSTELLEN**, wie unter [Bereitstellen eines Clusters mit der benutzerdefinierten Erstellungsoption](hdinsight-provision-clusters.md#portal) beschrieben.
2. Klicken Sie auf der Seite **Skriptaktionen** des Assistenten auf **Skriptaktion hinzufügen**, um wie unten gezeigt Details zur Skriptaktion bereitzustellen:

	![Anpassen eines Clusters mit "Skriptaktion"](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Anpassen eines Clusters mit "Skriptaktion"")

	<table border='1'>
	<tr><th>Eigenschaft</th><th>Wert</th></tr>
	<tr><td>Name</td>
		<td>Geben Sie einen Namen für die Skriptaktion an. Beispiel: <b>Solr installieren</b>.</td></tr>
	<tr><td>Skript-URI</td>
		<td>Geben Sie den Uniform Resource Identifier (URI) für das Skript an, das aufgerufen wird, um den Cluster anzupassen. Beispiel: <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
	<tr><td>Knotentyp</td>
		<td>Gibt die Knoten an, auf denen das Anpassungsskript ausgeführt wird. Sie können <b>Alle Knoten</b>, <b>Nur Hauptknoten</b> oder <b>Nur Workerknoten</b> auswählen.
	<tr><td>Parameter</td>
		<td>Geben Sie die Parameter an, wenn dies für das Skript erforderlich ist. Für das Skript zum Installieren von Solr sind keine Parameter erforderlich, sodass Sie dieses Feld leer lassen können.</td></tr>
</table>Sie können dem Cluster mehr als eine Skriptaktion zum Installieren von mehreren Komponenten hinzufügen. Nachdem Sie die Skripts hinzugefügt haben, klicken Sie auf das Häkchen, um die Bereitstellung des Clusters zu starten.


## <a name="usesolr"></a>Wie verwende ich Solr in HDInsight?

Sie müssen mit der Indizierung von Solr mit einigen Datendateien beginnen. Sie können dann Solr verwenden, um Suchabfragen der indizierten Daten auszuführen. Führen Sie die folgenden Schritte aus, um Solr in einem HDInsight-Cluster zu verwenden:

1. **Verwenden Sie das Remotedesktopprotokoll (RDP) zum Herstellen einer Remoteverbindung mit dem HDInsight-Cluster mit installiertem Solr**. Aktivieren Sie im Azure-Portal Remotedesktop für den Cluster, den Sie mit installiertem Solr erstellt haben, und stellen Sie dann eine Remoteverbindung mit dem Cluster her. Anweisungen hierzu finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Herstellen einer Verbindung mit HDInsight-Clustern mit RDP</a>.

2. **Indizieren Sie Solr durch Hochladen von Datendateien**. Wenn Sie Solr indizieren, legen Sie Dokumente ab, die Sie ggf. durchsuchen müssen. Stellen Sie zum Indizieren von Solr mittels RDP eine Verbindung mit dem Cluster her, navigieren Sie zum Desktop, öffnen Sie die Hadoop-Befehlszeile, und navigieren Sie zu **C:\\apps\\dist\\solr-4.7.2\\example\\exampledocs**. Führen Sie den folgenden Befehl aus:

		java -jar post.jar solr.xml monitor.xml

	In der Konsole wird die folgende Ausgabe angezeigt:

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	Das Hilfsprogramm "post.jar" indiziert Solr mit zwei Beispieldokumenten: **solr.xml** und **monitor.xml**. Das Dienstprogramm "post.jar" und die Beispieldokumente stehen in der Solr-Installation zur Verfügung.

3. **Verwenden Sie das Solr-Dashboard, um die indizierten Dokumente zu durchsuchen**. Öffnen Sie in der RDP-Sitzung mit dem HDInsight-Cluster Internet Explorer, und starten Sie das Solr-Dashboard unter ****http://headnodehost:8983/solr/#/**. Wählen Sie im linken Bereich in der Dropdownliste **Core Selector** den Eintrag **collection1** aus, und klicken Sie dann auf **Query**. Geben Sie beispielsweise die folgenden Werte an, um alle Dokumente in Solr auszuwählen und zurückzugeben.
	1. Geben Sie im Textfeld **q** Folgendes ein: ***:***. Dadurch werden alle Dokumente zurückgegeben, die in Solr indiziert sind. Wenn Sie nach einer bestimmten Zeichenfolge innerhalb der Dokumente suchen möchten, können diese Zeichenfolge hier eingeben.
	2. Wählen Sie im Textfeld **wt** das Ausgabeformat aus. Der Standardwert ist **json**. Klicken Sie auf **Execute Query**.

		![Anpassen eines Clusters mit "Skriptaktion"](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Ausführen einer Abfrage im Solr-Dashboard")

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


4. **Empfehlung: Sichern Sie die indizierten Daten aus Solr in Azure-Blobspeicher, der dem HDInsight-Cluster zugeordnet ist**. Bewährt hat sich auch das Sichern der indizierten Daten auf den Solr-Clusterknoten in Azure-Blobspeicher. Führen Sie dazu die folgenden Schritte aus:

	1. Öffnen Sie aus der RDP-Sitzung den Internet Explorer, und verweisen Sie auf die folgende URL:

			http://localhost:8983/solr/replication?command=backup

		Sie sollte eine Rückgabe wie diese erhalten:

			<?xml version="1.0" encoding="UTF-8"?>
			<response>
			  <lst name="responseHeader">
			    <int name="status">0</int>
			    <int name="QTime">9</int>
			  </lst>
			  <str name="status">OK</str>
			</response>

	2. Navigieren Sie in der Remotesitzung zu "{SOLR\_HOME}\\{Collection}\\data". Für den mit dem Beispielskript erstellten Cluster ist dies **C:\\apps\\dist\\solr-4.7.2\\example\\solr\\collection1\\data**. An diesem Speicherort sollte ein Momentaufnahmenordner mit einem Namen wie **snapshot.*Zeitstempel*** erstellt werden.

	3. Komprimieren Sie den Ordner für Momentaufnahmen im ZIP.-Format, und laden Sie ihn in Azure-Blobspeicher hoch. Navigieren Sie über die Hadoop-Befehlszeile zum Verzeichnis des Momentaufnahmenordners mithilfe des folgenden Befehls:

			  hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

		Mit diesem Befehl wird die Momentaufnahme in "/example/data/" unter der Container im Standardspeicherkonto kopiert, das dem Cluster zugeordnet ist.


## Weitere Informationen##
- [Installieren und Verwenden von Spark in HDInsight-Clustern][hdinsight-install-spark]. Verwenden Sie die Clusteranpassung, um Spark in HDInsight Hadoop-Clustern zu installieren. Spark ist ein Open-Source-Framework für die Parallelverarbeitung, das die arbeitsspeicherinterne Verarbeitung unterstützt, um die Leistung von Anwendungen zur Analyse von Big Data zu steigern.
- [Installieren und Verwenden von R in HDInsight-Clustern][hdinsight-install-r]. Verwenden Sie die Clusteranpassung, um R in HDInsight Hadoop-Clustern zu installieren. R ist eine Open-Source-Sprache und -Umgebung für statistische Berechnungen. Sie bietet Hunderte integrierter Statistikfunktionen und eine eigene Programmiersprache, die Aspekte der funktionalen und objektorientierten Programmierung kombiniert. Darüber hinaus werden umfangreiche Grafikfunktionen geboten.
- [Installieren von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install.md). Verwenden Sie die Clusteranpassung, um Giraph in HDInsight Hadoop-Clustern zu installieren. Giraph ermöglicht Ihnen, mithilfe von Hadoop Graphen zu verarbeiten, und kann mit Azure HDInsight eingesetzt werden.



[powershell-install-configure]: ../install-configure-powershell.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md

<!---HONumber=Sept15_HO2-->