<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure .Net Runtime Mobile Services and Notification Hubs to send push notifications to your Windows Store JavaScript app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Erste Schritte mit Pushbenachrichtigungen in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services mit .NET-Backend Pushbenachrichtigungen an eine Windows Store-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Azure-Benachrichtigungshubs zum Schnellstartprojekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung vom .NET-Backend über die Benachrichtigungshubs. Der von Ihnen erstellte Benachrichtigungshub ist für den mobilen Dienst kostenlos, kann unabhängig vom mobilen Dienst verwaltet werden und kann von anderen Anwendungen und Diensten verwendet werden.

> [WACOM.NOTE]In diesem Thema wird erläutert, wie Pushbenachrichtigungen mit dem Windows-Benachrichtigungsdienst (Windows Notification Service, WNS) für eine Windows Store-App manuell konfiguriert werden. Mithilfe von Visual Studio 2013 Tools können Sie dieselben Pushbenachrichtigungen in einem Windows-App-Projekt automatisch konfigurieren. Weitere Informationen finden Sie in der Lernprogrammversion zu [universellen Windows-Apps][universellen Windows-Apps].

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Registrieren Ihrer App bei WNS und Konfigurieren von Mobile Services][Registrieren Ihrer App bei WNS und Konfigurieren von Mobile Services]
2.  [Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen][Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen]
3.  [Aktualisieren des Servers zum Senden von Pushbenachrichtigungen][Aktualisieren des Servers zum Senden von Pushbenachrichtigungen]
4.  [Aktivieren von Pushbenachrichtigungen für lokale Tests][Aktivieren von Pushbenachrichtigungen für lokale Tests]
5.  [Einfügen von Daten zum Empfangen von Pushbenachrichtigungen][Einfügen von Daten zum Empfangen von Pushbenachrichtigungen]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst entweder [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten][Erste Schritte mit Daten] abschließen, um Ihr Projekt mit Mobile Services zu verbinden. Wenn kein mobiler Dienst verbunden wurde, stellt der Assistent für Pushbenachrichtigungen diese Verbindung für Sie her.

## <span id="register"></span></a> Registrieren Ihrer App bei WNS und Konfigurieren von Mobile Services

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Sowohl Ihr Mobile Service als auch Ihre App sind nun konfiguriert und funktionieren mit WNS und Benachrichtigungshubs. Anschließend aktualisieren Sie Ihre Windows Store-App, um sich für Benachrichtigungen zu registrieren.

## <span id="update-app"></span></a> Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen

Bevor die App Pushbenachrichtigungen empfangen kann, müssen Sie einen Benachrichtigungskanal registrieren.

1.  Öffnen Sie die Datei "default.js", und fügen Sie den folgenden Code nach dem Code, der die **MobileServiceClient**-Instanz erstellt, ein. Dieser Code erzeugt einen Pushbenachrichtigungskanal und führt die Registrierung für Pushbenachrichtigungen durch:

        // Request a push notification channel.
        Windows.Networking.PushNotifications
            .PushNotificationChannelManager
            .createPushNotificationChannelForApplicationAsync()
            .then(function (channel) {
                // Register for notifications using the new channel
                client.push.registerNative(channel.uri);
            }, function (error) {
                var message = "Registration failed: " + error.message;
                var dialog = new Windows.UI.Popups.MessageDialog(message);
                dialog.showAsync();
            });

    Dieser Code ruft den Kanal-URI für die App vom WNS ab und registriert anschließend diesen Kanal-URI für Pushbenachrichtigungen. Falls die Registrierung fehlschlägt, wird die Fehlermeldung in einem Fehlerdialog angezeigt.

2.  Öffnen Sie in Visual Studio die Datei "Package.appxmanifest" und vergewissern Sie sich, dass **Toast Capable** in der Registerkarte **Application UI** auf **Yes** gesetzt ist.

    ![][0]

    Damit stellen Sie sicher, dass Ihre App Popupbenachrichtigungen anzeigen kann. Diese Benachrichtigungen sind im heruntergeladenen Schnellstart-Projekt bereits aktiviert.

## <span id="update-server"></span></a> Aktualisieren des Servers zum Senden von Pushbenachrichtigungen

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <span id="local-testing"></span></a>Aktivieren von Pushbenachrichtigungen für lokale Tests

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## <span id="test"></span></a> Testen von Pushbenachrichtigungen in der App

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurde allgemein erläutert, wie eine Windows Store-App für das Senden von Pushbenachrichtigungen unter Verwendung von Mobile Services und Notification Hubs eingerichtet wird. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen:

-   [Senden von Pushbenachrichtigungen an authentifizierte Benutzer][Senden von Pushbenachrichtigungen an authentifizierte Benutzer]
    Erfahren Sie, wie Pushbenachrichtigungen mithilfe von Tags von einem mobilen Dienst ausschließlich an einen authentifizierten Benutzer gesendet werden.

Weitere Informationen zu Mobile Services und Notification Hubs:

-   [Erste Schritte mit Daten][Erste Schritte mit Daten]
    Informationen zum Speichern und Abfragen von Daten mit mobilen Diensten.

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App mit verschiedenen Kontotypen mithilfe von mobilen Diensten.

-   [Was sind Notification Hubs?][Was sind Notification Hubs?]
    Erfahren Sie, wie Sie mit Benachrichtigungshubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

-   [Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz][Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]
    Erfahren Sie mehr über die Verwendung von Mobile Services mit JavaScript-Apps.



  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [universellen Windows-Apps]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-push
  [Registrieren Ihrer App bei WNS und Konfigurieren von Mobile Services]: #register
  [Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen]: #update-app
  [Aktualisieren des Servers zum Senden von Pushbenachrichtigungen]: #update-server
  [Aktivieren von Pushbenachrichtigungen für lokale Tests]: #local-testing
  [Einfügen von Daten zum Empfangen von Pushbenachrichtigungen]: #test
  [Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
  [mobile-services-notification-hubs-register-windows-store-app]: ../includes/mobile-services-notification-hubs-register-windows-store-app.md
  [0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/enable-toast.png
  [mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [mobile-services-windows-store-test-push]: ../includes/mobile-services-windows-store-test-push.md
  [Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users
  [Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-html-how-to-use-client-library
