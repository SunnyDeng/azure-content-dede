<properties urlDisplayName="Get Started" pageTitle="Erste Schritte mit Azure Notification Hubs" metaKeywords="" description="Learn how to use Azure Notification Hubs to push notifications." metaCanonical="" services="notification-hubs" documentationCenter="Mobile" title="Get started with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="dwrede" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/24/2014" ms.author="glenga" />
# Erste Schritte mit Notification Hubs

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/" title="Windows Universal">Windows Universal</a><a href="/de-de/documentation/articles/notification-hubs-windows-phone-get-started/" title="Windows Phone" class="current">Windows Phone</a><a href="/de-de/documentation/articles/notification-hubs-ios-get-started/" title="iOS">iOS</a><a href="/de-de/documentation/articles/notification-hubs-android-get-started/" title="Android">Android</a><a href="/de-de/documentation/articles/notification-hubs-kindle-get-started/" title="Kindle">Kindle</a><a href="/de-de/documentation/articles/notification-hubs-baidu-get-started/" title="Baidu">Baidu</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-ios-get-started/" title="Xamarin.iOS">Xamarin.iOS</a><a href="/de-de/documentation/articles/partner-xamarin-notification-hubs-android-get-started/" title="Xamarin.Android">Xamarin.Android</a></div>

In diesem Thema wird gezeigt, wie Sie mit Azure Notification Hubs Pushbenachrichtigungen an eine Windows Phone 8- oder Windows Phone 8.1 Silverlight-Anwendung senden können. Informationen zu Windows Phone 8.1 (nicht Silverlight) finden Sie bei der [Windows Universal](/de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/)-Version.
In diesem Lernprogramm erstellen Sie eine leere Windows Phone 8-App, die Pushbenachrichtigungen mithilfe des Microsoft-Pushbenachrichtigungsdienst (MPNS) erhält. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen.

> [AZURE.NOTE] Im Windows Phone SDK von Notification Hubs wird die Verwendung von WNS mit Windows Phone 8.1 Silverlight-Apps nicht unterstützt. Wenn Sie WNS (statt MPNS) mit Windows Phone 8.1 Silverlight-Apps verwenden möchten, müssen Sie Ihre WNS-Anmeldeinformationen einrichten, wie unter [Erste Schritte mit Windows Universal](/de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/) beschrieben. Sie können sich dann vom Back-End aus registrieren, wie im Lernprogramm [Benachrichtigen von Benutzern](/de-de/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/) erläutert, oder aber die [Notification Hubs-REST-APIs](http://msdn.microsoft.com/de-de/library/dn223264.aspx) verwenden.

In diesem Lernprogramm werden die folgenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Erstellen Ihres Notification Hub]
2. [Verbinden Ihrer App mit dem Notification Hub]
3. [Senden von Benachrichtigungen von Ihrem Back-End aus]

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Benachrichtigungshubs. Bearbeiten Sie auch das nachfolgende Lernprogramm, um mehr über die Verwendung von Benachrichtigungshubs zur Adressierung von speziellen Benutzern und Gerätegruppen zu erfahren. Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Visual Studio 2012 Express für Windows Phone] oder eine höhere Version.

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Benachrichtigungshubs-Lernprogramme für Windows Phone 8-Apps. 

<div class="dev-callout"><strong>Hinweis</strong> <p>Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started%2F" target="_blank">Kostenlose Azure-Testversion</a>.</p></div>

##<a name="configure-hub"></a>Erstellen Ihres Notification Hub

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie im unteren Teil des Bildschirms auf **+NEW** (+NEU).

2. Klicken Sie auf **App-Dienste**, **Service Bus**, **Notification Hub**, und dann auf **Schnellerfassung**.

   	![][7]

3. Geben Sie einen Namen für den Notification Hub ein. Wählen Sie die gewünschte Region, und klicken Sie dann auf **Create a new Notification Hub** (Neuen Notification Hub erstellen).

   	![][8]

4. Klicken Sie auf den gerade erstellten Namespace (normalerweise ***Name des Notification Hub*-ns**), und klicken Sie dann oben auf **Configure**.

   	![][9]

5. Klicken Sie oben auf die Registerkarte **Notification Hubs** und dann auf den soeben erstellen Notification Hub.

   	![][10]

6. Klicken Sie unten auf **Verbindungsinformationen**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

   	![][12]

7. Klicken Sie auf die Registerkarte **Konfigurieren** und dann im Abschnitt **Windows Phone notifications settings** (Windows Phone-Benachrichtigungseinstellungen) auf das Kontrollkästchen **Enable unauthenticated push notifications** (Nicht authentifizierte Pushbenachrichtigungen aktivieren).

   	![][15]

