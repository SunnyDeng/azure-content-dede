<properties urlDisplayName="SendGrid Email Service" pageTitle="Verwenden des SendGrid-E-Mail-Dienstes (Node.js) - Azure"," metaKeywords="Azure SendGrid, Azure-E-Mail-Dienst, Azure SendGrid Node.js, Azure E-Mail Node.js" description="Learn how send email with the SendGrid email service on Azure. Code samples written using the Node.js API." metaCanonical="" services="" documentationCenter="nodejs" title="How to Send Email Using SendGrid from Node.js" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com" solutions="" manager="wpickett" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="10/30/2014" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork; matt.bernier@sendgrid.com" />





# Senden von E-Mails mit SendGrid aus Node.js

Dieser Leitfaden veranschaulicht die Durchführung allgemeiner Programmieraufgaben mit dem
SendGrid-E-Mail-Dienst in Azure. Die Beispiele sind in C# geschrieben und verwenden die Node.js-API. Die behandelten Szenarien umfassen das **Erstellen einer E-Mail**, **Senden einer E-Mail**, **Hinzufügen von Anhängen**, **Verwenden von Filtern** und **Aktualisieren von Eigenschaften**. Weitere Informationen zu SendGrid und zum Senden von E-Mails erhalten Sie im Abschnitt [Nächste Schritte][].

## Inhaltsverzeichnis

* [Was ist der SendGrid-E-Mail-Dienst?][]   
* [Erstellen eines SendGrid-Kontos][]   
* [Erstellen eines Verweises auf das SendGrid-Modul für Node.js][]   
* [Gewusst wie: Erstellen einer E-Mail][]   
* [Gewusst wie: Senden einer E-Mail][]   
* [Gewusst wie: Hinzufügen eines Anhangs][]   
* [Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen][]   
* [Gewusst wie: Aktualisieren von E-Mail-Eigenschaften][]   
* [Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste][]   
* [Nächste Schritte][1]

## <a name="whatis"> </a>Was ist der SendGrid-E-Mail-Dienst?

SendGrid ist ein [cloudbasierter E-Mail-Dienst], der zuverlässige [transaktionale E-Mail-Übermittlung], Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs bietet, die eine benutzerdefinierte Integration erleichtern. Häufige Verwendungsszenarien für SendGrid:

-   Automatisches Versenden von Empfangsbestätigungen an Kunden
-   Verwalten von Verteilerlisten, um Kunden monatlich
    E-Flyer und spezielle Angebote zu senden
-   Sammeln von Echtzeit-Kennzahlen für Sachverhalte wie gesperrte E-Mails und
    Kundenreaktionsfähigkeit
-   Generieren von Berichten zur Identifizierung von Trends
-   Weiterleiten von Kundenanfragen
-   E-Mail-Benachrichtigungen von Ihrer Anwendung aus

