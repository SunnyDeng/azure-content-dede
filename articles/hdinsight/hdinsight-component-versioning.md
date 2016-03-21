<properties
	pageTitle="Neuigkeiten zu den Hadoop-Clusterversionen in HDInsight | Microsoft Azure"
	description="HDInsight unterstützt mehrere bereitstellbare Hadoop-Clusterversionen. Entdecken Sie die unterstützten Versionen von Hadoop und der HortonWorks Data Platform (HDP)."
	services="hdinsight"
	editor="cgronlun"
	manager="paulettm"
	authors="mumian"
	tags="azure-portal"
	documentationCenter=""/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/29/2016"
	ms.author="jgao"/>


#Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen

##HDInsight-Versionen und Hadoop-Komponenten
Azure HDInsight unterstützt mehrere Hadoop-Clusterversionen, die jederzeit bereitgestellt werden können. Jede ausgewählte Version erstellt eine bestimmte Version der HDP-Distribution (Hortonworks Data Platform) und eine Reihe von Komponenten innerhalb dieser Distribution. Die den HDInsight-Clusterversionen zugeordneten Komponentenversionen sind in der folgenden Tabelle aufgeführt. Als Standardclusterversion wird von Azure HDInsight derzeit Version 3.2 auf Basis von HDP 2.2 (ab 03.12.2015) verwendet.


Komponente|HDInsight-Version 3.3 | HDInsight-Version 3.2 (Standard)|HDInsight-Version 3.1 |HDInsight Version 3,0|
---|---|---|---|---
Hortonworks Data Platform|2\.3|2\.2|2\.1.7|2,0|
Apache Hadoop und YARN|2\.7.1|2\.6.0|2\.4.0|2\.2.0|
Apache Tez|0\.7.0 | 0\.5.2|0\.4.0||
Apache Pig|0\.15.0|0\.14.0|0\.12.1|0\.12.0|
Apache Hive und HCatalog|1\.2.1|0\.14.0|0\.13.1|0\.12.0|
Apache HBase |1\.1.1|0\.98.4|0\.98.0||
Apache Sqoop|1\.4.6|1\.4.5|1\.4.4|1\.4.4|1\.4.3
Apache Oozie|4\.2.0|4\.1.0|4\.0.0|4\.0.0|
Apache Zookeeper|3\.4.6|3\.4.6|3\.4.5|3\.4.5|
Apache Storm|0\.10.0|0\.9.3|0\.9.1||
Apache Mahout|0\.9.0+|0\.9.0|0\.9.0||
Apache Phoenix|4\.4.0|4\.2.0|4\.0.0.2.1.7.0-2162||
Apache Spark|1\.5.2 (nur für Linux/experimenteller Build)|1\.3.1 (nur für Windows)|||


**Abrufen von Informationen zur aktuellen Komponentenversion**

Die mit den HDInsight-Clusterversionen verknüpften Komponentenversionen können sich bei künftigen Updates für HDInsight ändern. Eine Möglichkeit, die verfügbaren Komponenten zu bestimmen und zu prüfen, welche Versionen für einen Cluster verwendet werden, ist die Ambari REST-API. Über den **GetComponentInformation**-Befehl können Informationen zu einer Dienstkomponente abgerufen werden. Ausführliche Informationen finden Sie in der [Ambari-Dokumentation][ambari-docs]. Sie können sich auch per Remotedesktop bei einem Cluster anmelden und den Inhalt des Verzeichnisses "C:\\apps\\dist" direkt prüfen, um diese Informationen abzurufen.


**Versionshinweise**

In den [HDInsight-Versionshinweisen](hdinsight-release-notes.md) finden Sie zusätzliche Anmerkungen zu den aktuellen Versionen von HDInsight.

### Auswählen einer Version beim Erstellen eines HDInsight-Clusters

Wenn Sie einen Cluster über die HDInsight Windows PowerShell-Cmdlets oder das HDInsight .NET SDK erstellen, können Sie die Version für das HDInsight Hadoop-Cluster im Azure-Portal auf dem Blatt **Optionale Konfiguration** über das Dropdownmenü **HDInsight-Version** auswählen.

##Wichtige Features
Im Folgenden finden Sie einige der wichtigsten Features der HDInsight-Plattform:

