<properties 
	pageTitle="Verwenden von Blitline zur Bildverarbeitung - Leitfaden zu Azure-Features" 
	description="Erfahren Sie mehr über die Verwendung des Blitline-Dienstes zum Verarbeiten von Bildern in einer Azure-Anwendung." 
	services="" 
	documentationCenter=".net" 
	authors="blitline-dev" 
	manager="jason@blitline.com" 
	editor="jason@blitline.com"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/09/2014" 
	ms.author="support@blitline.com"/>






# Verwenden von Blitline mit Azure und Azure Storage

Dieser Leitfaden erläutert den Zugriff auf Blitline-Dienste und das Übertragen von Jobs zu Blitline.

## Inhaltsverzeichnis

[Was ist Blitline?][] 
[Was ist Blitline NICHT][] 
[Erstellen eines Blitline-Kontos][] 
[Erstellen eines Blitline-Jobs][] 
[Speichern eines Bilds in Azure Storage][] 
[Nächste Schritte][]

## <a id="whatis"></a>Was ist Blitline?

Blitline ist ein cloudbasierter Bildverarbeitungsdienst, der Bildverarbeitung auf Unternehmensebene zu einem Bruchteil des Preises bereitstellt, der das Erstellen eines eigenen Diensts kosten würde.

Bildverarbeitung wurde immer wieder überarbeitet und normalweise für jede Website von Grund auf neu erstellt. Das wissen wir, weil wir selbst unzählige Lösungen dieser Art implementiert haben. Eines Tages haben wir entschieden, dass vielleicht die Zeit für einen allgemeinen Ansatz gekommen ist. Wir wissen, wie es geht, sind schnell und effizient und sorgen dabei noch für Zeiteinsparungen.

Weitere Informationen erhalten Sie unter [http://www.blitline.com](http://www.blitline.com).

## <a id="whatisnot"></a>Was Blitline NICHT ist

Um zu erläutern, wozu Blitline nützlich ist, ist es zunächst häufig einfacher, zu beschreiben, was Blitline NICHT ist.

- Blitline hat KEINE HTML-Widgets zum Hochladen von Bildern. Bilder müssen öffentlich oder mit eingeschränkten Rechten verfügbar sein, damit Blitline sie verwenden kann.

- Blitline führt, zum Beispiel im Gegensatz zu Aviary.com, KEINE Livebildverarbeitung durch.

- Blitline akzeptiert KEINE Bilduploads – Sie können Bilder nicht direkt per Push an Blitline übertragen. Sie müssen sie per Push an Azure Storage oder andere von Blitline unterstützte Speicherorte übertragen und Blitline dann mitteilen, wo sie abgerufen werden können.

- Blitline arbeitet weitestgehend parallel und führt KEINE synchrone Verarbeitung durch. Sie müssen also einen Wert für "postback_url" angeben und werden informiert, sobald die Verarbeitung abgeschlossen ist.

## <a id="createaccount"></a>Erstellen eines Blitline-Kontos

[AZURE.INCLUDE [blitline-signup](../includes/blitline-signup.md)]

## <a id="createjob"></a>Erstellen eines Blitline-Auftrags

Blitline verwendet JSON, um die Aktionen für ein Bild zu definieren. JSON besteht aus einigen einfachen Feldern.

Es folgt das einfachste Beispiel:

	    json : '{
       "application_id": "MY_APP_ID",
       "src" : "http://cdn.blitline.com/filters/boys.jpeg",
       "functions" : [ {
           "name": "resize_to_fit",
           "params" : { "width": 240, "height": 140 },
           "save" : { "image_identifier" : "external_sample_1" }
       } ]
    }'

Hier ruft JSON ein "src"-Bild namens "...boys.jpeg" ab und ändert dann die Größe in 240x140.

Die Anwendungs-ID finden Sie auf der Registerkarte **Verbindungsinformationen** oder **Verwalten** in Azure. Dabei handelt es sich um Ihre geheime ID zum Ausführen von Jobs in Blitline.

