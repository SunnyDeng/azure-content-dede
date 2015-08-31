<properties
   	pageTitle="Linux-Lernprogramm: Erste Schritte mit Hadoop und Hive | Microsoft Azure"
   	description="Führen Sie dieses Linux-Lernprogramm aus, um sich mit Hadoop in HDInsight vertraut zu machen. Erfahren Sie, wie Sie Linux-Cluster bereitstellen und Daten mit Hive abfragen."
   	services="hdinsight"
   	documentationCenter=""
   	authors="nitinme"
   	manager="paulettm"
   	editor="cgronlun"
	tags="azure-portal"/>

<tags
   	ms.service="hdinsight"
   	ms.devlang="na"
   	ms.topic="hero-article"
   	ms.tgt_pltfrm="na"
   	ms.workload="big-data"
   	ms.date="08/07/2015"
   	ms.author="nitinme"/>

# Hadoop-Lernprogramm: Erste Schritte bei der Verwendung von Hadoop mit Hive in HDInsight unter Linux (Vorschau)

> [AZURE.SELECTOR]
- [Windows](hdinsight-hadoop-tutorial-get-started-windows.md)
- [Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

In diesem Lernprogramm zu Hadoop erhalten Sie Informationen zu den ersten Schritten mit Azure HDInsight unter Linux, indem Ihnen die Bereitstellung eines Hadoop-Clusters unter Linux und die Ausführung einer Hive-Abfrage gezeigt wird.


> [AZURE.NOTE]Wenn Sie noch keine Erfahrung mit Hadoop und Big Data haben, erfahren Sie mehr zu den Begriffen <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510086" target="_blank">MapReduce</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510087" target="_blank">Hadoop Distributed File System (HDFS)</a> und <a href="http://go.microsoft.com/fwlink/?LinkId=510085" target="_blank">Hive</a>. Wie HDInsight Hadoop in Azure bereitstellt, erfahren Sie unter [Einführung in Hadoop in HDInsight](hdinsight-hadoop-introduction.md).


## Welchen Zweck verfolgt dieses Lernprogramm?

Angenommen, Sie verfügen über einen großen unstrukturierten Datensatz und möchten Abfragen für diesen ausführen, um aussagekräftige Informationen zu extrahieren. Und so geht's:

   ![In diesem Hadoop-Lernprogramm werden folgende Schritte ausgeführt: Erstellen eines Speicherkontos, Bereitstellen eines Hadoop-Clusters und Abfragen von Daten mit Hive.](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.Linux.GetStartedFlow.png)


## Voraussetzungen

Bevor Sie mit diesem Linux-Lernprogramm beginnen können, benötigten Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Abrufen der kostenlosen Testversion von Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Secure Shell (SSH)-Schlüssel**. Wenn Sie mit SSH remote auf einen Linux-Cluster zugreifen und dabei anstelle eines Kennworts einen Schlüssel verwenden möchten. Bei Verwendung eines Schlüssels wird diese Methode empfohlen, da sie sicherer ist. Anweisungen zum Generieren von SSH-Schlüsseln finden Sie in den folgenden Artikeln:
	-  Auf einem Linux-Computer: [Verwenden von SSH mit Linux-basiertem HDInsight (Hadoop) unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md).
	-  Auf einem Windows-Computer: [Verwenden von SSH mit Linux-basiertem HDInsight (Hadoop) unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md).

**Geschätzter Zeitaufwand:** 30 Minuten

## <a name="provision"></a>Bereitstellen eines HDInsight-Clusters unter Linux

Beim Bereitstellen eines Clusters werden Azure-Compute-Ressourcen bereitgestellt, die Hadoop und verwandte Anwendungen enthalten. In diesem Abschnitt stellen Sie einen HDInsight-Cluster der Version 3.2 bereit. Sie können auch Hadoop-Cluster für andere Versionen erstellen. Anweisungen finden Sie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight][hdinsight-provision]. Informationen zu den verschiedenen HDInsight-Versionen und ihren SLAs finden Sie unter [HDInsight-Komponentenversionen](hdinsight-component-versioning.md).

