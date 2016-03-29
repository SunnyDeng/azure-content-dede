<properties
	pageTitle="Arbeiten mit der verwalteten Clientbibliothek von Mobile App Service-Apps (Windows | Xamarin) | Microsoft Azure"
	description="Erfahren Sie, wie Sie einen .NET-Client für Mobile App Service-Apps von Azure mit Windows- und Xamarin-Apps verwenden."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/08/2016"
	ms.author="glenga"/>

# Verwenden des verwalteten Clients für Azure Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

##Übersicht

In dieser Anleitung wird die Ausführung gängiger Aufgaben mithilfe der verwalteten Clientbibliothek für Mobile App Service-Apps unter Azure in Windows- und Xamarin-Apps beschrieben. Wenn Sie keine Erfahrungen mit Mobile Apps haben, sollten Sie eventuell zunächst das Tutorial [Erstellen einer Windows-App] absolvieren. In diesem Handbuch konzentrieren wir uns auf das clientseitige verwaltete SDK. Weitere Informationen zu den serverseitigen SDKs für Mobile Apps finden Sie in der Anleitung [Arbeiten Sie mit der Back-End-Server-SDK für Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) oder [Verwenden des Azure Mobile Apps SDK für Node.js](app-service-mobile-node-backend-how-to-use-server-sdk.md).

## Referenzdokumentation

Die Referenzdokumentation für das Client-SDK finden Sie hier: [.NET-Client-Referenz für Azure Mobile Apps]. Sie finden auch mehrere Clientbeispiele im [GitHub-Repository „Azure Samples“ (Azure Beispiele)].

##<a name="setup"></a>Einrichtung und Voraussetzungen

Es wird davon ausgegangen, dass Sie Ihr Mobile App-Back-End-Projekt bereits erstellt und veröffentlicht haben und dass es mindestens eine Tabelle enthält. Der Code in diesem Thema verwendet eine Tabelle mit dem Namen `TodoItem` und den folgenden Spalten: `Id`, `Text` und `Complete`. Dies ist die gleiche Tabelle, die Sie beim Durcharbeiten des Tutorials [Erstellen einer Windows-App] erstellt haben.

Der entsprechende typisierte clientseitige Typ in C# sieht wie folgt aus:

	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

Beachten Sie, dass [JsonPropertyAttribute] verwendet wird, um die *PropertyName*-Zuordnung zwischen dem Clienttyp und der Tabelle zu definieren.

