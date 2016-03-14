<properties 
	pageTitle="Erstellen einer Web-App in Azure, die eine Verbindung mit einer auf einem virtuellen Computer ausgeführten MongoDB herstellt" 
	description="In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Git eine ASP.NET-App im Azure App Service bereitstellen, die mit MongoDB auf einer Azure Virtual Machine verbunden ist."
	tags="azure-portal" 
	services="app-service\web, virtual-machines" 
	documentationCenter=".net" 
	authors="cephalin" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/29/2016" 
	ms.author="cephalin"/>


# Erstellen einer Web-App in Azure, die eine Verbindung mit einer auf einem virtuellen Computer ausgeführten MongoDB herstellt

Mit Git können Sie eine ASP.NET-Anwendung für Azure App Service-Web-Apps bereitstellen. In diesem Lernprogramm erstellen Sie eine einfache Front-End-ASP.NET-MVC-Tasklist-Anwendung, die eine Verbindung mit einer MongoDB-Datenbank herstellt, die auf einem virtuellen Computer in Azure ausgeführt wird. [MongoDB][MongoDB] ist eine beliebte, leistungsfähige Open Source-NoSQL-Datenbank. Nach dem Ausführen und Testen der ASP.NET-Anwendung auf Ihrem Entwicklungscomputer laden Sie die Anwendung mithilfe von Git in eine App Service-Web-App hoch.

