<properties 
	pageTitle=".NET-Website mit MongoDB auf einem virtuellen Computer - Azure" 
	description="In diesem Lernprogramm erfahren Sie, wie Sie Git verwenden, um eine ASP.NET-App an eine Azure-Website bereitzustellen, die mit MongoDB auf einem virtuellen Computer verbunden ist." 
	services="web-sites, virtual-machines" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 	
	editor=""/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/24/2014" 
	ms.author="cephalin"/>


# Erstellen einer Azure-Website, die eine Verbindung mit einer MongoDB herstellt, die auf einem virtuellen Computer in Azure ausgeführt wird

Mit Git können Sie eine ASP.NET-Anwendung auf einer Azure-Website bereitstellen. In diesem Lernprogramm erstellen Sie eine einfache Front-End-ASP.NET-MVC-Tasklist-Anwendung, die eine Verbindung mit einer MongoDB-Datenbank herstellt, die auf einem virtuellen Computer in Azure ausgeführt wird.  [MongoDB][MongoDB] ist eine beliebte, leistungsfähige Open Source-NoSQL-Datenbank. Nach dem Ausführen und Testen der ASP.NET-Anwendung auf Ihrem Entwicklungscomputer laden Sie die Anwendung mithilfe von Git zu einer Azure-Website hoch.

[AZURE.INCLUDE [create-account-and-websites-and-vms-note](../includes/create-account-and-websites-and-vms-note.md)]



##Übersicht##

In diesem Lernprogramm führen Sie folgende Schritte aus:

- [Erstellen eines virtuellen Computers und Installieren von MongoDB](#virtualmachine)
- [Erstellen und Ausführen der ASP.NET-Anwendung "My Task List" auf dem Entwicklungscomputer](#createapp)
- [Erstellen einer Azure-Website](#createwebsite)
- [Bereitstellen der ASP.NET-Anwendung auf der Website mit Git](#deployapp)


##Hintergrundkenntnisse##

Die folgenden Kenntnisse sind für dieses Lernprogramm sinnvoll, jedoch nicht obligatorisch:

* C#-Treiber für MongoDB. Weitere Informationen zum Entwickeln von C#-Anwendungen für MongoDB finden Sie im [CSharp Language Center][MongoC#LangCenter] von MongoDB. 
* ASP.NET-Webanwendungs-Framework. Informationen dazu finden Sie auf der [ASP.NET-Website][ASP.NET].
* ASP.NET-MVC-Webanwendungs-Framework. Umfassende Informationen dazu finden Sie auf der [ASP.NET-MVC-Website][MVCWebSite].
* Azure. Einführende Informationen finden Sie unter [Azure][WindowsAzure].


##Vorbereitung##

In diesem Abschnitt lernen Sie, wie Sie in Azure einen virtuellen Computer erstellen, MongoDB installieren und Ihre Entwicklungsumgebung einrichten.

<a id="virtualmachine"></a> 
###Erstellen eines virtuellen Computers und Installieren von MongoDB###

Bei diesem Lernprogramm wird vorausgesetzt, dass Sie in Azure einen virtuellen Computer erstellt haben. Nach dem Erstellen des virtuellen Computers müssen Sie MongoDB auf dem virtuellen Computer installieren:

* Informationen zum Erstellen eines virtuellen Windows-Computers und zum Installieren von MongoDB finden Sie unter [Installieren von MongoDB auf einem virtuellen Computer unter Windows Server in Azure][InstallMongoOnWindowsVM].
* Wenn Sie hingegen einen virtuellen Linux-Computer und MongoDB installieren möchten, finden Sie entsprechende Informationen unter [Installieren von MongoDB auf einem virtuellen Computer mit CentOS Linux in Azure][InstallMongoOnCentOSLinuxVM].

Nachdem Sie den virtuellen Computer in Azure erstellt und MongoDB installiert haben, merken Sie sich den DNS-Namen des virtuellen Computers (z. B. "testlinuxvm.cloudapp.net") und den externen Port für MongoDB, den Sie im Endpunkt angegeben haben.  Sie benötigen diese Informationen später im Lernprogramm.

### Installieren von Visual Studio###

Beginnen Sie mit der Installation und dem Ausführen von [Visual Studio Express 2013 für Web] [VSEWeb] oder [Visual Studio 2013] [VSUlt].

Visual Studio ist eine integrierte Entwicklungsumgebung. Wie Sie Microsoft Word zum Schreiben von Dokumenten verwenden, so verwenden Sie eine integrierte Entwicklungsumgebung zum Erstellen von Anwendungen. In diesem Lernprogramm wird Microsoft Visual Studio 2013 verwendet, Sie können jedoch auch Microsoft Visual Studio Express 2013 verwenden, eine kostenlose Version von Microsoft Visual Studio.

<a id="createapp"></a>
##Erstellen und Ausführen der ASP.NET-Anwendung "My Task List" auf dem Entwicklungscomputer##

In diesem Abschnitt erstellen Sie mit Visual Studio eine ASP.NET-Anwendung namens "My Task List".  Die Anwendung wird lokal ausgeführt, sie stellt jedoch eine Verbindung mit dem virtuellen Computer in Azure her und verwendet die dort erstellte MongoDB-Instanz.

###Erstellen der Anwendung###
Klicken Sie in Visual Studio auf **Neues Projekt**.

![Start Page New Project][StartPageNewProject]

Wählen Sie im Fenster **Neues Projekt** im linken Bereich **Visual C#** aus, und wählen Sie dann **Web** aus. Wählen Sie im mittleren Bereich **ASP.NET-Webanwendung** aus. Geben Sie unten den Namen "MyTaskListApp" für das Projekt ein, und klicken Sie auf **OK**.

![New Project Dialog][NewProjectMyTaskListApp]

Wählen Sie im Dialogfeld **Neues ASP.NET-Projekt** die Option **MVC**, und klicken Sie dann auf **OK**.

![Select MVC Template][VS2013SelectMVCTemplate]

Nachdem das Projekt angelegt wurde, wird die von der Vorlage erstellte Standardseite angezeigt.

![Default ASP.NET MVC Application][VS2013DefaultMVCApplication]

###Installieren des C#-Treibers für MongoDB

MongoDB bietet eine clientseitige Unterstützung für C#-Anwendungen durch einen Treiber, den Sie auf Ihrem lokalen Entwicklungscomputer installieren müssen. Der C#-Treiber ist über NuGet erhältlich.

So installieren Sie den C#-Treiber für MongoDB

1. Klicken Sie im **Projektmappen-Explorer** unter dem Projekt **MyTaskListApp** mit der rechten Maustaste auf **Verweise** und wählen Sie **NuGet-Pakete verwalten**.

	![Manage NuGet Packages][VS2013ManageNuGetPackages]

2. Klicken Sie im linken Bereich des Fensters **NuGet-Pakete verwalten** auf **Online**. Geben Sie auf der rechten Seite in das Feld **Online suchen** "mongocsharpdriver" ein.  Klicken Sie auf **Installieren**, um den Treiber zu installieren.

	![Search for MongoDB C# Driver][SearchforMongoDBCSharpDriver]

3. Klicken Sie auf **Akzeptieren**, um den Lizenzbedingungen von 10gen, Inc. zuzustimmen.

4. Klicken Sie nach der Installation des Treibers auf **Schließen**.
	![MongoDB C# Driver Installed][MongoDBCsharpDriverInstalled]


Der C#-Treiber für MongoDB ist jetzt installiert.  Dem Projekt wurden Verweise auf die Bibliotheken **MongoDB.Driver.dll** und **MongoDB.Bson.dll** hinzugefügt.

![MongoDB C# Driver References][MongoDBCSharpDriverReferences]

###Hinzufügen eines Modells###
Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Models*, wählen Sie **Hinzufügen**, um eine neue **Klasse** hinzuzufügen, und benennen Sie sie *TaskModel.cs*.  Ersetzen Sie den vorhandenen Code in der Datei *TaskModel.cs* durch den folgenden Code:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MongoDB.Bson.Serialization.Attributes;
	using MongoDB.Bson.Serialization.IdGenerators;
	using MongoDB.Bson;
	
	namespace MyTaskListApp.Models
	{
	    public class MyTask
	    {
	        [BsonId(IdGenerator = typeof(CombGuidGenerator))]
	        public Guid Id { get; set; }
	
	        [BsonElement("Name")]
	        public string Name { get; set; }
	
	        [BsonElement("Category")]
	        public string Category { get; set; }
	
	        [BsonElement("Date")]
	        public DateTime Date { get; set; }
	
	        [BsonElement("CreatedDate")]
	        public DateTime CreatedDate { get; set; }
	
	    }
	}

###Hinzufügen der Datenzugriffsschicht###
Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt *MyTaskListApp*, wählen Sie **Hinzufügen** und **Neuer Ordner**, und benennen Sie diesen *DAL*.  Klicken Sie mit der rechten Maustaste auf den Ordner *DAL*, und wählen Sie **Hinzufügen**, um eine neue **Klasse** hinzuzufügen. Nennen Sie die Klassendatei *Dal.cs*.  Ersetzen Sie den vorhandenen Code in der Datei *Dal.cs* durch den folgenden Code:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MyTaskListApp.Models;
	using MongoDB.Driver;
	using System.Configuration;
	
	namespace MyTaskListApp
	{
	    public class Dal : IDisposable
	    {
	        private MongoServer mongoServer = null;
	        private bool disposed = false;
	
	        // To do: update the connection string with the DNS name
			// or IP address of your server. 
			//For example, "mongodb://testlinux.cloudapp.net"
	        private string connectionString = "mongodb://<vm-dns-name>";
	
	        // This sample uses a database named "Tasks" and a 
			//collection named "TasksList".  The database and collection 
			//will be automatically created if they don't already exist.
	        private string dbName = "Tasks";
	        private string collectionName = "TasksList";
	
	        // Default constructor.        
	        public Dal()
	        {
	        }        
	
	        // Gets all Task items from the MongoDB server.        
	        public List<MyTask> GetAllTasks()
	        {
	            try
	            {
	                MongoCollection<MyTask> collection = GetTasksCollection();
	                return collection.FindAll().ToList<MyTask>();
	            }
	            catch (MongoConnectionException)
	            {
	                return new List<MyTask >();
	            }
	        }
	
	        // Creates a Task and inserts it into the collection in MongoDB.
	        public void CreateTask(MyTask task)
	        {
	            MongoCollection<MyTask> collection = GetTasksCollectionForEdit();
	            try
	            {
	                collection.Insert(task, SafeMode.True);
	            }
	            catch (MongoCommandException ex)
	            {
	                string msg = ex.Message;
	            }
	        }
	
	        private MongoCollection<MyTask> GetTasksCollection()
	        {
	            MongoServer server = MongoServer.Create(connectionString);
	            MongoDatabase database = server[dbName];
	            MongoCollection<MyTask> todoTaskCollection = database.GetCollection<MyTask>(collectionName);
	            return todoTaskCollection;
	        }
	
	        private MongoCollection<MyTask> GetTasksCollectionForEdit()
	        {
	            MongoServer server = MongoServer.Create(connectionString);
	            MongoDatabase database = server[dbName];
	            MongoCollection<MyTask> todoTaskCollection = database.GetCollection<MyTask>(collectionName);
	            return todoTaskCollection;
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

###Hinzufügen eines Controllers###
Öffnen Sie die Datei *Controllers\HomeController.cs* im **Projektmappen-Explorer**, und ersetzen Sie den vorhandenen Code durch den folgenden:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using System.Web.Mvc;
	using MyTaskListApp.Models;
	using System.Configuration;
	
	namespace MyTaskListApp.Controllers
	{
	    public class HomeController : Controller, IDisposable
	    {
	        private Dal dal = new Dal();
	        private bool disposed = false;
	        //
	        // GET: /MyTask/
	
	        public ActionResult Index()
	        {
	            return View(dal.GetAllTasks());
	        }
	
	        //
	        // GET: /MyTask/Create
	
	        public ActionResult Create()
	        {
	            return View();
	        }
	
	        //
	        // POST: /MyTask/Create
	
	        [HttpPost]
	        public ActionResult Create(MyTask task)
	        {
	            try
	            {
	                dal.CreateTask(task);
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

###Einrichten des Websitestils###
Um den Titel oben auf der Seite zu ändern, öffnen Sie die Datei *Views\Shared\\_Layout.cshtml* im **Projektmappen-Explorer** und ersetzen "Application name" im Navigationsleistenheader durch "My Task List Application", sodass die Zeile folgendermaßen lautet:

 	@Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

Öffnen Sie zum Einrichten des Aufgabenlisten-Menüs die Datei *\Views\Home\Index.cshtml*, und ersetzen Sie den vorhandenen Code durch den folgenden:
	
	@model IEnumerable<MyTaskListApp.Models.MyTask>
	
	@{
	    ViewBag.Title = "My Task List";
	}
	
	<h2>My Task List</h2>
	
	<table border="1">
	    <tr>
	        <th>Task</th>
	        <th>Category</th>
	        <th>Date</th>
	        
	    </tr>
	
	@foreach (var item in Model) {
	    <tr>
	        <td>
	            @Html.DisplayFor(modelItem => item.Name)
	        </td>
	        <td>
	            @Html.DisplayFor(modelItem => item.Category)
	        </td>
	        <td>
	            @Html.DisplayFor(modelItem => item.Date)
	        </td>
	        
	    </tr>
	}
	
	</table>
	<div>  @Html.Partial("Create", new MyTaskListApp.Models.MyTask())</div>


Um die Möglichkeit zum Erstellen eines neuen Tasks hinzuzufügen, klicken Sie mit der rechten Maustaste auf den Ordner *Views\Home\\*, und fügen Sie über **Hinzufügen** eine neue **Ansicht** hinzu.  Benennen Sie die Ansicht *Create*. Ersetzen Sie den Code durch den folgenden Code:

	@model MyTaskListApp.Models.MyTask
	
	<script src="@Url.Content("~/Scripts/jquery-1.10.2.min.js")" type="text/javascript"></script>
	<script src="@Url.Content("~/Scripts/jquery.validate.min.js")" type="text/javascript"></script>
	<script src="@Url.Content("~/Scripts/jquery.validate.unobtrusive.min.js")" type="text/javascript"></script>
	
	@using (Html.BeginForm("Create", "Home")) {
	    @Html.ValidationSummary(true)
	    <fieldset>
	        <legend>New Task</legend>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Name)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Name)
	            @Html.ValidationMessageFor(model => model.Name)
	        </div>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Category)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Category)
	            @Html.ValidationMessageFor(model => model.Category)
	        </div>
	
	        <div class="editor-label">
	            @Html.LabelFor(model => model.Date)
	        </div>
	        <div class="editor-field">
	            @Html.EditorFor(model => model.Date)
	            @Html.ValidationMessageFor(model => model.Date)
	        </div>
	
	        <p>
	            <input type="submit" value="Create" />
	        </p>
	    </fieldset>
	}

Der **Projektmappen-Explorer** sollte folgendermaßen aussehen:

![Solution Explorer][SolutionExplorerMyTaskListApp]

###Festlegen der MongoDB-Verbindungszeichenfolge###
Öffnen Sie im **Projektmappen-Explorer** die Datei *DAL/Dal.cs*. Suchen Sie die folgende Codezeile:

	private string connectionString = "mongodb://<vm-dns-name>";

Ersetzen Sie `<vm-dns-name>` durch den DNS-Namen des virtuellen Computers mit MongoDB, den Sie in diesem Lernprogramm im Schritt [Erstellen eines virtuellen Computers und Installieren von MongoDB][] erstellt haben.  Um den DNS-Namen des virtuellen Computers zu finden, gehen Sie zum Azure-Verwaltungsportal, wählen Sie **Virtuelle Computer** aus, und suchen Sie **DNS-Name**.

Wenn der DNS-Name des virtuellen Computers "testlinuxvm.cloudapp.net" lautet und MongoDB am Standardport 27017 empfangsbereit ist, lautet die Verbindungszeichenfolge folgendermaßen:

	private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

Wenn der Endpunkt des virtuellen Computers einen anderen externen Port für MongoDB angibt, können Sie den Port in der Verbindungszeichenfolge angeben:

 	private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

Weitere Informationen über MongoDB-Verbindungszeichenfolgen finden Sie unter [Verbindungen][MongoConnectionStrings].

###Testen der lokalen Bereitstellung###

Um die Anwendung auf dem Entwicklungscomputer auszuführen, wählen Sie **Debugging starten** im Menü **Debuggen**, oder drücken Sie **F5**. IIS Express wird gestartet, und in einem Browser wird die Startseite der Anwendung aufgerufen.  Sie können einen neuen Task hinzufügen, der der MongoDB-Datenbank hinzugefügt wird, die in Azure auf dem virtuellen Computer ausgeführt wird.

![My Task List Application][TaskListAppBlank]

<h2>Bereitstellen der ASP.NET-Webanwendung für eine Azure-Website</h2>

In diesem Abschnitt erstellen Sie eine Website und stellen die ASP.NET-Anwendung "My Task List" mit Git bereit.

<a id="createwebsite"></a> 
###Erstellen einer Azure-Website###
In diesem Abschnitt erstellen Sie eine Azure-Website.

1. Öffnen Sie einen Webbrowser, und navigieren Sie zum [Azure-Verwaltungsportal][AzurePortal]. Melden Sie sich mit Ihrem Azure-Konto an. 
2. Klicken Sie unten auf der Seite auf **+Neu**, danach auf **Website** und schließlich auf **Schnellerfassung**.
3. Geben Sie ein eindeutiges Präfix für die Anwendungs-URL ein.
4. Wählen Sie eine Region aus.
5. Klicken Sie auf **Website erstellen**.

![Create a new web site][WAWSCreateWebSite]

6. Die Website wird schnell erstellt und unter **Websites** aufgeführt.

![WAWSDashboardMyTaskListApp][WAWSDashboardMyTaskListApp]

<a id="deployapp"></a> 
###Bereitstellen der ASP.NET-Anwendung auf der Website mit Git
In diesem Abschnitt stellen Sie die Anwendung "My Task List" mit Git bereit.

1. Klicken Sie unter **Websites** auf den Namen der Website und dann auf **Dashboard**.  Klicken Sie rechts unter "Auf einen Blick" auf **Bereitstellung über Quellcodeverwaltung einrichten**.
2. Wählen Sie auf der Seite **Wo befindet sich Ihr Quellcode?** die Option **Lokales Git-Repository**, und klicken Sie auf den Pfeil **Weiter**. 
3. Das Git-Repository wird in der Regel schnell erstellt. Notieren Sie sich die Anweisungen auf der resultierenden Seite, da sie im nächsten Abschnitt verwendet werden.

	![Git Repository is Ready][Image9]

4. Unter **Lokale Dateien mithilfe von Push nach Azure übertragen** finden Sie Anweisungen zum Übertragen des Codes nach Azure. Die Anweisungen sehen etwa wie folgt aus:

	![Push local files to Azure][Image10]
	
5. Wenn Git nicht installiert ist, installieren Sie es mithilfe des Links **Get it here** in Schritt 1.
6. Fügen Sie die lokalen Dateien entsprechend den Anweisungen in Schritt 2 hinzu.  
7. Fügen Sie das Azure-Remote-Repository hinzu, und übertragen Sie die Dateien mithilfe von Push entsprechend den Anweisungen in Schritt 3 auf die Azure-Website.
8. Wenn die Bereitstellung abgeschlossen ist, wird die folgende Bestätigung angezeigt:

	![Deployment Complete][Image11]

9. Ihre Azure-Website ist jetzt verfügbar.  Prüfen Sie die Seite **Dashboard** für Ihre Website und das Feld **Website-URL**, um die URL für Ihre Website zu finden. Wenn Sie die Vorgehensweisen in diesem Lernprogramm durchführen, steht Ihre Website unter folgender URL zur Verfügung: http://mytasklistapp.azurewebsites.net.

##Zusammenfassung##

Sie haben Ihre ASP.NET-Anwendung erfolgreich auf einer Azure-Website bereitgestellt.  Um die Website anzuzeigen, klicken Sie auf den Link im Feld **Website-URL** auf der Seite **Dashboard**. Weitere Informationen zum Entwickeln von C#-Anwendungen für MongoDB finden Sie im [CSharp Language Center][MongoC#LangCenter]. 


<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnCentOSLinuxVM]: /de-de/manage/linux/common-tasks/mongodb-on-a-linux-vm/
[InstallMongoOnWindowsVM]: /de-de/manage/windows/common-tasks/install-mongodb/
[VSEWeb]: http://www.microsoft.com/visualstudio/eng/2013-downloads#d-2013-express
[VSUlt]: http://www.microsoft.com/visualstudio/eng/2013-downloads

<!-- IMAGES -->


[StartPageNewProject]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProject.png
[NewProjectMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/NewProjectMyTaskListApp.png
[VS2013SelectMVCTemplate]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013SelectMVCTemplate.png
[VS2013DefaultMVCApplication]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013DefaultMVCApplication.png
[VS2013ManageNuGetPackages]: ./media/web-sites-dotnet-store-data-mongodb-vm/VS2013ManageNuGetPackages.png
[SearchforMongoDBCSharpDriver]: ./media/web-sites-dotnet-store-data-mongodb-vm/SearchforMongoDBCSharpDriver.png
[MongoDBCsharpDriverInstalled]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCsharpDriverInstalled.png
[MongoDBCSharpDriverReferences]: ./media/web-sites-dotnet-store-data-mongodb-vm/MongoDBCSharpDriverReferences.png
[SolutionExplorerMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/SolutionExplorerMyTaskListApp.png
[TaskListAppBlank]: ./media/web-sites-dotnet-store-data-mongodb-vm/TaskListAppBlank.png
[WAWSCreateWebSite]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSCreateWebSite.png
[WAWSDashboardMyTaskListApp]: ./media/web-sites-dotnet-store-data-mongodb-vm/WAWSDashboardMyTaskListApp.png
[Image9]: ./media/web-sites-dotnet-store-data-mongodb-vm/RepoReady.png
[Image10]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitInstructions.png
[Image11]: ./media/web-sites-dotnet-store-data-mongodb-vm/GitDeploymentComplete.png

<!-- TOC BOOKMARKS -->
[Erstellen eines virtuellen Computers und Installieren von MongoDB]: #virtualmachine
[Erstellen und Ausführen der ASP.NET-Anwendung "My Task List" auf dem Entwicklungscomputer]: #createapp
[Erstellen einer Azure-Website]: #createwebsite
[Bereitstellen der ASP.NET-Anwendung auf der Website mit Git]: #deployapp




<!--HONumber=42-->
