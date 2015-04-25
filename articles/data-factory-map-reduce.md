<properties title="Invoke MapReduce Program from Azure Data Factory" pageTitle="Aufrufen eines MapReduce-Programms über Azure Data Factory" description="Erfahren Sie mehr über die Datenverarbeitung mit MapReduce-Programmen auf einem Azure HDInsight-Cluster aus einer Azure Data Factory." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/13/2014" ms.author="spelluru" />

# Aufrufen von MapReduce-Programmen über Data Factory
In diesem Artikel wird beschrieben, wie ein **MapReduce**-Programm mithilfe der **HDInsight-Aktivität** mit **MapReduce-Transformation** aus einer Azure Data Factory-Pipeline aufgerufen wird. 

## Einführung 
Eine Pipeline in einer Azure Data Factory verarbeitet Daten in verknüpften Speicherdiensten durch Verwendung von verknüpften Serverdiensten. Sie enthält eine Abfolge von Aktivitäten, wobei jede Aktivität einen bestimmten Verarbeitungsvorgang ausführt. 

- Eine **Kopieraktivität** kopiert Daten von einem Quellspeicher in einen Zielspeicher. Weitere Informationen zur Kopieraktivität finden Sie unter [Kopieren von Daten mit Data Factory][data-factory-copy-activity]. 
- Die **HDInsight-Aktivität** verarbeitet Daten durch Ausführen von Hive/Pig-Skripts oder MapReduce-Programmen auf einem HDInsight-Cluster. Die HDInsight-Aktivität unterstützt drei Transformationen: **Hive**, **Pig** und **MapReduce**. Die HDInsight-Aktivität kann mindestens eine Eingabe verwenden und mindestens eine Ausgabe produzieren.
 
Weitere Informationen zum Ausführen von Pig/Hive-Skripts auf einem HDInsight-Cluster über eine Azure Data Factory-Pipeline mithilfe von Pig/Hive-Transformationen der HDInsight-Aktivität finden Sie unter [Verwenden von Pig und Hive mit Data Factory][data-factory-pig-hive-activities]. In diesem Artikel wird die Verwendung der MapReduce-Transformation der HDInsight-Aktivität beschrieben.

## Pipeline-JSON
In der JSON-Datei für eine Pipeline:
 
1. Legen Sie für den **Typ** der **Aktivität** den Wert **HDInsightActivity** fest.
2. Legen Sie für den **Typ** der **Transformation** den Wert **MapReduce** fest.
3. Geben Sie den Namen der Klasse für die **className**-Eigenschaft an.
4. Geben Sie den Pfad zur JAR-Datei an, einschließlich des Dateinamens für die **jarFilePath**-Eigenschaft.
5. Geben Sie den verknüpften Dienst an, der auf den Azure Blob-Speicher verweist, der die JAR-Datei für die **jarLinkedService**-Eigenschaft enthält.   
6. Geben Sie Argumente für das MapReduce-Programm im **arguments**-Abschnitt an. 

Sie können die "MapReduce"-Transformation zum Ausführen beliebiger MapReduce-JAR-Dateien auf einem HDInsight-Cluster verwenden. In der folgenden JSON-Beispieldefinition einer Pipeline wird die HDInsight-Aktivität für die Ausführung einer Mahout-JAR-Datei konfiguriert.   
 

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

## Beispiel
Sie können ein Beispiel zur Verwendung der HDInsight-Aktivität mit "MapReduce"-Transformation herunterladen: [Data Factory-Beispiele auf GitHub][data-factory-samples].  

## Siehe auch

Artikel | Beschreibung
------ | ---------------
[Einführung in Azure Data Factory][data-factory-introduction] | In diesem Artikel werden der Azure Data Factory-Dienst, Azure Data Factory-Konzepte, der bereitgestellte Nutzen und unterstützte Szenarien erläutert.
[Erste Schritte mit Azure Data Factory][adf-getstarted] | Dieser Artikel bietet ein umfassendes Lernprogramm, in dem Sie erfahren, wie Sie eine Beispiel-Data Factory für Azure erstellen, die Daten aus einem Azure-BLOB in eine Azure SQL-Datenbank kopiert.
[Aktivieren von Pipelines zum Arbeiten mit lokalen Daten][use-onpremises-datasources] | Dieser Artikel enthält eine exemplarische Vorgehensweise zum Kopieren von Daten aus einer lokalen SQL Server-Datenbank in einen Azure-BLOB.
[Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Data Factory][adf-tutorial] | In diesem Artikel wird in einer umfassenden exemplarischen Vorgehensweise die Implementierung eines realen Szenarios mithilfe von Azure Data Factory veranschaulicht, um Einblicke aus Protokolldateien zu gewinnen.
[Verwenden von benutzerdefinierten Aktivitäten in einer Data Factory][use-custom-activities] | Dieser Artikel enthält eine exemplarische Vorgehensweise mit schrittweisen Anleitungen zum Erstellen einer benutzerdefinierten Aktivität und deren Verwendung in einer Pipeline.
[Problembehandlung für Data Factory][troubleshoot] | In diesem Artikel wird beschrieben, wie Probleme in Azure Data Factory behoben werden.
[Azure Data Factory-Entwicklerreferenz][developer-reference] | Die Entwicklerreferenz enthält umfassende Referenzinformationen für Cmdlets, JSON-Skripts, Funktionen usw. 


[data-factory-samples]: http://go.microsoft.com/fwlink/?LinkId=516907
[data-factory-pig-hive-activities]: ../data-factory-pig-hive-activities
[data-factory-copy-activity]: ..//data-factory-copy-activity
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction

[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456


[adfgetstarted]: ../data-factory-get-started
[adfgetstartedmonitoring]:../data-factory-get-started#MonitorDataSetsAndPipeline 
[adftutorial]: ../data-factory-tutorial

[Developer Reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[Azure Portal]: http://portal.azure.com

<!--HONumber=35.2-->
