<properties
	pageTitle="Hinzufügen der Anmeldung bei einer Node.js-Web-App für Azure B2C-Vorschau | Microsoft Azure"
	description="Gewusst wie: Erstellen einer Node.js-Web-App, die Benutzer mithilfe eines B2C-Mandanten anmeldet"
	services="active-directory-b2c"
	documentationCenter=""
	authors="brandwe"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
  ms.tgt_pltfrm="na"
	ms.devlang="javascript"
	ms.topic="hero-article"
	ms.date="02/25/2016"
	ms.author="brandwe"/>


# B2C-Vorschau: Hinzufügen der Anmeldung bei einer Node.js-Web-App

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

**Passport** ist eine Authentifizierungs-Middleware für Node.js. Das äußerst flexible und modular aufgebaute Passport kann unauffällig in jeder Express-basierten oder Restify-Webanwendung installiert werden. Ein umfassender Satz an Strategien unterstützt die Authentifizierung mittels eines Benutzernamens und Kennworts in Facebook, Twitter und anderen Anwendungen.

> [AZURE.NOTE] Das Implementieren der Anmeldung, Registrierung und Profilverwaltung mit Azure Active Directory B2C wird in diesem Artikel nicht behandelt. Er konzentriert sich auf das Aufrufen von Web-APIs nach der Authentifizierung des Benutzers. Beginnen Sie, sofern noch nicht geschehen, mit dem [Tutorial zu den ersten Schritten mit .NET-Web-Apps](active-directory-b2c-devquickstarts-web-dotnet.md), um Informationen zu den Grundlagen von Azure AD B2C zu erhalten.

Wir haben eine Strategie für Azure Active Directory (Azure AD) entwickelt. Installieren Sie dieses Modul, und fügen Sie dann das Azure AD-`passport-azure-ad`-Plug-In hinzu.

Gehen Sie hierzu wie folgt vor:

1. Registrieren Sie eine Anwendung mithilfe von Azure AD.
2. Legen Sie fest, dass Ihre App das `passport-azure-ad`-Plug-In verwendet.
3. Verwenden Sie Passport zur Ausgabe von An- und Abmeldeanforderungen für Azure AD.
4. Drucken Sie die Benutzerdaten.

Der Code für dieses Tutorial [wird auf GitHub verwaltet](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS). Laden Sie dazu [das App-Gerüst als ZIP-Datei herunter](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip). Sie können das Gerüst auch klonen:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

Die fertige Anwendung wird am Ende dieses Lernprogramms bereitgestellt.

> [AZURE.WARNING] 	Für diese B2C-Vorschau müssen Sie die gleiche **Client-ID**/**Anwendungs-ID** und die gleichen Richtlinien sowohl für den Web-API-Aufgabenserver als auch den Client verwenden, der eine Verbindung damit herstellt. Dies gilt auch für die iOS- und Android-Tutorials. Wenn Sie zuvor eine Anwendung in einem der beiden Schnellstarts erstellt haben, verwenden Sie die Werte von dort; erstellen Sie keine neuen.

## Erstellen eines Azure AD B2C-Verzeichnisses

Bevor Sie Azure AD B2C verwenden können, müssen Sie ein Verzeichnis oder einen Mandanten erstellen. Ein Verzeichnis ist ein Container für all Ihre Benutzer, Apps, Gruppen usw. [Erstellen Sie ein B2C-Verzeichnis](active-directory-b2c-get-started.md) (sofern noch nicht geschehen), bevor Sie die weiteren Schritte in diesem Leitfaden ausführen.

## Erstellen einer Anwendung

Als Nächstes müssen Sie in Ihrem B2C-Verzeichnis eine App erstellen. Dadurch erhält Azure AD die Informationen, die für die sichere Kommunikation mit der App erforderlich sind. Sowohl die Client-App als auch die Web-API werden durch eine einzige **Anwendungs-ID** dargestellt, da sie zusammen eine logische App bilden. Befolgen Sie zum Erstellen einer App [diese Anweisungen](active-directory-b2c-app-registration.md). Führen Sie folgende Schritte aus:

- Fügen Sie der Anwendung eine **Web-App**/**Web-API** hinzu.
- Geben Sie `http://localhost/TodoListService` als **Antwort-URL** ein. Dies ist die Standard-URL für dieses Codebeispiel.
- Erstellen Sie einen **geheimen Schlüssel** für Ihre Anwendung, und kopieren Sie ihn. Sie benötigen sie später. Beachten Sie, dass dieser Wert vor der Verwendung in [XML-Escape-Zeichen](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) gesetzt werden muss.
- Kopieren Sie die **Anwendungs-ID**, die Ihrer App zugewiesen ist. Diese benötigen Sie später ebenfalls.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Erstellen der Richtlinien

