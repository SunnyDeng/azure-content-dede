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
   ms.date="10/26/2015"
   ms.author="larryfr"/>


# Apache Storm-Lernprogramm: Erste Schritte mit Storm-Starter-Beispielen für die Big Data-Analyse in HDInsight

Apache Storm ist ein skalierbares, fehlertolerantes, verteiltes Echtzeit-Berechnungssystem für die Verarbeitung von Datenströmen. Mit Storm in Azure HDInsight können Sie einen cloudbasierten Storm-Cluster erstellen, der Big Data-Analysen in Echtzeit durchführt.

> [AZURE.NOTE]Mit den Schritten in diesem Artikel wird ein Linux-basierter HDInsight-Cluster erstellt. Schritte zum Erstellen eines Windows-basierten Storm in HDInsight-Clusters finden Sie unter [Apache Storm-Lernprogramm: Erste Schritte mit Storm-Starter-Beispielen für die Big Data-Analyse in HDInsight](hdinsight-apache-storm-tutorial-get-started.md).

## Voraussetzungen

Zur Ausführung dieses Lernprogramms zu Apache Storm benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Erfahrung mit SSH und SCP**. Weitere Informationen zur Verwendung von SSH und SCP mit HDInsight finden Sie in den folgenden Artikeln:

    - **Linux-, Unix- oder OS X-Clients**: Siehe [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, OS X oder Unix](hdinsight-hadoop-linux-use-ssh-unix.md)

	- **Windows-Clients**: Siehe [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

##  Erstellen eines Storm-Clusters

Storm in HDInsight verwendet Azure-Blobspeicher zum Speichern der an den Cluster übergebenen Protokolldateien und Topologien. Führen Sie zur Erstellung eines Azure-Speicherkontos für Ihren Cluster die folgenden Schritte aus:

1. Melden Sie sich beim [Azure-Portal][preview-portal] an.

2. Wählen Sie **NEU**, __Datenanalyse__ und anschließend __HDInsight__ aus.

	![Erstellen eines neuen Clusters im Azure-Portal](./media/hdinsight-apache-storm-tutorial-get-started-linux/new-cluster.png)

3. Geben Sie einen __Clusternamen__ ein, und wählen Sie dann __Storm__ als __Clustertyp__ aus. Wenn der __Clustername__ verfügbar ist, wird neben dem Namen ein grünes Häkchen angezeigt.

	![Clustername, Clustertyp und Betriebssystemtyp](./media/hdinsight-apache-storm-tutorial-get-started-linux/clustername.png)

	Wählen Sie __Ubuntu__ aus, um einen Linux-basierten HDInsight-Cluster zu erstellen.
	
4. Falls Sie mehrere Abonnements besitzen, wählen Sie den Eintrag __Abonnement__ aus, um das Azure-Abonnement für den Cluster auszuwählen.

5. Wählen Sie den Eintrag __Ressourcengruppe__ aus, um eine Liste der vorhandenen Ressourcengruppen anzuzeigen, und wählen Sie dann die Gruppe aus, in der der Cluster erstellt werden soll. Alternativ können Sie eine neue Ressourcengruppe erstellen, indem Sie auf __Neu erstellen__ klicken und den Namen der neuen Gruppe eingeben. Wenn der neue Gruppenname verfügbar ist, wird neben dem Namen ein grünes Häkchen angezeigt.

	> [AZURE.NOTE]Dieser Eintrag ist standardmäßig auf eine Ihrer vorhandenen Ressourcengruppen festgelegt (sofern verfügbar).

6. Wählen Sie __Anmeldeinformationen__ aus, und geben Sie dann ein __Clusteranmeldekennwort__ für den __Clusterbenutzernamen__ ein. Außerdem müssen Sie einen __SSH-Benutzernamen__ und entweder ein __Kennwort__ oder einen __öffentlichen Schlüssel__ zum Authentifizieren des SSH-Benutzers eingeben. Klicken Sie abschließend auf die Schaltfläche __Auswählen__, um die Anmeldeinformationen festzulegen.

	![Blatt „Clusteranmeldeinformationen“](./media/hdinsight-administer-use-portal-linux/clustercredentials.png)

	Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie in den folgenden Artikeln:

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

	* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows)

