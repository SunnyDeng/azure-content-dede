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
	ms.date="10/02/2015"
	ms.author="nitinme"/>

# Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen | Azure

HDInsight bietet die Konfigurationsoption **Skriptaktion**, die benutzerdefinierte Skripts aufruft, mit denen die auf den Cluster anzuwendende Anpassung während des Erstellungsvorgangs festgelegt wird. Sie können diese Skripts verwenden, um zusätzliche Software auf einem Cluster zu installieren oder die Konfiguration von Anwendungen auf einem Cluster zu ändern.

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-v1.md)

> [AZURE.NOTE]Die Informationen in diesem Artikel gelten für Windows-basierte HDInsight-Cluster. Eine Version dieses Artikels mit spezifischen Informationen zu Linux-basierten Clustern finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen (Linux)](hdinsight-hadoop-customize-cluster-linux.md).

HDInsight-Cluster können auch auf verschiedene andere Weisen angepasst werden, z. B. durch Hinzufügen von Azure-Speicherkonten, Ändern der Hadoop-Konfigurationsdateien (core-site.xml, hive-site.xml usw.) oder Hinzufügen gemeinsam genutzter Bibliotheken (z. B. Hive, Oozie) zu allgemeinen Speicherorten im Cluster. Diese Anpassungen können über Azure PowerShell, das Azure HDInsight .NET SDK oder das Azure-Vorschauportal erfolgen. Weitere Informationen finden Sie unter [Erstellen von Hadoop-Clustern in HDInsight][hdinsight-provision-cluster].

## Skriptaktionen im Clustererstellungsvorgang

Script Action wird nur verwendet, während ein Cluster erstellt wird. Das folgende Diagramm veranschaulicht, wann Skriptaktionen während des Erstellungsvorgangs ausgeführt werden:

![HDInsight-Clusteranpassung und Phasen während der Clustererstellung][img-hdi-cluster-states]

Wenn das Skript ausgeführt wird, wechselt der Cluster in die Phase **ClusterCustomization**. In dieser Phase wird das Skript unter dem Systemadministratorkonto parallel auf allen angegebenen Knoten im Cluster ausgeführt und verfügt auf den Knoten über die Vollzugriffsrechte für Administratoren.

> [AZURE.NOTE]Da Sie in der Phase **ClusterCustomization** Administratorrechte für den Clusterknoten haben, können Sie mit dem Skript Vorgänge wie das Beenden und Starten von Diensten, einschließlich Hadoop-bezogener Dienste, ausführen. Deshalb müssen Sie im Rahmen des Skripts sicherstellen, dass die Ambari-Dienste und andere Hadoop-bezogene Dienste in Betrieb sind, ehe die Ausführung des Skripts beendet wird. Diese Dienste werden benötigt, um die Integrität und den Status des Clusters erfolgreich sicherzustellen, während dieser erstellt wird. Wenn Sie eine Änderung der Clusterkonfiguration vornehmen, die sich auf diese Dienste auswirkt, müssen Sie die bereitgestellten Hilfsfunktionen verwenden. Weitere Informationen zu Hilfsfunktionen finden Sie unter [Entwickeln von Skriptaktionsskripts für HDInsight][hdinsight-write-script].

Die Ausgabe sowie die Fehlerprotokolle für das Skript werden im Standardspeicherkonto gespeichert, das Sie für den Cluster angegeben haben. Die Protokolle befinden sich in einer Tabelle mit dem Namen **u<\\cluster-name-fragment><\\time-stamp>setuplog**. Dabei handelt es sich um zusammengeführte Protokolle des Skripts, das auf allen Knoten (Haupt- und Workerknoten) im Cluster ausgeführt wurde.

Jeder Cluster unterstützt mehrere Skriptaktionen, die in der angegebenen Reihenfolge aufgerufen werden. Ein Skript kann auf dem Hauptknoten, Workerknoten oder beiden Knoten ausgeführt werden.

HDInsight verfügt über mehrere Skripts zum Installieren der folgenden Komponenten auf HDInsight-Clustern:

