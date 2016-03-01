<properties
	pageTitle="Hinzufügen von Pushbenachrichtigungen zu Apache Cordova-App mit Azure Mobile Apps | Azure App Service"
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Apps zum Senden von Pushbenachrichtigungen an Ihre Apache Cordova-App."
	services="app-service\mobile"
	documentationCenter="javascript"
	manager="ggailey777"
	editor=""
	authors="adrianhall"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="adrianha"/>

# Hinzufügen von Pushbenachrichtigungen zu Ihrer Apache Cordova-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]

## Übersicht

In diesem Tutorial fügen Sie Pushbenachrichtigungen zum [Apache Cordova-Schnellstartprojekt] hinzu, damit jedes Mal, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung gesendet wird. Dieses Tutorial baut auf dem [Apache Cordova-Schnellstarttutorial] auf, das Sie zuerst abschließen müssen. Wenn Sie über ein ASP.NET-Back-End verfügen und das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt das Pushbenachrichtigungs-Erweiterungspaket hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps] (in englischer Sprache).

##<a name="prerequisites"></a>Voraussetzungen

Dieses Tutorial behandelt eine Apache Cordova-Anwendung, die in Visual Studio 2015 entwickelt und auf dem Google Android Emulator ausgeführt wird. Sie können dieses Tutorial darüber hinaus auch mit anderen Emulatoren oder Geräten sowie auf anderen Plattformen durchführen.

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein [Google-Konto] mit einer verifizierten E-Mail-Adresse.
* Einen PC mit [Visual Studio Community 2015] oder höher.
* [Visual Studio-Tools für Apache Cordova].
* Ein [aktives Azure-Konto](https://azure.microsoft.com/pricing/free-trial/)
* Ein abgeschlossenes [Apache Cordova Schnellstartprojekt]. Sie können vorher andere Tutorials (z. B. zur [Authentifizierung]) abschließen, dies ist jedoch nicht erforderlich.
* Ein Android-Gerät.

Pushbenachrichtigungen werden auf Android-Emulatoren zwar unterstützt, dies ist jedoch nicht zuverlässig, daher empfehlen wir das Testen von Pushbenachrichtigungen auf Emulatoren nicht.

##<a name="create-hub"></a>Erstellen eines Notification Hubs

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##<a name="enable-gcm"></a>Aktivieren von Google Cloud Messaging

Da dieses Tutorial auf die Google-Android-Plattform abzielt, müssen Sie Google Cloud Messaging aktivieren. Wenn Sie für Apple iOS-Geräte entwickeln, müssen Sie die APNS-Unterstützung aktivieren. Im Fall von Microsoft Windows-Geräten müssen Sie die WNS- oder MPNS-Unterstützung aktivieren.

[AZURE.INCLUDE [mobile-services-enable-google-cloud-messaging](../../includes/mobile-services-enable-google-cloud-messaging.md)]

##<a name="configure-backend"></a>Konfigurieren des mobilen App-Back-Ends zum Senden von Pushanforderungen

[AZURE.INCLUDE [app-service-mobile-android-configure-push](../../includes/app-service-mobile-android-configure-push.md)]

##<a name="update-service"></a>Aktualisieren des Serverprojekts zum Senden von Pushbenachrichtigungen

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-configure-push-google](../../includes/app-service-mobile-dotnet-backend-configure-push-google.md)]

##<a name="configure-device"></a>Konfigurieren Ihres Android-Geräts für USB-Debugging

Bevor Sie Ihre Anwendung auf Ihrem Android-Gerät bereitstellen können, müssen Sie das USB-Debugging aktivieren. Führen Sie die folgenden Schritte auf Ihrem Android-Telefon aus:

1. Wechseln Sie zu **Einstellungen** > **Info zu Gerät**.
2. Tippen Sie auf die **Buildnummer**, bis der Entwicklermodus aktiviert ist.
3. Kehren Sie zu den **Einstellungen** zurück.
4. Wählen Sie **Entwickler-Optionen** aus.
5. Aktivieren Sie **USB-Debugging**.
6. Verbinden Sie Ihr Android-Telefon über ein USB-Kabel mit Ihrem Entwicklungs-PC.

In diesem Tutorial wurde zum Testen ein Google Nexus 5X unter Android-Version 6.0 (Marshmallow) verwendet. Das Verfahren ist jedoch für alle modernen Android-Versionen gleich.

##<a name="add-push-to-app"></a>Hinzufügen von Pushbenachrichtigungen zur App

Sie müssen sicherstellen, dass Ihr Apache Cordova-Projekt zur Verarbeitung von Pushbenachrichtigungen bereit ist.

### Installieren des Apache Cordova-Push-Plug-Ins

Apache Cordova-Anwendungen bieten keine native Verarbeitung von Geräte- und Netzwerkfunktionen. Diese Funktionen werden über Plug-Ins bereitgestellt, die entweder auf [npm] oder GitHub veröffentlicht werden. Das `phonegap-plugin-push`-Plug-In wird zur Verarbeitung von Netzwerkpushbenachrichtigungen verwendet. So führen Sie die Installation über die Befehlszeile aus:

    cordova plugin add phonegap-plugin-push

So installieren Sie das Plug-In in Visual Studio:

