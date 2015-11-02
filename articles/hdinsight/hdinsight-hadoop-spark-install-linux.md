<properties 
	pageTitle="Verwenden der Skriptaktion zum Installieren von Apache Spark unter Linux-basiertem HDInsight (Hadoop) | Microsoft Azure" 
	description="Erfahren Sie, wie Sie Spark auf einem Linux-basierten HDInsight-Cluster mit Skriptaktionen installieren. Mit Skriptaktionen können Sie den Cluster während der Erstellung anpassen, indem Sie die Clusterkonfiguration ändern oder Dienste und Hilfsprogramme installieren." 
	services="hdinsight" 
	documentationCenter="" 
	authors="Blackmist" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/19/2015" 
	ms.author="larryfr"/>

# Installieren und Verwenden von Spark in HDInsight-Clustern

In diesem Dokument wird beschrieben, wie Sie Spark mithilfe einer Skriptaktion installieren. Mit "Skriptaktion" können Sie nur beim Erstellen eines Clusters Skripts ausführen, um ein Cluster anzupassen. Weitere Informationen finden Sie unter [Anpassen eines HDInsight-Clusters mithilfe von Skriptaktion][hdinsight-cluster-customize]. Nach der Installation von Spark erfahren Sie, wie Sie eine Spark-Abfrage auf HDInsight-Cluster anwenden.

> [AZURE.NOTE]HDInsight verfügt auch über Spark als Clustertyp. Das bedeutet, dass Sie einen Spark-Cluster jetzt direkt bereitstellen können, ohne einen Hadoop-Cluster ändern zu müssen. Dies ist derzeit jedoch auf Windows-basierte Cluster beschränkt. Mit dem Spark-Clustertyp erhalten Sie einen Windows-basierten HDInsight-Cluster der Version 3.2 mit Spark-Version 1.3.1. Weitere Informationen finden Sie unter [Erste Schritte mit Apache Spark für HDInsight](hdinsight-apache-spark-zeppelin-notebook-jupyter-spark-sql.md).

## <a name="whatis"></a>Was ist Spark?

<a href="http://spark.apache.org/docs/latest/index.html" target="_blank">Apache Spark</a> ist ein Open-Source-Framework für die Parallelverarbeitung, das die arbeitsspeicherinterne Verarbeitung unterstützt, um die Leistung von Anwendungen zur Analyse von großen Datenmengen zu steigern. Dank seiner arbeitsspeicherinternen Datenverarbeitungsfunktionen eignet sich Spark besonders für iterative Algorithmen beim maschinellen Lernen und für Graphberechnungen.

Spark kann auch für die herkömmliche datenträgerbasierte Datenverarbeitung genutzt werden. Spark hat einen Vorteil gegenüber dem herkömmlichen MapReduce-Framework, da in den Zwischenstufen Schreibvorgänge auf den Datenträger vermieden werden. Außerdem ist Spark kompatibel mit dem Hadoop Distributed File System (HDFS) und Azure-Blob-Speicher, damit die vorhandenen Daten problemlos über Spark bearbeitet werden können.

In diesem Thema wird beschrieben, wie ein HDInsight-Cluster angepasst wird, indem Spark installiert wird.

## <a name="whatis"></a>Welche Version von Spark kann ich installieren?

In diesem Thema verwenden wir ein benutzerdefiniertes Skript für Aktion-Skript, um Spark auf einen HDInsight-Cluster zu installieren. Dieses Skript installiert Spark 1.5.1.

Sie können dieses Skript ändern oder ein eigenes Skript zum Installieren von anderen Versionen von Spark installieren.

## Funktion des Skripts

Dieses Skript installiert Spark 1.5.1 unter `/usr/hdp/current/spark`.

> [AZURE.WARNING]Vielleicht fällt Ihnen auf, dass einige Spark 1.3.1-Binärdateien standardmäßig in Ihrem HDInsight-Cluster installiert werden. Diese sollten nicht verwendet werden und werden im Rahmen eines zukünftigen Updates aus dem HDInsight-Clusterimage entfernt.

