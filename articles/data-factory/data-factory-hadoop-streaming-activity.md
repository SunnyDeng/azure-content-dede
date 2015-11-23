<properties 
	pageTitle="Hadoop-Streamingaktivität" 
	description="Erfahren Sie, wie Sie die Hadoop-Streamingaktivität in Azure Data Factory verwenden können, um Hadoop-Streamingprogramme in einem bedarfsgesteuerten oder einem eigenen HDInsight-Cluster auszuführen." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="11/09/2015" 
	ms.author="spelluru"/>

# Hadoop-Streamingaktivität
Mit der Aktivität „HDInsightStreamingActivity“ können Sie einen Hadoop-Streamingauftrag über eine Azure Data Factory-Pipeline aufrufen. Der folgende JSON-Codeausschnitt zeigt die Syntax für die Verwendung von HDInsightStreamingActivity in einer JSON-Pipelinedatei.

Die HDInsight-Streamingaktivität in einer [Data Factory-Pipeline](data-factory-create-pipelines.md) führt Hadoop-Streamingprogramme in [eigenen](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) oder [bedarfsgesteuerten](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-basierten HDInsight-Clustern aus. Dieser Artikel baut auf dem Artikel zu [Datentransformationsaktivitäten](data-factory-data-transformation-activities.md) auf, der eine allgemeine Übersicht über die Datentransformation und die unterstützten Transformationsaktivitäten bietet.

## Beispiel

	{
	    "name": "HadoopStreamingPipeline",
	    "properties":
	    {
	        "description" : "Hadoop Streaming Demo",
	        "activities":
	        [
	           {
	               "name": "RunHadoopStreamingJob",
	               "description": "Run a Hadoop streaming job",
	               "type": "HDInsightStreaming",
	               "inputs": [ ],
	               "outputs": [ {"name": "OutputTable"} ],
	               "linkedServiceName": "HDInsightLinkedService",
	               "typeProperties":
	               {
	                   "mapper": "cat.exe",
	                   "reducer": "wc.exe",
	                   "input":  "wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                   "output": " wasb://adfsample@<account name>.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                   "filePaths": [ 
	                       "adfsample/example/apps/wc.exe" , 
	                       "adfsample/example/apps/cat.exe" 
	                   ],
	                   "fileLinkedService" : "StorageLinkedService",
	                   "arguments":[
	                   ]
	               },
	               "policy":
	               {
	                   "concurrency": 1,
	                   "executionPriorityOrder": "NewestFirst",
	                   "retry": 1,
	                   "timeout": "01:00:00"
	               }
	            }
	        ]
	    }
	}

Beachten Sie Folgendes:

1. Legen Sie **linkedServiceName** auf den Namen des verknüpften Diensts fest, der auf Ihren HDInsight-Cluster verweist, auf dem der MapReduce-Streamingauftrag ausgeführt wird.
2. Legen Sie den Typ der Aktivität auf **HDInsightStreaming** fest.
3. Geben Sie für die **mapper**-Eigenschaft den Namen der ausführbaren Zuordnungsdatei an. Im obigen Beispiel ist „cat.exe“ die ausführbare Zuordnungsdatei.
4. Geben Sie für die **reducer**-Eigenschaft den Namen der ausführbaren Reduzierungsdatei an. Im obigen Beispiel ist „wc.exe“ die ausführbare Reduzierungsdatei.
5. Geben Sie für die **input**-Eigenschaft die Eingabedatei (einschließlich Speicherort) für die Zuordnung an. Im Beispiel „wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt“ ist „adfsample“ der Blob-Container, „example/data/Gutenberg“ der Ordner und „davinci.txt“ das Blob.
6. Geben Sie für die **output**-Eigenschaft die Ausgabedatei (einschließlich Speicherort) für die Reduzierung an. Die Ausgabe des Hadoop-Streamingauftrags wird an dem für diese Eigenschaft angegebenen Speicherort geschrieben.
7. Geben Sie im Abschnitt **filePaths** die Pfade für die ausführbaren Zuordnungs- und Reduzierungsdateien an. Im Beispiel „adfsample/example/apps/wc.exe“ ist „adfsample“ der Blob-Container, „example/apps“ der Ordner und „wc.exe“ die ausführbare Datei.
8. Geben Sie für die **fileLinkedService**-Eigenschaft den mit Azure Storage verknüpften Dienst an, der den Azure-Speicher mit den im Abschnitt „filePaths“ angegebenen Dateien darstellt.
9. Geben Sie für die **arguments**-Eigenschaft die Argumente für den Streamingauftrag an.

> [AZURE.NOTE]Wie im Beispiel gezeigt, müssen Sie in Bezug auf die Hadoop-Streamingaktivität für die **outputs**-Eigenschaft ein Ausgabedataset festlegen. Dies ist nur ein für die Pipeline erforderliches Dummy-Dataset. Sie müssen in Bezug auf die Aktivität kein Eingabedataset für die **inputs**-Eigenschaft festlegen.

	

<!---HONumber=Nov15_HO3-->