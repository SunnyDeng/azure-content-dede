<properties urlDisplayName="HTML Client" pageTitle="Verwenden eines HTML-Clients - Azure Mobile Services" metaKeywords="Azure Mobile Services, Mobile Service HTML client, HTML client" description="Erfahren Sie mehr über die Verwendung eines HTML-Clients für Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="How to use an HTML/JavaScript client for Azure Mobile Services" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-html" ms.devlang="javascript" ms.topic="article" ms.date="11/21/2014" ms.author="glenga" />


# So verwenden Sie einen HTML-/JavaScript-Client für Azure Mobile Services

<div class="dev-center-tutorial-selector sublanding">
  <a href="/de-de/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/de-de/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript" class="current">HTML/JavaScript</a><a href="/de-de/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/de-de/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/de-de/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>


Dieser Artikel beschreibt gängige Szenarien für die Verwendung eines HTML/JavaScript-Clients für mobile Dienste in Azure. Besprochen werden unter anderem Datenabfragen, Einfügen, Aktualisieren und Löschen von Daten, Authentifizierung von Benutzern und Fehlerbehandlung. Wenn Sie noch keine Erfahrungen mit mobilen Diensten haben, sollten Sie möglicherweise zunächst die Schnellstart-Lernprogramme [Windows Store JavaScript-Schnellstart] oder [HTML-Schnellstart] lesen. Im Schnellstart-Lernprogramm lernen Sie, Ihr Konto zu konfigurieren und Ihren ersten mobilen Dienst zu erstellen.


## Inhaltsverzeichnis

- [Was sind Mobile Services?]
- [Konzepte]
- [Gewusst wie: Erstellen des Mobile Services-Clients]
- [Gewusst wie: Abfragen von Daten aus einem mobilen Dienst]
	- [Zurückgegebene Daten filtern]
    - [Zurückgegebene Daten sortieren]
	- [Daten seitenweise zurückgeben]
	- [Bestimmte Spalten auswählen]
	- [Daten nach ID abrufen]
	- [Einen OData-Abfragevorgang ausführen]
- [Gewusst wie: Einfügen von Daten in einen mobilen Dienst]
- [Gewusst wie: Ändern von Daten in einem mobilen Dienst]
- [Gewusst wie: Löschen von Daten in einem mobilen Dienst]
- [Gewusst wie: Anzeigen von Daten in der Benutzeroberfläche]
- [Gewusst wie: Authentifizieren von Benutzern]
- [Gewusst wie: Fehlerbehandlung]
- [Gewusst wie: Einsatz von Zusagen]
- [Gewusst wie: Anpassen der Anforderungsheader]
- [Gewusst wie: Verwenden von Cross-Origin Resource Sharing (CORS)]
- [Nächste Schritte]

