<properties 
	pageTitle="Erstellen einer Webanwendung mit ASP.NET MVC unter Verwendung von DocumentDB | Azure" 
	description="Erfahren Sie, wie Sie mithilfe von DocumentDB eine To-Do-List-Webanwendung erstellen. Das Speichern von und der Zugriff auf Daten erfolgt von einer ASP.NET-MVC-Webanwendung, die in Azure gehostet wird." 
	services="documentdb" 
	documentationCenter="" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/12/2015" 
	ms.author="ryancraw"/>

<a name="_Toc395809351">Erstellen einer Webanwendung mit ASP.NET MVC unter Verwendung von DocumentDB</a>
=======================================================================================================
<a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Übersicht</a>
==============================================================================================

<a name="_Toc395637759">Szenario</a>
------------------------------------

Zur Verdeutlichung, wie Kunden Azure DocumentDB zum Speichern und Abfragen von JSON-Dokumenten einsetzen, wird in diesem Dokument eine umfassende Anleitung zum Erstellen einer ToDo-Listenwebanwendung unter Verwendung von Azure DocumentDB bereitgestellt.

In dieser Anleitung wird die Verwendung vom durch Azure bereitgestellten DocumentDB-Dienst zum Speichern und Zugreifen auf Daten von einer auf Azure gehosteten ASP.NET MVC-Webanwendung dargestellt, wobei davon ausgegangen wird, dass Sie bereits Erfahrung mit Verwendung von ASP.NET MVC und Azure Websites haben.

Sie erhalten Informationen zu folgenden Themen:

1\. Erstellen und Bereitstellen eines DocumentDB-Kontos

2\. Erstellen einer ASP.NET MVC-Anwendung

3\. Herstellen einer Verbindung zu Azure DocumentDB über eine Webanwendung und anschließendes Verwenden von DocumentDB

4\. Bereitstellen der Webanwendung für Azure-Websites

In dieser Anleitung erstellen Sie eine einfache webbasierte Anwendung zur Aufgabenverwaltung, mit der Sie Aufgaben erstellen, abrufen und vervollständigen können. Die Aufgaben werden als JSON-Dokumente in Azure DocumentDB gespeichert.

![Alt text](./media/documentdb-dotnet-application/image1.png)


<a name="_Toc395637760">Prerequisites</a>
================================================================

Bevor Sie diesen Artikel durcharbeiten, sollten Sie sicherstellen, dass Folgendes installiert ist:

Git für Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (oder [Visual Studio Express][], die kostenlose Version)

Azure SDK für .NET Version 2.3 oder höher, verfügbar über [Microsoft-Webplattform-Installer][]

Alle Screenshots in diesem Dokument wurden unter Verwendung von Visual Studio 2013 mit Update 3 und Azure SDK für .NET Version 2.4 aufgenommen. Wenn Ihr System mit anderen Versionen konfiguriert ist, weichen Ihre Bildschirme und Optionen möglicherweise ab. Wenn Sie jedoch die oben aufgeführten Voraussetzungen erfüllen, sollte diese Lösung funktionieren.

<a name="_Toc395637761">Erstellen eines DocumentDB-Datenbankkontos</a>
================================================================

Wenn Sie ein DocumentDB-Datenbankkonto in Azure bereitstellen möchten, öffnen Sie das Azure-Verwaltungsportal, und klicken Sie dann entweder auf der Startseite auf die Azure Galerie-Kachel oder in der unteren linken Bildschirmecke auf das Pluszeichen ("+").

![Alt text](./media/documentdb-dotnet-application/image2.png)


Dadurch wird die Azure Galerie geöffnet, in der Sie aus den zahlreichen verfügbaren Azure-Diensten auswählen können. In der Galerie wählen Sie "Daten, Speicher und Sicherung" aus der Liste von Kategorien.

![Alt text](./media/documentdb-dotnet-application/image3.png)

Hier wählen Sie dann die Option für Azure DocumentDB aus.

![Alt text](./media/documentdb-dotnet-application/image4.png)


Wählen Sie dann "Erstellen" im unteren Bildschirmbereich aus

