<properties linkid="dev-nodejs-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (Node.js) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid Node.js, Azure email Node.js" description="Learn how send email with the SendGrid email service on Azure. Code samples written using the Node.js API." metaCanonical="" services="" documentationCenter="Node.js" title="How to Send Email Using SendGrid from Node.js" authors="" solutions="" manager="" editor="" />

Senden von E-Mails mit SendGrid aus Node.js
===========================================

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem E-Mail-Dienst SendGrid in Azure. Die Beispiele sind in C\# geschrieben und verwenden die Node.js-API. Die behandelten Szenarien umfassen das **Erstellen einer E-Mail**, **Senden einer E-Mail**, **Hinzufügen von Anhängen**, **Verwenden von Filtern** und **Aktualisieren von Eigenschaften**. Weitere Informationen zu SendGrid und zum Senden von E-Mails erhalten Sie im Abschnitt [Nächste Schritte](http://www.windowsazure.com/en-us/develop/nodejs/how-to-guides/blob-storage/#next-steps).

Inhaltsverzeichnis
------------------

-   [Was ist der SendGrid-E-Mail-Dienst?](#whatis)
-   [Erstellen eines SendGrid-Kontos](#createaccount)
-   [Erstellen eines Verweises auf das SendGrid Node.js-Modul](#reference)
-   [Vorgehensweise: Erstellen einer E-Mail](#createemail)
-   [Vorgehensweise: Senden einer E-Mail](#sendemail)
-   [Vorgehensweise: Hinzufügen eines Anhangs](#addattachment)
-   [Vorgehensweise: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen](#usefilters)
-   [Vorgehensweise: Aktualisieren von E-Mail-Eigenschaften](#updateproperties)
-   [Vorgehensweise: Verwenden zusätzlicher SendGrid-Dienste](#useservices)
-   [Nächste Schritte](#nextsteps)

Was ist der SendGrid-E-Mail-Dienst?
-----------------------------------

SendGrid ist ein [cloudbasierter E-Mail-Dienst](http://sendgrid.com/solutions), der zuverlässige [transaktionale E-Mail-Übermittlung](http://sendgrid.com/transactional-email), Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs bietet, die die benutzerdefinierte Integration erleichtern. Häufige Verwendungsszenarien für SendGrid:

-   Automatisches Versenden von Empfangsbestätigungen an Kunden
-   Verwalten von Verteilungslisten zum Senden von monatlichen e-Flyern und speziellen Angeboten an Kunden
-   Sammeln von Echtzeit-Kennzahlen für Sachverhalte wie gesperrte E-Mails oder Kundenreaktionsfähigkeit
-   Generieren von Berichten zur Identifizierung von Trends
-   Weiterleiten von Kundenanfragen
-   E-Mail-Benachrichtigungen von Ihrer Anwendung aus

Weitere Informationen finden Sie unter <http://sendgrid.com>.

Erstellen eines SendGrid-Kontos
-------------------------------

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

Erstellen eines Verweises auf das SendGrid-Modul für Node.js
------------------------------------------------------------

Das SendGrid-Modul für Node.js kann über den Node Package Manager (NPM) installiert werden. Verwenden Sie dafür den folgenden Befehl:

    npm install sendgrid

Nach der Installation können Sie das Modul mithilfe des folgenden Codes in Ihrer Anwendung erforderlich machen:

    var SendGrid = require('sendgrid')

Das SendGrid-Modul exportiert die Funktionen **SendGrid** und **Email**. **SendGrid** sendet E-Mails entweder über SMTP oder Web API, während **Email** E-Mail-Nachrichten einkapselt.

Vorgehensweise: Erstellen einer E-Mail
--------------------------------------

Wenn Sie eine E-Mail-Nachricht mithilfe des SendGrid-Moduls senden möchten, müssen Sie sie zuerst mit der Funktion "Email" erstellen und dann mit der Funktion "SendGrid" senden. Es folgt ein Beispiel für die Erstellung einer neuen Nachricht mithilfe der Funktion "Email":

    var mail = new SendGrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

Sie können auch eine HTML-Nachricht für Clients festlegen, bei denen dies durch Einstellung der HTML-Eigenschaft unterstützt wird. Beispiel:

    html: This is a sample <b>HTML<b> email message.

Wenn die Text- und die HTML-Option ausgewählt sind, wird die Nachricht auf Clients, die keine HTML-Nachrichten unterstützen, im Textformat angezeigt.

Weitere Informationen zu allen von der Funktion "Email" unterstützten Eigenschaften finden Sie unter [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs).

Vorgehensweise: Senden einer E-Mail
-----------------------------------

Nach der Erstellung einer E-Mail-Nachricht mithilfe der Funktion "Email" können sie diese entweder über SMTP oder über die von SendGrid bereitgestellte Web-API senden. Genaue Informationen zu den Vor- und Nachteilen jeder API finden Sie in der SendGrid-Dokumentation unter [SMTP vs. Web API](http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/).

Wenn Sie die SMTP-API oder die Web-API verwenden möchten, müssen Sie zuerst die Funktion "SendGrid" mithilfe des Benutzers und des Schlüssels Ihres SendGrid-Kontos initialisieren. Gehen Sie dazu folgendermaßen vor:

    var sender = new SendGrid.SendGrid('user','key');

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

**Hinweis**

Während die Beispiele oben die Übergabe eines E-Mail-Objekts und den Aufruf der Rückruf-Funktion zeigen, können Sie die Sende- und die SMTP-Funktion auch direkt durch Festlegung der E-Mail-Eigenschaften aufrufen. Beispiel:

``` {.prettyprint}
sender.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
```

Vorgehensweise: Hinzufügen eines Anhangs
----------------------------------------

Anhänge können zu einer Nachricht hinzugefügt werden, indem die entsprechenden Dateinamen und Pfade in der Eigenschaft **files** angegeben werden. Im folgenden Beispiel wird das Senden eines Anhangs demonstriert:

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

**Hinweis**

Wenn Sie die Eigenschaft **files** verwenden, muss die Datei über [fs.readFile](http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile) verfügbar sein. Wenn die Datei, die Sie hinzufügen möchten, in Azure Storage gehostet wird, z. B. in einem Blob-Container, müssen Sie sie zuerst an einen lokalen Speicherort oder auf ein Azure-Laufwerk kopieren, bevor sie mithilfe der Eigenschaft **files** als Anhang gesendet werden kann.

Vorgehensweise: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen
-----------------------------------------------------------------------------------------------------------

SendGrid bietet zusätzliche E-Mail-Funktionen durch die Verwendung von Filtern. Hierbei handelt es sich um Einstellungen, die zu einer E-Mail-Nachricht hinzugefügt werden können, um spezifische Funktionen wie etwa Aktivierung von Klickprotokollierung, Google Analytics, Abonnement-Tracking usw. zu ermöglichen. Eine vollständige Liste der verfügbaren Filter finden Sie unter [Filtereinstellungen](http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/).

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

Vorgehensweise: Aktualisieren von E-Mail-Eigenschaften
------------------------------------------------------

Einige E-Mail-Eigenschaften können mit **set*Eigenschaft*** überschrieben oder mit **add*Eigenschaft*** angefügt werden. Sie können z. B. zusätzliche Empfänger hinzufügen:

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

Weitere Informationen finden Sie unter [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs).

Vorgehensweise: Verwenden zusätzlicher SendGrid-Dienste
-------------------------------------------------------

SendGrid bietet webbasierte APIs, die Sie zur Nutzung zusätzlicher SendGrid-Funktionen aus Ihrer Azure-Anwendung einsetzen können. Ausführliche Informationen finden Sie in der [SendGrid-API-Dokumentation](http://docs.sendgrid.com/documentation/api/).

Nächste Schritte
----------------

Nachdem Sie nun mit den Grundlagen des E-Mail-Dienstes SendGrid vertraut sind, finden Sie unter diesen Links weitere Informationen.

-   Repository für das SendGrid Node.js-Modul: [sendgrid-nodejs](https://github.com/sendgrid/sendgrid-nodejs)
-   SendGrid-API-Dokumentation: &lt;http://docs.sendgrid.com/documentation/api/&gt;
-   Spezielles SendGrid-Angebot für Azure-Kunden: &lt;http://sendgrid.com/azure.html&gt;