[WACOM.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

##<a name="create-client"></a>Gewusst wie: Erstellen des mobile Dienste-Clients



Öffnen Sie die HTML-Datei in Ihrem Web-Editor und fügen Sie den folgenden Code zu den Skriptverweisen der Seite hinzu:

    <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

>[WACOM.NOTE]Für eine in JavaScript/HTML geschriebene Windows Store-App müssen Sie Ihrem Projekt nur das NuGet-Paket **WindowsAzure.MobileServices.WinJS** hinzufügen.

Öffnen oder erstellen Sie eine JavaScript-Datei im Editor und fügen Sie den folgenden Code hinzu, um die "MobileServiceClient"-Variable zu definieren und übergeben Sie Anwendungs-URL und -Schlüssel in dieser Reihenfolge an den "MobileServiceClient"-Konstruktor.

	var MobileServiceClient = WindowsAzure.MobileServiceClient;
    var client = new MobileServiceClient('AppUrl', 'AppKey');

Ersetzen Sie den Platzhalter "AppUrl" durch die Anwendungs-URL Ihres mobilen Dienstes und "AppKey" durch den Anwendungsschlüssel. Im Lernprogramm [Erste Schritte mit Daten in Windows Store JavaScript] oder [Erste Schritte mit Daten in HTML/JavaScript] erfahren Sie, wie Sie Anwendungs-URL und -Schlüssel Ihres mobilen Dienstes abfragen können.

##<a name="querying"></a>Gewusst wie: Abfragen von Daten aus einem mobilen Dienst

Jeglicher Code zum Abrufen oder Ändern von Daten in der Tabelle der SQL-Datenbank ruft Funktionen des "MobileServiceTable"-Objekts auf. Sie erhalten einen Verweis auf die Tabelle, indem Sie die "getTable ()"-Funktion einer Instanz von "MobileServiceClient" aufrufen.

    var todoItemTable = client.getTable('todoitem');


### <a name="filtering"></a>Gewusst wie: Zurückgegebene Daten filtern

Der folgende Code zeigt, wie Sie Daten mithilfe einer "Where"-Klausel in einer Abfrage filtern können. Die Abfrage gibt alle Elemente aus "todoItemTable" zurück, deren Wert im Feld "Complete" gleich "false" ist. "todoItemTable" ist ein Verweis auf die zuvor erstellte Tabelle in Ihrem mobilen Dienst. Die Where-Funktion wendet ein Zeilenfilterungsprädikat auf die Tabellenabfrage an. Die Funktion nimmt ein JSON-Objekt oder eine Funktion entgegen, die den Zeilenfilter definiert, und liefert eine Abfrage zurück, die weiter bearbeitet werden kann.

	var query = todoItemTable.where({
	    complete: false
	}).read().done(function (results) {
	    alert(JSON.stringify(results));
	}, function (err) {
	    alert("Error: " + err);
	});

Durch die Verwendung von "where" im Abfrageobjekt und die Übergabe eines Objekts als Parameter weisen wir den mobilen Dienst an, nur diejenigen Zeilen zurückzugeben, deren "complete"-Spalte den Wert "false" enthält. Beachten Sie in der folgenden URI, dass wir die eigentliche Abfragezeichenfolge verändern:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Sie können den URI der an den mobilen Dienst gesendeten Anforderung anzeigen, indem Sie Software zur Überprüfung von Nachrichten verwenden, wie z. B. Browser-Entwicklertools oder Fiddler.

Diese Anfrage entspricht normalerweise in etwa der folgenden SQL-Abfrage auf der Serverseite:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

Das an die "Where"-Methode übergebene Objekt kann beliebig viele Bedingungen enthalten, die alle als AND-Klauseln für die Abfrage interpretiert werden. Die folgende Zeile:

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

Die obige "Where"-Anweisung und die SQL-Abfrage suchen nach unvollständigen Einträgen für "david" mit difficulty = "medium".

Dieselbe Abfrage kann auch auf eine andere Art geschrieben werden. Ein ".where"-Aufruf an das Abfrageobjekt fügt einen AND-Ausdruck zur WHERE-Klausel hinzu. Wir hätten die Abfrage also in drei Zeilen schreiben können:

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

Beide Methoden sind äquivalent und können frei austauschbar verwendet werden. Alle bisherigen "Where"-Aufrufe nehmen ein Objekt mit Parametern entgegen und prüfen auf Gleichheit mit den Daten in der Datenbank. Für die Abfragemethode gibt es jedoch noch eine weitere Überladung, die eine Funktion anstelle eines Objekts entgegennimmt. In dieser Funktion können wir komplexere Ausdrücke schreiben und Operatoren wie z. B. Ungleichheit oder andere relationale Operatoren verwenden. In diesen Funktionen ist das Schlüsselwort "this" an das Serverobjekt gebunden.

Der Funktionstext wird in einen booleschen OData (Open Data Protocol)-Ausdruck übersetzt, der anschließend an einen Abfragezeichenfolgenparameter übergeben wird. Es ist möglich, Funktionen ohne Parameter zu übergeben:

    query.where(function () {
       return this.assignee == "david" && (this.difficulty == "medium" || this.difficulty == "low");
    }).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });


Bei der Übergabe von Funktionen ohne Parameter werden alle Argumente nach der "Where"-Klausel in deren Reihenfolge an die Funktionsparameter gebunden. Alle Objekte, die von außerhalb des Funktionsbereichs stammen, MÜSSEN als Parameter übergeben werden - die Funktion kann keine externen Variablen erfassen. In den nächsten beiden Beispielen wird das Argument "david" an den Parameter "name" gebunden, und im ersten Beispiel wird außerdem das Argument "medium" an den Parameter "level" gebunden. Außerdem muss die Funktion aus einer einzigen "return"-Anweisung bestehen, wie hier gezeigt:

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

