<properties 
	pageTitle="Arbeiten mit der Mobile Services .NET-Clientbibliothek" 
	description="Erfahren Sie mehr über die Verwendung eines .NET-Clients für Azure Mobile Services." 
	services="" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="glenga"/>





# So verwenden Sie einen .NET-Client für mobile Dienste in Azure

<div class="dev-center-tutorial-selector sublanding">
  <a href="/de-de/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework" class="current">.NET Framework</a>
  	<a href="/de-de/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript">HTML/JavaScript</a><a href="/de-de/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/de-de/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/de-de/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>


Dieser Artikel beschreibt gängige Szenarien für die Verwendung eines .NET-Clients für mobile Dienste in Azure, in Windows Store-Apps und Windows Phone-Apps. Besprochen werden unter anderem Datenabfragen, Einfügen, Aktualisieren und Löschen von Daten, Authentifizierung von Benutzern und Fehlerbehandlung. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie möglicherweise zunächst die Lernprogramme "Schnellstart für Mobile Services" ([Windows Store-Schnellstartlernprogramm]/[Windows Phone-Schnellstartlernprogramm])und "Erste Schritte mit Daten in .NET" ([Windows Store-Datenlernprogramm]/[Windows Phone-Datenlernprogramm]) abschließen. Für das Schnellstartlernprogramm benötigen Sie das [SDK für Mobile Services]. Sie lernen dort, Ihr Konto zu konfigurieren und Ihren ersten mobilen Dienst zu erstellen.


## Inhaltsverzeichnis

- [Was sind Mobile Services]
- [Konzepte]
- [Gewusst wie: Erstellen des Mobile Services-Clients]
- [Gewusst wie: Erstellen eines Tabellenverweises]
- [Gewusst wie: Abfragen von Daten aus einem mobilen Dienst]
	- [Filtern zurückgegebener Daten]
    - [Sortieren zurückgegebener Daten]
	- [Seitenweises Zurückgegeben von Daten]
	- [Auswählen bestimmter Spalten]
	- [Abrufen von Daten nach ID]
- [Gewusst wie: Einfügen von Daten in einen mobilen Dienst]
- [Gewusst wie: Ändern von Daten in einem mobilen Dienst]
- [Gewusst wie: Löschen von Daten in einem mobilen Dienst]
- [Gewusst wie: Aufrufen einer benutzerdefinierten API]
- [Gewusst wie: Verwenden von optimistischer Nebenläufigkeit]
- [Gewusst wie: Bindung von Daten an die Benutzeroberfläche in einem mobilen Dienst]
- [Gewusst wie: Authentifizieren von Benutzern]
- [Gewusst wie: ]Fehlerbehandlung
- [Gewusst wie: Arbeiten mit nicht typisierten Daten]
- [Gewusst wie: Design von Komponententests]
- [Gewusst wie: Anpassen des Clients]
	- [Anpassen der Anforderungsheader]
	- [Anpassen der Serialisierung]
- [Nächste Schritte]

