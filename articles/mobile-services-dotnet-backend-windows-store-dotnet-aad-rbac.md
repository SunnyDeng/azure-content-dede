<properties 
	pageTitle="Rollenbasierte Zugriffssteuerung in Mobile Services und Azure Active Directory (Windows Store) | Mobile Dev Center" 
	description="Erfahren Sie, wie Sie den Zugriff basierend auf Azure Active Directory-Rollen in Ihrer Windows Store-Anwendung steuern." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/20/2015" 
	ms.author="wesmc"/>

# Rollenbasierte Zugriffssteuerung in Mobile Services und Azure Active Directory

[AZURE.INCLUDE [mobile-services-selector-rbac](../includes/mobile-services-selector-rbac.md)]

##Übersicht

Unter rollenbasierter Zugriffssteuerung (RBAC) wird das Zuweisen von Berechtigungen für Rollen, die Benutzer innehaben können, verstanden. Damit werden gut Grenzen bezüglich dessen definiert, was bestimmte Klassen von Benutzern tun können und was nicht. In diesem Lernprogramm wird erläutert, wie Sie Azure Mobile Services eine einfache rollenbasierte Zugriffssteuerung  hinzufügen.

In diesem Lernprogramm wird die rollenbasierte Zugriffssteuerung erläutert, welche die Mitgliedschaft der einzelnen Benutzer in der Gruppe „Sales“ prüft, die in Azure Active Directory (AAD) definiert ist. Die Zugriffsprüfung erfolgt mit dem Mobile Services .NET-Back-End unter Verwendung der [Graph REST API] für Azure Active Directory. Nur Benutzer, die der Gruppe „Sales“ angehören, dürfen Daten abfragen.


>[AZURE.NOTE]Dieses Lernprogramm ist dazu vorgesehen, Ihre Kenntnisse bezüglich der Authentifizierung um Autorisierungsmethoden zu erweitern. Es wird davon ausgegangen, dass Sie zunächst das Lernprogramm [Hinzufügen von Authentifizierung zur App] unter Verwendung des Azure Active Directory-Authentifizierungsanbieters bearbeiten. In diesem Lernprogramm wird die Anwendung TodoItem weiter aktualisiert, die im Lernprogramm [Hinzufügen von Authentifizierung zur App] verwendet wurde.

##Voraussetzungen

Für dieses Lernprogramm ist Folgendes erforderlich:

* Visual Studio 2013 für Windows 8.1
* Abschluss des Lernprogramms [Hinzufügen von Authentifizierung zur App] unter Verwendung des Azure Active Directory-Authentifizierungsanbieters.

 


##Erzeugen eines Schlüssels für die integrierte Anwendung


Im Lernprogramm [Hinzufügen von Authentifizierung zur App] haben Sie eine Registrierung für die integrierte Anwendung erstellt, als Sie den Schritt [Registrieren für die Verwendung einer Azure Active Directory-Anmeldung] ausgeführt haben. In diesem Abschnitt erzeugen Sie einen Zugriffsschlüssel, der beim Lesen der Verzeichnisinformationen mit der Client-ID der integrierten Anwendung verwendet wird.

Wenn Sie das Lernprogramm [Zugriff auf Azure Active Directory Graph-Informationen] abgeschlossen haben, haben Sie diesen Schritt bereits durchgeführt und können diesen Abschnitt überspringen.

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]



##Erstellen der Gruppe „Sales“ mit Mitgliedschaften

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../includes/mobile-services-aad-rbac-create-sales-group.md)]



##Erstellen eines benutzerdefinierten Autorisierungsattributs im mobilen Dienst 

In diesem Abschnitt erstellen Sie ein neues benutzerdefiniertes Autorisierungsattribut, das verwendet werden kann, um Zugriffsprüfungen für Vorgänge im mobilen Dienst durchzuführen. Das Attribut überprüft eine Active Directory-Gruppe basierend auf dem übergebenen Rollennamen. Anschließend führt es Zugriffsprüfungen basierend auf der Gruppenmitgliedschaft durch.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt für den mobilen Dienst mit .NET-Backend, und klicken Sie auf **NuGet-Pakete verwalten**.

