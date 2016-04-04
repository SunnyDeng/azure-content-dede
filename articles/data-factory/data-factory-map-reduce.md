<properties 
	pageTitle="Aufrufen eines MapReduce-Programms über Azure Data Factory" 
	description="Erfahren Sie, wie Sie Daten verarbeiten, indem Sie MapReduce-Programme mithilfe einer Azure Data Factory auf einen Azure HDInsight-Cluster anwenden." 
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

# Aufrufen von MapReduce-Programmen über Data Factory
Die HDInsight-MapReduce-Aktivität in einer Data Factory-[Pipeline](data-factory-create-pipelines.md) führt MapReduce-Streamingprogramme in [eigenen](data-factory-compute-linked-services.md#azure-hdinsight-linked-service) oder [bedarfsgesteuerten](data-factory-compute-linked-services.md#azure-hdinsight-on-demand-linked-service) Windows/Linux-basierten HDInsight-Clustern aus. Dieser Artikel baut auf dem Artikel zu [Datentransformationsaktivitäten](data-factory-data-transformation-activities.md) auf, der einen allgemeinen Überblick über die Datentransformation und die unterstützten Transformationsaktivitäten bietet.

## Einführung 
Eine Pipeline in einer Azure Data Factory verarbeitet Daten in verknüpften Speicherdiensten mithilfe verknüpfter Compute Services. Sie enthält eine Abfolge von Aktivitäten, wobei jede Aktivität einen bestimmten Verarbeitungsvorgang ausführt. In diesem Artikel wird die Verwendung der HDInsight-Aktivität „MapReduce“ beschrieben.
 
Weitere Informationen zum Ausführen von Pig-/Hive-Skripts auf einen Windows-/Linux-basierten HDInsight-Cluster über eine Azure Data Factory-Pipeline mithilfe von Pig-/Hive-HDInsight-Aktivitäten finden Sie im Artikel zu [Pig](data-factory-pig-activity.md) und [Hive](data-factory-hive-activity.md).

## JSON für die HDInsight-Aktivität „MapReduce“ 

Gehen Sie für die JSON-Definition der HDInsight-Aktivität so vor:
 
1. Legen Sie den **Typ** der **Aktivität** auf **HDInsight** fest.
3. Geben Sie für die Eigenschaft **className** den Namen der Klasse an.
4. Geben Sie den Pfad zur JAR-Datei an, einschließlich des Dateinamens für die Eigenschaft **jarFilePath**.
5. Geben Sie den verknüpften Dienst an, der auf den Azure-Blobspeicher verweist, der die JAR-Datei für die Eigenschaft **jarLinkedService** enthält.   
6. Geben Sie im Abschnitt **arguments** Argumente für das MapReduce-Programm an. Zur Laufzeit werden ein paar zusätzliche Argumente aus dem MapReduce-Framework angezeigt (z. B.: mapreduce.job.tags). Um Ihre Argumente mit den MapReduce-Argumenten zu unterscheiden, sollten Sie erwägen, sowohl Option als auch Wert als Argumente zu verwenden, wie im folgenden Beispiel gezeigt ("-s", "--input", "--output" usw. sind Optionen, die unmittelbar von ihrem Wert gefolgt werden).

		{
		    "name": "MahoutMapReduceSamplePipeline",
		    "properties": {
		        "description": "Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		        "activities": [
		            {
		                "type": "HDInsightMapReduce",
		                "typeProperties": {
		                    "className": "org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		                    "jarFilePath": "adfsamples/Mahout/jars/mahout-examples-0.9.0.2.2.7.1-34.jar",
		                    "jarLinkedService": "StorageLinkedService",
		                    "arguments": [
		                        "-s",
		                        "SIMILARITY_LOGLIKELIHOOD",
		                        "--input",
		                        "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/input",
		                        "--output",
		                        "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/output/",
		                        "--maxSimilaritiesPerItem",
		                        "500",
		                        "--tempDir",
		                        "wasb://adfsamples@spestore.blob.core.windows.net/Mahout/temp/mahout"
		                    ]
		                },
		                "inputs": [
		                    {
		                        "name": "MahoutInput"
		                    }
		                ],
		                "outputs": [
		                    {
		                        "name": "MahoutOutput"
		                    }
		                ],
		                "policy": {
		                    "timeout": "01:00:00",
		                    "concurrency": 1,
		                    "retry": 3
		                },
		                "scheduler": {
		                    "frequency": "Hour",
		                    "interval": 1
		                },
		                "name": "MahoutActivity",
		                "description": "Custom Map Reduce to generate Mahout result",
		                "linkedServiceName": "HDInsightLinkedService"
		            }
		        ],
		        "start": "2014-01-03T00:00:00Z",
		        "end": "2014-01-04T00:00:00Z",
		        "isPaused": false,
		        "hubName": "mrfactory_hub",
		        "pipelineMode": "Scheduled"
		    }
		}
	
	

