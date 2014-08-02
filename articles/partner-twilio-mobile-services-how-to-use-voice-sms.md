<properties linkid="develop-mobile-tutorials-twilio-for-voice-and-sms" pageTitle="Use Twilio for Voice and SMS Capabilities | Mobile Dev Center" metaKeywords="" description="Learn how to perform common tasks using the Twilio API with Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use Twilio for voice and SMS capabilities from Mobile Services" authors="twilio" solutions="" manager="" editor="" />

Verwenden von Twilio für Telefonie- und SMS-Funktionen aus Mobile Services
==========================================================================

Dieser Artikel beschreibt gängige Szenarien für die Verwendung der Twilio-API für Azure Mobile Services. Dieses Lernprogramm beschreibt die Erstellung benutzerdefinierter API-Skripts zur Verwendung der Twilio-API, um Telefonanrufe auszuführen und SMS-Nachrichten zu verschicken.

Was ist Twilio?
---------------

Twilio ist ein leistungsstarkes und zukunftsorientiertes Tool für die Unternehmenskommunikation, mit dem Entwickler Telefonie-, VoIP- und Messagingfunktionalitäten in Anwendungen einbetten können. Die gesamte in einer cloudbasierten, globalen Umgebung benötigte Infrastruktur wird virtualisiert und über die Twilio-API-Kommunikationsplattform zur Verfügung gestellt. Anwendungen können einfach erstellt und skaliert werden. Sie profitieren von flexiblen Pay-as-you-go-Preisen und der Zuverlässigkeit der Cloud.

**Twilio Voice** ermöglicht Anwendungen das Tätigen und Entgegennehmen von Telefonanrufen. **Twilio SMS** ermöglicht Anwendungen das Senden und Empfangen von SMS-Nachrichten. **Twilio Client** ermöglicht Ihnen das Tätigen von VoIP-Anrufen von jedem Telefon, Tablet-PC oder Browser und unterstützt WebRTC.

Twilio-Preise und -Sonderangebote
---------------------------------