Zudem kann "where" mit "orderBy", "take" und "skip" kombiniert werden. Weitere Details finden Sie im nächsten Abschnitt.

### <a name="sorting"></a>Gewusst wie: Zurückgegebene Daten sortieren

Der folgende Code zeigt, wie Sie Daten mithilfe einer "orderBy"- oder einer "orderByDescending"-Klausel in einer Abfrage sortieren können. Die Abfrage liefert Elemente aus der Tabelle "todoItemTable" aufsteigend sortiert nach dem "text"-Feld zurück. Standardmäßig gibt der Server nur die ersten 50 Elemente zurück.

<div class="dev-callout"><strong>Hinweis</strong> <p>Standardmäßig wird eine servergesteuerte Seitengröße verwendet, um zu verhindern, dass alle Elemente zurückgegeben werden. Damit wird verhindert, dass Standardabfragen für große Datensätze den Dienst negativ beeinflussen. </p> </div>
>
Sie können "take" aufrufen, um die Anzahl der zurückgegebenen Elemente zu erhöhen, wie im nächsten Abschnitt beschrieben. "todoItemTable" ist ein Verweis auf die zuvor erstellte Tabelle in Ihrem mobilen Dienst.

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

### <a name="paging"></a>Gewusst wie: Daten seitenweise zurückgeben

Der folgende Code zeigt, wie Sie mithilfe der "Take"- und "Skip"-Klausel in Ihrer Abfrage einen Seitenverwaltungsmechanismus umsetzen können.  Die folgende Abfrage liefert die ersten drei Elemente aus der Tabelle zurück.

	var query = todoItemTable.take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Beachten Sie, dass die "Take(3)"-Methode im Abfrage-URI als "$top=3" übersetzt wurde.

Die folgende geänderte Abfrage überspringt die ersten drei Ergebnisse und liefert die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

	var query = todoItemTable.skip(3).take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Sehen Sie sich erneut die URI der Anfrage an den mobilen Dienst an. Beachten Sie, dass die "skip(3)"-Methode in der Abfrage-URI als "$skip=3" übersetzt wurde.

Dieses vereinfachte Szenario übergibt fest codierte Werte an die "Take"- und "Skip"-Funktionen. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren.

### <a name="selecting"></a>Gewusst wie: Bestimmte Spalten auswählen

Sie können angeben, welche Eigenschaften im Ergebnis enthalten sein sollen, indem Sie eine "Select"-Klausel zu Ihrer Abfrage hinzufügen. Der folgende Beispielcode gibt die "id"-, "complete"- und "text"-Eigenschaften aller Zeilen in der Tabelle "todoItemTable" zurück:

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

### <a name="lookingup"></a>Gewusst wie: Daten nach ID abrufen

Die "lookup"-Funktion nimmt nur den "id"-Wert entgegen und gibt das Objekt aus der Datenbank mit der entsprechenden ID zurück. Die "id"-Spalten in Datenbanken enthalten entweder Ganzzahlen oder Zeichenfolgen. Standardmäßig werden Zeichenfolgen für die "id"-Spalte verwendet.

	todoItemTable.lookup("37BBF396-11F0-4B39-85C8-B319C729AF6D").done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	})

##<a name="odata-query"></a>Einen OData-Abfragevorgang ausführen

Mobile Services verwenden die URI-Konventionen für OData-Abfragen zum Erstellen und Ausführen von REST-Abfragen.  Nicht alle OData-Abfragen können mithilfe der integrierten Abfragefunktionen erstellt werden. Dies gilt insbesondere für komplexe Filtervorgänge wie der Suche nach einer Teilzeichenfolge in einer Eigenschaft. Für diese komplexen Abfragen können Sie eine beliebige gültige Optionszeichenfolge für OData-Abfragen wie folgt an die "read"-Funktion übergeben:

	function refreshTodoItems() {
	    todoItemTable.read("$filter=substringof('search_text',text)").then(function(items) {
	        var itemElements = $.map(items, createUiForTodoItem);
	        $("#todo-items").empty().append(itemElements);
	        $("#no-items").toggle(items.length === 0);
	    }, handleError);
	}

