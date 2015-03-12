<properties 
	pageTitle="Verwenden von Python mit Hive und Pig in Azure HDInsight" 
	description="Erfahren Sie, wie Sie Benutzerdefinierte Python-Funktionen mit Hive und Pig in Azure HDInsight verwenden können." 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="09/17/2014" 
	ms.author="larryfr"/>

#Verwenden von Python mit Hive und Pig in HDInsight

Hive und Pig sind großartig für die Arbeit mit Daten in HDInsight. Manchmal benötigt man aber eine allgemeinere Sprache. Sowohl Hive als auch Pig ermöglichen Ihnen die Erstellung benutzerdefinierter Funktionen (User Defined Functions, UDF) mithilfe einer Vielzahl von Programmiersprachen. In diesem Artikel erfahren Sie, wie Sie eine Python-UDF von Hive und Pig aus verwenden.

> [AZURE.NOTE] Die Schritte in diesem Artikel beziehen sich auf die HDInsight-Clusterversionen 2.1, 3.0 und 3.1.

##Inhaltsverzeichnis

* [Python in HDInsight](#python)
*   [Hive und Python](#hivepython)
*   [Pig und Python](#pigpython)
* [Ausführen der Beispiele](#running)
* [Problembehandlung](#troubleshooting)
* [Nächste Schritte](#next)

##<a name="python"></a>Python auf HDInsight

Python 2.7 ist auf HDInsight 3.0-Clustern standardmäßig installiert. Die Installation findet sich im Verzeichnis D:\Python. Hive kann mit dieser Version von Python für die Streamverarbeitung verwendet werden (Daten werden zwischen Hive und Python mithilfe von STDOUT/STDIN weitergegeben).

HDInsight enthält außerdem Jython, eine in Java geschriebene Python-Implementierung. Pig kann ohne Streaming mit Jython kommunizieren, weshalb es bei der Arbeit mit Pig vorzuziehen ist.

###<a name="hivepython"></a>Hive und Python

Python kann als UDF von Hive aus mittels der HiveQL-Anweisung **TRANSFORM** verwendet werden. Das folgende HiveQL ruft beispielsweise ein Python-Skript auf, das in der Datei **streaming.py** gespeichert ist.

	add file wasb:///streaming.py;
	
	SELECT TRANSFORM (clientid, devicemake, devicemodel)
	  USING 'D:\Python27\python.exe streaming.py' AS
	  (clientid string, phoneLable string, phoneHash string)
	FROM hivesampletable
	ORDER BY clientid LIMIT 50;

Das Beispiel tut Folgendes:

1. Die Anweisung **add file** am Anfang der Datei fügt die Datei **streaming.py** dem verteilten Cache hinzu, sodass sie von allen Knoten im Cluster aus zugänglich ist.

2. Die Anweisung  **SELECT TRANSFORM ... USING 'D:\Python27\python.exe streaming.py'** wählt Daten aus **hivesampletable** aus und übergibt "clientid", "devicemake" und "devicemodel" an das Skript **streaming.py**.

	> [AZURE.NOTE] Die **USING**-Klausel legt den vollständigen Pfad zu "python.exe" fest, da sich die Datei nicht im Pfad ist.

3. Die **AS**-Klausel beschreibt die Felder, die von **streaming.py** zurückgegeben werden.


<a name="streamingpy"></a>
Dies ist die Datei **streaming.py**, die im HiveQL-Beispiel verwendet wird.

	import sys
	import string
	import hashlib
	
	while True:
	  line = sys.stdin.readline()
	  if not line:
	    break
	
	  line = string.strip(line, "\n ")
	  clientid, devicemake, devicemodel = string.split(line, "\t")
	  phone_label = devicemake + ' ' + devicemodel
	  print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])

Da wir Streaming verwenden, muss das Skript Folgendes tun:

1. Lesen der Daten von STDIN. Das wird durch Verwendung von `sys.stdin.readline()` in diesem Beispiel erreicht.

2. Das Zeilenumbruchzeichen am Ende wird mit `string.strip(line, "\n ")` entfernt, da wir nur die Textdaten und keinen Indikator für das Zeilenende benötigen.

2. Bei der Streamverarbeitung enthält eine einzelne Zeile alle Werte, jeweils getrennt durch ein Tabulatorzeichen. Deshalb kann `string.split(line, "\t")` zum Teilen der Eingabe bei jedem Tab verwendet werden, sodass nur die Felder zurückgeben werden.

3. Wenn die Verarbeitung abgeschlossen ist, muss die Ausgabe als eine Zeile nach STDOUT geschrieben werden mit einem Tabulator zwischen jedem Feld. Dies wird mithilfe von `print "\t".join([clientid, phone_label, hashlib.md5(phone_label).hexdigest()])` erreicht.

4. All das ereignet sich in einer `while`-Schleife, die sich wiederholt, bis keine `line` mehr gelesen wird; dann verlässt `break` die Schleife, und das Skript wird beendet.

Danach verkettet das Skript einfach die Eingabewerte für `devicemake` und `devicemodel` und berechnet einen Hash der verketteten Werte. Ziemlich einfach, aber es beschreibt die Grundlagen zur Funktionsweise aller Python-Skripte, die von Hive aus aufgerufen werden: Schleife, lese Eingabe bis es keine mehr gibt, teile jede Eingabezeile an den Tabulatorzeichen, Verarbeiten, Schreiben einer einzigen Zeile mit tabulatorgetrennter Ausgabe.

Unter [Ausführen der Beispiele](#running) finden Sie weitere Informationen zum Ausführen dieses Beispiels in Ihrem HDInsight-Cluster.

###<a name="pigpython"></a>Pig und Python

Ein Python-Skript kann als UDF von Pig aus mittels der **GENERATE**-Anweisung verwendet werden. Das folgende HiveQL verwendet beispielsweise ein Python-Skript, das in der Datei **jython.py** gespeichert ist.

	Register 'wasb:///jython.py' using jython as myfuncs;
    LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);
    LOG = FILTER LOGS by LINE is not null;
    DETAILS = FOREACH LOG GENERATE myfuncs.create_structure(LINE);
    DUMP DETAILS;

So funktioniert dieses Beispiel:

1. Es registriert die Datei, die das Python-Skript (**jython.py**) enthält, mithilfe von **Jython** und gibt es als **myfuncs** an Pig weiter. Jython ist ein Python-Implementierung in Java und läuft auf demselben virtuellen Java-Computer wie Pig. Damit kann das Python-Skript genau wie ein traditioneller Funktionsaufruf behandelt werden, im Gegensatz zum Streamingansatz von Hive.

2. Die nächste Zeile lädt die Datei mit den Beispieldaten, **sample.log**, in **LOGS**. Da diese Protokolldatei kein gleichbleibendes Schema hat, definiert sie außerdem jeden Datensatz (in diesem Fall **LINE**) als ein **chararray**. Chararray ist im Grunde genommen eine Zeichenfolge.

3. Die dritte Zeile filtert etwaige NULL-Werte heraus und speichert die Ergebnisse des Vorgangs in **LOG**.

4. Dann werden die Datensätze in **LOG** durchlaufen, und mithilfe von **GENERATE** wird die Methode **create_structure** aufgerufen, die im Skript **jython.py** enthalten ist und als **myfuncs** geladen wird.  **LINE** wird für die Übergabe des aktuellen Datensatzes an die Funktion verwendet.

5. Schließlich werden die ausgegebenen Daten mit dem Befehl **DUMP** in "STDOUT" geschrieben. Das geschieht nur, damit die Ergebnisse direkt nach dem Ende des Vorgangs angezeigt werden; in einem echten Skript würden Sie die Daten normalerweise in einer neuen Datei speichern (**STORE**).

<a name="jythonpy"></a>
Dies ist die Datei **jython.py**, die im Pig-Beispiel verwendet wird.

	@outputSchema("log: {(date:chararray, time:chararray, classname:chararray, level:chararray, detail:chararray)}")
	def create_structure(input):
	  if (input.startswith('java.lang.Exception')):
	    input = input[21:len(input)] + ' - java.lang.Exception'
	  date, time, classname, level, detail = input.split(' ', 4)
	  return date, time, classname, level, detail

Sie erinnern sich daran, dass wir vorhin die **LINE**-Eingabe einfach als Chararray definiert haben, denn es gab kein gleichbleibendes Schema für die Eingabe. **jython.py** transformiert hingegen die Daten in ein gleichbleibendes Schema für die Ausgabe. Das funktioniert folgendermaßen:

1. Die Anweisung **@outputSchema** definiert das Format der Daten, die an Pig zurückgegeben werden. In diesem Fall ist das ein Datenbehälter (**Data Bag**), also ein Pig-Datentyp. Der Behälter enthält folgende Felder, die alle Chararray (Zeichenfolgen) sind:

	* date - das Datum, an dem der Protokolleintrag erstellt wurde
	* time - die Zeit, zu der der Protokolleintrag erstellt wurde
	* classname - der Klassenname, für den der Eintrag erstellt wurde
	* level - die Protokollierungsebene
	* detail - ausführliche Details des Protokolleintrags

2. Dann definiert **def create_structure(input)** die Funktion, an die Pig Positionen weitergibt.

3. Die Beispieldatei **sample.log** entspricht weitgehend dem Schema für Datum, Uhrzeit, Klassenname, Ebene und Detail, das wir zurückgeben möchten. Sie enthält aber auch ein paar Zeilen, die mit der Zeichenfolge "*java.lang.Exception*" beginnen, die geändert werden muss, damit sie dem Schema entspricht. Die Anweisung **if** überprüft deren Vorhandensein, weist dann die Eingabedaten an, die Zeichenfolge "*java.lang.Exception*" ans Ende zu stellen, sodass die Daten dem erwarteten Ausgabeschema entsprechen.

4. Als Nächstes wird der Befehl **split** zum Aufteilen der Daten bei den ersten vier Leerzeichen verwendet. Das führt zu fünf Werten, die **date**, **time**, **classname**, **level** und **detail** zugewiesen werden.

5. Zuletzt werden die Werte an Pig zurückgegeben.

Wenn die Daten an Pig zurückgegeben werden, haben sie ein gleichbleibendes Schema wie in der Anweisung **@outputSchema** definiert.

Unter [Ausführen der Beispiele](#running) finden Sie weitere Informationen zum Ausführen dieses Beispiels in Ihrem HDInsight-Cluster.

##<a name="running"></a>Ausführen der Beispiele

Diese Schritte verwenden Microsoft Azure PowerShell. Wenn es nicht bereits auf Ihrem Entwicklungsrechner installiert und konfiguriert ist, befolgen Sie die Schritte unter [Installieren und Konfigurieren von Azure PowerShell](http://azure.microsoft.com/documentation/articles/install-configure-powershell/).


1. Verwenden Sie die Python-Beispiele [streaming.py](#streamingpy) und [jython.py](#jythonpy), und erstellen Sie lokale Kopien der Dateien auf Ihrem Entwicklungsrechner.

2. Verwenden Sie  das folgende PowerShell-Skript zum Hochladen der Dateien **streaming.py** und **jython.py** auf den Server. Ersetzen Sie den Namen Ihres Azure HDInsight-Clusters und den Pfad zu den Dateien **streaming.py** und **jython.py** in den ersten drei Skriptzeilen.

		$clusterName = YourHDIClusterName
		$pathToStreamingFile = "C:\path\to\streaming.py"
		$pathToJythonFile = "C:\path\to\jython.py"

		$hdiStore = get-azurehdinsightcluster -name $clusterName
		$storageAccountName = $hdiStore.DefaultStorageAccount.StorageAccountName.Split(".",2)[0]
		$storageAccountKey = $hdiStore.defaultstorageaccount.storageaccountkey
		$defaultContainer = $hdiStore.DefaultStorageAccount.StorageContainerName
		
		$destContext = new-azurestoragecontext -storageaccountname $storageAccountName -storageaccountkey $storageAccountKey
		set-azurestorageblobcontent -file $pathToStreamingFile -Container $defaultContainer -Blob "streaming.py" -context $destContext
		set-azurestorageblobcontent -file $pathToJythonFile -Container $defaultContainer -Blob "jython.py" -context $destContext

	Dieses Skript ruft Informationen für Ihren HDInsight-Cluster ab, extrahiert dann das Konto und den Schlüssel für das standardmäßigen Speicherkonto und lädt die Dateien in den Stamm des Containers.

	> [AZURE.NOTE] Andere Methoden zum Hochladen der Skripts finden sich im Dokument [Hochladen von Daten für Hadoop-Aufträge in HDInsight](/de-de/documentation/articles/hdinsight-upload-data/).

###Verwenden des Hive-Dashboards (nur Hive-Beispiel)

1. Öffnen Sie nach dem Hochladen der Datei einen Webbrowser und navigieren Sie zu https://YourClusterName.azurehdinsight.net/. Wenn Sie zur Eingabe von Anmeldeinformationen aufgefordert werden, geben Sie den Admin-Benutzernamen und das -Kennwort für Ihren Cluster ein.

	> [AZURE.NOTE] Sie können auch den Link **Cluster verwalten** am unteren Rand des HDInsight-**Dashboards** im Azure-Verwaltungsportal verwenden, um das Hive-Dashboard zu starten.

2. Verwenden Sie den **Hive Editor**, und ersetzen Sie damit die Zeile `select * from hivesampletable` mit dem folgenden HiveQL.

		add file wasb:///streaming.py;
		SELECT TRANSFORM (clientid, devicemake, devicemodel)
		  USING 'D:\Python27\python.exe streaming.py' AS
		  (clientid string, phoneLable string, phoneHash string)
		FROM hivesampletable
		ORDER BY clientid LIMIT 50;

3. Klicken Sie auf die Schaltfläche **Senden**, um den Auftrag abzusenden. Je nach Version des HDInsight-Clusters werden Sie möglicherweise auf die Seite "Job-Details" weitergeleitet. Wenn nicht, wählen Sie **Details anzeigen** im Bereich **Auftragssitzung** unten auf der Seite aus.

4. Die Seite **Auftragsdetails** wird neu geladen, bis der Auftrag abgeschlossen ist. Nachdem das der Fall ist, werden Informationen über den Job sowie die Ausgabe angezeigt.

###Verwenden von PowerShell (Hive- und Pig-Beispiele)

Verwenden Sie nach dem Hochladen der Dateien die folgenden PowerShell-Skripts zum Starten der Jobs. Wenn der Job abgeschlossen ist, sollte die Ausgabe zur PowerShell-Konsole geschrieben werden.

**So führen Sie den Hive-Auftrag aus**
    
    # Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

	$HiveQuery = "add file wasb:///streaming.py;" +
	             "SELECT TRANSFORM (clientid, devicemake, devicemodel) " +
	               "USING 'D:\Python27\python.exe streaming.py' AS " +
	               "(clientid string, phoneLable string, phoneHash string) " +
	             "FROM hivesampletable " +
	             "ORDER BY clientid LIMIT 50;"
	
	$jobDefinition = New-AzureHDInsightHiveJobDefinition -Query $HiveQuery -StatusFolder '/hivepython'
	
	$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
	Write-Host "Wait for the Hive job to complete ..." -ForegroundColor Green
	Wait-AzureHDInsightJob -Job $job
    # Uncomment the following to see stderr output
    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

Die Ausgabe für den **Hive**-Auftrag sollte ungefähr wie folgt aussehen:

	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100041	RIM 9650	d476f3687700442549a83fac4560c51c
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9
	100042	Apple iPhone 4.2.x	375ad9a0ddc4351536804f1d5d0ea9b9

**So führen Sie den Pig-Auftrag aus**

	# Replace 'YourHDIClusterName' with the name of your cluster
	$clusterName = YourHDIClusterName

	$PigQuery = "Register wasb:///jython.py using jython as myfuncs;" +
	            "LOGS = LOAD 'wasb:///example/data/sample.log' as (LINE:chararray);" +
	            "LOG = FILTER LOGS by LINE is not null;" +
	            "DETAILS = foreach LOG generate myfuncs.create_structure(LINE);" +
	            "DUMP DETAILS;"
	
	$jobDefinition = New-AzureHDInsightPigJobDefinition -Query $PigQuery -StatusFolder '/pigpython'
	
	$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
	Write-Host "Wait for the Pig job to complete ..." -ForegroundColor Green
	Wait-AzureHDInsightJob -Job $job
    # Uncomment the following to see stderr output
    # Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName
	Write-Host "Display the standard output ..." -ForegroundColor Green
	Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

Die Ausgabe für den **Pig**-Job sollte ungefähr wie folgt aussehen:

	((2012-02-03,20:11:56,SampleClass5,[TRACE],verbose detail for id 990982084))
	((2012-02-03,20:11:56,SampleClass7,[TRACE],verbose detail for id 1560323914))
	((2012-02-03,20:11:56,SampleClass8,[DEBUG],detail for id 2083681507))
	((2012-02-03,20:11:56,SampleClass3,[TRACE],verbose detail for id 1718828806))
	((2012-02-03,20:11:56,SampleClass3,[INFO],everything normal for id 530537821))

##<a name="troubleshooting"></a>Problembehandlung

Beide für das Ausführen der Beispiele verwendete PowerShell-Beispielskripte enthalten eine Kommentarzeile, die Fehler bei der Ausgabe des Jobs anzeigt. Wenn Sie nicht die erwartete Ausgabe für den Job sehen, kommentieren Sie die folgende Zeile aus und sehen Sie nach, ob die Fehlerinformation auf ein Problem hinweist.

	# Get-AzureHDInsightJobOutput -StandardError -JobId $job.JobId -Cluster $clusterName

Die Fehlermeldung (STDERR) und das Ergebnis des Jobs (STDERR) werden ebenfalls im standardmäßigen BLOB-Container an folgenden Orten für Ihren Cluster protokolliert.

<table>
<tr>
<td>Für diesen Job...</td><td>Sehen Sie sich diese Dateien im BLOB-Container an</td>
</tr>
<tr>
<td>Hive</td><td>/HivePython/stderr<br />/HivePython/stdout</td>
</tr>
<td>Pig</td><td>/PigPython/stderr<br />/PigPython/stdout</td>
</tr>
</table>

##<a name="next"></a>Nächste Schritte

Wenn Sie Python-Module laden müssen, die standardmäßig nicht bereitgestellt werden, finden Sie dazu im englischsprachigen Blog-Beitrag [How to deploy a Python module to Windows Azure HDInsight](http://blogs.msdn.com/b/benjguin/archive/2014/03/03/how-to-deploy-a-python-module-to-windows-azure-hdinsight.aspx) (Bereitstellen eines Moduls für Azure HDInsight) ein Beispiel.

Wenn Sie Aufträge remote auf HDInsight ohne PowerShell ausführen möchten, finden Sie im englischsprachigen Blog-Beitrag [How to use Azure HDInsight from Linux](http://blogs.msdn.com/b/benjguin/archive/2014/02/18/how-to-use-hdinsight-from-linux.aspx) (Verwenden von Azure HDInsight unter Linux) ein Beispiel, wie Sie mit Python-Aufträge über die WebHCat REST API ausführen.
<!--HONumber=42-->
