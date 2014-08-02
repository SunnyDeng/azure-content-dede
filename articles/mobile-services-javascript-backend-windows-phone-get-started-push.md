<properties pageTitle="Get started with push notifications (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your Windows Store app." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="" manager="" editor="" />

Erste Schritte mit Pushbenachrichtigungen in Mobile Services
============================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started-push "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-ios-get-started-push "iOS")[Android](/en-us/documentation/articles/mobile-services-javascript-backend-android-get-started-push "Android")
[.NET-Backend](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/ ".NET-Backend") | [JavaScript-Backend](/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/ "JavaScript-Backend")

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services Pushbenachrichtigungen an eine Windows Store-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Azure-Benachrichtigungshubs zum Schnellstartprojekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung mithilfe von Benachrichtigungshubs. Der von Ihnen erstellte Benachrichtigungshub ist für den mobilen Dienst kostenlos, kann unabhängig vom mobilen Dienst verwaltet werden und kann von anderen Anwendungen und Diensten verwendet werden.

> [WACOM.NOTE]In diesem Lernprogramm wird die Mobile Services-Integration mit Benachrichtigungshubs dargestellt, die sich momentan in der Vorschauphase befindet. Standardmäßig ist das Senden von Pushbenachrichtigungen unter Verwendung von Benachrichtigungshubs auf einem JavaScript-Backend nicht aktiviert. Sobald der neue Benachrichtigungshub erstellt wurde, kann der Integrationsprozess nicht mehr rückgängig gemacht werden. Pushbenachrichtigungen für iOS und Android sind derzeit nur über die standardmäßige Push-Unterstützung verfügbar, die in [diesem Thema](/en-us/documentation/articles/mobile-services-windows-phone-get-started-push/) beschrieben ist.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen](#update-app)
2.  [Aktualisieren von Serverskripts zum Senden von Pushbenachrichtigungen](#update-scripts)
3.  [Einfügen von Daten zum Empfangen von Pushbenachrichtigungen](#test)

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst entweder [Erste Schritte mit Mobile Services](/en-us/documentation/articles/mobile-services-windows-phone-get-started) oder [Erste Schritte mit Daten](/en-us/documentation/articles/mobile-services-windows-phone-get-started-data) abschließen, um Ihr Projekt mit Mobile Services zu verbinden. Wenn kein mobiler Dienst verbunden wurde, stellt der Assistent für Pushbenachrichtigungen diese Verbindung für Sie her.

Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen
----------------------------------------------------------------

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

                     // Für Benachrichtigungen mit neuem Kanal registrieren
                     await MobileService.GetPush()
                         .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                 });
         }

    Dieser Code ruft den Kanal-URI für die App vom WNS ab und registriert anschließend diesen Kanal-URI für Pushbenachrichtigungen.

    > [WACOM.NOTE]In diesem Lernprogramm sendet der mobile Dienst eine Popupbenachrichtigung an das Gerät. Wenn Sie eine Kachelbenachrichtigung senden, müssen Sie stattdessen die **BindToShellTile**-Methode für den Kanal aufrufen.

3.  Fügen Sie in der Datei "App.xaml.cs" am Anfang des **Application\_Launching**-Ereignishandlers den folgenden Aufruf der neuen **AcquirePushChannel**-Methode hinzu:

         AcquirePushChannel();

    Dadurch wird sichergestellt, dass die Registrierung jedes Mal beim Laden der Seite angefordert wird. In Ihrer App möchten Sie diese Registrierung ggf. nur in bestimmten Abständen vornehmen, um sicherzustellen, dass die Registrierung noch aktuell ist.

4.  Drücken Sie **F5**, um die App auszuführen. Ein Dialogfenster wird mit dem Registrierungsschlüssel angezeigt.

5.  Erweitern Sie im Projektmappen-Explorer **Eigenschaften**, öffnen Sie die Datei "WMAppManifest.xml", klicken Sie auf die Registerkarte **Funktionen**, und aktivieren Sie die **ID\_\_*CAP*\_\_PUSH\_NOTIFICATION**-Funktion.

    ![](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png)
 <br/>Damit stellen Sie sicher, dass Ihre App Popupbenachrichtigungen anzeigen kann.

Aktualisieren von Serverskripts zum Senden von Pushbenachrichtigungen
---------------------------------------------------------------------

