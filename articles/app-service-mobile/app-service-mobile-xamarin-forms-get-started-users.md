<properties 
	pageTitle="Erste Schritte mit der Authentifizierung für mobile Apps in der Xamarin.Forms-App" 
	description="Erfahren Sie, wie Sie Mobile Apps zum Authentifizieren von Benutzern Ihrer Xamarin.Forms-App über eine Vielzahl von Identitätsanbietern nutzen können, darunter AAD, Google, Facebook, Twitter und Microsoft." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="wesmc7777"
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin" 
	ms.devlang="dotnet" 
	ms.topic="article"
	ms.date="11/30/2015" 
	ms.author="wesmc"/>

# Hinzufügen der Authentifizierung zu Ihrer Xamarin.Forms-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

##Übersicht

In diesem Thema wird die Authentifizierung von Benutzern einer mobilen App Service-App über Ihre Clientanwendung veranschaulicht. In diesem Tutorial fügen Sie dem Xamarin.Forms-Schnellstartprojekt durch Verwenden eines von App Service unterstützten Identitätsanbieters eine Authentifizierungsfunktion hinzu. Nach der erfolgreichen Authentifizierung und Autorisierung durch Ihre mobile App wird die Benutzer-ID angezeigt, und Sie erhalten Zugriff auf beschränkte Tabellendaten.

Sie müssen zunächst das [Xamarin.Forms-Schnellstart-Tutorial](app-service-mobile-xamarin-forms-get-started.md) absolvieren. Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt das Authentifizierungs-Erweiterungspaket hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) (in englischer Sprache).


##Registrieren Ihrer App für die Authentifizierung und Konfigurieren von App Services

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]


##Hinzufügen der Authentifizierung zur portablen Klassenbibliothek 

Mobile Apps verwendet eine plattformspezifische `MobileServiceClient.LoginAsync`-Methode zum Anzeigen der Anmeldeschnittstelle und der Cachedaten. Zur Authentifizierung mit einem Xamarin.Forms-Projekt definieren Sie eine `IAuthenticate`-Schnittstelle in der portablen Klassenbibliothek. Diese Schnittstelle kann auf jeder Plattform, die unterstützt werden soll, im plattformspezifischen Projekt implementiert werden. Sie fügen Code hinzu, sodass die Authentifizierung vor Aufrufen der eingeschränkten Tabelle über die portable Klassenbibliothek erfolgt.

1. Öffnen Sie die Datei „App.cs“ in Visual Studio oder Xamarin Studio über das **portable**-Projekt. Fügen Sie der Datei die folgende `using`-Anweisung hinzu.

		using System.Threading.Tasks;

2. Fügen Sie in „App.cs“ die folgende `IAuthenticate`-Schnittstellendefinition unmittelbar vor der `App`-Klassendefinition hinzu.

	    public interface IAuthenticate
	    {
	        Task<bool> Authenticate();
	    };

