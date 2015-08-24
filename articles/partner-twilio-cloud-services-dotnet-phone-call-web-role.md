<properties 
	pageTitle="Tätigen eines Telefonanrufs über Twilio (.NET) | Microsoft Azure" 
	description="Erfahren Sie, wie Sie mit dem Twilio API-Dienst in Azure einen Telefonanruf tätigen und eine SMS-Nachricht senden. In .NET geschriebene Codebeispiele." 
	services="" 
	documentationCenter=".net" 
	authors="devinrader" 
	manager="twilio" 
	editor=""/>

<tags 
	ms.service="cloud-services" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/02/2015" 
	ms.author="microsofthelp@twilio.com"/>




# Tätigen eines Telefonanrufs mithilfe von Twilio in einer Webrolle auf Azure

In diesem Leitfaden wird veranschaulicht, wie Sie von einer in Azure gehosteten Webseite einen Anruf über Twilio tätigen können. Die Anwendung fragt den Benutzer nach Werten für den Telefonanruf, wie im folgenden Screenshot gezeigt.

![Azure-Anrufformular mit Twilio und ASP.NET][twilio_dotnet_basic_form]

## <a name="twilio-prereqs"></a>Voraussetzungen

Sie benötigen Folgendes, um den Code in diesem Artikel ausführen zu können:

