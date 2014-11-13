<properties title="Erste Schritte mit Azure Search" pageTitle="Erste Schritte mit Azure Search" description="Erste Schritte mit Azure Search" metaKeywords="" services="Azure Search" solutions="" documentationCenter="" authors="Heidist" manager="mblythe" videoId="" scriptId="" />

<tags ms.service="azure-search" ms.devlang="" ms.workload="search" ms.topic="article" ms.tgt_pltfrm="" ms.date="09/23/2014" ms.author="heidist" />

# Erste Schritte mit Azure Search

[WACOM.INCLUDE [Dieser Artikel verwendet das Azure-Vorschauportal](../includes/preview-portal-note.md)]

Microsoft Azure Search (öffentliche Vorschau) ist ein neuer Dienst, mit dem Sie Suchfunktionen in benutzerdefinierte Anwendungen einbetten können. Er stellt die Suchmaschine und den Speicherplatz für Ihre Daten zur Verfügung. Der Zugriff darauf und die Verwaltung der Daten erfolgt mithilfe einer REST-API. Weitere Informationen über Gründe für die Verwendung von Azure Search finden Sie unter [Azure Search-Szenarien und -Funktionen][Azure Search-Szenarien und -Funktionen].

Administratoren können den Suchdienst bei der Auswahl des gemeinsam genutzten Diensts kostenlos zu einem bestehenden Abonnement hinzufügen, oder bei der Auswahl fest zugeordneter Ressourcen zu einem reduzierten Preis. Dieser Artikel besteht aus den folgenden Abschnitten:

<!--Table of contents -->

-   [Erste Schritte mit dem kostenlosen Dienst][Erste Schritte mit dem kostenlosen Dienst]
-   [Upgrade auf die Standardsuche][Upgrade auf die Standardsuche]
-   [Testen der Dienstfunktionen][Testen der Dienstfunktionen]
-   [Erkunden des Suchdienst-Dashboards][Erkunden des Suchdienst-Dashboards]
-   [Ausprobieren][Ausprobieren]

## Erste Schritte mit dem kostenlosen Dienst

Abonnenten erhalten kostenlosen Zugriff auf einen gemeinsam genutzten mehrinstanzenfähigen Suchdienst, den Sie zu Lernzwecken, zum Testen von Machbarkeitsstudien oder zur Entwicklung kleiner Suchprojekte nutzen können. Führen Sie die folgenden Schritte aus, um sich für die kostenlose Version anzumelden.

1.  Melden Sie sich mit Ihrem bestehenden Azure-Konto am [Azure-Vorschauportal][Azure-Vorschauportal] an. Beachten Sie, dass diese URL zum Vorschauportal führt. Die Verwendung des Vorschauportals ist zwingend notwendig.

2.  Klicken Sie unten auf der Seite auf **New**.

    ![][0]

3.  Klicken Sie oben auf der Seite auf **Alles**.

    ![][1]

4.  Klicken Sie im Katalog auf **Daten, Speicher, Cache + Backup**.

    ![][2]

5.  Klicken Sie auf **Alle anzeigen**, um die Liste der datenbezogenen Dienste zu erweitern.

    ![][3]

6.  Klicken Sie in den Datendiensten auf **Suchen**.

    ![][4]

7.  Klicken Sie unten auf der Suchseite auf **ERSTELLEN**.

8.  Geben Sie einen Dienstnamen in Kleinbuchstaben für die Dienst-URL ein. Vermeiden Sie dabei Bindestriche und Leerzeichen und beachten Sie die Längenbeschränkung von 15 Zeichen.

    ![][5]

9.  Klicken Sie auf den Pfeil in **Preisebene**, um eine Preisoption auszuwählen. Wählen Sie **KOSTENLOS** aus und klicken Sie auf **AUSWÄHLEN** am unteren Seitenrand. Die kostenlose Version bietet ausreichend Kapazität für Lernprogramme und zum Schreiben von Code für Machbarkeitsstudien, eignet sich jedoch nicht für den Einsatz in Produktionsumgebungen.

    ![][6]

