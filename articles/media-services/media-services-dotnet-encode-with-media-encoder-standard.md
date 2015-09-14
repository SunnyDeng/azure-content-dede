<properties 
	pageTitle="Codieren eines Assets mit Media Encoder Standard"
	description="In diesem Thema wird veranschaulicht, wie Sie .NET mithilfe von Media Encoder Standard verwenden, um ein Asset zu codieren."
	services="media-services"
	documentationCenter=""
	authors="juliako"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="media-services"
	ms.workload="media"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/24/2015"
	ms.author="juliako"/>


#Codieren eines Assets mit Media Encoder Standard

Die Codierung ist einer der häufigsten Verarbeitungsvorgänge in Media Services. Sie erstellen Codierungsaufträge, um Mediendateien von einer Codierung in eine andere zu konvertieren. Zum Codieren können Sie den in Media Services integrierten Media Encoder verwenden. Außerdem können Sie Codierer von Media Services-Partnern verwenden. Externe Codierer finden Sie im Azure Marketplace.

In diesem Thema wird veranschaulicht, wie Sie .NET mithilfe von Media Encoder Standard verwenden, um Assets zu codieren. Media Encoder Standard wird mithilfe einer der Encoder-Voreinstellungen konfiguriert, die [hier](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409) beschrieben sind.

>[AZURE.NOTE]Die aktuelle Version des Medienprozessors erfordert, dass Sie eine vollständige XML- oder JSON-Zeichenfolge als die Codierungsvoreinstellung übergeben. Die Unterstützung für die Übergabe in eine benannte Zeichenfolge, wie z. B. "H264 mehreren Bitraten 720p", ist in Kürze über ein Update verfügbar.

Es wird empfohlen, Zwischendateien immer in einen MP4-Satz mit adaptiver Bitrate zu codieren und anschließend mithilfe der [dynamischen Paketerstellung](media-services-dynamic-packaging-overview.md) in das gewünschte Format zu konvertieren. Um dynamische Paketerstellung nutzen zu können, ist mindestens eine bedarfsgesteuerte Streamingeinheit für den Streamingendpunkt erforderlich, aus dem die Inhalte bereitgestellt werden sollen. Weitere Informationen finden Sie unter [Skalieren von Media Services](media-services-manage-origins.md#scale_streaming_endpoints).

Wenn Ihr Ausgabemedienobjekt speicherverschlüsselt ist, müssen Sie die Übermittlungsrichtlinien für Medienobjekte konfigurieren. Weitere Informationen finden Sie unter [Konfigurieren von Übermittlungsrichtlinien für Medienobjekte](media-services-dotnet-configure-asset-delivery-policy.md).

##Beispiel

Im folgenden Codebeispiel wird das Media Services-.NET-SDK verwendet, um die folgenden Aufgaben auszuführen:

- Erstellen eines Codierungsauftrags.
- Abrufen eines Verweises auf den Media Encoder Standard-Encoder
- Laden Sie die vordefinierte XML aus einer der [hier](http://go.microsoft.com/fwlink/?linkid=618336&clcid=0x409) gezeigten Vorgaben.
- Fügen Sie eine einzelne Codierungsaufgabe zum Auftrag hinzu. 
- Geben Sie das zu codierende Asset an.
- Erstellen Sie ein Ausgabeasset, das das codierte Asset enthalten soll.
- Fügen Sie einen Ereignishandler hinzu, um den Auftragsstatus zu überprüfen.
- Übermitteln des Auftrags.
		
		static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset asset, string pathToLocalPresetFile)
		{
		    // Declare a new job.
		    IJob job = _context.Jobs.Create("Media Encoder Standard Job");
		    // Get a media processor reference, and pass to it the name of the 
		    // processor to use for the specific task.
		    IMediaProcessor processor = GetLatestMediaProcessorByName("Media Encoder Standard");
		
		    // Load the XML (or JSON) from the local file
		    string configuration = File.ReadAllText(pathToLocalPresetFile);
		
		    // Create a task
		    ITask task = job.Tasks.AddNew("Media Encoder Standard encoding task",
		        processor,
		        configuration,
		        TaskOptions.None);
		
		    // Specify the input asset to be encoded.
		    task.InputAssets.Add(asset);
		    // Add an output asset to contain the results of the job. 
		    // This output is specified as AssetCreationOptions.None, which 
		    // means the output asset is not encrypted. 
		    task.OutputAssets.AddNew("Output asset",
		        AssetCreationOptions.None);
		
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

##Siehe auch 

[Media Services-Codierung (Übersicht)](media-services-encode-asset.md)

<!---HONumber=September15_HO1-->