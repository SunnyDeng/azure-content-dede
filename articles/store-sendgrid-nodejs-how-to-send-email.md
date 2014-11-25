<properties linkid="dev-nodejs-how-to-sendgrid-email-service" urlDisplayName="SendGrid Email Service" pageTitle="How to use the SendGrid email service (Node.js) - Azure" metaKeywords="Azure SendGrid, Azure email service, Azure SendGrid Node.js, Azure email Node.js" description="Learn how send email with the SendGrid email service on Azure. Code samples written using the Node.js API." metaCanonical="" services="" documentationCenter="nodejs" title="How to Send Email Using SendGrid from Node.js" authors="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" solutions="" manager="wpickett" editor="" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="nodejs" ms.topic="article" ms.date="01/01/1900" ms.author="elmer.thomas@sendgrid.com; erika.berkland@sendgrid.com; vibhork" />

# Senden von E-Mails mit SendGrid aus Node.js

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben
mit dem E-Mail-Dienst SendGrid in Azure. Die Beispiele wurden unter Verwendung
der Node.js-API geschrieben. Die behandelten Szenarien umfassen das **Erstellen einer E-Mail**,
**Senden einer E-Mail**,
**Hinzufügen von Anhängen**,
**Verwenden von Filtern** und
**Aktualisieren von Eigenschaften**. Weitere Informationen zu SendGrid und zum Senden
von E-Mails erhalten Sie im Abschnitt [Nächste Schritte][Nächste Schritte].

## Inhaltsverzeichnis

-   [Was ist der SendGrid-E-Mail-Dienst?][Was ist der SendGrid-E-Mail-Dienst?]
-   [Erstellen eines SendGrid-Kontos][Erstellen eines SendGrid-Kontos]
-   [Erstellen eines Verweises auf das SendGrid Node.js-Modul][Erstellen eines Verweises auf das SendGrid Node.js-Modul]
-   [Gewusst wie: Erstellen einer E-Mail][Gewusst wie: Erstellen einer E-Mail]
-   [Gewusst wie: Senden einer E-Mail][Gewusst wie: Senden einer E-Mail]
-   [Gewusst wie: Hinzufügen eines Anhangs][Gewusst wie: Hinzufügen eines Anhangs]
-   [Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen][Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen]
-   [Gewusst wie: Aktualisieren von E-Mail-Eigenschaften][Gewusst wie: Aktualisieren von E-Mail-Eigenschaften]
-   [Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste][Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste]
-   [Nächste Schritte][1]

## <a name="whatis"> </a>Was ist der SendGrid-E-Mail-Dienst?

SendGrid ist ein [cloudbasierter E-Mail-Dienst][cloudbasierter E-Mail-Dienst], der zuverlässige
[transaktionale E-Mail-Übermittlung][transaktionale E-Mail-Übermittlung], Skalierbarkeit und Echtzeitanalysen mit flexiblen APIs
 bietet, die die benutzerdefinierte Integration erleichtern. Häufige Verwendungsszenarien für
SendGrid:

-   Automatisches Versenden von Empfangsbestätigungen an Kunden
-   Verwalten von Verteilungslisten zum Senden von monatlichen
    e-Flyern und speziellen Angeboten an Kunden
-   Sammeln von Echtzeit-Kennzahlen für Sachverhalte wie gesperrte E-Mails und
    Kundenreaktionszeit
-   Generieren von Berichten zur Identifizierung von Trends
-   Weiterleiten von Kundenanfragen
-   E-Mail-Benachrichtigungen von Ihrer Anwendung aus

Weitere Informationen finden Sie unter [][]<http://sendgrid.com></a>.

## <a name="createaccount"> </a>Erstellen eines SendGrid-Kontos

[WACOM.INCLUDE [sendgrid-sign-up](../includes/sendgrid-sign-up.md)]

## <a name="reference"> </a>Erstellen eines Verweises auf das SendGrid-Modul für Node.js

Das SendGrid-Modul für Node.js kann über den Node Package
Manager (NPM) installiert werden. Verwenden Sie dafür den folgenden Befehl:

    npm install sendgrid