- **Spark** – Apache Spark ist ein Open-Source-Framework für die Parallelverarbeitung, das die arbeitsspeicherinterne Verarbeitung unterstützt, um die Leistung von Anwendungen zur Analyse von großen Datenmengen zu steigern. Dank seiner arbeitsspeicherinternen Datenverarbeitungsfunktionen eignet sich Spark besonders für iterative Algorithmen beim maschinellen Lernen und für Graphberechnungen.

	Spark kann auch für die herkömmliche datenträgerbasierte Datenverarbeitung genutzt werden. Spark hat einen Vorteil gegenüber dem herkömmlichen MapReduce-Framework, da in den Zwischenstufen Schreibvorgänge auf den Datenträger vermieden werden. Außerdem ist Spark kompatibel mit dem Hadoop Distributed File System (HDFS) und Azure-Blob-Speicher, damit die vorhandenen Daten problemlos über Spark bearbeitet werden können.

	Spark kann auch per „Script Action“ hinzugefügt werden. Mit Script Action wird entweder dem HDInsight 3.2-Cluster Spark 1.2.0 oder dem HDInsight 3.1-Cluster Spark 1.0.2 hinzugefügt. Weitere Informationen finden Sie unter [Installieren und Verwenden von Spark in HDInsight-Hadoop-Clustern](hdinsight-hadoop-spark-install.md).


- **Storm** – Storm in Azure HDInsight ist jetzt allgemein verfügbar. Sie können mit nur wenigen Klicks und innerhalb weniger Minuten schnell und einfach Echtzeitanalysen bereitstellen. Apache Storm in Azure HDInsight ist ein Open-Source-Projekt des Apache Hadoop-Systems, das Zugriff auf eine Analyseplattform bietet, die zuverlässig Millionen an Ereignissen verarbeiten kann. Hadoop-Benutzer können nun Einblicke in stattfindende Ereignisse sowie vergangene Ereignisse gewinnen. Microsoft bietet auch eine integrierte Integration in Visual Studio, die die Interaktion von Entwicklern mit Storm erleichtert. Sie können jetzt in Visual Studio Storm-Topologien entwickeln, bereitstellen und debuggen.

- **HDInsight unter Linux** – Azure HDInsight bietet die Möglichkeit, Hadoop-Cluster zu erstellen, die auf virtuellen Linux-Computern (Ubuntu) ausgeführt werden. Wählen Sie diese Lösung, wenn Sie mit Linux oder Unix vertraut sind, eine Migration von einer vorhandenen Linux-basierten Hadoop-Lösung durchführen oder wenn Sie eine einfache Integration mit Komponenten des Hadoop-Systems wünschen, die für Linux konzipiert wurden. Unter Linux können Sie einen HDInsight-Cluster von einem Clientcomputer mit Windows oder Linux mit dem Azure-Portal, der Azure-Befehlszeilenschnittstelle (Azure-CLI) oder dem HDInsight .NET SDK (nur Windows) erstellen.

- **Zusätzliche VM-Größen** – HDInsight-Cluster sind jetzt auf weiteren VM-Arten und -Größen verfügbar. HDInsight-Cluster können jetzt die für allgemeine Zwecke konzipierten Größen A2 bis A7, Knoten der D-Serie mit Solid-State-Laufwerken (SSDs) und mit um 60 % schnelleren Prozessoren sowie die Größen A8 und A9 mit InfiniBand-Unterstützung für schnelle Netzwerkverbindungen verwenden. Kunden von Apache HBase in Azure HDInsight können von den größeren Speicherkonfigurationen der D-Serie profitieren, um Ihre Leistung zu steigern. Kunden von Apache Storm in Azure HDInsight profitieren auch vom zusätzlichen Arbeitsspeicher zum Laden größerer Referenzdatasets sowie von schnelleren CPUs für einen größeren Durchsatz.

- **Clusterskalierung** – Mithilfe der Clusterskalierung können Sie die Anzahl der Knoten eines ausgeführten HDInsight-Clusters ändern, ohne es löschen oder neu erstellen zu müssen. Derzeit bieten nur Hadoop Query und Apache Storm diese Möglichkeit; demnächst wird dies jedoch auch mit Apache HBase möglich sein.

