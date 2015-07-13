<properties 
	pageTitle="Codieren eines Medienobjekts mit Azure Media Encoder" 
	description="Erfahren Sie, wie Sie Medieninhalte in Media Services mithilfe des Azure Media Encoder codieren können. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK für .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>


#Codieren eines Medienobjekts mit Azure Media Encoder

Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](media-services-video-on-demand-workflow.md).

##Übersicht

Um digitale Videos über das Internet zu übermitteln, müssen Sie die Medien komprimieren. Digitale Videodateien sind sehr umfangreich und möglicherweise zu groß, um sie über das Internet zu übermitteln oder auf den Geräten Ihrer Kunden ordnungsgemäß wiederzugeben. Bei der Codierung werden Video- und Audiodaten komprimiert, damit Ihre Kunden Ihre Medien anzeigen können.

Die Codierung ist einer der häufigsten Verarbeitungsvorgänge in Media Services. Sie erstellen Codierungsaufträge, um Mediendateien von einer Codierung in eine andere zu konvertieren. Zum Codieren können Sie den in Media Services integrierten Media Encoder verwenden. Außerdem können Sie Codierer von Media Services-Partnern verwenden. Externe Codierer finden Sie im Azure Marketplace. Sie können Details zu Codierungsaufgaben angeben, indem Sie für Ihren Encoder vordefinierte Zeichenfolgen oder vordefinierte Konfigurationsdateien verwenden. Welche Arten von Voreinstellungen verfügbar sind, erfahren Sie unter [Aufgabenvoreinstellungen für Azure Media Services](https://msdn.microsoft.com/library/azure/dn619392.aspx). Wenn Sie einen Encoder eines Drittanbieters verwendet haben, sollten Sie Ihre [Dateien überprüfen](https://msdn.microsoft.com/library/azure/dn750842.aspx).

Es wird empfohlen, Zwischendateien immer in einen MP4-Satz mit adaptiver Bitrate zu codieren und anschließend mithilfe der [dynamischen Paketerstellung](https://msdn.microsoft.com/library/azure/jj889436.aspx) in das gewünschte Format zu konvertieren. Um dynamische Paketerstellung nutzen zu können, ist mindestens eine bedarfsgesteuerte Streamingeinheit für den Streamingendpunkt erforderlich, aus dem die Inhalte bereitgestellt werden sollen. Weitere Informationen finden Sie unter [Skalieren von Media Services](media-services-manage-origins.md#scale_streaming_endpoints).

Wenn Ihr Ausgabemedienobjekt speicherverschlüsselt ist, müssen Sie die Übermittlungsrichtlinien für Medienobjekte konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren von Übermittlungsrichtlinien für Medienobjekte](media-services-dotnet-configure-asset-delivery-policy.md).

##Erstellen eines Auftrags mit einer einzelnen Codierungsaufgabe 

Bei der Codierung mit Azure Media Encoder können Sie die [hier](https://msdn.microsoft.com/library/azure/dn619389.aspx) angegebenen Voreinstellungen für die Aufgabenkonfiguration verwenden.

###Verwenden des Azure Media Services SDKs für .NET  

Mit der folgenden **EncodeToAdaptiveBitrateMP4Set**-Methode wird ein Codierungsauftrag erstellt und dem Auftrag eine einzelne Codierungsaufgabe hinzugefügt. Bei der Aufgabe wird „Azure Media Encoder“ verwendet, um eine Codierung in „H264 Adaptive Bitrate MP4 Set 720p“ auszuführen.

    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
    {
        var encodingPreset = "H264 Adaptive Bitrate MP4 Set 720p";

        IJob job = _context.Jobs.Create(String.Format("Encoding {0} into to {1}",
                                inputAsset.Name,
                                encodingPreset));

        var mediaProcessors = GetLatestMediaProcessorByName("Azure Media Encoder");

        ITask encodeTask = job.Tasks.AddNew("Encoding", mediaProcessors, encodingPreset, TaskOptions.None);
        
        encodeTask.InputAssets.Add(inputAsset);

        // Specify the storage-encrypted output asset.
        encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), 
            AssetCreationOptions.StorageEncrypted);


        job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
        job.Submit();
        job.GetExecutionProgressTask(CancellationToken.None).Wait();

        return job.OutputMediaAssets[0];
    }

    private static void JobStateChanged(object sender, JobStateChangedEventArgs e)
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
                break;
            default:
                break;
        }
    }

    private static IMediaProcessor GetLatestMediaProcessorByName(string mediaProcessorName)
    {
        var processor = _context.MediaProcessors.Where(p => p.Name == mediaProcessorName).
           ToList().OrderBy(p => new Version(p.Version)).LastOrDefault();

        if (processor == null)
            throw new ArgumentException(string.Format("Unknown media processor", mediaProcessorName));

        return processor;
    }

