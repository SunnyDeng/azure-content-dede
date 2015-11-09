<properties
        pageTitle="Authentifizieren von Benutzern Ihrer iOS-App mit Azure Active Directory-Anmeldung"
        description="Erfahren Sie, wie Benutzer sich mithilfe der Active Directory-Authentifizierungsbibliothek bei Ihrer iOS-Anwendung anmelden."
        documentationCenter="Mobile"
        authors="mattchenderson"
        services="app-service\mobile"
        manager="dwrede" />

<tags ms.service="app-service-mobile"
ms.workload="mobile"
ms.tgt_pltfrm="mobile-ios"
ms.devlang="objective-c"
ms.topic="article"
ms.date="09/14/2015"
ms.author="mahender" />

# Hinzufügen der Azure Active Directory-Anmeldung zu Ihrer iOS-App

[AZURE.INCLUDE [app-service-mobile-selector-aad-sso](../../includes/app-service-mobile-selector-aad-sso.md)]

[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

In diesem Lernprogramm fügen Sie die Authentifizierung mithilfe der Active Directory-Authentifizierungsbibliothek (Active Directory Authentication Library, ADAL) zum Schnellstartprojekt hinzu. Sie können die Authentifizierung auch mit weniger Konfigurationsaufwand nur mithilfe des Mobile Apps-SDK aktivieren, wie im Lernprogramm [Hinzufügen von Authentifizierung zur App] beschrieben. Dieses Thema zur ADAL bietet Endbenutzern eine integriertere Authentifizierung, und die ADAL bietet umfassendere Funktionen für den Zugriff auf andere von AAD geschützte Ressourcen.

Um Benutzer mithilfe der ADAL authentifizieren zu können, müssen Sie Ihre Anwendung mit Ihrem Azure Active Directory-Mandanten (AAD) registrieren. Dazu sind zwei Schritte erforderlich. Zuerst müssen Sie App Service registrieren und Berechtigungen verfügbar machen. Anschließend müssen Sie Ihre iOS-App registrieren und ihr Zugriff auf diese Berechtigungen gewähren.

Für dieses Lernprogramm ist Folgendes erforderlich:

* XCode 4.5 und iOS 6.0 (oder höhere Versionen)
* Abschluss des Lernprogramms [Erste Schritte mit mobilen Apps].
* Microsoft Azure Mobile Services SDK
* Die [Active Directory-Authentifizierungsbibliothek für iOS]

##<a name="review"></a>Überprüfen der Konfiguration der Serverprojekts (optional)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-auth](../../includes/app-service-mobile-dotnet-backend-enable-auth.md)]

## <a name="register-application"></a>Registrieren Ihrer Anwendung mit Azure Active Directory

[AZURE.INCLUDE [app-service-mobile-adal-register-app](../../includes/app-service-mobile-adal-register-app.md)]

## <a name="require-authentication"></a>Konfigurieren der Anwendung zur Verwendung der Authentifizierung

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

## <a name="add-adal"></a>Hinzufügen eines Verweises auf die Active Directory-Authentifizierungsbibliothek

1. Herunterladen der [Active Directory-Authentifizierungsbibliothek für iOS].

2. Wählen Sie in Xcode Navigator Ihr Projekt aus, klicken Sie auf **File**, und wählen Sie **Add Files to...**. Navigieren Sie zum Speicherort der heruntergeladenen Bibliothek, und wählen Sie **ADALiOS.xcodeproj**.

3. Wählen Sie erneut Ihr Projekt, und öffnen Sie die Registerkarte **Build Settings**. Navigieren Sie zum Abschnitt **Linking**, und fügen Sie `-ObjC` zu **Other Linker Flags** hinzu.

4. Wählen Sie die Registerkarte **Build Phases**. Fügen Sie unter **Target Dependencies** den Wert `ADALiOS` hinzu.

5. Fügen Sie unter **Link Binary With Libraries** den Wert `libADALiOS.a` hinzu.

Jetzt können Sie auf die Active Directory-Authentifizierungsbibliothek in Ihrem Projekt verweisen.

## <a name="add-authentication-code"></a>Hinzufügen des Authentifizierungscodes zur Client-App

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

4. Ersetzen Sie im Code für die oben angegebene `loginAndGetData`-Methode **INSERT-AUTHORITY-HERE** durch den Namen des Mandanten, in dem Sie Ihre Anwendung bereitgestellt haben. Das Format sollte https://login.windows.net/tenant-name.onmicrosoft.com sein. Sie können diesen Wert in der Registerkarte Domäne in Ihrem Azure Active Directory im [Azure-Verwaltungsportal] kopieren.

5. Ersetzen Sie im oben angegebenen Code für die `loginAndGetData`-Methode **INSERT-RESOURCE-URI-HERE** durch den **App-ID-URI** für Ihre mobile App. Wenn Sie das Thema [Konfigurieren Ihrer mobilen App mit Azure Active Directory] befolgt haben, sollte der URI Ihrer App-ID in etwa so aussehen: https://contosogateway.azurewebsites.net/login/aad.

6. Ersetzen Sie im Code für die oben angegebene `loginAndGetData`-Methode **INSERT-CLIENT-ID-HERE** durch die Client-ID, die Sie aus der systemeigenen Clientanwendung kopiert haben.

7. Ersetzen Sie im oben angegebenen Code für die `loginAndGetData`-Methode **INSERT-REDIRECT-URI-HERE** durch den /login/done-Endpunkt für Ihr App Service-Gateway. Dieser sollte https://contosogateway.azurewebsites.net/login/done ähneln.

8. Ändern Sie im QSTodoListViewController `viewDidLoad`, indem Sie `[self refresh]` durch Folgendes ersetzen:

        [self loginAndGetData];

## <a name="test-client"></a>Testen des Clients mithilfe der Authentifizierung

1. Klicken Sie im Menü "Produkt" auf "Run", um die App zu starten.
2. Sie werden aufgefordert, sich bei Ihrem Azure Active Directory anzumelden.  
3. Die App wird authentifiziert sich und gibt die TodoItem-Elemente zurück.

<!-- URLs. -->
[Konfigurieren Ihrer mobilen App mit Azure Active Directory]: app-service-mobile-how-to-configure-active-directory-authentication.md
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Active Directory-Authentifizierungsbibliothek für iOS]: https://github.com/MSOpenTech/azure-activedirectory-library-for-ios
 [Erste Schritte mit mobilen Apps]: app-service-mobile-ios-get-started.md
 [Hinzufügen von Authentifizierung zur App]: app-service-mobile-ios-get-started-users.md

<!---HONumber=Nov15_HO1-->