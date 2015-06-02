<properties 
	pageTitle="Verwenden von Hive in HDInsight Hadoop für die Analyse von Website-Protokollen | Microsoft Azure" 
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

In diesem Beispiel verwenden Sie einen HDInsight-Cluster zur Analyse von Website-Protokolldateien, um Einblicke in die Häufigkeit von Besuchen pro Tag von externen Webseiten zu erhalten. Sie generieren außerdem eine Zusammenfassung der Website-Fehler, die bei den Benutzern auftreten. Sie erhalten Informationen zu folgenden Themen:

- Herstellen einer Verbindung mit einem Azure-Blob-Speicher mit den Protokolldateien für die Website
- Erstellen von HIVE-Tabellen zum Abfragen dieser Protokolle
- Erstellen von HIVE-Abfragen zum Analysieren der Daten
- Verbinden von Microsoft Excel mit HDInsight (über eine ODBC-Verbindung, Open Database Connectivity) zum Abrufen der analysierten Daten

![HDI.Samples.Website.Log.Analysis][img-hdi-weblogs-sample]

##Voraussetzungen

- Sie müssen ein **HDInsight-Cluster** bereitgestellt haben. Weitere Informationen finden Sie unter [Bereitstellen eines HDInsight-Clusters][hdinsight-provision]. 
- Sie benötigen Microsoft Excel 2013 oder Excel 2010.
- Sie benötigen den [Microsoft Hive ODBC-Treiber](http://www.microsoft.com/download/details.aspx?id=40886), um die Daten aus Hive in Excel zu importieren.


##Ausführen des Beispiels

1. Klicken Sie im Azure-Portal auf den Cluster, auf dem Sie das Beispiel ausführen möchten, und klicken Sie anschließend unten auf **Query Console**. Sie können die Abfragekonsole auch direkt über die folgende URL öffnen:

	 	https://<clustername>.azurehdinsight.net
	
	Authentifizieren Sie sich, wenn Sie dazu aufgefordert werden, mithilfe des Benutzernamens und des Kennworts des Administrators, den Sie bei der Bereitstellung dieses Clusters verwendet haben.
  
2. Klicken Sie in der Webseite, die sich daraufhin öffnet, auf die Registerkarte **Erste Schritte** und anschließend in der Kategorie **Beispiele** auf das Beispiel **Analyse von Website-Protokollen**.

3. Folgen Sie den Anweisungen auf der Webseite, um das Beispiel abzuschließen.

##Nächste Schritte
Probieren Sie das folgende Beispiel aus: [Analysieren von Sensordaten mit Hive und HDInsight](hdinsight-hive-analyze-sensor-data.md).


[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-sensor-data-sample]: hdinsight-use-hive-sensor-data-analysis.md

[img-hdi-weblogs-sample]: ./media/hdinsight-hive-analyze-website-log/hdinsight-weblogs-sample.png

<!--HONumber=54-->