<properties 
	pageTitle="Verwenden von Hive mit Hadoop für Analysen von Websiteprotokollen | Microsoft Azure" 
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
	ms.date="07/11/2015" 
	ms.author="nitinme"/>

# Verwenden von Hive in HDInsight zum Analysieren von Website-Protokollen

Erfahren Sie, wie Sie Website-Protokolle mit HiveQL in HDInsight analysieren können. Mit der Analyse von Websiteprotokollen können Sie Ihr Publikum nach ähnlichen Aktivitäten gruppieren, Seitenbesucher nach demografischen Daten kategorisieren, die angezeigten Inhalte und die Ursprungsseiten ermitteln und vieles mehr.

In diesem Beispiel analysieren Sie Websiteprotokolle in einem HDInsight-Cluster, um Einblicke in die Häufigkeit von Besuchen der Website von externen Websites pro Tag zu erhalten. Sie generieren zudem eine Zusammenfassung der Websitefehler, die bei den Benutzern auftreten. Sie lernen Folgendes:

- Herstellen einer Verbindung mit einem Azure-Blobspeicher, der Protokolldateien von Websites enthält
- Erstellen von HIVE-Tabellen zum Abfragen dieser Protokolle
- Erstellen von HIVE-Abfragen zum Analysieren der Daten
- Verwenden von Microsoft Excel zum Herstellen einer Verbindung mit HDInsight (über eine offene Datenbankverbindung (ODBC)), um die analysierten Daten abzurufen

![HDI.Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##Voraussetzungen

- Sie müssen einen Hadoop-Cluster unter Azure HDInsight bereitgestellt haben. Weitere Informationen finden Sie unter [Bereitstellen eines HDInsight-Clusters][hdinsight-provision]. 
- Microsoft Excel 2013 oder Excel 2010 muss installiert sein.
- Sie benötigen den [Microsoft Hive ODBC-Treiber](http://www.microsoft.com/download/details.aspx?id=40886), um die Daten aus Hive in Excel zu importieren.


##So führen Sie das Beispiel aus

1. Klicken Sie im Azure-Portal auf den Cluster, auf dem Sie das Beispiel ausführen möchten, und klicken Sie anschließend unten auf der Seite auf **Abfragekonsole**. Alternativ können Sie die Abfragekonsole direkt über die folgende URL öffnen:

	 	https://<clustername>.azurehdinsight.net
	
	Geben Sie den Benutzernamen und das Kennwort des Administrators ein, den Sie bei der Clusterbereitstellung verwendet haben, wenn Sie dazu aufgefordert werden.
  
2. Klicken Sie in der Webseite, die sich daraufhin öffnet, auf die Registerkarte **Erste Schritte** und anschließend in der Kategorie **Beispiele** auf das Beispiel **Analyse von Website-Protokollen**.

3. Folgen Sie den Anweisungen auf der Webseite, um das Beispiel abzuschließen.

##Nächste Schritte
Versuchen Sie es mit dem folgenden Beispiel: [Analysieren von Sensordaten mit Hive mit HDInsight](hdinsight-hive-analyze-sensor-data.md).


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: ../hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png
 

<!---HONumber=August15_HO6-->