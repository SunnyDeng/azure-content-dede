<properties
	pageTitle="Verwenden des Apache Cordova-Plug-Ins für Azure Mobile Apps"
	description="Verwenden des Apache Cordova-Plug-Ins für Azure Mobile Apps"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/17/2016"
	ms.author="adrianha"/>

# Verwenden der Apache Cordova-Clientbibliothek für Azure Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Dieser Artikel beschreibt gängige Szenarien für die Verwendung des neuesten [Apache Cordova-Plug-Ins für Azure Mobile Apps]. Wenn Sie mit Azure Mobile Apps noch nicht vertraut sind, führen Sie zunächst den [Schnellstart von Azure Mobile Apps] durch, um ein Back-End und eine Tabelle zu erstellen und ein vorgefertigtes Apache Cordova-Projekt herunterzuladen. In dieser Anleitung konzentrieren wir uns auf das clientseitige Apache Cordova-Plug-In.

##<a name="Setup"></a>Einrichtung und Voraussetzungen

Dieses Lernprogramm setzt voraus, dass Sie ein Back-End mit einer Tabelle erstellt haben. In dieser Anleitung wird davon ausgegangen, dass die Tabelle das gleiche Schema wie die Tabellen in diesen Lernprogrammen aufweist. In dieser Anleitung wird davon ausgegangen, dass Sie Ihrem Code das Apache Cordova-Plug-In hinzugefügt haben. Falls noch nicht geschehen, können Sie das Apache Cordova-Plug-In über die Befehlszeile Ihrem Projekt hinzufügen:

```
cordova plugin add ms-azure-mobile-apps
```

Weitere Informationen zum Erstellen [Ihrer ersten Apache Cordova-App] finden Sie in der dazugehörigen Dokumentation.

##<a name="create-client"></a>Vorgehensweise: Erstellen von Clients

Stellen Sie eine Clientverbindung her, indem Sie ein `WindowsAzure.MobileServicesClient`-Objekt erstellen. Ersetzen Sie `appUrl` durch die URL zu Ihrer mobilen App.

```
var client = WindowsAzure.MobileServicesClient(appUrl);
```

##<a name="table-reference"></a>Vorgehensweise: Erstellen von Tabellenverweisen

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

Weitere Informationen zur Abfragesyntax finden Sie in der [Dokumentation zum „Query“-Objekt].

### Filtern von Daten auf dem Server

Sie können für den Tabellenverweis eine `where`-Klausel verwenden:

```
table
    .where({ userId: user.userId, complete: false })
    .read()
    .then(success, failure);
```

Sie können auch eine Funktion nutzen, die das Objekt filtert. In diesem Fall wird die `this`-Variable dem aktuell gefilterten Objekt zugewiesen. Folgendes ist funktionell gleichwertig mit dem vorherigen Beispiel:

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

Die `.includeTotalCount()`-Methode wird verwendet, um ein „totalCount“-Feld dem „results“-Objekt hinzuzufügen. Das „totalCount“-Feld wird mit der Gesamtanzahl von Datensätzen aufgefüllt, die zurückgegeben wird, wenn keine Seitenverwaltung verwendet wird.

Sie können dann die „pages“-Variable und einige Schaltflächen auf der Benutzeroberfläche verwenden, um eine Seitenliste bereitzustellen. Verwenden Sie „loadPage()“ zum Laden der neuen Datensätze für jede Seite. Implementieren Sie eine Zwischenspeicherung, um den Zugriff auf Datensätze zu beschleunigen, die bereits geladen wurden.


###<a name="sorting-data"></a>Vorgehensweise: Zurückgeben sortierter Daten

Verwenden Sie die Abfragemethoden „.orderBy()“ oder „.orderByDescending()“:

```
table
    .orderBy('name')
    .read()
    .then(success, failure);
```

Weitere Informationen zum „Query“-Objekt finden Sie in der [Dokumentation zum „Query“-Objekt].

##<a name="inserting"></a>Vorgehensweise: Einfügen von Daten

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

##<a name="modifying"></a>Vorgehensweise: Ändern von Daten

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

##<a name="deleting"></a>Vorgehensweise: Löschen von Daten

Rufen Sie die „.del()“-Methode auf, um einen Datensatz zu löschen. Übergeben Sie die ID in einen Objektverweis:

```
table
    .del({ id: '7163bc7a-70b2-4dde-98e9-8818969611bd' })
    .done(function () {
        // Record is now deleted - update your cache
    }, failure);
```

##<a name="auth"></a>Vorgehensweise: Authentifizieren von Benutzern

Azure App Service unterstützt die Authentifizierung und Autorisierung von Anwendungsbenutzern mit einer Vielzahl externer Identitätsanbieter: Facebook, Google, Microsoft-Konto und Twitter. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln in Serverskripts zu implementieren. Weitere Informationen finden Sie im Lernprogramm [Erste Schritte mit der Authentifizierung].

Bei Verwendung der Authentifizierung in einer Apache Cordova-App müssen die folgenden Cordova-Plug-Ins verfügbar sein:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Insgesamt werden zwei Authentifizierungsflüsse unterstützt: ein Serverfluss und ein Clientfluss. Der Serverfluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Authentifizierungs-Webschnittstelle des Anbieters verwendet wird. Der Clientfluss ermöglicht eine tiefere Integration mit gerätespezifischen Fähigkeiten wie z. B. einmalige Anmeldung, da in diesem Fall anbieterspezifische und gerätespezifische SDKs verwendet werden.

