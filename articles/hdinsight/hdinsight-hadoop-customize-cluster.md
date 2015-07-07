<properties 
	pageTitle="Anpassen von HDInsight-Clustern mit Skriptaktionen | Microsoft Azure" 
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
	ms.date="03/03/2015" 
	ms.author="nitinme"/>

# Anpassen von HDInsight-Clustern mithilfe von "Skriptaktion"

Sie können einen Azure HDInsight-Cluster dergestalt anpassen, dass zusätzliche Software für einen Cluster installiert oder die Konfiguration von Anwendungen im Cluster geändert wird. HDInsight bietet die Konfigurationsoption **Skriptaktion**, die benutzerdefinierte Skripts aufruft, mit denen die auf den Cluster anzuwendende Anpassung bestimmt wird. Mit diesen Skripts kann ein Cluster angepasst werden, *während er bereitgestellt wird*.

HDInsight-Cluster können auch auf verschiedene andere Weisen angepasst werden, z. B. durch Hinzufügen von Azure-Speicherkonten, Ändern der Hadoop-Konfigurationsdateien (core-site.xml, hive-site.xml usw.) oder Hinzufügen gemeinsam genutzter Bibliotheken (z. B. Hive, Oozie) zu allgemeinen Speicherorten im Cluster. Diese Anpassungen können über Azure PowerShell, das Azure HDInsight .NET SDK oder Azure-Portal erfolgen. Weitere Informationen finden Sie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight][hdinsight-provision-cluster].



> [AZURE.NOTE]Die Verwendung von "Skriptaktion" zum Anpassen eines Clusters wird nur für HDInsight-Cluster ab Version 3.1 unterstützt. Weitere Informationen zu HDInsight-Clusterversionen finden Sie unter [HDInsight-Clusterversionen](hdinsight-component-versioning.md).


## <a name="lifecycle"></a>Wie wird das Skript bei der Clustererstellung verwendet?

Mithilfe von "Skriptaktion" kann ein HDInsight-Cluster nur angepasst werden, solange er sich im Erstellungsprozess befindet. Bei seiner Erstellung durchläuft ein HDInsight-Cluster die folgenden Phasen:

![HDInsight-Clusteranpassung und Phasen während der Clusterbereitstellung][img-hdi-cluster-states]

Das Skript wird während der Clustererstellung nach der Phase **HDInsightConfiguration** und vor der Phase **ClusterOperational** aufgerufen. Jeder Cluster unterstützt mehrere Skriptaktionen, die in der angegebenen Reihenfolge aufgerufen werden.

> [AZURE.NOTE]Die Möglichkeit der Anpassung von HDInsight-Clustern steht im Rahmen der Azure HDInsight-Standardabonnements ohne zusätzliche Gebühren zur Verfügung.

### Funktionsweise des Skripts

Sie können das Skript entweder auf dem Hauptknoten, den Workerknoten oder beiden ausführen. Wenn das Skript ausgeführt wird, wechselt der Cluster in die Phase **ClusterCustomization**. In dieser Phase wird das Skript unter dem Systemadministratorkonto parallel auf allen angegebenen Knoten im Cluster ausgeführt und verfügt auf den Knoten über die Vollzugriffsrechte für Administratoren.

> [AZURE.NOTE]Da Sie in der Phase **ClusterCustomization** Administratorrechte für den Clusterknoten haben, können Sie mit dem Skript Vorgänge wie das Beenden und Starten von Diensten, einschließlich Hadoop-bezogener Dienste, ausführen. Deshalb müssen Sie im Rahmen des Skripts sicherstellen, dass die Ambari-Dienste und andere Hadoop-bezogene Dienste in Betrieb sind, ehe die Ausführung des Skripts beendet wird. Diese Dienste werden benötigt, um die Integrität und den Status des Clusters erfolgreich sicherzustellen, während dieser erstellt wird. Wenn Sie eine Änderung der Clusterkonfiguration vornehmen, die sich auf diese Dienste auswirkt, müssen Sie die bereitgestellten Hilfsfunktionen verwenden. Weitere Informationen zu Hilfsfunktionen finden Sie unter [Entwickeln von Skriptaktionen mit HDInsight][hdinsight-write-script].

Die Ausgabe sowie die Fehlerprotokolle für das Skript werden im Standardspeicherkonto gespeichert, das Sie für den Cluster angegeben haben. Die Protokolle befinden sich in einer Tabelle mit dem Namen **u<\\cluster-name-fragment><\\time-stamp>setuplog**. Dabei handelt es sich um zusammengeführte Protokolle des Skripts, das auf allen Knoten (Haupt- und Workerknoten) im Cluster ausgeführt wurde.

