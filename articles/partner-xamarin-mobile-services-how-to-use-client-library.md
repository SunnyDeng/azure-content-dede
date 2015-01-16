<properties urlDisplayName="Xamarin" pageTitle="Verwenden des Clients der Xamarin-Komponente - Azure Mobile Services-Funktionsleitfaden" metaKeywords="Azure Mobile Services, Xamarin, iOS, Android, .NET-Client" description="Erfahren Sie, wie Sie den Client der Xamarin-Komponente für Azure Mobile Services verwenden." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="How to use the Xamarin Component client for Azure Mobile Services" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin" ms.devlang="dotnet" ms.topic="article" ms.date="11/11/2014" ms.author="donnam" />

# So verwenden Sie den Client der Xamarin-Komponente für Azure Mobile Services

<div class="dev-center-tutorial-selector sublanding"> 
  <a href="/de-de/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a>
  	<a href="/de-de/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/de-de/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/de-de/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/de-de/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin" class="current">Xamarin</a>
</div>


Dieser Artikel beschreibt gängige Szenarien für die Verwendung des Clients der Xamarin-Komponente für mobile Dienste in Azure und in Xamarin-Apps für iOS und Android. Besprochen werden unter anderem Datenabfragen, Einfügen, Aktualisieren und Löschen von Daten, Authentifizierung von Benutzern und Fehlerbehandlung. Falls Sie keine Erfahrung mit mobilen Diensten haben, sollten Sie möglicherweise zunächst die Lernprogramme "Schnellstart für mobile Dienste" ([Xamarin.iOS][Xamarin.iOS-Schnellstart-Lernprogramm]/[Xamarin.Android][Xamarin.Android-Schnellstart-Lernprogramm]) und "Erste Schritte mit Daten in .NET" ([Xamarin.iOS][Xamarin.iOS-Daten-Lernprogramm]/[Xamarin.Android][Xamarin.Android-Daten-Lernprogramm]) abschließen. Für das Schnellstart-Lernprogramm benötigen Sie [Xamarin][Xamarin-Download] und das [Mobile Services-SDK] Sie lernen dort, Ihr Konto zu konfigurieren und Ihren ersten mobilen Dienst zu erstellen.


## Inhaltsverzeichnis

- [Windows Azure Mobile Services]
- [Konzepte]
- [Gewusst wie: Erstellen des Mobile Services-Clients]
- [Gewusst wie: Erstellen eines Tabellenverweises]
- [Gewusst wie: Abfragen von Daten aus einem mobilen Dienst]
	- [Zurückgegebene Daten filtern]
    - [Zurückgegebene Daten sortieren]
	- [Daten seitenweise zurückgeben]
	- [Bestimmte Spalten auswählen]
	- [Daten nach ID abrufen]
- [Gewusst wie: Einfügen von Daten in einen mobilen Dienst]
- [Gewusst wie: Ändern von Daten in einem mobilen Dienst]
- [Gewusst wie: Löschen von Daten in einem mobilen Dienst]
- [Gewusst wie: Authentifizieren von Benutzern]
- [Gewusst wie: Fehlerbehandlung]
- [Gewusst wie: Arbeiten mit nicht typisierten Daten]
- [Gewusst wie: Design von Komponententests]
- [Nächste Schritte]
	
