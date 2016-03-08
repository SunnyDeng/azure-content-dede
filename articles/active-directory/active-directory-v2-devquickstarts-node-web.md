<properties
	pageTitle="Azure AD v2.0: Node.js-Web-App | Microsoft Azure"
	description="Vorgehensweise beim Erstellen einer NodeJS-Web-App, bei der sich Benutzer sowohl mit ihrem persönlichen Microsoft-Konto als auch ihrem Geschäfts- oder Schulkonto anmelden können."
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
	ms.date="02/20/2016"
	ms.author="brandwe"/>

# Hinzufügen der Anmeldung zu einer Node.js-Web-App


> [AZURE.NOTE]
	Nicht alle Szenarios und Funktionen von Azure Active Directory werden vom v2.0-Endpunkt unterstützt. Lesen Sie die Informationen zu den [Einschränkungen des v2.0-Endpunkts](active-directory-v2-limitations.md), um zu bestimmen, ob Sie den v2.0-Endpunkt verwenden sollten.


Hier wird Passport für Folgendes verwendet:

- Anmelden des Benutzers bei der App mit Azure AD und dem v2.0-Endpunkt
- Anzeigen einiger Informationen zum Benutzer
- Abmelden des Benutzers von der App

**Passport** ist eine Authentifizierungs-Middleware für Node.js. Das äußerst flexible und modular aufgebaute Passport kann unauffällig in jede Express- oder Restify-basierte Webanwendung integriert werden. Ein umfassender Satz an Strategien unterstützt die Authentifizierung mittels eines Benutzernamens und Kennworts in Facebook, Twitter und anderen Anwendungen. Wir haben eine Strategie für Microsoft Azure Active Directory entwickelt. Dieses Modul installieren Sie nun und fügen dann das Microsoft Azure Active Directory-Plug-In `passport-azure-ad` hinzu.

## Herunterladen

Der Code für dieses Tutorial wird [auf GitHub](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs) verwaltet. Um folgen zu können, können Sie [das App-Gerüst als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/skeleton.zip) oder das Gerüst klonen:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Die fertige Anwendung wird außerdem am Ende dieses Lernprogramms bereitgestellt.

## 1\. Registrieren einer App
Erstellen Sie eine neue App unter [apps.dev.microsoft.com](https://apps.dev.microsoft.com), oder führen Sie die folgenden [ausführlichen Schritte](active-directory-v2-app-registration.md) aus. Stellen Sie sicher, dass Sie:

- die Ihrer App zugewiesene **Anwendungs-ID** kopieren. Sie benötigen Sie in Kürze.
- die **Web**-Plattform für Ihre App hinzufügen.
- den richtigen **Umleitungs-URI** eingeben. Der Umleitungs-URI verweist auf Azure AD, wohin Authentifizierungsantworten gesendet werden sollen – der Standardwert in diesem Lernprogramm lautet `http://localhost:3000/auth/openid/return`.

## 2\. Erforderliche Komponenten zu Ihrem Verzeichnis hinzufügen

Wechseln Sie über die Befehlszeile vom Verzeichnis auf Ihren Stammordner, wenn dies noch nicht der Fall ist, und führen Sie die folgenden Befehle aus:

- `npm install express`
- `npm install ejs`
- `npm install ejs-locals`
- `npm install restify`
- `npm install mongoose`
- `npm install bunyan`
- `npm install assert-plus`
- `npm install passport`
- `npm install webfinger`
- `npm install body-parser`
- `npm install express-session`
- `npm install cookie-parser`

- Zusätzlich haben wir `passport-azure-ad` im Gerüst des Schnellstarts verwendet.

- `npm install passport-azure-ad`


Dadurch werden die Bibliotheken installiert, von denen "passport-azure-ad" abhängt.

## 3\. Richten Sie Ihre App zur Nutzung der "passport-node-js"-Strategie ein.
Hier konfigurieren wir die Express-Middleware für die Verwendung des Authentifizierungsprotokolls OpenID Connect. Passport wird unter anderem für die Ausgabe von Anmelde- und Abmeldeanforderungen, für die Verwaltung der Benutzerssitzungen und für das Abrufen der Benutzerinformationen verwendet.

-	Öffnen Sie zunächst die Datei `config.js` aus dem Stammverzeichnis des Projekts, und geben Sie die Konfigurationswerte Ihrer App im Abschnitt `exports.creds` ein.
    -	`clientID:` ist die **Anwendungs-ID**, die Ihrer App im Registrierungsportal zugewiesen ist.
    -	`returnURL` ist der **Umleitungs-URI**, den Sie im Portal eingegeben haben.
    - `clientSecret` ist der geheime Schlüssel, den Sie im Portal generiert haben.

- Öffnen Sie als Nächstes die Datei `app.js` im Stammverzeichnis des Projekts, und fügen Sie den Aufruf hinzu, um die `OIDCStrategy`-Strategie aufzurufen, die zu `passport-azure-ad` gehört.


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

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
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    skipUserProfile: config.creds.skipUserProfile
    scope: config.creds.scope
  },
  function(iss, sub, profile, accessToken, refreshToken, done) {
    log.info('Example: Email address we received was: ', profile.email);
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

> [AZURE.IMPORTANT]
Der obige Code erfasst alle Benutzer, die sich an unserem Server authentifizieren. Dies wird als automatische Registrierung bezeichnet. Bei Produktionsservern müssten die Benutzer zunächst einen Registrierungsprozess durchlaufen, den Sie festlegen. Dies ist normalerweise das Muster, das Sie bei Consumer-Apps finden, die es Ihnen ermöglichen, sich bei Facebook zu registrieren, aber dann dazu auffordern, zusätzliche Informationen anzugeben. Wenn dies nicht eine Beispielanwendung wäre, könnten wir einfach die E-Mail aus dem Tokenobjekt extrahieren, das zurückgegeben wird, und dann dazu auffordern, zusätzliche Informationen einzugeben. Da es sich um einen Testserver handelt, fügen wir sie einfach der Datenbank im Arbeitsspeicher hinzu.

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

- Schließlich fügen Sie die POST-Routen hinzu, die die eigentlichen Anmeldeanforderungen an das Modul `passport-azure-ad` übergeben:

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
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

    res.redirect('/');
  });
