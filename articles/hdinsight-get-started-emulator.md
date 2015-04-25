<properties 
	pageTitle="Erste Schritte mit dem HDInsight-Emulator | Azure" 
	description="Erfahren Sie, wie Sie den HDInsight-Emulator für Azure verwenden können." 
	editor="cgronlun" 
	manager="paulettm" 
	services="hdinsight" 
	author="nitinme" 
	authors="nitinme" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/06/2015" 
	ms.author="nitinme"/>

# Erste Schritte mit dem HDInsight Emulator 

Dieses Lernprogramm beschreibt die ersten Schritte mit Hadoop-Clustern im Microsoft HDInsight-Emulator für Azure (ehemals HDInsight Server Developer-Vorschau). Der HDInsight Emulator weist die gleichen Komponenten aus der Hadoop-Umgebung auf wie Azure HDInsight. Weitere Informationen inklusive der bereitgestellten Versionen finden Sie unter [Welche Hadoop-Version verwendet Azure HDInsight?][hdinsight-versions].

>[AZURE.NOTE] Der HDInsight Emulator enthält nur ein Hadoop-Cluster. HBase ist nicht enthalten.

HDInsight Emulator stellt eine lokale Entwicklungsumgebung für Azure HDInsight zur Verfügung. Wenn Sie mit Hadoop vertraut sind, können Sie die ersten Schritte mit dem Emulator mithilfe von HDFS durchführen. HDInsight verwendet jedoch Azure-BLOB-Speicher als Standard-Dateisystem (WASB, auch bekannt als Azure-Speicher - Blobs). Es empfiehlt sich also letztlich, WASB für die Entwicklung zu verwenden. Um WASB mit dem HDInsight-Emulator verwenden zu können, müssen Sie Änderungen an der Konfiguration des HDInsight-Emulators vornehmen. 

> [AZURE.NOTE] Der HDInsight Emulator kann nur eine aus einem Knoten bestehende Bereitstellung verwenden. 


**Voraussetzungen**	
Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- Der HDInsight Emulator benötigt eine 64-Bit-Version von Windows. Eine der folgenden Anforderungen muss erfüllt sein:

	- Windows 7 Service Pack 1
	- Windows Server 2008 R2 Service Pack 1
	- Windows 8 
	- Windows Server 2012

- Installieren und konfigurieren Sie Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure]. 

## Dieses Lernprogramm umfasst folgende Punkte