>[WACOM.NOTE]Wenn Sie für die "read"-Funktion eine unformatierte Optionszeichenfolge für OData-Abfragen angeben, können Sie in derselben Abfrage nicht gleichzeitig Methoden des Abfrage-Generators verwenden. In diesem Fall müssen Sie die gesamte Abfrage als OData-Abfragezeichenfolge erstellen. Weitere Informationen zu OData-Systemabfrageoptionen finden Sie in der [Referenz zu OData-Systemabfrageoptionen].

<h2><a name="inserting"></a>Gewusst wie: Einfügen von Daten in einen mobilen Dienst</h2>

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


Mobile Services unterstützen eindeutige benutzerdefinierte Zeichenfolgenwerte als Tabellen-ID. Auf diese Weise können Anwendungen benutzerdefinierte Werte wie z. B. E-Mail-Adressen oder Benutzernamen in der ID-Spalte einer Mobile Services-Tabelle verwenden. Wenn die Zeile beispielsweise durch eine E-Mail-Adresse identifiziert werden soll, können Sie das folgende JSON-Objekt verwenden.

			todoItemTable.insert({
			   id: "myemail@domain.com",
			   text: "New Item",
			   complete: false
			})

Falls beim Einfügen neuer Datensätze in eine Tabelle kein Wert für die Zeichenfolgen-ID angegeben wird, wird ein eindeutiger ID-Wert von Mobile Services generiert.

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

Der "id"-Wert muss eindeutig sein und darf keine Zeichen aus den folgenden Sätzen enthalten:

+ Steuerzeichen: [0x0000-0x001F] und [0x007F-0x009F]. Weitere Informationen finden Sie unter [ASCII-Steuerzeichen C0 und C1].
+  Druckbare Zeichen: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  Die IDs "." und ".."

Alternativ können Sie auch ganzzahlige Ids für Ihre Tabellen verwenden. Um ganzzahlige IDs zu verwenden, müssen Sie bei der Tabellenerstellung für den "mobile table create"-Befehl die Option "--integerId" verwenden. Dieser Befehl wird in der Befehlszeilenschnittstelle (CLI) für Azure verwendet. Weitere Informationen zur CLI finden Sie unter [CLI für Tabellen in mobilen Diensten].


<h2><a name="modifying"></a>Gewusst wie: Ändern von Daten in einem mobilen Dienst</h2>

Der folgende Code zeigt, wie Sie Daten in einer Tabelle ändern können. Der Client ändert die Daten in einer Zeile, indem er eine PATCH-Anforderung an den mobilen Dienst schickt. Der Anforderungstext enthält die zu aktualisierenden Felder als JSON-Objekt. Dieser Code aktualisiert ein vorhandenes Element in der Tabelle "todoItemTable".

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

<h2><a name="deleting"></a>Gewusst wie: Löschen von Daten in einem mobilen Dienst</h2>

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

<h2><a name="binding"></a>Gewusst wie: Anzeigen von Daten in der Benutzeroberfläche</h2>

Dieser Abschnitt beschreibt das Anzeigen von Datenobjekten in GUI-Elementen. Für die Abfrage von Elementen aus der "todoItemTable" und deren Anzeige in einer sehr einfachen Liste können Sie den folgenden Code ausführen. In diesem Beispiel wird keinerlei Auswahl, Filterung oder Sortierung durchgeführt.

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

<h2><a name="caching"></a>Gewusst wie: Authentifizieren von Benutzern</h2>

Mobile Dienste unterstützen Authentifizierung und Autorisierung von Anwendungsbenutzern mit einer Vielzahl externer Identitätsanbieter: Facebook, Google, Microsoft Account und Twitter. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln in Serverskripts zu implementieren. Weitere Informationen finden Sie im Lernprogramm [Erste Schritte mit der Authentifizierung].

Insgesamt werden zwei Authentifizierungsflüsse unterstützt: ein "Serverfluss" und ein "Clientfluss". Der Serverfluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Authentifizierungs-Webschnittstelle des Anbieters verwendet wird. Der Clientfluss ermöglicht eine tiefere Integration mit gerätespezifischen Fähigkeiten wie z. B. einmalige Anmeldung, da in diesem Fall anbieterspezifische und gerätespezifische SDKs verwendet werden.

