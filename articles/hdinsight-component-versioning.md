<properties linkid="manage-services-hdinsight-version" urlDisplayName="HDInsight Hadoop Version" pageTitle="What's new in Hadoop cluster versions of HDInsight? | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight supports multiple Hadoop cluster versions deployable at any time. See the Hadoop and HortonWorks Data Platform (HDP) distribution versions supported." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="What's new in the cluster versions provided by HDInsight?" authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev"></tags>

# Neuheiten in den von HDInsight bereitgestellten Hadoop-Clusterversionen

## HDInsight-Versionen

HDInsight unterstützt mehrere Hadoop-Clusterversionen, die jederzeit bereitgestellt werden können. Jede ausgewählte Version stellt eine bestimmte Version der HDP-Distribution (Hortonworks Data Platform) und eine Reihe von Komponenten innerhalb dieser Distribution bereit. Die den einzelnen HDInsight-Clusterversionen zugeordneten Komponentenversionen sind in der folgenden Tabelle aufgeführt. Als Standardclusterversion wird von [Azure HDInsight][] derzeit Version 3.1 auf Basis von HDP 2.1 verwendet.

<table>
<colgroup>
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
<col width="20%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Komponente</th>
<th align="left">HDInsight Version 3.1 (Standard)</th>
<th align="left">HDInsight Version 3.0</th>
<th align="left">HDInsight Version 2.1</th>
<th align="left">HDInsight Version 1.6</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Hortonworks Data Platform (HDP)</td>
<td align="left">2.1</td>
<td align="left">2.0</td>
<td align="left">1.3</td>
<td align="left">1.1</td>
</tr>
<tr class="even">
<td align="left">Apache Hadoop und YARN</td>
<td align="left">2.4.0</td>
<td align="left">2.2.0</td>
<td align="left">1.2.0</td>
<td align="left">1.0.3</td>
</tr>
<tr class="odd">
<td align="left">Tez</td>
<td align="left">0.4.0</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Apache Pig</td>
<td align="left">0.12.1</td>
<td align="left">0.12.0</td>
<td align="left">0.11.0</td>
<td align="left">0.9.3</td>
</tr>
<tr class="odd">
<td align="left">Apache Hive und HCatalog</td>
<td align="left">0.13.0</td>
<td align="left">0.12.0</td>
<td align="left">0.11.0</td>
<td align="left">0.9.0</td>
</tr>
<tr class="even">
<td align="left">HBase</td>
<td align="left">0.98.0</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Apache Sqoop</td>
<td align="left">1.4.4</td>
<td align="left">1.4.4</td>
<td align="left">1.4.3</td>
<td align="left">1.4.2</td>
</tr>
<tr class="even">
<td align="left">Apache Oozie</td>
<td align="left">4.0.0</td>
<td align="left">4.0.0</td>
<td align="left">3.3.2</td>
<td align="left">3.2.0</td>
</tr>
<tr class="odd">
<td align="left">Apache HCatalog</td>
<td align="left">Mit Hive zusammengeführt</td>
<td align="left">Mit Hive zusammengeführt</td>
<td align="left">Mit Hive zusammengeführt</td>
<td align="left">0.4.1</td>
</tr>
<tr class="even">
<td align="left">Apache Templeton</td>
<td align="left">Mit Hive zusammengeführt</td>
<td align="left">Mit Hive zusammengeführt</td>
<td align="left">Mit Hive zusammengeführt</td>
<td align="left">0.1.4</td>
</tr>
<tr class="odd">
<td align="left">Ambari</td>
<td align="left">1.5.1</td>
<td align="left">1.4.1</td>
<td align="left">API v1.0</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Zookeeper</td>
<td align="left">3.4.5</td>
<td align="left">3.4.5</td>
<td align="left"></td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Mahout</td>
<td align="left">0.9.0</td>
<td align="left"></td>
<td align="left"></td>
<td align="left"></td>
</tr>
</tbody>
</table>

**Abrufen von Informationen zu aktuellen Komponentenversionen**

