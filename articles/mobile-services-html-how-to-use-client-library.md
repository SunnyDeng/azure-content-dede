<properties 
	pageTitle="Verwenden eines HTML-Clients - Azure Mobile Services" 
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
	ms.date="11/21/2014" 
	ms.author="glenga"/>


# So verwenden Sie einen HTML-/JavaScript-Client für Azure Mobile Services

<div class="dev-center-tutorial-selector sublanding">
  <a href="/de-de/develop/mobile/how-to-guides/work-with-net-client-library/" title=".NET Framework">.NET Framework</a><a href="/de-de/develop/mobile/how-to-guides/work-with-html-js-client/" title="HTML/JavaScript" class="current">HTML/JavaScript</a><a href="/de-de/develop/mobile/how-to-guides/work-with-ios-client-library/" title="iOS">iOS</a><a href="/de-de/develop/mobile/how-to-guides/work-with-android-client-library/" title="Android">Android</a><a href="/de-de/develop/mobile/how-to-guides/work-with-xamarin-client-library/" title="Xamarin">Xamarin</a>
</div>


Dieser Artikel beschreibt gängige Szenarien für die Verwendung eines HTML/JavaScript-Clients für mobile Dienste in Azure. Besprochen werden unter anderem Datenabfragen, Einfügen, Aktualisieren und Löschen von Daten, Authentifizierung von Benutzern und Fehlerbehandlung. Wenn Sie noch keine Erfahrungen mit mobilen Diensten haben, sollten Sie möglicherweise zunächst die Schnellstart-Lernprogramme [Windows Store JavaScript-Schnellstart] oder [HTML-Schnellstart] lesen. Im Schnellstart-Lernprogramm lernen Sie, Ihr Konto zu konfigurieren und Ihren ersten mobilen Dienst zu erstellen.


## Inhaltsverzeichnis

- [Windows Azure Mobile Services]
- [Konzepte]
- [Vorgehensweise: Erstellen des Mobile Services-Clients]
- [Vorgehensweise: Abfragen von Daten aus einem mobilen Dienst]
	- [Zurückgegebene Daten filtern]
    - [Zurückgegebene Daten sortieren]
	- [Daten seitenweise zurückgeben]
	- [Bestimmte Spalten auswählen]
	- [Daten nach ID abrufen]
	- [Einen OData-Abfragevorgang ausführen]
- [Vorgehensweise: Einfügen von Daten in einen mobilen Dienst]
- [Vorgehensweise: Ändern von Daten in einem mobilen Dienst]
- [Vorgehensweise: Löschen von Daten in einem mobilen Dienst]
- [Vorgehensweise: Anzeigen von Daten in der Benutzeroberfläche]
- [Vorgehensweise: Authentifizieren von Benutzern]
- [Vorgehensweise: Fehlerbehandlung]
- [Vorgehensweise: Einsatz von Zusagen]
- [Vorgehensweise: Anpassen der Anforderungsheader]
- [Vorgehensweise: Verwenden von Cross-Origin Resource Sharing (CORS)]
- [Nächste Schritte]

[AZURE.INCLUDE [mobile-services-concepts](../includes/mobile-services-concepts.md)]

##<a name="create-client"></a>Vorgehensweise: Erstellen des mobile Dienste-Clients



Öffnen Sie die HTML-Datei in Ihrem Web-Editor und fügen Sie den folgenden Code zu den Skriptverweisen der Seite hinzu:

    <script src='http://ajax.aspnetcdn.com/ajax/mobileservices/MobileServices.Web-1.1.2.min.js'></script>

> [AZURE.NOTE] Für eine in JavaScript/HTML geschriebene Windows Store-App müssen Sie Ihrem Projekt nur das NuGet-Paket **WindowsAzure.MobileServices.WinJS** hinzufügen.

