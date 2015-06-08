<properties 
	pageTitle="Data Factory - Erstellen von Vorhersagepipelines mithilfe von Data Factory und Machine Learning | Azure" 
	description="Beschreibt das Erstellen von Vorhersage-Pipelines mit Azuer Daten Factory und Azure Computerlernen erstellen" 
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
	ms.date="05/05/2015" 
	ms.author="spelluru"/>

# Erstellen von Vorhersagepipelines mithilfe von Azure Data Factory und Azure Machine Learning 
## Übersicht
Sie können durchsetzen veröffentlicht [Azure maschinelles lernen][azure-machine-learning] Modelle innerhalb von Azure Data Factory-Pipelines. Diese Pipelines heißen predictive Pipelines. Sie benötigen Folgendes, um eine Vorhersagepipeline erstellen zu können:

-	Den API-Schlüssel des veröffentlichten Arbeitsbereichsmodells und die URL für die Stapelbewertung (siehe nachfolgende Abbildung)
-	Ein Azure-Blob-Speicher mit der CSV-Datei (oder) einer Azure SQL-Datenbank, die die Eingabe enthält Daten, die bewertet werden. 
-	Ein Azure-Blob-Speicher, die die Punktzahl Ergebnisse CSV-Datei (oder) einer Azure SQL-Datenbank, die die Ausgabedaten enthält enthalten. 

	![Machine Learning-Dashboard][machine-learning-dashboard]

	Die Bewertung der URL für die AzureMLLinkedService als aus in der Abbildung oben angegebenen abgerufen wird Batch minus ' ** Hilfe ** ": https://ussouthcentral.services.azureml.net/workspaces/da9e350b758e44b2812a6218d507e216/services/8c91ff373a81416f8f8e0d96a1162681/jobs/

Ein **predictive Pipeline** besteht aus folgenden Teilen:

-	Eingabe- und Ausgabetabellen
-	Azure-Speicher/Azure SQL und Azure ML verknüpfte Dienste
-	Eine Pipeline mit Azure ML-Stapelbewertungsaktivität

> [AZURE.NOTE]Sie können Web-Service-Parameter verwenden, die von einem veröffentlichten Azure Machine Learning Webdienst in Azure Data Factory (ADF) Pipelines verfügbar gemacht werden. Weitere Informationen finden Sie im Abschnitt "Web Service-Parameter" in diesem Artikel.

## Beispiel
Dieses Beispiel verwendet den Azure-Speicher zum Speichern der Eingabe- und Daten. Sie können auch Azure SQL-Datenbank verwenden, anstelle von Azure-Speicher.

Es wird empfohlen, dass Sie durchlaufen die [Erste Schritte mit Azure Data Factory][adf-getstarted] Tutorial, bevor Sie dieses Beispiel und der Verwendung des Factory-Editors zum Erstellen von Daten-Factory-Artefakte (verknüpften Dienste, Tabellen, Pipeline) in diesem Beispiel durchgehen.
 

1. Erstellen Sie für Ihr Azure Storage einen verknüpften Dienst. Wenn sich die Ein- und Ausgabedateien für die Bewertung in verschiedenen Speicherkonten befinden, benötigen Sie zwei verknüpfte Dienste. Hier folgt ein JSON-Beispiel:

		{
		    "name": "StorageLinkedService",
		    "properties":
		    {
		        "type": "AzureStorageLinkedService",
		        "connectionString": "DefaultEndpointsProtocol=https;AccountName=[acctName];AccountKey=[acctKey]"
		    }
		}

2. Erstellen Sie die Ein- und Ausgabetabellen für Azure Data Factory. Beachten Sie, dass im Gegensatz zu einigen anderen Data Factory-Tabellen, diese beiden sowohl enthalten müssen **FolderPath** und **Dateiname** Werte. Sie können die Partitionierung verwenden, damit jede Stapelausführung (jeder Datenslice) eindeutige Ein- und Ausgabedateien verarbeitet oder erzeugt. Sie müssen wahrscheinlich einige Upstream-Aktivitäten einbeziehen, um die Eingabe in das CSV-Dateiformat umzuwandeln und sie im Speicherkonto für die einzelnen Datenslices abzulegen. In diesem Fall beziehen Sie die im folgenden Beispiel gezeigte "waitOnExternal"-Einstellungen nicht ein, und Ihr "ScoringInputBlob" würde die Ausgabetabelle einer anderen Aktivität darstellen.

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
	
	Der Batch, die Bewertung der Csv-Datei muss die Spaltenkopfzeile verfügen. Bei Verwendung der **Kopie Aktivität** zum Erstellen/im CSV-Format in den Blob-Speicher verschieben, sollten Sie die Ereignissenken-Eigenschaft festlegen **BlobWriterAddHeader** auf **true**. Beispiel:
	
	     sink: 
	     {
	         "type": "BlobSink",     
	         "blobWriterAddHeader": true 
	     }
	 
	Wenn die Csv-Datei keine Kopfzeilen, sehen Sie möglicherweise den folgenden Fehler: **in der Aktivität Fehler: Fehler beim Lesen der Zeichenfolge. Unerwartetes Token: StartObject. Pfad '', Zeile 1, position 1**.
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


