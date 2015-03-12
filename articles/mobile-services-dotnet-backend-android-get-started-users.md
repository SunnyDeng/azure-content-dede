<properties 
	pageTitle="Erste Schritte mit der Authentifizierung (Android) | Mobile Dev Center" 
	description="Erfahren Sie, wie Sie Mobile Services verwenden, um die Benutzer Ihrer Windows Store-App über verschiedene Identitätsanbieter, einschließlich Google, Facebook, Twitter und Microsoft, zu authentifizieren." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="09/27/2014" 
	ms.author="mahender"/>

# Hinzufügen von Authentifizierung zur Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1. [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2. [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3. [Hinzufügen von Authentifizierung zur App]
4. [Speichern von Authentifizierungstoken auf dem Client]
5. [Aktualisieren abgelaufener Token]


Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen. 

##<a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

3. Öffnen Sie in Eclipse das Projekt, das Sie im Lernprogramm [Erste Schritte mit Mobile Services] erstellt haben. 

4. Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode 401 (nicht autorisiert) ausgegeben wird. 

	 Dies liegt daran, dass die App versucht, als nicht authentifizierter Benutzer auf Mobile Services zuzugreifen, für die TodoItem_TodoItem_-Tabelle jetzt jedoch eine Authentifizierung erforderlich ist.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

[AZURE.INCLUDE [mobile-services-android-authenticate-app](../includes/mobile-services-android-authenticate-app.md)]

## <a name="cache-tokens"></a>Zwischenspeichern von Authentifizierungstoken auf dem Client

[AZURE.INCLUDE [mobile-services-android-authenticate-app-with-token](../includes/mobile-services-android-authenticate-app-with-token.md)] 

## <a name="refresh-tokens"></a>Aktualisieren des Tokencaches

[AZURE.INCLUDE [mobile-services-android-authenticate-app-refresh-token](../includes/mobile-services-android-authenticate-app-refresh-token.md)] 

##<a name="next-steps"></a>Nächste Schritte

Im nächsten Lernprogramm [Dienstseitige Autorisierung von Mobile Services-Benutzern][Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um die von Mobile Services zurückgegebenen Daten zu filtern. 


<!-- Anchors. -->
[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
[Hinzufügen von Authentifizierung zur App]: #add-authentication
[Speichern von Authentifizierungstoken auf dem Client]: #cache-tokens
[Aktualisieren abgelaufener Token]: #refresh-tokens
[Nächste Schritte]:#next-steps

<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-data/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-push/
[Autorisieren von Benutzern mit Skripts]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-authorize-users-in-scripts

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library
[Registrieren Ihres Windows Store-App-Pakets für die Microsoft Authentifizierung]: /de-de/documentation/articles/mobile-services-how-to-register-store-app-package-microsoft-authentication


<!--HONumber=42-->
