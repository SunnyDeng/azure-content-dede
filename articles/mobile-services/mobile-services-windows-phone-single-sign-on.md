<properties 
	pageTitle="Authentifizieren Ihrer App mit Live Connect (Windows Phone) | Microsoft Azure" 
	description="Erfahren Sie, wie Sie das einmalige Anmelden mit Live Connect in Azure Mobile Services von einer Windows Phone-Anwendung verwenden." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/09/2015" 
	ms.author="glenga"/>

# Authentifizieren Ihrer Windows Phone-App mit auf dem Client verwalteter Authentifizierung mit Microsoft-Konto

[AZURE.INCLUDE [mobile-services-selector-single-signon](../../includes/mobile-services-selector-single-signon.md)]
##Übersicht
Unter diesem Thema erfahren Sie, wie Sie mit dem Live SDK einer Windows Phone 8.1 Silverlight- oder Windows Phone 8-App ein Authentifizierungstoken für das Microsoft-Konto erhalten. Mit diesem Token können Sie anschließend Benutzer mit Azure Mobile Services authentifizieren. In diesem Lernprogramm fügen Sie einem vorhandenen Projekt mithilfe des Live SDK eine Microsoft-Kontoauthentifizierung hinzu. Sobald die Authentifizierung erfolgreich war, wird ein angemeldeter Benutzer mit seinem Namen begrüßt, und der Benutzer-ID-Wert wird angezeigt.

>[AZURE.NOTE]Dieses Lernprogramm veranschaulicht die Vorteile der Verwendung einer auf dem Client verwalteten Authentifizierung und des Live SDK. Dadurch können Sie einen bereits angemeldeten Benutzer einfacher mit Ihrem Mobile Service authentifizieren. Sie können auch zusätzliche Bereiche anfordern, damit Ihre App auch Zugriff auf Ressourcen wie OneDrive erhält. Eine dienstverwaltete Authentifizierung ist etwas umfassender, und sie unterstützt auch mehrere Authentifizierungsanbieter. Weitere Informationen zur dienstverwalteten Authentifizierung finden Sie unter dem Thema [Hinzufügen von Authentifizierung zur App](mobile-services-windows-phone-get-started-users.md).

Für dieses Lernprogramm ist Folgendes erforderlich:

+ [Live SDK]
+ Microsoft Visual Studio 2013 Update 3 oder eine höhere Version.
+ Sie müssen außerdem zunächst das Lernprogramm [Hinzufügen von Mobile Services zu einer vorhandenen App] abschließen.

##Registrieren Ihrer App für die Verwendung eines Microsoft-Kontos 

Damit Sie Benutzer authentifizieren können, müssen Sie Ihre App beim Microsoft-Konto für das Developer Center registrieren. Sie müssen dann diese Registrierung mit Ihrem mobilen Dienst verbinden. Führen Sie die Schritte im folgenden Thema aus, um eine Microsoft-Kontoregistrierung zu erstellen und mit Ihrem mobilen Dienst zu verbinden.

+ [Registrieren Ihrer App für die Verwendung einer Microsoft-Kontoanmeldung](mobile-services-how-to-register-microsoft-authentication.md) 

##<a name="permissions"></a>Einschränken von Berechtigungen für authentifizierte Benutzer

Als Nächstes müssen Sie den Zugriff auf eine Ressource, in diesem Fall die *TodoItems*-Tabelle, einschränken, damit nur angemeldete Benutzer darauf zugreifen können.

[AZURE.INCLUDE [mobile-services-restrict-permissions-windows](../../includes/mobile-services-restrict-permissions-windows.md)]

##<a name="add-authentication"></a>Hinzufügen von Authentifizierung zur App

Abschließend fügen Sie das Live SDK hinzu und verwenden es zum Authentifizieren von Benutzern in Ihrer App.

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe, und wählen Sie dann **NuGet-Pakete verwalten** aus.

2. Wählen Sie im linken Bereich die Kategorie **Online** aus, suchen Sie nach **Live SDK**, klicken Sie auf dem Paket **Live SDK** auf **Installieren**, wählen Sie alle Projekte aus, und stimmen Sie dem Lizenzvertrag zu.

  	Damit wird das Live SDK der Projektmappe hinzugefügt.

5. Öffnen Sie die Projektdatei "mainpage.xaml.cs", und fügen Sie folgende Ausdrücke ein:

        using Microsoft.Live;      

