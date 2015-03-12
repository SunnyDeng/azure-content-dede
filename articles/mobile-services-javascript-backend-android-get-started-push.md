<properties 
	pageTitle="Erste Schritte mit Pushbenachrichtigungen (Android JavaScript) | Mobile Dev Center" 
	description="Erfahren Sie mehr über die Verwendung von Azure Mobile Services zum Senden von Pushbenachrichtigungen an Ihre Android-JavaScript-App." 
	services="mobile-services, notification-hubs" 
	documentationCenter="android" 
	authors="RickSaling" 
	writer="ricksal" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="Mobile-Android" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="10/16/2014" 
	ms.author="ricksal"/>

# Hinzufügen von Pushbenachrichtigungen zur Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services eine Pushbenachrichtigung per Google Cloud Messaging (GCM) an Ihre Android-App senden. In diesem Lernprogramm fügen Sie Pushbenachrichtigungen mithilfe von Azure Notification Hubs zum Schnellstartprojekt hinzu. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte zur Aktivierung von Pushbenachrichtigungen behandelt:

1. [Aktivieren von Google Cloud Messaging](#register)
2. [Konfigurieren von Mobile Services](#configure)
3. [Hinzufügen von Pushbenachrichtigungen zur App](#add-push)
4. [Aktualisieren von Skripts zum Senden von Pushbenachrichtigungen](#update-scripts)
5. [Einfügen von Daten zum Empfangen von Benachrichtigungen](#test)


>[AZURE.NOTE] Den Quellcode der fertig gestellten App finden Sie <a href="https://github.com/RickSaling/mobile-services-samples/tree/futures/GettingStartedWithPush/Android" target="_blank">hier</a>.

##Voraussetzungen

[AZURE.INCLUDE [mobile-services-android-prerequisites](../includes/mobile-services-android-prerequisites.md)]

##<a id="register"></a>Aktivieren von Google Cloud Messaging

>[AZURE.NOTE]Sie benötigen ein Google-Konto mit verifizierter E-Mail-Adresse, um diesen Vorgang abzuschließen. Besuchen Sie die Seite <a href="http://go.microsoft.com/fwlink/p/?LinkId=268302" target="_blank">accounts.google.com</a>, um ein neues Google-Konto zu erstellen.

[AZURE.INCLUDE [Enable GCM](../includes/mobile-services-enable-Google-cloud-messaging.md)]

Als Nächstes verwenden Sie diesen API-Schlüsselwert, um Mobile Services für die Authentifizierung mit GCM zu aktivieren und Pushbenachrichtigungen im Auftrag Ihrer App zu senden.

##<a id="configure"></a>Konfigurieren von Mobile Services zum Senden von Pushanforderungen

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**, und klicken Sie dann auf Ihre App.

   	![](./media/mobile-services-android-get-started-push/mobile-services-selection.png)

2. Klicken Sie auf die Registerkarte **Push**, geben Sie den Wert für **API-Schlüssel** ein, den Sie zuvor von GCM erhalten haben, und klicken Sie auf **Speichern**.

	>[AZURE.NOTE]Wenn Sie dieses Lernprogramm mit einem älteren mobilen Dienst ausführen, wird unten auf der Registerkarte **Push** unter Umständen ein Link mit dem Text **Erweiterten Push aktivieren** angezeigt. Klicken Sie jetzt darauf, um Ihren mobilen Dienst für die Integration mit Notification Hubs zu aktualisieren. Diese Änderung kann nicht mehr rückgängig gemacht werden. Einzelheiten zum Aktivieren der erweiterten Pushbenachrichtigungen in einem mobilen Dienst einer Produktionsumgebung finden Sie in <a href="http://go.microsoft.com/fwlink/p/?LinkId=391951">dieser Anleitung</a>.

   	![](./media/mobile-services-android-get-started-push/mobile-push-tab-android.png)

	> [AZURE.WICHTIG] Wenn Sie Ihre GCM-Anmeldeinformationen für erweiterte Pushbenachrichtigungen auf der Registerkarte "Push" im Portal festlegen, werden diese für Notification Hubs freigegeben, sodass der Benachrichtigungshub mit Ihrer App konfiguriert wird.


Sowohl Ihr Mobile Service als auch Ihre App sind nun konfiguriert und funktionieren mit GCM und Notification Hubs.

##<a id="add-push"></a>Hinzufügen von Pushbenachrichtigungen zur App

###Prüfen der Version des Android-SDK

[AZURE.INCLUDE [Verify SDK](../includes/mobile-services-verify-android-sdk-version.md)]

Als Nächstes installieren Sie Google Play Services. Google Cloud Messaging verfügt über einige Mindestanforderungen an den API-Level für Entwicklung und Tests, denen die Eigenschaft **minSdkVersion** im Manifest entsprechen muss. 

Wenn Sie auf einem älteren Gerät testen, konsultieren Sie [Einrichten des Google Play Services SDK], um herauszufinden, wie niedrig Sie diesen Wert einstellen können. Legen Sie den Wert anschließend entsprechend fest.

###Hinzufügen von Google Play Services zum Projekt

[AZURE.INCLUDE [Add Play Services](../includes/mobile-services-add-Google-play-services.md)]

###Hinzufügen des Codes

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../includes/mobile-services-android-getting-started-with-push.md)]


##<a id="update-scripts"></a>Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal

1. Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItems**. 

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

1. Starten Sie Eclipse neu, klicken Sie dann im Paket-Explorer mit der rechten Maustaste auf **Eigenschaften**, klicken Sie anschließend auf **Android**, aktivieren Sie die Option **Google APIs**, und klicken Sie abschließend auf **OK**.

	![](./media/mobile-services-android-get-started-push/mobile-services-import-android-properties.png)

  	Dies gibt das Projekt für die Google-APIs vor.

2. Wählen Sie unter **Fenster** die Option **Android Virtual Device Manager**, wählen Sie Ihr Gerät aus, und klicken Sie auf **Bearbeiten**.

	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager.png)

3. Wählen Sie **Google APIs** in **Ziel** aus, und klicken Sie auf "OK".

   	![](./media/mobile-services-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	Dies gibt das AVD zur Verwendung von Google-APIs vor.

###Ausführen des Tests

1. Klicken Sie in Eclipse im Menü **Ausführen** auf **Ausführen**, um die App zu starten.

2. Geben Sie in der App einen sinnvollen Text ein, beispielsweise _Eine neue Mobile Services-Aufgabe_, und klicken Sie dann auf die Schaltfläche **Hinzufügen**.

  	![](./media/mobile-services-android-get-started-push/mobile-quickstart-push1-android.png)

3. Führen Sie eine Streifbewegung vom oberen Bildschirmrand aus, um das Notification Center des Geräts zu öffnen, sodass die Benachrichtigung angezeigt wird.


Sie haben dieses Lernprogramm erfolgreich abgeschlossen.


## <a name="next-steps"> </a>Nächste Schritte

<!---In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Android-App für die Arbeit mit Mobile Services und Notification Hubs zum Senden von Pushbenachrichtigungen aufgezeigt. Als Nächstes können Sie das Lernprogramm [Senden von Pushbenachrichtigungen an authentifizierte Benutzer] ausführen, in dem die Verwendung von Tags zum Versand von Pushbenachrichtigungen von einem mobilen Dienst ausschließlich an einen authentifizierten Benutzer demonstriert wird.

+ [Senden von Pushbenachrichtigungen an authentifizierte Benutzer]
	<br/>Erfahren Sie, wie Pushbenachrichtigungen mithilfe von Tags von einem mobilen Dienst ausschließlich an einen authentifizierten Benutzer gesendet werden.

+ [Senden von Übertragungsbenachrichtigungen an Abonnenten]
	<br/>Erfahren Sie, wie sich Benutzer registrieren und Pushbenachrichtigungen für Kategorien erhalten können, an denen sie interessiert sind.

+ [Senden von vorlagenbasierten Benachrichtigungen an Abonnenten]
	<br/>Erfahren Sie, wie Vorlagen zum Senden von Pushbenachrichtigungen mit einem mobilen Dienst gesendet werden, ohne dass Sie in Ihrem Back-End auf plattformspezifische Nutzlasten zurückgreifen müssen.
-->

Weitere Informationen zu Mobile Services und Benachrichtigungshubs finden Sie in den folgenden Themen:

* [Erste Schritte mit Daten]
  <br/>Erfahren Sie mehr über das Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie mehr über die Authentifizierung von Benutzern Ihrer App mit verschiedenen Kontotypen über Mobile Services.

* [Was sind Notification Hubs?]
  <br/>Erfahren Sie, wie Sie mit Benachrichtigungshubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

* [Debuggen von Notification Hubs-Anwendungen](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Erhalten Sie Anweisungen zur Problembehandlung und zum Debuggen von Notification Hubs-Lösungen. 

* [Verwenden der Android-Clientbibliothek für Mobile Services]
  <br/>Lernen Sie mehr über die Verwendung von Mobile Services mit Android.

* [Mobile Services: Serverskriptreferenz]
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
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-android-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-android-get-started-data/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-android-get-started-users
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-js
[Senden von Pushbenachrichtigungen an App-Benutzer]: /de-de/develop/mobile/tutorials/push-notifications-to-users-js
[Autorisieren von Benutzern mit Skripts]: /de-de/develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript und HTML]: /de-de/develop/mobile/tutorials/get-started-with-push-js
[Einrichten des Google Play Services SDK]: http://go.microsoft.com/fwlink/?LinkId=389801
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verwenden der Android-Clientbibliothek für Mobile Services]: /de-de/documentation/articles/mobile-services-android-how-to-use-client-library

[gcm-Objekt]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293

[Senden von Pushbenachrichtigungen an authentifizierte Benutzer]: /de-de/documentation/articles/mobile-services-javascript-backend-android-push-notifications-app-users/

[Was sind Notification Hubs?]: /de-de/documentation/articles/notification-hubs-overview/
[Senden von Übertragungsbenachrichtigungen an Abonnenten]: /de-de/documentation/articles/notification-hubs-android-send-breaking-news/
[Senden von vorlagenbasierten Benachrichtigungen an Abonnenten]: /de-de/documentation/articles/notification-hubs-android-send-localized-breaking-news/


<!--HONumber=42-->
