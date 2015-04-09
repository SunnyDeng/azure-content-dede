<properties 
	pageTitle="Erstellen einer Webanwendung mit ASP.NET MVC unter Verwendung von DocumentDB | Azure" 
	description="Erfahren Sie, wie Sie mithilfe von DocumentDB und .NET eine To-Do-List-Webanwendung erstellen. Das Speichern von und der Zugriff auf Daten erfolgt über eine ASP.NET MVC-Webanwendung, die in Azure-Websites gehostet wird." 
	services="documentdb" 
	documentationCenter=".net" 
	authors="ryancrawcour" 
	manager="jhubbard" 
	editor="cgronlun"/>


<tags 
	ms.service="documentdb" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="03/23/2015" 
	ms.author="ryancraw"/>

# <a name="_Toc395809351"></a>Erstellen einer Webanwendung mit ASP.NET MVC unter Verwendung von DocumentDB

In diesem Artikel wird mithilfe einer umfassenden exemplarischen Vorgehensweise verdeutlicht, wie Sie Azure DocumentDB effizient zum Speichern und Abfragen von JSON-Dokumenten einsetzen, indem das Erstellen einer To-Do-List-Webanwendung unter Verwendung von Azure DocumentDB erläutert wird. Die Aufgaben werden als JSON-Dokumente in Azure DocumentDB gespeichert.

![Screen shot of the todo list web application created by this tutorial](./media/documentdb-dotnet-application/image1.png)

In dieser exemplarischen Vorgehensweise wird veranschaulicht, wie Sie mit dem von Azure bereitgestellten DocumentDB-Dienst Daten aus einer in Azure gehosteten ASP.NET MVC-Webanwendung speichern und auf diese zugreifen.

