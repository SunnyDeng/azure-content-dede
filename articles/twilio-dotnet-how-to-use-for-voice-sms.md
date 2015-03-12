<properties 
	pageTitle="Verwenden von Twilio für Telefonie- und SMS-Funktionen (.NET) – Azure" 
	description="Erfahren Sie, wie Sie mit dem Twilio API-Dienst in Azure einen Telefonanruf tätigen und eine SMS-Nachricht senden. In .NET geschriebene Codebeispiele."
	services="" 
	documentationCenter=".net" 
	authors="devinrader" 
	manager="twilio" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="MicrosoftHelp@twilio.com"/>





<h1>Verwenden von Twilio für Telefonie- und SMS-Funktionen aus Azure</h1>

Dieser Leitfaden veranschaulicht die Ausführung allgemeiner Programmierungsaufgaben mit dem Twilio-API-Dienst in Azure. Die Szenarien behandeln das Tätigen eines Telefonanrufs und das Senden einer Kurznachricht (SMS). Weitere Informationen zu Twilio und zur Verwendung von Telefonie und SMS in Ihren Anwendungen finden Sie im Abschnitt[Nächste Schritte](#NextSteps).

<h2>Inhaltsverzeichnis</h2>
* [Was ist Twilio?](#WhatIs)
* [Twilio-Preise](#Pricing)
* [Konzepte](#Concepts)
* [Erstellen eines Twilio-Kontos](#CreateAccount)
* [Verifizieren von Telefonnummern](#VerifyPhoneNumbers)
* [Erstellen einer Azure-Anwendung](#create_app)
* [Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken](#configure_app)
* [Gewusst wie: Tätigen von ausgehenden Anrufen](#howto_make_call)
* [Gewusst wie: Senden einer SMS-Nachricht](#howto_send_sms)
* [Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website](#howto_provide_twiml_responses)
* [Gewusst wie: Verwenden zusätzlicher Twilio-Dienste](#AdditionalServices)
* [Nächste Schritte](#NextSteps)

<h2><a id="WhatIs"></a>Was ist Twilio?</h2>
Twilio ist ein leistungsstarkes und zukunftsorientiertes Tool für die Unternehmenskommunikation, mit dem Entwickler Telefonie-, VoIP- und Messagingfunktionalitäten in Anwendungen integrieren können. Die gesamte in einer cloudbasierten, globalen Umgebung benötigte Infrastruktur wird virtualisiert und über die Twilio-API-Kommunikationsplattform zur Verfügung gestellt. Anwendungen können einfach erstellt und skaliert werden. Sie profitieren von flexiblen Pay-as-you-go-Preisen und der Zuverlässigkeit der Cloud.

**Twilio Voice** ermöglicht Anwendungen das Tätigen und Entgegennehmen von Telefonanrufen. **Twilio SMS** ermöglicht Anwendungen das Senden und Empfangen von SMS-Nachrichten. **Twilio Client** ermöglicht Ihnen das Tätigen von VoIP-Anrufen von jedem Telefon, Tablet-PC oder Browser aus und unterstützt WebRTC.

<h2><a id="Pricing"></a>Twilio-Preise und -Sonderangebote</h2>
Azure-Kunden erhalten ein [Sonderangebot](http://www.twilio.com/azure): eine Twilio-Gutschrift in Höhe von 10 US-Dollar bei einem Upgrade Ihres Twilio-Kontos. Diese Twilio-Gutschrift kann für jede beliebige Twilio-Nutzung ausgegeben werden (eine Gutschrift von 10 US-Dollar reicht für den Versand von 1.000 SMS-Nachrichten oder für bis zu 1.000 eingehende Telefonminuten, abhängig vom Standort der Telefonnummer und dem Nachrichten- oder Anrufziel). Lösen Sie diese Twilio-Gutschrift ein, und legen Sie los auf [ahoy.twilio.com/azure](http://ahoy.twilio.com/azure) (in englischer Sprache).

Twilio ist ein Pay-as-you-go-Dienst, also vertragsungebunden. Es fallen keine Einrichtungsgebühren an, und Sie können Ihr Konto jederzeit schließen. Weitere Informationen finden Sie unter [Twilio-Preise](http://www.twilio.com/voice/pricing).  

<h2><a id="Concepts"></a>Konzepte</h2>
Die Twilio-API ist eine RESTful-API, die Telefonie- und SMS-Funktionen für Anwendungen bereitstellt. Clientbibliotheken stehen in mehreren Sprachen zur Verfügung; eine Liste finden Sie auf der Seite der [Twilio-API-Bibliotheken] [twilio_libraries].

Schlüsselaspekte der Twilio API sind Twilio-Verben und die Twilio Markup Language (TwiML).

<h3><a id="Verbs"></a>Twilio-Verben</h3>
Die API verwendet Twilio-Verben; so weist beispielsweise das Verb **&lt;Say&gt;** Twilio an, ein Nachricht in einem Anruf akustisch zu übermitteln. 

Nachfolgend finden Sie eine Liste mit Twilio-Verben.  Andere Verben und Funktionen werden in der [Dokumentation zur Twilio Markup Language](http://www.twilio.com/docs/api/twiml) erläutert.

* **&lt;Dial&gt;**: Verbindet den Anrufer mit einem anderen Telefon.
* **&lt;Gather&gt;**: Erfasst Ziffern, die über die Telefontasten eingegeben werden.
* **&lt;Hangup&gt;**: Beendet einen Anruf.
* **&lt;Play&gt;**: Gibt eine Audiodatei wieder.
* **&lt;Pause&gt;**: Wartet ohne Ton für eine angegebene Anzahl von Sekunden.
* **&lt;Record&gt;**: Zeichnet die Stimme des Anrufers auf und gibt eine URL einer Datei mit der Aufzeichnung zurück.
* **&lt;Redirect&gt;**: Überträgt die Steuerung eines Anrufs oder einer SMS an die TwiML an einer anderen URL.
* **&lt;Reject&gt;**: Lehnt einen bei Ihrer Twilio-Nummer eingehenden Anruf ab, ohne dass Ihnen Kosten entstehen.
* **&lt;Say&gt;**: Konvertiert Text in Sprache für einen Anruf.
* **&lt;Sms&gt;**: Sendet eine SMS-Nachricht.

<h3> <a id="TwiML"></a>TwiML</h3>
TwiML ist ein Satz XML-basierter Anweisungen auf Grundlage der Twilio-Verben, die Twilio anweisen, wie ein Anruf oder eine SMS verarbeitet werden soll.

Die TwiML im folgenden Beispiel konvertiert den Text **Hello World** in Sprache.

    <?xml version="1.0" encoding="UTF-8" ?>
    <Response>
       <Say>Hello World</Say>
    </Response>

Wenn eine Anwendung die Twilio-API aufruft, ist einer der API-Parameter die URL, die die TwiML-Antwort zurückgibt. Zu Entwicklungszwecken können Sie die von Twilio zur Verfügung gestellten URLs verwenden, um die von Ihren Anwendungen verwendeten TwiML-Antworten bereitzustellen. Sie können auch Ihre eigenen URLs hosten, um TwiML-Antworten zu generieren; eine weitere Option ist die Verwendung des **TwiMLResponse**-Objekts.

Nähere Informationen zu Twilio-Verben, ihren Attributen sowie TwiML finden Sie unter [TwiML] [twiml]. Zusätzliche Informationen zur Twilio-API finden Sie unter [Twilio-API] [twilio_api].

<h2><a id="CreateAccount"></a>Erstellen eines Twilio-Kontos</h2>
Wenn Sie bereit sind, sich ein Twilio-Konto einzurichten, melden Sie sich zum [Testen von Twilio] [try_twilio] an. Sie können mit einem kostenlosen Konto beginnen und später ein Upgrade vornehmen.

Wenn Sie sich für ein Twilio-Konto anmelden, erhalten Sie eine Konto-ID und ein Authentifizierungstoken. Beide Angaben benötigen Sie zum Tätigen von Twilio-API-Anrufen. Um den unbefugten Zugriff auf Ihr Konto zu verhindern, bewahren Sie Ihr Authentifizierungstoken sicher auf. Ihre Konto-ID und das Authentifizierungstoken können Sie auf der [Twilio-Kontoseite] [twilio_account] in den Feldern **ACCOUNT SID** bzw. **AUTH TOKEN** einsehen.

<h2><a id="VerifyPhoneNumbers"></a>Überprüfen von Telefonnummern</h2>
Verschiedene Telefonnummern müssen bei Twilio für Ihr Konto verifiziert werden. Wenn Sie beispielsweise ausgehende Telefonate tätigen möchten, muss die Telefonnummer als ausgehende Anrufer-ID bei Twilio verifiziert werden. Wenn Sie SMS-Nachrichten an eine Telefonnummer senden möchten, muss entsprechend die Telefonnummer des Empfängers bei Twilio verifiziert werden. Informationen zum Verifizieren einer Telefonnummer finden Sie unter [Verwalten von Nummern] [verify_phone]. In einigen der folgenden Codebeispiele werden Telefonnummern verwendet, die Sie bei Twilio verifizieren müssen.

Alternativ zur Verwendung einer vorhandenen Telefonnummer für Ihre Anwendungen besteht die Möglichkeit, eine Twilio-Telefonnummer zu kaufen. Informationen zum Kaufen einer Twilio-Telefonnummer finden Sie in der [Twilio-Hilfe zu Telefonnummern](https://www.twilio.com/help/faq/phone-numbers).

<h2><a id="create_app"></a>Erstellen einer Azure-Anwendung</h2>
Eine Azure-Anwendung, die eine Twilio-fähige Anwendung hostet, unterscheidet sich nicht von anderen Azure-Anwendungen. Sie fügen einfach die Twilio-.NET-Bibliothek hinzu und konfigurieren die Rolle für die Verwendung der Twilio-.NET-Bibliotheken.
Informationen zum Erstellen eines ersten Azure-Projekts finden Sie unter [Erstellen eines Azure-Projekts mit Visual Studio][vs_project].

<h2><a id="configure_app"></a>Konfigurieren einer Anwendung für die Verwendung von Twilio-Bibliotheken</h2>
Twilio stellt einen Satz .NET-Hilfsbibliotheken zur Verfügung, die verschiedene Aspekte von Twilio abdecken, um eine einfache und problemlose Interaktion mit der Twilio-REST-API und Twilio Client zum Generieren von TwiML-Antworten zu bieten.

Twilio stellt .NET-Entwicklern fünf Bibliotheken zur Verfügung:
<table border="1">
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

Die Bibliotheken können [mit der NuGet-Paket-Manager-Erweiterung installiert werden](http://www.twilio.com/docs/csharp/install), die für Visual Studio 2010 und 2012 verfügbar ist. Der Quellcode wird auf [GitHub][twilio_github_repo] gehostet; auf dieser Website steht auch ein Wiki mit einer vollständigen Dokumentation zur Verwendung der Bibliotheken zur Verfügung.

Standardmäßig installiert Microsoft Visual Studio 2010 NuGet Version 1.2. Für die Installation der Twilio-Bibliotheken wird NuGet Version 1.6 oder höher benötigt. Informationen zur Installation oder Aktualisierung von NuGet finden Sie auf [http://nuget.org/][nuget] (in englischer Sprache).

<div class="dev-callout">
<b>Hinweis</b>
<p>Um die neueste NuGet-Version zu installieren, müssen Sie zuerst die geladene Version mit dem Visual Studio-Erweiterungs-Manager deinstallieren. Hierzu müssen Sie Visual Studio als Administrator ausführen. Andernfalls ist die Schaltfläche "Deinstallieren" deaktiviert.</p>
</div>

<h3><a id="use_nuget"></a>So fügen Sie die Twilio-Bibliotheken Ihrem Visual Studio-Projekt hinzu:</h3>

1.  Öffnen Sie Ihre Projektmappe in Visual Studio.
2.  Klicken Sie mit der rechten Maustaste auf **Verweise**.
3.  Klicken Sie auf **NuGet-Pakete verwalten**
4.  Klicken Sie auf **Online**.
5.  Geben Sie in das Onlinesuchfeld *twilio* ein.
6.  Klicken Sie für das Twilio-Paket auf **Installieren**.


<h2><a id="howto_make_call"></a>Gewusst wie: Tätigen von ausgehenden Anrufen</h2>
Nachfolgend wird erläutert, wie Sie einen ausgehenden Anruf mit der **TwilioRestClient**-Klasse tätigen. Dieser Code verwendet eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort (Twilio Markup Language). Geben Sie Ihre Werte für die Telefonnummern **From** und **To** ein, und stellen Sie sicher, dass Sie die **From**-Telefonnummer für Ihr Twilio-Konto verifizieren, bevor Sie den Code ausführen.

    // Use your account SID and authentication token instead
    // of the placeholders shown here.
    string accountSID = "your_twilio_account";
    string authToken = "your_twilio_authentication_token";

    // Create an instance of the Twilio client.
    TwilioRestClient client;
    client = new TwilioRestClient(accountSID, authToken);

    // Use the Twilio-provided site for the TwiML response.
    String Url="http://twimlets.com/message";
    Url = Url + "?Message%5B0%5D=Hello%20World";

    // Instantiate the call options that are passed
    // to the outbound call
    CallOptions options = new CallOptions();

    // Set the call From, To, and URL values to use for the call.
    // This sample uses the sandbox number provided by
    // Twilio to make the call.
    options.From = "+NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = Url;

    // Make the call.
    var call = client.InitiateOutboundCall(options);

Weitere Informationen zu den an die **client.InitiateOutboundCall**-Methode übergebenen Parametern finden Sie unter [http://www.twilio.com/docs/api/rest/making-calls][twilio_rest_making_calls] (in englischer Sprache).

Wie bereits erwähnt, verwendet dieser Code eine von Twilio bereitgestellte Website für die Rückgabe der TwiML-Antwort. Stattdessen können Sie die TwiML-Antwort auch von Ihrer eigenen Website bereitstellen lassen. Weitere Informationen finden Sie unter [Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website](#howto_provide_twiml_responses).

<h2><a id="howto_send_sms"></a>Gewusst wie: Senden einer SMS-Nachricht</h2>
In der folgenden Momentaufnahme sehen Sie, wie Sie eine SMS-Nachricht über die **TwilioRestClient**-Klasse senden. Die **From**-Nummer wird von Twilio für Testkonten zum Senden von SMS-Nachrichten bereitgestellt. Die **To**-Nummer muss für Ihr Twilio-Konto verifiziert werden, bevor Sie den Code ausführen.

        // Use your account SID and authentication token instead
        // of the placeholders shown here.
        string accountSID = "your_twilio_account";
        string authToken = "your_twilio_authentication_token";

        // Create an instance of the Twilio client.
        TwilioRestClient client;
        client = new TwilioRestClient(accountSID, authToken);

        // Send an SMS message.
        SMSMessage result = client.SendSmsMessage(
            "+14155992671", "+12069419717", "This is my SMS message.");

        if (result.RestException != null)
        {
            //an exception occurred making the REST call
            string message = result.RestException.Message;
        }

<h2><a id="howto_provide_twiml_responses"></a>Gewusst wie: Bereitstellen von TwiML-Antworten von der eigenen Website</h2>
Wenn Ihre Anwendung einen Anruf an die Twilio-API initiiert - z. B. über die **client.InitiateOutboundCall**-Methode - sendet Twilio Ihre Anforderung an eine URL, von der die Rückgabe einer TwiML-Antwort erwartet wird. Das Beispiel unter [Gewusst wie: Beim Tätigen eines ausgehenden Anrufs wird](#howto_make_call) die von Twilio bereitgestellte URL [http://twimlets.com/message][twimlet_message_url] verwendet, um die Antwort zurückzugeben. 

<div class="dev-callout">
<b>Hinweis</b>
<p>TwiML wurde für die Verwendung durch Webdienste konzipiert, kann jedoch im Browser angezeigt werden. Klicken Sie z. B. auf [http://twimlets.com/message](twimlet_message_url), um ein leeres &lt;Response&gt;-Element anzuzeigen, oder klicken Sie auf [http://twimlets.com/message?Message%5B0%5D=Hello%20World](twimlet_message_url_hello_world), um ein &lt;Response&gt;-Element mit einem &lt;Say&gt;-Element anzuzeigen.</p>
</div>

Anstelle der von Twilio bereitgestellten URL können Sie auch Ihre eigene URL-Website für die Rückgabe von HTTP-Antworten erstellen. Sie können die Website mit jeder Sprache erstellen, die HTTP-Antworten zurückgibt. In diesem Thema wird davon ausgegangen, dass Sie die URL von einem generischen ASP.NET-Handler hosten.

Der folgende ASP.NET-Handler erstellt eine TwiML-Antwort, die beim Anruf**Hello World** sagt.

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

Weitere Informationen zu TwiML finden Sie auf [https://www.twilio.com/docs/api/twiml](https://www.twilio.com/docs/api/twiml) (in englischer Sprache).

Nachdem Sie die URL zur Bereitstellung von TwiML-Antworten eingerichtet haben, können Sie diese URL an die **client.InitiateOutboundCall**-Methode übergeben. Beispiel: Ihre Webanwendung "MyTwiML" ist in einem Azure-Cloud-Dienst bereitgestellt, und der Name des ASP.NET-Handlers lautet "mytwiml.ashx". In diesem Fall kann die URL wie im folgenden Codebeispiel gezeigt an **client.InitiateOutboundCall** übergeben werden:

    // Place the call From, To, and URL values into a hash map.
    // This sample uses the sandbox number provided by Twilio to make the call.
    options.From = "NNNNNNNNNN";
    options.To = "NNNNNNNNNN";
    options.Url = "http://<your_hosted_service>.cloudapp.net/MyTwiML/mytwiml.ashx";

    // Place the call.
    var call = client.InitiateOutboundCall(options);


Weitere Informationen über die Verwendung von Twilio auf Azure mit ASP.NET finden Sie unter [Tätigen eines Telefonanrufs mithilfe von Twilio in einer Webrolle auf Azure][howto_phonecall_dotnet].

[WACOM.INCLUDE [twilio_additional_services_and_next_steps](../includes/twilio_additional_services_and_next_steps.md)]





[howto_phonecall_dotnet]: ../partner-twilio-cloud-services-dotnet-phone-call-web-role/



[twimlet_message_url]: http://twimlets.com/message

[twilio_rest_making_calls]: http://www.twilio.com/docs/api/rest/making-calls

[vs_project]:http://msdn.microsoft.com/library/windowsazure/ee405487.aspx
[nuget]:http://nuget.org/
[twilio_github_repo]:https://github.com/twilio/twilio-csharp



[twilio_libraries]: https://www.twilio.com/docs/libraries
[twiml]: http://www.twilio.com/docs/api/twiml
[twilio_api]: http://www.twilio.com/api
[try_twilio]: https://www.twilio.com/try-twilio
[twilio_account]:  https://www.twilio.com/user/account
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#

<!--HONumber=46--> 