10. Klicken Sie auf den Pfeil in **Ressourcengruppe**, um eine bestehende Gruppe auszuwählen oder eine neue Gruppe zu erstellen. Ressourcengruppen sind Container für Dienste und Ressourcen, die einem gemeinsamen Zweck dienen. Wenn Sie eine auf Azure Search, Azure Websites und Azure-Blobspeicher basierte Suchanwendung erstellen möchten, können Sie z. B. eine Ressourcengruppe erstellen, die diese Dienste in den Verwaltungsseiten im Portal gruppiert.

11. Klicken Sie auf den Pfeil in **Abonnement**, wenn Sie mehrere Abonnements haben und für diesen Suchdienst ein anderes Abonnement verwenden möchten.

12. Klicken Sie auf den Pfeil in **Standort**, um eine Region für das Rechenzentrum auszuwählen. In dieser Vorschau haben Sie die Wahl zwischen USA West, USA Ost, Nordeuropa und Südostasien. Später, wenn weitere Regionen online sind, können Sie eine Region für den zu erstellenden Dienst auswählen. Die öffentliche Vorschau unterstützt keine Konfiguration mit verteilten Ressourcen über mehrere Rechenzentren.

13. Klicken Sie auf **ERSTELLEN**, um den Dienst bereitzustellen. **CREATE** kann erst angeklickt werden, nachdem Sie alle erforderlichen Werte eingegeben haben.

Der Dienst wird innerhalb weniger Minuten erstellt. Sie können zu den Konfigurationseinstellungen zurückkehren, um die URL oder die API-Schlüssel abzurufen. Für Verbindungen mit Ihrem Suchdienst benötigen Sie sowohl URL als auch API-Schlüssel, um den Aufruf zu authentifizieren. So finden Sie diese Werte schnell und einfach:

1.  Klicken Sie auf **Durchsuchen** | **Alles** | **Daten, Speicher, Cache, + Backup** | **Alles anzeigen** | **Dienste suchen**. Klicken Sie auf Ihren Dienst, um dessen Dashboard anzuzeigen.

    ![][7]

2.  Im Dienst-Dashboard sehen Sie Kacheln für **EIGENSCHAFTEN** und **SCHLÜSSEL**, sowie eine Übersicht über die Ressourcennutzung.

    ![][8]

**EIGENSCHAFTEN** enthält die Dienst-URL.

**SCHLÜSSEL** enthält die für die Authentifizierung benötigten API-Schlüssel.

**NUTZUNG** zeigt Dokumentanzahl, verfügbare Ressourcen und Speicherbegrenzungen an.

Unter [Testen der Dienstfunktionen][Testen der Dienstfunktionen] wird beschrieben, wie Sie sich mithilfe dieser Werte mit dem Dienst verbinden können.

## Upgrade auf die Standardsuche

Mit der Standardsuche erhalten Sie fest zugeordnete Ressourcen in einem Azure-Rechenzentrum, die Ihnen exklusiv zur Verfügung stehen. Für Such-Arbeitslasten benötigen Sie sowohl Speicher- als auch Dienstreplikate. Bei der Anmeldung für die Standardsuche können Sie die Dienstkonfiguration optimieren, um mehr von der jeweils wichtigeren Ressource für Ihr Szenario zuzuweisen.

Fest zugeordnete Ressourcen verbessern Skalierbarkeit und Leistung, bieten jedoch keine zusätzlichen Funktionen. Die gemeinsam genutzte und die Standardsuche bieten denselben Funktionsumfang.

Für die Standardsuche erstellen Sie einen neuen Suchdienst und wählen die Standard-Preisebene aus. Beachten Sie, dass das Upgrade keine direkte Aktualisierung der kostenlosen Version ist. Beim Wechsel zu Standard und dessen Skalierungspotenzial müssen Sie einen neuen Dienst erstellen. Sie müssen die von Ihrer Suchanwendung verwendeten Indizes und Dokumente erneut laden.

Die Einrichtung fest zugeordneter Ressourcen kann einige Zeit in Anspruch nehmen (15 Minuten oder mehr).

**Schritt 1 - Erstellen eines neuen Diensts mit der Preisebene Standard**

1.  Melden Sie sich mit Ihrem bestehenden Azure-Konto am [Azure-Vorschauportal][Azure-Vorschauportal] an.

