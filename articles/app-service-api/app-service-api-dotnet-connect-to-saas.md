<properties 
	pageTitle="Benutzerdefinierte ASP.NET-API-App für SaaS-Connector in Azure App Service" 
	description="Erfahren Sie, wie Sie Code schreiben, der zum Herstellen einer Verbindung mit einer SaaS-Plattform aus einer API-App dient und wie die API-App in einem .NET-Client aufgerufen wird" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/01/2015" 
	ms.author="tdykstra"/>

# Verbinden mit einer SaaS-Plattform über eine ASP.NET-API-App in Azure App Service

## Übersicht

In diesem Tutorial wird gezeigt, wie eine [API-App](app-service-api-apps-why-best-platform.md) programmiert und konfiguriert wird, die eine Verbindung mit einer [SaaS-Plattform (Software-as-a-Service)](../app-service/app-service-authentication-overview.md#obotosaas) mithilfe des [App Service-API-App SDK für .NET](http://www.nuget.org/packages/Microsoft.Azure.AppService.ApiApps.Service/) herstellt. Das Tutorial veranschaulicht auch, wie die API-App aus einem .NET-Client mithilfe des [App Service SDK für .NET](http://www.nuget.org/packages/Microsoft.Azure.AppService) aufgerufen wird. Am Ende des Tutorials verfügen Sie über einen .NET-Konsolen-App-Client, der eine .NET API-App aufruft, die in Azure App Service ausgeführt wird. Die API-App ruft die Dropbox-API auf und gibt eine Liste mit Dateien und Ordnern im Dropbox-Konto des Benutzers zurück.

Als Alternative zum Schreiben von Code, der eine SaaS-API direkt aus einer benutzerdefinierten API-Anwendung aufruft, können Sie eine vorgefertigte [Connector-API-App](../app-service-logic/app-service-logic-what-are-biztalk-api-apps.md) aufrufen. Informationen hierzu finden Sie unter [Bereitstellen und Konfigurieren einer SaaS-Connector-API-App](app-service-api-connnect-your-app-to-saas-connector.md).

In diesem Tutorial werden die folgenden Schritte behandelt:

* Erstellen eines API-App-Projekts in Visual Studio 
* Konfigurieren der Datei *apiapp.json*, um der API-App die Verbindung mit dem Dropbox-Dienst zu ermöglichen
* Hinzufügen von Code, der Dropbox aufruft und die Ergebnisse zurückgibt
* Erstellen einer neuen API-App in Azure
* Bereitstellen des Projekts für die API-App
* Konfigurieren der API-App
* Konfigurieren des Gateways
* Erstellen eines Testclients
* Ausführen des Testclients

## Voraussetzungen

Für das Tutorial wird Folgendes angenommen:

* Sie haben die Tutorials [Erstellen einer API-App](app-service-dotnet-create-api-app.md) und [Bereitstellen einer API-App](app-service-dotnet-deploy-api-app.md) abgeschlossen.
  
* Sie haben ein grundlegendes Verständnis der Azure App Service-Gatewayarchitektur für die Authentifizierung, wie unter [Authentifizierung für API-Apps und mobile Apps in Azure App Service](app-service-authentication-overview.md) erläutert.

* Sie können mit API-Apps im Azure-Vorschauportal arbeiten, wie unter [Navigieren zu den Blättern "API-App" und "Gateway"](app-service-api-manage-in-portal.md#navigate) erläutert.

## Erstellen des API-App-Projekts
 
Wenn Sie in den Anweisungen aufgefordert werden, einen Namen für das Projekt einzugeben, geben Sie *SimpleDropbox* ein.

[AZURE.INCLUDE [app-service-api-create](../../includes/app-service-api-create.md)]

## Konfigurieren der Datei *apiapp.json*

Damit eine API-App ausgehende Aufrufe an eine SaaS-Plattform richten kann, muss die SaaS-Plattform in der Datei *apiapp.json* angegeben werden.

1. Öffnen Sie die Datei *apiapp.json*, und fügen Sie wie hier gezeigt eine `authentication`-Eigenschaft hinzu (Sie müssen auch ein Komma nach der vorherigen Eigenschaft hinzufügen):

		"authentication": [
		  {
		    "type": "dropbox"
		  }
		]

	Die vollständige Datei "apiapp.json" sieht in etwa so wie in diesem Beispiel aus:

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "SimpleDropBox",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "SimpleDropBox",
		    "summary": "",
		    "author": "",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    },
		    "authentication": [
		      {
		        "type": "dropbox"
		      }
		    ]
		}

