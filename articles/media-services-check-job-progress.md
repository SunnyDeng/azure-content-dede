<properties 
	pageTitle="Gewusst wie: Prüfen des Auftragsfortschritts mit .NET" 
	description="Erfahren Sie, wie Sie den Fortschritt von Aufträgen mit Ereignishandlern abrufen und Statusupdates senden können. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK für .NET." 
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
	ms.date="02/10/2015" 
	ms.author="juliako"/>

#Gewusst wie: Prüfen des Auftragsfortschritts

Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](media-services-video-on-demand-workflow.md) . 

Beim Ausführen von Aufträgen ist es nützlich, deren Fortschritt verfolgen zu können. Sie können den Fortschritt überprüfen, indem Sie [einen "StateChanged"-Ereignishandler definieren](#statechange_event_handler) oder indem Sie mit [Azure-Warteschlangenspeicher die Media Services-Auftragsbenachrichtigungen überwachen](#check_progress_with_queues). Beide Methoden werden in diesem Thema beschrieben. 

##<a id="statechange_event_handler"></a>Definieren eines StateChanged-Ereignishandlers zum Überwachen des Auftragsfortschritts

Das folgende Codebeispiel definiert den StateChanged-Ereignishandler. Dieser Ereignishandler verfolgt den Auftragsfortschritt und liefert Statusupdates je nach Auftragsfortschritt. Der Code definiert außerdem die LogJobStop-Methode. Diese Helfermethode protokolliert Fehlerdetails.

	private static void StateChanged(object sender, JobStateChangedEventArgs e)
	{
	    Console.WriteLine("Job state changed event:");
	    Console.WriteLine("  Previous state: " + e.PreviousState);
	    Console.WriteLine("  Current state: " + e.CurrentState);
	
	    switch (e.CurrentState)
	    {
	        case JobState.Finished:
	            Console.WriteLine();
	            Console.WriteLine("********************");
	            Console.WriteLine("Job is finished.");
	            Console.WriteLine("Please wait while local tasks or downloads complete...");
	            Console.WriteLine("********************");
	            Console.WriteLine();
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
	
	private static void LogJobStop(string jobId)
	{
	    StringBuilder builder = new StringBuilder();
	    IJob job = GetJob(jobId);
	
	    builder.AppendLine("\nThe job stopped due to cancellation or an error.");
	    builder.AppendLine("***************************");
	    builder.AppendLine("Job ID: " + job.Id);
	    builder.AppendLine("Job Name: " + job.Name);
	    builder.AppendLine("Job State: " + job.State.ToString());
	    builder.AppendLine("Job started (server UTC time): " + job.StartTime.ToString());
	    builder.AppendLine("Media Services account name: " + _accountName);
	    builder.AppendLine("Media Services account location: " + _accountLocation);
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
	    // Write the output to a local file and to the console. The template 
	    // for an error output file is:  JobStop-{JobId}.txt
	    string outputFile = _outputFilesFolder + @"\JobStop-" + JobIdAsFileName(job.Id) + ".txt";
	    WriteToFile(outputFile, builder.ToString());
	    Console.Write(builder.ToString());
	}
	
	private static string JobIdAsFileName(string jobID)
	{
	    return jobID.Replace(":", "_");
	}



##<a id="check_progress_with_queues"></a>Verwenden von Azure-Warteschlangenspeicher zum Überwachen von Media Services-Auftragsbenachrichtigungen

Microsoft Azure Media Services bietet die Möglichkeit, Benachrichtigungen an den [Azure-Warteschlangenspeicher](storage-dotnet-how-to-use-queues.md#what-is) zu übermitteln, während Medienaufträge verarbeitet werden. In diesem Thema wird gezeigt, wie Sie diese Benachrichtigungen aus dem Warteschlangenspeicher abrufen.

Auf die an den Warteschlangenspeicher übermittelten Nachrichten kann von überall auf der Welt aus zugegriffen werden. Die Benachrichtigungsarchitektur für Azure-Warteschlangen ist zuverlässig und hochgradig skalierbar. Das Abrufen des Warteschlangenspeichers wird gegenüber anderen Methoden empfohlen. 

Häufig werden Media Services-Benachrichtigungen überwacht, wenn Sie ein Content Management System entwickeln, das nach Abschluss der Codierung eines Auftrags einige zusätzliche Aufgaben durchführen soll (z. B. den nächsten Schritt in einem Workflow auslösen oder Inhalte veröffentlichen). 

###Überlegungen

Beachten Sie Folgendes beim Entwickeln von Media Services-Anwendungen, die die Azure-Speicherwarteschlange verwenden.

- Der Warteschlangendienst bietet keine Garantie, dass die Übermittlung nach First-in-First-out (FIFO) sortiert erfolgt. Weitere Informationen finden sie unter [Azure-Warteschlangen und Azure Service Bus-Warteschlangen - Vergleich und Gegenüberstellung](https://msdn.microsoft.com/library/azure/hh767287.aspx).
- Azure-Speicherwarteschlangen sind kein Push-Dienst. Sie müssen die Warteschlange abfragen. 
- Sie können eine beliebige Anzahl von Warteschlangen verwenden. Weitere Informationen finden Sie unter [REST-API des Warteschlangendiensts](https://msdn.microsoft.com/library/azure/dd179363.aspx).
- Azure-Speicherwarteschlangen weisen einige Einschränkungen und Besonderheiten auf, die im folgenden Artikel beschrieben werden: [Azure-Warteschlangen und Azure Service Bus-Warteschlangen - Vergleich und Gegenüberstellung](https://msdn.microsoft.com/library/azure/hh767287.aspx).

###Codebeispiel

Das Codebeispiel in diesem Abschnitt erfüllt die folgenden Aufgaben:

1. Definiert die **EncodingJobMessage**-Klasse, die das Format der Benachrichtigung zuordnet. Der Code deserialisiert Nachrichten aus der Warteschlange in Objekte vom Typ **EncodingJobMessage**.
1. Lädt die Informationen zu Media Services und Speicherkonto aus der Datei "app.config". Verwendet diese Informationen zum Erstellen des **CloudMediaContext**-Objekts und des **CloudQueue**-Objekts.
1. Erstellt die Warteschlange, die Benachrichtigungen zum Codierungsauftrag empfangen soll.
1. Erstellt den Endpunkt für Benachrichtigungen, der der Warteschlange zugeordnet ist.
1. Fügt den Endpunkt für die Benachrichtigung an den Auftrag an und übermittelt den Codierungsauftrag. Sie können mehrere Benachrichtigungsendpunkte an einen Auftrag anfügen.
1. In diesem Beispiel interessieren uns nur die Endphasen der Auftragsverarbeitung, deshalb übergeben wir **NotificationJobState.FinalStatesOnly** an die **AddNew**-Methode. 
		
		job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, _notificationEndPoint);
1. Wenn Sie NotificationJobState.All übergeben, sollten Sie damit alle Benachrichtigungen über Statusänderungen empfangen: In der Warteschlange -> Geplant -> In Verarbeitung -> Abgeschlossen. Wie aber bereits erwähnt, garantiert der Azure Storage-Warteschlangendienst keine geordneten Übermittlung. Sie können die "Timestamp"-Eigenschaft (im Beispiel unten im Typ "EncodingJobMessage" definiert) zum Ordnen der Nachrichten verwenden. Es ist möglich, dass Sie Benachrichtigungen doppelt erhalten. Mithilfe der "ETag"-Eigenschaft (im Typ "EncodingJobMessage" definiert) können Sie nach Duplikaten suchen. Beachten Sie, dass es auch möglich ist, dass einige Statusänderungsbenachrichtigungen übersprungen werden. 
1. Wartet, bis der Auftrag abgeschlossen ist, indem er die Warteschlange alle zehn Sekunden überprüft. Löscht Nachrichten, nachdem sie verarbeitet wurden.
1. Löscht die Warteschlange und den Benachrichtigungsendpunkt.

>[AZURE.NOTE]Die empfohlene Methode zum Überwachen des Auftragsstatus ist das Überwachen von Benachrichtigungen, wie im folgenden Beispiel gezeigt.
>
>Sie können aber auch den Status eines Auftrags mithilfe der **IJob.State**-Eigenschaft überprüfen.  Beachten Sie, dass eine Benachrichtigung über den Abschluss eines Auftrags eintreffen kann, bevor der Zustand von **IJob** auf **minFreeThreads** gesetzt wurde. Die **IJob.State**-Eigenschaft gibt den genauen Status mit einer geringfügigen Verzögerung an.

	
	using System;
	using System.Linq;
	using System.Configuration;
	using System.IO;
	using System.Text;
	using System.Threading;
	using System.Threading.Tasks;
	using System.Collections.Generic;
	using Microsoft.WindowsAzure.MediaServices.Client;
	using System.Web;
	using Microsoft.WindowsAzure.Storage;
	using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Queue;
	using System.Runtime.Serialization.Json;
	
	namespace JobNotification
	{
	    public class EncodingJobMessage
	    {
	        // MessageVersion is used for version control. 
	        public String MessageVersion { get; set; }
	    
	        // Type of the event. Valid values are 
	        // JobStateChange and NotificationEndpointRegistration.
	        public String EventType { get; set; }
	    
	        // ETag is used to help the customer detect if 
	        // the message is a duplicate of another message previously sent.
	        public String ETag { get; set; }
	    
	        // Time of occurrence of the event.
	        public String TimeStamp { get; set; }
	
	        // Collection of values specific to the event.
	
	        // For the JobStateChange event the values are:
	        //     JobId - Id of the Job that triggered the notification.
	        //     NewState- The new state of the Job. Valid values are:
	        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
	        //     OldState- The old state of the Job. Valid values are:
	        //          Scheduled, Processing, Canceling, Cancelled, Error, Finished
	
	        // For the NotificationEndpointRegistration event the values are:
	        //     NotificationEndpointId- Id of the NotificationEndpoint 
	        //          that triggered the notification.
	        //     State- The state of the Endpoint. 
	        //          Valid values are: Registered and Unregistered.
	
	        public IDictionary<string, object> Properties { get; set; }
	    }
	
	    class Program
	    {
	        private static CloudMediaContext _context = null;
	        private static CloudQueue _queue = null;
	        private static INotificationEndPoint _notificationEndPoint = null;
	
	        private static readonly string _singleInputMp4Path =
	            Path.GetFullPath(@"C:\supportFiles\multifile\BigBuckBunny.mp4");
	
	        static void Main(string[] args)
	        {
	            // Get the values from app.config file.
	            string mediaServicesAccountName = ConfigurationManager.AppSettings["MediaServicesAccountName"];
	            string mediaServicesAccountKey = ConfigurationManager.AppSettings["MediaServicesAccountKey"];
	            string storageConnectionString = ConfigurationManager.AppSettings["StorageConnectionString"];
	
	
	            string endPointAddress = Guid.NewGuid().ToString();
	
	            // Create the context. 
	            _context = new CloudMediaContext(mediaServicesAccountName, mediaServicesAccountKey);
	
	            // Create the queue that will be receiving the notification messages.
	            _queue = CreateQueue(storageConnectionString, endPointAddress);
	
	            // Create the notification point that is mapped to the queue.
	            _notificationEndPoint = 
	                    _context.NotificationEndPoints.Create(
	                    Guid.NewGuid().ToString(), NotificationEndPointType.AzureQueue, endPointAddress);
	
	
	            if (_notificationEndPoint != null)
	            {
	                IJob job = SubmitEncodingJobWithNotificationEndPoint(_singleInputMp4Path);
	                WaitForJobToReachedFinishedState(job.Id);
	            }
	
	            // Clean up.
	            _queue.Delete();      
	            _notificationEndPoint.Delete();
	       }
	
	
	        static public CloudQueue CreateQueue(string storageAccountConnectionString, string endPointAddress)
	        {
	            CloudStorageAccount storageAccount = CloudStorageAccount.Parse(storageAccountConnectionString);
	
	            // Create the queue client
	            CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
	
	            // Retrieve a reference to a queue
	            CloudQueue queue = queueClient.GetQueueReference(endPointAddress);
	
	            // Create the queue if it doesn't already exist
	            queue.CreateIfNotExists();
	
	            return queue;
	        }
	 
	
	        // Upload a video file, and encode to Smooth Streaming format
	        public static IJob SubmitEncodingJobWithNotificationEndPoint(string inputMediaFilePath)
	        {
	            // Declare a new job.
	            IJob job = _context.Jobs.Create("My MP4 to Smooth Streaming encoding job");
	
	            //Create an encrypted asset and upload the mp4. 
	            IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, 
	                inputMediaFilePath);
	
	            // Get a media processor reference, and pass to it the name of the 
	            // processor to use for the specific task.
	            IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");
	
	            // Create a task with the conversion details, using a configuration file. 
	            ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
	                processor,
	                "H264 Smooth Streaming 720p",
	                Microsoft.WindowsAzure.MediaServices.Client.TaskOptions.None);
	
	            // Specify the input asset to be encoded.
	            task.InputAssets.Add(asset);
	
	            // Add an output asset to contain the results of the job.
	            task.OutputAssets.AddNew("Output asset",
	                AssetCreationOptions.None);
	
	            // Add a notification point to the job. You can add multiple notification points.  
	            job.JobNotificationSubscriptions.AddNew(NotificationJobState.FinalStatesOnly, 
	                _notificationEndPoint);
	
	            job.Submit();
	
	            return job;
	        }
	
	        public static void WaitForJobToReachedFinishedState(string jobId)
	        {
	            int expectedState = (int)JobState.Finished;
	            int timeOutInSeconds = 600;
	
	            bool jobReachedExpectedState = false;
	            DateTime startTime = DateTime.Now;
	            int jobState = -1;
	
	            while (!jobReachedExpectedState)
	            {
	                // Specify how often you want to get messages from the queue.
	                Thread.Sleep(TimeSpan.FromSeconds(10));
	
	                foreach (var message in _queue.GetMessages(10))
	                {
	                    using (Stream stream = new MemoryStream(message.AsBytes))
	                    {
	                        DataContractJsonSerializerSettings settings = new DataContractJsonSerializerSettings();
	                        settings.UseSimpleDictionaryFormat = true;
	                        DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(EncodingJobMessage), settings);
	                        EncodingJobMessage encodingJobMsg = (EncodingJobMessage)ser.ReadObject(stream);
	
	                        Console.WriteLine();
	
	                        // Display the message information.
	                        Console.WriteLine("EventType: {0}", encodingJobMsg.EventType);
	                        Console.WriteLine("MessageVersion: {0}", encodingJobMsg.MessageVersion);
	                        Console.WriteLine("ETag: {0}", encodingJobMsg.ETag);
	                        Console.WriteLine("TimeStamp: {0}", encodingJobMsg.TimeStamp);
	                        foreach (var property in encodingJobMsg.Properties)
	                        {
	                            Console.WriteLine("    {0}: {1}", property.Key, property.Value);
	                        }
	
	                        // We are only interested in messages 
	                        // where EventType is "JobStateChange".
	                        if (encodingJobMsg.EventType == "JobStateChange")
	                        {
	                            string JobId = (String)encodingJobMsg.Properties.Where(j => j.Key == "JobId").FirstOrDefault().Value;
	                            if (JobId == jobId)
	                            {
	                                string oldJobStateStr = (String)encodingJobMsg.Properties.
	                                                            Where(j => j.Key == "OldState").FirstOrDefault().Value;
	                                string newJobStateStr = (String)encodingJobMsg.Properties.
	                                                            Where(j => j.Key == "NewState").FirstOrDefault().Value;
	
	                                JobState oldJobState = (JobState)Enum.Parse(typeof(JobState), oldJobStateStr);
	                                JobState newJobState = (JobState)Enum.Parse(typeof(JobState), newJobStateStr);
	
	                                if (newJobState == (JobState)expectedState)
	                                {
	                                    Console.WriteLine("job with Id: {0} reached expected state: {1}", 
	                                        jobId, newJobState);
	                                    jobReachedExpectedState = true;
	                                    break;
	                                }
	                            }
	                        }
	                    }
	                    // Delete the message after we've read it.
	                    _queue.DeleteMessage(message);
	                }
	
	                // Wait until timeout
	                TimeSpan timeDiff = DateTime.Now - startTime;
	                bool timedOut = (timeDiff.TotalSeconds > timeOutInSeconds);
	                if (timedOut)
	                {
	                    Console.WriteLine(@"Timeout for checking job notification messages, 
	                                        latest found state ='{0}', wait time = {1} secs",
	                        jobState,
	                        timeDiff.TotalSeconds);
	
	                    throw new TimeoutException();
	                }
	            }
	        }
	   
	        static private IAsset CreateAssetAndUploadSingleFile(AssetCreationOptions assetCreationOptions, string singleFilePath)
	        {
	            var asset = _context.Assets.Create("UploadSingleFile_" + DateTime.UtcNow.ToString(), 
	                assetCreationOptions);
	
	            var fileName = Path.GetFileName(singleFilePath);
	
	            var assetFile = asset.AssetFiles.Create(fileName);
	
	            Console.WriteLine("Created assetFile {0}", assetFile.Name);
	            Console.WriteLine("Upload {0}", assetFile.Name);
	
	            assetFile.Upload(singleFilePath);
	            Console.WriteLine("Done uploading of {0}", assetFile.Name);
	
	            return asset;
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

Das Beispiel oben generiert die folgende Ausgabe. Die Werte können variieren.
	
	Created assetFile BigBuckBunny.mp4
	Upload BigBuckBunny.mp4
	Done uploading of BigBuckBunny.mp4
	
	EventType: NotificationEndPointRegistration
	MessageVersion: 1.0
	ETag: e0238957a9b25bdf3351a88e57978d6a81a84527fad03bc23861dbe28ab293f6
	TimeStamp: 2013-05-14T20:22:37
	    NotificationEndPointId: nb:nepid:UUID:d6af9412-2488-45b2-ba1f-6e0ade6dbc27
	    State: Registered
	    Name: dde957b2-006e-41f2-9869-a978870ac620
	    Created: 2013-05-14T20:22:35
	
	EventType: JobStateChange
	MessageVersion: 1.0
	ETag: 4e381f37c2d844bde06ace650310284d6928b1e50101d82d1b56220cfcb6076c
	TimeStamp: 2013-05-14T20:24:40
	    JobId: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54
	    JobName: My MP4 to Smooth Streaming encoding job
	    NewState: Finished
	    OldState: Processing
	    AccountName: westeuropewamsaccount
	job with Id: nb:jid:UUID:526291de-f166-be47-b62a-11ffe6d4be54 reached expected 
	State: Finished
	

<!--HONumber=52-->