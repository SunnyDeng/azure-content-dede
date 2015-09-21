<properties
	pageTitle="Verwenden von Skriptaktionen zum Installieren von Solr in einem Hadoop-Cluster | Microsoft Azure"
	description="Erfahren Sie, wie Sie HDInsight-Cluster mit Spark anpassen. Sie verwenden eine Script Action-Konfigurationsoption, um mithilfe eines Skripts Spark zu installieren."
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
	ms.date="07/11/2015"
	ms.author="nitinme"/>

# Installieren und Verwenden von Spark in HDInsight-Clustern

Sie können Spark in einem beliebigen Clustertyp in Hadoop auf Azure HDInsight mithilfe der **Skriptaktion**-Clusteranpassung installieren. Mit einer Skriptaktion können Sie nur beim Erstellen eines Clusters Skripts ausführen, um einen Cluster anzupassen. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mithilfe von Skriptaktionen][hdinsight-cluster-customize].

In diesem Thema wird beschrieben, wie Sie Spark mithilfe der Funktion "Skriptaktion" installieren. Nach der Installation von Spark erfahren Sie, wie Sie eine Spark-Abfrage auf HDInsight-Cluster anwenden.

> [AZURE.IMPORTANT]HDInsight verfügt jetzt über Spark als erstklassigen Clustertyp für Windows-basierte Cluster. Dies bedeutet, dass Sie einen Spark-Cluster jetzt direkt bereitstellen können, ohne einen Hadoop-Cluster ändern zu müssen. Mit dem Spark-Clustertyp erhalten Sie einen HDInsight-Cluster der Version 3.2 mit Spark-Version 1.3.1. Weitere Informationen finden Sie unter [Erste Schritte mit Apache Spark für HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md).
>
> Informationen zur Verwendung von Spark mit einem Linux-basierten Cluster durch die Anpassung des Clusters finden Sie unter [Installieren Spark auf Linux-basierten HDInsight-Clustern](hdinsight-hadoop-spark-install-linux.md).

## <a name="whatis"></a>Was ist Spark?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> ist ein Open-Source-Framework für die Parallelverarbeitung, das die arbeitsspeicherinterne Verarbeitung unterstützt, um die Leistung von Anwendungen zur Analyse von großen Datenmengen zu steigern. Dank seiner arbeitsspeicherinternen Datenverarbeitungsfunktionen eignet sich Spark besonders für iterative Algorithmen beim maschinellen Lernen und für Graphberechnungen.

Spark kann auch für die herkömmliche datenträgerbasierte Datenverarbeitung genutzt werden. Spark hat einen Vorteil gegenüber dem herkömmlichen MapReduce-Framework, da in den Zwischenstufen Schreibvorgänge auf den Datenträger vermieden werden. Außerdem ist Spark kompatibel mit dem Hadoop Distributed File System (HDFS) und Azure-Blob-Speicher, damit die vorhandenen Daten problemlos über Spark bearbeitet werden können.

In diesem Thema wird beschrieben, wie ein HDInsight-Cluster angepasst wird, indem Spark installiert wird.

## <a name="whatis"></a>Welche Version von Spark kann ich installieren?

In diesem Thema verwenden wir ein benutzerdefiniertes Skript für Aktion-Skript, um Spark auf einen HDInsight-Cluster zu installieren. Mit diesem Skript kann Spark 1.2.0 oder Spark 1.0.2 abhängig von der Version des HDInsight-Clusters, die Sie bereitstellen, installiert werden.

- Wenn Sie das Skript bei der Bereitstellung eines **HDInsight 3.2**-Cluster verwenden, wird **Spark 1.2.0** installiert.
- Wenn Sie das Skript bei der Bereitstellung eines **HDInsight 3.1**-Cluster verwenden, wird **Spark 1.0.2** installiert.

Sie können dieses Skript ändern oder ein eigenes Skript zum Installieren von anderen Versionen von Spark installieren.


