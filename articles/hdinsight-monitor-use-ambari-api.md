<properties urlDisplayName="Monitor Hadoop clusters  in HDInsight using the Ambari API" pageTitle="Überwachen von Hadoop-Clustern in HDInsight mit der Ambari API | Azure" metaKeywords="" description="Verwenden Sie die Apache Ambari-APIs für die Bereitstellung, Verwaltung und Überwachung von Hadoop-Clustern. Die intuitiven Operatortools und APIs von Ambari verbergen die Komplexität von Hadoop." services="hdinsight" documentationCenter="" title="Monitor Hadoop clusters in HDInsight using the Ambari API" umbracoNaviHide="0" disqusComments="1" authors="jgao" editor="cgronlun" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="jgao" />

# Überwachen von Hadoop-Clustern in HDInsight mit der Ambari API
 
Erfahren Sie, wie Sie HDInsight-Cluster der Versionen 3.1 und 2.1 mit den Ambari-APIs überwachen können.

##Themen in diesem Artikel

- [Was ist Ambari?](#whatisambari)
- [Voraussetzungen](#prerequisites)
- [Schneller Einstieg](#jumpstart)
- [Ambari-Überwachungs-APIs](#monitor)
- [Nächste Schritte](#nextsteps)


## <a id="whatisambari"></a> Was ist Ambari?

[Apache Ambari][ambari-home] dient zur Bereitstellung, Verwaltung und Überwachung von Apache Hadoop-Clustern. Es umfasst eine Sammlung intuitiver Operatortools und eine Reihe robuster APIs, welche die Komplexität von Hadoop verbergen und den Betrieb von Clustern vereinfachen. Weitere Informationen zu den APIs finden Sie in der [Ambari API-Referenz][ambari-api-reference]..


HDInsight unterstützt derzeit nur das Ambari-Überwachungsfeature. Ambari API Version 1.0 wird von HDInsight-Clustern der Versionen 2.1 und 3.0 unterstützt.  Dieser Artikel behandelt den Zugriff auf HDInsight-Cluster der Versionen 3.1 und 2.1 mithilfe der Ambari-APIs.  Die beiden Versionen unterscheiden sich hauptsächlich darin, dass sich bestimmte Komponenten mit der Einführung neuer Funktionen geändert haben (z. B. der Auftragsverlauf-Server).


##<a id="prerequisites"></a>Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Eine Arbeitsstation**, auf der Azure PowerShell installiert und konfiguriert ist. Anweisungen hierzu finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install]. Um PowerShell-Skripts ausführen zu können, müssen Sie Azure PowerShell als Administrator ausführen und die Ausführungsrichtlinie auf "RemoteSigned" festlegen. Siehe [Ausführen von Windows PowerShell-Skripts][powershell-script].

	[Curl][curl] ist optional und kann von [hier][curl-download] installiert werden.

	>[WACOM.NOTE] Wenn Sie den Curl-Befehl in Windows verwenden, geben Sie für die Optionswerte doppelte anstelle einfacher Anführungszeichen ein.

- **Einen Azure HDInsight-Cluster**. Anweisungen zur Bereitstellung von Clustern finden Sie unter [Erste Schritte mit HDInsight][hdinsight-get-started] oder unter [Bereitstellen von HDInsight-Clustern][hdinsight-provision]. Sie benötigen die folgenden Daten, um das Lernprogramm durchzuarbeiten:

	<table border="1">
	<tr><th>Clustereigenschaft</th><th>PowerShell-Variablenname</th><th>Wert</th><th>Beschreibung</th></tr>
	<tr><td>HDInsight-Clustername</td><td>$clusterName</td><td></td><td>Der Name des HDInsight-Clusters.</td></tr>
	<tr><td>Cluster-Benutzername</td><td>$clusterUsername</td><td></td><td>Der bei der Bereitstellung angegebene Cluster-Benutzername.</td></tr>
	<tr><td>Cluster-Kennwort</td><td>$clusterPassword</td><td></td><td>Das Benutzerkennwort für den Cluster.</td></tr>
	</table>

	> [WACOM.NOTE] Tragen Sie die Werte in die Tabellen ein.  Dies wird Ihnen helfen, wenn Sie dieses Lernprogramm durcharbeiten.



##<a id="jumpstart"></a>Schneller Einstieg

Es gibt verschiedene Möglichkeiten, Ambari zur Überwachung von HDInsight-Clustern einzusetzen.

**Verwenden von Azure PowerShell**

Hier sehen Sie ein PowerShell-Skript zum Abrufen der MapReduce Jobtracker-Informationen für einen 3.1-Cluster.  Der wichtigste Unterschied hier besteht darin, dass wir diese Informationen nun vom YARN-Dienst abrufen (anstelle von MapReduce).

	$clusterName = "<HDInsightClusterName>"
	$clusterUsername = "<HDInsightClusterUsername>"
	$clusterPassword = "<HDInsightClusterPassword>"
	
	$ambariUri = "https://$clusterName.azurehdinsight.net:443/ambari"
	$uriJobTracker = "$ambariUri/api/v1/clusters/$clusterName.azurehdinsight.net/services/yarn/components/resourcemanager"
	
	$passwd = ConvertTo-SecureString $clusterPassword -AsPlainText -Force
	$creds = New-Object System.Management.Automation.PSCredential ($clusterUsername, $passwd)
	
	$response = Invoke-RestMethod -Method Get -Uri $uriJobTracker -Credential $creds -OutVariable $OozieServerStatus 
	
	$response.metrics.'yarn.queueMetrics'

Hier sehen Sie ein PowerShell-Skript zum Abrufen der MapReduce Jobtracker-Informationen für einen 2.1-Cluster.

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

![Jobtracker Output][img-jobtracker-output]

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
	             "host_name":"headnode0.{ClusterDNS}.azurehdinsight.net"}}]}

Hinweis für die Version vom 08.10.2014:
Beim Verwenden des Ambari-Endpunkts https://{clusterDns}.azurehdinsight.net/ambari/api/v1/clusters/{clusterDns}.azurehdinsight.net/services/{servicename}/components/{componentname} gibt das Feld host_name nun den vollqualifizierten Domänennamen (FQDN) des Knotens anstelle des Hostnamens zurück. Vor der Version vom 8.10.2014 hat dieses Beispiel lediglich "**headnode0**" zurückgegeben. Seit dem 8.10.2014 wird der FQDN "**headnode0.{ClusterDNS}.azurehdinsight.net**" zurückgegeben, wie im obigen Beispiel gezeigt. Diese Änderung erleichtert Szenarien, in denen mehrere Clustertypen wie z. B. HBase und Hadoop in einem einzigen virtuellen Netzwerk (VNET) bereitgestellt werden. Dies ist z. B. der Fall, wenn HBase als Back-End-Plattform für Hadoop verwendet wird.

##<a id="monitor"></a>Ambari-Überwachungs-APIs

In der folgenden Tabelle sind einige der am häufigsten verwendeten Ambari-Überwachungs-API-Aufrufe aufgeführt. Weitere Informationen zur API finden Sie in der [Ambari API-Referenz][ambari-api-reference].

<table border="1">
<tr><th>Überwachungs-API-Aufruf</th><th>URI</th><th>Beschreibung</th></tr>
<tr><td>Cluster abrufen</td><td><tt>/api/v1/clusters</tt></td><td></td></tr>
<tr><td>Clusterinformationen abrufen.</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net</tt></td><td>Cluster, Dienste, Hosts</td></tr>
<tr><td>Dienste abrufen</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services</tt></td><td>Services include: hdfs, mapreduce</td></tr>
<tr><td>Dienstinformationen abrufen.</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;</tt></td><td></td></tr>
<tr><td>Dienstkomponenten abrufen</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components</tt></td><td>HDFS: namenode, datanode<br/>MapReduce: jobtracker; tasktracker</td></tr>
<tr><td>Komponenteninformationen abrufen.</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/services/&lt;ServiceName&gt;/components/&lt;ComponentName&gt;</tt></td><td>ServiceComponentInfo, Hostkomponenten, Metriken</td></tr>
<tr><td>Hosts abrufen</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts</tt></td><td>headnode0, workernode0</td></tr>
<tr><td>Hostinformationen abrufen.</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt; 
</td><td></td></tr>
<tr><td>Hostkomponenten abrufen</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components
</tt></td><td>namenode, resourcemanager</td></tr>
<tr><td>Host-Komponenteninformationen abrufen.</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/hosts/&lt;HostName&gt;/host_components/&lt;ComponentName&gt;
</tt></td><td>HostRoles, Komponente, Host, Metriken</td></tr>
<tr><td>Konfigurationen abrufen</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations 
</tt></td><td>Config types: core-site, hdfs-site, mapred-site, hive-site</td></tr>
<tr><td>Konfigurationsinformationen abrufen.</td><td><tt>/api/v1/clusters/&lt;ClusterName&gt;.azurehdinsight.net/configurations?type=&lt;ConfigType&gt;&tag=&lt;VersionName&gt; 
</tt></td><td>Config types: core-site, hdfs-site, mapred-site, hive-site</td></tr>
</table>


##<a id="nextsteps"></a>Nächste Schritte 

Sie haben erfahren, wie Ambari-Überwachungs-API-Aufrufe verwendet werden. Weitere Informationen finden Sie unter:

- [Verwalten von HDInsight-Clustern mit dem Verwaltungsportal][hdinsight-admin-portal]
- [Verwalten von HDInsight-Clustern mit Azure PowerShell][hdinsight-admin-powershell]
- [Verwalten von HDInsight-Clustern mit der Befehlszeilenschnittstelle][hdinsight-admin-cli]
- [HDInsight-Dokumentation][hdinsight-documentation]
- [Erste Schritte mit HDInsight][hdinsight-get-started]



[ambari-home]: http://ambari.apache.org/
[ambari-api-reference]: https://github.com/apache/ambari/blob/trunk/ambari-server/docs/api/v1/index.md

[curl]: http://curl.haxx.se
[curl-download]: http://curl.haxx.se/download.html

[microsoft-hadoop-SDK]: http://hadoopsdk.codeplex.com/wikipage?title=Ambari%20Monitoring%20Client

[Powershell-install]: ../install-configure-powershell/
[Powershell-script]: http://technet.microsoft.com/de-de/library/ee176949.aspx 

[hdinsight-admin-powershell]: ../hdinsight-administer-use-powershell/
[hdinsight-admin-portal]: ../hdinsight-administer-use-management-portal/
[hdinsight-admin-cli]: ../hdinsight-administer-use-command-line/
[hdinsight-documentation]: /de-de/documentation/services/hdinsight/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-provision]: ../hdinsight-provision-clusters/

[img-jobtracker-output]: ./media/hdinsight-monitor-use-ambari-api/hdi.ambari.monitor.jobtracker.output.png


<!--HONumber=35.1-->
