<properties
	pageTitle="Hinzufügen von Authentifizierung auf iOS mit Azure Mobile Apps"
	description="Erfahren Sie, wie Sie Azure Mobile Apps zum Authentifizieren Ihrer iOS-App über eine Vielzahl von Identitätsanbietern nutzen können, darunter AAD, Google, Facebook, Twitter und Microsoft."
	services="app-service\mobile"
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="02/04/2016"
	ms.author="krisragh"/>

# Hinzufügen der Authentifizierung zu Ihrer iOS-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

In diesem Lernprogramm fügen Sie dem [iOS-Schnellstartprojekt] mithilfe eines unterstützten Identitätsanbieters eine Authentifizierung hinzu. Dieses Lernprogramm baut auf dem [iOS-Schnellstartlernprogramm] auf, das Sie zuerst abschließen müssen.

##<a name="register"></a>Registrieren Ihrer App für die Authentifizierung und Konfigurieren von App Service

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Klicken Sie in Xcode auf **Run**, um die App zu starten. Es wird eine Ausnahme ausgelöst, da die App als nicht authentifizierter Benutzer auf das Back-End zugreift, aber die _TodoItem_-Tabelle nun eine Authentifizierung verlangt.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../../includes/app-service-mobile-ios-authenticate-app.md)]


<!-- URLs. -->

[iOS-Schnellstartlernprogramm]: app-service-mobile-ios-get-started.md
[iOS-Schnellstartprojekt]: app-service-mobile-ios-get-started.md

[Azure portal]: https://portal.azure.com

<!---HONumber=AcomDC_0211_2016-->