###Verwenden von Media Services SDK für .NET-Erweiterungen

    static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset)
    {
        // 1. Prepare a job with a single task to transcode the specified mezzanine asset
        //    into a multi-bitrate asset.
        IJob job = _context.Jobs.CreateWithSingleTask(
            MediaProcessorNames.AzureMediaEncoder,
            MediaEncoderTaskPresetStrings.H264AdaptiveBitrateMP4Set720p,
            asset,
            "Adaptive Bitrate MP4",
            AssetCreationOptions.None);

        Console.WriteLine("Submitting transcoding job...");

        // 2. Submit the job and wait until it is completed.
        job.Submit();
        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    } 

##Erstellen eines Auftrags mit verketteten Aufgaben 

In zahlreichen Anwendungsszenarien möchten Entwickler eine Reihe von Verarbeitungsaufgaben erstellen. In Media Services können Sie eine Reihe verketteter Aufgaben erstellen. In jeder Aufgabe können verschiedene Verarbeitungsschritte ausgeführt und unterschiedliche Medienprozessoren verwendet werden. Innerhalb der verketteten Aufgaben kann ein Medienobjekt von einer Aufgabe an eine andere übergeben werden, sodass eine lineare Aufgabensequenz auf das Medienobjekt angewendet wird. In einem Auftrag ausgeführte Aufgaben müssen jedoch nicht sequenziell sein. Beim Erstellen einer verketteten Aufgabe werden die verketteten **ITask**-Objekte in einem einzelnen **IJob**-Objekt erstellt.

>[AZURE.NOTE]Die Aufgabenanzahl ist derzeit auf 30 Aufgaben pro Auftrag begrenzt. Wenn Sie mehr als 30 Aufgaben verketten müssen, erstellen Sie mehrere Aufträge, um die Aufgaben zu verteilen.

Durch die folgende **CreateChainedTaskEncodingJob**-Methode wird ein Auftrag mit zwei verketteten Aufgaben erstellt. Folglich gibt die Methode einen Auftrag zurück, der zwei Ausgabemedienobjekte enthält.

	
    public static IJob CreateChainedTaskEncodingJob(IAsset asset)
    {
        // Declare a new job.
        IJob job = _context.Jobs.Create("My task-chained encoding job");

        // Set up the first task to encode the input file.

        // Get a media processor reference
        IMediaProcessor processor = GetLatestMediaProcessorByName("Azure Media Encoder");

        // Create a task with the encoding details, using a string preset.
        ITask task = job.Tasks.AddNew("My encoding task",
            processor,
           "H264 Adaptive Bitrate MP4 Set 720p",
            TaskOptions.ProtectedConfiguration);

        // Specify the input asset to be encoded.
        task.InputAssets.Add(asset);

        // Specify the storage-encrypted output asset.
        task.OutputAssets.AddNew("My storage-encrypted output asset",
            AssetCreationOptions.StorageEncrypted);

        // Set up the second task to decrypt the encoded output file from 
        // the first task.

        // Get another media processor instance
        IMediaProcessor decryptProcessor = GetLatestMediaProcessorByName("Storage Decryption");

        // Declare the decryption task. 
        ITask decryptTask = job.Tasks.AddNew("My decryption task",
            decryptProcessor,
            string.Empty,
            TaskOptions.None);

        // Specify the input asset to be decrypted. This is the output 
        // asset from the first task. 
        decryptTask.InputAssets.Add(task.OutputAssets[0]);

        // Specify an output asset to contain the results of the job. 
        // This should have AssetCreationOptions.None. 
        decryptTask.OutputAssets.AddNew("My decrypted output asset",
            AssetCreationOptions.None);

        // Use the following event handler to check job progress. 
        job.StateChanged += new
            EventHandler<JobStateChangedEventArgs>(JobStateChanged);

        // Launch the job.
        job.Submit();

        // Check job execution and wait for job to finish. 
        Task progressJobTask = job.GetExecutionProgressTask(CancellationToken.None);
        progressJobTask.Wait();

        //return job that contains two output assets.
        return job;
    }


##Nächste Schritte

[Azure Marketplace]: https://datamarket.azure.com/
[Encoder Preset]: http://msdn.microsoft.com/library/dn619392.aspx
[How to: Get a Media Processor Instance]: http://go.microsoft.com/fwlink/?LinkId=301732
[How to: Upload an Encrypted Asset]: http://go.microsoft.com/fwlink/?LinkId=301733
[How to: Deliver an Asset by Download]: http://go.microsoft.com/fwlink/?LinkId=301734
[How to Check Job Progress]: http://go.microsoft.com/fwlink/?LinkId=301737
[Task Preset for Azure Media Packager]: http://msdn.microsoft.com/library/windowsazure/hh973635.aspx
 

<!---HONumber=62-->