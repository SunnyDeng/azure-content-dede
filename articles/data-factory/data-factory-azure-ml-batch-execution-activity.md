<properties 
	pageTitle="Erstellen von Vorhersagepipelines mithilfe der Aktivität ";Azure Machine Learning-Batchausführung"; | Microsoft Azure"
	description="Erläuterungen zum Erstellen von Vorhersagepipelines mithilfe von Data Factory und Azure Machine Learning"
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
	ms.date="08/24/2015"
	ms.author="spelluru"/>

# Erstellen von Vorhersagepipelines mithilfe der Aktivität "Azure Machine Learning-Batchausführung"   
## Übersicht

> [AZURE.NOTE]In den Artikeln [Einführung in Azure Data Factory](data-factory-introduction.md) und [Erstellen der ersten Pipeline](data-factory-build-your-first-pipeline.md) finden Sie Informationen für den schnellen Einstieg in den Azure Data Factory-Dienst.

Azure Data Factory ermöglicht die einfache Erstellung von Pipelines, die einen veröffentlichten [Azure Machine Learning][azure-machine-learning]-Webdienst für Predictive Analytics nutzen. Mithilfe von Azure Data Factory können Sie Big Data-Pipelines (z. B. Pig oder Hive) nutzen, um die Daten zu verarbeiten, die Sie aus verschiedenen Datenquellen erfasst haben. Zudem können Sie mithilfe der Azure Machine Learning-Webdienste Vorhersagen für die Daten im Batch vornehmen.

Mit Azure Data Factory können Sie die Verschiebung und Verarbeitung von Daten orchestrieren und anschließend eine Batchausführung mithilfe von Azure Machine Learning vornehmen. Hierzu müssen Sie folgende Aktionen ausführen:

1. Erstellen Sie einen verknüpften Azure Machine Learning-Dienst. Dazu benötigen Sie Folgendes:
	1. **Anforderungs-URI** für die Batchausführungs-API. Sie erhalten den Anforderungs-URI, indem Sie auf der Webdiensteseite auf den Link **BATCHAUSFÜHRUNG** klicken (wie unten gezeigt).
	1. **API-Schlüssel** für den veröffentlichten Azure Machine Learning-Webdienst. Den API-Schlüssel erhalten Sie durch Klicken auf den Webdienst, den Sie veröffentlicht haben. 
 2. Verwenden Sie die Aktivität **AzureMLBatchExecution**.

	![Machine Learning-Dashboard](./media/data-factory-azure-ml-batch-execution-activity/AzureMLDashboard.png)

	![Batch-URI](./media/data-factory-azure-ml-batch-execution-activity/batch-uri.png)


## Szenario: Experimente mit Eingaben/Ausgaben für den Webdienst, die auf Daten im Azure-Blob-Speicher verweisen
In diesem Szenario werden mit dem Azure Machine Learning-Webdienst anhand der Daten aus einer Datei eines Azure-Blob-Speichers Vorhersagen erstellt und die Vorhersageergebnisse im Blob-Speicher gespeichert. Das folgende JSON-Skript definiert eine Azure Data Factory-Pipeline mit einer AzureMLBatchExecution-Aktivität. Die Aktivität enthält das Dataset **DecisionTreeInputBlob** als Eingabe und **DecisionTreeResultBlob** als Ausgabe. **DecisionTreeInputBlob** wird mithilfe der JSON-Eigenschaft **webServiceInput** als Eingabe an den Webdienst und **DecisionTreeResultBlob** mithilfe der JSON-Eigenschaft **webServiceOutputs** als Ausgabe an den Webdienst übergeben. Es können nur Datasets, die Eingaben und Ausgaben für die Aktivität sind, als Ein- und Ausgaben für den Webdienst übergeben werden.


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

