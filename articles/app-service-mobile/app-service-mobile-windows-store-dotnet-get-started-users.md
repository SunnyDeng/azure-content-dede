<properties
	pageTitle="Hinzufügen von Authentifizierung zu Ihrer universellen Windows 8.1-Runtime-App | Azure Mobile Apps"
	description="Erfahren Sie, wie Sie Azure Mobile App Service-Apps zum Authentifizieren der Benutzer Ihrer Windows-App über eine Vielzahl von Identitätsanbietern nutzen können, z. B. AAD, Google, Facebook, Twitter und Microsoft."
	services="app-service\mobile"
	documentationCenter="windows"
	authors="mattchenderson" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/22/2015"
	ms.author="glenga"/>

# Hinzufügen der Authentifizierung zu Ihrer Windows-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

In diesem Thema wird die Authentifizierung von Benutzern einer mobilen App Service-App über Ihre Clientanwendung veranschaulicht. In diesem Lernprogramm fügen Sie dem Schnellstartprojekt durch Verwenden eines von App Service unterstützten Identitätsanbieters eine Authentifizierungsfunktion hinzu. Nach der erfolgreichen Authentifizierung und Autorisierung durch Ihre mobile App wird die Benutzer-ID angezeigt.

Dieses Lernprogramm baut auf dem Mobile App-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Ihrer mobilen App] abschließen.

##<a name="create-gateway"></a>Erstellen eines App Service-Gateways

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-gateway](../../includes/app-service-mobile-dotnet-backend-create-gateway.md)]

##<a name="register"></a>Registrieren Ihrer App für die Authentifizierung und Konfigurieren von App Service

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

&nbsp;&nbsp;4. Öffnen Sie in Visual Studio die freigegebene Projektdatei „App.Xaml.cs“, und stellen Sie sicher, dass die **MobileServiceClient**-Instanz so konfiguriert ist, dass sowohl die URL des Mobile App-Back-Ends als auch die des Gateways verwendet werden kann.

&nbsp;&nbsp;5. Drücken Sie mit einem der Windows-App-Projekte als Startprojekt zum Ausführen der App F5. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.

&nbsp;&nbsp;Dies liegt daran, dass die App versucht, als nicht authentifizierter Benutzer auf den Code der mobilen App zuzugreifen, die *TodoItem*-Tabelle jetzt jedoch Authentifizierung erfordert.

Als Nächstes aktualisieren Sie die App, um Benutzer zu authentifizieren, bevor diese Ressourcen von App Service anfordern.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

[AZURE.INCLUDE [app-service-mobile-windows-universal-dotnet-authenticate-app](../../includes/app-service-mobile-windows-universal-dotnet-authenticate-app.md)]


##<a name="tokens"></a>Speichern des Authentifizierungstokens auf dem Client

[AZURE.INCLUDE [app-service-mobile-windows-store-dotnet-authenticate-app-with-token](../../includes/app-service-mobile-windows-store-dotnet-authenticate-app-with-token.md)]

##Nächste Schritte


<!-- URLs. -->
[Erste Schritte mit Ihrer mobilen App]: app-service-mobile-windows-store-dotnet-get-started.md
 

<!---HONumber=Nov15_HO1-->