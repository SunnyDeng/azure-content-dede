<properties
	pageTitle="Hinzufügen von Authentifizierung zu einer vorhandenen Mobile Services-App (iOS) | Mobile Dev Center"
	description="Erfahren Sie, wie Sie Mobile Services verwenden, um die Benutzer Ihrer iOS-App über verschiedene Identitätsanbieter, einschließlich Google, Facebook, Twitter und Microsoft, zu authentifizieren."
	services="mobile-services"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm=""
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="2/16/2015"
	ms.author="krisragh"/>

# Hinzufügen von Authentifizierung zu einer vorhandenen Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

In diesem Lernprogramm fügen Sie dem Schnellstartprojekt mithilfe eines unterstützten Identitätsanbieters eine Authentifizierung hinzu. Dieses Lernprogramm baut auf dem Schnellstartlernprogramm [Erste Schritte mit Mobile Services] auf, das Sie zuerst abschließen müssen.

##<a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

Öffnen Sie das Projekt in Xcode. Klicken Sie auf die Schaltfläche **Ausführen**, um die App zu starten. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) ausgelöst wird. Dies liegt daran, dass die App versucht als nicht authentifizierter Benutzer auf Mobile Services zuzugreifen, die _TodoItem_-Tabelle jetzt jedoch Authentifizierung erfordert.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Speichern von Authentifizierungstoken in der App

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Nächste Schritte

Im nächsten Lernprogramm, [Dienstseitige Autorisierung von Benutzern in Mobile Services], werden Sie den Benutzer-ID-Wert verwenden, um zurückgegebene Daten zu filtern.

<!-- Anchors. -->
[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
[Hinzufügen von Authentifizierung zur App]: #add-authentication
[Nächste Schritte]:#next-steps
[Speichern von Authentifizierungstoken in der App]:#store-authentication

<!-- URLs. -->
[Dienstseitige Autorisierung von Benutzern in Mobile Services]: mobile-services-dotnet-backend-service-side-authorization.md
[Erste Schritte mit Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Erste Schritte mit Daten]: mobile-services-dotnet-backend-ios-get-started-data.md
[Erste Schritte mit der Authentifizierung]: mobile-services-dotnet-backend-ios-get-started-users.md
[Erste Schritte mit Pushbenachrichtigungen]: mobile-services-dotnet-backend-ios-get-started-push.md
[Autorisieren von Benutzern mit Skripts]: mobile-services-dotnet-backend-ios-authorize-users-in-scripts.md

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /develop/mobile/how-to-guides/work-with-net-client-library
[Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung]: mobile-services-how-to-register-store-app-package-microsoft-authentication.md

<!--HONumber=49-->