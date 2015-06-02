<properties 
	pageTitle="Zugreifen auf Informationen in Azure Active Directory Graph (Windows Store) | Mobile Developer Center" 
	description="Erfahren Sie, wie Sie über die Graph-API in Ihrer Windows Store-Anwendung auf Azure Active Directory-Informationen zugreifen." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="multiple" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/04/2015" 
	ms.author="wesmc"/>

# Zugriff auf Azure Active Directory Graph-Informationen



[AZURE.INCLUDE [mobile-services-selector-aad-graph](../includes/mobile-services-selector-aad-graph.md)]

##Übersicht

Wie auch andere Identitätsanbieter, die mit Mobile Services angeboten werden, unterstützt der Azure Active Directory (AAD)-Anbieter eine umfassende Graph-API, die für programmgesteuerten Zugriff auf das Verzeichnis verwendet werden kann. In diesem Lernprogramm aktualisieren Sie die ToDoList-App, um das App-Erlebnis des authentifizierten Benutzers basierend auf zusätzlichen Benutzerinformationen, die Sie aus dem Verzeichnis mithilfe der [Graph-REST-API] abrufen, zu personalisieren.

Weitere Informationen zur Azure AD Graph-API finden Sie im [Azure Active Directory Graph-Teamblog].


>[AZURE.NOTE]Dieses Lernprogramm soll Ihre Kenntnisse bezüglich der Authentifizierung mit Azure Active Directory vertiefen. Es wird davon ausgegangen, dass Sie das Lernprogramm [Hinzufügen von Authentifizierung zur App] unter Verwendung des Azure Active Directory-Authentifizierungsanbieters abgeschlossen haben. In diesem Lernprogramm wird die Anwendung TodoItem weiter aktualisiert, die im Lernprogramm [Hinzufügen von Authentifizierung zur App] verwendet wurde.




##Voraussetzungen 

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie die folgenden Mobile Services-Lernprogramme abgeschlossen haben:

+ [Hinzufügen von Authentifizierung zur App]<br/>Fügt eine Anmeldeanforderung zur Beispiel-App „TodoList“ hinzu.

+ [Benutzerdefinierte API – Lernprogramm]<br/>Erläutert das Aufrufen einer benutzerdefinierten API.



## <a name="generate-key"></a>Erzeugen eines Zugriffsschlüssels für die App-Registrierung in AAD


Im Lernprogramm [Hinzufügen von Authentifizierung zur App] haben Sie eine Registrierung für die integrierte Anwendung erstellt, als Sie den Schritt [Registrieren für die Verwendung einer Azure Active Directory-Anmeldung] ausgeführt haben. In diesem Abschnitt erzeugen Sie einen Zugriffsschlüssel, der beim Lesen der Verzeichnisinformationen mit der Client-ID der integrierten Anwendung verwendet wird.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]


## <a name="create-api"></a>Erstellen einer benutzerdefinierten API „GetUserInfo“

In diesem Abschnitt erstellen Sie die benutzerdefinierte API „GetUserInfo“, welche die Azure AD Graph-API verwendet, um zusätzliche Informationen über den Benutzer aus AAD abzurufen.

Falls Sie noch nie benutzerdefinierte APIs mit Mobile Services verwendet haben, informieren Sie sich im [Benutzerdefinierte API – Lernprogramm], bevor Sie diesen Abschnitt abschließen.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt für den mobilen Dienst mit .NET-Backend, und klicken Sie auf **NuGet-Pakete verwalten**.
2. Geben Sie im Dialogfeld „NuGet-Paket-Manager“ **ADAL** in das Suchfeld ein, um die **Active Directory-Authentifizierungsbibliothek** für Ihren mobilen Dienst zu suchen und zu installieren. Dieses Lernprogramm wurde zuletzt mit der Version 3.0.110281957-alpha (Vorabversion) des ADAL-Pakets getestet.


3. Klicken Sie in Visual Studio mit der rechten Maustaste auf den Ordner **Controllers** für das mobile Dienstprojekt und dann auf **Hinzufügen**, um einen neuen **Microsoft Azure Mobile Services benutzerdefinierten Controller** namens `GetUserInfoController` hinzuzufügen. Der Client ruft diese API auf, um Benutzerinformationen aus Active Directory abzurufen.

4. Fügen Sie in der neuen Datei „GetUserInfoController.cs“ die folgenden `using`-Anweisungen hinzu.

		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using System.Globalization;
		using System.Threading.Tasks;
		using Newtonsoft.Json;
		using System.IO;

5. Fügen Sie der neuen Datei„GetUserInfoController.cs“ die folgende `UserInfo`-Klasse zum Speichern der Informationen hinzu, die wir aus AAD abrufen möchten.

	    public class UserInfo
	    {
	        public String displayName { get; set; }
	        public String streetAddress { get; set; }
	        public String city { get; set; }
	        public String state { get; set; }
	        public String postalCode { get; set; }
	        public String mail { get; set; }
	        public String[] otherMails { get; set; }
	
	        public override string ToString()
	        {
	            return "displayName : " + displayName + "\n" +
	                   "streetAddress : " + streetAddress + "\n" +
	                   "city : " + city + "\n" +
	                   "state : " + state + "\n" +
	                   "postalCode : " + postalCode + "\n" +
	                   "mail : " + mail + "\n" +
	                   "otherMails : " + string.Join(", ",otherMails);
	        }
	    }

