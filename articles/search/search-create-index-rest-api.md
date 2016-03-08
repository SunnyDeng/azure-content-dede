<properties
    pageTitle="Erstellen eines Azure Search-Indexes mit einer REST-API | Microsoft Azure | Gehosteter Cloudsuchdienst"
    description="Erstellen Sie einen Index im Code mithilfe der Azure Search HTTP-REST-API."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager=""
    editor=""
    tags="azure-portal"/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="02/29/2016"
    ms.author="ashmaka"/>

# Erstellen eines Azure Search-Indexes mit der REST-API
> [AZURE.SELECTOR]
- [Übersicht](search-what-is-an-index.md)
- [Portal](search-create-index-portal.md)
- [.NET](search-create-index-dotnet.md)
- [REST](search-create-index-rest-api.md)


In diesem Artikel lernen Sie, wie Sie einen Azure Search-[Index](https://msdn.microsoft.com/library/azure/dn798941.aspx) mithilfe der [Azure Search REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) erstellen. Zum Erstellen des Azure Search-Indexes mithilfe der REST-API können Sie eine einzelne HTTP POST-Anforderung an den URL-Endpunkt Ihres Azure Search-Diensts ausgeben. Ihre Indexdefinition ist im Anforderungstext als richtig formatierter JSON-Inhalt enthalten.

Bevor Sie dieser Anleitung folgen und einen Index erstellen, müssen Sie einen [Azure Search-Dienst erstellen](search-create-service-portal.md).

## I. Identifizieren des Admin-API-Schlüssels Ihres Azure Search-Diensts
Nachdem Sie einen Azure Search-Dienst bereitgestellt haben, können Sie HTTP-Anforderungen für den URL-Endpunkt Ihres Diensts mithilfe der REST-API ausgeben. Allerdings müssen *alle* API-Anforderungen den API-Schlüssel enthalten, der für den bereitgestellten Suchdienst erstellt wurde. Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, um die API-Schlüssel für Ihren Dienst zu suchen.
2. Wechseln Sie zum Blatt Ihres Azure Search-Diensts.
3. Klicken Sie auf das Schlüsselsymbol.

Der Dienst enthält *Admin-Schlüssel* und *Abfrageschlüssel*.
  * Die primären und sekundären *Admin-Schlüssel* gewähren Ihnen Vollzugriff auf alle Vorgänge. Dazu zählen die Dienstverwaltung und das Erstellen und Löschen von Indizes, Indexern und Datenquellen. Ihnen stehen zwei Schlüssel zur Verfügung, damit Sie den sekundären Schlüssel weiterhin nutzen können, wenn Sie den primären Schlüssel neu generieren möchten, und umgekehrt.
  * Die *Abfrageschlüssel* gewähren Ihnen Lesezugriff auf Indizes und Dokumente. Diese werden in der Regel auf Clientanwendungen verteilt, die Suchanfragen ausgeben.

Verwenden Sie zum Erstellen eines Indexes entweder den primären oder den sekundären Admin-Schlüssel.

## II. Definieren des Azure Search-Indexes mithilfe richtig formatierter JSON
Eine einzelne HTTP POST-Anforderung an Ihren Dienst erstellt den Index. Der Hauptteil der HTTP POST-Anforderung enthält ein einzelnes JSON-Objekt, das den Azure Search-Index definiert.
1. Die erste Eigenschaft des JSON-Objekts ist der Name des Indexes.
2. Die zweite Eigenschaft des JSON-Objekts ist ein JSON-Array mit dem Namen `fields`, das ein separates JSON-Objekt für jedes Feld im Index enthält. Diese JSON-Objekte enthalten mehrere Name-Wert-Paare für jedes Feldattribut, einschließlich Name, Typ usw.

Berücksichtigen Sie beim Gestalten des Indexes die Benutzerfreundlichkeit und die geschäftlichen Anforderungen, da jedem Feld die [richtigen Attribute](https://msdn.microsoft.com/library/azure/dn798941.aspx) zugewiesen sein müssen. Diese Attribute steuern, welche Suchfunktionen (Filtern, Facettierung, Sortieren der Volltextsuche usw.) für welche Felder gelten. Für jedes nicht festgelegte Attribut ist die entsprechende Suchfunktion standardmäßig aktiviert, sofern Sie sie nicht explizit deaktivieren.

In unserem Beispiel hat der Index den Namen „hotels“. Die Felder wurden wie folgt definiert:

```JSON
{
    "name": "hotels",  
    "fields": [
        {"name": "hotelId", "type": "Edm.String", "key": true, "searchable": false, "sortable": false, "facetable": false},
        {"name": "baseRate", "type": "Edm.Double"},
        {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
        {"name": "description_fr", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "analyzer": "fr.lucene"},
        {"name": "hotelName", "type": "Edm.String", "facetable": false},
        {"name": "category", "type": "Edm.String"},
        {"name": "tags", "type": "Collection(Edm.String)"},
        {"name": "parkingIncluded", "type": "Edm.Boolean", "sortable": false},
        {"name": "smokingAllowed", "type": "Edm.Boolean", "sortable": false},
        {"name": "lastRenovationDate", "type": "Edm.DateTimeOffset"},
        {"name": "rating", "type": "Edm.Int32"},
        {"name": "location", "type": "Edm.GeographyPoint"}
    ]
}
```

Wir haben die Indexattribute für jedes Feld ausgehend davon ausgewählt, wie sie unseres Erachtens in einer Anwendung verwendet werden. `hotelId` ist beispielsweise ein eindeutige Schlüssel, den die Benutzer bei der Hotelsuche wahrscheinlich nicht kennen. Daher haben wir die Volltextsuche für dieses Feld deaktiviert, indem wir für `searchable` den Wert `false` festgelegt haben, was im Index Platz spart.

In Ihrem Index des Typs `Edm.String` muss genau ein Feld als „key“ bestimmt sein.

Diese Indexdefinition verwendet für das Feld `description_fr` eine benutzerdefinierte Sprachanalyse, da Sie Text auf Französisch speichern soll. Im [Thema zur Sprachunterstützung auf MSDN](https://msdn.microsoft.com/library/azure/dn879793.aspx) sowie im entsprechenden [Blogbeitrag](https://azure.microsoft.com/blog/language-support-in-azure-search/) finden Sie weitere Informationen zu Sprachanalysen.

## III. Stellen der HTTP-Anforderung
1. Stellen Sie eine HTTP POST-Anforderung an die Endpunkt-URL des Azure Search-Diensts, indem Sie Ihre Indexdefinition als Anforderungstext verwenden. Verwenden Sie in der URL Ihren Dienstnamen als Hostnamen, und geben Sie die richtige `api-version` als Abfrageparameter ein (zum Zeitpunkt der Veröffentlichung dieses Dokuments ist `2015-02-28` die aktuelle API-Version).
2. Legen Sie im Anforderungsheader `application/json` für `Content-Type` fest. Sie müssen außerdem den Admin-Schlüssel des Diensts angeben, den Sie in Schritt I im Header `api-key` identifiziert haben.


    POST https://[service name].search.windows.net/indexes?api-version=2015-02-28 Content-Type: application/json api-key: [api-key]

Bei einer erfolgreichen Anforderung erscheint der Statuscode 201 (erstellt). Weitere Informationen zum Erstellen eines Indexes über die REST-API finden Sie in der API-Referenz auf [MSDN](https://msdn.microsoft.com/library/azure/dn798941.aspx). Weitere Informationen zu anderen HTTP-Statuscodes, die bei Fehlern ausgegeben werden, finden Sie in [diesem Artikel](https://msdn.microsoft.com/library/azure/dn798925.aspx).

Wenn Sie einen Index nicht mehr benötigen und ihn löschen möchten, stellen Sie einfach eine HTTP DELETE-Anforderung. Der Index „hotels“ wird beispielsweise wie folgt gelöscht:

    DELETE https://[service name].search.windows.net/indexes/hotels?api-version=2015-02-28
    api-key: [api-key]

## Weiter
Nach dem Erstellen eines Azure Search-Indexes können Sie Ihre Inhalte in den Index hochladen und mit dem Durchsuchen der Daten beginnen.

<!---HONumber=AcomDC_0302_2016-->