![Alt text](./media/documentdb-dotnet-application/image5.png)

Dadurch wird das Blatt "Neuer DocumentDB" geöffnet, in dem Sie Name, Region, Skalierung, Ressourcengruppe und andere Einstellungen für Ihr neues Konto eingeben können.

![Alt text](./media/documentdb-dotnet-application/image6.png)


Sobald Sie die Werte für Ihr Konto bereitgestellt haben, klicken Sie auf "Erstellen", woraufhin der Bereitstellungsvorgang mit der Erstellung Ihres Datenbankkontos beginnt.
Nach Abschluss des Bereitstellungsvorgangs sollte eine Benachrichtigung im Bereich Benachrichtigungen des Portals angezeigt werden, und die Kachel auf Ihrem Startbildschirm (sofern Sie einen erstellt haben) wechselt die Darstellung, um die abgeschlossene Aktion anzuzeigen.

![Alt text](./media/documentdb-dotnet-application/image7.png)


Nachdem die Bereitstellung abgeschlossen ist, können Sie auf dem Startbildschirm auf die DocumentDB-Kachel klicken, um das Hauptblatt für dieses neu erstellte DocumentDB-Konto zu öffnen.

![Alt text](./media/documentdb-dotnet-application/image8.png) 
![Alt text](./media/documentdb-dotnet-application/image9.png)


Mithilfe der Schaltfläche "Schlüssel" greifen Sie auf Ihre Endpunkt-URL und den Primärschlüssel zu. Kopieren Sie diese in die Zwischenablage, um sie dort griffbereit zu halten, da diese Werte in der Webanwendung, die als nächstes erstellt wird, verwendet werden.

Im Folgenden wird erläutert, wie eine neue ASP.NET MVC-Anwendung von Grund auf erstellt wird. Als Referenz können Sie die vollständige Lösung [hier] herunterladen.

<a name="_Toc395637762">Erstellen einer neuen ASP.NET MVC-Anwendung</a>
================================================================

Klicken Sie in Visual Studio auf "Datei - Neues Projekt", und wählen Sie die Option zum Erstellen einer neuen ASP.NET MVC-Webanwendung.

![Alt text](./media/documentdb-dotnet-application/image10.png)


Wählen Sie einen Speicherort und einen Namen (in diesem Beispiel ist dies "ToDo") für das zu erstellende Projekt, und klicken Sie dann auf "OK".

![Alt text](./media/documentdb-dotnet-application/image11.png)


Wenn Sie Ihre Anwendung in Azure hosten möchten, aktivieren Sie das Kontrollkästchen "In der Cloud hosten" unten rechts aus. Es wurde das Hosten in der Cloud und Ausführen der auf einer Azure Website gehosteten Anwendung ausgewählt. Wenn diese Option ausgewählt wird, wird eine Azure Website vorab bereitgestellt, was die spätere Bereitstellung der endgültigen funktionierenden Anwendung wesentlich erleichtert. Wenn das Projekt nicht in der Cloud gehostet, oder Azure nicht im Vorfeld konfiguriert werden soll, deaktivieren Sie das Kontrollkästchen "Hosten in der Cloud".

Wählen Sie "OK" aus, und lassen Sie Visual Studio die leere ASP.NET MVC-Gerüstvorlage erstellen. Wenn Sie das Hosten in der Cloud ausgewählt haben, wird mindestens ein zusätzlicher Bildschirm angezeigt, in dem Sie dazu aufgefordert werden, sich bei Ihrem Azure-Konto anzumelden, und einige Werte für Ihre neue Website anzugeben. Geben Sie all diese Azure-Werte ein, und fahren Sie fort. 

In diesem Beispiel wurde kein "Datenbankserver" ausgewählt, da kein Azure SQL-Datenbankserver verwendet wird. Ein neues Azure DocumentDB-Konto wird zu einem späteren Zeitpunkt im Verwaltungsportal erstellt. 

![Alt text](./media/documentdb-dotnet-application/image11_1.png)

