<properties pageTitle="Erste Schritte mit Pushbenachrichtigungen (Legacy-Push) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services to send push notifications to your Windows Phone app (legacy push)." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services (legacy push)" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="glenga" />


# Erste Schritte mit Pushbenachrichtigungen in Mobile Services (Legacypushvorgang)

<div class="dev-center-tutorial-selector sublanding">
    <a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-push" title="Windows Store C#" >Windows Store C#</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-push" title="Windows Store JavaScript">Windows Store JavaScript</a>
    <a href="/de-de/documentation/articles/mobile-services-windows-phone-get-started-push" title="Windows Phone" class="current">Windows Phone</a>
    <a href="/de-de/documentation/articles/mobile-services-ios-get-started-push" title="iOS">iOS</a>
    <a href="/de-de/documentation/articles/mobile-services-android-get-started-push" title="Android">Android</a>
<!--    <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-push" title="Xamarin.iOS">Xamarin.iOS</a>
    <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-android-get-started-push" title="Xamarin.Android">Xamarin.Android</a>    -->
	<a href="/de-de/documentation/articles/partner-appcelerator-mobile-services-javascript-backend-appcelerator-get-started-push" title="Appcelerator">Appcelerator</a>
</div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push/" title=".NET backend">.NET backend</a> | <a href="/de-de/documentation/articles/mobile-services-windows-phone-get-started-push/"  title="JavaScript backend" class="current">JavaScript-Back-End</a>
</div>

In diesem Thema wird gezeigt, wie Sie mit Azure Mobile Services Pushbenachrichtigungen an eine Windows Phone 8-App senden können. 
In diesem Lernprogramm fügen Sie dem Schnellstart-Projekt mit dem Microsoft-Pushbenachrichtigungsdienst (MPNS) Pushbenachrichtigungen hinzu. Nach dem Abschluss sendet der mobile Dienst bei jedem Einfügen eines Datensatzes eine Pushbenachrichtigung.

>[WACOM.NOTE]In diesen Themen werden <em>vorhandene</em> mobile Dienste unterstützt, die <em>noch nicht</em> zum Verwenden der Notification-Hub-Integration aktualisiert wurden. Wenn Sie einen <em>neuen</em> mobilen Dienst erstellen, wird diese integrierte Funktionalität automatisch aktiviert. Weitere Informationen zu neuen mobilen Diensten finden Sie unter [Erste Schritte mit Pushbenachrichtigungen](/de-de/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/).
>
>Mobile Dienste werden in Azure Notification-Hubs für die Unterstützung zusätzlicher Pushbenachrichtigungsfunktionen, wie z. B. Vorlagen, mehreren Plattformen und einer verbesserten Skalierung. <em>Sie sollten Ihre vorhandenen mobilen Dienste möglichst für die Verwendung von Notification Hubs aktualisieren</em>. Nach dem Upgrade finden Sie weitere Informationen unter [Erste Schritte mit Pushbenachrichtigungen](/de-de/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push/).

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Erstellen der Registrations-Tabelle]
2. [Hinzufügen von Pushbenachrichtigungen zur App]
3. [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]
4. [Einfügen von Daten zum Empfangen von Benachrichtigungen]

Für dieses Lernprogramm benötigen Sie [Visual Studio 2012 Express für Windows Phone] oder eine spätere Version.

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Mobile Services] abschließen. 

   >[WACOM.NOTE]Wenn Sie täglich mehr als 500 Nachrichten pro Benutzer senden, müssen Sie stattdessen Notification Hubs verwenden. Weitere Informationen finden Sie unter <a href="/de-de/manage/services/notification-hubs/getting-started-windows-dotnet/">Erste Schritte mit Notification Hubs</a>.

## <a name="create-table"></a>Erstellen einer neuen Tabelle

[WACOM.INCLUDE [mobile-services-create-new-push-table](../includes/mobile-services-create-new-push-table.md)]

<h2><a name="add-push"></a>Hinzufügen von Pushbenachrichtigungen zur App</h2>
		
