<properties linkid="develop-java-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (Java) - Azure" metaKeywords="Twilio, Twilio API, phone calls, SMS message, TwiML responses, Azure Twilio Java" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in Java." metaCanonical="" services="" videoId="" scriptId="" documentationCenter="Java" title="How to Use Twilio for Voice and SMS Capabilities in Java" authors="MicrosoftHelp@twilio.com; robmcm" solutions="" manager="wpickett" editor="mollybos" />

<tags ms.service="multiple" ms.workload="na" ms.tgt_pltfrm="na" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; robmcm" />

# Verwenden von Twilio für Telefonie- und SMS-Funktionen in Java

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem Twilio-API-Dienst in Azure. Die Szenarien behandeln das Tätigen eines Telefonanrufs und das Senden einer Kurznachricht (SMS). Weitere Informationen zu Twilio und zur Verwendung von Telefonie und SMS in Ihren Anwendungen finden Sie im Abschnitt [Nächste Schritte][Nächste Schritte].

## Inhaltsverzeichnis

-   [Was ist Twilio?][Was ist Twilio?]
-   [Twilio-Preise][Twilio-Preise]
-   [Konzepte][Konzepte]
-   [Erstellen eines Twilio-Kontos][Erstellen eines Twilio-Kontos]
-   [Überprüfen von Telefonnummern][Überprüfen von Telefonnummern]
-   [Erstellen einer Java-Anwendung][Erstellen einer Java-Anwendung]
-   [Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken][Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken]
-   [Gewusst wie: Tätigen von ausgehenden Anrufen][Gewusst wie: Tätigen von ausgehenden Anrufen]
-   [Gewusst wie: Senden einer SMS-Nachricht][Gewusst wie: Senden einer SMS-Nachricht]
-   [Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website][Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website]
-   [Gewusst wie: Verwenden zusätzlicher Twilio-Dienste][Gewusst wie: Verwenden zusätzlicher Twilio-Dienste]
-   [Nächste Schritte][Nächste Schritte]

## <span id="WhatIs"></span></a>Was ist Twilio?

Twilio ist eine Webservice-API für Telefonie, mit deren Hilfe Sie Ihre existierenden Websprachen und -Fertigkeiten für die Erstellung von Telefonie- und SMS-Anwendungen nutzen können. Twilio ist ein externer Dienst (kein Feature von Azure und kein Microsoft-Produkt).

**Twilio Voice** ermöglicht Anwendungen das Tätigen und Entgegennehmen von Telefonanrufen. **Twilio SMS** ermöglicht Ihren Anwendungen das Senden und Empfangen von SMS-Nachrichten. **Twilio Client** ermöglicht Sprachkommunikation in Ihren Anwendungen über existierende Internetverbindungen, inklusive mobiler Verbindungen.

## <span id="Pricing"></span></a>Twilio-Preise und -Sonderangebote

Informationen über die Twilio-Preise finden Sie unter [Twilio Pricing][Twilio Pricing]. Azure-Kunden erhalten ein [Sonderangebot][Sonderangebot]: ein Gratis-Guthaben von 1000 SMS oder 1000 eingehenden Sprachminuten. Besuchen Sie [][Sonderangebot]<http://ahoy.twilio.com/azure></a>, um sich für dieses Angebot zu registrieren und weitere Informationen zu erhalten.

## <span id="Concepts"></span></a>Konzepte

Die Twilio-API ist eine RESTful-API, die Telefonie- und SMS-Funktionen für Anwendungen bereitstellt. Clientbibliotheken stehen in mehreren Sprachen zur Verfügung; eine Liste finden Sie auf der Seite der [Twilio-API-Bibliotheken][Twilio-API-Bibliotheken].

Schlüsselaspekte der Twilio API sind Twilio-Verben und die Twilio Markup Language (TwiML).

### <span id="Verbs"></span></a>Twilio-Verben

Die API verwendet Twilio-Verben; so weist beispielsweise das Verb **\<Say\>** Twilio an, ein Nachricht in einem Anruf akustisch zu übermitteln.

Nachfolgend finden Sie eine Liste mit Twilio-Verben.