Wenn Visual Studio die Bausteine der MVC-Anwendung erstellt hat, steht Ihnen eine leere ASP.NET-Anwendung zur Verfügung, die lokal ausgeführt werden kann.

Das lokale Ausführen wird übersprungen, da sicher alle von Ihnen die ASP.NET "Hello World"-Anwendung gesehen haben. Betrachten wir im Folgenden das Hinzufügen von DocumentDB zu diesem Projekt, und das Erstellen unserer Anwendung.

</h1>
<a name="_Toc395637767">Hinzufügen von DocumentDB zum Projekt</a>
=============================================================

So viel erstmal zum Verständnis von ASP.NET MVC, das für diese Lösung notwendig ist. Widmen wir uns nun dem eigentlichen Zweck dieses Lernprogramms, dem Hinzufügen von Azure DocumentDB zu Ihrer Webanwendung.

### 

### <a name="_Toc395637764">Installieren des NuGet-Pakets</a>

Das DocumentDB .NET SDK wird als NuGet-Paket verpackt und verteilt. Wenn Sie den NuGet-Paket-Manager in Visual Studio verwenden (zu dem Sie gelangen, indem Sie mit der rechten Maustaste auf das Projekt klicken und "NuGet-Pakete verwalten" wählen),

![Alt text](./media/documentdb-dotnet-application/image21.png)

suchen Sie online nach "Azure DocumentDB", und installieren Sie das Paket. Das DocumentDB-Paket, sowie alle Abhängigkeiten wie Newtonsoft.Json, werden heruntergeladen und installiert.

**HINWEIS:** Wenn der Dienst noch in der Vorschau ist, ist das NuGet-Paket als "Vorabversion" markiert. Sie müssen somit die Option "Vorabversion einbeziehen" aktivieren, andernfalls wird das Paket nicht aufgeführt.

Alternativ können Sie die Paketbefehlskonsole verwenden, um das Paket zu installieren, indem Sie Folgendes eingeben:

    Install-Package Microsoft.Azure.Documents.Client -Pre

Wenn Visual Studio installiert ist, sollte die Lösung die folgenden zwei neuen Verweise beinhalten;

![Alt text](./media/documentdb-dotnet-application/image22.png)


<a name="_Toc395637763">Einrichten der ASP.NET MVC-Anwendung</a>
==================================================================

### 

### <a name="_Toc395637764">Hinzufügen eines Modells</a>

Beginnen wir zunächst mit dem Erstellen des **M** in MVC, des Modells. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner *Models*, und klicken Sie dann auf **Hinzufügen**, und dann auf **Klasse**.

![Alt text](./media/documentdb-dotnet-application/image12.png)

Geben Sie der neuen Klasse den Namen **Element**, und fügen Sie den folgenden Code zu dieser neuen Klasse hinzu.

    public class Item
    {
        [JsonProperty(PropertyName="id")]
        public string Id { get; set; }
		
        [JsonProperty(PropertyName="name")]
        public string Name { get; set; }
		
        [JsonProperty(PropertyName = "desc")]
        public string Description { get; set; }
		
        [JsonProperty(PropertyName="isComplete")]
        public bool Completed { get; set; }    
    }

Alle Daten in DocumentDB werden über Kabel weitergegeben und als JSON gespeichert. Zum Kontrollieren, wie Ihre Objekte von JSON.NET serialisiert bzw. deserialisiert wurden, können Sie das JsonProperty-Attribut verwenden, wie in der erstellten Elementklasse dargestellt. Dies ist **nicht** notwendig, ist jedoch nützlich, wenn sichergestellt werden soll, dass die Eigenschaften die JSON camelCase-Benennungskonventionen befolgt haben. 

Sie können nicht nur das Format der Eigenschaftennamen kontrollieren, wenn es an JSON weitergegeben wird, sondern auch die .NET-Eigenschaften umbenennen, wie in diesem Beispiel mithilfe der Description-Eigenschaft geschehen. 

Sie können auch JsonConverter-Objekte an dieser Stelle verwenden, um die Serialisierung vollständig zu kontrollieren.  

