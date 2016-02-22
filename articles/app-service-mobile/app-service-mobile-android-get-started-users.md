<properties
	pageTitle="Hinzufügen von Authentifizierung unter Android mit Mobile Apps| Azure App Service"
	description="Erfahren Sie, wie Sie Mobile Apps in Azure App Service verwenden, um die Benutzer Ihrer Android-App über verschiedene Identitätsanbieter, einschließlich Google, Facebook, Twitter und Microsoft, zu authentifizieren."
	services="app-service\mobile"
	documentationCenter="android"
	authors="ggailey777"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="glenga"/>

# Hinzufügen der Authentifizierung zu Ihrer Android-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## Zusammenfassung

In diesem Tutorial fügen Sie dem Aufgabenlisten-Schnellstartprojekt unter Android mithilfe eines unterstützten Identitätsanbieters eine Authentifizierung hinzu. Dieses Tutorial baut auf dem Tutorial [Erste Schritte mit Mobile Apps] auf, das Sie zuerst abschließen müssen.

##<a name="register"></a>Registrieren Ihrer App für die Authentifizierung und Konfigurieren von App Service

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ Öffnen Sie in Android Studio das Projekt, das Sie beim Ausführen des Lernprogramms [Erste Schritte mit Mobile Apps] erstellt haben, klicken Sie im Menü **Ausführen** auf **App ausführen**, und stellen Sie sicher, dass ein unbehandelter Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) nach dem Start der App ausgelöst wird.

	 Dies liegt daran, dass die App als nicht authentifizierter Benutzer versucht, auf das Back-End zuzugreifen, aber die _TodoItem_-Tabelle nun eine Authentifizierung verlangt.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile App-Back-End anfordern.

## Hinzufügen von Authentifizierung zur App

[AZURE.INCLUDE [mobile-android-authenticate-app](../../includes/mobile-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Zwischenspeichern von Authentifizierungstoken auf dem Client

[AZURE.INCLUDE [mobile-android-authenticate-app-with-token](../../includes/mobile-android-authenticate-app-with-token.md)]

##Nächste Schritte

Nachdem Sie dieses einfache Tutorial zur Authentifizierung abgeschlossen haben, können Sie mit den folgenden Tutorials fortfahren:

+ [Hinzufügen von Pushbenachrichtigungen zu Ihrer Android-App](app-service-mobile-android-get-started-push.md) Erfahren Sie, wie Sie Pushbenachrichtigungen zum Unterstützen Ihrer App hinzufügen und Ihr Mobile App-Back-End für die Verwendung von Azure Notification Hubs zum Senden von Pushbenachrichtigungen konfigurieren.

+ [Aktivieren der Offlinesynchronisierung für Ihre Android-App](app-service-mobile-android-get-started-offline-data.md) Erfahren Sie, wie Sie mithilfe eines Mobile App-Back-Ends Ihrer App Offlineunterstützung hinzufügen. Die Offlinesynchronisierung ermöglicht Endbenutzern die Interaktion mit einer mobilen App (Anzeigen, Hinzufügen und Ändern von Daten), auch wenn keine Netzwerkverbindung vorhanden ist.



<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]: #next-steps


<!-- URLs. -->
[Erste Schritte mit Mobile Apps]: app-service-mobile-android-get-started.md

<!---HONumber=AcomDC_0211_2016-->