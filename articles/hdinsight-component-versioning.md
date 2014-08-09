<properties linkid="manage-services-hdinsight-version" urlDisplayName="HDInsight Hadoop Version" pageTitle="What's new in the cluster versions provided by HDInsight? | Azure" metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure" description="HDInsight supports multiple Hadoop cluster versions deployable at any time. See the Hadoop and HortonWorks Data Platform (HDP) distribution versions supported." services="HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="What's new in the cluster versions provided by HDInsight?" authors="bradsev" />

Neuheiten in den von HDInsight bereitgestellten Clusterversionen
================================================================

Azure HDInsight unterstützt nun Hadoop 2.2 mit HDInsight Clusterversion 3.0 und bietet den Kunden basierend auf dieser Plattform eine Reihe wichtiger Vorteile. Zu diesen Vorteilen gehören insbesondere:

-   Hive: Umfangreiche Verbesserungen an der Antwortzeit von Hive-Abfragen (um das bis zu 40-Fache) und an der Datenkomprimierung (bis zu 80 %) unter Verwendung des ORC-Formats (Optimized Row Columnar).

-   YARN: Ein neues allgemeines, verteiltes Framework für die Anwendungsverwaltung, welches das klassische Framework Apache Hadoop MapReduce für die Datenverarbeitung in Hadoop-Clustern ablöst. Es dient quasi als Hadoop-Betriebssystem und macht aus der einem einzigen Zweck dienenden Datenplattform für die Stapelverarbeitung eine Plattform mit vielerlei Verwendungszwecken, die eine stapelweise, interaktive Online- und Streamverarbeitung unterstützt. Dieses neue Management-Framework verbessert die Skalierbarkeit und Clusternutzung im Einklang mit wichtigen Kriterien wie Kapazitätsgarantien, Fairness und Servicelevel-Vereinbarungen.

-   Pig, Sqoop, Qozie, Ambari: Komponentenversionsupgrades für HDInsight Clusterversion 3.0 (HDP 2.0), die Parität mit der HDInsight Clusterversion 2.1 (HDP 1.3) bietet. Detaillierte Informationen finden Sie in den Versionstabellen unten. Beachten Sie, dass Hbase, Mahout und Flume nicht enthalten sind.

**Bereitstellung** Die Erstellung von HDInsight 3.0-Clustern auf Hadoop 2.2 wird vom Azure-Portal, HDInsight SDK und Azure PowerShell unterstützt.

**Globale Verfügbarkeit** Mit der Herausgabe von Azure HDInsight on Hadoop 2.2 macht Microsoft HDInsight in allen größeren Azure-Regionen verfügbar, mit Ausnahme des chinesischen Raums. Insbesondere Rechenzentren in Westeuropa und Südostasien wurden online geschaltet. So können die Kunden Cluster in einem Rechenzentrum finden, das sich in Ihrer Nähe befindet und potenziell in einer Zone mit ähnlichen Anforderungen zur Einhaltung von Vorschriften liegt.

**Wichtige Änderungen** In HDInsight 3.0-Clustern wird nur die "wasb://"-Syntax unterstützt. Die ältere "asv://"-Syntax wird in HDInsight 2.1- und 1.6-Clustern unterstützt, nicht aber in HDInsight 3.0-Clustern; sie werden auch in späteren Versionen nicht unterstützt. Dies bedeutet, dass alle an HDInsight-Cluster der Version 3.0 gesendeten Jobs, in denen explizit die “asv://”-Syntax verwendet wird, fehlschlagen. Stattdessen sollte die wasb://-Syntax verwendet werden. An HDInsight-Cluster der Version 3.0 gesendete Jobs, die mithilfe eines vorhandenen Metastores erzeugt wurden, der explizite Verweise auf Ressourcen mit der asv://-Syntax enthält, schlagen ebenfalls fehl. Diese Metastores müssen mit der wasb://-Syntax neu erstellt werden, um die Ressourcen zu adressieren.

HDInsight-Versionen
-------------------

