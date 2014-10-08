<properties linkid="develop-mobile-tutorials-sso-with-adal" urlDisplayName="Active Directory SSO Authentication with ADAL" pageTitle="Authenticate your app with Active Directory Authentication Library Single Sign-On (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to authentication users for single sign-on with ADAL in your Windows Store application." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Authenticate your app with Active Directory Authentication Library Single Sign-On" authors="wesmc" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="wesmc"></tags>

# Authentifizieren Ihrer App mit der Active Directory-Bibliothek für einmaliges Anmelden

<div class="dev-center-tutorial-selector sublanding">
<a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication" title="Windows Store C#" class="current">Windows Store C#</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication" title="iOS" >iOS</a>
<a href="/en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication" title="Xamarin.iOS">Xamarin.iOS</a>
</div>

In diesem Lernprogramm fügen Sie die Authentifizierung mithilfe der Active Directory-Authentifizierungsbibliothek zum Schnellstartprojekt hinzu.

Um Benutzer authentifizieren zu können, müssen Sie Ihre Anwendung in Azure Active Directory (AAD) registrieren. Dazu sind zwei Schritte erforderlich. Zunächst müssen Sie Ihren mobilen Dienst registrieren und entsprechende Berechtigungen vergeben. Anschließend müssen Sie Ihre Windows Store-App registrieren und der App Zugriff auf diese Berechtigungen gewähren

> [WACOM.NOTE] In diesem Lernprogramm erfahren Sie, wie Sie die Authentifizierung per einmaliger Anmeldung in Azure Active Directory für Windows Store-Apps umsetzen können. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie zunächst das Lernprogramm [Erste Schritte mit Mobile Services][] abschließen.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Registrieren Ihres mobilen Diensts beim Azure Active Directory][]
2.  [Registrieren Ihre App beim Azure Active Directory][]
3.  [Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung][]
4.  [Hinzufügen des Authentifizierungscodes zur Client-App][]
5.  [Testen des Clients mit Authentifizierung][]

Für dieses Lernprogramm ist Folgendes erforderlich:

-   Visual Studio 2013 für Windows 8.1
-   Abschluss des Lernprogramms [Erste Schritte mit Mobile Services][] oder [Erste Schritte mit Daten][].
-   NuGet-Paket: Azure Mobile Services SDK
-   NuGet-Paket: Active Directory-Bibliothek für Authentifizierung

[WACOM.INCLUDE [mobile-services-dotnet-adal-register-service][]]

## <a name="register-app-aad"></a>Registrieren Ihre App beim Azure Active Directory

Um Ihre App bei Azure Active Directory zu registrieren, müssen Sie die App im Windows Store zuweisen und benötigen eine Paket-Sicherheits-ID (SID) für die App. Die Paket-SID wird mit den systemeigenen Anwendungseinstellungen in Azure Active Directory registriert.

### Zuordnen der App mit einem neuen Store-App-Namen

1.  Klicken Sie in Visual Studio mit der rechten Maustaste auf das Client-App-Projekt und klicken Sie auf **Store** und **App dem Store zuordnen**

    ![][]

2.  Melden Sie sich mit Ihrem Dev Center-Konto an.

3.  Geben Sie den Namen ein, den sie für die App reservieren möchten, und klicken Sie auf **Reservieren**.

    ![][1]

4.  Wählen Sie den neuen App-Namen aus und klicken Sie auf **Weiter**.

5.  Klicken Sie auf **Zuordnen**, um die App zum Store-Namen zuzuordnen.

### Rufen Sie die Paket-SID für Ihre App ab.

Nun müssen Sie die Paket-SID abrufen, die in den systemeigenen App-Einstellungen konfiguriert ist.

1.  Melden Sie sich bei Ihrem [Windows Dev Center-Dashboard][] an und klicken Sie auf **Bearbeiten** für die App.

    ![][2]

2.  Klicken Sie anschließend auf **Dienste**

    ![][3]

3.  Klicken Sie auf **Live-Dienste-Website**.

    ![][4]

4.  Kopieren Sie Ihre Paket-SID im oberen Bereich der Seite.

    ![][5]

### Erstellen der systemeigenen App-Registrierung

1.  Navigieren Sie im [Azure-Verwaltungsportal][] zu **Active Directory**, und klicken Sie dann auf Ihr Verzeichnis.

    ![][6]

2.  Klicken Sie oben auf die Registerkarte **Applications** und anschließend auf **ADD**, um eine App hinzuzufügen.

    ![][7]

3.  Klicken Sie auf **Add an application my organization is developing**.

4.  Geben Sie im Assistenten zum Hinzufügen von Anwendungen einen **Namen** für Ihre Anwendung ein, und klicken Sie auf den Typ **Systemeigene Clientanwendung**. Klicken Sie dann auf Continue.

    ![][8]

5.  Fügen Sie die zuvor kopierte Paket-SID der App in das Feld **Weiterleitungs-URI** ein und schließen Sie die systemeigene App-Registrierung ab.

    ![][9]

6.  Klicken Sie auf die Registerkarte **Konfigurieren** für die systemeigene Anwendung, und kopieren Sie die **Client-ID**. Sie benötigen sie später.

    ![][10]

