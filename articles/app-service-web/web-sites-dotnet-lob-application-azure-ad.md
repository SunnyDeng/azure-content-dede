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
	ms.date="04/09/2015" 
	ms.author="cephalin"/>

# Erstellen einer .NET MVC-Web-App in Azure App Service mit Azure Active Directory-Authentifizierung #

In diesem Artikel erfahren Sie, wie Sie zum Erstellen einer ASP.NET MVC-Line-of-Business-Anwendung in [Azure App Service Web Apps](http://go.microsoft.com/fwlink/?LinkId=529714) mit [Azure Active Directory](/services/active-directory/) als Identitätsanbieter. Außerdem erfahren Sie, wie Sie die [Azure Active Directory Graph-Clientbibliothek](http://blogs.msdn.com/b/aadgraphteam/archive/2014/06/02/azure-active-directory-graph-client-library-1-0-publish.aspx) um Verzeichnisdaten in der Anwendung.

Der verwendete Azure Active Directory-Mandant kann ein reines Azure-Verzeichnis haben oder mit Ihrem lokalen Active Directory (AD) synchronisiert sein, um für lokale oder Remote-Mitarbeiter die Möglichkeit einer einmaligen Anmeldung zu schaffen.

>[AZURE.NOTE]Mit dem Easy Auth-Feature in Azure App Service-Web-Apps können Sie eine einfache Authentifizierung für einen Azure Active Directory-Mandanten mit wenigen Mausklicks einrichten. Weitere Informationen finden Sie unter [Verwenden Sie Active Directory für die Authentifizierung in Azure-Anwendungsdiensts](web-sites-authentication-authorization.md).

<a name="bkmk_build"></a>
## Was Sie erstellen ##

Sie erstellen eine einfache Branchenanwendung für Create-Read-Update-Delete (CRUD) in App Service-Web-Apps, die Arbeitsaufgaben mit folgenden Features erfasst:

- Authentifizieren von Benutzern mit Azure Active Directory
- Implements-Anmeldung und Abmeldung-Funktion
- Verwendet `[Authorize]` zum Autorisieren von Benutzern für verschiedene CRUD-Aktionen
- Abfragen Azure Active Directory-Daten mit [Azure Active Directory Graph-API-](http://msdn.microsoft.com/library/azure/hh974476.aspx)
- Verwendet [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (anstelle von Windows Identity Foundation, d. h. WIF), ist die Zukunft von ASP.NET und viel einfacher, die für die Authentifizierung und Autorisierung als WIF einrichten

<a name="bkmk_need"></a>
## Was Sie benötigen ##

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Sie benötigen Folgendes zum Bearbeiten dieses Lernprogramms:

- Einen Azure Active Directory-Mandanten mit Benutzern in verschiedenen Gruppen
- Berechtigungen zum Erstellen von Anwendungen auf dem Azure Active Directory-Mandanten
- Visual Studio 2013
- [Azure SDK 2.5.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) oder höher

<a name="bkmk_sample"></a>
## Verwenden Sie Beispielanwendung für Line-of-Business-Vorlage ##

Die Beispielanwendung in diesem Lernprogramm [WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet), wird durch das Azure Active Directory-Team erstellt und als Vorlage zum Erstellen neuer Line-of-Business-Anwendungen problemlos verwendet werden können. Sie hat die folgenden integrierten Features:

- Verwendet [OpenID Connect](http://openid.net/connect/) zur Authentifizierung mit Azure Active Directory
- `Roles` Domänencontroller, die einen Azure Active Directory-Suchfilter enthält und ermöglicht es Ihnen, Azure Active Directory-Benutzer oder Gruppen Anwendungsrollen leicht zuordnen.
- Beispiel `TaskTracker` -Controller, der veranschaulicht, wie Sie die verschiedene Rollen für bestimmte Aktionen in der Anwendung, einschließlich der standardmäßigen Nutzung der autorisieren können `[Authorize]`. 

![](./media/web-sites-dotnet-lob-application-azure-ad/role-management.png)

<a name="bkmk_run" />
## Ausführen der Beispielanwendung ##

1.	Klonen oder Herunterladen die Beispielprojektmappe am [WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet) in Ihr lokales Verzeichnis.

2.	Folgen Sie die Anweisungen unter ["Readme.MD"](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/master/README.md) zum Einrichten von Azure Active Directory-Anwendung und Projekt.

	> [AZURE.NOTE]In der Azure Active Directory-Anwendung konfigurierten Berechtigungen erfordert die <strong>Benutzer</strong> Rolle; nicht **globaler Administrator**.
	
3.	Nachdem Sie die Konfiguration der Anwendung abgeschlossen haben, geben Sie `F5` zum Ausführen der Anwendung.

4.	Nachdem die Anwendung geladen wird, klicken Sie auf **Anmelden**.

5.	Wenn Sie die Azure Active Directory-Anwendung ordnungsgemäß konfiguriert und die entsprechenden Einstellungen in der Datei Web.config festlegen, sollten Sie in das Protokoll in weitergeleitet. Melden Sie sich einfach mit dem Kontonamen, die zum Erstellen die Azure Active Directory-Anwendung in Azure-Portal erforderlich, da Standardbesitzer für die Azure Active Directory-Anwendung ist.
	
	> [AZURE.NOTE]Startup.Auth.cs des Beispielprojekts, beachten Sie, dass die Anwendung eine Methode namens <code>AddOwnerAdminClaim</code>, die verwendet wird, die Anwendungsbesitzer in der Admin-Rolle hinzufügen. Dies ermöglicht die Verwaltung von Anwendungsrollen in Immediatley-Start der <code>Rollen</code> Controller.
	
4.	Klicken Sie nach der Anmeldung **Rollen** Anwendungsrollen verwalten.

5.	In **Suche nach Benutzer/Gruppen**, beginnen mit der Eingabe des gewünschten Benutzer- oder Gruppennamen ein, und beachten Sie, dass eine Dropdown-Liste eine gefilterte Liste der Benutzer und/oder Gruppen aus Ihrem Azure Active Directory-Mandanten zeigt.

	![](./media/web-sites-dotnet-lob-application-azure-ad/select-user-group.png)

	> [AZURE.NOTE]In Views\\Roles\\Index.cshtml, sehen Sie, dass die Sicht ein JavaScript-Objekt namens verwendet <code>AadPicker</code> (definiert in Scripts\\AadPickerLibrary.js) Zugriff auf die <code>Suche</code> Aktion in der <code>Rollen</code> Controller. <pre class="prettyprint">Var SearchUrl = window.location.protocol + "/ /" + window.location.host + "<mark>/Rollen/Suche</mark>";... Var Datumsauswahl = new <mark>AadPicker (SearchUrl, MaxResultsPerPage, Eingabe, Token, Mandanten)</mark>;</pre> In Controllers\\RolesController.cs, sehen Sie die <code>Suche</code> Aktion, die die tatsächliche Anforderung an die Azure Active Directory Graph-API gesendet und die Antwort zurück an die Seite zurückgegeben. Später verwenden Sie dieselbe Methode, um einfache Funktionen in Ihrer Anwendung zu erstellen.

6.	Wählen Sie einen Benutzer oder eine Gruppe aus, wählen Sie eine Rolle, und auf **Rolle zuweisen**.

<a name="bkmk_deploy"></a>
## Bereitstellen der Beispielanwendung in App Service-Web-Apps

Sie veröffentlichen nun die Anwendung in einer Web-App in Azure App Service. Sind bereits Anweisungen am ["Readme.MD"](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/GroupClaims/README.md) für die Bereitstellung von Webanwendungen für App-Dienst, aber diese Schritte auch aufheben die Konfiguration für Ihre Umgebung für lokales Debuggen. Ich zeige Ihnen, wie eine Bereitstellung unter Beibehaltung der Debugkonfiguration möglich ist.

1. Mit der rechten Maustaste in des Projekts, und wählen Sie **Veröffentlichen**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/publish-app.png)

2. Wählen Sie **Microsoft Azure-Webanwendungen**.

3. Wenn Sie sich nicht bei Azure angemeldet haben, klicken Sie auf **Anmelden**, und verwenden Sie zum Anmelden das Microsoft-Konto für Ihr Azure-Abonnement.

4. Klicken Sie nach der Anmeldung **Neu** zum Erstellen einer neuen Webanwendung in Azure.

5. Füllen Sie alle erforderlichen Felder aus. Sie benötigen eine Datenbankverbindung, damit diese Anwendung Rollenzuordnungen zwischengespeicherte Token und eine Anwendungsdaten speichern kann.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-create-website.png)

6. Klicken Sie auf **Erstellen**. Sobald die Webanwendung erstellt wurde, die **Web veröffentlichen** Dialogfeld wird geöffnet.

7. In **Ziel-URL**, ändern Sie **http** auf **Https**. Kopieren Sie die gesamte URL in einen Text-Editor. Sie werden sie später verwenden. Klicken Sie auf **Weiter**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-change-to-https.png)

8. Deaktivieren der **Organisationsauthentifizierung aktivieren** Kontrollkästchen.

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-disable-organizational-authentication.png)

