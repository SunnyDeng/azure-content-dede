<properties
	pageTitle="Erste Schritte für das Anmelden und Abmelden bei Azure AD mit ";node.js";"
	description="In diesem Thema erfahren Sie, wie eine Express-MVC-Web-App mit ";node.js"; erstellt wird, die für die Anmeldung in Azure AD integriert wird."
	services="active-directory"
	documentationCenter="nodejs"
	authors="brandwe"
	manager="mbaldwin"
	editor=""/>

<tags
	ms.service="active-directory"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="brandwe"/>

# An- und Abmeldung bei Webanwendungen mit Azure AD


Hier wird Passport für Folgendes verwendet:

- Anmelden des Benutzers bei der App mit Azure AD und dem App-Modell v2.0
- Anzeigen einiger Informationen zum Benutzer
- Abmelden des Benutzers von der App

**Passport** ist eine Authentifizierungs-Middleware für Node.js. Das äußerst flexible und modular aufgebaute Passport kann unauffällig in jede Express- oder Restify-basierte Webanwendung integriert werden. Ein umfassender Satz an Strategien unterstützt die Authentifizierung mittels eines Benutzernamens und Kennworts in Facebook, Twitter und anderen Anwendungen. Wir haben eine Strategie für Microsoft Azure Active Directory entwickelt. Dieses Modul installieren Sie nun und fügen dann das Microsoft Azure Active Directory-Plug-In `passport-azure-ad` hinzu.

Dazu müssen Sie folgende Schritte ausführen:

1. Registrieren einer App
2. Richten Sie Ihre App zur Nutzung der Passport-azure-ad-Strategie ein.
3. Verwenden Sie Passport zur Ausgabe von An- und Abmeldeanforderungen für Azure AD.
4. Ausdrucken von Informationen zum Benutzer

Der Code für dieses Tutorial wird [auf GitHub](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS) verwaltet. Um folgen zu können, können Sie [das App-Gerüst als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) oder das Gerüst klonen:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Die fertige Anwendung wird außerdem am Ende dieses Lernprogramms bereitgestellt.

## 1. Registrieren einer App
- Melden Sie sich beim Azure-Verwaltungsportal an.
- Klicken Sie in der linken Navigationsleiste auf **Active Directory**..
- Wählen Sie den Mandanten aus, in dem die Beispielanwendung registriert werden soll.
- Klicken Sie auf die Registerkarte Anwendungen und dann in der unteren Schublade auf **Hinzufügen**.
- Folgen Sie den Bildschirmaufforderungen, und erstellen Sie eine neue **Webanwendung und/oder Web-API**.
    - Am **Namen** der Anwendung sollten die Endbenutzer die Funktion der Anwendung ablesen können
    -	Die **Anmelde-URL** ist die Basis-URL Ihrer Anwendung.  Der Standardwert des Gerüsts lautet `http://localhost:3000/auth/openid/return`.
    - Die **App-ID-URI** ist eine eindeutige Kennung für die Anwendung.  Üblicherweise wird `https://<tenant-domain>/<app-name>` verwendet, zum Beispiel: `https://contoso.onmicrosoft.com/my-first-aad-app`
- Nach Abschluss der Registrierung weist AAD Ihrer Anwendung eine eindeutige Client-ID zu. Diesen Wert benötigen Sie in den nächsten Abschnitten, weswegen Sie ihn aus der Registerkarte „Konfigurieren“ kopieren sollten.

## 2. Erforderliche Komponenten zu Ihrem Verzeichnis hinzufügen

Wechseln Sie über die Befehlszeile vom Verzeichnis auf Ihren Stammordner, wenn dies noch nicht der Fall ist, und führen Sie die folgenden Befehle aus:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`

- Dadurch werden die Bibliotheken installiert, von denen `passport-azure-ad` abhängt:

- `npm install passport-azure-ad`

Dadurch werden die Bibliotheken installiert, von denen "passport-azure-ad" abhängt.

## 3. Richten Sie Ihre App zur Nutzung der "passport-node-js"-Strategie ein
Hier konfigurieren wir die Express-Middleware für die Verwendung des Authentifizierungsprotokolls OpenID Connect.  Passport wird unter anderem für die Ausgabe von Anmelde- und Abmeldeanforderungen, für die Verwaltung der Benutzerssitzungen und für das Abrufen der Benutzerinformationen verwendet.

-	Öffnen Sie zunächst die Datei `web.config` aus dem Stammverzeichnis des Projekts, und geben Sie die Konfigurationswerte Ihrer App im Abschnitt `<appSettings>` ein.
    -	`clientID`: ist die **Anwendungs-ID**, die Ihrer App im Registrierungsportal zugewiesen ist.
    -	`returnURL` ist der **Umleitungs-URI**, den Sie im Portal eingegeben haben.
    - `clientSecret` ist der geheime Schlüssel, den Sie im Portal generiert haben.

- Öffnen Sie als Nächstes die Datei `app.js`  im Stammverzeichnis des Projekts, und fügen Sie den Aufruf hinzu, um die `OIDCStrategy`-Strategie aufzurufen, die zu `passport-azure-ad` gehört.


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Hinzufügen einer Protokollierung

var log = bunyan.createLogger({ name: 'Beispiel für eine Microsoft OIDC-Webanwendung' }); ```

