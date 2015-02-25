<properties pageTitle="Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden (Windows Store) | Mobile Developer Center" description="Erfahren Sie, wie Sie Benutzer für das für einmalige Anmelden mit ADAL in Ihrer Windows Store-Anwendung authentifizieren." documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor="" services=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="10/14/2014" ms.author="wesmc"/>

# Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden

[AZURE.INCLUDE [mobile-services-selector-adal-sso](../includes/mobile-services-selector-adal-sso.md)]

In diesem Lernprogramm fügen Sie dem Schnellstartprojekt mithilfe der Active Directory-Authentifizierungsbibliothek Authentifizierung hinzu, um [vom Client angewiesene Anmeldevorgänge](http://msdn.microsoft.com/de-de/library/azure/jj710106.aspx) mit Azure Active Directory zu unterstützen. Um [vom Dienst angewiesene Anmeldevorgänge](http://msdn.microsoft.com/de-de/library/azure/dn283952.aspx) mit Azure Active Directory zu unterstützen, beginnen Sie mit dem Lernprogramm [Hinzufügen von Authentifizierung zur Mobile Services-App](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/).

Um Benutzer authentifizieren zu können, müssen Sie Ihre Anwendung in Azure Active Directory (AAD) registrieren. Dazu sind zwei Schritte erforderlich. Zunächst müssen Sie Ihren mobilen Dienst registrieren und entsprechende Berechtigungen vergeben. Anschließend müssen Sie Ihre Windows Store-App registrieren und der App Zugriff auf diese Berechtigungen gewähren


>[AZURE.NOTE] In diesem Lernprogramm erfahren Sie, wie Sie die Authentifizierung per einmaliger Anmeldung in Azure Active Directory für Windows Store-Apps mithilfe eines [vom Client angewiesenen Anmeldevorgangs](http://msdn.microsoft.com/de-de/library/azure/jj710106.aspx) durchführen. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Registrieren Ihres mobilen Diensts bei Azure Active Directory]
2. [Registrieren Ihrer App bei Azure Active Directory] 
3. [Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung]
4. [Hinzufügen des Authentifizierungscodes zur Client-App]
5. [Testen des Clients mit Authentifizierung]

Für dieses Lernprogramm ist Folgendes erforderlich:

* Visual Studio 2013 für Windows 8.1
* Abschluss des Lernprogramms [Erste Schritte mit Mobile Services] oder [Erste Schritte mit Daten]
* NuGet-Paket: Azure Mobile Services SDK
* NuGet-Paket: Active Directory-Bibliothek für Authentifizierung 

[AZURE.INCLUDE [mobile-services-dotnet-adal-register-service](../includes/mobile-services-dotnet-adal-register-service.md)]

## <a name="register-app-aad"></a>Registrieren Ihrer App bei Azure Active Directory

Um Ihre App bei Azure Active Directory zu registrieren, müssen Sie die App im Windows Store zuweisen und benötigen eine Paket-Sicherheits-ID (SID) für die App. Die Paket-SID wird mit den systemeigenen Anwendungseinstellungen in Azure Active Directory registriert.


### Zuordnen der App mit einem neuen Store-App-Namen

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Client-App-Projekt, und klicken Sie auf **Store** und **App mit Store verknüpfen**.

    ![][1]

2. Melden Sie sich mit Ihrem Dev Center-Konto an.

3. Geben Sie den App-Namen ein, den sie für die App reservieren möchten, und klicken Sie auf **Reservieren**.

    ![][2]

4. Wählen Sie den neuen App-Namen aus, und klicken Sie auf **Weiter**.

5. Klicken Sie auf **Verknüpfen**, um die App mit dem Store-Namen zu verknüpfen.


### Rufen Sie die Paket-SID für Ihre App ab.

Nun müssen Sie die Paket-SID abrufen, die in den systemeigenen App-Einstellungen konfiguriert ist.

1. Melden Sie sich bei Ihrem [Windows Dev Center-Dashboard] an, und klicken Sie auf **Bearbeiten** für die App.

    ![][3]

2. Klicken Sie anschließend auf **Dienste**.

    ![][4]

3. Klicken Sie dann auf **Live-Dienste-Website**. 

    ![][5]

4. Kopieren Sie Ihre Paket-SID im oberen Bereich der Seite.

    ![][6]

### Erstellen der systemeigenen App-Registrierung

1. Navigieren Sie im [Azure-Verwaltungsportal] zu **Active Directory**, und klicken Sie dann auf Ihr Verzeichnis.

    ![][7] 

2. Klicken Sie oben auf die Registerkarte **Anwendungen**, und klicken Sie dann auf **HINZUFÜGEN**, um eine App hinzuzufügen. 

    ![][8]

3. Klicken Sie auf **Eine von meinem Unternehmen entwickelte Anwendung hinzufügen**.

4. Geben Sie im Assistenten zum Hinzufügen von Anwendungen einen **Namen** für Ihre Anwendung ein, und klicken Sie auf den Typ **Systemeigene Clientanwendung**. Klicken Sie dann auf Continue.

    ![][9]

5. Fügen Sie die zuvor kopierte Paket-SID der App in das Feld **Weiterleitungs-URI** ein, und schließen Sie die Registrierung der systemeigenen App ab.

    ![][10]

6. Klicken Sie auf die Registerkarte **Konfigurieren** für die systemeigene Anwendung, und kopieren Sie die **Client-ID**. Sie benötigen sie später.

    ![][11]

7. Blättern Sie nach unten zum Abschnitt **Berechtigungen für andere Anwendungen**, und gewähren Sie Vollzugriff auf die zuvor registrierte Mobile Services-Anwendung. Klicken Sie dann auf **Speichern**.

    ![][12]

Der mobile Dienst ist jetzt in AAD so konfiguriert, dass er SSO-Anmeldungen von Ihrer Anwendung empfangen kann.



## <a name="require-authentication"></a>Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

## <a name="add-authentication-code"></a>Hinzufügen des Authentifizierungscodes zur Client-App

1. Öffnen Sie Ihr Windows Store-Client-App-Projekt in Visual Studio.

[AZURE.INCLUDE [mobile-services-dotnet-adal-install-nuget](../includes/mobile-services-dotnet-adal-install-nuget.md)]

4. Öffnen Sie im Projektmappen-Explorer in Visual Studio die Datei MainPage.xaml.cs und fügen Sie die folgenden using-Anweisungen hinzu.

        using Windows.UI.Popups;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Newtonsoft.Json.Linq;


5. Fügen Sie den folgenden Code zur MainPage-Klasse hinzu, um die `AuthenticateAsync` -Methode zu deklarieren.

        private MobileServiceUser user; 
        private async Task AuthenticateAsync()
        {
            string authority = "<INSERT-AUTHORITY-HERE>";
            string resourceURI = "<INSERT-RESOURCE-URI-HERE>";
            string clientID = "<INSERT-CLIENT-ID-HERE>"; 
            while (user == null)
            {
                string message;
                try
                {
                  AuthenticationContext ac = new AuthenticationContext(authority);
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceURI, clientID, (Uri) null);
                  JObject payload = new JObject();
                  payload["access_token"] = ar.AccessToken;
                  user = await App.MobileService.LoginAsync(MobileServiceAuthenticationProvider.WindowsAzureActiveDirectory, payload);
                  message = string.Format("You are now logged in - {0}", user.UserId);
                }
                catch (InvalidOperationException)
                {
                  message = "You must log in. Login Required";
                } 
                var dialog = new MessageDialog(message);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
            } 
        }

6. Ersetzen Sie im oben angegebenen Code für die `AuthenticateAsync`-Methode **INSERT-AUTHORITY-HERE** durch den Namen des Mandanten, in dem Sie Ihre Anwendung bereitgestellt haben. Verwenden Sie dabei das Format https://login.windows.net/tenant-name.onmicrosoft.com. Sie können diesen Wert in der Registerkarte "Domäne" in Ihrem Azure Active Directory im [Azure-Verwaltungsportal] kopieren.

7. Ersetzen Sie im oben angegebenen Code für die `AuthenticateAsync`-Methode**INSERT-RESOURCE-URI-HERE** durch den **App-ID-URI** für Ihren mobilen Dienst. Wenn Sie die Schritte im Thema [So registrieren Sie sich beim Azure Active Directory] ausgeführt haben, sollte der App-ID-URI ungefähr wie folgt aussehen: https://todolist.azure-mobile.net/login/aad.

8. Ersetzen Sie im oben angegebenen Code für die `AuthenticateAsync`-Methode **INSERT-CLIENT-ID-HERE** durch die Client-ID, die Sie aus der systemeigenen Clientanwendung kopiert haben.

9. Öffnen Sie im Projektmappen-Explorer von Visual Studio die Datei Package.appxmanifest im Clientprojekt. Klicken Sie auf die Registerkarte **Funktionen**, und aktivieren Sie **Unternehmensanwendung** und **Private Netzwerke (Client und Server)**. Speichern Sie die Datei.

    ![][14]

10. Bearbeiten Sie in der Datei "MainPage.cs" den `OnNavigatedTo`-Ereignishandler so, dass die `AuthenticateAsync`-Methode wie folgt aufgerufen wird.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await AuthenticateAsync();
            await RefreshTodoItems();
        }


