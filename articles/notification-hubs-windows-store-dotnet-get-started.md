<properties urlDisplayName="Get started with notification hubs" pageTitle="Erste Schritte mit Azure Notification Hubs" metaKeywords="" description="Erfahren Sie mehr über die Verwendung von Azure Notification Hubs zum Senden von Pushbenachrichtigungen." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Getting Started with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />

# Erste Schritte mit Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal" class="current">Windows Universal</a><a href="/de-de/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone">Windows Phone</a><a href="/de-de/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/de-de/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/de-de/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/de-de/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

In diesem Thema wird gezeigt, wie Sie mit Azure Notification Hubs Pushbenachrichtigungen an eine Windows Store- oder Windows Phone 8.1-Anwendung (kein Silverlight) senden können. Wenn Ihr Ziel Windows Phone 8.1 Silverlight ist, konsultieren Sie den Abschnitt zur[Windows Phone](/de-de/documentation/articles/notification-hubs-windows-phone-get-started/)-Version. 
In diesem Lernprogramm erstellen Sie eine leere Windows Store-App, die Pushbenachrichtigungen mithilfe des Windows-Pushbenachrichtigungsdiensts (WNS) empfängt. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Registrieren der App für Pushbenachrichtigungen]
2. [Konfigurieren Ihres Notification Hub]
3. [Verbinden Ihrer App mit dem Notification Hub]
4. [Senden von Benachrichtigungen von Ihrem Back-End aus]

In diesem Lernprogramm wird ein einfaches Übertragungsszenario mit Notification Hubs dargestellt. Bearbeiten Sie auch das nachfolgende Lernprogramm, um mehr über die Verwendung von Notification Hubs zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren. Für dieses Lernprogramm ist Folgendes erforderlich:

+ Microsoft Visual Studio Express 2013 für Windows with Update 2<br/>Diese Visual Studio-Version ist zum Erstellen eines Projekts für eine universelle App erforderlich. Falls Sie lediglich eine Windows Store-App erstellen möchten, benötigen Sie Visual Studio 2012 Express für Windows 8.

+ Ein aktives Windows Store-Konto

+ Ein aktives Azure-Konto. <br/>Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>.

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hub-Lernprogramme für Windows Store-Apps. 

##<a name="register"></a>Registrieren der App für den Windows Store

Sie müssen die App an den Windows Store übermitteln, um Pushbenachrichtigungen von Mobile Services an Windows Store-Apps senden zu können. Sie müssen anschließend den Notification Hub zur Integration in den WNS konfigurieren.

1. Falls Sie Ihre Anwendung noch nicht registriert haben, navigieren Sie im Entwicklungscenter für Windows Store-Anwendungen zu <a href="http://go.microsoft.com/fwlink/p/?LinkID=266582" target="_blank">Anwendungsseite senden</a>, melden Sie sich mit Ihrem Microsoft-Konto an, und klicken Sie dann auf **Anwendungsname**.

   	![][0]

2. Geben Sie einen Namen für Ihre Anwendung unter **Anwendungsname** ein, klicken Sie auf **Anwendungsname reservieren** und dann auf **Speichern**.

   	![][1]

   	Auf diese Weise wird eine neue Windows Store-Registrierung für Ihre Anwendung erstellt.

3. Erstellen Sie in Visual Studio ein neues Visual C# Store App-Projekt mit der Vorlage **Leere App**.

   	![][2]

4. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Windows Store-App-Projekt, klicken Sie auf **Store**, und klicken Sie dann auf **App mit Store verknüpfen**. 

   	![][3]

   	Der Assistent zum **Verknüpfen der App mit dem Windows Store** wird angezeigt.

5. Klicken Sie im Assistenten auf **Anmelden**. Melden Sie sich dann mit Ihrem Microsoft-Konto an.

