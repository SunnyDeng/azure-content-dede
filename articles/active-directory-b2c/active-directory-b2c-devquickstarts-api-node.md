<properties
	pageTitle="B2C (Vorschauversion): Schützen einer Web-API mit „Node.js“ | Microsoft Azure"
	description="Erstellen einer Node.js-Web-API, die Token von einem B2C-Mandanten akzeptiert"
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
	ms.date="02/17/2016"
	ms.author="brandwe"/>

# B2C (Vorschauversion): Schützen einer Web-API mit „node.js“

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]


> [AZURE.NOTE] Dieser Artikel behandelt nicht das Implementieren der Anmeldung, Registrierung und Profilverwaltung mit Azure AD B2C. Der Schwerpunkt liegt auf dem Aufrufen von Web-APIs, nachdem der Benutzer authentifiziert wurde. Wenn nicht bereits erfolgt, sollten Sie mit dem [Tutorial „Erste Schritte mit .NET-Web-App“](active-directory-b2c-devquickstarts-web-dotnet.md) beginnen, um Informationen zu den Grundlagen von Azure Active Directory B2C zu erhalten.


> [AZURE.NOTE]	Dieses Beispiel wurde geschrieben, um es mit unserer [iOS B2C-Beispielanwendung](active-directory-b2c-devquickstarts-ios.md) zu verbinden. Führen Sie zunächst diese exemplarische Vorgehensweise durch, und machen Sie dann mit dem Beispiel weiter.

**Passport** ist eine Authentifizierungs-Middleware für Node.js. Das äußerst flexible und modular aufgebaute Passport kann unauffällig in jeder Express-basierten oder Restify-Webanwendung installiert werden. Ein umfassender Satz an Strategien unterstützt die Authentifizierung mittels eines Benutzernamens und Kennworts in Facebook, Twitter und anderen Anwendungen. Wir haben eine Strategie für Azure Active Directory (Azure AD) entwickelt. Sie installieren dieses Modul und fügen dann das Azure AD-`passport-azure-ad`-Plug-In hinzu.

Dazu müssen Sie folgende Schritte ausführen:

1. Registrieren Sie eine Anwendung in Azure AD.
2. Richten Sie Ihre App für die Verwendung des `azure-ad-passport`-Plug-Ins von Passport ein.
3. Konfigurieren Sie eine Clientanwendung für den Aufruf der Web-API „To Do List“.

Der Code für dieses Tutorial wird [auf GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs) verwaltet. Um folgen zu können, laden Sie [das App-Gerüst als ZIP-Datei herunter](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/skeleton.zip) oder klonen das Gerüst:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```

Die fertige Anwendung wird am Ende dieses Lernprogramms bereitgestellt.

> [AZURE.WARNING] 	Für unsere B2C-Vorschau müssen Sie die gleiche **Client-ID**/**Anwendungs-ID** und die gleichen Richtlinien sowohl für den Web-API-Aufgabenserver als auch den Client verwenden, der eine Verbindung damit herstellt. Dies gilt auch für unsere IOS- und Android-Tutorials. Wenn Sie zuvor eine Anwendung in einem der beiden Schnellstarts erstellt haben, verwenden Sie die Werte von dort; erstellen Sie keine neuen.


## Erstellen eines Azure AD B2C-Verzeichnisses

Bevor Sie Azure AD B2C verwenden können, müssen Sie ein Verzeichnis oder einen Mandanten erstellen. Ein Verzeichnis ist ein Container für alle Ihre Benutzer, Apps, Gruppen usw. Wenn Sie noch keines haben [erstellen Sie ein B2C-Verzeichnis](active-directory-b2c-get-started.md), bevor Sie fortfahren.

## Erstellen einer Anwendung

Als nächstes müssen Sie eine App in Ihrem B2C-Verzeichnis erstellen, sodass Azure AD die Informationen erhält, die für die sichere Kommunikation mit Ihrer App erforderlich sind. In diesem Fall werden die Client-App und die Web-API durch eine einzige **Anwendungs-ID** dargestellt, da sie zusammen eine logische App bilden. Befolgen Sie zum Erstellen einer App [diese Anweisungen](active-directory-b2c-app-registration.md). Führen Sie folgende Schritte aus:

- Fügen Sie der Anwendung eine **Web-App/Web-API** hinzu.
- Geben Sie `http://localhost/TodoListService` als **Antwort-URL** ein. Dies ist die Standard-URL für dieses Codebeispiel.
- Erstellen Sie einen **geheimen Schlüssel für Ihre Anwendung**, und kopieren Sie ihn. Sie benötigen sie später. Sie benötigen sie später. Beachten Sie, dass dieser Wert vor der Verwendung in [XML-Escapezeichen](https://www.w3.org/TR/2006/REC-xml11-20060816/#dt-escape) gesetzt werden muss.
- Kopieren Sie die **Anwendungs-ID**, die Ihrer App zugewiesen ist. Diese benötigen sie später ebenfalls.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-v2-apps](../../includes/active-directory-b2c-devquickstarts-v2-apps.md)]

