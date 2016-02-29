<properties
pageTitle="Verwenden der Tez-Benutzeroberfläche mit Windows-basiertem HDInsight | Azure"
description="Informationen zum Verwenden der Tez-Benutzeroberfläche zum Debuggen von Tez-Aufträgen in Windows-basiertem HDInsight."
services="hdinsight"
documentationCenter=""
authors="Blackmist"
manager="paulettm"
editor="cgronlun"/>

<tags
ms.service="hdinsight"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="big-data"
ms.date="02/16/2016"
ms.author="larryfr"/>

# Verwenden der Tez-Benutzeroberfläche zum Debuggen von Tez-Aufträgen in Windows-basiertem HDInsight

Die Tez-Benutzeroberfläche ist eine Webseite, die verwendet werden kann, um Aufträge zu verstehen und zu debuggen, die Tez als Ausführungsmodul in Windows-basierten Clustern nutzen. Die Tez-Benutzeroberfläche ermöglicht Ihnen das Visualisieren des Auftrags als Graphen verbundener Elemente, einen Drilldown in die einzelnen Elemente und das Abrufen von Statistiken und Protokollinformationen.

> [AZURE.NOTE] Die Informationen in diesem Artikel gelten für Windows-basierte HDInsight-Cluster. Informationen zum Anzeigen und Debuggen von Tez für Linux-basiertes HDInsight finden Sie unter [Debuggen von Tez-Aufträgen in HDInsight mithilfe von Ambari-Ansichten](hdinsight-debug-ambari-tez-view.md).

##Voraussetzungen

* Ein Windows-basierter HDInsight-Cluster Anweisungen zum Erstellen eines neuen Clusters finden Sie unter [Erste Schritte mit Windows-basiertem HDInsight](hdinsight-hadoop-tutorial-get-started-windows.md).

    > [AZURE.IMPORTANT] Die Tez-Benutzeroberfläche ist nur für Windows-basierte Cluster verfügbar, die nach dem 8. Februar 2016 erstellt wurden.

* Ein Windows-basierter Remotedesktopclient.

##Grundlegendes zu Tez

Tez ist ein erweiterbares Framework für die Datenverarbeitung in Hadoop, das eine schnellere Verarbeitung als übliche MapReduce-Verfahren bietet. Bei Windows-basierten Clustern handelt es sich um ein optionales Modul, das mit dem folgenden Befehl als Teil Ihrer Hive-Abfrage für Hive aktiviert werden kann:

    set hive.execution.engine=tez;

Wenn Aufträge an Tez übermittelt werden, erstellt das Tool einen gerichteten azyklischen Graphen (Directed Acyclic Graph, DAG), der die Reihenfolge der Ausführung der Aktionen beschreibt, die für den Auftrag erforderlich sind. Einzelne Aktionen werden auch Scheitelpunkte genannt und führen einen Teil des gesamten Auftrags aus. Die tatsächliche Ausführung des Auftrags, die von einem Scheitelpunkt beschrieben wird, heißt Aufgabe und kann auf mehrere Knoten im Cluster verteilt werden.

###Grundlegendes zur Tez-Benutzeroberfläche

Die Tez-Benutzeroberfläche ist eine Webseite mit Informationen zu Prozessen, die ausgeführt werden oder zuvor mithilfe von Tez ausgeführt wurden. Sie ermöglicht das Anzeigen des von Tez generierten DAG, seiner Verteilung auf Cluster, von Leistungsindikatoren wie für den von Aufgaben und Scheitelpunkten belegten Arbeitsspeicher sowie von Fehlerinformationen. Diese Ansicht kann in den folgenden Szenarien nützliche Informationen bieten:

* Überwachen lang andauernder Prozesse und Anzeigen des Status von Map/Reduce-Aufgaben.

* Analysieren von Verlaufsdaten erfolgreicher oder fehlerhafter Prozesse, um zu erfahren, wie die Verarbeitung verbessert werden kann oder warum sie misslungen ist.

##Generieren eines gerichteten azyklischen Graphen (DAG)

Die Tez-Benutzeroberfläche enthält nur Daten, wenn ein Auftrag, der das Tez-Modul verwendet, derzeit ausgeführt wird oder zuvor ausgeführt wurde. Einfache Hive-Abfragen können normalerweise ohne Tez aufgelöst werden. Doch komplexere Abfragen mit Filterung, Gruppierung, Sortierung, Joins usw. benötigen in der Regel Tez.

