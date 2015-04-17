<properties 
	pageTitle="Erste Schritte mit Azure Notification Hubs" 
	description="Erfahren Sie mehr über die Verwendung von Azure Notification Hubs zum Senden von Pushbenachrichtigungen." 
	services="notification-hubs" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="dwrede"/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="hero-article" 
	ms.date="03/16/2015" 
	ms.author="wesmc"/>
# Erste Schritte mit Notification Hubs

[AZURE.INCLUDE [notification-hubs-selector-get-started](../includes/notification-hubs-selector-get-started.md)]

##Übersicht

In diesem Thema wird gezeigt, wie Sie mit Azure Notification Hubs Pushbenachrichtigungen an eine Windows Phone 8- oder Windows Phone 8.1 Silverlight-Anwendung senden können. Informationen zu Windows Phone 8.1 (nicht Silverlight) finden Sie bei der [Windows Universal](notification-hubs-windows-store-dotnet-get-started.md)-Version .
In diesem Lernprogramm erstellen Sie eine leere Windows Phone 8-App, die Pushbenachrichtigungen mithilfe des Microsoft-Pushbenachrichtigungsdienst (MPNS) erhält. Sobald Sie dieses Lernprogramm abgeschlossen haben, können Sie über Ihren Notification Hub Pushbenachrichtigungen an alle Geräte senden, die Ihre App ausführen.

> [AZURE.NOTE] Im Windows Phone SDK von Notification Hubs wird die Verwendung von WNS mit Windows Phone 8.1 Silverlight-Apps nicht unterstützt. Um WNS (anstelle von MPNS) mit Windows Phone 8.1-Silverlight-Apps zu verwenden, führen Sie dieses Beispiellernprogramm [Notification Hub - WP Silverlight][Notification Hub - WP Silverlight tutorial (in englischer Sprache)] aus, in dem REST-APIs verwendet werden.

Das Lernprogramm zeigt ein einfaches Übertragungsszenario mithilfe von Notification Hubs. 

##Voraussetzungen

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Visual Studio 2012 Express für Windows Phone] oder eine höhere Version

Das Abschließen dieses Lernprogramms ist eine Voraussetzung für alle anderen Lernprogramme zu Notification Hubs für Windows Phone 8-Apps. 

> [AZURE.NOTE] Sie benötigen ein aktives Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [1 Monat kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fnotification-hubs-windows-phone-get-started%2F).

##Erstellen Ihres Notification Hub

1. Melden Sie sich beim [Azure Verwaltungsportal][Azure-Verwaltungsportal] an, klicken Sie im unteren Bereich des Bildschirms auf **+NEU**.

2. Klicken Sie auf **App-Dienste**, dann auf **Service Bus**, **Notification Hub** und **Schnellerfassung**.

   	![][7]

3. Geben Sie einen Namen für Ihren Notification Hub ein, wählen Sie Ihre gewünschte Region aus, und klicken Sie dann auf **Neuen Notification Hub erstellen**.

   	![][8]

4. Klicken Sie auf den gerade erstellten Namespace (normalerweise ***Name des Notification Hub*-ns**), und klicken Sie dann oben auf **Konfigurieren**.

   	![][9]

5. Klicken Sie oben auf die Registerkarte **Notification Hubs** und dann auf den soeben erstellten Notification Hub.

   	![][10]

6. Klicken Sie unten auf **Verbindungsinformationen**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

   	![][12]

7. Klicken Sie auf die Registerkarte **Konfigurieren** und dann im Abschnitt **Windows Phone notifications settings** auf das Kontrollkästchen **Enable unauthenticated push notifications**.

   	![][15]

Nun verfügen Sie über die Verbindungszeichenfolgen, die Sie zum Registrieren Ihrer Windows Phone 8-App und zum Senden von Benachrichtigungen benötigen.

> [AZURE.NOTE] Dieses Lernprogramm verwendet MPNS im nicht authentifizierten Modus. Im nicht authentifizierten MPNS-Modus liegen Einschränkungen für Benachrichtigungen vor, die Sie an jeden Kanal senden können. Notification Hubs unterstützen den [authentifizierten MPNS-Modus](http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx). <!--Refer to [Notification Hubs How-To for Windows Phone 8] for more information on how to use MPNS authenticated mode.-->

##Verbinden Ihrer App mit dem Notification Hub

1. Erstellen Sie in Visual Studio eine neue Windows Phone 8-Anwendung.

   	![][13]

	In Visual Studio 2013 Update 2 oder höher erstellen Sie stattdessen eine Windows Phone Silverlight-Anwendung.
	
	![][11]	

2. Klicken Sie in Visual Studio mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten**. 

	Daraufhin wird das Dialogfeld "NuGet-Pakete verwalten" angezeigt.

3. Suchen Sie nach  `WindowsAzure.Messaging.Managed`, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen. 

	![][20]

	Damit wird ein Verweis auf die Azure Messaging-Bibliothek für Windows mit dem <a href="http://nuget.org/packages/WindowsAzure.Messaging.Managed/">WindowsAzure.Messaging.Managed NuGet-Paket</a> heruntergeladen, installiert und hinzugefügt. 

4. Öffnen Sie die Datei "App.xaml.cs", und fügen Sie die folgenden `using`-Anweisungen hinzu:

        using Microsoft.Phone.Notification;
        using Microsoft.WindowsAzure.Messaging;

