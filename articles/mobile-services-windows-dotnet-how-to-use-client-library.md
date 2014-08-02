<properties linkid="obile-services-how-to-dotnet-client" urlDisplayName=".NET Client Library" pageTitle="Working with the Mobile Services .NET Client Library" metaKeywords="Azure Mobile Services, Mobile Service .NET client, .NET client" description="Learn how to use an .NET client for Azure Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="How to use a .NET client for Azure Mobile Services" authors="krisragh" solutions="" manager="" editor="" />

So verwenden Sie einen .NET-Client für mobile Dienste in Azure
==============================================================

[.NET Framework](/en-us/develop/mobile/how-to-guides/work-with-net-client-library/ ".NET Framework") [HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/ "HTML/JavaScript")[iOS](/en-us/develop/mobile/how-to-guides/work-with-ios-client-library/ "iOS")[Android](/en-us/develop/mobile/how-to-guides/work-with-android-client-library/ "Android")[Xamarin](/en-us/develop/mobile/how-to-guides/work-with-xamarin-client-library/ "Xamarin")

Dieser Artikel beschreibt gängige Szenarien für die Verwendung eines .NET-Clients für mobile Dienste in Azure, in Windows Store-Apps und Windows Phone-Apps. Besprochen werden unter anderem Datenabfragen, Einfügen, Aktualisieren und Löschen von Daten, Authentifizierung von Benutzern und Fehlerbehandlung. Falls Sie keine Erfahrung mit mobilen Diensten haben, sollten Sie möglicherweise zunächst die Lernprogramme "Schnellstart für mobile Dienste" ([Windows Store Schnellstart-Lernprogramm](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started/)/[Windows Phone Schnellstart-Lernprogramm](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-wp8/)) und "Erste Schritte mit Daten in .NET" ([Windows Store Daten-Lernprogramm](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/)/[Windows Phone Daten-Lernprogramm](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-data-wp8/)). Für das Schnellstart-Lernprogramm benötigen Sie das [SDK für mobile Dienste](http://nuget.org/packages/WindowsAzure.MobileServices/). Sie lernen dort, Ihr Konto zu konfigurieren und Ihren ersten mobilen Dienst zu erstellen.

Inhaltsverzeichnis
------------------

-   [Windows Azure Mobile Services](#what-is)
-   [Konzepte](#concepts)
-   [Vorgehensweise: Erstellen des mobile Dienste-Clients](#create-client)
-   [Vorgehensweise: Erstellen eines Tabellenverweises](#instantiating)
-   [Vorgehensweise: Abfragen von Daten aus einem mobilen Dienst](#querying)
    -   [Zurückgegebene Daten filtern](#filtering)
    -   [Zurückgegebene Daten sortieren](#sorting)
    -   [Daten seitenweise zurückgeben](#paging)
    -   [Bestimmte Spalten auswählen](#selecting)
    -   [Daten nach ID abrufen](#lookingup)
-   [Vorgehensweise: Einfügen von Daten in einen mobilen Dienst](#inserting)
-   [Vorgehensweise: Ändern von Daten in einem mobilen Dienst](#modifying)
-   [Vorgehensweise: Löschen von Daten in einem mobilen Dienst](#deleting)
-   [Vorgehensweise: Verwenden von optimistischer Parallelität.](#optimisticconcurrency)
-   [Vorgehensweise: Bindung von Daten an die Benutzeroberfläche in einem mobilen Dienst](#binding)
-   [Vorgehensweise: Authentifizieren von Benutzern](#authentication)
-   [Vorgehensweise: Fehlerbehandlung](#errors)
-   [Vorgehensweise: Arbeiten mit nicht typisierten Daten](#untyped)
-   [Vorgehensweise: Design von Komponententests](#unit-testing)
-   [Vorgehensweise: Anpassen des Clients](#customizing)
    -   [Anpassen der Anforderungsheader](#headers)
    -   [Anpassen der Serialisierung](#serialization)
-   [Nächste Schritte](#nextsteps)

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

EinrichtungEinrichtung und Voraussetzungen
------------------------------------------

Wir setzen voraus, dass Sie einen mobilen Dienst und eine Tabelle erstellt haben. Weitere Informationen finden Sie unter [Erstellen einer Tabelle](http://go.microsoft.com/fwlink/?LinkId=298592). Der Code in diesem Artikel verwendet eine Tabelle mit dem Namen `TodoItem` und den folgenden Spalten: `Id`, `Text` und `Complete`.

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

Erstellen des mobile Dienste-ClientsVorgehensweise: Erstellen des mobile Dienste-Clients
----------------------------------------------------------------------------------------

Der folgende Code erstellt das `MobileServiceClient`-Objekt, das für den Zugriff auf Ihren mobilen Dienst verwendet wird.

    MobileServiceClient client = new MobileServiceClient( 
        "AppUrl", 
        "AppKey" 
    ); 

Ersetzen Sie im obigen Code `AppUrl` und `AppKey` mit der URL und dem Anwendungsschlüssel Ihres mobilen Dienstes, in dieser Reihenfolge. Beide Werte finden Sie im Azure-Verwaltungsportal, indem Sie Ihren mobilen Dienst auswählen und auf "Dashboard" klicken.

Erstellen eines TabellenverweisesVorgehensweise: Erstellen eines Tabellenverweises
----------------------------------------------------------------------------------

Jeglicher Code zum Abrufen oder Ändern von Daten in der Tabelle des mobilen Dienstes ruft Funktionen des `MobileServiceTable`-Objekts auf. Sie erhalten einen Verweis auf die Tabelle, indem Sie die [GetTable](http://msdn.microsoft.com/en-us/library/windowsazure/jj554275.aspx)-Funktion einer Instanz von `MobileServiceClient` aufrufen.

    IMobileServiceTable<TodoItem> todoTable = 
        client.GetTable<TodoItem>();

Dies ist das typisierte Serialisierungsmodell. Eine Besprechung des [untypisierten Serialisierungsmodells](#untyped) finden Sie weiter unten.

Abfragen von DatenVorgehensweise: Abfragen von Daten aus einem mobilen Dienst
-----------------------------------------------------------------------------

Dieser Abschnitt beschreibt, wie Sie Abfragen an Ihren mobilen Dienst stellen können. Die Unterabschnitte beschreiben Aspekte wie z. B. Sortierung, Filterung und Seitenverwaltung.

> [WACOM.NOTE] Standardmäßig wird eine servergesteuerte Seitengröße verwendet, um zu verhindern, dass alle Zeilen zurückgegeben werden. Damit wird verhindert, dass Standardabfragen für große Datensätze den Dienst negativ beeinflussen. Verwenden Sie die `Take`-Methode, um mehr als 50 Zeilen zurückzugeben, wie beschrieben in [Daten seitenweise zurückgeben](#paging).

### Vorgehensweise: Zurückgegebene Daten filtern

Der folgende Code zeigt, wie Sie Daten mithilfe einer `Where`-Klausel in einer Abfrage filtern können. Die Abfrage gibt alle Elemente aus `todoTable` zurück, deren `Complete`-Eigenschaft gleich `false` ist. Die `Where`-Funktion wendet ein Zeilenfilterungsprädikat auf die Tabellenabfrage an.

    // Diese Abfrage filtert alle abgeschlossenen TodoItems-Einträge
    // und Einträge ohne Zeitstempel heraus. 
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .ToListAsync();

Sie können die URI der Anfrage an den mobilen Dienst anzeigen, indem Sie Tools zur Nachrichteninspektion verwenden, wie z. B. Browser-Entwicklertools oder Fiddler. Beachten Sie in der folgenden URI, dass wir die eigentliche Abfragezeichenfolge verändern:

    GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1                   

Diese Anfrage entspricht normalerweise in etwa der folgenden SQL-Abfrage auf der Serverseite:

    SELECT * 
    FROM TodoItem           
    WHERE ISNULL(complete, 0) = 0

Die an die `Where`-Methode übergebene Funktion kann beliebig viele Bedingungen enthalten. Die folgende Zeile:

    // Diese Abfrage filtert abgeschlossene TodoItems heraus, deren Text nicht null ist
    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false
           && todoItem.Text != null)
       .ToListAsync();

Lässt sich (für die bereits gezeigte Abfrage) in etwa wie folgt übersetzen:

    SELECT * 
    FROM TodoItem 
    WHERE ISNULL(complete, 0) = 0
          AND ISNULL(text, 0) = 0

Die obige `where`-Anweisung findet Elemente, deren `Complete`-Status gleich false ist und deren `Text` nicht null ist.

Wir hätten denselben Code auch in mehreren Zeilen schreiben können:

    List<TodoItem> items = await todoTable
       .Where(todoItem => todoItem.Complete == false)
       .Where(todoItem => todoItem.Text != null)
       .ToListAsync();

Beide Methoden sind äquivalent und können frei austauschbar verwendet werden. Die erste Option -- Verketten mehrerer Prädikate in einer Abfrage -- ist kompakter und empfehlenswert.

Die `where`-Klausel unterstützt Operationen, die in die OData-Teilmenge für mobile Dienste übersetzt werden können. Dazu gehören relationale Operatoren (==, !=, &lt;, &lt;=, \>, \>=), arithmetische Operatoren (+, -, /, \*, %), Zahlengenauigkeit (Math.Floor, Math.Ceiling), Zeichenfolgenfunktionen (Length, Substring, Replace, IndexOf, StartsWith, EndsWith), Datumseigenschaften (Year, Month, Day, Hour, Minute, Second), Zugriffseigenschaften von Objekten und Ausdrücke mit Kombinationen dieser Typen.

### Vorgehensweise: Zurückgegebene Daten sortieren

Der folgende Code zeigt, wie Sie Daten mithilfe einer `OrderBy`- oder einer `OrderByDescending`-Klausel in einer Abfrage sortieren können. Die Abfrage liefert Elemente aus der Tabelle `todoTable` aufsteigend sortiert nach dem `Text`-Feld zurück.

    // Elemente aufsteigend nach Text-Feld sortieren
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)   
	List items = await query.ToListAsync();

    // Elemente absteigend nach Text-Feld sortieren
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)       
	List items = await query.ToListAsync();

### Vorgehensweise: Daten seitenweise zurückgeben

Standardmäßig gibt der Server nur die ersten 50 Zeilen zurück. Sie können die [Take](http://msdn.microsoft.com/en-us/library/windowsazure/dn250574.aspx)-Methode aufrufen, um die Anzahl der zurückgegebenen Zeilen zu erhöhen. Verwenden Sie `Take` gemeinsam mit [Skip](http://msdn.microsoft.com/en-us/library/windowsazure/dn250573.aspx), um eine bestimmte "Seite" des gesamten Datensatzes der Abfrage anzufordern. Die folgende Abfrage liefert die ersten drei Elemente aus der Tabelle zurück.

    // Diese gefilterte Abfrage gibt die ersten 3 Elemente zurück.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Take(3);                              
    List<TodoItem> items = await query.ToListAsync();

Die folgende geänderte Abfrage überspringt die ersten drei Ergebnisse und liefert die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

    // Diese gefilterte Abfrage überspringt die ersten 3 Elemente und gibt die nächsten 3 Elemente zurück.
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Skip(3)
                    .Take(3);                              
    List<TodoItem> items = await query.ToListAsync();

Mit der [IncludeTotalCount](http://msdn.microsoft.com/en-us/library/windowsazure/dn250560.aspx)-Methode können Sie sicherstellen, dass die Abfrage die Gesamtanzahl für *alle* Datensätze abruft, die bei Ignorieren der angegebenen Take Paging/Limit-Klausel zurückgegeben worden wären.

    query = query.IncludeTotalCount();

Dieses vereinfachte Szenario übergibt fest codierte Werte an die `Take`- und `Skip`-Methoden. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren.

### Vorgehensweise: Bestimmte Spalten auswählen

Sie können angeben, welche Eigenschaften im Ergebnis enthalten sein sollen, indem Sie eine `Select`-Klausel zu Ihrer Abfrage hinzufügen. Der folgende Code zeigt, wie Sie nur ein Feld auswählen oder auch mehrere Felder auswählen und formatieren können:

    // Ein Feld auswählen -- nur Text
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .Select(todoItem => todoItem.Text);
    List<string> items = await query.ToListAsync();

    // Mehrere Felder auswählen -- Complete und Text-Info
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

### Vorgehensweise: Daten nach ID abrufen

Die `LookupAsync`-Funktion kann verwendet werden, um Objekte mit einer bestimmten ID aus der Datenbank abzufragen.

    // Diese Abfrage filtert das Element mit der ID 37BBF396-11F0-4B39-85C8-B319C729AF6D heraus
    TodoItem item = await todoTable.LookupAsync("37BBF396-11F0-4B39-85C8-B319C729AF6D");

Einfügen von DatenVorgehensweise: Einfügen von Daten in einen mobilen Dienst
----------------------------------------------------------------------------

**Hinweis**

Wenn Sie Operationen zum Einfügen, Nachschlagen, Löschen oder Aktualisieren auf einem Typ ausführen möchten, müssen Sie das **Id**-Element aufrufen. Aus diesem Grund hat die Beispielklasse **TodoItem** ein Element mit dem Namen **Id**. Alle Aktualisierungs- und Löschoperationen benötigen eine gültige Id.

Der folgende Code zeigt, wie Sie neue Zeilen in eine Tabelle einfügen können. Der Parameter enthält die einzufügenden Daten als .NET-Objekt.

    await todoTable.InsertAsync(todoItem);

Falls das `todoItem`, das im `todoTable.InsertAsync`-Aufruf übergeben wird, keine eindeutige benutzerdefinierte Id enthält, generiert der Server eine Id und setzt den entsprechenden Wert in dem `todoItem`-Objekt, das an den Client zurückgegeben wird.

Mobile Dienste unterstützen eindeutige benutzerdefinierte Zeichenfolgen als Tabellen-Id. Auf diese Weise können Anwendungen eigene Werte wie z. B. E-Mail-Adressen oder Benutzernamen in der Id-Spalte einer Tabelle in einem mobilen Dienst verwenden. Falls beim Einfügen eines Datensatzes in eine Tabelle kein Id-Wert angegeben wird, generiert der mobile Dienst einen eindeutigen Wert für die Id.

Die Möglichkeit zum Verwenden von Zeichenfolgen-Ids bietet Entwicklern die folgenden Vorzüge

-   Ids können ohne Roundtrip zur Datenbank generiert werden.
-   Datensätze aus unterschiedlichen Tabellen oder Datenbanken lassen sich leichter zusammenführen.
-   Id-Werte lassen sich möglicherweise leichter in die Anwendungslogik integrieren.

Sie können auch Serverskripts verwenden, um die Id-Werte zu setzen. Das folgende Skript generiert eine benutzerdefinierte GUID und verwendet diese als Id für einen neuen Eintrag. Dieser Wert ähnelt dem Id-Wert, den der mobile Dienst generieren würde, wenn in der Abfrage kein Id-Wert übergeben wird.

    //Generieren einer Id. Dies ist nicht erforderlich, da der mobile
    //Dienst eine Id generiert, wenn kein Wert übergeben wird.
    item.id = item.id || newGuid();
    request.execute();

    function newGuid() {
        var pad4 = function(str) { return "0000".substring(str.length) + str; };
        var hex4 = function () { return pad4(Math.floor(Math.random() * 0x10000 /* 65536 */ ).toString(16)); };
        return (hex4() + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + "-" + hex4() + hex4() + hex4());
    }

Wenn eine Anwendung einen Id-Wert übergibt, speichert der mobile Dienst diesen Wert unverändert. Dies beinhaltet auch vor- und nachstehende Leerzeichen. Leerzeichen werden aus dem Wert gekürzt.

Der `id`-Wert muss eindeutig sein und darf keine Zeichen aus den folgenden Sätzen enthalten:

-   Steuerzeichen: [0x0000-0x001F] und [0x007F-0x009F]. Weitere Informationen finden Sie unter [ASCII-Steuerzeichen C0 und C1](http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set).
-   Druckbare Zeichen: **"**(0x0022), **+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **\`** (0x0060)
-   Die IDs "." und ".."

Alternativ können Sie auch ganzzahlige Ids für Ihre Tabellen verwenden. Um ganzzahlige Ids zu verwenden, müssen Sie bei der Tabellenerstellung für den `mobile table create`-Befehl die Option `--integerId` verwenden. Dieser Befehl wird in der Befehlszeilenschnittstelle (CLI) für Azure verwendet. Weitere Informationen zur CLI finden Sie unter [CLI to manage Mobile Services tables](http://www.windowsazure.com/en-us/manage/linux/other-resources/command-line-tools/#Mobile_Tables) (CLI für Tabellen in mobilen Diensten, in englischer Sprache).

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

Ändern von DatenVorgehensweise: Ändern von Daten in einem mobilen Dienst
------------------------------------------------------------------------

Der folgende Code zeigt, wie Sie eine existierende Instanz mit derselben Id und neuen Daten aktualisieren können. Der Parameter enthält die zu aktualisierenden Daten als .NET-Objekt.

    await todoTable.UpdateAsync(todoItem);

Zum Einfügen von untypisierten Daten können Sie Json.NET verwenden, wie hier gezeigt. Beachten Sie, dass Sie bei Änderungen eine Id angeben müssen, damit der mobile Dienst die zu aktualisierende Instanz identifizieren kann. Sie erhalten die Id im Ergebnis des `InsertAsync`-Aufrufs.

    JObject jo = new JObject(); 
    jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    jo.Add("Text", "Hello World"); 
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);

Wenn Sie versuchen, ein Element ohne Angabe der Id zu aktualisieren, hat der Dienst keine Möglichkeit, die korrekte Instanz zu identifizieren und das SDK für mobile Dienste wirft eine `ArgumentException`.

Löschen von DatenVorgehensweise: Löschen von Daten in einem mobilen Dienst
--------------------------------------------------------------------------

Der folgende Code zeigt, wie Sie existierende Instanzen löschen können. Die Instanz wird durch das "Id"-Feld im `todoItem` eindeutig identifiziert.

    await todoTable.DeleteAsync(todoItem);

Zum Löschen von untypisierten Daten können Sie Json.NET verwenden, wie hier gezeigt. Beachten Sie, dass Sie bei Löschungen eine Id angeben müssen, damit der mobile Dienst die zu löschende Instanz identifizieren kann. Eine Löschanfrage benötigt nur die Id. Sonstige Eigenschaften werden nicht an den Dienst übergeben und eventuell vorhandene Eigenschaften werden vom Dienst ignoriert. Das Ergebnis eines `DeleteAsync`-Aufrufs ist üblicherweise ebenfalls `null`. Sie erhalten die zu übergebende Id im Ergebnis des `InsertAsync`-Aufrufs.

    JObject jo = new JObject(); 
    jo.Add("Id", "37BBF396-11F0-4B39-85C8-B319C729AF6D");
    await table.DeleteAsync(jo);

Wenn Sie versuchen, ein Element ohne Angabe der Id zu löschen, hat der Dienst keine Möglichkeit, die korrekte Instanz zu identifizieren und der Dienst gibt eine `MobileServiceInvalidOperationException` zurück. Wenn Sie versuchen, ein untypisiertes Element ohne Angabe der Id zu löschen, gibt der Dienst ebenfalls eine `MobileServiceInvalidOperationException` zurück.

Optimistische ParallelitätVorgehensweise: Verwenden von optimistischer Parallelität.
------------------------------------------------------------------------------------

In manchen Szenarien können zwei oder mehr Clients gleichzeitig versuchen, dasselbe Element zu bearbeiten. Ohne Konflikterkennung würde der letzte Schreibvorgang alle vorherigen Aktualisierungen überschreiben, selbst wenn dies nicht so gewollt wäre. Die Steuerung für optimistische Parallelität nimmt an, dass jede Transaktion Commits ausführen kann und sperrt daher keine Ressourcen. Vor dem Commit einer Transaktion prüft die Steuerung für optimistische Parallelität, ob die Daten von einer anderen Transaktion geändert wurden. Falls die Daten geändert wurden, wird für die Transaktion, die den Commit durchführen sollte, ein Rollback durchgeführt.

Mobile Dienste unterstützen die Steuerung für optimistische Parallelität, indem Änderungen an Elementen in der `__version`-Systemeigenschaftenspalte registriert werden, die in alle von mobilen Diensten erstellten Tabellen existiert. Bei jeder Änderung an einem Element setzt der mobile Dienst einen neuen Wert für die `__version`-Eigenschaft des entsprechenden Elements. Bei jeder Änderungsanfrage wird die `__version`-Eigenschaft des entsprechenden Eintrags mit der Eigenschaft des Eintrags auf dem Server verglichen. Falls die in der Anfrage übergebene Version nicht mit der Version auf dem Server übereinstimmt, wirft die .NET-Clientbibliothek für mobile Dienste eine `MobileServicePreconditionFailedException<T>`. Der in der Ausnahme enthaltene Typ ist die Serverversion des entsprechenden Eintrags. Anschließend kann die Anwendung anhand dieser Informationen entscheiden, ob die Änderungsanfrage erneut mit dem korrekten `__version`-Wert vom Server geschickt werden soll, um den Commit für die Änderungen auszuführen.

Die Anwendung definiert eine Spalte in der Tabellenklasse für die `__version`-Systemeigenschaft, um die Kontrolle für optimistische Parallelität zu aktivieren. Die folgende Definition zeigt ein Beispiel.

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

Anwendungen mit untypisierten Tabellen können die optimistische Parallelität aktivieren, indem sie das `Version`-Flag in den `SystemProperties` der Tabelle setzt, wie hier gezeigt.

    //Aktivieren der optimistischen Parallelität durch Abrufen von __version 
    todoTable.SystemProperties |= MobileServiceSystemProperties.Version;

Der folgende Code zeigt, wie ein erkannter Schreibkonflikt gelöst werden kann. Der korrekte `__version`-Wert muss im `UpdateAsync()`-Aufruf angegeben werden, um den Commit für die Lösung auszuführen.

	private async void UpdateToDoItem(TodoItem item)
	{
    	MobileServicePreconditionFailedException<TodoItem> exception = null;         
		
	    try
    	{
	        //Aktualisierung der Tabelle auf dem Server
            await todoTable.UpdateAsync(item);
    	}
    	catch (MobileServicePreconditionFailedException<TodoItem> writeException)
	    {
        	exception = writeException;
	    }
		
    	if (exception != null)
    	{
			// Konflikt erkannt, Element wurde seit der letzten Abfrage verändert
        	// Konflikt zwischen lokalem und Serverelement lösen
	        await ResolveConflict(item, exception.Item);
    	}
	}


	private async Task ResolveConflict(TodoItem localItem, TodoItem serverItem)
	{
    	 //Bitte an den Benutzer, zwischen den Versionen zu unterscheiden
	    MessageDialog msgDialog = new MessageDialog(String.Format("Server Text: \"{0}\" \nLocal Text: \"{1}\"\n", 
        	                                        serverItem.Text, localItem.Text), 
                                                	"CONFLICT DETECTED - Select a resolution:");

	    UICommand localBtn = new UICommand("Commit Local Text");
    	UICommand ServerBtn = new UICommand("Leave Server Text");
    	msgDialog.Commands.Add(localBtn);
	    msgDialog.Commands.Add(ServerBtn);          

    	localBtn.Invoked = async (IUICommand command) =>
	    {
        	// Aktualisieren der Version des zu speichernden
            // Elements, um den Konflikt zu lösen. Ansonsten erhalten Sie
            // erneut eine MobileServicePreConditionFailedException.
	        localItem.Version = serverItem.Version;             

    	    // Rekursive Aktualisierung, falls das Element erneut geändert
            // wurde, während sich der Benutzer entschieden hat
	        UpdateToDoItem(localItem);
    	};          
		
	    ServerBtn.Invoked = async (IUICommand command) =>
    	{
	        RefreshTodoItems();
    	};          
		
	    await msgDialog.ShowAsync();
	}

Ein kompletteres Beispiel für optimistische Parallelität in mobilen Diensten finden Sie unter [Optimistic Concurrency Tutorial](http://www.windowsazure.com/en-us/develop/mobile/tutorials/handle-database-write-conflicts-dotnet/) (Lernprogramm für optimistische Parallelität, in englischer Sprache).

Anzeigen von DatenVorgehensweise: Bindung von Daten an die Benutzeroberfläche in einem mobilen Dienst
-----------------------------------------------------------------------------------------------------

Dieser Abschnitt beschreibt das Anzeigen von Datenobjekten in GUI-Elementen. Für die Abfrage unvollständiger Elemente aus der `todoTable` und deren Anzeige in einer sehr einfachen Liste können Sie den folgenden Code ausführen, der die Quelle der Liste an eine Abfrage bindet. `MobileServiceCollection` erstellt eine Bindungsauflistung, die mobile Dienste unterstützt.

    // Diese Abfrage filtert abgeschlossene TodoItems heraus. 
    MobileServiceCollection<TodoItem, TodoItem> items = await todoTable
        .Where(todoItem => todoItem.Complete == false)
        .ToCollectionAsync();

    // itemsControl ist ein IEnumerable , das an ein GUI-Listensteuerelement gebunden werden kann
    IEnumerable itemsControl  = items;            
            
    // Bind this to a ListBox
    ListBox lb = new ListBox();
    lb.ItemsSource = items;

Manche Steuerelemente in der Windows-Runtime unterstützen eine Schnittstelle namens [ISupportIncrementalLoading](http://msdn.microsoft.com/en-us/library/windows/apps/Hh701916). Mit dieser Schnittstelle können Steuerelemente weitere Daten anfordern, wenn ein Benutzer blättert. `MobileServiceIncrementalLoadingCollection` bietet eingebaute Unterstützung für diese Schnittstelle für Windows Store-Apps und verarbeitet die Aufrufe dieser Steuerelemente automatisch. Gehen Sie wie folgt vor, um `MobileServiceIncrementalLoadingCollection` in Windows Store-Apps zu verwenden:

         MobileServiceIncrementalLoadingCollection<TodoItem,TodoItem> items;
        items =  todoTable.Where(todoItem => todoItem.Complete == false)
                    .ToIncrementalLoadingCollection();

        ListBox lb = new ListBox();
        lb.ItemsSource = items;

Verwenden Sie die `ToCollection`-Erweiterungsmethoden in `IMobileServiceTableQuery<T>` und `IMobileServiceTable<T>`, um die neue Sammlung unter Windows Phone zu verwenden. Rufen Sie `LoadMoreItemsAsync()` auf, um tatsächlich Daten zu laden.

    	MobileServiceCollection<TodoItem, TodoItem> items = todoTable.Where(todoItem => todoItem.Complete==false).ToCollection(); 
	await items.LoadMoreItemsAsync();         

Die Sammlung, die bei den Aufrufen von `ToCollectionAsync` bzw. `ToCollection` zurückgegeben wurde, kann an GUI-Steuerelemente gebunden werden. Diese Sammlung unterstützt Seitenverwaltung, d. h. ein Steuerelement kann die Sammlung anweisen, "weitere Elemente zu laden", und die Sammlung erledigt dies für das Steuerelement. Bis zu diesem Punkt wird noch kein Benutzercode ausgeführt, das Steuerelement startet den Fluss. Da die Sammlung jedoch Daten aus dem Netzwerk lädt, ist zu erwarten, dass dieser Ladevorgang manchmal fehlschlägt. Zur Behandlung solcher Fehler können Sie die `OnException`-Methode in `MobileServiceIncrementalLoadingCollection` überschreiben, um Ausnahmen zu verarbeiten, die aus Aufrufen von `LoadMoreItemsAsync` durch Steuerelemente entstehen.

Stellen Sie sich vor, Ihre Tabelle hat viele Felder, aber Sie möchten nur einen Teil der Felder in Ihrem Steuerelement anzeigen. Folgen Sie der Anleitung unter ["Bestimmte Spalten auswählen"](#selecting) weiter oben, um bestimmte Spalten für die Anzeige in der GUI auszuwählen.

AuthentifizierenVorgehensweise: Authentifizieren von Benutzern
--------------------------------------------------------------

Mobile Dienste unterstützen Authentifizierung und Autorisierung von Anwendungsbenutzern mit einer Vielzahl externer Identitätsanbieter: Facebook, Google, Microsoft Account, Twitter und Azure Active Directory. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln in Serverskripts zu implementieren. Weitere Informationen finden Sie im Lernprogramm "Erste Schritte mit der Authentifizierung" ([Windows Store](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/)/[Windows Phone](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-users-wp8/))

Insgesamt werden zwei Authentifizierungsflüsse unterstützt: ein *Serverfluss* und ein *Clientfluss*. Der Serverfluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Authentifizierungs-Webschnittstelle des Anbieters verwendet wird. Der Clientfluss ermöglicht eine tiefere Integration mit gerätespezifischen Fähigkeiten, da in diesem Fall anbieterspezifische und gerätespezifische SDKs verwendet werden.

### Serverfluss

Sie müssen Ihre Anwendung bei Ihrem Identitätsanbieter registrieren, um den mobilen Diensten die Verwaltung des Authentifizierungsprozesses in Ihrer Windows Store- oder Windows Phone-App zu ermöglichen. Anschließend müssen Sie in Ihrem mobilen Dienst die Anwendungs-ID und den geheimen Schlüssel Ihres Anbieters konfigurieren. Weitere Informationen finden Sie im Lernprogramm "Erste Schritte mit der Authentifizierung" ([Windows Store](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-users-dotnet/)/[Windows Phone](http://www.windowsazure.com/en-us/develop/mobile/tutorials/get-started-with-users-wp8/)).

Nach der Registrierung bei Ihrem Identitätsanbieter können Sie die [LoginAsync](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx)-Methode mit dem [MobileServiceAuthenticationProvider](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx)-Wert Ihres Anbieters aufrufen. Der folgende Beispielcode startet eine Serverfluss-Anmeldung für Facebook.

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
                    string.Format("Sie sind nun angemeldet - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "Sie müssen sich anmelden. Anmeldung erforderlich";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den Wert für [MobileServiceAuthenticationProvider](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceauthenticationprovider.aspx) oben entsprechend Ihrem Anbieter.

In diesem Fall verwaltet der mobile Dienst den OAuth 2.0-Authentifizierungsfluss, indem die Anmeldungsseite des ausgewählten Anbieters angezeigt und nach der erfolgreichen Anmeldung beim Identitätsanbieter ein Authentifizierungstoken für den mobilen Dienst generiert wird. Die [LoginAsync](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceclientextensions.loginasync.aspx)-Methode gibt einen [MobileServiceUser](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.aspx) zurück, der wiederum sowohl [userId](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.userid.aspx) des authentifizierten Benutzers als auch das [MobileServiceAuthenticationToken](http://msdn.microsoft.com/en-us/library/windowsazure/microsoft.windowsazure.mobileservices.mobileserviceuser.mobileserviceauthenticationtoken.aspx) in Form eines JSON-Webtokens (JWT) enthält. Dieses Token kann zwischen gespeichert und wiederverwendet werden, bis es abläuft. Weitere Informationen finden Sie unter [Zwischenspeichern des Authentifizierungstokens](#caching).

**Windows Store-App**

Wenn Sie den Anmeldeanbieter für Microsoft-Konten zum Authentifizieren von Benutzern Ihrer Windows Store-App verwenden, sollten Sie ebenfalls das App-Paket im mobilen Dienst registrieren. Wenn Sie Ihre Windows Store-App-Paketinformationen bei Mobile Services registrieren, kann der Client die Anmeldeinformationen für Microsoft Account für eine einmalige Anmeldung verwenden. Wenn Sie dies nicht tun, werden Ihre Benutzer mit Microsoft Account-Login jedes Mal zur Anmeldung aufgefordert, wenn diese Anmeldemethode aufgerufen wird. Informationen zur Registrierung Ihres Windows Store-App-Pakets finden Sie unter [Registrieren Ihres Windows Store-App-Pakets für Microsoft-Authentifizierung](/en-us/develop/mobile/how-to-guides/register-windows-store-app-package/). Nach der Registrierung Ihres Pakets im mobilen Dienst können Sie die [LoginAsync](http://go.microsoft.com/fwlink/p/?LinkId=311594)-Methode aufrufen und den Wert **true** für den *useSingleSignOn*-Parameter übergeben, um die Anmeldeinformationen wiederzuverwenden.

### Clientfluss

Ihre Anwendung kann den Identitätsanbieter auch unabhängig kontaktieren und das zurückgegebene Token zur Authentifizierung dem mobilen Dienst vorlegen. Mit diesem Clientfluss können Sie die einmalige Anmeldung für Ihre Benutzer implementieren oder zusätzliche Benutzerdaten vom Identitätsanbieter abrufen.

Der folgende Codeausschnitt zeigt die einfachste Form des Clientflusses für Facebook oder Google.

    var token = new JObject();
    // Ersetzen Sie access_token_value durch den tatsächlichen Token-Wert, den 
    // Sie über das Facebook- bzw. Google-SDK abgerufen haben.
    token.Add("access_token", "access_token_value");
            
    private MobileServiceUser user;
    private async System.Threading.Tasks.Task Authenticate()
    {
        while (user == null)
        {
            string message;
            try
            {
                // Ändern Sie MobileServiceAuthenticationProvider.Facebook 
                // zu MobileServiceAuthenticationProvider.Google für Google-Authentifizierung.
                user = await client
                    .LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
                message = 
                    string.Format("Sie sind nun angemeldet - {0}", user.UserId);
            }
            catch (InvalidOperationException)
            {
                message = "Sie müssen sich anmelden. Anmeldung erforderlich";
            }

            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }
    }

Für Microsoft-Konten funktioniert die Anmeldung folgendermaßen:

    // Ersetzen Sie authentication_token_value durch den tatsächlichen Wert des Microsoft-Authentifizierungstokens, den Sie über die Live SDK abgerufen haben
    user = await client
        .LoginWithMicrosoftAccountAsync(authentication_token_value);

Ein Beispiel für die einmalige Anmeldung mit Microsoft-Konten finden Sie im Lernprogramm "Authenticate your app with single sign-in" ([Windows Store](/en-us/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/)/[Windows Phone](/en-us/develop/mobile/tutorials/single-sign-on-wp8/)).

### Zwischenspeichern des Authentifizierungstokens

Manche Aufrufe der Anmeldemethode lassen sich vermeiden, nachdem sich der Benutzer authentifiziert hat. Sie können den [Kennworttresor](http://msdn.microsoft.com/en-us/library/windows/apps/windows.security.credentials.passwordvault.aspx) für Windows Store-Apps verwenden, um die Identität des aktuellen Benutzers bei der ersten Anmeldung zwischenzuspeichern und später bei Bedarf prüfen, ob Sie die Benutzeridentität bereits im Speicher haben. Wenn der Zwischenspeicher leer ist, muss der Benutzer den Anmeldeprozess durchlaufen.

    // Nach der Anmeldung
    PasswordVault vault = new PasswordVault();
    vault.Add(new PasswordCredential("Facebook", user.UserId, user.MobileServiceAuthenticationToken));

    // Anmelden
    var creds = vault.FindAllByResource("Facebook").FirstOrDefault();
    if (creds != null)
    {
        user = new MobileServiceUser(creds.UserName);
        user.MobileServiceAuthenticationToken = vault.Retrieve("Facebook", creds.UserName).Password;
    }
    else
    {
        // Normaler Anmeldefluss
        user = new MobileServiceuser( await client
            .LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
        var token = new JObject();
        // Replace access_token_value with actual value of your access token
        token.Add("access_token", "access_token_value");
    }
            
     // Abmelden
    client.Logout();
    vault.Remove(vault.Retrieve("Facebook", user.UserId));

Für Windows Phone-Apps können Sie die Daten mithilfe der [ProtectedData](http://msdn.microsoft.com/en-us/library/system.security.cryptography.protecteddata%28VS.95%29.aspx)-Klasse verschlüsseln und zwischenspeichern und die vertraulichen Informationen in einem isolierten Speicher vorhalten.

FehlerbehandlungVorgehensweise: Fehlerbehandlung
------------------------------------------------

Mobile Dienste bieten verschiedene Möglichkeiten zur Erkennung, Validierung und Behebung von Fehlern.

Ein Beispiel sind Serverskripts, die in einem mobilen Dienst registriert sind und für eine Vielzahl von Operationen auf einzufügende und zu aktualisierende Daten verwendet werden können, inklusive Validierung und Änderung der Daten. Sie könnten z. B. das folgende Serverskript erstellen und registrieren, das Daten validiert und ändert:

    function insert(item, user, request) 
    {
       if (item.text.length > 10) {
          request.respond(statusCodes.BAD_REQUEST, { error: "Text darf maximal 10 Zeichen lang sein" });
       } else {
          request.execute();
       }
    }

Das Serverskript validiert die Länge von Zeichenfolgen, die an den mobilen Dienst geschickt werden, und lehnt zu lange Zeichenfolgen ab (in diesem Fall länger als 10 Zeichen).

Nun ist Ihr mobiler Dienst in der Lage, Daten zu validieren und Fehlermeldungen im Server zu generieren und Sie können Ihre .NET-Anwendung erweitern, sodass diese mit den Validierungsfehlern umgehen kann.

    private async void InsertTodoItem(TodoItem todoItem)
    {
        // Dieser Code fügt ein neues TodoItem in die Datenbank ein. Wenn die Operation endet
        // und der mobile Dienst eine Id generiert hat, wird das Element zur CollectionView hinzugefügt
        try
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);
        }
        catch (MobileServiceInvalidOperationException e)
        {
            // Fehlerbehandlung
        }
    }

Arbeiten mit nicht typisierten DatenVorgehensweise: Arbeiten mit nicht typisierten Daten
----------------------------------------------------------------------------------------

Der .NET-Client wurde für stark typisierte Szenarien entwickelt. Manchmal macht jedoch eine weniger starke Typisierung Sinn, z. B. beim Umgang mit Objekten mit offenem Schema. Dieses Szenario funktioniert wie folgt. In Ihren Abfragen verwenden Sie das Übertragungsformat anstelle von LINQ.

    // Abrufen eines untypisierten Tabellenverweises
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");           

    // Nachschlagen von untypisierten Daten mit OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Sie erhalten JSON-Werte zurück, die Sie wie einen Eigenschaftenbehälter verwenden können. Weitere Informationen zu JToken und Json.NET finden Sie unter [Json.NET](http://json.codeplex.com/)

Design von KomponententestsVorgehensweise: Design von Komponententests
----------------------------------------------------------------------

Der von `MobileServiceClient.GetTable` zurückgegebene Wert und die Abfragen sind Schnittstellen. Daher lassen sich diese Komponenten leicht zu Testzwecken "nachbilden". Sie könnten z. B: die Tabelle `MyMockTable : IMobileServiceTable<TodoItem>` erstellen, die Ihre Testlogik implementiert.

Anpassen des ClientsVorgehensweise: Anpassen des Clients
--------------------------------------------------------

### Vorgehensweise: Anpassen der Anforderungsheader

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

### Vorgehensweise: Anpassen der Serialisierung

Die [MobileServiceClient](http://msdn.microsoft.com/en-us/library/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx)-Klasse stellt eine `SerializerSettings`-Eigenschaft vom Typ [JsonSerializerSettings zur Verfügung.](http://james.newtonking.com/projects/json/help/?topic=html/T_Newtonsoft_Json_JsonSerializerSettings.htm)

Mit dieser Eigenschaft können Sie Json.NET-Eigenschaften setzen (von denen es viele gibt), inklusive einer Eigenschaft, die alle Eigenschaften in Kleinbuchstaben umwandelt:

    var settings = new JsonSerializerSettings();
    settings.ContractResolver = new CamelCasePropertyNamesContractResolver();
    client.SerializerSettings = settings;

Nächste Schritte
----------------

Sie haben das konzeptuelle Referenzthema abgeschlossen und können sich nun wichtigen Aufgaben in mobilen Diensten im Detail widmen:

-   [Erste Schritte mit mobilen Diensten](/en-us/develop/mobile/tutorials/get-started)
    Lernen Sie die Grundlagen für den Einsatz von mobilen Diensten.

-   [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-dotnet/)
    Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-dotnet)
    Informationen zur Authentifizierung von Benutzern Ihrer App bei einem Identitätsanbieter.

-   [Prüfen und Ändern von Daten mit Skripten](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet)
    Informationen zur Verwendung von Serverskripten in Mobile Services, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet)
    Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

-   [Autorisieren von Benutzern mit Skripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-dotnet)
    Verwenden Sie die vom mobilen Dienst für einen authentifizierten Benutzer gelieferte Benutzer-ID zum Filtern von zurückgegebenen Daten.


