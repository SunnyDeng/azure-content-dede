<properties 
	pageTitle="Anpassen von HDInsight-Clustern mit Skriptaktionen | Microsoft Azure" 
	description="Erfahren Sie, wie Sie mit Skriptaktionen HDInsight-Cluster anpassen können." 
	services="hdinsight" 
	documentationCenter="" 
	authors="nitinme" 
	manager="paulettm" 
	editor="cgronlun"
	tags="azure-portal"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/07/2015" 
	ms.author="nitinme"/>

# Anpassen von HDInsight-Clustern mithilfe von Skriptaktion

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-v1.md)

HDInsight bietet die Konfigurationsoption **Skriptaktion**, die benutzerdefinierte Skripts aufruft, mit denen die auf den Cluster anzuwendende Anpassung während des Bereitstellungsprozesses bestimmt wird. Sie können diese Skripts verwenden, um zusätzliche Software auf einem Cluster zu installieren oder die Konfiguration von Anwendungen auf einem Cluster zu ändern.


> [AZURE.NOTE]Script Action wird nur unter HDInsight-Clusterversion 3.1 oder höher mit Windows-Betriebssystem unterstützt. Weitere Informationen zu HDInsight-Clusterversionen finden Sie unter [HDInsight-Clusterversionen](hdinsight-component-versioning.md).
> 
> Script Action ist im Rahmen der standardmäßigen Azure HDInsight-Abonnements ohne zusätzliche Kosten verfügbar.

HDInsight-Cluster können auch auf verschiedene andere Weisen angepasst werden, z. B. durch Hinzufügen von Azure-Speicherkonten, Ändern der Hadoop-Konfigurationsdateien (core-site.xml, hive-site.xml usw.) oder Hinzufügen gemeinsam genutzter Bibliotheken (z. B. Hive, Oozie) zu allgemeinen Speicherorten im Cluster. Diese Anpassungen können über Azure PowerShell, das Azure HDInsight .NET SDK oder das Azure-Vorschauportal erfolgen. Weitere Informationen finden Sie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight][hdinsight-provision-cluster].

## Script Action im Clusterbereitstellungsprozess

Script Action wird nur verwendet, während ein Cluster erstellt wird. Das folgende Diagramm veranschaulicht, wann Script Action während des Bereitstellungsprozesses ausgeführt wird:

![HDInsight-Clusteranpassung und Phasen während der Clusterbereitstellung][img-hdi-cluster-states]

Wenn das Skript ausgeführt wird, wechselt der Cluster in die Phase **ClusterCustomization**. In dieser Phase wird das Skript unter dem Systemadministratorkonto parallel auf allen angegebenen Knoten im Cluster ausgeführt und verfügt auf den Knoten über die Vollzugriffsrechte für Administratoren.

> [AZURE.NOTE]Da Sie in der Phase **ClusterCustomization** Administratorrechte für den Clusterknoten haben, können Sie mit dem Skript Vorgänge wie das Beenden und Starten von Diensten, einschließlich Hadoop-bezogener Dienste, ausführen. Deshalb müssen Sie im Rahmen des Skripts sicherstellen, dass die Ambari-Dienste und andere Hadoop-bezogene Dienste in Betrieb sind, ehe die Ausführung des Skripts beendet wird. Diese Dienste werden benötigt, um die Integrität und den Status des Clusters erfolgreich sicherzustellen, während dieser erstellt wird. Wenn Sie eine Änderung der Clusterkonfiguration vornehmen, die sich auf diese Dienste auswirkt, müssen Sie die bereitgestellten Hilfsfunktionen verwenden. Weitere Informationen zu Hilfsfunktionen finden Sie unter [Entwickeln von Skriptaktionsskripts für HDInsight][hdinsight-write-script].

Die Ausgabe sowie die Fehlerprotokolle für das Skript werden im Standardspeicherkonto gespeichert, das Sie für den Cluster angegeben haben. Die Protokolle befinden sich in einer Tabelle mit dem Namen **u<\\cluster-name-fragment><\\time-stamp>setuplog**. Dabei handelt es sich um zusammengeführte Protokolle des Skripts, das auf allen Knoten (Haupt- und Workerknoten) im Cluster ausgeführt wurde.