Damit das hier verwendete "JsonProperty"-Attribut von Visual Studio aufgelöst wird, müssen Sie die folgende using-Anweisung zu dem using-Abschnitt Ihrer Klassendatei hinzufügen;

    using Newtonsoft.Json;

### <a name="_Toc395637765">Hinzufügen eines Controllers</a>

Erstellen wir als Nächstes das **C** in MVC, eine Controllerklasse.
Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Controllers*, und klicken Sie dann auf **Hinzufügen**, und dann auf **Controller**.

![Alt text](./media/documentdb-dotnet-application/image13.png)

![Alt text](./media/documentdb-dotnet-application/image14.png)


Im Dialogfeld **Gerüst hinzufügen** klicken Sie auf **MVC 5 Controller - Leer**. Klicken Sie auf **Hinzufügen**.

![Alt text](./media/documentdb-dotnet-application/image15.png)

Geben Sie dem neuen Controller den Namen **ItemController**.

Visual Studio fügt nun den ItemController hinzu, Ihr Projektmappen-Explorer sollte nun ähnlich aussehen, wie unten dargestellt.

![Alt text](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766">Hinzufügen von Ansichten</a>

Abschließend wird das **V** in MVC erstellt, eine Ansicht (view).


#### Hinzufügen von Elementindexansicht

Erweitern Sie den Ordner ***Ansichten*** im Projektmappen-Explorer, und navigieren Sie zum (leeren) Elementordner, der beim Hinzufügen von  *ItemController* von Visual Studio erstellt wurde. Klicken Sie mit der rechten Maustaste auf ***Element***, und wählen Sie "Neue Ansicht hinzufügen" aus.

![Alt text](./media/documentdb-dotnet-application/image17.png)

Im Dialogfeld "Ansicht hinzufügen". Rufen Sie die Ansicht "***Index***" auf, verwenden Sie die Vorlage ***Liste***, und wählen Sie das ***Element (todo.Models)*** aus, das bereits als Klasse erstellt wurde, und verwenden Sie schließlich die Datei ***~Views/Shared/_Layout.cshtml*** in der Projektmappe als Layoutseite.

![Alt text](./media/documentdb-dotnet-application/image18.png)


Wenn alle Werte festgelegt sind, klicken Sie auf "Hinzufügen", und Visual Studio erstellt Ihre Ansicht. Visual Studio erstellt eine Vorlagenansicht. Wenn dies abgeschlossen ist, wird die erstellte CSHTML-Datei geöffnet. Dieses Dokument kann in Visual Studio geschlossen werden, da wir erst später darauf zurückkommen.

#### Hinzufügen einer neuen Elementansicht

Bei einer ähnlichen Methode wie oben, erstellen Sie eine neue Ansicht zum Erstellen neuer Elemente wie unten im Beispiel dargestellt;

![Alt text](./media/documentdb-dotnet-application/image19.png)

#### Hinzufügen einer Elementbearbeitungsansicht

<a name="_Toc395888515"></a>
============================

Fügen Sie abschließend eine letzte Ansicht zum Bearbeiten eines Elements auf dieselbe Art wie vorher hinzu;

![Alt text](./media/documentdb-dotnet-application/image20.png)


Wenn dies abgeschlossen ist, schließen Sie die CSHTML-Dokumente in Visual Studio, da wir erst später zu diesen Ansichten zurückkehren.

</h1>
<a name="_Toc395637769">Einrichten von DocumentDB</a>
------------------------------------------------

### <a name="_Toc395637770">Auflisten unvollständiger Elemente</a>

Öffnen Sie den **ItemController**, und entfernen Sie den gesamten Code innerhalb der Klasse(lassen Sie die Klasse selbst jedoch stehen), der von Visual Studio hinzugefügt wurde. Diesen werden wir Schritt für Schritt mit DocumentDB neu erstellen.

Fügen Sie den folgenden Codeausschnitt in die leere ItemController-Klasse hinzu;

    public ActionResult Index()
    {
        var items = DocumentDBRepository.GetIncompleteItems();
        return View(items);
    }

Dieser Code verwendet eine "Pseudorepository"-Klasse mit dem Namen DocumentDBRepository, die noch zu erstellen ist. Diese dient lediglich als Hilfsklasse und enthält den gesamten für DocumentDB spezifischen Code. Für die Zwecke dieser Anleitung wird nicht die gesamte Datenzugriffsschicht mit Abhängigkeitseinfügung, und Factorys und Repositorymuster implementiert, was beim Erstellen einer realen Anwendung der Fall wäre. 
Für die Zwecke dieser Anleitung wird der Einfachheit halber lediglich eine vollständige Datenzugriffslogik direkt in ein Projekt implementiert, und der Fokus somit auf die DocumentDB-spezifischen Bits gelegt.

Fügen Sie eine neue Klasse zu Ihrem Projekt hinzu, und geben Sie dieser den Namen **DocumentDBRepository**.
Ersetzen Sie den Code in der Klassendatei durch den folgenden Code;

    public static class DocumentDBRepository
    {
        private static string databaseId;
        private static String DatabaseId
        {
			get
			{
				if (string.IsNullOrEmpty(databaseId))
				{
					databaseId = ConfigurationManager.AppSettings["database"];
				}
			
				return databaseId;
			}
        }
			  
        private static string collectionId;
        private static String CollectionId
        {
			get
			{
				if (string.IsNullOrEmpty(collectionId))
				{
					collectionId = ConfigurationManager.AppSettings["collection"];
				}
			
				return collectionId;
			}
        }
				
        private static Database database;
        private static Database Database
        {
			get
			{
				if (database == null)
				{
					database = ReadOrCreateDatabase();
				}
				
				return database;
			}
        }
		
        private static DocumentCollection collection;
        private static DocumentCollection Collection
        {
			get
			{
				if (collection == null)
				{
					collection = ReadOrCreateCollection(Database.SelfLink);
				}
				
				return collection;
			}
        }
		
        private static DocumentClient client;
        private static DocumentClient Client
        {
            get
            {
                if (client == null)
                {
					string endpoint = ConfigurationManager.AppSettings["endpoint"];
					string authKey = ConfigurationManager.AppSettings["authKey"];
					Uri endpointUri = new Uri(endpoint);
					client = new DocumentClient(endpointUri, authKey);
                }
                
                return client;
            }
        }
    } 

#### 

Lösen Sie alle Namespaces in Visual Studio auf. Alle Namespaces können in Visual Studio nur dann auf einfache Art aufgelöst werden, wenn das NuGet-Paket erfolgreich installiert ist. Die Verweise auf die ReadOrCreateDatabase- und ReadOrCreateCollection-Methoden werden so lange nicht aufgelöst, bis sie hinzugefügt werden, was als Nächstes folgt. 

Es gibt zwei Methodenaufrufe, die hier zum Lesen oder Erstellen von DocumentDB-Datenbanken und Dokumentsammlungen verwendet werden.
Fügen Sie nun die folgenden zwei Methoden zur Klasse hinzu;

    private static DocumentCollection ReadOrCreateCollection(string databaseLink)
    {
        var col = Client.CreateDocumentCollectionQuery(databaseLink)
                          .Where(c => c.Id == CollectionId)
                          .AsEnumerable()
                          .FirstOrDefault();

        if (col == null)
        {
            col = Client.CreateDocumentCollectionAsync(databaseLink, new DocumentCollection { Id = CollectionId }).Result;
        }
		
        return col;
    }

    private static Database ReadOrCreateDatabase()
    {
        var db = Client.CreateDatabaseQuery()
                        .Where(d => d.Id == DatabaseId)
                        .AsEnumerable()
                        .FirstOrDefault();
		
        if (db == null)
        {
            db = Client.CreateDatabaseAsync(new Database { Id = DatabaseId }).Result;
        }
		
        return db;
    }

</h1>

#### 

Damit werden die Datenbank und eine DocumentCollection eingerichtet, und Code zum Herstellen einer Verbindung zu DocumentDB über den DocumentClient erstellt. 

Nun werden einige Werte aus der Konfiguration gelesen. Öffnen Sie die Datei **web.config**, und fügen Sie Folgendes im Abschnitt
<AppSettings\> hinzu;

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>


</h4>

Jetzt fügen wir unserer Arbeit etwas Code hinzu. 
Eine ToDo-Listenanwendung soll vor allem die nicht vollständigen Elemente anzeigen können. Das erreichen Sie mit der unten aufgeführten Methode. Kopieren Sie diese also, und fügen Sie sie an eine beliebige Stelle der Repository-Klasse ein, mit der wir arbeiten.
    
    public static List<Item> GetIncompleteItems()
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => !d.Completed)
                .AsEnumerable()
                .ToList<Item>();
    }
    
