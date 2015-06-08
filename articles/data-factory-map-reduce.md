<properties 
	pageTitle="Aufrufen eines MapReduce-Programms über Azure Data Factory" 
	description="Erfahren Sie, wie Daten durch Ausführen von MapReduce-Programme auf einem Azure HDInsight-Cluster aus einem Azure Data-Factory zu verarbeiten." 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Aufrufen von MapReduce-Programmen über Data Factory
In diesem Artikel wird beschrieben, wie das Aufrufen einer **MapReduce** Programm aus einem Azure Data Factory-Pipeline mithilfe der **HDInsight-Aktivität** mit **MapReduce-Transformation**.

## Einführung 
Eine Pipeline in einer Azure Data Factory verarbeitet Daten in verknüpften Speicherdiensten mithilfe verknüpfter Compute Services. Sie enthält eine Abfolge von Aktivitäten, wobei jede Aktivität einen bestimmten Verarbeitungsvorgang ausführt. In diesem Artikel wird die Verwendung der MapReduce-Transformation der HDInsight-Aktivität beschrieben.
 
Finden Sie unter [Verwendung Pig und Hive Data Factory][data-factory-pig-hive-activities] ausführliche Informationen zum Ausführen von Pig-Hive cluster Skripts auf einem HDInsight von einer Azure Data Factory-Pipeline mit Pig-Hive-Transformationen der HDInsight-Aktivität.

## JSON für HDInsight-Aktivität mit MapReduce-transformation 

In der JSON-Definition für die HDInsight-Aktivität:
 
1. Legen Sie die **Typ** von der **Aktivität** auf **HDInsightActivity**.
2. Legen Sie die **Typ** von der **Transformation** auf **MapReduce**.
3. Geben Sie den Namen der Klasse für **Klassenname** Eigenschaft.
4. Geben Sie den Pfad, einschließlich des Dateinamens für die JAR-Datei **JarFilePath** Eigenschaft.
5. Geben Sie den verknüpften Dienst, der auf den Azure-Blob-Speicher verweist, enthält die JAR-Datei für **JarLinkedService** Eigenschaft.   
6. Geben Sie Argumente für das MapReduce-Programm in die **Argumente** Abschnitt. 

   
 

		{  
		   "name":"MahoutMapReduceSamplePipeline",
		   "properties":{  
		      "description":"Sample Pipeline to Run a Mahout Custom Map Reduce Jar. This job calcuates an Item Similarity Matrix to determine the similarity between 2 items",
		      "activities":[  
		         {  
		            "name":"MyMahoutActivity",
		            "description":"Custom Map Reduce to generate Mahout result",
		            "type":"HDInsightActivity",
		            "inputs":[  
		               {  
		                  "Name":"MahoutInput"
		               }
		            ],
		            "outputs":[  
		               {  
		                  "Name":"MahoutOutput"
		               }
		            ],
		            "linkedServiceName":"HDInsightLinkedService",
		            "transformation":{  
		               "type":"MapReduce",
		               "className":"org.apache.mahout.cf.taste.hadoop.similarity.item.ItemSimilarityJob",
		               "jarFilePath":"<container>/Mahout/Jars/mahout-core-0.9.0.2.1.3.2-0002-job.jar",
		               "jarLinkedService":"StorageLinkedService",
		               "arguments":[  
		                  "-s",
		                  "SIMILARITY_LOGLIKELIHOOD",
		                  "--input",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/input",
		                  "--output",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/output/",
		                  "--maxSimilaritiesPerItem",
		                  "500",
		                  "--tempDir",
		                  "wasb://<container>@<accountname>.blob.core.windows.net/Mahout/temp/mahout"
		               ]
		            },
		            "policy":{  
		               "concurrency":1,
		               "executionPriorityOrder":"OldestFirst",
		               "retry":3,
		               "timeout":"01:00:00"
		            }
		         }
		      ]
		   }
		}

Sie können die "MapReduce"-Transformation zum Ausführen beliebiger MapReduce-JAR-Dateien auf einem HDInsight-Cluster verwenden. In der folgenden JSON-Beispieldefinition einer Pipeline wird die HDInsight-Aktivität für die Ausführung einer Mahout-JAR-Datei konfiguriert.

## Beispiel
Sie können ein Beispiel für die Verwendung der HDInsight-Aktivität mit MapReduce-Transformation von herunterladen: [Daten Factory-Beispielen auf GitHub][data-factory-samples].

## Siehe auch

Artikel | Beschreibung
------ | ---------------
[Lernprogramm: Verschieben und Verarbeiten von Protokolldateien, die mit Daten-Factory][adf-tutorial] | Dieser Artikel bietet eine End-to-End-exemplarische zum Implementieren von near Real World-Szenario mit Azure Data Factory zum Transformieren von Daten aus den Protokolldateien Einblicke in. In diesem Lernprogramm verwenden Sie beide Pig und Hive-Transformationen, um Daten zu verarbeiten. 
[Azure Data Factory-Entwicklerreferenz][developer-reference] | Entwicklerreferenz hat die umfassendes Referenzmaterial für die Cmdlets, JSON-Skripts, Funktionen usw.... 


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: data-factory-pig-hive-activities.md
[data-factory-copy-activity]: ..//data-factory-copy-activity
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: data-factory-get-started.md
[adfgetstartedmonitoring]: data-factory-get-started.md#MonitorDataSetsAndPipeline
[adftutorial]: data-factory-tutorial.md

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!---HONumber=GIT-SubDir-->