Jeder Cluster unterstützt mehrere Skriptaktionen, die in der angegebenen Reihenfolge aufgerufen werden. Ein Skript kann auf dem Hauptknoten, Workerknoten oder beiden Knoten ausgeführt werden.

## Aufrufen von Script Action-Skripts

Skriptaktionsskripts können über das Azure-Vorschauportal, Azure PowerShell oder das HDInsight .NET SDK genutzt werden. Dieser Artikel beschreibt, wie die Skriptaktion im Portal verwendet wird. Sehen Sie sich die Beispiele in der folgenden Tabelle an, um zu erfahren, wie Sie PowerShell und das .NET SDK für die Verwendung der Skriptaktion nutzen.

HDInsight verfügt über mehrere Skripts zum Installieren der folgenden Komponenten auf HDInsight-Clustern:

Name | Skript
----- | -----
**Installieren von Spark** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Siehe [Installieren und Verwenden von Spark in HDInsight-Clustern][hdinsight-install-spark].
**Installieren von R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Siehe [Installieren und Verwenden von R in HDInsight-Clustern][hdinsight-install-r].
**Installieren von Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Siehe [Installieren und Verwenden von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install.md).
\- **Installieren von Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Siehe [Installieren und Verwenden von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install.md).



**Im Azure-Vorschauportal**

1. Starten Sie die Bereitstellung eines Clusters wie unter [Benutzerdefinierte Bereitstellung eines Clusters](hdinsight-provision-clusters.md#portal) beschrieben. 
2. Klicken Sie unter „Optionale Konfiguration“ auf dem Blatt **Skriptaktionen** auf **Skriptaktion hinzufügen**, um Details zur Skriptaktion wie folgt anzugeben:

	![Anpassen eines Clusters mit "Skriptaktion"](./media/hdinsight-hadoop-customize-cluster/HDI.CreateCluster.8.png "Anpassen eines Clusters mit "Skriptaktion"")
	
	<table border='1'>
	<tr><th>Eigenschaft</th><th>Wert</th></tr>
	<tr><td>Name</td>
		<td>Geben Sie einen Namen für die Skriptaktion an.</td></tr>
	<tr><td>Skript-URI</td>
		<td>Geben Sie den URI für das Skript an, das aufgerufen wird, um den Cluster anzupassen.</td></tr>
	<tr><td>Haupt-/Workerknoten</td>
		<td>Gibt die Knoten (**Hauptknoten** oder **Workerknoten**) an, für die das Anpassungsskript ausgeführt wird.</b>
	<tr><td>Parameter</td>
		<td>Geben Sie die Parameter an, wenn dies für das Skript erforderlich ist.</td></tr>
</table>Drücken Sie die EINGABETASTE, um dem Cluster mehr als eine Skriptaktion zum Installieren von mehreren Komponenten hinzuzufügen.

3. Klicken Sie auf **Auswählen**, um die Skriptaktionskonfiguration zu speichern und die Clusterbereitstellung fortzusetzen.
  

## Unterstützung für Open-Source-Software in HDInsight-Clustern
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

## Entwickeln von Script Action-Skripts

Siehe [Entwickeln von Skriptaktionsskripts für HDInsight][hdinsight-write-script].


## Weitere Informationen

- Unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight][hdinsight-provision-cluster] finden Sie Anweisungen zum Bereitstellen eines HDInsight-Clusters mit anderen benutzerdefinierten Optionen.
- [Entwickeln von Script Action-Skripts für HDInsight][hdinsight-write-script]
- [Installieren und Verwenden von Spark in HDInsight-Clustern][hdinsight-install-spark]
- [Installieren und Verwenden von R in HDInsight-Clustern][hdinsight-install-r]
- [Installieren und Verwenden von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install.md)
- [Installieren und Verwenden von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install.md)

[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-write-script]: hdinsight-hadoop-script-actions.md
[hdinsight-provision-cluster]: hdinsight-provision-clusters.md
[powershell-install-configure]: ../install-configure-powershell.md


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Phasen während der Clusterbereitstellung"
 

<!---HONumber=August15_HO8-->