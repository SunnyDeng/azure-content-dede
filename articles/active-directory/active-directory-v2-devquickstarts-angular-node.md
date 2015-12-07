<properties
	pageTitle="Azure AD AngularJS – Erste Schritte | Microsoft Azure"
	description="Vorgehensweise beim Erstellen einer einseitigen AngularJS-App, bei der sich Benutzer sowohl mit ihrem persönlichen Microsoft-Konto als auch ihrem Geschäfts- oder Schulkonto anmelden können."
	services="active-directory"
	documentationCenter=""
	authors="dstrockis"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="11/19/2015"
	ms.author="dastrock"/>


# App-Modell v2.0 (Vorschauversion): Hinzufügen der Anmeldung zu einer einseitigen AngularJS-App – NodeJS

In diesem Artikel verwenden wir das wir das App-Modell v2.0 von Azure Active Directory, um einer AngularJS-App eine Anmeldung mit Microsoft-basierten Konten hinzuzufügen. Mit dem App-Model v2.0 können Sie eine einzelne Integration in Ihrer App ausführen und Benutzer sowohl mit persönlichen als auch mit Geschäfts-/Schulkonten authentifizieren.

Bei dem Beispiel handelt es sich um eine einfache Aufgabenlisten-App mit einer Seite, die die Aufgaben in einer Back-End-REST-API speichert. Sie ist in NodeJS geschrieben und wird mit einem OAuth-Bearertoken aus Azure AD gesichert. Die AngularJS-App verwendet unsere Open Source JavaScript-Authentifizierungsbibliothek [adal.js](https://github.com/AzureAD/azure-activedirectory-library-for-js), um den gesamten Anmeldeprozess abzuwickeln und Token für den Aufruf der REST-API abzurufen. Nach dem gleichen Muster kann die Authentifizierung auch bei anderen REST-APIs ausgeführt werden, z. B. bei der [Microsoft Graph](https://graph.microsoft.com)-API oder der Azure-Ressourcen-Manager-API.

## Herunterladen

Bevor Sie anfangen können, müssen Sie [node.js](https://nodejs.org) herunterladen und installieren. Anschließend können Sie eine Skelett-App [herunterladen](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/skeleton.zip) oder klonen:

```
git clone --branch skeleton https://github.com/AzureADQuickStarst/AppModelv2-SinglePageApp-AngularJS-NodeJS.git
```

Die Skelett-App enthält alle Codebausteine für eine einfache AngularJS-App, aber es fehlen alle identitätsbezogenen Teile. Wenn Sie dieser Beschreibung nicht genau folgen möchten, können Sie anstelle dessen auch das vollständige Muster [herunterladen](https://github.com/AzureADQuickStarts/AppModelv2-SinglePageApp-AngularJS-NodeJS/archive/complete.zip) oder klonen.

```
git clone https://github.com/AzureADSamples/SinglePageApp-AngularJS-NodeJS.git
```

## Registrieren einer App

Erstellen Sie zuerst eine App im [App-Registrierungsportal](https://apps.dev.microsoft.com), oder führen Sie [diese detailliert beschriebenen Schritte](active-directory-v2-app-registration.md) aus. Stellen Sie sicher, dass Sie:

- die **Web**-Plattform für Ihre App hinzufügen.
- den richtigen **Umleitungs-URI** eingeben. Die Standardeinstellung für dieses Beispiel lautet `http://localhost:8080`.
- das Kontrollkästchen **Impliziten Fluss zulassen** aktiviert lassen. 

Kopieren oder notieren Sie die Ihrer App zugewiesene **Anwendungs-ID**. Sie benötigen Sie in Kürze.

## Installieren von „adal.js“
Navigieren Sie zu Beginn zum heruntergeladenen Projekt, und installieren Sie „adal.js“. Wenn bei Ihnen [bower](http://bower.io/) installiert ist, können Sie einfach diesen Befehl ausführen. Falls Abhängigkeitsversionskonflikte auftreten, wählen Sie die höhere Version. ```
bower install adal-angular#experimental
```

Alternativ können Sie [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal.min.js) und [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/experimental/dist/adal-angular.min.js) auch manuell herunterladen. Fügen Sie beide Dateien dem Verzeichnis `app/lib/adal-angular-experimental/dist` hinzu.

Öffnen Sie das Projekt nun in Ihrem bevorzugten Text-Editor, und laden Sie „adal.js“ am Ende des Seitentexts:

```html
<!--index.html-->

...

<script src="App/bower_components/dist/adal.min.js"></script>
<script src="App/bower_components/dist/adal-angular.min.js"></script>

...
```

## Einrichten der REST-API

Kümmern wir uns nun bei der Einrichtung um die Back-End-REST-API. Installieren Sie über eine Eingabeaufforderung alle nötigen Pakete, indem Sie in der obersten Verzeichnisebene des Projekts folgenden Befehl ausführen:

```
npm install
```

Öffnen Sie jetzt `config.js`, und ersetzen Sie den `audience`-Wert:

```js
exports.creds = {
     
     // TODO: Replace this value with the Application ID from the registration portal
     audience: '<Your-application-id>',
	 
	 ...
}
```

Die REST-API verwendet diesen Wert, um die von der Angular-App bei AJAX-Anforderungen eingehenden Token zu validieren. Beachten Sie, dass diese einfache REST-API die Daten im Arbeitsspeicher speichert. Das heißt, dass bei jedem Stoppen des Servers alle zuvor erstellten Aufgaben verloren gehen.

Das war auch schon alles, was hier zur Arbeitsweise der REST-API gesagt werden soll. Sehen Sie sich gern den Code noch ein wenig genauer an. Wenn Sie mehr über das Absichern von Web-APIs mit Azure AD erfahren möchten, lesen Sie [diesen Artikel](active-directory-v2-devquickstarts-node-api.md).

## Anmelden von Benutzern
Widmen wir uns nun dem Code für die Identität. Wie Sie vielleicht schon bemerkt haben, enthält „adal.js“ einen AngularJS-Anbieter. Das sorgt für ein gutes Zusammenspiel mit den Angular-Routingmechanismen. Fügen Sie der App zunächst das ADAL-Modul hinzu.

```js
// app/scripts/app.js

angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {

...
```

Nun können Sie den `adalProvider` mit Ihrer Anwendungs-ID initialisieren:

```js
// app/scripts/app.js

...

adalProvider.init({
        
        // Use this value for the public instance of Azure AD
        instance: 'https://login.microsoftonline.com/', 
        
        // The 'common' endpoint is used for multi-tenant applications like this one
        tenant: 'common',
        
        // Your application id from the registration portal
        clientId: '<Your-application-id>',
        
        // If you're using IE, uncommment this line - the default HTML5 sessionStorage does not work for localhost.
        //cacheLocation: 'localStorage',
         
    }, $httpProvider);
```

„adal.js“ besitzt jetzt alle nötigen Informationen, um die App zu sichern und Benutzer anzumelden. Um das Anmelden über eine bestimmte Route in der App zu erzwingen, wird nur eine Zeile Code benötigt:

```js
// app/scripts/app.js

...

}).when("/TodoList", {
    controller: "todoListCtrl",
    templateUrl: "/static/views/TodoList.html",
    requireADLogin: true, // Ensures that the user must be logged in to access the route
})

...
```

Wenn ein Benutzer nun auf den Link `TodoList` klickt, erfolgt durch „adal.js“ automatisch eine Umleitung zu Azure AD, um bei Bedarf die Anmeldung auszuführen. Sie können auch explizit Anmelde- und Abmeldeanforderungen senden, indem Sie „adal.js“ in Ihren Controllern aufrufen:

```js
// app/scripts/homeCtrl.js

angular.module('todoApp')
// Load adal.js the same way for use in controllers and views   
.controller('homeCtrl', ['$scope', 'adalAuthenticationService','$location', function ($scope, adalService, $location) {
    $scope.login = function () {
        
        // Redirect the user to sign in
        adalService.login();
        
    };
    $scope.logout = function () {
        
        // Redirect the user to log out    
        adalService.logOut();
    
    };
...
```

## Anzeigen von Benutzerinformationen
Jetzt ist der Benutzer angemeldet, und Sie müssen wahrscheinlich in der Anwendung auf die Authentifizierungsdaten des angemeldeten Benutzers zugreifen. „Adal.js“ macht diese Informationen im `userInfo`-Objekt verfügbar. Um auf dieses Objekt in einer Ansicht zuzugreifen, fügen Sie zunächst „adal.js“ zum Stammbereich des entsprechenden Controllers hinzu:

```js
// app/scripts/userDataCtrl.js

angular.module('todoApp')
// Load ADAL for use in view
.controller('userDataCtrl', ['$scope', 'adalAuthenticationService', function ($scope, adalService) {}]);
```

Anschließend können Sie das `userInfo`-Objekt direkt in der Ansicht adressieren:

```html
<!--app/views/UserData.html-->

...

    <!--Get the user's profile information from the ADAL userInfo object-->
    <tr ng-repeat="(key, value) in userInfo.profile">
        <td>{{key}}</td>
        <td>{{value}}</td>
    </tr>
...
```

Sie können anhand des `userInfo`-Objekts auch ermitteln, ob der Benutzer angemeldet ist oder nicht.

```html
<!--index.html-->

...

    <!--Use the ADAL userInfo object to show the right login/logout button-->
    <ul class="nav navbar-nav navbar-right">
        <li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
        <li><a class="btn btn-link" ng-hide="userInfo.isAuthenticated" ng-click="login()">Login</a></li>
    </ul>
...
```

## Aufrufen der REST-API
Nun können Sie Token abrufen und die REST-API aufrufen, um Aufgaben zu erstellen, zu lesen, zu aktualisieren und zu löschen. Aber wissen Sie was? Sie müssen hier *nichts* tun. „Adal.js“ kümmert sich automatisch um das Abrufen, Zwischenspeichern und Aktualisieren von Token. Außerdem werden diese Token an die ausgehenden AJAX-Anforderungen angefügt, die Sie an die REST-API senden.

Wie funktioniert das genau? Möglich wird dies durch [AngularJS-Interceptors](https://docs.angularjs.org/api/ng/service/$http). Mit ihrer Hilfe kann „adal.js“ aus- und eingehende HTTP-Nachrichten transformieren. Darüber hinaus geht „adal.js“ davon aus, dass alle Anforderungen, die an dieselbe Domäne wie das Fenster gesendet werden, Token verwenden sollten, die für dieselbe Anwendungs-ID wie die AngularJS-App bestimmt sind. Daher haben wir sowohl in der Angular-App als auch in der NodeJS-REST-API die gleiche Anwendungs-ID verwendet. Natürlich können Sie dieses Verhalten überschreiben und „adal.js“ bei Bedarf zum Abrufen von Token für andere REST-APIs anweisen. Für dieses einfache Szenario sind die Standardeinstellungen jedoch ausreichend.

Der folgende Codeausschnitt veranschaulicht, wie einfach das Senden von Anforderungen mit Bearertoken von Azure AD ist:

```js
// app/scripts/todoListSvc.js

...
return $http.get('/api/tasks');
...
```

Glückwunsch! Ihre in Azure AD integrierte einseitige App ist nun vollständig. Genießen Sie das. Jetzt können Benutzer authentifiziert werden, die zugehörige Back-End-REST-API kann mit OpenID Connect sicher aufgerufen werden, und es können grundlegende Informationen zum Benutzer abgerufen werden. Von Anfang an werden alle Benutzer mit einem persönlichen Microsoft-Konto oder einem Geschäfts-/Schulkonto von Azure AD unterstützt. Testen Sie nun die App, indem Sie Folgendes ausführen:

```
node server.js
```

Navigieren Sie in einem Browser zu `http://localhost:8080`. Melden Sie sich mit einem persönlichen Microsoft-Konto oder einem Geschäfts- oder Schulkonto an. Fügen Sie der Aufgabenliste des Benutzers Aufgaben hinzu, und melden Sie sich ab. Versuchen Sie, sich mit dem anderen Kontotyp anzumelden. Falls Sie zum Erstellen von Geschäfts- oder Schulbenutzern einen Azure AD-Mandanten benötigen, [erfahren Sie hier, wie Sie einen Mandanten einrichten](active-directory-howto-tenant.md) (kostenlos).

Weitere Informationen zur Vorschauversion des App-Modells v2.0 finden Sie unter [v2.0 – Entwicklerhandbuch](active-directory-appmodel-v2-overview.md). Weitere Ressourcen:

- [Azure-Beispiele auf GitHub >>](https://github.com/Azure-Samples)
- [Azure AD auf Stack Overflow >>](http://stackoverflow.com/questions/tagged/azure-active-directory)
- Azure AD-Dokumentation auf [Azure.com >>](http://azure.microsoft.com/documentation/services/active-directory/)

<!---HONumber=AcomDC_1125_2015-->