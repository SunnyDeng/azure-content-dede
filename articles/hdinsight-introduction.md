<properties linkid="manage-services-hdinsight-introduction-hdinsight" urlDisplayName="HDInsight Introduction" pageTitle="Introduction to Hadoop in HDInsight | Azure" metaKeywords="" description="Learn how Azure HDInsight uses Apache Hadoop clusters in the cloud, to provide a software framework to manage, analyze, and report on big data." metaCanonical="" services="hdinsight" documentationCenter="" title="Introduction to Hadoop in HDInsight" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Einführung in Hadoop in HDInsight

## Übersicht

Azure HDInsight ist ein Dienst, der Apache™ Hadoop®-Cluster in der Cloud sowie ein Software-Framework bereitstellt, das für die Verwaltung, Analyse und Berichterstellung über Big Data konzipiert wurde.

### Große Datenmengen

Es wird ausdrücklich auf große Datenmengen verwiesen, da Daten in immer größeren Volumen, mit stetig höherer Geschwindigkeit und in zahlreichen verschiedenen, unstrukturierten Formaten und semantischem Kontext gesammelt werden. Das Sammeln großer Datenmengen selbst birgt keinen Mehrwert für Unternehmen. Damit große Datenmengen einen Mehrwert in Form neuer Erkenntnisse liefern, müssen nicht nur die richtigen Fragen gestellt und die richtigen Daten zu diesem Thema gesammelt werden. Die Daten müssen darüber hinaus zugänglich, bereinigt, analysiert und auf nützliche Art und Weise dargestellt werden, häufig in Kombination mit Daten aus diversen anderen Quellen, die im sogenannten "Mashup" für Perspektive und Kontext sorgen.

### Apache Hadoop

Apache Hadoop ist ein Software-Framework, das die Verwaltung und Analyse großer Datenmengen unterstützt. Apache Hadoop bietet eine zuverlässige Datenspeicherung über das Hadoop Distributed File System (HDFS) und ein einfaches MapReduce-Programmiermodell zur parallelen Verarbeitung und Analyse der Daten, die in diesem verteilten System gespeichert sind. HDFS nutzt Datenreplikation, um Hardware-Probleme zu beheben, die auftreten, wenn derart komplex verteilte Systeme bereitgestellt werden.

### MapReduce und YARN

Um die Analyse unstrukturierter Daten aus verschiedenen Quellen zu vereinfachen, bietet das MapReduce-Programmiermodell eine zentrale Abstraktion, die den Abschluss von Zuordnungs- und Reduzierungsvorgängen bestätigt. Das MapReduce-Programmiermodell betrachtet alle Aufgaben als Berechnungen zu Datensätzen, die aus Schlüssel-Wert-Paaren bestehen. Sowohl Eingabe- als auch Ausgabedateien müssen Datensätze enthalten, die aus nur einem Schlüssel-Wert-Paar bestehen. Diese Einschränkung sorgt dafür, dass MapReduce-Aufträge zusammensetzbar sind.

Andere Hadoop-Projekte wie Pig und Hive werden zusätzlich zum HDFS und dem MapReduce-Framework eingesetzt. Diese Projekte werden verwendet, um eine einfachere Möglichkeit für die Verwaltung eines Clusters bereitzustellen, als es das direkte Arbeiten in MapReduce-Programmen ermöglicht. Mit Pig können Sie z. B. Programme mithilfe der Verfahrenssprache "Pig Latin" schreiben, die auf dem Cluster mit MapReduce-Programmen kompiliert werden. Darüber hinaus bietet das Programm eine reibungslose Kontrolle zur Verwaltung des Datenflusses. Hive ist eine Data-Warehouse-Infrastruktur, die eine tabellarische Abstraktion für Daten in Dateien bietet, die in einem Cluster gespeichert sind. Diese Daten können anschließend über SQL-ähnliche Statements in HiveQL, einer deklarativen Sprache, abgefragt werden.

### HDInsight

Azure HDInsight stellt Apache Hadoop als Service in der Cloud bereit. Dazu gehört das HDFS/MapReduce-Software-Framework und zugehörige Projekte wie Pig, Hive und Oozie in einer vereinfachten, skalierbareren und kostengünstigeren Umgebung.

