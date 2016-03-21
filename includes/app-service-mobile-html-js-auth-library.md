##<a name="server-auth"></a>Vorgehensweise: Authentifizieren mithilfe eines Anbieters (Serverfluss)

Sie müssen Ihre App bei Ihrem Identitätsanbieter registrieren, um Mobile Services die Verwaltung des Authentifizierungsprozesses in Ihrer App zu ermöglichen. Anschließend müssen Sie in Ihrem Azure App Service die Anwendungs-ID und den geheimen Schlüssel Ihres Anbieters konfigurieren. Weitere Informationen finden Sie im Tutorial [Hinzufügen von Authentifizierung zu Ihrer App].

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

<!---HONumber=AcomDC_0309_2016-->