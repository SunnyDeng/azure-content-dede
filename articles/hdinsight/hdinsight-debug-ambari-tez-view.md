<properties
pageTitle="Verwenden der Ambari-Tez-Ansicht mit HDInsight | Azure"
description="Erfahren Sie, wie Sie die Ambari-Tez-Ansicht zum Debuggen von Tez-Aufträgen in HDInsight verwenden."
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

# Debuggen von Tez-Aufträgen in HDInsight mithilfe von Ambari-Ansichten

Die Ambari-Webbenutzeroberfläche für HDInsight enthält eine Tez-Ansicht, die verwendet werden kann, um Aufträge zu verstehen und zu debuggen, die Tez als Ausführungsmodul nutzen. Die Tez-Ansicht ermöglicht Ihnen das Visualisieren des Auftrags als Graphen verbundener Elemente, einen Drilldown in die einzelnen Elemente und das Abrufen von Statistiken und Protokollinformationen.

> [AZURE.NOTE] Die Informationen in diesem Artikel gelten für Linux-basierte HDInsight-Cluster. Informationen zum Debuggen von Tez-Aufträgen mithilfe von Windows-basiertem HDInsight finden Sie unter [Verwenden der Tez-Benutzeoberfläche zum Debuggen von Tez-Aufträgen in Windows-basiertem HDInsight](hdinsight-debug-tez-ui.md).

##Voraussetzungen

* Ein Linux-basierter HDInsight-Cluster. Anweisungen zum Erstellen eines neuen Clusters finden Sie unter [Erste Schritte mit Linux-basiertem HDInsight](hdinsight-hadoop-linux-tutorial-get-started.md).

* Ein zeitgemäßer Webbrowser, der HTML5 unterstützt.

##Grundlegendes zu Tez

Tez ist ein erweiterbares Framework für die Datenverarbeitung in Hadoop, das eine schnellere Verarbeitung als übliche MapReduce-Verfahren bietet. Bei Linux-basierten Clustern ist Tez das Standardmodul für Hive.

Wenn Aufträge an Tez übermittelt werden, erstellt das Tool einen gerichteten azyklischen Graphen (Directed Acyclic Graph, DAG), der die Reihenfolge der Ausführung der Aktionen beschreibt, die für den Auftrag erforderlich sind. Einzelne Aktionen werden auch Scheitelpunkte genannt und führen einen Teil des gesamten Auftrags aus. Die tatsächliche Ausführung des Auftrags, die von einem Scheitelpunkt beschrieben wird, heißt Aufgabe und kann auf mehrere Knoten im Cluster verteilt werden.

###Grundlegendes zur Tez-Ansicht

Die Tez-Ansicht liefert Informationen zu Prozessen, die ausgeführt werden oder zuvor mithilfe von Tez ausgeführt wurden. Sie ermöglicht das Anzeigen des von Tez generierten DAG, seiner Verteilung auf Cluster, von Leistungsindikatoren wie für den von Aufgaben und Scheitelpunkten belegten Arbeitsspeicher sowie von Fehlerinformationen. Diese Ansicht kann in den folgenden Szenarien nützliche Informationen bieten:

* Überwachen lang andauernder Prozesse und Anzeigen des Status von Map/Reduce-Aufgaben.

* Analysieren von Verlaufsdaten erfolgreicher oder fehlerhafter Prozesse, um zu erfahren, wie die Verarbeitung verbessert werden kann oder warum sie misslungen ist.

##Generieren eines gerichteten azyklischen Graphen (DAG)

Die Tez-Ansicht enthält nur Daten, wenn ein Auftrag, der das Tez-Modul verwendet, derzeit ausgeführt wird oder zuvor ausgeführt wurde. Einfache Hive-Abfragen können normalerweise ohne Tez aufgelöst werden. Doch komplexere Abfragen mit Filterung, Gruppierung, Sortierung, Joins usw. benötigen in der Regel Tez.

Gehen Sie folgendermaßen vor, um eine Hive-Abfrage mit Tez auszuführen.

1. Navigieren Sie in einem Webbrowser zu https://CLUSTERNAME.azurehdinsight.net, wobei __CLUSTERNAME__ der Name des HDInsight-Clusters ist.

2. Wählen im Menü oben auf der Seite das Symbol __Ansichten__ aus. Es sieht aus wie eine Reihe von Quadraten. Wählen Sie in der eingeblendeten Dropdownliste __Hive- Ansicht__ aus.

    ![Auswählen der Hive-Ansicht](./media/hdinsight-debug-ambari-tez-view/selecthive.png)

3. Fügen Sie in der geladenen Hive-Ansicht Folgendes in den Abfrage-Editor ein, und klicken Sie dann auf __Ausführen__.

        select market, state, country from hivesampletable where deviceplatform='Android' group by market, country, state;
    
    Sobald der Auftrag abgeschlossen ist, sollte die Ausgabe im Abschnitt __Abfrageprozessergebnisse__ angezeigt werden. Die Ergebnisse sollten in etwa wie folgt aussehen:
    
        market  state       country
        en-GB   Hessen      Germany
        en-GB   Kingston    Jamaica
        
4. Wählen Sie die Registerkarte __Protokoll__ aus. Die angezeigten Informationen sehen in etwa folgendermaßen aus:
    
        INFO : Session is already open
        INFO :

        INFO : Status: Running (Executing on YARN cluster with App id application_1454546500517_0063)

    Speichern Sie den Wert von __App-ID__, da dieser im nächsten Abschnitt verwendet wird.

##Verwenden der Tez-Ansicht

1. Wählen im Menü oben auf der Seite das Symbol __Ansichten__ aus. Wählen Sie in der eingeblendeten Dropdownliste __Tez-Ansicht__ aus.

    ![Auswählen der Tez-Ansicht](./media/hdinsight-debug-ambari-tez-view/selecttez.png)

