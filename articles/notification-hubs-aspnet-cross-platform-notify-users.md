<properties linkid="manage-services-notification-hubs-notify-users-xplat-aspnet" urlDisplayName="Notify Users xplat aspnet" pageTitle="Send cross-platform notifications to users with Notification Hubs (ASP.NET)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

Senden plattformübergreifender Benachrichtigungen an Benutzer mit Notification Hubs
===================================================================================

[Mobile Services](/de-de/manage/services/notification-hubs/notify-users-xplat-mobile-services/ "Mobile Services")[ASP.NET](/de-de/manage/services/notification-hubs/notify-users-xplat-aspnet/ "ASP.NET")

Im vorherigen Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs](/de-de/manage/services/notification-hubs/notify-users-aspnet) haben Sie gelernt, wie Sie Pushbenachrichtigungen an alle Geräte eines bestimmten authentifizierten Benutzers senden können. Dieses Lernprogramm benötigte mehrere Anfragen für den Versand einer Benachrichtigung an jede unterstützte Client-Plattform. Notification Hubs unterstützen Vorlagen, mit denen Sie angeben können, wie ein bestimmtes Gerät Benachrichtigungen empfangen soll. Damit können Sie den Versand plattformübergreifender Benachrichtigungen vereinfachen. Dieser Artikel beschreibt den Einsatz von Vorlagen für den Versand einer plattformunabhängigen Benachrichtigung für alle Plattformen mit nur einer Anfrage. Weitere Informationen zu Vorlagen finden Sie unter [Benachrichtigungshubs im Überblick](http://go.microsoft.com/fwlink/p/?LinkId=317339).

**Hinweis**

Mit Notification Hubs kann ein Gerät mehrere Vorlagen mit demselben Tag registrieren. In diesem Fall werden bei einer eingehenden Nachricht für das entsprechende Tag mehrere Benachrichtigungen an das Gerät verschickt (eine pro Vorlage). Auf diese Weise können Sie dieselbe Nachricht in mehreren visuellen Darstellungen anzeigen, z. B. als Signal und als Popupbenachrichtigung in einer Windows Store-App.

Führen Sie die folgenden Schritte aus, um plattformunabhängige Benachrichtigungen mit Vorlagen zu verschicken:

1.  Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **Controllers** und öffnen Sie die Datei RegisterController.cs.

2.  Suchen Sie den Codeblock in der **Post**-Methode, der eine neue Registrierung erstellt, wenn der Wert von `updated` gleich **false** ist, und ersetzen Sie den Block durch den folgenden Code:

         if (!updated)
         {
             switch (platform)
             {
                 case "windows":
                     var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";
                     await hubClient.CreateWindowsTemplateRegistrationAsync(channelUri, template, new string[] { instId, userTag });
                     break;
                 case "ios":
                     template = "{ if (!updated)? {?     switch (platform)?     {?         case "windows":
                     var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";?             await hubClient.CreateWindowsTemplateRegistrationAsync(channelUri, template, new string[] { instId, userTag });?             break;?         case "ios":
                     template = "{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}";?             await hubClient.CreateAppleTemplateRegistrationAsync(deviceToken, template, new string[] { instId, userTag });?             break;?     } ? }
        quot;aps if (!updated)? {?     switch (platform)?     {?         case "windows":
                     var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";?             await hubClient.CreateWindowsTemplateRegistrationAsync(channelUri, template, new string[] { instId, userTag });?             break;?         case "ios":
                     template = "{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}";?             await hubClient.CreateAppleTemplateRegistrationAsync(deviceToken, template, new string[] { instId, userTag });?             break;?     } ? }
        quot;:{ if (!updated)? {?     switch (platform)?     {?         case "windows":
                     var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";?             await hubClient.CreateWindowsTemplateRegistrationAsync(channelUri, template, new string[] { instId, userTag });?             break;?         case "ios":
                     template = "{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}";?             await hubClient.CreateAppleTemplateRegistrationAsync(deviceToken, template, new string[] { instId, userTag });?             break;?     } ? }
        quot;alert if (!updated)? {?     switch (platform)?     {?         case "windows":
                     var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";?             await hubClient.CreateWindowsTemplateRegistrationAsync(channelUri, template, new string[] { instId, userTag });?             break;?         case "ios":
                     template = "{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}";?             await hubClient.CreateAppleTemplateRegistrationAsync(deviceToken, template, new string[] { instId, userTag });?             break;?     } ? }
        quot;: if (!updated)? {?     switch (platform)?     {?         case "windows":
                     var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";?             await hubClient.CreateWindowsTemplateRegistrationAsync(channelUri, template, new string[] { instId, userTag });?             break;?         case "ios":
                     template = "{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}";?             await hubClient.CreateAppleTemplateRegistrationAsync(deviceToken, template, new string[] { instId, userTag });?             break;?     } ? }
        quot;$(message) if (!updated)? {?     switch (platform)?     {?         case "windows":
                     var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";?             await hubClient.CreateWindowsTemplateRegistrationAsync(channelUri, template, new string[] { instId, userTag });?             break;?         case "ios":
                     template = "{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}";?             await hubClient.CreateAppleTemplateRegistrationAsync(deviceToken, template, new string[] { instId, userTag });?             break;?     } ? }
        quot;},  if (!updated)? {?     switch (platform)?     {?         case "windows":
                     var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";?             await hubClient.CreateWindowsTemplateRegistrationAsync(channelUri, template, new string[] { instId, userTag });?             break;?         case "ios":
                     template = "{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}";?             await hubClient.CreateAppleTemplateRegistrationAsync(deviceToken, template, new string[] { instId, userTag });?             break;?     } ? }
        quot;inAppMessage if (!updated)? {?     switch (platform)?     {?         case "windows":
                     var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";?             await hubClient.CreateWindowsTemplateRegistrationAsync(channelUri, template, new string[] { instId, userTag });?             break;?         case "ios":
                     template = "{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}";?             await hubClient.CreateAppleTemplateRegistrationAsync(deviceToken, template, new string[] { instId, userTag });?             break;?     } ? }
        quot;: if (!updated)? {?     switch (platform)?     {?         case "windows":
                     var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";?             await hubClient.CreateWindowsTemplateRegistrationAsync(channelUri, template, new string[] { instId, userTag });?             break;?         case "ios":
                     template = "{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}";?             await hubClient.CreateAppleTemplateRegistrationAsync(deviceToken, template, new string[] { instId, userTag });?             break;?     } ? }
        quot;$(message) if (!updated)? {?     switch (platform)?     {?         case "windows":
                     var template = @"<toast><visual><binding template=""ToastText01""><text id=""1"">$(message)</text></binding></visual></toast>";?             await hubClient.CreateWindowsTemplateRegistrationAsync(channelUri, template, new string[] { instId, userTag });?             break;?         case "ios":
                     template = "{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}";?             await hubClient.CreateAppleTemplateRegistrationAsync(deviceToken, template, new string[] { instId, userTag });?             break;?     } ? }
        quot;}";
                     await hubClient.CreateAppleTemplateRegistrationAsync(deviceToken, template, new string[] { instId, userTag });
                     break;
             } 
         }

    Dieser Code ruft die plattformspezifische Methode zur Erstellung einer Vorlagenregistrierung anstelle der Systemregistrierung auf. Existierende Registrierungen müssen nicht verändert werden, da Vorlagenregistrierungen von Systemregistrierungen abgeleitet sind.