9. Anstatt auf **Veröffentlichen** gehen über mit dem Web zu veröffentlichen, klicken Sie auf **Schließen**. Klicken Sie auf **Ja** um das Veröffentlichungsprofil speichern.

2. In der [Azure-Verwaltungsportal](https://manage.windowsazure.com), wechseln Sie zu Ihrem Azure Active Directory-Mandanten, und klicken Sie auf die **Anwendungen** Registerkarte.

2. Klicken Sie unten auf der Seite auf **Hinzufügen**.

3. Wählen Sie **Web-Anwendung und/oder Web API**.

4. Benennen Sie der Anwendung, und klicken Sie auf **Weiter**.

5. Legen Sie im Eigenschaftenfenster App **URL für einmaliges Anmelden** Web-app-URL, die Sie mit früheren gespeichert (z. B. `https://<site-name>.azurewebsites.net`), und die **APP-ID-URI** auf `https://<aad-tenanet-name>/<app-name>`. Klicken Sie auf **Complete**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-app-properties.png)

6. Nachdem die Anwendung erstellt wurde, klicken Sie auf **konfigurieren**.

7. Klicken Sie unter **Schlüssel**, erstellen Sie einen neuen Schlüssel auswählen **1 Jahr** in der Dropdownliste aus.

8. Unter **Berechtigungen für andere Anwendungen**, für die **Azure Active Directory** Eintrag wählen **Zugriff auf das Verzeichnis der Organisation** in den **delegierte Berechtigungen** Dropdown-Liste.

	> [AZURE.NOTE]Die genauen Berechtigungen, die Sie hier benötigen, hängen von der gewünschten Funktionalität Ihrer Anwendung ab. Einige Berechtigungen erfordern die **globaler Administrator** Rolle festlegen, aber die für dieses Lernprogramm erforderlichen Berechtigungen erfordert nur die **Benutzer** Rolle.

