<properties 
	pageTitle="Installieren von R in HDInsight zum Anpassen von Clustern| Microsoft Azure" 
	description="Erfahren Sie, wie Sie R installieren und verwenden können, um Hadoop-Cluster anzupassen." 
	services="hdinsight" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/31/2015" 
	ms.author="bradsev"/>

# Installieren und Verwenden von R in HDInsight Hadoop-Clustern

Sie können R in einem beliebigen Clustertyp in Hadoop in der HDInsight-Umgebung mithilfe der Clusteranpassung über **Skriptaktion** installieren. Dies ermöglicht Big Data-Experten und -Analysten die Nutzung von R zum Bereitstellen des leistungsstarken MapReduce/YARN-Programmierungsframeworks für die Verarbeitung großer Datenmengen in Hadoop-Clustern, die in HDInsight bereitgestellt sind.

Mit Skriptaktion können Sie nur beim Erstellen eines Clusters Skripts ausführen, um ein Cluster anzupassen. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mithilfe von Skriptaktionen][hdinsight-cluster-customize].


## <a name="whatIs"></a>Was ist R?

Das <a href="http://www.r-project.org/" target="_blank">R Project for Statistical Computing</a> ist eine Open-Source-Sprache und -Umgebung für statistische Berechnungen. R bietet Hunderte integrierter Statistikfunktionen und eine eigene Programmiersprache, die Aspekte der funktionalen und objektorientierten Programmierung kombiniert. Darüber hinaus werden umfangreiche Grafikfunktionen geboten. R ist in einer Vielzahl von Feldern für die meisten professionellen Statistiker und Analysten die bevorzugte Programmierungsumgebung.

R-Skripts können in Hadoop-Clustern in HDInsight ausgeführt werden, die mithilfe der Option "Skriptaktion" angepasst wurden, als die R-Umgebung installiert wurde. R ist mit Azure-Blobspeicher (WASB) kompatibel, weshalb darin gespeicherte Daten mit R in HDInsight verarbeitet werden können.

## <a name="install"></a>Wie installiere ich R?

Ein [Beispielskript](https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1) zum Installieren von R in einem HDInsight-Cluster steht in einem schreibgeschützten Azure-Speicherblob zur Verfügung. Dieser Abschnitt enthält Anweisungen zur Verwendung des Beispielskripts während der Bereitstellung des Clusters mithilfe des Azure-Portals.

> [AZURE.NOTE]Das Beispielskript funktioniert nur mit HDInsight-Clustern der Version 3.1. Weitere Informationen zu HDInsight-Clusterversionen finden Sie unter [HDInsight-Clusterversionen](../hdinsight-component-versioning/).

