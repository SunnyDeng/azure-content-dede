<properties 
	pageTitle="Was ist Hadoop in HDInsight?: Big Data-Analyse in der Cloud | Microsoft Azure" 
	description="Eine Einführung in Hadoop-Komponenten in der Cloud in HDInsight. Erfahren Sie, wie HDInsight Hadoop-Cluster nutzt, um Big Data zu verwalten, zu analysieren und dazugehörige Berichte zu erstellen." 
	keywords="big data,big data analysis,hadoop,introduction to hadoop,what is hadoop"
	services="hdinsight" 
	documentationCenter="" 
	authors="cjgronlund" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="02/18/2015"
   ms.author="cgronlun"/>


# Einführung in Hadoop in HDInsight: Analyse und Verarbeitung von Big Data in der Cloud

Lesen Sie eine Einführung in Hadoop, sein Ökosystem und Big Data in Azure HDInsight, und erfahren Sie, was Hadoop in HDInsight ist und welche Hadoop-Komponenten es gibt. Außerdem werden die gängige Terminologie und Szenarien für die Big Data-Analyse vorgestellt. Lernen Sie außerdem Dokumentation, Lernprogramme und Ressourcen für die Verwendung von Hadoop in HDInsight kennen.

## Was ist Hadoop in HDInsight?

Azure HDInsight verwendet Apache Hadoop-Cluster in der Cloud und stellt ein Software-Framework zur Verwaltung, Analyse und Berichtserstellung für große Datenmengen bereit. Hadoop bietet eine zuverlässige Datenspeicherung über das Hadoop Distributed File System (HDFS) und ein einfaches MapReduce-Programmiermodell zur parallelen Verarbeitung und Analyse der Daten, die in diesem verteilten System gespeichert sind.


## Was versteht man unter "Big Data"?
"Big Data" bezieht sich auf Daten, die in immer größeren Volumen, mit immer höherer Geschwindigkeit und in zahlreichen verschiedenen, unstrukturierten Formaten und semantischem Kontext gesammelt werden.

"Big Data" beschreibt beliebig großen Mengen digitaler Informationen – vom Text in einem Twitter-Feed über Sensordaten von Industrieanlagen bis hin zu Daten über die Navigation und Einkäufe von Kunden in einem Onlinekatalog. "Big Data" kann sich auf historische (gespeicherte) Daten oder auf Echtzeit-Daten (direkt von der Quelle gestreamt) beziehen.

Um umsetzbare Erkenntnisse und Einblicke aus Big Data zu gewinnen, müssen nicht nur die richtigen Fragen gestellt und relevante Daten gesammelt werden. Die Daten müssen außerdem zugänglich gemacht, bereinigt, analysiert und auf nützliche Weise dargestellt werden. Hierbei kann die Big Data-Analyse mit Hadoop in HDInsight hilfreich sein.


## <a name="overview"></a>Übersicht über das Hadoop-Ökosystem in HDInsight

HDInsight ist eine Cloudimplementierung in Microsoft Azure des schnell wachsenden Apache Hadoop-Technologiestapels, der die bevorzuge Lösung für die Analyse von Big Data darstellt. HDInsight umfasst Implementierungen von Sturm, HBase, Pig, Hive, Sqoop, Oozie, Ambari usw. HDInsight kann außerdem mit Business Intelligence (BI)-Tools wie z. B. Excel, SQL Server Analysis Services und SQL Server Reporting Services integriert werden.


