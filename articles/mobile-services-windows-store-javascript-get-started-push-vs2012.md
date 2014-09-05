<properties linkid="develop-mobile-tutorials-get-started-with-push-js" urlDisplayName="Get Started with Push (JS)" pageTitle="Get started with push notifications (JavaScript)" metaKeywords="" description="Learn how to use push notifications in your Windows Store app with Azure Mobile Services." metaCanonical="http://www.windowsazure.com/de-de/develop/mobile/tutorials/get-started-with-push-dotnet/" disqusComments="1" umbracoNaviHide="1" title="Get started with push notifications in Mobile Services using Visual Studio 2012" documentationCenter="Mobile" authors="" />

Erste Schritte mit Pushbenachrichtigungen in Mobile Services mit Visual Studio 2012
===================================================================================

<div class="dev-center-tutorial-selector sublanding"> 
	<a href="/en-us/develop/mobile/tutorials/get-started-with-push-dotnet-vs2012" title="Windows Store C#">Windows Store C#</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-wp8" title="Windows Phone">Windows Phone</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-ios" title="iOS">iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-android" title="Android">Android</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-ios" title="Xamarin.iOS">Xamarin.iOS</a><a href="/en-us/develop/mobile/tutorials/get-started-with-push-xamarin-android" title="Xamarin.Android">Xamarin.Android</a> 
</div>

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services Pushbenachrichtigungen an eine Windows Store-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe des Windows Push Notification Services (WNS) zum Schnellstart-Projekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

<div class="dev-callout"><b>Hinweis</b>

