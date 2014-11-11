<properties title="HDInsight Release Notes" pageTitle="HDInsight Release Notes | Azure" description="HDInsight release notes." metaKeywords="hdinsight, hadoop, hdinsight hadoop, hadoop azure, release notes" services="HDInsight" solutions="" documentationCenter="" editor="cgronlun" manager="paulettm"  authors="bradsev" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="bradsev" />

# Microsoft HDInsight – Versionshinweise

## Hinweise für die Version vom 21.08.2014

-   Es wird die folgende neue WebHCat-Konfiguration (HIVE-7155) hinzugefügt, mit der die Standardspeicherbegrenzung für einen Templeton-Controllerauftrag auf 1 GB festgelegt wird (der vorherige Standardwert war 512 MB):

    -   templeton.mapper.memory.mb (=1024)
    -   Mit dieser Änderung wird der folgende Fehler behoben, der bei bestimmten Hive-Abfragen aufgrund der niedrigeren Speicherbegrenzungen auftrat: „Container is running beyond physical memory limits“.
    -   Um die Einstellung auf den alten Standardwert zurückzusetzen, können Sie diesen Konfigurationswert zum Zeitpunkt der Clustererstellung über PowerShell SDK auf 512 festlegen. Verwenden Sie folgenden Befehl:

        Add-AzureHDInsightConfigValues -Core @{"templeton.mapper.memory.mb"="512";}

-   Der Hostname der Zookeeper-Rolle wurde in „zookeeper“ geändert. Das wirkt sich auf die Namensauflösung im Cluster aus; die externen REST APIs sind aber nicht betroffen. Wenn Sie Komponenten haben, die den Hostnamen „zookeepernode“ verwenden, müssen Sie sie für die Verwendung des neuen Namens aktualisieren. Die neuen Namen der drei Zookeeper-Knoten lauten:

    -   zookeeper0
    -   zookeeper1
    -   zookeeper2
-   Die HBase-Versionssupportmatrix wurde aktualisiert. Nur Version HDInsight 3.1 (HBase Version 0.98) wird für HBase-Produktionsarbeitslasten unterstützt. Version 3.0, die zur Vorschau verfügbar war, wird zukünftig nicht mehr unterstützt. Während der Übergangszeit können Kunden noch Cluster von Version 3.0 erstellen.

## Hinweise für die Version vom 28.07.14

-   **HDInsight in neuen Regionen verfügbar**: Mit dieser Version wurde die geografische Präsenz von HDInsight auf drei neue Regionen ausgeweitet. HDInsight-Kunden können jetzt in diesen Regionen Cluster erstellen.

    -   Ostasien,
    -   USA (Mitte/Norden) und
    -   USA (Mitte/Süden).
-   Mit dieser Version werden HDInsight v1.6 (HDP1.1, Hadoop 1.0.3) und HDInsight v2.1 (HDP1.3, Hadoop 1.2) aus dem Azure-Verwaltungsportal entfernt. Sie können weiterhin Hadoop-Cluster für diese Versionen erstellen, indem Sie HDInsight PowerShell-Cmdlets ([New-AzureHDInsightCluster][New-AzureHDInsightCluster]) oder das [HDInsight SDK][HDInsight SDK] verwenden. Weitere Informationen finden Sie auf der Seite [HDInsight-Komponentenversionen][HDInsight-Komponentenversionen].
-   Änderungen an Hortonworks Data Platform (HDP) in dieser Version:

| HDP               | Änderungen                                                   |
|-------------------|--------------------------------------------------------------|
| HDP 1.3 / HDI 2.1 | Keine Änderungen                                             |
| HDP 2.0 / HDI 3.0 | Keine Änderungen                                             |
| HDP 2.1 / HDI 3.1 | zookeeper: ['3.4.5.2.1.3.0-1948'] -\> ['3.4.5.2.1.3.2-0002'] |

## Hinweise für die Version vom 24.06.2014

Diese Version enthält mehrere neue Verbesserungen des HDInsight-Dienstes:

-   **HDP 2.1-Verfügbarkeit**: HDInsight 3.1 mit HDP 2.1 ist jetzt allgemein verfügbar und die Standardversion für neue Cluster.
-   **HBase – Verbesserungen am Azure-Verwaltungsportal**: HBase-Cluster werden in der Vorschau verfügbar gemacht. Über das Portal können HBase-Cluster jetzt in 3 Klicks erstellt werden.

![][0]

