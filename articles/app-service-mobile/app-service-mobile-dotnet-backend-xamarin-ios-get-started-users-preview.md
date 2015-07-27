<properties 
	pageTitle="Erste Schritte mit der Authentifizierung für mobile Anwendungen in Xamarin iOS" 
	description="Erfahren Sie, wie Sie Mobile Apps zum Authentifizieren Ihrer Xamarin iOS-App über eine Vielzahl von Identitätsanbietern nutzen können, darunter AAD, Google, Facebook, Twitter und Microsoft." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/30/2015" 
	ms.author="mahender"/>

# Hinzufügen der Authentifizierung zu Ihrer Xamarin.iOS-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]

In diesem Thema wird die Authentifizierung von Benutzern einer mobilen App Service-App über Ihre Clientanwendung veranschaulicht. In diesem Lernprogramm fügen Sie dem Schnellstartprojekt durch Verwenden eines von App Service unterstützten Identitätsanbieters eine Authentifizierungsfunktion hinzu. Nach der erfolgreichen Authentifizierung und Autorisierung durch Ihre mobile App wird die Benutzer-ID angezeigt.

Dieses Lernprogramm baut auf dem Mobile App-Schnellstart auf. Sie müssen außerdem zunächst das Lernprogramm [Erstellen einer Xamarin.iOS-App] abschließen.

##<a name="register"></a>Registrieren Ihrer App für die Authentifizierung und Konfigurieren von App Services

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

<ol start="7">
<li><p>Führen Sie das Client-Projekt in Visual Studio oder Xamarin Studio auf einem Gerät oder Simulator aus. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.</p>
   
   	<p>Dies liegt daran, dass die App versucht, als nicht authentifizierter Benutzer auf den Code Ihrer mobilen App zuzugreifen, die <em>TodoItem</em>-Tabelle jetzt jedoch Authentifizierung erfordert.</p></li>
</ol>

Als Nächstes aktualisieren Sie die App, um Benutzer zu authentifizieren, bevor diese Ressourcen von App Service anfordern.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

In diesem Abschnitt modifizieren Sie die App, sodass vor der Anzeige von Daten ein Anmeldebildschirm angezeigt wird. Wenn die App gestartet wird, baut sie keine Verbindung zu App Service auf und zeigt keinerlei Daten an. Nachdem der Benutzer die Aktualisierungsgeste durchführt, wird der Anmeldebildschirm angezeigt, nach der erfolgreichen Anmeldung dann die Liste von To-Do-Objekten.

1. Öffnen Sie im Client-Projekt die Datei **QSTodoService.cs** und fügen Sie folgende Deklarationen zu QSTodoService hinzu:

		// Logged in user
		private MobileServiceUser user; 
		public MobileServiceUser User { get { return user; } }

2. Fügen Sie eine neue Methode **Authenticate** zu **QSTodoService** hinzu mit der folgenden Definition:

        public async Task Authenticate(UIViewController view)
        {
            try
            {
                user = await client.LoginAsync(view, MobileServiceAuthenticationProvider.Facebook);
            }
            catch (Exception ex)
            {
                Console.Error.WriteLine (@"ERROR - AUTHENTICATION FAILED {0}", ex.Message);
            }
        }

> [AZURE.NOTE]Falls Sie einen anderen Identitätsanbieter als ein Facebook verwenden, ändern Sie den an **LoginAsync** übergebenen Wert auf einen der folgenden Werte: _MicrosoftAccount_, _Twitter_, _Google_ oder _WindowsAzureActiveDirectory_.

3. Öffnen Sie **QSTodoListViewController.cs**. Ändern Sie die Methodendefinition von **ViewDidLoad** und entfernen Sie den Aufruf von **RefreshAsync()** in der Nähe des Endes:

		public override async void ViewDidLoad ()
		{
			base.ViewDidLoad ();

			todoService = QSTodoService.DefaultService;
           await todoService.InitializeStoreAsync ();

           RefreshControl.ValueChanged += async (sender, e) => {
                await RefreshAsync ();
           }

			// Comment out the call to RefreshAsync
			// await RefreshAsync ();
		}


4. Ändern Sie die Methode **RefreshAsync** für die Authentifizierung und Anzeige eines Anmeldebildschirms, wenn die Eigenschaft **User** NULL ist. Bei folgendem Code oben in der Methodendefinition:

		// start of RefreshAsync method
		if (todoService.User == null) {
			await QSTodoService.DefaultService.Authenticate (this);
			if (todoService.User == null) {
				Console.WriteLine ("couldn't login!!");
				return;
			}
		}
		// rest of RefreshAsync method
	
5. Klicken Sie auf die Schaltfläche **Ausführen**, um das Projekt zu erstellen und die App im iPhone-Simulator zu starten. Überprüfen Sie, dass die App keine Daten anzeigt.

	Führen Sie die Aktualisierungsgeste durch Herunterziehen der Objektliste, was die Anzeige des Anmeldebildschirms auslöst. Nachdem Sie erfolgreich gültige Anmeldeinformationen eingegeben haben, zeigt die App die Liste der To-Do-Objekte an, und Sie können die Daten ändern.

 
<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Erstellen einer Xamarin.iOS-App]: app-service-mobile-dotnet-backend-xamarin-ios-get-started-preview.md

[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=July15_HO3-->