2. Geben Sie im Dialogfeld „NuGet-Paket-Manager“ **ADAL** in das Suchfeld ein, um die **Active Directory-Authentifizierungsbibliothek** für Ihren mobilen Dienst zu suchen und zu installieren. Dieses Lernprogramm wurde zuletzt mit der Version 3.0.110281957-alpha (Vorabversion) des ADAL-Pakets getestet.

3. Klicken Sie in Visual Studio mit der rechten Maustaste auf das mobile Dienstprojekt, und wählen Sie **Hinzufügen**, **Neuer Ordner** aus. Nennen Sie den neuen Ordner **Utilities**.

4. Klicken Sie in Visual Studio mit der rechten Maustaste auf den Ordner **Utilities**, und fügen Sie eine neue Klassendatei namens **AuthorizeAadRole.cs** hinzu.

    ![][0]

5. Fügen Sie die folgenden `using`-Anweisungen am Anfang der Datei "AuthorizeAadRole.cs" hinzu:

		using System.Net;
		using System.Net.Http;
		using System.Web.Http;
		using System.Web.Http.Controllers;
		using System.Web.Http.Filters;
		using Newtonsoft.Json;
		using Microsoft.WindowsAzure.Mobile.Service.Security;
		using Microsoft.WindowsAzure.Mobile.Service;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using System.Globalization;
		using System.IO;

6. Fügen Sie in der Datei „AuthorizeAadRole.cs“ den folgenden Aufzählungstyp zum Utilities-Namespace hinzu. In diesem Beispiel arbeiten wir nur mit der Rolle **Sales**. Die anderen sind lediglich Beispiele für Gruppen, die Sie verwenden könnten.

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

7. Fügen Sie in der Datei "AuthorizeAadRole.cs" die folgende `AuthorizeAadRole`-Klassendefinition zum Utilities-Namespace hinzu.

        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;
	        private ApiServices services = null;
	
	        // Constants used with ADAL and the Graph REST API for AAD
	        private const string AadInstance = "https://login.windows.net/{0}";
	        private const string GraphResourceId = "https://graph.windows.net/";
	        private const string APIVersion = "?api-version=2013-04-05";
	
	        // App settings pulled from the Mobile Service
	        private string tenantdomain;
	        private string clientid;
	        private string clientkey;
	        private Dictionary<int, string> groupIds = new Dictionary<int, string>();
	
	        private string token = null;

            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }

	        // private class used to serialize the Graph REST API web response
	        private class MembershipResponse
	        {
	            public bool value;
	        }

            public AadRoles Role { get; private set; }

            // Generate a local dictionary for the role group ids configured as 
            // Mobile Service app settings
            private void InitGroupIds()
            {
            }

            // Use ADAL and the authentication app settings from the Mobile Service to 
            // get an AAD access token
            private string GetAADToken()
            {
            }

            // Given an AAD user id, check membership against the group associated with the role.
            private bool CheckMembership(string memberId)
            {
            }

            // Called when the user is attempting authorization
            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }



8. Aktualisieren Sie in der Datei "AuthorizeAadRole.cs" die `InitGroupIds`-Methode in der `AuthorizeAadRole`-Klasse mit dem folgenden Code. Diese Methode erstellt eine Wörterbuchzuordnung der Gruppen-IDs für die einzelnen Rollen.

        private void InitGroupIds()
        {
            string groupId;

            if (services == null)
                return;

            if (!groupIds.ContainsKey((int)AadRoles.Sales))
            {
                if (services.Settings.TryGetValue("AAD_SALES_GROUP_ID", out groupId))
                {
                    groupIds.Add((int)AadRoles.Sales, groupId);
                }
                else
                    services.Log.Error("AAD_SALES_GROUP_ID app setting not found.");
            }
        }