HDInsight unterstützt mehrere Hadoop-Clusterversionen, die jederzeit bereitgestellt werden können. Jede ausgewählte Version stellt eine bestimmte Version der HTP-Distribution (HortonWorks Data Platform) und eine Reihe von Komponenten innerhalb dieser Distribution bereit.

### Clusterversion 3.0

Azure HDInsight unterstützt nun Hadoop 2.2. Es basiert auf der Hortonworks Data Platform Version 2.0 und bietet Hadoop-Dienste mit Komponentenversionen, die in folgender Tabelle aufgeführt sind:

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">Komponente</th><th data-morhtml="true">Version</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hadoop</td><td data-morhtml="true">2.2.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hive</td><td data-morhtml="true">0.12.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Pig</td><td data-morhtml="true">0.12</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Sqoop</td><td data-morhtml="true">1.4.4</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Oozie</td><td data-morhtml="true">4.0.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache HCatalog</td><td data-morhtml="true">Merged with Hive</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Templeton</td><td data-morhtml="true">Merged with Hive</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Ambari</td><td data-morhtml="true">API v1.0</td></tr>
</table>

### Clusterversion 2.1

Die Standardclusterversion, die von [Azure HDInsight](http://go.microsoft.com/fwlink/?LinkID=285601) verwendet wird, ist die Version 2.1. Sie basiert auf der Hortonworks Data Platform Version 1.3.0 und bietet Hadoop-Dienste mit Komponentenversionen, die in folgender Tabelle aufgeführt sind:

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">Komponente</th><th data-morhtml="true">Version</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hadoop</td><td data-morhtml="true">1.2.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hive</td><td data-morhtml="true">0.11.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Pig</td><td data-morhtml="true">0.11</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Sqoop</td><td data-morhtml="true">1.4.3</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Oozie</td><td data-morhtml="true">3.2.2</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache HCatalog</td><td data-morhtml="true">Merged with Hive</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Templeton</td><td data-morhtml="true">Merged with Hive</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Ambari</td><td data-morhtml="true">API v1.0</td></tr>
</table>

### Clusterversion 1.6

[Azure HDInsight](http://go.microsoft.com/fwlink/?LinkID=285601) Clusterversion 1.6 ist ebenfalls verfügbar. Sie basiert auf der Hortonworks Data Platform Version 1.1.0 und bietet Hadoop-Dienste mit Komponentenversionen, die in folgender Tabelle aufgeführt sind:

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">Komponente</th><th data-morhtml="true">Version</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hadoop</td><td data-morhtml="true">1.0.3</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Hive</td><td data-morhtml="true">0.9.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Pig</td><td data-morhtml="true">0.9.3</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Sqoop</td><td data-morhtml="true">1.4.2</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Oozie</td><td data-morhtml="true">3.2.0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache HCatalog</td><td data-morhtml="true">0.4.1</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Apache Templeton</td><td data-morhtml="true">0.1.4</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">SQL Server JDBC Driver</td><td data-morhtml="true">3.0</td></tr>
</table>

### Auswählen einer Version bei Bereitstellung eines HDInsight-Clusters

Wenn Sie einen Cluster über die HDInsight PowerShell-Cmdlets oder das HDInsight .NET SDK erstellen, können Sie die Version für das HDInsight Hadoop-Cluster über den Parameter "Version" auswählen.

Wenn Sie die Option **Quick Create** wählen, erhalten Sie standardmäßig Version 2.1 des HDInsight Hadoop-Clusters. Wenn Sie die Option **Custom Create** im Azure-Portal verwenden, können Sie die Version des Clusters, die Sie bereitstellen wollen, im Dropdown-Menü **HDInsight Version** auf der Seite **Cluster Details** auswählen. Version 3.0 des HDInsight Hadoop-Clusters ist nur als Option im Assistenten **Custom Create** verfügbar.

![HDI.Versioning.VersionScreen](./media/hdinsight-component-versioning/hdi-versioning-version-screen.png)

Unterstützte Versionen
----------------------

In der folgenden Tabelle sind die derzeit verfügbaren Versionen von HDInsight, die entsprechenden HDP-Versionen (Hortonworks Data Platform), die sie verwenden, und die zugehörigen Herausgabetermine aufgeführt. Sofern bekannt sind auch die jeweiligen Termine angegeben, ab wann sie als veraltet gelten.

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">HDInsight-Version</th><th data-morhtml="true">HDP-Version</th><th data-morhtml="true">Herausgabedatum</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">HDI 3.0</td><td data-morhtml="true">HDP 2.0</td><td data-morhtml="true">11.02.2014</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">HDI 2.1</td><td data-morhtml="true">HDP 1.3</td><td data-morhtml="true">28.10.2013</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">HDI 1.6</td><td data-morhtml="true">HDP 1.1</td><td data-morhtml="true">28.10.2013</td></tr>
</table>

### Die Servicelevel-Vereinbarung (SLA) für HDInsight-Clusterversionen

Die SLA ist in Form eines "Supportfensters" definiert. Ein Supportfenster bezieht sich auf den Zeitraum, die eine HDInsight-Clusterversion vom Microsoft-Kundensupport unterstützt wird. Ein HDInsight-Cluster gilt als außerhalb des Supportfensters, wenn das Supportablaufdatum einer Version das aktuelle Datum überschritten hat. In der Tabelle oben finden Sie eine Liste der unterstützten HDInsight-Clusterversionen. Das Supportablaufdatum für eine bestimmte HDInsight-Version (bezeichnet als Version X) wird berechnet als das spätere Datum von:

-   Formel 1: Addieren Sie 180 Tage zum Herausgabedatum von HDInsight-Clusterversion X
-   Formel 2: Addieren Sie 90 Tage zu dem Datum, an dem HDInsight-Clusterversion X+1 (die nachfolgende Version nach X) im Azure-Verwaltungsportal verfügbar gemacht wurde.

> [WACOM.NOTE] Sowohl HDInsight 2.1 als auch 3.0 verwenden die 64-Bit-Version von Windows 2008 R2 SP1 mit .NET Framework 4.0.

**Weitere Hinweise und Informationen zur Versionsverwaltung**

-   Der SQL Server JDBC Driver wird intern von HDInsight und nicht für externe Vorgänge verwendet. Wenn Sie eine Verbindung zu HDInsight über ODBC herstellen möchten, verwenden Sie den Microsoft Hive ODBC Driver. Weitere Informationen zur Verwendung von Hive ODBC finden Sie unter [Verbinden von Excel über den Microsoft Hive ODBC Driver mit HDInsight](/de-de/documentation/articles/hdinsight-connect-excel-hive-ODBC-driver).

-   HDInsight-Clusterversion 3.0 verwendet eine Hadoop-Distribution, die auf der [Hortonworks Data Platform 2.0](http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html) basiert.

-   HDInsight-Clusterversion 2.1 verwendet eine Hadoop-Distribution, die auf der [Hortonworks Data Platform 1.3](http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html) basiert. Dies ist der Hadoop-Standardcluster, der bei der Verwendung des Azure HDInsight-Portals erstellt wird.

-   HDInsight-Clusterversion 1.6 verwendet eine Hadoop-Distribution, die auf der [Hortonworks Data Platform 1.1](http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html) basiert.

-   Die mit den HDInsight-Clusterversionen verknüpften Komponentenversionen können sich bei künftigen Updates für HDInsight ändern. Eine Möglichkeit, die verfügbaren Komponenten zu bestimmen und zu prüfen, welche Versionen für einen Cluster verwendet werden, bietet die Ambari REST-API. Über den GetComponentInformation-Befehl können Informationen zu einer Dienstkomponente abgerufen werden. Ausführliche Informationen finden Sie in der [Ambari-Dokumentation](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md). Um diese Informationen zu erhalten, können Sie sich auch per Remote-Desktop bei einem Cluster anmelden und den Inhalt des Verzeichnisses "C:\\apps\\dist" direkt prüfen.


