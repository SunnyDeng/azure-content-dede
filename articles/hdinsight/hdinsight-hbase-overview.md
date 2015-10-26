<properties
	pageTitle="Was ist HBase in HDInsight | Microsoft Azure"
	description="Eine Einführung in Apache HBase in HDInsight, ein NoSQL-Datenbankbuild auf Hadoop. Erfahren Sie mehr über Anwendungsfälle, und vergleichen Sie HBase mit anderen Hadoop-Clustern."
	keywords="bigtable,nosql,what is hbase"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian" 
	manager="paulettm"
	editor="cgronlun"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="09/17/2015"
	ms.author="jgao"/>



# Überblick über HBase in HDInsight: Eine NoSQL-Datenbank, die BigTable-ähnliche Funktionen für Hadoop bereitstellt

Apache HBase ist eine Open-Source-NoSQL-Datenbank, die auf Hadoop basiert und nach dem Vorbild von Google BigTable erstellt wurde. HBase bietet wahlfreien Zugriff und starke Konsistenz für große Mengen unstrukturierter und teilstrukturierter Daten in einer schemalosen Datenbank, die nach Spaltenfamilien gegliedert ist.

Daten werden in den Zeilen einer Tabelle gespeichert und die Daten in einer Zeile zu einer Spaltenfamilie zusammengefasst. HBase ist eine schemalose Datenbank in dem Sinne, dass weder die Spalten noch der Typ der darin gespeicherten Daten vor der Verwendung definiert werden müssen. Der Open-Source-Code lässt sich linear skalieren, sodass Petabytes von Daten auf Tausenden von Knoten verarbeitet werden können. HBase nutzt Datenredundanz, Stapelverarbeitung und andere Funktionen, die von verteilten Anwendungen im Hadoop-Ökosystem zur Verfügung gestellt werden.

## Wie wird HBase in Azure HDInsight implementiert?

HDInsight HBase wird als verwalteter Cluster angeboten, der in die Azure-Umgebung integriert ist. Die Cluster sind so konfiguriert, dass Daten direkt im Azure-Blobspeicher gespeichert werden. Dies sorgt für geringe Latenz und mehr Flexibilität bei Entscheidungen bezüglich Leistung und Kosten. So können Kunden interaktive Websites erstellen, die mit großen Datensätzen arbeiten, und Dienste entwickeln, die Sensor- und Telemetriedaten aus Millionen von Endpunkten speichern. Diese Daten können dann mit Hadoop-Jobs analysiert werden. HBase und Hadoop sind gute Ausgangspunkte für Big-Data-Projekte in Azure – sie ermöglichen besonders in Echtzeitanwendungen die Verarbeitung großer Datensätze.

Die HDInsight-Implementierung nutzt die skalierbare Architektur von HBase für automatische Partitionierung von Tabellen, starke Konsistenz für Lese- und Schreibvorgänge sowie automatisches Failover. Die Leistung wird durch speicherinterne Zwischenspeicherung für Lesevorgänge und Schreibvorgänge mit hohem Durchsatz optimiert. Bereitstellung in einem virtuellen Netzwerk ist für HDInsight HBase ebenfalls verfügbar. Details hierzu finden Sie unter [Bereitstellen von HDInsight-Clustern im virtuellen Azure-Netzwerk][hbase-provision-vnet].


## Wie werden Daten in HDInsight HBase verwaltet?

Daten können Sie in HBase mit den Befehlen `create`, `get`, `put` und `scan` über die HBase-Shell verwalten. Daten werden mit dem Befehl `put` in die Datenbank geschrieben und mit `get` gelesen. Der `scan`-Befehl wird verwendet, um Daten aus mehreren Zeilen in einer Tabelle abzurufen. Sie können Daten auch über die HBase-C#-API verwalten. Diese bietet eine Clientbibliothek auf der HBase-REST-API. Eine HBase-Datenbank können Sie auch mit Hive abfragen. Eine Einleitung für diese Programmiermodelle finden Sie unter [Erste Schritte mit HBase mit Hadoop in HDInsight][hbase-get-started]. Coprozessoren sind ebenfalls verfügbar und ermöglichen die Verarbeitung von Daten in den Knoten, die die Datenbank hosten.


## Szenarios: Anwendungsfälle für HBase
Der kanonische Anwendungsfall, für den BigTable (und daher auch HBase) erstellt wurde, ist die Websuche. Suchmaschinen erstellen Indizes, die Begriffe den Webseiten zuordnen, die diese Begriffe enthalten. Es gibt jedoch noch viele weitere Anwendungsfälle für HBase. Einige von ihnen werden in diesem Abschnitt beschrieben.

- Schlüssel-Wert-Speicherung

	HBase kann für die Schlüssel-Wert-Speicherung verwendet werden und ist für die Verwaltung von Nachrichtensystemen geeignet. Facebook nutzt HBase für sein Nachrichtensystem. HBase ist ideal für die Speicherung und Verwaltung von Internetkommunikation. WebTable nutzt HBase, um Tabellen, die aus Webseiten extrahiert wurden, zu suchen und zu verwalten.

- Sensordaten

	HBase kann für das Erfassen von Daten verwendet werden, die schrittweise aus verschiedenen Quellen gesammelt werden. Dies umfasst Analysen sozialer Netzwerke, Zeitreihen, Gewährleistung der Aktualität interaktiver Dashboards mit Trends und Indikatoren sowie die Verwaltung von Prüfungsprotokollierungssystemen. Zu den Beispielen zählen das Bloomberg Trader Terminal und die Open Time Series Database (OpenTSDB), die Metriken speichert, die zum Status von Serversystemen gesammelt wurden, und den Zugriff darauf ermöglicht.

- Echtzeitabfrage

	[Phoenix](http://phoenix.apache.org/) ist ein SQL-Abfragemodul für Apache HBase. Der Zugriff erfolgt als JDBC-Treiber. So können Sie HBase-Tabellen mit SQL abfragen und verwalten.

- HBase als Plattform

	Anwendungen können auf HBase ausgeführt werden, indem sie HBase als Datenspeicher nutzen. Beispiele hierfür sind Phoenix, OpenTSDB, Kiji und Titan. Es ist auch ein Integration von Anwendungen mit HBase möglich. Beispiele hierfür sind Hive, Pig, Solr, Storm, Flume, Impala, Spark, Ganglia und Drill.


##<a name="next-steps"></a>Nächste Schritte

- [Erste Schritte mit HBase mit Hadoop in HDInsight][hbase-get-started]
- [Bereitstellen von HDInsight-Clustern in Azure Virtual Network][hbase-provision-vnet]
- [Konfigurieren der HBase-Replikation in HDInsight](hdinsight-hbase-geo-replication.md)
- [Analysieren der Twitter-Stimmungen mit HBase in HDInsight][hbase-twitter-sentiment]
- [Verwenden von Maven zur Entwicklung von Java-Anwendungen, die HBase mit HDInsight (Hadoop) nutzen][hbase-build-java-maven]

##<a name="see-also"></a>Weitere Informationen

- [Apache HBase](https://hbase.apache.org/)
- [Bigtable: A Distributed Storage System for Structured Data (in englischer Sprache)](http://research.google.com/archive/bigtable.html)




[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: ../hdinsight-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://portal.azure.com/
[azure-create-storageaccount]: ../storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/

<!---HONumber=Oct15_HO3-->