<properties 
	pageTitle="Verwenden einer Skriptaktion in HDInsight zum Installieren von Solr in einem Hadoop-Cluster| Azure" 
	description="Erfahren Sie, wie Sie HDInsight-Cluster zum Installieren von Solr anpassen können. Sie verwenden eine Script Action-Konfigurationsoption, um mithilfe eines Skripts Solr zu installieren" 
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

# Installieren und Verwenden von Solr in HDInsight Hadoop-Clustern

Sie können Solr in einem beliebigen Clustertyp in Hadoop auf HDInsight mithilfe der **Skriptaktion**-Clusteranpassung installieren. Mit Skriptaktion können Sie nur beim Erstellen eines Clusters Skripts ausführen, um ein Cluster anzupassen. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mit einer Skriptaktion][hdinsight-cluster-customize].

In diesem Thema wird beschrieben, wie Sie Solr mithilfe der Funktion "Skriptaktion" installieren. Solr ist eine leistungsfähige Suchplattform und bietet Suchfunktionen der Unternehmensklasse für von Hadoop verwaltete Daten. Sobald Sie Solr in einem HDInsight-Cluster installiert haben, erfahren Sie auch, wie Sie Daten mithilfe von Solr suchen können.

> [AZURE.NOTE] Das in diesem Thema verwendete Beispielskript erstellt einen Solr-Cluster mit einer bestimmten Konfiguration. Wenn Sie den Solr-Cluster mit anderen Auflistungen, Shards, Schemas, Replikaten usw. konfigurieren möchten, müssen Sie das Skript und die Solr-Binärdateien entsprechend ändern.


## Themen in diesem Artikel

