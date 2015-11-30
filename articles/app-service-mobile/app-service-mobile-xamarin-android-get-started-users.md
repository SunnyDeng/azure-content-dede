<properties 
	pageTitle="Erste Schritte mit der Authentifizierung für mobile Apps in Xamarin Android" 
	description="Erfahren Sie, wie Sie mobile Apps zum Authentifizieren Ihrer Xamarin Android-App über eine Vielzahl von Identitätsanbietern nutzen können, darunter AAD, Google, Facebook, Twitter und Microsoft." 
	services="app-service\mobile" 
	documentationCenter="xamarin" 
	authors="mattchenderson" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="app-service-mobile" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-android" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="08/27/2015" 
	ms.author="mahender"/>

# Hinzufügen der Authentifizierung zu Ihrer Xamarin.Android-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]
&nbsp;  
[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

In diesem Thema wird die Authentifizierung von Benutzern einer mobilen App über Ihre Clientanwendung veranschaulicht. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Azure Mobile Apps unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung in Mobile Apps wird die Benutzer-ID angezeigt.

Dieses Lernprogramm baut auf dem Mobile App-Schnellstart auf. Sie müssen außerdem zunächst das Lernprogramm [Erstellen einer Xamarin.Android-App] abschließen. Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt das Authentifizierungs-Erweiterungspaket hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) (in englischer Sprache).

##<a name="register"></a>Registrieren Ihrer App für die Authentifizierung und Konfigurieren von App Services

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

<ol start="4">
<li><p>Führen Sie das Clientprojekt in Visual Studio oder Xamarin Studio auf einem Gerät oder Emulator aus. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde.</p>
   
   	<p>Dies liegt daran, dass die App versucht, als nicht authentifizierter Benutzer auf Ihr Mobile App-Back-End zuzugreifen. Die Tabelle <em>TodoItem</em> erfordert jetzt eine Authentifizierung.</p></li>
</ol>

Als Nächstes aktualisieren Sie die Client-App, um Ressourcen vom mobilen App-Back-End mit einem authentifizierten Benutzer zu aktualisieren.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

1. Fügen Sie der **TodoActivity**-Klasse die folgende Eigenschaft hinzu:

			private MobileServiceUser user;

2. Fügen Sie der **ToDoActivity**-Klasse die folgende Methode hinzu:

	        private async Task Authenticate()
	        {
	            try
	            {
	                user = await client.LoginAsync(this, MobileServiceAuthenticationProvider.Facebook);
	                CreateAndShowDialog(string.Format("you are now logged in - {0}", user.UserId), "Logged in!");
	            }
	            catch (Exception ex)
	            {
	                CreateAndShowDialog(ex, "Authentication failed");
	            }
	        }

    Dadurch wird eine neue Methode zum Authentifizieren eines Benutzers erstellt. Der Benutzer wird im Codebeispiel oben mithilfe eines Facebook-Logins authentifiziert. Ein Dialogfeld wird verwendet, um die Benutzer-ID nach der Authentifizierung anzuzeigen.

    > [AZURE.NOTE]Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den an **LoginAsync** übergebenen Wert auf einen der folgenden Werte: _MicrosoftAccount_, _Twitter_, _Google_ oder _WindowsAzureActiveDirectory_.

3. Fügen Sie in der **onCreate**-Methode die folgende Codezeile im Anschluss an den Code hinzu, der das `MobileServiceClient`-Objekt instanziiert.

		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL, Gateway URL and key
		client = new MobileServiceClient (applicationURL, gatewayURL, applicationKey);
		
		await Authenticate(); // Added for authentication

	Dieser Aufruf startet den Authentifizierungsprozess und wartet asynchron auf eine Antwort.


4. Führen Sie das Clientprojekt in Visual Studio oder Xamarin Studio auf einem Gerät oder Emulator aus, und melden Sie sich mit dem ausgewählten Identitätsanbieter an.

   	Nachdem Sie sich erfolgreich angemeldet haben, zeigt die App Ihr Login-ID und die Liste der Todo-Objekte an, und Sie können die Daten ändern.


<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Erstellen einer Xamarin.Android-App]: app-service-mobile-xamarin-android-get-started.md

[Azure Management Portal]: https://portal.azure.com
 

<!---HONumber=Nov15_HO4-->