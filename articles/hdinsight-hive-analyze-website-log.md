<properties 
	pageTitle="Verwenden von Hive in HDInsight Hadoop für die Analyse von Website-Protokollen | Azure" 
	description="Erfahren Sie, wie Sie Website-Protokolle mit Hive in HDInsight analysieren können. Sie verwenden eine Protokolldatei als Eingabe für eine HDInsight-Tabelle und verwenden anschließend HiveQL, um die Daten abzufragen." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/06/2015" 
	ms.author="nitinme"/>

# Verwenden von Hive in HDInsight zum Analysieren von Website-Protokollen

Erfahren Sie, wie Sie Website-Protokolle mit HiveQL in HDInsight analysieren können. Mit der Analyse von Website-Protokollen können Sie Ihr Publikum nach ähnlichen Aktivitäten gruppieren, Seitenbesucher nach demografischen Daten kategorisieren, die angezeigten Inhalte und die Ursprungsseiten herausfinden und vieles mehr.

In diesem Beispiel analysieren Sie Website-Protokolle in einem HDInsight-Cluster, um Einblicke in die Häufigkeit von Besuchen pro Tag von externen Webseiten sowie eine Zusammenfassung von Website-Fehlern zu erhalten, die den Benutzern angezeigt werden. Sie erhalten Informationen zu folgenden Themen:

- Verbinden mit einem Azure-Speicherblob, der Protokolldateien einer Website enthält
- Erstellen von HIVE-Tabellen zum Abfragen dieser Protokolle
- Erstellen von HIVE-Abfragen zum Analysieren der Daten
- Verbinden von Microsoft Excel mit HDInsight (über eine ODBC-Verbindung), um die analysierten Daten abzurufen

![HDI.Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

## Voraussetzungen

- Sie benötigen einen bereitgestellten **HDInsight-Cluster**. Anweisungen finden Sie unter [Bereitstellen von HDInsight-Clustern][hdinsight-provision]. 
- Sie benötigen Microsoft Excel 2010 oder Microsoft Excel 2013.
- Sie benötigen den [Microsoft Hive ODBC Driver](http://www.microsoft.com/de-de/download/details.aspx?id=40886), um die Daten aus Hive in Excel zu importieren.


## So führen Sie das Beispiel aus

1. Klicken Sie im Azure-Verwaltungsportal auf den Namen des entsprechenden Clusters, und klicken Sie anschließend unten in der Seite auf **Abfragekonsole**. Alternativ können Sie die Abfragekonsole direkt über die folgende URL öffnen:

	 	https://<clustername>.azurehdinsight.net
	
	Geben Sie den Benutzername und Kennwort des Administrators ein, den Sie bei der Clustererstellung verwendet haben, wenn Sie dazu aufgefordert werden.
  
2. Klicken Sie in der Webseite, die sich daraufhin öffnet, auf die Registerkarte **Erste Schritte** und anschließend in der Kategorie **Beispiele** auf das Beispiel **Analyse von Websiteprotokollen**.
3. Folgen Sie den Anweisungen auf der Webseite, um das Beispiel abzuschließen.

## Nächste Schritte
Probieren Sie das Beispiel zum Analysieren von Sensordaten mit Azure HDInsight aus. Siehe [Analysieren von Sensordaten mit Hive und HDInsight][hdinsight-sensor-data-sample].


[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis/

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
<!--HONumber=45--> 
