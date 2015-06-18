<properties 
	pageTitle="Überblick über HBase in HDInsight | Microsoft Azure" 
	description="Einführung in Apache HBase in HDInsight, eine auf Hadoop basierende NoSQL-Datenbank. Informationen zu dieser Technologie für Big Data und zu Anwendungsfällen sowie Vergleich mit anderen Hadoop-Clustern." 
	keywords="bigtable,nosql,what is hbase"
	services="hdinsight" 
	documentationCenter="" 
	authors="mumian" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="jgao"/>



# Überblick über HBase in HDInsight: Eine NoSQL-Datenbank, die BigTable-ähnliche Funktionen für Hadoop umfasst.

Apache HBase ist eine Open-Source-NoSQL-Datenbank, die auf Hadoop basiert und nach dem Vorbild von Google BigTable erstellt ist. HBase bietet wahlfreien Zugriff und starke Konsistenz für große Mengen unstrukturierter und teilstrukturierter Daten in einer schemalosen Datenbank, die nach Spaltenfamilien gegliedert ist.

Daten werden in den Zeilen einer Tabelle gespeichert und die Daten aus einer Zeile zu einer Spaltenfamilie zusammengefasst. HBase ist eine schemalose Datenbank in dem Sinne, dass weder die Spalten noch der Typ der in ihnen gespeicherten Daten vor der Verwendung definiert werden müssen. Der Open-Source-Code lässt sich linear skalieren, sodass Petabytes von Daten auf Tausenden von Knoten verarbeitet werden können. Er nutzt Datenredundanz, Stapelverarbeitung und andere Funktionen, die von verteilten Anwendungen im Hadoop-Ökosystem zur Verfügung gestellt werden.

## Wie wird HBase in Azure HDInsight implementiert?

HDInsight HBase wird als verwalteter Cluster angeboten, der in die Azure-Umgebung integriert ist. Die Cluster sind so konfiguriert, dass sie Daten direkt in den Azure-Blob-Speicher speichern. Dies sorgt für geringe Latenz und mehr Flexibilität bei Entscheidungen bezüglich Leistung und Kosten. So können Kunden interaktive Websites erstellen, die mit großen Datensätzen arbeiten, und Dienste entwickeln, die Sensor- und Telemetriedaten aus Millionen von Endpunkten speichern. Diese Daten können dann mit Hadoop-Jobs analysiert werden. HBase und Hadoop sind gute Ausgangspunkte für Big-Data-Projekte in Azure – sie ermöglichen besonders in Echtzeitanwendungen die Verarbeitung von großen Datensätzen.

Die HDInsight-Implementierung nutzt die skalierbare Architektur von HBase für automatische Partitionierung von Tabellen, starke Konsistenz für Lese- und Schreibvorgänge sowie automatisches Failover. Die Leistung wird durch speicherinterne Zwischenspeicherung für Lesevorgänge und Schreibvorgänge mit hohem Durchsatz optimiert. Bereitstellung in einem virtuellen Netzwerk ist für HDInsight HBase ebenfalls verfügbar. Details hierzu finden Sie unter [Bereitstellen von HDInsight-Clustern im virtuellen Azure-Netzwerk][hbase-provision-vnet].


## Wie werden Daten in HDInsight HBase verwaltet?

Daten können Sie in HBase mit den Befehlen `create`, `get`, `put` und `scan` über die HBase-Shell verwalten. Daten werden mit dem Befehl `put` in die Datenbank geschrieben und mit `get` gelesen. Der Befehl `scan` wird verwendet, um Daten aus mehreren Zeilen in einer Tabelle abzurufen. Sie können Daten auch über die HBase-C#-API verwalten. Diese bietet eine Clientbibliothek auf der HBase-REST-API. Eine HBase-Datenbank können Sie auch mit Hive abfragen. Eine Einleitung für diese Programmiermodelle finden Sie unter [Erste Schritte mit HBase mit Hadoop in HDInsight][hbase-get-started]. Coprozessoren sind ebenfalls verfügbar und ermöglichen die Verarbeitung von Daten in den Knoten, die die Datenbank hosten.
 

