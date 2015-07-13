<properties 
	pageTitle="Bereitstellen von Streaminginhalten über Media Services" 
	description="Erfahren Sie, wie Sie einen Locator erstellen, der zum Generieren einer Streaming-URL verwendet wird. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK für .NET." 
	authors="juliako" 
	manager="dwrede" 
	editor="" 
	services="media-services" 
	documentationCenter=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/24/2015" 
	ms.author="juliako"/>


#Gewusst wie: Bereitstellen von Streaminginhalten


Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](media-services-video-on-demand-workflow.md) und [Media Services: Livestreaming-Workflow](media-services-live-streaming-workflow.md).

##Übersicht

Sie können einen MP4-Satz mit adaptiver Bitrate streamen, indem Sie einen OnDemand-Streaminglocator und eine Streaming-URL erstellen. Im Thema [Codieren eines Medienobjekts](media-services-encode-asset.md) wird die Codierung in einen MP4-Satz mit adaptiver Bitrate erläutert. Bevor Sie einen Locator erstellen, sollten Sie eine Übermittlungsrichtlinie für Medienobjekte konfigurieren, wie in [diesem](media-services-dotnet-configure-asset-delivery-policy.md) Thema beschrieben.

Sie können auch einen OnDemand-Streaminglocator zum Erstellen von URLs verwenden, die auf MP4-Dateien verweisen, die progressiv heruntergeladen werden können.

In diesem Thema wird erläutert, wie Sie einen OnDemand-Streaminglocator erstellen, um Ihr Medienobjekt zu veröffentlichen und Smooth-, MPEG-DASH- sowie HLS-Streaming-URLs zu erstellen. Außerdem wird veranschaulicht, wie Sie URLs für progressive Downloads erstellen.
  	 
##Erstellen eines OnDemand-Streaminglocators

Verfahren Sie zum Erstellen des OnDemand-Streaminglocators und Abrufen von URLs wie folgt:

   1. Definieren Sie eine Zugriffsrichtlinie.
   2. Erstellen Sie einen OnDemand-Streaminglocator.
   3. Wenn Sie ein Streaming planen, rufen Sie die Streaming-Manifestdatei (ISM) im Medienobjekt ab. 
   		
	Wenn Sie einen progressiven Download ausführen möchten, rufen Sie die Namen der MP4-Dateien im Medienobjekt ab.  
   4. Erstellen Sie URLs für die Manifestdatei oder MP4-Dateien. 
   

###Verwenden des Media Services .NET SDKs 

Erstellen von Streaming-URLs

	private static void BuildStreamingURLs(IAsset asset)
	{
	
	    // Create a 30-day readonly access policy. 
	    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
	        TimeSpan.FromDays(30),
	        AccessPermissions.Read);
	
	    // Create a locator to the streaming content on an origin. 
	    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
	        policy,
	        DateTime.UtcNow.AddMinutes(-5));
	
	    // Display some useful values based on the locator.
	    Console.WriteLine("Streaming asset base path on origin: ");
	    Console.WriteLine(originLocator.Path);
	    Console.WriteLine();
	
	    // Get a reference to the streaming manifest file from the  
	    // collection of files in the asset. 
	    var manifestFile = asset.AssetFiles.Where(f => f.Name.ToLower().
	                                EndsWith(".ism")).
	                                FirstOrDefault();
	    
	    // Create a full URL to the manifest file. Use this for playback
	    // in streaming media clients. 
	    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
	    Console.WriteLine("URL to manifest for client streaming using Smooth Streaming protocol: ");
	    Console.WriteLine(urlForClientStreaming);
	    Console.WriteLine("URL to manifest for client streaming using HLS protocol: ");
	    Console.WriteLine(urlForClientStreaming + "(format=m3u8-aapl)");
	    Console.WriteLine("URL to manifest for client streaming using MPEG DASH protocol: ");
	    Console.WriteLine(urlForClientStreaming + "(format=mpd-time-csf)"); 
	    Console.WriteLine();
	}

Codeausgabe:
	
	URL to manifest for client streaming using Smooth Streaming protocol:
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest
	URL to manifest for client streaming using HLS protocol:
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=m3u8-aapl)
	URL to manifest for client streaming using MPEG DASH protocol:
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny.ism/manifest(format=mpd-time-csf)
	

Erstellen von URLs für progressive Downloads

	private static void BuildProgressiveDownloadURLs(IAsset asset)
	{
	    // Create a 30-day readonly access policy. 
	    IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
	        TimeSpan.FromDays(30),
	        AccessPermissions.Read);
	
	    // Create an OnDemandOrigin locator to the asset. 
	    ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
	        policy,
	        DateTime.UtcNow.AddMinutes(-5));
	
	    // Display some useful values based on the locator.
	    Console.WriteLine("Streaming asset base path on origin: ");
	    Console.WriteLine(originLocator.Path);
	    Console.WriteLine();
	
	    // Get MP4 files.
	    IEnumerable<IAssetFile> mp4AssetFiles = asset
	        .AssetFiles
	        .ToList()
	        .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));
	            
	    // Create a full URL to the MP4 files. Use this to progressively download files.
	    foreach (var pd in mp4AssetFiles)
	        Console.WriteLine(originLocator.Path + pd.Name);
	}

Codeausgabe:
	
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4
	http://amstest1.streaming.mediaservices.windows.net/3c5fe676-199c-4620-9b03-ba014900f214/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4
	
	. . . 

###Verwenden von Media Services .NET SDK-Erweiterungen

Im folgenden Code werden .NET SDK-Erweiterungsmethoden aufgerufen, durch die ein Locator erstellt und die Smooth Streaming-, HLS- und MPEG-DASH-URLs für adaptives Streaming generiert werden.

	// Create a loctor.
	_context.Locators.Create(
	    LocatorType.OnDemandOrigin,
	    inputAsset,
	    AccessPermissions.Read,
	    TimeSpan.FromDays(30));
	
	// Get the streaming URLs.
	Uri smoothStreamingUri = inputAsset.GetSmoothStreamingUri();
	Uri hlsUri = inputAsset.GetHlsUri();
	Uri mpegDashUri = inputAsset.GetMpegDashUri();
	
	Console.WriteLine(smoothStreamingUri);
	Console.WriteLine(hlsUri);
	Console.WriteLine(mpegDashUri);
 

<!---HONumber=July15_HO1-->