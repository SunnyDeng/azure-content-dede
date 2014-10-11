<properties linkid="develop-media-services-how-to-guides-encode-an-asset" urlDisplayName="How to Encode an Asset" pageTitle="How to Encode an Asset for Media Services - Azure" metaKeywords="" description="Learn how to use the Azure Media Encoder to encode media content on Media Services. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Encode an Asset" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# Gewusst wie: Codieren von Medienobjekten

Dieser Artikel ist Teil einer Reihe zum Thema Programmierung von Azure-Mediendiensten. Das vorherige Thema war [Vorgehensweise: Abrufen eines Medienprozessors][].

Sie können Medieninhalte auf dem Server mit dem Azure Media Encoder in verschiedenen Medien-Codierungen und Formaten codieren. Außerdem können Sie Codierer von Mediendienste-Partnern verwenden. Externe Codierer finden Sie im [Azure Marketplace][]. Sie können die Details von Codierungsaufgaben entweder mit [Voreinstellungs][]-Zeichenfolgen oder über Konfigurationsdateien angeben.

## MP4-Codierung

Die folgende Methode lädt ein einzelnes Medienobjekt hoch und erstellt einen Auftrag zur Codierung des Medienobjekts mit MP4 unter Verwendung der Voreinstellung "H264 Broadband 720p". Das Ergebnis ist eine einzige MP4-Datei mit H264-Codierung und einer Auflösung von 720p:

     static IJob CreateEncodingJob(string inputMediaFilePath, string outputFolder) { //Create an encrypted asset and upload to storage. IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, inputMediaFilePath);
        // Declare a new job.

        IJob job = _context.Jobs.Create("My encoding job");

        // Get a reference to the Azure Media Encoder
        IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Encoder");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My encoding task",
            processor,
            "H264 Broadband 720p",
            _protectedConfig);

        // Specify the input asset to be encoded.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job. 
        // This output is specified as AssetCreationOptions.None, which 
        // means the output asset is in the clear (unencrypted). 
        task.OutputAssets.AddNew("Output asset", AssetCreationOptions.None);

        // Use the following event handler to check job progress.  
        job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged);

        // Launch the job.
        job.Submit();

        // Optionally log job details. This displays basic job details
        // to the console and saves them to a JobDetails-JobId.txt file 
        // in your output folder.
        LogJobDetails(job.Id);

        // Check job execution and wait for job to finish. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        // If job state is Error, the event handling 
        // method for job progress should log errors.  Here we check 
        // for error state and exit if needed.
        if (job.State == JobState.Error)
        {
            Console.WriteLine("\nExiting method due to job error.");
            return job;
        }

        // Perform other tasks. For example, access the assets that are the output of a job, 
        // either by creating URLs to the asset on the server, or by downloading. 
        return job;
    }

    private static void StateChanged(object sender, JobStateChangedEventArgs e)
    {
        Console.WriteLine("Job state changed event:");
        Console.WriteLine("  Previous state: " + e.PreviousState);
        Console.WriteLine("  Current state: " + e.CurrentState);
        switch (e.CurrentState)
        {
            case JobState.Finished:
            Console.WriteLine();
            Console.WriteLine("Job is finished. Please wait while local tasks or downloads complete...");
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

<p>
</code>

</pre>

<h2> Codierung für Smooth Streaming</h2>

Sie haben zwei Optionen, um Videos für Smooth Streaming zu codieren:
<ul>
<li>   Direkte Codierung für Smooth Streaming</li>
<li>  Codierung in MP4 und anschließend codieren für Smooth Streaming</li>
</ul>
</p>
Direkte Codierung für Smooth Streaming mit dem obigen Code, jedoch mithilfe einer der Smooth Streaming Codierungs-Voreinstellungen. Eine vollständige Liste der Codierungs-Voreinstellungen finden Sie unter [Zeichenfolgen für vordefinierte Einstellungen für Aufgaben für Azure Media Encoder][].

Verwenden Sie den Azure Media Packager, um eine MP4-Datei für Smooth Streaming zu konvertieren. Der Azure Media Packager unterstützt keine Voreinstellungen, und Sie müssen die Konfigurationsoptionen im XML angeben. Sie finden das entsprechende XML zum Konvertieren von MP4 für Smooth Streaming unter [Voreinstellungen für Aufgaben für Azure Media Packager][]. Fügen Sie das XML in eine Datei mit dem Namen MediaPackager\_MP4ToSmooth.xml in Ihrem Projekt ein. Der folgende Code demonstriert die Konvertierung eines MP4-Medienobjekts für Smooth Streaming. Die folgende Methode konvertiert ein vorhandenes Medienobjekt.
<pre><code>
private static IJob ConvertMP4toSmooth(IAsset assetToConvert, string configFilePath)
 {
	// Declare a new job to contain the tasks
    IJob job = _context.Jobs.Create("Convert to Smooth Streaming job");
    // Set up the first Task to convert from MP4 to Smooth Streaming. 
    // Read in task configuration XML
    string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));
    // Get a media packager reference
    IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Packager");
    // Create a task with the conversion details, using the configuration data
    ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
           processor,
           configMp4ToSmooth,
           TaskOptions.None);
    // Specify the input asset to be converted.
    task.InputAssets.Add(assetToConvert);
    // Add an output asset to contain the results of the job.
    task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
    // Use the following event handler to check job progress. 
	// The StateChange method is the same as the one in the previous sample
    job.StateChanged += new EventHandler&ltJobStateChangedEventArgs&gt(StateChanged);
    // Launch the job.
    job.Submit();
    // Check job execution and wait for job to finish. 
    Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
    progressJobTask.Wait();
    // Get a refreshed job reference after waiting on a thread.
    job = GetJob(job.Id);
    // Check for errors
    if (job.State == JobState.Error)
    {
        Console.WriteLine("\nExiting method due to job error.");
    }
    return job;
}
</code></pre>
Weitere Informationen zur Verarbeitung von Medienobjekten finden Sie unter:

-   [Verarbeiten von Medienobjekten mit dem Mediendienste-SDK für .NET][]
-   [Verarbeiten von Medienobjekten mit der Mediendienste-REST-API][]

</p>
## Nächste Schritte

Sie sind nun in der Lage, einen Auftrag zur Codierung von Medienobjekten zu erstellen und können mit dem Thema [Prüfen des Auftragsfortschritts mit Media Services][] fortfahren.

  [Vorgehensweise: Abrufen eines Medienprozessors]: http://go.microsoft.com/fwlink/?LinkID=301732&ampclcid=0x409
  [Azure Marketplace]: https://datamarket.azure.com/
  [Voreinstellungs]: http://msdn.microsoft.com/en-us/library/hh973610.aspx
  [Zeichenfolgen für vordefinierte Einstellungen für Aufgaben für Azure Media Encoder]: http://msdn.microsoft.com/en-us/library/jj129582.aspx
  [Voreinstellungen für Aufgaben für Azure Media Packager]: http://msdn.microsoft.com/en-us/library/windowsazure/hh973635.aspx
  [Verarbeiten von Medienobjekten mit dem Mediendienste-SDK für .NET]: http://msdn.microsoft.com/en-us/library/jj129580.aspx
  [Verarbeiten von Medienobjekten mit der Mediendienste-REST-API]: http://msdn.microsoft.com/en-us/library/jj129574.aspx
  [Prüfen des Auftragsfortschritts mit Media Services]: http://go.microsoft.com/fwlink/?LinkID=301737&ampclcid=0x409
