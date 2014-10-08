## Erstellen des WebAPI-Projekts

Sie müssen zunächst ein ASP.NET-WebAPI-Projekt erstellen. Dies ist das Backend, das zur Authentifizierung von Clients und zum Erzeugen von Benachrichtigungen verwendet wird.

> [AZURE.NOTE] **Wichtig**: Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie sicherstellen, dass Sie die neueste Version des NuGet-Paket-Manager installiert haben. Um dies zu überprüfen, starten Sie Visual Studio. Klicken Sie im Menü **Extras** auf **Erweiterungen und Updates**. Suchen Sie nach **NuGet-Paket-Manager für Visual Studio 2013**, und vergewissern Sie sich, dass Sie Version 2.8.50313.46 oder höher installiert haben. Falls dies nicht der Fall ist, installieren Sie den NuGet-Paket-Manager neu.
>
> ![][]

1.  Starten Sie Visual Studio mit erhöhten Rechten (als Administrator).
2.  Klicken Sie in Visual Studio Express auf **Datei**, dann auf **Neu** und **Projekt**, erweitern Sie **Vorlagen**, **Visual C\#**, klicken Sie dann auf **Web** und **ASP.NET-Webanwendung**, geben Sie den Namen **AppBackend** ein, und klicken Sie dann auf **OK**.

    ![][1]

3.  Klicken Sie im Dialogfeld **New ASP.NET Project** auf **Web-API** und dann auf **OK**.

    ![][2]

4.  Wählen Sie im Dialogfeld **Azure-Website konfigurieren** ein Abonnement, eine Region und eine Datenbank für dieses Projekt aus. Klicken Sie anschließend auf **OK**, um das Projekt zu erstellen.

    ![][3]

5.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **AppBackend**, und klicken Sie dann auf **NuGet-Pakete verwalten**.

6.  Klicken Sie auf der linken Seite auf **Online**.

7.  Geben Sie im Suchfeld **servicebus** ein.

8.  Klicken Sie in der Ergebnisliste auf **Windows Azure Service Bus**, und klicken Sie dann auf **Installieren**. Schließen Sie die Installation ab, und schließen Sie dann das Fenster des NuGet-Paket-Managers.

    ![][4]

9.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Modelle**, und klicken Sie dann auf **Hinzufügen** und dann auf **Klasse**. Geben Sie der neuen Klasse den Namen **Notifications.cs**. Klicken Sie auf **Hinzufügen**, um die Klasse zu erstellen. Dieses Modul stellt die unterschiedlichen sicheren Benachrichtigungen dar, die gesendet werden. In einer vollständigen Implementierung werden die Benachrichtigungen in einer Datenbank gespeichert. In diesem Fall werden sie der Einfachheit halber im Speicher gespeichert.

    ![][5]

10. Fügen Sie der Datei "Notifications.cs" hinzu, und ersetzen Sie dabei die `Notifications`-Klassendefinition durch Folgendes:

        public class Notifications
        {
            public static Notifications Instance = new Notifications();

            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");
            }
        }

11. Fügen Sie die folgende `using`-Anweisung am Anfang der Datei hinzu:

        using Microsoft.ServiceBus.Notifications;

12. Ersetzen Sie in der `Notifications()`-Methode die beiden Platzhalter in der folgenden Codezeile durch die Verbindungszeichenfolge (mit vollständigen Zugriff) für Ihr Notification Hub und den Hub-Namen. Diese Information erhalten Sie im [Azure-Verwaltungsportal][]:

        Hub = NotificationHubClient.CreateClientFromConnectionString("{conn string with full access}", "{hub name}");

13. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **AppBackend**, und klicken Sie dann auf **Hinzufügen** und dann auf **Klasse**. Geben Sie der neuen Klasse den Namen **AuthenticationTestHandler.cs**. Klicken Sie auf **Hinzufügen**, um die Klasse zu erstellen. Diese Klasse wird zur Authentifizierung von Benutzern unter Verwendung der *einfachen Authentifizierung* verwendet. Ihre App kann ein beliebiges Authentifizierungsschema verwenden.

14. Fügen Sie Code zu AuthenticationTestHandler.cs hinzu, und ersetzen Sie dabei die `AuthenticationTestHandler`-Klassendefinition durch Folgendes:

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

    > [AZURE.NOTE] **Sicherheitshinweis**: Die `AuthenticationTestHandler`-Klasse liefert keine wirkliche Authentifizierung. Sie wird verwendet, um eine grundlegende Authentifizierung zu imitieren und ein Prinzip zurückzugeben. Der Benutzername ist erforderlich, um eine Notification Hub-Registrierung zu erstellen. Die obige Implementierung ist nicht sicher. Sie müssen einen sicheren Authentifizierungsmechanismus in Ihren Produktionsanwendungen und -diensten implementieren.

