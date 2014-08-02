<properties linkid="develop-media-services-how-to-guides-deliver-apple-live-streaming" urlDisplayName="Deliver Apple HTTP Live Streaming (HLS)" pageTitle="How to Deliver Apple HTTP Live Streaming (HLS) - Azure" metaKeywords="" description="Learn how to create a locator to Apple HTTP Live Stream (HLS) content on Media Services origin server. Code samples are written in C# and use the Media Services SDK for .NET." metaCanonical="" services="media-services" documentationCenter="" title="How to: Deliver Apple HLS streaming content" authors="migree" solutions="" manager="" editor="" />

Vorgehensweise: Bereitstellen von Apple HLS-Streaming-Inhalten
==============================================================

Dieser Artikel ist Teil einer Reihe zum Thema Programmierung von Azure Media Services. Das vorherige Thema war [Vorgehensweise: Bereitstellen von Streaming-Inhalten](http://go.microsoft.com/fwlink/?LinkID=301942&clcid=0x409).

In diesem Thema lernen Sie das Erstellen eines Locators zu Apple HTTP Live Streaming (HLS)-Inhalten auf einem Media Services-Ursprungsserver. Mit diesem Verfahren können Sie eine URL zu Apple HLS-Inhalten erstellen und Apple iOS-Geräten für die Wiedergabe bereitstellen. Die grundlegende Methode zum Erstellen der Locator-URL ist identisch. Erstellen Sie einen Locator zum Pfad des Apple HLS-Streaming-Medienobjekts auf einem Ursprungsserver, und erstellen Sie dann eine vollständige URL als Link zum Manifest für den Streaming-Inhalt.

Im folgenden Codebeispiel wird angenommen, dass Sie bereits einen Verweis auf ein HLS-Streaming-Medienobjekt abgerufen haben und dass in dem Code auf die Variable namens **assetToStream** verwiesen wird. Nachdem Sie mit diesem Code einen Ursprungs-Locator zum Medienobjekt generiert haben, können Sie die resultierende URL verwenden, um den Streaming-Inhalt auf einem iOS-Gerät wie z. B. einem iPad oder iPhone abzuspielen.

So erstellen Sie einen Locator zu Apple HLS-Streaming-Inhalten:

1.  Rufen Sie einen Verweis auf die Manifestdatei im Medienobjekt ab.
2.  Definieren Sie eine Zugriffsrichtlinie.
3.  Erstellen Sie den Ursprungs-Locator durch Aufrufen der CreateLocator-Methode.
4.  Erstellen Sie eine URL zur Manifestdatei.

Der folgende Code zeigt, wie diese Schritte implementiert werden:

<pre><code>
static ILocator GetStreamingHLSOriginLocator( string targetAssetID)
{
    // Verweis auf das Medienobjekt abrufen, das Sie streamen möchten.
    IAsset assetToStream = GetAsset(targetAssetID);

    // Verweis auf die HLS-Streaming-Manifestdatei aus der Sammlung  
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
    policy = _context.AccessPolicies.Create("Streaming HLS Policy",
        TimeSpan.FromDays(30),
        AccessPermissions.Read);

    originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, assetToStream,
                policy,
                DateTime.UtcNow.AddMinutes(-5));

    // Eine URL zur HLS-Streaming-Manifestdatei erstellen. Diese URL zum Abspielen
    // auf Apple iOS-Streaming-Clients verwenden.
    string urlForClientStreaming = originLocator.Path
        + manifestFile.Name + "/manifest(format=m3u8-aapl)";
    Console.WriteLine("URL to manifest for client streaming: ");
    Console.WriteLine(urlForClientStreaming);
    Console.WriteLine();

    // Den Locator zurückgeben. 
    return originLocator;
}
</code></pre>

Weitere Informationen zum Bereitstellen von Medienobjekten finden Sie unter:

-   [Bereitstellen von Medienobjekten mit dem Media Services SDK für .NET](http://msdn.microsoft.com/en-us/library/jj129575.aspx)
-   [Bereitstellen von Medienobjekten mit der Media Services REST-API](http://msdn.microsoft.com/en-us/library/jj129578.aspx)

Nächste Schritte
----------------

Dies ist das letzte Thema der Reihe zur Verwendung von Azure Media Services. Sie haben erfahren, wie Sie Ihren Computer für die Media Services-Entwicklung einrichten und typische Programmieraufgaben ausführen. Weitere Informationen zur Programmierung von Media Services finden Sie in den folgenden Ressourcen:

-   [Azure Media Services-Dokumentation](http://go.microsoft.com/fwlink/?linkid=245437)
-   [Schnelleinstieg: Verwenden des Media Services .NET SDKs](http://go.microsoft.com/fwlink/?linkid=252966)
-   [Erstellen von Anwendungen mit dem Media Services SDK für .NET](http://go.microsoft.com/fwlink/?linkid=247821)
-   [Erstellen von Anwendungen mit der Azure Media Services REST-API](http://go.microsoft.com/fwlink/?linkid=252967)
-   [Media Services-Forum](http://social.msdn.microsoft.com/Forums/en-US/MediaServices/threads)
-   [Überwachen eines Media Services-Kontos](http://www.windowsazure.com/en-us/manage/services/media-services/how-to-monitor-a-media-services-account/)
-   [Verwalten von Inhalten in Media Services](http://www.windowsazure.com/en-us/manage/services/media-services/how-to-manage-content-in-media-services/)