- [Was ist Solr?](#whatis)
- [Wie installiere ich Solr?](#install)
- [Wie verwende ich Solr in HDInsight?](#usesolr)
- [Installieren von Solr in HDInsight Hadoop-Clustern mithilfe von PowerShell](#usingPS)
- [Installieren von Solr in HDInsight Hadoop-Clustern mithilfe des .NET SDK](#usingSDK) 


## <a name="whatis"></a>Was ist Solr?

<a href="http://lucene.apache.org/solr/features.html" target="_blank">Apache Solr</a> ist Unternehmensplattform für die leistungsstarke Volltextsuche nach Daten. Während Hadoop das Speichern und Verwalten von großen Datenmengen ermöglicht, bietet Apache Solr die Suchfunktionen, um schnell Daten abzurufen. In diesem Thema wird beschrieben, wie Sie ein HDInsight-Cluster zum Installieren von Solr anpassen können.   

## <a name="install"></a>Wie installiere ich Solr?

Ein Beispielskript zum Installieren von Solr in einem HDInsight-Cluster steht in einem schreibgeschützten Azure-Speicher-BLOB unter [https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1) zur Verfügung. Dieser Abschnitt enthält Anweisungen zur Verwendung des Beispielskripts während der Bereitstellung des Clusters mit dem Azure-Verwaltungsportal. 


> [AZURE.NOTE] Das Beispielskript funktioniert nur mit HDInsight-Clustern der Version 3.1.  Weitere Informationen zu HDInsight-Clusterversionen finden Sie unter [HDInsight-Clusterversionen](http://azure.microsoft.com/de-de/documentation/articles/hdinsight-component-versioning/).


1. Starten Sie die Bereitstellung eines Clusters mit der Option **BENUTZERDEFINIERT ERSTELLEN**, wie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight](http://azure.microsoft.com/de-de/documentation/articles/hdinsight-provision-clusters/#portal) beschrieben. 
2. Klicken Sie auf der Seite **Skriptaktionen** des Assistenten auf **Skriptaktion hinzufügen**, um wie unten gezeigt Details zur Skriptaktion bereitzustellen:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-solr-install/hdi-script-action-solr.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Eigenschaft</th><th>Wert</th></tr>
		<tr><td>Name</td>
			<td>Geben Sie einen Namen für die Skriptaktion an. Beispielsweise <b>Solr installieren</b>.</td></tr>
		<tr><td>Skript-URI</td>
			<td>Geben Sie den URI für das Skript an, das aufgerufen wird, um den Cluster anzupassen. Beispiel: <i>https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1</i></td></tr>
		<tr><td>Knotentyp</td>
			<td>Gibt die Knoten an, auf denen das Anpassungsskript ausgeführt wird. Sie können <b>Alle Knoten</b>, <b>Nur Hauptknoten</b> oder <b>Nur Workerknoten</b> auswählen.
		<tr><td>Parameter</td>
			<td>Geben Sie die Parameter an, wenn dies für das Skript erforderlich ist. Für das Skript zum Installieren von Solr sind keine Parameter erforderlich, sodass Sie dieses Feld leer lassen können.</td></tr>
	</table>	

	Sie können dem Cluster mehr als eine Skriptaktion zum Installieren von mehreren Komponenten hinzufügen. Nachdem Sie die Skripts hinzugefügt haben, klicken Sie auf das Häkchen, um die Bereitstellung des Clusters zu starten.

Sie können das Skript auch zum Installieren von Solr auf HDInsight mit PowerShell oder dem HDInsight .NET SDK verwenden. Anweisungen zu diesen Verfahren finden Sie nachfolgend in diesem Thema.

## <a name="usesolr"></a>Wie verwende ich Solr in HDInsight?

Sie müssen mit der Indizierung von Solr mit einigen Datendateien beginnen. Sie können dann Solr verwenden, um Suchabfragen der indizierten Daten auszuführen. Führen Sie die folgenden Schritte aus, um Solr in einem HDInsight-Cluster zu verwenden:

1. **Stellen Sie eine Verbindung mit dem HDInsight-Cluster bei installiertem Solr mittels RDP her**. Aktivieren Sie im Azure-Verwaltungsportal einen Remotedesktop für den Cluster, den Sie bei installiertem Solr erstellt haben, und stellen Sie dann eine Remoteverbindung mit dem Cluster her. Anweisungen dazu finden Sie unter <a href="http://azure.microsoft.com/de-de/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Herstellen einer Verbindung zu HDInsight-Clustern mit RDP</a>.

2. **Indizieren Sie Solr durch Hochladen von Datendateien**. Wenn Sie Solr indizieren, legen Sie Dokumente ab, die Sie ggf. durchsuchen müssen. Stellen Sie zum Indizieren von Solr mittels RDP eine Verbindung zum Cluster her, navigieren Sie zum Desktop, öffnen Sie die Hadoop-Befehlszeile und navigieren Sie zu **C:\apps\dist\solr-4.7.2\example\exampledocs**. Führen Sie den folgenden Befehl aus: 
	
		java -jar post.jar solr.xml monitor.xml

	You'll see the following output on the console.

		POSTing file solr.xml
		POSTing file monitor.xml
		2 files indexed.
		COMMITting Solr index changes to http://localhost:8983/solr/update..
		Time spent: 0:00:01.624

	Das Dienstprogramm "post.jar" indiziert Solr mit zwei Beispieldokumenten: **solr.xml** und **monitor.xml**. Das Dienstprogramm "post.jar" und die Beispieldokumente stehen in der Solr-Installation zur Verfügung.

3. **Verwenden Sie das Solr-Dashboard, um die indizierten Dokumente zu durchsuchen**. Öffnen Sie in der RDP-Sitzung mit dem HDInsight-Cluster den Internet Explorer, und starten Sie das Solr-Dashboard unter **http://headnodehost:8983/solr/#/**. Wählen Sie im linken Bereich aus der Dropdown-Liste "Core-Auswahl" **collection1**, und klicken Sie dann auf **Abfrage**. Geben Sie beispielsweise die folgenden Werte an, um alle Dokumente in Solr auszuwählen und zurückzugeben.
	1. Geben Sie im Textfeld **q** Folgendes ein: **\*:**\*. Dadurch werden alle Dokumente zurückgegeben, die in Solr indiziert sind. Wenn Sie nach einer bestimmten Zeichenfolge innerhalb der Dokumente suchen möchten, können diese Zeichenfolge hier eingeben.
	2. Wählen Sie im Textfeld **wt** das Ausgabeformat aus. Der Standardwert ist **json**. Klicken Sie auf **Abfrage ausführen**.

		![Use Script Action to customize a cluster](./media/hdinsight-hadoop-solr-install/hdi-solr-dashboard-query.png "Run a query on Solr dashboard")
	
	Die Ausgabe gibt die beiden Dokumente zurück, die wir zur Indizierung von Solr verwendet haben. Die Ausgabe sieht ungefähr so aus:

			"response": {
			    "numFound": 2,
			    "start": 0,
			    "maxScore": 1,
			    "docs": [
			      {
			        "id": "SOLR1000",
			        "name": "Solr, the Enterprise Search Server",
			        "manu": "Apache Software Foundation",
			        "cat": [
			          "software",
			          "search"
			        ],
			        "features": [
			          "Advanced Full-Text Search Capabilities using Lucene",
			          "Optimized for High Volume Web Traffic",
			          "Standards Based Open Interfaces - XML and HTTP",
			          "Comprehensive HTML Administration Interfaces",
			          "Scalability - Efficient Replication to other Solr Search Servers",
			          "Flexible and Adaptable with XML configuration and Schema",
			          "Good unicode support: héllo (hello with an accent over the e)"
			        ],
			        "price": 0,
			        "price_c": "0,USD",
			        "popularity": 10,
			        "inStock": true,
			        "incubationdate_dt": "2006-01-17T00:00:00Z",
			        "_version_": 1486960636996878300
			      },
			      {
			        "id": "3007WFP",
			        "name": "Dell Widescreen UltraSharp 3007WFP",
			        "manu": "Dell, Inc.",
			        "manu_id_s": "dell",
			        "cat": [
			          "electronics and computer1"
			        ],
			        "features": [
			          "30\" TFT active matrix LCD, 2560 x 1600, .25mm dot pitch, 700:1 contrast"
			        ],
			        "includes": "USB cable",
			        "weight": 401.6,
			        "price": 2199,
			        "price_c": "2199,USD",
			        "popularity": 6,
			        "inStock": true,
			        "store": "43.17614,-90.57341",
			        "_version_": 1486960637584081000
			      }
			    ]
			  }
   

4. **Empfohlen: Sichern Sie die indizierten Daten aus Solr in Azure-Speicher-BLOB (WASB), das dem HDInsight-Cluster zugeordnet ist.**. Es empfiehlt sich, die indizierten Daten aus den Solr-Clusterknoten in WASB zu sichern. Führen Sie dazu die folgenden Schritte aus:

	1. Öffnen Sie aus der RDP-Sitzung den Internet Explorer, und verweisen Sie auf die folgende URL:

			http://localhost:8983/solr/replication?command=backup

		You should see a response like this

			<?xml version="1.0" encoding="UTF-8"?>
			<response>
			  <lst name="responseHeader">
			    <int name="status">0</int>
			    <int name="QTime">9</int>
			  </lst>
			  <str name="status">OK</str>
			</response>

	2. Navigieren Sie in der Remotesitzung zu "{SOLR_HOME}\{Collection}\data". Für den mit dem Beispielskript erstellten Cluster ist dies **C:\apps\dist\solr-4.7.2\example\solr\collection1\data**. An diesem Speicherort sollte ein Momentaufnahme-Ordner mit einem Namen wie **snapshot.*zeitstempel*** erstellt werden.
	
	3. Verpacken Sie den Momentaufnahme-Ordner, und laden Sie ihn in WASB hoch. Navigieren Sie in der Hadoop-Befehlszeile zum Verzeichnis des Momentaufnahme-Ordners mithilfe des folgenden Befehls:

			  hadoop fs -CopyFromLocal snapshot._timestamp_.zip /example/data

		Mit diesem Befehl wird die Momentaufnahme in "/example/data/" unter der Container im Standardspeicherkonto kopiert, das dem Cluster zugeordnet ist.

## <a name="usingPS"></a>Installieren von Solr in HDInsight Hadoop-Clustern mithilfe von PowerShell

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
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Solr" -ClusterRoleCollection HeadNode,DataNode -Uri https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1

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
	
4. Starten Sie dann die Bereitstellung eines angepassten Cluster mit installiertem Solr.  
	
		# START PROVISIONING A CLUSTER WITH SOLR INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Geben Sie die Anmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden. Die Erstellung des Clusters kann einige Minuten in Anspruch nehmen.


## <a name="usingSDK"></a>Installieren von Solr in HDInsight Hadoop-Clustern mithilfe des .NET SDK

Das HDInsight .NET SDK enthält .NET-Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in .NET-Anwendungen. Dieser Abschnitt enthält Anweisungen zur Verwendung von Skriptaktionen aus dem SDK, um ein Cluster mit installiertem Solr bereitzustellen. Die folgenden Verfahren müssen ausgeführt werden:

- Installieren des HDInsight .NET SDK
- Erstellen eines selbstsignierten Zertifikats
- Erstellen einer Konsolenanwendung
- Ausführen der Anwendung


**So installieren Sie das HDInsight .NET SDK**

Sie können die neueste veröffentlichte Version des SDK von [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) herunterladen und installieren. Die Anweisungen dazu werden im nächsten Verfahren erläutert.

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
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Konsole Anwendung</td></tr>
	<tr>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">Name</td>
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSolrCluster</td></tr>
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

10. Fügen Sie den folgenden Code an die "Main()"-Funktion an, sodass die [ScriptAction](http://msdn.microsoft.com/de-de/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx)-Klasse ein benutzerdefiniertes Skript zum Installieren von Solr aufruft.

		// ADD THE SCRIPT ACTION TO INSTALL Solr
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Solr", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode, ClusterNodeType.DataNode }, // List of nodes to install Solr on
          new Uri("https://hdiconfigactions.blob.core.windows.net/solrconfigactionv01/solr-installer-v01.ps1"), // Location of the script to install Solr
		  null //because the script used does not require any parameters.
        ));

11. Erstellen Sie schließlich den Cluster.

		client.CreateCluster(clusterInfo);

11. Speichern Sie Änderungen an der Anwendung, und erstellen Sie die Lösung. 

**So führen Sie die Anwendung aus**

Öffnen Sie eine PowerShell-Konsole, wechseln Sie zum Speicherort, an dem Sie das Visual Studio-Projekt gespeichert haben, dann innerhalb des Projekts zum Verzeichnis "\bin\debug", und führen Sie den folgenden Befehl aus:

	.\CreateSolrCluster <cluster-name>

Geben Sie einen Clusternamen ein, und drücken Sie die EINGABETASTE zum Bereitstellen eines Clusters mit installiertem Solr.


## Weitere Informationen##
- [Installieren und Verwenden von Spark in HDInsight-Clustern][hdinsight-install-spark] Verwenden Sie die Clusteranpassung, um Spark in HDInsight Hadoop-Clustern zu installieren. Spark ist ein Open-Source-Framework für die Parallelverarbeitung, das die arbeitsspeicherinterne Verarbeitung unterstützt, um die Leistung von Anwendungen zur Analyse von Big Data zu steigern.
- [Installieren und Verwenden von R in HDInsight-Clustern][hdinsight-install-r]. Verwenden Sie die Clusteranpassung, um R in HDInsight Hadoop-Clustern zu installieren. R ist eine Open-Source-Sprache und -Umgebung für statistische Berechnungen. Sie bietet Hunderte integrierter Statistikfunktionen und eine eigene Programmiersprache, die Aspekte der funktionalen und objektorientierten Programmierung kombiniert. Darüber hinaus werden umfangreiche Grafikfunktionen geboten.
- [Installieren und Verwenden von Giraph in HDInsight-Clustern](../hdinsight-hadoop-giraph-install). Verwenden Sie die Clusteranpassung, um Giraph in HDInsight Hadoop-Clustern zu installieren. Giraph ermöglicht es Ihnen, mithilfe von Hadoop Graphverarbeitungen durchzuführen. Es kann zudem mit Azure HDInsight eingesetzt werden.




[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-install-spark]: ../hdinsight-hadoop-spark-install/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
<!--HONumber=42-->
