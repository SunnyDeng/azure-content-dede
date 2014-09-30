<properties title="Build a web application with ASP.NET MVC using DocumentDB" pageTitle="Build a web application with ASP.NET MVC using DocumentDB | Azure" description="Learn how to use DocumentDB to build a To Do List web application. You'll store and access data from an ASP.NET MVC web application hosted on Azure."  metaKeywords="NoSQL, DocumentDB,  database, document-orientated database, JSON, account" services="documentdb"  solutions="data-management" documentationCenter=""  authors="hawong" manager="jhubbard" editor="cgronlun" videoId="" scriptId="" />

<tags ms.service="documentdb" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="08/19/2014" ms.author="hawong"></tags>

# <a name="_Toc395809351">Erstellen einer Webanwendung mit ASP.NET MVC unter Verwendung von DocumentDB</a>

# <a name="_Toc395637758"></a> <a name="_Toc389865467"></a> <a name="_Toc389828008">Übersicht</a>

## <a name="_Toc395637759">Szenario</a>

Zur Verdeutlichung, wie Kunden Azure DocumentDB zum Speichern und
Abfragen von JSON-Dokumenten einsetzen, wird in diesem Dokument eine
umfassende Anleitung zum Erstellen einer ToDo Listen-Webanwendung unter Verwendung von
Azure DocumentDB bereitgestellt.

In dieser Anleitung wird die Verwendung vom durch Azure bereitgestellten DocumentDB-Dienst
 zum Speichern und Zugreifen auf Daten von einer auf Azure gehosteten ASP.NET MVC-Webanwendung
 dargestellt, wobei davon ausgegangen wird, dass Sie bereits Erfahrung mit Verwendung von
ASP.NET MVC und Azure Websites haben.

Sie erhalten Informationen zu folgenden Themen:

1. Erstellen und Bereitstellen eines DocumentDB-Kontos

2. Erstellen einer ASP.NET MVC-Anwendung

3. Verbinden mit und Verwenden von Azure DocumentDB von einer Webanwendung

4. Bereitstellen der Webanwendung auf Azure Websites

In dieser Anleitung erstellen Sie eine einfache webbasierte
Anwendung zur Aufgabenverwaltung, mit der Sie Aufgaben erstellen, abrufen
 und vervollständigen können. Die Aufgaben werden als JSON-Dokumente in Azure
DocumentDB gespeichert.

![Alternativtext][]

# <a name="_Toc395637760">Voraussetzungen</a>

Bevor Sie diesen Artikel durcharbeiten, sollten Sie sicherstellen, dass
Folgendes installiert ist:

Git für Windows <http://www.git-scm.com/downloads>

Visual Studio 2013 (oder die kostenlose Version [Visual Studio Express][])

Azure SDK für .NET Version 2.3 oder höher, verfügbar über
[Microsoft-Webplattform-Installer][]

Alle Screenshots in diesem Dokument wurden unter Verwendung von
Visual Studio 2013 mit Update 3 und Azure SDK für .NET Version
2.4 aufgenommen. Wenn Ihr System mit anderen Versionen konfiguriert ist,
weichen Ihre Bildschirme und Optionen möglicherweise ab. Wenn Sie jedoch
die oben aufgeführten Voraussetzungen erfüllen, sollte diese Lösung funktionieren.

# <a name="_Toc395637761">Erstellen eines DocumentDB-Datenbankkontos</a>

Zum Bereitstellen eines DocumentDB-Datenbankkontos in Azure öffnen Sie das
Azure-Verwaltungsportal, und klicken Sie entweder auf das Azure Galerie-Symbol
auf der Startseite, oder klicken Sie auf „+“ in der unteren linken Ecke des Bildschirms.

![Alternativtext][1]

Öffnet die Azure Galerie, wo Sie aus vielen verfügbaren
Azure-Diensten wählen können. In der Galerie wählen Sie „Daten, Speicher und
Sicherung“ aus der Liste von Kategorien.

![Alternativtext][2]

Wählen Sie hier die Option für Azure DocumentDB aus

![Alternativtext][3]

Wählen Sie dann „Erstellen“ im unteren Bildschirmbereich aus

![Alternativtext][4]

Öffnet das Fenster „Neuer DocumentDB“, wo Sie Name, Region,
Skala, Ressourcengruppe und weitere Einstellungen für das neue
Konto festlegen.