Gehen Sie folgendermaßen vor, um eine Hive-Abfrage mit Tez auszuführen.

1. Navigieren Sie in einem Webbrowser zu https://CLUSTERNAME.azurehdinsight.net, wobei __CLUSTERNAME__ der Name des HDInsight-Clusters ist.

2. Wählen im Menü oben auf der Seite das Symbol __Hive-Editor__ aus. Dadurch wird eine Seite mit der folgenden Beispielabfrage angezeigt.

        Select * from hivesampletable

    Löschen Sie die Beispielabfrage, und ersetzen Sie sie durch Folgendes.

        set hive.execution.engine=tez;
        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;

3. Klicken Sie auf die Schaltfläche __Übermitteln__. Im Abschnitt __Auftragssitzung__ unten auf der Seite wird der Status der Abfrage gezeigt. Sobald sich der Status in __Abgeschlossen__ ändert, wählen Sie den Link __Details anzeigen__ aus, um die Ergebnisse anzuzeigen. Die __Auftragsausgabe__ sollte etwa wie folgt sein:
        
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        en-GB   Nairobi Area    Kenya

##Verwenden der Tez-Benutzeroberfläche

> [AZURE.NOTE] Die Tez-Benutzeroberfläche ist nur auf dem Desktop der Hauptknoten des Clusters verfügbar, weshalb Sie über Remotedesktop eine Verbindung mit den Hauptknoten herstellen müssen.