>[AZURE.NOTE]Sie können auch Hadoop-Cluster erstellen, die das Windows Server-Betriebssystem ausführen. Anweisungen hierzu finden Sie unter [Erste Schritte mit HDInsight unter Windows](hdinsight-hadoop-tutorial-get-started-windows.md).


**So stellen Sie HDInsight-Cluster bereit**

1. Melden Sie sich beim [Azure-Vorschauportal](https://ms.portal.azure.com/) an.
2. Klicken Sie auf **NEU**, auf **Datenanalyse** und anschließend auf **HDInsight**.

    ![Erstellen eines neuen Clusters im Azure-Vorschauportal](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.1.png "Erstellen eines neuen Clusters im Azure-Vorschauportal")

3. Geben Sie einen **Clusternamen** ein, wählen Sie **Hadoop** als **Clustertyp** aus, und wählen Sie in der Dropdownliste **Clusterbetriebssystem** den Eintrag **Ubuntu** aus. Wenn der Clustername verfügbar ist, wird neben dem Namen ein grünes Häkchen angezeigt.

	![Clusternamen und -typ eingeben](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.2.png "Clusternamen und -typ eingeben")

4. Falls Sie mehrere Abonnements besitzen, klicken Sie auf den Eintrag **Abonnement**, um das Azure-Abonnement für den Cluster auszuwählen.

5. Klicken Sie auf **Ressourcengruppe**, um eine Liste der vorhandenen Ressourcengruppen anzuzeigen, und wählen Sie dann die Gruppe aus, in der der Cluster erstellt werden soll. Alternativ können Sie eine neue Ressourcengruppe erstellen, indem Sie auf **Neu erstellen** klicken und den Namen der neuen Gruppe eingeben. Wenn der neue Gruppenname verfügbar ist, wird neben dem Namen ein grünes Häkchen angezeigt.

	> [AZURE.NOTE]Dieser Eintrag ist standardmäßig auf eine Ihrer vorhandenen Ressourcengruppen festgelegt (sofern verfügbar).

6. Klicken Sie auf **Anmeldeinformationen**, und geben Sie ein Kennwort für den Administrator ein. Außerdem müssen Sie einen **SSH-Benutzernamen** und entweder ein **Kennwort** oder einen **öffentlichen Schlüssel** zum Authentifizieren des SSH-Benutzers eingeben. Es wird empfohlen, einen öffentlichen Schlüssel zu verwenden. Klicken Sie unten auf **Auswählen**, um die Konfiguration der Anmeldeinformationen zu speichern.

	![Clusteranmeldeinformationen eingeben](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.3.png "Clusteranmeldeinformationen eingeben")

	Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie in den folgenden Artikeln:

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)


