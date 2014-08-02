<properties linkid="mobile-services-how-to-xamarin-client" urlDisplayName="Xamarin" pageTitle="How to use the Xamarin Component client - Azure Mobile Services feature guide" metaKeywords="Azure Mobile Services, Xamarin, iOS, Android, .NET client" description="Learn how to use the Xamarin Component client for Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="0" title="How to use the Xamarin Component client for Azure Mobile Services" authors="" />

So verwenden Sie den Client der Xamarin-Komponente für Azure Mobile Services
============================================================================

[.NET Framework](/en-us/develop/mobile/how-to-guides/work-with-net-client-library/ ".NET Framework") [HTML/JavaScript](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/ "HTML/JavaScript")[iOS](/en-us/develop/mobile/how-to-guides/work-with-ios-client-library/ "iOS")[Android](/en-us/develop/mobile/how-to-guides/work-with-android-client-library/ "Android")[Xamarin](/en-us/develop/mobile/how-to-guides/work-with-xamarin-client-library/ "Xamarin")

Dieser Artikel beschreibt gängige Szenarien für die Verwendung des Clients der Xamarin-Komponente für mobile Dienste in Azure und in Xamarin-Apps für iOS und Android. Besprochen werden unter anderem Datenabfragen, Einfügen, Aktualisieren und Löschen von Daten, Authentifizierung von Benutzern und Fehlerbehandlung. Falls Sie keine Erfahrung mit mobilen Diensten haben, sollten Sie möglicherweise zunächst die Lernprogramme "Schnellstart für mobile Dienste" ([Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-xamarin-ios/)/[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-xamarin-android/)) und "Erste Schritte mit Daten in .NET" ([Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios/)/[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android/)). Für das Schnellstart-Lernprogramm benötigen Sie [Xamarin](http://xamarin.com/download/) und das [Mobile Services-SDK](http://nuget.org/packages/WindowsAzure.MobileServices/). Sie lernen dort, Ihr Konto zu konfigurieren und Ihren ersten mobilen Dienst zu erstellen.

Inhaltsverzeichnis
------------------

-   [Was ist Mobile Services?](#what-is)
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
-   [Vorgehensweise: Authentifizieren von Benutzern](#authentication)
-   [Vorgehensweise: Fehlerbehandlung](#errors)
-   [Vorgehensweise: Arbeiten mit nicht typisierten Daten](#untyped)
-   [Vorgehensweise: Design von Komponententests](#unit-testing)
-   [Nächste Schritte](#nextsteps)

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

EinrichtungEinrichtung und Voraussetzungen
------------------------------------------

Wir setzen voraus, dass Sie einen mobilen Dienst und eine Tabelle erstellt haben. Weitere Informationen finden Sie unter [Erstellen einer Tabelle](http://go.microsoft.com/fwlink/?LinkId=298592). Der Code in diesem Artikel verwendet eine Tabelle mit dem Namen `TodoItem` und den folgenden Spalten: `id`, `Text` und `Complete`.

Der entsprechende typisierte clientseitige .NET-Typ sieht wie folgt aus:

    public class TodoItem
    {
        public string id { get; set; }

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

Der folgende Code zeigt, wie Sie Daten mithilfe einer `OrderBy`- oder einer `OrderByDescending`-Klausel in einer Abfrage sortieren können. Die Abfrage liefert Elemente aus der Tabelle `todoTable` aufsteigend sortiert nach dem `Text`-Feld zurück. Standardmäßig gibt der Server nur die ersten 50 Elemente zurück.

**Hinweis**

Standardmäßig wird eine servergesteuerte Seitengröße verwendet, um zu verhindern, dass alle Elemente zurückgegeben werden. Damit wird verhindert, dass Standardabfragen für große Datensätze den Dienst negativ beeinflussen.

Sie können `Take` aufrufen, um die Anzahl der zurückgegebenen Elemente zu erhöhen, wie im nächsten Abschnitt beschrieben.

    // Elemente aufsteigend nach Text-Feld sortieren
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderBy(todoItem => todoItem.Text)       
 	List items = await query.ToListAsync();

    // Elemente absteigend nach Text-Feld sortieren
    MobileServiceTableQuery<TodoItem> query = todoTable
                    .OrderByDescending(todoItem => todoItem.Text)       

 	List items = await query.ToListAsync();

### Vorgehensweise: Daten seitenweise zurückgeben

Der folgende Code zeigt, wie Sie mithilfe der `Take`- und `Skip`-Klausel in Ihrer Abfrage einen Seitenverwaltungsmechanismus umsetzen können. Die folgende Abfrage liefert die ersten drei Elemente aus der Tabelle zurück.

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

Mit der [IncludeTotalCount](http://msdn.microsoft.com/en-us/library/windowsazure/jj730933.aspx)-Methode können Sie sicherstellen, dass die Abfrage die Gesamtanzahl für *alle* Datensätze abruft, die bei Ignorieren der angegebenen Take Paging/Limit-Klausel zurückgegeben worden wären:

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

    // Diese Abfrage filtert das Element mit der ID 25 heraus
    TodoItem item25 = await todoTable.LookupAsync("25");

Einfügen von DatenVorgehensweise: Einfügen von Daten in einen mobilen Dienst
----------------------------------------------------------------------------

**Hinweis**

Wenn Sie Operationen zum Einfügen, Nachschlagen, Löschen oder Aktualisieren auf einem Typ ausführen möchten, müssen Sie das **Id**-Element aufrufen (Groß-/Kleinschreibung wird ignoriert). Aus diesem Grund hat die Beispielklasse **TodoItem** ein Element mit dem Namen **Id**. Der beim Einfügevorgang vergebene Id-Wert darf nicht geändert werden. Im Gegensatz dazu sollte der Id-Wert immer auf einen nicht-Standardwert gesetzt und in Änderungs- und Löschanfragen angegeben werden.

Der folgende Code zeigt, wie Sie neue Zeilen in eine Tabelle einfügen können. Der Parameter enthält die einzufügenden Daten als .NET-Objekt.

    await todoTable.InsertAsync(todoItem);

Nach Abschluss des await `todoTable.InsertAsync`-Aufrufs wird die serverseitige Id des Objekts in das `todoItem`-Objekt im Client eingetragen.

Zum Einfügen von untypisierten Daten können Sie Json.NET verwenden, wie unten gezeigt. Beachten Sie, dass auch hier beim Einfügen von Objekten keine Id angegeben wird.

    JObject jo = new JObject(); 
    jo.Add("Text", "Hello World"); 
    jo.Add("Complete", false);
    var inserted = await table.InsertAsync(jo);

Wenn Sie versuchen, ein Element mit Angabe einer Id einzufügen, gibt der Dienst eine `MobileServiceInvalidOperationException` zurück.

Ändern von DatenVorgehensweise: Ändern von Daten in einem mobilen Dienst
------------------------------------------------------------------------

Der folgende Code zeigt, wie Sie eine existierende Instanz mit derselben Id und neuen Daten aktualisieren können. Der Parameter enthält die zu aktualisierenden Daten als .NET-Objekt.

    await todoTable.UpdateAsync(todoItem);

Zum Einfügen von untypisierten Daten können Sie Json.NET verwenden, wie hier gezeigt. Beachten Sie, dass Sie bei Änderungen eine Id angeben müssen, damit der mobile Dienst die zu aktualisierende Instanz identifizieren kann. Sie erhalten die Id im Ergebnis des `InsertAsync`-Aufrufs.

    JObject jo = new JObject(); 
    jo.Add("Id", 52);
    jo.Add("Text", "Hello World"); 
    jo.Add("Complete", false);
    var inserted = await table.UpdateAsync(jo);

Wenn Sie versuchen, ein Element ohne Angabe der Id zu ändern, hat der Dienst keine Möglichkeit, die korrekte Instanz zu identifizieren und der Dienst gibt eine `MobileServiceInvalidOperationException` zurück. Wenn Sie versuchen, ein untypisiertes Element ohne Angabe der Id zu ändern, gibt der Dienst ebenfalls eine `MobileServiceInvalidOperationException` zurück.

Löschen von DatenVorgehensweise: Löschen von Daten in einem mobilen Dienst
--------------------------------------------------------------------------

Der folgende Code zeigt, wie Sie existierende Instanzen löschen können. Die Instanz wird durch das "Id"-Feld im `todoItem` eindeutig identifiziert.

    await todoTable.DeleteAsync(todoItem);

Zum Löschen von untypisierten Daten können Sie Json.NET verwenden, wie hier gezeigt. Beachten Sie, dass Sie bei Löschungen eine Id angeben müssen, damit der mobile Dienst die zu löschende Instanz identifizieren kann. Eine Löschanfrage benötigt nur die Id. Sonstige Eigenschaften werden nicht an den Dienst übergeben und eventuell vorhandene Eigenschaften werden vom Dienst ignoriert. Das Ergebnis eines `DeleteAsync`-Aufrufs ist üblicherweise ebenfalls `null`. Sie erhalten die zu übergebende Id im Ergebnis des `InsertAsync`-Aufrufs.

    JObject jo = new JObject(); 
    jo.Add("Id", 52);
    await table.DeleteAsync(jo);

Wenn Sie versuchen, ein Element ohne Angabe der Id zu löschen, hat der Dienst keine Möglichkeit, die korrekte Instanz zu identifizieren und der Dienst gibt eine `MobileServiceInvalidOperationException` zurück. Wenn Sie versuchen, ein untypisiertes Element ohne Angabe der Id zu löschen, gibt der Dienst ebenfalls eine `MobileServiceInvalidOperationException` zurück.

AuthentifizierenVorgehensweise: Authentifizieren von Benutzern
--------------------------------------------------------------

Mobile Dienste unterstützen Authentifizierung und Autorisierung von Anwendungsbenutzern mit einer Vielzahl externer Identitätsanbieter: Facebook, Google, Microsoft Account, Twitter und Azure Active Directory. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln in Serverskripts zu implementieren. Weitere Informationen finden Sie im Lernprogramm "Erste Schritte mit der Authentifizierung" ([Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios/)/[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android/)).

Insgesamt werden zwei Authentifizierungsflüsse unterstützt: ein *Serverfluss* und ein *Clientfluss*. Der Serverfluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Authentifizierungs-Webschnittstelle des Anbieters verwendet wird. Der Clientfluss ermöglicht eine tiefere Integration mit gerätespezifischen Fähigkeiten, da in diesem Fall anbieterspezifische und gerätespezifische SDKs verwendet werden.

### Serverfluss

Sie müssen Ihre Anwendung bei Ihrem Identitätsanbieter registrieren, um den mobilen Diensten die Verwaltung des Authentifizierungsprozesses in Ihrer Windows Store- oder Windows Phone-App zu ermöglichen. Anschließend müssen Sie in Ihrem mobilen Dienst die Anwendungs-ID und den geheimen Schlüssel Ihres Anbieters konfigurieren. Weitere Informationen finden Sie im Lernprogramm "Erste Schritte mit der Authentifizierung" ([Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios/)/[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android/)).

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

### Zwischenspeichern des Authentifizierungstokens

Manche Aufrufe der Anmeldemethode lassen sich vermeiden, nachdem sich der Benutzer authentifiziert hat. Sie können einen sicheren lokalen Speicher (z. B. [Xamarin.Auth](https://components.xamarin.com/view/xamarin.auth)) verwenden, um die Identität des aktuellen Benutzers bei der ersten Anmeldung zwischenzuspeichern und später bei Bedarf prüfen, ob Sie die Benutzeridentität bereits im Speicher haben. Wenn der Zwischenspeicher leer ist, muss der Benutzer den Anmeldeprozess durchlaufen.

    using Xamarin.Auth;
    var accountStore = AccountStore.Create(); // Xamarin.iOS
    //var accountStore = AccountStore.Create(this); // Xamarin.Android

    // Nach der Anmeldung
    var account = new Account (user.UserId, new Dictionary<string,string> {{"token",user.MobileServiceAuthenticationToken}});
    accountStore.Save(account, "Facebook");

    // Anmeldung
    var accounts = accountStore.FindAccountsForService ("Facebook").ToArray();
    if (accounts.Count != 0)
    {
        user = new MobileServiceUser (accounts[0].Username);
        user.MobileServiceAuthenticationToken = accounts[0].Properties["token"];
    }
    else
    {
        // Normaler Anmeldeprozess
        user = new MobileServiceuser( await client
            .LoginAsync(MobileServiceAuthenticationProvider.Facebook, token);
        var token = new JObject();
        // access_token durch den tatsächlichen Wert des Zugriffstokens ersetzen
        token.Add("access_token", "access_token_value");
    }
            
     // Abmeldung
    client.Logout();
    accountStore.Delete(account, "Facebook");

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

Der Client der Xamarin-Komponente wurde für stark typisierte Szenarien entwickelt. Manchmal macht jedoch eine weniger starke Typisierung Sinn, z. B. beim Umgang mit Objekten mit offenem Schema. Dieses Szenario funktioniert wie folgt. In Ihren Abfragen verwenden Sie das Übertragungsformat anstelle von LINQ.

    // Abrufen eines untypisierten Tabellenverweises
    IMobileServiceTable untypedTodoTable = client.GetTable("TodoItem");           

    // Nachschlagen von untypisierten Daten mit OData
    JToken untypedItems = await untypedTodoTable.ReadAsync("$filter=complete eq 0&$orderby=text");

Sie erhalten JSON-Werte zurück, die Sie wie einen Eigenschaftenbehälter verwenden können. Weitere Informationen zu JToken und Json.NET finden Sie unter [Json.NET](http://json.codeplex.com/)

Design von KomponententestsVorgehensweise: Design von Komponententests
----------------------------------------------------------------------

Der von `MobileServiceClient.GetTable` zurückgegebene Wert und die Abfragen sind Schnittstellen. Daher lassen sich diese Komponenten leicht zu Testzwecken "nachbilden". Sie könnten z. B: die Tabelle `MyMockTable : IMobileServiceTable<TodoItem>` erstellen, die Ihre Testlogik implementiert.

Nächste Schritte
----------------

Sie haben das konzeptuelle Referenzthema abgeschlossen und können sich nun wichtigen Aufgaben in mobilen Diensten im Detail widmen:

-   Erste Schritte mit mobilen Diensten ([Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-xamarin-ios)/[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-xamarin-android))
  <br/>Lernen Sie die Grundlagen für den Einsatz von mobilen Diensten.

-   Erste Schritte mit Daten ([Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios)/[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-android))
  <br/>Erhalten Sie weitere Informationen zum Speichern und Abfragen von Daten mithilfe von Mobile Services.

-   Erste Schritte mit der Authentifizierung ([Xamarin.iOS](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios)/[Xamarin.Android](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-android))
  <br/>Erhalten Sie Informationen zum Authentifizieren von Benutzern der App mit einem Identitätsanbieter.

-   Prüfen und Ändern von Daten mit Skripten ([Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios)/[Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android))
  <br/>Informationen zur Verwendung von Serverskripts in Mobile Services, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   Optimieren von Abfragen mittels Paging ([Xamarin.iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios)/[Xamarin.Android](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android))
  <br/>Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

-   Autorisieren von Benutzern mit Skripts ([Xamarin.iOS](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-ios)/[Xamarin.Android](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-xamarin-android))
  <br/>Verwenden Sie die vom mobilen Dienst für einen authentifizierten Benutzer gelieferte Benutzer-ID zum Filtern von zurückgegebenen Daten.