4. Erstellen Sie einen verknüpften Dienst vom Typ: **AzureMLLinkedService**, den API-Schlüssel und das Modell wird bewertet URL batch.
		
		{
		    "name": "MyAzureMLLinkedService",
		    "properties":
		    {
		        "type": "AzureMLLinkedService",
		        "mlEndpoint":"https://[batch scoring endpoint]/jobs",
		        "apiKey":"[apikey]"
		    }
		}

5. Erstellen Sie abschließend eine Pipeline mit einer **AzureMLBatchScoringActivity**. Dadurch wird der Speicherort der Eingabedatei aus Ihren Eingabetabellen abgerufen, die AzureML-API für die Stapelbewertung aufgerufen und die Stapelbewertungsausgabe in das in der Ausgabetabelle angegebene Blob kopiert. Im Gegensatz zu einigen anderen Data Factory-Aktivitäten kann "AzureMLBatchScoringActivity" nur eine Eingabe- und eine Ausgabetabelle besitzen.

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


## Web-Service-Parameter
Sie können Web-Service-Parameter verwenden, die von einem veröffentlichten Azure Machine Learning Webdienst in Azure Data Factory (ADF) Pipelines verfügbar gemacht werden. Sie können ein Experiment in Azure Computerlernen erstellen und als Webdienst zu veröffentlichen, und dann diesen Webdienst in mehreren ADF Pipelines oder Aktivitäten, die in unterschiedlichen Eingaben über die Web Service-Parameter übergeben.

### Übergeben von Werten für Web Service-Parameter
Hinzufügen einer **Transformation** im Abschnitt zu den **AzureMLBatchScoringActivty** Abschnitt in der Pipeline JSON und geben Sie Werte für Web Service-Parameter in diesem Abschnitt, wie im folgenden Beispiel gezeigt:

	transformation: {
		webServiceParameters: {
			"Param 1": "Value 1",
			"Param 2": "Value 2"
		}
	}