9.  Klicken Sie auf **Speichern**.

10.  Bevor Sie die gespeicherte Konfigurationsseite verlassen, kopieren Sie die folgenden Informationen in einen Text-Editor.

	-	Client-ID
	-	Schlüssel (Wenn Sie die Seite verlassen, können Sie den Schlüssel nicht mehr sehen)

11. Öffnen Sie in Visual Studio **Web.Release.config** in Ihrem Projekt. Fügen Sie den folgenden XML-Code in den `<configuration>` tag, und Ersetzen Sie den Wert der einzelnen Schlüssel mit den Informationen, die Sie für die neue Anwendung mit Azure Active Directory gespeichert.
	<pre class="prettyprint">
&lt;appSettings>
   &lt; hinzufügen Key = "Ida: ClientId" Value = "<mark>[z. B. 82692da5-a86f-44c9-9d53-2f88d52b478b]</mark>" xdt: Transform = "SetAttributes" xdt:Locator="Match(key)" / >
   &lt; fügen Sie Key = "Ida: AppKey" Value = "<mark>[z. B. rZJJ9bHSi/cYnYwmQFxLYDn/6EfnrnIfKoNzv9NKgbo =]</mark>" xdt: Transform = "SetAttributes" xdt:Locator="Match(key)" / >
   &lt; hinzufügen Key = "Ida: PostLogoutRedirectUri" Value = "<mark>[z. B. https://mylobapp.azurewebsites.net/]</mark>" xdt: Transform = "SetAttributes" xdt:Locator="Match(key)" / >