> [AZURE.NOTE]Nur Eingaben und Ausgaben der AzureMLBatchExecution-Aktivität können als Parameter an den Webdienst übergeben werden. Im JSON-Codeausschnitt oben ist beispielsweise "DecisionTreeInputBlob" eine Eingabe für die AzureMLBatchExecution-Aktivität, die über den webServiceInput-Parameter als Eingabe an den Webdienst übergeben wird.

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

	> [AZURE.NOTE]Die AzureMLBatchExecution-Aktivität kann über keine oder mehrere Eingaben sowie über eine oder mehrere Ausgaben verfügen.

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

	Datum und Uhrzeit von **Start** und **Ende** müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. Die Zeit für **end** ist optional. Wenn Sie für die **end**-Eigenschaft keinen Wert angeben, wird sie als "**start + 48 Stunden**" berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die **end**-Eigenschaft **9999-09-09** an. Informationen zu JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz](https://msdn.microsoft.com/library/dn835050.aspx).

	> [AZURE.NOTE]Das Festlegen der Eingaben für die AzureMLBatchExecution-Aktivität erfolgt optional.

## Szenario: Experimente mit Reader- und Writer-Modulen zum Verweisen auf Daten in verschiedenen Speichern

Ein weiteres gängiges Szenario beim Erstellen von Azure ML-Experimenten ist die Verwendung von Reader- und Writer-Modulen. Das Reader-Modul wird verwendet, um Daten in einem Experiment zu laden, während mit dem Writer-Modul Daten aus den Experimenten gespeichert werden. Ausführliche Informationen zu Reader- und Writer-Modulen finden Sie in den Themen [Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) und [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) in der MSDN Library.

Bei Verwendung der Reader- und Writer-Module empfiehlt es sich, einen Webdienstparameter für jede Eigenschaft dieser Module zu verwenden. Durch diese Webparameter können Sie die Werte zur Laufzeit konfigurieren. Sie können z. B. ein Experiment mit einem Reader-Modul erstellen, das eine Azure SQL-Datenbank mit dem Namen "XXX.database.windows.net" verwendet. Nach der Bereitstellung des Webdiensts sollen die Nutzer des Webdiensts einen weiteren Azure SQL Server mit dem Namen "YYY.database.windows.net" angeben können. Durch Verwendung eines Webdienstparameters wird ermöglicht, dass dieser Wert konfiguriert werden kann.

> [AZURE.NOTE]Eingaben und Ausgaben für den Webdienst unterscheiden sich von Webdienstparametern. Im ersten Szenario haben Sie gesehen, wie Eingaben und Ausgaben für einen Azure ML-Webdienst angegeben werden können. In diesem Szenario übergeben Sie Parameter für einen Webdienst, die Eigenschaften von Reader- und Writer-Modulen entsprechen.

Betrachten wir nun ein Szenario für die Verwendung von Webdienstparametern. Sie haben einen Azure Machine Learning-Webdienst bereitgestellt, bei dem Daten mithilfe eines Reader-Moduls aus einer der von Azure Machine Learning unterstützten Datenquellen (z. B. Azure SQL-Datenbank) gelesen werden. Nach der Batchausführung werden die Ergebnisse mit einem Writer-Modul (Azure SQL-Datenbank) geschrieben. In den Experimenten sind keine Eingaben und Ausgaben für den Webdienst definiert. In diesem Fall wird empfohlen, dass Sie die entsprechenden Webdienstparameter für das Reader- und das Writer-Modul einrichten. Dadurch können das Reader- und Writer-Modul bei Verwendung der AzureMLBatchExecution-Aktivität konfiguriert werden. Sie geben die Webdienstparameter wie folgt im Abschnitt **globalParameters** im JSON-Code der Aktivität an.


	"typeProperties": {
		"globalParameters": {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}

Sie können auch [Data Factory-Funktionen ](https://msdn.microsoft.com/library/dn835056.aspx) zum Übergeben von Werten für die Webdienstparameter verwenden, wie im folgenden Beispiel gezeigt:

	"typeProperties": {
    	"globalParameters": {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = \'{0:yyyy-MM-dd HH:mm:ss}\'', Time.AddHours(WindowStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]Bei Webdienstparametern wird Groß-/Kleinschreibung unterschieden, weshalb Sie sicherstellen müssen, dass die Namen, die Sie im JSON-Code der Aktivität angeben, denjenigen entsprechen, die vom Webdienst verfügbar gemacht werden.

### Lesen von Daten aus mehreren Dateien im Azure-Blob mithilfe eines Reader-Moduls
Big Data-Pipelines (Pig, Hive usw.) können eine oder mehrere Ausgabedateien ohne Erweiterung generieren. Wenn Sie beispielsweise eine externe Hive-Tabelle angeben, können die Daten für die externe Hive-Tabelle im Azure-Blob-Speicher unter dem Namen "000000\_0" gespeichert werden. Mithilfe des Reader-Moduls in einem Experiment können Sie mehrere Dateien lesen und für Vorhersagen verwenden.

Wenn Sie das Reader-Modul in einem Azure Machine Learning-Experiment verwenden, können Sie das Azure-Blob als Eingabe angeben. Bei den Dateien im Azure-Blobspeicher kann es sich um die Ausgabedateien (z. B. 000000\_0) handeln, die von einem Pig- und Hive-Skript unter HDInsight erstellt werden. Mit dem Reader-Modul können Sie Dateien (ohne Erweiterung) lesen, indem Sie die Eigenschaft **Path to container, directory or blob** des Reader-Moduls so konfigurieren, dass sie auf den Container/Ordner mit den Dateien verweist (wie unten dargestellt). Das Sternchen (*) **gibt an, dass alle Dateien im Container/Ordner (d. h. data/aggregateddata/year=2014/month=6/*)** als Teil des Experiments gelesen werden.

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
- Datum und Uhrzeit von **Start** und **Ende** müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. Die Zeit für **end** ist optional. Wenn Sie für die **end**-Eigenschaft keinen Wert angeben, wird sie als "**start + 48 Stunden**" berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die **end**-Eigenschaft **9999-09-09** an. Informationen zu JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz](https://msdn.microsoft.com/library/dn835050.aspx).
 

## Häufig gestellte Fragen

**F:** Ich verwende die AzureMLBatchScoring-Aktivität. Soll ich auf die AzureMLBatchExecution-Aktivität umsteigen?

**A:** Ja. Wenn Sie derzeit die AzureMLBatchScoring-Aktivität für die Integration in Azure Machine Learning verwenden, empfiehlt es sich, die neueste AzureMLBatchExecution-Aktivität zu verwenden. Die AzureMLBatchScoring-Aktivität ist veraltet und wird in zukünftigen Versionen nicht mehr enthalten sein.

Die AzureMLBatchExecution-Aktivität wird in den Azure SDK- und Azure PowerShell-Versionen von August 2015 eingeführt.

Die AzureMLBatchExecution-Aktivität erfordert keine Eingabe (wenn keine Eingabeabhängigkeiten benötigt werden). Zudem können Sie explizit angeben, ob die Eingabe und die Ausgabe für den Webdienst verwendet oder nicht verwendet werden sollen. Wenn Sie die Verwendung der Eingabe/Ausgabe für den Webdienst festlegen, können Sie die entsprechenden zu verwendenden Azure-Blob-Datasets angeben. Darüber hinaus können Sie die Werte für die Webdienstparameter, die vom Webdienst bereitgestellt werden, eindeutig angeben.

Wenn Sie weiterhin die AzureMLBatchScoring-Aktivität verwenden möchten, finden Sie ausführliche Informationen im Artikel zur [AzureMLBatchScoring-Aktivität](data-factory-create-predictive-pipelines.md).


**F:** Ich verfüge über mehrere Dateien, die von meinen Big Data-Pipelines erstellt werden. Kann ich die AzureMLBatchExecution-Aktivität zum Bearbeiten aller Dateien verwenden?

**A:** Ja. Ausführliche Informationen dazu finden Sie im Abschnitt **Lesen von Daten aus mehreren Dateien im Azure-Blob mithilfe eines Reader-Moduls**.








[adf-build-1st-pipeline]: data-factory-build-your-first-pipeline.md

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/


 

<!---HONumber=August15_HO9-->