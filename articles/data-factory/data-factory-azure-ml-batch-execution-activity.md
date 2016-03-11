<properties 
	pageTitle="Erstellen von Vorhersagepipelines mithilfe der Aktivität ";Azure Machine Learning-Batchausführung"; | Microsoft Azure" 
	description="Beschreibt das Erstellen von Vorhersagepipelines mithilfe von Azure Data Factory und Azure Machine Learning" 
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
	ms.date="02/06/2016" 
	ms.author="spelluru"/>

# Erstellen von Vorhersagepipelines mithilfe von Azure Machine Learning-Aktivitäten   
## Übersicht

> [AZURE.NOTE] In den Artikeln [Einführung in Azure Data Factory](data-factory-introduction.md) und [Erstellen der ersten Pipeline](data-factory-build-your-first-pipeline.md) finden Sie Informationen für den schnellen Einstieg in den Azure Data Factory-Dienst.

## Einführung

[Azure Machine Learning](https://azure.microsoft.com/documentation/services/machine-learning/) ermöglicht es Ihnen, Predictive Analytics-Lösungen zu erstellen, zu testen und bereitzustellen. Allgemein betrachtet, geschieht dies in drei Schritten:

1. **Erstellen eines Trainingsexperiments**. Sie verwenden dazu Azure ML Studio, eine gemeinsame visuelle Entwicklungsumgebung, mit der Sie ein Predictive Analytics-Modell mithilfe von Trainingsdaten, die Sie bereitstellen, trainieren und testen können.
2. **Konvertierten in ein Vorhersageexperiment**. Sobald Ihr Modell mit vorhandenen Daten trainiert wurde, können Sie es verwenden, um neue Daten zu bewerten. Sie bereiten das Experiment auf die Bewertung vor und optimieren es.
3. **Bereitstellen des Experiments als Webdienst**. Sie können das Bewertungsexperiment als Azure-Webdienst veröffentlichen. Benutzer können Daten über diesen Webdienstendpunkt an Ihr Modell senden und Ergebnisvorhersagen vom Modell empfangen.  

Azure Data Factory ermöglicht die einfache Erstellung von Pipelines, die einen veröffentlichten [Azure Machine Learning][azure-machine-learning]-Webdienst für Predictive Analytics nutzen. Mithilfe der **Batchausführungsaktivität** in einer Azure Data Factory-Pipeline können Sie einen Azure ML-Webdienst aufrufen, um Vorhersagen zu den Daten im Batch zu machen. Weitere Details finden Sie im Abschnitt [Aufrufen eines Azure ML-Webdiensts mit der Batchausführungsaktivität](#invoking-an-azure-ml-web-service-using-the-batch-execution-activity).

Im Laufe der Zeit müssen die Vorhersagemodelle in den Azure ML-Bewertungsexperimenten mit neuen Eingabedatasets neu trainiert werden. Sie können ein Azure ML-Modell über eine Data Factory-Pipeline neu trainieren, indem Sie die folgenden Schritte ausführen:

1. Veröffentlichen Sie das Trainingsexperiment (nicht das Vorhersageexperiment) als Webdienst. Dazu können Sie Azure ML Studio verwenden, wie beim Veröffentlichen des Vorhersageexperiments als Webdienst im vorherigen Szenario.
2. Verwenden Sie die Azure ML-Batchausführungsaktivität, um den Webdienst für das Trainingsexperiment aufzurufen. Grundsätzlich können Sie die Azure ML-Batchausführungsaktivität verwenden, um den Trainingswebdienst und den Bewertungswebdienst aufzurufen. 
  
Nachdem Sie das erneute Trainieren abgeschlossen haben, sollten Sie den Bewertungswebdienst (das Vorhersageexperiment, das als Webdienst verfügbar gemacht wurde) mit dem neu trainierten Modell aktualisieren. Dazu müssen Sie die folgenden Schritte ausführen:

1. Fügen Sie einen nicht standardmäßigen Endpunkt zum Bewertungswebdienst hinzu. Der Standardendpunkt des Webdiensts kann nicht aktualisiert werden, daher müssen Sie einen neuen nicht standardmäßigen Endpunkt mithilfe des klassischen Azure-Portals erstellen. Im Artikel [Erstellen von Endpunkten](../machine-learning/machine-learning-create-endpoint.md) finden Sie konzeptionelle Informationen und entsprechende Schritte.
2. Aktualisieren Sie vorhandene mit Azure ML verknüpfte Dienste für die Bewertung, sodass sie den nicht standardmäßigen Endpunkt verwenden. Sie sollten mit der Verwendung des neuen Endpunkts beginnen, um den Webdienst zu verwenden, der aktualisiert wurde.
3. Verwenden Sie die **Azure ML-Ressourcenaktualisierungsaktivität**, um den Webdienst mit dem neu trainierten Modell zu aktualisieren.  

Einzelheiten finden Sie im Abschnitt [Aktualisieren von Azure ML-Modellen mithilfe der Ressourcenaktualisierungsaktivität](#updating-azure-ml-models-using-the-update-resource-activity).

## Aufrufen eines Azure ML-Webdiensts mit der Batchausführungsaktivität

Mit Azure Data Factory können Sie die Verschiebung und Verarbeitung von Daten orchestrieren und anschließend eine Batchausführung mithilfe von Azure Machine Learning vornehmen. Hierzu müssen Sie folgende Aktionen ausführen:

1. Erstellen Sie einen verknüpften Azure Machine Learning-Dienst. Dazu benötigen Sie Folgendes:
	1. **Anforderungs-URI** für die Batchausführungs-API. Sie erhalten den Anforderungs-URI, indem Sie auf der Webdiensteseite auf den Link **BATCHAUSFÜHRUNG** klicken (wie unten gezeigt).
	1. **API-Schlüssel** für den veröffentlichten Azure Machine Learning-Webdienst. Den API-Schlüssel erhalten Sie durch Klicken auf den Webdienst, den Sie veröffentlicht haben. 
 2. Verwenden Sie die Aktivität **AzureMLBatchExecution**.

	![Machine Learning-Dashboard](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

	![Batch-URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)


### Szenario: Experimente mit Eingaben/Ausgaben für den Webdienst, die auf Daten im Azure-Blob-Speicher verweisen
In diesem Szenario werden mit dem Azure Machine Learning-Webdienst anhand der Daten aus einer Datei eines Azure-Blob-Speichers Vorhersagen erstellt und die Vorhersageergebnisse im Blob-Speicher gespeichert. Das folgende JSON-Skript definiert eine Azure Data Factory-Pipeline mit einer AzureMLBatchExecution-Aktivität. Die Aktivität enthält das Dataset **DecisionTreeInputBlob** als Eingabe und **DecisionTreeResultBlob** als Ausgabe. **DecisionTreeInputBlob** wird mithilfe der JSON-Eigenschaft **webServiceInput** als Eingabe an den Webdienst und **DecisionTreeResultBlob** mithilfe der JSON-Eigenschaft **webServiceOutputs** als Ausgabe an den Webdienst übergeben.

> [AZURE.NOTE] Datasets, auf die die Eigenschaften **webServiceInput** und **webServiceOutputs** (in **typeProperties**) verweisen, müssen auch in den Aktivitäten **inputs** und **outputs** enthalten sein.


	{
	  "name": "PredictivePipeline",
	  "properties": {
	    "description": "use AzureML model",
	    "activities": [
	      {
	        "name": "MLActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "prediction analysis on batch input",
	        "inputs": [
	          {
	            "name": "DecisionTreeInputBlob"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "DecisionTreeResultBlob"
	          }
	        ],
	        "linkedServiceName": "MyAzureMLLinkedService",
            "typeProperties":
            {
                "webServiceInput": "DecisionTreeInputBlob ",
                "webServiceOutputs": {
                    "output1": "DecisionTreeResultBlob "
                }                
            },
	        "policy": {
	          "concurrency": 3,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        }
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}

> [AZURE.NOTE] Nur Eingaben und Ausgaben der AzureMLBatchExecution-Aktivität können als Parameter an den Webdienst übergeben werden. Im JSON-Codeausschnitt oben ist beispielsweise "DecisionTreeInputBlob" eine Eingabe für die AzureMLBatchExecution-Aktivität, die über den webServiceInput-Parameter als Eingabe an den Webdienst übergeben wird.

### Beispiel

Dieses Beispiel verwendet Azure Storage zum Speichern der Ein- und Ausgabedaten.

Es wird empfohlen, dass Sie zunächst das Lernprogramm [Erstellen der ersten Pipeline mit Azure Data Factory][adf-build-1st-pipeline] durchlaufen, bevor Sie sich mit diesem Beispiel beschäftigen und mit dem Data Factory-Editor Data Factory-Artefakte (verknüpfte Dienste, Datasets, Pipeline) erstellen.
 

1. Erstellen Sie einen **verknüpften Dienst** für **Azure Storage**. Wenn sich die Ein- und Ausgabedateien in verschiedenen Speicherkonten befinden, benötigen Sie zwei verknüpfte Dienste. Hier folgt ein JSON-Beispiel:

		{
		  "name": "StorageLinkedService",
		  "properties": {
		    "type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		  }
		}

2. Erstellen Sie das **Eingabe****dataset** für Azure Data Factory. Beachten Sie, dass es im Gegensatz zu einigen anderen Data Factory-Datasets die beiden Werte **folderPath** und **fileName** enthalten muss. Sie können die Partitionierung verwenden, damit jede Batchausführung (jeder Datenslice) eindeutige Ein- und Ausgabedateien verarbeitet oder erzeugt. Sie müssen wahrscheinlich einige Upstream-Aktivitäten einbeziehen, um die Eingabe in das CSV-Dateiformat umzuwandeln und sie im Speicherkonto für die einzelnen Datenslices abzulegen. In diesem Fall würden Sie die im folgenden Beispiel gezeigten Einstellungen **external** und **externalData** nicht einbeziehen, und Ihr "DecisionTreeInputBlob" entspräche dem Ausgabedataset einer anderen Aktivität.

		{
		  "name": "DecisionTreeInputBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/input",
		      "fileName": "in.csv",
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "external": true,
		    "availability": {
		      "frequency": "Day",
		      "interval": 1
		    },
		    "policy": {
		      "externalData": {
		        "retryInterval": "00:01:00",
		        "retryTimeout": "00:10:00",
		        "maximumRetry": 3
		      }
		    }
		  }
		}
	
	Die CSV-Eingabedatei muss die Spaltenkopfzeile enthalten. Bei Verwenden der **Kopieraktivität** zum Erstellen/Verschieben der CSV-Datei in den Blobspeicher müssen Sie die Senkeneigenschaft **BlobWriterAddHeader** auf **true** festlegen. Beispiel:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Wenn die CSV-Datei nicht die Kopfzeilen enthält, wird möglicherweise folgender Fehler angezeigt: **Fehler in Aktivität: Fehler beim Lesen der Zeichenfolge. Unerwartetes Token: StartObject. Pfad '', Zeile 1, Position 1**.
3. Erstellen Sie das **Ausgabe****dataset** für Azure Data Factory. In diesem Beispiel wird die Partitionierung verwendet, um für die Ausführung der einzelnen Datenslices einen eindeutigen Ausgabepfad zu erstellen. Ohne diesen Schritt würde die Aktivität die Datei überschreiben.

		{
		  "name": "DecisionTreeResultBlob",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "azuremltesting/scored/{folderpart}/",
		      "fileName": "{filepart}result.csv",
		      "partitionedBy": [
		        {
		          "name": "folderpart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMdd"
		          }
		        },
		        {
		          "name": "filepart",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "HHmmss"
		          }
		        }
		      ],
		      "format": {
		        "type": "TextFormat",
		        "columnDelimiter": ","
		      }
		    },
		    "availability": {
		      "frequency": "Day",
		      "interval": 15
		    }
		  }
		}

4. Erstellen Sie einen **verknüpften Dienst** vom Typ **AzureMLLinkedService**, der den API-Schlüssel und die URL für die Modellbatchausführung bereitstellt.
		
		{
		  "name": "MyAzureMLLinkedService",
		  "properties": {
		    "type": "AzureML",
		    "typeProperties": {
		      "mlEndpoint": "https://[batch execution endpoint]/jobs",
		      "apiKey": "[apikey]"
		    }
		  }
		}
5. Erstellen Sie abschließend eine Pipeline, die eine **AzureMLBatchExecution**-Aktivität enthält. Dadurch werden der Speicherort der Eingabedatei aus Ihren Eingabedatasets abgerufen, die Azure Machine Learning-Batchausführungs-API aufgerufen und die Batchausführungsausgabe in das im Ausgabedataset angegebene Blob kopiert. 

	> [AZURE.NOTE] Die AzureMLBatchExecution-Aktivität kann über keine oder mehrere Eingaben sowie über eine oder mehrere Ausgaben verfügen.

		{
		  "name": "PredictivePipeline",
		  "properties": {
		    "description": "use AzureML model",
		    "activities": [
		      {
		        "name": "MLActivity",
		        "type": "AzureMLBatchExecution",
		        "description": "prediction analysis on batch input",
		        "inputs": [
		          {
		            "name": "DecisionTreeInputBlob"
		          }
		        ],
		        "outputs": [
		          {
		            "name": "DecisionTreeResultBlob"
		          }
		        ],
		        "linkedServiceName": "MyAzureMLLinkedService",
                "typeProperties":
                {
                    "webServiceInput": "DecisionTreeInputBlob ",
                    "webServiceOutputs": {
                        "output1": "DecisionTreeResultBlob "
                    }                
                },
		        "policy": {
		          "concurrency": 3,
		          "executionPriorityOrder": "NewestFirst",
		          "retry": 1,
		          "timeout": "02:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z"
		  }
		}

	Datum und Uhrzeit von **Start** und **Ende** müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. Die Zeit für **end** ist optional. Wenn Sie für die **end**-Eigenschaft keinen Wert angeben, wird sie als "**start + 48 Stunden**" berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die **end**-Eigenschaft **9999-09-09** an. Informationen zu JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz](https://msdn.microsoft.com/library/dn835050.aspx).

	> [AZURE.NOTE] Das Festlegen der Eingaben für die AzureMLBatchExecution-Aktivität erfolgt optional.

### Szenario: Experimente mit Reader- und Writer-Modulen zum Verweisen auf Daten in verschiedenen Speichern

Ein weiteres gängiges Szenario beim Erstellen von Azure ML-Experimenten ist die Verwendung von Reader- und Writer-Modulen. Das Reader-Modul wird verwendet, um Daten in einem Experiment zu laden, während mit dem Writer-Modul Daten aus den Experimenten gespeichert werden. Ausführliche Informationen zu Reader- und Writer-Modulen finden Sie in den Themen [Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) und [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) in der MSDN Library.

Bei Verwendung der Reader- und Writer-Module empfiehlt es sich, einen Webdienstparameter für jede Eigenschaft dieser Module zu verwenden. Durch diese Webparameter können Sie die Werte zur Laufzeit konfigurieren. Sie können z. B. ein Experiment mit einem Reader-Modul erstellen, das eine Azure SQL-Datenbank mit dem Namen "XXX.database.windows.net" verwendet. Nach der Bereitstellung des Webdiensts sollen die Nutzer des Webdiensts einen weiteren Azure SQL Server mit dem Namen "YYY.database.windows.net" angeben können. Durch Verwendung eines Webdienstparameters wird ermöglicht, dass dieser Wert konfiguriert werden kann.

> [AZURE.NOTE] Eingaben und Ausgaben für den Webdienst unterscheiden sich von Webdienstparametern. Im ersten Szenario haben Sie gesehen, wie Eingaben und Ausgaben für einen Azure ML-Webdienst angegeben werden können. In diesem Szenario übergeben Sie Parameter für einen Webdienst, die Eigenschaften von Reader- und Writer-Modulen entsprechen.

Betrachten wir nun ein Szenario für die Verwendung von Webdienstparametern. Sie haben einen Azure Machine Learning-Webdienst bereitgestellt, bei dem Daten mithilfe eines Reader-Moduls aus einer der von Azure Machine Learning unterstützten Datenquellen (z. B. Azure SQL-Datenbank) gelesen werden. Nach der Batchausführung werden die Ergebnisse mit einem Writer-Modul (Azure SQL-Datenbank) geschrieben. In den Experimenten sind keine Eingaben und Ausgaben für den Webdienst definiert. In diesem Fall wird empfohlen, dass Sie die entsprechenden Webdienstparameter für das Reader- und das Writer-Modul einrichten. Dadurch können das Reader- und Writer-Modul bei Verwendung der AzureMLBatchExecution-Aktivität konfiguriert werden. Sie geben die Webdienstparameter wie folgt im Abschnitt **globalParameters** im JSON-Code der Aktivität an.


	"typeProperties": {
		"globalParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}

Sie können auch [Data Factory-Funktionen ](https://msdn.microsoft.com/library/dn835056.aspx) zum Übergeben von Werten für die Webdienstparameter verwenden, wie im folgenden Beispiel gezeigt:

	"typeProperties": {
    	"globalParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE] Bei Webdienstparametern wird Groß-/Kleinschreibung unterschieden, weshalb Sie sicherstellen müssen, dass die Namen, die Sie im JSON-Code der Aktivität angeben, denjenigen entsprechen, die vom Webdienst verfügbar gemacht werden.

### Lesen von Daten aus mehreren Dateien im Azure-Blob mithilfe eines Reader-Moduls
Big Data-Pipelines (Pig, Hive usw.) können eine oder mehrere Ausgabedateien ohne Erweiterung generieren. Wenn Sie beispielsweise eine externe Hive-Tabelle angeben, können die Daten für die externe Hive-Tabelle im Azure-Blob-Speicher unter dem Namen "000000\_0" gespeichert werden. Mithilfe des Reader-Moduls in einem Experiment können Sie mehrere Dateien lesen und für Vorhersagen verwenden.

Wenn Sie das Reader-Modul in einem Azure Machine Learning-Experiment verwenden, können Sie das Azure-Blob als Eingabe angeben. Bei den Dateien im Azure-Blobspeicher kann es sich um die Ausgabedateien (z. B. 000000\_0) handeln, die von einem Pig- und Hive-Skript unter HDInsight erstellt werden. Mit dem Reader-Modul können Sie Dateien (ohne Erweiterung) lesen, indem Sie die Eigenschaft **Path to container, directory or blob** des Reader-Moduls so konfigurieren, dass sie auf den Container/Ordner mit den Dateien verweist (wie unten dargestellt). Das Sternchen (\*) **gibt an, dass alle Dateien im Container/Ordner (d. h. data/aggregateddata/year=2014/month=6/\*)** als Teil des Experiments gelesen werden.

![Azure-Blobeigenschaften](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)


### Beispiel 
#### Pipeline mit "AzureMLBatchExecution"-Aktivität mit Webdienstparametern

	{
	  "name": "MLWithSqlReaderSqlWriter",
	  "properties": {
	    "description": "Azure ML model with sql azure reader/writer",
	    "activities": [
	      {
	        "name": "MLSqlReaderSqlWriterActivity",
	        "type": "AzureMLBatchExecution",
	        "description": "test",
	        "inputs": [
	          {
	            "name": "MLSqlInput"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "MLSqlOutput"
	          }
	        ],
	        "linkedServiceName": "MLSqlReaderSqlWriterDecisionTreeModel",
            "typeProperties":
            {
                "webServiceInput": "MLSqlInput",
                "webServiceOutputs": {
                    "output1": "MLSqlOutput"
                }
	          	"globalParameters": {
	            	"Database server name": "<myserver>.database.windows.net",
		            "Database name": "<database>",
		            "Server user account name": "<user name>",
		            "Server user account password": "<password>"
	          	}              
            },
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        },
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}
 
Im obigen JSON-Beispiel:

- Der bereitgestellte Azure Machine Learning-Webdienst verwendet ein Reader- und ein Writer-Modul zum Lesen und Schreiben von Daten aus einer und in eine Azure SQL-Datenbank. Der Webdienst stellt die vier folgenden Parameter bereit: "Database server name", "Database name", "Server user account name" und "Server user account password".  
- Datum und Uhrzeit von **Start** und **Ende** müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. Die Zeit für **end** ist optional. Wenn Sie für die **end**-Eigenschaft keinen Wert angeben, wird sie als "**start + 48 Stunden**" berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die **end**-Eigenschaft **9999-09-09** an. Informationen zu JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz](https://msdn.microsoft.com/library/dn835050.aspx).

### Andere Szenarien

#### Webdienst erfordert keine Eingabe

Mit Webdiensten zur Azure ML-Batchausführung können beliebige Workflows ausgeführt werden, z. B. R- oder Python-Skripts, keine Eingaben erfordern. Das Experiment könnte auch mit einem Reader-Modul konfiguriert werden, das keine GlobalParameters verfügbar macht. In diesem Fall würde die AzureMLBatchExecution-Aktivität wie folgt konfiguriert werden:

	{
        "name": "scoring service",
        "type": "AzureMLBatchExecution",
        "outputs": [
            {
                "name": "myBlob"
            }
        ],
        "typeProperties": {
            "webServiceOutputs": {
                "output1": "myBlob"
            }              
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },
   

#### Webdienst erfordert keine Ein-/Ausgabe
Für den Webdienst zur Azure ML-Batchausführung ist möglicherweise keine Webdienst-Ausgabe konfiguriert. In diesem Beispiel ist weder eine Webdienst-Ein-/Ausgabe, noch sind GlobalParameters konfiguriert. Beachten Sie, dass immer noch eine Ausgabe in der Aktivität selbst konfiguriert ist, aber nicht als „webServiceOutput“.

	{
        "name": "retraining",
        "type": "AzureMLBatchExecution",
        "outputs": [
            {
                "name": "placeholderOutputDataset"
            }
        ],
        "typeProperties": {
         },
        "linkedServiceName": "mlEndpoint",
        "policy": {
            "concurrency": 1,
            "executionPriorityOrder": "NewestFirst",
            "retry": 1,
            "timeout": "02:00:00"
        }
    },

#### Der Webdienst verwendet Reader und Writer, und die Aktivität wird nur dann ausgeführt, wenn andere Aktivitäten erfolgreich ausgeführt wurden.

Die Reader- und Writer-Module des Azure ML-Webdiensts könnten für die Ausführung mit oder ohne GlobalParameters konfiguriert werden. Aber möglicherweise möchten Sie die Dienstaufrufe in eine Verarbeitungspipeline einbetten, die Datasetabhängigkeiten verwendet, um den Dienst nur dann aufzurufen, wenn bestimmte Upstreamverarbeitung abgeschlossen ist, und dann nach der Batchausführung eine andere Aktion auszulösen. In diesem Fall können Sie die Abhängigkeiten mit Eingaben und Ausgaben der Aktivität ausdrücken, ohne diese als Webdienstein- oder Ausgaben zu benennen.

	{
	    "name": "retraining",
	    "type": "AzureMLBatchExecution",
	    "inputs": [
	        {
	            "name": "upstreamData1"
	        },
	        {
	            "name": "upstreamData2"
	        }
	    ],
	    "outputs": [
	        {
	            "name": "downstreamData"
	        }
	    ],
	    "typeProperties": {
	     },
	    "linkedServiceName": "mlEndpoint",
	    "policy": {
	        "concurrency": 1,
	        "executionPriorityOrder": "NewestFirst",
	        "retry": 1,
	        "timeout": "02:00:00"
	    }
	},

Die **Vorteile** sind:

-   Wenn Ihr Experimentendpunkt einen „webServiceInput“ verwendet, wird er durch ein Blobdataset dargestellt und ist sowohl in den Aktivitätseingaben als auch der webServiceInput-Eigenschaft enthalten. Andernfalls wird die webServiceInput-Eigenschaft weggelassen. 
-   Wenn Ihr Experimentendpunkt „webServiceOutput(s)“ verwendet, werden sie von Blobdatasets dargestellt und sind sowohl in den Aktivitätsausgaben als auch der webServicepOutputs-Eigenschaft enthalten (zugeordnet von jedem Namen jeder Ausgabe im Experiment). Andernfalls wird die webServiceOutputs-Eigenschaft weggelassen.
-   Wenn Ihr Experimentendpunkt „globalParameter(s)“ verfügbar macht, werden sie in der globalParameters-Eigenschaft der Aktivität als Schlüsselwertpaare angegeben. Andernfalls wird die globalParameters-Eigenschaft weggelassen. Bei Schlüsseln wird Groß-/Kleinschreibung unterschieden. [Azure Data Factory-Funktionen](data-factory-scheduling-and-execution.md#data-factory-functions-reference) können in den Werten verwendet werden. 
- Weitere Datasets können in den Eigenschaften „inputs“ und „outputs“ der Aktivität enthalten sein, ohne das in „typeProperties“ der Aktivität darauf verwiesen wird. Diese bestimmen die Ausführung mit Slice-Abhängigkeiten, werden aber von der Aktivität „AzureMLBatchExecution“ ignoriert. 


## Aktualisieren von Azure ML-Modellen mithilfe der Ressourcenaktualisierungsaktivität
Im Laufe der Zeit müssen die Vorhersagemodelle in den Azure ML-Bewertungsexperimenten mit neuen Eingabedatasets neu trainiert werden. Wenn Sie mit dem erneuten Trainieren fertig sind, sollten Sie den Bewertungswebdienst mit dem neu trainierten ML-Modell aktualisieren. Typische Schritte, um das erneute Trainieren und das Aktualisieren von Azure ML-Modellen über Webdienste zu ermöglichen:

1. Erstellen Sie ein Experiment in [Azure ML Studio](https://studio.azureml.net). 
2. Wenn Sie mit dem Modell zufrieden sind, verwenden Sie Azure ML Studio, um Webdienste für das **Trainingsexperiment** und das Bewertungs-/**Vorhersageexperiment** zu veröffentlichen.

In der folgenden Tabelle werden die in diesem Beispiel verwendeten Webdienste beschrieben. Details finden Sie unter [Programmgesteuertes erneutes Trainieren von Machine Learning-Modellen](../machine-learning/machine-learning-retrain-models-programmatically.md).

| Webdiensttyp | description 
| :------------------ | :---------- 
| **Trainingswebdienst** | Empfängt Trainingsdaten und erzeugt trainierte Modelle. Die Ausgabe des erneuten Trainierens ist eine iLearner-Datei in einem Azure-Blobspeicher. Der **Standardendpunkt** wird automatisch erstellt, wenn Sie das Trainingsexperiment als Webdienst veröffentlichen. Sie können weitere Endpunkte erstellen, aber im Beispiel wird nur der Standardendpunkt verwendet. |
| **Bewertungswebdienst** | Empfängt Datenbeispiele ohne Bezeichnung und macht Vorhersagen. Die Ausgabe der Vorhersage kann verschiedene Formen aufweisen, z. B. eine CSV-Datei oder Zeilen in einer Azure SQL-Datenbank, dies ist abhängig von der Konfiguration des Experiments. Der Standardendpunkt wird automatisch erstellt, wenn Sie das Vorhersageexperiment als Webdienst veröffentlichen. Sie müssen einen zweiten **nicht standardmäßigen und aktualisierbaren Endpunkt** mithilfe des [klassischen Azure-Portals](https://manage.windowsazure.com) erstellen. Sie können weitere Endpunkte erstellen, aber im Beispiel wird nur der nicht standardmäßige aktualisierbare Endpunkt verwendet. Die Schritte sind im Artikel [Erstellen von Endpunkten](../machine-learning/machine-learning-create-endpoint.md) aufgeführt.       
 
Die folgende Abbildung zeigt die Beziehung zwischen Trainings- und Bewertungsendpunkten in Azure ML.

![Webdienste](./media/data-factory-azure-ml-batch-execution-activity/web-services.png)


Sie können die **Azure ML-Batchausführungsaktivität** verwenden, um den **Trainingswebdienst** aufzurufen. Dies gleicht dem Aufrufen eines Azure ML-Webdiensts (Bewertungswebdienst) für Bewertungsdaten. In den oben aufgeführten Abschnitten wird detailliert beschrieben, wie ein Azure ML-Webdienst über eine Azure Data Factory-Pipeline aufgerufen wird.
  
Sie können den **Bewertungswebdienst** aufrufen, indem Sie die **Azure ML-Ressourcenaktualisierungsaktivität** verwenden, um den Webdienst mit dem neu trainierten Modell zu aktualisieren. Wie in der Tabelle oben bereits erwähnt, müssen Sie den nicht standardmäßigen aktualisierbaren Endpunkt erstellen und verwenden. Sie sollten zudem alle vorhandenen verknüpften Dienste in der Data Factory aktualisieren, sodass sie den nicht standardmäßigen Endpunkt verwenden, damit sie immer das aktuellste neu trainierte Modell verwenden.

Das folgende Szenario bietet weitere Informationen anhand eines Beispiels für das erneute Trainieren und Aktualisieren von Azure ML-Modellen über eine Azure Data Factory-Pipeline.
 
### Szenario: Erneutes Trainieren und Aktualisieren eines Azure ML-Modells
Dieser Abschnitt enthält eine Beispielpipeline, die die **Azure ML-Batchausführungsaktivität** verwendet, um ein Modell neu zu trainieren, und die die **Azure ML-Ressourcenaktualisierungsaktivität** verwendet, um das Modell im Bewertungswebdienst zu aktualisieren. Darüber hinaus enthält er JSON-Codeausschnitte für alle verknüpften Dienste, Datasets und Pipelines im Beispiel.

Im Folgenden ist die Diagrammansicht der Beispielpipeline dargestellt. Wie Sie sehen können, übernimmt die Azure ML-Batchausführungsaktivität die Trainingseingabe und erzeugt eine Trainingsausgabe (iLearner-Datei). Die Azure ML-Ressourcenaktualisierungsaktivität verwendet die Trainingsausgabe und aktualisiert das Modell im Bewertungswebdienst-Endpunkt. Die Ressourcenaktualisierungsaktivität erzeugt keine Ausgabe. „placeholderBlob“ ist nur ein Platzhalter für ein Ausgabedataset, das für den Azure Data Factory-Dienst zum Ausführen der Pipeline erforderlich ist.

![Pipelinediagramm](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)


#### Mit Azure-Blobspeicher verknüpfter Dienst:
Der Azure-Speicher enthält die folgenden Daten:

- Trainingsdaten: Dies sind die Eingabedaten für den Azure ML-Trainingswebdienst.  
- iLearner-Datei: Dies ist die Ausgabe des Azure ML-Trainingswebdiensts. Es ist darüber hinaus die Eingabe für die Ressourcenaktualisierungsaktivität.  
   
Dies ist die JSON-Beispieldefinition des verknüpften-Diensts:

	{
		"name": "StorageLinkedService",
	  	"properties": {
	    	"type": "AzureStorage",
			"typeProperties": {
	    		"connectionString": "DefaultEndpointsProtocol=https;AccountName=name;AccountKey=key"
			}
		}
	}


#### Trainingseingabedataset:
Das folgende Dataset stellt die Trainingseingabedaten für den Azure ML-Trainingswebdienst dar. Die Azure ML-Batchausführungsaktivität verwendet dieses Dataset als Eingabe.

	{
	    "name": "trainingData",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "labeledexamples",
	            "fileName": "labeledexamples.arff",
	            "format": {
	                "type": "TextFormat"
	            }
	        },
	        "availability": {
	            "frequency": "Week",
	            "interval": 1
	        },
	        "policy": {          
	            "externalData": {
	                "retryInterval": "00:01:00",
	                "retryTimeout": "00:10:00",
	                "maximumRetry": 3
	            }
	        }
	    }
	}

#### Trainingsausgabedataset:
Das folgende Dataset stellt die iLearner-Ausgabedatei des Azure ML-Trainingswebdiensts dar. Die Azure ML-Batchausführungsaktivität erzeugt dieses Dataset. Dieses Dataset ist darüber hinaus die Eingabe für die Azure ML-Ressourcenaktualisierungsaktivität.

	{
	    "name": "trainedModelBlob",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "trainingoutput",
	            "fileName": "model.ilearner",
	            "format": {
	                "type": "TextFormat"
	            }
	        },
	        "availability": {
	            "frequency": "Week",
	            "interval": 1
	        }
	    }
	}

#### Verknüpfter Dienst für den Azure ML-Trainingsendpunkt 
Der folgende JSON-Codeausschnitt definiert einen mit Azure Machine Learning verknüpften Dienst, der auf den Standardendpunkt des Trainingswebdiensts verweist.

	{	
		"name": "trainingEndpoint",
	  	"properties": {
	    	"type": "AzureML",
	    	"typeProperties": {
	    		"mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--training experiment--/jobs",
	      		"apiKey": "myKey"
	    	}
	  	}
	}

Führen Sie in **Azure ML Studio** folgende Schritte aus, um Werte für **mlEndpoint** und **apiKey** abzurufen:

1. Klicken Sie im linken Menü auf **WEB SERVICES**.
2. Klicken Sie in der Liste der Webdienste auf den **Trainingswebdienst**. 
3. Klicken Sie neben dem Textfeld **API-Schlüssel** auf „Kopieren“, um den API-Schlüssel in die Zwischenablage zu kopieren. Fügen Sie den Schlüssel in den Data Factory-JSON-Editor ein.
4. Klicken Sie in **Azure ML Studio** auf den Link **BATCH-AUSFÜHRUNG**, kopieren Sie den **Anforderungs-URI** aus dem Abschnitt **Anforderung**, und fügen Sie ihn in den Data Factory-JSON-Editor ein.   


#### Verknüpfter Dienst für den aktualisierbaren Azure ML-Bewertungsendpunkt
Der folgende JSON-Codeausschnitt definiert einen mit Azure Machine Learning verknüpften Dienst, der auf den nicht standardmäßigen aktualisierbaren Endpunkt des Bewertungswebdiensts verweist.

	{
	    "name": "updatableScoringEndpoint2",
	    "properties": {
	        "type": "AzureML",
	        "typeProperties": {
	            "mlEndpoint": "https://ussouthcentral.services.azureml.net/workspaces/xxx/services/--scoring experiment--/jobs",
	            "apiKey": "endpoint2Key",
	            "updateResourceEndpoint": "https://management.azureml.net/workspaces/xxx/webservices/--scoring experiment--/endpoints/endpoint2"
	        }
	    }
	}


Vor dem Erstellen und Bereitstellen eines mit Azure ML verknüpften Diensts führen Sie die Schritte im Artikel [Erstellen von Endpunkten](../machine-learning/machine-learning-create-endpoint.md) aus, um einen zweiten (nicht standardmäßigen und aktualisierbaren) Endpunkt für den Bewertungswebdienst zu erstellen.

Klicken Sie nach dem Erstellen des nicht standardmäßigen aktualisierbaren Endpunkts auf **BATCH-AUSFÜHRUNG**, um den URI-Wert für die JSON-Eigenschaft **mlEndpoint** abzurufen. Klicken Sie dann auf den Link **RESSOURCE AKTUALISIEREN**, um den URI-Wert für die JSON-Eigenschaft **updateResourceEndpoint** abzurufen. Den API-Schlüssel finden Sie auf der Seite des Endpunkts (unten rechts).

![Aktualisierbarer Endpunkt](./media/data-factory-azure-ml-batch-execution-activity/updatable-endpoint.png)

 
#### Platzhalter-Ausgabedataset:
Die Azure ML-Ressourcenaktualisierungsaktivität generiert keine Ausgabe, aber in Azure Data Factory ist für die Pipeline ein Ausgabedataset erforderlich. Daher wird in diesem Beispiel ein Dummy-/Platzhalterdataset verwendet.

	{
	    "name": "placeholderBlob",
	    "properties": {
	        "availability": {
	            "frequency": "Week",
	            "interval": 1
	        },
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "folderPath": "any",
	            "format": {
	                "type": "TextFormat"
	            }
	        }
	    }
	}


#### Pipeline
Die Pipeline weist zwei Aktivitäten auf: **AzureMLBatchExecution** und **AzureMLUpdateResource**. Die Azure ML-Batchausführungsaktivität verwendet die Trainingsdaten als Eingabe und erzeugt eine iLearner-Datei als Ausgabe. Die Aktivität ruft den Trainingswebdienst (das als Webdienst bereitgestellte Trainingsexperiment) mit den Trainingseingabedaten auf und empfängt die iLearner-Datei vom Webdienst. „placeholderBlob“ ist nur ein Platzhalter für ein Ausgabedataset, das für den Azure Data Factory-Dienst zum Ausführen der Pipeline erforderlich ist.

![Pipelinediagramm](./media/data-factory-azure-ml-batch-execution-activity/update-activity-pipeline-diagram.png)


	{
	    "name": "pipeline",
	    "properties": {
	        "activities": [
	            {
	                "name": "retraining",
	                "type": "AzureMLBatchExecution",
	                "inputs": [
	                    {
	                        "name": "trainingData"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "trainedModelBlob"
	                    }
	                ],
	                "typeProperties": {
	                    "webServiceInput": "trainingData",
	                    "webServiceOutputs": {
	                        "output1": "trainedModelBlob"
	                    }              
	                 },
	                "linkedServiceName": "trainingEndpoint",
	                "policy": {
	                    "concurrency": 1,
	                    "executionPriorityOrder": "NewestFirst",
	                    "retry": 1,
	                    "timeout": "02:00:00"
	                }
	            },
	            {
	                "type": "AzureMLUpdateResource",
	                "typeProperties": {
	                    "trainedModelName": "Training Exp for ADF ML [trained model]",
	                    "trainedModelDatasetName" :  "trainedModelBlob"
	                },
	                "inputs": [
	                    {
	                        "name": "trainedModelBlob"
	                    }
	                ],
	                "outputs": [
	                    {
	                        "name": "placeholderBlob"
	                    }
	                ],
	                "policy": {
	                    "timeout": "01:00:00",
	                    "concurrency": 1,
	                    "retry": 3
	                },
	                "name": "AzureML Update Resource",
	                "linkedServiceName": "updatableScoringEndpoint2"
	            }
	        ],
	    	"start": "2015-02-13T00:00:00Z",
	   		"end": "2015-02-14T00:00:00Z"
	    }
	}


### Die Module "Reader" und "Writer"

Ein häufiges Szenario für Webdienstparameter ist die Verwendung der Azure SQL-Module "Reader" und "Writer". Das Modul "Reader" dient zum das Laden von Daten in ein Experiment aus Datenverwaltungsdiensten außerhalb von Azure Machine Learning Studio. Das Modul "Writer" wird zum Speichern von Daten aus Ihren Experimenten in Datenverwaltungsdiensten außerhalb von Azure Machine Learning Studio verwendet.

Ausführliche Informationen zu Azure-Blob/Azure SQL Reader/Writer finden Sie in den Themen [Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) und [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) in der MSDN Library. Im Beispiel im vorherigen Abschnitt wurden die Azure-Blobmodule "Reader" und "Writer" verwendet. In diesem Abschnitt werden die Azure SQL-Module "Reader" und "Writer" erläutert.


## Häufig gestellte Fragen

**F:** Ich verfüge über mehrere Dateien, die von meinen Big Data-Pipelines erstellt werden. Kann ich die AzureMLBatchExecution-Aktivität zum Bearbeiten aller Dateien verwenden?

**A:** Ja. Ausführliche Informationen dazu finden Sie im Abschnitt **Lesen von Daten aus mehreren Dateien im Azure-Blob mithilfe eines Reader-Moduls**.

## Azure ML-Batchbewertungsaktivität
Wenn Sie derzeit die Aktivität **AzureMLBatchScoring** für die Integration in Azure Machine Learning verwenden, empfiehlt es sich, die neueste **AzureMLBatchExecution**-Aktivität zu verwenden.

Die AzureMLBatchExecution-Aktivität wird in den Azure SDK- und Azure PowerShell-Versionen von August 2015 eingeführt.

Wenn Sie weiterhin die AzureMLBatchScoring-Aktivität verwenden möchten, lesen Sie weiterhin diesen Abschnitt.

### Azure ML-Batchbewertungsaktivität mit Azure Storage für Ein-/Ausgabe 

	{
	  "name": "PredictivePipeline",
	  "properties": {
	    "description": "use AzureML model",
	    "activities": [
	      {
	        "name": "MLActivity",
	        "type": "AzureMLBatchScoring",
	        "description": "prediction analysis on batch input",
	        "inputs": [
	          {
	            "name": "ScoringInputBlob"
	          }
	        ],
	        "outputs": [
	          {
	            "name": "ScoringResultBlob"
	          }
	        ],
	        "linkedServiceName": "MyAzureMLLinkedService",
	        "policy": {
	          "concurrency": 3,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        }
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}

### Webdienstparameter
Fügen Sie den Abschnitt **typeProperties** dem Abschnitt **AzureMLBatchScoringActivty** im JSON-Code der Pipeline hinzu, um in diesem Abschnitt, wie im folgenden Beispiel gezeigt, Werte für Webdienstparameter hinzuzufügen:

	"typeProperties": {
		"webServiceParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


Sie können auch [Data Factory-Funktionen ](https://msdn.microsoft.com/library/dn835056.aspx) zum Übergeben von Werten für die Webdienstparameter verwenden, wie im folgenden Beispiel gezeigt:

	"typeProperties": {
    	"webServiceParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \\'{0:yyyy-MM-dd HH:mm:ss}\\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE] Bei Webdienstparametern wird Groß-/Kleinschreibung unterschieden, weshalb Sie sicherstellen müssen, dass die Namen, die Sie im JSON-Code der Aktivität angeben, denjenigen entsprechen, die vom Webdienst verfügbar gemacht werden.

## Siehe auch

- [Azure-Blogbeitrag: Erste Schritte mit Azure Data Factory und Azure Machine Learning](https://azure.microsoft.com/blog/getting-started-with-azure-data-factory-and-azure-machine-learning-4/)





[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/


 

<!---HONumber=AcomDC_0224_2016-->