6. Wählen Sie die Anwendung aus, die Sie in Schritt 2 registriert haben, klicken Sie auf **Weiter** und dann auf **Zuordnen**.

   	![][4]

   	Auf diese Weise werden die erforderlichen Windows Store-Registrierungsinformationen zum Anwendungsmanifest hinzugefügt. 

7. (Optional) Wiederholen Sie die Schritte 4-6 für das Windows Phone Store-App-Projekt.  

7. Klicken Sie auf der Windows-Entwicklungscenter-Seite für Ihre neue Anwendung auf**Dienste**. 

   	![][5] 

8. Klicken Sie auf der Seite **Dienste** auf **Live-Dienste.Website** unter **Microsoft Azure Mobile Services**.

   	![][17]

9. Notieren Sie sich auf der Registerkarte **App-Einstellungen** die Werte von **Geheimer Clientschlüssel** und **Paket-Sicherheits-ID (SID)**. 

   	![][6]

 	<div class="dev-callout"><b>Sicherheitshinweis</b>
	<p>Der geheime Clientschlüssel und die Paket-SID sind wichtige Sicherheitsanmeldeinformationen. Geben Sie diese Werte nicht weiter, und verteilen Sie sie nicht mit Ihrer Anwendung.</p>
    </div>

##<a name="configure-hub"></a>Konfigurieren Ihres Notification Hub

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie im unteren Teil des Bildschirms auf **NEW**.

2. Klicken Sie auf **App-Dienste** dann auf **Servicebus** dann auf **Benachrichtigungshub** und dann auf **Schnellerfassung**.

   	![][7]

3. Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Create a new Notification Hub** (Neuen Notification Hub erstellen).

   	![][8]

4. Klicken Sie auf den gerade erstellten Namespace (normalerweise ***Name des Notification Hub*-ns**), und klicken Sie dann oben auf **Configure**.

   	![][9]

5. Klicken Sie oben auf die Registerkarte **Notification Hubs** und dann auf den soeben erstellten Notification Hub.

   	![][10]

6. Wählen Sie die obere Registerkarte **Konfigurieren**. Geben Sie die Werte für **Geheimer Clientschlüssel** und **Paket-SID** ein, die Sie im vorigen Abschnitt vom WNS erhalten haben. Klicken Sie dann auf **Speichern**.

   	![][11]

7. Klicken Sie oben auf die Registerkarte **Dashboard**, und klicken Sie danach auf **Connection Information**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

   	![][12]

Der Notification Hub ist nun konfiguriert, um mit dem WNS arbeiten zu können. Außerdem besitzen Sie die Verbindungszeichenfolgen zum Registrieren der App und zum Senden von Benachrichtigungen.

##<a name="connecting-app"></a>Verbinden Ihrer App mit dem Notification Hub

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten**. 

	Daraufhin wird das Dialogfeld "NuGet-Pakete verwalten" angezeigt.

2. Suchen Sie nach `WindowsAzure.Messaging.Managed`, klicken Sie auf **Installieren**, wählen Sie alle Projekte in der Projektmappe aus, und akzeptieren Sie die Nutzungsbedingungen. 

	![][20]

	T	Damit wird ein Verweis in allen Projekten auf die Azure Messaging-Bibliothek für Windows mit dem <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet package</a> heruntergeladen, installiert und hinzugefügt. 

3. Öffnen Sie die Projektdatei "App.xaml.cs", und fügen Sie die folgenden "using"-Anweisungen hinzu:

        using Windows.Networking.PushNotifications;
        using Microsoft.WindowsAzure.Messaging;
		using Windows.UI.Popups;

	In einem universellen Projekt befindet sich diese Datei im Ordner `<project_name>.Shared`.

