<properties linkid="manage-services-hdinsight-hbase-overview" urlDisplayName="HDInsight HBase overview" pageTitle="An overview of HBase in HDInsight | Azure" metaKeywords="" description="An introduction to HBase in HDInsight, use-cases and a comparison with other database solutions ." metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight HBase overview" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="bradsev"></tags>

# Überblick zu HDInsight HBase

## Was ist HBase?

HBase ist eine Open-Source-NoSQL-Datenbank, basierend auf Hadoop, die wahlfreien Zugriff und starke Konsistenz für große Mengen unstrukturierter und teilstrukturierter Daten bietet. Sie wurde mit BigTable von Google als Vorbild erstellt und ist eine Datenbank, die auf Spaltenfamilien basiert. Daten werden in den Zeilen einer Tabelle gespeichert und die Daten aus einer Zeile zu einer Spaltenfamilie zusammengefasst. HBase ist eine schemalose Datenbank in dem Sinne, dass weder die Spalten noch der Typ der in ihnen gespeicherten Daten vor der Verwendung definiert werden müssen. Der Open-Source-Code wurde zuerst von Mike Cafarella im Jahr 2007 veröffentlicht. Er skaliert linear, um Petabytes von Daten auf Tausenden Knoten zu behandeln. Sie nutzt Datenredundanz, Stapelverarbeitung und andere Funktionen, die von verteilten Anwendungen im Hadoop-Ökosystem zur Verfügung gestellt werden.

## Was ist Azure HDInsight HBase?

HDInsight HBase wird als verwalteter Cluster angeboten, der in die Azure-Umgebung integriert ist. Die Cluster sind so konfiguriert, dass sie Daten direkt in den Azure-Blob-Speicher speichern. Dies sorgt für geringe Latenz und mehr Flexibilität bei Entscheidungen bezüglich Leistung und Kosten. So können Kunden interaktive Websites erstellen, die mit großen Datensätzen arbeiten, und Dienste entwickeln, die Sensor- und Telemetriedaten aus Millionen von Endpunkten speichern. Diese Daten können dann mit Hadoop-Jobs analysiert werden. HBase und Hadoop sind gute Ausgangspunkte für Big-Data-Projekte in Azure und ermöglichen es besonders Echtzeitanwendungen, mit großen Datensätzen zu arbeiten.

Die HDInsight-Implementierung nutzt die skalierbare Architektur von HBase für automatische Partitionierung von Tabellen, starke Konsistenz für Lese- und Schreibvorgänge sowie automatisches Failover. Die Leistung wird durch speicherinterne Zwischenspeicherung für Lesevorgänge und Schreibvorgänge mit hohem Durchsatz optimiert. Bereitstellung in einem virtuellen Netzwerk ist für HDInsight HBase ebenfalls verfügbar. Details hierzu finden Sie unter [Bereitstellen von HDInsight-Clustern im virtuellen Azure-Netzwerk][].

## Wie werden Daten in HDInsight HBase verwaltet?

Daten können Sie in HBase mit den Befehlen `create` `get`, `put` und `scan` aus der HBase-Shell verwalten. Daten werden mit dem Befehl `put` in die Datenbank geschrieben und mit dem Befehl `get` daraus gelesen. Der `scan`-Befehl wird verwendet, um Daten aus mehreren Zeilen in einer Tabelle abzurufen. Sie können Daten auch über die HBase-C\#-API verwalten. Diese bietet eine Clientbibliothek auf der HBase-REST-API. Eine HBase-Datenbank können Sie auch mit Hive abfragen. Eine Einleitung für diese Programmiermodelle finden Sie unter [Erste Schritte mit HBase mit Hadoop in HDInsight][]. Coprozessoren sind ebenfalls verfügbar rund ermöglichen die Verarbeitung von Daten in den Knoten, welche die Datenbank hosten.

## Szenarien: Was sind die Anwendungsfälle für HBase?

