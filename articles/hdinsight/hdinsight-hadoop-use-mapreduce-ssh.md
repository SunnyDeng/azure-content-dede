<properties
   pageTitle="MapReduce und SSH-Verbindung mit Hadoop in HDInsight | Microsoft Azure"
   description="Erfahren Sie, wie MapReduce-Aufträge mithilfe von SSH mit Hadoop in HDInsight ausgeführt werden."
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
   ms.date="11/06/2015"
   ms.author="larryfr"/>

# Verwenden von MapReduce mit Hadoop in HDInsight über SSH

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

In diesem Artikel erfahren Sie, wie Sie mit Secure Shell (SSH) eine Verbindung mit einem Hadoop-Cluster in HDInsight herstellen und dann MapReduce-Aufträge mithilfe von Hadoop-Befehlen übermitteln.

> [AZURE.NOTE]Wenn Sie bereits mit der Verwendung von Linux-basierten Hadoop-Servern vertraut sind, Ihnen HDInsight jedoch neu ist, finden Sie weitere Informationen unter [Tipps zu Linux-basiertem HDInsight](hdinsight-hadoop-linux-information.md).

##<a id="prereq"></a>Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Einen Linux-basierten HDInsight-Cluster (Hadoop in HDInsight)

* Einen SSH-Client Die Betriebssysteme Linux, Unix und Mac sollten über einen SSH-Client verfügen. Windows-Benutzer müssen einen Client herunterladen, z. B [PuTTY](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html).

##<a id="ssh"></a>Verbinden mit SSH

Stellen Sie mithilfe des SSH-Befehls eine Verbindung zum vollqualifizierten Domänennamen (FQDN) des HDInsight-Clusters her. Der FQDN entspricht dem Namen, den Sie dem Cluster zugewiesen haben, gefolgt von **.azurehdinsight.net**. Mithilfe des Folgenden wird z. B. eine Verbindung zu einem Cluster namens **myhdinsight** hergestellt:

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Wenn Sie beim Erstellen des HDInsight-Clusters einen Zertifikatschlüssel für die SSH-Authentifizierung** angegeben haben, müssen Sie möglicherweise den Speicherort des privaten Schlüssels im Clientsystem angeben. Beispiel:

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Wenn Sie beim Erstellen des HDInsight-Clusters ein Kennwort für die SSH-Authentifizierung** angegeben haben, müssen Sie nach der entsprechenden Aufforderung das Kennwort angeben.

Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, OS X und Unix](hdinsight-hadoop-linux-use-ssh-unix.md).

###PuTTY (Windows-Clients)

Windows bietet keinen integrierten SSH-Client. Wir empfehlen die Verwendung von **PuTTY**. Sie können das Programm unter [http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html](http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html) herunterladen.

Weitere Informationen zum Verwenden von PuTTY finden Sie unter [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

##<a id="hadoop"></a>Verwenden von Hadoop-Befehlen

1. Nachdem die Verbindung mit dem HDInsight-Cluster hergestellt wurde, verwenden Sie den folgenden **Hadoop**-Befehl, um einen MapReduce-Auftrag zu starten:

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	Dadurch wird die Klasse **wordcount** gestartet, die in der Datei **hadoop-mapreduce-examples.jar** enthalten ist. Als Eingabe wird das Dokument ****wasb://example/data/gutenberg/davinci.txt** verwendet, und die Ausgabe wird unter ****wasb:///example/data/WordCountOutput** gespeichert.

	> [AZURE.NOTE]Weitere Informationen über diesen MapReduce-Auftrag und die Beispieldaten finden Sie unter [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md).

2. Der Auftrag gibt während der Verarbeitung Details aus und gibt ähnliche Informationen wie die folgenden zurück, wenn der Auftrag abgeschlossen ist:

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. Sobald der Auftrag abgeschlossen ist, verwenden Sie den folgenden Befehl zum Auflisten der Ausgabedateien, die unter ****wasb://example/data/WordCountOutput** gespeichert sind:

		hdfs dfs -ls wasb:///example/data/WordCountOutput

	Daraufhin sollten zwei Dateien angezeigt werden: **\_SUCCESS** und **part-r-00000**. Die Datei **part-r-00000** enthält die Ausgabe für diesen Auftrag.

	> [AZURE.NOTE]Einige MapReduce-Aufträge teilen die Ergebnisse möglicherweise auf mehrere **part-r-#####**-Dateien auf. Verwenden Sie in diesem Fall das Suffix "#####", um die Reihenfolge der Dateien anzugeben.

4. Verwenden Sie den folgenden Befehl, um die Ausgabe anzuzeigen:

		hdfs dfs -cat wasb:///example/data/WordCountOutput/part-r-00000

	Dadurch wird eine Liste der Wörter, die in der Datei ****wasb://example/data/gutenberg/davinci.txt** enthalten sind, sowie die Anzahl der Vorkommen der einzelnen Wörter angezeigt. Im Folgenden finden Sie ein Beispiel für die in der Datei enthaltenen Daten:

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

##<a id="summary"></a>Zusammenfassung

Wie Sie sehen können, bieten Hadoop-Befehle eine einfache Möglichkeit zum Ausführen von MapReduce-Aufträgen in einem HDInsight-Cluster und dem anschließenden Anzeigen der Auftragsausgabe.

##<a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu MapReduce-Aufträgen in HDInsight:

* [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md)

* [Verwenden von Pig mit Hadoop in HDInsight](hdinsight-use-pig.md)

<!---HONumber=Nov15_HO3-->