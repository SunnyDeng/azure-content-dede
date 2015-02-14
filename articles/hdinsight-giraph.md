<properties 
	pageTitle="Verwenden von Apache Giraph mit Azure HDInsight" 
	description="Erfahren Sie, wie Sie Apache Giraph für die Graphverarbeitung mit Azure HDInsight verwenden können" 
	services="hdinsight" 
	documentationCenter="" 
	authors="blackmist" 
	manager="paulettm" 
	editor=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/14/2014" 
	ms.author="larryfr"/>

#So setzen Sie Apache Giraph mit Azure HDInsight (Hadoop) ein

[Apache Giraph][giraph] ermöglicht es Ihnen, mithilfe von Hadoop Graphverarbeitung durchzuführen. Es kann zudem mit Azure HDInsight eingesetzt werden. 

Graphen (Diagramme) modellieren Beziehungen zwischen Objekten wie beispielsweise Verbindungen zwischen Routern in einem großen Netzwerk wie dem Internet oder Beziehungen zwischen Menschen in sozialen Netzwerken (manchmal als "Social Graph" bezeichnet). Mit der Graphverarbeitung können Sie sich Gedanken über die Beziehungen zwischen den Objekten im Diagramm machen wie etwa:

* Feststellen potenzieller Freunde aufgrund der aktuellen Beziehungen

* Feststellen der kürzesten Route zwischen zwei Computern in einem Netzwerk

* Berechnen des Seitenrangs von Websites

##Sie erhalten Informationen zu folgenden Themen

