<properties 
	pageTitle="Hinzufügen von Pushbenachrichtigungen zu Ihrer universellen Windows-App mit Azure App Service" 
	description="Erfahren Sie, wie Azure App Service zum Senden von Pushbenachrichtigungen an Ihre universelle Windows-App verwendet wird." 
	services="app-service\mobile" 
	documentationCenter="windows" 
	authors="yuaxu" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/16/2015" 
	ms.author="yuaxu"/>

# Hinzufügen von Pushbenachrichtigungen zu Ihrer Windows Store-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push-preview](../includes/app-service-mobile-selector-get-started-push-preview.md)]

In diesem Thema wird das Senden von Pushbenachrichtigungen an eine universelle Windows-App von einem .NET-Back-End mit Azure App Service veranschaulicht. Nach Abschluss des Lernprogramms senden Sie Pushbenachrichtigungen von Ihrem .NET-Back-End an alle registrierten universellen Windows-Apps, wenn Einträge hinzugefügt werden.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Registrieren der App für Pushbenachrichtigungen](#register)
2. [Konfigurieren](#configure)
3. [Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen](#update-service)
4. [Hinzufügen von Pushbenachrichtigungen zur App](#add-push)
5. [Testen von Pushbenachrichtigungen in Ihrer App](#test)

Dieses Lernprogramm baut auf dem App Service-Schnellstart für mobile Apps auf. Sie müssen zuerst [Erste Schritte mit mobilen App Service-Apps] ausführen, bevor Sie mit diesem Lernprogramm beginnen.

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives [Microsoft Store-Konto](http://go.microsoft.com/fwlink/p/?LinkId=280045).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Community 2013</a>.

##<a id="register"></a>Registrieren der App für Pushbenachrichtigungen

Um mit Azure App Service Pushbenachrichtigungen an universelle Windows-Apps senden zu können, müssen Sie Ihre App im Windows Store bereitstellen. Anschließend müssen Sie Ihre mobile App zur Integration in WNS mit Anmeldeinformationen für den Pushbenachrichtigungsdienst konfigurieren.

1. Falls Sie Ihre App noch nicht registriert haben, navigieren Sie im Dev Center für Windows Store-Apps zu <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">App-Seite senden</a>, melden Sie sich mit Ihrem Microsoft-Konto an, und klicken Sie dann auf **App-Name**.

    ![][0]

2. Geben Sie unter **App-Name** einen Namen für Ihre App ein, klicken Sie auf **App-Name reservieren** und dann auf **Speichern**.

    ![][1]

    Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre Anwendung erstellt.

4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Windows Store-App-Projekt, klicken Sie auf **Store**, und klicken Sie dann auf **App mit Store verknüpfen...**. 

    ![][3]

    Der ****Assistent zum Verknüpfen der App mit dem Windows Store wird angezeigt.

5. Klicken Sie im Assistenten auf **Anmelden**. Melden Sie sich dann mit Ihrem Microsoft-Konto an.

6. Klicken Sie auf die App, die Sie in Schritt 2 registriert haben. Klicken Sie auf **Weiter** und dann auf **Zuordnen**.

    ![][4]

    Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt. 

7. (Optional) Wiederholen Sie die Schritte 4-6 für das Windows Phone Store-App-Projekt.  

7. Klicken Sie in der Seite von Windows Dev Center für Ihre neue App auf **Dienste**. 

    ![][5] 

8. Klicken Sie auf der Seite **Dienste** auf **Live-Dienste-Website** unter **Microsoft Azure Mobile Services**.

    ![][17]

9. Notieren Sie sich auf der Registerkarte **App-Einstellungen** die Werte von **Geheimer Clientschlüssel** und **Paket-Sicherheits-ID (SID)**. 

    ![][6]

    > [AZURE.NOTE] **Sicherheitshinweis**
    Der geheime Clientschlüssel und die Paket-SID sind wichtige Sicherheitsanmeldeinformationen. Geben Sie diese Werte nicht weiter, und verteilen Sie sie nicht mit Ihrer Anwendung.

##<a id="configure"></a>Konfigurieren der mobilen App zum Senden von Pushanforderungen

1. Melden Sie sich am [Azure-Vorschauportal] an, wählen Sie **Durchsuchen**, **Mobile App**, und klicken Sie dann auf Ihre App. Klicken Sie auf "Pushbenachrichtigungsdienste".

2. Geben Sie im Windows-Benachrichtigungsdienst Ihren Wert für **Geheimer Clientschlüssel** und **Paket-Sicherheits-ID (SID)**, und speichern Sie die Änderungen.

Ihre mobile App Service-App ist jetzt für die Zusammenarbeit mit WNS konfiguriert.

<!-- URLs. -->
[Azure-Vorschauportal]: https://portal.azure.com/

##<a id="update-service"></a>Aktualisieren des Diensts zum Senden von Pushbenachrichtigungen

Nachdem die Pushbenachrichtigungen in der App aktiviert wurden, müssen Sie ihr App-Back-End aktualisieren, um Pushbenachrichtigungen senden zu können. 

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten**.

2. Suchen Sie nach **Microsoft.Azure.NotificationHubs**, und klicken Sie für alle Projekte in der Lösung auf **Installieren**.

3. Erweitern Sie im Projektmappen-Explorer von Visual Studio den Ordner **Controller** im Projekt für das mobile Back-End. Öffnen Sie "TodoItemController.cs". Fügen Sie am Anfang der Datei die folgenden `using`-Anweisungen hinzu:

        using System.Collections.Generic;
        using Microsoft.Azure.NotificationHubs;

4. Fügen Sie der `PostTodoItem`-Methode nach dem Aufruf von **InsertAsync** den folgenden Codeausschnitt hinzu:  

        // get Notification Hubs credentials associated with this Mobile App
        string notificationHubName = this.Services.Settings.NotificationHubName;
        string notificationHubConnection = this.Services.Settings.Connections[ServiceSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // connect to notification hub
        NotificationHubClient Hub = NotificationHubClient.CreateClientFromConnectionString(notificationHubConnection, notificationHubName)

        // windows payload
        var windowsToastPayload = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + item.Text + @"</text></binding></visual></toast>";

        try
        {
            await Hub.SendWindowsNativeNotificationAsync(windowsToastPayload);
        }
        catch (System.Exception ex)
        {
            throw ex;
        }

    Dieser Code weist den mit dieser mobilen App verknüpften Notification Hub an, nach dem Einfügen eines Todo-Elements eine Pushbenachrichtigung zu senden.


<h2><a name="publish-the-service"></a>Veröffentlichen des mobilen Back-Ends in Azure</h2>

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service-preview](../includes/app-service-mobile-dotnet-backend-publish-service-preview.md)]

##<a id="update-service"></a>Hinzufügen von Pushbenachrichtigungen zur App

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten**. 

    Daraufhin wird das Dialogfeld "NuGet-Pakete verwalten" angezeigt.

2. Suchen Sie nach dem App Service Mobile App-Client-SDK für verwaltete Objekte, und klicken Sie auf **Installieren**. Wählen Sie alle Projekte in der Lösung aus, und akzeptieren Sie die Nutzungsbedingungen. 

    Auf diese Weise wird ein Verweis auf die Azure Mobile Push-Bibliothek für Windows heruntergeladen, installiert und allen Projekten hinzugefügt. 

3. Öffnen Sie die Projektdatei **App.xaml.cs**, und fügen Sie die folgenden `using`-Anweisungen hinzu:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.MobileServices;

    In einem universellen Projekt befindet sich diese Datei im Ordner "<project_name>.Shared".

4. Fügen Sie in derselben Datei der **App**-Klasse die folgende **InitNotificationsAsync**-Methodendefinition hinzu:
    
        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            
            var result = MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Dieser Code ruft "ChannelURI" für die App aus WNS ab und registriert anschließend "ChannelURI" für Ihre mobile App Service-App.
    
5. Fügen Sie zu Beginn des **OnLaunched**-Ereignishandlers in **App.xaml.cs** den folgenden Aufruf der neuen **InitNotificationsAsync**-Methode hinzu:

        InitNotificationsAsync();

    Dadurch wird sichergestellt, dass der kurzlebige ChannelURI-Wert bei jedem Anwendungsstart registriert wird.

6. Doppelklicken Sie im Projektmappen-Explorer auf die Datei **Package.appxmanifest** der Windows Store-App, und legen Sie unter **Benachrichtigungen** die Option **Toastfähig** auf **Ja** fest.

    Klicken Sie im Menü **Datei** auf **Alle speichern**.

7. (Optional) Wiederholen Sie den vorherigen Schritt für das Windows Phone Store-App-Projekt.

8. Drücken Sie die Taste **F5**, um die Apps auszuführen.

Ihre App kann jetzt Popupbenachrichtigungen empfangen.

##<a id="test"></a>Testen von Pushbenachrichtigungen in Ihrer App

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-windows-universal-test-push-preview](../includes/app-service-mobile-dotnet-backend-windows-universal-test-push-preview.md)]

<!-- Anchors. -->

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
[3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
[4]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
[5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
[6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
[7]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
[17]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png

<!-- URLs. -->
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582

<!--HONumber=49-->