5. Fügen Sie den folgenden Code oben in der Methode **Application_Launching** in "App.xaml.cs" ein:
	
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
    Dieser Code ruft die Kanal-URI für die App von MPNS ab und registriert dann diese Kanal-URI bei Ihrem Notification Hub. Er sorgt außerdem dafür, dass die Kanal-URI in Ihrem Notification Hub registriert ist, sobald die Anwendung gestartet wird.

	>[AZURE.NOTE]Dieses Lernprogramm sendet eine Popupbenachrichtigung an das Gerät. Wenn Sie eine Kachelbenachrichtigung senden, müssen Sie stattdessen die  **BindToShellTile**-Methode für den Kanal aufrufen. Um Popup- und Kachelbenachrichtigungen zu unterstützen, rufen Sie beide Methoden, **BindToShellTile** und **BindToShellToast**, auf. 
    
6. Erweitern Sie im Projektmappen-Explorer **Eigenschaften**, öffnen Sie die Datei "WMAppManifest.xml", klicken Sie auf die Registerkarte **Funktionen**, und stellen Sie sicher, dass die Funktion **ID___CAP___PUSH_NOTIFICATION** aktiviert ist.

   	![][14]

   	Damit stellen Sie sicher, dass Ihre App Pushbenachrichtigungen empfangen kann.
	
7. Drücken Sie die Taste F5, um die App auszuführen.

	Es wird eine Registrierungsmeldung angezeigt.

##Senden von Benachrichtigungen vom Back-End aus

Sie können Benachrichtigungen mithilfe von Notification Hubs von jedem beliebigen Back-End aus über die <a href="http://msdn.microsoft.com/library/windowsazure/dn223264.aspx">REST-Schnittstelle</a> senden. In diesem Lernprogramm senden Sie Benachrichtigungen mit einer .NET-Konsolenanwendung. Ein Beispiel für das Senden von Benachrichtigungen von einem mit Notification Hubs integrierten Azure Mobile Services-Back-End finden Sie unter **Erste Schritte mit Pushbenachrichtigungen in Mobile Services** ([.NET-Back-End](mobile-services-javascript-backend-windows-phone-get-started-push.md) | [JavaScript-Back-End](mobile-services-javascript-backend-windows-phone-get-started-push.md)).  Ein Beispiel für das Senden von Benachrichtigungen über die REST-APIs finden Sie unter **Verwenden von Notification Hubs von Java/PHP aus** ([Java](notification-hubs-java-backend-how-to.md) | [PHP](notification-hubs-php-backend-how-to.md)).

1. Klicken Sie mit der rechten Maustaste auf die Projektmappe, wählen Sie **Hinzufügen** und **Neues Projekt...** aus, und klicken Sie dann unter **Visual C#** auf **Windows** und **Konsolenanwendung** und **OK**. 

   	![][6]

	Damit wird der Projektmappe eine neue Visual C#-Konsolenanwendung hinzugefügt. Sie können dies auch in einer separaten Projektmappe durchführen. 

4. Klicken Sie mit der rechten Maustaste auf **Extras**, und klicken Sie dann auf **Bibliothekspaket-Manager** und **Paket-Manager-Konsole**. 

	Daraufhin wird die Paket-Manager-Konsole angezeigt.

6. Stellen Sie im Konsolenfenster das **Standardprojekt** auf das neue Konsolenanwendungsprojekt ein, und führen Sie dann in diesem Fenster den folgenden Befehl aus:

        Install-Package WindowsAzure.ServiceBus
    
	Damit wird ein Verweis auf das Azure Service Bus-SDK mit dem <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet-Paket</a> hinzugefügt. 

5. Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende `using`-Anweisung hinzu:

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

	>[AZURE.NOTE]Stellen Sie sicher, dass Sie die Verbindungszeichenfolge mit Vollzugriff (**Full**) verwenden, nicht die mit Abhörzugriff (**Listen**). Die Verbindungszeichenfolge mit Abhörzugriff verfügt nicht über die Berechtigungen zum Senden von Benachrichtigungen.

4. Fügen Sie dann die folgende Zeile zu Ihrer Main-Methode hinzu:

         SendNotificationAsync();
		 Console.ReadLine();

5. Stellen Sie bei laufendem Windows Phone-Emulator und geschlossener App das Konsolenanwendungsprojekt als Standard-Startprojekt ein, und drücken Sie dann zur Ausführung der App die Taste F5. 

	Sie erhalten eine Popupbenachrichtigung. Durch Tippen auf das Popupbanner wird die App geladen.

Sie finden alle möglichen Nutzlasten auf MSDN in den Themen [Popupvorlagenkatalog] und [Kachelvorlagenkatalog].

##Nächste Schritte

In diesem einfachen Beispiel übertragen Sie Benachrichtigungen an all Ihre Windows Phone 8-Geräte. Informationen zum Adressieren bestimmter Benutzer finden Sie in dem Lernprogramm [Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]. Wenn Sie Ihre Benutzer in Interessengruppen einteilen möchten, finden Sie unter [Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten] weitere Informationen. Weitere Informationen zur Verwendung von Notification Hubs finden Sie in der [Notification Hubs-Anleitung][Notification Hubs-Leitfaden].



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
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Notification Hubs-Leitfaden]: http://msdn.microsoft.com/library/jj927170.aspx
[Authentifizierter MPNS-Modus]: http://msdn.microsoft.com/library/windowsphone/develop/ff941099(v=vs.105).aspx
[Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]: notification-hubs-aspnet-backend-windows-dotnet-notify-users.md
[Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten]: notification-hubs-windows-phone-send-breaking-news.md
[Popupvorlagenkatalog]: http://msdn.microsoft.com/library/windowsphone/develop/jj662938(v=vs.105).aspx
[Kachelvorlagenkatalog]: http://msdn.microsoft.com/library/windowsphone/develop/hh202948(v=vs.105).aspx
[Notification Hub - WP Silverlight tutorial (in englischer Sprache)]: https://github.com/Azure/azure-notificationhubs-samples/tree/master/PushToSLPhoneApp


<!--HONumber=49-->