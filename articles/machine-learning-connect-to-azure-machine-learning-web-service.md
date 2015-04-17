<properties 
	pageTitle="Herstellen einer Verbindung mit einem Azure Machine Learning-Webdienst | Azure" 
	description="Stellen Sie mit C# oder Python mithilfe eines Autorisierungsschlüssels eine Verbindung mit einem Azure Machine Learning-Webdienst her." 
	services="machine-learning" 
	documentationCenter="" 
	authors="derrickv" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="derrickv"/>


# Herstellen einer Verbindung mit einem Azure Machine Learning-Webdienst 
Die Azure Machine Learning-Entwicklungsumgebung ist eine Webdienst-API zum Erstellen von Vorhersagen aus Eingabedaten in Echtzeit oder im Stapelmodus. Sie verwenden Azure Machine Learning Studio (ML Studio) zum Erstellen von Vorhersagen und Veröffentlichen eines Azure ML-Webdiensts. 

Informationen zum Erstellen und Veröffentlichen eines Azure Machine Learning-Webdiensts mit ML Studio finden Sie unter:

- [Übersicht über den Azure ML-Prozess](machine-learning-overview-of-azure-ml-process.md)
- [Erste Schritte mit ML Studio](http://azure.microsoft.com/documentation/videos/getting-started-with-ml-studio/)
- [Azure-Computer Learning-Vorschau](https://studio.azureml.net/)
- [Machine Learning Center](http://azure.microsoft.com/documentation/services/machine-learning/)

## Azure Machine Learning-Webdienst ##

Mit dem Azure Machine Learning (ML)-Webdienst kommunizieren externe Anwendungen in Echtzeit mit einem ML-Workflow-Bewertungsmodell. Ein ML-Webdienstaufruf gibt Ergebnisse an eine externe Anwendung zurück. Um einen ML Webdienstaufruf durchzuführen, übergeben Sie einen API-Schlüssel, der erstellt wird, wenn Sie eine Vorhersage veröffentlichen. Der ML-Webdienst basiert auf REST, einer verbreiteten Architektur für Webprogrammierungsprojekte.

Azure Machine Learning verfügt über zwei Arten von Diensten:

- Request-Response Service (RRS) - ein hochskalierbarer Webdienst mit niedriger Latenz, der eine Schnittstelle für zustandslose Modelle bereitstellt, die in ML Studio erstellt und veröffentlicht wurden.
- Batch Execution Service (BES) - ein asynchroner Dienst für die Bewertung eines Stapels für Datensätze.

Weitere Informationen zu Azure Machine Learning-Webdiensten finden Sie unter [Übersicht über den Azure ML-Prozess](machine-learning-overview-of-azure-ml-process.md).

## Abrufen eines Autorisierungsschlüssels für Azure Machine Learning ##
Sie erhalten einen Webdienst-API-Schlüssel von einem ML-Webdienst. Sie können ihn über Microsoft Azure Machine Learning Studio oder über das Azure-Verwaltungsportal abrufen.
### Microsoft Azure Machine Learning Studio ###
1. Klicken Sie in Microsoft Azure Machine Learning Studio links auf **WEB SERVICES**.
2. Klicken Sie auf einen Webdienst. "API KEY" befindet sich auf der Registerkarte **DASHBOARD**.

### Azure-Verwaltungsportal ###

1. Klicken Sie links auf **MACHINE LEARNING**.
2. Klicken Sie auf einen Arbeitsbereich.
3. Klicken Sie auf **WEB SERVICES**.
4. Klicken Sie auf einen Webdienst.
5. Klicken Sie auf einen Endpunkt. "API KEY" befindet sich unten rechts.

## <a id="connect"></a>Herstellen einer Verbindung mit einem Azure Machine Learning-Webdienst

Sie können mit jeder Programmiersprache, die HTTP-Anforderungen und -Antworten unterstützt, eine Verbindung zu einem Azure Machine Learning-Webdienst herstellen. Sie können Beispiele in C#, Python und R auf einer für den Azure ML-Webdienst-Hilfeseite anzeigen.

### So zeigen Sie Azure ML Webdienst-API-Hilfeseiten an ###
Wenn Sie einen Webdienst veröffentlichen, wird eine Azure ML-API-Hilfeseite erstellt. Weitere Informationen finden Sie unter [Azure Machine Learning - exemplarische Vorgehensweise - Veröffentlichen eines Webdiensts](machine-learning-walkthrough-5-publish-web-service.md).


**So zeigen Sie eine Azure ML-API-Hilfeseite an**
In Microsoft Azure Machine Learning Studio:

1. Wählen Sie **WEB SERVICES** aus.
2. Wählen Sie einen Webdienst aus.
3. Wählen Sie **API help page** - **REQUEST/RESPONSE** oder **BATCH EXECUTION** aus.


**Azure ML-API-Hilfeseite**
Die Die Azure ML-API-Hilfeseite enthält Details zu einem Vorhersage-Webdienst, darunter:


<table>
	<tr>
		<td>&nbsp;</td>
		<td>Beispiel </td>
	</tr>
	<tr>
		<td>Request POST URI </td>

		<td>https://ussouthcentral.services.azureml.net/workspaces/{WorkspaceId}/services/{ServiceId}/score
		</td>
	</tr>
	<tr>
		<td>Beispiel für eine Anforderung </td>
		<td>{ <br/> 
			&nbsp;&nbsp; "Id": "score00001",   <br/>
			&nbsp;&nbsp; "Instance": <br/>
			&nbsp;&nbsp;&nbsp;&nbsp; {  <br/>  
 			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp; "FeatureVector": { <br/>
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  "Col1": "0", <br/>      
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  "Col2": "0", <br/>      
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  "Col3": "0", <br/>  
			&nbsp;&nbsp;&nbsp;&nbsp; &nbsp;&nbsp;  ...     },   <br/>
			&nbsp;&nbsp;&nbsp;&nbsp;   "GlobalParameters": {}   <br/>
			&nbsp;&nbsp;&nbsp;&nbsp; } <br/>
		}</td>
	</tr>
	<tr>
		<td>Antworttext </td>
		<td>
		<table style="width: 100%">

			<tr>
				<td><B>Name</B></td>
				<td><B>Data Type</B></td>
			</tr>
	
			<tr>
				<td>Funktion</td>
				<td>String</td>
			</tr>
			<tr>
				<td>Count</td>
				<td>Numeric</td>
			</tr>
			<tr>
				<td>Unique Value Count </td>
				<td>Numeric </td>
			</tr>
			<tr>
				<td>... </td>
				<td>... </td>
			</tr>
		</table>
		</td>
	</tr>
	<tr>
		<td>Beispiel für eine Antwort </td>
		<td>[&quot;Col1&quot;,&quot;1&quot;,&quot;1&quot;,...] </td>
	</tr>
	<tr>
		<td>Beispielcode: </td>
		<td>(Beispielcode in C#, Python und R) </td>
	</tr>
</table>

**HINWEIS:** Die Beispiele entstammen Beispiel 1: "Download dataset from UCI: Adult 2 class dataset", einem Teil der Azure ML-Beispielsammlung.

### C#-Beispiel ###

Verwenden Sie zum Verbinden mit einem Azure ML-Webdienst einen **HttpClient** durch Übergeben von ScoreData. ScoreData enthält einen FeatureVector, einen n-dimensionalen  Vektor von numerischen Funktionen, der ScoreData darstellt. Sie authentifizieren sich mit einem API-Schlüssel beim Azure ML-Dienst.

Für die Verbindung zu einem ML-Webdienst muss das Nuget-Paket **Microsoft.AspNet.WebApi.Client** installiert sein.

**Installieren von Microsoft.AspNet.WebApi.Client Nuget in Visual Studio**

1. Veröffentlichen Sie den "Download dataset from UCI: Adult 2 class dataset"-Webdienst.
2. Klicken Sie auf **Extras** > **NuGet-Paket-Manager** > **Paket-Manager-Konsole**.
2. Wählen Sie **Install-Package Microsoft.AspNet.WebApi.Client** aus.

**So führen Sie das Codebeispiel aus**

1. Veröffentlichen Sie Beispiel 1: "Download dataset from UCI: Adult 2 class dataset"-Experiment, einen Teil der Azure ML-Beispielsammlung.
2. Weisen Sie "apiKey" den Schlüssel von einem Webdienst zu. Weitere Informationen finden Sie unter "Abrufen eines Autorisierungsschlüssels für Azure ML".
3. Weisen Sie "serviceUri" die Anforderungs-URI zu. Weitere Informationen finden Sie unter "Abrufen einer Anforderungs-URI".

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
	    public class ScoreData
	    {
	        public Dictionary<string, string> FeatureVector { get; set; }
	        public Dictionary<string, string> GlobalParameters { get; set; }
	    }
	
	    public class ScoreRequest
	    {
	        public string Id { get; set; }
	        public ScoreData Instance { get; set; }
	    }
	
	    class Program
	    {
	        static void Main(string[] args)
	        {
	            InvokeRequestResponseService().Wait();
	
	            Console.ReadLine();
	        }
	
	        static async Task InvokeRequestResponseService()
	        {
	            //Assign apiKey with the key from a web service.
	            const string apiKey = "{ApiKey}";
	
	            //Assign serviceUri with the Request URI. See How to get a Request URI.
	            const string serviceUri = "{ServiceUri}";
	            
	            using (var client = new HttpClient())
	            {
	                ScoreData scoreData = new ScoreData()
	                {
	                    //Input data
	                    FeatureVector = new Dictionary<string, string>() 
	                    {
	                        { "Col1", "0" },
	                        { "Col2", "0" },
	                        { "Col3", "0" },
	                        { "Col4", "0" },
	                        { "Col5", "0" },
	                        { "Col6", "0" },
	                        { "Col7", "0" },
	                        { "Col8", "0" },
	                        { "Col9", "0" },
	                        { "Col10", "0" },
	                        { "Col11", "0" },
	                        { "Col12", "0" },
	                        { "Col13", "0" },
	                        { "Col14", "0" },
	                        { "Col15", "0" },
	                    },
	                    GlobalParameters = 
	                        new Dictionary<string, string>() {}
	                };
	
	                ScoreRequest scoreRequest = new ScoreRequest()
	                {
	                    Id = "score00001",
	                    Instance = scoreData
	                };
	
	                //Set authorization header
	                client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue( "Bearer", apiKey);
	             
	                client.BaseAddress = new Uri(serviceUrl);
	
	                //Post HTTP response message
	                HttpResponseMessage response = await client.PostAsJsonAsync("", scoreRequest);
	
	                if (response.IsSuccessStatusCode)
	                {
	                    //Read result string
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


### Python-Beispiel ###

Verwenden Sie zum Verbinden mit einem Azure ML-Webdienst die **urllib2**-Bibliothek, durch Übergeben von ScoreData. ScoreData enthält einen FeatureVector, einen n-dimensionalen  Vektor von numerischen Funktionen, der ScoreData darstellt. Sie authentifizieren sich mit einem API-Schlüssel beim Azure ML-Dienst.


**So führen Sie das Codebeispiel aus**

1. Veröffentlichen Sie Beispiel 1: "Download dataset from UCI: Adult 2 class dataset"-Experiment, einen Teil der Azure ML-Beispielsammlung.
2. Weisen Sie "apiKey" den Schlüssel von einem Webdienst zu. Weitere Informationen finden Sie unter "Abrufen eines Autorisierungsschlüssels für Azure ML".
3. Weisen Sie "serviceUri" die Anforderungs-URI zu. Weitere Informationen finden Sie unter "Abrufen einer Anforderungs-URI".

		import urllib2
		# If you are using Python 3+, import urllib instead of urllib2
	
		import json 
	
		data =  {
	            "Id": "score00001",
	            "Instance": {
	                "FeatureVector": {
	                    "Col1": "0",
	                    "Col2": "0",
	                    "Col3": "0",
	                    "Col4": "0",
	                    "Col5": "0",
	                    "Col6": "0",
	                    "Col7": "0",
	                    "Col8": "0",
	                    "Col9": "0",
	                    "Col10": "0",
	                    "Col11": "0",
	                    "Col12": "0",
	                    "Col13": "0",
	                    "Col14": "0",
	                    "Col15": "0",
	                },
	                "GlobalParameters": { }
	            }
	        }
	
		body = str.encode(json.dumps(data))
	
		#Assign serviceUrl with the Request URI. See How to get a Request URI.
		uri = '{ServiceUri}'
	
		#Assign apiKey with the key from a web service.
		api_key = '{ApiKey}'
		headers = {'Content-Type':'application/json', 'Authorization':('Bearer '+ api_key)}
	
		req = urllib2.Request(uri, body, headers) 
		response = urllib2.urlopen(req)
	
		#If you are using Python 3+, replace urllib2 with urllib.request in the above code:
		#req = urllib.request.Request(uri, body, headers) 
		#response = urllib.request.urlopen(req)
	
		result = response.read()
		print(result) 

<!--HONumber=49-->