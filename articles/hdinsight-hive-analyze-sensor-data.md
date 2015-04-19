<properties 
	pageTitle="Analysieren von Sensordaten mit Hive und Microsoft Azure HDInsight (Hadoop)" 
	description="Erfahren Sie, wie Sie Sensordaten mit Hive und HDInsight (Hadoop) in Echtzeit analysieren und visualisieren können" 
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
	ms.date="09/30/2014" 
	ms.author="larryfr"/>

# Analysieren von Sensordaten mit Hive und HDInsight

Erfahren Sie, wie Sie Sensordaten mithilfe von Hive in HDInsight (Hadoop) analysieren und die Daten anschließend in Microsoft Excel mithilfe von Power View visualisieren können.

In diesem Beispiel verarbeiten Sie Verlaufsdaten von Klimaanlagensystemen mithilfe von HDInsight, um herauszufinden, welche Systeme nicht in der Lage sind, eine eingestellte Temperatur zu halten. Sie erhalten Informationen zu folgenden Themen:

- Erstellen von HIVE-Tabellen zum Abfragen von Daten in CSV-Dateien
- Erstellen von HIVE-Abfragen zum Analysieren der Daten
- Verbinden von Microsoft Excel mit HDInsight (über eine ODBC-Verbindung), um die analysierten Daten abzurufen
- Verwenden von Power View zum Visualisieren der Daten

![A diagram of the solution architecture](./media/hdinsight-use-hive-sensor-data-analysis/hvac-architecture.png)

## Voraussetzungen:

* Ein HDInsight (Hadoop)-Cluster - Unter [Bereitstellen von Hadoop-Clustern in HDInsight](hdinsight-provision-clusters.md) finden Sie weitere Informationen zur Erstellung von Clustern.

* Microsoft Excel 2013 (PowerPivot für Datenanalysten - Microsoft Excel 2010)

	> [AZURE.NOTE] Microsoft Excel wird zur Datenvisualisierung mit [Power View](https://support.office.com/en-US/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US) verwendet, das momentan nur für Windows verfügbar ist.

* [Microsoft Hive ODBC Driver](http://www.microsoft.com/de-de/download/details.aspx?id=40886)

## So führen Sie das Beispiel aus

1. Klicken Sie im Azure-Verwaltungsportal auf den Namen des entsprechenden Clusters, und klicken Sie anschließend unten in der Seite auf **Abfragekonsole**. Alternativ können Sie die Abfragekonsole direkt über die folgende URL öffnen

	 	https://<clustername>.azurehdinsight.net

	Geben Sie den Benutzername und Kennwort des Administrators ein, den Sie bei der Clustererstellung verwendet haben, wenn Sie dazu aufgefordert werden.

2. Klicken Sie in der Webseite, die sich daraufhin öffnet, auf die Registerkarte **Erste Schritte** und anschließend in der Kategorie **Beispiele** auf das Beispiel **Analyse von Websiteprotokollen**.

3. Folgen Sie den Anweisungen auf der Webseite, um das Beispiel abzuschließen.

<!--HONumber=45--> 