6. Wählen Sie den Eintrag __Datenquelle__ aus, um eine vorhandene Datenquelle auszuwählen, oder erstellen Sie eine neue Datenquelle.

	![Blatt „Datenquelle“](./media/hdinsight-apache-storm-tutorial-get-started-linux/datasource.png)
	
	Zurzeit kann ein Azure-Speicherkonto als Datenquelle für einen HDInsight-Cluster ausgewählt werden. Die Einträge auf dem Blatt __Datenquelle__ werden im Folgenden erläutert.
	
	- __Auswahlmethode__: Wählen Sie __Aus allen Abonnements__ aus, damit die Speicherkonten in Ihren Abonnements durchsucht werden können. Wählen Sie __Zugriffsschlüssel__ aus, wenn Sie den __Speichernamen__ und __Zugriffsschlüssel__ eines vorhandenen Speicherkontos eingeben möchten.
	
	- __Neu erstellen__: Hiermit können Sie ein neues Speicherkonto erstellen. Geben Sie den Namen des Speicherkontos in das angezeigte Feld ein. Wenn der Name verfügbar ist, wird ein grünes Häkchen angezeigt.
	
	- __Standardcontainer auswählen__: Geben Sie den Namen des Standardcontainers für den Cluster ein. Sie können einen beliebigen Namen eingeben, es wird jedoch empfohlen, den Namen des Clusters zu verwenden. So können Sie leichter erkennen, dass es sich um den Container für diesen spezifischen Cluster handelt.
	
	- __Standort__: Die geografische Region, in der sich das Speicherkonto befindet bzw. in der es erstellt wird.
	
		> [AZURE.IMPORTANT]Durch die Auswahl des Standorts für die Standarddatenquelle wird auch der Standort des HDInsight-Clusters festgelegt. Der Cluster und die Standarddatenquelle müssen sich in der gleichen Region befinden.
		
	- __Auswählen__: Hiermit speichern Sie die Datenquellenkonfiguration.
	
7. Wählen Sie __Knotenpreistarife__ aus, um Informationen zu den Knoten anzuzeigen, die für diesen Cluster erstellt werden. Die Anzahl von Workerknoten ist standardmäßig auf __4__ festgelegt. Die vorkalkulierten Kosten für den Cluster werden unten auf dem Blatt angezeigt.

	![Blatt „Knotenpreistarife“](./media/hdinsight-apache-storm-tutorial-get-started-linux/nodepricingtiers.png)
	
	Klicken Sie auf die Schaltfläche __Auswählen__, um die Informationen zu den __Knotenpreistarifen__ zu speichern.

8. Wählen Sie __Optionale Konfiguration__ aus. Auf diesem Blatt können Sie die Clusterversion auswählen und andere optionale Einstellungen konfigurieren. Sie können den Cluster z. B. einem __virtuellen Netzwerk__ hinzufügen oder einen __benutzerdefinierten Metastore__ zum Speichern von Daten für Hive und Oozie einrichten.

	![Blatt „Optionale Konfiguration“](./media/hdinsight-apache-storm-tutorial-get-started-linux/optionalconfiguration.png)