9. Aktualisieren Sie in der Datei „AuthorizeAadRole.cs“ die `GetAADToken`-Methode in der `AuthorizeAadRole`-Klasse. Diese Methode verwendet die Anwendungseinstellungen, die im Mobile Service gespeichert sind, um ein Zugriffstoken für AAD aus ADAL abzurufen.

    >[AZURE.NOTE]ADAL für .NET umfasst standardmäßig einen speicherinternen Tokencache, um zusätzlichen Netzwerkverkehr mit Active Directory zu verringern. Allerdings können Sie auch eine eigene Cacheimplementierung schreiben oder die Zwischenspeicherung vollständig deaktivieren. Weitere Informationen hierzu finden Sie unter [ADAL für .NET].

        private string GetAADToken()
        {
            // Try to get the required AAD authentication app settings from the mobile service.  
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken() : Could not retrieve mobile service app settings.");
                return null;
            }

            ClientCredential clientCred = new ClientCredential(clientid, clientkey);
            string authority = String.Format(CultureInfo.InvariantCulture, AadInstance, tenantdomain);
            AuthenticationContext authContext = new AuthenticationContext(authority);
            AuthenticationResult result = authContext.AcquireTokenAsync(GraphResourceId, clientCred).Result;

            if (result != null)
                token = result.AccessToken;
            else
                services.Log.Error("GetAADToken() : Failed to return a token.");

            return token;
        }

10. Aktualisieren Sie in der Datei „AuthorizeAadRole.cs“ die `CheckMembership`-Methode in der `AuthorizeAadRole`-Klasse. Diese Methode empfängt die Objekt-Id des Benutzers. Anschließend wird mithilfe der AAD Graph-REST-API die Objekt-ID überprüft, um festzustellen, ob es eine Element-ID für die Gruppe ist, die der in der `AuthorizeAadRole`-Klasse ausgewählten Rolle zugeordnet ist.

        private bool CheckMembership(string memberId)
        {
            bool membership = false;
            string url = GraphResourceId + tenantdomain + "/isMemberOf" + APIVersion;
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create(url);

            // Use the Graph REST API to check group membership in the AAD
            try
            {
                request.Method = "POST";
                request.ContentType = "application/json";
                request.Headers.Add("Authorization", token);
                using (var sw = new StreamWriter(request.GetRequestStream()))
                {
                    // Request body must have the group id and a member id to check for membership
                    string body = String.Format(""groupId":"{0}","memberId":"{1}"",
                        groupIds[(int)Role], memberId);
                    sw.Write("{" + body + "}");
                }

                WebResponse response = request.GetResponse();
                StreamReader sr = new StreamReader(response.GetResponseStream());
                string json = sr.ReadToEnd();
                MembershipResponse membershipResponse = JsonConvert.DeserializeObject<MembershipResponse>(json);
                membership = membershipResponse.value;
            }
            catch (Exception e)
            {
                services.Log.Error("OnAuthorization() exception : " + e.Message);
            }

            return membership;
        }


11. Aktualisieren Sie in der Datei „AuthorizeAadRole.cs“ die `OnAuthorization`-Methode in der `AuthorizeAadRole`-Klasse mit dem folgenden Code. Dieser Code erwartet, dass der Benutzer, der den mobilen Dienst aufruft, von der AAD authentifiziert wurde. Anschließend wird die AAD-Objekt-IID des Benutzers abgerufen und die Mitgliedschaft in der Active Directory-Gruppe überprüft, die der Rolle entspricht.

    >[AZURE.NOTE]Die Active Directory-Gruppe kann nach dem Namen gesucht werden. In vielen Fällen ist es jedoch empfehlenswert, die Gruppen-ID als App-Einstellung des mobilen Diensts zu speichern. Der Gruppenname kann sich ändern, die ID bleibt dagegen immer gleich.

        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }

            services = new ApiServices(actionContext.ControllerContext.Configuration);

            // Check whether we are running in a mode where local host access is allowed 
            // through without authentication.
            if (!this.isInitialized)
            {
                HttpConfiguration config = actionContext.ControllerContext.Configuration;
                this.isHosted = config.GetIsHosted();
                this.isInitialized = true;
            }

            // No security when hosted locally
            if (!this.isHosted && actionContext.RequestContext.IsLocal)
            {
                services.Log.Warn("AuthorizeAadRole: Local Hosting.");
                return;
            }

            ApiController controller = actionContext.ControllerContext.Controller as ApiController;
            if (controller == null)
            {
                services.Log.Error("AuthorizeAadRole: No ApiController.");
            }

            bool isAuthorized = false;
            try
            {
                // Initialize a mapping for the group id to our enumerated type
                InitGroupIds();

                // Retrieve a AAD token from ADAL
                GetAADToken();
                if (token == null)
                {
                    services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                }
                else
                {
                    // Check group membership to see if the user is part of the group that corresponds to the role
                    if (!string.IsNullOrEmpty(groupIds[(int)Role]))
                    {
                        ServiceUser serviceUser = controller.User as ServiceUser;
                        if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                        {
                            var idents = serviceUser.GetIdentitiesAsync().Result;
                            AzureActiveDirectoryCredentials clientAadCredentials =
                                idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                            if (clientAadCredentials != null)
                            {
                                isAuthorized = CheckMembership(clientAadCredentials.ObjectId);
                            }
                        }
                    }
                }
            }
            catch (Exception e)
            {
                services.Log.Error(e.Message);
            }
            finally
            {
                if (isAuthorized == false)
                {
                    services.Log.Error("Denying access");

                    actionContext.Response = actionContext.Request
                        .CreateErrorResponse(HttpStatusCode.Forbidden,
                            "User is not logged in or not a member of the required group");
                }
            }
        }

