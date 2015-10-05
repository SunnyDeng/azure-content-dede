<properties
 pageTitle="Apache Storm-Beispieltopologien in HDInsight | Microsoft Azure"
 description="Eine Liste von Beispieltopologien, die mit Apache Storm in HDInsight erstellt und getestet wurden, einschließlich der grundlegenden C#- und Java-Topologien, und mit Event Hubs funktionieren."
 services="hdinsight"
 documentationCenter=""
 authors="Blackmist"
 manager="paulettm"
 editor="cgronlun"
	tags="azure-portal"/>

<tags
 ms.service="hdinsight"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="big-data"
 ms.date="09/23/2015"
 ms.author="larryfr"/>

# Beispiel-Storm-Topologien und -Komponenten für Apache Storm auf HDInsight

Es folgt eine Liste von Beispielen zur Verwendung mit Apache Storm in HDInsight, die von Microsoft erstellt und verwaltet wird. Diese Beispiele decken eine Vielzahl von Themen ab, angefangen bei grundlegenden C# und Java-Topologien bis hin zur Arbeit mit Azure-Diensten wie Event Hubs, DocumentDB, Power BI, SQL-Datenbank, HBase auf HDInsight und Azure-Speicher. Einige Beispiele demonstrieren außerdem die Arbeit mit nicht-Azure oder sogar nicht-Microsoft-Technologien, wie SignalR und Socket.IO

| Beschreibung | Zeigt | Sprache/Framework |
|:--------------------------------------------------------------------------------------------------------|:-----------------------------------------------------|:---------------------------|
| [Event Hub-Spout und -Bolt](https://github.com/apache/storm/tree/master/external/storm-eventhubs) | Quelle für Event Hub-Spout und -Bolt | Java |
| [Entwickeln von Java-basierten Topologien für Apache Storm in HDInsight][5797064f] | Maven | Java |
| [Entwickeln von C#-Topologien für Apache Storm in HDInsight mithilfe von Visual Studio][16fce2d1] | HDInsight Tools für Visual Studio | C#, Java |
| [Erstellen Sie mehrerer Datenströme in einer C#-Storm-Topologie][ec5a4064] | Mehrere Datenströme | C# |
| [Beliebte Twitter-Themen mit Apache Storm in HDInsight ermitteln][3c86c7c8] | Trident | Java, Trident |
| [Verarbeitung von Ereignissen von Azure-Event-Hubs mit Storm auf HDInsight][844d1d81] | Event Hubs | C# und Java |
| [Verwenden Sie zur Visualisierung von Daten aus einer Storm-Topologie Power Bi (Vorschau)][94d15238] | Power BI | C# |
| [Analysieren von Sensordaten mit Storm und HBase in HDInsight][ab894747] | Event Hubs, HBase, Socket.IO, Web-Dashboard | C#, Java, JavaScript, HTML |
| [Verarbeitung von Fahrzeugsensordaten von Event Hubs mit Storm auf HDInsight][246ee964] | Event Hubs, DocumentDb, Azure-Speicher-Blob (WASB) | C#, Java |
| [Extrahieren, Transformieren und Laden (ETL) von Azure- Event Hubs auf HBase mit Storm in HDInsight][b4b68194] | Event Hubs, HBase | C# |
| [C#-Storm-Topologie-Vorlagenprojekt für die Arbeit mit Azure-Diensten über Storm auf HDInsight][ce0c02a2] | Event Hubs, DocumentDb, SQL-Datenbank, HBase, SignalR | C#, Java |
| [Skalierbarkeits-Benchmarks für das Lesen von Azure-Event Hubs mit Storm auf HDInsight][d6c540e3] | Nachrichtendurchsatz, Event Hubs, SQL-Datenbank | C#, Java |
| [Zuordnen von Ereignissen mithilfe von Storm und HBase auf HDInsight](hdinsight-storm-correlation-topology.md) | HBase | C# |

## Nächste Schritte

* [Erste Schritte mit Apache Storm in HDInsight][2b8c3488]

* [Erfahren Sie, wie Sie Storm-Topologien mit Storm auf HDInsight bereitstellen und verwalten ][6eb0d3b8]

  [2b8c3488]: ../hdinsight-storm-getting-started.md "Erfahren Sie, wie sie Storm auf HDInsight-Cluster erstellen und das Storm-Dashboard zum Bereitstellen von Beispiel-Topologien verwenden."
  [6eb0d3b8]: hdinsight-storm-deploy-monitor-topology.md "Erfahren Sie, wie Sie Topologien mit dem webbasierten Storm-Dashboard und der Storm-Benutzeroberfläche oder die HDInsight-Tools für Visual Studio bereitstellen und verwalten."
  [16fce2d1]: hdinsight-storm-develop-csharp-visual-studio-topology.md "Erfahren Sie, wie Sie C#-Storm-Topologien mithilfe der HDInsight Tools für Visual Studio erstellen."
  [5797064f]: hdinsight-storm-develop-java-topology.md "Erfahren Sie, wie Sie Storm-Topologien in Java mit Maven erstellen, indem Sie eine Grundtopologie zur Wortzählung erstellen."
  [94d15238]: hdinsight-storm-power-bi-topology.md "Zeigt das Schreiben von Daten in Power BI aus einer C#-Topologie und das Erstellen eines Diagramm und eines Dashboards aus den Daten."
  [ec5a4064]: https://github.com/Blackmist/csharp-storm-example "Zeigt eine Storm-Grundtopologie, die eine Wortzählung ausführt, die in C# implementiert wird. Außerdem wird das Erstellen mehrerer Datenströme in einer C#-Topologie gezeigt."
  [844d1d81]: hdinsight-storm-develop-csharp-event-hub-topology.md "Informationen Sie zum Lesen und Schreiben von Daten aus Azure-Event Hubs mit Storm in HDInsight."
  [ab894747]: hdinsight-storm-sensor-data-analysis.md "Erfahren Sie, wie Sie Apache Storm auf HDInsight zur Verarbeitung von Sensordaten von Azure Event Hubs verwenden, D3.js darstellen und (optional) HBase speichern."
  [3c86c7c8]: hdinsight-storm-twitter-trending.md "Erfahren Sie, wie Sie mit Trident eine Storm-Topologie erstellen, die beliebte Twitter-Themen auf Grundlage von Hashtags ermittelt."
  [246ee964]: hdinsight-storm-iot-eventhub-documentdb.md "Erfahren Sie, wie Sie eine Storm-Topologie verwenden, um Azure Event Hubs-Nachrichten und Dokumente von Azure-DocumentDB zur Datenreferenzierung zu lesen sowie Daten im Azure-Speicher abspeichern."
  [d6c540e3]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/EventCountExample "Mehrere Topologien veranschaulichen den Durchsatz beim Lesen von Azure Event Hubs und das Speichern in einer SQL-Datenbank unter Verwendung von Apache Storm in HDInsight."
  [b4b68194]: https://github.com/hdinsight/hdinsight-storm-examples/blob/master/RealTimeETLExample "Erfahren Sie, wie Sie Daten aus Azure Event Hubs lesen, Daten aggregieren und transformieren und dann sie HBase auf HDInsight speichern."
  [ce0c02a2]: https://github.com/hdinsight/hdinsight-storm-examples/tree/master/templates/HDInsightStormExamples "Dieses Projekt enthält Vorlagen für Spouts, Bolts und Topologien für die Interaktion mit verschiedenen Azure-Diensten wie Event Hubs, DocumentDB und SQL-Datenbank."
 

<!---HONumber=Sept15_HO4-->