[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

<h2><a name="setup"></a>Einrichtung und Voraussetzungen</h2>

Wir setzen voraus, dass Sie einen mobilen Dienst und eine Tabelle erstellt haben. Weitere Informationen finden Sie unter [Erstellen einer Tabelle](http://go.microsoft.com/fwlink/?LinkId=298592). Der Code in diesem Artikel verwendet eine Tabelle mit dem Namen "TodoItem" und den folgenden Spalten: "id", "Text" und "Complete".

Der entsprechende typisierte clientseitige .NET-Typ sieht wie folgt aus:


	public class TodoItem
	{
		public string id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}
	
Wenn das dynamische Schema aktiviert ist, generiert Azure Mobile Services automatisch anhand des Objekts in der Einfüge- oder Updateanforderung neue Spalten. Weitere Informationen finden Sie unter [Dynamisches Schema](http://go.microsoft.com/fwlink/?LinkId=296271).

<h2><a name="create-client"></a>Gewusst wie: Erstellen des Mobile Services-Clients</h2>

Der folgende Code erstellt das "MobileServiceClient"-Objekt, das für den Zugriff auf Ihren mobilen Dienst verwendet wird. 
			
	MobileServiceClient client = new MobileServiceClient( 
		"AppUrl", 
		"AppKey" 
	); 

Ersetzen Sie im obigen Code "AppUrl" und "AppKey" durch die URL und den Anwendungsschlüssel des mobilen Diensts in dieser Reihenfolge. Beide finden Sie im Azure-Verwaltungsportal, indem Sie Ihren mobilen Dienst auswählen und auf "Dashboard" klicken.

<h2><a name="instantiating"></a>Gewusst wie: Erstellen eines Tabellenverweises</h2>

Jeglicher Code zum Abrufen oder Ändern von Daten in der Mobile Services-Tabelle ruft Funktionen des MobileServiceTable-Objekts auf. Sie erhalten einen Verweis auf die Tabelle, indem Sie die [GetTable](http://msdn.microsoft.com/de-de/library/windowsazure/jj554275.aspx)/Funktion einer Instanz von "MobileServiceClient" aufrufen. 

    IMobileServiceTable<TodoItem> todoTable = 
		client.GetTable<TodoItem>();

	Dies ist das typisierte Serialisierungsmodell. Eine Besprechung des <a href="#untyped">untypisierten Serialisierungsmodells</a> finden Sie weiter unten.
			
<h2><a name="querying"></a>Gewusst wie: Abfragen von Daten aus einem mobilen Dienst</h2>

Dieser Abschnitt beschreibt, wie Sie Abfragen an Ihren mobilen Dienst stellen können. Die Unterabschnitte beschreiben Aspekte wie z. B. Sortierung, Filterung und Seitenverwaltung. 
			
### <a name="filtering"></a>Gewusst wie: Zurückgegebene Daten filtern

Der folgende Code zeigt, wie Sie Daten mithilfe einer "Where"-Klausel in einer Abfrage filtern. Er gibt alle Elemente von "todoTable" zurück, deren Eigenschaft "Complete" den Wert "false" aufweist. Die "Where"-Funktion wendet ein Zeilenfilterungsprädikat auf die Tabellenabfrage an. 
	

	// This query filters out completed TodoItems and 
	// items without a timestamp. 
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Sie können den URI der an den mobilen Dienst gesendeten Anforderung anzeigen, indem Sie Software zur Überprüfung von Nachrichten verwenden, wie z. B. Browser-Entwicklertools oder Fiddler. Beachten Sie in der folgenden URI, dass wir die eigentliche Abfragezeichenfolge verändern:

€		   
Diese Anfrage entspricht normalerweise in etwa der folgenden SQL-Abfrage auf der Serverseite:
			
	                   
Die an die "Where"-Methode übergebene Funktion kann beliebig viele Bedingungen enthalten. Die folgende Zeile:

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false
		   && todoItem.Text != null)
	   .ToListAsync();

Lässt sich (für die bereits gezeigte Abfrage) in etwa wie folgt übersetzen:
			
	SELECT * 
	FROM TodoItem 
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

Die obige "where"-Anweisung findet Elemente, deren "Complete"-Status gleich false ist und deren "Text" nicht null ist.

Wir hätten denselben Code auch in mehreren Zeilen schreiben können:

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

Die beiden Methoden sind äquivalent und können frei austauschbar verwendet werden.  Die erste Option -- Verketten mehrerer Prädikate in einer Abfrage -- ist kompakter und wird empfohlen.

Die "where"-Klausel unterstützt Vorgänge, die in die OData-Teilmenge für Mobile Services übersetzt werden können. Dazu gehören relationale Operatoren (==, !=, <, <=, >, >=), arithmetische Operatoren (+, -, /, *, %), Zahlengenauigkeit (Math.Floor, Math.Ceiling), Zeichenfolgenfunktionen (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), Datumseigenschaften (Year, Month, Day, Hour, Minute, Second), Zugriffseigenschaften von Objekten und Ausdrücke mit Kombinationen dieser Typen.

### <a name="sorting"></a>Gewusst wie: Zurückgegebene Daten sortieren

Der folgende Code zeigt, wie Sie Daten mithilfe einer "OrderBy"- oder "OrderByDescending"-Funktion in der Abfrage sortieren können. Er gibt Elemente aus "todoTable" zurück, die aufsteigend nach dem "Text"-Feld sortiert werden. Standardmäßig gibt der Server nur die ersten 50 Elemente zurück. 

<div class="dev-callout"><strong>Hinweis</strong> <p>Standardmäßig wird eine servergesteuerte Seitengröße verwendet, um zu verhindern, dass alle Elemente zurückgegeben werden. Damit wird verhindert, dass Standardabfragen für große Datensätze den Dienst negativ beeinflussen. </p> </div>

Sie können "Take" aufrufen, um die Anzahl der zurückgegebenen Elemente zu erhöhen, wie im nächsten Abschnitt beschrieben.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)       
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)       
 	List<TodoItem> items = await query.ToListAsync();			

### <a name="paging"></a>Gewusst wie: Daten seitenweise zurückgeben

Der folgende Code zeigt, wie Sie mithilfe der "Take"- und "Skip"-Klausel in der Abfrage Paging in den zurückgegebenen Daten implementieren.  Die folgende Abfrage liefert die ersten drei Elemente aus der Tabelle zurück. 

	// Define a filtered query that returns the top 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Take(3);                              
	List<TodoItem> items = await query.ToListAsync();

Die folgende geänderte Abfrage überspringt die ersten drei Ergebnisse und liefert die folgenden drei zurück. Dabei handelt es sich effektiv um die zweite "Seite" von Daten, die Seitengröße beträgt dabei drei Elemente.

	// Define a filtered query that skips the top 3 items and returns the next 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Skip(3)
					.Take(3);                              
	List<TodoItem> items = await query.ToListAsync();
			
Mit der [IncludeTotalCount](http://msdn.microsoft.com/de-de/library/windowsazure/jj730933.aspx)-Methode können Sie auch sicherstellen, dass die Abfrage die Gesamtanzahl für <i>alle</i> Datensätze abruft, die bei Ignorieren der angegebenen Take Paging/Limit-Klausel zurückgegeben worden wären:

	query = query.IncludeTotalCount();

Dieses vereinfachte Szenario übergibt fest codierte Werte an die Methoden Take und Skip. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren. 

### <a name="selecting"></a>Gewusst wie: Bestimmte Spalten auswählen

Sie können angeben, welche Eigenschaften in den Ergebnissen enthalten sein sollen, indem Sie der Abfrage eine "Select"-Klausel hinzufügen. Im folgenden Code wird gezeigt, wie Sie nur ein Feld auswählen oder auch mehrere Felder auswählen und formatieren können:

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();
	
	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}", todoItem.Text.PadRight(30), todoItem.Complete ? "Now complete!" : "Incomplete!"));
	List<string> items = await query.ToListAsync();
			
Alle bisher beschriebenen Funktionen sind additiv, d. h. wir können sie immer wieder aufrufen und bei jedem Aufruf einen größeren Teil der Abfrage beeinflussen. Ein weiteres Beispiel:

	MobileServiceTableQuery<TodoItem> query = todoTable
					.Where(todoItem => todoItem.Complete == false)
					.Select(todoItem => todoItem.Text)
					.Skip(3).
					.Take(3);
	List<string> items = await query.ToListAsync();
	
### <a name="lookingup"></a>Gewusst wie: Daten nach ID abrufen

Die "LookAsync"-Funktion kann verwendet werden, um Objekte mit einer bestimmten ID in der Datenbank zu suchen. 

	// This query filters out the item with the ID of 25
	TodoItem item25 = await todoTable.LookupAsync(25);

<h2><a name="inserting"></a>Gewusst wie: Einfügen von Daten in einen mobilen Dienst</h2>

<div class="dev-callout"><strong>Hinweis</strong> <p>	Wenn Sie Operationen zum Einfügen, Nachschlagen, Löschen oder Aktualisieren auf einem Typ ausführen möchten, müssen Sie das <strong>Id</strong>-Element aufrufen (Groß-/Kleinschreibung wird ignoriert). Aus diesem Grund hat die Beispielklasse <strong>TodoItem</strong> ein Element mit dem Namen <strong>Id</strong>. </p>Der beim Einfügevorgang vergebene Id-Wert darf nicht geändert werden. Im Gegensatz dazu sollte der Id-Wert immer auf einen nicht-Standardwert gesetzt und in Änderungs- und Löschanfragen angegeben werden. </div>

Der folgende Code zeigt, wie Sie neue Zeilen in eine Tabelle einfügen können. Der Parameter enthält die einzufügenden Daten als .NET-Objekt.

	await todoTable.InsertAsync(todoItem);

Nach Rückgabe des await "todoTable.InsertAsync"-Aufrufs wird die serverseitige ID des Objekts in das "todoItem"-Objekt im Client eingetragen. 

Zum Einfügen von untypisierten Daten können Sie Json.NET verwenden, wie unten gezeigt. Beachten Sie, dass auch hier beim Einfügen von Objekten keine Id angegeben wird.

	JObject jo = new JObject(); 
	jo.Add("Text", "Hello World"); 
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

Wenn Sie versuchen, ein Element einzufügen, während das "Id"-Feld bereits festgelegt ist, gibt der Dienst eine "MobileServiceInvalidOperationException" zurück. 

<h2><a name="modifying"></a>Gewusst wie: Ändern von Daten in einem mobilen Dienst</h2>

Der folgende Code zeigt, wie Sie eine existierende Instanz mit derselben Id und neuen Daten aktualisieren können. Der Parameter enthält die zu aktualisierenden Daten als .NET-Objekt.

	await todoTable.UpdateAsync(todoItem);


Zum Einfügen von untypisierten Daten können Sie Json.NET verwenden, wie hier gezeigt. Beachten Sie, dass Sie bei Änderungen eine Id angeben müssen, damit der mobile Dienst die zu aktualisierende Instanz identifizieren kann. Sie erhalten die zu übergebende ID im Ergebnis des InsertAsync-Aufrufs.

	JObject jo = new JObject(); 
	jo.Add("Id", 52);
	jo.Add("Text", "Hello World"); 
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);
			
