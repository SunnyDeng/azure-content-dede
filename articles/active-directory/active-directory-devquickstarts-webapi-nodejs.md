<properties
	pageTitle="Erste Schritte in Azure AD NodeJS | Microsoft Azure"
	description="In diesem Thema erfahren Sie, wie eine Node.js-Web-API erstellt wird, die sich für die Authentifizierung in Azure AD integriert."
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
	ms.date="04/28/2015"
	ms.author="brandwe"/>

# Erste Schritte mit der Web-API für Node.js

[AZURE.INCLUDE [active-directory-devguide](../../includes/active-directory-devguide.md)]

In dieser exemplarischen Vorgehensweise wird Ihnen eine schnelle und einfache Möglichkeit der Einrichtung eines REST-API-Diensts gezeigt, der mittels des OAuth2-Protokolls in den API-Schutz von Azure Active Directory integriert ist. Der im Download enthaltene Beispielserver kann auf jeder Zielplattform mit Ausnahme von OSX und Linux ausgeführt werden.

Nach Abschluss dieser exemplarischen Vorgehensweise sollten Sie in der Lage sein, einen funktionsfähigen REST-API-Server mit den folgenden Funktionen erstellen zu können:

* Einen node.js-Server mit einer REST-API-Schnittstelle mit JSON und MongoDB als persistenten Speicher
* REST-APIs mit OAuth2-API-Schutz durch Bearer-Token von Azure Active Directory


Der vollständige Quellcode dieses funktionsfähigen Beispiels ist auf GitHub unter einer Apache 2.0-Lizenz veröffentlicht. Sie können diesen Code gerne klonen oder noch besser abwandeln. Ihr Feedback und Ihre Pull-Anforderungen sind uns herzlich willkommen.

## Informationen zu Node.js-Modulen

In dieser exemplarischen Vorgehensweise werden Node.js-Module verwendet. Module sind ladbare JavaScript-Pakete, die bestimmte Funktionen für die Anwendung bieten. In der Regel installieren Sie Module mithilfe des NPM-Befehlszeilenprogramms Node.js im Installationsverzeichnis von NPM, wobei jedoch einige Module, z. B. das HTTP-Modul, im Hauptpaket von Node.js enthalten sind. Installierte Module werden im Verzeichnis „node_modules“ im Stammverzeichnis Ihres Node.js-Installationsverzeichnisses gespeichert. Jedes Modul im Verzeichnis „node_modules“ enthält ein eigenes „node_modules“-Verzeichnis mit allen Modulen, von denen es abhängig ist, wobei jedes erforderliche Modul wiederum über ein eigenes „node_modules“-Verzeichnis verfügt. Diese rekursive Verzeichnisstruktur stellt die Abhängigkeitskette dar.

Die Struktur der Abhängigkeitskette erfordert zwar mehr Speicher, sie stellt aber sicher, dass alle Abhängigkeiten erfüllt sind und dass die bei der Entwicklung verwendeten Modulversionen auch in der Produktion verwendet werden. Dadurch wird das Verhalten der in der Produktion verwendeten Anwendung berechenbarer und Versionsprobleme, die sich auf die Benutzer auswirken, werden verhindert.

## Schritt 1: Registrieren eines Azure AD-Mandanten

Zur Verwendung dieses Beispiels benötigen Sie einen Azure Active Directory-Mandanten. Wenn Sie nicht wissen, was ein Mandant ist oder wie Sie einen erhalten, lesen Sie [Erhalten eines Azure AD-Mandanten](active-directory-howto-tenant.md).

## Schritt 2: Hinzufügen einer Web-API zu Ihrem Mandanten

Nachdem Sie Ihren Azure Active Directory-Mandanten erhalten haben, fügen Sie diese Beispielanwendung zu Ihrem Mandanten hinzu, damit Sie diesen zum Schutz der API verwenden können.

Damit Ihre Anwendung Benutzer authentifizieren kann, müssen Sie zunächst in Ihrem Mandanten eine neue Anwendung registrieren.

- Melden Sie sich beim Azure-Verwaltungsportal an.
- Klicken Sie in der linken Navigationsleiste auf **Active Directory**.
- Wählen Sie den Mandanten aus, in dem die Beispielanwendung registriert werden soll.
- Klicken Sie auf die Registerkarte **Anwendungen** und dann in der unteren Schublade auf „Hinzufügen“.
- Folgen Sie den Bildschirmaufforderungen, und erstellen Sie eine neue **Webanwendung und/oder Web-API**.
    - Am **Namen** der Anwendung sollten die Endbenutzer die Funktion der Anwendung ablesen können.
    -	Die **Anmelde-URL** ist die Basis-URL Ihrer Anwendung. Der Standardwert des Gerüsts lautet `https://localhost:8888`.
    - Die **App-ID-URI** ist eine eindeutige Kennung für die Anwendung. Üblicherweise wird `https://<tenant-domain>/<app-name>` verwendet, zum Beispiel: `https://contoso.onmicrosoft.com/my-first-aad-app`
