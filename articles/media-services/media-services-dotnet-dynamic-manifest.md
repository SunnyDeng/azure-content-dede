<properties 
	pageTitle="Erstellen von Filtern mit dem Media Services-.NET-SDK" 
	description="In diesem Thema wird erläutert, wie Sie Filter erstellen, mit denen Ihre Kunden bestimmte Abschnitte eines Streams streamen können. Media Services erstellt dynamische Manifeste, um dieses selektive Streaming zu erreichen." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ne" 
	ms.topic="article" 
	ms.date="09/07/2015"  
	ms.author="juliako"/>


#Erstellen von Filtern mit dem Media Services-.NET-SDK

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-dynamic-manifest.md)
- [REST](media-services-rest-dynamic-manifest.md)

Ab Version 2.11 können Sie mit Media Services Filter für Ihre Medienobjekte definieren. Diese Filter sind serverseitige Regeln, mit denen Ihre Kunden verschiedene Aktionen ausführen können, z. B. Wiedergabe bestimmter Videoabschnitte (anstelle des gesamten Videos). Sie können zudem nur eine Teilmenge von Audio- und Videowiedergaben (anstelle von allen mit dem Medienobjekt verknüpften Wiedergaben) angeben, die für das Gerät eines Kunden geeignet sind. Diese Filterung der Medienobjekte erfolgt über **dynamische Manifeste**, die auf Anfrage des Kunden zum Streamen von Videos basierend auf bestimmten Filtern erstellt werden.

Ausführlichere Informationen zu Filtern und dynamischen Manifesten finden Sie in der [Übersicht über dynamische Manifeste](media-services-dynamic-manifest-overview.md).

In diesem Thema wird die Verwendung des Media Services-.NET-SDKs zum Erstellen, Aktualisieren und Löschen von Filtern erläutert.


Beachten Sie, dass es beim Aktualisieren eines Filters bis zu 2 Minuten dauern kann, bis die Regeln am Streamingendpunkt aktualisiert wurden. Wenn der Inhalt mit diesem Filter verarbeitet (und in Proxys und CDN-Caches zwischengespeichert) wurde, können durch Aktualisieren des Filters Player-Fehler auftreten. Es wird empfohlen, den Cache nach dem Aktualisieren des Filters zu leeren. Wenn dies nicht möglich ist, empfiehlt sich die Verwendung eines anderen Filters.

##Verwendete Typen zum Erstellen von Filtern

Die folgenden Typen werden beim Erstellen von Filtern verwendet:

- **IStreamingFilter**. Dieser Typ basiert auf der folgenden REST-API [Filter](http://msdn.microsoft.com/library/azure/mt149056.aspx).
- **IStreamingAssetFilter**. Dieser Typ basiert auf der folgenden REST-API [AssetFilter](http://msdn.microsoft.com/library/azure/mt149053.aspx).
- **PresentationTimeRange**. Dieser Typ basiert auf dem folgenden REST-API [PresentationTimeRange](http://msdn.microsoft.com/library/azure/mt149052.aspx).
- **FilterTrackSelectStatement** und **IFilterTrackPropertyCondition**. Diese Typen basieren auf den folgenden REST-APIs [FilterTrackSelect und FilterTrackPropertyCondition](http://msdn.microsoft.com/library/azure/mt149055.aspx).


##Erstellen/Aktualisieren/Lesen/Löschen globaler Filter

Der folgende Code zeigt, wie Sie mithilfe von .NET Asset-Filter erstellen, aktualisieren, lesen und löschen.
	
	string filterName = "GlobalFilter_" + Guid.NewGuid().ToString();
	            
	List<FilterTrackSelectStatement> filterTrackSelectStatements = new List<FilterTrackSelectStatement>();
	
	FilterTrackSelectStatement filterTrackSelectStatement = new FilterTrackSelectStatement();
	filterTrackSelectStatement.PropertyConditions = new List<IFilterTrackPropertyCondition>();
	filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackNameCondition("Track Name", FilterTrackCompareOperator.NotEqual));
	filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackBitrateRangeCondition(new FilterTrackBitrateRange(0, 1), FilterTrackCompareOperator.NotEqual));
	filterTrackSelectStatement.PropertyConditions.Add(new FilterTrackTypeCondition(FilterTrackType.Audio, FilterTrackCompareOperator.NotEqual));
	filterTrackSelectStatements.Add(filterTrackSelectStatement);
	
	// Create
	IStreamingFilter filter = _context.Filters.Create(filterName, new PresentationTimeRange(), filterTrackSelectStatements);
	
	// Update
	filter.PresentationTimeRange = new PresentationTimeRange(timescale: 500);
	filter.Update();
	
	// Read
	var filterUpdated = _context.Filters.FirstOrDefault();
	Console.WriteLine(filterUpdated.Name);

	// Delete
	filter.Delete();


##Erstellen/Aktualisieren/Lesen/Löschen von Asset-Filtern

Der folgende Code zeigt, wie Sie mithilfe von .NET Asset-Filter erstellen, aktualisieren, lesen und löschen.

	
	string assetName = "AssetFilter_" + Guid.NewGuid().ToString();
	var asset = _context.Assets.Create(assetName, AssetCreationOptions.None);
	
	string filterName = "AssetFilter_" + Guid.NewGuid().ToString();
	
	    
	// Create
	IStreamingAssetFilter filter = asset.AssetFilters.Create(filterName,
	                                    new PresentationTimeRange(), 
	                                    new List<FilterTrackSelectStatement>());
	
	// Update
	filter.PresentationTimeRange = 
	        new PresentationTimeRange(start: 6000000000, end: 72000000000);
	
	filter.Update();
	
	// Read
	asset = _context.Assets.Where(c => c.Id == asset.Id).FirstOrDefault();
	var filterUpdated = asset.AssetFilters.FirstOrDefault();
	Console.WriteLine(filterUpdated.Name);
	
	// Delete
	filterUpdated.Delete();
	



##Erstellen von Streaming-URLs, die Filter verwenden

Informationen zum Veröffentlichen und Bereitstellen Ihrer Medienobjekte finden Sie unter [Bereitstellen von Inhalten für Kunden – Übersicht](media-services-deliver-content-overview.md).


In den folgenden Beispielen sehen Sie, wie Sie Ihren Streaming-URLs Filter hinzufügen können.

**MPEG DASH**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=mpd-time-csf, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V4**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl, filter=MyFilter)

**Apple HTTP Live Streaming (HLS) V3**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=m3u8-aapl-v3, filter=MyFilter)

**Smooth Streaming**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(filter=MyFilter)


**HDS**

	http://testendpoint-testaccount.streaming.mediaservices.windows.net/fecebb23-46f6-490d-8b70-203e86b0df58/BigBuckBunny.ism/Manifest(format=f4m-f4f, filter=MyFilter)



##Media Services-Lernpfade

Sie können sich die AMS-Lernpfade hier ansehen:

- [Media Services - Live Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-live/) (in englischer Sprache)
- [Media Services - on Demand Streaming](http://azure.microsoft.com/documentation/learning-paths/media-services-streaming-on-demand/) (in englischer Sprache)

##Weitere Informationen 

[Übersicht über dynamische Manifeste](media-services-dynamic-manifest-overview.md)
 

<!---HONumber=Sept15_HO2-->