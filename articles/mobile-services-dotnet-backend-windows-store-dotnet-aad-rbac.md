<properties pageTitle="Rollenbasierte Zugriffssteuerung in Mobile Services und Azure Active Directory (Windows Store) | Mobile Dev Center" description="Erfahren Sie, wie Sie den Zugriff basierend auf Azure Active Directory-Rollen in Ihrer Windows Store-Anwendung steuern." documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor="" services=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="wesmc"/>

# Rollenbasierte Zugriffssteuerung in Mobile Services und Azure Active Directory

[AZURE.INCLUDE [mobile-services-selector-rbac](../includes/mobile-services-selector-rbac.md)]

Rollenbasierte Zugriffssteuerung (RBAC) besteht im Zuweisen von Berechtigungen für Rollen, die Benutzer innehaben können, um Beschränkungen für die Möglichkeiten bestimmter Benutzer zu definieren. In diesem Lernprogramm erfahren Sie, wie Sie grundlegende RBAC für Azure Mobile Services hinzufügen können.

In diesem Lernprogramm wird die rollenbasierte Zugriffssteuerung erläutert, wobei die Mitgliedschaft der einzelnen Benutzer in der Gruppe "Sales" geprüft wird, die in Azure Active Directory (AAD) definiert ist. Die Zugriffsprüfung erfolgt mit dem Mobile Services .NET-Back-End mithilfe der [Graph-Clientbibliothek] für Azure Active Directory. Nur Benutzer, die der Gruppe "Sales" angehören, dürfen Daten abfragen.


>[AZURE.NOTE] Dieses Lernprogramm ist dazu vorgesehen, Ihre Kenntnisse bezüglich der Authentifizierung um Autorisierungsmethoden zu erweitern. Es wird davon ausgegangen, dass Sie das Lernprogramm [Hinzufügen von Authentifizierung zur App] unter Verwendung des Azure Active Directory-Authentifizierungsanbieters abgeschlossen haben. In diesem Lernprogramm wird die Anwendung TodoItem weiter aktualisiert, die im Lernprogramm [Hinzufügen von Authentifizierung zur App] verwendet wurde.

In diesem Lernprogramm werden die folgenden Schritte behandelt:

1. [Erstellen der Gruppe "Sales" mit Mitgliedschaften]
2. [Erzeugen eines Schlüssels für die integrierte Anwendung]
3. [Erstellen eines benutzerdefinierten Autorisierungsattributs]
4. [Hinzufügen einer rollenbasierten Zugriffsprüfung für Datenbankvorgänge]
5. [Testen des Clientzugriffs]

Für dieses Lernprogramm ist Folgendes erforderlich:

* Visual Studio 2013 für Windows 8.1
* Abschluss des Lernprogramms [Hinzufügen von Authentifizierung zur App] unter Verwendung des Azure Active Directory-Authentifizierungsanbieters.
* Abschluss des Lernprogramms [Speichern von Serverskripts] zur Verwendung eines Git-Repositorys zum Speichern von Serverskripts.
 


## <a name="create-group"></a>Erstellen der Gruppe "Sales" mit Mitgliedschaften

[AZURE.INCLUDE [mobile-services-aad-rbac-create-sales-group](../includes/mobile-services-aad-rbac-create-sales-group.md)]


## <a name="generate-key"></a>Erzeugen eines Schlüssels für die integrierte Anwendung


Im Lernprogramm [Hinzufügen von Authentifizierung zur App] haben Sie eine Registrierung für die integrierte Anwendung erstellt, als Sie den Schritt [Registrieren für die Verwendung einer Azure Active Directory-Anmeldung] ausgeführt haben. In diesem Abschnitt erzeugen Sie einen Zugriffsschlüssel, der beim Lesen der Verzeichnisinformationen mit der Client-ID der integrierten Anwendung verwendet wird. 

[AZURE.INCLUDE [mobile-services-generate-aad-app-registration-access-key](../includes/mobile-services-generate-aad-app-registration-access-key.md)]



## <a name="create-custom-authorization-attribute"></a>Erstellen eines benutzerdefinierten Autorisierungsattributs im mobilen Dienst 

In diesem Abschnitt erstellen Sie ein neues benutzerdefiniertes Autorisierungsattribut, das verwendet werden kann, um Zugriffsprüfungen für Vorgänge im mobilen Dienst durchzuführen. Das Attribut überprüft eine Active Directory-Gruppe basierend auf dem übergebenen Rollennamen. Anschließend führt es Zugriffsprüfungen basierend auf der Gruppenmitgliedschaft durch.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Projekt für den mobilen Dienst mit .NET-Back-End, und klicken Sie auf **NuGet-Pakete verwalten**.

2. Geben Sie im Dialogfeld "NuGet-Paket-Manager" **ADAL** in das Suchfeld ein, um die **Active Directory-Authentifizierungsbibliothek** für Ihren mobilen Dienst zu suchen und zu installieren.

