<properties
	pageTitle="Azure Notification Hubs – Benachrichtigen von Benutzern"
	description="Erfahren Sie mehr über das Senden von sicheren Pushbenachrichtigungen in Azure. Die Codebeispiele wurden in C# mithilfe der .NET-API geschrieben."
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	services="notification-hubs"
	editor=""/>

<tags
	ms.service="notification-hubs"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="10/15/2015"
	ms.author="wesmc"/>

#Azure Notification Hubs – Benachrichtigen von Benutzern

[AZURE.INCLUDE [notification-hubs-selector-aspnet-backend-notify-users](../../includes/notification-hubs-selector-aspnet-backend-notify-users.md)]


##Übersicht

Durch die Unterstützung von Pushbenachrichtigungen in Azure haben Sie Zugriff auf eine einfache, plattformübergreifende und skalierbare Infrastruktur, die die Verarbeitung von Pushbenachrichtigungen sowohl auf Privat- als auch auf Unternehmensanwendungen für mobile Plattformen erheblich erleichtert, In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an einen bestimmten App-Benutzer auf einem bestimmten Gerät senden. Ein ASP.NET WebAPI-Back-End wird verwendet, um Clients zu authentifizieren. Mit dem authentifizierten Clientbenutzer wird automatisch ein Tag vom Back-End zur Benachrichtigungsregistrierung hinzugefügt. Dieses Tag wird vom Back-End gesendet, um Benachrichtigungen für einen bestimmten Benutzer zu generieren. Weitere Informationen zum Registrieren für Benachrichtigungen mit einem App-Back-End finden Sie im Thema [Registrierung vom App-Back-End aus](http://msdn.microsoft.com/library/dn743807.aspx). Dieses Lernprogramm basiert auf dem Notification Hub und Projekt, den bzw. das Sie im Lernprogramm [Erste Schritte mit Notification Hubs] erstellt haben.

Dieses Lernprogramm ist außerdem die Voraussetzung für das Lernprogramm [Sichere Pushbenachrichtigungen]. Nachdem Sie die Schritte in diesem Lernprogramm abgeschlossen haben, können Sie mit dem Lernprogramm [Sichere Pushbenachrichtigungen] fortfahren, in dem Sie erfahren, wie Sie den Code aus diesem Lernprogramm ändern, um eine Pushbenachrichtigung auf sichere Weise zu senden.





## Voraussetzungen

Wir nehmen Ihr Feedback ernst. Wenn Sie beim Durcharbeiten dieses Themas Probleme oder Vorschläge zur Verbesserung dieses Inhalts haben, würden wir uns über Ihr Feedback unten auf der Seite freuen.

Den vollständigen Code für dieses Tutorial finden Sie [hier](https://github.com/Azure/azure-notificationhubs-samples/tree/master/dotnet/NotifyUsers) auf GitHub.



##Voraussetzungen

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie die folgenden Mobile Services-Lernprogramme abgeschlossen haben:

+ [Erste Schritte mit Notification Hubs]<br/>In diesem Lernprogramm erstellen Sie Ihren Notification Hub, reservieren den App-Namen und führen eine Registrierung zum Empfangen von Benachrichtigungen durch. In diesem Lernprogramm wird davon ausgegangen, dass Sie diese Schritte bereits durchgeführt haben. Falls nicht, führen Sie die Schritte im Thema [Erste Schritte mit Notification Hubs (Windows Store)](notification-hubs-windows-store-dotnet-get-started.md) durch, insbesondere die Abschnitte [Registrieren Ihrer App für den Windows Store](notification-hubs-windows-store-dotnet-get-started.md#register-your-app-for-the-windows-store) und [Konfigurieren Ihres Notification Hub](notification-hubs-windows-store-dotnet-get-started.md#configure-your-notification-hub). Vergewissern Sie sich insbesondere, dass Sie die Werte für **Paket-SID** und **Geheimer Clientschlüssel** im Portal auf der Registerkarte **Konfigurieren** für den Notification Hub eingegeben haben. Dieses Konfigurationsverfahren wird im Abschnitt [Konfigurieren Ihres Notification Hub](notification-hubs-windows-store-dotnet-get-started.md#configure-your-notification-hub) beschrieben. Dies ist ein wichtiger Schritt: Wenn die Anmeldeinformationen im Portal nicht mit denen für den gewählten App-Namen übereinstimmen, kann die Pushbenachrichtigung nicht gesendet werden.




> [AZURE.NOTE]Wenn Sie Mobile Services als Back-End-Dienst verwenden, finden Sie weitere Informationen in der [Mobile Services-Version](../mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md) dieses Lernprogramms.




[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Aktualisieren des Codes für das Clientprojekt

In diesem Abschnitt aktualisieren Sie den Code in dem Projekt, das Sie für das Lernprogramm [Erste Schritte mit Notification Hubs] abgeschlossen haben. Er sollte bereits dem Speicher zugeordnet und für Ihren Notification Hub konfiguriert sein. In diesem Abschnitt fügen Sie Code hinzu, um den neuen WebAPI Back-End aufzurufen und ihn zum Registrieren und Senden von Benachrichtigungen zu verwenden.

1. Öffnen Sie in Visual Studio die Lösung, die Sie für das Lernprogramm [Erste Schritte mit Notification Hubs] erstellt haben.

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **(Windows 8.1)**, und klicken Sie dann auf **NuGet-Pakete verwalten**.

3. Klicken Sie auf der linken Seite auf **Online**.

4. Geben Sie in das **Suchfeld** den Begriff **Http Client** ein.

5. Klicken Sie in der Ergebnisliste auf **Microsoft HTTP-Clientbibliotheken** und dann auf **Installieren**. Schließen Sie die Installation ab.

6. Geben Sie in das **Suchfeld** die Zeichenfolge **Json.net** ein. Installieren Sie das **Json.NET**-Paket, und schließen Sie dann das Fenster des NuGet-Paket-Managers.

7. Wiederholen Sie diese Schritte für das Projekt **(Windows Phone 8.1)**, um das NuGet-Paket **JSON.NET** für das Windows Phone-Projekt zu installieren.


8. Doppelklicken Sie im Projektmappen-Explorer im Projekt **(Windows 8.1)** auf **MainPage.xaml**, um die Datei im Visual Studio-Editor zu öffnen.

9. Ersetzen Sie im XML-Code von **MainPage.xaml** den Abschnitt `<Grid>` durch den folgenden Code: Dieser Code fügt ein Textfeld für den Benutzernamen und das Kennwort hinzu, mit denen der Benutzer authentifiziert wird. Darüber hinaus werden die Textfelder für die Benachrichtigungsmeldung und das Benutzernamentag, das die Benachrichtigung empfangen soll, hinzugefügt:

        <Grid>
            <Grid.RowDefinitions>
                <RowDefinition Height="Auto"/>
                <RowDefinition Height="*"/>
            </Grid.RowDefinitions>

            <TextBlock Grid.Row="0" Text="Notify Users" HorizontalAlignment="Center" FontSize="48"/>

            <StackPanel Grid.Row="1" VerticalAlignment="Center">
                <Grid>
                    <Grid.RowDefinitions>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                        <RowDefinition Height="Auto"/>
                    </Grid.RowDefinitions>
                    <Grid.ColumnDefinitions>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                        <ColumnDefinition></ColumnDefinition>
                    </Grid.ColumnDefinitions>
                    <TextBlock Grid.Row="0" Grid.ColumnSpan="3" Text="Username" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="UsernameTextBox" Grid.Row="1" Grid.ColumnSpan="3" Margin="20,0,20,0"/>
                    <TextBlock Grid.Row="2" Grid.ColumnSpan="3" Text="Password" FontSize="24" Margin="20,0,20,0" />
                    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Grid.ColumnSpan="3" Margin="20,0,20,0"/>

                    <Button Grid.Row="4" Grid.ColumnSpan="3" HorizontalAlignment="Center" VerticalAlignment="Center"
                                Content="1. Login and register" Click="LoginAndRegisterClick" Margin="0,0,0,20"/>

                    <ToggleButton Name="toggleWNS" Grid.Row="5" Grid.Column="0" HorizontalAlignment="Right" Content="WNS" IsChecked="True" />
                    <ToggleButton Name="toggleGCM" Grid.Row="5" Grid.Column="1" HorizontalAlignment="Center" Content="GCM" />
                    <ToggleButton Name="toggleAPNS" Grid.Row="5" Grid.Column="2" HorizontalAlignment="Left" Content="APNS" />

                    <TextBlock Grid.Row="6" Grid.ColumnSpan="3" Text="Username Tag To Send To" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="ToUserTagTextBox" Grid.Row="7" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <TextBlock Grid.Row="8" Grid.ColumnSpan="3" Text="Enter Notification Message" FontSize="24" Margin="20,0,20,0"/>
                    <TextBox Name="NotificationMessageTextBox" Grid.Row="9" Grid.ColumnSpan="3" Margin="20,0,20,0" TextWrapping="Wrap" />
                    <Button Grid.Row="10" Grid.ColumnSpan="3" HorizontalAlignment="Center" Content="2. Send push" Click="PushClick" Name="SendPushButton" />
                </Grid>
            </StackPanel>
        </Grid>


10. Öffnen Sie im Projektmappen-Explorer im Projekt **(Windows Phone 8.1)** die Datei **MainPage.xaml**, und ersetzen Sie den Windows Phone 8.1-Abschnitt `<Grid>` durch den gleichen Code wie oben. Die Oberfläche sollte etwa wie nachfolgend dargestellt aussehen.

	![][13]

11. Öffnen Sie im Projektmappen-Explorer die Datei **MainPage.xaml.cs** für die Projekte **(Windows 8.1)** und **(Windows Phone 8.1)**. Fügen Sie am Anfang der beiden Dateien die folgenden `using`-Anweisungen ein:

		using System.Net.Http;
		using Windows.Storage;
		using System.Net.Http.Headers;
		using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;
		using System.Threading.Tasks;

12. Fügen Sie in **MainPage.xaml.cs** für die Projekte **(Windows 8.1)** und **(Windows Phone 8.1)** das folgende Element der `MainPage`-Klasse hinzu. Ersetzen Sie `<Enter Your Backend Endpoint>` durch den aktuellen Back-End-Endpunkt, den Sie zuvor erhalten haben. Beispiel: `http://mybackend.azurewebsites.net`.

        private static string BACKEND_ENDPOINT = "<Enter Your Backend Endpoint>";



13. Fügen Sie den folgenden Code zur MainPage-Klasse in **MainPage.xaml.cs** für die Projekte **(Windows 8.1)** und **(Windows Phone 8.1)** hinzu.

	Die `PushClick`-Methode ist ein Klick-Handler für die Schaltfläche **Send Push**. Sie ruft den Back-End auf, um eine Benachrichtigung an alle Geräte mit einem Benutzernamentag zu senden, das dem `to_tag`-Parameter entspricht. Die Benachrichtigungsmeldung wird als JSON-Inhalt im Anforderungstext gesendet.

	Die `LoginAndRegisterClick`-Methode ist ein Klick-Handler für die Schaltfläche **Login and register**. Sie speichert das Token für die Standardauthentifizierung im lokalen Speicher (dies kann ein beliebiges von Ihrem Authentifizierungsschema verwendetes Token sein) und verwendet dann `RegisterClient` zur Registrierung für Benachrichtigungen mit dem Back-End.


        private async void PushClick(object sender, RoutedEventArgs e)
        {
            if (toggleWNS.IsChecked.Value)
            {
                await sendPush("wns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleGCM.IsChecked.Value)
            {
                await sendPush("gcm", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);
            }
            if (toggleAPNS.IsChecked.Value)
            {
                await sendPush("apns", ToUserTagTextBox.Text, this.NotificationMessageTextBox.Text);

            }
        }

        private async Task sendPush(string pns, string userTag, string message)
        {
            var POST_URL = BACKEND_ENDPOINT + "/api/notifications?pns=" +
                pns + "&to_tag=" + userTag;

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                try
                {
                    await httpClient.PostAsync(POST_URL, new StringContent(""" + message + """,
                        System.Text.Encoding.UTF8, "application/json"));
                }
                catch (Exception ex)
                {
                    MessageDialog alert = new MessageDialog(ex.Message, "Failed to send " + pns + " message");
                    alert.ShowAsync();
                }
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();

            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();

            // The "username:<user name>" tag gets automatically added by the message handler in the backend.
            // The tag passed here can be whatever other tags you may want to use.
            try
            {
                await new RegisterClient(BACKEND_ENDPOINT).RegisterAsync(channel.Uri, new string[] { "myTag" });

                var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
                dialog.Commands.Add(new UICommand("OK"));
                await dialog.ShowAsync();
                SendPushButton.IsEnabled = true;
            }
            catch (Exception ex)
            {
                MessageDialog alert = new MessageDialog(ex.Message, "Failed to register with RegisterClient");
                alert.ShowAsync();
            }
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;

            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }



14. Öffnen Sie im Projektmappen-Explorer unter dem Projekt **Shared** die Datei **App.xaml.cs**. Suchen Sie den Aufruf von `InitNotificationsAsync()` im `OnLaunched()`-Ereignishandler. Kommentieren Sie den Aufruf von `InitNotificationsAsync()` aus, oder löschen Sie ihn. Der oben hinzugefügte Schaltflächen-Handler initialisiert Benachrichtigungsregistrierungen.


        protected override void OnLaunched(LaunchActivatedEventArgs e)
        {
            //InitNotificationsAsync();


15. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **Shared**, und klicken Sie erst auf **Hinzufügen** und dann auf **Klasse**. Nennen Sie die Klasse **RegisterClient.cs**, und klicken Sie dann auf **OK**, um die Klasse zu generieren.

	Diese Klasse umschließt die REST-Aufrufe, die für das Kontaktieren des App-Back-Ends erforderlich sind, um sich für Pushbenachrichtigungen zu registrieren. Außerdem werden die vom Notification Hub erstellten *registrationIds* lokal gespeichert, wie unter [Registrierung vom App-Back-End aus](http://msdn.microsoft.com/library/dn743807.aspx) beschrieben. Beachten Sie, dass ein Authentifizierungstoken aus dem lokalen Speicher verwendet wird, wenn Sie auf die Schaltfläche **Log in and register** klicken.


16. Fügen Sie die folgenden `using`-Anweisungen am Anfang der Datei "RegisterClient.cs" hinzu:

		using Windows.Storage;
		using System.Net;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using Newtonsoft.Json;
		using System.Threading.Tasks;
		using System.Linq;

17. Fügen Sie innerhalb der `RegisterClient`-Klassendefinition den folgenden Code hinzu.

		private string POST_URL;

        private class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        public RegisterClient(string backendEndpoint)
        {
            POST_URL = backendEndpoint + "/api/register";
        }

        public async Task RegisterAsync(string handle, IEnumerable<string> tags)
        {
            var regId = await RetrieveRegistrationIdOrRequestNewOneAsync();

            var deviceRegistration = new DeviceRegistration
            {
                Platform = "wns",
                Handle = handle,
                Tags = tags.ToArray<string>()
            };

            var statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);

            if (statusCode == HttpStatusCode.Gone)
            {
                // regId is expired, deleting from local storage & recreating
                var settings = ApplicationData.Current.LocalSettings.Values;
                settings.Remove("__NHRegistrationId");
                regId = await RetrieveRegistrationIdOrRequestNewOneAsync();
                statusCode = await UpdateRegistrationAsync(regId, deviceRegistration);
            }

            if (statusCode != HttpStatusCode.Accepted)
            {
                // log or throw
            }
        }

        private async Task<HttpStatusCode> UpdateRegistrationAsync(string regId, DeviceRegistration deviceRegistration)
        {
            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                var putUri = POST_URL + "/" + regId;

                string json = JsonConvert.SerializeObject(deviceRegistration);
                                var response = await httpClient.PutAsync(putUri, new StringContent(json, Encoding.UTF8, "application/json"));
                return response.StatusCode;
            }
        }

        private async Task<string> RetrieveRegistrationIdOrRequestNewOneAsync()
        {
            var settings = ApplicationData.Current.LocalSettings.Values;
            if (!settings.ContainsKey("__NHRegistrationId"))
            {
                using (var httpClient = new HttpClient())
                {
                    httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string)settings["AuthenticationToken"]);

                    var response = await httpClient.PostAsync(POST_URL, new StringContent(""));
                    if (response.IsSuccessStatusCode)
                    {
                        string regId = await response.Content.ReadAsStringAsync();
                        regId = regId.Substring(1, regId.Length - 2);
                        settings.Add("__NHRegistrationId", regId);
                    }
                    else
                    {
                        throw new Exception();
                    }
                }
            }
            return (string)settings["__NHRegistrationId"];

        }

18. Speichern Sie alle Änderungen.


## Testen der Anwendung

1. Starten Sie die Anwendung unter Windows 8.1 und Windows Phone 8.1. Für Windows Phone 8.1 können Sie die Instanz im Emulator oder auf einem echten Gerät ausführen.

2. Geben Sie in der Windows 8.1-Instanz der App einen **Benutzernamen** und ein **Kennwort** ein, wie im folgenden Bildschirm dargestellt. Sie sollten einen anderen Benutzernamen und ein anders Kennwort als in Windows Phone eingeben.


3. Klicken Sie auf **Login and register**, und überprüfen Sie, ob ein Dialogfeld anzeigt, dass Sie sich angemeldet haben. Dadurch wird auch die Schaltfläche **Send Push** aktiviert.

    ![][14]

4. Geben Sie in der Windows Phone 8.1-Instanz eine Benutzernamenzeichenfolge in den Feldern **Username** und **Password** ein, und klicken Sie dann auf **Login and register**.
5. Geben Sie dann im Feld **Recipient Username Tag** den Benutzernamen ein, der unter Windows 8.1 registriert wurde. Geben Sie eine Benachrichtigungsmeldung ein, und klicken Sie auf **Send Push**.

    ![][16]

6. Nur die Geräte, die mit dem entsprechenden Benutzernamentag registriert wurden, erhalten eine Benachrichtigungsmeldung.

	![][15]

## Nächste Schritte

* Wenn Sie Ihre Benutzer nach Interessengruppen unterteilen möchten, lesen Sie [Verwenden von Benachrichtigungshubs zum Senden von neuesten Nachrichten].
* Weitere Informationen zur Verwendung von Notification Hubs finden Sie im [Notification Hubs-Leitfaden].



[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-ui.png
[14]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-windows-instance-username.png
[15]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-notification-received.png
[16]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-wp-send-message.png



<!-- URLs. -->
[Erste Schritte mit Notification Hubs]: notification-hubs-windows-store-dotnet-get-started.md
[Sichere Pushbenachrichtigungen]: notification-hubs-aspnet-backend-windows-dotnet-secure-push.md
[Verwenden von Benachrichtigungshubs zum Senden von neuesten Nachrichten]: notification-hubs-windows-store-dotnet-send-breaking-news.md
[Notification Hubs-Leitfaden]: http://msdn.microsoft.com/library/jj927170.aspx

<!---HONumber=Oct15_HO3-->