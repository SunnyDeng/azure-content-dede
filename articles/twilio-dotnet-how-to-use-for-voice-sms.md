<properties linkid="develop-net-how-to-twilio-sms-service" urlDisplayName="Twilio Voice/SMS Service" pageTitle="How to Use Twilio for Voice and SMS (.NET) - Azure" metaKeywords="Azure Twilio, Azure phone calls, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to use Twilio for voice and SMS capabilities from Azure" authors="" solutions="" manager="" editor="" />

Verwenden von Twilio für Telefonie- und SMS-Funktionen aus Azure
================================================================

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem Twilio-API-Dienst in Azure. Die Szenarien behandeln das Tätigen eines Telefonanrufs und das Senden einer Kurznachricht (SMS). Weitere Informationen zu Twilio und zur Verwendung von Telefonie und SMS in Ihren Anweisungen finden Sie im Abschnitt [Nächste Schritte](#NextSteps).

Inhaltsverzeichnis
------------------

-   [Was ist Twilio?](#WhatIs)
-   [Twilio-Preise](#Pricing)
-   [Konzepte](#Concepts)
-   [Erstellen eines Twilio-Kontos](#CreateAccount)
-   [Verifizieren von Telefonnummern](#VerifyPhoneNumbers)
-   [Erstellen einer Azure-Anwendung](#create_app)
-   [Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken](#configure_app)
-   [Gewusst wie: Tätigen von ausgehenden Anrufen](#howto_make_call)
-   [Gewusst wie: Senden einer SMS-Nachricht](#howto_send_sms)
-   [Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website](#howto_provide_twiml_responses)
-   [Gewusst wie: Verwenden zusätzlicher Twilio-Dienste](#AdditionalServices)
-   [Nächste Schritte](#NextSteps)

Was ist Twilio?
---------------

Twilio ist ein leistungsstarkes und zukunftsorientiertes Tool für die Unternehmenskommunikation, mit dem Entwickler Telefonie-, VoIP- und Messagingfunktionalitäten in Anwendungen einbetten können. Die gesamte in einer cloudbasierten, globalen Umgebung benötigte Infrastruktur wird virtualisiert und über die Twilio-API-Kommunikationsplattform zur Verfügung gestellt. Anwendungen können einfach erstellt und skaliert werden. Sie profitieren von flexiblen Pay-as-you-go-Preisen und der Zuverlässigkeit der Cloud.

**Twilio Voice** ermöglicht Anwendungen das Tätigen und Entgegennehmen von Telefonanrufen. **Twilio SMS** ermöglicht Anwendungen das Senden und Empfangen von SMS-Nachrichten. **Twilio Client** ermöglicht Ihnen das Tätigen von VoIP-Anrufen von jedem Telefon, Tablet-PC oder Browser und unterstützt WebRTC.

Twilio-Preise und -Sonderangebote
---------------------------------

Azure-Kunden erhalten ein [Sonderangebot](http://www.twilio.com/azure): eine Twilio-Gutschrift in Höhe von 10 US-Dollar bei einem Upgrade Ihres Twilio-Kontos. Diese Twilio-Gutschrift kann für jede beliebige Twilio-Nutzung ausgegeben werden (eine Gutschrift von 10 US-Dollar reicht für den Versand von 1.000 SMS-Nachrichten oder für bis zu 1.000 eingehende Telefonminuten, abhängig vom Standort der Telefonnummer und dem Nachrichten- oder Anrufziel). Lösen Sie diese Twilio-Gutschrift ein, und legen Sie los auf [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure).

Twilio ist ein Pay-as-you-go-Dienst, also vertragsungebunden. Es fallen keine Einrichtungsgebühren an, und Sie können Ihr Konto jederzeit schließen. Weitere Informationen finden Sie unter [Twilio-Preise](http://www.twilio.com/voice/pricing).

Konzepte
--------

Die Twilio-API ist eine RESTful-API, die Telefonie- und SMS-Funktionen für Anwendungen bereitstellt. Clientbibliotheken stehen in mehreren Sprachen zur Verfügung; eine Liste finden Sie auf der Seite der [Twilio-API-Bibliotheken](https://www.twilio.com/docs/libraries).

Schlüsselaspekte der Twilio API sind Twilio-Verben und die Twilio Markup Language (TwiML).

### Twilio-Verben

Die API verwendet Twilio-Verben; so weist beispielsweise das Verb **&lt;Say\>** Twilio an, ein Nachricht in einem Anruf akustisch zu übermitteln.

Nachfolgend finden Sie eine Liste mit Twilio-Verben. Andere Verben und Funktionen werden in der [Dokumentation zur Twilio Markup Language](http://www.twilio.com/docs/api/twiml) erläutert.

-   **&lt;Dial\>**: Verbindet den Anrufer mit einem anderen Telefon.
-   **&lt;Gather\>**: Erfasst Ziffern, die über die Telefontasten eingegeben werden.
-   **&lt;Hangup\>**: Beendet einen Anruf.
-   **&lt;Play\>**: Gibt eine Audiodatei wieder.
-   **&lt;Pause\>**: Wartet ohne Ton für eine angegebene Anzahl von Sekunden.
-   **&lt;Record\>**: Zeichnet die Stimme des Anrufers auf und gibt eine URL einer Datei mit der Aufzeichnung zurück.
-   **&lt;Redirect\>**: Überträgt die Steuerung eines Anrufs oder einer SMS an die TwiML an einer anderen URL.
-   **&lt;Reject\>**: Lehnt einen bei Ihrer Twilio-Nummer eingehenden Anruf ab, ohne dass Ihnen Kosten entstehen.
-   **&lt;Say\>**: Konvertiert Text in Sprache für einen Anruf.
-   **&lt;Sms\>**: Sendet eine SMS-Nachricht.

### TwiML

TwiML ist ein Satz XML-basierter Anweisungen auf Grundlage der Twilio-Verben, die Twilio anweisen, wie ein Anruf oder eine SMS verarbeitet werden soll.

Die TwiML im folgenden Beispiel konvertiert den Text **Hello World** in Sprache.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Wenn eine Anwendung die Twilio-API aufruft, ist einer der API-Parameter die URL, die die TwiML-Antwort zurückgibt. Zu Entwicklungszwecken können Sie die von Twilio zur Verfügung gestellten URLs verwenden, um die von Ihren Anwendungen verwendeten TwiML-Antworten bereitzustellen. Sie können auch Ihre eigenen URLs hosten, um TwiML-Antworten zu generieren; eine weitere Option ist die Verwendung des **TwiMLResponse**-Objekts.

Nähere Informationen zu Twilio-Verben, ihren Attributen sowie TwiML finden Sie unter [TwiML](http://www.twilio.com/docs/api/twiml). Weitere Informationen zur Twilio-API finden Sie unter [Twilio-API](http://www.twilio.com/api).

Erstellen eines Twilio-Kontos
-----------------------------

Wenn Sie ein Twilio-Konto erstellen möchten, melden Sie sich bei [Try Twilio](https://www.twilio.com/try-twilio) an. Sie können mit einem kostenlosen Konto beginnen und später ein Upgrade vornehmen.

Wenn Sie sich für ein Twilio-Konto anmelden, erhalten Sie eine Konto-ID und ein Authentifizierungstoken. Beide Angaben benötigen Sie zum Tätigen von Twilio-API-Anrufen. Um den unbefugten Zugriff auf Ihr Konto zu verhindern, bewahren Sie Ihr Authentifizierungstoken sicher auf. Ihre Konto-ID und das Authentifizierungstoken können Sie auf der [Twilio-Kontoseite](https://www.twilio.com/user/account) in den Feldern **ACCOUNT SID** bzw. **AUTH TOKEN** einsehen.

Verifizieren von Telefonnummern
-------------------------------

Verschiedene Telefonnummern müssen bei Twilio für Ihr Konto verifiziert werden. Wenn Sie beispielsweise ausgehende Telefonate tätigen möchten, muss die Telefonnummer als ausgehende Anrufer-ID bei Twilio verifiziert werden. Wenn Sie SMS-Nachrichten an eine Telefonnummer senden möchten, muss entsprechend die Telefonnummer des Empfängers bei Twilio verifiziert werden. Informationen zum Verifizieren von Telefonnummern finden Sie im Abschnitt zum [Verwalten von Nummern](https://www.twilio.com/user/account/phone-numbers/verified#) auf der Twilio-Website (in englischer Sprache). In einigen der folgenden Codebeispiele werden Telefonnummern verwendet, die Sie bei Twilio verifizieren müssen.

Alternativ zur Verwendung einer vorhandenen Telefonnummer für Ihre Anwendungen besteht die Möglichkeit, eine Twilio-Telefonnummer zu kaufen. Informationen zum Kaufen einer Twilio-Telefonnummer finden Sie in der [Twilio-Hilfe zu Telefonnummern](https://www.twilio.com/help/faq/phone-numbers) (in englischer Sprache).

Erstellen einer Azure-Anwendung
-------------------------------

Ein Azure-Anwendung, die eine Twilio-fähige Anwendung hostet, unterscheidet sich nicht von anderen Azure-Anwendungen. Sie fügen einfach die Twilio-.NET-Bibliothek hinzu und konfigurieren die Rolle für die Verwendung der Twilio-.NET-Bibliotheken. Informationen zum Erstellen eines ersten Azure-Projekts finden Sie unter [Erstellen eines Azure-Projekts mit Visual Studio](http://msdn.microsoft.com/de-de/library/windowsazure/ee405487.aspx).

Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken
------------------------------------------------------------------------

Twilio stellt einen Satz .NET-Hilfsbibliotheken zur Verfügung, die verschiedene Aspekte von Twilio abdecken, um eine einfache und problemlose Interaktion mit der Twilio-REST-API und Twilio Client zum Generieren von TwiML-Antworten zu bieten.

Twilio stellt .NET-Entwicklern fünf Bibliotheken zur Verfügung:

<table  border="1">
    <tr>
        <th>Bibliothek</th>
        <th>Beschreibung</th>
    </tr>
    <tr>
        <td>Twilio.API</td>
        <td>Die Twilio-Hauptbibliothek, die die Twilio-REST-API in einer benutzerfreundlichen .NET-Bibliothek verpackt. Diese Bibliothek steht für .NET, Silverlight und Windows Phone 7 zur Verfügung.</td>
    </tr>
    <tr>
        <td>Twilio.TwiML</td>
        <td>Stellt eine für .NET geeignete Methode zum Generieren von TwiML-Markup zur Verfügung.</td>
    </tr>
    <tr>
        <td>Twilio.MVC</td>
        <td>Für Entwickler, die mit ASP.NET MVC arbeiten, enthält diese Bibliothek einen TwilioController, TwiML ActionResult und ein Anforderungsvalidierungsattribut.</td>
    </tr>
    <tr>
        <td>Twilio.WebMatrix</td>
        <td>Für Entwickler, die mit dem kostenlosen WebMatrix-Entwicklungstool von Microsoft arbeiten, enthält diese Bibliothek Razor-Syntaxhilfsprogramme für verschiedene Twilio-Aktionen.</td>
    </tr>
    <tr>
        <td>Twilio.Client.Capability</td>
        <td>Enthält den Capability-Tokengenerator zur Verwendung mit dem Twilio Client-JavaScript-SDK.</td>
    </tr>
</table>


Beachten Sie, dass alle Bibliotheken .NET 3.5, Silverlight 4 oder Windows Phone 7 oder höher erfordern.

Die Beispiele in diesem Leitfaden verwenden die Twilio.API-Bibliothek.

Die Bibliotheken können [mit der NuGet-Paket-Manager-Erweiterung installiert werden](http://www.twilio.com/docs/csharp/install), die für Visual Studio 2010 und 2012 verfügbar ist. Der Quellcode wird auf [GitHub](https://github.com/twilio/twilio-csharp) gehostet; auf dieser Website steht auch ein Wiki mit einer vollständigen Dokumentation zur Verwendung der Bibliotheken zur Verfügung.

Standardmäßig installiert Microsoft Visual Studio 2010 NuGet Version 1.2. Für die Installation der Twilio-Bibliotheken wird NuGet Version 1.6 oder höher benötigt. Informationen zur Installation oder Aktualisierung von NuGet finden Sie auf <http://nuget.org/>.

**Hinweis**

Um die neueste NuGet-Version zu installieren, müssen Sie zuerst die geladene Version mit dem Visual Studio-Erweiterungs-Manager deinstallieren. Hierzu müssen Sie Visual Studio als Administrator ausführen. Andernfalls ist die Deinstallieren-Schaltfläche deaktiviert.

### So fügen Sie die Twilio-Bibliotheken Ihrem Visual Studio-Projekt hinzu:

1.  Öffnen Sie Ihre Projektmappe in Visual Studio.
2.  Klicken Sie mit der rechten Maustaste auf **Verweise**.
3.  Klicken Sie auf **Manage NuGet Packages**.
4.  Klicken Sie auf **Online**.
5.  Geben Sie in das Onlinesuchfeld *twilio* ein.
6.  Klicken Sie auf **Installieren** für das Twilio-Paket.

Gewusst wie: Tätigen von ausgehenden Anrufen
--------------------------------------------

Nachfolgend wird erläutert, wie Sie einen ausgehenden Anruf mit der **TwilioRestClient**-Klasse tätigen. Dieser Code verwendet eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort (Twilio Markup Language). Geben Sie Ihre Werte für die Telefonnummern **From** und **To** ein, und stellen Sie sicher, dass Sie die **From**-Telefonnummer für Ihr Twilio-Konto verifizieren, bevor Sie den Code ausführen.

    // Verwenden Sie Ihre Konto-SID und Ihr Authentifizierungstoken anstelle
    // der hier gezeigten Platzhalter.
    string accountSID = "your_twilio_account";
    string authToken = "your_twilio_authentication_token";

    // Erstellen Sie eine Instanz des Twilio Client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Verwenden Sie die von Twilio bereitgestellte Website für die TwiML-Antwort.
    String Url="http://twimlets.com/message";
    Url = Url + "
    Message%5B0%5D=Hello%20World";

    // Instantiieren Sie die Anrufoptionen, die
    // an den ausgehenden Anruf übergeben werden.
    CallOptions options = new CallOptions();

    // Legen Sie die für den Anruf zu verwendenden From-, To- und URL-Werte fest.
    // In diesem Beispiel wird die von Twilio bereitgestellte
    // Sandkastennummer zum Tätigen des Anrufs verwendet.
    options.From = "+NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = Url;

    // Tätigen Sie den Anruf.
    var call = client.InitiateOutboundCall(options);

Weitere Informationen zu den an die **client.InitiateOutboundCall**-Methode übergebenen Parametern finden Sie auf <http://www.twilio.com/docs/api/rest/making-calls>.

Wie bereits erwähnt, verwendet dieser Code eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort. Stattdessen können Sie die TwiML-Antwort auch von Ihrer eigenen Website bereitstellen lassen. Weitere Informationen finden Sie unter [Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website](#howto_provide_twiml_responses).

Gewusst wie: Senden einer SMS-Nachricht
---------------------------------------

Der folgende Screenshot zeigt, wie Sie eine SMS-Nachricht mit der **TwilioRestClient**-Klasse senden. Die **From**-Nummer wird von Twilio für Testkonten zum Senden von SMS-Nachrichten bereitgestellt. Die **To**-Nummer muss für Ihr Twilio-Konto verifiziert werden, bevor Sie den Code ausführen.

        // Verwenden Sie Ihre Konto-SID und Ihr Authentifizierungstoken anstelle
        // der hier gezeigten Platzhalter.
        string accountSID = "your_twilio_account";
        string authToken = "your_twilio_authentication_token";

        // Erstellen Sie eine Instanz des Twilio Client.
        TwilioRestClient client;
        client = new TwilioRestClient(accountSID, authToken);

        // Senden Sie eine SMS-Nachricht.
        SMSMessage result = client.SendSmsMessage(
            "+14155992671", "+12069419717", "This is my SMS message.");

        if (result.RestException != null)
        {
            //beim Tätigen des REST-Anrufs ist eine Ausnahme aufgetreten
            string message = result.RestException.Message;
        }

Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website
----------------------------------------------------------------------

Wenn Ihre Anwendung einen Anruf an die Twilio-API initiiert – z. B. über die **client.InitiateOutboundCall**-Methode – sendet Twilio Ihre Anforderung an eine URL, von der die Rückgabe einer TwiML-Antwort erwartet wird. Das Beispiel unter [Gewusst wie: Tätigen eines ausgehenden Anrufs](#howto_make_call) verwendet die von Twilio bereitgestellte URL <http://twimlets.com/message> für die Rückgabe der Antwort.

**Hinweis**

TwiML wurde für die Verwendung durch Webdienste konzipiert, kann jedoch im Browser angezeigt werden. Klicken Sie z. B. auf [http://twimlets.com/message](twimlet\_message\_url), um ein leeres &lt;Response\>-Element anzuzeigen, oder klicken Sie auf [http://twimlets.com/message?Message%5B0%5D=Hello%20World](twimlet\_message\_url\_hello\_world), um ein &lt;Response\>-Element mit einem &lt;Say\>-Element anzuzeigen.

Anstelle der von Twilio bereitgestellten URL können Sie auch Ihre eigene URL-Website für die Rückgabe von HTTP-Antworten erstellen. Sie können die Website mit jeder Sprache erstellen, die HTTP-Antworten zurückgibt. In diesem Thema wird davon ausgegangen, dass Sie die URL von einem generischen ASP.NET-Handler hosten.

Der folgende ASP.NET-Handler erstellt eine TwiML-Antwort, die beim Anruf **Hello World** sagt.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.ContentEncoding = System.Text.Encoding.UTF8;
                string twiMLResponse = "<Response><Say>Hello World.</Say></Response>";
                context.Response.Write(twiMLResponse);
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }

Wie das vorstehende Beispiel zeigt, handelt es sich bei der TwiML-Antwort einfach um ein XML-Dokument. Die Twilio.TwiML-Bibliothek enthält Klassen, die TwiML für Sie generieren. Das folgende Beispiel generiert die gleiche Antwort wie oben, jedoch unter Verwendung der TwilioResponse-Klasse.

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;

    namespace WebRole1
    {
        /// <summary>
        /// Summary description for Handler1
        /// </summary>
        public class Handler1 : IHttpHandler
        {

            public void ProcessRequest(HttpContext context)
            {
                var twiml = new Twilio.TwiML.TwilioResponse();
                twiml.Say("Hello World.");

                context.Response.Clear();
                context.Response.ContentType = "text/xml";
                context.Response.Write(twiml.ToString());
                context.Response.End();
            }

            public bool IsReusable
            {
                get
                {
                    return false;
                }
            }
        }
    }

Weitere Informationen zu TwiML finden Sie auf <https://www.twilio.com/docs/api/twiml>.

Nachdem Sie die URL zur Bereitstellung von TwiML-Antworten eingerichtet haben, können Sie diese URL an die **client.InitiateOutboundCall**-Methode übergeben. Beispiel: Ihre Webanwendung "MyTwiML" ist in einem Azure-Clouddienst bereitgestellt, und der Name des ASP.NET-Handlers lautet "mytwiml.ashx". In diesem Fall kann die URL wie im folgenden Codebeispiel gezeigt an **client.InitiateOutboundCall** übergeben werden:

    // Platzieren Sie die From-, To- und URL-Werte für den Anruf in einer Hashzuordnung.
    // In diesem Beispiel wird die von Twilio bereitgestellte Sandkastennummer zum Tätigen des Anrufs verwendet.
    options.From = "NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx";

    // Tätigen Sie den Anruf.
    var call = client.InitiateOutboundCall(options);

Weitere Informationen über die Verwendung von Twilio auf Azure mit ASP.NET finden Sie unter [Tätigen eines Telefonanrufs mithilfe von Twilio in einer Webrolle auf Azure](../partner-twilio-cloud-services-dotnet-phone-call-web-role/).

[WACOM.INCLUDE [twilio\_additional\_services\_and\_next\_steps](../includes/twilio_additional_services_and_next_steps.md)]

[howto_phonecall_dotnet]: ../partner-twilio-cloud-services-dotnet-phone-call-web-role/



[twimlet_message_url]: http://twimlets.com/message

[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[vs_project]:http://msdn.microsoft.com/de-de/library/windowsazure/ee405487.aspx
[nuget]:http://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp



[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#
