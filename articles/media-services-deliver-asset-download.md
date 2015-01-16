<properties urlDisplayName="Delivering Media Assets" pageTitle="Bereitstellen von Medieninhalten - Azure" metaKeywords="" description="Entdecken Sie Optionen zum Bereitstellen von Medienobjekten, die in Media Services in Azure hochgeladen wurden. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK für .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver an Asset by Download" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako" />





<h1>Gewusst wie: Bereitstellen eines Medienobjekts durch Herunterladen</h1>
Dieser Artikel ist Teil einer Reihe zum Thema Programmierung von Azure-Mediendiensten. Das vorherige Thema war [Gewusst wie: Verwalten von Medienobjekten](../media-services-manage-assets/).

Dieser Artikel beschreibt Optionen zur Bereitstellung von Medienobjekten, die in Media Services hochgeladen wurden. Sie können Media Services-Inhalte in verschiedenen Anwendungsszenarien bereitstellen. Sie können Medienobjekte herunterladen oder über einen Locator abrufen. Sie können Medieninhalte an andere Anwendungen oder andere Inhaltsanbieter senden. Für verbesserte Leistung und Skalierbarkeit können Sie Inhalte auch über ein Content Delivery Network (CDN) wie z. B. das Azure CDN anbieten.

Dieses Beispiel zeigt, wie Sie Medienobjekte von Media Services herunterladen können. Der Code fragt die Jobs des Media Services-Kontos nach Job-ID ab und greift auf die **OutputMediaAssets**-Sammlung zu (eine Sammlung mit einem oder mehreren Ausgabe-Medienobjekten als Ergebnis einer Jobausführung). Dieses Beispiel zeigt, wie Sie Ausgabe-Medienobjekte eines Jobs herunterladen können. Dieser Ansatz funktioniert jedoch auch für den Download anderer Medienobjekte.

<pre><code> 
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
    ILocator locator = _context.Locators.CreateSasLocator(outputAsset, accessPolicy);

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
</code></pre>

Weitere Informationen zur Auslieferung von Medienobjekten finden Sie unter:
<ul>
<li><a href="http://msdn.microsoft.com/de-de/library/jj129575.aspx">Bereitstellen von Medienobjekten mit dem Media Services SDK für .NET</a></li>
<li><a href="http://msdn.microsoft.com/de-de/library/jj129578.aspx">Bereitstellen von Medienobjekten mit der Media Services REST-API</a></li>
</ul>

<h2>Nächste Schritte</h2>
Sie haben nun gelernt, wie Sie ein Medienobjekt aus dem Azure-Speicher herunterladen können. Weitere Informationen zu anderen Möglichkeiten für die Bereitstellung von Medienobjekten finden Sie im Thema [Bereitstellen von Streaming-Inhalten](../media-services-deliver-streaming-content/) .

<!--HONumber=35.1-->

<!--HONumber=35.1-->
