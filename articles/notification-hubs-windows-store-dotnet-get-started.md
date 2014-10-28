<properties linkid="develop-notificationhubs-tutorials-get-started-windowsdotnet" urlDisplayName="Get started with notification hubs" pageTitle="Get started with Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Getting Started with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga"></tags>

# Erste Schritte mit Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal" class="current">Windows Universal</a><a href="/de-de/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/de-de/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/de-de/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

In diesem Thema wird gezeigt, wie Sie mit Azure Notification Hubs Pushbenachrichtigungen an eine Windows Store- oder Windows Phone 8.1-Anwendung (kein Silverlight) senden können. Wenn Ihr Ziel Windows Phone 8.1 Silverlight ist, konsultieren Sie die [Windows Phone][1]-Version.
In diesem Lernprogramm erstellen Sie eine leere Windows Store-App, die Pushbenachrichtigungen über den Windows-Pushbenachrichtigungsdienst (WNS) erhält. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Registrieren der App für Pushbenachrichtigungen][Registrieren der App für Pushbenachrichtigungen]
2.  [Konfigurieren Ihres Notification Hub][Konfigurieren Ihres Notification Hub]
3.  [Verbinden Ihrer App mit dem Notification Hub][Verbinden Ihrer App mit dem Notification Hub]
4.  [Senden von Benachrichtigungen von Ihrem Back-End aus][Senden von Benachrichtigungen von Ihrem Back-End aus]

In diesem Lernprogramm wird ein einfaches Übertragungsszenario mit Notification Hubs dargestellt. Bearbeiten Sie auch das nachfolgende Lernprogramm, um mehr über die Verwendung von Notification Hubs zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren. Für dieses Lernprogramm ist Folgendes erforderlich:

-   Microsoft Visual Studio Express 2013 für Windows with Update 2
    Diese Visual Studio-Version ist zum Erstellen eines Projekts für eine universelle App erforderlich. Falls Sie lediglich eine Windows Store-App erstellen möchten, benötigen Sie Visual Studio 2012 Express für Windows 8.

-   Ein aktives Windows Store-Konto

-   Ein aktives Azure-Konto.
    Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hub-Lernprogramme für Windows Store-Apps.

## <a name="register"></a>Registrieren der App für den Windows Store

Sie müssen die App an den Windows Store übermitteln, um Pushbenachrichtigungen von Mobile Services an Windows Store-Apps senden zu können. Sie müssen anschließend den Notification Hub zur Integration in den WNS konfigurieren.

1.  Falls Sie die App noch nicht registriert haben, navigieren Sie im Entwicklungscenter für Windows Store-Apps zu [App-Seite senden][App-Seite senden], melden Sie sich mit Ihrem Microsoft-Konto an, und klicken Sie dann auf **App-Name**.

    ![][]

2.  Geben Sie einen Namen für Ihre Anwendung unter **Anwendungsname** ein, klicken Sie auf **Anwendungsname reservieren** und dann auf **Speichern**.

    ![][2]

    Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre Anwendung erstellt.

3.  Erstellen Sie in Visual Studio ein neues Visual C# Store-App-Projekt mit der Vorlage **Leere App**.

    ![][3]

4.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Windows Store-App-Projekt, klicken Sie auf **Store**, und klicken Sie dann auf **App mit Store verknüpfen**.

    ![][4]

    Der Assistent zum Verknüpfen der App mit dem Windows Store wird angezeigt.

5.  Klicken Sie im Assistenten auf **Anmelden**. Melden Sie sich dann mit Ihrem Microsoft-Konto an.

6.  Klicken Sie auf die App, die Sie in Schritt 2 registriert haben. Klicken Sie auf **Weiter** und dann auf **Zuordnen**.

    ![][5]

    Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt.

7.  (Optional) Wiederholen Sie die Schritte 4-6 für das Windows Phone Store-App-Projekt.

8.  Klicken Sie auf der Windows-Entwicklungscenter-Seite für Ihre neue Anwendung auf **Dienste**.

    ![][6]

9.  Klicken Sie auf der Seite **Dienste** auf **Live-Dienste-Website** unter **Microsoft Azure Mobile Services**.

    ![][7]

10. Notieren Sie sich auf der Registerkarte **App-Einstellungen** die Werte von **Geheimer Clientschlüssel** und **Paket-Sicherheits-ID (SID)**.

    ![][8]

    <div class="dev-callout"><b>Sicherheitshinweis</b>
