<properties
	pageTitle="Azure AD AngularJS – Erste Schritte | Microsoft Azure"
	description="In diesem Thema erfahren Sie, wie eine einseitige AngularJS-Anwendung erstellt wird, die für die Anmeldung in Azure AD integriert wird und über OAuth per Azure AD geschützte APIs aufruft."
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
	ms.date="10/13/2015"
	ms.author="dastrock"/>


# Schützen von einseitigen AngularJS-Apps mit Azure AD

[AZURE.INCLUDE [active-directory-devquickstarts-switcher](../../includes/active-directory-devquickstarts-switcher.md)]

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

Mit Azure AD ist es einfach und problemlos möglich, Ihren einseitigen Apps die Anmeldung, die Abmeldung und sichere OAuth-API-Aufrufe hinzuzufügen. Ihre App kann Benutzer dann über ihre Active Directory-Konten authentifizieren und jede per Azure AD geschützte Web-API nutzen, z. B. die Office 365-APIs oder die Azure-API.

Für JavaScript-Anwendungen, die in einem Browser ausgeführt werden, stellt Azure AD die Active Directory Authentication Library (bzw. „adal.js“) bereit. Die einzige Aufgabe von „Adal.js“ besteht darin, Ihrer App das Abrufen von Zugriffstoken zu erleichtern. Um Ihnen zu zeigen, wie einfach es geht, erstellen wir hier eine AngularJS-Anwendung mit einer Aufgabenliste, mit der folgende Aktionen ausgeführt werden können:

- Anmelden von Benutzern an der App mit Azure AD als Identitätsanbieter
- Anzeigen einiger Informationen zum Benutzer
- Sicheres Aufrufen der Aufgabenlisten-API einer App mit Bearertoken aus AAD
- Abmelden von Benutzern von der App

Zur Entwicklung der vollständigen Arbeitsanwendung müssen Sie folgende Schritte ausführen:

2. Registrieren Ihrer Anwendung bei Azure AD
3. Installieren Sie die ADAL, und konfigurieren Sie die einseitige Anwendung (SPA).
5. Verwenden Sie die ADAL, um Seiten in der einseitigen Anwendung zu schützen.

Beginnen Sie, indem Sie [das Anwendungsgerüst herunterladen](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/skeleton.zip) oder [das vollständige Beispiel herunterladen](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip). Außerdem benötigen Sie einen Azure AD-Mandanten, unter dem Sie Benutzer erstellen und Ihre Anwendung registrieren können. Wenn Sie über noch keinen Mandanten verfügen, [erfahren Sie hier, wie Sie einen erhalten](active-directory-howto-tenant.md).

## *1. Registrieren der Anwendung DirectorySearcher*
Sie müssen die App zuerst in Ihrem Azure AD-Mandanten registrieren, um damit Benutzer authentifizieren und Token abrufen zu können:

-	Melden Sie sich am [Azure-Verwaltungsportal](https://manage.windowsazure.com) an.
-	Klicken Sie in der linken Navigationsleiste auf **Active Directory**.
-	Wählen Sie den Mandanten aus, unter dem die Anwendung registriert werden soll.
-	Klicken Sie auf die Registerkarte **Anwendungen** und dann unten auf **Hinzufügen**.
-	Folgen Sie den Bildschirmaufforderungen, und erstellen Sie eine neue **Webanwendung und/oder Web-API**.
    -	Am **Namen** der Anwendung sollten die Endbenutzer die Funktion der Anwendung ablesen können.
    -	Der **Umleitungs-URI** gibt den Ort an, an dem AAD die Token zurückgibt. Der Standardspeicherort für dieses Beispiel lautet `https://localhost:44326/`.
-	Nach Abschluss der Registrierung weist AAD Ihrer App eine **eindeutige Client-ID** zu. Diesen Wert benötigen Sie in den nächsten Abschnitten. Kopieren Sie ihn daher aus der Registerkarte **Konfigurieren**.
- Für „adal.js“ wird der implizite OAuth-Fluss genutzt, um mit Azure AD zu kommunizieren. Sie müssen den impliziten Fluss für Ihre Anwendung wie folgt aktivieren:
    - Laden Sie das Anwendungsmanifest herunter, indem Sie auf **Manifest verwalten** klicken.
    - Öffnen Sie das Manifest, und suchen Sie nach der `oauth2AllowImplicitFlow`-Eigenschaft. Legen Sie den Wert der Eigenschaft auf `true` fest.
    - Führen Sie das Speichern und Hochladen des Anwendungsmanifests durch, indem Sie erneut auf **Manifest verwalten** klicken.

## *2. Installieren der ADAL und Konfigurieren der einseitigen Anwendung*
Nachdem Sie nun eine Anwendung in Azure AD erstellt haben, können Sie „adal.js“ installieren und Ihren identitätsbezogenen Code schreiben.

-	Beginnen Sie, indem Sie „adal.js“ über die Paket-Manager-Konsole dem Projekt mit der einseitigen Aufgabenlisten-Anwendung (TodoSPA) hinzufügen:
  - Laden Sie [adal.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal.js) herunter, und fügen Sie die Datei dem Projektverzeichnis `App/Scripts/` hinzu.
  - Laden Sie [adal-angular.js](https://raw.githubusercontent.com/AzureAD/azure-activedirectory-library-for-js/master/lib/adal-angular.js) herunter, und fügen Sie die Datei dem Projektverzeichnis `App/Scripts/` hinzu.
  - Laden Sie jedes Skript vor dem Ende von `</body>` in `index.html`:

```js
...
<script src="App/Scripts/adal.js"></script>
<script src="App/Scripts/adal-angular.js"></script>
...
```

-	Damit die Back-End-Aufgabenlisten-API der einseitigen Anwendung Token aus dem Browser akzeptieren kann, benötigt das Back-End Konfigurationsinformationen zur App-Registrierung. Öffnen Sie `web.config` im Projekt mit der einseitigen Aufgabenlisten-Anwendung. Ersetzen Sie die Werte der Elemente im Abschnitt `<appSettings>` durch die Werte, die Sie im Azure-Verwaltungsportal eingegeben haben. Wenn die ADAL in Ihrem Code verwendet wird, verweist er auf diese Werte.
    -	`ida:Tenant` ist die Domäne Ihres Azure AD-Mandanten, z. B. „contoso.onmicrosoft.com“.
    -	`ida:Audience` muss die **Client-ID** der Anwendung sein, die Sie aus dem Portal kopiert haben.

## *3. Verwenden von ADAL zum Schützen von Seiten in der einseitigen Anwendung*
„adal.js“ wurde für die Integration in die AngularJS-Route und in HTTP-Anbieter erstellt, damit Sie einzelne Ansichten in Ihrer einseitigen Anwendung schützen können.

- Binden Sie in `App/Scripts/app.js` das Modul „adal.js“ ein:

```js
angular.module('todoApp', ['ngRoute','AdalAngular'])
.config(['$routeProvider','$httpProvider', 'adalAuthenticationServiceProvider',
 function ($routeProvider, $httpProvider, adalProvider) {
...
```
- Sie können `adalProvider` jetzt mit den Konfigurationswerten Ihrer Anwendungsregistrierung initialisieren, ebenfalls in `App/Scripts/app.js`:

```js
adalProvider.init(
  {
      instance: 'https://login.microsoftonline.com/',
      tenant: 'Enter your tenant name here e.g. contoso.onmicrosoft.com',
      clientId: 'Enter your client ID here e.g. e9a5a8b6-8af7-4719-9821-0deef255f68e',
      extraQueryParameter: 'nux=1',
      //cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
  },
  $httpProvider
);
```
- Zum Schützen der Ansicht `TodoList` in der App ist nur eine Codezeile erforderlich: `requireADLogin`.

```js
...
}).when("/TodoList", {
        controller: "todoListCtrl",
        templateUrl: "/App/Views/TodoList.html",
        requireADLogin: true,
...
```

Sie verfügen jetzt über eine sichere einseitige Anwendung, mit der Benutzer angemeldet und per Bearertoken geschützte Anforderungen für die Back-End-API ausgegeben werden können. Wenn ein Benutzer auf den Link `TodoList` klickt, wird „adal.js“ zur Anmeldung automatisch an Azure AD umgeleitet, falls dies erforderlich ist. Außerdem ordnet „adal.js“ allen AJAX-Anforderungen, die an das Back-End der Anwendung gesendet werden, automatisch ein access\_token-Element zu. Die obigen Angaben beschreiben die erforderlichen Mindestvoraussetzungen, die zum Erstellen einer einseitigen Anwendung per „adal.js“ erforderlich sind. Es gibt aber noch einige andere Features, die in einseitigen Anwendungen nützlich sind:

- Zum expliziten Ausgeben von Anmeldungs- und Abmeldungsanforderungen können Sie Funktionen in Ihren Controllern definieren, mit denen „adal.js“ aufgerufen wird. In `App/Scripts/homeCtrl.js`:

```js
...
$scope.login = function () {
    adalService.login();
};
$scope.logout = function () {
    adalService.logOut();
};
...
```
- Es kann auch sein, dass Sie in der App-UI Benutzerinformationen präsentieren möchten. Der ADAL-Dienst wurde dem `userDataCtrl`-Controller bereits hinzugefügt, sodass Sie auf das `userInfo`-Objekt in der zugeordneten Ansicht (`App/Views/UserData.html`) zugreifen können:

```js
<p>{{userInfo.userName}}</p>
<p>aud:{{userInfo.profile.aud}}</p>
<p>iss:{{userInfo.profile.iss}}</p>
...
```

- Es gibt auch viele Fälle, in denen Sie wissen möchten, ob der Benutzer angemeldet oder nicht angemeldet ist. Sie können auch das `userInfo`-Objekt verwenden, um diese Informationen zu sammeln. In `index.html` können Sie beispielsweise je nach Authentifizierungsstatus entweder die Schaltfläche „Anmelden“ oder „Abmelden“ anzeigen:

```js
<li><a class="btn btn-link" ng-show="userInfo.isAuthenticated" ng-click="logout()">Logout</a></li>
<li><a class="btn btn-link" ng-hide=" userInfo.isAuthenticated" ng-click="login()">Login</a></li>
```

Glückwunsch! Der Vorgang für Ihre in Azure AD integrierte einseitige Anwendung ist jetzt abgeschlossen. Damit können Benutzer authentifiziert werden, das Back-End kann mit OAuth 2.0 sicher aufgerufen werden, und es können grundlegende Informationen zum Benutzer abgerufen werden. Sofern nicht bereits geschehen, ist es nun an der Zeit, Ihren Mandanten mit Benutzern zu füllen. Führen Sie Ihre einseitige Aufgabenlistenanwendung aus, und melden Sie sich mit einem der Benutzer an. Fügen Sie der Aufgabenliste des Benutzers Aufgaben hinzu, melden Sie sich ab, und melden Sie sich dann wieder an.

„adal.js“ erleichtert Ihnen die Integration all dieser allgemeinen Identitätsfunktionen in Ihre Anwendung. Die Datei übernimmt die unangenehmen Verwaltungsarbeiten für Sie – die Cacheverwaltung, die Unterstützung des OAuth-Protokolls, die Anzeige einer Anmeldeschnittstelle für den Benutzer, die Aktualisierung abgelaufener Token und vieles mehr.

Als Referenz stellen wir [hier](https://github.com/AzureADQuickStarts/SinglePageApp-AngularJS-DotNet/archive/complete.zip) das vollständige Beispiel (ohne Ihre Konfigurationswerte) bereit. Sie können sich nun weiteren Szenarien zuwenden. Sie können beispielsweise Folgendes testen:

[Aufrufen einer CORS Web-API aus einer einseitigen Anwendung >>](https://github.com/AzureAdSamples/SinglePageApp-WebAPI-AngularJS-DotNet)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!---HONumber=AcomDC_1125_2015-->