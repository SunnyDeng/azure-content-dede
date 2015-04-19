<properties
   pageTitle="MapReduce mit Hadoop in HDInsight | Azure"
   description="Erfahren Sie, wie MapReduce-Aufträge mithilfe von SSH mit Hadoop in HDInsight ausgeführt werden."
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

# Verwenden von Hive mit Hadoop in HDInsight über SSH

[AZURE.INCLUDE [mapreduce-selector](../includes/hdinsight-selector-use-mapreduce.md)]

In diesem Artikel erfahren Sie, wie Sie mit SSH eine Verbindung zu einem Hadoop-Cluster in HDInsight herstellen und dann MapReduce-Aufträge mithilfe des Hadoop-Befehls übermitteln.

> [AZURE.NOTE] Wenn Sie bereits mit der Verwendung von Linux-basierten Hadoop-Servern vertraut sind, jedoch noch nicht mit HDInsight, finden Sie weitere Informationen unter <a href="../hdinsight-hadoop-linux-information/" target="_blank">Was Sie über Hadoop auf Linux-basiertem HDInsight wissen müssen</a>.

## <a id="prereq"></a>Voraussetzungen

Damit Sie die in dieser Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Einen Linux-basierten HDInsight-Cluster (Hadoop in HDInsight)

* Einen SSH-Client Linux, Unix und Mac OS sollten über einen SSH-Client verfügbar sein. Windows-Benutzer müssen einen Client herunterladen, z. B. <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">Putty</a>.

## <a id="ssh"></a>Verbinden mit SSH

Stellen Sie mithilfe des SSH-Befehls eine Verbindung zum vollqualifizierten Domänennamen (FQDN) des HDInsight-Clusters her. Der FQDN entspricht dem Namen, den Sie dem Cluster zugewiesen haben, gefolgt von **.azurehdinsight.net**. Mithilfe des Folgenden wird z. B. eine Verbindung zu einem Cluster namens **myhdinsight** hergestellt.

	ssh admin@myhdinsight-ssh.azurehdinsight.net

**Wenn Sie beim Erstellen des HDInsight-Clusters einen Zertifikatschlüssel für die SSH-Authentifizierung angegeben haben**, müssen Sie möglicherweise den Speicherort des privaten Schlüssel im Clientsystem angeben.

	ssh admin@myhdinsight-ssh.azurehdinsight.net -i ~/mykey.key

**Wenn Sie beim Erstellen des HDInsight-Clusters ein Kennwort für die SSH-Authentifizierung angegeben haben**, müssen Sie nach der entsprechenden Aufforderung das Kennwort angeben.

### PuTTY (Windows-Clients)

Windows bietet keinen integrierten SSH-Client. Es wird die Verwendung von **PuTTY** empfohlen, das Sie unter <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a> herunterladen können.

Weitere Informationen zum Verwenden von Putty finden Sie im Abschnitt **Verwenden von Putty für eine Verbindung mit einem Linux-Computer** unter <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Verwenden von SSH mit Linux auf Azure</a>.

> [AZURE.NOTE] Wenn Sie für die SSH-Authentifizierung für den HDInsight-Cluster ein Zertifikat verwendet haben, müssen Sie auch den Abschnitt **Erstellen eines PPK für Putty** unter <a href="http://azure.microsoft.com/documentation/articles/virtual-machines-linux-use-ssh-key/" target="_blank">Verwenden von SSH mit Linux auf Azure</a> lesen.

## <a id="hadoop"></a>Verwenden des Hadoop-Befehls

1. Nachdem die Verbindung mit dem HDInsight-Cluster hergestellt wurde, gehen Sie wie folgt vor, um einen MapReduce-Auftrag mithilfe des **Hadoop**-Befehls zu starten.

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount wasb:///example/data/gutenberg/davinci.txt wasb:///example/data/WordCountOutput

	Dadurch wird die Klasse **wordcount** gestartet, die in der Datei **hadoop-mapreduce-examples.jar** enthalten ist. Sie verwendet als Eingabe das Dokument **wasb://example/data/gutenberg/davinci.txt** und die Ausgabe wird im Verzeichnis **wasb:///example/data/WordCountOutput** gespeichert.

	> [AZURE.NOTE] Weitere Informationen über diesen MapReduce-Auftrag und die Beispieldaten finden Sie unter <a href="../hdinsight-use-mapreduce/" target="_blank">Verwenden von MapReduce in HDInsight Hadoop</a>.

2. Während der Verarbeitung gibt der Auftrag Details aus und gibt schließlich ähnliche Informationen wie die folgenden zurück, wenn der Auftrag abgeschlossen wird.

		File Input Format Counters
        Bytes Read=1395666
		File Output Format Counters
        Bytes Written=337623

3. Verwenden Sie nach Abschluss des Auftrags den folgenden Befehl zum Auflisten der Ausgabedateien, die in **wasb://example/data/WordCountOutput** gespeichert werden.

		hadoop fs -ls wasb:///example/data/WordCountOutput

	Daraufhin sollten zwei Dateien angezeigt werden, **_SUCCESS** und **part-r-00000**. Die Datei **part-r-00000** enthält die Ausgabe für diesen Auftrag.

	> [AZURE.NOTE] Einige MapReduce-Aufträge teilen die Ergebnisse möglicherweise auf mehrere **part-r-#####**-Dateien auf. Verwenden Sie in diesem Fall das Suffix "#####", um die Reihenfolge der Dateien anzugeben.

4. Verwenden Sie den folgenden Befehl, um die Ausgabe anzuzeigen.

		hadoop fs -cat wasb:///example/data/WordCountOutput/part-r-00000

	Dadurch wird eine Liste mit in der Datei **wasb://example/data/gutenberg/davinci.txt** enthaltenen Wörtern zusammen mit der Häufigkeit ihres jeweiligen Auftretens angezeigt.  Folgendes ist ein Beispiel für die in der Datei enthaltenen Daten.

		wreathed        3
		wreathing       1
		wreaths 		1
		wrecked 		3
		wrenching       1
		wretched        6
		wriggling       1

## <a id="summary"></a>Zusammenfassung

Wie Sie sehen können, bietet der Hadoop-Befehl eine einfache Möglichkeit zum Ausführen von MapReduce-Aufträgen auf einen HDInsight-Cluster und dem anschließenden Anzeigen der Auftragsausgabe.

## <a id="nextsteps"></a>Nächste Schritte

Allgemeine Informationen zu MapReduce-Aufträgen in HDInsight:

* [Verwenden von MapReduce mit Hadoop in HDInsight](hdinsight-use-mapreduce.md)

Informationen zu anderen Möglichkeiten, wie Sie mit Hadoop in HDInsight arbeiten können:

* [Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md)

* [Verwenden von Pig mit Hadoop in HDInsight](hdinsight-use-pig.md)
<!--HONumber=47-->
