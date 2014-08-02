<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Windows Azure .Net runtime mobile services and Notification Hubs to send push notifications to your Windows phone app." metaCanonical="" services="mobile" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc" solutions="" writer="wesmc" manager="" editor="" />

Erste Schritte mit Pushbenachrichtigungen in Mobile Services
============================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push "Windows Store C#") [Windows Store JavaScript](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push "Windows Store JavaScript") [Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push "Windows Phone")
[.NET backend](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push ".NET backend") | [JavaScript backend](/en-us/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/ "JavaScript backend")

In diesem Thema wird gezeigt, wie Sie mit Windows Azure .Net runtime Mobile Services Pushbenachrichtigungen an eine Windows Phone 8-App senden können. In diesem Lernprogramm aktivieren Sie Pushbenachrichtigungen mit Windows Azure Notification Hubs für das Schnellstartprojekt. Wenn dies abgeschlossen ist, sendet Ihr mobiler .Net-Laufzeitdienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung mit Notification Hubs. Der von Ihnen erstellte Benachrichtigungshub ist nicht mit Ihrem mobilen Dienst verknüpft, kann unabhängig vom mobilen Dienst verwaltet sowie von anderen Anwendungen und Diensten verwendet werden.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Aktualisieren der App zum Registrieren für Benachrichtigungen](#update-app)
2.  [Aktualisieren des Servers zum Senden von Pushbenachrichtigungen](#update-server)
3.  [Einfügen von Daten zum Empfangen von Pushbenachrichtigungen](#test)

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst entweder [Erste Schritte mit Mobile Services](/en-us/documentation/articles/mobile-services-windows-store-get-started) oder [Erste Schritte mit Daten](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data) abschließen, um Ihr Projekt mit Mobile Services zu verbinden.

Aktualisieren der App zum Registrieren für Benachrichtigungen
-------------------------------------------------------------

Bevor Ihre App Pushbenachrichtigungen empfangen kann, müssen Sie einen Benachrichtigungskanal registrieren.

1.  Öffnen Sie in Visual Studio die Datei "App.xaml.cs". Fügen Sie die folgende `using`-Anweisung hinzu:

         using Microsoft.Phone.Notification;

2.  Fügen Sie die folgende `AcquirePushChannel`-Methode zur `App`-Klasse hinzu:

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
                     System.Exception exception = null;
                     try
                     {
                         await MobileService.GetPush()
                             .RegisterNativeAsync(CurrentChannel.ChannelUri.ToString());
                     }
                     catch (System.Exception ex)
                     {
                         CurrentChannel.Close();
                         exception = ex;
                     }
                     if (exception != null)
                     {
                         Deployment.Current.Dispatcher.BeginInvoke(() =>
                         {
                             MessageBox.Show(exception.Message, 
                                             "Registering for Push Notifications",
                                             MessageBoxButton.OK);
                         });
                     }
             });
             CurrentChannel.ShellToastNotificationReceived += 
                 new EventHandler<NotificationEventArgs>((o, args) =>
                 {
                     string message = "";
                     foreach (string key in args.Collection.Keys)
                     {
                         message += key + " : " + args.Collection[key] + ", ";
                     }
                     Deployment.Current.Dispatcher.BeginInvoke(() =>
                     {
                         MessageBox.Show(message);
                     });
             });
         }

    Dieser Code ruft den Kanal-URI für die App ab, falls vorhanden. Andernfalls wird er erstellt. Der Kanal-URI wird dann für Popupbenachrichtigungen geöffnet und gebunden. Sobald der Kanal-URI vollständig geöffnet ist, wird der Handler für die `ChannelUriUpdated`-Methode aufgerufen, und der Kanal wird registriert, um Pushbenachrichtigungen zu empfangen. Falls die Registrierung fehlschlägt, wird der Kanal geschlossen, sodass nachfolgende Ausführungen der App die Registrierung erneut versuchen können. Der `ShellToastNotificationReceived`-Handler wird so eingerichtet, dass die App während der Ausführung Pushbenachrichtigungen empfangen und verarbeiten kann.

3.  Fügen Sie in der Datei "App.xaml.cs" des `Application_Launching`-Ereignishandlers den folgenden Aufruf der neuen `AcquirePushChannel`-Methode hinzu:

         AcquirePushChannel();

    Auf diese Weise wird sichergestellt, dass die Registrierung bei jedem Laden der App angefordert wird. Sie können diese Registrierung in Ihrer App auch nur von Zeit zu Zeit durchführen, um sicherzustellen, dass die Registrierung aktuell ist.

