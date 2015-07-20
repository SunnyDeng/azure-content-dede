<properties
	pageTitle="Erste Schritte mit einem Hadoop-Emulator für HDInsight | Microsoft Azure"
	description="Probieren Sie einen installierten Emulator mithilfe eines MapReduce-Lernprogramms und anderer Beispiele aus, um das Hadoop-Ökosystem kennenzulernen. Der HDInsight-Emulator verhält sich wie eine Hadoop-Sandbox."
	keywords="emulator,hadoop ecosystem,hadoop sandbox,mapreduce tutorial"
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	authors="nitinme"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article" 
	ms.date="05/07/2015"
	ms.author="nitinme"/>

# Erste Schritte im Hadoop-Ökosystem mit dem HDInsight-Emulator (einer Hadoop-Sandbox)

Dieses Lernprogramm beschreibt die ersten Schritte mit Hadoop-Clustern im Microsoft HDInsight-Emulator für Azure (ehemals HDInsight Server Developer-Vorschau). Der HDInsight Emulator weist die gleichen Komponenten aus der Hadoop-Umgebung auf wie Azure HDInsight. Weitere Informationen inklusive der bereitgestellten Versionen finden Sie unter [Welche Hadoop-Version verwendet Azure HDInsight?](hdinsight-component-versioning.md).

Nach der Installation des Emulators können Sie ein Lernprogramm zu MapReduce für die WordCount-Topologie durcharbeiten und die Beispiele ausführen.

> [AZURE.NOTE]Der HDInsight-Emulator enthält nur ein Hadoop-Cluster. HBase oder Storm sind nicht enthalten.


Der HDInsight-Emulator stellt eine lokale Entwicklungsumgebung zur Verfügung, die mit einem Hadoop-Sandkasten vergleichbar ist. Wenn Sie mit Hadoop vertraut sind, können Sie mithilfe des HDFS (Hadoop Distributed File System) die ersten Schritte mit dem HDInsight-Emulator ausführen. In HDInsight ist Azure-Blobspeicher das Standarddateisystem. Es empfiehlt sich also letztlich, Azure-Blobspeicher für Ihre Entwicklungsaufträge zu verwenden. Um Azure-Blobspeicher mit dem HDInsight-Emulator zu verwenden, müssen Sie die Konfiguration des Emulators ändern.

> [AZURE.NOTE]Der HDInsight Emulator kann nur eine aus einem Knoten bestehende Bereitstellung verwenden.


## Voraussetzungen
Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- Der HDInsight Emulator benötigt eine 64-Bit-Version von Windows. Eine der folgenden Anforderungen muss erfüllt sein:

	- Windows 7 Service Pack 1
	- Windows Server 2008 R2 Service Pack 1
	- Windows 8
	- Windows Server 2012

- **Azure PowerShell**. Siehe [Installieren und Verwenden von Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).


##<a name="install"></a>Installieren des HDInsight-Emulators

Der Microsoft HDInsight Emulator kann über den Microsoft Web Platform Installer installiert werden.

> [AZURE.NOTE]Der HDInsight-Emulator unterstützt derzeit nur englischsprachige Betriebssysteme. Wenn Sie eine frühere Version des Emulators installiert haben, müssen Sie die folgenden beiden Komponenten über "Systemsteuerung/Programme und Features" deinstallieren, bevor die neueste Version des Emulators installiert wird: <ul> <li>Microsoft HDInsight Emulator für Azure oder HDInsight Developer Preview, je nachdem, was installiert ist</li> <li>Hortonworks Data Platform</li> </ul>


**So installieren Sie den HDInsight-Emulator**

1. Öffnen Sie Internet Explorer, und navigieren Sie dann zur [Installationsseite für Microsoft HDInsight Emulator für Azure][hdinsight-emulator-install].
2. Klicken Sie auf **Install Now**.
3. Klicken Sie auf **Ausführen**, wenn Sie unten auf der Seite zur Installation von HDINSIGHT.exe aufgefordert werden.
4. Klicken Sie im Fenster **Benutzerkontensteuerung**, das angezeigt wird, um die Installation abzuschließen, auf **Ja**. Das Fenster "Webplattform-Installer" wird angezeigt.
6. Klicken Sie unten auf der Seite auf **Installieren**.
7. Klicken Sie auf **Akzeptieren**, um den Lizenzbedingungen zuzustimmen.
8. Überprüfen Sie, ob im Webplattform-Installer Folgendes angezeigt wird: **Folgende Produkte wurden erfolgreich installiert**. Klicken Sie dann auf **Fertig stellen**.
9. Klicken Sie auf **Beenden**, um das Web Platform Installer-Fenster zu schließen.

