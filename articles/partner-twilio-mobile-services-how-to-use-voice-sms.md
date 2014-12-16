<properties pageTitle="Verwenden von Twilio für Telefonie- und SMS-Funktionen | Mobile Dev Center" metaKeywords="" description="Learn how to perform common tasks using the Twilio API with Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to use Twilio for voice and SMS capabilities from Mobile Services" authors="MicrosoftHelp@twilio.com" solutions="" manager="twilio" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="10/03/2014" ms.author="MicrosoftHelp@twilio.com" />


<h1>Verwenden von Twilio für Telefonie- und SMS-Funktionen aus Mobile Services</h1>

Dieser Artikel beschreibt gängige Szenarien für die Verwendung der Twilio-API für Azure Mobile Services. Dieses Lernprogramm beschreibt die Erstellung benutzerdefinierter API-Skripts zur Verwendung der Twilio-API, um Telefonanrufe auszuführen und SMS-Nachrichten zu verschicken. 

<h2><a id="WhatIs"></a>Was ist Twilio?</h2>
Twilio ist ein leistungsstarkes und zukunftsorientiertes Tool für die Unternehmenskommunikation, mit dem Entwickler Telefonie-, VoIP- und Messagingfunktionalitäten in Anwendungen integrieren können. Die gesamte in einer cloudbasierten, globalen Umgebung benötigte Infrastruktur wird virtualisiert und über die Twilio-API-Kommunikationsplattform zur Verfügung gestellt. Anwendungen können einfach erstellt und skaliert werden. Sie profitieren von flexiblen Pay-as-you-go-Preisen und der Zuverlässigkeit der Cloud.

**Twilio Voice** ermöglicht Anwendungen das Tätigen und Entgegennehmen von Telefonanrufen. **Twilio SMS** ermöglicht Anwendungen das Senden und Empfangen von SMS-Nachrichten. **Twilio Client** ermöglicht Ihnen das Tätigen von VoIP-Anrufen von jedem Telefon, Tablet-PC oder Browser aus und unterstützt WebRTC.

<h2><a id="Pricing"></a>Twilio-Preise und -Sonderangebote</h2>
Azure-Kunden erhalten ein [Sonderangebot][special_offer]: eine Twilio-Gutschrift in Höhe von 10 US-Dollar bei einem Upgrade Ihres Twilio-Kontos. Diese Twilio-Gutschrift kann für jede beliebige Twilio-Nutzung ausgegeben werden (eine Gutschrift von 10 US-Dollar reicht für den Versand von 1.000 SMS-Nachrichten oder für bis zu 1.000 eingehende Telefonminuten, abhängig vom Standort der Telefonnummer und dem Nachrichten- oder Anrufziel). Lösen Sie diese Twilio-Gutschrift ein, und legen Sie los auf [ahoy.twilio.com/azure][special_offer].

Twilio ist ein Pay-as-you-go-Dienst, also vertragsungebunden. Es fallen keine Einrichtungsgebühren an, und Sie können Ihr Konto jederzeit schließen. Weitere Informationen finden Sie unter [Twilio-Preise][twilio_pricing].  

<h2><a id="Concepts"></a>Konzepte</h2>
Die Twilio-API ist eine RESTful-API, die Telefonie- und SMS-Funktionen für Anwendungen bereitstellt. Clientbibliotheken stehen in mehreren Sprachen zur Verfügung; eine Liste finden Sie auf der Seite der [Twilio-API-Bibliotheken] [twilio_libraries].  Zusätzliche Lernprogramme für die Verwendung von Twilio in Azure-Anwendungen sind verfügbar für [.NET][azure_twilio_howto_dotnet], [node.js][azure_twilio_howto_node], [Java][azure_twilio_howto_java], [PHP][azure_twilio_howto_php], [Python][azure_twilio_howto_python] und [Ruby][azure_twilio_howto_ruby].

Schlüsselaspekte der Twilio API sind Twilio-Verben und die Twilio Markup Language (TwiML).

<h3><a id="Verbs"></a>Twilio-Verben</h3>
Die API verwendet Twilio-Verben; so weist beispielsweise das Verb **&lt;Say&gt;** Twilio an, ein Nachricht in einem Anruf akustisch zu übermitteln. 

