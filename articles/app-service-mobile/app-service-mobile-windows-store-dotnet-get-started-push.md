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
	ms.date="11/25/2015" 
	ms.author="glenga"/>

# Hinzufügen von Pushbenachrichtigungen zu Ihrer universellen Windows Runtime 8.1-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-push](../../includes/app-service-mobile-selector-get-started-push.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Übersicht

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile App Service-Apps und Azure Notification Hubs Pushbenachrichtigungen an eine universelle Windows-Runtime 8.1-App senden. In diesem Szenario sendet das Mobile App-Back-End eine Pushbenachrichtigung an alle Windows-Apps, die beim Windows-Benachrichtigungsdienst (Windows Notification Service, WNS) registriert sind, wenn ein neues Element hinzugefügt wird.

Dieses Lernprogramm baut auf dem App Service-Schnellstart für mobile Apps auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst das Lernprogramm [Erstellen einer Windows-App](../app-service-mobile-windows-store-dotnet-get-started.md) abschließen. Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt das Pushbenachrichtigungs-Erweiterungspaket hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) (in englischer Sprache).

##Voraussetzungen

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives [Microsoft Store-Konto](http://go.microsoft.com/fwlink/p/?LinkId=280045)
* [Visual Studio Community 2013.](https://go.microsoft.com/fwLink/p/?LinkID=391934)
* Schließen Sie das [Schnellstart-Lernprogramm](../app-service-mobile-windows-store-dotnet-get-started.md) ab.  


##<a name="create-hub"></a>Erstellen eines Notification Hubs

[AZURE.INCLUDE [app-service-mobile-create-notification-hub](../../includes/app-service-mobile-create-notification-hub.md)]

##Registrieren der App für Pushbenachrichtigungen

Bevor Sie Pushbenachrichtigungen von Azure an Ihre Windows-Apps senden können, müssen Sie Ihre App an den Windows Store übermitteln. Sie können dann das Serverprojekt zur Integration mit dem WNS konfigurieren.

[AZURE.INCLUDE [app-service-mobile-register-wns](../../includes/app-service-mobile-register-wns.md)]


##Konfigurieren des Back-Ends zum Senden von Pushbenachrichtigungen

[AZURE.INCLUDE [app-service-mobile-configure-wns](../../includes/app-service-mobile-configure-wns.md)]


##<a id="update-service"></a>Aktualisieren des Servers zum Senden von Pushbenachrichtigungen

Nachdem die Pushbenachrichtigungen in der App aktiviert wurden, müssen Sie ihr App-Back-End aktualisieren, um Pushbenachrichtigungen senden zu können.

[AZURE.INCLUDE [app-service-mobile-update-server-project-for-push-template](../../includes/app-service-mobile-update-server-project-for-push-template.md)]


## <a name="publish-the-service"></a>Veröffentlichen des mobilen Back-Ends in Azure

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]

##<a id="update-service"></a>Hinzufügen von Pushbenachrichtigungen zur App

1. Öffnen Sie die freigegebene Projektdatei **App.xaml.cs**, und fügen Sie folgenden `using`-Anweisungen hinzu:

		using System.Threading.Tasks;  
        using Windows.Networking.PushNotifications;       

2. Fügen Sie in derselben Datei der **App**-Klasse die folgende **InitNotificationsAsync**-Methodendefinition hinzu:
    
        private async Task InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();

            await MobileService.GetPush().RegisterAsync(channel.Uri);
        }
    
    Dieser Code ruft "ChannelURI" für die App aus WNS ab und registriert anschließend "ChannelURI" für Ihre mobile App Service-App.
    
3. Fügen Sie in der Datei **App.xaml.cs** am Anfang des **OnLaunched**-Ereignishandlers den **async**-Modifizierer zur Methodendefinition hinzu, und fügen Sie den folgenden Aufruf wie im Beispiel unten zu der neuen **InitNotificationsAsync**-Methode hinzu:

        protected async override void OnLaunched(LaunchActivatedEventArgs e)
        {
            await InitNotificationsAsync();

			// ...
		}

    Dadurch wird sichergestellt, dass der kurzlebige ChannelURI-Wert bei jedem Anwendungsstart registriert wird.

4. Doppelklicken Sie im Projektmappen-Explorer auf die Datei **Package.appxmanifest** der Windows Store-App, und legen Sie unter **Benachrichtigungen** die Option **Toastfähig** auf **Ja** fest.

    Klicken Sie im Menü **Datei** auf **Alle speichern**.

5. Wiederholen Sie den vorherigen Schritt für das Windows Phone Store-App-Projekt.

Ihre App kann jetzt Popupbenachrichtigungen empfangen.

##<a id="test"></a>Testen von Pushbenachrichtigungen in der App

[AZURE.INCLUDE [app-service-mobile-windows-universal-test-push](../../includes/app-service-mobile-windows-universal-test-push.md)]

##Nächste Schritte

Abschließend erfahren Sie im Lernprogramm [Senden plattformübergreifender Benachrichtigungen an einen bestimmten Benutzer](app-service-mobile-windows-store-dotnet-push-notifications-to-users.md), wie eine Pushbenachrichtigung unabhängig von einer Geräteplattform an alle Geräteregistrierungen eines bestimmten authentifizierten Benutzers gesendet wird.

##<a id="more"></a>Weitere Informationen

* Vorlagen ermöglichen Ihnen, flexibel plattformübergreifende Pushvorgänge und lokalisierte Pushvorgänge zu senden. In [Verwenden des verwalteten Clients für Azure Mobile Apps](app-service-mobile-dotnet-how-to-use-client-library.md#how-to-register-push-templates-to-send-cross-platform-notifications) wird erklärt, wie Sie Vorlagen registrieren.
* Mit Tags können Sie für segmentierte Kunden Pushvorgänge auswählen. In [Arbeiten Sie mit der .NET-Back-End-Server-SDK für Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md#how-to-add-tags-to-a-device-installation-to-enable-push-to-tags) wird erklärt, wie Sie einer Geräteinstallation Tags hinzufügen.

<!-- Anchors. -->

<!-- URLs. -->
[Azure Portal]: https://portal.azure.com/

<!-- Images. -->

<!---HONumber=AcomDC_1203_2015-->