Öffnen oder erstellen Sie eine JavaScript-Datei im Editor, und fügen Sie den folgenden Code hinzu, um die  `MobileServiceClient`-Variable zu definieren, und übergeben Sie Anwendungs-URL und -Schlüssel des mobilen Diensts in dieser Reihenfolge an den  `MobileServiceClient`-Konstruktor.

	var MobileServiceClient = WindowsAzure.MobileServiceClient;
    var client = new MobileServiceClient('AppUrl', 'AppKey');

Sie müssen den Platzhalter  `AppUrl` durch die Anwendungs-URL Ihres mobilen Dienstes und  `AppKey` durch den Anwendungsschlüssel ersetzen. Im Lernprogramm [Erste Schritte mit Daten in Windows Store JavaScript] oder [Erste Schritte mit Daten in HTML/JavaScript] erfahren Sie, wie Sie Anwendungs-URL und -Schlüssel Ihres mobilen Dienstes abfragen können.

##<a name="querying"></a>Vorgehensweise: Abfragen von Daten aus einem mobilen Dienst

Jeglicher Code zum Abrufen oder Ändern von Daten in der Tabelle der SQL-Datenbank ruft Funktionen des  `MobileServiceTable`-Objekts auf. Sie erhalten einen Verweis auf die Tabelle, indem Sie die  `getTable()`-Funktion für eine Instanz von  `MobileServiceClient` aufrufen.

    var todoItemTable = client.getTable('todoitem');


### <a name="filtering"></a>Vorgehensweise: Zurückgegebene Daten filtern

Der folgende Code zeigt, wie Sie Daten mithilfe einer  `where`-Klausel in einer Abfrage filtern. Es werden alle Elemente aus der  `todoItemTable` zurückgegeben, deren complete-Feld  `false` ist.  `todoItemTable` ist der Verweis auf die Tabelle im mobilen Dienst, die wir zuvor erstellt haben. Die Where-Funktion wendet ein Zeilenfilterungsprädikat auf die Tabellenabfrage an. Die Funktion nimmt ein JSON-Objekt oder eine Funktion entgegen, die den Zeilenfilter definiert, und liefert eine Abfrage zurück, die weiter bearbeitet werden kann.

	var query = todoItemTable.where({
	    complete: false
	}).read().done(function (results) {
	    alert(JSON.stringify(results));
	}, function (err) {
	    alert("Error: " + err);
	});

Durch den Aufruf von  `where` im Abfrageobjekt und die Übergabe eines Objekts als Parameter weisen wir Mobile Services an, nur diejenigen Zeilen zurückzugeben, deren  `complete`-Spalte den Wert  `false` enthält. Beachten Sie in der folgenden URI, dass wir die eigentliche Abfragezeichenfolge verändern:

	GET /tables/todoitem?$filter=(complete+eq+false) HTTP/1.1

Sie können den URI der an den mobilen Dienst gesendeten Anforderung anzeigen, indem Sie Software zur Überprüfung von Nachrichten verwenden, wie z. B. Browser-Entwicklertools oder Fiddler.

Diese Anfrage entspricht normalerweise in etwa der folgenden SQL-Abfrage auf der Serverseite:

	SELECT *
	FROM TodoItem
	WHERE ISNULL(complete, 0) = 0

Das an die  `where`-Methode übergebene Objekt kann beliebig viele Parameter enthalten, die alle als AND-Klauseln für die Abfrage interpretiert werden. Die folgende Zeile:

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

Die obige  `where`-Anweisung und die SQL-Abfrage suchen nach unvollständigen Einträgen für "david", wobei "difficulty" = "medium" ist.

Dieselbe Abfrage kann auch auf eine andere Art geschrieben werden. Ein  `.where`-Aufruf an das Abfrageobjekt fügt der  `WHERE`-Klausel einen  `AND`-Ausdruck hinzu. Wir hätten das Ganze also in drei Zeilen schreiben können:

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