3. Installieren Sie im NuGet-Paket-Manager außerdem die **Microsoft Azure Active Directory Graph-Clientbibliothek** für Ihren mobilen Dienst.


4. Klicken Sie in Visual Studio mit der rechten Maustaste auf das mobile Dienstprojekt, und wählen Sie **Hinzufügen**, **Neuer Ordner** aus. Nennen Sie den neuen Ordner **Utilities**.

5. Klicken Sie in Visual Studio mit der rechten Maustaste auf den Ordner **Utilities**, und fügen Sie eine neue Klassendatei namens **AuthorizeAadRole.cs** hinzu.

    ![][0]

6. Fügen Sie die folgenden `using`-Anweisungen am Anfang der Datei "AuthorizeAadRole.cs" hinzu. 

        using System.Net;
        using System.Net.Http;
        using System.Web.Http;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using Microsoft.Azure.ActiveDirectory.GraphClient;
        using Microsoft.WindowsAzure.Mobile.Service.Security;
        using Microsoft.WindowsAzure.Mobile.Service;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using System.Globalization;
        using System.Linq.Expressions;

7. Fügen Sie in der Datei "AuthorizeAadRole.cs" den folgenden Aufzählungstyp zum Utilities-Namespace hinzu. In diesem Beispiel arbeiten wir nur mit der Rolle **Sales**. Die anderen sind lediglich Beispiele für Gruppen, die Sie verwenden könnten.

        public enum AadRoles
        {
            Sales,
            Management,
            Development
        }

8. Fügen Sie in der Datei "AuthorizeAadRole.cs" die folgende `AuthorizeAadRole`-Klassendefinition zum Utilities-Namespace hinzu.

        [AttributeUsage(AttributeTargets.Class | AttributeTargets.Method, AllowMultiple = false, Inherited = true)]
        public class AuthorizeAadRole : AuthorizationFilterAttribute
        {
            private bool isInitialized;
            private bool isHosted;

            public AuthorizeAadRole(AadRoles role)
            {
                this.Role = role;
            }

            public AadRoles Role { get; private set; }

            public override void OnAuthorization(HttpActionContext actionContext)
            {
            }
        }