Nun verfügen Sie über die Verbindungszeichenfolgen, die Sie zum Registrieren Ihrer Windows Phone 8-App und zum Senden von Benachrichtigungen benötigen.

<div class="dev-callout"><b>Hinweis</b>
		<p>Dieses Lernprogramm verwendet MPNS im nicht authentifizierten Modus. Im nicht authentifizierten MPNS-Modus liegen Einschränkungen für Benachrichtigungen vor, die Sie an jeden Kanal senden können. Benachrichtigungshubs unterstützen den <a href="http://msdn.microsoft.com/de-de/library/windowsphone/develop/ff941099(v=vs.105).aspx">authentifizierten MPNS-Modus</a>. <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.--></p>
</div>

##<a name="connecting-app"></a>Verbinden Ihrer App mit dem Notification Hub

1. Erstellen Sie in Visual Studio eine neue Windows Phone 8-Anwendung.

   	![][13]

	In Visual Studio 2013 Update 2 oder höher erstellen Sie stattdessen eine Windows Phone Silverlight-Anwendung.
	
	![][11]	

2. Klicken Sie in Visual Studio mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten**. 

	Daraufhin wird das Dialogfeld "NuGet-Pakete verwalten" angezeigt.

3. Suchen Sie nach `WindowsAzure.Messaging.Managed`, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen. 

	![][20]

	Damit wird ein Verweis auf die Azure Messaging-Bibliothek für Windows mit dem <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet package</a> heruntergeladen, installiert und hinzugefügt. 

4. Öffnen Sie die Datei "App.xaml.cs" und fügen Sie die folgenden `using`-Anweisungen hinzu:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. Fügen Sie den folgenden Code oben in der Methode **Application_Launching** in App.xaml.cs ein:
	
	    var channel = HttpNotificationChannel.Find("MyPushChannel");
        if (channel == null)
        {
            channel = new HttpNotificationChannel("MyPushChannel");
            channel.Open();
            channel.BindToShellToast();
        }

        channel.ChannelUriUpdated += new EventHandler<NotificationChannelUriEventArgs>(async (o, args) =>
        {
            var hub = new NotificationHub("<hub name>", "<connection string>");
            await hub.RegisterNativeAsync(args.ChannelUri.ToString());
        });

    Stellen Sie sicher, dass Sie den Namen des Hubs und die Verbindungszeichenfolge **DefaultListenSharedAccessSignature** einfügen, die Sie im vorigen Abschnitt erhalten haben.
    Dieser Code ruft die Kanal-URI für die App von MPNS ab und registriert dann diese Kanal-URI bei Ihrem Benachrichtigungshub. Er sorgt außerdem dafür, dass die Kanal-URI in Ihrem Benachrichtigungshub registriert ist, sobald die Anwendung gestartet wird.

	>[WACOM.NOTE]Dieses Lernprogramm sendet eine Popupbenachrichtigung an das Gerät. Wenn Sie eine Kachelbenachrichtigung senden, müssen Sie stattdessen die **BindToShellTile**-Methode für den Kanal aufrufen. Um Popup- und Kachelbenachrichtigungen zu unterstützen, rufen Sie beide Methoden, **BindToShellTile** und **BindToShellToast**, auf. 
    
6. Erweitern Sie im Projektmappen-Explorer **Eigenschaften**, öffnen Sie die Datei "WMAppManifest.xml", klicken Sie auf die Registerkarte **Funktionen**, und stellen Sie sicher, dass die Funktion **ID___CAP___PUSH_NOTIFICATION** aktiviert ist.

   	![][14]

   	Damit stellen Sie sicher, dass Ihre App Pushbenachrichtigungen empfangen kann.
	
7. Drücken Sie die Taste F5, um die App auszuführen.

	Es wird eine Registrierungsmeldung angezeigt.

##<a name="send"></a>Senden von Benachrichtigungen vom Back-End aus

Sie können Benachrichtigungen mithilfe von Notification Hubs von jedem beliebigen Back-End aus über die <a href="http://msdn.microsoft.com/de-de/library/windowsazure/dn223264.aspx">REST-Schnittstelle</a> senden. In diesem Lernprogramm senden Sie Benachrichtigungen mit einer .NET-Konsolenanwendung. Ein Beispiel zum Senden von Benachrichtigungen von einem mit Notification Hubs integrierten Azure Mobile Services-Back-End aus finden Sie unter **Erste Schritte mit Pushbenachrichtigungen in Mobile Services** ([.NET-Back-End](/de-de/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/) | [JavaScript-Back-End](/de-de/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/)).  Ein Beispiel zum Senden von Benachrichtigungen über die REST-APIs finden Sie unter **Verwenden von Notification Hubs von Java/PHP** ([Java](/de-de/documentation/articles/notification-hubs-java-backend-how-to/) | [PHP](/de-de/documentation/articles/notification-hubs-php-backend-how-to/)).

