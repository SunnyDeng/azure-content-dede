<properties linkid="develop-net-how-to-twilio-phone-call" urlDisplayName="Twilio Phone Call" pageTitle="How to make a phone call from Twilio (.NET) - Azure" metaKeywords="Azure .NET Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, ASP.NET twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to make a phone call using Twilio in a web role on Azure" authors="MicrosoftHelp@twilio.com; larryf" solutions="" manager="" editor="" />

<tags ms.service="cloud-services" ms.workload="tbd" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="MicrosoftHelp@twilio.com; larryf"></tags>

# Tätigen eines Telefonanrufs mithilfe von Twilio in einer Webrolle auf Azure

In diesem Leitfaden wird veranschaulicht, wie Sie von einer in Azure gehosteten Webseite einen Anruf über Twilio tätigen können. Die Anwendung fragt den Benutzer nach Werten für den Telefonanruf, wie im folgenden Screenshot gezeigt.

![Azure-Anrufformular mit Twilio und ASP.NET][Azure-Anrufformular mit Twilio und ASP.NET]

## Inhaltsverzeichnis

-   [Voraussetzungen][Voraussetzungen]
-   [Gewusst wie: Erstellen eines Web-Formulars für den Anruf][Gewusst wie: Erstellen eines Web-Formulars für den Anruf]
-   [Gewusst wie: Erstellen des Codes für den Anruf][Gewusst wie: Erstellen des Codes für den Anruf]
-   [Nächste Schritte][Nächste Schritte]
-   [Weitere Informationen][Weitere Informationen]

## <a name="twilio-prereqs"></a>Voraussetzungen

Sie benötigen Folgendes, um den Code in diesem Artikel ausführen zu können:

1.  Ein Twilio-Konto und ein Authentifizierungs-Token. Registrieren Sie sich unter [][]<https://www.twilio.com/try-twilio></a>, um mit Twilio zu beginnen. Sie finden Preisinformationen unter [][1]<http://www.twilio.com/pricing></a>. Weitere Informationen über die von Twilio bereitgestellte API finden Sie unter [][2]<http://www.twilio.com/voice/api></a>.
2.  Verifizieren Sie Ihre Telefonnummer bei Twilio. Informationen zum Verifizieren Ihrer Telefonnummer finden Sie unter [][3]<https://www.twilio.com/user/account/phone-numbers/verified>\#</a>. Alternativ zur Verwendung einer vorhandenen Telefonnummer besteht die Möglichkeit, eine Twilio-Telefonnummer zu kaufen.
    Für dieses Beispiel verwenden Sie die Twilio-Testtelefonnummer, um eine Nachricht an die verifizierte Telefonnummer zu schicken. Sie können nur die Testtelefonnummer verwenden, um Nachrichten an verifizierte Telefonnummern zu schicken.
3.  Fügen Sie die Twilio .NET-Bibliothek zu Ihrer Webrolle hinzu. Siehe "Hinzufügen der Twilio-Bibliotheken zu Ihrem Webrollen-Projekt" weiter unten in diesem Artikel.

Sie sollten mit der Erstellung einer einfachen Webrolle in Azure vertraut sein.

## <a name="howtocreateform"></a>Gewusst wie: Erstellen eines Web-Formulars für den Anruf

### <span id="use_nuget"></span></a>So fügen Sie dem Webrollenprojekt die Twilio-Bibliotheken hinzu

1.  Öffnen Sie Ihre Projektmappe in Visual Studio.
2.  Klicken Sie mit der rechten Maustaste auf **Verweise**.
3.  Klicken Sie auf **Manage NuGet Packages**.
4.  Klicken Sie auf **Online**.
5.  Geben Sie in das Onlinesuchfeld *twilio* ein.
6.  Klicken Sie auf **Installieren** für das Twilio-Paket.

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

## <span id="howtocreatecode"></span></a>Gewusst wie: Erstellen des Codes für den Anruf

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

                    // Diplay the enpoint, API version, and the URL for the message.
                    this.varDisplay.Items.Add("Using Tilio endpoint "
                        + TwilioBaseURL);
                    this.varDisplay.Items.Add("Twilioclient API Version is "
                        + APIversuion);
                    this.varDisplay.Items.Add("The URL is " + Url);

                    // Instantiate the call options that are passed
                    // to the outbound call.
                    CallOptions options = new CallOptions();

                    // Set the call From, To, and URL values into a hash map.
                    // This sample uses the sandbox number provided by Twilio
                    // to make the call.
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