15. Fügen Sie die folgenden `using`-Anweisungen am Anfang der Datei AuthenticationTestHandler.cs hinzu:

        using System.Net.Http;
        using System.Threading.Tasks;
        using System.Threading;
        using System.Text;
        using System.Security.Principal;
        using System.Net;               

16. Fügen Sie den folgenden Code am Ende der `Register`-Methode in der **App\_Start/WebApiConfig.cs**-Klasse hinzu:

        config.MessageHandlers.Add(new AuthenticationTestHandler());

17. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Controller**, klicken Sie dann auf **Hinzufügen** und dann auf **Controller**. Klicken Sie auf das Element **Web-API-2-Controller -- Leer**, und klicken Sie dann auf **Hinzufügen**.

    ![][6]

18. Geben Sie der neuen Klasse den Namen **RegisterController**, und klicken Sie dann erneut auf **Hinzufügen**, um den Controller zu erstellen.

    ![][7]

19. Fügen Sie innerhalb der `RegisterController`-Klassendefinition den folgenden Code hinzu:

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

20. Fügen Sie die folgenden `using`-Anweisungen am Anfang der Datei RegisterController.cs hinzu:

        using Microsoft.ServiceBus.Notifications;
        using SecurePush.Models;
        using System.Threading.Tasks;
        using Microsoft.ServiceBus.Messaging;
        using System.Web;

21. Beachten Sie, dass in diesem Code das Benutzertag für den Benutzer hinzugefügt wurde, der vom Handler authentifiziert wurde. Sie können auch optionale Überprüfungen hinzufügen, um zu überprüfen, dass der Benutzer die richtigen Berechtigungen besitzt, um sich für die angeforderten Tags zu registrieren.

22. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Controller**, klicken Sie dann auf **Hinzufügen** und dann auf **Controller**. Klicken Sie auf das Element **Web-API-2-Controller -- Leer**, und klicken Sie dann auf **Hinzufügen**. Geben Sie der neuen Klasse den Namen **NotificationsController**, und klicken Sie dann erneut auf **Hinzufügen**, um den Controller zu erstellen. Durch diese Komponente wird für das Gerät eine Möglichkeit zum sicheren Abrufen der Benachrichtigung implementiert und der Benutzer hat außerdem die Möglichkeit (zu Lernzwecken in diesem Lernprogramm), eine sichere Pushbenachrichtigung an seine Geräte auszulösen. Beachten Sie, dass wenn Sie die Benachrichtigung an das Notification Hub senden, eine reine Benachrichtigung nur mit der ID der Benachrichtigung (und nicht der tatsächlichen Meldung) gesendet wird.

23. Fügen Sie innerhalb der **NotificationsController**-Klassendefinition den folgenden Code hinzu:

        public async Task<HttpResponseMessage> Post()
        {
            var user = HttpContext.Current.User.Identity.Name;
            var userTag = "username:"+user;
            var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">Hello, " + user + "</text></binding></visual></toast>";
            await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);

            return Request.CreateResponse(HttpStatusCode.OK);

        }

24. Fügen Sie die folgenden `using`-Anweisungen am Anfang der Datei NotificationsController.cs hinzu:

        using SecurePush.Models;
        using System.Threading.Tasks;
        using System.Web;

25. Drücken Sie **F5**, um die Anwendung auszuführen und um sicherzustellen, dass Ihre Arbeit bisher korrekt ist. Die App sollte einen Webbrowser starten und die ASP.NET-Startseite anzeigen.

26. Nun werden wir diese App auf einer Azure-Website bereitstellen, damit von allen Geräten darauf zugegriffen werden kann. Klicken Sie mit der rechten Maustaste auf das Projekt **AppBackend**, und wählen Sie **Veröffentlichen** aus.

27. Wählen Sie die Azure-Website als Ihr Veröffentlichungsziel aus.

    ![][8]

28. Melden Sie sich bei Ihrem Azure-Konto an, und wählen Sie ein vorhandene oder neue Website.

    ![][9]

29. Notieren Sie sich die **Ziel-URL**-Eigenschaft auf der Registerkarte **Verbindung** tab. Diese URL wird später in diesem Lernprogramm als *Back-End-Endpunkt* bezeichnet. Klicken Sie auf **Veröffentlichen**.

    ![][10]

  []: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
  [1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
  [2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
  [3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
  [4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
  [5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
  [Azure-Verwaltungsportal]: http://manage.windowsazure.com
  [6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
  [7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
  [8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
  [9]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
  [10]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG
