<properties 
	pageTitle="Authentifizieren Ihrer Windows Store-App mit Live Connect" 
	description="Erfahren Sie, wie Sie das einmalige Anmelden mit Live Connect in Azure Mobile Services von einer Windows Store-Anwendung verwenden." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/08/2015" 
	ms.author="glenga"/>

# Authentifizieren Ihrer Windows Store-App mit auf dem Client verwalteter Authentifizierung mit Microsoft-Konto

[AZURE.INCLUDE [mobile-services-selector-single-signon](../../includes/mobile-services-selector-single-signon.md)] 

## Übersicht
Unter diesem Thema erfahren Sie, wie Sie mit dem Live SDK einer universellen Windows-App ein Authentifizierungstoken für das Microsoft-Konto erhalten. Mit diesem Token können Sie anschließend Benutzer mit Azure Mobile Services authentifizieren. In diesem Lernprogramm fügen Sie einem vorhandenen Projekt mithilfe des Live SDK eine Microsoft-Kontoauthentifizierung hinzu. Sobald die Authentifizierung erfolgreich war, wird ein angemeldeter Benutzer mit seinem Namen begrüßt, und der Benutzer-ID-Wert wird angezeigt.

>[AZURE.NOTE]Dieses Lernprogramm veranschaulicht die Vorteile der Verwendung einer auf dem Client gesteuerten Authentifizierung und des Live SDK. Dadurch können Sie einen bereits angemeldeten Benutzer einfacher mit Ihrem Mobile Service authentifizieren. Sie können auch zusätzliche Bereiche anfordern, damit Ihre App auch Zugriff auf Ressourcen wie OneDrive erhält.
>Eine dienstgesteuerte Authentifizierung ist etwas umfassender, und sie unterstützt auch mehrere Authentifizierungsanbieter. Weitere Informationen zur dienstgesteuerten Authentifizierung finden Sie unter dem Thema [Hinzufügen von Authentifizierung zur App](mobile-services-javascript-backend-windows-universal-dotnet-get-started-users.md).

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Live SDK]
+ Microsoft Visual Studio 2013 Update 3 oder eine höhere Version.
+ Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst die Lernprogramme [Erste Schritte mit Mobile Services](mobile-services-javascript-backend-windows-store-dotnet-get-started.md) oder [Hinzufügen von Mobile Services zu einer vorhandenen App] abschließen.

## Registrieren Ihrer App für die Verwendung eines Microsoft-Kontos für die Authentifizierung

Damit Sie Benutzer authentifizieren können, müssen Sie Ihre App beim Microsoft-Konto für das Developer Center registrieren. Sie müssen dann diese Registrierung mit Ihrem mobilen Dienst verbinden. Führen Sie die Schritte im folgenden Thema aus, um eine Microsoft-Kontoregistrierung zu erstellen und mit Ihrem mobilen Dienst zu verbinden.

+ [Registrieren Ihrer App für die Verwendung einer Microsoft-Kontoanmeldung](mobile-services-how-to-register-microsoft-authentication.md)

## <a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

Als Nächstes müssen Sie den Zugriff auf eine Ressource, in diesem Fall die *TodoItems*-Tabelle, einschränken, damit nur angemeldete Benutzer darauf zugreifen können.

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../../includes/mobile-services-restrict-permissions-windows.md)] 

## <a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

Abschließend fügen Sie das Live SDK hinzu und verwenden es zum Authentifizieren von Benutzern in Ihrer App.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2. Wählen Sie im linken Bereich die Kategorie **Online** aus, suchen Sie nach **Live SDK**, klicken Sie auf dem Paket **Live SDK** auf **Installieren**, wählen Sie alle Projekte aus, und stimmen Sie dem Lizenzvertrag zu.

  	Damit wird das Live SDK der Projektmappe hinzugefügt.

3. Öffnen Sie die gemeinsam genutzte Projektdatei "MainPage.xaml.cs", und fügen Sie die folgende using-Anweisung hinzu:

        using Microsoft.Live;        

