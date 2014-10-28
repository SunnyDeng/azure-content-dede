<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="glenga"></tags>

# Erste Schritte mit Pushbenachrichtigungen in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy][mobile-services-selector-get-started-push-legacy]]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services Pushbenachrichtigungen an eine universelle Windows-App senden.
In diesem Lernprogramm aktivieren Sie Pushbenachrichtigungen mit Azure Notification Hubs für das Schnellstartprojekt. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung mithilfe von Benachrichtigungshubs. Der von Ihnen erstellte Benachrichtigungshub ist für den mobilen Dienst kostenlos, kann unabhängig vom mobilen Dienst verwaltet werden und kann von anderen Anwendungen und Diensten verwendet werden.

> [WACOM.NOTE]In diesem Thema erfahren Sie, wie Sie mithilfe von Mobile Services Pushbenachrichtigungen in einem Windows Store-Projekt manuell konfigurieren. Mithilfe von Visual Studio 2013 können Sie dieselben Pushbenachrichtigungen einem Windows Store-App-Projekt hinzufügen. Weitere Informationen finden Sie in der Lernprogrammversion zu [universellen Windows-Apps][universellen Windows-Apps].

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Registrieren Ihrer App bei WNS und Konfigurieren von Mobile Services][Registrieren Ihrer App bei WNS und Konfigurieren von Mobile Services]
2.  [Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen][Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen]
3.  [Aktualisieren von Serverskripts zum Senden von Pushbenachrichtigungen][Aktualisieren von Serverskripts zum Senden von Pushbenachrichtigungen]
4.  [Einfügen von Daten zum Empfangen von Pushbenachrichtigungen][Einfügen von Daten zum Empfangen von Pushbenachrichtigungen]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst entweder [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten][Erste Schritte mit Daten] abschließen, um Ihr Projekt mit Mobile Services zu verbinden. Wenn kein mobiler Dienst verbunden wurde, stellt der Assistent für Pushbenachrichtigungen diese Verbindung für Sie her.

> [WACOM.NOTE]Dieses Thema unterstützt Windows Phone Store 8.1-Apps. Informationen zum Hinzufügen von Pushbenachrichtigungen zu einer Windows Phone 8- oder Windows Phone Silverlight 8.1-App finden Sie in der entsprechenden Version von [Erste Schritte mit Pushbenachrichtigungen in Mobile Services][Erste Schritte mit Pushbenachrichtigungen in Mobile Services].

## <span id="register"></span></a> Registrieren Ihrer App bei WNS und Konfigurieren von Mobile Services

[WACOM.INCLUDE [mobile-services-notification-hubs-register-windows-store-app][mobile-services-notification-hubs-register-windows-store-app]]

Sowohl Ihr Mobile Service als auch Ihre App sind nun konfiguriert und funktionieren mit WNS und Benachrichtigungshubs. Anschließend aktualisieren Sie Ihre universelle Windows Store-App, um sich für Benachrichtigungen zu registrieren.

## <span id="update-app"></span></a> Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen

Bevor die App Pushbenachrichtigungen empfangen kann, müssen Sie einen Benachrichtigungskanal registrieren.

1.  Öffnen Sie in Visual Studio die Datei "App.xaml.cs", und fügen Sie die folgenden `using`-Anweisungen hinzu:

        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

2.  Fügen Sie der **App**-Klasse die folgende Methode hinzu:

        private async void InitNotificationsAsync()
        {
            // Request a push notification channel.
            var channel =
                await PushNotificationChannelManager
                    .CreatePushNotificationChannelForApplicationAsync();

            // Register for notifications using the new channel
            await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
        }

    Dieser Code ruft den Kanal-URI für die App vom WNS ab und registriert anschließend diesen Kanal-URI für Pushbenachrichtigungen.

3.  Fügen Sie in der Datei "App.xaml.cs" am Anfang des **OnLaunched**-Ereignishandlers den folgenden Aufruf zu der neuen **InitNotificationsAsync**-Methode hinzu:

        InitNotificationsAsync();

    Dadurch wird sichergestellt, dass die Registrierung jedes Mal beim Laden der Seite angefordert wird. In Ihrer App möchten Sie diese Registrierung ggf. nur in bestimmten Abständen vornehmen, um sicherzustellen, dass die Registrierung noch aktuell ist.

