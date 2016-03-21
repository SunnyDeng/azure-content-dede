<properties
	pageTitle="Verwenden des Apache Cordova-Plug-Ins für Azure Mobile Apps"
	description="Verwenden des Apache Cordova-Plug-Ins für Azure Mobile Apps"
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="03/07/2016"
	ms.author="adrianha"/>

# Verwenden der Apache Cordova-Clientbibliothek für Azure Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-client-library](../../includes/app-service-mobile-selector-client-library.md)]

Dieser Artikel beschreibt gängige Szenarien für die Verwendung des neuesten [Apache Cordova-Plug-Ins für Azure Mobile Apps]. Wenn Sie mit Azure Mobile Apps noch nicht vertraut sind, führen Sie zunächst den [Schnellstart von Azure Mobile Apps] durch, um ein Back-End und eine Tabelle zu erstellen und ein vorgefertigtes Apache Cordova-Projekt herunterzuladen. In dieser Anleitung konzentrieren wir uns auf das clientseitige Apache Cordova-Plug-In.

##<a name="Setup"></a>Einrichtung und Voraussetzungen

Dieses Lernprogramm setzt voraus, dass Sie ein Back-End mit einer Tabelle erstellt haben. In dieser Anleitung wird davon ausgegangen, dass die Tabelle das gleiche Schema wie die Tabellen in diesen Lernprogrammen aufweist. In dieser Anleitung wird davon ausgegangen, dass Sie Ihrem Code das Apache Cordova-Plug-In hinzugefügt haben. Falls noch nicht geschehen, können Sie das Apache Cordova-Plug-In über die Befehlszeile Ihrem Projekt hinzufügen:

```
cordova plugin add cordova-plugin-ms-azure-mobile-apps
```

Weitere Informationen zum Erstellen [Ihrer ersten Apache Cordova-App] finden Sie in der dazugehörigen Dokumentation.

[AZURE.INCLUDE [app-service-mobile-html-js-library.md](../../includes/app-service-mobile-html-js-library.md)]

##<a name="auth"></a>Gewusst wie: Authentifizieren von Benutzern

Azure App Service unterstützt die Authentifizierung und Autorisierung von Anwendungsbenutzern mit einer Vielzahl externer Identitätsanbieter: Facebook, Google, Microsoft-Konto und Twitter. Sie können Berechtigungen für Tabellen vergeben, um den Zugriff auf bestimmte Operationen auf authentifizierte Benutzer zu beschränken. Außerdem können Sie die Identität authentifizierter Benutzer verwenden, um Autorisierungsregeln in Serverskripts zu implementieren. Weitere Informationen finden Sie im Lernprogramm [Erste Schritte mit der Authentifizierung].

Bei Verwendung der Authentifizierung in einer Apache Cordova-App müssen die folgenden Cordova-Plug-Ins verfügbar sein:

* [cordova-plugin-device]
* [cordova-plugin-inappbrowser]

Insgesamt werden zwei Authentifizierungsflüsse unterstützt: ein Serverfluss und ein Clientfluss. Der Serverfluss bietet die einfachste Authentifizierungsform, da in diesem Fall die Authentifizierungs-Webschnittstelle des Anbieters verwendet wird. Der Clientfluss ermöglicht eine tiefere Integration mit gerätespezifischen Fähigkeiten wie z. B. einmalige Anmeldung, da in diesem Fall anbieterspezifische und gerätespezifische SDKs verwendet werden.

[AZURE.INCLUDE [app-service-mobile-html-js-auth-library.md](../../includes/app-service-mobile-html-js-auth-library.md)]

##<a name="register-for-push"></a>Vorgehensweise: Registrieren für Pushbenachrichtigungen

Installieren Sie [phonegap-plug-in-push] zum Verarbeiten von Pushbenachrichtigungen. Dies kann problemlos mithilfe des `cordova plugin add`-Befehls über die Befehlszeile oder den Git-Plug-in-Installer in Visual Studio hinzugefügt werden. Der folgende Code in der Apache Cordova-App registriert Ihr Gerät für Pushbenachrichtigungen:

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

[Apache Cordova-Plug-Ins für Azure Mobile Apps]: https://www.npmjs.com/package/cordova-plugin-ms-azure-mobile-apps
[Ihrer ersten Apache Cordova-App]: http://cordova.apache.org/#getstarted
[phonegap-facebook-plugin]: https://github.com/wizcorp/phonegap-facebook-plugin
[phonegap-plug-in-push]: https://www.npmjs.com/package/phonegap-plugin-push
[cordova-plugin-device]: https://www.npmjs.com/package/cordova-plugin-device
[cordova-plugin-inappbrowser]: https://www.npmjs.com/package/cordova-plugin-inappbrowser
[Query object documentation]: https://msdn.microsoft.com/de-DE/library/azure/jj613353.aspx

<!---HONumber=AcomDC_0309_2016-->