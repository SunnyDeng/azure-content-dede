<properties pageTitle="Get started with push notification hubs using .NET runtime mobile services" metaKeywords="" description="Learn how to use Windows Azure .Net runtime mobile services and Notification Hubs to send push notifications to your Windows phone app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="wesmc"  solutions="" writer="wesmc" manager="dwrede" editor=""  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc"></tags>

# Erste Schritte mit Pushbenachrichtigungen in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push-legacy][mobile-services-selector-get-started-push-legacy]]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services mit einem .NET-Back-End Pushbenachrichtigungen an eine Windows Phone Silverlight 8-App senden. In diesem Lernprogramm aktivieren Sie Pushbenachrichtigungen mit Windows Azure Notification Hubs für das Schnellstartprojekt. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung mithilfe von Benachrichtigungshubs. Der von Ihnen erstellte Benachrichtigungshub ist für den mobilen Dienst kostenlos, kann unabhängig vom mobilen Dienst verwaltet werden und kann von anderen Anwendungen und Diensten verwendet werden.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen][Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen]
2.  [Aktualisieren des Servers zum Senden von Pushbenachrichtigungen][Aktualisieren des Servers zum Senden von Pushbenachrichtigungen]
3.  [Aktivieren von Pushbenachrichtigungen für lokale Tests][Aktivieren von Pushbenachrichtigungen für lokale Tests]
4.  [Einfügen von Daten zum Empfangen von Pushbenachrichtigungen][Einfügen von Daten zum Empfangen von Pushbenachrichtigungen]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst entweder [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten][Erste Schritte mit Daten] abschließen, um Ihr Projekt mit Mobile Services zu verbinden.

> [WACOM.NOTE]Dieses Lernprogramm ist auf Windows Phone 8.1 "Silverlight"-Apps ausgerichtet. Falls Sie stattdessen eine Windows Phone 8.1 Store-App erstellen, informieren Sie sich in der Lernprogrammversion zu [Windows Store-Apps][Windows Store-Apps]. Informationen zu Windows Phone Silverlight-Apps und einen Vergleich mit Windows Phone Store-Apps finden Sie unter [Windows Phone Silverlight 8.1-Apps][Windows Phone Silverlight 8.1-Apps].

## <span id="update-app"></span></a> Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen

Bevor die App Pushbenachrichtigungen empfangen kann, müssen Sie einen Benachrichtigungskanal registrieren.

1.  Öffnen Sie in Visual Studio die Datei "App.xaml.cs", und fügen Sie die folgende `using`-Anweisung hinzu:

        using Microsoft.Phone.Notification;

2.  Fügen Sie der `App`-Klasse die folgende `AcquirePushChannel`-Methode hinzu:

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

3.  Fügen Sie im `Application_Launching`-Ereignishandler in "App.xaml.cs" der neuen `AcquirePushChannel`-Methode folgenden Aufruf hinzu:

        AcquirePushChannel();

    Auf diese Weise wird sichergestellt, dass die Registrierung bei jedem Laden der App angefordert wird. In Ihrer App möchten Sie diese Registrierung ggf. nur in bestimmten Abständen vornehmen, um sicherzustellen, dass die Registrierung noch aktuell ist.

4.  Drücken Sie **F5**, um die App auszuführen. Ein Dialogfenster wird mit dem Registrierungsschlüssel angezeigt.

5.  Öffnen Sie in Visual Studio die Datei "Package.appxmanifest" und vergewissern Sie sich, dass **Toast Capable** in der Registerkarte **Application UI** auf **Yes** gesetzt ist.

    ![][]

    Damit stellen Sie sicher, dass Ihre App Popupbenachrichtigungen anzeigen kann.

## <span id="update-server"></span></a> Aktualisieren des Servers zum Senden von Pushbenachrichtigungen

1.  Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **Controllers** im mobilen Dienstprojekt. Öffnen Sie die Datei "TodoItemController.cs", und aktualisieren Sie die `PostTodoItem`-Methodendefinition mithilfe des folgenden Codes:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);
            MpnsPushMessage message = new MpnsPushMessage();
            message.XmlPayload = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>" + item.Text + "</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Dieser Code sendet eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags), nachdem ein todo-Eintrag eingefügt wurde. Falls ein Fehler auftritt, wird vom Code ein Fehlerprotokolleintrag hinzugefügt, der auf der Registerkarte **Protokolle** des mobilen Diensts im Verwaltungsportal angezeigt werden kann.

2.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre Anwendung.

3.  Klicken Sie auf die Registerkarte **Push**, aktivieren Sie **Enable unauthenticated push notifications**, und klicken Sie dann auf **Speichern**.

    ![][1]

    > [WACOM.NOTE]Dieses Lernprogramm verwendet MPNS im nicht authentifizierten Modus. In diesem Modus beschränkt MPNS die Anzahl der Benachrichtigungen, die an einen Gerätekanal gesendet werden können. Um diese Beschränkung aufzuheben, müssen Sie ein Zertifikat generieren und hochladen, indem Sie auf **Upload** klicken und das Zertifikat auswählen. Weitere Informationen zum Generieren des Zertifikats finden Sie unter [Einrichten eines authentifizierten Webdienstes zum Senden von Pushbenachrichtigungen für Windows Phone][Einrichten eines authentifizierten Webdienstes zum Senden von Pushbenachrichtigungen für Windows Phone].