9. Fügen Sie in der Datei "AuthorizeAadRole.cs" die folgende `GetAADToken`-Methode zur  `AuthorizeAadRole`-Klasse hinzu.

    >[AZURE.NOTE] Sie sollten den Token zwischenspeichern, statt für jede Zugriffsprüfung einen neuen Token zu erstellen. Aktualisieren Sie den Zwischenspeicher, wenn bei der Verwendung des Tokens ein AccessTokenExpiredException-Fehler ausgegeben wird, wie in der [Graph-Clientbibliothek] beschrieben ist. Aus Gründen der Vereinfachung wird dies im Code unten nicht dargestellt, aber es minimiert den zusätzlichen Netzwerkverkehr mit Active Directory.  

        private string GetAADToken(ApiServices services)
        {
            const string AadInstance = "https://login.windows.net/{0}";
            const string GraphResourceId = "https://graph.windows.net";

            string tenantdomain;
            string clientid;
            string clientkey;
            string token = null;
            
            if (services == null)
                return null;

            // Try to get the AAD app settings from the mobile service.  
            if (!(services.Settings.TryGetValue("AAD_CLIENT_ID", out clientid) &
                  services.Settings.TryGetValue("AAD_CLIENT_KEY", out clientkey) &
                  services.Settings.TryGetValue("AAD_TENANT_DOMAIN", out tenantdomain)))
            {
                services.Log.Error("GetAADToken: Could not retrieve all AAD app settings from the mobile service configuration.");
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

10. Aktualisieren Sie in der Datei "AuthorizeAadRole.cs" die `OnAuthorization`-Methode in der  `AuthorizeAadRole`-Klasse mit dem folgenden Code. Dieser Code verwendet die [Graph-Clientbibliothek], um die Active Directory-Gruppe aufzurufen, die der Rolle zugeordnet ist. Anschließend wird die Benutzermitgliedschaft in dieser Gruppe überprüft, um den Benutzer zu autorisieren.

    >[AZURE.NOTE] Dieser Code schlägt die Active Directory-Gruppe nach dem Namen nach. In vielen Fällen ist es empfehlenswert, die Gruppen-ID als App-Einstellung des mobilen Diensts zu speichern. Der Gruppenname kann sich ändern, die ID bleibt dagegen immer gleich. Mit der Änderung des Gruppennamens ist jedoch meist eine Änderung des Rollenumfangs verbunden, daher kann also trotzdem eine Aktualisierung des mobilen Dienstcodes erforderlich sein.  

        public override void OnAuthorization(HttpActionContext actionContext)
        {
            if (actionContext == null)
            {
                throw new ArgumentNullException("actionContext");
            }

            ApiServices services = new ApiServices(actionContext.ControllerContext.Configuration);

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
                return;
            }

            string accessToken = GetAADToken(services);
            if (accessToken == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get an AAD access token.");
                return;
            }

            GraphConnection graphConnection = new GraphConnection(accessToken);
            if (graphConnection == null)
            {
                services.Log.Error("AuthorizeAadRole: Failed to get a graph connection.");
                return;
            }

            bool isAuthorized = false;

            // Find the group using a filter on the name
            FilterGenerator groupFilter = new FilterGenerator();
            groupFilter.QueryFilter = ExpressionHelper
                .CreateConditionalExpression(typeof(Group), GraphProperty.DisplayName, Role.ToString(), 
                    ExpressionType.Equal);

            // Get the group id from the filtered results
            PagedResults<Group> groupPages = graphConnection.List<Group>(null, groupFilter);
            string groupId = groupPages.Results.FirstOrDefault().ObjectId;

            // Check group membership to see if the user is part of the group that corresponds to the role
            if (!string.IsNullOrEmpty(groupId))
            {
                ServiceUser serviceUser = controller.User as ServiceUser;
                if (serviceUser != null && serviceUser.Level == AuthorizationLevel.User)
                {
                    var idents = serviceUser.GetIdentitiesAsync().Result;
                    var clientAadCredentials = idents.OfType<AzureActiveDirectoryCredentials>().FirstOrDefault();
                    if (clientAadCredentials != null)
                    {
                        bool isMember = graphConnection.IsMemberOf(groupId, clientAadCredentials.ObjectId);
                        if (isMember)
                        {
                            isAuthorized = true;
                        }
                    }
                }
            }

            if (!isAuthorized)
            {
                actionContext.Response = actionContext.Request
                    .CreateErrorResponse(HttpStatusCode.Forbidden, 
						"User is not logged in or not a member of the required group");
            }
        }

8. Speichern Sie die Änderungen an der Datei "AuthorizeAadRole.cs".

## <a name="add-access-checking"></a>Hinzufügen einer rollenbasierten Zugriffsprüfung für Datenbankvorgänge

1. Erweitern Sie in Visual Studio den Ordner **Controllers** im mobilen Dienstprojekt. Öffnen Sie "TodoItemController.cs".

2. Fügen Sie in "TodoItemController.cs" eine `using`-Anweisung für den Utilities-Namespace hinzu, der das benutzerdefinierte Autorisierungsattribut enthält. 

        using todolistService.Utilities;

3. In "TodoItemController.cs" können Sie das Attribut zu Ihrer Controllerklasse oder einzelnen Methoden hinzufügen, je nachdem, wie der Zugriff geprüft werden soll. Wenn die Zugriffsprüfung für alle Controllervorgänge basierend auf derselben Rolle erfolgen soll, fügen Sie das Attribut einfach zur Klasse hinzu. Für den Test in diesem Lernprogramm fügen Sie das Attribut wie folgt zur Klasse hinzu.

        [AuthorizeAadRole(AadGroups.Sales)]
        public class TodoItemController : TableController<TodoItem>

    If you only wanted to access check insert, update, and delete operations, you would set the attribute only on those methods as follows.

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


4. Speichern Sie "TodoItemController.cs", und erstellen Sie den mobilen Dienst, um sicherzustellen, dass keine Syntaxfehler vorhanden sind.
5. Veröffentlichen Sie den mobilen Dienst in Ihrem Azure-Konto.


## <a name="test-client"></a>Testen des Clientzugriffs

[AZURE.INCLUDE [mobile-services-aad-rbac-test-app](../includes/mobile-services-aad-rbac-test-app.md)]





<!-- Anchors. -->
[Erstellen der Gruppe "Sales" mit Mitgliedschaften]: #create-group
[Erzeugen eines Schlüssels für die integrierte Anwendung]: #generate-key
[Erstellen eines benutzerdefinierten Autorisierungsattributs]: #create-custom-authorization-attribute
[Hinzufügen einer rollenbasierten Zugriffsprüfung für Datenbankvorgänge]: #add-access-checking
[Testen des Clientzugriffs]: #test-client



<!-- Images -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-aad-rbac/add-authorize-aad-role-class.png

<!-- URLs. -->
[Hinzufügen von Authentifizierung zur App]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[So registrieren Sie sich beim Azure Active Directory]: /de-de/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verzeichnissynchronisierungsszenarien]: http://msdn.microsoft.com/library/azure/jj573653.aspx
[Speichern von Serverskripts]: /de-de/documentation/articles/mobile-services-store-scripts-source-control/
[Registrieren für die Verwendung einer Azure Active Directory-Anmeldung]: /de-de/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Graph-Clientbibliothek]: http://go.microsoft.com/fwlink/?LinkId=510536
[IsMemberOf]: http://msdn.microsoft.com/de-de/library/azure/dn151601.aspx

<!--HONumber=42-->
