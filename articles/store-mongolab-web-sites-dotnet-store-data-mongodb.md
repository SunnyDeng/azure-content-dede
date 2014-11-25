<properties linkid="develop-net-tutorials-website-with-mongodb-mongolab" urlDisplayName="Website with MongoDB on MongoLab" pageTitle="Create a Website that uses MongoDB on MongoLab (.NET)" metaKeywords="" description="Learn how to create an Azure website that stores data in MongoDB hosted by MongoLab." metaCanonical="" services="web-sites" documentationCenter=".NET" title="Create a C# ASP.NET Application on Azure with MongoDB using the MongoLab Add-On" authors="eric@mongolab.com" solutions="" manager="" editor="mollybos" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="eric@mongolab.com" />

# Erstellen einer Node.js-Anwendung auf Azure mit MongoDB mithilfe des MongoLab-Add-On

*Von Eric Sedor, MongoLab*

Grüße, Abenteurer! Willkommen bei MongoDB-as-a-Service. In diesem Lernprogramm lernen Sie Folgendes:

1.  [Bereitstellen der Datenban][Bereitstellen der Datenban]: Das Azure Store-Add-On [MongoLab][MongoLab] stellt eine MongoDB-Datenbank bereit, die in der Azure-Cloud gehostet und durch die Clouddatenbankplattform von MongoLab verwaltet wird..
2.  [Erstellen der Anwendung][Erstellen der Anwendung]: Es handelt sich um eine einfache C# ASP.NET MVC-App zum Erstellen von Notizen.
3.  [Bereitstellen der App][Bereitstellen der App]: Durch das Verknüpfen einiger Konfigurationshooks wird das Pushen unseres Codes sehr einfach.
4.  [Verwalten der Datenbank][Verwalten der Datenbank]: Schließlich zeigen wir Ihnen das webbasierte Datenbankverwaltungsportal von MongoLab, in dem Sie Daten problemlos suchen, visualisieren und ändern können.

Falls Sie Fragen haben, können Sie in diesem Lernprogramm jederzeit eine E-Mail an [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>][\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>] senden.

## Schnellstart

Wenn Sie bereits eine Azure-Anwendung und eine Website besitzen, mit denen Sie arbeiten möchten, oder wenn Sie den Azure Store bereits kennengelernt haben, ermöglicht Ihnen dieser Abschnitt einen schnellen Einstieg. Fahren Sie andernfalls mit [Bereitstellen der Datenbank][Bereitstellen der Datenban] weiter unten fort.

1.  Öffnen Sie den Azure Store.
    ![Store][Store]
2.  Erwerben Sie das MongoLab-Add-On.
    ![MongoLab][1]
3.  Klicken Sie in der Liste der Add-Ons auf das MongoLab-Add-On und dann auf **Verbindungsinformationen**.
    ![ConnectionInfoButton][ConnectionInfoButton]
4.  Kopieren Sie MONGOLAB\_URI in die Zwischenablage.
    ![ConnectionInfoScreen][ConnectionInfoScreen]
    **Dieser URI enthält Ihren Benutzernamen und Ihr Kennwort für die Datenbank. Behandeln Sie diese Angaben als kritische Informationen, und teilen Sie sie niemandem mit.**
5.  Fügen Sie den Wert der Liste mit den Verbindungszeichenfolgen im Konfigurationsmenü der Azure-Webanwendung hinzu:
    ![WebSiteConnectionStrings][WebSiteConnectionStrings]
