<properties urlDisplayName="Use Hadoop Hive in HDInsight" pageTitle="Verwenden von Hadoop Hive in HDInsight | Azure" metaKeywords="" description="Learn how to use Hive with HDInsight. You'll use a log file as input into an HDInsight table, and use HiveQL to query the data and report basic statistics." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop Hive in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/25/2014" ms.author="jgao" />

# Verwenden von Hive mit Hadoop in HDInsight

[Apache Hive][apache-hive] unterstützt MapReduce-Jobs mit einer SQL-ähnlichen Skriptsprache namens *HiveQL*. Hive ist ein Data Warehouse-System für Hadoop und ermöglicht Zusammenfassung, Abfrage und Analyse großer Datenmengen. In diesem Artikel verwenden Sie HiveQL, um eine einfache Datendatei abzufragen, die als Teil der HDInsight-Clusterbereitstellung mitgeliefert wird.


**Voraussetzungen:**

- Sie benötigen einen bereitgestellten **HDInsight-Cluster**. Eine Anleitung zur entsprechenden Vorgehensweise mit dem Azure-Portal finden Sie unter [Erste Schritte mit HDInsight][hdinsight-get-started]. Informationen zu den verschiedenen anderen Arten, auf die solche Cluster erstellt werden können, finden Sie unter [Bereitstellen von HDInsight-Clustern][hdinsight-provision].

- Sie müssen **Azure PowerShell** auf Ihrer Arbeitsstation installiert haben. Anweisungen zur entsprechenden Vorgehensweise finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].

##Themen in diesem Artikel

