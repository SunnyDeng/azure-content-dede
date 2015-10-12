<properties 
	pageTitle="Verwenden eines HTML-Clients | Microsoft Azure" 
	description="Erfahren Sie mehr über die Verwendung eines HTML-Clients für Azure Mobile Services." 
	services="mobile-services" 
	documentationCenter="" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-html" 
	ms.devlang="javascript" 
	ms.topic="article" 
	ms.date="09/24/2015" 
	ms.author="glenga"/>

# So verwenden Sie einen HTML-/JavaScript-Client für Azure Mobile Services

[AZURE.INCLUDE [mobile-services-selector-client-library](../../includes/mobile-services-selector-client-library.md)]

##Übersicht

Dieser Artikel beschreibt gängige Szenarien für die Verwendung eines HTML/JavaScript-Clients für Azure Mobile Services, die Windows Store-JavaSript- und PhoneGap/Cordova-Apps enthalten. Besprochen werden unter anderem Datenabfragen, Einfügen, Aktualisieren und Löschen von Daten, Authentifizierung von Benutzern und Fehlerbehandlung. Wenn Sie keine Erfahrungen mit Mobile Services haben, sollten Sie eventuell zunächst den [Schnellstart für mobile Dienste](mobile-services-html-get-started.md) absolvieren. Im Schnellstart-Lernprogramm lernen Sie, Ihr Konto zu konfigurieren und Ihren ersten mobilen Dienst zu erstellen.

[AZURE.INCLUDE [mobile-services-concepts](../../includes/mobile-services-concepts.md)]

##<a name="create-client"></a>Gewusst wie: Erstellen des Mobile Services-Clients

Die Vorgehensweise zum Hinzufügen eines Verweises auf den Mobiles Services-Client hängt von Ihrer App-Plattform ab:

- Für eine webbasierte Anwendung müssen Sie die HTML-Datei öffnen und Folgendes zu den Skriptverweisen der Seite hinzufügen:

        <script src="http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.2.7.min.js"></script>

- Für eine in JavaScript/HTML geschriebene Windows Store-App müssen Sie Ihrem Projekt nur das NuGet-Paket **WindowsAzure.MobileServices.WinJS** hinzufügen.