&lt;/appSettings></pre>Stellen Sie sicher, dass der Wert von "ida:PostLogoutRedirectUri" mit einem Schrägstrich "/" endet.

1. Mit der rechten Maustaste in des Projekts, und wählen Sie **Veröffentlichen**.

2. Klicken Sie auf **Veröffentlichen** Webanwendungen in Azure-App-Dienst veröffentlichen.

Wenn Sie fertig sind, haben Sie zwei Azure Active Directory-Anwendungen in Azure-Verwaltungsportal konfiguriert: eine für Ihre Umgebung Debuggen in Visual Studio und eine für die veröffentlichte Webanwendung in Azure. Während des Debuggens die Anwendungseinstellungen in der Datei Web.config verwendet, um Ihre **Debuggen** Konfigurationsarbeit mit Azure Active Directory und bei der Veröffentlichung (standardmäßig die **Release** Konfiguration veröffentlicht wird), eine transformierte Web.config-Datei hochgeladen wird, die die app Einstellungsänderungen in Web.Release.config enthält.

Wenn Sie die veröffentlichte Webanwendung zum anfügen möchten Debugger (Sie müssen die Debugsymbole von Code in die veröffentlichte Webanwendung hochladen) können, erstellen Sie einen Klon der Debug-Konfiguration für das Debuggen von Azure, jedoch mit eigenen benutzerdefinierten Web.config-Transformation (z. B. Web.AzureDebug.config), das die Azure Active Directory aus Web.Release.config verwendet. Auf diese Weise können Sie eine statische Konfiguration in verschiedenen Umgebungen beibehalten.

<a name="bkmk_crud"></a>
## Hinzufügen von Branchenfunktionen zur Beispielanwendung

In diesem Teil des Lernprogramms erfahren Sie, wie Sie die gewünschte Branchenfunktionalität basierend auf der Beispielanwendung erstellen. Sie erstellen einen einfachen CRUD-Arbeitsaufgaben-Tracker ähnlich dem TaskTracker-Controller, der jedoch das standardmäßige CRUD-Gerüst und -Entwurfsmuster verwendet. Außerdem verwenden Sie die enthaltene Datei "Scripts\\AadPickerLibrary.js", um Ihre Anwendung mit Daten aus der Azure Active Directory Graph-API zu ergänzen.

5.	Erstellen Sie im Ordner "Models" ein neues Modell namens "WorkItem.cs", und ersetzen Sie den Code durch den folgenden Code:

		using System.ComponentModel.DataAnnotations;
		
		namespace WebAppGroupClaimsDotNet.Models
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

6.	Öffnen Sie die Datei "DAL\\GroupClaimContext.cs", und fügen Sie den hervorgehobenen Code hinzu:
	<pre class="prettyprint">
public Class GroupClaimContext: DbContext
{
    Öffentliche GroupClaimContext(): base("GroupClaimContext") {}

    Öffentliche DbSet &lt; RolleZuweisen von > RoleMappings {get; festgelegt.}
    Öffentliche DbSet &lt; Task > Aufgaben {get; festgelegt.}
    <mark>Öffentliche DbSet &lt; WorkItem > WorkItems {get; festgelegt.}</mark>
    Öffentliche DbSet &lt; TokenCacheEntry > TokenCacheEntries {get; festgelegt.}
}</pre>