Die den HDInsight-Clusterversionen zugeordneten Komponentenversionen können sich bei künftigen Updates für HDInsight ändern. Eine Möglichkeit, die verfügbaren Komponenten zu bestimmen und zu prüfen, welche Versionen für einen Cluster verwendet werden, bietet die Ambari REST-API. Über den **GetComponentInformation**-Befehl können Informationen zu einer Dienstkomponente abgerufen werden. Ausführliche Informationen finden Sie in der [Ambari-Dokumentation][]. Um diese Informationen zu erhalten, können Sie sich auch per Remotedesktop bei einem Cluster anmelden und den Inhalt des Verzeichnisses "C:\\apps\\dist" direkt prüfen.

**Versionshinweise**

Unter [HDInsight-Versionshinweise][] (HDInsight Release Notes, in englischer Sprache) finden Sie zusätzliche Anmerkungen zu den neuesten Versionen von HDInsight.

### Auswählen einer Version bei Bereitstellung eines HDInsight-Clusters

Wenn Sie einen Cluster über die HDInsight PowerShell-Cmdlets oder das HDInsight .NET SDK erstellen, können Sie die Version für das HDInsight Hadoop-Cluster über den Parameter "Version" auswählen.

Wenn Sie die Option **Schnellerfassung** wählen, erhalten Sie Version 3.1 von HDInsight, die standardmäßig Hadoop-Cluster erstellt. Wenn Sie die Option **Benutzerdefiniert erstellen** im Azure-Portal verwenden, können Sie die Version des Clusters, die Sie bereitstellen möchten, im Dropdownmenü **HDInsight-Version** auf der Seite **Clusterdetails** auswählen.

## Wichtige Features

Azure HDInsight unterstützt nun standardmäßig Hadoop 2.4 mit HDInsight-Clusterversion 3.1 (unter Verwendung von Hortonworks Data Platform 2.1). Kunden werden basierend auf dieser Plattform eine Reihe wichtiger Vorteile geboten. Zu diesen Vorteilen gehören insbesondere:

-   **HBase**: HBase ist eine NoSQL-Datenbank mit niedriger Latenz und ermöglicht die Onlinetransaktionsverarbeitung großer Datenmengen. HBase ist als verwalteter Cluster integriert in die Azure-Umgebung verfügbar. Die Cluster sind so konfiguriert, dass Daten direkt im Azure Blob-Speicher abgelegt werden. Dies verringert die Latenz und verbessert die Flexibilität bei Kosten-/Leistungsabwägungen. Auf diese Weise können Kunden interaktive Websites erstellen, die mit großen Datenmengen arbeiten, z. B. für Dienste, die Sensor- und Telemetriedaten von Millionen von Endpunkten speichern. Anschließend können die Daten mit Hadoop-Jobs analysiert werden.

-   **Cluster-Dashboard**: Eine neue Webanwendung, die im HDInsight-Cluster bereitgestellt wird. Sie können damit Hive-Abfragen ausführen, Job-Protokolle überprüfen und Azure Blob-Speicher durchsuchen. Die URL zum Zugreifen auf die Webanwendung lautet \<*Clustername*\>.azurehdinsight.net.

-   **Microsoft Avro-Bibliothek**: Diese Bibliothek implementiert das Apache Avro-Datenserialisierungssystem für die Microsoft .NET-Umgebung. Apache Avro stellt ein kompaktes binäres Datenaustauschformat für die Serialisierung bereit. Unter Verwendung von JSON definiert es ein sprachunabhängiges Schema, das die Interoperabilität von Sprachen sicherstellt. Die in einer Sprache serialisierten Daten können in einer anderen Sprache gelesen werden. Derzeit werden C, C++, C#, Java, PHP, Python und Ruby unterstützt. Das Apache Avro-Serialisierungsformat wird verbreitet in Azure HDInsight eingesetzt, um komplexe Datenstrukturen in einem Hadoop MapReduce-Job darzustellen.

-   **YARN**: Ein neues allgemeines, verteiltes Framework für die Anwendungsverwaltung, welches das klassische Framework Apache Hadoop MapReduce für die Datenverarbeitung in Hadoop-Clustern ablöst. Es dient quasi als Hadoop-Betriebssystem und macht aus der einem einzigen Zweck dienenden Datenplattform für die Stapelverarbeitung eine Plattform mit vielerlei Verwendungszwecken, die eine stapelweise, interaktive Online- und Streamverarbeitung unterstützt. Dieses neue Management-Framework verbessert die Skalierbarkeit und Clusternutzung im Einklang mit wichtigen Kriterien wie Kapazitätsgarantien, Fairness und Servicelevel-Vereinbarungen.

