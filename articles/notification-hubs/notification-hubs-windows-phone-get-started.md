<properties
	pageTitle="Senden von Pushbenachrichtigungen mit Azure Notification Hubs unter Windows Phone | Microsoft Azure"
	description="In diesem Lernprogramm erfahren Sie, wie Sie mit Azure Notification Hubs Pushbenachrichtigungen an eine Windows Phone 8- oder Windows Phone 8.1 Silverlight-Anwendung senden können."
	services="notification-hubs"
	documentationCenter="windows"
    keywords="Pushbenachrichtigung, Pushbenachrichtigung, Windows Phone Push"
	authors="wesmc7777"
	manager="dwrede"
	editor="dwrede"/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows-phone"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="03/10/2016"
	ms.author="wesmc"/>

# Senden von Pushbenachrichtigungen mit Azure Notification Hubs unter Windows Phone

[AZURE.INCLUDE [Notification-Hubs-Auswahl-Erste-Schritte](../../includes/notification-hubs-selector-get-started.md)]

##Übersicht

> [AZURE.NOTE] Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-DE%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).

In diesem Lernprogramm wird gezeigt, wie Sie mit Azure Notification Hubs Pushbenachrichtigungen an eine Windows Phone 8- oder Windows Phone 8.1 Silverlight-Anwendung senden können. Informationen zu Windows Phone 8.1 (nicht Silverlight) finden Sie bei der [Windows Universal](notification-hubs-windows-store-dotnet-get-started.md)-Version. In diesem Lernprogramm erstellen Sie eine leere Windows Phone 8-App, die Pushbenachrichtigungen mithilfe des Microsoft-Pushbenachrichtigungsdienstes (Microsoft Push Notification Service, MPNS) erhält. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen.

> [AZURE.NOTE] Im Windows Phone-SDK von Notification Hubs wird die Verwendung des Windows-Pushbenachrichtigungsdienstes (Windows Push Notification Service, WNS) mit Windows Phone 8.1 Silverlight-Apps nicht unterstützt. Um WNS (anstelle von MPNS) mit Windows Phone 8.1-Silverlight-Apps zu verwenden, führen Sie das Tutorial für [Notification Hubs – Windows Phone Silverlight] aus, in dem REST-APIs verwendet werden.

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs.

##Voraussetzungen

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Visual Studio 2012 Express für Windows Phone] oder eine höhere Version

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Notification Hubs-Lernprogramme für Windows Phone 8-Apps.

##Erstellen Ihres Notification Hub

[AZURE.INCLUDE [notification-hubs-portal-create-new-hub](../../includes/notification-hubs-portal-create-new-hub.md)]

<ol start="6">
<li><p>Klicken Sie im Abschnitt <b>Notification Services</b> (unter <i>Einstellungen</i>) auf <b>Windows Phone (MPNS)</b>, und aktivieren Sie dann das Kontrollkästchen <b>Nicht authentifizierte Pushbenachrichtigungen zulassen</b>.</p>
</li>
</ol>

&emsp;&emsp;![Azure-Portal – Nicht authentifizierte Pushbenachrichtigungen aktivieren](./media/notification-hubs-windows-phone-get-started/azure-portal-unauth.png)

Der Hub ist jetzt erstellt und so konfiguriert, dass nicht authentifizierte Benachrichtigungen für Windows Phone gesendet werden.

