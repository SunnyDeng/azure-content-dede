<properties linkid="manage-services-hdinsight-introduction-hdinsight" urlDisplayName="HDInsight Introduction" pageTitle="Introduction to Azure HDInsight | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Apache Hadoop clusters in the cloud, to provide a software framework to manage, analyze, and report on big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Introduction to Azure HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Einführung in Azure HDInsight
=============================

Übersicht
---------

Azure HDInsight ist ein Service, der Apache Hadoop® Cluster in der Cloud bereitstellt, und ein Software-Framework bietet, das auf die Verwaltung, Analyse und Berichterstattung großer Datenmengen ausgelegt ist.

### Große Datenmengen

Es wird ausdrücklich auf große Datenmengen verwiesen, da Daten in immer größeren Volumen, mit stetig höherer Geschwindigkeit und in zahlreichen verschiedenen, unstrukturierten Formaten und semantischem Kontext gesammelt werden. Das Sammeln großer Datenmengen selbst birgt keinen Mehrwert für Unternehmen. Damit große Datenmengen einen Mehrwert in Form neuer Erkenntnisse liefern, müssen nicht nur die richtigen Fragen gestellt und die richtigen Daten zu diesem Thema gesammelt werden. Die Daten müssen darüber hinaus zugänglich, bereinigt, analysiert und auf nützliche Art und Weise dargestellt werden, häufig in Kombination mit Daten aus diversen anderen Quellen, die im sogenannten "Mashup" für Perspektive und Kontext sorgen.

### Apache Hadoop

Apache Hadoop ist ein Software-Framework, das die Verwaltung und Analyse großer Datenmengen unterstützt. Apache Hadoop bietet eine zuverlässige Datenspeicherung über das Hadoop Distributed File System (HDFS) und ein einfaches MapReduce-Programmiermodell zur parallelen Verarbeitung und Analyse der Daten, die in diesem verteilten System gespeichert sind. HDFS nutzt Datenreplikation, um Hardware-Probleme zu beheben, die auftreten, wenn derart komplex verteilte Systeme bereitgestellt werden.

### MapReduce

Um die Analyse unstrukturierter Daten aus verschiedenen Quellen zu vereinfachen, bietet das MapReduce-Programmiermodell eine zentrale Abstraktion, die den Abschluss von Zuordnungs- und Reduzierungsvorgängen bestätigt. Das MapReduce-Programmiermodell betrachtet alle Aufgaben als Berechnungen zu Datensätzen, die aus Schlüssel-Wert-Paaren bestehen. Sowohl Eingabe- als auch Ausgabedateien müssen Datensätze enthalten, die aus nur einem Schlüssel-Wert-Paar bestehen. Diese Einschränkung sorgt dafür, dass MapReduce-Aufträge zusammensetzbar sind.

Andere Hadoop-Projekte wie Pig und Hive werden zusätzlich zum HDFS und dem MapReduce-Framework eingesetzt. Diese Projekte werden verwendet, um eine einfachere Möglichkeit für die Verwaltung eines Clusters bereitzustellen, als es das direkte Arbeiten in MapReduce-Programmen ermöglicht. Mit Pig können Sie z. B. Programme mithilfe der Verfahrenssprache "Pig Latin" schreiben, die auf dem Cluster mit MapReduce-Programmen kompiliert werden. Darüber hinaus bietet das Programm eine reibungslose Kontrolle zur Verwaltung des Datenflusses. Hive ist eine Data-Warehouse-Infrastruktur, die eine tabellarische Abstraktion für Daten in Dateien bietet, die in einem Cluster gespeichert sind. Diese Daten können anschließend über SQL-ähnliche Statements in HiveQL, einer deklarativen Sprache, abgefragt werden.

### HDInsight

Azure HDInsight stellt Apache Hadoop als Service in der Cloud bereit. Es stellt HDFS/MapReduce-Software-Frameworks und verwandte Projekte wie Pig und Hive in einer einfacheren, skalierbaren und kosteneffizienten Umgebung bereit.

Einer der Hauptvorteile von HDInsight ist die Verwaltung und Speicherung der Daten. HDInsight verwendet Azure-Blob-Speicherung als Standarddateisystem. Die Blob-Speicherung und HDFS sind unterschiedliche Dateisysteme, die jeweils für die Datenspeicherung und die Berechnung von Daten optimiert werden.

-   Die Azure-Blob-Speicherung stellt eine hochskalierbare und verfügbare, kostengünstige, langfristige und teilbare Speicheroption für Daten dar, die über HDInsight verarbeitet werden sollen.
-   Die Hadoop-Cluster, die von HDInsight auf HDFS bereitgestellt werden, sind für die Ausführung von MapReduce-Berechnungen der Daten optimiert.