7.  Blättern Sie nach unten zum Abschnitt **Berechtigungen für andere Anwendungen**, und gewähren Sie Vollzugriff auf die zuvor registrierte Mobile Services-Anwendung. Klicken Sie dann auf **Speichern**

    ![][11]

Der mobile Dienst ist jetzt in AAD so konfiguriert, dass er SSO-Anmeldungen von Ihrer Anwendung empfangen kann.

## <a name="require-authentication"></a>Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend][]]

## <a name="add-authentication-code"></a>Hinzufügen des Authentifizierungscodes zur Client-App

1.  Öffnen Sie Ihr Windows Store-Client-App-Projekt in Visual Studio.

[WACOM.INCLUDE [mobile-services-dotnet-adal-install-nuget][]]

1.  Öffnen Sie im Projektmappen-Explorer in Visual Studio die Datei MainPage.xaml.cs und fügen Sie die folgenden using-Anweisungen hinzu.

        using Windows.UI.Popups;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Newtonsoft.Json.Linq;

2.  Fügen Sie den folgenden Code zur MainPage-Klasse hinzu, um die `AuthenticateAsync`-Methode zu deklarieren.

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
                  AuthenticationResult ar = await ac.AcquireTokenAsync(resourceURI, clientID);
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

3.  Ersetzen Sie im Code für die oben angegebene `AuthenticateAsync`-Methode **INSERT-AUTHORITY-HERE** durch den Namen des Mandanten, in dem Sie Ihre Anwendung bereitgestellt haben. Verwenden Sie dabei das Format <https://login.windows.net/tenant-name.onmicrosoft.com>. Sie können diesen Wert in der Registerkarte Domäne in Ihrem Azure Active Directory im [Azure-Verwaltungsportal][] kopieren.

4.  Ersetzen Sie im Code für die oben angegebene `AuthenticateAsync`-Methode **INSERT-RESOURCE-URI-HERE** durch die **URI der APP-ID** für Ihren mobilen Dienst. Wenn Sie das Thema [Registrieren in Azure Active Directory][] verfolgt haben, sollte Ihre URI der App-ID in etwa so aussehen: <https://todolist.azure-mobile.net/login/aad>.

5.  Ersetzen Sie im Code für die oben angegebene `AuthenticateAsync`-Methode **INSERT-CLIENT-ID-HERE** durch die Client-ID, die Sie aus der systemeigenen Clientanwendung kopiert haben.

6.  Öffnen Sie im Projektmappen-Explorer von Visual Studio die Datei Package.appxmanifest im Clientprojekt. Klicken Sie auf die Registerkarte **Funktionen** und aktivieren Sie **Enterprise-Anwendung** und **Private Netzwerke (Client & Server)**. Speichern Sie die Datei.

    ![][12]

7.  Bearbeiten Sie in der Datei MainPage.cs den `OnNavigatedTo`-Ereignishandler so, dass die `AuthenticateAsync`-Methode wie folgt aufgerufen wird.

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            if (user == null)
                await AuthenticateAsync();

            RefreshTodoItems();
        }

## <a name="test-client"></a>Testen des Clients mit Authentifizierung

1.  Führen Sie die Client-App in Visual Studio aus.
2.  Sie werden aufgefordert, sich bei Ihrem Azure Active Directory anzumelden.
3.  Die App authentifiziert sich und gibt die TodoItems zurück.

    ![][13]

<!-- Anchors. --> <!-- Images --> <!-- URLs. -->

  [Windows Store C\#]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-adal-sso-authentication "Windows Store C#"
  [iOS]: /en-us/documentation/articles/mobile-services-dotnet-backend-ios-adal-sso-authentication "iOS"
  [Xamarin.iOS]: /en-us/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-adal-sso-authentication "Xamarin.iOS"
  [Erste Schritte mit Mobile Services]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Registrieren Ihres mobilen Diensts beim Azure Active Directory]: #register-mobile-service-aad
  [Registrieren Ihre App beim Azure Active Directory]: #register-app-aad
  [Konfigurieren des mobilen Diensts zur Verwendung von Authentifizierung]: #require-authentication
  [Hinzufügen des Authentifizierungscodes zur Client-App]: #add-authentication-code
  [Testen des Clients mit Authentifizierung]: #test-client
  [Erste Schritte mit Daten]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  [mobile-services-dotnet-adal-register-service]: ../includes/mobile-services-dotnet-adal-register-service.md
  []: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-associate-app.png
  [1]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-vs-reserve-store-appname.png
  [Windows Dev Center-Dashboard]: http://go.microsoft.com/fwlink/p/?LinkID=266734
  [2]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-edit.png
  [3]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-services.png
  [4]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-live-services-site.png
  [5]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-store-app-package-sid.png
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [6]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-select-aad.png
  [7]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-aad-applications-tab.png
  [8]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-selection.png
  [9]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-sid-redirect-uri.png
  [10]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-client-id.png
  [11]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-native-add-permissions.png
  [mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
  [mobile-services-dotnet-adal-install-nuget]: ../includes/mobile-services-dotnet-adal-install-nuget.md
  [Registrieren in Azure Active Directory]: /en-us/documentation/articles/mobile-services-how-to-register-active-directory-authentication/
  [12]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-package-appxmanifest.png
  [13]: ./media/mobile-services-windows-store-dotnet-adal-sso-authenticate/mobile-services-app-run.png