Den von HDInsight bereitgestellten Hadoop-Clustern wurde ein zweiter Hauptknoten hinzugefügt, um die Verfügbarkeit des Diensts zu erhöhen. Standardimplementierungen von Hadoop-Clustern verfügen in der Regel nur über einen Hauptknoten. Durch Hinzufügung eines zweiten Hauptknotens wird in HDInsight diese einzelne Fehlerquelle beseitigt. Beim Wechsel zu einer neuen HV-Clusterkonfiguration ändert sich der Preis des Clusters nur, wenn der Kunde Cluster mit einem sehr großen Stammknoten einrichtet.

Einer der Hauptvorteile von HDInsight ist die Verwaltung und Speicherung der Daten. HDInsight verwendet Azure-Blob-Speicherung als Standarddateisystem. Die Blob-Speicherung und HDFS sind unterschiedliche Dateisysteme, die jeweils für die Datenspeicherung und die Berechnung von Daten optimiert werden.

-   Die Azure-Blob-Speicherung stellt eine hochskalierbare und verfügbare, kostengünstige, langfristige und teilbare Speicheroption für Daten dar, die über HDInsight verarbeitet werden sollen.
-   Die Hadoop-Cluster, die von HDInsight auf HDFS bereitgestellt werden, sind für die Ausführung von MapReduce-Berechnungen der Daten optimiert.

HDInsight-Cluster werden in Azure auf Serverknoten bereitgestellt, um MapReduce-Aufgaben auszuführen. Sobald diese Aufgaben ausgeführt wurden, können sie von Benutzern gelöscht werden. Nachdem die Berechnungen erfolgt sind, wäre es zu teuer, diese Daten weiterhin in den HDFS-Clustern zu speichern. Blob-Speicher ist eine robuste Azure-Speicherlösung für allgemeine Zwecke. Die Speicherung von Daten in einem Blob-Speicher sorgt dafür, dass Cluster, die für Berechnungen verwendet werden, sicher gelöscht werden können, ohne Benutzerdaten zu verlieren. Die Blob-Speicherung ist jedoch nicht nur eine günstige Lösung: Sie bietet eine umfangreiche HDFS-Dateisystemoberfläche, die Kunden eine reibungslose Leistung bietet, indem sie dafür sorgt, dass alle Komponenten im Hadoop-System standardmäßig direkt mit den Daten arbeiten, die sie verwalten.

HDInsight verwendet Azure PowerShell, um Hadoop-Aufträge zu konfigurieren, auszuführen und nachzubearbeiten. HDInsight bietet darüber hinaus einen Sqoop-Konnektor, der verwendet werden kann, um Daten von einer Azure-SQL-Datenbank in HDFS zu importieren oder Daten von HDFS in eine Azure-SQL-Datenbank zu exportieren.

YARN ist nun ebenfalls in HDInsight verfügbar. Dieses neue, vielseitige und verteilte Framework für die Anwendungsverwaltung ersetzt das herkömmliche Apache Hadoop MapReduce-Framework für die Datenverarbeitung in Hadoop-Clustern. Es dient quasi als Hadoop-Betriebssystem und macht aus der einem einzigen Zweck dienenden Datenplattform für die Stapelverarbeitung eine Plattform mit vielerlei Verwendungszwecken, die eine stapelweise, interaktive Online- und Streamverarbeitung unterstützt. Dieses neue Management-Framework verbessert die Skalierbarkeit und Clusternutzung im Einklang mit wichtigen Kriterien wie Kapazitätsgarantien, Fairness und Servicelevel-Vereinbarungen.

Microsoft Power Query für Excel ist verfügbar, um Daten von Azure HDInsight oder einem beliebigen HDFS in Excel zu importieren. Dieses Add-On verbessert die Leistung des Self-Service-BI in Excel, indem es das Auffinden von Daten und den Zugriff auf diverse Datenquellen vereinfacht. Zusätzlich zu Power Query steht der Microsoft Hive ODBC-Driver zur Verfügung, um Business-Intelligence-Tools (BI-Tools) wie Excel, SQL Server Analysis Services und Reporting Services zu integrieren, die die End-to-End-Datenanalyse unterstützen und vereinfachen.

### Übersicht