Der "save"-Parameter gibt Informationen zum Ablageort des Bilds nach Abschluss der Verarbeitung an. In diesem einfachen Fall wurde keiner angegeben. Wenn kein Speicherort definiert wird, speichert Blitline das Bild lokal (und temporär) an einem eindeutigen Cloudspeicherplatz. Sie können diesen Speicherort von JSON abrufen, der von Blitline beim Erstellen der Blitline zurückgegeben wird. Die "image"-ID ist erforderlich und wird zurückgegeben, um dieses spezielle gespeicherte Bild zu identifizieren.

Weitere Informationen zu den unterstützten *Funktionen* erhalten Sie hier: <http://www.blitline.com/docs/functions> (in englischer Sprache)

Eine Dokumentation zu den Joboptionen erhalten Sie hier: <http://www.blitline.com/docs/api> (in englischer Sprache)

Nach dem Erstellen müssen Sie das JSON-Objekt nur noch per **POST** an `http://api.blitline.com/jobs` übertragen.

Sie erhalten ein JSON-Objekt ähnlich dem folgenden:

    {
     "results":
         {"images":
            [{
              "image_identifier":"external_sample_1",
              "s3_url":"https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg"
            }],
          "job_id":"4eb8c9f72a50ee2a9900002f"
         }
    }


Dadurch wissen Sie, dass Blitline Ihre Anforderung erhalten und in eine Verarbeitungswarteschlange eingereiht hat. Nach Abschluss der Verarbeitung ist das Bild hier verfügbar: **https://s3.amazonaws.com/dev.blitline/2011110722/YOUR_APP_ID/CK3f0xBF_2bV6wf7gEZE8w.jpg**

## <a id="saveazure"></a>Speichern eines Bilds im Azure-Speicherkonto

Wenn Sie über eine Azure Storage-Konto verfügen, kann Blitline die verarbeiteten Bilder auch per Push in Ihren Azure-Container übertragen. Durch Hinzufügen von "azure_destination" definieren Sie den Speicherort und die Berechtigungen für den Push durch Blitline.

Beispiel:

    job : '{
      "application_id": "YOUR_APP_ID",
      "src" : "http://www.google.com/logos/2011/houdini11-hp.jpg",
         "functions" : [{
         "name": "blur",
         "save" : {
             "image_identifier" : "YOUR_IMAGE_IDENTIFIER",
             "azure_destination" : {
                 "account_name" : "YOUR_AZURE_CONTAINER_NAME",
                 "shared_access_signature" : "SAS_THAT_GIVES_BLITLINE_PERMISSION_TO_WRITE_THIS_OBJECT_TO_CONTAINER",
               }
           }
         }]
       }'


Wenn Sie die Werte in GROSSBUCHSTABEN durch eigene ersetzen, können Sie das JSON-Objekt an http://api.blitline.com/job übertragen. Das "src"-Bild wird mit einem Unschärfefilter verarbeitet und dann per Push an Ihr Azure-Ziel übertragen.

<h3>Hinweis:</h3>

Die SAS muss die gesamte SAS-URL einschließlich Dateiname der Zieldatei enthalten.

Beispiel:

    http://blitline.blob.core.windows.net/sample/image.jpg?sr=b&sv=2012-02-12&st=2013-04-12T03%3A18%3A30Z&se=2013-04-12T04%3A18%3A30Z&sp=w&sig=Bte2hkkbwTT2sqlkkKLop2asByrE0sIfeesOwj7jNA5o%3D


Weitere Informationen finden Sie auch [hier](http://www.blitline.com/docs/azure_storage) in den Azure Storage-Dokumenten von Blitline.


## <a id="nextsteps"></a>Nächste Schritte

Besuchen Sie "blitline.com", um Informationen zu allen anderen Funktionen zu erhalten:

* Blitline-API-Endpunktdokumente <http://www.blitline.com/docs/api>
* Blitline-API-Funktionen <http://www.blitline.com/docs/functions>
* Blitline-API-Beispiele <http://www.blitline.com/docs/examples>
* Drittanbieter-NuGet-Bibliothek ahttp://nuget.org/packages/Blitline.Net>


[Nächste Schritte]: #nextsteps
[Was ist Blitline?]: #whatis
[Was ist Blitline NICHT]: #whatisnot
[Erstellen eines Blitline-Kontos]: #createaccount
[Erstellen eines Blitline-Jobs]: #createjob
[Speichern eines Bilds in Azure Storage]: #saveazure


<!--HONumber=52-->
