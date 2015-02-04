<properties urlDisplayName="Website with MongoDB on MongoLab" pageTitle="Erstellen einer Website, die MongoDB auf MongoLab (.NET) verwendet" metaKeywords="" description="Erfahren Sie mehr über die Erstellung einer Azure-Website, die Daten in einer von MongoLab gehosteten MongoDB speichert." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create a C# ASP.NET Application on Azure with MongoDB using the MongoLab Add-On" authors="chris@mongolab.com, eric@mongolab.com" solutions="" manager="mongolab" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="11/17/2014" ms.author="chris@mongolab.com" />



# Erstellen einer Node.js-Anwendung auf Azure mit MongoDB mithilfe des MongoLab-Add-On

<p><em>Von Eric Sedor, MongoLab</em></p>

Grüße, Abenteurer! Willkommen bei MongoDB-as-a-Service. In diesem Lernprogramm lernen Sie Folgendes:

1. [Bereitstellung der Datenbank][provision] - Das Azure Store-Add-On [MongoLab](http://mongolab.com) stellt eine MongoDB-Datenbank bereit, die in der Azure-Cloud gehostet und durch die Clouddatenbankplattform von MongoLab verwaltet wird.
1. [Erstellen der Anwendung][create]: Es handelt sich um eine einfache C# ASP.NET MVC-App zum Erstellen von Notizen.
1. [Bereitstellen der App][deploy]: Durch das Verknüpfen einiger Konfigurationshooks wird das Pushen unseres Codes sehr einfach.
1. [Verwalten der Datenbank][manage]: Schließlich zeigen wir Ihnen das webbasierte Datenbankverwaltungsportal von MongoLab, in dem Sie Daten problemlos suchen, visualisieren und ändern können.

Sie können in diesem Lernprogramm jederzeit eine E-Mail an [support@mongolab.com](mailto:support@mongolab.com) senden, falls Sie Fragen haben.

## Schnellstart
Wenn Sie bereits eine Azure-Anwendung und eine Website besitzen, mit denen Sie arbeiten möchten, oder wenn Sie den Azure Store bereits kennengelernt haben, ermöglicht Ihnen dieser Abschnitt einen schnellen Einstieg. Fahren Sie andernfalls mit [Bereitstellen der Datenbank][provision] weiter unten fort.
 
1. Öffnen Sie den Azure Store.  
![Store][button-store]
1. Kaufen Sie das MongoLab-Add-On.  
![MongoLab][entry-mongolab]
1. Klicken Sie in der Liste der Add-Ons auf das MongoLab-Add-On und dann auf **Verbindungsinformationen**.  
![ConnectionInfoButton][button-connectioninfo]  
1. Kopieren Sie die MONGOLAB_URI in die Zwischenablage.  
![ConnectionInfoScreen][screen-connectioninfo]  
**Dieser URI enthält Ihren Benutzernamen und Ihr Kennwort für die Datenbank.  Behandeln Sie diese Angaben als kritische Informationen, und teilen Sie sie niemandem mit.**
1. Fügen Sie den Wert der Liste mit den Verbindungszeichenfolgen im Konfigurationsmenü der Azure-Webanwendung hinzu:  
![WebSiteConnectionStrings][focus-website-connectinfo]
1. Geben Sie bei**Name** MONGOLAB\_URI ein.
1. Fügen Sie unter **Wert**, die Verbindungszeichenfolge ein, die wir im vorhergehenden Abschnitt erzeugt haben.
1. Wählen Sie im Dropdownfeld für den Typ die Option **Benutzerdefiniert** aus (anstelle des Standards **SQLAzure**).
1. Installieren Sie den Mongo C#-Treiber in Visual Studio, indem Sie **Extras > Library Package Manager > Paket-Manager-Konsole**. Geben Sie in der Paket-Manager-Konsole **Install-Package mongocsharpdriver** ein, und drücken Sie die **Eingabetaste**.
1. Richten Sie im Code einen Hook ein, um die MongoLab-Verbindungs-URI aus der Umgebungsvariablen abzurufen:

        using MongoDB.Driver;  
        ...
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        ...
        MongoUrl url = new MongoUrl(connectionString);
        MongoClient client = new MongoClient(url);
Hinweis: Azur fügt den Präfix **CUSTOMCONNSTR\_** der ursprünglich erklärten Verbindungszeichenfolge hinzu; deshalb referenziert der Code **CUSTOMCONNSTR\_MONGOLAB\_URI.** statt **MONGOLAB\_URI**.

Nun folgt das vollständige Lernprogramm ...

<h2><a name="provision"></a>Bereitstellen der Datenbank</h2>

[WACOM.INCLUDE [vorgehensweise-bereitstellung-mongolab](../includes/howto-provision-mongolab.md)]

<h2><a name="create"></a>Erstellen der App</h2>

In diesem Abschnitt wird die Erstellung eines C# ASP.NET Visual Studio-Projekts beschrieben und schrittweise erläutert, wie mit dem C# MongoDB-Treiber eine einfache Notizanwendung erstellt wird. Sie möchten Ihre Website besuchen, eine Notiz schreiben und alle Notizen anzeigen, die hinterlegt wurden.

Sie verwenden für diese Entwicklung Visual Studio Express 2013 für das Web.

### Erstellen des Projekts
Die Beispiel-App basiert der Einfachheit halber auf einer Visual Studio-Vorlage. Verwenden Sie unbedingt .NET Framework 4.5.

1. Wählen Sie **Datei > Neues Projekt** aus. Das Dialogfeld "Neues Projekt" wird angezeigt:    
![NewProject][dialog-mongolab-csharp-newproject]
1. Wählen Sie **Installiert > Vorlagen > Visual C# > Web** aus.
1. Wählen Sie im Dropdownmenü für die **.NET Framework 4.5** aus.
1. Wählen Sie die**MVC-Anwendung**.  
1. Geben Sie _mongoNotes_ als **Projektname** ein. Wenn Sie einen anderen Namen wählen, müssen Sie den bereitgestellten Code im gesamten Lernprogramm abändern.
1. Wählen Sie **Extras > Library Package Manager > Paket-Manager-Konsole** aus. Geben Sie in der Paket-Manager-Konsole **Install-Package mongocsharpdriver** ein, und drücken Sie die **Eingabetaste**.  
![PMConsole][focus-mongolab-csharp-pmconsole] 
Der MongoDB C#-Treiber wird in das Projekt integriert, und die folgende Zeile wird der Datei _packages.config_ automatisch hinzugefügt:

        < package id="mongocsharpdriver" version="1.9.2" targetFramework="net45" / >

### Hinzufügen eines Notizenmodells
Zuerst richten Sie ein Modell für Notizen ein, das nur ein Datum und Textinhalt enthält.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Modelle**, und wählen Sie **Hinzufügen > Klasse** aus. Geben Sie dieser neuen Klasse den Namen *Note.cs*.
1. Ersetzen Sie den automatisch generierten Code für diese Klasse durch folgenden Code:  

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

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **mongoNotes**, und wählen Sie **Hinzufügen > Neuer Ordner**. Geben Sie dem Ordner den Namen **DAL**.
1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **DAL**, und wählen Sie **Hinzufügen > Klasse** aus. Geben Sie dieser neuen Klasse den Namen *Dal.cs*.
1. Ersetzen Sie den automatisch generierten Code für diese Klasse durch folgenden Code:  

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
                MongoUrl url;
        
                private string dbName = "myMongoApp";
                private string collectionName = "Notes";
        
                // Default constructor.        
                public Dal()
                {
                    url = new MongoUrl(connectionString);
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
1. Beachten Sie den folgenden Code über:  
            
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        private string dbName = "myMongoApp";  
Hier greifen Sie auf eine Umgebungsvariable zu, die Sie später konfigurieren werden. Falls Sie eine lokale Mongo-Instanz zu Entwicklungszwecken haben, können Sie diesen Wert temporär auf "localhost" festlegen.  
  
  Legen Sie auch den Datenbanknamen fest. Genauer gesagt, legen Sie den **dbName**-Wert auf den Namen fest, den Sie bei der Bereitstellung des MongoLab Add-Ons eingegeben haben.
1. Untersuchen Sie schließlich den Code in **GetNotesCollection()**:  

        MongoClient client = new MongoClient(url);
        mongoServer = client.GetServer();
        MongoDatabase database = mongoServer.GetDatabase(dbName);
        MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);
  Hier muss nichts geändert werden. Sie sehen hier nur, wie Sie ein MongoCollection-Objekt zum Ausfühen von Einfügungen, Aktualisierungen und Abfragen erhalten, wie das folgende Objekt in **GetAllNotes()**:  

        collection.FindAll().ToList<Note>();

Weitere Informationen zur Verwendung des C# MongoDB-Treibers finden Sie im [CSharp Driver QuickStart](http://www.mongodb.org/display/DOCS/CSharp+Driver+Quickstart "CSharp Driver Quickstart") auf mongodb.org.

### Hinzufügen der Ansicht "Create"
Nun fügen Sie eine Ansicht zum Erstellen einer neuen Notiz hinzu.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Ansichten > Home**, und wählen Sie **Hinzufügen > View** aus. Geben Sie dieser Ansicht den Namen **Create**, und klicken Sie auf **Hinzufügen**.
1. Ersetzten Sie den automatisch für diese Ansicht generierten Code (**Create.cshtml**) durch den folgenden:  

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

1. Öffnen Sie **Index.cshtml** unter **Ansichten > Home**, und ersetzen Sie ihren Inhalt durch folgenden Code:  

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

1. Öffnen Sie im Projektmappen-Explorer unter **HomeController.cs** die Datei  **Controllers**, und ersetzen Sie ihren Inhalt durch folgenden Code:  

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

Da die Anwendung nun entwickelt wurde, sollten Sie eine Azure-Website zum Hosten der Anwendung erstellen und konfigurieren und den Code bereitstellen. In diesem Abschnitt ist die Verwendung der MongoDB-Verbindungszeichenfolge (URI) ein zentraler Aspekt. Sie konfigurieren auf Ihrer Website eine Umgebungsvariable mit diesem URI, um den URI vom Code zu trennen.  Sie sollten die URI als vertrauliche Information behandeln, da sie Anmeldeinformationen für die Verbindung zur Datenbank enthält.

### Erstellen einer neuen Website und Abrufen der Datei mit den Veröffentlichungseinstellungen
Das Erstellen einer Website in Azure ist sehr einfach, insbesondere weil Azure automatisch ein Veröffentlichungsprofil für Visual Studio generiert.

1. Klicken Sie im Azure-Portal auf **Neu**.  
![New][button-new]
1. Wählen Sie **Berechnen > Website > Schnellerfassung** aus.  
![CreateSite][screen-mongolab-newwebsite]
1. Geben Sie einen URL-Präfix ein. Wählen Sie einen Namen aus, aber denken Sie daran, dass er eindeutig sein muss ("mongoNotes" ist wahrscheinlich nicht verfügbar).
1. Klicken Sie auf **Website erstellen**.
1. Klicken Sie nach Abschluss der Websiteerstellung auf den Namen der Website in der Websiteliste. Das Website-Dashboard wird angezeigt.  
![WebSiteDashboard][screen-mongolab-websitedashboard]
1. Klicken Sie auf **Download publish profile** unter **Schnellübersicht**, und speichern Sie die .PublishSettings-Datei in einem Verzeichnis Ihrer Wahl.  
![DownloadPublishProfile][button-website-downloadpublishprofile]

Alternativ können Sie auch direkt in Visual Studio eine Website konfigurieren. Wenn Sie Ihr Azure-Konto mit Visual Studio verknüpfen, folgen Sie den Aufforderungen zum Konfigurieren einer Website von dort aus. Nachdem Sie fertig sind, können Sie einfach mit der rechten Maustaste auf den Projektnamen im Projektmappen-Explorer klicken, um in Azure bereitzustellen. Sie müssen dennoch die MongoLab-Verbindungszeichenfolge konfigurieren, was in den Schritten unten beschrieben wird.

### Abrufen der MongoLab-Verbindungszeichenfolge

[WACOM.INCLUDE [vorgehensweise-verbindungsinfo-mongolab-abrufen](../includes/howto-get-connectioninfo-mongolab.md)]

### Hinzufügen der Verbindungszeichenfolge zu den Umgebungsvariablen der Website

[WACOM.INCLUDE [vorgehensweise-verbindungsinfo-mongolab-speichern](../includes/howto-save-connectioninfo-mongolab.md)]

### Veröffentlichen der Website
1. Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **mongoNotes**, und wählen Sie **Veröffentlichen** aus. Das Dialogfeld "Veröffentlichen" wird angezeigt.  
![Publish][dialog-mongolab-vspublish]
1. Klicken Sie auf **Importieren**, und wählen Sie die .PublishSettings im gewählten Download-Verzeichnis aus. Diese Datei trägt automatisch Werte in das Dialogfeld "Veröffentlichen" ein.
1. Klicken Sie auf **Verbindung prüfen**, um die Datei zu testen.
1. Wenn die Überprüfung erfolgreich war, klicken Sie auf **Veröffentlichen**. Sobald die Veröffentlichung abgeschlossen ist, wird eine neue Browserregisterkarte geöffnet und die Website angezeigt.
1. Geben Sie einen Notiztext ein, klicken Sie auf **Erstellen**, und betrachten Sie das Ergebnis!  
![HelloMongoAzure][screen-mongolab-sampleapp]

<h2><a name="manage"></a>Verwalten der Datenbank</h2>

[WACOM.INCLUDE [vorgehensweise-zugang-zur-mongolab-benutzeroberflaeche](../includes/howto-access-mongolab-ui.md)]

Glückwunsch! Sie haben gerade eine C# ASP.NET-Anwendung mit einer von MongoLab gehosteten MongoDB-Datenbank gestartet! Nachdem Sie nun eine MongoLab-Datenbank haben,können Sie sich mit allen Fragen oder Bedenken bezüglich Ihrer Datenbank sowie mit Hilfeanfragen bezüglich MongoDB oder dem Node-Treiber selbst an [support@mongolab.com](mailto:support@mongolab.com) wenden. Viel Glück!

[screen-mongolab-sampleapp]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-sampleapp.png
[dialog-mongolab-vspublish]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-vspublish.png
[button-website-downloadpublishprofile]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-website-downloadpublishprofile.png
[screen-mongolab-websitedashboard]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-websitedashboard.png
[screen-mongolab-newwebsite]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-newwebsite.png
[button-new]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-new.png
[dialog-mongolab-csharp-newproject]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-csharp-newproject.png
[dialog-mongolab-csharp-projecttemplate]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/dialog-mongolab-csharp-projecttemplate.png
[focus-mongolab-csharp-pmconsole]: ./media/store-mongolab-web-sites-dotnet-store-data-mongodb/focus-mongolab-csharp-pmconsole.png
[button-store]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-store.png
[entry-mongolab]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/entry-mongolab.png 
[button-connectioninfo]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-connectioninfo.png
[screen-connectioninfo]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab_connectioninfo.png
[focus-website-connectinfo]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-mongolab-websiteconnectionstring.png
[provision]: #provision
[create]: #create
[deploy]: #deploy
[manage]: #manage


<!--HONumber=35.1-->
