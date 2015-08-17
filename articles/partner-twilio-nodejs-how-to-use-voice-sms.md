<properties 
	pageTitle="Verwenden von Twilio für Sprach-, VoIP- und SMS-Nachrichten in Azure" 
	description="Erfahren Sie, wie Sie mit dem Twilio API-Dienst in Azure einen Telefonanruf tätigen und eine SMS-Nachricht senden. Die Codebeispiele wurden in Node.js geschrieben." 
	services="" 
	documentationCenter="nodejs" 
	authors="MikeWasson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="nodejs" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="mwasson"/>


# Verwenden von Twilio für Sprach-, VoIP- und SMS-Nachrichten in Azure

Diese Anleitung zeigt, wie Sie Apps erstellen, die in Azure mit Twilio und node.js kommunizieren.

<a id="whatis"/>
## Was ist Twilio?

Twilio ist eine API-Plattform, die Entwicklern das Ausführen und Annehmen von Telefonanrufen, das Senden und Empfangen von Textnachrichten und das Einbetten von VoIP-Nachrichten in browserbasierte und native mobile Anwendungen erleichtert. Betrachten wir zunächst kurz, wie dies funktioniert, bevor wir tiefer in die Materie eintauchen.

### Empfangen von Rufen und Textnachrichten

Twilio erlaubt Entwicklern den [Kauf programmierbarer Telefonnummern][purchase_phone], die für das Senden und Empfangen von Rufen und Textnachrichten verwendet werden können. Wenn eine Twilio-Nummer einen eingehenden Anruf oder Text erhält, sendet Twilio eine HTTP POST- oder GET-Anfrage an Ihre Webanwendung, in der nach Anweisungen zur Verarbeitung des Rufs oder des Texts gefragt wird. Ihr Server beantwortet die HTTP-Anfrage von Twilio mit [TwiML][twiml], einem einfachen Satz von XML-Tags, die Anweisungen zur Verarbeitung eines Rufs oder eines Texts enthalten. In Kürze sehen wir einige Beispiele für TwiML.

### Ausführen von Rufen und Senden von Textnachrichten

Durch HTTP-Anfragen an die Twilio-Webdienst-API können Entwickler Textnachrichten senden oder ausgehende Telefonanrufe initiieren. Für ausgehende Anrufe muss der Entwickler eine URL angeben, die TwiML-Anweisungen zur Verarbeitung des ausgehenden Rufs zurückgibt, sobald die Rufverbindung hergestellt ist.

### Einbetten von VoIP-Funktionen in UI-Code (JavaScript iOS oder Android)

Twilio bietet ein clientseitiges SDK, das aus jedem Desktop-Webbrowser, jeder iOS-App oder jeder Android-App ein VoIP-Telefon machen kann. In diesem Artikel konzentrieren wir uns darauf, wie VoIP-Anrufe im Browser verwendet werden. Zusätzlich zum Twilio-JavaScript-SDK, das in Ihrem Browser läuft, muss eine serverseitige Anwendung (unsere node.js-Anwendung) verwendet werden, um ein "'Capability-Token" an den JavaScript-Client abzugeben. Mehr Informationen zur Verwendung von VoIP mit node.js erhalten Sie im [Twilio-Entwickler-Blog][voipnode].

<a id="signup"/>
## Anmelden bei Twilio (Microsoft-Rabatt)

Vor der Verwendung von Twilio-Diensten müssen Sie zuerst [ein Konto anmelden][signup]. Microsoft Azure-Kunden erhalten einen speziellen Rabatt - [registrieren Sie sich hier][signup]!

<a id="azuresite"/>
## Erstellen und Bereitstellen einer node.js-Azure-Website

Als Nächstes müssen Sie eine node.js-Website erstellen, die auf Azure läuft. [Die offizielle Dokumentation dafür finden Sie hier][azure_new_site]. Folgende Schritte sind notwendig:

* Anmelden eines Azure-Kontos, wenn Sie nicht bereits über eines verfügen.
* Erstellen einer neuen Website mithilfe der Azure-Konsole
* Hinzufügen einer Quellcodeverwaltungsunterstützung (dafür wird die Verwendung von git vorausgesetzt)
* Erstellen einer Datei namens `server.js` mit einer einfachen node.js-Webanwendung
* Bereitstellung dieser einfachen Anwendung in Azure

