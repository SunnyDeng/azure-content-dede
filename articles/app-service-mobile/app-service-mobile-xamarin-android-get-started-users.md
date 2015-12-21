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
	ms.date="12/07/2015" 
	ms.author="mahender"/>

# Hinzufügen der Authentifizierung zu Ihrer Xamarin.Android-App

[AZURE.INCLUDE [app-service-mobile-selector-get-started-users](../../includes/app-service-mobile-selector-get-started-users.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

In diesem Thema wird die Authentifizierung von Benutzern einer mobilen App über Ihre Clientanwendung veranschaulicht. In diesem Lernprogramm fügen Sie eine Authentifizierung zu dem Schnellstartprojekt hinzu. Sie verwenden dazu einen Identitätsanbieter, der von Azure Mobile Apps unterstützt wird. Nach der erfolgreichen Authentifizierung und Autorisierung in Mobile Apps wird die Benutzer-ID angezeigt.

Dieses Lernprogramm baut auf dem Mobile App-Schnellstart auf. Sie müssen außerdem zunächst das Lernprogramm [Erstellen einer Xamarin.Android-App] abschließen. Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt das Authentifizierungs-Erweiterungspaket hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) (in englischer Sprache).

##<a name="register"></a>Registrieren Ihrer App für die Authentifizierung und Konfigurieren von App Services

[AZURE.INCLUDE [app-service-mobile-register-authentication](../../includes/app-service-mobile-register-authentication.md)]

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

[AZURE.INCLUDE [app-service-mobile-restrict-permissions-dotnet-backend](../../includes/app-service-mobile-restrict-permissions-dotnet-backend.md)]

Führen Sie das Clientprojekt in Visual Studio oder Xamarin Studio auf einem Gerät oder Emulator aus. Stellen Sie sicher, dass ein Ausnahmefehler mit dem Statuscode 401 (Nicht autorisiert) angezeigt wird, nachdem die App gestartet wurde. Dies liegt daran, dass die App versucht, als nicht authentifizierter Benutzer auf Ihr Mobile App-Back-End zuzugreifen. Die Tabelle *TodoItem* erfordert jetzt eine Authentifizierung.

Als Nächstes aktualisieren Sie die Client-App, um Ressourcen vom mobilen App-Back-End mit einem authentifizierten Benutzer zu aktualisieren.

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

Die App wird so aktualisiert, dass Benutzer auf die Schaltfläche **Anmelden** tippen und sich authentifizieren müssen, bevor Daten angezeigt werden.

1. Fügen Sie der **TodoActivity**-Klasse den folgenden Code hinzu:

	    // Define a authenticated user.
	    private MobileServiceUser user;
	    private async Task<bool> Authenticate()
	    {
	            var success = false;
	            try
	            {
	                // Sign in with Facebook login using a server-managed flow.
	                user = await client.LoginAsync(this,
	                    MobileServiceAuthenticationProvider.Facebook);
	                CreateAndShowDialog(string.Format("you are now logged in - {0}",
	                    user.UserId), "Logged in!");
	
	                success = true;
	            }
	            catch (Exception ex)
	            {
	                CreateAndShowDialog(ex, "Authentication failed");
	            }
	            return success;
	    }

        [Java.Interop.Export()]
        public async void LoginUser(View view)
        {
            // Load data only after authentication succeeds.
            if (await Authenticate())
            {
                //Hide the button after authentication succeeds. 
                FindViewById<Button>(Resource.Id.buttonLoginUser).Visibility = ViewStates.Gone;

                // Load the data.
                OnRefreshItemsSelected();
            }
        }

    Dies erstellt eine neue Methode zum Authentifizieren eines Benutzers und einen Methodenhandler für die neue Schaltfläche **Anmelden**. Der Benutzer wird im Codebeispiel oben mithilfe eines Facebook-Logins authentifiziert. Ein Dialogfeld wird verwendet, um die Benutzer-ID nach der Authentifizierung anzuzeigen.

    > [AZURE.NOTE]Falls Sie einen anderen Identitätsanbieter als Facebook verwenden, ändern Sie den an **LoginAsync** übergebenen Wert auf einen der folgenden Werte: _MicrosoftAccount_, _Twitter_, _Google_ oder _WindowsAzureActiveDirectory_.

3. Löschen Sie in der **OnCreate**-Methode die folgende Codezeile, oder kommentieren Sie sie aus:

		OnRefreshItemsSelected ();

4. Fügen Sie in der Datei „Activity\_To\_Do.axml“ die folgende Definition der Schaltfläche *LoginUser* vor der vorhandenen Schaltfläche *AddItem* hinzu:

      	<Button
            android:id="@+id/buttonLoginUser"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="LoginUser"
            android:text="@string/login_button_text" />

5. Fügen Sie das folgende Element in die Ressourcendatei „Strings.xml“ ein:

		<string name="login_button_text">Sign in</string> 

6. Führen Sie das Clientprojekt in Visual Studio oder Xamarin Studio auf einem Gerät oder Emulator aus, und melden Sie sich mit dem ausgewählten Identitätsanbieter an.

   	Nachdem Sie sich erfolgreich angemeldet haben, zeigt die App Ihre Login-ID und die Liste der zu erledigenden Elemente an, und Sie können die Daten ändern.


<!-- URLs. -->
[Erstellen einer Xamarin.Android-App]: app-service-mobile-xamarin-android-get-started.md
 

<!---HONumber=AcomDC_1210_2015-->