-   **\<Dial\>**: Verbindet den Anrufer mit einem anderen Telefon.
-   **\<Gather\>**: Erfasst Ziffern, die über die Telefontasten eingegeben werden.
-   **\<Hangup\>**: Beendet einen Anruf.
-   **\<Play\>**: Gibt eine Audiodatei wieder.
-   **\<Pause\>**: Wartet ohne Ton für eine angegebene Anzahl von Sekunden.
-   **\<Record\>**: Zeichnet die Stimme des Anrufers auf und gibt eine URL einer Datei mit der Aufzeichnung zurück.
-   **\<Redirect\>**: Überträgt die Steuerung eines Anrufs oder einer SMS an die TwiML an einer anderen URL.
-   **\<Reject\>**: Lehnt einen bei Ihrer Twilio-Nummer eingehenden Anruf ab, ohne dass Ihnen Kosten entstehen.
-   **\<Say\>**: Konvertiert Text in Sprache für einen Anruf.
-   **\<Sms\>**: Sendet eine SMS-Nachricht.

### <span id="TwiML"></span></a>TwiML

TwiML ist ein Satz XML-basierter Anweisungen auf Grundlage der Twilio-Verben, die Twilio anweisen, wie ein Anruf oder eine SMS verarbeitet werden soll.

Die TwiML im folgenden Beispiel konvertiert den Text **Hello World** in Sprache.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Wenn eine Anwendung die Twilio-API aufruft, ist einer der API-Parameter die URL, die die TwiML-Antwort zurückgibt. Zu Entwicklungszwecken können Sie die von Twilio zur Verfügung gestellten URLs verwenden, um die von Ihren Anwendungen verwendeten TwiML-Antworten bereitzustellen. Sie können auch Ihre eigenen URLs hosten, um TwiML-Antworten zu generieren; eine weitere Option ist die Verwendung des **TwiMLResponse**-Objekts.

Nähere Informationen zu Twilio-Verben, ihren Attributen sowie TwiML finden Sie unter [TwiML][TwiML]. Weitere Informationen zur Twilio-API finden Sie unter [Twilio-API][Twilio-API].

## <span id="CreateAccount"></span></a>Erstellen eines Twilio-Kontos

Wenn Sie ein Twilio-Konto erstellen möchten, melden Sie sich bei [Try Twilio][Try Twilio] an. Sie können mit einem kostenlosen Konto beginnen und später ein Upgrade vornehmen.

Wenn Sie sich für ein Twilio-Konto anmelden, erhalten Sie eine Konto-ID und ein Authentifizierungstoken. Beide Angaben benötigen Sie zum Tätigen von Twilio-API-Anrufen. Um den unbefugten Zugriff auf Ihr Konto zu verhindern, bewahren Sie Ihr Authentifizierungstoken sicher auf. Ihre Konto-ID und das Authentifizierungstoken können Sie auf der [Twilio-Kontoseite][Twilio-Kontoseite] in den Feldern **ACCOUNT SID** bzw. **AUTH TOKEN** einsehen.

## <span id="VerifyPhoneNumbers"></span></a>Überprüfen von Telefonnummern

Verschiedene Telefonnummern müssen bei Twilio für Ihr Konto verifiziert werden. Wenn Sie beispielsweise ausgehende Telefonate tätigen möchten, muss die Telefonnummer als ausgehende Anrufer-ID bei Twilio verifiziert werden. Wenn Sie SMS-Nachrichten an eine Telefonnummer senden möchten, muss entsprechend die Telefonnummer des Empfängers bei Twilio verifiziert werden. Informationen zum Überprüfen von Telefonnummern finden Sie unter [Manage Numbers][Manage Numbers]. In einigen der folgenden Codebeispiele werden Telefonnummern verwendet, die Sie bei Twilio verifizieren müssen.

Alternativ zur Verwendung einer vorhandenen Telefonnummer für Ihre Anwendungen besteht die Möglichkeit, eine Twilio-Telefonnummer zu kaufen. Informationen zum Kaufen einer Twilio-Telefonnummer finden Sie in der [Twilio-Hilfe zu Telefonnummern][Twilio-Hilfe zu Telefonnummern] (in englischer Sprache).

## <span id="create_app"></span></a>Erstellen einer Java-Anwendung