* [Der Hive-Anwendungsbereich](#usage)
* [Hochladen von Daten für Hive-Tabellen](#uploaddata)
* [Ausführen von Hive-Abfragen mit PowerShell](#runhivequeries)
* [Ausführen von Hive-Abfragen mit HDInsight Tools für Visual Studio](#runhivefromvs)
* [Verwenden von Tez zur Verbesserung der Leistung](#usetez)
* [Nächste Schritte](#nextsteps)

##<a id="usage"></a>Der Hive-Anwendungsbereich

![HDI.HIVE.Architecture][image-hdi-hive-architecture]

Hive-Projekte strukturieren große unstrukturierte Datenmengen, damit Sie für diese Daten Abfragen ausführen können. Hive bietet eine Abstraktionsebene über dem Java-basierten MapReduce-Framework, und Benutzer können ohne Java- oder MapReduce-Kenntnisse Daten abfragen. HiveQL, die Hive-Abfragesprache, ermöglicht es Ihnen, Abfragen mit Anweisungen zu erstellen, die T-SQL ähneln. HiveQL-Abfragen werden von HDInsight für Sie für MapReduce kompiliert und auf dem Cluster ausgeführt. Weitere Vorteile von Hive:

- Mit Hive können Programmierer, die mit dem MapReduce-Framework vertraut sind, benutzerdefinierte Mapper und Reducer verwenden, um komplexere Analysen durchzuführen, die mit den integrierten Funktionen von HiveQL eventuell nicht möglich sind.
- Hive eignet sich hervorragend für die Stapelverarbeitung großer Mengen unveränderter Daten (wie in Webprotokollen). Es ist nicht für Transaktionsanwendungen geeignet, für die sehr schnelle Reaktionszeiten erforderlich sind, wie z. B. Datenbankverwaltungssysteme.
- Hive bietet optimierte Skalierbarkeit (mehr Computer können dynamisch zum Hadoop-Cluster hinzugefügt werden), Erweiterbarkeit (innerhalb des MapReduce-Frameworks und mit anderen Programmierschnittstellen) und Fehlertoleranz. Die Wartezeit wurde bei der Entwicklung nicht als wichtiger Aspekt betrachtet.

##<a id="uploaddata"></a>Hochladen von Daten für Hive-Tabellen

HDInsight verwendet einen Azure-Blobspeicher-Container als Standard-Dateisystem für Hadoop-Cluster. Einige Beispiel-Datendateien werden im Rahmen der Clusterbereitstellung zum Blobspeicher hinzugefügt. In diesem Beitrag verwenden Sie eine im HDInsight-Cluster mitgelieferte *log4j*-Musterdatei die unter **/example/data/sample.log** in Ihrem Blobspeicher-Container liegt. Jedes Protokoll innerhalb der Datei besteht aus einer Reihe von Feldern, unter denen sich ein Feld namens "[LOG LEVEL]" befindet, das die Art und den Schweregrad des jeweiligen Fehlers anzeigt. Beispiel:

	2012-02-03 20:26:41 SampleClass3 [ERROR] verbose detail for id 1527353937

Die Protokollebene im obigen Beispiel ist ERROR.

> [AZURE.NOTE] Außerdem generieren Sie Ihre eigenen log4j-Dateien mit dem [Apache Log4j][apache-log4j]-Protokollierungstool und laden diese Dateien in den Blob-Container hoch. Anweisungen hierzu finden Sie unter [Hochladen von Daten in HDInsight][hdinsight-upload-data]. Weitere Informationen zur Verwendung des Azure-Blobspeichers mit HDInsight finden Sie unter [Verwenden des Azure Blobspeichers mit HDInsight][hdinsight-storage].

HDInsight kann mithilfe des Präfix **wasb** auf Dateien zugreifen, die in Blobspeicher gespeichert werden. Für die Datei "sample.log" gilt z. B. die folgende Syntax:

	wasb:///example/data/sample.log

Da WASB der Standardspeicher für HDInsight ist, können Sie auch mithilfe von **/example/data/sample.log** auf die Datei zugreifen.

> [AZURE.NOTE] Die obige Syntax, **wasb:///**, wird für den Zugriff auf Dateien verwendet, die im Standard-Speichercontainer für Ihren HDInsight-Cluster gespeichert werden. Wenn Sie beim Bereitstellen Ihres Clusters weitere Speicherkonten angegeben haben und auf die unter diesen Konten gespeicherten Dateien zugreifen möchten, können Sie den Containernamen und die Speicherkontoadresse angeben, um auf diese Daten zuzugreifen. Beispiel: **wasb://mycontainer@mystorage.blob.core.windows.net/example/data/sample.log**.

##<a id="runhivequeries"></a> Ausführen von Hive-Abfragen mit PowerShell

Hive-Abfragen können in PowerShell über die Cmdlets **Start-AzureHDInsightJob** und **Invoke-Hive** ausgeführt werden.

* **Start-AzureHDInsightJob** dient zur generischen Auftragsausführung. Mit diesem Cmdlet können Hive-, Pig- und MapReduce-Aufträge auf einem HDInsight-Cluster gestartet werden. **Start-AzureHDInsightJob** ist asynchron und seine Rückgabe erfolgt, bevor der Auftrag abgeschlossen ist. Es werden Informationen zum Auftrag zurückgegeben, die mit Cmdlets wie **Wait-AzureHDInsightJob**, **Stop-AzureHDInsightJob** und **Get-AzureHDInsightJobOutput** verwendet werden können.  **Get-AzureHDInsightJobOutput** muss dazu verwendet werden, um die vom Auftrag an **STDOUT** oder **STDERR** geschriebenen Informationen abzurufen.

* **Invoke-Hive** führt eine Hive-Abfrage aus, wartet auf deren Abschluss und ruft die Ausgabe der Abfrage als Aktion ab.

1. Öffnen Sie ein Azure PowerShell-Konsolenfenster. Die Anweisungen dazu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].
2. Führen Sie den folgenden Befehl aus, um sich mit Ihrem Azure-Abonnement zu verbinden:

		Add-AzureAccount

	Sie werden zur Eingabe Ihrer Azure-Anmeldeinformationen aufgefordert.

2. Legen Sie die Variablen im folgenden Skript fest und führen es dann aus.

		# Provide Azure subscription name, and the Azure Storage account and container that is used for the default HDInsight file system.
		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<AzureStorageAccountName>"
		$containerName = "<AzureStorageContainerName>"

		# Provide HDInsight cluster name Where you want to run the Hive job
		$clusterName = "<HDInsightClusterName>"

3. Führen Sie das folgende Skript aus, um mithilfe der Beispieldaten eine neue Tabelle namens **log4jLogs** zu erstellen.

		# HiveQL
		# Create an EXTERNAL table
		$queryString = "DROP TABLE log4jLogs;" +
		               "CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' STORED AS TEXTFILE LOCATION 'wasb:///example/data/';" +
		               "SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;"

	Die HiveQL-Anweisungen führen die folgenden Aktionen aus:

	* **DROP TABLE** - löscht Tabelle und Datendatei, falls die Tabelle bereits existiert.
	* **CREATE EXTERNAL TABLE** - erstellt eine neue "externe" Tabelle in Hive. Externe Tabellen speichern nur die Tabellendefinition in Hive. Die Daten verbleiben am ursprünglichen Speicherort.
	* **ROW FORMAT** - teilt Hive mit, wie die Daten formatiert werden. In diesem Fall werden die Felder in den einzelnen Protokollen durch Leerzeichen getrennt.
	* **STORED AS TEXTFILE LOCATION** - teilt Hive den Speicherort der Daten (das Verzeichnis "example/data") und die Information mit, dass die Speicherung als Text erfolgt.
	* **SELECT** - die Anzahl aller Zeilen auswählen, für die die Spalte **t4** den Wert **[ERROR]** enthält. Dadurch sollte der Wert **3** zurückgegeben werden, da dieser Wert in drei Zeilen enthalten ist.

	> [AZURE.NOTE] Wenn erwartet wird, dass die zugrunde liegenden Daten über eine externe Quelle, z. B. einen automatisierten Prozess zum Hochladen von Daten, oder über einen anderen MapReduce-Vorgang aktualisiert, aber von Hive immer die neuesten Daten verwendet werden, sollten externe Tabellen verwendet werden.
	>
	> Durch das Ablegen einer externen Tabelle werden **nicht** die Daten, sondern nur die Tabellendefinitionen gelöscht.


4. Führen Sie das folgende Skript aus, um eine Hive-Jobdefinition aus der vorherigen Abfrage zu erstellen.

		# Create a Hive job definition
		$hiveJobDefinition = New-AzureHDInsightHiveJobDefinition -Query $queryString

	Sie können auch den Schalter "-File" verwenden, um eine HiveQL-Skriptdatei auf HDFS anzugeben.

5. Führen Sie das folgende Skript aus, um den Hive-Job zu senden:

		# Submit the job to the cluster
		Select-AzureSubscription $subscriptionName
		$hiveJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $hiveJobDefinition

6. Führen Sie das folgende Skript aus, um auf die Ausführung des Hive-Jobs zu warten:

		# Wait for the Hive job to complete
		Wait-AzureHDInsightJob -Job $hiveJob -WaitTimeoutInSeconds 3600

7. Führen Sie das folgende Skript aus, um die Standardausgabe zu drucken:

		# Print the standard error and the standard output of the Hive job.
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $hiveJob.JobId -StandardOutput


 	![HDI.HIVE.PowerShell][image-hdi-hive-powershell]

	Das Ergebnis lautet:

		[ERROR] 3

	Dies bedeutet, dass die Datei *sample.log* drei ERROR-Protokolle enthält.

4. Für die Verwendung von **Invoke-Hive** müssen Sie zunächst den zu verwendenden Cluster festlegen.

		# Connect to the cluster
		Use-AzureHDInsightCluster $clusterName

4. Verwenden Sie das folgende Skript, um eine neue "interne" Tabelle namens **errorLogs** mithilfe des Cmdlets **Invoke-Hive** zu erstellen.

		# Run a query to create an 'internal' Hive table
		$response = Invoke-Hive -Query @"
		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
		"@
		# print the output on the console
		Write-Host $response

	Diese Anweisungen führen die folgenden Aktionen aus.

	* **CREATE TABLE IF NOT EXISTS** - erstellt eine Tabelle, sofern diese noch nicht vorhanden ist. Da das Schlüsselwort **EXTERNAL** nicht verwendet wird, ist dies eine "interne" Tabelle, die im Hive-Data Warehouse gespeichert und vollständig von Hive verwaltet wird.
	* **STORED AS ORC** - speichert die Daten im ORC-Format (Optimized Row Columnar). Dies ist ein stark optimiertes und effizientes Format zum Speichern von Hive-Daten.
	* **INSERT OVERWRITE ... SELECT** - wählt Zeilen aus der Tabelle **log4jLogs** aus, die **[ERROR]** enthalten. Dann werden die Daten in die Tabelle **errorLogs** eingefügt.

	> [AZURE.NOTE] Anders als bei **externen** Tabellen werden beim Ablegen von internen Tabellen auch die zugrunde liegenden Daten gelöscht.

	Die Ausgabe sieht dann wie folgt aus.

	![PowerShell Invoke-Hive output][img-hdi-hive-powershell-output]

	> [AZURE.NOTE] Für längere HiveQL-Abfragen können Sie PowerShell Here-Zeichenfolgen oder HiveQL-Skriptdateien verwenden. Der folgende Ausschnitt zeigt, wie Sie eine HiveQL-Skriptdatei mit dem *Invoke-Hive*-Cmdlet ausführen können. Die HiveQL-Skriptdatei muss auf WASB hochgeladen werden.
	>
	> `Invoke-Hive -File "wasb://<ContainerName>@<StorageAccountName>/<Path>/query.hql"`
	>
	> Weitere Informationen zu Here-Strings erhalten Sie unter [Verwenden von Windows PowerShell-Here-Strings][powershell-here-strings].

5. Um zu überprüfen, dass nur Zeilen, die **[ERROR]** in Spalte "t4" enthalten, in der Tabelle **errorLogs** gespeichert wurden, verwenden Sie die folgende Anweisung, um alle Zeilen aus **errorLogs** zurückzugeben.

		#Select all rows
		$response = Invoke-Hive -Query "SELECT * from errorLogs;"
		Write-Host $response

	Es sollten drei Datenzeilen zurückgegeben werden, die alle **[ERROR]** in Spalte "t4" enthalten.


> [AZURE.NOTE] Bei Bedarf können Sie die Ausgabe Ihrer Abfragen zur weiteren Analyse in Excel importieren. Weitere Hinweise hierzu finden Sie unter [Verbinden von Excel über Power Query mit Hadoop][import-to-excel].

##<a id="runhivefromvs"></a>Ausführen von Hive-Abfragen mit Visual Studio
HDInsight Tools für Visual Studio werden mit dem Azure SDK für .NET Version 2.5 oder höher ausgeliefert.  Mithilfe der Tools von Visual Studio können Sie Verbindungen zu HDInsight-Clustern herstellen, Hive-Tabellen erstellen und Hive-Abfragen ausführen.  Weitere Informationen finden Sie unter [Erste Schritte mit HDInsight Hadoop-Tools für Visual Studio][1].



##<a id="usetez"></a>Verwenden von Tez zur Verbesserung der Leistung

[Apache Tez][apache-tez] ist ein Framework, mit dem datenintensive Anwendungen wie Hive wesentlich effizienter ausgeführt und skaliert werden können. In der neuesten Version von HDInsight unterstützt Hive die Ausführung in Tez.  Diese Funktion ist momentan standardmäßig deaktiviert und muss aktiviert werden.  In zukünftigen Clusterversionen wird diese Version standardmäßig aktiviert sein. Um die Vorteile von Tez zu nutzen, muss der folgende Wert in einer Hive-Abfrage gesetzt sein:

		set hive.execution.engine=tez;

Dieser Wert kann pro Abfrage gesetzt werden, indem Sie diesen Befehl am Anfang der Abfrage stellen.  Diese Funktion kann auch standardmäßig für ein Cluster aktiviert werden, indem Sie den Konfigurationswert bei der Erstellung des Clusters setzen.  Weitere Details finden Sie unter [Bereitstellen von HDInsight-Clustern][hdinsight-provision].

Die [Hive mit Tez-Designdokumente][hive-on-tez-wiki] enthalten weitere Details zu Implementierungen und Konfigurationseinstellungen.


##<a id="nextsteps"></a>Nächste Schritte

Während Hive die Datenabfrage mit einer SQL-ähnlichen Abfragesprache erleichtert, bieten andere in HDInsight verfügbare Komponenten ergänzende Funktionen wie Datenverschiebung und -transformation. Weitere Informationen finden Sie in den folgenden Artikeln:

* [Erste Schritte mit HDInsight Hadoop-Tools für Visual Studio][1]
* [Verwenden von Oozie mit HDInsight][hdinsight-use-oozie]
* [Programmgesteuerte Übermittlung von Hadoop-Jobs][hdinsight-submit-jobs]
* [Verwenden von Pig mit HDInsight](../hdinsight-use-pig/)
* [Analysieren von Daten zu Flugverspätungen mit HDInsight][hdinsight-analyze-flight-data]
* [Dokumentation zum Azure HDInsight SDK][hdinsight-sdk-documentation]
* [Hochladen von Daten zu HDInsight][hdinsight-upload-data]
* [Erste Schritte mit Azure HDInsight](../hdinsight-get-started/)


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/de-de/library/dn479185.aspx

[azure-purchase-options]: http://azure.microsoft.com/de-de/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/de-de/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/de-de/pricing/free-trial/

[apache-tez]: http://tez.apache.org
[apache-hive]: http://hive.apache.org/
[apache-log4j]: http://en.wikipedia.org/wiki/Log4j
[hive-on-tez-wiki]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
[import-to-excel]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-connect-excel-power-query/


[hdinsight-use-oozie]: ../hdinsight-use-oozie/
[hdinsight-analyze-flight-data]: ../hdinsight-analyze-flight-delay-data/



[hdinsight-storage]: ../hdinsight-use-blob-storage

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/

[Powershell-install-configure]: ../install-configure-powershell/
[powershell-here-strings]: http://technet.microsoft.com/de-de/library/ee692792.aspx

[image-hdi-hive-powershell]: ./media/hdinsight-use-hive/HDI.HIVE.PowerShell.png
[img-hdi-hive-powershell-output]: ./media/hdinsight-use-hive/HDI.Hive.PowerShell.Output.png
[image-hdi-hive-architecture]: ./media/hdinsight-use-hive/HDI.Hive.Architecture.png

<!--HONumber=35.1-->