Nach der Installation können Sie das Modul mithilfe des folgenden
Codes in Ihrer Anwendung erforderlich machen:

    var SendGrid = require('sendgrid')

Das SendGrid-Modul exportiert die **SendGrid**-Funktion und die **Email**-Funktion.
**SendGrid** sendet E-Mails entweder über SMTP oder Web
API, während **Email** E-Mails kapselt.

## <a name="createemail"> </a>Gewusst wie: Erstellen einer E-Mail

Wenn Sie eine E-Mail mithilfe des SendGrid-Moduls erstellen möchten,
müssen Sie sie zuerst mit der Email-Funktion erstellen und dann mit
der SendGrid-Funktion senden. Es folgt ein Beispiel für die Erstellung einer
neuen Nachricht mithilfe der Email-Funktion:

    var mail = new SendGrid.Email({
        to: 'john@contoso.com',
        from: 'anna@contoso.com',
        subject: 'test mail',
        text: 'This is a sample email message.'
    });

Sie können auch eine HTML-Nachricht für Clients festlegen, die diese
Funktion unterstützen, indem Sie die html-Eigenschaft festlegen. Beispiel:

    html: This is a sample <b>HTML<b> email message.

Wenn sowohl die text-Eigenschaft als auch die html-Eigenschaft festgelegt
ist und ein Client keine HTML-Nachrichten unterstützt, wird als Ausweichlösung auf Textinhalt zurückgegriffen.

Weitere Informationen zu allen von der Email-Funktion unterstützten Eigenschaften
finden Sie unter [sendgrid-nodejs][sendgrid-nodejs].

## <a name="sendemail"> </a>Gewusst wie: Senden einer E-Mail

Nach der Erstellung einer E-Mail mithilfe der Email-Funktion können sie
diese entweder über SMTP oder über die von SendGrid bereitgestellte Web-API senden. Ausführliche
Informationen zu den Vorteilen und Unterschieden jeder API finden Sie in der SendGrid-Dokumentation
unter [SMTP vs. Web API][SMTP vs. Web API].

Wenn Sie die SMTP-API oder die Web-API verwenden möchten, müssen
Sie zuerst die SendGrid-Funktion mithilfe des Benutzers und des Schlüssels
Ihres SendGrid-Kontos initialisieren. Gehen Sie dazu folgendermaßen vor:

    var sender = new SendGrid('user','key');

Die Nachricht kann nun über SMTP oder die Web-API gesendet werden. Die Aufrufe sind
praktisch identisch: Die E-Mail wird übergeben und eine optionale
Rückruffunktion ausgeführt. Der Rückruf wird verwendet,
um festzustellen, ob der Vorgang erfolgreich war oder nicht. Die folgenden Beispiele zeigen, wie Sie eine
Nachricht sowohl über SMTP als auch über die Web-API senden.

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
<p>W&auml;hrend die Beispiele oben die &Uuml;bergabe eines E-Mail-Objekts und
den Aufruf der R&uuml;ckruffunktion zeigen, k&ouml;nnen Sie die Sende- und SMTP-Funktion
auch direkt durch Festlegung der E-Mail-Eigenschaften aufrufen. Beispiel:</p>
<pre class="prettyprint">sender.send({
    to: 'john@contoso.com',
    from: 'anna@contoso.com',
    subject: 'test mail',
    text: 'This is a sample email message.'
});
</pre>
</div>

## <a name="addattachment"> </a>Gewusst wie: Hinzufügen eines Anhangs

Anhänge können einer Nachricht hinzugefügt werden, indem die Dateinamen
und Pfade in der **files**-Eigenschaft angegeben werden. Im folgenden Beispiel wird das
Senden eines Anhangs demonstriert:

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
<p>Wenn Sie die <strong>files</strong>-Eigenschaft verwenden, muss die Datei &uuml;ber
<a href="http://nodejs.org/docs/v0.6.7/api/fs.html#fs.readFile">fs.readFile</a> verf&uuml;gbar sein. Wenn die Datei, die Sie hinzuf&uuml;gen m&ouml;chten, in Azure Storage gehostet wird, z.&nbsp;B. in einem Blob-Container, m&uuml;ssen Sie sie zuerst an einen lokalen Speicherort oder auf ein Azure-Laufwerk kopieren, bevor sie mithilfe der Eigenschaft <strong>files</strong> als Anhang gesendet werden kann.</p>
</div>

