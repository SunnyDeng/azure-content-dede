<properties
	pageTitle="Importieren von Daten in Azure Search über die REST-API | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Hochladen von Daten in einen Index in Azure Search über die REST-API."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/17/2015"
	ms.author="heidist"/>

# Importieren von Daten in Azure Search über die REST-API
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Dieser Artikel beschreibt, wie Sie einen Index mithilfe der [Azure Search-REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) auffüllen. Ein Teil des folgenden Inhalts stammt aus [Hinzufügen, Aktualisieren oder Löschen von Dokumenten (Azure Search-REST-API)](https://msdn.microsoft.com/library/azure/dn798930.aspx). Mehr Kontextinformationen finden Sie im übergeordneten Artikel.

Um Dokumente mit REST-API in Ihren Index zu schieben, geben Sie POST-HTTP-Anforderungen an das URL-Endgerät Ihres Diensts aus.

**Anforderung und Anforderungsheader**:

In der URL müssen Sie Ihren Dienstnamen sowie die entsprechende API-Version (die aktuelle Version der API zum Zeitpunkt der Veröffentlichung dieses Dokuments ist „2015-02-28“) bereitstellen.

In Anforderungsheadern müssen Sie den Inhaltstyp (Content-Type) definieren und den primären Administratorschlüssel Ihres Diensts bereitstellen.

	POST https://[servicename].search.windows.net/indexes/[indexname]/docs/index?api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key]


**Anforderungstext**:


	{
		"value": [
			{
				"@search.action": "upload",
				"hotelId": "1",
				"baseRate": 199.0,
				"description": "Best hotel in town",
				"description_fr": "Meilleur hôtel en ville",
				"hotelName": "Fancy Stay",
				"category": "Luxury",
				"tags": ["pool", "view", "wifi", "concierge"],
				"parkingIncluded": false,
				"smokingAllowed": false,
				"lastRenovationDate": "2010-06-27T00:00:00Z",
				"rating": 5,
				"location": { "type": "Point", "coordinates": [-122.131577, 47.678581] }
			},
			{
				"@search.action": "upload",
				"hotelId": "2",
				"baseRate": 79.99,
				"description": "Cheapest hotel in town",
				"description_fr": "Hôtel le moins cher en ville",
				"hotelName": "Roach Motel",
				"category": "Budget",
				"tags": ["motel", "budget"],
				"parkingIncluded": true,
				"smokingAllowed": true,
				"lastRenovationDate": "1982-04-28T00:00:00Z",
				"rating": 1,
				"location": { "type": "Point", "coordinates": [-122.131577, 49.678581] }
			}
		]
	}

In diesem Fall verwenden wir „upload“ als Suchaktion. Beim Aktualisieren und Löschen vorhandener Dokumente können Sie „merge“, „mergeOrUpload“ und „delete“ verwenden.

Bei erfolgreicher Aktualisierung des Indexes wird der Statuscode „200 OK“ ausgegeben. Wenn mindestens ein Element in Ihrer Anforderung nicht erfolgreich indiziert wurde, wird der Statuscode „207“ ausgegeben.

Weitere Informationen zu Dokumentaktionen und Antworten bei Erfolg/Fehler finden Sie [auf dieser Seite](https://msdn.microsoft.com/library/azure/dn798930.aspx).

<!---HONumber=Nov15_HO4-->