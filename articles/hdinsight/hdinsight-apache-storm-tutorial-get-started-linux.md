<properties
	pageTitle="Apache Storm-Tutorial: Erste Schritte mit Storm in HDInsight unter Linux | Microsoft Azure"
	description="Erste Schritte mit Big Data-Analysen in HDInsight unter Linux mit Apache Storm und den Storm-Starter-Beispielen. Erfahren Sie, wie Sie Storm zur Verarbeitung von Daten in Echtzeit verwenden."
	keywords="Apache Storm, Apache Storm-Tutorial, Big Data-Analysen, Storm Starter"
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="03/10/2016"
   ms.author="larryfr"/>


# Apache Storm-Lernprogramm: Erste Schritte mit Storm-Starter-Beispielen für die Big Data-Analyse in HDInsight

Apache Storm ist ein skalierbares, fehlertolerantes, verteiltes Echtzeit-Berechnungssystem für die Verarbeitung von Datenströmen. Mit Storm in Azure HDInsight können Sie einen cloudbasierten Storm-Cluster erstellen, der Big Data-Analysen in Echtzeit durchführt.

> [AZURE.NOTE] Mit den Schritten in diesem Artikel wird ein Linux-basierter HDInsight-Cluster erstellt. Schritte zum Erstellen eines Windows-basierten Storm in HDInsight-Clusters finden Sie unter [Apache Storm-Lernprogramm: Erste Schritte mit Storm-Starter-Beispielen für die Big Data-Analyse in HDInsight](hdinsight-apache-storm-tutorial-get-started.md).

## Voraussetzungen

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