<p>Der geheime Clientschl&uuml;ssel und die Paket-SID sind wichtige Sicherheitsanmeldeinformationen. Geben Sie diese Werte nicht weiter, und verteilen Sie sie nicht mit Ihrer Anwendung.</p>
</div>

## <a name="configure-hub"></a>Konfigurieren Ihres Notification Hub

1.  Melden Sie sich am [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an, und klicken Sie unten auf dem Bildschirm auf **NEU**.

2.  Klicken Sie auf **App-Dienste**, **Service Bus**, **Notification Hub** und zuletzt auf **Schnellerfassung**.

    ![][9]

3.  Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Neuen Notification Hub erstellen**.

    ![][10]

4.  Klicken Sie auf den soeben erstellten Namespace (für gewöhnlich ***Name des Notification Hubs*-ns**) und anschließend oben auf die Registerkarte **Konfigurieren**.

    ![][11]

5.  Wählen Sie die obere Registerkarte **Notification Hubs**, und klicken Sie dann auf den soeben erstellten Notification Hub.

    ![][12]

6.  Wählen Sie die obere Registerkarte **Konfigurieren**. Geben Sie die Werte für **Geheimer Clientschlüssel** und **Paket-SID** ein, die Sie im vorigen Abschnitt vom WNS erhalten haben. Klicken Sie dann auf **Speichern**.

    ![][13]

7.  Wählen Sie die obere Registerkarte **Dashboard**, und klicken Sie danach auf **Verbindungsinformationen**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

    ![][14]

Der Notification Hub ist nun konfiguriert, um mit dem WNS arbeiten zu können. Außerdem besitzen Sie die Verbindungszeichenfolgen zum Registrieren der App und zum Senden von Benachrichtigungen.

## <a name="connecting-app"></a>Verbinden Ihrer App mit dem Notification Hub

1.  Klicken Sie in Visual Studio mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten**.

    Daraufhin wird das Dialogfeld "NuGet-Pakete verwalten" angezeigt.

2.  Suchen Sie nach `WindowsAzure.Messaging.Managed`, klicken Sie auf **Installieren**, wählen Sie alle Projekte in der Projektmappe aus, und akzeptieren Sie die Nutzungsbedingungen.

    ![][15]

    Damit wird ein Verweis in allen Projekten auf die Azure Messaging-Bibliothek für Windows mit dem [WindowsAzure.Messaging.Managed NuGet-Paket][WindowsAzure.Messaging.Managed NuGet-Paket] heruntergeladen, installiert und hinzugefügt.

3.  Öffnen Sie die Projektdatei "App.xaml.cs", und fügen Sie die folgenden `using`-Anweisungen hinzu:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
        using Windows.UI.Popups;

    In einem universellen Projekt befindet sich diese Datei im Ordner `<project_name>.Shared`.

4.  Fügen Sie auch in der Datei "App.xaml.cs" der **App**-Klasse folgende **InitNotificationsAsync**-Methodendefinition hinzu:

        private async void InitNotificationsAsync()
        {
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            var hub = new NotificationHub("<hub name>", "<connection string with listen access>");              
            var result = await hub.RegisterNativeAsync(channel.Uri);

            // Displays the registration ID so you know it was successful
            if (result.RegistrationId != null)
            {
                var dialog = new MessageDialog("Registration successful: " + result.RegistrationId);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            }

        }

    Dieser Code ruft den Kanal-URI für die App vom WNS ab und registriert anschließend diesen Kanal-URI mit dem Notification Hub.

    > [WACOM.NOTE]Stellen Sie sicher, dass Sie den Platzhalter "hub name" durch den Namen des Notification Hubs ersetzen, der im Portal auf der Registerkarte **Notification Hubs** angezeigt wird (wie etwa **mynotificationhub2** im vorigen Beispiel). Ersetzen Sie außerdem den Platzhalter für die Verbindungszeichenfolge durch die Verbindungszeichenfolge **DefaultListenSharedAccessSignature**, die Sie im vorherigen Abschnitt erhalten haben.

5.  Fügen Sie in der Datei "App.xaml.cs" am Anfang des **OnLaunched**-Ereignishandlers den folgenden Aufruf zu der neuen **InitNotificationsAsync**-Methode hinzu:

        InitNotificationsAsync();

    Dadurch wird gewährleistet, dass der Kanal-URI bei jedem Start der Anwendung im Notification Hub registriert wird.

6.  Doppelklicken Sie im Projektmappen-Explorer auf die Datei **Package.appxmanifest** der Windows Store-App, und legen Sie unter **Benachrichtigungen** die Option **Toastfähig** auf **Ja** fest:

    ![][16]

    Klicken Sie im Menü **Datei** auf **Alle speichern**.

7.  (Optional) Wiederholen Sie den vorherigen Schritt für das Windows Phone Store-App-Projekt.

8.  Drücken Sie **F5**, um die App auszuführen. Ein Dialogfenster wird mit dem Registrierungsschlüssel angezeigt.

    ![][17]

9.  (Optional) Wiederholen Sie den vorherigen Schritt, um das andere Projekt auszuführen.

Ihre App kann jetzt Popupbenachrichtigungen empfangen.

## <a name="send"></a>Senden von Benachrichtigungen vom Back-End aus

Sie können Benachrichtigungen mithilfe von Notification Hubs von jedem beliebigen Back-End aus über die [REST-Schnittstelle][REST-Schnittstelle] senden. In diesem Lernprogramm senden Sie Benachrichtigungen mit einer .NET-Konsolenanwendung. Ein Beispiel zum Senden von Benachrichtigungen von einem mit Notification Hubs integrierten Azure Mobile Services-Back-End aus finden Sie unter **Erste Schritte mit Pushbenachrichtigungen in Mobile Services** ([.NET-Back-End][.NET-Back-End] | [JavaScript-Back-End][.NET-Back-End]). Ein Beispiel zum Senden von Benachrichtigungen über die REST-APIs finden Sie unter **Verwenden von Notification Hubs von Java/PHP** ([Java][Java] | [PHP][PHP]).

1.  Klicken Sie mit der rechten Maustaste auf die Projektmappe, wählen Sie **Hinzufügen** und **Neues Projekt...** aus, und klicken Sie dann unter **Visual C#** auf **Windows**, **Konsolenanwendung** und **OK**.

    ![][18]

    Damit wird der Projektmappe eine neue Visual C#-Konsolenanwendung hinzugefügt. Sie können dies auch in einer separaten Projektmappe durchführen.

2.  Klicken Sie mit der rechten Maustaste auf **Extras**, und klicken Sie dann auf **Bibliothekspaket-Manager** und **Paket-Manager-Konsole**.

    Daraufhin wird die Paket-Manager-Konsole angezeigt.

3.  Stellen Sie im Konsolenfenster das **Standardprojekt** auf das neue Konsolenanwendungsprojekt ein, und führen Sie dann in diesem Fenster den folgenden Befehl aus:

        Install-Package WindowsAzure.ServiceBus

    Damit wird ein Verweis auf das Azure Service Bus-SDK mit dem [WindowsAzure.ServiceBus NuGet-Paket][WindowsAzure.ServiceBus NuGet-Paket] hinzugefügt.

4.  Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende `using`-Anweisung hinzu:

        using Microsoft.ServiceBus.Notifications;

5.  Fügen Sie in der Klasse **Program** folgende Methode hinzu:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
                .CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

    Stellen Sie sicher, dass Sie den Platzhalter "hub name" durch den Namen des Notification Hub ersetzen, der im Portal auf der Registerkarte **Notification Hubs** angezeigt wird. Ersetzen Sie außerdem den Platzhalter für die Verbindungszeichenfolge durch die Verbindungszeichenfolge **DefaultFullSharedAccessSignature**, die Sie im Abschnitt "Konfigurieren Ihres Notification Hub" erhalten haben.

    > [WACOM.NOTE]Stellen Sie sicher, dass Sie die Verbindungszeichenfolge mit Vollzugriff (**Full**) verwenden, nicht mit Lauschzugriff (**Listen**). Die Verbindungszeichenfolge mit Lauschzugriff verfügt nicht über die Berechtigungen zum Senden von Benachrichtigungen.

6.  Fügen Sie danach die folgenden Zeilen zur **Main**-Methode hinzu:

         SendNotificationAsync();
         Console.ReadLine();

7.  Die Konsolenanwendung muss als Startprojekt festgelegt sein. Drücken Sie dann **F5**, um die Anwendung auszuführen.

    ![][19]

    Sie erhalten eine Popupbenachrichtigung auf allen registrierten Geräten. Durch Klicken oder Tippen auf das Popupbanner wird die App geladen.

Sie finden alle unterstützten Nutzlasten auf MSDN in den Themen [Toast-Katalog][Toast-Katalog], [Kachelkatalog][Kachelkatalog] und [Übersicht über Signale][Übersicht über Signale].

## <a name="next-steps"> </a>Nächste Schritte

In diesem einfachen Beispiel haben Sie Übertragungsbenachrichtigungen an alle Ihre Windows-Geräte gesendet. Informationen zum Adressieren bestimmter Benutzer finden Sie in dem Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs][Benachrichtigen von Benutzern mit Notification Hubs]. Wenn Sie Ihre Benutzer nach Interessengruppen unterteilen möchten, lesen Sie [Verwenden von Notification Hubs zum Senden von neuesten Nachrichten][Verwenden von Notification Hubs zum Senden von neuesten Nachrichten]. Weitere Informationen zur Verwendung von Notification Hubs finden Sie im [Notification Hubs-Leitfaden][Notification Hubs-Leitfaden].

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Universal]: /de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/ "Windows Universal"
  [Windows Phone]: /de-de/documentation/articles/notification-hubs-windows-phone-get-started/ "Windows Phone"
  [iOS]: /de-de/documentation/articles/notification-hubs-ios-get-started/ "iOS"
  [Android]: /de-de/documentation/articles/notification-hubs-android-get-started/ "Android"
  [Kindle]: /de-de/documentation/articles/notification-hubs-kindle-get-started/ "Kindle"
  [Xamarin.iOS]: /de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/ "Xamarin.iOS"
  [Xamarin.Android]: /de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/ "Xamarin.Android"
  [1]: /de-de/documentation/articles/notification-hubs-windows-phone-get-started/
  [Registrieren der App für Pushbenachrichtigungen]: #register
  [Konfigurieren Ihres Notification Hub]: #configure-hub
  [Verbinden Ihrer App mit dem Notification Hub]: #connecting-app
  [Senden von Benachrichtigungen von Ihrem Back-End aus]: #send
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F
  [App-Seite senden]: http://go.microsoft.com/fwlink/p/?LinkID=266582
  []: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
  [2]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
  [3]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
  [4]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-associate-win8-app.png
  [5]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-select-app-name.png
  [6]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit-app.png
  [7]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-edit2-app.png
  [8]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-push-auth.png
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [9]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal.png
  [10]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-from-portal2.png
  [11]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal.png
  [12]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-select-from-portal2.png
  [13]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-configure-wns.png
  [14]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-connection-strings.png
  [15]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png
  [WindowsAzure.Messaging.Managed NuGet-Paket]: http://nuget.org/packages/WindowsAzure.Messaging.Managed/
  [16]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-win8-app-toast.png
  [17]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-reg.png
  [REST-Schnittstelle]: http://msdn.microsoft.com/de-de/library/windowsazure/dn223264.aspx
  [.NET-Back-End]: /de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/
  [Java]: /de-de/documentation/articles/notification-hubs-java-backend-how-to/
  [PHP]: /de-de/documentation/articles/notification-hubs-php-backend-how-to/
  [18]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-console-app.png
  [WindowsAzure.ServiceBus NuGet-Paket]: http://nuget.org/packages/WindowsAzure.ServiceBus/
  [19]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-toast.png
  [Toast-Katalog]: http://msdn.microsoft.com/de-de/library/windows/apps/hh761494.aspx
  [Kachelkatalog]: http://msdn.microsoft.com/de-de/library/windows/apps/hh761491.aspx
  [Übersicht über Signale]: http://msdn.microsoft.com/de-de/library/windows/apps/hh779719.aspx
  [Benachrichtigen von Benutzern mit Notification Hubs]: /de-de/manage/services/notification-hubs/notify-users-aspnet
  [Verwenden von Notification Hubs zum Senden von neuesten Nachrichten]: /de-de/manage/services/notification-hubs/breaking-news-dotnet
  [Notification Hubs-Leitfaden]: http://msdn.microsoft.com/de-de/library/jj927170.aspx
