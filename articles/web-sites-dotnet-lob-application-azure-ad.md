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

In diesem Artikel erfahren Sie, wie Sie eine ASP.NET MVC-Branchenanwendung in [Azure App Service-Web-Apps](http://go.microsoft.com/fwlink/?LinkId=529714) mit [Azure Active Directory](/services/active-directory/) als Identitätsanbieter erstellen. Außerdem erfahren Sie, wie Sie die [Azure Active Directory Graph-Clientbibliothek](http://blogs.msdn.com/b/aadgraphteam/archive/2014/06/02/azure-active-directory-graph-client-library-1-0-publish.aspx) zum Abfragen von Verzeichnisdaten in der Anwendung verwenden.

Der verwendete Azure Active Directory-Mandant kann ein reines Azure-Verzeichnis haben oder mit Ihrem lokalen Active Directory (AD) synchronisiert sein, um für lokale oder Remote-Mitarbeiter die Möglichkeit einer einmaligen Anmeldung zu schaffen.

>[AZURE.NOTE] Mit dem Easy Auth-Feature in Azure App Service-Web-Apps können Sie eine einfache Authentifizierung für einen Azure Active Directory-Mandanten mit wenigen Mausklicks einrichten. Weitere Informationen finden Sie unter [Verwenden von Active Directory für die Authentifizierung in Azure App Service](web-sites-authentication-authorization.md).

<a name="bkmk_build"></a>
## Sie erstellen Folgendes

Sie erstellen eine einfache Branchenanwendung für Create-Read-Update-Delete (CRUD) in App Service-Web-Apps, die Arbeitsaufgaben mit folgenden Features erfasst:

- Authentifizieren von Benutzern mit Azure Active Directory
- Funktionen zum An- und Abmelden
- Verwenden von `[Authorize]` zum Autorisieren von Benutzern für verschiedene CRUD-Aktionen
- Abfragen von Azure Active Directory-Daten mit der [Azure Active Directory Graph-API](http://msdn.microsoft.com/library/azure/hh974476.aspx)
- Verwenden von [Microsoft.Owin](http://www.asp.net/aspnet/overview/owin-and-katana/an-overview-of-project-katana) (anstelle von Windows Identity Foundation, d. h. WIF). Dies ist das künftige ASP.NET und lässt sich wesentlich einfacher als WIF für Authentifizierung und Autorisierung einrichten.

<a name="bkmk_need"></a>
## Was Sie benötigen

[AZURE.INCLUDE [free-trial-note](../includes/free-trial-note.md)]

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

Sie benötigen Folgendes zum Bearbeiten dieses Lernprogramms:

- Einen Azure Active Directory-Mandanten mit Benutzern in verschiedenen Gruppen
- Berechtigungen zum Erstellen von Anwendungen auf dem Azure Active Directory-Mandanten
- Visual Studio 2013
- [Azure SDK 2.5.1](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409) oder höher

<a name="bkmk_sample"></a>
## Verwenden der Beispielanwendung für die Vorlage der Branchenanwendung ##

Die Beispielanwendung in diesem Lernprogramm [WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet) wurde vom Azure Active Directory-Team erstellt und kann ohne Weiteres als Vorlage zum Erstellen neuer Branchenanwendungen verwendet werden. Sie hat die folgenden integrierten Features:

- Verwendet [OpenID Connect](http://openid.net/connect/) zur Authentifizierung mit Azure Active Directory
- `Roles`-Controller, der einen Azure Active Directory-Suchfilter enthält und mit dem Sie Azure Active Directory-Benutzer oder -Gruppen problemlos Anwendungsrollen zuordnen können.
-  `TaskTracker`-Beispielcontroller, der veranschaulicht, wie Sie verschiedene Rollen für bestimmte Aktionen in der Anwendung autorisieren können, einschließlich der standardmäßigen Verwendung von `[Authorize]`. 

![](./media/web-sites-dotnet-lob-application-azure-ad/role-management.png)

<a name="bkmk_run" />
## Ausführen der Beispielanwendung ##

1.	Klonen Sie die Beispielprojektmappe unter [WebApp-GroupClaims-DotNet](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet), oder laden Sie sie in Ihr lokales Verzeichnis herunter.
2.	Folgen Sie die Anweisungen unter [README.md](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/master/README.md), um die Azure Active Directory-Anwendung und das Projekt einzurichten.

	> [AZURE.NOTE] Die in der Azure Active Directory-Anwendung konfigurierten Berechtigungen erfordern nur die Rolle <strong>Benutzer</strong>, nicht jedoch die Rolle **Globaler Administrator**.
	
3.	Drücken Sie nach der Konfiguration der Anwendung `F5`, um die Anwendung auszuführen.
4.	Wenn die Anwendung geladen ist, klicken Sie auf  **Anmelden**. 
5.	Wenn Sie die Azure Active Directory-Anwendung ordnungsgemäß konfiguriert und die entsprechenden Einstellungen in der Datei "Web.config" festgelegt haben, werden Sie zur Anmeldung weitergeleitet. Melden Sie sich mit dem Konto an, das Sie beim Erstellen der Azure Active Directory-Anwendung im Azure-Portal verwendet haben, da dies der Standardbesitzer der Azure Active Directory-Anwendung ist. 
	
	> [AZURE.NOTE] Beachten Sie, dass in "Startup.Auth.cs" des Beispielprojekts die Anwendung die <code>AddOwnerAdminClaim</code>-Methode enthält, mit der der Anwendungsbesitzer der Rolle "Admin" hinzugefügt wird. Auf diese Weise können Sie sofort mit der Verwaltung von Anwendungsrollen im <code>Roles</code> -Controller beginnen.
	
4.	Klicken Sie nach der Anmeldung auf **Rollen**, um Anwendungsrollen zu verwalten.
5.	Beginnen Sie unter **Nach Benutzern/Gruppen suchen** mit der Eingabe des gewünschten Benutzer- oder Gruppennamens. In einer Dropdownliste wird eine gefilterte Liste der Benutzer und/oder Gruppen Ihres Azure Active Directory-Mandanten angezeigt.

	![](./media/web-sites-dotnet-lob-application-azure-ad/select-user-group.png) 

	> [AZURE.NOTE] Sie werden in "Views\Roles\Index.cshtml" sehen, dass die Ansicht das JavaScript-Objekt <code>AadPicker</code> (definiert in "Scripts\AadPickerLibrary.js") für den Zugriff auf die <code>Suchen,</code>-Aktion im <code>Roles</code>-Controller verwendet.
		<pre class="prettyprint">var searchUrl = window.location.protocol + "//" + window.location.host + "<mark>/Roles/Search</mark>";
	...
    var picker = new <mark>AadPicker(searchUrl, maxResultsPerPage, input, token, tenant)</mark>;</pre>
		In "Controllers\RolesController.cs" sendet die <code>Search</code>-Aktion die eigentliche Anforderung an die Azure Active Directory Graph-API und gibt die Antwort an die Seite zurück.
		Später verwenden Sie dieselbe Methode, um einfache Funktionen in Ihrer Anwendung zu erstellen.

6.	Wählen Sie in der Dropdownliste einen Benutzer oder eine Gruppe aus, wählen Sie eine Rolle aus, und klicken Sie auf **Rolle zuweisen**.

<a name="bkmk_deploy"></a>
## Bereitstellen der Beispielanwendung in App Service-Web-Apps

Sie veröffentlichen nun die Anwendung in einer Web-App in Azure App Service. Die Datei [README.md](https://github.com/AzureADSamples/WebApp-GroupClaims-DotNet/blob/GroupClaims/README.md) enthält bereits Anweisungen für die Bereitstellung in App Services-Web-Apps, dadurch wird aber auch die Konfiguration für Ihre lokale Debugumgebung aufgehoben. Ich zeige Ihnen, wie eine Bereitstellung unter Beibehaltung der Debugkonfiguration möglich ist.

1. Klicken Sie mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **Veröffentlichen** aus.

	![](./media/web-sites-dotnet-lob-application-azure-ad/publish-app.png)

2. Wählen Sie **Microsoft Azure-Web-Apps**.
3. Wenn Sie sich nicht bei Azure angemeldet haben, klicken Sie auf **Anmelden**, und verwenden Sie zum Anmelden das Microsoft-Konto für Ihr Azure-Abonnement.
4. Klicken Sie nach der Anmeldung auf **Neu**, um eine neue Web-App in Azure zu erstellen.
5. Füllen Sie alle erforderlichen Felder aus. Sie benötigen eine Datenbankverbindung, damit diese Anwendung Rollenzuordnungen zwischengespeicherte Token und eine Anwendungsdaten speichern kann.

	![](./media/web-sites-dotnet-lob-application-azure-ad/4-create-website.png)

6. Klicken Sie auf **Erstellen**. Nachdem die Web-App erstellt wurde, wird das Dialogfeld "Web veröffentlichen" geöffnet.
7. Ändern Sie unter **Ziel-URL** den Eintrag **http** in **https**. Kopieren Sie die gesamte URL in einen Text-Editor. Sie werden sie später verwenden. Klicken Sie dann auf **Weiter**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/5-change-to-https.png)

8. Deaktivieren Sie das Kontrollkästchen **Enable Organizational Authentication**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/6-disable-organizational-authentication.png)

9. Um das Veröffentlichen im Web fortzusetzen, klicken Sie nicht auf **Veröffentlichen**, sondern auf **Schließen**. Klicken Sie auf **Ja**, um die Änderungen am Veröffentlichungsprofil zu speichern.
2. Navigieren Sie im [Azure-Verwaltungsportal](https://manage.windowsazure.com) zu Ihrem Azure Active Directory-Mandanten, und klicken Sie auf die Registerkarte **Anwendungen**.
2. Klicken Sie unten auf der Seite auf **Hinzufügen**.
3. Wählen Sie **Webanwendung und/oder Web-API**.
4. Benennen Sie die Anwendung, und klicken Sie auf **Weiter**.
5. Legen Sie unter "App-Eigenschaften" die Option **URL für Anmeldung** auf die Web-App-URL fest, die Sie zuvor gespeichert haben (z. B. `https://<site-name>.azurewebsites.net`), und legen Sie **APP-ID-URI** auf `https://<aad-tenanet-name>/<app-name>` fest. Klicken Sie danach auf **Abschließen**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/7-app-properties.png)

6. Nachdem die Anwendung erstellt wurde, klicken Sie auf **Konfigurieren**.
7. Erstellen Sie unter **Schlüssel** einen neuen Schlüssel, indem Sie in der Dropdownliste **1 Jahr** auswählen.
8. Wählen Sie unter **Berechtigungen für andere Anwendungen** für den Eintrag **Azure Active Directory** die Option **Access your organization's directory** in der Dropdownliste **Delegierte Berechtigungen** aus.

	> [AZURE.NOTE] Die genauen Berechtigungen, die Sie hier benötigen, hängen von der gewünschten Funktionalität Ihrer Anwendung ab. Für einige Berechtigungen muss die Rolle **Globaler Administrator** festgelegt werden. Die von diesem Lernprogramm benötigten Berechtigungen erfordern jedoch nur die Rolle **Benutzer**.

9.  Klicken Sie auf **Speichern**.  
10.  Bevor Sie die gespeicherte Konfigurationsseite verlassen, kopieren Sie die folgenden Informationen in einen Text-Editor.

	-	Client-ID
	-	Schlüssel (Wenn Sie die Seite verlassen, können Sie den Schlüssel nicht mehr sehen)

11. Öffnen Sie in Visual Studio die Datei **Web.Release.config** in Ihrem Projekt. Fügen Sie den folgenden XML-Code in das `<configuration>`-Tag ein, und ersetzen Sie den Wert der einzelnen Schlüssel durch die Informationen, die Sie für die neue Azure Active Directory-Anwendung gespeichert haben.  
	<pre class="prettyprint">
&lt;appSettings&gt;
   &lt;add key="ida:ClientId" value="<mark>[e.g. 82692da5-a86f-44c9-9d53-2f88d52b478b]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
   &lt;add key="ida:AppKey" value="<mark>[e.g. rZJJ9bHSi/cYnYwmQFxLYDn/6EfnrnIfKoNzv9NKgbo=]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
   &lt;add key="ida:PostLogoutRedirectUri" value="<mark>[e.g. https://mylobapp.azurewebsites.net/]</mark>" xdt:Transform="SetAttributes" xdt:Locator="Match(key)" /&gt;
&lt;/appSettings&gt;</pre>

	Stellen Sie sicher, dass der Wert von "ida:PostLogoutRedirectUri" mit einem Schrägstrich "/" endet.

1. Klicken Sie mit der rechten Maustaste erneut auf das Projekt, und wählen Sie **Veröffentlichen**.
2. Klicken Sie auf **Veröffentlichen**, um die Anwendung in der in Azure App Service-Web-App zu veröffentlichen.

Wenn Sie fertig sind, sind zwei Azure Active Directory-Anwendungen im Azure-Verwaltungsportal konfiguriert, eine für Ihre Debugumgebung in Visual Studio und eine für die veröffentlichte Web-App in Azure. Während des Debuggens werden die App-Einstellungen in der Datei "Web.config" verwendet, damit die **Debug**-Konfiguration in Azure Active Directory funktioniert. Bei der Veröffentlichung (standardmäßig wird die **Release**-Konfiguration veröffentlicht) wird eine transformierte Datei "Web.config" hochgeladen, welche die geänderten App-Einstellungen der Datei "Web.Release.config" enthält.

Wenn Sie die veröffentlichte Web-App dem Debugger anfügen möchten (d. h., Sie müssen Debugsymbole Ihres Codes in der veröffentlichten Web-App hochladen) können Sie einen Klon der Debug-Konfiguration für das Debuggen von Azure erstellen, jedoch mit einer eigenen benutzerdefinierten "Web.config"-Transformation (z. B. "Web.AzureDebug.config"), welche die Azure Active Directory-Einstellungen der Datei "Web.Release.config" verwendet. Auf diese Weise können Sie eine statische Konfiguration in verschiedenen Umgebungen beibehalten.

<a name="bkmk_crud"></a>
## Hinzufügen von Branchenfunktionen zur Beispielanwendung

In diesem Teil des Lernprogramms erfahren Sie, wie Sie die gewünschte Branchenfunktionalität basierend auf der Beispielanwendung erstellen. Sie erstellen einen einfachen CRUD-Arbeitsaufgaben-Tracker ähnlich dem TaskTracker-Controller, der jedoch das standardmäßige CRUD-Gerüst und -Entwurfsmuster verwendet. Außerdem verwenden Sie die enthaltene Datei "Scripts\AadPickerLibrary.js", um Ihre Anwendung mit Daten aus der Azure Active Directory Graph-API zu ergänzen.  

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

6.	Öffnen Sie die Datei "DAL\GroupClaimContext.cs", und fügen Sie den hervorgehobenen Code hinzu:  
	<pre class="prettyprint">
    public class GroupClaimContext : DbContext
    {
        public GroupClaimContext() : base("GroupClaimContext") { }

        public DbSet&lt;RoleMapping&gt; RoleMappings { get; set; }
        public DbSet&lt;Task&gt; Tasks { get; set; }
        <mark>public DbSet&lt;WorkItem&gt; WorkItems { get; set; }</mark>
        public DbSet&lt;TokenCacheEntry&gt; TokenCacheEntries { get; set; }
    }</pre>

7.	Erstellen Sie das Projekt, damit das neue Modell für die Gerüstlogik in Visual Studio zugänglich ist.
8.	Fügen Sie dem Ordner "Controllers" das neue Gerüstelement `WorkItemsController` hinzu. Klicken Sie dazu mit der rechten Maustaste auf **Controllers**, zeigen Sie auf **Hinzufügen**, und wählen Sie **New scaffolded item**. 
9.	Wählen Sie **MVC 5 Controller with views, using Entity Framework**, und klicken Sie auf **Hinzufügen**.
10.	Wählen Sie das soeben erstellte Modell aus, und klicken Sie auf **Hinzufügen**.

	![](./media/web-sites-dotnet-lob-application-azure-ad/8-add-scaffolded-controller.png)

9.	Öffnen der Datei "Controllers\WorkItemsController.cs"

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
        public async Task&lt;ActionResult&gt; Create([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult&gt; Edit(int? id)
        ...

        <mark>[Authorize(Roles = "Admin, Writer")]</mark>
        public async Task&lt;ActionResult&gt; Edit([Bind(Include = "ItemID,AssignedToID,AssignedToName,Description,Status")] WorkItem workItem)
        ...

        <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
        public async Task&lt;ActionResult&gt; Delete(int? id)
        ...

        <mark>[Authorize(Roles = "Admin, Writer, Approver")]</mark>
        public async Task&lt;ActionResult&gt; DeleteConfirmed(int id)
        ...
	}</pre>

	Da Sie die Rollenzuordnungen im Controller "Rollen" vornehmen, müssen Sie lediglich sicherstellen, dass jede Aktion die richtigen Rollen autorisiert.

	> [AZURE.NOTE] Möglicherweise haben Sie die Dekoration <code>[ValidateAntiForgeryToken]</code> bei einigen der Aktionen bemerkt. Aufgrund des Verhaltens, das von [Brock Allen](https://twitter.com/BrockLAllen) unter [MVC 4, AntiForgeryToken und Claims](http://brockallen.com/2012/07/08/mvc-4-antiforgerytoken-and-claims/) (MVC 4, AntiForgeryToken und Ansprüche, in englischer Sprache) beschrieben wurde, ist aus folgenden Gründen eventuell keine Fälschungsschutz-Überprüfung für Ihren HTTP-POST möglich:
	> + Azure Active Directory sendet keinen "http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider", der standardmäßig für das Fälschungsschutztoken erforderlich ist.
	> + Wenn Azure Active Directory mit AD FS synchronisiert ist, sendet die AD FS-Vertrauensstellung standardmäßig nicht den "http://schemas.microsoft.com/accesscontrolservice/2010/07/claims/identityprovider"-Anspruch, obwohl Sie AD FS manuell zum Senden dieser Anspruchs konfigurieren können.
	> Mit diesen Dingen werden Sie sich im nächsten Schritt befassen.

12.  Fügen Sie in der Datei "App_Start\Startup.Auth.cs" die folgende Codezeile in der `ConfigureAuth`-Methode hinzu:

		AntiForgeryConfig.UniqueClaimTypeIdentifier = ClaimTypes.NameIdentifier;
	
	`ClaimTypes.NameIdentifies` gibt den Anspruch `http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier` an, den Azure Active Directory bereitstellt. Da Sie nun die Autorisierung vorgenommen haben (die gewiss nicht lange gedauert hat), können Sie sich der eigentlichen Funktionalität der Aktionen zuwenden. 

13.	Fügen Sie unter Create() und Edit() den folgenden Code hinzu, um einige Variablen später in JavaScript zur Verfügung zu stellen:
            ViewData["token"] = GraphHelper.AcquireToken(ClaimsPrincipal.Current.FindFirst(Globals.ObjectIdClaimType).Value);
            ViewData["tenant"] = ConfigHelper.Tenant;
14.	Suchen Sie in "Views\WorkItems\Create.cshtml" (einem automatisch erstellten Gerüstelement) die Hilfsmethode `Html.BeginForm`, und ändern Sie sie folgendermaßen:  
	<pre class="prettyprint">@using (Html.BeginForm(<mark>"Create", "WorkItems", FormMethod.Post, new { id = "main-form" }</mark>))
	{
	    @Html.AntiForgeryToken()
	    
	    &lt;div class="form-horizontal"&gt;
	        &lt;h4&gt;WorkItem&lt;/h4&gt;
	        &lt;hr /&gt;
	        @Html.ValidationSummary(true, "", new { @class = "text-danger" })
	
	        &lt;div class="form-group"&gt;
	            &lt;div class="col-md-10"&gt;
	                @Html.EditorFor(model =&gt; model.AssignedToID, new { htmlAttributes = new { @class = "form-control"<mark>, @type=&quot;hidden&quot;</mark> } })
	                @Html.ValidationMessageFor(model =&gt; model.AssignedToID, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            @Html.LabelFor(model =&gt; model.AssignedToName, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10"&gt;
	                @Html.EditorFor(model =&gt; model.AssignedToName, new { htmlAttributes = new { @class = "form-control" } })
	                @Html.ValidationMessageFor(model =&gt; model.AssignedToName, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            @Html.LabelFor(model =&gt; model.Description, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10"&gt;
	                @Html.EditorFor(model =&gt; model.Description, new { htmlAttributes = new { @class = "form-control" } })
	                @Html.ValidationMessageFor(model =&gt; model.Description, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            @Html.LabelFor(model =&gt; model.Status, htmlAttributes: new { @class = "control-label col-md-2" })
	            &lt;div class="col-md-10"&gt;
	                @Html.EnumDropDownListFor(model =&gt; model.Status, htmlAttributes: new { @class = "form-control" })
	                @Html.ValidationMessageFor(model =&gt; model.Status, "", new { @class = "text-danger" })
	            &lt;/div&gt;
	        &lt;/div&gt;
	
	        &lt;div class="form-group"&gt;
	            &lt;div class="col-md-offset-2 col-md-10"&gt;
	                &lt;input type="submit" value="Create" class="btn btn-default" <mark>id="submit-button"</mark> /&gt;
	            &lt;/div&gt;
	        &lt;/div&gt;
	    &lt;/div&gt;
	
	    <mark>&lt;script&gt;
	            // People/Group Picker Code
	            var maxResultsPerPage = 14;
	            var searchUrl = window.location.protocol + "//" + window.location.host + "/Roles/Search";
	            var input = document.getElementById("AssignedToName");
	            var token = "@ViewData["token"]";
	            var tenant = "@ViewData["tenant"]";
	
	            var picker = new AadPicker(searchUrl, maxResultsPerPage, input, token, tenant);
	
	            // Submit the selected user/group to be asssigned.
	            $("#submit-button").click({ picker: picker }, function () {
	                if (!picker.Selected())
	                    return;
	                $("#main-form").get()[0].elements["AssignedToID"].value = picker.Selected().objectId;
	            });
	    &lt;/script&gt;</mark>
	
	}</pre>

	Im Skript durchsucht das "AadPicker"-Objekt die Aktion `~/Roles/Search` nach Azure Active Directory-Benutzern und -Gruppen, die der Eingabe entsprechen. Wenn Sie auf die Schaltfläche "Senden" klicken, speichert das "AadPicker"-Objekt die Benutzer-ID im verborgenen Feld `AssignedToID`.  

15. Führen Sie die App nun im Visual Studio-Debugger aus, oder veröffentlichen Sie sie in Azure App Service-Web-Apps. Melden Sie sich als Anwendungsbesitzer an, und navigieren Sie zu `~/WorkItems/Create`. Für meine veröffentlichte Branchenanwendung navigiere ich zu `https://mylobapp.azurewebsites.net/WorkItems/Create`. Sie sehen nun, dass Sie denselben "AadPicker"-Suchfilter zum Auswählen eines Azure Active Directory-Benutzers verwenden können.

	![](./media/web-sites-dotnet-lob-application-azure-ad/9-create-workitem.png)

16. Füllen Sie den Rest des Formulars aus, und klicken Sie auf **Erstellen**. Die Seite "~/WorkItems/Index" zeigt jetzt die neu erstellte Arbeitsaufgabe an. Darüber hinaus sehen Sie im Screenshot unten, dass ich die Spalte `AssignedToID` in der Datei "Views\WorkItems\Index.cshtml" entfernt habe. 

	![](./media/web-sites-dotnet-lob-application-azure-ad/10-workitem-index.png)

11.	Nehmen Sie jetzt entsprechende Änderungen an der Ansicht **Bearbeiten** vor. Nehmen Sie in der Datei "Views\WorkItems\Edit.cshtml" die Änderungen an der Hilfsmethode `Html.BeginForm` vor, die mit den Änderungen an "Views\WorkItems\Create.cshtml" im vorherigen Schritt identisch sind (ersetzen Sie "Create" im oben hervorgehobenen Code durch "Edit").

Das ist alles!

Nachdem Sie die Autorisierungen und die Branchenfunktionalität für die verschiedenen Aktionen im "WorkItems"-Controller konfiguriert haben, können Sie versuchen, sich als Benutzer mit unterschiedlichen Anwendungsrollen anzumelden.

![](./media/web-sites-dotnet-lob-application-azure-ad/11-edit-unauthorized.png)

<a name="bkmk_resources"></a>
## Weitere Ressourcen

- [Schützen der Anwendung durch SSL und das "Authorize"-Attribut](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md#protect-the-application-with-ssl-and-the-authorize-attribute)
- [Verwenden von Active Directory für die Authentifizierung in Azure App Service](web-sites-authentication-authorization.md)
- [Erstellen einer .NET MVC-Web-App in Azure App Service mit Azure Active Directory-Authentifizierung](web-sites-dotnet-lob-application-adfs.md)
- [Microsoft Azure Active Directory - Beispiele und Dokumentation](https://github.com/AzureADSamples)
- [Blog von Vittorio Bertocci](http://blogs.msdn.com/b/vbertocci/)
- [Migrate a VS2013 Web Project From WIF to Katana](http://www.cloudidentity.com/blog/2014/09/15/MIGRATE-A-VS2013-WEB-PROJECT-FROM-WIF-TO-KATANA/) (Migrieren eines VS2013-Webprojekts von WIF nach Katana, in englischer Sprache)
- [Azure's new Hybrid Connections not your father's #hybridCloud](Die neuen Hybridverbindungen von Azure - not your father's #hybridCloud, Video in englischer Sprache)(/documentation/videos/new-hybrid-connections-not-your-fathers-hybridcloud/)
- [Similarities between Active Directory and Azure AD](http://technet.microsoft.com/library/dn518177.aspx) (Ähnlichkeiten zwischen Active Directory und Azure Active Directory, in englischer Sprache)
- [DirSync mit einmaligem Anmelden](http://technet.microsoft.com/library/dn441213.aspx)
- [Unterstützte Token- und Anspruchstypen](http://msdn.microsoft.com/library/azure/dn195587.aspx)

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service and existing Azure services (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529714)
* Hinweise zu den Änderungen des neuen Portals gegenüber dem alten finden Sie unter: [Reference for navigating the preview portal (in englischer Sprache)](http://go.microsoft.com/fwlink/?LinkId=529715)


<!--HONumber=52-->