Nachfolgend finden Sie eine Liste mit Twilio-Verben.  Andere Verben und Funktionen werden in der [Dokumentation zur Twilio Markup Language](http://www.twilio.com/docs/api/twiml) erläutert.

* **&lt;Dial&gt;**: Verbindet den Anrufer mit einem anderen Telefon.
* **&lt;Gather&gt;**: Erfasst Ziffern, die über die Telefontasten eingegeben werden.
* **&lt;Hangup&gt;**: Beendet einen Anruf.
* **&lt;Play&gt;**: Gibt eine Audiodatei wieder.
* **&lt;Pause&gt;**: Wartet ohne Ton für eine angegebene Anzahl von Sekunden.
* **&lt;Record&gt;**: Zeichnet die Stimme des Anrufers auf und gibt eine URL einer Datei mit der Aufzeichnung zurück.
* **&lt;Redirect&gt;**: Überträgt die Steuerung eines Anrufs oder einer SMS an die TwiML an einer anderen URL.
* **&lt;Reject&gt;**: Lehnt einen bei Ihrer Twilio-Nummer eingehenden Anruf ab, ohne dass Ihnen Kosten entstehen.
* **&lt;Say&gt;**: Konvertiert Text in Sprache für einen Anruf.
* **&lt;Sms&gt;**: Sendet eine SMS-Nachricht.

<h3> <a id="TwiML"></a>TwiML</h3>
TwiML ist ein Satz XML-basierter Anweisungen auf Grundlage der Twilio-Verben, die Twilio anweisen, wie ein Anruf oder eine SMS verarbeitet werden soll.

Die TwiML im folgenden Beispiel konvertiert den Text **Hello World** in Sprache.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Wenn eine Anwendung die Twilio-API aufruft, ist einer der API-Parameter die URL, die die TwiML-Antwort zurückgibt. Zu Entwicklungszwecken können Sie die von Twilio zur Verfügung gestellten URLs verwenden, um die von Ihren Anwendungen verwendeten TwiML-Antworten bereitzustellen. Sie können auch Ihre eigenen URLs hosten, um TwiML-Antworten zu generieren; eine weitere Option ist die Verwendung des **TwiMLResponse**-Objekts.

Nähere Informationen zu Twilio-Verben, ihren Attributen sowie TwiML finden Sie unter [TwiML][twiml]. Weitere Informationen zur Twilio-API finden Sie unter [Twilio-API][twilio_api].

<h2><a id="CreateAccount"></a>Erstellen eines Twilio-Kontos</h2>
Wenn Sie ein Twilio-Konto erstellen möchten, melden Sie sich bei [Try Twilio][try_twilio] an. Sie können mit einem kostenlosen Konto beginnen und später ein Upgrade vornehmen.

Wenn Sie sich für ein Twilio-Konto anmelden, erhalten Sie eine Konto-ID und ein Authentifizierungstoken. Beide Angaben benötigen Sie zum Tätigen von Twilio-API-Anrufen. Um den unbefugten Zugriff auf Ihr Konto zu verhindern, bewahren Sie Ihr Authentifizierungstoken sicher auf. Ihre Konto-ID und das Authentifizierungstoken können Sie auf der [Twilio-Kontoseite][twilio_account] in den Feldern **ACCOUNT SID** bzw. **AUTH TOKEN** einsehen.

<h2><a id="VerifyPhoneNumbers"></a>Überprüfen von Telefonnummern</h2>
Verschiedene Telefonnummern müssen bei Twilio für Ihr Konto verifiziert werden. Wenn Sie beispielsweise ausgehende Telefonate tätigen möchten, muss die Telefonnummer als ausgehende Anrufer-ID bei Twilio verifiziert werden. Wenn Sie SMS-Nachrichten an eine Telefonnummer senden möchten, muss entsprechend die Telefonnummer des Empfängers bei Twilio verifiziert werden. Informationen zum Überprüfen von Telefonnummern finden Sie unter [Manage Numbers] [verify_phone]. In einigen der folgenden Codebeispiele werden Telefonnummern verwendet, die Sie bei Twilio verifizieren müssen.

Alternativ zur Verwendung einer vorhandenen Telefonnummer für Ihre Anwendungen besteht die Möglichkeit, eine Twilio-Telefonnummer zu kaufen. Informationen zum Kaufen einer Twilio-Telefonnummer finden Sie in der [Twilio-Hilfe zu Telefonnummern](https://www.twilio.com/help/faq/phone-numbers).

<h2><a id="create_app"></a>Erstellen eines Mobile Service</h2>
Ein Mobile Service, der eine Twilio-fähige Anwendung hostet, unterscheidet sich nicht von anderen Mobile Services. Sie fügen einfach die Twilio Node.js-Bibliothek hinzu, um aus Ihren benutzerdefinierten Mobile Services API-Skripts auf diese Bibliothek verweisen zu können. Weitere Informationen zur Erstellung Ihres ersten Mobile Service finden Sie unter [Erste Schritte mit Mobile Services](/de-de/documentation/articles/mobile-services-ios-get-started/).

<h2><a id="ConfigureMobileService"></a>Konfigurieren einer Anwendung für die Verwendung der Twilio Node.js-Bibliothek</h2>
Twilio stellt eine Node.js-Bibliothek zur Verfügung, die verschiedene Aspekte von Twilio abdeckt, um eine einfache und problemlose Interaktion mit der Twilio-REST-API und dem Twilio-Client zum Generieren von TwiML-Antworten zu bieten.

Sie müssen das npm-Modul von Mobile Services verwenden, um die Twilio Node.js-Bibliothek in Ihrem Mobile Service nutzen zu können. Legen Sie Ihre Skripts dazu in einer Quellcodeverwaltung ab. 

1. Schließen Sie das Lernprogramm [Speichern von Skripts in der Quellcodeverwaltung](/de-de/documentation/articles/mobile-services-store-scripts-source-control/) ab. Darin werden das Einrichten der Quellcodeverwaltung für Ihre mobilen Dienste und das Speichern von Serverskripts in einem Git-Verzeichnis erläutert.

2. Nachdem Sie die Quellcodeverwaltung für Ihren mobilen Dienst eingerichtet haben, öffnen Sie das Verzeichnis auf dem lokalen Computer, navigieren Sie zum Unterordner `\services`, öffnen Sie die Datei "package.json" in einem Texteditor und fügen Sie dem **dependencies**-Objekt das folgende Feld hinzu:

		"twilio": "~1.7.0"
 
3. Nachdem Sie den Twilio-Paketverweis zum **dependencies**-Objekt hinzugefügt haben, sollte die Datei "package.json" folgendermaßen aussehen:

		{
		  "name": "todolist",
		  "version": "1.0.0",
		  "description": "todolist - hosted on Windows Azure Mobile Services",
		  "main": "server.js",
		  "engines": {
		    "node": ">= 0.8.19"
		  },
		  "dependencies": {
			"twilio": "~1.7.0" 
		  },
		  "devDependencies": {},
		  "scripts": {},
		  "author": "unknown",
		  "licenses": [],
		  "keywords":[]
		}

	>[WACOM.NOTE]Die Abhängigkeit für Twilio sollte hinzugefügt werden als `"twilio": "~1.7.0"`, mit (~). Verweise mit Caret-Zeichen (^) werden nicht unterstützt. 

4. Bestätigen Sie diese Dateiaktualisierung, und übertragen Sie die Aktualisierung per Pushvorgang auf den mobilen Dienst.

	Durch die Aktualisierung der Datei "package.json" wird der mobile Dienst neu gestartet.
	
Der mobile Dienst installiert und lädt nun das Twilio-Paket, sodass Sie die Twilio-Bibliothek in Ihrer benutzerdefinierten API und in Tabellenskripts verwenden und Verweise zu ihr erstellen können.

<h2><a id="howto_make_call"></a>Gewusst wie: Tätigen von ausgehenden Anrufen</h2>
Das folgende Skript zeigt, wie Sie mit der **makeCall**-Funktion einen ausgehenden Anruf aus Ihrem mobilen Dienst ausführen können. Dieser Code verwendet eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort (Twilio Markup Language). Geben Sie Ihre Werte für die Telefonnummern **From** und **To** ein, und stellen Sie sicher, dass Sie die **From**-Telefonnummer für Ihr Twilio-Konto verifizieren, bevor Sie den Code ausführen.

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://www.example.com/twiml.php' 

        }, function(err, responseData) {
            console.log(responseData.from); 
            response.send(200, '');
        });
    };