4.  Drücken Sie **F5**, um die App auszuführen. Ein Dialogfenster wird mit dem Registrierungsschlüssel angezeigt.

5.  (Optional) Wenn Sie nicht das vom Verwaltungsportal erzeugte Schnellstartprojekt verwenden, öffnen Sie die Datei Package.appxmanifest und vergewissern Sie sich, dass **Toast Capable** in der Registerkarte **Application UI** auf **Yes** gesetzt ist.

    ![][]

    Damit stellen Sie sicher, dass Ihre App Popupbenachrichtigungen anzeigen kann. Diese Benachrichtigungen sind im heruntergeladenen Schnellstart-Projekt bereits aktiviert.

## <span id="update-scripts"></span></a> Aktualisieren von Serverskripts zum Senden von Pushbenachrichtigungen

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs][mobile-services-javascript-update-script-notification-hubs]]

## <span id="test"></span></a> Testen von Pushbenachrichtigungen in der App

[WACOM.INCLUDE [mobile-services-windows-store-test-push][mobile-services-windows-store-test-push]]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurde allgemein erläutert, wie eine Windows Store-App für das Senden von Pushbenachrichtigungen unter Verwendung von Mobile Services und Notification Hubs eingerichtet wird. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen:

-   [Senden von Pushbenachrichtigungen an authentifizierte Benutzer][Senden von Pushbenachrichtigungen an authentifizierte Benutzer]
    Erfahren Sie, wie Pushbenachrichtigungen mithilfe von Tags von einem mobilen Dienst ausschließlich an einen authentifizierten Benutzer gesendet werden.

-   [Senden von Übertragungsbenachrichtigungen an Abonnenten][Senden von Übertragungsbenachrichtigungen an Abonnenten]
    Erfahren Sie, wie Benutzer sich registrieren und Pushbenachrichtigungen für Kategorien erhalten können, an denen sie interessiert sind.

-   [Senden vorlagenbasierter Benachrichtigungen an Abonnenten][Senden vorlagenbasierter Benachrichtigungen an Abonnenten]
    Erfahren Sie, wie Vorlagen zum Senden von Pushbenachrichtigungen von einem mobilen Dienst genutzt werden, ohne dass Sie in Ihrem Back-End auf plattformspezifische Nutzlasten zurückgreifen müssen.

Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

-   [Erste Schritte mit Daten][Erste Schritte mit Daten]
    Informationen zum Speichern und Abfragen von Daten mit mobilen Diensten.

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App mit verschiedenen Kontotypen mithilfe von mobilen Diensten.

-   [Was sind Notification Hubs?][Was sind Notification Hubs?]
    Erfahren Sie, wie Sie mit Benachrichtigungshubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz][Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
    Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.

-   [Mobile Services: Serverskriptreferenz][Mobile Services: Serverskriptreferenz]
    Erfahren Sie mehr über das Implementieren von Geschäftslogik in Ihren mobilen Dienst.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [universellen Windows-Apps]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data
  [Registrieren Ihrer App bei WNS und Konfigurieren von Mobile Services]: #register
  [Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen]: #update-app
  [Aktualisieren von Serverskripts zum Senden von Pushbenachrichtigungen]: #update-scripts
  [Einfügen von Daten zum Empfangen von Pushbenachrichtigungen]: #test
  [Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-get-started
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
  [Erste Schritte mit Pushbenachrichtigungen in Mobile Services]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push
  [mobile-services-notification-hubs-register-windows-store-app]: ../includes/mobile-services-notification-hubs-register-windows-store-app.md
  []: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png
  [mobile-services-javascript-update-script-notification-hubs]: ../includes/mobile-services-javascript-update-script-notification-hubs.md
  [mobile-services-windows-store-test-push]: ../includes/mobile-services-windows-store-test-push.md
  [Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/
  [Senden von Übertragungsbenachrichtigungen an Abonnenten]: /de-de/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
  [Senden vorlagenbasierter Benachrichtigungen an Abonnenten]: /de-de/documentation/articles/notification-hubs-windows-store-dotnet-send-localized-breaking-news/
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-users
  [Was sind Notification Hubs?]: /de-de/documentation/articles/notification-hubs-overview/
  [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