Abschließend müssen Sie das für den insert-Vorgang registrierte Skript in der TodoItem-Tabelle zum Senden von Benachrichtigungen aktualisieren.

1.  Klicken Sie auf **TodoItem**, dann auf **Script**, und wählen Sie **Insert** aus.

      ![][10]

2.  Ersetzen Sie die Einfügefunktion durch den folgenden Code und klicken Sie auf **Save**:

         function insert(item, user, request) {
         // Nutzlast für Windows Phone-Popupbenachrichtigung definieren.
         var payload = '<
         xml version="1.0" encoding="utf-8"
         >' +
             '<wp:Notification xmlns:wp="WPNotification"><wp:Toast>' +
             '<wp:Text1>New Item</wp:Text1><wp:Text2>' + item.text + 
             '</wp:Text2></wp:Toast></wp:Notification>';
            
         request.execute({
             success: function() {
                 // Nach erfolgreichem Einfügen Benachrichtigung senden.
                push.mpns.send(null, payload, 'toast', 22, {
                     success: function(pushResponse) {
                         console.log("Sent push:", pushResponse);
                         request.respond();
                         },              
                         error: function(pushResponse) {
                             console.log("Fehler beim Senden der Pushbenachrichtigung:", pushResponse);
                             request.respond(500, { error: pushResponse });
                             }
                         });
                     }
                 });      
         }

    Dieses Einfügeskript sendet eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags) an alle Windows Phone-App-Registrierungen, nachdem der Einfügevorgang erfolgreich war.

3.  Klicken Sie auf die Registerkarte **Push**, aktivieren Sie **Enable unauthenticated push notifications**, und klicken Sie dann auf **Speichern**.

    ![](./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png)

    Daraufhin kann der mobile Dienst im nicht authentifizierten Modus eine Verbindung mit MPNS zum Senden von Pushbenachrichtigungen herstellen.

    > [WACOM.NOTE]Dieses Lernprogramm verwendet MPNS im nicht authentifizierten Modus. In diesem Modus beschränkt MPNS die Anzahl der Benachrichtigungen, die an einen Gerätekanal gesendet werden können. Um diese Beschränkung aufzuheben, müssen Sie ein Zertifikat generieren und hochladen, indem Sie auf **Upload** klicken und das Zertifikat auswählen. Weitere Informationen zum Generieren des Zertifikats finden Sie unter [Einrichten eines authentifizierten Webdienstes zum Senden von Pushbenachrichtigungen für Windows Phone](http://msdn.microsoft.com/en-us/library/windowsphone/develop/ff941099(v=vs.105).aspx).

Testen von Pushbenachrichtigungen in der App
--------------------------------------------

1.  Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2.  Geben Sie in der App in das Textfeld den Text "hello push" ein, und klicken Sie dann auf **Save**.

      ![][4]

    Damit wird eine Einfügeanforderung zum Speichern des hinzugefügten Eintrags an den mobilen Dienst gesendet. Die Anwendung erhält eine Popupbenachrichtigung mit dem Text **hello push**.

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

-   [Erste Schritte mit Daten](/en-us/documentation/articles/mobile-services-windows-phone-get-started-data)
    <br/>Weitere Informationen zum Speichern und Abfragen von Daten mit mobilen Diensten.

-   [Erste Schritte mit der Authentifizierung](/en-us/documentation/articles/mobile-services-windows-phone-get-started-users)
    <br/>Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz](/en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library)
    <br/>Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[10]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-insert-script-push2.png
[11]: ./media/mobile-services-javascript-backend-windows-phone-get-started-push/mobile-push-tab.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-windows-phone-get-started
[Get started with data]: /en-us/documentation/articles/mobile-services-windows-phone-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-windows-phone-get-started-users
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-windows-phone-get-started-push

[Get started with Notification Hubs]: /en-us/manage/services/notification-hubs/getting-started-windows-dotnet/
[What are Notification Hubs?]: /en-us/develop/net/how-to-guides/service-bus-notification-hubs/
[Send notifications to subscribers]: /en-us/manage/services/notification-hubs/breaking-news-dotnet/
[Send notifications to users]: /en-us/manage/services/notification-hubs/notify-users/
[Send cross-platform notifications to users]: /en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services .NET How-to Conceptual Reference]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
[Setting up an authenticated web service to send push notifications for Windows Phone]: http://msdn.microsoft.com/en-us/library/windowsphone/develop/ff941099(v=vs.105).aspx

