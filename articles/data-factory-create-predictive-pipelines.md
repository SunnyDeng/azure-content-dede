<properties title="Azure Data Factory - Create Predictive Pipelines using Data Factory and Azure Machine Learning" pageTitle="Data Factory - Erstellen von Vorhersagepipelines mithilfe von Data Factory und Machine Learning | Azure" description="Beschreibt, wie Sie mit Azure Data Factory und Azure Machine Learning Prognose-Pipelines erstellen können." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/05/2014" ms.author="spelluru" />

# Erstellen von Vorhersagepipelines mithilfe von Azure Data Factory und Azure Machine Learning 
Sie können veröffentlichte [Azure Machine Learning][azure-machine-learning]-Modelle innerhalb von Azure Data Factory-Pipelines in Betrieb nehmen. Diese Pipelines werden als "Vorhersagepipelines" bezeichnet. Sie benötigen Folgendes, um eine Vorhersagepipeline erstellen zu können:

-	Den API-Schlüssel des veröffentlichten Arbeitsbereichsmodells und die URL für die Stapelbewertung (siehe nachfolgende Abbildung)
-	Einen Azure-Blob-Speicher, der die zu bewertende CSV-Eingabedatei enthält
-	Einen Azure-Blob-Speicher, der die CSV-Datei mit den Bewertungsergebnissen enthält

	![Machine Learning Dashboard][machine-learning-dashboard]

	Die URL für die Stapelbewertung für "AzureMLLinkedService" wird, wie in obiger Abbildung gezeigt, abgerufen (minus'**help**'):  https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

Eine **Vorhersagepipeline** besteht aus folgenden Teilen:

-	Eingabe- und Ausgabetabellen
-	Mit Azure Storage und Azure ML verknüpfte Dienste
-	Eine Pipeline mit Azure ML-Stapelbewertungsaktivität

## Beispiel



1. Erstellen Sie für Ihr Azure Storage einen verknüpften Dienst. Wenn sich die Ein- und Ausgabedateien für die Bewertung in verschiedenen Speicherkonten befinden, benötigen Sie zwei verknüpfte Dienste. Hier folgt ein JSON-Beispiel:

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. Erstellen Sie die Ein- und Ausgabetabellen für Azure Data Factory. Beachten Sie, dass diese im Gegensatz zu einigen anderen Data Factory-Tabellen die beiden Werte **folderPath** und **fileName** enthalten müssen. Sie können die Partitionierung verwenden, damit jede Stapelausführung (jeder Datenslice) eindeutige Ein- und Ausgabedateien verarbeitet oder erzeugt. Sie müssen wahrscheinlich einige Upstream-Aktivitäten einbeziehen, um die Eingabe in das CSV-Dateiformat umzuwandeln und sie im Speicherkonto für die einzelnen Datenslices abzulegen. In diesem Fall beziehen Sie die im folgenden Beispiel gezeigte "waitOnExternal"-Einstellungen nicht ein, und Ihr "ScoringInputBlob" würde die Ausgabetabelle einer anderen Aktivität darstellen.

		{  
			"name":"ScoringInputBlob",
			"properties":
			{  
					"location":
					{  
						"type":"AzureBlobLocation",
						"folderPath":"azuremltesting/input",
						"fileName":"in.csv",
						"format":
						{ 
							"type":"TextFormat",
							"columnDelimiter":","
						},
						"linkedServiceName":"StorageLinkedService"
					},
					"availability":
					{  
						"frequency":"Day",
						"interval":1,
						"waitOnExternal":
						{
		                	"retryInterval": "00:01:00",
		                	"retryTimeout": "00:10:00",
		                	"maximumRetry": 3
		            	}
		      		}
		   		}
			}

