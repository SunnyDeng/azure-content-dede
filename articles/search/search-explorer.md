<properties
    pageTitle="Abfragen des Azure Search-Indexes mit dem Azure-Portal | Microsoft Azure | Gehosteter Cloudsuchdienst"
    description="Eingeben einer Suchabfrage in den Suchexplorer im Azure-Portal."
    services="search"
    documentationCenter=""
	authors="ashmaka"
/>

<tags
    ms.service="search"
    ms.devlang="NA"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="03/10/2016"
    ms.author="ashmaka"
/>
# Abfragen des Azure Search-Indexes mit dem Azure-Portal
> [AZURE.SELECTOR]
- [Übersicht](search-query-overview.md)
- [Portal](search-explorer.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

In diesem Leitfaden erfahren Sie, wie Sie Ihren Azure Search-Index im Azure-Portal abfragen.

[Erstellen Sie einen Azure Search-Index](search-what-is-an-index.md) und [füllen Sie ihn mit Daten](search-what-is-data-import.md), bevor Sie mit dieser exemplarischen Vorgehensweise beginnen.

## I. Wechseln Sie zu Ihrem Azure Search-Blatt.
1. Klicken Sie auf im Menü auf der linken Seite des [Azure-Portals](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices) auf „Alle Ressourcen“.
2. Wählen Sie Ihren Azure Search-Dienst aus.

## II. Wählen Sie den Index aus, den Sie durchsuchen möchten.
1. Wählen Sie in der Kachel „Indizes“ den Index aus, den Sie durchsuchen möchten.

![](./media/search-explorer/pick-index.png)

## III. Klicken Sie auf die Kachel „Suchexplorer“.
![](./media/search-explorer/search-explorer-tile.png)

## III. Beginnen Sie mit der Suche.
1. Beginnen Sie mit einer Texteingabe in das Feld „*Abfragezeichenfolge*“, und drücken Sie danach auf „**Suchen**“.
 * Wenn Sie den Suchexplorer verwenden, können Sie alle beliebigen [Abfrageparameter](https://msdn.microsoft.com/library/dn798927.aspx) festlegen.

2. Im Abschnitt „*Ergebnisse*“ werden die Abfrageergebnisse im unformatierten JSON-Format angezeigt, das Sie bei Suchanfragen an die Azure Search REST-API in einem HTML-Antworttext erhalten würden.
3. Die Abfragezeichenfolge wird automatisch in die richtige Anforderungs-URL gelesen, um eine HTTP-Anforderung für die Azure Search REST-API zu übermitteln.

![](./media/search-explorer/search-bar.png)

<!---HONumber=AcomDC_0316_2016-->