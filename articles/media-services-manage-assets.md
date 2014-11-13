<properties urlDisplayName="Manage Assets in Media Services" pageTitle="Verwalten von Medienobjekten in Media Services &ndash; Azure" metaKeywords="" description="Erfahren Sie, wie Sie Inhalte in Media Services verwalten k&ouml;nnen. Sie k&ouml;nnen au&szlig;erdem Jobs, Aufgaben, Zugriffsrichtlinien, Locators usw. verwalten. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK f&uuml;r .NET." metaCanonical="" services="media-services" documentationCenter="" title="Gewusst wie: Verwalten von Medienobjekten im Speicher" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# Gewusst wie: Verwalten von Medienobjekten im Speicher

Dieser Artikel ist Teil einer Reihe zum Thema Programmierung von Azure-Mediendiensten. Das vorherige Thema war [Vorgehensweise: Schützen von Medienobjekten][Vorgehensweise: Schützen von Medienobjekten].

Nachdem Sie Medienobjekte erstellt und nach Media Services hochgeladen haben, können Sie auf die Medienobjekte auf dem Server zugreifen und sie dort verwalten. Sie können auch andere Objekte auf dem Server verwalten, die Teil von Media Services sind, darunter Jobs, Aufgaben, Zugriffsrichtlinien, Locators usw.

Das folgende Beispiel zeigt, wie Sie ein Medienobjekt nach dessen ID abfragen können.

    static IAsset GetAsset(string assetId){ // Use a LINQ Select query to get an asset. var assetInstance = from a in _context.Assets where a.Id == assetId select a; // Reference the asset as an IAsset. IAsset asset = assetInstance.FirstOrDefault();
    return asset;

<p>
}
</code>

</pre>
</p>
Mit der folgenden Methode können Sie die Sammlung der Medienobjekte durchlaufen, alle auf dem Server verfügbaren Medienobjekte auflisten und Details der einzelnen Medienobjekte anzeigen.

    static void ListAssets(){ string waitMessage = "Building the list. This may take a few " + "seconds to a few minutes depending on how many assets " + "you have." + Environment.NewLine + Environment.NewLine + "Please wait..." + Environment.NewLine; Console.Write(waitMessage);
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

<p>
}
</code>

</pre>
Der folgende Codeausschnitt löscht alle Medienobjekte aus dem Media Services-Konto.

    foreach (IAsset asset in _context.Assets){ asset.Delete();}

</p>
Weitere Informationen zur Verwaltung von Medienobjekten finden Sie unter:

-   [Verwalten von Medienobjekten mit dem Media Services SDK für .NET][Verwalten von Medienobjekten mit dem Media Services SDK für .NET]
-   [Verwalten von Medienobjekten mit der Media Services REST-API][Verwalten von Medienobjekten mit der Media Services REST-API]

</p>
## Nächste Schritte

Da Sie jetzt wissen, wie Medienobjekte verwaltet werden, wechseln Sie zum Thema [Bereitstellen eines Medienobjekts durch Herunterladen][Bereitstellen eines Medienobjekts durch Herunterladen].

  [Vorgehensweise: Schützen von Medienobjekten]: ../media-services-protect-asset/
  [Verwalten von Medienobjekten mit dem Media Services SDK für .NET]: http://msdn.microsoft.com/de-de/library/jj129589.aspx
  [Verwalten von Medienobjekten mit der Media Services REST-API]: http://msdn.microsoft.com/de-de/library/jj129583.aspx
  [Bereitstellen eines Medienobjekts durch Herunterladen]: ../media-services-deliver-asset-download/
