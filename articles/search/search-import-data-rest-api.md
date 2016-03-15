<properties
    pageTitle="Importieren von Daten in Azure Search über die REST-API | Microsoft Azure | Gehosteter Cloudsuchdienst"
    description="Hochladen von Daten in einen Index in Azure Search über die REST-API."
    services="search"
    documentationCenter=""
    authors="ashmaka"
    manager=""
    editor=""
    tags=""/>

<tags
    ms.service="search"
    ms.devlang="rest-api"
    ms.workload="search"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="na"
    ms.date="02/29/2016"
    ms.author="ashmaka"/>

# Importieren von Daten in Azure Search über die REST-API
> [AZURE.SELECTOR]
- [Übersicht](search-what-is-data-import.md)
- [Portal](search-import-data-portal.md)
- [.NET](search-import-data-dotnet.md)
- [REST](search-import-data-rest-api.md)
- [Indexer](search-howto-connecting-azure-sql-database-to-azure-search-using-indexers-2015-02-28.md)


In diesem Artikel erfahren Sie, wie Sie die [Azure Search REST-API](https://msdn.microsoft.com/library/azure/dn798935.aspx) zum Importieren von Daten in einen Azure Search-Index verwenden. [Erstellen Sie einen Azure Search-Index](search-create-index-rest-api.md), bevor Sie mit dieser exemplarischen Vorgehensweise beginnen.

Um Dokumente mit der REST-API mithilfe von Push in Ihren Index zu verschieben, geben Sie eine HTTP POST-Anforderung an das URL-Endpunkt Ihres Indexes aus. Der Hauptteil der HTTP-Anforderung ist ein JSON-Objekt, das die Dokumente enthält, die hinzugefügt, geändert oder gelöscht werden sollen.

## I. Identifizieren des Admin-API-Schlüssels Ihres Azure Search-Diensts
Beim Ausgeben von HTTP-Anforderungen in Ihrem Dienst mithilfe der REST-API muss *jede* API den API-Schlüssel enthalten, der für den bereitgestellten Suchdienst erstellt wurde. Ein gültiger Schlüssel stellt anforderungsbasiert eine Vertrauensstellung her zwischen der Anwendung, die die Anforderung versendet, und dem Dienst, der sie verarbeitet.

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com/) an, um die API-Schlüssel für Ihren Dienst zu suchen.
2. Wechseln Sie zum Blatt Ihres Azure Search-Diensts.
3. Klicken Sie auf das Schlüsselsymbol.

Der Dienst enthält *Admin-Schlüssel* und *Abfrageschlüssel*.
  * Die primären und sekundären *Admin-Schlüssel* gewähren Ihnen Vollzugriff auf alle Vorgänge. Dazu zählen die Dienstverwaltung und das Erstellen und Löschen von Indizes, Indexern und Datenquellen. Ihnen stehen zwei Schlüssel zur Verfügung, damit Sie den sekundären Schlüssel weiterhin nutzen können, wenn Sie den primären Schlüssel neu generieren möchten, und umgekehrt.
  * Die *Abfrageschlüssel* gewähren Ihnen Lesezugriff auf Indizes und Dokumente. Diese werden in der Regel auf Clientanwendungen verteilt, die Suchanfragen ausgeben.

Verwenden Sie zum Importieren von Daten in einen Index entweder den primären oder den sekundären Admin-Schlüssel.

## II. Entscheiden Sie, welche Indizierungsaktion verwendet werden soll.
Wenn Sie die REST-API verwenden, werden HTTP POST-Anforderungen mit JSON-Anforderungstexten an die Endpunkt-URL Ihres Azure Search-Indexes ausgegeben. Das JSON-Objekt im HTTP-Anforderungstext enthält ein einzelnes JSON-Array namens „value“, das JSON-Objekte enthält. Diese stellen Dokumente dar, die Sie zum Index hinzufügen, aktualisieren oder löschen möchten.

Jedes JSON-Objekt im Array „value“ stellt ein zu indizierendes Dokument dar. Jedes der Objekte enthält den Schlüssel des Dokuments und bestimmt die gewünschte Indizierungsaktion (Hochladen, Zusammenführen, Löschen usw.). Je nachdem, welche der folgenden Aktionen Sie wählen, müssen für jedes Dokument nur bestimmte Felder eingefügt werden:

@search.action | Beschreibung | Erforderliche Felder für jedes Dokument | Hinweise
--- | --- | --- | ---
`upload` | Eine `upload`-Aktion entspricht „upsert“, wobei neue Dokumente eingefügt und bestehende Dokumente aktualisiert/ersetzt werden. | Schlüssel und alle anderen zu definierenden Felder | Wenn ein bestehendes Dokument aktualisiert/ersetzt wird, werden alle in der Anforderung nicht festgelegten Felder auf `null` festgelegt. Dies tritt auch auf, wenn das Feld zuvor auf einen Wert festgelegt wurde, der nicht Null ist.
`merge` | Aktualisiert ein bestehendes Dokument mit den angegebenen Feldern. Wenn das Dokument im Index nicht vorhanden ist, schlägt die Zusammenführung fehl. | Schlüssel und alle anderen zu definierenden Felder | Jedes Feld, das Sie in einer Zusammenführung angeben, ersetzt das vorhandene Feld im Dokument. Dies beinhaltet auch Felder vom Typ `Collection(Edm.String)`. Wenn das Dokument beispielsweise das Feld `tags` mit dem Wert `["budget"]` enthält und Sie eine Zusammenführung mit dem Wert `["economy", "pool"]` für `tags` durchführen, hat das Feld `tags` am Ende den Wert `["economy", "pool"]`. Der Wert lautet nicht `["budget", "economy", "pool"]`.
`mergeOrUpload` | Verhält sich wie `merge`, wenn im Index bereits ein Dokument mit dem entsprechenden Schlüssel vorhanden ist. Wenn das Dokument nicht vorhanden ist, verhält es sich wie `upload` mit einem neuen Dokument. | Schlüssel und alle anderen zu definierenden Felder | - 
`delete` | Entfernt das festgelegte Dokument aus dem Index. | nur Schlüssel | Alle festgelegten Felder mit Ausnahme des Schlüsselfelds werden ignoriert. Wenn Sie ein einzelnes Feld aus einem Dokument entfernen möchten, verwenden Sie stattdessen die Funktion zum *Zusammenführen* und setzen das Feld explizit auf Null.