## <a name="install"></a>Installation von Spark mithilfe von Skriptaktionen

Ein Beispielskript zum Installieren von Spark in einem HDInsight-Cluster steht in einem schreibgeschützten Azure-Speicherblob unter [https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh](https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh) zur Verfügung. Dieser Abschnitt enthält Anweisungen zur Verwendung des Beispielskripts während der Erstellung des Clusters mithilfe des Azure-Portals.

> [AZURE.NOTE]Sie können auch Azure PowerShell oder das HDInsight .NET SDK zum Erstellen eines Clusters mit diesem Skript verwenden. Weitere Informationen zur Verwendung dieser Methoden finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen](hdinsight-hadoop-customize-cluster-linux.md).

1. Beginnen Sie die Erstellung eines Clusters anhand der Schritte in [Erstellen Linux-basierter HDInsight-Cluster](hdinsight-provision-linux-clusters.md#portal), aber schließen Sie sie nicht ab.

2. Wählen Sie auf dem Blatt **Optionale Konfiguration** die Option **Skriptaktionen**, und geben Sie die folgenden Informationen an:

	* __NAME__: Geben Sie einen Anzeigenamen für die Skriptaktion ein.
	* __SCRIPT URI__: https://hdiconfigactions.blob.core.windows.net/linuxsparkconfigactionv02/spark-installer-v02.sh
	* __HEAD__: Aktivieren Sie diese Option.
	* __WORKER__: Deaktivieren Sie diese Option.
	* __ZOOKEEPER__: Deaktivieren Sie diese Option.
	* __PARAMETERS__: Lassen Sie dieses Feld leer.
    
    > [AZURE.NOTE]Im Spark-Beispielskript werden Komponenten nur auf den Hauptknoten installiert, sodass die anderen Knotentypen deaktiviert bleiben können.

3. Verwenden Sie am unteren Rand der **Skriptaktionen** die Schaltfläche **Auswählen**, um die Konfiguration zu speichern. Verwenden Sie schließlich die Schaltfläche **Auswählen** am unteren Rand des Blatts **Optionale Konfiguration**, um die optionalen Konfigurationsinformationen zu speichern.

4. Setzen Sie die Bereitstellung des Clusters wie unter [Erstellen Linux-basierter HDInsight-Cluster](hdinsight-provision-linux-clusters.md#portal) beschrieben fort.

## <a name="usespark"></a>Wie wird Spark in HDInsight ausgeführt?

Spark stellt APIs in Scala, Python und Java bereit. Sie können für die Ausführung von Spark-Abfragen auch die interaktive Spark-Shell verwenden. Wenn die Clustererstellung abgeschlossen ist, stellen Sie mithilfe des folgenden Befehls eine Verbindung mit Ihrem HDInsight-Cluster her:

	ssh USERNAME@CLUSTERNAME-ssh.azurehdinsight.net
	
Weitere Informationen zur Verwendung von SSH mit HDInsight finden Sie in den folgenden Artikeln:

* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Linux, Unix oder OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

* [Verwenden von SSH mit Linux-basiertem Hadoop in HDInsight unter Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

Nachdem die Verbindung hergestellt wurde, führen Sie die spezifischen Schritte in den folgenden Abschnitten für die Verwendung von Spark aus:

- [Mithilfe der Spark-Shell interaktive Abfragen ausführen](#sparkshell)
- [Mithilfe der Spark-Shell Spark-SQL-Abfragen ausführen](#sparksql) 
- [Verwenden eines eigenständigen Scala-Programms](#standalone)

###<a name="sparkshell"></a>Mithilfe der Spark-Shell interaktive Abfragen ausführen

1. Führen Sie den folgenden Befehl aus, um die Spark-Shell zu starten:

		 /usr/hdp/current/spark/bin/spark-shell --master yarn

	Nach Ausführung des Befehls sollte eine Scala-Eingabeaufforderung angezeigt werden.

		 scala>

5. Geben Sie an der Scala-Eingabeaufforderung die nachstehende Spark-Abfrage ein. Mit dieser Abfrage wird das Vorkommen der einzelnen Wörter in der Datei "davinci.txt" gezählt, die im Ordner "/example/data/gutenberg/" im WASB-Speicher zur Verfügung steht, der dem Cluster zugeordnet ist.

		val file = sc.textFile("/example/data/gutenberg/davinci.txt")
		val counts = file.flatMap(line => line.split(" ")).map(word => (word, 1)).reduceByKey(_ + _)
		counts.toArray().foreach(println)

6. Die Ausgabe sollte wie folgt aussehen:

		(felt,,1)
		(axle-tree,1)
		(deals,9)
		(virtuous,4)

7. Geben Sie ":q", um die Scala-Eingabeaufforderung zu schließen.

		:q

###<a name="sparksql"></a>Ausführen von Spark-SQL-Abfragen mithilfe der Spark-Shell

Mit Spark SQL können Sie mit Spark relationale Abfragen (SQL = Structured Query Language), HiveQL oder Scala auszuführen. In diesem Abschnitt untersuchen wir, wie mit Spark eine Hive-Abfrage auf einer Hive-Beispieltabelle ausgeführt wird. Die in diesem Abschnitt verwendete Hive-Tabelle (mit dem Namen **hivesampletable**) ist standardmäßig verfügbar, wenn Sie einen Cluster erstellen.

1. Führen Sie den folgenden Befehl aus, um die Spark-Shell zu starten:

		 /usr/hdp/current/spark/bin/spark-shell --master yarn

	Nach Ausführung des Befehls sollte eine Scala-Eingabeaufforderung angezeigt werden.

		 scala>

2. Legen Sie bei der Scala-Eingabeaufforderung den Hive-Kontext fest. Dies ist erforderlich, um das Arbeiten mit Hive-Abfragen mithilfe von Spark zu ermöglichen.

		val hiveContext = new org.apache.spark.sql.hive.HiveContext(sc)

	> [AZURE.NOTE]Beachten Sie, dass `sc` der Spark-Standardkontext ist, der beim Starten der Spark-Shell festgelegt ist.

5. Führen Sie eine Hive-Abfrage mit dem Hive-Kontext aus, und drucken Sie die Ausgabe an die Konsole. Die Abfrage ruft Daten auf Geräten eines bestimmten Herstellers ab und beschränkt die Anzahl der abgerufenen Datensätze auf 20.

		hiveContext.sql("""SELECT * FROM hivesampletable WHERE devicemake LIKE "HTC%" LIMIT 20""").collect().foreach(println)

6. Folgendes sollte angezeigt werden:

		[820,11:35:17,de-DE,Android,HTC,Inspire 4G,Louisiana,UnitedStates, 2.7383836,0,1]
		[1055,17:24:08,de-DE,Android,HTC,Incredible,Ohio,United States,18.0894738,0,0]
		[1067,03:42:29,de-DE,Windows Phone,HTC,HD7,District Of Columbia,United States,null,0,0]

7. Geben Sie ":q", um die Scala-Eingabeaufforderung zu schließen.

		:q

### <a name="standalone"></a>Verwenden eines eigenständigen Scala-Programms

In diesem Abschnitt erstellen Sie eine Scala-Anwendung, die die Anzahl der Zeilen mit den Buchstaben „a“ und „b“ in einer Beispieldatendatei ((/example/data/gutenberg/davinci.txt) zählt.

1. Verwenden Sie die folgenden Befehle, um das Scala-Buildtool zu installieren:

		echo "deb http://dl.bintray.com/sbt/debian /" | sudo tee -a /etc/apt/sources.list.d/sbt.list
		sudo apt-get update
		sudo apt-get install sbt
		
	Wählen Sie bei entsprechender Aufforderung __Y__, um den Vorgang fortzusetzen.

2. Erstellen Sie die Verzeichnisstruktur für das Scala-Projekt:

		mkdir -p SimpleScalaApp/src/main/scala
		
3. Erstellen Sie eine neue Datei namens __simple.sbt__, die die Konfigurationsinformationen für dieses Projekt enthält.

		cd SimpleScalaApp
		nano simple.sbt
		
	Fügen Sie Folgendes als Inhalt der Datei hinzu:

		name := "SimpleApp"
	
		version := "1.0"
	
		scalaVersion := "2.10.4"
	
		libraryDependencies += "org.apache.spark" %% "spark-core" % "1.2.0"


	> [AZURE.NOTE]Stellen Sie sicher, dass Sie die leeren Zeilen zwischen den einzelnen Einträgen beibehalten.
	
	Drücken Sie zum Speichern der Datei __STRG+X__, __Y__ und dann __EINGABE__.

4. Verwenden Sie den folgenden Befehl zum Erstellen einer neuen Datei mit dem Namen __SimpleApp.scala__ im Verzeichnis __SimpleScalaApp/src/main/scala__:

		nano src/main/scala/SimpleApp.scala

	Fügen Sie Folgendes als Inhalt der Datei hinzu:

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

	Drücken Sie zum Speichern der Datei __STRG+X__, __Y__ und dann __EINGABE__.

5. Verwenden Sie im Verzeichnis __SimpleScalaApp__ den folgenden Befehl, um die Anwendung zu erstellen, und speichern Sie sie in einer JAR-Datei:

		sbt package

	Nach dem Kompilieren der Anwendung wird im Verzeichnis „\_\_SimpleScalaApp/target/scala-2.10**“ die Datei **simpleapp\_2.10-1.0.jar** angezeigt.

6. Geben Sie den folgenden Befehl ein, um das Programm „SimpleApp.scala“ auszuführen:


		/usr/hdp/current/spark/bin/spark-submit --class "SimpleApp" --master yarn target/scala-2.10/simpleapp_2.10-1.0.jar

4. Nach Ausführung des Programms wird die Ausgabe in der Konsole angezeigt.

		Lines with a: 21374, Lines with b: 11430

## Nächste Schritte

- [Installieren und Verwenden von Hue in HDInsight-Clustern](hdinsight-hadoop-hue-linux.md) Hue ist eine Webbenutzeroberfläche, die das Erstellen, Ausführen und Speichern von Pig- und Hive-Aufträgen sowie das Durchsuchen des Standardspeichers für Ihre HDInsight-Cluster vereinfacht.

- Unter [Installieren und Verwenden von R für HDInsight-Cluster][hdinsight-install-r] finden Sie Anweisungen bezüglich der Clusteranpassung zum Installieren und Verwenden von R in HDInsight Hadoop-Clustern. R ist eine Open-Source-Sprache und -Umgebung für statistische Berechnungen. Sie bietet Hunderte integrierter Statistikfunktionen und eine eigene Programmiersprache, die Aspekte der funktionalen und objektorientierten Programmierung kombiniert. Darüber hinaus werden umfangreiche Grafikfunktionen geboten.

- [Installieren von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install-linux.md). Verwenden Sie die Clusteranpassung, um Giraph in HDInsight Hadoop-Clustern zu installieren. Giraph ermöglicht Ihnen, mithilfe von Hadoop Graphen zu verarbeiten, und kann mit Azure HDInsight verwendet werden.

- [Installieren von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install-linux.md) Verwenden Sie die Clusteranpassung, um Solr in HDInsight Hadoop-Clustern zu installieren. Solr ermöglicht es Ihnen, leistungsstarke Suchvorgänge für gespeicherte Daten durchzuführen.

- [Installieren von Hue in HDInsight-Clustern](hdinsight-hadoop-hue-linux.md) Verwenden Sie die Clusteranpassung, um Hue in HDInsight Hadoop-Clustern zu installieren. Bei Hue handelt es sich um einen Satz von Webanwendungen zur Interaktion mit einem Hadoop-Cluster.



[hdinsight-provision]: hdinsight-provision-clusters-linux.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts-linux.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster-linux.md
[powershell-install-configure]: ../install-configure-powershell.md
 

<!---HONumber=Oct15_HO4-->