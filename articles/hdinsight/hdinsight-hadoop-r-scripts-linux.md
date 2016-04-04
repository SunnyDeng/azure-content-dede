<properties
	pageTitle="Installieren von R in Linux-basiertem HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Sie R zum Anpassen Linux-basierter Hadoop-Cluster installieren und verwenden können."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="larryfr"/>

# Installieren und Verwenden von R in HDInsight Hadoop-Clustern

Sie können R in einem beliebigen Clustertyp in Hadoop in der HDInsight-Umgebung mithilfe der Clusteranpassung über **Skriptaktion** installieren. Dies ermöglicht Big Data-Experten und -Analysten die Nutzung von R zum Bereitstellen des leistungsstarken MapReduce/YARN-Programmierungsframeworks für die Verarbeitung großer Datenmengen in Hadoop-Clustern, die in HDInsight bereitgestellt sind.

> [AZURE.NOTE] Die Schritte in diesem Dokument erfordern einen Linux-basierten HDInsight-Cluster. Informationen zur Verwendung von R mit einem Windows-basierten Cluster finden Sie unter [Installieren und Verwenden von R HDinsight Hadoop-Clustern (Windows)](hdinsight-hadoop-r-scripts.md).

## Was ist R?

Das <a href="http://www.r-project.org/" target="_blank">R Project for Statistical Computing</a> ist eine Open-Source-Sprache und -Umgebung für statistische Berechnungen. R bietet Hunderte integrierter Statistikfunktionen und eine eigene Programmiersprache, die Aspekte der funktionalen und objektorientierten Programmierung kombiniert. Darüber hinaus werden umfangreiche Grafikfunktionen geboten. R ist in einer Vielzahl von Feldern für die meisten professionellen Statistiker und Analysten die bevorzugte Programmierungsumgebung.

R-Skripts können in Hadoop-Clustern in HDInsight ausgeführt werden, die mithilfe der Option "Skriptaktion" angepasst wurden, als die R-Umgebung installiert wurde. R ist mit Azure-Blobspeicher (WASB) kompatibel, weshalb darin gespeicherte Daten mit R in HDInsight verarbeitet werden können.

## Funktion des Skripts

Die Skriptaktion zur Installation von R in einem HDInsight-Cluster installiert die folgenden Ubuntu-Pakete, die eine einfache Installation von R bereitstellen:

* [r-base](http://packages.ubuntu.com/precise/r-base): GNU R-Basispaket
* [r-base-dev](http://packages.ubuntu.com/precise/r-base-dev): GNU R-Hilfspaket

Folgende RHadoop-Pakete, die für die Integration von MapReduce und HDFS sorgen, werden ebenfalls installiert:

* [rmr2](https://github.com/RevolutionAnalytics/rmr2): Ermöglicht R-Entwicklern die Verwendung von Hadoop MapReduce.
* [rhdfs](https://github.com/RevolutionAnalytics/rhdfs): Ermöglicht R-Entwicklern die Verwendung von Hadoop HDFS (WASB für HDInsight).

Darüber hinaus werden die folgenden R-Pakete installiert:

| R-Paket | Inhalt |
| --------- | ---------------- |
| [rJava](https://cran.r-project.org/web/packages/rJava/index.html) | Low-Level-Schnittstelle R zu Java |
| [Rcpp](https://cran.r-project.org/web/packages/Rcpp/index.html) | Integration von R und C++ |
| [RJSONIO](https://cran.r-project.org/web/packages/RJSONIO/index.html) | Serialisieren/Deserialisieren von R-Objekten in JSON |
| [bitops](https://cran.r-project.org/web/packages/bitops/index.html) | Funktionen für bitweise Vorgänge für ganzzahlige Vektoren |
| [digest](https://cran.r-project.org/web/packages/digest/index.html) | Erstellen kryptografischer Hash-Digests von R-Objekten |
| [functional](https://cran.r-project.org/web/packages/functional/index.html) | Curry, Verfassen und andere Funktionen höherer Ordnung |
| [reshape2](https://cran.r-project.org/web/packages/reshape2/index.html) | Flexible Neustrukturierung und Aggregation von Daten |
| [stringr](https://cran.r-project.org/web/packages/stringr/index.html) | Einfache, konsistente Wrapper für allgemeine Zeichenfolgenvorgänge |
| [plyr](https://cran.r-project.org/web/packages/plyr/index.html) | Tools zum Aufteilen, Anwenden und Kombinieren von Daten |
| [caTools](https://cran.r-project.org/web/packages/caTools/index.html) | Tools zum Verschieben von Fenstern, GIF, Base64, ROC AUC usw. |
| [stringdist](https://cran.r-project.org/web/packages/stringdist/index.html) | Funktionen für ungefähren Zeichenfolgenabgleich und -abstand |

## Installation von R mithilfe von Skriptaktionen

Die folgende Skriptaktion dient zum Installieren von R in einem HDInsight-Cluster. https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
    
Dieser Abschnitt enthält Anweisungen zur Verwendung des Skripts während der Erstellung eines neuen Clusters mithilfe des Azure-Portals.

> [AZURE.NOTE] Zum Anwenden von Skriptaktionen können auch Azure PowerShell, das HDInsight .NET SDK oder Azure Resource Manager-Vorlagen verwendet werden. Sie können auch Skriptaktionen auf bereits ausgeführte Cluster anwenden. Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

1. Beginnen Sie die Bereitstellung eines Clusters anhand der Schritte in [Bereitstellen Linux-basierter HDInsight-Cluster](hdinsight-hadoop-provision-linux-clusters.md#portal), schließen Sie sie jedoch nicht ab.

2. Wählen Sie auf dem Blatt **Optionale Konfiguration** die Option **Skriptaktionen**, und geben Sie die folgenden Informationen an:

	* __NAME__: Geben Sie einen Anzeigenamen für die Skriptaktion ein.
	* __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxrconfigactionv01/r-installer-v01.sh
	* __HEAD__: Aktivieren Sie diese Option.
	* __WORKER__: Aktivieren Sie diese Option.
	* __ZOOKEEPER__: Aktivieren Sie diese Option für die Installation auf dem Zookeeper-Knoten.
	* __PARAMETERS__: Lassen Sie dieses Feld leer.

3. Verwenden Sie am unteren Rand der **Skriptaktionen** die Schaltfläche **Auswählen**, um die Konfiguration zu speichern. Verwenden Sie schließlich die Schaltfläche **Auswählen** am unteren Rand des Blatts **Optionale Konfiguration**, um die optionalen Konfigurationsinformationen zu speichern.

4. Setzen Sie die Bereitstellung des Clusters wie unter [Bereitstellen Linux-basierter HDInsight-Cluster](hdinsight-hadoop-provision-linux-clusters.md#portal) beschrieben fort.

## Ausführen von R-Skripts

Nach Abschluss der Clusterbereitstellung führen Sie anhand der folgenden Schritte mithilfe von R einen MapReduce-Vorgang im Cluster aus.

1. Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net

	Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie in den folgenden Artikeln:

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. Geben Sie an der `username@hn0-CLUSTERNAME:~$`-Aufforderung den folgenden Befehl ein, um eine interaktive R-Sitzung zu starten:

		R

3. Geben Sie das folgende R-Programm ein. Dadurch werden die Zahlen 1 bis 100 erzeugt und dann mit 2 multipliziert.

		library(rmr2)
		ints = to.dfs(1:100)
		calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))

	Die erste Zeile ruft die RHadoop-Bibliothek „rmr2“ auf, die für MapReduce-Vorgänge verwendet wird.

	Die zweite Zeile generiert die Werte 1 bis 100 und speichert sie mithilfe von `to.dfs` im Hadoop-Dateisystem.

	Die dritte Zeile erstellt einen MapReduce-Prozess mit rmr2-Funktionen und beginnt mit der Verarbeitung. Nach Beginn der Verarbeitung sollten mehrere Zeilen über den Bildschirm ablaufen.

4. Als Nächstes verwenden Sie den folgenden Befehl, um temporären Pfad anzuzeigen, unter dem die MapReduce-Ausgabe gespeichert wurde:

		print(calc())

	Der Pfad sollte etwa wie folgt aussehen: `/tmp/file5f615d870ad2`. Verwenden Sie den folgenden Befehl, um die tatsächliche Ausgabe anzuzeigen:

		print(from.dfs(calc))

	Die Ausgabe sollte wie folgt aussehen:

		[1,]  1 2
		[2,]  2 4
		.
		.
		.
		[98,]  98 196
		[99,]  99 198
		[100,] 100 200

5. Geben Sie zum Beenden von R Folgendes ein:

		q()


## Nächste Schritte

- [Installieren und Verwenden von Hue in HDInsight-Clustern](hdinsight-hadoop-hue-linux.md) Hue ist eine Webbenutzeroberfläche, die das Erstellen, Ausführen und Speichern von Pig- und Hive-Aufträgen sowie das Durchsuchen des Standardspeichers für Ihre HDInsight-Cluster vereinfacht.

- [Installieren von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install.md). Verwenden Sie die Clusteranpassung, um Giraph in HDInsight Hadoop-Clustern zu installieren. Giraph ermöglicht Ihnen, mithilfe von Hadoop Graphverarbeitungen durchzuführen. Es kann zudem mit Azure HDInsight eingesetzt werden.

- [Installieren von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install.md). Verwenden Sie die Clusteranpassung, um Solr in HDInsight Hadoop-Clustern zu installieren. Solr ermöglicht Ihnen, leistungsstarke Suchvorgänge auf gespeicherte Daten anzuwenden.

- [Installieren von Hue in HDInsight-Clustern](hdinsight-hadoop-hue-linux.md) Verwenden Sie die Clusteranpassung, um Hue in HDInsight Hadoop-Clustern zu installieren. Bei Hue handelt es sich um einen Satz von Webanwendungen zur Interaktion mit einem Hadoop-Cluster.

[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md

<!---HONumber=AcomDC_0323_2016-->