##<a name="server-auth"></a>Vorgehensweise: Authentifizieren mithilfe eines Anbieters (Serverfluss)

Sie müssen Ihre Anwendung bei Ihrem Identitätsanbieter registrieren, um Mobile Services die Verwaltung des Authentifizierungsprozesses in Ihrer Windows Store- oder HTML5-App zu ermöglichen. Anschließend müssen Sie in Ihrem Azure App Service die Anwendungs-ID und den geheimen Schlüssel Ihres Anbieters konfigurieren. Weitere Informationen finden Sie im Lernprogramm [Authentifizierung zu Ihrer App hinzufügen].

Nach der Registrierung bei Ihrem Identitätsanbieter können Sie die „.login()“-Methode mit dem Namens Ihres Anbieters aufrufen. Für die Facebook-Anmeldung verwenden Sie z. B. den folgenden Code.

```
client.login("facebook").done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```

Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den Wert, der an die „login“-Methode übergeben wird, in einen der folgenden Werte: `microsoftaccount`, `facebook`, `twitter`, `google` oder `aad`.

In diesem Fall verwaltet Azure App Service den OAuth 2.0-Authentifizierungsfluss, indem die Anmeldungsseite des ausgewählten Anbieters angezeigt und nach der erfolgreichen Anmeldung beim Identitätsanbieter ein App Service-Authentifizierungstoken generiert wird. Die „login“-Funktion gibt ein JSON-Objekt (user) zurück, das sowohl die Benutzer-ID als auch das App Service-Authentifizierungstoken in den Feldern userId bzw. authenticationToken zur Verfügung stellt. Dieses Token kann zwischen gespeichert und wiederverwendet werden, bis es abläuft.

##<a name="client-auth"></a>Vorgehensweise: Authentifizieren mithilfe eines Anbieters (Clientfluss)

Ihre Anwendung kann den Identitätsanbieter auch unabhängig kontaktieren und das zurückgegebene Token zur Authentifizierung Ihrem App Service vorlegen. Mit diesem Clientfluss können Sie die einmalige Anmeldung für Ihre Benutzer implementieren oder zusätzliche Benutzerdaten vom Identitätsanbieter abrufen.

### Einfaches Beispiel für die Authentifizierung über soziale Profile

Dieses Beispiel verwendet die Client-SDK von Facebook für die Authentifizierung:

```
client.login(
     "facebook",
     {"access_token": token})
.done(function (results) {
     alert("You are now logged in as: " + results.userId);
}, function (err) {
     alert("Error: " + err);
});
```
Dieses Beispiel geht davon aus, dass das vom jeweiligen Anbieter gelieferte Token in der token-Variable gespeichert wird.

### Beispiel mit Microsoft-Konto

Das folgende Beispiel verwendet das Live SDK, das einmalige Anmeldung für Windows Store-Apps mit Microsoft-Konto unterstützt:

```
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
```

Dieses Beispiel ruft ein Token von Live Connect ab und übergibt das Token in einem Aufruf der „login“-Funktion an Ihren App Service.

##<a name="auth-getinfo"></a>Vorgehensweise: Abrufen von Informationen zum authentifizierten Benutzer

Die Authentifizierungsinformationen für den aktuellen Benutzer können mit einer AJAX-Methode vom `/.auth/me`-Endpunkt abgerufen werden. Geben Sie beispielsweise Folgendes ein, um die API abzurufen:

```
var url = client.applicationUrl + '/.auth/me';
fetch(url)
    .then(function (data) {
        return data.json()
    }).then(function (user) {
        // The user object contains the claims for the authenticated user
    });
```

Sie können auch JQuery oder eine andere AJAX-API zum Abrufen der Informationen verwenden. Daten werden als JSON-Objekt empfangen.

##<a name="register-for-push"></a>Vorgehensweise: Registrieren für Pushbenachrichtigungen

Installieren Sie [phonegap-plug-in-push] zum Verarbeiten von Pushbenachrichtigungen. Dies kann problemlos mithilfe des `cordova plugin add`-Befehls über die Befehlszeile oder den Git-Plug-in-Installer in Visual Studio hinzugefügt werden. Der folgende Code in der Apache Cordova-App registriert Ihr Gerät für Pushbenachrichtigungen:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Verwenden Sie das Notification Hubs SDK zum Senden von Pushbenachrichtigungen vom Server. Pushbenachrichtigungen sollten niemals direkt von Clients gesendet werden, da sie zum Auslösen eines Denial-of-Service-Angriffs auf Notification Hubs oder PNS verwendet werden können.

<!-- URLs. -->
[Schnellstart von Azure Mobile Apps]: app-service-mobile-cordova-get-started.md
[Erste Schritte mit der Authentifizierung]: app-service-mobile-cordova-get-started-users.md
[Authentifizierung zu Ihrer App hinzufügen]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova-Plug-Ins für Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[Ihrer ersten Apache Cordova-App]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plug-in-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Dokumentation zum „Query“-Objekt]: https://msdn.microsoft.com/de-DE/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0224_2016-->