Der Verweis auf *CreateDocumentQuery* wird manuell aufgelöst, nachdem Sie die folgende using-Anweisung hinzugefügt haben.

    using Microsoft.Azure.Documents.Linq;

Jetzt sollte Ihre Lösung ohne Fehler erstellen können.

Wenn Sie die Anwendung jetzt ausführen, werden Sie zum Home-Controller und der Indexansicht des Controllers geleitet. Das ist das Standardverhalten für das MVC-Vorlagenprojekt, das am Anfang ausgewählt wurde, das möchten wir jedoch nicht! Zum Ändern dieses Verhaltens muss die Weiterleitung dieser MVC-Anwendung geändert werden.

Öffnen Sie ***App\_Start\RouteConfig.cs***, navigieren Sie zur Zeile, die mit "defaults" beginnt, und ändern Sie diese auf folgende Weise.

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Damit weiß ASP.NET MVC, dass Sie keinen Wert in der URL angegeben haben, um das Routingverhalten zu steuern, dass anstelle von "Home" "Item" als Controller und den Benutzerindex als Ansicht verwendet.
Wenn Sie die Anwendung jetzt ausführen, ruft sie **ItemController** auf, und gibt die Ergebnisse der **GetIncompleteItems**-Methode an die Ansicht Views\Item\Index zurück. 