7. Klicken Sie auf **Datenquelle**, um eine vorhandene Datenquelle für den Cluster auszuwählen, oder erstellen Sie eine neue Datenquelle. Bei der Bereitstellung eines Hadoop-Clusters in HDInsight geben Sie ein Azure-Speicherkonto an. Ein bestimmter Blobs-Speicher-Container aus diesem Konto wird genau wie in HDFS (Hadoop Distributed File System) als Standarddateisystem festgelegt. Der HDInsight-Cluster wird standardmäßig in demselben Rechenzentrum bereitgestellt wie das angegebene Speicherkonto. Weitere Informationen finden Sie unter [Verwenden von Azure-Blob-Speicher mit HDInsight](hdinsight-use-blob-storage.md).

	![Blatt „Datenquelle“](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.4.png "Datenquellenkonfiguration angeben")

	Zurzeit kann ein Azure-Speicherkonto als Datenquelle für einen HDInsight-Cluster ausgewählt werden. Die Einträge auf dem Blatt **Datenquelle** werden im Folgenden erläutert.

	- **Auswahlmethode**: Wählen Sie **Aus allen Abonnements** aus, damit Speicherkonten aus all Ihren Abonnements durchsucht werden können. Wählen Sie **Zugriffsschlüssel** aus, wenn Sie den **Speichernamen** und **Zugriffsschlüssel** eines vorhandenen Speicherkontos eingeben möchten.

	- **Speicherkonto auswählen/Neu erstellen**: Klicken Sie auf **Speicherkonto auswählen**, um ein vorhandenes Speicherkonto auszuwählen, das Sie dem Cluster zuordnen möchten. Alternativ können Sie auf **Neu erstellen** klicken, um ein neues Speicherkonto zu erstellen. Geben Sie den Namen des Speicherkontos in das angezeigte Feld ein. Wenn der Name verfügbar ist, wird ein grünes Häkchen angezeigt.

	- **Standardcontainer auswählen**: Geben Sie den Namen des Standardcontainers für den Cluster ein. Sie können einen beliebigen Namen eingeben, es wird jedoch empfohlen, den Namen des Clusters zu verwenden. So können Sie leichter erkennen, dass es sich um den Container für diesen spezifischen Cluster handelt.

	- **Standort**: Die geografische Region, in der sich das Speicherkonto befindet bzw. in der es erstellt wird.

		> [AZURE.IMPORTANT]Durch die Auswahl des Standorts für die Standarddatenquelle wird auch der Standort des HDInsight-Clusters festgelegt. Der Cluster und die Standarddatenquelle müssen sich in der gleichen Region befinden.

	Klicken Sie auf **Auswählen**, um die Datenquellenkonfiguration zu speichern.

8. Klicken Sie auf **Knotenpreistarife**, um Informationen zu den Knoten anzuzeigen, die für diesen Cluster erstellt werden. Legen Sie die Anzahl von Workerknoten fest, die Sie für den Cluster benötigen. Die vorkalkulierten Kosten für den Cluster werden auf dem Blatt angezeigt.

	![Blatt „Knotenpreistarife“](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.CreateCluster.5.png "Anzahl von Clusterknoten angeben")

	Klicken Sie auf **Auswählen**, um die Konfiguration der Knotenpreise zu speichern.

9. Vergewissern Sie sich auf dem Blatt **Neues HDInsight-Cluster**, dass **An Startmenü anheften** ausgewählt ist, und klicken Sie dann auf **Erstellen**. Der Cluster wird erstellt, und dem Startmenü Ihres Azure-Portals wird eine Kachel für den Cluster hinzugefügt. Das Symbol zeigt an, dass der Cluster bereitgestellt wird. Sobald die Bereitstellung abgeschlossen ist, ändert es sich in das HDInsight-Symbol.

Während der Bereitstellung|Nach Abschluss der Bereitstellung
------------------|---------------------
	![Bereitstellungsanzeige im Startmenü](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioning.png)|![Kachel für einen bereitgestellten Cluster](./media/hdinsight-hadoop-linux-tutorial-get-started/provisioned.png)

> [AZURE.NOTE]Die Erstellung des Clusters dauert in der Regel ca. 15 Minuten. Sie können den Status des Bereitstellungsprozesses auf der Kachel im Startmenü oder im linken Bereich der Seite unter **Benachrichtigungen** überprüfen.

Klicken Sie nach Abschluss der Bereitstellung im Startmenü auf die Kachel für den Cluster, um das Clusterblatt zu öffnen.

## <a name="hivequery"></a>Übermitteln eines Hive-Auftrags an den Cluster
Nachdem Sie einen HDInsight Linux-Cluster bereitgestellt haben, führen Sie im nächsten Schritt einen Hive-Beispielauftrag aus, um Beispieldaten (sample.log) abzufragen, die in HDInsight-Clustern enthalten sind. Die Beispieldaten enthalten Protokollinformationen, einschließlich Ablaufverfolgung, Warnungen, Informationen und Fehler. Wir fragen diese Daten ab, um alle Fehlerprotokolle mit einem bestimmten Schweregrad abzurufen. Führen Sie die folgenden Schritte aus, um eine Hive-Abfrage auf einen HDInsight Linux-Cluster anzuwenden:

