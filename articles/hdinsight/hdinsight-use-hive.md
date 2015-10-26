<properties
	pageTitle="Erfahren Sie, was Hive ist und wie Sie HiveQL verwenden | Microsoft Azure"
	description="Informieren Sie sich über Apache Hive und dessen Verwendung mit Hadoop in HDInsight. Wählen Sie die richtige Ausführung von Hive-Aufträgen, und verwenden Sie HiveQL zum Analysieren einer Apache-Log4j-Beispieldatei."
	keywords="hiveql,what is hive"
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
	ms.date="10/05/2015"
	ms.author="larryfr"/>

# Verwenden von Hive und HiveQL mit Hadoop in HDInsight zum Analysieren einer Apache Log4j-Beispieldatei

[AZURE.INCLUDE [Hive-Selektor](../../includes/hdinsight-selector-use-hive.md)]


In diesem Lernprogramm erfahren Sie, wie Sie Apache Hive in Hadoop in HDInsight verwenden und wie Sie die richtige Ausführung für Hive-Aufträge auswählen. Sie erhalten außerdem Informationen über HiveQL und die Analyse einer Apache Log4j-Beispieldatei.

##<a id="why"></a>Was ist Hive und wozu ist es gut?
[Apache Hive](http://hive.apache.org/) ist ein Data Warehouse-System für Hadoop, das die Zusammenfassung, Abfrage und Analyse von Daten per HiveQL (eine Abfragesprache ähnlich SQL) ermöglicht. Hive kann verwendet werden, um Daten interaktiv zu durchsuchen oder um mehrmals durchführbare Batchverarbeitungsaufträge zu erstellen.

Hive ermöglicht Ihnen die Strukturierung größtenteils unstrukturierter Daten. Nachdem Sie die Struktur definiert haben, können Sie mit Hive Daten abfragen, ohne Java- oder MapReduce-Kenntnisse zu besitzen. **HiveQL** (die Hive-Abfragesprache) ermöglicht es Ihnen, Abfragen mit Anweisungen zu erstellen, die T-SQL ähneln.

Hive kann mit strukturierten und semistrukturierten Daten arbeiten, z. B. Textdateien, in denen die Felder durch bestimmte Zeichen getrennt sind. Hive unterstützt auch benutzerdefinierte **Serialisierer/Deserialisierer (SerDe)** für komplexe oder unregelmäßig strukturierte Daten. Weitere Informationen finden Sie unter [Verwenden eines benutzerdefinierten JSON-SerDe mit HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/06/18/how-to-use-a-custom-json-serde-with-microsoft-azure-hdinsight.aspx).

Hive kann auch über **benutzerdefinierte Funktionen (UDF)** erweitert werden. Mit einer UDF-Datei können Sie Funktionen oder Logik implementieren, die sich nicht einfach in HiveQL modellieren lässt. Ein Beispiel für benutzerdefinierte Funktionen mit Hive finden Sie hier:

* [Verwenden von Python mit Hive und Pig in HDInsight](hdinsight-python.md)

* [Verwenden von C# mit Hive und Pig in HDInsight](hdinsight-hadoop-hive-pig-udf-dotnet-csharp.md)

* [How to add a custom Hive UDF to HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx) (in englischer Sprache)

##<a id="data"></a>Infos zu den Beispieldaten, eine Apache Log4j-Datei

Dieses Beispiel verwendet eine *log4j*-Beispieldatei, die unter **/example/data/sample.log** in Ihrem Blob-Speichercontainer abgelegt ist. Jedes Protokoll innerhalb der Datei besteht aus einer Reihe von Feldern, unter denen sich ein Feld namens `[LOG LEVEL]` befindet, das die Art und den Schweregrad des jeweiligen Fehlers anzeigt, beispielsweise:

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Die Protokollebene im vorherigen Beispiel ist ERROR.

> [AZURE.NOTE]Sie können auch eine Log4j-Datei generieren, indem Sie das [Apache Log4j](http://en.wikipedia.org/wiki/Log4j)-Protokollierungstool verwenden und die Datei dann in Ihren Blobcontainer hochladen. Anweisungen hierzu finden Sie unter [Hochladen von Daten in HDInsight](hdinsight-upload-data.md). Weitere Informationen zur Verwendung des Azure-Blobspeichers mit HDInsight finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](../hdinsight-use-blob-storage.md).

Die Beispieldaten werden in einem Azure-Blobspeicher gespeichert, den HDInsight als Standarddateisystem verwendet. HDInsight kann mithilfe des Präfix **wasb** auf Dateien zugreifen, die in Blobs gespeichert sind. Für die Datei "sample.log" gilt z. B. die folgende Syntax:

	wasb:///example/data/sample.log

Da Azure-Blobspeicher der Standardspeicher für HDInsight ist, können Sie auch über **/example/data/sample.log** von HiveQL aus auf die Datei zugreifen.

> [AZURE.NOTE]Die Syntax ****wasb:///** wird für den Zugriff auf Dateien verwendet, die im Standardspeichercontainer für Ihren HDInsight-Cluster gespeichert werden. Wenn Sie beim Bereitstellen Ihres Clusters weitere Speicherkonten angegeben haben und auf die unter diesen Konten gespeicherten Dateien zugreifen möchten, können Sie den Containernamen und die Speicherkontoadresse angeben, um auf diese Daten zuzugreifen, beispielsweise: ****wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**

##<a id="job"></a>Beispielauftrag: Projizieren von Spalten auf durch Trennzeichen getrennte Daten

Die folgenden HiveQL-Anweisungen projizieren Spalten auf durch Trennzeichen getrennte Daten, die sich im Verzeichnis ****wasb:///example/data** befinden:

	DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
    SELECT t4 AS sev, COUNT(*) AS count FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log' GROUP BY t4;

Im vorherigen Beispiel führen die HiveQL-Anweisungen die folgenden Aktionen aus:

* **TABELLE LÖSCHEN**: Löscht Tabelle und Datendatei, falls die Tabelle bereits vorhanden ist.
* **EXTERNE TABELLE ERSTELLEN**: Erstellt eine neue **externe** Tabelle in Hive. Externe Tabellen speichern nur die Tabellendefinition in Hive. Die Daten verbleiben am ursprünglichen Speicherort und im ursprünglichen Format.
* **ZEILENFORMAT**: Teilt Hive mit, wie die Daten formatiert werden. In diesem Fall werden die Felder in den einzelnen Protokollen durch Leerzeichen getrennt.
* **SPEICHERORT DER TEXTDATEI**: Teilt Hive den Speicherort der Daten (das Verzeichnis "Beispiel/Daten") und die Information mit, dass die Speicherung als Text erfolgt. Die Daten können sich in einer Datei befinden oder auf mehrere Dateien im Verzeichnis verteilt sein.
* **AUSWÄHLEN**: Wählt die Anzahl aller Zeilen aus, bei denen die Spalte **t4** den Wert **[FEHLER]** enthält. Dadurch sollte der Wert **3** zurückgegeben werden, da dieser Wert in drei Zeilen enthalten ist.
* **INPUT\_\_FILE\_\_NAME LIKE '%.log'**: Teilt Hive mit, dass nur Daten aus Dateien mit der Erweiterung ".log" zurückgeben werden sollen. Dies schränkt die Suche auf die Datei "sample.log" ein, die die Daten enthält, und verhindert, dass Daten aus anderen Beispieldatendateien zurückgegeben werden, die nicht dem von uns definierten Schema entsprechen.

> [AZURE.NOTE]Externe Tabellen sollten verwendet werden, wenn zu erwarten ist, dass die zugrunde liegenden Daten über eine externe Quelle (z. B. einen automatisierten Prozess zum Hochladen von Daten) oder über einen anderen MapReduce-Vorgang aktualisiert werden und wenn Sie möchten, dass Hive immer die neuesten Daten verwendet werden.
>
> Durch das Löschen einer externen Tabelle werden **nicht** die Daten, sondern nur die Tabellendefinitionen gelöscht.

Nach dem Erstellen der externen Tabelle werden die folgenden Anweisungen zum Erstellen einer **internen** Tabelle verwendet.

	CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
	STORED AS ORC;
	INSERT OVERWRITE TABLE errorLogs
	SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]' AND INPUT__FILE__NAME LIKE '%.log';

Diese Anweisungen führen die folgenden Aktionen aus:

* **TABELLE ERSTELLEN, FALLS NICHT VORHANDEN**: Erstellt eine Tabelle, sofern diese noch nicht vorhanden ist. Da das Schlüsselwort **EXTERN** nicht verwendet wird, ist dies eine interne Tabelle, die im Hive-Data-Warehouse gespeichert und vollständig von Hive verwaltet wird.
* **ALS ORC GESPEICHERT**: Speichert die Daten im ORC-Format (Optimized Row Columnar). Dies ist ein stark optimiertes und effizientes Format zum Speichern von Hive-Daten.
* **ÜBERSCHREIBEN EINFÜGEN ... AUSWÄHLEN**: Wählt Zeilen in der Tabelle **log4jLogs** aus, die **[FEHLER]** enthalten. Dann werden die Daten in die Tabelle **errorLogs** eingefügt.

> [AZURE.NOTE]Anders als bei externen Tabellen werden beim Löschen von internen Tabellen auch die zugrunde liegenden Daten gelöscht.

##<a id="usetez"></a>Verwenden von Apache Tez zur Verbesserung der Leistung

[Apache Tez](http://tez.apache.org) ist ein Framework, mit dem datenintensive Anwendungen wie Hive wesentlich effizienter ausgeführt und skaliert werden können. In der neuesten Version von HDInsight unterstützt Hive die Ausführung in Tez. Tez ist für Linux-basierte HDInsight-Cluster standardmäßig aktiviert.

> [AZURE.NOTE]Tez ist derzeit für Windows-basierte HDInsight-Cluster standardmäßig deaktiviert und muss aktiviert werden. Um die Vorteile von Tez zu nutzen, muss der folgende Wert in einer Hive-Abfrage gesetzt sein:
>
> ```set hive.execution.engine=tez;```
>
>Dieser Wert kann pro Abfrage gesetzt werden, indem Sie diesen Befehl an den Anfang der Abfrage stellen. Diese Funktion kann auch standardmäßig für einen Cluster aktiviert werden, indem Sie den Konfigurationswert bei der Erstellung des Clusters setzen. Weitere Details finden Sie unter [Bereitstellen von HDInsight-Clustern](hdinsight-provision-clusters.md).

Die [Hive mit Tez-Designdokumente](https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez) enthalten weitere Details zu Implementierungsauswahl und Konfigurationseinstellungen.


##<a id="run"></a>Wählen der richtigen Ausführung des HiveQL-Auftrags

HDInsight kann HiveQL-Aufträge mithilfe verschiedener Methoden ausführen. Die folgende Tabelle hilft Ihnen bei der Entscheidung, welche Methode für Sie geeignet ist. Folgen Sie anschließend dem Link für eine exemplarische Vorgehensweise.

| **Verwenden Sie dies** auf Wunsch... | ... eine **interaktive** Shell | ...**Batch**verarbeitung | ...mit diesem **Clusterbetriebssystem** | ...von diesem **Clusterbetriebssystem** |
|:--------------------------------------------------------------------------------|:---------------------------:|:-----------------------:|:------------------------------------------|:-----------------------------------------|
| [Beeline-Befehl (in einer SSH-Sitzung)](hdinsight-hadoop-use-hive-beeline.md) | ✔ | ✔ | Linux | Linux, Unix, Mac OS X oder Windows |
| [Hive-Befehl (in einer SSH-Sitzung)](hdinsight-hadoop-use-hive-ssh.md) | ✔ | ✔ | Linux | Linux, Unix, Mac OS X oder Windows |
| [Curl](hdinsight-hadoop-use-hive-curl.md) | &nbsp; | ✔ | Linux oder Windows | Linux, Unix, Mac OS X oder Windows |
| [Abfragekonsole](hdinsight-hadoop-use-hive-query-console.md) | &nbsp; | ✔ | Windows | Browserbasiert |
| [HDInsight-Tools für Visual Studio](hdinsight-hadoop-use-hive-visual-studio.md) | &nbsp; | ✔ | Linux oder Windows | Windows |
| [Windows PowerShell](hdinsight-hadoop-use-hive-powershell.md) | &nbsp; | ✔ | Linux oder Windows | Windows |
| [Remotedesktop](hdinsight-hadoop-use-hive-remote-desktop.md) | ✔ | ✔ | Windows | Windows |

## Ausführen von Hive-Aufträgen in Azure HDInsight mithilfe lokaler SQL Server Integration Services

Sie können auch mit den SQL Server Integration Services (SSIS) einen Hive-Auftrag ausführen. Das Azure Feature Pack für SSIS bietet die folgenden Komponenten, die mit Hive-Aufträgen in HDInsight funktionieren.


- [Hive-Aufgabe in Azure HDInsight][hivetask]
- [Verbindungs-Manager für Azure-Abonnements][connectionmanager]


Erfahren Sie [hier][ssispack] mehr über das Azure Feature Pack für SSIS.


##<a id="nextsteps"></a>Nächste Schritte

Nachdem Sie erfahren haben, was Hive ist und wie Sie es mit Hadoop in HDInsight verwenden, können Sie mithilfe der nachfolgenden Links andere Möglichkeiten für die Arbeit mit Azure HDInsight untersuchen.


- [Hochladen von Daten in HDInsight][hdinsight-upload-data]
- [Verwenden von Pig mit HDInsight][hdinsight-use-pig]
- [Verwenden von MapReduce-Aufträgen mit HDInsight][hdinsight-use-mapreduce]

[check]: ./media/hdinsight-use-hive/hdi.checkmark.png

[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/documentation/articles/hdinsight-connect-excel-power-query/
[hivetask]: http://msdn.microsoft.com/de-DE/library/mt146771(v=sql.120).aspx
[connectionmanager]: http://msdn.microsoft.com/de-DE/library/mt146773(v=sql.120).aspx
[ssispack]: http://msdn.microsoft.com/de-DE/library/mt146770(v=sql.120).aspx

[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-use-oozie]: hdinsight-use-oozie.md
[hdinsight-analyze-flight-data]: hdinsight-analyze-flight-delay-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md


[hdinsight-storage]: ../hdinsight-use-blob-storage.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: ../hdinsight-get-started.md

[Powershell-install-configure]: ../install-configure-powershell.md
[powershell-here-strings]: http://technet.microsoft.com/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!---HONumber=Oct15_HO3-->