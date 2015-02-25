<properties pageTitle="Erste Schritte mit Pushbenachrichtigungen (Windows Store) | Mobile Dev Center" description="Erfahren Sie, wie Sie Azure .NET Runtime Mobile Services und Notification Hubs verwenden, um Pushbenachrichtigungen an Ihre Windows Store-JavaScript-App zu senden." services="mobile-services, notification-hubs" documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/27/2014" ms.author="wesmc"/>


# Hinzufügen von Pushbenachrichtigungen zu einer Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services mit .NET-Back-End Pushbenachrichtigungen an eine Windows Store-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Azure-Benachrichtigungshubs zum Schnellstartprojekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung vom .NET-Back-End über die Benachrichtigungshubs. Der von Ihnen erstellte Benachrichtigungshub ist für den mobilen Dienst kostenlos, kann unabhängig vom mobilen Dienst verwaltet werden und kann von anderen Anwendungen und Diensten verwendet werden.

>[AZURE.NOTE]In diesem Thema wird erläutert, wie Pushbenachrichtigungen mit dem Windows-Benachrichtigungsdienst (Windows Notification Service, WNS) für eine Windows Store-App manuell konfiguriert werden. Mithilfe von Visual Studio 2013 Tools können Sie dieselben Pushbenachrichtigungen in einem Windows-App-Projekt automatisch konfigurieren. Weitere Informationen finden Sie in der Lernprogrammversion zu [universellen Windows-Apps](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-push).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Registrieren Ihrer App bei WNS und Konfigurieren von Mobile Services](#register)
2. [Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen](#update-app)
3. [Aktualisieren des Servers zum Senden von Pushbenachrichtigungen](#update-server)
4. [Aktivieren von Pushbenachrichtigungen für lokale Tests](#local-testing)
5. [Einfügen von Daten zum Empfangen von Pushbenachrichtigungen](#test)

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst entweder [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten] abschließen, um Ihr Projekt mit Mobile Services zu verbinden. Wenn kein mobiler Dienst verbunden wurde, stellt der Assistent für Pushbenachrichtigungen diese Verbindung für Sie her. 

##<a id="register"></a>Registrieren Ihrer App bei WNS und Konfigurieren von Mobile Services

[AZURE.INCLUDE [mobile-services-notification-hubs-register-windows-store-app](../includes/mobile-services-notification-hubs-register-windows-store-app.md)]

Sowohl der mobile Dienst als auch die Apps sind nun für die Verwendung von WNS und Notification Hubs konfiguriert. Anschließend aktualisieren Sie Ihre Windows Store-App, um sich für Benachrichtigungen zu registrieren.

##<a id="update-app"></a> Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen

Bevor die App Pushbenachrichtigungen empfangen kann, müssen Sie einen Benachrichtigungskanal registrieren.

1. Öffnen Sie die Datei "default.js", und fügen Sie den folgenden Code nach dem Code, der die **MobileServiceClient**-Instanz erstellt, ein: Dieser Code erzeugt einen Pushbenachrichtigungskanal und führt die Registrierung für Pushbenachrichtigungen durch:

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

2. Öffnen Sie in Visual Studio die Datei Package.appxmanifest, und vergewissern Sie sich, dass **Toastfähig** in der Registerkarte **Anwendungsbenutzeroberfläche** auf **Ja** gesetzt ist.

   	![][1]

   	Damit stellen Sie sicher, dass Ihre App Popupbenachrichtigungen anzeigen kann. Diese Benachrichtigungen sind im heruntergeladenen Schnellstart-Projekt bereits aktiviert.

##<a id="update-server"></a> Aktualisieren des Servers zum Senden von Pushbenachrichtigungen


[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Aktivieren von Pushbenachrichtigungen für lokale Tests

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push](../includes/mobile-services-dotnet-backend-configure-local-push.md)]

##<a id="test"></a> Testen von Pushbenachrichtigungen in der App

[AZURE.INCLUDE [mobile-services-windows-store-test-push](../includes/mobile-services-windows-store-test-push.md)]

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Verwendung von Mobile Services und Notification Hubs zum Senden von Pushbenachrichtigungen gezeigt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen:

+ [Senden von Pushbenachrichtigungen an authentifizierte Benutzer]
	<br/>Erfahren Sie, wie Pushbenachrichtigungen mithilfe von Tags von einem mobilen Dienst ausschließlich an einen authentifizierten Benutzer gesendet werden.

Weitere Informationen zu Mobile Services und Notification Hubs:

* [Erste Schritte mit Daten]
  <br/>Erfahren Sie mehr über das Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie mehr über die Authentifizierung von Benutzern Ihrer App mit verschiedenen Kontotypen über Mobile Services.

* [Was sind Notification Hubs?]
  <br/>Erfahren Sie, wie Sie mit Benachrichtigungshubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

* [Debuggen von Notification Hubs-Anwendungen](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Holen Sie sich Anleitungen für die Problembehandlung und das Debuggen von Notification Hubs-Lösungen. 

* [Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]
  <br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit JavaScript-Apps.

<!-- Anchors. -->

<!-- Images. -->

[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/enable-toast.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users

[Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/

[Was sind Notification Hubs?]: /de-de/documentation/articles/notification-hubs-overview/

[Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-html-how-to-use-client-library


<!--HONumber=42-->
