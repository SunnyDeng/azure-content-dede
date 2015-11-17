<properties
	pageTitle="Erstellen eines Azure Search-Index mit .NET | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Erstellen Sie einen Index im Code mithilfe des Azure Search .NET SDK oder der Bibliothek."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="dotnet"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="11/09/2015"
	ms.author="heidist"/>

# Erstellen eines Azure Search-Index mit .NET
> [AZURE.SELECTOR]
- [Overview](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)

Dieser Artikel beschreibt, wie Sie einen Index mit dem [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx) erstellen. Der folgende Inhalt ist ein Auszug des Artikels [Verwenden von Azure Search aus einer .NET-Anwendung](search-howto-dotnet-sdk.md). Ausführliche Anweisungen finden Sie im übergeordneten Artikel.

Zu den Voraussetzungen zum Erstellen eines Index gehört das vorherige Einrichten einer Verbindung mit Ihrem Suchdienst, was üblicherweise über einen `SearchServiceClient` erfolgt. Eine bewährte Methode zur Sicherstellung einer reibungslosen erneuten Bereitstellung ist das Löschen eines vorhandenen Index mit dem gleichen Namen, wenn er bereits vorhanden ist.

Wenn es einen Index mit dem Namen „hotels“ gibt, können Sie dafür folgendermaßen eine Methode erstellen:

    private static void DeleteHotelsIndexIfExists(SearchServiceClient serviceClient)
    {
        if (serviceClient.Indexes.Exists("hotels"))
        {
            serviceClient.Indexes.Delete("hotels");
        }
    }

Diese Methode verwendet das bereitgestellte Objekt `SearchServiceClient`, um zu überprüfen, ob der Index vorhanden ist, und löscht ihn gegebenenfalls.

Um einen neuen Index namens „hotels“ zu erstellen, erstellen Sie eine Methode wie die folgende:

    private static void CreateHotelsIndex(SearchServiceClient serviceClient)
    {
        var definition = new Index()
        {
            Name = "hotels",
            Fields = new[]
            {
                new Field("hotelId", DataType.String)                       { IsKey = true },
                new Field("hotelName", DataType.String)                     { IsSearchable = true, IsFilterable = true },
                new Field("baseRate", DataType.Double)                      { IsFilterable = true, IsSortable = true },
                new Field("category", DataType.String)                      { IsSearchable = true, IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("tags", DataType.Collection(DataType.String))     { IsSearchable = true, IsFilterable = true, IsFacetable = true },
                new Field("parkingIncluded", DataType.Boolean)              { IsFilterable = true, IsFacetable = true },
                new Field("lastRenovationDate", DataType.DateTimeOffset)    { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("rating", DataType.Int32)                         { IsFilterable = true, IsSortable = true, IsFacetable = true },
                new Field("location", DataType.GeographyPoint)              { IsFilterable = true, IsSortable = true }
            }
        };

        serviceClient.Indexes.Create(definition);
    }

Diese Methode erstellt ein neues `Index`-Objekt mit einer Liste von `Field`-Objekten, die das Schema des neuen Index definiert. Jedes Feld weist einen Namen, einen Datentyp und mehrere Attribute auf, die das Suchverhalten des Felds definieren. Neben Feldern können Sie dem Index auch Wertungsprofile, Vorschläge oder CORS-Optionen hinzufügen, die im Beispiel fehlen, um es einfach zu halten. Weitere Informationen zum Indexobjekt und seinen Komponenten finden Sie in der SDK-Referenz auf [MSDN](https://msdn.microsoft.com/library/azure/microsoft.azure.search.models.index_members.aspx) sowie in der [Azure Search-REST-API-Referenz](https://msdn.microsoft.com/library/azure/dn798935.aspx).

<!---HONumber=Nov15_HO3-->