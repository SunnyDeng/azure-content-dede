<properties
	pageTitle="Hinzufügen von Authentifizierung zu Apache Cordova mit Mobile Apps| Azure App Service"
	description="Erfahren Sie, wie Sie Mobile Apps in Azure App Service verwenden, um die Benutzer Ihrer Apache Cordova-App über verschiedene Identitätsanbieter, einschließlich Google, Facebook, Twitter und Microsoft, zu authentifizieren."
	services="app-service\mobile"
	documentationCenter="javascript"
	authors="adrianhall"
	manager="ggailey777"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="na"
	ms.tgt_pltfrm="mobile-html"
	ms.devlang="javascript"
	ms.topic="article"
	ms.date="02/11/2016"
	ms.author="adrianha"/>

# Hinzufügen von Authentifizierung zu Ihrer Apache Cordova-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

## Zusammenfassung

In diesem Tutorial fügen Sie dem Aufgabenlisten-Schnellstartprojekt unter Apache Cordova mithilfe eines unterstützten Identitätsanbieters eine Authentifizierung hinzu. Dieses Tutorial baut auf dem Tutorial [Erste Schritte mit Mobile Apps] auf, das Sie zuerst abschließen müssen.

##<a name="register"></a>Registrieren Ihrer App für die Authentifizierung und Konfigurieren von App Service

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

+ Öffnen Sie das im Rahmen des Tutorials [Erste Schritte mit Mobile Apps] erstellte Projekt in Visual Studio. Führen Sie die Anwendung im **Google Android Emulator** aus, und überprüfen Sie, ob nach dem Start der App ein unerwarteter Verbindungsfehler angezeigt wird.

    Dies liegt daran, dass die App versucht, als nicht authentifizierter Benutzer auf das Back-End zuzugreifen. Das Back-End leitet den Benutzer an eine Authentifizierungsseite weiter, die OAuth verwendet. Die Anwendung vertraut jedoch dem OAuth-Endpunkt nicht.

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile App-Back-End anfordern.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

1. Öffnen Sie Ihr Projekt in **Visual Studio**, und öffnen Sie dann die Datei `www/index.html` zur Bearbeitung.

2. Suchen Sie das `Content-Security-Policy`-Metatag im Kopfzeilenbereich. Sie müssen den OAuth-Host der Liste der zulässigen Quellen hinzufügen.

    | Anbieter | Name des SDK-Anbieters | OAuth-Host |
    | :--------------------- | :---------------- | :-------------------------- |
    | Azure Active Directory | aad | https://login.windows.net |
    | Facebook | facebook | https://www.facebook.com |
    | Google | google | https://accounts.google.com |
    | Microsoft | microsoftaccount | https://login.live.com |
    | Twitter | twitter | https://api.twitter.com |

    Hier ein Beispiel für Content-Security-Policy (implementiert für Azure Active Directory):

        <meta http-equiv="Content-Security-Policy" content="default-src 'self'
			data: gap: https://login.windows.net https://yourapp.azurewebsites.net; style-src 'self'">

    Ersetzen Sie `https://login.windows.net` durch den OAuth-Host aus der oben stehenden Tabelle. Weitere Informationen zu diesem Metatag finden Sie in der Dokumentation [Whitelist Guide] (Whitelist-Ratgeber).

    Beachten Sie, dass einige Authentifizierungsanbieter bei der Verwendung auf geeigneten Mobilgeräten keine Änderungen an Content-Security-Policy erfordern. Beispielsweise sind bei Verwendung der Google-Authentifizierung auf einem Android-Gerät keine Änderungen an Content-Security-Policy notwendig.

3. Öffnen Sie die Datei `www/js/index.js` zur Bearbeitung, suchen Sie die `onDeviceReady()`-Methode, und fügen Sie unterhalb des Codes zur Clienterstellung Folgendes ein:

        // Login to the service
        client.login('SDK_Provider_Name')
            .then(function () {

                // BEGINNING OF ORIGINAL CODE

                // Create a table reference
                todoItemTable = client.getTable('todoitem');

                // Refresh the todoItems
                refreshDisplay();

                // Wire up the UI Event Handler for the Add Item
                $('#add-item').submit(addItemHandler);
                $('#refresh').on('click', refreshDisplay);

                // END OF ORIGINAL CODE

            }, handleError);

    Beachten Sie, dass dieser Code den vorhandenen Code ersetzt, der die Tabellenreferenz erstellt und die Benutzeroberfläche aktualisiert.

    Die Login()-Methode startet die Authentifizierung mit dem Anbieter. Die login()-Methode ist eine asynchrone Funktion, die eine JavaScript-Zusage zurückgibt. Der Rest der Initialisierung wird in der Zusagenantwort platziert, sodass sie erst ausgeführt wird, wenn die login()-Methode abgeschlossen ist.

4. Ersetzen Sie in dem gerade hinzugefügten Code `SDK_Provider_Name` durch den Namen des Login-Anbieters. Verwenden Sie für Azure Active Directory beispielsweise `client.login('aad')`.

4. Führen Sie das Projekt aus. Wenn die Initialisierung des Projekts abgeschlossen ist, zeigt Ihre Anwendung die OAuth-Anmeldeseite für den ausgewählten Authentifizierungsanbieter an.

##<a name="next-steps"></a>Nächste Schritte

* Erfahren Sie mehr über die [Authentifizierung] mit Azure App Service.
* Führen Sie das Tutorial fort, indem Sie Ihrer Apache Cordova-App [Pushbenachrichtigungen] hinzufügen.

Erfahren Sie, wie Sie die SDKs nutzen,

* [Apache Cordova-SDK]
* [ASP.NET Server-SDK]
* [Node.js Server-SDK]

<!-- URLs. -->
[Erste Schritte mit Mobile Apps]: app-service-mobile-cordova-get-started.md
[Whitelist Guide]: https://cordova.apache.org/docs/en/latest/guide/appdev/whitelist/index.html
[Pushbenachrichtigungen]: app-service-mobile-cordova-get-started-push.md
[Authentifizierung]: app-service-mobile-auth.md
[Apache Cordova-SDK]: app-service-mobile-codova-how-to-use-client-library.md
[ASP.NET Server-SDK]: app-service-mobile-dotnet-backend-how-to-use-server-sdk.md
[Node.js Server-SDK]: app-service-mobile-node-backend-how-to-use-server-sdk.md

<!---HONumber=AcomDC_0323_2016-->