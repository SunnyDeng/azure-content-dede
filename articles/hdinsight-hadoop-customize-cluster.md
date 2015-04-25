<properties 
	pageTitle="Anpassen von HDInsight-Clustern mithilfe von Skriptaktion | Azure" 
	description="Erfahren Sie, wie Sie mit Skriptaktionen HDInsight-Cluster anpassen können." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"/> 

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/15/2015" 
	ms.author="nitinme"/> 

# Anpassen von HDInsight-Clustern mithilfe von Skriptaktion

Sie können einen Azure HDInsight-Cluster dergestalt anpassen, dass zusätzliche Software für einen Cluster installiert oder die Konfiguration von Anwendungen im Cluster geändert wird. HDInsight bietet die Konfigurationsoption **Skriptaktion**, die benutzerdefinierte Skripts aufruft, mit denen die auf den Cluster anzuwendende Anpassung bestimmt wird. Diese Skripts können zur Anpassung eines Clusters *bei der Bereitstellung* verwendet werden.  

HDInsight-Cluster können auch auf verschiedene andere Weisen angepasst werden, z. B. durch Hinzufügen von Speicherkonten, Ändern der Hadoop-Konfigurationsdateien (core-site.xml, hive-site.xml usw.) oder Hinzufügen gemeinsam genutzter Bibliotheken (z. B. Hive, Oozie) zu allgemeinen Speicherorten im Cluster. Diese Anpassungen können mithilfe von HDInsight PowerShell, des .NET SDK oder Azure-Verwaltungsportals erfolgen. Weitere Informationen finden Sie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight][hdinsight-provision-cluster].



> [AZURE.NOTE] Die Verwendung von "Skriptaktion" zum Anpassen eines Clusters wird nur für HDInsight-Cluster ab Version 3.1 unterstützt. Weitere Informationen zu HDInsight-Clusterversionen finden Sie unter [HDInsight-Clusterversionen](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/).



## Themen in diesem Artikel

