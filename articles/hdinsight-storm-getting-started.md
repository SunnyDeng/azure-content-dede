<properties title="Erste Schritte mit Storm und Hadoop in HDInsight" pageTitle="Erste Schritte mit Apache Storm und Microsoft Azure HDInsight (Hadoop)" description="Erfahren Sie, wie Sie Daten mit Apache Storm und HDInsight (Hadoop) in Echtzeit verarbeiten k&ouml;nnen" metaKeywords="Azure hdinsight storm, Azure hdinsight realtime, azure hadoop storm, azure hadoop realtime, azure hadoop real-time, azure hdinsight real-time" services="hdinsight" solutions="" documentationCenter="big-data" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="09/30/2014" ms.author="larryfr" />

# Erste Schritte mit Storm und HDInsight (Hadoop)

Apache Storm ist ein skalierbares, fehlertolerantes, verteiltes Echtzeit-Berechnungssystem für die Verarbeitung von Datenströmen. Mit Azure HDInsight können Sie ein cloudbasiertes Hadoop-Cluster für die Echtzeit-Datenanalyse mit Storm erstellen.

## In diesem Artikel lernen Sie Folgendes:

-   [Bereitstellen eines HDInsight Storm-Clusters][Bereitstellen eines HDInsight Storm-Clusters]

-   [Verbinden mit dem Cluster][Verbinden mit dem Cluster]

-   [Ausführen einer Storm-Topologie][Ausführen einer Storm-Topologie]

-   [Überwachen einer Storm-Topologie][Überwachen einer Storm-Topologie]

-   [Beenden einer Storm-Topologie][Beenden einer Storm-Topologie]

-   [Nächste Schritte][Nächste Schritte]

## Voraussetzungen

Sie benötigen Folgendes, um dieses Lernprogramm abzuschließen.

-   Ein Azure-Abonnement

-   Windows Azure PowerShell

-   Falls Sie nicht mit Apache Storm vertraut sind, sollten Sie zunächst den Artikel [HDInsight Storm: Übersicht][HDInsight Storm: Übersicht] lesen.

## <span id="provision"></span></a>Bereitstellen eines Storm-Clusters im Azure-Portal

[WACOM.INCLUDE [provisioningnote](../includes/hdinsight-provisioning.md)]

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an

2.  Klicken Sie links auf **HDInsight** und anschließend unten links auf **+NEU**.

3.  Klicken Sie auf das HDInsight-Symbol in der zweiten Spalte und wählen Sie **Benutzerdefiniert** aus.

4.  Geben Sie in der Seite **Clusterdetails** den Namen für das neue Cluster ein und wählen Sie **Storm** als **Clustertyp** aus. Wählen Sie den Pfeil, um fortzufahren.

    ![Clusterdetails][Clusterdetails]

5.  Geben Sie die Anzahl der **Datenknoten** ein und wählen Sie **Region bzw. virtuelles Netzwerk** für dieses Cluster aus. Klicken Sie auf den Pfeil, um fortzufahren.

    > [WACOM.NOTE] Reduzieren Sie die **Clustergröße** auf 1 und löschen Sie das Cluster nach der Verwendung, um die Kosten für das in diesem Artikel verwendete Cluster zu minimieren.

    ![Datenknoten und Region][Datenknoten und Region]

6.  Geben Sie **Benutzername** und **Kennwort** für den Administrator ein und klicken Sie auf den Pfeil, um fortzufahren.

    ![Konto und Kennwort][Konto und Kennwort]

7.  Wählen Sie unter **Speicherkonto** entweder **Neuen Speicher erstellen** aus oder verwenden Sie ein vorhandenes Speicherkonto. Wählen Sie den zu verwendenden **Kontonamen** und den **Standardcontainer** aus. Klicken Sie auf das Häkchensymbol unten links, um das Storm-Cluster zu erstellen.

    ![Speicherkonto][Speicherkonto]

## Verwenden von HDInsight Storm

Für die Vorschauversion von HDInsight Storm müssen Sie sich per Remotedesktop mit dem Cluster verbinden, um mit Storm arbeiten zu können. Führen Sie die folgenden Schritte aus, um sich mit dem HDInsight-Cluster zu verbinden und Storm-Befehle auszuführen.

### <span id="connect"></span></a>Verbinden mit dem Cluster

