<properties urlDisplayName="HDInsight Introduction" pageTitle="Einf&uuml;hrung in Hadoop in HDInsight: Analyse gro&szlig;er Datenmengen in der Cloud | Azure" metaKeywords="" description="Erfahren Sie, wie Azure HDInsight mithilfe von Apache Hadoop-Clustern in der Cloud ein Software-Framework zur Verwaltung, Analyse und Berichtserstellung f&uuml;r gro&szlig;e Datenmengen bereitstellt." metaCanonical="" services="hdinsight" documentationCenter="" title="Einf&uuml;hrung in Hadoop in HDInsight: Analyse und Verarbeitung gro&szlig;er Datenmengen in der Cloud" authors="cgronlun" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/23/2014" ms.author="cgronlun" />

# Einführung in Hadoop in HDInsight: Analyse und Verarbeitung großer Datenmengen in der Cloud

Dieser Artikel enthält eine Übersicht über HDInsight-Komponenten, gängige Terminologie und Szenarien sowie Ressourcen für die Verwendung von Hadoop in HDInsight.

Azure HDInsight verwendet Apache Hadoop-Cluster in der Cloud und stellt ein Software-Framework zur Verwaltung, Analyse und Berichtserstellung für große Datenmengen bereit. Hadoop bietet eine zuverlässige Datenspeicherung über das Hadoop Distributed File System (HDFS) und ein einfaches MapReduce-Programmiermodell zur parallelen Verarbeitung und Analyse der Daten, die in diesem verteilten System gespeichert sind.

### Was versteht man unter "Big Data"?

"Big Data" bezieht sich auf Daten, die in immer größeren Volumen, mit immer höherer Geschwindigkeit und in zahlreichen verschiedenen, unstrukturierten Formaten und semantischem Kontext gesammelt werden.

"Big Data" beschreibt alle großen Mengen digitaler Informationen vom Text in einem Twitter-Feed über Sensordaten von Industriegeräten bis hin zu Daten über Navigation und Einkäufe von Kunden in einem Online-Katalog. "Big Data" kann sich auf historische (gespeicherte) Daten oder auf Echtzeit-Daten (direkt von der Quelle gestreamt) beziehen.

Um neue Erkenntnisse und Einblicke aus großen Datenmengen zu gewinnen, müssen nicht nur die richtigen Fragen gestellt und relevante Daten gesammelt werden. Die Daten müssen außerdem zugänglich gemacht, bereinigt, analysiert und auf nützliche Weise dargestellt werden. Dabei hilft Hadoop in HDInsight.

## Themen in diesem Artikel

Dieser Artikel enthält eine Übersicht über Hadoop in HDInsight, inklusive:

-   **[Übersicht über das Hadoop-Ökosystem in HDInsight:][Übersicht über das Hadoop-Ökosystem in HDInsight:]**HDInsight ist die Hadoop-Lösung in Azure und enthält Implementierungen von Storm, HBase, Pig, Hive, Sqoop, Oozie, Ambari usw. HDInsight kann außerdem mit Business Intelligence (BI)-Tools wie z. B. Excel, SQL Server Analysis Services und SQL Server Reporting Services integriert werden.
-   **[Vorzüge von Hadoop in der Cloud:][Vorzüge von Hadoop in der Cloud:]**Die folgenden Gründe sprechen für die Cloud-Implementierung von Hadoop in HDInsight.
-   **[HDInsight-Lösungen für die Analyse großer Datenmengen:][HDInsight-Lösungen für die Analyse großer Datenmengen:]**Nutzen Sie die Vorzüge von HDInsight, um Fragen für Ihr Unternehmen zu beantworten, von der Analyse von Twitter-Feedback bis hin zur Effizienzanalyse des Klimaanlagensystems.
-   **[Weitere Lernressourcen für Hadoop, HDInsight und die Analyse großer Datenmengen:][Weitere Lernressourcen für Hadoop, HDInsight und die Analyse großer Datenmengen:]**Links zu weiteren Informationen.

## <a name="overview"></a>Übersicht über das Hadoop-Ökosystem in HDInsight

Hadoop ist ein schnell wachsender Technologiestack und eine der Standardlösungen für die Analyse großer Datenmengen. HDInsight ist das Framework für die Azure Cloud-Implementierung von Hadoop.

Hadoop-Cluster in HDInsight verwenden Versionen der HDP-Distribution (Hortonworks Data Platform) und eine Reihe von Komponenten innerhalb dieser Distribution. Informationen zu den Komponenten und deren Versionen in HDInsight-Clustern finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen][Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen]

