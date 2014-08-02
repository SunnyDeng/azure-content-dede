<properties linkid="manage-services-hdinsight-use-Ambari" urlDisplayName="Monitor HDInsight clusters using the Ambari API" pageTitle="Monitor HDInsight clusters using the Ambari API | Azure" metaKeywords="" description="Use the Apache Ambari APIs for provisioning, managing, and monitoring Hadoop clusters. Ambari's intuitive operator tools and APIs hide the complexity of Hadoop." services="hdinsight" documentationCenter="" title="Monitor HDInsight clusters using the Ambari API" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

Überwachen von HDInsight-Clustern mit der Ambari-API
====================================================

Sie erfahren, wie HDInsight 2.1-Cluster mithilfe der Ambari-APIs überwacht werden.

**Geschätzter Zeitaufwand:** 15 Minuten

Themen in diesem Artikel
------------------------

-   [Was ist Ambari?](#whatisambari)
-   [Voraussetzungen](#prerequisites)
-   [Schneller Einstieg](#jumpstart)
-   [Ambari-Überwachungs-APIs](#monitor)
-   [Nächste Schritte](#nextsteps)

Was ist Ambari?
---------------

[Apache Ambari](http://ambari.apache.org/) ist für die Bereitstellung, Vewaltung und Überwachung von Apache Hadoop-Clustern vorgesehen. Es umfasst eine Sammlung intuitiver Operatortools und eine Reihe robuster APIs, welche die Komplexität von Hadoop verbergen und den Betrieb von Clustern vereinfachen. Weitere Informationen über die APIs finden Sie unter [Ambari API Reference](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md) (Referenz zur Ambari-API, in englischer Sprache).

HDInsight unterstützt derzeit nur das Ambari-Überwachungsfeature. Ambari API Version 1.0 wird von HDInsight-Clustern der Versionen 2.1 und 3.0 unterstützt. Dieser Artikel befasst sich nur mit der Ausführung von Ambari-APIs auf HDInsight-Clustern der Version 2.1.

Voraussetzungen
---------------

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

-   **Eine Arbeitsstation**, auf der Azure PowerShell installiert und konfiguriert ist. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell/). Um PowerShell-Skripts auszuführen, müssen Sie Azure PowerShell als Administrator ausführen und die Ausführungsrichtlinie auf *RemoteSigned* festlegen. Weitere Informationen finden Sie unter [Running Windows PowerShell Scripts](http://technet.microsoft.com/en-us/library/ee176949.aspx) (Ausführen von Windows PowerShell-Skripts, in englischer Sprache).

    [Curl](http://curl.haxx.se) ist optional und kann von [hier](http://curl.haxx.se/download.html) installiert werden.

    > [WACOM.NOTE] Wenn Sie den Curl-Befehl in Windows verwenden, geben Sie für die Optionswerte doppelte anstelle einfacher Anführungszeichen ein.

-   **Einen Azure HDInsight-Cluster**. Anweisungen zum Bereitstellen des Clusters finden Sie unter [Erste Schritte mit HDInsight](../hdinsight-get-started/) oder unter [Bereitstellen von HDInsight-Clustern](../hdinsight-provision-clusters/). Zum Durcharbeiten des Lernprogramms benötigen Sie die folgenden Daten:

    <table data-morhtml="true" border="1">
  <tr data-morhtml="true"><th data-morhtml="true">Clustereigenschaft</th><th data-morhtml="true">PowerShell-Variablenname</th><th data-morhtml="true">Wert</th><th data-morhtml="true">Beschreibung</th></tr>
  <tr data-morhtml="true"><td data-morhtml="true">HDInsight-Clustername</td><td data-morhtml="true">$clusterName</td><td data-morhtml="true"></td><td data-morhtml="true">Der Name des HDInsight-Clusters.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Cluster-Benutzername</td><td data-morhtml="true">$clusterUsername</td><td data-morhtml="true"></td><td data-morhtml="true">Der bei der Bereitstellung angegebene Cluster-Benutzername.</td></tr>
  <tr data-morhtml="true"><td data-morhtml="true">Cluster-Kennwort</td><td data-morhtml="true">$clusterPassword</td><td data-morhtml="true"></td><td data-morhtml="true">Das Benutzerkennwort für den Cluster.</td></tr>
  </table>

    > [WACOM.NOTE] Tragen Sie die Werte in die Tabellen ein. Dies ist beim Durcharbeiten dieses Lernprogramms hilfreich.

Schneller Einstieg
------------------

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

![JobTracker-Ausgabe](./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png)

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

Ambari-Überwachungs-APIs
------------------------

In der folgenden Tabelle sind einige der am häufigsten verwendeten Ambari-Überwachungs-API-Aufrufe aufgeführt. Weitere Informationen über die API finden Sie unter [Ambari API Reference](https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md) (Referenz zur Ambari-API, in englischer Sprache).

<table data-morhtml="true" border="1">
<tr data-morhtml="true"><th data-morhtml="true">Überwachungs-API-Aufruf</th><th data-morhtml="true">URI</th><th data-morhtml="true">Beschreibung</th></tr>
<tr data-morhtml="true"><td data-morhtml="true">Cluster abrufen</td><td data-morhtml="true">/api/v1/clusters</td><td data-morhtml="true"></td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Clusterinformationen abrufen.</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net</td><td data-morhtml="true">Cluster, Dienste, Hosts</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Dienste abrufen</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services</td><td data-morhtml="true">Zu den Diensten gehören: hdfs, mapreduce</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Dienstinformationen abrufen.</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;</td><td data-morhtml="true"></td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Dienstkomponenten abrufen</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components</td><td data-morhtml="true">HDFS: namenode, datanode<br data-morhtml="true" />MapReduce: jobtracker; tasktracker</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Komponenteninformationen abrufen.</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components/&lt;ComponentName&gt;</td><td data-morhtml="true">ServiceComponentInfo, Hostkomponenten, Metriken</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Hosts abrufen</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts</td><td data-morhtml="true">headnode0, workernode0</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Hostinformationen abrufen.</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt; 
</td><td data-morhtml="true"></td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Hostkomponenten abrufen</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components
</td><td data-morhtml="true">namenode, resourcemanager</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Host-Komponenteninformationen abrufen.</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components/&lt;ComponentName&gt;
</td><td data-morhtml="true">HostRoles, Komponente, Host, Metriken</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Konfigurationen abrufen</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations 
</td><td data-morhtml="true">Konfigurationstypen: core-site, hdfs-site, mapred-site, hive-site</td></tr>
<tr data-morhtml="true"><td data-morhtml="true">Konfigurationsinformationen abrufen.</td><td data-morhtml="true">/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations?type=&lt;ConfigType&gt;&amp;tag=&lt;VersionName&gt; 
</td><td data-morhtml="true">Konfigurationstypen: core-site, hdfs-site, mapred-site, hive-site</td></tr>
</table>

Nächste Schritte
----------------

Sie haben erfahren, wie Ambari-Überwachungs-API-Aufrufe verwendet werden. Weitere Informationen finden Sie unter:

-   [Verwalten von HDInsight-Clustern mit dem Verwaltungsportal](../hdinsight-administer-use-management-portal/)
-   [Verwalten von HDInsight-Clustern mit Azure PowerShell](../hdinsight-administer-use-powershell/)
-   [Verwalten von HDInsight-Clustern mit der Befehlszeilenschnittstelle](../hdinsight-administer-use-command-line/)
-   [HDInsight-Dokumentation](/en-us/documentation/services/hdinsight/)
-   [Erste Schritte mit HDInsight](../hdinsight-get-started/)

