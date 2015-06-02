<properties
	pageTitle="Apache Storm-Lernprogramm: Storm Starter-Beispiele | Microsoft Azure"
	description="Erste Schritte mit der Analyse großer Datenmengen mit Apache-Storm durch die Ausführung von Storm Starter-Beispielen in HDInsight Erfahren Sie, wie Sie Apache Storm zum Verarbeiten von Daten in Echtzeit verwenden."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="java"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="04/28/2015"
   ms.author="larryfr"/>


# Apache-Storm-Lernprogramm in HDInsight: Erste Schritte mit der Analyse großer Datenmengen mit Storm Starter-Beispielen 

Apache Storm ist ein skalierbares, fehlertolerantes, verteiltes Echtzeit-Berechnungssystem für die Verarbeitung von Datenströmen. Mit Storm in Azure HDInsight können Sie ein cloudbasiertes Storm-Cluster für die Echtzeitanalyse großer Datenmengen erstellen.

##Voraussetzungen

Sie benötigen Folgendes, um dieses Apache Storm-Lernprogramm abzuschließen.

* Ein Azure-Abonnement

##Erstellen eines Azure-Speicherkontos

Storm in HDInsight verwendet Azure-Blob-Speicher zum Speichern von Protokolldateien und Topologien, die an den Cluster übermittelt wurden. Führen Sie die folgenden Schritte zum Erstellen eines Speicherkontos für die Verwendung mit Ihrem Cluster aus:

1. Melden Sie sich beim [Azure-Portal](http://manage.windowsazure.com/) an.

2. Klicken Sie links unten auf **NEU**, zeigen Sie auf **DATA SERVICES**, auf **STORAGE**, und klicken Sie anschließend auf **SCHNELLERFASSUNG**.

	![Azure-Portal, in dem Sie die Schnellerfassungsfunktion nutzen können, um ein neues Speicherkonto einzurichten.](./media/hdinsight-storm-getting-started/HDI.StorageAccount.QuickCreate.png)

3. Geben Sie **URL**, **SPEICHERORT** und **REPLIKATION** ein, und klicken Sie dann auf **SPEICHERKONTO ERSTELLEN**. Wählen Sie keine Affinitätsgruppe bei der Erstellung des Speichers für HDInsight. Das neue Speicherkonto wird dann in der Speicherliste angezeigt.

	>[AZURE.NOTE]Die Schnellerfassungsoption zur Bereitstellung eines HDInsight-Clusters wie die in diesem Lernprogramm verwendete fordert beim Bereitstellen des Clusters nicht zur Angabe eines Standorts auf. Stattdessen wird der Cluster standardmäßig in demselben Rechenzentrum wie das Speicherkonto bereitgestellt. Stellen Sie daher sicher, dass Sie das Speicherkonto an Standorten erstellen, die den Cluster unterstützen: **Ostasien**, **Südostasien**, **Nordeuropa**, **Westeuropa**, **USA (Osten)**, **USA (Westen)**, **USA (Mitte/Norden)**, **USA (Süden-Mitte)**.

4. Warten Sie, bis sich der **STATUS** des neuen Speicherkontos in **Online** geändert hat.

Weitere Informationen zum Erstellen von Speicherkonten finden Sie unter <a href="../storage-create-storage-account/" target="_blank">Erstellen eines Speicherkontos</a>.

##Bereitstellen eines Storm-Clusters im Azure-Portal

Beim Bereitstellen eines HDInsight-Clusters werden Azure-Serverressourcen bereitgestellt, die Apache Storm und verwandte Anwendungen enthalten. Mit dem Azure-Portal, Azure PowerShell-Cmdlets für HDInsight oder dem HDInsight .NET SDK können Sie auch Hadoop-Cluster für andere Versionen erstellen. Anweisungen finden Sie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight][hdinsight-provision]. Informationen zu den verschiedenen HDInsight-Versionen und ihren Vereinbarungen zum Servicelevel (SLAs) finden Sie auf der Seite [HDInsight-Komponentenversionen](hdinsight-component-versioning.md).

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

1. Melden Sie sich beim [Azure-Portal][azureportal] an.

2. Klicken Sie links auf **HDInsight** und anschließend unten links auf der Seite auf **+NEU**.

