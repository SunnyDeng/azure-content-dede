<properties
	pageTitle="Analysieren von Sensordaten mit Hive und Hadoop | Microsoft Azure"
	description="Erfahren Sie, wie Sie Sensordaten mithilfe der Hive-Abfragekonsole in HDInsight (Hadoop) analysieren und die Daten anschließend in Microsoft Excel mit Power View visualisieren können."
	services="hdinsight"
	documentationCenter=""
	authors="Blackmist"
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/06/2015" 
	ms.author="larryfr"/>

#Analysieren von Sensordaten mit der Hive-Abfragekonsole für Hadoop in HDInsight

Erfahren Sie, wie Sie Sensordaten mithilfe der Hive-Abfragekonsole in HDInsight (Hadoop) analysieren und die Daten anschließend in Microsoft Excel mithilfe von Power View visualisieren können.

> [AZURE.NOTE]Die Schritte in diesem Dokument funktionieren nur mit einem Windows-basierten HDInsight-Cluster.

In diesem Beispiel verarbeiten Sie Verlaufsdaten von Klimaanlagensystemen mithilfe von HDInsight, um herauszufinden, welche Systeme nicht in der Lage sind, eine eingestellte Temperatur zu halten. Sie lernen Folgendes:

- Erstellen von HIVE-Tabellen zum Abfragen von Daten in CSV-Dateien
- Erstellen von HIVE-Abfragen zum Analysieren der Daten
- Verwenden von Microsoft Excel zum Herstellen einer Verbindung mit HDInsight (über eine offene Datenbankverbindung (ODBC)), um die analysierten Daten abzurufen
- Verwenden von Power View zum Visualisieren der Daten

![Ein Diagramm der Lösungsarchitektur](./media/hdinsight-hive-analyze-sensor-data/hvac-architecture.png)

##Voraussetzungen

* Ein HDInsight-Cluster (Hadoop): Unter [Bereitstellen von Hadoop-Clustern in HDInsight](hdinsight-provision-clusters.md) finden Sie weitere Informationen zur Erstellung von Clustern.

* Microsoft Excel 2013 (PowerPivot für Datenanalysten – Microsoft Excel 2010)

	> [AZURE.NOTE]Microsoft Excel dient zur Visualisierung von Daten mithilfe von [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US).

* [Microsoft Hive ODBC-Treiber](http://www.microsoft.com/download/details.aspx?id=40886)

##So führen Sie das Beispiel aus

1. Klicken Sie im Azure-Portal auf den Cluster, auf dem Sie das Beispiel ausführen möchten, und klicken Sie anschließend unten in der Seite auf **Abfragekonsole**. Alternativ können Sie die Abfragekonsole direkt über die folgende URL öffnen:

	 	https://<clustername>.azurehdinsight.net

	Geben Sie den Benutzernamen und das Kennwort des Administrators ein, den Sie bei der Clusterbereitstellung verwendet haben, wenn Sie dazu aufgefordert werden.

2. Klicken Sie auf der Webseite, die sich daraufhin öffnet, auf die Registerkarte **Erste Schritte mit dem Katalog ** und anschließend in der Kategorie **Lösungen mit Beispieldaten** auf das Beispiel **Analysieren von Sensordaten**.

3. Folgen Sie den Anweisungen auf der Webseite, um das Beispiel abzuschließen.

<!---HONumber=July15_HO4-->