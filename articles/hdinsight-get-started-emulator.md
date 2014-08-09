<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started with the HDInsight Emulator | Azure" metaKeywords="hdinsight, Azure hdinsight, hdinsight azure, get started hdinsight, emulator, hdinsight emulator" description="Learn how to use HDInsight Emulator for Azure." umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" services="hdinsight" title="Get started with the HDInsight Emulator" authors="" />

Erste Schritte mit dem HDInsight Emulator
=========================================

In diesem Lernprogramm erhalten Sie eine Einführung in die Verwendung des Microsoft HDInsight Emulator für Azure (vormals HDInsight Server Developer Preview). Der HDInsight Emulator weist die gleichen Komponenten aus der Hadoop-Umgebung auf wie Azure HDInsight. Weitere Informationen einschließlich Angaben zu den bereitgestellten Versionen finden Sie unter [Welche Version von Hadoop ist in Azure HDInsight enthalten?](http://www.windowsazure.com/de-de/manage/services/hdinsight/howto-hadoop-version/ "HDInsight-Komponenten und -Versionen").

HDInsight Emulator stellt eine lokale Entwicklungsumgebung für Azure HDInsight zur Verfügung. Wenn Sie mit Hadoop vertraut sind, können Sie die ersten Schritte mit dem Emulator mithilfe von HDFS durchführen. In HDInsight ist das Standarddateisystem jedoch Azure Blob-Speicher (WASB, auch bekannt als Azure-Speicher - Blobs). Es empfiehlt sich also letztlich, Ihre Jobs mithilfe von WASB zu entwickeln. Sie können mit der Entwicklung für WASB mithilfe des Azure-Speicheremulators beginnen. Wahrscheinlich möchten Sie nur eine kleine Teilmenge Ihrer Daten verwenden (keine Konfigurationsänderungen im HDInsight Emulator erforderlich, nur ein anderer Speicherkontoname). Anschließend testen Sie Ihre Jobs lokal für Windows Azure-Speicher - erneut nur mit einer Teilmenge Ihrer Daten (erfordert eine Konfigurationsänderung im HDInsight Emulator). Schließlich können Sie den berechneten Teil Ihres Jobs zu HDInsight verschieben und einen Auftrag für Produktionsdaten ausführen.

> [WACOM.NOTE] Der HDInsight Emulator kann nur eine aus einem Knoten bestehende Bereitstellung verwenden.

Ein Lernprogramm zur Verwendung von HDInsight finden Sie unter [Erste Schritte mit Azure HDInsight](/de-de/manage/services/hdinsight/get-started-hdinsight/).

**Voraussetzungen**

Bevor Sie mit diesem Lernprogramm beginnen, benötigten Sie Folgendes:

-   Der HDInsight Emulator benötigt eine 64-Bit-Version von Windows. Eine der folgenden Anforderungen muss erfüllt sein:

    -   Windows 7 Service Pack 1
    -   Windows Server 2008 R2 Service Pack 1
    -   Windows 8
    -   Windows Server 2012
-   Installieren und konfigurieren Sie Azure PowerShell. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](/de-de/documentation/articles/install-configure-powershell/).

Dieses Lernprogramm umfasst folgende Punkte
-------------------------------------------

