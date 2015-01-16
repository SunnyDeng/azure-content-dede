<properties title="How to make a phone call from Twilio (PHP) - Azure" pageTitle="Tätigen eines Telefonanrufs über Twilio (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure Twilio, Azure-Anrufe, Azure Twilio, Azure-SMS, Azure SMS, Azure-Telefonate, Azure-Sprachanrufe, Azure-Textnachrichten, Azure Textnachrichten, PHP twilio Azure" description="Erfahren Sie, wie Sie mit dem Twilio API-Dienst in Azure einen Telefonanruf tätigen und eine SMS-Nachricht senden. Die Beispiele sind für eine PHP-Anwendung vorgesehen." documentationCenter="PHP" services="" solutions="" videoId="" scriptId="" authors="MicrosoftHelp@twilio.com; robmcm" manager="twilio" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm" />

# Tätigen eines Telefonanrufs mithilfe von Twilio in einer PHP-Anwendung auf Azure 

Das folgende Beispiel zeigt, wie Sie von einer in Azure gehosteten PHP-Webseite einen Anruf über Twilio tätigen können. Die Anwendung fragt den Benutzer nach Werten für den Telefonanruf, wie im folgenden Screenshot gezeigt.

![Azure Call Form Using Twilio and PHP][twilio_php]

Sie benötigen Folgendes, um den Code in diesem Artikel ausführen zu können:

