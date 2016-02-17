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
	ms.date="02/01/2016" 
	ms.author="spelluru"/>

# Hadoop-Streamingaktivität
Mit der Aktivität „HDInsightStreamingActivity“ können Sie einen Hadoop-Streamingauftrag über eine Azure Data Factory-Pipeline aufrufen. Der folgende JSON-Codeausschnitt zeigt die Syntax für die Verwendung von HDInsightStreamingActivity in einer JSON-Pipelinedatei.

Die HDInsight-Streamingaktivität in einer [Data Factory-Pipeline](data-factory-create-pipelines.md) führt Hadoop-Streamingprogramme in [eigenen](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) oder [bedarfsgesteuerten](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-basierten HDInsight-Clustern aus. Dieser Artikel baut auf dem Artikel zu [Datentransformationsaktivitäten](data-factory-data-transformation-activities.md) auf, der einen allgemeinen Überblick über die Datentransformation und die unterstützten Transformationsaktivitäten bietet.

## JSON-Beispiel
Der HDInsight-Cluster wird automatisch mit Beispielprogrammen („wc.exe“ und „cat.exe“) und Daten („davinci.txt“) aufgefüllt. Standardmäßig entspricht der Name des Containers, der im HDInsight-Cluster verwendet wird, dem Namen des Clusters. Wenn der Name Ihres Clusters beispielsweise „myhdicluster“ lautet, lautet der Name des zugeordneten Blob-Containers auch „myhdicluster“.

	{
	    "name": "HadoopStreamingPipeline",
	    "properties": {
	        "description": "Hadoop Streaming Demo",
	        "activities": [
	            {
	                "type": "HDInsightStreaming",
	                "typeProperties": {
	                    "mapper": "cat.exe",
	                    "reducer": "wc.exe",
	                    "input": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                    "output": "wasb://<nameofthecluster>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                    "filePaths": [
	                        "<nameofthecluster>/example/apps/wc.exe",
	                        "<nameofthecluster>/example/apps/cat.exe"
	                    ],
	                    "fileLinkedService": "StorageLinkedService",
	                    "getDebugInfo": "Failure"
	                },
	                "outputs": [
	                    {
	                        "name": "StreamingOutputDataset"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "RunHadoopStreamingJob",
	                "description": "Run a Hadoop streaming job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-04T00:00:00Z",
	        "end": "2014-01-05T00:00:00Z"
	    }
	}

Beachten Sie Folgendes:

1. Legen Sie **linkedServiceName** auf den Namen des verknüpften Diensts fest, der auf Ihren HDInsight-Cluster verweist, auf dem der MapReduce-Streamingauftrag ausgeführt wird.
2. Legen Sie den Typ der Aktivität auf **HDInsightStreaming** fest.
3. Geben Sie für die **mapper**-Eigenschaft den Namen der ausführbaren Zuordnungsdatei an. Im obigen Beispiel ist „cat.exe“ die ausführbare Zuordnungsdatei.
4. Geben Sie für die **reducer**-Eigenschaft den Namen der ausführbaren Reduzierungsdatei an. Im obigen Beispiel ist „wc.exe“ die ausführbare Reduzierungsdatei.
5. Geben Sie für die **input**-Eigenschaft die Eingabedatei (einschließlich Speicherort) für die Zuordnung an. Im Beispiel „wasb://adfsample@<account name>.blob.core.windows.net/example/data/gutenberg/davinci.txt“ ist „adfsample“ der Blob-Container, „example/data/Gutenberg“ der Ordner und „davinci.txt“ das Blob.
6. Geben Sie für die **output**-Eigenschaft die Ausgabedatei (einschließlich Speicherort) für die Reduzierung an. Die Ausgabe des Hadoop-Streamingauftrags wird an dem für diese Eigenschaft angegebenen Speicherort geschrieben.
7. Geben Sie im Abschnitt **filePaths** die Pfade für die ausführbare Zuordnungs- und Reduzierungsdatei an. Im Beispiel „adfsample/example/apps/wc.exe“ ist „adfsample“ der Blob-Container, „example/apps“ der Ordner und „wc.exe“ die ausführbare Datei.
8. Geben Sie für die **fileLinkedService**-Eigenschaft den mit Azure Storage verknüpften Dienst an, der den Azure-Speicher mit den im Abschnitt „filePaths“ angegebenen Dateien darstellt.
9. Geben Sie für die **arguments**-Eigenschaft die Argumente für den Streamingauftrag an.
10. Die **getDebugInfo**-Eigenschaft ist ein optionales Element. Wenn die Eigenschaft auf "Failure" festgelegt wird, werden die Protokolle nur bei Fehlern heruntergeladen. Wenn die Eigenschaft auf "All" festgelegt wird, werden Protokolle immer heruntergeladen, unabhängig vom Ausführungsstatus.

> [AZURE.NOTE] Wie im Beispiel gezeigt, müssen Sie in Bezug auf die Hadoop-Streamingaktivität für die **outputs**-Eigenschaft ein Ausgabedataset festlegen. Dies ist nur ein für die Pipeline erforderliches Dummy-Dataset. Sie müssen in Bezug auf die Aktivität kein Eingabedataset für die **inputs**-Eigenschaft festlegen.

	
## Beispiel
In der Pipeline in dieser exemplarischen Vorgehensweise wird das Zuordnungs-/Reduzierungsprogramm für das Streaming der Wortzählung auf Ihrem Azure HDInsight-Cluster ausgeführt.

### Verknüpfte Dienste

#### Mit Azure Storage verknüpfter Dienst
Erstellen Sie zunächst einen verknüpften Dienst, um die vom Azure HDInsight-Cluster verwendete Azure Storage-Instanz mit der Azure Data Factory zu verknüpfen. Denken Sie beim Kopieren und Einfügen des folgenden Codes daran, „account name“ (Kontoname) und „account key“ (Kontoschlüssel) durch den Namen und Schlüssel Ihrer Azure Storage-Instanz zu ersetzen.

	{
	    "name": "StorageLinkedService",
	    "properties": {
	        "type": "AzureStorage",
	        "typeProperties": {
	            "connectionString": "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=<account key>"
	        }
	    }
	}

#### Mit Azure HDInsight verknüpfter Dienst
Erstellen Sie anschließend einen verknüpften Dienst, um Ihren Azure HDInsight-Cluster mit der Azure Data Factory zu verknüpfen. Ersetzen Sie beim Kopieren und Einfügen des folgenden Codes „HDInsight cluster name“ durch den Namen des HDInsight-Clusters, und ändern Sie die Werte für Benutzername und Kennwort.
	
	{
	    "name": "HDInsightLinkedService",
	    "properties": {
	        "type": "HDInsight",
	        "typeProperties": {
	            "clusterUri": "https://<HDInsight cluster name>.azurehdinsight.net",
	            "userName": "admin",
	            "password": "**********",
	            "linkedServiceName": "StorageLinkedService"
	        }
	    }
	}

### Datasets

#### Ausgabedataset
Die Pipeline in diesem Beispiel akzeptiert keine Eingaben. Für die HDInsight-Streamingaktivität muss ein Ausgabedataset angegeben werden. Dies ist nur ein für die Pipeline erforderliches Dummy-Dataset.

	{
	    "name": "StreamingOutputDataset",
	    "properties": {
	        "published": false,
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "adftutorial/streamingdata/",
	            "format": {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            },
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

### Pipeline

Die Pipeline in diesem Beispiel weist nur eine einzelne Aktivität vom Typ **HDInsightStreaming** auf.

Der HDInsight-Cluster wird automatisch mit Beispielprogrammen („wc.exe“ und „cat.exe“) und Daten („davinci.txt“) aufgefüllt. Standardmäßig entspricht der Name des Containers, der im HDInsight-Cluster verwendet wird, dem Namen des Clusters. Wenn der Name Ihres Clusters beispielsweise „myhdicluster“ lautet, lautet der Name des zugeordneten Blob-Containers auch „myhdicluster“.

	{
	    "name": "HadoopStreamingPipeline",
	    "properties": {
	        "description": "Hadoop Streaming Demo",
	        "activities": [
	            {
	                "type": "HDInsightStreaming",
	                "typeProperties": {
	                    "mapper": "cat.exe",
	                    "reducer": "wc.exe",
	                    "input": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/gutenberg/davinci.txt",
	                    "output": "wasb://<blobcontainer>@spestore.blob.core.windows.net/example/data/StreamingOutput/wc.txt",
	                    "filePaths": [
	                        "<blobcontainer>/example/apps/wc.exe",
	                        "<blobcontainer>/example/apps/cat.exe"
	                    ],
	                    "fileLinkedService": "StorageLinkedService"
	                },
	                "outputs": [
	                    {
	                        "name": "StreamingOutputDataset"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "RunHadoopStreamingJob",
	                "description": "Run a Hadoop streaming job",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-04T00:00:00Z",
	        "end": "2014-01-05T00:00:00Z"
	    }
	}

<!---HONumber=AcomDC_0204_2016-->