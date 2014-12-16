<properties urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden (Xamarin.iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your Xamarin.iOS application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc,mahender" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/29/2014" ms.author="wesmc,mahender" />

# Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden

[WACOM.INCLUDE [mobile-services-selector-adal-sso](../includes/mobile-services-selector-adal-sso.md)]

In diesem Lernprogramm fügen Sie die Authentifizierung mithilfe der Active Directory-Authentifizierungsbibliothek zum Schnellstartprojekt hinzu. 

Um Benutzer authentifizieren zu können, müssen Sie Ihre Anwendung in Azure Active Directory (AAD) registrieren. Dazu sind zwei Schritte erforderlich. Zunächst müssen Sie Ihren mobilen Dienst registrieren und entsprechende Berechtigungen vergeben. Anschließend müssen Sie Ihre Xamarin.iOS-App registrieren und der App Zugriff auf diese Berechtigungen gewähren


>[WACOM.NOTE] In diesem Lernprogramm erfahren Sie, wie Sie die Authentifizierung per einmaliger Anmeldung in Azure Active Directory für Xamarin.iOS-Apps umsetzen können. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Registrieren Ihres mobilen Diensts beim Azure Active Directory]
2. [Registrieren Ihre App beim Azure Active Directory] 
3. [Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung]
4. [Hinzufügen des Authentifizierungscodes zur Client-App]
5. [Testen des Clients mit Authentifizierung]

Für dieses Lernprogramm ist Folgendes erforderlich:

* XCode 4.5 und iOS 6.0 (oder neuere Versionen) 
* Visual Studio mit der [Xamarin-Erweiterung] oder [Xamarin Studio] auf OS X
* Abschluss des Lernprogramms [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten].
* Microsoft Azure Mobile Services SDK
* Eine [Xamarin-Bindung für die Active Directory-Authentifizierungsbibliothek für iOS].

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-client](../includes/mobile-services-dotnet-adal-register-client.md)]

## <a name="require-authentication"></a>Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>Hinzufügen des Authentifizierungscodes zur Client-App

1. Fügen Sie Ihrem Xamarin.iOS-Projekt die Xamarin-Bindung für die Active Directory-Authentifizierungsbibliothek hinzu. Klicken Sie in Visual Studio 2013 mit der rechten Maustaste auf **Verweis** und wählen Sie **Verweis hinzufügen**. Durchsuchen Sie dann Ihre Bindungsbibliothek und klicken Sie auf **Hinzufügen**. Achten Sie darauf, auch die Storyboards der ADAL-Quelle hinzuzufügen.

2. Fügen Sie der QSTodoService-Klasse folgenden Code hinzu: 

        private MobileServiceUser user;
        public MobileServiceUser User { get { return user; } }

        public async Task Authenticate()
        {
            var aadAuthority = @"<INSERT-AUTHORITY-HERE>";
            var aadResource = @"<INSERT-RESOURCE-URI-HERE>";
            var aadClientId = @"<INSERT-CLIENT-ID-HERE>";
            var aadRedirect = @"<INSERT-REDIRECT-URI-HERE>";

            ADAuthenticationError error;
            var aadContext = ADAuthenticationContext.AuthenticationContextWithAuthority(aadAuthority, out error);
            if (error != null)
            {
                throw new InvalidOperationException("Error creating the AAD context: " + error.ErrorDetails);
            }

            var tcs = new TaskCompletionSource<string>();
            aadContext.AcquireTokenWithResource(aadResource, aadClientId, new NSUrl(aadRedirect), result =>
                {
                    if (result.Status == ADAuthenticationResultStatus.SUCCEEDED)
                    {
                        tcs.SetResult(result.AccessToken);
                    }
                    else
                    {
                        string errorDetails;
                        if (result.Status == ADAuthenticationResultStatus.USER_CANCELLED)
                        {
                            errorDetails = "Authentication cancelled by user";
                        }
                        else
                        {
                            errorDetails = result.Error.ErrorDetails;
                        }

                        tcs.SetException(new InvalidOperationException("Error during authentication: " + errorDetails));
                    }
                });
            string accessToken = await tcs.Task;

            var token = new JObject(new JProperty("access_token", accessToken));

            try
            {
                user = await this.client.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, token);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

6. Ersetzen Sie im Code der obigen obigen Methode`AuthenticateAsync`-Methode den Text **INSERT-AUTHORITY-HERE** durch den Namen des Mandanten, in dem Sie die Anwendung bereitstellen. Der Name sollte folgendes Format haben: https://login.windows.net/tenant-name.onmicrosoft.com. Sie können diesen Wert von der Registerkarte "Domäne" in Azure Active Directory im[Azure-Verwaltungsportal] kopieren.

7. Ersetzen Sie im Code für die oben angegebene `AuthenticateAsync`-Methode den Text **INSERT-RESOURCE-URI-HERE** durch die **App-ID-URI** des mobilen Diensts. Wenn Sie das Thema [Registrieren in Azure Active Directory] verfolgt haben, sollte Ihre URI der App-ID in etwa so aussehen: https://todolist.azure-mobile.net/login/aad.

8. Ersetzen Sie im Code für die oben angegebene `AuthenticateAsync`-Methode den Text **INSERT-CLIENT-ID-HERE** durch die Client-ID, die Sie aus der systemeigenen Clientanwendung kopiert haben.

9. Ersetzen Sie im Code in der obigen Methode `AuthenticateAsync` den Text **INSERT-REDIRECT-URI-HERE** mit dem /login/done Endpunkt für Ihren mobilen Dienst. Das ist vergleichbar mit https://todolist.azure-mobile.net/login/done.


3. Ändern Sie im QSTodoListViewController **ViewDidLoad**, indem Sie folgenden Code direkt vor dem Aufruf von RefreshAsync() hinzufügen;

        if (QSTodoService.DefaultService.User == null)
        {
            await QSTodoService.DefaultService.Authenticate();
        }

## <a name="test-client"></a>Testen des Clients mit Authentifizierung

1. Klicken Sie im Menü "Run" auf "Run", um die App zu starten. 
2. Sie werden aufgefordert, sich bei Ihrem Azure Active Directory anzumelden.  
3. Die App authentifiziert sich und gibt die TodoItems zurück.

   ![](./media/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication/mobile-services-app-run.png)

<!-- Anchors. -->
[Registrieren Ihres mobilen Diensts beim Azure Active Directory]: #register-mobile-service-aad
[Registrieren Ihre App beim Azure Active Directory]: #register-app-aad
[Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung]: #require-authentication
[Hinzufügen des Authentifizierungscodes zur Client-App]: #add-authentication-code
[Testen des Clients mit Authentifizierung]: #test-client

<!-- URLs. -->
[Erste Schritte mit Daten]: /de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-data/
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
[So registrieren Sie sich beim Azure Active Directory]: /de-de/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Xamarin-Bindung für die Active Directory-Authentifizierungsbibliothek für iOS]: https://github.com/AzureADSamples/NativeClient-Xamarin-iOS
[Xamarin-Erweiterung]: http://xamarin.com/visual-studio
[Xamarin Studio]: http://xamarin.com/download
