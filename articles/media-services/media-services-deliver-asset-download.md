<properties 
	pageTitle="Medienobjekt herunterladen" 
	description="Erfahren Sie, wie Sie Medienobjekte auf Ihren Computer herunterladen. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK für .NET." 
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
	ms.date="09/07/2015" 
	ms.author="juliako"/>

#Gewusst wie: Bereitstellen eines Medienobjekts durch Herunterladen

Dieser Artikel beschreibt Optionen zur Bereitstellung von Medienobjekten, die in Media Services hochgeladen wurden. Sie können Media Services-Inhalte in verschiedenen Anwendungsszenarien bereitstellen. Sie können Medienobjekte herunterladen oder über einen Locator abrufen. Sie können Medieninhalte an andere Anwendungen oder andere Inhaltsanbieter senden. Für verbesserte Leistung und Skalierbarkeit können Sie Inhalte auch über ein Netzwerk für die Inhaltsübermittlung (Content Delivery Network, CDN) anbieten.

Dieses Beispiel zeigt, wie Sie Medienobjekte von Media Services auf Ihren lokalen Computer herunterladen können. Der Code fragt die Jobs des Media Services-Kontos nach Job-ID ab und greift auf die **OutputMediaAssets**-Sammlung zu (eine Sammlung mit einem oder mehreren Ausgabe-Medienobjekten als Ergebnis einer Jobausführung). Dieses Beispiel zeigt, wie Sie Ausgabemedienobjekte eines Auftrags herunterladen können. Dieser Ansatz funktioniert jedoch auch für den Download anderer Medienobjekte.

	
	// Download the output asset of the specified job to a local folder.
	static IAsset DownloadAssetToLocal( string jobId, string outputFolder)
	{
	    // This method illustrates how to download a single asset. 
	    // However, you can iterate through the OutputAssets
	    // collection, and download all assets if there are many. 
	
	    // Get a reference to the job. 
	    IJob job = GetJob(jobId);
	
	    // Get a reference to the first output asset. If there were multiple 
	    // output media assets you could iterate and handle each one.
	    IAsset outputAsset = job.OutputMediaAssets[0];
	
		// Create a SAS locator to download the asset
	    IAccessPolicy accessPolicy = _context.AccessPolicies.Create("File Download Policy", TimeSpan.FromDays(30), AccessPermissions.Read);
	    ILocator locator = _context.Locators.CreateLocator(LocatorType.Sas, outputAsset, accessPolicy);
	
	    BlobTransferClient blobTransfer = new BlobTransferClient
	    {
	        NumberOfConcurrentTransfers = 20,
	        ParallelTransferThreadCount = 20
	    };
	
	    var downloadTasks = new List<Task>();
	    foreach (IAssetFile outputFile in outputAsset.AssetFiles)
	    {
	        // Use the following event handler to check download progress.
	        outputFile.DownloadProgressChanged += DownloadProgress;
	
	        string localDownloadPath = Path.Combine(outputFolder, outputFile.Name);
	
	        Console.WriteLine("File download path:  " + localDownloadPath);
	
	        downloadTasks.Add(outputFile.DownloadAsync(Path.GetFullPath(localDownloadPath), blobTransfer, locator, CancellationToken.None));
	
	        outputFile.DownloadProgressChanged -= DownloadProgress;
	    }
	
	    Task.WaitAll(downloadTasks.ToArray());
	
	    return outputAsset;
	}
	
	static void DownloadProgress(object sender, DownloadProgressChangedEventArgs e)
	{
	    Console.WriteLine(string.Format("{0} % download progress. ", e.Progress));
	}



##Media Services-Lernpfade

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Feedback geben

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

   
##Siehe auch 

[Bereitstellen von Streaming-Inhalten](media-services-deliver-streaming-content.md)

<!---HONumber=Nov15_HO3-->