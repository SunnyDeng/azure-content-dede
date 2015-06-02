<properties 
	pageTitle="Erste Schritte mit der Authentifizierung für mobile Anwendungen in iOS" 
	description="Erfahren Sie, wie Sie Mobile Apps zum Authentifizieren Ihrer iOS-App über eine Vielzahl von Identitätsanbietern nutzen können, darunter AAD, Google, Facebook, Twitter und Microsoft." 
	services="app-service\mobile" 
	documentationCenter="ios" 
	authors="mattchenderson,krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/22/2015" 
	ms.author="mahender"/>

# Hinzufügen der Authentifizierung zu Ihrer iOS-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../includes/app-service-mobile-selector-get-started-users.md)]

In diesem Thema wird die Authentifizierung von Benutzern einer mobilen App Service-App über Ihre Clientanwendung veranschaulicht. In diesem Lernprogramm fügen Sie dem Schnellstartprojekt durch Verwenden eines von App Service unterstützten Identitätsanbieters eine Authentifizierungsfunktion hinzu. Nach der erfolgreichen Authentifizierung und Autorisierung durch Ihre mobile App wird die Benutzer-ID angezeigt.

Dieses Lernprogramm baut auf dem Mobile App-Schnellstart auf. Sie müssen außerdem zunächst das Lernprogramm [Erstellen einer iOS-App] abschließen.

##<a name="register"></a>Registrieren Ihrer App für die Authentifizierung und Konfigurieren von App Services

[AZURE.INCLUDE [app-service-mobile-register-authentication](../includes/app-service-mobile-register-authentication.md)] 

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)] 

<ol start="7">
<li><p>Öffnen Sie das Projekt in Xcode. Klicken Sie auf die Schaltfläche <b>Ausführen</b>, um die App zu starten. Vergewissern Sie sich, dass nach dem Start der App ein Ausnahmefehler mit dem Statuscode&#160;401 (nicht autorisiert) ausgelöst wird.</p>
   
   	<p>Dies liegt daran, dass die App versucht, als nicht authentifizierter Benutzer auf den Code Ihrer mobilen App zuzugreifen, die <em>TodoItem</em>-Tabelle jetzt jedoch Authentifizierung erfordert.</p></li>
</ol>

Als Nächstes aktualisieren Sie die App, um Benutzer zu authentifizieren, bevor diese Ressourcen von App Service anfordern.

##<a name="add-authentication"></a>Hinzufügen der Authentifizierung zur App

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app](../includes/app-service-mobile-ios-authenticate-app.md)]

##<a name="store-authentication"></a>Speichern von Authentifizierungstoken in der App

[AZURE.INCLUDE [app-service-mobile-ios-authenticate-app-with-token](../includes/app-service-mobile-ios-authenticate-app-with-token.md)]


<!-- URLs. -->

[Erstellen einer iOS-App]: app-service-mobile-dotnet-backend-ios-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
<!--HONumber=54-->