>[AZURE.NOTE] Wenn Sie Azure App Service ausprobieren möchten, ehe Sie sich für ein Azure-Konto anmelden, können Sie unter [App Service testen](http://go.microsoft.com/fwlink/?LinkId=523751) sofort kostenlos eine kurzlebige Starter-Web-App in App Service erstellen. Keine Kreditkarte erforderlich, keine Verpflichtungen.


## Hintergrundkenntnisse ##

Die folgenden Kenntnisse sind für dieses Lernprogramm sinnvoll, jedoch nicht obligatorisch:

* C#-Treiber für MongoDB. Weitere Informationen zum Entwickeln von C#-Anwendungen für MongoDB finden Sie im [CSharp Language Center][MongoC#LangCenter] von MongoDB. 
* ASP.NET-Webanwendungs-Framework. Informationen dazu finden Sie auf der [ASP.net-Website][ASP.NET].
* ASP.NET-MVC-Webanwendungs-Framework. Informationen dazu finden Sie auf der [ASP.NET MVC-Website][MVCWebSite].
* Azure. Einführende Informationen finden Sie unter [Azure][WindowsAzure].

## Voraussetzungen ##

- [Visual Studio Express 2013 für Web][VSEWeb] oder [Visual Studio 2013][VSUlt]
- [Azure SDK für .NET](http://go.microsoft.com/fwlink/p/?linkid=323510&clcid=0x409)
- Ein aktives Microsoft Azure-Abonnement

[AZURE.INCLUDE [create-account-and-websites-note](../../includes/create-account-and-websites-note.md)]

<a id="virtualmachine"></a>
## Erstellen eines virtuellen Computers und Installieren von MongoDB ##

Bei diesem Lernprogramm wird vorausgesetzt, dass Sie in Azure einen virtuellen Computer erstellt haben. Nach dem Erstellen des virtuellen Computers müssen Sie MongoDB auf dem virtuellen Computer installieren:

* Informationen zum Erstellen eines virtuellen Windows-Computers und zum Installieren von MongoDB finden Sie unter [Install MongoDB on a virtual machine running Windows Server in Azure][InstallMongoOnWindowsVM] (Installieren von MongoDB auf einem virtuellen Computer mit Windows Server in Azure, in englischer Sprache).


Nachdem Sie den virtuellen Computer in Azure erstellt und MongoDB installiert haben, merken Sie sich den DNS-Namen des virtuellen Computers (z. B. "testlinuxvm.cloudapp.net") und den externen Port für MongoDB, den Sie im Endpunkt angegeben haben. Sie benötigen diese Informationen später im Lernprogramm.

<a id="createapp"></a>
## Erstellen der Anwendung ##

In diesem Abschnitt erstellen Sie mit Visual Studio eine ASP.NET-Anwendung namens "My Task List" und führen eine erste Bereitstellung für Azure App Service-Web-Apps durch. Die Anwendung wird lokal ausgeführt, sie stellt jedoch eine Verbindung mit dem virtuellen Computer in Azure her und verwendet die dort erstellte MongoDB-Instanz.

1. Klicken Sie in Visual Studio auf **Neues Projekt**.

	![Startseite "Neues Projekt"][StartPageNewProject]

1. Wählen Sie im Fenster **Neues Projekt** im linken Bereich **Visual C#** aus, und wählen Sie dann **Web** aus.. Wählen Sie im mittleren Bereich **ASP.NET-Webanwendung** aus. Geben Sie unten den Namen "MyTaskListApp" für das Projekt ein, und klicken Sie auf **OK**.

	![Dialogfeld "Neues Projekt"][NewProjectMyTaskListApp]

1. Wählen Sie im Dialogfeld **New ASP.NET Project** die Option **MVC**, und klicken Sie dann auf **OK**.

	![MVC-Vorlage auswählen][VS2013SelectMVCTemplate]

1. Wenn Sie nicht bereits bei Microsoft Azure angemeldet sind, werden Sie aufgefordert, sich anzumelden. Folgen Sie den Anweisungen, um sich bei Azure anzumelden.
2. Sobald Sie angemeldet sind, können Sie beginnen, Ihre App Service-Web-App zu konfigurieren. Nehmen Sie Eingaben in den Feldern **Web-App-Name**, **App Service-Plan**, **Ressourcengruppe** und **Region** vor, und klicken Sie dann auf **Erstellen**.

	![](./media/web-sites-dotnet-store-data-mongodb-vm/VSConfigureWebAppSettings.png)

1. Nachdem die Projekterstellung abgeschlossen ist, warten Sie, bis die Web-App im Azure App Service erstellt wurde, wie im Fenster **Azure App Service-Aktivität** angegeben. Klicken Sie dann auf**MyTaskListApp jetzt in dieser Web-App veröffentlichen**.

1. Klicken Sie auf **Veröffentlichen**.

	![](./media/web-sites-dotnet-store-data-mongodb-vm/VSPublishWeb.png)

	Sobald die Standard-ASP.NET-Anwendung in Azure App Service-Web-Apps veröffentlicht wurde, wird sie im Browser gestartet.

## Installieren des C#-Treibers für MongoDB

MongoDB bietet eine clientseitige Unterstützung für C#-Anwendungen durch einen Treiber, den Sie auf Ihrem lokalen Entwicklungscomputer installieren müssen. Der C#-Treiber ist über NuGet erhältlich.

So installieren Sie den C#-Treiber für MongoDB

1. Klicken Sie mit der rechten Maustaste im **Projektmappen-Explorer** auf das Projekt **MyTaskListApp**, und wählen Sie **NuGet-Pakete verwalten** aus.

	![NuGet-Pakete verwalten][VS2013ManageNuGetPackages]

2. Klicken Sie im linken Bereich des Fensters **NuGet-Pakete verwalten** auf **Online**. Geben Sie auf der rechten Seite in das Feld **Online suchen** "mongodb.driver" ein. Klicken Sie auf **Installieren**, um den Treiber zu installieren.

	![Nach C#-Treiber für MongoDB suchen][SearchforMongoDBCSharpDriver]

3. Klicken Sie auf **Akzeptieren**, um den Lizenzbedingungen von 10gen, Inc. zuzustimmen.

4. Klicken Sie nach der Installation des Treibers auf **Schließen**. ![C#-Treiber für MongoDB installiert][MongoDBCsharpDriverInstalled]


Der C#-Treiber für MongoDB ist jetzt installiert. Dem Projekt wurden Verweise auf die Bibliotheken **MongoDB.Bson**, **MongoDB.Driver** und **MongoDB.Driver.Core** hinzugefügt.

![Verweise auf C#-Treiber für MongoDB][MongoDBCSharpDriverReferences]

## Hinzufügen eines Modells ##
Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Models*, fügen Sie eine neue **Klasse** hinzu, und benennen Sie sie *TaskModel.cs*. Ersetzen Sie den vorhandenen Code in der Datei *TaskModel.cs* durch den folgenden Code:

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

## Hinzufügen der Datenzugriffsschicht ##
Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt *MyTaskListApp* und fügen Sie einen **Neuen Ordner** mit dem Namen *DAL* hinzu. Klicken Sie mit der rechten Maustaste auf den Ordner *DAL*, und fügen Sie eine neue **Klasse** hinzu. Benennen Sie die Klassendatei *Dal.cs*. Ersetzen Sie den vorhandenen Code in der Datei *Dal.cs* durch den folgenden Code:

	using System;
	using System.Collections.Generic;
	using System.Linq;
	using System.Web;
	using MyTaskListApp.Models;
	using MongoDB.Driver;
	using MongoDB.Bson;
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
	        private string connectionString = "mongodb://mongodbsrv20151211.cloudapp.net";
	
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
	                var collection = GetTasksCollection();
	                return collection.Find(new BsonDocument()).ToList();
	            }
	            catch (MongoConnectionException)
	            {
	                return new List<MyTask>();
	            }
	        }
	
	        // Creates a Task and inserts it into the collection in MongoDB.
	        public void CreateTask(MyTask task)
	        {
	            var collection = GetTasksCollectionForEdit();
	            try
	            {
	                collection.InsertOne(task);
	            }
	            catch (MongoCommandException ex)
	            {
	                string msg = ex.Message;
	            }
	        }
	
	        private IMongoCollection<MyTask> GetTasksCollection()
	        {
	            MongoClient client = new MongoClient(connectionString);
	            var database = client.GetDatabase(dbName);
	            var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
	            return todoTaskCollection;
	        }
	
	        private IMongoCollection<MyTask> GetTasksCollectionForEdit()
	        {
	            MongoClient client = new MongoClient(connectionString);
	            var database = client.GetDatabase(dbName);
	            var todoTaskCollection = database.GetCollection<MyTask>(collectionName);
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

## Hinzufügen eines Controllers ##
Öffnen Sie die Datei *Controllers\\HomeController.cs* im **Projektmappen-Explorer**, und ersetzen Sie den vorhandenen Code durch den folgenden:

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

## Einrichten der Stile ##
Um den Titel oben auf der Seite zu ändern, öffnen Sie die Datei "Views\\Shared\\_Layout.cshtml" im **Projektmappen-Explorer** und ersetzen "Application name" im Navigationsleistenheader durch "My Task List Application", sodass die Zeile folgendermaßen lautet:

 	@Html.ActionLink("My Task List Application", "Index", "Home", null, new { @class = "navbar-brand" })

Öffnen Sie zum Einrichten des Aufgabenlisten-Menüs die Datei *\\Views\\Home\\Index.cshtml*, und ersetzen Sie den vorhandenen Code durch den folgenden:
	
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


Um die Möglichkeit zum Erstellen eines neuen Tasks hinzuzufügen, klicken Sie mit der rechten Maustaste auf den Ordner *Views\\Home\*, und fügen Sie eine neue **Ansicht** hinzu. Benennen Sie die Ansicht *Create*. Ersetzen Sie den Code durch den folgenden Code:

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

![Projektmappen-Explorer][SolutionExplorerMyTaskListApp]

## Festlegen der MongoDB-Verbindungszeichenfolge ##
Öffnen Sie im **Projektmappen-Explorer** die Datei *DAL/Dal.cs*. Suchen Sie die folgende Codezeile:

	private string connectionString = "mongodb://<vm-dns-name>";

Ersetzen Sie `<vm-dns-name>` durch den DNS-Namen des virtuellen Computers, auf dem die MongoDB-Datenbank ausgeführt wird und den Sie in diesem Lernprogramm im Schritt [Erstellen eines virtuellen Computers und Installieren von MongoDB][] erstellt haben. Um den DNS-Namen des virtuellen Computers zu finden, wechseln Sie zum Azure-Portal, wählen Sie **Virtuelle Computer** aus, und suchen Sie **DNS-Name**.

Wenn der DNS-Name des virtuellen Computers "testlinuxvm.cloudapp.net" lautet und MongoDB am Standardport 27017 empfangsbereit ist, lautet die Verbindungszeichenfolge folgendermaßen:

	private string connectionString = "mongodb://testlinuxvm.cloudapp.net";

Wenn der Endpunkt des virtuellen Computers einen anderen externen Port für MongoDB angibt, können Sie den Port in der Verbindungszeichenfolge angeben:

 	private string connectionString = "mongodb://testlinuxvm.cloudapp.net:12345";

Weitere Informationen über MongoDB-Verbindungszeichenfolgen finden Sie unter [Connections][MongoConnectionStrings] (Verbindungen, in englischer Sprache).

## Testen der lokalen Bereitstellung ##

Um die Anwendung auf dem Entwicklungscomputer auszuführen, wählen Sie **Debugging starten** im Menü **Debuggen**, oder drücken Sie **F5**. IIS Express wird gestartet, und in einem Browser wird die Startseite der Anwendung aufgerufen. Sie können einen neuen Task hinzufügen, der der MongoDB-Datenbank hinzugefügt wird, die in Azure auf dem virtuellen Computer ausgeführt wird.

![Anwendung "My Task List"][TaskListAppBlank]

## Veröffentlichen in Azure App Service-Web-Apps

In diesem Abschnitt veröffentlichen Sie Ihre Änderungen in Azure App Service-Web-Apps.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste erneut auf **MyTaskListApp**, und klicken Sie auf **Veröffentlichen**.
2. Klicken Sie auf **Veröffentlichen**.

	Ihre Web-App, die im Azure App Service ausgeführt wird und auf die MongoDB-Datenbank in Azure Virtual Machines zugreift, sollte jetzt angezeigt werden.

## Zusammenfassung ##

Sie haben Ihre ASP.NET-Anwendung erfolgreich in Azure App Service-Web-Apps bereitgestellt. So zeigen Sie die Web-App an:

1. Melden Sie sich beim Azure-Portal an.
2. Klicken Sie auf **Web-Apps**. 
3. Wählen Sie Ihre Web-App in der **Web-Apps**-Liste aus.

Weitere Informationen zum Entwickeln von C#-Anwendungen für MongoDB finden Sie im [CSharp Language Center][MongoC#LangCenter].

[AZURE.INCLUDE [app-service-web-whats-changed](../../includes/app-service-web-whats-changed.md)]
 

<!-- HYPERLINKS -->

[AzurePortal]: http://manage.windowsazure.com
[WindowsAzure]: http://www.windowsazure.com
[MongoC#LangCenter]: http://docs.mongodb.org/ecosystem/drivers/csharp/
[MVCWebSite]: http://www.asp.net/mvc
[ASP.NET]: http://www.asp.net/
[MongoConnectionStrings]: http://www.mongodb.org/display/DOCS/Connections
[MongoDB]: http://www.mongodb.org
[InstallMongoOnWindowsVM]: ../virtual-machines/virtual-machines-install-mongodb-windows-server.md
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
[Create and run the My Task List ASP.NET application on your development computer]: #createapp
[Create an Azure web site]: #createwebsite
[Deploy the ASP.NET application to the web site using Git]: #deployapp
 

<!---HONumber=AcomDC_0302_2016-->