1. Klicken Sie mit der rechten Maustaste auf die Projektmappe, wählen Sie**Hinzufügen** und **Neues Projekt...** aus, und klicken Sie dann unter **Visual C#** auf **Windows** und auf **Konsolenanwendung**. Klicken Sie dann auf **OK**. 

   	![][6]

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
            string toast = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                "<wp:Notification xmlns:wp=\"WPNotification\">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

	Stellen Sie sicher, dass Sie den Platzhalter "hub name" durch den Namen des Notification Hub ersetzen, der im Portal auf der Registerkarte **Notification Hubs** angezeigt wird. Ersetzen Sie außerdem den Platzhalter für die Verbindungszeichenfolge durch die Verbindungszeichenfolge **DefaultFullSharedAccessSignature**, die Sie im Abschnitt "Konfigurieren Ihres Notification Hub" erhalten haben. 

	>[WACOM.NOTE]Stellen Sie sicher, dass Sie die Verbindungszeichenfolge mit Vollzugriff (**Full**) verwenden, nicht mit Abhörzugriff (**Listen**). Die Verbindungszeichenfolge mit Abhörzugriff verfügt nicht über die Berechtigungen zum Senden von Benachrichtigungen.

4. Fügen Sie dann die folgende Zeile zu Ihrer Main-Methode hinzu:

         SendNotificationAsync();
		 Console.ReadLine();

5. Stellen Sie bei laufendem Windows Phone-Emulator und geschlossener App das Konsolenanwendungsprojekt als Standard-Startprojekt ein, und drücken Sie dann zur Ausführung der App die Taste F5. 

	Sie erhalten eine Popupbenachrichtigung. Durch Tippen auf das Popupbanner wird die App geladen.

Sie finden alle möglichen Nutzlasten auf MSDN in den Themen [Popupvorlagenkatalog] und [Kachelvorlagenkatalog].

## <a name="next-steps"> </a>Nächste Schritte

In diesem einfachen Beispiel übertragen Sie Benachrichtigungen an all Ihre Windows Phone 8-Geräte. Informationen zum Adressieren bestimmter Benutzer finden Sie in dem Lernprogramm [Benachrichtigen von Benutzern mit Benachrichtigungshubs]. Wenn Sie Ihre Benutzer in Interessengruppen einteilen möchten, finden Sie unter [Verwenden von Benachrichtigungshubs zum Übermitteln von Nachrichten] weitere Informationen. Weitere Informationen zur Verwendung von Benachrichtigungshubs finden Sie in der [Benachrichtigungshubs-Anleitung].

<!-- Anchors. -->
[Erstellen Ihres Notification Hub]: #configure-hub
[Verbinden Ihrer App mit dem Notification Hub]: #connecting-app
[Senden von Benachrichtigungen von Ihrem Back-End aus]: #send
[Nächste Schritte]:#next-steps

<!-- Images. -->
[6]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png
[7]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal.png
[8]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-from-portal2.png
[9]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal.png
[10]: ./media/notification-hubs-windows-phone-get-started/notification-hub-select-from-portal2.png
[11]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-silverlight-app.png
[12]: ./media/notification-hubs-windows-phone-get-started/notification-hub-connection-strings.png

[13]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-wp-app.png
[14]: ./media/notification-hubs-windows-phone-get-started/mobile-app-enable-push-wp8.png
[15]: ./media/notification-hubs-windows-phone-get-started/notification-hub-pushauth.png
[20]: ./media/notification-hubs-windows-phone-get-started/notification-hub-windows-universal-app-install-package.png
[213]: ./media/notification-hubs-windows-phone-get-started/notification-hub-create-console-app.png





<!-- URLs. -->
[Visual Studio 2012 Express für Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Notification Hubs-Leitfaden]: http://msdn.microsoft.com/de-de/library/jj927170.aspx
[Notification Hubs - Anleitungen für Windows Phone 8]: tbd!!!
[Authentifizierter MPNS-Modus]: http://msdn.microsoft.com/de-de/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]: /de-de/manage/services/notification-hubs/notify-users-aspnet
[Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten]: /de-de/manage/services/notification-hubs/breaking-news-dotnet
[Popupvorlagenkatalog]: http://msdn.microsoft.com/de-de/library/windowsphone/develop/jj662938(v=vs.105).aspx
[Kachelvorlagenkatalog]: http://msdn.microsoft.com/de-de/library/windowsphone/develop/hh202948(v=vs.105).aspx
