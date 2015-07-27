<properties 
	pageTitle="Verwenden von Twilio für Telefonie- und SMS-Funktionen (Ruby) - Azure" 
	description="Erfahren Sie, wie Sie mit dem Twilio API-Dienst in Azure einen Telefonanruf tätigen und eine SMS-Nachricht senden. Die Codebeispiele wurden in Ruby geschrieben." 
	services="" 
	documentationCenter="ruby" 
	authors="devinrader" 
	manager="twilio" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="MicrosoftHelp@twilio.com"/>





# Verwenden von Twilio für Telefonie- und SMS-Funktionen in Ruby
Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem Twilio-API-Dienst in Azure. Die Szenarien behandeln das Tätigen eines Telefonanrufs und das Senden einer Kurznachricht (SMS). Weitere Informationen zu Twilio und zur Verwendung von Telefonie und SMS in Ihren Anwendungen finden Sie im Abschnitt [Nächste Schritte](#NextSteps).

## <a id="WhatIs"></a>Was ist Twilio?
Twilio ist eine Webservice-API für Telefonie, mit deren Hilfe Sie Ihre existierenden Websprachen und -Fertigkeiten für die Erstellung von Telefonie- und SMS-Anwendungen nutzen können. Twilio ist ein externer Dienst (kein Feature von Azure und kein Microsoft-Produkt).

**Twilio Voice** ermöglicht Anwendungen das Tätigen und Entgegennehmen von Telefonanrufen. **Twilio SMS** ermöglicht Ihren Anwendungen das Senden und Empfangen von SMS-Nachrichten. **Twilio Client** ermöglicht Sprachkommunikation in Ihren Anwendungen über existierende Internetverbindungen, inklusive mobiler Verbindungen.

## <a id="Pricing"></a>Twilio-Preise und -Sonderangebote
Informationen über die Twilio-Preise finden Sie unter [Twilio Pricing][twilio_pricing]. Azure-Kunden erhalten ein [Sonderangebot][special_offer]: ein Gratis-Guthaben von 1000 SMS oder 1000 eingehenden Sprachminuten. Besuchen Sie [http://ahoy.twilio.com/azure][special_offer], um sich für dieses Angebot zu registrieren und weitere Informationen zu erhalten.

## <a id="Concepts"></a>Konzepte
Die Twilio-API ist eine RESTful-API, die Telefonie- und SMS-Funktionen für Anwendungen bereitstellt. Clientbibliotheken stehen in mehreren Sprachen zur Verfügung; eine Liste finden Sie auf der Seite der [Twilio-API-Bibliotheken][twilio_libraries].

### <a id="TwiML"></a>TwiML
TwiML ist ein Satz XML-basierter Anweisungen, die Twilio anweisen, wie ein Anruf oder eine SMS verarbeitet werden soll.

Die TwiML im folgenden Beispiel konvertiert den Text **Hello World** in Sprache.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Das Stammelement aller TwiML-Dokumente ist `<Response>`. Von hier aus verwenden Sie Twilio-Verben, um das Verhalten Ihrer Anwendung festzulegen.

### <a id="Verbs"></a>TwiML-Verben
Twilio-Verben sind XML-Tags, die Twilio **Anweisungen** geben. Das Verb **&lt;Say&gt;** weist Twilio z. B. an, eine Nachricht in einem Anruf akustisch zu übermitteln.

Nachfolgend finden Sie eine Liste mit Twilio-Verben.

* **&lt;Dial&gt;**: verbindet den Anrufer mit einem anderen Telefon.
* **&lt;Gather&gt;**: erfasst Ziffern, die über die Telefontasten eingegeben werden.
* **&lt;Hangup&gt;**: beendet einen Anruf.
* **&lt;Play&gt;**: gibt eine Audiodatei wieder.
* **&lt;Pause&gt;**: wartet ohne Ton für eine angegebene Anzahl von Sekunden.
* **&lt;Record&gt;**: zeichnet die Stimme des Anrufers auf und gibt eine URL einer Datei mit der Aufzeichnung zurück.
* **&lt;Redirect&gt;**: überträgt die Steuerung eines Anrufs oder einer SMS an die TwiML an einer anderen URL.
* **&lt;Reject&gt;**: lehnt einen bei Ihrer Twilio-Nummer eingehenden Anruf ab, ohne dass Ihnen Kosten entstehen.
* **&lt;Say&gt;**: konvertiert Text in Sprache für einen Anruf.
* **&lt;Sms&gt;**: sendet eine SMS-Nachricht.

Nähere Informationen zu Twilio-Verben, ihren Attributen sowie TwiML finden Sie unter [TwiML][twiml]. Weitere Informationen zur Twilio-API finden Sie unter [Twilio-API][twilio_api].

## <a id="CreateAccount"></a>Erstellen von Twilio-Konten
Wenn Sie ein Twilio-Konto erstellen möchten, melden Sie sich bei [Try Twilio][try_twilio] an. Sie können mit einem kostenlosen Konto beginnen und später ein Upgrade vornehmen.

Wenn Sie sich an einem Twilio-Konto anmelden, erhalten Sie eine kostenlose Telefonnummer für Ihre Anwendung. Außerdem erhalten Sie eine Konto-SID und ein Authentifizierungstoken. Beide Angaben benötigen Sie zum Tätigen von Twilio-API-Anrufen. Bewahren Sie Ihr Authentifizierungstoken sicher auf, um unbefugten Zugriff auf Ihr Konto zu verhindern. Ihre Konto-SID und das Authentifizierungstoken können Sie auf der [Twilio-Kontoseite][twilio_account] in den Feldern **ACCOUNT SID** bzw. **AUTH TOKEN** einsehen.

### <a id="VerifyPhoneNumbers"></a>Überprüfen von Telefonnummern
Zusätzlich zu der Nummer, die Sie von Twilio erhalten, können Sie auch Nummern überprüfen, die Ihnen gehören (z. B. Ihre Mobilfunk- oder Festnetznummer), um sie in Ihren Anwendungen zu verwenden.

Informationen zum Überprüfen von Telefonnummern finden Sie unter [Manage Numbers][verify_phone].

## <a id="create_app"></a>Erstellen einer Ruby-Anwendung
Ruby-Anwendungen, die den Twilio-Dienst verwenden und in Azure ausgeführt werden, unterscheiden sich nicht von anderen Ruby-Anwendungen, die den Twilio-Dienst verwenden. Twilio-Dienste sind RESTful-basiert und können in Ruby auf verschiedene Arten aufgerufen werden. Dieser Artikel konzentriert sich auf die Verwendung von Twilio mit der [Twilio-Bibliothek für Ruby][twilio_ruby].

Richten SIe zuerst einen [neuen virtuellen Linux-Computer auf Azure][azure_vm_setup] ein, der als Host für Ihre neue Ruby-Webanwendung fungiert. Ignorieren Sie die Schritte zur Erstellung einer Rails-App, und erstellen Sie nur den virtuellen Computer. Erstellen Sie auch unbedingt einen Endpunkt mit dem externen Port 80 und dem internen Port 5000.

Im Beispiel unten verwenden wir [Sinatra][sinatra], ein sehr einfaches Web-Framework für Ruby. Sie können die Twilio-Hilfsbibliothek für Ruby aber auch mit jedem anderen Web-Framework verwenden, z. B. Ruby on Rails.

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

## <a id="configure_app"></a>Konfigurieren einer Anwendung für die Verwendung von Twilio
Sie können die Web-App zur Verwendung der Twilio-Bibliothek konfigurieren, indem Sie in der `Gemfile` die folgende Zeile einfügen:

    gem 'twilio-ruby'

Führen Sie in der Befehlszeile `bundle install` aus. Öffnen Sie nun `web.rb`, und fügen Sie am Anfang folgende Zeile ein:

    require 'twilio-ruby'

Nun sind Sie bereit, die Twilio-Hilfsbibliothek für Ruby in Ihrer Webanwendung zu verwenden.

## <a id="howto_make_call"></a>Tätigen von ausgehenden Anrufen
Im Folgenden ist dargestellt, wie Sie einen ausgehenden Anruf tätigen. Zu den wichtigen Konzepten gehören die Verwendung der Twilio-Hilfsbibliothek für Ruby zur Ausführung von REST API-Anrufen und zum Rendering von TwiML. Geben Sie Ihre Werte für die Telefonnummern **From** und **To** ein, und stellen Sie sicher, dass Sie die **From**-Telefonnummer für Ihr Twilio-Konto verifizieren, bevor Sie den Code ausführen.

Fügen Sie diese Funktion in die Datei `web.md` ein:

    # Set your account ID and authentication token.
	sid = "your_twilio_account_sid";
	token = "your_twilio_authentication_token";

	# The number of the phone initiating the the call.
    # This should either be a Twilio number or a number that you've verified
	from = "NNNNNNNNNNN";

	# The number of the phone receiving call.
	to = "NNNNNNNNNNN";

	# Use the Twilio-provided site for the TwiML response.
    url = "http://yourdomain.cloudapp.net/voice_url";
      
    get '/make_call' do
	  # Create the call client.
	  client = Twilio::REST::Client.new(sid, token);
      
      # Make the call
      client.account.calls.create(to: to, from: from, url: url)
    end

    post '/voice_url' do
      "<Response>
         <Say>Hello Monkey!</Say>
       </Response>"
    end
    
Wenn Sie `http://yourdomain.cloudapp.net/make_call` in einem Browser öffnen, wird der Aufruf der Twilio-API ausgelöst, die den Telefonanruf tätigen soll. Die ersten beiden Parameter in `client.account.calls.create` sind nahezu selbsterklärend: die Telefonnummer für den ausgehenden Anruf (`from`) und die Telefonnummer des Anrufempfängers (`to`).

Der dritte Parameter (`url`) ist die URL, die Twilio anfordert, um Anweisungen darüber zu erhalten, was nach Herstellung der Rufverbindung zu tun ist. In diesem Fall wird eine URL (`http://yourdomain.cloudapp.net`) eingerichtet, die ein einfaches TwiML-Dokument zurückgibt und das Verb `<Say>` verwendet, um Text in Sprache umzuwandeln und zur angerufenen Person „Hello Monkey“ zu sagen.

## <a id="howto_recieve_sms"></a>Empfangen einer SMS-Nachricht
Im vorhergehenden Beispiel haben wir einen **ausgehenden** Telefonanruf initiiert. Lassen Sie uns nun die Telefonnummer verwenden, die wir bei der Anmeldung von Twilio erhalten haben, um eine **eingehende** SMS-Nachricht zu verarbeiten.

Melden Sie sich zuerst an Ihrem [Twilio-Dashboard][twilio_account] an. Klicken Sie auf der oberen Navigationsleiste auf "Nummern" und anschließend auf die Twilio-Nummer, die Sie erhalten haben. Sie sehen zwei URLs, die Sie konfigurieren können: eine Sprachanfragen-URL und eine SMS-Anfragen-URL. Dies sind die URLs, die Twilio anruft, wenn ein Telefonanruf an Ihre Nummer erfolgt oder eine SMS-Nachricht an Ihre Nummer gesendet wird. Die URLs sind auch als "Webhooks" bekannt.

Ändern Sie nun die URL in `http://yourdomain.cloudapp.net/sms_url`, um eingehende SMS-Nachrichten verarbeiten zu können. Klicken Sie anschließend unten auf der Seite auf "Änderungen speichern". Gehen Sie dann zurück zu `web.rb`, und programmieren Sie die Anwendung entsprechend:

    post '/sms_url' do
      "<Response>
         <Message>Hey, thanks for the ping! Twilio and Azure rock!</Message>
       </Response>"
    end

Starten Sie nach der Durchführung dieser Änderung Ihre Webanwendung unbedingt neu. Nehmen Sie nun Ihr Telefon zur Hand, und senden Sie eine SMS an Ihre Twilio-Nummer. Sie sollten direkt eine SMS-Antwort mit dem Wortlaut "Hey, thanks for the ping! Twilio and Azure rock!" erhalten.

## <a id="additional_services"></a>Verwenden zusätzlicher Twilio-Dienste
Zusätzlich zu den hier gezeigten Beispielen bietet Twilio webbasierte APIs, mit denen Sie zusätzliche Twilio-Funktionen in Ihrer Azure-Anwendung verwenden können. Ausführliche Informationen finden Sie in der [Twilio API-Dokumentation][twilio_api_documentation].

### <a id="NextSteps"></a>Nächste Schritte
Nachdem Sie nun mit den Grundlagen des Twilio-Dienstes vertraut sind, finden Sie unter diesen Links weitere Informationen:

* [Twilio Security Guidelines][twilio_security_guidelines] (Twilio-Sicherheitsrichtlinien, in englischer Sprache)
* [Twilio HowTos and Example Code][twilio_howtos] (Twilio-Anleitungen und Beispielcode, in englischer Sprache)
* [Twilio Quickstart Tutorials][twilio_quickstarts] (Twilio-Schnellstart-Lernprogramme, in englischer Sprache) 
* [Twilio auf GitHub][twilio_on_github]
* [Kontakt zum Twilio-Support][twilio_support]

[twilio_ruby]: https://www.twilio.com/docs/ruby/install





[twilio_pricing]: http://www.twilio.com/pricing
[special_offer]: http://ahoy.twilio.com/azure
[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]: https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
[twilio_api_documentation]: http://www.twilio.com/api
[twilio_security_guidelines]: http://www.twilio.com/docs/security
[twilio_howtos]: http://www.twilio.com/docs/howto
[twilio_on_github]: https://github.com/twilio
[twilio_support]: http://www.twilio.com/help/contact
[twilio_quickstarts]: http://www.twilio.com/docs/quickstart
[sinatra]: http://www.sinatrarb.com/
[azure_vm_setup]: http://www.windowsazure.com/develop/ruby/tutorials/web-app-with-linux-vm/

<!---HONumber=July15_HO3-->