Der kanonische Anwendungsfall, für den BigTable und daher auch HBase erstellt wurde, ist die Websuche. Suchmaschinen erstellen Indizes, die Begriffe den Webseiten zuordnen, die diese Begriffe enthalten. Es gibt jedoch noch viele weitere Anwendungsfälle für HBase. Einige von ihnen werden in diesem Abschnitt beschrieben.

### Anwendungsfall 1: Schlüssel-Wert-Speicherung

HBase kann für die Schlüssel-Wert-Speicherung verwendet werden und ist für die Verwaltung von Nachrichtensystemen geeignet. Facebook nutzt HBase für sein Nachrichtensystem. HBase ist ideal für die Speicherung und Verwaltung von Internetkommunikation. WebTable nutzt HBase, um Tabellen, die aus Webseiten extrahiert wurden, zu suchen und zu verwalten.

### Anwendungsfall 2: Sensordaten

HBase kann für das Erfassen von Daten verwendet werden, die schrittweise aus verschiedenen Quellen gesammelt werden. Dies umfasst Analyse sozialer Netzwerke, Zeitreihen, Gewährleistung der Aktualität interaktiver Dashboards mit Trends und Indikatoren sowie die Verwaltung von Prüfungsprotokollierungssystemen. Beispiele umfassen das Bloomberg Trader Terminal
und die Open Time Series Database (OpenTSDB), die Metriken speichert, die zum Status von Serversystemen gesammelt wurden, und Zugriff auf sie ermöglicht.

### Anwendungsfall 3: Echtzeitabfrage

[Phoenix][] ist ein SQL-Abfragemodul für Apache HBase. Der Zugriff erfolgt als JDBC-Treiber. So können Sie HBase-Tabellen mit SQL abfragen und verwalten.

### Anwendungsfall 4: HBase als Plattform

Anwendungen können auf HBase ausgeführt werden und HBase als Datenspeicher nutzen. Beispiele hierfür sind Phoenix, OpenTSDB, Kiji und Titan. Es ist auch ein Integration von Anwendungen mit HBase möglich. Beispiele hierfür sind Hive, Pig, Solr, Storm, Flume, Impala, Spark, Ganglia und Drill.

## <a name="next-steps"></a>Nächste Schritte

[Erste Schritte mit HBase mit Hadoop in HDInsight][]

[Bereitstellen von HDInsight-Clustern im virtuellen Azure-Netzwerk][]

[Analysieren von Stimmungen in Twitter mit HBase in HDInsight][]

[Verwenden von Maven zur Entwicklung von Java-Anwendungen, die HBase mit HDInsight (Hadoop) nutzen][]

[C\#-HBase-SDK][]

## <a name="see-also"></a>Weitere Informationen

[Apache HBase][]

[Bigtable: A Distributed Storage System for Structured Data (Bigtable: Ein verteiltes Speichersystem für strukturierte Daten, in englischer Sprache)][]

  [Bereitstellen von HDInsight-Clustern im virtuellen Azure-Netzwerk]: ../hdinsight-hbase-provision-vnet/
  [Erste Schritte mit HBase mit Hadoop in HDInsight]: http://azure.microsoft.com/en-us/documentation/articles/hdinsight-hbase-get-started/
  [Phoenix]: http://phoenix.apache.org/
  [Analysieren von Stimmungen in Twitter mit HBase in HDInsight]: ../hdinsight-hbase-analyze-twitter-sentiment/
  [Verwenden von Maven zur Entwicklung von Java-Anwendungen, die HBase mit HDInsight (Hadoop) nutzen]: ../hdinsight-hbase-build-java-maven/
  [C\#-HBase-SDK]: https://github.com/hdinsight/hbase-sdk-for-net
  [Apache HBase]: https://hbase.apache.org/
  [Bigtable: A Distributed Storage System for Structured Data (Bigtable: Ein verteiltes Speichersystem für strukturierte Daten, in englischer Sprache)]: http://research.google.com/archive/bigtable.html
