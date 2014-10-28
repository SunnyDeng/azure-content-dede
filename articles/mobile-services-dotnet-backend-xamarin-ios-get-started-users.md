<properties linkid="mobile-services-dotnet-backend-xamarin-ios-get-started-users" urlDisplayName="Get Started with authentication in Mobile Services for Xamarin iOS apps" pageTitle="Get Started with authentication in Mobile Services for Xamarin iOS apps - Azure Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to authenticate users of your Xamarin iOS app through a variety of identity providers, including Google, Facebook, Twitter, and Microsoft." metaCanonical="" services="" documentationCenter="Mobile" title="Get Started with authentication in Mobile Services" authors="donnam" solutions="" manager="" editor="mollybos" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Erste Schritte bei der Authentifizierung in Mobile Services

<div class="dev-center-tutorial-selector sublanding">
  <a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users" title="Windows Store C#">Windows Store C#</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users" title="Windows Store JavaScript">Windows Store JavaScript</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users" title="Windows Phone">Windows Phone</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users" title="iOS">iOS</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-users" title="Android">Android</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users" title="Xamarin.iOS" class="current">Xamarin.iOS</a>
<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users" title="Xamarin.Android">Xamarin.Android</a>    
</div>

<div class="dev-center-tutorial-subselector"><a href="/de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/" title=".NET-Backend" class="current">.NET-Backend</a> | <a href="/de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users/"  title="JavaScript-Backend">JavaScript-Backend</a></div>

In diesem Thema erfahren Sie, wie Sie Benutzer in Azure Mobile Services über Ihre App authentifizieren. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Mobile Services unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung durch Mobile Services wird der Benutzer-ID-Wert angezeigt.

Dieses Lernprogramm zeigt Ihnen die grundlegenden Schritte zur Aktivierung von Authentifizierung in Ihrer App:

1. [Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services][Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]
2. [Einschränken von Tabellenberechtigungen für authentifizierte Benutzer][Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]
3. [Hinzufügen von Authentifizierung zur App][Hinzufügen von Authentifizierung zur App]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf. Sie müssen zunächst das Lernprogramm [Erste Schritte mit Mobile Services][Erste Schritte mit Mobile Services] abschließen.

## <a name="register"></a>Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services

[WACOM.INCLUDE [mobile-services-register-authentication](../includes/mobile-services-register-authentication.md)]

[WACOM.INCLUDE [mobile-services-dotnet-backend-aad-server-extension](../includes/mobile-services-dotnet-backend-aad-server-extension.md)]

## <a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[WACOM.INCLUDE [mobile-services-restrict-permissions-dotnet-backend](../includes/mobile-services-restrict-permissions-dotnet-backend.md)]

<ol start="6">
<li><p>Führen Sie das Client-Projekt in Visual Studio oder Xamarin Studio auf einem Gerät oder Simulator aus. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.</p>
   
   	<p>Dies liegt daran, dass die App als nicht authentifizierter Benutzer auf Mobile Services zugreift und die <em>TodoItem</em>-Tabelle nun eine Authentifizierung verlangt.</p></li>
</ol>

Als Nächstes werden Sie die App aktualisieren, um Benutzer zu authentifizieren, bevor diese Ressourcen vom Mobile Service anfordern.

## <a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

In diesem Abschnitt modifizieren Sie die App, sodass vor der Anzeige von Daten ein Anmeldebildschirm angezeigt wird. Wenn die App gestartet wird, baut sie keine Verbindung zu ihrem mobilen Dienst auf und zeigt keinerlei Daten an. Nachdem der Benutzer die Aktualisierungsgeste durchführt, wird der Anmeldebildschirm angezeigt, nach der erfolgreichen Anmeldung dann die Liste von To-Do-Objekten.

1. Öffnen Sie im Client-Projekt die Datei **QSTodoService.cs** und fügen Sie folgende Deklarationen zu QSTodoService hinzu:

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

    <div class="dev-callout"><b>Hinweis</b>
	<p>Falls Sie einen anderen Identit&auml;tsanbieter als Facebook verwenden, &auml;ndern Sie den an <strong>oginWithProvider</strong> oben &uuml;bergebenen Wert auf einen der folgenden Werte: <i>MicrosoftAccount</i>, <i>Twitter</i>, <i>Google</i> oder <i>WindowsAzureActiveDirectory</i>.</p>
    </div>

3. Öffnen Sie **QSTodoListViewController.cs**. Ändern Sie die Methodendefinition von **ViewDidLoad** und entfernen Sie den Aufruf von **RefreshAsync()** in der Nähe des Endes:

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

## <a name="next-steps"></a>Next steps  
In the next tutorial, [Service-side authorization of Mobile Services users][Authorize users with scripts], you will take the user ID value provided by Mobile Services based on an authenticated user and use it to filter the data returned by Mobile Services.

<!-- Anchors. --> 
<!-- URLs. -->

[Windows Store C#]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users "Windows Store C#"
[Windows Store JavaScript]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users "Windows Store JavaScript"
[Windows Phone]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users "Windows Phone"
[iOS]: /de-de/documentation/articles/mobile-services-dotnet-backend-ios-get-started-users "iOS"
[Android]: /de-de/documentation/articles/mobile-services-dotnet-backend-android-get-started-users "Android"
[Xamarin.iOS]: /de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users "Xamarin.iOS"
[Xamarin.Android]: /de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-android-get-started-users "Xamarin.Android"
[.NET-Backend]: /de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started-users/ ".NET-Backend"
[JavaScript-Backend]: /de-de/documentation/articles/partner-xamarin-mobile-services-ios-get-started-users/ "JavaScript-Backend"
[Registrieren Ihrer App für Authentifizierung und Konfigurieren von Mobile Services]: #register
[Einschränken von Tabellenberechtigungen für authentifizierte Benutzer]: #permissions
[Hinzufügen von Authentifizierung zur App]: #add-authentication
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-xamarin-ios-get-started/
[mobile-services-register-authentication]: ../includes/mobile-services-register-authentication.md
[mobile-services-dotnet-backend-aad-server-extension]: ../includes/mobile-services-dotnet-backend-aad-server-extension.md
[mobile-services-restrict-permissions-dotnet-backend]: ../includes/mobile-services-restrict-permissions-dotnet-backend.md