* [Entwickeln und Bereitstellen von Apache Giraph in einem HDInsight-Cluster](#build)

* [Ausführen des Beispiels "SimpleShortestPathsComputation"](#run)

 Eine Liste anderer mit Giraph bereitgestellter Beispiele finden Sie unter [Package org.apache.giraph.examples](https://giraph.apache.org/apidocs/org/apache/giraph/examples/package-summary.html).

* [Behandeln von Problemen, die auftreten können](#tshoot)

##Anforderungen

* Ein Azure HDInsight-Cluster, Version 3.0 oder 3.1

* [Git](http://git-scm.com/)

* Java 1.6

* [Maven](http://maven.apache.org/) 3 oder höher

##<a id="build"></a>Erstellen und Bereitstellen von Giraph

Giraph wird nicht als Teil des HDInsight-Clusters bereitgestellt. Es muss also von Grund auf erstellt werden.  Weitere Informationen zum Erstellen von Giraph finden Sie im [Giraph Repository](https://github.com/apache/giraph).

1. Aktuell (14.07.2014) erfordert Giraph einen Patch, damit es mit dem WASB-Dateispeicher arbeitet, der von HDInsight verwendet wird. Der Patch wurde beim Apache Giraph-Projekt eingereicht, bisher aber noch nicht akzeptiert. Laden Sie den Patch im Abschnitt __attachments__ von [GIRAPH-930](https://issues.apache.org/jira/browse/GIRAPH-930) herunter, und speichern Sie ihn auf dem lokalen Laufwerk unter dem Namen __giraph-930.diff__.

1. Verwenden Sie in der Befehlszeile den folgenden Git-Befehl und erstellen einen Klon des Giraph Repository.

		git clone https://github.com/apache/giraph.git

2. Ändern Sie die Verzeichnisse in das Verzeichnis __giraph__, das im Klonvorgang in Schritt 2 erstellt wurde.

		cd giraph

3. Führen Sie den Patch mithilfe des folgenden Befehls mit dem lokalen Repository zusammen.

		git apply giraph-930.diff

	Ersetzen Sie __giraph-930.diff__ durch den Pfad zu der Datei, die Sie in Schritt 1 erstellt haben.

3. Erstellen Sie Giraph für Ihre HDInsight-Clusterversion mithilfe eines der folgenden Befehle.

	* Für __HDInsight 3.0__ (Hadoop 2.2)

			mvn package -Phadoop_0.20.203 - DskipTests

	* Für __HDInsight 3.1__ (Hadoop 2.4)

			mvn package -Phadoop_0.23 -DskipTests

	Nachdem der Build fertig ist, finden Sie die Beispiels-JAR-Datei unter __\\giraph\\giraph-examples\\target__.

4. Laden Sie die Beispiel-JAR-Datei mithilfe von [Azure PowerShell][aps] und den [HDInsight-Tools][tools] in den Hauptspeicher Ihres HDInsight-Clusters hoch.

		Add-HDInsightFile giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar example/jars/giraph.jar clustername

	Ersetzen Sie __giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar__ durch den Pfad und Namen der JAR-Datei, die im vorherigen Schritt erstellt wurde, und __clustername__ durch den Namen Ihres HDInsight-Clusters. Wenn Sie das Paket beispielsweise mit dem Parameter `-Phadoop_0.20.203` erstellen, enthält der Name des JAR __hadoop-0.20.203__.

	Ist der Befehl abgeschlossen, dann wurde die JAR-Datei auf wasb:///example/jars/giraph.jar hochgeladen.

	> [AZURE.NOTE] Eine Liste der Hilfsprogramme, die für das Hochladen von Dateien in HDInsight verwendet werden können, finden Sie unter [Hochladen von Daten für Hadoop-Aufträge in HDInsight](http://azure.microsoft.com/de-de/documentation/articles/hdinsight-upload-data/)

##<a id="run"></a>Ausführen des Beispiels

SimpleShortestPathsComputation demonstriert die grundlegende [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf)-Implementierung für das Suchen des kürzesten Pfads zwischen Objekten in einem Diagramm. Mit den folgenden Schritten können die Beispieldaten hochladen, einen Auftrag mithilfe des Beispiels SimpleShortestPathsComputation ausführen und dann die Ergebnisse anzeigen.

> [AZURE.NOTE] Die Quelle dafür und für andere Beispiele ist im [release-1.1 branch](https://github.com/apache/giraph/tree/release-1.1) im [GitHub repository](https://github.com/apache/giraph) verfügbar.

1. Erstellen Sie eine neue Datei namens __tiny\_graph.txt__. Sie sollte folgende Zeilen enthalten.

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Diese Daten beschreiben die Beziehung zwischen Objekten in einem [gerichteten Diagramm](http://en.wikipedia.org/wiki/Directed_graph) unter Verwendung des Formats `[source_id,source_value,[[dest_id], [edge_value],...]]`. Jede Zeile repräsentiert eine Beziehung zwischen einer __source\_id__ und einem oder mehreren __dest\_id__-Objekten. Der __edge\_value__ (oder die Gewichtung) ist vorstellbar als die Stärke oder Distanz der Verbindung zwischen __source\_id__ und __dest\_id__. 

	Wenn man die obigen Daten auseinanderzieht und den Wert (oder das Gewicht) als den Abstand zwischen den Objekten verwendet, dann könnte das so aussehen.

	![tiny_graph.txt drawn as circles with lines of varying distance between](.\media\hdinsight-giraph\giraph-graph.png)

2. Laden Sie die Datei __tiny\_graph.txt__ mithilfe von [Azure PowerShell][aps] und den [HDInsight-Tools][tools] in den Hauptspeicher Ihres HDInsight-Clusters hoch.

		Add-HDInsightFile tiny_graph.txt example/data/tiny_graph.txt clustername

	Ersetzen Sie den "clustername" durch den Namen Ihres HDInsight-Clusters.

3. Verwenden Sie die folgende PowerShell zum Ausführen des Beispiels __SimpleShortstPathsComputation__ mit der Datei __tiny\_graph.txt__ als Eingabe. Dazu muss [Azure PowerShell][aps] installiert und konfiguriert sein.

		$clusterName = "clustername"
		# Giraph examples JAR
		$jarFile = "wasb:///example/jars/giraph.jar"
		# Arguments for this job
		$jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation", `
		                "-ca", "mapred.job.tracker=headnodehost:9010", `
		                "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat", `
		                "-vip", "wasb:///example/data/tinygraph.txt", `
		                "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat", `
		                "-op",  "wasb:///example/output/shortestpaths", `
		                "-w", "2"
		# Create the definition
		$jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
		  -JarFile $jarFile `
		  -ClassName "org.apache.giraph.GiraphRunner" `
		  -Arguments $jobArguments
		
		# Run the job, write output to the PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	Ersetzen Sie im Beispiel oben __clustername__ durch den Namen Ihres HDInsight-Clusters.

###Anzeigen der Ergebnisse

Nachdem der Auftrag abgeschlossen ist, werden die Ergebnisse im Ordner __wasb:///example/out/shortestpaths als part-m-#####__-Dateien gespeichert. Verwenden Sie [Azure PowerShell][aps] und die [HDInsight-Tools][tools] zum Herunterladen der Ausgabedateien.

	Find-HDInsightFile example/output/shortestpaths/part* clustername | foreach-object { Get-HDInsightFile $_.name .  itsfullofstorage }
	Cat example/output/shortestpaths/part*

Damit wird die Verzeichnisstruktur __example/output/shortestpaths__ im aktuellen Verzeichnis erstellt, und die Dateien, die mit __part__ beginnen, werden heruntergeladen. Das Cmdlet __Cat__ zeigt dann den Inhalt der Dateien an, der ähnlich wie das Folgende aussehen sollte.

	0	1.0
	4	5.0
	2	2.0
	1	0.0
	3	1.0

Das Beispiel SimpleShortestPathComputation wurde fest programmiert und beginnt mit der Objekt-ID 1. Es findet den kürzesten Pfad zu anderen Objekten. Die Ausgabe sollte daher als `destination_id distance` gelesen werden, wobei der Abstand der Wert (oder das Gewicht) der Kanten ist, der zwischen Objekt-ID 1 und der Ziel-ID zurückgelegt wird.

Wenn Sie dies visualisieren, können Sie die Ergebnisse überprüfen, indem Sie den kürzesten Weg zwischen ID 1 und allen anderen Objekten zurücklegen. Beachten Sie, dass 5 der kürzeste Pfad zwischen ID 1 und ID 4 ist. Dies ist die Gesamtentfernung zwischen <span style="color:orange">ID 1 und 3</span>und dann <span style="color:red">ID 3 und 4</span>.

![Drawing of objects as circles with shortest paths drawn between](.\media\hdinsight-giraph\giraph-graph-out.png)

##<a id="tshoot"></a>Problembehandlung

###Das Ausgabeverzeichnis existiert bereits

Giraph-Aufträge erstellen während der Laufzeit das festgelegte Ausgabeverzeichnis. Wenn das Verzeichnis bereits vorhanden ist, tritt ein Fehler auf der besagt, dass das Ausgabeverzeichnis schon existiert.

Wenn Sie einen Auftrag mehrmals ausführen möchten, müssen Sie entweder zwischen den Aufträgen das Ausgabeverzeichnis entfernen oder Sie legen für jeden Auftrag ein anderes Ausgabeverzeichnis fest.

###<a id="cmd"></a>Verwenden der Hadoop-Befehlszeile

Dieser Artikel zeigt, wie Sie einen Giraph-Auftrag mit PowerShell ausführen. Sie können das aber auch mit der Hadoop-Befehlszeile tun.

> [AZURE.NOTE] Die Hadoop-Befehlszeile steht nur dann zur Verfügung, wenn über Remotedesktop eine Verbindung zum HDInsight-Cluster aufgebaut wird.
> 
> Remotedesktop-Sitzungen mit Azure-Rechnerressourcen wie dem HDInsight-Cluster funktionieren möglicherweise nur von Windows-basierten Remotedesktop-Clients.

Führen Sie für die Verbindung zum HDInsight-Cluster folgende Schritte aus:

1. Wählen Sie mit dem [Azure-Verwaltungsportal](https://manage.windowsazure.com) den HDInsight-Cluster und dann __Konfiguration__ aus.

2. Wählen Sie unten auf der Seite __Remote aktivieren__ aus, und geben Sie dann den Benutzernamen, das Kennwort und das Ablaufdatum für die Remotedesktopverbindung ein.

3. Nachdem die Anfrage zum Aktivieren von Remotedesktop verarbeitet wurde, wird unten auf der Seite ein neuer Eintrag bei __Verbinden__ angezeigt. Wählen Sie diesen für das Herunterladen der .RDP-Datei für die Remotedesktop-Sitzung.

4. Die .RDP-Datei lässt sich speichern oder sofort zum Start des Remotedesktop-Client öffnen. Während des Verbindungsprozesses werden Sie aufgefordert, den Benutzernamen und das Kennwort einzugeben, die Sie beim Aktivieren der Remotedesktopverbindung gewählt haben.

5. Nachdem die Verbindung hergestellt wurde, verwenden Sie das Symbol __Hadoop-Befehlszeile__ auf dem Desktop, um diese zu starten.

6. Das folgende Beispiel veranschaulicht, wie Sie die Datei __giraph.jar__ in den Hauptknoten des Clusters kopieren und dann den Auftrag mithilfe der Hadoop-Befehlszeile ausführen.

		hadoop fs -copyToLocal wasb:///example/jar/giraph.jar
		hadoop jar giraph.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca "mapred.job.tracker=headnodehost:9010" -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip wasb:///example/data/tinygraph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op wasb:///example/output/shortestpaths -w 2


###Ältere Versionen von HDInsight

Wenn Sie Giraph mit einer älteren Version von HDInsight verwenden möchten, müssen Sie es für die spezifische Hadoop-Version kompilieren, die von dieser Version unterstützt wird. Unter [Neuheiten in den von HDInsight bereitgestellten Clusterversionen](http://azure.microsoft.com/de-de/documentation/articles/hdinsight-component-versioning/) können Sie bestimmen, welche Hadoop-Version zu Ihrer Version von HDInsight passt.

Ältere Versionen von HDInsight können es außerdem erforderlich machen, dass der Giraph-Auftrag von der Befehlszeile aus ausgeführt wird. Wenn Sie beim Ausführen des Auftrags von PowerShell aus Fehler erhalten, versuchen Sie, diesen über die [Hadoop-Befehlszeile] auszuführen(#cmd).

##Nächste Schritte

Nachdem Sie nun wissen, wie Sie Giraph mit HDInsight verwenden, versuchen Sie [Pig][] und [Hive][] mit HDInsight.

[giraph]: http://giraph.apache.org
[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/de-de/documentation/articles/install-configure-powershell/
[pig]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-use-pig/
[hive]: http://azure.microsoft.com/de-de/documentation/articles/hdinsight-use-hive/
<!--HONumber=42-->