Hier finden Sie eine Beschreibung des Hadoop-Ökosystems, das von HDInsight unterstütz wird, die wichtigsten Benutzerszenarien für HDInsight und Informationen zu weiteren Ressourcen. Folgende Themen werden behandelt:

-   [Das Hadoop-Ökosystem auf HDInsight][]: HDInsight enthält Implementierungen von Pig, Hive, Sqoop, Oozie und Ambari und unterstützt weitere BI-Tools wie z. B. Excel, SQL Server Analysis Services und Berichterstellungsdienste, die entweder über Power Query oder über den Microsoft Hive ODBC-Treiber mit dem Blob storage/HDFS und dem MapReduce-Framework verbunden sind. Dieser Abschnitt beschreibt, welche Aufgaben diese Programme im Hadoop-Ökosystem übernehmen.

-   [Große Datenmengen in HDInsight][]: In diesem Abschnitt erfahren Sie, für welche Art von Aufgaben sich HDInsight eignet.

-   [Resources für HDInsight][]: In diesem Abschnitt informieren wir Sie darüber, wo Sie relevante Ressourcen für weitere Informationen finden.

## <a name="Ecosystem">Das Hadoop-Ökosystem auf Azure </a>

### Einführung

HDInsight bietet ein Framework für die Implementierung der Cloud-basierten Lösung von Microsoft für den Umgang mit großen Datenmengen. Dieses Partner-Ökosystem verwaltet und analysiert große Datenmengen und nutzt dafür die parallelen Verarbeitungskapazitäten des MapReduce-Programmiermodells. Die mit Apache kompatiblen Hadoop-Technologien, die mit HDInsight verwendet werden können, werden in diesem Abschnitt kurz aufgeführt und beschrieben.

HDInsight bietet die Implementierung von Hive und Pig, um Datenverarbeitungs- und Warehousing-Kapazitäten zu integrieren. Die Lösung von Microsoft für große Datenmengen kann mit Microsoft-BI-Tools wie SQL Server Analysis Services, Reporting Services, PowerPivot und Excel integriert werden. Dadurch können Sie direkt eine BI mit Daten ausführen, die über HDInsight in einem Blob-Speicher gespeichert und verwaltet werden.

Andere mit Apache kompatible Technologien und verwandte Technologien, die Teil des Hadoop-Ökosystems sind, und auf Hadoop-Clustern ausgeführt werden können, können ebenfalls heruntergeladen und mit HDInsight verwendet werden. Hierzu gehören Open-Source-Technologien wie Sqoop, die HDFS in relationalen Datenspeichern integrieren.

### Pig

Pig ist eine High-Level-Plattform für die Verarbeitung großer Datenmengen auf Hadoop-Clustern. Pig besteht aus einer Programmiersprache, die auf dem Datenflussmodell beruht, und als Pig Latin bezeichnet wird. Sie unterstützt das Schreiben von Anfragen zu großen Datensätzen und eine Ausführungsumgebung, in der Programme über eine Konsole ausgeführt werden können. Die Pig-Latin-Programme bestehen aus einer Datensatz-Transformationsserie, die unter dem Cover in eine MapReduce-Programmserie umgewandelt werden. Pig-Latin-Abstraktionen bieten komplexere Datenstrukturen als MapReduce und leisten für Hadoop was SQL für Relational Database Management Systeme (RDBMS) leistet. Pig Latin ist umfassend erweiterbar. Benutzerdefinierte Funktionen, die in Java, Python, Ruby, C# oder JavaScript verfasst wurden, können abgerufen werden, um die einzelnen Verarbeitungspfadschritte für die Zusammenstellung der Analyse zu personalisieren. Weitere Informationen finden Sie unter [Willkommen bei Apache Pig!][]

### Hive

Hive ist ein verteiltes Data Warehouse, das Daten verwaltet, die in einem HDFS gespeichert sind. Hive ist ein Hadoop-Abfragemodul. Hive eignet sich für Analysten mit SQL-Erfahrung, da es eine SQL-ähnliche Oberfläche und ein relationales Datenmodell bietet. Hive verwendet die Programmiersprache HiveQL; eine Abwandlung von SQL. Hive ist ebenso wie Pig eine Abstraktion, die auf MapReduce aufsetzt. Wenn Hive ausgeführt wird, wandelt es Anfragen in eine Reihe von MapReduce-Aufträgen um. Szenarios in Hive ähneln dem Konzept von RDBMS und können somit für strukturierte Daten verwendet werden. Handelt es sich um unstrukturierte Daten, ist Pig die bessere Wahl. Weitere Informationen finden Sie unter [Willkommen bei Apache Hive!][]

