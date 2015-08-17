<properties 
	pageTitle="Data Factory - Erstellen von Vorhersagepipelines mithilfe von Data Factory und Machine Learning | Azure" 
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
	ms.date="08/04/2015" 
	ms.author="spelluru"/>


# Erstellen von Vorhersagepipelines mithilfe von Azure Data Factory und Azure Machine Learning 
## Übersicht

Azure Data Factory ermöglicht Ihnen die einfache Erstellung von Pipelines, die einen veröffentlichten [Azure Machine Learning][azure-machine-learning]-Webdienst für Vorhersageanalysen nutzen. Dadurch können Sie mit Azure Data Factory die Verschiebung und Verarbeitung von Daten orchestrieren und anschließend eine Batchbewertung mithilfe von Azure Machine Learning durchführen. Hierzu müssen Sie folgende Aktionen ausführen:

1. Verwenden Sie die Aktivität **AzureMLBatchScoring**.
2. Rufen Sie den **Anforderungs-URI** für die Batchausführungs-API ab. Sie erhalten den Anforderungs-URI, indem Sie auf der Webdiensteseite auf den Link **BATCHAUSFÜHRUNG** klicken (wie unten gezeigt).
3. Kopieren Sie den **API-Schlüssel** für den veröffentlichten Machine Learning-Webdienst. Sie finden diese Informationen durch Klicken auf den Webdienst, den Sie veröffentlicht haben. 

	![Machine Learning-Dashboard][machine-learning-dashboard]

Eine **Vorhersagepipeline** besteht aus folgenden Teilen:

-	Eingabe- und Ausgabetabellen
-	Mit Azure Storage/Azure SQL und Azure ML verknüpfte Dienste
-	Eine Pipeline mit Azure ML-Stapelbewertungsaktivität

> [AZURE.NOTE]Sie können Webdienstparameter verwenden, die von einem veröffentlichten Azure Machine Learning-Webdienst in ADF-Pipelines (Azure Data Factory) verfügbar gemacht werden. Weitere Informationen finden Sie im Abschnitt "Webdienstparameter" in diesem Artikel.

## Beispiel
Dieses Beispiel verwendet Azure Storage zum Speichern der Ein- und Ausgabedaten. Sie können auch die Azure SQL-Datenbank anstelle von Azure Storage verwenden.

Es wird empfohlen, dass Sie zunächst das Lernprogramm [Erste Schritte mit Azure Data Factory][adf-getstarted] durchlaufen, bevor Sie sich mit diesem Beispiel beschäftigen und mit dem Data Factory-Editor Data Factory-Artefakte (verknüpfte Dienste, Tabellen, Pipeline) erstellen.
 