<a id="twiliomodule"/>
## Konfigurieren des Twilio-Moduls

Als Nächstes beginnen wir mit dem Schreiben einer einfachen node-js-Anwendung, die die Twilio-API nutzt. Bevor wir beginnen, müssen wir die Anmeldeinformationen für unser Twilio-Konto konfigurieren.

### Konfigurieren von Twilio-Anmeldeinformationen in Systemumgebungsvariablen

Um authentische Anfragen am Twilio-Back-End auszuführen, benötigen wir die Konto-SID und das Authentifizierungstoken, die als Benutzername und Kennwort für unser Twilio-Konto fungieren. Der sicherste Weg, diese zur Verwendung mit dem Knotenmodul in Azure zu konfigurieren, sind Systemumgebungsvariablen, die Sie direkt in der Azure-Verwaltungskonsole einstellen können.

Wählen Sie Ihre node.js-Website, und klicken Sie auf den Link "CONFIGURE". Wenn Sie ein wenig herunterscrollen, sehen Sie einen Bereich, in dem Sie Konfigurationseigenschaften für Ihre Anwendung festlegen können. Geben Sie Ihre Twilio-Anmeldeinformationen ([die Sie in Ihrem Twilio-Dashboard finden][twilio_dashboard]) wie gezeigt ein - geben Sie ihnen die Namen "TWILIO\_ACCOUNT\_SID" bzw. "TWILIO\_AUTH\_TOKEN":

![Azure-Verwaltungskonsole][azure-admin-console]

Wenn Sie diese Variablen konfiguriert haben, starten Sie Ihre Anwendung in der Azure-Konsole neu.

### Deklarieren des Twilio-Moduls in package.json

Als Nächstes müssen wir eine Datei namens package.json erstellen, um unsere Knotenmodulabhängigkeiten über [npm] zu verwalten. Legen Sie auf derselben Ebene wie die Datei "server.js", die Sie im Lernprogramm für node.js in Azure erstellt haben, eine Datei namens "package.json" an. Platzieren Sie in dieser Datei den folgenden Code:

  { "name": "application-name", "version": "0.0.1", "private": true, "scripts": { "start": "node server" }, "dependencies": { "express": "3.1.0", "ejs": "*", "twilio":"*" } }

Damit wird das Twilio-Modul ebenso wie das beliebte [Web-Framework Express][express] und die EJS Template Engine als Abhängigkeit deklariert. So - nun wissen wir genug, um einen Code zu schreiben.

<a id="makecall"/>
## Ausführen eines ausgehenden Rufs

