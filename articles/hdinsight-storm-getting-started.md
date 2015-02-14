<properties 
	pageTitle="Erste Schritte mit Apache Storm und Microsoft Azure HDInsight (Hadoop)" 
	description="Erfahren Sie, wie Sie  Apache Storm zum Verarbeiten von Daten in Echtzeit mit HDInsight (Hadoop) verwenden." 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/19/2014" 
	ms.author="larryfr"/>

# Erste Schritte mit Storm und HDInsight (Hadoop)

Apache Storm ist ein skalierbares, fehlertolerantes, verteiltes Echtzeit-Berechnungssystem für die Verarbeitung von Datenströmen. Mit Azure HDInsight können Sie ein cloudbasiertes Hadoop-Cluster für die Echtzeit-Datenanalyse mit Storm erstellen.

## In diesem Artikel lernen Sie Folgendes:

* [Bereitstellen eines Storm-Clusters in HDInsight](#provision)

* [Herstellen einer Verbindung mit dem Cluster](#connect)

* [Ausführen einer Storm-Topologie](#run)

* [Überwachen einer Storm-Topologie](#monitor)

* [Beenden einer Storm-Topologie](#stop)

* [Nächste Schritte](#next)

## Voraussetzungen

Sie benötigen Folgendes, um dieses Lernprogramm abzuschließen.

* Ein Azure-Abonnement

* Windows Azure PowerShell

* Falls Sie nicht mit Apache Storm vertraut sind, sollten Sie zunächst den Artikel [Storm in HDInsight: Übersicht](/de-de/documentation/articles/hdinsight-storm-overview) lesen.

## <a id="provision"></a>Bereitstellen eines Storm-Clusters im Azure-Portal

[AZURE.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

1. Melden Sie sich beim [Azure-Verwaltungsportal][azureportal] an.

2. Klicken Sie links auf **HDInsight** und anschließend unten links auf **+NEU**.

3. Klicken Sie auf das HDInsight-Symbol in der zweiten Spalte, und wählen Sie **Benutzerdefiniert** aus.

4. Geben Sie auf der Seite **Clusterdetails** den Namen für das neue Cluster ein, und wählen Sie **Storm** als **Clustertyp** aus. Klicken Sie auf den Pfeil, um fortzufahren.

	![cluster details](./media/hdinsight-storm-getting-started/wizard1.png)

5. Geben Sie die Anzahl der **Datenknoten** ein, und wählen Sie **Region bzw. virtuelles Netzwerk** für dieses Cluster aus. Klicken Sie auf den Pfeil, um fortzufahren.

	> [AZURE.NOTE] Reduzieren Sie die **Clustergröße** auf 1, und löschen Sie den Cluster nach der Verwendung, um die Kosten für den in diesem Artikel verwendeten Cluster zu minimieren.

	![data nodes and region](./media/hdinsight-storm-getting-started/wizard2.png)

6. Geben Sie den **Benutzernamen** und das **Kennwort** für den Administrator ein, und klicken Sie auf den Pfeil, um fortzufahren.

	![account and password](./media/hdinsight-storm-getting-started/wizard3.png)

7. Wählen Sie unter **Speicherkonto** entweder **Neuen Speicher erstellen** aus, oder verwenden Sie ein vorhandenes Speicherkonto. Wählen Sie den zu verwendenden **Kontonamen** und **Standardcontainer** aus, oder geben Sie sie ein.

	![storage account](./media/hdinsight-storm-getting-started/wizard4.png)

8. Klicken Sie auf der Seite **Skriptaktionen** auf **Skriptaktion hinzufügen**, um Einzelheiten zum benutzerdefinierten Skript anzugeben, das Sie zum Anpassen eines Clusters bei seiner Erstellung ausführen möchten. Sie können beispielsweise Skriptaktion verwenden, um ein Cluster zum Installieren von <a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a>anzupassen. Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktion](/de-de/documentation/articles/hdinsight-hadoop-customize-cluster/). 
	
	![Configure Script Action to customize an HDInsight HBase cluster](./media/hdinsight-storm-getting-started/wizard5.png "Use Script Action to customize an HDInsight cluster") 

	<table border='1'>
		<tr><th>Eigenschaft</th><th>Wert</th></tr>
		<tr><td>Name</td>
			<td>Geben Sie einen Namen für die Skriptaktion an.</td></tr>
		<tr><td>Skript-URI</td>
			<td>Geben Sie den URI für das Skript an, das aufgerufen wird, um den Cluster anzupassen.</td></tr>
		<tr><td>Knotentyp</td>
			<td>Gibt die Knoten an, auf denen das Anpassungsskript ausgeführt wird. Sie können <b>Alle Knoten</b>, <b>Nur Hauptknoten</b> oder <b>Nur Workerknoten</b> auswählen.
		<tr><td>Parameter</td>
			<td>Geben Sie die Parameter an, wenn dies für das Skript erforderlich ist.</td></tr>
	</table>

	Sie können dem Cluster mehr als eine Skriptaktion zum Installieren von mehreren Komponenten hinzufügen. Nachdem Sie die Skripts hinzugefügt haben, klicken Sie auf das Häkchen, um die Bereitstellung des Clusters zu starten.

## Verwenden von Storm in HDInsight

Für die Vorschauversion von Storm in HDInsight müssen Sie per Remotedesktop eine Verbindung mit dem Cluster herstellen, um mit Storm arbeiten zu können. Führen Sie die folgenden Schritte aus, um sich mit dem HDInsight-Cluster zu verbinden und Storm-Befehle auszuführen.

###<a id="connect"></a>Verbinden mit dem Cluster

1. Aktivieren Sie die Remotedesktopverbindung für Ihr HDInsight-Cluster im [Azure-Verwaltungsportal][management]. Wählen Sie im Portal Ihren HDInsight-Cluster aus und dann "__Remote aktivieren__" unten auf der Seite "__Konfiguration__".

    ![enable remote](./media/hdinsight-storm-getting-started/enableremotedesktop.png)

    Wenn Sie aufgefordert werden, geben Sie einen Benutzernamen und ein Kennwort für Remotesitzungen ein. Außerdem müssen Sie ein Ablaufdatum für den Remotezugriff angeben.

	![remote desktop config](./media/hdinsight-storm-getting-started/configremotedesktop.png)

2. Nachdem der Remotezugriff aktiviert ist, wählen Sie "Verbinden" und starten Sie die Verbindung. Dabei wird eine .rdp-Datei heruntergeladen, mit der eine Remotedesktop-Sitzung gestartet werden kann.

    ![connect](./media/hdinsight-storm-getting-started/connect.png)

	> [AZURE.NOTE] Bei der Verbindung zum Remote-Computer erhalten Sie möglicherweise mehrere Aufforderungen, Zertifikaten zu vertrauen.

3. Klicken Sie nach dem Verbindungsaufbau auf das Symbol "Hadoop-Befehlszeile" auf dem Desktop, um die Hadoop-Befehlszeile zu öffnen.

	![hadoop cli](./media/hdinsight-storm-getting-started/hadoopcommandline.png)

4. Führen Sie in der Hadoop-Befehlszeile den folgenden Befehl aus, um in das Verzeichnis zu wechseln, das den Storm-Befehl enthält.

	cd %storm_home%\bin

5. Geben Sie `storm` ohne Parameter ein, um eine Liste der verfügbaren Befehle anzuzeigen.

Das HDInsight-Cluster enthält bereits verschiedene Storm-Topologien. Die folgenden Schritten basieren auf dem Beispiel **WordcountTopology**. Weitere Informationen zu den Beispielen in Storm in HDInsight finden Sie unter [Nächste Schritte](#next).

### <a id="run"></a>Ausführen einer Storm-Topologie

Führen Sie die folgenden Befehle aus, um das **WordCountTopology**-Beispiel auszuführen.

	storm jar ..\contrib\storm-starter\storm-starter-<version>-jar-with-dependencies.jar storm.starter.WordCountTopology wordcount

Damit weisen Sie Storm an, die **wordcount**-Topologie über die **storm.starter.WordCountTopology**-Klasse auszuführen, die in der Datei **storm-starter-&lt;version>-jar-with-dependencies.jar** enthalten ist. Diese Datei befindet sich im Unterverzeichnis "contrib" im Verzeichnis "%storm_home%", gemeinsam mit den anderen Storm-Beispielen.

> [AZURE.NOTE] Die Version der JAR-Datei mit den Beispielen kann sich regelmäßig ändern. Geben Sie beim Ausführen dieses Befehls die Version der Datei in Ihrem Cluster an.

Beachten Sie, dass beim Ausführen dieses Befehls nichts zurückgegeben wird. **Eine gestartete Storm-Topologie wird solange ausgeführt, bis Sie sie beenden.** Die WordCountTopology generiert zufällige Sätze und zählt, wie oft ein bestimmtes Wort vorkommt, bis Sie die Zählung beenden.

### <a id="monitor"></a>Überwachen des Status einer Storm-Topologie

Das WordCountTopology-Beispiel schreibt keine Ausgaben in ein Verzeichnis. Sie können jedoch in den Storm UI-Webseiten den Status der Topologie sowie deren Ausgaben abrufen.

1. Verbinden Sie sich per Remotedesktop mit dem HDInsight-Cluster.

2. Öffnen Sie die **Storm UI**-Verknüpfung auf dem Desktop. Daraufhin wird die Storm UI-Webseite geöffnet.  Klicken Sie unter **Topologiezusammenfassung** auf den Eintrag **wordcount**.

	![storm ui](./media/hdinsight-storm-getting-started/stormui.png)

	Daraufhin werden Statistiken für die Topologie angezeigt, inklusive der Bestandteile der Topologie, den **Spouts** und **Bolts**.

3. Wählen Sie auf der Seite die Verknüpfung **Spout** und dann die **Portnummer** für einen der Einträge in der Liste **Ausführer (Alle Zeiten)** aus, der in den Spalten **Ausgegeben** und **Übertragen** eine Zahl enthält, die größer als "0" ist.

	![spouts and bolts](./media/hdinsight-storm-getting-started/stormuiboltsnspouts.png)

	![selecting executors](./media/hdinsight-storm-getting-started/executors.png)

	> [AZURE.NOTE] Je nach Anzahl der Knoten in Ihrem Cluster und der ausgeführten Topologie dauert es möglicherweise mehrere Minuten, bis die Topologie mit der Verarbeitung beginnt. Aktualisieren Sie die Seite regelmäßig, bis die Werte unter **Ausgegeben** und **Übertragen** ansteigen.

4. Die Ausgabe sollte in etwa wie folgt aussehen.

		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [snow white and the seven dwarfs]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16774]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16775]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:17, stream: default, id: {}, [dwarfs]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8359]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:20, stream: default, id: {}, [dwarfs]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [dwarfs, 8360]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: spout default [i am at two with nature]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:23, stream: default, id: {}, [two]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [two, 8236]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:22, stream: default, id: {}, [a]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [a, 8280]
		2014-09-24 14:16:22 b.s.d.executor [INFO] Processing received message source: split:19, stream: default, id: {}, [and]
		2014-09-24 14:16:22 b.s.d.task [INFO] Emitting: count default [and, 16776]

	In diesem Ausschnitt hat der Spout den Text 'snow white and the seven dwarfs' ausgegeben, der daraufhin in einzelne Worte aufgeteilt wurde. Außerdem zählt die Topologie, wie oft jedes einzelne Wort seit dem Start der Topologie verarbeitet wurde. Das Wort 'dwarfs' wurde zum Zeitpunkt der obigen Ausgabe 8360 Mal vom Spout ausgegeben.

### <a id="stop"></a>Beenden einer Storm-Topologie

Die **WordCountTopology** wird solange ausgeführt, bis Sie sie beenden. Führen Sie den folgenden Befehl aus, um die Topologie zu beenden.

	storm kill wordcount

Wenn Sie die Storm UI-Webseite direkt nach dem Ausführen dieses Befehls anzeigen, wird der Status für **wordcount** in der **Topologiezusammenfassung** als "ABGEBROCHEN" angezeigt. Wenige Sekunden später wird die Topologie nicht mehr im Bereich **Topologiezusammenfassung** angezeigt.

## <a id="next"></a>Nächste Schritte

* **Beispieldateien** - Der Storm-Cluster enthält verschiedene Beispiele im Verzeichnis **%storm_home%\contrib**. Jedes Beispiel enthält Folgendes.

	* Quellcode - z. B. storm-starter-0.9.1.2.1.5.0-2057-sources.jar

	* Javadocs - z. B. storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar

	* Das eigentliche Beispiel - z. B. storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar

	Verwenden Sie den 'jar'-Befehl, um Quellcode oder Javadocs zu extrahieren. Beispiel:  'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar'.

	> [AZURE.NOTE] Javadocs bestehen aus Webseiten. Öffnen Sie nach dem Extrahieren die Datei **index.html** in einem Browser.

* [Analysieren von Sensordaten mit Storm und HBase in HDInsight (Hadoop)](/de-de/documentation/articles/hdinsight-storm-sensor-data-analysis)

* [Entwickeln von Anwendungen zur Verarbeitung von Datenströmen in C# mit Stream Computing Platform und Storm in HDInsight](/de-de/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application)

[apachestorm]: https://storm.incubator.apache.org
[stormdocs]: http://storm.incubator.apache.org/documentation/Documentation.html
[stormstarter]: https://github.com/apache/storm/tree/master/examples/storm-starter
[stormjavadocs]: https://storm.incubator.apache.org/apidocs/
[azureportal]: https://manage.windowsazure.com/
<!--HONumber=42-->
