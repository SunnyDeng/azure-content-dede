<properties 
	pageTitle="Nutzen eines Machine Learning-Webdiensts, der von einem Machine Learning-Experiment aus veröffentlicht wurde | Azure" 
	description="Sobald ein Machine Learning-Dienst veröffentlicht wurde, kann der zur Verfügung gestellte RESTFul-Webdienst als Antwort-/Anfrage-Dienst oder als Stapelausführungsdienst genutzt werden." 
	services="machine-learning" 
	solutions="big-data" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="tbd" 
	ms.date="02/20/2015" 
	ms.author="bradsev"/>


# Nutzen eines veröffentlichten Machine Learning-Webdiensts

## Einführung

Beim Veröffentlichen als Webdienst bieten Azure Machine Learning-Experimente eine REST-API, die von einer Vielzahl von Geräten und Plattformen genutzt werden kann, denn die einfache REST-API akzeptiert JSON-formatierte Nachrichten und antwortet mit diesen. Das Azure Machine Learning-Portal bietet Code zum Aufrufen des Webdiensts in R, C# und Python. Diese Dienste können jedoch in jeder beliebigen Programmiersprache und von jedem Gerät aus aufgerufen werden, sofern die drei folgenden Kriterien erfüllt sind:

* Verfügt über eine Netzwerkverbindung
* Verfügt über SSL-Funktionen zum Ausführen von HTTPS-Anforderungen
* Verfügt über die Möglichkeit zum Analysieren von JSON (manuell oder durch Support-Bibliotheken)

Dies bedeutet, dass die Dienste von Webanwendungen, mobilen Anwendungen, benutzerdefinierten Desktopanwendungen und selbst in Excel genutzt werden können!  

Ein Azure Machine Learning-Webdienst kann auf zwei unterschiedliche Arten verarbeitet werden, als Antwort-/Anfrage-Dienst oder als Stapelausführungsdienst. In jedem Szenario wird die Funktionalität über den RESTFul-Webdienst bereitgestellt, der für die Nutzung verfügbar gemacht wird, sobald das Experiment veröffentlicht wurde. Durch Bereitstellen eines Machine Learning-Webdiensts in Azure mit einem Azure-Webdienst-Endpunkt, bei dem der Dienst automatisch anhand der Nutzung skaliert wird, können Sie im Vorfeld anfallende und fortlaufende Kosten für Hardwareressourcen vermeiden.

<!-- Bei Veröffentlichung dieses Artikels den Link reparieren und die Auskommentierung aufheben
Weitere Informationen zum Verwalten von Azure Machine Learning-Webdienst-Endpunkten mithilfe der REST-API finden Sie unter **Azure Machine Learning-Webdienst-Endpunkte**. 
-->

Informationen zum Erstellen und Veröffentlichen eines Azure Machine Learning-Webdiensts 
finden Sie unter [Veröffentlichen des Azure Machine Learning-Webdiensts][publish]. Eine schrittweise Anleitung zum Erstellen und Veröffentlichen eines Machine Learning-Experiments finden Sie unter [Entwickeln einer vorhersehbaren Lösung mit Azure Machine Learning][walkthrough].

[publish]: machine-learning-publish-a-machine-learning-web-service.md
[walkthrough]: machine-learning-walkthrough-develop-predictive-solution.md


## Antwort-/Anfrage-Dienst (Request-Response Service RRS)

Ein Antwort-/Anfrage-Dienst (Request-Response Service, RRS) ist ein hochgradig skalierbarer Webdienst mit niedriger Latenz, mit dem eine Schnittstelle für die aus einem Azure Machine Learning Studio-Experiment erstellten und veröffentlichten zustandslosen Modelle bereitgestellt werden kann.

RRS akzeptiert eine einzige Reihe von Eingabeparametern und generiert eine einzige Reihe als Ausgabe. Die Ausgabereihe kann mehrere Spalten enthalten.

Ein Beispiel für RRS ist das Überprüfen der Authentizität einer Anwendung. Hunderte bis Millionen von Installationen einer Anwendung können in diesem Fall erwartet werden. Wenn die Anwendung gestartet wird, ruft sie den RRS-Dienst mit der entsprechenden Eingabe auf. Die Anwendung empfängt dann eine Überprüfungsantwort vom Dienst, die das Ausführen der Anwendung zulässt oder blockiert.


## Stapelausführungsdienst (Batch Execution Service BES)
 