<h3>Serverfluss</h3>
Sie müssen Ihre Anwendung bei Ihrem Identitätsanbieter registrieren, um den mobilen Diensten die Verwaltung des Authentifizierungsprozesses in Ihrer Windows Store- oder HTML5-App zu ermöglichen. Anschließend müssen Sie in Ihrem mobilen Dienst die Anwendungs-ID und den geheimen Schlüssel Ihres Anbieters konfigurieren. Weitere Informationen finden Sie im Lernprogramm "Erste Schritte mit Authentifizierung" ([Windows Store][Get started with authentication Windows Store]/[HTML][Get started with authentication]).

Nach der Registrierung bei Ihrem Identitätsanbieter können Sie die [LoginAsync]-Methode mit dem [MobileServiceAuthenticationProvider]-Wert Ihres Anbieters aufrufen. Für die Facebook-Anmeldung verwenden Sie z. B. den folgenden Code.

		client.login("facebook").done(function (results) {
		     alert("You are now logged in as: " + results.userId);
		}, function (err) {
		     alert("Error: " + err);
		});

Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den an die "login"-Methode übergebenen Wert auf einen der folgenden Werte: "microsoftaccount", "facebook", "twitter", "google" oder "windowsazureactivedirectory".

In diesem Fall verwaltet der mobile Dienst den OAuth 2.0-Authentifizierungsfluss, indem die Anmeldungsseite des ausgewählten Anbieters angezeigt und nach der erfolgreichen Anmeldung beim Identitätsanbieter ein Authentifizierungstoken für den mobilen Dienst generiert wird. Die [login]-Funktion gibt ein JSON-Objekt (**user**) zurück, das sowohl Benutzer-ID als auch Authentifizierungstoken für den mobilen Dienst in den Feldern **userId** und **authenticationToken** zur Verfügung stellt. Dieses Token kann zwischen gespeichert und wiederverwendet werden, bis es abläuft. Weitere Informationen finden Sie unter [Zwischenspeichern des Authentifizierungstokens].

<div class="dev-callout"><b>Windows Store-App</b>
<p>Wenn Sie den Anmeldeanbieter für Microsoft-Konten zum Authentifizieren von Benutzern Ihrer Windows Store-App verwenden, sollten Sie ebenfalls das App-Paket im mobilen Dienst registrieren. Wenn Sie Ihre Windows Store-App-Paketinformationen bei Mobile Services registrieren, kann der Client die Anmeldeinformationen für Microsoft Account für eine einmalige Anmeldung verwenden. Wenn Sie dies nicht tun, werden Ihre Benutzer mit Microsoft Account-Login jedes Mal zur Anmeldung aufgefordert, wenn diese Anmeldemethode aufgerufen wird. Informationen zum Registrieren Ihres Windows Store-App-Pakets finden Sie unter <a href="/de-de/develop/mobile/how-to-guides/register-windows-store-app-package/" target="_blank">Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung</a>. Nachdem die Paketinformationen mit Mobile Services registriert wurden, rufen Sie die Methode zur <a href="http://go.microsoft.com/fwlink/p/?LinkId=322050" target="_blank">Anmeldung</a> auf, indem Sie den Wert <strong>wahr</strong> für <em>useSingleSignOn</em> bereitstellen, um die Anmeldeinformationen wiederzuverwenden.</p>
</div>

<h3>Clientfluss</h3>
Ihre Anwendung kann den Identitätsanbieter auch unabhängig kontaktieren und das zurückgegebene Token zur Authentifizierung dem mobilen Dienst vorlegen. Mit diesem Clientfluss können Sie die einmalige Anmeldung für Ihre Benutzer implementieren oder zusätzliche Benutzerdaten vom Identitätsanbieter abrufen.

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

Dieses vereinfachte Beispiel ruft ein Token von Live Connect ab und übergibt das Token in einem Aufruf der [login]-Funktion an den mobilen Dienst. Ein weiteres Beispiel für die einmalige Anmeldung mit einem Microsoft-Konto finden Sie unter [Authentifizieren Ihrer Anwendung mit einmaliger Anmeldung].

Wenn Sie die Facebook- oder Google-APIs für die Clientauthentifizierung verwenden, müssen Sie den Code abändern.

		client.login(
		     "facebook",
		     {"access_token": token})
		.done(function (results) {
		     alert("You are now logged in as: " + results.userId);
		}, function (err) {
		     alert("Error: " + err);
		});

Dieses Beispiel geht davon aus, dass das vom jeweiligen Anbieter gelieferte Token in der "token"-Variable gespeichert wird.
Clientauthentifizierung für Twitter ist derzeit nicht möglich.