Weitere Informationen zu den an die **client.makeCall**-Funktion übergebenen Parametern finden Sie unter [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Wie bereits erwähnt, verwendet dieser Code eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort. Stattdessen können Sie die TwiML-Antwort auch von Ihrer eigenen Website bereitstellen lassen. Weitere Informationen finden Sie unter [Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website](#howto_provide_twiml_responses).

<h2><a id="howto_send_sms"></a>Gewusst wie: Senden einer SMS-Nachricht</h2>
Der folgende Code zeigt, wie Sie eine SMS-Nachricht mit der**sendSms**-Funktion senden. Die **From**-Nummer wird von Twilio für Testkonten zum Senden von SMS-Nachrichten bereitgestellt. Die **To**-Nummer muss für Ihr Twilio-Konto verifiziert werden, bevor Sie den Code ausführen.

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');
 
        client.sendSms({
            to:'[]',
            from:'[]',
            body:'ahoy hoy! Testing Twilio and node.js'
        }, function(error, message) {
    
            // The "error" variable will contain error information, if any.
            // If the request was successful, this value will be "false"
            if (!error) {
                console.log('Success! The SID for this SMS message is: ' + message.sid);
                console.log('Message sent on: ' + message.dateCreated);
            }
            else {
                console.log('Oops! There was an error.');
            }
            response.send(200, { error : error } );
        });
    };


<h2><a id="howto_provide_twiml_responses"></a>Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website</h2>

Wenn Ihre Anwendung einen Anruf an die Twilio-API initiiert - z. B. über die client.InitiateOutboundCall-Methode - sendet Twilio Ihre Anforderung an eine URL, von der die Rückgabe einer TwiML-Antwort erwartet wird. Das Beispiel unter "Gewusst wie: Tätigen eines ausgehenden Anrufs verwendet die von Twilio bereitgestellte URL http://twimlets.com/message für die Rückgabe der Antwort.

<div class="dev-callout">
<b>Hinweis</b>
<p>TwiML wurde für die Verwendung durch Webdienste konzipiert, kann jedoch im Browser angezeigt werden. Klicken Sie z. B. auf <a href="http://twimlets.com/message">twimlet_message_url</a>, um ein leeres &lt;Response&gt;-Element anzuzeigen, oder klicken Sie auf <a href="http://twimlets.com/message?Message%5B0%5D=Hello%20World">twimlet_message_url_hello_world</a>, um ein &lt;Response&gt;-Element mit einem &lt;Say&gt;-Element anzuzeigen.</p>
</div>

Anstelle der von Twilio bereitgestellten URL können Sie auch Ihre eigene URL-Website für die Rückgabe von HTTP-Antworten erstellen. Sie können die Website mit jeder Sprache erstellen, die HTTP-Antworten zurückgibt. In diesem Thema wird davon ausgegangen, dass Sie die URL von einem generischen ASP.NET-Handler hosten.

Das folgende Skript erstellt eine TwiML-Antwort, die beim Anruf Hello World sagt.

    var twilio = require('twilio');

    exports.post = function(request, response) {
        var resp = new twilio.TwimlResponse();
        resp.say({voice:'woman'}, 'ahoy hoy! Testing Twilio and node.js');
        response.set('Content-Type', 'text/xml');
        response.send(200, resp.toString());
    };

Weitere Informationen zu TwiML finden Sie auf [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml).

Nachdem Sie die URL zur Bereitstellung von TwiML-Antworten eingerichtet haben, können Sie diese URL wie hier gezeigt an die **client.makeCall**-Methode übergeben:
    
    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');

        client.makeCall({
            to:'+16515556677', 
            from: '+14506667788',
            url: 'http://<your_mobile_service>.azure-mobile.net/api/makeCall' 

        }, function(err, responseData) {

            console.log(responseData.from);
            response.send(200, '');
        });
    };

[WACOM.INCLUDE [twilio_additional_services_and_next_steps](../includes/twilio_additional_services_and_next_steps.md)]


[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#


[azure_twilio_howto_dotnet]: /de-de/develop/net/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_java]: /de-de/develop/java/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_node]: /de-de/develop/nodejs/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_ruby]: /de-de/develop/ruby/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_python]: /de-de/develop/python/how-to-guides/twilio-voice-and-sms-service/
[azure_twilio_howto_php]: /de-de/develop/php/how-to-guides/twilio-voice-and-sms-service/
