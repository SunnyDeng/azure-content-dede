<properties urlDisplayName="Get Started with Push (JS)" pageTitle="Erste Schritte mit Pushbenachrichtigungen (Android JavaScript) | Mobile Dev Center" metaKeywords="" description="Erfahren Sie mehr über die Verwendung von Azure Mobile Services zum Senden von Pushbenachrichtigungen an Ihre Android-JavaScript-App." metaCanonical="http://www.windowsazure.com/de-de/develop/mobile/tutorials/get-started-with-push-dotnet/" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services " authors="ricksal"  solutions="" writer="ricksal" manager="dwrede" editor=""   />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="10/16/2014" ms.author="ricksal" />

# Hinzufügen von Pushbenachrichtigungen zur Mobile Services-App

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services eine Pushbenachrichtigung per Google Cloud Messaging (GCM) an Ihre Android-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Azure Notification Hubs zum Schnellstartprojekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Aktivieren von Google Cloud Messaging](#register)
2. [Konfigurieren von Mobile Services](#configure)
3. [Hinzufügen von Pushbenachrichtigungen zur App](#add-push)
4. [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen](#update-scripts)
5. [Einfügen von Daten zum Empfangen von Benachrichtigungen](#test)


>[AZURE.NOTE] Wenn Sie den Quellcode der fertigen App anzeigen möchten, wechseln Sie <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithPush/Android" target="_blank">hierher</a>.

##Voraussetzungen

[WACOM.INCLUDE [mobile-services-android-prerequisites](../includes/mobile-services-android-prerequisites.md)]

##<a id="register"></a>Aktivieren von Google Cloud Messaging

>[WACOM.NOTE]Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um diesen Vorgang abzuschließen. Rufen Sie zum Erstellen eines neuen Google-Kontos die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>auf.

[WACOM.INCLUDE [Aktivieren von GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Als Nächstes verwenden Sie diesen API-Schlüsselwert, um Mobile Services für die Authentifizierung mit GCM zu aktivieren und Pushbenachrichtigungen im Auftrag Ihrer App zu senden.

##<a id="configure"></a>Konfigurieren von Mobile Services zum Senden von Pushanforderungen

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services** und dann auf Ihre App.

   	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2. Klicken Sie auf die Registerkarte **Push**, geben Sie den Wert für **API-Schlüssel** ein, den Sie zuvor von GCM erhalten haben, und klicken Sie auf **Speichern**.

	>[WACOM.NOTE]Wenn Sie dieses Lernprogamm mit einem älteren mobilen Dienst ausführen, wird unten auf der Registerkarte **Push** unter Umständen ein Link mit dem Text **Erweiterten Push aktivieren** angezeigt. Klicken Sie jetzt darauf, um Ihren mobilen Dienst für die Integration mit Notification Hubs zu aktualisieren. Diese Änderung kann nicht mehr rückgängig gemacht werden. Einzelheiten zum Aktivieren der erweiterten Pushbenachrichtigungen in einem mobilen Dienst einer Produktionsumgebung finden Sie <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">in dieser Anleitung</a>.

   	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

    <div class="dev-callout"><b>Wichtig</b>
	<p>Wenn Sie Ihre GCM-Anmeldeinformationen für erweiterte Pushbenachrichtigungen auf der Registerkarte "Push" im Portal festlegen, werden diese für Notification Hubs freigegeben, sodass der Benachrichtigungshub mit Ihrer App konfiguriert wird.</p>
    </div>


Sowohl Ihr Mobile Service als auch Ihre App sind nun konfiguriert und funktionieren mit GCM und Notification Hubs.

##<a id="add-push"></a>Hinzufügen von Pushbenachrichtigungen zur App

###Prüfen der Version des Android-SDK

[WACOM.INCLUDE [Überprüfen des SDKs](../includes/mobile-services-verify-android-sdk-version.md)]

Als Nächstes installieren Sie Google Play Services. Google Cloud Messaging verfügt über einige Mindestanforderungen an den API-Level für Entwicklung und Tests, denen die Eigenschaft **minSdkVersion** im Manifest entsprechen muss. 

Wenn Sie den Testvorgang mit einem älteren Gerät durchführen, konsultieren Sie [Einrichten des Google Play Services SDK], um zu ermitteln, wie niedrig Sie diesen Wert einstellen können. Legen Sie den Wert anschließend entsprechend fest.

###Hinzufügen von Google Play Services zum Projekt

[WACOM.INCLUDE [Hinzufügen von Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Hinzufügen des Codes

[WACOM.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]


##<a id="update-scripts"></a>Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal

1. Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**. 

   	![](./media/mobile-services-android-get-started-push/mobile-portal-data-tables.png)

2. Klicken Sie in **todoitem** auf die Registerkarte **Skript** und dann auf **Einfügen**.
   
  	![](./media/mobile-services-android-get-started-push/mobile-insert-script-push2.png)

   	Auf diese Weise wird die Funktion angezeigt, die aufgerufen wird, wenn in der Tabelle **TodoItem** eine Einfügung auftritt.

3. Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

		function insert(item, user, request) {
		// Define a payload for the Google Cloud Messaging toast notification.
		var payload = {
		    data: {
		        message: item.text 
		    }
		};		
		request.execute({
		    success: function() {
		        // If the insert succeeds, send a notification.
		        push.gcm.send(null, payload, {
		            success: function(pushResponse) {
		                console.log("Sent push:", pushResponse, payload);
		                request.respond();
		                },              
		            error: function (pushResponse) {
		                console.log("Error Sending push:", pushResponse);
		                request.respond(500, { error: pushResponse });
		                }
		            });
		        },
		    error: function(err) {
		        console.log("request.execute error", err)
		        request.respond();
		    }
		  });
		}

   	Daraufhin wird ein neues Einfügeskript registriert, das das [gcm-Objekt] zum Senden einer Pushbenachrichtigung an alle registrierten Geräte verwendet, nachdem der Einfügevorgang erfolgreich war. 

##<a id="test"></a>Testen von Pushbenachrichtigungen in der App

Sie können die App testen, indem Sie ein Android-Telefon direkt mit einem USB-Kabel verbinden oder ein virtuelles Gerät im Emulator verwenden.

###Einrichten des Emulators für den Testvorgang

Wenn Sie diese App im Emulator ausführen, stellen Sie sicher, dass Sie ein Android Virtual Device (AVD) verwenden, das Google-APIs unterstützt.

1. Starten Sie Eclipse neu, klicken Sie dann im Paket-Explorer mit der rechten Maustaste auf **Eigenschaften**, klicken Sie anschließend auf **Android**, aktivieren Sie die Option **Google APIs** und klicken Sie abschließend auf **OK**.

	![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

  	Dies gibt das Projekt für die Google-APIs vor.

2. Wählen Sie unter **Fenster** die Option **Android Virtual Device Manager**, wählen Sie Ihr Gerät aus, und klicken Sie auf **Bearbeiten**.

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

3. Wählen Sie **Google APIs** in **Ziel** aus und klicken Sie dann auf "OK".

   	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	Dies gibt das AVD zur Verwendung von Google-APIs vor.

###Ausführen des Tests

1. Klicken Sie in Eclipse im Menü **Ausführen** auf **Ausführen**, um die App zu starten.

2. Geben Sie in der App einen sinnvollen Text ein, beispielsweise "Eine neue Mobile Services-Aufgabe hinzufügen", und klicken Sie dann auf die Schaltfläche **Hinzufügen**.

  	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

3. Führen Sie eine Streifbewegung vom oberen Bildschirmrand aus, um das Notification Center des Geräts zu öffnen, sodass die Benachrichtigung angezeigt wird.


Sie haben dieses Lernprogramm erfolgreich abgeschlossen.


## <a name="next-steps"> </a>Nächste Schritte

<!---This tutorial demonstrated the basics of enabling an Android app to use Mobile Services and Notification Hubs to send push notifications. Next, consider completing the next tutorial, [Send push notifications to authenticated users], which shows how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send push notifications to authenticated users]
	<br/>Learn how to use tags to send push notifications from a Mobile Service to only an authenticated user.

+ [Send broadcast notifications to subscribers]
	<br/>Learn how users can register and receive push notifications for categories they're interested in.

+ [Send template-based notifications to subscribers]
	<br/>Learn how to use templates to send push notifications from a Mobile Service, without having to craft platform-specific payloads in your back-end.
-->

Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

* [Erste Schritte mit Daten]
  <br/>Informationen über das Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Authentifizierung]
  <br/>Informationen zur Authentifizierung von Benutzern Ihrer App mit verschiedenen Kontotypen mithilfe von Mobile Services.

* [Was sind Notification Hubs?]
  <br/>Erfahren Sie, wie Sie mit Benachrichtigungshubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

* [Debuggen von Notification Hubs-Anwendungen](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Erhalten Sie Anweisungen zur Problembehandlung und zum Debuggen von Notification Hubs-Lösungen. 

* [Verwenden der Android-Clientbibliothek für Mobile Services]
  <br/>Lernen Sie mehr über die Verwendung von Mobile Services mit Android.

* [Mobile Services server script reference]
  <br/>Erfahren Sie mehr über das Implementieren von Geschäftslogik in Ihren mobilen Dienst.


<!-- Anchors. -->
[Registrieren Ihrer App für Pushbenachrichtigungen und Konfigurieren von Mobile Services]: #register
[Aktualisieren des erzeugten Pushbenachrichtigungscodes]: #update-scripts
[Einfügen von Daten zum Empfangen von Benachrichtigungen]: #test
[Nächste Schritte]:#next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Anwendungsseite senden]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Eigene Apps]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-android-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-android-get-started-data/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-android-get-started-users
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-js
[Pushbenachrichtigungen für App-Benutzer]: /de-de/develop/mobile/tutorials/push-notifications-to-users-js
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript und HTML]: /de-de/develop/mobile/tutorials/get-started-with-push-js
[Einrichten des Google Play Services SDKs]: http://go.microsoft.com/fwlink/?LinkId=389801
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verwenden der Android-Clientbibliothek für Mobile Services]: /de-de/documentation/articles/mobile-services-android-how-to-use-client-library

[gcm-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293

[Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/

[Was sind Notification Hubs?]: /de-de/documentation/articles/notification-hubs-overview/
[Senden von Übertragungsbenachrichtigungen an Abonnenten]: /de-de/documentation/articles/notification-hubs-android-send-breaking-news/
[Senden vorlagenbasierter Benachrichtigungen an Abonnenten]: /de-de/documentation/articles/notification-hubs-android-send-localized-breaking-news/

<!--HONumber=35.1-->
