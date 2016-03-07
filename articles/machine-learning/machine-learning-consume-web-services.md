<properties
	pageTitle="Nutzen eines Machine Learning-Webdiensts | Microsoft Azure"
	description="Sobald ein Machine Learning-Dienst bereitgestellt wurde, kann der zur Verfügung gestellte RESTFul-Webdienst als Antwort-/Anfrage-Dienst oder als Stapelausführungsdienst genutzt werden."
	services="machine-learning"
	documentationCenter=""
	authors="garyericson"
	manager="paulettm"
	editor="cgronlun" />

<tags
	ms.service="machine-learning"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd"
	ms.date="02/21/2016"
	ms.author="garye" />


# Nutzen eines Azure Machine Learning-Webdiensts, der von einem Machine Learning-Experiment aus bereitgestellt wurde

## Einführung

Beim Bereitstellen als Webdienst bieten Azure Machine Learning-Experimente eine REST-API, die von einer Vielzahl von Geräten und Plattformen genutzt werden kann, denn die einfache REST-API akzeptiert JSON-formatierte Nachrichten und antwortet mit diesen. Das Azure Machine Learning-Portal bietet Code zum Aufrufen des Webdiensts in R, C# und Python. Diese Dienste können jedoch in jeder beliebigen Programmiersprache und von jedem Gerät aus aufgerufen werden, sofern die drei folgenden Kriterien erfüllt sind:

* Verfügt über eine Netzwerkverbindung
* Verfügt über SSL-Funktionen zum Ausführen von HTTPS-Anforderungen
* Verfügt über die Möglichkeit zum Analysieren von JSON (manuell oder durch Support-Bibliotheken)

Dies bedeutet, dass die Dienste von Webanwendungen, mobilen Anwendungen, benutzerdefinierten Desktopanwendungen und selbst in Excel genutzt werden können.

[AZURE.INCLUDE [machine-learning-kostenlose-Testversion](../../includes/machine-learning-free-trial.md)]

Ein Azure Machine Learning-Webdienst kann auf zwei unterschiedliche Arten verarbeitet werden, als Antwort-/Anfrage-Dienst oder als Stapelausführungsdienst. In jedem Szenario wird die Funktionalität über den RESTFul-Webdienst bereitgestellt, der für die Nutzung verfügbar gemacht wird, sobald das Experiment bereitgestellt wurde. Durch Bereitstellen eines Machine Learning-Webdiensts in Azure mit einem Azure-Webdienst-Endpunkt, bei dem der Dienst automatisch anhand der Nutzung skaliert wird, können Sie im Vorfeld anfallende und fortlaufende Kosten für Hardwareressourcen vermeiden.

> [AZURE.TIP] Eine einfache Möglichkeit zum Erstellen einer Web-App für den Zugriff auf Ihren Vorhersagewebdienst finden Sie unter [Verwenden eines Azure Machine Learning-Webdiensts mit einer Web-App-Vorlage](machine-learning-consume-web-service-with-web-app-template.md).

<!-- When this article gets published, fix the link and uncomment
For more information on how to manage Azure Machine Learning web service endpoints using the REST API, see **Azure machine learning web service endpoints**.
-->

Weitere Informationen zum Erstellen und Bereitstellen eines Machine Learning-Webdiensts finden Sie unter [Bereitstellen eines Azure Machine Learning-Webdiensts][publish]. Eine schrittweise Anleitung zum Erstellen und Bereitstellen eines Machine Learning-Experiments finden Sie unter [Entwickeln einer Predictive Analytics-Lösung mit Azure Machine Learning][walkthrough].