4. Fügen Sie auch in der Datei "App.xaml.cs" der **App**-Klasse folgende **InitNotificationsAsync**-Methodendefinition hinzu:
	
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

    >[WACOM.NOTE]Stellen Sie sicher, dass Sie den Platzhalter "Hub-Name" durch den Namen des Benachrichtigungshubs ersetzen, der im Portal auf der Registerkarte **Notification Hubs** angezeigt wird (wie etwa **mynotificationhub2** im vorigen Beispiel). Ersetzen Sie außerdem den Platzhalter für die Verbindungszeichenfolge durch die Verbindungszeichenfolge **DefaultListenSharedAccessSignature**, die Sie im vorherigen Abschnitt erhalten haben.
    
5. Fügen Sie in der Datei "App.xaml.cs" am Anfang des **OnLaunched**-Ereignishandlers den folgenden Aufruf der neuen **InitNotificationsAsync**-Methode hinzu:

        InitNotificationsAsync();

    Dadurch wird gewährleistet, dass der Kanal-URI bei jedem Start der Anwendung im Notification Hub registriert wird.

6. Doppelklicken Sie im Projektmappen-Explorer auf die Datei **Package.appxmanifest** der Windows Store-App, und legen Sie unter **Benachrichtigungen** die Option **Toastfähig** auf **Ja** fest:

   	![][18]

   	Klicken Sie im Menü **Datei** auf **Alle Speichern**.

7. (Optional) Wiederholen Sie den vorherigen Schritt für das Windows Phone Store-App-Projekt.

8. Drücken Sie die Taste **F5**, um die App auszuführen. Ein Dialogfenster wird mit dem Registrierungsschlüssel angezeigt.
   
   	![][19]

9. (Optional) Wiederholen Sie den vorherigen Schritt, um das andere Projekt auszuführen.

Ihre App kann jetzt Popupbenachrichtigungen empfangen.

##<a name="send"></a>Senden von Benachrichtigungen vom Back-End aus

Sie können Benachrichtigungen mithilfe von Notification Hubs von jedem beliebigen Back-End aus über die <a href="http://msdn.microsoft.com/de-de/library/windowsazure/dn223264.aspx">REST-Schnittstelle</a> senden. In diesem Lernprogramm senden Sie Benachrichtigungen mit einer .NET-Konsolenanwendung. Ein Beispiel zum Senden von Benachrichtigungen von einem mit Notification Hubs integrierten Azure Mobile Services-Back-End aus finden Sie unter **Erste Schritte mit Pushbenachrichtigungen in Mobile Services** ([.NET-Back-End](/de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/) | [JavaScript-Back-End](/de-de/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-get-started-push/)).  Ein Beispiel zum Senden von Benachrichtigungen über die REST-APIs finden Sie unter **Verwenden von Notification Hubs von Java/PHP** ([Java](/de-de/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/de-de/documentation/articles/notification-hubs-php-backend-how-to/)).

1. Klicken Sie mit der rechten Maustaste auf die Projektmappe, wählen Sie**Hinzufügen** und **Neues Projekt...** aus, und klicken Sie dann unter **Visual C#** auf **Windows** und auf **Konsolenanwendung**. Klicken Sie dann auf **OK**. 

   	![][13]

	Damit wird der Projektmappe eine neue Visual C#-Konsolenanwendung hinzugefügt. Sie können dies auch in einer separaten Projektmappe durchführen. 

4. Klicken Sie mit der rechten Maustaste auf **Extras**, und klicken Sie dann auf **Bibliothekspaket-Manager** und **Paket-Manager-Konsole**. 

	Daraufhin wird die Paket-Manager-Konsole angezeigt.

6. Legen Sie im Konsolenfenster das **Standardprojekt** auf das neue Konsolenanwendungsprojekt fest, und führen Sie dann in diesem Fenster den folgenden Befehl aus:

        Install-Package WindowsAzure.ServiceBus
    
	Damit wird ein Verweis auf das Azure Service Bus-SDK mit dem <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet-Paket</a> hinzugefügt. 

5. Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende "using"-Anweisung hinzu:

        using Microsoft.ServiceBus.Notifications;