2.  Klicken Sie unten auf der Seite auf **New**.

3.  Klicken Sie oben auf der Seite auf **Alles**.

4.  Klicken Sie im Katalog auf **Daten, Speicher, Cache + Backup**.

5.  Klicken Sie auf **Alle anzeigen**, um die Liste der datenbezogenen Dienste zu erweitern.

6.  Klicken Sie in den Datendiensten auf **Suchen**.

7.  Klicken Sie unten auf der Suchseite auf **ERSTELLEN**.

8.  Geben Sie einen Dienstnamen in Kleinbuchstaben für die Dienst-URL ein. Vermeiden Sie dabei Bindestriche und Leerzeichen und beachten Sie die Längenbeschränkung von 15 Zeichen.

9.  Klicken Sie auf den Pfeil in **Preisebene**, um eine Preisoption auszuwählen. Wählen Sie **STANDARD** aus und klicken Sie auf **AUSWÄHLEN** am unteren Seitenrand.

![][9]

**Schritt 2 - Anpassen der Sucheinheiten an die Skalierungsanforderungen**

Die Standardsuche beginnt mit je einem Replikat und einer Partition, kann jedoch schnell und einfach auf höhere Ressourcenebenen skaliert werden.

1.  Kehren Sie nach der Erstellung des Diensts zum Dienst-Dashboard zurück und klicken Sie auf die Kachel **Skalieren**.

2.  Mit den Schiebereglern können Sie Replikate, Partitionen oder beides hinzufügen.

Zusätzliche Replikate und Partitionen werden in Sucheinheiten abgerechnet. Die insgesamt benötigten Sucheinheiten zum Unterstützen einer bestimmten Ressourcenkonfiguration wird auf der Seite angezeigt, während Sie Ressourcen hinzufügen. Unter [Preisdetails][Preisdetails] finden Sie Abrechnungsinformationen pro Einheit.

![][10]

## Testen der Dienstfunktionen

Als letzter Schritt bei der Konfiguration der Suche überprüfen Sie, ob Ihr Dienst einsatzbereit ist und über eine Clientanwendung angesprochen werden kann. Diese Prozedur verwendet Fiddler, einen [kostenlosen Download von Telerik][kostenlosen Download von Telerik], um HTTP-Anforderungen zu senden und die Antworten anzuzeigen. Mit Fiddler können Sie die API sofort testen, ohne Code schreiben zu müssen.

Die folgende Prozedur funktioniert für die gemeinsam genutzte und für die Standardsuche. In den folgenden Schritten erstellen Sie einen Index, laden Dokumente hoch, fragen den Index ab und fragen zuletzt Dienstinformationen aus dem System ab.

### Erstellen eines Index

1.  Starten Sie Fiddler. Deaktivieren Sie die Option **Capture Traffic** im Menü "File", um zusätzliche HTTP-Aktivität auszublenden, die nicht zur aktuellen Aufgabe gehört. Erstellen Sie die folgende Anforderung in der Registerkarte Composer:

    ![][11]

2.  Wählen Sie **PUT** aus.

3.  Geben Sie eine URL ein, die sich aus Dienst-URL (aus der Eigenschaftenseite), Anfrageattributen und der API-Version zusammensetzt. Beachten Sie dabei Folgendes:

-   Verwenden Sie das Präfix HTTPS
-   Das Anfrageattribut ist "/indexes/hotels". Damit weisen Sie Search an, einen Index mit dem Namen "hotels" zu erstellen.
-   API-Version steht in Kleinbuchstaben mit dem Format "?api-version=2014-07-31-preview". API-Versionen sind wichtig, da regelmäßig Updates für Azure Search bereitgestellt werden. In seltenen Fällen kann es passieren, dass ein Teil der API durch ein Update nicht mehr wie gewohnt funktioniert. Mit API-Versionen können Sie weiterhin Ihre bestehende Version verwenden und selbst bestimmen, wann Sie zur neueren Version wechseln möchten.

    Die komplette URL sollte in etwa wie folgt aussehen:

         https://my-app.search.windows.net/indexes/hotels?api-version=2014-07-31-Preview

1.  Geben Sie den Anforderungsheader an, und ersetzen Sie host und api-key durch die entsprechenden Werte für Ihren Dienst.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

