<properties 
	pageTitle="Aufrufen einer API-Anwendung von einem authentifizierten Client" 
	description="Erfahren Sie, wie Sie eine Azure API-App von einem durch Azure Active Directory authentifizierten Web-App-Client aufrufen." 
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
	ms.date="07/26/2015" 
	ms.author="tdykstra"/>

# Aufrufen einer Azure API-App von einem durch Azure Active Directory authentifizierten Web-App-Client

## Übersicht

Dieses Lernprogramm zeigt Ihnen, wie eine vom Azure Active Directory (AAD) geschützte API-App von einer Web-App aufgerufen wird, die ebenfalls durch AAD geschützt ist. Wenn Sie das Lernprogramm abgeschlossen haben, verfügen Sie über eine Web-App und eine API-App, die in Ihrem Azure-Abonnement ausgeführt werden. Die Web-App wird Daten anzeigen, die sie durch den Aufruf der API-App abruft, wie im folgenden Screenshot gezeigt.

![](./media/app-service-api-authentication-client-flow/aboutpage.png)

Sie erfahren, wie AAD-Anmeldeinformationen aus der Web-App an die API-Anwendung übergeben werden, damit der Benutzer nicht erneut von der API-App dazu aufgefordert wird, sich anzumelden.

Sie führen die folgenden Schritte aus:

- Erstellen Sie eine API-App und konfigurieren Sie sie zur Verwendung mit der AAD-Authentifizierung.
- Erstellen Sie eine Web-App und konfigurieren Sie sie zur Verwendung mit der AAD-Authentifizierung.
- Fügen Sie Code zur Web-App hinzu, um die geschützte API-App aufzurufen.
- Stellen Sie die Web-App und die API-App in Azure bereit.
- Überprüfen Sie durch Testen, dass die Web-App die API-App aufrufen kann.

### Clientflussauthentifizierung

