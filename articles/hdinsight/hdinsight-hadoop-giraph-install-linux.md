<properties
	pageTitle="Installieren und Verwenden von Giraph in Hadoop-Clustern in HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Sie HDInsight-Cluster mit Giraph anpassen. Sie verwenden die Konfigurationsoption ";Skriptaktion";, um Giraph mithilfe eines Skripts zu installieren."
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
	ms.date="08/18/2015"
	ms.author="larryfr"/>

# Installieren von Giraph in HDInsight Hadoop-Clustern und Verwenden von Giraph zur Verarbeitung großer Diagramme

Sie können Giraph in einem beliebigen Clustertyp in Hadoop in Azure HDInsight mithilfe der Clusteranpassung über **Skriptaktion** installieren. Mit "Skriptaktion" können Sie nur beim Erstellen eines Clusters Skripts ausführen, um ein Cluster anzupassen. Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

In diesem Thema wird beschrieben, wie Sie Giraph mithilfe der Funktion "Skriptaktion" installieren. Nach der Installation von Giraph erfahren Sie auch, wie Sie Giraph bei den typischsten Anwendungen verwenden können, d. h. bei der Verarbeitung großer Graphen.

> [AZURE.NOTE]Die Informationen in diesem Artikel gelten für Linux-basierte HDInsight-Cluster. Informationen zum Arbeiten mit Windows-basierten Clustern finden Sie unter [Installieren von Giraph in HDInsight Hadoop-Clustern (Windows)](hdinsight-hadoop-giraph-install.md).

## <a name="whatis"></a>Was ist Giraph?

