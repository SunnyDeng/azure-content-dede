<properties linkid="notification-hubs-how-to-guides-howto-register-user-with-aspnet-webapi-windowsphonedotnet" urlDisplayName="Notify Windows Store app users by using Web API" pageTitle="Register the current user for push notifications by using Web API - Notification Hubs" metaKeywords="Azure registering application, Notification Hubs, Azure push notifications, push notification Windows Store app" description="Learn how to request push notification registration in a Windows Store app with Azure Notification Hubs when registeration is performed by ASP.NET Web API." metaCanonical="" services="notification-hubs" documentationCenter="" title="Register the current user for push notifications by using ASP.NET" authors="glenga" solutions="" manager="dwrede" editor="" />

<tags ms.service="notification-hubs" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Registrieren des aktuellen Benutzers für Pushbenachrichtigungen mit ASP.NET

<div class="dev-center-tutorial-selector sublanding">
    <a href="/de-de/documentation/articles/notification-hubs-windows-store-aspnet-register-user-push-notifications/" title="Windows Store C#" class="current">Windows Store C#</a><a href="/de-de/documentation/articles/notification-hubs-ios-aspnet-register-user-push-notifications/" title="iOS">iOS</a>
</div>

In diesem Artikel erfahren Sie, wie Sie Pushbenachrichtigungs-Registrierungen mit Azure Notification Hubs anfordern können, wenn die Registrierung von einer ASP.NET-Web-API durchgeführt wird. Dieses Lernprogramm baut auf dem Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs][Benachrichtigen von Benutzern mit Notification Hubs] auf. Sie müssen zuvor die Schritte in diesem Lernprogramm abgeschlossen haben, in denen der authentifizierte Mobile Service erstellt wird. Weitere Informationen zum Benachrichtigen von Benutzern finden Sie unter [Benachrichtigen von Benutzern mit Notification Hubs][Benachrichtigen von Benutzern mit Notification Hubs].

1.  Öffnen Sie in Visual Studio 2012 die Datei app.xaml.cs im Projekt, das Sie erstellt haben, als Sie das Lernprogramm [Benachrichtigen von Benutzern mit Notification Hubs][Benachrichtigen von Benutzern mit Notification Hubs] abgeschlossen haben.

2.  Suchen Sie die Methode **InitNotificationsAsync** und kommentieren Sie den Code in der Methode aus.

    Sie werden stattdessen die ASP.NET-Web-API verwenden, um sich für Benachrichtigungen zu registrieren.

3.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektnamen, und wählen Sie dann **Manage NuGet Packages** aus.

4.  Wählen Sie im linken Bereich die Kategorie **Online**, suchen Sie nach `json.net`, klicken Sie auf **Installieren** für das Paket **Json.NET**, und akzeptieren Sie die Lizenzvereinbarung.

    ![][]

    Daraufhin wird die externe Assembly Newtonsoft.Json.dll zu Ihrem Projekt hinzugefügt.

5.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Hinzufügen** und dann auf **Klasse**. Geben Sie anschließend `LocalStorageManager` ein, und klicken Sie auf **Hinzufügen**.

    ![][1]

    Daraufhin wird eine Codedatei für die Klasse **LocalStorageManager** zum Projekt hinzugefügt.

6.  Öffnen Sie die neue Projektdatei LocalStoreManager.cs, und fügen Sie die folgende **using**-Anweisung hinzu:

        using Windows.Storage;

7.  Ersetzen Sie die **LocalStorageManager**-Klassendefinition durch den folgenden Code:

        class LocalStorageManager
        {
            private static ApplicationDataContainer GetLocalStorageContainer()
            {
                if (!ApplicationData.Current.LocalSettings
                    .Containers.ContainsKey("InstallationContainer"))
                {
                    ApplicationData.Current.LocalSettings
                        .CreateContainer("InstallationContainer",                                                            
                        ApplicationDataCreateDisposition.Always);
                }
                return ApplicationData.Current.LocalSettings
                    .Containers["InstallationContainer"];
            }

            public static string GetInstallationId()
            {
                var container = GetLocalStorageContainer();
                if (!container.Values.ContainsKey("InstallationId"))
                {
                    container.Values["InstallationId"] = Guid.NewGuid().ToString();
                }
                return (string)container.Values["InstallationId"];
            }
        }

    Dieser Code erstellt und speichert eine gerätespezifische Installations-ID, die als Tag bei der Erstellung von Benachrichtigungen verwendet wird. Falls eine Installations-ID existiert, wird diese stattdessen verwendet.