Mit HBase können Sie verschiedene Arbeitslasten in Echtzeit in HDInsight erstellen, von interaktiven Websites mit großen Datasets bis zu Diensten, die Sensor- und Telemetriedaten aus Millionen von Endpunkten speichern. Der nächste Schritt besteht im Analysieren der Daten in diesen Arbeitslasten mit Hadoop-Jobs. Dies ist in HDInsight dank der bereitgestellten Möglichkeiten wie PowerShell und Hive-Cluster-Dashboard unmittelbar möglich.

### Apache Mahout jetzt auf HDInsight 3.1 vorinstalliert

[Mahout][Mahout] ist auf Hadoop-Clustern auf HDInsight 3.1 vorinstalliert. Sie können also Mahout-Jobs ausführen, ohne eine zusätzliche Clusterkonfiguration vorzunehmen. Sie können beispielsweise remote auf einen Hadoop-Cluster über das Remote Desktop Protocol (RDP) zugreifen und ohne zusätzliche Schritte den Mahout-Befehl „Hello world“ ausführen:

        mahout org.apache.mahout.classifier.df.tools.Describe -p /user/hdp/glass.data -f /user/hdp/glass.info -d I 9 N L  

        mahout org.apache.mahout.classifier.df.BreimanExample -d /user/hdp/glass.data -ds /user/hdp/glass.info -i 10 -t 100

Eine umfassendere Erklärung dieses Verfahrens finden Sie in der Dokumentation zum [Breiman-Beispiel][Breiman-Beispiel] auf der Apache Mahout-Website.

### Hive-Abfragen können Tez in HDinsight 3.1 verwenden

Hive 0.13 ist jetzt in HDInsight 3.1 verfügbar und kann Abfragen mit Tez ausführen, was für erhebliche Leistungsverbesserungen genutzt werden kann.
Tez ist nicht standardmäßig für Hive-Abfragen aktiviert. Sie müssen die Option aktivieren, um sie verwenden zu können. Aktivieren Sie Tez, indem Sie das folgende Code-Snippet ausführen:

        set hive.execution.engine=tez;
        select sc_status, count(*), histogram_numeric(sc_bytes,5) from website_logs_orc_local group by sc_status;

Hortonworks hat eine detaillierte Aufschlüsselung der Leistungsverbesserungen bei Hive-Abfragen mit Tez veröffentlicht, die sich aus Standardvergleichstests ergaben. Einzelheiten finden Sie unter [Benchmark von Apache Hive 13 für Enterprise Hadoop][Benchmark von Apache Hive 13 für Enterprise Hadoop].

Weitere Einzelheiten zur Verwendung von Hive mit Tez finden Sie auf der [Wiki-Seite für Hive auf Tez][Wiki-Seite für Hive auf Tez].

### Globale Verfügbarkeit

Mit der Freigabe von Azure HDInsight auf Hadoop 2.2 hat Microsoft HDInsight in allen größeren Azure-Gebieten bereitgestellt. Insbesondere Rechenzentren in Westeuropa und Südostasien wurden online geschaltet. So können die Kunden Cluster in einem Rechenzentrum finden, das sich in Ihrer Nähe befindet und potenziell in einer Zone mit ähnlichen Anforderungen zur Einhaltung von Vorschriften liegt.

### Fehlerhafte Änderungen

**Präfixsyntax**:
 In HDInsight 3.0- und 3.1-Clustern wird nur die „wasb://“-Syntax unterstützt. Die ältere „asv://“-Syntax wird in HDInsight 2.1- und 1.6-Clustern unterstützt, nicht aber in HDInsight 3.0-Clustern oder späteren Versionen. Dies bedeutet, dass alle an einen HDInsight-Cluster der Version 3.0 oder 3.1 übermittelten Aufträge, die explizit die „asv://“-Syntax verwenden, fehlschlagen werden. Stattdessen sollte die wasb://-Syntax verwendet werden. An HDInsight-Cluster der Version 3.0 oder 3.1 gesendete Aufträge, die mithilfe eines vorhandenen Metastores erstellt wurden, der explizite Verweise auf Ressourcen mit der „asv://“-Syntax enthält, schlagen ebenfalls fehl. Diese Metastores müssen mit der wasb://-Syntax neu erstellt werden, um die Ressourcen zu adressieren.

**Ports**: Die vom HDInsight-Dienst verwendeten Ports wurden geändert. Die Portnummern, die verwendet wurden, befanden sich im kurzlebigen Portbereich des Windows-Betriebssystems. Ports werden automatisch von einem vordefinierten kurzlebigen Bereich für kurzlebige internetprotokollbasierte Kommunikationen zugewiesen. Die neue Reihe der zulässigen Portnummern des HDP-Dienstes (Hortonworks Data Platform) befinden sich nun außerhalb dieses Bereichs, um Konflikte zu vermeiden, die mit den Ports auftreten könnten, welche von auf dem Hauptknoten ausgeführten Diensten verwendet werden. Die neuen Portnummern dürften keine fehlerhaften Änderungen verursachen. Es werden jetzt folgende Nummern verwendet:

**HDInsight 1.6 (HDP 1.1)**

<table border="1">

<tr>
<th>
Name

</th>
<th>
Wert

</th>
</tr>

<tr>
<td>
dfs.http.address

</td>
<td>
namenodehost:30070

</td>
</tr>

<tr>
<td>
dfs.datanode.address

</td>
<td>
0.0.0.0:30010

</td>
</tr>

<tr>
<td>
dfs.datanode.http.address

</td>
<td>
0.0.0.0:30075

</td>
</tr>

<tr>
<td>
dfs.datanode.ipc.address

</td>
<td>
0.0.0.0:30020

</td>
</tr>

<tr>
<td>
dfs.secondary.http.address

</td>
<td>
0.0.0.0:30090

</td>
</tr>

<tr>
<td>
mapred.job.tracker.http.address

</td>
<td>
jobtrackerhost:30030

</td>
</tr>

<tr>
<td>
mapred.task.tracker.http.address

</td>
<td>
0.0.0.0:30060

</td>
</tr>

<tr>
<td>
mapreduce.history.server.http.address

</td>
<td>
0.0.0.0:31111

</td>
</tr>

<tr>
<td>
templeton.port

</td>
<td>
30111

</td>
</tr>

</table>

</p>
**HDInsight 3.0 und 3.1 (HDP 2.0 und 2.1)**

<table border="1">

<tr>
<th>
Name

</th>
<th>
Wert

</th>
</tr>

<tr>
<td>
dfs.namenode.http-address

</td>
<td>
namenodehost:30070

</td>
</tr>

<tr>
<td>
dfs.namenode.https-address

</td>
<td>
headnodehost:30470

</td>
</tr>

<tr>
<td>
dfs.datanode.address

</td>
<td>
0.0.0.0:30010

</td>
</tr>

<tr>
<td>
dfs.datanode.http.address

</td>
<td>
0.0.0.0:30075

</td>
</tr>

<tr>
<td>
dfs.datanode.ipc.address

</td>
<td>
0.0.0.0:30020

</td>
</tr>

<tr>
<td>
dfs.namenode.secondary.http-address

</td>
<td>
0.0.0.0:30090

</td>
</tr>

<tr>
<td>
yarn.nodemanager.webapp.address

</td>
<td>
0.0.0.0:30060

</td>
</tr>

<tr>
<td>
templeton.port

</td>
<td>
30111

</td>
</tr>

</table>

</p>
### Abhängigkeiten

Die folgende Abhängigkeiten wurden in HDInsight 3.x (HDP2.x) hinzugefügt:

-   guice-servlet
-   optiq-core
-   javax.inject
-   activation
-   jsr305
-   geronimo-jaspic\_1.0\_spec
-   jul-to-slf4j
-   java-xmlbuilder
-   ant
-   commons-compiler
-   jdo-api
-   commons-math3
-   paranamer
-   jaxb-impl
-   stringtemplate
-   eigenbase-xom
-   jersey-servlet
-   commons-exec
-   jaxb-api
-   jetty-all-server
-   janino
-   xercesImpl
-   optiq-avatica
-   jta
-   eigenbase-properties
-   groovy-all
-   hamcrest-core
-   mail
-   linq4j
-   jpam
-   jersey-client
-   aopalliance
-   geronimo-annotation\_1.0\_spec
-   ant-launcher
-   jersey-guice
-   xml-apis
-   stax-api
-   asm-commons
-   asm-tree
-   wadl
-   geronimo-jta\_1.1\_spec
-   guice
-   leveldbjni-all
-   velocity
-   jettison
-   snappy-java
-   jetty-all
-   commons-dbcp

Die folgenden Abhängigkeiten bestehen in HDInsight 3.x (HDP2.x) nicht mehr:

-   jdeb
-   kfs
-   sqlline
-   ivy
-   aspectjrt
-   json
-   core
-   jdo2-api
-   avro-mapred
-   datanucleus-enhancer
-   jsp
-   commons-logging-api
-   commons-math
-   JavaEWAH
-   aspectjtools
-   javolution
-   hdfsproxy
-   hbase
-   snappy

### Versionsänderungen

Die folgenden Versionsänderungen wurden zwischen HDInsight 2.x (HDP1.x) und HDInsight 3.x (HDP2.x) vorgenommen:

-   metrics-core: ['2.1.2'] -\> ['3.0.0']
-   derbynet: ['10.4.2.0'] -\> ['10.10.1.1']
-   datanucleus: ['rdbms-3.0.8'] -\> ['rdbms-3.2.9']
-   jasper-compiler: ['5.5.12'] -\> ['5.5.23']
-   log4j: ['1.2.15', '1.2.16'] -\> ['1.2.16', '1.2.17']
-   derbyclient: ['10.4.2.0'] -\> ['10.10.1.1']
-   httpcore: ['4.2.4'] -\> ['4.2.5']
-   hsqldb: ['1.8.0.10'] -\> ['2.0.0']
-   jets3t: ['0.6.1'] -\> ['0.9.0']
-   protobuf-java: ['2.4.1'] -\> ['2.5.0']
-   derby: ['10.4.2.0'] -\> ['10.10.1.1']
-   jasper: ['runtime-5.5.12'] -\> ['runtime-5.5.23']
-   commons-daemon: ['1.0.1'] -\> ['1.0.13']
-   datanucleus-core: ['3.0.9'] -\> ['3.2.10']
-   datanucleus-api-jdo: ['3.0.7'] -\> ['3.2.6']
-   zookeeper: ['3.4.5.1.3.9.0-01320'] -\> ['3.4.5.2.1.3.0-1948']
-   bonecp: ['0.7.1.RELEASE'] -\> ['0.8.0.RELEASE']

### Treiber

Der SQL Server JDBC Driver wird intern von HDInsight und nicht für externe Vorgänge verwendet. Wenn Sie eine Verbindung zu HDInsight über ODBC herstellen möchten, verwenden Sie den Microsoft Hive ODBC Driver. Weitere Informationen zur Verwendung von Hive ODBC finden Sie unter [Verbinden von Excel über den Microsoft Hive ODBC Driver mit HDInsight][connect-excel-with-hive-ODBC].

### Fehlerbehebungen

In dieser Version wurden die folgenden HDInsight-Versionen (Hortonworks Data Plattform - HDP) mit mehreren Fehlerbehebungen aktualisiert:

-   HDInsight 2.1 (HDP 1.3)
-   HDInsight 3.0 (HDP 2.0)
-   HDInsight 3.1 (HDP 2.1)

## Hortonworks-Versionshinweise

Versionshinweise für die HDPs, die von den Versionen von HDInsight-Cluster verwendet werden, finden sich an folgenden Speicherorten.

-   HDInsight-Clusterversion 3.1 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2.1][Hortonworks Data Platform 2.1] basiert. (Dies ist der Hadoop-Cluster, der bei Verwendung des Azure HDInsight-Portals standardmäßig erstellt wird.)

-   HDInsight-Clusterversion 3,0 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 2,0][Hortonworks Data Platform 2,0] basiert.

-   HDInsight-Clusterversion 2,1 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 1,3][Hortonworks Data Platform 1,3] basiert.

-   HDInsight-Clusterversion 1.6 verwendet eine Hadoop-Distribution, die auf [Hortonworks Data Platform 1.1][Hortonworks Data Platform 1.1] basiert.

  [New-AzureHDInsightCluster]: http://msdn.microsoft.com/de-de/library/dn593744.aspx
  [HDInsight SDK]: http://msdn.microsoft.com/de-de/library/azure/dn469975.aspx
  [HDInsight-Komponentenversionen]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-component-versioning/
  [0]: http://i.imgur.com/cmOl5fM.png
  [Mahout]: http://hortonworks.com/hadoop/mahout/
  [Breiman-Beispiel]: https://mahout.apache.org/users/classification/breiman-example.html
  [Benchmark von Apache Hive 13 für Enterprise Hadoop]: http://hortonworks.com/blog/benchmarking-apache-hive-13-enterprise-hadoop/
  [Wiki-Seite für Hive auf Tez]: https://cwiki.apache.org/confluence/display/Hive/Hive+on+Tez
  [Hortonworks Data Platform 2.1]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.1.1/bk_releasenotes_hdp_2.1/content/ch_relnotes-hdp-2.1.1.html
  [Hortonworks Data Platform 2,0]: http://docs.hortonworks.com/HDPDocuments/HDP2/HDP-2.0.8.0/bk_releasenotes_hdp_2.0/content/ch_relnotes-hdp2.0.8.0.html
  [Hortonworks Data Platform 1,3]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-1.3.0/bk_releasenotes_hdp_1.x/content/ch_relnotes-hdp1.3.0_1.html
  [Hortonworks Data Platform 1.1]: http://docs.hortonworks.com/HDPDocuments/HDP1/HDP-Win-1.1/bk_releasenotes_HDP-Win/content/ch_relnotes-hdp-win-1.1.0_1.html