3.  Ersetzen Sie die **sendNotification**-Methode durch den folgenden Code:

         // Plattformunabhängige Benachrichtigung mit Vorlagen senden. 
         private async Task sendNotification(string notificationText, string tag)
         {           
                 var notification = new Dictionary<string, string> { { "message", "Hello, " + tag } };
                 await hubClient.SendTemplateNotificationAsync(notification, tag);        
         }

    Dieser Code sendet eine Benachrichtigung an alle Plattformen gleichzeitig, ohne dass eine System-Nutzlast angegeben werden muss. Notification Hubs erstellen und senden die korrekte Nutzlast an alle Geräte mit dem angegebenen *tag*-Wert entsprechend der registrierten Vorlagen.

4.  Führen Sie die Client-App erneut aus und vergewissern Sie sich, dass die Registrierung erfolgreich.

5.  (Optional) Stellen Sie die Client-App auf einem zweiten Gerät bereit und führen Sie die App aus.

    Beachten Sie, dass auf jedem Gerät eine Benachrichtigung angezeigt wird.

Nächste Schritte
----------------

Nach Abschluss dieses Lernprogramms finden Sie weitere Informationen über Notification Hubs und Vorlagen in den folgenden Themen:

-   **Verwenden von Notification Hubs zum Senden von Nachrichten ([Windows Store C\#](/de-de/manage/services/notification-hubs/breaking-news-dotnet) / [iOS](/de-de/manage/services/notification-hubs/breaking-news-dotnet))**
    Demonstriert ein weiteres Szenario für Vorlagen

-   **[Benachrichtigungshubs im Überblick](http://go.microsoft.com/fwlink/p/?LinkId=317339)**
    Dieser Übersichtsartikel enthält weitere Detailinformationen zu Vorlagen.

-   **[Notification Hub How to for Windows Store](http://msdn.microsoft.com/de-de/library/windowsazure/jj927172.aspx)** (in englischer Sprache)
    Enthält eine Sprachreferenz für Vorlagen.