Wenn Sie versuchen, ein Element zu aktualisieren, ohne dass das "Id"-Feld bereits festgelegt ist, hat der Dienst keine Möglichkeit, die korrekte Instanz zu identifizieren, und der Dienst gibt eine "MobileServiceInvalidOperationException" zurück. Auch wenn Sie versuchen, ein nicht typisiertes Element zu aktualisieren, ohne dass das "Id"-Feld bereits festgelegt ist, gibt der Dienst ebenfalls "MobileServiceInvalidOperationException" zurück. 
			
			
<h2><a name="deleting"></a>Gewusst wie: Löschen von Daten in einem mobilen Dienst</h2>

Der folgende Code zeigt, wie Sie existierende Instanzen löschen können. Die Instanz wird durch das "Id"-Feld in "todoItem" eindeutig identifiziert.

	await todoTable.DeleteAsync(todoItem);

Zum Löschen von untypisierten Daten können Sie Json.NET verwenden, wie hier gezeigt. Beachten Sie, dass Sie bei Löschungen eine Id angeben müssen, damit der mobile Dienst die zu löschende Instanz identifizieren kann. Eine Löschanfrage benötigt nur die Id. Sonstige Eigenschaften werden nicht an den Dienst übergeben und eventuell vorhandene Eigenschaften werden vom Dienst ignoriert. Das Ergebnis eines "DeleteAsync"-Aufrufs ist in der Regel ebenfalls "null". Sie erhalten die zu übergebende ID im Ergebnis des "InsertAsync"-Aufrufs.

	JObject jo = new JObject(); 
	jo.Add("Id", 52);
	await table.DeleteAsync(jo);
			