Ein Stapelausführungsdienst (Batch Execution Service, BES) ist ein Dienst zur asynchronen Bewertung eines umfangreichen Stapels von Datensätzen. Die Eingabe für den BES enthält einen Stapel von Datensätzen aus verschiedenen Quellen, z. B. Blobs, Tabellen in Azure, SQL Azure, HDInsight (beispielsweise Ergebnisse einer Hive-Abfrage) oder HTTP-Quellen. Die Ausgabe des BES enthält die Ergebnisse der Wertung. Die Ergebnisse werden in eine Datei im Azure-Blob-Speicher geschrieben, und Daten aus dem Speicherendpunkt werden in der Antwort zurückgegeben.

Ein BES ist nützlich, wenn Antworten nicht sofort benötigt werden, z. B. für regelmäßig geplante Bewertungen für einzelne Benutzer oder Geräte im Internet der Dinge (IOT).

## Beispiele
Zum Erläutern der Funktionsweise von RRS und BES greifen wir auf ein Beispiel für einen Azure-Webdienst zurück. Dieser Dienst wird in einem IOT-Szenario (Internet der Dinge) verwendet werden. Der Einfachheit halber sendet unser Gerät nur einen Wert, `cog_speed`, und erhält eine einzelne Antwort zurück. 

Vier Arten von Informationen sind erforderlich, um den RRS- oder den BES-Dienst aufzurufen. Diese Informationen sind von den [Azure Machine Learning-Dienstseiten](https://studio.azureml.net) verfügbar, nachdem das Experiment veröffentlicht wurde. Klicken Sie auf den Link "WEB SERVICES" auf der linken Seite des Bildschirms, um die veröffentlichten Dienste anzuzeigen. Zum Suchen von Informationen über einen bestimmten Dienst gibt es Links zu API-Hilfeseiten für RRS und BES.

1.	Der **Dienst-API-Schlüssel**, verfügbar auf der Hauptseite von Diensten
2.	Die **Dienst-URI**, verfügbar auf der API-Hilfeseite für den ausgewählten Dienst
3.	Der erwartete **API-Anforderungstext**, verfügbar auf der API-Hilfeseite für den ausgewählten Dienst
4.	Der erwartete **API-Antworttext**, verfügbar auf der API-Hilfeseite für den ausgewählten Dienst

In den folgenden beiden Beispielen wird die Sprache C# verwendet, um den erforderlichen Code zu veranschaulichen. Die Zielplattform ist dabei ein Windows 8-Desktop. 

### RRS-Beispiel
Auf der API-Hilfeseite finden Sie neben der URI Eingabe- und Ausgabedefinitionen sowie Codebeispiele. Die API-Eingabe wird speziell für diesen Dienst aufgerufen und bildet die Nutzlast des API-Aufrufs. 

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


Auf ähnliche Weise wird auch die API-Antwort speziell erneut für diesen Dienst aufgerufen.

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

Unten auf der Seite finden Sie die Codebeispiele. Im Folgenden finden Sie das Codebeispiel für die Implementierung in C#. 
                   
**Beispielcode**
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

### BES-Beispiel
Auf der API-Hilfeseite finden Sie neben der URI Informationen über mehrere verfügbare Aufrufe. Im Gegensatz zum RRS-Dienst ist der BES-Dienst asynchron. Dies bedeutet, dass es sich bei der BES-API einfach um eine Warteschlange auszuführender Aufträge handelt. Diese wird jedoch nicht ausgeführt, bevor die API-Antwort empfangen wird. Es gibt für Entwickler drei Möglichkeiten für die Verwendung des BES-Diensts, die nachstehend beschrieben werden.

1. Übermitteln eines Stapelausführungsauftrags
1. Abrufen des Status oder des Ergebnisses eines Stapelausführungsauftrags
1. Löschen eines Stapelausführungsauftrags  

**1. Übermitteln eines Stapelausführungsauftrags**

Sie senden einen Stapelausführungsauftrag durch Bereitstellen von Informationen zum Speicherort der Stapeldaten. In diesem Beispiel wird davon ausgegangen, dass sich die Datensätze die stapelweise bewertet werden sollen, in einer Blobdatei in einem Speicherkonto befinden.

Die Antwort auf einen Stapelverarbeitungsauftrag ist wiederum eine Auftrags-ID, da der Auftrag asynchron ausgeführt wird. Wir verwenden die Auftrags-ID, um den Auftragsstatus und die Ergebnisse zu einem späteren Zeitpunkt abrufen.

**Beispiel für eine Anforderung**

	{
	  "Input": {
	    "ConnectionString":     
	    "DefaultEndpointsProtocol=https;AccountName=mystorageacct;AccountKey=mystorageacctKey",
	    "RelativeLocation": "/mycontainer/mydatablob.csv",
	    "BaseLocation": null,
	    "SasBlobToken": null
	  },
	  "Output": null,
	  "GlobalParameters": {}
	}

**Beispiel für eine Antwort**

	"539d0bc2fde945b6ac986b851d0000f0" // The JOB_ID

**Beispielcode**

	// This code requires the Nuget package Microsoft.AspNet.WebApi.Client to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.AspNet.WebApi.Client
	
	using System;
	using System.Collections.Generic;
	using System.Net.Http;
	using System.Threading.Tasks;
	using System.Net.Http.Headers;
	
	namespace CallBatchExecutionService
	{
	    internal class Program
	    {
	        private static void Main(string[] args)
	        {
	            InvokeBatchExecutionService().Wait();
	        }
	
	        private static async Task InvokeBatchExecutionService()
	        {
	            // API Information
	            const string BESUrl = "[BES URI]";
	            const string ApiKey = "abc123"; 
	            // The storage account information
	            const string StorageAccountName = @"mystorageacct"; 
	            const string StorageAccountKey = @"Dx9WbMIThAvXRQWap/aLnxT9LV5txxw==";
	            // Storage file with the batch of records
	            const string StorageInputFile = @"/mycontainermydatablob.csv"; 
	
	
	            String connString = String.Format(
	                "DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
	                StorageAccountName,
	                StorageAccountKey);
	
	            BatchRequest request = new BatchRequest();
	            request.Input.RelativeLocation = StorageInputFile;
	            request.Input.ConnectionString = connString;
	
	            using (var client = new HttpClient())
	            {
	                client.BaseAddress = new Uri(BESUrl);
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", ApiKey);
	
	                HttpResponseMessage response = await client.PostAsJsonAsync("", request);
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    Console.WriteLine("Job ID: {0}", result);
	                }
	                else
	                {
	                    Console.WriteLine("Failed with status code: {0}", response.StatusCode);
	                }
	            }
	        }
	    }
	
	    public class BatchInput
	    {
	        public String ConnectionString { get; set; }
	        public String RelativeLocation { get; set; }
	        public String BaseLocation { get; set; }
	        public String SasBlobToken { get; set; }
	
	        public BatchInput()
	        {
	            ConnectionString = null;
	            RelativeLocation = null;
	            BaseLocation = null;
	            SasBlobToken = null;
	        }
	    }
	
	    public class BatchRequest
	    {
	        public BatchInput Input { get; set; }
	
	        public Object Output { get; set; }
	
	        public Dictionary<string, string> GlobalParameters { get; set; }
	
	        public BatchRequest()
	        {
	            this.GlobalParameters = new Dictionary<string, string>();
	            Input = new BatchInput();
	            Output = null;
	        }
	    }
	}
	
