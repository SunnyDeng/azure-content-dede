<properties linkid="notification-hubs-how-to-guides-howto-notify-users-mobileservices" urlDisplayName="Notify Users" pageTitle="Notify Users of your mobile service with Notification Hubs" metaKeywords="" description="Follow this tutorial to register to receive notifications from your mobile service by using Notification Hubs" metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="" title="Notify users with Notification Hubs" authors="glenga" solutions="" manager="" editor="" />

Benachrichtigen von Benutzern mit Notification Hubs
===================================================

[Mobile Services](/de-de/manage/services/notification-hubs/notify-users "Mobile Services")[ASP.NET](/de-de/manage/services/notification-hubs/notify-users-aspnet "ASP.NET")

In diesem Lernprogramm erfahren Sie, wie Sie mithilfe von Azure Notification Hubs eine Pushbenachrichtigung an einen bestimmten App-Benutzer auf einem bestimmten Gerät senden. Ein Azure Mobile Services-Backend wird verwendet, um Clients zu authentifizieren und Benachrichtigungen zu generieren. Dieses Lernprogramm basiert auf dem Benachrichtigungshub, den Sie im vorherigen Lernprogramm **Erste Schritte mit Notification Hubs** erstellt haben. Der Benachrichtigungsregistrierungscode wird vom Client zum Backend-Dienst verschoben. So wird sichergestellt, dass die Registrierung erst dann abgeschlossen wird, wenn ein Client durch den Dienst positiv authentifiziert wurde. Das bedeutet auch, dass die Anmeldeinformationen des Benachrichtigungshubs nicht mit der Client-App verteilt werden. Der Dienst steuert auch die Tags, die während der Registrierung angefordert werden.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte behandelt:

-   [Aktualisieren Ihres mobilen Dienstes zur Registrierung für Benachrichtigungen](#register-notification)
-   [Aktualisieren der App zum Anmelden und zum Anfordern der Registrierung](#update-app)
-   [Aktualisieren Ihres mobilen Dienstes zum Senden von Benachrichtigungen](#send-notifications)

Voraussetzungen
---------------

Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst die folgenden Lernprogramme abschließen:

-   **Erste Schritte mit Notification Hubs** ([Windows Store C\#](/de-de/manage/services/notification-hubs/getting-started-windows-dotnet)/[iOS](/de-de/manage/services/notification-hubs/get-started-notification-hubs-ios)/[Android](/de-de/manage/services/notification-hubs/get-started-notification-hubs-android)).

-   **Erste Schritte bei der Authentifizierung in Mobile Services** ([Windows Store C\#](/de-de/develop/mobile/tutorials/get-started-with-users-dotnet/)/[iOS](/de-de/develop/mobile/tutorials/get-started-with-users-ios/)/[Android](/de-de/develop/mobile/tutorials/get-started-with-users-android/))

Dieses Lernprogramm basiert auf der App und dem Benachrichtigungshub, die Sie im vorherigen Lernprogramm **Erste Schritte mit Notification Hubs** erstellt haben. Der authentifizierte mobile Dienst, den Sie unter **Erste Schritte bei der Authentifizierung in Mobile Services** konfiguriert haben, wird verwendet.

**Hinweis**

Standardmäßig verwendet das Lernprogramm **Erste Schritte bei der Authentifizierung in Mobile Services** Facebook-Authentifizierung. Sie können in diesem Lernprogramm keine Microsoft Account-Authentifizierung verwenden, da zwei Windows Store-Apps nicht dieselbe Live Connect-Registrierung verwenden können. Der mobile Dienst und der Benachrichtigungshub müssen für dieselbe App in Live Connect registriert sein, damit die Microsoft Account-Authentifizierung verwendet werden kann.

Registrieren für BenachrichtigungenAktualisieren Ihres mobilen Dienstes zur Registrierung für Benachrichtigungen
----------------------------------------------------------------------------------------------------------------

Da die Benachrichtigungsregistrierung erst abgeschlossen werden darf, nachdem ein Client positiv authentifiziert wurde, wird die Registrierung mit einer benutzerdefinierten API durchgeführt, die im mobilen Dienst definiert ist. Diese benutzerdefinierte API wird durch einen authentifizierten Client aufgerufen, um die Benachrichtigungsregistrierung anzufordern. In diesem Abschnitt aktualisieren Sie den authentifizierten mobilen Dienst, den Sie definiert haben, als Sie das Lernprogramm **Erste Schritte bei der Authentifizierung in Mobile Services** abgeschlossen haben.

1.  Melden Sie sich beim [Azure Verwaltungsportal](https://manage.windowsazure.com/) an, klicken Sie auf **Service Bus**, Ihren Namespace, **Notification Hubs**, dann wählen Sie Ihren Benachrichtigungshub aus und klicken auf **Verbindungsinformationen**.

    ![](./media/notification-hubs-mobile-services-notify-users/notification-hub-select-hub-connection.png)

2.  Notieren Sie sich den Namen Ihres Benachrichtigungshubs, und kopieren Sie die Verbindungszeichenfolge für die **DefaultFullSharedAccessSignature**.

    ![](./media/notification-hubs-mobile-services-notify-users/notification-hub-connection-strings.png)

    Sie verwenden diese Verbindungszeichenfolge zusammen mit dem Namen des Benachrichtigungshubs, um sich für Benachrichtigungen zu registrieren und um Benachrichtigungen zu senden.

3.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf Ihre App.

     ![](./media/notification-hubs-mobile-services-notify-users/mobile-services-selection.png)

4.  Klicken Sie auf die Registerkarte **API** und dann auf **Create a custom API**.

     ![](./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create.png)

     Das Dialogfeld **Create a new custom API** wird angezeigt.

5.  Geben Sie *register\_notifications* unter **API-Name** ein, wählen Sie für **POST Permissions** **Only Authenticated Users**

     ![](./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-create2.png)

	Daraufhin wird die API erstellt, die die Authentifizierung von Benutzern mithilfe der HTTP-POST-Methode erfordert, bevor diese Aufrufe tätigen können. Die anderen HTTP-Methoden müssen wir nicht festlegen, da wir diese nicht implementieren werden.

1.  Klicken Sie auf den neuen Eintrag **register\_notifications** in der API-Tabelle.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-custom-api-select.png)

2.  Klicken Sie auf die Registerkarte **Skript**, und ersetzen Sie den vorhandenen Code durch den folgenden:

         exports.post = function(request, response) {

             // Create a notification hub instance.
             var azure = require('azure');
             var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
                 '<FULL_SAS_CONNECTION_STRING>');
            
             // Get the registration info that we need from the request. 
             var platform = request.body.platform;
             var userId = request.user.userId;
             var installationId = request.header('X-ZUMO-INSTALLATION-ID');
                
             // Function called when registration is completed.
             var registrationComplete = function(error, registration) {
                 if (!error) {
                     // Return the registration.
                     response.send(200, registration);
                 } else {
                     response.send(500, 'Registration failed!');
                 }
             }
             // Function called to log errors.
             var logErrors = function(error) {
                 if (error) {
                     console.error(error)
                 }
             }
             // Get existing registrations.
             hub.listRegistrationsByTag(installationId, function(error, existingRegs) {
                 var firstRegistration = true;
                 if (existingRegs.length > 0) {
                      for (var i = 0; i < existingRegs.length; i++) {
                         if (firstRegistration) {
                             // Update an existing registration.
                             if (platform === 'win8') {
                                 existingRegs[i].ChannelUri = request.body.channelUri;                        
                                 hub.updateRegistration(existingRegs[i], registrationComplete);                        
                             } else if (platform === 'ios') {
                                 existingRegs[i].DeviceToken = request.body.deviceToken;
                                 hub.updateRegistration(existingRegs[i], registrationComplete);
                             } else {
                                 response.send(500, 'Unknown client.');
                             }
                             firstRegistration = false;
                         } else {
                             // We shouldn't have any extra registrations; delete if we do.
                             hub.deleteRegistration(existingRegs[i].RegistrationId, logErrors);
                         }
                     }
                 } else {
                     // Create a new registration.
                     if (platform === 'win8') {                
                         hub.wns.createNativeRegistration(request.body.channelUri, 
                         [userId, installationId], registrationComplete);
                     } else if (platform === 'ios') {
                         hub.apns.createNativeRegistration(request.body.deviceToken, 
                         [userId, installationId], registrationComplete);
                     } else {
                         response.send(500, 'Unknown client.');
                     }
                 }
             });
         }

    Dieser Code ruft Plattform- und Geräte-ID-Informationen aus dem Nachrichtentext ab. Diese Daten werden zusammen mit der Installations-ID aus dem Anforderungsheader und der Benutzer-ID des angemeldeten Benutzers verwendet, um eine Registrierung zu aktualisieren, falls diese vorhanden ist, oder um eine neue Registrierung zu erstellen. Diese Registrierung ist mit der Benutzer-ID und der Installations-ID markiert.

3.  Aktualisieren Sie das Skript, und ersetzen Sie *`<NOTIFICATION_HUB_NAME>`* und *`<FULL_SAS_CONNECTION_STRING>`* mit den Werten für Ihren Benachrichtigungshub, und klicken Sie dann auf **Speichern**.

    **Hinweis**

    Stellen Sie sicher, dass Sie die **DefaultFullSharedAccessSignature** für *`<FULL_SAS_CONNECTION_STRING>`* verwenden. Mit diesem Anspruch kann Ihre benutzerdefinierte API-Methode Registrierungen erstellen und aktualisieren.

Aktualisieren der AppAktualisieren der App zum Anmelden und zum Anfordern der Registrierung
-------------------------------------------------------------------------------------------

Als Nächstes müssen Sie die TodoList-App aktualisieren, um eine Registrierung für die Benachrichtigung anzufordern, indem die neue benutzerdefinierte API aufgerufen wird.

1.  Befolgen Sie die Schritte einer der folgenden Versionen von **Registrieren des aktuellen Benutzers für Pushbenachrichtigungen mithilfe eines mobilen Dienstes**, je nach Ihrer Client-Plattform:

    -   [Windows Store C\#-Version](/de-de/manage/services/notification-hubs/register-users-mobile-services-dotnet)
    -   [iOS-Version](/de-de/manage/services/notification-hubs/register-users-ios)

2.  Führen Sie die aktualisierte App aus, melden Sie sich bei Facebook an, und stellen Sie dann sicher, dass die Registrierungs-ID angezeigt wird, die der Benachrichtigung zugeordnet ist.

Senden von BenachrichtigungenAktualisieren Ihres mobilen Dienstes zum Senden von Benachrichtigungen
---------------------------------------------------------------------------------------------------

Abschließend wird Code zu dem mobilen Dienst hinzugefügt, der Benachrichtigungen im mobilen Dienst sendet. Dieser Benachrichtigungscode wird zum Serverskript hinzugefügt, das für den Einfügen-Handler der Tabelle **TodoItem** registriert wurde.

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **TodoItem**.

    ![](./media/notification-hubs-mobile-services-notify-users/mobile-portal-data-tables.png)

2.  Klicken Sie unter **todoitem** auf die Registerkarte **Skript** und wählen Sie **Einfügen** aus.

	![](./media/notification-hubs-mobile-services-notify-users/mobile-insert-script-push2.png) 

	Daraufhin wird die Funktion angezeigt, die aufgerufen wird, wenn etwas in die Tabelle **TodoItem** eingefügt wird.

1.  Ersetzen Sie die Einfügefunktion durch den folgenden Code:

         function insert(item, user, request) {
             var azure = require('azure');
             var hub = azure.createNotificationHubService('<NOTIFICATION_HUB_NAME>', 
             '<FULL_SAS_CONNECTION_STRING>');

			// Erstellen Sie die Nutzlast für eine Windows Store-App.
		     var wnsPayload = '<toast><visual><binding template="ToastText02"><text id="1">New item added:</text><text id="2">' + item.text + '</text></binding></visual></toast>';
		
		    // Execute the request and send notifications.
		    request.execute({
		        success: function() {
		            // Write the default response and send a notification 
		            // to the user on all devices by using the userId tag.
		            request.respond();
		            // Send to Windows Store apps.
		            hub.wns.send(user.userId, wnsPayload, 'wns/toast', function(error) {
		                if (error) {
		                    console.log(error);
		                }
		            });
		            // Send to iOS apps.
		            hub.apns.send(user.userId, {
		                alert: item.text
		            }, function(error) {
		                if (error) {
		                    console.log(error);
		                }
		            });
		        }
		    });
		}

    Daraufhin wird versucht, Benachrichtigungen an das Tag für den aktuell angemeldeten Benutzer zu senden, sowohl in Windows Store- als auch in iOS-Apps.

1.  Aktualisieren Sie das Skript, und ersetzen Sie *`<NOTIFICATION_HUB_NAME>`* und *`<FULL_SAS_CONNECTION_STRING>`* mit den Werten für Ihren Benachrichtigungshub, und klicken Sie dann auf **Speichern**.

    Auf diese Weise wird ein neues insert-Skript registriert, das über Notification Hubs eine Pushbenachrichtigung (den eingefügten Text) zu dem in der Einfügeanforderung angegebenen Kanal sendet.

    **Hinweis**

    Stellen Sie sicher, dass Sie die **DefaultFullSharedAccessSignature** für *`<FULL_SAS_CONNECTION_STRING>`* verwenden. Dieser Anspruch bietet Ihrem insert-Skript vollen Zugriff und ermöglicht ihm das Senden von Benachrichtigungen an registrierte Clients.

Testen der AppTesten von Pushbenachrichtigungen in Ihrer App
------------------------------------------------------------

Da die Benachrichtigungen nun konfiguriert sind, ist es an der Zeit, die App zu testen, indem Daten eingefügt werden, um eine Benachrichtigung zu generieren.

1.  Führen Sie die App aus.

    Beim Starten wird erneut eine Registrierungsbenachrichtigung angezeigt.

2.  Geben Sie wie zuvor Text ein, und fügen Sie ein neues Element hinzu.

    Bitte beachten Sie, dass die App nach dem Einfügen eine Pushbenachrichtigung von Notification Hubs erhält.

    **Hinweis**

    Es wird ein Fehler am Backend angezeigt, wenn keine Registrierung für eine Plattform vorliegt, an die eine Benachrichtigung gesendet werden soll. In diesem Fall kann dieser Fehler ignoriert werden. Unter [Senden plattformübergreifender Benachrichtigungen an Benutzer mit Notification Hubs](/de-de/manage/services/notification-hubs/notify-users-xplat-mobile-services/) erfahren Sie, wie Sie diese Situation durch die Verwendung von Vorlagen vermeiden.

3.  (Optional) Stellen Sie die Client-App auf einem zweiten Gerät bereit, führen Sie die App aus, und fügen Sie Text ein.

    Auf jedem Gerät wird eine Benachrichtigung angezeigt.

Nächste Schritte
----------------

Da Sie dieses Lernprogramm nun abgeschlossen haben, können Sie die folgenden Lernprogramme abschließen:

-   **Verwenden von Notification Hubs zum Senden von Nachrichten ([Windows Store C\#](/de-de/manage/services/notification-hubs/breaking-news-dotnet) / [iOS](/de-de/manage/services/notification-hubs/breaking-news-ios))**
    In diesem plattformspezifischen Lernprogramm erfahren Sie, wie Sie Tags verwenden, um Benutzern die Möglichkeit zu geben, verschiedene Benachrichtigungen zu abonnieren, die sie interessieren.

-   **[Send cross-platform notifications to users with Notification Hubs](/de-de/manage/services/notification-hubs/notify-users-xplat-mobile-services) (Senden plattformübergreifender Benachrichtigungen an Benutzer mit Notification Hubs, in englischer Sprache)**
    Dieses Lernprogramm ergänzt das aktuelle Lernprogramm **Benachrichtigen von Benutzern mit Notification Hubs** und zeigt die Verwendung plattformspezifischer Vorlagen zur Registrierung von Benachrichtigungen. So können Sie Benachrichtigungen über eine einzige Methode in Ihrem serverseitigen Code senden.

Weitere Informationen zu Notification Hubs finden Sie unter [Azure Notification Hubs](http://msdn.microsoft.com/de-de/library/windowsazure/jj927170.aspx).