- Verwenden Sie danach die Strategie, auf die gerade verwiesen wurde, um die Anmeldeanforderungen zu verarbeiten.

```JavaScript
// Use the OIDCStrategy within Passport. (Section 2) 
// 
//   Strategies in passport require a `validate` function, which accept
//   credentials (in this case, an OpenID identifier), and invoke a callback
//   with a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    clientSecret: config.creds.clientSecret,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    if (!profile.email) {
      return done(new Error("No email found"), null);
    }
    // asynchronous verification, for effect...
    process.nextTick(function () {
      findByEmail(profile.email, function(err, user) {
        if (err) {
          return done(err);
        }
        if (!user) {
          // "Auto-registration"
          users.push(profile);
          return done(null, profile);
        }
        return done(null, user);
      });
    });
  }
));
```
Passport verwendet ein ähnliches Muster für alle Strategien (Twitter, Facebook usw.), das beim Schreiben aller Strategien eingehalten wird. Beim Betrachten der Strategie sehen Sie, dass ein function()-Element übergeben wird, das Token- und done-Elemente als Parameter aufweist. Die Strategie kehrt wieder an den Ausgangspunkt zurück, sobald die gesamte Arbeit abgeschlossen ist. Sobald dies der Fall ist, speichern wir den Benutzer und das Token, damit wir beides nicht mehr erfragen müssen.


> [AZURE.IMPORTANT]Der obige Code erfasst alle Benutzer, die sich an unserem Server authentifizieren. Dies wird als automatische Registrierung bezeichnet. Bei Produktionsservern müssten die Benutzer zunächst einen Registrierungsprozess durchlaufen, den Sie festlegen. Dies ist normalerweise das Muster, das Sie bei Consumer-Apps finden, die es Ihnen ermöglichen, sich bei Facebook zu registrieren, aber dann dazu auffordern, zusätzliche Informationen anzugeben. Wenn dies nicht eine Beispielanwendung wäre, könnten wir einfach die E-Mail aus dem Tokenobjekt extrahieren, das zurückgegeben wird, und dann dazu auffordern, zusätzliche Informationen einzugeben. Da es sich um einen Testserver handelt, fügen wir sie einfach der Datenbank im Arbeitsspeicher hinzu.

- Als Nächstes fügen Sie die Methoden hinzu, mit denen die angemeldeten Benutzer nachverfolgt werden können, wie es von Passport gefordert wird. Dies umfasst die Serialisierung und Deserialisierung von Informationen des Benutzers:

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent login sessions, Passport needs to be able to
//   serialize users into and deserialize users out of the session.  Typically,
//   this will be as simple as storing the user ID when serializing, and finding
//   the user by ID when deserializing.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// array to hold logged in users
var users = [];

var findByEmail = function(email, fn) {
  for (var i = 0, len = users.length; i < len; i++) {
    var user = users[i];
   log.info('we are using user: ', user);
    if (user.email === email) {
      return fn(null, user);
    }
  }
  return fn(null, null);
};
```

- Als Nächstes fügen Sie den Code hinzu, um das Express-Modul zu laden. Hier sehen Sie, dass wir die standardmäßigen /views- und /routes-Muster verwenden, die Express bietet.

```JavaScript

// configure Express (Section 2)

var app = express();


