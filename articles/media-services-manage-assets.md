<properties pageTitle="Verwalten von Medienobjekten in Media Services - Azure" description="Erfahren Sie, wie Sie Inhalte in Media Services verwalten können. Sie können außerdem Jobs, Aufgaben, Zugriffsrichtlinien, Locators usw. verwalten. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK für .NET." services="media-services" documentationCenter="" authors="juliako" manager="dwrede" editor=""/>

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="10/30/2014" ms.author="juliako"/>




<h1>Gewusst wie: Verwalten von Medienobjekten im Speicher</h1>

Dieser Artikel ist Teil einer Reihe zum Thema Programmierung von Azure-Mediendiensten. Das vorherige Thema war [Vorgehensweise: Schützen von Medienobjekten](../media-services-protect-asset/).

Nachdem Sie Medienobjekte erstellt und nach Media Services hochgeladen haben, können Sie auf die Medienobjekte auf dem Server zugreifen und sie dort verwalten. Sie können auch andere Objekte auf dem Server verwalten, die Teil von Media Services sind, darunter Jobs, Aufgaben, Zugriffsrichtlinien, Locators usw.

Das folgende Beispiel zeigt das Abfragen eines Medienobjekts nach assetid. 
<pre><code>
static IAsset GetAsset(string assetId)
{
    // Use a LINQ Select query to get an asset.
    var assetInstance =
        from a in _context.Assets
        where a.Id == assetId
        select a;
    // Reference the asset as an IAsset.
    IAsset asset = assetInstance.FirstOrDefault();

    return asset;
}
</code></pre> 

Um alle auf dem Server verfügbaren Medienobjekte aufzulisten, können Sie die folgende Methode verwenden, die die Medienobjektsammlung durchläuft und Details zu den einzelnen Medienobjekten anzeigt.
<pre><code> 
static void ListAssets()
{
    string waitMessage = "Building the list. This may take a few "
        + "seconds to a few minutes depending on how many assets "
        + "you have."
        + Environment.NewLine + Environment.NewLine
        + "Please wait..."
        + Environment.NewLine;
    Console.Write(waitMessage);

    // Create a Stringbuilder to store the list that we build. 
    StringBuilder builder = new StringBuilder();

    foreach (IAsset asset in _context.Assets)
    {
        // Display the collection of assets.
        builder.AppendLine("");
        builder.AppendLine("******ASSET******");
        builder.AppendLine("Asset ID: " + asset.Id);
        builder.AppendLine("Name: " + asset.Name);
        builder.AppendLine("==============");
        builder.AppendLine("******ASSET FILES******");

        // Display the files associated with each asset. 
        foreach (IAssetFile fileItem in asset.AssetFiles)
        {
            builder.AppendLine("Name: " + fileItem.Name);
            builder.AppendLine("Size: " + fileItem.ContentFileSize);
            builder.AppendLine("==============");
        }
    }

    // Display output in console.
    Console.Write(builder.ToString());
}
</code></pre>
Der folgende Codeausschnitt löscht alle Medienobjekte aus dem Media Services-Konto.
<pre><code>
foreach (IAsset asset in _context.Assets)
{
    asset.Delete();
}
</code></pre>

Weitere Informationen zum Verwalten von Medienobjekten finden Sie unter:
<ul>
<li><a href="http://msdn.microsoft.com/de-de/library/jj129589.aspx">Verwalten von Medienobjekten mit dem Media Services SDK für .NET</a></li>
<li><a href="http://msdn.microsoft.com/de-de/library/jj129583.aspx">Verwalten von Medienobjekten mit der Media Services REST-API</a></li></ul>


<h2>Nächste Schritte</h2>
Da Sie jetzt wissen, wie Medienobjekte verwaltet werden, wechseln Sie zum Thema [Bereitstellen eines Medienobjekts durch Herunterladen](../media-services-deliver-asset-download/).

<!--HONumber=42-->