Wenn Sie versuchen, ein Element ohne Angabe im Feld "ID" zu löschen, hat der Dienst keine Möglichkeit, die korrekte Instanz zu identifizieren, und der Dienst gibt eine Ausnahme vom Typ MobileServiceInvalidOperationException zurück. Auch wenn Sie versuchen, ein nicht typisiertes Element zu löschen, ohne dass das "Id"-Feld bereits festgelegt ist, gibt der Dienst "MobileServiceInvalidOperationException" zurück. 
		


<h2><a name="authentication"></a>Gewusst wie: Authentifizieren von Benutzern</h2>

Mobile Services unterstützt Authentifizierung und Autorisierung von Anwendungsbenutzern mit einer Vielzahl externer Identitätsanbieter: Facebook, Google, Microsoft Account, Twitter und Azure Active Directory. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln in Serverskripts zu implementieren. Weitere Informationen finden Sie im Lernprogramm "Erste Schritte mit der Authentifizierung" ([Xamarin.iOS][Xamarin.iOS-Authentifizierung]/[Xamarin.Android][Xamarin.Android-Authentifizierung]).

Insgesamt werden zwei Authentifizierungsflüsse unterstützt: ein _Serverfluss_ und ein _Clientfluss_. Der Serverfluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Authentifizierungs-Webschnittstelle des Anbieters verwendet wird. Der Clientfluss ermöglicht eine tiefere Integration mit gerätespezifischen Fähigkeiten, da in diesem Fall anbieter- und gerätespezifische SDKs verwendet werden.