Daraufhin kann der mobile Dienst im nicht authentifizierten Modus eine Verbindung mit MPNS zum Senden von Pushbenachrichtigungen herstellen.

## <span id="local-testing"></span></a>Aktivieren von Pushbenachrichtigungen für lokale Tests

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push][mobile-services-dotnet-backend-configure-local-push]]

## <span id="test"></span></a> Testen von Pushbenachrichtigungen in der App

1.  Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

    > [WACOM.NOTE] Beim Ausführen von Tests im Windows Phone-Emulator kann der Fehler "401 Nicht autorisiert –RegistrationAuthorizationException" auftreten. Der Fehler kann während des `RegisterNativeAsync()`-Aufrufs auftreten und wird dadurch verursacht, wie der Windows Phone-Emulator seine Uhr mit dem Host-PC synchronisiert. Dies kann dazu führen, dass ein Sicherheitstoken abgelehnt wird. Um das Problem zu beheben, stellen Sie die Uhrzeit im Emulator vor dem Testen einfach manuell ein.

2.  Geben Sie in der App in das Textfeld den Text "hello push" ein, und klicken Sie dann auf **Speichern**. Klicken Sie dann direkt auf die Schaltfläche "Start" oder "Zurück", um die App zu verlassen

    ![][2]

    Damit wird eine Einfügeanforderung zum Speichern des hinzugefügten Eintrags an den mobilen Dienst gesendet. Das Gerät empfängt eine Popupbenachrichtigung mit dem Text **hello push**.

    ![][3]

    > [WACOM.NOTE]Wenn Sie sich noch in der App befinden, erhalten Sie die Benachrichtigung nicht. Um eine Popupbenachrichtigung zu empfangen, während die App aktiv ist, müssen Sie das [ShellToastNotificationReceived][ShellToastNotificationReceived]-Ereignis behandeln.

## <a name="next-steps">Nächste Schritte</a>

In diesem Lernprogramm wurde allgemein erläutert, wie eine Windows Phone-App für das Senden von Pushbenachrichtigungen unter Verwendung von Mobile Services und Notification Hubs eingerichtet wird. Im Anschluss empfiehlt sich das nächste Lernprogramm [Senden von Pushbenachrichtigungen an authentifizierte Benutzer][Senden von Pushbenachrichtigungen an authentifizierte Benutzer]. Hier wird erläutert, wie Pushbenachrichtigungen mithilfe von Tags von einem mobilen Dienst ausschließlich an einen authentifizierten Benutzer gesendet werden.

<!--+ [Send push notifications to authenticated users]     <br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.  + [Send broadcast notifications to subscribers]     <br/>Learn how users can register and receive push notifications for categories they're interested in. -->

Weitere Informationen zu Mobile Services und Notification Hubs:

-   [Erste Schritte mit Daten][Erste Schritte mit Daten]
    Informationen zum Speichern und Abfragen von Daten mit mobilen Diensten.

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App mit verschiedenen Kontotypen mithilfe von mobilen Diensten.

-   [Was sind Notification Hubs?][Was sind Notification Hubs?]
    Erfahren Sie, wie Sie mit Benachrichtigungshubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz][Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
    Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [mobile-services-selector-get-started-push-legacy]: ../includes/mobile-services-selector-get-started-push-legacy.md
  [Aktualisieren Ihrer App zur Registrierung für Benachrichtigungen]: #update-app
  [Aktualisieren des Servers zum Senden von Pushbenachrichtigungen]: #update-server
  [Aktivieren von Pushbenachrichtigungen für lokale Tests]: #local-testing
  [Einfügen von Daten zum Empfangen von Pushbenachrichtigungen]: #test
  [Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-data
  [Windows Store-Apps]: mobile-services-dotnet-backend-windows-store-dotnet-get-started-push
  [Windows Phone Silverlight 8.1-Apps]: http://msdn.microsoft.com/de-de/library/windowsphone/develop/dn642082(v=vs.105).aspx
  []: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-push-tab.png
  [Einrichten eines authentifizierten Webdienstes zum Senden von Pushbenachrichtigungen für Windows Phone]: http://msdn.microsoft.com/de-de/library/windowsphone/develop/ff941099(v=vs.105).aspx
  [mobile-services-dotnet-backend-configure-local-push]: ../includes/mobile-services-dotnet-backend-configure-local-push.md
  [2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
  [3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-push/mobile-quickstart-push5-wp8.png
  [ShellToastNotificationReceived]: http://msdn.microsoft.com/library/windowsphone/develop/microsoft.phone.notification.httpnotificationchannel.shelltoastnotificationreceived.aspx
  [Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-push-notifications-app-users/
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users
  [Was sind Notification Hubs?]: /de-de/documentation/articles/notification-hubs-overview/
  [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-html-how-to-use-client-library
