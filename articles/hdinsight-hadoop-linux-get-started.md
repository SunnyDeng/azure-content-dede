<properties 
   pageTitle="Erste Schritte bei der Verwendung von Hadoop mit Hive in HDInsight unter Linux | Azure" 
   description="Erste Schritte mit Hadoop in HDInsight unter Linux. Erfahren Sie, wie Sie unter Linux ausgeführte HDInsight Hadoop-Cluster bereitstellen und Daten mithilfe von Hive abfragen." 
   services="hdinsight" 
   documentationCenter="" 
   authors="nitinme" 
   manager="paulettm" 
   editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/18/2015"
   ms.author="nitinme"/>

# Erste Schritte bei der Verwendung von Hadoop mit Hive in HDInsight unter Linux (Vorschau)

In diesem Lernprogramm erhalten Sie Informationen zu den ersten Schritten mit HDInsight unter Linux, indem Ihnen die Bereitstellung eines HDInsight Hadoop-Clusters unter Linux und die Ausführung einer Hive-Abfrage zum Extrahieren aussagekräftiger Informationen aus unstrukturierten Daten gezeigt wird. Anschließend analysieren Sie die Ergebnisse in einem BI-Tool (Business Intelligence) wie Tableau.


> [AZURE.NOTE] Wenn Sie mit Hadoop und großen Datenmengen nicht vertraut sind, erhalten Sie weitere Informationen zu den Begriffen <a href="http://go.microsoft.com/fwlink/?LinkId=510084" target="_blank">Apache Hadoop</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510086" target="_blank">MapReduce</a>, <a href="http://go.microsoft.com/fwlink/?LinkId=510087" target="_blank">HDFS</a>und <a href="http://go.microsoft.com/fwlink/?LinkId=510085" target="_blank">Hive</a>. Wie HDInsight Hadoop in Azure bereitstellt, erfahren Sie unter [Einführung in Hadoop in HDInsight](../hdinsight-hadoop-introduction/).


## Welchen Zweck verfolgt dieses Lernprogramm? ##

Angenommen, Sie verfügen über einen großen unstrukturierten Datensatz und möchten Abfragen für diesen ausführen, um aussagekräftige Informationen zu extrahieren. Und so geht es:

   !["Get started using Hadoop with Hive in HDInsight" tutorial steps illustrated: create an account; provision a cluster; query data; and analyze in Tableau.](./media/hdinsight-hadoop-linux-get-started/HDI.Linux.GetStartedFlow.png)


**Voraussetzungen:**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:


- Ein Azure-Abonnement. Weitere Informationen zum Erwerb eines Abonnements finden Sie unter <a href="http://azure.microsoft.com/ pricing/purchase-options/" target="_blank">Kaufoptionen</a>, <a href="http://azure.microsoft.com/ pricing/member-offers/" target="_blank">Spezielle Angebote</a>oder <a href="http://azure.microsoft.com/ pricing/free-trial/" target="_blank">Kostenlose Testversion</a>.

**Geschätzter Zeitaufwand:** 30 Minuten

## Dieses Lernprogramm umfasst folgende Punkte

