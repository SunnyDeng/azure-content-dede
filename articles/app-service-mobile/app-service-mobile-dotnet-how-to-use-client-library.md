<properties
	pageTitle="Arbeiten mit der verwalteten Clientbibliothek von Mobile App Service-Apps (Windows | Xamarin) | Microsoft Azure"
	description="Erfahren Sie, wie Sie einen .NET-Client für Mobile App Service-Apps von Azure mit Windows- und Xamarin-Apps verwenden."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="11/05/2015" 
	ms.author="glenga"/>

# Verwenden des verwalteten Clients für Azure Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]&nbsp;

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Übersicht 

In dieser Anleitung wird die Ausführung gängiger Aufgaben mithilfe der verwalteten Clientbibliothek für Mobile App Service-Apps unter Azure in Windows- und Xamarin-Apps beschrieben. Wenn Sie keine Erfahrungen mit Mobile Apps haben, sollten Sie eventuell zunächst das Lernprogramm [Mobile Apps-Schnellstart](app-service-mobile-windows-store-dotnet-get-started.md) absolvieren. In diesem Handbuch konzentrieren wir uns auf das clientseitige verwaltete SDK. Weitere Informationen zum serverseitigen SDK für das .NET-Back-End finden Sie unter [Arbeiten mit dem .NET-Back-End](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md).

## Referenzdokumentation

Die Referenzdokumentation für das Client-SDK finden Sie hier: [Azure Mobile Apps .NET-Client-Referenz](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.aspx).

##<a name="setup"></a>Einrichtung und Voraussetzungen

Es wird davon ausgegangen, dass Sie Ihr Mobile App-Back-End-Projekt bereits erstellt und veröffentlicht haben und dass es mindestens eine Tabelle enthält. Der Code in diesem Thema verwendet eine Tabelle mit dem Namen `TodoItem` und den folgenden Spalten: `Id`, `Text` und `Complete`. Dies ist die Tabelle, die Sie beim Durcharbeiten des [Schnellstarttutorials](app-service-mobile-windows-store-dotnet-get-started.md) erstellt haben.

Der entsprechende typisierte clientseitige Typ in C# sieht wie folgt aus:


	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

