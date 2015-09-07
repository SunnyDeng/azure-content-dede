<properties 
	pageTitle="Hinzufügen von Pushbenachrichtigungen zu Ihrer universellen Windows Runtime 8.1-App | Azure Mobile Apps"
	description="Erfahren Sie, wie Sie Azure Mobile App Service-Apps und Azure Notification Hubs verwenden, um Pushbenachrichtigungen an Ihre Windows-App zu senden."
	services="app-service\mobile,notification-hubs"
	documentationCenter="windows"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags 
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/14/2015"
	ms.author="glenga"/>

# Hinzufügen von Pushbenachrichtigungen zu Ihrer universellen Windows Runtime 8.1-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../../includes/app-service-mobile-selector-get-started-push-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

##Übersicht

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile App Service-Apps und Azure Notification Hubs Pushbenachrichtigungen an eine universelle Windows-Runtime 8.1-App senden. In diesem Szenario sendet das Mobile App-Back-End eine Pushbenachrichtigung an alle Windows-Apps, die beim Windows-Benachrichtigungsdienst (Windows Notification Service, WNS) registriert sind, wenn ein neues Element hinzugefügt wird.

Dieses Lernprogramm baut auf dem App Service-Schnellstart für mobile Apps auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst das Lernprogramm [Erstellen einer Windows-App](../app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md) abschließen. Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt das Pushbenachrichtigungs-Erweiterungspaket hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) (in englischer Sprache).