1. Wählen Sie im [Azure-Portal](https://portal.azure.com) Ihren HDInsight-Cluster aus. Wählen Sie oben im Blatt „HDInsight“ das Symbol __Remotedesktop__ aus. Das Blatt „Remotedesktop“ wird angezeigt.

    ![Symbol „Remotedesktop“](./media/hdinsight-debug-tez-ui/remotedesktopicon.png)

2. Wählen Sie auf dem Blatt „Remotedesktop“ __Verbinden__ aus, um eine Verbindung mit dem Hauptknoten des Clusters herzustellen. Geben Sie bei Aufforderung den Remotedesktop-Benutzernamen samt Kennwort für den Cluster an, um die Verbindung zu authentifizieren.

    ![Symbol „Remotedesktopverbindung“](./media/hdinsight-debug-tez-ui/remotedesktopconnect.png)

    > [AZURE.NOTE] Wenn Sie die Remotedesktopverbindung nicht aktiviert haben, geben Sie einen Benutzernamen, ein Kennwort und Ablaufdatum ein, und wählen Sie dann __Aktivieren__ aus, um Remotedesktop zu aktivieren. Führen Sie nach der Aktivierung die zuvor genannten Schritte zum Herstellen der Verbindung aus.

3. Sobald die Verbindung steht, öffnen Sie auf dem Remotedesktop Internet Explorer. Wählen Sie rechts oben im Browser das Zahnradsymbol und dann __Einstellungen der Kompatibilitätsansicht__ aus.

4. Deaktivieren Sie unten in __Einstellungen der Kompatibilitätsansicht__ die Kontrollkästchen __Intranetsites in Kompatibilitätsansicht anzeigen__ und __Kompatibilitätslisten von Microsoft verwenden__, und wählen Sie dann __Schließen__ aus.

5. Navigieren Sie in Internet Explorer zu http://headnodehost:8188/tezui/#/. Die Tez-Benutzeroberfläche wird geöffnet.

    ![Tez-Benutzeroberfläche](./media/hdinsight-debug-tez-ui/tezui.png)

    Nach Laden der Tez-Benutzeroberfläche sehen Sie eine Liste mit gerichteten azyklischen Graphen (Directed Acyclic Graphs, DAGs), die derzeit im Cluster ausgeführt werden oder zuvor ausgeführt wurden. Die Standardansicht enthält die folgenden Angaben für den DAG: Name, Id, Submitter, Status, Start Time, End Time, Duration, Application ID und Queue. Weitere Spalten können über das Zahnradsymbol rechts auf der Seite hinzugefügt werden.

    Wenn nur ein Eintrag vorhanden ist, gehört dieser zur Abfrage, die Sie im vorherigen Abschnitt ausgeführt haben. Wenn mehrere Einträge vorhanden sind, können Sie eine Suche durch Eingeben von Suchkriterien in die Felder über den DAGs und Drücken der EINGABETASTE starten.

4. Wählen Sie den __DAG-Namen__ des letzten DAG-Eintrags aus. Daraufhin werden Informationen zum DAG sowie die Option zum Herunterladen einer ZIP-Datei mit JSON-Dateien mit Informationen zum DAG angezeigt.

    ![DAG-Details](./media/hdinsight-debug-tez-ui/dagdetails.png)

5. Über den __DAG-Details__ befinden sich mehrere Links, die zum Anzeigen von Informationen über den DAG verwendet werden können.

    * __DAG Counters__ zeigt Informationen zu Leistungsindikatoren für diesen DAG.
    
    * __Graphical View__ zeigt eine grafische Darstellung dieses DAG.
    
    * __All Vertices__ zeigt eine Liste mit allen Scheitelpunkten in diesem DAG.
    
    * __All Tasks__ zeigt eine Liste der Aufgaben für alle Scheitelpunkte in diesem DAG.
    
    * __All TaskAttempts__ zeigt Informationen zu den Versuchen der Ausführung von Aufgaben für diesen DAG.
    
    > [AZURE.NOTE] Wenn Sie die Spaltenanzeige für „Vertices“, „Tasks“ und „TaskAttempts“ durchlaufen, sehen Sie Links zum Anzeigen von __Leistungsindikatoren__ und __Anzeigen oder Herunterladen von Protokollen__ für jede Zeile.

    Wenn beim Auftrag ein Fehler aufgetreten ist, werden in „DAG Details“ der Status FAILED sowie Informationen zur fehlerhaften Aufgabe angezeigt. Diagnoseinformationen wird unter den DAG-Details angezeigt.

7. Wählen Sie __Graphical View__ aus. Es wird eine grafische Darstellung des DAG gezeigt. Sie können den Mauszeiger über jedem Scheitelpunkt in der Ansicht platzieren, um Informationen dazu anzuzeigen.

    ![Grafische Ansicht](./media/hdinsight-debug-tez-ui/dagdiagram.png)

8. Durch Klicken auf einen Scheitelpunkt werden die __Vertex Details__ für dieses Element geladen. Klicken Sie auf den Scheitelpunkt __Map 1__, um Informationen zu diesem Element anzuzeigen. Wählen Sie __Bestätigen__ aus, um die Navigation zu bestätigen.

    ![Scheitelpunktdetails](./media/hdinsight-debug-tez-ui/vertexdetails.png)

9. Beachten Sie, dass sich nun oben auf der Seite Links befinden, die sich auf Scheitelpunkte und Aufgaben beziehen.

    > [AZURE.NOTE] Sie können auch auf diese Seite gelangen, indem Sie zu __DAG Details__ zurückkehren, __Vertex Details__ und dann den Scheitelpunkt __Map 1__ auswählen.

    * __Vertex Counters__ zeigt Leistungsindikatorinformationen für diesen Scheitelpunkt an.
    
    * __Tasks__ zeigt Aufgaben für diesen Scheitelpunkt an.
    
    * __Task Attempts__ zeigt Informationen zu Versuchen der Ausführung von Aufgaben für diesen Scheitelpunkt an.
    
    * __Sources & Sinks__ zeigt Datenquellen und Senken für diesen Scheitelpunkt an.

    > [AZURE.NOTE] Wie beim vorherigen Menü können Sie die Spaltenanzeige für „Tasks“, „Task Attempts“ und „Sources & Sinks“ durchlaufen, um Links zu weiteren Informationen zu den einzelnen Elementen anzuzeigen.

10. Wählen Sie __Tasks__ und dann das Element mit dem Namen __00\_000000__ aus. Dadurch werden __Task Details__ für diese Aufgabe angezeigt. Auf diesem Bildschirm können Sie __Task Counters__ und __Task Attempts__ anzeigen.

    ![Aufgabendetails](./media/hdinsight-debug-tez-ui/taskdetails.png)

##Nächste Schritte

Nachdem Sie sich mit der Verwendung der Tez-Ansicht vertraut gemacht haben, erfahren Sie mehr zum [Verwenden von Hive in HDInsight](hdinsight-use-hive.md).

Ausführliche technische Informationen zu Tez finden Sie auf der [Seite zu Tez bei Hortonworks](http://hortonworks.com/hadoop/tez/).

<!---HONumber=AcomDC_0218_2016-->