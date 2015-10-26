<properties
	pageTitle="Erste Schritte mit der Authentifizierung (Android) | Microsoft Azure"
	description="Erfahren Sie, wie Sie Mobile Services verwenden, um die Benutzer Ihrer Windows Store-App über verschiedene Identitätsanbieter, einschließlich Google, Facebook, Twitter und Microsoft, zu authentifizieren."
	services="mobile-services"
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="10/05/2015" 
	ms.author="ricksal"/>

# Hinzufügen von Authentifizierung zu Ihrer Mobile Services-Android-App

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

## Zusammenfassung

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren.. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

In diesem Lernprogramm werden die grundlegenden Schritte zur Aktivierung der Authentifizierung in einer App erläutert.


## Voraussetzungen

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

##<a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

3. Öffnen Sie das Projekt, das Sie im Lernprogramm [Erste Schritte mit Mobile Services erstellt haben].

4. Klicken Sie im Menü **Ausführen** auf **App ausführen**, um die App zu starten. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) geworfen wird.

	 Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift und die _TodoItem_-Tabelle nun eine Authentifizierung verlangt.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

[AZURE.INCLUDE [mobile-services-android-authenticate-app](../../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Zwischenspeichern von Authentifizierungstoken auf dem Client

[AZURE.INCLUDE [mobile-services-android-authenticate-app-with-token](../../includes/mobile-services-android-authenticate-app-with-token.md)]

## <a name="refresh-tokens"></a>Aktualisieren des Tokencaches

[AZURE.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../../includes/mobile-services-android-authenticate-app-refresh-token.md)]

##<a name="next-steps"></a>Nächste Schritte

Im nächsten Lernprogramm [Dienstweite Autorisierung von Mobile Services-Benutzern][Authorize users with scripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern.


<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Store authentication tokens on the client]: #cache-tokens
[Refresh expired tokens]: #refresh-tokens
[Next Steps]: #next-steps

<!-- URLs. -->
[Erste Schritte mit Mobile Services erstellt haben]: mobile-services-dotnet-backend-android-get-started.md
[Get started with authentication]: mobile-services-dotnet-backend-android-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-android-get-started-push.md
[Authorize users with scripts]: ../mobile-services-dotnet-backend-android-authorize-users-in-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!---HONumber=Oct15_HO3-->