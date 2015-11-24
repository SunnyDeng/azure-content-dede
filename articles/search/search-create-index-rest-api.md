<properties
	pageTitle="Erstellen eines Azure Search-Indexes mit einer REST-API | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Erstellen Sie einen Index im Code mit Azure Search und einer HTTP-REST-API."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/17/2015"
	ms.author="heidist"/>

# Erstellen eines Azure Search-Indexes mit einer REST-API
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Dieser Artikel beschreibt, wie Sie einen Index mit der [Azure Search-REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) erstellen. Ein Teil des folgenden Inhalts stammt aus [Index erstellen (Azure Search-REST-API)](https://msdn.microsoft.com/library/azure/dn798941.aspx). Mehr Kontextinformationen finden Sie im übergeordneten Artikel.

Zu den Voraussetzungen zum Erstellen eines Indexes gehört das vorherige Einrichten einer Verbindung mit Ihrem Suchdienst, was üblicherweise über „httpClient“ erfolgt.

## Definieren des Indexschemas

Zum Erstellen eines Indexes mithilfe der REST-API können Sie eine POST-Anforderung an Ihr Azure Search-URL-Endgerät ausgeben. Sie definieren die Struktur des Indexes mithilfe von JSON im Text der Anforderung.

**Anforderung und Anforderungsheader**:

Im folgenden Beispiel müssen Sie das URL-Endgerät Ihres Diensts verwenden, insbesondere Ihren Dienstnamen und die richtige API-Version (die aktuelle Version der API zum Zeitpunkt der Veröffentlichung dieses Dokuments ist „2015-02-28“). In den Anforderungsheadern müssen Sie außerdem den primären Administratorschlüssel Ihres Diensts bereitstellen, den Sie beim [Erstellen eines Diensts](https://msdn.microsoft.com/library/azure/dn798941.aspx/) empfangen haben.

	POST https://[servicename].search.windows.net/indexes?api-version=2015-02-28
	Content-Type: application/JSON
	api-key:[primary admin key]


**Anforderungstext**:

Hier geben Sie Ihren Indexnamen an (in diesem Fall „hotels“) sowie [Namen, Typen und Attribute der Felder](https://msdn.microsoft.com/library/azure/dn798941.aspx).

	{
		"name": "hotels",  
		"fields": [
			{"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false},
			{"name": "baseRate", "type": "Edm.Double"},
			{"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
			{"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, analyzer:"fr.lucene"},
			{"name": "hotelName", "type": "Edm.String"},
			{"name": "category", "type": "Edm.String"},
			{"name": "tags", "type": "Collection(Edm.String)"},
			{"name": "parkingIncluded", "type": "Edm.Boolean"},
			{"name": "smokingAllowed", "type": "Edm.Boolean"},
			{"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
			{"name": "rating", "type": "Edm.Int32"},
			{"name": "location", "type": "Edm.GeographyPoint"}
		]
	}

Bei einer erfolgreichen Anforderung sollte der Statuscode „201 Created“ angezeigt werden. Weitere Informationen zum Erstellen eines Indexes über die REST-API finden Sie [auf dieser Seite](https://msdn.microsoft.com/library/azure/dn798941.aspx).

<!---HONumber=Nov15_HO4-->