Apache Hadoop ist ein Software-Framework für Verwaltung und Analyse großer Datenmengen. Apache Hadoop bietet eine zuverlässige Datenspeicherung über das Hadoop Distributed File System (HDFS) und ein einfaches MapReduce-Programmiermodell zur parallelen Verarbeitung und Analyse der Daten, die in diesem verteilten System gespeichert sind. HDFS nutzt Datenreplikation, um Hardware-Probleme zu beheben, die auftreten, wenn derart komplex verteilte Systeme bereitgestellt werden.

Dies sind die Hadoop-Technologien in HDInsight:

-   **[Ambari][Ambari]:** Bereitstellung, Verwaltung und Überwachung von Clustern
-   **[Avro][Avro]** (Microsoft .NET-Bibliothek für Avro): Datenserialisierung für die Microsoft .NET-Umgebung
-   **[HBase][HBase]:** Nichtrelationale Datenbank für extrem große Tabellen
-   **[HDFS][HDFS]:** Hadoop Distributed File System
-   **[Hive][Hive]:** SQL-ähnliche Abfragen
-   **[Mahout][Mahout]:** Maschinelles Lernen
-   **[MapReduce und YARN][MapReduce und YARN]:** Verteilte Verarbeitung und Ressourcenverwaltung
-   **[Oozie][Oozie]:** Workflow-Verwaltung
-   **[Pig][Pig]:** Einfache Skripts für MapReduce-Transformationen
-   **[Sqoop][Sqoop]:** Import und Export von Daten
-   **[Storm][Storm]:** Echtzeit-Verarbeitung schneller und umfangreicher Datenströme
-   **[Zookeeper][Zookeeper]:** Koordinierung von Prozessen in verteilten Systemen

Entdecken Sie außerdem die **[Business Intelligence-Tools][Business Intelligence-Tools]**, die Sie zusammen mit HDInsight verwenden können.

### <a name="ambari"></a>Ambari

Apache Ambari dient zur Bereitstellung, Verwaltung und Überwachung von Apache Hadoop-Clustern. Es umfasst eine Sammlung intuitiver Operatortools und eine Reihe robuster APIs, welche die Komplexität von Hadoop verbergen und den Betrieb von Clustern vereinfachen. Siehe [Überwachen von Hadoop-Clustern in HDInsight mit der Ambari API][Überwachen von Hadoop-Clustern in HDInsight mit der Ambari API] und [Apache Ambari API Reference][Apache Ambari API Reference] (Referenz zur Ambari-API, in englischer Sprache).

### <a name="avro"></a>Avro (Microsoft .NET-Bibliothek für Avro)

Die Microsoft .NET-Bibliothek für Avro implementiert das kompakte Apache Avro-Datenaustauschformat für Binärdaten für die Microsoft .NET-Umgebung. Dabei wird in [JSON][JSON] ein sprachunabhängiges Schema definiert, das die Interoperabilität von Sprachen sicherstellt, sodass die in einer Sprache serialisierten Daten in einer anderen Sprache gelesen werden können. Detaillierte Informationen über das Format finden Sie in der [Apache Avro-Spezifikation][Apache Avro-Spezifikation].
Das Format der Avro-Dateien unterstützt das verteilte MapReduce-Programmiermodell. Die Dateien sind "teilbar", d. h. es kann nach einem beliebigen Punkt in der Datei gesucht und ab einem beliebigen Block gelesen werden. Weitere Informationen hierzu finden Sie unter [Serialisieren von Daten mit der Microsoft .NET-Bibliothek für Avro][Serialisieren von Daten mit der Microsoft .NET-Bibliothek für Avro].

### <a name="hbase"></a>HBase

[Apache HBase][Apache HBase] ist eine nichtrelationale Datenbank basierend auf Hadoop und eignet sich speziell für große Mengen unstrukturierter und teilstrukturierter Daten - bis zu Milliarden von Zeilen mal Millionen von Spalten. HBase-Cluster in HDInsight speichern die Daten direkt im Azure-Blobspeicher mit niedriger Latenz und verbesserter Flexibilität. Siehe [Übersicht über HBase in HDInsight][Übersicht über HBase in HDInsight].

### <a name="hdfs"></a>HDFS

Hadoop Distributed File System (HDFS) ist ein verteiltes Dateisystem, das zusammen mit MapReduce und YARN den Kern des Hadoop-Ökosystems bildet. HDFS ist das Standard-Dateisystem für Hadoop-Cluster in HDInsight.

