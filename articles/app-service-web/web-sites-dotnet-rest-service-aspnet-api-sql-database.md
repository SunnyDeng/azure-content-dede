<properties 
	pageTitle="Erstellen eines REST-Diensts mithilfe der ASP.NET Web API und einer SQL-Datenbank in Azure App Service" 
	description="In diesem Tutorial erfahren Sie, wie Sie eine App bereitstellen, die die ASP.NET-Web-API mithilfe von Visual Studio für eine Azure-Web-App verwendet." 
	services="app-service\web" 
	documentationCenter=".net" 
	authors="Rick-Anderson" 
	writer="Rick-Anderson" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/31/2015" 
	ms.author="riande"/>

# Erstellen eines REST-Diensts mithilfe der ASP.NET Web API und einer SQL-Datenbank in Azure App Service

In diesem Tutorial wird die Bereitstellung einer ASP.NET-Web-App für einen [Azure App Service](http://go.microsoft.com/fwlink/?LinkId=529714) mit dem Assistenten "Web veröffentlichen" in Visual Studio 2013 oder Visual Studio 2013 for Web Express gezeigt.

Sie können ein kostenloses Azure-Konto erstellen. Wenn Sie noch nicht über Visual Studio 2013 verfügen, wird Visual Studio 2013 for Web Express automatisch durch das SDK installiert. Auf diese Weise können Sie völlig kostenlos mit der Entwicklung für Azure beginnen.

Bei diesem Tutorial wird davon ausgegangen, dass Sie noch keine Erfahrung mit der Verwendung von Azure haben. Nach Abschluss dieses Tutorials verfügen Sie über eine einfache Web-App, die in der Cloud ausgeführt wird.
 
Sie lernen Folgendes:

* Ermöglichen der Azure-Entwicklung auf Ihrem Computer durch Installieren des Azure SDK
* Erstellen eines Visual Studio ASP.NET MVC 5-Projekts und Veröffentlichen dieses Projekts in einer Azure-App.
* Verwenden des ASP.NET Web-API zum Aktivieren von Restful API-Aufrufen
* Verwenden einer SQL-Datenbank zum Speichern von Daten in Azure
* Veröffentlichen von Anwendungsaktualisierungen in Azure

Sie erstellen eine einfache Kontaktlisten-Webanwendung, die auf ASP.NET MVC 5 beruht und für den Datenbankzugriff ADO.NET Entity Framework verwendet. In der folgenden Abbildung wird die fertige Anwendung dargestellt:

![Screenshot von Website][intro001]

<!-- the next line produces the "Set up the development environment" section as see at http://azure.microsoft.com/documentation/articles/web-sites-dotnet-get-started/ -->
[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

### Erstellen des Projekts

1. Starten Sie Visual Studio 2013.
1. Klicken Sie im Menü **Datei** auf **Neues Projekt**.
3. Erweitern Sie im Dialogfeld **Neues Projekt** den Eintrag **Visual C#**, und wählen Sie die Option **Web** aus. Wählen Sie dann **ASP.NET MVC 5-Webanwendung** aus. Nennen Sie die Anwendung **ContactManager**, und klicken Sie auf **OK**.

	![Dialogfeld "Neues Projekt"](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr4.PNG)]

1. Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** die Vorlage **MVC** und **Web API** aus, und klicken Sie dann auf **Authentifizierung ändern**.

	![Dialogfeld "Neues ASP.NET-Projekt"](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt3.PNG)

1. Klicken Sie im Dialogfeld **Authentifizierung ändern** auf **Keine Authentifizierung** und dann auf **OK**.

	![Keine Authentifizierung](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/GS13noauth.png)

	Die erstellte Beispielanwendung enthält keine Features, die eine Benutzeranmeldung erfordern. Informationen zum Implementieren von Authentifizierungs- und Autorisierungsfeatures finden Sie im Abschnitt [Nächste Schritte](#nextsteps) am Ende dieses Lernprogramms.

1. Klicken Sie im Dialogfeld **Neues ASP.Net-Projekt** auf **OK**.

	![Dialogfeld "Neues ASP.NET-Projekt"](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt3.PNG)

Wenn Sie sich noch nicht in Azure angemeldet haben, werden Sie dazu aufgefordert.

1. Der Konfigurationsassistent einen eindeutigen Namen schlägt basierend auf *ContactManager* vor (siehe Bild unten). Wählen Sie eine Region in Ihrer Nähe. Über [azurespeed.com](http://www.azurespeed.com/ "AzureSpeed.com") können Sie das Rechenzentrum mit der niedrigsten Latenz suchen. 
2. Wenn Sie zuvor noch keinen Datenbankserver erstellt haben, wählen Sie **Neuen Server erstellen** aus, und geben Sie einen Namen und ein Kennwort für den Datenbankbenutzer ein.

	![Konfigurieren von Azure Websites](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/configAz.PNG)

Wenn Sie über einen Datenbankserver verfügen, verwenden Sie diesen, um eine neue Datenbank zu erstellen. Datenbankserver sind wertvolle Ressourcen. Sie sollten in der Regel zwecks Test und Entwicklung mehrere Datenbanken auf demselben Server erstellen, anstatt einen Datenbankserver pro Datenbank zu erstellen. Vergewissern Sie sich, dass sich Website und Datenbank in derselben Region befinden.

![Konfigurieren von Azure Websites](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/configWithDB.PNG)

### Einrichten der Seitenkopf- und -fußzeile


1. Erweitern Sie im **Projektmappen-Explorer** den Ordner *Views\\Shared*, und öffnen Sie die Datei "*\_Layout.cshtml*".

	![\_Layout.cshtml in Solution Explorer][newapp004]

1. Ersetzen Sie den Inhalt der Datei *Views\\Shared\_Layout.cshtml* durch den folgenden Code.


		<!DOCTYPE html>
		<html lang="en">
		<head>
		    <meta charset="utf-8" />
		    <title>@ViewBag.Title - Contact Manager</title>
		    <link href="~/favicon.ico" rel="shortcut icon" type="image/x-icon" />
		    <meta name="viewport" content="width=device-width" />
		    @Styles.Render("~/Content/css")
		    @Scripts.Render("~/bundles/modernizr")
		</head>
		<body>
		    <header>
		        <div class="content-wrapper">
		            <div class="float-left">
		                <p class="site-title">@Html.ActionLink("Contact Manager", "Index", "Home")</p>
		            </div>
		        </div>
		    </header>
		    <div id="body">
		        @RenderSection("featured", required: false)
		        <section class="content-wrapper main-content clear-fix">
		            @RenderBody()
		        </section>
		    </div>
		    <footer>
		        <div class="content-wrapper">
		            <div class="float-left">
		                <p>&copy; @DateTime.Now.Year - Contact Manager</p>
		            </div>
		        </div>
		    </footer>
		    @Scripts.Render("~/bundles/jquery")
		    @RenderSection("scripts", required: false)
		</body>
		</html>
			
Das Markup oben ändert den App-Namen von "My ASP.NET App" auf "Contact Manager" und entfernt die Links auf **Home**, **About** und **Contact**.

### Lokales Ausführen der Anwendung

1. Drücken Sie STRG+F5, um die Anwendung auszuführen. Die Startseite der Anwendung wird im Standardbrowser angezeigt. ![Homepage To-Do-Liste](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr5.PNG)

Darüber hinaus müssen Sie keine weiteren Aktionen ausführen, um die Anwendung zu erstellen, die Sie für Azure bereitstellen. Zu einem späteren Zeitpunkt fügen Sie Datenbankfunktionalität hinzu.

## Bereitstellen der Anwendung in Azure

1. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf das Projekt, und wählen Sie im Kontextmenü **Veröffentlichen** aus.

	!["Veröffentlichen" im Kontextmenü des Projekts][PublishVSSolution]

	Der Assistent **Web veröffentlichen** wird geöffnet.

12. Klicken Sie auf **Veröffentlichen**.

![Registerkarte "Einstellungen"](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/pw.png)

Die Dateien werden von Visual Studio auf den Azure-Server kopiert. Im Fenster **Ausgabe** wird angezeigt, welche Bereitstellungsaktionen ausgeführt wurden, und es wird die erfolgreiche Durchführung der Bereitstellung gemeldet.

14. Der Standard-Browser öffnet automatisch die URL der bereitgestellten Seite.

	Die erstellte Anwendung wird nun in der Cloud ausgeführt.
	
	![To-Do-List-Homepage in Azure][rxz2]

## Hinzufügen einer Datenbank zur Anwendung

Als Nächstes aktualisieren Sie die MVC-Anwendung, um Funktionen zum Anzeigen und Aktualisieren von Kontakten sowie Speichern der Daten in einer Datenbank hinzuzufügen. Die Anwendung verwendet Entity Framework, um die Datenbank zu erstellen und Daten in der Datenbank zu lesen und zu aktualisieren.

### Hinzufügen von Datenmodellklassen für die Kontakte

Sie beginnen mit der Erstellung eines einfachen Datenmodells in Code.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Models**, und klicken Sie dann auf **Hinzufügen** > **Klasse**.

	![Klasse hinzufügen im Kontextmenü des Ordners "Models"][adddb001]

2. Geben Sie der neuen Klassendatei im Dialogfeld **Neues Element hinzufügen** den Namen *Contact.cs*, und klicken Sie dann auf **Hinzufügen**.

	![Dialogfeld "Neues Element hinzufügen"][adddb002]

3. Ersetzen Sie den Inhalt der Datei Contacts.cs durch den folgenden Code.

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
				public string Email { get; set; }
				public string Twitter { get; set; }
				public string Self
        		{
            		get { return string.Format(CultureInfo.CurrentCulture,
				         "api/contacts/{0}", this.ContactId); }
            		set { }
        		}
    		}
		}

Mit der Klasse **Contacts** werden die Daten definiert, die für die einzelnen Kontakte gespeichert werden, sowie ein Primärschlüssel ("ContactID"), der von der Datenbank benötigt wird. Weitere Informationen zu Datenmodellen erhalten Sie unter [Nächste Schritte](#nextsteps) am Ende dieses Lernprogramms.

### Erstellen von Webseiten, die Anwendungsbenutzern das Arbeiten mit den Kontakten ermöglichen

Mit dem ASP.NET MVC-Gerüstfeature kann automatisch Code generiert werden, der Erstellungs-, Lese-, Aktualisierungs- und Löschaktionen (CRUD) durchführt.

## Hinzufügen eines Controllers und einer Ansicht für die Daten

1. Zeigen Sie im **Projektmappen-Explorer** den Ordner "Controllers" an.

3. Erstellen Sie das Projekt **(STRG+UMSCHALT+B)**. (Sie müssen das Projekt vor der Verwendung des Gerüstmechanismus erstellen.)

4. Klicken Sie mit der rechten Maustaste auf den Ordner "Controllers", und klicken Sie anschließend auf **Hinzufügen** und **Controller**.

	![Controller hinzufügen im Kontextmenü des Ordners "Controllers"][addcode001]

1. Wählen Sie im Dialogfeld **Gerüst hinzufügen** die Option **MVC-Controller mit Ansichten mithilfe von Entity Framework** aus, und klicken Sie dann auf **Hinzufügen**.

 ![Controller hinzufügen](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rrAC.PNG)

6. Wählen Sie als Bezeichnung für den Controller **HomeController**. Wählen Sie **Contact** als Modellklasse. Klicken Sie auf **Neuer Datenkontext** und bestätigen Sie für **Neuer Datenkontexttyp** die Standardeinstellung "ContactManager.Models.ContactManagerContext". Klicken Sie auf **Hinzufügen**.

	![Dialogfeld "Controller" hinzufügen](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rr9.PNG)

	In einem Dialogfeld wird die Meldung angezeigt: "Es besteht bereits eine Datei mit der Bezeichnung "HomeController". Möchten Sie sie ersetzen?" Klicken Sie auf **Ja**. Der erstellte HomeController wird mit dem neuen Projekt überschrieben. Der neue HomeController wird für die Kontaktliste verwendet.

	In Visual Studio werden Controllermethoden und Ansichten für CRUD-Datenbankvorgänge für **Contact**-Objekte erstellt.

## Aktivieren von Migrationen, Erstellen der Datenbank, Hinzufügen von Beispieldaten und eines Dateninitialisierers ##

Die nächste Aufgabe besteht darin, das Feature [Code First-Migrationen](http://curah.microsoft.com/55220) zu aktivieren, um die Datenbank basierend auf dem erstellten Datenmodell zu erstellen.

1. Wählen Sie im Menü **Extras** den Eintrag **Library Package Manager** und danach **Paket-Manager-Konsole** aus.

	!["Paket-Manager-Konsole" im Menü "Extras"][addcode008]

2. Geben Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl ein:

		enable-migrations 
  
	Mit dem Befehl **enable-migrations** wird der Ordner *Migrations* erstellt und die Datei *Configuration.cs* darin abgelegt. Sie können diese Datei bearbeiten, um "Migrations" zu konfigurieren.

2. Geben Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl ein:

		add-migration Initial

	Mit dem Befehl **add-migration Initial** wird eine Klasse namens **&lt;date\_stamp&gt;Initial** generiert, mit der die Datenbank erstellt wird. Der erste Parameter (*Initial*) ist willkürlich und wird zum Erstellen des Dateinamens verwendet. Sie können die neue Klasse im **Projektmappen-Explorer** anzeigen.

	In der **Initial**-Klasse wird mit der **Up**-Methode die Tabelle **Contacts** erstellt und mit der Down-Methode (wird verwendet, wenn Sie zum vorherigen Status zurückkehren möchten) wieder verworfen.

3. Öffnen Sie die Datei *Migrations\\Configuration.cs*.

4. Fügen Sie die folgenden Namespaces hinzu.

    	 using ContactManager.Models;

5. Ersetzen Sie die *Seed*-Methode durch den folgenden Code:
		
        protected override void Seed(ContactManager.Models.ContactManagerContext context)
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
                   Twitter = "debra_example"
               },
                new Contact
                {
                    Name = "Thorsten Weinrich",
                    Address = "5678 1st Ave W",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "thorsten@example.com",
                    Twitter = "thorsten_example"
                },
                new Contact
                {
                    Name = "Yuhong Li",
                    Address = "9012 State st",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "yuhong@example.com",
                    Twitter = "yuhong_example"
                },
                new Contact
                {
                    Name = "Jon Orton",
                    Address = "3456 Maple St",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "jon@example.com",
                    Twitter = "jon_example"
                },
                new Contact
                {
                    Name = "Diliana Alexieva-Bosseva",
                    Address = "7890 2nd Ave E",
                    City = "Redmond",
                    State = "WA",
                    Zip = "10999",
                    Email = "diliana@example.com",
                    Twitter = "diliana_example"
                }
                );
        }

	Mit diesem Code wird die Datenbank mit den Kontaktinformationen initialisiert. Weitere Informationen zum Ausführen eines Seedings für die Datenbank finden Sie unter [Debugging Entity Framework (EF) DBs](http://blogs.msdn.com/b/rickandy/archive/2013/02/12/seeding-and-debugging-entity-framework-ef-dbs.aspx).


1. Geben Sie im Fenster **Paket-Manager-Konsole** den folgenden Befehl ein:

		update-database

	![Befehle in der Paket-Manager-Konsole][addcode009]

	Mit **update-database** wird die erste Migration ausgeführt, wodurch die Datenbank erstellt wird. Standardmäßig wird die Datenbank als SQL Server Express LocalDB-Datenbank erstellt.

1. Drücken Sie STRG+F5, um die Anwendung auszuführen.

In der Anwendung werden die Seeddaten angezeigt und Links zum Bearbeiten und Löschen und zu Details bereitgestellt.

![MVC-Datenansicht][rxz3]

## Bearbeiten der Anzeige

1. Öffnen Sie die Datei *Views\\Home\\Index.cshtml*. Im nächsten Schritt ersetzen wir das generierte Markup mit Code, der [jQuery](http://jquery.com/) und [Knockout.js](http://knockoutjs.com/) verwendet. Dieser neue Code ruft die Liste der Kontakte mithilfe von Web API und JSON ab und bindet die Kontaktdaten mithilfe von knockout.js an das UI. Weitere Informationen finden Sie unter [Nächste Schritte](#nextsteps) am Ende dieses Lernprogramms. 


2. Ersetzen Sie den Inhalt der Datei durch den folgenden Code.

		@model IEnumerable<ContactManager.Models.Contact>
		@{
		    ViewBag.Title = "Home";
		}
		@section Scripts {
		    @Scripts.Render("~/bundles/knockout")
		    <script type="text/javascript">
		        function ContactsViewModel() {
		            var self = this;
		            self.contacts = ko.observableArray([]);
		            self.addContact = function () {
		                $.post("api/contacts",
		                    $("#addContact").serialize(),
		                    function (value) {
		                        self.contacts.push(value);
		                    },
		                    "json");
		            }
		            self.removeContact = function (contact) {
		                $.ajax({
		                    type: "DELETE",
		                    url: contact.Self,
		                    success: function () {
		                        self.contacts.remove(contact);
		                    }
		                });
		            }

		            $.getJSON("api/contacts", function (data) {
		                self.contacts(data);
		            });
		        }
		        ko.applyBindings(new ContactsViewModel());	
		</script>
		}
		<ul id="contacts" data-bind="foreach: contacts">
		    <li class="ui-widget-content ui-corner-all">
		        <h1 data-bind="text: Name" class="ui-widget-header"></h1>
		        <div><span data-bind="text: $data.Address || 'Address?'"></span></div>
		        <div>
		            <span data-bind="text: $data.City || 'City?'"></span>,
		            <span data-bind="text: $data.State || 'State?'"></span>
		            <span data-bind="text: $data.Zip || 'Zip?'"></span>
		        </div>
		        <div data-bind="if: $data.Email"><a data-bind="attr: { href: 'mailto:' + Email }, text: Email"></a></div>
		        <div data-bind="ifnot: $data.Email"><span>Email?</span></div>
		        <div data-bind="if: $data.Twitter"><a data-bind="attr: { href: 'http://twitter.com/' + Twitter }, text: '@@' + Twitter"></a></div>
		        <div data-bind="ifnot: $data.Twitter"><span>Twitter?</span></div>
		        <p><a data-bind="attr: { href: Self }, click: $root.removeContact" class="removeContact ui-state-default ui-corner-all">Remove</a></p>
		    </li>
		</ul>
		<form id="addContact" data-bind="submit: addContact">
		    <fieldset>
		        <legend>Add New Contact</legend>
		        <ol>
		            <li>
		                <label for="Name">Name</label>
		                <input type="text" name="Name" />
		            </li>
		            <li>
		                <label for="Address">Address</label>
		                <input type="text" name="Address" >
		            </li>
		            <li>
		                <label for="City">City</label>
		                <input type="text" name="City" />
		            </li>
		            <li>
		                <label for="State">State</label>
		                <input type="text" name="State" />
		            </li>
		            <li>
		                <label for="Zip">Zip</label>
		                <input type="text" name="Zip" />
		            </li>
		            <li>
		                <label for="Email">E-mail</label>
		                <input type="text" name="Email" />
		            </li>
		            <li>
		                <label for="Twitter">Twitter</label>
		                <input type="text" name="Twitter" />
		            </li>
		        </ol>
		        <input type="submit" value="Add" />
		    </fieldset>
		</form>

3. Klicken Sie mit der rechten Maustaste auf den Ordner "Content", und klicken Sie anschließend auf **Hinzufügen** und **Neues Element**.

	![Hinzufügen eines Stylesheets im Ordner "Content" im Kontextmenü][addcode005]

4. Geben Sie im Dialogfeld **Neues Element hinzufügen** im Suchfeld oben rechts **Style** ein, und wählen Sie anschließend **Stylesheet** aus. ![Dialogfeld "Neues Element hinzufügen"][rxStyle]

5. Nennen Sie die Datei *Contacts.css* und klicken Sie auf **Hinzufügen**. Ersetzen Sie den Inhalt der Datei durch den folgenden Code.
    
        .column {
            float: left;
            width: 50%;
            padding: 0;
            margin: 5px 0;
        }
        form ol {
            list-style-type: none;
            padding: 0;
            margin: 0;
        }
        form li {
            padding: 1px;
            margin: 3px;
        }
        form input[type="text"] {
            width: 100%;
        }
        #addContact {
            width: 300px;
            float: left;
            width:30%;
        }
        #contacts {
            list-style-type: none;
            margin: 0;
            padding: 0;
            float:left;
            width: 70%;
        }
        #contacts li {
            margin: 3px 3px 3px 0;
            padding: 1px;
            float: left;
            width: 300px;
            text-align: center;
            background-image: none;
            background-color: #F5F5F5;
        }
        #contacts li h1
        {
            padding: 0;
            margin: 0;
            background-image: none;
            background-color: Orange;
            color: White;
            font-family: Trebuchet MS, Tahoma, Verdana, Arial, sans-serif;
        }
        .removeContact, .viewImage
        {
            padding: 3px;
            text-decoration: none;
        }

	Wir verwenden dieses Stylesheet für das Layout, die Farben und die Stile, die in der App "Content Manager" verwendet werden.