## Erstellen der Richtlinien

In Azure AD B2C wird jede Benutzeroberfläche durch eine [Richtlinie](active-directory-b2c-reference-policies.md) definiert. Diese Anwendung enthält drei Oberflächen für die Identität: Registrierung, Anmeldung und Facebook-Anmeldung. Sie müssen eine Richtlinie jedes Typs erstellen, wie im [Artikel zu Richtlinienreferenzen](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy) beschrieben. Beachten Sie beim Erstellen der drei Richtlinien Folgendes:

- Wählen Sie den **Anzeigenamen** und andere Registrierungsattribute in der Registrierungsrichtlinie aus.
- Wählen Sie den **Anzeigenamen** und die **Objekt-ID** als Anwendungsansprüche in jeder Richtlinie aus. Sie können auch andere Ansprüche auswählen.
- Notieren Sie sich die **Namen** der einzelnen Richtlinien nach ihrer Erstellung. Sie müssen das Präfix `b2c_1_` aufweisen. Sie benötigen diese Richtliniennamen später.

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-policy](../../includes/active-directory-b2c-devquickstarts-policy.md)]

Nachdem Sie die drei Richtlinien erstellt haben, können Sie Ihre App erstellen.

Beachten Sie, dass in diesem Artikel nicht behandelt wird, wie die gerade erstellten Richtlinien verwendet werden. Grundlegende Informationen zur Funktionsweise von Richtlinien in Azure AD B2C finden Sie im [Tutorial „Erste Schritte mit .NET-Web-Apps“](active-directory-b2c-devquickstarts-web-dotnet.md).

## Herunterladen der Datei „node.js“ für Ihre Plattform

Für die Verwendung dieses Beispiels benötigen Sie eine funktionsfähige Installation von Node.js.

