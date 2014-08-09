<properties linkid="manage-services-notification-hubs-notify-users-xplat-mobile-services" urlDisplayName="notify users xplat mobile services" pageTitle="Send cross-platform notifications to users with Notification Hubs (Mobile Services)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

Senden plattformübergreifender Benachrichtigungen an Benutzer mit Notification Hubs
===================================================================================

[Mobile Services](/de-de/manage/services/notification-hubs/notify-users-xplat-mobile-services/ "Mobile Services") [ASP.NET](/de-de/manage/services/notification-hubs/notify-users-xplat-aspnet/ "ASP.NET")

Im vorherigen Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs](/de-de/manage/services/notification-hubs/notify-users) haben Sie gelernt, wie Sie Pushbenachrichtigungen an alle Geräte eines bestimmten authentifizierten Benutzers senden können. Dieses Lernprogramm benötigte mehrere Anfragen für den Versand einer Benachrichtigung an jede unterstützte Client-Plattform. Notification Hubs unterstützen Vorlagen, mit denen Sie angeben können, wie ein bestimmtes Gerät Benachrichtigungen empfangen soll. Damit können Sie den Versand plattformübergreifender Benachrichtigungen vereinfachen. Dieser Artikel beschreibt den Einsatz von Vorlagen für den Versand einer plattformunabhängigen Benachrichtigung für alle Plattformen mit nur einer Anfrage. Weitere Informationen zu Vorlagen finden Sie unter [Benachrichtigungshubs im Überblick](http://go.microsoft.com/fwlink/p/?LinkId=317339).

**Hinweis**

Mit Notification Hubs kann ein Gerät mehrere Vorlagen mit demselben Tag registrieren. In diesem Fall werden bei einer eingehenden Nachricht für das entsprechende Tag mehrere Benachrichtigungen an das Gerät verschickt (eine pro Vorlage). Auf diese Weise können Sie dieselbe Nachricht in mehreren visuellen Darstellungen anzeigen, z. B. als Signal und als Popupbenachrichtigung in einer Windows Store-App.

Führen Sie die folgenden Schritte aus, um plattformunabhängige Benachrichtigungen mit Vorlagen zu verschicken:

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an. Klicken Sie auf **Mobile Services** und dann auf Ihre App.

   	![][0]

2.  Klicken Sie auf die Registerkarte **API** und anschließend auf den Eintrag **register\_notifications** in der API-Tabelle.

   	![](./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-custom-api-select.png)

3.  Klicken Sie auf die Registerkarte **Script**, suchen Sie nach dem **else**-Block, der die neue Registrierung erstellt, wenn der Wert von `existingRegs` **false** ist, und ersetzen Sie ihn durch den folgenden Code:

         else {
             // Neue Registrierung erstellen.
             var template;
             if (platform === 'win8') {                
                 template = { text1: '$(message)' };              
                 hub.wns.createToastText01Registration(request.body.channelUri, 
                 [userId, installationId], template, registrationComplete);
             } else if (platform === 'ios') {
                 template = '{\"aps\":{\"alert\":\"$(message)\"}, \"inAppMessage\":\"$(message)\"}';
                 hub.apns.createTemplateRegistration(request.body.deviceToken,
                 [userId, installationId], template, registrationComplete);
                 } else {
                 response.send(500, 'Unknown client.');
            }
        }

    Dieser Code ruft die plattformspezifische Methode zur Erstellung einer Vorlagenregistrierung anstelle der Systemregistrierung auf. Existierende Registrierungen müssen nicht verändert werden, da Vorlagenregistrierungen von Systemregistrierungen abgeleitet sind.

4.  Klicken Sie auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

   	![][2]

5.  Klicken Sie in **todoitem** auf die Registerkarte **Script**, wählen Sie **Insert** aus und ersetzen Sie die **insert**-Funktion durch den folgenden Code:

   	![](./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-insert-script-push2.png)

   	Daraufhin wird die Funktion angezeigt, die aufgerufen wird, wenn etwas in die Tabelle **TodoItem** eingefügt wird.

1.  Ersetzen Sie die Einfügefunktion durch den folgenden Code:

         function insert(item, user, request) {
             var azure = require('azure');
             var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
             '<FULL_SAS_CONNECTION_STRING>');
            
             // Anfrage ausführen und Benachrichtigungen senden.
             request.execute({
                 success: function() {
                     // Standardantwort schreiben und Benachrichtigung an den
                     // Benutzer auf allen Geräten mithilfe des userId-Tags senden.
                     request.respond();
                     // Vorlagenbasierte Nutzlast erstellen.
                     var payload = '{ "message" : "New item added: ' + item.text + '" }';            
                     // Benachrichtigung an den aktuellen Benutzer auf allen Plattformen senden. 
                     hub.send(user.userId, payload,  
                     function(error, outcome){
                         // Ausgabe bzw. Fehler verarbeiten.
                     });     
                 }
             });
         }

    Dieser Code sendet eine Benachrichtigung an alle Plattformen gleichzeitig, ohne dass eine System-Nutzlast angegeben werden muss. Notification Hubs erstellen und senden die korrekte Nutzlast an alle Geräte mit dem angegebenen *tag*-Wert entsprechend der registrierten Vorlagen.

2.  Aktualisieren Sie das Skript, und ersetzen Sie *`<NOTIFICATION_HUB_NAME>`* und *`<FULL_SAS_CONNECTION_STRING>`* mit den Werten für Ihren Benachrichtigungshub, und klicken Sie dann auf **Save**.

3.  Beenden Sie den Geräte-Emulator oder deinstallieren Sie die existierende Client-App vom Gerät.

    So wird sichergestellt, dass vom Mobile Services-Client eine neue Installations-ID generiert wird. Wenn Sie dies nicht tun, versucht der Dienst, die existierende Registrierung ohne Vorlage zu verwenden.

4.  Erstellen Sie die Client-App erneut, führen Sie diese aus und vergewissern Sie sich, dass die Registrierung erfolgreich ist und eine neue Registrierungs-ID angezeigt wird.

5.  Geben Sie wie zuvor Text ein, und fügen Sie ein neues Element hinzu.

    Bitte beachten Sie, dass die App nach dem Einfügen eine Pushbenachrichtigung von Notification Hubs erhält.

6.  (Optional) Stellen Sie die Client-App auf einem zweiten Gerät bereit, führen Sie die App aus, und fügen Sie Text ein.

    Beachten Sie, dass auf jedem Gerät eine Benachrichtigung angezeigt wird.

Nächste Schritte
----------------

Nach Abschluss dieses Lernprogramms finden Sie weitere Informationen über Notification Hubs und Vorlagen in den folgenden Themen:

-   **Verwenden von Notification Hubs zum Senden von Nachrichten ([Windows Store C\#](/de-de/manage/services/notification-hubs/breaking-news-dotnet) / [iOS](/de-de/manage/services/notification-hubs/breaking-news-ios))**
    Demonstriert ein weiteres Szenario für Vorlagen

-   **[Benachrichtigungshubs im Überblick](http://go.microsoft.com/fwlink/p/?LinkId=317339)**
    Dieser Übersichtsartikel enthält weitere Detailinformationen zu Vorlagen.

-   **[Notification Hub How to for Windows Store](http://msdn.microsoft.com/de-de/library/windowsazure/jj927172.aspx)** (in englischer Sprache)
    Enthält eine Sprachreferenz für Vorlagen.



<!-- Images. -->
[0]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-services-selection.png
[1]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-custom-api-select.png
[2]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-portal-data-tables.png
[3]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-insert-script-push2.png

