
In diesem Abschnitt erfahren Sie, wie Sie Benachrichtigungen von einer .NET-Konsolen-App oder anderen senden.
Bei Verwendung von Mobile Services finden Sie weitere Informationen im Lernprogramm [Erste Schritte mit Push](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/) . Wenn Sie Java oder PHP verwenden möchten, lesen Sie die Informationen unter [Verwenden von Notification Hubs von Java/PHP](/de-de/documentation/articles/notification-hubs-java-backend-how-to/). Sie können Benachrichtigungen von beliebigen Back-Ends mithilfe der [Notification Hubs-REST-Schnittstelle] senden.

Der folgende Code sendet Benachrichtigungen an Windows Store-, Windows Phone-, iOS- und Android-Geräte. 

Überspringen Sie die Schritte 1 bis 3, wenn Sie eine Konsolenanwendung erstellt haben, als Sie [Erste Schritte mit Notification Hubs][get-started]. abgeschlossen haben.

1. Erstellen Sie in Visual Studio eine neue Visual C#-Konsolenanwendung 

   	![][13]

2. Klicken Sie im Visual Studio-Hauptmenü auf **Extras**, **Bibliothekspaket-Manager** und **Paket-Managerkonsole**. Geben Sie dann in das Konsolenfenster Folgendes ein und drücken Sie die **Eingabetaste**:

        Install-Package WindowsAzure.ServiceBus
 	
	Dadurch wird dem Azure Service Bus-SDK mithilfe des <a href="http://nuget.org/packages/WindowsAzure.ServiceBus/">WindowsAzure.ServiceBus NuGet-Pakets</a> ein Verweis hinzugefügt. 

3. Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende `using`-Anweisung hinzu:

        using Microsoft.ServiceBus.Notifications;

4. Fügen Sie der Klasse `Program` folgende Methode hinzu oder ersetzen Sie sie, falls sie bereits vorhanden ist:

        private static async void SendNotificationAsync()
        {
			// Define the notification hub.
		    NotificationHubClient hub = 
				NotificationHubClient.CreateClientFromConnectionString(
					"<connection string with full access>", "<hub name>");
		
		    // Create an array of breaking news categories.
		    var categories = new string[] { "World", "Politics", "Business", 
		        "Technology", "Science", "Sports"};
		
            foreach (var category in categories)
            {
                try
                {
                    // Define a Windows Store toast.
                    var wnsToast = "<toast><visual><binding template=\"ToastText01\">" 
                        + "<text id=\"1\">Breaking " + category + " News!" 
                        + "</text></binding></visual></toast>";         
                    await hub.SendWindowsNativeNotificationAsync(wnsToast, category);

                    // Define a Windows Phone toast.
                    var mpnsToast =
                        "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                        "<wp:Notification xmlns:wp=\"WPNotification\">" +
                            "<wp:Toast>" +
                                "<wp:Text1>Breaking " + category + " News!</wp:Text1>" +
                            "</wp:Toast> " +
                        "</wp:Notification>";         
                    await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);

                    // Define an iOS alert.
                    var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
                    await hub.SendAppleNativeNotificationAsync(alert, category);

					// Define an Android notification.
                    var notification = "{\"data\":{\"msg\":\"Breaking " + category + " News!\"}}";
                    await hub.SendGcmNativeNotificationAsync(notification, category);
                }
                catch (ArgumentException)
                {
                    // An exception is raised when the notification hub hasn't been 
                    // registered for the iOS, Windows Store, or Windows Phone platform. 
                }
            }
		 }

	Dieser Code sendet Benachrichtigungen für jeden der sechs Tags im Zeichenfolgenarray an Windows Store-, Windows Phone- und iOS-Geräte. Durch die Verwendung von Tags wird sichergestellt, dass Geräte nur Benachrichtigungen für die registrierten Kategorien erhalten.
	
	> [AZURE.NOTE] Dieser Back-End-Code unterstützt Windows Store-, Windows Phone-, iOS- und Android-Clients. Send-Methoden geben einen Fehler zurück, wenn der Benachrichtigungshub noch nicht für eine bestimmte Clientplattform konfiguriert wurde. 

6. Ersetzen Sie im Code oben die Platzhalter <hub name> und <connection string with full access> durch den Namen Ihres Notification Hub und die Verbindungszeichenfolge für *DefaultFullSharedAccessSignature*, die Sie zuvor erhalten haben.

7. Fügen Sie die folgenden Zeilen zur **Main**-Methode hinzu:

         SendNotificationAsync();
		 Console.ReadLine();

<!-- Anchors -->
[Von einer Konsolenanwendung aus]: #console
[Von Mobile Services aus]: #mobile-services
[Ausführen der Anwendung und Erzeugen von Benachrichtigungen]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[get-started]: /de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Verwenden von Notification Hubs für Pushbenachrichtigungen an Benutzer]: ../notificationhubs/tutorial-notify-users-mobileservices.md
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[wns-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs-Leitfaden]: http://msdn.microsoft.com/de-de/library/jj927170.aspx
[Notification Hubs-Anleitung für Windows Store]: http://msdn.microsoft.com/de-de/library/jj927172.aspx
[Notification Hubs-REST-Schnittstelle]: http://msdn.microsoft.com/de-de/library/windowsazure/dn223264.aspx
<!--HONumber=42-->