Mit der HDInsight-Aktivität „MapReduce“ können Sie beliebige MapReduce-JAR-Dateien auf einem HDInsight-Cluster ausführen. In der folgenden JSON-Beispieldefinition einer Pipeline wird die HDInsight-Aktivität für die Ausführung einer Mahout-JAR-Datei konfiguriert.

## Beispiel auf GitHub
Unter [Data Factory-Beispiele auf GitHub](https://github.com/Azure/Azure-DataFactory/tree/master/Samples/JSON/MapReduce_Activity_Sample) können Sie ein Beispiel für die Verwendung der HDInsight-Aktivität „MapReduce“ herunterladen.

## Ausführen des Wortzählungsprogramms
Die Pipeline in diesem Beispiel führt das Zuordnungs-/Reduzierungsprogramm für die Wortzählung auf Ihrem Azure HDInsight-Cluster aus.

### Verknüpfte Dienste
Erstellen Sie zunächst einen verknüpften Dienst, um die vom Azure HDInsight-Cluster verwendete Azure Storage-Instanz mit der Azure Data Factory zu verknüpfen. Denken Sie beim Kopieren und Einfügen des folgenden Codes daran, **account name** (Kontoname) und **account key** (Kontoschlüssel) durch den Namen und Schlüssel Ihrer Azure Storage-Instanz zu ersetzen.

#### Mit Azure Storage verknüpfter Dienst

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
Erstellen Sie anschließend einen verknüpften Dienst, um Ihren Azure HDInsight-Cluster mit der Azure Data Factory zu verknüpfen. Ersetzen Sie beim Kopieren und Einfügen des folgenden Codes den HDInsight-Clusternamen (**HDInsight cluster name**) durch den Namen des HDInsight-Clusters, und ändern Sie die Werte für Benutzername und Kennwort.

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
Die Pipeline in diesem Beispiel akzeptiert keine Eingaben. Für die HDInsight-Aktivität „MapReduce“ muss ein Ausgabedataset angegeben werden. Dies ist nur ein für die Pipeline erforderliches Dummy-Dataset.

	{
	    "name": "MROutput",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "fileName": "WordCountOutput1.txt",
	            "folderPath": "example/data/",
	            "format": {
	                "type": "TextFormat",
	                "columnDelimiter": ","
	            }
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

### Pipeline
Die Pipeline in diesem Beispiel besitzt nur eine einzelne Aktivität vom Typ „HDInsightMapReduce“. Einige wichtige Eigenschaften in JSON:

Eigenschaft | Hinweise
:-------- | :-----
type | Der Typ muss auf **HDInsightMapReduce** festgelegt werden. 
className | Der Name der Klasse lautet **wordcount**.
jarFilePath | Der Pfad zu der JAR-Datei mit der oben angegebenen Klasse. Denken Sie beim Kopieren und Einfügen des folgenden Codes daran, den Namen des Clusters zu ändern. 
jarLinkedService | Der mit Azure Storage verknüpfte Dienst mit der JAR-Datei. Hierbei handelt es sich um den Speicher, der dem HDInsight-Cluster zugeordnet ist. 
arguments | Das Wortzählungsprogramm akzeptiert zwei Argumente: eine Eingabe und eine Ausgabe. Die Eingabedatei ist die Datei „davinci.txt“.
frequency/interval | Die Werte für diese Eigenschaften entsprechen dem Ausgabedataset. 
linkedServiceName | Bezieht sich auf den mit HDInsight verknüpften Dienst, den Sie vorhin erstellt haben.   

	{
	    "name": "MRSamplePipeline",
	    "properties": {
	        "description": "Sample Pipeline to Run the Word Count Program",
	        "activities": [
	            {
	                "type": "HDInsightMapReduce",
	                "typeProperties": {
	                    "className": "wordcount",
	                    "jarFilePath": "<HDInsight cluster name>/example/jars/hadoop-examples.jar",
	                    "jarLinkedService": "StorageLinkedService",
	                    "arguments": [
	                        "/example/data/gutenberg/davinci.txt",
	                        "/example/data/WordCountOutput1"
	                    ]
	                },
	                "outputs": [
	                    {
	                        "name": "MROutput"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "retry": 3
	                },
	                "scheduler": {
	                    "frequency": "Day",
	                    "interval": 1
	                },
	                "name": "MRActivity",
	                "linkedServiceName": "HDInsightLinkedService"
	            }
	        ],
	        "start": "2014-01-03T00:00:00Z",
	        "end": "2014-01-04T00:00:00Z"
	    }
	}

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#monitor-pipelines
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Classic Portal]: http://portal.azure.com
 

<!---HONumber=AcomDC_0323_2016-->