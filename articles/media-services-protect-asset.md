<properties urlDisplayName="Encrypt Assets in Media Services" pageTitle="Verschl&uuml;sseln von Medienobjekten in Media Services &ndash; Azure" metaKeywords="" description="Erfahren Sie, wie Sie Medieninhalte in Media Services mit dem Microsoft PlayReady-Schutz verschl&uuml;sseln k&ouml;nnen. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK f&uuml;r .NET. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK f&uuml;r .NET." metaCanonical="" services="media-services" documentationCenter="" title="Gewusst wie: Sch&uuml;tzen eines Medienobjekts mit dem PlayReady-Schutz" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# <a name="playready"></a>Gewusst wie: Schützen eines Medienobjekts mit dem PlayReady-Schutz

Dieser Artikel ist Teil einer Reihe zum Thema Programmierung von Azure-Mediendiensten. Das vorherige Thema war [Vorgehensweise: Nachverfolgen des Auftragsstatus][Vorgehensweise: Nachverfolgen des Auftragsstatus].

Mit den Azure-Mediendiensten können Sie einen Auftrag übermitteln, der den Microsoft PlayReady-Schutz zur Verschlüsselung eines Medienobjekts integriert. Der Code in diesem Abschnitt übernimmt mehrere Streamingdateien aus einem Eingabeordner, erstellt eine Aufgabe und verschlüsselt die Dateien mithilfe von PlayReady Protection.

Das folgende Beispiel zeigt, wie ein einfacher Auftrag erstellt wird, um PlayReady Protection bereitzustellen.

1.  Rufen Sie die Konfigurationsdaten ab. Sie können eine Beispielkonfigurationsdatei des Themas [Aufgabenvoreinstellung für Azure Media Encryptor][Aufgabenvoreinstellung für Azure Media Encryptor] erhalten.
2.  Hochladen einer MP4-Eingabedatei
3.  Umwandeln der MP4-Datei in ein Smooth Streaming-Medienobjekt
4.  Verschlüsseln des Medienobjekts mit PlayReady
5.  Übermitteln des Auftrags

Das folgende Codebeispiel zeigt, wie die Schritte implementiert werden:

    private static IJob CreatePlayReadyProtectionJob(string inputMediaFilePath, string primaryFilePath, string configFilePath)
    {
        // Create a storage-encrypted asset and upload the MP4. 
        IAsset asset = CreateAssetAndUploadSingleFile(AssetCreationOptions.StorageEncrypted, inputMediaFilePath);

        // Declare a new job to contain the tasks.
        IJob job = _context.Jobs.Create("My PlayReady Protection job");

        // Set up the first task. 

        // Read the task configuration data into a string. 
        string configMp4ToSmooth = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaPackager_MP4ToSmooth.xml"));

        // Get a media processor instance
        IMediaProcessor processor = GetLatestMediaProcessorByName("Windows Azure Media Packager");

        // Create a task with the conversion details, using the configuration data 
        ITask task = job.Tasks.AddNew("My Mp4 to Smooth Task",
            processor,
            configMp4ToSmooth,
            _clearConfig);

        // Specify the input asset to be converted.
        task.InputAssets.Add(asset);

        // Add an output asset to contain the results of the job. We do not need 
        // to persist the output asset to storage, so set the shouldPersistOutputOnCompletion
        // parameter to false. 
        task.OutputAssets.AddNew("Streaming output asset", AssetCreationOptions.None);
        IAsset smoothOutputAsset = task.OutputAssets[0];

        // Set up the encryption task. 

        // Read the configuration data into a string. 
        string configPlayReady = File.ReadAllText(Path.GetFullPath(configFilePath + @"\MediaEncryptor_PlayReadyProtection.xml"));

        // Get a media processor instance
        IMediaProcessor playreadyProcessor = GetLatestMediaProcessorByName("Windows Azure Media Encryptor");

        // Create a second task, specifying a task name, the media processor, and configuration
        ITask playreadyTask = job.Tasks.AddNew("My PlayReady Task",
            playreadyProcessor,
            configPlayReady,
            _protectedConfig);

        // Add the input asset, which is the smooth streaming output asset from the first task. 
        playreadyTask.InputAssets.Add(smoothOutputAsset);

        // Add an output asset to contain the results of the job. Persist the output by setting 
        // the shouldPersistOutputOnCompletion param to true.
        playreadyTask.OutputAssets.AddNew("PlayReady protected output asset",
            AssetCreationOptions.None);

        // Use the following event handler to check job progress. 
        job.StateChanged += new
                EventHandler<JobStateChangedEventArgs>(StateChanged);

        // Launch the job.
        job.Submit();

        // Optionally log job details. This displays basic job details
        // to the console and saves them to a JobDetails-{JobId}.txt file 
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

Weitere Informationen zum PlayReady-Schutz finden Sie unter:

-   [Schützen von Medienobjekten mit Microsoft PlayReady][Schützen von Medienobjekten mit Microsoft PlayReady]
-   [Microsoft PlayReady][Microsoft PlayReady]

</p>
## Nächste Schritte

Da Sie nun wissen, wie Medienobjekte mit Media Services geschützt werden, können Sie mit dem Thema [Verwalten von Medienobjekten][Verwalten von Medienobjekten] fortfahren.

  [Vorgehensweise: Nachverfolgen des Auftragsstatus]: ../media-services-check-job-progress/
  [Aufgabenvoreinstellung für Azure Media Encryptor]: http://msdn.microsoft.com/de-de/library/hh973610.aspx
  [Schützen von Medienobjekten mit Microsoft PlayReady]: http://msdn.microsoft.com/de-de/library/dn189154.aspx
  [Microsoft PlayReady]: http://www.microsoft.com/PlayReady/
  [Verwalten von Medienobjekten]: ../media-services-manage-assets/
