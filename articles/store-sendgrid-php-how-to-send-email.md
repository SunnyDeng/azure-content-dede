<properties title="How to use the SendGrid email service (PHP) - Azure" pageTitle="How to use the SendGrid email service (PHP) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid PHP, Azure email PHP" description="Learn how send email with the SendGrid email service on Azure. Code samples written in PHP." documentationCenter="PHP" services="" manager="wpickett" editor="mollybos" authors="robmcm" scriptId="" videoId="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="PHP" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Verwenden des SendGrid-E-Mail-Dienstes aus PHP

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem E-Mail-Dienst SendGrid in Azure. Die Beispiele sind in PHP geschrieben.
Die behandelten Szenarien umfassen das **Erstellen einer E-Mail**, **Senden einer E-Mail** und **Hinzufügen von Anhängen**. Weitere Informationen zu SendGrid und zum Senden von E-Mails erhalten Sie im Abschnitt [Nächste Schritte][Nächste Schritte].

## Inhaltsverzeichnis

-   [Was ist der SendGrid-E-Mail-Dienst?][Was ist der SendGrid-E-Mail-Dienst?]
-   [Erstellen eines SendGrid-Kontos][Erstellen eines SendGrid-Kontos]
-   [Verwenden von SendGrid aus der PHP-Anwendung][Verwenden von SendGrid aus der PHP-Anwendung]
-   [Gewusst wie: Senden einer E-Mail][Gewusst wie: Senden einer E-Mail]
-   [Gewusst wie: Hinzufügen eines Anhangs][Gewusst wie: Hinzufügen eines Anhangs]
-   [Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen][Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen]
-   [Nächste Schritte][Nächste Schritte]

## <a name="bkmk_WhatIsSendGrid"> </a>Was ist der SendGrid-E-Mail-Dienst?

SendGrid ist ein [cloudbasierter E-Mail-Dienst][cloudbasierter E-Mail-Dienst], der zuverlässige
[transaktionale E-Mail-Übermittlung][transaktionale E-Mail-Übermittlung], Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs
 bietet, die die benutzerdefinierte Integration erleichtern. Häufige Verwendungsszenarien für
SendGrid:

-   Automatisches Versenden von Empfangsbestätigungen an Kunden
-   Verwalten von Verteilungslisten zum Senden von monatlichen
    e-Flyern und speziellen Angeboten an Kunden
-   Sammeln von Echtzeit-Kennzahlen für Sachverhalte wie gesperrte E-Mails oder
    Kundenreaktionszeit
-   Generieren von Berichten zur Identifizierung von Trends
-   Weiterleiten von Kundenanfragen
-   E-Mail-Benachrichtigungen von Ihrer Anwendung aus

Weitere Informationen finden Sie unter [][]<http://sendgrid.com></a>.

## <a name="bkmk_CreateSendGrid"> </a>Erstellen eines SendGrid-Kontos

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="bkmk_UsingSendGridfromPHP"> </a>Verwenden von SendGrid aus der PHP-Anwendung

Die Verwendung von SendGrid in Azure-PHP-Anwendungen erfordert keine besondere
Konfiguration oder Programmierung. Da es sich bei SendGrid um einen Dienst handelt
, kann auf ihn von einer Cloud-Anwendung auf dieselbe Weise zugegriffen werden wie von
einer lokalen Anwendung.

## <a name="bkmk_HowToSendEmail"> </a>Gewusst wie: Senden einer E-Mail

E-Mails können Sie entweder über SMTP oder über die von
SendGrid bereitgestellte Web-API senden.

### SMTP-API

Um eine E-Mail über die SendGrid-SMTP-API zu versenden, verwenden Sie *Swift Mailer*, eine
komponentenbasierte Bibliothek zum Versenden von E-Mails aus PHP-Anwendungen. Die
*Swift Mailer*-Bibliothek können Sie von
[][1]<http://swiftmailer.org/download></a> herunterladen. Das Versenden von E-Mails mithilfe der Bibliothek
umfasst das Erstellen von Instanzen der Klassen
<span class="auto-style2">Swift\_SmtpTransport</span>,
<span class="auto-style2">Swift\_Mailer</span> und
<span class="auto-style2">Swift\_Message</span>, das Festlegen der
entsprechenden Eigenschaften und das Aufrufen der
<span class="auto-style2">Swift\_Mailer::send</span>-Methode.

    <?php
     include_once "lib/swift_required.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */ 
     $text = "Hi!\nHow are you?\n";
     $html = <<<EOM
           <html>
           <head></head>
           <body>
               <p>Hi!<br>
                   How are you?<br>
               </p>
           </body>
           </html>
           EOM;
     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     // Email recipients
     $to = array(
           'john@contoso.com'=>'Destination 1 Name',
           'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';

     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);

     // Create a message (subject)
     $message = new Swift_Message($subject);

     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
         // This will let us know how many users received this message
         echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
         echo "Something went wrong - ";
         print_r($failures);
     }

### Web-API