## <a name="writescript"></a>Wie wird ein Skript für die Clusteranpassung geschrieben?

Weitere Informationen zum Schreiben eines Skripts für die Clusteranpassung finden Sie unter [Entwickeln von Skriptaktionen mit HDInsight][hdinsight-write-script].

## <a name="howto"></a>Anpassen eines Clusters mit "Skriptaktion"

Sie können Skriptaktion über das Azure-Verwaltungsportal, PowerShell-Cmdlets oder das HDInsight .NET SDK verwenden, um einen Cluster anzupassen.

**Verwenden des Azure-Portals**

1. Beginnen Sie die Bereitstellung eines Clusters mit der Option **BENUTZERDEFINIERT ERSTELLEN**, wie unter [Bereitstellen eines Clusters mit der benutzerdefinierten Erstellungsoption](hdinsight-provision-clusters.md#portal) beschrieben. 
2. Klicken Sie auf der Seite **Skriptaktionen** des Assistenten auf **Skriptaktion hinzufügen**, um wie unten gezeigt Details zur Skriptaktion bereitzustellen:

	![Anpassen eines Clusters mit "Skriptaktion"](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Anpassen eines Clusters mit "Skriptaktion"")
	
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
</table>Sie können dem Cluster mehr als eine Skriptaktion zum Installieren von mehreren Komponenten hinzufügen. Nachdem Sie die Skripts hinzugefügt haben, klicken Sie auf das Häkchen, um die Bereitstellung des Clusters zu starten.
  
**Verwenden von Azure PowerShell-Cmdlets**

Verwenden Sie Azure PowerShell-Befehle für HDInsight zum Ausführen einer oder mehrerer Skriptaktionen. Mit dem Cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** können Sie benutzerdefinierte Skripts aufrufen. Zum Ausführen dieser Cmdlets muss Azure PowerShell installiert und konfiguriert sein. Weitere Informationen zum Konfigurieren einer Arbeitsstation für die Ausführung von Azure PowerShell-Cmdlets für HDInsight finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].

Verwenden Sie den folgenden Azure PowerShell-Befehl zum Ausführen einer einzelnen Skriptaktion, wenn ein HDInsight-Cluster bereitgestellt wird:

	$config = New-AzureHDInsightClusterConfig –ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName –Uri http://uri.to/scriptaction.ps1 –Parameters MyScriptActionParameter -ClusterRoleCollection HeadNode,DataNode

	New-AzureHDInsightCluster -Config $config

Verwenden Sie den folgenden Azure PowerShell-Befehl zum Ausführen mehrerer Skriptaktionen, wenn ein HDInsight-Cluster bereitgestellt wird:

	$config = New-AzureHDInsightClusterConfig –ClusterSizeInNodes 4

	$config = Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName1 –Uri http://uri.to/scriptaction1.ps1 –Parameters MyScriptAction1Parameters -ClusterRoleCollection HeadNode,DataNode | Add-AzureHDInsightScriptAction -Config $config –Name MyScriptActionName2 –Uri http://uri.to/scriptaction2.ps1 -Parameters MyScriptAction2Parameters -ClusterRoleCollection HeadNode

	New-AzureHDInsightCluster -Config $config

**Verwenden des HDInsight .NET SDK**

Das HDInsight .NET SDK bietet eine <a href="http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx" target="_blank">ScriptAction</a>-Klasse, um benutzerdefinierte Skripts aufzurufen. So verwenden Sie das HDInsight .NET SDK:

1. Erstellen Sie eine Visual Studio-Anwendung, und installieren Sie dann das SDK von NuGet. Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**. Führen Sie den folgenden Befehl in der Konsole aus, um das Paket zu installieren:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

2. Erstellen Sie einen Cluster mit dem SDK. Anweisungen finden Sie unter [Bereitstellen eines HDInsight-Clusters mit .NET SDK](hdinsight-provision-clusters.md#sdk).

3. Verwenden Sie die **ScriptAction**-Klasse, um wie unten gezeigt ein benutzerdefiniertes Skript aufzurufen:

		
		var clusterInfo = new ClusterCreateParameters()
		{
			// Provide the cluster information, like
			// name, Storage account, credentials,
			// cluster size, and version		    
			...
			...
		};

		// Add the script action to install Spark
		clusterInfo.ConfigActions.Add(new ScriptAction(
	  		"MyScriptActionName", // Name of the config action
	  		new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install the component on
	  		new Uri("http://uri.to/scriptaction.ps1"), // Location of the script to install the component
	  		"MyScriptActionParameter" //Parameters, if any, required by the script
		));


## <a name="example"></a>Beispiele für die Clusteranpassung

Zum Einstieg bietet Ihnen HDInsight Beispielskripts für die Installation der folgenden Komponenten in einem HDInsight-Cluster:

- **Installieren von Spark** – Siehe [Installieren und Verwenden von Spark in HDInsight-Clustern][hdinsight-install-spark].
- **Installieren von R** – Siehe [Installieren und Verwenden von R in HDInsight-Clustern][hdinsight-install-r].
- **Installieren von Solr** – Siehe [Installieren und Verwenden von Solr in HDInsight-Clustern.](hdinsight-hadoop-solr-install.md).
- **Installieren von Giraph** – Siehe [Installieren und Verwenden von Giraph in HDInsight-Clustern.](hdinsight-hadoop-giraph-install.md).

## <a name="support"></a>Unterstützung für Open-Source-Software in HDInsight-Clustern
Der Microsoft Azure HDInsight-Dienst ist eine flexible Plattform, die es Ihnen ermöglicht, Big Data-Anwendungen in der Cloud innerhalb des Ökosystems der Open-Source-Technologien rund um Hadoop zu erstellen. Microsoft Azure bietet allgemeinen Support für Open-Source-Technologien. Siehe den Abschnitt **Supportumfang** auf der Website <a href="http://azure.microsoft.com/support/faq/" target="_blank">Azure-Support-FAQ</a>. Der HDInsight-Dienst bietet, wie nachstehend beschrieben, zusätzliche Unterstützung für einige der Komponenten.

Es gibt zwei Arten von Open-Source-Komponenten, die im HDInsight-Dienst verfügbar sind:

- **Integrierte Komponenten** – Diese Komponenten sind in HDInsight-Clustern vorinstalliert und stellen Kernfunktionen des Clusters bereit. So gehören beispielsweise Yarn Resource Manager, die Hive-Abfragesprache (HiveQL) und die Mahout Library zu dieser Kategorie. Eine vollständige Liste der Clusterkomponenten finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">Neuerungen in den von HDInsight bereitgestellten Hadoop-Clusterversionen</a>.
- **Benutzerdefinierte Komponenten** – Als Benutzer des Clusters können Sie in Ihrem Workload eine beliebige in der Community verfügbare oder von Ihnen erstellte Komponente installieren oder verwenden.

Integrierte Komponenten werden vollständig unterstützt, und Microsoft Support hilft Ihnen, Probleme im Zusammenhang mit diesen Komponenten zu isolieren und zu beheben.

Für benutzerdefinierte Komponenten steht kommerziell angemessener Support für eine weiterführende Behebung des Problems zur Verfügung. Auf diese Weise kann das Problem behoben werden, oder Sie werden aufgefordert, verfügbare Kanäle für Open-Source-Technologien in Anspruch zu nehmen, die über umfassende Kenntnisse dieser Technologien verfügen. So können z. B. viele Communitywebsites verwendet werden, wie: das <a href ="https://social.msdn.microsoft.com/Forums/azure/home?forum=hdinsight" target="_blank">MSDN-Forum für HDInsight</a> und <a href="http://stackoverflow.com" target="_blank">Stack Overflow</a>. Für Apache-Projekte gibt es Projektwebsites auf <a href="http://apache.org" target="_blank">Apache.org</a>, z. B. <a href="http://hadoop.apache.org/" target="_blank">Hadoop</a> und <a href="http://spark.apache.org/" target="_blank">Spark</a>.

Der HDInsight-Dienst bietet mehrere Möglichkeiten, benutzerdefinierte Komponenten zu verwenden. Unabhängig davon, wie die Komponente verwendet wird oder im Cluster installiert ist, gilt der gleiche Supportumfang. Nachfolgend finden Sie eine Liste der am häufigsten genutzten Möglichkeiten für die Verwendung von benutzerdefinierten Komponenten in HDInsight-Clustern:

1. Senden des Auftrags – Hadoop- oder andere Auftragstypen, die benutzerdefinierte Komponenten ausführen oder verwenden, können zum Cluster gesendet werden.
2. Clusteranpassung – Während der Clustererstellung können Sie zusätzliche Einstellungen und benutzerdefinierte Komponenten angeben, die auf den Clusterknoten installiert werden.
3. Beispiele – Für beliebte benutzerdefinierte Komponenten stellen Microsoft und andere Anbieter u. U. Beispiele dafür bereit, wie diese Komponenten in den HDInsight-Clustern verwendet werden können. Für diese Beispiele wird kein Support bereitgestellt.


## Weitere Informationen##
Unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight][hdinsight-provision-cluster] finden Sie Anweisungen zum Bereitstellen eines HDInsight-Clusters mit anderen benutzerdefinierten Optionen.

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: ../install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Phasen während der Clusterbereitstellung"
 

<!---HONumber=62-->