<properties urlDisplayName="Notify Users xplat aspnet" pageTitle="Senden plattformübergreifender Benachrichtigungen an Benutzer mit Notification Hubs (ASP.NET)" metaKeywords="" description="Learn how to use Notification Hubs templates to send, in a single request, a platform-agnostic notification that targets all platforms." metaCanonical="" services="notification-hubs" documentationCenter="" title="Send cross-platform notifications to users with Notification Hubs" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-multiple" ms.devlang="dotnet" ms.topic="article" ms.date="11/22/2014" ms.author="glenga" />
# Senden plattformübergreifender Benachrichtigungen an Benutzer mit Notification Hubs


Im vorherigen Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs] haben Sie erfahren, wie Sie Pushbenachrichtigungen an alle Geräte eines bestimmten authentifizierten Benutzers senden können. Dieses Lernprogramm benötigte mehrere Anfragen für den Versand einer Benachrichtigung an jede unterstützte Client-Plattform. Notification Hubs unterstützen Vorlagen, mit denen Sie angeben können, wie ein bestimmtes Gerät Benachrichtigungen empfangen soll. Damit können Sie den Versand plattformübergreifender Benachrichtigungen vereinfachen. Dieser Artikel beschreibt den Einsatz von Vorlagen für den Versand einer plattformunabhängigen Benachrichtigung für alle Plattformen mit nur einer Anfrage. Ausführlichere Informationen zu Vorlagen finden Sie unter [Überblick über Azure Notification Hubs][Templates].

<div class="dev-callout"><b>Hinweis</b>
	<p>Mit Notification Hubs kann ein Gerät mehrere Vorlagen mit demselben Tag registrieren. In diesem Fall werden bei einer eingehenden Nachricht für das entsprechende Tag mehrere Benachrichtigungen an das Gerät verschickt (eine pro Vorlage). Auf diese Weise können Sie dieselbe Nachricht in mehreren visuellen Darstellungen anzeigen, z. B. als Signal und als Popupbenachrichtigung in einer Windows Store-App.</p>
</div>

Führen Sie die folgenden Schritte aus, um plattformunabhängige Benachrichtigungen mit Vorlagen zu verschicken:

1. Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **Controllers**, und öffnen Sie dann die RegisterController.cs-Datei. 

2. Suchen Sie den Codeblock in der Methode **Post**, mit dem eine neue Registrierung erstellt wird, und ersetzen Sie den Inhalt "switch" durch den folgenden Code:

		switch (deviceUpdate.Platform)
        {
            case "mpns":
                var toastTemplate = "<?xml version=\"1.0\" encoding=\"utf-8\"?>" +
                    "<wp:Notification xmlns:wp=\"WPNotification\">" +
                       "<wp:Toast>" +
                            "<wp:Text1>$(message)</wp:Text1>" +
                       "</wp:Toast> " +
                    "</wp:Notification>";
                registration = new MpnsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "wns":

                registration = new WindowsTemplateRegistrationDescription(deviceUpdate.Handle, toastTemplate);
                break;
            case "apns":
                var alertTemplate = "{\"aps\":{\"alert\":\"$(message)\"}}";
                registration = new AppleTemplateRegistrationDescription(deviceUpdate.Handle, alertTemplate);
                break;
            case "gcm":
                var messageTemplate = "{\"data\":{\"msg\":\"$(message)\"}}";
                registration = new GcmTemplateRegistrationDescription(deviceUpdate.Handle, messageTemplate);
                break;
            default:
                throw new HttpResponseException(HttpStatusCode.BadRequest);
        }
	
	Dieser Code ruft die plattformspezifische Methode zur Erstellung einer Vorlagenregistrierung anstelle der Systemregistrierung auf. Existierende Registrierungen müssen nicht verändert werden, da Vorlagenregistrierungen von Systemregistrierungen abgeleitet sind.

3. Ersetzen Sie im **Notifications**-Controller die Methode **sendNotification** durch den folgenden Code:

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:" + user;

            var notification = new Dictionary<string, string> { { "message", "Hello, " + user } };
            await Notifications.Instance.Hub.SendTemplateNotificationAsync(notification, userTag);   

            return Request.CreateResponse(HttpStatusCode.OK);
        }

	Dieser Code sendet eine Benachrichtigung an alle Plattformen gleichzeitig, ohne dass eine System-Nutzlast angegeben werden muss. Notification Hubs erstellen und senden die korrekte Nutzlast an alle Geräte mit dem angegebenen "tag"-Wert gemäß den registrierten Vorlagen.

4. Veröffentlichen Sie Ihr WebAPI-Back-End-Projekt erneut.

5. Führen Sie die Client-App erneut aus und vergewissern Sie sich, dass die Registrierung erfolgreich.

6. (Optional) Stellen Sie die Client-App auf einem zweiten Gerät bereit und führen Sie die App aus. 

	Beachten Sie, dass auf jedem Gerät eine Benachrichtigung angezeigt wird.

## Nächste Schritte

Nach Abschluss dieses Lernprogramms finden Sie weitere Informationen über Notification Hubs und Vorlagen in den folgenden Themen:

+ **[Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten]** <br/>Stellt ein weiteres Szenario für die Verwendung von Vorlagen dar. 

+  **[Überblick über Azure Notification Hubs][Templates]**<br/>Das Übersichtsthema bietet ausführlichere Informationen zu Vorlagen.

+  **[Notification Hub-Anleitung für Windows Store]**<br/>Enthält eine Vorlagen-Ausdruckssprachreferenz.



<!-- Anchors. -->

<!-- Images. -->




<!-- URLs. -->
[Pushbenachrichtigungen an Benutzer - ASP.NET]: /de-de/manage/services/notification-hubs/notify-users-aspnet
[Pushbenachrichtigungen an Benutzer - Mobile Services]: /de-de/manage/services/notification-hubs/notify-users/
[Visual Studio 2012 Express für Windows 8]: http://go.microsoft.com/fwlink/?LinkId=257546

[Verwaltungsportal]: https://manage.windowsazure.com/
[Verwenden von Notification Hubs zum Übermitteln von aktuellen Nachrichten]: /de-de/documentation/articles/notification-hubs-windows-store-dotnet-send-breaking-news/
[Azure Notification Hubs]: http://go.microsoft.com/fwlink/p/?LinkId=314257
[Benachrichtigen von Benutzern mit Notification Hubs]: /de-de/documentation/articles/notification-hubs-aspnet-backend-windows-dotnet-notify-users/
[Vorlagen]: http://go.microsoft.com/fwlink/p/?LinkId=317339
[Notification Hub-Anleitung für Windows Store]: http://msdn.microsoft.com/de-de/library/windowsazure/jj927172.aspx

<!--HONumber=35_1-->