6.  Geben Sie unter **Name** MONGOLAB\_URI ein.
7.  Fügen Sie unter **Wert** die Verbindungszeichenfolge ein, die wir im vorhergehenden Abschnitt erzeugt haben.
8.  Wählen Sie im Dropdownfeld für den Typ die Option **Benutzerdefiniert** aus (anstelle des Standards **SQLAzure**).
9.  Installieren Sie den Mongo C#-Treiber in Visual Studio, indem Sie **Extras \> Library Package Manager \> Paket-Manager-Konsole**. Geben Sie in der Paket-Manager-Konsole **Install-Package mongocsharpdriver** ein, und drücken Sie die **Eingabetaste**.
10. Richten Sie im Code einen Hook ein, um die MongoLab-Verbindungs-URI aus der Umgebungsvariablen abzurufen:

        using MongoDB.Driver;  
        ...
        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        ...
        MongoUrl url = new MongoUrl(connectionString);
        MongoClient client = new MongoClient(url);

    Hinweis: Azure fügt den Präfix **CUSTOMCONNSTR\_** zur ursprünglich deklarierten Verbindungszeichenfolge hinzu. Darum verweist der Code auf **CUSTOMCONNSTR\_MONGOLAB\_URI.**, nicht auf **MONGOLAB\_URI**.

Nun folgt das vollständige Lernprogramm ...

## <a name="provision"></a>Bereitstellen der Datenbank

[WACOM.INCLUDE [howto-provision-mongolab](../includes/howto-provision-mongolab.md)]

## <a name="create"></a>Erstellen der App

In diesem Abschnitt wird die Erstellung eines C# ASP.NET Visual Studio-Projekts beschrieben und schrittweise erläutert, wie mit dem C# MongoDB-Treiber eine einfache Notizanwendung erstellt wird. Sie möchten Ihre Website besuchen, eine Notiz schreiben und alle Notizen anzeigen, die hinterlegt wurden.

Sie verwenden für diese Entwicklung Visual Studio Express 2012 für das Web.

### Erstellen des Projekts

Die Beispiel-App basiert der Einfachheit halber auf einer Visual Studio-Vorlage. Verwenden Sie unbedingt .NET Framework 4.0.

1.  Wählen Sie **Datei \> Neues Projekt** aus. Das Dialogfeld "Neues Projekt" wird angezeigt:
    ![NewProject][NewProject]
2.  Wählen Sie **Installiert \> Vorlagen \> Visual C# \> Web** aus.
3.  Wählen Sie im Dropdownmenü für die .NET-Version den Eintrag **.NET Framework 4** aus (*Hinweis: Framework 4.5 funktioniert derzeit nicht*).

    ![Projekt-Framework][Projekt-Framework]

4.  Wählen Sie **ASP.NET MVC 4-Webanwendung**.
5.  Geben Sie *mongoNotes* als **Projektname** ein. Wenn Sie einen anderen Namen wählen, müssen Sie den bereitgestellten Code im gesamten Lernprogramm abändern.
6.  Klicken Sie auf **OK**. Das Dialogfeld "Projektvorlage" wird angezeigt:
    ![ProjectTemplate][ProjectTemplate]
7.  Wählen Sie **Internetanwendung** aus, und klicken Sie auf **OK**. Das Projekt wird erstellt.
8.  Wählen **Extras \> Library Package Manager \> Paket-Manager-Konsole** aus. Geben Sie in der Paket-Manager-Konsole **Install-Package mongocsharpdriver** ein, und drücken Sie **EINGABE**.
    ![PMConsole][PMConsole]
     Der MongoDB C#-Treiber wird in das Projekt integriert, und die folgende Zeile wird der Datei *packages.config* automatisch hinzugefügt:

        < package id="mongocsharpdriver" version="1.8" targetFramework="net40" / >

### Hinzufügen eines Notizenmodells

Zuerst richten Sie ein Modell für Notizen ein, das nur ein Datum und Textinhalt enthält.

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Modelle**, und wählen Sie **Add \> Klasse** aus. Geben Sie dieser neuen Klasse den Namen *Note.cs*.
2.  Ersetzen Sie den automatisch generierten Code für diese Klasse durch folgenden Code:

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

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **mongoNotes**, und wählen Sie **Hinzufügen \> Neuer Ordner** aus. Geben Sie dem Ordner den Namen **DAL**.
2.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **DAL**, und wählen Sie **Hinzufügen \> Klasse** aus. Geben Sie dieser neuen Klasse den Namen *Dal.cs*.
3.  Ersetzen Sie den automatisch generierten Code für diese Klasse durch folgenden Code:

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

