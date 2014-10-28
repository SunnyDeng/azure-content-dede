<properties linkid="develop-php-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in PHP." metaCanonical="" services="" documentationCenter="" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="MicrosoftHelp@twilio.com" solutions="" manager="" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com"></tags>

# Verwenden von Twilio für Telefonie- und SMS-Funktionen in PHP

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem Twilio-API-Dienst in Azure. Die Szenarien behandeln das Tätigen eines Telefonanrufs und das Senden einer Kurznachricht (SMS). Weitere Informationen zu Twilio und zur Verwendung von Telefonie und SMS in Ihren Anwendungen finden Sie im Abschnitt [Nächste Schritte][Nächste Schritte].

## Inhaltsverzeichnis

-   [Was ist Twilio?][Was ist Twilio?]
-   [Twilio-Preise][Twilio-Preise]
-   [Konzepte][Konzepte]
-   [Erstellen eines Twilio-Kontos][Erstellen eines Twilio-Kontos]
-   [Überprüfen von Telefonnummern][Überprüfen von Telefonnummern]
-   [Erstellen einer PHP-Anwendung][Erstellen einer PHP-Anwendung]
-   [Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken][Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken]
-   [Gewusst wie: Tätigen von ausgehenden Anrufen][Gewusst wie: Tätigen von ausgehenden Anrufen]
-   [Gewusst wie: Senden einer SMS-Nachricht][Gewusst wie: Senden einer SMS-Nachricht]
-   [Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website][Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website]

## <span id="WhatIs"></span></a>Was ist Twilio?

Twilio ist ein leistungsstarkes und zukunftsorientiertes Tool für die Unternehmenskommunikation, mit dem Entwickler Telefonie-, VoIP- und Messagingfunktionalitäten in Anwendungen einbetten können. Die gesamte in einer cloudbasierten, globalen Umgebung benötigte Infrastruktur wird virtualisiert und über die Twilio-API-Kommunikationsplattform zur Verfügung gestellt. Anwendungen können einfach erstellt und skaliert werden. Sie profitieren von flexiblen Pay-as-you-go-Preisen und der Zuverlässigkeit der Cloud.

**Twilio Voice** ermöglicht Anwendungen das Tätigen und Entgegennehmen von Telefonanrufen. **Twilio SMS** ermöglicht Ihren Anwendungen das Senden und Empfangen von SMS-Nachrichten. **Twilio Client** ermöglicht Ihnen das Tätigen von VoIP-Anrufen von jedem Telefon, Tablet-PC oder Browser und unterstützt WebRTC.

## <span id="Pricing"></span></a>Twilio-Preise und -Sonderangebote

Azure-Kunden erhalten ein [Sonderangebot]: eine Twilio-Gutschrift in Höhe von 10 US-Dollar bei einem Upgrade Ihres Twilio-Kontos. Diese Twilio-Gutschrift kann für jede beliebige Twilio-Nutzung ausgegeben werden (eine Gutschrift von 10 US-Dollar reicht für den Versand von 1.000 SMS-Nachrichten oder für bis zu 1.000 eingehende Telefonminuten, abhängig vom Standort der Telefonnummer und dem Nachrichten- oder Anrufziel). Lösen Sie diese Twilio-Gutschrift ein, und legen Sie los unter: [ahoy.twilio.com/azure].

Twilio ist ein Pay-as-you-go-Dienst, also vertragsungebunden. Es fallen keine Einrichtungsgebühren an, und Sie können Ihr Konto jederzeit schließen. Weitere Informationen finden Sie unter [Twilio-Preise][1].

## <span id="Concepts"></span></a>Konzepte

Die Twilio-API ist eine RESTful-API, die Telefonie- und SMS-Funktionen für Anwendungen bereitstellt. Clientbibliotheken stehen in mehreren Sprachen zur Verfügung; eine Liste finden Sie auf der Seite der [Twilio-API-Bibliotheken][Twilio-API-Bibliotheken].