4.  Drücken Sie **F5**, um die App auszuführen. Ein Dialogfenster wird mit dem Registrierungsschlüssel angezeigt.

5.  Öffnen Sie die Datei "Package.appxmanifest" in Visual Studio. Stellen Sie sicher, dass **Toast capable** auf **Yes** auf der Registerkarte **Application UI** festgelegt ist.

      ![][1]

      Damit stellen Sie sicher, dass Ihre App Pushbenachrichtigungen auslösen kann. 

Aktualisieren des Servers zum Senden von Pushbenachrichtigungen
---------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

1.  Melden Sie sich am [Windows Azure-Verwaltungsportal](%20https://manage.windowsazure.com/) an, klicken Sie auf **Mobile Services** und dann auf Ihre Anwendung.

2.  Klicken Sie auf die Registerkarte **Push**, aktivieren Sie **Enable unauthenticated push notifications**, und klicken Sie dann auf **Save**.

![](./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png)

> [WACOM.NOTE]Dieses Lernprogramm verwendet MPNS im nicht authentifizierten Modus. In diesem Modus beschränkt MPNS die Anzahl der Benachrichtigungen, die an einen Gerätekanal gesendet werden können. Um diese Beschränkung zu entfernen, müssen Sie ein Zertifikat generieren und hochladen, indem Sie auf **Upload** klicken und das Zertifikat auswählen. Weitere Informationen zum Generieren des Zertifikats finden Sie unter [Setting up an authenticated web service to send push notifications for Windows Phone](http://msdn.microsoft.com/en-us/library/windowsphone/develop/ff941099(v=vs.105).aspx).

Dadurch kann der mobile Dienst eine Verbindung zu MPNS im nicht authentifizierten Modus herstellen, um Pushbenachrichtigungen zu senden.

Testen von Pushbenachrichtigungen in der App
--------------------------------------------

1.  Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2.  Geben Sie in der App in das Textfeld den Text "hello push" ein, und klicken Sie dann auf **Save**.

      ![][2]

  Damit wird eine Einfügeanforderung zum Speichern des hinzugefügten Elements an den mobilen Dienst gesendet. Beachten Sie, dass die Anwendung eine Popupbenachrichtigung mit dem Inhalt **hello push** empfängt.

Nächste Schritte
----------------

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Arbeit mit Daten in Mobile Services gezeigt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Erste Schritte mit Notification Hubs](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
    <br/>Erfahren Sie, wie Sie Notification Hubs in Ihrer Windows Store-App einsetzen können.

-   [Senden von Benachrichtigungen an Abonnenten](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
    <br/>Erfahren Sie, wie Benutzer sich registrieren und Pushbenachrichtigungen für Kategorien erhalten können, an denen sie interessiert sind.

-   [Senden von Benachrichtigungen an Benutzer](/en-us/manage/services/notification-hubs/notify-users/)
    <br/>Erfahren Sie, wie Sie Pushbenachrichtigungen von einem mobilen Dienst an bestimmte Benutzer auf beliebigen Geräten senden können.

-   [Senden plattformübergreifender Benachrichtigungen an Benutzer](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    <br/>Erfahren Sie, wie Vorlagen zum Senden von Pushbenachrichtigungen mit einem mobilen Dienst gesendet werden, ohne dass Sie in Ihrem Back-End auf plattformspezifische Nutzlasten zurückgreifen müssen.

Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data)
    <br/>Erfahren Sie mehr über das Speichern und Abfragen von Daten mit mobilen .Net-Laufzeitdaten.

-   [Erste Schritte mit der Authentifizierung](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users)
    <br/>Erfahren Sie, wie Benutzer Ihrer App mit verschiedenen Kontotypen mithilfe von mobilen .Net-Laufzeitdiensten authentifiziert werden.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>Erfahren Sie mehr über das Registrieren und Verwenden von Serverskripts.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz](/en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library)
    <br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET.

<!-- Images. -->


[1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/documentation/articles/mobile-services-windows-store-get-started
[Get started with data]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
[Get started with authentication]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
[Get started with push notifications]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started-push

[Get started with Notification Hubs]: /en-us/manage/services/notification-hubs/getting-started-windows-dotnet/
[What are Notification Hubs?]: /en-us/develop/net/how-to-guides/service-bus-notification-hubs/
[Send notifications to subscribers]: /en-us/manage/services/notification-hubs/breaking-news-dotnet/
[Send notifications to users]: /en-us/manage/services/notification-hubs/notify-users/
[Send cross-platform notifications to users]: /en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services .NET How-to Conceptual Reference]: /en-us/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library
