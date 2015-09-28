<properties
	pageTitle="B2C (Vorschauversion): Schützen einer Web-API mit ";node.js"; | Microsoft Azure"
	description="Erstellen einer NodeJS-Web-API, die Token von einem B2C-Mandanten akzeptiert"
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
	ms.date="09/15/2015"
	ms.author="brandwe"/>

# B2C (Vorschauversion): Schützen einer Web-API mit "node.js"

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

> [AZURE.NOTE]Dieser Artikel behandelt nicht das Implementieren der Anmeldung, Registrierung und Profilverwaltung mit Azure AD B2C. Er konzentriert sich auf das Aufrufen von Web-APIs aufrufen, nachdem der Benutzer bereits authentifiziert wurde. Wenn nicht bereits erfolgt, sollten Sie mit dem [Lernprogramm "Erste Schritte mit .NET-Web-App"](active-directory-b2c-devquickstarts-web-dotnet.md) beginnen, um Informationen zu den Grundlagen von Azure AD B2C zu erhalten.

> [AZURE.NOTE]Dieses Beispiel wurde geschrieben, um es mit unserer [iOS B2C-Beispielanwendung](active-directory-b2c-devquickstarts-ios.md) zu verbinden. Führen Sie bitte zuerst diese exemplarische Vorgehensweise durch, und machen Sie dann mit dem Beispiel weiter.

**Passport** ist eine Authentifizierungs-Middleware für Node.js. Das äußerst flexible und modular aufgebaute Passport kann unauffällig in jede Express- oder Restify-basierte Webanwendung integriert werden. Ein umfassender Satz an Strategien unterstützt die Authentifizierung mittels eines Benutzernamens und Kennworts in Facebook, Twitter und anderen Anwendungen. Wir haben eine Strategie für Microsoft Azure Active Directory entwickelt. Dieses Modul installieren Sie nun und fügen dann das Microsoft Azure Active Directory-Plug-In `passport-azure-ad` hinzu.

Dazu müssen Sie folgende Schritte ausführen:

1. Registrieren einer Anwendung in Azure AD
2. Richten Sie Ihre App für die Verwendung des azure-ad-passport-Plug-Ins von Passport ein.
3. Konfigurieren einer Clientanwendung für den Aufruf der Web-API „To Do List“

Der Code für dieses Lernprogramm wird [auf GitHub](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs) verwaltet. Um folgen zu können, können Sie [das App-Gerüst als ZIP-Datei herunterladen](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/skeleton.zip) oder das Gerüst klonen:

```git clone --branch skeleton https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```

The completed application is provided at the end of this tutorial as well.

> [AZURE.WARNING] 	For our B2C Preview you must use the same client ID/Application ID and policies for both the Web-API task server and the client that connects to it. This is true for our iOS and Android tutorials. If you have previously created an application in either of those quickstarts, please use those values instead of creating new ones below.


## 1. Get an Azure AD B2C directory

Before you can use Azure AD B2C, you must create a directory, or tenant.  A directory is a container for all your users, apps, groups, and so on.  If you don't have
one already, go [create a B2C directory](active-directory-b2c-get-started.md) before moving on.

## 2. Create an application

Now you need to create an app in your B2C directory, which gives Azure AD some information that it needs to securely communicate with your app.  Both the client app and web API will be represented by a single **Application ID** in this case, since they comprise one logical app.  To create an app,
follow [these instructions](active-directory-b2c-app-registration.md).  Be sure to

- Include a **web app/web api** in the application
- Enter `http://localhost/TodoListService` as a **Reply URL** - it is the default URL for this code sample.
- Create an **Application Secret** for your application and copy it down.  You will need it shortly.
- Copy down the **Application ID** that is assigned to your app.  You will also need it shortly.

## 3. Create your policies