Informationen zum Erstellen neuer Tabellen in Ihrem Mobile Apps-Back-End finden Sie unter [Arbeiten Sie mit der Back-End-Server-SDK für Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#define-table-controller) oder [Verwenden des Azure Mobile Apps SDK für Node.js](app-service-mobile-node-backend-how-to-use-server-sdk.md#howto-dynamicschema). Wenn Sie Ihr Mobile App-Back-End im Azure-Portal mithilfe des Schnellstarts erstellt haben, können Sie auch die Einstellung **Einfache Tabellen** im [Azure-Portal] verwenden.

###<a name="symbolsource"></a>Arbeiten mit Debugsymbolen in Visual Studio

Die Symbole für den Namespace „Microsoft.Azure.Mobile“ sind unter [SymbolSource] verfügbar. Integrieren Sie SymbolSource gemäß der [SymbolSource-Anweisungen] in Visual Studio.

##<a name="create-client"></a>Erstellen des Mobile App-Clients

Der folgende Code erstellt das [MobileServiceClient]-Objekt, das für den Zugriff auf Ihr Mobile App-Back-End verwendet wird.

	MobileServiceClient client = new MobileServiceClient("MOBILE_APP_URL");

Ersetzen Sie im obigen Code `MOBILE_APP_URL` durch die URL des Mobile App-Back-Ends. Sie finden die URL auf dem Blatt für das Mobile App-Back-End im [Azure-Portal]. Es ist normal und wird empfohlen, dass die Clientinstanz ein Singleton ist.

## Arbeiten mit Tabellen

Der folgende Abschnitt enthält Informationen zum Suchen und Abrufen von Datensätzen sowie Ändern der Daten in der Tabelle. Die folgenden Themen werden behandelt:

* [Erstellen eines Tabellenverweises](#instantiating)
* [Abfragen von Daten](#querying)
* [Zurückgegebene Daten filtern](#filtering)
* [Zurückgegebene Daten sortieren](#sorting)
* [Daten seitenweise zurückgeben](#paging)
* [Bestimmte Spalten auswählen](#selecting)
* [Suchen eines Datensatzes nach ID](#lookingup)
* [Umgang mit nicht typisierten Abfragen](#untypedqueries)
* [Einfügen von Daten](#inserting)
* [Aktualisieren von Daten](#updating)
* [Löschen von Daten](#deleting)
* [Lösung von Konflikten und optimistische Parallelität](#optimisticconcurrency)
* [Binden an eine Windows-Benutzeroberfläche](#binding)

###<a name="instantiating"></a>Erstellen von Tabellenverweisen

Jeglicher Code zum Abrufen oder Ändern von Daten in einer Back-End-Tabelle ruft Funktionen des `MobileServiceTable`-Objekts auf. Sie erhalten einen Verweis auf die Tabelle, indem Sie wie folgt die Methode [GetTable] für eine Instanz von `MobileServiceClient` aufrufen:

    IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();

Dies ist das typisierte Serialisierungsmodell. Ein nicht typisiertes Serialisierungsmodell wird ebenfalls unterstützt. Hiermit wird ein [Verweis auf eine nicht typisierte Tabelle erstellt]\:

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

Bei nicht typisierten Abfragen müssen Sie die zugrunde liegende OData-Abfragezeichenfolge angeben.

###<a name="querying"></a>Vorgehensweise: Abfragen von Daten aus Mobile App

Dieser Abschnitt beschreibt, wie Sie Abfragen an Ihr Mobile App-Back-End stellen können. Dies umfasst folgende Funktionen:

- [Zurückgegebene Daten filtern]
- [Zurückgegebene Daten sortieren]
- [Daten seitenweise zurückgeben]
- [Bestimmte Spalten auswählen]
- [Daten nach ID abrufen]

>[AZURE.NOTE] Es wird die Verwendung einer servergesteuerten Seitengröße erzwungen, um zu verhindern, dass alle Zeilen zurückgegeben werden. Damit wird verhindert, dass Standardabfragen für große Datensätze den Dienst negativ beeinflussen. Verwenden Sie die `Take`-Methode, um mehr als 50 Zeilen zurückzugeben, wie unter [Daten seitenweise zurückgeben] beschrieben.

###<a name="filtering"></a>Gewusst wie: Filtern zurückgegebener Daten

Der folgende Code zeigt, wie Sie Daten mithilfe einer `Where`-Klausel in einer Abfrage filtern. Die Abfrage gibt alle Elemente aus `todoTable` zurück, deren `Complete`-Eigenschaft gleich `false` ist. Die [Where]-Funktion wendet ein Zeilenfilterungsprädikat auf die Tabellenabfrage an.

	// This query filters out completed TodoItems and items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Sie können den URI der an das Back-End gesendeten Anforderung anzeigen, indem Sie Software zur Überprüfung von Nachrichten verwenden, z. B. Browserentwicklertools oder [Fiddler]. Beachten Sie im folgenden Anforderungs-URI, dass die Abfragezeichenfolge verändert ist:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Diese OData-Anforderung wird vom Server-SDK in eine SQL-Abfrage übersetzt, die etwa wie folgt lautet:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

Die an die `Where`-Methode übergebene Funktion kann beliebig viele Bedingungen enthalten. Die folgende Zeile:

	// This query filters out completed TodoItems where Text isn't null
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false && todoItem.Text != null)
	   .ToListAsync();

Würde vom Server-SDK in eine SQL-Abfrage übersetzt, die etwa wie folgt lautet:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND ISNULL(text, 0) = 0

Die oben aufgeführte `WHERE`-Anweisung findet Elemente, deren `Complete`-Status gleich "false" und deren `Text` nicht NULL ist.

Diese Abfrage kann auch in mehrere Klauseln aufgeteilt werden:

	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .Where(todoItem => todoItem.Text != null)
	   .ToListAsync();

Beide Methoden sind äquivalent und können frei austauschbar verwendet werden. Die erste Option – Verketten mehrerer Prädikate in einer Abfrage – ist kompakter und deshalb empfehlenswert.

Die `Where`-Klausel unterstützt Vorgänge, die in die OData-Teilmenge übersetzt werden können. Dazu gehören relationale Operatoren (==, !=, <, <=, >, >=), arithmetische Operatoren (+, -, /, *, %), Zahlengenauigkeit (Math.Floor, Math.Ceiling), Zeichenfolgenfunktionen (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), Datumseigenschaften (Year, Month, Day, Hour, Minute, Second), Zugriffseigenschaften von Objekten und Ausdrücke mit Kombinationen dieser Typen. Wenn Sie überlegen, was das Server-SDK unterstützt, können Sie die [OData v3-Dokumentation] berücksichtigen.

###<a name="sorting"></a>Gewusst wie: Sortieren zurückgegebener Daten

Der folgende Code zeigt, wie Sie Daten mithilfe einer [OrderBy]- oder einer [OrderByDescending]-Klausel in einer Abfrage sortieren können. Die Abfrage gibt Elemente aus der Tabelle `todoTable` aufsteigend sortiert nach dem `Text`-Feld zurück.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

###<a name="paging"></a>Seitenweises Zurückgeben von Daten

Standardmäßig gibt das Back-End nur die ersten 50 Zeilen zurück. Sie können die [Take]-Methode aufrufen, um die Anzahl der zurückgegebenen Zeilen zu erhöhen. Verwenden Sie `Take` zusammen mit der [Skip]-Methode, um eine bestimmte "Seite" des gesamten Datasets anzufordern, das von der Abfrage zurückgegeben wird. Die folgende Abfrage liefert die ersten drei Elemente aus der Tabelle zurück.

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

>[AZURE.NOTE]Um die Begrenzung auf 50 Zeilen in einem Mobile App-Back-End zu überschreiben, müssen Sie [EnableQueryAttribute] auf die öffentliche GET-Methode anwenden und das Pagingverhalten festlegen. Bei Anwendung des Attributs auf die Methode wird durch Folgendes die maximale Anzahl zurückgegebener Zeilen auf 1000 beschränkt:
>
>    [EnableQuery(MaxTop=1000)]

### <a name="selecting"></a>Gewusst wie: Auswählen bestimmter Spalten

Sie können angeben, welche Eigenschaften im Ergebnis enthalten sein sollen, indem Sie eine [Select]-Klausel zu Ihrer Abfrage hinzufügen. Der folgende Code zeigt, wie Sie nur ein Feld auswählen oder auch mehrere Felder auswählen und formatieren können:

	// Select one field -- just the Text
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => todoItem.Text);
	List<string> items = await query.ToListAsync();

	// Select multiple fields -- both Complete and Text info
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Select(todoItem => string.Format("{0} -- {1}",
						todoItem.Text.PadRight(30), todoItem.Complete ?
						"Now complete!" : "Incomplete!"));
	List<string> items = await query.ToListAsync();

Alle bisher beschriebenen Funktionen sind additiv, d. h. wir können sie immer wieder aufrufen und bei jedem Aufruf einen größeren Teil der Abfrage beeinflussen. Ein weiteres Beispiel:

	MobileServiceTableQuery<TodoItem> query = todoTable
					.Where(todoItem => todoItem.Complete == false)
					.Select(todoItem => todoItem.Text)
					.Skip(3).
					.Take(3);
	List<string> items = await query.ToListAsync();

### <a name="lookingup"></a>Gewusst wie: Abrufen von Daten nach ID

Die [LookupAsync]-Funktion kann verwendet werden, um Objekte mit einer bestimmten ID aus der Datenbank abzufragen.

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="untypedqueries"></a>Ausführen von nicht typisierten Abfragen

Beim Ausführen einer Abfrage mit einem nicht typisierten Tabellenobjekt müssen Sie die OData-Abfragezeichenfolge wie im folgenden Beispiel durch Aufruf von [ReadAsync] explizit angeben:

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Sie erhalten JSON-Werte zurück, die Sie wie einen Eigenschaftenbehälter verwenden können. Weitere Informationen zu JToken und Newtonsoft Json.NET finden Sie unter [Json.NET].

### <a name="inserting"></a>Einfügen von Daten in ein Mobile App-Back-End

Alle Clienttypen müssen einen Member mit dem Namen **Id** enthalten. Dies ist standardmäßig eine Zeichenfolge. Diese **Id** ist für die Durchführung von CRUD-Vorgängen und den Offlinezustand erforderlich. Der folgende Code zeigt, wie Sie mit der Methode [InsertAsync] neue Zeilen in eine Tabelle einfügen können. Der Parameter enthält die einzufügenden Daten als .NET-Objekt.

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

Mobile Apps unterstützt eindeutige benutzerdefinierte Zeichenfolgenwerte für die Spalte **id** der Tabelle. Auf diese Weise können Anwendungen benutzerdefinierte Werte wie E-Mail-Adressen oder Benutzernamen für die ID verwenden.

Zeichenfolgen-IDs bieten Ihnen die folgenden Vorteile:

* IDs werden ohne Roundtrip zur Datenbank generiert.
* Datensätze aus unterschiedlichen Tabellen oder Datenbanken lassen sich leichter zusammenführen.
* ID-Werte lassen sich möglicherweise leichter in die Anwendungslogik integrieren.

Wenn der ID-Wert einer Zeichenfolge für keine eingefügten Datensätze festgelegt ist, generiert das Mobile App-Back-End einen eindeutigen Wert für die ID. Sie können die Methode [Guid.NewGuid] verwenden, um eigene ID-Werte entweder auf dem Client oder im Back-End zu generieren.

    JObject jo = new JObject();
    jo.Add("id", Guid.NewGuid().ToString("N"));

###<a name="modifying"></a>Ändern von Daten in einem Mobile App-Back-End

Der folgende Code zeigt, wie Sie mit der Methode [UpdateAsync] einen vorhandenen Datensatz mit der gleichen ID und neuen Daten aktualisieren können. Der Parameter enthält die zu aktualisierenden Daten als .NET-Objekt.

	await todoTable.UpdateAsync(todoItem);

Zum Löschen von nicht typisierten Daten können Sie [Json.NET] wie folgt verwenden:

	JObject jo = new JObject();
	jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

Beim Ausführen eines Updates muss ein `id`-Feld angegeben werden. Sie wird benötigt, damit das Back-End erkennen kann, welche Instanz aktualisiert werden soll. Das `id`-Feld können Sie dem Ergebnis des `InsertAsync`-Aufrufs entnehmen. Eine `ArgumentException` wird ausgelöst, wenn Sie versuchen, ein Element ohne Angabe des `id`-Werts zu aktualisieren.

###<a name="deleting"></a>Löschen von Daten in einem Mobile App-Back-End

Der folgende Code zeigt, wie Sie existierende Instanzen mit der Methode [DeleteAsync] löschen können. Die Instanz wird durch das `id`-Feld identifiziert, das in `todoItem` festgelegt wird.

	await todoTable.DeleteAsync(todoItem);

Zum Löschen von nicht typisierten Daten können Sie Json.NET wie folgt verwenden:

	JObject jo = new JObject();
	jo.Add("id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

Beachten Sie, dass Sie bei einer Löschanforderung eine ID angeben müssen. Andere Eigenschaften werden nicht an den Dienst übergeben oder vom Dienst ignoriert. Das Ergebnis eines `DeleteAsync`-Aufrufs lautet normalerweise `null`. Sie erhalten die zu übergebende ID im Ergebnis des `InsertAsync`-Aufrufs. Eine `MobileServiceInvalidOperationException` wird ausgelöst, wenn Sie versuchen, ein Element ohne Angabe des `id`-Felds zu löschen.

###<a name="optimisticconcurrency"></a>Verwenden der optimistischen Parallelität zur Lösung von Konflikten

Zwei oder mehr Clients können gleichzeitig versuchen, das gleiche Element zu bearbeiten. Ohne Konflikterkennung würde der letzte Schreibvorgang alle vorherigen Aktualisierungen überschreiben, selbst wenn dies nicht so gewollt wäre. Die *Steuerung für optimistische Parallelität* nimmt an, dass jede Transaktion Commits ausführen kann und sperrt daher keine Ressourcen. Vor dem Commit einer Transaktion prüft die Steuerung für optimistische Parallelität, ob die Daten von einer anderen Transaktion geändert wurden. Falls die Daten geändert wurden, wird für die Transaktion, die den Commit durchführen sollte, ein Rollback durchgeführt.

Mobile Apps unterstützt die Steuerung für optimistische Parallelität, indem Änderungen an Elementen in der Spalte `version` mit den Systemeigenschaften nachverfolgt werden, die für jede Tabelle im Mobile App-Back-End definiert wird. Bei jeder Aktualisierung eines Datensatzes wird die `version`-Eigenschaft des entsprechenden Datensatzes von Mobile Apps auf einen neuen Wert festgelegt. Bei jeder Aktualisierungsanforderung wird die `\version`-Eigenschaft des in der Anforderung enthaltenen Datensatzes mit der Eigenschaft des Datensatzes auf dem Server verglichen. Wenn die mit der Anforderung übergebene Version nicht mit dem Back-End übereinstimmt, löst die Clientbibliothek eine `MobileServicePreconditionFailedException<T>`. Der in der Ausnahme enthaltene Typ ist der Datensatz des Back-Ends, der die Serverversion des entsprechenden Datensatzes enthält. Anschließend kann die Anwendung anhand dieser Informationen entscheiden, ob die Updateanforderung erneut mit dem korrekten `version`-Wert vom Back-End ausgeführt werden soll, um Commits für die Änderungen auszuführen.

Definieren Sie eine Spalte in der Tabellenklasse für die `version`-Systemeigenschaft, um optimistische Parallelität zu aktivieren. Beispiel:

    public class TodoItem
    {
        public string Id { get; set; }

        [JsonProperty(PropertyName = "text")]
        public string Text { get; set; }

        [JsonProperty(PropertyName = "complete")]
        public bool Complete { get; set; }

		// *** Enable Optimistic Concurrency *** //
        [JsonProperty(PropertyName = "version")]
        public byte[] Version { set; get; }
    }


Anwendungen, die nicht typisierte Tabellen verwenden, können die optimistische Nebenläufigkeit aktivieren, indem sie das `Version`-Flag in den `SystemProperties` der Tabelle wie folgt festlegen.

	//Enable optimistic concurrency by retrieving version
	todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

Zusätzlich zum Aktivieren der optimistischen Parallelität müssen Sie auch die `MobileServicePreconditionFailedException<T>`-Ausnahme beim Aufrufen von [UpdateAsync] im Code abfangen. Lösen Sie den Konflikt durch Anwenden der richtigen `version` auf den aktualisierte Datensatz, und rufen Sie [UpdateAsync] mit dem aufgelösten Datensatz auf. Der folgende Code zeigt, wie ein erkannter Schreibkonflikt gelöst werden kann:

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
	    MessageDialog msgDialog = new MessageDialog(
            String.Format("Server Text: "{0}" \nLocal Text: "{1}"\n",
            serverItem.Text, localItem.Text),
            "CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// To resolve the conflict, update the version of the item being committed. Otherwise, you will keep
        	// catching a MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;

    	    // Updating recursively here just in case another change happened while the user was making a decision
	        UpdateToDoItem(localItem);
    	};

	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};

	    await msgDialog.ShowAsync();
	}

Weitere Informationen finden Sie in dem Thema [Synchronisierung von Offlinedaten in Azure Mobile Apps].

###<a name="binding"></a>Binden von Mobile Apps-Daten an eine Windows-Benutzeroberfläche

In diesem Abschnitt wird das Anzeigen zurückgegebener Datenobjekte mithilfe von Benutzeroberflächenelementen in einer Windows-App beschrieben. Sie können den folgenden Beispielcode zur Bindung an die Quelle der Liste mit einer Abfrage unvollständiger Elemente in `todoTable` und deren Anzeige in einer sehr einfachen Liste ausführen. [MobileServiceCollection] erstellt eine Bindungsauflistung, die Mobile Apps unterstützt.

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Einige Steuerelemente in der verwalteten Laufzeit unterstützen eine Schnittstelle namens [ISupportIncrementalLoading]. Mit dieser Schnittstelle können Steuerelemente weitere Daten anfordern, wenn ein Benutzer blättert. [MobileServiceIncrementalLoadingCollection] bietet integrierte Unterstützung für diese Schnittstelle für universelle Windows-Apps und verarbeitet die Aufrufe dieser Steuerelemente automatisch. Gehen Sie wie folgt vor, um `MobileServiceIncrementalLoadingCollection` in Windows-Apps zu verwenden:

    MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
    items = todoTable.Where(todoItem => todoItem.Complete == false).ToIncrementalLoadingCollection();

    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Um die neue Sammlung in Windows Phone 8- und Silverlight-Apps zu nutzen, verwenden Sie die `ToCollection`-Erweiterungsmethoden für `IMobileServiceTableQuery<T>` und `IMobileServiceTable<T>`. Rufen Sie `LoadMoreItemsAsync()` auf, um tatsächlich Daten zu laden.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

Die Sammlung, die durch den Aufruf von `ToCollectionAsync` bzw. `ToCollection` erstellt wurde, kann an UI-Steuerelemente gebunden werden. Diese Sammlung unterstützt Seitenverwaltung, d. h. ein Steuerelement kann die Sammlung anweisen, "weitere Elemente zu laden", und die Sammlung erledigt dies für das Steuerelement. Bis zu diesem Punkt wird noch kein Benutzercode ausgeführt, das Steuerelement startet den Fluss. Da die Sammlung jedoch Daten aus dem Netzwerk lädt, ist zu erwarten, dass dieser Ladevorgang manchmal fehlschlägt. Zur Behandlung solcher Fehler können Sie die `OnException`-Methode für `MobileServiceIncrementalLoadingCollection` überschreiben, um Ausnahmen zu behandeln, die aus Aufrufen von `LoadMoreItemsAsync` durch Steuerelemente entstehen.

Stellen Sie sich vor, Ihre Tabelle hat viele Felder, aber Sie möchten nur einen Teil der Felder in Ihrem Steuerelement anzeigen. Folgen Sie der weiter oben bereitgestellten Anleitung im Abschnitt [Bestimmte Spalten auswählen](#selecting), um bestimmte Spalten für die Anzeige auf der Benutzeroberfläche auszuwählen.

##<a name="#customapi"></a>Arbeiten mit einer benutzerdefinierten API

Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Sie rufen eine benutzerdefinierte API auf, indem Sie eine der [InvokeApiAsync]-Methodenüberladungen für den Client aufrufen. Mit der folgenden Codezeile wird beispielsweise eine POST-Anforderung an die **completeAll**-API im Back-End gesendet:

    var result = await client.InvokeApiAsync<MarkAllResult>("completeAll", System.Net.Http.HttpMethod.Post, null);

Beachten Sie, dass dies ein typisierter Methodenaufruf ist, der erfordert, dass der **MarkAllResult**-Rückgabetyp definiert ist. Typisierte und nicht typisierte Methoden werden unterstützt.


##<a name="authentication"></a>Authentifizieren von Benutzern

Mobile Apps unterstützt Authentifizierung und Autorisierung von App-Benutzern mit einer Vielzahl externer Identitätsanbieter: Facebook, Google, Microsoft Account, Twitter und Azure Active Directory. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln in Serverskripts zu implementieren. Weitere Informationen finden Sie im Lernprogramm [Authentifizierung zu Ihrer App hinzufügen].

Insgesamt werden zwei Authentifizierungsflüsse unterstützt: ein _Serverfluss_ und ein _Clientfluss_. Der Serverfluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Authentifizierungs-Webschnittstelle des Anbieters verwendet wird. Der Clientfluss ermöglicht eine tiefere Integration mit gerätespezifischen Fähigkeiten, da in diesem Fall anbieterspezifische und gerätespezifische SDKs verwendet werden.

In jedem Fall müssen Sie die App bei Ihrem Identitätsanbieter registrieren. Der Identitätsanbieter stellt eine Client-ID und einen geheimen Clientschlüssel bereit. Anschließend müssen Sie die Azure App Service-Authentifizierung/-Autorisierung mit der Client-ID und dem geheimen Clientschlüssel von Ihrem Identitätsanbieter konfigurieren. Weitere Informationen finden Sie in den detaillierten Anleitung im Tutorial [Hinzufügen der Authentifizierung zu Ihrer Windows-App].

###<a name="serverflow"></a>Serverfluss
Rufen Sie nach der Registrierung bei Ihrem Identitätsanbieter den „MobileServiceClient“ auf. [LoginAsync-Methode] mit dem [[MobileServiceAuthenticationProvider]]-Wert Ihres Anbieters. Der folgende Beispielcode startet eine Serverfluss-Anmeldung über Facebook.

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

In einem Serverfluss verwaltet Azure App Service den OAuth 2.0-Authentifizierungsfluss, indem die Anmeldungsseite des ausgewählten Anbieters angezeigt und nach der erfolgreichen Anmeldung beim Identitätsanbieter ein App Service-Authentifizierungstoken generiert wird. Die [LoginAsync-Methode] gibt einen [MobileServiceUser] zurück, der wiederum sowohl die [UserId] des authentifizierten Benutzers als auch das [MobileServiceAuthenticationToken] in Form eines JSON-Webtokens (JWT) enthält. Dieses Token kann zwischen gespeichert und wiederverwendet werden, bis es abläuft. Weitere Informationen finden Sie unter [Zwischenspeichern des Authentifizierungstokens](#caching).

###Clientfluss

Ihre Anwendung kann den Identitätsanbieter auch unabhängig kontaktieren und das zurückgegebene Token zur Authentifizierung dem App Service vorlegen. Mit diesem Clientfluss können Sie die einmalige Anmeldung für Ihre Benutzer implementieren oder zusätzliche Benutzerdaten vom Identitätsanbieter abrufen.

####Einmaliges Anmelden mit einem Token von Facebook oder Google

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
				user = await client.LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
				message = string.Format("You are now logged in - {0}", user.UserId);
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

####Einmaliges Anmelden mit einem Microsoft-Konto mit dem Live SDK

Damit Sie Benutzer authentifizieren können, müssen Sie Ihre App beim Microsoft-Konto für das Developer Center registrieren. Sie müssen dann diese Registrierung mit Ihrem Mobile App-Back-End verbinden. Führen Sie die Schritte unter [Registrieren Ihrer App für die Verwendung einer Microsoft-Kontoanmeldung] aus, um eine Microsoft-Kontoregistrierung zu erstellen und diese mit Ihrem Mobile App-Back-End zu verbinden. Wenn Sie sowohl eine Windows Store- als auch eine Windows Phone 8-/Silverlight-Version Ihrer App haben, registrieren Sie zuerst die Windows Store-Version.

Im folgenden Code erfolgt eine Authentifizierung mit Live SDK und wird das zurückgegebene Token verwendet, um eine Anmeldung bei Ihrem Mobile App-Back-End vorzunehmen.

	private LiveConnectSession session;
 	//private static string clientId = "<microsoft-account-client-id>";
    private async System.Threading.Tasks.Task AuthenticateAsync()
    {

        // Get the URL the Mobile App backend.
        var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

        // Create the authentication client for Windows Store using the service URL.
        LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);
        //// Create the authentication client for Windows Phone using the client ID of the registration.
        //LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

        while (session == null)
        {
            // Request the authentication token from the Live authentication service.
			// The wl.basic scope should always be requested.  Other scopes can be added
            LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
            if (result.Status == LiveConnectSessionStatus.Connected)
            {
                session = result.Session;

                // Get information about the logged-in user.
                LiveConnectClient client = new LiveConnectClient(session);
                LiveOperationResult meResult = await client.GetAsync("me");

                // Use the Microsoft account auth token to sign in to App Service.
                MobileServiceUser loginResult = await App.MobileService
                    .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                // Display a personalized sign-in greeting.
                string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                var dialog = new MessageDialog(message, title);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
            else
            {
                session = null;
                var dialog = new MessageDialog("You must log in.", "Login Required");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }
    }

Weitere Informationen finden Sie in der Dokumentation zum [Windows Live SDK].

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

-->

### <a name="adal"></a>Authentifizieren von Benutzern mit der Active Directory-Authentifizierungsbibliothek

Nutzen Sie die Active Directory-Authentifizierungsbibliothek (Active Directory Authentication Library, ADAL), um Benutzer mithilfe von Azure Active Directory bei Ihrer Anwendung anzumelden. Dies wird aufgrund der eher nativen UX-Benutzererfahrung und der Möglichkeit, zusätzliche Anpassungen vorzunehmen, den `loginAsync()`-Methoden häufig vorgezogen.

1. Konfigurieren Sie Ihr Mobile App-Back-End für die AAD-Anmeldung, indem Sie die im Tutorial [So konfigurieren Sie Ihre App Service-Anwendung zur Verwendung der Azure Active Directory-Anmeldung] beschriebenen Schritte ausführen. Schließen Sie auch den optionalen Schritt zur Registrierung einer nativen Clientanwendung ab.

2. Öffnen Sie Ihr Projekt in Visual Studio oder Xamarin Studio, und fügen Sie einen Verweis auf das NuGet-Paket `Microsoft.IdentityModel.CLients.ActiveDirectory` hinzu. Nehmen Sie in die Suche auch Vorabversionen auf.

3. Fügen Sie auf Grundlage der verwendeten Plattform den unten stehenden Code zu Ihrer Anwendung hinzu. Nehmen Sie dabei die folgenden Änderungen vor:

* Ersetzen Sie **INSERT-AUTHORITY-HERE** durch den Namen des Mandanten, unter dem Sie Ihre Anwendung bereitgestellt haben. Das Format sollte https://login.windows.net/contoso.onmicrosoft.com entsprechen. Sie können diesen Wert auf der Registerkarte „Domäne“ in Ihrer Azure Active Directory-Instanz im [klassischen Azure-Portal] kopieren.

* Ersetzen Sie **INSERT-RESOURCE-ID-HERE** durch die Client-ID für Ihr Mobile App-Back-End. Sie finden diese im Portal auf der Registerkarte **Erweitert** unter **Azure Active Directory-Einstellungen**.

* Ersetzen Sie **INSERT-CLIENT-ID-HERE** durch die Client-ID, die Sie aus der nativen Clientanwendung kopiert haben.

* Ersetzen Sie **INSERT-REDIRECT-URI-HERE** durch den Endpunkt _/.auth/login/done_ Ihrer Website mithilfe des HTTPS-Schemas. Dieser Wert sollte etwa so aussehen: \__https://contoso.azurewebsites.net/.auth/login/done_.

Der für jede Plattform erforderliche Code:

**Windows:**

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        while (user == null)
        {
            string message;
            try
            {
                AuthenticationContext ac = new AuthenticationContext(authority);
                AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(PromptBehavior.Auto, false) );
                JObject payload = new JObject();
                payload["access_token"] = ar.AccessToken;
                user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                message = string.Format("You are now logged in - {0}", user.UserId);
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

**Xamarin.iOS**

    private MobileServiceUser user;
    private async Task AuthenticateAsync(UIViewController view)
    {
        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(view));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            Console.Error.WriteLine(@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
        }
    }

**Xamarin.Android**

    private MobileServiceUser user;
    private async Task AuthenticateAsync()
    {
        string authority = "INSERT-AUTHORITY-HERE";
        string resourceId = "INSERT-RESOURCE-ID-HERE";
        string clientId = "INSERT-CLIENT-ID-HERE";
        string redirectUri = "INSERT-REDIRECT-URI-HERE";
        try
        {
            AuthenticationContext ac = new AuthenticationContext(authority);
            AuthenticationResult ar = await ac.AcquireTokenAsync(resourceId, clientId, new Uri(redirectUri), new PlatformParameters(this));
            JObject payload = new JObject();
            payload["access_token"] = ar.AccessToken;
            user = await client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
        }
        catch (Exception ex)
        {
            AlertDialog.Builder builder = new AlertDialog.Builder(this);
            builder.SetMessage(ex.Message);
            builder.SetTitle("You must log in. Login Required");
            builder.Create().Show();
        }
    }
    protected override void OnActivityResult(int requestCode, Result resultCode, Intent data)
    {
        base.OnActivityResult(requestCode, resultCode, data);
        AuthenticationAgentContinuationHelper.SetAuthenticationAgentContinuationEventArgs(requestCode, resultCode, data);
    }

##<a name="pushnotifications">Pushbenachrichtigungen

Die folgenden Themen behandeln Pushbenachrichtigungen:

* [Registrieren für Pushbenachrichtigungen](#register-for-push)
* [Abrufen einer Windows Store-Paket-SID](#package-sid)
* [Registrieren von Pushvorlagen zum Senden plattformübergreifender Benachrichtigungen](#register-xplat)

###<a name="register-for-push"></a>Registrieren für Pushbenachrichtigungen

Mit dem Mobile Apps-Client können Sie die App für Pushbenachrichtigungen mit Azure Notification Hubs registrieren. Wenn Sie sich registrieren, erhalten Sie ein Handle, das vom plattformspezifischen Pushbenachrichtigungsdienst bezogen wird. Diesen Wert müssen Sie zusammen mit allen übrigen Tags beim Registrieren angeben. Der folgende Code registriert Ihre Windows-App für Pushbenachrichtigungen mit dem Windows-Benachrichtigungsdienst (Windows Notification Service, WNS):

    private async void InitNotificationsAsync()
    {
        // Request a push notification channel.
        var channel =await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

        // Register for notifications using the new channel.
        await MobileService.GetPush().RegisterNativeAsync(channel.Uri, null);
    }

Zu einer Pushbenachrichtigung an den WNS BENÖTIGEN Sie eine Windows Store-Paket-SID (siehe unten). Beachten Sie, dass in diesem Beispiel zwei Tags in der Registrierung enthalten sind. Weitere Informationen zu Windows-Apps, z. B. zum Registrieren für Vorlagenregistrierungen, finden Sie unter [Hinzufügen von Pushbenachrichtigungen zu Ihrer App].

Beachten Sie, dass die Anforderung von Tags vom Client nicht unterstützt wird. Tag-Anforderungen werden automatisch aus der Registrierung gelöscht. Wenn Sie Ihr Gerät mit Tags registrieren möchten, erstellen Sie eine benutzerdefinierte API, die die Notification Hubs-API verwendet, um die Registrierung in Ihrem Namen auszuführen. [Rufen Sie die benutzerdefinierte API](#customapi) statt der `RegisterNativeAsync()`-Methode auf.

###<a name="package-sid"></a>Abrufen einer Windows Store-Paket-SID

Für die Aktivierung von Pushbenachrichtigungen in Windows Store-Apps ist eine Paket-SID erforderlich. Die Paket-SID wird auch für andere Elemente (z. B. einmaliges Anmelden bei Windows) verwendet. Sie müssen Ihre Anwendung beim Windows Store registrieren, um eine Paket-SID zu erhalten.

Dieser Wert wird wie folgt abgerufen:

1. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf das Windows Store-App-Projekt, und klicken Sie dann auf **Store** > **App mit Store verknüpfen...**
2. Klicken Sie im Assistenten auf **Weiter**, melden Sie sich mit Ihrem Microsoft-Konto an, geben Sie unter **App-Namen reservieren** einen Namen für Ihre App ein, und klicken Sie dann auf **Reservieren**.
3. Nachdem die App-Registrierung erfolgreich erstellt wurde, wählen Sie den Namen der neuen App aus, klicken Sie auf **Weiter** und dann auf **Zuordnen**. Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.
4. Melden Sie sich mit Ihrem Microsoft-Konto beim [Windows Dev Center] an. Klicken Sie unter **Meine Apps** auf die soeben erstellte App-Registrierung.
5. Klicken Sie auf **App-Verwaltung** > **App-Identität**, und scrollen Sie nach unten zu Ihrer **Paket-SID**.

In vielen Anwendungsfällen wird die Paket-SID als URI behandelt. In diesem Fall müssen Sie _ms-app://_ als Schema verwenden. Notieren Sie sich die Version der Paket-SID, die durch Verkettung dieses Werts als Präfix gebildet wird.

Xamarin-Apps erfordern zusätzlichen Code zum Registrieren einer App unter iOS oder Android beim Apple Push Notification Service (APNS) bzw. den Google Cloud Messaging-Diensten (GCM). Weitere Informationen finden Sie im Thema zu Ihrer Plattform:

* [Xamarin.Android](app-service-mobile-xamarin-android-get-started-push.md#add-push)
* [Xamarin.iOS](app-service-mobile-xamarin-ios-get-started-push.md#add-push)

###<a name="register-xplat"></a>Gewusst wie: Registrieren von Pushvorlagen zum Senden plattformübergreifender Benachrichtigungen

Um Vorlagen zu registrieren, verwenden Sie die `RegisterAsync()`-Methode mit den Vorlagen wie folgt:

        JObject templates = myTemplates();
        MobileService.GetPush().RegisterAsync(channel.Uri, templates);

Ihre Vorlagen vom Typ „JObject“ können mehrere Vorlagen im folgenden JSON-Format enthalten:

        public JObject myTemplates()
        {
            // single template for Windows Notification Service toast
            var template = "<toast><visual><binding template="ToastText01"><text id="1">$(message)</text></binding></visual></toast>";

            var templates = new JObject
            {
                ["generic-message"] = new JObject
                {
                    ["body"] = template,
                    ["headers"] = new JObject
                    {
                        ["X-WNS-Type"] = "wns/toast"
                    },
                    ["tags"] = new JArray()
                },
                ["more-templates"] = new JObject {...}
            };
            return templates;
        }

Die **RegisterAsync()**-Methode akzeptiert auch sekundäre Kacheln:

        MobileService.GetPush().RegisterAsync(string channelUri, JObject templates, JObject secondaryTiles);

Beachten Sie, dass aus Sicherheitsgründen alle Tags entfernt werden. Informationen zum Hinzufügen von Tags zu Installationen bzw. Vorlagen innerhalb von Installationen finden Sie unter [Arbeiten Sie mit dem Back-End-Server-SDK für Azure Mobile Apps].

Zum Senden von Benachrichtigungen, die diese registrierten Vorlagen verwenden, informieren Sie sich über die [Notification Hubs-APIs].

##<a name="misc"></a>Verschiedene Themen

###<a name="errors"></a>Gewusst wie: Fehlerbehandlung

Tritt ein Fehler im Back-End auf, löst das Client-SDK eine `MobileServiceInvalidOperationException` aus. Das folgende Beispiel zeigt, wie eine vom Back-End zurückgegebene Ausnahme behandelt wird:

	private async void InsertTodoItem(TodoItem todoItem)
	{
		// This code inserts a new TodoItem into the database. When the operation completes
		// and App Service has assigned an Id, the item is added to the CollectionView
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

Ein weiteres Beispiel für den Umgang mit Fehlerbedingungen finden Sie im [Mobile Apps-Dateienbeispiel] – das [LoggingHandler]-Beispiel zeigt einen Handler zum Delegieren von Protokollierungen (siehe unten), mit dem die Anforderungen protokolliert werden können, die an das Back-End gestellt werden. Dies bietet eine einfachere Möglichkeit zum Debuggen von Xamarin-Anwendung, als sich auf Fiddler zu verlassen.

###<a name="headers"></a>Gewusst wie: Anpassen der Anforderungsheader

Um Ihr spezielles App-Szenario zu unterstützen, müssen Sie unter Umständen die Kommunikation mit dem Mobile App-Back-End anpassen. Sie können z. B. benutzerdefinierte Header zu allen ausgehenden Anforderungen hinzufügen oder den Statuscode von Antworten ändern. Hierzu müssen Sie ein benutzerdefiniertes [DelegatingHandler]-Element bereitstellen, wie im folgenden Beispiel gezeigt:

    public async Task CallClientWithHandler()
    {
        HttpResponseMessage[]
        MobileServiceClient client = new MobileServiceClient("AppUrl",
            new MyHandler()
            );
        IMobileServiceTable<TodoItem> todoTable = client.GetTable<TodoItem>();
        var newItem = new TodoItem { Text = "Hello world", Complete = false };
        await todoTable.InsertAsync(newItem);
    }

    public class MyHandler : DelegatingHandler
    {
        protected override async Task<HttpResponseMessage>
            SendAsync(HttpRequestMessage request, CancellationToken cancellationToken)
        {
            // Change the request-side here based on the HttpRequestMessage
            request.Headers.Add("x-my-header", "my value");

            // Do the request
            var response = await base.SendAsync(request, cancellationToken);

            // Change the response-side here based on the HttpResponseMessage

            // Return the modified response
            return response;
        }
    }


<!-- Anchors. -->
[Zurückgegebene Daten filtern]: #filtering
[Zurückgegebene Daten sortieren]: #sorting
[Daten seitenweise zurückgeben]: #paging
[Bestimmte Spalten auswählen]: #selecting
[Daten nach ID abrufen]: #lookingup

<!-- Images. -->

<!-- Internal URLs. -->
[Erstellen einer Windows-App]: app-service-mobile-windows-store-dotnet-get-started.md
[Authentifizierung zu Ihrer App hinzufügen]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Hinzufügen der Authentifizierung zu Ihrer Windows-App]: app-service-mobile-windows-store-dotnet-get-started-users.md
[Work with .NET backend SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Arbeiten Sie mit dem Back-End-Server-SDK für Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[How to use the Node.js backend SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md
[How to: Define a table controller]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-define-a-table-controller
[Define Tables using a Dynamic Schema]: app-service-mobile-node-backend-how-to-use-server-sdk.md#TableOperations
[Synchronisierung von Offlinedaten in Azure Mobile Apps]: app-service-mobile-offline-data-sync.md
[Hinzufügen von Pushbenachrichtigungen zu Ihrer App]: app-service-mobile-windows-store-dotnet-get-started-push.md
[Registrieren Ihrer App für die Verwendung einer Microsoft-Kontoanmeldung]: app-service-mobile-how-to-configure-microsoft-authentication.md
[So konfigurieren Sie Ihre App Service-Anwendung zur Verwendung der Azure Active Directory-Anmeldung]: app-service-mobile-how-to-configure-active-directory-authentication.md

<!-- Microsoft URLs. -->
[.NET-Client-Referenz für Azure Mobile Apps]: https://msdn.microsoft.com/de-DE/library/azure/mt419521(v=azure.10).aspx
[MobileServiceClient]: https://msdn.microsoft.com/de-DE/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient(v=azure.10).aspx
[MobileServiceCollection]: https://msdn.microsoft.com/de-DE/library/azure/dn250636(v=azure.10).aspx
[MobileServiceIncrementalLoadingCollection]: https://msdn.microsoft.com/de-DE/library/azure/dn268408(v=azure.10).aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[[MobileServiceAuthenticationProvider]]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider(v=azure.10).aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser(v=azure.10).aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken(v=azure.10).aspx
[GetTable]: https://msdn.microsoft.com/de-DE/library/azure/jj554275(v=azure.10).aspx
[Verweis auf eine nicht typisierte Tabelle erstellt]: https://msdn.microsoft.com/de-DE/library/azure/jj554278(v=azure.10).aspx
[DeleteAsync]: https://msdn.microsoft.com/de-DE/library/azure/dn296407(v=azure.10).aspx
[IncludeTotalCount]: https://msdn.microsoft.com/de-DE/library/azure/dn250560(v=azure.10).aspx
[InsertAsync]: https://msdn.microsoft.com/de-DE/library/azure/dn296400(v=azure.10).aspx
[InvokeApiAsync]: https://msdn.microsoft.com/de-DE/library/azure/dn268343(v=azure.10).aspx
[LoginAsync]: https://msdn.microsoft.com/de-DE/library/azure/dn296411(v=azure.10).aspx
[LookupAsync]: https://msdn.microsoft.com/de-DE/library/azure/jj871654(v=azure.10).aspx
[OrderBy]: https://msdn.microsoft.com/de-DE/library/azure/dn250572(v=azure.10).aspx
[OrderByDescending]: https://msdn.microsoft.com/de-DE/library/azure/dn250568(v=azure.10).aspx
[ReadAsync]: https://msdn.microsoft.com/de-DE/library/azure/mt691741(v=azure.10).aspx
[Take]: https://msdn.microsoft.com/de-DE/library/azure/dn250574(v=azure.10).aspx
[Select]: https://msdn.microsoft.com/de-DE/library/azure/dn250569(v=azure.10).aspx
[Skip]: https://msdn.microsoft.com/de-DE/library/azure/dn250573(v=azure.10).aspx
[UpdateAsync]: https://msdn.microsoft.com/de-DE/library/azure/dn250536.(v=azure.10)aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid(v=azure.10).aspx
[Where]: https://msdn.microsoft.com/de-DE/library/azure/dn250579(v=azure.10).aspx
[Azure-Portal]: https://portal.azure.com/
[klassischen Azure-Portal]: https://manage.windowsazure.com/
[EnableQueryAttribute]: https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx
[Guid.NewGuid]: https://msdn.microsoft.com/de-DE/library/system.guid.newguid(v=vs.110).aspx
[ISupportIncrementalLoading]: http://msdn.microsoft.com/library/windows/apps/Hh701916.aspx
[Windows Dev Center]: https://dev.windows.com/de-DE/overview
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx
[Windows Live SDK]: https://msdn.microsoft.com/de-DE/library/bb404787.aspx
[Kennworttresor]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Notification Hubs-APIs]: https://msdn.microsoft.com/library/azure/dn495101.aspx
[Mobile Apps-Dateienbeispiel]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files
[LoggingHandler]: https://github.com/Azure-Samples/app-service-mobile-dotnet-todo-list-files/blob/master/src/client/MobileAppsFilesSample/Helpers/LoggingHandler.cs#L63
[GitHub-Repository „Azure Samples“ (Azure Beispiele)]: https://github.com/Azure-Samples

<!-- External URLs -->
[JsonPropertyAttribute]: http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm
[OData v3-Dokumentation]: http://www.odata.org/documentation/odata-version-3-0/
[Fiddler]: http://www.telerik.com/fiddler
[Json.NET]: http://www.newtonsoft.com/json
[SymbolSource]: http://www.symbolsource.org/
[SymbolSource-Anweisungen]: http://www.symbolsource.org/Public/Wiki/Using

<!---HONumber=AcomDC_0316_2016-->