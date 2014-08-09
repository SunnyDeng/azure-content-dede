Dieser Abschnitt zeigt, wie Benachrichtigungen auf zwei unterschiedliche Weisen gesendet werden:

* [Von einer Konsolenanwendung aus](#console)
* [Von Mobile Services aus](#mobile-services)

Beide Back-Ends senden Benachrichtigungen an Windows Store und iOS-Geräte. Sie können Benachrichtigungen von beliebigen Back-Ends mithilfe der [Notification Hubs-REST-Schnittstelle][1] senden.

<h3><a name="console"></a>Senden von Benachrichtigungen von einer Konsolenanwendung aus in C#</h3>


Überspringen Sie die Schritte 1 bis 3, wenn Sie eine Konsolenanwendung erstellt haben, als Sie [Erste Schritte mit Notification Hubs](/de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/) abgeschlossen haben.

1.  Erstellen Sie in Visual Studio eine neue Visual C#-Konsolenanwendung
    
	![][13]

2.  Klicken Sie im Visual Studio-Hauptmenü auf **Extras**, **Bibliothekspaket-Manager** und **Paket-Managerkonsole**. Geben Sie dann in das Konsolenfenster Folgendes ein und drücken Sie die **Eingabetaste**:
    
         Install-Package WindowsAzure.ServiceBus
    
    Dadurch wird dem Azure Service Bus-SDK mithilfe des [WindowsAzure.ServiceBus NuGet-Pakets][2] ein Verweis hinzugefügt.

3.  Öffnen Sie die Datei "Program.cs" und fügen Sie die folgende `using`-Anweisung hinzu:
    
		using Microsoft.ServiceBus.Notifications;

4.  Fügen Sie der Klasse `Program` folgende Methode hinzu oder ersetzen Sie sie, falls sie bereits vorhanden ist:
    
         private static async void SendNotificationAsync()
         {
             // Benachrichtigungshub definieren
             NotificationHubClient hub = 
                 NotificationHubClient.CreateClientFromConnectionString(
                     "<connection  string with full access>", "<hub  name>");
        	
             // Array für Nachrichtenkategorien erstellen
             var categories = new string[] { "World", "Politics", "Business", 
                 "Technology", "Science", "Sports"};
        	
             foreach (var category in categories)
             {
                 try
                 {
                     // Windows Store-Toast definieren
                     var wnsToast = "<toast><visual><binding  template=\"ToastText01\">" 
                         + "<text  id=\"1\">Breaking " + category + " News!" 
                         + "</text></binding></visual></toast>";
                     // WNS-Benachrichtigung mithilfe der Vorlage senden            
                     await hub.SendWindowsNativeNotificationAsync(wnsToast, category);
        
                     // Windows Phone-Toast definieren
                     var mpnsToast =
                         "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                         "<wp :Notification xmlns:wp=\"WPNotification\">" +
                             "<wp :Toast>" +
                                 "<wp :Text1>Breaking " + category + " News!</wp:Text1>" +
                             "</wp:Toast> " +
                         "</wp:Notification>";
                     // MPNS-Benachrichtigung mithilfe der Vorlage senden            
                     await hub.SendMpnsNativeNotificationAsync(mpnsToast, category);
        
                     // iOS-Warnung definieren
                     var alert = "{\"aps\":{\"alert\":\"Breaking " + category + " News!\"}}";
                     // APNS-Benachrichtigung mithilfe der Vorlage senden
                     await hub.SendAppleNativeNotificationAsync(alert, category);
                 }
                 catch (ArgumentException)
                 {
                     // Eine Ausnahme wird ausgelöst, wenn der Benachrichtigungshub nicht 
                     // Für die iOS-, Windows Store- oder Windows Phone-Plattform registriert wurde. 
                 }
             }
          }
    
    Dieser Code sendet Benachrichtigungen für jeden der sechs Tags im Zeichenfolgenarray an Windows Store-, Windows Phone- und iOS-Geräte. Durch die Verwendung von Tags wird sichergestellt, dass Geräte nur Benachrichtigungen für die registrierten Kategorien erhalten.

	<div class="dev-callout"><b>Hinweis</b><p>Dieser Back-End-Code unterstützt Windows Store-, Windows Phone- und
	iOS-Clients. Send-Methoden geben einen Fehler zurück, wenn der Benachrichtigungshub noch nicht für eine bestimmte Clientplattform konfiguriert wurde.</p></div>


5.  Ersetzen Sie im Code oben die Platzhalter `<hub name>` und `<connection  string with full access>` durch den Namen Ihres Benachrichtigungshubs und die Verbindungszeichenfolge für *DefaultFullSharedAccessSignature*, die Sie zuvor erhalten haben.

6.  Fügen Sie die folgenden Zeilen zur **Main**-Methode hinzu:
    
          SendNotificationAsync();
          Console.ReadLine();

Sie können jetzt mit [Ausführen der Anwendung und Erzeugen von Benachrichtigungen](#test-app) fortfahren.

### <a name="mobile-services"></a>Senden von Benachrichtigungen von Mobile Services aus

Um eine Benachrichtigung mithilfe eines mobilen Dienstes zu senden, gehen Sie folgendermaßen vor:

1.  Schließen Sie das Lernprogramm [Erste Schritte mit Mobile Services](/de-de/develop/mobile/tutorials/get-started/#create-new-service) ab, um Ihren mobilen Dienst zu erstellen.

2.  Melden Sie sich beim [Azure-Verwaltungsportal][3] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihren mobilen Dienst.

3.  Klicken Sie auf der Registerkarte **Scheduler** auf **Weiter**.
    
	![][15]

4.  Geben Sie in **Neuen Auftrag erstellen** einen Namen ein, wählen Sie **On demand** aus und klicken Sie auf das Häkchen, um zu akzeptieren.
    
	![][16]

5.  Wenn der Auftrag erstellt wurde, klicken Sie auf den Auftragsnamen. Fügen Sie dann auf der Registerkarte **Skript** das folgende Skript innerhalb der geplanten Auftragsfunktion ein:
    
        var azure = require('azure');
         var notificationHubService = azure.createNotificationHubService(
                 '<hub  name>', 
                 '<connection  string with full access>');
        
        	 // Array für Nachrichtenkategorien erstellen
             var categories = ['World', 'Politics', 'Business', 'Technology', 'Science', 'Sports'];
             for (var i = 0; i < categories.length; i++) {
                 // WNS-Benachrichtigung senden
                 notificationHubService.wns.sendToastText01(categories[i], {
                     text1: 'Breaking ' + categories[i] + ' News!'
                 }, sendComplete);
                 // MPNS-Benachrichtigung senden
                 notificationHubService.mpns.sendToast(categories[i], {
                     text1: 'Breaking ' + categories[i] + ' News!'
                 }, sendComplete);
                 // APNS-Benachrichtigung senden
                 notificationHubService.apns.send(categories[i], {
                     alert: 'Breaking ' + categories[i] + ' News!'
                 }, sendComplete);
             }
    
    Dieser Code sendet Benachrichtigungen für jeden der sechs Tags im Zeichenfolgenarray an Windows Store-, Windows Phone- und iOS-Geräte. Durch die Verwendung von Tags wird sichergestellt, dass Geräte nur Benachrichtigungen für die registrierten Kategorien erhalten.

6.  Ersetzen Sie im Code oben die Platzhalter `<hub name>` und `<connection  string with full access>` durch den Namen Ihres Benachrichtigungshubs und die Verbindungszeichenfolge für *DefaultFullSharedAccessSignature*, die Sie zuvor erhalten haben.

7.  Fügen Sie die folgende Hilfefunktion nach der geplanten Auftragsfunktion hinzu und klicken Sie dann auf **Speichern**:
    
		function sendComplete(error) { 
   			if (error) \{
				// Eine Ausnahme wird aufgelöst, wenn keine Geräte für die 
				// iOS-, Windows Store- oder Windows Phone-Plattformen registriert wurden. Stattdessen können Sie 
				// Vorlagenbenachrichtigungen verwenden.
				//console.warn("Notification failed:" + error);
     		 }
		}

	<div class="dev-callout"><b>Hinweis</b>
		<p>Dieser Code unterstützt Windows Store-, Windows Phone- und iOS-Clients. Send-Methoden geben einen Fehler zurück, wenn eine Registrierung für eine bestimmte Plattform nicht vorhanden ist. Um dies zu vermeiden, können Sie Vorlagenregistrierungen verwenden, um eine einzelne Benachrichtigung an mehrere Plattformen zusenden. Ein Beispiel finden Sie im Thema zum [Verwenden von Notification Hubs zum Übertragen lokalisierter Nachrichten](/de-de/manage/services/notification-hubs/breaking-news-localized-dotnet/).</p>
	</div>


Sie können jetzt mit [Ausführen der Anwendung und Erzeugen von Benachrichtigungen](#test-app) fortfahren.

<!-- Anchors -->
[From a console app]: #console
[From Mobile Services]: #mobile-services
[Run the app and generate notifications]: #test-app


<!-- Images. -->
[13]: ./media/notification-hubs-back-end/notification-hub-create-console-app.png

[15]: ./media/notification-hubs-back-end/notification-hub-scheduler1.png
[16]: ./media/notification-hubs-back-end/notification-hub-scheduler2.png

<!-- URLs. -->



[1]: http://msdn.microsoft.com/de-de/library/windowsazure/dn223264.aspx
[2]: http://nuget.org/packages/WindowsAzure.ServiceBus/
[3]: https://manage.windowsazure.com/


[get-started]: /de-de/documentation/articles/notification-hubs-windows-store-dotnet-get-started/
[Use Notification Hubs to send notifications to users]: ../notificationhubs/tutorial-notify-users-mobileservices.md
[Get started with Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service
[Azure Management Portal]: https://manage.windowsazure.com/
[wns object]: http://go.microsoft.com/fwlink/p/?LinkId=260591
[Notification Hubs Guidance]: http://msdn.microsoft.com/de-de/library/jj927170.aspx
[Notification Hubs How-To for Windows Store]: http://msdn.microsoft.com/de-de/library/jj927172.aspx
[Notification Hubs REST interface]: http://msdn.microsoft.com/de-de/library/windowsazure/dn223264.aspx