In Azure AD B2C, every user experience is defined by a [**policy**](active-directory-b2c-reference-policies.md).  This app contains three 
identity experiences - sign-up, sign-in, and sign-in with Facebook.  You will need to create one policy of each type, as described in the 
[policy reference article](active-directory-b2c-reference-policies.md#how-to-create-a-sign-up-policy).  When creating your three policies, be sure to:

- Choose the **Display Name** and a few other sign-up attributes in your sign-up policy.
- Choose the **Display Name** and **Object ID** application claims in every policy.  You can choose other claims as well.
- Copy down the **Name** of each policy after you create it.  It should have the prefix `b2c_1_`.  You'll need those policy names shortly. 

Once you have your three policies successfully created, you're ready to build your app.

Note that this article does not cover how to use the policies you just created.  If you want to learn about how policies work in Azure AD B2C,
you should start with the [.NET Web App getting started tutorial](active-directory-b2c-devquickstarts-web-dotnet.md).

## 4: Download node.js for your platform
To successfully use this sample, you must have a working installation of Node.js.

Install Node.js from [http://nodejs.org](http://nodejs.org).

## 5: Install MongoDB on to your platform

To successfully use this sample, you must have a working installation of MongoDB. We will use MongoDB to make our REST API persistant across server instances.

Install MongoDB from [http://mongodb.org](http://www.mongodb.org).

> [AZURE.NOTE] This walkthrough assumes that you use the default installation and server endpoints for MongoDB, which at the time of this writing is: mongodb://localhost

## 6: Install the Restify modules in to your Web API

We will be using Resitfy to build our REST API. Restify is a minimal and flexible Node.js application framework derived from Express that has a robust set of features for building REST APIs on top of Connect.

### Install Restify

From the command-line, change directories to the azuread directory. If the **azuread** directory does not exist, create it.

`cd azuread` - or- `mkdir azuread;`

Type the following command:

`npm install restify`

This command installs Restify.

#### Did you get an error?

When using npm on some operating systems, you may receive an error of Error: EPERM, chmod '/usr/local/bin/..' and a request to try running the account as an administrator. If this occurs, use the sudo command to run npm at a higher privilege level.

#### Did you get an error regarding DTrace?

You may see something like this when installing Restify:

```Shell
clang: error: no such file or directory: 'HD/azuread/node\_modules/restify/node\_modules/dtrace-provider/libusdt' make: *** [Release/DTraceProviderBindings.node] Error 1 gyp ERR! build error gyp ERR! stack Error: `make` failed with exit code: 2 gyp ERR! stack at ChildProcess.onExit (/usr/local/lib/node\_modules/npm/node\_modules/node-gyp/lib/build.js:267:23) gyp ERR! stack at ChildProcess.EventEmitter.emit (events.js:98:17) gyp ERR! stack at Process.ChildProcess.\_handle.onexit (child\_process.js:789:12) gyp ERR! System Darwin 13.1.0 gyp ERR! command "node" "/usr/local/lib/node\_modules/npm/node\_modules/node-gyp/bin/node-gyp.js" "rebuild" gyp ERR! cwd /Volumes/Development HD/azuread/node\_modules/restify/node\_modules/dtrace-provider gyp ERR! node -v v0.10.11 gyp ERR! node-gyp -v v0.10.0 gyp ERR! not ok npm WARN optional dep failed, continuing dtrace-provider@0.2.8 ```


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


## 7: Installieren von "Passport.js" in Ihrer Web-API

[Passport](http://passportjs.org/) ist eine Authentifizierungs-Middleware für Node.js. Das äußerst flexible und modular aufgebaute Passport kann unauffällig in jede Express- oder Restify-basierte Webanwendung integriert werden. Ein umfassender Satz an Strategien unterstützt die Authentifizierung mittels eines Benutzernamens und Kennworts in Facebook, Twitter und anderen Anwendungen. Wir haben eine Strategie für Azure Active Directory entwickelt. Dieses Modul installieren Sie nun und fügen dann das Plug-in für die Azure Active Directory-Strategie hinzu.

Wechseln Sie an der Befehlszeile zum Verzeichnis „azuread“.

Geben Sie für die Installation von passport.js den folgenden Befehl ein.

`npm install passport`

Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## 8: Hinzufügen von Passport-Azure-AD zu Ihrer Web-API

Als Nächstes fügen wir die OAuth-Strategie mit "passport-azuread" hinzu, einer Reihe von Strategien, die Azure Active Directory mit Passport verbinden. Wir verwenden diese Strategie für Bearertoken in diesem Rest-API-Beispiel.

> [AZURE.NOTE]OAuth2 bietet zwar ein Framework, in dem alle bekannten Tokentypen ausgegeben werden können, allerdings findet hiervon meist nur ein eingeschränkter Tokensatz Anwendung. Für den Schutz von Endpunkten sind dies in der Regel Bearer-Tokens. Bearer-Tokens sind die in OAuth2 am häufigsten ausgegebenen Tokens, und viele Implementierungen gehen sogar davon aus, dass nur dieser Tokentyp ausgestellt wird.

Wechseln Sie an der Befehlszeile zum Verzeichnis "azuread".

Geben Sie für die Installation des Passport.js-Moduls "passport-azure-ad" den folgenden Befehl ein:

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

## 9: Hinzufügen der MongoDB-Module zu Ihrer Web-API

Als Datenspeicher verwenden wir MongoDB. Daher müssen wir sowohl das weit verbreitete Plug-in Mongoose installieren, mit dem Modelle und Schemas verwaltet werden, als auch den Datenbanktreiber für MongoDB, der ebenfalls MongoDB heißt.


* `npm install mongoose`
* `npm install mongodb`

## 10: Installieren zusätzlicher Module

Nun installieren Sie alle weiteren erforderlichen Module.


Wechseln Sie an der Befehlszeile zum Verzeichnis **azuread**, sofern noch nicht geschehen:

`cd azuread`


Geben Sie zur Installation der folgenden Module im Verzeichnis „node\_modules“ die folgenden Befehle ein:

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


## 11: Erstellen der Datei "server.js" mit Ihren Abhängigkeiten

Der größte Teil der Funktionalität unseres Web-API-Servers wird in der Datei „server.js“ bereitgestellt. Sie fügen den größten Teil des Codes dieser Datei hinzu. Für Produktionszwecke würden Sie die Funktionalität auf kleinere Dateien aufteilen und so zum Beispiel Routen und Controller getrennt behandeln. Zu Demonstrationszwecken packen wir die gesamte Funktionalität hier aber nur in die Datei „server.js“.

Wechseln Sie an der Befehlszeile zum Verzeichnis **azuread**, sofern noch nicht geschehen:

`cd azuread`

Erstellen Sie eine `server.js`-Datei in Ihrem bevorzugten Editor, und fügen Sie ihr die folgenden Informationen hinzu:

```Javascript
'use strict';
/**
* Module dependencies.
*/
var util = require('util');
var assert = require('assert-plus');
var mongoose = require('mongoose/');
var bunyan = require('bunyan');
var restify = require('restify');
var config = require('./config');
var passport = require('passport');
var OIDCBearerStrategy = require('passport-azure-ad').OIDCStrategy;
```

Speichern Sie die Datei . Wir werden schon bald wieder auf diese Datei zurückkommen.

## 12: Erstellen einer Konfigurationsdatei für die Azure AD-Einstellungen

Diese Codedatei übergibt Ihre Konfigurationsparameter aus dem Azure Active Directory-Portal an Passport.js. Sie haben diese Konfigurationswerte erstellt, als Sie die Web-API im ersten Teil dieser exemplarischen Vorgehensweise zum Portal hinzufügten. Sie erfahren nun, welche Werte Sie für diese Parameter eingeben, nachdem Sie den Code kopiert haben.


Wechseln Sie an der Befehlszeile zum Verzeichnis **azuread**, sofern noch nicht geschehen:

`cd azuread`

Erstellen Sie eine `config.js`-Datei in Ihrem bevorzugten Editor, und fügen Sie ihr die folgenden Informationen hinzu:

```Javascript
// Don't commit this file to your public repos. This config is for first-run
exports.creds = {
mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
issuer: 'https://sts.windows.net/**<your application id>**/',
audience: '<your redirect URI>',
identityMetadata: 'https://login.microsoftonline.com/common/.well-known/openid-configuration' // For using Microsoft you should never need to change this.
tenantName:'<tenant name>',
policyName:'<policy>',
};

```

### Erforderliche Werte

*IdentityMetadata*: Hier sucht "passport-azure-ad" nach den Konfigurationsdaten für IdP und nach den Schlüsseln zum Überprüfen der JWT-Token. Wahrscheinlich nehmen Sie hier keine Änderungen vor, wenn Sie Azure Active Directory verwenden.

*audience*: Die Umleitungs-URI aus dem Portal. Unser Beispiel verwendet: `http://localhost/TodoListService`

*tenantName*: der Name Ihres Mandanten (z. B. "contoso.microsoftonline.com")

*policyName*: die Richtlinie, gegen die die auf Ihrem Server eingehenden Token überprüft werden sollen. Dies sollte dieselbe Richtlinie sein, die Sie in der Clientanwendung verwenden würden.

> [AZURE.NOTE]Wir setzen unsere Schlüssel in regelmäßigen Abständen zurück. Stellen Sie sicher, dass Sie Ihre Schlüssel immer über die „openid\_keys“-URL abrufen und die Anwendung auf das Internet zugreifen kann.


## 11: Hinzufügen der Konfiguration zur Datei "server.js"

Diese Werte müssen für die gesamte Anwendung aus der eben erstellten Konfigurationsdatei gelesen werden. Dazu fügen wir die .config-Datei einfach als erforderliche Ressource zur Anwendung hinzu. Danach legen wir für die globalen Variablen die im Dokument config.js angegebenen Werte fest.

Wechseln Sie an der Befehlszeile zum Verzeichnis **azuread**, sofern noch nicht geschehen:

`cd azuread`

Öffnen Sie die Datei `server.js` in Ihrem bevorzugten Editor, und fügen Sie ihr die folgenden Informationen hinzu:

```Javascript
var config = require('./config');
```
Fügen Sie der Datei `server.js` dann einen neuen Abschnitt mit dem folgenden Code hinzu:

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
// array to hold logged in users and the current logged in user (owner)
var users = [];
var owner = null;
// Our logger
var log = bunyan.createLogger({
name: 'Microsoft Azure Active Directory Sample'
});
```

## 12: Hinzufügen der Modell- und Schemainformationen von MongoDB mithilfe von Moongoose

Die vorangegangenen Vorbereitungen werden nun, da wir diese drei Dateien zu einem REST-API-Dienst kombinieren, Früchte tragen.

In dieser exemplarischen Vorgehensweise verwenden wir MongoDB zum Speichern unserer Aufgaben, wie in ***Schritt 4*** beschrieben.

Wie Sie sich bestimmt noch von der Datei "config.js" entsinnen, die wir in Schritt 11 erstellt haben, nannten wir die Datenbank *tasklist*, denn dies war die Zeichenfolge, die wir am Ende der Verbindungs-URL "mogoose\_auth\_local" eingegeben haben. Sie müssen diese Datenbank nicht im Voraus in MongoDB erstellen, denn sie wird, wenn sie noch nicht vorhanden ist, automatisch bei der ersten Ausführung der Serveranwendung erstellt.

Nachdem der Server nun weiß, welche MongoDB-Datenbank er verwenden soll, müssen wir weiteren Code schreiben, der Modell und Schema der Serveraufgaben erstellt.

#### Beschreibung des Modells

Unsere Schemamodell ist sehr einfach: Sie können es nach Bedarf erweitern.

NAME – Der Name des Benutzers, der der Aufgabe zugeordnet ist. Eine ***Zeichenfolge***.

TASK – Die Aufgabe selbst. Eine ***Zeichenfolge***.

DATE – Das Datum, an dem die Aufgabe fällig ist. Ein ***DATUM/ZEIT***-Wert.

COMPLETED – Gibt an, ob die Aufgabe abgeschlossen ist. Ein ***BOOLESCHER*** Wert.

#### Erstellen des Schemas im Code


Wechseln Sie an der Befehlszeile zum Verzeichnis **azuread**, sofern noch nicht geschehen:

`cd azuread`

Öffnen Sie die Datei `server.js` in Ihrem bevorzugten Editor, und fügen Sie ihr nach dem Konfigurationseintrag die folgenden Informationen hinzu:

```Javascript
// MongoDB setup
// Setup some configuration
var serverPort = process.env.PORT || 8080;
var serverURI = (process.env.PORT) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;
// Connect to MongoDB
global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;
log.info('MongoDB Schema loaded');
```
Dadurch wird eine Verbindung zum MongoDB-Server hergestellt und ein Schemaobjekt zurückgegeben.

#### Erstellen des Modells im Code mithilfe des Schemas

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
Wie Sie dem Code entnehmen können, erstellen wir zunächst unser Schema und danach ein Modellobjekt, das wir im gesamten Code zum Speichern der Daten verwenden, wenn wir die ***Routen*** definieren.

## Schritt 13: Hinzufügen der Routen für den Task-REST-API-Server

Nachdem das Datenbankmodell erstellt ist, fügen wir die Routen hinzu, die wir für die REST-API-Server benötigen.

### Routen in Restify

Routen funktionieren in Restify auf genau dieselbe Weise wie im Express-Stack. Sie definieren die Routen mit den URIs, die die Client-Anwendungen Ihrer Erwartung nach aufrufen werden. In der Regel werden Routen in einer separaten Datei definiert. Für die Zwecke dieser Demonstration fügen wir sie jedoch der Datei „server.js“ hinzu. Wenn Sie das Beispiel für Produktionszwecke verwenden möchten, empfehlen wir Ihnen, die Routen in einer eigenen Datei zu definieren.

Hier ein typisches Muster für eine Restify-Route:

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


Dies ist das einfachste Muster. Restify (und Express) bieten weitaus tiefere Funktionalität wie das Definieren von Anwendungstypen und Ausführen komplexer Weiterleitungen über mehrere Endpunkte. Für unsere Zwecke halten wir diese Routen sehr einfach.

#### Hinzufügen von Standardrouten zum Server

Nachfolgend fügen Sie die CRUD-Routen (Create (Erstellen), Retrieve (Abrufen), Update (Aktualisieren) und Delete (Löschen)) hinzu.

Wechseln Sie an der Befehlszeile zum Verzeichnis **azuread**, sofern noch nicht geschehen:

`cd azuread`

Öffnen Sie die Datei `server.js` in Ihrem bevorzugten Editor, und fügen Sie ihr nach den zuvor erfolgten Datenbankeinträgen die folgenden Informationen hinzu:

```Javascript
/**
*
* APIs for our REST Task server
*/
// Create a task
function createTask(req, res, next) {
// Resitify currently has a bug which doesn't allow you to set default headers
// This headers comply with CORS and allow us to mongodbServer our response to any origin
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
// Resitify currently has a bug which doesn't allow you to set default headers
// This headers comply with CORS and allow us to mongodbServer our response to any origin
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

### Hinzufügen einer Fehlerbehandlungsroutine für Routen

Unbedingt erforderlich ist ein Fehlerbehandlungsmechanismus, mit der ein Problem so zum Client zurück kommuniziert werden kann, dass er es versteht.

Fügen Sie den folgenden Code unter dem zuvor erstellten Code hinzu:

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


## Schritt 14: Erstellen des Servers

Unsere Datenbank ist definiert, die Routen sind erstellt, nun müssen wir nur noch die Serverinstanz hinzufügen, die unsere Aufrufe verwaltet.

Restify (und Express) bieten für einen REST-API-Server sehr viele Möglichkeiten der Anpassung, jedoch verwenden wir für unsere Zwecke auch hier nur die grundlegenden Einstellungen.

```Javascript
/**
* Our Server
*/
var server = restify.createServer({
name: "Microsoft Azure Active Directroy TODO Server",
version: "2.0.1"
});
// Ensure we don't drop data on uploads
server.pre(restify.pre.pause());
// Clean up sloppy paths like //todo//////1//
server.pre(restify.pre.sanitizePath());
// Handles annoying user agents (curl)
server.pre(restify.pre.userAgentConnection());
// Set a per request bunyan logger (with requestid filled in)
server.use(restify.requestLogger());
// Allow 5 requests/second by IP, and burst to 10
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
## 15: Hinzufügen der Routen (vorläufig ohne Authentifizierung)

```Javascript
/// Now the real handlers. Here we just CRUD
/**
/*
/* Each of these handlers are protected by our OIDCBearerStrategy by invoking 'oidc-bearer'
/* in the pasport.authenticate() method. We set 'session: false' as REST is stateless and
/* we don't need to maintain session state. You can experiement removing API protection
/* by removing the passport.authenticate() method like so:
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
## 16: Ausführen des Servers, vor dem Hinzufügen der OAuth-Unterstützung

Testen Sie den Server vor dem Hinzufügen der Authentifizierung.

Am einfachsten geht das, indem Sie "curl" an einer Befehlszeile verwenden. Dazu benötigen wir aber ein einfaches Dienstprogramm, mit dem die Ausgabe als JSON analysiert werden kann. Installieren Sie dazu das Tool JSON (dieses Tool wird von allen nachfolgenden Beispielen verwendet).

`$npm install -g jsontool`

Dadurch wird das Tool JSON global installiert. Jetzt können wir uns dem Server zuwenden:

Vergewissern Sie sich zunächst, dass Ihre MonogoDB-Instanz ausgeführt wird.

`$sudo mongod`

Wechseln Sie danach zum Verzeichnis, und beginnen Sie mit dem Curling.

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

Nun können Sie wie folgt eine Aufgabe hinzufügen:

`$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello`

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
Brandons Aufgaben können wir nun beispielsweise wie folgt auflisten:

`$ curl -isS http://127.0.0.1:8080/tasks/brandon/`

Wenn all dies funktioniert, können wir OAuth zum REST-API-Server hinzufügen.

**Sie verfügen über einen REST-API-Server mit MongoDB.**

## 17: Hinzufügen der Authentifizierung zum REST-API-Server

Nachdem unsere REST-API nun ordnungsgemäß funktioniert (herzlichen Glückwunsch im übrigen), wollen wir sie mit Azure AD integrieren, um sie dort nutzen zu können.

Wechseln Sie an der Befehlszeile zum Verzeichnis **azuread**, sofern noch nicht geschehen:

`cd azuread`

### 1: Verwenden von "oidcbearerstrategy" (in "passport-azure-ad" enthalten)

Bisher haben wir einen normalen REST-TODO-Server ohne jegliche Autorisierung erstellt. Jetzt beginnen wir damit, dies zusammenzusetzen.

Zunächst müssen wir darauf hinweisen, dass wir Passport verwenden möchten. Fügen Sie diesen Hinweis direkt nach der sonstigen Serverkonfiguration ein:

```Javascript
// Let's start using Passport.js

server.use(passport.initialize()); // Starts passport
server.use(passport.session()); // Provides session support
```

> [AZURE.TIP]Beim Schreiben von APIs sollten Sie die Daten immer mit einem eindeutigen Element aus dem Token verknüpfen, das der Benutzer nicht spoofen kann. Wenn dieser Server TODO-Elemente speichert, speichert er sie entsprechend der Abonnement-ID des Benutzers im Token (wird "token.sub" genannt), die wir in das Feld "owner" aufnehmen. Dadurch wird sichergestellt, dass nur dieser Benutzer auf seine TODOs zugreifen kann und dass sonst niemand auf die eingegebenen TODOs zugreifen kann. Es ist keine Offenlegung in den APIs von "owner" vorgesehen, also kann ein externer Benutzer TODOs von anderen anfordern, selbst wenn eine Authentifizierung erfolgt ist.

Als Nächstes verwenden wir die Strategie "Open ID Connect Bearer", die zu "passport-azure-ad" gehört. Sehen wir uns jetzt nur den Code an, den ich kurz erläutern werde. Fügen Sie ihn nach dem ein, was Sie weiter oben hinzugefügt haben:

```Javascript
/**
/*
/* Calling the OIDCBearerStrategy and managing users
/*
/* Passport pattern provides the need to manage users and info tokens
/* with a FindorCreate() method that must be provided by the implementor.
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
log.info(token, 'was the token retreived');
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

Passport verwendet ein ähnliches Muster für alle Strategien (Twitter, Facebook usw.), das beim Schreiben aller Strategien eingehalten wird. Beim Betrachten der Strategie sehen Sie, dass ein function()-Element übergeben wird, das Token- und done-Elemente als Parameter aufweist. Die Strategie kehrt wieder an den Ausgangspunkt zurück, sobald die gesamte Arbeit abgeschlossen ist. Sobald dies der Fall ist, speichern wir den Benutzer und das Token, damit wir beides nicht mehr erfragen müssen.

> [AZURE.IMPORTANT]Der obige Code erfasst alle Benutzer, die sich an unserem Server authentifizieren. Dies wird als automatische Registrierung bezeichnet. Bei Produktionsservern müssten die Benutzer zunächst einen Registrierungsprozess durchlaufen, den Sie festlegen. Dies ist normalerweise das Muster, das Sie bei Consumer-Apps finden, die es Ihnen ermöglichen, sich bei Facebook zu registrieren, aber dann dazu auffordern, zusätzliche Informationen anzugeben. Wenn dies nicht ein Befehlszeilenprogramm wäre, könnten wir einfach die E-Mail aus dem Tokenobjekt extrahieren, das zurückgegeben wird, und dann dazu auffordern, zusätzliche Informationen einzugeben. Da es sich um einen Testserver handelt, fügen wir sie einfach der Datenbank im Arbeitsspeicher hinzu.

### 2\. Schützen einiger Endpunkte

Endpunkte schützen Sie mit dem Aufruf „passport.authenticate()“, wobei Sie darin das Protokoll angeben, das Sie verwenden möchten.

Wir aber wollen nun unsere Route so bearbeiten, dass etwas Interessanteres geschieht:

```Javascript
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), listTasks);
server.get('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.head('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), getTask);
server.post('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.post('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), createTask);
server.del('/tasks/:owner/:task', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks/:owner', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeTask);
server.del('/tasks', passport.authenticate('oidc-bearer', {
session: false
}), removeAll, function respond(req, res, next) {
res.send(204);
next();
});
```

## 18: Erneutes Ausführen der Serveranwendung, wobei Sie nun abgelehnt werden sollten

Führen wir `curl` erneut aus, um festzustellen, ob unsere Endpunkte nun durch OAuth2 geschützt sind. Dies machen wir auf jeden Fall, bevor einer unserer Client-SDKs an diesen Endpunkten ausgeführt wird. Die zurückgegebenen Header sollte uns bereits zeigen, dass wir auf dem richtigen Weg sind.

Vergewissern Sie sich zunächst, dass Ihre MonogoDB-Instanz ausgeführt wird.

	$sudo mongod

Wechseln Sie danach zum Verzeichnis, und beginnen Sie mit dem Curling.

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

401 ist die Antwort, nach der Sie hier suchen, denn sie gibt an, dass die Passportebene eine Umleitung an den Autorisierungsendpunkt versucht, also genau das, was Sie wollen.


## Glückwunsch! Sie haben einen REST-API-Dienst erstellt, der OAuth2 verwendet!

Sie haben nun mit diesem Server alles getan, was noch ohne einem OAuth2-konformen Client geht. Für alle weiteren Schritte müssen Sie die nächste exemplarische Vorgehensweise durchführen.

Wenn Sie lediglich wissen wollten, wie eine REST-API mit Restify und OAuth2 implementiert wird, haben Sie bereits mehr als genug Code zur Weiterentwicklung Ihres Diensts und Erweiterung dieses Beispiels.

Als Referenz stellen wir das vollständige Beispiel (ohne Ihre Konfigurationswerte) [hier als ZIP-Datei bereit](https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs/archive/complete.zip). Sie können es alternativ aus GitHub klonen:

```git clone --branch complete https://github.com/AzureADQuickStarts/B2C-WebAPI-nodejs.git```


## Nächste Schritte

Sie können nun mit den Themen für fortgeschrittenere Benutzer fortfahren. Wie wäre es zum Beispiel mit Folgendem:

[Herstellen einer Verbindung mit einer Web-API mithilfe von iOS mit B2C >>](active-directory-b2c-devquickstarts-ios.md)

<!---HONumber=Sept15_HO3-->