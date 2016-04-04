<properties
	pageTitle="Verwenden des JavaScript-SDKs für Azure Mobile Apps"
	description="Vorgehensweise beim Verwenden von V for Azure Mobile Apps"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="adrianha"/>

# Verwenden der JavaScript-Clientbibliothek für Azure Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Dieser Artikelleitfaden zeigt Ihnen, wie Sie gängige Szenarien mithilfe des aktuellsten [JavaScript SDK für Azure Mobile Apps] ausführen. Wenn Sie mit Azure Mobile Apps noch nicht vertraut sind, führen Sie zunächst den [Schnellstart von Azure Mobile Apps] durch, um ein Back-End und eine Tabelle zu erstellen. In diesem Leitfaden liegt der Schwerpunkt auf der Verwendung des mobilen Back-Ends in HTML/JavaScript-Webanwendungen.

##<a name="Setup"></a>Einrichtung und Voraussetzungen

Dieses Lernprogramm setzt voraus, dass Sie ein Back-End mit einer Tabelle erstellt haben. In dieser Anleitung wird davon ausgegangen, dass die Tabelle das gleiche Schema wie die Tabellen in diesen Lernprogrammen aufweist.

Die Installation des JavaScript SDKs für Azure Mobile Apps kann durch den Befehl `npm` vorgenommen werden.

```
npm install azure-mobile-apps-client --save
```

Wenn die Installation abgeschlossen ist, befindet sich die Bibliothek in `node_modules/azure-mobile-apps-client/dist/MobileServices.Web.min.js`. Kopieren Sie diese Datei in Ihren Web-Bereich.

```
<script src="path/to/MobileServices.Web.min.js"></script>
```

Die Bibliothek kann auch als ES2015-Modul in CommonJS-Umgebungen verwendet werden, wie z. B. Browserify und Webpack sowie als eine AMD-Bibliothek. Beispiel:

```
# For ECMAScript 5.1 CommonJS
var WindowsAzure = require('azure-mobile-apps-client');
# For ES2015 modules
import * as WindowsAzure from 'azure-mobile-apps-client';
```

[AZURE.INCLUDE [app-service-mobile-html-js-library](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>Gewusst wie: Authentifizieren von Benutzern

Azure App Service unterstützt die Authentifizierung und Autorisierung von Anwendungsbenutzern mit einer Vielzahl externer Identitätsanbieter: Facebook, Google, Microsoft-Konto und Twitter. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln in Serverskripts zu implementieren. Weitere Informationen finden Sie im Lernprogramm [Erste Schritte mit der Authentifizierung].

Insgesamt werden zwei Authentifizierungsflüsse unterstützt: ein Serverfluss und ein Clientfluss. Der Serverfluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Authentifizierungs-Webschnittstelle des Anbieters verwendet wird. Der Clientfluss ermöglicht eine tiefere Integration mit gerätespezifischen Fähigkeiten wie z. B. einmalige Anmeldung, da in diesem Fall anbieterspezifische SDKs verwendet werden.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library](../../includes/app-service-mobile-html-js-auth-library.md)]

##<a name="register-for-push"></a>Vorgehensweise: Registrieren für Pushbenachrichtigungen

Installieren Sie [phonegap-plug-in-push] zum Verarbeiten von Pushbenachrichtigungen. Dies kann problemlos mithilfe des Befehls `cordova plugin add` über die Befehlszeile oder den Git-Plug-in-Installer in Visual Studio hinzugefügt werden. Der folgende Code in der Apache Cordova-App registriert Ihr Gerät für Pushbenachrichtigungen:

```
var pushOptions = {
    android: {
        senderId: '<from-gcm-console>'
    },
    ios: {
        alert: true,
        badge: true,
        sound: true
    },
    windows: {
    }
};
pushHandler = PushNotification.init(pushOptions);

pushHandler.on('registration', function (data) {
    registrationId = data.registrationId;
    // For cross-platform, you can use the device plugin to determine the device
    // Best is to use device.platform
    var name = 'gcm'; // For android - default
    if (device.platform.toLowerCase() === 'ios')
        name = 'apns';
    if (device.platform.toLowerCase().substring(0, 3) === 'win')
        name = 'wns';
    client.push.register(name, registrationId);
});

pushHandler.on('notification', function (data) {
    // data is an object and is whatever is sent by the PNS - check the format
    // for your particular PNS
});

pushHandler.on('error', function (error) {
    // Handle errors
});
```

Verwenden Sie das Notification Hubs SDK zum Senden von Pushbenachrichtigungen vom Server. Pushbenachrichtigungen sollten niemals direkt von Clients gesendet werden, da sie zum Auslösen eines Denial-of-Service-Angriffs auf Notification Hubs oder PNS verwendet werden können.

<!-- URLs. -->
[Schnellstart von Azure Mobile Apps]: app-service-mobile-cordova-get-started.md
[Erste Schritte mit der Authentifizierung]: app-service-mobile-cordova-get-started-users.md
[Add authentication to your app]: app-service-mobile-cordova-get-started-users.md

[Apache Cordova Plugin for Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[your first Apache Cordova app]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plug-in-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/de-DE/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0323_2016-->