1. Erhalten Sie ein Twilio-Konto und ein Authentifizierungs-Token. Sie können Sie die Preise von Twilio unter [http://www.twilio.com/pricing][twilio_pricing] ansehen. Sie können unter [https://www.twilio.com/try-twilio][try_twilio] ein Probekonto registrieren. Weitere Informationen zu der von Twilio bereitgestellten API finden Sie unter [http://www.twilio.com/api][twilio_api].
2. Verifizieren Sie Ihre Telefonnummer als ausgehende Anrufer-ID bei Twilio. Informationen zum Verifizieren Ihrer Telefonnummer finden Sie unter [https://www.twilio.com/user/account/phone-numbers/verified#][verify_phone]. Alternativ zur Verwendung einer vorhandenen Telefonnummer besteht die Möglichkeit, eine Twilio-Telefonnummer zu kaufen.
Verwenden Sie für dieses Beispiel die verifizierte Telefonnummer als **From**-Wert in **callform.php** (weiter unten beschrieben).
3. Laden Sie die Twilio-Bibliothek für PHP herunter. Sie können die Bibliothek auf Github herunterladen ([https://github.com/twilio/twilio-php][twilio_php_github]) oder als PEAR-Paket installieren. Weitere Informationen finden Sie unter [https://github.com/twilio/twilio-php/blob/master/README.md][twilio_github_readme].
4. Installieren Sie das Azure SDK für PHP. Eine Übersicht über das SDK und Hinweise für die Installation finden Sie unter [Einrichten des Azure SDK für PHP][setup_php_sdk].

## Erstellen eines Web-Formulars für den Anruf

Der folgende HTML-Code erstellt eine Webseite (**callform.html**) zur Eingabe der Benutzerdaten für den Anruf:

    <html>
	<head>
		<title>Automated call form</title>
	</head>
	<body>
	<h1>Automated Call Form</h1>
 	<p>Fill in all fields and click <b>Make this call</b>.</p>
  	<form action="makecall.php" method="post">
   	<table>
     	<tr>
       		<td>To:</td>
       		<td><input type="text" size=50 name="callTo" value=""></td>
     	</tr>
     	<tr>
       		<td>From:</td>
       		<td><input type="text" size=50 name="callFrom" value=""></td>
     	</tr>
     	<tr>
       		<td>Call message:</td>
       		<td><input type="text" size=100 name="callText" value="Hello. This is the call text. Good bye." /></td>
     	</tr>
     	<tr>
       		<td colspan=2><input type="submit" value="Make this call"></td>
     	</tr>
   	</table>
 	</form>
 	<br/>
	</body>
	</html>

## Erstellen des Codes für den Anruf
Der folgende Code erstellt eine Webseite (**makecall.php**), die aufgerufen wird, wenn der Benutzer das Formular aus **callform.html** übermittelt. Der folgende Code erstellt die Anrufnachricht und führt den Anruf aus. (Ersetzen Sie die Platzhalterwerte **$sid** und **$token** im folgenden Code durch Ihr Twilio-Konto und Ihr Authentifizierungs-Token.)

    <html>
	<head><title>Making call...</title></head>
	<body>
	<p>Your call is being made.</p>

	<?php
	require_once 'Services/Twilio.php';

	$sid = "your_account_sid";
	$token = "your_authentication_token";

	$from_number = $_POST['callFrom']; // Calls must be made from a registered Twilio number.
	$to_number = $_POST['callTo'];
	$message = $_POST['callText'];

	$client = new Services_Twilio($sid, $token, "2010-04-01");

	$call = $client->account->calls->create(
		$from_number, 
		$to_number,
  		'http://twimlets.com/message?Message='.urlencode($message)
	);

	echo "Call status: ".$call->status."<br />";
	echo "URI resource: ".$call->uri."<br />";
	?>
	</body>
	</html>

**makecall.php** führt nicht nur den Anruf aus, sondern zeigt außerdem auch einige Anruf-Metadaten an (der folgende Screenshot zeigt ein Beispiel). Weitere Informationen zu Anruf-Metadaten finden Sie unter [https://www.twilio.com/docs/api/rest/call#instance-properties][twilio_call_properties].

![Azure Call Response Using Twilio and PHP][twilio_php_response]

## Ausführen der Anwendung
Im nächsten Schritt stellen Sie die Anwendung auf Azure-Websites bereit. Die folgenden Artikel enthalten Informationen zur Erstellung einer Website und deren Bereitstellung mit Git, FTP oder WebMatrix (wobei nicht alle Informationen in allen Artikeln relevant sind):

* [Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über Git][website-git]
* [Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über FTP][website-ftp]
* [Erstellen und Bereitstellen einer PHP-MySQL-Azure-Website über WebMatrix][website-webmatrix]

## Nächste Schritte
Dieser Code demonstriert die allgemeinen Funktionen für die Verwendung von Twilio mit PHP in Azure. Bevor Sie dieses Beispiel in einer Produktionsumgebung bereitstellen, sollten Sie einige Funktionen zur Fehlerbehandlung oder andere Features hinzufügen. Beispiel:

* Anstelle eines Web-Formulars können Sie Azure-Speicher-BLOBs oder eine SQL-Datenbank zum Speichern von Telefonnummern und Anruftexten verwenden. Weitere Informationen zur Verwendung von Azure-Speicher-Blobs in PHP finden Sie unter [Verwenden des Blob-Speichers in PHP-Anwendungen][howto_blob_storage_php]. Informationen zur Verwendung von SQL-Datenbanken in PHP finden Sie unter [Verwenden der SQL-Datenbank in PHP-Anwendungen][howto_sql_azure_php].
* Der Code in **makecall.php** verwendet eine von Twilio bereitgestellte URL ([http://twimlets.com/message][twimlet_message_url]), die eine Antwort in der Twilio Markup Language (TwiML) zurückgibt, die Twilio mitteilt, wie mit dem Anruf verfahren werden soll. Das zurückgegebene TwiML kann z. B. ein `<Say>`-Verb enthalten, das dazu führt, dass dem Anrufempfänger ein bestimmter Text vorgesprochen wird. Anstelle der von Twilio bereitgestellten URL können Sie auch mit einem eigenen Dienst auf die Twilio-Anfrage antworten. Weitere Informationen finden Sie unter [Verwenden von Twilio für Telefonie- und SMS-Funktionen in PHP][howto_twilio_voice_sms_php]. Weitere Informationen zu TwiML finden Sie unter [http://www.twilio.com/docs/api/twiml][twiml], und weitere Informationen zu `<Say>` und anderen Twilio-Verben finden Sie unter [http://www.twilio.com/docs/api/twiml/say][twilio_say].
* Lesen Sie die Twilio-Sicherheitsrichtlinien unter [https://www.twilio.com/docs/security][twilio_docs_security].

Weitere Informationen zu Twilio finden Sie unter [https://www.twilio.com/docs][twilio_docs].

## Weitere Informationen
* [Verwenden von Twilio für Telefonie- und SMS-Funktionen in PHP](../partner-twilio-php-how-to-use-voice-sms)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_php]: https://github.com/twilio/twilio-php
[twilio_github_readme]: https://github.com/twilio/twilio-php/blob/master/README.md
[setup_php_sdk]: http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php
[twimlet_message_url]: http://twimlets.com/message
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api_service]: http://api.twilio.com
[build_php_azure_app]: http://azurephp.interoperabilitybridges.com/articles/build-and-deploy-a-windows-azure-php-application
[howto_twilio_voice_sms_php]: ../partner-twilio-php-how-to-use-voice-sms
[howto_blob_storage_php]: http://azure.microsoft.com/de-de/documentation/articles/storage-php-how-to-use-blobs/
[howto_sql_azure_php]: http://azure.microsoft.com/de-de/documentation/articles/sql-database-php-how-to-use/
[twilio_call_properties]: https://www.twilio.com/docs/api/rest/call#instance-properties
[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say
[ssl_validation]: http://readthedocs.org/docs/twilio-php/en/latest/usage/rest.html
[twilio_php]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg
[twilio_php_response]: ./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg
[website-git]: https://www.windowsazure.com/de-de/develop/php/tutorials/website-w-mysql-and-git/
[website-ftp]: https://www.windowsazure.com/de-de/develop/php/tutorials/website-w-mysql-and-ftp/
[website-webmatrix]: https://www.windowsazure.com/de-de/develop/php/tutorials/website-w-mysql-and-webmatrix/
[twilio_php_github]: https://github.com/twilio/twilio-php
