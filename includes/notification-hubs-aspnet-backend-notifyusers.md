## Erstellen des WebAPI-Projekts

In den folgenden Abschnitten wird das neue ASP.NET WebAPI-Back-End mit den folgenden drei Hauptzielen erstellt:

1. **Authentifizieren von Clients**: Ein Meldungshandler wird später zum Authentifizieren von Clientanforderungen und zum Zuordnen des Benutzers zu den Anforderungen hinzugefügt.
2. **Registrierungen für Clientbenachrichtigungen**: Später fügen Sie einen Controller hinzu, um neue Registrierungen für ein Clientgerät zum Empfangen von Benachrichtigungen zu verarbeiten. Der authentifizierte Benutzername wird automatisch als [Tag](https://msdn.microsoft.com/library/azure/dn530749.aspx) der Registrierung hinzugefügt.
3. **Senden von Benachrichtigungen an Clients**: Später fügen Sie zudem einen Controller hinzu, um Benutzern die Möglichkeit zu bieten, eine sichere Pushbenachrichtigung an Geräte und Clients auszulösen, die mit dem Tag verknüpft sind. 

In den folgenden Schritten erfahren Sie, wie das neue ASP.NET WebAPI-Back-End erstellt wird:


> [AZURE.NOTE] **Wichtig:** Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie sicherstellen, dass Sie die neueste Version des NuGet-Paket-Managers installiert haben. Um dies zu überprüfen, starten Sie Visual Studio. Klicken Sie im Menü **Extras** auf **Erweiterungen und Updates**. Suchen Sie nach **NuGet-Paket-Manager für Visual Studio 2013**, und vergewissern Sie sich, dass Sie Version 2.8.50313.46 oder höher installiert haben. Falls dies nicht der Fall ist, installieren Sie den NuGet-Paket-Manager neu.
> 
> ![][B4]

> [AZURE.NOTE] Stellen Sie sicher, dass Sie das Visual Studio [Azure SDK](https://azure.microsoft.com/downloads/) für die Websitebereitstellung installiert haben.

1. Starten Sie Visual Studio oder Visual Studio Express. Klicken Sie auf **Server-Explorer**, und melden Sie sich bei Ihrem Azure-Konto an. Visual Studio benötigt Ihre Anmeldung, um die Websiteressourcen für Ihr Konto zu erstellen.
2. Klicken Sie in Visual Studio auf **Datei**, dann auf **Neu** und **Projekt**, erweitern Sie **Vorlagen** und **Visual C#**, und klicken Sie dann auf **Web** und **ASP.NET-Webanwendung**. Geben Sie den Namen **AppBackend** ein, und klicken Sie dann auf **OK**. 
	
	![][B1]

3. Klicken Sie im Dialogfeld **New ASP.NET Project** auf **Web-API** und dann auf **OK**.

	![][B2]

4. Wählen Sie im Dialogfeld **Configure Microsoft Azure Web App** ein Abonnement und einen bereits erstellten **App Service-Plan** aus. Sie können auch **Einen neuen App Services-Plan erstellen** auswählen und im Dialogfeld einen neuen Plan erstellen. Sie benötigen für dieses Lernprogramm keine Datenbank. Nachdem Sie Ihren App Services-Plan ausgewählt haben, klicken Sie zum Erstellen des Projekts auf **OK**.

	![][B5]



## Authentifizieren von Clients beim WebAPI-Back-End

In diesem Abschnitt erstellen Sie eine neue Meldungshandlerklasse mit dem Namen **AuthenticationTestHandler** für das neue Back-End. Diese Klasse wird von [DelegatingHandler](https://msdn.microsoft.com/library/system.net.http.delegatinghandler.aspx) abgeleitet und als Meldungshandler hinzugefügt, damit alle Anforderungen an das Back-End verarbeitet werden können.



1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **AppBackend**, und klicken Sie auf **Hinzufügen** und anschließend auf **Klasse**. Geben Sie der neuen Klasse den Namen **AuthenticationTestHandler.cs**, und klicken Sie auf **Hinzufügen**, um die Klasse zu generieren. Mit dieser Klasse werden Benutzer der Einfachheit halber unter Verwendung der *Standardauthentifizierung* authentifiziert. Ihre App kann ein beliebiges Authentifizierungsschema verwenden.

2. Fügen Sie in der Datei "AuthenticationTestHandler.cs" die folgenden `using`-Anweisungen hinzu:

        using System.Net.Http;
        using System.Threading;
        using System.Security.Principal;
        using System.Net;
        using System.Web;

3. Ersetzen Sie in der Datei "AuthenticationTestHandler.cs" die Definition der `AuthenticationTestHandler`-Klasse durch den folgenden Code.

	Dieser Handler autorisiert die Anforderung, wenn die folgenden drei Bedingungen erfüllt sind: * Die Anforderung enthält einen *Autorisierungsheader*. * Die Anforderung verwendet die *Standardauthentifizierung*. * Die Benutzernamen-Zeichenfolge und die Kennwortzeichenfolge sind die gleiche Zeichenfolge.

	Andernfalls wird die Anforderung abgelehnt. Dies ist keine sinnvolle Vorgehensweise zur Authentifizierung und Autorisierung. Es handelt sich um ein sehr einfaches Beispiel für dieses Lernprogramm.

	Wenn die Anforderungsnachricht von `AuthenticationTestHandler` authentifiziert und autorisiert ist, wird der Benutzer der Standardauthentifizierung an die aktuelle Anforderung im [HttpContext](https://msdn.microsoft.com/library/system.web.httpcontext.current.aspx) angefügt. Die Benutzerinformationen im HttpContext werden von einem anderen Controller (RegisterController) später verwendet, um der Registrierungsanforderung für die Benachrichtigung ein [Tag](https://msdn.microsoft.com/library/azure/dn530749.aspx) hinzuzufügen.

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
	                else return Unauthorized();
	            }
	            else return Unauthorized();
	
	            return base.SendAsync(request, cancellationToken);
	        }
	
	        private bool verifyUserAndPwd(string user, string password)
	        {
	            // This is not a real authentication scheme.
	            return user == password;
	        }
	
	        private Task<HttpResponseMessage> Unauthorized()
	        {
	            var response = new HttpResponseMessage(HttpStatusCode.Forbidden);
	            var tsc = new TaskCompletionSource<HttpResponseMessage>();
	            tsc.SetResult(response);
	            return tsc.Task;
	        }
	    }

	> [AZURE.NOTE] **Sicherheitshinweis**: Die `AuthenticationTestHandler`-Klasse liefert keine wirkliche Authentifizierung. Sie wird nur verwendet, um eine Standardauthentifizierung zu imitieren, und ist nicht sicher. Sie müssen einen sicheren Authentifizierungsmechanismus in Ihren Produktionsanwendungen und -diensten implementieren.

4. Fügen Sie den folgenden Code am Ende der `Register`-Methode in der **App\_Start/WebApiConfig.cs**-Klasse hinzu, um den Meldungshandler zu registrieren:

		config.MessageHandlers.Add(new AuthenticationTestHandler());

5. Speichern Sie die Änderungen.

## Registrieren für Benachrichtigungen unter Verwendung des WebAPI-Back-Ends

In diesem Abschnitt wird dem WebAPI-Back-End ein neuer Controller hinzugefügt, um Anforderungen zum Registrieren eines Benutzers und eines Geräts für Benachrichtigungen mit der Clientbibliothek für Notification Hubs zu registrieren. Der Controller fügt eine Benutzermarkierung für den Benutzer hinzu, der mit `AuthenticationTestHandler` authentifiziert und an den HttpContext angefügt wurde. Die Markierung hat das Zeichenfolgenformat `"username:<actual username>"`.


 

1. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf das Projekt **AppBackend**, und klicken Sie dann auf NuGet-Pakete verwalten.

2. Klicken Sie auf der linken Seite auf **Online**, und suchen Sie im **Suchfeld** nach **Microsoft.Azure.NotificationHubs**.

3. Klicken Sie in der Ergebnisliste auf **Microsoft Azure Notification Hubs** und dann auf **Installieren**. Schließen Sie die Installation ab, und schließen Sie dann das Fenster des NuGet-Paket-Managers.

	Dies fügt mithilfe des <a href="http://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/">Microsoft.Azure.Notification Hubs-NuGet-Pakets</a> einen Verweis auf das Azure Notification Hubs-SDK hinzu.

4. Wir erstellen nun eine neue Klassendatei, in der die unterschiedlichen sicheren Benachrichtigungen angegeben sind, die gesendet werden. In einer vollständigen Implementierung werden die Benachrichtigungen in einer Datenbank gespeichert. Der Einfachheit halber wird sie in diesem Lernprogramm im Arbeitsspeicher gespeichert. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Modelle**, und klicken Sie dann auf **Hinzufügen** und anschließend auf **Klasse**. Geben Sie der neuen Klasse den Namen **Notifications.cs**, und klicken Sie dann auf **Hinzufügen**, um die Klasse zu generieren.

	![][B6]

5. Fügen Sie die folgende `using`-Anweisung am Anfang der Datei "Notifications.cs" hinzu:

        using Microsoft.Azure.NotificationHubs;

6. Ersetzen Sie dann die Definition der `Notifications`-Klasse durch den folgenden Code sowie die beiden Platzhalter durch die Verbindungszeichenfolge (mit Vollzugriff) für Ihren Notification Hub und durch den Namen des Hubs (der im [klassischen Azure-Portal](http://manage.windowsazure.com) verfügbar ist):

		public class Notifications
        {
            public static Notifications Instance = new Notifications();
        
            public NotificationHubClient Hub { get; set; }

            private Notifications() {
                Hub = NotificationHubClient.CreateClientFromConnectionString("<your hub's DefaultFullSharedAccessSignature>", 
																			 "<hub name>");
            }
        }



7. Als Nächstes wird der neue Controller **RegisterController** erstellt. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Controller**, klicken Sie dann auf **Hinzufügen** und dann auf **Controller**. Klicken Sie auf das Element **Web-API-2-Controller -- Leer**, und klicken Sie dann auf **Hinzufügen**. Geben Sie der neuen Klasse den Namen **RegisterController**, und klicken Sie dann erneut auf **Hinzufügen**, um den Controller zu erstellen.

	![][B7]

	![][B8]

8. Fügen Sie in der Datei "RegisterController.cs" die folgenden `using`-Anweisungen hinzu:

        using Microsoft.Azure.NotificationHubs;
		using Microsoft.Azure.NotificationHubs.Messaging;
        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

9. Fügen Sie innerhalb der `RegisterController`-Klassendefinition den folgenden Code hinzu. Beachten Sie, dass mit diesem Code ein Benutzertag für den Benutzer hinzugefügt wird, der an den HttpContext angefügt ist. Der Benutzer wurde mit dem hinzugefügten Nachrichtenfilter `AuthenticationTestHandler` authentifiziert und an den HttpContext angefügt. Sie können auch optionale Überprüfungen hinzufügen, um zu überprüfen, dass der Benutzer die richtigen Berechtigungen besitzt, um sich für die angeforderten Tags zu registrieren.

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
            string newRegistrationId = null;
            
            // make sure there are no existing registrations for this push handle (used for iOS and Android)
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

            if (newRegistrationId == null) 
				newRegistrationId = await hub.CreateRegistrationIdAsync();

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

10. Speichern Sie die Änderungen.

## Senden von Benachrichtigungen über das WebAPI-Back-End

In diesem Abschnitt fügen Sie einen neuen Controller hinzu, über den Clientgeräte anhand des Benutzernamentags unter Verwendung der Azure Notification Hubs Service Management-Bibliothek des ASP.NET WebAPI-Back-Ends eine Benachrichtigung senden können.


1. Erstellen Sie einen weiteren neuen Controller mit dem Namen **NotificationsController**. Erstellen Sie ihn auf die gleiche Weise, wie Sie den Controller **RegisterController** im vorherigen Abschnitt erstellt haben.

2. Fügen Sie in der Datei "NotificationsController.cs" die folgenden `using`-Anweisungen hinzu:

        using AppBackend.Models;
        using System.Threading.Tasks;
        using System.Web;

3. Fügen Sie der **NotificationsController**-Klasse die folgende Methode hinzu.

	Mit diesem Code wird ein Benachrichtigungstyp basierend auf dem `pns`-Parameter des Plattform Notification Service (PNS) gesendet. Der Wert von `to_tag` dient zum Festlegen des *username*-Tags in der Nachricht. Dieses Tag muss mit einem Benutzernamentag einer aktiven Notification Hub-Registrierung übereinstimmen. Die Benachrichtigung wird aus dem Text der POST-Anforderung abgerufen und für den Ziel-PNS formatiert.

	Abhängig von dem Plattform Notification Service (PNS), mit dem Ihre unterstützten Geräte Benachrichtigungen empfangen, werden unterschiedliche Benachrichtigungen mit unterschiedlichen Formaten unterstützt. Auf Windows-Geräten können Sie beispielsweise eine [Popupbenachrichtigung mit WNS](https://msdn.microsoft.com/library/windows/apps/br230849.aspx) verwenden, die nicht direkt durch einen anderen PNS unterstützt wird. Ihr Back-End müsste die Benachrichtigung somit so formatieren, dass sie mit dem PNS der zu unterstützenden Geräte kompatibel ist. Verwenden Sie anschließend die entsprechende Übermittlungs-API für die [NotificationHubClient-Methode](https://msdn.microsoft.com/library/azure/microsoft.azure.notificationhubs.notificationhubclient_methods.aspx).

        public async Task<HttpResponseMessage> Post(string pns, [FromBody]string message, string to_tag)
        {
            var user = HttpContext.Current.User.Identity.Name;
            string[] userTag = new string[2];
            userTag[0] = "username:" + to_tag;
            userTag[1] = "from:" + user;

            Microsoft.Azure.NotificationHubs.NotificationOutcome outcome = null;
            HttpStatusCode ret = HttpStatusCode.InternalServerError;

            switch (pns.ToLower())
            {
                case "wns":
                    // Windows 8.1 / Windows Phone 8.1
                    var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" + 
                                "From " + user + ": " + message + "</text></binding></visual></toast>";
                    outcome = await Notifications.Instance.Hub.SendWindowsNativeNotificationAsync(toast, userTag);
                    break;
                case "apns":
                    // iOS
                    var alert = "{"aps":{"alert":"" + "From " + user + ": " + message + ""}}";
                    outcome = await Notifications.Instance.Hub.SendAppleNativeNotificationAsync(alert, userTag);
                    break;
                case "gcm":
                    // Android
                    var notif = "{ "data" : {"message":"" + "From " + user + ": " + message + ""}}";
                    outcome = await Notifications.Instance.Hub.SendGcmNativeNotificationAsync(notif, userTag);
                    break;
            }

            if (outcome != null)
            {
                if (!((outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Abandoned) ||
                    (outcome.State == Microsoft.Azure.NotificationHubs.NotificationOutcomeState.Unknown)))
                {
                    ret = HttpStatusCode.OK;
                }
            }

            return Request.CreateResponse(ret);
        }


4. Drücken Sie **F5**, um die Anwendung auszuführen und um sicherzustellen, dass Ihre Arbeit bisher korrekt ist. Die App sollte einen Webbrowser starten und die ASP.NET-Startseite anzeigen.

##Veröffentlichen des neuen WebAPI-Back-Ends

1. Nun werden wir diese App auf einer Azure-Website bereitstellen, damit von allen Geräten darauf zugegriffen werden kann. Klicken Sie mit der rechten Maustaste auf das Projekt **AppBackend**, und wählen Sie **Veröffentlichen** aus.

2. Wählen Sie **Microsoft Azure Web Apps** als Veröffentlichungsziel aus.

    ![][B15]

3. Melden Sie sich bei Ihrem Azure-Konto an, und wählen Sie eine vorhandene oder neue Web-App aus.

    ![][B16]

4. Notieren Sie sich die **Ziel-URL**-Eigenschaft auf der Registerkarte **Verbindung** tab. Diese URL wird später in diesem Lernprogramm als *Back-End-Endpunkt* bezeichnet. Klicken Sie auf **Veröffentlichen**.

    ![][B18]


[B1]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push1.png
[B2]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push2.png
[B3]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push3.png
[B4]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push4.png
[B5]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push5.png
[B6]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push6.png
[B7]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push7.png
[B8]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push8.png
[B14]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-secure-push14.png
[B15]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users15.PNG
[B16]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users16.PNG
[B18]: ./media/notification-hubs-aspnet-backend-notifyusers/notification-hubs-notify-users18.PNG

<!---HONumber=AcomDC_0128_2016-->