> [AZURE.TIP] Dieses Lernprogramm setzt vorherige Erfahrung mit der Verwendung von ASP.NET MVC und Azure-Websites voraus. Wenn Sie noch nicht mit ASP.NET oder den [als Voraussetzung erforderlichen Tools](#_Toc395637760) vertraut sind, sollten Sie das vollständige [Todo](https://github.com/Azure/azure-documentdb-net/tree/master/tutorials/todo)-Lernprogrammprojekt von [GitHub](https://github.com/Azure/azure-documentdb-net) herunterladen und gemäß den [Anweisungen am Ende dieses Artikels](#GetProject) erstellen. Nachdem Sie das Projekt erstellt haben, können Sie den Artikel lesen, um Einblick in den Code im Kontext des Projekts zu erhalten.

## <a name="_Toc395637760"></a>Voraussetzungen

Bevor Sie diesen Artikel durcharbeiten, sollten Sie sicherstellen, dass Folgendes vorhanden ist:

- Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](../../pricing/free-trial/).
- [Visual Studio 2013](http://www.visualstudio.com/) oder höher oder [Visual Studio Express] (die kostenlose Version).
- Azure SDK für .NET Version 2.3 oder höher, verfügbar über den [Microsoft-Webplattform-Installer][].

Alle Screenshots in diesem Artikel wurden unter Verwendung von Visual Studio 2013 mit Update 3 und Azure SDK für .NET Version 2.4 aufgenommen. Wenn Ihr System mit anderen Versionen konfiguriert ist, weichen Ihre Bildschirme und Optionen möglicherweise ab. Wenn Sie jedoch die oben aufgeführten Voraussetzungen erfüllen, sollte diese Lösung funktionieren.

## <a name="_Toc395637761"></a>Schritt 1: Erstellen eines DocumentDB-Datenbankkontos

Erstellen Sie zunächst ein DocumentDB-Konto. Wenn Sie bereits über ein Konto verfügen, können Sie diesen Schritt überspringen und mit [Erstellen einer neuen ASP.NET MVC-Anwendung](#_Toc395637762) fortfahren.

[AZURE.INCLUDE [documentdb-create-dbaccount](../includes/documentdb-create-dbaccount.md)]

[AZURE.INCLUDE [documentdb-keys](../includes/documentdb-keys.md)]

<br/>
Im Folgenden wird erläutert, wie eine neue ASP.NET MVC-Anwendung von Grund auf erstellt wird. 

## <a name="_Toc395637762"></a>Schritt 2: Erstellen einer neuen ASP.NET MVC-Anwendung

Jetzt erstellen wir das neue ASP.NET-Projekt.

1. Zeigen Sie in Visual Studio im Menü **Datei** auf **Neu**, und klicken Sie dann auf **Projekt**.

   	Das Dialogfeld **Neues Projekt ** wird angezeigt.
2. Erweitern Sie im Bereich **Projekttypen** die Einträge **Vorlagen**, **Visual C#**, **Web**, und wählen Sie dann **ASP.NET-Webanwendung** aus.

  	![Screen shot of the New Project dialog box with the ASP.NET Web Application project type highlighted](./media/documentdb-dotnet-application/image10.png)

3. Geben Sie im Feld **Name** den Namen des Projekts ein. In diesem Lernprogramm wird der Name "Todo" (Aufgabe) verwendet. 
4. Klicken Sie auf **Durchsuchen**, um zu dem Ordner zu navigieren, in dem Sie das Projekt erstellen möchten, und klicken Sie dann auf **OK**.

  	Das Dialogfeld **Neues ASP.NET-Projekt ** wird angezeigt.

  	![Screen shot of the New ASP.NET Project dialog box with the MVC template highlighted and the Host in the cloud box checked](./media/documentdb-dotnet-application/image11.png)

5. Wählen Sie im Vorlagenbereich **MVC** aus.
6. Wenn Sie Ihre Anwendung in Azure hosten möchten, aktivieren Sie das Kontrollkästchen **In der Cloud hosten**. Wir haben das Hosten in der Cloud und das Ausführen der auf einer Azure-Website gehosteten Anwendung ausgewählt. Wenn diese Option ausgewählt wird, wird eine Azure-Website vorab bereitgestellt, was die spätere Bereitstellung der endgültigen funktionierenden Anwendung wesentlich erleichtert. Wenn das Projekt nicht in der Cloud gehostet oder Azure nicht im Vorfeld konfiguriert werden soll, deaktivieren Sie das Kontrollkästchen **In der Cloud hosten**.
7. Klicken Sie auf **OK**, und lassen Sie Visual Studio die leere ASP.NET MVC-Gerüstvorlage erstellen. 
8. Wenn Sie das Hosten in der Cloud ausgewählt haben, wird mindestens ein zusätzlicher Bildschirm angezeigt, in dem Sie dazu aufgefordert werden, sich bei Ihrem Azure-Konto anzumelden und einige Werte für Ihre neue Website anzugeben. Geben Sie alle zusätzlichen Werte an, und fahren Sie fort. 

  	In diesem Beispiel wurde kein "Datenbankserver" ausgewählt, da kein Azure SQL-Datenbankserver verwendet wird. Ein neues Azure DocumentDB-Konto wird zu einem späteren Zeitpunkt im Azure-Vorschauportal erstellt. 

  	![Screen shot of the Configure Microsoft Azure Website dialog box](./media/documentdb-dotnet-application/image11_1.png)

9. Wenn Visual Studio die Bausteine der MVC-Anwendung erstellt hat, steht Ihnen eine leere ASP.NET-Anwendung zur Verfügung, die lokal ausgeführt werden kann.

Die lokale Ausführung des Projekts wird übersprungen, da sicher alle von Ihnen die ASP.NET-Anwendung "Hello World" gesehen haben. Betrachten wir im Folgenden das Hinzufügen von DocumentDB zu diesem Projekt, und das Erstellen unserer Anwendung.

## <a name="_Toc395637767"></a>Schritt 3: Hinzufügen von DocumentDB zum Projekt

So viel erstmal zum Verständnis von ASP.NET MVC, das für
diese Lösung notwendig ist. Widmen wir uns nun dem eigentlichen Zweck dieses Lernprogramms, dem Hinzufügen von Azure DocumentDB zu Ihrer Webanwendung.

1. Das DocumentDB .NET SDK wird als NuGet-Paket verpackt und verteilt. Verwenden Sie zum Abrufen des NuGet-Pakets in Visual Studio den NuGet-Paket-Manager in Visual Studio, indem Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt klicken und dann auf **NuGet-Pakete verwalten** klicken.

  	Das Dialogfeld **NuGet-Pakete verwalten** wird geöffnet.

  	![Sreen shot of the right-click options for the project in Solution Explorer, with Manage NuGet Packages highlighted.](./media/documentdb-dotnet-application/image21.png)

2. Geben Sie im Feld **Online suchen** den Suchbegriff "Azure DocumentDB" ein. Installieren Sie das in den Ergebnisse aufgeführte Paket **Microsoft Azure DocumentDB-Clientbibliotheken**. Das DocumentDB-Paket, sowie alle Abhängigkeiten wie Newtonsoft.Json, werden heruntergeladen und installiert.

  	> [AZURE.NOTE] Da der Dienst noch in der Vorschau ist, ist das NuGet-Paket als "Vorabversion" markiert. Sie müssen somit die Option "Vorabversion einbeziehen" aktivieren, andernfalls wird das Paket nicht aufgeführt. 

  	Alternativ können Sie die Paketbefehlskonsole verwenden, um das Paket zu installieren, indem Sie Folgendes eingeben.

    	Install-Package Microsoft.Azure.Documents.Client -Pre

3. Wenn das Paket installiert ist, sollte die Visual Studio-Projektmappe ungefähr wie folgt aussehen und die folgenden zwei neuen Verweise enthalten: Microsoft.Azure.Documents.Client und Newtonsoft.Json.

  	![Sreen shot of the two references added to the project in Solution Explorer.](./media/documentdb-dotnet-application/image22.png)


## <a name="_Toc395637763"></a>Schritt 4: Einrichten der ASP.NET MVC-Anwendung
 
Jetzt werden der MVC-Anwendung die Modelle, Ansichten und Controller hinzugefügt:

- [Hinzufügen eines Modells](#_Toc395637764).
- [Hinzufügen eines Controllers](#_Toc395637765).
- [Hinzufügen von Ansichten](#_Toc395637766).


### <a name="_Toc395637764"></a>Hinzufügen eines Modells

Beginnen wir zunächst mit dem Erstellen des **M** in MVC, des Modells. 

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Modelle**, und klicken Sie dann auf **Hinzufügen** und anschließend auf **Klasse**.

  	Das Dialogfeld **Neues Element hinzufügen** wird angezeigt.

2. Weisen Sie der neuen Klasse den Namen **Item** (Element) zu, und fügen Sie anschließend der neuen Datei "Item.cs" den folgenden Code hinzu.

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

	Alle Daten in DocumentDB werden über das Netzwerk übertragen und als JSON gespeichert. Um die Serialisierung bzw. Deserialisierung Ihrer Objekte durch JSON.NET zu steuern, können Sie das **JsonProperty**-Attribut verwenden, wie in der soeben erstellten Klasse **Item** veranschaulicht. Dies ist **nicht** notwendig, jedoch nützlich, wenn sichergestellt werden soll, dass die Eigenschaften die JSON-camelCase-Benennungskonventionen befolgen. 

	Sie können nicht nur das Format des Eigenschaftennamens bei der Weitergabe an JSON steuern, sondern die .NET-Eigenschaften sogar vollständig umbenennen, wie in diesem Beispiel mithilfe der **Description**-Eigenschaft geschehen. 

	Bei Bedarf können Sie hier auch **JsonConverter**-Objekte verwenden, um die Serialisierung vollständig zu steuern.  

3. Damit das hier verwendete **JsonProperty**-Attribut von Visual Studio aufgelöst wird, müssen Sie die folgende using-Anweisung zum using-Abschnitt Ihrer Klassendatei hinzufügen.

    	using Newtonsoft.Json;

### <a name="_Toc395637765"></a>Hinzufügen eines Controllers

Das **M** ist damit abgedeckt, nun wird das **C** in MVC erstellt, eine Controllerklasse.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Ordner **Controller**, und klicken Sie dann auf **Hinzufügen** und **Controller**.

    Das Dialogfeld **Gerüst hinzufügen** wird angezeigt.

2. Wählen Sie **MVC 5-Controller - Leer** aus, und klicken Sie dann auf **Hinzufügen**.

	![Screen shot of the Add Scaffold dialog box with the MVC 5 Controller - Empty option highlighted](./media/documentdb-dotnet-application/image14.png)

3. Geben Sie dem neuen Controller den Namen **ItemController**.

	![Screen shot of the Add Controller dialog box](./media/documentdb-dotnet-application/image15.png)

Nachdem die Datei erstellt wurde, sollte die Visual Studio-Projektmappe ungefähr wie folgt aussehen, und die neue Datei "ItemController.cs" sollte im **Projektmappen-Explorer** angezeigt werden.

![Screen shot of Solution Explorer with the new ItemController.cs file and Item.cs file highlighted](./media/documentdb-dotnet-application/image16.png)

### <a name="_Toc395637766"></a>Hinzufügen von Ansichten

Abschließend wird das **V** in MVC erstellt, die Ansichten (Views):

- [Hinzufügen einer Elementindexansicht](#AddItemIndexView).
- [Hinzufügen einer neuen Elementansicht](#AddNewIndexView).
- [Hinzufügen einer Elementbearbeitungsansicht](#_Toc395888515).


#### <a name="AddItemIndexView"></a>Hinzufügen einer Elementindexansicht

1. Erweitern Sie im **Projektmappen-Explorer** den Ordner ***Ansichten***, klicken Sie mit der rechten Maustaste auf den leeren Ordner **Item**, der zuvor beim Hinzufügen des **ItemController** von Visual Studio erstellt wurde, klicken Sie auf **Hinzufügen** und dann auf **Ansicht**.

![Screen shot of Solution Explorer showing the Item folder with the Add View commands highlighted](./media/documentdb-dotnet-application/image17.png)

2. Gehen Sie im Dialogfeld **Ansicht hinzufügen** folgendermaßen vor:
	- Geben Sie im Feld **Ansichtsname** den Namen ***Index*** ein.
	- Wählen Sie im Feld **Vorlage** die Option ***Liste*** aus.
	- Wählen Sie im Feld **Modellklasse** die Option ***Item (todo.Models)*** aus.
	- Geben Sie im Feld für die Layoutseite ***~/Views/Shared/_Layout.cshtml*** ein.
	- Klicken Sie auf **Hinzufügen**.
	
![Screen shot showing the Add View dialog box](./media/documentdb-dotnet-application/image18.png)

3. Wenn alle Werte festgelegt sind, klicken Sie auf **Hinzufügen**, damit Visual Studio die Ansicht erstellt. Visual Studio erstellt eine Vorlagenansicht. Wenn dies abgeschlossen ist, wird die erstellte CSHTML-Datei geöffnet. Diese Datei kann in Visual Studio geschlossen werden, da wir erst später darauf zurückkommen.

#### <a name="AddNewIndexView"></a>Hinzufügen einer neuen Elementansicht

Ähnlich wie eine Ansicht vom Typ **Elementindex** wird nun eine neue Ansicht zum Erstellen neuer **Elemente** erstellt.

Gehen Sie im Dialogfeld **Ansicht hinzufügen** folgendermaßen vor:

- Geben Sie im Feld **Ansichtsname** den Namen ***Create*** ein.
- Wählen Sie im Feld **Vorlage** die Option ***Erstellen*** aus.
- Wählen Sie im Feld **Modellklasse** die Option ***Item (todo.Models)*** aus.
- Geben Sie im Feld für die Layoutseite ***~/Views/Shared/_Layout.cshtml*** ein.
- Klicken Sie auf **Hinzufügen**.

![Screen shot showing the Add View dialog box](./media/documentdb-dotnet-application/image19.png)

#### <a name="_Toc395888515"></a>Hinzufügen einer Elementbearbeitungsansicht

Fügen Sie abschließend mit der gleichen Methode wie zuvor eine letzte Ansicht zum Bearbeiten eines **Elements** hinzu.


Gehen Sie im Dialogfeld **Ansicht hinzufügen** folgendermaßen vor:

- Geben Sie im Feld **Ansichtsname** den Namen ***Edit*** ein.
- Wählen Sie im Feld **Vorlage** die Option ***Bearbeiten*** aus.
- Wählen Sie im Feld **Modellklasse** die Option ***Item (todo.Models)*** aus.
- Wählen Sie **Als Teilansicht erstellen** aus.
- Klicken Sie auf **Hinzufügen**.

![Screen shot showing the Add View dialog box](./media/documentdb-dotnet-application/image20.png)

Wenn dieser Schritt abgeschlossen ist, schließen Sie die CSHTML-Dokumente in Visual Studio, da wir erst später zu diesen Ansichten zurückkehren.

## <a name="_Toc395637769"></a>Schritt 5: Einrichten von DocumentDB

In diesem Abschnitt fügen wir Code zur Ausführung folgender Aufgaben hinzu:

- [Auflisten unvollständiger Elemente](#_Toc395637770).
- [Hinzufügen von Elementen](#_Toc395637771).
- [Bearbeiten von Elementen](#_Toc395637772).

### <a name="_Toc395637770"></a>Auflisten unvollständiger Elemente

1. Öffnen Sie den **ItemController**, und entfernen Sie den gesamten Code innerhalb der Klasse (lassen Sie die Klasse selbst jedoch stehen), der von Visual Studio hinzugefügt wurde. Diesen werden wir Schritt für Schritt mit DocumentDB neu erstellen.

2. Fügen Sie den folgenden Codeausschnitt in der jetzt leeren **ItemController**-Klasse hinzu.

    	public ActionResult Index()
    	{
			var items = DocumentDBRepository<Item>.GetItems(d => !d.Completed);
			return View(items);
    	}

	Dieser Code verwendet außerdem eine "Pseudorepository"-Klasse namens **DocumentDBRepository**, die noch erstellt werden muss. Diese dient lediglich als Hilfsklasse und enthält den gesamten für DocumentDB spezifischen Code. Für die Zwecke dieser Anleitung wird nicht die gesamte Datenzugriffsschicht mit Abhängigkeitseinfügung, und Factorys und Repositorymuster implementiert, was beim Erstellen einer realen Anwendung der Fall wäre. 
	Für die Zwecke dieser exemplarischen Vorgehensweise wird der Einfachheit halber die vollständige Datenzugriffslogik direkt in ein Projekt eingefügt und der Fokus somit auf die DocumentDB-spezifischen Aspekte gelegt.

3. Fügen Sie Ihrem Projekt eine neue Klasse hinzu, und geben Sie dieser den Namen **DocumentDBRepository**. 
4. Ersetzen Sie den Code in der **DocumentDBRepository**-Klasse durch den folgenden Code.

    	public static class DocumentDBRepository<T>
    	{
	       using Microsoft.Azure.Documents; 
	       using Microsoft.Azure.Documents.Client; 
	       using Microsoft.Azure.Documents.Linq; 
		
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

5. Lösen Sie alle Namespaces in Visual Studio auf; im Rahmen dieser Aufgabe sollten Sie der Datei "DocumentDBRepository.cs" auch die folgenden using-Direktiven hinzufügen.

		using Microsoft.Azure.Documents;
		using Microsoft.Azure.Documents.Client;
		using Microsoft.Azure.Documents.Linq;
		using System.Configuration;

 	Alle Namespaces können in Visual Studio nur dann auf einfache Art aufgelöst werden, wenn das NuGet-Paket erfolgreich installiert ist. Die Verweise auf die Methoden **ReadOrCreateDatabase** und **ReadOrCreateCollection** werden erst aufgelöst, wenn sie hinzugefügt werden, was im nächsten Schritt geschieht.
 
6. Es gibt zwei Methodenaufrufe, die hier zum Lesen oder Erstellen von DocumentDB-Datenbanken und -Dokumentsammlungen verwendet werden. Fügen Sie daher der **DocumentDBRepository**-Klasse die folgenden zwei Methoden hinzu.

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

	Mit diesem Code wird zum einen die Datenbank (eine [**DocumentCollection**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.documentcollection.aspx)) eingerichtet und zum anderen Code erstellt, um über den [**DocumentClient**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.client.documentclient.aspx) eine Verbindung mit DocumentDB herzustellen. 

7. Nun werden einige Werte aus der Konfiguration gelesen. Öffnen Sie die Datei **Web.config**, und fügen Sie die folgenden Zeilen unter dem Abschnitt `<AppSettings>` hinzu.
	
    	<add key="endpoint" value="enter you endpoint url from the Azure Management Portal"/>
    	<add key="authKey" value="enter one of the keys from the Azure Management Portal"/>
    	<add key="database" value="ToDoList"/>
    	<add key="collection" value="Items"/>
	
8. Aktualisieren Sie jetzt diese Werte mit dem Blatt "Schlüssel" des Azure-Verwaltungsportals. Verwenden Sie den Wert für **URI** aus dem Blatt "Schlüssel" als Wert für den endpoint-Schlüssel, und verwenden Sie den Wert für **PRIMÄRSCHLÜSSEL** aus dem Blatt "Schlüssel" als Wert für den authKey-Schlüssel.
	
	Jetzt fügen wir unserer Arbeit etwas Code hinzu. 	

9. Die erste Aufgabe, die mit einer To-Do-List-Anwendung ausgeführt werden soll, ist die Anzeige nicht abgeschlosssener Elemente.  Diese Aufgabe wird von der nachstehenden Methode ausführt; kopieren Sie also die Methode, und fügen Sie sie an beliebiger Stelle in der **DocumentDBRepository**-Klasse ein.

	    public static IEnumerable<T> GetItems(Expression<Func<T, bool>> predicate) 
	    {
			return Client.CreateDocumentQuery<T>(Collection.DocumentsLink) 
				.Where(predicate) 
				.AsEnumerable(); 
		} 
	    
	Jetzt sollte Ihre Lösung ohne Fehler erstellen können.

	Wenn Sie die Anwendung jetzt ausführen, werden Sie zum **HomeController** und der Ansicht **Index** dieses Controllers geleitet. Das ist das Standardverhalten für das MVC-Vorlagenprojekt, das am Anfang ausgewählt wurde, das möchten wir jedoch nicht! Zum Ändern dieses Verhaltens muss die Weiterleitung dieser MVC-Anwendung geändert werden.

11. Öffnen Sie ***App\_Start\RouteConfig.cs***, navigieren Sie zur Zeile, die mit "defaults" beginnt, und ändern Sie diese auf folgende Weise.

    	defaults: new { controller = "Item", action = "Index", id = UrlParameter.Optional }

	Damit weiß ASP.NET MVC Folgendes: Wenn Sie keinen Wert in der URL angegeben haben, um das Routingverhalten zu steuern, soll anstelle von **Home** **Item** als Controller und **Index** als Ansicht verwendet werden.
	Wenn Sie die Anwendung jetzt ausführen, ruft sie **ItemController** auf und gibt die Ergebnisse der **GetIncompleteItems**-Methode an die Ansicht **Views**\\**Item**\\**Index** zurück. 

12. Wenn Sie dieses Projekt jetzt erstellen und ausführen, sollte ein Ergebnis ähnlich dem folgenden angezeigt werden.    

	![Screen shot of the todo list web application created by this tutorial](./media/documentdb-dotnet-application/image23.png)

### <a name="_Toc395637771"></a>Hinzufügen von Elementen

Als Nächstes werden einige Elemente zur Datenbank hinzugefügt, damit mehr als eine leere Tabelle angezeigt wird.

Es sind bereits eine Ansicht **Create** und eine Schaltfläche in der Ansicht **Index** vorhanden, über die der Benutzer zur Ansicht **Erstellen** gelangt. Jetzt fügen wir dem Controller und Repository Code hinzu, mit dem der Datensatz in DocumentDB gespeichert wird.

1. Öffnen Sie die Datei "ItemController.cs", und fügen Sie den folgenden Codeausschnitt hinzu, der ASP.NET MVC mitteilt, welche Schritte für die Aktion **Create** ausgeführt werden sollen. In diesem Fall soll lediglich die zuvor erstellte zugehörige Ansicht "Create.cshtml" gerendert werden.

    	public ActionResult Create()
    	{ 
			return View(); 
   		}

	Es ist noch weiterer Code in diesem Controller notwendig, mit dem die Übermittlung aus der Ansicht **Create** akzeptiert wird.

2. Fügen Sie den nächsten Codeblock hinzu, mit dem ASP.NET MVC mitgeteilt wird, was mit einem Formular POST für diesen Controller geschehen soll.
	
    	[HttpPost]
    	[ValidateAntiForgeryToken]
   	 	public async Task<ActionResult> Create([Bind(Include = 	"Id,Name,Description,Completed")] Item item)  
  	  	{
			if (ModelState.IsValid)  
			{  
			    await DocumentDBRepository<Item>.CreateItemAsync(item);
			    return RedirectToAction("Index");  
			}   
			return View(item);   
		}

	**Sicherheitshinweis**: Das Attribut **ValidateAntiForgeryToken** wird hier verwendet, um den Schutz dieser Anwendung vor websiteübergreifenden Anforderungsfälschungen zu unterstützen. Das Hinzufügen dieses Attributs reicht jedoch nicht aus, Ihre Ansichten müssen auch mit diesem Antifälschungstoken zusammenarbeiten. Weitere Informationen zum Thema und Beispiele für eine ordnungsgemäße Implementierung finden Sie unter [Verhindern der websiteübergreifenden Anforderungsfälschung][]. Der auf [GitHub][] bereitgestellte Quellcode enthält die vollständige Implementierung.

	**Sicherheitshinweis**: Wir verwenden außerdem das **Bind**-Attribut für den Methodenparameter, um Schutz vor Overpostingangriffen bereitzustellen. Weitere Informationen finden Sie unter [Grundlegende CRUD-Vorgänge in ASP.NET MVC][].

3. Nachdem die **Create**-Methode eingefügt wurde, übergibt der **ItemController** das **Item**-Objekt aus dem Formular an die **CreateDocument**-Methode. Fügen Sie Ihrer **DocumentDBRepository**-Klasse nun die folgende Methode hinzu.

    	public static async Task<Document> CreateItemAsync(T item)
   	 	{
   	   		return await Client.CreateDocumentAsync(Collection.SelfLink, item);
   		}

	Mit dieser Methode wird ein Objekt an diese übergeben und in DocumentDB gespeichert.

Damit ist der für das Hinzufügen neuer Elemente zur Datenbank erforderliche Code komplett.


### <a name="_Toc395637772"></a>Bearbeiten von Elementen

Eine letzte Sache bleibt noch zu tun, nämlich das Hinzufügen der Möglichkeit, **Elemente** in der Datenbank zu bearbeiten und sie als abgeschlossen zu markieren. Die Bearbeitungsansicht wurde bereits zum Projekt hinzugefügt, daher muss lediglich Code zum Controller und zur **DocumentDBRepository**-Klasse hinzugefügt werden.

1. Fügen Sie der **ItemController**-Klasse den folgenden Code hinzu.

    	[HttpPost]
   		[ValidateAntiForgeryToken]
    	public async Task<ActionResult> Edit([Bind(Include = "Id,Name,Description,Completed")] Item item)
    	{
     	   if (ModelState.IsValid)
    	    {
    	        await DocumentDBRepository<Item>.UpdateItemAsync(item.Id, item);
    	        return RedirectToAction("Index");
    	    }

  	      return View(item);
 	   	}
		
		public ActionResult Edit(string id)
		{
		    if (string.IsNullOrEmpty(id))
		    {
		        return new HttpStatusCodeResult(HttpStatusCode.BadRequest);
		    }
					 
		    Item item = (Item)DocumentDBRepository<Item>.GetItem(d => d.Id == id);
		 	
		    if (item == null)
		    {
		        return HttpNotFound();
		    }
		 	
		    return View(item);
		}
		
	
	Die erste Methode verarbeitet den Http GET-Aufruf, der erfolgt, wenn der Benutzer auf den Link **Edit** in der Ansicht **Index** klickt. Mit dieser Methode wird ein [**Dokument**](http://msdn.microsoft.com/library/azure/microsoft.azure.documents.document.aspx) aus DocumentDB abgerufen und an die Ansicht **Edit** übergeben.

	Die Ansicht **Edit** führt dann einen Http POST-Aufruf für den **IndexController** durch. 
	
	Die zweite Methode, die wir hinzugefügt haben, verarbeitet die Weitergabe des aktualisierten Objekts an DocumentDB, damit es in der Datenbank gespeichert werden kann.

2. Fügen Sie der Datei "ItemController.cs" die folgende using-Direktive hinzu.

		using todo.Models;
		using System.Threading.Tasks;
		using System.Net;

3. Fügen Sie der **DocumentDBRepository**-Klasse den folgenden Code hinzu.

    	public static T GetItem(Expression<Func<T, bool>> predicate)
    	{
        	return Client.CreateDocumentQuery<T>(Collection.DocumentsLink)
                    	.Where(predicate)
                    	.AsEnumerable()
                    	.FirstOrDefault();
    	}	
		
		public static Document GetDocument(string id) 
		{ 
			return Client.CreateDocumentQuery(Collection.DocumentsLink) 
				.Where(d => d.Id == id) 
				.AsEnumerable() 
				.FirstOrDefault(); 
		} 

    	public static async Task<Document> UpdateItemAsync(string id, T item)
    	{
        	Document doc = GetDocument(id);	
        	return await Client.ReplaceDocumentAsync(doc.SelfLink, item);
    	}

	Mit der ersten dieser Methoden wird ein **Element** aus DocumentDB abgerufen, das an den **ItemController** und dann an die Ansicht **Edit** zurückgegeben wird.
	
	Mit der zweiten hinzugefügten Methode wird das **Dokument** in DocumentDB durch die Version des vom **ItemController** übergebenen **Dokument**s ersetzt.

4. Fügen Sie der Datei "DocumentDBRepository.cs" die folgende using-Direktive hinzu.

		using System.Threading.Tasks;

	Das ist alles, was zum Ausführen unserer Anwendung, zum Auflisten nicht abgeschlossener **Elemente**, zum Hinzufügen neuer **Elemente** und zum Bearbeiten von **Elemente**n notwendig ist.

## <a name="_Toc395637773"></a>Schritt 6: Lokales Ausführen der Anwendung

Um die Anwendung lokal zu testen, führen Sie die folgenden Schritte aus:

1. Drücken Sie in Visual Studio F5 , um die Anwendung im Debugmodus zu erstellen. Die Anwendung sollte erstellt, und ein Browser mit der zuvor angezeigten leeren Rasterseite sollte geöffnet werden:

	![Screen shot of the todo list web application created by this tutorial](./media/documentdb-dotnet-application/image24.png)

	Wenn an dieser Stelle Fehler auftreten, können Sie Ihren Code mit dem Todo-Lernprogramm auf [GitHub][] vergleichen.

2. Klicken Sie auf den Link **Neu erstellen**, und fügen Sie Werte in die Felder **Name** und **Beschreibung** ein. Lassen Sie das Kontrollkästchen **Abgeschlossen** deaktiviert, andernfalls wird das neue **Element** mit dem Status "Abgeschlossen" hinzugefügt und nicht in der anfänglichen Liste angezeigt.

	![Screen shot of the Create view](./media/documentdb-dotnet-application/image25.png)

3. Klicken Sie auf **Erstellen**. Sie werden zur Ansicht **Index** zurückgeleitet, und Ihr **Element** wird in der Liste angezeigt.

	![Screen shot of the Index view](./media/documentdb-dotnet-application/image26.png)

	Sie können Ihrer To-Do-Liste nach Bedarf weitere **Elemente** hinzufügen.

3. Wenn Sie auf **Bearbeiten** neben einem **Element** in der Liste klicken, werden Sie zur Ansicht **Edit** weitergeleitet, in der Sie beliebige Eigenschaften Ihres Objekts aktualisieren können, einschließlich der Markierung als **Abgeschlossen**. Wenn Sie das Flag **Abgeschlossen** markieren und auf **Speichern** klicken, wird das **Element** aus der Liste der nicht abgeschlossenen Aufgaben entfernt.

	![Screen shot of the Index view with the Completed box checked](./media/documentdb-dotnet-application/image27.png)

4. Nachdem Sie die App getestet haben, drücken Sie STRG+F5, um das Debuggen der App zu beenden. Jetzt können Sie Ihre App bereitstellen.

## <a name="_Toc395637774"></a>Schritt 7: Bereitstellen der Anwendung in Azure-Websites

Nachdem die vollständige Anwendung korrekt mit DocumentDB zusammenarbeitet, stellen wir diese Web App in Azure-Websites bereit. Wenn Sie beim Erstellen des leeren ASP.NET MVC-Projekts **In der Cloud hosten** ausgewählt haben, ist das Bereitstellen mit Visual Studio wirklich einfach, da die meiste Arbeit Ihnen abgenommen wird. 

Zum Veröffentlichen der Anwendung müssen Sie lediglich im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt klicken und **Veröffentlichen** wählen.

![Screen shot of the Publish option in Solution Explorer](./media/documentdb-dotnet-application/image28.png)

Alles sollte bereits Ihren Anmeldeinformationen entsprechend konfiguriert sein; tatsächlich wurde die Website bereits unter der angezeigten **Ziel-URL** in Azure erstellt, Sie müssen lediglich noch auf **Veröffentlichen** klicken.

![Screen shot of the Publish Web dialog box](./media/documentdb-dotnet-application/image29.png)

Dann schließt Visual Studio die Veröffentlichung Ihrer Webanwendung in wenigen Sekunden ab und startet einen Browser, in dem das Ergebnis Ihrer Arbeit in Azure ausgeführt wird!

## <a name="_Toc395637775"></a>Nächste Schritte

Glückwunsch! Sie haben soeben Ihre erste ASP.NET MVC-Anwendung unter Verwendung von Azure DocumentDB erstellt und in Azure-Websites veröffentlicht. Der Quellcode für die vollständige Anwendung, einschließlich der Detail- und Löschfunktionen, die in diesem Lernprogramm nicht enthalten waren, kann von [GitHub][] heruntergeladen oder geklont werden. Wenn Sie diese Funktionen also zu Ihrer App hinzufügen möchten, laden Sie den Code herunter, und fügen Sie ihn dieser App hinzu.

Wenn Sie Ihrer Anwendung zusätzliche Funktionen hinzufügen möchten, sehen Sie sich die APIs in der [DocumentDB .NET-Bibliothek](http://msdn.microsoft.com/library/azure/dn783362.aspx) an. Sie können auch gerne eigene Beiträge zur DocumentDB .NET-Bibliothek auf [GitHub][] schreiben. 

## <a id="GetProject"></a>Abrufen der Projektmappe von aus 

Wenn Sie Zeit sparen und einfach nur die vollständige Todo-Projektmappe erstellen möchten, ohne den Code selbst hinzuzufügen, haben Sie Glück. Die vollständige Projektmappe ist auf GitHub verfügbar, und Sie können sie mithilfe der folgenden Anweisungen in wenigen Minuten erstellen und bereitstellen.

1. Stellen Sie sicher, dass Sie die [als Voraussetzung erforderliche Software](#_Toc395637760) installiert haben, einschließlich Visual Studio und Azure SDK für .NET Version 2.3 oder höher.

2. Klonen Sie das azure-documentdb-net-Repository mit Git für Windows ([http://www.git-scm.com/](http://www.git-scm.com/)), oder laden Sie die ZIP-Datei von [GitHub](https://github.com/Azure/azure-documentdb-net/) herunter.

2. Öffnen Sie in Visual Studio die Datei "todo.sln" aus dem Verzeichnis "azure-documentdb-net/tutorials/todo".

3. Um die Verweise auf das DocumentDB .NET SDK in Visual Studio 2013 wiederherzustellen, klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Todo-Projektmappe, und klicken Sie dann auf die Option zum ****Aktivieren der NuGet-Paketwiederherstellung, um die Verweise wiederherzustellen. 

4. Rufen Sie die Werte für den **URI** und den **PRIMÄRSCHLÜSSEL** oder den **SEKUNDÄRSCHLÜSSEL** aus dem Blatt **Schlüssel** Ihres DocumentDB-Kontos im [Azure-Vorschauportal](https://portal.azure.com/) ab. 

	
	Wenn Sie nicht über ein Konto verfügen, finden Sie unter [Erstellen eines Datenbankkontos](documentdb-create-account.md) Informationen zum Einrichten eines Kontos.

	![Screen shot of the Azure Preview portal, showing a DocumentDB account, with the ACTIVE hub highlighted, the Keys button highlighted on the DocumentDB account blade, and the URI, PRIMARY KEY and SECONDARY KEY values highlighted on the Keys blade](../includes/media/documentdb-keys/keys.png)

5. Aktualisieren Sie in der Datei "Web.config" die Standardwerte für die Schlüssel **endpoint** und **authKey**.

    	<add key="endpoint" value="~enter URI for your DocumentDB Account, from Azure Management Portal~" /> 
		<add key="authKey" value="~enter either Primary or Secondary key for your DocumentDB Account, from Azure Management Portal~" /> 

	- Kopieren Sie den Wert für **URI** auf dem Blatt "Schlüssel", und fügen Sie ihn in den Wert der **endpoint**-Eigenschaft ein. 
	- Kopieren Sie den Wert für **PRIMÄRSCHLÜSSEL** oder **SEKUNDÄRSCHLÜSSEL** auf dem Blatt **Schlüssel**, und fügen Sie ihn in den Wert der **authKey**-Eigenschaft ein.
	


7. Sie können nun [Ihre Anwendung lokal ausführen](#_Toc395637773) und anschließend [auf Azure-Websites bereitstellen](#_Toc395637774).


[\*]: https://microsoft.sharepoint.com/teams/DocDB/Shared%20Documents/Documentation/Docs.LatestVersions/PicExportError
[Visual Studio Express]: http://www.visualstudio.com/products/visual-studio-express-vs.aspx
[Microsoft-Webplattform-Installer]: http://www.microsoft.com/web/downloads/platform.aspx
[GitHub]: http://go.microsoft.com/fwlink/?LinkID=509838&clcid=0x409
[Verhindern von websiteübergreifender Anforderungsfälschung]: http://go.microsoft.com/fwlink/?LinkID=517254
[Grundlegende CRUD-Vorgänge in ASP.NET MVC]: http://go.microsoft.com/fwlink/?LinkId=317598

<!--HONumber=49-->