In Azure AD B2C wird jede Benutzeroberfläche durch eine [Richtlinie](active-directory-b2c-reference-policies.md) definiert. Diese App enthält drei Oberflächen für die Identität: Registrierung, Anmeldung und Facebook-Anmeldung. Sie müssen eine Richtlinie jedes Typs erstellen, wie es im [Artikel zu Richtlinienreferenzen](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) beschrieben ist. Beachten Sie beim Erstellen der drei Richtlinien Folgendes:

- Wählen Sie den **Anzeigenamen** und andere Registrierungsattribute in der Registrierungsrichtlinie aus.
- Wählen Sie den **Anzeigenamen** und die **Objekt-ID** als Anwendungsansprüche in jeder Richtlinie aus. Sie können auch andere Ansprüche auswählen.
- Notieren Sie sich die **Namen** der einzelnen Richtlinien nach ihrer Erstellung. Sie müssen das Präfix `b2c_1_` aufweisen. Sie benötigen diese Richtliniennamen später.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Nachdem Sie die drei Richtlinien erstellt haben, können Sie Ihre App erstellen.

Beachten Sie, dass in diesem Artikel nicht behandelt wird, wie die gerade erstellten Richtlinien verwendet werden. Grundlegende Informationen zur Funktionsweise von Richtlinien in Azure AD B2C finden Sie im [Tutorial zu den ersten Schritten mit .NET-Web-Apps](active-directory-b2c-devquickstarts-web-dotnet.md).

## Hinzufügen von Voraussetzungen zu Ihrem Verzeichnis

Wechseln Sie über die Befehlszeile vom Verzeichnis zu Ihrem Stammordner, sofern Sie sich noch nicht dort befinden. Führen Sie die folgenden Befehle aus:

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

Zusätzlich haben wir `passport-azure-ad` für unsere Vorschau im Gerüst des Schnellstarts verwendet.

- `npm install passport-azure-ad`

Dadurch werden die Bibliotheken installiert, von denen `passport-azure-ad` abhängt.

## Richten Sie Ihre App zur Nutzung der Passport-Node.js-Strategie ein.
Konfigurieren Sie die Express-Middleware für die Verwendung des OpenID Connect-Authentifizierungsprotokolls. Passport wird unter anderem zum Ausgeben von Anmelde- und Abmeldeanforderungen, zum Verwalten der Benutzersitzungen und zum Abrufen von Benutzerinformationen verwendet.

Öffnen Sie die Datei `config.js` aus dem Stammverzeichnis des Projekts, und geben Sie die Konfigurationswerte Ihrer App im Abschnitt `exports.creds` ein.
- `clientID`: Die **Anwendungs-ID**, die Ihrer App im Registrierungsportal zugewiesen ist.
- `returnURL`: Der **Umleitungs-URI**, den Sie im Portal eingegeben haben.
- `tenantName`: Der Mandantenname Ihrer App, z. B. **contoso.onmicrosoft.com**.

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Öffnen Sie die Datei `app.js` im Stammverzeichnis des Projekts. Fügen Sie den folgenden Aufruf hinzu, um die Strategie `OIDCStrategy` aufzurufen, die in `passport-azure-ad` enthalten ist.


```JavaScript
var OIDCStrategy = require('passport-azure-ad').OIDCStrategy;

// Add some logging
var log = bunyan.createLogger({
    name: 'Microsoft OIDC Example Web Application'
});
```

Verwenden Sie die gerade referenzierte Strategie zum Verarbeiten von Anmeldeanforderungen.

