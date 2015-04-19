<properties 
	pageTitle="Azure Notification Hubs - Benachrichtigen von Benutzern" 
	description="Erfahren Sie mehr über das Senden von sicheren Pushbenachrichtigungen in Azure. Die Codebeispiele wurden in C# mithilfe der .NET-API geschrieben." 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	services="notification-hubs" 
	editor=""/>

<tags 
	ms.service="notification-hubs" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="11/22/2014" 
	ms.author="glenga"/>

#Azure Notification Hubs - Benachrichtigen von Benutzern

<div class="dev-center-tutorial-selector sublanding"> 
    	<a href="/de-de/documentation/articles/notification-hubs-windows-dotnet-notify-users/" title="Windows Universal" class="current">Windows Universal</a><a href="/de-de/documentation/articles/notification-hubs-aspnet-backend-ios-notify-users/" title="iOS">iOS</a>
		<a href="/de-de/documentation/articles/notification-hubs-aspnet-backend-android-notify-users/" title="Android">Android</a>
</div>

Durch die Unterstützung von Pushbenachrichtigungen in Azure haben Sie Zugriff auf eine einfache, plattformübergreifende und skalierbare Infrastruktur, die die Verarbeitung von Pushbenachrichtigungen sowohl auf Privat- als auch auf Unternehmensanwendungen für mobile Plattformen erheblich erleichtert, In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an einen bestimmten App-Benutzer auf einem bestimmten Gerät senden. Zum Authentifizieren von Clients und zum Generieren von Benachrichtigungen wird ein ASP.NET WebAPI-Back-End verwendet, wie im Thema [Registrieren von App-Back-End aus](http://msdn.microsoft.com/library/dn743807.aspx) gezeigt. Dieses Lernprogramm basiert auf dem Notification Hub, den Sie im Lernprogramm **Erste Schritte mit Notification Hubs** erstellt haben.

Dieses Lernprogramm ist außerdem die Voraussetzung für das Lernprogramm **Sichere Pushbenachrichtigungen**. Nachdem Sie die Schritte im Lernprogramm **Benachrichtigen von Benutzern** abgeschlossen haben, können Sie mit dem Lernprogramm **Sichere Pushbenachrichtigungen** fortfahren, in dem Sie erfahren, wie Sie den Code aus dem Lernprogramm **Benachrichtigen von Benutzern** ändern, um eine Pushbenachrichtigung auf sichere Weise zu senden. 

> [AZURE.NOTE] Bei diesem Lernprogramm wird davon ausgegangen, dass Sie Ihren Notification Hub wie unter [Erste Schritte mit Notification Hubs (Windows Store)](http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/) beschrieben erstellt und konfiguriert haben.
> Wenn Sie Mobile Services als Ihren Back-End-Dienst verwenden, finden Sie weitere Informationen in diesem Lernprogramm unter [Mobile Services-Version](mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users.md).
>Beachten Sie außerdem, dass Sie in diesem Lernprogramm eine Windows Phone Store 8.1-App erstellen. Der gleiche Code kann für Windows Store- und Windows Universal-Apps verwendet werden. Für alle diese Apps müssen die Anmeldeinformationen von Windows (nicht Windows Phone) verwendet werden.

## Erstellen und Konfigurieren des Notification Hubs

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie einen Anwendungsnamen reservieren und anschließend einen Azure Notification Hub erstellen und diesen mit der Anwendung verbinden. Führen Sie die Schritte im Thema [Erste Schritte mit Notification Hubs (Windows Store)](http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/) durch, insbesondere die Abschnitte [Registrieren Ihrer App für den Windows Store](http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#register) und [Konfigurieren Ihres Notification Hub](http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub). Vergewissern Sie sich insbesondere, dass Sie die Werte für **Paket-SID** und **Geheimer Clientschlüssel** im Portal auf der Registerkarte **Konfigurieren** für den Notification Hub eingegeben haben. Dieses Konfigurationsverfahren wird im Abschnitt [Konfigurieren Ihres Notification Hub](http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub) beschrieben. Dies ist ein wichtiger Schritt: Wenn die Anmeldeinformationen im Portal nicht mit denen für den gewählten App-Namen übereinstimmen, kann die Pushbenachrichtigung nicht gesendet werden.

[AZURE.INCLUDE [notification-hubs-aspnet-backend-notifyusers](../includes/notification-hubs-aspnet-backend-notifyusers.md)]

## Erstellen des Windows Phone-Projekts

Im nächsten Schritt erstellen Sie die Windows Phone-Anwendung. Gehen Sie folgendermaßen vor, um dieses Projekt der aktuellen Lösung hinzuzufügen:

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Knoten der obersten Ebene der Projektmappe (in diesem Fall **Solution NotifyUsers**). Klicken Sie dann auf **Hinzufügen** und auf **Neues Projekt**.

2. Erweitern Sie **Store-Apps**, und klicken Sie anschließend auf **Windows Phone-Apps**, dann auf **Leere App (Windows Phone)**.

	![][9]

3. Geben Sie in das Feld **Name** den Text **NotifyUserWindowsPhone** ein, und klicken Sie dann auf **OK**, um das Projekt zu generieren.

 
4. Verknüpfen Sie diese Anwendung mit dem Windows Phone Store: Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **NotifyUserWindowsPhone (Windows Phone 8.1)**, und klicken Sie dann auf **Store** und auf **App mit Store verknüpfen...**.

	![][10]
 
5. Führen Sie die Schritte im Assistenten aus, um sich anzumelden und die App dem Store zuzuordnen.

	![][11]
	
	> [AZURE.NOTE] Notieren Sie sich den Namen der Anwendung, den Sie während des Verfahrens auswählen. Sie müssen den Notification Hub im Portal mit den Anmeldeinformationen konfigurieren, die Sie vom [Windows Dev Center](http://go.microsoft.com/fwlink/p/?linkid=266582&clcid=0x409) für diesen spezifischen reservierten App-Namen erhalten. Dieses Konfigurationsverfahren wird unter [Konfigurieren Ihres Notification Hub](http://azure.microsoft.com/documentation/articles/notification-hubs-windows-store-dotnet-get-started/#configure-hub) beschrieben. Dies ist ein wichtiger Schritt: Wenn die Anmeldeinformationen im Portal nicht mit denen für den gewählten App-Namen übereinstimmen, kann die Pushbenachrichtigung nicht gesendet werden.

6. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **NotifyUserWindowsPhone (Windows Phone 8.1)**, und klicken Sie dann auf **NuGet-Pakete verwalten**.

7. Klicken Sie auf der linken Seite auf **Online**.

8. Geben Sie in das Suchfeld die Zeichenfolge **HTTP-Client** ein.

9. Klicken Sie in der Ergebnisliste auf **Microsoft HTTP-Clientbibliotheken** und dann auf **Installieren**. Schließen Sie die Installation ab.

10. Geben Sie in das **Suchfeld** die Zeichenfolge **Json.net** ein. Installieren Sie das **Json.NET**-Paket, und schließen Sie dann das Fenster des NuGet-Paket-Managers.

11. Doppelklicken Sie im Projektmappen-Explorer im Projekt **NotifyUserWindowsPhone (Windows Phone 8.1)** auf **MainPage.xaml**, um die Datei im Visual Studio-Editor zu öffnen.

12. Ersetzen Sie im XML-Code von **MainPage.xaml** den Abschnitt <Grid> durch den folgenden Code:

		<Grid>
	        <Grid.RowDefinitions>
	            <RowDefinition Height="Auto"/>
	            <RowDefinition Height="*"/>
	        </Grid.RowDefinitions>

	        <TextBlock Grid.Row="0" Text="Secure Push" HorizontalAlignment="Center" FontSize="48"/>

        	<StackPanel Grid.Row="1" VerticalAlignment="Center">
        	    <Grid>
        	        <Grid.RowDefinitions>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="Auto"/>
        	            <RowDefinition Height="*"/>
        	        </Grid.RowDefinitions>
            	    <TextBlock Grid.Row="0" Text="Username" FontSize="24" Margin="20,0,20,0"/>
            	    <TextBox Name="UsernameTextBox" Grid.Row="1" Margin="20,0,20,0"/>
            	    <TextBlock Grid.Row="2" Text="Password" FontSize="24" Margin="20,0,20,0" />
            	    <PasswordBox Name="PasswordTextBox" Grid.Row="3" Margin="20,0,20,0"/>
	
            	    <Button Grid.Row="4" HorizontalAlignment="Center" VerticalAlignment="Center" Content="1. Login and register" Click="LoginAndRegisterClick" />

            	    <Button Grid.Row="5" HorizontalAlignment="Center" VerticalAlignment="Center" Content="2. Send push" Click="PushClick" />
            	</Grid>
        	</StackPanel>
    	</Grid>


13. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **NotifyUserWindowsPhone (Windows Phone 8.1)**, und klicken Sie dann auf **Hinzufügen** und auf **Klasse**. Nennen Sie die Klasse **RegisterClient.cs**, und klicken Sie dann auf **OK**, um die Klasse zu generieren. Diese Komponente implementiert die REST-Aufrufe, die für das Kontaktieren des App-Back-Ends erforderlich sind, um sich für Pushbenachrichtigungen zu registrieren. Außerdem werden die vom Notification Hub erstellten  *registrationIds* lokal gespeichert, wie unter [Registrierung vom App-Back-End aus](http://msdn.microsoft.com/library/dn743807.aspx) beschrieben. Beachten Sie, dass ein Authentifizierungstoken aus dem lokalen Speicher verwendet wird, wenn Sie auf die Schaltfläche **Anmelden und Registrieren** klicken.

14. Fügen Sie innerhalb der `RegisterClient`-Klassendefinition den folgenden Code hinzu. Ersetzen Sie  `{backend endpoint}` durch den Back-End-Endpunkt, den Sie im vorherigen Abschnitt erhalten haben:

		private string POST_URL = "{backend endpoint}/api/register";

        private class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
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


15. Fügen Sie die folgenden  `using`-Anweisungen am Anfang der Datei "RegisterClient.cs" hinzu:

		using Windows.Storage;
		using System.Net;
		using System.Net.Http;
		using System.Net.Http.Headers;
		using Newtonsoft.Json;
		
16. Fügen Sie einen Code für Schaltflächen in MainPage.xaml.cs hinzu. Der Rückruf für **Log in und register** speichert das Basisauthentifizierungstoken im lokalen Speicher (dies kann ein beliebiges von Ihrem Authentifizierungsschema verwendetes Token sein) und verwendet dann  `RegisterClient`, um das Back-End aufzurufen. Der Rückruf für **AppBackend** ruft das Back-End auf, um eine sichere Benachrichtigung an alle Geräte dieses Benutzers auszulösen. 

	Fügen Sie der Datei "MainPage.xaml.cs" nach der  `OnNavigatedTo()`-Methode den folgenden Code hinzu. Ersetzen Sie  `{backend endpoint}` durch den Back-End-Endpunkt, den Sie im vorherigen Abschnitt erhalten haben:

		private async void PushClick(object sender, RoutedEventArgs e)
        {
            var POST_URL = "{backend endpoint}/api/notifications";

            using (var httpClient = new HttpClient())
            {
                var settings = ApplicationData.Current.LocalSettings.Values;
                httpClient.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Basic", (string) settings["AuthenticationToken"]);

                await httpClient.PostAsync(POST_URL, new StringContent(""));
            }
        }

        private async void LoginAndRegisterClick(object sender, RoutedEventArgs e)
        {
            SetAuthenticationTokenInLocalStorage();
            
            var channel = await PushNotificationChannelManager.CreatePushNotificationChannelForApplicationAsync();
            await new RegisterClient().RegisterAsync(channel.Uri, new string[] { "myTag" });

            var dialog = new MessageDialog("Registered as: " + UsernameTextBox.Text);
            dialog.Commands.Add(new UICommand("OK"));
            await dialog.ShowAsync();
        }

        private void SetAuthenticationTokenInLocalStorage()
        {
            string username = UsernameTextBox.Text;
            string password = PasswordTextBox.Password;
            
            var token = Convert.ToBase64String(System.Text.Encoding.UTF8.GetBytes(username + ":" + password));
            ApplicationData.Current.LocalSettings.Values["AuthenticationToken"] = token;
        }


17. Fügen Sie die folgenden  `using`-Anweisungen am Anfang der "MainPage.xaml.cs" hinzu:

		using System.Net.Http;
		using Windows.Storage;
		using System.Net.Http.Headers;
		using Windows.Networking.PushNotifications;
		using Windows.UI.Popups;

## Ausführen der Anwendung

Gehen Sie zum Ausführen der Anwendung folgendermaßen vor:

1. Führen Sie in Visual Studio die Windows Phone-App **NotifyUserWindowsPhone (Windows Phone 8.1)** aus. Der Windows Phone-Emulator wird ausgeführt und lädt die App automatisch.

2. Geben Sie in der Benutzeroberfläche der **NotifyUserWindowsPhone**-App einen Benutzernamen und das Kennwort ein. Dies kann eine beliebige Zeichenfolge sein, beide müssen jedoch denselben Wert haben.

3. Klicken Sie in der Benutzeroberfläche der **NotifyUserWindowsPhone**-App auf **Anmelden und Registrieren**. Klicken Sie anschließend auf **Pushbenachrichtigung senden**.


[9]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push9.png
[10]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push10.png
[11]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push11.png
[12]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push12.png
[13]: ./media/notification-hubs-aspnet-backend-windows-dotnet-notify-users/notification-hubs-secure-push13.png

<!--HONumber=45--> 
