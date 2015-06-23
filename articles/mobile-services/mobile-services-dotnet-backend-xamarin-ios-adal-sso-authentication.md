<properties 
	pageTitle="Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden (Xamarin.iOS) | Mobile Dev Center" 
	description="Erfahren Sie, wie Sie Benutzer für das für einmalige Anmelden mit ADAL in Ihrer Xamarin.iOS-Anwendung authentifizieren." 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/25/2015" 
	ms.author="mahender"/>

# Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../../includes/mobile-services-selector-adal-sso.md)]

##Übersicht

In diesem Lernprogramm fügen Sie die Authentifizierung mithilfe der Active Directory-Authentifizierungsbibliothek zum Schnellstartprojekt hinzu.

Um Benutzer authentifizieren zu können, müssen Sie Ihre Anwendung in Azure Active Directory (AAD) registrieren. Dazu sind zwei Schritte erforderlich. Zunächst müssen Sie Ihren mobilen Dienst registrieren und entsprechende Berechtigungen vergeben. Anschließend müssen Sie Ihre Xamarin.iOS-App registrieren und der App Zugriff auf diese Berechtigungen gewähren


>[AZURE.NOTE]In diesem Lernprogramm erfahren Sie, wie Sie die Authentifizierung per einmaliger Anmeldung in Azure Active Directory für Xamarin.iOS-Apps umsetzen können. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen.

##Voraussetzungen

Für dieses Lernprogramm ist Folgendes erforderlich:

* Xcode 4.5 und iOS 6.0 (oder neuere Versionen) 
* Visual Studio mit der Erweiterung [Xamarin] oder [Xamarin Studio] unter OS X
* Abschluss des Lernprogramms [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten].
* Microsoft Azure Mobile Services SDK
* Eine [Xamarin-Bindung für die Active Directory-Authentifizierungsbibliothek für iOS].

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-service](../../includes/mobile-services-dotnet-adal-register-service.md)]

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-client](../../includes/mobile-services-dotnet-adal-register-client.md)]

##Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

##Hinzufügen des Authentifizierungscodes zur Client-App

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

6. Ersetzen Sie im Code für die oben angegebene `AuthenticateAsync`-Methode **INSERT-AUTHORITY-HERE** durch den Namen des Mandanten, in dem Sie Ihre Anwendung bereitgestellt haben. Das Format sollte https://login.windows.net/tenant-name.onmicrosoft.com sein. Sie können diesen Wert in der Registerkarte Domäne in Ihrem Azure Active Directory im [Azure-Verwaltungsportal] kopieren.

7. Ersetzen Sie im Code für die oben angegebene `AuthenticateAsync`-Methode **INSERT-RESOURCE-URI-HERE** durch die **URI der APP-ID** für Ihren mobilen Dienst. Wenn Sie das Thema [Registrieren in Azure Active Directory] befolgt haben, sollte der URI Ihrer App-ID in etwa so aussehen: https://todolist.azure-mobile.net/login/aad.

8. Ersetzen Sie im Code für die oben angegebene `AuthenticateAsync`-Methode **INSERT-CLIENT-ID-HERE** durch die Client-ID, die Sie aus der systemeigenen Clientanwendung kopiert haben.

9. Ersetzen Sie im Code in der obigen `AuthenticateAsync`-Methode den Text **INSERT-REDIRECT-URI-HERE** durch den/login/done-Endpunkt für den mobilen Dienst. Das ist vergleichbar mit https://todolist.azure-mobile.net/login/done.


3. Ändern Sie im QSTodoListViewController **ViewDidLoad**, indem Sie folgenden Code direkt vor dem Aufruf von RefreshAsync() hinzufügen;

        if (QSTodoService.DefaultService.User == null)
        {
            await QSTodoService.DefaultService.Authenticate();
        }

##Testen des Clients mit Authentifizierung

1. Klicken Sie im Menü „Run” auf „Run”, um die App zu starten. 
2. Sie werden aufgefordert, sich bei Ihrem Azure Active Directory anzumelden.  
3. Die App authentifiziert sich und gibt die TodoItems zurück.

   ![](./media/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication/mobile-services-app-run.png)



<!-- URLs. -->
[Erste Schritte mit Daten]: mobile-services-ios-get-started-data.md
[Erste Schritte mit Mobile Services]: mobile-services-dotnet-backend-xamarin-ios-get-started.md
[Registrieren in Azure Active Directory]: mobile-services-how-to-register-active-directory-authentication.md
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Xamarin-Bindung für die Active Directory-Authentifizierungsbibliothek für iOS]: https://github.com/AzureADSamples/NativeClient-Xamarin-iOS
[Xamarin]: http://xamarin.com/visual-studio
[Xamarin Studio]: http://xamarin.com/download
<!--HONumber=54--> 