```JavaScript
// Use the OIDCStrategy in Passport (Section 2).
//
//   Strategies in Passport require a "validate" function that accepts
//   credentials (in this case, an OpenID identifier), and invokes a callback
//   by using a user object.
passport.use(new OIDCStrategy({
    callbackURL: config.creds.returnURL,
    realm: config.creds.realm,
    clientID: config.creds.clientID,
    oidcIssuer: config.creds.issuer,
    identityMetadata: config.creds.identityMetadata,
    skipUserProfile: config.creds.skipUserProfile,
    responseType: config.creds.responseType,
    responseMode: config.creds.responseMode,
    tenantName: config.creds.tenantName
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
Passport verwendet ein ähnliches Muster für alle seine Strategien (einschließlich Twitter und Facebook). Beim Schreiben jeglicher Strategien wird dieses Muster angewandt. Wenn Sie sich die Strategie ansehen, sehen Sie, dass Sie `function()` mit einem Token und `done` als Parameter übergeben. Die Strategie kehrt wieder an Ihren Ausgangspunkt zurück, sobald alle Aufgaben abgeschlossen sind. Speichern Sie den Benutzer und das Token, damit Sie beides nicht mehr erfragen müssen.

> [AZURE.IMPORTANT]
Dieser Code verwendet alle Benutzer, die der Server authentifiziert. Dies wird als automatische Registrierung bezeichnet. Wenn Sie Produktionsserver verwenden, müssen Benutzer zunächst einen Registrierungsprozess durchlaufen, den Sie festlegen. Diesem Muster begegnen Sie häufig in Verbraucher-Apps. Dabei können Sie sich über Facebook registrieren, werden dann aber dazu aufgefordert, zusätzliche Informationen einzugeben. Wenn dies nicht eine Beispielanwendung wäre, könnten wir einfach eine E-Mail aus dem zurückgegebenen Tokenobjekt extrahieren und den Benutzer daraufhin zur Eingabe zusätzlicher Informationen auffordern. Da es sich um einen Testserver handelt, fügen wir die Benutzer einfach der Datenbank im Arbeitsspeicher hinzu.

Fügen Sie die Methoden hinzu, mit denen die angemeldeten Benutzer nachverfolgt werden können, wie es von Passport gefordert wird. Dies umfasst die Serialisierung und Deserialisierung von Benutzerinformationen:

```JavaScript

// Passport session setup. (Section 2)

//   To support persistent sign-in sessions, Passport needs to be able to
//   serialize users into and deserialize users out of sessions. Typically,
//   this is as simple as storing the user ID when Passport serializes a user
//   and finding the user by ID when Passport deserializes that user.
passport.serializeUser(function(user, done) {
  done(null, user.email);
});

passport.deserializeUser(function(id, done) {
  findByEmail(id, function (err, user) {
    done(err, user);
  });
});

// Array to hold users who have signed in
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

Fügen Sie den Code hinzu, um das Express-Modul zu laden. Im Folgenden sehen Sie, dass die Standardmuster `/views` und `/routes` verwendet werden, die Express bereitstellt.

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
  // Initialize Passport!  Also use passport.session() middleware to support
  // persistent sign-in sessions (recommended).
  app.use(passport.initialize());
  app.use(passport.session());
  app.use(app.router);
  app.use(express.static(__dirname + '/../../public'));
});

```

Fügen Sie die `POST`-Routen hinzu, die die eigentlichen Anmeldeanforderungen an die Engine `passport-azure-ad` übergeben:

```JavaScript

// Our Auth routes (Section 3)

// GET /auth/openid
//   Use passport.authenticate() as route middleware to authenticate the
//   request. The first step in OpenID authentication involves redirecting
//   the user to an OpenID provider. After the user is authenticated,
//   the OpenID provider redirects the user back to this application at
//   /auth/openid/return

app.get('/auth/openid',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {
    log.info('Authentication was called in the Sample');
    res.redirect('/');
  });