8.  Öffnen Sie die Projektdatei MainPage.xaml und ersetzen Sie das **Grid**-Element durch den folgenden XAML-Code:

        <Grid Background="{StaticResource ApplicationPageBackgroundThemeBrush}">
            <Grid Margin="120, 58, 120, 80" >
                <Grid.RowDefinitions>
                    <RowDefinition />
                    <RowDefinition/>
                    <RowDefinition/>
                    <RowDefinition/>
                    <RowDefinition/>
                </Grid.RowDefinitions>
                <Grid.ColumnDefinitions>
                    <ColumnDefinition MaxWidth="200"/>
                    <ColumnDefinition/>
                </Grid.ColumnDefinitions>
                <TextBlock Grid.Row="0" Grid.Column="0" Grid.ColumnSpan="2"  
                               TextWrapping="Wrap" Text="Push To Users with Notification Hubs" 
                               FontSize="42"  VerticalAlignment="Top"/>
                <TextBlock Grid.Row="1" Grid.Column="0" Text="Installation Id:" 
                               FontSize="24" VerticalAlignment="Center" />
                <TextBlock Grid.Row="1" Grid.Column="1" Name="InstId" FontSize="24" 
                               VerticalAlignment="Center"/>
                <TextBlock Grid.Row="2" Grid.Column="0" Text="Username:" FontSize="24" 
                               VerticalAlignment="Center"/>
                <TextBox Grid.Row="2" Grid.Column="1" Name="User" FontSize="24" 
                             Width="300" Height="40" HorizontalAlignment="Left"/>
                <TextBlock Grid.Row="3" Grid.Column="0" Text="Password:" FontSize="24" 
                               VerticalAlignment="Center" />
                <PasswordBox Grid.Row="3" Grid.Column="1" Name="Password" FontSize="24" 
                                 Width="300" Height="40" HorizontalAlignment="Left"/>
                <Button Name="Login" Grid.Row="4" Grid.Column="0" Grid.ColumnSpan="2" 
                            Content="Login and Register for Push" FontSize="24" Click="Login_Click" />
            </Grid>
        </Grid>

    Dieser Code erstellt eine Benutzeroberfläche zur Eingabe von Benutzername und Kennwort sowie zur Anzeige der Installations-ID.

9.  Fügen Sie in der Datei MainPage.xaml.cs die folgenden **using**-Anweisungen hinzu:

        using Newtonsoft.Json;
        using System.Net.Http;
        using System.Text;
        using Windows.Networking.PushNotifications;
        using Windows.UI.Popups;

10. Fügen Sie die folgende Definition in der **MainPage**-Klasse hinzu:

        private string registerUri = "https://<SERVICE_ROOT_URL>/api/register";

    Ersetzen Sie *`<SERVICE_ROOT_URL>`* durch die Basis-URI der Web-API, die Sie in **Benachrichtigen von Benutzern mit Notification Hubs** erstellt haben.

11. Fügen Sie die folgende Methode zur **MainPage**-Klasse hinzu:

        private async void Login_Click(object sender, RoutedEventArgs e)
        {
            // Get the info that we need to request registration.
            var installationId = InstId.Text = LocalStorageManager.GetInstallationId();
            var channel = await PushNotificationChannelManager
                .CreatePushNotificationChannelForApplicationAsync();
            var user = User.Text;
            var pwd = Password.Password;

            // Define a registration to pass in the body of the POST request.
            var registration = new Dictionary<string, string>()
                                   {{"platform", "windows"},
                                   {"instId", installationId.ToString()},
                                   {"channelUri", channel.Uri}};

            // Create a client to send the HTTP registration request.
            var client = new HttpClient();
            var request =
                new HttpRequestMessage(HttpMethod.Post, new Uri(registerUri));

            // Add the Authorization header with the basic login credentials.
            var auth = "Basic " +
                Convert.ToBase64String(Encoding.UTF8.GetBytes(user + ":" + pwd));
            request.Headers.Add("Authorization", auth);
            request.Content = new StringContent(JsonConvert.SerializeObject(registration),
                Encoding.UTF8, "application/json");

            string message;

            try
            {
                // Send the registration request.
                HttpResponseMessage response = await client.SendAsync(request);

                // Get and display the response, either the registration ID
                // or an error message.
                message = await response.Content.ReadAsStringAsync();
                message = string.Format("Registration ID: {0}", message);
            }
            catch (Exception ex)
            {
                message = ex.Message;
            }

            // Display a message dialog.
            var dialog = new MessageDialog(message);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

    Diese Methode ruft eine Installations-ID und einen Channel für Pushbenachrichtigungen ab und sendet diese zusammen mit dem Gerätetyp an die authentifizierte Web-API-Methode, die wiederum eine Registrierung in Notification Hubs erstellt. Diese Web-API wurde in [Benachrichtigen von Benutzern mit Notification Hubs][Benachrichtigen von Benutzern mit Notification Hubs] definiert.

Nun haben Sie die Client-App aktualisiert und können zum Thema [Benachrichtigen von Benutzern mit Notification Hubs][Benachrichtigen von Benutzern mit Notification Hubs] zurückkehren und den mobilen Dienst aktualisieren, um Benachrichtigungen mit Notification Hubs zu senden.

<!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /de-de/documentation/articles/notification-hubs-windows-store-aspnet-register-user-push-notifications/ "Windows Store C#"
  [iOS]: /de-de/documentation/articles/notification-hubs-ios-aspnet-register-user-push-notifications/ "iOS"
  [Benachrichtigen von Benutzern mit Notification Hubs]: /de-de/manage/services/notification-hubs/notify-users-aspnet
  []: ./media/notification-hubs-windows-store-aspnet-register-user-push-notifications/notification-hub-add-nuget-package-json.png
  [1]: ./media/notification-hubs-windows-store-aspnet-register-user-push-notifications/notification-hub-create-aspnet-class.png
