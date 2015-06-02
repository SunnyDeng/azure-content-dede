<properties
   pageTitle="MapReduce und Remote Desktop mit Hadoop in HDInsight | Microsoft Azure"
   description="Erfahren Sie, wie Sie mithilfe des Remotedesktops eine Verbindung mit Hadoop in HDInsight herstellen und MapReduce-Aufträge ausführen."
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
   ms.date="02/18/2015"
   ms.author="larryfr"/>

# Verwenden von MapReduce in Hadoop in HDInsight über den Remotedesktop

[AZURE.INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

In diesem Artikel erfahren Sie, wie Sie mithilfe des Remotedesktops eine Verbindung mit einem Hadoop-Cluster in HDInsight herstellen und dann MapReduce-Aufträge mit dem Hadoop-Befehl ausführen.

##<a id="prereq"></a>Voraussetzungen

Um die in diesem Artikel aufgeführten Schritte auszuführen, benötigen Sie Folgendes:

* Einen Windows-basierten HDInsight-Cluster (Hadoop in HDInsight)

* Einen Clientcomputer mit einem der Betriebssysteme Windows 10, Windows 8 oder Windows 7

##<a id="connect"></a>Verbinden mit dem Remotedesktop

Aktivieren Sie Remotedesktop für den HDInsight-Cluster, und stellen Sie anschließend mithilfe der Anweisungen unter <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Herstellen einer Verbindung zu HDInsight-Clustern mit RDP</a> eine Verbindung her.

##<a id="hadoop"></a>Verwenden des Hadoop-Befehls

Wenn Sie mit dem Desktop des HDInsight-Clusters verbunden sind, gehen Sie wie folgt vor, um einen MapReduce-Auftrag mithilfe des Hadoop-Befehls auszuführen:

1. Starten Sie auf dem HDInsight-Desktop die **Hadoop-Befehlszeile**. Dadurch wird eine neue Eingabeaufforderung im Verzeichnis **c:\\apps\\dist\\hadoop-&lt;Versionsnummer>** geöffnet.

	> [AZURE.NOTE]Die Versionsnummer wechselt, wenn Hadoop aktualisiert wird. Die Umgebungsvariable **HADOOP_HOME** kann für die Suche nach dem Pfad verwendet werden. Mithilfe von `cd %HADOOP_HOME%` können Sie beispielsweise in das Hadoop-Verzeichnis wechseln, ohne die Versionsnummer von Hadoop kennen zu müssen.

2. Verwenden Sie den folgenden Befehl, um mit dem **Hadoop**-Befehl einen MapReduce-Auftrag auszuführen:

		hadoop jar hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	Dadurch wird die **wordcount**-Klasse gestartet, die in der Datei **hadoop-mapreduce-examples.jar** im aktuellen Verzeichnis enthalten ist. Als Eingabe wird das Dokument **wasb://example/data/gutenberg/davinci.txt** verwendet, und die Ausgabe wird unter **wasb:///example/data/WordCountOutput** gespeichert.

	> [AZURE.NOTE]Weitere Informationen über diesen MapReduce-Auftrag und die Beispieldaten finden Sie unter <a href="hdinsight-use-mapreduce.md">Verwenden von MapReduce in HDInsight Hadoop</a>.

2. Während der Verarbeitung gibt der Auftrag Details aus, und nach Abschluss des Auftrags werden Informationen ähnlich den folgenden zurückgegeben:

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. Nach Abschluss des Auftrags verwenden Sie den folgenden Befehl, um die Ausgabedateien aufzulisten, die unter **wasb://example/data/WordCountOutput** gespeichert sind:

		hadoop fs -ls wasb:///example/data/WordCountOutput

	Daraufhin sollten zwei Dateien angezeigt werden, **_SUCCESS** und **part-r-00000**. Die Datei **part-r-00000** enthält die Ausgabe für diesen Auftrag.

	> [AZURE.NOTE]Einige MapReduce-Aufträge teilen die Ergebnisse möglicherweise auf mehrere **part-r-#####**-Dateien auf. Verwenden Sie in diesem Fall das Suffix "#####", um die Reihenfolge der Dateien anzugeben.

4. Verwenden Sie den folgenden Befehl, um die Ausgabe anzuzeigen:

		hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

	Dadurch wird eine Liste der in der Datei **wasb://example/data/gutenberg/davinci.txt** enthaltenen Wörter und der Auftrittshäufigkeit jedes Worts angezeigt. Im Folgenden finden Sie ein Beispiel für die in der Datei enthaltenen Daten:

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

##<a id="summary"></a>Zusammenfassung

Wie Sie sehen können, bietet der Hadoop-Befehl eine einfache Möglichkeit zum Ausführen von MapReduce-Aufträgen auf einen HDInsight-Cluster und dem anschließenden Anzeigen der Auftragsausgabe.

##<a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu MapReduce-Aufträgen in HDInsight:

* [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md)

* [Verwenden von Pig mit Hadoop in HDInsight](hdinsight-use-pig.md)

<!--HONumber=54-->