- Für eine PhoneGap- oder Cordova-App müssen Sie das [Mobile Services-Plug-In](https://github.com/Azure/azure-mobile-services-cordova) zu Ihrem Projekt hinzufügen. Dieses Plug-In unterstützt [Pushbenachrichtigungen](#push-notifications).

Öffnen oder erstellen Sie eine JavaScript-Datei im Editor, und fügen Sie den folgenden Code hinzu, um die `MobileServiceClient`-Variable zu definieren, und übergeben Sie Anwendungs-URL und -Schlüssel des mobilen Diensts in dieser Reihenfolge an den `MobileServiceClient`-Konstruktor.

	var MobileServiceClient = WindowsAzure.MobileServiceClient;
    var client = new MobileServiceClient('AppUrl', 'AppKey');

Sie müssen den Platzhalter `AppUrl` durch die Anwendungs-URL Ihres mobilen Dienstes und `AppKey` durch den Anwendungsschlüssel ersetzen. Wie Sie die Anwendungs-URL und den Anwendungsschlüssel für den mobilen Dienst erhalten, erfahren Sie im Lernprogramm [Hinzufügen von Mobile Services zu einer vorhandenen App](mobile-services-html-get-started-data.md).

>[AZURE.IMPORTANT]Der Anwendungsschlüssel dient dem Herausfiltern zufälliger Anforderungen an den mobilen Dienst und wird mit der Anwendung verteilt. Da dieser Schlüssel nicht verschlüsselt ist, kann er nicht als sicher angesehen werden. Um die Daten des mobilen Dienstes zu schützen, müssen Sie stattdessen Benutzer vor dem Zugriff authentifizieren. Weitere Informationen finden Sie unter [Gewusst wie: Authentifizieren von Benutzern](#authentication).

##<a name="querying"></a>Gewusst wie: Abfragen von Daten aus einem mobilen Dienst

Jeglicher Code zum Abrufen oder Ändern von Daten in der Tabelle der SQL-Datenbank ruft Funktionen des `MobileServiceTable`-Objekts auf. Sie erhalten einen Verweis auf die Tabelle, indem Sie die `getTable()`-Funktion für eine Instanz von `MobileServiceClient` aufrufen.

    var todoItemTable = client.getTable('todoitem');


### <a name="filtering"></a>Gewusst wie: Filtern zurückgegebener Daten

Der folgende Code zeigt, wie Sie Daten mithilfe einer `where`-Klausel in einer Abfrage filtern. Es werden alle Elemente aus der `todoItemTable`-Tabelle zurückgegeben, deren complete-Feld `false` ist. `todoItemTable` ist der Verweis auf die Tabelle im mobilen Dienst, die wir zuvor erstellt haben. Die Where-Funktion wendet ein Zeilenfilterungsprädikat auf die Tabellenabfrage an. Die Funktion nimmt ein JSON-Objekt oder eine Funktion entgegen, die den Zeilenfilter definiert, und liefert eine Abfrage zurück, die weiter bearbeitet werden kann.

	var query = todoItemTable.where({
	    complete: false
	}).read().done(function (results) {
	    alert(JSON.stringify(results));
	}, function (err) {
	    alert("Error: " + err);
	});

Durch den Aufruf von `where` im Abfrageobjekt und die Übergabe eines Objekts als Parameter weisen wir Mobile Services an, nur diejenigen Zeilen zurückzugeben, deren `complete`-Spalte den Wert `false` enthält. Beachten Sie in der folgenden URI, dass wir die eigentliche Abfragezeichenfolge verändern:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Sie können den URI der an den mobilen Dienst gesendeten Anforderung anzeigen, indem Sie Software zur Überprüfung von Nachrichten verwenden, wie z. B. Browser-Entwicklertools oder Fiddler.

Diese Anfrage entspricht normalerweise in etwa der folgenden SQL-Abfrage auf der Serverseite:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

Das an die `where`-Methode übergebene Objekt kann beliebig viele Parameter enthalten, die alle als AND-Klauseln für die Abfrage interpretiert werden. Die folgende Zeile:

	query.where({
	   complete: false,
	   assignee: "david",
	   difficulty: "medium"
	}).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Lässt sich (für die bereits gezeigte Abfrage) in etwa wie folgt übersetzen:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0
	      AND assignee = 'david'
	      AND difficulty = 'medium'

Die obige `where`-Anweisung und die SQL-Abfrage suchen nach unvollständigen Einträgen für "david", wobei "difficulty" = "medium" ist.

Dieselbe Abfrage kann auch auf eine andere Art geschrieben werden. Ein `.where`-Aufruf an das Abfrageobjekt fügt der `WHERE`-Klausel einen `AND`-Ausdruck hinzu. Wir hätten das Ganze also in drei Zeilen schreiben können:

	query.where({
	   complete: false
	});
	query.where({
	   assignee: "david"
	});
	query.where({
	   difficulty: "medium"
	});

Oder mit der Fluent-API:

	query.where({
	   complete: false
	})
	   .where({
	   assignee: "david"
	})
	   .where({
	   difficulty: "medium"
	});

Beide Methoden sind äquivalent und können frei austauschbar verwendet werden. Alle bisherigen `where`-Aufrufe verwenden ein Objekt mit Parametern und prüfen auf Gleichheit mit den Daten in der Datenbank. Für die Abfragemethode gibt es jedoch noch eine weitere Überladung, die eine Funktion anstelle eines Objekts entgegennimmt. In dieser Funktion können wir komplexere Ausdrücke schreiben und Operatoren wie z. B. Ungleichheit oder andere relationale Operatoren verwenden. In diesen Funktionen ist das Schlüsselwort `this` an das Serverobjekt gebunden.

Der Funktionstext wird in einen booleschen OData (Open Data Protocol)-Ausdruck übersetzt, der anschließend an einen Abfragezeichenfolgenparameter übergeben wird. Es ist möglich, Funktionen ohne Parameter zu übergeben:

    query.where(function () {
       return this.assignee == "david" && (this.difficulty == "medium" || this.difficulty == "low");
    }).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });


Bei der Übergabe einer Funktion mit Parametern werden alle Argumente nach der `where`-Klausel in deren Reihenfolge an die Funktionsparameter gebunden. Alle Objekte, die von außerhalb des Funktionsbereichs stammen, MÜSSEN als Parameter übergeben werden - die Funktion kann keine externen Variablen erfassen. In den nächsten beiden Beispielen wird das Argument "david" an den Parameter `name` gebunden, und im ersten Beispiel wird außerdem das Argument "medium" an den Parameter `level` gebunden. Außerdem muss die Funktion aus einer einzelnen `return`-Anweisung mit einem unterstützten Ausdruck bestehen, wie hier gezeigt:

	 query.where(function (name, level) {
	    return this.assignee == name && this.difficulty == level;
	 }, "david", "medium").read().done(function (results) {
	    alert(JSON.stringify(results));
	 }, function (err) {
	    alert("Error: " + err);
	 });

Solange wir also die Regeln beachten, können wir komplexere Filter zu unseren Datenbankabfragen hinzufügen, wie hier gezeigt:

    query.where(function (name) {
       return this.assignee == name &&
          (this.difficulty == "medium" || this.difficulty == "low");
    }, "david").read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });

`where` kann mit `orderBy`, `take` und `skip` kombiniert werden. Weitere Details finden Sie im nächsten Abschnitt.

### <a name="sorting"></a>Gewusst wie: Sortieren zurückgegebener Daten

Der folgende Code zeigt, wie Sie Daten mithilfe einer `orderBy`- oder `orderByDescending`-Funktion in der Abfrage sortieren können. Die Abfrage gibt Elemente aus der Tabelle `todoItemTable` aufsteigend sortiert nach dem `text`-Feld zurück. Standardmäßig gibt der Server nur die ersten 50 Elemente zurück.