9. Stellen Sie sicher, dass __An Startmenü anheften__ ausgewählt ist, und klicken Sie dann auf __Erstellen__. Der Cluster wird erstellt, und dem Startmenü Ihres Azure-Portals wird eine Kachel für den Cluster hinzugefügt. Das Symbol zeigt an, dass der Cluster bereitgestellt wird. Sobald die Bereitstellung abgeschlossen ist, ändert es sich in das HDInsight-Symbol.

	| Während der Bereitstellung | Nach Abschluss der Bereitstellung |
	| ------------------ | --------------------- |
	| ![Bereitstellungsanzeige im Startmenü](./media/hdinsight-apache-storm-tutorial-get-started-linux/provisioning.png) | ![Kachel für einen bereitgestellten Cluster](./media/hdinsight-apache-storm-tutorial-get-started-linux/provisioned.png) |

	> [AZURE.NOTE]Die Erstellung des Clusters dauert in der Regel ca. 15 Minuten. Sie können den Status des Bereitstellungsprozesses auf der Kachel im Startmenü oder im linken Bereich der Seite unter __Benachrichtigungen__ überprüfen.

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
		
	> [AZURE.NOTE]`0.9.3.2.2.4.9-1` im Dateinamen kann auch anders lauten, wenn HDinsight mit neueren Versionen von Storm aktualisiert wird.

    Hierdurch wird die "WordCount"-Beispieltopologie im Cluster mit dem Anzeigenamen "Wordcount" gestartet. Nach dem Zufallsprinzip werden Sätze generiert und die Instanzen jedes Worts in den Sätzen gezählt.

    > [AZURE.NOTE]Wenn die Topologie an den Cluster gesendet wird, müssen Sie zuerst die JAR-Datei mit dem Cluster kopieren, bevor Sie den Befehl `storm` verwenden. Hierzu können Sie den Befehl `scp` auf dem Client mit der Datei verwenden. Beispiel: `scp FILENAME.jar USERNAME@CLUSTERNAME-ssh.azurehdinsight.net:FILENAME.jar`
    >
    > Das Beispiel "WordCount" und andere Storm-Starter-Beispiele sind unter `/usr/hdp/current/storm-client/contrib/storm-starter/` bereits auf dem Cluster enthalten.

##Überwachen der Topologie

Die Storm-Benutzeroberfläche bietet eine Weboberfläche zum Arbeiten mit ausgeführten Topologien und befindet sich auf dem HDInsight-Cluster.

> [AZURE.IMPORTANT]Der Storm-Benutzeroberfläche ist nicht öffentlich über das Internet verfügbar. Der Zugriff muss über einen SSH-Tunnel zum Hauptknoten des HDInsight-Clusters erfolgen. Weitere Informationen finden Sie unter [Verwenden von SSH-Tunneling zum Zugriff auf die Ambari-Webbenutzeroberfläche, ResourceManager, JobHistory, NameNode, Oozie und andere Webbenutzeroberflächen](hdinsight-linux-ambari-ssh-tunnel.md).

Führen Sie die folgenden Schritte aus, um die Storm-Benutzeroberfläche anzuzeigen:

1. Wenn Sie einen SSH-Tunnel zu dem Cluster erstellt haben, öffnen Sie https://CLUSTERNAME.azurehdinsight.net in einem Webbrowser, wobei __CLUSTERNAME__ für den Namen Ihres Clusters steht. Die Ambari-Webbenutzeroberfläche wird geöffnet.

	> [AZURE.NOTE]Wenn Sie dazu aufgefordert werden, einen Benutzernamen und ein Kennwort anzugeben, geben Sie den Namen des Cluster-Administrators (Admin) und das entsprechende Kennwort ein, die Sie beim Erstellen des Clusters verwendet haben. Möglicherweise werden Sie zwei Mal zur Authentifizierung aufgefordert, einmal vom Browser und ein zweites Mal von der Ambari-Webbenutzeroberfläche. Geben Sie in beiden Fällen die gleichen Anmeldeinformationen ein.