3. Klicken Sie auf das HDInsight-Symbol in der zweiten Spalte, und wählen Sie **STORM** aus.

	![Schnellerfassung](./media/hdinsight-storm-getting-started/quickcreate.png)

4. Geben Sie einen eindeutigen Clusternamen und ein eindeutiges Kennwort für das Administratorkonto ein. Wählen Sie für **SPEICHERKONTO** das zuvor erstellte Speicherkonto aus.

	Für **CLUSTERGRÖSSE** wählen Sie eine Größe von **1 Datenknoten** für diesen Cluster. Dies dient der Reduzierung der mit dem Cluster verbundenen Kosten. Für die Verwendung in einer Produktionsumgebung würden Sie einen größeren Cluster erstellen.

	> [AZURE.NOTE]Das Administratorkonto für den Cluster hat den Namen **admin**. Das eingegebene Kennwort ist das Kennwort für dieses Konto. Sie benötigen diese Informationen zum Ausführen von Aktionen mit dem Cluster, z. B. Senden oder Verwalten von Storm-Topologien.

5. Aktivieren Sie abschließend das Häkchen neben **HDINSIGHT-CLUSTER ERSTELLEN** zum Erstellen des Clusters.

> [AZURE.NOTE]Die Clusterbereitstellung nimmt einige Zeit in Anspruch. Normalerweise dauert es jedoch weniger als 15 Minuten, den Cluster zu erstellen, die Software zu konfigurieren und Beispieldaten und Topologien zu installieren.

##Ausführen einer Storm Starter-Stichprobe in HDInsight

In diesem Apache Storm-Lernprogramm wird die Analyse großer Datenmengen mit Storm Starter-Beispielen von GitHub vorgestellt.

Jedes Storm in HDInsight-Cluster verfügt über das Storm-Dashboard zum Hochladen und Ausführen von Storm-Topologien auf dem Cluster. Jeder Cluster verfügt außerdem über Beispieltopologien, die direkt über das Storm-Dashboard ausgeführt werden können.

###<a id="connect"></a>Verbinden mit dem Dashboard

Das Dashboard befindet sich unter **https://&lt;clustername>.azurehdinsight.net//**, wobei **clustername** der Name des Clusters ist. Sie finden auch einen Link zum Dashboard am unteren Rand der Azure-Portalseite für den Cluster.

![Azure-Portal mit Storm-Dashboard-Link](./media/hdinsight-storm-getting-started/dashboard-link.png)

> [AZURE.NOTE]Bei der Verbindung mit dem Dashboard werden Sie aufgefordert, einen Benutzernamen und ein Kennwort einzugeben. Dies ist der Name des Administrators (**admin**) und das Kennwort, das Sie bei der Erstellung des Clusters verwendet haben.

Sobald das Storm-Dashboard geladen wurde, wird das Formular **Topologie senden** angezeigt.

![Storm-Dashboard mit Formular "Topologie senden"](./media/hdinsight-storm-getting-started/submit.png)

Mit dem Formular **Topologie senden** können Sie JAR-Dateien mit Storm-Topologien hochladen und ausführen. Darüber hinaus enthält es einige grundlegende Beispiele, die mit dem Cluster bereitgestellt werden.

###<a id="run"></a>Ausführen des Wortzählungs-Beispiels im Storm Starter-Projekt in GitHub

Die Beispiele im Cluster umfassen mehrere Varianten einer Wortzählungs-Topologie. Diese Beispiele enthalten einen **Spout**, der nach dem Zufallsprinzip Sätze ausgibt, und **Bolts**, die jeden Satz in einzelne Wörter aufteilen und dann zählen, wie oft jedes Wort aufgetreten ist. Diese Beispiele stammen aus den <a href="https://github.com/apache/storm/tree/master/examples/storm-starter" target="_blank">Storm Starter-Beispielen</a>, die Bestandteil von Apache Storm sind.

Führen Sie die folgenden Schritte aus, um ein Storm Starter-Beispiel auszuführen:

1. Wählen Sie **StormStarter - WordCount** aus der Dropdownliste **Jar File** aus. Dadurch sollten der **Klassenname** und **zusätzliche Parameter**-Felder mit den Parametern für dieses Beispiel ausgefüllt werden.

	![Storm-Dashboard mit ausgewähltem "WordCount"](./media/hdinsight-storm-getting-started/submit.png)

	* **Klassenname:** die Klasse in der JAR-Datei, die die Topologie übermittelt.
	* **Zusätzliche Parameter:** die von der Topologie benötigten Parameter. In diesem Beispiel wird das Feld verwendet, um einen Anzeigenamen für die übermittelte Topologie bereitzustellen.

