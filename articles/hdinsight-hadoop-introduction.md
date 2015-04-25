<properties 
	pageTitle="Einführung in Hadoop in HDInsight: Analyse großer Datenmengen in der Cloud | Azure" 
	description="Erfahren Sie, wie Azure HDInsight mithilfe von Apache Hadoop-Clustern in der Cloud ein Software-Framework zur Verwaltung, Analyse und Berichtserstellung für große Datenmengen bereitstellt." 
	services="hdinsight" 
	documentationCenter="" 
	authors="cjgronlund" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/23/2014" 
	ms.author="cgronlun"/>



# Einführung in Hadoop in HDInsight: Analyse und Verarbeitung großer Datenmengen in der Cloud

Dieser Artikel enthält eine Übersicht über HDInsight-Komponenten, gängige Terminologie und Szenarien sowie Ressourcen für die Verwendung von Hadoop in HDInsight.

Azure HDInsight verwendet Apache Hadoop-Cluster in der Cloud und stellt ein Software-Framework zur Verwaltung, Analyse und Berichtserstellung für große Datenmengen bereit. Hadoop bietet eine zuverlässige Datenspeicherung über das Hadoop Distributed File System (HDFS) und ein einfaches MapReduce-Programmiermodell zur parallelen Verarbeitung und Analyse der Daten, die in diesem verteilten System gespeichert sind. 

### Was versteht man unter "Big Data"?
"Big Data" bezieht sich auf Daten, die in immer größeren Volumen, mit immer höherer Geschwindigkeit und in zahlreichen verschiedenen, unstrukturierten Formaten und semantischem Kontext gesammelt werden. 

"Big Data" beschreibt alle großen Mengen digitaler Informationen vom Text in einem Twitter-Feed über Sensordaten von Industriegeräten bis hin zu Daten über Navigation und Einkäufe von Kunden in einem Online-Katalog. "Big Data" kann sich auf historische (gespeicherte) Daten oder auf Echtzeit-Daten (direkt von der Quelle gestreamt) beziehen. 

Um neue Erkenntnisse und Einblicke aus großen Datenmengen zu gewinnen, müssen nicht nur die richtigen Fragen gestellt und relevante Daten gesammelt werden. Die Daten müssen außerdem zugänglich gemacht, bereinigt, analysiert und auf nützliche Weise dargestellt werden. Dabei hilft Hadoop in HDInsight.


## Themen in diesem Artikel

Dieser Artikel enthält eine Übersicht über Hadoop in HDInsight, inklusive:

* **[Übersicht über das Hadoop-Ökosystem in HDInsight:](#overview)**  HDInsight ist die Hadoop-Lösung in Azure und enthält Implementierungen von Storm, HBase, Pig, Hive, Sqoop, Oozie, Ambari usw. HDInsight kann außerdem mit Business Intelligence (BI)-Tools wie z. B. Excel, SQL Server Analysis Services und SQL Server Reporting Services integriert werden.
* **[Vorzüge von Hadoop in der Cloud:](#advantage)** Es gibt gute Gründe, aus denen Sie die HDInsight Cloud-Implementierung von Hadoop in Erwägung ziehen sollten.
* **[HDInsight-Lösungen für die Analyse großer Datenmengen:](#solutions)** Ihnen stehen einige nützliche Möglichkeiten zur Verfügung, mit deren Hilfe Sie HDInsight zur Beantwortung von Fragen für Ihr Unternehmen einsetzen können - von der Analyse von Twitter-Stimmungsdaten bis zur Bewertung der HVAC-Systemleistungsfähigkeit.
* **[Weitere Lernressourcen für Hadoop, HDInsight und die Analyse großer Datenmengen:](#resources)** Links für zusätzliche Informationen.


## <a name="overview"></a>Übersicht über das Hadoop-Ökosystem in HDInsight

Hadoop ist ein schnell wachsender Technologiestack und eine der Standardlösungen für die Analyse großer Datenmengen. HDInsight ist das Framework für die Azure Cloud-Implementierung von Hadoop.
 
Hadoop-Cluster in HDInsight verwenden Versionen der HDP-Distribution (Hortonworks Data Platform) und eine Reihe von Komponenten innerhalb dieser Distribution. Informationen zu den Komponenten und deren Versionen in HDInsight-Clustern finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen][component-versioning].

Apache Hadoop ist ein Software-Framework für Verwaltung und Analyse großer Datenmengen. Apache Hadoop bietet eine zuverlässige Datenspeicherung über das Hadoop Distributed File System (HDFS) und ein einfaches MapReduce-Programmiermodell zur parallelen Verarbeitung und Analyse der Daten, die in diesem verteilten System gespeichert sind. HDFS nutzt Datenreplikation, um Hardware-Probleme zu beheben, die auftreten, wenn derart komplex verteilte Systeme bereitgestellt werden.

Dies sind die Hadoop-Technologien in HDInsight:

* **[Ambari](#ambari):** Bereitstellung, Verwaltung und Überwachung von Clustern 
* **[Avro](#avro)** (Microsoft .NET-Bibliothek für Avro): Datenserialisierung für die Microsoft .NET-Umgebung 
* **[HBase](#hbase):** Nichtrelationale Datenbank für extrem große Tabellen
* **[HDFS](#hdfs):** Hadoop Distributed File System
* **[Hive](#hive):** SQL-ähnliche Abfragen
* **[Mahout](#mahout):** Maschinelles Lernen
* **[MapReduce und YARN](#mapreduce):** Verteilte Verarbeitung und Ressourcenverwaltung
* **[Oozie](#oozie):** Workflow-Verwaltung
* **[Pig](#pig):** Einfache Skripts für MapReduce-Transformationen
* **[Sqoop](#sqoop):** Import und Export von Daten 
* **[Storm](#storm):** Echtzeit-Verarbeitung schneller und umfangreicher Datenströme
* **[Zookeeper](#zookeeper):** Koordinierung von Prozessen in verteilten Systemen
 
Entdecken Sie auch die **[Business Intelligence-Tools](#bi)**, die Sie zusammen mit HDInsight verwenden können.

###<a name="ambari"></a>Ambari

Apache Ambari dient zur Bereitstellung, Verwaltung und Überwachung von Apache Hadoop-Clustern. Es umfasst eine Sammlung intuitiver Operatortools und eine Reihe robuster APIs, welche die Komplexität von Hadoop verbergen und den Betrieb von Clustern vereinfachen. Siehe  [Überwachen von Hadoop-Clustern in HDInsight mit der Ambari-API](hdinsight-monitor-use-ambari-api.md) und <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari API reference</a>(Referenz zur Ambari-API, in englischer Sprache).

### <a name="avro"></a>Avro (Microsoft .NET-Bibliothek für Avro)

Die Microsoft .NET-Bibliothek für Avro implementiert das kompakte Apache Avro-Datenaustauschformat für Binärdaten für die Microsoft .NET-Umgebung. Dabei wird in  <a target="_blank" href="http://www.json.org/">JSON</a> ein sprachunabhängiges Schema definiert, das die Interoperabilität von Sprachen sicherstellt, sodass die in einer Sprache serialisierten Daten in einer anderen Sprache gelesen werden können. Detaillierte Informationen über das Format finden Sie in der <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Apache Avro-Spezifikation</a>(Referenz zur Ambari-API, in englischer Sprache). 
Das Format von Avro-Dateien unterstützt das verteilte MapReduce-Programmiermodell. Die Dateien sind "teilbar", d. h. es kann nach einem beliebigen Punkt in der Datei gesucht und ab einem beliebigen Block gelesen werden. Weitere Informationen hierzu finden Sie unter [Serialisieren von Daten mit der Microsoft .NET-Bibliothek für Avro](hdinsight-dotnet-avro-serialization.md).

### <a name="hbase"></a>HBase

<a target="_blank" href="http://hbase.apache.org/">Apache HBase</a> ist eine nichtrelationale Datenbank basierend auf Hadoop und eignet sich speziell für große Mengen unstrukturierter und teilstrukturierter Daten - bis zu potenziell Milliarden von Zeilen mal Millionen von Spalten. HBase-Cluster in HDInsight speichern die Daten direkt im Azure-BLOB-Speicher mit niedriger Latenz und verbesserter Flexibilität. Siehe [Übersicht über HBase in HDInsight](hdinsight-hbase-overview.md).

### <a name="hdfs"></a>HDFS

Hadoop Distributed File System (HDFS) ist ein verteiltes Dateisystem, das zusammen mit MapReduce und YARN den Kern des Hadoop-Ökosystems bildet. HDFS ist das Standard-Dateisystem für Hadoop-Cluster in HDInsight.

### <a name="hive"></a>Hive

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> ist eine Data Warehouse-Software basierend auf Hadoop, mit der Sie große Datensätze in verteilten Speicherformen mithilfe einer SQL-ähnlichen Sprache namens HiveQL abfragen und verwalten können. Hive ist ebenso wie Pig eine Abstraktion, die auf MapReduce aufsetzt. Wenn Hive ausgeführt wird, wandelt es Anfragen in eine Reihe von MapReduce-Aufträgen um. Das Hive-Konzept ähnelt eher einem Managementsystem für relationale Datenbanken als Pig und ist daher besser für strukturierte Daten geeignet. Handelt es sich um unstrukturierte Daten, ist Pig die bessere Wahl. Siehe [Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md).

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> ist eine skalierbare Bibliothek mit Algorithmen für maschinelles Lernen basierend auf Hadoop. Anwendungen für maschinelles Lernen verwenden Grundsätze der Statistik, um aus Daten zu lernen und um zukünftiges Verhalten anhand vergangener Daten zu bestimmen. Siehe [Erstellen von Filmempfehlungen mithilfe von Apache Mahout mit HDInsight (Hadoop)](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce und YARN
Hadoop MapReduce ist ein Software-Framework zum Schreiben von Anwendungen, die große Datenmengen parallel verarbeiten. MapReduce-Aufträge teilen große Datensätze auf und organisieren die Daten in Schlüssel-Wert-Paar für die Verarbeitung. 

Apache YARN ist die nächste Generation von MapReduce (MapReduce 2.0 oder MRv2) und teilt die beiden Hauptaufgaben von JobTracker - Ressourcenverwaltung und Planung/Überwachung von Aufgaben - in separate Entitäten auf.

Weitere Informationen zu MapReduce finden Sie unter <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> im Hadoop-Wiki. Weitere Informationen zu YARN finden Sie unter <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> ist ein Workflow-Koordinationssystem zur Verwaltung von Hadoop-Aufträgen. Es ist in den Hadoop-Stack integriert und unterstützt Hadoop-Jobs für MapReduce, Pig, Hive und Sqoop. Es kann auch dazu verwendet werden, bestimmte Jobs für ein System zu planen, beispielsweise Java-Programme oder Shellskripts. Siehe [Verwenden des zeitbasierten Oozie-Koordinators mit Hadoop in HDInsight](hdinsight-use-oozie-coordinator-time.md).

### <a name="pig"></a>Pig

<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> ist eine Plattform auf einer hohen Ebene zur Durchführung komplexer MapReduce-Transformationen von extrem großen Datensätzen mithilfe einer einfachen Skriptsprache namens Pig Latin. Pig übersetzt Pig Latin-Skripts für die Ausführung in Hadoop. Sie können benutzerdefinierte Funktionen erstellen, um Pig Latin zu erweitern. Siehe [Verwenden von Pig mit Hadoop zur Analyse einer Apache-Protokolldatei](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> ist ein Tool für einen möglichst effektiven Transfer großer Datenmengen zwischen Hadoop und relationalen Datenbanken wie SQL oder anderen strukturierten Datenspeichern. Siehe [Verwenden von Sqoop mit Hadoop](hdinsight-use-sqoop.md).

### <a name="storm"></a>Storm
<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a> ist ein verteiltes Echtzeit-Berechnungssystem für die schnelle Verarbeitung großer Datenströme. Storm wird als verwalteter Cluster in HDInsight angeboten. Siehe [Analysieren von Sensordaten mit Storm und HBase in HDInsight (Hadoop)](hdinsight-storm-sensor-data-analysis.md).

### <a name="zookeeper"></a>Zookeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache Zookeeper</a> koordiniert Prozesse in großen verteilten Systemen mithilfe eines gemeinsam genutzten hierarchischen Namespace von Datenregistern (ZNodes). ZNodes enthalten kleine Mengen an Metainformationen, die für die Koordination von Prozessen erforderlich sind: Status, Speicherort, Konfiguration usw. 

### <a name="bi"></a>Business Intelligence-Tools 
Bekannte Business-Intelligence-Tools wie Excel, PowerPivot, SQL Server Analysis Services und Reporting Services rufen Daten, die in HDInsight integriert sind, entweder über das Add-In Power Query oder den Microsoft Hive ODBC-Driver ab, analysieren sie und erstellen einen Bericht. 

Diese BI-Tools sind hilfreich für Ihre Analyse großer Datenmengen:

* <a target="_blank" href="http://www.microsoft.com/de-de/download/details.aspx?id=39379">Microsoft Power Query für Excel herunterladen</a> 
* <a target="_blank" href="http://www.microsoft.com/de-de/download/details.aspx?id=40886">Microsoft Hive ODBC Driver herunterladen</a>
* <a target="_blank" href="http://www.microsoft.com/de-de/server-cloud/solutions/business-intelligence/analysis.aspx">Weitere Informationen zu SQL Server Analysis Services</a>
* <a target="_blank" href="http://msdn.microsoft.com/library/ms159106.aspx">Weitere Informationen zu SQL Server Reporting Services</a>

## <a name="advantage"></a>Vorzüge von Hadoop in der Cloud

Hadoop in HDInsight ist Teil des Azure Cloud-Ökosystems und bietet eine Vielzahl von Vorteilen, zum Beispiel:

* Modernste Hadoop-Komponenten. Weitere Informationen finden Sie unter [Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen][component-versioning].
* Hohe Verfügbarkeit und Zuverlässigkeit von Clustern. Weitere Details finden Sie unter [Verfügbarkeit und Zuverlässigkeit von Hadoop-Clustern in HDInsight](hdinsight-high-availability.md).
* Effiziente und wirtschaftliche Datenspeicherung im Azure-BLOB-Speicher, eine Hadoop-kompatible Option. Weitere Informationen finden Sie unter [Verwenden des Azure-BLOB-Speichers mit Hadoop in HDInsight](hdinsight-use-blob-storage.md).
* Integration mit anderen Azure-Diensten, z. B. [Websites](../documentation/services/websites/) und [SQL Database](../documentation/services/sql-database/).
* Niedrige Einstiegskosten. Probieren Sie die [kostenlose Testversion](/pricing/free-trial/) aus, oder entdecken Sie die [HDInsight-Preisdetails](../pricing/details/hdinsight/).

Weitere Informationen zu den Vorteilen von Hadoop in HDInsight finden Sie in der [Azure-Features-Seite für HDInsight][marketing-page].

## <a name="solutions"></a>HDInsight-Lösungen für die Analyse großer Datenmengen ausprobieren

Analysieren Sie Daten aus Ihrer Organisation, um neue Einblicke zu gewinnen. Hier einige Beispiele: 

* [Analysieren von Klimaanlagen-Sensordaten](hdinsight-hive-analyze-sensor-data.md): Erfahren Sie, wie Sie Sensordaten mithilfe von Hive in HDInsight (Hadoop) analysieren und die Daten anschließend in Microsoft Excel visualisieren können. Dieses Beispiel verwendet Hive zur Verarbeitung historischer Daten von Klimaanlagen-Systemen, um festzustellen, welche Systeme Probleme beim Einhalten der eingestellten Temperatur haben.
* [Verwenden von Hive mit HDInsight zum Analysieren von Website-Protokollen](hdinsight-hive-analyze-website-log.md): Erfahren Sie, wie Sie Website-Protokolle mithilfe von HiveQL in HDInsight analysieren können, um Einblicke in die Häufigkeit von Besuchen von externen Webseiten sowie eine Zusammenfassung von Website-Fehlern zu erhalten, die den Benutzern angezeigt werden.
* [Analysieren von Sensordaten in Echtzeit mit Storm und HBase in HDInsight (Hadoop)](hdinsight-storm-sensor-data-analysis.md): Erfahren Sie, wie Sie Sensordaten aus Azure Ereignis-Hubs mithilfe eines Storm-Clusters in HDInsight verarbeiten und die verarbeiteten Sensordaten anschließend beinahe in Echtzeit in einem webbasierten Dashboard darstellen können.

Weitere Informationen zu Hadoop in HDInsight finden Sie in den "Erste Schritte"-Artikeln im Durchsuchen-Bereich auf der [HDInsight-Dokumentationsseite](../documentation/services/hdinsight/). Im Analysieren-Bereich finden Sie Beispiele für fortgeschrittene Anwender.



## <a name="resources"></a>Weitere Lernressourcen für Hadoop, HDInsight und die Analyse großer Datenmengen 

### Hadoop in HDInsight	

* [HDInsight-Dokumentation](../documentation/services/hdinsight/): Dokumentationsseite für Azure HDInsight mit Links auf Artikel Videos und weitere Ressourcen.

* [Lernempfehlungen für Hadoop in HDInsight](hdinsight-learn-map.md): Eine Einführung in die Hadoop-Dokumentation für HDInsight.

* [Erste Schritte mit Azure HDInsight](hdinsight-get-started.md): Ein Lernprogramm für den schnellen Einstieg in Hadoop in HDInsight.

* [Ausführen von HDInsight-Beispielen](hdinsight-run-samples.md): Lernprogramm zum Ausführen der Beispiele die mit HDInsight geliefert werden.
	
* [Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/de-de/library/dn479185.aspx): Referenzdokumentation für die HDInsight SDK.


### SQL Database in Azure	
		
* [Azure SQL Database](http://msdn.microsoft.com/library/windowsazure/ee336279.aspx): MSDN-Dokumentation für SQL-Datenbank.
	
* [Verwaltungsportal für SQL Database](http://msdn.microsoft.com/library/windowsazure/gg442309.aspx): Einfaches und unkompliziertes Datenbankmanagementtool für die Verwaltung einer SQL-Datenbank in der Cloud.

* [Adventure Works für SQL Database](http://msftdbprodsamples.codeplex.com/releases/view/37304): Download-Seite für SQL-Beispieldatenbank.	

### Microsoft Business Intelligence		

* [Verbinden von Excel über Power Query mit Hadoop](hdinsight-connect-excel-power-query.md): Erfahren Sie, wie Sie mithilfe von Microsoft Power Query Excel mit dem Azure-Speicherkonto verbinden, in dem die Daten für Ihren HDInsight-Cluster gespeichert werden. 

* [Verbinden von Excel über den Microsoft Hive ODBC Driver mit Hadoop ](hdinsight-connect-excel-hive-ODBC-driver.md): Erfahren Sie, wie Sie Daten aus HDInsight über den Microsoft Hive ODBC-Driver importieren können.

* [Microsoft Business Intelligence (BI)](http://www.microsoft.com/de-de/server-cloud/solutions/business-intelligence/default.aspx): Entdecken Sie Power BI für Office 365, laden Sie die Testversion von SQL Server herunter und richten Sie SharePoint Server 2013 und SQL Server BI ein.
			

### Apache Hadoop			

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a>: Erfahren Sie mehr über die Apache Hadoop-Softwarebibliothek, ein Framework, das die verteilte Verarbeitung großer Datensätze auf Clustern verfügbarer Computer ermöglicht.	

*  <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html">HDFS</a>: Erfahren Sie mehr über die Architektur und das Design von Hadoop Distributed File System (HDFS), dem primären Speichersystem, das von Hadoop-Anwendungen verwendet wird.		

* <a target="_blank" href="http://mapreduce.org/">MapReduce</a>: Erfahren Sie mehr über das Programmierframework für das Schreiben von Hadoop-Anwendungen, die parallel große Datenmengen auf großen Cluster-Serverknoten verarbeiten.	


[marketing-page]: ../services/hdinsight/
[component-versioning]: ../hdinsight-component-versioning/

[zookeeper]: http://zookeeper.apache.org/ 

<!--HONumber=42-->