* Azure HDInsight stellt Hadoop-Cluster mithilfe von entweder **Linux** oder **Windows** als zugrunde liegendes Betriebssystem in der Cloud bereit.

	* **HDInsight unter Linux (Vorschau)** – Ein Hadoop-Cluster unter Ubuntu. Wählen Sie diese Lösung, wenn Sie mit Linux oder Unix und der Migration von einer vorhandenen Linux-basierten Hadoop-Lösung vertraut sind oder Sie eine einfache Integration mit Komponenten des Hadoop-Systems wünschen, die für Linux konzipiert sind.

	* **HDInsight unter Windows** – Ein Hadoop-Cluster unter Windows Server. Verwenden Sie diese Option, wenn Sie mit Windows vertraut sind, von einer vorhandenen Windows-basierten Hadoop-Lösung migrieren oder eine Integration in .NET- oder andere Windows-Funktionen wünschen.

	Die folgende Tabelle zeigt einen Vergleich der beiden Varianten:

	Category | HDInsight unter Linux | HDInsight unter Windows 
	---------| -------------------| --------------------
	**Clusterbetriebssystem** | Ubuntu 12.04 Long Term Support (LTS) | Windows Server 2012 R2
	**Clustertyp** | Hadoop | Hadoop, Hbase, Storm
	**Bereitstellung** | Azure-Verwaltungsportal, plattformübergreifende Befehlszeile, Azure PowerShell | Azure Management Portal, plattformübergreifende Befehlszeile, Azure PowerShell, HDInsight .NET SDK
	**Clusterbenutzeroberfläche** | Ambari | Clusterdashboard
	**Remotezugriff** | Secure Shell (SSH) | Remotedesktopprotokoll (RDP)
	

* HDInsight verwendet die **Hortonworks Data Platform (HDP)**-Hadoop-Distribution.