2. Klicken Sie auf die Schaltfläche **Senden**. Nach kurzer Zeit werden im Feld **Ergebnis** der Befehl, der zum Senden des Auftrags verwendet wurde, sowie die Ergebnisse des Befehls angezeigt. Im Feld **Fehler** werden alle bei der Übermittlung der Topologie auftretenden Fehler angezeigt.

	![Schaltfläche "Senden" und Ergebnisse](./media/hdinsight-storm-getting-started/submit-results.png)

	> [AZURE.NOTE]Die Ergebnisse zeigen nicht an, dass die Topologie abgeschlossen wurde – **eine Storm-Topologie wird nach ihrem Start so lange ausgeführt, bis sie beendet wird.** Die Topologie zum Zählen der Wörter generiert zufällige Sätze und zählt, wie oft jedes Wort vorkommt, bis Sie die Topologie beenden.

###<a id="monitor"></a>Überwachen der Topologie

Die Storm-Benutzeroberfläche kann zur Überwachung der Topologie verwendet werden.

1. Wählen Sie oben im Storm-Dashboard die Option **Storm-Benutzeroberfläche** aus. Dadurch werden zusammenfassende Informationen zum Cluster und allen aktiven Topologien angezeigt.

	![Storm-Benutzeroberfläche mit Topologiezusammenfassung](./media/hdinsight-storm-getting-started/stormui.png)

	Auf der Seite oben sehen Sie, dass die Topologie aktiv war, und es wird die Anzahl der Worker, Ausführer sowie der Aufgaben angezeigt.

	> [AZURE.NOTE]Die Spalte **Name** enthält den Anzeigenamen, der zuvor über das Feld **Zusätzliche Parameter** angegeben wurde.

4. Klicken Sie unter **Topologie-Zusammenfassung** in der Spalte **Name** auf den Eintrag **wordcount**. Dadurch werden weitere Informationen zur Topologie angezeigt.

	![Storm UI](./media/hdinsight-storm-getting-started/topology-summary.png)

	Diese Seite enthält die folgenden Informationen:

	* **Topologiestatistik:** grundlegende Informationen zur Leistung der Topologie in Zeitfenstern organisiert.

		> [AZURE.NOTE]Wenn Sie ein bestimmtes Zeitfenster auswählen, wird das Zeitfenster für die in anderen Bereichen der Seite angezeigten Informationen geändert.

	* **Spouts:** grundlegende Informationen zu Spouts, einschließlich des letzten Fehlers, der von den einzelnen Spouts zurückgegeben wurde.

	* **Bolts:** grundlegende Informationen zu Bolts.

	* **Topologiekonfiguration:** ausführliche Informationen zur Topologiekonfiguration.

	Diese Seite enthält auch Aktionen, die auf der Topologie ausgeführt werden können:

	* **Aktivieren:** setzt die Verarbeitung einer deaktivierten Topologie fort.

	* **Deaktivieren:** hält eine aktive Topologie an.

	* **Ausgleichen:** passt die Parallelität der Topologie an. Sie sollten aktive Topologien ausgleichen, nachdem Sie die Anzahl der Knoten im Cluster geändert haben. Dadurch kann die Topologie die Parallelität anpassen, um die höhere/geringere Anzahl der Knoten im Cluster zu kompensieren. Weitere Informationen finden Sie unter <a href="http://storm.apache.org/documentation/Understanding-the-parallelism-of-a-Storm-topology.html" target="_blank">Understanding the parallelism of a Storm topology</a> (in englischer Sprache).

	* **Beenden:** beendet eine Storm-Topologie nach dem angegebenen Timeout.