<p>In diesem Lernprogramm werden Pushbenachrichtigungen zu einer in Visual Studio 2012 erstellten Windows Store-App hinzugefügt. Visual Studio 2013 umfasst neue Funktionen, die das Einrichten von Pushbenachrichtigungen in Ihrer Windows Store-App mithilfe von Mobile Services vereinfachen. Siehe <a href="/en-us/develop/mobile/tutorials/get-started-with-push-js">Erste Schritte mit Pushbenachrichtigungen</a> für die Visual Studio 2013-Version.</p>
</div>
In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1.  [Registrieren Ihrer App für Pushbenachrichtigungen und Konfigurieren von Mobile Services](#register)
2.  [Erstellen der Registrations-Tabelle](#create-table)
3.  [Hinzufügen von Pushbenachrichtigungen zur App](#add-push)
4.  [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen](#update-scripts)
5.  [Einfügen von Daten zum Empfangen von Benachrichtigungen](#test)

Für dieses Lernprogramm ist Folgendes erforderlich:

-   Microsoft Visual Studio 2012 Express für Windows 8
-   Ein aktives Windows Store-Konto

Dieses Lernprogramm baut auf das Lernprogramm [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-js) auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [dieses Lernprogramm](/en-us/develop/mobile/tutorials/get-started-with-data-js) abschließen.

<a name="register">
Registrieren Ihrer AppRegistrieren Ihrer App für den Windows Store
------------------------------------------------------------------

Sie müssen die App an den Windows Store übermitteln, um Pushbenachrichtigungen von Mobile Services an Windows Store-Apps senden zu können. Anschließend müssen Sie Ihren Mobile Service zur Integration mit WNS konfigurieren.

[WACOM.INCLUDE [mobile-services-register-windows-store-app](../includes/mobile-services-register-windows-store-app.md)]

Sowohl Ihr Mobile Service als auch Ihre App sind nun konfiguriert und funktionieren mit WNS. Als Nächstes werden Sie eine neue Tabelle erstellen, um die Registrierungen zu speichern.

<a name="create-table">
Erstellen einer neuen Tabelle
-----------------------------

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

<a name="add-push">
Hinzufügen von PushbenachrichtigungenHinzufügen von Pushbenachrichtigungen zu Ihrer App
---------------------------------------------------------------------------------------

1.  Öffnen Sie die Datei "default.js" und fügen Sie das folgende Codefragment in die **app.OnActivated**-Methodenüberladung nach der **args.setPromise**-Methode ein:

         // Get the channel for the application.
         var channel;
         var channelOperation = Windows.Networking.PushNotifications
             .PushNotificationChannelManager
             .createPushNotificationChannelForApplicationAsync()
             .then(function (newChannel) {
                 channel = newChannel;
             });

        Dieser Code ruft einen Pushbenachrichtigungschannel bei jedem Start der Anwendung ab und speichert ihn.

2.  Fügen Sie den folgenden Code nach dem Code ein, der die **MobileServiceClient**-Instanz erstellt:

         // Insert the new channel URI into the Registrations table.
         var registrationsTable = client.getTable('Registrations');
         registrationsTable.insert({ handle: channel.uri });         

    Dieser Code fügt den aktuellen Channel in die Registrierungstabelle ein.

3.  Öffnen Sie die Datei "Package.appxmanifest" und vergewissern Sie sich, dass **Toast Capable** in der Registerkarte **Application UI** auf **Yes** gesetzt ist.

	![][15]

	Damit stellen Sie sicher, dass Ihre App Popupbenachrichtigungen ausgeben kann. 

<a name="update-scripts">
Aktualisieren des EinfügeskriptsAktualisieren des registrierten Einfügeskripts im Verwaltungsportal
---------------------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

1.  Klicken Sie auf **TodoItem**, dann auf **Script**, und wählen Sie **Insert** aus.

	![][5]

2.  Ersetzen Sie die Einfügefunktion durch den folgenden Code und klicken Sie auf **Save**:

	    function insert(item, user, request) {
    	    request.execute({
        	    success: function() {
            	    request.respond();
            	    sendNotifications();
        	    }
    	    });

	        function sendNotifications() {
        	    var registrationsTable = tables.getTable('Registrations');
        	    registrationsTable.read({
            	    success: function(registrations) {
                	    registrations.forEach(function(registration) {
                    	    push.wns.sendToastText04(registration.handle, {
                        	    text1: item.text
                    	    }, {
                        	    success: function(pushResponse) {
                            	    console.log("Sent push:", pushResponse);
                        	    }
                    	    });
                	    });
            	    }
        	    });
    	    }
	    }

    Dieses Einfügeskript sendet eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags) an alle Kanäle, die in der **Registrations**-Tabelle gespeichert sind.

<a name="test">
Testen der AppTesten von Pushbenachrichtigungen in Ihrer App
------------------------------------------------------------

1.  Drücken Sie in Visual Studio die Taste F5, um die App auszuführen.

2.  Geben Sie in der App einen Text in **Insert a TodoItem** ein, und klicken Sie dann auf **Save**.

 	![][13]

	Bitte beachten Sie, dass die App nach dem Einfügen eine Pushbenachrichtigung von WNS erhält.

	![][14]

<a name="next-steps">
Nächste Schritte
----------------

In diesem Lernprogramm wurden die Grundlagen der von Mobile Services bereitgestellten Pushbenachrichtigungsfunktion gezeigt. Wenn Sie Ihrer App fortgeschrittenere Funktionen, wie z. B. Senden von plattformübergreifenden Benachrichtigungen, abonnementbasiertes Routing oder sehr große Mengen von Benachrichtigungen, hinzufügen möchten, sollten Sie Azure Notification Hubs mit Ihrem mobilen Dienst verwenden. Weitere Informationen finden Sie in einem der folgenden Themen zu Notification Hubs:

-   [Erste Schritte mit Notification Hubs](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet/)
    Lernen Sie, wie Sie Notification Hubs in Ihrer Windows Store-App einsetzen können.

-   [Senden von Benachrichtigungen an Abonnenten](/en-us/manage/services/notification-hubs/breaking-news-dotnet/)
    Lernen Sie, wie Benutzer sich registrieren und Pushbenachrichtigungen für Kategorien erhalten können, an denen sie interessiert sind.

-   [Senden von Benachrichtigungen an Benutzer](/en-us/manage/services/notification-hubs/notify-users/)
    Lernen Sie, wie Sie Pushbenachrichtigungen von einem mobilen Dienst an bestimmte Benutzer auf beliebigen Geräten senden können.

-   [Senden plattformübergreifender Benachrichtigungen an Benutzer](/en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/)
    Lernen Sie, wie Vorlagen zum Senden von Pushbenachrichtigungen mit einem mobilen Dienst gesendet werden, ohne dass Sie in Ihrem Backend auf plattformspezifische Nutzlasten zurückgreifen müssen.

Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-js)
    Weitere Informationen zum Speichern und Abfragen von Daten mit mobilen Diensten.

-   [Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-js)
    Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
    Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.

-   [Mobile Services HTML/JavaScript-Anleitungen: Konzeptionelle Referenz](/en-us/develop/mobile/how-to-guides/work-with-html-js-client/)
    Lernen Sie mehr über die Verwendung von Mobile Services mit HTML und JavaScript.


<!-- Anchors. -->
[Register your app for push notifications and configure Mobile Services]: #register
[Create the Registrations table]: #create-table
[Update scripts to send push notifications]: #update-scripts
[Add push notifications to the app]: #add-push
[Insert data to receive notifications]: #test
[Next Steps]:#next-steps

<!-- Images. -->





[5]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-insert-script-push2.png







[13]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-quickstart-push2.png
[15]: ./media/mobile-services-windows-store-javascript-get-started-push-vs2012/mobile-app-enable-toast-win8.png



<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: /en-us/develop/mobile/tutorials/get-started/
[Get started with data]: /en-us/develop/mobile/tutorials/get-started-with-data-js
[Get started with authentication]: /en-us/develop/mobile/tutorials/get-started-with-users-js
[Get started with push notifications]: /en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012/
[Push notifications to app users]: /en-us/develop/mobile/tutorials/push-notifications-to-users-js
[Authorize users with scripts]: /en-us/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /en-us/develop/mobile/tutorials/get-started-with-push-js-vs2012

[Azure Management Portal]: https://manage.windowsazure.com/
[Get started with Notification Hubs]: /en-us/manage/services/notification-hubs/getting-started-windows-dotnet/
[What are Notification Hubs?]: /en-us/develop/net/how-to-guides/service-bus-notification-hubs/
[Send notifications to subscribers]: /en-us/manage/services/notification-hubs/breaking-news-dotnet/
[Send notifications to users]: /en-us/manage/services/notification-hubs/notify-users/
[Send cross-platform notifications to users]: /en-us/manage/services/notification-hubs/notify-users-xplat-mobile-services/
[Mobile Services HTML/JavaScript How-to Conceptual Reference]: /en-us/develop/mobile/how-to-guides/work-with-html-js-client/
[Mobile Services server script reference]: http://go.microsoft.com/fwlink/?LinkId=262293