- Verbinden mit einem Linux-Cluster
- Ausführen eines Hive-Jobs



### So stellen Sie die Verbindung zu einem Cluster her

Sie können die Verbindung mit einem HDInsight-Cluster unter Linux über einen Linux-Computer oder einen Windows-Computer mit SSH herstellen.

**So stellen Sie die Verbindung auf einem Linux-Computer her**

1. Öffnen Sie ein Terminal, und geben Sie den folgenden Befehl ein:

		ssh <username>@<clustername>-ssh.azurehdinsight.net

	Da Sie einen Cluster mithilfe der Option "Schnellerfassung" bereitgestellt haben, lautet der SSH-Standardbenutzername **hdiuser**. Daher muss der Befehl wie folgt lauten:

		ssh hdiuser@myhdinsightcluster-ssh.azurehdinsight.net

2. Geben Sie nach der entsprechenden Aufforderung das Kennwort ein, das Sie bei der Bereitstellung des Clusters angegeben haben. Nachdem Sie die Verbindung erfolgreich hergestellt haben, zeigt die Eingabeaufforderung Folgendes an:

		hdiuser@headnode-0:~$


**So stellen Sie die Verbindung auf einem Windows-Computer her**

1. Laden Sie <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">PuTTY</a> für Windows-Clients herunter.

2. Öffnen Sie PuTTY. Klicken Sie in **Category** auf **Session**. Geben Sie auf dem Bildschirm **Basic options for your PuTTY session** die SSH-Adresse Ihres HDInsight-Servers in das Feld **Host name (or IP address)** ein. Die SSH-Adresse ist Ihr Clustername, gefolgt von "-ssh.azurehdinsight.net". Beispiel: **myhdinsightcluster-ssh.azurehdinsight.net**.

	![Herstellen einer Verbindung mit einem HDInsight-Cluster unter Linux über PuTTY](./media/hdinsight-hadoop-linux-tutorial-get-started/HDI.linux.connect.putty.png)

3. Geben Sie zum Speichern der Verbindungsinformationen für die künftige Verwendung einen Namen für diese Verbindung unter **Saved Sessions** ein, und klicken Sie dann auf **Save**. Die Verbindung wird zur Liste der gespeicherten Sitzungen hinzugefügt.

4. Klicken Sie auf **Open**, um die Verbindung mit dem Cluster herzustellen. Geben Sie **hdiuser** ein, wenn Sie zur Eingabe des Benutzernamens aufgefordert werden. Als Kennwort geben Sie das während der Bereitstellung des Clusters angegebene Kennwort ein. Nachdem Sie die Verbindung erfolgreich hergestellt haben, zeigt die Eingabeaufforderung Folgendes an:

		hdiuser@headnode-0:~$

### So führen Sie einen Hive-Job aus

Nachdem Sie mit dem Cluster über SSH verbunden sind, verwenden Sie die folgenden Befehle zum Ausführen einer Hive-Abfrage.

1. Starten Sie die Hive-Befehlszeilenschnittstelle (CLI) mit dem folgenden Befehl an der Eingabeaufforderung:

		hive