7.	Erstellen Sie das Projekt, damit das neue Modell für die Gerüstlogik in Visual Studio zugänglich ist.

8.	Fügen Sie ein neues Element für die erstellte `WorkItemsController` auf den Ordner "Controller". Dazu, mit der Maustaste **Controller**, zeigen Sie auf **Hinzufügen**, und wählen Sie **Neues erstellten Element**.

9.	Wählen Sie **MVC 5-Controller mit Ansichten mithilfe von Entity Framework** und klicken Sie auf **Hinzufügen**.

10.	Wählen Sie das Modell, das Sie gerade erstellt haben, und klicken Sie auf **Hinzufügen**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-add-scaffolded-controller.png)

9.	Öffnen der Datei "Controllers\\WorkItemsController.cs"

11. Fügen Sie die hervorgehobene [Autorisieren] Dekorationen, um die folgenden Aktionen.
	<pre class="prettyprint">
...

<mark>[Autorisieren (Rollen = "Admin, Beobachter, Writer, genehmigende Person")]</mark>
public Class WorkItemsController: Controller
{
	...

    <mark>[Autorisieren (Rollen = "Admin Writer")]</mark>
    public ActionResult Create()
    ...

    <mark>[Autorisieren (Rollen = "Admin Writer")]</mark>
    Öffentliche Async-Task &lt; ActionResult > Create ([binden (Include = "" ItemID "," AssignedToID "," AssignedToName "," Beschreibung "," Status ")] WorkItem WorkItem)
    ...

    <mark>[Autorisieren (Rollen = "Admin Writer")]</mark>
    Öffentliche Async-Task &lt; ActionResult > Edit(int? id)
    ...

    <mark>[Autorisieren (Rollen = "Admin Writer")]</mark>
    Öffentliche Async-Task &lt; ActionResult > Bearbeiten ([binden (Include = "" ItemID "," AssignedToID "," AssignedToName "," Beschreibung "," Status ")] WorkItem WorkItem)
    ...

    <mark>[Autorisieren (Rollen = "Admin", "Writer, genehmigende Person")]</mark>
    Öffentliche Async-Task &lt; ActionResult > Delete(int? id)
    ...

    <mark>[Autorisieren (Rollen = "Admin", "Writer, genehmigende Person")]</mark>
    Öffentliche Async-Task &lt; ActionResult > DeleteConfirmed (Int-Id)
    ...
}</pre>Da Sie die Rollenzuordnungen im Controller "Rollen" vornehmen, müssen Sie lediglich sicherstellen, dass jede Aktion die richtigen Rollen autorisiert.

	> [AZURE.NOTE]Sie haben möglicherweise bemerkt der <code>[ValidateAntiForgeryToken]</code> Dekoration auf einige der Aktionen. Aufgrund des Verhaltens von beschriebenen [Brock Allen](https://twitter.com/BrockLAllen) am [MVC 4, AntiForgeryToken und Ansprüche](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/) HTTP-POST Schutz vor Fälschung Überprüfung schlägt fehl, da: + sendet Azure Active Directory nicht die http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider, der standardmäßig erforderlich ist, durch den Schutz vor Fälschung Token. + ist Azure Active Directory Directory mit AD FS synchronisiert, die AD FS-Vertrauensstellung standardmäßig sendet keine der http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider entweder in Anspruch nehmen , obwohl Sie AD FS zum Senden dieser Anspruch manuell konfigurieren können. Mit diesen Dingen können Sie sich im nächsten Schritt befassen.

12.  In App_Start\\Startup.Auth.cs, fügen Sie die folgende Codezeile in der `ConfigureAuth` Methode:

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	`ClaimTypes.NameIdentifies` Gibt den Anspruch `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier`, die Azure Active Directory bereitgestellt wird. Da Sie nun die Autorisierung vorgenommen haben (die gewiss nicht lange gedauert hat), können Sie sich der eigentlichen Funktionalität der Aktionen zuwenden.

13.	Create() und Edit() fügen Sie den folgenden Code, um einige Variablen JavaScript später zur Verfügung: ViewData["token"] = GraphHelper.AcquireToken(ClaimsPrincipal.Current.FindFirst(Globals.ObjectIdClaimType). Wert); ViewData["Mandant"] = ConfigHelper.Tenant;

14.	Suchen in Views\\WorkItems\\Create.cshtml (eine automatisch erstellte Element), die `Html.BeginForm` Hilfsmethode und ändern Sie es wie folgt:
	<pre class="prettyprint">@ verwenden (Html.BeginForm (<mark>"Erstellen", "WorkItems", FormMethod.Post, neue {Id = "Hauptformular"}</mark>))
{
    @Html.AntiForgeryToken()

    &lt; Div Class = "Form Horizontal" >
        &lt; h4 > WorkItems &lt; / h4 >
        &lt;hr />
        @Html.ValidationSummary ("true", "", neue {@class = "Text-Danger"})

        &lt; Div Class = "Formular-Gruppe" >
            &lt; Div Class = "Col-Md-10" >
                @Html.EditorFor (Model = > Modell. Neue AssignedToID {HtmlAttributes = new {@class = "Form-Control"<mark>, @type = "hidden"</mark> }})
                @Html.ValidationMessageFor (Model = > Modell. AssignedToID, "", new {@class = "Text-Danger"})
            &lt;/div>
        &lt;/div>

        &lt; Div Class = "Formular-Gruppe" >
            @Html.LabelFor (Model = > Modell. AssignedToName, HtmlAttributes: new {@class = "-Bezeichnung Col-Md-2"})
            &lt; Div Class = "Col-Md-10" >
                @Html.EditorFor (Model = > Modell. Neue AssignedToName {HtmlAttributes = new {@class = "Form-Control"}})
                @Html.ValidationMessageFor (Model = > Modell. AssignedToName, "", new {@class = "Text-Danger"})
            &lt;/div>
        &lt;/div>

        &lt; Div Class = "Formular-Gruppe" >
            @Html.LabelFor (Model = > Modell. Beschreibung, HtmlAttributes: new {@class = "-Bezeichnung Col-Md-2"})
            &lt; Div Class = "Col-Md-10" >
                @Html.EditorFor (Model = > Modell. Beschreibung, neue {HtmlAttributes = new {@class = "Form-Control"}})
                @Html.ValidationMessageFor (Model = > Modell. Beschreibung, "", new {@class = "Text-Danger"})
            &lt;/div>
        &lt;/div>

        &lt; Div Class = "Formular-Gruppe" >
            @Html.LabelFor (Model = > Modell. Status, HtmlAttributes: new {@class = "-Bezeichnung Col-Md-2"})
            &lt; Div Class = "Col-Md-10" >
                @Html.EnumDropDownListFor (Model = > Modell. Status, HtmlAttributes: new {@class = "Form-Control"})
                @Html.ValidationMessageFor (Model = > Modell. Status "", new {@class = "Text-Danger"})
            &lt;/div>
        &lt;/div>

        &lt; Div Class = "Formular-Gruppe" >
            &lt; Div Class = "Col-Md-Offset-2 Col-Md-10" >
                &lt; input Type = "submit" Value = "Create" Class = "Btn Btn-Default" <mark>Id = "Abschicken"</mark> / >
            &lt;/div>
        &lt;/div>
    &lt;/div>

    <mark>&lt; Script >
            Benutzer/Gruppe Picker-Code
            Var MaxResultsPerPage = 14;
            Var SearchUrl = window.location.protocol + "/ /" + window.location.host + "/ Rollen/Suche";
            Var Eingabe = document.getElementById("AssignedToName");
            Var Token = "@ViewData ["Token"]";
            Var Mandanten = "@ViewData ["Mandant"]";

            Auswahl einer Var = neue AadPicker (SearchUrl, MaxResultsPerPage, Eingabe, Token, Mandanten);

            Senden der ausgewählten Benutzer/Gruppe Richtlinienkatalog werden.
            $("#submit-button").click ({Datumsauswahl: Auswahl}, () {}-Funktion
                Wenn (! Auswahl. Selected())
                    return;
                Wert von $("#main-form").get() [0] .elements ["AssignedToID"] = Auswahl. .ObjectId des ausgewählten ();
            });
    &lt; / script ></mark>

}</pre>Im Skript wird das Objekt AadPicker durchsucht die `~/Roles/Search` Aktion für Azure Active Directory-Benutzer und Gruppen, die mit die Eingabe übereinstimmen. Klicken Sie dann bei die Senden-Schaltfläche geklickt wird, das AadPicker-Objekt speichert die Benutzer-ID ausgeblendeten `AssignedToID` Feld.

15. Führen Sie die App nun im Visual Studio-Debugger aus, oder veröffentlichen Sie sie in Azure App Service-Web-Apps. Melden Sie sich als Anwendungsbesitzer, und navigieren Sie zu `~/WorkItems/Create`. Für Meine veröffentlichten Line-of-Business-app erfolgt die Navigation zur `https://mylobapp.azurewebsites.net/WorkItems/Create`. Sie sehen nun, dass Sie denselben "AadPicker"-Suchfilter zum Auswählen eines Azure Active Directory-Benutzers verwenden können.

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-workitem.png)

16. Füllen Sie den Rest des Formulars aus, und klicken Sie auf **Erstellen**. Die Seite "~/WorkItems/Index" zeigt jetzt die neu erstellte Arbeitsaufgabe an. Darüber hinaus sehen im Screenshot unten, dass ich entfernt die `AssignedToID` Spalte in Views\\WorkItems\\Index.cshtml.

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-workitem-index.png)

11.	Führen Sie jetzt ähnliche Änderungen an den **Bearbeiten** anzeigen. Views\\WorkItems\\Edit.cshtml, nehmen die Änderungen vor, um die `Html.BeginForm` Hilfsmethode, die mit denen für Views\\WorkItems\\Create.cshtml im vorherigen Schritt identisch sind (ersetzen Sie "Erstellen" mit "Bearbeiten" im obigen Code hervorgehoben).

Das ist alles!

Nachdem Sie die Autorisierungen und die Branchenfunktionalität für die verschiedenen Aktionen im "WorkItems"-Controller konfiguriert haben, können Sie versuchen, sich als Benutzer mit unterschiedlichen Anwendungsrollen anzumelden.

![](./media/web-sites-dotnet-lob-application-azure-ad/11-edit-unauthorized.png)

<a name="bkmk_resources"></a>
## Weitere Ressourcen

- [Schützen der Anwendung durch SSL und das Attribut "Authorize"](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Verwenden Sie Active Directory für die Authentifizierung in Azure-App-Dienst](web-sites-authentication-authorization.md)
- [Erstellen Sie eine .NET MVC-Webanwendung in Azure-App-Dienst mit AD FS-Authentifizierung](web-sites-dotnet-lob-application-adfs.md)
- [Microsoft Azure Active Directory Beispiele und Dokumentation](https://github.com/AzureADSamples)
- [Vittorio Bertocci blog](http://blogs.msdn.com/b/vbertocci/)
- [Migrieren von einem Webprojekt VS2013 von WIF zu Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/)
- [Azure neue Hybridverbindungen nicht Ihres Vaters des \#hybridCloud](/documentation/videos/new-hybrid-connections-not-your-fathers-hybridcloud/)
- [Ähnlichkeiten zwischen Active Directory und Azure Active Directory](http://technet.microsoft.com/library/dn518177.aspx)
- [Verzeichnissynchronisierung mit Szenario für einmaliges Anmelden](http://technet.microsoft.com/library/dn441213.aspx)
- [Azure Active Directory unterstützt Token und Anspruchstypen](http://msdn.microsoft.com/library/azure/dn195587.aspx)

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]

[AZURE.INCLUDE [app-service-web-try-app-service](../../includes/app-service-web-try-app-service.md)]
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->