1.  Aktivieren Sie die Remotedesktopverbindung für Ihr HDInsight-Cluster im [Azure-Verwaltungsportal][management]. Wählen Sie im Portal Ihren HDInsight-Cluster aus und dann **Remote aktivieren** unten auf der Seite **Konfiguration**.

    ![enable remote][enable remote]

    Wenn Sie aufgefordert werden, geben Sie einen Benutzernamen und ein Kennwort für Remotesitzungen ein. Außerdem müssen Sie ein Ablaufdatum für den Remotezugriff angeben.

    ![Remotedesktop-Konfiguration][Remotedesktop-Konfiguration]

2.  Nachdem der Remotezugriff aktiviert ist, wählen Sie **Verbinden** und starten Sie die Verbindung. Dabei wird eine **.rdp**-Datei heruntergeladen, mit der eine Remotedesktop-Sitzung gestartet werden kann.

    ![connect][connect]

    > [WACOM.NOTE] Bei der Verbindung zum Remote-Computer erhalten Sie möglicherweise mehrere Aufforderungen, Zertifikaten zu vertrauen.

3.  Klicken Sie nach dem Verbindungsaufbau auf das Symbol **Hadoop-Befehlszeile** auf dem Desktop, um die Hadoop-Befehlszeile zu öffnen.

    ![hadoop cli][hadoop cli]

4.  Führen Sie in der Hadoop-Befehlszeile den folgenden Befehl aus, um in das Verzeichnis zu wechseln, das den Storm-Befehl enthält.

    cd %storm\_home%\\bin

5.  Geben Sie `storm` ohne Parameter ein, um eine Liste der verfügbaren Befehle anzuzeigen.

Das HDInsight-Cluster enthält bereits verschiedene Storm-Topologien. Das Beispiel **WordcountTopology** wird in den folgenden Schritten verwendet. Weitere Informationen zu den Beispielen in HDInsight Storm finden Sie unter [Nächste Schritte][Nächste Schritte].

### <span id="run"></span></a>Ausführen einer Storm-Topologie

Führen Sie die folgenden Befehle aus, um das **WordCountTopology**-Beispiel auszuführen.

    storm jar ..\contrib\storm-starter\storm-starter-<version>-jar-with-dependencies.jar storm.starter.WordCountTopology wordcount

Damit weisen Sie Storm an, die **wordcount**-Topologie über die **storm.starter.WordCountTopology**-Klasse auszuführen, die in der Datei **storm-starter-\<version\>-jar-with-dependencies.jar** enthalten ist. Diese Datei befindet sich im Unterverzeichnis "contrib" im Verzeichnis "%storm\_home%", gemeinsam mit den anderen Storm-Beispielen.

> [WACOM.NOTE] Die Version der JAR-Datei mit den Beispielen kann sich regelmäßig ändern. Geben Sie beim Ausführen dieses Befehls die Version der Datei in Ihrem Cluster an.

Beachten Sie, dass beim Ausführen dieses Befehls nichts zurückgegeben wird. **Storm-Topologien werden nach dem Start solange ausgeführt, bis sie beendet werden.**Die The WordCountTopology generiert zufällige Sequenzen und zählt mit, wie oft jedes Wort vorkommt, bis Sie die Topologie beenden.

### <span id="monitor"></span></a>Überwachen des Status einer Storm-Topologie

Das WordCountTopology-Beispiel schreibt keine Ausgaben in ein Verzeichnis. Sie können jedoch in den Storm UI-Webseiten den Status der Topologie sowie deren Ausgaben abrufen.

1.  Verbinden Sie sich per Remotedesktop mit dem HDInsight-Cluster.

2.  Öffnen Sie die **Storm UI**-Verknüpfung auf dem Desktop. Daraufhin wird die Storm UI-Webseite geöffnet. Klicken Sie unter **Topologie-Zusammenfassung** auf den Eintrag **wordcount**.

    ![Storm UI][Storm UI]

    Daraufhin werden Statistiken für die Topologie angezeigt, inklusive der Bestandteile der Topologie, den **Spouts** und **Bolts**.

3.  Wählen Sie die **Spout**-Verknüpfung auf der Seite aus und klicken Sie auf die **Portnummer** für einen der Einträge in der Liste **Ausführer (Gesamtdauer)** mit einer Nummer größer als 0 in den Spalten **Ausgegeben** und **Übertragen**.

    ![Spouts und Bolts][Spouts und Bolts]

    ![Auswählen der Ausführer][Auswählen der Ausführer]

    > [WACOM.NOTE] Je nach Anzahl der Knoten in Ihrem Cluster und der ausgeführten Topologie dauert es möglicherweise mehrere Minuten, bis die Topologie mit der Verarbeitung beginnt. Aktualisieren Sie die Seite regelmäßig, bis die Werte unter **Ausgegeben** und **Übertragen** ansteigen.

