<properties urlDisplayName="Twilio Voice/SMS Service" pageTitle="Verwenden von Twilio für Telefonie und SMS (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure-Telefonanrufe, Telefonanrufe in Azure, Azure-Twilio, Azure-SMS, SMS in Azure, Azure-Sprachanrufe, Sprachanrufe in Azure, Azure-Textnachrichten, Textnachrichten in Azure" description="Erfahren Sie, wie Sie mit dem Twilio API-Dienst in Azure einen Telefonanruf tätigen und eine SMS-Nachricht senden. Die Codebeispiele sind in PHP geschrieben." metaCanonical="" services="" documentationCenter="" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="MicrosoftHelp@twilio.com" solutions="" manager="twilio" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="11/25/2014" ms.author="MicrosoftHelp@twilio.com" />





# Verwenden von Twilio für Telefonie- und SMS-Funktionen in PHP
Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem Twilio-API-Dienst in Azure. Die Szenarien behandeln das Tätigen eines Telefonanrufs und das Senden einer Kurznachricht (SMS). Weitere Informationen zu Twilio und zur Verwendung von Telefonie und SMS in Ihren Anwendungen finden Sie im Abschnitt [Nächste Schritte](#NextSteps) .

## Inhaltsverzeichnis
* [Was ist Twilio?](#WhatIs)
* [Twilio-Preise](#Pricing)
* [Konzepte](#Concepts)
* [Erstellen eines Twilio-Kontos](#CreateAccount)
* [Überprüfen von Telefonnummern](#VerifyPhoneNumbers)
* [Erstellen einer PHP-Anwendung](#create_app)
* [Konfigurieren Ihrer Anwendung für die Verwendung von Twilio-Bibliotheken](#configure_app)
* [Gewusst wie: Tätigen von ausgehenden Anrufen](#howto_make_call)
* [Gewusst wie: Senden einer SMS-Nachricht](#howto_send_sms)
* [Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website](#howto_provide_twiml_responses)

<h2><a id="WhatIs"></a>Was ist Twilio?</h2>
Twilio ist ein leistungsstarkes und zukunftsorientiertes Tool für die Unternehmenskommunikation, mit dem Entwickler Telefonie-, VoIP- und Messagingfunktionalitäten in Anwendungen einbetten können. Die gesamte in einer cloudbasierten, globalen Umgebung benötigte Infrastruktur wird virtualisiert und über die Twilio-API-Kommunikationsplattform zur Verfügung gestellt. Anwendungen können einfach erstellt und skaliert werden. Sie profitieren von flexiblen Pay-as-you-go-Preisen und der Zuverlässigkeit der Cloud.

**Twilio Voice** ermöglicht Ihren Anwendungen das Tätigen und Entgegennehmen von Telefonanrufen. **Twilio SMS** ermöglicht Ihren Anwendungen das Senden und Empfangen von SMS-Nachrichten. **Twilio Client** ermöglicht Ihnen das Tätigen von VoIP-Anrufen von jedem Telefon, Tablet-PC oder Browser und unterstützt WebRTC.

<h2><a id="Pricing"></a>Twilio-Preise und -Sonderangebote</h2>

Azure-Kunden erhalten ein [Sonderangebot]: eine Twilio-Gutschrift in Höhe von 10 US-Dollar bei einem Upgrade Ihres Twilio-Kontos. Diese Twilio-Gutschrift kann für jede beliebige Twilio-Nutzung ausgegeben werden (eine Gutschrift von 10 US-Dollar reicht für den Versand von 1.000 SMS-Nachrichten oder für bis zu 1.000 eingehende Telefonminuten, abhängig vom Standort der Telefonnummer und dem Nachrichten- oder Anrufziel). Lösen Sie diese Twilio-Gutschrift ein, und legen Sie los unter: [ahoy.twilio.com/azure].

Twilio ist ein Pay-as-you-go-Dienst, also vertragsungebunden. Es fallen keine Einrichtungsgebühren an, und Sie können Ihr Konto jederzeit schließen. Weitere Informationen finden Sie unter [Twilio-Preise] [twilio_pricing].

<h2><a id="Concepts"></a>Konzepte</h2>
Die Twilio-API ist eine RESTful-API, die Telefonie- und SMS-Funktionen für Anwendungen bereitstellt. Clientbibliotheken stehen in mehreren Sprachen zur Verfügung; eine Liste finden Sie auf der Seite [Twilio-API-Bibliotheken] [twilio_libraries].

Schlüsselaspekte der Twilio API sind Twilio-Verben und die Twilio Markup Language (TwiML).

<h3><a id="Verbs"></a>Twilio-Verben</h3>
Die API verwendet Twilio-Verben; so weist beispielsweise das Verb **&lt;Say&gt;** Twilio an, eine Nachricht in einem Anruf akustisch zu übermitteln.

Nachfolgend finden Sie eine Liste mit Twilio-Verben. Andere Verben und Funktionen werden in der [Dokumentation zur Twilio Markup Language] [http://www.twilio.com/docs/api/twiml] erläutert.

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

<h3><a id="TwiML"></a>TwiML</h3>
TwiML ist ein Satz XML-basierter Anweisungen auf Grundlage der Twilio-Verben, die Twilio anweisen, wie ein Anruf oder eine SMS verarbeitet werden soll.

Die TwiML im folgenden Beispiel konvertiert den Text **Hello World** in Sprache.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Wenn eine Anwendung die Twilio-API aufruft, ist einer der API-Parameter die URL, die die TwiML-Antwort zurückgibt. Zu Entwicklungszwecken können Sie die von Twilio zur Verfügung gestellten URLs verwenden, um die von Ihren Anwendungen verwendeten TwiML-Antworten bereitzustellen. Sie können auch Ihre eigenen URLs hosten, um TwiML-Antworten zu generieren; eine weitere Option ist die Verwendung des **TwiMLResponse**-Objekts.

Weitere Informationen zu Twilio-Verben, ihren Attributen sowie TwiML finden Sie unter [TwiML] [twiml]. Weitere Informationen zur Twilio-API finden Sie unter [Twilio-API] [twilio_api].

<h2><a id="CreateAccount"></a>Erstellen eines Twilio-Kontos</h2>
Wenn Sie ein Twilio-Konto erstellen möchten, melden Sie sich bei [Try Twilio] [try_twilio] an. Sie können mit einem kostenlosen Konto beginnen und später ein Upgrade vornehmen.

Wenn Sie sich für ein Twilio-Konto anmelden, erhalten Sie eine Konto-ID und ein Authentifizierungstoken. Beide Angaben benötigen Sie zum Tätigen von Twilio-API-Anrufen. Um den unbefugten Zugriff auf Ihr Konto zu verhindern, bewahren Sie Ihr Authentifizierungstoken sicher auf. Ihre Konto-ID und das Authentifizierungstoken können Sie auf der [Twilio-Kontoseite] [twilio_account] in den Feldern **ACCOUNT SID** bzw. **AUTH TOKEN** einsehen.

<h2><a id="create_app"></a>Erstellen einer PHP-Anwendung</h2>
PHP-Anwendungen, die den Twilio-Dienst verwenden und in Azure ausgeführt werden, unterscheiden sich nicht von anderen PHP-Anwendungen, die den Twilio-Dienst verwenden. Twilio-Dienste sind REST-basiert und können in PHP auf verschiedene Arten aufgerufen werden. Dieser Artikel konzentriert sich auf die Verwendung von Twilio mit der [Twilio-Bibliothek für PHP von Github][twilio_php]. Weitere Informationen zur Verwendung der Twilio-Bibliothek für PHP finden Sie unter [http://readthedocs.org/docs/twilio-php/en/latest/index.html][twilio_lib_docs].

Ausführliche Informationen zur Erstellung und Bereitstellung von Twilio/PHP-Anwendungen in Azure finden Sie unter [Tätigen eines Telefonanrufs mit Twilio in einer Java-Anwendung in Azure][howto_phonecall_php].

<h2><a id="configure_app"></a>Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken</h2>
Sie können die Anwendung auf zwei verschiedene Arten für die Twilio-Bibliothek in PHP konfigurieren:

1. Sie können die Twilio-Bibliothek für PHP von Github ([https://github.com/twilio/twilio-php][twilio_php]) herunterladen und das **Services**-Verzeichnis zu Ihrer Anwendung hinzufügen.

	-OR-

2. Sie können die Twilio-Bibliothek für PHP als PEAR-Paket installieren. Führen Sie dazu die folgenden Befehle aus:

		$ pear channel-discover twilio.github.com/pear
		$ pear install twilio/Services_Twilio

Nachdem Sie die Twilio-Bibliothek für PHP installiert haben, können Sie eine **require_once**-Anweisung am Anfang Ihrer PHP-Dateien hinzufügen, um auf die Bibliothek zu verweisen:

    	require_once 'Services/Twilio.php';

Weitere Informationen finden Sie unter [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].

<h2><a id="howto_make_call"></a>Gewusst wie: Tätigen von ausgehenden Anrufen</h2>
Nachfolgend wird erläutert, wie Sie einen ausgehenden Anruf mit der **Services_Twilio**-Klasse tätigen. Dieser Code verwendet eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort (Twilio Markup Language). Geben Sie Ihre Werte für die Telefonnummern **From** und **To** ein, und stellen Sie sicher, dass Sie die **From**-Telefonnummer für Ihr Twilio-Konto verifizieren, bevor Sie den Code ausführen.

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

Wie bereits erwähnt, verwendet dieser Code eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort. Stattdessen können Sie die TwiML-Antwort auch von Ihrer eigenen Website bereitstellen lassen. Weitere Informationen dazu finden Sie unter [Bereitstellen von TwiML-Antworten von der eigenen Website](#howto_provide_twiml_responses).


- **Hinweis**: Informationen zur Behandlung von Validierungsfehlern für SSL-Zertifikate finden Sie unter [http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html][ssl_validation]. 


<h2><a id="howto_send_sms"></a>Gewusst wie: Senden einer SMS-Nachricht</h2>
Das folgende Beispiel zeigt, wie Sie eine SMS-Nachricht mit der **Services_Twilio**-Klasse senden. Die **From**-Nummer wird von Twilio für Testkonten zum Senden von SMS-Nachrichten bereitgestellt. Die **To**-Nummer muss für Ihr Twilio-Konto überprüft werden, bevor Sie den Code ausführen.

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

<h2><a id="howto_provide_twiml_responses"></a>Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website</h2>
Wenn Ihre Anwendung einen Anruf an die Twilio-API initiiert, sendet Twilio Ihre Anforderung an eine URL, von der die Rückgabe einer TwiML-Antwort erwartet wird. Das obige Beispiel verwendet die von Twilio bereitgestellte URL [http://twimlets.com/message][twimlet_message_url]. (TwiML wurde für die Verwendung durch Twilio konzipiert, kann jedoch im Browser angezeigt werden. Klicken Sie z. B. auf [http://twimlets.com/message][twimlet_message_url], um ein leeres `<Response>`-Element anzuzeigen, oder klicken Sie auf [http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world], um ein `<Response>`-Element mit einem `<Say>`-Element anzuzeigen.

Anstelle der von Twilio bereitgestellten URL können Sie auch Ihre eigene Website für die Rückgabe von HTTP-Antworten erstellen. Sie können die Website in allen Sprachen erstellen, die XML-Antworten zurückgeben. Dieses Beispiel geht davon aus, dass Sie die TwiML-Antwort mit PHP erstellen.

Die folgende PHP-Seite erstellt eine TwiML-Antwort, die beim Anruf **Hello World** sagt.

    <?php    
		header("content-type: text/xml");    
		echo "<?xml version=\"1.0\" encoding=\"UTF-8\"?>\n";
	?>
	<Response>    
		<Say>Hello world.</Say>
	</Response>

Wie das vorstehende Beispiel zeigt, handelt es sich bei der TwiML-Antwort einfach um ein XML-Dokument. Die Twilio-Bibliothek für PHP enthält Klassen, die TwiML für Sie generieren. Das folgende Beispiel generiert die gleiche Antwort wie oben, jedoch unter Verwendung der **Services_Twilio_Twiml**-Klasse in der Twilio-Bibliothek für PHP:

	require_once('Services/Twilio.php');
	
	$response = new Services_Twilio_Twiml();
	$response->say("Hello world.");
	print $response;

Weitere Informationen zu TwiML finden Sie auf [https://www.twilio.com/docs/api/twiml][twiml_reference]. 

Sobald Sie Ihre PHP-Seite mit den TwiML-Antworten eingerichtet haben, können Sie die URL der PHP-Seite an die `Services_Twilio->account->calls->create`-Methode übergeben. Beispiel: Ihre Webanwendung **MyTwiML** wird für einen unter Azure gehosteten Dienst bereitgestellt, und der Name der PHP-Seite lautet **mytwiml.php**. In diesem Fall kann die URL wie im folgenden Codebeispiel gezeigt an **Services_Twilio->account->calls->create** übergeben werden:

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

Weitere Informationen zur Verwendung von Twilio in Azure mit PHP finden Sie unter [Tätigen eines Telefonanrufs mithilfe von Twilio in einer PHP-Anwendung auf Azure][howto_phonecall_php].

<h2><a id="AdditionalServices"></a>Gewusst wie: Verwenden zusätzlicher Twilio-Dienste</h2>
Zusätzlich zu den hier gezeigten Beispielen bietet Twilio webbasierte APIs, mit denen Sie zusätzliche Twilio-Funktionen in Ihrer Azure-Anwendung verwenden können. Ausführliche Informationen finden Sie in der [Twilio API-Dokumentation] [twilio_api_documentation].

<h2><a id="NextSteps"></a>Nächste Schritte</h2>
Nachdem Sie nun mit den Grundlagen des Twilio-Dienstes vertraut sind, finden Sie unter diesen Links weitere Informationen:

* [Twilio-Sicherheitsrichtlinien] [twilio_security_guidelines]
* [Twilio-Anleitungen und Beispielcode] [twilio_howtos]
* [Twilio-Schnellstart-Lernprogramme][twilio_quickstarts]
* [Twilio auf GitHub] [twilio_on_github]
* [Kontakt zum Twilio-Support] [twilio_support]

[twilio_php]: https://github.com/twilio/twilio-php
[twilio_lib_docs]: http://readthedocs.org/docs/twilio-php/en/latest/index.html
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html

[howto_phonecall_php]: http://windowsazure.com/de-de/documentation/articles/partner-twilio-php-make-phone-call
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twiml_reference]: https://www.twilio.com/docs/api/twiml
[twilio_pricing]: http://www.twilio.com/pricing

[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart

<!--HONumber=35.2-->
