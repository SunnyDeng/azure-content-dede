<properties urlDisplayName="Accessing Azure Active Directory Graph Information" pageTitle="Zugreifen auf Informationen in Azure Active Directory Graph (Windows Store) | Mobile Developer Center" metaKeywords="" description="Erfahren Sie, wie Sie über die Graph-API in Ihrer Windows Store-Anwendung auf Azure Active Directory-Informationen zugreifen." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Accessing Azure Active Directory Graph Information" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="wesmc" />

# Zugriff auf Azure Active Directory Graph-Informationen

[WACOM.INCLUDE [mobile-services-selector-aad-graph](../includes/mobile-services-selector-aad-graph.md)]



Wie auch andere Identitätsanbieter, die mit Mobile Services angeboten werden, unterstützt der Azure Active Directory (AAD)-Anbieter eine umfassende [Graph-Clientbibliothek], die für programmgesteuerten Zugriff auf das Verzeichnis verwendet werden kann. In diesem Lernprogramm aktualisieren Sie die ToDoList-App, um das App-Erlebnis des authentifizierten Benutzers basierend auf zusätzlichen Benutzerinformationen, die Sie aus dem Verzeichnis mithilfe der [Graph-Clientbibliothek] abrufen, zu personalisieren.

>[AZURE.NOTE] Dieses Lernprogramms soll Ihre Kenntnisse bezüglich der Authentifizierung mit Azure Active Directory vertiefen. Es wird davon ausgegangen, dass Sie das Lernprogramm [Hinzufügen von Authentifizierung zur App] unter Verwendung des Azure Active Directory-Authentifizierungsanbieters abgeschlossen haben. In diesem Lernprogramm wird die Anwendung TodoItem weiter aktualisiert, die im Lernprogramm [Hinzufügen von Authentifizierung zur App] verwendet wurde. 



In diesem Lernprogramm werden die folgenden Schritte behandelt:


1. [Erzeugen eines Zugriffsschlüssels für die App-Registrierung in AAD] 
2. [Erstellen einer benutzerdefinierten API "GetUserInfo"] 
3. [Aktualisieren der App zur Verwendung der benutzerdefinierten API]
4. [Testen der App]

##Voraussetzungen 

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie die folgenden Mobile Services-Lernprogramme abgeschlossen haben:

+ [Erste Schritte mit der Authentifizierung]<br/>Fügt eine Anmeldeanforderung zur Beispiel-App "TodoList" hinzu.

+ [Benutzerdefinierte API - Lernprogramm]<br/>Erläutert das Aufrufen einer benutzerdefinierten API. 



## <a name="generate-key"></a>Erzeugen eines Zugriffsschlüssels für die App-Registrierung in AAD


Im Lernprogramm [Hinzufügen von Authentifizierung zur App] haben Sie eine Registrierung für die integrierte Anwendung erstellt, als Sie den Schritt [Registrieren für die Verwendung einer Azure Active Directory-Anmeldung] ausgeführt haben. In diesem Abschnitt erzeugen Sie einen Schlüssel, der beim Lesen der Verzeichnisinformationen mit der Client-ID der integrierten Anwendung verwendet wird. 

[WACOM.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]


## <a name="create-api"></a>Erstellen einer benutzerdefinierten API "GetUserInfo"

In diesem Abschnitt erstellen Sie die benutzerdefinierte API "GetUserInfo", welche die [Graph-Clientbibliothek] verwendet, um zusätzliche Informationen über den Benutzer aus AAD abzurufen.

Falls Sie noch nie benutzerdefinierte APIs mit Mobile Services verwendet haben, informieren Sie sich im [Benutzerdefinierte API - Lernprogramm], bevor Sie diesen Abschnitt abschließen.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt für den mobilen Dienst mit .NET-Back-End, und klicken Sie auf **NuGet-Pakete verwalten**.
2. Geben Sie im Dialogfeld "NuGet-Paket-Manager" **ADAL** in das Suchfeld ein, um die **Active Directory-Authentifizierungsbibliothek** für Ihren mobilen Dienst zu suchen und zu installieren.
3. Installieren Sie im NuGet-Paket-Manager außerdem die **Microsoft Azure Active Directory Graph-Clientbibliothek** ür den mobilen Dienst.

4. Klicken Sie in Visual Studio mit der rechten Maustaste auf den Ordner **Controllers** für das mobile Dienstprojekt und dann auf **Hinzufügen**, um einen neuen **Microsoft Azure Mobile Services benutzerdefinierten Controller** namens `GetUserInfoController` hinzuzufügen. Der Client ruft diese API auf, um Benutzerinformationen aus Active Directory abzurufen.

5. Fügen Sie in der neuen Datei "GetUserInfoController.cs" die folgenden `using`-Anweisungen hinzu.

        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;