2. Geben Sie die folgenden Anweisungen zum Erstellen einer neuen Tabelle namens **log4jLogs** mithilfe der Befehlszeilenschnittstelle ein, wobei Sie die bereits im Cluster verfügbare Beispieldaten verwenden:

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
		STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	Mit diesen Anweisungen werden die folgenden Aktionen ausgeführt:

	- **DROP TABLE** löscht die Tabelle und Datendatei, falls die Tabelle bereits vorhanden ist.
	- **CREATE EXTERNAL TABLE** erstellt eine neue externe Tabelle in Hive. Externe Tabellen dienen nur zum Speichern der Tabellendefinition in Hive. Die Daten verbleiben am ursprünglichen Speicherort.
	- **ROW FORMAT** teilt Hive mit, wie die Daten formatiert werden sollen. In diesem Fall werden die Felder in den einzelnen Protokollen durch Leerzeichen getrennt.
	- **SPEICHERORT DER TEXTDATEI** – Teilt Hive den Speicherort der Daten (das Verzeichnis "example/data") und die Information mit, dass die Speicherung als Text erfolgt.
	- **AUSWÄHLEN** – Wählt die Anzahl aller Zeilen aus, bei denen die Spalte "t4" den Wert "FEHLER" enthält.

	>[AZURE.NOTE]Wenn erwartet wird, dass die zugrunde liegenden Daten über eine externe Quelle, z. B. einen automatisierten Prozess zum Hochladen von Daten, oder über einen anderen MapReduce-Vorgang aktualisiert, aber von Hive immer die neuesten Daten verwendet werden, sollten externe Tabellen verwendet werden. Durch das Löschen einer externen Tabelle werden *nicht* die Daten, sondern nur die Tabellendefinitionen gelöscht.

	Dadurch wird die folgende Ausgabe zurückgegeben:

		Query ID = hdiuser_20150116000202_cceb9c6b-4356-4931-b9a7-2c373ebba493
		Total jobs = 1
		Launching Job 1 out of 1
		Number of reduce tasks not specified. Estimated from input data size: 1
		In order to change the average load for a reducer (in bytes):
		  set hive.exec.reducers.bytes.per.reducer=<number>
		In order to limit the maximum number of reducers:
		  set hive.exec.reducers.max=<number>
		In order to set a constant number of reducers:
		  set mapreduce.job.reduces=<number>
		Starting Job = job_1421200049012_0006, Tracking URL = <URL>:8088/proxy/application_1421200049012_0006/
		Kill Command = /usr/hdp/2.2.1.0-2165/hadoop/bin/hadoop job  -kill job_1421200049012_0006
		Hadoop job information for Stage-1: number of mappers: 1; number of reducers: 1
		2015-01-16 00:02:40,823 Stage-1 map = 0%,  reduce = 0%
		2015-01-16 00:02:55,488 Stage-1 map = 100%,  reduce = 0%, Cumulative CPU 3.32 sec
		2015-01-16 00:03:05,298 Stage-1 map = 100%,  reduce = 100%, Cumulative CPU 5.62 sec
		MapReduce Total cumulative CPU time: 5 seconds 620 msec
		Ended Job = job_1421200049012_0006
		MapReduce Jobs Launched:
		Stage-Stage-1: Map: 1  Reduce: 1   Cumulative CPU: 5.62 sec   HDFS Read: 0 HDFS Write: 0 SUCCESS
		Total MapReduce CPU Time Spent: 5 seconds 620 msec
		OK
		[ERROR]    3
		Time taken: 60.991 seconds, Fetched: 1 row(s)

	Beachten Sie, das die Ausgabe **[FEHLER] 3** enthält, da drei Zeilen vorhanden sind, die diesen Wert enthalten.

3. Verwenden Sie die folgenden Anweisungen zum Erstellen einer neuen "internen" Tabelle namens **errorLogs**:

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';


	Mit diesen Anweisungen werden die folgenden Aktionen ausgeführt:

	- **TABELLE ERSTELLEN, FALLS NICHT VORHANDEN** – Erstellt eine Tabelle, sofern diese noch nicht vorhanden ist. Da das Schlüsselwort **EXTERN** nicht verwendet wird, ist dies eine "interne" Tabelle, die im Hive-Data Warehouse gespeichert und vollständig von Hive verwaltet wird. Im Gegensatz zu externen Tabellen werden beim Ablegen von internen Tabellen auch die zugrunde liegenden Daten gelöscht.
	- **ALS ORC GESPEICHERT** – Speichert die Daten im ORC-Format (Optimized Row Columnar) Dies ist ein stark optimiertes und effizientes Format zum Speichern von Hive-Daten.
	- **ÜBERSCHREIBEN EINFÜGEN ... AUSWÄHLEN** – Wählt Zeilen in der Tabelle **log4jLogs** aus, die [FEHLER] enthalten, und fügt die Daten dann in die Tabelle **errorLogs** ein.