> [AZURE.NOTE]Standardmäßig wird eine servergesteuerte Seitengröße verwendet, um zu verhindern, dass alle Elemente zurückgegeben werden. Damit wird verhindert, dass Standardabfragen für große Datensätze den Dienst negativ beeinflussen. Sie können `take` aufrufen, um die Anzahl der zurückzugebenden Elemente zu erhöhen, wie im nächsten Abschnitt beschrieben. `todoItemTable` ist der Verweis auf die Tabelle im mobilen Dienst, die wir zuvor erstellt haben.

	var ascendingSortedTable = todoItemTable.orderBy("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

	var descendingSortedTable = todoItemTable.orderBy("text").orderByDescending("text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

### <a name="paging"></a>Gewusst wie: Seitenweises Zurückgeben von Daten

Standardmäßig gibt Mobile Services nur 50 Zeilen in einer Anforderung zurück, es sei denn, der Client fordert explizit weitere Daten in der Antwort an. Der folgende Code zeigt, wie Sie mithilfe der `take`-Klausel und der `skip`-Klausel in der Abfrage einen Seitenverwaltungsmechanismus in den zurückgegebenen Daten implementieren. Die folgende Abfrage liefert die ersten drei Elemente aus der Tabelle zurück.

	var query = todoItemTable.take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Beachten Sie, dass die `take(3)`-Methode im Abfrage-URI in die Abfrageoption `$top=3` übersetzt wurde.

Die folgende geänderte Abfrage überspringt die ersten drei Ergebnisse und liefert die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

	var query = todoItemTable.skip(3).take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Sehen Sie sich erneut die URI der Anfrage an den mobilen Dienst an. Beachten Sie, dass die `skip(3)`-Methode im Abfrage-URI in die Abfrageoption `$skip=3` übersetzt wurde.

In diesem vereinfachten Szenario werden hartcodierte Pagingwerte an die `take`- und `skip`-Funktion übergeben. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren.

### <a name="selecting"></a>Gewusst wie: Auswählen bestimmter Spalten

Sie können angeben, welche Eigenschaften in den Ergebnissen enthalten sein sollen, indem Sie der Abfrage eine `select`-Klausel hinzufügen. Der folgende Code gibt beispielsweise die Eigenschaften `id`, `complete` und `text` aus jeder Zeile in `todoItemTable` zurück:

	var query = todoItemTable.select("id", "complete", "text").read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	})

Die Parameter der select-Funktion sind in diesem Fall die Namen der Tabellenspalten, die Sie zurückgeben möchten.


Alle bisher beschriebenen Funktionen sind additiv, d. h. wir können sie immer wieder aufrufen und bei jedem Aufruf einen größeren Teil der Abfrage beeinflussen. Ein weiteres Beispiel:

    query.where({
       complete: false
    })
       .select('id', 'assignee')
       .orderBy('assignee')
       .take(10)
       .read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);

### <a name="lookingup"></a>Gewusst wie: Abrufen von Daten nach ID

Die `lookup`-Funktion nimmt nur den `id`-Wert entgegen und gibt das Objekt aus der Datenbank mit der entsprechenden ID zurück. Die `id`-Spalten in Datenbanken enthalten entweder Ganzzahlen oder Zeichenfolgen. Standardmäßig werden Zeichenfolgen für die `id`-Spalte verwendet.

	todoItemTable.lookup("37BBF396-11F0-4B39-85C8-B319C729AF6D").done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	})

##<a name="odata-query"></a>Ausführen eines OData-Abfragevorgangs

Mobile Services verwenden die URI-Konventionen für OData-Abfragen zum Erstellen und Ausführen von REST-Abfragen. Nicht alle OData-Abfragen können mithilfe der integrierten Abfragefunktionen erstellt werden. Dies gilt insbesondere für komplexe Filtervorgänge wie der Suche nach einer Teilzeichenfolge in einer Eigenschaft. Für diese komplexen Abfragen können Sie eine beliebige gültige Optionszeichenfolge für OData-Abfragen wie folgt an die `read`-Funktion übergeben:

	function refreshTodoItems() {
	    todoItemTable.read("$filter=substringof('search_text',text)").then(function(items) {
	        var itemElements = $.map(items, createUiForTodoItem);
	        $("#todo-items").empty().append(itemElements);
	        $("#no-items").toggle(items.length === 0);
	    }, handleError);
	}

>[AZURE.NOTE]Wenn Sie für die `read`-Funktion eine unformatierte Optionszeichenfolge für OData-Abfragen angeben, können Sie in derselben Abfrage nicht gleichzeitig Methoden des Abfrage-Generators verwenden. In diesem Fall müssen Sie die gesamte Abfrage als OData-Abfragezeichenfolge erstellen. Weitere Informationen zu OData-Systemabfrageoptionen finden Sie in der [Referenz zu OData-Systemabfrageoptionen].

##<a name="inserting"></a>Gewusst wie: Einfügen von Daten in einen mobilen Dienst

Der folgende Code zeigt, wie Sie neue Zeilen in eine Tabelle einfügen können. Der Client fügt eine Zeile mit Daten ein, indem er eine POST-Anforderung an den mobilen Dienst schickt. Der Anforderungstext enthält die einzufügenden Daten als JSON-Objekt.

	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	})