6. Fügen Sie in "GetUserInfoController.cs" die folgende `GetAADToken`-Methode zur Klasse hinzu.

        private string GetAADToken()
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(Services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  Services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  Services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                Services.Log.Error("GetUserInfo API: Could not retrieve AAD app settings from the mobile service configuration.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = await authContext.AcquireTokenAsync(GraphResourceId, clientid, clientCred);

            if (result != null)            
                token = result.AccessToken;

            return token;
        }

    Diese Methode verwendet die von Ihnen im [Azure-Verwaltungsportal] für den mobilen Dienst konfigurierten App-Einstellungen, um ein Token zum Zugriff auf Active Directory zu erhalten.

7. Fügen Sie in "GetUserInfoController.cs" die folgende `GetAADUser`-Methode zur Klasse hinzu.

        private User GetAADUser()
        {
            ServiceUser serviceUser = (ServiceUser)this.User;

            // Need a user
            if (serviceUser == null || serviceUser.Level != AuthorizationLevel.User)
            {
                Services.Log.Error("GetUserInfo API: No Service or wrong Authorizationlevel");
                return null;
            }

            // Get the user's AAD object id
            var idents = serviceUser.GetIdentitiesAsync().Result;
            var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
            if (clientAadCredentials == null)
            {
                Services.Log.Error("GetUserInfo API: Could not get AAD credientials for the logged in user.");
                return null;
            }

            string accesstoken = GetAADToken();
            if (accesstoken == null)
            {
                Services.Log.Error("GetUserInfo API: Failed to get an AAD access token.");
                return null;
            }

            GraphConnection graphConnection = new GraphConnection(accesstoken);
            var user = graphConnection.Get<User>(clientAadCredentials.ObjectId);

            return user;
        }

    Diese Methode ruft die Active Directory-Objekt-ID für den autorisierten Benutzer ab und verwendet dann die Graph-Clientbibliothek zum Abrufen der Benutzerinformationen aus Active Directory.


8. Ersetzen Sie in "GetUserInfoController.cs" die `Get`-Methode durch die folgende Methode. Diese Methode gibt das `User`-Objekt der Graph-Clientbibliothek zurück und erfordert einen autorisierten Benutzer zum Aufrufen der API.

        // GET api/GetUserInfo
        [AuthorizeLevel(AuthorizationLevel.User)]
        public User Get()
        {
            Services.Log.Info("Entered GetUserInfo custom controller!");
            return GetAADUser();
        }

9. Speichern Sie die Änderungen, und erstellen Sie den Dienst, um ihn auf Syntaxfehler zu überprüfen.
10. Veröffentlichen Sie das mobile Dienstprojekt in Ihrem Azure-Konto. 


## <a name="update-app"></a>Aktualisieren der App zur Verwendung von GetUserInfo

In diesem Abschnitt aktualisieren Sie die `AuthenticateAsync`-Methode, die Sie im Lernprogramm [Hinzufügen von Authentifizierung zur App] implementiert haben, um die benutzerdefinierte API aufzurufen und zusätzliche Informationen über den Benutzer aus AAD zurückzugeben. 

[WACOM.INCLUDE [mobile-services-aad-graph-info-update-app](../includes/mobile-services-aad-graph-info-update-app.md)]
  


## <a name="test-app"></a>Testen der App

[WACOM.INCLUDE [mobile-services-aad-graph-info-test-app](../includes/mobile-services-aad-graph-info-test-app.md)]





##<a name="next-steps"></a>Nächste Schritte

m nächsten Lernprogramm mit dem Titel [Rollenbasierte Zugriffssteuerung mit AAD in Mobile Services] verwenden Sie die rollenbasierte Zugriffssteuerung mit Azure Active Directory (AAD), um die Gruppenmitgliedschaft zu prüfen, bevor Sie Zugriff gewähren. 



<!-- Anchors. -->
[Erzeugen eines Zugriffsschlüssels für die App-Registrierung in AAD]: #generate-key
[Erstellen einer benutzerdefinierten API "GetUserInfo"]: #create-api
[Aktualisieren der App zur Verwendung der benutzerdefinierten API]: #update-app
[Testen der App]: #test-app
[Nächste Schritte]:#next-steps

<!-- Images -->


<!-- URLs. -->
[Hinzufügen von Authentifizierung zur App]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[So registrieren Sie sich beim Azure Active Directory]: /de-de/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Benutzerdefinierte API - Lernprogramm]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
[Speichern von Serverskripts]: /de-de/documentation/articles/mobile-services-store-scripts-source-control/
[Registrieren für die Verwendung einer Azure Active Directory-Anmeldung]: /de-de/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Graph-Clientbibliothek]: http://go.microsoft.com/fwlink/?LinkId=510536
[Get User]: http://msdn.microsoft.com/de-de/library/azure/dn151678.aspx
[Rollenbasierte Zugriffssteuerung mit AAD in Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/
