<properties linkid="manage-services-hdinsight-use-Ambari" urlDisplayName="Monitor Hadoop clusters  in HDInsight using the Ambari API" pageTitle="Monitor Hadoop clusters in HDInsight using the Ambari API | Azure" metaKeywords="" description="Use the Apache Ambari APIs for provisioning, managing, and monitoring Hadoop clusters. Ambari's intuitive operator tools and APIs hide the complexity of Hadoop." services="hdinsight" documentationCenter="" title="Monitor Hadoop clusters in HDInsight using the Ambari API" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Überwachen von Hadoop-Clustern in HDInsight mit der Ambari API

Sie erfahren, wie HDInsight 2.1-Cluster mithilfe der Ambari-APIs überwacht werden.

**Geschätzter Zeitaufwand:** 15 Minuten

## Themen in diesem Artikel

-   [Was ist Ambari?][]
-   [Voraussetzungen][]
-   [Schneller Einstieg][]
-   [Ambari-Überwachungs-APIs][]
-   [Nächste Schritte][]

## <span id="whatisambari"></span></a>Was ist Ambari?

[Apache Ambari][] dient zur Bereitstellung, Verwaltung und Überwachung von Apache Hadoop-Clustern. Es umfasst eine Sammlung intuitiver Operatortools und eine Reihe robuster APIs, welche die Komplexität von Hadoop verbergen und den Betrieb von Clustern vereinfachen. Weitere Informationen über die APIs finden Sie unter [Ambari API Reference][] (Referenz zur Ambari-API, in englischer Sprache).

HDInsight unterstützt derzeit nur das Ambari-Überwachungsfeature. Ambari API Version 1.0 wird von HDInsight-Clustern der Versionen 2.1 und 3.0 unterstützt. Dieser Artikel befasst sich nur mit der Ausführung von Ambari-APIs auf HDInsight-Clustern der Version 2.1.

## <span id="prerequisites"></span></a>Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   **Eine Arbeitsstation**, auf der Azure PowerShell installiert und konfiguriert ist. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][]. Um PowerShell-Skripts ausführen zu können, müssen Sie Azure PowerShell als Administrator ausführen und die Ausführungsrichtlinie auf *RemoteSigned* setzen. Siehe [Ausführen von Windows PowerShell-Skripts][].

    [Curl][] ist optional und kann von [hier][] installiert werden.

    > [WACOM.NOTE] Wenn Sie den Curl-Befehl in Windows verwenden, geben Sie für die Optionswerte doppelte anstelle einfacher Anführungszeichen ein.

-   **Einen Azure HDInsight-Cluster**. Anweisungen zur Bereitstellung von Clustern finden Sie unter [Erste Schritte mit HDInsight][] oder unter [Bereitstellen von HDInsight-Clustern][]. Sie benötigen die folgenden Daten, um das Lernprogramm durchzuarbeiten:

    <table>
    <colgroup>
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    <col width="25%" />
    </colgroup>
    <thead>
    <tr class="header">
    <th align="left">Clustereigenschaft</th>
    <th align="left">PowerShell-Variablenname</th>
    <th align="left">Wert</th>
    <th align="left">Beschreibung</th>
    </tr>
    </thead>
    <tbody>
    <tr class="odd">
    <td align="left">HDInsight-Clustername</td>
    <td align="left">$clusterName</td>
    <td align="left"></td>
    <td align="left">Der Name des HDInsight-Clusters.</td>
    </tr>
    <tr class="even">
    <td align="left">Cluster-Benutzername</td>
    <td align="left">$clusterUsername</td>
    <td align="left"></td>
    <td align="left">Der bei der Bereitstellung angegebene Cluster-Benutzername.</td>
    </tr>
    <tr class="odd">
    <td align="left">Cluster-Kennwort</td>
    <td align="left">$clusterPassword</td>
    <td align="left"></td>
    <td align="left">Das Benutzerkennwort für den Cluster.</td>
    </tr>
    </tbody>
    </table>

    > [WACOM.NOTE] Tragen Sie die Werte in die Tabellen ein. Dies wird Ihnen helfen, wenn Sie dieses Lernprogramm durcharbeiten.

## <span id="jumpstart"></span></a>Schneller Einstieg

Es gibt verschiedene Möglichkeiten, Ambari zur Überwachung von HDInsight-Clustern einzusetzen.

**Verwenden von Azure PowerShell**

Das folgende Skript ist ein PowerShell-Skript zum Abrufen der MapReduce-JobTracker-Informationen:

    $clusterName = "<HDInsightClusterName>"
    $clusterUsername = "<HDInsightClusterUsername>"
    $clusterPassword = "<HDInsightClusterPassword>"

    $ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
    $uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/mapreduce/components/jobtracker"

    $passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
    $creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)

    $response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 

    $response.metrics.'mapred.JobTracker'

Die Ausgabe ist:

![JobTracker-Ausgabe][]

**Verwenden von curl**

Es folgt ein Beispiel zum Abrufen von Clusterinformationen mithilfe von Curl:

    curl -u <username>:<password> -k https://<ClusterName>.azurehdinsight.net:443/ambari/api/v1/clusters/<ClusterName>.azurehdinsight.net