Azure-Kunden erhalten ein [Sonderangebot](http://ahoy.twilio.com/azure): eine Twilio-Gutschrift in Höhe von 10 US-Dollar bei einem Upgrade Ihres Twilio-Kontos. Diese Twilio-Gutschrift kann für jede beliebige Twilio-Nutzung ausgegeben werden (eine Gutschrift von 10 US-Dollar reicht für den Versand von 1.000 SMS-Nachrichten oder für bis zu 1.000 eingehende Telefonminuten, abhängig vom Standort der Telefonnummer und dem Nachrichten- oder Anrufziel). Lösen Sie diese Twilio-Gutschrift ein, und legen Sie los auf [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio ist ein Pay-as-you-go-Dienst, also vertragsungebunden. Es fallen keine Einrichtungsgebühren an, und Sie können Ihr Konto jederzeit schließen. Weitere Informationen finden Sie unter [Twilio Pricing](http://www.twilio.com/pricing).

Konzepte
--------

Die Twilio-API ist eine RESTful-API, die Telefonie- und SMS-Funktionen für Anwendungen bereitstellt. Clientbibliotheken stehen in mehreren Sprachen zur Verfügung; eine Liste finden Sie auf der Seite der [Twilio-API-Bibliotheken](https://www.twilio.com/docs/libraries). Zusätzliche Lernprogramme für die Verwendung von Twilio in Azure-Anwendungen sind verfügbar für [.NET](/en-us/develop/net/how-to-guides/twilio-voice-and-sms-service/), [node.js](/en-us/develop/nodejs/how-to-guides/twilio-voice-and-sms-service/), [Java](/en-us/develop/java/how-to-guides/twilio-voice-and-sms-service/), [PHP](/en-us/develop/php/how-to-guides/twilio-voice-and-sms-service/), [Python](/en-us/develop/python/how-to-guides/twilio-voice-and-sms-service/) und [Ruby](/en-us/develop/ruby/how-to-guides/twilio-voice-and-sms-service/).

Schlüsselaspekte der Twilio API sind Twilio-Verben und die Twilio Markup Language (TwiML).

### Twilio-Verben

Die API verwendet Twilio-Verben; so weist beispielsweise das Verb **&lt;Say\>** Twilio an, ein Nachricht in einem Anruf akustisch zu übermitteln.

Nachfolgend finden Sie eine Liste mit Twilio-Verben. Andere Verben und Funktionen werden in der [Dokumentation zur Twilio Markup Language](http://www.twilio.com/docs/api/twiml) erläutert.

-   **&lt;Dial\>**: Verbindet den Anrufer mit einem anderen Telefon.
-   **&lt;Gather\>**: Erfasst Ziffern, die über die Telefontasten eingegeben werden.
-   **&lt;Hangup\>**: Beendet einen Anruf.
-   **&lt;Play\>**: Gibt eine Audiodatei wieder.
-   **&lt;Pause\>**: Wartet ohne Ton für eine angegebene Anzahl von Sekunden.
-   **&lt;Record\>**: Zeichnet die Stimme des Anrufers auf und gibt eine URL einer Datei mit der Aufzeichnung zurück.
-   **&lt;Redirect\>**: Überträgt die Steuerung eines Anrufs oder einer SMS an die TwiML an einer anderen URL.
-   **&lt;Reject\>**: Lehnt einen bei Ihrer Twilio-Nummer eingehenden Anruf ab, ohne dass Ihnen Kosten entstehen
-   **&lt;Say\>**: Konvertiert Text in Sprache für einen Anruf.
-   **&lt;Sms\>**: Sendet eine SMS-Nachricht.

### TwiML

TwiML ist ein Satz XML-basierter Anweisungen auf Grundlage der Twilio-Verben, die Twilio anweisen, wie ein Anruf oder eine SMS verarbeitet werden soll.

Die TwiML im folgenden Beispiel konvertiert den Text **Hello World** in Sprache.

    <
    xml version="1.0" encoding="UTF-8" 
    >
    <Response>
       <Say>Hello World</Say>
    </Response>

Wenn eine Anwendung die Twilio-API aufruft, ist einer der API-Parameter die URL, welche die TwiML-Antwort zurückgibt. Zu Entwicklungszwecken können Sie die von Twilio zur Verfügung gestellten URLs verwenden, um die von Ihren Anwendungen verwendeten TwiML-Antworten bereitzustellen. Sie können auch Ihre eigenen URLs hosten, um TwiML-Antworten zu generieren; eine weitere Option ist die Verwendung des **TwiMLResponse**-Objekts.

Nähere Informationen zu Twilio-Verben, ihren Attributen sowie TwiML finden Sie unter [TwiML](http://www.twilio.com/docs/api/twiml). Weitere Informationen zur Twilio-API finden Sie unter [Twilio-API](http://www.twilio.com/api).

Erstellen eines Twilio-Kontos
-----------------------------

Wenn Sie ein Twilio-Konto erstellen möchten, melden Sie sich bei [Try Twilio](https://www.twilio.com/try-twilio) an. Sie können mit einem kostenlosen Konto beginnen und später ein Upgrade vornehmen.

Wenn Sie sich für ein Twilio-Konto anmelden, erhalten Sie eine Konto-ID und ein Authentifizierungstoken. Beide Angaben benötigen Sie zum Tätigen von Twilio-API-Anrufen. Um den unbefugten Zugriff auf Ihr Konto zu verhindern, bewahren Sie Ihr Authentifizierungstoken sicher auf. Ihre Konto-ID und das Authentifizierungstoken können Sie auf der [Twilio-Kontoseite](https://www.twilio.com/user/account) in den Feldern **ACCOUNT SID** bzw. **AUTH TOKEN** einsehen.

Verifizieren von Telefonnummern
-------------------------------

Verschiedene Telefonnummern müssen bei Twilio für Ihr Konto verifiziert werden. Wenn Sie beispielsweise ausgehende Telefonate tätigen möchten, muss die Telefonnummer als ausgehende Anrufer-ID bei Twilio verifiziert werden. Wenn Sie SMS-Nachrichten an eine Telefonnummer senden möchten, muss entsprechend die Telefonnummer des Empfängers bei Twilio verifiziert werden. Informationen zum Verifizieren von Telefonnummern finden Sie unter [Manage Numbers](https://www.twilio.com/user/account/phone-numbers/verified#) (in englischer Sprache). In einigen der folgenden Codebeispiele werden Telefonnummern verwendet, die Sie bei Twilio verifizieren müssen.

Alternativ zur Verwendung einer vorhandenen Telefonnummer für Ihre Anwendungen besteht die Möglichkeit, eine Twilio-Telefonnummer zu kaufen. Informationen zum Kaufen einer Twilio-Telefonnummer finden Sie unter [Twilio Phone Numbers Help](https://www.twilio.com/help/faq/phone-numbers) (in englischer Sprache).

Erstellen eines Mobile Service
------------------------------

Ein Mobile Service, der eine Twilio-fähige Anwendung hostet, unterscheidet sich nicht von anderen Mobile Services. Sie fügen einfach die Twilio Node.js-Bibliothek hinzu, um aus Ihren benutzerdefinierten Mobile Services API-Skripts auf diese Bibliothek verweisen zu können. Weitere Informationen zur Erstellung Ihres ersten Mobile Service finden Sie unter [Erste Schritte mit Mobile Services](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started/).

Konfigurieren einer Anwendung für die Verwendung der Twilio Node.js-Bibliothek
------------------------------------------------------------------------------

Twilio stellt eine Node.js-Bibliothek zur Verfügung, die verschiedene Aspekte von Twilio abdeckt, um eine einfache und problemlose Interaktion mit der Twilio-REST-API und dem Twilio-Client zum Generieren von TwiML-Antworten zu bieten.

Sie müssen das npm-Modul von Mobile Services verwenden, um die Twilio Node.js-Bibliothek in Ihrem Mobile Service nutzen zu können. Legen Sie Ihre Skripts dazu in einer Quellcodeverwaltung ab. Das Lernprogramm [Speichern von Skripts in der Quellcodeverwaltung](http://www.windowsazure.com/en-us/develop/mobile/tutorials/store-scripts-in-source-control/) beschreibt die Einrichtung der Quellcodeverwaltung für Ihren Mobile Service und die Speicherung Ihrer Serverskripts in einem Git-Repository.

Nachdem Sie die Quellcodeverwaltung für Ihren Mobile Service eingerichtet haben, öffnen Sie die Registerkarte Configure im Dashboard Ihres Mobile Service und kopieren Sie die Git-URL

Fügen Sie die URL in einem Browser ein und ersetzen Sie den Namen des Repositorys durch */DebugConsole/index.html*

Ändern Sie zum Beispiel:

    https://twilioSample.scm.azure-mobile.net/twilioSample.git

zu:

    https://twilioSample.scm.azure-mobile.net/DebugConsole/index.html

Geben Sie bei Aufforderung die Anmeldeinformationen ein, die Sie bei der Einrichtung der Quellcodeverwaltung angegeben haben. Nach der Anmeldung wird die Azure Mobile Services-Konsole angezeigt.

![Mobile Services-Konsole](./media/partner-twilio-mobile-services-how-to-use-voice-sms/twilio-kuduconsole.png)

Wechseln Sie in der Konsole anschließend in den scripts-Ordner:

    cd site\wwwroot\App_Data\config\scripts

Sobald Sie sich im api-Ordner befinden, können Sie das Twilio node-Modul mit dem folgenden Befehl installieren:

    npm install twilio

Nun können Sie die Twilio-Bibliothek in Ihren benutzerdefiniertem API- und Tabellenskripts verwenden.

Gewusst wie: Tätigen von ausgehenden Anrufen
--------------------------------------------

Das folgende Skript zeigt, wie Sie mit der **makeCall**-Funktion einen ausgehenden Anruf aus Ihrem Mobile Service ausführen können. Dieser Code verwendet eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort (Twilio Markup Language). Geben Sie Ihre Werte für die Telefonnummern **From** und **To** ein, und stellen Sie sicher, dass Sie die **From**-Telefonnummer für Ihr Twilio-Konto verifizieren, bevor Sie den Code ausführen.

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

Weitere Informationen zu den an die **client.makeCall**-Funktion übergebenen Parametern finden Sie unter <http://www.twilio.com/docs/api/rest/making-calls>.

Wie bereits erwähnt, verwendet dieser Code eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort. Stattdessen können Sie die TwiML-Antwort auch von Ihrer eigenen Website bereitstellen lassen. Weitere Informationen finden Sie unter [Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website](#howto_provide_twiml_responses).

Gewusst wie: Senden einer SMS-Nachricht
---------------------------------------

Das folgende Beispiel zeigt, wie Sie eine SMS-Nachricht mit der **sendSms**-Funktion senden. Die **From**-Nummer wird von Twilio für Testkonten zum Senden von SMS-Nachrichten bereitgestellt. Die **To**-Nummer muss für Ihr Twilio-Konto verifiziert werden, bevor Sie den Code ausführen.

    var twilio = require('twilio');

    exports.post = function(request, response) {

        var client = new twilio.RestClient('[ACCOUNT_SID]', 'AUTH_TOKEN');
     
        client.sendSms({
            to:'[]',
            from:'[]',
            body:'ahoy hoy! Testing Twilio and node.js'
        }, function(error, message) {

            // Die "error"-Variable enthält Infos zu eventuellen Fehlern.
            // Dieser Wert ist "false", falls der Aufruf erfolgreich war
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

Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website
----------------------------------------------------------------------

Wenn Ihre Anwendung einen Anruf an die Twilio-API initiiert – z. B. über die client.InitiateOutboundCall-Methode – sendet Twilio Ihre Anforderung an eine URL, von der die Rückgabe einer TwiML-Antwort erwartet wird. Das Beispiel unter Gewusst wie: Tätigen eines ausgehenden Anrufs verwendet die von Twilio bereitgestellte URL http://twimlets.com/message für die Rückgabe der Antwort.

**Hinweis**

TwiML wurde für die Verwendung durch Webdienste konzipiert, kann jedoch im Browser angezeigt werden. Klicken Sie z. B. auf [twimlet\_message\_url](http://twimlets.com/message), um ein leeres &lt;Response\>-Element anzuzeigen, oder klicken Sie auf [twimlet\_message\_url\_hello\_world](http://twimlets.com/message?Message%5B0%5D=Hello%20World), um ein &lt;Response\>-Element mit einem &lt;Say\>-Element anzuzeigen.

Anstelle der von Twilio bereitgestellten URL können Sie auch Ihre eigene URL-Website für die Rückgabe von HTTP-Antworten erstellen. Sie können die Website mit jeder Sprache erstellen, die HTTP-Antworten zurückgibt. In diesem Thema wird davon ausgegangen, dass Sie die URL von einem generischen ASP.NET-Handler hosten.

Das folgende Skript erstellt eine TwiML-Antwort, die beim Anruf Hello World sagt.

    var twilio = require('twilio');

    exports.post = function(request, response) {
        var resp = new twilio.TwimlResponse();
        resp.say({voice:'woman'}, 'ahoy hoy! Testing Twilio and node.js');
        response.set('Content-Type', 'text/xml');
        response.send(200, resp.toString());
    };

Weitere Informationen zu TwiML finden Sie unter <https://www.twilio.com/docs/api/twiml>.

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

[WACOM.INCLUDE [twilio\_additional\_services\_and\_next\_steps](../includes/twilio_additional_services_and_next_steps.md)]