// GET /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it redirects the user to the home page.
app.get('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });

// POST /auth/openid/return
//   Use passport.authenticate() as route middleware to authenticate the
//   request. If authentication fails, the user will be redirected back to the
//   sign-in page. Otherwise, the primary route function will be called.
//   In this example, it will redirect the user to the home page.

app.post('/auth/openid/return',
  passport.authenticate('azuread-openidconnect', { failureRedirect: '/login' }),
  function(req, res) {

    res.redirect('/');
  });
```

## Verwenden von Passport zur Ausgabe von An- und Abmeldeanforderungen für Azure AD

Ihre App ist nun für die Kommunikation mit dem v2.0-Endpunkt über das OpenID Connect-Authentifizierungsprotokoll konfiguriert. `passport-azure-ad` hat dabei alle Details zur Erstellung von Authentifizierungsnachrichten, zur Überprüfung der Token von Azure AD und zur Verwaltung von Benutzersitzungen übernommen. Sie müssen Ihren Benutzern nur noch die An- und Abmeldung ermöglichen und zusätzliche Informationen zu den angemeldeten Benutzern sammeln.

Fügen Sie zunächst Ihrer Datei `app.js` die Standard-, Anmelde-, Konto- und Abmeldemethoden hinzu:

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

So überprüfen Sie diese Methoden im Detail:
- Die Route `/` leitet an die Ansicht `index.ejs` weiter und übergibt den Benutzer in der Anforderung (falls vorhanden).
- Die Route `/account` überprüft zuerst, ob Sie authentifiziert sind (die diesbezügliche Implementierung finden Sie weiter unten). Sie übergibt daraufhin den Benutzer in der Anforderung, sodass Sie zusätzliche Informationen über den Benutzer erhalten.
- Die Route `/login` ruft den Authenticator `azuread-openidconnect` von `passport-azure-ad` ab. Wenn dies nicht erfolgreich ist, leitet die Route den Benutzer wieder zu `/login` zurück.
- `/logout` ruft `logout.ejs` auf (und seine Route). Dadurch werden Cookies gelöscht, und der Benutzer kehrt zu `index.ejs` zurück.


Fügen Sie für den letzten Teil `app.js` die Methode `EnsureAuthenticated`, die in der Route `/account` verwendet wird, hinzu.

```JavaScript

// Simple route middleware to ensure that the user is authenticated. (Section 4)

//   Use this route middleware on any resource that needs to be protected. If
//   the request is authenticated (typically via a persistent sign-in session),
//   then the request will proceed. Otherwise, the user will be redirected to the
//   sign-in page.
function ensureAuthenticated(req, res, next) {
  if (req.isAuthenticated()) { return next(); }
  res.redirect('/login')
}

```

Erstellen Sie als Letztes den Server selbst in `app.js`.

```JavaScript

app.listen(3000);

```


## Erstellen der Ansichten und Routen in Express zum Aufrufen der Richtlinien

`app.js` ist nun vollständig. Fügen Sie nun nur noch die Routen und Ansichten hinzu, mit denen Sie die Anmelde- und Registrierungsrichtlinien aufrufen können. Diese behandeln auch die Routen `/logout` und `/login`, die Sie erstellt haben.

Erstellen der Route `/routes/index.js` im Stammverzeichnis

```JavaScript

/*
 * GET home page.
 */

exports.index = function(req, res){
  res.render('index', { title: 'Express' });
};
```

Erstellen der Route `/routes/user.js` im Stammverzeichnis

```JavaScript

/*
 * GET users listing.
 */

exports.list = function(req, res){
  res.send("respond with a resource");
};
```

Diese einfachen Routen übergeben lediglich Anforderungen an die Ansichten. Dazu zählt auch der Benutzer, sofern vorhanden.

Erstellen Sie die Ansicht `/views/index.ejs` im Stammverzeichnis. Dies ist eine einfache Seite, die die Anmelde- und Abmelderichtlinien aufruft. Sie können sie auch zum Erfassen von Kontoinformationen verwenden. Beachten Sie, dass die bedingte Anweisung `if (!user)` verwendet werden kann, da das Übergeben des Benutzers in der Anforderung belegt, dass er angemeldet ist.

```JavaScript
<% if (!user) { %>
	<h2>Welcome! Please sign in.</h2>
	<a href="/login/?p=your facebook policy">Sign in with Facebook</a>
	<a href="/login/?p=your email sign-in policy">Sign in with email</a>
	<a href="/login/?p=your email sign-up policy">Sign up with email</a>
<% } else { %>
	<h2>Hello, <%= user.displayName %>.</h2>
	<a href="/account">Account info</a></br>
	<a href="/logout">Log out</a>
<% } %>
```

Erstellen Sie die Ansicht `/views/account.ejs` im Stammverzeichnis, sodass zusätzliche Informationen angezeigt werden können, die von `passport-azure-ad` in die Benutzeranforderung eingefügt wurden.

```Javascript
<% if (!user) { %>
	<h2>Welcome! Please sign in.</h2>
	<a href="/login">Sign in</a>
<% } else { %>
<p>displayName: <%= user.displayName %></p>
<p>givenName: <%= user.name.givenName %></p>
<p>familyName: <%= user.name.familyName %></p>
<p>UPN: <%= user._json.upn %></p>
<p>Profile ID: <%= user.id %></p>
<p>Full Claims</p>
<%- JSON.stringify(user) %>
<p></p>
<a href="/logout">Log Out</a>
<% } %>
```

Erstellen Sie nun Ihre Anwendung, und führen Sie sie aus.

Führen Sie `node app.js` aus, und navigieren Sie zu `http://localhost:3000`.


Registrieren Sie sich bei der App mit Ihrer E-Mail-Adresse oder Ihrem Facebook-Konto, oder melden Sie sich auf diese Weise an. Melden Sie sich ab, und melden Sie sich als anderer Benutzer wieder an.

##Nächste Schritte

Als Referenz stellen wir das vollständige Beispiel (ohne Ihre Konfigurationswerte) [als ZIP-Datei](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip) bereit. Sie können sie auch aus GitHub klonen:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

Sie können nun mit den Themen für fortgeschrittenere Benutzer fortfahren. Versuchen Sie Folgendes:

[Schützen Sie eine Web-API mit dem B2C-Modell in Node.js.](active-directory-b2c-devquickstarts-api-node.md)

<!--

For additional resources, check out:
You can now move on to more advanced B2C topics. You might try:

[Call a Node.js web API from a Node.js web app]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=AcomDC_0302_2016-->