4.  Beachten Sie den folgenden Code über:

        private string connectionString = System.Environment.GetEnvironmentVariable("CUSTOMCONNSTR_MONGOLAB_URI");
        private string dbName = "myMongoApp";  

    Hier greifen Sie auf eine Umgebungsvariable zu, die Sie später konfigurieren werden. Falls Sie eine lokale Mongo-Instanz zu Entwicklungszwecken haben, können Sie diesen Wert temporär auf "localhost" festlegen.

Legen Sie auch den Datenbanknamen fest. Genauer gesagt, legen Sie den **dbName**-Wert auf den Namen fest, den Sie bei der Bereitstellung des MongoLab Add-Ons eingegeben haben.

1.  Untersuchen Sie schließlich den Code in **GetNotesCollection()**:

        MongoClient client = new MongoClient(url);
        mongoServer = client.GetServer();
        MongoDatabase database = mongoServer.GetDatabase(dbName);
        MongoCollection<Note> noteCollection = database.GetCollection<Note>(collectionName);

    Hier muss nichts geändert werden. Sie sehen hier nur, wie Sie ein MongoCollection-Objekt zum Ausfühen von Einfügungen, Aktualisierungen und Abfragen erhalten, wie das folgende Objekt in **GetAllNotes()**:

        collection.FindAll().ToList<Note>();

Weitere Informationen zur Verwendung des C# MongoDB-Treibers finden Sie im [CSharp Driver QuickStart][CSharp Driver QuickStart] auf mongodb.org.

### Hinzufügen der Ansicht "Create"

Nun fügen Sie eine Ansicht zum Erstellen einer neuen Notiz hinzu.

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Ansichten \> Home**, und wählen Sie **Hinzufügen \> Ansicht** aus. Geben Sie dieser Ansicht den Namen **Create**, und klicken Sie auf **Hinzufügen**.
2.  Ersetzten Sie den automatisch für diese Ansicht generierten Code (**Create.cshtml**) durch den folgenden Code:

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

1.  Öffnen Sie unter **Ansichten \> Home** die Datei **Index.cshtml**, und ersetzen Sie ihren Inhalt durch folgenden Code:

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

1.  Öffnen Sie im Projektmappen-Explorer unter **Controller** die Datei **HomeController.cs**, und ersetzen Sie ihren Inhalt durch folgenden Code:

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

## <a name="deploy"></a>Bereitstellen der App

Da die Anwendung nun entwickelt wurde, sollten Sie eine Azure-Website zum Hosten der Anwendung erstellen und konfigurieren und den Code bereitstellen. In diesem Abschnitt ist die Verwendung der MongoDB-Verbindungszeichenfolge (URI) ein zentraler Aspekt. Sie konfigurieren auf Ihrer Website eine Umgebungsvariable mit diesem URI, um den URI vom Code zu trennen. Sie sollten die URI als vertrauliche Information behandeln, da sie Anmeldeinformationen für die Verbindung zur Datenbank enthält.

### Erstellen einer neuen Website und Abrufen der Datei mit den Veröffentlichungseinstellungen

Das Erstellen einer Website in Azure ist sehr einfach, insbesondere weil Azure automatisch ein Veröffentlichungsprofil für Visual Studio generiert.

1.  Klicken Sie im Azure-Portal auf **Neu**.
    ![New][New]
2.  Wählen Sie **Berechnen \> Website \> Schnellerfassung** aus.
    ![CreateSite][CreateSite]
3.  Geben Sie einen URL-Präfix ein. Wählen Sie einen Namen aus, aber denken Sie daran, dass er eindeutig sein muss ("mongoNotes" ist wahrscheinlich nicht verfügbar).
4.  Klicken Sie auf **Website erstellen**.
5.  Klicken Sie nach Abschluss der Websiteerstellung auf den Namen der Website in der Websiteliste. Das Website-Dashboard wird angezeigt.
    ![WebSiteDashboard][WebSiteDashboard]
