<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# Erste Schritte mit Pushbenachrichtigungen in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy](../includes/mobile-services-selector-get-started-push-legacy.md)]

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Pushbenachrichtigungen an eine Windows Phone Silverlight-App senden können. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Azure-Benachrichtigungshubs zum Schnellstartprojekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung mithilfe von Benachrichtigungshubs. Der von Ihnen erstellte Benachrichtigungshub ist für den mobilen Dienst kostenlos, kann unabhängig vom mobilen Dienst verwaltet werden und kann von anderen Anwendungen und Diensten verwendet werden.

> [WACOM.NOTE]In diesem Lernprogramm wird die Integration von Mobile Services in Notification Hubs veranschaulicht, d. h., das Senden von Pushbenachrichtigungen von Ihrem mobilen Dienst. Bei Verwendung eines älteren mobilen Diensts, der Legacypushvorgänge verwendet und noch nicht für die Verwendung von Notification Hubs aktualisiert wurde, *empfehlen wir ein Upgrade* im Rahmen dieses Lernprogramms. Falls Sie sich jetzt gegen das Upgrade entscheiden, sollten Sie die folgende Lernprogrammversion nutzen: [Erste Schritte mit Pushbenachrichtigungen (Legacypushvorgang)][Erste Schritte mit Pushbenachrichtigungen (Legacypushvorgang)].

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen][Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen]
2.  [Aktualisieren von Serverskripts zum Senden von Pushbenachrichtigungen][Aktualisieren von Serverskripts zum Senden von Pushbenachrichtigungen]
3.  [Einfügen von Daten zum Empfangen von Pushbenachrichtigungen][Einfügen von Daten zum Empfangen von Pushbenachrichtigungen]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst entweder [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten][Erste Schritte mit Daten] abschließen, um Ihr Projekt mit Mobile Services zu verbinden. Wenn kein mobiler Dienst verbunden wurde, stellt der Assistent für Pushbenachrichtigungen diese Verbindung für Sie her.

> [WACOM.NOTE]Zum Senden von Pushbenachrichtigungen an eine Windows Phone 8.1 Store-App führen Sie die Lernprogrammversion für [Windows Store-Apps][Windows Store-Apps] aus.

## <span id="update-app"></span></a> Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen

Bevor die App Pushbenachrichtigungen empfangen kann, müssen Sie einen Benachrichtigungskanal registrieren.

1.  Öffnen Sie in Visual Studio die Datei "App.xaml.cs", und fügen Sie die folgende `using`-Anweisung hinzu:

        using Microsoft.Phone.Notification;

2.  Fügen Sie der Datei "App.xaml.cs" folgenden Code hinzu:

        public static HttpNotificationChannel CurrentChannel { get; private set; }

        private void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellToast();
            }

            CurrentChannel.ChannelUriUpdated +=
                new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
                {

                    // Register for notifications using the new channel
                    await MobileService.GetPush()
                        .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                });
        }

    Dieser Code ruft den Kanal-URI für die App aus dem von Windows Phone 8.x "Silverlight" verwendeten Microsoft-Pushbenachrichtigungsdienst (MPNS) ab und registriert anschließend diesen Kanal-URI für Pushbenachrichtigungen.

    > [WACOM.NOTE]In diesem Lernprogramm sendet der mobile Dienst eine Popupbenachrichtigung an das Gerät. Wenn Sie eine Kachelbenachrichtigung senden, müssen Sie stattdessen die **BindToShellTile**-Methode für den Kanal aufrufen.

3.  Fügen Sie in der Datei "App.xaml.cs" am Anfang des **Application\_Launching**-Ereignishandlers den folgenden Aufruf der neuen **AcquirePushChannel**-Methode hinzu:

        AcquirePushChannel();

    Dadurch wird sichergestellt, dass die Registrierung jedes Mal beim Laden der Seite angefordert wird. In Ihrer App möchten Sie diese Registrierung ggf. nur in bestimmten Abständen vornehmen, um sicherzustellen, dass die Registrierung noch aktuell ist.

4.  Drücken Sie **F5**, um die App auszuführen. Ein Dialogfenster wird mit dem Registrierungsschlüssel angezeigt.

5.  Erweitern Sie im Projektmappen-Explorer **Eigenschaften**, öffnen Sie die Datei "WMAppManifest.xml", klicken Sie auf die Registerkarte **Funktionen**, und aktivieren Sie die Funktion \*\*ID\_**CAP**\_PUSH\_NOTIFICATION\*\*.

    ![][0]

    Damit stellen Sie sicher, dass Ihre App Popupbenachrichtigungen anzeigen kann.

## <span id="update-scripts"></span></a> Aktualisieren von Serverskripts zum Senden von Pushbenachrichtigungen

Schließlich müssen Sie das Skript, das für den Einfügevorgang in der Tabelle "TodoItem" registriert wird, aktualisieren, um Benachrichtigungen zu senden.

1.  Klicken Sie auf **TodoItem**, dann auf **Script**, und wählen Sie **Insert** aus.

    ![][1]

2.  Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

        function insert(item, user, request) {
        // Define a payload for the Windows Phone toast notification.
        var payload = '<?xml version="1.0" encoding="utf-8"?>' +
            '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
            '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text + 
            '</wp:Text2></wp:Toast></wp:Notification>';

        request.execute({
            success: function() {
                // If the insert succeeds, send a notification.
                push.mpns.send(null, payload, 'toast', 22, {
                    success: function(pushResponse) {
                        console.log("Sent push:", pushResponse);
                        request.respond();
                        },              
                        error: function (pushResponse) {
                            console.log("Error Sending push:", pushResponse);
                            request.respond(500, { error: pushResponse });
                            }
                        });
                    }
                });      
        }

    Dieses Einfügeskript sendet eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags) an alle Windows Phone-App-Registrierungen, nachdem der Einfügevorgang erfolgreich war.

3.  Klicken Sie auf die Registerkarte **Push**, aktivieren Sie **Enable unauthenticated push notifications**, und klicken Sie dann auf **Speichern**.

    > [WACOM.NOTE]Wenn Sie dieses Lernprogramm mit einem älteren mobilen Dienst ausführen, kann unten auf der Registerkarte **Push** der Link **Erweiterten Push aktivieren** angezeigt werden. Klicken Sie jetzt auf diesen Link, um Ihren mobilen Dienst zu aktualisieren und Notification Hubs zu integrieren. Dies Änderung kann nicht rückgängig gemacht werden. Ausführliche Informationen zum Aktivieren erweiterter Pushbenachrichtigungen für einen mobilen Dienst in einer Produktionsumgebung finden Sie in [dieser Anleitung][dieser Anleitung].

    ![][2]

    Daraufhin kann der mobile Dienst im nicht authentifizierten Modus eine Verbindung mit MPNS zum Senden von Pushbenachrichtigungen herstellen.

    > [WACOM.NOTE]Dieses Lernprogramm verwendet MPNS im nicht authentifizierten Modus. In diesem Modus beschränkt MPNS die Anzahl der Benachrichtigungen, die an einen Gerätekanal gesendet werden können. Um diese Beschränkung aufzuheben, müssen Sie ein Zertifikat generieren und hochladen, indem Sie auf **Upload** klicken und das Zertifikat auswählen. Weitere Informationen zum Generieren des Zertifikats finden Sie unter [Einrichten eines authentifizierten Webdienstes zum Senden von Pushbenachrichtigungen für Windows Phone][Einrichten eines authentifizierten Webdienstes zum Senden von Pushbenachrichtigungen für Windows Phone].

## <span id="test"></span></a> Testen von Pushbenachrichtigungen in der App

1.  Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

    > [WACOM.NOTE] Beim Ausführen von Tests im Windows Phone-Emulator kann der Fehler "401 Nicht autorisiert – RegistrationAuthorizationException" auftreten. Der Fehler kann während des `RegisterNativeAsync()`-Aufrufs auftreten und wird dadurch verursacht, wie der Windows Phone-Emulator seine Uhr mit dem Host-PC synchronisiert. Dies kann dazu führen, dass ein Sicherheitstoken abgelehnt wird. Um das Problem zu beheben, stellen Sie die Uhrzeit im Emulator vor dem Testen einfach manuell ein.

2.  Geben Sie in der App in das Textfeld den Text "hello push" ein, und klicken Sie dann auf **Speichern**. Klicken Sie dann direkt auf die Schaltfläche "Start" oder "Zurück", um die App zu verlassen

    ![][3]

    Damit wird eine Einfügeanforderung zum Speichern des hinzugefügten Eintrags an den mobilen Dienst gesendet. Das Gerät empfängt eine Popupbenachrichtigung mit dem Text **hello push**.

    ![][4]

    > [WACOM.NOTE]Wenn Sie sich noch in der App befinden, erhalten Sie die Benachrichtigung nicht. Um eine Popupbenachrichtigung zu empfangen, während die App aktiv ist, müssen Sie das [ShellToastNotificationReceived][ShellToastNotificationReceived]-Ereignis behandeln.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurde allgemein erläutert, wie eine Windows Store-App für das Senden von Pushbenachrichtigungen unter Verwendung von Mobile Services und Notification Hubs eingerichtet wird. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen:

-   [Senden von Pushbenachrichtigungen an authentifizierte Benutzer][Senden von Pushbenachrichtigungen an authentifizierte Benutzer]
    Erfahren Sie, wie Pushbenachrichtigungen mithilfe von Tags von einem mobilen Dienst ausschließlich an einen authentifizierten Benutzer gesendet werden.

-   [Senden von Übertragungsbenachrichtigungen an Abonnenten][Senden von Übertragungsbenachrichtigungen an Abonnenten]
    Erfahren Sie, wie Benutzer sich registrieren und Pushbenachrichtigungen für Kategorien erhalten können, an denen sie interessiert sind.

<!---+ [Send template-based notifications to subscribers]     <br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end. -->

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



  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen]: #update-app
  [Aktualisieren von Serverskripts zum Senden von Pushbenachrichtigungen]: #update-scripts
  [Einfügen von Daten zum Empfangen von Pushbenachrichtigungen]: #test
  [Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-windows-phone-get-started
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-data
  [Windows Store-Apps]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push
  [0]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
  [1]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-insert-script-push2.png
  [dieser Anleitung]: http://go.microsoft.com/fwlink/p/?LinkId=391951
  [2]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png
  [Einrichten eines authentifizierten Webdienstes zum Senden von Pushbenachrichtigungen für Windows Phone]: http://msdn.microsoft.com/de-de/library/windowsphone/develop/ff941099(v=vs.105).aspx
  [3]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
  [4]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png
  [ShellToastNotificationReceived]: http://msdn.microsoft.com/de-de/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived(v=vs.105).aspx
  [Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-phone-push-notifications-app-users/
  [Senden von Übertragungsbenachrichtigungen an Abonnenten]: /de-de/documentation/articles/notification-hubs-windows-phone-send-breaking-news/
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-users
  [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