Beide Methoden sind äquivalent und können frei austauschbar verwendet werden. Alle bisherigen  `where`-Aufrufe verwenden ein Objekt mit Parametern und prüfen auf Gleichheit mit den Daten in der Datenbank. Für die Abfragemethode gibt es jedoch noch eine weitere Überladung, die eine Funktion anstelle eines Objekts entgegennimmt. In dieser Funktion können wir komplexere Ausdrücke schreiben und Operatoren wie z. B. Ungleichheit oder andere relationale Operatoren verwenden. In diesen Funktionen ist das Schlüsselwort  `this` an das Serverobjekt gebunden.

Der Funktionstext wird in einen booleschen OData (Open Data Protocol)-Ausdruck übersetzt, der anschließend an einen Abfragezeichenfolgenparameter übergeben wird. Es ist möglich, Funktionen ohne Parameter zu übergeben:

    query.where(function () {
       return this.assignee == "david" && (this.difficulty == "medium" || this.difficulty == "low");
    }).read().done(function (results) {
       alert(JSON.stringify(results));
    }, function (err) {
       alert("Error: " + err);
    });


Bei der Übergabe einer Funktion mit Parametern werden alle Argumente nach der  `where`-Klausel in deren Reihenfolge an die Funktionsparameter gebunden. Alle Objekte, die von außerhalb des Funktionsbereichs stammen, MÜSSEN als Parameter übergeben werden - die Funktion kann keine externen Variablen erfassen. In den nächsten beiden Beispielen wird das Argument "david" an den Parameter  `name` gebunden, und im ersten Beispiel wird außerdem das Argument "medium" an den Parameter  `level` gebunden. Außerdem muss die Funktion aus einer einzelnen  `return`-Anweisung mit einem unterstützten Ausdruck bestehen, wie hier gezeigt:

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

 `where` kann mit  `orderBy`,  `take` und  `skip` kombiniert werden. Weitere Details finden Sie im nächsten Abschnitt.

### <a name="sorting"></a>Vorgehensweise: Zurückgegebene Daten sortieren

Der folgende Code zeigt, wie Sie Daten mithilfe einer  `orderBy`- oder  `orderByDescending`-Funktion in der Abfrage sortieren können. Die Abfrage gibt Elemente aus der Tabelle  `todoItemTable` aufsteigend sortiert nach dem  `text`-Feld zurück. Standardmäßig gibt der Server nur die ersten 50 Elemente zurück.

> [AZURE.NOTE] Standardmäßig wird eine servergesteuerte Seitengröße verwendet, um zu verhindern, dass alle Elemente zurückgegeben werden. Damit wird verhindert, dass Standardabfragen für große Datensätze den Dienst negativ beeinflussen. 
Sie können  `take` aufrufen, um die Anzahl der zurückzugebenden Elemente zu erhöhen, wie im nächsten Abschnitt beschrieben.  `todoItemTable` ist der Verweis auf die Tabelle im mobilen Dienst, die wir zuvor erstellt haben.

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

### <a name="paging"></a>Vorgehensweise: Daten seitenweise zurückgeben

Der folgende Code zeigt, wie Sie mithilfe der  `take`- und  `skip`-Klausel in der Abfrage Paging in den zurückgegebenen Daten implementieren.  Die folgende Abfrage liefert die ersten drei Elemente aus der Tabelle zurück.

	var query = todoItemTable.take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Beachten Sie, dass die  `take(3)`-Methode im Abfrage-URI in die Abfrageoption `$top=3` übersetzt wurde.

Die folgende geänderte Abfrage überspringt die ersten drei Ergebnisse und liefert die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

	var query = todoItemTable.skip(3).take(3).read().done(function (results) {
	   alert(JSON.stringify(results));
	}, function (err) {
	   alert("Error: " + err);
	});

Sehen Sie sich erneut die URI der Anfrage an den mobilen Dienst an. Beachten Sie, dass die  `skip(3)`-Methode im Abfrage-URI in die Abfrageoption `$skip=3` übersetzt wurde.

In diesem vereinfachten Szenario werden hartcodierte Pagingwerte an die  `take`- und  `skip`-Funktion übergeben. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren.

### <a name="selecting"></a>Vorgehensweise: Bestimmte Spalten auswählen