2. Wählen Sie aus der Liste der Dienste auf der linken Seite den Dienst __Storm__ aus. Wählen Sie dann unter __QuickLinks__ den Eintrag __Storm-UI__ aus.

    ![Eintrag der Storm-Benutzeroberfläche in den Quicklinks](./media/hdinsight-apache-storm-tutorial-get-started-linux/ambari-storm.png)

    Damit wird die Storm-Benutzeroberfläche geöffnet:

    ![Storm-Benutzeroberfläche](./media/hdinsight-apache-storm-tutorial-get-started-linux/stormui.png)
	
	> [AZURE.NOTE]Wenn ein Fehler angezeigt wird, dass der Server nicht gefunden werden kann, haben Sie möglicherweise keinen SSH-Tunnel zum Cluster eingerichtet. Weitere Informationen finden Sie unter [Verwenden von SSH-Tunneling zum Zugriff auf die Ambari-Webbenutzeroberfläche, ResourceManager, JobHistory, NameNode, Oozie und andere Webbenutzeroberflächen](hdinsight-linux-ambari-ssh-tunnel.md).

4. Klicken Sie unter **Topologiezusammenfassung** in der Spalte **Name** auf den Eintrag **wordcount**. Dadurch werden weitere Informationen zur Topologie angezeigt.

	![Storm-Dashboard mit Informationen zur Storm-Starter-WordCount-Topologie.](./media/hdinsight-apache-storm-tutorial-get-started-linux/topology-summary.png)

	Auf dieser Seite werden die folgenden Informationen angezeigt:

	* **Topologiestatistik** – Grundlegende Informationen zur Leistung der Topologie, aufgeteilt in Zeitfenster.

		> [AZURE.NOTE]Durch die Auswahl eines bestimmten Zeitfensters werden die Informationen in den anderen Abschnitten der Seite entsprechend angepasst.

	* **Spouts** – Grundlegende Informationen zu Spouts, einschließlich des letzten von einem Spout zurückgegebenen Fehlers.

	* **Bolts** – Grundlegende Informationen zu Bolts.

	* **Topologiekonfiguration** – Ausführliche Informationen zur Konfiguration der Topologie.

	Außerdem gibt diese Seite die für die Topologie ausführbaren Aktionen an:

	* **Aktivieren** – Setzt die Verarbeitung einer deaktivierten Topologie fort.

	* **Deaktivieren** – Hält eine aktive Topologie an.

	* **Ausgleichen** – Passt die Parallelität der Topologie an. Sie sollten aktive Topologien ausgleichen, nachdem Sie die Anzahl der Knoten im Cluster geändert haben. Dadurch kann die Topologie die Parallelität anpassen, um die höhere/geringere Anzahl der Knoten im Cluster zu kompensieren. Weitere Informationen finden Sie unter [Grundlegendes zur Parallelität einer Storm-Topologie](http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html).

	* **Beenden** – Beendet eine Storm-Topologie nach dem angegebenen Zeitlimit.

5. Wählen Sie auf dieser Seite einen Eintrag aus dem Abschnitt **Spouts** oder **Bolts** aus. Dadurch werden Informationen zur ausgewählten Komponente angezeigt.

	![Storm-Dashboard mit Informationen zu ausgewählten Komponenten.](./media/hdinsight-apache-storm-tutorial-get-started-linux/component-summary.png)

	Auf dieser Seite werden die folgenden Informationen angezeigt:

	* **Statistik für Spout/Bolt** – Grundlegende Informationen zur Leistung der Komponente, aufgeteilt in Zeitfenster.

		> [AZURE.NOTE]Durch die Auswahl eines bestimmten Zeitfensters werden die Informationen in den anderen Abschnitten der Seite entsprechend angepasst.

	* **Eingabestatistik** (nur Bolt) – Informationen zu Komponenten, die vom Bold konsumierte Daten generieren.

	* **Ausgabestatistik** – Informationen zu den von diesem Bold ausgegebenen Daten.

	* **Executors** – Informationen zu Instanzen dieser Komponente.

	* **Fehler** – Von dieser Komponente generierte Fehler.

5. Zum Anzeigen der Spout- oder Bolt-Details wählen Sie im Abschnitt **Executors** einen Eintrag aus der Spalte **Port** aus, um die Details einer bestimmten Instanz der Komponente anzuzeigen.

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

<!---HONumber=AcomDC_1203_2015-->