6. Fügen Sie in der Klasse **Program** folgende Methode hinzu:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello from a .NET App!</text></binding></visual></toast>";
            await hub.SendWindowsNativeNotificationAsync(toast);
        }

   	Stellen Sie sicher, dass Sie den Platzhalter "hub name" durch den Namen des Notification Hub ersetzen, der im Portal auf der Registerkarte **Notification Hubs** angezeigt wird. Ersetzen Sie außerdem den Platzhalter für die Verbindungszeichenfolge durch die Verbindungszeichenfolge **DefaultFullSharedAccessSignature**, die Sie im Abschnitt "Konfigurieren Ihres Notification Hub" erhalten haben. 

	>[WACOM.NOTE]Stellen Sie sicher, dass Sie die Verbindungszeichenfolge mit Vollzugriff (**Full**) verwenden, nicht mit Abhörzugriff (**Listen**). Die Verbindungszeichenfolge mit Abhörzugriff verfügt nicht über die Berechtigungen zum Senden von Benachrichtigungen.

7. Fügen Sie abschließend die folgenden Zeilen der **Main**-Methode hinzu:

         SendNotificationAsync();
		 Console.ReadLine();

8. Die Konsolenanwendung muss als Startprojekt festgelegt sein. Drücken Sie dann **F5**, um die Anwendung auszuführen. 

   	![][14]

	Sie erhalten eine Popupbenachrichtigung auf allen registrierten Geräten. Durch Klicken oder Tippen auf das Popupbanner wird die App geladen.

Sie finden alle unterstützten Nutzlasten auf MSDN in den Themen [Popupvorlagenkatalog], [Kachelvorlagenkatalog] und [Signalübersicht].

## <a name="next-steps"> </a>Nächste Schritte

In diesem einfachen Beispiel haben Sie Übertragungsbenachrichtigungen an alle Ihre Windows-Geräte gesendet. Informationen zum Adressieren bestimmter Benutzer finden Sie in dem Lernprogramm [Benachrichtigen von Benutzern mit Benachrichtigungshubs]. Wenn Sie Ihre Benutzer nach Interessengruppen unterteilen möchten, lesen Sie [Verwenden von Notification Hubs zum Senden von neuesten Nachrichten]. Weitere Informationen zur Verwendung von Notification Hubs finden Sie im [Notification Hubs-Leitfaden].

<!-- Anchors. -->
[Registrieren der App für Pushbenachrichtigungen]: #register
[Konfigurieren Ihres Notification Hub]: #configure-hub
[Verbinden Ihrer App mit dem Notification Hub]: #connecting-app
[Senden von Benachrichtigungen von Ihrem Back-End aus]: #send
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-submit-win8-app.png
[1]: ./media/notification-hubs-windows-store-dotnet-get-started/mobile-services-win8-app-name.png
[2]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-create-windows-universal-app.png
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
[20]: ./media/notification-hubs-windows-store-dotnet-get-started/notification-hub-windows-universal-app-install-package.png

<!-- URLs. -->
[Übermitteln einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-dotnet
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-dotnet
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-dotnet
[Senden von Pushbenachrichtigungen an App-Benutzer]: /de-de/develop/mobile/tutorials/push-notifications-to-users-dotnet
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-dotnet
[JavaScript und HTML]: /de-de/develop/mobile/tutorials/get-started-with-push-js

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[wns-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs-Leitfaden]: http://msdn.microsoft.com/de-de/library/jj927170.aspx

[Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]: /de-de/manage/services/notification-hubs/notify-users-aspnet
[Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten]: /de-de/manage/services/notification-hubs/breaking-news-dotnet

[Popupvorlagenkatalog]: http://msdn.microsoft.com/de-de/library/windows/apps/hh761494.aspx
[Kachelvorlagenkatalog]: http://msdn.microsoft.com/de-de/library/windows/apps/hh761491.aspx
[Signalübersicht]: http://msdn.microsoft.com/de-de/library/windows/apps/hh779719.aspx
