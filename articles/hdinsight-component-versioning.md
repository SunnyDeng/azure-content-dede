<properties urlDisplayName="HDInsight Hadoop Version" pageTitle="Neuheiten in den Hadoop-Clusterversionen in HDInsight | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight unterstützt mehrere Hadoop-Clusterversionen, die jederzeit bereitgestellt werden können. Entdecken Sie die unterstützten Versionen von Hadoop und der HortonWorks Data Platform (HDP)." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="What's new in the cluster versions provided by HDInsight?" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/14/2014" ms.author="bradsev" />


#Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen

##HDInsight-Versionen
HDInsight unterstützt mehrere Hadoop-Clusterversionen, die jederzeit bereitgestellt werden können. Jede ausgewählte Version stellt eine bestimmte Version der HDP-Distribution (Hortonworks Data Platform) und eine Reihe von Komponenten innerhalb dieser Distribution bereit. Die den einzelnen HDInsight-Clusterversionen zugeordneten Komponentenversionen sind in der folgenden Tabelle aufgeführt. Als Standardclusterversion wird von Azure HDInsight derzeit Version 3.1 auf Basis von HDP 2.1.7 (ab 11/7/2014) verwendet.


<table border="1">
<tr><th>Komponente</th><th>HDInsight Version 3.1 (Standard)</th><th>HDInsight Version 3.0</th><th>HDInsight Version 2.1</th><th>HDInsight Version 1.6</th></tr>
<tr><td>Hortonworks Data Platform (HDP)</td><td>2.1.7</td><td>2.0</td><td>1.3</td><td>1.1</td></tr>
<tr><td>Apache Hadoop und YARN</td><td>2.4.0</td><td>2.2.0</td><td>1.2.0</td><td>1.0.3</td></tr>
<tr><td>Tez</td><td>0.4.0</td><td></td><td></td><td></td></tr>
<tr><td>Apache Pig</td><td>0.12.1</td><td>0.12.0</td><td>0.11.0</td><td>0.9.3</td></tr>
<tr><td>Apache Hive und HCatalog</td><td>0.13.1</td><td>0.12.0</td><td>0.11.0</td><td>0.9.0</td></tr>
<tr><td>HBase </td><td>0.98.0</td><td></td><td></td><td></td></tr>
<tr><td>Apache Sqoop</td><td>1.4.4</td><td>1.4.4</td><td>1.4.3</td><td>1.4.2</td></tr>
<tr><td>Apache Oozie</td><td>4.0.0</td><td>4.0.0</td><td>3.3.2</td><td>3.2.0</td></tr>
<tr><td>Apache HCatalog</td><td>Mit Hive zusammengeführt</td><td>Mit Hive zusammengeführt</td><td>Mit Hive zusammengeführt</td><td>0.4.1</td></tr>
<tr><td>Apache Templeton</td><td>Mit Hive zusammengeführt</td><td>Mit Hive zusammengeführt</td><td>Mit Hive zusammengeführt</td><td>0.1.4</td></tr>
<tr><td>Ambari</td><td>>=1.5.1</td><td>1.4.1</td><td>API v1.0</td><td></td></tr>
<tr><td>Zookeeper</td><td>3.4.5</td><td>3.4.5</td><td></td><td></td></tr>
<tr><td>Storm</td><td>0.9.1</td><td></td><td></td><td></td></tr>
<tr><td>Mahout</td><td>0.9.0</td><td></td><td></td><td></td></tr>
<tr><td>Phoenix</td><td>4.0.0.2.1.7.0-2162</td><td></td><td></td><td></td></tr>
</table>


**Abrufen von Informationen zu aktuellen Komponentenversionen**

Die den HDInsight-Clusterversionen zugeordneten Komponentenversionen können sich bei künftigen Updates für HDInsight ändern. Eine Möglichkeit, die verfügbaren Komponenten zu bestimmen und zu prüfen, welche Versionen für einen Cluster verwendet werden, bietet die Ambari REST-API. Über den **GetComponentInformation**-Befehl können Informationen zu einer Dienstkomponente abgerufen werden. Ausführliche Informationen finden Sie in der [Ambari-Dokumentation][ambari-docs]. Um diese Informationen zu erhalten, können Sie sich auch per Remotedesktop bei einem Cluster anmelden und den Inhalt des Verzeichnisses "C:\apps\dist\" direkt prüfen.


**Versionshinweise**	