1.  Öffnen Sie die Datei `config.xml` über den Projektmappen-Explorer.
2.  Klicken Sie auf der linken Seite auf **Plug-Ins** und dann oben auf **Benutzerdefiniert**.
3.  Wählen Sie **Git** als Installationsquelle. Geben Sie `https://github.com/phonegap/phonegap-plugin-push` als Quelle ein.
4.  Klicken Sie auf den Pfeil neben der Installationsquelle, und klicken Sie dann auf **Hinzufügen**.

Das Push-Plug-In wird installiert.

### Installieren von Android Google Play-Diensten

Das PhoneGap-Push-Plug-In nutzt Google Play-Dienste für Pushbenachrichtigungen. So führen Sie die Installation durch:

1.  Öffnen Sie **Visual Studio**.
2.  Klicken Sie auf **Tools** > **Android** > **Android SDK Manager**.
3.  Aktivieren Sie im Ordner „Extras“ das Kontrollkästchen neben jedem SDK, das erforderlich, aber nicht installiert ist. Folgende Pakete sind erforderlich:
    * Android Support Library, Version 23 oder höher
    * Android Support Repository, Version 20 oder höher
    * Google Play-Dienste, Version 27 oder höher
    * Google Repository, Version 22 oder höher
4.  Klicken Sie auf **Pakete installieren**.
5.  Warten Sie, bis die Installation abgeschlossen ist.

Die aktuell erforderlichen Bibliotheken werden in der [phonegap-plugin-push installation documentation] (Dokumentation zur Pushinstallation des PhoneGap-Plug-Ins) aufgelistet.

### Registrieren des Geräts zum Push bei Start

Fügen Sie während des Rückrufs für den Anmeldeprozess oder unten in der `onDeviceReady()`-Methode einen Aufruf an `registerForPushNotifications()` hinzu:

    // Login to the service
    client.login('google')
        .then(function () {
            // Create a table reference
            todoItemTable = client.getTable('todoitem');

            // Refresh the todoItems
            refreshDisplay();

            // Wire up the UI Event Handler for the Add Item
            $('#add-item').submit(addItemHandler);
            $('#refresh').on('click', refreshDisplay);

            registerForPushNotifications();

        }, handleError);

Implementieren Sie `registerForPushNotifications()` folgendermaßen:

    /**
     * Register for Push Notifications - requires the phonegap-plugin-push be installed
     */
    var pushRegistration = null;
    function registerForPushNotifications() {
        pushRegistration = PushNotification.init({
            android: {
                senderID: 'YourProjectID'
            },
            ios: {
                alert: 'true',
                badge: 'true',
                sound: 'true'
            },
            wns: {

            }
        });

        pushRegistration.on('registration', function (data) {
            client.push.register('gcm', data.registrationId);
        });

        pushRegistration.on('notification', function (data, d2) {
            alert('Push Received: ' + data.message);
        });

        pushRegistration.on('error', handleError);
    }

Ersetzen Sie _YourProjectID_ durch die numerische Projekt-ID für Ihre App aus der [Google-Entwicklerkonsole].

## Testen der App mit dem veröffentlichten mobilen Dienst

Sie können die App testen, indem Sie ein Android-Telefon direkt über ein USB-Kabel anschließen. Anstelle von **Google Android Emulator** wählen Sie **Gerät**. Visual Studio lädt die Anwendung auf das Gerät herunter und führt sie aus. Anschließend können Sie mit der Anwendung auf dem Gerät interagieren.

Verbessern Sie Ihre Entwicklungsumgebung. Anwendungen für die Bildschirmfreigabe wie beispielsweise [Mobizen] können Sie bei der Entwicklung einer Android-Anwendung unterstützen, indem sie Ihren Android-Bildschirm in einen Webbrowser auf Ihrem PC projizieren.

##<a name="next-steps"></a>Nächste Schritte

* Informationen zu Pushbenachrichtigungen finden Sie unter [Notification Hubs].
* Falls noch nicht erfolgt, führen Sie das Tutorial mit dem [Hinzufügen von Authentifizierung] zu Ihrer Apache Cordova-App fort.

<!-- URLs -->
[Hinzufügen von Authentifizierung]: app-service-mobile-cordova-get-started-users.md
[Apache Cordova Schnellstartprojekt]: app-service-mobile-cordova-get-started.md
[Apache Cordova-Schnellstartprojekt]: app-service-mobile-cordova-get-started.md
[Apache Cordova-Schnellstarttutorial]: app-service-mobile-cordova-get-started.md
[Authentifizierung]: app-service-mobile-cordova-get-started-users.md
[Work with the .NET backend server SDK for Azure Mobile Apps]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Google-Konto]: http://go.microsoft.com/fwlink/p/?LinkId=268302
[Google-Entwicklerkonsole]: https://console.developers.google.com/home/dashboard
[phonegap-plugin-push installation documentation]: https://github.com/phonegap/phonegap-plugin-push/blob/master/docs/INSTALLATION.md
[Mobizen]: https://www.mobizen.com/
[Visual Studio Community 2015]: http://www.visualstudio.com/
[Visual Studio-Tools für Apache Cordova]: https://www.visualstudio.com/de-DE/features/cordova-vs.aspx
[Notification Hubs]: ../notification-hubs/notification-hubs-overview.md

<!---HONumber=AcomDC_0218_2016-->