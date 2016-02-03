<properties
	pageTitle="Auswerten und Testen von Azure Search-REST-APIs mithilfe von Fiddler | Microsoft Azure | Gehosteter Cloudsuchdienst"
	description="Verwenden Sie Fiddler zur codefreien Überprüfung der Verfügbarkeit von Azure Search sowie zum Testen der REST-APIs."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.date="12/18/2015"
	ms.author="heidist"/>

# Auswerten und Testen von Azure Search-REST-APIs mithilfe von Fiddler
> [AZURE.SELECTOR]
- [Overview](search-query-overview.md)
- [Search Explorer](search-explorer.md)
- [Fiddler](search-fiddler.md)
- [.NET](search-query-dotnet.md)
- [REST](search-query-rest-api.md)

In diesem Artikel erfahren Sie, wie Sie mithilfe von Fiddler (erhältlich als [kostenloser Download von Telerik](http://www.telerik.com/fiddler)) HTTP-Anforderungen ausgeben und Antworten mithilfe der Azure Search-REST-API anzeigen, ohne Code zu schreiben. Azure Search ist ein vollständig verwalteter, gehosteter Cloudsuchdienst in Microsoft Azure, der leicht über .NET- und REST-APIs programmiert werden kann. Die Dokumentation zu den REST-APIs des Azure Search-Diensts finden Sie in [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

In den folgenden Schritten erstellen Sie einen Index, laden Dokumente hoch, fragen den Index ab und fragen zuletzt Dienstinformationen aus dem System ab.

Für diese Schritte benötigen Sie einen Azure Search-Dienst und `api-key`. Anweisungen zu den ersten Schritten finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service-portal.md).

## Erstellen eines Index

1. Starten Sie Fiddler. Deaktivieren Sie die Option **Capture Traffic** im Menü **File**, um zusätzliche HTTP-Aktivität auszublenden, die nicht zur aktuellen Aufgabe gehört.

3. Erstellen Sie die in der Registerkarte **Composer** eine Anforderung, die wie im folgenden Screenshot aussieht.

  	![][1]

2. Wählen Sie **PUT** aus.

3. Geben Sie eine URL ein, die sich aus der Dienst-URL, Anforderungsattributen und der API-Version zusammensetzt. Beachten Sie dabei Folgendes:
   + Verwenden Sie das Präfix HTTPS.
   + Das Anfrageattribut ist "/indexes/hotels". Damit weisen Sie Search an, einen Index mit dem Namen "hotels" zu erstellen.
   + Die API-Version ist in Kleinbuchstaben als „?api-version=2015-02-28“ angegeben. API-Versionen sind wichtig, da regelmäßig Updates für Azure Search bereitgestellt werden. In seltenen Fällen kann es passieren, dass ein Teil der API durch ein Update nicht mehr wie gewohnt funktioniert. Aus diesem Grund erfordert Azure Search eine API-Version für jede Anforderung, damit Sie steuern können, welche Version verwendet wird.

    Die komplette URL sollte in etwa wie folgt aussehen.

         https://my-app.search.windows.net/indexes/hotels?api-version=2015-02-28

4.	Geben Sie den Anforderungsheader an, und ersetzen Sie host und api-key durch die entsprechenden Werte für Ihren Dienst.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

5.	Fügen Sie im Anforderungstext die Felder für die Indexdefinition ein.

         {
        "name": "hotels",  
        "fields": [
          {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
          {"name": "baseRate", "type": "Edm.Double"},
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false},
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

6.	Klicken Sie auf **Ausführen**.

In der Sitzungsliste sollte innerhalb weniger Sekunden eine HTTP 201-Antwort angezeigt werden. Das bedeutet, dass der Index erfolgreich erstellt wurde.

Falls Sie eine "HTTP 504"-Antwort erhalten, prüfen Sie, ob die URL mit HTTPS beginnt. Wenn Sie eine "HTTP 400"- oder "HTTP 404"-Antwort erhalten, prüfen Sie den Hauptteil der Anforderung auf Fehler, die durch Kopieren und Einfügen entstanden sind. "HTTP 403" deutet in der Regel auf ein Problem mit dem api-key hin (entweder ein ungültiger Schlüssel oder ein Syntaxproblem bei der Angabe des API-Schlüssels).

## Laden von Dokumenten

Ihre Anforderung für Dokumente in der Registerkarte **Composer** sieht in etwa wie folgt aus. Der Text der Anforderung enthält die Suchdaten für 4 Hotels.

   ![][2]

1. Wählen Sie **POST** aus.

2.	Geben Sie eine URL ein, die sich aus „HTTPS“, Ihrer Dienst-URL und „/indexes/<'indexname'>/docs/index?api-version=2015-02-28“ zusammensetzt. Die komplette URL sollte in etwa wie folgt aussehen.

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2015-02-28

3.	Der Anforderungsheader muss identisch zur vorherigen Abfrage sein. Ersetzen Sie host und api-key (Kleinbuchstaben) durch die entsprechenden Werte für Ihren Dienst.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	Der Anforderungstext enthält vier Dokumente, die zum Index "hotels" hinzugefügt werden sollen.

        {
        "value": [
        {
        	"@search.action": "upload",
        	"hotelId": "1",
        	"baseRate": 199.0,
        	"description": "Best hotel in town",
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
        	"@search.action": "upload",
        	"hotelId": "3",
        	"baseRate": 279.99,
        	"description": "Surprisingly expensive",
        	"hotelName": "Dew Drop Inn",
        	"category": "Bed and Breakfast",
        	"tags": ["charming", "quaint"],
        	"parkingIncluded": true,
        	"smokingAllowed": false,
        	"lastRenovationDate": null,
        	"rating": 4,
        	"location": { "type": "Point", "coordinates": [-122.33207, 47.60621] }
          },
          {
        	"@search.action": "upload",
        	"hotelId": "4",
        	"baseRate": 220.00,
        	"description": "This could be the one",
        	"hotelName": "A Hotel for Everyone",
        	"category": "Basic hotel",
        	"tags": ["pool", "wifi"],
        	"parkingIncluded": true,
        	"smokingAllowed": false,
        	"lastRenovationDate": null,
        	"rating": 4,
        	"location": { "type": "Point", "coordinates": [-122.12151, 47.67399] }
          }
         ]
        }

8.	Klicken Sie auf **Ausführen**.

Innerhalb weniger Sekunden sollten Sie eine "HTTP 200"-Antwort in der Sitzungsliste sehen. Dies bedeutet, dass die Dokumente erfolgreich erstellt wurden. Falls Sie eine 207-Antwort erhalten, ist der Upload von mindestens einem Dokument fehlgeschlagen. Wenn Sie eine 404-Antwort erhalten, enthält entweder der Header oder der Text Ihrer Anforderung einen Syntaxfehler.

## Indexabfragen

Sie haben nun einen Index erstellt und Dokumente hochgeladen und können beginnen, Abfragen auszuführen. Erstellen Sie zur Abfrage des Dienstes einen **GET**-Befehl in der Registerkarte **Composer**, der ähnlich wie im folgenden Screenshot aussieht.

   ![][3]

1.	Wählen Sie **GET** aus.

2.	Geben Sie eine URL ein, die mit HTTPS beginnt, gefolgt von Ihrer Dienst-URL, gefolgt von "/indexes/<'indexname'>/docs?", gefolgt von den Abfrageparametern. Verwenden Sie als Beispiel die folgende URL und ersetzen Sie den Beispiel-Hostnamen durch einen gültigen Hostnamen für Ihren Dienst.

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    Diese Abfrage sucht nach dem Begriff "motel" und ruft Facettenkategorien für Bewertungen ab.

3.	Der Anforderungsheader muss identisch zur vorherigen Abfrage sein. Ersetzen Sie host und api-key (Kleinbuchstaben) durch die entsprechenden Werte für Ihren Dienst.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

Der Antwortcode sollte 200 sein, und die Antwortausgabe sollte in etwa dem folgenden Screenshot entsprechen.

   ![][4]

Die folgende Beispielabfrage stammt aus dem Artikel [Suchindex-Operationen (Azure Search-API)](http://msdn.microsoft.com/library/dn798927.aspx) auf MSDN. Viele der Beispielabfragen in diesem Thema enthalten Leerzeichen, die von Fiddler nicht unterstützt werden. Ersetzen Sie alle Leerzeichen vor dem Einfügen durch ein +-Zeichen, bevor Sie die Abfrage in Fiddler ausführen.

**Vor dem Ersetzen der Leerzeichen:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**Nach dem Ersetzen der Leerzeichen durch +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28

## Systemabfragen

Sie können auch das System abfragen, um Dokumentenanzahl oder Speicherverbrauch zu erhalten. Erstellen Sie eine Anforderung in der Registerkarte **Composer** mit dem folgenden Text. Die Antwort enthält die Anzahl der Dokumente und den verbrauchten Speicherplatz.

 ![][5]

1.	Wählen Sie **GET** aus.

2.	Geben Sie eine URL mit Ihrer Dienst-URL ein, gefolgt von „/indexes/hotels/stats?api-version=2015-02-28“:

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2015-02-28

3.	Geben Sie den Anforderungsheader an, und ersetzen Sie host und api-key durch die entsprechenden Werte für Ihren Dienst.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.	Lassen Sie den Anforderungstext leer.

5.	Klicken Sie auf **Ausführen**. Sie sollten eine "HTTP 200"-Antwort in der Sitzungsliste sehen. Wählen Sie den Eintrag zu Ihrem Befehl aus.

6.	Klicken Sie auf die Registerkarte **Inspectors**, klicken Sie dann auf die Registerkarte **Headers**, und wählen Sie das JSON-Format aus. Sie sollten nun die Dokumentenanzahl und die Speichergröße (in KB) sehen.

## Nächste Schritte

Informationen zur Verwaltung und Verwendung von Azure Search ohne Code finden Sie unter [Verwalten Ihres Suchdiensts in Azure](search-manage.md).


<!--Image References-->
[1]: ./media/search-fiddler/AzureSearch_Fiddler1_PutIndex.png
[2]: ./media/search-fiddler/AzureSearch_Fiddler2_PostDocs.png
[3]: ./media/search-fiddler/AzureSearch_Fiddler3_Query.png
[4]: ./media/search-fiddler/AzureSearch_Fiddler4_QueryResults.png
[5]: ./media/search-fiddler/AzureSearch_Fiddler5_QueryStats.png

<!---HONumber=AcomDC_0128_2016-->