4. Fügen Sie den folgenden Codeausschnitt zur **MainPage**-Klasse hinzu:
	
        private LiveConnectSession session;
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {

            // Get the URL the mobile service.
            var serviceUrl = App.MobileService.ApplicationUri.AbsoluteUri;

            // Create the authentication client using the mobile service URL.
            LiveAuthClient liveIdClient = new LiveAuthClient(serviceUrl);

            while (session == null)
            {
                // Request the authentication token from the Live authentication service.
				// The wl.basic scope is requested.
                LiveLoginResult result = await liveIdClient.LoginAsync(new string[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;

                    // Get information about the logged-in user.
                    LiveConnectClient client = new LiveConnectClient(session);
                    LiveOperationResult meResult = await client.GetAsync("me");

                    // Use the Microsoft account auth token to sign in to Mobile Services.
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                    // Display a personalized sign-in greeting.
                    string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                    var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                    var dialog = new MessageDialog(message, title);
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
                else
                {
                    session = null;
                    var dialog = new MessageDialog("You must log in.", "Login Required");
                    dialog.Commands.Add(new UICommand("OK"));
                    await dialog.ShowAsync();
                }
            }
        }
    
    Auf diese Weise wird eine Membervariable zum Speichern der aktuellen Live Connect-Sitzung erstellt und eine Methode zur Verarbeitung des Authentifizierungsprozesses.

	>[AZURE.NOTE]Sie sollten versuchen, zwischengespeicherte Live- oder Mobile Services-Autorisierungstokens zu verwenden, bevor Sie neue Tokens von den Diensten anfordern. Andernfalls können nutzungsbedingte Probleme auftreten, wenn viele Kunden die App gleichzeitig starten möchten. Ein Beispiel zum Zwischenspeichern dieses Tokens finden Sie unter [Erste Schritte mit der Authentifizierung](../mobile-services-windows-store-dotnet-get-started-users.md#tokens).

5. Löschen Sie in der vorhandenen Methodenüberschreibung von **OnNavigatedTo** den Aufruf der Methode **RefreshTodoItems**, oder kommentieren Sie diesen Aufruf aus.

	Dadurch wird sichergestellt, dass die Daten erst geladen werden, nachdem der Benutzer authentifiziert wurde. Als Nächstes fügen Sie eine Schaltfläche hinzu, um den Anmeldevorgang zu beginnen.

6. Fügen Sie den folgenden Codeausschnitt zur MainPage-Klasse hinzu:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = Windows.UI.Xaml.Visibility.Collapsed;
            await RefreshTodoItems();
        }
		
7. Öffnen Sie im Windows Store-App-Projekt die Projektdatei "MainPage.xaml", und fügen Sie unmittelbar vor dem Element, das die Schaltfläche **Save** definiert, folgendes **Button**-Element hinzu:

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>

8. Führen Sie den obigen Schritt für das Windows Phone Store-App-Projekt erneut aus, fügen Sie das **Button**-Element diesmal jedoch im **TitlePanel** nach dem **TextBlock**-Element ein.
		
9. Drücken Sie die Taste F5, um die App auszuführen, und melden Sie sich bei Live Connect mit Ihrem Microsoft-Konto an.

	Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

10. Klicken Sie mit der rechten Maustaste auf das Windows Phone Store-App-Projekt, klicken Sie auf **Als Startprojekt festlegen**, und führen Sie dann den obigen Schritt erneut aus, um sicherzustellen, dass die Windows Phone Store-App ebenfalls ordnungsgemäß ausgeführt wird.

Nun können alle von einem der registrierten Identitätsanbieter authentifizierten Benutzer auf die Tabelle *TodoItem* zugreifen. Um benutzerspezifische Daten besser zu schützen, müssen Sie zudem eine Autorisierung implementieren. Hierzu rufen Sie die Benutzer-ID eines Benutzers ab, und ermitteln damit, welche Zugriffsebene dieser Benutzer für eine bestimmte Ressource erhalten soll.

## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm [Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. Weitere Informationen zur Verwendung anderer Identitätsanbieter für die Authentifizierung finden Sie unter [Erste Schritte mit der Authentifizierung]. Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET unter [Mobile Services .NET – Erstellen einer konzeptionellen Referenz].

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039

[Hinzufügen von Mobile Services zu einer vorhandenen App]: ../mobile-services-windows-store-dotnet-get-started-data.md
[Erste Schritte mit der Authentifizierung]: ../mobile-services-windows-store-dotnet-get-started-users.md
[Autorisieren von Benutzern mit Skripts]: mobile-services-javascript-backend-service-side-authorization.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Mobile Services .NET – Erstellen einer konzeptionellen Referenz]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253

<!--HONumber=54--> 