[AZURE.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

<h2><a name="setup"></a>Einrichtung und Voraussetzungen</h2>

Wir setzen voraus, dass Sie einen mobilen Dienst und eine Tabelle erstellt haben. Weitere Informationen finden Sie unter [Erstellen einer Tabelle](http://go.microsoft.com/fwlink/?LinkId=298592). Der Code in diesem Artikel verwendet eine Tabelle mit dem Namen `TodoItem` und enthält die folgenden Spalten: `Id`, `Text`, und `Complete`.

Der entsprechende typisierte clientseitige .NET-Typ sieht wie folgt aus:


	public class TodoItem
	{
		public string Id { get; set; }

		[JsonProperty(PropertyName = "text")]
		public string Text { get; set; }

		[JsonProperty(PropertyName = "complete")]
		public bool Complete { get; set; }
	}

Wenn das dynamische Schema aktiviert ist, generiert Azure Mobile Services automatisch anhand des Objekts in der Einfüge- oder Updateanforderung neue Spalten. Weitere Informationen finden Sie unter [Dynamisches Schema](http://go.microsoft.com/fwlink/?LinkId=296271).

<h2><a name="create-client"></a>Gewusst wie: Erstellen des mobile Dienste-Clients</h2>

Der folgende Code erstellt das `MobileServiceClient`-Objekt, das für den Zugriff auf Ihren mobilen Dienst verwendet wird.


	MobileServiceClient client = new MobileServiceClient(
		"AppUrl",
		"AppKey"
	);

Ersetzen Sie im obigen Code `AppUrl` und `AppKey` durch die URL und den Anwendungsschlüssel des mobilen Diensts in dieser Reihenfolge. Beide Werte finden Sie im Azure-Verwaltungsportal, indem Sie Ihren mobilen Dienst auswählen und auf "Dashboard" klicken.

<h2><a name="instantiating"></a>Gewusst wie: Erstellen eines Tabellenverweises</h2>

Jeglicher Code zum Abrufen oder Ändern von Daten in der Mobile Services-Tabelle ruft Funktionen des `MobileServiceTable`-Objekts auf. Sie erhalten einen Verweis auf die Tabelle, indem Sie die [GetTable](http://msdn.microsoft.com/library/windowsazure/jj554275.aspx)-Funktion für eine Instanz von `MobileServiceClient` aufrufen.

    IMobileServiceTable<TodoItem> todoTable =
		client.GetTable<TodoItem>();

Dies ist das typisierte Serialisierungsmodell. Eine Beschreibung des <a href="#untyped">untypisierten Serialisierungsmodells</a> finden Sie weiter unten.

<h2><a name="querying"></a>Gewusst wie: Abfragen von Daten aus einem mobilen Dienst</h2>

Dieser Abschnitt beschreibt, wie Sie Abfragen an Ihren mobilen Dienst stellen können. Die Unterabschnitte beschreiben Aspekte wie z. B. Sortierung, Filterung und Seitenverwaltung.

>[AZURE.NOTE] Standardmäßig wird eine servergesteuerte Seitengröße verwendet, um zu verhindern, dass alle Zeilen zurückgegeben werden. Damit wird verhindert, dass Standardabfragen für große Datensätze den Dienst negativ beeinflussen. Verwenden Sie die `Take`-Methode, um mehr als 50 Zeilen zurückzugeben, wie unter [Seitenweises Zurückgeben von Daten] beschrieben.  

### <a name="filtering"></a>Gewusst wie: Zurückgegebene Daten filtern

Der folgende Code zeigt, wie Sie Daten mithilfe einer `Where`-Klausel in einer Abfrage filtern. Die Abfrage gibt alle Elemente aus `todoTable` zurück, deren `Complete`-Eigenschaft gleich `false` ist. Die `Where`-Funktion wendet ein Zeilenfilterungsprädikat auf die Tabellenabfrage an.

	// This query filters out completed TodoItems and
	// items without a timestamp.
	List<TodoItem> items = await todoTable
	   .Where(todoItem => todoItem.Complete == false)
	   .ToListAsync();

Sie können den URI der an den mobilen Dienst gesendeten Anforderung anzeigen, indem Sie Software zur Überprüfung von Nachrichten verwenden, wie z. B. Browserentwicklertools oder [Fiddler]. Beachten Sie in dem folgenden Abfrage-URI, dass wir die eigentliche Abfragezeichenfolge verändern:

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

Beide Methoden sind äquivalent und können austauschbar verwendet werden.  Die erste Option -- Verketten mehrerer Prädikate in einer Abfrage -- ist kompakter und empfehlenswert.

Die `where`-Klausel unterstützt Vorgänge, die in die OData-Teilmenge für Mobile Services übersetzt werden können. Dazu gehören relationale Operatoren (==, !=, <, <=, >, >=), arithmetische Operatoren (+, -, /, *, %), Zahlengenauigkeit (Math.Floor, Math.Ceiling), Zeichenfolgenfunktionen (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), Datumseigenschaften (Year, Month, Day, Hour, Minute, Second), Zugriffseigenschaften von Objekten und Ausdrücke mit Kombinationen dieser Typen.

### <a name="sorting"></a>Gewusst wie: Zurückgegebene Daten sortieren

Der folgende Code zeigt, wie Sie Daten mithilfe einer `OrderBy`- oder `OrderByDescending`-Funktion in der Abfrage sortieren können. Die Abfrage gibt Elemente aus der Tabelle `todoTable` aufsteigend sortiert nach dem `Text`-Feld zurück.

	// Sort items in ascending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderBy(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

	// Sort items in descending order by Text field
	MobileServiceTableQuery<TodoItem> query = todoTable
					.OrderByDescending(todoItem => todoItem.Text)
 	List<TodoItem> items = await query.ToListAsync();

### <a name="paging"></a>Gewusst wie: Daten seitenweise zurückgeben

Standardmäßig gibt der Server nur die ersten 50 Zeilen zurück. Sie können die [Take]-Methode aufrufen, um die Anzahl der zurückgegebenen Zeilen zu erhöhen. Verwenden Sie `Take` zusammen mit der [Skip]-Methode, um eine bestimmte "Seite" des gesamten Datasets anzufordern, das von der Abfrage zurückgegeben wird. Die folgende Abfrage liefert die ersten drei Elemente aus der Tabelle zurück.

	// Define a filtered query that returns the top 3 ite
	ms.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

Die folgende geänderte Abfrage überspringt die ersten drei Ergebnisse und liefert die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

	// Define a filtered query that skips the top 3 items and returns the next 3 ite
	ms.
	MobileServiceTableQuery<TodoItem> query = todoTable
					.Skip(3)
					.Take(3);
	List<TodoItem> items = await query.ToListAsync();

Mit der [IncludeTotalCount]-Methode können Sie sicherstellen, dass die Abfrage die Gesamtanzahl für <i>alle</i> Datensätze abruft, die bei Ignorieren der angegebenen Take Paging/Limit-Klausel zurückgegeben worden wären.

	query = query.IncludeTotalCount();

In diesem vereinfachten Szenario werden hartcodierte Pagingwerte an die `Take`- und die `Skip`-Methode übergeben. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren.

### <a name="selecting"></a>Gewusst wie: Bestimmte Spalten auswählen

Sie können angeben, welche Eigenschaften in den Ergebnissen enthalten sein sollen, indem Sie der Abfrage eine `Select`-Klausel hinzufügen Der folgende Code zeigt, wie Sie nur ein Feld auswählen oder auch mehrere Felder auswählen und formatieren können:

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

Die `LookupAsync`-Funktion kann verwendet werden, um Objekte mit einer bestimmten ID in der Datenbank abzurufen.

	// This query filters out the item with the ID of 37BBF396-11F0-4B39-85C8-B319C729AF6D
	TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

<a name="inserting"></a>Gewusst wie: Einfügen von Daten in einen mobilen Dienst

> [AZURE.NOTE] Wenn Sie Vorgänge zum Einfügen, Abrufen, Löschen oder Aktualisieren auf einem Typ ausführen möchten, müssen Sie ein **Id**-Element erstellen. Aus diesem Grund hat die Beispielklasse **TodoItem** ein Element mit dem Namen **Id**. Alle Aktualisierungs- und Löschoperationen benötigen eine gültige Id.

Der folgende Code zeigt, wie Sie neue Zeilen in eine Tabelle einfügen können. Der Parameter enthält die einzufügenden Daten als .NET-Objekt.

	await todoTable.InsertAsync(todoItem);

Falls das `todoItem`, das an den `todoTable.InsertAsync`-Aufruf übergeben wird, keinen Wert für die eindeutige benutzerdefinierte ID enthält, generiert der Server einen ID-Wert und legt diesen in dem an den Client zurückgegebenen `todoItem`-Objekt fest.

Mobile Services unterstützen eindeutige benutzerdefinierte Zeichenfolgenwerte als Tabellen-ID. Auf diese Weise können Anwendungen benutzerdefinierte Werte wie z. B. E-Mail-Adressen oder Benutzernamen in der ID-Spalte einer Tabelle für mobile Dienste verwenden. Falls beim Einfügen neuer Datensätze in eine Tabelle kein Wert für die Zeichenfolgen-ID angegeben wird, wird ein eindeutiger ID-Wert von Mobile Services generiert.

Die Möglichkeit zum Verwenden von Zeichenfolgen-Ids bietet Entwicklern die folgenden Vorzüge

+ Ids können ohne Roundtrip zur Datenbank generiert werden.
+ Datensätze aus unterschiedlichen Tabellen oder Datenbanken lassen sich leichter zusammenführen.
+ Id-Werte lassen sich möglicherweise leichter in die Anwendungslogik integrieren.

Sie können auch Serverskripts verwenden, um die Id-Werte zu setzen. Das folgende Skript generiert eine benutzerdefinierte GUID und verwendet diese als Id für einen neuen Eintrag. Dieser Wert ähnelt dem Id-Wert, den der mobile Dienst generieren würde, wenn in der Abfrage kein Id-Wert übergeben wird.

	//Example of generating an id. This is not required since Mobile Services
	//will generate an id if one is not passed in.
	item.id = item.id || newGuid();
	request.execute();

	function newGuid() {
		var pad4 = function(str) { return "0000".substring(str.length) + str; };
		var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
		return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
	}


Wenn eine Anwendung einen Id-Wert übergibt, speichert der mobile Dienst diesen Wert unverändert. Dies beinhaltet auch vor- und nachstehende Leerzeichen. Leerzeichen werden aus dem Wert gekürzt.

Der `id`-Wert muss eindeutig sein und darf keine Zeichen aus den folgenden Sätzen enthalten:

+ Steuerzeichen: [0x0000-0x001F] und [0x007F-0x009F]. Weitere Informationen finden Sie unter [ASCII-Steuerzeichen C0 und C1].
+  Druckbare Zeichen: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  Die IDs "." und ".."


Alternativ können Sie auch ganzzahlige Ids für Ihre Tabellen verwenden. Um ganzzahlige Ids zu verwenden, müssen Sie bei der Tabellenerstellung für den `mobile table create`-Befehl die "--integerId"-Option verwenden. Dieser Befehl wird in der Befehlszeilenschnittstelle (CLI) für Azure verwendet. Weitere Informationen zur CLI finden Sie unter [CLI zum Verwalten von Tabellen in Mobile Services].


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


<h2><a name="modifying"></a>Gewusst wie: Ändern von Daten in einem mobilen Dienst</h2>

Der folgende Code zeigt, wie Sie eine existierende Instanz mit derselben Id und neuen Daten aktualisieren können. Der Parameter enthält die zu aktualisierenden Daten als .NET-Objekt.

	await todoTable.UpdateAsync(todoItem);


Zum Einfügen von untypisierten Daten können Sie Json.NET verwenden, wie hier gezeigt. Beachten Sie, dass Sie bei Änderungen eine Id angeben müssen, damit der mobile Dienst die zu aktualisierende Instanz identifizieren kann. Sie finden die ID im Ergebnis des `InsertAsync`-Aufrufs.

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	jo.Add("Text", "Hello World");
	jo.Add("Complete", false);
	var inserted = await table.UpdateAsync(jo);

Wenn Sie versuchen, ein Element ohne Angabe des "ID"-Werts zu aktualisieren, hat der Dienst keine Möglichkeit, die korrekte Instanz zu identifizieren, und das Mobile Services SDK löst `ArgumentException` aus.


<h2><a name="deleting"></a>Gewusst wie: Löschen von Daten in einem mobilen Dienst</h2>

Der folgende Code zeigt, wie Sie existierende Instanzen löschen können. Die Instanz wird durch das in `todoItem` festgelegte "Id"-Feld identifiziert.

	await todoTable.DeleteAsync(todoItem);

Zum Löschen von untypisierten Daten können Sie Json.NET verwenden, wie hier gezeigt. Beachten Sie, dass Sie bei Löschungen eine Id angeben müssen, damit der mobile Dienst die zu löschende Instanz identifizieren kann. Eine Löschanfrage benötigt nur die Id. Sonstige Eigenschaften werden nicht an den Dienst übergeben und eventuell vorhandene Eigenschaften werden vom Dienst ignoriert. Das Ergebnis eines `DeleteAsync` -Aufrufs ist üblicherweise auch `null`. Sie erhalten die zu übergebende ID im Ergebnis des `InsertAsync`-Aufrufs.

	JObject jo = new JObject();
	jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
	await table.DeleteAsync(jo);

Wenn Sie versuchen, ein Element zu löschen, ohne dass das "Id"-Feld bereits festgelegt ist, hat der Dienst keine Möglichkeit, die korrekte Instanz zu identifizieren, und der Dienst gibt `MobileServiceInvalidOperationException` zurück. Auch wenn Sie versuchen, ein nicht typisiertes Element zu löschen, ohne dass das "Id"-Feld bereits festgelegt ist, gibt der Dienst ebenfalls `MobileServiceInvalidOperationException` zurück.

##<a name="#custom-api"></a>Gewusst wie: Aufrufen einer benutzerdefinierten API

Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats. Ein vollständiges Beispiel, in dem u. a. das Erstellen einer benutzerdefinierten API im mobilen Dienst beschrieben wird, finden Sie unter [Aufrufen einer benutzerdefinierten API aus dem Client].

Sie rufen eine benutzerdefinierte API auf, indem Sie eine der [InvokeApiAsync]-Methodenüberladungen für den Client aufrufen. Mit der folgenden Codezeile wird beispielsweise eine POST-Anforderung an die **completeAll**-API im mobilen Dienst gesendet:

    var result = await App.MobileService
        .InvokeApiAsync<MarkAllResult>("completeAll",
        System.Net.Http.HttpMethod.Post, null);

Beachten Sie, dass dies ein typisierter Methodenaufruf ist, der erfordert, dass der **MarkAllResult**-Rückgabetyp definiert ist. Typisierte und nicht typisierte Methoden werden unterstützt. Dieses Beispiel ist fast zu einfach, da es typisiert ist und keine Abfrageparameter enthält und weil keine Nutzlast gesendet und keine Anforderungsheader geändert werden. Beispiele mit mehr Praxisnähe und eine ausführlichere Erörterung von [InvokeApiAsync] finden Sie unter [Benutzerdefinierte API in Azure Mobile Services Client SDKs].


##<a name="optimisticconcurrency"></a>Gewusst wie: Verwenden von optimistischer Nebenläufigkeit.

In manchen Szenarien können zwei oder mehr Clients gleichzeitig versuchen, dasselbe Element zu bearbeiten. Ohne Konflikterkennung würde der letzte Schreibvorgang alle vorherigen Aktualisierungen überschreiben, selbst wenn dies nicht so gewollt wäre. Die Steuerung für optimistische Nebenläufigkeit setzt voraus, dass jede Transaktion Commits ausführen kann, und sperrt daher keine Ressourcen. Vor dem Commit einer Transaktion prüft die Steuerung für optimistische Nebenläufigkeit, ob die Daten von einer anderen Transaktion geändert wurden. Falls die Daten geändert wurden, wird für die Transaktion, die den Commit durchführen sollte, ein Rollback durchgeführt.

Mobile Services unterstützen die Steuerung für optimistische Parallelität, indem Änderungen an Elementen in der "__version"-Systemeigenschaftenspalte registriert werden, die in allen von Mobile Services erstellten Tabellen existiert. Bei jeder Änderung an einem Element setzt Mobile Services einen neuen Wert für die "__version"-Eigenschaft des entsprechenden Elements fest. Bei jeder Änderungsanfrage wird die "__version"-Eigenschaft des entsprechenden Eintrags mit der Eigenschaft des Eintrags auf dem Server verglichen. Wenn die mit der Anforderung übergebene Version nicht mit dem Server übereinstimmt, löst die Mobile Services .NET-Clientbibliothek `MobileServicePreconditionFailedException<T>` aus. Der in der Ausnahme enthaltene Typ ist der Eintrag vom Server mit der Serverversion des Eintrags. Anschließend kann die Anwendung anhand dieser Informationen entscheiden, ob die Änderungsanfrage erneut mit dem korrekten "__version"-Wert vom Server geschickt werden soll, um den Commit für die Änderungen auszuführen.  

Die Anwendung definiert eine Spalte in der Tabellenklasse für die "__version"-Systemeigenschaft, um die Kontrolle für optimistische Nebenläufigkeit zu aktivieren. Die folgende Definition zeigt ein Beispiel.

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


Der folgende Code zeigt, wie ein erkannter Schreibkonflikt gelöst werden kann. Der korrekte "__version"-Wert muss im `UpdateAsync()`-Aufruf angegeben werden, um einen Commit für die Auflösung auszuführen.

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
	    MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n",
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


Ein umfassenderes Beispiel für optimistische Nebenläufigkeit in Mobile Services finden Sie unter [Lernprogramm für optimistische Nebenläufigkeit].


<h2><a name="binding"></a>Gewusst wie: Bindung von Daten an die Benutzeroberfläche in einem mobilen Dienst</h2>

Dieser Abschnitt beschreibt das Anzeigen von Datenobjekten in GUI-Elementen. Für die Abfrage unvollständiger Elemente aus `todoTable` und deren Anzeige in einer sehr einfachen Liste können Sie den folgenden Beispielcode ausführen, um die Quelle der Liste an eine Abfrage zu binden. Mit `MobileServiceCollection` wird eine Bindungssammlung erstellt, die einen mobilen Dienst unterstützt.

	// This query filters out completed TodoIte
	ms.
	MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
		.Where(todoItem => todoItem.Complete == false)
		.ToCollectionAsync();

	// itemsControl is an IEnumerable that could be bound to a UI list control
	IEnumerable itemsControl  = items;

	// Bind this to a ListBox
	ListBox lb = new ListBox();
	lb.ItemsSource = items;

Manche Steuerelemente in der Windows-Runtime unterstützen eine Schnittstelle namens [ISupportIncrementalLoading](http://msdn.microsoft.com/library/windows/apps/Hh701916). Mit dieser Schnittstelle können Steuerelemente weitere Daten anfordern, wenn ein Benutzer blättert.  `MobileServiceIncrementalLoadingCollection` bietet integrierte Unterstützung für diese Schnittstelle für Windows Store-Apps und verarbeitet die Aufrufe dieser Steuerelemente automatisch. Gehen Sie wie folgt vor, um  `MobileServiceIncrementalLoadingCollection` in Windows Store-Apps zu verwenden:

			MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
		items =  todoTable.Where(todoItem => todoItem.Complete == false)
					.ToIncrementalLoadingCollection();

		ListBox lb = new ListBox();
		lb.ItemsSource = items;


Um die neue Sammlung in Windows Phone zu nutzen, verwenden Sie die `ToCollection`-Erweiterungsmethoden für `IMobileServiceTableQuery<T>` und `IMobileServiceTable<T>`. Rufen Sie `LoadMoreItemsAsync()` auf, um Daten zu laden.

	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection();
	await ite
	ms.LoadMoreItemsAsync();

Die Sammlung, die durch den Aufruf von `ToCollectionAsync` bzw. `ToCollection`, erstellt wurde, kann an Benutzeroberflächen-Steuerelemente gebunden werden. Diese Sammlung unterstützt Seitenverwaltung, d. h. ein Steuerelement kann die Sammlung anweisen, "weitere Elemente zu laden", und die Sammlung erledigt dies für das Steuerelement. Bis zu diesem Punkt wird noch kein Benutzercode ausgeführt, das Steuerelement startet den Fluss. Da die Sammlung jedoch Daten aus dem Netzwerk lädt, ist zu erwarten, dass dieser Ladevorgang manchmal fehlschlägt. Zur Behandlung solcher Fehler können Sie die `OnException`-Methode für `MobileServiceIncrementalLoadingCollection` überschreiben, um Ausnahmen zu behandeln, die aus Aufrufen von `LoadMoreItemsAsync` durch Steuerelemente entstehen.

Stellen Sie sich vor, Ihre Tabelle hat viele Felder, aber Sie möchten nur einen Teil der Felder in Ihrem Steuerelement anzeigen. Folgen Sie der Anleitung unter <a href="#selecting">"Auswählen bestimmter Spalten"</a> weiter oben, um bestimmte Spalten für die Anzeige in der Benutzeroberfläche auszuwählen.

<h2><a name="authentication"></a>Gewusst wie: Authentifizieren von Benutzern</h2>

Mobile Dienste unterstützen Authentifizierung und Autorisierung von Anwendungsbenutzern mit einer Vielzahl externer Identitätsanbieter: Facebook, Google, Microsoft Account, Twitter und Azure Active Directory. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln in Serverskripts zu implementieren. Weitere Informationen finden Sie im Lernprogramm "Erste Schritte mit Authentifizierung" ([Windows Store][Windows Store-Authentifizierung]/[Windows Phone][Windows Phone-Authentifizierung])

Insgesamt werden zwei Authentifizierungsflüsse unterstützt: ein "Serverfluss" und ein "Clientfluss". Der Serverfluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Authentifizierungs-Webschnittstelle des Anbieters verwendet wird. Der Clientfluss ermöglicht eine tiefere Integration mit gerätespezifischen Fähigkeiten, da in diesem Fall anbieterspezifische und gerätespezifische SDKs verwendet werden.

<h3>Serverfluss</h3>
Zum Verwalten des Authentifizierungsvorgangs durch Mobile Services in Ihrer Windows Store- oder Windows Phone-App
müssen Sie die App mit Ihrem Identitätsanbieter registrieren. Anschließend müssen Sie in Ihrem mobilen Dienst die Anwendungs-ID und den geheimen Schlüssel Ihres Anbieters konfigurieren. Weitere Informationen finden Sie im Lernprogramm "Erste Schritte mit Authentifizierung" ([Windows Store][Windows Store-Authentifizierung]/[Windows Phone][Windows Phone-Authentifizierung]).

Nach der Registrierung bei Ihrem Identitätsanbieter können Sie die [LoginAsync-Methode] mit dem [MobileServiceAuthenticationProvider]-Wert Ihres Anbieters aufrufen. Der folgende Beispielcode startet eine Serverfluss-Anmeldung für Facebook.

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

In diesem Fall verwaltet Mobile Services den OAuth 2.0-Authentifizierungsfluss, indem die Anmeldungsseite des ausgewählten Anbieters angezeigt und nach der erfolgreichen Anmeldung beim Identitätsanbieter ein Authentifizierungstoken für Mobile Services generiert wird. Die [LoginAsync-Methode] gibt einen [MobileServiceUser] zurück, der wiederum sowohl [userId] des authentifizierten Benutzers als auch das [MobileServiceAuthenticationToken], in Form eines JSON-Webtokens (JWT) enthält. Dieses Token kann zwischen gespeichert und wiederverwendet werden, bis es abläuft. Weitere Informationen finden Sie unter [Zwischenspeichern des Authentifizierungstokens].

> [AZURE.NOTE] **Windows Store-App**
Wenn Sie den Anmeldeanbieter für Microsoft-Konten zum Authentifizieren von Benutzern Ihrer Windows Store-App verwenden, sollten Sie ebenfalls das App-Paket im mobilen Dienst registrieren. Wenn Sie Ihre Windows Store-App-Paketinformationen bei Mobile Services registrieren, kann der Client die Anmeldeinformationen für Microsoft Account für eine einmalige Anmeldung verwenden. Wenn Sie dies nicht tun, werden Ihre Benutzer mit Microsoft Account-Login jedes Mal zur Anmeldung aufgefordert, wenn diese Anmeldemethode aufgerufen wird. Informationen zur Registrierung Ihres Windows Store-App-Pakets finden Sie unter [Registrieren Ihres Windows Store-App-Pakets für die Microsoft-Authentifizierung](/de-de/develop/mobile/how-to-guides/register-windows-store-app-package/%20target="_blank"). Nach der Registrierung Ihres Pakets in Mobile Services können Sie die [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594%20target="_blank")-Methode aufrufen und den Wert **true** für den _useSingleSignOn_-Parameter übergeben, um die Anmeldeinformationen wiederzuverwenden.

<h3>Clientfluss</h3>

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

Ein Beispiel für einmaliges Anmelden mit Microsoft-Konten finden Sie im Lernprogramm "Authentifizieren Ihrer App mit einmaligem Anmelden" ([Windows Store](/de-de/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/de-de/develop/mobile/tutorials/single-sign-on-wp8/)).

<h3><a name="caching"></a>Zwischenspeichern des Authentifizierungstokens</h3>
Manche Aufrufe der Anmeldemethode lassen sich vermeiden, nachdem sich der Benutzer authentifiziert hat. Sie können den [PasswordVault] für Windows Store-Apps verwenden, um die Identität des aktuellen Benutzers bei der ersten Anmeldung zwischenzuspeichern, und später bei Bedarf prüfen, ob Sie die Benutzeridentität bereits im Speicher haben. Wenn der Zwischenspeicher leer ist, muss der Benutzer den Anmeldeprozess durchlaufen.

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

<h2><a name="errors"></a>Gewusst wie: Fehlerbehandlung</h2>

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
			ite
	ms.Add(todoItem);
		}
		catch (MobileServiceInvalidOperationException e)
		{
			// Handle error
		}
	}

<h2><a name="untyped"></a>Gewusst wie: Arbeiten mit nicht typisierten Daten</h2>

Der .NET-Client wurde für stark typisierte Szenarien entwickelt. Manchmal macht jedoch eine weniger starke Typisierung Sinn, z. B. beim Umgang mit Objekten mit offenem Schema. Dieses Szenario funktioniert wie folgt. In Ihren Abfragen verwenden Sie das Übertragungsformat anstelle von LINQ.

	// Get an untyped table reference
	IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");

	// Lookup untyped data using OData
	JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Sie erhalten JSON-Werte zurück, die Sie wie einen Eigenschaftenbehälter verwenden können. Weitere Informationen zu JToken and Json.NET finden Sie unter [Json.NET](http://json.codeplex.com/)

<h2><a name="unit-testing"></a>Gewusst wie: Design von Komponententests</h2>

Der von `MobileServiceClient.GetTable` zurückgegebene Wert und die Abfragen sind Schnittstellen. Daher lassen sich diese Komponenten leicht zu Testzwecken nachbilden. Sie könnten z. B. `MyMockTable : IMobileServiceTable<TodoItem>` zur Implementierung Ihrer Testlogik erstellen.

<h2><a name="customizing"></a>Gewusst wie: Anpassen des Clients</h2>

### <a name="headers"></a>Gewusst wie: Anpassen der Anforderungsheader

Sie können z. B. benutzerdefinierte Header zu allen ausgehenden Anforderungen hinzufügen oder den Statuscode von Antworten ändern. Konfigurieren Sie dazu einen DelegatingHandler, wie hier gezeigt:

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


### <a name="serialization"></a>Gewusst wie: Anpassen der Serialisierung

Die [MobileServiceClient](http://msdn.microsoft.com/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx)-Klasse macht eine `SerializerSettings`-Eigenschaft vom Typ [JsonSerializerSettings](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm) verfügbar.

Mit dieser Eigenschaft können Sie Json.NET-Eigenschaften setzen (von denen es viele gibt), inklusive einer Eigenschaft, die alle Eigenschaften in Kleinbuchstaben umwandelt:

	var settings = new JsonSerializerSettings();
	settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
	client.SerializerSettings = settings;

<h2><a name="nextsteps"></a>Nächste Schritte</h2>

Sie haben das konzeptuelle Referenzthema abgeschlossen und können sich nun wichtigen Aufgaben in mobilen Diensten im Detail widmen:

* [Erste Schritte mit Mobile Services]
  <br/>Erfahren Sie die Grundlagen zur Verwendung von Mobile Services.

* [Erste Schritte mit Daten]
  <br/>Informationen über das Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Authentifizierung]
  <br/>Informationen über die Authentifizierung von Benutzern der App mit einem Identitätsanbieter.

* [Überprüfen und Ändern von Daten mit Skripts]
  <br/>Erfahren Sie mehr über die Verwendung von Serverskripts in Mobile Services, um die von Ihrer App gesendeten Daten zu prüfen und zu ändern.

* [Optimieren von Abfragen mit Paging]
  <br/>Erfahren Sie, wie Sie Paging in Abfragen zum Steuern der in einer einzelnen Anforderung verarbeiteten Datenmengen verwenden können.

* [Autorisieren von Benutzern mit Skripts]
  <br/>Verwenden Sie die vom mobilen Dienst für einen authentifizierten Benutzer gelieferte Benutzer-ID zum Filtern von zurückgegebenen Daten.

<!-- Anchors. -->
[Mobile Services]: #what-is
[Konzepte]: #concepts
[Gewusst wie: Erstellen des Mobile Services-Clients]: #create-client
[Gewusst wie: Erstellen eines Tabellenverweises]: #instantiating
[Gewusst wie: Abfragen von Daten aus einem mobilen Dienst]: #querying
[Filtern zurückgegebener Daten]: #filtering
[Sortieren zurückgegebener Daten]: #sorting
[Seitenweises Zurückgegeben von Daten]: #paging
[Auswählen bestimmter Spalten]: #selecting
[Abrufen von Daten nach ID]: #lookingup
[Gewusst wie: Bindung von Daten an die Benutzeroberfläche in einem mobilen Dienst]: #binding
[Gewusst wie: Einfügen von Daten in einen mobilen Dienst]: #inserting
[Gewusst wie: Ändern von Daten in einem mobilen Dienst]: #modifying
[Gewusst wie: Löschen von Daten in einem mobilen Dienst]: #deleting
[Gewusst wie: Verwenden von optimistischer Nebenläufigkeit]: #optimisticconcurrency
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
[Gewusst wie: Aufrufen einer benutzerdefinierten API]: #custom-api

<!-- Images. -->



<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Windows Store Schnellstart-Lernprogramm]: http://azure.microsoft.com/develop/mobile/tutorials/get-started/
[Windows Phone Schnellstart-Lernprogramm]: http://azure.microsoft.com/develop/mobile/tutorials/get-started-wp8/
[Lernprogramm für Windows Store-Daten]: http://azure.microsoft.com/develop/mobile/tutorials/get-started-with-data-dotnet/
[Lernprogramm für Windows Phone-Daten]: http://azure.microsoft.com/develop/mobile/tutorials/get-started-with-data-wp8/
[Windows Store-Authentifizierung]: http://azure.microsoft.com/develop/mobile/tutorials/get-started-with-users-dotnet/
[Windows Phone-Authentifizierung]: http://azure.microsoft.com/develop/mobile/tutorials/get-started-with-users-wp8/
[PasswordVault]: http://msdn.microsoft.com/library/windows/apps/windows.security.credentials.passwordvault.aspx
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[ProtectedData]: http://msdn.microsoft.com/library/system.security.cryptography.protecteddata%28VS.95%29.aspx
[Mobile Services SDK]: http://nuget.org/packages/WindowsAzure.MobileServices/
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-dotnet/
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-dotnet
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[LoginAsync-Methode]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx
[MobileServiceAuthenticationProvider]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx
[MobileServiceUser]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx
[UserID]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx
[MobileServiceAuthenticationToken]: http://msdn.microsoft.com/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx
[ASCII-Steuerungscodes C0 und C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI zum Verwalten von Mobile Services-Tabellen]: http://azure.microsoft.com/manage/linux/other-resources/command-line-tools/#Mobile_Tables
[Lernprogramm für optimistische Nebenläufigkeit]: http://azure.microsoft.com/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/

[IncludeTotalCount]: http://msdn.microsoft.com/library/windowsazure/dn250560.aspx
[Überspringen]: http://msdn.microsoft.com/library/windowsazure/dn250573.aspx
[Verwenden]: http://msdn.microsoft.com/library/windowsazure/dn250574.aspx
[Fiddler]: http://www.telerik.com/fiddler
[Benutzerdefinierte API in Azure Mobile Services Client SDKs]: http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx
[Aufrufen einer benutzerdefinierten API aus dem Client]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api/
[InvokeApiAsync]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.invokeapiasync.aspx



<!--HONumber=42-->
