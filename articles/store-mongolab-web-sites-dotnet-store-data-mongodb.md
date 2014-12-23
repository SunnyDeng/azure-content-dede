<properties urlDisplayName="Website with MongoDB on MongoLab" pageTitle="Erstellen einer Website, die MongoDB in MongoLab (.NET) verwendet" metaKeywords="" description="Learn how to create an Azure website that stores data in MongoDB hosted by MongoLab." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create a C# ASP.NET Application on Azure with MongoDB using the MongoLab Add-On" authors="eric@mongolab.com" solutions="" manager="mongolab" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="eric@mongolab.com" />



# Erstellen einer Node.js-Anwendung auf Azure mit MongoDB mithilfe des MongoLab-Add-On

<p><em>Von Eric Sedor, MongoLab</em></p>

Seid gegrüßt, Abenteurer! Willkommen bei MongoDB-as-a-Service. In diesem Lernprogramm führen Sie folgende Schritte aus:

1. [Bereitstellen der Datenbank][provision]: Das Azure Store-Add-On [MongoLab](http://mongolab.com) stellt eine MongoDB-Datenbank bereit, die in der Azure-Cloud gehostet und von der Clouddatenbankplattform von MongoLab verwaltet wird.
1. [Erstellen der App][create]: Es handelt sich um eine einfache C# ASP.NET MVC-App zum Erstellen von Notizen.
1. [Bereitstellen der App][deploy]: Durch das Verknüpfen einiger Konfigurationsdaten lässt sich unser Codes sehr einfach bereitstellen.
1. [Verwalten der Datenbank][manage]: Schließlich zeigen wir Ihnen das webbasierte Datenbankverwaltungsportal von MongoLab, in dem Sie Daten problemlos suchen, visualisieren und ändern können.

Sie können in diesem Lernprogramm jederzeit eine E-Mail an [support@mongolab.com](mailto:support@mongolab.com) senden, falls Sie Fragen haben.

## Schnellstart
Wenn Sie bereits eine Azure-Anwendung und eine Website besitzen, mit denen Sie arbeiten möchten, oder wenn Sie bereits mit dem Azure Store vertraut sind, ermöglicht Ihnen dieser Abschnitt einen schnellen Einstieg. Andernfalls fahren Sie unten mit dem [Bereitstellen der Datenbank][provision] fort.
 
1. Öffnen Sie den Azure Store.  
![Store][button-store]
1. Kaufen Sie das MongoLab Add-On.  
![MongoLab][entry-mongolab]
1. Klicken Sie in der Liste der Add-Ons auf das MongoLab-Add-On und dann auf **Verbindungsinformationen**.  
![ConnectionInfoButton][button-connectioninfo]  
1. Kopieren Sie den MONGOLAB_URI in die Zwischenablage.  
![ConnectionInfoScreen][screen-connectioninfo]  
**Dieser URI enthält Ihren Benutzernamen und Ihr Kennwort für die Datenbank.  Behandeln Sie diese Angaben als kritische Informationen, und teilen Sie sie niemandem mit.**
1. Fügen Sie den Wert der Liste mit den Verbindungszeichenfolgen im Konfigurationsmenü der Azure-Webanwendung hinzu:  
![WebSiteConnectionStrings][focus-website-connectinfo]
1. Geben Sie im Feld **Name**die Zeichenfolge MONGOLAB\_URI ein.
1. Fügen Sie unter **Wert** die Verbindungszeichenfolge ein, die wir im vorhergehenden Abschnitt ermittelt haben.
1. Wählen Sie im Dropdownfeld für den Typ die Option **Benutzerdefiniert** aus (statt des Standardwerts **SQLAzure**).
1. Installieren Sie den Mongo C#-Treiber in Visual Studio, indem Sie **Extras > Bibliothekspaket-Manager > Paket-Manager-Konsole** auswählen. Geben Sie in der Paket-Manager-Konsole **Install-Package mongocsharpdriver** ein, und drücken Sie die **Eingabetaste**.
1. Richten Sie im Code einen Hook ein, um die MongoLab-Verbindungs-URI aus der Umgebungsvariablen abzurufen:

        using MongoDB.Driver;  
        ...
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        ...
        MongoUrl url = new MongoUrl(connectionString);
        MongoClient client = new MongoClient(url);
Hinweis: Azure fügt das Präfix **CUSTOMCONNSTR\_** zur ursprünglich deklarierten Verbindungszeichenfolge hinzu. Darum wird im Code auf **CUSTOMCONNSTR\_MONGOLAB\_URI.** und nicht auf **MONGOLAB\_URI** verwiesen.

Nun folgt das vollständige Lernprogramm ...

<h2><a name="provision"></a>Bereitstellen der Datenbank</h2>

[WACOM.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

<h2><a name="create"></a>Erstellen der App</h2>

In diesem Abschnitt wird die Erstellung eines C# ASP.NET Visual Studio-Projekts beschrieben und schrittweise erläutert, wie mit dem C# MongoDB-Treiber eine einfache Notizanwendung erstellt wird. Sie möchten Ihre Website besuchen, eine Notiz verfassen und alle Notizen anzeigen können, die Sie dort hinterlassen haben.

Sie verwenden für diese Entwicklung Visual Studio Express 2012 für das Web.

### Erstellen des Projekts
Für die Beispiel-App wird der Einfachheit halber eine Visual Studio-Vorlage verwendet. Verwenden Sie unbedingt .NET Framework 4.0.

1. Wählen Sie **Datei > Neues Projekt** aus. Das Dialogfeld "Neues Projekt" wird angezeigt:    
![NewProject][dialog-mongolab-csharp-newproject]
1. Wählen Sie **Installiert > Vorlagen > Visual C# > Web** aus.
1. Wählen Sie im Dropdownmenü für die .NET-Version den Eintrag **.NET Framework 4** aus (*Hinweis: Framework 4.5 funktioniert derzeit nicht*).

    ![ProjectFramework][dotNet-framework-four]
1. Wählen Sie **ASP.NET MVC 4-Webanwendung**.  
1. Geben Sie _mongoNotes_ in **Projektname** ein. Wenn Sie einen anderen Namen wählen, müssen Sie den in diesem Lernprogramm bereitgestellten Code ändern.
1. Klicken Sie auf **OK**. Das Dialogfeld "Projektvorlage" wird geöffnet:  
![ProjectTemplate][dialog-mongolab-csharp-projecttemplate]
1. Wählen Sie **Internetanwendung** aus, und klicken Sie auf **OK**. Das Projekt wird erstellt.
1. Wählen Sie **Extras > Bibliothekspaket-Manager > Paket-Manager-Konsole** aus. Geben Sie in der Paket-Manager-Konsole **Install-Package mongocsharpdriver** ein, und drücken Sie die **Eingabetaste**.  
![PMConsole][focus-mongolab-csharp-pmconsole] 
Der MongoDB C#-Treiber wird in das Projekt integriert, und die folgende Zeile wird automatisch der Datei _packages.config_ hinzugefügt:

        < package id="mongocsharpdriver" version="1.8" targetFramework="net40" / >

### Hinzufügen eines Notizenmodells
Zuerst richten Sie ein Modell für Notizen ein, das nur ein Datum und Textinhalt enthält.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Modelle**, und wählen Sie **Hinzufügen > Klasse** aus. Geben Sie dieser neuen Klasse den Namen *Note.cs*.
1. Ersetzen Sie den automatisch für diese Klasse generierten Code durch folgenden Code:  

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using MongoDB.Bson.Serialization.Attributes;
        using MongoDB.Bson.Serialization.IdGenerators;
        using MongoDB.Bson;
                
        namespace mongoNotes.Models
        {
            public class Note
            {
                public Note()
                {
                    Date = DateTime.UtcNow;
                }
                
                private DateTime date;
        
                [BsonId(IdGenerator = typeof(CombGuidGenerator))]
                public Guid Id { get; set; }
        
                [BsonElement("Note")]
                public string Text { get; set; }
        
                [BsonElement("Date")]
                public DateTime Date {
                    get { return date.ToLocalTime(); }
                    set { date = value;}
                }
            }
        }

### Hinzufügen einer Datenzugriffsschicht
Es ist wichtig, dass Sie eine Möglichkeit zum Zugriff auf MongoDB einrichten, um die Notizen abrufen und speichern zu können. Die Datenzugriffsschicht nutzt das Modell "Note" und wird später in "HomeController" eingebunden.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **mongoNotes**, und wählen Sie **Hinzufügen > Neuer Ordner** aus. Geben Sie dem Ordner den Namen **DAL**.
1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **DAL**, und wählen Sie **Hinzufügen > Neue Klasse** aus. Geben Sie dieser neuen Klasse den Namen *Dal.cs*.
1. Ersetzen Sie den automatisch für diese Klasse generierten Code durch folgenden Code:  

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using mongoNotes.Models;
        using MongoDB.Driver;
        using System.Configuration;

        namespace mongoNotes
        {
            public class Dal : IDisposable
            {
                private MongoServer mongoServer = null;
                private bool disposed = false;
        
                private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
                MongoUrl url = new MongoUrl(connectionString);
        
                private string dbName = "myMongoApp";
                private string collectionName = "Notes";
        
                // Default constructor.        
                public Dal()
                {
                }
           
                public List<Note> GetAllNotes()
                {
                    try
                    {
                        MongoCollection<Note> collection = GetNotesCollection();
                        return collection.FindAll().ToList<Note>();
                    }
                    catch (MongoConnectionException)
                    {
                        return new List<Note>();
                    }
                }
        
                // Creates a Note and inserts it into the collection in MongoDB.
                public void CreateNote(Note note)
                {
                    MongoCollection<Note> collection = getNotesCollectionForEdit();
                    try
                    {
                        collection.Insert(note);
                    }
                    catch (MongoCommandException ex)
                    {
                        string msg = ex.Message;
                    }
                }
        
                private MongoCollection<Note> GetNotesCollection()
                {
                    MongoClient client = new MongoClient(url);
                    mongoServer = client.GetServer();
                    MongoDatabase database = mongoServer.GetDatabase(dbName);
                    MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
                    return noteCollection;
                }
        
                private MongoCollection<Note> getNotesCollectionForEdit()
                {
                    MongoClient client = new MongoClient(url);
                    mongoServer = client.GetServer();
                    MongoDatabase database = mongoServer.GetDatabase(dbName);
                    MongoCollection<Note> notesCollection = database.GetCollection<Note>(collectionName);
                    return notesCollection;
                }
        
                # region IDisposable
        
                public void Dispose()
                {
                    this.Dispose(true);
                    GC.SuppressFinalize(this);
                }
        
                protected virtual void Dispose(bool disposing)
                {
                    if (!this.disposed)
                    {
                        if (disposing)
                        {
                            if (mongoServer != null)
                            {
                                this.mongoServer.Disconnect();
                            }
                        }
                    }
        
                    this.disposed = true;
                }
        
                # endregion
            }
        }
1. Beachten Sie den folgenden Code oben:  
            
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        private string dbName = "myMongoApp";  
Hier greifen Sie auf eine Umgebungsvariable zu, die Sie später konfigurieren werden. Falls Sie eine lokale Mongo-Instanz zu Entwicklungszwecken haben, können Sie diesen Wert temporär auf "localhost" festlegen.  
  
  Legen Sie auch den Datenbanknamen fest. Genauer gesagt, legen Sie den **dbName**-Wert auf den Namen fest, den Sie bei der Bereitstellung des MongoLab Add-Ons eingegeben haben.
1. Untersuchen Sie schließlich den Code in **GetNotesCollection()**:  

        MongoClient client = new MongoClient(url);
        mongoServer = client.GetServer();
        MongoDatabase database = mongoServer.GetDatabase(dbName);
        MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
  There's nothing to change here; Just be aware that this is how you get a MongoCollection object for performing inserts, updates, and queries, such as the following in **GetAllNotes()**:  

        collection.FindAll().ToList<Note>();

Weitere Informationen zur Verwendung des C# MongoDB-Treibers finden Sie unter [CSharp Driver QuickStart](http://www.mongodb.org/display/DOCS/CSharp+Driver+Quickstart "CSharp Driver Quickstart") auf mongodb.org.

### Hinzufügen der Ansicht "Create"
Nun fügen Sie eine Ansicht zum Erstellen einer neuen Notiz hinzu.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Ansichten > Home**, und wählen Sie **Hinzufügen > Ansicht** aus. Geben Sie dieser neuen Ansicht den Namen **Create**, und klicken Sie auf **Hinzufügen**.
1. 	Ersetzten Sie den automatisch für diese Ansicht generierten Code (**Create.cshtml**) durch den folgenden Code:  

        @model mongoNotes.Models.Note
        
        <script src="@Url.Content("~/Scripts/jquery-1.5.1.min.js")" type="text/javascript"></script>
        <script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
        <script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
        
        @using (Html.BeginForm("Create", "Home")) {
            @Html.ValidationSummary(true)
            <fieldset>
                <legend>New Note</legend>
                <h3>New Note</h3>       
                <div class="editor-label">
                    @Html.LabelFor(model => model.Text)
                </div>
                <div class="editor-field">
                    @Html.EditorFor(model => model.Text)
                </div>
               <p>
                    <input type="submit" value="Create" />
               </p>
           </fieldset>
        }

### Ändern der Datei "index.cshtml"
Als Nächstes fügen Sie ein einfaches Layout zum Anzeigen und Erstellen von Notizen auf Ihrer Website hinzu.

1. Öffnen Sie die Datei **Index.cshtml** unter **Ansichten > Home**, und ersetzen Sie ihren Inhalt durch folgenden Code:  

        @model IEnumerable<mongoNotes.Models.Note>
        
        @{
            ViewBag.Title = "Notes";
        }
        
        <h2>My Notes</h2>

        <table border="1">
            <tr>
                <th>Date</th>
                <th>Note Text</th>      
            </tr>
        
        @foreach (var item in Model) {
            <tr>
                <td>
                    @Html.DisplayFor(modelItem => item.Date)
                </td>
                <td>
                    @Html.DisplayFor(modelItem => item.Text)
                </td>       
            </tr>
        }
        
        </table>
        <div>  @Html.Partial("Create", new mongoNotes.Models.Note())</div>

### Ändern der Datei "HomeController.cs"
Schließlich muss Ihr HomeController die Datenzugriffsschicht instanziieren und auf die Ansichten anwenden.

1. Öffnen Sie im Projektmappen-Explorer unter **Controller** die Datei **HomeController.cs**, und ersetzen Sie ihren Inhalt durch folgenden Code:  

        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Web;
        using System.Web.Mvc;
        using mongoNotes.Models;
        using System.Configuration;
        
        namespace mongoNotes.Controllers
        {
            public class HomeController : Controller, IDisposable
            {
                private Dal dal = new Dal();
                private bool disposed = false;
                //
                // GET: /Task/
        
                public ActionResult Index()
                {
                    return View(dal.GetAllNotes());
                }
        
                //
                // GET: /Task/Create
        
                public ActionResult Create()
                {
                    return View();
                }
        
                //
                // POST: /Task/Create
        
                [HttpPost]
                public ActionResult Create(Note note)
                {
                    try
                    {
                        dal.CreateNote(note);
                        return RedirectToAction("Index");
                    }
                    catch
                    {
                        return View();
                    }
                }
        
                public ActionResult About()
                {
                    return View();
                }
        
                # region IDisposable
        
                new protected void Dispose()
                {
                    this.Dispose(true);
                    GC.SuppressFinalize(this);
                }
        
                new protected virtual void Dispose(bool disposing)
                {
                    if (!this.disposed)
                    {
                        if (disposing)
                        {
                            this.dal.Dispose();
                        }
                    }
        
                    this.disposed = true;
                }
        
                # endregion
        
            }
        }
    
    
<h2><a name="deploy"></a>Bereitstellen der App</h2>

Nachdem die Anwendung jetzt entwickelt worden ist, müssen Sie eine Azure-Website zum Hosten der Anwendung erstellen und konfigurieren und den Code bereitstellen. In diesem Abschnitt ist die Verwendung der MongoDB-Verbindungszeichenfolge (URI) ein zentraler Aspekt. Sie konfigurieren auf Ihrer Website eine Umgebungsvariable mit diesem URI, um den URI vom Code zu trennen.  Sie sollten den URI als vertrauliche Information behandeln, da er Anmeldeinformationen für die Verbindung mit der Datenbank enthält.

### Erstellen einer neuen Website und Abrufen der Datei mit den Veröffentlichungseinstellungen
Das Erstellen einer Website in Azure ist sehr einfach, insbesondere weil Azure automatisch ein Veröffentlichungsprofil für Visual Studio generiert.

1. Klicken Sie im Azure-Portal auf **Neu**.  
![New][button-new]
1. Wählen Sie **Berechnen > Website > Schnellerfassung** aus.  
![CreateSite][screen-mongolab-newwebsite]
1. Geben Sie ein URL-Präfix ein. Wählen Sie einen Namen Ihrer Wahl, aber denken Sie daran, dass er eindeutig sein muss ("mongoNotes" ist wahrscheinlich nicht verfügbar).
1. Klicken Sie auf **Website erstellen**.
1. Klicken Sie nach Abschluss der Websiteerstellung in der Websiteliste auf den Namen der Website. Das Website-Dashboard wird angezeigt.  
![WebSiteDashboard][screen-mongolab-websitedashboard]
1. Klicken Sie unter **Auf einen Blick** auf **Veröffentlichungsprofil herunterladen**, und speichern Sie die .PublishSettings-Datei in einem Verzeichnis Ihrer Wahl.  
![DownloadPublishProfile][button-website-downloadpublishprofile]

### Abrufen der MongoLab-Verbindungszeichenfolge

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

### Hinzufügen der Verbindungszeichenfolge zu den Umgebungsvariablen der Website

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

### Veröffentlichen der Website
1. Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **mongoNotes**, und wählen Sie **Veröffentlichen** aus. Das Dialogfeld "Veröffentlichen" wird angezeigt:  
![Publish][dialog-mongolab-vspublish]
1. Klicken Sie auf **Importieren**, und wählen Sie die .PublishSettings-Datei im gewählten Download-Verzeichnis aus. Diese Datei trägt automatisch Werte in das Dialogfeld "Veröffentlichen" ein.
1. Klicken Sie auf **Verbindung prüfen**, um die Datei zu testen.
1. Wenn die Überprüfung erfolgreich war, klicken Sie auf **Veröffentlichen**. Sobald die Veröffentlichung abgeschlossen ist, wird eine neue Browserregisterkarte geöffnet und die Website angezeigt.
1. Geben Sie etwas Text als Notiz ein, klicken Sie auf **Create**, und betrachten Sie das Ergebnis!  
![HelloMongoAzure][screen-mongolab-sampleapp]

<h2><a name="manage"></a>Verwalten der Datenbank</h2>

[WACOM.INCLUDE [howto-access-mongolab-ui](../includes/howto-access-mongolab-ui.md)]

Glückwunsch! Sie haben gerade eine C# ASP.NET-Anwendung mit einer von MongoLab gehosteten MongoDB-Datenbank gestartet! Nachdem Sie nun eine MongoLab-Datenbank haben,können Sie sich mit allen Fragen oder Bedenken bezüglich Ihrer Datenbank sowie mit Hilfeanfragen zu MongoDB oder dem C#-Treiber an [support@mongolab.com](mailto:support@mongolab.com) wenden. Viel Glück!

[screen-mongolab-sampleapp]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-sampleapp.png
[dialog-mongolab-vspublish]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-vspublish.png
[button-website-downloadpublishprofile]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-website-downloadpublishprofile.png
[screen-mongolab-websitedashboard]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-newwebsite.png
[button-new]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-new.png
[dialog-mongolab-csharp-newproject]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-csharp-newproject.png
[dialog-mongolab-csharp-projecttemplate]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-csharp-projecttemplate.png
[focus-mongolab-csharp-pmconsole]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-mongolab-csharp-pmconsole.png
[button-store]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-store.png
[entry-mongolab]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/entry-mongolab.png 
[button-connectioninfo]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-connectioninfo.png
[screen-connectioninfo]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab_connectioninfo.png
[dotNet-framework-four]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-dotNet-Framework4-mongolab.png
[focus-website-connectinfo]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage

