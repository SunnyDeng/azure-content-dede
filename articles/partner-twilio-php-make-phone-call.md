<properties title="How to make a phone call from Twilio (PHP) - Azure" pageTitle="How to make a phone call from Twilio (PHP) - Azure" metaKeywords="Azure PHP Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, PHP twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Samples are for PHP application." documentationCenter="PHP" services="" solutions="" videoId="" scriptId="" authors="waltpo" manager="bjsmith" editor="mollybos" />

Tätigen eines Telefonanrufs mithilfe von Twilio in einer PHP-Anwendung auf Azure
================================================================================

Das folgende Beispiel zeigt, wie Sie von einer in Azure gehosteten PHP-Webseite einen Anruf über Twilio tätigen können. Die Anwendung fragt den Benutzer nach Werten für den Telefonanruf, wie im folgenden Screenshot gezeigt.

![Azure-Anrufformular mit Twilio und PHP](./media/partner-twilio-php-make-phone-call/WA_TwilioPHPCallForm.jpg)

Sie benötigen Folgendes, um den Code in diesem Artikel ausführen zu können:

1.  Ein Twilio-Konto und ein Authentifizierungs-Token. Sie können Sie die Preise von Twilio unter <http://www.twilio.com/pricing> ansehen. Sie können unter [https://www.twilio.com/try-twilio](http://www.twilio.com/try-twilio) ein Probekonto registrieren. Weitere Informationen über die von Twilio bereitgestellte API finden Sie unter <http://www.twilio.com/api>.
2.  Verifizieren Sie Ihre Telefonnummer als ausgehende Anrufer-ID bei Twilio. Informationen zum Verifizieren Ihrer Telefonnummer finden Sie unter <https://www.twilio.com/user/account/phone-numbers/verified#>. Alternativ zur Verwendung einer vorhandenen Telefonnummer besteht die Möglichkeit, eine Twilio-Telefonnummer zu kaufen. Verwenden Sie für dieses Beispiel die verifizierte Telefonnummer als **From**-Wert in **callform.php** (weiter unten beschrieben).
3.  Laden Sie die Twilio-Bibliothek für PHP herunter. Sie können die Bibliothek auf Github herunterladen (<https://github.com/twilio/twilio-php>) oder als PEAR-Paket installieren. Weitere Informationen finden Sie unter <https://github.com/twilio/twilio-php/blob/master/README.md>.
4.  Installieren Sie das Azure SDK für PHP. Eine Übersicht über das SDK und Hinweise für die Installation finden Sie unter [Einrichten des Azure SDK für PHP](http://azurephp.interoperabilitybridges.com/articles/setup-the-windows-azure-sdk-for-php).

Erstellen eines Web-Formulars für den Anruf
-------------------------------------------

Der folgende HTML-Code erstellt eine Webseite (**callform.html**) zur Eingabe der Benutzerdaten für den Anruf:

    <html>
    <head>
        <title>Automated call form</title>
    </head>
    <body>
    <h1>Automated Call Form</h1>

&lt;p\>Füllen Sie die Felder aus und klicken Sie auf **Make this call**.&lt;/p\> &lt;form action="makecall.php" method="post"\> &lt;table\>

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

Erstellen des Codes für den Anruf
---------------------------------

Der folgende Code erstellt eine Webseite (**makecall.php**), die aufgerufen wird, wenn der Benutzer das Formular aus **callform.html** übermittelt. Der folgende Code erstellt die Anrufnachricht und führt den Anruf aus. (Ersetzen Sie die Platzhalterwerte **\$sid** und **\$token** im folgenden Code durch Ihr Twilio-Konto und Ihr Authentifizierungs-Token.)

    <html>
    <head><title>Making call...</title></head>
    <body>
    <p>Your call is being made.</p>

    <
    php
    require_once 'Services/Twilio.php';

    $sid = "your_account_sid";
    $token = "your_authentication_token";

    $from_number = $_POST['callFrom']; // Anrufe müssen von einer registrierten Twilio-Nummer ausgeführt werden.
    $to_number = $_POST['callTo'];
    $message = $_POST['callText'];

    $client = new Services_Twilio($sid, $token, "2010-04-01");

    $call = $client->account->calls->create(
        $from_number, 
        $to_number,
      'http://twimlets.com/message?Message='.urlencode(\$message)
    );

    echo "Call status: ".$call->status."<br />";
    echo "URI resource: ".$call->uri."<br />";

    >
    </body>
    </html>

**makecall.php** führt nicht nur den Anruf aus, sondern zeigt außerdem auch einige Anruf-Metadaten an (der folgende Screenshot zeigt ein Beispiel). Weitere Informationen zu Anruf-Metadaten finden Sie unter <https://www.twilio.com/docs/api/rest/call#instance-properties>.

![Azure-Anrufantwort mit Twilio und PHP](./media/partner-twilio-php-make-phone-call/WA_TwilioPHPMakeCall.jpg)

Ausführen der Anwendung
-----------------------

Im nächsten Schritt stellen Sie Ihre Anwendung auf Azure Web Sites bereit. Die folgenden Artikel enthalten Informationen zur Erstellung einer Website und deren Bereitstellung mit with Git, FTP oder WebMatrix (wobei nicht alle Informationen in allen Artikeln relevant sind):

-   [Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über Git](https://www.windowsazure.com/en-us/develop/php/tutorials/website-w-mysql-and-git/)
-   [Erstellen einer PHP-MySQL-Azure-Website und Bereitstellen über FTP](https://www.windowsazure.com/en-us/develop/php/tutorials/website-w-mysql-and-ftp/)
-   [Erstellen und Bereitstellen einer PHP-MySQL-Azure-Website über WebMatrix](https://www.windowsazure.com/en-us/develop/php/tutorials/website-w-mysql-and-webmatrix/)

Nächste Schritte
----------------

Dieser Code demonstriert die allgemeinen Funktionen für die Verwendung von Twilio mit PHP in Azure. Bevor Sie dieses Beispiel in einer Produktionsumgebung bereitstellen, sollten Sie einige Funktionen zur Fehlerbehandlung oder andere Features hinzufügen. Beispiel:

-   Anstelle eines Web-Formulars könnten Sie Azure-Speicher-Blobs oder eine SQL-Datenbank zum Speichern von Telefonnummern und Anruftexten verwenden. Weitere Informationen zur Verwendung von Azure-Speicher-Blobs in PHP finden Sie unter [Verwenden des Blob-Speichers in PHP-Anwendungen](http://msdn.microsoft.com/en-us/library/windowsazure/hh674502(v=vs.103).aspx). Informationen zur Verwendung von SQL-Datenbanken in PHP finden Sie unter [Verwenden der SQL-Datenbank in PHP-Anwendungen](http://msdn.microsoft.com/en-us/library/windowsazure/hh674500(v=vs.103).aspx).
-   Der Code in **makecall.php** verwendet eine von Twilio bereitgestellte URL (<http://twimlets.com/message>), die eine Antwort in der Twilio Markup Language (TwiML) zurückgibt, die Twilio mitteilt, wie mit dem Anruf verfahren werden soll. Das zurückgegebene TwiML kann z. B. ein `<Say>`-Verb enthalten, das dazu führt, dass dem Anrufempfänger ein bestimmter Text vorgesprochen wird. Anstelle der von Twilio bereitgestellten URL können Sie auch mit einem eigenen Dienst auf die Twilio-Anfrage antworten. Weitere Informationen finden Sie unter [Verwenden von Twilio für Telefonie- und SMS-Funktionen in PHP](../partner-twilio-php-how-to-use-voice-sms). Weitere Informationen zu TwiML finden Sie unter <http://www.twilio.com/docs/api/twiml>, und weitere Informationen zu `<Say>` und anderen Twilio-Verben finden Sie unter <http://www.twilio.com/docs/api/twiml/say>.
-   Lesen Sie die Twilio-Sicherheitsrichtlinien unter [https://www.twilio.com/docs/security](http://www.twilio.com/docs/security).

Weitere Informationen zu Twilio finden Sie unter [https://www.twilio.com/docs](http://www.twilio.com/docs).

Weitere Informationen
---------------------

-   [Verwenden von Twilio für Telefonie- und SMS-Funktionen in PHP](../partner-twilio-php-how-to-use-voice-sms)

