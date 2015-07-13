<properties 
	pageTitle="Verwenden von Twilio für Telefonie- und SMS-Funktionen (Java) - Azure" 
	description="Erfahren Sie, wie Sie mit dem Twilio API-Dienst in Azure einen Telefonanruf tätigen und eine SMS-Nachricht senden. Die Codebeispiele wurden in Java geschrieben." 
	services="" 
	documentationCenter="java" 
	authors="devinrader" 
	manager="twilio" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="11/25/2014" 
	ms.author="microsofthelp@twilio.com"/>

# Verwenden von Twilio für Telefonie- und SMS-Funktionen in Java

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem Twilio-API-Dienst in Azure. Die Szenarien behandeln das Tätigen eines Telefonanrufs und das Senden einer Kurznachricht (SMS). Weitere Informationen zu Twilio und zur Verwendung von Telefonie und SMS in Ihren Anwendungen finden Sie im Abschnitt [Nächste Schritte](#NextSteps).

<h2><a id="WhatIs"></a>Was ist Twilio?</h2>
Twilio ist eine Webservice-API für Telefonie, mit deren Hilfe Sie Ihre existierenden Websprachen und -Fertigkeiten für die Erstellung von Telefonie- und SMS-Anwendungen nutzen können. Twilio ist ein externer Dienst (kein Feature von Azure und kein Microsoft-Produkt).

**Twilio Voice** ermöglicht Anwendungen das Tätigen und Entgegennehmen von Telefonanrufen. **Twilio SMS** ermöglicht Ihren Anwendungen das Senden und Empfangen von SMS-Nachrichten. **Twilio Client** ermöglicht Sprachkommunikation in Ihren Anwendungen über existierende Internetverbindungen, inklusive mobiler Verbindungen.

<h2><a id="Pricing"></a>Twilio-Preise und -Sonderangebote</h2>
Informationen über die Twilio-Preise finden Sie unter [Twilio Pricing][twilio_pricing]. Azure-Kunden erhalten ein [Sonderangebot][special_offer]: ein Gratis-Guthaben von 1000 SMS oder 1000 eingehenden Sprachminuten. Besuchen Sie [http://ahoy.twilio.com/azure][special_offer], um sich für dieses Angebot zu registrieren und weitere Informationen zu erhalten.

<h2><a id="Concepts"></a>Konzepte</h2>
Die Twilio-API ist eine RESTful-API, die Telefonie- und SMS-Funktionen für Anwendungen bereitstellt. Clientbibliotheken stehen in mehreren Sprachen zur Verfügung; eine Liste finden Sie auf der Seite der [Twilio-API-Bibliotheken][twilio_libraries].

Schlüsselaspekte der Twilio API sind Twilio-Verben und die Twilio Markup Language (TwiML).

<h3><a id="Verbs"></a>Twilio-Verben</h3>
Die API verwendet Twilio-Verben. So weist beispielsweise das Verb **&lt;Say&gt;** Twilio an, ein Nachricht in einem Anruf akustisch zu übermitteln.

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

<h3><a id="TwiML"></a>TwiML</h3>
TwiML ist ein Satz XML-basierter Anweisungen auf Grundlage der Twilio-Verben, die Twilio anweisen, wie ein Anruf oder eine SMS verarbeitet werden soll.

Die TwiML im folgenden Beispiel konvertiert den Text **Hello World** in Sprache.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Wenn eine Anwendung die Twilio-API aufruft, ist einer der API-Parameter die URL, die die TwiML-Antwort zurückgibt. Zu Entwicklungszwecken können Sie die von Twilio zur Verfügung gestellten URLs verwenden, um die von Ihren Anwendungen verwendeten TwiML-Antworten bereitzustellen. Sie können auch Ihre eigenen URLs hosten, um TwiML-Antworten zu generieren; eine weitere Option ist die Verwendung des **TwiMLResponse**-Objekts.

Nähere Informationen zu Twilio-Verben, ihren Attributen sowie TwiML finden Sie unter [TwiML][twiml]. Weitere Informationen zur Twilio-API finden Sie unter [Twilio-API][twilio_api].

<h2><a id="CreateAccount"></a>Erstellen eines Twilio-Kontos</h2>
Wenn Sie ein Twilio-Konto erstellen möchten, melden Sie sich bei [Try Twilio][try_twilio] an. Sie können mit einem kostenlosen Konto beginnen und später ein Upgrade vornehmen.

Wenn Sie sich für ein Twilio-Konto anmelden, erhalten Sie eine Konto-ID und ein Authentifizierungstoken. Beide Angaben benötigen Sie zum Tätigen von Twilio-API-Anrufen. Um den unbefugten Zugriff auf Ihr Konto zu verhindern, bewahren Sie Ihr Authentifizierungstoken sicher auf. Ihre Konto-ID und das Authentifizierungstoken können Sie auf der [Twilio-Kontoseite][twilio_account] in den Feldern **ACCOUNT SID** bzw. **AUTH TOKEN** einsehen.

<h2><a id="create_app"></a>Erstellen einer Java-Anwendung</h2>
1. Laden Sie das Twilio-JAR herunter und fügen Sie es zu Ihrem Java-Buildpfad und zu Ihrer WAR-Bereitstellungs-Assembly hinzu. Unter [https://github.com/twilio/twilio-java][twilio_java], können Sie die GitHub-Quellen herunterladen und Ihr eigenes JAR erstellen oder ein bereits erstelltes JAR herunterladen (mit oder ohne Abhängigkeiten).
2. Stellen Sie sicher, dass der **cacerts**-Zertifikatspeicher Ihres SDK das Zertifikat der Equifax-Zertifizierungsstelle mit dem MD5-Fingerabdruck 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 enthält (die Seriennummer ist 35:DE:F4:CF und der SHA1-Fingerabdruck ist D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Dies ist das Zertifizierungsstellen (ZS)-Zertifikat für den [https://api.twilio.com][twilio_api_service]-Dienst, der beim Verwenden der Twilio-APIs aufgerufen wird. Informationen zur Prüfung der im **cacerts**-Zertifikatspeicher Ihres SDK enthaltenen ZS-Zertifikate finden Sie unter [Hinzufügen eines Zertifikats zum Java ZS-Zertifikatspeicher][add_ca_cert].

Ausführliche Informationen zur Verwendung der Twilio-Clientbibliothek für Java finden Sie unter [How to Make a Phone Call Using Twilio in a Java Application on Azure][howto_phonecall_java] (in englischer Sprache).

<h2><a id="configure_app"></a>Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken</h2>
Sie können **import**-Anweisungen am Anfang Ihrer Quelldateien für die Twilio-Pakete oder Klassen hinzufügen, die Sie in Ihrer Anwendung verwenden möchten.

Für Java-Quelldateien:

    import com.twilio.*;
    import com.twilio.sdk.*;
    import com.twilio.sdk.resource.factory.*;
    import com.twilio.sdk.resource.instance.*;

Für Java Server Page (JSP)-Quelldateien:

    import="com.twilio.*"
    import="com.twilio.sdk.*"
    import="com.twilio.sdk.resource.factory.*"
    import="com.twilio.sdk.resource.instance.*"
Je nach verwendeten Twilio-Paketen oder Klassen sehen Ihre **import**-Anweisungen möglicherweise unterschiedlich aus.

<h2><a id="howto_make_call"></a>Tätigen von ausgehenden Anrufen</h2>
Nachfolgend wird erläutert, wie Sie einen ausgehenden Anruf mit der **CallFactory**-Klasse tätigen. Dieser Code verwendet eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort (Twilio Markup Language). Geben Sie Ihre Werte für die Telefonnummern **From** und **To** ein, und stellen Sie sicher, dass Sie die **From**-Telefonnummer für Ihr Twilio-Konto verifizieren, bevor Sie den Code ausführen.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the CallFactory.
    Account account = client.getAccount();

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Use your own value for the second parameter.
    params.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    params.put("Url", Url);

    // Create an instance of the CallFactory class.
    CallFactory callFactory = account.getCallFactory();

    // Make the call.
    Call call = callFactory.create(params);

Weitere Informationen zu den an die **CallFactory.create**-Methode übergebenen Parametern finden Sie unter [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls].

Wie bereits erwähnt, verwendet dieser Code eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort. Stattdessen können Sie die TwiML-Antwort auch von Ihrer eigenen Website bereitstellen lassen. Weitere Informationen finden Sie unter [How to Provide TwiML Responses in a Java Application on Azure](#howto_provide_twiml_responses) (in englischer Sprache).

<h2><a id="howto_send_sms"></a>Senden von SMS-Nachrichten</h2>
Das folgende Beispiel zeigt, wie Sie eine SMS-Nachricht mit der **SmsFactory**-Klasse senden. Die **From**-Nummer **4155992671** wird von Twilio für Testkonten zum Senden von SMS-Nachrichten bereitgestellt. Die **To**-Nummer muss für Ihr Twilio-Konto überprüft werden, bevor Sie den Code ausführen.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Retrieve the account, used later to create an instance of the SmsFactory.
    Account account = client.getAccount();

    // Send an SMS message.
    MessageFactory messageFactory = account.getMessageFactory();
    
    List<NameValuePair> params = new ArrayList<NameValuePair>();
    params.add(new BasicNameValuePair("To", "+14159352345")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("From", "+14158141829")); // Replace with a valid phone number for your account.
    params.add(new BasicNameValuePair("Body", "Where's Wallace?"));
    
    Message sms = messageFactory.create(params);
        
Weitere Informationen zu den an die **SmsFactory.create**-Methode übergebenen Parametern finden Sie unter [http://www.twilio.com/docs/api/rest/sending-sms][twilio_rest_sending_sms].

<h2><a id="howto_provide_twiml_responses"></a>Bereitstellen von TwiML-Antworten von der eigenen Website</h2>
Wenn Ihre Anwendung einen Anruf an die Twilio-API initiiert – z. B. über die **CallFactory.create**-Methode – sendet Twilio Ihre Anforderung an eine URL, von der die Rückgabe einer TwiML-Antwort erwartet wird. Das obige Beispiel verwendet die von Twilio bereitgestellte URL [http://twimlets.com/message][twimlet_message_url]. (TwiML wurde für die Verwendung durch Webdienste konzipiert, kann jedoch im Browser angezeigt werden. Klicken Sie beispielsweise auf [http://twimlets.com/message][twimlet_message_url], um ein leeres **&lt;Response&gt;**-Element anzuzeigen. Klicken Sie auf [http://twimlets.com/message?Message%5B0%5D=Hello%20World][twimlet_message_url_hello_world], um ein weiteres Beispiel mit einem **&lt;Response&gt;**-Element anzuzeigen, das ein **&lt;Say&gt;**-Element enthält.)

Anstelle der von Twilio bereitgestellten URL können Sie auch Ihre eigene URL-Website für die Rückgabe von HTTP-Antworten erstellen. Sie können die Website in allen Sprachen erstellen, die HTTP-Antworten zurückgeben. Dieses Beispiel geht davon aus, dass Sie die URL in einer JSP-Seite hosten.

Die folgende JSP-Seite erstellt eine TwiML-Antwort, die beim Anruf **Hello World** sagt.

    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello World</Say>
    </Response>

Die folgende JSP-Seite erstellt eine TwiML-Antwort, die zunächst etwas Text spricht, mehrere Pausen enthält, und anschließend Informationen über die Version der Twilio-API und den Azure-Rollennamen spricht.


    <%@ page contentType="text/xml" %>
    <Response> 
        <Say>Hello from Azure</Say>
        <Pause></Pause>
        <Say>The Twilio API version is <%= request.getParameter("ApiVersion") %>.</Say>
        <Say>The Azure role name is <%= System.getenv("RoleName") %>.</Say>
        <Pause></Pause>
        <Say>Good bye.</Say>
    </Response>

Der Parameter **ApiVersion** ist nur in Twilio-Sprachanforderungen verfügbar (nicht in SMS-Anforderungen). Eine Liste der verfügbaren Anforderungsparameter für Twilio-Sprach- und SMS-Anforderungen finden Sie unter <https://www.twilio.com/docs/api/twiml/twilio_request> bzw. <https://www.twilio.com/docs/api/twiml/sms/twilio_request>. Die Umgebungsvariable **RoleName** ist als Teil der Azure-Bereitstellung verfügbar. (Informationen zum Hinzufügen eigener Umgebungsvariablen, die mit **System.getenv** abgerufen werden können, finden Sie im Abschnitt zu Umgebungsvariablen unter [Miscellaneous Role Configuration Settings][misc_role_config_settings] (in englischer Sprache).)

Sobald Sie Ihre JSP-Seite mit den TwiML-Antworten eingerichtet haben, können Sie die URL der JSP-Seite an die **CallFactory.create**-Methode übergeben. Beispiel: Ihre Webanwendung "MyTwiML" liegt in einem unter Azure gehosteten Dienst, und der Name der JSP-Seite lautet "mytwiml.jsp". In diesem Fall kann die URL wie im folgenden Codebeispiel gezeigt an **CallFactory.create** übergeben werden:

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Eine weitere Option zum Antworten mit TwiML ist die **TwiMLResponse**-Klasse, die im Paket **com.twilio.sdk.verbs** verfügbar ist.

Weitere Informationen über die Verwendung von Twilio in Azure mit Java finden Sie unter [Tätigen eines Telefonanrufs mithilfe von Twilio in einer Java-Anwendung auf Azure][howto_phonecall_java].

<h2><a id="AdditionalServices"></a>Verwenden zusätzlicher Twilio-Dienste</h2>
Zusätzlich zu den hier gezeigten Beispielen bietet Twilio webbasierte APIs, mit denen Sie zusätzliche Twilio-Funktionen in Ihrer Azure-Anwendung verwenden können. Ausführliche Informationen finden Sie in der [Twilio API-Dokumentation][twilio_api_documentation].

<h2><a id="NextSteps"></a>Nächste Schritte</h2>
Nachdem Sie nun mit den Grundlagen des Twilio-Dienstes vertraut sind, finden Sie unter diesen Links weitere Informationen:

* [Twilio Security Guidelines][twilio_security_guidelines] (Twilio-Sicherheitsrichtlinien, in englischer Sprache)
* [Twilio-Anleitungen und Beispielcode][twilio_howtos]
* [Twilio Quickstart Tutorials][twilio_quickstarts] (Twilio-Schnellstart-Lernprogramme, in englischer Sprache) 
* [Twilio auf GitHub][twilio_on_github]
* [Kontakt zum Twilio-Support][twilio_support]

[twilio_java]: https://github.com/twilio/twilio-java
[twilio_api_service]: https://api.twilio.com
[add_ca_cert]: java-add-certificate-ca-store.md
[howto_phonecall_java]: partner-twilio-java-phone-call-example.md
[misc_role_config_settings]: http://msdn.microsoft.com/library/windowsazure/hh690945.aspx
[twimlet_message_url]: http://twimlets.com/message
[twimlet_message_url_hello_world]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls
[twilio_rest_sending_sms]: http://www.twilio.com/docs/api/rest/sending-sms
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

<!---HONumber=July15_HO1-->