<h3>Serverfluss</h3>
Sie müssen Ihre App beim Identitätsanbieter registrieren, um Mobile Services die Verwaltung des Authentifizierungsprozesses in Ihrer Windows Store- oder Windows Phone-App zu ermöglichen. Anschließend müssen Sie in Ihrem mobilen Dienst die Anwendungs-ID und den geheimen Schlüssel Ihres Anbieters konfigurieren. Weitere Informationen finden Sie im Lernprogramm "Erste Schritte mit der Authentifizierung" ([Xamarin.iOS][Xamarin.iOS-Authentifizierung]/[Xamarin.Android][Xamarin.Android-Authentifizierung]).

Nachdem Sie den Identitätsanbieter registriert haben, rufen Sie einfach die [LoginAsync-Methode] mit dem [MobileServiceAuthenticationProvider]-Wert Ihres Anbieters auf. Der folgende Beispielcode startet eine Serverfluss-Anmeldung für Facebook. 

	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
				message = 
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

Wenn Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den Wert für [MobileServiceAuthenticationProvider] oben entsprechend Ihrem Anbieter.

In diesem Fall verwaltet Mobile Services den OAuth 2.0-Authentifizierungsfluss, indem die Anmeldungsseite des ausgewählten Anbieters angezeigt und nach der erfolgreichen Anmeldung beim Identitätsanbieter ein Authentifizierungstoken für den mobilen Dienst generiert wird. Die [LoginAsync]-Methode gibt einen [MobileServiceUser] zurück, der wiederum sowohl [userId] des authentifizierten Benutzers als auch das [MobileServiceAuthenticationToken] in Form eines JSON-Webtokens (JWT) enthält. Dieses Token kann zwischen gespeichert und wiederverwendet werden, bis es abläuft. Weitere Informationen finden Sie unter [Zwischenspeichern des Authentifizierungstokens].