##Voraussetzungen

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives [Microsoft Store-Konto](http://go.microsoft.com/fwlink/p/?LinkId=280045)
* [Visual Studio Community 2013](https://go.microsoft.com/fwLink/p/?LinkID=391934).
* Schließen Sie das [Schnellstart-Lernprogramm](../app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md) ab.  



##<a name="create-gateway"></a>Erstellen eines Notification Hubs

Um einen neuen Notification Hub zur Behandlung von Pushbenachrichtigungen zu erstellen, gehen Sie wie folgt vor. Wenn Sie bereits über ein Hub in derselben Ressourcengruppe verfügen, müssen Sie diesen Abschnitt nicht durchführen.

1. Besuchen Sie das [Azure-Portal]. Klicken Sie auf **Alle durchsuchen** > **Mobile Apps** > Back-End, das Sie gerade erstellt haben. Klicken Sie auf **Einstellungen** > **Mobile** > **Push**. 

2. Führen Sie die Schritte zum Erstellen eines Benachrichtigungshubs durch. Sie müssen einen neuen Namespace erstellen, wenn in der aktuellen Ressourcengruppe keiner vorhanden ist. Klicken Sie auf **Erstellen**, nachdem Sie alle Einstellungen konfiguriert haben.

Als Nächstes verwenden Sie diesen Benachrichtigungshub, um Pushbenachrichtigungen für Ihre App zu aktivieren.

##Registrieren der App für Pushbenachrichtigungen

Bevor Sie Pushbenachrichtigungen von Azure an Ihre Windows-Apps senden können, müssen Sie Ihre App an den Windows Store übermitteln. Sie können dann das Serverprojekt zur Integration mit dem WNS konfigurieren.

1. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf das Windows Store-App-Projekt, und klicken Sie dann auf **Store** > **App mit Store verknüpfen...** 

    ![][3]
    
2. Klicken Sie im Assistenten auf **Weiter**, melden Sie sich mit Ihrem Microsoft-Konto an, geben Sie unter **App-Namen reservieren** einen Namen für Ihre App ein, und klicken Sie dann auf **Reservieren**.

3. Nachdem die App-Registrierung erfolgreich erstellt wurde, wählen Sie den Namen der neuen App aus, klicken Sie auf **Weiter** und dann auf **Zuordnen**. Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.

7. Wiederholen Sie die Schritte 1 und 3 für das Windows Phone Store-App-Projekt mithilfe der zuvor erstellten Registrierung für die Windows Store-App.

7. Navigieren Sie zum [Windows-Entwicklungscenter](https://dev.windows.com/de-DE/overview), melden Sie sich mit Ihrem Microsoft-Konto an, klicken Sie auf die neue App-Registrierung unter **Meine Apps**, und erweitern Sie dann **Dienste** > **Pushbenachrichtigungen**.

8. Klicken Sie auf der Seite **Pushbenachrichtigungen** auf **Live-Dienste-Website** unter **Microsoft Azure Mobile Services**.

9. Notieren Sie sich auf der Registerkarte **App-Einstellungen** die Werte des **geheimen Clientschlüssels** und die **Paket-SID**.

    ![][6]

    > [AZURE.IMPORTANT]Der geheime Clientschlüssel und die Paket-SID sind wichtige Sicherheitsanmeldeinformationen. Geben Sie diese Werte nicht weiter, und verteilen Sie sie nicht mit Ihrer Anwendung.

##Konfigurieren der mobilen App zum Senden von Pushanforderungen

1. Melden Sie sich beim [Azure-Portal] an, und wählen Sie **Durchsuchen** > **Mobile App** > Ihre App > **Pushbenachrichtigungsdienste** aus.

2. Geben Sie in **Windows-Benachrichtigungsdienst** den **Sicherheitsschlüssel** (geheimer Clientschlüssel) und die **Paket-SID** ein, die Sie auf der Website für die Live-Dienste erhalten haben. Klicken Sie anschließend auf **Speichern**.

Ihr Mobile-App-Back-End ist jetzt für die Zusammenarbeit mit WNS konfiguriert.

##<a id="update-service"></a>Aktualisieren des Servers zum Senden von Pushbenachrichtigungen

Nachdem die Pushbenachrichtigungen in der App aktiviert wurden, müssen Sie ihr App-Back-End aktualisieren, um Pushbenachrichtigungen senden zu können.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Serverprojekt, und klicken Sie auf **NuGet-Pakete verwalten**, und suchen Sie nach `Microsoft.Azure.NotificationHubs`. Klicken Sie anschließend auf **Installieren**. Dadurch wird die Notification Hubs-Clientbibliothek installiert.

3. Öffnen Sie **Controller** > **TodoItemController.cs** im Serverprojekt, und fügen Sie die folgenden using-Anweisungen hinzu:

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.Mobile.Server.Config;
	

2. Fügen Sie in der **PostTodoItem**-Methode den folgenden Code nach dem Aufruf von **InsertAsync** hinzu:

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
			this.Configuration.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

		// Define a WNS payload
		var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" 
                                + item.Text + @"</text></binding></visual></toast>";

        try
        {
			// Send the push notification and log the results.
            var result = await hub.SendWindowsNativeNotificationAsync(windowsToastPayload);

            // Write the success result to the logs.
            config.Services.GetTraceWriter().Info(result.State.ToString());
        }
        catch (System.Exception ex)
        {
            // Write the failure result to the logs.
            config.Services.GetTraceWriter()
                .Error(ex.Message, null, "Push.SendAsync Error");
        }

    Dieser Code weist das Benachrichtigungshub an, nach dem Einfügen eines neuen Elements eine Pushbenachrichtigung zu senden.


## <a name="publish-the-service"></a>Veröffentlichen des mobilen Back-Ends in Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##<a id="update-service"></a>Hinzufügen von Pushbenachrichtigungen zur App

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten**. 

    Daraufhin wird das Dialogfeld "NuGet-Pakete verwalten" angezeigt.

2. Suchen Sie nach dem App Service Mobile App-Client-SDK für verwaltete Objekte, und klicken Sie auf **Installieren**. Wählen Sie alle Clientprojekte in der Mappe aus, und akzeptieren Sie die Nutzungsbedingungen.

    Auf diese Weise wird ein Verweis auf die Azure Mobile Push-Bibliothek für Windows heruntergeladen, installiert und allen Clientprojekten hinzugefügt.

3. Öffnen Sie die freigegebene Projektdatei **App.xaml.cs**, und fügen Sie folgenden `using`-Anweisungen hinzu:

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

4. Fügen Sie in derselben Datei der **App**-Klasse die folgende **InitNotificationsAsync**-Methodendefinition hinzu:
    
        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            await App.MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Dieser Code ruft "ChannelURI" für die App aus WNS ab und registriert anschließend "ChannelURI" für Ihre mobile App Service-App.
    
5. Fügen Sie in der Datei **App.xaml.cs** am Anfang des **OnLaunched**-Ereignishandlers den **async**-Modifizierer zur Methodendefinition hinzu, und fügen Sie den folgenden Aufruf wie im Beispiel unten zu der neuen **InitNotificationsAsync**-Methode hinzu:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    Dadurch wird sichergestellt, dass der kurzlebige ChannelURI-Wert bei jedem Anwendungsstart registriert wird.

6. Doppelklicken Sie im Projektmappen-Explorer auf die Datei **Package.appxmanifest** der Windows Store-App, und legen Sie unter **Benachrichtigungen** die Option **Toastfähig** auf **Ja** fest.

    Klicken Sie im Menü **Datei** auf **Alle speichern**.

7. Wiederholen Sie den vorherigen Schritt für das Windows Phone Store-App-Projekt.

Ihre App kann jetzt Popupbenachrichtigungen empfangen.

##<a id="test"></a>Testen von Pushbenachrichtigungen in der App

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push-preview](../../includes/app-service-mobile-windows-universal-test-push-preview.md)]

<!-- Anchors. -->
<!-- URLs. -->
[Azure-Portal]: https://portal.azure.com/
<!-- Images. -->
[0]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-submit-win8-app.png
[1]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/notification-hub-associate-win8-app.png
[4]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-select-app-name.png
[5]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-edit-app.png
[6]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[17]: ./media/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-push-preview/mobile-services-win8-edit2-app.png

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582

<!---HONumber=August15_HO9-->