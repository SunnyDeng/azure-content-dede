<properties 
	pageTitle="Erste Schritte mit Azure Search | Microsoft Azure | Gehosteter Cloudsuchdienst" 
	description="Erste Schritte mit Azure Search, einem in Microsoft Azure gehosteten Cloudsuchdienst." 
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
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.date="01/11/2016" 
	ms.author="heidist"/>

# Erste Schritte mit Azure Search

Microsoft Azure Search ist ein gehosteter Cloudsuchdienst, mit dem Sie Suchfunktionen in benutzerdefinierte Anwendungen einbetten können. Er stellt die Suchmaschine und den Speicherplatz für Ihre Daten zur Verfügung. Der Zugriff darauf und die Verwaltung der Daten erfolgt mithilfe einer .NET-Bibliothek oder einer REST-API.

In diesem Artikel erhalten Sie eine Einführung in die REST-API für Azure Search.

Ein alternativer Ansatz für .NET Entwickler ist die Verwendung des Azure Search .NET SDK. Weitere Informationen finden Sie unter [Erste Schritte mit Azure Search in .NET](search-get-started-dotnet.md) oder [Verwenden des Azure Search .NET SDK](search-howto-dotnet-sdk.md) Details.


> [AZURE.NOTE]Um dieses Lernprogramm absolvieren zu können, benötigen Sie ein [Azure-Abonnement](../includes/free-trial-note.md). Wenn Sie sich noch nicht für ein Testabonnement registrieren möchten, können Sie dieses Lernprogramm überspringen und stattdessen [Azure App Service testen](https://tryappservice.azure.com/). Bei dieser Alternativoption erhalten Sie Azure Search mit einer ASP.NET-Web-App kostenlos – eine Stunde pro Sitzung, ganz ohne Abonnement.
 
<a id="sub-1"></a>
## Erstellen eines Azure Search-Diensts

Administratoren können den Suchdienst bei der Auswahl des gemeinsam genutzten Diensts kostenlos zu einem bestehenden Abonnement hinzufügen, oder bei der Auswahl fest zugeordneter Ressourcen zu einem reduzierten Preis.

Abonnenten erhalten kostenlosen Zugriff auf einen gemeinsam genutzten mehrinstanzenfähigen Suchdienst, den Sie zu Lernzwecken, zum Testen von Machbarkeitsstudien oder zur Entwicklung kleiner Suchprojekte nutzen können.

Melden Sie sich mit Ihrem bestehenden Abonnement beim [Azure-Portal](https://portal.azure.com) an. Schrittweise Anweisungen finden Sie unter [Erstellen eines Azure Search-Diensts im Portal](search-create-service-portal.md).

## Abrufen der Dienst-URL und eines API-Schlüssels

Nach der Diensterstellung können Sie zu den Konfigurationseinstellungen zurückkehren, um die URL und die API-Schlüssel abzurufen. Für Verbindungen mit Ihrem Suchdienst benötigen Sie sowohl URL als auch API-Schlüssel, um den Aufruf zu authentifizieren. So finden Sie diese Werte schnell und einfach:

1. Melden Sie sich beim [Azure-Portal](https://portal.azure.com) an.
2. Klicken Sie in der Navigationsleiste auf **Search-Dienst**, um alle für Ihr Abonnement bereitgestellten Azure Search-Dienste aufzuführen.
3. Wählen Sie den Dienst aus, den Sie verwenden möchten.
4.	Im Dienst-Dashboard sehen Sie Kacheln für **EIGENSCHAFTEN** und **SCHLÜSSEL**, sowie eine Übersicht über die Ressourcennutzung. 


<a id="sub-3"></a>
## Testen der Dienstfunktionen

Testen Sie, ob Ihr Dienst einsatzbereit ist und darauf von einer Clientanwendung aus zugegriffen werden kann. Diese Prozedur verwendet Fiddler, einen [kostenlosen Download von Telerik](http://www.telerik.com/fiddler), um HTTP-Anforderungen zu senden und die Antworten anzuzeigen. Mit Fiddler können Sie die API sofort testen, ohne Code schreiben zu müssen.

In den folgenden Schritten erstellen Sie einen Index, laden Dokumente hoch, fragen den Index ab und fragen zuletzt Dienstinformationen aus dem System ab.

### Erstellen eines Index

1. Starten Sie Fiddler. Deaktivieren Sie die Option **Capture Traffic** im Menü "File", um zusätzliche HTTP-Aktivität auszublenden, die nicht zur aktuellen Aufgabe gehört. Erstellen Sie die folgende Anforderung in der Registerkarte Composer: 

  	![][16]

2. Wählen Sie **PUT** aus.

3. Geben Sie eine URL ein, die sich aus Dienst-URL (aus der Eigenschaftenseite), Anfrageattributen und der API-Version zusammensetzt. Beachten Sie dabei Folgendes:
   + Verwenden Sie das Präfix HTTPS
   + Das Anfrageattribut ist "/indexes/hotels". Damit weisen Sie Search an, einen Index mit dem Namen "hotels" zu erstellen.
   + Die API-Version ist in Kleinbuchstaben als „?api-version=2015-02-28“ angegeben. API-Versionen sind wichtig, da regelmäßig Updates für Azure Search bereitgestellt werden. In seltenen Fällen kann es passieren, dass ein Teil der API durch ein Update nicht mehr wie gewohnt funktioniert. Mit API-Versionen können Sie weiterhin Ihre bestehende Version verwenden und selbst bestimmen, wann Sie zur neueren Version wechseln möchten.

    Die komplette URL sollte in etwa wie folgt aussehen:

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
          {"name": "hotelName", "type": "Edm.String", "suggestions": true},
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

### Laden von Dokumenten

Ihre Anforderung für Dokumente in der Registerkarte "Composer" sieht in etwa wie folgt aus. Der Text der Anforderung enthält die Suchdaten für 4 Hotels.

   ![][17]

1. Wählen Sie **POST** aus.

2.	Geben Sie eine URL ein, die sich aus „HTTPS“, Ihrer Dienst-URL und „/indexes/<'indexname'>/docs/index?api-version=2015-02-28“ zusammensetzt. Die komplette URL sollte in etwa wie folgt aussehen:

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

### Indexabfragen

Sie haben nun einen Index erstellt und Dokumente hochgeladen und können beginnen, Abfragen auszuführen. Erstellen Sie einen GET-Befehl in der Registerkarte Composer mit dem folgenden Text:

   ![][18]

1.	Wählen Sie **GET** aus.

2.	Geben Sie eine URL ein, die mit HTTPS beginnt, gefolgt von Ihrer Dienst-URL, gefolgt von "/indexes/<'indexname'>/docs?", gefolgt von den Abfrageparametern. Verwenden Sie als Beispiel die folgende URL und ersetzen Sie den Beispiel-Hostnamen durch einen gültigen Hostnamen für Ihren Dienst.

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2015-02-28

    Diese Abfrage sucht nach dem Begriff "motel" und ruft Facettenkategorien für Bewertungen ab.

3.	Der Anforderungsheader muss identisch zur vorherigen Abfrage sein. Ersetzen Sie host und api-key (Kleinbuchstaben) durch die entsprechenden Werte für Ihren Dienst.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

Der Antwortcode sollte 200 sein, und die Antwortausgabe sollte in etwa der folgenden Abbildung entsprechen.
 
   ![][19]

Die folgende Beispielabfrage stammt aus dem Artikel [Suchindex-Operationen (Azure Search-API)](http://msdn.microsoft.com/library/dn798927.aspx) auf MSDN. Viele der Beispielabfragen in diesem Thema enthalten Leerzeichen, die von Fiddler nicht unterstützt werden. Ersetzen Sie alle Leerzeichen vor dem Einfügen durch ein +-Zeichen, bevor Sie die Abfrage in Fiddler ausführen:

**Vor dem Ersetzen der Leerzeichen:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2015-02-28

**Nach dem Ersetzen der Leerzeichen durch +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2015-02-28
### Systemabfragen

Sie können auch das System abfragen, um Dokumentenanzahl oder Speicherverbrauch zu erhalten. Erstellen Sie eine Anforderung in der Registerkarte Composer mit dem folgenden Text. Die Antwort enthält die Anzahl der Dokumente und den verbrauchten Speicherplatz.

   ![][20]

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

6.	Klicken Sie auf die Registerkarte Inspectors | Headers und wählen Sie das JSON-Format aus. Sie sollten nun die Dokumentenanzahl und die Speichergröße (in KB) sehen.

 	![][21]

<a id="sub-4"></a>
## Erkunden des Suchdienst-Dashboards

Falls Sie eine Auffrischung brauchen, um die Konfigurationsseiten zu finden, folgen Sie den folgenden Schritten, um zum Dienst-Dashboard zu gelangen.

1.	Melden Sie sich mit Ihrem bestehenden Abonnement beim [Azure-Portal](https://portal.azure.com) an. 
2.	Klicken Sie auf **Startseite** und anschließend auf die Kachel für Ihren Search-Dienst.

 	![][22]

4.	Durch das Klicken auf die Kachel wird das Dienst-Dashboard geöffnet. Beachten Sie die Kommandos **Start**, **Stop** und **Löschen** am oberen Rand.

5.	Die Dienst-URL befindet sich nahe dem oberen Seitenrand. Sie benötigen diese URL zum Herstellen einer Verbindung mit Ihrem Azure Search-Dienst.
	
7.	Klicken Sie auf das Symbol **SCHLÜSSEL**, um die API-Schlüssel anzuzeigen. Sie benötigen einen Administratorschlüssel, um sich beim Dienst zu authentifizieren. Sie können entweder den primären oder den sekundären verwenden. Optional können Sie Abfrageschlüssel für einen schreibgeschützten Zugriff auf den Dienst erstellen.


<!--Next steps and links -->
<a id="next-steps"></a>
## Ausprobieren

Bereit für den nächsten Schritt? Unter den folgenden Links finden Sie zusätzliches Lernmaterial für die Erstellung und Verwaltung von Suchanwendungen mit Azure Search.

- [Erstellen eines Azure Search GeoSearch-Beispiels](search-create-geospatial.md)

- [Verwalten Ihrer Suchlösung in Microsoft Azure](search-manage.md)

- [Was ist Azure Search?](search-what-is-azure-search.md)

- [REST-API für Azure Suchdienst](http://msdn.microsoft.com/library/dn798935.aspx)

- [Azure Search .NET SDK](https://msdn.microsoft.com/library/azure/dn951165.aspx)

- [Channel 9-Video: Introduction to Azure Search](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)

- [Channel 9-Video: Azure Search und Geodaten](http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data)

- [Cloud Cover-Episode 152: Generieren eines Index in Azure Search](http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh)

<!--Anchors-->
[Start with the free service]: #sub-1
[Upgrade to standard search]: #sub-2
[Test service operations]: #sub-3
[Explore Search service dashboard]: #sub-4
[Try it out]: #next-steps

<!--Image references-->
[6]: ./media/search-get-started/AzureSearch_Configure1_1_New.PNG
[7]: ./media/search-get-started/AzureSearch_Configure1_2_Everything.PNG
[8]: ./media/search-get-started/Azuresearch_Configure1_3a_Gallery.PNG
[9]: ./media/search-get-started/AzureSearch_Configure1_4_GallerySeeAll.PNG
[10]: ./media/search-get-started/AzureSearch_Configure1_5_SearchTile.PNG
[11]: ./media/search-get-started/AzureSearch_Configure1_6_URL.PNG
[12]: ./media/search-get-started/AzureSearch_Configure1_7a_Free.PNG
[13]: ./media/search-get-started/AzureSearch_Configure1_17_HomeDashboard.PNG
[14]: ./media/search-get-started/AzureSearch_Configure1_9_Standard.PNG
[15]: ./media/search-get-started/AzureSearch_Configure1_10_ScaleUp.PNG
[16]: ./media/search-get-started/AzureSearch_Configure1_11_PUTIndex.PNG
[17]: ./media/search-get-started/AzureSearch_Configure1_12_POSTDocs.PNG
[18]: ./media/search-get-started/AzureSearch_Configure1_13_GETQuery.PNG
[19]: ./media/search-get-started/AzureSearch_Configure1_14_GETQueryResponse.PNG
[20]: ./media/search-get-started/AzureSearch_Configure1_15_Stats.PNG
[21]: ./media/search-get-started/AzureSearch_Configure1_16_StatsResponse.PNG
[22]: ./media/search-get-started/AzureSearch_Configure1_17_HomeDashboard.PNG
[23]: ./media/search-get-started/AzureSearch_Configure1_18_Explore.PNG


<!--Link references-->
[Manage your search solution in Microsoft Azure]: search-manage.md
[Azure Search development workflow]: search-workflow.md
[Create your first azure search solution]: search-create-first-solution.md
[Create a geospatial search app using Azure Search]: search-create-geospatial.md

<!---HONumber=AcomDC_0114_2016-->