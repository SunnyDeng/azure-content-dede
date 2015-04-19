## Erstellen des WebAPI-Projekts

Gehen Sie wie unten beschrieben vor, um ein neues ASP.NET WebAPI-Back-End zu erstellen, Clients zu authentifizierungen und Benachrichtigugnen zu generieren, oder ändern Sie ein aus früheren Projekten oder dem Lernprogramm [Senden von Pushbenachrichtigungen an authentifizierte Benutzer](http://azure.microsoft.com/documentation/articles/mobile-services-dotnet-backend-ios-push-notifications-app-users/) vorhandenes Back-End.

> [AZURE.NOTE] **Wichtig**: Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie sicherstellen, dass Sie die neueste Version des NuGet-Paket-Manager installiert haben. Um dies zu überprüfen, starten Sie Visual Studio. Klicken Sie im Menü **Extras** auf **Erweiterungen und Updates**. Suchen Sie nach **NuGet-Paket-Manager für Visual Studio 2013**, und vergewissern Sie sich, dass Sie Version 2.8.50313.46 oder höher installiert haben. Falls dies nicht der Fall ist, installieren Sie den NuGet-Paket-Manager neu.
> 
> ![][4]

> [AZURE.NOTE] Stellen Sie sicher, dass Sie Visual Studio [Azure SDK](http://azure.microsoft.com/downloads/) für die Websitebereitstellung installiert haben.

1. Starten Sie Visual Studio oder Visual Studio Express.
2. Klicken Sie in Visual Studio auf **Datei**, klicken Sie dann auf **Neu**, dann auf **Projekt**, erweitern Sie **Vorlagen**, **Visual C#**, klicken Sie dann auf **Web** und **ASP.NET-Webanwendung**, geben Sie den Namen **AppBackend** ein, und klicken Sie auf **OK**. 
	
	![][1]

3. Klicken Sie im Dialogfeld **Neues ASP.NET-Projekt** auf **Web-API** und dann auf **OK**.

	![][2]

4. Wählen Sie im Dialogfeld **Azure-Website konfigurieren** ein Abonnement, eine Region und eine Datenbank für dieses Projekt aus. Klicken Sie anschließend auf **OK**, um das Projekt zu erstellen.

	![][5]

5. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **AppBackend**, und klicken Sie dann auf **NuGet-Pakete verwalten**.

6. Klicken Sie auf der linken Seite auf **Online**, und suchen Sie im **Suchfeld** nach **servicebus**.

7. Klicken Sie in der Ergebnisliste auf **Windows Azure Service Bus**, und klicken Sie dann auf **Installieren**. Schließen Sie die Installation ab, und schließen Sie dann das Fenster des NuGet-Paket-Managers.

	![][14]

8. Sie erstellen jetzt eine neue Klasse namens **Notifications.cs**. Wechseln Sie zum Projektmappen-Explorer, klicken Sie mit der rechten Maustaste auf den Ordner **Modelle**, klicken Sie auf **Hinzufügen** und dann auf **Klasse**. Nachdem Sie der neuen Klasse den Namen **Notifications.cs** gegeben haben, klicken Sie auf **Hinzufügen**, um die Klasse zu generieren. Dieses Modul stellt die unterschiedlichen sicheren Benachrichtigungen dar, die gesendet werden. In einer vollständigen Implementierung werden die Benachrichtigungen in einer Datenbank gespeichert. Der Einfachheit halber wird sie in diesem Lernprogramm im Arbeitsspeicher gespeichert.

	![][6]

9. Fügen Sie die folgende  `using`-Anweisung am Anfang der Datei "Notifications.cs" hinzu:

        using Microsoft.ServiceBus.Notifications;

10. Ersetzen Sie dann die Definition der Klasse `Notifications` durch den folgenden Code, und ersetzen Sie unbedingt die beiden Platzhalter durch die Verbindungszeichenfolge (mit Vollzugriff) bzw. dem Hub-Namen (der im [Azure-Verwaltungsportal](http://manage.windowsazure.com) verfügbar ist):

		public class Notifications
        {
            public static Notifications Instance = new Notifications();
        
            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");
            }
        }

11. Anschließend erstellen wir eine neue Klasse namens **AuthenticationTestHandler.cs**. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **AppBackend**, klicken Sie auf **Hinzufügen** und anschließend auf **Klasse**. Geben Sie der neuen Klasse den Namen **AuthenticationTestHandler.cs**, und klicken Sie auf **Hinzufügen**, um die Klasse zu generieren. Diese Klasse wird zur Authentifizierung von Benutzern unter Verwendung der *Basic Authentication* verwendet. Ihre App kann ein beliebiges Authentifizierungsschema verwenden.

12. Fügen Sie in der Datei "AuthenticationTestHandler.cs" die folgenden `using`-Anweisungen hinzu:

        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Threading;
        using System.Text;
        using System.Security.Principal;
        using System.Net;

13. Ersetzen Sie in der Datei "AuthenticationTestHandler.cs" die Definition der `AuthenticationTestHandler`-Klasse durch folgenden Code:

		public class AuthenticationTestHandler : DelegatingHandler
	    {
	        protected override Task<HttpResponseMessage> SendAsync(
	        HttpRequestMessage request, CancellationToken cancellationToken)
	        {
	            var authorizationHeader = request.Headers.GetValues("Authorization").First();
	
	            if (authorizationHeader != null && authorizationHeader
	                .StartsWith("Basic ", StringComparison.InvariantCultureIgnoreCase))
	            {
	                string authorizationUserAndPwdBase64 =
	                    authorizationHeader.Substring("Basic ".Length);
	                string authorizationUserAndPwd = Encoding.Default
	                    .GetString(Convert.FromBase64String(authorizationUserAndPwdBase64));
	                string user = authorizationUserAndPwd.Split(':')[0];
	                string password = authorizationUserAndPwd.Split(':')[1];
	
	                if (verifyUserAndPwd(user, password))
	                {
	                    // Attach the new principal object to the current HttpContext object
	                    HttpContext.Current.User =
	                        new GenericPrincipal(new GenericIdentity(user), new string[0]);
	                    System.Threading.Thread.CurrentPrincipal =
	                        System.Web.HttpContext.Current.User;
	                }
	                else return Unauthorised();
	            }
	            else return Unauthorised();
	
	            return base.SendAsync(request, cancellationToken);
	        }
	
	        private bool verifyUserAndPwd(string user, string password)
	        {
	            // This is not a real authentication scheme.
	            return user == password;
	        }
	
	        private Task<HttpResponseMessage> Unauthorised()
	        {
	            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
	            var tsc = new TaskCompletionSource<HttpResponseMessage>();
	            tsc.SetResult(response);
	            return tsc.Task;
	        }
	    }

	> [AZURE.NOTE] **Sicherheitshinweis**: Die `AuthenticationTestHandler`-Klasse liefert keine wirkliche Authentifizierung. Sie wird nur verwendet, um eine Standardauthentifizierung zu imitieren, und ist nicht sicher. Sie müssen einen sicheren Authentifizierungsmechanismus in Ihren Produktionsanwendungen und -diensten implementieren.				

14. Fügen Sie den folgenden Code am Ende der `Register`-Methode in der **App_Start/WebApiConfig.cs**-Klasse hinzu:

		config.MessageHandlers.Add(new AuthenticationTestHandler());

15. Als Nächstes erstellen wir einen neuen Controller namens **RegisterController**. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Controller**, klicken Sie dann auf **Hinzufügen** und dann auf **Controller**. Klicken Sie auf das Element **Web-API-2-Controller -- Leer**, und klicken Sie dann auf **Hinzufügen**. Geben Sie der neuen Klasse den Namen **RegisterController**, und klicken Sie dann erneut auf **Hinzufügen**, um den Controller zu erstellen.

	![][7]

	![][8]

16. Fügen Sie in der Datei "RegiterController.cs" die folgenden `using`-Anweisungen hinzu:

        using Microsoft.ServiceBus.Notifications;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using Microsoft.ServiceBus.Messaging;
        using System.Web;

17. Fügen Sie innerhalb der `RegisterController`-Klassendefinition den folgenden Code hinzu. Beachten Sie, dass in diesem Code das Benutzertag für den Benutzer hinzugefügt wurde, der vom Handler authentifiziert wurde. Sie können auch optionale Überprüfungen hinzufügen, um zu überprüfen, dass der Benutzer die richtigen Berechtigungen besitzt, um sich für die angeforderten Tags zu registrieren.

		private NotificationHubClient hub;

        public RegisterController()
        {
            hub = Notifications.Instance.Hub;
        }

        public class DeviceRegistration
        {
            public string Platform { get; set; }
            public string Handle { get; set; }
            public string[] Tags { get; set; }
        }

        // POST api/register
        // This creates a registration id
        public async Task<string> Post(string handle = null)
        {
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
            string newRegistrationId = null;
            
            if (handle != null)
            {
                var registrations = await hub.GetRegistrationsByChannelAsync(handle, 100);

                foreach (RegistrationDescription registration in registrations)
                {
                    if (newRegistrationId == null)
                    {
                        newRegistrationId = registration.RegistrationId;
                    }
                    else
                    {
                        await hub.DeleteRegistrationAsync(registration);
                    }
                }
            }

            if (newRegistrationId == null) newRegistrationId = await hub.CreateRegistrationIdAsync();

            return newRegistrationId;
        }

        // PUT api/register/5
        // This creates or updates a registration (with provided channelURI) at the specified id
        public async Task<HttpResponseMessage> Put(string id, DeviceRegistration deviceUpdate)
        {
            RegistrationDescription registration = null;
            switch (deviceUpdate.Platform)
            {
                case "mpns":
                    registration = new MpnsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "wns":
                    registration = new WindowsRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "apns":
                    registration = new AppleRegistrationDescription(deviceUpdate.Handle);
                    break;
                case "gcm":
                    registration = new GcmRegistrationDescription(deviceUpdate.Handle);
                    break;
                default:
                    throw new HttpResponseException(HttpStatusCode.BadRequest);
            }

            registration.RegistrationId = id;
            var username = HttpContext.Current.User.Identity.Name;

            // add check if user is allowed to add these tags
            registration.Tags = new HashSet<string>(deviceUpdate.Tags);
            registration.Tags.Add("username:" + username);

            try
            {
                await hub.CreateOrUpdateRegistrationAsync(registration);
            }
            catch (MessagingException e)
            {
                ReturnGoneIfHubResponseIsGone(e);
            }

            return Request.CreateResponse(HttpStatusCode.OK);
        }

        // DELETE api/register/5
        public async Task<HttpResponseMessage> Delete(string id)
        {
            await hub.DeleteRegistrationAsync(id);
            return Request.CreateResponse(HttpStatusCode.OK);
        }

        private static void ReturnGoneIfHubResponseIsGone(MessagingException e)
        {
            var webex = e.InnerException as WebException;
            if (webex.Status == WebExceptionStatus.ProtocolError)
            {
                var response = (HttpWebResponse)webex.Response;
                if (response.StatusCode == HttpStatusCode.Gone)
                    throw new HttpRequestException(HttpStatusCode.Gone.ToString());
            }
        }

18. Erstellen Sie einen neuen Controller namens **NotificationsController** auf die gleiche Weise wie den Controller **RegisterController**. Diese Komponente stellt für Geräte eine Möglichkeit bereit, die Benachrichtigung sicher abzurufen, und für Benutzer die Möglichkeit, eine sichere Übertragung an das Gerät auszulösen. Beachten Sie, dass wenn Sie die Benachrichtigung an das Notification Hub senden, eine reine Benachrichtigung nur mit der ID der Benachrichtigung (und nicht der tatsächlichen Meldung) gesendet wird.

19. Fügen Sie in der Datei "NotificationsController.cs" die folgenden `using`-Anweisungen hinzu:

        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

20. Fügen Sie innerhalb der **NotificationsController**-Klassendefinition den folgenden Code hinzu, und kennzeichnen Sie die Codefragemente für Plattformen, die Sie nicht verwenden, als Kommentar.

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:"+user;


            // windows
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello, " + user + "</text></binding></visual></toast>";
            await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);


            // apns
            var alert = "{\"aps\":{\"alert\":\"Hello\"}}";
            await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);


            // gcm
            var notif = "{ \"data\" : {\"msg\":\"Hello\"}}";
            await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);


            return Request.CreateResponse(HttpStatusCode.OK);
        }

21. Drücken Sie die Taste **F5**, um die Anwendung auszuführen und um sicherzustellen, dass Ihre Arbeit bisher korrekt ist. Die App sollte einen Webbrowser starten und die ASP.NET-Startseite anzeigen. 

22. Nun werden wir diese App auf einer Azure-Website bereitstellen, damit von allen Geräten darauf zugegriffen werden kann. Klicken Sie mit der rechten Maustaste auf das Projekt **AppBackend**, und wählen Sie **Veröffentlichen** aus.

23. Wählen Sie die Azure-Website als Ihr Veröffentlichungsziel aus.

    ![][B15]

24. Melden Sie sich bei Ihrem Azure-Konto an, und wählen Sie ein vorhandene oder neue Website.

    ![][B16]

25. Notieren Sie sich den Wert der **Ziel-URL**-Eigenschaft auf der Registerkarte **Verbindung**. Diese URL wird später in diesem Lernprogramm als *backend endpoint* bezeichnet. Klicken Sie auf **Veröffentlichen**.

    ![][B18]


[1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG

<!--HONumber=45--> 
