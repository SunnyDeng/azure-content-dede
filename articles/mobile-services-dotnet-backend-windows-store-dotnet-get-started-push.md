<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc,ricksal" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc,ricksal" />

# Erste Schritte mit Pushbenachrichtigungen in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services mit einem .NET-Back-End Pushbenachrichtigungen an eine universelle Windows-App senden. In diesem Lernprogramm aktivieren Sie Pushbenachrichtigungen mit Azure Notification Hubs für das universelle Windows-Schnellstartprojekt. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung vom .NET-Backend über die Benachrichtigungshubs. Der von Ihnen erstellte Benachrichtigungshub ist für den mobilen Dienst kostenlos, kann unabhängig vom mobilen Dienst verwaltet werden und kann von anderen Anwendungen und Diensten verwendet werden.

> [WACOM.NOTE]In diesem Thema wird erläutert, wie Pushbenachrichtigungen mit dem Windows-Benachrichtigungsdienst (Windows Notification Service, WNS) für eine Windows Store-App manuell konfiguriert werden. Mithilfe von Visual Studio 2013 Tools können Sie dieselben Pushbenachrichtigungen in einem Windows-App-Projekt automatisch konfigurieren. Weitere Informationen finden Sie in der Lernprogrammversion zu [universellen Windows-Apps][universellen Windows-Apps].

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Registrieren Ihrer App bei WNS und Konfigurieren von Mobile Services][Registrieren Ihrer App bei WNS und Konfigurieren von Mobile Services]
2.  [Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen][Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen]
3.  [Aktualisieren des Servers zum Senden von Pushbenachrichtigungen][Aktualisieren des Servers zum Senden von Pushbenachrichtigungen]
4.  [Aktivieren von Pushbenachrichtigungen für lokale Tests][Aktivieren von Pushbenachrichtigungen für lokale Tests]
5.  [Einfügen von Daten zum Empfangen von Pushbenachrichtigungen][Einfügen von Daten zum Empfangen von Pushbenachrichtigungen]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst entweder [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten][Erste Schritte mit Daten] abschließen, um Ihr Projekt mit Mobile Services zu verbinden.

> [WACOM.NOTE]Dieses Thema unterstützt Windows Phone Store 8.1-Apps. Informationen zum Hinzufügen von Pushbenachrichtigungen zu einer Windows Phone 8- oder Windows Phone Silverlight 8.1-App finden Sie in der entsprechenden Version von [Erste Schritte mit Pushbenachrichtigungen in Mobile Services][Erste Schritte mit Pushbenachrichtigungen in Mobile Services].

## <span id="register"></span></a> Registrieren Ihrer App bei WNS und Konfigurieren von Mobile Services

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Sowohl Ihr mobiler Dienst als auch Ihre Apps sind nun konfiguriert und funktionieren mit WNS und Notification Hubs. Anschließend aktualisieren Sie Ihre universelle Windows-App für die Registrierung für Benachrichtigungen.

## <span id="update-app"></span></a> Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen

Bevor die App Pushbenachrichtigungen empfangen kann, müssen Sie einen Benachrichtigungskanal registrieren.

1.  Öffnen Sie in Visual Studio die Datei "App.xaml.cs", und fügen Sie die folgenden `using`-Anweisungen hinzu:

        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

2.  Fügen Sie die folgende `InitNotificationAsync`-Methode der **App**-Klasse hinzu, um einen Pushbenachrichtigungskanal zu erstellen und sich für Pushbenachrichtigungen zu registrieren:

        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            System.Exception exception = null;
            try
            {
                await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
            }
            catch (System.Exception ex)
            {
                exception = ex;
            }
            if (exception != null)
            {
                var dialog = new MessageDialog(exception.Message, "Registering Channel URI");
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }
        }

    Dieser Code ruft den Kanal-URI für die App vom WNS ab und registriert anschließend diesen Kanal-URI für Pushbenachrichtigungen.

3.  Fügen Sie in der Datei "App.xaml.cs" am Anfang des **OnLaunched**-Ereignishandlers den folgenden Aufruf zu der neuen **InitNotificationsAsync**-Methode hinzu:

        InitNotificationsAsync();

    Dadurch wird sichergestellt, dass die Registrierung jedes Mal beim Laden der Seite angefordert wird. In Ihrer App möchten Sie diese Registrierung ggf. nur in bestimmten Abständen vornehmen, um sicherzustellen, dass die Registrierung noch aktuell ist.

4.  Öffnen Sie in Visual Studio die Datei "Package.appxmanifest" und vergewissern Sie sich, dass **Toast Capable** in der Registerkarte **Application UI** auf **Yes** gesetzt ist. Speichern Sie die Datei.

    ![][0]

    Damit stellen Sie sicher, dass Ihre App Popupbenachrichtigungen anzeigen kann.

## <span id="update-server"></span></a> Aktualisieren des Servers zum Senden von Pushbenachrichtigungen

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <span id="local-testing"></span></a>Aktivieren von Pushbenachrichtigungen für lokale Tests

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

## <span id="test"></span></a> Testen von Pushbenachrichtigungen in der App

[WACOM.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurde allgemein erläutert, wie eine Windows Store-App für das Senden von Pushbenachrichtigungen unter Verwendung von Mobile Services und Notification Hubs eingerichtet wird. Im Anschluss empfiehlt sich das nächste Lernprogramm [Senden von Pushbenachrichtigungen an authentifizierte Benutzer][Senden von Pushbenachrichtigungen an authentifizierte Benutzer]. Hier wird erläutert, wie Pushbenachrichtigungen mithilfe von Tags von einem mobilen Dienst ausschließlich an einen authentifizierten Benutzer gesendet werden.

<!--+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in.  + [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

-   [Erste Schritte mit Daten][Erste Schritte mit Daten]
    Informationen zum Speichern und Abfragen von Daten mit mobilen Diensten.

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App mit verschiedenen Kontotypen mithilfe von mobilen Diensten.

-   [Was sind Notification Hubs?][Was sind Notification Hubs?]
    Erfahren Sie, wie Sie mit Benachrichtigungshubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz][Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
    Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.



  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [universellen Windows-Apps]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [Registrieren Ihrer App bei WNS und Konfigurieren von Mobile Services]: #register
  [Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen]: #update-app
  [Aktualisieren des Servers zum Senden von Pushbenachrichtigungen]: #update-server
  [Aktivieren von Pushbenachrichtigungen für lokale Tests]: #local-testing
  [Einfügen von Daten zum Empfangen von Pushbenachrichtigungen]: #test
  [Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data
  [Erste Schritte mit Pushbenachrichtigungen in Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push
  [mobile-services-notification-hubs-register-windows-store-app]: ../includes/mobile-services-notification-hubs-register-windows-store-app.md
  [0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/enable-toast.png
  [mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [mobile-services-windows-store-test-push]: ../includes/mobile-services-windows-store-test-push.md
  [Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users
  [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-html-how-to-use-client-library