1. Beginnen Sie die Bereitstellung eines Clusters mit der Option **BENUTZERDEFINIERT ERSTELLEN**, wie unter [Bereitstellen eines Clusters mit der benutzerdefinierten Erstellungsoption](../hdinsight-provision-clusters/#portal) beschrieben. 
2. Klicken Sie auf der Seite **Skriptaktionen** des Assistenten auf **Skriptaktion hinzufügen**, um wie unten gezeigt Details zur Skriptaktion bereitzustellen:

	![Anpassen eines Clusters mit "Skriptaktion"](./media/hdinsight-hadoop-r-scripts/hdi-r-script-action.png "Anpassen eines Clusters mit "Skriptaktion"")
	
	<table border='1'>
	<tr><th>Eigenschaft</th><th>Wert</th></tr>
	<tr><td>Name</td>
		<td>Geben Sie einen Namen für die Skriptaktion an, z. B. <b>R installieren</b>.</td></tr>
	<tr><td>Skript-URI</td>
		<td>Geben Sie den URI des Skripts, das aufgerufen wird, um den Cluster anzupassen. Beispiel: <i>https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1</i></td></tr>
	<tr><td>Knotentyp</td>
		<td>Gibt die Knoten an, auf denen das Anpassungsskript ausgeführt wird. Sie können <b>Alle Knoten</b>, <b>Nur Hauptknoten</b> oder <b>Nur Workerknoten</b> auswählen.
	<tr><td>Parameter</td>
		<td>Geben Sie die Parameter an, wenn dies für das Skript erforderlich ist. Für das Skript zum Installieren von R sind keine Parameter erforderlich, sodass Sie dieses Feld leer lassen können.</td></tr>
</table>Sie können dem Cluster mehr als eine Skriptaktion zum Installieren von mehreren Komponenten hinzufügen. Nachdem Sie die Skripts hinzugefügt haben, klicken Sie auf das Häkchen, um die Bereitstellung des Clusters zu starten.

Sie können das Skript auch zum Installieren von R in HDInsight mit Azure PowerShell oder dem HDInsight .NET SDK verwenden. Anweisungen zu diesen Verfahren finden Sie nachfolgend in diesem Artikel.

## <a name="useR"></a>Wie werden R-Skripts in HDInsight ausgeführt?
In diesem Abschnitt wird beschrieben, wie ein R-Skript in einem Hadoop-Cluster mit HDInsight ausgeführt wird.

1. **Richten Sie eine Remotedesktopverbindung mit dem Cluster ein**: Aktivieren Sie im Azure-Portal Remotedesktop für den Cluster, den Sie mit installiertem R erstellt haben, und stellen Sie dann eine Remoteverbindung mit dem Cluster her. Anweisungen hierzu finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Herstellen einer Verbindung mit HDInsight-Clustern mit RDP</a>.

2. **Öffnen der R-Konsole**: Nach der Installation von R befindet sich eine Verknüpfung mit der R-Konsole auf dem Desktop des Hauptknotens. Klicken Sie darauf, um die R-Konsole zu öffnen.

3. **Ausführen des R-Skripts**: Das R-Skript kann direkt über die R-Konsole ausgeführt werden, indem es darin eingefügt, ausgewählt und die EINGABETASTE gedrückt wird. Hier ist ein einfaches Beispielskript, das die Zahlen von 1 bis 100 generiert und diese anschließend mit 2 multipliziert.

		library(rmr2)
		library(rhdfs)
		ints = to.dfs(1:100)
		calc = mapreduce(input = ints, map = function(k, v) cbind(v, 2*v))
		from.dfs(calc)

Die ersten beiden Zeilen rufen die mit R installierten RHadoop-Bibliotheken auf. Die letzte Zeile gibt die Ergebnisse in der Konsole aus. Die Ausgabe sollte wie folgt aussehen:

	[1,]  1 2
	[2,]  2 4
	.
	.
	.
	[98,]  98 196
	[99,]  99 198
	[100,] 100 200

## <a name="usingPS"></a>Installieren von R in HDInsight mit Azure PowerShell

In diesem Abschnitt verwenden wir das Cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** zum Aufrufen von Skripts mithilfe von "Skriptaktion" zum Anpassen eines Clusters. Stellen Sie vor dem Fortfahren sicher, dass Azure PowerShell installiert und konfiguriert ist. Weitere Informationen zum Konfigurieren einer Arbeitsstation für die Ausführung von HDInsight Powershell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure-PowerShell][powershell-install-configure].

Führen Sie die folgenden Schritte aus:

1. Öffnen Sie die Azure PowerShell-Konsole, und deklarieren Sie die folgenden Variablen:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# Location of the HDInsight cluster. It must be in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # HDInsight version, for example "3.1"
	
2. Legen Sie die Konfigurationswerte fest, z. B. Knoten im Cluster und den zu verwendenden Standardspeicher.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Rufen Sie mithilfe des Cmdlets **Add-AzureHDInsightScriptAction** das Beispielskript für die Installation von R auf:

		# INVOKE THE SCRIPT USING THE SCRIPT ACTION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install R"  -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1


	Das Cmdlet **Add-AzureHDInsightScriptAction** verwendet die folgenden Parameter:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parameter</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definition</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Konfiguration</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Konfigurationsobjekt, dem Skriptaktionsinformationen hinzugefügt werden.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name der Skriptaktion</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Gibt die Knoten an, auf denen das Anpassungsskript ausgeführt wird. Die gültigen Werte lauten "HeadNode" (für die Installation auf dem Hauptknoten) oder "DataNode" (für die Installation auf allen Datenknoten). Sie können einen oder beide Werte verwenden.</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parameter</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Vom Skript benötigte Parameter. 
</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Gibt den URI des auszuführenden Skripts an.</td></tr>
</table>
	
4. Stellen Sie den Cluster bereit, der durch die Installation von R angepasst werden soll.
	
		# PROVISION A CLUSTER WITH R INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Geben Sie die Anmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden. Die Erstellung des Clusters kann einige Minuten in Anspruch nehmen.


## <a name="usingSDK"></a>Installieren von R in HDInsight mit dem .NET SDK

Das HDInsight .NET SDK enthält .NET-Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in .NET-Anwendungen.

Die folgenden Prozeduren müssen für die Bereitstellung eines HDInsight-Clusters mithilfe des SDK ausgeführt werden:

- [Installieren des HDInsight .NET SDK](#installSDK)
- [Erstellen eines selbstsignierten Zertifikats](#createCert)
- [Erstellen einer .NET-Anwendung in Visual Studio](#createApp)
- [Ausführen der Anwendung](#runApp)

In den folgenden Abschnitten wird gezeigt, wie Sie diese Verfahren ausführen.

### <a name="installSDK"></a>Installieren des HDInsight .NET SDK

Sie können die neueste veröffentlichte Version des SDK von [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) installieren. Die Anweisungen dazu werden im nächsten Verfahren erläutert.

### <a name="createCert"></a>Erstellen eines selbstsignierten Zertifikats

Erstellen Sie ein selbstsigniertes Zertifikat, installieren Sie es auf Ihrer Arbeitsstation und laden Sie es in Ihr Azure-Abonnement hoch. Weitere Hinweise hierzu finden Sie unter [Erstellen eines selbstsignierten Zertifikats](http://go.microsoft.com/fwlink/?LinkId=511138).


### <a name="createApp"></a>Erstellen einer .NET-Anwendung in Visual Studio

1. Öffnen Sie Visual Studio 2013.

2. Klicken Sie im Menü **Datei** auf **Neu** und dann auf **Projekt**.

3. Unter **Neues Projekt** können Sie die folgenden Werte eingeben bzw. auswählen:
	
	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
<tr>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Eigenschaft</th>
<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Wert</th></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Kategorie</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Vorlagen/Visual C#/Windows</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Vorlage</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Konsolenanwendung</td></tr>
<tr>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateRCluster</td></tr>
</table>

4. Klicken Sie auf **OK**, um das Projekt zu erstellen.

5. Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**.

6. Führen Sie den folgenden Befehl in der Konsole aus, um das Paket zu installieren:

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Mit diesem Befehl werden dem aktuellen Visual Studio-Projekt .NET-Bibliotheken und Verweise darauf hinzugefügt.

7. Doppelklicken Sie im **Projektmappen-Explorer** auf **Program.cs**, um die Datei zu öffnen.

8. Fügen Sie am Anfang der Datei die folgenden **using**-Anweisungen ein:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. Fügen Sie den folgenden Code in die Funktion **Main()** ein, und geben Sie Werte für die Variablen ein:
		
        var clusterName = args[0];

        // PROVIDE VALUES FOR THE VARIABLES
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // PROVIDE THE CERTIFICATE THUMBPRINT TO RETRIEVE THE CERTIFICATE FROM THE CERTIFICATE STORE 
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // CREATE AN HDINSIGHT CLIENT OBJECT
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;

        // PROVIDE THE CLUSTER INFORMATION
		var clusterInfo = new ClusterCreateParameters()
        {
            Name = clusterName,
            Location = location,
            DefaultStorageAccountName = storageaccountname,
            DefaultStorageAccountKey = storageaccountkey,
            DefaultStorageContainer = clusterName,
            UserName = username,
            Password = password,
            ClusterSizeInNodes = clustersize,
            Version = "3.1"
        };        

10. Fügen Sie den folgenden Code an die Funktion **Main()** an, sodass die [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx)-Klasse ein benutzerdefiniertes Skript zum Installieren von R aufruft.

		// ADD THE SCRIPT ACTION TO INSTALL R

        clusterInfo.ConfigActions.Add(new ScriptAction(
            "Install R",
            new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode },
            new Uri("https://hdiconfigactions.blob.core.windows.net/rconfigactionv02/r-installer-v02.ps1"), null
            ));

11. Erstellen Sie schließlich den Cluster:

		client.CreateCluster(clusterInfo);

11. Speichern Sie Änderungen an der Anwendung, und erstellen Sie die Lösung.

### <a name="runApp"></a>Ausführen der Anwendung

Öffnen Sie die Azure PowerShell-Konsole, wechseln Sie zum Speicherort, an dem Sie das Projekt gespeichert haben, wechseln Sie innerhalb des Projekts zum Verzeichnis "\\bin\\debug", und führen Sie dann den folgenden Befehl aus:

	.\CreateRCluster <cluster-name>

Geben Sie einen Clusternamen ein, und drücken Sie die EINGABETASTE zum Bereitstellen eines Clusters mit installiertem R.

## <a name="seeAlso"></a>Weitere Informationen

- Unter [Installieren und Verwenden von Spark für HDInsight-Cluster][hdinsight-install-spark] finden Sie Anweisungen zum Verwenden der Clusteranpassung zum Installieren und Verwenden von Spark für HDInsight Hadoop-Cluster. Spark ist ein Open-Source-Framework für die Parallelverarbeitung, das die arbeitsspeicherinterne Verarbeitung unterstützt, um die Leistung von Anwendungen zur Analyse von Big Data zu steigern.
- [Installieren von Giraph in HDInsight-Clustern](../hdinsight-hadoop-giraph-install). Verwenden Sie die Clusteranpassung, um Giraph in HDInsight Hadoop-Clustern zu installieren. Giraph ermöglicht Ihnen, mithilfe von Hadoop Graphverarbeitungen durchzuführen. Es kann zudem mit Azure HDInsight eingesetzt werden.
- [Installieren von Solr in HDInsight-Clustern](../hdinsight-hadoop-solr-install). Verwenden Sie die Clusteranpassung, um Solr in HDInsight Hadoop-Clustern zu installieren. Solr ermöglicht Ihnen, leistungsstarke Suchvorgänge auf gespeicherte Daten anzuwenden.

[powershell-install-configure]: ../install-configure-powershell.md
[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
 

<!---HONumber=July15_HO2-->