1. Ein Twilio-Konto und ein Authentifizierungs-Token. Registrieren Sie sich unter [https://www.twilio.com/try-twilio][try_twilio], um mit Twilio zu beginnen. Sie finden Preisinformationen unter [http://www.twilio.com/pricing][twilio_pricing]. Weitere Informationen über die von Twilio bereitgestellte API finden Sie unter [http://www.twilio.com/voice/api][twilio_api].
2. Fügen Sie die Twilio .NET-Bibliothek zu Ihrer Webrolle hinzu. Siehe "Hinzufügen der Twilio-Bibliotheken zu Ihrem Webrollen-Projekt" weiter unten in diesem Artikel.

Sie sollten mit der Erstellung einer einfachen Webrolle in Azure vertraut sein.

## <a name="howtocreateform"></a>Erstellen eines Webformulars für einen Anruf

<a id="use_nuget"></a>So fügen Sie dem Webrollenprojekt die Twilio-Bibliotheken hinzu:

1.  Öffnen Sie Ihre Projektmappe in Visual Studio.
2.  Klicken Sie mit der rechten Maustaste auf **References**.
3.  Klicken Sie auf **Manage NuGet Packages**.
4.  Klicken Sie auf **Online**.
5.  Geben Sie in das Onlinesuchfeld *twilio* ein.
6.  Klicken Sie auf **Install** für das Twilio-Paket.

Der folgende Code erstellt ein Web-Formular zur Eingabe der Benutzerdaten für den Anruf. In diesem Beispiel wird eine ASP.NET-Webrolle mit dem Namen **TwilioCloud** erstellt.

    <%@ Page Title="Home Page" Language="C#" MasterPageFile="~/Site.master"
        AutoEventWireup="true" CodeBehind="Default.aspx.cs"
        Inherits="WebRole1._Default" %>

    <asp:Content ID="HeaderContent" runat="server" ContentPlaceHolderID="HeadContent">
    </asp:Content>
    <asp:Content ID="BodyContent" runat="server" ContentPlaceHolderID="MainContent">
        <div>
            <asp:BulletedList ID="varDisplay" runat="server" BulletStyle="NotSet">
            </asp:BulletedList>
        </div>
        <div>
            <p>Fill in all fields and click <b>Make this call</b>.</p>
            <div>
                To:<br /><asp:TextBox ID="toNumber" runat="server" /><br /><br />
                Message:<br /><asp:TextBox ID="message" runat="server" /><br /><br />
                <asp:Button ID="callpage" runat="server" Text="Make this call"
                    onclick="callpage_Click" />
            </div>
        </div>
    </asp:Content>

## <a id="howtocreatecode"></a>Erstellen des Codes für den Anruf
Der folgende Code wird aufgerufen wenn der Benutzer das Formular abschließt und generiert die Anrufnachricht und führt den Anruf aus. In diesem Beispiel wird der Code im oncklick-Ereignishandler der Schaltfläche im Formular ausgeführt. (Ersetzen Sie die Platzhalterwerte **accountSID** und **authToken** im folgenden Code durch Ihr Twilio-Konto und Ihr Authentifizierungs-Token.)

    using System;
    using System.Collections.Generic;
    using System.Linq;
    using System.Web;
    using System.Web.UI;
    using System.Web.UI.WebControls;
    using Twilio;

    namespace WebRole1
    {
        public partial class _Default : System.Web.UI.Page
        {
            protected void Page_Load(object sender, EventArgs e)
            {

            }

            protected void callpage_Click(object sender, EventArgs e)
            {
                // Call porcessing happens here.

                // Use your account SID and authentication token instead of
                // the placeholders shown here.
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";

                // Instantiate an instance of the Twilio client.
                TwilioRestClient client;
                client = new TwilioRestClient(accountSID, authToken);

                // Retrieve the account, used later to retrieve the
                Twilio.Account account = client.GetAccount();
                string APIversuion = client.ApiVersion;
                string TwilioBaseURL = client.BaseUrl;

                this.varDisplay.Items.Clear();
                if (this.toNumber.Text == "" || this.message.Text == "")
                {
                    this.varDisplay.Items.Add(
                            "You must enter a phone number and a message.");
                }
                else
                {
                    // Retrieve the values entered by the user.
                    string to = this.toNumber.Text;
                    string myMessage = this.message.Text;

                    // Create a URL using the Twilio message and the user-entered
                    // text. You must replace spaces in the user's text with '%20'
                    // to make the text suitable for a URL.
                    String Url = "http://twimlets.com/message?Message%5B0%5D="
                            + myMessage.Replace(" ", "%20");

                    // Display the endpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Twilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values.                    
                    options.From = "+14155992671";
                    options.To = to;
                    options.Url = Url;

                    // Place the call.
                    var call = client.InitiateOutboundCall(options);
                    this.varDisplay.Items.Add("Call status: " + call.Status);
                }
            }
        }
    }

Der Anruf wird ausgeführt, und der Twilio-Endpunkt, die API-Version und der Anrufstatus werden angezeigt. Der folgende Screenshot zeigt die Ausgabe bei einer Beispielausführung.

![Azure-Anrufantwort mit Twilio und ASP.NET][twilio_dotnet_basic_form_output]

Weitere Informationen zu TwiML finden Sie unter [http://www.twilio.com/docs/api/twiml][twiml]. Weitere Informationen zu &lt;Say&gt und anderen Twilio-Verben finden Sie unter [http://www.twilio.com/docs/api/twiml/say][twilio_say].

## <a id="nextsteps"></a>Nächste Schritte
Dieser Code demonstriert die allgemeinen Funktionen für die Verwendung von Twilio in einer ASP.NET-Webrolle in Azure. Bevor Sie dieses Beispiel in einer Produktionsumgebung bereitstellen, sollten Sie einige Funktionen zur Fehlerbehandlung oder andere Features hinzufügen. Beispiel:

* Anstelle eines Web-Formulars könnten Sie Azure-Blob-Speicher oder eine Azure SQL-Datenbankinstanz zum Speichern von Telefonnummern und Anruftexten verwenden. Weitere Informationen zur Verwendung von Blobs in Azure finden Sie unter [Verwenden des Azure Blob-Speicherdiensts in .NET][howto_blob_storage_dotnet]. Weitere Informationen zur Verwendung von SQL-Datenbanken finden Sie unter [Verwenden der Azure SQL-Datenbank in .NET-Anwendungen][howto_sql_azure_dotnet].
* Sie können RoleEnvironment.getConfigurationSettings verwenden, um die Twilio-Konto-ID und das Authentifizierungs-Token aus den Konfigurationseinstellungen Ihrer Anwendung abzurufen, anstelle diese Werte in Ihr Formular fest einzuprogrammieren. Weitere Informationen zur RoleEnvironment-Klasse finden Sie unter [Microsoft.WindowsAzure.ServiceRuntime-Namespace][azure_runtime_ref_dotnet].
* Lesen Sie die Twilio-Sicherheitsrichtlinien unter [https://www.twilio.com/docs/security][twilio_docs_security].
* Weitere Informationen zu Twilio finden Sie unter [https://www.twilio.com/docs][twilio_docs].

##<a name="seealso"></a>Weitere Informationen
* [Verwenden von Twilio für Telefonie- und SMS-Funktionen aus Azure](twilio-dotnet-how-to-use-for-voice-sms.md)

[twilio_pricing]: http://www.twilio.com/pricing
[try_twilio]: http://www.twilio.com/try-twilio
[twilio_api]: http://www.twilio.com/voice/api
[verify_phone]: https://www.twilio.com/user/account/phone-numbers/verified#

[twilio_dotnet_basic_form]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
[twilio_dotnet_basic_form_output]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png

[twiml]: http://www.twilio.com/docs/api/twiml



[howto_twilio_voice_sms_dotnet]: /develop/net/how-to-guides/twilio/

[howto_blob_storage_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/blob-storage/

[howto_sql_azure_dotnet]: https://www.windowsazure.com/develop/net/how-to-guides/sql-database/


[twilio_docs_security]: http://www.twilio.com/docs/security
[twilio_docs]: http://www.twilio.com/docs
[twilio_say]: http://www.twilio.com/docs/api/twiml/say


[azure_runtime_ref_dotnet]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx

<!---HONumber=August15_HO7-->