* [Installieren des HDInsight Emulator](#install)
* [Ausführen des Beispiels zum Zählen von Wörtern](#runwordcount)
* [Ausführen der Erste-Schritte-Beispiele](#rungetstartedsamples)
* [Verbinden mit Azure BLOB-Speicher](#blobstorage)
* [Ausführen von HDInsight PowerShell](#powershell)
* [Entfernen des HDInsight Emulator](#remove)
* [Nächste Schritte](#nextsteps)

##<a name="install"></a>Installieren des HDInsight Emulator

Der Microsoft HDInsight Emulator kann über den Microsoft Web Platform Installer installiert werden.  

> [AZURE.NOTE] Der HDInsight Emulator unterstützt zurzeit nur englische Betriebssystemversionen. Falls Sie eine ältere Version des Emulators installiert haben, müssen Sie die folgenden beiden Komponenten über Systemsteuerung/Programme und Funktionen deinstallieren, bevor Sie die neueste Version des Emulators installieren.
><ul>
<li>Microsoft HDInsight-Emulator für Azure oder HDInsight-Entwicklervorschau, je nachdem, welche Komponente installiert ist.</li>
<li>Hortonworks Data Platform</li>
</ul>


**So installieren Sie den HDInsight Emulator**

1. Öffnen Sie Internet Explorer, und navigieren Sie dann zur [Installationsseite für Microsoft HDInsight Emulator für Azure][hdinsight-emulator-install].
2. Klicken Sie auf **Jetzt installieren**. 
3. Klicken Sie auf **Ausführen**, wenn Sie unten auf der Seite zur Installation von HDINSIGHT.exe aufgefordert werden. 
4. Klicken Sie im Fenster **Benutzerkontensteuerung**, das angezeigt wird, um die Installation abzuschließen, auf **Ja**. Das Dialogfeld des Webplattform-Installers wird angezeigt.
6. Klicken Sie unten auf der Seite auf **Installieren**.
7. Klicken Sie auf **Akzeptieren**, um den Lizenzbedingungen zuzustimmen.
8. Überprüfen Sie, ob im Web Platform Installer Folgendes angezeigt wird: **Folgende Produkte wurden erfolgreich installiert**. Klicken Sie anschließend auf **Fertig stellen**.
9. Klicken Sie auf **Beenden**, um das Web Platform Installer-Fenster zu schließen.

**So überprüfen Sie die HDInsight Emulator-Installation**
	
Auf Ihrem Desktop wurden drei Symbole installiert. Sie sind wie folgt verknüpft: 
	
- **Hadoop-Befehlszeile**: Die Hadoop-Eingabeaufforderung, von der MapReduce-, Pig- und Hive-Jobs im HDInsight Emulator ausgeführt werden.

- **Hadoop-NameNode-Status**: Der NameNode stellt ein strukturbasiertes Verzeichnis für alle Dateien in HDFS zur Verfügung. Außerdem verfolgt er, wo die Dateien für alle Dateien in einem Hadoop-Cluster aufbewahrt werden. Clients kommunizieren mit dem NameNode, um zu ermitteln, wo die Datenknoten für alle Dateien gespeichert werden.
	
- **Hadoop-Yarn-Status**: Der JobTracker, der MapReduce-Aufgaben den Knoten in einem Cluster zuordnet.

Zudem sollten mehrere lokale Dienste installiert worden sein. Nachfolgend wird ein Screenshot des Fensters mit Diensten angezeigt:

![HDI.Emulator.Services][image-hdi-emulator-services]

Die Dienste des HDInsight-Emulators werden nicht automatisch gestartet. Führen Sie in der Hadoop-Befehlszeile den Befehl **start_local_hdp_services.cmd** unter <Systemlaufwerk\>\hdp aus, um die Dienste zu starten. Führen Sie **set-onebox-autostart.cmd** aus, um die Dienste beim Computerneustart automatisch zu starten.  

Informationen zu bekannten Problemen beim Installieren und Ausführen von HDInsight Server finden Sie in den [Versionshinweisen für HDInsight Emulator][hdinsight-emulator-release-notes]. Das Installationsprotokoll befindet sich im Verzeichnis **C:\HadoopFeaturePackSetup\HadoopFeaturePackSetupTools\gettingStarted.winpkg.install.log**.


##<a name="runwordcount"></a>Ausführen eines MapReduce-Jobs zum Zählen von Wörtern

Der HDInsight Emulator ist nun auf Ihrer Arbeitsstation konfiguriert.  Sie können einen MapReduce-Auftrag ausführen, um die Installation zu testen. Laden Sie zunächst einige Textdateien in HDFS hoch und führen Sie dann einen MapReduce-Job zum Zählen von Wörtern aus, um die Wortanzahl dieser Dateien zu zählen. 

Das MapReduce-Programm zum Zählen von Wörtern wurde in der Datei *hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar* gepackt.  Die JAR-Datei ist im Ordner *C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce* gespeichert.

Der MapReduce-Job zum Zählen von Wörtern hat zwei Argumente:

- Ein Eingabeordner. Verwenden Sie *hdfs://localhost/user/HDIUser* als Eingabeordner.
- Ein Ausgabeordner. Verwenden Sie *hdfs://localhost/user/HDIUser/WordCount_Output* als Ausgabeverzeichnis. Der Ausgabeordner kann kein bereits vorhandener Ordner sein. Andernfalls schlägt der MapReduce-Job fehl. Wenn Sie den MapReduce-Job zum zweiten Mal ausführen möchten, müssen Sie entweder einen anderen Ausgabeordner angeben oder den vorhandenen Ausgabeordner löschen. 

**So führen Sie den MapReduce-Auftrag zum Zählen von Wörtern aus**

1. Doppelklicken Sie auf dem Desktop auf **Hadoop-Befehlszeile**, um das Hadoop-Befehlszeilenfenster zu öffnen.  Dies sollte der aktuelle Ordner sein:

		c:\hdp\hadoop-2.4.0.2.1.3.0-1981

	Ist dies nicht der Fall, führen Sie den folgenden Befehl aus:

		cd %hadoop_home%

2. Führen Sie die folgenden Hadoop-Befehle aus, um einen HDFS-Ordner zum Speichern der Ein- und Ausgabedateien zu erstellen:

		hadoop fs -mkdir /user
		hadoop fs -mkdir /user/HDIUser
	
3. Führen Sie den folgenden Hadoop-Befehl aus, um einige lokale Textdateien nach HDFS zu kopieren:

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common\*.txt /user/HDIUser

4. Führen Sie den folgenden Befehl aus, um die Dateien im Ordner "/user/HDIUser" aufzulisten:

		hadoop fs -ls /user/HDIUser

	Daraufhin sollten die folgenden Dateien angezeigt werden:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -ls /user/HDIUser
		Found 4 items
		-rw-r--r--   1 username hdfs     574261 2014-09-08 12:56 /user/HDIUser/CHANGES.txt
		-rw-r--r--   1 username hdfs      15748 2014-09-08 12:56 /user/HDIUser/LICENSE.txt
		-rw-r--r--   1 username hdfs        103 2014-09-08 12:56 /user/HDIUser/NOTICE.txt
		-rw-r--r--   1 username hdfs       1397 2014-09-08 12:56 /user/HDIUser/README.txt

5. Führen Sie den folgenden Befehl aus, um den MapReduce-Job zum Zählen von Wörtern auszuführen:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981> hadoop jar C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce\hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6. Führen Sie den folgenden Befehl aus, um die Wörter, die "windows" enthalten, in der Ausgabedatei aufzulisten:

		hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

	Die Ausgabe sollte wie folgt aussehen:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"
		windows 4
		windows.        2
		windows/cygwin. 1

Weitere Informationen zu Hadoop-Befehlen finden Sie im [Handbuch zu Hadoop-Befehlen][hadoop-commands-manual].

##<a name="rungetstartedsamples"></a> Ausführen der Erste Schritte-Beispiele

Die Installation des HDInsight-Emulators enthält einige Beispiele, mit denen neue Benutzer den Umgang mit Apache Hadoop-basierten Diensten unter Windows schnell lernen können. Diese Beispiele decken einige typische Aufgaben bei der Verarbeitung großer Datenmengen ab. Gehen Sie die Beispiele durch, um sich mit den Konzepten des MapReduce-Programmiermodells und dessen Ökosystem vertraut zu machen.

Die Beispiele beschäftigen sich mit der Verarbeitung von IIS W3C-Protokolldatenszenarien. Es wird ein Datengenerierungstool zur Verfügung gestellt, mit dem die Datasets erstellt und in verschiedenen Größen in HDFS oder WASB (Azure Blob-Speicher) importiert werden können. Weitere Informationen finden Sie unter [Verwenden von Azure BLOB-Speicher für HDInsight][hdinsight-storage]. MapReduce-, Pig- oder Hive-Jobs können anschließend für die Datenseiten ausgeführt werden, die vom PowerShell-Skript generiert wurden. Die Pig- und Hive-Skripts sind eine Abstraktionsebene über MapReduce und werden letztlich zu MapReduce-Programmen kompiliert. Benutzer können eine Reihe von Jobs ausführen, um die Auswirkungen dieser unterschiedlichen Technologien und der Datengröße auf die Ausführung der Verarbeitungsaufgaben zu beobachten. 

### Inhalt dieses Abschnitts

- [Die IIS w3c-Protokolldatenszenarien](#scenarios)
- [Laden von w3c-Beispielprotokolldaten](#loaddata)
- [Ausführen von Java MapReduce-Aufträgen](#javamapreduce)
- [Ausführen von Hive-Aufträgen](#hive)
- [Ausführen von Pig-Aufträgen](#pig)
- [Neuerstellung der Beispiele](#rebuild)

###<a name="scenarios"></a>Die IIS w3c-Protokolldatenszenarien

Das w3c-Szenario generiert und importiert IIS W3C-Protokolldaten in drei Größen in HDFS oder WASB: 1MB (klein), 500MB (mittel) und 2GB (groß). Es stellt drei Jobtypen zur Verfügung und implementiert jeden davon in C#, Java, Pig und Hive.

- **totalhits**: Berechnet die Gesamtzahl der Anforderungen für eine bestimmte Seite. 
- **avgtime**: Berechnet die durchschnittliche Zeit (in Sekunden), die für eine Anforderung pro Seite benötigt wird. 
- **errors**: Berechnet die Anzahl der Fehler pro Seite, pro Stunde, für Anforderungen, deren Status 404 oder 500 war. 

Diese Beispiele und ihre Dokumentation stellen keine detaillierte Studie oder vollständige Implementierung der wesentlichen Hadoop-Technologien zur Verfügung. Der verwendete Cluster hat nur einen einzigen Knoten. Daher kann die Auswirkung des Hinzufügens weiterer Knoten in dieser Version nicht beobachtet werden. 

###<a name="loaddata"></a>Laden von w3c-Beispielprotokolldaten

Das Generieren der Daten und Importieren in HDFS erfolgt über das PowerShell-Skript "importdata.ps1".

**So importieren Sie w3c-Beispielprotokolldaten**

1. Öffnen Sie die Hadoop-Befehlszeile auf dem Desktop.
2. Wechseln Sie zum Verzeichnis **C:\hdp\GettingStarted**.
3. Führen Sie den folgenden Befehl aus, um Daten zu generieren und in HDFS zu importieren:

		powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted 

	Wenn Sie stattdessen Daten in WASB laden möchten, finden Sie weitere Informationen unter [Verbinden mit Azure BLOB-Speicher](#blobstorage).

4. Führen Sie den folgenden Befehl in der Hadoop-Befehlszeile aus, um die importierten Dateien nach HDFS zu kopieren:

		hadoop fs -ls -R /w3c

	Die Ausgabe sollte in etwa wie folgt aussehen: 

		C:\hdp\GettingStarted>hadoop fs -ls -R /w3c
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:41 /w3c/input/large
		-rw-r--r--   1 username hdfs  543683503 2014-09-08 15:41 /w3c/input/large/data_w3c_large.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:40 /w3c/input/medium
		-rw-r--r--   1 username hdfs  272435159 2014-09-08 15:40 /w3c/input/medium/data_w3c_medium.txt
		drwxr-xr-x   - username hdfs          0 2014-09-08 15:39 /w3c/input/small
		-rw-r--r--   1 username hdfs    1058423 2014-09-08 15:39 /w3c/input/small/data_w3c_small.txt

5. Falls Sie die Dateiinhalte anzeigen möchten, können Sie den folgenden Befehl ausführen, um eine der Datendateien im Konsolenfenster anzuzeigen:

		hadoop fs -cat /w3c/input/small/data_w3c_small.txt

Die Datendatei ist nun erstellt und in HDFS importiert. Sie können nun verschiedene Hadoop-Jobs ausführen.

###<a name="javamapreduce"></a> Ausführen von Java MapReduce-Aufträgen

MapReduce ist die grundlegende Compute Engine für Hadoop. Sie ist standardmäßig in Java implementiert. Es gibt jedoch auch Beispiele, bei denen .NET und Hadoop Streaming in C# verwendet werden. Für die Ausführung eines MapReduce-Jobs gilt folgende Syntax:

	hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

Die JAR-Datei und die Quelldateien befinden sich im Ordner "C:\Hadoop\GettingStarted\Java".

**So führen Sie einen MapReduce-Auftrag zur Ermittlung von Webseitentreffern aus**

1. Öffnen Sie die Hadoop-Befehlszeile.
2. Wechseln Sie zum Verzeichnis **C:\hdp\GettingStarted**.
3. Führen Sie den folgenden Befehl aus, um das Ausgabeverzeichnis zu entfernen, für den Fall, dass der Ordner bereits vorhanden ist.  Der MapReduce-Job schlägt fehlt, wenn der Ausgabeordner bereits existiert.

		hadoop fs -rm -r /w3c/output

3. Führen Sie den folgenden Befehl aus:

		hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

	Die folgende Tabelle beschreibt die Elemente des Befehls:
	<table border="1">
	<tr><td>Parameter</td><td>Hinweis</td></tr>
	<tr><td>w3c_scenarios.jar</td><td>Die JAR-Datei befindet sich im Ordner C:\hdp\GettingStarted\Java.</td></tr>
	<tr><td>microsoft.hadoop.w3c.TotalHitsForPage</td><td>Dieser Typ kann durch einen der folgenden Typen ersetzt werden: 
	<ul>
	<li>microsoft.hadoop.w3c.AverageTimeTaken</li>
	<li>microsoft.hadoop.w3c.ErrorsByPage</li>
	</ul></td></tr>
	<tr><td>/w3c/input/small/data_w3c_small.txt</td><td>Die Eingabedatei kann durch die folgenden Dateien ersetzt werden:
	<ul>
	<li>/w3c/input/medium/data_w3c_medium.txt</li>
	<li>/w3c/input/large/data_w3c_large.txt</li>
	</ul></td></tr>
	<tr><td>/w3c/output</td><td>Dies ist der Name des Ausgabeordners.</td></tr>
	</table>

4. Führen Sie den folgenden Befehl aus, um die Ausgabedatei anzuzeigen:

		hadoop fs -cat /w3c/output/part-00000

	Die Ausgabe sollte der Folgenden ähneln:

		c:\Hadoop\GettingStarted\Java>hadoop fs -cat /w3c/output/part-00000
		/Default.aspx   3360
		/Info.aspx      1156
		/UserService    1137

	Die Seite "Default.aspx" erzielt 3360 Treffer, und so weiter. Führen Sie die Befehle erneut aus, ersetzen Sie die Werte anhand der Vorschläge in der obigen Tabelle und beobachten Sie, wie sich die Ausgabe je nach Jobtyp und Größe der Daten ändert.

### <a name="hive"></a>Ausführen von Hive-Aufträgen
Die Hive-Abfrage-Engine wird Analytikern mit guten SQL-Kenntnissen vertraut vorkommen. Sie bietet eine SQL-ähnliche Schnittstelle und ein relationales Datenmodell für HDFS. Hive verwendet die Sprache HiveQL, die sich sehr ähnlich verhält wie SQL. Hive bietet eine Abstraktionsebene über dem Java-basierten MapReduce-Framework, und die Hive-Abfragen werden zur Laufzeit nach MapReduce kompiliert.

**So führen Sie einen Hive-Auftrag aus**

1. Öffnen Sie die Hadoop-Befehlszeile.
2. Wechseln Sie zum Verzeichnis **C:\hdp\GettingStarted**.
3. Führen Sie den folgenden Befehl aus, um den Ordner **/w3c/hive/input** zu entfernen, für den Fall, dass er bereits vorhanden ist.  Der Hive-Job schlägt fehl, falls der Ordner bereits existiert.

		hadoop fs -rmr /w3c/hive/input

4. Führen Sie den folgenden Befehl aus, um die Ordner **/w3c/hive/input** zu erstellen und die Datendateien in den Ordner "/hive/input" zu kopieren:

        hadoop fs -mkdir /w3c/hive
		hadoop fs -mkdir /w3c/hive/input
        
		hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5. Führen Sie den folgenden Befehl aus, um die Skriptdatei **w3ccreate.hql** auszuführen.  Das Skript erstellt eine Hive-Tabelle und lädt Daten in die Hive-Tabelle:
        
		C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

	Die Ausgabe sollte in etwa wie folgt aussehen:

		Logging initialized using configuration in file:/C:/hdp/hive-0.13.0.2.1.3.0-1981	/conf/hive-log4j.properties
		OK
		Time taken: 1.137 seconds
		OK
		Time taken: 4.403 seconds
		Loading data to table default.w3c
		Moved: 'hdfs://HDINSIGHT02:8020/hive/warehouse/w3c' to trash at: hdfs://HDINSIGHT02:8020/user/<username>/.Trash/Current
		Table default.w3c stats: [numFiles=1, numRows=0, totalSize=1058423, rawDataSize=0]
		OK
		Time taken: 2.881 seconds

6. Führen Sie den folgenden Befehl aus, um die HiveQL-Skriptdatei **w3ctotalhitsbypage.hql** auszuführen.  

        C:\hdp\hive-0.13.0\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

	Die folgende Tabelle beschreibt die Elemente des Befehls:
	<table border="1">
	<tr><td>Datei</td><td>Beschreibung</td></tr>
	<tr><td>C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd</td><td>Das Hive-Befehlsskript</td></tr>
	<tr><td>C:\hdp\GettingStarted\Hive\w3c\w3ctotalhitsbypage.hql</td><td> Sie können die Hive-Skriptdatei durch eine der folgenden Dateien ersetzen:
	<ul>
	<li>C:\hdp\GettingStarted\Hive\w3c\w3caveragetimetaken.hql</li>
	<li>C:\hdp\GettingStarted\Hive\w3c\w3cerrorsbypage.hql</li>
	</ul>
	</td></tr>

	</table>

	Das HiveQL-Skript "w3ctotalhitsbypage.hql" sieht wie folgt aus:

		SELECT filtered.cs_uri_stem,COUNT(*) 
		FROM (
		  SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
		) filtered
		GROUP BY (filtered.cs_uri_stem);

	Das Ende der Ausgabe sollte in etwa wie folgt aussehen:
		
		MapReduce Total cumulative CPU time: 5 seconds 391 msec
		Ended Job = job_1410201800143_0008
		MapReduce Jobs Launched:
		Job 0: Map: 1  Reduce: 1   Cumulative CPU: 5.391 sec   HDFS Read: 1058638 HDFS Write: 53 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 391 msec
		OK
		/Default.aspx   3360
		/Info.aspx      1156
		/UserService    1137
		Time taken: 49.304 seconds, Fetched: 3 row(s)

Beachten Sie, dass als erster Schritt bei jedem der Jobs eine Tabelle erstellt wird und dann Daten aus der zuvor erstellten Datei in die Tabelle geladen werden. Sie können zu der erstellten Datei navigieren, indem Sie mit dem folgenden Befehl unter dem Knoten "/Hive" in HDFS suchen:

	hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Ausführen von Pig-Aufträgen

Bei der Pig-Verarbeitung wird eine als *Pig Latin* bezeichnete Datenflusssprache verwendet. Pig Latin-Abstraktionen stellen komplexere Datenstrukturen als MapReduce zur Verfügung und führen für Hadoop die Vorgänge durch, die SQL für RDBMS-Systeme durchführt. 


**So führen Sie Pig-Aufträge aus:**

1. Öffnen Sie die Hadoop-Befehlszeile.
2. Wechseln Sie das Verzeichnis zum Ordner **C:\hdp\GettingStarted**.
3. Führen Sie den folgenden Befehl aus, um einen Pig-Job zu übermitteln:

		C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

	Die folgende Tabelle beschreibt die Elemente des Befehls:
	<table border="1">
	<tr><td>Datei</td><td>Beschreibung</td></tr>
	<tr><td>C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd</td><td>Das Pig-Befehlsskript</td></tr>
	<tr><td>C:\hdp\GettingStarted\Pig\w3c\TotalHitsForPage.pig</td><td> Sie können die Pig Latin-Skriptdatei durch eine der folgenden Dateien ersetzen:
	<ul>
	<li>C:\hdp\GettingStarted\Pig\w3c\AverageTimeTaken.pig</li>
	<li>C:\hdp\GettingStarted\Pig\w3c\ErrorsByPage.pig</li>
	</ul>
	</td></tr>
	<tr><td>/w3c/input/small/data_w3c_small.txt</td><td> Sie können den Parameter durch eine größere Datei ersetzen:
	
	<ul>
	<li>/w3c/input/medium/data_w3c_medium.txt</li>
	<li>/w3c/input/large/data_w3c_large.txt</li>
	</ul>
	
	</td></tr>
	</table>

	Die Ausgabe sollte in etwa wie folgt aussehen:

		(/Info.aspx,1156)
		(/UserService,1137)
		(/Default.aspx,3360)
		
Beachten Sie, dass möglicherweise im Verlauf eines Pig-Jobs mehrere MapReduce-Jobs ausgeführt werden. Dies liegt daran, dass ein Pig-Skript unter Umständen in mehrere MapReduce-Jobs kompiliert wird.


### <a name="rebuild"></a>Neuerstellung der Beispiele
Die Beispiele enthalten momentan alle erforderlichen Binärdateien und müssen daher nicht erstellt werden. Wenn Sie Änderungen an den Java- oder .NET-Beispielen vornehmen möchten, können Sie sie entweder mit MSBuild oder dem enthaltenen PowerShell-Skript neu erstellen.

**So erstellen Sie Beispiele neu**

1. Öffnen Sie die Hadoop-Befehlszeile.
2. Führen Sie den folgenden Befehl aus:

		powershell -F buildsamples.ps1


##<a name="blobstorage"></a>Verbinden mit Azure Blob-Speicher
Der HDInsight-Emulator verwendet HDFS als Standard-Dateisystem. Azure HDInsight verwendet jedoch Azure-BLOB-Speicher als Standard-Dateisystem. Der HDInsight-Emulator kann jedoch so konfiguriert werden, dass er Azure-BLOB-Speicher anstelle des lokalen Speichers verwendet. Führen Sie die folgenden Schritte aus, um einen Speichercontainer in Azure zu erstellen und mit dem HDInsight-Emulator zu verbinden.

>[AZURE.NOTE] Weitere Informationen zur Verwendung des Azure BLOB-Speichers durch HDInsight finden Sie unter [Verwenden des Azure BLOB-Speichers mit HDInsight][hdinsight-storage].

Sie benötigen ein Speicherkonto, um die folgenden Schritte ausführen zu können. Anweisungen hierzu finden Sie unter [Erstellen eines Speicherkontos][azure-create-storage-account].

**So erstellen Sie einen Container**

1. Melden Sie sich beim [Verwaltungsportal][azure-management-portal] an.
2. Klicken Sie auf der linken Seite auf **SPEICHER**. Eine Liste der Speicherkonten für Ihr Abonnement wird angezeigt.
3. Klicken Sie auf das Speicherkonto in der Liste, für das Sie den Container erstellen möchten.
4. Klicken Sie oben auf der Seite auf **CONTAINER**.
5. Klicken Sie am unteren Rand der Seite auf **HINZUFÜGEN**.
6. Geben Sie den **NAMEN** ein, und wählen Sie **ZUGRIFF** aus. Sie können jede der drei Zugriffsstufen verwenden.  Die Standardstufe ist **Privat**.
7. Klicken Sie zum Speichern der Änderungen auf **OK**. Der neue Container sollte nun im Portal aufgeführt sein.

Bevor Sie auf ein Azure-Speicherkonto zugreifen können, müssen Sie der Konfigurationsdatei den Kontonamen und den Kontoschlüssel hinzufügen.

**So konfigurieren Sie die Verbindung mit einem Azure-Speicherkonto**

1. Öffnen Sie die Datei **C:\hdp\hadoop-2.4.0.2.1.3.0-1981\etc\hadoop\core-site.xml** im Editor.
2. Fügen Sie das folgende Tag <property\> neben den anderen Tags <property\> hinzu:

		<property>
		    <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
		    <value><StorageAccountKey></value>
		</property>

	Sie müssen <StorageAccountName\> und <StorageAccountKey\> durch die Werte ersetzen, die den Speicherkontoinformationen entsprechen.

3. Speichern Sie die Änderungen.  Ein Neustart der Hadoop-Dienste ist nicht erforderlich.

Verwenden Sie die folgende Syntax für den Zugriff auf das Speicherkonto:

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

Beispiel:

	hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/


##<a name="powershell"></a> Ausführen von HDInsight PowerShell
Einige der HDInsight PowerShell-Cmdlets werden im HDInsight-Emulator ebenfalls unterstützt. Zu diesem Cmdlets zählen die Folgenden:

- Cmdlets für die HDInsight-Jobdefinition
	
	- New-AzureHDInsightSqoopJobDefinition
	- New-AzureHDInsightStreamingMapReduceJobDefinition
	- New-AzureHDInsightPigJobDefinition                                                                                          
	- New-AzureHDInsightHiveJobDefinition                                                                                           
	- New-AzureHDInsightMapReduceJobDefinition
- Start-AzureHDInsightJob
- Get-AzureHDInsightJob
- Wait-AzureHDInsightJob

Nachfolgend finden Sie ein Beispiel für die Übermittlung eines Hadoop-Jobs:

	$creds = Get-Credential (hadoop as username, password can be anything)
	$hdinsightJob = <JobDefinition>
	Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

Sie werden zu einer Eingabe aufgefordert, wenn Sie Get-Credential aufrufen. Sie müssen **hadoop** als Benutzernamen verwenden. Das Kennwort kann eine beliebige Zeichenfolge sein. Der Clustername ist immer **http://localhost:50111**.

Weitere Informationen zur Übermittlung von Hadoop-Aufträgen finden Sie unter [Programmgesteuerte Übermittlung von Hadoop-Aufträgen][hdinsight-submit-jobs]. Weitere Informationen zu den HDInsight PowerShell-Cmdlets finden Sie unter [HDInsight-Cmdlet-Referenz][hdinsight-powershell-reference].


##<a name="remove"></a> Entfernen des HDInsight Emulator
Öffnen Sie auf dem Computer, auf dem der Emulator installiert ist, die Systemsteuerung, und klicken Sie unter **Programme** auf **Programm deinstallieren**. Klicken Sie in der Liste der installierten Programme mit der rechten Maustaste auf **Microsoft HDInsight Emulator für Azure** und dann auf **Deinstallieren**. 


##<a name="nextsteps"></a> Nächste Schritte
In diesem Lernprogramm haben Sie HDInsight Emulator installiert und einige Hadoop-Jobs ausgeführt. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Erste Schritte mit Azure HDInsight][hdinsight-get-started]
- [Entwickeln von Java MapReduce-Programmen für HDInsight][hdinsight-develop-mapreduce]
- [Entwickeln von c# Hadoop-MapReduce-Streamingprogrammen für HDInsight][hdinsight-develop-deploy-streaming]
- [Versionshinweise für HDInsight Emulator][hdinsight-emulator-release-notes]
- [MSDN-Forum zur Erörterung von HDInsight](http://social.msdn.microsoft.com/Forums/en-US/hdinsight)



[azure-sdk]: http://azure.microsoft.com/downloads/
[azure-create-storage-account]: ../storage-create-storage-account/
[azure-management-portal]: https://manage.windowsazure.com/
[netstat-url]: http://technet.microsoft.com/library/ff961504.aspx

[hdinsight-develop-mapreduce]: ../hdinsight-develop-deploy-java-mapreduce/

[hdinsight-emulator-install]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
[hdinsight-emulator-release-notes]: ../hdinsight-emulator-release-notes/

[hdinsight-storage]: ../hdinsight-use-blob-storage/
[hdinsight-submit-jobs]: ../hdinsight-submit-hadoop-jobs-programmatically/
[hdinsight-powershell-reference]: http://msdn.microsoft.com/library/windowsazure/dn479228.aspx
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-develop-deploy-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-versions]: ../hdinsight-component-versioning/

[Powershell-install-configure]: ../install-configure-powershell/

[hadoop-commands-manual]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html

[image-hdi-emulator-services]: ./media/hdinsight-get-started-emulator/HDI.Emulator.Services.png 

<!--HONumber=42-->