![Alternativtext][5]

Nachdem Sie die Werte für Ihr Konto angegeben haben, klicken Sie auf „Erstellen“, und der Bereitstellungsvorgang beginnt mit der Erstellung Ihres Datenbankkontos.
Wenn der Bereitstellungsvorgang abgeschlossen ist, wird eine Benachrichtigung
im Benachrichtigungsbereich des Portals angezeigt, und die Kachel in Ihrem
Startbildschirm (wenn Sie ausgewählt haben, dass eine erstellt werden soll) ändert sich,
um den Abschluss des Vorgangs anzuzeigen.

![Alternativtext][6]

Wenn Sie nach Abschluss der Bereitstellung auf die DocumentDB-Kachel im
Startbildschirm klicken, wird das Hauptfenster für das
neu erstellte DocumentDB-Konto aufgerufen.

![Alternativtext][7]
![Alternativtext][8]

Mithilfe der Schaltfläche „Schlüssel“ greifen Sie auf Ihre Endpunkt-URL und den Primärschlüssel zu.
Kopieren Sie diese in die Zwischenablage und
halten sie griffbereit, da diese Werte in der Webanwendung, die als Nächstes erstellt wird, verwendet werden.

Im Folgenden wird erläutert, wie eine neue ASP.NET MVC-Anwendung
von Grund auf erstellt wird. Als Referenz können Sie die vollständige Lösung
[hier] herunterladen.

# <a name="_Toc395637762">Erstellen einer neuen ASP.NET MVC-Anwendung</a>

Klicken Sie in Visual Studio auf „Datei – Neues Projekt“ und wählen Sie die
Option zum Erstellen einer neuen ASP.NET MVC-Webanwendung.

![Alternativtext][9]

Wählen Sie einen Ort aus, an dem Sie das Projekt erstellen möchten, und klicken Sie dann auf „OK“.

![Alternativtext][10]

Wenn Sie Ihre Anwendung in Azure hosten möchten, aktivieren Sie das Kontrollkästchen „In der Cloud hosten“ unten rechts aus. Es wurde das Hosten in der Cloud und Ausführen der auf einer Azure Website gehosteten Anwendung ausgewählt. Wenn
diese Option ausgewählt wird, wird eine Azure Website vorab bereitgestellt,
was die spätere Bereitstellung der endgültigen funktionierenden Anwendung wesentlich erleichtert.

Wählen Sie „OK“ aus, und lassen Sie Visual Studio die leere
ASP.NET MVC-Gerüstvorlage erstellen. Möglicherweise werden zusätzliche Bildschirme angezeigt,
in denen Sie aufgefordert werden, sich bei Ihrem Azure-Konto anzumelden,
und einige Werte für Ihre neue Website bereitzustellen. Geben Sie all diese Azure-Werte ein, und fahren Sie fort.

Wenn Visual Studio die Bausteine der MVC-Anwendung erstellt hat,
steht Ihnen eine leere ASP.NET-Anwendung, die lokal ausgeführt werden kann, zur Verfügung.

Das lokale Ausführen wird übersprungen, da sicher alle von Ihnen die ASP.NET „Hello World“-Anwendung gesehen haben. Betrachten wir im Folgenden das Hinzufügen von DocumentDB zu diesem Projekt, und das Erstellen unserer Anwendung.

# <a name="_Toc395637763">Einrichten der ASP.NET MVC-Anwendung</a>

### 

### <a name="_Toc395637764">Hinzufügen eines Modells</a>

Beginnen wir zunächst mit dem Erstellen des **M** in MVC, des Modells. Klicken Sie im
Projektmappen-Explorer mit der rechten Maustaste auf den Ordner *Modelle*, und klicken Sie dann auf **Hinzufügen**, und dann auf
**Klasse**

![Alternativtext][11]

Geben Sie der neuen Klasse den Namen **Element**, und fügen Sie den folgenden Code
zu dieser neuen Klasse hinzu.

    public class Item
    {
        [JsonProperty(PropertyName="id")]
        public int ID { get; set; }
        [JsonProperty(PropertyName="name")]
        public string Name { get; set; }
        [JsonProperty(PropertyName = "desc")]
        public string Description { get; set; }
        [JsonProperty(PropertyName="isComplete")]
        public bool Completed { get; set; }    
    }

