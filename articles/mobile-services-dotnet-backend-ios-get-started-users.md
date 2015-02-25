<properties pageTitle="Erste Schritte mit der Authentifizierung (Windows Store) | Mobile Dev Center" description="Erfahren Sie, wie Sie Mobile Services verwenden, um die Benutzer Ihrer Windows Store-App über verschiedene Identitätsanbieter, einschließlich Google, Facebook, Twitter und Microsoft, zu authentifizieren." services="mobile-services" documentationCenter="ios" authors="krisragh" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="krisragh"/>

# Hinzufügen von Authentifizierung zur Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1. [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2. [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3. [Hinzufügen von Authentifizierung zur App]
4. [Speichern von Authentifizierungstoken in der App]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen.

##<a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

<ol start="6">
<li><p>Öffnen Sie in Xcode das Schnellstartprojekt der Client-App.</li>
<li><p>Klicken Sie auf <strong>Ausführen</strong>, um das Projekt zu erstellen und die App im iPhone-Emulator zu starten. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) ausgelöst wird.<p>

   	<p>Dies liegt daran, dass die App versucht, als nicht authentifizierter Benutzer auf Mobile Services zuzugreifen, die <em>TodoItem</em>-Tabelle jetzt jedoch Authentifizierung erfordert.</p></li>
</ol>

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

[AZURE.INCLUDE [mobile-services-ios-authenticate-app](../includes/mobile-services-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Speichern von Authentifizierungstoken in der App

[AZURE.INCLUDE [mobile-services-ios-authenticate-app-with-token](../includes/mobile-services-ios-authenticate-app-with-token.md)]

##<a name="next-steps"></a>Nächste Schritte

Im nächsten Lernprogramm [Serviceseitige Autorisierung von Mobile Services-Benutzern][ Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern.

<!-- Anchors. -->
[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
[Hinzufügen von Authentifizierung zur App]: #add-authentication
[Nächste Schritte]:#next-steps
[Speichern von Authentifizierungstoken in der App]:#store-authentication

<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-data/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-push/
[Autorisieren von Benutzern mit Skripts]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-authorize-users-in-scripts

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library
[Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung]: /de-de/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication


<!--HONumber=42-->