2. Nach Laden der Tez-Ansicht sehen Sie eine Liste mit gerichteten azyklischen Graphen (Directed Acyclic Graphs, DAGs), die derzeit im Cluster ausgeführt werden oder zuvor ausgeführt wurden. Die Standardansicht enthält die folgenden Angaben für den DAG: Name, Id, Submitter, Status, Start Time, End Time, Duration, Application ID und Queue. Weitere Spalten können über das Zahnradsymbol rechts auf der Seite hinzugefügt werden.

    ![Alle DAGs](./media/hdinsight-debug-ambari-tez-view/alldags.png)

3. Wenn nur ein Eintrag vorhanden ist, gehört dieser zur Abfrage, die Sie im vorherigen Abschnitt ausgeführt haben. Wenn mehrere Einträge vorhanden sind, können Sie eine Suche durch Eingeben der Anwendungs-ID in das Feld __Application ID__ und Drücken der EINGABETASTE starten.

4. Wählen Sie den __Namen des DAG__ aus. Daraufhin werden Informationen zum DAG sowie die Option zum Herunterladen einer ZIP-Datei mit JSON-Dateien mit Informationen zum DAG angezeigt.

    ![DAG-Details](./media/hdinsight-debug-ambari-tez-view/dagdetails.png)

5. Über den __DAG-Details__ befinden sich mehrere Links, die zum Anzeigen von Informationen über den DAG verwendet werden können.

    * __DAG Counters__ zeigt Informationen zu Leistungsindikatoren für diesen DAG.
    
    * __Graphical View__ zeigt eine grafische Darstellung dieses DAG.
    
    * __All Vertices__ zeigt eine Liste mit allen Scheitelpunkten in diesem DAG.
    
    * __All Tasks__ zeigt eine Liste der Aufgaben für alle Scheitelpunkte in diesem DAG.
    
    * __All TaskAttempts__ zeigt Informationen zu den Versuchen der Ausführung von Aufgaben für diesen DAG.
    
    > [AZURE.NOTE] Wenn Sie die Spaltenanzeige für „Vertices“, „Tasks“ und „TaskAttempts“ durchlaufen, sehen Sie Links zum Anzeigen von __Leistungsindikatoren__ und __Anzeigen oder Herunterladen von Protokollen__ für jede Zeile.

    Wenn beim Auftrag ein Fehler aufgetreten ist, werden in „DAG Details“ der Status FAILED sowie Informationen zur fehlerhaften Aufgabe angezeigt. Diagnoseinformationen wird unter den DAG-Details angezeigt.
    
    ![Eine DAG-Detailbildschirm mit Details zu einem Fehler](./media/hdinsight-debug-ambari-tez-view/faileddag.png)

7. Wählen Sie __Graphical View__ aus. Es wird eine grafische Darstellung des DAG gezeigt. Sie können den Mauszeiger über jedem Scheitelpunkt in der Ansicht platzieren, um Informationen dazu anzuzeigen.

    ![Grafische Ansicht](./media/hdinsight-debug-ambari-tez-view/dagdiagram.png)

8. Durch Klicken auf einen Scheitelpunkt werden die __Vertex Details__ für dieses Element geladen. Klicken Sie auf den Scheitelpunkt __Map 1__, um Informationen zu diesem Element anzuzeigen.

    ![Scheitelpunktdetails](./media/hdinsight-debug-ambari-tez-view/vertexdetails.png)

9. Beachten Sie, dass sich nun oben auf der Seite Links befinden, die sich auf Scheitelpunkte und Aufgaben beziehen.

    > [AZURE.NOTE] Sie können auch auf diese Seite gelangen, indem Sie zu __DAG Details__ zurückkehren, __Vertex Details__ und dann den Scheitelpunkt __Map 1__ auswählen.

    * __Vertex Counters__ zeigt Leistungsindikatorinformationen für diesen Scheitelpunkt an.
    
    * __Tasks__ zeigt Aufgaben für diesen Scheitelpunkt an.
    
    * __Task Attempts__ zeigt Informationen zu Versuchen der Ausführung von Aufgaben für diesen Scheitelpunkt an.
    
    * __Sources & Sinks__ zeigt Datenquellen und Senken für diesen Scheitelpunkt an.

    > [AZURE.NOTE] Wie beim vorherigen Menü können Sie die Spaltenanzeige für „Tasks“, „Task Attempts“ und „Sources & Sinks“ durchlaufen, um Links zu weiteren Informationen zu den einzelnen Elementen anzuzeigen.

10. Wählen Sie __Tasks__ und dann das Element mit dem Namen __00\_000000__ aus. Dadurch werden __Task Details__ für diese Aufgabe angezeigt. Auf diesem Bildschirm können Sie __Task Counters__ und __Task Attempts__ anzeigen.

    ![Aufgabendetails](./media/hdinsight-debug-ambari-tez-view/taskdetails.png)

##Nächste Schritte

Nachdem Sie sich mit der Verwendung der Tez-Ansicht vertraut gemacht haben, erfahren Sie mehr zum [Verwenden von Hive in HDInsight](hdinsight-use-hive.md).

Ausführliche technische Informationen zu Tez finden Sie auf der [Seite zu Tez bei Hortonworks](http://hortonworks.com/hadoop/tez/).

Weitere Informationen zur Verwendung von Ambari mit HDInsight finden Sie unter [Verwalten von HDInsight-Clustern mithilfe der Ambari-Webbenutzeroberfläche](hdinsight-hadoop-manage-ambari.md).

<!---HONumber=AcomDC_0218_2016-->