Wenn Sie dieses Projekt jetzt erstellen und ausführen, sollte etwas angezeigt werden, das dem Folgenden ähnelt;    

![Alt text](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771">Hinzufügen von Elementen</a>

Als Nächstes werden einige Elemente zu unserer Datenbank hinzugefügt, damit mehr als eine leere Tabelle angezeigt wird.

Wir haben bereits eine Ansicht zum Erstellen und eine Schaltfläche in der Indexansicht, mit der der Benutzer zur Ansicht "Erstellen" gelangt. Jetzt fügen wir dem Controller und Repository etwas Code hinzu, mit dem der Datensatz in DocumentDB gespeichert wird.

Öffnen Sie ***ItemController.cs***, und fügen Sie den folgenden Codeausschnitt hinzu, mit dem ASP.NET MVC mitgeteilt wird, was bei der Aktion "Erstellen" zu tun ist, in diesem Fall nur die erstellte Create.cshtml-Ansicht anzeigen.

    public ActionResult Create()
    { 
	return View(); 
    }

Es ist noch weiterer Code in diesem Controller notwendig, mit dem die Übermittlung aus der Erstellansicht akzeptiert wird.

Fügen Sie als Nächstes den nächsten Codeblock hinzu, mit dem ASP.NET MVC mitgeteilt wird, was mit einem Formular POST für diesen Controller geschehen soll.
	
    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Create([Bind(Include = "Id,Name,Description,Completed")] Item item)  
    {
		if (ModelState.IsValid)  
		{  
		    await repo.CreateDocument(item);  
		    return RedirectToAction("Index");  
		}   
    	return View(item);   
    }

**Seicherheitshinweis**: Das Attribut [ValidateAntiForgeryToken] wird hier verwendet, um den Schutz dieser Anwendung vor websiteübergreifenden Anforderungsfälschungen zu unterstützen. Das Hinzufügen dieses Attributs reicht jedoch nicht aus, denn auch Ihre Ansichten müssen mit diesem Antifälschungstoken zusammenarbeiten. Weitere Informationen zum Thema und Beispiele für eine ordnungsgemäße Implementierung finden Sie unter [Verhindern der websiteübergreifenden Anforderungsfälschung][]. Im Quellcode unter dem Downloadlink am Ende des Artikels finden Sie die vollständige Implementierung.

**Sicherheitshinweis**: Wir verwenden außerdem das Attribut [Bind] im Methodenparameter, um einen Schutz vor Overposting-Angriffen bereitzustellen. Weitere Informationen finden Sie unter [Grundlegende CRUD-Vorgänge in ASP.NET MVC][]

Da dies jetzt vorhanden ist, übergibt der Elementcontroller auf sichere Weise das Element vom Formular an die CreateDocument-Methode des Repositorys der Klasse. Fügen Sie deshalb die folgende Methode zu Ihrer DocumentDBRepository-Klasse hinzu.

    public static async Task<Document> CreateItemAsync(Item item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item);
    }