Alle Daten in DocumentDB werden über Kabel weitergegeben und als JSON gespeichert. Mit
dem oben genannten JsonProperty-Attribut können Sie die Art steuern,
wie Ihre Objekte durch JSON.NET serialisiert/deserialisiert werden. Mit diesem wird sichergestellt,
dass Eigenschaftsnamen die JSON-Konvention der Kleinschreibung von Eigenschaftsnamen befolgen.
Sie müssen dies nicht **tun**.

### <a name="_Toc395637765">Hinzufügen eines Controllers</a>

Erstellen wir als Nächstes das **C** in MVC, eine
Controllerklasse.
 Klicken Sie im**Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner *Controller*,
und klicken Sie dann auf **Hinzufügen**, und dann auf **Controller**.

![Alternativtext][12]

![Alternativtext][13]

Im Dialogfeld **Gerüst hinzufügen** klicken Sie auf **MVC 5 Controller - Leer**
, und dann auf **Hinzufügen.**

![Alternativtext][14]

Geben Sie dem neuen Controller den Namen **ItemController.**

Visual Studio fügt nun den ItemController hinzu,
Ihr Projektmappen-Explorer sollte nun ähnlich, wie unten dargestellt, aussehen.

![Alternativtext][15]

### <a name="_Toc395637766">Hinzufügen von Ansichten</a>

Abschließend wir das **V** in MVC erstellt, eine Ansicht (view).

#### Hinzufügen von Elementindexansicht

Erweitern Sie den Ordner ***Ansichten*** im Projektmappen-Explorer,
und navigieren Sie zum (leeren) Elementordner, der
beim Hinzufügen von *ItemController* von Visual Studio erstellt wurde. Klicken Sie mit der rechten Maustaste auf ***Element***
, und wählen Sie „Neue Ansicht hinzufügen“ aus.

![Alternativtext][16]

Im Dialogfeld „Ansicht hinzufügen“. Rufen Sie die Ansicht „***Index***“ auf, verwenden Sie die Vorlage
***Liste***, und wählen Sie das ***Element (Todo.Models)*** aus,
das bereits als Klasse erstellt wurde, und verwenden Sie schließlich ***\_Layout.cshtml***
 in der Projektmappe als Layoutseite.

![Alternativtext][17]

Wenn alle Werte festgelegt sind, klicken Sie auf „Hinzufügen“, und Visual Studio
erstellt Ihre Ansicht. Visual Studio erstellt eine Vorlagenansicht. Wenn dies
abgeschlossen ist, wird die erstellte CSHTML-Datei geöffnet. Dieses Dokument kann in
Visual Studio geschlossen werden, da wir erst darauf zurückkommen.

#### Hinzufügen einer neuen Elementansicht

Bei einer ähnlichen Methode wie oben, erstellen Sie eine neue Ansicht zum Erstellen neuer Elemente
wie unten im Beispiel dargestellt;

![Alternativtext][18]

#### Hinzufügen einer Elementbearbeitungsansicht

# <a name="_Toc395888515"></a>

Fügen Sie abschließend eine letzte Ansicht zum Bearbeiten eines Elements
auf dieselbe Art wie vorher hinzu;

![Alternativtext][19]

Wenn dies abgeschlossen ist, schließen Sie die CSHTML-Dokumente in Visual Studio,
da wir später zu diesen Ansichten zurückkehren.

</h1>
# <a name="_Toc395637767">Hinzufügen von DocumentDB zum Projekt</a>

So viel erstmal zum Verständnis von ASP.NET MVC, das für diese Lösung notwendig
ist. Widmen wir uns nun dem eigentlichen Zweck dieses Lernprogramms, dem Hinzufügen von Azure DocumentDB zu Ihrer Webanwendung.

</h1>
## <a name="_Toc395637768">Installieren des DocumentDB NuGet-Pakets</a>