-   [Installieren des HDInsight Emulator](#install)
-   [Ausführen des Beispiels zum Zählen von Wörtern](#runwordcount)
-   [Ausführen der Erste Schritte-Beispiele](#rungetstartedsamples)
-   [Verbinden mit Azure Blob-Speicher](#blobstorage)
-   [Ausführen von HDInsight PowerShell](#powershell)
-   [Nächste Schritte](#nextsteps)

Installieren des HDInsight Emulator
-----------------------------------

Der Microsoft HDInsight Emulator kann über den Microsoft Web Platform Installer installiert werden.

> [WACOM.NOTE] Der HDInsight Emulator unterstützt zurzeit nur englische Betriebssystemversionen.

> [WACOM.NOTE] Falls Microsoft HDInsight Developer Preview installiert war, müssen Sie zuerst die folgenden beiden Komponenten in der Systemsteuerung bzw. unter "Programme und Funktionen" deinstallieren.
><ul>
<li>HDInsight Developer Preview</li>
<li>Hortonworks Data Platform Developer Preview</li>
</ul>

**So installieren Sie HDInsight Emulator**

1.  Öffnen Sie Internet Explorer, und navigieren Sie dann zur [Installationsseite für Microsoft HDInsight Emulator für Azure](http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT).
2.  Klicken Sie auf **Install Now**.
3.  Klicken Sie auf **Ausführen**, wenn Sie unten auf der Seite zur Installation von HDINSIGHT.exe aufgefordert werden.
4.  Klicken Sie im Fenster **Benutzerkontensteuerung**, das angezeigt wird, um die Installation abzuschließen, auf **Ja**. Es sollte nun das Fenster von Web Platform Installer 4.6 angezeigt werden.
5.  Klicken Sie unten auf der Seite auf **Installieren**.
6.  Klicken Sie auf **Akzeptieren**, um den Lizenzbedingungen zuzustimmen.
7.  Überprüfen Sie, ob im Web Platform Installer Folgendes angezeigt wird: **Folgende Produkte wurden erfolgreich installiert**. Klicken Sie dann auf **Fertig stellen**.
8.  Klicken Sie auf **Beenden**, um das Web Platform Installer 4.6-Fenster zu schließen.

    Auf Ihrem Desktop wurden drei Symbole installiert. Sie sind wie folgt verknüpft:

    -   **Hadoop Command Line**: Die Hadoop-Eingabeaufforderung, von der MapReduce-, Pig- und Hive-Jobs im HDInsight Emulator ausgeführt werden.

    -   **Hadoop-NameNode Status**: Der NameNode stellt ein strukturbasiertes Verzeichnis für alle Dateien in HDFS zur Verfügung. Außerdem verfolgt er, wo die Dateien für alle Dateien in einem Hadoop-Cluster aufbewahrt werden. Clients kommunizieren mit dem NameNode, um zu ermitteln, wo die Datenknoten für alle Dateien gespeichert werden.

    -   **Hadoop-MapReduce Status**: Der JobTracker, der MapReduce-Aufgaben den Knotenin einem Cluster zuordnet.

    Zudem sollten mehrere lokale Dienste installiert worden sein. Nachfolgend wird ein Screenshot des Fensters mit Diensten angezeigt:

    ![HDI.Emulator.Services](./media/hdinsight-get-started-emulator/HDI.Emulator.Services.png)

    Informationen zu bekannten Problemen beim Installieren und Ausführen von HDInsight Server finden Sie in den [Versionshinweisen für HDInsight Emulator](http://gettingstarted.hadooponazure.com/releaseNotes.html). Das Installationsprotokoll befindet sich im Verzeichnis **C:\\HadoopFeaturePackSetup\\HadoopFeaturePackSetupTools\\gettingStarted.winpkg.install.log**.

Ausführen eines MapReduce-Jobs zum Zählen von Wörtern
-----------------------------------------------------

Der HDInsight Emulator ist nun auf Ihrer Arbeitsstation konfiguriert. Sie können einen MapReduce-Job ausführen, um die Installation zu testen. Zunächst laden Sie einige Textdateien in HDFS hoch und dann führen Sie einen MapReduce-Job zum Zählen von Wörtern aus, um die Wortanzahl dieser Dateien zu zählen.

Das MapReduce-Programm zum Zählen von Wörtern wurde in der Datei *hadoop-examples.jar* gepackt. Die JAR-Datei ist im Ordner *C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT* gespeichert.

Für den JAR-Befehl gilt folgende Syntax:

    hadoop jar <jar> [mainClass] args...

Außerdem verwenden Sie einige fs-Befehle. Weitere Informationen zu Hadoop-Befehlen finden Sie im [Handbuch zu Hadoop-Befehlen](http://hadoop.apache.org/docs/r1.1.1/commands_manual.html).

Der MapReduce-Job zum Zählen von Wörtern hat zwei Argumente: einen Eingabeordner und einen Ausgabeordner. Sie verwenden *hdfs://localhost/user/HDIUser* als Eingabeordner und *hdfs://localhost/user/HDIUser/WordCount\_Output* als Ausgabeverzeichnis. Der Ausgabeordner kann kein bereits vorhandener Ordner sein. Andernfalls schlägt der MapReduce-Job fehl. Wenn Sie den MapReduce-Job zum zweiten Mal ausführen möchten, müssen Sie entweder einen anderen Ausgabeordner angeben oder den vorhandenen Ausgabeordner löschen.

**So führen Sie den MapReduce-Job zum Zählen von Wörtern aus**

1.  Doppelklicken Sie auf dem Desktop auf **Hadoop Command Line**, um das Hadoop-Befehlszeilenfenster zu öffnen. Dies sollte der aktuelle Ordner sein:

         c:\Hadoop\hadoop-1.1.0-SNAPSHOT>

    Ist dies nicht der Fall, führen Sie den folgenden Befehl aus:

         cd %hadoop_home%

2.  Führen Sie den folgenden Hadoop-Befehl aus, um einen HDFS-Ordner zum Speichern der Ein- und Ausgabedateien zu erstellen:

         hadoop fs -mkdir /user/HDIUser

3.  Führen Sie den folgenden Hadoop-Befehl aus, um einige lokale Dateien in HDFS zu kopieren:

         hadoop fs -copyFromLocal *.txt /user/HDIUser/

4.  Führen Sie den folgenden Befehl aus, um die Dateien im Ordner /user/HDIUser aufzulisten:

         hadoop fs -ls /user/HDIUser

    Daraufhin sollten die folgenden Dateien angezeigt werden:

         c:\Hadoop\hadoop-1.1.0-SNAPSHOT>hadoop fs -ls /user/HDIUser
         Found 8 items
         -rw-r--r--   1 username supergroup      16372 2013-10-30 12:07 /user/HDIUser/CHANGES.branch-1-win.txt
         -rw-r--r--   1 username supergroup     463978 2013-10-30 12:07 /user/HDIUser/CHANGES.txt
         -rw-r--r--   1 username supergroup       6631 2013-10-30 12:07 /user/HDIUser/Jira-Analysis.txt
         -rw-r--r--   1 username supergroup      13610 2013-10-30 12:07 /user/HDIUser/LICENSE.txt
         -rw-r--r--   1 username supergroup       1663 2013-10-30 12:07 /user/HDIUser/Monarch-CHANGES.txt
         -rw-r--r--   1 username supergroup        103 2013-10-30 12:07 /user/HDIUser/NOTICE.txt
         -rw-r--r--   1 username supergroup       2295 2013-10-30 12:07 /user/HDIUser/README.Monarch.txt
         -rw-r--r--   1 username supergroup       1397 2013-10-30 12:07 /user/HDIUser/README.txt

5.  Führen Sie den folgenden Befehl aus, um den MapReduce-Job zum Zählen von Wörtern auszuführen:

         hadoop jar hadoop-examples.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6.  Führen Sie den folgenden Befehl aus, um die Wörter, die "windows" enthalten, in der Ausgabedatei aufzulisten:

         hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

    Die Ausgabe sollte wie folgt aussehen:

         c:\Hadoop\hadoop-1.1.0-SNAPSHOT>hadoop fs -cat /user/HDIUser/WordCount_Output/pa
         rt-r-00000 | findstr "windows"
         windows 12
         windows+java6.  1
         windows.        3

Ausführen der Erste Schritte-Beispiele
--------------------------------------

Bei der Installation von HDInsight Emulator werden einige Beispiele zur Verfügung gestellt, damit neue Benutzer den Umgang mit Apache Hadoop-basierten Diensten unter Windows schnell lernen können. Diese Beispiele decken einige Aufgaben ab, die üblicherweise bei der Verarbeitung großer Datenmengen durchgeführt werden müssen. Indem Sie die Beispiele durchgehen, können Sie sich mit den Konzepten des MapReduce-Programmiermodells und seiner Umgebung vertraut machen.

Die Beispiele beschäftigen sich mit der Verarbeitung von IIS W3C-Protokolldatenszenarien. Es wird ein Datengenerierungstool zur Verfügung gestellt, mit dem die Datasets erstellt und in verschiedenen Größen in HDFS oder WASB (Azure Blob-Speicher) importiert werden können. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher für HDInsight](/de-de/manage/services/hdinsight/howto-blob-store/). MapReduce-, Pig- oder Hive-Jobs können dann für die Datenseiten ausgeführt werden, die durch das PowerShell-Skript generiert wurden. Beachten Sie, dass die verwendeten Pig- und Hive-Skripts beide in MapReduce-Programme kompiliert werden. Benutzer können eine Reihe von Jobs ausführen, um für sich selbst die Auswirkungen der Verwendung dieser unterschiedlichen Technologien sowie die Auswirkungen der Datengröße auf die Ausführung der Verarbeitungsaufgaben zu beobachten.

### Inhalt dieses Abschnitts

-   [Die IIS w3c-Protokolldatenszenarien](#scenarios)
-   [Laden von w3c-Beispielprotokolldaten](#loaddata)
-   [Ausführen von Java MapReduce-Jobs](#javamapreduce)
-   [Ausführen von Hive-Jobs](#hive)
-   [Ausführen von Pig-Jobs](#pig)
-   [Neuerstellung der Beispiele](#rebuild)

### Die IIS w3c-Protokolldatenszenarien

Das w3c-Szenario generiert und importiert IIS W3C-Protokolldaten in drei Größen in HDFS oder WASB: 1 MB, 500 MB und 2 GB. Es stellt drei Jobtypen zur Verfügung und implementiert jeden davon in C\#, Java, Pig und Hive.

-   **totalhits**: Berechnet die Gesamtzahl der Anforderungen für eine bestimmte Seite.
-   **avgtime**: Berechnet die durchschnittliche Zeit (in Sekunden), die für eine Anforderung pro Seite benötigt wird.
-   **errors**: Berechnet die Anzahl der Fehler pro Seite, pro Stunde, für Anforderungen, deren Status 404 oder 500 war.

Diese Beispiele und ihre Dokumentation stellen keine detaillierte Studie oder vollständige Implementierung der wesentlichen Hadoop-Technologien zur Verfügung. Der verwendete Cluster hat nur einen einzigen Knoten. Daher kann die Auswirkung des Hinzufügens weiterer Knoten in dieser Version nicht beobachtet werden.

### Laden von w3c-Beispielprotokolldaten

Das Generieren der Daten und Importieren in HDFS erfolgt über das PowerShell-Skript "importdata.ps1".

**So importieren Sie w3c-Beispielprotokolldaten:**

1.  Öffnen Sie die Hadoop-Befehlszeile auf dem Desktop.
2.  Führen Sie den folgenden Befehl aus, um das Verzeichnis in **C:\\Hadoop\\GettingStarted** zu ändern:

         cd \Hadoop\GettingStarted

3.  Führen Sie den folgenden Befehl aus, um Daten zu generieren und in HDFS zu importieren:

         powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted 

    Wenn Sie stattdessen Daten in WASB laden möchten, finden Sie weitere Informationen dazu unter [Verbinden mit Azure Blob-Speicher](#blobstorage).

4.  Führen Sie den folgenden Befehl in der Hadoop-Befehlszeile aus, um die importierten Dateien in HDFS aufzulisten:

         hadoop fs -lsr /w3c

    Die Ausgabe sollte in etwa wie folgt aussehen:

         c:\Hadoop\GettingStarted\w3c>hadoop fs -lsr /w3c
         drwxr-xr-x   - username supergroup          0 2013-10-30 13:29 /w3c/input
         drwxr-xr-x   - username supergroup          0 2013-10-30 13:29 /w3c/input/large
         -rw-r--r--   1 username supergroup  543692369 2013-10-30 13:29 /w3c/input/large/data_w3c_large.txt
         drwxr-xr-x   - username supergroup          0 2013-10-30 13:28 /w3c/input/medium
         -rw-r--r--   1 username supergroup  272394671 2013-10-30 13:28 /w3c/input/medium/data_w3c_medium.txt
         drwxr-xr-x   - username supergroup          0 2013-10-30 13:28 /w3c/input/small
         -rw-r--r--   1 username supergroup    1058328 2013-10-30 13:28 /w3c/input/small/data_w3c_small.txt

5.  Führen Sie den folgenden Befehl aus, um eine der Datendateien im Konsolenfenster anzuzeigen:

         hadoop fs -cat /w3c/input/small/data_w3c_small.txt

Die Datendatei wurde nun erstellt und in HDFS importiert. Sie können verschiedene Hadoop-Jobs ausführen.

### Ausführen von Java MapReduce-Jobs

MapReduce ist die grundlegende Compute Engine für Hadoop. Sie ist standardmäßig in Java implementiert. Es gibt jedoch auch Beispiele, bei denen .NET und Hadoop Streaming in C\# verwendet werden. Für die Ausführung eines MapReduce-Jobs gilt folgende Syntax:

    hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

Die JAR-Datei und die Quelldateien befinden sich im Ordner C:\\Hadoop\\GettingStarted\\Java.

**So führen Sie einen MapReduce-Job zur Ermittlung von Webseitentreffern aus**

1.  Öffnen Sie die Hadoop-Befehlszeile.
2.  Führen Sie den folgenden Befehl aus, um das Verzeichnis in **C:\\Hadoop\\GettingStarted** zu ändern:

         cd \Hadoop\GettingStarted

3.  Führen Sie den folgenden Befehl aus, um das Ausgabeverzeichnis zu entfernen, für den Fall, dass der Ordner bereits vorhanden ist. Der MapReduce-Job schlägt fehlt, wenn der Ausgabeordner bereits existiert.

         hadoop fs -rmr /w3c/output

4.  Führen Sie den folgenden Befehl aus:

         hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

    In der folgenden Tabelle werden die Elemente des Befehls beschrieben:

	<table border="1">
	<tr><td>Parameter</td><td>Hinweis</td></tr>
	<tr><td>w3c_scenarios.jar</td><td>Die JAR-Datei befindet sich im Ordner C:\Hadoop\GettingStarted\Java.</td></tr>
	<tr><td>microsoft.hadoop.w3c.TotalHitsForPage</td><td>Der Typ kann durch einen der folgenden Typen ersetzt werden: 
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

5.  Führen Sie den folgenden Befehl aus, um die Ausgabedatei anzuzeigen:

         hadoop fs -cat /w3c/output/part-00000

    Die Ausgabe sollte der Folgenden ähneln:

         c:\Hadoop\GettingStarted\Java>hadoop fs -cat /w3c/output/part-00000
         /Default.aspx   3409
         /Info.aspx      1115
         /UserService    1130

    Die Seite Default.aspx erzielt also 3409 Treffer usw.

### Ausführen von Hive-Jobs

Die Hive-Abfrage-Engine wird Analytikern mit guten SQL-Kenntnissen vertraut vorkommen. Sie bietet eine SQL-ähnliche Schnittstelle und ein relationales Datenmodell für HDFS. Hive verwendet eine HiveQL (oder HQL) genannte Sprache. Dies ist eine Abwandlung von SQL.

**So führen Sie einen Hive-Job aus**

1.  Öffnen Sie die Hadoop-Befehlszeile.
2.  Ändern Sie das Verzeichnis in **C:\\Hadoop\\GettingStarted**.
3.  Führen Sie den folgenden Befehl aus, um den Ordner **/w3c/hive/input** zu entfernen, für den Fall, dass er bereits vorhanden ist. Der Hive-Job schlägt fehl, falls der Ordner bereits existiert.

         hadoop fs -rmr /w3c/hive/input

4.  Führen Sie den folgenden Befehl aus, um den Ordner **/w3c/hive/input** zu erstellen und die Datendatei von der Arbeitsstation in HDFS zu kopieren:

         hadoop fs -mkdir /w3c/hive/input
         hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5.  Führen Sie den folgenden Befehl aus, um die Skriptdatei **w3ccreate.hql** auszuführen. Das Skript erstellt eine Hive-Tabelle und lädt Daten in die Hive-Tabelle:

         C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ccreate.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"

    Dies ist das HiveQL-Skript:

         DROP TABLE w3c;

         CREATE TABLE w3c(
          logdate string,
          logtime string,
          c_ip string,
          cs_username string,
          s_ip string,
          s_port string,
          cs_method string,
          cs_uri_stem string,
          cs_uri_query string,
          sc_status int,
          sc_bytes int,
          cs_bytes int,
          time_taken int,
          cs_agent string, 
          cs_Referrer string)
         ROW FORMAT delimited
         FIELDS TERMINATED BY ' ';

         LOAD DATA INPATH '${hiveconf:input}' OVERWRITE INTO TABLE w3c;

    Die Ausgabe sollte in etwa wie folgt aussehen:

         c:\Hadoop\GettingStarted>C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ccrea te.hql -hiveconf "input=/w3c/hive/input/data_w3c_small.txt"
         Hive history file=c:\hadoop\hive-0.9.0\logs\history/hive_job_log_username_201310311452_1053491002.txt
         Logging initialized using configuration in file:/C:/Hadoop/hive-0.9.0/conf/hive-log4j.properties
         OK
         Time taken: 0.616 seconds
         OK
         Time taken: 0.139 seconds
         Loading data to table default.w3c
         Moved to trash: hdfs://localhost:8020/apps/hive/warehouse/w3c
         OK
         Time taken: 0.573 seconds

6.  Führen Sie den folgenden Befehl aus, um die HiveQL-Skriptdatei **w3ctotalhitsbypate.hql** auszuführen.

         C:\Hadoop\hive-0.9.0\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

    In der folgenden Tabelle werden die Elemente des Befehls beschrieben:

	<table border="1">
	<tr><td>Datei</td><td>Beschreibung</td></tr>
	<tr><td>C:\Hadoop\hive-0.9.0\bin\hive.cmd</td><td> Das Hive-Befehlsskript </td></tr>
	<tr><td>C:\Hadoop\GettingStarted\Hive\w3c\w3ctotalhitsbypage.hql</td><td> Sie können die Hive-Skriptdatei durch eine der folgenden Dateien ersetzen:
	<ul>
	<li>C:\Hadoop\GettingStarted\Hive\w3c\w3caveragetimetaken.hql</li>
	<li>C:\Hadoop\GettingStarted\Hive\w3c\w3cerrorsbypage.hql</li>
	</ul>
	</td></tr>

	</table>

    Das HiveQL-Skript w3ctotalhitsbypage.hql sieht wie folgt aus:

         SELECT filtered.cs_uri_stem,COUNT(*) 
         FROM (
           SELECT logdate,cs_uri_stem from w3c WHERE logdate NOT RLIKE '.*#.*'
         ) filtered
         GROUP BY (filtered.cs_uri_stem);

    Das Ende der Ausgabe sollte in etwa wie folgt aussehen:

         MapReduce Total cumulative CPU time: 3 seconds 47 msec
         Ended Job = job_201310291309_0006
         MapReduce Jobs Launched:
         Job 0: Map: 1  Reduce: 1   Cumulative CPU: 3.047 sec   HDFS Read: 1058546 HDFS W
         rite: 53 SUCCESS
         Total MapReduce CPU Time Spent: 3 seconds 47 msec
         OK
         /Default.aspx   3409
         /Info.aspx      1115
         /UserService    1130
         Time taken: 34.68 seconds

Beachten Sie, dass als erster Schritt bei jedem der Jobs eine Tabelle erstellt wird und dann Daten aus der zuvor erstellten Datei in die Tabelle geladen werden. Sie können zu der erstellten Datei navigieren, indem Sie mit dem folgenden Befehl unter dem Knoten /Hive in HDFS suchen:

    hadoop fs -lsr /apps/hive/

### Ausführen von Pig-Jobs

Bei der Pig-Verarbeitung wird eine als *Pig Latin* bezeichnete Datenflusssprache verwendet. Pig Latin-Abstraktionen stellen komplexere Datenstrukturen als MapReduce zur Verfügung und führen für Hadoop die Vorgänge durch, die SQL für RDBMS-Systeme durchführt.

**So führen Sie die Pig-Jobs aus:**

1.  Öffnen Sie die Hadoop-Befehlszeile.
2.  Ändern Sie das Verzeichnis in C:\\Hadoop\\GettingStarted.
3.  Führen Sie den folgenden Befehl aus, um einen Pig-Job zu übermitteln:

         C:\Hadoop\pig-0.9.3-SNAPSHOT\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

    Die folgende Tabelle enthält die Elemente des Befehls:

	<table border="1">
	<tr><td>Datei</td><td>Beschreibung</td></tr>
	<tr><td>C:\Hadoop\pig-0.9.3-SNAPSHOT\bin\pig.cmd</td><td> Das Pig-Befehlsskript</td></tr>
	<tr><td>C:\Hadoop\GettingStarted\Pig\w3c\TotalHitsForPage.pig</td><td> Sie können die Pig Latin-Skriptdatei durch eine der folgenden Dateien ersetzen:
	<ul>
	<li>C:\Hadoop\GettingStarted\Pig\w3c\AverageTimeTaken.pig</li>
	<li>C:\Hadoop\GettingStarted\Pig\w3c\ErrorsByPage.pig</li>
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

         (/Info.aspx,1115)
         (/UserService,1130)
         (/Default.aspx,3409)

Beachten Sie Folgendes: Da Pig-Skripts in MapReduce-Jobs kompiliert werden (und potenziell in mehrere solcher Jobs), stellen Benutzer möglicherweise fest, dass im Verlauf der Verarbeitung eines Pig-Jobs mehrere MapReduce-Jobs ausgeführt werden.

### Neuerstellung der Beispiele

Die Beispiele enthalten zurzeit alle erforderlichen Binärdateien. Eine Erstellung ist also nicht erforderlich. Wenn Sie Änderungen an den Java- oder .NET-Beispielen vornehmen möchten, können Sie sie entweder mit MSBuild oder dem enthaltenen PowerShell-Skript neu erstellen.

**So erstellen Sie die Beispiele neu**

1.  Öffnen Sie die Hadoop-Befehlszeile.
2.  Führen Sie den folgenden Befehl aus:

         powershell -F buildsamples.ps1

Verbinden mit Azure Blob-Speicher
---------------------------------

Azure HDInsight verwendet Azure Blob-Speicher als Standarddateisystem. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight](/de-de/manage/services/hdinsight/howto-blob-store/).

Es besteht die Möglichkeit, einen lokalen Cluster im HDInsight Emulator zu konfigurieren, um Azure Blob-Speicher statt des lokalen Speichers zu verwenden. In diesem Abschnitt werden folgende Themen behandelt:

-   Verbinden mit dem Speicheremulator
-   Verbinden mit Azure Blob-Speicher
-   Konfigurieren eines Azure Blob-Speichers als Standarddateisystem für den HDInsight Emulator

### Verbinden mit dem Speicheremulator

Der Azure-Speicheremulator wird mit [Azure SDK for .NET](http://www.windowsazure.com/de-de/downloads/) geliefert. Der Speicheremulator wird nicht automatisch gestartet. Sie müssen ihn manuell starten. Der Name der Anwendung ist *Azure-Speicheremulator*. Zum Starten/Beenden der Emulatoren klicken Sie mit der rechten Maustaste auf das blaue Azure-Symbol in der Windows-Taskleiste und dann auf Show Storage Emulator UI.

> [WACOM.NOTE] Sie erhalten möglicherweise die folgende Fehlermeldung, wenn Sie den Speicheremulator starten:

> ``` {}
> Der Prozess kann nicht auf die Datei zugreifen, da sie bereits von einem anderen Prozess verwendet wird.
> ```

> Dies ist der Fall, da einer der Hive-Dienste von Hadoop ebenfalls Port 10000 nutzt. Verwenden Sie das folgende Verfahren, um das Problem zu umgehen:

><ol>
<li>Beenden Sie die beiden Hadoop Hive-Dienste mithilfe von services.msc: Apache Hadoop hiveserver und Apache Hadoop Hiveserver2.</li>
<li>Starten Sie den Blob-Speicheremulator. </li>
<li>Führen Sie einen Neustart der beiden Hadoop Hive-Dienste durch. </li>
</ol>

Für den Zugriff auf den Speicheremulator gilt folgende Syntax:

    wasb://<ContainerName>@storageemulator

Beispiel:

    hadoop fs -ls wasb://myContainer@storageemulator

> [WACOM.NOTE] Möglicherweise erhalten Sie die folgende Fehlermeldung:

> ``` {}
> ls: No FileSystem for scheme: wasb
> ```

> Der Grund hierfür ist, dass Sie immer noch die Developer Preview-Version verwenden. Befolgen Sie die Anweisungen im Abschnitt "Installieren des HDInsight Emulator" dieses Artikels, um die Developer Preview-Version zu deinstallieren, und installieren Sie die Anwendung anschließend neu.

### Verbinden mit Azure Blob-Speicher

Anweisungen zum Erstellen eines Speicherkontos finden Sie unter [So erstellen Sie ein Speicherkonto](/de-de/manage/services/storage/how-to-create-a-storage-account/).

**So erstellen Sie einen Container**

1.  Melden Sie sich beim [Verwaltungsportal](https://manage.windowsazure.com/) an.
2.  Klicken Sie auf der linken Seite auf **Speicher**. Eine Liste der Speicherkonten für Ihr Abonnement wird angezeigt.
3.  Klicken Sie auf das Speicherkonto in der Liste, für das Sie den Container erstellen möchten.
4.  Klicken Sie oben auf der Seite auf **Container**.
5.  Klicken Sie unten auf der Seite auf **Hinzufügen**.
6.  Geben Sie den **Namen** ein, und wählen Sie **Zugriff** aus. Sie können jede der drei Zugriffsstufen verwenden. Die Standardstufe ist **Privat**.
7.  Klicken Sie zum Speichern der Änderungen auf **OK**. Der neue Container sollte nun im Portal aufgeführt sein.

Bevor Sie auf ein Azure-Speicherkonto zugreifen können, müssen Sie der Konfigurationsdatei den Kontonamen und den Kontoschlüssel hinzufügen.

**So konfigurieren Sie die Verbindung mit einem Azure-Speicherkonto**

1.  Öffnen Sie **C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf\\core-site.xml** im Editor.
2.  Fügen Sie das folgende &lt;property\>-Tag neben den anderen &lt;property\>-Tags hinzu:

         <property>
             <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
             <value><StorageAccountKey></value>
         </property>

    Sie müssen &lt;StorageAccountName\> und &lt;StorageAccountKey\> durch die Werte ersetzen, die Ihren Speicherkontoinformationen entsprechen.

3.  Speichern Sie die Änderungen. Ein Neustart der Hadoop-Dienste ist nicht erforderlich.

Verwenden Sie die folgende Syntax für den Zugriff auf das Speicherkonto:

    wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

Beispiel:

    hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/

### Verwenden eines Azure Blob-Speichercontainers als Standarddateisystem

Es besteht auch die Möglichkeit, einen Azure Blob-Speichercontainer als Standarddateisystem zu verwenden, wie dies in Azure HDInsight der Fall ist.

**So konfigurieren Sie das Standarddateisystem mithilfe eines Azure Blob-Speichercontainers**

1.  Öffnen Sie **C:\\Hadoop\\hadoop-1.1.0-SNAPSHOT\\conf\\core-site.xml** im Editor.
2.  Ermitteln Sie das folgende &lt;property\>-Tag:

         <property>
           <name>fs.default.name</name>
           <!-- cluster variant -->
           <value>hdfs://localhost:8020</value>
           <description>The name of the default file system.  Either the    literal string "local" or a host:port for NDFS.</description>
           <final>true</final>
         </property>

3.  Ersetzen Sie es durch die folgenden beiden &lt;property\>-Tags:

         <property>
           <name>fs.default.name</name>
           <!-- cluster variant -->
           <!--<value>hdfs://localhost:8020</value>-->
           <value>wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net</value>
           <description>The name of the default file system.  Either the    literal string "local" or a host:port for NDFS.</description>
           <final>true</final>
         </property>
            
         <property>
           <name>dfs.namenode.rpc-address</name>
           <value>hdfs://localhost:8020</value>
           <description>A base for other temporary directories.</description>
         </property>

    Sie müssen &lt;StorageAccountName\> und &lt;StorageAccountKey\> durch die Werte ersetzen, die Ihren Speicherkontoinformationen entsprechen.

4.  Speichern Sie die Änderungen.
5.  Öffnen Sie die Hadoop-Befehlszeile auf dem Desktop im Modus mit erhöhten Rechten (Als Administrator ausführen)
6.  Führen Sie die folgenden Befehle aus, um die Hadoop-Dienste neu zu starten:

         C:\Hadoop\stop-onebox.cmd
         C:\Hadoop\start-onebox.cmd

7.  Führen Sie den folgenden Befehl aus, um die Verbindung mit dem Standarddateisystem zu testen:

         hadoop fs -ls /

    Mit den folgenden Befehlen werden die Inhalte im gleichen Ordner aufgelistet:

         hadoop fs -ls wasb:///
         hadoop fs -ls wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/
         hadoop fs -ls wasbs://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

    Verwenden Sie den folgenden Befehl, um auf HDFS zuzugreifen:

         hadoop fs -ls hdfs://localhost:8020/

Ausführen von HDInsight PowerShell
----------------------------------

Einige der HDInsight PowerShell-Cmdlets werden von HDInsight Emulator unterstützt. Zu diesem Cmdlets zählen die Folgenden:

-   Cmdlets für die HDInsight-Jobdefinition

    -   New-AzureHDInsightSqoopJobDefinition
    -   New-AzureHDInsightStreamingMapReduceJobDefinition
    -   New-AzureHDInsightPigJobDefinition
    -   New-AzureHDInsightHiveJobDefinition
    -   New-AzureHDInsightMapReduceJobDefinition
-   Start-AzureHDInsightJob
-   Get-AzureHDInsightJob
-   Wait-AzureHDInsightJob

Nachfolgend finden Sie ein Beispiel für die Übermittlung eines Hadoop-Jobs:

    $creds = Get-Credential (hadoop as username, password can be anything)
    $hdinsightJob = <JobDefinition>
    Start-AzureHDInsightJob -Cluster http://localhost:50111 -Credential $creds -JobDefinition $hdinsightJob

Sie werden zu einer Eingabe aufgefordert, wenn Sie Get-Credential aufrufen. Sie müssen **hadoop** als Benutzername verwenden. Das Kennwort kann eine beliebige Zeichenfolge sein. Der Clustername ist immer **http://localhost:50111**.

Weitere Informationen zur Übermittlung von Hadoop-Jobs finden Sie unter [Programmgesteuerte Übermittlung von Hadoop-Jobs](/de-de/manage/services/hdinsight/submit-hadoop-jobs-programmatically/). Weitere Informationen zu den HDInsight PowerShell-Cmdlets finden Sie unter [HDInsight-Cmdlet-Referenz](http://msdn.microsoft.com/de-de/library/windowsazure/dn479228.aspx).

Nächste Schritte
----------------

In diesem Lernprogramm haben Sie HDInsight Emulator installiert und einige Hadoop-Jobs ausgeführt. Weitere Informationen finden Sie in den folgenden Artikeln:

-   [Erste Schritte mit Azure HDInsight](/de-de/manage/services/hdinsight/get-started-hdinsight/)
-   [Entwickeln von Java MapReduce-Programmen für HDInsight](/de-de/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)
-   [Entwickeln von Hadoop-Streaming-MapReduce-Programmen in C\# für HDInsight](/de-de/manage/services/hdinsight/develop-deploy-hadoop-streaming-jobs/)
-   [Versionshinweise für HDInsight Emulator](https://gettingstarted.hadooponazure.com/releaseNotes.html)
-   [MSDN-Forum zur Erörterung von Themen rund um HDInsight](http://social.msdn.microsoft.com/Forums/en-US/hdinsight)