app.configure(function() {
  app.set('views', __dirname + '/views');
  app.set('view engine', 'ejs');
  app.use(express.logger());
  app.use(express.methodOverride());
  app.use(cookieParser());
  app.use(expressSession({ secret: 'keyboard cat', resave: true, saveUninitialized: false }));
  app.use(bodyParser.urlencoded({ extended : true }));
  // Initialize Passport!  Also use passport.session() middleware, to support
  // persistent login sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

- Schließlich fügen Sie die Routen hinzu, die die eigentlichen Anmeldeanforderungen an die Engine `passport-azure-ad` übergeben:

```JavaScript

// Our Auth routes (Section 3)

// POST /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  The first step in OpenID authentication will involve redirecting
//   the user to their OpenID provider.  After authenticating, the OpenID
//   provider will redirect the user back to this application at
//   /auth/openid/return
app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authenitcation was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request.  If authentication fails, the user will be redirected back to the
//   login page.  Otherwise, the primary route function function will be called,
//   which, in this example, will redirect the user to the home page.
app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('We received a return from AzureAD.');
    res.redirect('/');
  });
  ```

## 4. Use Passport to issue sign-in and sign-out requests to Azure AD

Your app is now properly configured to communicate with the v2.0 endpoint using the OpenID Connect authentication protocol.  `passport-azure-ad` has taken care of all of the ugly details of crafting authentication messages, validating tokens from Azure AD, and maintaining user session.  All that remains is to give your users a way to sign in, sign out, and gather additional info on the logged in user.

- First, lets add the default, login, account, and logout methods to our `app.js` file:

```JavaScript

//Routen (Abschnitt 4)

app.get('/', function(req, res){ res.render('index', { user: req.user }); });

app.get('/account', ensureAuthenticated, function(req, res){ res.render('account', { user: req.user }); });

app.get('/login', passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }), function(req, res) { log.info('Anmeldung wurde in Beispiel aufgerufen'); res.redirect('/'); });

app.get('/logout', function(req, res){ req.logout(); res.redirect('/'); });

```

-	Let's review these in detail:
    -	The `/` route will redirect to the index.ejs view passing the user in the request (if it exists)
    - The `/account` route will first ***ensure we are authenticated*** (we implement that below) and then pass the user in the request so that we can get additional information about the user.
    - The `/login` route will call our azuread-openidconnect authenticator from `passport-azuread` and if that doesn't succeed will redirect the user back to /login
    - The `/logout` will simply call the logout.ejs (and route) which clears cookies and then return the user back to index.ejs


- For the last part of `app.js`, let's add the EnsureAuthenticated method that is used in `/account` above.

```JavaScript

// Einfache Routenmiddleware, um sicherzustellen, dass der Benutzer authentifiziert ist. (Abschnitt 4)

// Verwenden Sie diese Routenmiddleware für jede Ressource, die geschützt werden muss. Wenn // die Anforderung authentifiziert ist (in der Regel über eine permanente Anmeldesitzung), // wird die Anforderung fortgesetzt. Andernfalls wird der Benutzer zur // Anmeldeseite umgeleitet. function ensureAuthenticated(req, res, next) { if (req.isAuthenticated()) { return next(); } res.redirect('/login') } ```

- Erstellen wir schließlich den Server selbst in `app.js`:

```JavaScript

app.listen(3000);

```


## 5\. Erstellen von Ansichten und Routen in Express, um die Benutzer auf der Website anzuzeigen

`app.js` ist jetzt vollständig. Nun müssen einfach die Routen und Ansichten hinzugefügt werden, die die Informationen für die Benutzer anzeigen und die erstellten Routen `/logout` und `/login` verarbeiten.

- Erstellen Sie die Route `/routes/index.js` im Stammverzeichnis.

```JavaScript
/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- Erstellen Sie die Route `/routes/user.js` im Stammverzeichnis.

```JavaScript
/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Diese einfachen Routen übergeben lediglich die Anforderung an die Ansichten, einschließlich des Benutzers, falls vorhanden.

- Erstellen Sie die Ansicht `/views/index.ejs` im Stammverzeichnis. Dies ist eine einfache Seite, die die Anmelde- und Abmeldemethoden aufruft und es ermöglicht, Kontoinformationen zu erfassen. Beachten Sie, dass eine bedingte `if (!user)`-Anweisung verwendet werden kann, da belegt ist, dass der in der Anforderung übergebene Benutzer ein angemeldeter Benutzer ist.

```JavaScript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
	<h2>Hello, <%= user.displayName %>.</h2>
	<a href="/account">Account Info</a></br>
	<a href="/logout">Log Out</a>
<% } %>

```

- Erstellen Sie die Ansicht `/views/account.ejs` im Stammverzeichnis, sodass zusätzliche Informationen angezeigt werden können, die von `passport-azuread` in die Benutzeranforderung eingefügt wurden.

```Javascript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login">Log In</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claimes</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>

```

- Abschließend wird die Darstellung durch Hinzufügen eines Layouts optimiert. Erstellen Sie die Ansicht "/views/layout.ejs" im Stammverzeichnis.

```HTML

<!DOCTYPE html>
<html>
	<head>
		<title>Passport-OpenID Example</title>
	</head>
	<body>
		<% if (!user) { %>
			<p>
			<a href="/">Home</a> | 
			<a href="/login">Log In</a>
			</p>
		<% } else { %>
			<p>
			<a href="/">Home</a> | 
			<a href="/account">Account</a> | 
			<a href="/logout">Log Out</a>
			</p>
		<% } %>
		<%- body %>
	</body>
</html>```

Finally, build and run your app! 

Run `node app.js` and navigate to `http://localhost:3000`


Sign in with either a personal Microsoft Account or a work or school account, and notice how the user's identity is reflected in the /account list.  You now have a web app secured using industry standard protocols that can authenticate users with both their personal and work/school accounts.

For reference, the completed sample (without your configuration values) [is provided as a .zip here](https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS/archive/complete.zip), or you can clone it from GitHub:

```git clone --branch complete https://github.com/AzureADQuickStarts/WebApp-OpenIDConnect-NodeJS.git```


Sie können nun mit den Themen für fortgeschrittenere Benutzer fortfahren. Wie wäre es zum Beispiel mit Folgendem:

[Schützen einer Web-API mit Azure AD >>](active-directory-devquickstarts-webapi-nodejs.md)

[AZURE.INCLUDE [active-directory-devquickstarts-additional-resources](../../includes/active-directory-devquickstarts-additional-resources.md)]

<!----HONumber=September15_HO1-->