Name | Skript
----- | -----
**Installieren von Spark** | https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1. Siehe [Installieren und Verwenden von Spark in HDInsight-Clustern][hdinsight-install-spark].
**Installieren von R** | https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1. Siehe [Installieren und Verwenden von R in HDInsight-Clustern][hdinsight-install-r].
**Installieren von Solr** | https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1. Siehe [Installieren und Verwenden von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install.md).
- **Installieren von Giraph** | https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1. Siehe [Installieren und Verwenden von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install.md).



## Aufrufen von Skripts mit dem Azure-Vorschauportal

**Im Azure-Vorschauportal**

1. Beginnen Sie mit dem Erstellen eines Clusters, wie unter [Erstellen von Hadoop-Clustern in HDInsight](hdinsight-provision-clusters.md#portal) beschrieben.
2. Klicken Sie unter "Optionale Konfiguration" auf dem Blatt **Skriptaktionen** auf **Skriptaktion hinzufügen**, um Details zur Skriptaktion wie folgt anzugeben:

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

3. Klicken Sie auf **Auswählen**, um die Skriptaktionskonfiguration zu speichern und die Clustererstellung fortzusetzen.

## Aufrufen von Skripts mit Azure PowerShell

Das folgende PowerShell-Skript veranschaulicht das Installieren von Spark auf HDInsight-Clustern mit Windows. Um andere Software zu installieren, müssen Sie die Skriptdatei im Skript ersetzen:

In diesem Abschnitt verwenden wir das Cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** zum Aufrufen von Skripts mithilfe von "Skriptaktion" zum Anpassen eines Clusters. Stellen Sie vor dem Fortfahren sicher, dass Azure PowerShell installiert und konfiguriert ist. Weitere Informationen zum Konfigurieren einer Arbeitsstation für die Ausführung von Azure PowerShell-Cmdlets für HDInsight finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].

Führen Sie die folgenden Schritte aus:

1. Öffnen Sie ein Azure PowerShell-Fenster, und deklarieren Sie die folgenden Variablen:

		# Provide values for these variables
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure Storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the Storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the Storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# Number of nodes in the HDInsight cluster
		$version = "<HDInsightClusterVersion>"          # For example, "3.2"

2. Legen Sie die Konfigurationswerte fest, z. B. Knoten im Cluster und den zu verwendenden Standardspeicher.

		# Specify the configuration options
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName

3. Fügen Sie mithilfe des Cmdlets **Add-AzureHDInsightScriptAction** eine Skriptaktion zur Clusterkonfiguration hinzu. Die Skriptaktion wird später bei der Erstellung des Clusters ausgeführt.

		# Add a script action to the cluster configuration
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1

	Das Cmdlet **Add-AzureHDInsightScriptAction** verwendet die folgenden Parameter:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parameter</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definition</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Konfiguration</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Das Konfigurationsobjekt, dem Skriptaktionsinformationen hinzugefügt werden.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name der Skriptaktion.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Gibt die Knoten an, auf denen das Anpassungsskript ausgeführt wird. Die gültigen Werte lauten "HeadNode" (für die Installation auf dem Hauptknoten) oder "DataNode" (für die Installation auf allen Datenknoten). Sie können einen oder beide Werte verwenden.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Gibt den URI des auszuführenden Skripts an.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parameter</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Vom Skript benötigte Parameter. Das in diesem Thema verwendete Skript benötigt keine Parameter, weshalb dieser Parameter nicht im obigen Auszug enthalten ist.
</td></tr>
</table>

4. Beginnen Sie mit dem Erstellen eines angepassten Clusters mit installiertem Spark.

		# Start creating a cluster with Spark installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

Geben Sie die Anmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden. Die Erstellung des Clusters kann einige Minuten in Anspruch nehmen.

## Aufrufen von Skripts mit dem .NET SDK 

Das folgende Beispiel veranschaulicht das Installieren von Spark auf HDInsight-Clustern mit Windows. Um andere Software zu installieren, müssen Sie die Skriptdatei im Code ersetzen:

**So erstellen Sie einen HDInsight-Cluster mit Spark**

1. Erstellen Sie eine C#-Konsolenanwendung in Visual Studio.
2. Führen Sie in der NuGet-Paket-Manager-Konsole den folgenden Befehl aus.

		Install-Package Microsoft.Azure.Management.HDInsight -Pre
		Install-Package Microsoft.Azure.Common.Authentication -Pre