1.  Laden Sie das Twilio-JAR herunter und fügen Sie es zu Ihrem Java-Buildpfad und zu Ihrer WAR-Bereitstellungs-Assembly hinzu. Unter [][]<https://github.com/twilio/twilio-java></a>, können Sie die GitHub-Quellen herunterladen und Ihr eigenes JAR erstellen oder ein bereits erstelltes JAR herunterladen (mit oder ohne Abhängigkeiten).
2.  Stellen Sie sicher, dass der **cacerts**-Zertifikatspeicher Ihres SDK das Zertifikat der Equifax-Zertifizierungsstelle mit dem MD5-Fingerabdruck 67:CB:9D:C0:13:24:8A:82:9B:B2:17:1E:D1:1B:EC:D4 enthält (die Seriennummer ist 35:DE:F4:CF und der SHA1-Fingerabdruck ist D2:32:09:AD:23:D3:14:23:21:74:E4:0D:7F:9D:62:13:97:86:63:3A). Dies ist das Zertifizierungsstellen (ZS)-Zertifikat für den [][1]<https://api.twilio.com></a>-Dienst, der beim Verwenden der Twilio-APIs aufgerufen wird. Informationen zur Prüfung der im **cacerts**-Zertifikatspeicher Ihres SDK enthaltenen ZS-Zertifikate finden Sie unter [Hinzufügen eines Zertifikats zum Java ZS-Zertifikatspeicher][Hinzufügen eines Zertifikats zum Java ZS-Zertifikatspeicher].

Ausführliche Informationen zur Verwendung der Twilio-Clientbibliothek für Java finden Sie unter [How to Make a Phone Call Using Twilio in a Java Application on Azure][How to Make a Phone Call Using Twilio in a Java Application on Azure] (in englischer Sprache).

## <span id="configure_app"></span></a>Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken

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

## <span id="howto_make_call"></span></a>Gewusst wie: Tätigen von ausgehenden Anrufen

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

Weitere Informationen zu den an die **CallFactory.create**-Methode übergebenen Parametern finden Sie unter [][2]<http://www.twilio.com/docs/api/rest/making-calls></a>.

Wie bereits erwähnt, verwendet dieser Code eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort. Stattdessen können Sie die TwiML-Antwort auch von Ihrer eigenen Website bereitstellen lassen. Weitere Informationen finden Sie unter [How to Provide TwiML Responses in a Java Application on Azure][Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website] (in englischer Sprache).

## <span id="howto_send_sms"></span></a>Gewusst wie: Senden einer SMS-Nachricht

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
    // Place the call From, To and Body values into a hash map. 
    HashMap<String, String> smsParams = new HashMap<String, String>();
    smsParams.put("From", "4155992671"); // The second parameter is a phone number provided
                                         // by Twilio for trial accounts.
    smsParams.put("To", "NNNNNNNNNN");   // Use your own value for the second parameter.
    smsParams.put("Body", "This is my SMS message.");

    // Create an instance of the SmsFactory class.
    SmsFactory smsFactory = account.getSmsFactory();

    // Send the message.
    Sms sms = smsFactory.create(smsParams);

Weitere Informationen zu den an die **SmsFactory.create**-Methode übergebenen Parametern finden Sie unter [][3]<http://www.twilio.com/docs/api/rest/sending-sms></a>.

## <span id="howto_provide_twiml_responses"></span></a>Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website

Wenn Ihre Anwendung einen Anruf an die Twilio-API initiiert – z. B. über die **CallFactory.create**-Methode – sendet Twilio Ihre Anforderung an eine URL, von der die Rückgabe einer TwiML-Antwort erwartet wird. Das obige Beispiel verwendet die von Twilio bereitgestellte URL [][4]<http://twimlets.com/message></a>. (TwiML wurde für die Verwendung durch Webdienste konzipiert, kann jedoch im Browser angezeigt werden. Klicken Sie z. B. auf [][4]<http://twimlets.com/message></a>, um ein leeres **\<Response\>**-Element anzuzeigen, oder klicken Sie auf [][5]<http://twimlets.com/message?Message%5B0%5D=Hello%20World></a>, um ein **\<Response\>**-Element mit einem **\<Say\>**-Element anzuzeigen.

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

Der Parameter **ApiVersion** ist nur in Twilio-Sprachanforderungen verfügbar (nicht in SMS-Anforderungen). Eine Liste der verfügbaren Anforderungsparameter für Twilio-Sprach- und SMS-Anforderungen finden Sie unter <https://www.twilio.com/docs/api/twiml/twilio_request> bzw. <https://www.twilio.com/docs/api/twiml/sms/twilio_request>. Die Umgebungsvariable **RoleName** ist als Teil der Azure-Bereitstellung verfügbar. (Informationen zum Hinzufügen eigener Umgebungsvariablen, die mit **System.getenv** abgerufen werden können, finden Sie im Abschnitt zu Umgebungsvariablen unter [Miscellaneous Role Configuration Settings][Miscellaneous Role Configuration Settings] (in englischer Sprache).)