<h3>Clientfluss</h3>

Ihre App kann den Identitätsanbieter auch unabhängig kontaktieren und Mobile Services das zurückgegebene Token zur Authentifizierung vorlegen. Mit diesem Clientfluss können Sie die einmalige Anmeldung für Ihre Benutzer implementieren oder zusätzliche Benutzerdaten vom Identitätsanbieter abrufen. 

Der folgende Codeausschnitt zeigt die einfachste Form des Clientflusses für Facebook oder Google. 

	var token = new JObject();
	// Replace access_token_value with actual value of your access token obtained 
	// using the Facebook or Google SDK.
	token.Add("access_token", "access_token_value");
			
	private MobileServiceUser user;
	private async System.Threading.Tasks.Task Authenticate()
	{
		while (user == null)
		{
			string message;
			try
			{
				// Change MobileServiceAuthenticationProvider.Facebook 
				// to MobileServiceAuthenticationProvider.Google if using Google auth.
				user = await client
					.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
				message = 
					string.Format("You are now logged in - {0}", user.UserId);
			}
			catch (InvalidOperationException)
			{
				message = "You must log in. Login Required";
			}

			var dialog = new MessageDialog(message);
			dialog.Commands.Add(new UICommand("OK"));
			await dialog.ShowAsync();
		}
	}

<h3><a name="caching"></a>Zwischenspeichern des Authentifizierungstokens</h3>
Manche Aufrufe der Anmeldemethode lassen sich vermeiden, nachdem sich der Benutzer authentifiziert hat. 	Sie können einen sicheren lokalen Speicher (z. B. [Xamarin.Auth][Xamarin.Auth component]) verwenden, um die Identität des aktuellen Benutzers bei der ersten Anmeldung zwischenzuspeichern und später bei Bedarf prüfen, ob Sie die Benutzeridentität bereits im Speicher haben. Wenn der Zwischenspeicher leer ist, muss der Benutzer den Anmeldeprozess durchlaufen. 

	using Xamarin.Auth;
	var accountStore = AccountStore.Create(); // Xamarin.iOS
	//var accountStore = AccountStore.Create(this); // Xamarin.Android

	// After logging in
	var account = new Account (user.UserId, new Dictionary<string,string> {{"token",user.MobileServiceAuthenticationToken}});
	accountStore.Save(account, "Facebook");

	// Log in 
	var accounts = accountStore.FindAccountsForService ("Facebook").ToArray();
	if (accounts.Count != 0)
	{
		user = new MobileServiceUser (accounts[0].Username);
		user.MobileServiceAuthenticationToken = accounts[0].Properties["token"];
	}
	else
	{
		// Regular login flow
		user = new MobileServiceuser( await client
			.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
		var token = new JObject();
		// Replace access_token_value with actual value of your access token
		token.Add("access_token", "access_token_value");
	}
			
	 // Log out
	client.Logout();
	accountStore.Delete(account, "Facebook");


<h2><a name="errors"></a>Gewusst wie: Fehlerbehandlung</h2>

Mobile Dienste bieten verschiedene Möglichkeiten zur Erkennung, Validierung und Behebung von Fehlern. 

Ein Beispiel sind Serverskripts, die in einem mobilen Dienst registriert sind und für eine Vielzahl von Vorgängen mit einzufügenden und zu aktualisierenden Daten verwendet werden können, einschließlich zur Überprüfung und Änderung der Daten. Sie könnten z. B. das folgende Serverskript erstellen und registrieren, das Daten validiert und ändert:

	function insert(item, user, request) 
	{
	   if (item.text.length > 10) {
		  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
	   } else {
		  request.execute();
	   }
	}

Das Serverskript validiert die Länge von Zeichenfolgen, die an den mobilen Dienst geschickt werden, und lehnt zu lange Zeichenfolgen ab (in diesem Fall länger als 10 Zeichen).

Nun ist Ihr mobiler Dienst in der Lage, Daten zu validieren und Fehlermeldungen im Server zu generieren und Sie können Ihre .NET-Anwendung erweitern, sodass diese mit den Validierungsfehlern umgehen kann.

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and Mobile Services has assigned an Id, the item is added to the CollectionView
		try
		{
			await todoTable.InsertAsync(todoItem);
			items.Add(todoItem);
		}
		catch (MobileServiceInvalidOperationException e)
		{
			// Handle error
		}
	}