Mit dieser Methode wird ein Objekt an diese übergeben und in DocumentDB gespeichert.
Damit ist der für das Hinzufügen neuer Elemente zu unserer Datenbank erforderliche Code komplett.


### <a name="_Toc395637772">Bearbeiten von Elementen</a>

Es bleibt noch eine letzte Sache, nämlich die Möglichkeit, Elemente in der Datenbank zu bearbeiten, und sie als vollständig zu markieren, sobald die Aufgaben abgeschlossen sind. Wie beim Hinzufügen wurde die Ansicht zum Bearbeiten bereits hinzugefügt, sodass erneut lediglich zusätzlicher Code zu unserem Controller und zur DocumentDBRepository-Klasse hinzugefügt werden muss.

Fügen Sie der ItemController-Klasse folgenden Code hinzu:

    [HttpPost]
    [ValidateAntiForgeryToken]
    public async Task<ActionResult> Edit([Bind(Include = "Id,Name,Description,Completed")] Item item)
    {
        if (ModelState.IsValid)
        {
            await DocumentDBRepository.UpdateItemAsync(item);
            return RedirectToAction("Index");
        }

        return View(item);
    }
	
    public ActionResult Edit(string id)
    {
        if (id == null)
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
        }

        Item item = (Item)DocumentDBRepository.GetItem(id);
        if (item == null)
        {
            return HttpNotFound();
        }

        return View(item);
    }

Die erste Methode berücksichtigt HTTP GET, der erfolgt, wenn der Benutzer auf den Link "Bearbeiten" in der Indexansicht klickt. Mit dieser Methode wird ein Dokument aus DocumentDB abgerufen und an die Ansicht "Bearbeiten" übergeben.

Die Ansicht "Bearbeiten" führt dann ein HTTP POST an den IndexController durch. 
Die zweite Methode, die wir hinzugefügt haben, verarbeitet dieses Übergeben des aktualisierten Objekts bis zu DocumentDB, damit es in der Datenbank beibehalten werden kann.

