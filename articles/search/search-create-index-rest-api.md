<properties
	pageTitle="Erstellen eines Azure Search-Index mit einer REST-API | Microsoft Azure | Gehosteter Cloudsuchdienst"
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
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Erstellen eines Azure Search-Index mit einer REST-API
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Dieser Artikel beschreibt, wie Sie einen Index mit der [Azure Search REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) erstellen. Ein Teil des folgenden Inhalts stammt aus [Erstellen eines Index (Azure Search REST-API)](https://msdn.microsoft.com/library/azure/dn798941.aspx). Mehr Kontextinformationen finden Sie im übergeordneten Artikel.

Zu den Voraussetzungen zum Erstellen eines Index gehört das vorherige Einrichten einer Verbindung mit Ihrem Suchdienst, was üblicherweise über „httpClient“ erfolgt.

Ein dreiteiliger Ansatz zum Erstellen eines Index über die REST-API umfasst das Einrichten einer Verbindung, das Bereitstellen eines Indexschemas und das anschließende Ausführen eines Befehls, der den Index erstellt. Codeausschnitte stammen aus dem Beispiel [Bewertungsprofile](search-get-started-scoring-profiles.md).

##Definieren des Indexschemas

Die REST-API akzeptiert Anforderungen im JSON-Format. Eine Möglichkeit zum Erstellen des Schemas ist die Einbindung einer eigenständigen Schemadatei als JSON-Dokument. Das folgende Beispiel bietet eine Erläuterung.

In dem Beispiel, aus dem dieser Codeausschnitt stammt, wird der unten aufgeführte JSON-Code in einer Datei namens „schema.json“ gespeichert.

	{
	    "name": "musicstoreindex",
	    "fields": [
	        { "name": "key", "type": "Edm.String", "key": true },
	        { "name": "albumTitle", "type": "Edm.String"},
	        { "name": "albumUrl", "type": "Edm.String", "filterable": false },
	        { "name": "genre", "type": "Edm.String" },
	        { "name": "genreDescription", "type": "Edm.String", "filterable": false },
	        { "name": "artistName", "type": "Edm.String"},
	        { "name": "orderableOnline", "type": "Edm.Boolean" },
	        { "name": "rating", "type": "Edm.Int32" },
	        { "name": "tags", "type": "Collection(Edm.String)" },
	        { "name": "price", "type": "Edm.Double", "filterable": false },
	        { "name": "margin", "type": "Edm.Int32", "retrievable": false },
	        { "name": "inventory", "type": "Edm.Int32" },
	        { "name": "lastUpdated", "type": "Edm.DateTimeOffset" }
	    ],
	    "scoringProfiles": [
	        {
	            "name": "boostGenre",
	            "text": {
	                "weights": {
	                    "albumTitle": 5,
	                    "genre": 50,
	                    "artistName": 5
	                }
	            }
	        },
	        {
	            "name": "newAndHighlyRated",
	            "functions": [
	                {
	                    "type": "freshness",
	                    "fieldName": "lastUpdated",
	                    "boost": 10,
	                    "interpolation": "quadratic",
	                    "freshness": {
	                        "boostingDuration": "P365D"
	                    }
	                },
	                {
	                    "type": "magnitude",
	                    "fieldName": "rating",
	                    "boost": 10,
	                    "interpolation": "linear",
	                    "magnitude": {
	                        "boostingRangeStart": 1,
	                        "boostingRangeEnd": 5,
	                        "constantBoostBeyondRange": false
	                    }
	                }
	            ]
	        },
	        {
	            "name": "boostMargin",
	            "functions": [
	
	                {
	                    "type": "magnitude",
	                    "fieldName": "margin",
	                    "boost": 50,
	                    "interpolation": "linear",
	                    "magnitude": {
	                        "boostingRangeStart": 50,
	                        "boostingRangeEnd": 100,
	                        "constantBoostBeyondRange": false
	                    }
	                }
	            ]
	        }
	    ]
	}

##Einrichten einer HTTP-Anforderung zum Herstellen einer Verbindung und zum Erstellen des Index

REST-APIs enthalten in jeder Anforderung die HTTP-Verbindung. Jede Anforderung legt immer die Dienst-URL, die verwendete API-Version und einen Administratorschlüssel fest, der Lese-/ Schreibvorgänge im Dienst ermöglicht (er wird im Folgenden als Primärschlüssel bezeichnet, da der Schlüssel im Portal so heißt).

Der Codeausschnitt verwendet statische Werte als Eingaben, einschließlich eines Namens für den Index, wie in der Datei „app.config“ (nicht dargestellt) angegeben. Das Beispiel wurde so erstellt, um den Code einfach zu halten.

        private const string ApiVersion = "api-version=2015-02-28";
        private static string serviceUrl = ConfigurationManager.AppSettings["serviceUrl"];
        private static string indexName = ConfigurationManager.AppSettings["indexName"];
        private static string primaryKey = ConfigurationManager.AppSettings["primaryKey"];

Im folgenden Beispiel sehen Sie eine HTTP PUT-Anforderung an die Dienst-URL zusammen mit `api-version` und dem Namen des zu erstellenden Index.

        static bool CreateIndex()
        {
            // Create an index using an index name
            Uri requestUri = new Uri(serviceUrl + indexName + "?" + ApiVersion);

            // Load the json containing the schema from an external file
            string json = File.ReadAllText("schema.json");

            using (HttpClient client = new HttpClient())
            {
                // Create the index
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.PutAsync(requestUri,        // To create index use PUT
                    new StringContent(json, Encoding.UTF8, "application/json")).Result;

                if (response.StatusCode == HttpStatusCode.Created)   // For Posts we want to know response had no content
                {
                    Console.WriteLine("Index created. \r\n");
                    return true;
                }

                Console.WriteLine("Index creation failed: {0} {1} \r\n", (int)response.StatusCode, response.Content.ReadAsStringAsync().Result.ToString());
                return false;

            }
        }

<!---HONumber=Nov15_HO3-->