HDInsight-Cluster werden in Azure auf Serverknoten bereitgestellt, um MapReduce-Aufgaben auszuführen. Sobald diese Aufgaben ausgeführt wurden, können sie von Benutzern gelöscht werden. Nachdem die Berechnungen erfolgt sind, wäre es zu teuer, diese Daten weiterhin auf den HDFS-Clustern zu speichern. Die Blob-Speicherung ist eine robuste Azure-Speicherlösung für allgemeine Zwecke. Die Speicherung von Daten in einem Blob-Speicher sorgt dafür, dass Cluster, die für Berechnungen verwendet werden, sicher gelöscht werden können, ohne Benutzerdaten zu verlieren. Die Blob-Speicherung ist jedoch nicht nur eine günstige Lösung: Sie bietet eine umfangreiche HDFS-Dateisystemoberfläche, die Kunden eine reibungslose Leistung bietet, indem sie dafür sorgt, dass alle Komponenten im Hadoop-System standardmäßig direkt mit den Daten arbeiten, die sie verwalten.

HDInsight verwendet Azure PowerShell, um Hadoop-Aufträge zu konfigurieren, auszuführen und nachzubearbeiten. HDInsight bietet darüber hinaus einen Sqoop-Konnektor, der verwendet werden kann, um Daten von einer Azure-SQL-Datenbank in HDFS zu importieren oder Daten von HDFS in eine Azure-SQL-Datenbank zu exportieren.

Microsoft Power Query für Excel ist verfügbar, um Daten von Azure HDInsight oder einem beliebigen HDFS in Excel zu importieren. Dieses Add-On verbessert die Leistung des Self-Service-BI in Excel, indem es das Auffinden von Daten und den Zugriff auf diverse Datenquellen vereinfacht. Zusätzlich zu Power Query steht der Microsoft Hive ODBC-Driver zur Verfügung, um Business-Intelligence-Tools (BI-Tools) wie Excel, SQL Server Analysis Services und Reporting Services zu integrieren, die die End-to-End-Datenanalyse unterstützen und vereinfachen.

### Übersicht

Hier finden Sie eine Beschreibung des Hadoop-Ökosystems, das von HDInsight unterstütz wird, die wichtigsten Benutzerszenarien für HDInsight und Informationen zu weiteren Ressourcen. Folgende Themen werden behandelt:

-   [Das Hadoop-Ökosystem auf HDInsight](#Ecosystem): HDInsight stellt die Implementierung von Pig, Hive und Sqoop bereit und unterstützt andere BI-Tools wie Excel, SQL Server Analysis Services und Reporting Services, die in die Blob-Speicherung/HDFS und das MapReduce-Framework integriert sind. Hierbei kommen entweder Power Query oder Microsoft Hive ODBC-Driver zum Einsatz. Dieser Abschnitt beschreibt, welche Aufgaben diese Programme im Hadoop-Ökosystem übernehmen.

-   [Große Datenmengen in HDInsight](#Scenarios): In diesem Abschnitt erfahren Sie, für welche Art von Aufgaben sich HDInsight eignet.

-   [Resources für HDInsight](#Resources): In diesem Abschnitt informieren wir Sie darüber, wo Sie relevante Ressourcen für weitere Informationen finden.

Das Hadoop-Ökosystem auf Azure
------------------------------

### Einführung

HDInsight bietet ein Framework für die Implementierung der Cloud-basierten Lösung von Microsoft für den Umgang mit großen Datenmengen. Dieses Partner-Ökosystem verwaltet und analysiert große Datenmengen und nutzt dafür die parallelen Verarbeitungskapazitäten des MapReduce-Programmiermodells. Die mit Apache kompatiblen Hadoop-Technologien, die mit HDInsight verwendet werden können, werden in diesem Abschnitt kurz aufgeführt und beschrieben.

HDInsight bietet die Implementierung von Hive und Pig, um Datenverarbeitungs- und Warehousing-Kapazitäten zu integrieren. Die Lösung von Microsoft für große Datenmengen kann mit Microsoft-BI-Tools wie SQL Server Analysis Services, Reporting Services, PowerPivot und Excel integriert werden. Dadurch können Sie direkt eine BI mit Daten ausführen, die über HDInsight in einem Blob-Speicher gespeichert und verwaltet werden.

Andere mit Apache kompatible Technologien und verwandte Technologien, die Teil des Hadoop-Ökosystems sind, und auf Hadoop-Clustern ausgeführt werden können, können ebenfalls heruntergeladen und mit HDInsight verwendet werden. Hierzu gehören Open-Source-Technologien wie Sqoop, die HDFS in relationalen Datenspeichern integrieren.

### Pig

Pig ist eine High-Level-Plattform für die Verarbeitung großer Datenmengen auf Hadoop-Clustern. Pig besteht aus einer Programmiersprache, die auf dem Datenflussmodell beruht, und als Pig Latin bezeichnet wird. Sie unterstützt das Schreiben von Anfragen zu großen Datensätzen und eine Ausführungsumgebung, in der Programme über eine Konsole ausgeführt werden können. Die Pig-Latin-Programme bestehen aus einer Datensatz-Transformationsserie, die unter dem Cover in eine MapReduce-Programmserie umgewandelt werden. Pig-Latin-Abstraktionen bieten komplexere Datenstrukturen als MapReduce und leisten für Hadoop was SQL für Relational Database Management Systeme (RDBMS) leistet. Pig Latin ist umfassend erweiterbar. Benutzerdefinierte Funktionen, die in Java, Python, Ruby, C\# oder JavaScript verfasst wurden, können abgerufen werden, um die einzelnen Verarbeitungspfadschritte für die Zusammenstellung der Analyse zu personalisieren. Weitere Informationen finden Sie unter [Willkommen bei Apache Pig!](http://pig.apache.org/)

### Hive

Hive ist ein verteiltes Data Warehouse, das Daten verwaltet, die in einem HDFS gespeichert sind. Hive ist ein Hadoop-Abfragemodul. Hive eignet sich für Analysten mit SQL-Erfahrung, da es eine SQL-ähnliche Oberfläche und ein relationales Datenmodell bietet. Hive verwendet die Programmiersprache HiveQL; eine Abwandlung von SQL. Hive ist ebenso wie Pig eine Abstraktion, die auf MapReduce aufsetzt. Wenn Hive ausgeführt wird, wandelt es Anfragen in eine Reihe von MapReduce-Aufträgen um. Szenarios in Hive ähneln dem Konzept von RDBMS und können somit für strukturierte Daten verwendet werden. Handelt es sich um unstrukturierte Daten, ist Pig die bessere Wahl. Weitere Informationen finden Sie unter [Willkommen bei Apache Hive!](http://hive.apache.org/)

### Sqoop

Sqoop ist ein Tool für einen möglichst effektiven Transfer großer Datenmengen zwischen Hadoop und relationalen Datenbanken wie SQL oder anderen strukturierten Datenspeichern. Verwenden Sie Sqoop, um Daten von externen strukturierten Datenspeichern in HDFS oder verwandte Systeme wie Hive zu importieren. Sqoop kann außerdem Daten von Hadoop extrahieren und die extrahierten Daten in externe relationale Datenbanken, Enterprise Data Warehouses oder andere strukturierte Datenspeicher exportieren. Weitere Informationen finden Sie auf der Website von [Apache Sqoop](http://sqoop.apache.org/).

### Business-Intelligence-Tools und -Verbindungen

Bekannte Business-Intelligence-Tools wie Excel, PowerPivot, SQL Server Analysis Services und Reporting Services rufen Daten, die in HDInsight integriert sind, entweder über das Add-In Power Query oder den Microsoft Hive ODBC-Driver ab, analysieren sie und erstellen einen Bericht.

-   Microsoft Power Query für Excel kann im [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkID=286689) heruntergeladen werden.

-   Microsoft Hive ODBC Driver kann von [Download Site](http://go.microsoft.com/fwlink/?LinkID=286698) heruntergeladen werden.

-   Informationen zu Analysis Services finden Sie unter [SQL Server 2012 Analysis Services](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-analysis-services.aspx).

-   Informationen zu Reporting Services finden Sie unter [SQL Server 2012 Reporting](http://www.microsoft.com/en-us/sqlserver/solutions-technologies/business-intelligence/reporting.aspx).

Große Datenmengen in HDInsight
------------------------------

Ein Beispielszenario für einen Anwendungsfall für HDInsight ist eine Ad-Hoc-Analyse in Batch-Modus für einen unstrukturierten Datensatz, der auf Azure-Knoten gespeichert ist, und keine regelmäßigen Aktualisierungen erfordert.

Dies trifft auf viele verschiedene Aktivitäten in Unternehmen, der Wissenschaft und in Regierungsbehörden zu. Als Beispiele können die Überwachung der Lieferkette im Einzelhandel, verdächtige Vorgänge im Finanzwesen, Nachfragemuster für öffentliche Einrichtungen und Dienstleistungen, Daten zu Luft- und Wasserqualität von verschiedenen Umweltsensoren oder Verbrechensmuster in Städten aufgeführt werden.

HDInsight (und Hadoop-Technologien im Allgemeinen) eignen sich am besten für die Verwaltung großer protokollierter oder archivierter Datenmengen, die nach der Erfassung nicht regelmäßig aktualisiert werden müssen, jedoch meist zum Zweck einer vollständigen Analyse häufig gelesen werden. Dieses Szenario ergänzt die Verwaltung von Daten, die besser über RDBMS verwaltet werden, wofür eine geringere Datenmenge (Gigabytes anstatt Petabytes) erforderlich ist, und die regelmäßig aktualisiert oder für bestimmte Datenpunkte in einem Datensatz abgerufen werden müssen. RDBMS funktionieren am besten mit strukturierten Daten, die nach einem festgelegten Schema organisiert und gespeichert werden. MapReduce eignet sich vor allem für unstrukturierte Daten ohne vordefiniertes Schema, da es die Daten interpretieren kann, wenn sie verarbeitet werden.

Nächste Schritte: Ressourcen für HDInsight
------------------------------------------

**Microsoft: HDInsight**

-   [HDInsight-Dokumentation](http://go.microsoft.com/fwlink/?LinkID=285601): Dokumentationsseite für Azure HDInsight mit Links auf Artikel Videos und weitere Ressourcen.

-   [Erste Schritte mit Azure HDInsight](/de-de/manage/services/hdinsight/get-started-hdinsight/): Lernprogramm für den schnellen Einstieg in HDInsight.

-   [Ausführen der HDInsight-Beispiele](/de-de/manage/services/hdinsight/howto-run-samples/): Lernprogramm zum Ausführen der Beispiele die mit HDInsight geliefert werden.

-   [Große Datenmengen in Azure](http://www.windowsazure.com/de-de/home/scenarios/big-data/): Beispiele für große Datenmengen, die zeigen, was Sie mit Azure machen können.

-   [Azure HDInsight SDK](http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx): Referenzdokumentation für die HDInsight SDK.

**Microsoft: Windows und SQL-Datenbank**

-   [Azure Homepage](https://www.windowsazure.com/en-us/): Szenarios, Anmeldung für kostenlose Testversion, Entwicklungstools und Dokumentation, die Sie benötigen, um mit dem Aufbau der Anwendung zu beginnen.

-   [Azure SQL-Datenbank](http://msdn.microsoft.com/de-de/library/windowsazure/ee336279.aspx): MSDN-Dokumentation für SQL-Datenbank.

-   [Managementportal für SQL-Datenbank](http://msdn.microsoft.com/de-de/library/windowsazure/gg442309.aspx): Einfaches und unkompliziertes Datenbankmanagementtool für die Verwaltung einer SQL-Datenbank in der Cloud.

-   [Adventure Works für SQL-Datenbank](http://msftdbprodsamples.codeplex.com/releases/view/37304): Download-Seite für SQL-Beispieldatenbank.

**Microsoft: Business Intelligence**

-   [Verbinden von Excel mit HDInsight über Power Query](/de-de/manage/services/hdinsight/connect-excel-with-power-query/): Erfahren Sie, wie Sie mithilfe von Microsoft Power Query Excel mit dem Azure-Speicherkonto verbinden, in dem die Daten für Ihren HDInsight-Cluster gespeichert werden.

-   [Verbinden von Excel über den Microsoft Hive ODBC Driver mit HDInsight](/de-de/manage/services/hdinsight/connect-excel-with-hive-ODBC/): Erfahren Sie, wie Sie Daten von Azure HDInsight über den Microsoft Hive ODBC-Driver importieren können.

-   [Microsoft BI PowerPivot](http://www.microsoft.com/en-us/bi/PowerPivot.aspx): Downloads und Informationen zu leistungsstarken Datenmashups und Tools zum Durchsuchen von Daten.

-   [SQL Server 2012 Analysis Services](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-analysis-services.aspx): Laden Sie eine Evaluierungsversion von SQL Server 2012 herunter und erfahren Sie, wie Sie umfassende Analyselösungen auf Unternehmensebene erstellen, die wichtige Kenntnisse liefern.

-   [SQL Server 2012 Reporting](http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-reporting-services.aspx): Laden Sie eine Evaluierungsversion von SQL Server 2012 herunter, und erfahren Sie, wie Sie eine umfassende, hochskalierbare Lösung erstellen, die unternehmensübergreifende Entscheidungen in Echtzeit ermöglichen.

**Apache Hadoop**:

-   [Apache Hadoop](http://hadoop.apache.org/): Erfahren Sie mehr über die Apache Hadoop-Softwarebibliothek, ein Framework, das die verteilte Verarbeitung großer Datensätze auf Clustern verfügbarer Computer ermöglicht.

-   [HDFS](http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html): Erfahren Sie mehr über die Architektur und das Design von Hadoop Distributed File System (HDFS), dem primären Speichersystem, das von Hadoop-Anwendungen verwendet wird.

-   [MapReduce](http://mapreduce.org/): Erfahren Sie mehr über das Programmierframework für das Schreiben von Hadoop-Anwendungen, die parallel große Datenmengen auf großen Cluster-Serverknoten verarbeiten.