Fügen Sie der DocumentDBRepository-Klasse folgenden Code hinzu;

    public static Item GetItem(string id)
    {
        return Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => d.Id == id)
                    .AsEnumerable()
                    .FirstOrDefault();
    }
	
    public static async Task<Document> UpdateItemAsync(Item item)
    {
        Document doc = Client.CreateDocumentQuery(Collection.DocumentsLink)
                            .Where(d => d.Id == item.Id)
                            .AsEnumerable()
                            .FirstOrDefault();

        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

Mit der ersten dieser zwei Methoden wird ein Element aus DocumentDB abgerufen, das an den ItemController und dann an die Ansicht "Bearbeiten" zurückgegeben wurde.

Mit der zweiten von uns hinzugefügten Methode wird das Dokument in DocumentDB mit der Version des vom ItemController übergebenen Dokuments ersetzt.

Das ist alles, was zum Ausführen unserer Anwendung, zum Auflisten unvollständiger Elemente, Hinzufügen neuer Elemente und Bearbeiten von Elementen notwendig ist.

</h3>
<a name="_Toc395637773">Lokales Ausführen der Anwendung</a>
=========================================================

### 

Zum Testen der Anwendung auf Ihrem lokalen Computer drücken Sie in Visual Studio F5. Damit sollte die Anwendung erstellt und ein Browser mit einer uns bereits bekannten leeren Tabellenseite gestartet werden:

![Alt text](./media/documentdb-dotnet-application/image24.png)

1\.Verwenden Sie zum Eingeben von Informationen die bereitgestellten Felder für Element, Elementname und Kategorie, und klicken Sie dann auf den Link **"Neu erstellen"**, und geben einige Werte ein. Lassen Sie das Kontrollkästchen "Abgeschlossen" deaktiviert, andernfalls hat das neue hinzugefügte Element den Status abgeschlossen und erscheint nicht in der Anfangsliste.

![Alt text](./media/documentdb-dotnet-application/image25.png)

Wenn Sie auf "Erstellen" klicken, werden Sie zurück zur Indexansicht geleitet, und Ihr Element hoffentlich in der Liste angezeigt.

![Alt text](./media/documentdb-dotnet-application/image26.png)

Sie können weitere Elemente zu Ihrer "Todo"-Liste hinzufügen.

2\.Wenn Sie auf "Bearbeiten" neben einem Element in der Liste klicken, werden Sie zur Ansicht "Bearbeiten" weitergeleitet, wo Sie beliebige Eigenschaften Ihres Objekts aktualisieren können, einschließlich der Markierung als "Abgeschlossen". Damit wird das Element als abgeschlossen gekennzeichnet und aus der Liste unvollständiger Aufgaben entfernt.

![Alt text](./media/documentdb-dotnet-application/image27.png)

3\.Aktivieren Sie zum Abschließen einer Aufgabe das Kontrollkästchen, und klicken Sie dann auf **Speichern**. Sie werden zurück zur Listenseite weitergeleitet, auf der das Element jetzt nicht mehr in der Liste erscheint.

</h3>
<a name="_Toc395637774">Bereitstellen der Anwendung auf Azure-Websites</a>
================================================================

### 

Nachdem eine vollständige, DocumentDB korrekt entgegenwirkende, Anwendung erstellt ist, stellen wir sie auf Azure Websites bereit.

Wenn Sie beim Erstellen eines leeren ASP.NET MVC-Projekts "In der Cloud hosten" ausgewählt haben, ist das Bereitstellen mit Visual Studio wirklich einfach, da die meiste Arbeit Ihnen abgenommen wird. Zum Veröffentlichen dieser Anwendung klicken Sie mit der rechten Maustaste auf das Projekt im Projektmappen-Explorer (stellen Sie vorher sicher, dass die Anwendung nicht lokal ausgeführt wird), und wählen Sie "Veröffentlichen" aus.

![Alt text](./media/documentdb-dotnet-application/image28.png)

Ihren Anmeldeinformationen entsprechend sollte alles bereits konfiguriert sein; tatsächlich wurde die Website bereits unter der angezeigten "Ziel-URL" in Azure erstellt, Sie müssen lediglich noch auf **Veröffentlichen** klicken.

![Alt text](./media/documentdb-dotnet-application/image29.png)

Dann schließt Visual Studio die Veröffentlichung Ihrer Webanwendung in wenigen Sekunden ab und startet einen Browser, in dem das Ergebnis Ihrer Arbeit in Azure ausgeführt wird!

</h3>



<a name="_Toc395637775">Zusammenfassung</a>
======================================

### 

Glückwunsch! Sie haben somit Ihre erste ASP.NET MVC-Anwendung unter Verwendung von Azure DocumentDB erstellt und auf Azure Websites veröffentlicht. Der Quellcode für die vollständige Anwendung, einschließlich der Funktion "Details & Löschen", die wir hier übersprungen haben, kann [hier][] heruntergeladen werden.


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/de-de/products/visual-studio-express-vs.aspx
[Microsoft-Webplattform-Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[hier]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[Verhindern von websiteübergreifender Anforderungsfälschung]: http://go.microsoft.com/fwlink/?LinkID=517254
[Grundlegende CRUD-Vorgänge in ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598

<!--HONumber=27-->

<!--HONumber=46--> 
