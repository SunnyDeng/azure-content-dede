<properties urlDisplayName="Deliver Streaming Content from Media Services" pageTitle="Bereitstellen von Streaming-Inhalten in Media Services &ndash; Azure" metaKeywords="" description="Erfahren Sie, wie Sie Streaming-Inhalte in Media Services mit einer URL f&uuml;r direkten Zugriff bereitstellen k&ouml;nnen. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK f&uuml;r .NET." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="Gewusst wie: Bereitstellen von Streaming-Inhalten" authors="juliako" manager="dwrede" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# Gewusst wie: Bereitstellen von Streaming-Inhalten

Dieser Artikel ist Teil einer Reihe zum Thema Programmierung von Azure-Mediendiensten. Das vorherige Thema war [Vorgehensweise: Bereitstellen eines Medienobjekts durch Herunterladen][Vorgehensweise: Bereitstellen eines Medienobjekts durch Herunterladen].

Zusätzlich zum Herunterladen von Medieninhalten von Media Services können Sie Streaming mit adaptiver Bitrate verwenden, um Inhalte bereitzustellen. Sie können z. B. eine direkte URL, einen so genannten Locator, erstellen, um Inhalte auf einem Media Services-Ursprungsserver zu streamen. Anschließend können die Streaming-Inhalte auf Client-Anwendungen wie Microsoft Silverlight direkt über den Locator abgespielt werden.

Das folgende Beispiel zeigt, wie ein Ursprungs-Locator für ein von einem Job produziertes bereitgestelltes Medienobjekt erstellt wird. In diesem Beispiel wird angenommen, dass Sie bereits einen Verweis auf ein Medienobjekt abgerufen haben, das Smooth Streaming-Dateien enthält, und dass in dem Code auf die Variable namens **assetToStream** verwiesen wird.

So erstellen Sie einen Ursprungs-Locator zum Streamen von Inhalten:

1.  Rufen Sie einen Verweis auf die Streaming-Manifestdatei (.ism) in dem Medienobjekt ab
2.  Definieren Sie eine Zugriffsrichtlinie
3.  Erstellen Sie den Ursprungs-Locator durch Aufrufen der Methode CreateLocator
4.  Erstellen Sie eine URL zur Manifestdatei

Der folgende Code zeigt die Implementierung der Schritte:

    private static ILocator GetStreamingOriginLocator( string targetAssetID){ // Get a reference to the asset you want to stream. IAsset assetToStream = GetAsset(targetAssetID);
    // Get a reference to the streaming manifest file from the  
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
    policy = _context.AccessPolicies.Create("Streaming policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

    // Create an OnDemandOrigin locator to the asset. 
    originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
        policy,
        DateTime.UtcNow.AddMinutes(-5));
            
    // Display some useful values based on the locator.
    Console.WriteLine("Streaming asset base path on origin: ");
    Console.WriteLine(originLocator.Path);
    Console.WriteLine();

    // Create a full URL to the manifest file. Use this for playback
    // in streaming media clients. 
    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
    Console.WriteLine("URL to manifest for client streaming: ");
    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();

    // Display the ID of the origin locator, the access policy, and the asset.
    Console.WriteLine("Origin locator Id: " + originLocator.Id);
    Console.WriteLine("Access policy Id: " + policy.Id);
    Console.WriteLine("Streaming asset Id: " + assetToStream.Id);

    // Return the locator. 
    return originLocator;

<p>
}
</code>

</pre>
</p>
Weitere Informationen zur Auslieferung von Medienobjekten finden Sie unter:

-   [Bereitstellen von Medienobjekten mit dem Media Services SDK für .NET][Bereitstellen von Medienobjekten mit dem Media Services SDK für .NET]
-   [Bereitstellen von Medienobjekten mit der Media Services REST-API][Bereitstellen von Medienobjekten mit der Media Services REST-API]

</p>
## Nächste Schritte

Bisher haben wir die Bereitstellung von Medien durch Herunterladen von Azure-Speicher mithilfe von Smooth Streaming beschrieben. Im nächsten Thema [Bereitstellen von HLS-Inhalten][Bereitstellen von HLS-Inhalten] wird die Bereitstellung von Streaming-Inhalten mit Apple HTTP Live Streaming (HLS) diskutiert.

  [Vorgehensweise: Bereitstellen eines Medienobjekts durch Herunterladen]: ../media-services-deliver-asset-download/
  [Bereitstellen von Medienobjekten mit dem Media Services SDK für .NET]: http://msdn.microsoft.com/de-de/library/jj129575.aspx
  [Bereitstellen von Medienobjekten mit der Media Services REST-API]: http://msdn.microsoft.com/de-de/library/jj129578.aspx
  [Bereitstellen von HLS-Inhalten]: ../media-services-deliver-http-live-streaming-content/
