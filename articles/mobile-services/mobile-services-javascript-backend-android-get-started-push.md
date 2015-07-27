
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
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="06/01/2015" 
	ms.author="ricksal"/>


# Hinzufügen von Pushbenachrichtigungen zur Mobile Services-Android-App

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

## Zusammenfassung

In diesem Thema erfahren Sie, wie Sie mithilfe von Azure Mobile Services eine Pushbenachrichtigung per Google Cloud Messaging ("GCM") an Ihre Android-App senden. Sie fügen Pushbenachrichtigungen dem Schnellstart-Projekt hinzu, das eine Voraussetzung für dieses Lernprogramm ist. Pushbenachrichtigungen werden mithilfe des Azure Notification Hub aktiviert, der in Ihrem mobilen Dienst enthalten ist. Wenn dies abgeschlossen ist, sendet Ihr mobiler Dienst immer dann, wenn ein Datensatz eingefügt wird, eine Pushbenachrichtigung.

<!-- [AZURE.NOTE] If you would like to see the source code of the completed app, go <a href="https://github.com/RickSaling/mobile-services-samples/tree/androidStudio/GettingStartedWithPush/AndroidStudio" target="_blank">here</a>.
-->


## Voraussetzungen

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

##<a id="register"></a>Aktivieren von Google Cloud Messaging

[AZURE.INCLUDE [mobile-services-enable-Google-cloud-messaging](../../includes/mobile-services-enable-Google-cloud-messaging.md)]

##<a id="configure"></a>Konfigurieren von Mobile Services zum Senden von Pushanforderungen

[AZURE.INCLUDE [mobile-services-android-configure-push](../../includes/mobile-services-android-configure-push.md)]

##<a id="add-push"></a>Hinzufügen von Pushbenachrichtigungen zur App



Als Nächstes installieren Sie Google Play Services. Google Cloud Messaging verfügt über einige Mindestanforderungen an den API-Level für Entwicklung und Tests, denen die Eigenschaft **minSdkVersion** im Manifest entsprechen muss.

Wenn Sie den Testvorgang mit einem älteren Gerät durchführen, konsultieren Sie [Set Up Google Play Services SDK] (Einrichten des Google Play Services SDK, in englischer Sprache), um zu ermitteln, wie niedrig Sie diesen Wert einstellen können. Legen Sie den Wert anschließend entsprechend fest.

###Hinzufügen von Google Play Services zum Projekt

[AZURE.INCLUDE [Hinzufügen von Play Services](../../includes/mobile-services-add-Google-play-services.md)]

###Hinzufügen des Codes

[AZURE.INCLUDE [mobile-services-android-getting-started-with-push](../../includes/mobile-services-android-getting-started-with-push.md)]


##<a id="update-scripts"></a>Aktualisieren des registrierten Einfügeskripts im Verwaltungsportal

[AZURE.INCLUDE [mobile-services-javascript-backend-android-push-insert-script](../../includes/mobile-services-javascript-backend-android-push-insert-script.md)]


##<a id="test"></a>Testen von Pushbenachrichtigungen in der App

Sie können die App testen, indem Sie ein Android-Telefon direkt mit einem USB-Kabel verbinden oder ein virtuelles Gerät im Emulator verwenden.

###Einrichten des Android-Emulators für den Testvorgang

Wenn Sie diese App im Emulator ausführen, stellen Sie sicher, dass Sie ein Android Virtual Device (AVD) verwenden, das Google-APIs unterstützt.

1. Wählen Sie am rechten Ende der Symbolleiste den Android Virtual Device Manager, wählen Sie Ihr Gerät aus, und klicken Sie auf das Symbol "Bearbeiten" auf der rechten Seite.

	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager.png)

2. Wählen Sie auf der Gerätebeschreibungszeile **Ändern**, wählen Sie **Google-APIs**, und klicken Sie auf "OK".

   	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-services-android-virtual-device-manager-edit.png)

	Dies gibt das AVD zur Verwendung von Google-APIs vor.

###Ausführen des Tests

