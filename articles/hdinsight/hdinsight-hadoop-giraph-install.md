<properties
	pageTitle="Installieren und Verwenden von Giraph in Hadoop-Clustern in HDInsight | Microsoft Azure"
	description="Erfahren Sie, wie Sie HDInsight-Cluster mit Giraph anpassen. Sie verwenden die Konfigurationsoption ";Skriptaktion";, um Giraph mithilfe eines Skripts zu installieren."
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
	ms.date="07/11/2015"
	ms.author="nitinme"/>

# Installieren von Giraph in HDInsight Hadoop-Clustern und Verwenden von Giraph zur Verarbeitung großer Diagramme

Sie können Giraph in einem beliebigen Clustertyp in Hadoop in Azure HDInsight mithilfe der Clusteranpassung über **Skriptaktion** installieren. Mit "Skriptaktion" können Sie nur beim Erstellen eines Clusters Skripts ausführen, um ein Cluster anzupassen. Weitere Informationen finden Sie unter [Anpassen von HDInsight-Clustern mithilfe von Skriptaktionen][hdinsight-cluster-customize].

> [AZURE.NOTE]Die Informationen in diesem Artikel gelten für Windows-basierte HDInsight-Cluster. Informationen zum Arbeiten mit Linux-basierten Clustern finden Sie unter [Installieren von Giraph in HDInsight Hadoop-Clustern (Linux)](hdinsight-hadoop-giraph-install-linux.md).

In diesem Thema wird beschrieben, wie Sie Giraph mithilfe der Funktion "Skriptaktion" installieren. Nach der Installation von Giraph erfahren Sie auch, wie Sie Giraph bei den typischsten Anwendungen verwenden können, d. h. bei der Verarbeitung großer Graphen.

[AZURE.INCLUDE [hdinsight-azure-preview-portal](../../includes/hdinsight-azure-preview-portal.md)]

* [Installieren von Giraph in HDInsight-Clustern](hdinsight-hadoop-giraph-install-v1.md)

## <a name="whatis"></a>Was ist Giraph?

<a href="http://giraph.apache.org/" target="_blank">Apache Giraph</a> ermöglicht die Graphverarbeitung mit Hadoop und lässt sich mit Azure HDInsight nutzen. Graphen bilden Beziehungen zwischen Objekten ab, wie z. B. Verbindungen zwischen Routern in einem großen Netzwerk wie dem Internet oder Beziehungen zwischen Menschen in sozialen Netzwerken (mitunter als "Social Graph" bezeichnet). Mit der Graphverarbeitung können Sie sich Gedanken über die Beziehungen zwischen den Objekten im Diagramm machen wie etwa:

- Ermitteln potenzieller Freunde aufgrund Ihrer aktuellen Beziehungen.
- Ermitteln der kürzesten Route zwischen zwei Computern in einem Netzwerk.
- Berechnen des Seitenrangs von Webseiten.


## <a name="install"></a>Wie installiere ich Giraph?

Ein Beispielskript zum Installieren von Giraph in einem HDInsight-Cluster steht in einem schreibgeschützten Azure-Speicherblob unter [https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1](https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1) zur Verfügung. Dieser Abschnitt enthält Anweisungen zur Verwendung des Beispielskripts während der Bereitstellung des Clusters mithilfe des Azure-Portals.

> [AZURE.NOTE]Das Beispielskript funktioniert nur mit HDInsight-Clustern der Version 3.1. Weitere Informationen zu HDInsight-Clusterversionen finden Sie unter [HDInsight-Clusterversionen](hdinsight-component-versioning.md).