4.  Die Ausgabe sollte in etwa wie folgt aussehen.

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

    In diesem Ausschnitt hat der Spout den Text 'snow white and the seven dwarfs' ausgegeben, der daraufhin in einzelne Worte aufgeteilt wurde. Außerdem zählt die Topologie, wie oft jedes einzelne Wort seit dem Start der Topologie verarbeitet wurde. Das Wort 'dwarfs' wurde zum Zeitpunkt der obigen Ausgabe 8360 mal vom Spout ausgegeben.

### <span id="stop"></span></a>Beenden einer Storm-Topologie

Die **WordCountTopology** läuft solange, bis Sie sie beenden. Führen Sie den folgenden Befehl aus, um die Topologie zu beenden.

    storm kill wordcount

Wenn Sie die Storm UI-Webseite direkt nach dem Ausführen dieses Befehls anzeigen, wird der Status für **wordcount** in der **Topologie-Zusammenfassung** als KILLED angezeigt. Wenige Sekunden später wird die Topologie nicht mehr im Bereich **Topologie-Zusammenfassung** angezeigt.

## <span id="next"></span></a>Nächste Schritte

-   **Beispieldateien** - Das HDInsight Storm-Cluster enthält verschiedene Beispiele im Verzeichnis **%storm\_home%\\contrib**. Jedes Beispiel enthält Folgendes.

    -   Quellcode - z. B. storm-starter-0.9.1.2.1.5.0-2057-sources.jar

    -   Javadocs - z. B. storm-starter-0.9.1.2.1.5.0-2057-javadoc.jar

    -   Das eigentliche Beispiel - z. B. storm-starter-0.9.1.2.1.5.0-2057-jar-with-dependencies.jar

    Verwenden Sie den "jar"-Befehl, um Quellcode oder Javadocs zu extrahieren. Zum Beispiel 'jar -xvf storm-starter-0.9.1.2.1.5.0.2057-javadoc.jar'.

    > [WACOM.NOTE] Javadocs bestehen aus Webseiten. Öffnen Sie nach dem Extrahieren die Datei **index.html** in einem Browser.

-   [Analysieren von Sensordaten mit Storm und HDInsight][Analysieren von Sensordaten mit Storm und HDInsight]

-   [Entwickeln von Anwendungen zur Verarbeitung von Streamingdaten mit SCP.NET und C# mit Storm in HDInsight][Entwickeln von Anwendungen zur Verarbeitung von Streamingdaten mit SCP.NET und C# mit Storm in HDInsight]

  [Bereitstellen eines HDInsight Storm-Clusters]: #provision
  [Verbinden mit dem Cluster]: #connect
  [Ausführen einer Storm-Topologie]: #run
  [Überwachen einer Storm-Topologie]: #monitor
  [Beenden einer Storm-Topologie]: #stop
  [Nächste Schritte]: #next
  [HDInsight Storm: Übersicht]: /de-de/documentation/articles/hdinsight-storm-overview
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [Clusterdetails]: ./media/hdinsight-storm-getting-started/wizard1.png
  [Datenknoten und Region]: ./media/hdinsight-storm-getting-started/wizard2.png
  [Konto und Kennwort]: ./media/hdinsight-storm-getting-started/wizard3.png
  [Speicherkonto]: ./media/hdinsight-storm-getting-started/wizard4.png
  [enable remote]: ./media/hdinsight-storm-getting-started/enableremotedesktop.png
  [Remotedesktop-Konfiguration]: ./media/hdinsight-storm-getting-started/configremotedesktop.png
  [connect]: ./media/hdinsight-storm-getting-started/connect.png
  [hadoop cli]: ./media/hdinsight-storm-getting-started/hadoopcommandline.png
  [Storm UI]: ./media/hdinsight-storm-getting-started/stormui.png
  [Spouts und Bolts]: ./media/hdinsight-storm-getting-started/stormuiboltsnspouts.png
  [Auswählen der Ausführer]: ./media/hdinsight-storm-getting-started/executors.png
  [Analysieren von Sensordaten mit Storm und HDInsight]: /de-de/documentation/articles/hdinsight-storm-sensor-data-analysis
  [Entwickeln von Anwendungen zur Verarbeitung von Streamingdaten mit SCP.NET und C# mit Storm in HDInsight]: /de-de/documentation/articles/hdinsight-hadoop-storm-scpdotnet-csharp-develop-streaming-data-processing-application