* Apache Hadoop ist ein Softwareframework für die Verwaltung und Analyse von Big Data. HDInsight bietet mehrere Konfigurationen für bestimmte Workloads, oder Sie können <a href="http://azure.microsoft.com/documentation/articles/hdinsight-hadoop-customize-cluster/" target="_blank">Cluster mithilfe von Skriptaktionen anpassen</a>.

	* **Hadoop**: Bietet eine zuverlässige Datenspeicherung mithilfe von [HDFS](#HDFS) und ein einfaches [MapReduce](#mapreduce)-Programmiermodell zum parallelen Verarbeiten und Analysieren von Daten.
	
	* **<a target="_blank" href="http://hbase.apache.org/">HBase</a>**: Eine auf Hadoop basierende NoSQL-Datenbank, die wahlfreien Zugriff und starke Konsistenz für große Mengen unstrukturierter und teilstrukturierter Daten bietet – in einer Dimension von Milliarden von Zeilen multipliziert mit Milliarden von Spalten. Siehe [Übersicht über HBase in HDInsight](hdinsight-hbase-overview.md).
	
	* **<a  target="_blank" href="https://storm.incubator.apache.org/">Apache Storm</a>**: Ein verteiltes Echtzeitberechnungssystem für die schnelle Verarbeitung großer Datenströme. Storm wird als verwalteter Cluster in HDInsight angeboten. Siehe [Analysieren von Echtzeit-Sensordaten mit Storm und Hadoop](hdinsight-storm-sensor-data-analysis.md).

## Aus welchen Komponenten besteht Hadoop?

Zusätzlich zu den vorherigen allgemeinen Konfigurationen sind auch die folgenden einzelnen Komponenten in HDInsight-Clustern enthalten.

* **[Ambari](#ambari)** – Bereitstellung, Verwaltung und Überwachung von Clustern. 

* **[Avro](#avro)** (Microsoft .NET-Bibliothek für Avro) – Datenserialisierung für die Microsoft .NET-Umgebung.

* **[Hive](#hive)** – SQL-ähnliche Abfragen (Structured Query Language).

* **[Mahout](#mahout)** – Maschinelles Lernen.

* **[MapReduce und YARN](#mapreduce)** – Verteilte Verarbeitung und Ressourcenverwaltung.

* **[Oozie](#oozie)** – Workflowverwaltung.

* **[Pig](#pig)** – Einfachere Skripts für MapReduce-Transformationen.

* **[Sqoop](#sqoop)** – Import und Export von Daten.

* **[ZooKeeper](#zookeeper)** – Koordination von Prozessen in verteilten Systemen.

> [AZURE.NOTE]Informationen zu den verschiedenen Komponenten und ihren Versionen finden Sie unter [Neuerungen in den von HDInsight bereitgestellten Hadoop-Clusterversionen][component-versioning].

###<a name="ambari"></a>Ambari

Apache Ambari dient zur Bereitstellung, Verwaltung und Überwachung von Apache Hadoop-Clustern. Es umfasst eine Sammlung intuitiver Operatortools und eine Reihe robuster APIs, welche die Komplexität von Hadoop verbergen und den Betrieb von Clustern vereinfachen. Siehe [Verwalten von HDInsight-Clustern mit Ambari](hdinsight-hadoop-manage-ambari.md) (nur Linux), [Überwachen von Hadoop-Clustern in HDInsight mit der Ambari-API](hdinsight-monitor-use-ambari-api.md) und <a target="_blank" href="https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md">Apache Ambari-API-Referenz</a>.

### <a name="avro"></a>Avro (Microsoft .NET-Bibliothek für Avro)

Die Microsoft .NET-Bibliothek für Avro implementiert das kompakte Apache Avro-Datenaustauschformat für Binärdaten für die Microsoft .NET-Umgebung. Verwendet <a target="_blank" href="http://www.json.org/">JavaScript Object Notation (JSON)</a> zum Definieren eines sprachunabhängigen Schemas, das die Interoperabilität von Sprachen sicherstellt, sodass die in einer Sprache serialisierten Daten in einer anderen Sprache gelesen werden können. Detaillierte Informationen zum Format finden Sie in der <a target=_"blank" href="http://avro.apache.org/docs/current/spec.html">Apache Avro-Spezifikation</a>. Das Format von Avro-Dateien unterstützt das verteilte MapReduce-Programmiermodell. Die Dateien sind "teilbar", d. h. es kann nach einem beliebigen Punkt in der Datei gesucht und ab einem beliebigen Block gelesen werden. Weitere Informationen hierzu finden Sie unter [Serialisieren von Daten mit der Microsoft .NET-Bibliothek für Avro](hdinsight-dotnet-avro-serialization.md).


### <a name="hdfs"></a>HDFS

Hadoop Distributed File System (HDFS) ist ein verteiltes Dateisystem, das zusammen mit MapReduce und YARN den Kern des Hadoop-Ökosystems bildet. HDFS ist das Standard-Dateisystem für Hadoop-Cluster in HDInsight.

### <a name="hive"></a>Hive

<a target="_blank" href="http://hive.apache.org/">Apache Hive</a> ist eine auf Hadoop basierende Data Warehouse-Software, mit der Sie große Datasets in verteilten Speicherformen mithilfe einer SQL-ähnlichen Sprache namens HiveQL abfragen und verwalten können. Hive ist ebenso wie Pig eine auf MapReduce aufsetzende Abstraktion. Beim Ausführen übersetzt Hive Abfragen in eine Serie von MapReduce-Aufträgen. Das Hive-Konzept ähnelt eher einem Managementsystem für relationale Datenbanken als Pig und ist daher besser für strukturierte Daten geeignet. Für unstrukturierte Daten ist Pig die bessere Wahl. Siehe [Verwenden von Hive mit Hadoop in HDInsight](hdinsight-use-hive.md).

### <a name="mahout"></a>Mahout

<a target="_blank" href="https://mahout.apache.org/">Apache Mahout</a> ist eine auf Hadoop basierende skalierbare Bibliothek mit Algorithmen für maschinelles Lernen. Anwendungen für maschinelles Lernen verwenden Grundsätze der Statistik, um aus Daten zu lernen und um zukünftiges Verhalten anhand vergangener Daten zu bestimmen. Siehe [Erstellen von Filmempfehlungen mithilfe von Mahout und Hadoop](hdinsight-mahout.md).

### <a name="mapreduce"></a>MapReduce und YARN
Hadoop MapReduce ist ein Softwareframework zum Schreiben von Anwendungen für die parallele Verarbeitung großer Datasets. Ein MapReduce-Auftrag unterteilt große Datasets und verwaltet die Daten in Schlüssel-Wert-Paaren für die Verarbeitung.

Apache YARN ist die nächste Generation von MapReduce (MapReduce 2.0 oder MRv2) und teilt die beiden Hauptaufgaben von JobTracker - Ressourcenverwaltung und Planung/Überwachung von Aufgaben - in separate Entitäten auf.

Weitere Informationen zu MapReduce finden Sie unter <a target="_blank" href="http://wiki.apache.org/hadoop/MapReduce">MapReduce</a> im Hadoop-Wiki. Weitere Informationen zu YARN finden Sie unter <a target="_blank" href="http://hadoop.apache.org/docs/current/hadoop-yarn/hadoop-yarn-site/YARN.html">Apache Hadoop NextGen MapReduce (YARN)</a>￼.

### <a name="oozie"></a>Oozie
<a target="_blank" href="http://oozie.apache.org/">Apache Oozie</a> ist ein Koordinationssystem für Workflows zur Verwaltung von Hadoop-Aufträgen. Es ist in den Hadoop-Stack integriert und unterstützt Hadoop-Jobs für MapReduce, Pig, Hive und Sqoop. Es kann auch dazu verwendet werden, bestimmte Jobs für ein System zu planen, beispielsweise Java-Programme oder Shellskripts. Siehe [Verwenden eines zeitbasierten Oozie-Koordinators mit Hadoop in HDInsight](hdinsight-use-oozie-coordinator-time.md).

### <a name="pig"></a>Pig

<a  target="_blank" href="http://pig.apache.org/">Apache Pig</a> ist eine allgemeine Plattform zum Anwenden komplexer MapReduce-Transformationen auf extrem große Datasets mithilfe einer einfachen Skriptsprache namens Pig Latin. Pig übersetzt Pig Latin-Skripts für die Ausführung in Hadoop. Sie können benutzerdefinierte Funktionen erstellen, um Pig Latin zu erweitern. Siehe [Verwenden von Pig mit Hadoop zur Analyse von Apache-Protokolldateien](hdinsight-use-pig.md).

### <a name="sqoop"></a>Sqoop
<a  target="_blank" href="http://sqoop.apache.org/">Apache Sqoop</a> ist ein Tool für die möglichst effektive Übertragung großer Datenmengen zwischen Hadoop und relationalen Datenbanken wie SQL oder anderen strukturierten Datenspeichern. Siehe [Verwenden von Sqoop mit Hadoop](hdinsight-use-sqoop.md).


### <a name="zookeeper"></a>ZooKeeper
<a  target="_blank" href="http://zookeeper.apache.org/">Apache Zookeeper</a> koordiniert Prozesse in großen verteilten Systemen mithilfe eines hierarchischen Namespace von Datenregistern (znodes). ZNodes enthalten kleine Mengen an Metainformationen, die für die Koordination von Prozessen erforderlich sind: Status, Speicherort, Konfiguration usw.

## <a name="advantage"></a>Vorzüge von Hadoop in der Cloud

Hadoop in HDInsight ist Teil des Azure Cloud-Ökosystems und bietet eine Vielzahl von Vorteilen, zum Beispiel:

* Automatische Bereitstellung von Hadoop-Clustern. HDInsight-Cluster sind viel einfacher zu erstellen als manuell konfigurierte Hadoop-Cluster. Weitere Informationen finden Sie unter [Bereitstellen von Hadoop-Clustern in HDInsight](hdinsight-provision-clusters.md).

* Modernste Hadoop-Komponenten. Einzelheiten dazu finden Sie hier [
* Neuerungen in den von HDInsight bereitgestellten Hadoop-Clusterversionen?][component-versioning].

* Hohe Verfügbarkeit und Zuverlässigkeit von Clustern. Weitere Details finden Sie unter [Verfügbarkeit und Zuverlässigkeit von Hadoop-Clustern in HDInsight](hdinsight-high-availability.md).

* Effiziente und wirtschaftliche Datenspeicherung im Azure-Blobspeicher, eine Hadoop-kompatible Option. Siehe [Verwenden des Azure-Blobspeichers mit Hadoop in HDInsight](../hdinsight-use-blob-storage.md).

* Integration mit anderen Azure-Diensten, z. B. [Websites](../documentation/services/websites/) und [SQL Database](../documentation/services/sql-database/).

* Niedrige Einstiegskosten. Probieren Sie die [kostenlose Testversion](/pricing/free-trial/) aus oder entdecken Sie die [HDInsight-Preisdetails](/pricing/details/hdinsight/).


Weitere Informationen zu den Vorteilen von Hadoop in HDInsight finden Sie in der [Azure-Features-Seite für HDInsight][marketing-page].



## <a id="resources"></a>Ressourcen zum Veranschaulichen von Big Data-Analysen, Hadoop und HDInsight

Lesen Sie im Anschluss an diese Einführung in Hadoop in HDInsight und die Big Data-Analyse nachstehende Ressourcen.


### HDInsight unter Linux (Vorschau)

* [Erste Schritte mit HDInsight unter Linux](../hdinsight-hadoop-linux-get-started.md) – Ein Lernprogramm für die schnelle Bereitstellung von HDInsight-Hadoop-Clustern unter Linux und Ausführung von Hive-Beispielabfragen.

* [Bereitstellen von HDInsight unter Linux mit benutzerdefinierten Optionen](hdinsight-hadoop-provision-linux-clusters.md) – Erfahren Sie, wie ein HDInsight Hadoop-Cluster unter Linux über das Azure-Verwaltungsportal, die plattformübergreifende Azure-Befehlszeile oder Azure PowerShell mit benutzerdefinierten Optionen bereitgestellt wird.

* [Arbeiten mit HDInsight unter Linux](hdinsight-hadoop-linux-information.md) – Tipps für den Schnelleinstieg in das Arbeiten mit in Azure bereitgestellten Hadoop-Linux-Clustern.

* [Verwalten von HDInsight-Clustern mit Ambari](hdinsight-hadoop-manage-ambari.md) – Erfahren Sie, wie Sie Ihren Linux-basierten Hadoop-Cluster in HDInsight mithilfe von Ambari Web oder der Ambari-REST-API überwachen und verwalten.


### HDInsight unter Windows

* [HDInsight-Dokumentation](http://azure.microsoft.com/documentation/services/hdinsight/) – Die Dokumentationsseite für Azure HDInsight mit Links zu Artikeln, Videos und weiteren Ressourcen.

* [Lernkonzept für HDInsight](hdinsight-learn-map.md) – Eine Einführung in die Hadoop-Dokumentation für HDInsight.

* [Erste Schritte mit Azure HDInsight](../hdinsight-get-started.md) – Lernprogramm für den Schnelleinstieg in das Verwenden von Hadoop in HDInsight.

* [Ausführen der HDInsight-Beispiele](hdinsight-run-samples.md) – Lernprogramm zum Ausführen der Beispiele im Funktionsumfang von HDInsight.
	
* [Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx) – Referenzdokumentation für das HDInsight SDK.


### Apache Hadoop			

* <a target="_blank" href="http://hadoop.apache.org/">Apache Hadoop</a> – Erfahren Sie mehr über die Apache Hadoop-Softwarebibliothek, ein Framework, das die verteilte Verarbeitung großer Datasets in Computerclustern ermöglicht.	

* <a target="_blank" href="http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html">HDFS</a> – Erfahren Sie mehr über die Architektur und das Design von Hadoop Distributed File System (HDFS), dem primären Speichersystem, das von Hadoop-Anwendungen verwendet wird.

* <a target="_blank" href="http://hadoop.apache.org/docs/r1.2.1/mapred_tutorial.html">MapReduce Tutorial</a> – Erfahren Sie mehr über das Programmierframework für das Schreiben von Hadoop-Anwendungen, die riesige Datenmengen auf großen Compute-Knoten parallel verarbeiten.

### SQL Database in Azure	
		
* [Azure SQL-Datenbank](http://msdn.microsoft.com/library/windowsazure/ee336279.aspx) – MSDN-Dokumentation zur SQL-Datenbank.
	
* [Verwaltungsportal für SQL-Datenbank](https://msdn.microsoft.com/library/azure/dn771027.aspx) – Einfaches und unkompliziertes Datenbankverwaltungstool für eine SQL-Datenbank in der Cloud.

* [Adventure Works für SQL-Datenbank](http://msftdbprodsamples.codeplex.com/releases/view/37304) – Downloadseite für eine SQL-Beispieldatenbank.

### Microsoft Business Intelligence (für HDInsight unter Windows)

Bekannte Business-Intelligence-Tools wie Excel, PowerPivot, SQL Server Analysis Services und SQL Server Reporting Services rufen Daten, die in HDInsight integriert sind, entweder über das Add-In Power Query oder den Microsoft Hive-ODBC-Treiber ab, analysieren sie und erstellen einen Bericht.

Diese BI-Tools sind hilfreich für Ihre Big Data-Analyse:
 
* [Verbinden von Excel mit Hadoop über Power Query](hdinsight-connect-excel-power-query.md) –Erfahren Sie, wie Sie Excel mithilfe von Microsoft Power Query mit dem Azure-Speicherkonto verbinden, in dem die Daten für Ihren HDInsight-Cluster gespeichert werden. 

* [Verbinden von Excel über den Microsoft Hive-ODBC-Treiber mit Hadoop](hdinsight-connect-excel-hive-ODBC-driver.md) – Informationen zum Importieren von Daten aus HDInsight mit den Microsoft Hive-ODBC-Treiber.

* [Microsoft-Cloudplattform](http://www.microsoft.com/server-cloud/solutions/business-intelligence/default.aspx) – Entdecken Sie Power BI für Office 365, laden Sie die Testversion von SQL Server herunter, und richten Sie SharePoint Server 2013 und SQL Server BI ein.

* <a target="_blank" https://msdn.microsoft.com/library/hh231701.aspx">Weitere Informationen zu SQL Server Analysis Services</a>.

* <a target="_blank" href="http://msdn.microsoft.com/library/ms159106.aspx">Weitere Informationen zu SQL Server Reporting Services</a>.


### Testen von Hadoop-Lösungen für die Big Data-Analyse (für HDInsight unter Windows)

Wenden Sie Big Data-Analysen auf Daten Ihres Unternehmens an, um sich Einblicke in Ihr Unternehmen zu verschaffen. Hier einige Beispiele:

* [Analysieren von HVAC-Sensordaten](hdinsight-hive-analyze-sensor-data.md) – Erfahren Sie, wie Sie Sensordaten mithilfe von Hive in HDInsight (Hadoop) analysieren und die Daten anschließend in Microsoft Excel visualisieren können. Dieses Beispiel verwendet Hive zur Verarbeitung historischer Daten von Klimaanlagen-Systemen, um festzustellen, welche Systeme Probleme beim Einhalten der eingestellten Temperatur haben.

* [Verwenden von Hive in HDInsight zum Analysieren von Websiteprotokollen](hdinsight-hive-analyze-website-log.md) – Erfahren Sie, wie Sie Websiteprotokolle mithilfe von HiveQL in HDInsight analysieren können, um Einblicke in die Häufigkeit von Besuchen externer Websites an einem Tag sowie eine Zusammenfassung von Websitefehlern zu erhalten, die den Benutzern angezeigt werden.

* [Analysieren von Sensordaten in Echtzeit mit Storm und HBase in HDInsight (Hadoop)](hdinsight-storm-sensor-data-analysis.md) – Erfahren Sie, wie Sie Sensordaten aus Azure Event Hubs mithilfe eines Storm-Clusters in HDInsight verarbeiten und die verarbeiteten Sensordaten anschließend nahezu in Echtzeit in einem webbasierten Dashboard darstellen können.


[marketing-page]: ../services/hdinsight/
[component-versioning]: hdinsight-component-versioning.md
[zookeeper]: http://zookeeper.apache.org/

<!--HONumber=54--> 