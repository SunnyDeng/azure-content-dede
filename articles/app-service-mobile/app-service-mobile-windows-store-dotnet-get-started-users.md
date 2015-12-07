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
	ms.date="11/23/2015"
	ms.author="glenga"/>

# Hinzufügen der Authentifizierung zu Ihrer Windows-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

In diesem Thema erfahren Sie, wie Sie Ihrer mobilen App eine cloudbasierte Authentifizierung hinzufügen können. In diesem Tutorial fügen Sie dem Mobile Apps-Schnellstartprojekt eine Authentifizierung hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Azure Mobile Apps unterstützt wird. Nach erfolgreicher Authentifizierung und Autorisierung durch das Back-End Ihrer mobilen App wird die Benutzer-ID angezeigt.

Dieses Tutorial baut auf dem Mobile App-Schnellstart auf. Sie müssen zunächst das Tutorial [Erste Schritte mit Mobile Apps](app-service-mobile-windows-store-dotnet-get-started.md) durchlaufen.

##<a name="register"></a>Registrieren Ihrer App für die Authentifizierung und Konfigurieren von App Service

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Drücken Sie mit einem der Windows-App-Projekte als Startprojekt zum Ausführen der App F5. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde. Dies liegt daran, dass die App versucht, als nicht authentifizierter Benutzer auf den Code Ihrer mobilen App zuzugreifen, die *TodoItem*-Tabelle jetzt jedoch Authentifizierung erfordert.

Als Nächstes aktualisieren Sie die App, um Benutzer zu authentifizieren, bevor diese Ressourcen von App Service anfordern.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app](../../includes/mobile-windows-universal-dotnet-authenticate-app.md)]


##<a name="tokens"></a>Speichern des Authentifizierungstokens auf dem Client

Im vorhergehenden Beispiel wurde eine Standardanmeldung gezeigt, die erfordert, dass der Client bei jedem Start der App sowohl den Identitätsanbieter als auch den App Service kontaktiert. Diese Methode ist nicht nur ineffizient, sie kann auch zu nutzungsbezogenen Problemen führen, wenn eine große Anzahl von Kunden die App gleichzeitig starten sollte. Ein besserer Ansatz ist es daher, das vom App Service zurückgegebene Authentifizierungstoken zwischenzuspeichern und vor einer anbieterbasierten Anmeldung zu verwenden.

>[AZURE.NOTE]Unabhängig davon, ob Sie clientverwaltete oder dienstverwaltete Authentifizierung verwenden, können Sie den von App Service ausgestellten Authentifizierungstoken zwischenspeichern. In diesem Lernprogramm wird die dienstverwaltete Authentifizierung verwendet.

[AZURE.INCLUDE [mobile-windows-universal-dotnet-authenticate-app-with-token](../../includes/mobile-windows-universal-dotnet-authenticate-app-with-token.md)]

##Nächste Schritte

Nachdem Sie dieses einfache Tutorial zur Authentifizierung abgeschlossen haben, können Sie mit den folgenden Tutorials fortfahren:

+ [Hinzufügen von Pushbenachrichtigungen zu Ihrer Windows-App](app-service-mobile-windows-store-dotnet-get-started-push.md) Erfahren Sie, wie Sie Pushbenachrichtigungen zum Unterstützen Ihrer App hinzufügen und Ihr Mobile App-Back-End für die Verwendung von Azure Notification Hubs zum Senden von Pushbenachrichtigungen konfigurieren.

+ [Aktivieren der Offlinesynchronisierung für Ihre Windows-App](app-service-mobile-windows-store-dotnet-get-started-offline-data.md) Erfahren Sie, wie Sie mithilfe eines Mobile App-Back-Ends Ihrer App Offlineunterstützung hinzufügen. Die Offlinesynchronisierung ermöglicht Endbenutzern die Interaktion mit einer mobilen App (Anzeigen, Hinzufügen und Ändern von Daten), auch wenn keine Netzwerkverbindung vorhanden ist.


<!-- URLs. -->
[Get started with your mobile app]: app-service-mobile-windows-store-dotnet-get-started.md
 

<!---HONumber=AcomDC_1125_2015-->