- **Skriptaktion** – Dieses Clusteranpassungsfeature ermöglicht dank benutzerdefinierter Skripts eine beliebige Modifizierung von Hadoop-Clustern. Mithilfe dieses neuen Features können Benutzer experimentieren und Projekte, die über das Apache Hadoop-Anbietersystem zur Verfügung stehen, in Azure HDInsight-Clustern bereitstellen. Dieses Anpassungsfeature steht für alle Arten von HDInsight-Clustern einschließlich Hadoop, HBase und Storm zur Verfügung.

- **HBase** – HBase ist eine NoSQL-Datenbank mit niedriger Latenz, die eine Big-Data-Onlinetransaktionsverarbeitung ermöglicht. HBase ist als verwalteter Cluster integriert in die Azure-Umgebung verfügbar. Die Cluster sind so konfiguriert, dass sie Daten direkt in den Azure-Blobspeicher speichern. Dies sorgt für geringe Latenz und mehr Flexibilität bei Entscheidungen bezüglich Leistung und Kosten. So können Kunden interaktive Websites erstellen, die mit großen Datensätzen arbeiten, und Dienste entwickeln, die Sensor- und Telemetriedaten aus Millionen von Endpunkten speichern. Diese Daten können dann mit Hadoop-Jobs analysiert werden.

- **Apache Phoenix** – Apache Phoenix ist eine SQL-Abfrageschicht (Structured Query Language) über HBase. Es unterstützt eine begrenzte Teilmenge der SQL-Abfragesprachspezifikationen, einschließlich der Unterstützung sekundärer Indizes. Die Bereitstellung erfolgt als in den Client eingebetteter JDBC-Treiber (Java Database Connectivity), der die Abfrage von HBase-Daten bei geringer Latenz ermöglicht. Apache Phoenix verwendet Ihre SQL-Abfrage, kompiliert sie zu einer Reihe von HBase-Scans und Coprozessor-Aufrufen und erstellt reguläre JDBC-Resultsets. Apache Phoenix ist eine relationale Datenbankschicht über HBase. Die Bereitstellung erfolgt als in den Client eingebetteter JDBC-Treiber, der die Abfrage von HBase-Daten bei geringer Latenz ermöglicht. Apache Phoenix verwendet Ihre SQL-Abfrage, kompiliert sie zu einer Reihe von HBase-Scans und koordiniert die Ausführung dieser Scans, um reguläre JDBC-Resultsets zu erstellen.

- **Clusterdashboard** – Eine neue Webanwendung, die im HDInsight-Cluster bereitgestellt wird. Sie können damit Hive-Abfragen ausführen, Auftragsprotokolle überprüfen und den Azure-Blobspeicher durchsuchen. Die URL zum Zugreifen auf die Webanwendung lautet <*Clustername*>.azurehdinsight.net.

- **Microsoft Avro-Bibliothek** – Die Bibliothek implementiert das Apache Avro-Datenserialisierungssystem für die Microsoft .NET-Umgebung. Apache Avro stellt ein kompaktes binäres Datenaustauschformat für die Serialisierung bereit. Unter Verwendung von JavaScript Object Notation (JSON) definiert es ein sprachunabhängiges Schema, das die Interoperabilität von Sprachen sicherstellt. Die in einer Sprache serialisierten Daten können in einer anderen Sprache gelesen werden. Derzeit werden C, C++, C#, Java, PHP, Python und Ruby unterstützt. Das Apache Avro-Serialisierungsformat wird verbreitet in Azure HDInsight eingesetzt, um komplexe Datenstrukturen in einem Hadoop MapReduce-Job darzustellen.

- **YARN** – Ein neues allgemeines, verteiltes Anwendungsmanagement-Framework, das das klassische Apache Hadoop MapReduce-Framework für die Datenverarbeitung in Hadoop-Clustern ersetzt. Es dient quasi als Hadoop-Betriebssystem und macht aus der einem einzigen Zweck dienenden Datenplattform für die Stapelverarbeitung eine Plattform mit vielerlei Verwendungszwecken, die eine stapelweise, interaktive Online- und Streamverarbeitung unterstützt. Dieses neue Managementframework verbessert die Skalierbarkeit und Clusternutzung im Einklang mit wichtigen Kriterien wie Kapazitätsgarantien, Fairness und Servicelevel-Vereinbarungen (SLAs).