Erstellen wir nun ein einfaches Formular zur Platzierung eines Anrufs an eine von uns gewählte Nummer. Öffnen Sie server.js, und geben Sie den folgenden Code ein. Bitte beachten Sie: "CHANGE\_ME" muss durch den Namen Ihrer Azure-Website ersetzt werden:

    // Module dependencies
    var express = require('express'), 
      path = require('path'), 
      http = require('http'), 
      twilio = require('twilio');

    // Create Express web application
    var app = express();

    // Express configuration
    app.configure(function(){
      app.set('port', process.env.PORT || 3000);
      app.set('views', __dirname + '/views');
      app.set('view engine', 'ejs');
      app.use(express.favicon());
      app.use(express.logger('dev'));
      app.use(express.bodyParser());
      app.use(express.methodOverride());
      app.use(app.router);
      app.use(express.static(path.join(__dirname, 'public')));
    });
    app.configure('development', function(){
      app.use(express.errorHandler());
    });

    // Render an HTML user interface for the application's home page
    app.get('/', function(request, response) {
      response.render('index');
    });

    // Handle the form POST to place a call
    app.post('/call', function(request, response) {
      var client = twilio();
      client.makeCall({
          // make a call to this number
          to:request.body.number,

          // Change to a Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // A URL in our app which generates TwiML
          // Change "CHANGE_ME" to your app's name
          url:'https://CHANGE_ME.azurewebsites.net/outbound_call'
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

    // Generate TwiML to handle an outbound call
    app.post('/outbound_call', function(request, response) {
      var twiml = new twilio.TwimlResponse();

      // Say a message to the call's receiver 
      twiml.say('hello - thanks for checking out Twilio and Azure', {
          voice:'woman'
      });

      response.set('Content-Type', 'text/xml');
      response.send(twiml.toString());
    });

    // Start server
    http.createServer(app).listen(app.get('port'), function(){
      console.log("Express server listening on port " + app.get('port'));
    });

Erstellen Sie als Nächstes ein Verzeichnis namens "views", und erstellen Sie in diesem Verzeichnis eine Datei namens "index.ejs" mit dem folgenden Inhalt:

    <!DOCTYPE html>
    <html>
    <head>
      <title>Twilio Test</title>
      <style>
        input { height:20px; width:300px; font-size:18px; margin:5px; padding:5px; }
      </style>
    </head>
    <body>
      <h1>Twilio Test</h1>
      <form action="/call" method="POST">
          <input placeholder="Enter a phone number" name="number"/>
          <br/>
          <input type="submit" value="Call the number above"/>
      </form>
    </body>
    </html>

Stellen Sie Ihre Website nun in Azure bereit, und öffnen Sie Ihre Startseite. Sie sollten nun Ihre Telefonnummer in das Textfeld eingeben können und einen Anruf von Ihrer Twilio-Nummer erhalten!

<a id="sendmessage"/>
## Senden einer SMS-Nachricht

Lassen Sie uns nun eine Benutzerschnittstelle einrichten und eine Anwendungslogik zum Senden einer Textnachricht formulieren. Öffnen Sie „server.js“, und geben Sie nach dem letzten Anruf an „app.post“ den folgenden Code ein:

    app.post('/sms', function(request, response) {
      var client = twilio();
      client.sendSms({
          // send a text to this number
          to:request.body.number,

          // A Twilio number you bought - see:
          // https://www.twilio.com/user/account/phone-numbers/incoming
          from:'+15558675309',

          // The body of the text message
          body: request.body.message
          
      }, function(error, data) {
          // Go back to the home page
          response.redirect('/');
      });
    });

Geben Sie unter „views/index.ejs“ ein weiteres Formular unter dem ersten ein, um eine Nummer und eine Textnachricht zu übermitteln:

    <form action="/sms" method="POST">
      <input placeholder="Enter a phone number" name="number"/>
      <br/>
      <input placeholder="Enter a message to send" name="message"/>
      <br/>
      <input type="submit" value="Send text to the number above"/>
    </form>

Stellen Sie Ihre Anwendung erneut auf Azure bereit. Nun sollten Sie dieses Formular übermitteln können und eine Textnachricht an sich selbst (oder einen Ihrer Freunde) senden können.

<a id="nextsteps"/>
## Nächste Schritte

Sie haben nun die Grundlagen der Verwendung von node.js und Twilio zum Aufbauen von Kommunikations-Apps kennengelernt. Aber diese Beispiele kratzen nur an der Oberfläche dessen, was mit Twilio und node.js möglich ist. Weitere Informationen zur Verwendung von Twilio mit node.js finden Sie in folgenden Quellen:

* [Offizielle Modul-Dokumentationen][docs]
* [Lernprogramm zu VoIP mit node.js-Anwendungen][voipnode]
* [Votr – eine Echtzeit-SMS-Wählanwendung mit node.js und CouchDB (dreiteilig)][votr]
* [Paarprogrammierung im Browser mit node.js][pair]

Wir hoffen, dass Sie Freude an node.js und Twilio in Azure haben!

[purchase_phone]: https://www.twilio.com/user/account/phone-numbers/available/local
[twiml]: https://www.twilio.com/docs/api/twiml
[signup]: http://ahoy.twilio.com/azure
[azure_new_site]: http://www.windowsazure.com/develop/nodejs/tutorials/create-a-website-(mac)/
[twilio_dashboard]: https://www.twilio.com/user/account
[npm]: http://npmjs.org
[express]: http://expressjs.com
[voipnode]: http://www.twilio.com/blog/2013/04/introduction-to-twilio-client-with-node-js.html
[docs]: http://twilio.github.io/twilio-node/
[votr]: http://www.twilio.com/blog/2012/09/building-a-real-time-sms-voting-app-part-1-node-js-couchdb.html
[pair]: http://www.twilio.com/blog/2013/06/pair-programming-in-the-browser-with-twilio.html
[azure-admin-console]: ./media/partner-twilio-nodejs-how-to-use-voice-sms/twilio_1.png

<!---HONumber=August15_HO6-->