### <a name="hive"></a>Hive

[Apache Hive][Apache Hive] ist eine Data Warehouse-Software basierend auf Hadoop, mit der Sie große Datensätze in verteilten Speicherformen mithilfe einer SQL-ähnlichen Sprache namens HiveQL abfragen und verwalten können. Hive ist ebenso wie Pig eine Abstraktion, die auf MapReduce aufsetzt. Wenn Hive ausgeführt wird, wandelt es Anfragen in eine Reihe von MapReduce-Aufträgen um. Das Hive-Konzept ähnelt eher einem Managementsystem für relationale Datenbanken als Pig und ist daher besser für strukturierte Daten geeignet. Handelt es sich um unstrukturierte Daten, ist Pig die bessere Wahl. Siehe [Verwenden von Hive mit Hadoop in HDInsight][Verwenden von Hive mit Hadoop in HDInsight]

### <a name="mahout"></a>Mahout

[Apache Mahout][Apache Mahout] ist eine skalierbare Bibliothek mit Algorithmen für maschinelles Lernen basierend auf Hadoop. Anwendungen für maschinelles Lernen verwenden Grundsätze der Statistik, um aus Daten zu lernen und um zukünftiges Verhalten anhand vergangener Daten zu bestimmen. Siehe [Erstellen von Filmempfehlungen mithilfe von Mahout und Hadoop][Erstellen von Filmempfehlungen mithilfe von Mahout und Hadoop].

### <a name="mapreduce"></a>MapReduce und YARN

Hadoop MapReduce ist ein Software-Framework zum Schreiben von Anwendungen, die große Datenmengen parallel verarbeiten. MapReduce-Aufträge teilen große Datensätze auf und organisieren die Daten in Schlüssel-Wert-Paar für die Verarbeitung.

Apache YARN ist die nächste Generation von MapReduce (MapReduce 2.0 oder MRv2) und teilt die beiden Hauptaufgaben von JobTracker - Ressourcenverwaltung und Planung/Überwachung von Aufgaben - in separate Entitäten auf.

Weitere Informationen zu MapReduce finden Sie unter [MapReduce][MapReduce] im Hadoop-Wiki. Weitere Informationen zu YARN finden Sie unter [Apache Hadoop NextGen MapReduce (YARN)][Apache Hadoop NextGen MapReduce (YARN)].

### <a name="oozie"></a>Oozie

[Apache Oozie][Apache Oozie] ist ein Workflow-Koordinationssystem zur Verwaltung von Hadoop-Jobs. Es ist in den Hadoop-Stack integriert und unterstützt Hadoop-Jobs für MapReduce, Pig, Hive und Sqoop. Es kann auch dazu verwendet werden, bestimmte Jobs für ein System zu planen, beispielsweise Java-Programme oder Shellskripts. Siehe [Verwenden eines zeitbasierten Oozie-Koordinators mit Hadoop in HDInsight][Verwenden eines zeitbasierten Oozie-Koordinators mit Hadoop in HDInsight].

### <a name="pig"></a>Pig

[Apache Pig][Apache Pig] ist eine Plattform auf einer hohen Ebene zur Durchführung komplexer MapReduce-Transformationen auf extrem große Datensätze mithilfe einer einfachen Skriptsprache namens Pig Latin. Pig übersetzt Pig Latin-Skripts für die Ausführung in Hadoop. Sie können benutzerdefinierte Funktionen erstellen, um Pig Latin zu erweitern. Siehe [Verwenden von Pig mit Hadoop zur Analyse von Apache-Protokolldateien][Verwenden von Pig mit Hadoop zur Analyse von Apache-Protokolldateien].

### <a name="sqoop"></a>Sqoop

[Apache Sqoop][Apache Sqoop] ist ein Tool für die möglichst effektive Übertragung großer Datenmengen zwischen Hadoop und relationalen Datenbanken wie SQL oder anderen strukturierten Datenspeichern. Siehe [Verwenden von Sqoop mit Hadoop][Verwenden von Sqoop mit Hadoop].

### <a name="storm"></a>Storm

[Apache Storm][Apache Storm] ist ein verteiltes Echtzeit-Berechnungssystem für die schnelle Verarbeitung großer Datenströme. Storm wird als verwalteter Cluster in HDInsight angeboten. Siehe [Analysieren von Echtzeit-Sensordaten mit Storm und Hadoop][Analysieren von Echtzeit-Sensordaten mit Storm und Hadoop].