- **Tez (nur HDInsight 3.1 und höher)** – Ein allgemeines und anpassbares Framework zur Erstellung vereinfachter Datenverarbeitungsaufgaben für kleine und umfangreiche Arbeitsauslastungen in Hadoop. Es bietet die Möglichkeit, ein komplexes gerichtetes azyklisches Diagramm (Directed Acyclic Graph, DAG) von Aufgaben für einen einzelnen Auftrag auszuführen, sodass Projekte im Apache Hadoop-System, wie Apache Hive und Apache Pig, Anforderungen im Hinblick auf Antwortzeiten für die Benutzerinteraktion und besonders hohen Durchsatz im Petabyte-Bereich erfüllen können. Hive 0.13 ermöglicht die Ausführung von Hive-Abfragen auf Tez anstatt auf MapReduce.

- **Hohe Verfügbarkeit (HA)** – Den von HDInsight bereitgestellten Hadoop-Clustern wurde ein zweiter Hauptknoten hinzugefügt, um die Verfügbarkeit des Diensts zu erhöhen. Standardimplementierungen von Hadoop-Clustern verfügen in der Regel nur über einen Hauptknoten. Durch Hinzufügen eines zweiten Hauptknotens wird in HDInsight diese einzelne Fehlerquelle beseitigt. Der Wechsel zur neuen Clusterkonfiguration mit hoher Verfügbarkeit ändert nichts am Preis des Clusters, es sei denn, Kunden erstellen Cluster mit besonders großem Hauptknoten anstelle des großen Knotens in Standardgröße.

- **Hive-Leistung** – Umfangreiche Verbesserungen an der Antwortzeit von Hive-Abfragen (um das bis zu 40-fache) und an der Datenkomprimierung (bis zu 80 %) unter Verwendung des **ORC-Formats (Optimized Row Columnar)**.

- **Pig, Sqoop, Oozie, Ambari** – Komponentenversionsupgrades für HDInsight-Clusterversion 3.0 (HDP 2.0/Hadoop 2.2), die Parität mit der HDInsight-Clusterversion 2.1 (HDP 1.3/Hadoop 1.2) bieten. Detaillierte Informationen finden Sie in der Versionstabelle unten.

- **Mahout** – Diese Bibliothek skalierbarer Algorithmen für maschinelles Lernen ist in HDInsight 3.1 Hadoop-Clustern (und höher) vorinstalliert. Sie können also Mahout-Jobs ausführen, ohne eine zusätzliche Clusterkonfiguration vorzunehmen.

- **Unterstützung von Virtual Network** – HDInsight-Cluster können mit Azure Virtual Network verwendet werden, um eine Isolierung von Cloudressourcen oder Hybridlösungen zu unterstützen, bei denen Cloudressourcen mit Ressourcen in Ihrem Datencenter verknüpft werden.


## Unterstützte Versionen
In der folgenden Tabelle werden die derzeit verfügbaren Versionen von HDInsight, die entsprechenden verwendeten Versionen von Hortonworks Data Platform und die zugehörigen Relesasedaten aufgeführt. Sofern bekannt sind auch die jeweiligen Termine angegeben, ab denen der Support abläuft und sie als veraltet gelten. Beachten Sie Folgendes:

