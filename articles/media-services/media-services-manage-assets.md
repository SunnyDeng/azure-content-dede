<properties 
	pageTitle="Verwalten von Medienobjekten in Media Services" 
	description="Erfahren Sie, wie Sie Inhalte in Media Services verwalten können. Sie können außerdem Jobs, Aufgaben, Zugriffsrichtlinien, Locators usw. verwalten. Die Codebeispiele sind in C# geschrieben und verwenden das Media Services SDK für .NET." 
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
	ms.date="05/24/2015" 
	ms.author="juliako"/>


#Gewusst wie: Verwalten von Medienobjekten im Speicher

Dieser Artikel gehört zur Reihe [Media Services: Video-on-Demand-Workflow](media-services-video-on-demand-workflow.md) und [Media Services: Livestreaming-Workflow](media-services-live-streaming-workflow.md).


Nachdem Sie Medienobjekte erstellt haben, können Sie auf dem Server auf Medienobjekte zugreifen und sie dort verwalten. Sie können auch andere Objekte auf dem Server verwalten, die Teil von Media Services sind, darunter Aufträge, Aufgaben, Zugriffsrichtlinien, Locators usw.

Das folgende Beispiel zeigt das Abfragen eines Medienobjekts nach assetid.

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

Um alle auf dem Server verfügbaren Medienobjekte aufzulisten, können Sie die folgende Methode verwenden, die die Medienobjektsammlung durchläuft und Details zu den einzelnen Medienobjekten anzeigt.
	
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

Der folgende Codeausschnitt löscht alle Medienobjekte aus dem Media Services-Konto. Wenn ein Medienobjekt mit einem Programm verknüpft ist, müssen Sie das Programm zunächst löschen.

	foreach (IAsset asset in _context.Assets)
	{
	    asset.Delete();
	}

 

<!---HONumber=July15_HO4-->