## <a name="install"></a>Wie installiere ich Spark?

Ein Beispielskript zum Installieren von Spark in einem HDInsight-Cluster steht in einem schreibgeschützten Azure-Speicher-Blob unter [https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1](https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1) zur Verfügung. Dieser Abschnitt enthält Anweisungen zur Verwendung des Beispielskripts während der Bereitstellung des Clusters mithilfe des Azure-Portals.

> [AZURE.NOTE]Das Beispielskript funktioniert nur mit HDInsight-Clustern der Version 3.1 und 3.2. Weitere Informationen zu HDInsight-Clusterversionen finden Sie unter [HDInsight-Clusterversionen](hdinsight-component-versioning.md).

1. Beginnen Sie die Bereitstellung eines Clusters mit der Option **BENUTZERDEFINIERT ERSTELLEN**, wie unter [Bereitstellen eines Clusters mit der benutzerdefinierten Erstellungsoption](hdinsight-provision-clusters.md#portal) beschrieben. Wählen Sie die Clusterversion abhängig von Folgendem:

	- Wenn Sie **Spark 1.2.0** installieren möchten, stellen Sie ein HDInsight 3.2-Cluster bereit.
	- Wenn Sie **Spark 1.0.2** installieren möchten, stellen Sie ein HDInsight 3.1-Cluster bereit.


2. Klicken Sie auf der Seite **Skriptaktionen** des Assistenten auf **Skriptaktion hinzufügen**, um wie unten gezeigt Details zur Skriptaktion bereitzustellen:

	![Anpassen eines Clusters mit "Skriptaktion"](./media/hdinsight-hadoop-spark-install/HDI.CustomProvision.Page6.png "Anpassen eines Clusters mit "Skriptaktion"")

	<table border='1'>
	<tr><th>Eigenschaft</th><th>Wert</th></tr>
	<tr><td>Name</td>
		<td>Geben Sie einen Namen für die Skriptaktion an. Z. B. <b>Spark installieren</b>.</td></tr>
	<tr><td>Skript-URI</td>
		<td>Geben Sie den Uniform Resource Identifier (URI) für das Skript an, das aufgerufen wird, um den Cluster anzupassen. Beispielsweise <i>https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1</i></td></tr>
	<tr><td>Knotentyp</td>
		<td>Gibt die Knoten an, auf denen das Anpassungsskript ausgeführt wird. Sie können <b>Alle Knoten</b>, <b>Nur Hauptknoten</b> oder <b>Nur Workerknoten</b> auswählen.
	<tr><td>Parameter</td>
		<td>Geben Sie die Parameter an, wenn dies für das Skript erforderlich ist. Für das Skript zum Installieren von Spark sind keine Parameter erforderlich, sodass Sie dieses Feld leer lassen können.</td></tr>
</table>Sie können dem Cluster mehr als eine Skriptaktion zum Installieren von mehreren Komponenten hinzufügen. Nachdem Sie die Skripts hinzugefügt haben, klicken Sie auf das Häkchen, um die Bereitstellung des Clusters zu starten.

Sie können das Skript auch zum Installieren von Giraph auf HDInsight mit Azure PowerShell oder dem HDInsight .NET SDK verwenden. Anweisungen zu diesen Verfahren finden Sie nachfolgend in diesem Thema.

## <a name="usespark"></a>Wie wird Spark in HDInsight ausgeführt?
Spark stellt APIs in Scala, Python und Java bereit. Sie können für die Ausführung von Spark-Abfragen auch die interaktive Spark-Shell verwenden. Dieser Abschnitt bietet eine Einführung in diese beiden Ansätze zum Arbeiten mit Spark:

- [Mithilfe der Spark-Shell interaktive Abfragen ausführen](#sparkshell)
- [Mithilfe der Spark-Shell Spark-SQL-Abfragen ausführen](#sparksql)
- [Verwenden eines eigenständigen Scala-Programms](#standalone)

###<a name="sparkshell"></a>Mithilfe der Spark-Shell interaktive Abfragen ausführen
Führen Sie die folgenden Schritte aus, um Spark-Abfragen über eine interaktive Spark-Shell auszuführen. In diesem Abschnitt führen wir eine Spark-Abfrage nach einer Beispieldatendatei (/example/data/gutenberg/davinci.txt) durch, die standardmäßig in HDInsight-Clustern verfügbar ist.

1. Aktivieren Sie im Azure-Portal Remotedesktop für den Cluster, den Sie mit installiertem Spark erstellt haben, und stellen Sie dann eine Remoteverbindung mit dem Cluster her. Anweisungen hierzu finden Sie unter <a href="http://azure.microsoft.com/documentation/articles/hdinsight-administer-use-management-portal/#rdp" target="_blank">Herstellen einer Verbindung mit HDInsight-Clustern mit RDP</a>.

2. Öffnen Sie in der RDP-Sitzung auf dem Desktop die Hadoop-Befehlszeile, und navigieren Sie zum Speicherort der Installation von Spark, z. B. **C:\\apps\\dist\\spark-1.2.0**.


3. Führen Sie den folgenden Befehl aus, um die Spark-Shell zu starten:

		 .\bin\spark-shell --master yarn

	Nach Ausführung des Befehls sollte eine Scala-Eingabeaufforderung angezeigt werden.

		 scala>

5. Geben Sie an der Scala-Eingabeaufforderung die nachstehende Spark-Abfrage ein. Mit dieser Abfrage wird das Vorkommen der einzelnen Wörter in der Datei "davinci.txt" gezählt, die im Ordner "/example/data/gutenberg/" im WASB-Speicher zur Verfügung steht, der dem Cluster zugeordnet ist.

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. Die Ausgabe sollte wie folgt aussehen:

	![Ausgabe der ausgeführten Scala interaktive Shell in einem HDInsight-Cluster](./media/hdinsight-hadoop-spark-install/hdi-scala-interactive.png)


7. Geben Sie ":q", um die Scala-Eingabeaufforderung zu schließen.

		:q

###<a name="sparksql"></a>Ausführen von Spark-SQL-Abfragen mithilfe der Spark-Shell

Mit Spark SQL können Sie mit Spark relationale Abfragen (SQL = Structured Query Language), HiveQL oder Scala auszuführen. In diesem Abschnitt untersuchen wir, wie mit Spark eine Hive-Abfrage auf einer Hive-Beispieltabelle ausgeführt wird. Die in diesem Abschnitt verwendete Hive-Tabelle (namens **Hivesampletable**) ist standardmäßig verfügbar, wenn Sie einen Cluster bereitstellen.

>[AZURE.NOTE]Das folgende Beispiel wurde gegen **Spark 1.2.0** erstellt, das installiert wird, wenn Sie die Skript-Aktion beim Bereitstellen von HDInsight 3.2 Cluster ausführen.

1. Aktivieren Sie im Azure-Portal Remotedesktop für den Cluster, den Sie mit installiertem Spark erstellt haben, und stellen Sie dann eine Remoteverbindung mit dem Cluster her. Anweisungen hierzu finden Sie unter </a>Herstellen einer Verbindung mit HDInsight-Clustern mit RDP.

2. Öffnen Sie in der RDP-Sitzung auf dem Desktop die Hadoop-Befehlszeile, und navigieren Sie zum Speicherort der Installation von Spark, z. B. **C:\\apps\\dist\\spark-1.2.0**.


3. Führen Sie den folgenden Befehl aus, um die Spark-Shell zu starten:

		 .\bin\spark-shell --master yarn

	Nach Ausführung des Befehls sollte eine Scala-Eingabeaufforderung angezeigt werden.

		 scala>

4. Legen Sie bei der Scala-Eingabeaufforderung den Hive-Kontext fest. Dies ist erforderlich, um das Arbeiten mit Hive-Abfragen mithilfe von Spark zu ermöglichen.

		val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

	Beachten Sie, dass **sc** der Spark-Standardkontext ist, der beim Starten von Shell-Spark festgelegt ist.

5. Führen Sie eine Hive-Abfrage mit dem Hive-Kontext aus, und drucken Sie die Ausgabe an die Konsole. Die Abfrage ruft Daten auf Geräten eines bestimmten Herstellers ab und beschränkt die Anzahl der abgerufenen Datensätze auf 20.

		hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. Folgendes sollte angezeigt werden:

	![Ausgabe der ausgeführten Spark SQL in einem HDInsight-Cluster](./media/hdinsight-hadoop-spark-install/hdi-spark-sql.png)

7. Geben Sie ":q", um die Scala-Eingabeaufforderung zu schließen.

		:q

### <a name="standalone"></a>Verwenden eines eigenständigen Scala-Programms

In diesem Abschnitt schreiben wir eine Scala-Anwendung, die die Anzahl der Zeilen mit den Buchstaben "a" und "b" in einer Beispieldatendatei (/example/data/gutenberg/davinci.txt) zählt, die standardmäßig in HDInsight-Clustern verfügbar ist. Zum Schreiben und Verwenden eines eigenständigen Scala-Programms mit einem mit einer Spark-Installation angepassten Cluster müssen Sie die folgenden Schritte ausführen:

- Schreiben eines Scala-Programms
- Erstellen des Scala-Programms zum Abrufen der JAR-Datei
- Ausführen des Auftrags im Cluster

#### Schreiben eines Scala-Programms
In diesem Abschnitt schreiben Sie ein Scala-Programm, das die Anzahl der Zeilen mit "a" und "b" in einer Beispieldatendatei zählt.

1. Öffnen Sie einen Text-Editor, und fügen Sie folgenden Code ein:


		/* SimpleApp.scala */
		import org.apache.spark.SparkContext
		import org.apache.spark.SparkContext._
		import org.apache.spark.SparkConf

		object SimpleApp {
		  def main(args: Array[String]) {
		    val logFile = "/example/data/gutenberg/davinci.txt"			//Location of the sample data file on Azure Blob storage
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
In diesem Abschnitt verwenden Sie das <a href="http://www.scala-sbt.org/0.13/docs/index.html" target="_blank">Simple Build Tool</a> (oder (SBT) zum Erstellen des Scala-Programms. SBT erfordert Java 1.6 oder höher, daher sollten Sie sicherstellen, dass die richtige Version von Java installiert ist, bevor Sie mit diesem Abschnitt fortfahren.

1. Installieren Sie SBT aus http://www.scala-sbt.org/0.13/tutorial/Installing-sbt-on-Windows.html.
2. Erstellen Sie den Ordner **SimpleScalaApp** und in diesem Ordner die Datei **simple.sbt**. Dies ist eine Konfigurationsdatei mit Informationen zur Scala-Version, zu Bibliotheksabhängigkeiten usw. Fügen Sie Folgendes in die Datei simple.sbt, und speichern Sie sie:


		name := "SimpleApp"

		version := "1.0"

		scalaVersion := "2.10.4"

		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"



	>[AZURE.NOTE]Stellen Sie sich, dass Sie die leeren Zeilen in der Datei beibehalten.


3. Erstellen Sie unter dem Ordner **SimpleScalaApp** die Verzeichnisstruktur **\\src\\main\\scala**, und fügen Sie das zuvor erstellte Scala-Programm (**SimpleApp.scala**) unter dem Ordner "\\src\\main\\scala" ein.
4. Öffnen Sie eine Eingabeaufforderung, navigieren Sie zum Verzeichnis SimpleScalaApp, und geben Sie den folgenden Befehl ein:


		sbt package


	Nach der Kompilierung der Anwendung wird im Stammordner SimpleScalaApp unter **\\target\\scala-2.10** die Datei **simpleapp\_2.10-1.0.jar** angezeigt.


#### Ausführen des Auftrags im Cluster
In diesem Abschnitt stellen Sie eine Remoteverbindung mit dem Cluster mit installiertem Spark her und kopieren anschließend den Zielordner des Projekts SimpleScalaApp. Danach übermitteln Sie den Auftrag mit dem Befehl **spark-submit** an den Cluster.

1. Stellen Sie eine Remoteverbindung mit dem Cluster mit installiertem Spark her. Kopieren Sie auf dem Computer, auf dem Sie das Programm SimpleApp.scala erstellt haben, den Ordner **SimpleScalaApp\\target**, und fügen Sie ihn an einen Speicherort im Cluster ein.
2. Öffnen Sie in der RDP-Sitzung auf dem Desktop die Hadoop-Befehlszeile, und navigieren Sie zum Speicherort, dem Sie den **Ziel**-Ordner hinzugefügt haben.
3. Geben Sie den folgenden Befehl ein, um das Programm SimpleApp.scala auszuführen:


		C:\apps\dist\spark-1.2.0\bin\spark-submit --class "SimpleApp" --master local target/scala-2.10/simpleapp_2.10-1.0.jar

4. Nach Ausführung des Programms wird die Ausgabe in der Konsole angezeigt.


		Lines with a: 21374, Lines with b: 11430

## <a name="usingPS"></a>Installieren von Spark in HDInsight Hadoop-Clustern mit Azure PowerShell

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

4. Beginnen Sie mit der Bereitstellung eines angepassten Clusters mit installiertem Spark.

		# Start provisioning a cluster with Spark installed
		New-AzureHDInsightCluster -Config $config -Name $clusterName -Location $location -Version $version

Geben Sie die Anmeldeinformationen für den Cluster ein, wenn Sie dazu aufgefordert werden. Die Erstellung des Clusters kann einige Minuten in Anspruch nehmen.


## <a name="usingSDK"></a>Installieren von Spark in HDInsight Hadoop-Clustern mit dem .NET SDK

Das HDInsight .NET SDK enthält .NET-Clientbibliotheken zur Vereinfachung der Arbeit mit HDInsight in einer .NET Framework-Anwendung. Dieser Abschnitt bietet Anweisungen zum Verwenden von "Skriptaktion" über das SDK, um einen Cluster bereitzustellen, in dem Spark installiert ist. Die folgenden Verfahren müssen ausgeführt werden:

- Installieren des HDInsight .NET SDK
- Erstellen eines selbstsignierten Zertifikats
- Erstellen einer Konsolenanwendung
- Ausführen der Anwendung


**So installieren Sie das HDInsight .NET SDK**

Sie können die neueste veröffentlichte Version des SDK von [NuGet](http://nuget.codeplex.com/wikipage?title=Getting%20Started) installieren. Die Anweisungen dazu werden im nächsten Verfahren erläutert.

**So erstellen Sie ein selbstsigniertes Zertifikat**

Erstellen Sie ein selbstsigniertes Zertifikat, installieren Sie es auf Ihrer Arbeitsstation und laden Sie es in Ihr Azure-Abonnement hoch. Weitere Hinweise hierzu finden Sie unter [Erstellen eines selbstsignierten Zertifikats](http://go.microsoft.com/fwlink/?LinkId=511138).


**So erstellen Sie eine Visual Studio-Anwendung**

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
<td style="border-color: #c6c6c6; border-width: 2px; border-style: solid; border-collapse: collapse; padding-left:5px;">CreateSparkCluster</td></tr>
</table>

4. Klicken Sie auf **OK**, um das Projekt zu erstellen.

5. Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager** und dann auf **Paket-Manager-Konsole**.

6. Führen Sie den folgenden Befehl in der Konsole aus, um das Paket zu installieren:

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

        // Provide values for the variables
        string thumbprint = "<CertificateThumbprint>";  
        string subscriptionId = "<AzureSubscriptionID>";
        string location = "<MicrosoftDataCenterLocation>";
        string storageaccountname = "<AzureStorageAccountName>.blob.core.windows.net";
        string storageaccountkey = "<AzureStorageAccountKey>";
        string username = "<HDInsightUsername>";
        string password = "<HDInsightUserPassword>";
        int clustersize = <NumberOfNodesInTheCluster>;

        // Provide the certificate thumbprint to retrieve the certificate from the certificate store
        X509Store store = new X509Store();
        store.Open(OpenFlags.ReadOnly);
        X509Certificate2 cert = store.Certificates.Cast<X509Certificate2>().First(item => item.Thumbprint == thumbprint);

        // Create an HDInsight client object
        HDInsightCertificateCredential creds = new HDInsightCertificateCredential(new Guid(subscriptionId), cert);
        var client = HDInsightClient.Connect(creds);
		client.IgnoreSslErrors = true;

        // Provide the cluster information
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
            Version = "3.2"
        };

10. Fügen Sie den folgenden Code an die "Main()"-Funktion an, sodass die [ScriptAction](http://msdn.microsoft.com/library/microsoft.windowsazure.management.hdinsight.clusterprovisioning.data.scriptaction.aspx)-Klasse ein benutzerdefiniertes Skript zum Installieren von Spark aufruft.

		// Add the script action to install Spark
        clusterInfo.ConfigActions.Add(new ScriptAction(
          "Install Spark", // Name of the config action
          new ClusterNodeType[] { ClusterNodeType.HeadNode }, // List of nodes to install Spark on
          new Uri("https://hdiconfigactions.blob.core.windows.net/sparkconfigactionv03/spark-installer-v03.ps1"), // Location of the script to install Spark.
		  null //Because the script used does not require any parameters
        ));

11. Erstellen Sie schließlich den Cluster.

		client.CreateCluster(clusterInfo);

11. Speichern Sie Änderungen an der Anwendung, und erstellen Sie die Lösung.

**So führen Sie die Anwendung aus**

Öffnen Sie eine Azure PowerShell-Konsole, wechseln Sie zum Speicherort, an dem Sie das Visual Studio-Projekt gespeichert haben, wechseln Sie innerhalb des Projekts zum Verzeichnis "\\bin\\debug", und führen Sie den folgenden Befehl aus:

	.\CreateSparkCluster <cluster-name>

Geben Sie einen Clusternamen ein, und drücken Sie die EINGABETASTE zum Bereitstellen eines Clusters mit installiertem Spark.


## Weitere Informationen##
- Unter [Installieren und Verwenden von R für HDInsight-Cluster][hdinsight-install-r] finden Sie Anweisungen bezüglich der Clusteranpassung zum Installieren und Verwenden von R in HDInsight Hadoop-Clustern. R ist eine Open-Source-Sprache und -Umgebung für statistische Berechnungen. Sie bietet Hunderte integrierter Statistikfunktionen und eine eigene Programmiersprache, die Aspekte der funktionalen und objektorientierten Programmierung kombiniert. Darüber hinaus werden umfangreiche Grafikfunktionen geboten.
- [Installieren von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install.md). Verwenden Sie die Clusteranpassung, um Giraph in HDInsight Hadoop-Clustern zu installieren. Giraph ermöglicht Ihnen, mithilfe von Hadoop Graphen zu verarbeiten, und kann mit Azure HDInsight verwendet werden.
- [Installieren von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install.md) Verwenden Sie die Clusteranpassung, um Solr in HDInsight Hadoop-Clustern zu installieren. Solr ermöglicht es Ihnen, leistungsstarke Suchvorgänge für gespeicherte Daten durchzuführen.





[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md
[powershell-install-configure]: ../install-configure-powershell.md

<!---HONumber=Sept15_HO2-->