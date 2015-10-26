
In diesem Abschnitt erfahren Sie, wie Sie Benachrichtigungen von einer .NET-Konsolen-App oder anderen senden. Bei Verwendung von Mobile Services finden Sie weitere Informationen im Lernprogramm [Erste Schritte mit Push](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md). Wenn Sie Java oder PHP verwenden möchten, lesen Sie die Informationen unter [Verwenden von Notification Hubs von Java/PHP](../articles/notification-hubs/notification-hubs-java-backend-how-to.md). Sie können Benachrichtigungen von beliebigen Back-Ends mithilfe der [Notification Hubs-REST-Schnittstelle](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) senden.

Der folgende Code sendet Benachrichtigungen an Windows Store-, Windows Phone-, iOS- und Android-Geräte.

Überspringen Sie die Schritte 1 bis 3, wenn Sie eine Konsolenanwendung erstellt haben, als Sie [Erste Schritte mit Notification Hubs][get-started] abgeschlossen haben.

1. Erstellen Sie in Visual Studio eine neue Visual C#-Konsolenanwendung 

   	![][13]

2. Klicken Sie im Visual Studio-Hauptmenü auf **Extras**, **Bibliothekspaket-Manager** und **Paket-Managerkonsole**. Geben Sie dann in das Konsolenfenster Folgendes ein und drücken Sie die **Eingabetaste**:

        Install-Package Microsoft.Azure.NotificationHubs
 	
	Dies fügt mithilfe des <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.NotificationHubs-NuGet-Pakets</a> einen Verweis auf das Azure Notification Hubs-SDK hinzu.

3. Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende `using`-Anweisung hinzu:

        using Microsoft.Azure.NotificationHubs;

4. Fügen Sie in der `Program`-Klasse die folgende Methode hinzu, oder ersetzen Sie sie, falls sie bereits vorhanden ist:

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
                    var wnsToast = "<toast><visual><binding template="ToastText01">" 
                        + "<text id="1">Breaking " + category + " News!" 
                        + "</text></binding></visual></toast>";         
                    await hub.SendWindowsNativeNotificationAsync(wnsToast, category);

                    // Define a Windows Phone toast.
                    var mpnsToast =
                        "<?xml version="1.0" encoding="utf-8"?>" +
                        "<wp:Notification xmlns:wp="WPNotification">" +
                            "<wp:Toast>" +
                                "<wp:Text1>Breaking " + category + " News!</wp:Text1>" +
                            "</wp:Toast> " +
                        "</wp:Notification>";         
                    await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);

                    // Define an iOS alert.
                    var alert = "{"aps":{"alert":"Breaking " + category + " News!"}}";
                    await hub.SendAppleNativeNotificationAsync(alert, category);

					// Define an Android notification.
                    var notification = "{"data":{"msg":"Breaking " + category + " News!"}}";
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
	
	> [AZURE.NOTE]Dieser Back-End-Code unterstützt Windows Store-, Windows Phone-, iOS- und Android-Clients. Send-Methoden geben einen Fehler zurück, wenn der Benachrichtigungshub noch nicht für eine bestimmte Clientplattform konfiguriert wurde.

6. Ersetzen Sie im Code oben die Platzhalter `<hub name>` und `<connection string with full access>` durch den Namen Ihres Notification Hubs und die Verbindungszeichenfolge für *DefaultFullSharedAccessSignature*, die Sie zuvor erhalten haben.

7. Fügen Sie die folgenden Zeilen zur **Main**-Methode hinzu:

         SendNotificationAsync();
		 Console.ReadLine();

<!-- Anchors -->
[From a console app]: #console
[From Mobile Services]: #mobile-services
[Run the app and generate notifications]: #test-app

<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->
[get-started]: ../articles/notification-hubs/notification-hubs-windows-store-dotnet-get-started.md
[Use Notification Hubs to send notifications to users]: ../articles/tutorial-notify-users-mobileservices.md
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started/#create-new-service
[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx

<!---HONumber=Oct15_HO3-->