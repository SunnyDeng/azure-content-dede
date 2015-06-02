<properties 
	pageTitle="Arbeiten mit der Mobile Services .NET-Clientbibliothek" 
	description="Erfahren Sie mehr über die Verwendung eines .NET-Clients für Azure Mobile Services." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/01/2015" 
	ms.author="glenga"/>

# So verwenden Sie einen .NET-Client für mobile Dienste in Azure

[AZURE.INCLUDE [mobile-services-selector-client-library](../includes/mobile-services-selector-client-library.md)]

##Übersicht

Dieser Artikel beschreibt gängige Szenarien für die Verwendung eines .NET-Clients für mobile Dienste in Azure, in Windows Store-Apps und Windows Phone-Apps. Besprochen werden unter anderem Datenabfragen, Einfügen, Aktualisieren und Löschen von Daten, Authentifizierung von Benutzern und Fehlerbehandlung. Wenn Sie keine Erfahrungen mit Mobile Services haben, sollten Sie eventuell zunächst das Lernprogramm [Mobile Services-Schnellstart]() oder [Hinzufügen von Mobile Services zu einer vorhandenen App]() absolvieren.

[AZURE.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

##<a name="setup"></a>Einrichtung und Voraussetzungen

Wir setzen voraus, dass Sie einen mobilen Dienst und eine Tabelle erstellt haben. Weitere Informationen finden Sie unter [Erstellen einer Tabelle](http://go.microsoft.com/fwlink/?LinkId=298592). Der Code in diesem Thema verwendet eine Tabelle mit dem Namen `TodoItem` und den folgenden Spalten: `Id`, `Text` und `Complete`.

Der entsprechende typisierte clientseitige .NET-Typ sieht wie folgt aus:


	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

Wenn das dynamische Schema aktiviert ist, generieren die mobilen Azure-Dienste automatisch neue Spalten auf der Grundlage des Objekts in der Einfüge- oder Updateanforderung. Weitere Informationen finden Sie unter [Dynamisches Schema](http://go.microsoft.com/fwlink/?LinkId=296271).

##<a name="create-client"></a>Gewusst wie: Erstellen des Mobile Services-Clients

Der folgende Code erstellt das `MobileServiceClient`-Objekt, das für den Zugriff auf Ihren mobilen Dienst verwendet wird.


	MobileServiceClient client = new MobileServiceClient(
		"AppUrl",
		"AppKey"
	);

Ersetzen Sie im obigen Code `AppUrl` und `AppKey` durch die URL und den Anwendungsschlüssel des mobilen Diensts in dieser Reihenfolge. Beide Werte finden Sie im Azure-Verwaltungsportal, indem Sie Ihren mobilen Dienst auswählen und auf "Dashboard" klicken.

>[AZURE.IMPORTANT]Der Anwendungsschlüssel dient dem Herausfiltern zufälliger Anforderungen an den mobilen Dienst und wird mit der Anwendung verteilt. Da dieser Schlüssel nicht verschlüsselt ist, kann er nicht als sicher angesehen werden. Um die Daten des mobilen Dienstes zu schützen, müssen Sie stattdessen Benutzer vor dem Zugriff authentifizieren. Weitere Informationen finden Sie unter [Gewusst wie: Authentifizieren von Benutzern](#authentication).

##<a name="instantiating"></a>Gewusst wie: Erstellen von Tabellenverweisen

Jeglicher Code zum Abrufen oder Ändern von Daten in der Mobile Services-Tabelle ruft Funktionen des `MobileServiceTable`-Objekts auf. Sie erhalten einen Verweis auf die Tabelle, indem Sie die [GetTable](http://msdn.microsoft.com/library/windowsazure/jj554275.aspx)-Funktion für eine Instanz von `MobileServiceClient` aufrufen.

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

Dies ist das typisierte Serialisierungsmodell. Eine Besprechung des <a href="#untyped">untypisierten Serialisierungsmodells</a> finden Sie weiter unten.

##<a name="querying"></a>Gewusst wie: Abfragen von Daten aus einem mobilen Dienst

Dieser Abschnitt beschreibt, wie Sie Abfragen an Ihren mobilen Dienst stellen können und umfasst folgende Funktionen:

- [Zurückgegebene Daten filtern]
- [Zurückgegebene Daten sortieren]
- [Daten seitenweise zurückgeben]
- [Bestimmte Spalten auswählen]
- [Daten nach ID abrufen]

>[AZURE.NOTE]Es wird die Verwendung einer servergesteuerten Seitengröße erzwungen, um zu verhindern, dass alle Zeilen zurückgegeben werden. Damit wird verhindert, dass Standardabfragen für große Datensätze den Dienst negativ beeinflussen. Verwenden Sie die `Take`-Methode, um mehr als 50 Zeilen zurückzugeben, wie unter [Daten seitenweise zurückgeben] beschrieben.

### <a name="filtering"></a>Gewusst wie: Filtern zurückgegebener Daten

Der folgende Code zeigt, wie Sie Daten mithilfe einer `Where`-Klausel in einer Abfrage filtern. Die Abfrage gibt alle Elemente aus `todoTable` zurück, deren `Complete`-Eigenschaft gleich `false` ist. Die `Where`-Funktion wendet ein Zeilenfilterungsprädikat auf die Tabellenabfrage an.

	// This query filters out completed TodoItems and
	// items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Sie können den URI der an den mobilen Dienst gesendeten Anforderung anzeigen, indem Sie Software zur Überprüfung von Nachrichten verwenden, wie z. B. Browser-Entwicklertools oder [Fiddler]. Beachten Sie in der folgenden URI, dass wir die eigentliche Abfragezeichenfolge verändern:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1
Diese Anfrage entspricht normalerweise in etwa der folgenden SQL-Abfrage auf der Serverseite:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

Die an die `Where`-Methode übergebene Funktion kann beliebig viele Bedingungen enthalten. Die folgende Zeile:

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

Die oben aufgeführte `where`-Anweisung findet Elemente, deren `Complete`-Status gleich "false" und deren `Text` nicht NULL ist.

Wir hätten denselben Code auch in mehreren Zeilen schreiben können:

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

Beide Methoden sind äquivalent und können frei austauschbar verwendet werden. Die erste Option -- Verketten mehrerer Prädikate in einer Abfrage -- ist kompakter und empfehlenswert.

Die `where`-Klausel unterstützt Vorgänge, die in die OData-Teilmenge für Mobile Services übersetzt werden können. Dazu gehören relationale Operatoren (==, !=, <, <=, >, >=), arithmetische Operatoren (+, -, /, *, %), Zahlengenauigkeit (Math.Floor, Math.Ceiling), Zeichenfolgenfunktionen (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), Datumseigenschaften (Year, Month, Day, Hour, Minute, Second), Zugriffseigenschaften von Objekten und Ausdrücke mit Kombinationen dieser Typen.*

### <a name="sorting"></a>Gewusst wie: Sortieren zurückgegebener Daten

Der folgende Code zeigt, wie Sie Daten mithilfe einer `OrderBy`- oder `OrderByDescending`-Funktion in der Abfrage sortieren können. Die Abfrage gibt Elemente aus der Tabelle `todoTable` aufsteigend sortiert nach dem `Text`-Feld zurück.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

### <a name="paging"></a>Gewusst wie: Seitenweises Zurückgeben von Daten

Standardmäßig gibt der Server nur die ersten 50 Zeilen zurück. Sie können die [Take]-Methode aufrufen, um die Anzahl der zurückgegebenen Zeilen zu erhöhen. Verwenden Sie `Take` zusammen mit der [Skip]-Methode, um eine bestimmte "Seite" des gesamten Datasets anzufordern, das von der Abfrage zurückgegeben wird. Die folgende Abfrage liefert die ersten drei Elemente aus der Tabelle zurück.

	// Define a filtered query that returns the top 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

Die folgende geänderte Abfrage überspringt die ersten drei Ergebnisse und liefert die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

	// Define a filtered query that skips the top 3 items and returns the next 3 items.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Skip(3)
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

Mit der [IncludeTotalCount]-Methode können Sie sicherstellen, dass die Abfrage die Gesamtanzahl für <i>alle</i> Datensätze abruft, die bei Ignorieren der angegebenen "Take Paging"-/"Limit"-Klausel zurückgegeben worden wären.

	query = query.IncludeTotalCount();

In diesem vereinfachten Szenario werden hartcodierte Pagingwerte an die `Take`-Methode und die `Skip`-Methode übergeben. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren.

####Überlegungen zu Paging für einen mobilen .NET-Back-End-Dienst

Um die Begrenzung auf 50 Zeilen in einem mobilen .NET-Back-End-Dienst zu überschreiben, müssen Sie das [EnableQueryAttribute](https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx) auf die öffentliche GET-Methode anwenden und das Pagingverhalten festlegen. Bei Anwendung des Attributs auf die Methode wird durch Folgendes die maximale Anzahl zurückgegebener Zeilen auf 1000 beschränkt:

    [EnableQuery(MaxTop=1000)]


### <a name="selecting"></a>Gewusst wie: Auswählen bestimmter Spalten

Sie können angeben, welche Eigenschaften in den Ergebnissen enthalten sein sollen, indem Sie der Abfrage eine `Select`-Klausel hinzufügen. Der folgende Code zeigt, wie Sie nur ein Feld auswählen oder auch mehrere Felder auswählen und formatieren können:

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

### <a name="lookingup"></a>Gewusst wie: Abrufen von Daten nach ID

Die `LookupAsync`-Funktion kann verwendet werden, um Objekte mit einer bestimmten ID in der Datenbank zu suchen.

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

##<a name="inserting"></a>Gewusst wie: Einfügen von Daten in einen mobilen Dienst

> [AZURE.NOTE]Wenn Sie Operationen zum Einfügen, Nachschlagen, Löschen oder Aktualisieren auf einem Typ ausführen möchten, müssen Sie das **Id**-Element aufrufen. Aus diesem Grund hat die Beispielklasse **TodoItem** ein Element mit dem Namen **Id**. Alle Aktualisierungs- und Löschoperationen benötigen eine gültige Id.

Der folgende Code zeigt, wie Sie neue Zeilen in eine Tabelle einfügen können. Der Parameter enthält die einzufügenden Daten als .NET-Objekt.

	await todoTable.InsertAsync(todoItem);

Falls das `todoItem`, das an den `todoTable.InsertAsync`-Aufruf übergeben wird, keinen Wert für die eindeutige benutzerdefinierte ID enthält, generiert der Server einen ID-Wert und legt diesen in dem an den Client zurückgegebenen `todoItem`-Objekt fest.

Zum Einfügen von untypisierten Daten können Sie Json.NET verwenden, wie unten gezeigt.

	JObject jo = new JObject();
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);

Das folgende Beispiel verwendet eine E-Mail-Adresse als eindeutige Id-Zeichenfolge.

	JObject jo = new JObject();
	jo.Add("id", "myemail@emaildomain.com");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.InsertAsync(jo);


###Arbeiten mit ID-Werten

Mobile Services unterstützt eindeutige benutzerdefinierte Zeichenfolgenwerte für die Spalte **id** der Tabelle. Auf diese Weise können Anwendungen benutzerdefinierte Werte wie E-Mail-Adressen oder Benutzernamen für die ID verwenden.

Zeichenfolgen-IDs bieten Ihnen die folgenden Vorteile:

+ IDs werden ohne Roundtrip zur Datenbank generiert.
+ Datensätze aus unterschiedlichen Tabellen oder Datenbanken lassen sich leichter zusammenführen.
+ ID-Werte lassen sich möglicherweise leichter in die Anwendungslogik integrieren.

Wenn der ID-Wert einer Zeichenfolge für keinen eingefügten Datensätze festgelegt ist, generiert Mobile Services einen eindeutigen Wert für die ID. Sie können die `Guid.NewGuid()`-Methode verwenden, um eigene ID-Werte entweder auf dem Client oder in einem mobilen .NET-Back-End-Dienst zu generieren. Weitere Informationen zum Generieren von GUIDs in einem mobilen JavaScript-Back-End-Dienst finden Sie unter [Gewusst wie: Generieren eindeutiger ID-Werte](mobile-services-how-to-use-server-scripts.md#generate-guids).

Alternativ können Sie auch ganzzahlige IDs für Ihre Tabellen verwenden. Um ganzzahlige IDs zu verwenden, müssen Sie bei der Tabellenerstellung für den `mobile table create`-Befehl die Option `--integerId` verwenden. Dieser Befehl wird in der Befehlszeilenschnittstelle (CLI) für Azure verwendet. Weitere Informationen zur CLI finden Sie unter [CLI to manage Mobile Services tables](virtual-machines-command-line-tools.md#Mobile_Tables) (CLI für Tabellen in mobilen Diensten, in englischer Sprache).

##<a name="modifying"></a>Gewusst wie: Ändern von Daten in einem mobilen Dienst

Der folgende Code zeigt, wie Sie eine existierende Instanz mit derselben Id und neuen Daten aktualisieren können. Der Parameter enthält die zu aktualisierenden Daten als .NET-Objekt.

	await todoTable.UpdateAsync(todoItem);


Zum Einfügen von untypisierten Daten können Sie Json.NET verwenden, wie hier gezeigt. Beachten Sie, dass Sie bei Änderungen eine Id angeben müssen, damit der mobile Dienst die zu aktualisierende Instanz identifizieren kann. Sie finden die ID im Ergebnis des `InsertAsync`-Aufrufs.

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

Wenn Sie versuchen, ein Element ohne Angabe des "ID"-Werts zu aktualisieren, hat der Dienst keine Möglichkeit, die korrekte Instanz zu identifizieren, und das Mobile Services SDK löst `ArgumentException` aus.


##<a name="deleting"></a>Gewusst wie:Löschen von Daten in einem mobilen Dienst

Der folgende Code zeigt, wie Sie existierende Instanzen löschen können. Die Instanz wird durch das in `todoItem` festgelegte "Id"-Feld identifiziert.

	await todoTable.DeleteAsync(todoItem);

Zum Löschen von untypisierten Daten können Sie Json.NET verwenden, wie hier gezeigt. Beachten Sie, dass Sie bei Löschungen eine Id angeben müssen, damit der mobile Dienst die zu löschende Instanz identifizieren kann. Eine Löschanfrage benötigt nur die Id. Sonstige Eigenschaften werden nicht an den Dienst übergeben und eventuell vorhandene Eigenschaften werden vom Dienst ignoriert. Das Ergebnis eines `DeleteAsync`-Aufrufs ist üblicherweise auch `null`. Sie erhalten die zu übergebende ID im Ergebnis des `InsertAsync`-Aufrufs.

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

Wenn Sie versuchen, ein Element zu löschen, ohne dass das "Id"-Feld bereits festgelegt ist, hat der Dienst keine Möglichkeit, die korrekte Instanz zu identifizieren, und der Dienst gibt eine `MobileServiceInvalidOperationException` zurück. Auch wenn Sie versuchen, ein nicht typisiertes Element zu löschen, ohne dass das "Id"-Feld bereits festgelegt ist, gibt der Dienst ebenfalls `MobileServiceInvalidOperationException` zurück.

##<a name="#custom-api"></a>Gewusst wie: Aufrufen einer benutzerdefinierten API

Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats. Ein vollständiges Beispiel, in dem u. a. das Erstellen einer benutzerdefinierten API im mobilen Dienst beschrieben wird, finden Sie unter [Aufrufen einer benutzerdefinierten API aus dem Client].

Sie rufen eine benutzerdefinierte API auf, indem Sie eine der [InvokeApiAsync]-Methodenüberladungen für den Client aufrufen. Mit der folgenden Codezeile wird beispielsweise eine POST-Anforderung an die **completeAll**-API im mobilen Dienst gesendet:

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

Beachten Sie, dass dies ein typisierter Methodenaufruf ist, der erfordert, dass der **MarkAllResult**-Rückgabetyp definiert ist. Typisierte und nicht typisierte Methoden werden unterstützt. Dieses Beispiel ist fast zu einfach, da es typisiert ist und keine Abfrageparameter enthält und weil keine Nutzlast gesendet und keine Anforderungsheader geändert werden. Beispiele mit mehr Praxisnähe und eine ausführlichere Erörterung von [InvokeApiAsync] finden Sie unter [Benutzerdefinierte API in Azure Mobile Services Client SDKs].

##Gewusst wie: Registrieren für Pushbenachrichtigungen

Mit dem Mobile Services-Client können Sie die App für Pushbenachrichtigungen mit Azure Notification Hubs registrieren. Beim Registrieren, erhalten Sie von der Windows-Plattform einen Kanal-URI. Diesen Wert müssen Sie zusammen mit allen übrigen Tags beim Registrieren angeben. Der folgende Code registriert für Pushbenachrichtigungen auf einer Windows Store-App, die den Windows-Benachrichtigungsdienst (Windows Notification Service, WNS) verwendet:

		private async void InitNotificationsAsync()
		{
		    // Request a push notification channel.
		    var channel =
		        await PushNotificationChannelManager
		            .CreatePushNotificationChannelForApplicationAsync();
		
		    // Register for notifications using the new channel and a tag collection.
			var tags = new List<string>{ "mytag1", "mytag2"};
		    await MobileService.GetPush().RegisterNativeAsync(channel.Uri, tags);
		}

Beachten Sie, dass in diesem Beispiel zwei Tags in der Registrierung enthalten sind. Weitere Informationen finden Sie unter [Hinzufügen von Pushbenachrichtigungen zur App](mobile-services-dotnet-backend-windows-universal-dotnet-get-started-push.md)

>[AZURE.NOTE]Wenn Sie Benachrichtigungen an bestimmte registrierte Benutzer senden möchten, ist es wichtig, vor der Registrierung eine Authentifizierung anzufordern und sicherzustellen, dass der Benutzer dazu berechtigt ist, sich mit einem bestimmten Tag zu registrieren. Sie müssen beispielsweise sicherstellen, dass ein Benutzer sich nicht mit einem Tag anmeldet, das die Benutzer-ID eines anderen Benutzers darstellt. Weitere Informationen finden Sie unter [Senden von Pushbenachrichtigungen an authentifizierte Benutzer](mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md)


##<a name="optimisticconcurrency"></a>Gewusst wie: Verwenden von optimistischer Parallelität.

In manchen Szenarien können zwei oder mehr Clients gleichzeitig versuchen, dasselbe Element zu bearbeiten. Ohne Konflikterkennung würde der letzte Schreibvorgang alle vorherigen Aktualisierungen überschreiben, selbst wenn dies nicht so gewollt wäre. Die Steuerung für optimistische Parallelität nimmt an, dass jede Transaktion Commits ausführen kann und sperrt daher keine Ressourcen. Vor dem Commit einer Transaktion prüft die Steuerung für optimistische Parallelität, ob die Daten von einer anderen Transaktion geändert wurden. Falls die Daten geändert wurden, wird für die Transaktion, die den Commit durchführen sollte, ein Rollback durchgeführt.

Mobile Services unterstützt die Steuerung für optimistische Parallelität, indem Änderungen an Elementen in der `__version`-Systemeigenschaftenspalte, die für jede durch Mobile Services erstellte Tabelle definiert wird, nachverfolgt werden. Bei jeder Aktualisierung eines Datensatzes wird die `__version`-Eigenschaft des entsprechenden Datensatzes durch Mobile Services auf einen neuen Wert festgelegt. Bei jeder Aktualisierungsanforderung wird die `__version`-Eigenschaft des in der Anforderung enthaltenen Datensatzes mit der Eigenschaft des Datensatzes auf dem Server verglichen. Falls die in der Anforderung übergebene Version nicht mit der Version auf dem Server übereinstimmt, löst die .NET-Clientbibliothek von Mobile Services `MobileServicePreconditionFailedException<T>` aus. Der in der Ausnahme enthaltene Typ ist die Serverversion des entsprechenden Eintrags. Anschließend kann die Anwendung anhand dieser Informationen entscheiden, ob die Updateanforderung erneut mit dem korrekten `__version`-Wert vom Server ausgeführt werden soll, um Commits für die Änderungen auszuführen.

Die Anwendung definiert eine Spalte in der Tabellenklasse für die `__version`-Systemeigenschaft, um optimistische Nebenläufigkeit zu unterstützen. Die folgende Definition zeigt ein Beispiel.

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

		// *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "__version")]
        public byte[] Version { set; get; }
    }


Anwendungen, die nicht typisierte Tabellen verwenden, können die optimistische Nebenläufigkeit aktivieren, indem sie das `Version`-Flag in den `SystemProperties` der Tabelle wie folgt festlegen.

	//Enable optimistic concurrency by retrieving __version
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;


Der folgende Code zeigt, wie ein erkannter Schreibkonflikt gelöst werden kann. Der korrekte `__version`-Wert muss im `UpdateAsync()`-Aufruf angegeben werden, um einen Commit für die Auflösung auszuführen.

	private async void UpdateToDoItem(TodoItem item)
	{
    	MobileServicePreconditionFailedException<TodoItem> exception = null;

	    try
    	{
	        //update at the remote table
    	    await todoTable.UpdateAsync(item);
    	}
    	catch (MobileServicePreconditionFailedException<TodoItem> writeException)
	    {
        	exception = writeException;
	    }

    	if (exception != null)
    	{
			// Conflict detected, the item has changed since the last query
        	// Resolve the conflict between the local and server item
	        await ResolveConflict(item, exception.Item);
    	}
	}


	private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
	{
    	//Ask user to choose the resoltion between versions
	    MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n",
        	                                        serverItem.Text, localItem.Text),
                                                	"CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// To resolve the conflict, update the version of the
	        // item being committed. Otherwise, you will keep
        	// catching a MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;

    	    // Updating recursively here just in case another
        	// change happened while the user was making a decision
	        UpdateToDoItem(localItem);
    	};

	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};

	    await msgDialog.ShowAsync();
	}


Ein kompletteres Beispiel für optimistische Nebenläufigkeit in mobilen Diensten finden Sie unter [Optimistic Concurrency Tutorial] (Lernprogramm für optimistische Nebenläufigkeit, in englischer Sprache).


##<a name="binding"></a>Gewusst wie: Bindung von Daten an die Benutzeroberfläche in einem mobilen Dienst

Dieser Abschnitt beschreibt das Anzeigen von Datenobjekten in GUI-Elementen. Für die Abfrage unvollständiger Elemente aus `todoTable` und deren Anzeige in einer sehr einfachen Liste können Sie den folgenden Beispielcode ausführen, um die Quelle der Liste an eine Abfrage zu binden. Durch `MobileServiceCollection` wird eine Bindungssammlung erstellt, die Mobile Services unterstützt.

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Manche Steuerelemente in der Windows-Runtime unterstützen eine Schnittstelle namens [ISupportIncrementalLoading](http://msdn.microsoft.com/library/windows/apps/Hh701916). Mit dieser Schnittstelle können Steuerelemente weitere Daten anfordern, wenn ein Benutzer blättert. `MobileServiceIncrementalLoadingCollection` bietet integrierte Unterstützung für diese Schnittstelle für Windows Store-Apps und verarbeitet die Aufrufe dieser Steuerelemente automatisch. Gehen Sie wie folgt vor, um `MobileServiceIncrementalLoadingCollection` in Windows Store-Apps zu verwenden:

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


Um die neue Sammlung in Windows Phone zu nutzen, verwenden Sie die `ToCollection`-Erweiterungsmethoden für `IMobileServiceTableQuery<T>` und `IMobileServiceTable<T>`. Rufen Sie `LoadMoreItemsAsync()` auf, um tatsächlich Daten zu laden.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

Die Sammlung, die durch den Aufruf von `ToCollectionAsync` bzw. `ToCollection` erstellt wurde, kann an UI-Steuerelemente gebunden werden. Diese Sammlung unterstützt Seitenverwaltung, d. h. ein Steuerelement kann die Sammlung anweisen, "weitere Elemente zu laden", und die Sammlung erledigt dies für das Steuerelement. Bis zu diesem Punkt wird noch kein Benutzercode ausgeführt, das Steuerelement startet den Fluss. Da die Sammlung jedoch Daten aus dem Netzwerk lädt, ist zu erwarten, dass dieser Ladevorgang manchmal fehlschlägt. Zur Behandlung solcher Fehler können Sie die `OnException`-Methode für `MobileServiceIncrementalLoadingCollection` überschreiben, um Ausnahmen zu behandeln, die aus Aufrufen von `LoadMoreItemsAsync` durch Steuerelemente entstehen.

Stellen Sie sich vor, Ihre Tabelle hat viele Felder, aber Sie möchten nur einen Teil der Felder in Ihrem Steuerelement anzeigen. Folgen Sie der Anleitung unter <a href="#selecting">"Bestimmte Spalten auswählen"</a> weiter oben, um bestimmte Spalten für die Anzeige in der GUI auszuwählen.

##<a name="authentication"></a>Gewusst wie: Authentifizieren von Benutzern

Mobile Services unterstützt Authentifizierung und Autorisierung von Anwendungsbenutzern mit einer Vielzahl externer Identitätsanbieter: Facebook, Google, Microsoft Account, Twitter und Azure Active Directory. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln in Serverskripts zu implementieren. Weitere Informationen finden Sie im Lernprogramm [Authentifizierung zu Ihrer App hinzufügen].

Insgesamt werden zwei Authentifizierungsflüsse unterstützt: ein _Serverfluss_ und ein _Clientfluss_. Der Serverfluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Authentifizierungs-Webschnittstelle des Anbieters verwendet wird. Der Clientfluss ermöglicht eine tiefere Integration mit gerätespezifischen Fähigkeiten, da in diesem Fall anbieterspezifische und gerätespezifische SDKs verwendet werden.

###Serverfluss
Sie müssen Ihre Anwendung bei Ihrem Identitätsanbieter registrieren, um den mobilen Diensten die Verwaltung des Authentifizierungsprozesses in Ihrer Windows Store- oder Windows Phone-App zu ermöglichen. Anschließend müssen Sie in Ihrem mobilen Dienst die Anwendungs-ID und den geheimen Schlüssel Ihres Anbieters konfigurieren. Weitere Informationen finden Sie im Lernprogramm [Authentifizierung zu Ihrer App hinzufügen].

Nach der Registrierung bei Ihrem Identitätsanbieter können Sie die [LoginAsync]-Methode mit dem [MobileServiceAuthenticationProvider]-Wert Ihres Anbieters aufrufen. Der folgende Beispielcode startet eine Serverfluss-Anmeldung für Facebook.

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

Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den Wert für [MobileServiceAuthenticationProvider] oben entsprechend Ihrem Anbieter.

In diesem Fall verwaltet der mobile Dienst den OAuth 2.0-Authentifizierungsfluss, indem die Anmeldungsseite des ausgewählten Anbieters angezeigt und nach der erfolgreichen Anmeldung beim Identitätsanbieter ein Authentifizierungstoken für den mobilen Dienst generiert wird. Die [LoginAsync]-Methode gibt einen [MobileServiceUser] zurück, der wiederum sowohl [userId] des authentifizierten Benutzers als auch das [MobileServiceAuthenticationToken] in Form eines JSON-Webtokens (JWT) enthält. Dieses Token kann zwischen gespeichert und wiederverwendet werden, bis es abläuft. Weitere Informationen finden Sie unter [Zwischenspeichern des Authentifizierungstokens].

> [AZURE.NOTE]**Windows Store-App** Wenn Sie den Anmeldeanbieter für Microsoft-Konten zum Authentifizieren von Benutzern Ihrer Windows Store-App verwenden, sollten Sie ebenfalls das App-Paket im mobilen Dienst registrieren. Wenn Sie Ihre Windows Store-App-Paketinformationen bei Mobile Services registrieren, kann der Client die Anmeldeinformationen für Microsoft Account für eine einmalige Anmeldung verwenden. Wenn Sie dies nicht tun, werden Ihre Benutzer mit Microsoft Account-Login jedes Mal zur Anmeldung aufgefordert, wenn diese Anmeldemethode aufgerufen wird. Informationen zur Registrierung Ihres Windows Store-App-Pakets finden Sie unter [Registrieren Ihres Windows Store-App-Pakets für Microsoft-Authentifizierung](/develop/mobile/how-to-guides/register-windows-store-app-package/%20target="_blank"). Nach der Registrierung Ihres Pakets im mobilen Dienst können Sie die [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594%20target="_blank")-Methode aufrufen und den Wert **true** für den _useSingleSignOn_-Parameter übergeben, um die Anmeldeinformationen wiederzuverwenden.

###Clientfluss

Ihre Anwendung kann den Identitätsanbieter auch unabhängig kontaktieren und das zurückgegebene Token zur Authentifizierung dem mobilen Dienst vorlegen. Mit diesem Clientfluss können Sie die einmalige Anmeldung für Ihre Benutzer implementieren oder zusätzliche Benutzerdaten vom Identitätsanbieter abrufen.

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

Für Microsoft-Konten funktioniert die Anmeldung folgendermaßen:

	// Replace authentication_token_value with actual value of your Microsoft authentication token obtained through the Live SDK
	user = await client
		.LoginWithMicrosoftAccountAsync(authentication_token_value);

Ein Beispiel für die einmalige Anmeldung mit Microsoft-Konten finden Sie im Lernprogramm "Authenticate your app with single sign-in" ([Windows Store](/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/develop/mobile/tutorials/single-sign-on-wp8/)).

###<a name="caching"></a>Zwischenspeichern von Authentifizierungstoken
Manche Aufrufe der Anmeldemethode lassen sich vermeiden, nachdem sich der Benutzer authentifiziert hat. Sie können den [Kennworttresor] für Windows Store-Apps verwenden, um die Identität des aktuellen Benutzers bei der ersten Anmeldung zwischenzuspeichern und später bei Bedarf prüfen, ob Sie die Benutzeridentität bereits im Speicher haben. Wenn der Zwischenspeicher leer ist, muss der Benutzer den Anmeldeprozess durchlaufen.

	// After logging in
	PasswordVault vault = new PasswordVault();
	vault.Add(new PasswordCredential("Facebook", user.UserId, user.MobileServiceAuthenticationToken));

	// Log in
	var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
	if (creds != null)
	{
		user = new MobileServiceUser(creds.UserName);
		user.MobileServiceAuthenticationToken = vault.Retrieve("Facebook", creds.UserName).Password;
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
	vault.Remove(vault.Retrieve("Facebook", user.UserId));


Für Windows Phone-Apps können Sie die Daten mithilfe der [ProtectedData]-Klasse verschlüsseln und zwischenspeichern und die vertraulichen Informationen in einem isolierten Speicher vorhalten.

##<a name="errors"></a>Gewusst wie: Fehlerbehandlung

Mobile Dienste bieten verschiedene Möglichkeiten zur Erkennung, Validierung und Behebung von Fehlern.

Ein Beispiel sind Serverskripts, die in einem mobilen Dienst registriert sind und für eine Vielzahl von Operationen auf einzufügende und zu aktualisierende Daten verwendet werden können, inklusive Validierung und Änderung der Daten. Sie könnten z. B. das folgende Serverskript erstellen und registrieren, das Daten validiert und ändert:

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

##<a name="untyped"></a>Gewusst wie: Arbeiten mit nicht typisierten Daten

Der .NET-Client wurde für stark typisierte Szenarien entwickelt. Manchmal macht jedoch eine weniger starke Typisierung Sinn, z. B. beim Umgang mit Objekten mit offenem Schema. Dieses Szenario funktioniert wie folgt. In Ihren Abfragen verwenden Sie das Übertragungsformat anstelle von LINQ.

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Sie erhalten JSON-Werte zurück, die Sie wie einen Eigenschaftenbehälter verwenden können. Weitere Informationen zu JToken und Json.NET finden Sie unter [Json.NET](http://json.codeplex.com/)

##<a name="unit-testing"></a>Gewusst wie: Design von Komponententests

Der von `MobileServiceClient.GetTable` zurückgegebene Wert und die Abfragen sind Schnittstellen. Daher lassen sich diese Komponenten leicht zu Testzwecken "nachbilden". Sie könnten z. B. `MyMockTable : IMobileServiceTable<TodoItem>` zur Implementierung Ihrer Testlogik erstellen.

##<a name="customizing"></a>Gewusst wie: Anpassen des Clients

Dieser Abschnitt beschreibt die Möglichkeiten zum Anpassen der Anforderungsheader und der Serialisierung von JSON-Objekten in der Antwort.

### <a name="headers"></a>Gewusst wie: Anpassen der Anforderungsheader

Um Ihre spezielle App-Szenario zu unterstützen, müssen Sie möglicherweise die Kommunikation mit dem mobilen Dienst anpassen. Sie können z. B. benutzerdefinierte Header zu allen ausgehenden Anforderungen hinzufügen oder den Statuscode von Antworten ändern. Hierzu müssen Sie einen benutzerdefinierten DelegatingHandler bereitstellen, wie im folgenden Beispiel gezeigt:

	public async Task CallClientWithHandler()
	{
		MobileServiceClient client = new MobileServiceClient(
			"AppUrl",
			"AppKey" ,
			new MyHandler()
			);
		IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
		var newItem = new TodoItem { Text = "Hello world", Complete = false };
		await table.InsertAsync(newItem);
	}

	public class MyHandler : DelegatingHandler
	{
		protected override async Task<HttpResponseMessage> SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
		{
			request.Headers.Add("x-my-header", "my value");
			var response = awaitbase.SendAsync(request, cancellationToken);
			response.StatusCode = HttpStatusCode.ServiceUnavailable;
			return response;
		}
	}

Dieser Code fügt der Anforderungen einen neuen **x-my-header**-Header hinzu und legt den Antwortcode willkürlich auf "nicht verfügbar" fest. In einem realen Szenario würden Sie den Statuscode der Antwort entsprechend der benutzerdefinierten Logik festlegen, die für Ihre App erforderlich ist.

### <a name="serialization"></a>Gewusst wie: Anpassen der Serialisierung

Die Mobile Services-Clientbibliothek verwendet Json.NET, um eine JSON-Antwort in .NET-Objekte auf dem Client zu konvertieren. Sie können das Verhalten dieser Serialisierung von zwischen .NET-Typen und JSON in den Nachrichten konfigurieren. Die [MobileServiceClient](http://msdn.microsoft.com/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx)-Klasse macht eine `SerializerSettings`-Eigenschaft vom Typ [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm) verfügbar.

Mit dieser Eigenschaft können Sie eine der zahlreichen Json.NET-Eigenschaften festlegen, wie z. B. Folgende:

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

Mit dieser Eigenschaft werden alle Eigenschaften während der Serialisierung in Kleinbuchstaben konvertiert.

<!-- Anchors. -->
[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Create a table reference]: #instantiating
[How to: Query data from a mobile service]: #querying
[Zurückgegebene Daten filtern]: #filtering
[Zurückgegebene Daten sortieren]: #sorting
[Daten seitenweise zurückgeben]: #paging
[Bestimmte Spalten auswählen]: #selecting
[Daten nach ID abrufen]: #lookingup
[How to: Bind data to user interface in a mobile service]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Delete data in a mobile service]: #deleting
[How to: Use Optimistic Concurrency]: #optimisticconcurrency
[How to: Authenticate users]: #authentication
[How to: Handle errors]: #errors
[How to: Design unit tests]: #unit-testing
[How to: Query data from a mobile service]: #querying
[How to: Customize the client]: #customizing
[How to: Work with untyped data]: #untyped
[Customize request headers]: #headers
[Customize serialization]: #serialization
[Next steps]: #nextsteps
[Zwischenspeichern des Authentifizierungstokens]: #caching
[How to: Call a custom API]: #custom-api

<!-- Images. -->



<!-- URLs. -->
[Authentifizierung zu Ihrer App hinzufügen]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[Kennworttresor]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[LoginAsync]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: virtual-machines-command-line-tools.md/#Commands_to_manage_mobile_services
[Optimistic Concurrency Tutorial]: mobile-services-windows-store-dotnet-handle-database-conflicts.md

[IncludeTotalCount]: http://msdn.microsoft.com/library/windowsazure/dn250560.aspx
[Skip]: http://msdn.microsoft.com/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[Benutzerdefinierte API in Azure Mobile Services Client SDKs]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[Aufrufen einer benutzerdefinierten API aus dem Client]: mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api.md
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx

<!--HONumber=54-->