5. Wählen Sie auf dieser Seite einen Eintrag aus dem Abschnitt **Spouts** bzw. **Bolts** aus. Damit werden Informationen über die gewählte Komponente angezeigt.

	![Storm UI](./media/hdinsight-storm-getting-started/component-summary.png)

	Auf der Seite werden die folgenden Informationen angezeigt:

	* **Statistik für Spout/Bolt:** grundlegende Informationen zur Leistung der Komponente in Zeitfenstern organisiert.

		> [AZURE.NOTE]Wenn Sie ein bestimmtes Zeitfenster auswählen, wird das Zeitfenster für die in anderen Bereichen der Seite angezeigten Informationen geändert.

	* **Eingabestatistik** (nur Bolt): Informationen zu Komponenten, die vom Bolt genutzte Daten erzeugen.

	* **Ausgabestatistik:** Informationen zu den Daten, die von diesem Bolt ausgegeben werden.

	* **Ausführer:** Informationen zu Instanzen dieser Komponente.

	* **Fehler:** Fehler, die von dieser Komponente erzeugt wurden.

5. Wenn Sie Details eines Spouts oder Bolts anzeigen, wählen Sie im Abschnitt **Ausführer** einen Eintrag aus der Spalte **Port**, um ausführliche Informationen zu einer bestimmten Instanz der Komponente anzuzeigen.

		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["with"]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: split default ["nature"]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [snow]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [snow, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [white]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [white, 747293]
		2015-01-27 14:18:02 b.s.d.executor [INFO] Processing received message source: split:21, stream: default, id: {}, [seven]
		2015-01-27 14:18:02 b.s.d.task [INFO] Emitting: count default [seven, 1493957]

	Diesen Daten können Sie entnehmen, dass das Wort **seven** 1.493.957-mal vorgekommen ist. Dies ist die Anzahl der gezählten Vorkommen seit dem Start der Topologie.

###Beenden der Topologie

Wechseln Sie zurück zur Seite **Topologiezusammenfassung** der Wortzählungs-Topologie, und wählen Sie dann im Abschnitt **Topologieaktionen** die Schaltfläche **Beenden** aus. Geben Sie bei entsprechender Aufforderung "10" als Wert für die Sekunden vor dem Beenden der Topologie ein. Nach dem Timeout für die Topologie wird diese nicht mehr angezeigt, wenn Sie den Abschnitt **Storm-Benutzeroberfläche** des Dashboards aufrufen.

##Zusammenfassung

In diesem Apache Storm-Lernprogramm haben Sie erfahren, wie Sie Storm Starter verwenden, um einen Storm in HDInsight-Cluster zu erstellen, und mit dem Storm-Dashboard Storm-Topologien bereitstellen, überwachen und verwalten.

##<a id="next"></a>Nächste Schritte

* **HDInsight-Tools für Visual Studio:** Mit den HDInsight-Tools können Sie auf ähnliche Weise wie mit dem zuvor erwähnten Storm-Dashboard in Visual Studio Storm-Topologien übermitteln, überwachen und verwalten. HDInsight-Tools bieten außerdem die Möglichkeit zum Erstellen von C#-Storm-Topologien und enthalten Beispieltopologien, die Sie auf dem Cluster ausführen und bereitstellen können.

	Weitere Informationen finden Sie unter <a href="../hdinsight-hadoop-visual-studio-tools-get-started/" target="_blank">Erste Schritte mit den HDInsight-Tools für Visual Studio</a>.

* **Beispieldateien:** Der HDInsight Storm-Cluster enthält verschiedene Beispiele im Verzeichnis **%STORM_HOME%\\contrib**. Jedes Beispiel enthält Folgendes:

	* Quellcode - z. B. storm-starter-0.9.1.2.1.5.0-2057-sources.jar

	* Javadocs - z. B. storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar

	* Das eigentliche Beispiel - z. B. storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar

	Verwenden Sie den "jar"-Befehl, um Quellcode oder Javadocs zu extrahieren. Zum Beispiel 'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar'.

	> [AZURE.NOTE]Javadocs bestehen aus Webseiten. Öffnen Sie nach dem Extrahieren die Datei **index.html** in einem Browser.

	Für den Zugriff auf diese Beispiele müssen Sie Remotedesktop für Storm in HDInsight-Cluster aktivieren und die Dateien aus **%STORM_HOME%\\contrib** kopieren.

* Das folgende Dokument enthält eine Liste weiterer Beispiele, die mit Storm in HDInsight verwendet werden können:

	* [Beispiele für Storm-Topologien für Storm in HDInsight](hdinsight-storm-example-topology.md)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
[hdinsight-provision]: hdinsight-provision-clusters.md

<!--HONumber=54-->