Weitere Informationen finden Sie unter [https://sendgrid.com](https://sendgrid.com).

## <a name="createaccount"> </a>Erstellen eines SendGrid-Kontos

[WACOM.INCLUDE [SendGrid-Anmeldung](../includes/sendgrid-sign-up.md)]

## <a name="reference"> </a>Erstellen eines Verweises auf das SendGrid Node.js-Modul

Das SendGrid-Modul für Node.js kann über den Node Package Manager (NPM) installiert werden. Verwenden Sie dafür den folgenden Befehl:

    npm install sendgrid

Nach der Installation können Sie das Modul mithilfe des folgenden Codes in Ihrer Anwendung erforderlich machen:

    var SendGrid = require('sendgrid')

Das SendGrid-Modul exportiert die Funktionen **SendGrid** und **Email**. **SendGrid** sendet E-Mails entweder über SMTP oder eine Web-API, während **Email** E-Mail-Nachrichten einkapselt.

## <a name="createemail"> </a>Gewusst wie: Erstellen einer E-Mail

Zum Erstellen einer E-Mail-Nachricht mit dem SendGrid-Modul muss zuerst
eine E-Mail-Nachricht mithilfe der Email-Funktion erstellt und dann
mithilfe der SendGrid-Funktion gesendet werden. Im Folgenden finden Sie ein Beispiel für das Erstellen einer
neuen Nachricht mit der Email-Funktion:

    var mail = new SendGrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

Sie können auch eine HTML-Nachricht für Clients festlegen, bei denen dies durch Einstellung der HTML-Eigenschaft unterstützt wird. Beispiel:

    html: This is a sample <b>HTML<b> email message.

Wenn die Text- und die HTML-Option ausgewählt sind, wird die Nachricht auf Clients, die keine HTML-Nachrichten unterstützen, im Textformat angezeigt.

Weitere Informationen zu allen von der Email-Funktion unterstützten Eigenschaften finden Sie unter [sendgrid-nodejs][].

## <a name="sendemail"> </a>Gewusst wie: Senden einer E-Mail

Nach der Erstellung einer E-Mail mithilfe der Email-Funktion können Sie diese entweder über SMTP oder über die von SendGrid bereitgestellte Web-API senden. Genaue Informationen zu den Vorteilen und den Unterschieden jeder API finden Sie in der SendGrid-Dokumentation unter [SMTP vs. Web API][].

Wenn Sie die SMTP-API oder die Web-API verwenden möchten, müssen Sie zuerst die Funktion "SendGrid" mithilfe des Benutzers und des Schlüssels Ihres SendGrid-Kontos initialisieren. Gehen Sie dazu folgendermaßen vor:

    var sender = new SendGrid('user','key');

Die Nachricht kann nun über SMTP oder die Web-API gesendet werden. Die Aufrufe sind praktisch identisch: Die E-Mail-Nachricht wird übergeben und eine optionale Rückruffunktion ausgeführt. Der Rückruf wird verwendet, um festzustellen, ob der Vorgang erfolgreich war oder nicht. Die folgenden Beispiele zeigen, wie Sie eine Nachricht sowohl über SMTP als auch über die Web-API senden.

### SMTP

    sender.smtp(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
    });

### Web-API

    sender.send(mail, function(success, err){
        if(success) console.log('Email sent');
        else console.log(err);
    });

<div class="dev-callout">
<strong>Hinweis</strong>
<p>Während die Beispiele oben die Übergabe eines E-Mail-Objekts und den Aufruf der Rückruf-Funktion zeigen, können Sie die Sende- und die SMTP-Funktionen auch direkt durch Festlegung der E-Mail-Eigenschaften aufrufen. Beispiel:</p>
<pre class="prettyprint">sender.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
</pre>
</div>

## <a name="addattachment"> </a>Gewusst wie: Hinzufügen eines Anhangs

Anhänge können einer Nachricht hinzugefügt werden, indem die Dateinamen und Pfade in der **files**-Eigenschaft angegeben werden. Im folgenden Beispiel wird das Senden eines Anhangs demonstriert:

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        files: {
            'file1.txt': __dirname + '/file1.txt',
            'image.jpg': __dirname + '/image.jpg'
        }
    });

<div class="dev-callout">
<strong>Hinweis</strong>
<p>Wenn Sie die <strong>files</strong>-Eigenschaft verwenden, muss die Datei über
<a href="http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile">fs.readFile</a> verfügbar sein. Wenn die Datei, die Sie hinzufügen möchten, in Azure Storage gehostet wird, z. B. in einem BLOB-Container, müssen Sie sie zuerst an einen lokalen Speicherort oder auf ein Azure-Laufwerk kopieren, bevor sie mithilfe der Eigenschaft <strong>files</strong> als Anhang gesendet werden kann.</p>
</div>

## <a name="usefilters"> </a>Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Twitter