Beachten Sie, dass das [JsonPropertyAttribute](http://www.newtonsoft.com/json/help/html/Properties_T_Newtonsoft_Json_JsonPropertyAttribute.htm) verwendet wird, um die *PropertyName*-Zuordnung zwischen dem Clienttyp und der Tabelle zu definieren.

##<a name="create-client"></a>Erstellen des Mobile App-Clients

Mit dem folgenden Code wird das `MobileServiceClient`-Objekt erstellt, das zum Zugreifen auf Ihr Mobile App-Back-End verwendet wird.

	MobileServiceClient client = new MobileServiceClient("MOBILE_APP_URL");

Ersetzen Sie im obigen Code `MOBILE_APP_URL` durch die URL des Mobile App-Back-Ends. Sie finden die URL auf dem Blatt für das Mobile App-Back-End im [Azure-Portal](https://portal.azure.com).

##<a name="instantiating"></a>Gewusst wie: Erstellen von Tabellenverweisen

Jeglicher Code zum Abrufen oder Ändern von Daten in einer Back-End-Tabelle ruft Funktionen des `MobileServiceTable`-Objekts auf. Sie erhalten einen Verweis auf die Tabelle, indem Sie wie folgt die Methode [GetTable](https://msdn.microsoft.com/library/azure/jj554275.aspx) für eine Instanz von `MobileServiceClient` aufrufen:

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

Dies ist das typisierte Serialisierungsmodell. Ein nicht typisiertes Serialisierungsmodell wird ebenfalls unterstützt. Hiermit wird ein Verweis auf eine nicht typisierte Tabelle erstellt:

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

Bei nicht typisierten Abfragen müssen Sie die zugrunde liegende OData-Abfragezeichenfolge angeben.

##<a name="querying"></a>Abfragen von Daten aus Mobile App

Dieser Abschnitt beschreibt, wie Sie Abfragen an Ihr Mobile App-Back-End stellen können. Dies umfasst folgende Funktionen:

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

Sie können den URI der an das Back-End gesendeten Anforderung anzeigen, indem Sie Software zur Überprüfung von Nachrichten verwenden, z. B. Browser-Entwicklertools oder [Fiddler]. Beachten Sie in der folgenden URI, dass wir die eigentliche Abfragezeichenfolge verändern:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Diese Anfrage entspricht normalerweise in etwa der folgenden SQL-Abfrage auf der Azure-Seite:

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

Beide Methoden sind äquivalent und können frei austauschbar verwendet werden. Die erste Option – Verketten mehrerer Prädikate in einer Abfrage – ist kompakter und deshalb empfehlenswert.

Die `where`-Klausel unterstützt Vorgänge, die in die OData-Teilmenge übersetzt werden können. Dazu gehören relationale Operatoren (==, !=, <, <=, >, >=), arithmetische Operatoren (+, -, /, *, %), Zahlengenauigkeit (Math.Floor, Math.Ceiling), Zeichenfolgenfunktionen (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), Datumseigenschaften (Year, Month, Day, Hour, Minute, Second), Zugriffseigenschaften von Objekten und Ausdrücke mit Kombinationen dieser Typen.

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

Standardmäßig gibt das Back-End nur die ersten 50 Zeilen zurück. Sie können die [Take]-Methode aufrufen, um die Anzahl der zurückgegebenen Zeilen zu erhöhen. Verwenden Sie `Take` zusammen mit der [Skip]-Methode, um eine bestimmte "Seite" des gesamten Datasets anzufordern, das von der Abfrage zurückgegeben wird. Die folgende Abfrage liefert die ersten drei Elemente aus der Tabelle zurück.

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

>[AZURE.NOTE]Um die Begrenzung auf 50 Zeilen in einem Mobile App-Back-End zu überschreiben, müssen Sie das [EnableQueryAttribute](https://msdn.microsoft.com/library/system.web.http.odata.enablequeryattribute.aspx) auf die öffentliche GET-Methode anwenden und das Pagingverhalten festlegen. Bei Anwendung des Attributs auf die Methode wird durch Folgendes die maximale Anzahl zurückgegebener Zeilen auf 1000 beschränkt:

    [EnableQuery(MaxTop=1000)]


### <a name="selecting"></a>Gewusst wie: Auswählen bestimmter Spalten

Sie können angeben, welche Eigenschaften in den Ergebnissen enthalten sein sollen, indem Sie der Abfrage eine `Select`-Klausel hinzufügen. Der folgende Code zeigt, wie Sie nur ein Feld auswählen oder auch mehrere Felder auswählen und formatieren können:

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

Die `LookupAsync`-Funktion kann verwendet werden, um Objekte mit einer bestimmten ID in der Datenbank zu suchen.

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

### <a name="lookingup"></a>Ausführen von nicht typisierten Abfragen

Beim Ausführen einer Abfrage mit einem nicht typisierten Tabellenobjekt müssen Sie die OData-Abfragezeichenfolge wie im folgenden Beispiel explizit angeben:

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Sie erhalten JSON-Werte zurück, die Sie wie einen Eigenschaftenbehälter verwenden können. Weitere Informationen zu JToken und Json.NET finden Sie unter [Json.NET](http://json.codeplex.com/)

##<a name="inserting"></a>Einfügen von Daten in ein Mobile App-Back-End

Alle Clienttypen müssen einen Member mit dem Namen **Id** enthalten. Dies ist standardmäßig eine Zeichenfolge. Diese **Id** ist für die Durchführung von CRUD-Vorgängen und den Offlinezustand erforderlich. Der folgende Code zeigt, wie Sie neue Zeilen in eine Tabelle einfügen können. Der Parameter enthält die einzufügenden Daten als .NET-Objekt.

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

+ IDs werden ohne Roundtrip zur Datenbank generiert.
+ Datensätze aus unterschiedlichen Tabellen oder Datenbanken lassen sich leichter zusammenführen.
+ ID-Werte lassen sich möglicherweise leichter in die Anwendungslogik integrieren.

Wenn der ID-Wert einer Zeichenfolge für keine eingefügten Datensätze festgelegt ist, generiert das Mobile App-Back-End einen eindeutigen Wert für die ID. Sie können die `Guid.NewGuid()`-Methode verwenden, um eigene ID-Werte entweder auf dem Client oder im Back-End zu generieren.

##<a name="modifying"></a>Ändern von Daten in einem Mobile App-Back-End

Der folgende Code zeigt, wie Sie eine existierende Instanz mit derselben Id und neuen Daten aktualisieren können. Der Parameter enthält die zu aktualisierenden Daten als .NET-Objekt.

	await todoTable.UpdateAsync(todoItem);

Zum Einfügen nicht typisierter Daten können Sie Json.NET wie folgt nutzen: JObject jo = new JObject(); jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D"); jo.Add("Text", "Hello World"); jo.Add("Complete", false); var inserted = await table.UpdateAsync(jo);

Beachten Sie, dass Sie beim Durchführen eines Updates eine ID angeben müssen. Sie wird benötigt, damit das Back-End erkennen kann, welche Instanz aktualisiert werden soll. Sie finden die ID im Ergebnis des `InsertAsync`-Aufrufs. Wenn Sie versuchen, ein Element ohne Angabe eines Werts für „Id“ zu aktualisieren, wird eine `ArgumentException` ausgelöst.


##<a name="deleting"></a>Löschen von Daten in einem Mobile App-Back-End

Der folgende Code zeigt, wie Sie existierende Instanzen löschen können. Die Instanz wird durch das in `todoItem` festgelegte "Id"-Feld identifiziert.

	await todoTable.DeleteAsync(todoItem);

Zum Löschen von nicht typisierten Daten können Sie Json.NET wie folgt verwenden:

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

Beachten Sie, dass Sie bei einer Löschanforderung eine ID angeben müssen. Andere Eigenschaften werden nicht an den Dienst übergeben oder vom Dienst ignoriert. Das Ergebnis eines `DeleteAsync`-Aufrufs lautet normalerweise `null`. Sie erhalten die zu übergebende ID im Ergebnis des `InsertAsync`-Aufrufs. Wenn Sie versuchen, ein Element ohne vorherige Eingabe im Feld „Id“ zu löschen, wird vom Back-End eine `MobileServiceInvalidOperationException` zurückgegeben.

##<a name="#custom-api"></a>Gewusst wie: Aufrufen einer benutzerdefinierten API

Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats.

Sie rufen eine benutzerdefinierte API auf, indem Sie eine der [InvokeApiAsync]-Methodenüberladungen für den Client aufrufen. Mit der folgenden Codezeile wird beispielsweise eine POST-Anforderung an die **completeAll**-API auf dem Back-End gesendet:

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

Beachten Sie, dass dies ein typisierter Methodenaufruf ist, der erfordert, dass der **MarkAllResult**-Rückgabetyp definiert ist. Typisierte und nicht typisierte Methoden werden unterstützt. Dieses Beispiel ist fast zu einfach, da es typisiert ist und keine Abfrageparameter enthält und weil keine Nutzlast gesendet und keine Anforderungsheader geändert werden. Beispiele mit mehr Praxisnähe und eine ausführlichere Erörterung von [InvokeApiAsync] finden Sie unter [Benutzerdefinierte API in Azure Mobile Services Client SDKs].

##Gewusst wie: Registrieren für Pushbenachrichtigungen

Mit dem Mobile Apps-Client können Sie die App für Pushbenachrichtigungen mit Azure Notification Hubs registrieren. Wenn Sie sich registrieren, erhalten Sie ein Handle, das vom plattformspezifischen Pushbenachrichtigungsdienst bezogen wird. Diesen Wert müssen Sie zusammen mit allen übrigen Tags beim Registrieren angeben. Der folgende Code registriert Ihre Windows-App für Pushbenachrichtigungen mit dem Windows-Benachrichtigungsdienst (Windows Notification Service, WNS):

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

Beachten Sie, dass in diesem Beispiel zwei Tags in der Registrierung enthalten sind. Weitere Informationen zu Windows-Apps, z. B. zum Registrieren für Vorlagenregistrierungen, finden Sie unter [Hinzufügen von Pushbenachrichtigungen zu Ihrer App](app-service-mobile-windows-store-dotnet-get-started-push.md).

Xamarin-Apps erfordern zusätzlichen Code zum Registrieren einer App unter iOS oder Android beim Apple Push Notification Service (APNS) bzw. den Google Cloud Messaging-Diensten (GCM). Weitere Informationen finden Sie unter **Hinzufügen von Pushbenachrichtigungen zur App** ([Xamarin.iOS](partner-xamarin-mobile-services-ios-get-started-push.md#add-push) | [Xamarin.Android](partner-xamarin-mobile-services-android-get-started-push.md#add-push)).

## Registrieren von Pushvorlagen zum Senden plattformübergreifender Benachrichtigungen

Um Vorlagen zu registrieren, übergeben Sie einfach die Vorlagen mit der **MobileService.GetPush().RegisterAsync()**-Methode in Ihrer Client-App.

        MobileService.GetPush().RegisterAsync(channel.Uri, newTemplates());

Ihre Vorlagen vom Typ „JObject“ können mehrere Vorlagen im folgenden JSON-Format enthalten:

        public JObject newTemplates()
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

Beachten Sie, dass aus Sicherheitsgründen alle Tags entfernt werden. Informationen zum Hinzufügen von Tags zu Installationen bzw. Vorlagen innerhalb von Installationen finden Sie unter [Arbeiten Sie mit der Back-End-Server-SDK für Azure Mobile Apps].

Zum Senden von Benachrichtigungen, die diese registrierten Vorlagen verwenden, arbeiten Sie mit den [Notification Hubs-APIs](https://msdn.microsoft.com/library/azure/dn495101.aspx).

##<a name="optimisticconcurrency"></a>Gewusst wie: Verwenden von optimistischer Parallelität.

In manchen Szenarien können zwei oder mehr Clients gleichzeitig versuchen, dasselbe Element zu bearbeiten. Ohne Konflikterkennung würde der letzte Schreibvorgang alle vorherigen Aktualisierungen überschreiben, selbst wenn dies nicht so gewollt wäre. Die *Steuerung für optimistische Parallelität* nimmt an, dass jede Transaktion Commits ausführen kann und sperrt daher keine Ressourcen. Vor dem Commit einer Transaktion prüft die Steuerung für optimistische Parallelität, ob die Daten von einer anderen Transaktion geändert wurden. Falls die Daten geändert wurden, wird für die Transaktion, die den Commit durchführen sollte, ein Rollback durchgeführt.

Mobile Apps unterstützt die Steuerung für optimistische Parallelität, indem Änderungen an Elementen in der Spalte `__version` mit den Systemeigenschaften nachverfolgt werden, die für jede Tabelle im Mobile App-Back-End definiert wird. Bei jeder Aktualisierung eines Datensatzes wird die `__version`-Eigenschaft des entsprechenden Datensatzes von Mobile Apps auf einen neuen Wert festgelegt. Bei jeder Aktualisierungsanforderung wird die `__version`-Eigenschaft des in der Anforderung enthaltenen Datensatzes mit der Eigenschaft des Datensatzes auf dem Server verglichen. Wenn die mit der Anforderung übergebene Version nicht mit dem Back-End übereinstimmt, löst die Clientbibliothek eine `MobileServicePreconditionFailedException<T>` aus. Der in der Ausnahme enthaltene Typ ist der Eintrag des Back-Ends, der die Serverversion des entsprechenden Eintrags enthält. Anschließend kann die Anwendung anhand dieser Informationen entscheiden, ob die Updateanforderung erneut mit dem korrekten `__version`-Wert vom Back-End ausgeführt werden soll, um Commits für die Änderungen auszuführen.

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

Weitere Informationen finden Sie unter [Synchronisierung von Offlinedaten in Azure Mobile Apps](app-service-mobile-offline-data-sync.md).


##<a name="binding"></a>Binden von Mobile Apps-Daten an eine Windows-Benutzeroberfläche

In diesem Abschnitt wird das Anzeigen zurückgegebener Datenobjekte mithilfe von Benutzeroberflächenelementen in einer Windows-App beschrieben. Für die Abfrage unvollständiger Elemente aus `todoTable` und deren Anzeige in einer sehr einfachen Liste können Sie den folgenden Beispielcode ausführen, um die Quelle der Liste an eine Abfrage zu binden. Durch `MobileServiceCollection` wird eine Bindungssammlung erstellt, die Mobile Apps unterstützt.

	// This query filters out completed TodoItems.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Einige Steuerelemente in der verwalteten Laufzeit unterstützen eine Schnittstelle namens [ISupportIncrementalLoading](http://msdn.microsoft.com/library/windows/apps/Hh701916). Mit dieser Schnittstelle können Steuerelemente weitere Daten anfordern, wenn ein Benutzer blättert. `MobileServiceIncrementalLoadingCollection` bietet integrierte Unterstützung für diese Schnittstelle für universelle Windows 8.1-Apps und verarbeitet die Aufrufe dieser Steuerelemente automatisch. Gehen Sie wie folgt vor, um `MobileServiceIncrementalLoadingCollection` in Windows-Apps zu verwenden:

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


Um die neue Sammlung in Windows Phone 8- und Silverlight-Apps zu nutzen, verwenden Sie die `ToCollection`-Erweiterungsmethoden für `IMobileServiceTableQuery<T>` und `IMobileServiceTable<T>`. Rufen Sie `LoadMoreItemsAsync()` auf, um tatsächlich Daten zu laden.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await items.LoadMoreItemsAsync();

Die Sammlung, die durch den Aufruf von `ToCollectionAsync` bzw. `ToCollection` erstellt wurde, kann an UI-Steuerelemente gebunden werden. Diese Sammlung unterstützt Seitenverwaltung, d. h. ein Steuerelement kann die Sammlung anweisen, "weitere Elemente zu laden", und die Sammlung erledigt dies für das Steuerelement. Bis zu diesem Punkt wird noch kein Benutzercode ausgeführt, das Steuerelement startet den Fluss. Da die Sammlung jedoch Daten aus dem Netzwerk lädt, ist zu erwarten, dass dieser Ladevorgang manchmal fehlschlägt. Zur Behandlung solcher Fehler können Sie die `OnException`-Methode für `MobileServiceIncrementalLoadingCollection` überschreiben, um Ausnahmen zu behandeln, die aus Aufrufen von `LoadMoreItemsAsync` durch Steuerelemente entstehen.

Stellen Sie sich vor, Ihre Tabelle hat viele Felder, aber Sie möchten nur einen Teil der Felder in Ihrem Steuerelement anzeigen. Folgen Sie der weiter oben bereitgestellten Anleitung im Abschnitt [Bestimmte Spalten auswählen](#selecting), um bestimmte Spalten für die Anzeige auf der Benutzeroberfläche auszuwählen.

## <a name="package-sid"></a>Abrufen einer Windows Store-Paket-SID

Für Windows-Apps ist eine Paket-SID für die Aktivierung von Pushbenachrichtigungen und bestimmter Authentifizierungsmodi erforderlich. Dieser Wert wird wie folgt abgerufen:

1. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf das Windows Store-App-Projekt, und klicken Sie dann auf **Store** > **App mit Store verknüpfen...**
2. Klicken Sie im Assistenten auf **Weiter**, melden Sie sich mit Ihrem Microsoft-Konto an, geben Sie unter **App-Namen reservieren** einen Namen für Ihre App ein, und klicken Sie dann auf **Reservieren**.
3. Nachdem die App-Registrierung erfolgreich erstellt wurde, wählen Sie den Namen der neuen App aus, klicken Sie auf **Weiter** und dann auf **Zuordnen**. Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.
4. Melden Sie sich mit Ihrem Microsoft-Konto beim [Windows Dev Center](https://dev.windows.com/de-DE/overview) an. Klicken Sie unter **Meine Apps** auf die soeben erstellte App-Registrierung.
5. Klicken Sie auf **App-Verwaltung** > **App-Identität**, und scrollen Sie nach unten zu Ihrer **Paket-SID**.

In vielen Anwendungsfällen wird die Paket-SID als URI behandelt. In diesem Fall müssen Sie _ms-app://_ als Schema verwenden. Notieren Sie sich die Version der Paket-SID, die durch Verkettung dieses Werts als Präfix gebildet wird.

<!--- We want to just point to the authentication topic when it's done
##<a name="authentication"></a>How to: Authenticate users

Mobile Apps supports authenticating and authorizing app users using a variety of external identity providers: Facebook, Google, Microsoft Account, Twitter, and Azure Active Directory. You can set permissions on tables to restrict access for specific operations to only authenticated users. You can also use the identity of authenticated users to implement authorization rules in server scripts. For more information, see the tutorial [Add authentication to your app].

Two authentication flows are supported: a _server flow_ and a _client flow_. The server flow provides the simplest authentication experience, as it relies on the provider's web authentication interface. The client flow allows for deeper integration with device-specific capabilities as it relies on provider-specific device-specific SDKs.

###Server flow
To have App Service manage the authentication process in your Windows apps,
you must register your app with your identity provider. Then in your mobile App backed, you need to configure the application ID and secret provided by your provider. For more information, see the tutorial [Add authentication to your app].

Once you have registered your identity provider, simply call the [LoginAsync method] with the [MobileServiceAuthenticationProvider] value of your provider. For example, the following code initiates a server flow sign-in by using Facebook.

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

If you are using an identity provider other than Facebook, change the value of [MobileServiceAuthenticationProvider] above to the value for your provider.

In this case, App Service manages the OAuth 2.0 authentication flow by displaying the sign-in page of the selected provider and generating an App Service authentication token after successful sign-on with the identity provider. The [LoginAsync method] returns a [MobileServiceUser], which provides both the [userId] of the authenticated user and the [MobileServiceAuthenticationToken], as a JSON web token (JWT). This token can be cached and re-used until it expires. For more information, see [Caching the authentication token].

###Client flow

Your app can also independently contact the identity provider and then provide the returned token to App Service for authentication. This client flow enables you to provide a single sign-in experience for users or to retrieve additional user data from the identity provider.

####Single sign-in using a token from Facebook or Google

In the most simplified form, you can use the client flow as shown in this snippet for Facebook or Google.

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


####Single sign-in using Microsoft Account with the Live SDK

To be able to authenticate users, you must register your app at the Microsoft account Developer Center. You must then connect this registration with your Mobile App backend. Complete the steps in [Register your app to use a Microsoft account login](mobile-services-how-to-register-microsoft-authentication.md) to create a Microsoft account registration and connect it to your Mobile App backend. If you have both Windows Store and Windows Phone 8/Silverlight versions of your app, register the Windows Store version first.

The following code authenticates using Live SDK and uses the returned token to sign-in to your Mobile App backend. 

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
			// The wl.basic scope is requested.
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


###<a name="caching"></a>Caching the authentication token
In some cases, the call to the login method can be avoided after the first time the user authenticates. You can use [PasswordVault] for Windows Store apps to cache the current user identity the first time they log in and every subsequent time you check whether you already have the user identity in our cache. When the cache is empty, you still need to send the user through the login process.

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


For Windows Phone apps, you may encrypt and cache data using the [ProtectedData] class and store sensitive information in isolated storage.

-->

##<a name="errors"></a>Fehlerbehandlung

Im folgenden Beispiel wird veranschaulicht, wie Sie eine vom Back-End zurückgegebene Ausnahme behandeln:

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


##<a name="unit-testing"></a>Gewusst wie: Design von Komponententests

Der von `MobileServiceClient.GetTable` zurückgegebene Wert und die Abfragen sind Schnittstellen. Daher lassen sich diese Komponenten leicht zu Testzwecken "nachbilden". Sie könnten z. B. `MyMockTable : IMobileServiceTable<TodoItem>` zur Implementierung Ihrer Testlogik erstellen.

##<a name="customizing"></a>Gewusst wie: Anpassen des Clients

Dieser Abschnitt beschreibt die Möglichkeiten zum Anpassen der Anforderungsheader und der Serialisierung von JSON-Objekten in der Antwort.

### <a name="headers"></a>Gewusst wie: Anpassen der Anforderungsheader

Um Ihr spezielles App-Szenario zu unterstützen, müssen Sie unter Umständen die Kommunikation mit dem Mobile App-Back-End anpassen. Sie können z. B. benutzerdefinierte Header zu allen ausgehenden Anforderungen hinzufügen oder den Statuscode von Antworten ändern. Hierzu müssen Sie einen benutzerdefinierten [DelegatingHandler] bereitstellen, wie im folgenden Beispiel gezeigt:

    public async Task CallClientWithHandler()
    {
        MobileServiceClient client = new MobileServiceClient(
            "AppUrl", "", "",
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
            // Add a custom header to the request.
            request.Headers.Add("x-my-header", "my value");
            var response = await base.SendAsync(request, cancellationToken);
            // Set a differnt response status code.
            response.StatusCode = HttpStatusCode.ServiceUnavailable;
            return response;
        }
    }

Dieser Code fügt der Anforderungen einen neuen **x-my-header**-Header hinzu und legt den Antwortcode willkürlich auf "nicht verfügbar" fest. In einem realen Szenario würden Sie den Statuscode der Antwort entsprechend der benutzerdefinierten Logik festlegen, die für Ihre App erforderlich ist.

### <a name="serialization"></a>Gewusst wie: Anpassen der Serialisierung

Die Mobile Apps-Clientbibliothek verwendet Json.NET, um eine JSON-Antwort auf dem Client in .NET-Objekte zu konvertieren. Sie können das Verhalten dieser Serialisierung von zwischen .NET-Typen und JSON in den Nachrichten konfigurieren. Die [MobileServiceClient]-Klasse macht eine `SerializerSettings`-Eigenschaft vom Typ [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm) verfügbar.

Mit dieser Eigenschaft können Sie eine der zahlreichen Json.NET-Eigenschaften festlegen, wie z. B. Folgende:

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

Mit dieser Eigenschaft werden alle Eigenschaften während der Serialisierung in Kleinbuchstaben konvertiert.

<!-- Anchors. -->
[Zurückgegebene Daten filtern]: #filtering
[Zurückgegebene Daten sortieren]: #sorting
[Daten seitenweise zurückgeben]: #paging
[Bestimmte Spalten auswählen]: #selecting
[Daten nach ID abrufen]: #lookingup

<!-- Images. -->



<!-- URLs. -->
[Add authentication to your app]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md
[Arbeiten Sie mit der Back-End-Server-SDK für Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[LoginAsync method]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI to manage Mobile Services tables]: ../virtual-machines-command-line-tools.md/#Commands_to_manage_mobile_services
[Optimistic Concurrency Tutorial]: mobile-services-windows-store-dotnet-handle-database-conflicts.md
[MobileServiceClient]: http://msdn.microsoft.com/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
[IncludeTotalCount]: http://msdn.microsoft.com/library/windowsazure/dn250560.aspx
[Skip]: http://msdn.microsoft.com/library/windowsazure/dn250573.aspx
[Take]: http://msdn.microsoft.com/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[Benutzerdefinierte API in Azure Mobile Services Client SDKs]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx
[DelegatingHandler]: https://msdn.microsoft.com/library/system.net.http.delegatinghandler(v=vs.110).aspx

<!---HONumber=AcomDC_1203_2015-->