## <a name="usefilters"> </a>Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen

SendGrid bietet zusätzliche E-Mail-Funktionen durch die Verwendung von
Filtern. Hierbei handelt es sich um Einstellungen, die zu einer E-Mail-Nachricht hinzugefügt werden können, um
spezifische Funktionen wie etwa Aktivierung von Klickprotokollierung, Google Analytics,
Abonnement-Tracking usw. zu ermöglichen. Eine vollständige Liste
der Filter finden Sie unter [Filtereinstellungen][Filtereinstellungen].

Filter können mithilfe der **filters**-Eigenschaft auf eine Nachricht angewendet werden.
Jeder Filter wird durch einen Hash mit filterspezifischen Einstellungen festgelegt.
Die folgenden Beispiele veranschaulichen die Filter für die Fußzeile, für die Klickprotokollierung und für
Twitter:

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

Einige E-Mail-Eigenschaften können mit **set*Eigenschaft*** überschrieben
oder mit **add*Eigenschaft*** angefügt werden. Sie können z. B. zusätzliche
Empfänger wie folgt hinzufügen:

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

Weitere Informationen finden Sie unter [sendgrid-nodejs][sendgrid-nodejs].

## <a name="useservices"> </a>Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste

SendGrid bietet webbasierte APIs, die Sie zur Nutzung zusätzlicher
SendGrid-Funktionen aus Ihrer Azure-Anwendung einsetzen können. Ausführliche
Informationen finden Sie in der [SendGrid-API-Dokumentation][SendGrid-API-Dokumentation].

## <a name="nextsteps"> </a>Nächste Schritte

Nachdem Sie nun mit den Grundlagen des E-Mail-Diensts SendGrid vertraut sind, finden Sie unter
diesen Links weitere Informationen.

-   Repository für das SendGrid Node.js-Modul: [sendgrid-nodejs][sendgrid-nodejs]
-   SendGrid-API-Dokumentation:
    <http://docs.sendgrid.com/documentation/api/>
-   Spezielles SendGrid-Angebot für Azure-Kunden:
    [][2]<http://sendgrid.com/azure.html></a>

  [Nächste Schritte]: http://www.windowsazure.com/de-de/develop/nodejs/how-to-guides/blob-storage/#next-steps
  [Erstellen eines SendGrid-Kontos]: #createaccount
  [Erstellen eines Verweises auf das SendGrid Node.js-Modul]: #reference
  [Gewusst wie: Erstellen einer E-Mail]: #createemail
  [Gewusst wie: Senden einer E-Mail]: #sendemail
  [Gewusst wie: Hinzufügen eines Anhangs]: #addattachment
  [Gewusst wie: Verwenden von Filtern zur Aktivierung von Fußzeilen sowie für Nachverfolgungen und Analysen]: #usefilters
  [Gewusst wie: Aktualisieren von E-Mail-Eigenschaften]: #updateproperties
  [Gewusst wie: Verwenden zusätzlicher SendGrid-Dienste]: #useservices
  [1]: #nextsteps
  [cloudbasierter E-Mail-Dienst]: http://sendgrid.com/solutions
  [transaktionale E-Mail-Übermittlung]: http://sendgrid.com/transactional-email
  []: http://sendgrid.com
  [sendgrid-nodejs]: https://github.com/sendgrid/sendgrid-nodejs
  [SMTP vs. Web API]: http://docs.sendgrid.com/documentation/get-started/integrate/examples/smtp-vs-rest/
  [Filtereinstellungen]: http://docs.sendgrid.com/documentation/api/smtp-api/filter-settings/
  [SendGrid-API-Dokumentation]: http://docs.sendgrid.com/documentation/api/
  [2]: http://sendgrid.com/azure.html