<h3>Zwischenspeichern des Authentifizierungstokens</h3>
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


<h2><a name="errors"></a>Gewusst wie: Fehlerbehandlung</h2>

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

			client.getTable("tablename").read().then(function (data) { /* do something */ }, handleError);

<h2><a name="promises"></a>Gewusst wie: Einsatz von Zusagen</h2>

Promises bieten einen Mechanismus zur Planung von Operationen auf einen Wert, der noch nicht berechnet wurde. Es handelt sich um eine Abstraktion für Interaktionen mit asynchronen APIs.

Die "done"-Promise wird ausgeführt, sobald die übergebene Funktion entweder erfolgreich abgeschlossen wurde oder ein Fehler aufgetreten ist. Im Gegensatz zur "then" ist in diesem Fall garantiert, dass alle nicht in der Funktion verarbeiteten Fehler geworfen werden, und nach Abschluss der Ausführung der Handler wirft diese Funktion alle Fehler, die von der "then"-Promise im Fehlerstatus geworfen worden wären. Weitere Informationen finden Sie unter [done].

			promise.done(onComplete, onError);

Wie folgt:

			var query = todoItemTable;
			query.read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

Die "then" hat dieselbe Funktion wie die "done"-Promise, aber im Gegensatz zu "then" ist bei "done" garantiert, dass alle nicht in der Funktion verarbeiteten Fehler ausgelöst werden. Falls Sie keinen Fehlerhandler an "then" übergeben und ein Fehler in der Anwendung auftritt, wird keine Ausnahme ausgelöst sondern eine Promise im Fehlerzustand zurückgegeben. Weitere Informationen finden Sie unter [then].

			promise.then(onComplete, onError).done( /* Your success and error handlers */ );

Wie folgt:

			var query = todoItemTable;
			query.read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

Sie können Promises auf verschiedene Arten einsetzen. Sie können Promises verketten, indem Sie "then" oder "done" für die Promise aufrufen, die von der vorherigen "then"-Funktion zurückgegeben wurde. Verwenden Sie then für Zwischenschritte der Operation (z. B. ".then().then()") und "done" für den letzten Schritt der Operation (z. B. ".then().then().done()").  Sie können mehrere "then"-Funktionen verketten, da "then" eine Promise zurückgibt. Sie können nicht mehr als eine "done"-Methode verketten, da die Rückgabe undefiniert ist. [Weitere Informationen zum Unterschied zwischen "then" und "done"].

 			todoItemTable.insert({
 			   text: "foo"
 			}).then(function (inserted) {
 			   inserted.newField = 123;
 			   return todoItemTable.update(inserted);
 			}).done(function (insertedAndUpdated) {
 			   alert(JSON.stringify(insertedAndUpdated));
 			})

<h2><a name="customizing"></a>Gewusst wie: Anpassen der Client-Anforderungsheader</h2>

Sie können benutzerdefinierte Anforderungsheader mit der "withFilter"-Funktion verschicken und beliebige zu verschickende Eigenschaften der Anforderung innerhalb des Filters definieren. Diese benutzerdefinierten HTTP-Header machen z. B. Sinn, um Werte an serverseitige Skripts zu übergeben.

			var client = new WindowsAzure.MobileServiceClient('https://your-app-url', 'your-key')
			   .withFilter(function (request, next, callback) {
			   request.headers.MyCustomHttpHeader = "Some value";
			   next(request, callback);
			});

Filter haben noch zahlreiche weitere Funktionen neben der Anpassung von Anforderungsheadern. Mit Filtern können Sie Anforderungen untersuchen oder ändern, Antworten untersuchen oder ändern, Netzwerkaufrufe umgehen, mehrere Aufrufe verschicken etc.

<h2><a name="hostnames"></a>Gewusst wie: Verwenden von Cross-Origin Resource Sharing (CORS)</h2>

