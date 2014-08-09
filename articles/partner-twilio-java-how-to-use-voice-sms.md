<properties linkid="develop-java-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Java) - Azure" metaKeywords="Twilio, Twilio API, phone calls, SMS message, TwiML responses, Azure Twilio Java" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Java." metaCanonical="" services="" videoId="" scriptId="" documentationCenter="Java" title="How to Use Twilio for Voice and SMS Capabilities in Java" authors="waltpo" solutions="" manager="bjsmith" editor="mollybos" />

Verwenden von Twilio für Telefonie- und SMS-Funktionen in Java
==============================================================

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem Twilio-API-Dienst in Azure. Die Szenarien behandeln das Tätigen eines Telefonanrufs und das Senden einer Kurznachricht (SMS). Weitere Informationen zu Twilio und zur Verwendung von Telefonie und SMS in Ihren Anweisungen finden Sie im Abschnitt [Nächste Schritte](#NextSteps).

Inhaltsverzeichnis
------------------

-   [Was ist Twilio?](#WhatIs)
-   [Twilio-Preise](#Pricing)
-   [Konzepte](#Concepts)
-   [Erstellen eines Twilio-Kontos](#CreateAccount)
-   [Verifizieren von Telefonnummern](#VerifyPhoneNumbers)
-   [Erstellen einer Java-Anwendung](#create_app)
-   [Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken](#configure_app)
-   [Vorgehensweise: Tätigen von ausgehenden Anrufen](#howto_make_call)
-   [Vorgehensweise: Senden einer SMS-Nachricht](#howto_send_sms)
-   [Vorgehensweise: Bereitstellen von TwiML-Antworten von der eigenen Website](#howto_provide_twiml_responses)
-   [Vorgehensweise: Verwenden zusätzlicher Twilio-Dienste](#AdditionalServices)
-   [Nächste Schritte](#NextSteps)

Was ist Twilio?
---------------

Twilio ist eine Webservice-API für Telefonie, mit deren Hilfe Sie Ihre existierenden Websprachen und -Fertigkeiten für die Erstellung von Telefonie- und SMS-Anwendungen nutzen können. Twilio ist ein externer Dienst (kein Feature von Azure und kein Microsoft-Produkt).

**Twilio Voice** ermöglicht Anwendungen das Tätigen und Entgegennehmen von Telefonanrufen. **Twilio SMS** ermöglicht Anwendungen das Senden und Empfangen von SMS-Nachrichten. **Twilio Client** ermöglicht Sprachkommunikation in Ihren Anwendungen über existierende Internetverbindungen, inklusive mobiler Verbindungen.

Twilio-Preise und -Sonderangebote
---------------------------------

Informationen über die Twilio-Preise finden Sie unter [Twilio Pricing](http://www.twilio.com/pricing). Azure-Kunden erhalten ein [Sonderangebot](http://ahoy.twilio.com/azure): ein Gratis-Guthaben von 1000 SMS oder 1000 eingehenden Sprachminuten. Besuchen Sie <http://ahoy.twilio.com/azure>, um sich für dieses Angebot zu registrieren und weitere Informationen zu erhalten.

Konzepte
--------

Die Twilio-API ist eine RESTful-API, die Telefonie- und SMS-Funktionen für Anwendungen bereitstellt. Clientbibliotheken stehen in mehreren Sprachen zur Verfügung; eine Liste finden Sie auf der Seite der [Twilio-API-Bibliotheken](https://www.twilio.com/docs/libraries).

Schlüsselaspekte der Twilio API sind Twilio-Verben und die Twilio Markup Language (TwiML).

### Twilio-Verben

Die API verwendet Twilio-Verben; so weist beispielsweise das Verb **&lt;Say\>** Twilio an, ein Nachricht in einem Anruf akustisch zu übermitteln.

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

### TwiML

TwiML ist ein Satz XML-basierter Anweisungen auf Grundlage der Twilio-Verben, die Twilio anweisen, wie ein Anruf oder eine SMS verarbeitet werden soll.

Die TwiML im folgenden Beispiel konvertiert den Text **Hello World** in Sprache.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Wenn eine Anwendung die Twilio-API aufruft, ist einer der API-Parameter die URL, welche die TwiML-Antwort zurückgibt. Zu Entwicklungszwecken können Sie die von Twilio zur Verfügung gestellten URLs verwenden, um die von Ihren Anwendungen verwendeten TwiML-Antworten bereitzustellen. Sie können auch Ihre eigenen URLs hosten, um TwiML-Antworten zu generieren; eine weitere Option ist die Verwendung des **TwiMLResponse**-Objekts.

Nähere Informationen zu Twilio-Verben, ihren Attributen sowie TwiML finden Sie unter [TwiML](http://www.twilio.com/docs/api/twiml). Weitere Informationen zur Twilio-API finden Sie unter [Twilio-API](http://www.twilio.com/api).

Erstellen eines Twilio-Kontos
-----------------------------

Wenn Sie ein Twilio-Konto erstellen möchten, melden Sie sich bei [Try Twilio](https://www.twilio.com/try-twilio) an. Sie können mit einem kostenlosen Konto beginnen und später ein Upgrade vornehmen.

Wenn Sie sich für ein Twilio-Konto anmelden, erhalten Sie eine Konto-ID und ein Authentifizierungstoken. Beide Angaben benötigen Sie zum Tätigen von Twilio-API-Anrufen. Um den unbefugten Zugriff auf Ihr Konto zu verhindern, bewahren Sie Ihr Authentifizierungstoken sicher auf. Ihre Konto-ID und das Authentifizierungstoken können Sie auf der [Twilio-Kontoseite](https://www.twilio.com/user/account) in den Feldern **ACCOUNT SID** bzw. **AUTH TOKEN** einsehen.

Verifizieren von Telefonnummern
-------------------------------

Verschiedene Telefonnummern müssen bei Twilio für Ihr Konto verifiziert werden. Wenn Sie beispielsweise ausgehende Telefonate tätigen möchten, muss die Telefonnummer als ausgehende Anrufer-ID bei Twilio verifiziert werden. Wenn Sie SMS-Nachrichten an eine Telefonnummer senden möchten, muss entsprechend die Telefonnummer des Empfängers bei Twilio verifiziert werden. Informationen zum Verifizieren von Telefonnummern finden Sie unter [Manage Numbers](https://www.twilio.com/user/account/phone-numbers/verified#) (in englischer Sprache). In einigen der folgenden Codebeispiele werden Telefonnummern verwendet, die Sie bei Twilio verifizieren müssen.

Alternativ zur Verwendung einer vorhandenen Telefonnummer für Ihre Anwendungen besteht die Möglichkeit, eine Twilio-Telefonnummer zu kaufen. Informationen zum Kaufen einer Twilio-Telefonnummer finden Sie unter [Twilio Phone Numbers Help](https://www.twilio.com/help/faq/phone-numbers) (in englischer Sprache).

Erstellen einer Java-Anwendung
------------------------------

1.  Laden Sie das Twilio-JAR herunter und fügen Sie es zu Ihrem Java-Buildpfad und zu Ihrer WAR-Bereitstellungs-Assembly hinzu. Unter <https://github.com/twilio/twilio-java>, können Sie die GitHub-Quellen herunterladen und Ihr eigenes JAR erstellen oder ein bereits erstelltes JAR herunterladen (mit oder ohne Abhängigkeiten).
2.  Stellen Sie sicher, dass der **cacerts**-Zertifikatspeicher Ihres SDK das Zertifikat der Equifax-Zertifizierungsstelle mit dem MD5-Fingerabdruck 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 enthält (die Seriennummer ist 35:DE:F4:CF und der SHA1-Fingerabdruck ist D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Dies ist das Zertifizierungsstellen (ZS)-Zertifikat für den <https://api.twilio.com>-Dienst, der beim Verwenden der Twilio-APIs aufgerufen wird. Informationen zur Prüfung der im **cacerts**-Zertifikatspeicher Ihres SDK enthaltenen ZS-Zertifikate finden Sie unter [Hinzufügen eines Zertifikats zum Java ZS-Zertifikatspeicher](../java-add-certificate-ca-store).

Ausführliche Informationen zur Verwendung der Twilio-Clientbibliothek für Java finden Sie unter [How to Make a Phone Call Using Twilio in a Java Application on Azure](../partner-twilio-java-phone-call-example) (in englischer Sprache).

Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken
------------------------------------------------------------------------

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

Vorgehensweise: Tätigen von ausgehenden Anrufen
-----------------------------------------------

Nachfolgend wird erläutert, wie Sie einen ausgehenden Anruf mit der **CallFactory**-Klasse tätigen. Dieser Code verwendet eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort (Twilio Markup Language). Geben Sie Ihre Werte für die Telefonnummern **From** und **To** ein, und stellen Sie sicher, dass Sie die **From**-Telefonnummer für Ihr Twilio-Konto verifizieren, bevor Sie den Code ausführen.

    // Verwenden Sie Ihre Konto-SID und Ihr Authentifizierungstoken
    // anstelle der hier gezeigten Platzhalter.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Erstellen einer Instanz des Twilio-Clients.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Konto abrufen, wird später für die Erstellung einer CallFactory verwendet.
    Account account = client.getAccount();

    // Verwenden Sie die von Twilio bereitgestellte Website für die TwiML-Antwort.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Platzieren Sie die From-, To- und URL-Werte für den Anruf in einer Hashzuordnung. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN"); // Verwenden Sie einen eigenen Wert für den ersten Parameter.
    params.put("To", "NNNNNNNNNN");   // Verwenden Sie einen eigenen Wert für den zweiten Parameter.
    params.put("Url", Url);

    // Erstellen einer Instanz der CallFactory-Klasse.
    CallFactory callFactory = account.getCallFactory();

    // Ausführen des Anrufs.
    Call call = callFactory.create(params);

Weitere Informationen zu den an die **CallFactory.create**-Methode übergebenen Parametern finden Sie unter <http://www.twilio.com/docs/api/rest/making-calls>.

Wie bereits erwähnt, verwendet dieser Code eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort. Stattdessen können Sie die TwiML-Antwort auch von Ihrer eigenen Website bereitstellen lassen. Weitere Informationen finden Sie unter [How to Provide TwiML Responses in a Java Application on Azure](#howto_provide_twiml_responses) (in englischer Sprache).

Vorgehensweise: Senden einer SMS-Nachricht
------------------------------------------

Das folgende Beispiel zeigt, wie Sie eine SMS-Nachricht mit der **SmsFactory**-Klasse senden. Die **From**-Nummer **4155992671** wird von Twilio für Testkonten zum Senden von SMS-Nachrichten bereitgestellt. Die **To**-Nummer muss für Ihr Twilio-Konto verifiziert werden, bevor Sie den Code ausführen.

    // Verwenden Sie Ihre Konto-SID und Ihr Authentifizierungstoken
    // anstelle der hier gezeigten Platzhalter.
    String accountSID = "your_twilio_account";
    String authToken = "your_twilio_authentication_token";

    // Erstellen einer Instanz des Twilio-Clients.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Konto abrufen, wird später für die Erstellung einer SmsFactory verwendet.
    Account account = client.getAccount();

    // SMS-Nachricht verschicken.
    // Platzieren Sie die From-, To- und Body-Werte in einer Hashzuordnung. 
    HashMap<String, String> smsParams = new HashMap<String, String>();
    smsParams.put("From", "4155992671"); // der zweite Parameter ist eine
                                         // Twilio-Testnummer.
    smsParams.put("To", "NNNNNNNNNN");   // Verwenden Sie einen eigenen Wert für den zweiten Parameter.
    smsParams.put("Body", "This is my SMS message.");

    // Erstellen einer Instanz der SmsFactory-Klasse.
    SmsFactory smsFactory = account.getSmsFactory();

    // Nachricht verschicken.
    Sms sms = smsFactory.create(smsParams);

Weitere Informationen zu den an die **SmsFactory.create**-Methode übergebenen Parametern finden Sie unter <http://www.twilio.com/docs/api/rest/sending-sms>.

Vorgehensweise: Bereitstellen von TwiML-Antworten von der eigenen Website
-------------------------------------------------------------------------

Wenn Ihre Anwendung einen Anruf an die Twilio-API initiiert – z. B. über die **CallFactory.create**-Methode – sendet Twilio Ihre Anforderung an eine URL, von der die Rückgabe einer TwiML-Antwort erwartet wird. Das obige Beispiel verwendet die von Twilio bereitgestellte URL <http://twimlets.com/message>. (TwiML wurde für die Verwendung durch Webdienste konzipiert, kann jedoch im Browser angezeigt werden. Klicken Sie z. B. auf <http://twimlets.com/message>, um ein leeres **&lt;Response\>**-Element anzuzeigen, oder klicken Sie auf <http://twimlets.com/message?Message%5B0%5D=Hello%20World>, um ein **&lt;Response\>**-Element mit einem **&lt;Say\>**-Element anzuzeigen.

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

Der Parameter **ApiVersion** ist nur in Twilio-Sprachanfragen verfügbar (nicht in SMS-Anfragen). Eine Liste der verfügbaren Anfrageparameter für Twilio-Sprach- und SMS-Anfragen finden Sie unter &lt;https://www.twilio.com/docs/api/twiml/twilio\_request\> bzw. &lt;https://www.twilio.com/docs/api/twiml/sms/twilio\_request\>. Die Umgebungsvariable **RoleName** ist als Teil der Azure-Bereitstellung verfügbar. (Informationen zum Hinzufügen eigener Umgebungsvariablen, die mit **System.getenv** abgerufen werden können, finden Sie im Abschnitt zu Umgebungsvariablen unter [Miscellaneous Role Configuration Settings](http://msdn.microsoft.com/de-de/library/windowsazure/hh690945.aspx) (in englischer Sprache).)

Sobald Sie Ihre JSP-Seite mit den TwiML-Antworten eingerichtet haben, können Sie die URL der JSP-Seite an die **CallFactory.create**-Methode übergeben. Beispiel: Ihre Webanwendung "MyTwiML" liegt in einem unter Azure gehosteten Dienst, und der Name der JSP-Seite lautet "mytwiml.jsp". In diesem Fall kann die URL wie im folgenden Codebeispiel gezeigt an **CallFactory.create** übergeben werden:

    // Platzieren Sie die From-, To- und URL-Werte für den Anruf in einer Hashzuordnung. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Eine weitere Option zum Antworten mit TwiML ist die **TwiMLResponse**-Klasse, die im Paket **com.twilio.sdk.verbs** verfügbar ist.

Weitere Informationen über die Verwendung von Twilio in Azure mit Java finden Sie unter [Tätigen eines Telefonanrufs mithilfe von Twilio in einer Java-Anwendung auf Azure](../partner-twilio-java-phone-call-example).

Vorgehensweise: Verwenden zusätzlicher Twilio-Dienste
-----------------------------------------------------

Zusätzlich zu den hier gezeigten Beispielen bietet Twilio webbasierte APIs, mit denen Sie zusätzliche Twilio-Funktionen in Ihrer Azure-Anwendung nutzen können. Ausführliche Informationen finden Sie in der [Twilio API-Dokumentation](http://www.twilio.com/api).

Nächste Schritte
----------------

Nachdem Sie nun mit den Grundlagen des Twilio-Dienstes vertraut sind, finden Sie unter diesen Links weitere Informationen:

-   [Twilio Security Guidelines](http://www.twilio.com/docs/security)
-   [Twilio HowTos und Beispielcode](http://www.twilio.com/docs/howto)
-   [Twilio Quickstart Tutorials](http://www.twilio.com/docs/quickstart)
-   [Twilio on GitHub](https://github.com/twilio)
-   [Talk to Twilio Support](http://www.twilio.com/help/contact)