- [Wie wird das Skript während der Clustererstellung verwendet?](#lifecycle)
- [Wie wird ein Skript für die Clusteranpassung geschrieben?](#writescript)
- [Wie wird ein Cluster mit Skriptaktion angepasst?](#howto)
- [Beispiele für die Clusteranpassung](#example)
- [Unterstützung für Open-Source-Software in HDInsight-Clustern](#support)


## <a name="lifecycle"></a>Wie wird das Skript während der Clustererstellung verwendet?

Mithilfe von "Skriptaktion" kann ein HDInsight-Cluster nur angepasst werden, solange er sich im Erstellungsprozess befindet. Bei seiner Erstellung durchläuft ein HDInsight-Cluster die folgenden Phasen:

![HDInsight cluster customization and stages during cluster provisioning][img-hdi-cluster-states] 

Das Skript wird während der Clustererstellung nach der Phase *HDInsightConfiguration* und vor der Phase *ClusterOperational* aufgerufen. Jeder Cluster unterstützt mehrere Skriptaktionen, die in der angegebenen Reihenfolge aufgerufen werden.

> [AZURE.NOTE] Die Möglichkeit der Anpassung von HDInsight-Clustern steht im Rahmen der Azure HDInsight-Standardabonnements ohne zusätzliche Gebühren zur Verfügung.

### Wie funktioniert das Skript?

Sie können das Skript entweder auf dem Hauptknoten, dem Workerknoten oder beiden ausführen. Wenn das Skript ausgeführt wird, wechselt der Cluster in die Phase *ClusterCustomization*. In dieser Phase wird das Skript unter dem Systemadministratorkonto parallel auf allen angegebenen Knoten im Cluster ausgeführt und verfügt auf den Knoten über die Vollzugriffsrechte für Administratoren. 

> [AZURE.NOTE] Da Sie in der Phase *Cluster customization* Administratorrechte für den Clusterknoten haben, können Sie mit dem Skript Vorgänge wie das Beenden und Starten von Diensten, einschließlich Hadoop-bezogener Dienste, ausführen. Deshalb müssen Sie im Rahmen des Skripts sicherstellen, dass die Ambari-Dienste und andere Hadoop-bezogene Dienste in Betrieb sind, ehe die Ausführung des Skripts beendet wird. Diese Dienste werden benötigt, um die Integrität und den Status des Clusters erfolgreich sicherzustellen, während dieser erstellt wird. Wenn Sie eine Änderung der Clusterkonfiguration vornehmen, die sich auf diese Dienste auswirkt, müssen Sie die bereitgestellten Hilfsfunktionen verwenden. Weitere Informationen zu Hilfsfunktionen finden Sie unter [Entwickeln von Skriptaktionen mit HDInsight][hdinsight-write-script].

Die Ausgabe sowie die Fehlerprotokolle für das Skript werden im standardmäßigen Speicherkonto gespeichert, das Sie für den Cluster angegeben haben. Die Protokolle werden in einer Tabelle mit dem Namen *u<\cluster-name-fragment><\time-stamp>setuplog* gespeichert. Dabei handelt es sich um zusammengeführte Protokolle des Skripts, das auf allen Knoten (Haupt- und Arbeitsknoten) im Cluster ausgeführt wurde.

## <a name="writescript"></a>Wie wird ein Skript für die Clusteranpassung geschrieben?

Weitere Informationen zum Schreiben eines Skripts für die Clusteranpassung finden Sie unter [Entwickeln von Skriptaktionen mit HDInsight][hdinsight-write-script]. 

## <a name="howto"></a>Wie wird ein Cluster mit Skriptaktion angepasst?

Sie können Skriptaktion aus dem Azure-Verwaltungsportal, aus PowerShell-Cmdlets oder dem HDInsight .NET SDK verwenden, um einen Cluster anzupassen. 

**Aus dem Verwaltungsportal**

1. Starten Sie die Bereitstellung eines Clusters mit der Option **BENUTZERDEFINIERT ERSTELLEN**, wie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#portal) beschrieben. 
2. Klicken Sie auf der Seite "Skriptaktionen" des Assistenten auf **Skriptaktion hinzufügen**, um wie unten gezeigt Details zur Skriptaktion bereitzustellen:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Eigenschaft</th><th>Wert</th></tr>
		<tr><td>Name</td>
			<td>Geben Sie einen Namen für die Skriptaktion an.</td></tr>
		<tr><td>Skript-URI</td>
			<td>Geben Sie den URI für das Skript an, das aufgerufen wird, um den Cluster anzupassen.</td></tr>
		<tr><td>Knotentyp</td>
			<td>Gibt die Knoten an, auf denen das Anpassungsskript ausgeführt wird. Sie können <b>Alle Knoten</b>, <b>Nur Hauptknoten</b> oder <b>Nur Workerknoten</b> auswählen.
		<tr><td>Parameter</td>
			<td>Geben Sie die Parameter an, wenn dies für das Skript erforderlich ist.</td></tr>
	</table>

	Sie können dem Cluster mehr als eine Skriptaktion zum Installieren von mehreren Komponenten hinzufügen. Nachdem Sie die Skripts hinzugefügt haben, klicken Sie auf das Häkchen, um die Bereitstellung des Clusters zu starten. 
  
**Verwenden der PowerShell-Cmdlets**

Verwenden Sie HDInsight PowerShell-Befehle zum Ausführen einer oder mehrerer Skriptaktionen. Sie können das Cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** verwenden, um benutzerdefinierte Skripts aufzurufen. Zum Ausführen dieser Cmdlets muss Azure PowerShell installiert und konfiguriert sein. Weitere Informationen zum Konfigurieren einer Arbeitsstation für die Ausführung von HDInsight Powershell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].

Verwenden Sie den folgenden PowerShell-Befehl zum Ausführen einer einzelnen Skriptaktion, wenn ein HDInsight-Cluster bereitgestellt wird:

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName -Uri http://uri.to/scriptaction.ps1 -Parameters MyScriptActionParameter -ClusterRoleCollection HeadNode,DataNode

	New-AzureHDInsightCluster -Config $config

Verwenden Sie den folgenden PowerShell-Befehl zum Ausführen mehrerer Skriptaktionen, wenn ein HDInsight-Cluster bereitgestellt wird:

	$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName1 -Uri http://uri.to/scriptaction1.ps1 -Parameters MyScriptAction1Parameters -ClusterRoleCollection HeadNode,DataNode | Add-AzureHDInsightScriptAction -Config $config -Name MyScriptActionName2 -Uri http://uri.to/scriptaction2.ps1 -Parameters MyScriptAction2Parameters -ClusterRoleCollection HeadNode

	New-AzureHDInsightCluster -Config $config

**Verwenden des HDInsight .NET SDK**

HDInsight .NET SDK bietet eine <a href="http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx" target="_blank">ScriptAction</a> -Klasse, um benutzerdefinierte Skripts aufzurufen. So verwenden Sie das HDInsight .NET SDK:

1. Erstellen Sie eine Visual Studio-Anwendung, und installieren Sie dann das SDK von NuGet. Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**. Führen Sie die folgenden Befehle in der Konsole aus, um das Paket zu installieren.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

2. Erstellen Sie mit dem SDK einen Cluster. Anweisungen finden Sie unter [Bereitstellen eines HDInsight-Clusters mit .NET SDK](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#sdk).

3. Verwenden Sie die **ScriptAction**-Klasse, um wie unten gezeigt ein benutzerdefiniertes Skript aufzurufen:

		
		var clusterInfo = new ClusterCreateParameters()
		{
			// PROVIDE THE CLUSTER INFORMATION LIKE
			// NAME, STORAGE ACCOUNT, CREDENTIALS,
			// CLUSTER SIZE, and VERSION		    
			...
			...
		};

		// ADD THE SCRIPT ACTION TO INSTALL SPARK
		clusterInfo.ConfigActions.Add(new ScriptAction(
	  		"MyScriptActionName", // Name of the config action
	  		new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install component on
	  		new Uri("http://uri.to/scriptaction.ps1"), // Location of the script to install the component
	  		"MyScriptActionParameter" //Parameters, if any, required by the script.
		));


## <a name="example"></a>Beispiele für die Clusteranpassung

Zum Einstieg bietet Ihnen HDInsight Beispielskripts für die Installation der folgenden Komponenten in einem HDInsight-Cluster.

- **Installieren Sie Spark**. Siehe [Installieren von Spark in HDInsight-Clustern][hdinsight-install-spark].
- **Installieren Sie R**. Siehe [Installieren von R in HDInsight-Clustern][hdinsight-install-r].
- **Installieren Sie Solr**. Siehe [Installieren und Verwenden von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install.md).
- **Installieren Sie Giraph**. Siehe [Installieren und Verwenden von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install.md).

## <a name="support"></a>Unterstützung für Open-Source-Software in HDInsight-Clustern
Der Microsoft Azure HDInsight-Dienst ist eine flexible Plattform, die es Ihnen ermöglicht, Big-Data-Anwendungen in der Cloud innerhalb des Ökosystems der Open-Source-Technologien rund um Hadoop zu erstellen. Microsoft Azure bietet allgemeinen Support für Open-Source-Technologien wie im <a href="http://azure.microsoft.com/support/faq/" target="_blank">Abschnitt "Supportumfang" auf der FAQ-Website zum Azure-Support</a>beschrieben. Der HDInsight-Dienst bietet darüber hinaus, wie unten beschrieben, zusätzlichen Support für einige der Komponenten.

Es gibt zwei Arten von Open-Source-Komponenten, die im HDInsight-Dienst verfügbar sind:

- **Integrierte Komponenten**. Diese Komponenten sind auf HDInsight-Clustern vorinstalliert und stellen Kernfunktionen des Clusters bereit. So gehören beispielsweise Yarn Resource Manager, Hive Query Language und Mahout Library zu dieser Kategorie. Eine vollständige Liste der Clusterkomponenten finden Sie <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">hier</a>.
- **Benutzerdefinierte Komponenten**. Als Benutzer des Clusters können Sie während der Arbeitsauslastung eine beliebige in der Community verfügbare oder von Ihnen erstellte Komponente installieren oder verwenden.

Integrierte Komponenten werden vollständig unterstützt, und Microsoft Support hilft Ihnen, Probleme im Zusammenhang mit diesen Komponenten zu isolieren und zu beheben.

Für benutzerdefinierte Komponenten steht kommerziell angemessener Support für eine weiterführende Behebung des Problems zur Verfügung. Auf diese Weise kann das Problem behoben werden, ODER Sie werden aufgefordert, verfügbare Kanäle für Open-Source-Technologien in Anspruch zu nehmen, die über umfassende Kenntnisse für diese Technologien verfügen. So können z. B. viele Community-Websites verwendet werden, wie: <a href ="https://social.msdn.microsoft.com/Forums/azure/en-US/home?forum=hdinsight" target="_blank">MSDN-Forum für HDInsight</a>, <a href="http://stackoverflow.com" target="_blank">http://stackoverflow.com</a>. Auch Apache-Projekte verfügen über Projektwebsites unter <a href="http://apache.org" target="_blank">http://apache.org</a>, z. B.: <a href="http://hadoop.apache.org/" target="_blank">Hadoop</a>, <a href="http://spark.apache.org/" target="_blank">Spark</a>.

Der HDInsight-Dienst bietet mehrere Möglichkeiten, um benutzerdefinierte Komponenten zu verwenden. Unabhängig davon, wie die Komponente verwendet wird oder im Cluster installiert ist, gilt der gleiche Supportumfang. Nachfolgend finden Sie eine Liste der am häufigsten genutzten Möglichkeiten für die Verwendung von benutzerdefinierten Komponenten auf HDInsight-Clustern.

1. Senden des Auftrags: Hadoop oder andere Auftragstypen können an den Cluster gesendet werden, der benutzerdefinierte Komponenten ausführt oder verwendet.
2. Anpassen des Clusters: Während der Clustererstellung können Sie zusätzliche Einstellungen und benutzerdefinierte Komponenten angeben, die auf den Clusterknoten installiert werden.
3. Beispiele: Für beliebte benutzerdefinierte Komponenten stellen Microsoft und andere Anbieter u. U. Beispiele dafür bereit, wie diese Komponenten auf den HDInsight-Clustern verwendet werden können. Für diese Beispiele wird kein Support bereitgestellt.


## Weitere Informationen##
[Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight][hdinsight-provision-cluster] enthält Anweisungen zum Bereitstellen eines HDInsight-Clusters mit anderen benutzerdefinierten Optionen.

[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-write-script]: ../hdinsight-hadoop-script-actions/
[hdinsight-provision-cluster]: ../hdinsight-provision-clusters/
[powershell-install-configure]: ../install-configure-powershell/


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Stages during cluster provisioning"

<!--HONumber=42-->