Um E-Mails über die SendGrid-Web-API zu versenden, verwenden Sie die [Curl -Funktion][Curl -Funktion] von PHP.

    <?php

     $url = 'http://sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD'; 

     $params = array(
          'api_user' => $user,
          'api_key' => $pass,
          'to' => 'john@contoso.com',
          'subject' => 'testing from curl',
          'html' => 'testing body',
          'text' => 'testing body',
          'from' => 'anna@sendgrid.com',
       );
       
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

Die Web-API von SendGrid ähnelt stark einer REST-API, obwohl es
sich eigentlich nicht um eine RESTful-API handelt, da in den meisten Aufrufen die Verben GET und POST
austauschbar sind.

## <a name="bkmk_HowToAddAttachment"> </a>Gewusst wie: Hinzufügen eines Anhangs

### SMTP-API

Das Versenden eines Anhangs über die SMTP-API umfasst eine zusätzliche
Codezeile im Beispielskript für das Versenden von E-Mails mit Swift Mailer.

    <?php
     include_once "lib/swift_required.php";
     /*
      * Create the body of the message (a plain-text and an HTML version).
      * $text is your plain-text email
      * $html is your html version of the email
      * If the reciever is able to view html emails then only the html
      * email will be displayed
      */
     $text = "Hi!\nHow are you?\n";
      $html = <<<EOM
          <html>
          <head></head>
          <body>
             <p>Hi!<br>
                How are you?<br>
             </p>
          </body>
          </html>
     EOM;

     // This is your From email address
     $from = array('someone@example.com' => 'Name To Appear');
     
     // Email recipients
     $to = array(
          'john@contoso.com'=>'Destination 1 Name',
          'anna@contoso.com'=>'Destination 2 Name'
     );
     // Email subject
     $subject = 'Example PHP Email';
     
     // Login credentials
     $username = 'yoursendgridusername';
     $password = 'yourpassword';
     
     // Setup Swift mailer parameters
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 587);
     $transport->setUsername($username);
     $transport->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName("file_name"));
     
     // send message 
     if ($recipients = $swift->send($message, $failures))
     {
          // This will let us know how many users received this message
          echo 'Message sent out to '.$recipients.' users';
     }
     // something went wrong =(
     else
     {
          echo "Something went wrong - "
          print_r($failures);
     }

Die zusätzliche Codezeile lautet:

     $message->attach(Swift_Attachment::fromPath("path\to\file")->setFileName('file_name'));

Diese Codezeile ruft die Attach-Methode auf dem
<span class="auto-style2">Swift\_Message</span>-Objekt auf und verwendet die statische
Methode <span class="auto-style2">fromPath</span> in der
<span class="auto-style2">Swift\_Attachment</span>-Klasse,
um eine Datei an eine Nachricht anzuhängen.

### Web-API

Das Versenden eines Anhangs über die Web-API ähnelt dem
Versenden von E-Mails über die Web-API. Beachten Sie jedoch, dass im folgenden Beispiel
das Parameterarray das folgende Element enthalten muss:

     'files['.$fileName.']' => '@'.$filePath.'/'.$fileName

    <?php

     $url = 'http://sendgrid.com/';
     $user = 'USERNAME';
     $pass = 'PASSWORD';
     
     $fileName = 'myfile';
     $filePath = dirname(__FILE__);

     $params = array(
         'api_user' => $user,
         'api_key' => $pass,
         'to' =>'john@contoso.com',
         'subject' => 'test of file sends',
         'html' => '<p> the HTML </p>',
         'text' => 'the plain text',
         'from' => 'anna@sendgrid.com',
         'files['.$fileName.']' => '@'.$filePath.'/'.$fileName
     );
     
     print_r($params);
     
     $request = $url.'api/mail.send.json';
     
     // Generate curl request
     $session = curl_init($request);
     
     // Tell curl to use HTTP POST
     curl_setopt ($session, CURLOPT_POST, true);
     
     // Tell curl that this is the body of the POST
     curl_setopt ($session, CURLOPT_POSTFIELDS, $params);
     
     // Tell curl not to return headers, but do return the response
     curl_setopt($session, CURLOPT_HEADER, false);
     curl_setopt($session, CURLOPT_RETURNTRANSFER, true);
     
     // obtain response
     $response = curl_exec($session);
     curl_close($session);
     
     // print everything out
     print_r($response);

## <a name="bkmk_HowToUseFilters"> </a>Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen

SendGrid bietet zusätzliche E-Mail-Funktionen durch die Verwendung von
'Filtern'. Hierbei handelt es sich um Einstellungen, die zu einer E-Mail-Nachricht hinzugefügt werden können, um
spezifische Funktionen wie etwa Aktivierung von Klickprotokollierung, Google Analytics,
Abonnement-Tracking usw. zu ermöglichen.