![Azure-Anrufantwort mit Twilio und ASP.NET][Azure-Anrufantwort mit Twilio und ASP.NET]

Weitere Informationen zu TwiML finden Sie unter [][4]<http://www.twilio.com/docs/api/twiml></a>. Weitere Informationen zu \<Say\> und anderen Twilio-Verben finden Sie unter [][5]<http://www.twilio.com/docs/api/twiml/say></a>.

## <span id="nextsteps"></span></a>Nächste Schritte

Dieser Code demonstriert die allgemeinen Funktionen für die Verwendung von Twilio in einer ASP.NET-Webrolle in Azure. Bevor Sie dieses Beispiel in einer Produktionsumgebung bereitstellen, sollten Sie einige Funktionen zur Fehlerbehandlung oder andere Features hinzufügen. Beispiel:

-   Anstelle eines Web-Formulars könnten Sie Azure-Blob-Speicher oder eine Azure SQL-Datenbankinstanz zum Speichern von Telefonnummern und Anruftexten verwenden. Weitere Informationen zur Verwendung von Blobs in Azure finden Sie unter [Verwenden des Azure Blob-Speicherdiensts in .NET][Verwenden des Azure Blob-Speicherdiensts in .NET]. Weitere Informationen zur Verwendung von SQL-Datenbanken finden Sie unter [Verwenden der Azure SQL-Datenbank in .NET-Anwendungen][Verwenden der Azure SQL-Datenbank in .NET-Anwendungen].
-   Sie können RoleEnvironment.getConfigurationSettings verwenden, um die Twilio-Konto-ID und das Authentifizierungs-Token aus den Konfigurationseinstellungen Ihrer Anwendung abzurufen, anstelle diese Werte in Ihr Formular fest einzuprogrammieren. Weitere Informationen zur RoleEnvironment-Klasse finden Sie unter [Microsoft.WindowsAzure.ServiceRuntime-Namespace][Microsoft.WindowsAzure.ServiceRuntime-Namespace].
-   Lesen Sie die Twilio-Sicherheitsrichtlinien unter [][6]<https://www.twilio.com/docs/security></a>.
-   Weitere Informationen zu Twilio finden Sie unter [][7]<https://www.twilio.com/docs></a>.

## <a name="seealso"></a>Weitere Informationen

-   [Verwenden von Twilio für Telefonie- und SMS-Funktionen aus Azure][Verwenden von Twilio für Telefonie- und SMS-Funktionen aus Azure]

  [Azure-Anrufformular mit Twilio und ASP.NET]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png
  [Voraussetzungen]: #twilio-prereqs
  [Gewusst wie: Erstellen eines Web-Formulars für den Anruf]: #howtocreateform
  [Gewusst wie: Erstellen des Codes für den Anruf]: #howtocreatecode
  [Nächste Schritte]: #nextsteps
  [Weitere Informationen]: #seealso
  []: http://www.twilio.com/try-twilio
  [1]: http://www.twilio.com/pricing
  [2]: http://www.twilio.com/voice/api
  [3]: https://www.twilio.com/user/account/phone-numbers/verified#
  [Azure-Anrufantwort mit Twilio und ASP.NET]: ./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png
  [4]: http://www.twilio.com/docs/api/twiml
  [5]: http://www.twilio.com/docs/api/twiml/say
  [Verwenden des Azure Blob-Speicherdiensts in .NET]: https://www.windowsazure.com/de-de/develop/net/how-to-guides/blob-storage/
  [Verwenden der Azure SQL-Datenbank in .NET-Anwendungen]: https://www.windowsazure.com/de-de/develop/net/how-to-guides/sql-database/
  [Microsoft.WindowsAzure.ServiceRuntime-Namespace]: http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx
  [6]: http://www.twilio.com/docs/security
  [7]: http://www.twilio.com/docs
  [Verwenden von Twilio für Telefonie- und SMS-Funktionen aus Azure]: ../twilio-dotnet-how-to-use-for-voice-sms/