6. Fügen Sie den folgenden Codeausschnitt zur MainPage-Klasse hinzu:
	
        private LiveConnectSession session;
        private static string clientId = "<microsoft-account-client-id>";
        private async System.Threading.Tasks.Task AuthenticateAsync()
        {
            // Create the authentication client using the client ID of the registration.
            LiveAuthClient liveIdClient = new LiveAuthClient(clientId);

            while (session == null)
            {
                LiveLoginResult result = await liveIdClient.LoginAsync(new[] { "wl.basic" });
                if (result.Status == LiveConnectSessionStatus.Connected)
                {
                    session = result.Session;
                    LiveConnectClient client = new LiveConnectClient(result.Session);
                    LiveOperationResult meResult = await client.GetAsync("me");
                    MobileServiceUser loginResult = await App.MobileService
                        .LoginWithMicrosoftAccountAsync(result.Session.AuthenticationToken);

                    string title = string.Format("Welcome {0}!", meResult.Result["first_name"]);
                    var message = string.Format("You are now logged in - {0}", loginResult.UserId);
                    MessageBox.Show(message, title, MessageBoxButton.OK);
                }
                else
                {
                    session = null;
                    MessageBox.Show("You must log in.", "Login Required", MessageBoxButton.OK);
                }
            }
        }

    Auf diese Weise wird eine Membervariable zum Speichern der aktuellen Live Connect-Sitzung erstellt und eine Methode zur Verarbeitung des Authentifizierungsprozesses. "LiveLoginResult" enthält das Authentifizierungstoken, das Mobile Services zur Authentifizierung des Benutzers zugewiesen wird.

7. Ersetzen Sie im oben aufgeführten Codeausschnitt den Platzhalter `<microsoft-account-client-id>` durch die Client-ID, die Sie bei der Microsoft-Konto-Registrierung für Ihre App erhalten haben.

5. Löschen Sie in der vorhandenen Methodenüberschreibung von **OnNavigatedTo** den Aufruf der Methode **RefreshTodoItems**, oder kommentieren Sie diesen Aufruf aus.

	Dadurch wird sichergestellt, dass die Daten erst geladen werden, nachdem der Benutzer authentifiziert wurde. Als Nächstes fügen Sie eine Schaltfläche hinzu, um den Anmeldevorgang zu beginnen.

6. Fügen Sie den folgenden Codeausschnitt zur MainPage-Klasse hinzu:

        private async void ButtonLogin_Click(object sender, RoutedEventArgs e)
        {
            // Login the user and then load data from the mobile service.
            await AuthenticateAsync();

            // Hide the login button and load items from the mobile service.
            this.ButtonLogin.Visibility = System.Windows.Visibility.Collapsed;
            RefreshTodoItems();
        }
		
7. Öffnen Sie im App-Projekt die Projektdatei "MainPage.xaml", und fügen Sie das folgende **Button**-Elemente für **TitlePanel** hinzu. Dies erfolgt im Anschluss an das **TextBlock**-Element:

		<Button Name="ButtonLogin" Click="ButtonLogin_Click" 
                        Visibility="Visible">Sign in</Button>
		
9. Drücken Sie F5, um die App auszuführen, und melden Sie sich mit Ihrem Microsoft-Konto an.

   Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.

Nun können alle von einem der registrierten Identitätsanbieter authentifizierten Benutzer auf die Tabelle *TodoItem* zugreifen. Um benutzerspezifische Daten besser zu schützen, müssen Sie zudem eine Autorisierung implementieren. Hierzu rufen Sie die Benutzer-ID eines Benutzers ab, und ermitteln damit, welche Zugriffsebene dieser Benutzer für eine bestimmte Ressource erhalten soll.

## <a name="next-steps"> </a>Nächste Schritte

Im nächsten Lernprogramm [Autorisieren von Benutzern mit Skripts] werden Sie den von Mobile Services auf Basis eines authentifizierten Benutzers bereitgestellten Benutzer-ID-Wert verwenden, um von Mobile Services zurückgegebene Daten zu filtern. Weitere Informationen zur Verwendung anderer Identitätsanbieter für die Authentifizierung finden Sie unter [Erste Schritte mit der Authentifizierung].

<!-- Anchors. -->
[Register your app for authentication and configure Mobile Services]: #register
[Restrict table permissions to authenticated users]: #permissions
[Add authentication to the app]: #add-authentication
[Next Steps]: #next-steps

<!-- Images. -->


<!-- URLs. -->
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Hinzufügen von Mobile Services zu einer vorhandenen App]: mobile-services-windows-phone-get-started-data.md
[Erste Schritte mit der Authentifizierung]: mobile-services-windows-phone-get-started-users.md
[Autorisieren von Benutzern mit Skripts]: ../mobile-services-windows-phone-authorize-users-in-scripts.md

[Azure Management Portal]: https://manage.windowsazure.com/
 

<!---HONumber=August15_HO7-->