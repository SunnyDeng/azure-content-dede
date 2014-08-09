<properties linkid="develop-media-services-how-to-guides-deliver-streaming-content" urlDisplayName="Deliver Streaming Content from Media Services" pageTitle="How to Deliver Streaming Content from Media Services – Azure" metaKeywords="" description="Learn how to deliver streaming content from Media Services using a direct URL. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="How to: Deliver streaming content" authors="" />

Vorgehensweise: Bereitstellen von Streaming-Inhalten
====================================================

Dieser Beitrag gehört zur einer Reihe mehrerer Beiträge, in denen es um die Programmierung von Azure Media Services geht. Das vorherige Thema war [Gewusst wie: Bereitstellen eines Medienobjekts durch Herunterladen](http://go.microsoft.com/fwlink/?LinkID=301734&clcid=0x409).

Zusätzlich zum Herunterladen von Medieninhalten von Media Services können Sie Streaming mit adaptiver Bitrate verwenden, um Inhalte bereitzustellen. Sie können z. B. eine direkte URL, einen so genannten Locator, erstellen, um Inhalte auf einem Media Services-Ursprungsserver zu streamen. Anschließend können die Streaming-Inhalte auf Client-Anwendungen wie Microsoft Silverlight direkt über den Locator abgespielt werden.

Das folgende Beispiel zeigt, wie ein Ursprungs-Locator für ein von einem Job produziertes bereitgestelltes Medienobjekt erstellt wird. In diesem Beispiel wird angenommen, dass Sie bereits einen Verweis auf ein Medienobjekt abgerufen haben, das Smooth Streaming-Dateien enthält, und dass in dem Code auf die Variable namens **assetToStream** verwiesen wird.

So erstellen Sie einen Ursprungs-Locator zum Streamen von Inhalten:

1.  Rufen Sie einen Verweis auf die Streaming-Manifestdatei (.ism) in dem Medienobjekt ab
2.  Definieren Sie eine Zugriffsrichtlinie
3.  Erstellen Sie den Ursprungs-Locator durch Aufrufen der Methode CreateLocator
4.  Erstellen Sie eine URL zur Manifestdatei

Der folgende Code zeigt, wie diese Schritte ausgeführt werden:

``` {}
private static ILocator GetStreamingOriginLocator( string targetAssetID)
{
    // Verweis auf das Medienobjekt abrufen, das Sie streamen möchten.
    IAsset assetToStream = GetAsset(targetAssetID);

    // Verweis auf die Streaming-Manifestdatei aus der Sammlung  
    // von Dateien in dem Medienobjekt abrufen. 
    var theManifest =
                        from f in assetToStream.AssetFiles
                        where f.Name.EndsWith(".ism")
                        select f;

    // Den Verweis in das Dateiformat IAssetFile umwandeln. 
    IAssetFile manifestFile = theManifest.First();
    IAccessPolicy policy = null;
    ILocator originLocator = null;
            
    // Eine Zugriffsrichtlinie mit 30-tägigem Schreibschutz erstellen. 
    policy = _context.AccessPolicies.Create("Streaming policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

    // Einen OnDemandOrigin-Locator für das Medienobjekt erstellen. 
    originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
        policy,
        DateTime.UtcNow.AddMinutes(-5));
            
    // Einige nützliche auf dem Locator basierende Werte anzeigen.
    Console.WriteLine("Streaming asset base path on origin: ");
    Console.WriteLine(originLocator.Path);
    Console.WriteLine();
    
    // Eine vollständige URL zur Manifestdatei erstellen. Diese URL zum Abspielen
    // auf Streaming-Media-Clients verwenden. 
    string urlForClientStreaming = originLocator.Path + manifestFile.Name + "/manifest";
    Console.WriteLine("URL to manifest for client streaming: ");
    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();
    
    // Die IDs des Ursprungs-Locators, der Zugriffsrichtlinie und des Medienobjekts anzeigen.
    Console.WriteLine("Origin locator Id: " + originLocator.Id);
    Console.WriteLine("Access policy Id: " + policy.Id);
    Console.WriteLine("Streaming asset Id: " + assetToStream.Id);

    // Den Locator zurückgeben. 
    return originLocator;
}
```

Weitere Informationsquellen zum Bereitstellen von Medienobjekten finden Sie unter

-   [Bereitstellen von Medienobjekten mit dem Media Services SDK für .NET](http://msdn.microsoft.com/de-de/library/jj129575.aspx)
-   [Bereitstellen von Medienobjekten mit der Media Services REST-API](http://msdn.microsoft.com/de-de/library/jj129578.aspx)

Nächste Schritte
----------------

Bisher haben wir die Bereitstellung von Medien durch Herunterladen von Azure-Speicher mithilfe von Smooth Streaming beschrieben. Im nächsten Thema [Bereitstellen von HLS-Inhalten](http://go.microsoft.com/fwlink/?LinkId=301817) wird die Bereitstellung von Streaming-Inhalten mit Apple HTTP Live Streaming (HLS) diskutiert.

