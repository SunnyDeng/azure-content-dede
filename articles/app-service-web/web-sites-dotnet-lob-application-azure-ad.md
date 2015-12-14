<properties 
	pageTitle="Erstellen einer .NET MVC-Web-App in Azure App Service mit Azure Active Directory-Authentifizierung" 
	description="Erfahren Sie, wie Sie eine ASP.NET MVC-Branchenanwendung in Azure App Service erstellen, die mit Azure Active Directory authentifiziert wird." 
	services="app-service\web, active-directory" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="web" 
	ms.date="10/14/2015" 
	ms.author="cephalin"/>

# Erstellen einer .NET MVC-Web-App in Azure App Service mit Azure Active Directory-Authentifizierung #

In diesem Artikel erfahren Sie, wie Sie eine ASP.NET MVC-Branchenanwendung in [Azure App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) mit [Azure Active Directory](/services/active-directory/) als Identitätsanbieter erstellen. Außerdem erfahren Sie, wie Sie die [Azure Active Directory Graph-Clientbibliothek](http://blogs.msdn.com/b/aadgraphteam/archive/2014/06/02/azure-active-directory-graph-client-library-1-0-publish.aspx) zum Abfragen von Verzeichnisdaten in der Anwendung verwenden.

Der verwendete Azure Active Directory-Mandant kann ein reines Azure-Verzeichnis haben oder mit Ihrem lokalen Active Directory (AD) synchronisiert sein, um für lokale oder Remote-Mitarbeiter die Möglichkeit einer einmaligen Anmeldung zu schaffen.

>[AZURE.NOTE]Für Azure App Service-Web-Apps können Sie eine Authentifizierung für einen Azure Active Directory-Mandanten mit wenigen Mausklicks konfigurieren. Weitere Informationen finden Sie unter [Verwenden von Active Directory für die Authentifizierung in Azure App Service](web-sites-authentication-authorization.md).

<a name="bkmk_build"></a>
## Was Sie erstellen ##

Sie erstellen eine einfache Branchenanwendung für Create-Read-Update-Delete (CRUD) in App Service-Web-Apps, die Arbeitsaufgaben mit folgenden Features erfasst:

- Authentifizieren von Benutzern mit Azure Active Directory
- Implementieren von Funktionen zum An- und Abmelden
- Verwenden von `[Authorize]` zum Autorisieren von Benutzern für verschiedene CRUD-Aktionen
- Abfragen von Azure Active Directory-Daten mit der [Azure Active Directory Graph-API](http://msdn.microsoft.com/library/azure/hh974476.aspx)
- Verwenden von [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (anstelle von Windows Identity Foundation, WIF). Dies ist das künftige ASP.NET und lässt sich wesentlich einfacher als WIF für die Authentifizierung und Autorisierung einrichten.

<a name="bkmk_need"></a>
## Was Sie benötigen ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Sie benötigen Folgendes zum Bearbeiten dieses Lernprogramms:

- Einen Azure Active Directory-Mandanten mit Benutzern in verschiedenen Gruppen
- Berechtigungen zum Erstellen von Anwendungen auf dem Azure Active Directory-Mandanten
- Visual Studio 2013
- [Azure SDK 2.5.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) oder höher

<a name="bkmk_sample"></a>
## Verwenden der Beispielanwendung für die Vorlage der Branchenanwendung ##

Die Beispielanwendung in diesem Tutorial ([WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims)) wurde vom Azure Active Directory-Team erstellt und kann ohne Weiteres als Vorlage zum Erstellen neuer Branchenanwendungen verwendet werden. Sie hat die folgenden integrierten Features:

- Verwendet [OpenID Connect](http://openid.net/connect/) zur Authentifizierung mit Azure Active Directory
- Enthält einen `TaskTracker`-Beispielcontroller, der veranschaulicht, wie Sie verschiedene Rollen für bestimmte Aktionen in einer Anwendung autorisieren können, einschließlich der standardmäßigen Verwendung von `[Authorize]`. 
- Es handelt sich um eine mehrinstanzenfähige Anwendung mit vordefinierten Rollen, die Sie sofort Ihren Benutzern und Gruppen zuweisen können. 

<a name="bkmk_run" />
## Ausführen der Beispielanwendung ##

1.	Klonen Sie die Beispielprojektmappe unter [WebApp-RoleClaims-DotNet](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims), oder laden Sie sie in Ihr lokales Verzeichnis herunter.

2.	Befolgen Sie die Anweisungen unter [How To Run The Sample as a Single Tenant App](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims#how-to-run-the-sample-as-a-single-tenant-app) (Ausführen des Beispiels als Einzelinstanz-App, in englischer Sprache), um die Azure Active Directory-Anwendung und das Projekt einzurichten. Achten Sie darauf, dass Sie alle Anweisungen befolgen, um die Anwendung von einer mehrinstanzenfähigen Anwendung in eine Einzelinstanzanwendung zu konvertieren.

3.	Klicken Sie in der Ansicht [Klassisches Azure-Portal](https://manage.windowsazure.com) für Ihre Azure Active Directory-Anwendung, die Sie gerade erstellt haben, auf die Registerkarte **BENUTZER**. Weisen Sie die gewünschten Benutzer anschließend den gewünschten Rollen zu.

	>[AZURE.NOTE]Wenn Sie den Gruppen zusätzlich zu Benutzern Rollen zuweisen möchten, müssen Sie für Ihren Azure Active Directory-Mandanten ein Upgrade auf [Azure Active Directory Premium](/pricing/details/active-directory/) durchführen. Wenn auf der klassischen Portaloberfläche Ihrer Anwendung die Registerkarte **BENUTZER** anstelle der Registerkarte **BENUTZER UND GRUPPEN angezeigt wird, können Sie Azure Active Directory Premium ausprobieren, indem Sie auf die Registerkarte **LIZENZEN** Ihres Azure Active Directory-Mandanten zugreifen.

3.	Nachdem Sie mit dem Konfigurieren der Anwendung fertig sind, geben Sie in Visual Studio `F5` ein, um die ASP.NET-Anwendung auszuführen.

4.	Klicken Sie nach dem Laden der Anwendung auf **Anmelden**, und melden Sie sich als Benutzer an, der im klassischen Azure-Portal über die Administratorrolle verfügt.

5.	Wenn Sie die Azure Active Directory-Anwendung richtig konfiguriert und die entsprechenden Einstellungen in der Datei "Web.config" festgelegt haben, werden Sie an die Anmeldung umgeleitet. Melden Sie sich einfach mit dem Konto an, das Sie zum Erstellen der Azure Active Directory-Anwendung im klassischen Azure-Portal verwendet haben. Dies ist der standardmäßige Besitzer der Azure Active Directory-Anwendung.
	
<a name="bkmk_deploy"></a>
## Bereitstellen der Beispielanwendung in App Service-Web-Apps

Sie veröffentlichen nun die Anwendung in einer Web-App in Azure App Service. Die Datei [README.md](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims/blob/master/README.md) enthält bereits Anweisungen für die Bereitstellung in App Services-Web-Apps, aber dadurch wird auch die Konfiguration für Ihre lokale Debugumgebung aufgehoben. Ich zeige Ihnen, wie eine Bereitstellung unter Beibehaltung der Debugkonfiguration möglich ist.

1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Veröffentlichen**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/publish-app.png)

2. Wählen Sie **Microsoft Azure-Web-Apps**.

3. Wenn Sie sich nicht bei Azure angemeldet haben, klicken Sie auf **Konto hinzufügen**, und verwenden Sie zum Anmelden das Microsoft-Konto für Ihr Azure-Abonnement.

4. Klicken Sie nach der Anmeldung auf **Neu**, um eine neue Web-App in Azure zu erstellen.

5. Füllen Sie alle erforderlichen Felder aus. Sie benötigen eine Datenbankverbindung, damit diese Anwendung Rollenzuordnungen zwischengespeicherte Token und eine Anwendungsdaten speichern kann.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-create-website.png)

6. Klicken Sie auf **Erstellen**. Nachdem die Web-App erstellt wurde, wird das Dialogfeld **Web veröffentlichen** geöffnet.

7. Ändern Sie **http** unter **Ziel-URL** in **https**. Kopieren Sie die gesamte URL in einen Text-Editor. Sie werden sie später verwenden. Klicken Sie auf **Weiter**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-change-to-https.png)

8. Deaktivieren Sie das Kontrollkästchen **Unternehmensauthentifizierung aktivieren**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-enable-code-first-migrations.png)

8. Erweitern Sie **RoleClaimContext**, und wählen Sie **Code First-Migrationen ausführen (wird beim Öffnen der Anwendung ausgeführt)**. [Code First-Migrationen](https://msdn.microsoft.com/data/jj591621.aspx) dienen als Unterstützung beim Aktualisieren des Datenbankschemas Ihrer App in Azure, wenn Sie später zusätzliche Code First-Datenmodelle definieren.

9. Um das Veröffentlichen im Web fortzusetzen, klicken Sie nicht auf **Veröffentlichen**, sondern auf **Schließen**. Klicken Sie auf **Ja**, um die Änderungen am Veröffentlichungsprofil zu speichern.

2. Navigieren Sie im [klassischen Azure-Portal](https://manage.windowsazure.com) zu Ihrem Azure Active Directory-Mandanten, und klicken Sie auf die Registerkarte **Anwendungen**.

2. Klicken Sie unten auf der Seite auf **Hinzufügen**.

2. Klicken Sie auf **Add an application my organization is developing**.

3. Wählen Sie **Webanwendung und/oder Web-API**.

4. Benennen Sie die Anwendung, und klicken Sie auf **Weiter**.

5. Legen Sie unter "App-Eigenschaften" die Option **URL für Anmeldung** auf die Web-App-URL fest, die Sie zuvor gespeichert haben (z. B. `https://<site-name>.azurewebsites.net/`), und legen Sie **APP-ID-URI** auf `https://<aad-tenanet-name>/<app-name>` fest. Klicken Sie danach auf **Abschließen**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-app-properties.png)

2.	Aktualisieren Sie das Anwendungsmanifest nach Erstellung der Anwendung, wie Sie dies bereits gemäß den Anweisungen unter [Define your Application Roles](https://github.com/Azure-Samples/active-directory-dotnet-webapp-roleclaims#step-2-define-your-application-roles) (Definieren der Anwendungsrollen, in englischer Sprache) durchgeführt haben.

3.	Klicken Sie in der Ansicht [Klassisches Azure-Portal](https://manage.windowsazure.com) für Ihre Azure Active Directory-Anwendung, die Sie gerade erstellt haben, auf die Registerkarte **BENUTZER**. Weisen Sie die gewünschten Benutzer anschließend den gewünschten Rollen zu.

6. Klicken Sie auf die Registerkarte **KONFIGURIEREN**.

7. Erstellen Sie unter **Schlüssel** einen neuen Schlüssel, indem Sie in der Dropdownliste die Option **1 Jahr** auswählen.

8. Wählen Sie unter **Berechtigungen für andere Anwendungen** für den Eintrag **Azure Active Directory** in der Dropdownliste **Delegierte Berechtigungen** die Optionen **Anmelden und Benutzerprofil lesen** und **Verzeichnisdaten lesen** aus.

	> [AZURE.NOTE]Die genauen Berechtigungen, die Sie hier benötigen, hängen von der gewünschten Funktionalität Ihrer Anwendung ab. Für einige Berechtigungen muss die Rolle **Globaler Administrator** festgelegt werden. Die von diesem Lernprogramm benötigten Berechtigungen erfordern jedoch nur die Rolle **Benutzer**.

9.  Klicken Sie auf **Speichern**.

10.  Bevor Sie die gespeicherte Konfigurationsseite verlassen, kopieren Sie die folgenden Informationen in einen Text-Editor.

	-	Client-ID
	-	Schlüssel (Wenn Sie die Seite verlassen, können Sie den Schlüssel nicht mehr sehen)

11. Öffnen Sie in Visual Studio die Datei **Web.Release.config** in Ihrem Projekt. Fügen Sie den folgenden XML-Code in das `<configuration>`-Tag ein, und ersetzen Sie den Wert der einzelnen Schlüssel durch die Informationen, die Sie für die neue Azure Active Directory-Anwendung gespeichert haben.
	<pre class="prettyprint">
&lt;appSettings>
   &lt;add key="ida:ClientId" value="<mark>[e.g. 82692da5-a86f-44c9-9d53-2f88d52b478b]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
   &lt;add key="ida:AppKey" value="<mark>[e.g. rZJJ9bHSi/cYnYwmQFxLYDn/6EfnrnIfKoNzv9NKgbo=]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
   &lt;add key="ida:PostLogoutRedirectUri" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" />
&lt;/appSettings></pre>Stellen Sie sicher, dass der Wert von "ida:PostLogoutRedirectUri" mit einem Schrägstrich "/" endet.

1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Veröffentlichen**.

2. Klicken Sie auf **Veröffentlichen**, um die Anwendung in der Azure App Service-Web-App zu veröffentlichen.

Wenn Sie fertig sind, sind zwei Azure Active Directory-Anwendungen im klassischen Azure-Portal konfiguriert, eine für Ihre Debugumgebung in Visual Studio und eine für die veröffentlichte Web-App in Azure. Während des Debuggens werden die App-Einstellungen in der Datei "Web.config" verwendet, damit die **Debug**-Konfiguration in Azure Active Directory funktioniert. Bei der Veröffentlichung (standardmäßig wird die **Release**-Konfiguration veröffentlicht) wird eine transformierte Datei "Web.config" hochgeladen, welche die geänderten App-Einstellungen der Datei "Web.Release.config" enthält.

Wenn Sie die veröffentlichte Web-App dem Debugger anfügen möchten (Sie müssen Debugsymbole Ihres Codes in der veröffentlichten Web-App hochladen), können Sie einen Klon der Debugkonfiguration für das Debuggen von Azure erstellen, jedoch mit einer eigenen benutzerdefinierten "Web.config"-Transformation (z. B. "Web.AzureDebug.config"), welche die Azure Active Directory-Einstellungen der Datei "Web.Release.config" verwendet. Auf diese Weise können Sie eine statische Konfiguration in verschiedenen Umgebungen beibehalten.

<a name="bkmk_crud"></a>
## Hinzufügen von Branchenfunktionen zur Beispielanwendung

In diesem Teil des Lernprogramms erfahren Sie, wie Sie die gewünschte Branchenfunktionalität basierend auf der Beispielanwendung erstellen. Sie erstellen einen einfachen CRUD-Arbeitsaufgaben-Tracker ähnlich dem TaskTracker-Controller, der jedoch das standardmäßige CRUD-Gerüst und -Entwurfsmuster verwendet. Außerdem verwenden Sie die enthaltene Datei "Scripts\\AadPickerLibrary.js", um Ihre Anwendung mit Daten aus der Azure Active Directory Graph-API zu ergänzen.

5.	Erstellen Sie im Ordner „Models“ ein neues [Code First](http://www.asp.net/mvc/overview/getting-started/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)-Modell namens „WorkItem.cs“, und ersetzen Sie den Code durch den folgenden Code:

		using System.ComponentModel.DataAnnotations;
		
		namespace WebApp_RoleClaims_DotNet.Models
		{
		    public class WorkItem
		    {
		        [Key]
		        public int ItemID { get; set; }
		        public string AssignedToID { get; set; }
		        public string AssignedToName { get; set; }
		        public string Description { get; set; }
		        public WorkItemStatus Status { get; set; }
		    }
		
		    public enum WorkItemStatus
		    {
		        Open, 
		        Investigating, 
		        Resolved, 
		        Closed
		    }
		}

6.	Öffnen Sie die Datei „DAL\\RoleClaimContext.cs“, und fügen Sie den hervorgehobenen Code hinzu:
	<pre class="prettyprint">
public class RoleClaimContext : DbContext
{
    public RoleClaimContext() : base("RoleClaimContext") { }

    public DbSet&lt;Task> Tasks { get; set; }
    <mark>public DbSet&lt;WorkItem> WorkItems { get; set; }</mark>
    public DbSet&lt;TokenCacheEntry> TokenCacheEntries { get; set; }
}</pre>

7.	Erstellen Sie das Projekt, damit das neue Modell für die Gerüstlogik in Visual Studio zugänglich ist.

8.	Fügen Sie dem Ordner "Controllers" das neue Gerüstelement `WorkItemsController` hinzu. Klicken Sie dazu mit der rechten Maustaste auf **Controllers**, zeigen Sie auf **Hinzufügen**, und wählen Sie **Neues Gerüstelement**.

9.	Wählen Sie **MVC 5-Controller mit Ansichten per Entity Framework**, und klicken Sie auf **Hinzufügen**.

10.	Wählen Sie das soeben erstellte Modell aus, und klicken Sie auf **Hinzufügen**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-add-scaffolded-controller.png)

9.	Öffnen der Datei "Controllers\\WorkItemsController.cs"

11. Fügen Sie den jeweiligen Aktionen unten die hervorgehobenen [Authorize]-Dekorationen hinzu.
	<pre class="prettyprint">
...

<mark>[Authorize(Roles = "Admin, Observer, Writer, Approver")]</mark>
public class WorkItemsController : Controller
{
	...

    <mark>[Authorize(Roles = "Admin, Writer")]</mark>
    public ActionResult Create()
    ...

    <mark>[Authorize(Roles = "Admin, Writer")]</mark>
    public async Task&lt;ActionResult> Create([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
    ...

    <mark>[Authorize(Roles = "Admin, Writer")]</mark>
    public async Task&lt;ActionResult> Edit(int? id)
    ...

    <mark>[Authorize(Roles = "Admin, Writer")]</mark>
    public async Task&lt;ActionResult> Edit([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
    ...

    <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
    public async Task&lt;ActionResult> Delete(int? id)
    ...

    <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
    public async Task&lt;ActionResult> DeleteConfirmed(int id)
    ...
}</pre>Da Sie die Rollenzuordnungen auf der Benutzeroberfläche des klassischen Azure-Portals vornehmen, müssen Sie lediglich sicherstellen, dass jede Aktion die richtigen Rollen autorisiert.

	> [AZURE.NOTE]Bei einigen Aktionen haben Sie vielleicht die Dekoration <code>[ValidateAntiForgeryToken]</code> bemerkt. Aufgrund des Verhaltens, das von [Brock Allen](https://twitter.com/BrockLAllen) unter [MVC 4, AntiForgeryToken and Claims](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/) (MVC 4, AntiForgeryToken und Ansprüche, in englischer Sprache) beschrieben wird, besteht Ihr HTTP POST-Vorgang aus folgenden Gründen die Validierung des Fälschungsschutztokens ggf. nicht: + Azure Active Directory sendet http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider nicht, was vom Fälschungsschutztoken aber standardmäßig benötigt wird. + Wenn für Azure Active Directory eine Verzeichnissynchronisierung mit AD FS durchgeführt wird, sendet die AD FS-Vertrauensstellung den Anspruch http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider standardmäßig ebenfalls nicht. Sie können AD FS aber manuell konfigurieren, um diesen Anspruch zu senden. Mit diesen Dingen können Sie sich im nächsten Schritt befassen.

12.  Fügen Sie in der Datei „App\_Start\\Startup.Auth.cs“ der `ConfigureAuth`-Methode die folgende Codezeile hinzu. Klicken Sie mit der rechten Maustaste jeweils auf einen Fehler zur Namensauflösung, um ihn zu beheben.

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	Mit `ClaimTypes.NameIdentifies` wird der Anspruch `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` angegeben, der von Azure Active Directory nicht bereitgestellt wird. Da Sie nun die Autorisierung vorgenommen haben (die gewiss nicht lange gedauert hat), können Sie sich der eigentlichen Funktionalität der Aktionen zuwenden.

13.	Fügen Sie unter Create() und Edit() den folgenden Code hinzu, um einige Variablen später in JavaScript zur Verfügung zu stellen. Klicken Sie mit der rechten Maustaste jeweils auf einen Fehler zur Namensauflösung, um ihn zu beheben.

        ViewData["token"] = AcquireToken(ClaimsPrincipal.Current.FindFirst(Globals.ObjectIdClaimType).Value);
        ViewData["tenant"] = ConfigHelper.Tenant;

13.	Da die `AcquireToken()`-Methode noch nicht definiert ist, müssen Sie sie jetzt in der `WorkItemsController`-Klasse definieren. Klicken Sie mit der rechten Maustaste jeweils auf einen Fehler zur Namensauflösung, um ihn zu beheben.

        static string AcquireToken(string userObjectId)
        {
            ClientCredential cred = new ClientCredential(ConfigHelper.ClientId, ConfigHelper.AppKey);
            Claim tenantIdClaim = ClaimsPrincipal.Current.FindFirst(Globals.TenantIdClaimType);
            AuthenticationContext authContext = new AuthenticationContext(String.Format(CultureInfo.InvariantCulture, ConfigHelper.AadInstance, tenantIdClaim.Value), new TokenDbCache(userObjectId));
            AuthenticationResult result = authContext.AcquireTokenSilent(ConfigHelper.GraphResourceId, cred, new UserIdentifier(userObjectId, UserIdentifierType.UniqueId));
            return result.AccessToken;
        }
		
14.	Suchen Sie in "Views\\WorkItems\\Create.cshtml" (einem automatisch erstellten Gerüstelement) die Hilfsmethode `Html.BeginForm`, und ändern Sie sie folgendermaßen:
	<pre class="prettyprint">@using (Html.BeginForm(<mark>"Create", "WorkItems", FormMethod.Post, new { id = "main-form" }</mark>))
{
    @Html.AntiForgeryToken()

    &lt;div class="form-horizontal">
        &lt;h4>WorkItem&lt;/h4>
        &lt;hr />
        @Html.ValidationSummary(true, "", new { @class = "text-danger" })

        &lt;div class="form-group">
            &lt;div class="col-md-10">
                @Html.EditorFor(model => model.AssignedToID, new { htmlAttributes = new { @class = "form-control"<mark>, @type="hidden"</mark> } })
                @Html.ValidationMessageFor(model => model.AssignedToID, "", new { @class = "text-danger" })
            &lt;/div>
        &lt;/div>

        &lt;div class="form-group">
            @Html.LabelFor(model => model.AssignedToName, htmlAttributes: new { @class = "control-label col-md-2" })
            &lt;div class="col-md-10">
                @Html.EditorFor(model => model.AssignedToName, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.AssignedToName, "", new { @class = "text-danger" })
            &lt;/div>
        &lt;/div>

        &lt;div class="form-group">
            @Html.LabelFor(model => model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
            &lt;div class="col-md-10">
                @Html.EditorFor(model => model.Description, new { htmlAttributes = new { @class = "form-control" } })
                @Html.ValidationMessageFor(model => model.Description, "", new { @class = "text-danger" })
            &lt;/div>
        &lt;/div>

        &lt;div class="form-group">
            @Html.LabelFor(model => model.Status, htmlAttributes: new { @class = "control-label col-md-2" })
            &lt;div class="col-md-10">
                @Html.EnumDropDownListFor(model => model.Status, htmlAttributes: new { @class = "form-control" })
                @Html.ValidationMessageFor(model => model.Status, "", new { @class = "text-danger" })
            &lt;/div>
        &lt;/div>

        &lt;div class="form-group">
            &lt;div class="col-md-offset-2 col-md-10">
                &lt;input type="submit" value="Create" class="btn btn-default" <mark>id="submit-button"</mark> />
            &lt;/div>
        &lt;/div>
    &lt;/div>

    <mark>&lt;script>
            // Code für Personen-/Gruppenauswahl
            var maxResultsPerPage = 14;
            var input = document.getElementById("AssignedToName");
            var token = "@ViewData["token"]";
            var tenant = "@ViewData["tenant"]";

            var picker = new AadPicker(maxResultsPerPage, input, token, tenant);

            // Übermitteln Sie den ausgewählten Benutzer bzw. die Gruppe für die Zuweisung.
            $("#submit-button").click({ picker: picker }, function () {
                if (!picker.Selected())
                    return;
                $("#main-form").get()[0].elements["AssignedToID"].value = picker.Selected().objectId;
            });
    &lt;/script></mark>

}</pre>Im Skript ruft das AadPicker-Objekt die [Azure Active Directory Graph-API](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) auf, um nach Benutzern und Gruppen zu suchen, die für die Eingabe eine Übereinstimmung ergeben.

15. Öffnen Sie die [Paket-Manager-Konsole](http://docs.nuget.org/Consume/Package-Manager-Console), und führen Sie **Enable-Migrations –EnableAutomaticMigrations** aus. Ähnlich wie die Option, die Sie beim Veröffentlichen der App unter Azure gewählt haben, dient dieser Befehl der Aktualisierung des Datenbankschemas Ihrer App in [LocalDB](https://msdn.microsoft.com/library/hh510202.aspx), wenn Sie das Debuggen in Visual Studio durchführen.

15. Führen Sie die App nun im Visual Studio-Debugger aus, oder veröffentlichen Sie sie erneut in App Service-Web-Apps. Melden Sie sich als Anwendungsbesitzer an, und navigieren Sie zu `https://<webappname>.azurewebsites.net/WorkItems/Create`. Sie sehen, dass Sie in der Dropdownliste einen Azure Active Directory-Benutzer oder eine -Gruppe auswählen können oder etwas eingeben können, um die Liste zu filtern.

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-workitem.png)

16. Füllen Sie den Rest des Formulars aus, und klicken Sie auf **Erstellen**. Die Seite "~/WorkItems/Index" zeigt jetzt die neu erstellte Arbeitsaufgabe an. Darüber hinaus sehen Sie im Screenshot unten, dass ich die Spalte `AssignedToID` in der Datei "Views\\WorkItems\\Index.cshtml" entfernt habe.

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-workitem-index.png)

11.	Nehmen Sie jetzt entsprechende Änderungen an der Ansicht **Bearbeiten** vor. Nehmen Sie in der Datei "Views\\WorkItems\\Edit.cshtml" die Änderungen an der `Html.BeginForm`-Hilfsmethode vor, die mit den Änderungen an "Views\\WorkItems\\Create.cshtml" im vorherigen Schritt identisch sind (ersetzen Sie "Create" im oben hervorgehobenen Code durch "Edit").

Das ist alles!

Nachdem Sie die Autorisierungen und die Branchenfunktionalität für die verschiedenen Aktionen im „WorkItems“-Controller konfiguriert haben, können Sie versuchen, sich als Benutzer mit unterschiedlichen Anwendungsrollen anzumelden. Sie sehen dann, wie die Anwendung reagiert.

![](./media/web-sites-dotnet-lob-application-azure-ad/11-edit-unauthorized.png)

<a name="bkmk_resources"></a>
## Weitere Ressourcen

- [Schützen der Anwendung durch SSL und das Authorize-Attribut](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Verwenden von Active Directory für die Authentifizierung in Azure App Service](web-sites-authentication-authorization.md)
- [Erstellen einer .NET MVC-Web-App in Azure App Service mit AD FS-Authentifizierung](web-sites-dotnet-lob-application-adfs.md)
- [Microsoft Azure Active Directory – Beispiele und Dokumentation](https://github.com/AzureADSamples)
- [Blog von Vittorio Bertocci](http://blogs.msdn.com/b/vbertocci/)
- [Migrate a VS2013 Web Project From WIF to Katana (Migrieren eines VS2013-Webprojekts von WIF nach Katana, in englischer Sprache)](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Azure's new Hybrid Connections not your father's #hybridCloud (Die neuen Hybridverbindungen von Azure – not your father's #hybridCloud, Video in englischer Sprache)](/documentation/videos/new-hybrid-connections-not-your-fathers-hybridcloud/)
- [Ähnlichkeiten zwischen Active Directory und Azure AD](http://technet.microsoft.com/library/dn518177.aspx)
- [DirSync mit einmaligem Anmelden](http://technet.microsoft.com/library/dn441213.aspx)
- [Unterstützte Token- und Anspruchstypen](http://msdn.microsoft.com/library/azure/dn195587.aspx)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!---HONumber=AcomDC_1203_2015-->