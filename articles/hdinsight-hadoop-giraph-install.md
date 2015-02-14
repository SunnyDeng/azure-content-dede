<properties 
	pageTitle="Verwenden einer Skriptaktion in HDInsight zum Installieren von Giraph in einem Hadoop-Cluster| Azure" 
	description="Erfahren Sie, wie Sie HDInsight-Cluster zum Installieren von Giraph anpassen können. Sie verwenden eine Script Action-Konfigurationsoption, um mithilfe eines Skripts Giraph zu installieren" 
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
	ms.date="12/19/2014" 
	ms.author="nitinme"/>

# Installieren und Verwenden von Giraph in HDInsight Hadoop-Clustern

Sie können Giraph in einem beliebigen Clustertyp in Hadoop auf HDInsight mithilfe der **Skriptaktion**-Clusteranpassung installieren. Mit Skriptaktion können Sie nur beim Erstellen eines Clusters Skripts ausführen, um ein Cluster anzupassen. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mit einer Skriptaktion][hdinsight-cluster-customize].

In diesem Thema wird beschrieben, wie Sie Giraph mithilfe der Funktion "Skriptaktion" installieren. Nach der Installation von Giraph erfahren Sie auch, wie Sie Giraph bei den typischsten Anwendungen verwenden können, bei der Verarbeitung von großen Graphen.


## Themen in diesem Artikel