<h2><a name="untyped"></a>Gewusst wie: Arbeiten mit nicht typisierten Daten</h2>

Der Client der Xamarin-Komponente wurde für stark typisierte Szenarien entwickelt. Manchmal macht jedoch eine weniger starke Typisierung Sinn, z. B. beim Umgang mit Objekten mit offenem Schema. Dieses Szenario funktioniert wie folgt. In Abfragen verwenden Sie das Übertragungsformat statt LINQ.

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");			

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Sie erhalten JSON-Werte zurück, die Sie wie einen Eigenschaftenbehälter verwenden können. Weitere Informationen zu JToken und Json.NET finden Sie unter [Json.NET](http://json.codeplex.com/)

<h2><a name="unit-testing"></a>Gewusst wie: Design von Komponententests</h2>

Der von "MobileServiceClient.GetTable" zurückgegebene Wert und die Abfragen sind Schnittstellen. Daher lassen sich diese Komponenten leicht zu Testzwecken "nachbilden". Sie könnten z. B: die Tabelle "MyMockTable : IMobileServiceTable<TodoItem>", die Ihre Testlogik implementiert.

<h2><a name="nextsteps"></a>Nächste Schritte</h2>

Sie haben das konzeptuelle Referenzthema abgeschlossen und können sich nun wichtigen Aufgaben in mobilen Diensten im Detail widmen:

* Erste Schritte mit Mobile Services ([Xamarin.iOS][Erste Schritte mit Mobile Services iOS]/[Xamarin.Android][Erste Schritte mit Mobile Services Android])
  <br/>Lernen Sie die Grundlagen für den Einsatz von Mobile Services.

* Erste Schritte mit Daten ([Xamarin.iOS][Erste Schritte mit Daten iOS]/[Xamarin.Android][Erste Schritte mit Daten Android])
  <br/>Informationen über das Speichern und Abfragen von Daten mit Mobile Services.

* Erste Schritte mit der Authentifizierung ([Xamarin.iOS][Erste Schritte mit der Authentifizierung iOS]/[Xamarin.Android][Erste Schritte mit der Authentifizierung Android])
  <br/>Informationen über die Authentifizierung von Benutzern der App mit einem Identitätsanbieter.

* Prüfen und Ändern von Daten mit Skripts ([Xamarin.iOS][Prüfen und Ändern von Daten mit Skripts iOS]/[Xamarin.Android][Prüfen und Ändern von Daten mit Skripts Android])
  <br/>Erfahren Sie mehr über die Verwendung von Serverskripts in Mobile Services, um die von Ihrer App gesendeten Daten zu prüfen und zu ändern.

* Optimieren von Abfragen mit Paging ([Xamarin.iOS][Optimieren von Abfragen mit Paging iOS]/[Xamarin.Android][Optimieren von Abfragen mit Paging Android])
  <br/>Erfahren Sie, wie Sie Paging in Abfragen zum Steuern der in einer einzelnen Anforderung verarbeiteten Datenmengen verwenden können.

* Autorisieren von Benutzern mit Skripts ([Xamarin.iOS][Autorisieren von Benutzern mit Skripts iOS]/[Xamarin.Android][Autorisieren von Benutzern mit Skripts Android])
  <br/>Verwenden Sie die vom mobilen Dienst für einen authentifizierten Benutzer gelieferte Benutzer-ID zum Filtern von zurückgegebenen Daten. 

<!-- Anchors. -->
[Windows Azure Mobile Services]: #what-is
[Konzepte]: #concepts
[Gewusst wie: Erstellen des Mobile Services-Clients]: #create-client
[Gewusst wie: Erstellen eines Tabellenverweises]: #instantiating
[Gewusst wie: Abfragen von Daten aus einem mobilen Dienst]: #querying
[Zurückgegebene Daten filtern]: #filtering
[Zurückgegebene Daten sortieren]: #sorting
[Daten seitenweise zurückgeben]: #paging
[Bestimmte Spalten auswählen]: #selecting
[Daten nach ID abrufen]: #lookingup
[Gewusst wie: Bindung von Daten an die Benutzeroberfläche in einem mobilen Dienst]: #binding
[Gewusst wie: Einfügen von Daten in einen mobilen Dienst]: #inserting
[Gewusst wie: Ändern von Daten in einem mobilen Dienst]: #modifying
[Gewusst wie: Löschen von Daten in einem mobilen Dienst]: #deleting
[Gewusst wie: Authentifizieren von Benutzern]: #authentication
[Gewusst wie: Fehlerbehandlung]: #errors
[Gewusst wie: Design von Komponententests]: #unit-testing 
[Gewusst wie: Abfragen von Daten aus einem mobilen Dienst]: #querying
[Gewusst wie: Anpassen des Clients]: #customizing
[Gewusst wie: Arbeiten mit nicht typisierten Daten]: #untyped
[Anpassen der Anforderungsheader]: #headers
[Anpassen der Serialisierung]: #serialization
[Nächste Schritte]: #nextsteps
[Zwischenspeichern des Authentifizierungstokens]: #caching

<!-- URLs. -->
[Erste Schritte mit Mobile Services iOS]: /de-de/develop/mobile/tutorials/get-started-xamarin-ios
[Erste Schritte mit Mobile Services Android]: /de-de/develop/mobile/tutorials/get-started-xamarin-android
[Xamarin-Download]: http://xamarin.com/download/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Xamarin.iOS-Schnellstart-Lernprogramm]: /de-de/develop/mobile/tutorials/get-started-xamarin-ios/
[Xamarin.Android-Schnellstart-Lernprogramm]: /de-de/develop/mobile/tutorials/get-started-xamarin-android/
[Xamarin.iOS-Daten-Lernprogramm]: /de-de/develop/mobile/tutorials/get-started-with-data-xamarin-ios/
[Xamarin.Android-Daten-Lernprogramm]: /de-de/develop/mobile/tutorials/get-started-with-data-xamarin-android/
[Xamarin.iOS-Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-xamarin-ios/
[Xamarin.Android-Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-xamarin-android/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Xamarin.Auth-Komponente]: https://components.xamarin.com/view/xamarin.auth

[Mobile Services SDK]: http://nuget.org/packages/WindowsAzure.MobileServices/
[Erste Schritte mit Daten iOS]: /de-de/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Erste Schritte mit Daten Android]: /de-de/develop/mobile/tutorials/get-started-with-data-xamarin-android
[Erste Schritte mit der Authentifizierung iOS]: /de-de/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Erste Schritte mit der Authentifizierung Android]: /de-de/develop/mobile/tutorials/get-started-with-users-xamarin-android
[Prüfen und Ändern von Daten mit Skripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
[Prüfen und Ändern von Daten mit Skripts Android]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android
[Optimieren von Abfragen mit Paging iOS]: /de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Optimieren von Abfragen mit Paging Android]: /de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-android
[Autorisieren von Benutzern mit Skripts iOS]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios
[Autorisieren von Benutzern mit Skripts Android]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android
[LoginAsync-Methode]: http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/de-de/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