Filter können mithilfe der Eigenschaft "filters" auf eine Nachricht angewendet werden. Jeder
Filter wird durch einen Hash mit filterspezifischen Einstellungen festgelegt. Im
folgenden Beispiel wird der Filter für Fußzeilen aktiviert und eine Textnachricht angegeben,
die an das Ende der E-Mail angehängt wird.

    <?php
     /*
      * This example is used for Swift Mailer V4
      */
     include "./lib/swift_required.php";
     include 'SmtpApiHeader.php';
     
     $hdr = new SmtpApiHeader();
     // The list of addresses this message will be sent to
     // [This list is used for sending multiple emails using just ONE request to 
     SendGrid]
     $toList = array('john@contoso.com', 'anna@contoso.com');
     
     // Specify the names of the recipients
     $nameList = array('Name 1', 'Name 2');
     
     // Used as an example of variable substitution
     $timeList = array('4 PM', '5 PM');
     
     // Set all of the above variables
     $hdr->addTo($toList);
     $hdr->addSubVal('-name-', $nameList);
     $hdr->addSubVal('-time-', $timeList);
     
     // Specify that this is an initial contact message
     $hdr->setCategory("initial");
     
     // You can optionally setup individual filters here, in this example, we have 
     enabled the footer filter
     $hdr->addFilterSetting('footer', 'enable', 1);
     $hdr->addFilterSetting('footer', "text/plain", "Thank you for your business");
     
     // The subject of your email
     $subject = 'Example SendGrid Email';
     
     // Where is this message coming from. For example, this message can be from support@yourcompany.com, info@yourcompany.com
     $from = array('someone@example.com' =&gt; 'Name Of Your Company');
     
     // If you do not specify a sender list above, you can specifiy the user here. If 
     // a sender list IS specified above, this email address becomes irrelevant.
     $to = array('john@contoso.com'=&gt;'Personal Name Of Recipient');
     
     # Create the body of the message (a plain-text and an HTML version). 
     # text is your plain-text email 
     # html is your html version of the email
     # if the receiver is able to view html emails then only the html
     # email will be displayed
     
     /*
     * Note the variable substitution here =)
     */
     $text = <<<EOM 
     Hello -name-,
     Thank you for your interest in our products. We have set up an appointment to call you at -time- EST to discuss your needs in more detail.
     Regards,
     Fred
     EOM;
     
     $html = <<<EOM
     < html> 
     <head></head>
     <body>
     <p>Hello -name-,<br>
     Thank you for your interest in our products. We have set up an appointment
     to call you at -time- EST to discuss your needs in more detail.
     
     Regards,
     
     Fred, How are you?<br>
     </p>
     </body>
     < /html>
     EOM;
     
     // Your SendGrid account credentials
     $username = 'sendgridusername@yourdomain.com';
     $password = 'example';
     
     // Create new swift connection and authenticate
     $transport = Swift_SmtpTransport::newInstance('smtp.sendgrid.net', 25);
     $transport ->setUsername($username);
     $transport ->setPassword($password);
     $swift = Swift_Mailer::newInstance($transport);
     
     // Create a message (subject)
     $message = new Swift_Message($subject);
     
     // add SMTPAPI header to the message
     // *****IMPORTANT NOTE*****
     // SendGrid's asJSON function escapes characters. If you are using Swift 
     Mailer's
     // PHP Mailer functions, the getTextHeader function will also escape characters.
     // This can cause the filter to be dropped.
     $headers = $message->getHeaders();
     $headers->addTextHeader('X-SMTPAPI', $hdr->asJSON());
     
     // attach the body of the email
     $message->setFrom($from);
     $message->setBody($html, 'text/html');
     $message->setTo($to);
     $message->addPart($text, 'text/plain');
     
     // send message
     if ($recipients = $swift->send($message, $failures))
     {
     // This will let us know how many users received this message
     // If we specify the names in the X-SMTPAPI header, then this will always be 1.
     echo 'Message sent out to '.$recipients.' users';
     }

     // something went wrong =(
     else
     {
     echo "Something went wrong - ";
     print_r($failures);
     }

## <a name="bkmk_NextSteps"> </a>Nächste Schritte

Nachdem Sie nun mit den Grundlagen des E-Mail-Dienstes SendGrid vertraut sind, finden Sie unter
diesen Links weitere Informationen.

-   SendGrid-Dokumentation: <https://sendgrid.com/docs>
-   Spezielles SendGrid-Angebot für Azure-Kunden: <http://sendgrid.com/azure.html>

  [Nächste Schritte]: #bkmk_NextSteps
  [Was ist der SendGrid-E-Mail-Dienst?]: #bkmk_WhatIsSendGrid
  [Erstellen eines SendGrid-Kontos]: #bkmk_CreateSendGrid
  [Verwenden von SendGrid aus der PHP-Anwendung]: #bkmk_UsingSendGridfromPHP
  [Gewusst wie: Senden einer E-Mail]: #bkmk_HowToSendEmail
  [Gewusst wie: Hinzufügen eines Anhangs]: #bkmk_HowToAddAttachment
  [Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen]: #bkmk_HowToUseFilters
  [cloudbasierter E-Mail-Dienst]: http://sendgrid.com/solutions
  [transaktionale E-Mail-Übermittlung]: http://sendgrid.com/transactional-email
  []: http://sendgrid.com
  [sendgrid-sign-up]: ../includes/sendgrid-sign-up.md
  [1]: http://swiftmailer.org/download
  [Curl -Funktion]: http://php.net/curl
