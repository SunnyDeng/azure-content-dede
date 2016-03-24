<properties
	pageTitle="Hinzufügen von Authentifizierung zur Mobile Services-App (iOS) | .NET-Back-End | Microsoft Azure"
	description="Erfahren Sie, wie Sie Mobile Services verwenden, um die Benutzer Ihrer iOS-App über verschiedene Identitätsanbieter, einschließlich Google, Facebook, Twitter und Microsoft, zu authentifizieren."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="03/05/2016"
	ms.author="krisragh"/>

# Hinzufügen von Authentifizierung zu einer vorhandenen Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../../includes/mobile-services-selector-get-started-users.md)]

&nbsp;

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]
> Informationen für die entsprechende Mobile Apps-Version dieses Themas finden Sie unter [Hinzufügen von Authentifizierung zu Ihrer iOS-App](../app-service-mobile/app-service-mobile-ios-get-started-users.md).
 
In diesem Lernprogramm fügen Sie dem Schnellstartprojekt mithilfe eines unterstützten Identitätsanbieters eine Authentifizierung hinzu. Dieses Lernprogramm baut auf dem Schnellstartlernprogramm [Erste Schritte mit Mobile Services] auf, das Sie zuerst abschließen müssen.

##<a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

Öffnen Sie das Projekt in Xcode. Klicken Sie auf die Schaltfläche **Ausführen**, um die App zu starten. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) ausgelöst wird. Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift und die _TodoItem_-Tabelle nun eine Authentifizierung verlangt.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Speichern von Authentifizierungstoken in der App

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Nächste Schritte

Im nächsten Lernprogramm, [Dienstseitige Autorisierung von Benutzern in Mobile Services], werden Sie den Benutzer-ID-Wert verwenden, um zurückgegebene Daten zu filtern.

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps
[Storing authentication tokens in your app]: #store-authentication

<!-- URLs. -->
[Dienstseitige Autorisierung von Benutzern in Mobile Services]: mobile-services-dotnet-backend-service-side-authorization.md
[Erste Schritte mit Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Get started with authentication]: mobile-services-dotnet-backend-ios-get-started-users.md
[Get started with push notifications]: mobile-services-dotnet-backend-ios-get-started-push.md
[Authorize users with scripts]: mobile-services-dotnet-backend-service-side-authorization.md
[Mobile Services .NET How-to Conceptual Reference]: /develop/mobile/how-to-guides/work-with-net-client-library
[Register your Windows Store app package for Microsoft authentication]: ../mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!---HONumber=AcomDC_0309_2016-->