Sie können auch [Factory-Funktionen zur](https://msdn.microsoft.com/library/dn835056.aspx) bei der Übergabe von Werten für das Web service-Parameter wie im folgenden Beispiel gezeigt:

	transformation: {
    	webServiceParameters: {
    	   "Database query": "$$Text.Format('SELECT * FROM myTable WHERE timeColumn = '{0:yyyy-MM-dd HH:mm:ss}'', Time.AddHours(SliceStart, 0))"
    	}
  	}
 
> [AZURE.NOTE]Die Web Service-Parameter Groß-/Kleinschreibung beachtet werden, müssen Sie sicherstellen, dass den Namen, den Sie angeben, in der Aktivität JSON derjenigen, die vom Webdienst verfügbar gemacht.

### Azure SQL-Readern und Writern
Ein häufiges Szenario für die Verwendung von Web Service-Parameter ist die Verwendung von Azure SQL-Readern und Writern. Das Reader-Modul wird verwendet, um das Laden von Daten in einem Experiment aus Datenverwaltungsdienste außerhalb von Azure Machine Learning Studio und das Writer-Modul wird zum Speichern von Daten aus Ihrer Versuche in Data Management Services außerhalb von Azure Machine Learning Studio. Ausführliche Informationen zum Azure-Blob/Azure SQL-Leser/Schreiber finden Sie unter [Reader](https://msdn.microsoft.com/library/azure/dn905997.aspx) und [Writer](https://msdn.microsoft.com/library/azure/dn905984.aspx) -Themen in der MSDN Library. Im Beispiel im vorherigen Abschnitt verwendet den Azure-Blob-Reader und Writer der Azure-Blob. In diesem Abschnitt wird erläutert, mit Azure SQL-Reader und Azure SQL Writer.

#### Azure SQL-Reader
In Azure ML Studio können Sie ein Experiment zu erstellen und veröffentlichen einen Webdienst mit einer Azure SQL-Reader für die Eingabe. Der Azure SQL-Reader hat es sich um Verbindungseigenschaften, die als Web-Service-Parameter bereitgestellt werden, können Werte für die Verbindungseigenschaften zur Laufzeit im Batch, die Bewertung der Anforderung übergeben werden kann.

Zur Laufzeit werden die Details der Eingabetabelle Data Factory vom Service Factory von Daten zum Auffüllen der Web Service-Parameter verwendet werden. Beachten Sie, dass Sie Standardnamen (Servername, Datenbankname, Benutzerkontoname Server, Benutzerkontokennwort für Server) für die Web Service-Parameter für diese Integration in die Service Factory von Daten arbeiten.

Wenn man alle zusätzlichen Web service-Parameter, verwenden Sie die **WebServiceParameters** Abschnitt der JSON-Aktivität. Bei Angabe von Werten für Azure SQL-Reader-Parameter in diesem Abschnitt werden die Werte der Werte aus der Eingabe Azure SQL-verknüpfte Dienst abgeholt überschrieben. Angeben von Werten für Azure SQL-Leser direkt im Abschnitt WebServiceParameters wird nicht empfohlen. Verwenden Sie im Abschnitt, um Werte für alle zusätzlichen Parameter übergeben.

Um eine Azure SQL-Reader über eine Pipeline Azure Data Factory verwenden zu können, führen Sie folgende Schritte aus:

- Erstellen einer **Azure SQL verknüpft Service**. 
- Erstellen Sie eine Daten-Factory **Tabelle** verwendet, die **AzureSqlTableLocation**.
- Festlegen von dieser Factory Daten **Tabelle** als die **input** für die **AzureMLBatchScoringActivity** in der Pipeline JSON. 



#### Azure SQL Writer
Wie können bei Azure SQL-Reader einer Azure SQL Writer auch seine Eigenschaften, die als Parameter für Web-Service verfügbar gemacht haben. Eine Azure SQL Writer verwendet entweder aus den verknüpften Dienst die Eingabetabelle oder die Ausgabetabelle zugeordnet. In der folgende Tabelle wird beschrieben, wann die Eingabe verwendeten verknüpfte Service im Vergleich zu Ausgabe Dienst verknüpft.

<table>
<tr>
<td>Ausgabe/Input</td>
<td><b>Eingabe ist Azure SQL</b></td>
<td><b>Eingabe ist Azure-Blob</b></td>
</tr>
<tr>
<td><b>Ausgabe wird von Azure-SQL</b></td>
<td><p>Der Daten-Factory-Dienst verwendet die Verbindungszeichenfolge aus dem Dienst verknüpfte Eingabe zum Generieren der Web Service-Parameter mit Namen: "Database Servername", "Datenbankname", "Server User Account Name", "Benutzerkontokennwort für Server". Beachten Sie, dass Sie diese Standardnamen für Web Service-Parameter in Azure ML Studio verwenden müssen.</p>
<p>Wenn das Azure SQL-Reader und Azure SQL Writer in Ihrem Azure-ML-Modell die gleichen Web-Service-Parameter, die oben erwähnten, sind Sie in Ordnung. Wenn sie nicht dieselbe Web Service-Paramers, z. B. freigeben, wenn der Azure SQL Writer Parameternamen verwendet: Datenbank Server name1, Datenbank-name1, Server User Account name1, Server-Benutzer Kennwort1 (mit "1" am Ende) zu berücksichtigen, müssen Sie Werte für diese Ausgabe Web Service-Parameter im Abschnitt WebServiceParameters der JSON-Aktivität übergeben.</p>
<p>
Sie können die Werte für alle anderen Web Service Parameter mithilfe des Abschnitts WebServiceParameters JSON-Aktivität übergeben.  
</p>

</td>
<td>
<p>Der Daten-Factory-Dienst verwendet die Verbindungszeichenfolge aus dem Dienst verknüpften Ausgabe zum Generieren der Web Service-Parameter mit Namen: "Database Servername", "Datenbankname", "Server User Account Name", "Benutzerkontokennwort für Server". Beachten Sie, dass Sie diese Standardnamen für Web Service-Parameter in Azure ML Studio verwenden müssen.</p>
<p>Sie können die Werte für alle anderen Web Service Parameter mithilfe des Abschnitts WebServiceParameters JSON-Aktivität übergeben. <p>Input-Blob wird als Eingabe Speicherort verwendet werden.</p>
</td>
</tr>
<tr>
<td><b>Ausgabe wird von Azure-Blob</b></td>
<td>Der Daten-Factory-Dienst verwendet die Verbindungszeichenfolge aus dem Dienst verknüpfte Eingabe zum Generieren der Web Service-Parameter mit Namen: "Database Servername", "Datenbankname", "Server User Account Name", "Benutzerkontokennwort für Server". Beachten Sie, dass Sie diese Standardnamen für Web Service-Parameter in Azure ML Studio verwenden müssen.
</td>
<td>
<p>Sie müssen Werte für alle WebServiceParameters im Bereich der Aktivität JSON Web Service-Parameter übergeben.</p> 

<p>BLOBs werden als Eingabe- und Speicherorte verwendet werden.</p>

</td>
<tr>

</table>

> [AZURE.NOTE]Wenn sie eine Identity-Spalte überschreibt, kann Azure SQL Writer Schlüsselverletzungen kommen. Sie sollten sicherstellen, dass Ihre Ausgabetabelle zur Vermeidung dieser Situation zu strukturieren.
> 
> Stagingtabellen können mit einer gespeicherten Prozedur für die Aktivität zum Zusammenführen von Zeilen oder zum Abschneiden von Daten vor dem bewerten. Wenn Sie diesen Ansatz verwenden, Parallelitätseinstellung von die Ausführungsrichtlinie auf 1 festgelegt.

### Beispiel zur Verwendung von Web Service-Parameter
#### Pipeline mit AzureMLBatchScoringActivity mit Web Service-Parameter

	{
		"name": "MLWithSqlReaderSqlWriter",
	  	"properties": {
		    "description": "Azure ML model with sql azure reader/writer",
		    "activities": [
		    	{
		    	    "name": "MLSqlReaderSqlWriterActivity",
		    	    "type": "AzureMLBatchScoringActivity",
		    	    "description": "test",
		        	"inputs": [ { "name": "MLSqlInput" } ],
		        	"outputs": [ { "name": "MLSqlOutput" } ],
		        	"linkedServiceName": "MLSqlReaderSqlWriterScoringModel",
		        	"policy": {
		          		"concurrency": 1,
			          	"executionPriorityOrder": "NewestFirst",
			          "retry": 1,
			          "timeout": "02:00:00"
			        },
			        transformation: {
			        	webServiceParameters: {
		            		"Database server name1": "output.database.windows.net",
				            "Database name1": "outputDatabase",
		            		"Server user account name1": "outputUser",
		            		"Server user account password1": "outputPassword",
			           		"Comma separated list of columns to be saved": "CustID, Scored Labels, Scored Probabilities",
		    		        "Data table name": "BikeBuyerPredicted" 
		          		}  
		        	}
		      	}
	    	]
		}
	}
 
Im obigen Beispiel für JSON:

- Das Azure-ML-Modell verwendet, Azure SQL-Reader und Azure SQL Writer
- Wenn über den Webdienst verfügbar gemacht wird, werden die Standardnamen für die Parameter verwendet.
	- Für die **Reader**: Datenbank-Servername, Datenbankname, Benutzerkontoname Server und Server-Benutzerkennwort.
	- Für die **Writer**: Server name1, Datenbank-name1, Server User Account name1 und Server User Account Kennwort1-Datenbank.
	
		Beachten Sie, dass der Reader und Writer keine Parameter in diesem Fall austauschen.  
- Der Daten-Factory-Dienst generiert automatisch Werte für Parameter mit den Namen **Datenbankservernamen**, **Datenbankname**, **Benutzerkontoname Server**, und **Benutzerkontokennwort für Server**, die die Namen des Readers für die Eingabe übereinstimmen. Daher müssen Sie nicht explizit übergeben, die Werte für diese Parameter über **WebServiceParameters** in der folgenden JSON-Aktivität.  
- Die Parameter für den Writer (diejenigen mit "1" Suffix) werden vom Data Factory-Dienst nicht automatisch ausgefüllt. Daher müssen Sie Werte für diese Parameter in der **WebServiceParameters** Abschnitt der JSON-Aktivität.  
- **Kunden-ID**, **bewertet Bezeichnungen**, und **bewertet Wahrscheinlichkeiten** werden als durch Trennzeichen getrennte Spalten gespeichert. 
- Die **Name der Datentabelle** in diesem Beispiel wird eine Tabelle in der Ausgabedatenbank entspricht.




## Siehe auch

Artikel | Beschreibung
------ | ---------------
[Azure Data Factory-Entwicklerreferenz][developer-reference] | Entwicklerreferenz hat die umfassendes Referenzmaterial für die Cmdlets, JSON-Skripts, .NET Klassenbibliothek, Funktionen usw.... 

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

<!---HONumber=GIT-SubDir-->