Sie können angeben, welche Eigenschaften in den Ergebnissen enthalten sein sollen, indem Sie der Abfrage eine  `select`-Klausel hinzufügen. Der folgende Code gibt beispielsweise die Eigenschaften  `id`,  `complete` und  `text` aus jeder Zeile in  `todoItemTable` zurück:

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

### <a name="lookingup"></a>Vorgehensweise: Daten nach ID abrufen

Die  `lookup`-Funktion nimmt nur den  `id`-Wert entgegen und gibt das Objekt aus der Datenbank mit der entsprechenden ID zurück. Die  `id`-Spalten in Datenbanken enthalten entweder Ganzzahlen oder Zeichenfolgen. Standardmäßig ist eine  `id`-Spalte vom Typ "string".

	todoItemTable.lookup("37BBF396-11F0-4B39-85C8-B319C729AF6D").done(function (result) {
	   alert(JSON.stringify(result));
	}, function (err) {
	   alert("Error: " + err);
	})

##<a name="odata-query"></a>Einen OData-Abfragevorgang ausführen

Mobile Services verwenden die URI-Konventionen für OData-Abfragen zum Erstellen und Ausführen von REST-Abfragen.  Nicht alle OData-Abfragen können mithilfe der integrierten Abfragefunktionen erstellt werden. Dies gilt insbesondere für komplexe Filtervorgänge wie der Suche nach einer Teilzeichenfolge in einer Eigenschaft. Für diese komplexen Abfragen können Sie eine beliebige gültige Optionszeichenfolge für OData-Abfragen wie folgt an die  `read`-Funktion übergeben:

	function refreshTodoItems() {
	    todoItemTable.read("$filter=substringof('search_text',text)").then(function(items) {
	        var itemElements = $.map(items, createUiForTodoItem);
	        $("#todo-items").empty().append(itemElements);
	        $("#no-items").toggle(ite
	ms.length === 0);
	    }, handleError);
	}

>[AZURE.NOTE]Wenn Sie für die  `read`-Funktion eine unformatierte Optionszeichenfolge für OData-Abfragen angeben, können Sie in derselben Abfrage nicht gleichzeitig Methoden des Abfrage-Generators verwenden. In diesem Fall müssen Sie die gesamte Abfrage als OData-Abfragezeichenfolge erstellen. Weitere Informationen zu OData-Systemabfrageoptionen finden Sie in der [Referenz zu OData-Systemabfrageoptionen].

<h2><a name="inserting"></a>Vorgehensweise: Einfügen von Daten in einen mobilen Dienst</h2>

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

Der  `id`-Wert muss eindeutig sein und darf keine Zeichen aus den folgenden Sätzen enthalten:

+ Steuerzeichen: [0x0000-0x001F] und [0x007F-0x009F]. Weitere Informationen finden Sie unter [ASCII-Steuerzeichen C0 und C1].
+  Druckbare Zeichen: **"**(0x0022), **\+** (0x002B), **/** (0x002F), **?** (0x003F), **\\** (0x005C), **`** (0x0060)
+  Die IDs "." und ".."

Alternativ können Sie auch ganzzahlige Ids für Ihre Tabellen verwenden. Um ganzzahlige Ids zu verwenden, müssen Sie bei der Tabellenerstellung für den  `mobile table create`-Befehl die Option `--integerId` verwenden. Dieser Befehl wird in der Befehlszeilenschnittstelle (CLI) für Azure verwendet. Weitere Informationen zur CLI finden Sie unter [CLI zum Verwalten von Mobile Services-Tabellen].


<h2><a name="modifying"></a>Vorgehensweise: Ändern von Daten in einem mobilen Dienst</h2>

Der folgende Code zeigt, wie Sie Daten in einer Tabelle ändern können. Der Client ändert die Daten in einer Zeile, indem er eine PATCH-Anforderung an den mobilen Dienst schickt. Der Anforderungstext enthält die zu aktualisierenden Felder als JSON-Objekt. Dadurch wird ein vorhandenes Element in der Tabelle  `todoItemTable` aktualisiert.

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