**So überprüfen Sie die Installation des HDInsight-Emulators**

Auf Ihrem Desktop wurden drei Symbole installiert. Sie sind wie folgt verknüpft:

- **Hadoop-Befehlszeile** – Die Hadoop-Eingabeaufforderung, über die MapReduce-, Pig- und Hive-Aufträge im HDInsight-Emulator ausgeführt werden.

- **Status von "Hadoop NameNode"** – Der "NameNode" stellt ein strukturbasiertes Verzeichnis für alle Dateien in HDFS zur Verfügung. Außerdem verfolgt er, wo die Dateien für alle Dateien in einem Hadoop-Cluster aufbewahrt werden. Clients kommunizieren mit dem NameNode, um zu ermitteln, wo die Datenknoten für alle Dateien gespeichert werden.

- **Status von "Hadoop Yarn"** – Die Auftragsverfolgungsfunktion, die MapReduce-Aufgaben Knoten in einem Cluster zuordnet.

Zudem sollten mehrere lokale Dienste installiert worden sein. Nachfolgend wird ein Screenshot des Fensters mit Diensten angezeigt:

![Im Emulator-Fenster aufgeführte Dienste des Hadoop-Ökosystems.][image-hdi-emulator-services]

Die Dienste im Zusammenhang mit dem HDInsight-Emulator werden nicht automatisch gestartet. Führen Sie an der Hadoop-Befehlszeile unter "C:\\hdp" (Standardspeicherort) den Befehl **start_local_hdp_services.cmd** aus, um die Dienste zu starten. Führen Sie **set-onebox-autostart.cmd** aus, um die Dienste beim Computer-Neustart automatisch zu starten.

Informationen zu bekannten Problemen beim Installieren und Ausführen des HDInsight-Emulators finden Sie in den [Anmerkungen zu dieser Version des HDInsight-Emulators](hdinsight-emulator-release-notes.md). Das Installationsprotokoll befindet sich im Verzeichnis **C:\\HadoopFeaturePackSetup\\HadoopFeaturePackSetupTools\\gettingStarted.winpkg.install.log**.

##<a name="vstools"></a>Erste Schritte mit den HDInsight-Tools für Visual Studio

