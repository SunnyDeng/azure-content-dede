<properties linkid="develop-mobile-tutorials-sso-with-adal-ios" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your iOS application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc,mahender" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc,mahender" />

# Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden

<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="Windows Store C#" >Windows Store C#</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" class="current">iOS</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS">Xamarin.iOS</a>
</div>

In diesem Lernprogramm fügen Sie die Authentifizierung mithilfe der Active Directory-Authentifizierungsbibliothek zum Schnellstartprojekt hinzu.

Um Benutzer authentifizieren zu können, müssen Sie Ihre Anwendung in Azure Active Directory (AAD) registrieren. Dazu sind zwei Schritte erforderlich. Zunächst müssen Sie Ihren mobilen Dienst registrieren und entsprechende Berechtigungen vergeben. Zweitens müssen Sie Ihre iOS-App registrieren und ihr Zugriff auf diese Berechtigungen geben.


>[WACOM.NOTE] In diesem Lernprogramm erfahren Sie, wie Sie die Authentifizierung per einmaliger Anmeldung in Azure Active Directory für iOS-Apps umsetzen können. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie zunächst das Lernprogramm [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Registrieren Ihres mobilen Diensts beim Azure Active Directory][Registrieren Ihres mobilen Diensts beim Azure Active Directory]
2. [Registrieren Ihre App beim Azure Active Directory][Registrieren Ihre App beim Azure Active Directory]
3. [Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung][Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung]
4. [Hinzufügen des Authentifizierungscodes zur Client-App][Hinzufügen des Authentifizierungscodes zur Client-App]
5. [Testen des Clients mit Authentifizierung][Testen des Clients mit Authentifizierung]

Für dieses Lernprogramm ist Folgendes erforderlich:

* Xcode 4.5 und iOS 6.0 (oder neuere Versionen)
* Abschluss des Lernprogramms [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten][Erste Schritte mit Daten].
* Microsoft Azure Mobile Services SDK
* Die [Active Directory-Authentifizierungsbibliothek für iOS][Active Directory-Authentifizierungsbibliothek für iOS]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-client](../includes/mobile-services-dotnet-adal-register-client.md)]

## <a name="require-authentication"></a>Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>Hinzufügen des Authentifizierungscodes zur Client-App

1. Laden Sie die [Active Directory-Authentifizierungsbibliothek für iOS][Active Directory-Authentifizierungsbibliothek für iOS] herunter und schließen Sie sie in Ihr Projekt ein. Achten Sie darauf, auch die Storyboards der ADAL-Quelle hinzuzufügen.

2. Schließen Sie im QSTodoListViewController ADAL mit Folgendem ein:

        #import "ADALiOS/ADAuthenticationContext.h"

3. Fügen Sie anschließend die folgende Methode ein:

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


4. Ersetzen Sie im Code für die oben angegebene `loginAndGetData`-Methode **INSERT-AUTHORITY-HERE** durch den Namen des Mandanten, in dem Sie Ihre Anwendung bereitgestellt haben. Verwenden Sie dabei das Format <https://login.windows.net/tenant-name.onmicrosoft.com>. Sie können diesen Wert in der Registerkarte Domäne in Ihrem Azure Active Directory im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] kopieren.

5. Ersetzen Sie im Code für die oben angegebene `loginAndGetData`-Methode **INSERT-RESOURCE-URI-HERE** durch die **URI der APP-ID** für Ihren mobilen Dienst. Wenn Sie das Thema [Registrieren in Azure Active Directory][Registrieren in Azure Active Directory] verfolgt haben, sollte Ihre URI der App-ID in etwa so aussehen: <https://todolist.azure-mobile.net/login/aad>.

6. Ersetzen Sie im Code für die oben angegebene `loginAndGetData`-Methode **INSERT-CLIENT-ID-HERE** durch die Client-ID, die Sie aus der systemeigenen Clientanwendung kopiert haben.

7. Ersetzen Sie im Code in der obigen Methode `loginAndGetData` den Text **INSERT-REDIRECT-URI-HERE** mit dem /login/done Endpunkt für Ihren mobilen Dienst. Das ist vergleichbar mit <https://todolist.azure-mobile.net/login/done>.

8. Modifizieren Sie im QSTodoListViewController `ViewDidLoad`, indem Sie `[self refresh]` mit Folgendem ersetzen:

        [self loginAndGetData];

## <a name="test-client"></a>Testen des Clients mit Authentifizierung

1. Klicken Sie im Menü „Produkt“ auf „Run“, um die App zu starten.
2. Sie werden aufgefordert, sich bei Ihrem Azure Active Directory anzumelden.
3. Die App authentifiziert sich und gibt die TodoItems zurück.

![][0]

 


[Windows Store C#]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication "Windows Store C#"
[iOS]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication "iOS"
[Xamarin.iOS]: /de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication "Xamarin.iOS"
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started/
[Registrieren Ihres mobilen Diensts beim Azure Active Directory]: #register-mobile-service-aad
[Registrieren Ihre App beim Azure Active Directory]: #register-app-aad
[Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung]: #require-authentication
[Hinzufügen des Authentifizierungscodes zur Client-App]: #add-authentication-code
[Testen des Clients mit Authentifizierung]: #test-client
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-ios-get-started-data/
[Active Directory-Authentifizierungsbibliothek für iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
[mobile-services-dotnet-adal-register-service]: ../includes/mobile-services-dotnet-adal-register-service.md
[mobile-services-dotnet-adal-register-client]: ../includes/mobile-services-dotnet-adal-register-client.md
[mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Registrieren in Azure Active Directory]: /de-de/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[0]: ./media/mobile-services-dotnet-backend-ios-adal-sso-authentication/mobile-services-app-run.png
