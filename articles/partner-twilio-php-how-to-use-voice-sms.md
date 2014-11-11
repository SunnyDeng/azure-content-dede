<properties title="How to Use Twilio for Voice and SMS (PHP) - Azure" pageTitle="How to Use Twilio for Voice and SMS (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in PHP." documentationCenter="PHP" services="" authors="waltpo" manager="bjsmith" editor="mollybos" />

Verwenden von Twilio für Telefonie- und SMS-Funktionen in PHP
=============================================================

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem Twilio-API-Dienst in Azure. Die Szenarien behandeln das Tätigen eines Telefonanrufs und das Senden einer Kurznachricht (SMS). Weitere Informationen zu Twilio und zur Verwendung von Telefonie und SMS in Ihren Anweisungen finden Sie im Abschnitt [Nächste Schritte](#NextSteps).

Inhaltsverzeichnis
------------------

-   [Was ist Twilio?](#WhatIs)
-   [Twilio-Preise](#Pricing)
-   [Konzepte](#Concepts)
-   [Erstellen eines Twilio-Kontos](#CreateAccount)
-   [Verifizieren von Telefonnummern](#VerifyPhoneNumbers)
-   [Erstellen einer PHP-Anwendung](#create_app)
-   [Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken](#configure_app)
-   [Gewusst wie: Tätigen von ausgehenden Anrufen](#howto_make_call)
-   [Gewusst wie: Senden einer SMS-Nachricht](#howto_send_sms)
-   [Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website](#howto_provide_twiml_responses)

Was ist Twilio?
---------------

Twilio ist ein leistungsstarkes und zukunftsorientiertes Tool für die Unternehmenskommunikation, mit dem Entwickler Telefonie-, VoIP- und Messagingfunktionalitäten in Anwendungen einbetten können. Die gesamte in einer cloudbasierten, globalen Umgebung benötigte Infrastruktur wird virtualisiert und über die Twilio-API-Kommunikationsplattform zur Verfügung gestellt. Anwendungen können einfach erstellt und skaliert werden. Sie profitieren von flexiblen Pay-as-you-go-Preisen und der Zuverlässigkeit der Cloud.

**Twilio Voice** ermöglicht Anwendungen das Tätigen und Entgegennehmen von Telefonanrufen. **Twilio SMS** ermöglicht Anwendungen das Senden und Empfangen von SMS-Nachrichten. **Twilio Client** ermöglicht Ihnen das Tätigen von VoIP-Anrufen von jedem Telefon, Tablet-PC oder Browser und unterstützt WebRTC.

Twilio-Preise und -Sonderangebote
---------------------------------

Azure-Kunden erhalten ein [Sonderangebot][http://www.twilio.com/azure]: eine Twilio-Gutschrift in Höhe von 10 US-Dollar bei einem Upgrade Ihres Twilio-Kontos. Diese Twilio-Gutschrift kann für jede beliebige Twilio-Nutzung ausgegeben werden (eine Gutschrift von 10 US-Dollar reicht für den Versand von 1.000 SMS-Nachrichten oder für bis zu 1.000 eingehende Telefonminuten, abhängig vom Standort der Telefonnummer und dem Nachrichten- oder Anrufziel). Lösen Sie diese Twilio-Gutschrift ein, und legen Sie los unter: <http://ahoy.twilio.com/azure>.

Twilio ist ein Pay-as-you-go-Dienst, also vertragsungebunden. Es fallen keine Einrichtungsgebühren an, und Sie können Ihr Konto jederzeit schließen. Weitere Informationen finden Sie unter [Twilio Pricing](http://www.twilio.com/pricing).

Konzepte
--------

Die Twilio-API ist eine RESTful-API, die Telefonie- und SMS-Funktionen für Anwendungen bereitstellt. Clientbibliotheken stehen in mehreren Sprachen zur Verfügung; eine Liste finden Sie auf der Seite der [Twilio-API-Bibliotheken](https://www.twilio.com/docs/libraries).

Schlüsselaspekte der Twilio API sind Twilio-Verben und die Twilio Markup Language (TwiML).

### Twilio-Verben

Die API verwendet Twilio-Verben; so weist beispielsweise das Verb **&lt;Say\>** Twilio an, ein Nachricht in einem Anruf akustisch zu übermitteln.

Nachfolgend finden Sie eine Liste mit Twilio-Verben. Andere Verben und Funktionen werden in der [Dokumentation zur Twilio Markup Language][http://www.twilio.com/docs/api/twiml] erläutert.

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

Verschiedene Telefonnummern müssen bei Twilio für Ihr Konto verifiziert werden. Wenn Sie beispielsweise ausgehende Telefonate mit Ihrer existierenden Telefonnummer als Anrufer-ID tätigen möchten, muss die Telefonnummer bei Twilio verifiziert werden. Wenn Sie SMS-Nachrichten an eine Telefonnummer senden möchten, muss entsprechend die Telefonnummer des Empfängers bei Twilio verifiziert werden, bevor Sie ein Upgrade durchführen. Nach dem Upgrade können Sie SMS ohne Verifizierung an beliebige Telefonnummern schicken. Informationen zum Verifizieren von Telefonnummern finden Sie unter [Manage Numbers](https://www.twilio.com/user/account/phone-numbers/verified#) (in englischer Sprache). In einigen der folgenden Codebeispiele werden Telefonnummern verwendet, die Sie bei Twilio verifizieren müssen.

Alternativ zur Verwendung einer vorhandenen Telefonnummer für Ihre Anwendungen besteht die Möglichkeit, eine Twilio-Telefonnummer zu kaufen. Informationen zum Kaufen einer Twilio-Telefonnummer finden Sie unter [Twilio Phone Numbers Help](https://www.twilio.com/help/faq/phone-numbers) (in englischer Sprache).

Erstellen einer PHP-Anwendung
-----------------------------

PHP-Anwendungen, die den Twilio-Dienst verwenden und in Azure ausgeführt werden, unterscheiden sich nicht von anderen PHP-Anwendungen, die den Twilio-Dienst verwenden. Twilio-Dienste sind REST-basiert und können in PHP auf verschiedene Arten aufgerufen werden. Dieser Artikel konzentriert sich auf die Verwendung von Twilio mit der [Twilio-Bibliothek für PHP von Github](https://github.com/twilio/twilio-php). Weitere Informationen zur Verwendung der Twilio-Bibliothek für PHP finden Sie unter <http://readthedocs.org/docs/twilio-php/en/latest/index.html>.

Ausführliche Informationen zur Erstellung und Bereitstellung von Twilio/PHP-Anwendungen in Azure finden Sie unter [How to Make a Phone Call Using Twilio in a Java Application on Azure](../partner-twilio-php-make-phone-call) (in englischer Sprache).

Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken
------------------------------------------------------------------------

Sie können die Anwendung auf zwei verschiedene Arten für die Twilio-Bibliothek in PHP konfigurieren:

1.  Sie können die Twilio-Bibliothek für PHP von Github (<https://github.com/twilio/twilio-php>) herunterladen und das **Services**-Verzeichnis zu Ihrer Anwendung hinzufügen.

    -ODER-

2.  Sie können die Twilio-Bibliothek für PHP als PEAR-Paket installieren. Führen Sie dazu die folgenden Befehle aus:

         $ pear channel-discover twilio.github.com/pear
         $ pear install twilio/Services_Twilio

Nachdem Sie die Twilio-Bibliothek für PHP installiert haben, können Sie eine **require\_once**-Anweisung am Anfang Ihrer PHP-Dateien hinzufügen, um auf die Bibliothek zu verweisen:

     require_once 'Services/Twilio.php';

Weitere Informationen finden Sie unter <https://github.com/twilio/twilio-php/blob/master/README.md>.

Gewusst wie: Tätigen von ausgehenden Anrufen
--------------------------------------------

Nachfolgend wird erläutert, wie Sie einen ausgehenden Anruf mit der **Services\_Twilio**-Klasse tätigen. Dieser Code verwendet eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort (Twilio Markup Language). Geben Sie Ihre Werte für die Telefonnummern **From** und **To** ein, und stellen Sie sicher, dass Sie die **From**-Telefonnummer für Ihr Twilio-Konto verifizieren, bevor Sie den Code ausführen.

    // Einbinden der Twilio PHP-Bibliothek.
    require_once 'Services/Twilio.php';

    // Bibliotheksversion.
    $version = "2010-04-01";

    // Ihre Konto-ID und Ihr Authentifizierungs-Token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // Die Telefonnummer, die den Anruf einleitet.
    // (Muss zuvor bei Twilio verifiziert werden.)
    $from_number = "NNNNNNNNNNN";

    // Die Telefonnummer, die den Anruf entgegennimmt.
    $to_number = "NNNNNNNNNNN";

    // Verwenden Sie die von Twilio bereitgestellte Website für die TwiML-Antwort.
    $url = "http://twimlets.com/message";

    // Der Nachrichtentext.
    $message = "Hello world.";

    // Anruf-Client erstellen.
    $client = new Services_Twilio($sid, $token, $version);

    //Anruf ausführen.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,

    \$url.'?Message='.urlencode(\$message)

     );
        }
        catch (Exception $e) 
        {
            echo 'Error: ' . $e->getMessage();
        }

Wie bereits erwähnt, verwendet dieser Code eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort. Stattdessen können Sie die TwiML-Antwort auch von Ihrer eigenen Website bereitstellen lassen. Weitere Informationen dazu finden Sie unter [How to Provide TwiML Responses from Your Own Web Site](#howto_provide_twiml_responses) (in englischer Sprache).

-   **Hinweis**: Informationen zur Behandlung von Validierungsfehlern für SSL-Zertifikate finden Sie unter <http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html>

Gewusst wie: Senden einer SMS-Nachricht
---------------------------------------

Das folgende Beispiel zeigt, wie Sie eine SMS-Nachricht mit der **Services\_Twilio**-Klasse senden. Die **From**-Nummer wird von Twilio für Testkonten zum Senden von SMS-Nachrichten bereitgestellt. Die **To**-Nummer muss für Ihr Twilio-Konto verifiziert werden, bevor Sie den Code ausführen.

    // Einbinden der Twilio PHP-Bibliothek.
    require_once 'Services/Twilio.php';

    // Bibliotheksversion.
    $version = "2010-04-01";

    // Ihre Konto-ID und Ihr Authentifizierungs-Token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // Mit einem Probekonto können Sie SMS nur von Ihrer Twilio-Nummer senden.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Anruf-Client erstellen.
    $client = new Services_Twilio($sid, $token, $version);

    // SMS verschicken.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website
----------------------------------------------------------------------

Wenn Ihre Anwendung einen Anruf an die Twilio-API initiiert, sendet Twilio Ihre Anforderung an eine URL, von der die Rückgabe einer TwiML-Antwort erwartet wird. Das obige Beispiel verwendet die von Twilio bereitgestellte URL <http://twimlets.com/message>. (TwiML wurde für die Verwendung durch Twilio konzipiert, kann jedoch im Browser angezeigt werden. Klicken Sie z. B. auf <http://twimlets.com/message>, um ein leeres `<Response>`-Element anzuzeigen, oder klicken Sie auf <http://twimlets.com/message?Message%5B0%5D=Hello%20World>, um ein `<Response>`-Element anzuzeigen, das ein `<Say>`-Element enthält.)

Anstelle der von Twilio bereitgestellten URL können Sie auch Ihre eigene Website für die Rückgabe von HTTP-Antworten erstellen. Sie können die Website in allen Sprachen erstellen, die XML-Antworten zurückgeben. Dieses Beispiel geht davon aus, dass Sie die TwiML-Antwort mit PHP erstellen.

Die folgende PHP-Seite erstellt eine TwiML-Antwort, die beim Anruf **Hello World** sagt.

    <
    <?php    
		header("content-type: text/xml");    
		echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
	?>
	<Response>    
		<Say>Hello world.</Say>
	</Response>

Wie das vorstehende Beispiel zeigt, handelt es sich bei der TwiML-Antwort einfach um ein XML-Dokument. Die Twilio-Bibliothek für PHP enthält Klassen, die TwiML für Sie generieren. Das folgende Beispiel generiert die gleiche Antwort wie oben, jedoch unter Verwendung der **Services\_Twilio\_Twiml**-Klasse in der Twilio-Bibliothek für PHP:

    require_once('Services/Twilio.php');

    $response = new Services_Twilio_Twiml();
    $response->say("Hello world.");
    print $response;

Weitere Informationen zu TwiML finden Sie unter <https://www.twilio.com/docs/api/twiml>.

Sobald Sie Ihre PHP-Seite mit den TwiML-Antworten eingerichtet haben, können Sie die URL der PHP-Seite an die `Services_Twilio->account->calls->create`-Methode übergeben. Beispiel: Ihre Webanwendung **MyTwiML** liegt in einem unter Azure gehosteten Dienst, und der Name der PHP-Seite lautet **mytwiml.php**. In diesem Fall kann die URL wie im folgenden Codebeispiel gezeigt an **Services\_Twilio-\>account-\>calls-\>create** übergeben werden:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // Der Text der Nachricht.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            \$url.'?Message='.urlencode(\$message)
     );
        }
        catch (Exception $e) 
        {
            echo 'Error: ' . $e->getMessage();
        }

Weitere Informationen über die Verwendung von Twilio in Azure mit PHP finden Sie unter [Tätigen eines Telefonanrufs mithilfe von Twilio in einer PHP-Anwendung auf Azure](../partner-twilio-php-make-phone-call).

Gewusst wie: Verwenden zusätzlicher Twilio-Dienste
--------------------------------------------------

Zusätzlich zu den hier gezeigten Beispielen bietet Twilio webbasierte APIs, mit denen Sie zusätzliche Twilio-Funktionen in Ihrer Azure-Anwendung nutzen können. Ausführliche Informationen finden Sie in der [Twilio API-Dokumentation](http://www.twilio.com/api).

Nächste Schritte
----------------

Nachdem Sie nun mit den Grundlagen des Twilio-Dienstes vertraut sind, finden Sie unter diesen Links weitere Informationen:

-   [Twilio Security Guidelines](http://www.twilio.com/docs/security)
-   [Twilio HowTos und Beispielcode](http://www.twilio.com/docs/howto)
-   [Twilio Quickstart Tutorials](http://www.twilio.com/docs/quickstart)
-   [Twilio on GitHub](https://github.com/twilio)
-   [Talk to Twilio Support](http://www.twilio.com/help/contact)