4. Um zu überprüfen, dass nur Zeilen, die [FEHLER] in Spalte "t4" enthalten, in der Tabelle **errorLogs** gespeichert wurden, verwenden Sie die folgende Anweisung, um alle Zeilen aus **errorLogs** zurückzugeben:

		SELECT * from errorLogs;

	Die folgende Ausgabe sollte in der Konsole angezeigt werden:

		2012-02-03	18:35:34	SampleClass0	[ERROR]	 incorrect		id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	 incorrect		id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	 incorrect		id
		Time taken: 0.987 seconds, Fetched: 3 row(s)

	Die zurückgegebenen Daten sollten alle den [FEHLER]-Protokollen entsprechen.

## <a name="nextsteps"></a>Nächste Schritte
In diesem Linux-Lernprogramm haben Sie erfahren, wie Sie einen Hadoop-Cluster unter Linux mit HDInsight bereitstellen und darauf eine Hive-Abfrage mithilfe von SSH anwenden. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Verwalten von HDInsight-Clustern mit Ambari](hdinsight-hadoop-manage-ambari.md): Für Linux-basierte HDInsight-Cluster wird Ambari zur Verwaltung und Überwachung von Hadoop-Diensten verwendet. Die Ambari-Webbenutzeroberfläche ist auf jedem Cluster unter https://CLUSTERNAME.azurehdinsight.net verfügbar.

	> [AZURE.IMPORTANT]Während viele Abschnitte der Ambari-Webbenutzeroberfläche direkt über das Internet zugänglich sind, muss zum Anzeigen der Webbenutzeroberfläche für Hadoop-Dienste (z. B. Ressourcen-Manager oder Auftragsverlauf) ein SSH-Tunnel verwendet werden. Weitere Informationen zur Verwendung eines SSH-Tunnels mit HDInsight finden Sie in den folgenden Artikeln:
	>
	> * [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md#tunnel)
	> * [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md#tunnel)

- [Benutzerdefinierte Bereitstellung von HDInsight unter Linux](hdinsight-hadoop-provision-linux-clusters.md): Dieser Artikel enthält ausführliche Informationen zur Bereitstellung von HDInsight-Clustern.

- [Arbeiten mit HDInsight unter Linux](hdinsight-hadoop-linux-information.md): Wenn Sie bereits mit Hadoop auf Linux-Plattformen vertraut sind, finden Sie in diesem Dokument Azure-spezifische Informationen, z. B.:

	* URLs für im Cluster gehostete Dienste, z. B. Ambari und WebHCat
	* Speicherort von Hadoop-Dateien und -Beispielen im lokalen Dateisystem
	* Verwendung von Azure-Speicher (WASB) anstelle von HDFS als Standarddatenspeicher

- Weitere Informationen zu Hive, Pig und MapReduce finden Sie in den folgenden Artikeln:

	- [Verwenden von MapReduce mit HDInsight][hdinsight-use-mapreduce]
	- [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
	- [Verwenden von Pig mit HDInsight][hdinsight-use-pig]

- Weitere Informationen zum Arbeiten mit dem von Ihrem HDInsight-Cluster verwendeten Azure-Speicher finden Sie in den folgenden Artikeln:

	- [Verwenden von Azure-Blobspeicher mit HDInsight](../hdinsight-use-blob-storage.md)
	- [Hochladen von Daten in HDInsight][hdinsight-upload-data]


[1]: ../HDInsight/hdinsight-hadoop-visual-studio-tools-get-started.md

[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell.md
[powershell-open]: ../install-configure-powershell.md#Install

[img-hdi-dashboard]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-hadoop-tutorial-get-started-windows/HDI.GettingStarted.PowerQuery.ImportData2.png

<!---HONumber=August15_HO8-->