Zur Ausführung dieses Lernprogramms zu Apache Storm benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Erfahrung mit SSH und SCP**. Weitere Informationen zur Verwendung von SSH und SCP mit HDInsight finden Sie in den folgenden Artikeln:

    - **Linux-, Unix- oder OS X-Clients**: Siehe [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, OS X oder Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

	- **Windows-Clients**: Siehe [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

##  Erstellen eines Storm-Clusters

In diesem Abschnitt erstellen Sie mithilfe einer Azure ARM-Vorlage einen HDInsight-Cluster der Version 3.2 (Storm-Version 0.9.3). Informationen zu den verschiedenen HDInsight-Versionen und ihren SLAs finden Sie unter [HDInsight-Komponentenversionen](hdinsight-component-versioning.md). Andere Methoden zur Erstellung von Clustern finden Sie unter [Erstellen von Linux-basierten Hadoop-Clustern in HDInsight](hdinsight-hadoop-provision-linux-clusters.md).

1. Klicken Sie auf die folgende Abbildung, um eine ARM-Vorlage im Azure-Portal zu öffnen.         

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fusesqoop%2Fcreate-linux-based-storm-cluster-in-hdinsight.json" target="_blank"><img src="https://acom.azurecomcdn.net/80C57D/cdn/mediahandler/docarticles/dpsmedia-prod/azure.microsoft.com/de-DE/documentation/articles/hdinsight-hbase-tutorial-get-started-linux/20160201111850/deploy-to-azure.png" alt="Deploy to Azure"></a>
    
    Die ARM-Vorlage befindet sich in einem öffentlichen Blobcontainer, **https://hditutorialdata.blob.core.windows.net/armtemplates/create-linux-based-storm-cluster-in-hdinsight.json*.
   
2. Geben Sie auf dem Blatt "Parameter" Folgendes ein:

    - **Clustername**: Geben Sie einen Namen für den Hadoop-Cluster ein, den Sie erstellen möchten.
    - **Cluster-Benutzername und -Kennwort**: Der Standardname für die Anmeldung lautet „admin“.
    - **SSH-Benutzername und -Kennwort**.
    
    Bitte notieren Sie diese Werte. Sie werden diese später im Lernprogramm benötigen.

    > [AZURE.NOTE] SSH wird verwendet, um remote über eine Befehlszeile auf den HDInsight-Cluster zuzugreifen. Der Benutzername und das Kennwort, die Sie hier verwenden, dienen zum Herstellen der Verbindung mit dem Cluster über SSH. Außerdem muss der SSH-Benutzername eindeutig sein, da mit ihm auf allen HDInsight-Clusterknoten ein Benutzerkonto erstellt wird. Die folgenden Namen sind einige der Namen, die für die Verwendung durch Dienste auf dem Cluster reserviert sind und daher nicht als SSH-Benutzername verwendet werden können:
    >
    > root, hdiuser, storm, hbase, ubuntu, zookeeper, hdfs, yarn, mapred, hbase, hive, oozie, falcon, sqoop, admin, tez, hcat, hdinsight-zookeeper.

	> Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie in den folgenden Artikeln:

	> * [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)
	> * [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

    
3\. Klicken Sie auf **OK**, um die Parameter zu speichern.

4\. Klicken Sie auf dem Blatt **Benutzerdefinierte Bereitstellung** auf das Dropdownfeld **Ressourcengruppe** und dann auf **Neu**, um eine neue Ressourcengruppe zu erstellen. Die Ressourcengruppe ist ein Container, in dem der Cluster, das abhängige Speicherkonto und andere verknüpfte Ressourcen gruppiert werden.

5\. Klicken Sie auf **Rechtliche Bedingungen** und dann auf **Erstellen**.

6\. Klicken Sie auf **Erstellen**. Daraufhin wird eine neue Kachel mit der Bezeichnung "Bereitstellung für Vorlagenbereitstellung wird gesendet" angezeigt. Das Erstellen des Clusters und der SQL-Datenbank dauert ca. 20 Minuten.


##Ausführen eines Storm Starter-Beispiels in HDInsight

Die [Storm-Starter](https://github.com/apache/storm/tree/master/examples/storm-starter)-Beispiele sind im HDInsight-Cluster enthalten. In den folgenden Schritten führen Sie das Beispiel "WordCount" aus.

1. Stellen Sie mithilfe von SSH eine Verbindung mit dem HDInsight-Cluster her:

		ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
		
	Wenn Sie zum Schutz Ihres SSH-Benutzerkontos ein Kennwort verwendet haben, werden Sie zur Eingabe dieses Kennworts aufgefordert. Wenn Sie einen öffentlichen Schlüssel verwendet haben, müssen Sie möglicherweise den Parameter `-i` verwenden, um den passenden privaten Schlüssel anzugeben. Beispiel: `ssh -i ~/.ssh/id_rsa USERNAME@CLUSTERNAME-ssh.azurehdinsight.net`.
		
	Weitere Informationen zum Verwenden von SSH mit Linux-basierten HDInsight-Clustern finden Sie in den folgenden Artikeln:
	
	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows)

2. Verwenden Sie zum Starten einer Beispieltopologie den folgenden Befehl.

        storm jar /usr/hdp/current/storm-client/contrib/storm-starter/storm-starter-topologies-0.9.3.2.2.4.9-1.jar storm.starter.WordCountTopology wordcount
		
	> [AZURE.NOTE] `0.9.3.2.2.4.9-1` im Dateinamen kann auch anders lauten, wenn HDinsight mit neueren Versionen von Storm aktualisiert wird.

    Hierdurch wird die "WordCount"-Beispieltopologie im Cluster mit dem Anzeigenamen "Wordcount" gestartet. Nach dem Zufallsprinzip werden Sätze generiert und die Instanzen jedes Worts in den Sätzen gezählt.

    > [AZURE.NOTE] Wenn die Topologie an den Cluster gesendet wird, müssen Sie zuerst die JAR-Datei mit dem Cluster kopieren, bevor Sie den Befehl `storm` verwenden. Hierzu können Sie den Befehl `scp` auf dem Client mit der Datei verwenden. Beispiel: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > Das Beispiel "WordCount" und andere Storm-Starter-Beispiele sind unter `/usr/hdp/current/storm-client/contrib/storm-starter/` bereits auf dem Cluster enthalten.

##Überwachen der Topologie

Die Storm-Benutzeroberfläche bietet eine Weboberfläche zum Arbeiten mit ausgeführten Topologien und befindet sich auf dem HDInsight-Cluster.

Führen Sie die folgenden Schritte aus, um die Topologie mithilfe der Storm-Benutzeroberfläche zu überwachen:

1. Öffnen Sie in einem Webbrowser die Seite https://CLUSTERNAME.azurehdinsight.net/stormui, wobei __CLUSTERNAME__ der Name Ihres Clusters ist. Dadurch wird die Storm-Benutzeroberfläche geöffnet.

	> [AZURE.NOTE] Wenn Sie dazu aufgefordert werden, einen Benutzernamen und ein Kennwort anzugeben, geben Sie den Namen des Cluster-Administrators (Admin) und das entsprechende Kennwort ein, die Sie beim Erstellen des Clusters verwendet haben.

2. Klicken Sie unter **Topologiezusammenfassung** in der Spalte **Name** auf den Eintrag **wordcount**. Dadurch werden weitere Informationen zur Topologie angezeigt.

	![Storm-Dashboard mit Informationen zur Storm-Starter-WordCount-Topologie.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)

	Auf dieser Seite werden die folgenden Informationen angezeigt:

	* **Topologiestatistik** – Grundlegende Informationen zur Leistung der Topologie, aufgeteilt in Zeitfenster.

		> [AZURE.NOTE] Durch die Auswahl eines bestimmten Zeitfensters werden die Informationen in den anderen Abschnitten der Seite entsprechend angepasst.

	* **Spouts** – Grundlegende Informationen zu Spouts, einschließlich des letzten von einem Spout zurückgegebenen Fehlers.

	* **Bolts** – Grundlegende Informationen zu Bolts.

	* **Topologiekonfiguration** – Ausführliche Informationen zur Konfiguration der Topologie.

	Außerdem gibt diese Seite die für die Topologie ausführbaren Aktionen an:

	* **Aktivieren** – Setzt die Verarbeitung einer deaktivierten Topologie fort.

	* **Deaktivieren** – Hält eine aktive Topologie an.

	* **Ausgleichen** – Passt die Parallelität der Topologie an. Sie sollten aktive Topologien ausgleichen, nachdem Sie die Anzahl der Knoten im Cluster geändert haben. Dadurch kann die Topologie die Parallelität anpassen, um die höhere/geringere Anzahl der Knoten im Cluster zu kompensieren. Weitere Informationen finden Sie unter [Grundlegendes zur Parallelität einer Storm-Topologie](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

	* **Beenden** – Beendet eine Storm-Topologie nach dem angegebenen Zeitlimit.

3. Wählen Sie auf dieser Seite einen Eintrag aus dem Abschnitt **Spouts** oder **Bolts** aus. Dadurch werden Informationen zur ausgewählten Komponente angezeigt.

	![Storm-Dashboard mit Informationen zu ausgewählten Komponenten.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)

	Auf dieser Seite werden die folgenden Informationen angezeigt:

	* **Statistik für Spout/Bolt** – Grundlegende Informationen zur Leistung der Komponente, aufgeteilt in Zeitfenster.

		> [AZURE.NOTE] Durch die Auswahl eines bestimmten Zeitfensters werden die Informationen in den anderen Abschnitten der Seite entsprechend angepasst.

	* **Eingabestatistik** (nur Bolt) – Informationen zu Komponenten, die vom Bold konsumierte Daten generieren.

	* **Ausgabestatistik** – Informationen zu den von diesem Bold ausgegebenen Daten.

	* **Executors** – Informationen zu Instanzen dieser Komponente.

	* **Fehler** – Von dieser Komponente generierte Fehler.

4. Zum Anzeigen der Spout- oder Bolt-Details wählen Sie im Abschnitt **Executors** einen Eintrag aus der Spalte **Port** aus, um die Details einer bestimmten Instanz der Komponente anzuzeigen.

		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

	In diesem Beispiel entnehmen Sie der Spalte, dass das Wort **seven** 1493957mal vorkam. So oft wurde dieses Wort seit dem Start der Topologie erkannt.

##Beenden der Topologie

Kehren Sie zur **Topologiezusammenfassung** der WordCount-Topologie zurück, und klicken Sie im Abschnitt mit den **Topologieaktionen** auf die Schaltfläche **Beenden**. Geben Sie auf Aufforderung für die Verzögerung vor dem Beenden der Topologie 10 (Sekunden) ein. Nach dieser Wartezeit wird die Topologie im Abschnitt **Storm-UI** des Dashboards nicht mehr angezeigt.

##Löschen des Clusters

[AZURE.INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

##Zusammenfassung

In diesem Lernprogramm zu Apache Storm lernen Sie anhand der Storm-Starter-Beispiele, wie ein Storm-Cluster in HDInsight erstellt wird und wie Storm-Topologien im Storm-Dashboard bereitgestellt, überwacht und verwaltet werden.

##<a id="next"></a>Nächste Schritte

* Das folgende Dokument enthält eine Liste weiterer Beispiele, die mit Storm in HDInsight verwendet werden können:

	* [Beispiele für Storm-Topologien für Storm in HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md
[preview-portal]: https://portal.azure.com/

<!---HONumber=AcomDC_0316_2016-->