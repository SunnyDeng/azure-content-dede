<properties
	pageTitle="Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden (iOS) | Microsoft Azure"
	description="Erfahren Sie, wie Sie Benutzer für das für einmalige Anmelden mit ADAL in Ihrer iOS-Anwendung authentifizieren."
	documentationCenter="ios"
	authors="mattchenderson"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="08/18/2015" 
	ms.author="mahender"/>

# Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../../includes/mobile-services-selector-adal-sso.md)]

##Übersicht

In diesem Lernprogramm fügen Sie die Authentifizierung mithilfe der Active Directory-Authentifizierungsbibliothek zum Schnellstartprojekt hinzu.

Um Benutzer authentifizieren zu können, müssen Sie Ihre Anwendung in Azure Active Directory (AAD) registrieren. Dazu sind zwei Schritte erforderlich. Zunächst müssen Sie Ihren mobilen Dienst registrieren und entsprechende Berechtigungen vergeben. Zweitens müssen Sie Ihre iOS-App registrieren und ihr Zugriff auf diese Berechtigungen geben.


>[AZURE.NOTE]In diesem Lernprogramm erfahren Sie, wie Sie die Authentifizierung per einmaliger Anmeldung in Azure Active Directory für iOS-Apps umsetzen können. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen.


##Voraussetzungen


Für dieses Lernprogramm ist Folgendes erforderlich:

* Xcode 4.5 und iOS 6.0 (oder neuere Versionen)
* Abschluss des Lernprogramms [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten].
* Microsoft Azure Mobile Services SDK
* Die [Active Directory-Authentifizierungsbibliothek für iOS]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-client](../../includes/mobile-services-dotnet-adal-register-client.md)]

##Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

##Hinzufügen des Authentifizierungscodes zur Client-App

1. Laden Sie die [Active Directory-Authentifizierungsbibliothek für iOS] herunter und schließen Sie sie in Ihr Projekt ein. Achten Sie darauf, auch die Storyboards der ADAL-Quelle hinzuzufügen.

2. Schließen Sie im QSTodoListViewController ADAL mit Folgendem ein:

        #import "ADALiOS/ADAuthenticationContext.h"

2. Fügen Sie anschließend die folgende Methode ein:

        - (void) loginAndGetData
        {
            MSClient *client = self.todoService.client;
            if (client.currentUser != nil) {
                return;
            }

            NSString *authority = @"<INSERT-AUTHORITY-HERE>";
            NSString *resourceURI = @"<INSERT-RESOURCE-URI-HERE>";
            NSString *clientID = @"<INSERT-CLIENT-ID-HERE>";
            NSString *redirectURI = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError *error;
            ADAuthenticationContext *authContext = [ADAuthenticationContext authenticationContextWithAuthority:authority error:&error];
            NSURL *redirectUri = [[NSURL alloc]initWithString:redirectURI];

            [authContext acquireTokenWithResource:resourceURI clientId:clientID redirectUri:redirectUri completionBlock:^(ADAuthenticationResult *result) {
                if (result.tokenCacheStoreItem == nil)
                {
                    return;
                }
                else
                {
                    NSDictionary *payload = @{
                        @"access_token" : result.tokenCacheStoreItem.accessToken
                    };
                    [client loginWithProvider:@"windowsazureactivedirectory" token:payload completion:^(MSUser *user, NSError *error) {
                        [self refresh];
                    }];
                }
            }];
        }


6. Ersetzen Sie im Code für die oben angegebene `loginAndGetData`-Methode **INSERT-AUTHORITY-HERE** durch den Namen des Mandanten, in dem Sie Ihre Anwendung bereitgestellt haben. Das Format sollte https://login.windows.net/tenant-name.onmicrosoft.com sein. Sie können diesen Wert in der Registerkarte Domäne in Ihrem Azure Active Directory im [Azure-Verwaltungsportal] kopieren.

7. Ersetzen Sie im Code für die oben angegebene `loginAndGetData`-Methode **INSERT-RESOURCE-URI-HERE** durch die **URI der APP-ID** für Ihren mobilen Dienst. Wenn Sie das Thema [Registrieren in Azure Active Directory] befolgt haben, sollte der URI Ihrer App-ID in etwa so aussehen: https://todolist.azure-mobile.net/login/aad.

8. Ersetzen Sie im Code für die oben angegebene `loginAndGetData`-Methode **INSERT-CLIENT-ID-HERE** durch die Client-ID, die Sie aus der systemeigenen Clientanwendung kopiert haben.

9. Ersetzen Sie im Code in der obigen `loginAndGetData`-Methode den Text **INSERT-REDIRECT-URI-HERE** durch den/login/done-Endpunkt für den mobilen Dienst. Das ist vergleichbar mit https://todolist.azure-mobile.net/login/done.


3. Modifizieren Sie im QSTodoListViewController `ViewDidLoad`, indem Sie `[self refresh]` durch Folgendes ersetzen:

        [self loginAndGetData];

##Testen des Clients mit Authentifizierung

1. Klicken Sie im Menü „Produkt“ auf „Run“, um die App zu starten.
2. Sie werden aufgefordert, sich bei Ihrem Azure Active Directory anzumelden.  
3. Die App wird authentifiziert sich und gibt die TodoItem-Elemente zurück.

   ![](./media/mobile-services-dotnet-backend-ios-adal-sso-authentication/mobile-services-app-run.png)



<!-- URLs. -->
[Erste Schritte mit Daten]: mobile-services-ios-get-started-data.md
[Erste Schritte mit Mobile Services]: mobile-services-dotnet-backend-ios-get-started.md
[Registrieren in Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Active Directory-Authentifizierungsbibliothek für iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios

<!---HONumber=August15_HO8-->