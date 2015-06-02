<properties 
	pageTitle="Analysieren von Sensordaten mit Hive und Hadoop | Microsoft Azure" 
	description="Erfahren Sie, wie Sie Sensordaten mithilfe der Hive-Abfragekonsole in HDInsight (Hadoop) analysieren und die Daten anschließend in Microsoft Excel mithilfe von Power View visualisieren." 
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
	ms.date="02/20/2015" 
	ms.author="larryfr"/>

#Analysieren von Sensordaten mit der Hive-Abfragekonsole auf Hadoop in HDInsight

Erfahren Sie, wie Sie Sensordaten mithilfe der Hive-Abfragekonsole in HDInsight (Hadoop) analysieren und die Daten anschließend in Microsoft Excel mithilfe von Power View visualisieren.

> [AZURE.NOTE]Die Schritte in diesem Dokument gelten nur für Windows-basierte HDInsight-Cluster.

In diesem Beispiel verarbeiten Sie Verlaufsdaten von Klimaanlagensystemen mithilfe von HDInsight, um herauszufinden, welche Systeme nicht in der Lage sind, eine eingestellte Temperatur zu halten. Sie erhalten Informationen zu folgenden Themen:

- Erstellen von HIVE-Tabellen zum Abfragen von Daten in CSV-Dateien
- Erstellen von HIVE-Abfragen zum Analysieren der Daten
- Verbinden von Microsoft Excel mit HDInsight (über eine ODBC-Verbindung, Open Database Connectivity) zum Abrufen der analysierten Daten
- Verwenden von Power View zum Visualisieren der Daten

![Ein Diagramm der Lösungsarchitektur](./media/hdinsight-use-hive-sensor-data-analysis/hvac-architecture.png)

##Voraussetzungen

* Ein HDInsight (Hadoop)-Cluster – unter [Bereitstellen von Hadoop-Clustern in HDInsight](hdinsight-provision-clusters.md) finden Sie weitere Informationen zum Erstellen von Clustern

* Microsoft Excel 2013 (PowerPivot für Datenanalysten – Microsoft Excel 2010)

	> [AZURE.NOTE]Microsoft Excel dient zur Visualisierung der Daten mithilfe von [Power View](https://support.office.com/Article/Power-View-Explore-visualize-and-present-your-data-98268d31-97e2-42aa-a52b-a68cf460472e?ui=en-US&rs=en-US&ad=US)

* [Microsoft Hive ODBC-Treiber](http://www.microsoft.com/download/details.aspx?id=40886)

##Ausführen des Beispiels

1. Klicken Sie im Azure-Portal auf den Cluster, auf dem Sie das Beispiel ausführen möchten, und klicken Sie anschließend unten auf **Query Console**. Sie können die Abfragekonsole auch direkt über die folgende URL öffnen:

	 	https://<clustername>.azurehdinsight.net

	Authentifizieren Sie sich, wenn Sie dazu aufgefordert werden, mithilfe des Benutzernamens und des Kennworts des Administrators, den Sie bei der Bereitstellung dieses Clusters verwendet haben.

2. Klicken Sie auf der daraufhin geöffneten Webseite auf die Registerkarte **Getting Started Gallery** und anschließend in der Kategorie **Solutions with Sample Data** auf das Beispiel **Sensor Data Analysis**.

3. Folgen Sie den Anweisungen auf der Webseite, um das Beispiel abzuschließen.

<!--HONumber=54-->