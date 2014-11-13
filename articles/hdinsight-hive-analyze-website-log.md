<properties linkid="manage-services-hdinsight-howto-hive" urlDisplayName="Use Hive in HDInsight Hadoop for website log analysis" pageTitle="Verwenden von Hive in HDInsight Hadoop f&uuml;r die Analyse von Website-Protokollen | Azure" metaKeywords="" description="Erfahren Sie, wie Sie Website-Protokolle mit Hive in HDInsight analysieren k&ouml;nnen. Sie verwenden eine Protokolldatei als Eingabe f&uuml;r eine HDInsight-Tabelle und verwenden anschlie&szlig;end HiveQL, um die Daten abzufragen." metaCanonical="" services="hdinsight" documentationCenter="" title="Verwenden von Hive in HDInsight Hadoop f&uuml;r die Analyse von Website-Protokollen" authors="nitinme" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/14/2014" ms.author="nitinme" />

# Verwenden von Hive in HDInsight zum Analysieren von Website-Protokollen

Erfahren Sie, wie Sie Website-Protokolle mit HiveQL in HDInsight analysieren können. Mit der Analyse von Website-Protokollen können Sie Ihr Publikum nach ähnlichen Aktivitäten gruppieren, Seitenbesucher nach demografischen Daten kategorisieren, die angezeigten Inhalte und die Ursprungsseiten herausfinden und vieles mehr.

In diesem Beispiel analysieren Sie Website-Protokolle in einem HDInsight-Cluster, um Einblicke in die Häufigkeit von Besuchen pro Tag von externen Webseiten sowie eine Zusammenfassung von Website-Fehlern zu erhalten, die den Benutzern angezeigt werden. Sie erhalten Informationen zu folgenden Themen:

-   Verbinden mit einem Azure-Speicherblob, der Protokolldateien einer Website enthält
-   Erstellen von HIVE-Tabellen zum Abfragen dieser Protokolle
-   Erstellen von HIVE-Abfragen zum Analysieren der Daten
-   Verbinden von Microsoft Excel mit HDInsight (über eine ODBC-Verbindung), um die analysierten Daten abzurufen

![HDI.Samples.Website.Log.Analysis][HDI.Samples.Website.Log.Analysis]

## Voraussetzungen

-   Sie benötigen einen bereitgestellten **HDInsight-Cluster**. Weitere Informationen finden Sie unter [Bereitstellen eines HDInsight-Clusters][Bereitstellen eines HDInsight-Clusters].
-   Sie benötigen Microsoft Excel 2010 oder Microsoft Excel 2013.
-   Sie benötigen den [Microsoft Hive ODBC-Treiber][Microsoft Hive ODBC-Treiber], um die Daten aus Hive in Excel zu importieren.

## Ausführen des Beispiels

1.  Klicken Sie im Azure-Verwaltungsportal auf den Namen des entsprechenden Clusters und klicken Sie anschließend unten in der Seite auf **Abfragekonsole**. Alternativ können Sie die Abfragekonsole direkt über die folgende URL öffnen:

        https://<clustername>.azurehdinsight.net

    Geben Sie den Benutzername und Kennwort des Administrators ein, den Sie bei der Clustererstellung verwendet haben, wenn Sie dazu aufgefordert werden.

2.  Klicken Sie in der Webseite, die sich daraufhin öffnet, auf die Registerkarte **Erste Schritte** und anschließend in der Kategorie **Beispiele** auf das Beispiel **Analyse von Website-Protokollen**.
3.  Folgen Sie den Anweisungen auf der Webseite, um das Beispiel abzuschließen.

## Nächste Schritte

Probieren Sie das Beispiel zum Analysieren von Sensordaten mit Azure HDInsight aus. Siehe [Analysieren von Sensordaten mit Hive und HDInsight][Analysieren von Sensordaten mit Hive und HDInsight].

  [HDI.Samples.Website.Log.Analysis]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
  [Bereitstellen eines HDInsight-Clusters]: ../hdinsight-provision-clusters/
  [Microsoft Hive ODBC-Treiber]: http://www.microsoft.com/de-de/download/details.aspx?id=40886
  [Analysieren von Sensordaten mit Hive und HDInsight]: ../hdinsight-use-hive-sensor-data-analysis/
