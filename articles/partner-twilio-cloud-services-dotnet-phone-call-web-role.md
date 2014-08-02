<properties linkid="develop-net-how-to-twilio-phone-call" urlDisplayName="Twilio Phone Call" pageTitle="How to make a phone call from Twilio (.NET) - Azure" metaKeywords="Azure .NET Twilio, Azure Twilio, Azure phone calls, Azure twilio, Azure SMS, Azure SMS, Azure voice calls, azure voice calls, Azure text messages, Azure text messages, ASP.NET twilio Azure" description="Learn how to make a phone call and send a SMS message with the Twilio API service on Azure. Code samples written in .NET." metaCanonical="" services="" documentationCenter=".NET" title="How to make a phone call using Twilio in a web role on Azure" authors="" solutions="" manager="" editor="" />

Tätigen eines Telefonanrufs mithilfe von Twilio in einer Webrolle auf Azure
===========================================================================

In diesem Leitfaden wird veranschaulicht, wie Sie von einer in Azure gehosteten Webseite einen Anruf über Twilio tätigen können. Die Anwendung fragt den Benutzer nach Werten für den Telefonanruf, wie im folgenden Screenshot gezeigt.

![Azure-Anrufformular mit Twilio und ASP.NET](./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form.png)

InhaltInhaltsverzeichnis
------------------------