### <a name="zookeeper"></a>Zookeeper

[Apache Zookeeper][Apache Zookeeper] koordiniert Prozesse in großen verteilten Systemen mithilfe eines hierarchischen Namespace von Datenregistern (znodes). Znodes enthalten kleine Mengen von Metainformationen für die Koordinierung von Prozessen: Status, Standort, Konfiguration usw.

### <a name="bi"></a>Business Intelligence-Tools

Bekannte Business-Intelligence-Tools wie Excel, PowerPivot, SQL Server Analysis Services und Reporting Services rufen Daten, die in HDInsight integriert sind, entweder über das Add-In Power Query oder den Microsoft Hive ODBC-Driver ab, analysieren sie und erstellen einen Bericht.

Diese BI-Tools sind hilfreich für Ihre Analyse großer Datenmengen:

-   [Microsoft Power Query für Excel herunterladen][Microsoft Power Query für Excel herunterladen]
-   [Microsoft Hive ODBC Driver herunterladen][Microsoft Hive ODBC Driver herunterladen]
-   [Weitere Informationen zu SQL Server Analysis Services][Weitere Informationen zu SQL Server Analysis Services]
-   [Weitere Informationen zu SQL Server Reporting Services][Weitere Informationen zu SQL Server Reporting Services]

## <a name="advantage"></a>Vorzüge von Hadoop in der Cloud

Hadoop in HDInsight ist Teil des Azure Cloud-Ökosystems und bietet eine Vielzahl von Vorteilen, zum Beispiel:

-   Modernste Hadoop-Komponenten. Weitere Informationen finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen][Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen]
-   Hohe Verfügbarkeit und Zuverlässigkeit von Clustern. Weitere Details finden Sie unter [Verfügbarkeit und Zuverlässigkeit von Hadoop-Clustern in HDInsight][Verfügbarkeit und Zuverlässigkeit von Hadoop-Clustern in HDInsight].
-   Effiziente und wirtschaftliche Datenspeicherung im Azure-Blobspeicher, eine Hadoop-kompatible Option. Siehe [Verwenden des Azure-Blobspeichers mit Hadoop in HDInsight][Verwenden des Azure-Blobspeichers mit Hadoop in HDInsight].
-   Integration mit anderen Azure-Diensten, z. B. [Websites][Websites] und [SQL Database][SQL Database].
-   Niedrige Einstiegskosten. Probieren Sie die [kostenlose Testversion][kostenlose Testversion] aus oder entdecken Sie die [HDInsight-Preisdetails][HDInsight-Preisdetails].

Weitere Informationen zu den Vorteilen von Hadoop in HDInsight finden Sie in der [Azure-Features-Seite für HDInsight][Azure-Features-Seite für HDInsight].

## <a name="solutions"></a>HDInsight-Lösungen für die Analyse großer Datenmengen ausprobieren

Analysieren Sie Daten aus Ihrer Organisation, um neue Einblicke zu gewinnen. Hier einige Beispiele:

-   [Analysieren von Klimaanlagen-Sensordaten][Analysieren von Klimaanlagen-Sensordaten]: Erfahren Sie, wie Sie Sensordaten mithilfe von Hive in HDInsight (Hadoop) analysieren und die Daten anschließend in Microsoft Excel visualisieren können. Dieses Beispiel verwendet Hive zur Verarbeitung historischer Daten von Klimaanlagen-Systemen, um festzustellen, welche Systeme Probleme beim Einhalten der eingestellten Temperatur haben.
-   [Verwenden von Hive mit HDInsight zum Analysieren von Website-Protokollen][Verwenden von Hive mit HDInsight zum Analysieren von Website-Protokollen]: Erfahren Sie, wie Sie Website-Protokolle mithilfe von HiveQL in HDInsight analysieren können, um Einblicke in die Häufigkeit von Besuchen von externen Webseiten sowie eine Zusammenfassung von Website-Fehlern zu erhalten, die den Benutzern angezeigt werden.
-   [Analysieren von Sensordaten in Echtzeit mit Storm und HBase in HDInsight (Hadoop)][Analysieren von Echtzeit-Sensordaten mit Storm und Hadoop]: Erfahren Sie, wie Sie Sensordaten aus Azure Ereignis-Hubs mithilfe eines Storm-Clusters in HDInsight verarbeiten und die verarbeiteten Sensordaten anschließend beinahe in Echtzeit in einem webbasierten Dashboard darstellen können.