Um zu steuern, welche Websites mit Ihrem mobilen Dienst interagieren und Anforderungen senden dürfen, sollten Sie den Hostnamen der Website, die Sie zum Hosten verwenden, über die Registerkarte "Konfiguration" der CORS (Cross-Origin Resource Sharing)-Positivliste hinzufügen. Sie können bei Bedarf Platzhalter verwenden. Mobile Dienste weisen Browser normalerweise an, nur Zugriff von "localhost" zuzulassen, und Cross-Origin Resource Sharing (CORS) gestattet es dem JavaScript-Code in einem Browser auf einem externen Hostnamen, mit Ihrem mobilen Dienst zu interagieren.  Diese Konfiguration wird für WinJS-Anwendungen nicht benötigt.

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
[Was sind Mobile Services?]: #what-is
[Konzepte]: #concepts
[Gewusst wie: Erstellen des Mobile Services-Clients]: #create-client
[Gewusst wie: Abfragen von Daten aus einem mobilen Dienst]: #querying
[Zurückgegebene Daten filtern]: #filtering
[Zurückgegebene Daten sortieren]: #sorting
[Daten seitenweise zurückgeben]: #paging
[Bestimmte Spalten auswählen]: #selecting
[Daten nach ID abrufen]: #lookingup
[Gewusst wie: Anzeigen von Daten in der Benutzeroberfläche]: #binding
[Gewusst wie: Einfügen von Daten in einen mobilen Dienst]: #inserting
[Gewusst wie: Ändern von Daten in einem mobilen Dienst]: #modifying
[Gewusst wie: Löschen von Daten in einem mobilen Dienst]: #deleting
[Gewusst wie: Authentifizieren von Benutzern]: #caching
[Gewusst wie: Fehlerbehandlung]: #errors
[Gewusst wie: Einsatz von Zusagen]: #promises
[Gewusst wie: Anpassen der Anforderungsheader]: #customizing
[Gewusst wie: Verwenden von Cross-Origin Resource Sharing (CORS)]: #hostnames
[Nächste Schritte]: #nextsteps
[Einen OData-Abfragevorgang ausführen]: #odata-query



<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-html
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Erste Schritte mit Daten]: http://www.windowsazure.com/de-de/develop/mobile/tutorials/get-started-with-data-html/
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-html
[Erste Schritte zur Authentifizierung von Windows Store]: /de-de/develop/mobile/tutorials/get-started-with-users-js
[then]: http://msdn.microsoft.com/de-de/library/windows/apps/br229728.aspx
[done]: http://msdn.microsoft.com/de-de/library/windows/apps/hh701079.aspx
[Weitere Informationen zum Unterschied zwischen "then" und "done"]: http://msdn.microsoft.com/de-de/library/windows/apps/hh700334.aspx
[Behandeln von Fehlern in Promises]: http://msdn.microsoft.com/de-de/library/windows/apps/hh700337.aspx

[sessionStorage]: http://msdn.microsoft.com/de-de/library/cc197062(v=vs.85).aspx
[localStorage]: http://msdn.microsoft.com/de-de/library/cc197062(v=vs.85).aspx

[ListView]: http://msdn.microsoft.com/de-de/library/windows/apps/br211837.aspx
[Datenbindung (Windows Store-Apps mit JavaScript und HTML)]: http://msdn.microsoft.com/de-de/library/windows/apps/hh758311.aspx
[Windows Store JavaScript-Schnellstart]: http://www.windowsazure.com/de-de/develop/mobile/tutorials/get-started
[HTML-Schnellstart]: http://www.windowsazure.com/de-de/develop/mobile/tutorials/get-started-html
[Erste Schritte mit Daten in Windows Store JavaScript]: http://www.windowsazure.com/de-de/develop/mobile/tutorials/get-started-with-data-js
[Erste Schritte mit Daten in HTML/JavaScript]: http://www.windowsazure.com/de-de/develop/mobile/tutorials/get-started-with-data-html/
[Ein vollständiges Beispiel zum Einrichten dieses Szenarios finden Sie hier]: http://www.windowsazure.com/de-de/develop/mobile/tutorials/single-sign-on-windows-8-js/
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-html
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-html
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-html
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-html
[login]: http://msdn.microsoft.com/de-de/library/windowsazure/jj554236.aspx
[Authentifizieren Ihrer Anwendung mit einmaliger Anmeldung]: /de-de/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[ASCII-Steuerzeichen C0 und C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI für Tabellen in mobilen Diensten]: http://www.windowsazure.com/de-de/manage/linux/other-resources/command-line-tools/#Mobile_Tables
[Referenz zu OData-Systemabfrageoptionen]: http://go.microsoft.com/fwlink/p/?LinkId=444502

<!--HONumber=35.1-->