6. Öffnen Sie die Datei *App\_Start\\BundleConfig.cs*.


7. Fügen Sie den folgenden Code hinzu, um das [Knockout](http://knockoutjs.com/index.html "KO")-Plugin zu registrieren.

		bundles.Add(new ScriptBundle("~/bundles/knockout").Include(
		            "~/Scripts/knockout-{version}.js"));
	In diesem Beispiel wird "Knockout" verwendet, um den dynamischen JavaScript-Code zu vereinfachen, der für Bildschirmvorlagen verwendet wird.

8. Bearbeiten Sie den Eintrag "contents/css", um das Stylesheet *contacts.css* zu registrieren. Ändern Sie die folgende Zeile:

                 bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/site.css"));
in:

        bundles.Add(new StyleBundle("~/Content/css").Include(
                   "~/Content/bootstrap.css",
                   "~/Content/contacts.css",
                   "~/Content/site.css"));

1. Führen Sie in der Paket-Managerkonsole den folgenden Befehl aus, um "Knockout" zu installieren.

	Installationspaket Knockoutjs
## Hinzufügen eines Controllers für die Restful-Web-API-Oberfläche

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner **Controllers**, und klicken Sie dann auf **Hinzufügen** und Controller.

1. Wählen Sie im Dialogfeld **Add Scaffold** die Option **Web API 2 Controller with actions, using Entity Framework** aus, und klicken Sie dann auf **Hinzufügen**.

	![API-Kontroller hinzufügen](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt1.PNG)

4. Geben Sie im Dialogfeld **Controller hinzufügen** die Zeichenfolge "ContactsController" als Controllernamen ein. Wählen Sie "Contact (ContactManager.Models)" als **Modellklasse** aus. Behalten Sie für die **Datenkontextklasse** die Standardeinstellung bei.

6. Klicken Sie auf **Hinzufügen**.

### Lokales Ausführen der Anwendung

1. Drücken Sie STRG+F5, um die Anwendung auszuführen.

	![Indexseite][intro001]

2. Geben Sie einen Kontakt ein, und klicken Sie auf **Hinzufügen**. Die App kehrt zur Homepage zurück und zeigt den eingegebenen Kontakt an.

	![Indexseite mit To-Do-List-Elementen][addwebapi004]

3. Hängen Sie im Browser **/api/contacts** an die URL an.

	Die resultierende URL sieht ähnlich aus wie http://localhost:1234/api/contacts. Die RESTful-Web-API, die Sie hinzugefügt haben, gibt die gespeicherten Kontakte zurück. Firefox und Chrome zeigen die Daten im XML-Format an.

	![Indexseite mit To-Do-List-Elementen][rxFFchrome]
	

	Im Internet Explorer werden Sie aufgefordert, die Kontakte zu öffnen oder zu speichern.

	![Web-API-Speicherdialog][addwebapi006]
	
	
	Sie können die ausgegebenen Kontakte in Notepad oder einem Browser öffnen.
	
	Das Ergebnis kann von anderen Anwendungen wie einer mobilen Website oder einer mobilen Anwendung verwendet werden.

	![Web-API-Speicherdialog][addwebapi007]

	**Sicherheitswarnung**: Ihre Anwendung ist unsicher und anfällig für CSRF-Angriffe. Im Verlauf des Tutorials korrigieren wir diese Schwachstelle. Weitere Informationen erhalten Sie unter [Verhindern von Cross-Site Request Forgery (CSRF)-Angriffen][prevent-csrf-attacks].
## Hinzufügen von XSRF-Schutz

Bei der websiteübergreifenden Anforderungsfälschung (auch bekannt als XSRF oder CSRF) handelt es sich um einen Angriff auf eine im Web gehostete Anwendung, bei der eine schädliche Website die Interaktion zwischen einem Clientbrowser und einer Website, der der Browser vertraut, beeinflussen kann. Diese Angriffe sind möglich, da Webbrowser bei jeder Anforderung an eine Website automatisch Authentifizierungstoken senden. Ein typisches Beispiel ist ein Authentifizierungscookie, wie das Authentifizierungsticket von ASP.NET. Websites, die einen persistenten Authentifizierungsmechanismus verwenden (wie Windows-Authentifizierung, HTTP-Basic usw.), sind für derartige Angriffe anfällig.

Ein XSRF-Angriff unterscheidet sich von einem Phishing-Angriff. Bei Phishing-Angriffen ist die Beihilfe des Opfers erforderlich. Bei Phishing-Angriffen wird die Zielwebsite durch eine schädliche Website imitiert und das Opfer dazu verleitet, geheime Daten an den Angreifer zu übermitteln. Bei einem XSRF-Angriff ist häufig keinerlei Aktion des Opfers erforderlich. Der Angreifer geht davon aus, dass der Browser automatisch alle relevanten Cookies an die Zielwebsite sendet.

Weitere Informationen finden Sie unter [Open Web Application Security Project](https://www.owasp.org/index.php/Main_Page) (OWASP) XSRF(https://www.owasp.org/index.php/Cross-Site_Request_Forgery_(CSRF)).

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **ContactManager**, und klicken Sie dann auf **Hinzufügen** > **Klasse**.

2. Geben Sie der Datei den Namen *ValidateHttpAntiForgeryTokenAttribute.cs*, und fügen Sie den folgenden Code hinzu:

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Net;
        using System.Net.Http;
        using System.Web.Helpers;
        using System.Web.Http.Controllers;
        using System.Web.Http.Filters;
        using System.Web.Mvc;
        namespace ContactManager.Filters
        {
            public class ValidateHttpAntiForgeryTokenAttribute : AuthorizationFilterAttribute
            {
                public override void OnAuthorization(HttpActionContext actionContext)
                {
                    HttpRequestMessage request = actionContext.ControllerContext.Request;
                    try
                    {
                        if (IsAjaxRequest(request))
                        {
                            ValidateRequestHeader(request);
                        }
                        else
                        {
                            AntiForgery.Validate();
                        }
                    }
                    catch (HttpAntiForgeryException e)
                    {
                        actionContext.Response = request.CreateErrorResponse(HttpStatusCode.Forbidden, e);
                    }
                }
                private bool IsAjaxRequest(HttpRequestMessage request)
                {
                    IEnumerable<string> xRequestedWithHeaders;
                    if (request.Headers.TryGetValues("X-Requested-With", out xRequestedWithHeaders))
                    {
                        string headerValue = xRequestedWithHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(headerValue))
                        {
                            return String.Equals(headerValue, "XMLHttpRequest", StringComparison.OrdinalIgnoreCase);
                        }
                    }
                    return false;
                }
                private void ValidateRequestHeader(HttpRequestMessage request)
                {
                    string cookieToken = String.Empty;
                    string formToken = String.Empty;
					IEnumerable<string> tokenHeaders;
                    if (request.Headers.TryGetValues("RequestVerificationToken", out tokenHeaders))
                    {
                        string tokenValue = tokenHeaders.FirstOrDefault();
                        if (!String.IsNullOrEmpty(tokenValue))
                        {
                            string[] tokens = tokenValue.Split(':');
                            if (tokens.Length == 2)
                            {
                                cookieToken = tokens[0].Trim();
                                formToken = tokens[1].Trim();
                            }
                        }
                    }
                    AntiForgery.Validate(cookieToken, formToken);
                }
            }
        }

1. Fügen Sie den folgenden *Benutzervermerk* zum "Contracts Controller" hinzu, damit Sie Zugriff auf **[ValidateHttpAntiForgeryToken]** haben.

	using ContactManager.Filters;

1. Fügen Sie das Attribut **[ValidateHttpAntiForgeryToken]** zu den Post-Methoden des **ContactsController** hinzu, um ihn vor XSRF-Angriffen zu schützen. Fügen Sie ihn zu den Methoden "PutContact", "PostContact" und **DeleteContact** hinzu.

	[ValidateHttpAntiForgeryToken] public IHttpActionResult PutContact(int id, Contact contact) {

1. Aktualisieren Sie den Abschnitt *Skripts* der Datei *Views\\Home\\Index.cshtml*, um Code für die XSRF-Tokens zu erhalten.

         @section Scripts {
            @Scripts.Render("~/bundles/knockout")
            <script type="text/javascript">
                @functions{
                   public string TokenHeaderValue()
                   {
                      string cookieToken, formToken;
                      AntiForgery.GetTokens(null, out cookieToken, out formToken);
                      return cookieToken + ":" + formToken;                
                   }
                }

               function ContactsViewModel() {
                  var self = this;
                  self.contacts = ko.observableArray([]);
                  self.addContact = function () {

                     $.ajax({
                        type: "post",
                        url: "api/contacts",
                        data: $("#addContact").serialize(),
                        dataType: "json",
                        success: function (value) {
                           self.contacts.push(value);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }
                     });

                  }
                  self.removeContact = function (contact) {
                     $.ajax({
                        type: "DELETE",
                        url: contact.Self,
                        success: function () {
                           self.contacts.remove(contact);
                        },
                        headers: {
                           'RequestVerificationToken': '@TokenHeaderValue()'
                        }

                     });
                  }

                  $.getJSON("api/contacts", function (data) {
                     self.contacts(data);
                  });
               }
               ko.applyBindings(new ContactsViewModel());
            </script>


## Veröffentlichen von Anwendungsaktualisierungen von Azure und SQL-Datenbank

Wiederholen Sie den Prozess, den Sie bereits zuvor durchgeführt haben, um die Anwendung zu veröffentlichen.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt, und wählen Sie **Veröffentlichen** aus.

	![Veröffentlichen][rxP]

5. Klicken Sie auf die Registerkarte **Einstellungen**.
	

1. Klicken Sie unter **ContactsManagerContext(ContactsManagerContext)** auf das Symbol **v**, um die *Remoteverbindungszeichenfolge* in die Verbindungszeichenfolge für die Kontaktdatenbank zu ändern. Klicken Sie auf **ContactDB**.

	![Einstellungen](./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rt5.png)

7. Markieren Sie das Kästchen **Execute Code First Migrations (wird beim Öffnen der Anwendung ausgeführt)**.

1. Klicken Sie auf **Weiter** und anschließend auf **Vorschau**. Visual Studio zeigt eine Liste der Dateien an, die hinzugefügt oder aktualisiert werden.

8. Klicken Sie auf **Veröffentlichen**. Nach Abschluss der Bereitstellung öffnet der Browser die Homepage der Anwendung.

	![Indexseite ohne Kontakte][intro001]

	Der Veröffentlichungsprozess von Visual Studio konfiguriert den Verbindungsstring automatisch in der bereitgestellten Datei *Web.config*, um zur SQL-Datenbank zu verweisen. Darüber hinaus wurde "Code First Migrations" so konfiguriert, dass es automatisch die Datenbank auf die neueste Version aktualisiert, sobald die Anwendung nach der Bereitstellung zum ersten Mal auf die Datenbank zugreift.

	Im Zuge dieser Konfiguration erstellte Code First die Datenbank, indem es den Code in der Klasse **Initial** ausführte, die Sie zuvor erstellt haben. Die Erstellung erfolgte, als die Anwendung zum ersten Mal nach der Bereitstellung versuchte, auf die Datenbank zuzugreifen.

9. Geben Sie einen Kontakt ein und befolgen Sie dabei die Vorgehensweise, die Sie bei der lokalen Ausführung der App verwendet haben, um zu prüfen, ob die Bereitstellung der Datenbank erfolgreich war.

Wenn der Eintrag auf der Contact Manager Seite gespeichert und angezeigt wird, wissen Sie, dass er in der Datenbank gespeichert wurde.

![Indexseite mit Kontakten][addwebapi004]

Die Anwendung wird nun in der Cloud ausgeführt und verwendet die SQL-Datenbank zum Speichern von Daten. Löschen Sie die Anwendung, sobald Sie die Tests in Azure abgeschlossen haben. Die Anwendung ist öffentlich und der Zugriff auf sie kann nicht beschränkt werden.

>[AZURE.NOTE]Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.

## Nächste Schritte

Eine echte Anwendung benötigt eine Authentifizierung und Autorisierung, und Sie würden für diesen Zweck die Mitgliedsdatenbank verwenden. Das Lernprogramm [Bereitstellen einer sicheren ASP.NET MVC 5-Anwendung mit Mitgliedschaft, OAuth und einer SQL-Datenbank ](web-sites-dotnet-deploy-aspnet-mvc-app-membership-oauth-sql-database.md) basiert auf diesem Lernprogramm und zeigt, wie Sie eine Web-Anwendung mithilfe der Mitgliedsdatenbank bereitstellen.

Ferner können Daten in einer Azure-Anwendung mithilfe von Azure Storage gespeichert werden, das nichtrelationale Datenspeicherung in Form von Blobs und Tabellen bietet. Die folgenden Links bieten weitere Informationen zu WEB API, ASP.NET MVC und Microsoft Azure.
 

* [Erste Schritte in Entity Framework mit MVC][EFCodeFirstMVCTutorial]
* [Einführung in ASP.NET MVC 5](http://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [Ihre erste ASP.NET WEB-API](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api)
* [Debugging von WAWS](web-sites-dotnet-troubleshoot-visual-studio.md)

Dieses Lernprogramm und die Beispielanwendung wurden von [Rick Anderson](http://blogs.msdn.com/b/rickandy/) (Twitter [@RickAndMSFT](https://twitter.com/RickAndMSFT)) sowie Tom Dykstra und Barry Dorrans (Twitter [@blowdart](https://twitter.com/blowdart)) geschrieben.

Bitte teilen Sie uns mit, was Ihrer Meinung nach beim Tutorial wie auch den darin vorgestellten Produkten gelungen ist und was vielleicht verbessert werden kann. Ihr Feedback unterstützt uns dabei, eine entsprechende Priorität für Verbesserungen festzulegen. Wir interessieren uns vor allem dafür, wie groß das Interesse an weiteren Automatisierungen in Bezug auf die Konfiguration und Bereitstellung der Mitgliedsdatenbank ist.

## Änderungen
* Hinweise zu den Veränderungen von Websites zum App Service finden Sie unter: [Azure App Service und vorhandene Azure-Dienste](http://go.microsoft.com/fwlink/?LinkId=529714).
* Hinweise zu den Veränderungen des neuen Portals gegenüber dem alten finden Sie unter [Referenz zur Navigation im Azure-Portal](http://go.microsoft.com/fwlink/?LinkId=529715)

<!-- bookmarks -->
[Add an OAuth Provider]: #addOauth
[Add Roles to the Membership Database]: #mbrDB
[Create a Data Deployment Script]: #ppd
[Update the Membership Database]: #ppd2
[setupdbenv]: #bkmk_setupdevenv
[setupwindowsazureenv]: #bkmk_setupwindowsazure
[createapplication]: #bkmk_createmvc4app
[deployapp1]: #bkmk_deploytowindowsazure1
[adddb]: #bkmk_addadatabase
[addcontroller]: #bkmk_addcontroller
[addwebapi]: #bkmk_addwebapi
[deploy2]: #bkmk_deploydatabaseupdate

<!-- links -->
[EFCodeFirstMVCTutorial]: http://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/creating-an-entity-framework-data-model-for-an-asp-net-mvc-application
[dbcontext-link]: http://msdn.microsoft.com/library/system.data.entity.dbcontext(v=VS.103).aspx


<!-- images-->
[rxE]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxE.png
[rxP]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxP.png
[rx22]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/
[rxb2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxb2.png
[rxz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz.png
[rxzz]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxzz.png
[rxz2]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz2.png
[rxz3]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz3.png
[rxStyle]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxStyle.png
[rxz4]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz4.png
[rxz44]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxz44.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxPrevDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPrevDB.png
[rxOverwrite]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxOverwrite.png
[rxPWS]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxPWS.png
[rxNewCtx]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxNewCtx.png
[rxAddApiController]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxAddApiController.png
[rxFFchrome]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxFFchrome.png
[intro001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobil-intro-finished-web-app.png
[rxCreateWSwithDB]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/rxCreateWSwithDB.png
[setup007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-setup-azure-site-004.png
[setup009]: ../Media/dntutmobile-setup-azure-site-006.png
[newapp002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-002.png
[newapp004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-createapp-004.png
[firsdeploy007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-005.png
[firsdeploy009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-deploy1-publish-007.png
[adddb001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-001.png
[adddb002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-adddatabase-002.png
[addcode001]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-context-menu.png
[addcode002]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-controller-dialog.png
[addcode004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-index-context.png
[addcode005]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-add-contents-context-menu.png
[addcode007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-controller-modify-bundleconfig-context.png
[addcode008]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-menu.png
[addcode009]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-migrations-package-manager-console.png
[addwebapi004]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-added-contact.png
[addwebapi006]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-save-returned-contacts.png
[addwebapi007]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/dntutmobile-webapi-contacts-in-notepad.png
[Add XSRF Protection]: #xsrf
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[Add XSRF Protection]: #xsrf
[ImportPublishSettings]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishSettings.png
[ImportPublishProfile]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ImportPublishProfile.png
[PublishVSSolution]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/PublishVSSolution.png
[ValidateConnection]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/ValidateConnection.png
[WebPIAzureSdk20NetVS12]: ./media/web-sites-dotnet-rest-service-aspnet-api-sql-database/WebPIAzureSdk20NetVS12.png
[prevent-csrf-attacks]: http://www.asp.net/web-api/overview/security/preventing-cross-site-request-forgery-(csrf)-attacks
 

<!---HONumber=Oct15_HO3-->