Weitere Informationen zu Hadoop in HDInsight finden Sie in den "Erste Schritte"-Artikeln im Durchsuchen-Bereich auf der [HDInsight-Dokumentationsseite][HDInsight-Dokumentationsseite]. Im Analysieren-Bereich finden Sie Beispiele für fortgeschrittene Anwender.

## <a name="resources"></a>Weitere Lernressourcen für Hadoop, HDInsight und die Analyse großer Datenmengen

### Hadoop in HDInsight

-   [HDInsight-Dokumentation][HDInsight-Dokumentationsseite]: Dokumentationsseite für Azure HDInsight mit Links auf Artikel Videos und weitere Ressourcen.

-   [Lernempfehlung für Hadoop in HDInsight][Lernempfehlung für Hadoop in HDInsight]: Eine Einführung in die Hadoop-Dokumentation für HDInsight.

-   [Erste Schritte mit Azure HDInsight][Erste Schritte mit Azure HDInsight]: Ein Lernprogramm für den schnellen Einstieg in Hadoop in HDInsight.

-   [Ausführen der HDInsight-Beispiele][Ausführen der HDInsight-Beispiele]: Lernprogramm zum Ausführen der Beispiele die mit HDInsight geliefert werden.

-   [Azure HDInsight SDK][Azure HDInsight SDK]: Referenzdokumentation für die HDInsight SDK.

### SQL Database in Azure

-   [Azure SQL-Datenbank][Azure SQL-Datenbank]: MSDN-Dokumentation für SQL-Datenbank.

-   [Managementportal für SQL-Datenbank][Managementportal für SQL-Datenbank]: Einfaches und unkompliziertes Datenbankmanagementtool für die Verwaltung einer SQL-Datenbank in der Cloud.

-   [Adventure Works für SQL-Datenbank][Adventure Works für SQL-Datenbank]: Download-Seite für SQL-Beispieldatenbank.

### Microsoft Business Intelligence

-   [Verbinden von Excel über Power Query mit Hadoop][Verbinden von Excel über Power Query mit Hadoop]: Erfahren Sie, wie Sie mithilfe von Microsoft Power Query Excel mit dem Azure-Speicherkonto verbinden, in dem die Daten für Ihren HDInsight-Cluster gespeichert werden.

-   [Verbinden von Excel über den Microsoft Hive ODBC Driver mit Hadoop][Verbinden von Excel über den Microsoft Hive ODBC Driver mit Hadoop]: Erfahren Sie, wie Sie Daten aus HDInsight über den Microsoft Hive ODBC-Driver importieren können.

-   [Microsoft Business Intelligence (BI)][Microsoft Business Intelligence (BI)]: Entdecken Sie Power BI für Office 365, laden Sie die Testversion von SQL Server herunter und richten Sie SharePoint Server 2013 und SQL Server BI ein.

### Apache Hadoop

-   [Apache Hadoop][Apache Hadoop]: Erfahren Sie mehr über die Apache Hadoop-Softwarebibliothek, ein Framework, das die verteilte Verarbeitung großer Datensätze auf Clustern verfügbarer Computer ermöglicht.

-   [HDFS][1]: Erfahren Sie mehr über die Architektur und das Design von Hadoop Distributed File System (HDFS), dem primären Speichersystem, das von Hadoop-Anwendungen verwendet wird.

