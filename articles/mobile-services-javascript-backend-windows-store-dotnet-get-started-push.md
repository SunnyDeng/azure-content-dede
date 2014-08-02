<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Erste Schritte mit Pushbenachrichtigungen in Mobile Services
============================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-ios-get-started-push "iOS")[Android](/en-us/documentation/articles/mobile-services-javascript-backend-android-get-started-push "Android")
[.NET-Backend](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/ ".NET-Backend") | [JavaScript-Backend](/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/ "JavaScript-Backend")

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services Pushbenachrichtigungen an eine Windows Store-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Azure-Benachrichtigungshubs zum Schnellstartprojekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung mithilfe von Benachrichtigungshubs. Der von Ihnen erstellte Benachrichtigungshub ist für den mobilen Dienst kostenlos, kann unabhängig vom mobilen Dienst verwaltet werden und kann von anderen Anwendungen und Diensten verwendet werden.

> [WACOM.NOTE]In diesem Lernprogramm wird die Mobile Services-Integration mit Benachrichtigungshubs dargestellt, die sich momentan in der Vorschauphase befindet. Standardmäßig ist das Senden von Pushbenachrichtigungen unter Verwendung von Benachrichtigungshubs auf einem JavaScript-Backend nicht aktiviert. Sobald der neue Benachrichtigungshub erstellt wurde, kann der Integrationsprozess nicht mehr rückgängig gemacht werden. Pushbenachrichtigungen für iOS und Android sind derzeit nur über die standardmäßige Push-Unterstützung verfügbar, die in [diesem Thema](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/) beschrieben ist.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Registrieren Ihrer App bei WNS und Konfigurieren von Mobile Services](#register)
2.  [Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen](#update-app)
3.  [Aktualisieren von Serverskripts zum Senden von Pushbenachrichtigungen](#update-scripts)
4.  [Einfügen von Daten zum Empfangen von Pushbenachrichtigungen](#test)

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst entweder [Erste Schritte mit Mobile Services](/en-us/documentation/articles/mobile-services-windows-store-get-started) oder [Erste Schritte mit Daten](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-data) abschließen, um Ihr Projekt mit Mobile Services zu verbinden. Wenn kein mobiler Dienst verbunden wurde, stellt der Assistent für Pushbenachrichtigungen diese Verbindung für Sie her.

Registrieren Ihrer App bei WNS und Konfigurieren von Mobile Services
--------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-javascript-backend-register-windows-store-app](../includes/mobile-services-javascript-backend-register-windows-store-app.md)]

Sowohl Ihr Mobile Service als auch Ihre App sind nun konfiguriert und funktionieren mit WNS und Benachrichtigungshubs. Anschließend aktualisieren Sie Ihre Windows Store-App, um sich für Benachrichtigungen zu registrieren.

Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen
----------------------------------------------------------------

Bevor die App Pushbenachrichtigungen empfangen kann, müssen Sie einen Benachrichtigungskanal registrieren.

1.  Öffnen Sie in Visual Studio die Datei "App.xaml.cs" und fügen Sie die folgende `using`-Anweisungen hinzu:

         using Windows.Networking.PushNotifications;
         using Windows.UI.Popups;

2.  Fügen Sie der **App**-Klasse die folgende Methode hinzu:

         private async void InitNotificationsAsync()
         {
             // Pushbenachrichtigungskanal anfordern.
             var channel =
                 await PushNotificationChannelManager
                     .CreatePushNotificationChannelForApplicationAsync();

             // Für Benachrichtigungen mit neuem Kanal registrieren
             await MobileService.GetPush().RegisterNativeAsync(channel.Uri);
         }

    Dieser Code ruft den Kanal-URI für die App vom WNS ab und registriert anschließend diesen Kanal-URI für Pushbenachrichtigungen.

3.  Fügen Sie in der Datei "App.xaml.cs" am Anfang des **OnLaunched**-Ereignishandlers den folgenden Aufruf zu der neuen **InitNotificationsAsync**-Methode hinzu:

         InitNotificationsAsync();

    Dadurch wird sichergestellt, dass die Registrierung jedes Mal beim Laden der Seite angefordert wird. In Ihrer App möchten Sie diese Registrierung ggf. nur in bestimmten Abständen vornehmen, um sicherzustellen, dass die Registrierung noch aktuell ist.

4.  Drücken Sie **F5**, um die App auszuführen. Ein Dialogfenster wird mit dem Registrierungsschlüssel angezeigt.

5.  (Optional) Wenn Sie nicht das vom Verwaltungsportal erzeugte Schnellstartprojekt verwenden, öffnen Sie die Datei Package.appxmanifest und vergewissern Sie sich, dass **Toast Capable** in der Registerkarte **Application UI** auf **Yes** gesetzt ist.

      ![][2]

      Damit stellen Sie sicher, dass Ihre App Popupbenachrichtigungen anzeigen kann. Diese Benachrichtigungen sind im heruntergeladenen Schnellstart-Projekt bereits aktiviert.

Aktualisieren von Serverskripts zum Senden von Pushbenachrichtigungen
---------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

Testen von Pushbenachrichtigungen in der App
--------------------------------------------

1.  Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2.  Geben Sie in der App einen Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

      ![][13]

      Bitte beachten Sie, dass die App nach dem Einfügen eine Pushbenachrichtigung von WNS erhält.

      ![][14]

Nächste Schritte
----------------

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Arbeit mit Daten in Mobile Services gezeigt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Erste Schritte mit Benachrichtigungshubs](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
    <br/>Lernen Sie, wie Sie Benachrichtigungshubs in Ihrer Windows Store-App einsetzen können.

-   [Senden von Benachrichtigungen an Abonnenten](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
    <br/>Lernen Sie, wie Benutzer sich registrieren und Pushbenachrichtigungen für Kategorien erhalten können, an denen sie interessiert sind.

-   [Senden von Benachrichtigungen an Benutzer](/en-us/manage/services/notification-hubs/notify-users/)
    <br/>Lernen Sie, wie Sie Pushbenachrichtigungen von einem mobilen Dienst an bestimmte Benutzer auf beliebigen Geräten senden können.

-   [Senden plattformübergreifender Benachrichtigungen an Benutzer](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    <br/>Lernen Sie, wie Vorlagen zum Senden von Pushbenachrichtigungen mit einem mobilen Dienst gesendet werden, ohne dass Sie in Ihrem Backend auf plattformspezifische Nutzlasten zurückgreifen müssen.

Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-data)
    <br/>Weitere Informationen zum Speichern und Abfragen von Daten mit mobilen Diensten.

-   [Erste Schritte mit der Authentifizierung](/en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users)
    <br/>Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz](/en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library)
    <br/>Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.

<!-- Anchors. -->

<!-- Images. -->


[13]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-dotnet-get-started-push/mobile-quickstart-push2.png
[2]: ./media/mobile-services-windows-store-dotnet-get-started-push-vs2012/mobile-app-enable-toast-win8.png


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-windows-store-get-started
[Get started with data]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-users
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-push

[Get started with Notification Hubs]: /en-us/manage/services/notification-hubs/getting-started-windows-dotnet/
[What are Notification Hubs?]: /en-us/develop/net/how-to-guides/service-bus-notification-hubs/
[Send notifications to subscribers]: /en-us/manage/services/notification-hubs/breaking-news-dotnet/
[Send notifications to users]: /en-us/manage/services/notification-hubs/notify-users/
[Send cross-platform notifications to users]: /en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services .NET How-to Conceptual Reference]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