3. Fügen Sie in „App.cs“ die folgenden statischen Member hinzu, um die Schnittstelle mit einer plattformspezifischen Implementierung zu initialisieren.

		public class App : Application
		{
	
	        public static IAuthenticate Authenticator { get; private set; }
	
	        public static void Init(IAuthenticate authenticator)
	        {
	            Authenticator = authenticator;
	        }
	
			...

4. Öffnen Sie „TodoList.xaml.cs“ über das **portable**-Projekt, und aktualisieren Sie die `OnAppearing`-Methode so, dass die Authentifizierung vor der Aktualisierung der Elemente aus der Tabelle erfolgt.


        protected override async void OnAppearing()
        {
            base.OnAppearing();

            if (App.Authenticator != null)
                await App.Authenticator.Authenticate();

            // Set syncItems to true in order to synchronize the data on startup when running in offline mode
            await RefreshItems(true, syncItems: false);
        }


5. Speichern Sie die Änderungen, und erstellen Sie das Portalprojekt zum Überprüfen auf Fehler.


##Hinzufügen der Authentifizierung zur Android-App

In diesem Abschnitt fügen Sie die Authentifizierung für das droid-Projekt hinzu. Wenn Sie nicht mit Android-Geräten arbeiten, können Sie diesen Abschnitt überspringen.

1. Klicken Sie in Visual Studio oder Xamarin Studio mit der rechten Maustaste auf das **droid**-Projekt, und klicken Sie dann auf **Als Startprojekt festlegen**.

2. Führen Sie dann das Projekt im Debugger aus, um sicherzustellen, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde. Dies erfolgt, da Sie den Zugriff auf das Back-End nur auf autorisierte Benutzer beschränkt haben.

3. Öffnen Sie als Nächstes die Datei „MainActivity.cs“ im droid-Projekt, und fügen Sie die folgende `using`-Anweisung hinzu.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Aktualisieren Sie die `MainActivity`-Klasse mit der Implementierung der `IAuthenticate`-Schnittstelle.

		public class MainActivity : global::Xamarin.Forms.Platform.Android.FormsApplicationActivity, IAuthenticate


5. Aktualisieren Sie die `MainActivity`-Klasse, indem Sie ein `MobileServiceUser`-Feld und die unten dargestellte `Authenticate`-Methode zur Unterstützung der `IAuthenticate`-Schnittstelle hinzufügen.
 
	Wenn Sie einen anderen `MobileServiceAuthenticationProvider` als Facebook verwenden möchten, nehmen Sie auch diese Änderung vor.

		// Define a authenticated user.
		private MobileServiceUser user;
	
        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(this,
                    MobileServiceAuthenticationProvider.Facebook);
                CreateAndShowDialog(string.Format("you are now logged in - {0}",
                    user.UserId), "Logged in!");

                success = true;
            }
            catch (Exception ex)
            {
                CreateAndShowDialog(ex.Message, "Authentication failed");
            }
            return success;
        }

6. Aktualisieren Sie die `OnCreate`-Methode der `MainActivity`-Klasse so, dass der Authentifikator vor dem Laden der App initialisiert wird.

        App.Init((IAuthenticate)this);

        LoadApplication(new App());



7. Erstellen Sie die App neu, und führen Sie sie aus. Melden Sie sich über den ausgewählten Authentifizierungsanbieter an, und stellen Sie sicher, dass Sie als authentifizierter Benutzer auf die Tabelle zugreifen können.



##Hinzufügen der Authentifizierung zur iOS-App

In diesem Abschnitt fügen Sie die Authentifizierung für das iOS-Projekt hinzu. Wenn Sie nicht mit iOS-Geräten arbeiten, können Sie diesen Abschnitt überspringen.

1. Klicken Sie in Visual Studio oder Xamarin Studio mit der rechten Maustaste auf das **iOS**-Projekt, und klicken Sie dann auf **Als Startprojekt festlegen**.

2. Führen Sie dann das Projekt im Debugger aus, um sicherzustellen, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde. Dies erfolgt, da Sie den Zugriff auf das Back-End nur auf autorisierte Benutzer beschränkt haben.

3. Öffnen Sie als Nächstes die Datei „AppDelegate.cs“ im iOS-Projekt, und fügen Sie die folgende `using`-Anweisung hinzu.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;

4. Aktualisieren Sie die `AppDelegate`-Klasse mit der Implementierung der `IAuthenticate`-Schnittstelle.

		public partial class AppDelegate : global::Xamarin.Forms.Platform.iOS.FormsApplicationDelegate, IAuthenticate


5. Aktualisieren Sie die `AppDelegate`-Klasse, indem Sie ein `MobileServiceUser`-Feld und die unten dargestellte `Authenticate`-Methode zur Unterstützung der `IAuthenticate`-Schnittstelle hinzufügen.
 
	Wenn Sie einen anderen `MobileServiceAuthenticationProvider` als Facebook verwenden möchten, nehmen Sie auch diese Änderung vor.

		// Define a authenticated user.
		private MobileServiceUser user;
	
        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(UIApplication.SharedApplication.KeyWindow.RootViewController,
                        MobileServiceAuthenticationProvider.Facebook);
                }

                success = true;
            }
            catch (Exception ex)
            {
                UIAlertView avAlert = new UIAlertView("Authentication failed", ex.Message, null, "OK", null);
                avAlert.Show();
            }
            return success;
        }