```

## 4\. Verwenden von Passport zur Ausgabe von An- und Abmeldeanforderungen für Azure AD

Ihre App ist nun ordnungsgemäß für die Kommunikation mit dem v2.0-Endpunkt über das OpenID Connect-Authentifizierungsprotokoll konfiguriert. `passport-azure-ad` hat dabei alle Details zur Erstellung von Authentifizierungsnachrichten, zur Überprüfung der Token von Azure AD und zur Verwaltung von Benutzersitzungen übernommen. Sie müssen es Ihren Benutzern nur noch ermöglichen, sich anzumelden und abzumelden, und zusätzliche Informationen zu den angemeldeten Benutzer sammeln.

- Zuerst fügen wir der Datei `app.js` Standard-, Anmelde-, Konto- und Abmeldemethoden hinzu:

```JavaScript

//Routes (Section 4)

app.get('/', function(req, res){
  res.render('index', { user: req.user });
});

app.get('/account', ensureAuthenticated, function(req, res){
  res.render('account', { user: req.user });
});

app.get('/login',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Login was called in the Sample');
    res.redirect('/');
});

app.get('/logout', function(req, res){
  req.logout();
  res.redirect('/');
});

```

-	Betrachten Sie diese im Detail:
    -	Die Route `/` leitet an die Ansicht "index.ejs" weiter und übergibt den Benutzer in der Anforderung (falls vorhanden).
    - Die Route `/account` ***stellt zuerst sicher, dass wir authentifiziert sind*** (wird weiter unten implementiert) und übergibt dann den Benutzer in der Anforderung, damit wir zusätzliche Informationen zum Benutzer abrufen können.
    - Die Route `/login` ruft die azuread-openidconnect -Authentifizierung von `passport-azuread` auf und leitet den Benutzer an "/login" um, wenn dies nicht erfolgreich ist.
    - `/logout` ruft einfach „logout.ejs“ (und die Route) auf, um Cookies zu löschen, und leitet dann den Benutzer zu „index.ejs“ zurück.


- Im letzten Teil von `app.js` fügen wir die "EnsureAuthenticated"-Methode hinzu, die in `/account` oben verwendet wird.

```JavaScript

// Simple route middleware to ensure user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

- Schließlich erstellen wir den Server selbst in `app.js`:

```JavaScript

app.listen(3000);

```


## 5\. Erstellen von Ansichten und Routen in Express, um die Benutzer auf der Website anzuzeigen

`app.js` ist jetzt vollständig. Nun müssen einfach die Routen und Ansichten hinzugefügt werden, die die Informationen für die Benutzer anzeigen und die erstellten Routen `/logout` und `/login` verarbeiten.

- Erstellen der Route `/routes/index.js` im Stammverzeichnis

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

- Erstellen der Route `/routes/user.js` im Stammverzeichnis

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Diese einfachen Routen übergeben lediglich die Anforderung an die Ansichten, einschließlich des Benutzers, falls vorhanden.

- Erstellen Sie die Ansicht `/views/index.ejs` im Stammverzeichnis. Dies ist eine einfache Seite, die die Anmelde- und Abmeldemethoden aufruft und es ermöglicht, Kontoinformationen zu erfassen. Beachten Sie, dass eine bedingte `if (!user)`-Anweisung verwendet werden kann, da das Übergeben des Benutzers in der Anforderung belegt, dass er ein angemeldeter Benutzer ist.

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
</html>
```

Erstellen und führen Sie Ihre Anwendung zum Schluss aus!

Führen Sie `node app.js` aus, und navigieren Sie zu `http://localhost:3000`.


Melden Sie sich entweder mit einem persönlichen Microsoft-Konto oder einem Geschäfts- oder Schulkonto an, und beachten Sie, wie die Identität des Benutzers in der Liste "/account" dargestellt wird. Sie verfügen jetzt über eine mit branchenüblichen Protokollen gesicherte Web-App, die Benutzer mit ihren persönlichen Konten oder ihren Geschäfts- oder Schulkonten authentifizieren kann.

##Nächste Schritte

Als Referenz stellen wir das vollständige Beispiel (ohne Ihre Konfigurationswerte) [hier als ZIP-Datei bereit](https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs/archive/complete.zip). Sie können es alternativ aus GitHub klonen:

```git clone --branch complete https://github.com/AzureADQuickStarts/AppModelv2-WebApp-OpenIDConnect-nodejs.git```

Sie können nun mit den Themen für fortgeschrittenere Benutzer fortfahren. Wie wäre es zum Beispiel mit Folgendem:

[Schützen einer Node.js-Web-API mit dem v2.0-Endpunkt >>](active-directory-v2-devquickstarts-node-api.md)

Weitere Ressourcen: - [v2.0-Entwicklerhandbuch >>](active-directory-appmodel-v2-overview.md) - [StackOverflow-Tag „azure-active-directory“ >>](http://stackoverflow.com/questions/tagged/azure-active-directory)

<!---HONumber=AcomDC_0224_2016-->