-   **Tez (nur HDInsight 3.1)**: Ein allgemeines und anpassbares Framework zur Erstellung vereinfachter Datenverarbeitungsaufgaben für kleine und umfangreiche Arbeitsauslastungen in Hadoop. Es bietet die Möglichkeit, ein komplexes gerichtetes azyklisches Diagramm (Directed Acyclic Graph, DAG) von Aufgaben für einen einzelnen Job auszuführen, sodass Projekte im Apache Hadoop-System, wie Apache Hive und Apache Pig, Anforderungen im Hinblick auf Antwortzeiten für die Benutzerinteraktion und besonders hohen Durchsatz im Petabyte-Bereich erfüllen können. Hive 0.13 ermöglicht die Ausführung von Hive-Abfragen auf Tez anstatt auf MapReduce.

-   **Hohe Verfügbarkeit**: Den von HDInsight bereitgestellten Hadoop-Clustern wurde ein zweiter Hauptknoten hinzugefügt, um die Verfügbarkeit des Diensts zu erhöhen. Standardimplementierungen von Hadoop-Clustern verfügen in der Regel nur über einen Hauptknoten. Durch Hinzufügung eines zweiten Hauptknotens wird in HDInsight diese einzelne Fehlerquelle beseitigt. Der Wechsel zur neuen Clusterkonfiguration mit hoher Verfügbarkeit ändert nichts am Preis des Clusters, es sei denn, Kunden stellen Cluster mit besonders großem Hauptknoten anstelle dem großen Knoten in Standardgröße bereit.

-   **Hive-Leistung**: Umfangreiche Verbesserungen an der Antwortzeit von Hive-Abfragen (um das bis zu 40-fache) und an der Datenkomprimierung (bis zu 80 %) unter Verwendung des **ORC-Formats (Optimized Row Columnar)**.

-   **Pig, Sqoop, Qozie, Ambari**: Komponentenversionsupgrades für HDInsight-Clusterversion 3.0 (HDP 2.0/Hadoop 2.2), die Parität mit der HDInsight-Clusterversion 2.1 (HDP 1.3/Hadoop 1.2) bieten. Detaillierte Informationen finden Sie in den Versionstabellen unten.

-   **Mahout**: Diese Bibliothek skalierbarer Algorithmen für maschinelles Lernen ist in HDInsight 3.1 Hadoop-Clustern vorinstalliert. Sie können also Mahout-Jobs ausführen, ohne eine zusätzliche Clusterkonfiguration vorzunehmen.

## Unterstützte Versionen

In der folgenden Tabelle sind die derzeit verfügbaren Versionen von HDInsight, die entsprechenden HDP-Versionen (Hortonworks Data Platform), die sie verwenden, und die zugehörigen Herausgabetermine aufgeführt. Sofern bekannt sind auch die jeweiligen Termine angegeben, ab denen ihr Support abläuft und sie als veraltet gelten. Beachten Sie Folgendes:

-   Hochverfügbare Cluster mit zwei Hauptknoten werden standardmäßig für HDInsight 2.1-, 3.0- und 3.1-Cluster bereitgestellt. Für HDInsight 1.6-Cluster sind sie nicht verfügbar.
-   Nachdem der Support für eine bestimmte Version abgelaufen ist, steht sie möglicherweise nicht mehr über das Azure-Verwaltungsportal zur Verfügung. In der folgenden Tabelle werden die im Azure-Verwaltungsportal verfügbaren Versionen aufgeführt. Clusterversionen stehen bis zu dem Zeitpunkt, an dem sie veralten, weiterhin über den Parameter `Version` im PowerShell-Befehl [New-AzureHDInsightCluster][] und das .NET SDK zur Verfügung.

<table border="1">
<tr>
<th>
HDInsight-Version

</th>
<th>
HDP-Version</a>

<th>
Hohe Verfügbarkeit

</th>
</th>
<th>
Herausgabedatum

</th>
<th>
Im Azure-Verwaltungsportal verfügbar

</th>
<th>
Supportablaufdatum

</th>
<th>
Veraltet ab

</th>
</tr>
<tr>
<td>
HDI 3.1