**2. Abrufen des Status oder des Ergebnisses eines Stapelausführungsauftrags**

Um das Ergebnis eines Auftrags abzurufen, müssen Sie über die Auftrags-ID verfügen, die in der Antwort auf die Auftragsübermittlung enthalten war. Es gibt keine echte Eingabe zu diesem API-Aufruf. Es wird eine geringfügige Änderung der BES-URI und der Anforderungsmethode verwendet. Anstelle einer POST-Anforderung wird eine GET-Anforderung nach der auf der API-Hilfeseite definierten URI verwendet.
 
Die Antwort ist jedoch in Ebenen unterteilt.

**Antwortnutzlast**

	{
	    "StatusCode": STATUS_CODE,
	    "Result": RESULT,
	    "Details": DETAILS
	}

`StatusCode` kann den Wert 0, 1, 2, 3 oder 4 aufweisen, wobei folgende Semantik gilt:

* 0	Nicht gestartet
* 1	Ausgeführt
* 2	Fehlgeschlagen
* 3	Abgebrochen
* 4	Abgeschlossen

Wenn der Auftrag noch nicht abgeschlossen wurde, ist `Result` **null**. Wenn der Auftrag abgeschlossen ist, hat `Result` die Form: 

	{
	  "ConnectionString": null,
	  "RelativeLocation": "RELATIVE_LOCATION",
	  "BaseLocation": "BASE_LOCATION",
	  "SasBlobToken": "SAS_BLOB_TOKEN"
	}

"Details" enthält ggf. die Fehlerdetails.