* Hochverfügbare Cluster mit zwei Hauptknoten werden standardmäßig für HDInsight 2.1 und höher bereitgestellt. Für HDInsight 1.6-Cluster sind sie nicht verfügbar.
* Nachdem der Support für eine bestimmte Version abgelaufen ist, steht sie möglicherweise nicht mehr im Azure-Portal zur Verfügung. Die folgende Tabelle zeigt, welche Versionen im klassischen Azure-Portal verfügbar sind. Clusterversionen stehen bis zum Zeitpunkt ihrer „Veraltung“ weiterhin über den Parameter `Version` im Windows PowerShell-Befehl [New-AzureRmHDInsightCluster](https://msdn.microsoft.com/library/mt619331.aspx) und das .NET SDK zur Verfügung.

HDInsight-Version|HDP-Version|Hohe Verfügbarkeit|Herausgabedatum|Im Azure-Portal verfügbar|Supportablaufdatum|Veraltet ab
---|---|---|---|---|---|---
HDI 3.3|HDP 2.3|Ja|02\.12.2015|Ja||
HDI 3.2|HDP 2.2|Ja|18\.02.2015|Ja||
HDI 3,1|HDP 2,1|Ja|24\.06.2014|Ja||
HDI 3,0|HDP 2,0|Ja|11\.02.2014|Ja|17\.09.2014|30\.06.2015
HDI 2,1|HDP 1,3|Ja|28\.10.2013|Ja|12\.05.2014|31\.05.2015
HDI 1.6|HDP 1.1|Nein|28\.10.2013|Ja|26\.04.2014|31\.05.2015

**Bereitstellung von Nicht-Standardclustern**

### Die Servicelevel-Vereinbarung (SLA) für HDInsight-Clusterversionen

Die SLA ist in Form eines "Supportfensters" definiert. Ein Supportfenster bezieht sich auf den Zeitraum, für den eine HDInsight-Clusterversion vom Microsoft-Kundendienst und -Support unterstützt wird. Ein HDInsight-Cluster gilt als außerhalb des Supportfensters, wenn das **Supportablaufdatum** am aktuellen Datum bereits verstrichen ist. In der Tabelle oben finden Sie eine Liste der unterstützten HDInsight-Clusterversionen. Das Supportablaufdatum für eine bestimmte HDInsight-Version X (sobald eine neuere X+1-Version verfügbar ist) wird berechnet als das spätere Datum von:

- Formel 1: Addieren Sie 180 Tage zum Releasedatum von HDInsight-Clusterversion X.
- Formel 2: Addieren Sie 90 Tage zu dem Datum, an dem HDInsight-Clusterversion X+1 (die Folgeversion von X) im Portal verfügbar gemacht wurde.

Das Datum **Veraltet ab** ist das Datum, nach dem eine Clusterversion nicht mehr in HDInsight erstellt werden kann.

> [AZURE.NOTE] Die HDInsight 2.1- und 3.0-Cluster werden unter dem Azure-Gastbetriebssystem [Family 4](../cloud-services/cloud-services-guestos-update-matrix.md) ausgeführt, das die 64-Bit-Version von Windows Server 2012 R2 verwendet und .NET Framework 4.0, 4.5. und 4.5.1 unterstützt.

## Hortonworks-Versionshinweise im Zusammenhang mit HDInsight-Versionen##

* HDInsight-Clusterversion 3.3 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2.3](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.3.0/bk_HDP_RelNotes/content/ch_relnotes_v230.html) basiert.
	* Apache Storm-Versionsanmerkungen finden Sie [hier](https://storm.apache.org/2015/11/05/storm0100-released.html).
	* Apache Hive-Versionsanmerkungen finden Sie [hier](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12332384&styleName=Text&projectId=12310843).

* HDInsight-Clusterversion 3.2 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2.2][hdp-2-2] basiert. Dies ist der **standardmäßige** Hadoop-Cluster, der bei Verwendung des Portals erstellt wird.

	* Versionshinweise für bestimmte Komponenten von Apache – [Hive 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310843&version=12326450), [Pig 0.14](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310730&version=12326954), [HBase 0.98.4](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310753&version=12326810), [Phoenix 4.2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12315120&version=12327581), [M/R 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310941&version=12327180), [HDFS 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310942&version=12327181), [YARN 2.6](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12313722&version=12327197), [Common](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12310240&version=12327179), [Tez 0.5.2](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314426&version=12328742), [Ambari 2.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12312020&version=12327486), [Storm 0.9.3](https://issues.apache.org/jira/secure/ReleaseNote.jspa?projectId=12314820&version=12327112), [Oozie 4.1.0](https://issues.apache.org/jira/secure/ReleaseNote.jspa?version=12324960&projectId=12311620).


* HDInsight-Clusterversion 3.1 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2.1.7][hdp-2-1-7] basiert. Vor dem 07.11.2014 erstellte HDInsight 3.1-Cluster basierten auf [Hortonworks Data Platform 2.1.1][hdp-2-1-1].

* HDInsight-Clusterversion 3.0 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2.0][hdp-2-0-8] basiert.

* HDInsight-Clusterversion 2.1 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 1.3][hdp-1-3-0] basiert.

* HDInsight-Clusterversion 1.6 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 1.1][hdp-1-1-0] basiert.


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/support/forums/

[connect-excel-with-hive-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md

[hdp-2-2]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.2.0/HDP_2.2.0_Release_Notes_20141202_version/index.html

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/

<!---HONumber=AcomDC_0309_2016-->