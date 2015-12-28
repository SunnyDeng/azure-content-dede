
In diesem Abschnitt erfahren Sie, wie Sie Neuigkeiten als Vorlage mit Tags von einer .NET-Konsolen-App senden.

Bei Verwendung von Mobile Services finden Sie weitere Informationen im Lernprogramm [Erste Schritte mit Push](mobile-services-dotnet-backend-windows-store-dotnet-get-started-push.md).

Wenn Sie Java oder PHP verwenden möchten, lesen Sie die Informationen unter [Verwenden von Notification Hubs von Java/PHP](../articles/notification-hubs/notification-hubs-java-backend-how-to.md). Sie können Benachrichtigungen von beliebigen Back-Ends mithilfe der [Notification Hubs-REST-Schnittstelle](http://msdn.microsoft.com/library/windowsazure/dn223264.aspx) senden.

Überspringen Sie die Schritte 1 bis 3, wenn Sie eine Konsolen-App für das Senden von Benachrichtigungen erstellt haben, als Sie das Tutorial [Erste Schritte mit Notification Hubs][get-started] abgeschlossen haben.

1. Erstellen Sie in Visual Studio eine neue Visual C#-Konsolenanwendung 

   	![][13]

2. Klicken Sie im Visual Studio-Hauptmenü auf **Extras**, **Bibliothekspaket-Manager** und **Paket-Managerkonsole**. Geben Sie dann in das Konsolenfenster Folgendes ein und drücken Sie die **Eingabetaste**:

        Install-Package Microsoft.Azure.NotificationHubs
 	
	Dies fügt mithilfe des <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.NotificationHubs-NuGet-Pakets</a> einen Verweis auf das Azure Notification Hubs-SDK hinzu.

3. Öffnen Sie die Datei "Program.cs", und fügen Sie die folgende `using`-Anweisung hinzu:

        using Microsoft.Azure.NotificationHubs;

4. Fügen Sie in der `Program`-Klasse die folgende Methode hinzu, oder ersetzen Sie sie, falls sie bereits vorhanden ist:

        private static async void SendTemplateNotificationAsync()
        {
			// Define the notification hub.
		    NotificationHubClient hub = 
				NotificationHubClient.CreateClientFromConnectionString(
					"<connection string with full access>", "<hub name>");

            // Create an array of breaking news categories.
            var categories = new string[] { "World", "Politics", "Business", 
											"Technology", "Science", "Sports"};

            // Sending the notification as a template notification. All template registrations that contain 
			// "messageParam" and the proper tags will receive the notifications. 
			// This includes APNS, GCM, WNS, and MPNS template registrations.

            Dictionary<string, string> templateParams = new Dictionary<string, string>();

            foreach (var category in categories)
            {
                templateParams["messageParam"] = "Breaking " + category + " News!";            
                await hub.SendTemplateNotificationAsync(templateParams, category);
            }
		 }

	Dieser Code sendet eine Vorlagenbenachrichtigung für jedes der sechs Tags im Zeichenfolgenarray. Durch die Verwendung von Tags wird sichergestellt, dass Geräte nur Benachrichtigungen für die registrierten Kategorien erhalten.

6. Ersetzen Sie im Code oben die Platzhalter `<hub name>` und `<connection string with full access>` durch den Namen Ihres Notification Hubs und die Verbindungszeichenfolge für *DefaultFullSharedAccessSignature* aus dem Dashboard für Ihren Notification Hub.

7. Fügen Sie die folgenden Zeilen zur **Main**-Methode hinzu:

         SendTemplateNotificationAsync();
		 Console.ReadLine();

8. Erstellen Sie die Konsolenanwendung.

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
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/library/jj927172.aspx
[Notification Hubs REST interface]: http://msdn.microsoft.com/library/windowsazure/dn223264.aspx

<!---HONumber=AcomDC_1217_2015-->