2. Speichern Sie die Datei.

Das Festlegen der `authentication`-Eigenschaft hat eine Reihe von Auswirkungen:

* Es bewirkt, dass im Portal eine Benutzeroberfläche auf dem Blatt "API-App" angezeigt wird, wodurch die Eingabe der Client-ID und des geheimen Clientschlüssels für die SaaS-Plattform ermöglicht wird.

	![](./media/app-service-api-dotnet-connect-to-saas/authblade.png)

* Es ermöglicht der API-App das Abrufen des Zugriffstokens des SaaS-Anbieters aus dem Gateway, um es zu verwenden, wenn die API des SaaS-Anbieters aufgerufen wird.

Die `authentication`-Eigenschaft ist ein Array, doch diese Preview-Version unterstützt nicht das Angeben mehrerer Anbieter.

Eine Liste der unterstützten Plattformen finden Sie unter [Erhalten der Zustimmung des Benutzers für den Zugriff auf andere SaaS-Plattformen](../app-service/app-service-authentication-overview.md#obotosaas).

Sie können auch wie im folgenden Beispiel Bereiche angeben:

		"authentication": [
		  {
		    "type": "google",
		    "scopes": ["https://www.googleapis.com/auth/userinfo.email", "https://www.googleapis.com/auth/userinfo.profile"]
		  }
		]

Verfügbare Bereiche werden von jedem SaaS-Anbieter definiert und sind im Entwicklerportal des Anbieters zu finden.

## Hinzufügen von Code zum Aufrufen von Dropbox

1. Installieren Sie das NuGet-Paket [DropboxRestAPI](https://www.nuget.org/packages/DropboxRestAPI) im Projekt "SimpleDropbox".

	* Klicken Sie im Menü **Extras** auf **NuGet-Paket-Manager > Paket-Manager-Konsole**.

	* Geben Sie im Fenster **Paket-Manager-Konsole** diesen Befehl ein:
	 
			install-package DropboxRestAPI  

1. Öffnen Sie *Controllers\ValuesController.cs*, und ersetzen Sie den gesamten Code in der Datei durch folgenden Code.

		using DropboxRestAPI;
		using Microsoft.Azure.AppService.ApiApps.Service;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace SimpleDropBox2.Controllers
		{
		    public class ValuesController : ApiController
		    {
		        public async Task<IEnumerable<string>> Get()
		        {
		            // Retrieve the token from the gateway
		            var runtime = Runtime.FromAppSettings(Request);
		            var dropboxTokenResult = await runtime.CurrentUser.GetRawTokenAsync("dropbox");
		
		            // Create a Dropbox client object that will send the token
		            // with REST API calls to Dropbox.
		            var dropboxClient = new Client(
		                new Options
		                {
		                    AccessToken = dropboxTokenResult.Properties["AccessToken"]
		                }
		            );
		
		            // Call the Dropbox API
		            var metadata = await dropboxClient.Core.Metadata.MetadataAsync("/");
		
		            // Return a list of files and folders.
		            return metadata.contents.Select(md => md.path);
		        }
		    }
		}

	Bevor der Client diese Methode aufruft, hat sich der Benutzer bei Dropbox angemeldet und der API-App die Zustimmung für den Zugriff auf das Dropbox-Konto des Benutzers gegeben. Dropbox bestätigt diese Zustimmung, indem dem App-Service-Gateway ein Zugriffstoken bereitgestellt wird. Dieser Code ruft das Token vom Gateway ab und fügt es einem Aufruf an die Dropbox-API hinzu, wie in den Schritten 6 und 7 in der folgenden Abbildung dargestellt.

	![](./media/app-service-api-dotnet-connect-to-saas/saastoken.png)

2. Erstellen Sie das Projekt.

## Erstellen einer API-App in Azure

In diesem Abschnitt verwenden Sie den Visual Studio-Assistenten **Web veröffentlichen** zum Erstellen einer API-App in Azure. Wenn Sie aufgefordert werden, einen Namen für die API-App einzugeben, geben Sie *SimpleDropbox* ein.

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## Bereitstellen Ihres Codes

Verwenden Sie nochmals den Assistenten **Web veröffentlichen** zum Bereitstellen Ihres Codes für die neue API-App.

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## Konfigurieren der Authentifizierung für eingehende Anrufe

Damit Azure App Service authentifizierte ausgehende Aufrufe aus der API-App zulässt, muss die API-App auch anfordern, dass eingehende Aufrufe von authentifizierten Benutzern stammen. Dies ist keine allgemeine OAuth 2.0-Anforderung, jedoch eine Voraussetzung für die App-Service-Gatewayarchitektur, wie sie derzeit implementiert ist.

Die Bildschirmfotos in diesem Abschnitt zeigen die API-App "ContactsList", doch der Prozess ist für die API-App "SimpleDropbox" identisch, die Sie in diesem Tutorial erstellen.

### Konfigurieren der API-App für das Anfordern, dass eingehende Aufrufe authentifiziert sind

[AZURE.INCLUDE [app-service-api-config-auth](../../includes/app-service-api-config-auth.md)]

### Konfigurieren eines Identitätsanbieters im Gateway

[AZURE.INCLUDE [app-service-api-gateway-config-auth](../../includes/app-service-api-gateway-config-auth.md)]

## Konfigurieren der Authentifizierung für ausgehende Aufrufe

Damit Ihre API-App die Dropbox-API aufrufen kann, müssen Sie Einstellungen zwischen Ihrer API-App und einer Dropbox-App austauschen, die Sie auf der Website für Dropbox-Entwickler erstellen.

### Erstellen einer Dropbox-App auf der Website Dropbox.com

[AZURE.INCLUDE [app-service-api-create-dropbox-app](../../includes/app-service-api-create-dropbox-app.md)]

### Austauschen von Einstellungen zwischen Dropbox und Ihrer API-App

Die folgenden Schritte beziehen sich auf eine Dropbox-Connector-API-App, doch die Verfahren und Benutzeroberfläche sind für die API-App "SimpleDropbox" identisch , die Sie in diesem Tutorial erstellen.

> **Hinweis:** Wenn auf dem Blatt **Authentifizierung** für die API-App "SimpleDropbox" keine Felder für die Clientschlüssel-ID und den geheimen Clientschlüssel angezeigt werden, vergewissern Sie sich, dass Sie gemäß den Anweisungen das Gateway nach dem Bereitstellen des API-App-Projekts für die API-App neu gestartet haben. Der Wert "Dropbox" in der `authentication`-Eigenschaft der Datei *apiapp.json*, die Sie zuvor bereitgestellt haben, sorgt dafür, dass das Portal diese Felder anzeigt.

[AZURE.INCLUDE [app-service-api-exchange-dropbox-settings](../../includes/app-service-api-exchange-dropbox-settings.md)]

## Erstellen eines Testclients

In diesem Abschnitt erstellen Sie ein Konsolen-App-Projekt, das den von Visual Studio generierten Clientcode verwendet, um die API-App "SimpleDropbox" aufzurufen. Die Konsolen-App instanziiert ein Windows Forms-Browsersteuerelement zur Verarbeitung der Benutzerinteraktionen mit dem Gateway und den Webseiten für die Dropbox-Anmeldung.

### Erstellen des Projekts

1. Erstellen Sie in Visual Studio ein Konsolenanwendungsprojekt, und nennen Sie es *SimpleDropboxTest*.

2. Legen Sie einen Verweis auf "System.Windows.Forms" fest.
 
	* Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf **Verweise** und anschließend auf **Verweis hinzufügen**.

	* Aktivieren Sie das Kontrollkästchen auf der linken Seite von **System.Windows.Forms**, und klicken Sie dann auf **OK**.
	 
	![](./media/app-service-api-dotnet-connect-to-saas/setref.png)

	Die Konsolenanwendung verwendet die Windows Forms-Assembly zum Instanziieren eines Browsersteuerelements , wenn dem Benutzer ermöglicht werden soll, sich beim Gateway und bei Dropbox anzumelden.

### Hinzufügen von generiertem Clientcode

Die Bildschirmfotos in diesem Abschnitt zeigen das Projekt "ContactsList" und die API-App. Wählen Sie für dieses Tutorial jedoch das Projekt "SimpleDropboxTest" und die API-App "SimpleDropbox" aus.

[AZURE.INCLUDE [app-service-api-dotnet-add-generated-client](../../includes/app-service-api-dotnet-add-generated-client.md)]

### Hinzufügen von Code zum Aufrufen der API-App

3. Öffnen Sie die Datei *Program.cs*, und ersetzen Sie den vorhandenen Code durch folgenden Code.
		
		using Microsoft.Azure.AppService;
		using Newtonsoft.Json;
		using System;
		using System.Collections.Generic;
		using System.Diagnostics;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Windows.Forms;
		
		namespace SimpleDropboxTest
		{
		    enum Step
		    {
		        GatewayLogin,
		        GetSaaSConsentLink,
		        GetUserConsent
		    }
		
		    class Program
		    {
		        private const string GATEWAY_URL = @"{gateway url}";
		        private const string URL_TOKEN = "#token=";
		        private const string SAAS_URL = "dropbox.com";
		        private static Form frm = new Form();
		        private static string responseURL = "";
		        private static Step step;
		        [STAThread]
		        static void Main(string[] args)
		        {
		            // Create the web browser control
		            WebBrowser browser = new WebBrowser();
		            browser.Dock = DockStyle.Fill;
		            browser.Navigated += CheckResponseURL;
		            frm.Controls.Add(browser);
		            frm.Width = 640;
		            frm.Height = 480;
		
		            // Create the gateway and API app clients.
		            AppServiceClient appServiceClient = new AppServiceClient(GATEWAY_URL);
		            SimpleDropbox simpleDropboxClient = appServiceClient.CreateSimpleDropbox();
		
		            // Navigate browser to gateway login URL for configured identity provider.
		            // Identity provider for this example is Azure Active Directory.
		            step = Step.GatewayLogin;
		            browser.Navigate(string.Format(@"{0}/login/aad", GATEWAY_URL));
		            frm.ShowDialog();
		            Console.WriteLine("Logged in to gateway, response URL=" + responseURL);
		
		            // Get user ID and Zumo token from return URL, then call 
		            // the gateway URL to log in the gateway client.
		            var encodedJson = responseURL.Substring(responseURL.IndexOf(URL_TOKEN) + URL_TOKEN.Length);
		            var decodedJson = Uri.UnescapeDataString(encodedJson);
		            var result = JsonConvert.DeserializeObject<dynamic>(decodedJson);
		            string userId = result.user.userId;
		            string userToken = result.authenticationToken;
		            appServiceClient.SetCurrentUser(userId, userToken);
		
		            // Call gateway API to get consent link URL for target SaaS platform.
		            // SaaS platform for this example is Dropbox.
		            // See the tutorial for an explanation of
		            // the redirectURL parameter for GetConsentLinkAsync
		            var gatewayConsentLink = appServiceClient.GetConsentLinkAsync("SimpleDropbox", GATEWAY_URL).Result;
		            Console.WriteLine("\nGot gateway consent link, URL=" + gatewayConsentLink);
		
		            // Navigate browser to consent link URL returned from gateway.
		            // Response URL will be the SaaS logon link
		            step = Step.GetSaaSConsentLink;
		            browser.Navigate(gatewayConsentLink);
		            frm.ShowDialog();
		            Console.WriteLine("\nGot SaaS consent link response, URL=" + responseURL);
		
		            // Navigate browser to login/consent link for SaaS platform.
		            step = Step.GetUserConsent;
		            browser.Navigate(responseURL);
		            frm.ShowDialog();
		            Console.WriteLine("\nGot Dropbox login response, URL=" + responseURL);
		
		            Console.WriteLine("\nResponse from SaaS Provider");
		            var response = simpleDropboxClient.Values.Get();
		            foreach (string s in response)
		            {
		                Console.WriteLine(s);
		            }
		            Console.Read();
		        }
		
		        static void CheckResponseURL(object sender, WebBrowserNavigatedEventArgs e)
		        {
		            if ((step == Step.GatewayLogin && e.Url.AbsoluteUri.IndexOf(URL_TOKEN) > -1)
		                || (step == Step.GetSaaSConsentLink && e.Url.AbsoluteUri.IndexOf(SAAS_URL) > -1)
		                || (step == Step.GetUserConsent && e.Url.AbsoluteUri.IndexOf(GATEWAY_URL) > -1))
		            {
		                responseURL = e.Url.AbsoluteUri;
		                frm.Close();
		            }
		        }
		
		    }
		}

1. Ersetzen Sie {gateway url} durch die tatsächliche URL Ihres Gateways.
 
	Sie können die Gateway-URL auf dem Blatt **Gateway** abrufen:

	![](./media/app-service-api-dotnet-connect-to-saas/gwurl.png)

		private const string GATEWAY_URL = @"https://sd1aeb4ae60b7cb4f3d966dfa43b660.azurewebsites.net";

	> **Wichtig**: Vergewissern Sie sich, dass die Gateway-URL mit `https://` und nicht mit `http://` beginnt. **Wenn Sie "http://" aus dem Portal kopieren, müssen Sie es in "https://" ändern, wenn Sie den Eintrag in den Code einfügen.**

### Erläuterungen zum Code

Diese Konsolenanwendung ist auf eine minimale Menge von Code ausgelegt, um die Schritte zu veranschaulichen, die eine Clientanwendung durchläuft. Eine Anwendung in einer Produktionsumgebung ist in der Regel keine Konsolenanwendung und verfügt über Möglichkeiten der Fehlerbehandlung und Protokollierung.

Es folgt eine Übersicht über die Aufgaben, die der Code ausführt:

* Öffnen eines Browsers mit der Gatewayanmelde-URL für den konfigurierten Identitätsanbieter, in diesem Fall Azure Active Directory 
	 
* Verarbeiten der erwarteten Antwort-URL nach der Benutzeranmeldung, Extrahieren von Benutzer-ID und Zumo-Token und deren Bereitstellung für das App Service-Clientobjekt

* Verwenden des App Service-Clientobjekts zum Abrufen einer Gateway-URL, die für die Anmeldung und Zustimmung zum Dropbox-Link umgeleitet wird. Schritt 1 im Diagramm

* Öffnen eines Browsers mit der Zustimmungs-URL des Gateways. Der Browser wird zum Dropbox-Anmeldungs- und Zustimmungslink umgeleitet. Schritt 2 im Diagramm
	 
* Schließen des Browsers, nachdem der Benutzer sich angemeldet hat und auf Dropbox.com die Zustimmung gegeben hat. Schritt 3 im Diagramm
 
* Aufrufen der API-App auf Schritt 5 im Diagramm. (Schritt 4 erfolgt im Hintergrund zwischen Dropbox.com und Gateway. Die Schritte 6 und 7 werden von der API-App, nicht vom Client ausgeführt.)

![](./media/app-service-api-dotnet-connect-to-saas/saastoken.png)

Weitere Hinweise:

* Das `STAThread`-Attribut in der `Main`-Methode ist für das Browsersteuerelement erforderlich und steht nicht im Zusammenhang mit dem Einrichten oder Aufrufen der API-App.

* Die gezeigte Anmelde-URL für das Gateway endet für Azure Active Directory mit `/aad`.

		browser.Navigate(string.Format(@"{0}/login/aad", GATEWAY_URL));

	Hier die Werte für andere Anbieter:
	* "microsoftaccount"
	* "facebook"
	* "twitter"
	* "google"
<br/><br/>

* Der zweite Parameter für die `GetConsentLinkAsync()`-Methode ist die Rückruf-URL, zu der der Zustimmungsserver umgeleitet wird, nachdem sich der Benutzer bei Dropbox angemeldet und die Zustimmung für den Zugriff auf das Konto des Benutzers gegeben hat.

		var gatewayConsentLink = appServiceClient.GetConsentLinkAsync("SimpleDropbox", GATEWAY_URL).Result;

	Für diesen Parameter geben Sie normalerweise die nächste Webseite an, zu der der Benutzer in der Clientanwendung geleitet werden soll. Da dieser Democode in einer Konsolen-App enthalten ist, gibt es keine besuchbare Anwendungsseite, weshalb der Code die Gateway-URL als Angebotsseite angibt.

	Die Clientanwendung muss sicherstellen, dass sie zu dieser URL umgeleitet und keine Fehlermeldung angezeigt wird. Wenn der Anmeldungs-/Zustimmungsprozess keinen Erfolg hat, kann die Umleitungs-URL eine Fehlermeldung in der Abfragezeichenfolge enthalten. Weitere Informationen finden Sie im Abschnitt [Problembehandlung](#troubleshooting).

## Test

1. Führen Sie die Konsolenanwendung "SimpleDropboxTest" aus.

2. Melden Sie sich auf der ersten Anmeldeseite mit Ihren Azure Active Directory-Anmeldeinformationen (oder Anmeldeinformationen für einen anderen Identitätsanbieter wie z. B. Google oder Twitter, wenn dies im Gateway so konfiguriert ist) an.

	![](./media/app-service-api-dotnet-connect-to-saas/aadlogon.png)

3. Melden Sie sich auf der Anmeldeseite von Dropbox.com mit Ihren Dropbox-Anmeldeinformationen an.

	![](./media/app-service-api-dotnet-connect-to-saas/dblogon.png)

4. Erteilen Sie auf der Dropbox-Zustimmungsseite der Anwendung die Berechtigung, auf Ihre Daten zuzugreifen.

	![](./media/app-service-api-dotnet-connect-to-saas/dbconsent.png)

	Die Konsolen-App ruft dann die API-App auf und gibt eine Liste der Dateien in Ihrem Dropbox-Konto zurück.

	![](./media/app-service-api-dotnet-connect-to-saas/testclient.png)

## Problembehandlung

Dieser Abschnitt enthält die folgenden Themen:

* [HTTP-Fehler 405 nach Gatewayanmeldung](#405)
* [HTTP-Fehler 400 anstelle der Dropbox-Anmeldeseite](#400)
* [HTTP-Fehler 403 beim Aufrufen der API-App](#403)

### <a id="405"></a>HTTP-Fehler 405 nach Gatewayanmeldung

Falls Sie den HTTP-Fehler 405 erhalten, sobald der Code "GetConsentLinkAsync" aufruft, stellen Sie sicher, dass Sie "https://" und nicht "http://" für die Gateway-URL verwenden.

![](./media/app-service-api-dotnet-connect-to-saas/http405.png)

Sie erhalten den Fehler "405 – Nicht zulässig", da der Client versucht, eine Nicht-SSL-HTTP POST-Anforderung zu stellen, die das Gateway an *https://* umleitet. Diese Umleitung verursacht eine GET-Anforderung. Die URL zum Abrufen von eines Zustimmungslinks akzeptiert nur POST-Anforderungen.

### <a id="400"></a>HTTP-Fehler 400 anstelle der Dropbox-Anmeldeseite

Stellen Sie sicher, dass Sie die richtige **Client-ID** auf dem Blatt **Authentifizierung** der API-App angegeben haben und dass keine führenden oder nachgestellten Leerzeichen vorhanden sind.

### <a id="403"></a>HTTP-Fehler 403 beim Aufrufen der API-App

* Stellen Sie sicher, dass die **Zugriffsebene** der API-App auf **Öffentlich (authentifiziert)** und nicht auf **Intern** festgelegt ist.

* Stellen Sie sicher, dass Sie den richtigen **geheimen Clientschlüssel** auf dem Blatt **Authentifizierung** der API-App angegeben haben und dass keine führenden oder nachgestellten Leerzeichen vorhanden sind.

Die Umleitungs-URL nach der Dropbox-Anmeldung kann wie folgt aussehen:

	https://sd1aeb4ae60b7cb4f3d966dfa43b6607f30.azurewebsites.net/?error=RmFpbGVkIHRvIGV4Y2hhbmdlIGNvZGUgZm9yIHRva2VuLiBEZXRhaWxzOiB7ImVycm9yX2Rlc2NyaXB0aW9uIjogIkludmFsaWQgY2xpZW50X2lkIG9yIGNsaWVudF9zZWNyZXQiLCAiZXJyb3IiOiAiaW52YWxpZF9jbGllbnQifQ%3d%3d

Wenn Sie "%3d%3d" am Ende des Werts `error` der Abfragezeichenfolge entfernen, ist dies eine gültige base64-codierte Zeichenfolge. Wenn Sie die Zeichenfolge decodieren, erhalten Sie die Fehlermeldung:

	Failed to exchange code for token. Details: {"error_description": "Invalid client_id or client_secret", "error": "invalid_client"}

## Nächste Schritte

Sie haben erfahren, wie Sie eine API-App programmieren und konfigurieren, die sich mit einer SaaS-Plattform verbindet. Links zu anderen Tutorials zur Authentifizierung in API-Apps finden Sie unter [Authentifizierung für API-Apps und mobile Apps in Azure App Service – Nächste Schritte](../app-service/app-service-authentication-overview.md#next-steps).

[Azure preview portal]: https://portal.azure.com/
[Azure-Vorschauportal]: https://portal.azure.com/
[Azure portal]: https://manage.windowsazure.com/
[Azure-Portal]: https://manage.windowsazure.com/

<!----HONumber=July15_HO4-->