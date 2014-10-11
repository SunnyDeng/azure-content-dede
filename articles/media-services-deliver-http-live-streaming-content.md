<properties linkid="develop-media-services-how-to-guides-deliver-apple-live-streaming" urlDisplayName="Deliver Apple HTTP Live Streaming (HLS)" pageTitle="How to Deliver Apple HTTP Live Streaming (HLS) - Azure" metaKeywords="" description="Learn how to create a locator to Apple HTTP Live Stream (HLS) content on Media Services origin server. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver Apple HLS streaming content" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# Gewusst wie: Bereitstellen von Apple HLS-Streaming-Inhalten

Dieser Artikel ist Teil einer Reihe zum Thema Programmierung von Azure-Mediendiensten. Das vorherige Thema war [Vorgehensweise: Bereitstellen von Streaming-Inhalten][].

In diesem Thema lernen Sie das Erstellen eines Locators zu Apple HTTP Live Streaming (HLS)-Inhalten auf einem Media Services-Ursprungsserver. Mit diesem Verfahren können Sie eine URL zu Apple HLS-Inhalten erstellen und Apple iOS-Geräten für die Wiedergabe bereitstellen. Die grundlegende Methode zum Erstellen der Locator-URL ist identisch. Erstellen Sie einen Locator zum Pfad des Apple HLS-Streaming-Medienobjekts auf einem Ursprungsserver, und erstellen Sie dann eine vollständige URL als Link zum Manifest für den Streaming-Inhalt.

Im folgenden Codebeispiel wird angenommen, dass Sie bereits einen Verweis auf ein HLS-Streaming-Medienobjekt abgerufen haben und dass in dem Code auf die Variable namens **assetToStream** verwiesen wird. Nachdem Sie mit diesem Code einen Ursprungs-Locator zum Medienobjekt generiert haben, können Sie die resultierende URL verwenden, um den Streaming-Inhalt auf einem iOS-Gerät wie z. B. einem iPad oder iPhone abzuspielen.

So erstellen Sie einen Locator zu Apple HLS-Streaming-Inhalten:

1.  Rufen Sie einen Verweis auf die Manifestdatei im Medienobjekt ab.
2.  Definieren Sie eine Zugriffsrichtlinie
3.  Erstellen Sie den Ursprungs-Locator durch Aufrufen der CreateLocator-Methode.
4.  Erstellen Sie eine URL zur Manifestdatei

Der folgende Code zeigt die Implementierung der Schritte:

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

Weitere Informationen zur Auslieferung von Medienobjekten finden Sie unter:

-   [Bereitstellen von Medienobjekten mit dem Media Services SDK für .NET][]
-   [Bereitstellen von Medienobjekten mit der Media Services REST-API][]

</p>
## Nächste Schritte

Dies ist das letzte Thema der Reihe zur Verwendung von Azure Media Services. Sie haben erfahren, wie Sie Ihren Computer für die Media Services-Entwicklung einrichten und typische Programmieraufgaben ausführen. Weitere Informationen zur Programmierung von Media Services finden Sie in den folgenden Ressourcen:

-   [Azure Media Services-Dokumentation][]
-   [Schnelleinstieg: Verwenden des Media Services .NET SDKs][]
-   [Erstellen von Anwendungen mit dem Media Services SDK für .NET][]
-   [Erstellen von Anwendungen mit der Azure Media Services REST-API][]
-   [Media Services-Forum][]
-   [Überwachen eines Media Services-Kontos][]
-   [Verwalten von Inhalten in Media Services][]

  [Vorgehensweise: Bereitstellen von Streaming-Inhalten]: http://go.microsoft.com/fwlink/?LinkID=301942&clcid=0x409
  [Bereitstellen von Medienobjekten mit dem Media Services SDK für .NET]: http://msdn.microsoft.com/en-us/library/jj129575.aspx
  [Bereitstellen von Medienobjekten mit der Media Services REST-API]: http://msdn.microsoft.com/en-us/library/jj129578.aspx
  [Azure Media Services-Dokumentation]: http://go.microsoft.com/fwlink/?linkid=245437
  [Schnelleinstieg: Verwenden des Media Services .NET SDKs]: http://go.microsoft.com/fwlink/?linkid=252966
  [Erstellen von Anwendungen mit dem Media Services SDK für .NET]: http://go.microsoft.com/fwlink/?linkid=247821
  [Erstellen von Anwendungen mit der Azure Media Services REST-API]: http://go.microsoft.com/fwlink/?linkid=252967
  [Media Services-Forum]: http://social.msdn.microsoft.com/Forums/en-US/MediaServices/threads
  [Überwachen eines Media Services-Kontos]: http://www.windowsazure.com/en-us/manage/services/media-services/how-to-monitor-a-media-services-account/
  [Verwalten von Inhalten in Media Services]: http://www.windowsazure.com/en-us/manage/services/media-services/how-to-manage-content-in-media-services/