3. In diesem Ausgabebeispiel wird die Partitionierung verwendet, um für die Ausführung der einzelnen Datenslices einen eindeutigen Ausgabepfad zu erstellen. Ohne diesen Schritt würde die Aktivität die Datei überschreiben.

		{  
		   "name":"ScoringResultBlob",
		   "properties":
			{  
		        "location":
				{  
		            "type":"AzureBlobLocation",
		            "folderPath": "azuremltesting/scored/{folderpart}/",
		            "fileName": "{filepart}result.csv",
		            "partitionedBy": [ 
		                 { "name": "folderpart", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMdd" } },
		                 { "name": "filepart", "value": { "type": "DateTime", "date": "SliceStart", "format": "HHmmss" } } 
		             ], 
		            "format":{  
		              "type":"TextFormat",
		              "columnDelimiter":","
		            },
		            "linkedServiceName":"StorageLinkedService"
		        },
		        "availability":
				{  
		            "frequency":"Day",
		            "interval":15
		        }
		   }
		}


4. Erstellen Sie einen verknüpften Dienst vom Typ: **AzureMLLinkedService**, der den API-Schlüssel und die URL für die Modellstapelbewertung bereitstellt.
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. Erstellen Sie abschließend eine Pipeline, die ein **AzureMLBatchScoringActivity** enthält. Dadurch wird der Speicherort der Eingabedatei aus Ihren Eingabetabellen abgerufen, die AzureML-API für die Stapelbewertung aufgerufen und die Stapelbewertungsausgabe in das in der Ausgabetabelle angegebene Blob kopiert. Im Gegensatz zu einigen anderen Data Factory-Aktivitäten kann "AzureMLBatchScoringActivity" nur eine Eingabe- und eine Ausgabetabelle besitzen.

		 {
		    "name": "PredictivePipeline",
		    "properties":
		    {
		        "description" : "use AzureML model",
		        "activities":
		        [
		         {  
		            "name":"MLActivity",
		            "type":"AzureMLBatchScoringActivity",
		            "description":"prediction analysis on batch input",
		            "inputs": [ { "name": "ScoringInputBlob" } ],
		            "outputs":[ { "name": "ScoringResultBlob" } ],
		            "linkedServiceName":"MyAzureMLLinkedService",
		            "policy":{  
		               "concurrency":3,
		               "executionPriorityOrder":"NewestFirst",
		               "retry":1,
		               "timeout":"02:00:00"
		            }
		         }
		        ]
		    }
		}

## Siehe auch

Artikel | Beschreibung
------ | ---------------
[Einführung in Azure Data Factory][adf-introduction] | Dieser Artikel bietet einen Überblick über den Azure Data Factory-Dienst und über die von ihm unterstützten Szenarien. 
[Erste Schritte mit Azure Data Factory][adf-getstarted] | Dieser Artikel bietet ein einfaches Lernprogramm, das schrittweise Anweisungen zum Erstellen und Überwachen einer beispielhaften Data Factory bereitstellt.
[Aktivieren von Pipelines zum Arbeiten mit lokalen Daten][use-onpremises-datasources] | Dieser Artikel enthält eine exemplarische Vorgehensweise zum Kopieren von Daten aus einer lokalen SQL Server-Datenbank in einen Azure-BLOB.
[Verwenden von Pig und Hive mit Data Factory][use-pig-and-hive-with-data-factory] | Dieser Artikel enthält eine exemplarische Vorgehensweise, in der gezeigt wird, wie eine HDInsight-Aktivität verwendet wird, um ein Hive-/Pig-Skript zur Verarbeitung von Eingabedaten auszuführen, um Ausgabedaten zu produzieren.
[Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Data Factory][adf-tutorial] | In diesem Artikel wird in einer umfassenden exemplarischen Vorgehensweise die Implementierung eines realen Szenarios mithilfe von Azure Data Factory veranschaulicht, um Einblicke aus Protokolldateien zu gewinnen.
[Verwenden von benutzerdefinierten Aktivitäten in einer Data Factory][use-custom-activities] | Dieser Artikel enthält eine exemplarische Vorgehensweise mit schrittweisen Anleitungen zum Erstellen einer benutzerdefinierten Aktivität und deren Verwendung in einer Pipeline.
[Problembehandlung für Data Factory][troubleshoot] | In diesem Artikel wird beschrieben, wie Probleme in Azure Data Factory behoben werden. Sie können die exemplarische Vorgehensweise in diesem Artikel mit "ADFTutorialDataFactory" ausprobieren, indem Sie einen Fehler einbauen (die Tabelle in der Azure SQL-Datenbank löschen). 
[Azure Data Factory-Entwicklerreferenz][developer-reference] | Die Entwicklerreferenz enthält umfassende Referenzinformationen für Cmdlets, JSON-Skripts, Funktionen usw. 

[adf-introduction]: ../data-factory-introduction
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction  
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908

[azure-machine-learning]: http://azure.microsoft.com/de-de/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png


<!--HONumber=35.2-->