[publish]: machine-learning-publish-a-machine-learning-web-service.md
[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md


## Antwort-/Anfrage-Dienst (Request-Response Service RRS)

Ein Antwort-/Anfrage-Dienst (Request-Response Service, RRS) ist ein hochgradig skalierbarer Webdienst mit niedriger Latenz, mit dem eine Schnittstelle für die aus einem Azure Machine Learning Studio-Experiment erstellten und bereitgestellten zustandslosen Modelle bereitgestellt werden kann. Dadurch werden Szenarien ermöglicht, bei denen die verarbeitende Anwendung eine Antwort in Echtzeit erwartet.

RRS akzeptiert eine einzelne oder mehrere Zeilen von Eingabeparametern und kann eine einzelne oder mehrere Zeilen als Ausgabe generieren. Die Ausgabereihen können jeweils mehrere Spalten enthalten.

Ein Beispiel für RRS ist das Überprüfen der Authentizität einer Anwendung. Hunderte bis Millionen von Installationen einer Anwendung können in diesem Fall erwartet werden. Wenn die Anwendung gestartet wird, ruft sie den RRS-Dienst mit der entsprechenden Eingabe auf. Die Anwendung empfängt dann eine Überprüfungsantwort vom Dienst, die das Ausführen der Anwendung zulässt oder blockiert.


## Stapelausführungsdienst (Batch Execution Service BES)

Ein Stapelausführungsdienst (Batch Execution Service, BES) ist ein Dienst zur asynchronen Bewertung eines umfangreichen Stapels von Datensätzen. Die Eingabe für den BES enthält einen Stapel von Datensätzen aus verschiedenen Quellen, z. B. Blobs, Tabellen in Azure, SQL Azure, HDInsight (beispielsweise Ergebnisse einer Hive-Abfrage) oder HTTP-Quellen. Die Ausgabe des BES enthält die Ergebnisse der Wertung. Die Ergebnisse werden in eine Datei im Azure-Blob-Speicher geschrieben, und Daten aus dem Speicherendpunkt werden in der Antwort zurückgegeben.

Ein BES ist nützlich, wenn Antworten nicht sofort benötigt werden, z. B. für regelmäßig geplante Bewertungen für einzelne Benutzer oder Geräte im Internet der Dinge (IOT).

## Beispiele
Zum Erläutern der Funktionsweise von RRS und BES greifen wir auf ein Beispiel eines Azure-Webdiensts zurück. Dieser Dienst wird in einem IOT-Szenario (Internet der Dinge) verwendet werden. Der Einfachheit halber sendet unser Gerät nur einen Wert, `cog_speed`, und erhält eine einzelne Antwort zurück.

Vier Arten von Informationen sind erforderlich, um den RRS- oder den BES-Dienst aufzurufen. Diese Informationen sind jederzeit auf den Dienstseiten in [Azure Machine Learning Studio](https://studio.azureml.net) verfügbar, nachdem das Experiment bereitgestellt wurde. Klicken Sie auf die Registerkarte WEB SERVICES auf der linken Seite des Bildschirms, um die bereitgestellten Dienste anzuzeigen. Wenn Sie auf einen Dienst klicken, werden die folgenden Links und Informationen für RRS und BES angezeigt:

1.	Der **API-Schlüssel** des Dienst, der im Dashboard der Dienste verfügbar ist
2.	Der **Anforderungs-URI** des Diensts, der auf der API-Hilfeseite für den ausgewählten Dienst verfügbar ist
3.	Die erwarteten **Anforderungsheader** und der **Text** der API, die auf der API-Hilfeseite für den ausgewählten Dienst verfügbar sind
4.	Die erwarteten **Antwortheader** und der **Text** der API, die auf der API-Hilfeseite für den ausgewählten Dienst verfügbar sind

In den folgenden beiden Beispielen wird die Sprache C# verwendet, um den erforderlichen Code zu veranschaulichen. Die Zielplattform ist dabei ein Windows 8-Desktop.

### RRS-Beispiel
Klicken Sie auf dem Dashboard des Diensts unter **API HELP PAGE** auf **REQUEST/RESPONSE**, um die API-Hilfeseite anzuzeigen. Auf dieser Seite finden Sie neben dem URI Eingabe- und Ausgabedefinitionen sowie Codebeispiele. Die API-Eingabe speziell für diesen Dienst wird nachstehend gezeigt und bildet die Nutzlast des API-Aufrufs.

**Beispiel für eine Anforderung**

	{
	  "Inputs": {
	    "input1": {
	      "ColumnNames": [
	        "cog_speed"
	      ],
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}


Auch die API-Antwort für diesen Dienst wird nachstehend gezeigt.

**Beispiel für eine Antwort**

	{
	  "Results": {
	    "output1": {
	      "type": "DataTable",
	      "value": {
	        "ColumnNames": [
	          "cog_speed"
	        ],
	        "ColumnTypes": [
	          "Numeric"
	        ].
	      "Values": [
	        [
	          "0"
	        ],
	        [
	          "1"
	        ]
	      ]
	    }
	  },
	  "GlobalParameters": {}
	}

Unten auf der Hilfeseite finden Sie die Codebeispiele. Nachstehend sehen Sie das Codebeispiel für die C#-Implementierung.

**Beispielcode in C#**

	using System;
	using System.Collections.Generic;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Formatting;
	using System.Net.Http.Headers;
	using System.Text;
	using System.Threading.Tasks;

	namespace CallRequestResponseService
	{
	    public class StringTable
	    {
	        public string[] ColumnNames { get; set; }
	        public string[,] Values { get; set; }
	    }

	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	        }

	        static async Task InvokeRequestResponseService()
	        {
	            using (var client = new HttpClient())
	            {
	                var scoreRequest = new
	                {
	                    Inputs = new Dictionary<string, StringTable> () {
	                        {
	                            "input1",
	                            new StringTable()
	                            {
	                                ColumnNames = new string[] {"cog_speed"},
	                                Values = new string[,] {  { "0"},  { "1"}  }
	                            }
	                        },
	                    GlobalParameters = new Dictionary<string, string>() { }
	                };

	                const string apiKey = "abc123"; // Replace this with the API key for the web service
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);

	                client.BaseAddress = new Uri("https://ussouthcentral.services.azureml.net/workspaces/<workspace id>/services/<service id>/execute?api-version=2.0&details=true");

	                // WARNING: The 'await' statement below can result in a deadlock if you are calling this code from the UI thread of an ASP.Net application.
	                // One way to address this would be to call ConfigureAwait(false) so that the execution does not attempt to resume on the original context.
	                // For instance, replace code such as:
	                //      result = await DoSomeTask()
	                // with the following:
	                //      result = await DoSomeTask().ConfigureAwait(false)

	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);

	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Result: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	}

**Beispielcode in Java**

Der folgende Beispielcode veranschaulicht die Erstellung einer REST-API-Anforderung in Java. Es wird davon ausgegangen, dass die Variablen („apikey“ und „apiurl“) die erforderlichen API-Details aufweisen, und die Variable „jsonBody“ über ein korrektes JSON-Objekt gemäß Erwartung der REST-API verfügt, um eine erfolgreiche Vorhersage zu gewährleisten. Sie können den vollständigen Code von Github herunterladen – [https://github.com/nk773/AzureML\_RRSApp](https://github.com/nk773/AzureML_RRSApp). Dieses Java-Beispiel erfordert die [Apache-HTTP-Clientbibliothek](https://hc.apache.org/downloads.cgi).

	/**
	 * Download full code from github - [https://github.com/nk773/AzureML_RRSApp](https://github.com/nk773/AzureML_RRSApp)
 	 */
    	/**
     	  * Call REST API for retrieving prediction from Azure ML 
     	  * @return response from the REST API
     	  */	
    	public static String rrsHttpPost() {
        
        	HttpPost post;
        	HttpClient client;
        	StringEntity entity;
        
        	try {
            		// create HttpPost and HttpClient object
            		post = new HttpPost(apiurl);
            		client = HttpClientBuilder.create().build();
            
            		// setup output message by copying JSON body into 
            		// apache StringEntity object along with content type
            		entity = new StringEntity(jsonBody, HTTP.UTF_8);
            		entity.setContentEncoding(HTTP.UTF_8);
            		entity.setContentType("text/json");

            		// add HTTP headers
            		post.setHeader("Accept", "text/json");
            		post.setHeader("Accept-Charset", "UTF-8");
        
            		// set Authorization header based on the API key
            		post.setHeader("Authorization", ("Bearer "+apikey));
            		post.setEntity(entity);

            		// Call REST API and retrieve response content
            		HttpResponse authResponse = client.execute(post);
            
            		return EntityUtils.toString(authResponse.getEntity());
            
        	}
        	catch (Exception e) {
            
            		return e.toString();
        	}
    
    	}
    
    	
 

### BES-Beispiel
Im Gegensatz zum RRS-Dienst ist der BES-Dienst asynchron. Dies bedeutet, dass die Bus-API einen auszuführenden Auftrag in die Warteschlange reiht und der Aufrufer den Auftragsstatus abruft, um zu sehen, wann dieser abgeschlossen wurde. Hier sind die Vorgänge, die derzeit für Batchaufträge unterstützt werden:

1. Erstellen (Einreichen) eines Batchauftrags
1. Starten des Batchauftrags
1. Abrufen des Status oder des Ergebnisses des Batchauftrags
1. Abbrechen eines Batchauftrag, der ausgeführt wird

**1. Erstellen Sie einen Batchausführungsauftrag**

Beim Erstellen eines Batchauftrags für Ihren Azure Machine Learning-Dienstendpunkt können Sie mehrere Parameter angeben, die diese Batchausführung definieren:

* **Input**: ein Blobverweis auf den Speicherort der Eingabe des Batchauftrags.
* **GlobalParameters**: eine Gruppe globaler Parameter, die für ein Experiment definiert werden können. Ein Azure Machine Learning-Experiment kann erforderliche und optionale Parameter haben, die die Ausführung des Diensts individuell anpassen. Außerdem wird vom Aufrufer erwartet, ggf. alle benötigten Parameter bereitzustellen. Diese Parameter werden als eine Auflistung von Schlüssel-Wert-Paaren angegeben.
* **Outputs**: wenn für den Dienst eine oder mehrere Ausgaben definiert sind, kann der Aufrufer beliebig viele an einen Azure-Blobspeicherort umleiten. Dadurch können Sie die Ausgaben des Dienst an einem gewünschten Speicherort und unter einem vorhersagbaren Namen speichern, da ansonsten der Name des Ausgabeblobs zufällig generiert wird. 

    Der Dienst erwartet, dass der Inhalt der Ausgabe basierend auf seinem Typ in unterstützten Formaten gespeichert wird:
  - Datasetausgaben können als **.csv, .tsv, .arff** gespeichert werden.
  - Ausgaben trainierter Modelle können als **.ilearner** gespeichert werden.

  Die Überschreibungen des Ausgabespeicherorts werden als Auflistung von *<output name  blob reference>*-Paaren angegeben, in denen der *output name* der benutzerdefinierte Name eines bestimmten Ausgabeknotens ist (der auch auf der API-Hilfeseite des Diensts angezeigt wird). *blob reference* ist ein Verweis auf einen Azure-Blobspeicherort, an den die Ausgabe umgeleitet werden soll.

Diese Auftragserstellungsparameter können je nach Art Ihres Diensts optional sein. Dienste ohne definierten Eingabeknoten erfordern z. B. nicht, das an sie ein *Input*-Parameter übergeben wird. Die Überschreibungsfunktion für den Ausgabespeicherort ist gänzlich optional, da Ausgaben andernfalls im Standardspeicherkonto gespeichert werden, das für Ihren Azure Machine Learning-Arbeitsbereich eingerichtet wurde. Nachstehend wird ein Beispiel einer Anforderungsnutzlast für einen Dienst als an die REST-API übergeben gezeigt, bei dem nur die Eingabe-Informationen übergeben werden:

**Beispiel für eine Anforderung**

	{
	  "Input": {
	    "ConnectionString":     
	    "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
	    "RelativeLocation": "/mycontainer/mydatablob.csv",
	    "BaseLocation": null,
	    "SasBlobToken": null
	  },
	  "Outputs": null,
	  "GlobalParameters": null
	}

Die Antwort auf die Batchauftragserstellungs-API ist die eindeutige Auftrags-ID, die mit Ihrem Auftrags verknüpft war. Diese ID ist sehr wichtig, da sie Ihre einzige Möglichkeit darstellt, für andere Vorgänge auf diesen Auftrag im System zu verweisen.

**Beispiel für eine Antwort**

	"539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**2. Starten eines Batchausführungsauftrags**

Ein erstellter Batchauftrag wird innerhalb des Systems registriert und erhält den Zustand *Not started*. Um tatsächlich die Ausführung des Auftrags zu planen, rufen Sie die **start**-API auf, die auf der API-Hilfeseite des Dienstendpunkts beschrieben ist, und geben die Auftrags-ID an, die vergeben wurde, als der Auftrag erstellt wurde.

**3. Abrufen des Status eines Batchausführungsauftrags**

Sie können jederzeit den Status Ihres asynchronen Batchauftrags abfragen, indem Sie die Auftrags-ID an die „GetJobStatus“-API übergeben. Die API-Antwort enthält einen Indikator für den aktuellen Status des Auftrags sowie die tatsächlichen Ergebnisse des Batchauftrags, wenn der Vorgang erfolgreich abgeschlossen wurde. Bei einem Fehler werden weitere Informationen über die tatsächlichen Gründe für den Fehler in der Eigenschaft *Details* zurückgegeben (siehe die folgende Abbildung):

**Antwortnutzlast**

	{
	    "StatusCode": STATUS_CODE,
	    "Results": RESULTS,
	    "Details": DETAILS
	}

Der *StatusCode* kann wie folgt lauten:

* Nicht gestartet
* Wird ausgeführt
* Fehler
* Abgebrochen
* Abgeschlossen

Die Eigenschaft *Ergebnisse* wird nur aufgefüllt, wenn der Auftrag erfolgreich abgeschlossen wurde (andernfalls ist er **null**). Wenn der Auftrag abgeschlossen wurde und für den Dienst mindestens ein Ausgabeknoten definiert ist, werden die Ergebnisse als Auflistung von Paaren von *[Ausgabename, Blobverweis]* zurückgegeben, für die der Blobverweis ein SAS-schreibgeschützter Verweis auf das Blob mit dem tatsächlichen Ergebnis ist.

**Beispiel für eine Antwort**

	{
	    "Status Code": "Finished",
	    "Results":
	    {
	        "dataOutput":
	        {              
	            "ConnectionString": null,
	            "RelativeLocation": "outputs/dataOutput.csv",
	            "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
	            "SasBlobToken": "?sv=2013-08-15&sr=b&sig=ABCD&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
	        },
	        "trainedModelOutput":
	        {              
	            "ConnectionString": null,
	            "RelativeLocation": "models/trainedModel.ilearner",
	            "BaseLocation": "https://mystorageaccount.blob.core.windows.net/",
	            "SasBlobToken": "?sv=2013-08-15&sr=b&sig=EFGH%3D&st=2015-04-04T05%3A39%3A55Z&se=2015-04-05T05%3A44%3A55Z&sp=r"              
	        },           
	    },
	    "Details": null
	}

**4. Abbrechen eines Batchausführungsauftrags**

Ein Batchauftrag, der ausgeführt wird, kann jederzeit abgebrochen werden, indem die festgelegte CancelJob-API aufgerufen und die Auftrags-ID übergeben wird. Dies kann aus verschiedenen Gründen geschehen, etwa weil der Auftrag zu lange dauert.



#### Verwenden des BES SDK

Das [BES SDK NuGet-Paket](http://www.nuget.org/packages/Microsoft.Azure.MachineLearning/) bietet Funktionen, die das Aufrufen von BES zur Bewertung im Batchmodus vereinfachen. Um das NuGet-Paket zu installieren, wählen Sie in Visual Studio unter **Tools** den **NuGet-Paket-Manager** aus und klicken dann auf **Paket-Manager-Konsole**.

Azure Machine Learning-Experimente, die als Webdienste bereitgestellt werden, können Webdienst-Eingabemodule einschließen. Dies bedeutet, dass sie erwarten, dass die Eingabe über den Webdienstaufruf in Form eines Verweises auf einen Blobspeicherort bereitgestellt wird. Es gibt auch die Möglichkeit, kein Webdienst-Eingabemodul zu verwenden und stattdessen ein **Reader**-Modul zu nutzen. In diesem Fall liest das **Reader**-Modul in der Regel Daten aus einer SQL-Datenbank, indem zur Laufzeit Daten mithilfe einer Abfrage abgerufen werden. Webdienst-Parameter können verwendet werden, um dynamisch auf andere Server oder Tabellen usw. zu verweisen. Das SDK unterstützt beide dieser Muster.

Im folgenden Codebeispiel wird veranschaulicht, wie Sie einen Batchauftrag anhand eines Azure Machine Learning-Dienstendpunkts mithilfe von BES SDK senden und überwachen können. Beachten Sie die Kommentare für Informationen zu den Einstellungen und Aufrufen.

#### **Beispielcode**

	// This code requires the Nuget package Microsoft.Azure.MachineLearning to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.Azure.MachineLearning

	  using System;
	  using System.Collections.Generic;
	  using System.Threading.Tasks;

	  using Microsoft.Azure.MachineLearning;
	  using Microsoft.Azure.MachineLearning.Contracts;
	  using Microsoft.Azure.MachineLearning.Exceptions;

	namespace CallBatchExecutionService
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {	            
	            InvokeBatchExecutionService().Wait();
	        }

	        static async Task InvokeBatchExecutionService()
	        {
	            // First collect and fill in the URI and access key for your web service endpoint.
	            // These are available on your service's API help page.
	            var endpointUri = "https://ussouthcentral.services.azureml.net/workspaces/YOUR_WORKSPACE_ID/services/YOUR_SERVICE_ENDPOINT_ID/";
	            string accessKey = "YOUR_SERVICE_ENDPOINT_ACCESS_KEY";

	            // Create an Azure Machine Learning runtime client for this endpoint
	            var runtimeClient = new RuntimeClient(endpointUri, accessKey);

	            // Define the request information for your batch job. This information can contain:
	            // -- A reference to the AzureBlob containing the input for your job run
	            // -- A set of values for global parameters defined as part of your experiment and service
	            // -- A set of output blob locations that allow you to redirect the job's results

	            // NOTE: This sample is applicable, as is, for a service with explicit input port and
	            // potential global parameters. Also, we choose to also demo how you could override the
	            // location of one of the output blobs that could be generated by your service. You might
	            // need to tweak these features to adjust the sample to your service.
	            //
	            // All of these properties of a BatchJobRequest shown below can be optional, depending on
	            // your service, so it is not required to specify all with any request.  If you do not want to
	            // use any of the parameters, a null value should be passed in its place.

	            // Define the reference to the blob containing your input data. You can refer to this blob by its
                    // connection string / container / blob name values; alternatively, we also support references
                    // based on a blob SAS URI

                    BlobReference inputBlob = BlobReference.CreateFromConnectionStringData(connectionString:                                         "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                        containerName: "YOUR_CONTAINER_NAME",
                        blobName: "YOUR_INPUT_BLOB_NAME");

                    // If desired, one can override the location where the job outputs are to be stored, by passing in
                    // the storage account details and name of the blob where we want the output to be redirected to.

                    var outputLocations = new Dictionary<string, BlobReference>
                        {
                          {
                           "YOUR_OUTPUT_NODE_NAME",
                           BlobReference.CreateFromConnectionStringData(                                     connectionString: "DefaultEndpointsProtocol=https;AccountName=YOUR_ACCOUNT_NAME;AccountKey=YOUR_ACCOUNT_KEY",
                                containerName: "YOUR_CONTAINER_NAME",
                                blobName: "YOUR_DESIRED_OUTPUT_BLOB_NAME")
                           }
                        };

	            // If applicable, you can also set the global parameters for your service
	            var globalParameters = new Dictionary<string, string>
	            {
	                { "YOUR_GLOBAL_PARAMETER", "PARAMETER_VALUE" }
	            };

	            var jobRequest = new BatchJobRequest
	            {
	                Input = inputBlob,
	                GlobalParameters = globalParameters,
	                Outputs = outputLocations
	            };

	            try
	            {
	                // Register the batch job with the system, which will grant you access to a job object
	                BatchJob job = await runtimeClient.RegisterBatchJobAsync(jobRequest);

	                // Start the job to allow it to be scheduled in the running queue
	                await job.StartAsync();

	                // Wait for the job's completion and handle the output
	                BatchJobStatus jobStatus = await job.WaitForCompletionAsync();
	                if (jobStatus.JobState == JobState.Finished)
	                {
	                    // Process job outputs
	                    Console.WriteLine(@"Job {0} has completed successfully and returned {1} outputs", job.Id, jobStatus.Results.Count);
	                    foreach (var output in jobStatus.Results)
	                    {
	                        Console.WriteLine(@"\t{0}: {1}", output.Key, output.Value.AbsoluteUri);
	                    }
	                }
	                else if (jobStatus.JobState == JobState.Failed)
	                {
	                    // Handle job failure
	                    Console.WriteLine(@"Job {0} has failed with this error: {1}", job.Id, jobStatus.Details);
	                }
	            }
	            catch (ArgumentException aex)
	            {
	                Console.WriteLine("Argument {0} is invalid: {1}", aex.ParamName, aex.Message);
	            }
	            catch (RuntimeException runtimeError)
	            {
	                Console.WriteLine("Runtime error occurred: {0} - {1}", runtimeError.ErrorCode, runtimeError.Message);
	                Console.WriteLine("Error details:");
	                foreach (var errorDetails in runtimeError.Details)
	                {
	                    Console.WriteLine("\t{0} - {1}", errorDetails.Code, errorDetails.Message);
	                }
	            }
	            catch (Exception ex)
	            {
	                Console.WriteLine("Unexpected error occurred: {0} - {1}", ex.GetType().Name, ex.Message);
	            }
	        }
	    }
	}

#### Beispielcode in Java für BES
Der Batchausführungsdienst REST-API akzeptiert die JSON, die aus einem Verweis auf eine Eingabebeispiel-CSV- und eine Ausgabebeispiel-CSV-Datei – wie unten dargestellt – besteht, und erstellt in Azure ML einen Auftrag zur Ausführung der Batchvorhersagen. Sie können den gesamten Code in [Github](https://github.com/nk773/AzureML_BESApp/tree/master/src/azureml_besapp) anzeigen. Dieses Java-Beispiel erfordert die [Apache-HTTP-Clientbibliothek](https://hc.apache.org/downloads.cgi).


	{ "GlobalParameters": {}, 
    	"Inputs": { "input1": { "ConnectionString": 	"DefaultEndpointsProtocol=https;
			AccountName=myAcctName; AccountKey=Q8kkieg==", 
        	"RelativeLocation": "myContainer/sampleinput.csv" } }, 
    	"Outputs": { "output1": { "ConnectionString": 	"DefaultEndpointsProtocol=https;
			AccountName=myAcctName; AccountKey=kjC12xQ8kkieg==", 
        	"RelativeLocation": "myContainer/sampleoutput.csv" } } 
	} 


#####Erstellen eines BES-Auftrags	
	    
	    /**
	     * Call REST API to create a job to Azure ML 
	     * for batch predictions
	     * @return response from the REST API
	     */	
	    public static String besCreateJob() {
	        
	        HttpPost post;
	        HttpClient client;
	        StringEntity entity;
	        
	        try {
	            // create HttpPost and HttpClient object
	            post = new HttpPost(apiurl);
	            client = HttpClientBuilder.create().build();
	            
	            // setup output message by copying JSON body into 
	            // apache StringEntity object along with content type
	            entity = new StringEntity(jsonBody, HTTP.UTF_8);
	            entity.setContentEncoding(HTTP.UTF_8);
	            entity.setContentType("text/json");
	
	            // add HTTP headers
	            post.setHeader("Accept", "text/json");
	            post.setHeader("Accept-Charset", "UTF-8");
	        
	            // set Authorization header based on the API key
				// note a space after the word "Bearer " - don't miss that
	            post.setHeader("Authorization", ("Bearer "+apikey));
	            post.setEntity(entity);
	
	            // Call REST API and retrieve response content
	            HttpResponse authResponse = client.execute(post);
	            
	            jobId = EntityUtils.toString(authResponse.getEntity()).replaceAll(""", "");
	            
	            
	            return jobId;
	            
	        }
	        catch (Exception e) {
	            
	            return e.toString();
	        }
	    
	    }
	    
#####Starten eines zuvor erstellten BES-Auftrags	        
	    /**
	     * Call REST API for starting prediction job previously submitted 
	     * 
	     * @param job job to be started 
	     * @return response from the REST API
	     */	
	    public static String besStartJob(String job){
	        HttpPost post;
	        HttpClient client;
	        StringEntity entity;
	        
	        try {
	            // create HttpPost and HttpClient object
	            post = new HttpPost(startJobUrl+"/"+job+"/start?api-version=2.0");
	            client = HttpClientBuilder.create().build();
	         
	            // add HTTP headers
	            post.setHeader("Accept", "text/json");
	            post.setHeader("Accept-Charset", "UTF-8");
	        
	            // set Authorization header based on the API key
	            post.setHeader("Authorization", ("Bearer "+apikey));
	
	            // Call REST API and retrieve response content
	            HttpResponse authResponse = client.execute(post);
	            
	            if (authResponse.getEntity()==null)
	            {
	                return authResponse.getStatusLine().toString();
	            }
	            
	            return EntityUtils.toString(authResponse.getEntity());
	            
	        }
	        catch (Exception e) {
	            
	            return e.toString();
	        }
	    }
#####Abbrechen eines zuvor erstellten BES-Auftrags
	    
	    /**
	     * Call REST API for canceling the batch job 
	     * 
	     * @param job job to be started 
	     * @return response from the REST API
	     */	
	    public static String besCancelJob(String job) {
	        HttpDelete post;
	        HttpClient client;
	        StringEntity entity;
	        
	        try {
	            // create HttpPost and HttpClient object
	            post = new HttpDelete(startJobUrl+job);
	            client = HttpClientBuilder.create().build();
	         
	            // add HTTP headers
	            post.setHeader("Accept", "text/json");
	            post.setHeader("Accept-Charset", "UTF-8");
	        
	            // set Authorization header based on the API key
	            post.setHeader("Authorization", ("Bearer "+apikey));
	
	            // Call REST API and retrieve response content
	            HttpResponse authResponse = client.execute(post);
	         
	            if (authResponse.getEntity()==null)
	            {
	                return authResponse.getStatusLine().toString();
	            }
	            return EntityUtils.toString(authResponse.getEntity());
	            
	        }
	        catch (Exception e) {
	            
	            return e.toString();
	        }
	    }
	    
###Andere Programmierumgebungen
Sie können den Code auch in vielen anderen Sprachen generieren, indem Sie ein Swagger-Dokument aus der API-Hilfeseite verwenden und die Anweisungen auf der Website [swagger.io](http://swagger.io/) befolgen. Wechseln Sie zu [swagger.io](http://swagger.io/swagger-codegen/), und befolgen Sie die Anweisungen zum Herunterladen von Swagger-Code, Java und Apache Maven. Hier ist die Zusammenfassung der Anweisungen zum Einrichten von Swagger für andere Programmierumgebungen.

* Stellen Sie sicher, dass Java 7 oder höher installiert ist.
* Installieren Sie Apache Maven (unter Ubuntu können Sie *apt-get install mvn* verwenden)
* Rufen Sie Github für Swagger auf, und laden Sie das Swagger-Projekt als ZIP-Datei herunter
* Entzippen Sie Swagger
* Erstellen Sie die Swagger-Tools durch Ausführen von *mvn package* im Swagger-Quellverzeichnis

Jetzt können Sie beliebige Swagger-Tools verwenden. Hier sind die Anweisungen, um Java-Clientcode zu generieren.

* Rufen Sie die Azure ML-API-Hilfeseite auf (Beispiel [hier](https://studio.azureml.net/apihelp/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/jobs))
* Suchen Sie die URL für „swagger.json“ für Azure ML-REST-APIs (zweitletztes Aufzählungszeichen oben auf der API-Hilfeseite)
* Klicken Sie auf den Link zum Swagger-Dokument (Beispiel [hier](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument))
* Verwenden Sie den folgenden Befehl, wie in der [Readme-Datei von Swagger](https://github.com/swagger-api/swagger-codegen/blob/master/README.md) gezeigt, zum Generieren von Clientcode

**Beispielbefehlszeile zum Generieren von Clientcode**

	java -jar swagger-codegen-cli.jar generate\
	 -i https://ussouthcentral.services.azureml.net:443/workspaces/\
	fb62b56f29fc4ba4b8a8f900c9b89584/services/26a3afce1767461ab6e73d5a206fbd62/swagger.json\
	 -l java -o /home/username/sample

* Kombinieren Sie die Werte in den Feldern „host“, „basePath“ und „/swagger.JSON“ im Beispiel einer unten gezeigten Swagger-[API-Hilfeseite](https://management.azureml.net/workspaces/afbd553b9bac4c95be3d040998943a4f/webservices/4dfadc62adcc485eb0cf162397fb5682/endpoints/26a3afce1767461ab6e73d5a206fbd62/apidocument), um die in der obigen Befehlszeile verwendete Swagger-URL zu erstellen

**Beispiel-API-Hilfeseite**


	{
	  "swagger": "2.0",
	  "info": {
	    "version": "2.0",
	    "title": "Sample 5: Binary Classification with Web Service: Adult Dataset [Predictive Exp.]",
	    "description": "No description provided for this web service.",
	    "x-endpoint-name": "default"
	  },
	  "host": "ussouthcentral.services.azureml.net:443",
	  "basePath": "/workspaces/afbd553b9bac4c95be3d040998943a4f/services/26a3afce1767461ab6e73d5a206fbd62",
	  "schemes": [
	    "https"
	  ],
	  "consumes": [
	    "application/json"
	  ],
	  "produces": [
	    "application/json"
	  ],
	  "paths": {
	    "/swagger.json": {
	      "get": {
	        "summary": "Get swagger API document for the web service",
	        "operationId": "getSwaggerDocument",
	        

<!---HONumber=AcomDC_0224_2016-->