Installieren Sie „Node.js“ von [http://nodejs.org](http://nodejs.org).

## Installieren von MongoDB für Ihre Plattform

Für die Verwendung dieses Beispiels benötigen Sie auch eine funktionsfähige Installation von MongoDB. Durch die Verwendung von MongoDB bleibt die REST-API über Serverinstanzen hinweg persistent.

Installieren Sie MongoDB von [mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] In dieser exemplarischen Vorgehensweise wird davon ausgegangen, dass Sie die Standardinstallation und die Standardserverendpunkte für MongoDB verwenden. Diese sind zum Zeitpunkt der Erstellung dieses Artikels: `mongodb://localhost`

## Installieren der Restify-Module in Ihrer Web-API

Zum Erstellen der REST-API verwenden Sie Restify. Restify ist ein einfaches und flexibles, von Express abgeleitetes Node.js-Anwendungsframework. Er verfügt über eine Reihe robuster Funktionen für die Erstellung von REST-APIs für Connect.

### Installieren von Restify

Wechseln Sie über die Befehlszeile zum Verzeichnis `azuread`. Wenn das Verzeichnis `azuread` nicht vorhanden ist, erstellen Sie es.

`cd azuread` oder `mkdir azuread;`

Geben Sie den folgenden Befehl ein:

`npm install restify`

Mit diesem Befehl wird Restify installiert.

#### Bei einem Fehler

In einigen Betriebssystemen tritt bei der Verwendung von `npm` möglicherweise der Fehler `Error: EPERM, chmod '/usr/local/bin/..'` auf, und Sie werden aufgefordert, das Konto als Administrator auszuführen. Verwenden Sie in diesem Fall den Befehl `sudo`, um `npm` mit einer höheren Berechtigungsstufe auszuführen.

#### Ist ein DTrace-Fehler aufgetreten?

Bei der Installation von Restify erhalten Sie eventuell einen Fehler wie den Folgenden:

```Shell
clang: error: no such file or directory: 'HD/azuread/node_modules/restify/node_modules/dtrace-provider/libusdt'
make: *** [Release/DTraceProviderBindings.node] Error 1
gyp ERR! build error
gyp ERR! stack Error: `make` failed with exit code: 2
gyp ERR! stack     at ChildProcess.onExit (/usr/local/lib/node_modules/npm/node_modules/node-gyp/lib/build.js:267:23)
gyp ERR! stack     at ChildProcess.EventEmitter.emit (events.js:98:17)
gyp ERR! stack     at Process.ChildProcess._handle.onexit (child_process.js:789:12)
gyp ERR! System Darwin 13.1.0
gyp ERR! command "node" "/usr/local/lib/node_modules/npm/node_modules/node-gyp/bin/node-gyp.js" "rebuild"
gyp ERR! cwd /Volumes/Development HD/azuread/node_modules/restify/node_modules/dtrace-provider
gyp ERR! node -v v0.10.11
gyp ERR! node-gyp -v v0.10.0
gyp ERR! not ok
npm WARN optional dep failed, continuing dtrace-provider@0.2.8
```

Restify stellt einen leistungsstarken Mechanismus zum Verfolgen von REST-Aufrufen mit DTrace bereit. Unter vielen Betriebssystemen steht DTrace jedoch nicht zur Verfügung. Sie können diese Fehler ignorieren.

Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

	restify@2.6.1 node_modules/restify
	├── assert-plus@0.1.4
	├── once@1.3.0
	├── deep-equal@0.0.0
	├── escape-regexp-component@1.0.2
	├── qs@0.6.5
	├── tunnel-agent@0.3.0
	├── keep-alive-agent@0.0.1
	├── lru-cache@2.3.1
	├── node-uuid@1.4.0
	├── negotiator@0.3.0
	├── mime@1.2.11
	├── semver@2.2.1
	├── spdy@1.14.12
	├── backoff@2.3.0
	├── formidable@1.0.14
	├── verror@1.3.6 (extsprintf@1.0.2)
	├── csv@0.3.6
	├── http-signature@0.10.0 (assert-plus@0.1.2, asn1@0.1.11, ctype@0.5.2)
	└── bunyan@0.22.0 (mv@0.0.5)

## Installieren von Passport in Ihrer Web-API

[Passport](http://passportjs.org/) ist eine Authentifizierungs-Middleware für Node.js. Das äußerst flexible und modular aufgebaute Passport kann unauffällig in jeder Express-basierten oder Restify-Webanwendung installiert werden. Ein umfassender Satz an Strategien unterstützt die Authentifizierung mittels eines Benutzernamens und Kennworts in Facebook, Twitter und anderen Anwendungen. Wir haben eine Strategie für Azure AD entwickelt. Sie installieren dieses Modul und fügen dann das Plug-In für die Azure AD-Strategie hinzu.

Wechseln Sie über die Befehlszeile zum Verzeichnis `azuread`, wenn es nicht bereits geöffnet ist.

Geben Sie für die Installation von Passport den folgenden Befehl ein.

`npm install passport`

Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## Hinzufügen von „passport-azuread“ zu Ihrer Web-API

Als Nächstes fügen Sie die OAuth-Strategie hinzu; dazu verwenden Sie `passport-azuread`, eine Reihe von Strategien, die Azure AD mit Passport verbinden. Verwenden Sie diese Strategie für Bearertoken im Rest-API-Beispiel.

> [AZURE.NOTE] OAuth2 bietet zwar ein Framework, in dem alle bekannten Tokentypen ausgegeben werden können, allerdings findet hiervon meist nur ein eingeschränkter Tokensatz Anwendung. Die Token für den Schutz von Endpunkten sind Bearertoken. Diese Tokentypen werden in OAuth2 am häufigsten ausgestellt. Bei vielen Implementierungen wird davon ausgegangen, dass Bearertoken der einzige ausgestellte Tokentyp sind.

Wechseln Sie über die Befehlszeile zum Verzeichnis `azuread`, wenn es nicht bereits geöffnet ist.

Geben Sie den folgenden Befehl ein, um das Passport-Modul `passport-azure-ad` zu installieren.

`npm install passport-azure-ad`

Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

``
passport-azure-ad@1.0.0 node_modules/passport-azure-ad
├── xtend@4.0.0
├── xmldom@0.1.19
├── passport-http-bearer@1.0.1 (passport-strategy@1.0.0)
├── underscore@1.8.3
├── async@1.3.0
├── jsonwebtoken@5.0.2
├── xml-crypto@0.5.27 (xpath.js@1.0.6)
├── ursa@0.8.5 (bindings@1.2.1, nan@1.8.4)
├── jws@3.0.0 (jwa@1.0.1, base64url@1.0.4)
├── request@2.58.0 (caseless@0.10.0, aws-sign2@0.5.0, forever-agent@0.6.1, stringstream@0.0.4, tunnel-agent@0.4.1, oauth-sign@0.8.0, isstream@0.1.2, extend@2.0.1, json-stringify-safe@5.0.1, node-uuid@1.4.3, qs@3.1.0, combined-stream@1.0.5, mime-types@2.0.14, form-data@1.0.0-rc1, http-signature@0.11.0, bl@0.9.4, tough-cookie@2.0.0, hawk@2.3.1, har-validator@1.8.0)
└── xml2js@0.4.9 (sax@0.6.1, xmlbuilder@2.6.4)
``

## Hinzufügen der MongoDB-Module zu Ihrer Web-API

Sie verwenden MongoDB als Datenspeicher. Aus diesem Grund müssen sowohl Mongoose, ein weit verbreitetes Plug-In zum Verwalten von Verwalten von Modellen und Schemas, als auch den Datenbanktreiber für MongoDB installieren, der ebenfalls MongoDB heißt.

* `npm install mongoose`
* `npm install mongodb`

## Installieren zusätzlicher Module

Installieren Sie als nächstes weiteren erforderlichen Module.

Wechseln Sie über die Befehlszeile zum Verzeichnis `azuread`, wenn es nicht bereits geöffnet ist:

`cd azuread`

Geben Sie zur Installation der Module im Verzeichnis `node_modules` die folgenden Befehle ein:

* `npm install crypto`
* `npm install assert-plus`
* `npm install posix-getopt`
* `npm install util`
* `npm install path`
* `npm install connect`
* `npm install xml-crypto`
* `npm install xml2js`
* `npm install xmldom`
* `npm install async`
* `npm install request`
* `npm install underscore`
* `npm install grunt-contrib-jshint@0.1.1`
* `npm install grunt-contrib-nodeunit@0.1.2`
* `npm install grunt-contrib-watch@0.2.0`
* `npm install grunt@0.4.1`
* `npm install xtend@2.0.3`
* `npm install bunyan`
* `npm update`


## Erstellen der Datei „server.js“ mit Ihren Abhängigkeiten

Die `server.js`-Datei stellt den Großteil der Funktionalität für Ihren Web-API-Server bereit. Dieser Datei fügen Sie den größten Teil unseres Codes hinzu. Für Produktionszwecke sollten Sie die Funktionalität auf kleinere Dateien aufteilen und so zum Beispiel Routen und Controller getrennt behandeln. Zu Demonstrationszwecken packen wir in diesem Tutorial die gesamte Funktionalität hier aber nur in die Datei „server.js“.

Wechseln Sie über die Befehlszeile zum Verzeichnis `azuread`, wenn es nicht bereits geöffnet ist:

`cd azuread`

Erstellen Sie in einem Editor eine `server.js`-Datei. Fügen Sie die folgenden Informationen hinzu:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var fs = require('fs');
var path = require('path');
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').BearerStrategy;
```

Speichern Sie die Datei. Sie wird später noch benötigt.

## Erstellen der Datei „config.js“ für die Azure AD-Einstellungen

Diese Codedatei übergibt Ihre Konfigurationsparameter aus dem Azure AD-Portal an die `Passport.js`-Datei. Sie haben diese Konfigurationswerte erstellt, als Sie die Web-API im ersten Teil dieser exemplarischen Vorgehensweise zum Portal hinzufügten. Sie erfahren, welche Werte Sie für diese Parameter eingeben, nachdem Sie den Code kopiert haben.

Wechseln Sie über die Befehlszeile zum Verzeichnis `azuread`, wenn es nicht bereits geöffnet ist:

`cd azuread`

Erstellen Sie in einem Editor eine `config.js`-Datei. Fügen Sie die folgenden Informationen hinzu:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
audience: '<your audience URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
tenantName:'<tenant name>',
policyName:'b2c_1_<sign in policy name>',
};

```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

### Erforderliche Werte

`IdentityMetadata`: Hier sucht `passport-azure-ad` nach Ihren Konfigurationsdaten für den Identitätsanbieter. Außerdem wird hier nach den Schlüsseln zum Überprüfen der JSON-Webtoken gesucht. Wenn Sie Azure AD verwenden, sollten Sie diesen Wert nicht ändern.

`audience`: der URI (Uniform Resource Identifier) aus dem Portal, der Ihren Dienst identifiziert In unserem Beispiel wird `http://localhost/TodoListService` verwendet.

`tenantName`: der Name Ihres Mandanten (z. B. **contoso.onmicrosoft.com**)

`policyName`: die Richtlinie, anhand der die auf Ihrem Server eingehenden Token überprüft werden sollen Dies sollte dieselbe Richtlinie sein, die Sie in der Clientanwendung zum Anmelden verwenden.

> [AZURE.NOTE] Für unsere B2C-Vorschau verwenden Sie bei der Client- und Servereinrichtung dieselben Richtlinien. Wenn Sie bereits eine exemplarische Vorgehensweise abgeschlossen und diese Richtlinien erstellt haben, müssen Sie diese nicht erneut erstellen. Da Sie die exemplarische Vorgehensweise abgeschlossen haben, müssen Sie keine neuen Richtlinien einrichten, wenn Sie auf dieser Website exemplarische Vorgehensweisen für Clients befolgen.

## Hinzufügen der Konfiguration zur Datei „server.js“

Die Werte müssen für die gesamte Anwendung aus der `config.js`-Datei gelesen werden. Dazu fügen wir die `.config`-Datei als erforderliche Ressource zur Anwendung hinzu. Danach legen wir für die globalen Variablen die im Dokument `config.js` angegebenen Werte fest.

Wechseln Sie über die Befehlszeile zum Verzeichnis `azuread`, wenn es nicht bereits geöffnet ist:

`cd azuread`

Öffnen Sie die `server.js`-Datei in einem Editor. Fügen Sie die folgenden Informationen hinzu:

```Javascript
var config = require('./config');
```
Fügen Sie der Datei `server.js` einen neuen Abschnitt mit dem folgenden Code hinzu:

```Javascript
// We pass these options in to the ODICBearerStrategy.
var options = {
// The URL of the metadata document for your app. We will put the keys for token validation from the URL found in the jwks_uri tag of the in the metadata.
    identityMetadata: config.creds.identityMetadata,
    clientID: config.creds.clientID,
    tenantName: config.creds.tenantName,
    policyName: config.creds.policyName,
    validateIssuer: config.creds.validateIssuer,
    audience: config.creds.audience
};
// array to hold logged-in users and the current logged-in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## Hinzufügen der Modell- und Schemainformationen von MongoDB mithilfe von Moongoose

Die vorangegangenen Vorbereitungen werden sich jetzt bezahlt machen, da wir diese drei Dateien zu einem REST-API-Dienst kombinieren.

Wie zuvor beschrieben verwenden wir in dieser exemplarischen Vorgehensweise MongoDB zum Speichern der Aufgaben.

In der von Ihnen erstellten `config.js`-Datei haben Sie die Datenbank **tasklist** genannt. Dieser Name wurde auch am Ende der `mongoose_auth_local`-Verbindungs-URL eingefügt. Sie müssen diese Datenbank nicht vorab in MongoDB erstellen. Sofern sie noch nicht vorhanden ist, wird sie bei der ersten Ausführung der Serveranwendung automatisch erstellt.

Nachdem Sie dem Server mitteilen, welche MongoDB-Datenbank er verwenden soll, müssen Sie weiteren Code schreiben, um das Modell und Schema für die Serveraufgaben zu erstellen.

### Erweiterung des Modells

Dieses Schemamodell ist sehr einfach. Sie können es nach Bedarf erweitern.

`name`: Name des Benutzers, der der Aufgabe zugewiesen ist. Dies ist eine **Zeichenfolge**.

`task`: die Aufgabe selbst Dies ist eine **Zeichenfolge**.

`date`: Datum, an dem die Aufgabe fällig ist. Dies ist ein **datetime**-Wert.

`completed`: Gibt an, ob die Aufgabe abgeschlossen ist. Dies ist ein **Boolescher Wert**.

### Erstellen des Schemas im Code

Wechseln Sie über die Befehlszeile zum Verzeichnis `azuread`, wenn es nicht bereits geöffnet ist:

`cd azuread`

Öffnen Sie die `server.js`-Datei in einem Editor. Fügen Sie nach dem Konfigurationseintrag die folgenden Informationen hinzu:

```Javascript
// MongoDB setup
// Set up some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Dadurch wird eine Verbindung zum MongoDB-Server hergestellt und ein Schemaobjekt zurückgegeben.

### Erstellen des Modells im Code mithilfe des Schemas

Fügen Sie unter dem zuvor eingegebenen Code den folgenden Code hinzu:

```Javascript
// Here we create a schema to store our tasks and users. Pretty simple schema for now.
var TaskSchema = new Schema({
owner: String,
task: String,
completed: Boolean,
date: Date
});
// Use the schema to register a model
mongoose.model('Task', TaskSchema);
var Task = mongoose.model('Task');
```
Sie erstellen zunächst das Schema und danach ein Modellobjekt, das Sie im gesamten Code zum Speichern der Daten verwenden, wenn Sie die **Routen** definieren.

## Hinzufügen der Routen für den REST-API-Taskserver

Nachdem das Datenbankmodell erstellt ist, fügen Sie die Routen hinzu, die Sie für den REST-API-Server benötigen.

### Routen in Restify

Routen funktionieren in Restify auf genau dieselbe Weise wie im Express-Stack. Sie definieren die Routen mit den URIs, die die Client-Anwendungen Ihrer Erwartung nach aufrufen werden. In den meisten Fällen werden Routen in einer separaten Datei definiert. Zu Lernzwecken fügen Sie der `server.js`-Datei in diesem Tutorial Ihre Routen hinzu. Wenn Sie das Beispiel für Produktionszwecke verwenden möchten, empfehlen wir Ihnen, die Routen in einer eigenen Datei zu definieren.

Ein typisches Muster für eine Restify-Route ist:

```Javascript
function createObject(req, res, next) {
// do work on Object
_object.name = req.params.object; // passed value is in req.params under object
///...
return next(); // keep the server going
}
....
server.post('/service/:add/:object', createObject); // calls createObject on routes that match this.
```

Dies ist das einfachste Muster. Restify und Express bieten weitaus tiefere Funktionalität wie das Definieren von Anwendungstypen und Ausführen komplexer Weiterleitungen über mehrere Endpunkte. Für dieses Tutorial halten wir diese Routen sehr einfach.

#### Hinzufügen von Standardrouten zum Server

Jetzt fügen Sie die grundlegenden CRUD-Routen **create**, **retrieve**, **update** und **delete** hinzu.

Wechseln Sie über die Befehlszeile zum Verzeichnis `azuread`, wenn es nicht bereits geöffnet ist:

`cd azuread`

Öffnen Sie die `server.js`-Datei in einem Editor. Fügen Sie ihr nach den zuvor erfolgten Datenbankeinträgen die folgenden Informationen hinzu:

```Javascript
/**
*
* APIs for our REST task server
*/
// Create a task
function createTask(req, res, next) {
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
// Create a new task model, fill it up and save it to Mongodb
var _task = new Task();
if (!req.params.task) {
req.log.warn({
params: p
}, 'createTodo: missing task');
next(new MissingTaskError());
return;
}
_task.owner = owner;
_task.task = req.params.task;
_task.date = new Date();
_task.save(function(err) {
if (err) {
req.log.warn(err, 'createTask: unable to save');
next(err);
} else {
res.send(201, _task);
}
});
return next();
}
// Delete a task by name
function removeTask(req, res, next) {
Task.remove({
task: req.params.task,
owner: owner
}, function(err) {
if (err) {
req.log.warn(err,
'removeTask: unable to delete %s',
req.params.task);
next(err);
} else {
log.info('Deleted task:', req.params.task);
res.send(204);
next();
}
});
}
// Delete all tasks
function removeAll(req, res, next) {
Task.remove();
res.send(204);
return next();
}
// Get a specific task based on name
function getTask(req, res, next) {
log.info('getTask was called for: ', owner);
Task.find({
owner: owner
}, function(err, data) {
if (err) {
req.log.warn(err, 'get: unable to read %s', owner);
next(err);
return;
}
res.json(data);
});
return next();
}
/// Simple returns the list of TODOs that were loaded.
function listTasks(req, res, next) {
// Restify currently has a bug that doesn't allow you to set default headers
// The headers comply with CORS and allow us to mongodbServer our response to any origin
res.header("Access-Control-Allow-Origin", "*");
res.header("Access-Control-Allow-Headers", "X-Requested-With");
log.info("listTasks was called for: ", owner);
Task.find({
owner: owner
}).limit(20).sort('date').exec(function(err, data) {
if (err)
return next(err);
if (data.length > 0) {
log.info(data);
}
if (!data.length) {
log.warn(err, "There is no tasks in the database. Add one!");
}
if (!owner) {
log.warn(err, "You did not pass an owner when listing tasks.");
} else {
res.json(data);
}
});
return next();
}
```

#### Hinzufügen einer Fehlerbehandlungsroutine für Routen

Sie sollten eine Fehlerbehandlungsroutine hinzufügen, mit der ein Problem so zum Client zurück kommuniziert werden kann, dass er es versteht.

Fügen Sie unter dem zuvor eingegebenen Code den folgenden Code hinzu:

```Javascript
///--- Errors for communicating something interesting back to the client
function MissingTaskError() {
restify.RestError.call(this, {
statusCode: 409,
restCode: 'MissingTask',
message: '"task" is a required parameter',
constructorOpt: MissingTaskError
});
this.name = 'MissingTaskError';
}
util.inherits(MissingTaskError, restify.RestError);
function TaskExistsError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 409,
restCode: 'TaskExists',
message: owner + ' already exists',
constructorOpt: TaskExistsError
});
this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);
function TaskNotFoundError(owner) {
assert.string(owner, 'owner');
restify.RestError.call(this, {
statusCode: 404,
restCode: 'TaskNotFound',
message: owner + ' was not found',
constructorOpt: TaskNotFoundError
});
this.name = 'TaskNotFoundError';
}
util.inherits(TaskNotFoundError, restify.RestError);
```


## Erstellen des Servers

Sie haben jetzt Ihre Datenbank definiert und die Routen erstellt. Als letztes fügen Sie jetzt noch die Serverinstanz hinzu, die Ihre Aufrufe verwaltet.

Restify und Express bieten für einen REST-API-Server sehr viele Möglichkeiten der Anpassung, hier verwenden Sie jedoch nur die grundlegenden Einstellungen.

```Javascript
/**
* Our server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directory TODO Server",
version: "2.0.1"
});
// Ensure that we don't drop data on uploads
server.pre(restify.pre.pause());
// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());
// Handle annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());
// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());
// Allow five requests/second by IP, and burst to 10
server.use(restify.throttle({
burst: 10,
rate: 5,
ip: true,
}));
// Use the common stuff you probably want
server.use(restify.acceptParser(server.acceptable));
server.use(restify.dateParser());
server.use(restify.queryParser());
server.use(restify.gzipResponse());
server.use(restify.bodyParser({
mapParams: true
}));
```
## Hinzufügen der Routen zum Server (ohne Authentifizierung)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers is protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the passport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiment with removing API protection
/* by removing the passport.authenticate() method like this:
/*
/* server.get('/tasks', listTasks);
/*
**/
server.get('/tasks', listTasks);
server.get('/tasks', listTasks);
server.get('/tasks/:owner', getTask);
server.head('/tasks/:owner', getTask);
server.post('/tasks/:owner/:task', createTask);
server.post('/tasks', createTask);
server.del('/tasks/:owner/:task', removeTask);
server.del('/tasks/:owner', removeTask);
server.del('/tasks', removeTask);
server.del('/tasks', removeAll, function respond(req, res, next) {
res.send(204);
next();
});
// Register a default '/' handler
server.get('/', function root(req, res, next) {
var routes = [
'GET /',
'POST /tasks/:owner/:task',
'POST /tasks (for JSON body)',
'GET /tasks',
'PUT /tasks/:owner',
'GET /tasks/:owner',
'DELETE /tasks/:owner/:task'
];
res.send(200, routes);
next();
});
server.listen(serverPort, function() {
var consoleMessage = '\n Microsoft Azure Active Directory Tutorial';
consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++';
consoleMessage += '\n %s server is listening at %s';
consoleMessage += '\n Open your browser to %s/tasks\n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n';
consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n';
consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n';
});
```
## Ausführen des Servers vor dem Hinzufügen der OAuth-Unterstützung

Vor dem Hinzufügen der Authentifizierung sollten Sie den Server testen.

Am einfachsten geht das, indem Sie `curl` in einer Befehlszeile ausgeben. Davor benötigen Sie jedoch aber ein einfaches Dienstprogramm, mit dem die Ausgabe als JSON analysiert werden kann. Installieren Sie zunächst das JSON-Tool.

`$npm install -g jsontool`

Dadurch wird das Tool JSON global installiert. Testen Sie den Server nach der Installation des JSON-Tools:

Vergewissern Sie sich, dass die MongoDB-Instanz ausgeführt wird.

`$sudo mongodb`

Wechseln Sie zum `azuread`-Verzeichnis, und verwenden Sie `curl`.

`$ cd azuread` `$ node server.js`

`$ curl -isS http://127.0.0.1:8080 | json`

```Shell
HTTP/1.1 200 OK
Connection: close
Content-Type: application/json
Content-Length: 171
Date: Tue, 14 Jul 2015 05:43:38 GMT
[
"GET /",
"POST /tasks/:owner/:task",
"POST /tasks (for JSON body)",
"GET /tasks",
"PUT /tasks/:owner",
"GET /tasks/:owner",
"DELETE /tasks/:owner/:task"
]
```

Fügen Sie eine Aufgabe hinzu:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

Die Antwort sollte wie folgt lauten:

```Shell
HTTP/1.1 201 Created
Connection: close
Access-Control-Allow-Origin: *
Access-Control-Allow-Headers: X-Requested-With
Content-Type: application/x-www-form-urlencoded
Content-Length: 5
Date: Tue, 04 Feb 2014 01:02:26 GMT
Hello
```
Aufgaben für den Benutzer „Brandon“ können Sie nun beispielsweise wie folgt auflisten:

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Wenn dies funktioniert, können Sie OAuth dem REST-API-Server hinzufügen.

Sie verfügen über einen REST-API-Server mit MongoDB.

## Hinzufügen der Authentifizierung zum REST-API-Server

Nachdem der REST-API-Server nun ausgeführt wird, können Sie ihn mit Azure AD nutzen.

Wechseln Sie über die Befehlszeile zum Verzeichnis `azuread`, wenn es nicht bereits geöffnet ist:

`cd azuread`

### Verwenden von „OIDCBearerStrategy“ (in „passport-azure-ad“ enthalten)

Bisher haben Sie einen normalen REST-ToDo-Server ohne jegliche Autorisierung erstellt. Jetzt können Sie mit der Zusammensetzung der Autorisierung beginnen.

Zunächst müssen Sie darauf hinweisen, dass Sie Passport verwenden möchten. Fügen Sie diesen Hinweis direkt nach der sonstigen Serverkonfiguration ein:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts Passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]
Beim Schreiben von APIs sollten Sie die Daten immer mit einem eindeutigen Element aus dem Token verknüpfen, das der Benutzer nicht spoofen kann. Wenn der Server ToDo-Elemente speichert, speichert er sie entsprechend der **Objekt-ID** des Benutzers im Token (aufgerufen über „token.oid“), die in das Feld „ownerStore“ aufgenommen wird. Dadurch wird sichergestellt, dass niemand außer dem Benutzer auf seine eingegebenen ToDo-Elemente zugreifen kann. Es ist keine Offenlegung in den APIs von „owner“ vorgesehen, also kann ein externer Benutzer ToDo-Elemente von anderen anfordern, selbst wenn eine Authentifizierung erfolgt ist.

Verwenden Sie als Nächstes die Bearer-Strategie, die zu `passport-azure-ad` gehört. (Wir betrachten vorerst nur den Code.) Fügen Sie ihn nach dem ein, was Sie weiter oben hinzugefügt haben:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementer.
/* Here we just autoregister any user and implement a FindById().
/* You'll want to do something smarter.
**/
var findById = function(id, fn) {
for (var i = 0, len = users.length; i < len; i++) {
var user = users[i];
if (user.sub === id) {
log.info('Found user: ', user);
return fn(null, user);
}
}
return fn(null, null);
};
var oidcStrategy = new OIDCBearerStrategy(options,
function(token, done) {
log.info('verifying the user');
log.info(token, 'was the token retrieved');
findById(token.sub, function(err, user) {
if (err) {
return done(err);
}
if (!user) {
// "Auto-registration"
log.info('User was added automatically as they were new. Their sub is: ', token.sub);
users.push(token);
owner = token.sub;
return done(null, token);
}
owner = token.sub;
return done(null, user, token);
});
}
);
passport.use(oidcStrategy);
```

Passport verwendet ein ähnliches Muster für alle Strategien (einschließlich Twitter und Facebook), das beim Schreiben aller Strategien eingehalten wird. Sie übergeben es als ein `function()`-Element, das `token` und `done` als Parameter aufweist. Die Strategie kehrt wieder an Ihren Ausgangspunkt zurück, sobald die gesamte Arbeit abgeschlossen ist. Sie sollten den Benutzer und das Token dann speichern, damit Sie beides nicht mehr erfragen müssen.

> [AZURE.IMPORTANT]
Der obige Code erfasst alle Benutzer, die sich an Ihrem Server authentifizieren. Dies wird als automatische Registrierung bezeichnet. Bei Produktionsservern müssten die Benutzer zunächst einen Registrierungsprozess durchlaufen, den Sie festlegen. Dies ist normalerweise das Muster, das Sie bei Consumer-Apps finden, die es Ihnen ermöglichen, sich über Facebook zu registrieren, aber dann dazu auffordern, zusätzliche Informationen anzugeben. Wenn dies kein Befehlszeilenprogramm wäre, könnten wir die E-Mail aus dem Tokenobjekt extrahieren, das zurückgegeben wird, und dann Benutzer dazu auffordern, zusätzliche Informationen einzugeben. Da es sich um einen Testserver handelt, fügen wir sie einfach der Datenbank im Arbeitsspeicher hinzu.

### Schützen von Endpunkten

Endpunkte schützen Sie mit dem `passport.authenticate()`-Aufruf, wobei Sie darin das Protokoll angeben, das Sie verwenden möchten.

Sie können die Route im Servercode so bearbeiten, dass etwas Interessanteres geschieht:

```Javascript
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oauth-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## Führen Sie Ihre Serveranwendung erneut aus, um sicherzustellen, dass Sie abgelehnt werden.

Sie können `curl` erneut verwenden, um festzustellen, ob Ihre Endpunkte nun durch OAuth2 geschützt sind. Sie sollten dies tun, bevor einer unserer Client-SDKs an diesem Endpunkt ausgeführt wird. Die zurückgegebenen Header sollten bereits zeigen, dass Sie auf dem richtigen Weg sind.

Vergewissern Sie sich, dass die MongoDB-Instanz ausgeführt wird:

	$sudo mongodb

Wechseln Sie zum Verzeichnis, und verwenden Sie `curl`:

	$ cd azuread
	$ node server.js

Probieren Sie es mit einem einfachen POST:

`$ curl -isS -X POST http://127.0.0.1:8080/tasks/brandon/Hello`

```Shell
HTTP/1.1 401 Unauthorized
Connection: close
WWW-Authenticate: Bearer realm="Users"
Date: Tue, 14 Jul 2015 05:45:03 GMT
Transfer-Encoding: chunked
```

Ein 401-Fehler ist die gewünschte Antwort. Sie gibt an, dass die Passport-Ebene eine Umleitung an den Autorisierungsendpunkt versucht.


## Sie verfügen jetzt über einen REST-API-Dienst, der OAuth2 verwendet

Sie haben nun mit diesem Server alles getan, was noch ohne einem OAuth2-konformen Client geht. Für alle anderen Schritte müssen Sie eine weitere exemplarische Vorgehensweise durchführen.

Wenn Sie nur Informationen zum Implementieren einer REST-API mit Restify und OAuth2 benötigen, verfügen Sie jetzt über genügend Code zur Weiterentwicklung Ihres Diensts und zur Erweiterung dieses Beispiels

Als Referenz stellen wir das vollständige Beispiel (ohne Ihre Konfigurationswerte) [als ZIP-Datei](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/complete.zip) bereit. Sie können sie auch aus GitHub klonen:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```


## Nächste Schritte

Sie können nun mit den Themen für fortgeschrittenere Benutzer fortfahren, z. B.:

[Herstellen einer Verbindung mit einer Web-API mithilfe von iOS mit B2C](active-directory-b2c-devquickstarts-ios.md)

<!---HONumber=AcomDC_0302_2016-->