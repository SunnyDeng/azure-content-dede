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
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Importieren von Daten in Azure Search über die REST-API
> [AZURE.SELECTOR]
- [Overview](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexers](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)

Dieser Artikel beschreibt, wie Sie einen Index mithilfe der [Azure Search-REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) auffüllen. Ein Teil des folgenden Inhalts stammt aus [Hinzufügen, Aktualisieren oder Löschen von Dokumenten] [Azure Search REST-API] (https://msdn.microsoft.com/library/azure/dn798930.aspx)). Mehr Kontextinformationen finden Sie im übergeordneten Artikel.

Eine der Voraussetzungen für den Import ist ein bereits vorhandener Index zum Empfangen der Daten.

Wenn Sie die REST-API verwenden, basiert die Erfassung von Daten auf einer HTTP-Anforderung vom Typ PUT oder POST. Codeausschnitte stammen aus dem Beispiel [Bewertungsprofile](search-get-started-scoring-profiles.md).

        static bool PostDocuments(string fileName)
        {
            // Add some documents to the newly created index
            Uri requestUri = new Uri(serviceUrl + indexName + "/docs/index?" + ApiVersion);

            // Load the json containing the data from an external file
            string json = File.ReadAllText(fileName);

            using (HttpClient client = new HttpClient())
            {
                // Create the index
                client.DefaultRequestHeaders.Add("api-key", primaryKey);
                HttpResponseMessage response = client.PostAsync(requestUri,       // To add data use POST
                    new StringContent(json, Encoding.UTF8, "application/json")).Result;

                if (response.StatusCode == HttpStatusCode.OK)
                {
                    Console.WriteLine("Documents posted from file {0}. \r\n", fileName);
                    return true;
                }

                Console.WriteLine("Documents failed to upload: {0} {1} \r\n", (int)response.StatusCode, response.Content.ReadAsStringAsync().Result.ToString());
                return false;

            }
        }

<!---HONumber=Nov15_HO3-->