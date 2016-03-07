<properties
	pageTitle="Hinzufügen der Anmeldung bei einer NodeJS-Web-App für Azure B2C (Vorschauversion) | Microsoft Azure"
	description="Erstellen einer Node.js-Web-App, die Benutzer bei einem B2C-Mandanten anmeldet."
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
	ms.topic="article"
	ms.date="02/18/2016"
	ms.author="brandwe"/>

# B2C (Vorschauversion): Hinzufügen der Anmeldung bei einer NodeJS-Web-App


[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]
	Dieser Artikel behandelt nicht das Implementieren der Anmeldung, Registrierung und Profilverwaltung mit Azure AD B2C. Er konzentriert sich auf das Aufrufen von Web-APIs aufrufen, nachdem der Benutzer bereits authentifiziert wurde. Wenn nicht bereits erfolgt, sollten Sie mit dem [Lernprogramm "Erste Schritte mit .NET-Web-App"](active-directory-b2c-devquickstarts-web-dotnet.md) beginnen, um Informationen zu den Grundlagen von Azure AD B2C zu erhalten.

**Passport** ist eine Authentifizierungs-Middleware für Node.js. Das äußerst flexible und modular aufgebaute Passport kann unauffällig in jede Express- oder Restify-basierte Webanwendung integriert werden. Ein umfassender Satz an Strategien unterstützt die Authentifizierung mittels eines Benutzernamens und Kennworts in Facebook, Twitter und anderen Anwendungen. Wir haben eine Strategie für Microsoft Azure Active Directory entwickelt. Dieses Modul installieren Sie nun und fügen dann das Microsoft Azure Active Directory-Plug-In `passport-azure-ad` hinzu.

Dazu müssen Sie folgende Schritte ausführen:

1. Registrieren einer Anwendung in Azure AD
2. Richten Sie Ihre App für die Verwendung des azure-ad-passport-Plug-Ins von Passport ein.
3. Verwenden Sie Passport zur Ausgabe von An- und Abmeldeanforderungen für Azure AD.
4. Ausdrucken von Informationen zum Benutzer

Der Code für dieses Lernprogramm wird [auf GitHub](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS) verwaltet. Um folgen zu können, können Sie [das App-Gerüst als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/skeleton.zip) oder das Gerüst klonen:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS.git```

Die fertige Anwendung wird außerdem am Ende dieses Lernprogramms bereitgestellt.

> [AZURE.WARNING] 	Für unsere B2C-Vorschau müssen Sie die gleiche Client- oder Anwendungs-ID und die gleichen Richtlinien sowohl für den Web-API-Aufgabenserver als auch den Client verwenden, zu dem eine Verbindung hergestellt wird. Dies gilt für unsere IOS- und Android-Lernprogramme. Wenn Sie zuvor eine Anwendung in einem der beiden Schnellstarts erstellt haben, verwenden Sie die Werte von dort, anstatt wie unten neue zu erstellen.

## 1\. Erstellen eines Azure AD B2C-Verzeichnisses

Bevor Sie Azure AD B2C verwenden können, müssen Sie ein Verzeichnis oder einen Mandanten erstellen. Ein Verzeichnis ist ein Container für alle Benutzer, Apps, Gruppen usw. Wenn Sie noch nicht über ein Verzeichnis verfügen,[ erstellen Sie ein B2C-Verzeichnis](active-directory-b2c-get-started.md), bevor Sie fortfahren.

## 2\. Erstellen einer Anwendung

Nun müssen Sie eine App in Ihrem B2C-Verzeichnis erstellen, sodass Azure AD die Informationen erhält, die für die sichere Kommunikation mit Ihrer App erforderlich sind. Die Client-App und die Web-API werden in diesem Fall durch eine einzige **Anwendungs-ID** dargestellt, da sie zusammen eine logische App bilden. Befolgen Sie zum Erstellen einer App [diese Anweisungen](active-directory-b2c-app-registration.md). Ersetzen Sie

- Fügen Sie der Anwendung eine **Web-App/Web-API** hinzu.
- Geben Sie `http://localhost/TodoListService` als **Antwort-URL** ein – dies ist die Standard-URL für dieses Codebeispiel.
- Erstellen Sie einen **geheimen Schlüssel für Ihre Anwendung**, und notieren Sie ihn sich. Sie benötigen sie später. Beachten Sie, dass dieser Wert vor der Verwendung in [XML-Escapezeichen](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) gesetzt werden muss.
- Notieren Sie die **Anwendungs-ID**, die Ihrer App zugewiesen ist. Sie benötigen sie ebenfalls in Kürze.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## 3\. Erstellen der Richtlinien

In Azure AD B2C wird jede Benutzeroberfläche durch eine [**Richtlinie**](active-directory-b2c-reference-policies.md) definiert. Diese Anwendung enthält drei Oberflächen für die Identität: Registrierung, Anmeldung und Facebook-Anmeldung. Sie müssen eine Richtlinie jedes Typs erstellen, wie im [Artikel mit Richtlinienreferenzen](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) beschrieben. Wenn Sie die drei Richtlinien erstellen, beachten Sie Folgendes:

- Wählen Sie den **Anzeigenamen** und einige andere Registrierungsattribute in der Registrierungsrichtlinie aus.
- Wählen Sie den **Anzeigenamen** und die **Objekt-ID** als Anwendungsansprüche in jeder Richtlinie aus. Sie können auch andere Ansprüche auswählen.
- Notieren Sie sich die **Namen** der einzelnen Richtlinien nach ihrer Erstellung. Sie müssen das Präfix `b2c_1_` aufweisen. Sie benötigen diese Richtliniennamen in Kürze.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Nachdem Sie die drei Richtlinien erfolgreich erstellt haben, können Sie Ihre App erstellen.

Beachten Sie, dass in diesem Artikel nicht behandelt wird, wie die gerade erstellten Richtlinien verwendet werden. Weitere Informationen zur Funktionsweise von Richtlinien in Azure AD B2C finden Sie im [Tutorial "Erste Schritte mit .NET-Web-Apps"](active-directory-b2c-devquickstarts-web-dotnet.md).



## 4\. Erforderliche Komponenten zu Ihrem Verzeichnis hinzufügen

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

- Zusätzlich haben wir `passport-azure-ad` für unsere Vorschau im Gerüst des Schnellstarts verwendet.

- `npm install passport-azure-ad`


Dadurch werden die Bibliotheken installiert, von denen "passport-azure-ad" abhängt.

## 5\. Richten Sie Ihre App zur Nutzung der "passport-node-js"-Strategie ein.
Hier konfigurieren wir die Express-Middleware für die Verwendung des Authentifizierungsprotokolls OpenID Connect. Passport wird unter anderem für die Ausgabe von Anmelde- und Abmeldeanforderungen, für die Verwaltung der Benutzerssitzungen und für das Abrufen der Benutzerinformationen verwendet.

-	Öffnen Sie zunächst die Datei `config.js` aus dem Stammverzeichnis des Projekts, und geben Sie die Konfigurationswerte Ihrer App im Abschnitt `exports.creds` ein.
    -	`clientID:` ist die **Anwendungs-ID**, die Ihrer App im Registrierungsportal zugewiesen ist.
    -	`returnURL` ist der **Umleitungs-URI**, den Sie im Portal eingegeben haben.
    - `tenantName:` ist der **Mandantenname** Ihrer App, z. B. "contoso.onmicrosoft.com".

[AZURE.INCLUDE [active-directory-b2c-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

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


## 5\. Erstellen der Ansichten und Routen in Express, um unsere Richtlinien aufzurufen

`app.js` ist jetzt vollständig. Nun müssen einfach die Routen und Ansichten hinzugefügt werden, die es uns erlauben, Anmelde- und Registrierungsrichtlinien aufzurufen sowie die von uns erstellten Routen `/logout` und `/login` zu verarbeiten.

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

- Erstellen Sie die Ansicht `/views/index.ejs` im Stammverzeichnis. Dies ist eine einfache Seite, die unsere Richtlinien für die An- und Abmeldung aufruft und es ermöglicht, Kontoinformationen zu erfassen. Beachten Sie, dass eine bedingte `if (!user)`-Anweisung verwendet werden kann, da das Übergeben des Benutzers in der Anforderung belegt, dass er ein angemeldeter Benutzer ist.

```JavaScript
<% if (!user) { %>
	<h2>Welcome! Please log in.</h2>
	<a href="/login/?p=your facebook policy">Sign In with Facebook</a>
	<a href="/login/?p=your email sign-in policy">Sign In With Email</a>
	<a href="/login/?p=your email sign-up policy">Sign Up With Email</a>
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

Erstellen und führen Sie Ihre Anwendung zum Schluss aus!

Führen Sie `node app.js` aus, und navigieren Sie zu `http://localhost:3000`.


Registrieren Sie sich bei der App mit Ihrer E-Mail-Adresse oder Ihrem Facebook-Konto, oder melden Sie sich auf diese Weise an. Melden Sie sich ab, und melden Sie sich als anderer Benutzer wieder an.



##Nächste Schritte

Als Referenz stellen wir das vollständige Beispiel (ohne Ihre Konfigurationswerte) [hier als ZIP-Datei bereit](https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-NodeJS/archive/complete.zip). Sie können es alternativ aus GitHub klonen:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebApp-OpenIDConnect-nodejs.git```

Sie können nun mit den Themen für fortgeschrittenere Benutzer fortfahren. Wie wäre es zum Beispiel mit Folgendem:

[Sichern einer Web-API mit dem B2C-Modell in "node.js" >>](active-directory-b2c-devquickstarts-api-node.md)

<!--

For additional resources, check out:
You can now move onto more advanced B2C topics.  You may want to try:

[Calling a node.js Web API from a node.js Web App >>]()

[Customizing the your B2C App's UX >>]()

-->

<!---HONumber=AcomDC_0224_2016-->