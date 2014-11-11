<properties linkid="manage-services-notification-hubs-notify-users-xplat-mobile-services" urlDisplayName="notify users xplat mobile services" pageTitle="Send cross-platform notifications to users with Notification Hubs (Mobile Services)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="multiple" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Senden plattformübergreifender Benachrichtigungen an Benutzer mit Notification Hubs

<div class="dev-center-tutorial-selector sublanding">
    <a href="/de-de/documentation/articles/notification-hubs-mobile-services-cross-platform-notify-users/" title="Mobile Services" class="current">Mobile Services</a>
    <a href="/de-de/documentation/articles/notification-hubs-aspnet-cross-platform-notify-users/" title="ASP.NET">ASP.NET</a>
</div>

Im vorherigen Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs][Benachrichtigen von Benutzern mit Notification Hubs] haben Sie gelernt, wie Sie Pushbenachrichtigungen an alle Geräte eines bestimmten authentifizierten Benutzers senden können. Dieses Lernprogramm benötigte mehrere Anfragen für den Versand einer Benachrichtigung an jede unterstützte Client-Plattform. Notification Hubs unterstützen Vorlagen, mit denen Sie angeben können, wie ein bestimmtes Gerät Benachrichtigungen empfangen soll. Damit können Sie den Versand plattformübergreifender Benachrichtigungen vereinfachen. Dieser Artikel beschreibt den Einsatz von Vorlagen für den Versand einer plattformunabhängigen Benachrichtigung für alle Plattformen mit nur einer Anfrage. Weitere Informationen zu Vorlagen finden Sie unter [Benachrichtigungshubs im Überblick][Benachrichtigungshubs im Überblick].

<div class="dev-callout"><b>Hinweis</b>
    <p>Mit Notification Hubs kann ein Ger&auml;t mehrere Vorlagen mit demselben Tag registrieren. In diesem Fall werden bei einer eingehenden Nachricht f&uuml;r das entsprechende Tag mehrere Benachrichtigungen an das Ger&auml;t verschickt (eine pro Vorlage). Auf diese Weise k&ouml;nnen Sie dieselbe Nachricht in mehreren visuellen Darstellungen anzeigen, z. B. als Signal und als Popupbenachrichtigung in einer Windows Store-App.</p>
</div>

Führen Sie die folgenden Schritte aus, um plattformunabhängige Benachrichtigungen mit Vorlagen zu verschicken:

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an. Klicken Sie auf **Mobile Services** und dann auf Ihre App.

    ![][0]

2.  Klicken Sie auf die Registerkarte **API** und anschließend auf den Eintrag **register\_notifications** in der API-Tabelle.

    ![][1]

3.  Klicken Sie auf die Registerkarte **Skript**, suchen Sie nach dem Block **else**, der eine neue Registrierung erstellt, wenn der Wert von `existingRegs` **false** ist, und ersetzen Sie ihn durch den folgenden Code:

        else {
            // Create a new registration.
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

5.  Klicken Sie in **todoitem** auf die Registerkarte **Skript**, wählen Sie **Insert** aus und ersetzen Sie die **insert**-Funktion durch den folgenden Code:

    ![][3]

    Daraufhin wird die Funktion angezeigt, die aufgerufen wird, wenn etwas in die Tabelle **TodoItem** eingefügt wird.

6.  Ersetzen Sie die Einfügefunktion durch den folgenden Code:

        function insert(item, user, request) {
            var azure = require('azure');
            var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
            '<FULL_SAS_CONNECTION_STRING>');

            // Execute the request and send notifications.
            request.execute({
                success: function() {
                    // Write the default response and send a notification 
                    // to the user on all devices by using the userId tag.
                    request.respond();
                    // Create a template-based payload.
                    var payload = '{ "message" : "New item added: ' + item.text + '" }';            
                    // Send a notification to the current user on all platforms. 
                    hub.send(user.userId, payload,  
                    function(error, outcome){
                        // Do something here with the outcome or error.
                    });     
                }
            });
        }

    Dieser Code sendet eine Benachrichtigung an alle Plattformen gleichzeitig, ohne dass eine System-Nutzlast angegeben werden muss. Notification Hubs erstellen und senden die korrekte Nutzlast an alle Geräte mit dem angegebenen *tag*-Wert entsprechend der registrierten Vorlagen.

7.  Aktualisieren Sie das Skript, indem Sie *`<NOTIFICATION_HUB_NAME>`* und *`<FULL_SAS_CONNECTION_STRING>`* durch die Werte für Ihren Notification Hub ersetzen, und klicken Sie dann auf **Speichern**.

8.  Beenden Sie den Geräte-Emulator oder deinstallieren Sie die existierende Client-App vom Gerät.

    So wird sichergestellt, dass vom Mobile Services-Client eine neue Installations-ID generiert wird. Wenn Sie dies nicht tun, versucht der Dienst, die existierende Registrierung ohne Vorlage zu verwenden.

9.  Erstellen Sie die Client-App erneut, führen Sie diese aus und vergewissern Sie sich, dass die Registrierung erfolgreich ist und eine neue Registrierungs-ID angezeigt wird.

10. Geben Sie wie zuvor Text ein, und fügen Sie ein neues Element hinzu.

    Bitte beachten Sie, dass die App nach dem Einfügen eine Pushbenachrichtigung von Notification Hubs erhält.

11. (Optional) Stellen Sie die Client-App auf einem zweiten Gerät bereit, führen Sie die App aus, und fügen Sie Text ein.

    Beachten Sie, dass auf jedem Gerät eine Benachrichtigung angezeigt wird.

## Nächste Schritte

Nach Abschluss dieses Lernprogramms finden Sie weitere Informationen über Notification Hubs und Vorlagen in den folgenden Themen:

-   **Verwenden von Notification Hubs zum Senden von Nachrichten ([Windows Store C#][Windows Store C#] / [iOS][iOS])**
    Demonstriert ein weiteres Szenario für Vorlagen

-   **[Benachrichtigungshubs im Überblick][Benachrichtigungshubs im Überblick]**
    Dieser Übersichtsartikel enthält weitere Detailinformationen zu Vorlagen.

-   **[Notification Hub-Informationen für Windows Store][Notification Hub-Informationen für Windows Store]**
    Enthält eine Referenz für die Vorlagen-Ausdruckssprache.

<!-- Anchors. -->
<!-- Images. -->
<!-- URLs. -->

  [Benachrichtigen von Benutzern mit Notification Hubs]: /de-de/manage/services/notification-hubs/notify-users
  [Benachrichtigungshubs im Überblick]: http://go.microsoft.com/fwlink/p/?LinkId=317339
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [0]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-services-selection.png
  [1]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-custom-api-select.png
  [2]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-portal-data-tables.png
  [3]: ./media/notification-hubs-mobile-services-cross-platform-notify-users/mobile-insert-script-push2.png
  [Windows Store C#]: /de-de/manage/services/notification-hubs/breaking-news-dotnet
  [iOS]: /de-de/manage/services/notification-hubs/breaking-news-ios
  [Notification Hub-Informationen für Windows Store]: http://msdn.microsoft.com/de-de/library/windowsazure/jj927172.aspx