<h2><a name="deleting"></a>Vorgehensweise: Löschen von Daten in einem mobilen Dienst</h2>

Der folgende Code zeigt, wie Sie Daten in einer Tabelle löschen können. Der Client löscht eine Datenzeile, indem er eine DELETE-Anforderung an den mobilen Dienst schickt. Dieser Code löscht ein vorhandenes Element in der Tabelle todoItemTable.

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

<h2><a name="binding"></a>Vorgehensweise: Anzeigen von Daten in der Benutzeroberfläche</h2>

Dieser Abschnitt beschreibt das Anzeigen von Datenobjekten in GUI-Elementen. Zum Abfragen von Elementen in  `todoItemTable` und zum Anzeigen der Elemente in einer sehr einfachen Liste können Sie den folgenden Beispielcode ausführen. In diesem Beispiel wird keinerlei Auswahl, Filterung oder Sortierung durchgeführt.

			var query = todoItemTable;

			query.read().then(function (todoItems) {
			   // The space specified by 'placeToInsert' is an unordered list element <ul> ... </ul>
			   var listOfItems = document.getElementById('placeToInsert');
			   for (var i = 0; i < todoIte
	ms.length; i++) {
			      var li = document.createElement('li');
			      var div = document.createElement('div');
			      div.innerText = todoItems[i].text;
			      li.appendChild(div);
			      listOfIte
	ms.appendChild(li);
			   }
			}).read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

In Windows Store-Apps können Sie mit dem Abfrageergebnis ein [WinJS.Binding.List]-Objekt erstellen, das als Datenquelle für ein [ListView]-Objekt gebunden werden kann. Weitere Informationen finden Sie unter [Datenbindung (Windows Store-Apps mit JavaScript und HTML)].

<h2><a name="caching"></a>Vorgehensweise: Authentifizieren von Benutzern</h2>

Mobile Dienste unterstützen Authentifizierung und Autorisierung von Anwendungsbenutzern mit einer Vielzahl externer Identitätsanbieter: Facebook, Google, Microsoft Account und Twitter. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln in Serverskripts zu implementieren. Weitere Informationen finden Sie im Lernprogramm [Erste Schritte mit der Authentifizierung].

Insgesamt werden zwei Authentifizierungsflüsse unterstützt: ein _server flow_ und ein _client flow_. Der Serverfluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Authentifizierungs-Webschnittstelle des Anbieters verwendet wird. Der Clientfluss ermöglicht eine tiefere Integration mit gerätespezifischen Fähigkeiten wie z. B. einmalige Anmeldung, da in diesem Fall anbieterspezifische und gerätespezifische SDKs verwendet werden.

<h3>Serverfluss</h3>
Damit Mobile Services den Authentifizierungsprozess in Ihrer Windows Store- oder HTML5-App verwalten können,
müssen Sie Ihre App bei Ihrem Identitätsanbieter registrieren. Anschließend müssen Sie in Ihrem mobilen Dienst die Anwendungs-ID und den geheimen Schlüssel Ihres Anbieters konfigurieren. Weitere Informationen finden Sie im Lernprogramm "Erste Schritte mit der Authentifizierung" ([Windows Store][Erste Schritte zur Authentifizierung von Windows Store]/[HTML][Erste Schritte mit der Authentifizierung]).

Nach der Registrierung bei Ihrem Identitätsanbieter können Sie die [LoginAsync-Methode] mit dem [MobileServiceAuthenticationProvider]-Wert Ihres Anbieters aufrufen. Für die Facebook-Anmeldung verwenden Sie z. B. den folgenden Code.

		client.login("facebook").done(function (results) {
		     alert("You are now logged in as: " + results.userId);
		}, function (err) {
		     alert("Error: " + err);
		});

Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den an die  `login`-Methode oben übergebenen Wert in einen der folgenden Werte: `microsoftaccount`,  `facebook`,  `twitter`,  `google` oder  `windowsazureactivedirectory`.