2. Verwenden Sie die folgenden using-Anweisungen in der Datei "Program.cs":

		using System;
		using System.Security;
		using Microsoft.Azure.Management.HDInsight;
		using Microsoft.Azure.Management.HDInsight.Models;
		
		using Microsoft.Azure;
		using Microsoft.Azure.Common.Authentication;
		using Microsoft.Azure.Common.Authentication.Factories;
		using Microsoft.Azure.Common.Authentication.Models;

3. Ersetzen Sie den Code in der Klasse durch den folgenden Code:

        private static HDInsightManagementClient _hdiManagementClient;

        private static Guid SubscriptionId = new Guid("<YourAzureSubscriptionID>");
        private const string ResourceGroupName = "<ExistingAzureResourceGroupName>";
        private const string NewClusterName = "<NewAzureHDInsightClusterName>";
        private const int NewClusterNumNodes = <NumberOfClusterNodes>;
        private const string NewClusterLocation = "East US";
        private const string NewClusterVersion = "3.2";
        private const string ExistingStorageName = "<ExistingAzureStorageAccountName>";
        private const string ExistingStorageKey = "<ExistingAzureStorageAccountKey>";
        private const string ExistingContainer = "<ExistingAzureBlobStorageContainer>";
        private const HDInsightClusterType NewClusterType = HDInsightClusterType.Hadoop;
        private const OSType NewClusterOSType = OSType.Windows;
        private const string NewClusterUsername = "<HttpUserName>";
        private const string NewClusterPassword = "<HttpUserPassword>";

        static void Main(string[] args)
        {
            System.Console.WriteLine("Running");

            var tokenCreds = GetTokenCloudCredentials();
            var subCloudCredentials = GetSubscriptionCloudCredentials(tokenCreds, SubscriptionId);

            _hdiManagementClient = new HDInsightManagementClient(subCloudCredentials);

            CreateCluster();

        }

        private static void CreateCluster()
        {
            var parameters = new ClusterCreateParameters
            {
                ClusterSizeInNodes = NewClusterNumNodes,
                Location = NewClusterLocation,
                ClusterType = NewClusterType,
                OSType = NewClusterOSType,
                Version = NewClusterVersion,

                DefaultStorageAccountName = ExistingStorageName,
                DefaultStorageAccountKey = ExistingStorageKey,
                DefaultStorageContainer = ExistingContainer,

                UserName = NewClusterUsername,
                Password = NewClusterPassword,
            };

            ScriptAction sparkScriptAction = new ScriptAction("Install Spark",
                new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), "");

            parameters.ScriptActions.Add(ClusterNodeType.HeadNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });
            parameters.ScriptActions.Add(ClusterNodeType.WorkerNode, new System.Collections.Generic.List<ScriptAction> { sparkScriptAction });

            _hdiManagementClient.Clusters.Create(ResourceGroupName, NewClusterName, parameters);
        }


        public static SubscriptionCloudCredentials GetTokenCloudCredentials(string username = null, SecureString password = null)
        {
            var authFactory = new AuthenticationFactory();

            var account = new AzureAccount { Type = AzureAccount.AccountType.User };

            if (username != null && password != null)
                account.Id = username;

            var env = AzureEnvironment.PublicEnvironments[EnvironmentName.AzureCloud];

            var accessToken =
                authFactory.Authenticate(account, env, AuthenticationFactory.CommonAdTenant, password, ShowDialog.Auto)
                    .AccessToken;

            return new TokenCloudCredentials(accessToken);
        }

        public static SubscriptionCloudCredentials GetSubscriptionCloudCredentials(SubscriptionCloudCredentials creds, Guid subId)
        {
            return new TokenCloudCredentials(subId.ToString(), ((TokenCloudCredentials)creds).Token);
        }

4. Drücken Sie **F5**, um die Anwendung auszuführen.


