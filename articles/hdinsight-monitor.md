<properties linkid="manage-services-hdinsight-howto-monitor-hdinsight" urlDisplayName="Monitor" pageTitle="Monitor HDInsight | Azure" metaKeywords="" description="Learn how to monitor an HDInsight cluster and view Hadoop job history through the Azure management portal." metaCanonical="" services="hdinsight" documentationCenter="" title="How to Monitor HDInsight" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />

So überwachen Sie HDInsight
===========================

In diesem Thema wird beschrieben, wie Sie einen HDInsight-Cluster überwachen.

Inhaltsverzeichnis
------------------

-   [Gewusst wie: Überwachen eines HDInsight-Clusters](#monitorcluster)
-   [Gewusst wie: Anzeigen des Hadoop-Jobverlaufs](#jobhistory)

Gewusst wie: Überwachen eines HDInsight-Clusters
------------------------------------------------

Um die Integrität eines HDInsight-Clusters und der auf dem Cluster ausgeführten Hadoop-Jobs zu überwachen, können Sie sich mit dem HDInsight-Dashboard verbinden und auf die Kachel "Monitor Cluster" klicken.

![HDI.TileMonitorCluster](./media/hdinsight-monitor/HDI.TileMonitorCluster.PNG)

Die Überwachungsseite sieht wie folgt aus:

![HDI.MonitorPage](./media/hdinsight-monitor/HDI.MonitorPage.PNG)

Auf der rechten Seite sehen Sie den ausgeführten NameNode und JobTracker sowie die vier Datenknoten, die fehlerfrei ausgeführt werden.

Auf der linken Seite werden die MapReduce-Metriken für die letzten 30 Minuten angezeigt. Sie können diese Zeit für die Überwachungsfenster in 30 Minuten, 1 Stunde, 3 Stunden, 12 Stunden, 1 Tag, 2 Tage, 1 Woche und 2 Wochen ändern.

Gewusst wie: Anzeigen des Hadoop-Jobverlaufs
--------------------------------------------

Um den Hadoop-Jobverlauf anzuzeigen, verbinden Sie sich mit dem HDInsight-Dashboard, und klicken Sie auf die Kachel "Job History".

![HDI.TileJobHistory](./media/hdinsight-monitor/HDI.TileJobHistory.PNG)

Die Kachel zeigt die Anzahl Jobs an, die ausgeführt wurden, z. B. zeigt das vorherige Bild, dass der Jobverlauf für sechs Jobs verfügbar ist. Die Seite mit dem Jobverlauf sieht wie folgt aus:

![HDI.JobHistoryPage](./media/hdinsight-monitor/HDI.JobHistoryPage.PNG)

Weitere Informationen
---------------------

-   [Gewusst wie: Verwalten von HDInsight](/en-us/manage/services/hdinsight/howto-administer-hdinsight/)
-   [Gewusst wie: Programmgesteuertes Bereitstellen eines HDInsight-Clusters](/en-us/manage/services/hdinsight/howto-deploy-cluster/)
-   [Gewusst wie: Programmgesteuertes Ausführen von Remote-Jobs für Ihren HDInsight-Cluster](/en-us/manage/services/hdinsight/howto-execute-jobs-programmatically/)
-   [Lernprogramm: Erste Schritte mit Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)