1. Beginnen Sie die Bereitstellung eines Clusters mit der Option **BENUTZERDEFINIERT ERSTELLEN**, wie unter [Bereitstellen eines Clusters mit der benutzerdefinierten Erstellungsoption](hdinsight-provision-clusters.md#portal) beschrieben.
2. Klicken Sie auf der Seite **Skriptaktionen** des Assistenten auf **Skriptaktion hinzufügen**, um wie unten gezeigt Details zur Skriptaktion bereitzustellen:

	![Anpassen eines Clusters mit "Skriptaktion"](./media/hdinsight-hadoop-giraph-install/hdi-script-action-giraph.png "Anpassen eines Clusters mit "Skriptaktion"")

	<table border='1'>
	<tr><th>Eigenschaft</th><th>Wert</th></tr>
	<tr><td>Name</td>
		<td>Geben Sie einen Namen für die Skriptaktion an. Beispiel: <b>Giraph installieren</b>.</td></tr>
	<tr><td>Skript-URI</td>
		<td>Geben Sie den Uniform Resource Identifier (URI) für das Skript an, das aufgerufen wird, um den Cluster anzupassen. Beispiel: <i>https://hdiconfigactions.blob.core.windows.net/giraphconfigactionv01/giraph-installer-v01.ps1</i></td></tr>
	<tr><td>Knotentyp</td>
		<td>Gibt die Knoten an, auf denen das Anpassungsskript ausgeführt wird. Sie können <b>Alle Knoten</b>, <b>Nur Hauptknoten</b> oder <b>Nur Workerknoten</b> auswählen.
	<tr><td>Parameter</td>
		<td>Geben Sie die Parameter an, wenn dies für das Skript erforderlich ist. Für das Skript zum Installieren von Giraph sind keine Parameter erforderlich, sodass Sie diese leer lassen können.</td></tr>
</table>Sie können dem Cluster mehr als eine Skriptaktion zum Installieren von mehreren Komponenten hinzufügen. Nachdem Sie die Skripts hinzugefügt haben, klicken Sie auf das Häkchen, um die Bereitstellung des Clusters zu starten.

## <a name="usegiraph"></a>Wie verwende ich Giraph in HDInsight?

Das Beispiel "SimpleShortestPathsComputation" demonstriert die grundlegende <a href = "http://people.apache.org/~edwardyoon/documents/pregel.pdf">Pregel</a>-Implementierung für das Auffinden des kürzesten Pfads zwischen Objekten in einem Graph. Mit den folgenden Schritten können Sie die Beispieldaten und das Beispiel-Jar hochladen, einen Auftrag mithilfe des Beispiels "SimpleShortestPathsComputation" ausführen und dann die Ergebnisse anzeigen.

1. Laden Sie eine Beispieldatendatei in den Azure-Blobspeicher hoch. Erstellen Sie auf der lokalen Arbeitsstation eine neue Datei namens **tiny\_graph.txt**. Sie sollte folgende Zeilen enthalten:

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Laden Sie die Datei "tiny\_graph.txt" in den Primärspeicher Ihres HDInsight-Clusters hoch. Anweisungen zum Hochladen von Daten finden Sie unter [Hochladen von Daten für Hadoop-Aufträge in HDInsight](hdinsight-upload-data.md).

	Diese Daten beschreiben eine Beziehung zwischen Objekten in einem gerichteten Graph mithilfe des Formats [source\_id, source\_value,[[dest\_id], [edge\_value],...]]. Jede Zeile repräsentiert eine Beziehung zwischen einem **source\\_id**-Objekt und einem oder mehreren **dest\\_id**-Objekten. Der **edge\\_value** (bzw. die Gewichtung) ist vorstellbar als die Stärke oder Distanz der Verbindung zwischen **source\_id** und **dest\\_id**.

	Wenn die obigen Daten auseinandergezogen und der Wert (die Gewichtung) als Abstand zwischen den Objekten verwendet werden, dann könnte das so aussehen:

	![tiny\_graph.txt als Kreise dargestellt mit Linien unterschiedlicher Länge dazwischen](./media/hdinsight-hadoop-giraph-install/giraph-graph.png)



4. Führen Sie das Beispiel "SimpleShortstPathsComputation" aus. Verwenden Sie die folgenden Azure PowerShell-Cmdlets, um das Beispiel unter Verwendung der Datei "tiny\_graph.txt" als Eingabe auszuführen. Das macht es erforderlich, dass [Azure PowerShell][powershell-install] installiert und konfiguriert wurde.

		$clusterName = "clustername"
		# Giraph examples jar
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

		# Run the job, write output to the Azure PowerShell window
		$job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
		Write-Host "Wait for the job to complete ..." -ForegroundColor Green
		Wait-AzureHDInsightJob -Job $job
		Write-Host "STDERR"
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
		Write-Host "Display the standard output ..." -ForegroundColor Green
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

	Ersetzen Sie im Beispiel oben **clustername** durch den Namen Ihres HDInsight-Clusters, in dem Giraph installiert ist.

5. Zeigen Sie die Ergebnisse an. Nach Abschluss des Auftrags werden die Ergebnisse in zwei Ausgabedateien im Ordner "\_\___wasb:///example/out/shotestpaths__" gespeichert. Die Dateien heißen __part-m-00001__ und __part-m-00002__. Führen Sie die folgenden Schritte aus, um die Ausgabe herunterzuladen und anzuzeigen:

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure Storage account name
		$containerName = "<ContainerName>"             # Blob storage container name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the Storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00001 -Context $storageContext -Force
		Get-AzureStorageBlobContent -Container $containerName -Blob example/output/shortestpaths/part-m-00002 -Context $storageContext -Force

	Damit wird die Verzeichnisstruktur __example/output/shortestpaths__ im aktuellen Verzeichnis auf der Arbeitsstation erstellt, und die beiden Ausgabedateien werden in diesen Speicherort heruntergeladen.

	Verwenden Sie das Cmdlet __Cat__, um den Inhalt der Dateien anzuzeigen:

		Cat example/output/shortestpaths/part*

	Die Ausgabe sollte in etwa folgendermaßen aussehen:


		0	1.0
		4	5.0
		2	2.0
		1	0.0
		3	1.0

	Das Beispiel "SimpleShortestPathComputation" wurde hartcodiert und beginnt mit der Objekt-ID 1. Es findet den kürzesten Pfad zu anderen Objekten. Die Ausgabe sollte also `destination_id distance` lauten, wobei der Abstand der Wert (oder das Gewicht) der Kanten ist, der zwischen Objekt-ID 1 und der Ziel-ID zurückgelegt wird.

	Wenn Sie dies visualisieren, können Sie die Ergebnisse überprüfen, indem Sie den kürzesten Weg zwischen ID 1 und allen anderen Objekten zurücklegen. Beachten Sie, dass 5 der kürzeste Pfad zwischen ID 1 und ID 4 ist. Dies ist die gesamte Entfernung zwischen <span style="color:orange">ID 1 und 3</span> und dann zwischen <span style="color:red">ID 3 und 4</span>.

	![Zeichnen von Objekten als Kreise mit dem kürzesten Pfad dazwischen](./media/hdinsight-hadoop-giraph-install/giraph-graph-out.png)



## Weitere Informationen##
- Unter [Installieren und Verwenden von Spark für HDInsight-Cluster][hdinsight-install-spark] finden Sie Anweisungen zum Verwenden der Clusteranpassung zum Installieren und Verwenden von Spark für HDInsight Hadoop-Cluster. Spark ist ein Open-Source-Framework für die Parallelverarbeitung, das die arbeitsspeicherinterne Verarbeitung unterstützt, um die Leistung von Anwendungen zur Analyse von Big Data zu steigern.
- Unter [Installieren und Verwenden von R für HDInsight-Cluster][hdinsight-install-r] finden Sie Anweisungen zum Verwenden der Clusteranpassung zum Installieren und Verwenden von R in HDInsight Hadoop-Clustern. R ist eine Open-Source-Sprache und -Umgebung für statistische Berechnungen. Sie bietet Hunderte integrierter Statistikfunktionen und eine eigene Programmiersprache, die Aspekte der funktionalen und objektorientierten Programmierung kombiniert. Darüber hinaus werden umfangreiche Grafikfunktionen geboten.
- [Installieren von Solr in HDInsight-Clustern](hdinsight-hadoop-solr-install.md). Verwenden Sie die Clusteranpassung, um Solr in HDInsight Hadoop-Clustern zu installieren. Solr ermöglicht es Ihnen, leistungsstarke Suchvorgänge für gespeicherte Daten durchzuführen.



[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/

[powershell-install]: ../powershell-install-configure.md
[hdinsight-provision]: hdinsight-provision-clusters.md
[hdinsight-install-r]: hdinsight-hadoop-r-scripts.md
[hdinsight-install-spark]: hdinsight-hadoop-spark-install.md
[hdinsight-cluster-customize]: hdinsight-hadoop-customize-cluster.md

<!---HONumber=Sept15_HO2-->