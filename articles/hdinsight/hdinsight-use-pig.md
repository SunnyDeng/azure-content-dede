<properties
   pageTitle="Verwenden von Hadoop Pig in HDInsight | Microsoft Azure"
   description="Erfahren Sie, wie Sie Pig mit Hadoop in HDInsight verwenden."
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
   ms.date="07/06/2015"
   ms.author="larryfr"/>

# Verwenden von Pig mit Hadoop in HDInsight

[AZURE.INCLUDE [pig-selector](../../includes/hdinsight-selector-use-pig.md)]

[Apache Pig](http://pig.apache.org/) ist eine Plattform zum Erstellen von Programmen für Hadoop mithilfe einer prozeduralen Sprache namens *Pig Latin*. Pig ist eine Alternative zu Java für das Erstellen von *MapReduce*-Lösungen, und es ist in Azure HDInsight enthalten.

In diesem Artikel erfahren Sie, wie Sie Pig mit HDInsight verwenden können.

##<a id="why"></a>Warum Pig?

Eine der Herausforderungen der Verarbeitung von Daten mithilfe von MapReduce in Hadoop ist die Implementierung der Verarbeitungslogik mit nur einer Karte und einer Reduce-Funktion. Für die komplexe Verarbeitung muss die diese häufig in mehrere MapReduce-Vorgänge unterteilt werden, die miteinander verkettet sind, um das gewünschte Ergebnis zu erzielen.

Anstatt nur Karte und Reduce-Funktion verwenden zu können, ermöglicht Pig Ihnen, die Verarbeitung als eine Reihe von Transformationen zu definieren, durch die Daten durchlaufen, um die gewünschte Ausgabe zu erzeugen.

Mit der Pig Latin-Sprache können Sie den Datenfluss von Rohdaten durch eine oder mehrere Transformationen beschreiben, um die gewünschte Ausgabe zu erzeugen. Pig Latin-Programme folgen diesem allgemeinen Muster:

- **Laden**: Die zu verändernden Daten werden aus dem Dateisystem gelesen
- **Transformieren**: Manipulation der Daten
- **Ablage oder Speicherung**: Ausgabe der Daten auf dem Bildschirm oder Speicherung zur Weiterverarbeitung

Pig Latin unterstützt auch benutzerdefinierte Funktionen (UDF), wodurch Sie externe Komponenten aufrufen können, die in Pig Latin schwer zu modellierende Logik implementieren.

Weitere Informationen zu Pig Latin finden Sie unter [Pig Latin-Referenzhandbuch 1](http://pig.apache.org/docs/r0.7.0/piglatin_ref1.html) und [Pig Latin-Referenzhandbuch 2](http://pig.apache.org/docs/r0.7.0/piglatin_ref2.html).

Ein Beispiel für benutzerdefinierte Funktionen mit Pig finden Sie in den folgenden Dokumenten:

* [Verwenden von Python mit Pig und Hive in HDInsight](hdinsight-python.md)

* [Verwenden von C# mit Hive und Pig in HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

##<a id="data"></a>Infos zu Beispiel-Daten

Dieses Beispiel verwendet eine *log4j*-Beispieldatei, die unter **/example/data/sample.log** in Ihrem Blob-Speichercontainer abgelegt ist. Jedes Protokoll innerhalb der Datei besteht aus einer Reihe von Feldern, unter denen sich ein Feld namens `[LOG LEVEL]` befindet, das die Art und den Schweregrad des jeweiligen Fehlers anzeigt, beispielsweise:

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Die Protokollebene im vorherigen Beispiel ist ERROR.

> [AZURE.NOTE]Sie können auch eine log4j-Datei generieren, indem Sie das [Apache Log4j](http://en.wikipedia.org/wiki/Log4j)-Protokollierungstool verwenden und die Datei dann in Ihren Blob hochladen. Anweisungen hierzu finden Sie unter [Hochladen von Daten in HDInsight](hdinsight-upload-data.md). Weitere Informationen zur Verwendung von Blobs im Azure-Speicher mit HDInsight finden Sie unter [Verwenden des Azure Blobspeichers mit HDInsight](../hdinsight-use-blob-storage.md).

Die Beispieldaten werden in einem Azure Blobspeicher gespeichert, den HDInsight als Standard-Dateisystem für Hadoop-Cluster verwendet. HDInsight kann mithilfe des Präfix **wasb** auf Dateien zugreifen, die in Blobs gespeichert sind. Für die Datei "sample.log" gilt z. B. die folgende Syntax:

	wasb:///example/data/sample.log

Da WASB der Standardspeicher für HDInsight ist, können Sie auch mithilfe von **/example/data/sample.log** von Pig Latin auf die Datei zugreifen.

> [AZURE.NOTE]Die Syntax ****wasb:///** wird für den Zugriff auf Dateien verwendet, die im Standardspeichercontainer für Ihren HDInsight-Cluster gespeichert werden. Wenn Sie beim Bereitstellen Ihres Clusters weitere Speicherkonten angegeben haben und auf die unter diesen Konten gespeicherten Dateien zugreifen möchten, können Sie den Containernamen und die Speicherkontoadresse angeben, um auf diese Daten zuzugreifen, beispielsweise: ****wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.


##<a id="job"></a>Infos zum Beispielauftrag

Der folgende Pig Latin-Auftrag lädt die **sample.log**-Datei aus dem Standardspeicher in Ihr HDInsight-Cluster. Er führt dann eine Reihe von Transformationen durch, die als Ergebnis anzeigen, wie oft jede Protokollebene in den Eingabedaten aufgetreten ist. Die Ergebnisse werden in STDOUT abgebildet.

	LOGS = LOAD 'wasb:///example/data/sample.log';
	LEVELS = foreach LOGS generate REGEX_EXTRACT($0, '(TRACE|DEBUG|INFO|WARN|ERROR|FATAL)', 1)  as LOGLEVEL;
	FILTEREDLEVELS = FILTER LEVELS by LOGLEVEL is not null;
	GROUPEDLEVELS = GROUP FILTEREDLEVELS by LOGLEVEL;
	FREQUENCIES = foreach GROUPEDLEVELS generate group as LOGLEVEL, COUNT(FILTEREDLEVELS.LOGLEVEL) as COUNT;
	RESULT = order FREQUENCIES by COUNT desc;
	DUMP RESULT;

Die folgende Abbildung zeigt eine Aufschlüsselung der Wirkung jeder einzelnen Transformation auf die Daten.

![Grafische Darstellung der Transformationen][image-hdi-pig-data-transformation]

##<a id="run"></a>Pig Latin-Auftrag ausführen

HDInsight kann Pig Latin-Aufträge mithilfe verschiedener Methoden ausführen. Die folgende Tabelle hilft Ihnen bei der Entscheidung, welche Methode für Sie geeignet ist. Folgen Sie anschließend dem Link für eine exemplarische Vorgehensweise.

| **Verwenden Sie dies** auf Wunsch... | ... eine **interaktive** Shell | ...**Batch**verarbeitung | ...mit diesem **Clusterbetriebssystem** | ...von diesem **Clusterbetriebssystem** |
|:--------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-pig-ssh.md) | ✔ | ✔ | Linux | Linux, Unix, Mac OS X oder Windows |
| [Curl](hdinsight-hadoop-use-pig-curl.md) | &nbsp; | ✔ | Linux oder Windows | Linux, Unix, Mac OS X oder Windows |
| [.NET SDK für Hadoop](hdinsight-hadoop-use-pig-dotnet-sdk.md) | &nbsp; | ✔ | Linux oder Windows | Windows (vorläufig) |
| [Windows PowerShell](hdinsight-hadoop-use-pig-powershell.md) | &nbsp; | ✔ | Linux oder Windows | Windows |
| [Remotedesktop](hdinsight-hadoop-use-pig-remote-desktop.md) | ✔ | ✔ | Windows | Windows |

##<a id="nextsteps"></a>Nächste Schritte

Nachdem Sie erfahren haben, wie Sie Pig mit HDInsight verwenden, können Sie mithilfe der nachfolgenden Links andere Möglichkeiten für die Arbeit mit Azure HDInsight untersuchen.

* [Hochladen von Daten in HDInsight][hdinsight-upload-data]
* [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
* [Verwenden von MapReduce-Aufträgen mit HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-pig/hdi.checkmark.png

[apachepig-home]: http://pig.apache.org/
[putty]: http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html
[curl]: http://curl.haxx.se/

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md#mapreduce-sdk

[Powershell-install-configure]: ../install-configure-powershell.md

[powershell-start]: http://technet.microsoft.com/library/hh847889.aspx

[image-hdi-log4j-sample]: ./media/hdinsight-use-pig/HDI.wholesamplefile.png
[image-hdi-pig-data-transformation]: ./media/hdinsight-use-pig/HDI.DataTransformation.gif
[image-hdi-pig-powershell]: ./media/hdinsight-use-pig/hdi.pig.powershell.png
[image-hdi-pig-architecture]: ./media/hdinsight-use-pig/HDI.Pig.Architecture.png

<!---HONumber=August15_HO8-->