### Sqoop

Sqoop ist ein Tool für einen möglichst effektiven Transfer großer Datenmengen zwischen Hadoop und relationalen Datenbanken wie SQL oder anderen strukturierten Datenspeichern. Verwenden Sie Sqoop, um Daten von externen strukturierten Datenspeichern in HDFS oder verwandte Systeme wie Hive zu importieren. Sqoop kann außerdem Daten von Hadoop extrahieren und die extrahierten Daten in externe relationale Datenbanken, Enterprise Data Warehouses oder andere strukturierte Datenspeicher exportieren. Weitere Informationen finden Sie auf der [Apache Sqoop][]-Website.

### Oozie

Apache Oozie ist ein Workflow-/Koordinationssystem zur Verwaltung von Hadoop-Jobs. Es ist in den Hadoop-Stapel integriert und unterstützt Hadoop-Jobs für Apache MapReduce, Apache Pig, Apache Hive und Apache Sqoop. Es kann auch dazu verwendet werden, bestimmte Jobs für ein System zu planen, beispielsweise Java-Programme oder Shellskripts.

### Ambari

Apache Ambari dient zur Bereitstellung, Verwaltung und Überwachung von Apache Hadoop-Clustern. Es umfasst eine Sammlung intuitiver Operatortools und eine Reihe robuster APIs, welche die Komplexität von Hadoop verbergen und den Betrieb von Clustern vereinfachen. Weitere Informationen über die APIs finden Sie unter [Ambari API Reference][] (Referenz zur Ambari-API, in englischer Sprache). HDInsight unterstützt derzeit nur das Ambari-Überwachungsfeature. Ambari API Version 1.0 wird von den HDInsight-Clusterversionen 2.1 und 3.0 unterstützt. Weitere Informationen zu Ambari finden Sie auf der [Apache Ambari][]-Website.

### Microsoft Avro-Bibliothek

Die Microsoft Avro-Bibliothek implementiert das Apache Avro-Datenserialisierungssystem für die Microsoft .NET-Umgebung. Apache Avro stellt ein kompaktes binäres Datenaustauschformat für die Serialisierung bereit. Unter Verwendung von [JSON][] definiert es ein sprachunabhängiges Schema, das die Interoperabilität von Sprachen sicherstellt. Die in einer Sprache serialisierten Daten können in einer anderen Sprache gelesen werden. Derzeit werden C, C++, C#, Java, PHP, Python und Ruby unterstützt. Detaillierte Informationen über das Format finden Sie in der [Apache Avro-Spezifikation][]. Beachten Sie, dass die aktuelle Version der Microsoft Avro-Bibliothek den Remoteprozeduraufruf (RPC)-Teil dieser Spezifikation nicht unterstützt.

Das Apache Avro-Serialisierungsformat wird verbreitet in Azure HDInsight und anderen Apache Hadoop-Umgebungen eingesetzt. Avro bietet eine einfache Methode zur Darstellung komplexer Datenstrukturen in einem Hadoop MapReduce-Job. Das Format von Avro-Dateien ist dafür ausgelegt, das verteilte MapReduce-Programmiermodell zu unterstützen. Das wichtigste Merkmal, das die Verteilung ermöglicht, ist die "Teilbarkeit" der Dateien; hier bedeutet "teilbar", dass nach einem beliebigen Punkt in der Datei gesucht und ab einem bestimmten Block gelesen werden kann. Weitere Informationen finden Sie unter [Serialisieren von Daten mit der Microsoft Avro-Bibliothek][].

### Business-Intelligence-Tools und -Verbindungen

Bekannte Business-Intelligence-Tools wie Excel, PowerPivot, SQL Server Analysis Services und Reporting Services rufen Daten, die in HDInsight integriert sind, entweder über das Add-In Power Query oder den Microsoft Hive ODBC-Driver ab, analysieren sie und erstellen einen Bericht.

