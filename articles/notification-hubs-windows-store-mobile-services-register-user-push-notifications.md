<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-mobile-service-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Mobile Services" pageTitle="Register the current user for push notifications by using a mobile service - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by Azure Mobile Services." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Register the current user for push notifications by using a mobile service" authors="" solutions="" manager="" editor="" />

Registrieren des aktuellen Benutzers für Pushbenachrichtigungen mithilfe eines mobilen Dienstes
===============================================================================================

[Windows Store C\#](/de-de/documentation/articles/notification-hubs-windows-store-mobile-services-register-user-push-notifications/ "Windows Store C#")[iOS](/de-de/documentation/articles/notification-hubs-ios-mobile-services-register-user-push-notifications/ "iOS")

In diesem Artikel erfahren Sie, wie Sie Pushbenachrichtigungs-Registrierungen mit Azure Notification Hubs anfordern können, wenn die Registrierung von Azure Mobile Services durchgeführt wird. Dieses Lernprogramm baut auf dem Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs](/de-de/manage/services/notification-hubs/notify-users) auf. Sie müssen zuvor die Schritte in diesem Lernprogramm abgeschlossen haben, in denen der authentifizierte Mobile Service erstellt wird. Weitere Informationen zum Benachrichtigen von Benutzern finden Sie unter [Benachrichtigen von Benutzern mit Notification Hubs](/de-de/manage/services/notification-hubs/notify-users).

1.  Öffnen Sie in Visual Studio 2012 Express für Windows 8 das Projekt, das Sie erstellt haben, als Sie das Lernprogramm [Erste Schritte mit der Authentifizierung](/de-de/develop/mobile/tutorials/get-started-with-users-dotnet/) abgeschlossen haben.

2.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Store**, und klicken Sie dann auf **Associate App with the Store**.

	![](./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/mobile-services-select-app-name.png)

	Daraufhin wird der Assistent **Associate Your App with the Windows Store** angezeigt.

1.  Klicken Sie im Assistenten auf **Sign in**. Melden Sie sich dann mit Ihrem Microsoft-Konto ein.

2.  Wählen Sie die App aus, die Sie in [Benachrichtigen von Benutzern mit Notification Hubs](/de-de/manage/services/notification-hubs/notify-users) registriert haben, klicken Sie auf **Next** und dann auf **Associate**.

	![][2]

	Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.  

    **Hinweis**

    Auf diese Weise wird die Windows Store-Registrierung aus der Notification Hubs-Lernprogramm-App mit dieser Mobile Services-App wiederverwendet. So kann verhindert werden, dass die Notification Hubs-Lernprogramm-App Benachrichtigungen empfängt.

3.  Doppelklicken Sie im Projektmappen-Explorer auf die Projektdatei "Package.appxmanifest", um sie im Visual Studio-Editor zu öffnen.

4.  Führen Sie einen Bildlauf nach unten zu **All Image Assets** aus, und klicken Sie auf **Badge Logo**. Legen Sie unter **Benachrichtigungen** den Wert für **Toast capable** auf **Ja** fest:

	![][3]

    Auf diese Weise kann die Mobile Services-Lernprogramm-App Popupbenachrichtigungen empfangen.

5.  Öffnen Sie in Visual Studio die Datei "MainPage.xaml.cs", und fügen Sie den folgenden Code hinzu, der die Klassen **NotificationRequest** und **RegistrationResult** definiert:

         public class NotificationRequest
         {
             public string channelUri { get; set; }
             public string platform { get; set; }
         }

         public class RegistrationResult
         {
             public string RegistrationId { get; set; }
             public string ExpirationTime { get; set; }
         }

    Diese Klassen enthalten den Anforderungstext und die Registrierungs-ID, der bzw. die beim Aufruf der benutzerdefinierten API zurückgegeben wird.

6.  Fügen Sie in der **MainPage**-Klasse die folgende Methode hinzu:

         private async System.Threading.Tasks.Task RegisterNotification()
         {
             string message;

             // Get a channel for push notifications.
             var channel =
                 await Windows.Networking.PushNotifications
                 .PushNotificationChannelManager
                 .CreatePushNotificationChannelForApplicationAsync();

             // Create the body of the request.
             var body = new NotificationRequest 
             {
                 channelUri = channel.Uri, 
                 platform = "win8" 
             }; 

             try
             {
                 // Call the custom API POST method with the supplied body.
                 var result = await App.MobileService
                     .InvokeApiAsync<NotificationRequest, 
                     RegistrationResult>("register_notifications", body,
                     System.Net.Http.HttpMethod.Post, null);

                 // Set the response, which is the ID of the registration.
                 message = string.Format("Registration ID: {0}", result.RegistrationId);
             }
             catch (MobileServiceInvalidOperationException ex)
             {
                 message = ex.Message;
             }

             // Display a message dialog.
             var dialog = new MessageDialog(message);
             dialog.Commands.Add(new UICommand("OK"));
             await dialog.ShowAsync();
         }

    Diese Methode erstellt ein Kanal für Pushbenachrichtigungen und sendet ihn, zusammen mit dem Gerätetyp, an die benutzerdefinierte API-Methode, die eine Registrierung in Notification Hubs erstellt. Diese benutzerdefinierte API wurde in [Benachrichtigen von Benutzern mit Notification Hubs](/de-de/manage/services/notification-hubs/notify-users) definiert.

7.  Fügen Sie der **OnNavigatedTo**-Methode nach dem Aufruf der **Authenticate**-Methode die folgende Codezeile hinzu:

         await RegisterNotification();

    **Hinweis**

    Auf diese Weise wird sichergestellt, dass die Registrierung bei jedem Laden der Seite angefordert wird. In Ihrer App können Sie diese Registrierung auch nur von Zeit zu Zeit durchführen, um sicherzustellen, dass die Registrierung aktuell ist.

Nun haben Sie die Client-App aktualisiert und können zum Thema [Benachrichtigen von Benutzern mit Notification Hubs](/de-de/manage/services/notification-hubs/notify-users) zurückkehren und den mobilen Dienst aktualisieren, um Benachrichtigungen mit Notification Hubs zu senden.

<!-- Anchors. -->

<!-- Images. -->
[1]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/mobile-services-select-app-name.png
[2]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-associate-win8-app.png
[3]: ./media/notification-hubs-windows-store-mobile-services-register-user-push-notifications/notification-hub-win8-app-toast.png

<!-- URLs. -->
[Notify users with Notification Hubs]: /de-de/manage/services/notification-hubs/notify-users
[Get started with authentication]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet/

[Azure Management Portal]: https://manage.windowsazure.com/