In diesem Fall verwaltet Mobile Services den OAuth 2.0-Authentifizierungsfluss, indem die Anmeldungsseite des ausgewählten Anbieters angezeigt und nach der erfolgreichen Anmeldung beim Identitätsanbieter ein Authentifizierungstoken für den mobilen Dienst generiert wird. Die [login]-Funktion gibt ein JSON-Objekt (**user**) zurück, das sowohl die Benutzer-ID als auch das Mobile Services-Authentifizierungstoken in den Feldern **userId** bzw. **authenticationToken** zur Verfügung stellt. Dieses Token kann zwischen gespeichert und wiederverwendet werden, bis es abläuft. Weitere Informationen finden Sie unter [Zwischenspeichern des Authentifizierungstokens].

> [AZURE.NOTE] **Windows Store-App**
Wenn Sie den Anmeldeanbieter für Microsoft-Konten zum Authentifizieren von Benutzern Ihrer Windows Store-App verwenden, sollten Sie ebenfalls das App-Paket im mobilen Dienst registrieren. Wenn Sie Ihre Windows Store-App-Paketinformationen bei Mobile Services registrieren, kann der Client die Anmeldeinformationen für Microsoft Account für eine einmalige Anmeldung verwenden. Wenn Sie dies nicht tun, werden Ihre Benutzer mit Microsoft Account-Login jedes Mal zur Anmeldung aufgefordert, wenn diese Anmeldemethode aufgerufen wird. Informationen zur Registrierung Ihres Windows Store-App-Pakets finden Sie unter [Registrieren Ihres Windows Store-App-Pakets für Microsoft-Authentifizierung](/de-de/develop/mobile/how-to-guides/register-windows-store-app-package/%20target="_blank"). Nach der Registrierung Ihres Pakets mit Mobile Services können Sie die [login](http://go.microsoft.com/fwlink/p/?LinkId=322050%20target="_blank")-Methode aufrufen und den Wert **true** für den <em>useSingleSignOn</em>-Parameter übergeben, um die Anmeldeinformationen wiederzuverwenden.

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

Dieses vereinfachte Beispiel ruft ein Token von Live Connect ab und übergibt das Token in einem Aufruf der [login]-Funktion an Mobile Services. Ein weiteres Beispiel für die einmalige Anmeldung mit einem Microsoft-Konto finden Sie unter [Authentifizieren Ihrer Anwendung mit einmaliger Anmeldung].

Wenn Sie die Facebook- oder Google-APIs für die Clientauthentifizierung verwenden, müssen Sie den Code abändern.

		client.login(
		     "facebook",
		     {"access_token": token})
		.done(function (results) {
		     alert("You are now logged in as: " + results.userId);
		}, function (err) {
		     alert("Error: " + err);
		});

Dieses Beispiel geht davon aus, dass das vom jeweiligen Anbieter gelieferte Token in der  `token`-Variable gespeichert wird.
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


<h2><a name="errors"></a>Vorgehensweise: Fehlerbehandlung</h2>

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

<h2><a name="promises"></a>Vorgehensweise: Einsatz von Zusagen</h2>

Promises bieten einen Mechanismus zur Planung von Operationen auf einen Wert, der noch nicht berechnet wurde. Es handelt sich um eine Abstraktion für Interaktionen mit asynchronen APIs.

Die  `done`-Zusage wird ausgeführt, sobald die übergebene Funktion entweder erfolgreich abgeschlossen wurde oder ein Fehler aufgetreten ist. Im Gegensatz zur  `then`-Zusage ist in diesem Fall gewährleistet, dass alle nicht innerhalb der Funktion behandelten Fehler ausgelöst werden. Nachdem die Handlerausführung abgeschlossen ist, löst diese Funktion alle Fehler aus, die von der then-Zusage im Fehlerstatus zurückgegeben worden wären. Weitere Informationen finden Sie unter [done].

			promise.done(onComplete, onError);

Wie folgt:

			var query = todoItemTable;
			query.read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

Die  `then`-Zusage hat dieselbe Funktion wie die  `done`-Zusage, aber im Gegensatz zur  `then`-Zusage ist bei  `done` garantiert, dass alle nicht in der Funktion behandelten Fehler ausgelöst werden. Falls Sie keinen Fehlerhandler für  `then` bereitstellen und ein Fehler im Vorgang auftritt, wird keine Ausnahme ausgelöst, sondern eine Zusage im Fehlerzustand zurückgegeben. Weitere Informationen finden Sie unter [then].

			promise.then(onComplete, onError).done( /* Your success and error handlers */ );

Wie folgt:

			var query = todoItemTable;
			query.read().done(function (results) {
			   alert(JSON.stringify(results));
			}, function (err) {
			   alert("Error: " + err);
			});

Sie können Promises auf verschiedene Arten einsetzen. Sie können Zusagevorgänge verketten, indem Sie  `then` oder  `done` für die Zusage aufrufen, die von der vorherigen  `then`-Funktion zurückgegeben wird. Verwenden Sie  `then` für die Zwischenschritte des Vorgangs (z. B.  `.then().then()`) und  `done` für die den letzten Schritt des Vorgangs (z. B.  `.then().then().done()`).  Sie können mehrere  `then`-Funktionen verketten, da  `then` eine Zusage zurückgibt. Sie können nicht mehr als eine  `done`-Methode verketten, da die Rückgabe nicht definiert ist. [Weitere Informationen zum Unterschied zwischen "then" und "done"].

 			todoItemTable.insert({
 			   text: "foo"
 			}).then(function (inserted) {
 			   inserted.newField = 123;
 			   return todoItemTable.update(inserted);
 			}).done(function (insertedAndUpdated) {
 			   alert(JSON.stringify(insertedAndUpdated));
 			})

<h2><a name="customizing"></a>Vorgehensweise: Anpassen der Client-Anforderungsheader</h2>

Sie können benutzerdefinierte Anforderungsheader mit der  `withFilter`-Funktion senden, wobei beliebige Eigenschaften der im Filter zu sendenden Anforderung gelesen und geschrieben werden. Diese benutzerdefinierten HTTP-Header machen z. B. Sinn, um Werte an serverseitige Skripts zu übergeben.

			var client = new WindowsAzure.MobileServiceClient('https://your-app-url', 'your-key')
			   .withFilter(function (request, next, callback) {
			   request.headers.MyCustomHttpHeader = "Some value";
			   next(request, callback);
			});

Filter haben noch zahlreiche weitere Funktionen neben der Anpassung von Anforderungsheadern. Mit Filtern können Sie Anforderungen untersuchen oder ändern, Antworten untersuchen oder ändern, Netzwerkaufrufe umgehen, mehrere Aufrufe verschicken etc.

<h2><a name="hostnames"></a>Vorgehensweise: Verwenden von Cross-Origin Resource Sharing (CORS)</h2>

Um zu steuern, welche Websites mit Ihrem mobilen Dienst interagieren und Anforderungen senden dürfen, sollten Sie den Hostnamen der Website, die Sie zum Hosten verwenden, über die Registerkarte "Konfiguration" der CORS (Cross-Origin Resource Sharing)-Positivliste hinzufügen. Sie können bei Bedarf Platzhalter verwenden. Neue Mobile Services weisen Browser standardmäßig an, nur Zugriff von  `localhost` zuzulassen, und Cross-Origin Resource Sharing (CORS) gestattet es dem JavaScript-Code in einem Browser unter einem externen Hostnamen, mit Ihrem mobilen Dienst zu interagieren.  Diese Konfiguration wird für WinJS-Anwendungen nicht benötigt.

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
[Windows Azure Mobile Services]: #what-is
[Konzepte]: #concepts
[Vorgehensweise: Erstellen des Mobile Services-Clients]: #create-client
[Vorgehensweise: Abfragen von Daten aus einem mobilen Dienst]: #querying
[Zurückgegebene Daten filtern]: #filtering
[Zurückgegebene Daten sortieren]: #sorting
[Daten seitenweise zurückgeben]: #paging
[Bestimmte Spalten auswählen]: #selecting
[Daten nach ID abrufen]: #lookingup
[Vorgehensweise: Anzeigen von Daten in der Benutzeroberfläche]: #binding
[Vorgehensweise: Einfügen von Daten in einen mobilen Dienst]: #inserting
[Vorgehensweise: Ändern von Daten in einem mobilen Dienst]: #modifying
[Vorgehensweise: Löschen von Daten in einem mobilen Dienst]: #deleting
[Vorgehensweise: Authentifizieren von Benutzern]: #caching
[Vorgehensweise: Fehlerbehandlung]: #errors
[Vorgehensweise: Einsatz von Zusagen]: #promises
[Vorgehensweise: Anpassen der Anforderungsheader]: #customizing
[Vorgehensweise: Verwenden von Cross-Origin Resource Sharing (CORS)]: #hostnames
[Nächste Schritte]: #nextsteps
[Einen OData-Abfragevorgang ausführen]: #odata-query



<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-html
[Mobile Services SDK]: http://go.microsoft.com/fwlink/?LinkId=257545
[Erste Schritte mit Daten]: http://azure.microsoft.com/develop/mobile/tutorials/get-started-with-data-html/
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-html
[Erste Schritte zur Authentifizierung von Windows Store]: /de-de/develop/mobile/tutorials/get-started-with-users-js
[then]: http://msdn.microsoft.com/library/windows/apps/br229728.aspx
[done]: http://msdn.microsoft.com/library/windows/apps/hh701079.aspx
[Weitere Informationen zum Unterschied zwischen "then" und "done"]: http://msdn.microsoft.com/library/windows/apps/hh700334.aspx
[Behandeln von Fehlern in Zusagen]: http://msdn.microsoft.com/library/windows/apps/hh700337.aspx

[sessionStorage]: http://msdn.microsoft.com/library/cc197062(v=vs.85).aspx
[localStorage]: http://msdn.microsoft.com/library/cc197062(v=vs.85).aspx

[ListView]: http://msdn.microsoft.com/library/windows/apps/br211837.aspx
[Datenbindung (Windows Store-Apps mit JavaScript und HTML)]: http://msdn.microsoft.com/library/windows/apps/hh758311.aspx
[Windows Store JavaScript-Schnellstart]: http://azure.microsoft.com/develop/mobile/tutorials/get-started
[HTML-Schnellstart]: http://azure.microsoft.com/develop/mobile/tutorials/get-started-html
[Erste Schritte mit Daten in Windows Store JavaScript]: http://azure.microsoft.com/develop/mobile/tutorials/get-started-with-data-js
[Erste Schritte mit Daten in HTML/JavaScript]: http://azure.microsoft.com/develop/mobile/tutorials/get-started-with-data-html/
[Ein vollständiges Beispiel zum Einrichten dieses Szenarios finden Sie hier]: http://azure.microsoft.com/develop/mobile/tutorials/single-sign-on-windows-8-js/
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-html
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-html
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-html
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-html
[login]: http://msdn.microsoft.com/library/windowsazure/jj554236.aspx
[Authentifizieren Ihrer Anwendung mit einmaliger Anmeldung]: /de-de/develop/mobile/tutorials/single-sign-on-windows-8-dotnet/
[ASCII-Steuerungscodes C0 und C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[ASCII-Steuerzeichen C0 und C1]: http://en.wikipedia.org/wiki/Data_link_escape_character#C1_set
[CLI zum Verwalten von Mobile Services-Tabellen]: http://azure.microsoft.com/manage/linux/other-resources/command-line-tools/#Mobile_Tables
[Referenz zu OData-Systemabfrageoptionen]: http://go.microsoft.com/fwlink/p/?LinkId=444502


<!--HONumber=42-->
