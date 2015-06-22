<properties 
	pageTitle="Gewusst wie: Verwenden von Chrome Postman mit Azure Search" 
	description="Gewusst wie: Verwenden von Chrome Postman mit Azure Search" 
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
	ms.date="04/27/2015" 
	ms.author="heidist"/>

# Gewusst wie: Verwenden von Chrome Postman mit Azure Search #

[Postman](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm "Chrome Postman") ist ein Tool, das als Bestandteil von Google Chrome bereitgestellt wird und Entwicklern die effiziente Arbeit mit REST-basierten API-Diensten, z. B. Azure Search, ermöglicht. Sie können Postman verwenden, um Ihre Suchindizes schnell zu erstellen und abzufragen, indem Sie API-Aufrufe über Postman senden, ohne Code schreiben zu müssen. Diese Vorgehensweise ist eine effiziente Möglichkeit, sich mit der API vertraut zu machen und neue Funktionen auszuprobieren.

![][1]
 
## Anforderungen ##

Sie benötigen einen Azure-Suchdienst. Wie bei jeder benutzerdefinierten Anwendung, in der Azure Search verwendet wird, benötigen Sie die URL zu Ihrem Dienst sowie einen  `api-key` für Administratoren, sodass Sie den Index erstellen können. Anweisungen dazu, wie Sie die Werte für Ihren Suchdienst abrufen können, finden Sie unter [Erstellen eines Diensts im Portal](../Search-erstellen-Service-portal.md).

## Installieren von Postman ##
Um Postman herunterzuladen, wechseln Sie zum [Google Chrome Store](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm). Über den Link auf dieser Seite können Sie den REST-Client für Postman herunterladen und installieren. Sobald Postman installiert ist, können Sie das Tool aus dem Startprogramm für Chrome-Apps starten.

![][2]
 
## Konfigurieren von Postman zum Abfragen von Azure Search ##
Führen Sie die folgenden Schritte aus, um Postman zu konfigurieren: 