Das DokumentDB .NET SDK wird als NuGet-Paket gepackt und bereitgestellt.
Verwenden Sie den NuGet-Paketmanager in Visual Studio (zu diesem gelangen Sie, wenn
Sie mit der rechten Maustaste auf das Projekt klicken, und „NuGet-Pakete verwalten“ auswählen

![Alternativtext][20]

Suchen Sie online nach „Azure DocumentDB“, und installieren Sie das Paket. Das
DocumentDB-Paket, sowie alle Abhängigkeiten wie Newtonsoft.Json,
werden heruntergeladen und installiert.

Wenn Visual Studio installiert ist, sollte die Lösung die folgenden zwei
neuen Verweise beinhalten;

![Alternativtext][21]

</h1>
## <a name="_Toc395637769">Einrichten von DocumentDB</a>

### <a name="_Toc395637770">Auflisten unvollständiger Elemente</a>

Öffnen Sie den **ItemController** und entfernen Sie den gesamten Code innerhalb der Klasse
(lassen Sie die Klasse selbst jedoch stehen), der von Visual Studio hinzugefügt wurde. Diesen werden wir Schritt für Schritt mit DocumentDB neu erstellen.

Fügen Sie den folgenden Codeausschnitt in die leere ItemController-Klasse
hinzu;

    public async Task<ActionResult> Index() 
    {
        var items = await repo.GetIncompleteItems();
        return View(items);
    }

Damit wird eine „Pseudo-Repository“-Klasse für DocumentDB verwendet,
die lediglich eine unterstützende Klasse ist und den gesamten DocumentDB-spezifischen Code beinhaltet. Für
die Zwecke dieser Anleitung wird nicht die gesamte Datenzugriffsschicht
mit Abhängigkeitseinfügung unter Verwendung eines Repositorymusters implementiert
, was beim Erstellen einer realen Anwendung der Fall wäre. Für die
Zwecke dieser Anleitung wird der Einfachheit halber lediglich eine vollständige Datenzugriffslogik
 in ein Projekt implementiert.

Fügen Sie eine neue Klasse zu Ihrem Projekt hinzu und nennen Sie sie **DocumentDBRepository.**
Ersetzen Sie den Code in der Klassendatei mit dem folgenden;

    public static class DocumentDBRepository
    {
        public static async Task<List<Item>> GetIncompleteItems()
        {
            return await Task<List<Item>>.Run( () => 
                Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                    .Where(d => !d.Completed)
                    .AsEnumerable()
                    .ToList<Item>());
        }
    } 

#### 

Fügen Sie die entsprechenden Using-Anweisungen gewissenhaft hinzu, um die
Verweise aufzulösen. Alle Verweise können in Visual Studio nur dann auf einfache Art aufgelöst werden,
wenn das NuGet-Paket erfolgreich installiert ist. Der Verweis auf
*CreateDocumentQuery* wird manuell aufgelöst, nachdem Sie die folgende
Using-Anweisung hinzugefügt haben;

    using Microsoft.Azure.Documents.Linq; 

Zum Hinzufügen einer einwandfrei funktionierenden „Pseudo-Repository“-Klasse
ist noch etwas mehr manuelles Verständnis notwendig. Fügen Sie den
folgenden Code zu Ihrer Repository-Klasse hinzu;


        private static Database database;
        private static Database Database
        {
            get
            {
                if (database == null)
                {
                    ReadOrCreateDatabase().Wait();
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
                    ReadOrCreateCollection(Database.SelfLink).Wait();
                }
                
                return collection;
            }
        }

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

        private static DocumentClient client;
        private static DocumentClient Client
        {
            get
            {
                if (client == null)
                {
                    String endpoint = ConfigurationManager.GetSetting("endpoint")
                    string authKey = ConfigurationManager.GetSetting("authKey");
                    Uri endpointUri = new Uri(endpoint);
                    client = new DocumentClient(endpointUri, authKey);
                }
                
                return client;
            }
        }

        private static async Task ReadOrCreateCollection(string databaseLink)
        {
            var collections = Client.CreateDocumentCollectionQuery(databaseLink)
                              .Where(col => col.Id == CollectionId).ToArray();
            
            if (collections.Any())
            {
                collection = collections.First();
            }
            else
            {
                collection = await Client.CreateDocumentCollectionAsync(databaseLink, 
                    new DocumentCollection { Id = CollectionId });
            }
        }
     
        private static async Task ReadOrCreateDatabase()
        {
            var databases = Client.CreateDatabaseQuery()
                            .Where(db => db.Id == DatabaseId).ToArray();
            
            if (databases.Any())
            {
                database = databases.First();
            }
            else
            {
                Database database = new Database { Id = DatabaseId };   
                database = await Client.CreateDatabaseAsync(database);
            }
        }

</h1>
#### 

Wenn Sie zum ersten Mal auf eine Methode wie GetIncompleteItems() zugreifen, wird
Client aufgerufen, wodurch eine Verbindung zu Azure DocumentDB hergestellt wird. Beim ersten
Aufruf von GetIncompleteItems() werden die Datenbank und
DocumentCollection für diese Anwendung gelesen oder erstellt. Dies muss lediglich
einmal durchgeführt werden. Danach können Sie die SelfLink-Eigenschaften für
beide Ressourcen zwischenspeichern und in der Anwendung erneut verwenden.

Wenn Sie später eine Verbindung mit DocumentDB innerhalb derselben
Anwendung herstellen möchten, wird die gleiche Instanz von Client, Datenbank und Sammlung erneut verwendet,
und nicht jedes Mal eine neue Instanz erstellt.

Öffnen Sie **web.config**, und fügen Sie die folgenden Codezeilen im
<appsettings\>-Abschnitt hinzu;

    <add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    <add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    <add key="database" value="ToDoList"/>
    <add key="collection" value="Items"/>

</h4>
Jetzt sollte Ihre Lösung ohne Fehler erstellen können.

Wenn Sie die Anwendung jetzt ausführen, werden Sie zum Home-Controller und
der Indexansicht des Controllers geleitet. Das ist das Standardverhalten für das
MVC-Vorlagenprojekt, das am Anfang ausgewählt wurde, das möchten wir jedoch nicht! Zum
Ändern dieses Verhaltens muss die Weiterleitung dieser MVC-Anwendung geändert werden.

Öffnen Sie ***RouteConfig.cs*** im App\_Start-Ordner, suchen Sie nach der
mit „defaults:“ beginnenden Codezeile, und ersetzten Sie diese mit der folgenden;

    defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

Damit wird ASP.NET MVC mitgeteilt, dass Sie keinen Wert in der URL für die Steuerung des Weiterleitungsverhaltens angegeben haben, mit dem „Element“ statt „Home“ als Controller und Index als Ansicht verwendet wird.
Wenn Sie die Anwendung jetzt ausführen, wird **ItemController** aufgerufen, und die Ergebnisse der **GetIncompleteItems**-Methode werden in der Views\\Item\\Index-Ansicht zurückgegeben.
Wenn Sie das Projekt jetzt ausführen, sollte etwas in der Art angezeigt werden;

![Alternativtext][22]

### <a name="_Toc395637771">Hinzufügen von Elementen</a>

Als Nächstes werden einige Elemente zu unserer Datenbank hinzugefügt, damit
mehr als eine leere Tabelle angezeigt wird.

Wir haben bereits eine Ansicht zum Erstellen und eine Schaltfläche in der Indexansicht,
mit der der Benutzer zur Ansicht „Erstellen“ gelangt. Jetzt fügen wir dem
Controller und Repository etwas Code hinzu, mit dem der Datensatz in DocumentDB gespeichert wird.

Öffnen Sie ***ItemController.cs***, und fügen Sie den folgenden Codeausschnitt
hinzu, mit dem ASP.NET MVC mitgeteilt wird, was bei der Aktion „Erstellen“ zu tun ist, in diesem
Fall nur die erstellte Create.cshtml-Ansicht anzeigen.

    public ActionResult Create()
    { 
          return View(); 
    }

Es ist noch weiterer Code in diesem Controller notwendig, mit dem die
Übermittlung aus der Erstellansicht akzeptiert wird.

Fügen Sie als Nächstes den nächsten Codeblock hinzu, mit dem ASP.NET MVC mitgeteilt wird,
was mit einem Formular POST für diesen Controller geschehen soll.

    [HttpPost]  
    public async Task<ActionResult> Create(Item item)  
    {
        if (ModelState.IsValid)  
        {  
            await repo.CreateDocument(item);  
            return RedirectToAction("Index");  
        }   
        return View(item);   
    }

Die Elementcontroller geben nun das Element aus dem Formular an die
CreateDocument-Methode von unserer Pseudorepository-Klasse weiter,
fügen Sie daher die folgende Methode zu Ihrer DocumentDBRepository-Klasse.

    public static async Task<Document> CreateDocument(dynamic item)
    {
        return await Client.CreateDocumentAsync(Collection.SelfLink, item); 
    }

Mit dieser Methode wird jedes Objekt an diese übergeben und in
DocumentDB gespeichert.

Damit ist der für das Hinzufügen neuer Elemente zu unserer Datenbank erforderliche Code komplett.

### <a name="_Toc395637772">Bearbeiten von Elementen</a>

Es bleibt noch eine letzte Sache, nämlich die Möglichkeit,
Elemente in der Datenbank zu bearbeiten, und sie als vollständig
zu markieren, sobald die Aufgaben abgeschlossen sind. Wie beim Hinzufügen, wurde die Ansicht zum Bearbeiten bereits hinzugefügt,
sodass erneut lediglich zusätzlicher Code zu unserem
Controller und zur DocumentDBRepository-Klasse hinzugefügt werden muss.

Fügen Sie der ItemController-Klasse folgenden Code hinzu:

    public async Task<ActionResult> Edit(string id)    
    {  
        if (id == null)  
        {
            return new HttpStatusCodeResult(HttpStatusCode.BadRequest);   
        }

        Item item = await repo.GetDocument(id);
        if (item == null)
        {       
            return HttpNotFound();
        }
        
        return View(item);
    }

    [HttpPost] 
    public async Task<ActionResult> Edit(Item item)  
    {
        if (ModelState.IsValid)
        {
             await repo.UpdateDocument(item);
             return RedirectToAction("Index");
        }
        
        return View(item); 
    }

Die erste Methode berücksichtigt HTTP GET, der erfolgt, wenn der Benutzer
auf den Link „Bearbeiten“ in der Indexansicht klickt. Mit dieser Methode wird ein
Dokument aus DocumentDB abgerufen und an die Ansicht „Bearbeiten“ übergeben.

### 

Die Ansicht „Bearbeiten“ sendet dann einen HTTP Post an unseren IndexController zurück, und
mit der zweiten von uns hinzugefügten Methode
wird das aktualisierte Objekt an DocumentDB zum Speichern in der Datenbank übergeben.

Fügen Sie der DocumentDBRepository-Klasse folgenden Code hinzu;

    public static async Task<Item> GetDocument(string id)
    {
        return await Task<Item>.Run(() =>
            Client.CreateDocumentQuery<Item>(Collection.DocumentsLink)
                .Where(d => d.ID == id)
                .AsEnumerable()
                .FirstOrDefault());
    }

    public static async Task<Document> UpdateDocument(Item item)
    {
        var doc = Client.CreateDocumentQuery<Document>(Collection.DocumentsLink)
                    .Where(d => d.Id == item.ID)
                    .AsEnumerable().FirstOrDefault();
        
        return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    }

Mit der ersten dieser zwei Methoden wird ein Element aus DocumentDB abgerufen,
das an den ItemController und dann an die Ansicht „Bearbeiten“ zurückgegeben wurde.

Mit der zweiten von uns hinzugefügten Methode wird das Dokument in
DocumentDB mit der Version des vom ItemController übergebenen
Dokuments ersetzt.

Das ist alles, was zum Ausführen unserer Anwendung, zum
Auflisten unvollständiger Elemente, Hinzufügen neuer Elemente und Bearbeiten von Elementen notwendig ist.

</h3>
# <a name="_Toc395637773">Lokales Ausführen der Anwendung</a>

### 

Zum Testen der Anwendung auf Ihrem lokalen Computer, drücken Sie in Visual Studio F5
. Damit sollte die Anwendung erstellt werden, und
ein Browser mit einer uns bereits bekannten leeren Tabellenseite gestartet werden:

![Alternativtext][23]

1.Verwenden Sie zum Eingeben von Informationen die bereitgestellten Felder für Element, Elementname und Kategorie
, und klicken Sie dann auf den Link**„Neu erstellen“** und geben
einige Werte ein. Lassen Sie das Kontrollkästchen „Abgeschlossen“ deaktiviert, andernfalls hat das
neue hinzugefügte Element den Status abgeschlossen und erscheint nicht in der Anfangsliste.

![Alternativtext][24]

Wenn Sie auf „Erstellen“ klicken, werden Sie zurück zur Indexansicht
geleitet, und Ihr Element hoffentlich in der Liste angezeigt.

![Alternativtext][25]

Sie können weitere Elemente zu Ihrer „Todo“-Liste hinzufügen.

2.Wenn Sie auf „Bearbeiten“ neben einem Element in der Liste klicken, werden Sie
zur Bearbeitenansicht geleitet, wo Sie beliebige
Eigenschaften Ihres Objekts, inklusive die Markierung als „Abgeschlossen“, aktualisieren können. Damit wird das Element als
abgeschlossen gekennzeichnet und aus der Liste unvollständiger Aufgaben entfernt.

![Alternativtext][26]

3.Aktivieren Sie zum Abschließen einer Aufgabe das Kontrollkästchen, und klicken Sie dann auf
**Speichern.** Sie werden zurück zur Listenseite weitergeleitet, auf der das
Element jetzt nicht mehr in der Liste erscheint.

</h3>
# <a name="_Toc395637774">Bereitstellen einer Anwendung auf Azure Websites</a>

### 

Nachdem eine vollständige, DocumentDB korrekt entgegenwirkende, Anwendung
erstellt ist, stellen wir sie auf Azure Websites bereit.

Wenn Sie beim Erstellen eines leeren ASP.NET
MVC-Projekts „In der Cloud hosten“ ausgewählt haben, ist das Bereistellen mit Visual Studio wirklich einfach, da die meiste
Arbeit Ihnen abgenommen wird. Zum Veröffentlichen dieser Anwendung klicken Sie mit
der rechten Maustaste auf das Projekt im Projektmappen-Explorer (stellen Sie
vorher sicher, dass die Anwendung nicht lokal ausgeführt wird), und wählen Sie „Veröffentlichen“ aus.

![Alternativtext][27]

Ihren Anmeldeinformatioen entsprechend sollte alles bereits konfiguriert sein;
tatsächlich wurde die Website bereits unter der angezeigten
„Ziel-URL“ in Azure erstellt, Sie müssen lediglich noch auf **Veröffentlichen** klicken.

![Alternativtext][28]

In einigen Sekunden ist das Veröffentlichen Ihrer Webanwendung
mit Visual Studio abgeschlossen, und es wird ein Browser gestartet, in
dem Sie das in Azure funktionierende Ergebnis Ihrer Arbeit betrachten können!

</h3>
# <a name="_Toc395637775">Zusammenfassung</a>

### 

Glückwunsch! Sie haben somit Ihre erste ASP.NET MVC-Anwendung unter Verwendung von Azure DocumentDB erstellt und auf Azure Websites veröffentlicht. Der Quellcode für die vollständige Referenzanwendung kann hier heruntergeladen werden.

[hier] (<http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409>)

  [Alternativtext]: ./media/documentdb-dotnet-application/image1.png
  [Visual Studio Express]: http://www.visualstudio.com/en-us/products/visual-studio-express-vs.aspx
  [Microsoft-Webplattform-Installer]: http://www.microsoft.com/web/downloads/platform.aspx
  [1]: ./media/documentdb-dotnet-application/image2.png
  [2]: ./media/documentdb-dotnet-application/image3.png
  [3]: ./media/documentdb-dotnet-application/image4.png
  [4]: ./media/documentdb-dotnet-application/image5.png
  [5]: ./media/documentdb-dotnet-application/image6.png
  [6]: ./media/documentdb-dotnet-application/image7.png
  [7]: ./media/documentdb-dotnet-application/image8.png
  [8]: ./media/documentdb-dotnet-application/image9.png
  [9]: ./media/documentdb-dotnet-application/image10.png
  [10]: ./media/documentdb-dotnet-application/image11.png
  [11]: ./media/documentdb-dotnet-application/image12.png
  [12]: ./media/documentdb-dotnet-application/image13.png
  [13]: ./media/documentdb-dotnet-application/image14.png
  [14]: ./media/documentdb-dotnet-application/image15.png
  [15]: ./media/documentdb-dotnet-application/image16.png
  [16]: ./media/documentdb-dotnet-application/image17.png
  [17]: ./media/documentdb-dotnet-application/image18.png
  [18]: ./media/documentdb-dotnet-application/image19.png
  [19]: ./media/documentdb-dotnet-application/image20.png
  [20]: ./media/documentdb-dotnet-application/image21.png
  [21]: ./media/documentdb-dotnet-application/image22.png
  [22]: ./media/documentdb-dotnet-application/image23.png
  [23]: ./media/documentdb-dotnet-application/image24.png
  [24]: ./media/documentdb-dotnet-application/image25.png
  [25]: ./media/documentdb-dotnet-application/image26.png
  [26]: ./media/documentdb-dotnet-application/image27.png
  [27]: ./media/documentdb-dotnet-application/image28.png
  [28]: ./media/documentdb-dotnet-application/image29.png