Dieser Code fügt Daten aus dem angegebenen JSON-Objekt in die Tabelle ein. Sie können auch eine Rückruffunktion angeben, die nach Ausführung des Einfügevorgangs aufgerufen werden soll:

	todoItemTable.insert({
	   text: "New Item",
	   complete: false
	}).done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	});

###Arbeiten mit ID-Werten

Mobile Services unterstützt eindeutige benutzerdefinierte Zeichenfolgenwerte für die Spalte **id** der Tabelle. Auf diese Weise können Anwendungen benutzerdefinierte Werte wie E-Mail-Adressen oder Benutzernamen für die ID verwenden. Der folgende Code fügt z. B. ein neues Element als JSON-Objekt ein, wobei die eindeutige ID eine E-Mail-Adresse ist:

	todoItemTable.insert({
	   id: "myemail@domain.com",
	   text: "New Item",
	   complete: false
	});

Zeichenfolgen-IDs bieten Ihnen die folgenden Vorteile:

+ IDs werden ohne Roundtrip zur Datenbank generiert.
+ Datensätze aus unterschiedlichen Tabellen oder Datenbanken lassen sich leichter zusammenführen.
+ ID-Werte lassen sich möglicherweise leichter in die Anwendungslogik integrieren.

Wenn der ID-Wert einer Zeichenfolge für eingefügte Datensätze nicht bereits festgelegt ist, generiert Mobile Services einen eindeutigen Wert für die ID. Weitere Informationen zum Generieren eigener ID-Werte auf dem Client oder in einem .NET-Back-End finden Sie unter [Gewusst wie: Generieren eindeutiger ID-Werte](mobile-services-how-to-use-server-scripts.md#generate-guids).

Alternativ können Sie auch ganzzahlige IDs für Ihre Tabellen verwenden. Um ganzzahlige IDs zu verwenden, müssen Sie bei der Tabellenerstellung für den `mobile table create`-Befehl die Option `--integerId` verwenden. Dieser Befehl wird in der Befehlszeilenschnittstelle (CLI) für Azure verwendet. Weitere Informationen zur CLI finden Sie unter [CLI to manage Mobile Services tables](../virtual-machines-command-line-tools.md#Mobile_Tables) (CLI für Tabellen in mobilen Diensten, in englischer Sprache).

##<a name="modifying"></a>Gewusst wie: Ändern von Daten in einem mobilen Dienst

Der folgende Code zeigt, wie Sie Daten in einer Tabelle ändern können. Der Client ändert die Daten in einer Zeile, indem er eine PATCH-Anforderung an den mobilen Dienst schickt. Der Anforderungstext enthält die zu aktualisierenden Felder als JSON-Objekt. Dadurch wird ein vorhandenes Element in der Tabelle `todoItemTable` aktualisiert.

	todoItemTable.update({
	   id: idToUpdate,
	   text: newText
	})

Der erste Parameter definiert die zu aktualisierende Instanz in der Tabelle anhand der ID.

Sie können auch eine Rückruffunktion angeben, die nach Ausführung des Aktualisierungsvorgangs aufgerufen werden soll:

	todoItemTable.update({
	   id: idToUpdate,
	   text: newText
	}).done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	});

##<a name="deleting"></a>Gewusst wie: Löschen von Daten in einem mobilen Dienst

Der folgende Code zeigt, wie Sie Daten in einer Tabelle löschen können. Der Client löscht eine Datenzeile, indem er eine DELETE-Anforderung an den mobilen Dienst schickt. Dieser Code löscht ein vorhandenes Element in der Tabelle todoItem.

	todoItemTable.del({
	   id: idToDelete
	})

Der erste Parameter definiert die zu löschende Instanz in der Tabelle anhand der ID.

Sie können auch eine Rückruffunktion angeben, die nach Ausführung des Löschvorgangs aufgerufen werden soll:

	todoItemTable.del({
	   id: idToDelete
	}).done(function () {
	   /* Do something */
	}, function (err) {
	   alert("Error: " + err);
	});

##<a name="binding"></a>Gewusst wie: Anzeigen von Daten in der Benutzeroberfläche

Dieser Abschnitt beschreibt das Anzeigen von Datenobjekten in GUI-Elementen. Zum Abfragen von Elementen in `todoItemTable` und zum Anzeigen der Elemente in einer sehr einfachen Liste können Sie den folgenden Beispielcode ausführen. In diesem Beispiel wird keinerlei Auswahl, Filterung oder Sortierung durchgeführt.

	var query = todoItemTable;

	query.read().then(function (todoItems) {
	   // The space specified by 'placeToInsert' is an unordered list element <ul> ... </ul>
	   var listOfItems = document.getElementById('placeToInsert');
	   for (var i = 0; i < todoItems.length; i++) {
	      var li = document.createElement('li');
	      var div = document.createElement('div');
	      div.innerText = todoItems[i].text;
	      li.appendChild(div);
	      listOfItems.appendChild(li);
	   }
	}).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

