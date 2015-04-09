<properties
	pageTitle="Hinzufügen von Authentifizierung zur Mobile Services-App (iOS) | Mobile Dev Center"
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

In diesem Lernprogramm fügen Sie dem Schnellstartprojekt mithilfe eines unterstützten Identitätsanbieters eine Authentifizierung hinzu.

Dieses Lernprogramm baut auf dem Schnellstartlernprogramm [Erste Schritte mit Mobile Services] auf, das Sie zuerst abschließen müssen.

##<a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-restrict-permissions-javascript-backend](../includes/mobile-services-restrict-permissions-javascript-backend.md)]

Öffnen Sie das Projekt in Xcode. Klicken Sie auf die Schaltfläche **Ausführen**, um die App zu starten. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) ausgelöst wird. Dies liegt daran, dass die App versucht als nicht authentifizierter Benutzer auf Mobile Services zuzugreifen, die _TodoItem_-Tabelle jetzt jedoch Authentifizierung erfordert.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Speichern von Authentifizierungstoken in der App

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

## <a name="next-steps"></a>Nächste Schritte

Im nächsten Lernprogramm, [Dienstseitige Autorisierung von Benutzern in Mobile Services], werden Sie den Benutzer-ID-Wert verwenden, um zurückgegebene Daten zu filtern.

<!-- Anchors. -->
[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
[Hinzufügen von Authentifizierung zur App]: #add-authentication
[Nächste Schritte]:#next-steps
[Speichern von Authentifizierungstoken in der App]:#store-authentication

<!-- Images. -->




[4]: ./media/mobile-services-ios-get-started-users/mobile-services-selection.png
[5]: ./media/mobile-services-ios-get-started-users/mobile-service-uri.png







[13]: ./media/mobile-services-ios-get-started-users/mobile-identity-tab.png
[14]: ./media/mobile-services-ios-get-started-users/mobile-portal-data-tables.png
[15]: ./media/mobile-services-ios-get-started-users/mobile-portal-change-table-perms.png


<!-- URLs. -->
[Dienstseitige Autorisierung von Benutzern in Mobile Services]: mobile-services-javascript-backend-service-side-authorization.md
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Einmaliges Anmelden für Windows Store-Apps mithilfe von Live Connect]: /develop/mobile/tutorials/single-sign-on-windows-8-dotnet
[Erste Schritte mit Mobile Services]: /develop/mobile/tutorials/get-started-ios
[Erste Schritte mit Daten]: /develop/mobile/tutorials/get-started-with-data-ios
[Erste Schritte mit der Authentifizierung]: /develop/mobile/tutorials/get-started-with-users-ios
[Erste Schritte mit Pushbenachrichtigungen]: /develop/mobile/tutorials/get-started-with-push-ios
[Autorisieren von Benutzern mit Skripts]: /develop/mobile/tutorials/authorize-users-in-scripts-ios

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/

<!--HONumber=49-->