1. Erstellen Sie einen **verknüpften Dienst** für **Azure Storage**. Wenn sich die Ein- und Ausgabedateien für die Bewertung in verschiedenen Speicherkonten befinden, benötigen Sie zwei verknüpfte Dienste. Hier folgt ein JSON-Beispiel:

		{
		  "name": "StorageLinkedService",
		  "properties": {
		    "type": "AzureStorage",
		    "typeProperties": {
		      "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		  }
		}

2. Erstellen Sie die **Eingabe****tabelle** für Azure Data Factory. Beachten Sie, dass diese im Gegensatz zu einigen anderen Data Factory-Tabellen die beiden Werte **folderPath** und **fileName** enthalten müssen. Sie können die Partitionierung verwenden, damit jede Stapelausführung (jeder Datenslice) eindeutige Ein- und Ausgabedateien verarbeitet oder erzeugt. Sie müssen wahrscheinlich einige Upstream-Aktivitäten einbeziehen, um die Eingabe in das CSV-Dateiformat umzuwandeln und sie im Speicherkonto für die einzelnen Datenslices abzulegen. In diesem Fall würden Sie die im folgenden Beispiel gezeigten Einstellungen **external** und **externalData** nicht einbeziehen, und Ihr "ScoringInputBlob" entspräche der Ausgabetabelle einer anderen Aktivität.

		{
		  "name": "ScoringInputBlob",
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
	
	Die CSV-Datei mit der Stapelbewertung muss die Spaltenkopfzeile enthalten. Bei Verwenden der **Kopieraktivität** zum Erstellen/Verschieben der CSV-Datei in den Blobspeicher müssen Sie die Senkeneigenschaft **BlobWriterAddHeader** auf **true** festlegen. Beispiel:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Wenn die CSV-Datei nicht die Kopfzeilen enthält, wird möglicherweise folgender Fehler angezeigt: **Fehler in Aktivität: Fehler beim Lesen der Zeichenfolge. Unerwartetes Token: StartObject. Pfad '', Zeile 1, Position 1**.
3. Erstellen Sie die **Ausgabe****tabelle** für Azure Data Factory. In diesem Beispiel wird die Partitionierung verwendet, um für die Ausführung der einzelnen Datenslices einen eindeutigen Ausgabepfad zu erstellen. Ohne diesen Schritt würde die Aktivität die Datei überschreiben.

		{
		  "name": "ScoringResultBlob",
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

4. Erstellen Sie einen **verknüpften Dienst** vom Typ **AzureMLLinkedService**, der den API-Schlüssel und die URL für die Modellbatchbewertung bereitstellt.
		
		{
		  "name": "MyAzureMLLinkedService",
		  "properties": {
		    "type": "AzureML",
		    "typeProperties": {
		      "mlEndpoint": "https://[batch scoring endpoint]/jobs",
		      "apiKey": "[apikey]"
		    }
		  }
		}
5. Erstellen Sie abschließend eine Pipeline, die **AzureMLBatchScoringActivity** enthält. Dadurch wird der Speicherort der Eingabedatei aus Ihren Eingabetabellen abgerufen, die AzureML-API für die Stapelbewertung aufgerufen und die Stapelbewertungsausgabe in das in der Ausgabetabelle angegebene Blob kopiert. Im Gegensatz zu einigen anderen Data Factory-Aktivitäten kann "AzureMLBatchScoringActivity" nur eine Eingabe- und eine Ausgabetabelle besitzen.

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

	Datum und Uhrzeit von **Start** und **Ende** müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. Die Zeit für **end** ist optional. Wenn Sie für die **end**-Eigenschaft keinen Wert angeben, wird sie als **start + 48 Stunden** berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die **end**-Eigenschaft **9999-09-09** an. Informationen zu JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz](https://msdn.microsoft.com/library/dn835050.aspx).

## Webdienstparameter
Sie können Webdienstparameter verwenden, die von einem veröffentlichten Azure Machine Learning-Webdienst in ADF-Pipelines (Azure Data Factory) verfügbar gemacht werden. Sie können ein Experiment in Azure Machine Learning erstellen, es als Webdienst veröffentlichen und dann diesen Webdienst in mehreren ADF-Pipelines oder -Aktivitäten verwenden, um über die Webdienstparameter unterschiedliche Eingaben zu übergeben.

### Übergeben von Werten für Webdienstparameter
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
 
> [AZURE.NOTE]Bei Webdienstparametern wird Groß-/Kleinschreibung unterschieden, weshalb Sie sicherstellen müssen, dass die Namen, die Sie im JSON-Code der Aktivität angeben, denjenigen entsprechen, die vom Webdienst verfügbar gemacht werden.

### Die Module "Reader" und "Writer"

Ein häufiges Szenario für Webdienstparameter ist die Verwendung der Azure SQL-Module "Reader" und "Writer". Das Modul "Reader" dient zum das Laden von Daten in ein Experiment aus Datenverwaltungsdiensten außerhalb von Azure Machine Learning Studio. Das Modul "Writer" wird zum Speichern von Daten aus Ihren Experimenten in Datenverwaltungsdiensten außerhalb von Azure Machine Learning Studio verwendet. Ausführliche Informationen zu Azure-Blob/Azure SQL Reader/Writer finden Sie in den Themen [Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) und [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) in der MSDN Library. Im Beispiel im vorherigen Abschnitt wurden die Azure-Blobmodule "Reader" und "Writer" verwendet. In diesem Abschnitt werden die Azure SQL-Module "Reader" und "Writer" erläutert.

#### Azure SQL als Datenquelle
In Azure ML Studio können Sie ein Experiment erstellen und einen Webdienst mit einem Azure SQL-Reader für die Eingabe veröffentlichen. Der Azure SQL-Reader weist Verbindungseigenschaften auf, die als Webdienstparameter verfügbar gemacht werden. Dadurch können Werte für die Verbindungseigenschaften zur Laufzeit an die Aufforderung zur Stapelbewertung übergeben werden.

Zur Laufzeit werden die Details in der Data Factory-Eingabetabelle vom Data Factory-Dienst zum Ausfüllen der Webdienstparameter verwendet. Beachten Sie, dass Sie Standardnamen (Datenbankservername, Datenbankname, Benutzerkontoname für Server, Benutzerkontokennwort für Server) für die Webdienstparameter angeben müssen, damit diese Integration in den Data Factory-Dienst funktioniert.

Wenn Sie über weitere Webdienstparameter verfügen, verwenden Sie den Abschnitt **WebServiceParameters** im JSON-Code der Aktivität. Bei Angabe von Werten für Azure SQL-Reader-Parameter in diesem Abschnitt überschreiben diese Werte die Werte, die vom mit der Azure SQL-Eingabedatenbank verknüpften Dienst gewählt wurden. Es wird nicht empfohlen, Werte für Azure SQL-Reader direkt in den Abschnitt "webServiceParameters" einzugeben. Verwenden Sie diesen Abschnitt, um Werte für alle zusätzlichen Parameter zu übergeben.

Um einen Azure SQL-Reader über eine Azure Data Factory-Pipeline zu verwenden, führen Sie folgende Schritte aus:

- Erstellen Sie einen **mit Azure SQL verknüpften Dienst**. 
- Erstellen Sie eine Data Factory-**Tabelle**, die **AzureSqlTable** verwendet.
- Legen Sie diese Data Factory-**Tabelle** als **Eingabe** für die **AzureMLBatchScoringActivity** im JSON-Code der Pipeline fest. 



#### Azure SQL als Datensenke
Wie ein Azure SQL-Reader kann auch ein Azure SQL-Writer seine Eigenschaften als Webdienstparameter verfügbar machen. Ein Azure SQL-Writer verwendet Einstellungen aus dem verknüpften Dienst, der der Eingabetabelle oder der Ausgabetabelle zugeordnet ist. In der folgende Tabelle werden diese beiden Typen verknüpfter Dienste miteinander verglichen.

| Ausgabe/Eingabe | Eingabe ist Azure SQL | Eingabe ist Azure-Blob |
| ------------ | ------------------ | ------------------- |
| Ausgabe ist Azure SQL | <p>Der Data Factory-Dienst verwendet die Informationen in der Verbindungszeichenfolge aus dem verknüpften Dienst INPUT zum Generieren der Webdienstparameter mit den Namen "Database server name", "Database name", "Server user account name" und "Server user account password". Beachten Sie, dass Sie diese Standardnamen für Webdienstparameter in Azure ML Studio verwenden müssen.</p><p>Wenn der Azure SQL Reader und der Azure SQL Writer in Ihrem Azure-ML-Modell die oben angegebenen Webdienstparameter verwenden, ist alles in Ordnung. Wenn sie nicht dieselben Webdienstparameter verwenden, also beispielsweise der Azure SQL Writer die Parameternamen "Database server name1", "Database name1", "Server user account name1", "Server user account password1" (mit einer "1" am Ende) verwendet, müssen Sie für diese OUTPUT-Webdienstparameter im Abschnitt "webServiceParameters" der JSON-Aktivität Werte übergeben.</p><p>Sie können Werte für alle anderen Webdienstparameter über den webServiceParameters-Abschnitt der JSON-Aktivität übergeben.</p> | <p>Der Data Factory-Dienst generiert mithilfe der Informationen in der Verbindungszeichenfolge aus dem verknüpften Dienst OUTPUT die Webdienstparameter mit den Namen "Database server name", "Database name", "Server user account name" und "Server user account password". Beachten Sie, dass Sie diese Standardnamen für Webdienstparameter in Azure ML Studio verwenden müssen.</p><p>Sie können die Werte für alle anderen Webdienstparameter über den webServiceParameters-Abschnitt der JSON-Aktivität übergeben. <p>Das Eingabeblob wird als Eingabespeicherort verwendet.</p> |
|Ausgabe ist Azure-Blob | Der Data Factory-Dienst verwendet die Informationen in der Verbindungszeichenfolge aus dem verknüpften Dienst INPUT zum Generieren der Webdienstparameter mit den Namen "Database server name", "Database name", "Server user account name", "Server user account password". Beachten Sie, dass Sie diese Standardnamen für Webdienstparameter in Azure ML Studio verwenden müssen. | <p>Sie müssen im Abschnitt "WebServiceParameters" der JSON-Aktivität Werte für alle Webdienstparameter übergeben.</p><p>Blobs werden als Eingabe- und Ausgabespeicherorte verwendet.</p> |
    

> [AZURE.NOTE]Im Azure SQL-Writer kann es zu Schlüsselverletzungen kommen, wenn eine Identitätsspalte überschrieben wird. Sie müssen Ihre Ausgabetabelle so strukturieren, dass diese Situation vermieden wird.
> 
> Sie können Stagingtabellen mit einer Aktivität vom Typ "Gespeicherte Prozedur" zum Zusammenführen von Zeilen oder Abschneiden von Daten vor der Bewertung nutzen. Legen Sie bei diesem Ansatz die Parallelitätseinstellung von "executionPolicy" auf "1" fest.

#### Azure-Blob als Quelle

Wenn Sie das Reader-Modul in einem Azure Machine Learning-Experiment verwenden, können Sie Azure-Blob als Eingabe angeben. Bei den Dateien im Azure-Blobspeicher kann es sich um die Ausgabedateien (z. B. 000000\_0) handeln, die von einem Pig- und Hive-Skript unter HDInsight erstellt werden. Mit dem Reader-Modul können Sie Dateien (ohne Erweiterung) lesen, indem Sie die Eigenschaft **Pfad zu Container, Verzeichnis oder Blob** des Reader-Moduls so konfigurieren, dass sie auf den Container/Ordner mit den Dateien verweist (wie unten dargestellt). Das Sternchen (\*) **gibt an, dass alle Dateien im Container/Ordner (d. h. data/aggregateddata/year=2014/month=6/\*)** als Teil des Experiments gelesen werden.

![Azure-Blobeigenschaften](./media/data-factory-create-predictive-pipelines/azure-blob-properties.png)

### Beispiel der Verwendung von Webdienstparametern
#### Pipeline mit "AzureMLBatchScoringActivity" mit Webdienstparameter

	{
	  "name": "MLWithSqlReaderSqlWriter",
	  "properties": {
	    "description": "Azure ML model with sql azure reader/writer",
	    "activities": [
	      {
	        "name": "MLSqlReaderSqlWriterActivity",
	        "type": "AzureMLBatchScoring",
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
	        "linkedServiceName": "MLSqlReaderSqlWriterScoringModel",
	        "policy": {
	          "concurrency": 1,
	          "executionPriorityOrder": "NewestFirst",
	          "retry": 1,
	          "timeout": "02:00:00"
	        },
	        "typeProperties": {
	          "webServiceParameters": {
	            "Database server name1": "output.database.windows.net",
	            "Database name1": "outputDatabase",
	            "Server user account name1": "outputUser",
	            "Server user account password1": "outputPassword",
	            "Comma separated list of columns to be saved": "CustID, Scored Labels, Scored Probabilities",
	            "Data table name": "BikeBuyerPredicted"
	          }
	        }
	      }
	    ],
	    "start": "2015-02-13T00:00:00Z",
	    "end": "2015-02-14T00:00:00Z"
	  }
	}
 
Im obigen JSON-Beispiel:

- Das Azure ML-Modell verwendet den Azure SQL-Reader und Azure SQL-Writer.
- Falls über den Webdienst verfügbar gemacht, werden die Standardnamen für die Parameter verwendet.
	- Für den **Reader** sind dies "Database server name", "Database name", "Server user account name" und "Server user account password".
	- Für den **Writer** sind dies "Database server name1", "Database name1", "Server user account name1" und "Server user account password1".
	
		Beachten Sie, dass der Reader und Writer in diesem Fall keine Parameter gemeinsam verwenden.  
- Der Data Factory-Dienst generiert Werte für Webdienstparameter mit den Namen **Database server name**, **Database name**, **Server user account name** und **Server user account password**, die mit den Namen des Eingabereaders übereinstimmen. Daher müssen Sie die Werte für diese Parameter nicht explizit über **WebServiceParameters** im folgenden JSON-Code der Aktivität übergeben.  
- Die Parameter für den Writer (mit dem Suffix "1") werden nicht automatisch vom Data Factory-Dienst ausgefüllt. Daher müssen Sie Werte für diese Parameter im Abschnitt **webServiceParameters** im JSON-Code der Aktivität angeben.  
- **Customer ID**, **scored labels** und **scored probabilities** werden als durch Trennzeichen getrennte Spalten gespeichert. 
- Der **Name der Datentabelle** in diesem Beispiel entspricht einer Tabelle in der Ausgabedatenbank.
- Datum und Uhrzeit von **Start** und **Ende** müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. Die Zeit für **end** ist optional. Wenn Sie für die **end**-Eigenschaft keinen Wert angeben, wird sie als **start + 48 Stunden** berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie als Wert für die **end**-Eigenschaft **9999-09-09** an. Informationen zu JSON-Eigenschaften finden Sie in der [JSON-Skriptreferenz](https://msdn.microsoft.com/library/dn835050.aspx).




## Weitere Informationen

Artikel | Beschreibung
------ | ---------------
[Azure Data Factory-Entwicklerreferenz][developer-reference] | Die Entwicklerreferenz enthält umfassende Referenzinformationen für Cmdlets, die .NET-Klassenbibliothek, JSON-Skripts, Funktionen usw. 

[adf-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[developer-reference]: http://go.microsoft.com/fwlink/p/?LinkId=516908

[azure-machine-learning]: http://azure.microsoft.com/services/machine-learning/
[machine-learning-dashboard]: ./media/data-factory-create-predictive-pipelines/AzureMLDashboard.png

 

<!---HONumber=August15_HO6-->