- [Was ist Giraph?](#whatis)
- [Wie installiere ich Giraph?](#install)
- [Wie verwende ich Giraph in HDInsight?](#usegiraph)
- [Installieren von Giraph in HDInsight Hadoop-Clustern mit PowerShell](#usingPS)
- [Installieren von Giraph in HDInsight Hadoop-Clustern mit dem .NET SDK](#usingSDK) 


## <a name="whatis"></a>Was ist Giraph?

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> ermöglicht es Ihnen, mithilfe von Hadoop Graphverarbeitung durchzuführen. Es kann zudem mit Azure HDInsight eingesetzt werden. Graphen (Diagramme) modellieren Beziehungen zwischen Objekten wie beispielsweise Verbindungen zwischen Routern in einem großen Netzwerk wie dem Internet oder Beziehungen zwischen Menschen in sozialen Netzwerken (manchmal als "Social Graph" bezeichnet). Mit der Graphverarbeitung können Sie sich Gedanken über die Beziehungen zwischen den Objekten im Diagramm machen wie etwa:

- Feststellen potenzieller Freunde aufgrund der aktuellen Beziehungen
- Feststellen der kürzesten Route zwischen zwei Computern in einem Netzwerk
- Berechnen des Seitenrangs von Websites

   
## <a name="install"></a>Wie installiere ich Giraph?

Ein Beispielskript zum Installieren von Giraph in einem HDInsight-Cluster steht in einem schreibgeschützten Azure-Speicher-BLOB unter [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1) zur Verfügung. Dieser Abschnitt enthält Anweisungen zur Verwendung des Beispielskripts während der Bereitstellung des Clusters mit dem Azure-Verwaltungsportal. 

> [AZURE.NOTE] Das Beispielskript funktioniert nur mit HDInsight-Clustern der Version 3.1.  Weitere Informationen zu HDInsight-Clusterversionen finden Sie unter [HDInsight-Clusterversionen](http://azure.microsoft.com/de-de/documentation/articles/hdinsight-component-versioning/).

1. Starten Sie die Bereitstellung eines Clusters mit der Option **BENUTZERDEFINIERT ERSTELLEN**, wie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight](http://azure.microsoft.com/de-de/documentation/articles/hdinsight-provision-clusters/#portal) beschrieben. 
2. Klicken Sie auf der Seite **Skriptaktionen** des Assistenten auf **Skriptaktion hinzufügen**, um wie unten gezeigt Details zur Skriptaktion bereitzustellen:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Eigenschaft</th><th>Wert</th></tr>
		<tr><td>Name</td>
			<td>Geben Sie einen Namen für die Skriptaktion an. Beispielsweise <b>Giraph installieren</b>.</td></tr>
		<tr><td>Skript-URI</td>
			<td>Geben Sie den URI für das Skript an, das aufgerufen wird, um den Cluster anzupassen. Beispiel: <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
		<tr><td>Knotentyp</td>
			<td>Gibt die Knoten an, auf denen das Anpassungsskript ausgeführt wird. Sie können <b>Alle Knoten</b>, <b>Nur Hauptknoten</b> oder <b>Nur Workerknoten</b> auswählen.
		<tr><td>Parameter</td>
			<td>Geben Sie die Parameter an, wenn dies für das Skript erforderlich ist. Für das Skript zum Installieren von Giraph sind keine Parameter erforderlich, sodass Sie dies leer lassen können.</td></tr>
	</table>	

	Sie können dem Cluster mehr als eine Skriptaktion zum Installieren von mehreren Komponenten hinzufügen. Nachdem Sie die Skripts hinzugefügt haben, klicken Sie auf das Häkchen, um die Bereitstellung des Clusters zu starten.

Sie können das Skript auch zum Installieren von Giraph auf HDInsight mit PowerShell oder dem HDInsight .NET SDK verwenden. Anweisungen zu diesen Verfahren finden Sie nachfolgend in diesem Thema.

## <a name="usegiraph"></a>Wie verwende ich Giraph in HDInsight?

Zur Demonstration der grundlegenden <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a> -Implementierung verwenden wir SimpleShortestPathsComputation, um den kürzesten Pfad zwischen Objekten in einem Graph zu finden. Mit den folgenden Schritten können Sie die Beispieldaten und das Beispiel-Jar hochladen, einen Auftrag mithilfe des Beispiels "SimpleShortestPathsComputation" ausführen und dann die Ergebnisse anzeigen.

1. **Laden Sie eine Beispieldatendatei in WASB hoch**. Erstellen Sie auf der lokalen Arbeitsstation eine neue Datei namens **tiny_graph.txt**. Sie sollte folgende Zeilen enthalten.

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Laden Sie die Datei "tiny_graph.txt" in den Primärspeicher Ihres HDInsight-Clusters hoch. Anweisungen zum Hochladen von Daten finden Sie unter [Hochladen von Daten für Hadoop-Aufträge in HDInsight](../hdinsight-upload-data/).

	Diese Daten beschreiben eine Beziehung zwischen Objekten in einem gerichteten Graph mithilfe des Formats [source\_id, source\_value,[[dest\_id], [edge\_value],...]]. Jede Zeile repräsentiert eine Beziehung zwischen einer **source\_id** und einem oder mehreren **dest\_id**-Objekten. Der **edge\_value** (oder die Gewichtung) ist vorstellbar als die Stärke oder Distanz der Verbindung zwischen **source\_id** und **dest\_id**.

	Wenn man die obigen Daten auseinanderzieht und den Wert (oder das Gewicht) als den Abstand zwischen den Objekten verwendet, dann könnte das so aussehen.

	![tiny_graph.txt drawn as circles with lines of varying distance between](.\media\hdinsight-hadoop-giraph-install\giraph-graph.png)

	

4. Führen Sie das Beispiel **SimpleShortstPathsComputation** aus. Verwenden Sie die folgenden PowerShell-Cmdlets, um das Beispiel mithilfe der Datei "tiny_graph.txt" als Eingabe auszuführen. Dazu muss [Azure PowerShell][powershell-install-configure] installiert und konfiguriert sein.

		$clusterName = "clustername"
		# Giraph examples JAR
		$jarFile = "wasb:///example/jars/giraph-examples.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation",
		                "-ca", "mapred.job.tracker=headnodehost:9010",
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat",
		                "-vip", "wasb:///example/data/tiny_graph.txt",
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat",
		                "-op",  "wasb:///example/output/shortestpaths",
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition
		  -JarFile $jarFile
		  -ClassName "org.apache.giraph.GiraphRunner"
		  -Arguments $jobArguments
		
		# Run the job, write output to the PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	Ersetzen Sie im Beispiel oben **clustername** durch den Namen Ihres HDInsight-Clusters, in dem Giraph installiert ist.

5. **Zeigen Sie die Ergebnisse an**.  Nach Abschluss des Auftrags werden die Ergebnisse in zwei Ausgabedateien im Ordner __wasb:///example/out/shotestpaths__ gespeichert. Die Dateien haben den Namen __part-m-00001__ und __part-m-00002__. Führen Sie die folgenden Schritte aus, um die Ausgabe herunterzuladen und anzuzeigen.

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"             # Blob storage container name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

	Damit wird die Verzeichnisstruktur __example/output/shortestpaths__ im aktuellen Verzeichnis auf der Arbeitsstation erstellt, und die beiden Ausgabedateien werden an diesen Speicherort heruntergeladen.

	Verwenden Sie das Cmdlet __Cat__, um den Inhalt der Dateien anzuzeigen: 

		Cat example/output/shortestpaths/part*

	Die Ausgabe sollte in etwa folgendermaßen aussehen:


		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

 Das Beispiel SimpleShortestPathComputation wurde fest programmiert und beginnt mit der Objekt-ID 1. Es findet den kürzesten Pfad zu anderen Objekten. Die Ausgabe sollte daher als `destination_id distance` gelesen werden, wobei der Abstand der Wert (oder das Gewicht) der Kanten ist, der zwischen Objekt-ID 1 und der Ziel-ID zurückgelegt wird.
	
Wenn Sie dies visualisieren, können Sie die Ergebnisse überprüfen, indem Sie den kürzesten Weg zwischen ID 1 und allen anderen Objekten zurücklegen. Beachten Sie, dass 5 der kürzeste Pfad zwischen ID 1 und ID 4 ist. Dies ist die Gesamtentfernung zwischen <span style="color:orange">ID 1 und 3</span>und dann <span style="color:red">ID 3 und 4</span>.

	![Drawing of objects as circles with shortest paths drawn between](.\media\hdinsight-hadoop-giraph-install\giraph-graph-out.png) 

## <a name="usingPS"></a>Installieren von Giraph in HDInsight Hadoop-Clustern mit PowerShell

In diesem Abschnitt wird das Cmdlet **<a href = "http://msdn.microsoft.com/de-de/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** verwendet, um Skripts mithilfe der Skriptaktion aufzurufen, um ein Cluster anzupassen. Stellen Sie vor dem Fortfahren sicher, dass PowerShell installiert und konfiguriert ist. Weitere Informationen zum Konfigurieren einer Arbeitsstation für die Ausführung von HDInsight Powershell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].

Führen Sie die folgenden Schritte aus:

1. Öffnen Sie ein Azure PowerShell-Fenster, und deklarieren Sie die folgenden Variablen:

		# PROVIDE VALUES FOR THESE VARIABLES
		$subscriptionName = "<SubscriptionName>"		# Name of the Azure subscription
		$clusterName = "<HDInsightClusterName>"			# The HDInsight cluster name
		$storageAccountName = "<StorageAccountName>"	# Azure storage account that hosts the default container
		$storageAccountKey = "<StorageAccountKey>"      # Key for the storage account
		$containerName = $clusterName
		$location = "<MicrosoftDataCenter>"				# The location of the HDInsight cluster. It must in the same data center as the storage account.
		$clusterNodes = <ClusterSizeInNumbers>			# The number of nodes in the HDInsight cluster.
		$version = "<HDInsightClusterVersion>"          # For example "3.1"
	
2. Legen Sie die Konfigurationswerte fest, z. B. Knoten im Cluster und den zu verwendenden Standardspeicher.

		# SPECIFY THE CONFIGURATION OPTIONS
		Select-AzureSubscription $subscriptionName
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes
		$config.DefaultStorageAccount.StorageAccountName="$storageAccountName.blob.core.windows.net"
		$config.DefaultStorageAccount.StorageAccountKey=$storageAccountKey
		$config.DefaultStorageAccount.StorageContainerName=$containerName
	
3. Rufen Sie mithilfe des Cmdlets **Add-AzureHDInsightScriptAction** das Beispielskript auf, um die Skriptaktion zur Clusterkonfiguration hinzuzufügen. Die Skriptaktion wird später bei der Erstellung des Clusters ausgeführt. 

		# ADD SCRIPT ACTION TO CLUSTER CONFIGURATION
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Giraph" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1

	Das Cmdlet **Add-AzureHDInsightScriptAction** verwendet die folgenden Parameter:

	<table style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse;">
	<tr>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:90px; padding-left:5px; padding-right:5px;">Parameter</th>
	<th style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; width:550px; padding-left:5px; padding-right:5px;">Definition</th></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Config</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px; padding-right:5px;">Das Konfigurationsobjekt, dem Skriptaktionsinformationen hinzugefügt werden.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name der Skriptaktion</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">ClusterRoleCollection</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Gibt die Knoten an, auf denen das Anpassungsskript ausgeführt wird. Die gültigen Werte sind "HeadNode" (für die Installation auf dem Hauptknoten) oder "DataNode" (für die Installation auf allen Datenknoten). Sie können einen oder beide Werte verwenden.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Uri</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Gibt den URI des auszuführenden Skripts an.</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Parameter</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Vom Skript benötigte Parameter. Das in diesem Thema verwendete Skript benötigt keine Parameter, weshalb dieser Parameter nicht im obigen Auszug enthalten ist.
	</td></tr>
	</table>
	
4. Starten Sie dann mit der Bereitstellung eines angepassten Clusters mit installiertem Giraph.  
	
		# START PROVISIONING A CLUSTER WITH GIRAPH INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Geben Sie die Anmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden. Die Erstellung des Clusters kann einige Minuten in Anspruch nehmen.


## <a name="usingSDK"></a>Installieren von Giraph in HDInsight Hadoop-Clustern mit dem .NET SDK

Das HDInsight .NET SDK enthält .NET-Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in .NET-Anwendungen. Dieser Abschnitt enthält Anweisungen zur Verwendung von Skriptaktionen aus dem SDK, um ein Cluster mit installiertem Giraph bereitzustellen. Die folgenden Verfahren müssen ausgeführt werden:

- Installieren des HDInsight .NET SDK
- Erstellen eines selbstsignierten Zertifikats
- Erstellen einer Konsolenanwendung
- Ausführen der Anwendung


**So installieren Sie das HDInsight .NET SDK**

Sie können die neueste veröffentlichte Version des SDK von [NuGet](http://nuget.codeplex.0com/wikipage?title=Getting%20Started) herunterladen und installieren. Die Anweisungen dazu werden im nächsten Verfahren erläutert.

**So erstellen Sie ein selbstsigniertes Zertifikat**

Erstellen Sie ein selbstsigniertes Zertifikat, installieren Sie es auf Ihrer Arbeitsstation und laden Sie es in Ihr Azure-Abonnement hoch. Anweisungen finden Sie unter [Erstellen eines selbstsignierten Zertifikats](http://go.microsoft.com/fwlink/?LinkId=511138). 


**So erstellen Sie eine Visual Studio-Anwendung**

1. Öffnen Sie Visual Studio 2013.

2. Klicken Sie im Menü Datei auf **Neu** und anschließend auf **Projekt**.

3. Unter "Neues Projekt" können Sie die folgenden Werte eingeben bzw. auswählen:
	
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
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateGiraphCluster</td></tr>
	</table>

4. Klicken Sie auf **OK**, um das Projekt zu erstellen.

5. Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**.

6. Führen Sie die folgenden Befehle in der Konsole aus, um das Paket zu installieren.

		Install-Package Microsoft.WindowsAzure.Management.HDInsight

	Mit diesem Befehl werden dem aktuellen Visual Studio-Projekt .NET-Bibliotheken und Verweise darauf hinzugefügt.

7. Doppelklicken Sie im Projektmappen-Explorer auf **Program.cs**, um die Datei zu öffnen.

8. Fügen Sie die folgenden Anweisungen am Anfang der Datei ein:

		using System.Security.Cryptography.X509Certificates;
		using Microsoft.WindowsAzure.Management.HDInsight;
		using Microsoft.WindowsAzure.Management.HDInsight.ClusterProvisioning;
		using Microsoft.WindowsAzure.Management.HDInsight.Framework.Logging;
	
9. Fügen Sie den folgenden Code in die "Main()"-Funktion ein, und geben Sie Werte für die Variablen ein:
		
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

10. Fügen Sie den folgenden Code an die "Main()"-Funktion an, sodass die [ScriptAction](http://msdn.microsoft.com/de-de/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx)-Klasse ein benutzerdefiniertes Skript zum Installieren von Giraph aufruft.

		// ADD THE SCRIPT ACTION TO INSTALL GIRAPH
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Giraph", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Giraph on
          new Uri("https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1"), // Location of the script to install Giraph
		  null //because the script used does not require any parameters.
        ));

11. Erstellen Sie schließlich den Cluster.

		client.CreateCluster(clusterInfo);

11. Speichern Sie Änderungen an der Anwendung, und erstellen Sie die Lösung. 

**So führen Sie die Anwendung aus**

Öffnen Sie eine PowerShell-Konsole, wechseln Sie zum Speicherort, an dem Sie das Visual Studio-Projekt gespeichert haben, dann innerhalb des Projekts zum Verzeichnis "\bin\debug", und führen Sie den folgenden Befehl aus:

	.\CreateGiraphCluster <cluster-name>

Geben Sie einen Clusternamen an, und drücken Sie die EINGABETASTE, um ein Cluster mit installiertem Giraph bereitzustellen.


## Weitere Informationen##
- Unter [Installieren und Verwenden von Spark für HDInsight-Cluster][hdinsight-install-spark] finden Sie Anweisungen zum Verwenden der Clusteranpassung zum Installieren und Verwenden von Spark für HDInsight Hadoop-Cluster. Spark ist ein Open-Source-Framework für die Parallelverarbeitung, das die arbeitsspeicherinterne Verarbeitung unterstützt, um die Leistung von Anwendungen zur Analyse von Big Data zu steigern.
- Unter [Installieren und Verwenden von R für HDInsight-Cluster][hdinsight-install-r] finden Sie Anweisungen zum Verwenden der Clusteranpassung zum Installieren und Verwenden von R in HDInsight Hadoop-Clustern. R ist eine Open-Source-Sprache und -Umgebung für statistische Berechnungen. Sie bietet Hunderte integrierter Statistikfunktionen und eine eigene Programmiersprache, die Aspekte der funktionalen und objektorientierten Programmierung kombiniert. Darüber hinaus werden umfangreiche Grafikfunktionen geboten.
- [Installieren und Verwenden von Solr in HDInsight-Clustern](../hdinsight-hadoop-solr-install). Verwenden Sie die Clusteranpassung, um Solr in HDInsight Hadoop-Clustern zu installieren. Solr ermöglicht es Ihnen, leistungsstarke Suchvorgänge für gespeicherte Daten durchzuführen.



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/de-de/documentation/articles/install-configure-powershell/

[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster<!--HONumber=42-->
