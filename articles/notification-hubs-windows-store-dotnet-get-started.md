<properties linkid="develop-notificationhubs-tutorials-get-started-windowsdotnet" urlDisplayName="Get started with notification hubs" pageTitle="Get started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Getting Started with Notification Hubs" authors="elioda" solutions="" manager="" editor="" />

Erste Schritte mit Benachrichtigungshubs
========================================

[Windows Store C\#](/de-de/manage/services/notification-hubs/getting-started-windows-dotnet "Windows Store C#")[Windows Phone](/de-de/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone")[iOS](/de-de/documentation/articles/notification-hubs-ios-get-started/ "iOS")[Android](/de-de/documentation/articles/notification-hubs-android-get-started/ "Android")[Kindle](/de-de/documentation/articles/notification-hubs-kindle-get-started/ "Kindle")[Xamarin.iOS](/de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS")[Xamarin.Android](/de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android")

In diesem Thema wird gezeigt, wie Sie mit Azure Benachrichtigungshubs Pushbenachrichtigungen an eine Windows Store-Anwendung senden können. In diesem Lernprogramm erstellen Sie eine leere Windows Store-App, die Pushbenachrichtigungen mithilfe des Windows-Pushbenachrichtigungsdiensts (WNS) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Benachrichtigungshub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Registrieren der App für Pushbenachrichtigungen](#register)
2.  [Konfigurieren Ihres Benachrichtigungshubs](#configure-hub)
3.  [Verbinden Ihrer App mit dem Benachrichtigungshub](#connecting-app)
4.  [Senden von Benachrichtigungen von Ihrem Back-End aus](#send)

In diesem Lernprogramm wird ein einfaches Übertragungsszenario mit Benachrichtigungshubs dargestellt. Bearbeiten Sie auch das nachfolgende Lernprogramm, um mehr über die Verwendung von Benachrichtigungshubs zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren. Für dieses Lernprogramm ist Folgendes erforderlich:

-   Microsoft Visual Studio 2012 Express für Windows 8
-   Ein aktives Windows Store-Konto

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Benachrichtigungshub-Lernprogramme für Windows Store-Apps.

**Hinweis**

Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenloses Azure-Testkonto](http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F).

Registrieren Ihrer AppRegistrieren Ihrer App für den Windows Store
------------------------------------------------------------------

Sie müssen die App an den Windows Store übermitteln, um Pushbenachrichtigungen von Mobile Services an Windows Store-Apps senden zu können. Sie müssen anschließend den Benachrichtigungshub zur Integration in den WNS konfigurieren.

1.  Falls Sie die App noch nicht registriert haben, navigieren Sie im Entwicklungscenter für Windows Store-Apps zu [App-Seite senden](http://go.microsoft.com/fwlink/p/?LinkID=266582), melden Sie sich mit Ihrem Microsoft-Konto an, und klicken Sie dann auf **App-Name**.

	![][0]

2.  Geben Sie einen Namen für die App unter **App-Name** ein, klicken Sie auf **App-Namen reservieren** und dann auf **Speichern**.

	![][1]

	Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre App erstellt.

3.  Erstellen Sie in Visual Studio 2012 Express für Windows 8 ein neues Visual C\# Windows Store-Projekt mit der Vorlage **Leere App**.

	![][2]

4.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Store**, und klicken Sie dann auf **App dem Store zuordnen**.

	![][3]

	Der Assistent **Zuordnen Ihrer App zum Windows Store** wird angezeigt.

5.  Klicken Sie im Assistenten auf **Anmelden**. Melden Sie sich dann mit Ihrem Microsoft-Konto an.

6.  Klicken Sie auf die App, die Sie in Schritt 2 registriert haben. Klicken Sie auf **Weiter** und dann auf **Zuordnen**.

	![][4]

	Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.    

7.  Klicken Sie auf der Windows-Entwicklungscenter-Seite für die neue App auf **Dienste**.

	![][5] 

8.  Klicken Sie auf der Seite **Dienste** auf **Live-Dienste-Website** unter **Azure Mobile Services**.

	![][17]

9.  Klicken Sie auf **Authentifizieren des Diensts** und notieren Sie sich die Werte von **Geheimer Clientschlüssel** und **Paket-Sicherheits-ID (SID)**.

	![][6]

	<div class="dev-callout"\><b>Sicherheitshinweis</b>
    <p>Der geheime Clientschlüssel und die Paket-SID sind wichtige sicherheitsrelevante Anmeldeinformationen. Geben Sie diese Werte nicht weiter, und verteilen Sie sie nicht mit Ihrer App.</p>
    </div>

Konfigurieren Ihres BenachrichtigungshubsKonfigurieren Ihres Benachrichtigungshubs
----------------------------------------------------------------------------------

1.  Melden Sie sich am [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, und klicken Sie unten auf dem Bildschirm auf **NEU**.

2.  Klicken Sie auf **App-Dienste**, **Service Bus**, **Benachrichtigungshub** und zuletzt auf **Schnellerfassung**.
	
	![][7]

3.  Geben Sie einen Namen für den Benachrichtigungshub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Neuen Benachrichtigungshub erstellen**.

	![][8]

4.  Klicken Sie auf den soeben erstellten Namespace (für gewöhnlich ***Name des Benachrichtigungshubs*-ns**) und anschließend oben auf die Registerkarte **Konfigurieren**.

	![][9]

5.  Wählen Sie die obere Registerkarte **Benachrichtigungshubs**, und klicken Sie dann auf den soeben erstellten Benachrichtigungshub.

	![][10]

6.  Wählen Sie die obere Registerkarte **Konfigurieren**. Geben Sie die Werte für **Geheimer Clientschlüssel** und **Paket-SID** ein, die Sie im vorigen Abschnitt vom WNS erhalten haben. Klicken Sie dann auf **Speichern**.

	![][11]

7.  Wählen Sie die obere Registerkarte **Dashboard**, und klicken Sie danach auf **Verbindungsinformationen**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

	![][12]

Der Benachrichtigungshub ist nun konfiguriert, um mit dem WNS arbeiten zu können. Außerdem besitzen Sie die Verbindungszeichenfolgen zum Registrieren der App und zum Senden von Benachrichtigungen.

Verbinden Ihrer AppVerbinden Ihrer App mit dem Benachrichtigungshub
-------------------------------------------------------------------

1.  Fügen Sie mithilfe des [WindowsAzure.Messaging.Managed NuGet-Paket](http://nuget.org/packages/WindowsAzure.Messaging.Managed/) eine Referenz zur Azure Messaging-Bibliothek für Windows Store hinzu. Klicken Sie im Visual Studio-Hauptmenü auf **Tools**, auf **Bibliothek-Paket-Manager** und danach auf **Paket-Managerkonsole**. Geben Sie dann folgenden Code in das Konsolenfenster ein:

         Install-Package WindowsAzure.Messaging.Managed

    Drücken Sie anschließend **Enter**.

2.  Öffnen Sie die Datei "App.xaml.cs", und fügen Sie die folgenden `using`-Anweisungen hinzu:

         using Windows.Networking.PushNotifications;
         using Microsoft.WindowsAzure.Messaging;
         using Windows.UI.Popups;

3.  Fügen Sie den folgenden Code zur Datei "App.xaml.cs" in der `App`-Klasse hinzu. Stellen Sie sicher, dass Sie den Platzhalter "Hub-Name" durch den Namen des Benachrichtigungshubs ersetzen, der im Portal auf der Registerkarte **Benachrichtigungshubs** angezeigt wird (wie etwa **mynotificationhub2** im vorigen Beispiel):

         private async void InitNotificationsAsync()
         {
             var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
                
             var hub = new NotificationHub("<hub name>", "<connection string with listen access>");              
             var result = await hub.RegisterNativeAsync(channel.Uri);
                
             // Zeigt die ID der Registrierung an, wenn diese erfolgreich war.
             if (result.RegistrationId != null)
             {
                 var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                 dialog.Commands.Add(new UICommand("OK"));
                 await dialog.ShowAsync();
             }

         }

    Stellen Sie sicher, dass Sie den Namen des Hubs und die Verbindungszeichenfolge **DefaultListenSharedAccessSignature** einfügen, die Sie im vorigen Abschnitt erhalten haben. Dieser Code ruft den Kanal-URI für die App vom WNS ab und registriert anschließend diesen Kanal-URI mit dem Benachrichtigungshub.

4.  Fügen Sie in der Datei "App.xaml.cs" am Anfang des **OnLaunched**-Ereignishandlers den folgenden Aufruf zu der neuen **InitNotificationsAsync**-Methode hinzu:

         InitNotificationsAsync();

    Dadurch wird gewährleistet, dass der Kanal-URI bei jedem Start der Anwendung im Benachrichtigungshub registriert wird.

5.  Drücken Sie die Taste **F5**, um die App auszuführen. Ein Dialogfenster wird mit dem Registrierungsschlüssel angezeigt.

	![][19]

Senden einer BenachrichtigungSenden einer Benachrichtigung von Ihrem Back-End aus
---------------------------------------------------------------------------------

Sie können Benachrichtigungen mit Benachrichtigungshubs von jedem beliebigen Back-End aus mithilfe der [REST-Schnittstelle](http://msdn.microsoft.com/de-de/library/windowsazure/dn223264.aspx) senden. In diesem Lernprogramm senden Sie Benachrichtigungen mit einer .NET-Konsolenanwendung und mit einem mobilen Dienst, der ein Knoten-Skript verwendet.

So senden Sie Benachrichtigungen mit einer .NET-App:

1.  Doppelklicken Sie bei in Visual Studio noch geöffneter voriger Lösung auf **Package.appxmanifest** im Projektmappen-Explorer, um diese im Visual Studio-Editor zu öffnen.

2.  Führen Sie einen Bildlauf nach unten zu **All Image Assets** aus, und klicken Sie auf **Badge Logo**. Stellen Sie unter **Benachrichtigungen** den Wert für **Toast capable** auf **Ja**:

	![][18]

	Klicken Sie im Menü **Datei** auf **Save All**.

3.  Erstellen Sie nun eine neue Visual C\#-Konsolenanwendung:

	![][13]

4.  Fügen Sie mithilfe des [WindowsAzure.ServiceBus NuGet-Pakets](http://nuget.org/packages/WindowsAzure.ServiceBus/) eine Referenz zum Windows Azure Service Bus-SDK hinzu. Klicken Sie im Visual Studio-Hauptmenü auf **Tools**, auf **Bibliothek-Paket-Manager** und danach auf **Paket-Managerkonsole**. Geben Sie dann Folgendes im Konsolenfenster ein:

         Install-Package WindowsAzure.ServiceBus

    Drücken Sie anschließend **Enter**.

5.  Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende `using`-Anweisung hinzu:

         using Microsoft.ServiceBus.Notifications;

6.  Fügen Sie in der `Programm`-Klasse die folgende Methode hinzu. Stellen Sie sicher, dass Sie den Platzhalter "Hub-Name" durch den Namen des Benachrichtigungshubs ersetzen, der im Portal auf der Registerkarte **Benachrichtigungshubs** angezeigt wird:

         private static async void SendNotificationAsync()
         {
             NotificationHubClient hub = NotificationHubClient.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
             var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
             await hub.SendWindowsNativeNotificationAsync(toast);
         }

	Stellen Sie sicher, dass Sie den Namen des Hubs und die Verbindungszeichenfolge **DefaultFullSharedAccessSignature** einfügen, die Sie im Abschnitt "Konfigurieren Ihres Benachrichtigungshubs" erhalten haben. Beachten Sie, dass es sich hierbei um die Verbindungszeichenfolge mit **Full**-Zugriff und nicht mit **Listen**-Zugriff handelt.

7.  Fügen Sie danach die folgenden Zeilen zur `Main`-Methode hinzu:

          SendNotificationAsync();
          Console.ReadLine();

8.  Drücken Sie die Taste **F5**, um die App auszuführen. Sie sollten dann eine Popupbenachrichtigung erhalten.

	![][14]

	Sie können alle möglichen Nutzlasten zu MSDN unter [toast catalog](http://msdn.microsoft.com/de-de/library/windows/apps/hh761494.aspx), [tile catalog](http://msdn.microsoft.com/de-de/library/windows/apps/hh761491.aspx) und [badge overview](http://msdn.microsoft.com/de-de/library/windows/apps/hh779719.aspx) ermitteln.

	Zum Senden einer Benachrichtigung mit Mobile Service folgen Sie den Anweisungen unter [Erste Schritte mit Mobile Services](/de-de/develop/mobile/tutorials/get-started/#create-new-service). Gehen Sie dann wie folgt vor:

1.  Melden Sie sich am [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an, und klicken Sie auf "Mobile Service".

2.  Wählen Sie oben die Registerkarte **Scheduler**.

	![][15]

3.  Erstellen Sie einen neuen geplanten Auftrag. Fügen Sie einen Namen ein, und klicken Sie dann auf **On demand**.
	
	![][16]

4.  Wenn der Auftrag erstellt ist, klicken Sie auf den Auftragsnamen. Klicken Sie anschließend in der Kopfleiste auf die Registerkarte **Skript**.

5.  Fügen Sie das folgende Skript in Ihre Scheduler-Funktion ein. Stellen Sie sicher, dass Sie die Platzhalter durch den Benachrichtigungshub-Namen und die Verbindungszeichenfolge für *DefaultFullSharedAccessSignature* ersetzen, die Sie zuvor erhalten haben. Klicken Sie nach dem Abschluss in der unteren Leiste auf **Speichern**.

         var azure = require('azure');
         var notificationHubService = azure.createNotificationHubService('<hub name>', '<connection string with full access>');
         notificationHubService.wns.sendToastText01(
             null,
             {
                 text1: 'Hello from Mobile Services!!!'
             },
             function (error) {
                 if (!error) {
                     console.warn("Notification successful");
                 }
         });

6.  Klicken Sie in der unteren Leiste auf **Einmal ausführen**. Sie sollten dann eine Popupbenachrichtigung erhalten.

Nächste Schritte
----------------

In diesem einfachen Beispiel haben Sie Übertragungsbenachrichtigungen an alle Ihre Windows-Geräte gesendet. Informationen zum Adressieren bestimmter Benutzer finden Sie in dem Lernprogramm [Benachrichtigen von Benutzern mit Benachrichtigungshubs](/de-de/manage/services/notification-hubs/notify-users-aspnet). Wenn Sie Ihre Benutzer nach Interessengruppen unterteilen möchten, lesen Sie [Verwenden von Benachrichtigungshubs zum Senden von neuesten Nachrichten](/de-de/manage/services/notification-hubs/breaking-news-dotnet). Weitere Informationen über die Verwendung von Benachrichtigungshubs finden Sie unter [Benachrichtigungshubs-Leitfaden](http://msdn.microsoft.com/de-de/library/jj927170.aspx) und [Benachrichtigungshubs-Informationen für Windows Store](http://msdn.microsoft.com/de-de/library/jj927172.aspx).

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-win8-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
[5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
[12]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-connection-strings.png
[13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
[14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
[15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-scheduler2.png
[17]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png
[18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
[19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png