* [Erstellen eines Azure-Speicherkontos](#storage)
* [Bereitstellen eines HDInsight Linux-Clusters](#provision)
* [Übermitteln eines Hive-Jobs](#hivequery)
* [Nächste Schritte](#nextsteps)

## <a name="storage"></a>Erstellen eines Azure-Speicherkontos

HDInsight verwendet Azure Blob-Speicher zum Speichern von Daten. Dies wird als *WASB* oder *Azure Storage - Blob* bezeichnet. WASB ist Microsofts Implementierung von HDFS in Azure Blob-Speicher. Weitere Informationen finden Sie unter [Verwenden von Azure Blob-Speicher mit HDInsight](../hdinsight-use-blob-storage/).

Bei der Bereitstellung eines HDInsight-Clusters geben Sie ein Azure-Speicherkonto an. Ein bestimmter Blob-Speichercontainer aus diesem Konto wird als Standarddateisystem festgelegt, genau wie in HDFS. Der HDInsight-Cluster wird standardmäßig in demselben Rechenzentrum bereitgestellt wie das angegebene Speicherkonto.

Zusätzlich zu diesem Speicherkonto können Sie weitere Speicherkonten hinzufügen, wenn Sie einen HDInsight-Cluster benutzerdefiniert konfigurieren. Diese zusätzlichen Speicherkonten können entweder aus demselben Azure-Abonnement oder anderen Azure-Abonnements stammen. Anweisungen finden Sie unter [Benutzerdefinierte Bereitstellung eines HDInsight Linux-Clusters](../hdinsight-hadoop-provision-linux-clusters). 

Zur Vereinfachung des Lernprogramms werden nur der standardmäßige Blob-Container und das Standardspeicherkonto verwendet. In der Praxis werden die Datendateien in der Regel in einem festgelegten Speicherkonto gespeichert.

**So erstellen Sie ein Azure-Speicherkonto**

1. Melden Sie sich auf dem <a href="https://manage.windowsazure.com/" target="_blank">Azure-Verwaltungsportal</a> an.
2. Klicken Sie links unten auf **NEU**, zeigen Sie auf **DATENDIENSTE**, auf **SPEICHER**, und klicken Sie anschließend auf **SCHNELLERFASSUNG**.

	![Azure portal where you can use Quick Create to set up a new storage account.](./media/hdinsight-hadoop-linux-get-started/HDI.StorageAccount.QuickCreate.png)

3. Geben Sie Daten in **URL**, **SPEICHERORT** und **REPLIKATION** ein, und klicken Sie dann auf **SPEICHERKONTO ERSTELLEN**. Affinitätsgruppen werden nicht unterstützt. Sie werden das neue Speicherkonto in der Speicherliste sehen.

	>[AZURE.NOTE]  Die Schnellerfassungsoption zur Bereitstellung eines HDInsight Linux-Clusters wie die in diesem Lernprogramm verwendete fordert beim Bereitstellen des Clusters nicht zur Angabe eines Standorts auf. Stattdessen wird der Cluster standardmäßig in demselben Rechenzentrum wie das Speicherkonto bereitgestellt. Achten Sie daher darauf, das Speicherkonto an den Standorten zu erstellen, die für den Cluster unterstützt werden; dies sind:  **Ostasien**, **Südostasien**, **Nordeuropa**, **Westeuropa**, **USA (Osten)**, **USA (Westen)**, **USA (Mitte/Norden)**, **USA (Mitte/Süden)**.

4. Warten Sie, bis sich der **STATUS** des neuen Speicherkontos in **Online** geändert hat.
5. Wählen Sie das neue Speicherkonto in der Liste aus, und klicken Sie unten auf der Seite auf **ZUGRIFFSSCHLÜSSEL VERWALTEN**.
7. Notieren Sie sich die Werte unter **SPEICHERKONTONAME** und **PRIMÄRER ZUGRIFFSSCHLÜSSEL** (oder **SEKUNDÄRER ZUGRIFFSSCHLÜSSEL**. Beide können verwendet werden.)  Sie werden diese später im Lernprogramm benötigen.


Weitere Informationen finden Sie unter
[Erstellen eines Speicherkontos](../storage-create-storage-account/) und [Verwenden von Azure Blob-Speicher mit HDInsight](../hdinsight-use-blob-storage/).
	
## <a name="provision"></a>Bereitstellen eines HDInsight-Clusters unter Linux

Beim Bereitstellen eines HDInsight-Clusters werden Azure-Serverressourcen bereitgestellt, die Hadoop und verwandte Anwendungen enthalten. In diesem Abschnitt stellen Sie mithilfe der Option "quick-create" einen HDInsight-Cluster unter Linux bereit. Diese Option verwendet Standardbenutzernamen und Azure-Speichercontainer und konfiguriert einen Cluster mit HDInsight Version 3.2 (Hadoop Version 2.5, HDP Version 2.2), der unter Ubuntu 12.04 LTS ausgeführt wird. Informationen zu den verschiedenen HDInsight-Versionen und ihren SLAs finden Sie auf der Seite zu den [HDInsight-Komponentenversionen](http://azure.microsoft.com/ documentation/articles/hdinsight-component-versioning/).

>[AZURE.NOTE]  Sie können auch Hadoop-Cluster erstellen, die ein Windows Server-Betriebssystem ausführen. Anweisungen hierzu finden Sie unter [Erste Schritte mit HDInsight unter Windows](../hdinsight-get-started/).


**So stellen Sie ein HDInsight-Cluster bereit** 

1. Melden Sie sich auf dem <a href="https://manage.windowsazure.com/" target="_blank">Azure-Verwaltungsportal</a>an. 

2. Klicken Sie links unten auf **NEU** und dann auf **Datendienste**, **HDInsight** und **Hadoop unter Linux**.

	![Creation of a Hadoop cluster in HDInsight.](./media/hdinsight-hadoop-linux-get-started/HDI.QuickCreateCluster.png)

4. Geben Sie folgende Werte ein, bzw. wählen Sie diese aus:

	<table border="1">
	<tr><th>Name</th><th>Wert</th></tr>
	<tr><td>Clustername</td><td>Der Name des Clusters</td></tr>
	<tr><td>Clustergröße</td><td>Die Anzahl der Datenknoten, die Sie bereitstellen möchten. Der Standardwert ist 4. Die Option zur Verwendung von einem oder zwei Datenknoten ist jedoch ebenfalls im Dropdownmenü verfügbar. Mit der Option <strong>Benutzerdefiniert erstellen</strong> kann eine beliebige Anzahl von Clusterknoten angegeben werden. Es sind Preisdetails zur Abrechnung für verschiedene Cluster-Größen verfügbar. Klicken Sie auf das Symbol <strong>?</strong> direkt über dem Dropdownfeld, und folgen Sie dem Link im Popupfenster.</td></tr>
	<tr><td>Kennwort</td><td>Das Kennwort für das Konto <i>HTTP</i> (Standardbenutzername: admin) und <i>SSH</i> (Standardbenutzername: hdiuser). Es handelt sich hierbei NICHT um die Administratorkonten für die virtuellen Computer, auf denen die Cluster bereitgestellt werden. </td></tr>
	
	<tr><td>Speicherkonto</td><td>Wählen Sie das erstellte Speicherkonto im Dropdownfeld aus. <br/>

	Sobald ein Speicherkonto ausgewählt wurde, kann es nicht mehr geändert werden. Wird das Speicherkonto entfernt, kann der Cluster nicht mehr verwendet werden.

	Der HDInsight-Cluster befindet sich in demselben Rechenzentrum wie das Speicherkonto. 
	</td></tr>
	</table>

	Notieren Sie sich den Clusternamen. Sie werden ihn später im Lernprogramm benötigen.

	
5. Klicken Sie auf **HDInsight-Cluster erstellen**. Wenn die Bereitstellung abgeschlossen ist, wird in der Statusspalte **Wird ausgeführt** angezeigt.

	>[AZURE.NOTE] Das oben beschriebene Verfahren erstellt einen Linux-Cluster mithilfe der Option "quick-create", die den SSH-Standardbenutzernamen und Azure-Speichercontainer verwendet. Informationen zum Erstellen eines Clusters mit benutzerdefinierten Optionen (z. B. mithilfe des SSH-Schlüssels zur Authentifizierung oder mithilfe zusätzlicher Speicherkonten) finden Sie in [Benutzerdefiniertes Bereitstellen von HDInsight Linux-Clustern](../hdinsight-hadoop-provision-linux-clusters).


## <a name="hivequery"></a>Übermitteln eines Hive-Auftrags auf dem Cluster
Nachdem Sie einen HDInsight Linux-Cluster bereitgestellt haben, führen Sie im nächsten Schritt einen Hive-Beispielauftrag aus, um Beispieldaten (sample.log) abzufragen, die in HDInsight-Clustern enthalten sind. Die Beispieldaten enthalten Protokollinformationen, einschließlich Ablaufverfolgung, Warnungen, Informationen, Fehler usw. Diese Daten werden abgefragt, um alle Fehlerprotokolle mit einem bestimmten Schweregrad abzurufen. Führen Sie die folgenden Schritte aus, um eine Hive-Abfrage auf einem HDInsight Linux-Cluster auszuführen.

- Verbinden mit einem Linux-Cluster
- Ausführen eines Hive-Jobs



### So stellen Sie die Verbindung zu einem Cluster her

Sie können die Verbindung zu einem HDInsight-Cluster unter Linux über einen Linux-Computer oder einen Windows-Computer mit SSH herstellen.

**So stellen Sie die Verbindung von einem Linux-Computer aus her**

1. Öffnen Sie ein Terminal, und geben Sie den folgenden Befehl ein:

		ssh <username>@<clustername>-ssh.azurehdinsight.net

	Da Sie einen Cluster mithilfe der Option "quick-create" bereitgestellt haben, lautet der SSH-Standardbenutzername **hdiuser**. Daher muss der Befehl wie folgt aussehen:

		ssh hdiuser@myhdinsightcluster-ssh.azurehdinsight.net

2. Geben Sie nach der entsprechenden Aufforderung das Kennwort ein, das Sie bei der Bereitstellung des Clusters angegeben haben. Nachdem Sie die Verbindung erfolgreich hergestellt haben, zeigt die Eingabeaufforderung Folgendes an:

		hdiuser@headnode-0:~$


**So stellen Sie die Verbindung von einem Windows-Computer aus her**

1. Laden Sie **PuTTY** für Windows-Clients herunter. Dies ist unter der folgenden Adresse verfügbar: <a href="http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html" target="_blank">http://www.chiark.greenend.org.uk/~sgtatham/putty/download.html</a>

2. Öffnen Sie **PuTTY**. Klicken Sie in **Category** auf **Session**. Geben Sie auf dem Bildschirm **Basic options for your PuTTY session** die SSH-Adresse Ihres HDInsight-Servers in das Feld **Host name (or IP address)** ein. Die SSH-Adresse ist Ihr Clustername, gefolgt von**-ssh.azurehdinsight.net**. Beispiel: **myhdinsightcluster-ssh.azurehdinsight.net**.

	![Connect to an HDInsight cluster on Linux using PuTTY](./media/hdinsight-hadoop-linux-get-started/HDI.linux.connect.putty.png)

3. Geben Sie zum Speichern der Verbindungsinformationen für die zukünftige Verwendung einen Namen für diese Verbindung unter **Gespeicherte Sitzungen** ein, und klicken Sie dann auf **Speichern**. Die Verbindung wird zur Liste der gespeicherten Sitzungen hinzugefügt.

4. Klicken Sie auf **Öffnen**, um die Verbindung zum Cluster herzustellen. Geben Sie *hdiuser* ein, wenn Sie zur Eingabe des Benutzernamens aufgefordert werden. Für das Kennwort geben Sie das während der Bereitstellung des Clusters angegebene Kennwort ein. Nachdem Sie die Verbindung erfolgreich hergestellt haben, zeigt die Eingabeaufforderung Folgendes an:

		hdiuser@headnode-0:~$

### So führen Sie einen Hive-Job aus

Nachdem Sie mit dem Cluster über SSH verbunden sind, verwenden Sie die folgenden Befehle zum Ausführen einer Hive-Abfrage.

1. Starten Sie die Hive-Befehlszeilenschnittstelle (CLI) mit dem folgenden Befehl an der Eingabeaufforderung:

		hive

2. Geben Sie die folgenden Anweisungen zum Erstellen einer neuen Tabelle namens "log4jLogs" mithilfe der Befehlszeilenschnittstelle ein, wobei Sie die bereits auf dem Cluster verfügbaren Beispieldaten verwenden.

		DROP TABLE log4jLogs;
		CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) 
		ROW FORMAT DELIMITED FIELDS TERMINATED BY ' ' 
		STORED AS TEXTFILE LOCATION 'wasb:///example/data/';
		SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;

	Diese Anweisungen führen die folgenden Aktionen aus.

	- **DROP TABLE** - löscht Tabelle und Datendatei, falls die Tabelle bereits existiert.
	- **CREATE EXTERNAL TABLE** erstellt eine neue 'external' Tabelle in Hive. Externe Tabellen speichern nur die Tabellendefinition in Hive. Die Daten verbleiben am ursprünglichen Speicherort.
	- **ROW FORMAT** - teilt Hive mit, wie die Daten formatiert werden. In diesem Fall werden die Felder in den einzelnen Protokollen durch Leerzeichen getrennt.
	- **STORED AS TEXTFILE LOCATION** - teilt Hive den Speicherort der Daten (das Verzeichnis "example/data") und die Information mit, dass die Speicherung als Text erfolgt.
	- **SELECT** - die Anzahl aller Zeilen auswählen, für die die Spalte "t4" den Wert [ERROR] enthält. 

	>[WACOM.NOTE] Wenn erwartet wird, dass die zugrunde liegenden Daten über eine externe Quelle, z. B. einen automatisierten Prozess zum Hochladen von Daten, oder über einen anderen MapReduce-Vorgang aktualisiert, aber von Hive immer die neuesten Daten verwendet werden, sollten externe Tabellen verwendet werden. Durch das Ablegen einer externen Tabelle werden **nicht** die Daten, sondern nur die Tabellendefinitionen gelöscht.

	Dadurch wird die folgende Ausgabe zurückgegeben.

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

3. Verwenden Sie die folgenden Anweisungen zum Erstellen einer neuen "internen" Tabelle namens **errorLogs**.

		CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
		INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';


	Diese Anweisungen führen die folgenden Aktionen aus.

	- **CREATE TABLE IF NOT EXISTS** - erstellt eine Tabelle, sofern diese noch nicht vorhanden ist. Da das Schlüsselwort EXTERNAL nicht verwendet wird, ist dies eine 'internal' Tabelle, die im Hive-Data Warehouse gespeichert und vollständig von Hive verwaltet wird. Anders als bei **externen** Tabellen werden beim Ablegen von internen Tabellen auch die zugrunde liegenden Daten gelöscht.
	- **STORED AS ORC** - speichert die Daten im ORC-Format (Optimized Row Columnar). Dies ist ein stark optimiertes und effizientes Format zum Speichern von Hive-Daten.
	- **INSERT OVERWRITE ... SELECT** - wählt Zeilen aus der Tabelle **log4jLogs** aus, die [ERROR] enthalten. Dann werden die Daten in die Tabelle **errorLogs** eingefügt.

4. Um zu überprüfen, dass nur Zeilen, die **[ERROR]** in Spalte "t4" enthalten, in der Tabelle **errorLogs** gespeichert wurden, verwenden Sie die folgende Anweisung, um alle Zeilen aus "errorLogs" zurückzugeben.

		SELECT * from errorLogs;

	The following output should be displayed on the console.

		2012-02-03	18:35:34	SampleClass0	[ERROR]	 incorrect		id
		2012-02-03	18:55:54	SampleClass1	[ERROR]	 incorrect		id
		2012-02-03	19:25:27	SampleClass4	[ERROR]	 incorrect		id
		Time taken: 0.987 seconds, Fetched: 3 row(s)

	Die zurückgegebenen Daten sollten alle den [ERROR]-Protokollen entsprechen.


## <a name="nextsteps"></a>Nächste Schritte
In diesem Lernprogramm haben Sie erfahren, wie Sie einen Hadoop Linux-Cluster mit HDInsight bereitstellen und darauf eine Hive-Abfrage mithilfe von SSH ausführen. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Benutzerdefinierte Bereitstellung von HDInsight unter Linux](../hdinsight-hadoop-provision-linux-clusters)
- [Arbeiten mit HDInsight unter Linux](../hdinsight-hadoop-linux-information)
- [Verwalten von HDInsight-Clustern mit Ambari](../hdinsight-hadoop-manage-ambari)
- [Verwenden von MapReduce mit HDInsight][hdinsight-use-mapreduce]
- [Verwenden von Hive mit HDInsight][hdinsight-use-hive]
- [Verwenden von Pig mit HDInsight][hdinsight-use-pig]
- [Verwenden von Azure Blob-Speicher mit HDInsight](../hdinsight-use-blob-storage)
- [Hochladen von Daten in HDInsight][hdinsight-upload-data]


[1]: ../hdinsight-hadoop-visual-studio-tools-get-started/

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-use-mapreduce]: ../hdinsight-use-mapreduce/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: ../install-configure-powershell/
[powershell-open]: ../install-configure-powershell/#Install

[img-hdi-dashboard]: ./media/hdinsight-get-started/HDI.dashboard.png
[img-hdi-dashboard-query-select]: ./media/hdinsight-get-started/HDI.dashboard.query.select.png
[img-hdi-dashboard-query-select-result]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.png
[img-hdi-dashboard-query-select-result-output]: ./media/hdinsight-get-started/HDI.dashboard.query.select.result.output.png
[img-hdi-dashboard-query-browse-output]: ./media/hdinsight-get-started/HDI.dashboard.query.browse.output.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
<!--HONumber=45--> 