**Beispielcode**

	// This code requires the Nuget package Microsoft.AspNet.WebApi.Client to be installed.
	// Instructions for doing this in Visual Studio:
	// Tools -> Nuget Package Manager -> Package Manager Console
	// Install-Package Microsoft.AspNet.WebApi.Client
	//
	// Also, add a reference to Microsoft.WindowsAzure.Storage.dll for reading from and writing to the Azure blob storage
	
	using System;
	using System.IO;
	using System.Net.Http;
	using System.Net.Http.Headers;
	using System.Threading.Tasks;
	using Newtonsoft.Json;
	
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;
	
	namespace CallBatchExecutionService
	{
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            String jobId = "123";
	            InvokeBatchExecutionService(jobId).Wait();
	        }
	
	        static async Task InvokeBatchExecutionService(String JobId)
	        {
	            Console.WriteLine(String.Format("Getting job status for job {0}", JobId));
	
	            // BES Information
	            const string BaseUrl = @"[BES Job Id]/{0}";
	            const string ApiKey = "abc123"; 
	            // Replace this with the location you would like to use for your output file
	            const string OutputFileLocation = @"myresults.csv"; 
	
	            using (var client = new HttpClient())
	            {
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", ApiKey);
	
	                HttpResponseMessage response = await client.GetAsync(String.Format(BaseUrl, JobId));
	                if (response.IsSuccessStatusCode)
	                {
	                    string result = await response.Content.ReadAsStringAsync();
	                    BatchResponseStructure responseStruct = JsonConvert.DeserializeObject<BatchResponseStructure>(result);
	
	                    switch (responseStruct.StatusCode)
	                    {
	                        case (int)BatchScoreStatusCode.NotStarted:
	                            Console.WriteLine("Not started...");
	                            break;
	                        case (int)BatchScoreStatusCode.Running:
	                            Console.WriteLine("Running...");
	                            break;
	                        case (int)BatchScoreStatusCode.Failed:
	                            Console.WriteLine("Failed!");
	                            Console.WriteLine(string.Format(@"Error details: {0}", status.Details));
	                            break;
	                        case (int)BatchScoreStatusCode.Cancelled:
	                            Console.WriteLine("Cancelled!");
	                            break;
	                        case (int)BatchScoreStatusCode.Finished:
	                            Console.WriteLine("Finished!");
	                            var credentials = new StorageCredentials(status.Result.SasBlobToken);
	                            var cloudBlob = new CloudBlockBlob(new Uri(new Uri(responseStruct.Result.BaseLocation), 
	                                                                                               responseStruct.Result.RelativeLocation), credentials);
	                            cloudBlob.DownloadToFile(OutputFileLocation, FileMode.Create);
	                            Console.WriteLine(string.Format(@"The results have been written to the file {0}", OutputFileLocation));
	                            break;
	                    }
	                }
	                else
	                {
	                    Console.WriteLine(String.Format("Batch Result : Failed with status code: {0}", response.StatusCode));
	                }
	            }
	        }
	    }
	
	    public enum BatchScoreStatusCode : int
	    {
	        NotStarted = 0,
	        Running = 1,
	        Failed = 2,
	        Cancelled = 3,
	        Finished = 4
	    }
	
	    public class BatchResult
	    {
	        public String ConnectionString { get; set; }
	        public String RelativeLocation { get; set; }
	        public String BaseLocation { get; set; }
	        public String SasBlobToken { get; set; }
	    }
	
	    public class BatchResponseStructure
	    {
	        public int StatusCode { get; set; }
	        public BatchResult Result { get; set; }
	        public String Details { get; set; }
	        public BatchResponseStructure()
	        {
	            this.Result = new BatchResult();
	        }
	    }
	}

**3. Löschen eines Stapelausführungsauftrags**              
Ein Auftrag kann nach dem Start auch gelöscht werden. Dies kann aus verschiedenen Gründen geschehen, etwa weil der Auftrag zu lange dauert. Um einen Auftrag zu löschen, müssen Sie über die Auftrags-ID verfügen, die in der Antwort auf die Auftragsübermittlung enthalten war.

Es gibt keine echte Eingabe zu diesem API-Aufruf. Es wird eine geringfügige Änderung der BES-URI und der Anforderungsmethode verwendet. Anstelle einer POST-Anforderung wird eine DELETE-Anforderung nach der auf der API-Hilfeseite definierten URI verwendet. Das Codebeispiel dafür ist sehr einfach.


<!--HONumber=49-->