-   [MapReduce][2]: Erfahren Sie mehr über das Programmierframework für das Schreiben von Hadoop-Anwendungen, die parallel große Datenmengen auf großen Cluster-Serverknoten verarbeiten.

  [Übersicht über das Hadoop-Ökosystem in HDInsight:]: #overview
  [Vorzüge von Hadoop in der Cloud:]: #advantage
  [HDInsight-Lösungen für die Analyse großer Datenmengen:]: #solutions
  [Weitere Lernressourcen für Hadoop, HDInsight und die Analyse großer Datenmengen:]: #resources
  [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen]: ../hdinsight-component-versioning/
  [Ambari]: #ambari
  [Avro]: #avro
  [HBase]: #hbase
  [HDFS]: #hdfs
  [Hive]: #hive
  [Mahout]: #mahout
  [MapReduce und YARN]: #mapreduce
  [Oozie]: #oozie
  [Pig]: #pig
  [Sqoop]: #sqoop
  [Storm]: #storm
  [Zookeeper]: #zookeeper
  [Business Intelligence-Tools]: #bi
  [Überwachen von Hadoop-Clustern in HDInsight mit der Ambari API]: ../hdinsight-monitor-use-ambari-api/
  [Apache Ambari API Reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [JSON]: http://www.json.org/
  [Apache Avro-Spezifikation]: http://avro.apache.org/docs/current/spec.html
  [Serialisieren von Daten mit der Microsoft .NET-Bibliothek für Avro]: ../hdinsight-dotnet-avro-serialization/
  [Apache HBase]: http://hbase.apache.org/
  [Übersicht über HBase in HDInsight]: ../hdinsight-hbase-overview/
  [Apache Hive]: http://hive.apache.org/
  [Verwenden von Hive mit Hadoop in HDInsight]: ../hdinsight-use-hive/
  [Apache Mahout]: https://mahout.apache.org/
  [Erstellen von Filmempfehlungen mithilfe von Mahout und Hadoop]: ../hdinsight-mahout/
  [MapReduce]: http://wiki.apache.org/hadoop/MapReduce
  [Apache Hadoop NextGen MapReduce (YARN)]: http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html
  [Apache Oozie]: http://oozie.apache.org/
  [Verwenden eines zeitbasierten Oozie-Koordinators mit Hadoop in HDInsight]: ../hdinsight-use-oozie-coordinator-time/
  [Apache Pig]: http://pig.apache.org/
  [Verwenden von Pig mit Hadoop zur Analyse von Apache-Protokolldateien]: ../hdinsight-use-pig/
  [Apache Sqoop]: http://sqoop.apache.org/
  [Verwenden von Sqoop mit Hadoop]: ../hdinsight-use-sqoop/
  [Apache Storm]: https://storm.incubator.apache.org/
  [Analysieren von Echtzeit-Sensordaten mit Storm und Hadoop]: ../hdinsight-storm-sensor-data-analysis/
  [Apache Zookeeper]: http://zookeeper.apache.org/
  [Microsoft Power Query für Excel herunterladen]: http://www.microsoft.com/de-de/download/details.aspx?id=39379
  [Microsoft Hive ODBC Driver herunterladen]: http://www.microsoft.com/de-de/download/details.aspx?id=40886
  [Weitere Informationen zu SQL Server Analysis Services]: http://www.microsoft.com/de-de/server-cloud/solutions/business-intelligence/analysis.aspx
  [Weitere Informationen zu SQL Server Reporting Services]: http://msdn.microsoft.com/de-de/library/ms159106.aspx
  [Verfügbarkeit und Zuverlässigkeit von Hadoop-Clustern in HDInsight]: ../hdinsight-high-availability/
  [Verwenden des Azure-Blobspeichers mit Hadoop in HDInsight]: ../hdinsight-use-blob-storage/
  [Websites]: ../documentation/services/websites/
  [SQL Database]: ../documentation/services/sql-database/
  [kostenlose Testversion]: /pricing/free-trial/
  [HDInsight-Preisdetails]: ../pricing/details/hdinsight/
  [Azure-Features-Seite für HDInsight]: ../services/hdinsight/
  [Analysieren von Klimaanlagen-Sensordaten]: ../hdinsight-hive-analyze-sensor-data/
  [Verwenden von Hive mit HDInsight zum Analysieren von Website-Protokollen]: ../hdinsight-hive-analyze-website-log/
  [HDInsight-Dokumentationsseite]: ../documentation/services/hdinsight/
  [Lernempfehlung für Hadoop in HDInsight]: ../hdinsight-learn-map
  [Erste Schritte mit Azure HDInsight]: ../hdinsight-get-started/
  [Ausführen der HDInsight-Beispiele]: ../hdinsight-run-samples/
  [Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/de-de/library/dn479185.aspx
  [Azure SQL-Datenbank]: http://msdn.microsoft.com/de-de/library/windowsazure/ee336279.aspx
  [Managementportal für SQL-Datenbank]: http://msdn.microsoft.com/de-de/library/windowsazure/gg442309.aspx
  [Adventure Works für SQL-Datenbank]: http://msftdbprodsamples.codeplex.com/releases/view/37304
  [Verbinden von Excel über Power Query mit Hadoop]: ../hdinsight-connect-excel-power-query/
  [Verbinden von Excel über den Microsoft Hive ODBC Driver mit Hadoop]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Microsoft Business Intelligence (BI)]: http://www.microsoft.com/de-de/server-cloud/solutions/business-intelligence/default.aspx
  [Apache Hadoop]: http://hadoop.apache.org/
