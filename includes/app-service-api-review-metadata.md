## Überprüfen von API-App-Metadaten

Die Metadaten, dank derer ein Web-API-Projekt als API-App bereitgestellt werden kann, sind in der Datei *apiapp.json* und dem Ordner *Metadata* enthalten.

![](./media/app-service-api-review-metadata/metadatainse.png)

Der standardmäßige Inhalt der Datei *apiapp.json* sieht etwa wie im folgenden Beispiel aus:

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "ContactsList",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "ContactsList",
		    "summary": "",
		    "author": "",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    }
		}

Im Rahmen dieses Lernprogramms können Sie die Standardwerte übernehmen. Im Abschnitt [API-App-Metadaten](#api-app-metadata) später in diesem Lernprogramm wird erläutert, wie Sie diese Metadaten anpassen.
<!--HONumber=54-->