6.  Klicken Sie unter **Auf einen Blick** auf **Veröffentlichungsprofil herunterladen**, und speichern Sie die PUBLISHSETTINGS-Datei in einem Verzeichnis Ihrer Wahl.
    ![DownloadPublishProfile][DownloadPublishProfile]

### Abrufen der MongoLab-Verbindungszeichenfolge

[WACOM.INCLUDE [howto-get-connectioninfo-mongolab](../includes/howto-get-connectioninfo-mongolab.md)]

### Hinzufügen der Verbindungszeichenfolge zu den Umgebungsvariablen der Website

[WACOM.INCLUDE [howto-save-connectioninfo-mongolab](../includes/howto-save-connectioninfo-mongolab.md)]

### Veröffentlichen der Website

1.  Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **mongoNotes**, und wählen Sie **Veröffentlichen** aus. Das Dialogfeld "Veröffentlichen" wird angezeigt.
    ![Veröffentlichen][Veröffentlichen]
2.  Klicken Sie auf **Importieren**, und wählen Sie die .PublishSettings im gewählten Download-Verzeichnis aus. Diese Datei trägt automatisch Werte in das Dialogfeld "Veröffentlichen" ein.
3.  Klicken Sie auf **Verbindung prüfen**, um die Datei zu testen.
4.  Wenn die Überprüfung erfolgreich war, klicken Sie auf **Veröffentlichen**. Sobald die Veröffentlichung abgeschlossen ist, wird eine neue Browserregisterkarte geöffnet und die Website angezeigt.
5.  Geben Sie Notizentext ein, klicken Sie auf **Erstellen**, und betrachten Sie das Ergebnis!
    ![HelloMongoAzure][HelloMongoAzure]

## <a name="manage"></a>Verwalten der Datenbank

[WACOM.INCLUDE [howto-access-mongolab-ui](../includes/howto-access-mongolab-ui.md)]

Glückwunsch! Sie haben gerade eine C# ASP.NET-Anwendung mit einer von MongoLab gehosteten MongoDB-Datenbank gestartet! Das Sie jetzt über eine MongoLab-Datenbank verfügen, können Sie sich mit allen Fragen oder Bedenken bezüglich Ihrer Datenbank sowie mit Hilfeanfragen zu MongoDB oder dem C#-Treiber selbst an [\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>][\<a href="mailto:support@mongolab.com"\>support@mongolab.com\</a\>] wenden. Viel Glück!

  [Bereitstellen der Datenban]: #provision
  [MongoLab]: http://mongolab.com
  [Erstellen der Anwendung]: #create
  [Bereitstellen der App]: #deploy
  [Verwalten der Datenbank]: #manage
  [Store]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-store.png
  [1]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/entry-mongolab.png
  [ConnectionInfoButton]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-connectioninfo.png
  [ConnectionInfoScreen]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab_connectioninfo.png
  [WebSiteConnectionStrings]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-mongolab-websiteconnectionstring.png
  [NewProject]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-csharp-newproject.png
  [Projekt-Framework]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-dotNet-Framework4-mongolab.png
  [ProjectTemplate]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-csharp-projecttemplate.png
  [PMConsole]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/focus-mongolab-csharp-pmconsole.png
  [CSharp Driver QuickStart]: http://www.mongodb.org/display/DOCS/CSharp+Driver+Quickstart "CSharp Driver Quickstart"
  [New]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-new.png
  [CreateSite]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-newwebsite.png
  [WebSiteDashboard]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-websitedashboard.png
  [DownloadPublishProfile]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/button-website-downloadpublishprofile.png
  [Veröffentlichen]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/dialog-mongolab-vspublish.png
  [HelloMongoAzure]: ./media/partner-mongodb-web-sites-dotnet-use-mongolab/screen-mongolab-sampleapp.png
