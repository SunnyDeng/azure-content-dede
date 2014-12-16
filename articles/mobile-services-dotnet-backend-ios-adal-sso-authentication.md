<properties urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your iOS application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc,mahender" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="10/10/2014" ms.author="wesmc,mahender" />

# Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden

[WACOM.INCLUDE [mobile-services-selector-adal-sso](../includes/mobile-services-selector-adal-sso.md)]

In diesem Lernprogramm fügen Sie die Authentifizierung mithilfe der Active Directory-Authentifizierungsbibliothek zum Schnellstartprojekt hinzu.

Um Benutzer authentifizieren zu können, müssen Sie Ihre Anwendung in Azure Active Directory (AAD) registrieren. Dazu sind zwei Schritte erforderlich. Zunächst müssen Sie Ihren mobilen Dienst registrieren und entsprechende Berechtigungen vergeben. Zweitens müssen Sie Ihre iOS-App registrieren und ihr Zugriff auf diese Berechtigungen geben.


>[WACOM.NOTE] In diesem Lernprogramm erfahren Sie, wie Sie die Authentifizierung per einmaliger Anmeldung in Azure Active Directory für iOS-Apps umsetzen können. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Registrieren Ihres mobilen Diensts beim Azure Active Directory]
2. [Registrieren Ihre App beim Azure Active Directory]
3. [Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung]
4. [Hinzufügen des Authentifizierungscodes zur Client-App]
5. [Testen des Clients mit Authentifizierung]

Für dieses Lernprogramm ist Folgendes erforderlich:

* XCode 4.5 und iOS 6.0 (oder neuere Versionen)
* Abschluss des Lernprogramms [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten].
* Microsoft Azure Mobile Services SDK
* Die [Active Directory-Authentifizierungsbibliothek für iOS]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-client](../includes/mobile-services-dotnet-adal-register-client.md)]

## <a name="require-authentication"></a>Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>Hinzufügen des Authentifizierungscodes zur Client-App

1. Laden Sie die [Active Directory-Authentifizierungsbibliothek für iOS] herunter, und schließen Sie sie in Ihr Projekt ein. Achten Sie darauf, auch die Storyboards der ADAL-Quelle hinzuzufügen.

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


6. Ersetzen Sie im Code der obigen obigen Methode`loginAndGetData`-Methode den Text **INSERT-AUTHORITY-HERE** durch den Namen des Mandanten, in dem Sie die Anwendung bereitstellen. Der Name sollte folgendes Format haben: https://login.windows.net/tenant-name.onmicrosoft.com. Sie können diesen Wert von der Registerkarte "Domäne" in Azure Active Directory im[Azure-Verwaltungsportal] kopieren.

7. Ersetzen Sie im Code für die oben angegebene `loginAndGetData`-Methode den Text **INSERT-RESOURCE-URI-HERE** durch die **App-ID-URI** des mobilen Diensts. Wenn Sie das Thema [Registrieren in Azure Active Directory] verfolgt haben, sollte Ihre URI der App-ID in etwa so aussehen:

8. Ersetzen Sie im Code für die oben angegebene `loginAndGetData`-Methode den Text **INSERT-CLIENT-ID-HERE** durch die Client-ID, die Sie aus der systemeigenen Clientanwendung kopiert haben.

9. Ersetzen Sie im Code in der obigen Methode `loginAndGetData` den Text **INSERT-REDIRECT-URI-HERE** durch den /login/done-Endpunkt für Ihren mobilen Dienst. Das ist vergleichbar mit https://todolist.azure-mobile.net/login/done.


3. Ändern Sie im "QSTodoListViewController" `ViewDidLoad`, indem Sie `[self refresh]` durch Folgendes:

        [self loginAndGetData];

## <a name="test-client"></a>Testen des Clients mit Authentifizierung

1. Klicken Sie im Menü "Produkt" auf "Run", um die App zu starten.
2. Sie werden aufgefordert, sich bei Ihrem Azure Active Directory anzumelden.  
3. Die App authentifiziert sich und gibt die TodoItems zurück.

   ![](./media/mobile-services-dotnet-backend-ios-adal-sso-authentication/mobile-services-app-run.png)

<!-- Anchors. -->
[Registrieren Ihres mobilen Diensts beim Azure Active Directory]: #register-mobile-service-aad
[Registrieren Ihre App beim Azure Active Directory]: #register-app-aad
[Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung]: #require-authentication
[Hinzufügen des Authentifizierungscodes zur Client-App]: #add-authentication-code
[Testen des Clients mit Authentifizierung]: #test-client

<!-- URLs. -->
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-ios-get-started-data/
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[So registrieren Sie sich beim Azure Active Directory]: /de-de/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Active Directory-Authentifizierungsbibliothek für iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