-   [Voraussetzungen](#twilio-prereqs)
-   [Gewusst wie: Erstellen eines Web-Formulars für den Anruf](#howtocreateform)
-   [Gewusst wie: Erstellen des Codes für den Anruf](#howtocreatecode)
-   [Nächste Schritte](#nextsteps)
-   [Weitere Informationen](#seealso)

VoraussetzungenVoraussetzungen
------------------------------

Sie benötigen Folgendes, um den Code in diesem Artikel ausführen zu können:

1.  Ein Twilio-Konto und ein Authentifizierungs-Token. Registrieren Sie sich unter [https://www.twilio.com/try-twilio](http://www.twilio.com/try-twilio), um mit Twilio zu beginnen. Sie finden Preisinformationen unter <http://www.twilio.com/pricing>. Weitere Informationen über die von Twilio bereitgestellte API finden Sie unter <http://www.twilio.com/voice/api>.
2.  Verifizieren Sie Ihre Telefonnummer bei Twilio. Informationen zum Verifizieren Ihrer Telefonnummer finden Sie unter <https://www.twilio.com/user/account/phone-numbers/verified#>. Alternativ zur Verwendung einer vorhandenen Telefonnummer besteht die Möglichkeit, eine Twilio-Telefonnummer zu kaufen.
     Für dieses Beispiel verwenden Sie die Twilio-Testtelefonnummer, um eine Nachricht an die verifizierte Telefonnummer zu schicken. Sie können nur die Testtelefonnummer verwenden, um Nachrichten an verifizierte Telefonnummern zu schicken.
3.  Fügen Sie die Twilio .NET-Bibliothek zu Ihrer Webrolle hinzu. Siehe "Hinzufügen der Twilio-Bibliotheken zu Ihrem Webrollen-Projekt" weiter unten in diesem Artikel.

Sie sollten mit der Erstellung einer einfachen Webrolle in Azure vertraut sein.

Erstellen eines Web-Formulars für den AnrufGewusst wie: Erstellen eines Web-Formulars für den Anruf
---------------------------------------------------------------------------------------------------

### So fügen Sie die Twilio-Bibliotheken zu Ihrem Webrollen-Projekt hinzu:

1.  Öffnen Sie Ihre Projektmappe in Visual Studio.
2.  Klicken Sie mit der rechten Maustaste auf **References**.
3.  Klicken Sie auf **Manage NuGet Packages**.
4.  Klicken Sie auf **Online**.
5.  Geben Sie *twilio* in das Online-Suchfeld ein.
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

Erstellen des CodesGewusst wie: Erstellen des Codes für den Anruf
-----------------------------------------------------------------

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
                            // Anrufverarbeitung erfolgt hier.

                // Verwenden Sie Ihre Konto-SID und Ihr Authentifizierungstoken
                // anstelle der hier gezeigten Platzhalter.
                string accountSID = "ACNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                            string authToken =  "NNNNNNNNNNNNNNNNNNNNNNNNNNNNNNNN";
                
                            // Instanz des Twilio-Clients erstellen.
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
                                // Vom Benutzer eingegebene Werte abrufen.
                    string to = this.toNumber.Text;
                                string myMessage = this.message.Text;
                
                // Erstellen einer URL aus der Twilio-Nachricht und dem Text vom Benutzer.      // Leerzeichen im Text durch '%20' ersetzen, um den 
                     // Text für URL-kompatibel zu machen.
                    String Url = "http://twimlets.com/message
                Message%5B0%5D="
                                        + myMessage.Replace(" ", "%20");
                
                                // Endpunkt, API-Version und URL für die Nachricht anzeigen.
                    this.varDisplay.Items.Add("Using Tilio endpoint "
                                    + TwilioBaseURL);
                                this.varDisplay.Items.Add("Twilioclient API Version is "
                                    + APIversuion);
                                this.varDisplay.Items.Add("The URL is " + Url);
                
                                // Anrufoptionen instanziieren, die für den
                                // ausgehenden Anruf übergeben werden.
                    CallOptions options = new CallOptions();
                
                                // Ablegen der From-, To- und URL-Werte für den Anruf in einer Hashzuordnung.
                    // Dieses Beispiel verwendet die von Twilio bereitgestellte Testnummer für den Anruf.
                    options.From = "+14155992671";
                                options.To = to;
                                options.Url = Url;
                
                                // Anruf ausführen.
                    var call = client.InitiateOutboundCall(options);
                                this.varDisplay.Items.Add("Call status: " + call.Status);
                            }
                        }
                    }
                }

Der Anruf wird ausgeführt, und der Twilio-Endpunkt, die API-Version und der Anrufstatus werden angezeigt. Der folgende Screenshot zeigt die Ausgabe bei einer Beispielausführung.

![Azure-Anrufantwort mit Twilio und ASP.NET](./media/partner-twilio-cloud-services-dotnet-phone-call-web-role/WA_twilio_dotnet_basic_form_output.png)

Weitere Informationen zu TwiML finden Sie unter <http://www.twilio.com/docs/api/twiml>. Weitere Informationen zu &lt;Say\> und anderen Twilio-Verben finden Sie unter <http://www.twilio.com/docs/api/twiml/say>.

Nächste SchritteNächste Schritte
--------------------------------

Dieser Code demonstriert die allgemeinen Funktionen für die Verwendung von Twilio in einer ASP.NET-Webrolle in Azure. Bevor Sie dieses Beispiel in einer Produktionsumgebung bereitstellen, sollten Sie einige Funktionen zur Fehlerbehandlung oder andere Features hinzufügen. Beispiel:

-   Anstelle eines Web-Formulars könnten Sie Azure-Blob-Speicher oder eine Azure SQL-Datenbankinstanz zum Speichern von Telefonnummern und Anruftexten verwenden. Weitere Informationen zur Verwendung von Blobs in Azure finden Sie unter [Verwenden des Azure Blob-Speicherdiensts in .NET](https://www.windowsazure.com/en-us/develop/net/how-to-guides/blob-storage/). Weitere Informationen zur Verwendung von SQL-Datenbanken finden Sie unter [Verwenden der Azure SQL-Datenbank in .NET-Anwendungen](https://www.windowsazure.com/en-us/develop/net/how-to-guides/sql-database/).
-   Sie können RoleEnvironment.getConfigurationSettings verwenden, um die Twilio-Konto-ID und das Authentifizierungs-Token aus den Konfigurationseinstellungen Ihrer Anwendung abzurufen, anstelle diese Werte in Ihr Formular fest einzuprogrammieren. Weitere Informationen zur RoleEnvironment-Klasse finden Sie unter [Microsoft.WindowsAzure.ServiceRuntime-Namespace](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.serviceruntime.aspx).
-   Lesen Sie die Twilio-Sicherheitsrichtlinien unter [https://www.twilio.com/docs/security](http://www.twilio.com/docs/security).
-   Weitere Informationen zu Twilio finden Sie unter [https://www.twilio.com/docs](http://www.twilio.com/docs).

Weitere InformationenWeitere Informationen
------------------------------------------

-   [Verwenden von Twilio für Telefonie- und SMS-Funktionen in einer Webrolle](/en-us/develop/net/how-to-guides/twilio/)