Schlüsselaspekte der Twilio API sind Twilio-Verben und die Twilio Markup Language (TwiML).

### <span id="Verbs"></span></a>Twilio-Verben

Die API verwendet Twilio-Verben; so weist beispielsweise das Verb **\<Say\>** Twilio an, ein Nachricht in einem Anruf akustisch zu übermitteln.

Nachfolgend finden Sie eine Liste mit Twilio-Verben. Andere Verben und Funktionen werden in der [Dokumentation zur Twilio Markup Language][<http://www.twilio.com/docs/api/twiml>] erläutert.

-   **\<Dial\>**: Verbindet den Anrufer mit einem anderen Telefon.
-   **\<Gather\>**: Erfasst Ziffern, die über die Telefontasten eingegeben werden.
-   **\<Hangup\>**: Beendet einen Anruf.
-   **\<Play\>**: Gibt eine Audiodatei wieder.
-   **\<Pause\>**: Wartet ohne Ton für eine angegebene Anzahl von Sekunden.
-   **\<Record\>**: Zeichnet die Stimme des Anrufers auf und gibt eine URL einer Datei mit der Aufzeichnung zurück.
-   **\<Redirect\>**: Überträgt die Steuerung eines Anrufs oder einer SMS an die TwiML an einer anderen URL.
-   **\<Reject\>**: Lehnt einen bei Ihrer Twilio-Nummer eingehenden Anruf ab, ohne dass Ihnen Kosten entstehen.
-   **\<Say\>**: Konvertiert Text in Sprache für einen Anruf.
-   **\<Sms\>**: Sendet eine SMS-Nachricht.

### <span id="TwiML"></span></a>TwiML

TwiML ist ein Satz XML-basierter Anweisungen auf Grundlage der Twilio-Verben, die Twilio anweisen, wie ein Anruf oder eine SMS verarbeitet werden soll.

Die TwiML im folgenden Beispiel konvertiert den Text **Hello World** in Sprache.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Wenn eine Anwendung die Twilio-API aufruft, ist einer der API-Parameter die URL, die die TwiML-Antwort zurückgibt. Zu Entwicklungszwecken können Sie die von Twilio zur Verfügung gestellten URLs verwenden, um die von Ihren Anwendungen verwendeten TwiML-Antworten bereitzustellen. Sie können auch Ihre eigenen URLs hosten, um TwiML-Antworten zu generieren; eine weitere Option ist die Verwendung des **TwiMLResponse**-Objekts.

Nähere Informationen zu Twilio-Verben, ihren Attributen sowie TwiML finden Sie unter [TwiML][TwiML]. Weitere Informationen zur Twilio-API finden Sie unter [Twilio-API][Twilio-API].

## <span id="CreateAccount"></span></a>Erstellen eines Twilio-Kontos

Wenn Sie ein Twilio-Konto erstellen möchten, melden Sie sich bei [Try Twilio][Try Twilio] an. Sie können mit einem kostenlosen Konto beginnen und später ein Upgrade vornehmen.

Wenn Sie sich für ein Twilio-Konto anmelden, erhalten Sie eine Konto-ID und ein Authentifizierungstoken. Beide Angaben benötigen Sie zum Tätigen von Twilio-API-Anrufen. Um den unbefugten Zugriff auf Ihr Konto zu verhindern, bewahren Sie Ihr Authentifizierungstoken sicher auf. Ihre Konto-ID und das Authentifizierungstoken können Sie auf der [Twilio-Kontoseite][Twilio-Kontoseite] in den Feldern **ACCOUNT SID** bzw. **AUTH TOKEN** einsehen.

## <span id="VerifyPhoneNumbers"></span></a>Überprüfen von Telefonnummern

Verschiedene Telefonnummern müssen bei Twilio für Ihr Konto verifiziert werden. Wenn Sie beispielsweise ausgehende Telefonate mit Ihrer existierenden Telefonnummer als Anrufer-ID tätigen möchten, muss die Telefonnummer bei Twilio überprüft werden. Wenn Sie SMS-Nachrichten an eine Telefonnummer senden möchten, muss entsprechend die Telefonnummer des Empfängers bei Twilio überprüft werden, bevor Sie ein Upgrade durchführen. Nach dem Upgrade können Sie SMS ohne Überprüfung an beliebige Telefonnummern schicken. Informationen zum Überprüfen von Telefonnummern finden Sie unter [Manage Numbers][Manage Numbers]. In einigen der folgenden Codebeispiele werden Telefonnummern verwendet, die Sie bei Twilio verifizieren müssen.

Alternativ zur Verwendung einer vorhandenen Telefonnummer für Ihre Anwendungen besteht die Möglichkeit, eine Twilio-Telefonnummer zu kaufen. Informationen zum Kaufen einer Twilio-Telefonnummer finden Sie in der [Twilio-Hilfe zu Telefonnummern][Twilio-Hilfe zu Telefonnummern] (in englischer Sprache).

## <span id="create_app"></span></a>Erstellen einer PHP-Anwendung

PHP-Anwendungen, die den Twilio-Dienst verwenden und in Azure ausgeführt werden, unterscheiden sich nicht von anderen PHP-Anwendungen, die den Twilio-Dienst verwenden. Twilio-Dienste sind REST-basiert und können in PHP auf verschiedene Arten aufgerufen werden. Dieser Artikel konzentriert sich auf die Verwendung von Twilio mit der [Twilio-Bibliothek für PHP von Github][Twilio-Bibliothek für PHP von Github]. Weitere Informationen zur Verwendung der Twilio-Bibliothek für PHP finden Sie unter [][]<http://readthedocs.org/docs/twilio-php/en/latest/index.html></a>.

Ausführliche Informationen zur Erstellung und Bereitstellung von Twilio/PHP-Anwendungen in Azure finden Sie unter [How to Make a Phone Call Using Twilio in a Java Application on Azure][How to Make a Phone Call Using Twilio in a Java Application on Azure] (Ausführen eines Telefonanrufs mit Twilio in einer Java-Anwendung in Azure, in englischer Sprache).

## <span id="configure_app"></span></a>Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken

Sie können die Anwendung auf zwei verschiedene Arten für die Twilio-Bibliothek in PHP konfigurieren:

1.  Sie können die Twilio-Bibliothek für PHP von Github ([][Twilio-Bibliothek für PHP von Github]<https://github.com/twilio/twilio-php></a>) herunterladen und das **Services**-Verzeichnis zu Ihrer Anwendung hinzufügen.

    ODER

2.  Sie können die Twilio-Bibliothek für PHP als PEAR-Paket installieren. Führen Sie dazu die folgenden Befehle aus:

        $ pear channel-discover twilio.github.com/pear
        $ pear install twilio/Services_Twilio

Nachdem Sie die Twilio-Bibliothek für PHP installiert haben, können Sie eine **require\_once**-Anweisung am Anfang Ihrer PHP-Dateien hinzufügen, um auf die Bibliothek zu verweisen:

        require_once 'Services/Twilio.php';

Weitere Informationen finden Sie unter [][2]<https://github.com/twilio/twilio-php/blob/master/README.md></a>.

## <span id="howto_make_call"></span></a>Gewusst wie: Tätigen von ausgehenden Anrufen

Nachfolgend wird erläutert, wie Sie einen ausgehenden Anruf mit der **Services\_Twilio**-Klasse tätigen. Dieser Code verwendet eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort (Twilio Markup Language). Geben Sie Ihre Werte für die Telefonnummern **From** und **To** ein, und stellen Sie sicher, dass Sie die **From**-Telefonnummer für Ihr Twilio-Konto verifizieren, bevor Sie den Code ausführen.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";

    // The number of the phone initiating the the call.
    // (Must be previously validated with Twilio.)
    $from_number = "NNNNNNNNNNN";

    // The number of the phone receiving call.
    $to_number = "NNNNNNNNNNN";

    // Use the Twilio-provided site for the TwiML response.
    $url = "http://twimlets.com/message";

    // The phone message text.
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    //Make the call.
    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Wie bereits erwähnt, verwendet dieser Code eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort. Stattdessen können Sie die TwiML-Antwort auch von Ihrer eigenen Website bereitstellen lassen. Weitere Informationen dazu finden Sie unter [Bereitstellen von TwiML-Antworten von der eigenen Website][Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website].

-   **Hinweis**: Informationen zur Behandlung von Validierungsfehlern für SSL-Zertifikate finden Sie unter [][3]<http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html></a>

## <span id="howto_send_sms"></span></a>Gewusst wie: Senden einer SMS-Nachricht

Das folgende Beispiel zeigt, wie Sie eine SMS-Nachricht mit der **Services\_Twilio**-Klasse senden. Die **From**-Nummer wird von Twilio für Testkonten zum Senden von SMS-Nachrichten bereitgestellt. Die **To**-Nummer muss für Ihr Twilio-Konto überprüft werden, bevor Sie den Code ausführen.

    // Include the Twilio PHP library.
    require_once 'Services/Twilio.php';

    // Library version.
    $version = "2010-04-01";

    // Set your account ID and authentication token.
    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";


    $from_number = "NNNNNNNNNNN"; // With trial account, texts can only be sent from your Twilio number.
    $to_number = "NNNNNNNNNNN";
    $message = "Hello world.";

    // Create the call client.
    $client = new Services_Twilio($sid, $token, $version);

    // Send the SMS message.
    try
    {
        $client->account->sms_messages->create($from_number, $to_number, $message);
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

## <span id="howto_provide_twiml_responses"></span></a>Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website

Wenn Ihre Anwendung einen Anruf an die Twilio-API initiiert, sendet Twilio Ihre Anforderung an eine URL, von der die Rückgabe einer TwiML-Antwort erwartet wird. Das obige Beispiel verwendet die von Twilio bereitgestellte URL [][4]<http://twimlets.com/message></a>. (TwiML wurde für die Verwendung durch Twilio konzipiert, kann jedoch im Browser angezeigt werden. Klicken Sie z. B. auf [][4]<http://twimlets.com/message></a>, um ein leeres `<Response>`-Element anzuzeigen, oder klicken Sie auf [][5]<http://twimlets.com/message?Message%5B0%5D=Hello%20World></a>, um ein `<Response>`-Element mit einem `<Say>`-Element zu sehen.)

Anstelle der von Twilio bereitgestellten URL können Sie auch Ihre eigene Website für die Rückgabe von HTTP-Antworten erstellen. Sie können die Website in allen Sprachen erstellen, die XML-Antworten zurückgeben. Dieses Beispiel geht davon aus, dass Sie die TwiML-Antwort mit PHP erstellen.

Die folgende PHP-Seite erstellt eine TwiML-Antwort, die beim Anruf **Hello World** sagt.

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

Weitere Informationen zu TwiML finden Sie auf [][6]<https://www.twilio.com/docs/api/twiml></a>.

Sobald Sie Ihre PHP-Seite mit den TwiML-Antworten eingerichtet haben, können Sie die URL der PHP-Seite an die Methode `Services_Twilio->account->calls->create` übergeben. Beispiel: Ihre Webanwendung **MyTwiML** liegt in einem unter Azure gehosteten Dienst, und der Name der PHP-Seite lautet **mytwiml.php**. In diesem Fall kann die URL wie im folgenden Codebeispiel gezeigt an **Services\_Twilio-\>account-\>calls-\>create** übergeben werden:

    require_once 'Services/Twilio.php';

    $sid = "your_twilio_account_sid";
    $token = "your_twilio_authentication_token";
    $from_number = "NNNNNNNNNNN";
    $to_number = "NNNNNNNNNNN";
    $url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.php";

    // The phone message text.
    $message = "Hello world.";

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    try
    {
        $call = $client->account->calls->create(
            $from_number, 
            $to_number,
            $url.'?Message='.urlencode($message)
        );
    }
    catch (Exception $e) 
    {
        echo 'Error: ' . $e->getMessage();
    }

Weitere Informationen zur Verwendung von Twilio in Azure mit PHP finden Sie unter [Tätigen eines Telefonanrufs mithilfe von Twilio in einer PHP-Anwendung auf Azure][How to Make a Phone Call Using Twilio in a Java Application on Azure].

## <span id="AdditionalServices"></span></a>Gewusst wie: Verwenden zusätzlicher Twilio-Dienste

Zusätzlich zu den hier gezeigten Beispielen bietet Twilio webbasierte APIs, mit denen Sie zusätzliche Twilio-Funktionen in Ihrer Azure-Anwendung verwenden können. Ausführliche Informationen finden Sie in der [Twilio API-Dokumentation][Twilio-API].

## <span id="NextSteps"></span></a>Nächste Schritte

Nachdem Sie nun mit den Grundlagen des Twilio-Dienstes vertraut sind, finden Sie unter diesen Links weitere Informationen:

-   [Twilio Security Guidelines][Twilio Security Guidelines]
-   [Twilio-Lernprogramme und Beispielcode][Twilio-Lernprogramme und Beispielcode]
-   [Twilio Quickstart Tutorials][Twilio Quickstart Tutorials]
-   [Twilio on GitHub][Twilio on GitHub]
-   [Talk to Twilio Support][Talk to Twilio Support]

  [Nächste Schritte]: #NextSteps
  [Was ist Twilio?]: #WhatIs
  [Twilio-Preise]: #Pricing
  [Konzepte]: #Concepts
  [Erstellen eines Twilio-Kontos]: #CreateAccount
  [Überprüfen von Telefonnummern]: #VerifyPhoneNumbers
  [Erstellen einer PHP-Anwendung]: #create_app
  [Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken]: #configure_app
  [Gewusst wie: Tätigen von ausgehenden Anrufen]: #howto_make_call
  [Gewusst wie: Senden einer SMS-Nachricht]: #howto_send_sms
  [Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website]: #howto_provide_twiml_responses
  [1]: http://www.twilio.com/pricing
  [Twilio-API-Bibliotheken]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [Twilio-API]: http://www.twilio.com/api
  [Try Twilio]: https://www.twilio.com/try-twilio
  [Twilio-Kontoseite]: https://www.twilio.com/user/account
  [Manage Numbers]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Twilio-Hilfe zu Telefonnummern]: https://www.twilio.com/help/faq/phone-numbers
  [Twilio-Bibliothek für PHP von Github]: https://github.com/twilio/twilio-php
  []: http://readthedocs.org/docs/twilio-php/en/latest/index.html
  [How to Make a Phone Call Using Twilio in a Java Application on Azure]: http://windowsazure.com/de-de/documentation/articles/partner-twilio-php-make-phone-call
  [2]: https://github.com/twilio/twilio-php/blob/master/README.md
  [3]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
  [4]: http://twimlets.com/message
  [5]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
  [6]: https://www.twilio.com/docs/api/twiml
  [Twilio Security Guidelines]: http://www.twilio.com/docs/security
  [Twilio-Lernprogramme und Beispielcode]: http://www.twilio.com/docs/howto
  [Twilio Quickstart Tutorials]: http://www.twilio.com/docs/quickstart
  [Twilio on GitHub]: https://github.com/twilio
  [Talk to Twilio Support]: http://www.twilio.com/help/contact
