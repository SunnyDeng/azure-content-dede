<properties 
	pageTitle="Bereitstellen von Apple HTTP Live Streaming (HLS) - Azure" 
	description="Erfahren Sie, wie Sie einen Locator zu Apple HTTP Live Streaming (HLS)-Inhalten auf einem Media Services-Ursprungsserver erstellen können. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK für .NET." 
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
	ms.date="10/30/2014" 
	ms.author="juliako"/>





<h1>Vorgehensweise: Bereitstellen von Apple HLS-Streaminginhalten</h1>

Dieser Artikel ist Teil einer Reihe zum Thema Programmierung mit Azure Media Services. Das vorherige Thema war [Gewusst wie: Bereitstellen von Streaminginhalten](media-services-deliver-streaming-content.md).

In diesem Thema lernen Sie das Erstellen eines Locators zu Apple HTTP Live Streaming (HLS)-Inhalten auf einem Media Services-Ursprungsserver. Mit diesem Verfahren können Sie eine URL zu Apple HLS-Inhalten erstellen und Apple iOS-Geräten für die Wiedergabe bereitstellen. Die grundlegende Methode zum Erstellen der Locator-URL ist identisch. Erstellen Sie einen Locator zum Pfad des Apple HLS-Streaming-Medienobjekts auf einem Ursprungsserver, und erstellen Sie dann eine vollständige URL als Link zum Manifest für den Streaminginhalt.

Im folgenden Codebeispiel wird angenommen, dass Sie bereits einen Verweis auf ein HLS-Streaming-Medienobjekt abgerufen haben und dass in dem Code auf die Variable namens **assetToStream** verwiesen wird. Nachdem Sie mit diesem Code einen Ursprungs-Locator zum Medienobjekt generiert haben, können Sie die resultierende URL verwenden, um den Streaminginhalt auf einem iOS-Gerät wie z. B. einem iPad oder iPhone abzuspielen.

So erstellen Sie einen Locator zu Apple HLS-Streaminginhalten:

   1. Rufen Sie einen Verweis auf die Manifestdatei im Medienobjekt ab.
   2. Definieren Sie eine Zugriffsrichtlinie.
   3. Erstellen Sie den Ursprungs-Locator durch Aufrufen der CreateLocator-Methode.
   4. Erstellen Sie eine URL zur Manifestdatei.

Der folgende Code zeigt die Implementierung der Schritte:

<pre><code>
static ILocator GetStreamingHLSOriginLocator( string targetAssetID)
{
    // Get a reference to the asset you want to stream.
    IAsset assetToStream = GetAsset(targetAssetID);

    // Get a reference to the HLS streaming manifest file from the  
    // collection of files in the asset. 
    var theManifest =
                        from f in assetToStream.AssetFiles
                        where f.Name.EndsWith(".ism")
                        select f;

    // Cast the reference to a true IAssetFile type. 
    IAssetFile manifestFile = theManifest.First();
    IAccessPolicy policy = null;
    ILocator originLocator = null;

    // Create a 30-day readonly access policy. 
    policy = _context.AccessPolicies.Create("Streaming HLS Policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

    originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
                policy,
                DateTime.UtcNow.AddMinutes(-5));

    // Create a URL to the HLS streaming manifest file. Use this for playback
    // in Apple iOS streaming clients.
    string urlForClientStreaming = originLocator.Path
        + manifestFile.Name + "/manifest(format=m3u8-aapl)";
    Console.WriteLine("URL to manifest for client streaming: ");
    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();

    // Return the locator. 
    return originLocator;
}
</code></pre>

Weitere Informationen zur Auslieferung von Medienobjekten finden Sie unter:
<ul>
<li><a href="http://msdn.microsoft.com/library/jj129575.aspx">Bereitstellen von Medienobjekten mit dem Media Services SDK für .NET</a></li>
<li><a href="http://msdn.microsoft.com/library/jj129578.aspx">Bereitstellen von Medienobjekten mit der Media Services-REST-API</a></li>
</ul>

<h2>Nächste Schritte</h2>

Dies ist das letzte Thema der Reihe zur Verwendung von Azure Media Services. Sie haben erfahren, wie Sie Ihren Computer für die Media Services-Entwicklung einrichten und typische Programmieraufgaben ausführen. Weitere Informationen zur Programmierung von Media Services finden Sie in den folgenden Ressourcen:

-   [Azure Media Services-Dokumentation][]
-   [Erste Schritte mit dem Media Services SDK für .NET][]
-   [Erstellen von Anwendungen mit dem Media Services SDK für .NET][]
-   [Erstellen von Anwendungen mit der Azure Media Services-REST-API][]
-   [Media Services-Forum][]
-	[Überwachen eines Media Services-Kontos](media-services-monitor-services-account.md)
-	[Verwalten von Inhalten in Media Services](media-services-manage-content.md)

[Azure Media Services-Dokumentation]: http://go.microsoft.com/fwlink/?linkid=245437
[Erste Schritte mit dem Media Services SDK für .NET]: http://go.microsoft.com/fwlink/?linkid=252966
[Erstellen von Anwendungen mit der Azure Media Services-REST-API]: http://go.microsoft.com/fwlink/?linkid=252967
[Erstellen von Anwendungen mit dem Media Services SDK für .NET]: http://go.microsoft.com/fwlink/?linkid=247821
[Media Services-Forum]: http://social.msdn.microsoft.com/Forums/en-US/MediaServices/threads

<!--HONumber=45--> 
