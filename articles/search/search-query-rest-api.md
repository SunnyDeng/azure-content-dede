<properties
	pageTitle="Erstellen von Abfragen in Azure Search mithilfe von Aufrufen der REST-API | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Erstellen Sie eine Suchabfrage in Azure Search, und verwenden Sie Suchparameter zum Filtern, Sortieren und Facettieren von Suchergebnissen mithilfe der .NET-Bibliothek oder des SDK."
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
	ms.date="11/10/2015"
	ms.author="heidist"/>

#Erstellen von Abfragen in Azure Search über Aufrufe der REST-API 
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Fiddler](search-fiddler.md)
- [Postman](search-chrome-postman.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

Dieser Artikel beschreibt, wie Sie eine Abfrage eines Indexes mithilfe der [ Azure Search-REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) erstellen. Ein Teil des folgenden Inhalts stammt aus [Durchsuchen von Dokumente (Azure Search-REST-API)](https://msdn.microsoft.com/library/azure/dn798927.aspx). Mehr Kontextinformationen finden Sie im übergeordneten Artikel.

Zu den Voraussetzungen für den Import gehört ein bereits vorhandener Index, in den Dokumente mit durchsuchbaren Daten geladen wurden.

Wenn Sie die REST-API verwenden, basieren Abfragen auf einer HTTP-Anforderung vom Typ GET. Codeausschnitte stammen aus dem Beispiel [Bewertungsprofile](search-get-started-scoring-profiles.md).

        static JObject ExecuteRequest(string action, string query = "")
        {
            // original:  string url = serviceUrl + indexName + "/" + action + "?" + ApiVersion; 
            string url = serviceUrl + indexName + "/docs?" + action ;
            if (!String.IsNullOrEmpty(query))
            {
                url += query + "&" + ApiVersion;
            }

            string response = ExecuteGetRequest(url);
            return JObject.Parse(response);

        }

        static string ExecuteGetRequest(string requestUri)
        {
            //This will execute a get request and return the response
            using (HttpClient client = new HttpClient())
            {
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.GetAsync(requestUri).Result;        // Searches are done over port 80 using Get
                return response.Content.ReadAsStringAsync().Result;
            }

        }

<!---HONumber=Nov15_HO3-->