1. Öffnen Sie in Visual Studio die Projektdatei "MainPage.xaml.cs", und fügen Sie den folgenden Code hinzu, der eine neue **Registrations**-Klasse erstellt:

	    public class Registrations
	    {
	        public string Id { get; set; }
	
	        [JsonProperty(PropertyName = "handle")]
	        public string Handle { get; set; }
	    }
	
	Mit der "Handle"-Eigenschaft wird der Kanal-URI gespeichert.

2. Öffnen Sie die Datei App.xaml.cs, und fügen Sie die folgende using-Anweisung hinzu:

        using Microsoft.Phone.Notification;

3. Fügen Sie der Datei "App.xaml.cs" folgenden Code hinzu:
	
        public static HttpNotificationChannel CurrentChannel { get; private set; }

		private async void AcquirePushChannel()
        {
            CurrentChannel = HttpNotificationChannel.Find("MyPushChannel");

            if (CurrentChannel == null)
            {
                CurrentChannel = new HttpNotificationChannel("MyPushChannel");
                CurrentChannel.Open();
                CurrentChannel.BindToShellTile();
            }
                  
	       IMobileServiceTable<Registrations> registrationsTable = App.MobileService.GetTable<Registrations>();
	       var registration = new Registrations { Handle = CurrentChannel.ChannelUri.AbsoluteUri };
	       await registrationsTable.InsertAsync(registration);
        }

   	Dieser Code reserviert und speichert einen Kanal für ein Pushbenachrichtigungsabonnement und bindet ihn an die Standardkachel der App.

	<div class="dev-callout"><b>Hinweis</b>
		<p>In diesem Lernprogramm sendet der mobile Dienst eine Benachrichtigung zum Umdrehen der Kachel an das Gerät. Wenn Sie eine Popupbenachrichtigung senden, müssen Sie stattdessen die <strong>BindToShellToast</strong>-Methode für den Kanal aufrufen. Um Popup- und Kachelbenachrichtigungen zu unterstützen, rufen Sie beide Methoden, <strong>BindToShellTile</strong> und <strong>BindToShellToast</strong> </p>, auf.
	</div>
    
4. Fügen Sie in der Datei "App.xaml.cs" am Anfang des **Application_Launching**Ereignishandlers den folgenden Aufruf der neuen **AcquirePushChannel**-Methode hinzu:

        AcquirePushChannel();

   	Damit wird sichergestellt, dass die **CurrentChannel**-Eigenschaft bei jedem Aufruf der Anwendung initialsiert wird.


5.	Erweitern Sie im Projektmappen-Explorer **Eigenschaften**, öffnen Sie die Datei "WMAppManifest.xml", klicken Sie auf die Registerkarte **Funktionen**, und stellen Sie sicher, dass die Funktion **ID___CAP___PUSH_NOTIFICATION** aktiviert ist.

   	![][1]

   	Damit stellen Sie sicher, dass Ihre App Pushbenachrichtigungen empfangen kann.

<h2><a name="update-scripts"></a>Aktualisieren der registrierten Einfügeskripts im Verwaltungsportal</h2>

[WACOM.INCLUDE [mobile-services-update-registrations-script](../includes/mobile-services-update-registrations-script.md)]

4. Klicken Sie auf **TodoItem**, klicken Sie auf **Script**, und wählen Sie **Insert**. 

   	![][10]

3. Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

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
                    	    push.mpns.sendFlipTile(registration.handle, {
                        	    title: item.text
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

<h2><a name="test"></a>Testen von Pushbenachrichtigungen in der App</h2>

1. Wählen Sie in Visual Studio in Menü **Build** den Befehl **Projektmappe bereitstellen** aus.

2. Streifen Sie im Emulator nach links, um die Liste der installierten Apps anzuzeigen, und suchen Sie die neue App **TodoList**.

3. Tippen Sie auf das App-Symbol, und halten Sie den Finger darauf, und wählen Sie dann aus dem Kontextmenü **pin to start**.

  	![][2]

  	Damit wird die Kachel **TodoList** an das Startmenü angeheftet.

4. Tippen Sie auf die Kachel **TodoList**, um die App zu starten. 

  	![][3]

5. Geben Sie in der App in das Textfeld den Text "hello push" ein, und klicken Sie dann auf **Speichern**. 

   	![][4]

  	Damit wird eine Einfügeanforderung zum Speichern des hinzugefügten Eintrags an den mobilen Dienst gesendet.

6. Drücken Sie auf die **Start**-Schaltfläche, um zum Startmenü zurückzukehren. 

  	![][5]

  	Die Anwendung hat die Pushbenachrichtigung empfangen, und der Titel der Kachel lautet nun **hello push**.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der von Mobile Services bereitgestellten Pushbenachrichtigungsfunktion gezeigt. Wenn Sie Ihrer App fortgeschrittenere Funktionen, wie z. B. Senden von plattformübergreifenden Benachrichtigungen, abonnementbasiertes Routing oder sehr große Mengen von Benachrichtigungen, hinzufügen möchten, sollten Sie Azure Notification Hubs mit Ihrem mobilen Dienst verwenden. Weitere Informationen finden Sie in einem der folgenden Themen zu Notification Hubs:

+ [Erste Schritte mit Notification Hubs]
  <br/>Erfahren Sie, wie Sie Benachrichtigungshubs in Ihrer Windows Store-App einsetzen können.

+ [Was sind Notification Hubs?]
	<br/>Informationen zum Erstellen und Senden von Benachrichtigungen an Benutzer auf mehreren Plattformen.

+ [Senden von Benachrichtigungen an Abonnenten]
	<br/>Erfahren Sie, wie Benutzer sich registrieren und Pushbenachrichtigungen für Kategorien empfangen können, die sie interessieren.

<!--+ [Send notifications to users]
	<br/>Learn how to send push notifications from a Mobile Service to specific users on any device.

+ [Send cross-platform notifications to users]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

Weitere Informationen zu Mobile Services:

* [Erste Schritte mit Daten]
  <br/>Informationen über das Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie, wie Sie Benutzer der App mit Windows-Konto authentifizieren.

* [Mobile Services: Serverskriptreferenz]
  <br/>Erfahren Sie mehr über das Registrieren und Verwenden von Serverskripts.

* [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
  <br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET. 

<!-- Anchors. -->
[Erstellen der Registrations-Tabelle]: #create-table
[Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen]: #update-scripts
[Hinzufügen von Pushbenachrichtigungen zur App]: #add-push
[Einfügen von Daten zum Empfangen von Benachrichtigungen]: #test
[Nächste Schritte]:#next-steps

<!-- Images. -->
[1]: ./media/mobile-services-windows-phone-get-started-push/mobile-app-enable-push-wp8.png
[2]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push1-wp8.png
[3]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push2-wp8.png
[4]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push3-wp8.png
[5]: ./media/mobile-services-windows-phone-get-started-push/mobile-quickstart-push4-wp8.png
[10]: ./media/mobile-services-windows-phone-get-started-push/mobile-insert-script-push2.png



<!-- URLs. -->
[Mobile Services SDK]: https://go.microsoft.com/fwLink/p/?LinkID=268375
[Visual Studio 2012 Express für Windows Phone]: https://go.microsoft.com/fwLink/p/?LinkID=268374
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-wp8
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-wp8
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-wp8
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-wp8
[Senden von Pushbenachrichtigungen an App-Benutzer]: /de-de/develop/mobile/tutorials/push-notifications-to-users-wp8
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-wp8

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[mpns-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=271130
[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library/
[Erste Schritte mit Notification Hubs]: /de-de/manage/services/notification-hubs/get-started-notification-hubs-wp8/
[Was sind Notification Hubs?]: /de-de/develop/net/how-to-guides/service-bus-notification-hubs/
[Senden von Benachrichtigungen an Abonnenten]: /de-de/manage/services/notification-hubs/breaking-news-wp8/
[Senden von Benachrichtigungen an Benutzer]: /de-de/manage/services/notification-hubs/notify-users/
[Senden plattformübergreifender Benachrichtigungen an Benutzer]: /de-de/manage/services/notification-hubs/notify-users-xplat-mobile-services/