- Nach Abschluss der Registrierung weist AAD Ihrer Anwendung eine eindeutige Client-ID zu. Diesen Wert benötigen Sie in den nächsten Abschnitten, weswegen Sie ihn aus der Registerkarte „Konfigurieren“ kopieren sollten.

## Schritt 3: Herunterladen der Datei „node.js“ für Ihre Plattform
Für die Verwendung dieses Beispiels benötigen Sie eine funktionsfähige Installation von Node.js.

Installieren Sie Node.js von [http://nodejs.org](http://nodejs.org).

## Schritt 4: Installieren von MongoDB auf Ihrer Plattform

Für die Verwendung dieses Beispiels benötigen Sie eine funktionsfähige Installation von MongoDB. Durch die Verwendung von MongoDB bleibt die REST-API über Serverinstanzen hinweg persistent.

Installieren Sie MongoDB von [http://mongodb.org](http://www.mongodb.org).

**Hinweis:** In dieser exemplarischen Vorgehensweise wird davon ausgegangen, dass Sie die Standardinstallation und die Standardserverendpunkte für MongoDB verwenden. Diese sind zum Zeitpunkt der Erstellung dieses Artikels: mongodb://localhost


## Schritt 5: Installieren der Restify-Module in Ihrer Web-API

Zur Erstellung der REST-API verwenden Sie Restify. Restify ist ein einfaches und flexibles, von Express abgeleitetes Node.js-Anwendungsframework mit einem umfassenden Funktionssatz für die Erstellung von REST-APIs für Connect.

### Installieren von Restify

Wechseln Sie an der Befehlszeile zum Verzeichnis „azuread“. Falls das Verzeichnis **azuread** nicht vorhanden ist, erstellen Sie es.

`cd azuread - or- mkdir azuread; cd azuread`

Geben Sie den folgenden Befehl ein:

`npm install restify`

Mit diesem Befehl wird Restify installiert.

#### BEI EINEM FEHLER

Bei Verwendung von „npm“ kann bei einigen Betriebssystemen der Fehler „Error: EPERM, chmod '/usr/local/bin/..'“ zurückgegeben werden und die Aufforderung erfolgen, das Konto als Administrator auszuführen. Verwenden Sie in diesem Fall den Befehl sudo, um npm mit einer höheren Berechtigungsstufe auszuführen.

#### BEI EINEM FEHLER ZU DTRACE

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


## Schritt 6: Installieren von Passport.js in Ihrer Web-API

[Passport](http://passportjs.org/) ist eine Authentifizierungs-Middleware für Node.js. Das äußerst flexible und modular aufgebaute Passport kann unauffällig in jede Express- oder Restify-basierte Webanwendung integriert werden. Ein umfassender Satz an Strategien unterstützt die Authentifizierung mittels eines Benutzernamens und Kennworts in Facebook, Twitter und anderen Anwendungen. Wir haben eine Strategie für Azure Active Directory entwickelt. Dieses Modul installieren Sie nun und fügen dann das Plug-in für die Azure Active Directory-Strategie hinzu.

Wechseln Sie an der Befehlszeile zum Verzeichnis „azuread“.

Geben Sie für die Installation von passport.js den folgenden Befehl ein.

`npm install passport`

Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

	passport@0.1.17 node_modules\passport
	├── pause@0.0.1
	└── pkginfo@0.2.3

## Schritt 7: Hinzufügen der Bearer-Token-Unterstützung der Passport.js zu Ihrer Web-API

Als Nächstes fügen Sie die Bearer-Strategie mittels passport-bearer-http hinzu, ein Bearer-Handler für [Passport](http://passportjs.org/). Mit node-jwt fügen Sie außerdem Unterstützung für JWT-Token-Handler hinzu.

**Hinweis:** OAuth2 bietet zwar ein Framework, in dem alle bekannten Tokentypen ausgegeben werden können, allerdings findet hiervon meist nur ein eingeschränkter Tokensatz Anwendung. Für den Schutz von Endpunkten sind dies in der Regel Bearer-Tokens. Bearer-Tokens sind die in OAuth2 am häufigsten ausgegebenen Tokens, und viele Implementierungen gehen sogar davon aus, dass nur dieser Tokentyp ausgestellt wird.

Wechseln Sie an der Befehlszeile zum Verzeichnis **azuread**.

Geben Sie für die Installation der Passport.js-Module den folgenden Befehl ein:

- `npm install passport-oauth`
- `npm install passport-http-bearer`
- `npm install node-jwt`

Die Ausgabe dieses Befehls sollte ungefähr wie folgt aussehen:

	ms-passport-wsfed-saml2@0.3.8 node_modules\passport-oauth  
	├── xtend@2.0.3
	├── xml-crypto@0.0.9
	├── xmldom@0.1.13
	└── xml2js@0.1.14 (sax@0.5.2)


## Schritt 8: Hinzufügen der MongoDB-Module zu Ihrer Web-API

Als Datenspeicher verwenden wir MongoDB. Daher müssen wir sowohl das weit verbreitete Plug-in Mongoose installieren, mit dem Modelle und Schemas verwaltet werden, als auch den Datenbanktreiber für MongoDB, der ebenfalls MongoDB heißt.


* `npm install mongoose`
* `npm install mongodb`

## Schritt 9: Installieren zusätzlicher Module

Nun installieren Sie alle weiteren erforderlichen Module.


Wechseln Sie an der Befehlszeile zum Verzeichnis **azuread**, sofern noch nicht geschehen:

`cd azuread`


Geben Sie zur Installation der folgenden Module im Verzeichnis „node_modules“ die folgenden Befehle ein:

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


## Schritt 10: Erstellen der Datei „server.js“ mit Ihren Abhängigkeiten

Der größte Teil der Funktionalität unseres Web-API-Servers wird in der Datei „server.js“ bereitgestellt. Sie fügen den größten Teil des Codes dieser Datei hinzu. Für Produktionszwecke würden Sie die Funktionalität auf kleinere Dateien aufteilen und so zum Beispiel Routen und Controller getrennt behandeln. Zu Demonstrationszwecken packen wir die gesamte Funktionalität hier aber nur in die Datei „server.js“.

Wechseln Sie an der Befehlszeile zum Verzeichnis **azuread**, sofern noch nicht geschehen:

`cd azuread`

Erstellen Sie eine `server.js`-Datei in Ihrem bevorzugten Editor, und fügen Sie ihr die folgenden Informationen hinzu:

```Javascript
	'use strict';

	/**
 	* Module dependencies.
 	*/

	var fs = require('fs');
	var path = require('path');
	var util = require('util');
	var assert = require('assert-plus');
	var bunyan = require('bunyan');
	var getopt = require('posix-getopt');
	var mongoose = require('mongoose/');
	var restify = require('restify');
```

Speichern Sie die Datei . Wir werden schon bald wieder auf diese Datei zurückkommen.

## Schritt 11: Erstellen einer Konfigurationsdatei für die Azure AD-Einstellungen

Diese Codedatei übergibt Ihre Konfigurationsparameter aus dem Azure Active Directory-Portal an Passport.js. Sie haben diese Konfigurationswerte erstellt, als Sie die Web-API im ersten Teil dieser exemplarischen Vorgehensweise zum Portal hinzufügten. Sie erfahren nun, welche Werte Sie für diese Parameter eingeben, nachdem Sie den Code kopiert haben.


Wechseln Sie an der Befehlszeile zum Verzeichnis **azuread**, sofern noch nicht geschehen:

`cd azuread`

Erstellen Sie eine `config.js`-Datei in Ihrem bevorzugten Editor, und fügen Sie ihr die folgenden Informationen hinzu:

```Javascript
// Don't commit this file to your public repos
    exports.creds = {
    mongoose_auth_local: 'mongodb://localhost/tasklist', // Your mongo auth uri goes here
    openid_configuration: 'https://login.microsoftonline.com/common/.well-known/openid-configuration', // For using Microsoft you should never need to change this.
    openid_keys: 'https://login.microsoftonline.com/common/discovery/keys', // For using Microsoft you should never need to change this. If absent will attempt to get from openid_configuration
}

```



**Hinweis:** Diese Werte müssen Sie wahrscheinlich nie ändern.

**Hinweis:** Wir setzen unsere Schlüssel in regelmäßigen Abständen zurück. Stellen Sie sicher, dass Sie Ihre Schlüssel immer über die „openid_keys“-URL abrufen und die Anwendung auf das Internet zugreifen kann.


## Schritt 12: Hinzufügen der Konfiguration zur Datei „server.js“

Diese Werte müssen für die gesamte Anwendung aus der eben erstellten Konfigurationsdatei gelesen werden. Dazu fügen wir die .config-Datei einfach als erforderliche Ressource zur Anwendung hinzu. Danach legen wir für die globalen Variablen die im Dokument config.js angegebenen Werte fest.

Wechseln Sie an der Befehlszeile zum Verzeichnis **azuread**, sofern noch nicht geschehen:

`cd azuread`

Öffnen Sie die Datei `server.js` in Ihrem bevorzugten Editor, und fügen Sie ihr die folgenden Informationen hinzu:

```Javascript
var config = require('./config');
```
Fügen Sie der Datei `server.js` dann einen neuen Abschnitt mit dem folgenden Code hinzu:

```Javascript
/**
* Setup some configuration
*/
var mongoose = require('mongoose/');
var serverPort = process.env.PORT || 8888;
var serverURI = ( process.env.PORT ) ? config.creds.mongoose_auth_mongohq : config.creds.mongoose_auth_local;

```
## Schritt 13: Erstellen der Hilfsdatei „metadata.js“ zur Unterstützung bei der Analyse von Metadaten und Tokens

Da die Datei „server.js“ nur die Anwendungslogik enthalten soll, empfiehlt es sich, die erforderlichen Hilfsmethoden in einer separaten Datei zu speichern. Diese Methoden helfen lediglich bei der Analyse der OpenID Connect-Metadaten und beziehen sich nicht auf das Kernszenario. Zunächst wollen wir diese aber zur Seite legen. Im Laufe unserer exemplarischen Vorgehensweise werden wir dieser Datei noch mehr hinzufügen.

***Hinweis:*** Wie Sie sehen, kann die Datei „metadata.js“ sowohl XML für SAML- und WS-Fed als auch JSON für OpenID Connect analysieren. Dies ist Absicht, da Sie diese Datei auch für unsere anderen Beispiele benötigen. Im Augenblick können Sie die Datei aber ignorieren.

Wechseln Sie an der Befehlszeile zum Verzeichnis **azuread**, sofern noch nicht geschehen:

`cd azuread`

Erstellen Sie eine `metadata.js`-Datei in Ihrem bevorzugten Editor, und fügen Sie ihr die folgenden Informationen hinzu:

```Javascript

'use strict';

var xml2js = require('xml2js');
var request = require('request');
var aadutils = require('./aadutils');
var async = require('async');

// Logging

var bunyan = require('bunyan');
var log = bunyan.createLogger({name: 'Microsoft OpenID Connect Passport Strategy'});

var Metadata = function (url, authtype) {


  if(!url) {
    throw new Error("Metadata: url is a required argument");
  }
  if(!authtype) {
    throw new Error('OIDCBearerStrategy requires an authentication type specified to metadata parser. Valid types are saml, wsfed, or odic"');
  }

  this.url = url;
  this.metadata = null;
  this.authtype = authtype;
  log.info(authtype, 'Metadata requested for authentication type');
};

Object.defineProperty(Metadata, 'url', {
  get: function () {
    return this.url;
  }
});

Object.defineProperty(Metadata, 'saml', {
  get: function () {
    return this.saml;
  }
});

Object.defineProperty(Metadata, 'wsfed', {
  get: function () {
    return this.wsfed;
  }
});

Object.defineProperty(Metadata, 'oidc', {
  get: function () {
    return this.oidc;
  }
});


Object.defineProperty(Metadata, 'metadata', {
  get: function () {
    return this.metadata;
  }
});

Metadata.prototype.updateSamlMetadata = function(doc, next) {
  log.info('Request to update the SAML Metadata');
  try {

    this.saml = {};

    var entity = aadutils.getElement(doc, 'EntityDescriptor');
    var idp = aadutils.getElement(entity, 'IDPSSODescriptor');
    var signOn = aadutils.getElement(idp[0], 'SingleSignOnService');
    var signOff = aadutils.getElement(idp[0], 'SingleLogoutService');
    var keyDescriptor = aadutils.getElement(idp[0], 'KeyDescriptor');
    this.saml.loginEndpoint = signOn[0].$.Location;
    this.saml.logoutEndpoint = signOff[0].$.Location;

    // copy the x509 certs from the metadata
    this.saml.certs = [];
    for (var j=0;j<keyDescriptor.length;j++) {
      this.saml.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
    }
    next(null);
  } catch (e) {
    next(new Error('Invalid SAMLP Federation Metadata ' + e.message));
  }
};

Metadata.prototype.updateOidcMetadata = function(doc, next) {
  log.info('Request to update the Open ID Connect Metadata');
  try {
    this.oidc = {};

    var issuer = doc['issuer'];
    var keyDescriptor = aadutils.getElement(idp[0], 'keys');

    // copy the x509 certs from the metadata
    this.oidc.certs = [];
    for (var j=0;j<keyDescriptor.length;j++) {
      this.oidc.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
    }
    next(null);
  } catch (e) {
    next(new Error('Invalid Open ID Connect Federation Metadata ' + e.message));
  }
};


Metadata.prototype.updateWsfedMetadata = function(doc, next) {
  log.info('Request to update the WS Federation Metadata');
  try {
    this.wsfed = {};
    var entity = aadutils.getElement(doc, 'EntityDescriptor');
    var roles = aadutils.getElement(entity, 'RoleDescriptor');
    for(var i = 0; i < roles.length; i++) {
      var role = roles[i];
      if(role['fed:SecurityTokenServiceEndpoint']) {
        var endpoint = role['fed:SecurityTokenServiceEndpoint'];
        var endPointReference = aadutils.getFirstElement(endpoint[0],'EndpointReference');
        this.wsfed.loginEndpoint = aadutils.getFirstElement(endPointReference,'Address');

        var keyDescriptor = aadutils.getElement(role, 'KeyDescriptor');
        // copy the x509 certs from the metadata
        this.wsfed.certs = [];
        for (var j=0;j<keyDescriptor.length;j++) {
          this.wsfed.certs.push(keyDescriptor[j].KeyInfo[0].X509Data[0].X509Certificate[0]);
        }
        break;
      }
    }

    return next(null);
  } catch (e) {
    next(new Error('Invalid WSFED Federation Metadata ' + e.message));
  }
};

Metadata.prototype.fetch = function(callback) {
  var self = this;
  log.info("Fetching metadata from the provided metadata URL: " + self.url);
  async.waterfall([
    // fetch the Federation metadata for the AAD tenant
    function(next){
      request(self.url, function (err, response, body) {
        if(err) {
          next(err);
        } else if(response.statusCode !== 200) {
          next(new Error("Error:" + response.statusCode +  " Cannot get AAD Federation metadata from " + self.url));
        } else {
          log.info(body, "retreived");
          next(null, body);
        }
      });
    },
    function(body, next){
      // parse the AAD Federation metadata xml

      if(self.authtype == "saml" || self.authtype == "wsfed") {
      log.info(body, "Parsing XML retreived from the endpoint");
      var parser = new xml2js.Parser({explicitRoot:true});
      // Note: xml responses from Azure AAD have a leading \ufeff which breaks xml2js parser!
      parser.parseString(body.replace("\ufeff", ""), function (err, data) {
        self.metatdata = data;
        next(err);

      });
    } else if(self.authtype == "oidc") {
      log.info(body, "Parsing JSON retreived from the endpoint");
      JSON.parse(body, function (err, data) {
        self.metatdata = data;
        next(err);
      });

    } else {

       next(new Error("Error: No Authentication type specified to metadata parser. Valid types are saml, wsfed, or odic"));
    }

    },

    function(next){
      if(self.authtype = "saml") {
      // update the SAML SSO endpoints and certs from the metadata
      self.updateSamlMetadata(self.metatdata, next);
    }},
    function(next){
      if(self.authtype = "wsfed") {
      // update the SAML SSO endpoints and certs from the metadata
      self.updateWsfedMetadata(self.metatdata, next);
    }},
    function(next){
      if(self.authtype = "oidc") {
      self.updateOidcMetadata(self.metadata, next);
    }},
  ], function (err) {
    // return err or success (err === null) to callback
    callback(err);
  });
};

exports.Metadata = Metadata;
```
Wie Sie dem Code entnehmen können, nimmt er lediglich die in `config.js` übergebene Openid-URL an und analysiert diese dann nach Informationen, die wir in der Datei `server.js` verwenden. Untersuchen Sie diesen Code ruhig ausgiebig, und passen Sie ihn gegebenenfalls an.

### Laden Sie die Datei „metadata.js“ in Ihre „server.js“-Datei.

Dem Server muss mitgeteilt werden, wo er die Methoden, die Sie gerade erstellt haben, erhält.

Wechseln Sie an der Befehlszeile zum Verzeichnis **azuread**, sofern noch nicht geschehen:

`cd azuread`

Öffnen Sie die Datei `server.js` in Ihrem bevorzugten Editor, und fügen Sie ihr die folgenden Informationen hinzu:

```Javascript
var metadata = require('./metadata);
```
Fügen Sie am Ende des Abschnitts `Configuration` den folgenden Aufruf hinzu, um das Metadatendokument in unserer `config.js`-Datei an den Parser zu senden, den wir gerade erstellt haben:

```Javascript
this.aadutils = new var Metadata = require('./metadata').Metadata;
```

## Schritt 14: Hinzufügen der Modell- und Schemainformationen von MongoDB mithilfe von Moongoose

Die vorangegangenen Vorbereitungen werden nun, da wir diese drei Dateien zu einem REST-API-Dienst kombinieren, Früchte tragen.

In dieser exemplarischen Vorgehensweise verwenden wir MongoDB zum Speichern unserer Aufgaben, wie in ***Schritt 4*** beschrieben.

Wie Sie sich bestimmt noch von der Datei `config.js` entsinnen, die wir in ***Schritt 11*** erstellt haben, nannten wir die Datenbank `tasklist`, denn dies war die Zeichenfolge, die wir am Ende der Verbindungs-URL „mogoose_auth_local“ eingegeben haben. Sie müssen diese Datenbank nicht im Voraus in MongoDB erstellen, denn sie wird, wenn sie noch nicht vorhanden ist, automatisch bei der ersten Ausführung der Serveranwendung erstellt.

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
/**
*
* Connect to MongoDB
*/

global.db = mongoose.connect(serverURI);
var Schema = mongoose.Schema;  
```
Dadurch wird eine Verbindung zum MongoDB-Server hergestellt und ein Schemaobjekt zurückgegeben.

#### Erstellen des Modells im Code mithilfe des Schemas

Fügen Sie unter dem zuvor eingegebenen Code den folgenden Code hinzu:

```Javascript
/**
/ Here we create a schema to store our tasks. Pretty simple schema for now.
*/

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

## Schritt 15: Hinzufügen der Routen für den Task-REST-API-Server

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
 * APIs
 */

function createTask(req, res, next) {

	// Resitify currently has a bug which doesn't allow you to set default headers
  	// This headers comply with CORS and allow us to mongodbServer our response to any origin

  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");

    // Create a new task model, fill it up and save it to Mongodb
  var _task = new Task();

        if (!req.params.task) {
                req.log.warn('createTask: missing task');
                next(new MissingTaskError());
                return;
        }


  _task.owner = req.params.owner;
   _task.task = req.params.task;
   _task.date = new Date();

  _task.save(function (err) {
  	if (err) {
        req.log.warn(err, 'createTask: unable to save');
        next(err);
    } else {
    res.send(201, _task);

			}
  });

  return next();

}


/**
 * Deletes a Task by name
 */
function removeTask(req, res, next) {

        Task.remove( { task:req.params.task }, function (err) {
                if (err) {
                        req.log.warn(err,
                                     'removeTask: unable to delete %s',
                                     req.params.task);
                        next(err);
                } else {
                        res.send(204);
                        next();
                }
        });
}

/**
 * Deletes all Tasks. A wipe
 */
function removeAll(req, res, next) {
        Task.remove();
        res.send(204);
        return next();
}    });
}


/**
 *
 *
 *
 */
function getTask(req, res, next) {


        Task.find(req.params.name, function (err, data) {
                if (err) {
                        req.log.warn(err, 'get: unable to read %s', req.params.name);
                        next(err);
                        return;
                }

                res.json(data);
        });

        return next();
}


/**
 * Simple returns the list of TODOs that were loaded.
 *
 */

function listTasks(req, res, next) {
  // Resitify currently has a bug which doesn't allow you to set default headers
  // This headers comply with CORS and allow us to mongodbServer our response to any origin

  res.header("Access-Control-Allow-Origin", "*");
  res.header("Access-Control-Allow-Headers", "X-Requested-With");

  console.log("server getTasks");

  Task.find().limit(20).sort('date').exec(function (err,data) {

    if (err)
      return next(err);

    if (data.length > 0) {
            console.log(data);
        }

    if (!data.length) {
            console.log('there was a problem');
            console.log(err);
            console.log("There is no tasks in the database. Did you initalize the database as stated in the README?");
        }

    else {

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


function TaskExistsError(name) {
        assert.string(name, 'name');

        restify.RestError.call(this, {
                statusCode: 409,
                restCode: 'TaskExists',
                message: name + ' already exists',
                constructorOpt: TaskExistsError
        });

        this.name = 'TaskExistsError';
}
util.inherits(TaskExistsError, restify.RestError);


function TaskNotFoundError(name) {
        assert.string(name, 'name');

        restify.RestError.call(this, {
                statusCode: 404,
                restCode: 'TaskNotFound',
                message: name + ' was not found',
                constructorOpt: TaskNotFoundError
        });

        this.name = 'TaskNotFoundError';
}

util.inherits(TaskNotFoundError, restify.RestError);
```


## Schritt 16: Erstellen des Servers

Unsere Datenbank ist definiert, die Routen sind erstellt, nun müssen wir nur noch die Serverinstanz hinzufügen, die unsere Aufrufe verwaltet.

Restify (und Express) bieten für einen REST-API-Server sehr viele Möglichkeiten der Anpassung, jedoch verwenden wir für unsere Zwecke auch hier nur die grundlegenden Einstellungen.

```Javascript
/**
 * Our Server
 */


var server = restify.createServer({
        name: "Azure Active Directroy TODO Server",
    version: "1.0.0",
    formatters: {
        'application/json': function(req, res, body){
            if(req.params.callback){
                var callbackFunctionName = req.params.callback.replace(/[^A-Za-z0-9_.]/g, '');
                return callbackFunctionName + "(" + JSON.stringify(body) + ");";
            } else {
                return JSON.stringify(body);
            }
        },
        'text/html': function(req, res, body){
            if (body instanceof Error)
                        return body.stack;

                      if (Buffer.isBuffer(body))
                        return body.toString('base64');

                return util.inspect(body);
        },
        'application/x-www-form-urlencoded': function(req, res, body){
            if (body instanceof Error) {
                    res.statusCode = body.statusCode || 500;
                    body = body.message;
            } else if (typeof (body) === 'object') {
                body = body.task || JSON.stringify(body);
            } else {
                body = body.toString();
            }

        res.setHeader('Content-Length', Buffer.byteLength(body));
        return (body);
        }
    }
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

        // This lets us push JSON to the REST API endpoint as well. Maps x: y as /name:value

        server.use(restify.bodyParser({ mapParams: false }));

        /// Now the real handlers. Here we just CRUD

        server.get('/tasks', listTasks);
        server.head('/tasks', listTasks);
        server.get('/tasks/:name', getTask);
        server.head('/tasks/:name', getTask);
        server.post('/tasks/:name/:task', createTask);
        server.del('/tasks/:name/:task', removeTask);
        server.del('/tasks/:name', removeTask);
        server.del('/tasks', removeAll, function respond(req, res, next) { res.send(204); next(); });


        // Register a default '/' handler

        server.get('/', function root(req, res, next) {
                var routes = [
                        'GET     /',
                        'POST    /tasks/:name/:task',
                        'GET     /tasks',
                        'PUT     /tasks/:name',
                        'GET     /tasks/:name',
                        'DELETE  /tasks/:name/:task'
                ];
                res.send(200, routes);
                next();
        });

  server.listen(serverPort, function() {

  var consoleMessage = '\n Azure Active Directory Tutorial'
  consoleMessage += '\n +++++++++++++++++++++++++++++++++++++++++++++++++++++'
  consoleMessage += '\n %s server is listening at %s';
  consoleMessage += '\n Open your browser to %s/tasks\n';
  consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n'
  consoleMessage += '\n !!! why not try a $curl -isS %s | json to get some ideas? \n'
  consoleMessage += '+++++++++++++++++++++++++++++++++++++++++++++++++++++ \n\n'  

  console.log(consoleMessage, server.name, server.url, server.url, server.url);

});
```

## Schritt 17: Ausführen des Servers, vor dem Hinzufügen der OAuth-Unterstützung

Bevor wir aber mit dem OAuth-Teil der exemplarischen Vorgehensweise fortfahren, sollten Sie sicherstellen, dass bis zu diesem Punkt keine Fehler vorliegen.

Am einfachsten geht das, indem Sie `curl` in einer Befehlszeile ausgeben. Dazu benötigen wir aber ein einfaches Dienstprogramm, mit dem die Ausgabe als JSON analysiert werden kann. Installieren Sie dazu das Tool [json](https://github.com/trentm/json) (dieses Tool wird von allen nachfolgenden Beispielen verwendet).

	$npm install -g jsontool

Dadurch wird das Tool JSON global installiert. Jetzt können wir uns dem Server zuwenden:

Vergewissern Sie sich zunächst, dass Ihre MonogoDB-Instanz ausgeführt wird.

	$sudo mongod

Wechseln Sie danach zum Verzeichnis, und beginnen Sie mit dem Curling.

	$ cd azuread
	$ node server.js

	$ curl -isS http://127.0.0.1:8888 | json
	HTTP/1.1 200 OK
	Connection: close
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 145
	Date: Wed, 29 Jan 2014 03:41:24 GMT

	[
  	"GET     /",
  	"POST    /tasks/:owner/:task",
  	"GET     /tasks",
  	"DELETE  /tasks",
  	"PUT     /tasks/:owner",
  	"GET     /tasks/:owner",
  	"DELETE  /tasks/:task"
	]

Nun können Sie wie folgt eine Aufgabe hinzufügen:

	$ curl -isS -X POST http://127.0.0.1:8888/tasks/brandon/Hello

Die Antwort sollte wie folgt lauten:

	HTTP/1.1 201 Created
	Connection: close
	Access-Control-Allow-Origin: *
	Access-Control-Allow-Headers: X-Requested-With
	Content-Type: application/x-www-form-urlencoded
	Content-Length: 5
	Date: Tue, 04 Feb 2014 01:02:26 GMT

	Hello

Brandons Aufgaben können wir nun beispielsweise wie folgt auflisten:

	$ curl -isS http://127.0.0.1:8888/tasks/brandon/

Wenn all dies funktioniert, können wir OAuth zum REST-API-Server hinzufügen.

## Schritt 18: Hinzufügen des Passport.js-Codes zum REST-API-Server

Nachdem unsere REST-API nun ordnungsgemäß funktioniert (herzlichen Glückwunsch im übrigen), wollen wir sie mit Azure AD integrieren, um sie dort nutzen zu können.

Wechseln Sie an der Befehlszeile zum Verzeichnis **azuread**, sofern noch nicht geschehen:

`cd azuread`

### Schritt 1: Hinzufügen der Passport-Module

Öffnen Sie die Datei `server.js` in Ihrem bevorzugten Editor, und fügen Sie ihr nach der Definition der zu ladenden Module die folgenden Informationen hinzu: Diese Stelle befindet sich noch ziemlich am Anfang der Datei direkt nach dem Import von `var aadutils = require('./aadutils');`.

```Javascript
/*
*
* Load our old friend Passport for OAuth2 flows
*/

var passport = require('passport')
  , OAuth2Strategy = require('passport-oauth').OAuth2Strategy;
```

### 2. Informieren des Servers, dass Authentifizierung verwendet wird

Öffnen Sie die Datei `server.js` in Ihrem bevorzugten Editor, und fügen Sie ihr die folgenden Informationen nach der Methode **server.get()** hinzu, mit der Sie Ihre Routen definiert haben, jedoch noch vor der Methode **server.listen()**.


Restify muss nun mitgeteilt werden, dass es mit der Verwendung seines `authorizationParser()` beginnen und den Inhalt des Autorisierungsheaders untersuchen soll.

```Javascript
        server.use(restify.authorizationParser());


```


### 3. Hinzufügen des Passport-OAuth2-Moduls zum Code

Hier verwenden wir nun die OAuth2-Parameter, die wir zuvor der Datei „config.js“ hinzugefügt haben. Wenn die `aadutils.js` Datei ihre Aufgabe erledigt und das Verbundmetadaten-Dokument analysiert hat, sollten diese Werte nun alle ausgefüllt sein, selbst wenn sie in der Datei „config.js“ leer sind.

```Javascript
// Now our own handlers for authentication/authorization
// Here we only use Oauth2 from Passport.js

passport.use('provider', new OAuth2Strategy({
    authorizationURL: authEndpoint,
    tokenURL: tokenEndpoint,
    clientID: clientID,
    clientSecret: clientSecret,
    callbackURL: callbackURL
  },
  function(accessToken, refreshToken, profile, done) {
    User.findOrCreate({ UserId: profile.id }, function(err, user) {
      done(err, user);
    });
  }
));

// Let's start using Passport.js

server.use(passport.initialize());

```
### Schritt 4: Hinzufügen von Routen für die OAuth-Authentifizierung

```Javascript
// Redirect the user to the OAuth 2.0 provider for authentication.  When
// complete, the provider will redirect the user back to the application at
//     /auth/provider/callback

server.get('/auth/provider', passport.authenticate('provider'));

// The OAuth 2.0 provider has redirected the user back to the application.
// Finish the authentication process by attempting to obtain an access
// token.  If authorization was granted, the user will be logged in.
// Otherwise, authentication has failed.

server.get('/auth/provider/callback',
  passport.authenticate('provider', { successRedirect: '/',
                                      failureRedirect: '/login' }));
```

### Schritt 5: Hinzufügen der Hilfsmethode IsAuthenticated() zu den Routen

```Javascript
// Simple route middleware to ensure user is authenticated.
//   Use this route middleware on any resource that needs to be protected.  If
//   the request is authenticated (typically via a persistent login session),
//   the request will proceed.  Otherwise, the user will be redirected to the
//   login page.

var ensureAuthenticated = function(req, res, next) {
  if (req.isAuthenticated()) {
    return next();
  }
  res.redirect('/login');
};

```

### Schritt 6: Hinzufügen eines Cachemechanismus für Cookies

```Javascript
// Passport session setup.
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
```
### Schritt 7: Schützen einiger Endpunkte

Endpunkte schützen Sie mit dem Aufruf „passport.authenticate()“, wobei Sie darin das Protokoll angeben, das Sie verwenden möchten.

Wir aber wollen nun unsere Route so bearbeiten, dass etwas Interessanteres geschieht:

```Javascript
server.get('/tasks', passport.authenticate('provider', { session: false }), listTasks);
```


## Schritt 19: Erneutes Ausführen der Serveranwendung, wobei Sie nun abgelehnt werden sollten

Führen wir `curl` erneut aus, um festzustellen, ob unsere Endpunkte nun durch OAuth2 geschützt sind. Dies machen wir auf jeden Fall, bevor einer unserer Client-SDKs an diesen Endpunkten ausgeführt wird. Die zurückgegebenen Header sollte uns bereits zeigen, dass wir auf dem richtigen Weg sind.

Vergewissern Sie sich zunächst, dass Ihre MonogoDB-Instanz ausgeführt wird.

	$sudo mongod

Wechseln Sie danach zum Verzeichnis, und beginnen Sie mit dem Curling.

	$ cd azuread
	$ node server.js

Probieren Sie es mit einem einfachen GET:

	$ curl -isS http://127.0.0.1:8888/tasks/
	HTTP/1.1 302 Moved Temporarily
	Connection: close
	Location: https://login.windows.net/468a75f4-f9a7-4dc4-a527-4f4522734790/oauth2/authorize?response_type=code&redirect_uri=&client_id=123
	Content-Length: 0
	Date: Tue, 04 Feb 2014 02:15:14 GMT


302 ist die Antwort, nach der Sie hier suchen, denn sie gibt an, dass die Passportebene eine Umleitung an den Autorisierungsendpunkt versucht, also genau das, was Sie wollen.

## Glückwunsch! Sie haben einen REST-API-Dienst erstellt, der OAuth2 verwendet!

Sie haben nun mit diesem Server alles getan, was noch ohne einem OAuth2-konformen Client geht. Für alle weiteren Schritte müssen Sie die nächste exemplarische Vorgehensweise durchführen.

Wenn Sie lediglich wissen wollten, wie eine REST-API mit Restify und OAuth2 implementiert wird, haben Sie bereits mehr als genug Code zur Weiterentwicklung Ihres Diensts und Erweiterung dieses Beispiels.

Wenn Sie Ihren Weg auf den Spuren von ADAL weiterverfolgen möchten, bieten wir Ihnen hier weitere unterstützte ADAL-Clients an, die wir Ihnen sehr für Ihre Arbeit empfehlen können:

Klonen Sie einfach hinunter zu Ihrem Entwicklungscomputer und führen Sie die Konfiguration aus, wie in der exemplarischen Vorgehensweise beschrieben.

[ADAL für iOS](https://github.com/MSOpenTech/azure-activedirectory-library-for-ios)

[ADAL für Android](https://github.com/MSOpenTech/azure-activedirectory-library-for-android)

[ADAL für .Net](http://msdn.microsoft.com/library/windowsazure/jj573266.aspx)
 

<!---HONumber=58_postMigration-->