SendGrid bietet zusätzliche E-Mail-Funktionen durch die Verwendung von Filtern. Hierbei handelt es sich um Einstellungen, die zu einer E-Mail-Nachricht hinzugefügt werden können, um spezifische Funktionen wie etwa Aktivierung von Klickprotokollierung, Google Analytics, Abonnement-Tracking usw. zu ermöglichen. Eine vollständige Liste der verfügbaren Filter finden Sie unter [Filtereinstellungen][].

Filter können mithilfe der Eigenschaft **filters** auf eine Nachricht angewendet werden. Jeder Filter wird durch einen Hash mit filterspezifischen Einstellungen festgelegt. Die folgenden Beispiele veranschaulichen die Filter für die Fußzeile, für die Klickprotokollierung und für Twitter:

### Fußzeile

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'footer': {
                'settings': {
                    'enable': 1,
                    'text/plain': 'This is a text footer.'
                }
            }
        }
    });

### Klickprotokollierung

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'clicktrack': {
                'settings': {
                    'enable': 1
                }
            }
        }
    });

### Twitter

    sender.send({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.',
        filters: {
            'twitter': {
                'settings': {
                    'enable': 1,
                    'username': 'twitter_username',
                    'password': 'twitter_password'
                }
            }
        }
    });

## <a name="updateproperties"> </a>Gewusst wie: Aktualisieren von E-Mail-Eigenschaften

Einige E-Mail-Eigenschaften können mithilfe von **set*Property*** überschrieben oder mit **add*Property*** angehängt werden. Sie können z. B. zusätzliche Empfänger hinzufügen:

    email.addTo('jeff@contoso.com');

oder einen Filter setzen:

    email.setFilterSetting({
      'footer': {
        'setting': {
          'enable': 1,
          'text/plain': 'This is a footer.'
        }
      }
    });

Weitere Informationen finden Sie unter [sendgrid-nodejs][].

## <a name="useservices"> </a>Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste

SendGrid bietet webbasierte APIs, die Sie zur Nutzung zusätzlicher SendGrid-Funktionen aus Ihrer Azure-Anwendung einsetzen können. Ausführliche Informationen finden Sie in der [SendGrid-API-Dokumentation][].

## <a name="nextsteps"> </a>Nächste Schritte

Nachdem Sie nun mit den Grundlagen des E-Mail-Dienstes SendGrid vertraut sind, finden Sie unter diesen Links weitere Informationen.

-   Repository für das SendGrid Node.js-Modul: [sendgrid-nodejs][]
-   SendGrid-API-Dokumentation:
    <https://sendgrid.com/docs>
-   Spezielles SendGrid-Angebot für Azure-Kunden:
    [http://sendgrid.com/azure.html](https://sendgrid.com/windowsazure.html)

  [Nächste Schritte]: http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/blob-storage/#next-steps
  [Was ist der SendGrid-E-Mail-Dienst?]: #whatis
  [Erstellen eines SendGrid-Kontos]: #createaccount
  [Erstellen eines Verweises auf das SendGrid Node.js-Modul]: #reference
  [Gewusst wie: Erstellen einer E-Mail]: #createemail
  [Gewusst wie: Senden einer E-Mail]: #sendemail
  [Gewusst wie: Hinzufügen eines Anhangs]: #addattachment
  [Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen]: #usefilters
  [Gewusst wie: Aktualisieren von E-Mail-Eigenschaften]: #updateproperties
  [Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste]: #useservices
  [1]: #nextsteps

  
  
  [Sonderangebot]: https://sendgrid.com/windowsazure.html
  
  
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  [SMTP vs. Web API]: https://sendgrid.com/docs/Integrate/index.html
  
  [Filter Settings]: https://sendgrid.com/docs/API_Reference/SMTP_API/apps.html
  [SendGrid API documentation]: https://sendgrid.com/docs
  [cloud-based email service]: https://sendgrid.com/email-solutions
  [transactional email delivery]: https://sendgrid.com/transactional-email