> [AZURE.NOTE] Dieses Lernprogramm verwendet MPNS im nicht authentifizierten Modus. Im nicht authentifizierten MPNS-Modus liegen Einschränkungen für Benachrichtigungen vor, die Sie an jeden Kanal senden können. Notification Hubs unterstützt den [authentifizierten MPNS-Modus](http://msdn.microsoft.com/library/windowsphone/develop/ff941099.aspx), wodurch Sie Ihr Zertifikat hochladen können.

##Verbinden Ihrer App mit dem Notification Hub

1. Erstellen Sie in Visual Studio eine neue Windows Phone 8-Anwendung.

   	![Visual Studio – Neues Projekt – Windows Phone-App][13]

	In Visual Studio 2013 Update 2 oder höher erstellen Sie stattdessen eine Windows Phone Silverlight-Anwendung.

	![Visual Studio – Neues Projekt – Leere App – Windows Phone Silverlight][11]

2. Klicken Sie in Visual Studio mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten**.

	Daraufhin wird das Dialogfeld **NuGet-Pakete verwalten** angezeigt.

3. Suchen Sie nach `WindowsAzure.Messaging.Managed`, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.

	![Visual Studio – NuGet-Paket-Manager][20]

	Damit wird ein Verweis auf die Azure Messaging-Bibliothek für Windows mit dem <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet-Paket</a> heruntergeladen, installiert und hinzugefügt.

4. Öffnen Sie die Datei "App.xaml.cs", und fügen Sie die folgenden `using`-Anweisungen hinzu:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. Fügen Sie den folgenden Code oben in der Methode **Application\_Launching** in „App.xaml.cs“ ein:

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
            var result = await hub.RegisterNativeAsync(args.ChannelUri.ToString());

            System.Windows.Deployment.Current.Dispatcher.BeginInvoke(() =>
            {
                MessageBox.Show("Registration :" + result.RegistrationId, "Registered", MessageBoxButton.OK);
            });
        });

    >[AZURE.NOTE] Der Wert **MyPushChannel** ist ein Index, der verwendet wird, um nach einem vorhandenen Kanal in der Sammlung [HttpNotificationChannel](https://msdn.microsoft.com/library/windows/apps/microsoft.phone.notification.httpnotificationchannel.aspx) zu suchen. Wenn kein Kanal vorhanden ist, erstellen Sie einen neuen Eintrag mit diesem Namen.
    
    Stellen Sie sicher, dass Sie den Namen des Hubs und die Verbindungszeichenfolge **DefaultListenSharedAccessSignature** einfügen, die Sie im vorigen Abschnitt erhalten haben. Dieser Code ruft den Kanal-URI für die App von MPNS ab und registriert dann diesen Kanal-URI bei Ihrem Benachrichtigungshub. Er sorgt außerdem dafür, dass der Kanal-URI in Ihrem Benachrichtigungshub registriert ist, sobald die Anwendung gestartet wird.

	>[AZURE.NOTE]Dieses Lernprogramm sendet eine Popupbenachrichtigung an das Gerät. Wenn Sie eine Kachelbenachrichtigung senden, müssen Sie stattdessen die **BindToShellTile**-Methode für den Kanal aufrufen. Um Popup- und Kachelbenachrichtigungen zu unterstützen, rufen Sie beide Methoden, **BindToShellTile** und **BindToShellToast**, auf.

6. Erweitern Sie im Projektmappen-Explorer **Eigenschaften**, öffnen Sie die Datei `WMAppManifest.xml`, klicken Sie auf die Registerkarte **Funktionen**, und aktivieren Sie die Funktion **ID\_CAP\_PUSH\_NOTIFICATION**.

   	![Visual Studio – Windows Phone-App-Funktionen][14]

   	Damit stellen Sie sicher, dass Ihre App Pushbenachrichtigungen empfangen kann. Andernfalls schlägt das Senden einer Pushbenachrichtigung an die App fehl.

7. Drücken Sie `F5`, um die App auszuführen.

	In der App wird eine Registrierungsmeldung angezeigt.

8. Schließen Sie die App.

   >[AZURE.NOTE] Zum Empfangen einer Popup-Pushbenachrichtigung muss die Anwendung nicht im Vordergrund ausgeführt werden.

##Senden von Pushbenachrichtigungen von Ihrem Back-End

Sie können Pushbenachrichtigungen mit Notification Hubs von jedem beliebigen Back-End über die öffentliche <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST-Schnittstelle</a> senden. In diesem Tutorial senden Sie Pushbenachrichtigungen mithilfe einer .NET-Konsolenanwendung.

Ein Beispiel für das Senden von Pushbenachrichtigungen von einem mit Notification Hubs integrierten ASP.NET-WebAPI-Back-End finden Sie unter [Azure Notification Hubs – Benachrichtigen von Benutzern über .NET-Back-End](./notification-hubs-aspnet-backend-windows-dotnet-notify-users.md).

Ein Beispiel zum Senden von Pushbenachrichtigungen über die [REST APIs](https://msdn.microsoft.com/library/azure/dn223264.aspx) finden Sie unter [Verwenden von Notification Hubs von Java aus](./notification-hubs-java-backend-how-to.md) und [Verwenden von Notification Hubs von PHP aus](./notification-hubs-php-backend-how-to.md).

1. Klicken Sie mit der rechten Maustaste auf die Projektmappe, wählen Sie **Hinzufügen** und **Neues Projekt...** aus, und klicken Sie dann unter **Visual C#** auf **Windows**, **Konsolenanwendung** und **OK**.

   	![Visual Studio – Neues Projekt – Konsolenanwendung][6]

	Damit wird der Projektmappe eine neue Visual C#-Konsolenanwendung hinzugefügt. Sie können dies auch in einer separaten Projektmappe durchführen.

4. Klicken Sie auf **Extras**, auf **Bibliothekspaket-Manager** und dann auf **Paket-Manager-Konsole**.

	Daraufhin wird die Paket-Manager-Konsole angezeigt.

5.  Legen Sie im Fenster der **Paket-Manager-Konsole** das neue Konsolenanwendungsprojekt als **Standardprojekt** fest, und führen Sie dann im Konsolenfenster den folgenden Befehl aus:

        Install-Package Microsoft.Azure.NotificationHubs

	Dies fügt mithilfe des <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.NotificationHubs-NuGet-Pakets</a> einen Verweis auf das Azure Notification Hubs-SDK hinzu.

6. Öffnen Sie die Datei `Program.cs`, und fügen Sie die folgende `using`-Anweisung hinzu:

        using Microsoft.Azure.NotificationHubs;

6. Fügen Sie in der `Program`-Klasse die folgende Methode hinzu:

        private static async void SendNotificationAsync()
        {
            NotificationHubClient hub = NotificationHubClient
				.CreateClientFromConnectionString("<connection string with full access>", "<hub name>");
            string toast = "<?xml version="1.0" encoding="utf-8"?>" +
                "<wp:Notification xmlns:wp="WPNotification">" +
                   "<wp:Toast>" +
                        "<wp:Text1>Hello from a .NET App!</wp:Text1>" +
                   "</wp:Toast> " +
                "</wp:Notification>";
            await hub.SendMpnsNativeNotificationAsync(toast);
        }

	Stellen Sie sicher, dass Sie den Platzhalter `<hub name>` durch den Namen des Notification Hubs ersetzen, der im Portal angezeigt wird. Ersetzen Sie außerdem den Platzhalter für die Verbindungszeichenfolge durch die Verbindungszeichenfolge **DefaultFullSharedAccessSignature**, die Sie im Abschnitt „Konfigurieren Ihres Notification Hubs“ erhalten haben.

	>[AZURE.NOTE]Stellen Sie sicher, dass Sie die Verbindungszeichenfolge mit Vollzugriff (**Full**) verwenden, nicht mit Abhörzugriff (**Listen**). Die Verbindungszeichenfolge mit Abhörzugriff verfügt nicht über Berechtigungen zum Senden von Pushbenachrichtigungen.

4. Fügen Sie in Ihrer `Main`-Methode die folgende Zeile hinzu:

         SendNotificationAsync();
		 Console.ReadLine();

5. Legen Sie bei laufendem Windows Phone-Emulator und geschlossener App das Konsolenanwendungsprojekt als Standardstartprojekt fest, und drücken Sie dann zum Ausführen der App die Taste `F5`.

	Sie erhalten eine Popup-Pushbenachrichtigung. Durch Tippen auf das Popupbanner wird die App geladen.

Sie finden alle möglichen Nutzlasten auf MSDN in den Themen [Toast-Katalog] und [Kachelkatalog].

##Nächste Schritte

In diesem einfachen Beispiel haben Sie Pushbenachrichtigungen an all Ihre Windows Phone 8-Geräte übertragen.

Informationen zum Senden von Benachrichtigungen an bestimmte Benutzer finden Sie im Tutorial [Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer].

Wenn Sie Ihre Benutzer in Interessengruppen einteilen möchten, finden Sie unter [Verwenden von Benachrichtigungshubs zum Übermitteln von Nachrichten] weitere Informationen.

Weitere Informationen zur Verwendung von Benachrichtigungshubs finden Sie in der [Benachrichtigungshubs-Anleitung].



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
[Benachrichtigungshubs-Anleitung]: http://msdn.microsoft.com/library/jj927170.aspx
[MPNS authenticated mode]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Verwenden von Benachrichtigungshubs zum Übermitteln von Nachrichten]: notification-hubs-windows-phone-send-breaking-news.md
[Toast-Katalog]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[Kachelkatalog]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notification Hubs – Windows Phone Silverlight]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp

<!---HONumber=AcomDC_0316_2016-->