-   Microsoft Power Query für Excel kann im [Microsoft Download Center][] heruntergeladen werden.

-   Microsoft Hive ODBC Driver kann von [Download Site][] heruntergeladen werden.

-   Informationen zu Analysis Services finden Sie unter [SQL Server 2012 Analysis Services][].

-   Informationen zu Reporting Services finden Sie unter [SQL Server 2012 Reporting][].

## <a name="Scenarios"></a>Große Datenmengen in HDInsight

Ein Beispielszenario für einen Anwendungsfall für HDInsight ist eine Ad-Hoc-Analyse in Batch-Modus für einen unstrukturierten Datensatz, der auf Azure-Knoten gespeichert ist, und keine regelmäßigen Aktualisierungen erfordert.

Dies trifft auf viele verschiedene Aktivitäten in Unternehmen, der Wissenschaft und in Regierungsbehörden zu. Als Beispiele können die Überwachung der Lieferkette im Einzelhandel, verdächtige Vorgänge im Finanzwesen, Nachfragemuster für öffentliche Einrichtungen und Dienstleistungen, Daten zu Luft- und Wasserqualität von verschiedenen Umweltsensoren oder Verbrechensmuster in Städten aufgeführt werden.

HDInsight (und Hadoop-Technologien im Allgemeinen) eignen sich am besten für die Verwaltung großer protokollierter oder archivierter Datenmengen, die nach der Erfassung nicht regelmäßig aktualisiert werden müssen, jedoch meist zum Zweck einer vollständigen Analyse häufig gelesen werden. Dieses Szenario ergänzt die Verwaltung von Daten, die besser über RDBMS verwaltet werden, wofür eine geringere Datenmenge (Gigabytes anstatt Petabytes) erforderlich ist, und die regelmäßig aktualisiert oder für bestimmte Datenpunkte in einem Datensatz abgerufen werden müssen. RDBMS funktionieren am besten mit strukturierten Daten, die nach einem festgelegten Schema organisiert und gespeichert werden. MapReduce eignet sich vor allem für unstrukturierte Daten ohne vordefiniertes Schema, da es die Daten interpretieren kann, wenn sie verarbeitet werden.

## <a name="Resources"></a>Nächste Schritte: Ressourcen für HDInsight

**Microsoft: HDInsight**

-   [HDInsight-Dokumentation][]: Dokumentationsseite für Azure HDInsight mit Links auf Artikel Videos und weitere Ressourcen.

-   [HDInsight-Versionshinweise][]: Hinweise zu den neuesten Versionen.

-   [Erste Schritte mit Azure HDInsight][]: Lernprogramm für den schnellen Einstieg in HDInsight.

-   [Ausführen der HDInsight-Beispiele][]: Lernprogramm zum Ausführen der Beispiele die mit HDInsight geliefert werden.

-   [Große Datenmengen in Azure][]: Beispiele für große Datenmengen, die zeigen, was Sie mit Azure machen können.

-   [Azure HDInsight SDK][]: Referenzdokumentation für die HDInsight SDK.

**Microsoft: Windows und SQL-Datenbank**

-   [Azure Homepage][]: Szenarios, Anmeldung für kostenlose Testversion, Entwicklungstools und Dokumentation, die Sie benötigen, um mit dem Aufbau der Anwendung zu beginnen.

-   [Azure SQL-Datenbank][]: MSDN-Dokumentation für SQL-Datenbank.

-   [Managementportal für SQL-Datenbank][]: Einfaches und unkompliziertes Datenbankmanagementtool für die Verwaltung einer SQL-Datenbank in der Cloud.

-   [Adventure Works für SQL-Datenbank][]: Download-Seite für SQL-Beispieldatenbank.

**Microsoft: Business Intelligence**

-   [Verbinden von Excel mit HDInsight über Power Query][]: Erfahren Sie, wie Sie mithilfe von Microsoft Power Query Excel mit dem Azure-Speicherkonto verbinden, in dem die Daten für Ihren HDInsight-Cluster gespeichert werden.

-   [Verbinden von Excel über den Microsoft Hive ODBC Driver mit HDInsight][]: Erfahren Sie, wie Sie Daten von Azure HDInsight über den Microsoft Hive ODBC-Driver importieren können.

