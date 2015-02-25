<properties pageTitle="Erste Schritte bei der Authentifizierung in Mobile Services für Xamarin iOS-Apps - Azure Mobile Services" description="Erfahren Sie, wie Sie Mobile Services verwenden, um die Benutzer Ihrer Xamarin iOS-App über verschiedene Identitätsanbieter, einschließlich Google, Facebook, Twitter und Microsoft, zu authentifizieren." services="mobile-services" documentationCenter="xamarin" authors="lindydonna" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="09/23/2014" ms.author="donnam"/>

# Hinzufügen von Authentifizierung zu Ihrer Mobile Services-App

[AZURE.INCLUDE [mobile-services-selector-get-started-users](../includes/mobile-services-selector-get-started-users.md)]

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1. [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2. [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3. [Hinzufügen von Authentifizierung zur App]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen. 

##<a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[AZURE.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)] 

[AZURE.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)] 

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)] 

<ol start="6">
<li><p>Führen Sie das Client-Projekt in Visual Studio oder Xamarin Studio auf einem Gerät oder Simulator aus. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.</p>
   
   	<p>Dies liegt daran, dass die App versucht, als nicht authentifizierter Benutzer auf Mobile Services zuzugreifen, die <em>TodoItem</em>-Tabelle jetzt jedoch Authentifizierung erfordert.</p></li>
</ol>

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

In diesem Abschnitt modifizieren Sie die App, sodass vor der Anzeige von Daten ein Anmeldebildschirm angezeigt wird. Wenn die App gestartet wird, baut sie keine Verbindung zu ihrem mobilen Dienst auf und zeigt keinerlei Daten an. Nachdem der Benutzer die Aktualisierungsgeste durchführt, wird der Anmeldebildschirm angezeigt, nach der erfolgreichen Anmeldung dann die Liste von To-Do-Objekten.

1. Öffnen Sie im Client-Projekt die Datei **QSTodoService.cs**, und fügen Sie folgende Deklarationen zu QSTodoService hinzu:

		// Mobile Service logged in user
		private MobileServiceUser user; 
		public MobileServiceUser User { get { return user; } }

2. Fügen Sie eine neue Methode **Authenticate** zu **QSTodoService** hinzu mit der folgenden Definition:

        private async Task Authenticate(UIViewController view)
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

> [AZURE.NOTE] Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den an **LoginAsync** oben übergebenen Wert auf einen der folgenden Werte: _MicrosoftAccount_, _Twitter_, _Google_ oder _WindowsAzureActiveDirectory_.

3. Öffnen Sie **QSTodoListViewController.cs**. Ändern Sie die Methodendefinition von **ViewDidLoad**, und entfernen Sie den Aufruf von **RefreshAsync()** in der Nähe des Endes:

		public override async void ViewDidLoad ()
		{
			base.ViewDidLoad ();

			todoService = QSTodoService.DefaultService;

			todoService.BusyUpdate += (bool busy) => {
				if (busy)
					activityIndicator.StartAnimating ();
				else 
					activityIndicator.StopAnimating ();
			};

			// Comment out the call to RefreshAsync
			// await RefreshAsync ();

			AddRefreshControl ();
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

	Führen Sie die Aktualisierungsgeste durch Herunterziehen der Objektliste, was die Anzeige des Anmeldebildschirms auslöst. Nachdem Sie gültige Anmeldeinformationenen erfolgreich eingegeben haben, zeigt die App die Liste der To-Do-Objekte an, und Sie können die Daten ändern.

<!-- ## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm [Serviceseitige Autorisierung von Mobile Services-Benutzern][Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. 
 -->
 
<!-- Anchors. -->
[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
[Hinzufügen von Authentifizierung zur App]: #add-authentication
[Nächste Schritte]:#next-steps


<!-- URLs. -->
[Absenden einer App-Seite]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Meine Anwendungen]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK für Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-push/
[Autorisieren von Benutzern mit Skripts]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-authorize-users-in-scripts
[JavaScript und HTML]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/


<!--HONumber=42-->