Der Code, den Sie zur Web-App hinzufügen, implementiert einen Prozess namens [Clientfluss](../app-service/app-service-authentication-overview.md#client-flow)authentifizierung. Das folgende Diagramm veranschaulicht den Prozess für das Abrufen von AAD-Zugriffstoken und das Austauschen gegen ein API-App(-Zumo)-Token.

![](./media/app-service-api-authentication-client-flow/clientflow.png)

Wenn Sie nicht mit der Rolle des API-App-Gateways bei der Authentifizierung für API-Apps vertraut sind, finden Sie Informationen unter [Authentifizierung für API- und mobile Apps](../app-service/app-service-authentication-overview.md).

## Voraussetzungen

Bevor Sie dieses Lernprogramm starten, stellen Sie sicher, dass [Visual Studio 2015](https://www.visualstudio.com/) und das [Azure-SDK für .NET](http://go.microsoft.com/fwlink/?linkid=518003) installiert sind. Die gleichen Schritte gelten auch für Visual Studio 2013.

Dieses Lernprogramm setzt voraus, dass Sie mit der Arbeit mit Webprojekten in Visual Studio vertraut sind.

## Erstellen und Sichern einer API-App mit AAD

1. Erstellen Sie ein API-App-Projekt oder laden Sie es herunter.
 
	* Um ein Projekt zu erstellen, führen Sie die Anweisungen in [Erstellen einer API-App](app-service-dotnet-create-api-app.md) aus.

	* Um ein Projekt herunterzuladen, besuchen Sie das [ContactsList GitHub-Repository](https://github.com/tdykstra/ContactsList).

	Sie haben nun ein Web-API-Projekt vorliegen, das Kontaktdaten zurückgibt.

	![](./media/app-service-api-authentication-client-flow/swaggerlocal.png)

2. Stellen Sie das API-App-Projekt in der neuen API-App in Azure bereit.

	Führen Sie die Schritte in [Bereitstellen einer API-App](app-service-dotnet-deploy-api-app.md) aus.

	Im [Azure-Vorschauportal] zeigt das Blatt **API-Definition** für die API-App jetzt die "Get"- und "Post"-Methoden aus dem Web-API-Projekt an, das Sie bereitgestellt haben.

	![](./media/app-service-api-authentication-client-flow/apiappinportal.png)

4. Sichern Sie die API-App mithilfe von Azure Active Directory (AD) als Identitätsanbieter.
 
	Führen Sie die Schritte für AAD in [Schützen einer API-App](app-service-api-dotnet-add-authentication.md) durch. Sie müssen in diesem Lernprogramm nicht den Abschnitt **Verwenden von Postman zum Senden einer Post-Anforderung** durchgehen.

	Wenn Sie fertig sind, verfügt das Blatt **Azure Active Directory** der API-App im [Azure-Vorschauportal] über die Client-ID der AAD-Anwendung und Ihren AAD-Mandanten.

	![](./media/app-service-api-authentication-client-flow/aadblade.png)

	Und die Registerkarte **Konfigurieren** der AAD-Anwendung im [Azure-Portal] verfügt über die App-ID-URL und die Antwort-URL der API-App.

	![](./media/app-service-api-authentication-client-flow/aadconfig.png)

## Erstellen und Sichern einer Web-App mit AAD

In diesem Abschnitt laden Sie ein Webprojekt herunter, das für die Authentifizierung mit AAD eingerichtet ist, und konfigurieren es. Das Projekt verfügt über eine **Info**-Seite, die Informationen zu Ansprüchen für den angemeldeten Benutzer enthält.

![](./media/app-service-api-authentication-client-flow/aboutpagestart.png)

Sie müssen diese Seite später ändern, indem Sie einen Abschnitt für die Anzeige von Kontaktinformationen, die aus der API-Anwendung abgerufen wurden, hinzufügen.

1. Herunterladen des Webprojekts aus dem [WebApp-GroupClaims-DotNet-Repository](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/)
 
2. Führen Sie die Schritte in **So führen Sie das Beispiel aus** in der [Infodatei](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/master/README.md) aus, wobei es folgende Ausnahmen gibt:
 
	* Obwohl die Infodatei angibt, dass Sie Visual Studio 2013 verwenden müssen, können Sie auch Visual Studio 2015 nutzen. 

	* Verwenden Sie die bereits erstellte AAD-Anwendung, anstatt eine neue zu erstellen.
 
	* Behalten Sie denselben **App-ID-URI** bei, der bereits für die AAD-Anwendung vorhanden ist. (Ändern Sie nicht ihn in das Format, das in der Infodatei angegeben ist.)
	
	* Ändern Sie weitere AAD-Einstellungen gemäß Anweisung. Legen Sie insbesondere die Anmelde- und Antwort-URLs auf die Basis-URL für die Beispiel-App fest.

Sie behalten den gleichen App-ID-URI, den Sie für die API-App erstellt haben, damit Sie das gleiche AAD-Zugriffstoken sowohl für die Web-App als auch die API-App verwenden können. Wenn Sie den App-ID-URI in das von der Infodatei vorgeschriebene Format geändert haben, wäre nur der Zugriff auf die Web-App, nicht aber die API-App möglich. Sie wären nicht in der Lage, das AAD-Token an das API-App-Gateway zu übergeben, um ein Zumo-Token zu erhalten, da das Gateway ein Token für einen App-ID-URI erwartet, der aus der Gateway-URL plus "/login/aad" besteht.

## Hinzufügen von generiertem Clientcode für die API-App

In diesem Abschnitt fügen Sie automatisch generierten Code für eine typisierte Schnittstelle zum Aufrufen der API-App hinzu.

8.	Klicken Sie in Visual Studio im **Projektmappen-Explorer** mit der rechten Maustaste auf das WebApp-GroupClaims-DotNet-Projekt, und klicken Sie dann auf **Hinzufügen > Azure-API-App-Client**.

9.	Wählen Sie im Dialogfeld **Microsoft Azure-API-App-Client hinzufügen** die API-Anwendung, die Sie mit AAD gesichert haben.

	Sobald der Code generiert wurde, wird im **Projektmappen-Explorer** ein neuer Ordner mit dem Namen der API-App angezeigt. Dieser Ordner enthält den Code zur Implementierung der Clientklassen und Datenmodelle.

10. Korrigieren Sie mehrdeutige Verweise, die auf den generierte Code in *ContactsList/ContactsExtensions.cs* zurückzuführen sind: Ändern Sie die beiden Instanzen von `Task.Factory.StartNew` zu `System.Threading.Tasks.Task.Factory.StartNew`.
 
## Hinzufügen von Code für den Tausch des AAD-Tokens gegen ein Zumo-Token

1.	Fügen Sie in der Datei "HomeController.cs" `using`-Anweisungen für das App Service-SDK und den JSON-Serialisierer hinzu.

		using Microsoft.Azure.AppService;
		using Newtonsoft.Json.Linq;

2. Fügen Sie Konstanten zur Gateway-URL und zum App-ID-URI der AAD-Anwendung hinzu. Stellen Sie sicher, dass die Gateway-URL Https und nicht Http ist.

		private const string GATEWAY_URL = "https://clientflowgroupaeb4ae60b7cb4f3d966dfa43b6607f30.azurewebsites.net/";
		private const string APP_ID_URI = GATEWAY_URL + "login/aad";

2.	Fügen Sie eine Methode hinzu, die ein Clientobjekt zum Aufrufen der API-App abruft.

		public async Task<AppServiceClient> GetAppServiceClient()
		{
		    var appServiceClient = new AppServiceClient(GATEWAY_URL);
		    string userObjectID = ClaimsPrincipal.Current.FindFirst
		        ("http://schemas.microsoft.com/identity/claims/objectidentifier").Value;
		
		    var authContext = new AuthenticationContext
		        (ConfigHelper.Authority, new TokenDbCache(userObjectID));
		
		    ClientCredential credential = new ClientCredential
		        (ConfigHelper.ClientId, ConfigHelper.AppKey);
		
		    // Get the AAD token.
		    AuthenticationResult result = authContext.AcquireToken(APP_ID_URI, credential);
		    var aadToken = new JObject();
		    aadToken["access_token"] = result.AccessToken;
		
		    // Send the AAD token to the gateway and get a Zumo token
		    var appServiceUser = await appServiceClient.LoginAsync
		        ("aad", aadToken).ConfigureAwait(false);
		
		    return appServiceClient;
		}

	In diesem Code löst sich `ConfigHelper.Authority` in "https://login.microsoftonline.com/{Ihr Mandant}" auf, z. B.: "https://login.microsoftonline.com/contoso.onmicrosoft.com".

2.	Fügen Code direkt vor der `return View()`-Anweisung am Ende der `About`-Methode hinzu, um die API-App aufzurufen. (Im nächsten Schritt fügen Sie Code für die `About`-Ansicht hinzu, um die zurückgegebenen Daten anzuzeigen.)

		var appServiceClient = await GetAppServiceClient();
		var client = appServiceClient.CreateContactsList();
		var contacts = client.Contacts.Get();
		ViewData["contacts"] = contacts;

3. Fügen Sie in der Datei *Views/Home/About.cshtml* Code unmittelbar nach der `h2`-Überschrift ein, um die Kontaktinformationen anzuzeigen.

		<h3>Contacts</h3>
		<table class="table table-striped table-bordered table-condensed table-hover">
		    <tr>
		        <th>Name</th>
		        <th>Email</th>
		    </tr>
		
		    @foreach (WebAppGroupClaimsDotNet.Models.Contact contact in (IList<WebAppGroupClaimsDotNet.Models.Contact>)ViewData["contacts"])
		    {
		        <tr>
		            <td>@contact.Name</td>
		            <td>@contact.EmailAddress</td>
		        </tr>
		    }
		
		</table>


3. Fügen Sie in der Datei "Web.config" der Anwendung die folgende bindende Umleitung nach dem öffnenden `<assemblyBinding>`-Tag hinzu.

		<dependentAssembly>
		  <assemblyIdentity name="System.Net.Http.Primitives" publicKeyToken="b03f5f7f11d50a3a" culture="neutral"/>
		  <bindingRedirect oldVersion="0.0.0.0-4.2.28.0" newVersion="4.2.28.0"/>
		</dependentAssembly>

	Ohne diese Bindungsumleitung schlägt die App fehl, weil das App Service-SDK eine Abhängigkeit von System.Net.Http.Primitives Version 1.5.0.0 enthält, die in diesem Projekt verwendete Version jedoch 4.2.28.0 ist.
 
3. Führen Sie die Schritte in **Bereitstellen des Beispiels in Azure** in der [Infodatei](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/) aus.

5. Führen Sie die App aus.

	![](./media/app-service-api-authentication-client-flow/homepage.png)

6. Klicken Sie auf **Anmelden**, und geben Sie dann die Anmeldeinformationen eines Benutzers in der AAD-Domäne ein, die Sie für diese Anwendung verwenden.

	![](./media/app-service-api-authentication-client-flow/signedin.png)

7. Klicken Sie auf **Info**.

	Der Code, die Sie an den Info-Controller gefügt haben und in der Ansicht ausgeführt wird, zeigt die erfolgreiche Authentifizierung für die API-App und den erfolgreichen Aufruf der Get-Methode an.

	![](./media/app-service-api-authentication-client-flow/aboutpage.png)

## Problembehandlung

### HTTP 400-Fehler 

Stellen Sie sicher, dass die Gateway-Url Https und nicht Http ist. Wenn Sie die Zeile direkt aus dem Azure-Vorschauportal kopiert haben, könnte http angegeben werden.
 
### SQL Server-Fehler

Die Anwendung erfordert Zugriff auf eine SQL Server-Datenbank, die durch die Verbindungszeichenfolge "GroupClaimContext" identifiziert wird. Stellen Sie sicher, dass die Verbindungszeichenfolge auf der bereitgestellten Website auf eine SQL-Datenbankinstanz verweist. Die richtige Verbindungszeichenfolge können Sie in der bereitgestellten Web.config-Datei oder in den Konfigurationseinstellungen der Azure-Laufzeit ablegen.

## Danksagungen

Unser Dank gilt Govind S. Yadav ([@govindsyadav](https://twitter.com/govindsyadav)) für die Hilfe beim Entwickeln dieses Lernprogramms.

## Nächste Schritte

Sie haben eine Clientflussauthentifizierung für App Service API-Apps kennengelernt. Informationen zu anderen Möglichkeiten der Authentifizierung in API-Apps finden Sie unter [Authentifizierung für API-Apps und mobile Apps](../app-service/app-service-authentication-overview.md).

[Azure-Portal]: https://manage.windowsazure.com/
[Azure-Vorschauportal]: https://portal.azure.com/

<!---HONumber=Oct15_HO1-->