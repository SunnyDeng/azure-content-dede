<properties 
	pageTitle="Erweiterte Codierung mit dem Media Encoder Premium Workflow" 
	description="Erfahren Sie, wie Sie eine Codierung mit dem Media Encoder Premium Workflow durchführen. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK für .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/03/2015" 
	ms.author="juliako"/>

#Erweiterte Codierung mit dem Media Encoder Premium Workflow (öffentliche Vorschau)

**Hinweis**Der in diesem Thema beschriebene Media Encoder Premium Workflow-Medienprozessor ist in China nicht verfügbar.

##Übersicht

Microsoft Azure Media Services stellt eine öffentliche Vorschau des **Media Encoder Premium Workflow**-Medienprozessors vor. Dieser Prozessor bietet leistungsstarke Codierungsfunktionen für Ihre bedarfsgesteuerten Premium-Workflows. 

Die folgenden Themen stellen nähere Informationen zum **Media Encoder Premium-Workflow** bereit: 

- [Von Media Encoder Premium Workflow unterstützte Formate](media-services-premium-workflow-encoder-formats.md) - Erläutert die von **Media Encoder Premium Workflow** unterstützten Dateiformate und Codecs.

- Im Abschnitt [Encoder-Vergleich](media-services-encode-asset.md#compare_encoders) werden die Codierungsfunktionen von**Media Encoder Premium Workflow** und **Azure Media Encoder** verglichen.

Dieses Thema zeigt die Codierung mit **Media Encoder Premium Workflow** unter Verwendung von .NET.

##Codieren

Das Codieren von Aufgaben für den **Media Encoder Premium Workflow** erfordert eine separate Konfigurationsdatei, die sogenannte Workflowdatei. Diese Dateien haben die Erweiterung .workflow und werden mit dem [Workflow-Designer](media-services-workflow-designer.md) erstellt.

Sie können die standardmäßigen Workflowdateien [hier](https://github.com/Azure/azure-media-services-samples/tree/master/Encoding%20Presets/VoD/MediaEncoderPremiumWorkfows) abrufen. Der Ordner enthält auch Beschreibungen dieser Dateien.

Die Workflowdateien müssen als Medienobjekt in Ihr Media Services-Konto hochgeladen und das Medienobjekt muss an die Codierungsaufgabe übergeben werden.

Das folgende Beispiel zeigt, wie Sie mit dem **Media Encoder Premium Workflow** eine Codierung durchführen. 

Es werden folgende Schritte ausgeführt: 
 
1. Erstellen eines Medienobjekts und Hochladen einer Workflowdatei. 
2. Erstellen eines Medienobjekts und Hochladen einer Quellmediendatei.
3. Abrufen des Medienprozessors "Media Encoder Premium Workflow".
4. Erstellen eines Auftrags und einer Aufgabe.
5. Hinzufügen von zwei Medienobjekten zur Aufgabe.
	
	a. 1. - das Workflow-Medienobjekt

	b. 2. - das Videomedienobjekt
	
	**Hinweis**: Das Medienobjekt für den Workflow muss der Aufgabe vor dem Videomedienobjekt hinzugefügt werden. 
Die Konfigurationszeichenfolge für diese Aufgabe sollte leer sein. 

6. Übermitteln des Codierungsauftrags.

Im folgenden finden Sie ein vollständiges Beispiel. Informationen zur Einrichtung von Media Services für die .NET-Entwicklung finden Sie unter [Media Services-Entwicklung mit .NET](media-services-dotnet-how-to-use.md).


 	using System; 
	using System.Linq;
	using System.Configuration;
	using System.IO;
	using System.Text;
	using System.Threading;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	using Microsoft.WindowsAzure;
	using Microsoft.WindowsAzure.MediaServices.Client;
	
	namespace MediaEncoderPremiumWorkflowSample
	{
	    class Program
	    {
	        // Read values from the App.config file.
	        private static readonly string _mediaServicesAccountName =
	            ConfigurationManager.AppSettings["MediaServicesAccountName"];
	        private static readonly string _mediaServicesAccountKey =
	            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	
	        // Field for service context.
	        private static CloudMediaContext _context = null;
	        private static MediaServicesCredentials _cachedCredentials = null;
	
	        private static readonly string _supportFiles =
	            Path.GetFullPath(@"../..\Media");
	
	        private static readonly string _workflowFilePath =
	            Path.GetFullPath(_supportFiles + @"\H264 Progressive Download MP4.workflow");
	        
	        private static readonly string _singleMP4InputFilePath =
	            Path.GetFullPath(_supportFiles + @"\BigBuckBunny.mp4");
	
	
	        static void Main(string[] args)
	        {
	            // Create and cache the Media Services credentials in a static class variable.
	            _cachedCredentials = new MediaServicesCredentials(
	                            _mediaServicesAccountName,
	                            _mediaServicesAccountKey);
	
	            // Used the cached credentials to create CloudMediaContext.
	            _context = new CloudMediaContext(_cachedCredentials);
	
	            var workflowAsset = CreateAssetAndUploadSingleFile(_workflowFilePath);
	            var videoAsset = CreateAssetAndUploadSingleFile(_singleMP4InputFilePath);
	            IAsset outputAsset = CreateEncodingJob(workflowAsset, videoAsset); 
	
	        }
	
	        static public IAsset CreateAssetAndUploadSingleFile(string singleFilePath)
	        {
	            var assetName = "UploadSingleFile_" + DateTime.UtcNow.ToString();
	            var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
	
	            var fileName = Path.GetFileName(singleFilePath);
	
	            var assetFile = asset.AssetFiles.Create(fileName);
	
	            Console.WriteLine("Created assetFile {0}", assetFile.Name);
	
	            var accessPolicy = _context.AccessPolicies.Create(assetName, TimeSpan.FromDays(30),
	                                                                AccessPermissions.Write | AccessPermissions.List);
	
	            var locator = _context.Locators.CreateLocator(LocatorType.Sas, asset, accessPolicy);
	
	            Console.WriteLine("Upload {0}", assetFile.Name);
	
	            assetFile.Upload(singleFilePath);
	            Console.WriteLine("Done uploading {0}", assetFile.Name);
	
	            locator.Delete();
	            accessPolicy.Delete();
	
	            return asset;
	        }
	
	        static public IAsset CreateEncodingJob(IAsset workflow, IAsset video)
	        {
	            // Declare a new job.
	            IJob job = _context.Jobs.Create("Premium Workflow encoding job");
	            // Rufen Sie einen Verweis auf den Medienprozessor ab, und übergeben Sie ihm den Namen des 
	            // Prozessors für die spezielle Aufgabe.
	            IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Premium Workflow");
	
	            // Erstellen Sie eine Aufgabe mit den Codierungsdetails unter Verwendung einer Voreinstellung.
	            ITask task = job.Tasks.AddNew("Premium Workflow encoding task",
	                processor,
	                "",
	                TaskOptions.None);
	
	            // Geben Sie das zu codierende Eingabemedienobjekt an.
	            task.InputAssets.Add(workflow);
	            task.InputAssets.Add(video); // Fügen Sie ein Medienobjekt hinzu.
	            // Fügen Sie ein Ausgabemedienobjekt hinzu, das die Ergebnisse des Auftrags enthält. 
	            // Diese Ausgabe wird als AssetCreationOptions.None, angegeben. Das 
	            // bedeutet, dass das Ausgabemedienobjekt nicht verschlüsselt ist. 
	            task.OutputAssets.AddNew("Output asset",
	                AssetCreationOptions.None);
	
	            // Verwenden Sie den folgenden Ereignishandler zum Überprüfen des Auftragsfortschritts.  
	            job.StateChanged += new
	                    EventHandler<JobStateChangedEventArgs>(StateChanged);
	
	            // Starten Sie den Auftrag.
	            job.Submit();
	
	            // Überprüfen Sie die Auftragsausführung, und warten Sie die Beendigung des Auftrags ab. 
	            Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
	            progressJobTask.Wait();
	
	            // Wenn der Auftragsstatus einem Fehler entspricht, sollte die Event Handling- 
	            // Methode zum Auftragsfortschritt etwaige Fehler protokollieren.  Hier erfolgt eine Überprüfung 
	            // auf einen Fehlerstatus. Bei Bedarf wird die Anwendung beendet.
	            if (job.State == JobState.Error)
	            {
	                throw new Exception("\nExiting method due to job error.");
	            }
	
	            return job.OutputMediaAssets[0];
	        }
	
	        static private void StateChanged(object sender, JobStateChangedEventArgs e)
	        {
	            Console.WriteLine("Job state changed event:");
	            Console.WriteLine("  Previous state: " + e.PreviousState);
	            Console.WriteLine("  Current state: " + e.CurrentState);
	
	            switch (e.CurrentState)
	            {
	                case JobState.Finished:
	                    Console.WriteLine();
	                    Console.WriteLine("Job is finished.");
	                    Console.WriteLine();
	                    break;
	                case JobState.Canceling:
	                case JobState.Queued:
	                case JobState.Scheduled:
	                case JobState.Processing:
	                    Console.WriteLine("Please wait...\n");
	                    break;
	                case JobState.Canceled:
	                case JobState.Error:
	                    // Cast sender as a job.
	                    IJob job = (IJob)sender;
	                    // Display or log error details as needed.
	                    LogJobStop(job.Id);
	                    break;
	                default:
	                    break;
	            }
	        }
	
	        static private void LogJobStop(string jobId)
	        {
	            StringBuilder builder = new StringBuilder();
	            IJob job = _context.Jobs.Where(j => j.Id == jobId).FirstOrDefault();
	
	            builder.AppendLine("\nThe job stopped due to cancellation or an error.");
	            builder.AppendLine("***************************");
	            builder.AppendLine("Job ID: " + job.Id);
	            builder.AppendLine("Job Name: " + job.Name);
	            builder.AppendLine("Job State: " + job.State.ToString());
	            builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
	            builder.AppendLine("Media Services account name: " + _mediaServicesAccountName);
	            // Log job errors if they exist.  
	            if (job.State == JobState.Error)
	            {
	                builder.Append("Error Details: \n");
	                foreach (ITask task in job.Tasks)
	                {
	                    foreach (ErrorDetail detail in task.ErrorDetails)
	                    {
	                        builder.AppendLine("  Task Id: " + task.Id);
	                        builder.AppendLine("    Error Code: " + detail.Code);
	                        builder.AppendLine("    Error Message: " + detail.Message + "\n");
	                    }
	                }
	            }
	            builder.AppendLine("***************************\n");
	
	            Console.Write(builder.ToString());
	        }
	
	
	        static private IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
	        {
	            var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
	                ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();
	
	            if (processor == null)
	                throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));
	
	            return processor;
	        }
	    }
	}


##Bekannte Probleme

Wenn Ihr Eingabevideo keine Untertitel enthält, enthält das Ausgabemedienobjekt trotzdem eine leere TTML-Datei.


<!--HONumber=52--> 