[Apache Giraph](http://giraph.apache.org/) ermöglicht die Graphverarbeitung mit Hadoop und lässt sich mit Azure HDInsight nutzen. Graphen bilden Beziehungen zwischen Objekten ab, wie z. B. Verbindungen zwischen Routern in einem großen Netzwerk wie dem Internet oder Beziehungen zwischen Menschen in sozialen Netzwerken (mitunter als "Social Graph" bezeichnet). Mit der Graphverarbeitung können Sie sich Gedanken über die Beziehungen zwischen den Objekten im Diagramm machen wie etwa:

- Ermitteln potenzieller Freunde aufgrund Ihrer aktuellen Beziehungen.
- Ermitteln der kürzesten Route zwischen zwei Computern in einem Netzwerk.
- Berechnen des Seitenrangs von Webseiten.

##Funktion des Skripts

Dieses Skript führt folgende Aktionen aus:

* Installiert Giraph in `/usr/hdp/current/giraph`
* Kopiert die `giraph-examples.jar`-Datei in den Standardspeicher (WASB) für den Cluster: `/example/jars/giraph-examples.jar`

## <a name="install"></a>Installation von Giraph mithilfe von Skriptaktionen

Ein Beispielskript zum Installieren von Giraph in einem HDInsight-Cluster steht in einem schreibgeschützten Azure-Speicherblob unter [https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh) zur Verfügung. Dieser Abschnitt enthält Anweisungen zur Verwendung des Beispielskripts während der Bereitstellung des Clusters mithilfe des Azure-Portals.

> [AZURE.NOTE]Sie können auch Azure PowerShell oder das HDInsight .NET SDK zum Erstellen eines Clusters mit diesem Skript verwenden. Weitere Informationen zur Verwendung dieser Methoden finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

1. Beginnen Sie die Bereitstellung eines Clusters anhand der Schritte unter [Bereitstellen Linux-basierter HDInsight-Cluster](hdinsight-provision-linux-clusters.md#portal), schließen Sie sie jedoch nicht ab.

2. Wählen Sie auf dem Blatt **Optionale Konfiguration** die Option **Skriptaktionen**, und geben Sie die folgenden Informationen an:

	* __NAME__: Geben Sie einen Anzeigenamen für die Skriptaktion ein.
	* __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxgiraphconfigactionv01/giraph-installer-v01.sh
	* __HEAD__: Aktivieren Sie diese Option.
	* __WORKER__: Aktivieren Sie diese Option.
	* __ZOOKEEPER__: Aktivieren Sie diese Option für die Installation auf dem Zookeeper-Knoten.
	* __PARAMETERS__: Lassen Sie dieses Feld leer.

3. Verwenden Sie am unteren Rand der **Skriptaktionen** die Schaltfläche **Auswählen**, um die Konfiguration zu speichern. Verwenden Sie schließlich die Schaltfläche **Auswählen** am unteren Rand des Blatts **Optionale Konfiguration**, um die optionalen Konfigurationsinformationen zu speichern.

4. Setzen Sie die Bereitstellung des Clusters wie unter [Bereitstellen Linux-basierter HDInsight-Cluster](hdinsight-provision-linux-clusters.md#portal) beschrieben fort.

## <a name="usegiraph"></a>Wie verwende ich Giraph in HDInsight?

Sobald die Cluster-Bereitstellung abgeschlossen ist, gehen Sie folgendermaßen vor, um das in Giraph enthaltene Beispiel "SimpleShortestPathsComputation" auszuführen. Dadurch wird die grundlegende <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a>-Implementierung implementiert, um den kürzesten Pfad zwischen Objekten in einem Graph zu finden.

1. Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie in den folgenden Artikeln:

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

1. Verwenden Sie den folgenden Befehl, um eine neue Datei namens __tiny\_graph.txt__ zu erstellen:

		nano tiny_graph.txt

	Fügen Sie Folgendes als Inhalt der Datei hinzu:

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Diese Daten beschreiben eine Beziehung zwischen Objekten in einem gerichteten Graph mithilfe des Formats [source\_id, source\_value,[[dest\_id], [edge\_value],...]]. Jede Zeile repräsentiert eine Beziehung zwischen einem **source\\_id**-Objekt und einem oder mehreren **dest\\_id**-Objekten. Der **edge\\_value** (bzw. die Gewichtung) ist vorstellbar als die Stärke oder Distanz der Verbindung zwischen **source\_id** und **dest\\_id**.

	Wenn die obigen Daten auseinandergezogen und der Wert (die Gewichtung) als Abstand zwischen den Objekten verwendet werden, dann könnte das so aussehen:

	![tiny\_graph.txt als Kreise dargestellt mit Linien unterschiedlicher Länge dazwischen](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph.png)

2. Drücken Sie zum Speichern der Datei __STRG + X__, dann __Y__, und schließlich die __Eingabetaste__, um den Dateinamen zu akzeptieren.

3. Verwenden Sie zum Speichern der Daten im primären Speicher für den HDInsight-Cluster den folgenden Befehl:

		hadoop fs -copyFromLocal tiny_graph.txt /example/data/tiny_graph.txt

4. Führen Sie das Beispiel "SimpleShortstPathsComputation" mit dem folgenden Befehl aus:

		 hadoop jar /usr/hdp/current/giraph/giraph-examples.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca mapred.job.tracker=headnode0:9010 -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip /example/data/tiny_graph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op /example/output/shortestpaths -w 2

	Die mit diesem Befehl verwendeten Parameter werden in der folgenden Tabelle beschrieben.

	| Parameter | Funktionsbeschreibung |
	| --------- | ------------ |
	| `jar /usr/hdp/current/giraph/giraph-examples.jar` | Die JAR-Datei, die die Beispiele enthält |
	| `org.apache.giraph.GiraphRunner` | Die Klasse, die zum Starten der Beispiele verwendet wird. |
	| `org.apache.giraph.examples.SimpleShortestPathsCoputation` | Das Beispiel, das ausgeführt wird. In diesem Fall wird der kürzeste Pfad zwischen ID 1 und allen anderen IDs im Graph berechnet. |
	| `-ca mapred.job.tracker=headnode0:9010` | Der Hauptknoten für den Cluster. |
	| `-vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFromat` | Das Eingabeformat, das für die Eingabedaten verwendet wird. |
	| `-vip /example/data/tiny_graph.txt` | Die Eingabedatendatei. |
	| `-vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat` | Das Ausgabeformat. In diesem Fall die ID und der Wert als Nur-Text. |
	| `-op /example/output/shortestpaths` | Der Ausgabespeicherort. |
	| `-w 2` | Die Anzahl der zu verwendenden Worker. In diesem Fall 2. |

	Weitere Informationen zu diesen und anderen mit Giraph-Beispielen verwendeten Parametern finden Sie unter [Giraph Schnellstart](http://giraph.apache.org/quick_start.html).

5. Nach Abschluss des Auftrags werden die Ergebnisse im Verzeichnis "\_\___wasb:///example/out/shotestpaths__" gespeichert. Die erstellten Dateien beginnen mit __part-m-__ und enden mit einer Zahl, die die erste, zweite, usw. Datei anzeigt. Verwenden Sie den folgenden Befehl, um die Ausgabe anzuzeigen:

		hadoop fs -text /example/output/shortestpaths/*

	Die Ausgabe sollte in etwa folgendermaßen aussehen:

		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	Das Beispiel "SimpleShortestPathComputation" wurde hartcodiert und beginnt mit der Objekt-ID 1. Es findet den kürzesten Pfad zu anderen Objekten. Die Ausgabe sollte also `destination_id distance` lauten, wobei der Abstand der Wert (oder das Gewicht) der Kanten ist, der zwischen Objekt-ID 1 und der Ziel-ID zurückgelegt wird.

	Wenn Sie dies visualisieren, können Sie die Ergebnisse überprüfen, indem Sie den kürzesten Weg zwischen ID 1 und allen anderen Objekten zurücklegen. Beachten Sie, dass 5 der kürzeste Pfad zwischen ID 1 und ID 4 ist. Dies ist die gesamte Entfernung zwischen <span style="color:orange">ID 1 und 3</span> und dann zwischen <span style="color:red">ID 3 und 4</span>.

	![Zeichnen von Objekten als Kreise mit dem kürzesten Pfad dazwischen](./media/hdinsight-hadoop-giraph-install-linux/giraph-graph-out.png)


## Nächste Schritte

- [Installieren und Verwenden von Hue in HDInsight-Clustern](hdinsight-hadoop-hue-linux.md) Hue ist eine Webbenutzeroberfläche, die das Erstellen, Ausführen und Speichern von Pig- und Hive-Aufträgen sowie das Durchsuchen des Standardspeichers für Ihre HDInsight-Cluster vereinfacht.

- Unter [Installieren und Verwenden von Spark für HDInsight-Cluster](hdinsight-hadoop-spark-install-linux.md) finden Sie Anweisungen zum Verwenden der Clusteranpassung zum Installieren und Verwenden von Spark für HDInsight Hadoop-Cluster. Spark ist ein Open-Source-Framework für die Parallelverarbeitung, das die arbeitsspeicherinterne Verarbeitung unterstützt, um die Leistung von Anwendungen zur Analyse von Big Data zu steigern.

- Unter [Installieren und Verwenden von R für HDInsight-Cluster](hdinsight-hadoop-r-scripts-linux.md) finden Sie Anweisungen bezüglich der Clusteranpassung zum Installieren und Verwenden von R in HDInsight Hadoop-Clustern. R ist eine Open-Source-Sprache und -Umgebung für statistische Berechnungen. Sie bietet Hunderte integrierter Statistikfunktionen und eine eigene Programmiersprache, die Aspekte der funktionalen und objektorientierten Programmierung kombiniert. Darüber hinaus werden umfangreiche Grafikfunktionen geboten.

- [Installieren von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install-linux.md). Verwenden Sie die Clusteranpassung, um Solr in HDInsight Hadoop-Clustern zu installieren. Solr ermöglicht es Ihnen, leistungsstarke Suchvorgänge für gespeicherte Daten durchzuführen.

<!---HONumber=September15_HO1-->