6. Fügen Sie in „GetUserInfoController.cs“ der `GetUserInfoController`-Klasse die folgenden Membervariablen hinzu.

        private const string AadInstance = "https://login.windows.net/{0}";
        private const string GraphResourceId = "https://graph.windows.net/";
        private const string APIVersion = "?api-version=2013-04-05";

        private string tenantdomain;
        private string clientid;
        private string clientkey;
        private string token = null;


7. Fügen Sie in „GetUserInfoController.cs“ die folgende `GetAADToken`-Methode zur Klasse hinzu.

        private async Task<string> GetAADToken()
        {
            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetAADToken() : Could not retrieve mobile service app settings.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientCred);

            if (result != null)
                token = result.AccessToken;
            else
                Services.Log.Error("GetAADToken() : Failed to return a token.");

            return token;
        }

    Diese Methode verwendet die von Ihnen im [Azure-Verwaltungsportal] für den mobilen Dienst konfigurierten App-Einstellungen, um ein Token zum Zugriff auf Active Directory zu erhalten.

7. Fügen Sie in „GetUserInfoController.cs“ die folgende `GetAADUser`-Methode zur Klasse hinzu.

        private async Task<UserInfo> GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetAADUser() : No ServiceUser or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetAADUser() : Could not get AAD credientials for the logged in user.");
                return null;
            }

            if (token == null)
                await GetAADToken();

            if (token == null)
            {
                Services.Log.Error("GetAADUser() : No token.");
                return null;
            }

            // User the AAD Graph REST API to get the user's information
            string url = GraphResourceId + tenantdomain + "/users/" + clientAadCredentials.ObjectId + APIVersion;
            Services.Log.Info("GetAADUser() : Request URL : " + url);
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);
            request.Method = "GET";
            request.ContentType = "application/json";
            request.Headers.Add("Authorization", token);
            UserInfo userinfo = null;
            try
            {
                WebResponse response = await request.GetResponseAsync();
                StreamReader sr = new StreamReader(response.GetResponseStream());
                string userjson = sr.ReadToEnd();
                userinfo = JsonConvert.DeserializeObject<UserInfo>(userjson);
                Services.Log.Info("GetAADUser user : " + userinfo.ToString());
            }
            catch(Exception e)
            {
                Services.Log.Error("GetAADUser exception : " + e.Message);
            }

            return userinfo;
        }

    Diese Methode ruft die Active Directory-Objekt-ID für den autorisierten Benutzer ab und verwendet dann die Graph-REST-API zum Abrufen der Benutzerinformationen aus Active Directory.


8. Ersetzen Sie in „GetUserInfoController.cs“ die `Get`-Methode durch die folgende Methode. Diese Methode gibt die User-Entität mithilfe der Graph-REST-API aus dem Azure Active Directory zurück und erfordert, dass ein autorisierter Benutzer die API aufruft.

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public async Task<UserInfo> Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return await GetAADUser();
        }

9. Speichern Sie die Änderungen, und erstellen Sie den Dienst, um ihn auf Syntaxfehler zu überprüfen.
10. Veröffentlichen Sie das mobile Dienstprojekt in Ihrem Azure-Konto. 


## <a name="update-app"></a>Aktualisieren der App zur Verwendung von GetUserInfo

In diesem Abschnitt aktualisieren Sie die `AuthenticateAsync`-Methode, die Sie im Lernprogramm [Hinzufügen von Authentifizierung zur App] implementiert haben, um die benutzerdefinierte API aufzurufen und zusätzliche Informationen über den Benutzer aus AAD zurückzugeben.

[AZURE.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]
  


## <a name="test-app"></a>Testen der App

[AZURE.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]





##<a name="next-steps"></a>Nächste Schritte

Im nächsten Lernprogramm, [Rollenbasierte Zugriffssteuerung mit AAD in Mobile Services], verwenden Sie rollenbasierte Zugriffssteuerung mit Azure Active Directory (AAD), um die Gruppenmitgliedschaft zu prüfen, bevor Sie Zugriff gewähren.



<!-- Anchors. -->
[Generate an access key for the App registration in AAD]: #generate-key
[Create a GetUserInfo custom API]: #create-api
[Update the app to use the custom API]: #update-app
[Test the app]: #test-app
[Next Steps]: #next-steps

<!-- Images -->


<!-- URLs. -->
[Hinzufügen von Authentifizierung zur App]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Graph-REST-API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[Benutzerdefinierte API – Lernprogramm]: mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api.md
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[Registrieren für die Verwendung einer Azure Active Directory-Anmeldung]: mobile-services-how-to-register-active-directory-authentication.md
[Azure Active Directory Graph-Teamblog]: http://go.microsoft.com/fwlink/?LinkId=510536
[Get User]: http://msdn.microsoft.com/library/azure/dn151678.aspx
[Rollenbasierte Zugriffssteuerung mit AAD in Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac.md

<!--HONumber=54-->