Unter [HDInsight-Versionshinweise](http://azure.microsoft.com/de-de/documentation/articles/hdinsight-release-notes/) finden Sie zusätzliche Anmerkungen zu den neuesten Versionen von HDInsight.

### Auswählen einer Version bei Bereitstellung eines HDInsight-Clusters

Wenn Sie einen Cluster über die HDInsight PowerShell-Cmdlets oder das HDInsight .NET SDK erstellen, können Sie die Version für das HDInsight Hadoop-Cluster über den Parameter "Version" auswählen.

Wenn Sie die Option **Schnellerfassung** wählen, erhalten Sie Version 3.1 von HDInsight, die standardmäßig Hadoop-Cluster erstellt. Wenn Sie die Option **Benutzerdefiniert erstellen** im Azure-Portal verwenden, können Sie die Version des Clusters, die Sie bereitstellen möchten, im Dropdownmenü **HDInsight-Version** auf der Seite **Clusterdetails** auswählen. 

##Wichtige Features
Azure HDInsight unterstützt nun standardmäßig Hadoop 2.4 mit HDInsight-Clusterversion 3.1 (unter Verwendung von Hortonworks Data Platform 2.1.7). Kunden werden basierend auf dieser Plattform eine Reihe wichtiger Vorteile geboten. Zu diesen Vorteilen gehören insbesondere:

- **Skriptaktion (Vorschau)**: Die Vorschau dieser Cluster-Anpassungsfunktion ermöglicht die Modifizierung von Hadoop-Clustern auf beliebige Weise mithilfe benutzerdefinierter Skripts. Mit dieser neuen Funktion können Benutzer mit Projekten experimentieren, die im Apache Hadoop-System für Azure HDInsight-Clusters zur Verfügung stehen, und diese bereitstellen. Diese Anpassungsfunktion ist für alle Typen von HDInsight-Clustern verfügbar, einschließlich Hadoop, HBase und Storm.

- **HBase**: HBase ist eine NoSQL-Datenbank mit niedriger Latenz und ermöglicht die Onlinetransaktionsverarbeitung großer Datenmengen. HBase ist als verwalteter Cluster integriert in die Azure-Umgebung verfügbar. Die Cluster sind so konfiguriert, dass sie Daten direkt in den Azure-Blob-Speicher speichern. Dies sorgt für geringe Latenz und mehr Flexibilität bei Entscheidungen bezüglich Leistung und Kosten. So können Kunden interaktive Websites erstellen, die mit großen Datensätzen arbeiten, und Dienste entwickeln, die Sensor- und Telemetriedaten aus Millionen von Endpunkten speichern. Diese Daten können dann mit Hadoop-Jobs analysiert werden.

- **Apache Phoenix**: Apache Phoenix ist eine über HBase gelegte SQL-Abfrageebene. Es wird eine begrenzte Teilmenge von Spezifikationen der SQL-Abfragesprache unterstützt, einschließlich der Unterstützung sekundärer Indizes. Die Bereitstellung erfolgt als in den Client eingebetteter JDBC-Treiber, der die Abfrage von HBase-Daten bei geringer Latenz ermöglicht. Apache Phoenix verwendet Ihre SQL-Abfrage, kompiliert sie zu einer Reihe von HBase-Scans und Coprozessor-Aufrufen und erstellt reguläre JDBC-Resultsets. Apache Phoenix ist eine relationale Datenbankebene über HBase. Die Bereitstellung erfolgt als in den Client eingebetteter JDBC-Treiber, der die Abfrage von HBase-Daten bei geringer Latenz ermöglicht. Apache Phoenix verwendet Ihre SQL-Abfrage, kompiliert sie zu einer Reihe von HBase-Scans und koordiniert die Ausführung dieser Scans, um reguläre JDBC-Resultsets zu erstellen.

- **Cluster-Dashboard**: Eine neue Webanwendung, die im HDInsight-Cluster bereitgestellt wird.  Sie können damit Hive-Abfragen ausführen, Job-Protokolle überprüfen und Azure Blob-Speicher durchsuchen. Die URL zum Zugreifen auf die Webanwendung lautet <*Clustername*>.azurehdinsight.net.

- **Microsoft Avro-Bibliothek**: Diese Bibliothek implementiert das Apache Avro-Datenserialisierungssystem für die Microsoft .NET-Umgebung. Apache Avro stellt ein kompaktes binäres Datenaustauschformat für die Serialisierung bereit. Unter Verwendung von JSON definiert es ein sprachunabhängiges Schema, das die Interoperabilität von Sprachen sicherstellt. Die in einer Sprache serialisierten Daten können in einer anderen Sprache gelesen werden. Derzeit werden C, C++, C#, Java, PHP, Python und Ruby unterstützt. Das Apache Avro-Serialisierungsformat wird verbreitet in Azure HDInsight eingesetzt, um komplexe Datenstrukturen in einem Hadoop MapReduce-Job darzustellen.

- **YARN**: Ein neues allgemeines, verteiltes Framework für die Anwendungsverwaltung, welches das klassische Framework Apache Hadoop MapReduce für die Datenverarbeitung in Hadoop-Clustern ablöst. Es dient quasi als Hadoop-Betriebssystem und macht aus der einem einzigen Zweck dienenden Datenplattform für die Stapelverarbeitung eine Plattform mit vielerlei Verwendungszwecken, die eine stapelweise, interaktive Online- und Streamverarbeitung unterstützt. Dieses neue Management-Framework verbessert die Skalierbarkeit und Clusternutzung im Einklang mit wichtigen Kriterien wie Kapazitätsgarantien, Fairness und Servicelevel-Vereinbarungen.

- **Tez (nur HDInsight 3.1)**: Ein allgemeines und anpassbares Framework zur Erstellung vereinfachter Datenverarbeitungsaufgaben für kleine und umfangreiche Arbeitsauslastungen in Hadoop. Es bietet die Möglichkeit, ein komplexes gerichtetes azyklisches Diagramm (Directed Acyclic Graph, DAG) von Aufgaben für einen einzelnen Job auszuführen, sodass Projekte im Apache Hadoop-System, wie Apache Hive und Apache Pig, Anforderungen im Hinblick auf Antwortzeiten für die Benutzerinteraktion und besonders hohen Durchsatz im Petabyte-Bereich erfüllen können. Hive 0.13 ermöglicht die Ausführung von Hive-Abfragen auf Tez anstatt auf MapReduce.

- **Hohe Verfügbarkeit**: Den von HDInsight bereitgestellten Hadoop-Clustern wurde ein zweiter Hauptknoten hinzugefügt, um die Verfügbarkeit des Diensts zu erhöhen. Standardimplementierungen von Hadoop-Clustern verfügen in der Regel nur über einen Hauptknoten. Durch Hinzufügung eines zweiten Hauptknotens wird in HDInsight diese einzelne Fehlerquelle beseitigt. Der Wechsel zur neuen Clusterkonfiguration mit hoher Verfügbarkeit ändert nichts am Preis des Clusters, es sei denn, Kunden stellen Cluster mit besonders großem Hauptknoten anstelle dem großen Knoten in Standardgröße bereit.

- **Hive-Leistung**: Umfangreiche Verbesserungen an der Antwortzeit von Hive-Abfragen (um das bis zu 40-Fache) und an der Datenkomprimierung (bis zu 80 %) unter Verwendung des **ORC**-Formats (Optimized Row Columnar).

- **Pig, Sqoop, Oozie, Ambari**: Komponentenversionsupgrades für HDInsight-Clusterversion 3.0 (HDP 2.0/Hadoop 2.2), die Parität mit der HDInsight-Clusterversion 2.1 (HDP 1.3/Hadoop 1.2) bieten. Detaillierte Informationen finden Sie in den Versionstabellen unten.

- **Mahout**: Diese Bibliothek skalierbarer Algorithmen für maschinelles Lernen ist in HDInsight 3.1 Hadoop-Clustern vorinstalliert. Sie können also Mahout-Jobs ausführen, ohne eine zusätzliche Clusterkonfiguration vorzunehmen.

- **Unterstützung für virtuelle Netzwerke**: HDInsight-Cluster können mit virtuellen Azure-Netzwerken verwendet werden, um Cloud-Ressourcen zu isolieren oder um Hybrid-Lösungen zu unterstützen, die Cloud-Ressourcen mit Ressourcen in Ihrem Rechenzentrum verbinden.


## Unterstützte Versionen
In der folgenden Tabelle sind die derzeit verfügbaren Versionen von HDInsight, die entsprechenden HDP-Versionen (Hortonworks Data Platform), die sie verwenden, und die zugehörigen Herausgabetermine aufgeführt. Sofern bekannt sind auch die jeweiligen Termine angegeben, ab denen ihr Support abläuft und sie als veraltet gelten. Beachten Sie Folgendes:

* Hochverfügbare Cluster mit zwei Hauptknoten werden standardmäßig für HDInsight 2.1-, 3.0- und 3.1-Cluster bereitgestellt. Für HDInsight 1.6-Cluster sind sie nicht verfügbar.
* Nachdem der Support für eine bestimmte Version abgelaufen ist, steht sie möglicherweise nicht mehr über das Azure-Verwaltungsportal zur Verfügung. In der folgenden Tabelle werden die im Azure-Verwaltungsportal verfügbaren Versionen aufgeführt. Clusterversionen sind bis zu ihrem Ablaufdatum weiterhin über den Parameter "Version" des PowerShell-Befehls [New-AzureHDInsightCluster](http://msdn.microsoft.com/de-de/library/dn593744.aspx) und das .NET SDK verfügbar. 

<table border="1">
<tr><th>HDInsight-Version</th><th>HDP-Version</a><th>Hohe Verfügbarkeit</th></th><th>Herausgabedatum</th><th>Im Azure-Verwaltungsportal verfügbar</th><th>Supportablaufdatum</th><th>Veraltet ab</th></tr>
<tr><td>HDI 3.1</td><td>HDP 2.1</td><td>Ja</td><td></td><td>Ja</td><td></td><td></td></tr>
<tr><td>HDI 3.0</td><td>HDP 2.0</td><td>Ja</td><td>11.02.2014</td><td>Ja</td><td>17.09.2014</td><td>30.06.2015</td></tr>
<tr><td>HDI 2.1</td><td>HDP 1.3</td><td>Ja</td><td>28.10.2013</td><td>Nein</td><td>12.05.2014</td><td>31.05.2015</td></tr>
<tr><td>HDI 1.6</td><td>HDP 1.1</td><td>Nein</td><td>28.10.2013</td><td>Nein</td><td>26.04.2014</td><td>31.05.2015</td></tr>
</table><br>

**Bereitstellung von Nicht-Standardclustern**	

Die Erstellung von HDInsight 3.1-Clustern auf Hadoop 2.4 wird vom Azure-Portal, dem HDInsight SDK und Azure PowerShell unterstützt. Beachten Sie, dass HDInsight 3.1-Cluster standardmäßig auf Hadoop 2.4 erstellt werden, d. h., die Benutzer müssen die Option **Benutzerdefiniert erstellen** im Portal verwenden, um bei Bedarf andere HDInsight-Clusterversionen zu erstellen.

### Die Servicelevel-Vereinbarung (SLA) für HDInsight-Clusterversionen

Die SLA ist in Form eines "Supportfensters" definiert. Ein Supportfenster bezieht sich auf den Zeitraum, für den eine HDInsight-Clusterversion vom Microsoft-Kundensupport unterstützt wird.  Ein HDInsight-Cluster gilt als außerhalb des Supportfensters, wenn das **Supportablaufdatum** einer Version das aktuelle Datum überschritten hat.  In der Tabelle oben finden Sie eine Liste der unterstützten HDInsight-Clusterversionen.  Das Supportablaufdatum für eine bestimmte HDInsight-Version X (sobald eine neuere X+1-Version verfügbar ist) wird berechnet als das spätere Datum von:  

- Formel 1:  Addieren Sie 180 Tage zum Herausgabedatum von HDInsight-Clusterversion X
- Formel 2: Addieren Sie 90 Tage zu dem Datum, an dem HDInsight-Clusterversion X+1 (die nachfolgende Version nach X) im Azure-Verwaltungsportal verfügbar gemacht wurde.

Das Datum **Veraltet ab** ist das Datum, nach dem eine Clusterversion nicht mehr in HDInsight erstellt werden kann.

> [WACOM.NOTE] HDInsight 2.1- und 3.0-Cluster werden auf Azure Guest OS [Family 4](http://msdn.microsoft.com/de-de/library/azure/ee924680.aspx#explanation) ausgeführt. Dabei wird eine 64-Bit-Version von Windows Server 2012 R2 verwendet, und es wird .NET Framework 4.0, 4.5. und 4.5.1 unterstützt. 

## Hortonworks-Versionshinweise im Zusammenhang mit HDInsight-Versionen##

* HDInsight-Clusterversion 3.1 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2.1.7][hdp-2-1-7] basiert. Dies ist der Hadoop-Standardcluster, der bei Verwendung des Azure HDInsight-Portals nach 11/7/2014 standardmäßig erstellt wird. HDInsight 3.1-Cluster, die vor 11/7/2014 erstellt wurden, basieren auf [Hortonworks Data Platform 2.1.1][hdp-2-1-1] 

* HDInsight-Clusterversion 3.0 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2.0][hdp-2-0-8] basiert.

* HDInsight-Clusterversion 2.1 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 1.3][hdp-1-3-0] basiert. 

* HDInsight-Clusterversion 1.6 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 1.1][hdp-1-1-0] basiert. 


[image-hdi-versioning-versionscreen]: ./media/hdinsight-component-versioning/hdi-versioning-version-screen.png

[wa-forums]: http://azure.microsoft.com/de-de/support/forums/

[connect-excel-with-hive-ODBC]: ../hdinsight-connect-excel-hive-ODBC-driver/

[hdp-2-1-7]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.7-Win/bk_releasenotes_HDP-Win/content/ch_relnotes-HDP-2.1.7.html

[hdp-2-1-1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html

[hdp-2-0-8]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html

[hdp-1-3-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html

[hdp-1-1-0]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html

[ambari-docs]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[zookeeper]: http://zookeeper.apache.org/ 

<!--HONumber=35.1-->