## Szenarios: Anwendungsfälle für HBase
Der kanonische Anwendungsfall, für den BigTable (und daher auch HBase) erstellt wurde, ist die Websuche. Suchmaschinen erstellen Indizes, die Begriffe den Webseiten zuordnen, die diese Begriffe enthalten. Es gibt jedoch noch viele weitere Anwendungsfälle für HBase. Einige werden in diesem Abschnitt beschrieben.

### Schlüssel-Wert-Speicherung
HBase kann für die Schlüssel-Wert-Speicherung verwendet werden und ist für die Verwaltung von Nachrichtensystemen geeignet. Facebook nutzt HBase für sein Nachrichtensystem. HBase ist ideal für die Speicherung und Verwaltung von Internetkommunikation. WebTable nutzt HBase, um Tabellen, die aus Webseiten extrahiert wurden, zu suchen und zu verwalten.

### Sensordaten
HBase kann für das Erfassen von Daten verwendet werden, die schrittweise aus verschiedenen Quellen gesammelt werden. Dies umfasst Analysen sozialer Netzwerke, Zeitreihen, Gewährleistung der Aktualität interaktiver Dashboards mit Trends und Indikatoren sowie die Verwaltung von Prüfungsprotokollierungssystemen. Beispiele umfassen das Bloomberg Trader Terminal und die Open Time Series Database (OpenTSDB), die Metriken speichert, die zum Status von Serversystemen gesammelt wurden, und den Zugriff auf sie ermöglicht.

### Echtzeitabfrage
[Phoenix](http://phoenix.apache.org/) ist ein SQL-Abfragemodul für Apache HBase. Der Zugriff erfolgt als JDBC-Treiber. So können Sie HBase-Tabellen mit SQL abfragen und verwalten.

### HBase als Plattform
Anwendungen können auf HBase ausgeführt werden und HBase als Datenspeicher nutzen. Beispiele hierfür sind Phoenix, OpenTSDB, Kiji und Titan. Es ist auch ein Integration von Anwendungen mit HBase möglich. Beispiele hierfür sind Hive, Pig, Solr, Storm, Flume, Impala, Spark, Ganglia und Drill.


##<a name="next-steps"></a>Nächste Schritte

- [Erste Schritte mit HBase mit Hadoop in HDInsight][hbase-get-started]
- [Bereitstellen von HDInsight-Clustern in Azure Virtual Network][hbase-provision-vnet]
- [Konfigurieren von HBase-Replikation in HDInsight](hdinsight-hbase-geo-replication.md) 
- [Analysieren der Twitter-Stimmungen mit HBase in HDInsight][hbase-twitter-sentiment]
- [Verwenden von Maven zur Entwicklung von Java-Anwendungen, die HBase mit HDInsight (Hadoop) nutzen][hbase-build-java-maven]

##<a name="see-also"></a>Weitere Informationen

- [Apache HBase](https://hbase.apache.org/)
- [BigTable: A Distributed Storage System for Structured Data](http://research.google.com/archive/bigtable.html) (BigTable: Ein verteiltes Speichersystem für strukturierte Daten, in englischer Sprache)




[hbase-provision-vnet]: hdinsight-hbase-provision-vnet.md

[hbase-twitter-sentiment]: hdinsight-hbase-analyze-twitter-sentiment.md

[hbase-build-java-maven]: hdinsight-hbase-build-java-maven.md

[hdinsight-use-hive]: hdinsight-use-hive.md

[hdinsight-storage]: hdinsight-use-blob-storage.md

[hbase-get-started]: http://azure.microsoft.com/documentation/articles/hdinsight-hbase-get-started/

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: storage/storage-create-storage-account.md

[apache-hadoop]: http://hadoop.apache.org/











<!--HONumber=54-->