1. Geben Sie die URL Ihres Azure-Suchdiensts dort ein, wo "Enter request URL here" angezeigt wird.  
2. Hängen Sie Folgendes an die URL an: `?api-version=2015-02-28`. Sie können auch eine andere API-Version angeben. Weitere Informationen hierzu finden Sie unter [Versionsverwaltung für den Azure-Suchdienst](https://msdn.microsoft.com/library/azure/dn864560.aspx).
3. Vergewissern Sie sich, dass `GET` ausgewählt ist.
4. Klicken Sie auf die Schaltfläche **Headers**.
5. Geben Sie einen Wert ein für:
	- `api-key`:  [Administratorschlüssel]
	- `Content-Type`: `application/json; charset=utf-8`
6. Klicken Sie auf **Send**, um den REST-Aufruf an Azure Search zu senden und die JSON-Antwort anzuzeigen.

![][3]

## Erstellen eines Azure Search-Indexes mit Postman ##

Im nächsten Vorgang wird das erweitert, was im letzten Schritt abgeschlossen wurde. Dazu wird ein REST-Aufruf ausgegeben, um einen neuen Azure Search-Index zu erstellen. Im Gegensatz zum vorherigen Aufruf ist für die Indexerstellung ein HTTP PUT sowie ein JSON-Dokument mit der Definition des Indexschemas erforderlich. Für dieses Beispiel soll ein Index erstellt werden, der eine Liste von Wanderwegen speichert. Gehen Sie dazu folgendermaßen vor:

1. Ändern Sie die URL in: `https://[SUCHDIENST].search.windows.net/indexes/trails?api-version=2015-02-28`, wobei Sie den Namen Ihres Suchdiensts verwenden.
2. Ändern Sie den Typ der Anforderung von `GET` in `PUT`.
3. Geben Sie in den unformatierten Textteil die folgende JSON ein:

	    {
	    "name": "trails", 
	    "fields": [
	    {"name": "id", "type": "Edm.String", "key": true, "searchable": false}, 
	    {"name": "name", "type": "Edm.String"}, 
	    {"name": "county", "type": "Edm.String"}, 
	    {"name": "elevation", "type": "Edm.Int32"}, 
	    {"name": "location", "type": "Edm.GeographyPoint"} ]
	    }

4. Klicken Sie auf **Send**.

![][4]
 
## Senden von Dokumenten an einen Azure Search-Index mit Postman ##
Nachdem der Index nun erstellt ist, können Dokumente in ihn geladen werden. Dazu wird in einem Batch eine Gruppe von fünf Dokumenten gesendet, wozu die Daten für fünf Wanderwege aus dem USGS-Datenbestand (United States Geological Survey) verwendet werden: 

1. Ändern Sie die URL in: `https://[SUCHDIENST].windows.net/indexes/trails/docs/index?api-version=2015-02-28`, wobei Sie den Namen Ihres Suchdiensts verwenden. Beachten Sie, dass die URL einen Pfad zu dem Index enthält, den Sie soeben erstellt haben.
2. Ändern Sie den HTTP-Typ in `POST`.
3. Geben Sie in den unformatierten Textteil die folgende JSON ein:

	    {
	      "value": [
		    {"@search.action": "upload", "id": "233358", "name": "Pacific Crest National Scenic Trail", "county": "San Diego", "elevation":1294, "location": { "type": "Point", "coordinates": [-120.802102,49.00021] }},
		    {"@search.action": "upload", "id": "801970", "name": "Lewis and Clark National Historic Trail", "county": "Richland", "elevation":584, "location": { "type": "Point", "coordinates": [-104.8546903,48.1264084] }},
		    {"@search.action": "upload", "id": "1144102", "name": "Intake Trail", "county": "Umatilla", "elevation":1076, "location": { "type": "Point", "coordinates": [-118.0468873,45.9981939] }},
		    {"@search.action": "upload", "id": "1509897", "name": "Burke Gilman Trail", "county": "King", "elevation":10, "location": { "type": "Point", "coordinates": [-122.2754036,47.7059315] }},
		    {"@search.action": "upload", "id": "1517508", "name": "Cavanaugh-Oso Truck Trail", "county": "Skagit", "elevation":339, "location": { "type": "Point", "coordinates": [-121.9470829,48.2981608] }}
	      ]
	    }
    
4. Klicken Sie auf **Send**.

![][5]

## Abfragen des Indexes mit Postman ##
Der letzte Schritt besteht darin, den Index abzufragen und eine einfache Anforderung zur Volltextsuche für das Wort *trail* auszugeben. 

1. Geben Sie Folgendes in das Feld für die URL ein: `https://[SEARCH SERVICE].search.windows.net/indexes/trails/docs?api-version=2015-02-28&search=trail`, wobei Sie den Namen Ihres Suchdiensts verwenden. Beachten Sie, dass die URL den Abfrageparameter `search` und den Suchbegriff *trail* enthält.
2. Ändern Sie den Typ der HTTP-Anforderung in `GET`.
3. Klicken Sie auf **Send**.
 
In der Antwort sollten Sie das JSON-Suchergebnis sehen, das von Azure Search zurückgegeben wurde.

![][6]

## Nächste Schritte ##
Nachdem Sie die Grundlagen des Verwendens von Azure Search mit Postman kennen gelernt haben, gibt es einige Dinge, die Sie für die nächsten Schritte wissen sollten:

1. Postman unterstützt `Collections` (Sammlungen), die eine bequeme Möglichkeit bieten, häufig ausgegebene Anforderungen zu speichern. Sie können Sammlungen für andere Personen freigeben, sodass diese die Sammlungen in ihren eigenen Kopien von Postman ausgeben können.
2. Sehen Sie sich in der Azure Search-Dokumentation den HTTP-Anforderungstyp (`GET`, `PUT` usw.) an, der jedem Aufruf zugeordnet ist, und nehmen Sie entsprechende Änderungen in Postman vor.

Die Dokumentation für die REST-API finden Sie auf [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Sie können auch zur [Liste der Videos und Lernprogramme](https://msdn.microsoft.com/library/azure/dn818681.aspx) wechseln, um sich weitere Beispiele anzusehen.

<!-- Image References -->
[1]: ./media/search-chrome-postman/full_postman_client.png
[2]: ./media/search-chrome-postman/postman.png
[3]: ./media/search-chrome-postman/configure.png
[4]: ./media/search-chrome-postman/create_index.png
[5]: ./media/search-chrome-postman/upload_documents.png
[6]: ./media/search-chrome-postman/query.png

<!--HONumber=54--> 