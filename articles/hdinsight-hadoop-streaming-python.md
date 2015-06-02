<properties
   pageTitle="Entwickeln von Python MapReduce-Aufträgen mit HDInsight | Azure"
   description="Erfahren Sie, wie Sie Python MapReduce-Aufträge auf Linux-basierten HDInsight-Clustern erstellen und ausführen."
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

#Entwickeln von Python-Streamingprogrammen für HDInsight

Hadoop stellt eine Streaming-API für MapReduce zur Verfügung, mit der Sie Map- und Reduce-Funktionen in anderen Sprache als Java schreiben können. In diesem Dokument erfahren Sie, wie Sie mit Python MapReduce-Vorgänge ausführen.

> [AZURE.NOTE]Dieser Artikel basiert auf Informationen und Beispielen, die von Michael Noll unter [http://www.michael-noll.com/tutorials/writing-an-hadoop-mapreduce-program-in-python/] (Schreiben eines MapReduce-Programms in Python) veröffentlicht wurden.

##Voraussetzungen

Damit Sie die in diesem Artikel aufgeführten Schritte ausführen können, benötigen Sie Folgendes:

* Ein Linux-basierter Hadoop-Cluster in HDInsight

* Ein Texteditor

* Für Windows-Clients: PuTTY und PSCP Diese Dienstprogramme stehen über die <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">PuTTY-Downloadseite</a> zur Verfügung.

##Wortzählung

In diesem Beispiel implementieren Sie eine einfache Wortanzahlermittlung mithilfe einer Mapper- und Reducer-Funktion. Die Mapper-Funktion teilt Sätze in einzelne Wörter auf und die Reducer-Funktion aggregiert die Wörter und Zähler, um die Ausgabe zu erzeugen.

Im folgenden Flußdiagramm ist dargestellt, was in den Mapper- und Reducer-Funktionen geschieht.

![Abbildung von MapReduce](./media/hdinsight-hadoop-streaming-python/HDI.WordCountDiagram.png)

##Gründe für Python

Python ist eine allgemeine Programmiersprache, mit der Sie Konzepte in weniger Codezeilen als in vielen anderen Sprachen ausdrücken können. Sie wurde kürzlich bei Datenwissenschaftlern als Prototyping-Sprache beliebt, da sie durch ihre interpretierende Art, dynamische Typisierung und elegante Syntax für die schnelle Anwendungsentwicklung geeignet ist.

Python wird auf allen HDInsight-Clustern installiert.

##Streaming-MapReduce

Mit Hadoop können Sie eine Datei angeben, die die von einem Auftrag verwendete Logik für den Mapper und Reducer enthält. Bestimmte Anforderungen an die Logik für Mapper und Reducer:

* **Eingabe**: Die Komponenten für Mapper und Reducer müssen Eingabedaten von STDIN lesen.

* **Ausgabe**: Die Komponenten für Mapper und Reducer müssen Ausgabedaten an STDOUT schreiben.

* **Datenformat**: Die verwendeten und erzeugten Daten müssen ein Schlüssel-/Wertpaar darstellen, das durch ein Tabulatorzeichen getrennt werden muss.

Python kann diese Anforderungen einfach mithilfe des Moduls **sys** zum Lesen von STDIN und mithilfe des Moduls **print** für die Ausgabe an STDOUT behandeln. Der Rest besteht einfach aus der Formatierung der Daten mit einem Tabulatorzeichen (`\t`) zwischen Schlüssel und Wert.

##Erstellen von Mapper und Reducer

Mapper und Reducer sind reine Textdateien, in diesem Fall **mapper.py** und **reducer.py**, daher ist eindeutig, welche Datei wofür zuständig ist. Sie können diese mit einem Editor Ihrer Wahl erstellen.

###Mapper.py

Erstellen Sie eine neue Datei namens **mapper.py**, und verwenden Sie den folgenden Inhalt für die Datei.

	#!/usr/bin/env python

	# Use the sys module
	import sys

	# 'file' in this case is STDIN
	def read_input(file):
		# Split each line into words
		for line in file:
			yield line.split()

	def main(separator='\t'):
		# Read the data using read_input
		data = read_input(sys.stdin)
		# Process each words returned from read_input
		for words in data:
			# Process each word
			for word in words:
				# Write to STDOUT
				print '%s%s%d' % (word, separator, 1)

	if __name__ == "__main__":
		main()

Nehmen Sie sich einen Moment Zeit, um den Code zu lesen und seine Funktionsweise zu verstehen.

###Reducer.py

Erstellen Sie eine neue Datei namens **mapper.py**, und verwenden Sie den folgenden Inhalt für die Datei:

	#!/usr/bin/env python
	
	# import modules
	from itertools import groupby
	from operator import itemgetter
	import sys
	
	# 'file' in this case is STDIN
	def read_mapper_output(file, separator='\t'):
		# Go through each line
	    for line in file:
			# Strip out the separator character
	        yield line.rstrip().split(separator, 1)
	
	def main(separator='\t'):
	    # Read the data using read_mapper_output
	    data = read_mapper_output(sys.stdin, separator=separator)
		# Group words and counts into 'group'
		#   Since MapReduce is a distributed process, each word
        #   may have multiple counts. 'group' will have all counts
        #   which can be retrieved using the word as the key.
	    for current_word, group in groupby(data, itemgetter(0)):
	        try:
				# For each word, pull the count(s) for the word
				#   from 'group' and create a total count
	            total_count = sum(int(count) for current_word, count in group)
				# Write to stdout
	            print "%s%s%d" % (current_word, separator, total_count)
	        except ValueError:
	            # Count was not a number, so do nothing
	            pass
	
	if __name__ == "__main__":
	    main()

##Hochladen der Dateien

Sowohl **mapper.py** als auch **reducer.py** müssen sich auf dem Stammknoten des Clusters befinden, bevor sie ausgeführt werden können. Am einfachsten können sie mit **scp** (**pscp**, wenn Sie einen Windows-Client verwenden) hochgeladen werden.

Verwenden Sie den folgenden Befehl auf dem Client im gleichen Verzeichnis, in dem sich auch **mapper.py** und **reducer.py** befinden. Ersetzen Sie **Benutzernamen** durch einen SSH-Benutzer und den **Clusternamen** durch den Namen des Clusters.

	scp mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:

Dadurch werden die Dateien aus dem lokalen System auf den Stammknoten kopiert.

> [AZURE.NOTE]Wenn Sie zum Schutz Ihres SSH-Kontos ein Kennwort verwendet haben, werden Sie zur Eingabe dieses Kennworts aufgefordert. Wenn Sie einen SSH-Schlüssel verwendet haben, müssen Sie möglicherweise den Parameter `-i` und den Pfad zum privaten Schlüssel, z. B. `scp -i /path/to/private/key mapper.py reducer.py username@clustername-ssh.azurehdinsight.net:`, angeben.

##Ausführen von MapReduce

1. Herstellen einer Verbindung zum Cluster mithilfe von SSH:

		ssh username@clustername-ssh.azurehdinsight.net

	> [AZURE.NOTE]Wenn Sie zum Schutz Ihres SSH-Kontos ein Kennwort verwendet haben, werden Sie zur Eingabe dieses Kennworts aufgefordert. Wenn Sie einen SSH-Schlüssel verwendet haben, müssen Sie möglicherweise den Parameter `-i` und den Pfad zum privaten Schlüssel, z. B. `ssh -i /path/to/private/key username@clustername-ssh.azurehdinsight.net`, angeben.

2. Verwenden Sie zum Starten des MapReduce-Auftrags den folgenden Befehl.

		hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-streaming.jar -files mapper.py,reducer.py -mapper mapper.py -reducer reducer.py -input wasb:///example/data/davinci.txt -output wasb:///example/wordcountout

	Dieser Befehl besteht aus den folgenden Komponenten:

	* **hadoop-streaming.jar**: Wird verwendet, wenn Streaming-MapReduce-Vorgänge ausgeführt werden. Es verbindet Hadoop mit dem von Ihnen bereitgestellten externen MapReduce-Code.
	
	* **-files**: Teilt Hadoop mit, dass die angegebenen Dateien für diesen MapReduce-Auftrag erforderlich sind und auf alle Arbeitsknoten kopiert werden müssen

	* **-mapper**: Teilt Hadoop mit, welche Datei als Mapper verwendet werden soll.

	* **-reducer**: Teilt Hadoop mit, welche Datei als Reducer verwendet werden soll.

	* **-input**: Die Eingabedatei, deren Wörter gezählt werden sollen.

	* **-output**: Das Verzeichnis, in das die Ausgabe geschrieben wird.

		> [AZURE.NOTE]Dieses Verzeichnis wird durch den Auftrag erstellt.

Beim Start des Auftrags sollten eine Reihe von **INFO**-Anweisungen angezeigt werden. Abschließend werden die Vorgänge **map** und **reduce** als Prozentsätze angezeigt.

	15/02/05 19:01:04 INFO mapreduce.Job:  map 0% reduce 0%
	15/02/05 19:01:16 INFO mapreduce.Job:  map 100% reduce 0%
	15/02/05 19:01:27 INFO mapreduce.Job:  map 100% reduce 100%

Zuletzt erhalten Sie Statusinformationen zum Auftrag, wenn dieser abgeschlossen ist.

##Anzeigen der Ausgabe

Sobald der Auftrag abgeschlossen ist, verwenden Sie den folgenden Befehl zum Anzeigen der Ausgabe:

	hadoop fs -text /example/wordcountout/part-00000

Dabei sollte eine Liste angezeigt werden, die die Wörter und die Häufigkeit ihres Auftretens enthält. Nachfolgend sehen Sie ein Beispiel für die Ausgabedaten:

	wrenching       1
	wretched        6
	wriggling       1
	wrinkled,       1
	wrinkles        2
	wrinkling       2

##Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Streaming-MapReduce-Aufträge mit HDInsight verwenden, können Sie mithilfe der nachfolgenden Links andere Möglichkeiten für die Arbeit mit Azure HDInsight untersuchen.

* [Verwenden von Hive mit HDInsight](hdinsight-use-hive.md)
* [Verwenden von Pig mit HDInsight](hdinsight-use-pig.md)
* [Verwenden von MapReduce-Aufträgen mit HDInsight](hdinsight-use-mapreduce.md)

<!--HONumber=54-->