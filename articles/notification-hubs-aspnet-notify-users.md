<properties linkid="notification-hubs-how-to-guides-howto-notify-users-aspnet" urlDisplayName="Notify Users" pageTitle="Notify Users of your ASP.NET service with Notification Hubs" metaKeywords="" description="Follow this tutorial to register to receive notifications from your ASP.NET service by using Notification Hubs" metaCanonical="" services="notification-hubs" documentationCenter="" title="Notify users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

Benachrichtigen von Benutzern mit Notification Hubs
===================================================

[Mobile Services](/en-us/manage/services/notification-hubs/notify-users "Mobile Services")[ASP.NET](/en-us/manage/services/notification-hubs/notify-users-aspnet "ASP.NET")

In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an einen bestimmten App-Benutzer auf einem bestimmten Gerät senden. Ein ASP.NET Web-API-Backend wird verwendet, um Clients zu authentifizieren und Benachrichtigungen zu generieren. Dieses Lernprogramm basiert auf dem Benachrichtigungshub, den Sie im vorherigen Lernprogramm **Erste Schritte mit Notification Hubs** erstellt haben. Der Benachrichtigungsregistrierungscode wird vom Client zum Backend-Dienst verschoben. So wird sichergestellt, dass die Registrierung erst dann abgeschlossen wird, wenn ein Client durch den Dienst positiv authentifiziert wurde. Das bedeutet auch, dass die Anmeldeinformationen des Benachrichtigungshubs nicht mit der Client-App verteilt werden. Der Dienst steuert auch die Tags, die während der Registrierung angefordert werden.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte behandelt:

-   [Erstellen einer ASP.NET-Anwendung mit Authentifizierung](#create-application)
-   [Aktualisieren Ihrer ASP.NET-Anwendung zur Registrierung für Benachrichtigungen](#register-notification)
-   [Aktualisieren der App zum Anmelden und zum Anfordern der Registrierung](#update-app)

Voraussetzungen
---------------

-   Visual Studio 2012. Sie können sowohl Visual Studio Express 2012 für das Web als auch Visual Studio Express 2012 für Windows 8 verwenden, um entsprechend die ASP.NET-Anwendung und die Windows Store-App zu erstellen.
-   Dieses Lernprogramm basiert auf der App und dem Benachrichtigungshub, die Sie im vorherigen Lernprogramm **Erste Schritte mit Notification Hubs** erstellt haben. Bevor Sie dieses Lernprogramm starten, müssen Sie zunächst das Lernprogramm **Erste Schritte mit Notification Hubs** ([Windows Store C\#](/en-us/manage/services/notification-hubs/getting-started-windows-dotnet)/[iOS](/en-us/manage/services/notification-hubs/get-started-notification-hubs-ios)/[Android](/en-us/manage/services/notification-hubs/get-started-notification-hubs-android)) abschließen.

**Hinweis**

Das ASP.NET Web-API-Projekt, das Sie in diesem Lernprogramm erstellen, wird auf Ihrem lokalen Computer ausgeführt. Sie können ein ASP.NET Web-API-Projekt auch in Azure veröffentlichen. Weitere Informationen finden Sie unter [Erstellen eines für Mobiltelefone optimierten REST-Dienstes, der ASP.NET Web-API und SQL-Datenbank verwendet](/en-us/develop/net/tutorials/rest-service-using-web-api/).

Erstellen der ASP.NET-AppErstellen einer ASP.NET-Anwendung mit Authentifizierung
--------------------------------------------------------------------------------

Zunächst erstellen Sie eine ASP.NET Web-API-Anwendung. Dieser Backend-Dienst authentifiziert Clients, registriert Pushbenachrichtigungen im Auftrag von authentifizierten Benutzern und versendet Benachrichtigungen.

1.  Klicken Sie in Visual Studio oder Visual Studio Express 2012 für das Web auf **Datei**, dann auf **New project from the File menu, expand **Templates**, **Visual C\#**, dann klicken Sie auf **Web** und **ASP.NET MVC 4-Webanwendung**, geben Sie den Namen *NotificationService* ein, und klicken Sie auf **OK**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-create-mvc-app.png)

2.  Klicken Sie im Dialogfeld **New ASP.NET MVC Project** auf **Leer** und dann auf **OK**.

    Daraufhin wird ein ASP.NET MVC-Projekt erstellt.

3.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Hinzufügen** und dann auf **Klasse**, geben Sie dann `AuthenticationTestHandler` ein, und klicken Sie auf **Hinzufügen**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-create-aspnet-class.png)

    Daraufhin wird eine Codedatei für die Klasse **AuthenticationTestHandler** zum Projekt hinzugefügt.

4.  Öffnen Sie die neue Projektdatei "AuthenticationTestHandler.cs", und ersetzen Sie die Klassendefinition durch den folgenden Code:

         using System;
         using System.Collections.Generic;
         using System.Linq;
         using System.Net;
         using System.Net.Http;
         using System.Threading;
         using System.Threading.Tasks;
         using System.Security.Principal;
         using System.Text;
         using System.Web;
            
         namespace NotificationService
         {
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
                     } else return Unauthorised();
            
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
         } 

    **Sicherheitshinweis**

    Die Klasse **AuthenticationTestHandler** bietet keine wirkliche Authentifizierung. Sie wird verwendet, um eine grundlegende Authentifizierung zu imitieren und ein Prinzip zurückzugeben. Der Benutzername ist erforderlich, um eine Notification Hub-Registrierung zu erstellen. Die obige Implementierung ist nicht sicher. Sie müssen einen sicheren Authentifizierungsmechanismus in Ihren Produktionsanwendungen und -diensten implementieren.

5.  Erweitern Sie den Ordner **App\_Start**, öffnen Sie die Datei "WebApiConfig.cs", und fügen Sie die folgende Codezeile am Ende der Methode **Registrieren** ein:

         config.MessageHandlers.Add(new AuthenticationTestHandler());

    Dies erfordert, dass Anfragen an die ASP.NET-Anwendung den Authorization-Header enthalten.

Nun haben wir die grundlegende Anwendung mit einem Pseudo-Authentifizierungsschema erstellt, damit uns ein Benutzername angegeben wird.

Registrieren für BenachrichtigungenAktualisieren Ihrer ASP.NET-Anwendung zur Registrierung für Benachrichtigungen
-----------------------------------------------------------------------------------------------------------------

Der nächste Schritt ist das Hinzufügen der Registrierungslogik für Benachrichtigungshubs zur ASP-NET-Anwendung, indem ein neuer Controller **Registrierung** erstellt wird.

1.  Melden Sie sich beim [Azure Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Service Bus**, Ihren Namespace, **Notification Hubs**, dann wählen Sie Ihren Benachrichtigungshub aus und klicken auf **Verbindungsinformationen**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-select-hub-connection.png)

2.  Notieren Sie sich den Namen Ihres Benachrichtigungshubs, und kopieren Sie die Verbindungszeichenfolge für die **DefaultFullSharedAccessSignature**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-connection-strings.png)

    Sie verwenden diese Verbindungszeichenfolge zusammen mit dem Namen des Benachrichtigungshubs, um sich für Benachrichtigungen zu registrieren und um Benachrichtigungen zu senden.

3.  Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf den Projektnamen, und wählen Sie dann **NuGet-Pakete verwalten** aus.

4.  Wählen Sie im linken Bereich die Kategorie **Online** aus, suchen Sie nach `WindowsAzure.ServiceBus`, klicken Sie auf dem Paket **Azure-Dienstbus** auf **Installieren**, und stimmen Sie dem Lizenzvertrag zu.

	![](./media/notification-hubs-aspnet-notify-users/notification-hub-add-nuget-package.png)
 
	Daraufhin wird das Assembly "Microsoft.ServiceBus.dll" zu Ihrem Projekt hinzugefügt.

1.  Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Ordner **Controllers**, klicken Sie auf **Hinzufügen**, **Controller...**, geben Sie dann `RegisterController` für **Controllername** ein, wählen Sie **Empty API controller**, und klicken Sie auf **Hinzufügen**.

    ![](./media/notification-hubs-aspnet-notify-users/notification-hub-add-register-controller2.png)

    Auf diese Weise wird eine Controllerklasse zum Projekt hinzugefügt. Wenn dieser Controller aufgerufen wird, führt er die Registrierung eines Geräts mit Notification Hubs durch.

2.  Öffnen Sie die neue Projektdatei "RegisterController.cs", und fügen Sie die folgenden **using**-Anweisungen hinzu:

         using Microsoft.ServiceBus.Notifications;
         using Newtonsoft.Json.Linq;
         using System.Threading.Tasks;
         using System.Web;

3.  Fügen Sie den folgenden Code zur neuen RegisterController-Klasse hinzu:

         // Define the Notification Hubs client.
         private NotificationHubClient hubClient;

         // Create the client in the constructor.
         public RegisterController()
         {
             var cn = "<FULL_SAS_CONNECTION_STRING>";
             hubClient = NotificationHubClient
                 .CreateClientFromConnectionString(cn, "<NOTIFICATION_HUB_NAME>");
         }

4.  Aktualisieren Sie den Code im Konstruktor, ersetzen Sie *`<NOTIFICATION_HUB_NAME>`* und *`<FULL_SAS_CONNECTION_STRING>`* mit den Werten für Ihren Benachrichtigungshub, und klicken Sie dann auf **Speichern**.

    **Hinweis**

    Stellen Sie sicher, dass Sie die **DefaultFullSharedAccessSignature** für *`<FULL_SAS_CONNECTION_STRING>`* verwenden. Mit diesem Anspruch kann Ihre Web-API Registrierungen erstellen und aktualisieren.

5.  Fügen Sie den folgenden Post-Methoden-Code in die RegisterController-Klasse ein:

         public async Task<RegistrationDescription> Post([FromBody]JObject registrationCall)
         {
             // Get the registration info that we need from the request. 
             var platform = registrationCall["platform"].ToString();
             var installationId = registrationCall["instId"].ToString();
             var channelUri = registrationCall["channelUri"] != null 
         
                 registrationCall["channelUri"].ToString() : null;
             var deviceToken = registrationCall["deviceToken"] != null 
         
                 registrationCall["deviceToken"].ToString() : null;       
             var userName = HttpContext.Current.User.Identity.Name;

             // Get registrations for the current installation ID.
             var regsForInstId = await hubClient.GetRegistrationsByTagAsync(installationId, 100);

             bool updated = false;
             bool firstRegistration = true;
             RegistrationDescription registration = null;

             // Check for existing registrations.
             foreach (var registrationDescription in regsForInstId)
             {
                 if (firstRegistration)
                 {public async Task<RegistrationDescription> Post([FromBody]JObject registrationCall)
        {
            // Get the registration info that we need from the request. 
            var platform = registrationCall["platform"].ToString();
            var installationId = registrationCall["instId"].ToString();
            var channelUri = registrationCall["channelUri"] != null ? 
                registrationCall["channelUri"].ToString() : null;
            var deviceToken = registrationCall["deviceToken"] != null ? 
                registrationCall["deviceToken"].ToString() : null;       
            var userName = HttpContext.Current.User.Identity.Name;

            // Get registrations for the current installation ID.
            var regsForInstId = await hubClient.GetRegistrationsByTagAsync(installationId, 100);

            bool updated = false;
            bool firstRegistration = true;
            RegistrationDescription registration = null;

            // Check for existing registrations.
            foreach (var registrationDescription in regsForInstId)
            {
                if (firstRegistration)
                {
 					// Update the tags.
                    registrationDescription.Tags = new HashSet<string>() { installationId, userName };

                    // We need to handle each platform separately.
                    switch (platform)
                    {
                        case "windows":
                            var winReg = registrationDescription as WindowsRegistrationDescription;
                            winReg.ChannelUri = new Uri(channelUri);
                            registration = await hubClient.UpdateRegistrationAsync(winReg);                            
                            break;
                        case "ios":
                            var iosReg = registrationDescription as AppleRegistrationDescription;
                            iosReg.DeviceToken = deviceToken;
                            registration = await hubClient.UpdateRegistrationAsync(iosReg);
                            break;
                    }
                    updated = true;
                    firstRegistration = false;
                }
                else
                {
                    // We shouldn't have any extra registrations; delete if we do.
                    await hubClient.DeleteRegistrationAsync(registrationDescription);
                }
            }

            // Create a new registration.
            if (!updated)
            {
                switch (platform)
                {
                    case "windows":
                        registration = await hubClient.CreateWindowsNativeRegistrationAsync(channelUri, 
                            new string[] { installationId, userName });
                        break;
                    case "ios":
                        registration = await hubClient.CreateAppleNativeRegistrationAsync(deviceToken, 
                            new string[] { installationId, userName });
                        break;
                }
            }

            // Send out a test notification.
            sendNotification(string.Format("Test notification for {0}", userName), userName);

            return registration;
        }

    Dieser Code wird durch eine POST-Anfrage aufgerufen und ruft Plattform- und Geräte-ID-Informationen aus dem Nachrichtentext ab. Diese Daten werden zusammen mit der Installations-ID aus dem Anforderungsheader und der Benutzer-ID des angemeldeten Benutzers verwendet, um eine Registrierung zu aktualisieren. Wenn keine Registrierung vorhanden ist, wird eine neue Registrierung erstellt. Diese Registrierung ist mit der Benutzer-ID und der Installations-ID markiert.

6.  Fügen Sie die folgende sendNotification-Methode hinzu:

        // Basic implementation that sends a not ification to Windows Store and iOS app clients.
        private async Task sendNotification(string notificationText, string tag)
        {
            try
            {
                // Create notifications for both Windows Store and iOS platforms.
                var toast = @"<toast><visual><binding template=""ToastText01""><text id=""1"">" +
                    notificationText + "</text></binding></visual></toast>";
                var alert = "{\"aps\":{\"alert\":\"" + notificationText + 
                    "\"}, \"inAppMessage\":\"" + notificationText + "\"}";

                // Send a notification to the logged-in user on both platforms.
                await hubClient.SendWindowsNativeNotificationAsync(toast, tag);
                await hubClient.SendAppleNativeNotificationAsync(alert, tag);
            }
            catch(ArgumentException ex)
            {
                // This is expected when an APNS registration doesn't exist.
                Console.WriteLine(ex.Message);
            }
        }

    Diese Methode wird aufgerufen, um sofort eine Benachrichtigung zu senden, sobald die Registrierung abgeschlossen ist.

Als Nächstes aktualisieren wir die Client-App, die Sie erstellt haben, als Sie das Lernprogramm **Erste Schritte mit Notification Hubs** abgeschlossen haben.

Aktualisieren der AppAktualisieren der App zum Anmelden und zum Anfordern der Registrierung
-------------------------------------------------------------------------------------------

Die App, die Sie beim Abschließen des Lernprogramms **Erste Schritte mit Notification Hubs** erstellt haben, erfordert Authentifizierung direkt über den Benachrichtigungshub. Sie entfernen diesen Registrierungscode aus der Client-App und ersetzen ihn durch die neue Registrierungs-API in der ASP.NET Web-API-Anwendung.

1.  Drücken Sie **F5**, um die ASP.NET-Anwendung zu starten und die Standardseite zu laden.

    Wenn der Browser angezeigt wird, notieren Sie sich den Hostnamen der angeforderten Site. Sie benötigen diese Stamm-URL, um die Client-App zu aktualisieren.

    **Hinweis**

    Wenn Sie den lokalen IIS-Webserver oder den Visual Studio Development Server verwenden, müssen Sie auch eine Portnummer angeben. Beachten Sie, dass ein 404-Fehler zurückgegeben wird, da wir keine Standardseite in dieser Anwendung implementiert haben.

2.  Befolgen Sie die Schritte einer der folgenden Versionen von **Registrieren des aktuellen Benutzers für Pushbenachrichtigungen mithilfe der ASP.NET Web-API**, je nach Ihrer Client-Plattform:

    -   [Windows Store C\#-Version](/en-us/manage/services/notification-hubs/register-users-aspnet-dotnet)
    -   [iOS-Version](/en-us/manage/services/notification-hubs/howto-register-user-with-aspnet-ios)

3.  Führen Sie die aktualisierte App aus, melden Sie sich mit derselben Zeichenfolge für Benutzername und Kennwort bei dem Dienst an, und stellen Sie dann sicher, dass die Registrierungs-ID angezeigt wird, die der Benachrichtigung zugeordnet ist.

    Sie erhalten auch eine Pushbenachrichtigung.

    **Hinweis**

    Es wird ein Fehler am Backend angezeigt, wenn keine Registrierung für eine Plattform vorliegt, an die eine Benachrichtigung gesendet werden soll. In diesem Fall kann dieser Fehler ignoriert werden. Unter [Senden plattformübergreifender Benachrichtigungen an Benutzer mit Notification Hubs](/en-us/manage/services/notification-hubs/notify-users-xplat-aspnet) erfahren Sie, wie Sie diese Situation durch die Verwendung von Vorlagen vermeiden.

4.  (Optional) Stellen Sie die Client-App auf einem zweiten Gerät bereit, führen Sie die App aus, und fügen Sie Text ein.

    Auf jedem Gerät wird eine Benachrichtigung angezeigt.

Nächste Schritte
----------------

Da Sie dieses Lernprogramm nun abgeschlossen haben, können Sie die folgenden Lernprogramme abschließen:

-   **Verwenden von Notification Hubs zum Senden von Nachrichten ([Windows Store C\#](/en-us/manage/services/notification-hubs/breaking-news-dotnet) / [iOS](/en-us/manage/services/notification-hubs/breaking-news-dotnet))**
    In diesem plattformspezifischen Lernprogramm erfahren Sie, wie Sie Tags verwenden, um Benutzern die Möglichkeit zu geben, verschiedene Benachrichtigungen zu abonnieren, die sie interessieren.

-   **[Send cross-platform notifications to users with Notification Hubs](/en-us/manage/services/notification-hubs/notify-users-xplat-aspnet) (Senden plattformübergreifender Benachrichtigungen an Benutzer mit Notification Hubs, in englischer Sprache)**
    Dieses Lernprogramm ergänzt das aktuelle Lernprogramm **Benachrichtigen von Benutzern mit Notification Hubs** und zeigt die Verwendung plattformspezifischer Vorlagen zur Registrierung von Benachrichtigungen. So können Sie Benachrichtigungen über eine einzige Methode in Ihrem serverseitigen Code senden.

Weitere Informationen zu Notification Hubs finden Sie unter [Azure Notification Hubs](http://msdn.microsoft.com/en-us/library/windowsazure/jj927170.aspx).

