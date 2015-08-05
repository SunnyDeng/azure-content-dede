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
	ms.date="07/07/2015" 
	ms.author="spelluru"/>

# Aufrufen von MapReduce-Programmen über Data Factory
In diesem Artikel wird beschrieben, wie ein **MapReduce-Programm** mithilfe der **HDInsight**-Aktivität mit **MapReduce-Transformation** aus einer Azure Data Factory-Pipeline aufgerufen wird.

## Einführung 
Eine Pipeline in einer Azure Data Factory verarbeitet Daten in verknüpften Speicherdiensten mithilfe verknüpfter Compute Services. Sie enthält eine Abfolge von Aktivitäten, wobei jede Aktivität einen bestimmten Verarbeitungsvorgang ausführt. In diesem Artikel wird die Verwendung der MapReduce-Transformation der HDInsight-Aktivität beschrieben.
 
Weitere Informationen zum Anwenden von Pig/Hive-Skripts auf einen HDInsight-Cluster über eine Azure Data Factory-Pipeline mithilfe von Pig/Hive-Transformationen der HDInsight-Aktivität finden Sie unter [Verwenden von Pig und Hive mit Data Factory][data-factory-pig-hive-activities].

## JSON für HDInsight-Aktivität mit MapReduce-Transformation 

Gehen Sie für die JSON-Definition der HDInsight-Aktivität so vor:
 
1. Legen Sie den **Typ** der **Aktivität** auf **HDInsightActivity** fest.
2. Legen Sie den **Typ** der **Transformation** auf **MapReduce** fest.
3. Geben Sie für die Eigenschaft **className** den Namen der Klasse an.
4. Geben Sie den Pfad zur JAR-Datei an, einschließlich des Dateinamens für die Eigenschaft **jarFilePath**.
5. Geben Sie den verknüpften Dienst an, der auf den Azure-Blobspeicher verweist, der die JAR-Datei für die Eigenschaft **jarLinkedService** enthält.   
6. Geben Sie im Abschnitt **arguments** Argumente für das MapReduce-Programm an. 

   
 

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
Sie können ein Beispiel zur Verwendung der HDInsight-Aktivität mit MapReduce-Transformation aus den [Data Factory-Beispielen auf GitHub][data-factory-samples] herunterladen.

## Siehe auch

Artikel | Beschreibung
------ | ---------------
[Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Data Factory][adf-tutorial] | In diesem Artikel wird in einer umfassenden exemplarischen Vorgehensweise die Implementierung eines nahezu realen Szenarios mithilfe von Azure Data Factory veranschaulicht, um mithilfe von Protokolldateien nützliche Einblicke zu gewinnen. In diesem Lernprogramm verwenden Sie sowohl Pig- als auch Hive-Transformationen, um Daten zu verarbeiten. 
[Azure Data Factory-Entwicklerreferenz][developer-reference] | Die Entwicklerreferenz enthält umfassende Referenzinformationen für Cmdlets, JSON-Skripts, Funktionen usw. 


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
 

<!---HONumber=July15_HO4-->