Die Ausgabe ist:

    {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/",
     "Clusters":{"cluster_name":"hdi0211v2.azurehdinsight.net","version":"2.1.3.0.432823"},
     "services"[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/hdfs",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"hdfs"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/services/mapreduce",
        "ServiceInfo":{"cluster_name":"hdi0211v2.azurehdinsight.net","service_name":"mapreduce"}}],
     "hosts":[
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/headnode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"headnode0"}},
       {"href":"https://hdi0211v2.azurehdinsight.net/ambari/api/v1/clusters/hdi0211v2.azurehdinsight.net/hosts/workernode0",
        "Hosts":{"cluster_name":"hdi0211v2.azurehdinsight.net",
                 "host_name":"workernode0"}}]}

## <span id="monitor"></span></a>Ambari-Überwachungs-APIs

In der folgenden Tabelle sind einige der am häufigsten verwendeten Ambari-Überwachungs-API-Aufrufe aufgeführt. Weitere Informationen über die API finden Sie unter [Ambari API Reference][] (Referenz zur Ambari-API, in englischer Sprache).

<table>
<colgroup>
<col width="33%" />
<col width="33%" />
<col width="33%" />
</colgroup>
<thead>
<tr class="header">
<th align="left">Überwachungs-API-Aufruf</th>
<th align="left">URI</th>
<th align="left">Beschreibung</th>
</tr>
</thead>
<tbody>
<tr class="odd">
<td align="left">Cluster abrufen</td>
<td align="left">/api/v1/clusters</td>
<td align="left"></td>
</tr>
<tr class="even">
<td align="left">Clusterinformationen abrufen.</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net</td>
<td align="left">Cluster, Dienste, Hosts</td>
</tr>
<tr class="odd">
<td align="left">Dienste abrufen</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services</td>
<td align="left">Zu den Diensten gehören: hdfs, mapreduce</td>
</tr>
<tr class="even">
<td align="left">Dienstinformationen abrufen.</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Dienstkomponenten abrufen</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components</td>
<td align="left">HDFS: namenode, datanode<br />MapReduce: jobtracker; tasktracker</td>
</tr>
<tr class="even">
<td align="left">Komponenteninformationen abrufen.</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components/&lt;ComponentName&gt;</td>
<td align="left">ServiceComponentInfo, Hostkomponenten, Metriken</td>
</tr>
<tr class="odd">
<td align="left">Hosts abrufen</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts</td>
<td align="left">headnode0, workernode0</td>
</tr>
<tr class="even">
<td align="left">Hostinformationen abrufen.</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;</td>
<td align="left"></td>
</tr>
<tr class="odd">
<td align="left">Hostkomponenten abrufen</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components</td>
<td align="left">namenode, resourcemanager</td>
</tr>
<tr class="even">
<td align="left">Host-Komponenteninformationen abrufen.</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components/&lt;ComponentName&gt;</td>
<td align="left">HostRoles, Komponente, Host, Metriken</td>
</tr>
<tr class="odd">
<td align="left">Konfigurationen abrufen</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations</td>
<td align="left">Konfigurationstypen: core-site, hdfs-site, mapred-site, hive-site</td>
</tr>
<tr class="even">
<td align="left">Konfigurationsinformationen abrufen.</td>
<td align="left">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations?type=&lt;ConfigType&gt;&amp;tag=&lt;VersionName&gt;</td>
<td align="left">Konfigurationstypen: core-site, hdfs-site, mapred-site, hive-site</td>
</tr>
</tbody>
</table>

## <span id="nextsteps"></span></a>Nächste Schritte

Sie haben erfahren, wie Ambari-Überwachungs-API-Aufrufe verwendet werden. Weitere Informationen finden Sie unter:

-   [Verwalten von HDInsight-Clustern mit dem Verwaltungsportal][]
-   [Verwalten von HDInsight-Clustern mit Azure PowerShell][]
-   [Verwalten von HDInsight-Clustern mit der Befehlszeilenschnittstelle][]
-   [HDInsight-Dokumentation][]
-   [Erste Schritte mit HDInsight][]

  [Was ist Ambari?]: #whatisambari
  [Voraussetzungen]: #prerequisites
  [Schneller Einstieg]: #jumpstart
  [Ambari-Überwachungs-APIs]: #monitor
  [Nächste Schritte]: #nextsteps
  [Apache Ambari]: http://ambari.apache.org/
  [Ambari API Reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md
  [Installieren und Konfigurieren von Azure PowerShell]: ../install-configure-powershell/
  [Ausführen von Windows PowerShell-Skripts]: http://technet.microsoft.com/en-us/library/ee176949.aspx
  [Curl]: http://curl.haxx.se
  [hier]: http://curl.haxx.se/download.html
  [Erste Schritte mit HDInsight]: ../hdinsight-get-started/
  [Bereitstellen von HDInsight-Clustern]: ../hdinsight-provision-clusters/
  [JobTracker-Ausgabe]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png
  [Verwalten von HDInsight-Clustern mit dem Verwaltungsportal]: ../hdinsight-administer-use-management-portal/
  [Verwalten von HDInsight-Clustern mit Azure PowerShell]: ../hdinsight-administer-use-powershell/
  [Verwalten von HDInsight-Clustern mit der Befehlszeilenschnittstelle]: ../hdinsight-administer-use-command-line/
  [HDInsight-Dokumentation]: /de-de/documentation/services/hdinsight/