## <a name="test-client"></a>Testen des Clients mit Authentifizierung

1. Führen Sie die Client-App in Visual Studio aus.
2. Sie werden aufgefordert, sich bei Ihrem Azure Active Directory anzumelden.  
3. Die App authentifiziert sich und gibt die TodoItems zurück.

    ![][15]



<!-- Anchors. -->
[Registrieren Ihres mobilen Diensts bei Azure Active Directory]: #register-mobile-service-aad
[Registrieren Ihrer App bei Azure Active Directory]: #register-app-aad
[Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung]: #require-authentication
[Mobiler JavaScript-Back-End-Dienst]: #javascript-authentication
[Mobiler .NET-Back-End-Dienst]: #dotnet-authentication
[Hinzufügen des Authentifizierungscodes zur Client-App]: #add-authentication-code
[Testen des Clients mit Authentifizierung]: #test-client

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-app-manage-manifest.png
[1]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-associate-app.png
[2]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-reserve-store-appname.png
[3]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-edit.png
[4]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-services.png
[5]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-live-services-site.png
[6]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-package-sid.png
[7]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-select-aad.png
[8]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-applications-tab.png
[9]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-selection.png
[10]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-sid-redirect-uri.png
[11]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-client-id.png
[12]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-add-permissions.png
[14]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-package-appxmanifest.png
[15]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-app-run.png

<!-- URLs. -->
[So registrieren Sie sich beim Azure Active Directory]: /de-de/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Windows Dev Center-Dashboard]: http://go.microsoft.com/fwlink/p/?LinkID=266734

<!--HONumber=42-->