Sobald Sie Ihre JSP-Seite mit den TwiML-Antworten eingerichtet haben, können Sie die URL der JSP-Seite an die **CallFactory.create**-Methode übergeben. Beispiel: Ihre Webanwendung "MyTwiML" liegt in einem unter Azure gehosteten Dienst, und der Name der JSP-Seite lautet "mytwiml.jsp". In diesem Fall kann die URL wie im folgenden Codebeispiel gezeigt an **CallFactory.create** übergeben werden:

    // Place the call From, To and URL values into a hash map. 
    HashMap<String, String> params = new HashMap<String, String>();
    params.put("From", "NNNNNNNNNN");
    params.put("To", "NNNNNNNNNN");
    params.put("Url", "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.jsp");

    CallFactory callFactory = account.getCallFactory();
    Call call = callFactory.create(params);

Eine weitere Option zum Antworten mit TwiML ist die **TwiMLResponse**-Klasse, die im Paket **com.twilio.sdk.verbs** verfügbar ist.

Weitere Informationen über die Verwendung von Twilio in Azure mit Java finden Sie unter [Tätigen eines Telefonanrufs mithilfe von Twilio in einer Java-Anwendung auf Azure][How to Make a Phone Call Using Twilio in a Java Application on Azure].

## <span id="AdditionalServices"></span></a>Gewusst wie: Verwenden zusätzlicher Twilio-Dienste

Zusätzlich zu den hier gezeigten Beispielen bietet Twilio webbasierte APIs, mit denen Sie zusätzliche Twilio-Funktionen in Ihrer Azure-Anwendung verwenden können. Ausführliche Informationen finden Sie in der [Twilio API-Dokumentation][Twilio-API].

## <span id="NextSteps"></span></a>Nächste Schritte

Nachdem Sie nun mit den Grundlagen des Twilio-Dienstes vertraut sind, finden Sie unter diesen Links weitere Informationen:

-   [Twilio Security Guidelines][Twilio Security Guidelines]
-   [Twilio HowTos und Beispielcode][Twilio HowTos und Beispielcode]
-   [Twilio Quickstart Tutorials][Twilio Quickstart Tutorials]
-   [Twilio on GitHub][Twilio on GitHub]
-   [Talk to Twilio Support][Talk to Twilio Support]

  [Nächste Schritte]: #NextSteps
  [Twilio-Preise]: #Pricing
  [Konzepte]: #Concepts
  [Erstellen eines Twilio-Kontos]: #CreateAccount
  [Überprüfen von Telefonnummern]: #VerifyPhoneNumbers
  [Erstellen einer Java-Anwendung]: #create_app
  [Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken]: #configure_app
  [Gewusst wie: Tätigen von ausgehenden Anrufen]: #howto_make_call
  [Gewusst wie: Senden einer SMS-Nachricht]: #howto_send_sms
  [Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website]: #howto_provide_twiml_responses
  [Gewusst wie: Verwenden zusätzlicher Twilio-Dienste]: #AdditionalServices
  [Twilio Pricing]: http://www.twilio.com/pricing
  [Sonderangebot]: http://ahoy.twilio.com/azure
  [Twilio-API-Bibliotheken]: https://www.twilio.com/docs/libraries
  [TwiML]: http://www.twilio.com/docs/api/twiml
  [Twilio-API]: http://www.twilio.com/api
  [Try Twilio]: https://www.twilio.com/try-twilio
  [Twilio-Kontoseite]: https://www.twilio.com/user/account
  [Manage Numbers]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Twilio-Hilfe zu Telefonnummern]: https://www.twilio.com/help/faq/phone-numbers
  []: https://github.com/twilio/twilio-java
  [1]: https://api.twilio.com
  [Hinzufügen eines Zertifikats zum Java ZS-Zertifikatspeicher]: ../java-add-certificate-ca-store
  [How to Make a Phone Call Using Twilio in a Java Application on Azure]: ../partner-twilio-java-phone-call-example
  [2]: http://www.twilio.com/docs/api/rest/making-calls
  [3]: http://www.twilio.com/docs/api/rest/sending-sms
  [4]: http://twimlets.com/message
  [5]: http://twimlets.com/message?Message%5B0%5D=Hello%20World
  [Miscellaneous Role Configuration Settings]: http://msdn.microsoft.com/de-de/library/windowsazure/hh690945.aspx
  [Twilio Security Guidelines]: http://www.twilio.com/docs/security
  [Twilio HowTos und Beispielcode]: http://www.twilio.com/docs/howto
  [Twilio Quickstart Tutorials]: http://www.twilio.com/docs/quickstart
  [Twilio on GitHub]: https://github.com/twilio
  [Talk to Twilio Support]: http://www.twilio.com/help/contact