1. Klicken Sie im Menü **Ausführen** auf **App ausführen**, um das Projekt zu starten.

2. Geben Sie in der App einen sinnvollen Text ein, beispielsweise _Eine neue Mobile Services-Aufgabe hinzufügen_, und klicken Sie dann auf die Schaltfläche **Hinzufügen**.

  	![](./media/mobile-services-javascript-backend-android-get-started-push/mobile-quickstart-push1-android.png)

3. Führen Sie eine Streifbewegung vom oberen Bildschirmrand aus, um die Benachrichtigungsleiste des Geräts zu öffnen, sodass die Benachrichtigung angezeigt wird.


Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

## Problembehandlung

### Prüfen der Version des Android-SDK

[AZURE.INCLUDE [Prüfen des SDK](../../includes/mobile-services-verify-android-sdk-version.md)]


## Ältere Codeversionen

Die Eclipse-Version dieses Lernprogramms finden Sie unter [Erste Schritte mit Pushbenachrichtigungen (Eclipse)].


<!--
To see a completed version of the source code in an Eclipse project, go <a href="https://github.com/Azure/mobile-services-samples/tree/master/GettingStartedWithData/Android">here</a>.
-->


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

* [Erste Schritte mit Daten] <br/>Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

* [Erste Schritte mit der Authentifizierung] <br/>Informationen zur Authentifizierung von Benutzern Ihrer App mit verschiedenen Kontotypen mithilfe von mobilen Diensten.

* [Was sind Notification Hubs?] <br/>Erfahren Sie, wie Sie mit Benachrichtigungshubs Benachrichtigungen an all Ihre Apps auf allen großen Clientplattformen versenden können.

* [Debuggen von Notification Hubs-Anwendungen](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Hier finden Sie die Anleitungen zur Problembehandlung und zum Debuggen von Notification Hubs-Lösungen.

* [Verwenden der Android-Clientbibliothek für Mobile Services] <br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit Android.

* [Mobile Services: Serverskriptreferenz] <br/>Erfahren Sie mehr über das Implementieren von Geschäftslogik in Ihren mobilen Dienst.


<!-- Anchors. -->
[Register your app for push notifications and configure Mobile Services]: #register
[Update the generated push notification code]: #update-scripts
[Insert data to receive notifications]: #test
[Next Steps]: #next-steps

<!-- Images. -->
[13]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push1.png
[14]: ./media/mobile-services-windows-store-javascript-get-started-push/mobile-quickstart-push2.png


<!-- URLs. -->
[Erste Schritte mit Pushbenachrichtigungen (Eclipse)]: mobile-services-javascript-backend-android-get-started-push-EC.md
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Get started with Mobile Services]: mobile-services-android-get-started.md
[Erste Schritte mit Daten]: mobile-services-android-get-started-data.md
[Erste Schritte mit der Authentifizierung]: mobile-services-android-get-started-users.md
[Get started with push notifications]: /develop/mobile/tutorials/get-started-with-push-js
[Push notifications to app users]: /develop/mobile/tutorials/push-notifications-to-users-js
[Authorize users with scripts]: /develop/mobile/tutorials/authorize-users-in-scripts-js
[JavaScript and HTML]: /develop/mobile/tutorials/get-started-with-push-js
[Set Up Google Play Services SDK]: http://go.microsoft.com/fwlink/?LinkId=389801
[Azure Management Portal]: https://manage.windowsazure.com/
[Verwenden der Android-Clientbibliothek für Mobile Services]: mobile-services-android-how-to-use-client-library.md

[gcm object]: http://go.microsoft.com/fwlink/p/?LinkId=282645

[Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293

[Send push notifications to authenticated users]: mobile-services-javascript-backend-android-push-notifications-app-users.md

[Was sind Notification Hubs?]: ../notification-hubs-overview.md
[Send broadcast notifications to subscribers]: ../notification-hubs-android-send-breaking-news.md
[Send template-based notifications to subscribers]: ../notification-hubs-android-send-localized-breaking-news.md
 

<!---HONumber=July15_HO3-->