Die HDInsight-Tools für Visual Studio dienen zum Herstellen einer Verbindung mit dem HDInsight-Emulator. Informationen zur Verwendung von Visual Studio-Tools mit HDInsight-Clustern in Azure finden Sie unter [Erste Schritte mit den HDInsight Hadoop-Tools für Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

### Installieren der HDInsight-Tools für den Emulator

Anleitungen zum Installieren der HDInsight Visual Studio-Tools finden Sie [hier](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md#installation).

### Verbinden mit dem HDInsight-Emulator

1. Öffnen Sie Visual Studio.
2. Klicken Sie im Menü **Ansicht** auf **Server-Explorer**, um das Fenster "Server-Explorer" zu öffnen.
3. Erweitern Sie **Azure**, klicken Sie mit der rechten Maustaste auf **HDInsight**, und klicken Sie dann auf **Mit HDInsight-Emulator verbinden**.

	 ![Visual Studio-Ansicht: Menübefehl "Mit HDInsight-Emulator verbinden".](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.connect.vs.png)

4. Überprüfen Sie im Dialogfeld "Mit HDInsight-Emulator verbinden" die Werte für die Endpunkte WebHCat, HiveServer2 und WebHDFS, und klicken Sie dann auf **Weiter**. Die standardmäßig aufgefüllten Werte sollten funktionieren, solange Sie keine Änderungen an der Standardkonfiguration des Emulators vorgenommen haben. Wenn Sie Änderungen vorgenommen haben, aktualisieren Sie die Werte im Dialogfeld und klicken dann auf "Weiter".

	![Dialogfeld "Mit HDInsight-Emulator verbinden" mit Einstellungen.](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.connect.vs.dialog.png)

5. Nachdem die Verbindung erfolgreich hergestellt wurde, klicken Sie auf **Fertig stellen**. Jetzt sollte der HDInsight-Emulator im Server-Explorer angezeigt werden.

	![Server-Explorer mit verbundenem lokalen HDInsight-Emulator (eine Hadoop-Sandbox).](./media/hdinsight-hadoop-emulator-get-started/hdi.emulator.vs.connected.png)

Sobald die Verbindung erfolgreich hergestellt wurde, können Sie die HDInsight Visual Studio-Tools mit dem Emulator wie mit einem Azure HDInsight-Cluster verwenden. Anleitungen zur Verwendung von Visual Studio-Tools mit Azure HDInsight-Clustern finden Sie unter [Verwenden der HDInsight Hadoop-Tools für Visual Studio](../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md).

## Problembehandlung: Verbinden der HDInsight-Tools mit dem HDInsight-Emulator

1. Zum Herstellen einer Verbindung mit dem HDInsight-Emulator müssen Sie, obwohl das Dialogfeld zeigt, dass "HiveServer2" erfolgreich eine Verbindung hergestellt hat, in der Hive-Konfigurationsdatei "C:\\hdp\\hive-*version*\\conf\\hive-site.xml" die Eigenschaft **hive.security.authorization.enabled ** manuell auf **false** festlegen und dann den lokalen Emulator neu starten. Die HDInsight-Tools für Visual Studio stellen nur dann eine Verbindung mit "HiveServer2" her, wenn die obersten 100 Zeilen der Tabelle in der Vorschau angezeigt werden. Wenn Sie eine solche Abfrage nicht beabsichtigen, belassen Sie die Hive-Konfiguration unverändert.

2. Wenn Sie dynamische IP-Zuweisung (DHCP) auf dem Computer mit dem HDInsight-Emulator verwenden, müssen Sie möglicherweise die Datei "C:\\hdp\\hadoop-*Version*\\etc\\hadoop\\core-site.xml" aktualisieren, indem Sie den Wert der Eigenschaft **hadoop.proxyuser.hadoop.hosts** in (*) ändern. Dies ermöglicht Hadoop-Benutzern das Herstellen einer Verbindung von allen Hosts aus, um die Identität des Benutzers übernehmen, den Sie in Visual Studio eingegeben haben.

		<property>
			<name>hadoop.proxyuser.hadoop.hosts</name>
			<value>*</value>
		</property>

3. Sie erhalten möglicherweise eine Fehlermeldung, wenn Visual Studio versucht, sich mit dem WebHCat-Dienst zu verbinden ("Fehler": "Auftrag job_XXXX_0001 nicht gefunden"). In diesem Fall müssen Sie den WebHCat-Dienst neu starten und den Vorgang wiederholen. Um den WebHCat-Dienst neu zu starten, öffnen Sie die MMC **Dienste**. Klicken Sie mit der rechten Maustaste auf **Apache Hadoop Templeton** (dies ist der alte Name des WebHCat-Diensts), und klicken Sie dann auf **Neu starten**.

##<a name="runwordcount"></a>Lernprogramm zu MapReduce für die WordCount-Topologie

Nachdem Sie den HDInsight-Emulator auf Ihrer Arbeitsstation konfiguriert haben, können Sie die Installation mithilfe dieses Lernprogramms zu MapReduce testen. Laden Sie zunächst einige Textdateien in HDFS hoch und führen Sie dann einen MapReduce-Job zum Zählen von Wörtern aus, um die Wortanzahl dieser Dateien zu zählen.

Das MapReduce-Programm zum Zählen von Wörtern befindet sich in der Datei *hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar*. Die JAR-Datei befindet sich im Ordner *C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\hadoop\\mapreduce*.

Der MapReduce-Job zum Zählen von Wörtern hat zwei Argumente:

- Ein Eingabeordner. Verwenden Sie *hdfs://localhost/user/HDIUser* als Eingabeordner.- Ein Ausgabeordner. Verwenden Sie *hdfs://localhost/user/HDIUser/WordCount_Output* als Ausgabeordner. Der Ausgabeordner darf kein bereits vorhandener Ordner sein, weil andernfalls der MapReduce-Auftrag misslingt. Wenn Sie den MapReduce-Auftrag zum zweiten Mal ausführen möchten, müssen Sie entweder einen anderen Ausgabeordner angeben oder den vorhandenen Ausgabeordner löschen. 
**So führen Sie den MapReduce-Auftrag zum Zählen von Wörtern aus**

1. Doppelklicken Sie auf dem Desktop auf **Hadoop Command Line**, um das Hadoop-Befehlszeilenfenster zu öffnen. Dies sollte der aktuelle Ordner sein:

		c:\hdp\hadoop-2.4.0.2.1.3.0-1981

	Ist dies nicht der Fall, führen Sie den folgenden Befehl aus:

		cd %hadoop_home%

2. Führen Sie die folgenden Hadoop-Befehle aus, um einen HDFS-Ordner zum Speichern der Ein- und Ausgabedateien zu erstellen:

		hadoop fs -mkdir /user
		hadoop fs -mkdir /user/HDIUser

3. Führen Sie den folgenden Hadoop-Befehl aus, um einige lokale Textdateien nach HDFS zu kopieren:

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common*.txt /user/HDIUser

4. Führen Sie den folgenden Befehl aus, um die Dateien im Ordner "/user/HDIUser" aufzulisten:

		hadoop fs -ls /user/HDIUser

	Daraufhin sollten die folgenden Dateien angezeigt werden:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -ls /user/HDIUser
		Found 4 items
		-rw-r--r--   1 username hdfs     574261 2014-09-08 12:56 /user/HDIUser/CHANGES.txt
		-rw-r--r--   1 username hdfs      15748 2014-09-08 12:56 /user/HDIUser/LICENSE.txt
		-rw-r--r--   1 username hdfs        103 2014-09-08 12:56 /user/HDIUser/NOTICE.txt
		-rw-r--r--   1 username hdfs       1397 2014-09-08 12:56 /user/HDIUser/README.txt

5. Führen Sie den folgenden Befehl aus, um den MapReduce-Auftrag zum Zählen von Wörtern auszuführen:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop jar C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\hadoop\mapreduce\hadoop-mapreduce-examples-2.4.0.2.1.3.0-1981.jar wordcount /user/HDIUser/*.txt /user/HDIUser/WordCount_Output

6. Führen Sie den folgenden Befehl aus, um die Wörter, die "windows" enthalten, in der Ausgabedatei aufzulisten:

		hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"

	Die Ausgabe sollte wie folgt aussehen:

		C:\hdp\hadoop-2.4.0.2.1.3.0-1981>hadoop fs -cat /user/HDIUser/WordCount_Output/part-r-00000 | findstr "windows"
		windows 4
		windows.        2
		windows/cygwin. 1

Weitere Informationen zu Hadoop-Befehlen finden Sie im [Handbuch zu Hadoop-Befehlen][hadoop-commands-manual].

##<a name="rungetstartedsamples"></a> Analysieren der Web-Beispielprotokolldaten

Die Installation des HDInsight-Emulators enthält einige Beispiele, mit denen neue Benutzer den Umgang mit Apache Hadoop-basierten Diensten unter Windows schnell lernen können. Diese Beispiele decken einige typische Aufgaben bei der Verarbeitung großer Datasets ab. Aufbauend auf dem zuvor erwähnten Lernprogramm zu MapReduce-helfen Ihnen die Beispiele, mit den Konzepten des MapReduce-Programmiermodells und dessen Ökosystem vertraut zu werden.

Die Beispieldaten zentrieren um die Verarbeitung der vom IIS World Wide Web Consortium (W3C) bereitgestellten Protokolldaten. Es wird ein Datengenerierungstool zur Verfügung gestellt, mit dem die Datasets erstellt und in verschiedenen Größen in HDFS oder Azure-Blobspeicher importiert werden können. Weitere Informationen finden Sie unter [Verwenden von Azure-Blobspeicher für HDInsight](../hdinsight-use-blob-storage.md)). MapReduce-, Pig- oder Hive-Aufträge können anschließend für die Datenseiten ausgeführt werden, die vom Azure PowerShell-Skript generiert wurden. Die Pig- und Hive-Skripts sind eine Abstraktionsebene über MapReduce und werden letztlich zu MapReduce-Programmen kompiliert. Sie können eine Reihe von Jobs ausführen, um die Auswirkungen dieser unterschiedlichen Technologien und der Datengröße auf die Ausführung der Verarbeitungsaufgaben zu beobachten.

### Inhalt dieses Abschnitts

- [Das IIS W3C-Protokolldatenszenario](#scenarios)
- [Laden von W3C-Beispielprotokolldaten](#loaddata)
- [Ausführen des Java MapReduce-Auftrags](#javamapreduce)
- [Ausführen des Hive-Auftrags](#hive)
- [Ausführen des Pig-Auftrags](#pig)
- [Neuerstellung der Beispiele](#rebuild)

###<a name="scenarios"></a>Die IIS W3C-Protokolldatenszenarien

Das W3C-Szenario generiert und importiert IIS W3C-Protokolldaten in drei Größen in HDFS oder Azure-Blobspeicher: 1 MB (klein), 500 MB (mittel) und 2 GB (groß). Es stellt drei Jobtypen zur Verfügung und implementiert jeden davon in C#, Java, Pig und Hive.

- **totalhits** – Berechnet die Gesamtzahl der Anforderungen für eine bestimmte Seite.
- **avgtime** – Berechnet die durchschnittliche Zeit (in Sekunden), die für eine Anforderung pro Seite benötigt wird.
- **errors** – Berechnet die Anzahl der Fehler pro Seite (pro Stunde) für Anforderungen, deren Status 404 oder 500 war.

Diese Beispiele und ihre Dokumentation stellen keine detaillierte Studie oder vollständige Implementierung der wesentlichen Hadoop-Technologien zur Verfügung. Der verwendete Cluster hat nur einen einzigen Knoten. Daher kann die Auswirkung des Hinzufügens weiterer Knoten in dieser Version nicht beobachtet werden.

###<a name="loaddata"></a>Laden von W3C-Beispielprotokolldaten

Das Generieren der Daten und Importieren in HDFS erfolgt über das Azure PowerShell-Skript "importdata.ps1".

**So importieren Sie W3C-Beispielprotokolldaten**

1. Öffnen Sie eine Hadoop-Befehlszeile auf dem Desktop.
2. Wechseln Sie in das Verzeichnis **C:\\hdp\\GettingStarted**.
3. Führen Sie den folgenden Befehl aus, um Daten zu generieren und in HDFS zu importieren:

		powershell -File importdata.ps1 w3c -ExecutionPolicy unrestricted

	Wenn Sie stattdessen Daten in Azure-Blobspeicher laden möchten, finden Sie weitere Informationen dazu unter [Verbinden mit Azure-Blobspeicher](#blobstorage).

4. Führen Sie den folgenden Befehl über die Hadoop-Befehlszeile aus, um die importierten Dateien in HDFS zu kopieren:

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

###<a name="javamapreduce"></a>Ausführen von Java MapReduce-Aufträgen

MapReduce ist die grundlegende Compute Engine für Hadoop. Sie ist standardmäßig in Java implementiert. Es gibt jedoch auch Beispiele, bei denen .NET und Hadoop Streaming in C# verwendet werden. Für die Ausführung eines MapReduce-Jobs gilt folgende Syntax:

	hadoop jar <jarFileName>.jar <className> <inputFiles> <outputFolder>

Die JAR-Datei und die Quelldateien befinden sich im Ordner C:\\Hadoop\\GettingStarted\\Java.

**So führen Sie einen MapReduce-Auftrag zur Ermittlung von Webseitentreffern aus**

1. Öffnen Sie die Hadoop-Befehlszeile.
2. Wechseln Sie in das Verzeichnis **C:\\hdp\\GettingStarted**.
3. Führen Sie den folgenden Befehl aus, um das Ausgabeverzeichnis zu entfernen, für den Fall, dass der Ordner bereits vorhanden ist. Der MapReduce-Job schlägt fehlt, wenn der Ausgabeordner bereits existiert.

		hadoop fs -rm -r /w3c/output

3. Führen Sie den folgenden Befehl aus:

		hadoop jar .\Java\w3c_scenarios.jar "microsoft.hadoop.w3c.TotalHitsForPage" "/w3c/input/small/data_w3c_small.txt" "/w3c/output"

	In der folgenden Tabelle werden die Elemente des Befehls beschrieben: <table border="1"> <tr><td>Parameter</td><td>Hinweis</td></tr> <tr><td>w3c_scenarios.jar</td><td>Die JAR-Datei befindet sich im Ordner "C:\\hdp\\GettingStarted\\Java".</td></tr> <tr><td>microsoft.hadoop.w3c.TotalHitsForPage</td><td>Der Typ kann durch einen der Folgenden ersetzt werden: <ul> <li>microsoft.hadoop.w3c.AverageTimeTaken</li> <li>microsoft.hadoop.w3c.ErrorsByPage</li> </ul></td></tr> <tr><td>/w3c/input/small/data_w3c_small.txt</td><td>Die Eingabedatei kann durch eine der Folgenden ersetzt werden: <ul> <li>/w3c/input/medium/data_w3c_medium.txt</li> <li>/w3c/input/large/data_w3c_large.txt</li> </ul></td></tr> <tr><td>/w3c/output</td><td>Dies ist der Name des Ausgabeordners.</td></tr> </table>

4. Führen Sie den folgenden Befehl aus, um die Ausgabedatei anzuzeigen:

		hadoop fs -cat /w3c/output/part-00000

	Die Ausgabe sollte der Folgenden ähneln:

		c:\Hadoop\GettingStarted>hadoop fs -cat /w3c/output/part-00000
		/Default.aspx   3380
		/Info.aspx      1135
		/UserService    1126

	Die Seite "Default.aspx" erzielt 3360 Treffer, und so weiter. Führen Sie die Befehle erneut aus, ersetzen Sie die Werte anhand der Vorschläge in der obigen Tabelle und beobachten Sie, wie sich die Ausgabe je nach Jobtyp und Größe der Daten ändert.

### <a name="hive"></a>Ausführen von Hive-Aufträgen
Das Hive-Abfragemodul wird Analytikern mit guten SQL-Kenntnissen (Structured Query Language) vertraut vorkommen. Sie bietet eine SQL-ähnliche Schnittstelle und ein relationales Datenmodell für HDFS. Hive verwendet die Sprache HiveQL, die sich sehr ähnlich verhält wie SQL. Hive bietet eine Abstraktionsebene über dem Java-basierten MapReduce-Framework, und die Hive-Abfragen werden zur Laufzeit nach MapReduce kompiliert.

**So führen Sie einen Hive-Auftrag aus**

1. Öffnen Sie eine Hadoop-Befehlszeile.
2. Wechseln Sie in das Verzeichnis **C:\\hdp\\GettingStarted**.
3. Führen Sie den folgenden Befehl aus, um den Ordner **/w3c/hive/input** zu entfernen, für den Fall, dass er bereits vorhanden ist. Der Hive-Auftrag misslingt, falls der Ordner bereits vorhanden ist.

		hadoop fs -rmr /w3c/hive/input

4. Führen Sie den folgenden Befehl aus, um den Ordner **/w3c/hive/input** zu erstellen und die Datendateien in den Ordner /hive/input zu kopieren:

        hadoop fs -mkdir /w3c/hive
		hadoop fs -mkdir /w3c/hive/input

		hadoop fs -cp /w3c/input/small/data_w3c_small.txt /w3c/hive/input

5. Führen Sie den folgenden Befehl aus, um die Skriptdatei **w3ccreate.hql** auszuführen. Das Skript erstellt eine Hive-Tabelle und lädt Daten in die Hive-Tabelle:

	> [AZURE.NOTE]In dieser Phase können Sie auch die HDInsight Visual Studio-Tools zum Ausführen der Hive-Abfrage verwenden. Öffnen Sie Visual Studio, erstellen Sie ein neues Projekt, und wählen Sie in der HDInsight-Vorlage **Hive-Anwendung** aus. Wenn das Projekt geöffnet wird, fügen Sie die Abfrage als neues Element hinzu. Die Abfrage finden Sie unter **C:/hdp/GettingStarted/Hive/w3c**. Nachdem die Abfrage zum Projekt hinzugefügt wurde, ersetzen **${hiveconf:input}** durch **/w3c/hive/input** und klicken dann auf **Senden**.

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

6. Führen Sie den folgenden Befehl aus, um die HiveQL-Skriptdatei **w3ctotalhitsbypage.hql** auszuführen:

	> [AZURE.NOTE]Wie bereits erwähnt, können Sie diese Abfrage auch mit den HDInsight Visual Studio-Tools ausführen.

        C:\hdp\hive-0.13.0.2.1.3.0-1981\bin\hive.cmd -f ./Hive/w3c/w3ctotalhitsbypage.hql

	In der folgenden Tabelle werden die Elemente des Befehls beschrieben: <table border="1"> <tr><td>Datei</td><td>Beschreibung</td></tr> <tr><td>C:\\hdp\\hive-0.13.0.2.1.3.0-1981\\bin\\hive.cmd</td><td>Das Hive-Befehlsskript.</td></tr> <tr><td>C:\\hdp\\GettingStarted\\Hive\\w3c\\w3ctotalhitsbypage.hql</td><td> Sie können die Hive-Skriptdatei durch eine der folgenden Dateien ersetzen: <ul> <li>C:\\hdp\\GettingStarted\\Hive\\w3c\\w3caveragetimetaken.hql</li> <li>C:\\hdp\\GettingStarted\\Hive\\w3c\\w3cerrorsbypage.hql</li> </ul> </td></tr>

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
		/Default.aspx   3380
		/Info.aspx      1135
		/UserService    1126
		Time taken: 49.304 seconds, Fetched: 3 row(s)

Beachten Sie, dass als erster Schritt bei jedem der Jobs eine Tabelle erstellt wird und dann Daten aus der zuvor erstellten Datei in die Tabelle geladen werden. Sie können zu der erstellten Datei navigieren, indem Sie sie mit dem folgenden Befehl unter dem Knoten "/Hive" in HDFS suchen:

	hadoop fs -lsr /apps/hive/

### <a name="pig"></a>Ausführen von Pig-Aufträgen

Bei der Pig-Verarbeitung wird eine als *Pig Latin* bezeichnete Datenflusssprache verwendet. Pig-Latin-Abstraktionen bieten komplexere Datenstrukturen als MapReduce und leisten für Hadoop, was SQL für relationale Datenbankmanagementsysteme leistet.


**So führen Sie die Pig-Aufträge aus**

1. Öffnen Sie eine Hadoop-Befehlszeile.
2. Wechseln Sie zum Ordner **C:\\hdp\\GettingStarted**.
3. Führen Sie den folgenden Befehl aus, um einen Pig-Job zu übermitteln:

		C:\hdp\pig-0.12.1.2.1.3.0-1981\bin\pig.cmd -f ".\Pig\w3c\TotalHitsForPage.pig" -p "input=/w3c/input/small/data_w3c_small.txt"

	Die folgende Tabelle enthält die Elemente des Befehls: <table border="1"> <tr><td>Datei</td><td>Beschreibung</td></tr> <tr><td>C:\\hdp\\pig-0.12.1.2.1.3.0-1981\\bin\\pig.cmd</td>Das Pig-Befehlsskript<td>.</td></tr> <tr><td>C:\\hdp\\GettingStarted\\Pig\\w3c\\TotalHitsForPage.Pig</td><td> Sie können die Pig Latin-Skriptdatei durch eine der folgenden Dateien ersetzen: <ul> <li>C:\\hdp\\GettingStarted\\Pig\\w3c\\AverageTimeTaken.pig</li> <li>C:\\hdp\\GettingStarted\\Pig\\w3c\\ErrorsByPage.pig</li> </ul> </td></tr> <tr><td>/w3c/input/small/data_w3c_small.txt</td><td> Sie können den Parameter durch eine größere Datei ersetzen:

	<ul>
<li>/w3c/input/medium/data_w3c_medium.txt</li>
<li>/w3c/input/large/data_w3c_large.txt</li>
</ul></td></tr> </table>

	Die Ausgabe sollte in etwa wie folgt aussehen:

		(/Info.aspx,1135)
		(/UserService,1126)
		(/Default.aspx,3380)

Beachten Sie, dass möglicherweise im Verlauf eines Pig-Jobs mehrere MapReduce-Jobs ausgeführt werden. Dies liegt daran, dass ein Pig-Skript unter Umständen in mehrere MapReduce-Jobs kompiliert wird.

<!---
### <a name="rebuild"></a>Rebuild the samples
The samples currently contain all the required binaries, so building is not required. If you'd like to make changes to the Java or .NET samples, you can rebuild them by using either the Microsoft Build Engine (MSBuild) or the included Azure PowerShell script.


**To rebuild the samples**

1. Open a Hadoop command line.
2. Run the following command:

		powershell -F buildsamples.ps1
--->

##<a name="blobstorage"></a>Verbinden mit Azure-Blobspeicher
Der HDInsight-Emulator verwendet HDFS als Standard-Dateisystem. Azure HDInsight verwendet jedoch Azure-Blobspeicher als Standard-Dateisystem. Der HDInsight-Emulator kann so konfiguriert werden, dass Azure-Blobspeicher anstelle von lokalem Speicher verwendet wird. Führen Sie die folgenden Schritte aus, um einen Speichercontainer in Azure zu erstellen und mit dem HDInsight-Emulator zu verbinden.

>[AZURE.NOTE]Weitere Informationen zur Verwendung von Azure-Blobspeicher durch HDInsight finden Sie unter [Verwenden von Azure-Blobspeicher mit HDInsight](../hdinsight-use-blob-storage.md).

Sie benötigen ein Speicherkonto, um die folgenden Schritte ausführen zu können. Informationen dazu finden Sie unter [Erstellen eines Speicherkontos](../storage-create-storage-account.md).

**So erstellen Sie einen Container**

1. Melden Sie sich beim [Azure-Portal][azure-management-portal] an.
2. Klicken Sie auf der linken Seite auf **Speicher**. Eine Liste der Speicherkonten für Ihr Abonnement wird angezeigt.
3. Klicken Sie auf das Speicherkonto in der Liste, für das Sie den Container erstellen möchten.
4. Klicken Sie oben auf der Seite auf **Container**.
5. Klicken Sie unten auf der Seite auf **Hinzufügen**.
6. Geben Sie den **Namen** ein, und wählen Sie **Zugriff** aus. Sie können jede der drei Zugriffsstufen verwenden. Die Standardstufe ist **Privat**.
7. Klicken Sie zum Speichern der Änderungen auf **OK**. Der neue Container wird jetzt im Portal aufgeführt.

Bevor Sie auf ein Azure-Speicherkonto zugreifen können, müssen Sie der Konfigurationsdatei den Kontonamen und den Kontoschlüssel hinzufügen.

**So konfigurieren Sie die Verbindung mit einem Azure-Speicherkonto**

1. Öffnen Sie in Editor die Datei **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\etc\\hadoop\\core-site.xml**.
2. Fügen Sie das folgende <property>-Tag neben den anderen <property>-Tags hinzu:

		<property>
		    <name>fs.azure.account.key.<StorageAccountName>.blob.core.windows.net</name>
		    <value><StorageAccountKey></value>
		</property>

	Sie müssen <StorageAccountName> und <StorageAccountKey> durch die Werte ersetzen, die Ihren Speicherkontoinformationen entsprechen.

3. Speichern Sie die Änderungen. Ein Neustart der Hadoop-Dienste ist nicht erforderlich.

Verwenden Sie die folgende Syntax für den Zugriff auf das Speicherkonto:

	wasb://<ContainerName>@<StorageAccountName>.blob.core.windows.net/

Zum Beispiel:

	hadoop fs -ls wasb://myContainer@myStorage.blob.core.windows.net/


##<a name="powershell"></a> Ausführen von Azure PowerShell
Einige der Azure PowerShell-Cmdlets werden ebenfalls für den HDInsight-Emulator unterstützt. Zu diesem Cmdlets zählen die Folgenden:

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

Sie werden zu einer Eingabe aufgefordert, wenn Sie Get-Credential aufrufen. Sie müssen **hadoop** als Benutzernamen verwenden. Das Kennwort kann eine beliebige Zeichenfolge sein. Der Clustername ist stets **http://localhost:50111**.

Weitere Informationen zur Übermittlung von Hadoop-Aufträgen finden Sie unter [Programmgesteuerte Übermittlung von Hadoop-Aufträgen](hdinsight-submit-hadoop-jobs-programmatically.md). Weitere Informationen zu den Azure PowerShell-Cmdlets für HDInsight finden Sie unter [HDInsight-Cmdlet-Referenz][hdinsight-powershell-reference].


##<a name="remove"></a> Entfernen des HDInsight-Emulators
Öffnen Sie auf dem Computer, auf dem der Emulator installiert ist, die Systemsteuerung, und klicken Sie unter **Programme** auf **Programm deinstallieren**. Klicken Sie in der Liste der installierten Programme mit der rechten Maustaste auf **Microsoft HDInsight-Emulator für Azure** und dann auf **Deinstallieren**.


##<a name="nextsteps"></a> Nächste Schritte
In diesem Lernprogramm zu MapReduce haben Sie den HDInsight-Emulator – eine Hadoop-Sandbox – installiert und einige Hadoop-Aufträge ausgeführt. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Erste Schritte mit Azure HDInsight](../hdinsight-get-started.md)
- [Entwickeln von Java MapReduce-Programmen für HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
- [Entwickeln von Hadoop-Streaming-MapReduce-Programmen in C# für HDInsight](hdinsight-hadoop-develop-deploy-streaming-jobs.md)
- [Anmerkungen zu dieser Version von HDInsight Emulator](hdinsight-emulator-release-notes.md)
- [MSDN-Forum zur Erörterung von Themen rund um HDInsight](http://social.msdn.microsoft.com/Forums/hdinsight)



[azure-sdk]: http://azure.microsoft.com/downloads/
[azure-create-storage-account]: ../storage-create-storage-account.md
[azure-management-portal]: https://manage.windowsazure.com/
[netstat-url]: http://technet.microsoft.com/library/ff961504.aspx

[hdinsight-develop-mapreduce]: hdinsight-develop-deploy-java-mapreduce.md

[hdinsight-emulator-install]: http://www.microsoft.com/web/gallery/install.aspx?appid=HDINSIGHT
[hdinsight-emulator-release-notes]: hdinsight-emulator-release-notes.md

[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-powershell-reference]: https://msdn.microsoft.com/library/dn858087.aspx
[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-develop-deploy-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md
[hdinsight-versions]: hdinsight-component-versioning.md

[Powershell-install-configure]: ../install-configure-powershell.md

[hadoop-commands-manual]: http://hadoop.apache.org/docs/r1.1.1/commands_manual.html

[image-hdi-emulator-services]: ./media/hdinsight-hadoop-emulator-get-started/HDI.Emulator.Services.png
 

<!---HONumber=July15_HO2-->