6. Aktualisieren Sie die `FinishedLaunching`-Methode der `AppDelegate`-Klasse so, dass der Authentifikator vor dem Laden der App initialisiert wird.

        App.Init((IAuthenticate)this);

		LoadApplication (new App ());



7. Erstellen Sie die App neu, und führen Sie sie aus. Melden Sie sich über den ausgewählten Authentifizierungsanbieter an, und stellen Sie sicher, dass Sie als authentifizierter Benutzer auf die Tabelle zugreifen können.




##Hinzufügen der Authentifizierung zur Windows-App

In diesem Abschnitt fügen Sie die Authentifizierung für das WinApp-Projekt hinzu. Wenn Sie nicht mit Windows-Geräten arbeiten, können Sie diesen Abschnitt überspringen.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das **WinApp**-Projekt, und klicken Sie auf **Als Startprojekt festlegen**.

2. Führen Sie dann das Projekt im Debugger aus, um sicherzustellen, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde. Dies erfolgt, da Sie den Zugriff auf das Back-End nur auf autorisierte Benutzer beschränkt haben.

3. Öffnen Sie als Nächstes die Datei „MainPage.xaml.cs“ im WinApp-Projekt, und fügen Sie die folgende `using`-Anweisung hinzu. Ersetzen Sie <*Your portable class library namespace*> durch den Namespace für Ihre portable Klassenbibliothek.

		using Microsoft.WindowsAzure.MobileServices;
		using System.Threading.Tasks;
		using <Your portable class library namespace>;

4. Aktualisieren Sie die `MainPage`-Klasse mit der Implementierung der `IAuthenticate`-Schnittstelle.

	    public sealed partial class MainPage : IAuthenticate


5. Aktualisieren Sie die `MainPage`-Klasse, indem Sie ein `MobileServiceUser`-Feld und die unten dargestellte `Authenticate`-Methode zur Unterstützung der `IAuthenticate`-Schnittstelle hinzufügen.
 
	Wenn Sie einen anderen `MobileServiceAuthenticationProvider` als Facebook verwenden möchten, nehmen Sie auch diese Änderung vor.

        // Define a authenticated user.
        private MobileServiceUser user;

        public async Task<bool> Authenticate()
        {
            var success = false;
            try
            {
                // Sign in with Facebook login using a server-managed flow.
                if (user == null)
                {
                    user = await TodoItemManager.DefaultManager.CurrentClient.LoginAsync(MobileServiceAuthenticationProvider.Facebook);
                    var messageDialog = new Windows.UI.Popups.MessageDialog(
							string.Format("you are now logged in - {0}", user.UserId), "Authentication");
                    messageDialog.ShowAsync();
                }

                success = true;
            }
            catch (Exception ex)
            {
                var messageDialog = new Windows.UI.Popups.MessageDialog(ex.Message, "Authentication Failed");
                messageDialog.ShowAsync();
            }
            return success;
        }

6. Aktualisieren Sie den Konstruktor für die `MainPage`-Klasse so, dass der Authentifikator vor dem Laden der App initialisiert wird. Ersetzen Sie <*Your portable class library namespace*> durch den Namespace für Ihre portable Klassenbibliothek.

        public MainPage()
        {
            this.InitializeComponent();

            <Your portable class library namespace>.App.Init((IAuthenticate)this);
            
            LoadApplication(new WesmcMobileAppGaTest.App());
        }



7. Erstellen Sie die App neu, und führen Sie sie aus. Melden Sie sich über den ausgewählten Authentifizierungsanbieter an, und stellen Sie sicher, dass Sie als authentifizierter Benutzer auf die Tabelle zugreifen können.




<!-- Images. -->

<!-- URLs. -->
[Xamarin Studio]: http://xamarin.com/platform
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Xcode]: https://go.microsoft.com/fwLink/?LinkID=266532
[Installing Xamarin.iOS on Windows]: http://developer.xamarin.com/guides/ios/getting_started/installation/windows/
[apns object]: http://go.microsoft.com/fwlink/p/?LinkId=272333


 

<!---HONumber=AcomDC_1203_2015--->