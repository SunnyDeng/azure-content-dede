<properties 
	pageTitle="Bereitstellen einer sicheren ASP.NET MVC-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website" 
	description="Erfahren Sie, wie Sie eine ASP.NET-MVC-5-Website mit einem SQL-Datenbank-Back-End entwickeln und in Azure bereitstellen." 
	services="web-sites, sql-database" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	writer="Rick-Anderson" 
	manager="wpickett" 
	editor="mollybos"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="1/28/2015" 
	ms.author="riande"/> 



# Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung mit Mitgliedschafts-, OAuth- und SQL-Datenbank für eine Azure-Website

In diesem Lernprogramm wird die Erstellung einer sicheren ASP.NET MVC 5-Webanwendung erläutert, mit der Benutzer sich mithilfe der Anmeldeinformationen von Facebook oder Google anmelden können. Darüber hinaus wird die Anwendung für Azure bereitgestellt.

Sie können ein kostenloses Azure-Konto erstellen. Wenn Sie noch nicht über Visual Studio 2013 verfügen, wird Visual Studio 2013 für Web Express automatisch durch das SDK installiert. Sie können kostenlos mit der Entwicklung für Azure beginnen.

Bei diesem Lernprogramm wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss dieses Lernprogramms verfügen Sie über eine sichere datengesteuerte Webanwendung, die unter Rückgriff auf eine Clouddatenbank in der Cloud ausgeführt wird.

Sie lernen Folgendes:

* Erstellen eines sicheren ASP.NET MVC 5-Projekts und Veröffentlichen dieses Projekts auf einer Azure-Website
* Verwenden von[ OAuth](http://oauth.net/ "http://oauth.net/") und der ASP.NET-Mitgliedschaftsdatenbank zum Sichern der Anwendung
* Verwenden einer SQL-Datenbank zum Speichern von Daten in Azure

Sie erstellen eine einfache Kontaktlisten-Webanwendung, die auf ASP.NET MVC 5 beruht und für den Datenbankzugriff ADO.NET Entity Framework verwendet. In der folgenden Abbildung wird die Anmeldeseite der fertiggestellten Anwendung dargestellt:

![Anmeldeseite][rxb]

>[AZURE.NOTE] Sie benötigen ein Microsoft Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie kein Konto haben, können Sie Ihre <a href="/de-de/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F" target="_blank">MSDN-Abonnentenvorteile aktivieren</a> oder <a href="/de-de/pricing/free-trial/?WT.mc_id=A261C142F" target="_blank">sich für eine kostenlose Testversion registrieren</a>. Wenn Sie Azure-Websites ausprobieren möchten, ehe Sie sich für ein Konto anmelden, besuchen Sie <a href="https://trywebsites.azurewebsites.net/">https://trywebsites.azurewebsites.net</a>. Auf dieser Seite können Sie sofort und kostenlos eine befristete ASP.NET Starter Site in Azure-Websites erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.


Teile des Lernprogramms:

- [Einrichten der Entwicklungsumgebung](#setupdevenv)
- [Erstellen einer ASP.NET MVC 5-Anwendung][createapplication]
- [Bereitstellen der Anwendung in Azure][deployapp1]
- [Hinzufügen einer Datenbank zur Anwendung][adddb]
- [Hinzufügen eines OAuth-Anbieters][]
- [Bereitstellen der Anwendung in Azure][deployapp11]
- [Nächste Schritte][]


[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

Sie müssen[ Visual Studio 2013 Update 3](http://go.microsoft.com/fwlink/?LinkId=390521) oder höher installieren, um das neue SSL-Zertifikat für den Localhost zu verwenden.

<h2><a name="bkmk_createmvc4app"></a>Erstellen einer ASP.NET MVC 5-Anwendung</h2>

### Erstellen des Projekts

1. Klicken Sie im Menü **Datei** auf **Neues Projekt**.

	!["Neues Projekt" im Menü "Datei"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/gs13newproj.png)

1. Erweitern Sie im Dialogfeld **Neues Projekt** den Eintrag **C#**, und wählen Sie unter **Installierte Vorlagen** die Option **Web** aus. Wählen Sie dann **ASP.NET-Webanwendung** aus.

1. Nennen Sie die Anwendung **ContactManager**, und klicken Sie auf **OK**.

	![Dialogfeld "Neues Projekt"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13newprojdb.png)
 
	**Hinweis:** Geben Sie "ContactManager" ein. In den Codeblöcken, die Sie später kopieren, wird davon ausgegangen, dass der Projektname "ContactManager" lautet. 

1. Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** die Vorlage **MVC** aus. Stellen Sie sicher, dass **Authentifizierung** auf **Einzelne Benutzerkonten* festgelegt, **Host in der Cloud** aktiviert und **Website** ausgewählt ist.

	![Dialogfeld "Neues ASP.NET-Projekt"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss1.PNG)

1. Der Konfigurations-Assistent schlägt einen eindeutigen Namen basierend auf  *ContactManager* vor (siehe Abbildung unten). Wählen Sie eine Region in Ihrer Nähe aus. Sie können [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com") verwenden, um das Rechenzentrum mit der niedrigsten Latenz zu suchen. 
2. Wenn Sie zuvor keine Datenbank erstellt haben, wählen Sie **Neuen Server erstellen** aus, und geben Sie einen Datenbankbenutzernamen und ein Kennwort ein.

	![Konfigurieren der Azure-Website](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configAz.PNG)

Wenn Sie über einen Datenbankserver verfügen, verwenden Sie diesen, um eine neue Datenbank zu erstellen. Datenbankserver sind eine wichtige Ressource, und in der Regel empfiehlt es sich, mehrere Datenbanken auf demselben Server für Tests und Entwicklung zu erstellen, anstatt einen Datenbankserver pro Datenbank zu erstellen. Vergewissern Sie sich, dass sich Website und Datenbank in derselben Region befinden.

![Konfigurieren der Azure-Website](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/configWithDB.PNG)

### Einrichten der Seitenkopf- und -fußzeile


1. Öffnen Sie im **Projektmappen-Explorer** im Ordner  *Views\Shared* die Datei *Layout.cshtml*.

	![_Layout.cshtml im Projektmappen-Explorer][newapp004]

1. Ersetzen Sie das Markup der Datei *Layout.cshtml* durch den folgenden Code. Die Änderungen sind unten hervorgehoben.

<pre>
			&lt;!DOCTYPE html&gt;
			&lt;html&gt;
			&lt;head&gt;
			    &lt;meta charset="utf-8" /&gt;
			    &lt;meta name="viewport" content="width=device-width, initial-scale=1.0"&gt;
			    &lt;title&gt;@ViewBag.Title - <mark>Contact Manager</mark>&lt;/title&gt;
			    @Styles.Render("~/Content/css")
			    @Scripts.Render("~/bundles/modernizr")
			
			&lt;/head&gt;
			&lt;body&gt;
			    &lt;div class="navbar navbar-inverse navbar-fixed-top"&gt;
			        &lt;div class="container"&gt;
			            &lt;div class="navbar-header"&gt;
			                &lt;button type="button" class="navbar-toggle" data-toggle="collapse" data-target=".navbar-collapse"&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                    &lt;span class="icon-bar"&gt;&lt;/span&gt;
			                &lt;/button&gt;
			                @Html.ActionLink("<mark>CM Demo</mark>", "Index", "<mark>Cm</mark>", new { area = "" }, new { @class = "navbar-brand" })
			            &lt;/div&gt;
			            &lt;div class="navbar-collapse collapse"&gt;
			                &lt;ul class="nav navbar-nav"&gt;
			                    &lt;li&gt;@Html.ActionLink("Home", "Index", "Home")&lt;/li&gt;
			                    &lt;li&gt;@Html.ActionLink("About", "About", "Home")&lt;/li&gt;
			                    &lt;li&gt;@Html.ActionLink("Contact", "Contact", "Home")&lt;/li&gt;
			                &lt;/ul&gt;
			                @Html.Partial("_LoginPartial")
			            &lt;/div&gt;
			        &lt;/div&gt;
			    &lt;/div&gt;
			    &lt;div class="container body-content"&gt;
			        @RenderBody()
			        &lt;hr /&gt;
			        &lt;footer&gt;
			            &lt;p&gt;&amp;copy; @DateTime.Now.Year - <mark>Contact Manager</mark>&lt;/p&gt;
			        &lt;/footer&gt;
			    &lt;/div&gt;
			
			    @Scripts.Render("~/bundles/jquery")
			    @Scripts.Render("~/bundles/bootstrap")
			    @RenderSection("scripts", required: false)
			&lt;/body&gt;
			&lt;/html&gt;
</pre>


![Änderungen am Code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs3.png)


### Lokales Ausführen der Anwendung

1. Drücken Sie STRG+F5, um die App auszuführen.

	Die Startseite der Anwendung wird im Standardbrowser angezeigt.

	![Lokal ausgeführte Website](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr2.png)

Darüber hinaus müssen Sie keine weiteren Aktionen ausführen, um die Anwendung zu erstellen, die Sie für Azure bereitstellen. 

## Aktivieren von SSL für das Projekt ##

1. Aktivieren Sie SSL. Klicken Sie im Projektmappen-Explorer auf das Projekt **ContactManager**, und drücken Sie dann F4, um das Eigenschaftsdialogfeld aufzurufen. Ändern Sie **SSL-aktiviert** in *true*. Kopieren Sie die **SSL-URL**. Die SSL-URL lautet https://localhost:44300/, sofern Sie nicht zuvor SSL-Websites erstellt haben.

	![Aktivieren von SSL][rxSSL]
 
1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **Contact Manager** und dann auf **Eigenschaften**.
1. Klicken Sie auf der linken Registerkarte auf **Web**.
1. Ändern Sie die **Projekt-URL** so, dass die **SSL-URL** verwendet wird, und speichern Sie die Seite (STRG+S).

	![SSL aktivieren](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr1.png)
 
1. Stellen Sie sicher, dass Internet Explorer von Visual Studio gestartet wird, wie in der folgenden Abbildung dargestellt:

	![Standardbrowser](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss12.PNG)

	In der Browserauswahl können Sie den Browser angeben, der von Visual Studio gestartet wird.

 	![Browserauswahl](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss13.png)

	Sie können mehrere Browser auswählen und die Browser von Visual Studio aktualisieren lassen, wenn Sie Änderungen vornehmen. Weitere Informationen finden Sie unter [Verwenden der Browserverknüpfung in Visual Studio 2013](http://www.asp.net/visual-studio/overview/2013/using-browser-link).


1. Drücken Sie STRG+F5, um die Anwendung auszuführen. Befolgen Sie die Anweisungen, um das von IIS Express generierte selbstsignierte Zertifikat als vertrauenswürdig einzustufen.

	 ![Anweisungen, um das von IIS Express generierte selbstsignierte Zertifikat als vertrauenswürdig einzustufen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss26.PNG)

1. Lesen Sie das Dialogfeld **Sicherheitswarnung**, und klicken Sie dann auf **Ja**, wenn Sie das Zertifikat installieren möchten, das **localhost** darstellt.

 	![IIS Express-Zertifikatwarnung für localhost](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss27.PNG)

1. Die Seite *Home* wird in IE angezeigt, und es gibt keine SSL-Warnungen.

	 ![IE mit Localhost-SSL, keine Warnungen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss28.PNG)

	Google Chrome akzeptiert das Zertifikat ebenfalls und zeigt HTTPS-Inhalt ohne Warnung an. Firefox verwendet einen eigenen Zertifikatspeicher, deshalb wird eine Warnung angezeigt.

	 ![Firefox-Zertifikatwarnung](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss30.PNG)

<h2><a name="bkmk_deploytowindowsazure1"></a>Bereitstellen der Anwendung in Azure</h2>

1. Klicken Sie im **Projektmappen-Explorer** von Visual Studio mit der rechten Maustaste auf das Projekt, und wählen Sie im Kontextmenü **Veröffentlichen** aus.

	!["Veröffentlichen" im Kontextmenü des Projekts](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
   Der Assistent **Web veröffentlichen** wird geöffnet.

1. Klicken Sie im Dialogfeld **Web veröffentlichen** auf **Veröffentlichen**.

	![Veröffentlichen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr3.png)

	Die erstellte Anwendung wird nun in der Cloud ausgeführt. Bei der nächsten Bereitstellung der Anwendung werden nur die geänderten (oder neuen) Dateien bereitgestellt.

	![In Cloud ausgeführt](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss4.PNG)

<h2><a name="bkmk_addadatabase"></a>Hinzufügen einer Datenbank zur Anwendung</h2>

Als Nächstes aktualisieren Sie die App, um Funktionen zum Anzeigen und Aktualisieren von Kontakten sowie zum Speichern der Daten in einer Datenbank hinzuzufügen. Die App verwendet das Entity Framework (EF), um die Datenbank zu erstellen und Daten zu lesen und zu aktualisieren.

### Hinzufügen von Datenmodellklassen für die Kontakte

Sie beginnen mit der Erstellung eines einfachen Datenmodells in Code.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner "Modelle", und klicken Sie dann auf **Hinzufügen** und anschließend auf **Klasse**.

	![Klasse hinzufügen im Kontextmenü des Ordners "Models"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr5.png)

2. Geben Sie der neuen Klassendatei im Dialogfeld **Neues Element hinzufügen** den Namen *Contact.cs*, und klicken Sie dann auf **Hinzufügen**.

	![Dialogfeld "Neues Element hinzufügen"][adddb002]

3. Ersetzen Sie den Inhalt der Datei "Contacts.cs" durch den folgenden Code.

        using System.ComponentModel.DataAnnotations;
        using System.Globalization;
        namespace ContactManager.Models
        {
            public class Contact
            {
                public int ContactId { get; set; }
                public string Name { get; set; }
                public string Address { get; set; }
                public string City { get; set; }
                public string State { get; set; }
                public string Zip { get; set; }
                [DataType(DataType.EmailAddress)]
                public string Email { get; set; }
            }
        }
Mit der Klasse **Contacts** werden die Daten definiert, die für die einzelnen Kontakte gespeichert werden, sowie ein Primärschlüssel (*ContactID*), der von der Datenbank benötigt wird.

### Erstellen von Webseiten, die Anwendungsbenutzern das Arbeiten mit den Kontakten ermöglichen

Mit dem ASP.NET MVC-Gerüstfeature kann automatisch Code generiert werden, der Erstellungs-, Lese-, Aktualisierungs- und Löschaktionen (CRUD) durchführt.

<h2><a name="bkmk_addcontroller"></a>Hinzufügen eines Controllers und einer Ansicht für die Daten</h2>

1. Erstellen Sie das Projekt **(STRG+UMSCHALT+B)**. (Sie müssen das Projekt vor der Verwendung des Gerüstmechanismus erstellen.) 
1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Controllers*, und klicken Sie dann auf **Hinzufügen** > **Controller**.

	![Controller hinzufügen im Kontextmenü des Ordners "Controllers"][addcode001]

5. Wählen Sie im Dialogfeld **Add Scaffold** die Option **MVC 5 Controller with views, using EF** aus, und klicken Sie dann auf **Hinzufügen**.
	
	![Dialogfeld "Gerüst hinzufügen"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr6.png)


1. Wählen Sie im Dropdownfeld **Modellklasse** die Option **Contact (ContactManager.Models)** aus. (Siehe Abbildung unten.)
1. Wählen Sie unter **Datenkontextklasse** die Option **ApplicationDbContext (ContactManager.Models)** aus. **ApplicationDbContext** wird für die Mitgliedschaftsdatenbank und die Kontaktdaten verwendet.
1. Geben Sie im Texteingabefeld **Controllername** die Zeichenfolge "CmController" als Controllernamen ein. 

	![Kontextdialogfeld "Neue Daten"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss5.PNG)

1. Klicken Sie auf **Hinzufügen**.

   In Visual Studio werden Controllermethoden und Ansichten für CRUD-Datenbankvorgänge für **Contact**-Objekte erstellt.

## Aktivieren von Migrationen, Erstellen der Datenbank, Hinzufügen von Beispieldaten und eines Dateninitialisierers ##

Die nächste Aufgabe besteht darin, das Feature [Code First-Migrationen](http://msdn.microsoft.com/library/hh770484.aspx) zu aktivieren, um die Datenbank basierend auf dem erstellten Datenmodell zu erstellen.

1. Wählen Sie im Menü **Extras** den Eintrag **NuGet Package Manager** und danach **Paket-Manager-Konsole** aus.
	!["Paket-Manager-Konsole" im Menü "Extras"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/SS6.png)

2. Geben Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl ein:

		enable-migrations
	Mit dem Befehl **enable-migrations** wird der Ordner *Migrations* erstellt und die Datei *Configuration.cs* darin abgelegt. Sie können diese Datei bearbeiten, um ein Seeding für die Datenbank auszuführen und Migrationen zu konfigurieren. 

2. Geben Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl ein:

		add-migration Initial


	Mit dem Befehl **add-migration-initial** wird eine Datei namens **&lt;Zeitstempel&gt;Initial** im Ordner *Migrations* generiert, mit der die Datenbank erstellt wird. Der erste Parameter ( **Initial** ) ist willkürlich und wird zum Erstellen des Dateinamens verwendet. Sie können die neue Klasse im **Projektmappen-Explorer** anzeigen.
	In der **Initial**-Klasse wird mit der **Up**-Methode die Tabelle "Contacts" erstellt und mit der **Down**-Methode (wird verwendet, wenn Sie zum vorherigen Status zurückkehren möchten) wieder verworfen.
3. Öffnen Sie die Datei *Migrations\Configuration.cs*. 
4. Fügen Sie den folgenden Namespace hinzu. 

    	 using ContactManager.Models;



5. Ersetzen Sie die *Seed*-Methode durch den folgenden Code:

        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            context.Contacts.AddOrUpdate(p => p.Name,
               new Contact
               {
                   Name = "Debra Garcia",
                   Address = "1234 Main St",
                   City = "Redmond",
                   State = "WA",
                   Zip = "10999",
                   Email = "debra@example.com",
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                }
                );
        }

	Mit diesem Code wird die Datenbank mit den Kontaktinformationen initialisiert (es wird ein Seeding dafür ausgeführt). Weitere Informationen zum Ausführen eines Seedings für die Datenbank finden Sie unter [Seeding and Debugging Entity Framework (EF) DBs](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


6. Geben Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl ein:

		update-database

	![Befehle in der Paket-Manager-Konsole][addcode009]

	Mit **update-database** wird die erste Migration ausgeführt, bei welcher die Datenbank erstellt wird. Standardmäßig wird die Datenbank als SQL Server Express LocalDB-Datenbank erstellt. 

7. Drücken Sie STRG+F5, um die Anwendung auszuführen, und klicken Sie dann auf den Link **CM Demo**, oder navigieren Sie zu http://localhost:(port#)/Cm. 

	In der Anwendung werden die Seeddaten angezeigt und Links zum Bearbeiten und Löschen und zu Details bereitgestellt. Sie können Daten erstellen, bearbeiten, löschen und anzeigen.

	![MVC-Ansicht der Daten][rx2]



<h2><a name="addOauth"></a>Hinzufügen eines OAuth2-Anbieters</h2>

[OAuth](http://oauth.net/ "http://oauth.net/") ist ein offenes Protokoll, das die sichere Autorisierung in einer einfachen und standardisierten Methode von Web-, Mobil- und Desktopanwendungen ermöglicht. OAuth wird von der ASP.NET MVC-Internetvorlage verwendet, um Facebook, Twitter, Google und Microsoft als Authentifizierungsanbieter verfügbar zu machen. Auch wenn in diesem Lernprogramm nur Google als Authentifizierungsanbieter eingesetzt wird, können Sie den Code problemlos für die Verwendung einer der anderen Anbieter anpassen. Die Schritte zur Implementierung anderer Anbieter unterscheiden sich kaum von den Schritten in diesem Lernprogramm. Informationen zum Verwenden von Facebook als Authentifizierungsanbieter finden Sie in meinem Lernprogramm [MVC 5 App with Facebook, Twitter, LinkedIn und Google OAuth2 anmelden ](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on).

Abgesehen von der Authentifizierung werden in diesem Lernprogramm auch Rollen verwendet, um die Autorisierung zu implementieren. Nur Benutzer, die Sie der Rolle *canEdit* hinzufügen, können Daten ändern (d. h. Kontakte erstellen, bearbeiten oder löschen).

Befolgen Sie die Anweisungen in meinem Lernprogramm [MVC 5 App with Facebook, Twitter, LinkedIn and Google OAuth2 Sign-on ](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on#goog)  unter **Creating a Google app for OAuth 2 to set up a Google app for OAuth2**. Führen Sie die App aus, und testen Sie, ob Sie sich mit der Google-Authentifizierung anmelden können.

<h2><a name="mbrDB"></a>Verwenden der Mitgliedschafts-API</h2>
In diesem Abschnitt fügen Sie der Mitgliedschaftsdatenbank einen lokalen Benutzer und die Rolle *canEdit* hinzu. Nur Benutzer mit der Rolle *canEdit* können Daten bearbeiten. Eine bewährte Methode besteht darin, Rollen nach den Aktionen zu benennen, die damit ausgeführt werden können. Entsprechend ist *canEdit* einer Rolle mit dem Namen *admin* vorzuziehen. Mit fortschreitender Fertigstellung der Anwendung können Sie neue Rollen wie *canDeleteMembers* anstelle von weniger aussagekräftigen Rollen wie *superAdmin* hinzufügen.

1. Öffnen Sie die Datei *migrations\configuration.cs*, und fügen Sie die folgenden `using`-Anweisungen hinzu:

        using Microsoft.AspNet.Identity;
        using Microsoft.AspNet.Identity.EntityFramework;

1. Fügen Sie der Klasse die folgende **AddUserAndRole**-Methode hinzu:

    
         bool AddUserAndRole(ContactManager.Models.ApplicationDbContext context)
         {
            IdentityResult ir;
            var rm = new RoleManager<IdentityRole>
                (new RoleStore<IdentityRole>(context));
            ir = rm.Create(new IdentityRole("canEdit"));
            var um = new UserManager<ApplicationUser>(
                new UserStore<ApplicationUser>(context));
            var user = new ApplicationUser()
            {
               UserName = "user1@contoso.com",
            };
            ir = um.Create(user, "P_assw0rd1");
            if (ir.Succeeded == false)
               return ir.Succeeded;
            ir = um.AddToRole(user.Id, "canEdit");
            return ir.Succeeded;
         }

1. Rufen Sie die neue Methode über die **Seed**-Methode auf:
	<pre>
        protected override void Seed(ContactManager.Models.ApplicationDbContext context)
        {
            <mark>AddUserAndRole(context);</mark>
            context.Contacts.AddOrUpdate(p => p.Name,
                // Code removed for brevity
        }
	</pre>  
<span></span>
	In den folgenden Abbildungen werden die Änderungen an der *Seed*-Methode gezeigt:

	![Codebild](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss24.PNG)

   Dieser Code erstellt eine neue Rolle namens *canEdit*, erstellt einen neuen lokalen Benutzer *user1@contoso.com*, und fügt *user1@contoso.com* der Rolle *canEdit* hinzu. Weitere Informationen finden Sie auf der [ASP.NET Identity-Ressourcenseite](http://curah.microsoft.com/55636/aspnet-identity).

## Verwenden von temporärem Code zum Hinzufügen neuer Benutzer mit Anmeldung zu sozialen Netzwerken zur Rolle "canEdit" ##
In diesem Abschnitt ändern Sie die **ExternalLoginConfirmation**-Methode im Kontocontroller vorübergehend, um neue Benutzer hinzuzufügen, die sich mit einem OAuth- oder OpenID-Anbieter bei der Rolle *canEdit* registrieren. Die **ExternalLoginConfirmation**-Methode wird automatisch geändert, um einer Administratorrolle neue Benutzer hinzuzufügen. Bis ein Tool zum Hinzufügen und Verwalten von Rollen bereitgestellt wird, verwenden wir den unten dargestellten Code zur temporären automatischen Registrierung. Es ist geplant, in Zukunft ein [WSAT](http://msdn.microsoft.com/library/ms228053.aspx) vergleichbares Tool bereitzustellen, mit dem Benutzerkonten und -rollen erstellt und bearbeitet werden können. An einer späteren Stelle in diesem Lernprogramm wird das Hinzufügen von Benutzern zu Rollen im **Server-Explorer** demonstriert.  

1. Öffnen Sie die Datei **Controllers\AccountController.cs**, und navigieren Sie zur **ExternalLoginConfirmation**-Methode.
1. Fügen Sie **AddToRoleAsync** unmittelbar vor dem Aufruf **SignInAsync** den folgenden Aufruf hinzu.

                await UserManager.AddToRoleAsync(user.Id, "canEdit");

   Mit dem oben dargestellten Code wird der neu registrierte Benutzer der Rolle "canEdit" hinzugefügt, sodass er auf Aktionsmethoden zugreifen kann, die das Ändern (Bearbeiten) von Daten erlauben. Nachfolgend wird die Codeänderung dargestellt:

   ![Code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss9.PNG)

An späterer Stelle in diesem Lernprogramm stellen Sie die Anwendung für Azure bereit. Die Anmeldung erfolgt dann über Google oder einen anderen Authentifizierungsdrittanbieter. Dadurch wird Ihr neu registriertes Konto der Rolle *canEdit* hinzugefügt. Jede Person, die die URL Ihrer Website findet und über eine Google-ID verfügt, kann sich dann registrieren und die Datenbank aktualisieren. Sie können die Website beenden, um dies zu verhindern. Durch eine Untersuchung der Datenbank können Sie überprüfen, wer der Rolle *canEdit* angehört.

Drücken Sie in der **Paket-Manager-Konsole** die NACH-OBEN-TASTE, um den folgenden Befehl aufzurufen:

		Update-Database

Führen Sie den Befehl **Update-Database** aus, mit dem die **Seed**-Methode ausgeführt wird. Daraufhin wird die gerade hinzugefügte **AddUserAndRole**-Methode ausgeführt. **AddUserAndRole** erstellt die Benutzerin *user1@contoso.com* und fügt sie der Rolle *canEdit* hinzu.

## Schützen der Anwendung durch SSL und das Attribut "Authorize" ##

In diesem Abschnitt wenden Sie das [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx)-Attribut an, um den Zugriff auf die Aktionsmethoden einzuschränken. Anonyme Benutzer können nur die **Index**-Aktionsmethode des Controllers Home anzeigen. Registrierten Benutzern werden die Kontaktdaten (die Seiten **Index** und **Details** des Controllers Cm) sowie die Info- und Kontaktseiten angezeigt. Nur Benutzer mit der Rolle  *canEdit* können auf Aktionsmethoden zugreifen, mit denen Daten geändert werden.

1. Fügen Sie der Anwendung die Filter [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) und [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) hinzu. Alternativ können Sie das [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx)-Attribut und das [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx)-Attribut jedem Controller hinzufügen, es wird jedoch als Best Practice im Bereich Sicherheit betrachtet, die Attribute der gesamten Anwendung hinzuzufügen. Auf diese Weise wird jeder neu hinzugefügte Controller und jede neu hinzugefügte Aktionsmethode automatisch geschützt und Sie müssen nicht daran denken, die Attribute anzuwenden. Weitere Informationen finden Sie unter [Sichern Ihrer ASP.NET MVC-App und das neue Attribut "AllowAnonymous"](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx). Öffnen Sie die Datei *App_Start\FilterConfig.cs*, und ersetzen Sie die  *RegisterGlobalFilters*-Methode durch den folgenden Code (damit werden die beiden Filter hinzugefügt):
		<pre>
        public static void
        RegisterGlobalFilters(GlobalFilterCollection filters)
        {
            filters.Add(new HandleErrorAttribute());
            <mark>filters.Add(new System.Web.Mvc.AuthorizeAttribute());
            filters.Add(new RequireHttpsAttribute());</mark>
        }
		</pre>




	Der im obigen Code angewendete Filter [Authorize](http://msdn.microsoft.com/library/system.web.mvc.authorizeattribute.aspx) verhindert, dass anonyme Benutzer auf Methoden in der Anwendung zugreifen. Sie verwenden das [AllowAnonymous](-Attributhttp://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx), um die Autorisierungsanforderung in einigen Methoden zu deaktivieren, sodass anonyme Benutzer sich anmelden und die Startseite anzeigen können. [RequireHttps](http://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) erfordert, dass der gesamte Zugriff auf die Webanwendung durch HTTPS erfolgen muss.

1. Fügen Sie der **Index**-Methode des Controllers "Home" das [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx)-Attribut hinzu. Mit dem [AllowAnonymous](http://blogs.msdn.com/b/rickandy/archive/2012/03/23/securing-your-asp-net-mvc-4-app-and-the-new-allowanonymous-attribute.aspx)-Attribut lassen Sie die Methoden zu, die von der Autorisierung ausgenommen werden sollen. Im Folgenden wird eine Abbildung eines Teils von HomeController angezeigt:	

  	![Code](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss11.PNG)

2. Führen Sie eine globale Suche nach *AllowAnonymous* durch. Sie werden feststellen, dass es in den Anmelde- und Registrierungsmethoden des Kontocontrollers verwendet wird.
1. Fügen Sie in der Datei *CmController.cs* den Code `[Authorize(Roles = "CanEdit")]` zu den HttpGet- und HttpPost-Methoden hinzu, mit denen Daten im Controller *Cm* geändert werden (Erstellen, Bearbeiten, Löschen, jede Aktionsmethode abgesehen von Index und Details). Im Folgenden wird ein Teil des fertigen Codes angezeigt: 

   	![Abbildung des Codes](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr11.png)



1. Wenn Sie immer noch von einer vorherigen Sitzung angemeldet sind, klicken Sie auf den Link **Abmelden**.
1. Klicken Sie auf den Link **About** oder **Contact**. Sie werden zur Anmeldeseite weitergeleitet, da anonyme Benutzer diese Seiten nicht anzeigen können. 
1. Klicken Sie auf den Link **Register as a new user**, und fügen Sie einen lokalen Benutzer mit der E-Mail-Adresse *joe@contoso.com* hinzu. Stellen Sie sicher, dass *Joe* die Seiten Home, About und Contact anzeigen kann. 

	![Anmeldung](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss14.PNG)

1. Klicken Sie auf den Link *CM Demo*, und überprüfen Sie, ob die Daten sichtbar sind. 
1. Klicken Sie auf einen Bearbeitungslink auf der Seite. Sie werden zur Anmeldeseite weitergeleitet (da der Rolle *canEdit* kein neuer lokaler Benutzer hinzugefügt wurde).
1. Melden Sie sich als *user1@contoso.com* mit dem Kennwort "P_assw0rd1" an (bei "0" in "word" handelt es sich um eine Null). Sie werden zu der Bearbeitungsseite weitergeleitet, die Sie zuvor ausgewählt haben. <br/>
   Wenn Sie sich nicht mit diesem Konto und Kennwort anmelden können, versuchen Sie, das Kennwort aus dem Quellcode zu kopieren und einzufügen. Sollten Sie sich dennoch nicht anmelden können, überprüfen Sie in der Tabelle **AspNetUsers** in der Spalte **UserName**, ob  *user1@contoso.com* wirklich hinzugefügt wurde. 

1. Stellen Sie sicher, dass Sie Daten ändern können.

<h2><a name="bkmk_deploytowindowsazure11"></a>Bereitstellen der Anwendung in Azure</h2>

1. Klicken Sie im **Projektmappen-Explorer** von Visual Studio mit der rechten Maustaste auf das Projekt, und wählen Sie im Kontextmenü **Veröffentlichen** aus.

	!["Veröffentlichen im Kontextmenü des Projekts][firsdeploy003]

	Der Assistent **Web veröffentlichen** wird geöffnet.

1. Klicken Sie im Dialogfeld **Web veröffentlichen** auf der linken Seite auf **Einstellungen**. Klicken Sie auf das Symbol **v**, um die **Remoteverbindungszeichenfolge** für **ApplicationDbContext** auszuwählen, und wählen Sie **ContactManagerNN_db** aus.

   
	![Einstellungen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc2.png)

1. Wählen Sie unter **ContactManagerContext** die Option **Execute Code First Migrations** aus.

	![Einstellungen](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrc3.png)

1. Klicken Sie auf **Veröffentlichen**.
1. Melden Sie sich als *user1@contoso.com* (mit dem Kennwort "P_assw0rd1") an, und überprüfen Sie, ob Sie die Daten bearbeiten können.
1. Melden Sie sich ab.
1. Wechseln Sie zur [Google Developers Console](https://console.developers.google.com/), und aktualisieren Sie auf der Registerkarte **Anmeldeinformationen ** die Umleitungs-URIs und JavaScript-Ursprünge so , dass die Azure-URL verwendet wird.
1. Melden Sie sich über Google oder Facebook an. Damit wird das Google- oder Facebook-Konto der Rolle **canEdit** hinzugefügt. Wenn ein HTTP 400-Fehler mit der folgenden Meldung angezeigt wird: *Der Umleitungs-URI in der Anforderung: https://contactmanager{my version}.azurewebsites.net/signin-google stimmt nicht mit einem registrierten Umleitungs-URI überein*, müssen Sie warten, bis die Änderungen verbreitet wurden. Wenn dieser Fehler nach mehr als einer Minute angezeigt wird, überprüfen Sie die URIs.

### Beenden der Website, um die Registrierung anderer Personen zu verhindern  

1. Wechseln Sie im **Server-Explorer** zu **Websites**.
4. Klicken Sie mit der rechten Maustaste auf jede Instanz der Website, und wählen Sie **Website anhalten**. 

	![Website anhalten](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr2.png) 

	Sie können die Website alternativ im Azure-Verwaltungsportal auswählen und dann unten auf der Seite auf das Symbol **Beenden** klicken.

	![Website anhalten](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr3.png)

### Entfernen von "AddToRoleAsync", "Publish" und "Test"

1. Kommentieren Sie den folgenden Code in der **ExternalLoginConfirmation**-Methode im Kontocontroller aus, oder entfernen Sie ihn: 
                `await UserManager.AddToRoleAsync(user.Id, "canEdit");`
1. Erstellen Sie das Projekt (die Dateiänderungen werden dabei gespeichert und es wird sichergestellt, dass keine Kompilierungsfehler vorhanden sind).
5. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

	   !["Veröffentlichen" im Kontextmenü des Projekts](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/GS13publish.png)
	
4. Klicken Sie auf die Schaltfläche **Vorschau starten**. Es werden nur die Dateien bereitgestellt, die aktualisiert werden müssen.
5. Starten Sie die Website in Visual Studio oder über das Portal. **Eine Veröffentlichung ist bei angehaltener Website nicht möglich**.

	![Website starten](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss15.png)

5. Kehren Sie zu Visual Studio zurück, und klicken Sie auf **Veröffentlichen**.
3. Die Azure-Anwendung wird im Standardbrowser geöffnet. Wenn Sie angemeldet sind, melden Sie sich ab, sodass Sie die Startseite als anonymer Benutzer anzeigen können.  
4. Klicken Sie auf den Link **About**. Sie werden zur Anmeldeseite weitergeleitet.
5. Klicken Sie auf der Anmeldeseite auf den Link **Registrieren**, und erstellen Sie ein lokales Konto. Dieses lokale Konto wird verwendet, um zu überprüfen, ob Sie auf die schreibgeschützten Seiten zugreifen können, jedoch nicht auf die Seiten mit Datenänderungen (die durch die Rolle *canEdit* geschützt sind). An späterer Stelle in diesem Lernprogramm wird der lokale Kontozugriff entfernt. 

	![Registrieren](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss16.PNG)

1. Stellen Sie sicher, dass Sie zu den Seiten *About* und *Contact* navigieren können.

	![Abmelden](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss17.PNG)

1. Klicken Sie auf den Link **CM Demo**, um zum Controller **Cm** zu navigieren. Alternativ können Sie *Cm* an die URL anhängen. 

	![CM-Seite](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr4.png)
 
1. Klicken Sie auf einen Bearbeitungslink. Sie werden zur Anmeldeseite weitergeleitet. Klicken Sie unter **Use another service to log in** auf Google oder Facebook, und melden Sie sich mit dem zuvor registrierten Konto an. (Wenn Sie schnell arbeiten und das Sitzungscookie noch nicht abgelaufen ist, werden Sie automatisch mit dem zuvor verwendeten Google- oder Facebook-Konto angemeldet.)
2. Stellen Sie sicher, dass Sie Daten bearbeiten können, während Sie beim Konto angemeldet sind.
 	**Hinweis:** Sie können sich von dieser App aus nicht von Google abmelden und mit demselben Browser bei einem anderen Google-Konto anmelden. Wenn Sie einen einzigen Browser verwenden, müssen Sie zu Google navigieren und sich abmelden. Sie können sich mit einem anderen Konto desselben Drittanbieterauthentifikators (wie Google) über einen anderen Browser anmelden.

Falls Sie Ihren Vornamen und Nachnamen im Google-Konto nicht ausgefüllt haben, tritt ein NullReferenceException-Ausnahmefehler auf.


## Überprüfen der SQL Azure-DB ##

1. Navigieren Sie im **Server-Explorer** zu **ContactDB**.
2. Klicken Sie mit der rechten Maustaste auf **ContactDB**, und wählen Sie **Öffnen in SQL Server-Objekt-Explorer** aus.
 
	![Öffnen in SSOX](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr12.png)
 
**Hinweis:** Wenn Sie **SQL-Datenbanken** nicht erweitern und **ContactDB** in Visual Studio *nicht* anzeigen können (*can't*), müssen Sie die folgenden Anweisungen befolgen, um einen Firewallport oder einen Portbereich zu öffnen. Befolgen Sie die Anweisungen unter **Einrichten von Azure-Firewallregeln**. Nach dem Hinzufügen der Firewallregel müssen Sie möglicherweise einige Minuten warten.
 
1. Klicken Sie mit der rechten Maustaste auf die Tabelle **AspNetUsers**, und wählen Sie **Daten anzeigen** aus.

	![CM-Seite](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr8.png)
 
1. Beachten Sie, dass sich die ID des Google-Kontos, bei dem Sie sich registriert haben, und die ID von *user1@contoso.com* in der Rolle **canEdit** befinden. Dies sollten die einzigen Benutzer in der Rolle **canEdit** sein. (Wird im nächsten Schritt überprüft.)

	![CM-Seite](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rrr9.png)
 
2. Klicken Sie im **SQL Server-Objekt-Explorer** mit der rechten Maustaste auf **AspNetUserRoles**, und wählen Sie** Daten anzeigen** aus.

	![CM-Seite](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)
 
Stellen Sie sicher, dass die **UserId**s von *user1@contoso.com* und dem registrierten Google-Konto stammen. 


## Einrichten von Azure-Firewallregeln ##

Führen Sie die Schritte in diesem Abschnitt aus, wenn Sie in Visual Studio keine Verbindung mit SQL Azure herstellen können oder die Fehlermeldung "Server kann nicht geöffnet werden" erhalten.

![Firewallfehler](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png)

Sie müssen Ihre IP-Adresse den zulässigen IPs hinzufügen.

1. Wählen Sie im Azure-Portal auf der linken Registerkarte **SQL-Datenbanken** aus.

	![SQL wählen][rx6]

1. Klicken Sie auf **ContactDB**.

1. Klicken Sie auf den Link **Azure-Firewallregeln für diese IP-Adresse einrichten**.

	![Firewall-Regeln][rx7]

1. Wenn die Frage "Die aktuelle IP-Adresse xxx.xxx.xxx.xxx ist in den vorhandenen Firewall-Regeln nicht enthalten.  Möchten Sie die Firewall-Regeln aktualisieren?" angezeigt wird, klicken Sie auf **Ja**. Das Hinzufügen dieser Adresse ist hinter einigen Unternehmensfirewalls oft nicht ausreichend, Sie müssen einen IP-Adressbereich hinzufügen.

Im nächsten Schritt wird ein Bereich zulässiger IP-Adressen hinzugefügt.

1. Klicken Sie im Azure-Portal auf **SQL-Datenbanken**.
1. Wählen Sie die Registerkarte **Server** aus, und klicken Sie dann auf den Server, den Sie konfigurieren möchten.

	![Registerkarte "Server" in Azure](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss25.PNG)

1. Klicken Sie auf die Registerkarte **Konfigurieren**.

1. Fügen Sie einen Regelnamen sowie die Start- und End-IP-Adresse hinzu.

	![IP-Bereich][rx9]

1. Klicken Sie unten auf der Seite auf **Speichern**.
1. Bitte hinterlassen Sie Feedback und teilen Sie mir mit, ob Sie einen IP-Adressbereich hinzufügen mussten, um die Verbindung herzustellen.

Schließlich können Sie über den SQL Server-Objekt-Explorer (SSOX) die Verbindung mit der SQL-Datenbank herstellen.

1. Klicken Sie im Menü *Ansicht* auf **SQL Server-Objekt-Explorer**.
1. Klicken Sie mit der rechten Maustaste auf **SQL Server**, und wählen Sie **Add SQL Server** aus.
1. Legen Sie im Dialogfeld **Mit Server verbinden** die **Authentifizierung** auf die Option **SQL Server-Authentifizierung** fest. Sie erhalten den **Servernamen** und die **Anmeldung** aus dem Azure-Portal.
1. Navigieren Sie im Browser zum Portal, und wählen Sie **SQL-Datenbanken** aus.
1. Wählen Sie **ContactDB** aus, und klicken Sie dann auf **SQL-Datenbank-Verbindungszeichenfolgen anzeigen**.
1. Kopieren Sie auf der Seite **Verbindungszeichenfolgen** den **Server** und die **Benutzer-ID**.
 
	![Verbindungszeichenfolge](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ss21.PNG)
1. Fügen Sie die Werte für **Server** und **Benutzer-ID** in das Dialogfeld **Mit Server verbinden** in Visual Studio ein. Der Wert **Benutzer-ID** wird in den Eintrag **Anmeldung** eingefügt. Geben Sie das Kennwort ein, das Sie zum Erstellen der SQL-Datenbank verwendet haben.

	![Dialogfeld "Mit Server verbinden"](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rss1.png)

Sie können nun mithilfe der oben genannten Anweisungen zur Kontaktdatenbank navigieren.


## So fügen Sie der Rolle "canEdit" durch die Bearbeitung von Datenbanktabellen einen Benutzer hinzu

An früherer Stelle in diesem Lernprogramm haben Sie Code verwendet, um Benutzer der Rolle "canEdit" hinzuzufügen. Alternativ dazu können Sie die Daten direkt in den Mitgliedschaftstabellen bearbeiten. In den folgenden Schritten wird gezeigt, wie mit dieser alternativen Methode ein Benutzer zu einer Rolle hinzugefügt wird.

2. Klicken Sie im **SQL Server-Objekt-Explorer** mit der rechten Maustaste auf **AspNetUserRoles**, und wählen Sie** Daten anzeigen** aus.

	![CM-Seite](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs1.png)

1. Kopieren Sie *RoleId*, und fügen Sie sie in die leere (neue) Zeile ein.
	
	![CM-Seite](./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rs2.png)
	
2. Suchen Sie in der Tabelle **AspNetUsers** den Benutzer, den Sie in die Rolle versetzen möchten, kopieren Sie die *Id* des Benutzers, und fügen Sie sie in die Spalte **UserId** der Tabelle **AspNetUserRoles** ein.

Zurzeit wird ein Tool entwickelt, das die Verwaltung von Benutzern und Rollen vereinfacht.


<h2><a name="nextsteps"></a>Nächste Schritte</h2>

Führen Sie die Lernprogramme aus, die auf diesem Beispiel basieren:

1.	[Erstellen einer sicheren ASP.NET MVC 5-Web-App mit Anmeldung, E-Mail-Bestätigung und Zurücksetzen des Kennworts](http://www.asp.net/mvc/overview/getting-started/create-an-aspnet-mvc-5-web-app-with-email-confirmation-and-password-reset)
2.	[ASP.NET MVC 5-App mit zweistufiger Authentifizierung über SMS und E-Mail](http://www.asp.net/mvc/overview/getting-started/aspnet-mvc-5-app-with-sms-and-email-two-factor-authentication)
3.	[Best Practices für das Bereitstellen von Kennwörtern und anderen vertraulichen Daten auf ASP.NET und Azure](http://www.asp.net/identity/overview/features-api/best-practices-for-deploying-passwords-and-other-sensitive-data-to-aspnet-and-azure)
4.	[Erstellen einer ASP.NET MVC 5-App mit Facebook und Google OAuth2](http://www.asp.net/mvc/tutorials/mvc-5/create-an-aspnet-mvc-5-app-with-facebook-and-google-oauth2-and-openid-sign-on ) Dieser Artikel enthält Anweisungen zum Hinzufügen von Profildaten zur Benutzerregistrierungsdatenbank sowie ausführliche Anweisungen zur Verwendung von Facebook als Authentifizierungsanbieter.
5.	[Erste Schritte mit ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)

Informationen zum Aktivieren der Anmeldeschaltflächen für soziale Netzwerke, die oben in diesem Lernprogramm dargestellt sind, finden Sie unter [Pretty social login buttons for ASP.NET MVC 5](http://www.beabigrockstar.com/pretty-social-login-buttons-for-asp-net-mvc-5/) (in englischer Sprache).

Im ausgezeichneten Artikel [Getting Started with EF and MVC](http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application)von Tom Dykstra erhalten Sie einen Einblick in die fortgeschrittene MVC- und EF-Programmierung.

Dieses Lernprogramm und die Beispielanwendung wurden von [Rick Anderson](http://blogs.msdn.com/b/rickandy/)(Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) sowie Tom Dykstra und Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)) geschrieben. 

***Bitte teilen Sie uns mit***, was Ihrer Meinung nach gelungen ist, bzw. verbessert werden kann, und zwar nicht nur bezüglich des Lernprogramms, sondern auch bezüglich der Produkte, die darin vorgestellt werden. Ihr Feedback unterstützt uns dabei, eine entsprechende Priorität für Verbesserungen festzulegen. Unter [Show Me How With Code](http://aspnet.uservoice.com/forums/228522-show-me-how-with-code) (in englischer Sprache) können Sie neue Themen anfordern oder eine Stimme dafür abgeben.

<!-- bookmarks -->
[Hinzufügen eines OAuth-Anbieters]: #addOauth
[Verwenden der Mitgliedschafts-API]:#mbrDB
[Erstellen eines Datenbereitstellungsskripts]:#ppd
[Aktualisieren der Mitgliedschaftsdatenbank]:#ppd2

[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[deployapp11]: #bkmk_deploytowindowsazure11
[adddb]: #bkmk_addadatabase


<!-- images-->
[rx2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx2.png

[rx5]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx5.png
[rx6]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx6.png
[rx7]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx7.png
[rx8]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx8.png
[rx9]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rx9.png

[rxb]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxb.png


[rxSSL]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxSSL.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT2]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT2.png

[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rxNOT]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxNOT.png
[rr1]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rr1.png

[rxPrevDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxPrevDB.png

[rxWSnew]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxWSnew2.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/rxCreateWSwithDB.png

[setup007]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-setup-azure-site-004.png

[newapp004]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-createapp-004.png

[firsdeploy003]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-deploy1-publish-001.png

[adddb002]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-controller-add-context-menu.png

[addcode008]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/dntutmobile-migrations-package-manager-console.png


[Wichtige Informationen zu ASP.NET in Azure-Websites]: #aspnetwindowsazureinfo
[Nächste Schritte]: #nextsteps

[ImportPublishSettings]: ./media/web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database-vs2013/ImportPublishSettings.png




























<!--HONumber=42-->