-   [Microsoft BI PowerPivot][]: Downloads und Informationen zu leistungsstarken Datenmashups und Tools zum Durchsuchen von Daten.

-   [SQL Server 2012 Analysis Services][]: Laden Sie eine Evaluierungsversion von SQL Server 2012 herunter und erfahren Sie, wie Sie umfassende Analyselösungen auf Unternehmensebene erstellen, die wichtige Kenntnisse liefern.

-   [SQL Server 2012 Reporting][1]: Laden Sie eine Evaluierungsversion von SQL Server 2012 herunter, und erfahren Sie, wie Sie eine umfassende, hochskalierbare Lösung erstellen, die unternehmensübergreifende Entscheidungen in Echtzeit ermöglichen.

**Apache Hadoop**:

-   [Apache Hadoop][]: Erfahren Sie mehr über die Apache Hadoop-Softwarebibliothek, ein Framework, das die verteilte Verarbeitung großer Datensätze auf Clustern verfügbarer Computer ermöglicht.

-   [HDFS][]: Erfahren Sie mehr über die Architektur und das Design von Hadoop Distributed File System (HDFS), dem primären Speichersystem, das von Hadoop-Anwendungen verwendet wird.

-   [MapReduce][]: Erfahren Sie mehr über das Programmierframework für das Schreiben von Hadoop-Anwendungen, die parallel große Datenmengen auf großen Cluster-Serverknoten verarbeiten.

  [Das Hadoop-Ökosystem auf HDInsight]: #Ecosystem
  [Große Datenmengen in HDInsight]: #Scenarios
  [Resources für HDInsight]: #Resources
  [Willkommen bei Apache Pig!]: http://pig.apache.org/
  [Willkommen bei Apache Hive!]: http://hive.apache.org/
  [Apache Sqoop]: http://sqoop.apache.org/
  [Ambari API Reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [Apache Ambari]: http://ambari.apache.org/
  [JSON]: http://www.json.org
  [Apache Avro-Spezifikation]: http://avro.apache.org/docs/current/spec.html
  [Serialisieren von Daten mit der Microsoft Avro-Bibliothek]: ../hdinsight-dotnet-avro-serialization/
  [Microsoft Download Center]: http://go.microsoft.com/fwlink/?LinkID=286689
  [Download Site]: http://go.microsoft.com/fwlink/?LinkID=286698
  [SQL Server 2012 Analysis Services]: http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-analysis-services.aspx
  [SQL Server 2012 Reporting]: http://www.microsoft.com/en-us/sqlserver/solutions-technologies/business-intelligence/reporting.aspx
  [HDInsight-Dokumentation]: http://go.microsoft.com/fwlink/?LinkID=285601
  [HDInsight-Versionshinweise]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-release-notes/
  [Erste Schritte mit Azure HDInsight]: ../hdinsight-get-started/
  [Ausführen der HDInsight-Beispiele]: ../hdinsight-run-samples/
  [Große Datenmengen in Azure]: http://azure.microsoft.com/en-us/solutions/big-data/
  [Azure HDInsight SDK]: http://msdnstage.redmond.corp.microsoft.com/en-us/library/dn479185.aspx
  [Azure Homepage]: http://azure.microsoft.com/en-us/
  [Azure SQL-Datenbank]: http://msdn.microsoft.com/en-us/library/windowsazure/ee336279.aspx
  [Managementportal für SQL-Datenbank]: http://msdn.microsoft.com/en-us/library/windowsazure/gg442309.aspx
  [Adventure Works für SQL-Datenbank]: http://msftdbprodsamples.codeplex.com/releases/view/37304
  [Verbinden von Excel mit HDInsight über Power Query]: ../hdinsight-connect-excel-power-query/
  [Verbinden von Excel über den Microsoft Hive ODBC Driver mit HDInsight]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Microsoft BI PowerPivot]: http://www.microsoft.com/en-us/bi/PowerPivot.aspx
  [1]: http://www.microsoft.com/sqlserver/en/us/solutions-technologies/business-intelligence/SQL-Server-2012-reporting-services.aspx
  [Apache Hadoop]: http://hadoop.apache.org/
  [HDFS]: http://hadoop.apache.org/docs/r0.18.1/hdfs_design.html
  [MapReduce]: http://mapreduce.org/