## Unterstützung für Open-Source-Software in HDInsight-Clustern
Der Microsoft Azure HDInsight-Dienst ist eine flexible Plattform, die es Ihnen ermöglicht, Big Data-Anwendungen in der Cloud innerhalb des Ökosystems der Open-Source-Technologien rund um Hadoop zu erstellen. Microsoft Azure bietet allgemeinen Support für Open-Source-Technologien. Siehe den Abschnitt **Supportumfang** auf der Website <a href="http://azure.microsoft.com/support/faq/" target="_blank">Azure-Support-FAQ</a>. Der HDInsight-Dienst bietet, wie nachstehend beschrieben, zusätzliche Unterstützung für einige der Komponenten.

Es gibt zwei Arten von Open-Source-Komponenten, die im HDInsight-Dienst verfügbar sind:

- **Integrierte Komponenten** – Diese Komponenten sind in HDInsight-Clustern vorinstalliert und stellen Kernfunktionen des Clusters bereit. So gehören beispielsweise Yarn Resource Manager, die Hive-Abfragesprache (HiveQL) und die Mahout Library zu dieser Kategorie. Eine vollständige Liste der Clusterkomponenten finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/" target="_blank">Neuerungen in den von HDInsight bereitgestellten Hadoop-Clusterversionen</a>.
- **Benutzerdefinierte Komponenten** – Als Benutzer des Clusters können Sie in Ihrem Workload eine beliebige in der Community verfügbare oder von Ihnen erstellte Komponente installieren oder verwenden.

Integrierte Komponenten werden vollständig unterstützt, und Microsoft Support hilft Ihnen, Probleme im Zusammenhang mit diesen Komponenten zu isolieren und zu beheben.

> [AZURE.WARNING]Komponenten, die mit dem HDInsight-Cluster bereitgestellt werden, werden vollständig unterstützt, und Microsoft Support hilft Ihnen, Probleme im Zusammenhang mit diesen Komponenten zu isolieren und zu beheben.
>
> Für benutzerdefinierte Komponenten steht kommerziell angemessener Support für eine weiterführende Behebung des Problems zur Verfügung. Auf diese Weise kann das Problem behoben werden, ODER Sie werden aufgefordert, verfügbare Kanäle für Open-Source-Technologien in Anspruch zu nehmen, die über umfassende Kenntnisse für diese Technologien verfügen. So können z. B. viele Communitywebsites wie die folgenden verwendet werden: [MSDN-Forum für HDInsight](https://social.msdn.microsoft.com/Forums/azure/de-DE/home?forum=hdinsight), [http://stackoverflow.com](http://stackoverflow.com). Für Apache-Projekte gibt es auf [http://apache.org](http://apache.org) Projektwebsites, zum Beispiel: [Hadoop](http://hadoop.apache.org/), [Spark](http://spark.apache.org/).

Der HDInsight-Dienst bietet mehrere Möglichkeiten, benutzerdefinierte Komponenten zu verwenden. Unabhängig davon, wie die Komponente verwendet wird oder im Cluster installiert ist, gilt der gleiche Supportumfang. Nachfolgend finden Sie eine Liste der am häufigsten genutzten Möglichkeiten für die Verwendung von benutzerdefinierten Komponenten in HDInsight-Clustern:

1. Senden des Auftrags – Hadoop- oder andere Auftragstypen, die benutzerdefinierte Komponenten ausführen oder verwenden, können zum Cluster gesendet werden.
2. Clusteranpassung – Während der Clustererstellung können Sie zusätzliche Einstellungen und benutzerdefinierte Komponenten angeben, die auf den Clusterknoten installiert werden.
3. Beispiele – Für beliebte benutzerdefinierte Komponenten stellen Microsoft und andere Anbieter u. U. Beispiele dafür bereit, wie diese Komponenten in den HDInsight-Clustern verwendet werden können. Für diese Beispiele wird kein Support bereitgestellt.

## Entwickeln von Script Action-Skripts

Weitere Informationen finden Sie unter [Entwickeln von Skriptaktionsskripts für HDInsight][hdinsight-write-script].


## Weitere Informationen

- Unter [Erstellen von Hadoop-Clustern in HDInsight][hdinsight-provision-cluster] finden Sie Anweisungen zum Erstellen eines HDInsight-Clusters mit anderen benutzerdefinierten Optionen.
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


[img-hdi-cluster-states]: ./media/hdinsight-hadoop-customize-cluster/HDI-Cluster-state.png "Phasen während der Clustererstellung"

<!---HONumber=Oct15_HO3-->