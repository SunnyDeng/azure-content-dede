<properties
	pageTitle="Verwenden von Chrome Postman mit Azure Search"
	description="Verwenden von Chrome Postman mit Azure Search"
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
	ms.date="07/08/2015"
	ms.author="heidist"/>

# Verwenden von Chrome Postman mit Azure Search #

Das Tool [Postman](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm "Chrome Postman") wird im Rahmen von Google Chrome bereitgestellt und ermöglicht Entwicklern die effiziente Verwendung REST-basierter API-Dienste wie Azure Search. Mit Postman können Sie schnell Suchindizes erstellen und abfragen, indem Sie API-Aufrufe über Postman senden. Hierzu müssen Sie keinerlei Code schreiben. Dadurch können Sie sich auf effiziente Weise mit der API vertraut machen und neue Features ausprobieren.

![][1]

## Anforderungen ##

Sie benötigen einen Azure Search-Dienst. Genau wie bei anderen benutzerdefinierten Anwendungen, die Azure Search verwenden, benötigen Sie auch hier die URL für Ihren Dienst sowie einen API-Administratorschlüssel (`api-key`), um den Index erstellen zu können. Anweisungen zum Ermitteln der Werte für Ihren Search-Dienst finden Sie unter [Erstellen eines Diensts im Portal](search-create-service-portal.md).

## Installieren von Postman ##
Postman kann über den [Google Chrome Store](https://chrome.google.com/webstore/detail/postman-rest-client/fdmmgilgnpjigdojojpjoooidkmcomcm) heruntergeladen werden. Über den Link auf dieser Seite können Sie den REST-Client für Postman herunterladen und installieren. Nach der Installation können Sie Postman über den Chrome App Launcher starten.

![][2]

## Konfigurieren von Postman zum Abfragen von Azure Search ##
Gehen Sie zum Konfigurieren von Postman wie folgt vor:

1. Geben Sie unter „Enter request URL here“ die URL Ihres Azure Search-Diensts ein.  
2. Fügen Sie der URL Folgendes an: `?api-version=2015-02-28`. Sie können auch eine andere API-Version angeben. Ausführliche Informationen finden Sie unter [Versionsverwaltung für den Azure-Suchdienst](https://msdn.microsoft.com/library/azure/dn864560.aspx).
3. Vergewissern Sie sich, dass `GET` ausgewählt ist.
4. Klicken Sie auf die Schaltfläche **Headers**.
5. Geben Sie Werte für Folgendes ein:
	- `api-key`: [Administratorschlüssel]
	- `Content-Type`: `application/json; charset=utf-8`
6. Klicken Sie auf **Send**, um den REST-Aufruf an Azure Search zu initiieren und die JSON-Antwort zu visualisieren.

![][3]

## Erstellen eines Azure Search-Index mit Postman ##

Hier gehen wir noch einen Schritt weiter und initiieren einen REST-Aufruf, um einen neuen Azure Search-Index zu erstellen. Im Gegensatz zum vorherigen Aufruf werden für die Indexerstellung eine HTTP PUT-Anforderung sowie ein JSON-Dokument mit der Definition des Indexschemas benötigt. In diesem Beispiel erstellen wir einen Index zur Speicherung einer Liste mit Wanderwegen. Gehen Sie dazu folgendermaßen vor:

1. Ändern Sie die URL in `https://[SEARCH SERVICE].search.windows.net/indexes/trails?api-version=2015-02-28`. Verwenden Sie dabei den Namen Ihres Search-Diensts.
2. Ändern Sie den Anforderungstyp von `GET` in `PUT`.
3. Geben Sie im unformatierten Textinhalt den folgenden JSON-Code ein:

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

## Senden von Dokumenten an einen Azure Search-Index mit Postman ##
Nachdem der Index nun erstellt ist, kann er mit Dokumenten gefüllt werden. Hierzu veröffentlichen wir eine Gruppe von Dokumenten mit fünf Wanderwegen aus dem Dataset des USGS (geologischer Dienst der USA) in einem Batch:

1. Ändern Sie die URL in `https://[SEARCH SERVICE].windows.net/indexes/trails/docs/index?api-version=2015-02-28`. Verwenden Sie dabei den Namen Ihres Search-Diensts. Beachten Sie, dass die URL einen Pfad zum soeben erstellten Index enthält.
2. Ändern Sie den HTTP-Typ in `POST`.
3. Geben Sie im unformatierten Textinhalt den folgenden JSON-Code ein:

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

## Abfragen des Index mit Postman ##
Im letzten Schritt fragen wir den Index ab und initiieren eine einfache Volltextsuche für das Wort *trail*.

1. Geben Sie Folgendes in die URL ein: `https://[SEARCH SERVICE].search.windows.net/indexes/trails/docs?api-version=2015-02-28&search=trail`. Verwenden Sie dabei den Namen Ihres Search-Diensts. Beachten Sie, dass die URL den Abfrageparameter `search` und den Suchbegriff *trail* enthält.
2. Ändern Sie den HTTP-Anforderungstyp in `GET`.
3. Klicken Sie auf **Send**.

Die Antwort enthält die von Azure Search zurückgegebenen JSON-Suchergebnisse.

![][6]

## Nächste Schritte ##
Nachdem Sie nun mit der grundlegenden Verwendung von Azure Search mit Postman vertraut sind, gibt es noch einige hilfreiche Punkte für die nächsten Schritte:

1. Mit `Collections` unterstützt Postman eine komfortable Möglichkeit zum Speichern häufig verwendeter Anforderungen. Sie können Auflistungen an andere Personen weitergeben, sodass sie sie in ihrem eigenen Exemplar von Postman verwenden können.
2. Informieren Sie sich in der Azure Search-Dokumentation über den HTTP-Anforderungstyp (`GET`, `PUT` usw.) für die einzelnen Aufrufe, und nehmen Sie in Postman ggf. entsprechende Änderungen vor.

Die Dokumentation für die REST-API finden Sie bei [MSDN](https://msdn.microsoft.com/library/azure/dn798935.aspx).

Weitere Beispiele finden Sie in der [Liste mit Videos und Lernprogrammen](https://msdn.microsoft.com/library/azure/dn818681.aspx).

<!-- Image References -->
[1]: ./media/search-chrome-postman/full_postman_client.png
[2]: ./media/search-chrome-postman/postman.png
[3]: ./media/search-chrome-postman/configure.png
[4]: ./media/search-chrome-postman/create_index.png
[5]: ./media/search-chrome-postman/upload_documents.png
[6]: ./media/search-chrome-postman/query.png
 

<!---HONumber=July15_HO4-->