12. Speichern Sie die Änderungen an der Datei „AuthorizeAadRole.cs“.

##Hinzufügen einer rollenbasierten Zugriffsprüfung für Datenbankvorgänge

1. Erweitern Sie in Visual Studio den Ordner **Controllers** im mobilen Dienstprojekt. Öffnen Sie „TodoItemController.cs“.

2. Fügen Sie in „TodoItemController.cs“ eine `using`-Anweisung für den Utilities-Namespace hinzu, der das benutzerdefinierte Autorisierungsattribut enthält.

        using todolistService.Utilities;

3. In „TodoItemController.cs“ können Sie das Attribut zu Ihrer Controllerklasse oder einzelnen Methoden hinzufügen, je nachdem, wie der Zugriff geprüft werden soll. Wenn die Zugriffsprüfung für alle Controllervorgänge basierend auf derselben Rolle erfolgen soll, fügen Sie das Attribut einfach zur Klasse hinzu. Für den Test in diesem Lernprogramm fügen Sie das Attribut wie folgt zur Klasse hinzu.

        [AuthorizeAadRole(AadGroups.Sales)]
        public class TodoItemController : TableController<TodoItem>

    Wenn Sie dagegen nur Einfüge-, Änderungs- und Löschvorgänge prüfen möchten, legen Sie das Attribut nur für diese Methoden fest, wie folgt:

        // PATCH tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task<TodoItem> PatchTodoItem(string id, Delta<TodoItem> patch)
        {
            return UpdateAsync(id, patch);
        }

        // POST tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

        // DELETE tables/TodoItem
        [AuthorizeAadRole(AadGroups.Sales)]
        public Task DeleteTodoItem(string id)
        {
            return DeleteAsync(id);
        }


4. Speichern Sie „TodoItemController.cs“, und erstellen Sie den mobilen Dienst, um sicherzustellen, dass keine Syntaxfehler vorhanden sind.
5. Veröffentlichen Sie den mobilen Dienst in Ihrem Azure-Konto.


##Testen des Clientzugriffs

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../includes/mobile-services-aad-rbac-test-app.md)]







<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png

<!-- URLs. -->
[Hinzufügen von Authentifizierung zur App]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[How to Register with the Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure Management Portal]: https://manage.windowsazure.com/
[Directory Sync Scenarios]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[Store Server Scripts]: mobile-services-store-scripts-source-control.md
[Registrieren für die Verwendung einer Azure Active Directory-Anmeldung]: mobile-services-how-to-register-active-directory-authentication.md
[Graph REST API]: http://msdn.microsoft.com/library/azure/hh974478.aspx
[IsMemberOf]: http://msdn.microsoft.com/library/azure/dn151601.aspx
[Zugriff auf Azure Active Directory Graph-Informationen]: mobile-services-dotnet-backend-windows-store-dotnet-aad-graph-info.md
[ADAL für .NET]: https://msdn.microsoft.com/library/azure/jj573266.aspx
<!--HONumber=54-->