</td>
<td>
HDP 2.1

</td>
<td>
Ja

</td>
<td>
</td>
<td>
Ja

</td>
<td>
</td>
<td>
</td>
</tr>
<tr>
<td>
HDI 3.0

</td>
<td>
HDP 2.0

</td>
<td>
Ja

</td>
<td>
11.02.2014

</td>
<td>
Ja

</td>
<td>
17.09.2014

</td>
<td>
30.06.2015

</td>
</tr>
<tr>
<td>
HDI 2.1

</td>
<td>
HDP 1.3

</td>
<td>
Ja

</td>
<td>
28.10.2013

</td>
<td>
Nein

</td>
<td>
12.05.2014

</td>
<td>
31.05.2015

</td>
</tr>
<tr>
<td>
HDI 1.6

</td>
<td>
HDP 1.1

</td>
<td>
Nein

</td>
<td>
28.10.2013

</td>
<td>
Nein

</td>
<td>
26.04.2014

</td>
<td>
31.05.2015

</td>
</tr>
</table>
**Bereitstellung von Nicht-Standardclustern**

Die Erstellung von HDInsight 3.1-Clustern auf Hadoop 2.4 wird vom Azure-Portal, dem HDInsight SDK und Azure PowerShell unterstützt. Beachten Sie, dass HDInsight 3.1-Cluster standardmäßig auf Hadoop 2.4 erstellt werden, d. h., die Benutzer müssen die Option **Benutzerdefiniert erstellen** im Portal verwenden, um bei Bedarf andere HDInsight-Clusterversionen zu erstellen.

### Die Servicelevel-Vereinbarung (SLA) für HDInsight-Clusterversionen

Die SLA ist in Form eines "Supportfensters" definiert. Ein Supportfenster bezieht sich auf den Zeitraum, für den eine HDInsight-Clusterversion vom Microsoft-Kundensupport unterstützt wird. Ein HDInsight-Cluster gilt als außerhalb des Supportfensters, wenn das **Supportablaufdatum** am aktuellen Datum bereits verstrichen ist. In der Tabelle oben finden Sie eine Liste der unterstützten HDInsight-Clusterversionen. Das Supportablaufdatum für eine bestimmte HDInsight-Version X (sobald eine neuere X+1-Version verfügbar ist) wird berechnet als das spätere Datum von:

-   Formel 1: Addieren Sie 180 Tage zum Herausgabedatum von HDInsight-Clusterversion X
-   Formel 2: Addieren Sie 90 Tage zu dem Datum, an dem HDInsight-Clusterversion X+1 (die nachfolgende Version nach X) im Azure-Verwaltungsportal verfügbar gemacht wurde.

Das Datum **Veraltet ab** ist das Datum, nach dem eine Clusterversion nicht mehr in HDInsight erstellt werden kann.

> [WACOM.NOTE] Sowohl HDInsight 2.1- als auch 3.0-Cluster laufen unter der Azure-Gastbetriebssystem-[Familie 4][], die die 64-Bit-Version von Windows Server 2012 R2 verwendet und .NET Framework 4.0, 4.5 und 4.5.1 unterstützt.

## Hortonworks-Versionshinweise im Zusammenhang mit HDInsight-Versionen

-   HDInsight-Clusterversion 3.1 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2.1][] basiert. Dies ist das Hadoop-Cluster, das bei Verwendung des Azure HDInsight-Portals standardmäßig erstellt wird.

-   HDInsight-Clusterversion 3.0 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2.0][] basiert.

-   HDInsight-Clusterversion 2.1 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 1.3][] basiert.

-   HDInsight-Clusterversion 1.6 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 1.1][] basiert.

  [Azure HDInsight]: http://go.microsoft.com/fwlink/?LinkID=285601
  [Ambari-Dokumentation]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [HDInsight-Versionshinweise]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-release-notes/
  [New-AzureHDInsightCluster]: http://msdn.microsoft.com/en-us/library/dn593744.aspx
  [Familie 4]: http://msdn.microsoft.com/en-us/library/azure/ee924680.aspx#explanation
  [Hortonworks Data Platform 2.1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html
  [Hortonworks Data Platform 2.0]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html
  [Hortonworks Data Platform 1.3]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html
  [Hortonworks Data Platform 1.1]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html