In Windows Store-Apps können Sie mit dem Abfrageergebnis ein [WinJS.Binding.List]-Objekt erstellen, das als Datenquelle für ein [ListView]-Objekt gebunden werden kann. Weitere Informationen finden Sie unter [Datenbindung (Windows Store-Apps mit JavaScript und HTML)].

##<a name="#custom-api"></a>Gewusst wie: Aufrufen einer benutzerdefinierten API

Mit einer benutzerdefinierten API können Sie benutzerdefinierte Endpunkte definieren, die Serverfunktionen zur Verfügung stellen, welche keinem Einfüge-, Aktualisierungs-, Lösch- oder Lesevorgang zugeordnet sind. Durch die Verwendung einer benutzerdefinierten API erhalten Sie mehr Kontrolle über das Messaging, einschließlich Lesen und Einstellen der HTTP-Nachrichten-Header sowie Definieren eines von JSON abweichenden Nachrichtentextformats. Ein Beispiel für das Erstellen einer benutzerdefinierten API in Ihrem mobilen Dienst finden Sie unter [Gewusst wie: Definieren eines benutzerdefinierten API-Endpunkts](mobile-services-dotnet-backend-define-custom-api.md).

Sie können eine benutzerdefinierte API vom Client aufrufen, indem Sie die [InvokeApi](https://github.com/Azure/azure-mobile-services/blob/master/sdk/Javascript/src/MobileServiceClient.js#L337)-Methode für **MobileServiceClient** aufrufen. Mit der folgenden Codezeile wird beispielsweise eine POST-Anforderung an die **completeAll**-API im mobilen Dienst gesendet:

    client.invokeApi("completeall", {
        body: null,
        method: "post"
    }).done(function (results) {
        var message = results.result.count + " item(s) marked as complete.";
        alert(message);
        refreshTodoItems();
    }, function(error) {
        alert(error.message);
    });

 
Beispiele mit mehr Praxisnähe und eine ausführlichere Erörterung von **invokeApi** finden Sie unter [Benutzerdefinierte API in Azure Mobile Services Client SDKs](http://blogs.msdn.com/b/carlosfigueira/archive/2013/06/19/custom-api-in-azure-mobile-services-client-sdks.aspx).

##<a name="authentication"></a>Gewusst wie: Authentifizieren von Benutzern

Mobile Services unterstützt Authentifizierung und Autorisierung von Anwendungsbenutzern mit einer Vielzahl externer Identitätsanbieter: Facebook, Google, Microsoft-Konto und Twitter. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln in Serverskripts zu implementieren. Weitere Informationen finden Sie im Lernprogramm "Erste Schritte mit der Authentifizierung".

>[AZURE.NOTE]Wenn Sie Authentifizierung in einer PhoneGap- oder Cordova-App verwenden, müssen Sie dem Projekt auch die folgenden Plug-Ins hinzufügen:
>
>+ https://git-wip-us.apache.org/repos/asf/cordova-plugin-device.git
>+ https://git-wip-us.apache.org/repos/asf/cordova-plugin-inappbrowser.git


Insgesamt werden zwei Authentifizierungsflüsse unterstützt: ein _Serverfluss_ und ein _Clientfluss_. Der Serverfluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Authentifizierungs-Webschnittstelle des Anbieters verwendet wird. Der Clientfluss ermöglicht eine tiefere Integration mit gerätespezifischen Fähigkeiten wie z. B. einmalige Anmeldung, da in diesem Fall anbieterspezifische und gerätespezifische SDKs verwendet werden.

###Serverfluss
Sie müssen Ihre Anwendung bei Ihrem Identitätsanbieter registrieren, um den mobilen Diensten die Verwaltung des Authentifizierungsprozesses in Ihrer Windows Store- oder HTML5-App zu ermöglichen. Anschließend müssen Sie in Ihrem mobilen Dienst die Anwendungs-ID und den geheimen Schlüssel Ihres Anbieters konfigurieren. Weitere Informationen finden Sie im Lernprogramm [Authentifizierung zu Ihrer App hinzufügen](mobile-services-html-get-started-users.md).

Nach der Registrierung bei Ihrem Identitätsanbieter können Sie die [LoginAsync]-Methode mit dem [MobileServiceAuthenticationProvider]-Wert Ihres Anbieters aufrufen. Für die Facebook-Anmeldung verwenden Sie z. B. den folgenden Code.

	client.login("facebook").done(function (results) {
	     alert("You are now logged in as: " + results.userId);
	}, function (err) {
	     alert("Error: " + err);
	});

Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den an die `login`-Methode oben übergebenen Wert in einen der folgenden Werte: `microsoftaccount`, `facebook`, `twitter`, `google` oder `windowsazureactivedirectory`.

In diesem Fall verwaltet der mobile Dienst den OAuth 2.0-Authentifizierungsfluss, indem die Anmeldungsseite des ausgewählten Anbieters angezeigt und nach der erfolgreichen Anmeldung beim Identitätsanbieter ein Authentifizierungstoken für den mobilen Dienst generiert wird. Die [login]-Funktion gibt ein JSON-Objekt (**user**) zurück, das sowohl Benutzer-ID als auch Authentifizierungstoken für den mobilen Dienst in den Feldern **userId** bzw. **authenticationToken** zur Verfügung stellt. Dieses Token kann zwischen gespeichert und wiederverwendet werden, bis es abläuft. Weitere Informationen finden Sie unter [Zwischenspeichern des Authentifizierungstokens].

###Clientfluss
Ihre Anwendung kann den Identitätsanbieter auch unabhängig kontaktieren und das zurückgegebene Token zur Authentifizierung dem mobilen Dienst vorlegen. Mit diesem Clientfluss können Sie die einmalige Anmeldung für Ihre Benutzer implementieren oder zusätzliche Benutzerdaten vom Identitätsanbieter abrufen.

####Grundlegendes Beispiel für ein Facebook/Google-SDK

Dieses Beispiel verwendet die Client-SDK von Facebook für die Authentifizierung:

	client.login(
	     "facebook",
	     {"access_token": token})
	.done(function (results) {
	     alert("You are now logged in as: " + results.userId);
	}, function (err) {
	     alert("Error: " + err);
	});

Dieses Beispiel geht davon aus, dass das vom jeweiligen Anbieter gelieferte Token in der `token`-Variable gespeichert wird. Clientauthentifizierung für Twitter ist derzeit nicht möglich.

####Grundlegendes Beispiel für Microsoft-Konto
Das folgende Beispiel verwendet das Live SDK, das einmalige Anmeldung für Windows Store-Apps mit Microsoft-Konto unterstützt:

	WL.login({ scope: "wl.basic"}).then(function (result) {
	      client.login(
	            "microsoftaccount",
	            {"authenticationToken": result.session.authentication_token})
	      .done(function(results){
	            alert("You are now logged in as: " + results.userId);
	      },
	      function(error){
	            alert("Error: " + err);
	      });
	});

Dieses vereinfachte Beispiel ruft ein Token von Live Connect ab und übergibt das Token in einem Aufruf der [login]-Funktion an den mobilen Dienst.


####Vollständiges Beispiel für Microsoft-Konto

Das folgende Beispiel zeigt die Verwendung des Live-SDKs mit WinJS-APIs zum Bereitstellen einer verbesserten Single-Sign-On-Erfahrung:

	// Set the mobileClient variable to client variable generated by the tooling.
	var mobileClient = <yourClient>;

	var session = null;
	var login = function () {
		return new WinJS.Promise(function (complete) {
			WL.login({ scope: "wl.basic" }).then(function (result) {
				session = result.session;

				WinJS.Promise.join([
					WL.api({ path: "me", method: "GET" }),
					mobileClient.login(result.session.authentication_token)
				]).done(function (results) {
					// Build the welcome message from the Microsoft account info.
					var profile = results[0];                            
					var title = "Welcome " + profile.first_name + "!";
					var message = "You are now logged in as: "
						+ mobileClient.currentUser.userId;
					var dialog = new Windows.UI.Popups.MessageDialog(message, title);
					dialog.showAsync().then(function () {
						// Reload items from the mobile service.
						refreshTodoItems();
					}).done(complete);
					
				}, function (error) {

				});
			}, function (error) {
				session = null;
				var dialog = new Windows.UI.Popups.MessageDialog("You must log in.", "Login Required");
				dialog.showAsync().done(complete);
			});
		});
	}

	var authenticate = function () {
		// Block until sign-in is successful.
		login().then(function () {
			if (session === null) {
				// Authentication failed, try again.
				authenticate();
			}
		});
	}

	// Initialize the Live client.
	WL.init({
		redirect_uri: mobileClient.applicationUrl
	});

	// Start the sign-in process.
	authenticate();

Dies initialisiert den Live Connect-Client, sendet eine neue Anmelde-Anforderung an das Microsoft-Konto, sendet das zurückgegebene Authentifizierungstoken an Mobile Services und zeigt anschließend Informationen über den angemeldeten Benutzer an. Die App startet erst, wenn die Authentifizierung erfolgreich ist.

###Zwischenspeichern des Authentifizierungstokens
Manche Aufrufe der Anmeldemethode lassen sich vermeiden, nachdem sich der Benutzer authentifiziert hat. Sie können entweder [sessionStorage] oder [localStorage] verwenden, um die Identität des aktuellen Benutzers bei der ersten Anmeldung zwischenzuspeichern und später bei Bedarf prüfen, ob Sie die Benutzeridentität bereits im Speicher haben. Falls der Zwischenspeicher leer ist oder ein Aufruf fehlschlägt (d. h. die aktuelle Anmeldesitzung abgelaufen ist), müssen wir dennoch den Anmeldeprozess durchlaufen.

    // After logging in
    sessionStorage.loggedInUser = JSON.stringify(client.currentUser);

    // Log in
    if (sessionStorage.loggedInUser) {
       client.currentUser = JSON.parse(sessionStorage.loggedInUser);
    } else {
       // Regular login flow
   }

     // Log out
    client.logout();
    sessionStorage.loggedInUser = null;

##<a name="push-notifications"></a>Gewusst wie: Registrieren für Pushbenachrichtigungen

Für PhoneGap- oder Apache Cordova-Apps in HTML/JavaScript ermöglicht die systemeigene mobile Plattform das Empfangen von Pushbenachrichtigungen auf dem Gerät. Mit dem [Apache Cordova-Plug-In für Azure Mobile Services](https://github.com/Azure/azure-mobile-services-cordova) können Sie die App für Pushbenachrichtigungen mit Azure Notification Hubs registrieren. Welcher Benachrichtigungsdienst verwendet wird, ist von der systemeigenen Plattform abhängig, auf der der Code ausgeführt wird. Ein Beispiel hierzu finden Sie unter [Use Microsoft Azure to push notifications to Cordova apps](https://github.com/Azure/mobile-services-samples/tree/master/CordovaNotificationsArticle) (Microsoft Azure für Pushbenachrichtigungen an Cordova-Apps verwenden, in englischer Sprache).

>[AZURE.NOTE]Dieses Plug-In unterstützt derzeit nur iOS- und Android-Geräte. Eine Lösung, die auch Windows-Geräte umfasst, finden Sie im Artikel [Push Notifications to PhoneGap Apps using Notification Hubs Integration](http://blogs.msdn.com/b/azuremobile/archive/2014/06/17/push-notifications-to-phonegap-apps-using-notification-hubs-integration.aspx) (Pushbenachrichtigungen für PhoneGap-Apps mit Notification Hubs-Integration, in englischer Sprache).

##<a name="errors"></a>Gewusst wie: Fehlerbehandlung

Mobile Dienste bieten verschiedene Möglichkeiten zur Erkennung, Validierung und Behebung von Fehlern.

Ein Beispiel sind Serverskripts, die in einem mobilen Dienst registriert sind und für eine Vielzahl von Operationen auf einzufügende und zu aktualisierende Daten verwendet werden können, inklusive Validierung und Änderung der Daten. Sie könnten z. B. das folgende Serverskript erstellen und registrieren, das Daten validiert und ändert:

	function insert(item, user, request) {
	   if (item.text.length > 10) {
		  request.respond(statusCodes.BAD_REQUEST, { error: "Text cannot exceed 10 characters" });
	   } else {
	      request.execute();
	   }
	}

Das Serverskript validiert die Länge von Zeichenfolgen, die an den mobilen Dienst geschickt werden, und lehnt zu lange Zeichenfolgen ab (in diesem Fall länger als 10 Zeichen).

Nun ist Ihr mobiler Dienst in der Lage, Daten zu validieren und Fehlermeldungen im Server zu generieren und Sie können Ihre HTML-Anwendung erweitern, sodass diese mit den Validierungsfehlern umgehen kann.

	todoItemTable.insert({
	   text: itemText,
	   complete: false
	})
	   .then(function (results) {
	   alert(JSON.stringify(results));
	}, function (error) {
	   alert(JSON.parse(error.request.responseText).error);
	});


Zusätzlich können Sie sogar den Fehler-Handler bei jedem Datenzugriff als zweites Argument übergeben:

	function handleError(message) {
	   if (window.console && window.console.error) {
	      window.console.error(message);
	   }
	}

	client.getTable("tablename").read()
		.then(function (data) { /* do something */ }, handleError);

##<a name="promises"></a>Gewusst wie: Einsatz von Zusagen

Promises bieten einen Mechanismus zur Planung von Operationen auf einen Wert, der noch nicht berechnet wurde. Es handelt sich um eine Abstraktion für Interaktionen mit asynchronen APIs.

Die `done`-Zusage wird ausgeführt, sobald die übergebene Funktion entweder erfolgreich abgeschlossen wurde oder ein Fehler aufgetreten ist. Im Gegensatz zur `then`-Zusage ist in diesem Fall gewährleistet, dass alle nicht innerhalb der Funktion behandelten Fehler ausgelöst werden. Nachdem die Handlerausführung abgeschlossen ist, löst diese Funktion alle Fehler aus, die von der then-Zusage im Fehlerstatus zurückgegeben worden wären. Weitere Informationen finden Sie unter [done].

	promise.done(onComplete, onError);

Wie folgt:

	var query = todoItemTable;
	query.read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Die `then`-Zusage hat dieselbe Funktion wie die `done`-Zusage, aber im Gegensatz zur `then`-Zusage ist bei `done` garantiert, dass alle nicht in der Funktion behandelten Fehler ausgelöst werden. Falls Sie keinen Fehlerhandler für `then` bereitstellen und ein Fehler im Vorgang auftritt, wird keine Ausnahme ausgelöst, sondern eine Zusage im Fehlerzustand zurückgegeben. Weitere Informationen finden Sie unter [then].

	promise.then(onComplete, onError).done( /* Your success and error handlers */ );

Wie folgt:

	var query = todoItemTable;
	query.read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Sie können Promises auf verschiedene Arten einsetzen. Sie können Zusagevorgänge verketten, indem Sie `then` oder `done` für die Zusage aufrufen, die von der vorherigen `then`-Funktion zurückgegeben wird. Verwenden Sie `.then().then()` für die Zwischenschritte des Vorgangs (z. B. `then`) und `done` für die den letzten Schritt des Vorgangs (z. B. `.then().then().done()`). Sie können mehrere `then`-Funktionen verketten, da `then` eine Zusage zurückgibt. Sie können nicht mehr als eine `done`-Methode verketten, da die Rückgabe nicht definiert ist. [Weitere Informationen zum Unterschied zwischen then und done].

	todoItemTable.insert({
	   text: "foo"
	}).then(function (inserted) {
	   inserted.newField = 123;
	   return todoItemTable.update(inserted);
	}).done(function (insertedAndUpdated) {
	   alert(JSON.stringify(insertedAndUpdated));
	})

##<a name="customizing"></a>Gewusst wie: Anpassen der Client-Anforderungsheader

Sie können benutzerdefinierte Anforderungsheader mit der `withFilter`-Funktion senden, wobei beliebige Eigenschaften der im Filter zu sendenden Anforderung gelesen und geschrieben werden. Diese benutzerdefinierten HTTP-Header machen z. B. Sinn, um Werte an serverseitige Skripts zu übergeben.

	var client = new WindowsAzure.MobileServiceClient('https://your-app-url', 'your-key')
	   .withFilter(function (request, next, callback) {
	   request.headers.MyCustomHttpHeader = "Some value";
	   next(request, callback);
	});

Filter haben noch zahlreiche weitere Funktionen neben der Anpassung von Anforderungsheadern. Mit Filtern können Sie Anforderungen untersuchen oder ändern, Antworten untersuchen oder ändern, Netzwerkaufrufe umgehen, mehrere Aufrufe verschicken etc.

##<a name="hostnames"></a>Gewusst wie: Verwenden von Cross-Origin Resource Sharing (CORS)

Um zu steuern, welche Websites mit Ihrem mobilen Dienst interagieren und Anforderungen senden dürfen, sollten Sie den Hostnamen der Website, die Sie zum Hosten verwenden, der CORS (Cross-Origin Resource Sharing)-Positivliste hinzufügen. Für einen mobilen Dienst mit JavaScript-Back-End konfigurieren Sie die Positivliste auf der Registerkarte "Konfigurieren" im [Azure-Verwaltungsportal](https://manage.windowsazure.com). Sie können bei Bedarf Platzhalter verwenden. Neue mobile Dienste weisen Browser standardmäßig an, nur Zugriff von `localhost` zuzulassen, und Cross-Origin Resource Sharing (CORS) gestattet es dem JavaScript-Code in einem Browser unter einem externen Hostnamen, mit Ihrem mobilen Dienst zu interagieren. Diese Konfiguration wird für WinJS-Anwendungen nicht benötigt.

<!-- Anchors. -->
[What is Mobile Services]: #what-is
[Concepts]: #concepts
[How to: Create the Mobile Services client]: #create-client
[How to: Query data from a mobile service]: #querying
[Filter returned data]: #filtering
[Sort returned data]: #sorting
[Return data in pages]: #paging
[Select specific columns]: #selecting
[Look up data by ID]: #lookingup
[How to: Display data in the user interface]: #binding
[How to: Insert data into a mobile service]: #inserting
[How to: Modify data in a mobile service]: #modifying
[How to: Delete data in a mobile service]: #deleting
[How to: Authenticate users]: #authentication
[How to: Handle errors]: #errors
[How to: Use promises]: #promises
[How to: Customize request headers]: #customizing
[How to: Use cross-origin resource sharing]: #hostnames
[Next steps]: #nextsteps
[Execute an OData query operation]: #odata-query



<!-- URLs. -->
[then]: http://msdn.microsoft.com/library/windows/apps/br229728.aspx
[done]: http://msdn.microsoft.com/library/windows/apps/hh701079.aspx
[Weitere Informationen zum Unterschied zwischen then und done]: http://msdn.microsoft.com/library/windows/apps/hh700334.aspx
[how to handle errors in promises]: http://msdn.microsoft.com/library/windows/apps/hh700337.aspx

[sessionStorage]: http://msdn.microsoft.com/library/cc197062(v=vs.85).aspx
[localStorage]: http://msdn.microsoft.com/library/cc197062(v=vs.85).aspx

[ListView]: http://msdn.microsoft.com/library/windows/apps/br211837.aspx
[Datenbindung (Windows Store-Apps mit JavaScript und HTML)]: http://msdn.microsoft.com/library/windows/apps/hh758311.aspx
[login]: https://github.com/Azure/azure-mobile-services/blob/master/sdk/Javascript/src/MobileServiceClient.js#L301
[Authenticate your app with single sign-in]: mobile-services-windows-store-javascript-single-sign-on.md
[ASCII control codes C0 and C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[Referenz zu OData-Systemabfrageoptionen]: http://go.microsoft.com/fwlink/p/?LinkId=444502
[Call a custom API from the client]: mobile-services-html-call-custom-api.md
 

<!---HONumber=Oct15_HO1-->