## III. Erstellen Sie die HTTP-Anforderung und den Anforderungstext
Da Sie nun die erforderlichen Feldwerte für Ihre Indexaktionen gesammelt haben, können Sie die HTTP-Anforderung und den JSON-Anforderungstext für den Datenimport erstellen.

#### Anforderung und Anforderungsheader
In der URL müssen Sie Ihren Dienstnamen, den Indexnamen (in diesem Fall „hotels“) sowie die entsprechende API-Version (die aktuelle Version der API zum Zeitpunkt der Veröffentlichung dieses Dokuments ist `2015-02-28`) bereitstellen. Sie müssen die Anforderungsheader `Content-Type` und `api-key` festlegen. Verwenden Sie für letzteres einen der Admin-Schlüssel Ihres Diensts.

    POST https://[search service].search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28
    Content-Type: application/json
    api-key: [admin key]

#### Anforderungstext

```JSON
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
        },
        {
            "@search.action": "mergeOrUpload",
            "hotelId": "3",
            "baseRate": 129.99,
            "description": "Close to town hall and the river"
        },
        {
            "@search.action": "delete",
            "hotelId": "6"
        }
    ]
}
```

In diesem Fall verwenden wir `upload`, `mergeOrUpload` und `delete` als Suchaktionen.

Wir gehen davon aus, dass der Index in diesem Beispiel („hotels“) bereits mit einigen Dokumenten gefüllt ist. Beachten Sie, dass alle möglichen Dokumentfelder bei `merge` nicht festgelegt werden mussten, und bei `delete` musste nur der Dokumentschlüssel (`hotelId`) definiert werden.

Beachten Sie außerdem, dass nur bis zu 1000 Dokumente (oder 16 MB) in einer einzigen Indizierungsanforderung enthalten sein können.

## IV. Erläuterungen zum HTTP-Antwortcode
#### 200
Nach der Übermittlung einer erfolgreichen Indizierungsanforderung erhalten Sie eine HTTP-Antwort mit dem Statuscode `200 OK`. Der JSON-Text der HTTP-Antwort lautet wie folgt:

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": true,
            "errorMessage": null
        },
        ...
    ]
}
```

#### 207
Der Statuscode `207` wird zurückgegeben, wenn mindestens ein Element nicht erfolgreich indiziert wurde. Der JSON-Text der HTTP-Antwort enthält Informationen zu den fehlgeschlagenen Dokumenten.

```JSON
{
    "value": [
        {
            "key": "unique_key_of_document",
            "status": false,
            "errorMessage": "The search service is too busy to process this document. Please try again later."
        },
        ...
    ]
}
```

> [AZURE.NOTE] Dies bedeutet häufig, dass die Auslastung Ihres Suchdiensts einen Punkt erreicht, an dem Indizierungsanforderungen damit beginnen, `503`-Antworten zurückzugeben. In diesem Fall empfehlen wir ein Backoff des Clientcodes. Versuchen Sie es daraufhin nach einer Weile erneut. Dadurch hat das System ausreichend Zeit für eine Wiederherstellung, und die Wahrscheinlichkeit, dass spätere Anforderungen gelingen, steigt. Wenn Sie Ihre Anforderungen schnell wiederholen, dauert das Problem nur an.

#### 429
Der Statuscode `429` wird zurückgegeben, wenn Sie Ihr Kontingent hinsichtlich der Anzahl der Dokumente pro Index überschritten haben.

#### 503
Der Statuscode `503` wird zurückgegeben, wenn keines der Elemente in der Anforderung erfolgreich indiziert wurde. Dieser Fehler bedeutet, dass die Auslastung des Systems sehr hoch ist und Ihre Anforderungen aktuell nicht verarbeitet werden können.

> [AZURE.NOTE] In diesem Fall empfehlen wir ein Backoff des Clientcodes. Versuchen Sie es daraufhin nach einer Weile erneut. Dadurch hat das System ausreichend Zeit für eine Wiederherstellung, und die Wahrscheinlichkeit, dass spätere Anforderungen gelingen, steigt. Wenn Sie Ihre Anforderungen schnell wiederholen, dauert das Problem nur an.

Weitere Informationen zu Dokumentaktionen und Antworten bei Erfolg/Fehler finden Sie [auf dieser Seite](https://msdn.microsoft.com/library/azure/dn798925.aspx). Weitere Informationen zu anderen HTTP-Statuscodes, die bei Fehlern ausgegeben werden, finden Sie in [diesem Artikel](https://msdn.microsoft.com/library/azure/dn798925.aspx).

## Weiter
Nach dem Auffüllen des Azure Search-Indexes können Sie mit Abfragen für die Suche nach Dokumenten beginnen.

<!---HONumber=AcomDC_0302_2016-->