2.  Fügen Sie im Anforderungstext die Felder für die Indexdefinition ein.

         {
        "name": "hotels",  
        "fields": [
          {"name": "hotelId", "type": "Edm.String", "key":true, "searchable": false},
          {"name": "baseRate", "type": "Edm.Double"},
          {"name": "description", "type": "Edm.String", "filterable": false, "sortable": false, "facetable": false, "suggestions": true},
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

3.  Klicken Sie auf **Ausführen**.

Innerhalb weniger Sekunden sollten Sie eine "HTTP 204"-Antwort in der Sitzungsliste sehen. Dies bedeutet, dass der Index erfolgreich erstellt wurde.

Falls Sie eine "HTTP 504"-Antwort erhalten, prüfen Sie, ob die URL mit HTTPS beginnt. Wenn Sie eine "HTTP 400"- oder "HTTP 404"-Antwort erhalten, prüfen Sie den Hauptteil der Anforderung auf Fehler, die durch Kopieren und Einfügen entstanden sind. "HTTP 403" deutet in der Regel auf ein Problem mit dem api-key hin (entweder ein ungültiger Schlüssel oder ein Syntaxproblem bei der Angabe des API-Schlüssels).

### Laden von Dokumenten

Ihre Anforderung für Dokumente in der Registerkarte "Composer" sieht in etwa wie folgt aus. Der Text der Anforderung enthält die Suchdaten für 4 Hotels.

![][12]

1.  Wählen Sie **POST** aus.

2.  Geben Sie eine URL ein, die mit HTTPS beginnt, gefolgt von Ihrer Dienst-URL, gefolgt von "/indexes/\<'indexname'\>/docs/index?api-version=2014-07-31-preview". Die komplette URL sollte in etwa wie folgt aussehen:

        https://my-app.search.windows.net/indexes/hotels/docs/index?api-version=2014-07-31-Preview

3.  Der Anforderungsheader muss identisch zur vorherigen Abfrage sein. Ersetzen Sie host und api-key (Kleinbuchstaben) durch die entsprechenden Werte für Ihren Dienst.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.  Der Anforderungstext enthält vier Dokumente, die zum Index "hotels" hinzugefügt werden sollen.

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

5.  Klicken Sie auf **Ausführen**.

Innerhalb weniger Sekunden sollten Sie eine "HTTP 200"-Antwort in der Sitzungsliste sehen. Dies bedeutet, dass die Dokumente erfolgreich erstellt wurden. Falls Sie eine 207-Antwort erhalten, ist der Upload von mindestens einem Dokument fehlgeschlagen. Wenn Sie ein 404-Antwort erhalten, enthält entweder der Header oder der Text Ihrer Anforderung einen Syntaxfehler.

### Indexabfragen

Sie haben nun einen Index erstellt und Dokumente hochgeladen und können beginnen, Abfragen auszuführen. Erstellen Sie einen GET-Befehl in der Registerkarte Composer mit dem folgenden Text:

![][13]

1.  Wählen Sie **GET** aus.

2.  Geben Sie eine URL ein, die mit HTTPS beginnt, gefolgt von Ihrer Dienst-URL, gefolgt von "/indexes/\<'indexname'\>/docs?", gefolgt von den Abfrageparametern. Verwenden Sie als Beispiel die folgende URL und ersetzen Sie den Beispiel-Hostnamen durch einen gültigen Hostnamen für Ihren Dienst.

        https://my-app.search.windows.net/indexes/hotels/docs?search=motel&facet=category&facet=rating,values:1|2|3|4|5&api-version=2014-07-31-Preview

    Diese Abfrage sucht nach dem Begriff "motel" und ruft Facettenkategorien für Bewertungen ab.

3.  Der Anforderungsheader muss identisch zur vorherigen Abfrage sein. Ersetzen Sie host und api-key (Kleinbuchstaben) durch die entsprechenden Werte für Ihren Dienst.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

Der Antwortcode sollte 200 sein, und die Antwortausgabe sollte in etwa der folgenden Abbildung entsprechen.

![][14]

Die folgende Beispielabfrage stammt aus dem Artikel [Suchindex-Operationen (Azure Search-API)][Suchindex-Operationen (Azure Search-API)] auf MSDN. Viele der Beispielabfragen in diesem Thema enthalten Leerzeichen, die von Fiddler nicht unterstützt werden. Ersetzen Sie alle Leerzeichen vor dem Einfügen durch ein +-Zeichen, bevor Sie die Abfrage in Fiddler ausführen:

**Vor dem Ersetzen der Leerzeichen:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate desc&api-version=2014-07-31-Preview

**Nach dem Ersetzen der Leerzeichen durch +:**

        GET /indexes/hotels/docs?search=*&$orderby=lastRenovationDate+desc&api-version=2014-07-31-Preview

### Systemabfragen

Sie können auch das System abfragen, um Dokumentenanzahl oder Speicherverbrauch zu erhalten. Erstellen Sie eine Anforderung in der Registerkarte Composer mit dem folgenden Text. Die Antwort enthält die Anzahl der Dokumente und den verbrauchten Speicherplatz.

![][15]

1.  Wählen Sie **GET** aus.

2.  Geben Sie eine URL ein, die aus Ihrer Dienst-URL besteht, gefolgt von "/indexes/hotels/stats?api-version=2014-07-31-Preview":

        https://my-app.search.windows.net/indexes/hotels/stats?api-version=2014-07-31-Preview 

3.  Geben Sie den Anforderungsheader an, und ersetzen Sie host und api-key durch die entsprechenden Werte für Ihren Dienst.

        User-Agent: Fiddler
        host: my-app.search.windows.net
        content-type: application/json
        api-key: 1111222233334444

4.  Lassen Sie den Anforderungstext leer.

5.  Klicken Sie auf **Ausführen**. Sie sollten eine "HTTP 200"-Antwort in der Sitzungsliste sehen. Wählen Sie den Eintrag zu Ihrem Befehl aus.

6.  Klicken Sie auf die Registerkarte Inspectors | Headers und wählen Sie das JSON-Format aus. Sie sollten nun die Dokumentenanzahl und die Speichergröße (in KB) sehen.

    ![][16]

## Erkunden des Suchdienst-Dashboards

Falls Sie eine Auffrischung brauchen, um die Konfigurationsseiten zu finden, folgen Sie den folgenden Schritten, um zum Dienst-Dashboard zu gelangen.

1.  Melden Sie sich mit Ihrem bestehenden Azure-Konto am [Azure-Vorschauportal][Azure-Vorschauportal] an.
2.  Klicken Sie auf **Durchsuchen** | **Alles**.

    ![][17]

3.  Wählen Sie **Dienste suchen** aus der Liste aus. Daraufhin wird eine Liste aller in Ihren Abonnements erstellten Suchdienste angezeigt.

4.  Klicken Sie auf einen Dienst, um dessen Dashboard anzuzeigen. Beachten Sie die Kommandos **Start**, **Stop** und **Löschen** am oberen Rand. Das Dienst-Dashboard enthält Kacheln zum Anzeigen von Eigenschaften, Schlüsseln und ein Schnellstartmenü mit Links zu Informationen und Anweisungen. Blättern Sie nach unten, um die Nutzung anzuzeigen.

5.  Klicken Sie auf **EIGENSCHAFTEN**. Beachten Sie, dass die Eigenschaftenseite rechts geöffnet wird. Die Dienst-URL befindet sich oben auf der Seite. Klicken Sie auf **SCHLÜSSEL**, um die API-Schlüssel für die Authentifizierung mit diesem Dienst anzuzeigen.

    ![][8]

<!--Next steps and links -->

## Ausprobieren

Bereit für den nächsten Schritt? Unter den folgenden Links finden Sie zusätzliches Lernmaterial für die Erstellung und Verwaltung von Suchanwendungen mit Azure Search.

-   [Erstellen Sie Ihre erste Azure Search-Lösung][Erstellen Sie Ihre erste Azure Search-Lösung]

-   [Erstellen eines Azure Search GeoSearch-Beispiels][Erstellen eines Azure Search GeoSearch-Beispiels]

-   [Verwalten Ihrer Suchlösung in Microsoft Azure][Verwalten Ihrer Suchlösung in Microsoft Azure]

-   [Azure Search: Technische Übersicht][Azure Search: Technische Übersicht]

-   [Azure Search REST-API][Azure Search REST-API]

-   [Channel 9-Video: Einführung in Azure Search][Channel 9-Video: Einführung in Azure Search]

-   [Channel 9-Video: Azure Search und Geodaten][Channel 9-Video: Azure Search und Geodaten]

-   [Cloud Cover-Episode 152: Generieren eines Index in Azure Search][Cloud Cover-Episode 152: Generieren eines Index in Azure Search]

<!--Anchors--> <!--Image references--> <!--Link references-->

  [Azure Search-Szenarien und -Funktionen]: http://azure.microsoft.com/blog/2014/08/28/azure-search-scenarios-and-capabilities/
  [Erste Schritte mit dem kostenlosen Dienst]: #sub-1
  [Upgrade auf die Standardsuche]: #sub-2
  [Testen der Dienstfunktionen]: #sub-3
  [Erkunden des Suchdienst-Dashboards]: #sub-4
  [Ausprobieren]: #next-steps
  [Azure-Vorschauportal]: https://portal.azure.com
  [0]: ./media/search-get-started/AzureSearch_Configure1_1_New.PNG
  [1]: ./media/search-get-started/AzureSearch_Configure1_2_Everything.PNG
  [2]: ./media/search-get-started/Azuresearch_Configure1_3_Gallery.PNG
  [3]: ./media/search-get-started/AzureSearch_Configure1_4_GallerySeeAll.PNG
  [4]: ./media/search-get-started/AzureSearch_Configure1_5_DataServicesList.PNG
  [5]: ./media/search-get-started/AzureSearch_Configure1_6_URL.PNG
  [6]: ./media/search-get-started/AzureSearch_Configure1_7_Free.PNG
  [7]: ./media/search-get-started/AzureSearch_Configure1_8_SearchServiceList.PNG
  [8]: ./media/search-get-started/AzureSearch_Configure1_18_Explore.PNG
  [9]: ./media/search-get-started/AzureSearch_Configure1_9_Standard.PNG
  [Preisdetails]: http://go.microsoft.com/fwlink/p/?LinkID=509792
  [10]: ./media/search-get-started/AzureSearch_Configure1_10_ScaleUp.PNG
  [kostenlosen Download von Telerik]: http://www.telerik.com/fiddler
  [11]: ./media/search-get-started/AzureSearch_Configure1_11_PUTIndex.PNG
  [12]: ./media/search-get-started/AzureSearch_Configure1_12_POSTDocs.PNG
  [13]: ./media/search-get-started/AzureSearch_Configure1_13_GETQuery.PNG
  [14]: ./media/search-get-started/AzureSearch_Configure1_14_GETQueryResponse.PNG
  [Suchindex-Operationen (Azure Search-API)]: http://msdn.microsoft.com/de-de/library/dn798927.aspx
  [15]: ./media/search-get-started/AzureSearch_Configure1_15_Stats.PNG
  [16]: ./media/search-get-started/AzureSearch_Configure1_16_StatsResponse.PNG
  [17]: ./media/search-get-started/AzureSearch_Configure1_17_BrowseEverything.PNG
  [Erstellen Sie Ihre erste Azure Search-Lösung]: ../search-create-first-solution/
  [Erstellen eines Azure Search GeoSearch-Beispiels]: ../search-create-geospatial/
  [Verwalten Ihrer Suchlösung in Microsoft Azure]: ../search-manage/
  [Azure Search: Technische Übersicht]: http://msdn.microsoft.com/de-de/library/dn798933.aspx
  [Azure Search REST-API]: http://msdn.microsoft.com/de-de/library/dn798935.aspx
  [Channel 9-Video: Einführung in Azure Search]: http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search
  [Channel 9-Video: Azure Search und Geodaten]: http://channel9.msdn.com/Shows/Data-Exposed/Azure-Search-and-Geospatial-Data
  [Cloud Cover-Episode 152: Generieren eines Index in Azure Search]: http://channel9.msdn.com/Shows/Cloud+Cover/Cloud-Cover-152-Azure-Search-with-Liam-Cavanagh
