<properties linkid="develop-ruby-how-to-twilio-sms-voice-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Ruby) - Azure" metaKeywords="Azure Ruby Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Ruby." metaCanonical="" services="" documentationCenter="Ruby" title="How to Use Twilio for Voice and SMS Capabilities in PHP" authors="" solutions="" manager="" editor="" />

Verwenden von Twilio für Telefonie- und SMS-Funktionen in Ruby
==============================================================

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem Twilio-API-Dienst in Azure. Die Szenarien behandeln das Tätigen eines Telefonanrufs und das Senden einer Kurznachricht (SMS). Weitere Informationen zu Twilio und zur Verwendung von Telefonie und SMS in Ihren Anwendungen finden Sie im Abschnitt [Nächste Schritte](#NextSteps).

Inhaltsverzeichnis
------------------

-   [Was ist Twilio?](#WhatIs)
-   [Twilio-Preise](#Pricing)
-   [Konzepte](#Concepts)
-   [Erstellen eines Twilio-Kontos](#CreateAccount)
-   [Erstellen einer Ruby Sinatra-Anwendung](#create_app)
-   [Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken](#configure_app)
-   [Gewusst wie: Tätigen von ausgehenden Anrufen](#howto_make_call)
-   [Gewusst wie: Empfangen einer SMS-Nachricht](#howto_recieve_sms)
-   [Gewusst wie: Zusätzliche Twilio-Dienste](#additional_services)
-   [Nächste Schritte](#NextSteps)

Was ist Twilio?
---------------

Twilio ist eine Webservice-API für Telefonie, mit deren Hilfe Sie Ihre existierenden Websprachen und -Fertigkeiten für die Erstellung von Telefonie- und SMS-Anwendungen nutzen können. Twilio ist ein externer Dienst (kein Feature von Azure und kein Microsoft-Produkt).

**Twilio Voice** ermöglicht Ihren Anwendungen das Tätigen und Entgegennehmen von Telefonanrufen. **Twilio SMS** ermöglicht Ihren Anwendungen das Senden und Empfangen von SMS-Nachrichten. **Twilio Client** ermöglicht Sprachkommunikation in Ihren Anwendungen über existierende Internetverbindungen, inklusive mobiler Verbindungen.

Twilio-Preise und -Sonderangebote
---------------------------------

Informationen über die Twilio-Preise finden Sie unter [Twilio Pricing](http://www.twilio.com/pricing). Azure-Kunden erhalten ein [Sonderangebot](http://ahoy.twilio.com/azure): ein Gratis-Guthaben von 1000 SMS oder 1000 eingehenden Sprachminuten. Besuchen Sie <http://ahoy.twilio.com/azure>, um sich für dieses Angebot zu registrieren und weitere Informationen zu erhalten.

Konzepte
--------

Die Twilio-API ist eine RESTful-API, die Telefonie- und SMS-Funktionen für Anwendungen bereitstellt. Clientbibliotheken stehen in mehreren Sprachen zur Verfügung; eine Liste finden Sie auf der Seite der [Twilio-API-Bibliotheken](https://www.twilio.com/docs/libraries).

### TwiML

TwiML ist ein Satz XML-basierter Anweisungen, die Twilio anweisen, wie ein Anruf oder eine SMS verarbeitet werden soll.

Die TwiML im folgenden Beispiel konvertiert den Text **Hello World** in Sprache.

    <?xml version="1.0" encoding="UTF-8"?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Das Stammelement aller TwiML-Dokumente ist `<Response>`. Von hier aus verwenden Sie Twilio-Verben, um das Verhalten Ihrer Anwendung festzulegen.

### TwiML-Verben

Twilio-Verben sind XML-Tags, die Twilio **Anweisungen** geben. Das Verb **&lt;Say\>** weist Twilio z. B. an, beim Anrufen eine hörbare Nachricht auszugeben.

Nachfolgend finden Sie eine Liste mit Twilio-Verben.

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

Nähere Informationen zu Twilio-Verben, ihren Attributen sowie TwiML finden Sie unter [TwiML](http://www.twilio.com/docs/api/twiml). Weitere Informationen zur Twilio-API finden Sie unter [Twilio-API](http://www.twilio.com/api).

Erstellen eines Twilio-Kontos
-----------------------------

Wenn Sie ein Twilio-Konto erstellen möchten, melden Sie sich bei [Try Twilio](https://www.twilio.com/try-twilio) an. Sie können mit einem kostenlosen Konto beginnen und später ein Upgrade vornehmen.

Wenn Sie sich an einem Twilio-Konto anmelden, erhalten Sie eine kostenlose Telefonnummer für Ihre Anwendung. Außerdem erhalten Sie eine Konto-SID und ein Authentifizierungstoken. Beide Angaben benötigen Sie zum Tätigen von Twilio-API-Anrufen. Bewahren Sie Ihr Authentifizierungstoken sicher auf, um unbefugten Zugriff auf Ihr Konto zu verhindern. Ihre Konto-SID und das Authentifizierungstoken können Sie auf der [Twilio-Kontoseite](https://www.twilio.com/user/account) in den Feldern **ACCOUNT SID** bzw. **AUTH TOKEN** einsehen.

### Überprüfen von Telefonnummern

Zusätzlich zu der Nummer, die Sie von Twilio erhalten, können Sie auch Nummern überprüfen, die Ihnen gehören (z. B. Ihre Mobilfunk- oder Festnetznummer), um sie in Ihren Anwendungen zu verwenden.

Informationen zum Überprüfen von Telefonnummern finden Sie unter [Manage Numbers](https://www.twilio.com/user/account/phone-numbers/verified#).

Erstellen einer Ruby-Anwendung
------------------------------

Ruby-Anwendungen, die den Twilio-Dienst verwenden und in Azure ausgeführt werden, unterscheiden sich nicht von anderen Ruby-Anwendungen, die den Twilio-Dienst verwenden. Twilio-Dienste sind RESTful-basiert und können in Ruby auf verschiedene Arten aufgerufen werden. Dieser Artikel konzentriert sich auf die Verwendung von Twilio mit der [Twilio-Bibliothek für Ruby](https://www.twilio.com/docs/ruby/install).

Richten SIe zuerst einen [neuen virtuellen Linux-Computer auf Azure](http://www.windowsazure.com/en-us/develop/ruby/tutorials/web-app-with-linux-vm/) ein, der als Host für Ihre neue Ruby-Webanwendung fungiert. Ignorieren Sie die Schritte zur Erstellung einer Rails-App, und erstellen Sie nur den virtuellen Computer. Erstellen Sie auch unbedingt einen Endpunkt mit dem externen Port 80 und dem internen Port 5000.

Im Beispiel unten verwenden wir [Sinatra](http://www.sinatrarb.com/), ein sehr einfaches Web-Framework für Ruby. Sie können die Twilio-Hilfsbibliothek für Ruby aber auch mit jedem anderen Web-Framework verwenden, z. B. Ruby on Rails.

Stellen Sie eine SSH-Verbindung zum neuen virtuellen Computer her, und erstellen Sie ein Verzeichnis für Ihre neue App. Erstellen Sie in diesem Verzeichnis eine Datei namens Gemfile, und kopieren Sie den folgenden Code in diese hinein:

    source 'https://rubygems.org'
    gem 'sinatra'
    gem 'thin'

Führen Sie in der Befehlszeile `bundle install` aus. Dadurch werden die oben genannten Abhängigkeiten installiert. Erstellen Sie dann eine Datei namens `web.rb`. In dieser Datei wird sich der Code für Ihre Webanwendung befinden. Fügen Sie den folgenden Code in diese Datei ein:

    require 'sinatra'

    get '/' do
        "Hello Monkey!"
    end

An diesem Punkt sollten Sie den Befehl `ruby web.rb -p 5000` ausführen können. Dadurch wird ein kleiner Webserver auf Port 5000 hochgefahren. Sie sollten diese App nun in Ihrem Browser suchen können, indem Sie die URL eingeben, die Sie für Ihren virtuellen Azure-Computer eingerichtet haben, Wenn Sie Ihre Webanwendung im Browser erreichen können, sind Sie bereit, mit der Erstellung einer Twilio-App zu beginnen.

Konfigurieren einer Anwendung für die Verwendung von Twilio
-----------------------------------------------------------

Sie können Ihre Webanwendung für eine Verwendung der Twilio-Bibliothek konfigurieren, indem Sie in Ihre Datei `Gemfile` die folgende Zeile einfügen:

    gem 'twilio-ruby'

Führen Sie in der Befehlszeile `bundle install` aus. Öffnen Sie nun `web.rb`, und fügen Sie am Anfang folgende Zeile ein:

    require 'twilio-ruby'

Nun sind Sie bereit, die Twilio-Hilfsbibliothek für Ruby in Ihrer Webanwendung zu verwenden.

Gewusst wie: Tätigen von ausgehenden Anrufen
--------------------------------------------

Im Folgenden ist dargestellt, wie Sie einen ausgehenden Anruf tätigen. Zu den wichtigen Konzepten gehören die Verwendung der Twilio-Hilfsbibliothek für Ruby zur Ausführung von REST API-Anrufen und zum Rendering von TwiML. Geben Sie Ihre Werte für die Telefonnummern **From** und **To** ein, und stellen Sie sicher, dass Sie die **From**-Telefonnummer für Ihr Twilio-Konto überprüfen, bevor Sie den Code ausführen.

Fügen Sie diese Funktion in die Datei `web.md` ein:

    # Konto-ID und Authentifizierungstoken festlegen.
    sid = "your_twilio_account_sid";
    token = "your_twilio_authentication_token";

    # Die Nummer des Telefons, das den Ruf initiiert.
    # Dies sollte entweder eine Twilio-Nummer oder eine von Ihnen geprüfte Nummer sein
    from = "NNNNNNNNNNN";

    # Die Nummer des Telefons, das den Ruf empfängt.
    to = "NNNNNNNNNNN";

    # Die von Twilio bereitgestellte Website für die TwiML-Antwort verwenden.
    url = "http://yourdomain.cloudapp.net/voice_url";
      
    get '/make_call' do
      # Den Anrufclient erstellen.
      client = Twilio::REST::Client.create(sid, token);
      
      # Den Anruf ausführen
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end

Wenn Sie `http://yourdomain.cloudapp.net/make_call` in einem Browser öffnen, wird der Anruf an die Twilio-API ausgelöst, die den Telefonanruf tätigen soll. Die ersten beiden Parameter in `client.account.calls.create` erklären sich praktisch selbst: Es handelt sich um die anrufende Nummer (`from`) und die empfangende Nummer (`to`).

Der dritte Parameter (`url`) ist die URL, die Twilio anfragt, um Anweisungen darüber zu erhalten, was nach der Herstellung der Rufverbindung zu tun ist. In diesem Fall richten wir eine URL ein (`http://yourdomain.cloudapp.net`), die ein einfaches TwiML-Dokument zurückgibt und das Verb `<Say>` verwendet, um Text in Sprache umzuwandeln und zur angerufenen Person "Hello Monkey" zu sagen.

Gewusst wie: Empfangen einer SMS-Nachricht
------------------------------------------

Im vorhergehenden Beispiel haben wir einen **ausgehenden** Telefonanruf initiiert. Lassen Sie uns nun die Telefonnummer verwenden, die wir bei der Anmeldung von Twilio erhalten haben, um eine **eingehende** SMS-Nachricht zu verarbeiten.

Melden Sie sich zuerst an Ihrem [Twilio-Dashboard](https://www.twilio.com/user/account) an. Klicken Sie auf der oberen Navigationsleiste auf "Nummern" und anschließend auf die Twilio-Nummer, die Sie erhalten haben. Sie sehen zwei URLs, die Sie konfigurieren können: eine Sprachanfragen-URL und eine SMS-Anfragen-URL. Dies sind die URLs, die Twilio anruft, wenn ein Telefonanruf an Ihre Nummer erfolgt oder eine SMS-Nachricht an Ihre Nummer gesendet wird. Die URLs sind auch als "Webhooks" bekannt.

Lassen Sie uns nun die URL in `http://yourdomain.cloudapp.net/sms_url` aktualisieren, um eingehende SMS-Nachrichten verarbeiten zu können. Klicken Sie anschließend unten auf der Seite auf "Änderungen speichern". Gehen Sie dann zurück zu `web.rb`, und programmieren Sie die Anwendung entsprechend:

    post '/sms_url' do
      "<Response>
         <Sms>Hey, thanks for the ping! Twilio and Azure rock!</Sms>
       </Response>"
    end

Starten Sie nach der Durchführung dieser Änderung Ihre Webanwendung unbedingt neu. Nehmen Sie nun Ihr Telefon zur Hand, und senden Sie eine SMS an Ihre Twilio-Nummer. Sie sollten direkt eine SMS-Antwort mit dem Wortlaut "Hey, thanks for the ping! Twilio and Azure rock!" erhalten.

Gewusst wie: Verwenden zusätzlicher Twilio-Dienste
--------------------------------------------------

Zusätzlich zu den hier gezeigten Beispielen bietet Twilio webbasierte APIs, mit denen Sie zusätzliche Twilio-Funktionen in Ihrer Azure-Anwendung nutzen können. Ausführliche Informationen finden Sie in der [Twilio API-Dokumentation](http://www.twilio.com/api).

### Nächste Schritte

Nachdem Sie nun mit den Grundlagen des Twilio-Dienstes vertraut sind, finden Sie unter diesen Links weitere Informationen:

-   [Twilio Security Guidelines](http://www.twilio.com/docs/security)
-   [Twilio HowTos and Example Code](http://www.twilio.com/docs/howto)
-   [Twilio Quickstart Tutorials](http://www.twilio.com/docs/quickstart)
-   [Twilio on GitHub](https://github.com/twilio)
-   [Talk to Twilio Support](http://www.twilio.com/help/contact)

