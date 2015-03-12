<properties 
	pageTitle="Verwenden einer Skriptaktion in HDInsight zum Installieren von Spark in einem Hadoop-Cluster| Azure" 
	description="Erfahren Sie, wie Sie HDInsight-Cluster zum Installieren von Spark anpassen können. Sie verwenden eine Script Action-Konfigurationsoption, um mithilfe eines Skripts Spark zu installieren" 
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

# Installieren und Verwenden von Spark 1.0 in HDInsight-Clustern

Sie können Spark in einem beliebigen Clustertyp in Hadoop auf HDInsight mithilfe der **Skriptaktion**-Clusteranpassung installieren. Mit Skriptaktion können Sie nur beim Erstellen eines Clusters Skripts ausführen, um ein Cluster anzupassen. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mit einer Skriptaktion][hdinsight-cluster-customize].

In diesem Thema wird beschrieben, wie Sie Spark 1.0 mithilfe der Funktion "Skriptaktion" installieren. Nach der Installation von Spark erfahren Sie, wie Sie eine Spark-Abfrage auf HDInsight-Cluster anwenden.


## Themen in diesem Artikel

- [Was ist Spark?](#whatis)
- [Wie installiere ich Spark?](#install)
- [Wie wird Spark in HDInsight ausgeführt?](#usespark)
- [Installieren von Spark in HDInsight Hadoop-Clustern mithilfe von PowerShell](#usingPS)
- [Installieren von Spark in HDInsight Hadoop-Clustern mit dem .NET SDK](#usingSDK) 


## <a name="whatis"></a>Was ist Spark?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> ist ein Open-Source-Framework für die Parallelverarbeitung, das die arbeitsspeicherinterne Verarbeitung unterstützt, um die Leistung von Anwendungen zur Analyse von Big Data zu steigern. Dank seiner arbeitsspeicherinternen Datenverarbeitungsfunktionen eignet sich Spark besonders für iterative Algorithmen beim maschinellen Lernen und für Graphberechnungen.

Spark kann auch für die herkömmliche datenträgerbasierte Datenverarbeitung genutzt werden. Spark hat einen Vorteil gegenüber dem herkömmlichen MapReduce-Framework dadurch, dass auf den Zwischenstufen Schreibvorgänge auf den Datenträger vermieden werden. Spark ist auch kompatibel mit HDFS und WASB, sodass vorhandene Daten mühelos mit Spark verarbeitet werden können. 

In diesem Thema wird beschrieben, wie ein HDInsight-Cluster angepasst wird, indem Spark installiert wird.   

## <a name="install"></a>Wie installiere ich Spark?

Ein Beispielskript zum Installieren von Spark in einem HDInsight-Cluster steht in einem schreibgeschützten Azure-Speicher-Blob unter [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1) zur Verfügung. Dieser Abschnitt enthält Anweisungen zur Verwendung des Beispielskripts während der Bereitstellung des Clusters mit dem Azure-Verwaltungsportal. 

> [AZURE.NOTE] Das Beispielskript funktioniert nur mit HDInsight-Clustern der Version 3.1.  Weitere Informationen zu HDInsight-Clusterversionen finden Sie unter [HDInsight-Clusterversionen](http://azure.microsoft.com/documentation/articles/hdinsight-component-versioning/).

1. Starten Sie die Bereitstellung eines Clusters mit der Option **BENUTZERDEFINIERT ERSTELLEN**, wie unter [Benutzerdefinierte Bereitstellung eines Hadoop-Clusters in HDInsight](http://azure.microsoft.com/documentation/articles/hdinsight-provision-clusters/#portal) beschrieben. 
2. Klicken Sie auf der Seite **Skriptaktionen** des Assistenten auf **Skriptaktion hinzufügen**, um wie unten gezeigt Details zur Skriptaktion bereitzustellen:

	![Use Script Action to customize a cluster](./media/hdinsight-hadoop-customize-cluster/HDI.CustomProvision.Page6.png "Use Script Action to customize a cluster")
	
	<table border='1'>
		<tr><th>Eigenschaft</th><th>Wert</th></tr>
		<tr><td>Name</td>
			<td>Geben Sie einen Namen für die Skriptaktion an. Beispielsweise <b>Spark installieren</b>.</td></tr>
		<tr><td>Skript-URI</td>
			<td>Geben Sie den URI für das Skript an, das aufgerufen wird, um den Cluster anzupassen. Beispiel: <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1</i></td></tr>
		<tr><td>Knotentyp</td>
			<td>Gibt die Knoten an, auf denen das Anpassungsskript ausgeführt wird. Sie können <b>Alle Knoten</b>, <b>Nur Hauptknoten</b> oder <b>Nur Workerknoten</b> auswählen.
		<tr><td>Parameter</td>
			<td>Geben Sie die Parameter an, wenn dies für das Skript erforderlich ist. Für das Skript zum Installieren von Spark sind keine Parameter erforderlich, sodass Sie dieses Feld leer lassen können.</td></tr>
	</table>	

	Sie können dem Cluster mehr als eine Skriptaktion zum Installieren von mehreren Komponenten hinzufügen. Nachdem Sie die Skripts hinzugefügt haben, klicken Sie auf das Häkchen, um die Bereitstellung des Clusters zu starten.

Sie können das Skript auch zum Installieren von Spark auf HDInsight mit PowerShell oder dem HDInsight .NET SDK verwenden. Anweisungen zu diesen Verfahren finden Sie nachfolgend in diesem Thema.

## <a name="usespark"></a>Wie wird Spark in HDInsight ausgeführt?
Spark stellt APIs in Scala, Python und Java bereit. Sie können für die Ausführung von Spark-Abfragen auch die interaktive Spark-Shell verwenden. Dieser Abschnitt bietet eine Einführung in diese beiden Ansätze zum Arbeiten mit Spark:

- [Verwenden der  Spark-Shell](#sparkshell)
- [Verwenden eines eigenständigen Scala-Programms](#standalone)

###<a name="sparkshell"></a>Verwenden der Spark-Shell
Führen Sie die folgenden Schritte aus, um Spark-Abfragen über eine interaktive Spark-Shell auszuführen. In diesem Abschnitt führen wir eine Spark-Abfrage nach einer Beispieldatendatei (/example/data/gutenberg/davinci.txt) durch, die standardmäßig in HDInsight-Clustern verfügbar ist.

1. Aktivieren Sie im Azure-Verwaltungsportal einen Remotedesktop für den Cluster, den Sie mit installiertem Spark erstellt haben, und stellen Sie dann eine Remoteverbindung mit dem Cluster her. Anweisungen dazu finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Herstellen einer Verbindung zu HDInsight-Clustern mit RDP</a>.

2. Öffnen Sie in der RDP-Sitzung auf dem Desktop die Hadoop-Befehlszeile, und navigieren Sie zum Speicherort der Installation von Spark, z. B. **C:\apps\dist\spark-1.0.2**.


3. Führen Sie den folgenden Befehl aus, um die Spark-Shell zu starten.

		 .\bin\spark-shell --master yarn

	Nach Ausführung des Befehls sollte eine Scala-Eingabeaufforderung angezeigt werden.

		 scala>

5. Geben Sie an der Scala-Eingabeaufforderung die nachstehende Spark-Abfrage ein. Mit dieser Abfrage werden die Vorkommen der einzelnen Wörter in der Datei "davinci.txt" gezählt, die im Ordner "/example/data/gutenberg/" im WASB-Speicher zur Verfügung steht, der dem Cluster zugeordnet ist.

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. Die Ausgabe sollte wie folgt aussehen:

	![Output from running Scala interactive shell in an HDInsight cluster][img-hdi-scala-interactive]
		

7. Geben Sie ":q", um die Scala-Eingabeaufforderung zu schließen.

		:q

### <a name="standalone"></a>Verwenden eines eigenständigen Scala-Programms

In diesem Abschnitt schreiben wir eine Scala-Anwendung, die die Anzahl der Zeilen mit den Buchstaben 'a' and 'b' in einer Beispieldatendatei (/example/data/gutenberg/davinci.txt) zählt, die standardmäßig in HDInsight-Clustern verfügbar ist. Zum Schreiben und Verwenden eines eigenständigen Scala-Programms mit einem mit einer Spark-Installation angepassten Cluster müssen Sie die folgenden Schritte ausführen:

- Schreiben eines Scala-Programms
- Erstellen des Programms, um die JAR-Datei zu erhalten
- Ausführen des Auftrags im Cluster

#### Schreiben eines Scala-Programms
In diesem Abschnitt schreiben Sie ein Scala-Programm, das die Anzahl der Zeilen mit den Buchstaben 'a' and 'b' in einer Beispieldatendatei zählt. 

1. Öffnen Sie einen Text-Editor, und fügen Sie folgenden Code ein:


		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf
		
		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//location of sample data file on WASB
		    val conf = new SparkConf().setAppName("SimpleApplication")
		    val sc = new SparkContext(conf)
		    val logData = sc.textFile(logFile, 2).cache()
		    val numAs = logData.filter(line => line.contains("a")).count()
		    val numBs = logData.filter(line => line.contains("b")).count()
		    println("Lines with a: %s, Lines with b: %s".format(numAs, numBs))
		  }
		}

2. Speichern Sie die Datei mit dem Namen **SimpleApp.scala**.

#### Erstellen des Scala-Programms
In diesem Abschnitt verwenden Sie das <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">Simple Build Tool</a> (bzw. SBT) zum Erstellen des Scala-Programms. SBT erfordert mindestens Java 1.6. Vergewissern Sie sich vor dem Fortfahren, dass die richtige Java-Version installiert ist.

1. Laden Sie STB von "http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html" herunter, und installieren Sie das Tool.
2. Erstellen Sie den Ordner **SimpleScalaApp** und in diesem Ordner die Datei **simple.sbt**. Dies ist eine Konfigurationsdatei mit Informationen zur Scala-Version, zu Bibliotheksabhängigkeiten usw. Fügen Sie folgenden Code in simple.sbt ein, und speichern Sie die Datei.


		name := "SimpleApp"
	
		version := "1.0"
	
		scalaVersion := "2.10.4"
	
		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.0.2"



	>[AZURE.NOTE] Stellen Sie sich, dass Sie die leeren Zeilen in der Datei beibehalten.

	
3. Erstellen Sie unter dem Ordner **SimpleScalaApp** die Verzeichnisstruktur **\src\main\scala**, und fügen Sie das zuvor erstellte Scala-Programm (**SimpleApp.scala**) unter dem Ordner \src\main\scala ein.
4. Öffnen Sie eine Eingabeaufforderung, navigieren Sie zum Verzeichnis SimpleScalaApp, und geben Sie den folgenden Befehl ein:


		sbt package


	Nach der Kompilierung der Anwendung wird im Stammordner SimpleScalaApp unter **\target\scala-2.10** die Datei **simpleapp_2.10-1.0.jar** angezeigt.


#### Ausführen des Auftrags im Cluster
In diesem Abschnitt stellen Sie eine Remoteverbindung mit dem Cluster mit installiertem Spark her und kopieren anschließend den Zielordner des Projekts SimpleScalaApp. Danach übermitteln Sie den Auftrag mit dem Befehl **spark-submit** an den Cluster.

1. Stellen Sie eine Remoteverbindung mit dem Cluster mit installiertem Spark her. Kopieren Sie auf dem Computer, auf dem Sie das Programm SimpleApp.scala erstellt haben, den Ordner **SimpleScalaApp\target**, und fügen Sie ihn an einem Speicherort im Cluster ein.
2. Öffnen Sie in der RDP-Sitzung auf dem Desktop die Hadoop-Befehlszeile, und navigieren Sie zum Speicherort, dem Sie den Ordner **target** hinzugefügt haben.
3. Geben Sie den folgenden Befehl ein, um das Programm SimpleApp.scala auszuführen:


		C:\apps\dist\spark-1.0.2\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. Nach Ausführung des Programms wird die Ausgabe in der Konsole angezeigt.


		Lines with a: 21374, Lines with b: 11430

## <a name="usingPS"></a>Installieren von Spark in HDInsight Hadoop-Clustern mithilfe von PowerShell

In diesem Abschnitt wird das Cmdlet **<a href = "http://msdn.microsoft.com/library/dn858088.aspx" target="_blank">Add-AzureHDInsightScriptAction</a>** verwendet, um Skripts mithilfe der Skriptaktion aufzurufen, um ein Cluster anzupassen. Stellen Sie vor dem Fortfahren sicher, dass PowerShell installiert und konfiguriert ist. Weitere Informationen zum Konfigurieren einer Arbeitsstation für die Ausführung von HDInsight Powershell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure PowerShell][powershell-install-configure].

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
		$config = Add-AzureHDInsightScriptAction -Config $config -Name "Install Spark" -ClusterRoleCollection HeadNode -Uri https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1

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
	
4. Beginnen Sie mit der Bereitstellung eines angepassten Clusters mit installiertem Spark.  
	
		# START PROVISIONING A CLUSTER WITH SPARK INSTALLED
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version 

Geben Sie die Anmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden. Die Erstellung des Clusters kann einige Minuten in Anspruch nehmen.


## <a name="usingSDK"></a>Installieren von Spark in HDInsight Hadoop-Clustern mit dem .NET SDK

Das HDInsight .NET SDK enthält .NET-Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in .NET-Anwendungen. Dieser Abschnitt bietet Anweisungen zum Verwenden von "Skriptaktion" über das SDK, um einen Cluster bereitzustellen, in dem Spark installiert ist. Die folgenden Verfahren müssen ausgeführt werden:

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
	<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSparkCluster</td></tr>
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

10. Fügen Sie den folgenden Code an die "Main()"-Funktion an, sodass die [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx)-Klasse ein benutzerdefiniertes Skript zum Installieren von Spark aufruft.

		// ADD THE SCRIPT ACTION TO INSTALL SPARK
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Spark", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Spark on
          new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv02/spark-installer-v02.ps1"), // Location of the script to install Spark
		  null //because the script used does not require any parameters.
        ));

11. Erstellen Sie schließlich den Cluster.

		client.CreateCluster(clusterInfo);

11. Speichern Sie Änderungen an der Anwendung, und erstellen Sie die Lösung. 

**So führen Sie die Anwendung aus**

Öffnen Sie eine PowerShell-Konsole, wechseln Sie zum Speicherort, an dem Sie das Visual Studio-Projekt gespeichert haben, dann innerhalb des Projekts zum Verzeichnis "\bin\debug", und führen Sie den folgenden Befehl aus:

	.\CreateSparkCluster <cluster-name>

Geben Sie einen Clusternamen ein, und drücken Sie die EINGABETASTE zum Bereitstellen eines Clusters mit installiertem Spark.


## Weitere Informationen##
- Unter [Installieren und Verwenden von R für HDInsight-Cluster][hdinsight-install-r] finden Sie Anweisungen zum Verwenden der Clusteranpassung zum Installieren und Verwenden von R in HDInsight Hadoop-Clustern. R ist eine Open-Source-Sprache und -Umgebung für statistische Berechnungen. Sie bietet Hunderte integrierter Statistikfunktionen und eine eigene Programmiersprache, die Aspekte der funktionalen und objektorientierten Programmierung kombiniert. Darüber hinaus werden umfangreiche Grafikfunktionen geboten.
- [Installieren und Verwenden von Giraph in HDInsight-Clustern](../hdinsight-hadoop-giraph-install). Verwenden Sie die Clusteranpassung, um Giraph in HDInsight Hadoop-Clustern zu installieren. Giraph ermöglicht es Ihnen, mithilfe von Hadoop Graphverarbeitungen durchzuführen. Es kann zudem mit Azure HDInsight eingesetzt werden.
- [Installieren und Verwenden von Solr in HDInsight-Clustern](../hdinsight-hadoop-solr-install). Verwenden Sie die Clusteranpassung, um Solr in HDInsight Hadoop-Clustern zu installieren. Solr ermöglicht es Ihnen, leistungsstarke Suchvorgänge für gespeicherte Daten durchzuführen.





[hdinsight-provision]: ../hdinsight-provision-clusters/
[hdinsight-install-r]: ../hdinsight-hadoop-r-scripts/
[hdinsight-cluster-customize]: ../hdinsight-hadoop-customize-cluster
[powershell-install-configure]: ../install-configure-powershell/
\<!--HONumber=42-->
