##<a name="create-client"></a>Vorgehensweise beim Erstellen von Clients

Stellen Sie eine Clientverbindung her, indem Sie ein `WindowsAzure.MobileServicesClient`-Objekt erstellen. Ersetzen Sie `appUrl` durch die URL zu Ihrer mobilen App.

```
var client = WindowsAzure.MobileServicesClient(appUrl);
```

##<a name="table-reference"></a>Gewusst wie: Erstellen von Tabellenverweisen

Zum Zugreifen auf oder Aktualisieren von Daten erstellen Sie einen Verweis auf die Back-End-Tabelle. Ersetzen Sie `tableName` durch den Namen Ihrer Tabelle.

```
var table = client.getTable(tableName);
```

##<a name="querying"></a>Vorgehensweise: Abfragen von Tabellenverweisen

Sobald Sie über einen Tabellenverweis verfügen, können Sie diesen zum Abfragen von Daten auf dem Server verwenden. Abfragen erfolgen in einer „LINQ-ähnlichen“ Sprache. Um alle Daten aus der Tabelle zurückzugeben, verwenden Sie Folgendes:

```
/**
 * Process the results that are received by a call to table.read()
 *
 * @param {Object} results the results as a pseudo-array
 * @param {int} results.length the length of the results array
 * @param {Object} results[] the individual results
 */
function success(results) {
   var numItemsRead = results.length;

   for (var i = 0 ; i < results.length ; i++) {
       var row = results[i];
       // Each row is an object - the properties are the columns
   }
}

function failure(error) {
    throw new Error('Error loading data: ', error);
}

table
    .read()
    .then(success, failure);
```

Die „success“-Funktion mit den Ergebnissen wird aufgerufen. Verwenden Sie `for (var i in results)` nicht in der „success“-Funktion, da dies zum Durchlaufen von Informationen in den Ergebnissen führt, wenn andere Abfragefunktionen verwendet werden (wie z. B. `.includeTotalCount()`).

Weitere Informationen zur Abfragesyntax finden Sie in der [Dokumentation „Query-Objekt“].

### Filtern von Daten auf dem Server

Sie können für den Tabellenverweis eine `where` Klausel verwenden:

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

Sie können auch eine Funktion nutzen, die das Objekt filtert. In diesem Fall wird die Variable `this` dem aktuell gefilterten Objekt zugewiesen. Folgendes ist funktionell gleichwertig mit dem vorherigen Beispiel:

```
function filterByUserId(currentUserId) {
    return this.userId === currentUserId && this.complete === false;
}

table
    .where(filterByUserId, user.userId)
    .read()
    .then(success, failure);
```

### Aufteilung von Daten auf Seiten

Verwenden Sie die Methoden „take()“ und „skip()“. Angenommen, die Tabelle soll in Datensätze mit 100 Zeilen aufgeteilt werden:

```
var totalCount = 0, pages = 0;

// Step 1 - get the total number of records
table.includeTotalCount().take(0).read(function (results) {
    totalCount = results.totalCount;
    pages = Math.floor(totalCount/100) + 1;
    loadPage(0);
}, failure);

function loadPage(pageNum) {
    let skip = pageNum * 100;
    table.skip(skip).take(100).read(function (results) {
        for (var i = 0 ; i < results.length ; i++) {
            var row = results[i];
            // Process each row
        }
    }
}
```

Die Methode `.includeTotalCount()` wird verwendet, um ein totalCount-Feld dem „results“-Objekt hinzuzufügen. Das „totalCount“-Feld wird mit der Gesamtanzahl von Datensätzen aufgefüllt, die zurückgegeben wird, wenn keine Seitenverwaltung verwendet wird.

Sie können dann die „pages“-Variable und einige Schaltflächen auf der Benutzeroberfläche verwenden, um eine Seitenliste bereitzustellen. Verwenden Sie „loadPage()“ zum Laden der neuen Datensätze für jede Seite. Implementieren Sie eine Zwischenspeicherung, um den Zugriff auf Datensätze zu beschleunigen, die bereits geladen wurden.


###<a name="sorting-data"></a>Vorgehensweise: Zurückgeben sortierter Daten

Verwenden Sie die Abfragemethoden „.orderBy()“ oder „.orderByDescending()“:

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Weitere Informationen zum „Query“-Objekt finden Sie in der [Dokumentation „Query-Objekt“].

##<a name="inserting"></a>Gewusst wie: Einfügen von Daten

Erstellen Sie ein „JavaScript“-Objekt mit dem entsprechenden Datum, und rufen Sie „table.insert()“ asynchron auf:

```
var newItem = {
    name: 'My Name',
    signupDate: new Date()
};

table
    .insert(newItem)
    .done(function (insertedItem) {
        var id = insertedItem.id;
    }, failure);
```

Bei erfolgreichem Einfügen wird das eingefügte Element mit den zusätzlichen Feldern zurückgegeben, die für Synchronisierungsvorgänge erforderlich sind. Sie müssen Ihren Cache mit diesen Informationen für spätere Updates aktualisieren.

Beachten Sie, dass das Node.js-Server SDK für Azure Mobile Apps für Entwicklungszwecke ein dynamisches Schema unterstützt. Bei einem dynamischen Schema wird das Schema der Tabelle dynamisch aktualisiert, was das Hinzufügen von Spalten zur Tabelle ermöglicht, indem diese in einem Einfüge- oder Aktualisierungsvorgang angegeben werden. Es wird empfohlen, das dynamische Schema zu deaktivieren, bevor die Anwendung in die Produktion verlagert wird.

##<a name="modifying"></a>Gewusst wie: Ändern von Daten

Ähnlich wie bei der „.insert()“-Methode müssen Sie zuerst ein „Update“-Objekt erstellen und dann „.update()“ aufrufen. Das „Update“-Objekt muss die ID des Datensatzes enthalten, der aktualisiert werden soll. Diese wird beim Lesen des Datensatzes oder Aufrufen von „.insert()“ abgerufen.

```
var updateItem = {
    id: '7163bc7a-70b2-4dde-98e9-8818969611bd',
    name: 'My New Name'
};

table
    .update(updateItem)
    .done(function (updatedItem) {
        // You can now update your cached copy
    }, failure);
```

##<a name="deleting"></a>Gewusst wie: Löschen von Daten

Rufen Sie die „.del()“-Methode auf, um einen Datensatz zu löschen. Übergeben Sie die ID in einen Objektverweis:

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```

<!----HONumber=AcomDC_0309_2016-->

