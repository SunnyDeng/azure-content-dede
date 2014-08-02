<properties linkid="" urlDisplayName="" pageTitle="Push notifications to users (Android ) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

Pushbenachrichtigungen an Benutzer über Mobile Services
=======================================================

[Windows Phone](/en-us/develop/mobile/tutorials/push-notifications-to-users-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/push-notifications-to-users-ios "iOS")[Android](/en-us/develop/mobile/tutorials/push-notifications-to-users-android "Android")

In diesem Thema wird das [vorherige Lernprogramm zu Pushbenachrichtigungen](/en-us/develop/mobile/tutorials/get-started-with-push-android) weiter ausgebaut, indem eine neue Tabelle hinzugefügt wird. In dieser werden Registrierungs-URIs für Google Cloud Messaging (GCM) gespeichert, welche dann zum Versenden von Pushbenachrichtigungen an mehrere Benutzer der Android-App verwendet werden können. In diesem Lernprogramm wird eine einzige Aktualisierung jedes Mal Pushbenachrichtigungen an alle registrierten Geräte generieren, wenn etwas in die ToDoList-Tabelle eingefügt wird. Im vorherigen Lernprogramm wurde nur eine Benachrichtigung an das Gerät versendet, das die Einfügung vorgenommen hat.

Dieses Lernprogramm führt Sie in folgenden Schritten zur Aktualisierung von Pushbenachrichtigungen in Ihrer App:

1.  [Erstellen der Registrations-Tabelle](#create-table)
2.  [Aktualisieren Ihrer App](#update-app)
3.  [Aktualisieren von Serverskripts](#update-scripts)
4.  [Verifizieren des Pushbenachrichtigungsverhaltens](#test-app)

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf, und führt das vorherige Lernprogramm [Erste Schritte mit Pushbenachrichtigungen](/en-us/develop/mobile/tutorials/get-started-with-push-android) weiter fort. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Pushbenachrichtigungen](/en-us/develop/mobile/tutorials/get-started-with-push-android) abschließen.

Erstellen einer neuen Tabelle
-----------------------------

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an. Klicken Sie auf **Mobile Services** und dann auf Ihre App.

   	![][0]

2.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Erstellen**.

   	![][1]

   	Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

3.  Behalten Sie die Standardeinstellung **Jeder mit dem Anwendungsschlüssel** für alle Berechtigungen bei, geben Sie *Registration* in das Feld **Tabellenname** ein, und klicken Sie dann auf den Häkchenknopf.

   	![][2]

   	Dadurch wird die Tabelle **Registration** erstellt, in der die Registration-URIs gespeichert werden, über die Pushbenachrichtigungen getrennt von Elementdaten gesendet werden.

Als Nächstes modifizieren Sie die Pushbenachrichtigungs-App zur Speicherung von Registrierungsdaten in dieser neuen Tabelle anstatt in der Tabelle **TodoItem**.

Aktualisieren Ihrer App
-----------------------

1.  Klicken Sie im Paket-Explorer mit der rechten Maustaste auf das Paket (unter dem Knoten `src`), klicken Sie auf **Neu** und dann auf **Klasse**.

2.  Geben Sie für **Name** `Registration` ein, und klicken Sie dann auf **Fertig stellen**

    ![](./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-class.png)

    Daraufhin wird die neue Registration-Klasse erstellt.

3.  Öffnen Sie die Datei "ToDoItem.java", und schneiden Sie folgenden Code aus:

         @com.google.gson.annotations.SerializedName("handle")
         private String mHandle;

         public String getHandle() {
             return mHandle;
         }

         public final void setHandle(String handle) {
             mHandle = handle;
         }

4.  Fügen Sie den im vorherigen Schritt ausgeschnittenen Code im Text der zuvor erstellten **Registration**-Klasse ein.

5.  Fügen Sie der **Registration**-Klasse folgenden Code hinzu:

         @com.google.gson.annotations.SerializedName("id")
         private int mId;

         /**
          * Returns the item id
          */
         public int getId() {
             return mId;
         }

         /**
          * Sets the item id
          * 
          * @param id : id to set
          */
         public final void setId(int id) {
             mId = id;
         }

6.  Öffnen Sie die Datei **ToDoActivity.java**, und löschen Sie in der Methode `addItem` folgende Zeilen:

        item.setHandle(MyHandler.getHandle());

7.  Suchen Sie die `mClient`-Eigenschaft, und ersetzen Sie diese mit folgendem Code:

         /**
          * Mobile Service Client reference
          */
         private static MobileServiceClient mClient;

         /**
          * Returns the client reference
          */
         public static MobileServiceClient getClient() {
             return mClient;
         }

8.  Fügen Sie in der Datei **MyHandler** folgende Importanweisungen hinzu:

         import android.util.Log;
            
         import com.microsoft.windowsazure.notifications.NotificationsHandler;

         import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
         import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
         import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
         import com.microsoft.windowsazure.mobileservices.TableOperationCallback;

9.  Fügen Sie den folgenden Code am Ende der `onRegistered`-Methode hinzu:

         MobileServiceClient client = ToDoActivity.getClient();
         MobileServiceTable<Registration> registrations = client.getTable(Registration.class);

         // Neue Registration erstellen
         Registration registration = new Registration();
         registration.setHandle(gcmRegistrationId);

         // Neue Registration einfügen
         registrations.insert(registration, new TableOperationCallback<Registration>() {

             public void onCompleted(Registration entity, Exception exception, ServiceFilterResponse response) {

                 if (exception != null) {
                     Log.e("MyHandler", exception.getMessage());
                 } else {
                     Log.e("MyHandler", "Registration OK");
                 }
             }
         });

Ihre App kann nun Pushbenachrichtigungen an Benutzer unterstützen.

Aktualisieren von Serverskripts
-------------------------------

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **Registration**.

   	![][3]

2.  Klicken Sie unter **Registration** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.

   	![][4]

   	Auf diese Weise wird die Funktion angezeigt, die aufgerufen wird, wenn in der Tabelle **Registration** eine Einfügung auftritt.

3.  Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

         function insert(item, user, request) {
             var registrationTable = tables.getTable('Registration');
             registrationTable
                 .where({ handle: item.handle })
                 .read({ success: insertRegistrationIfNotFound });
             function insertRegistrationIfNotFound(existingRegistrations) {
                if (existingRegistrations.length > 0) {
                    request.respond(200, existingRegistrations[0]);
                } else {
                    request.execute();
        	    }
    	    }
	    }

   	Dieses Skript überprüft die Tabelle **Registration** auf eine bereits vorhandene Registrierung mit demselben Uri. Die Einfügung wird nur vorgenommen, wenn keine übereinstimmende Registrierung gefunden wurde. Dadurch werden doppelte Registration-Datensätze verhindert.

4.  Klicken Sie auf **TodoItem**, dann auf **Skript**, und wählen Sie **Einfügen** aus.

   	![][5]

5.  Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

         function insert(item, user, request) {
             request.execute({
                 success: function() {
                     // In die Antwort schreiben und dann die Benachrichtigung im Hintergrund senden
                     request.respond();
                     sendNotifications(item.text);
            
                 }
             });
            
         function sendNotifications(item_text) {
             var registrationTable = tables.getTable('Registration');
             registrationTable.read({
                 success: function(registrations) {
                     registrations.forEach(function(registration) {
                         push.gcm.send(registration.handle, item_text, {
                             success: function(response) {
                                 console.log('Push notification sent: ', response);
                             }, error: function (error) {
                                 console.log('Error sending push notification: ', error);
                            }
                        });
                    });
                 }
            });
          }
        }

    Dieses Einfügeskript sendet eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags) an alle Registrierungen, die in der **Registration**-Tabelle gespeichert sind.

Testen der App
--------------

1.  Klicken Sie im Eclipse-Menü **Ausführen** auf **Ausführen**, um die Anwendung auszuführen.

2.  Geben Sie in der App einen sinnvollen Text ein, beispielsweise *Eine neue Mobile Services-Aufgabe hinzufügen*, und klicken Sie dann auf die Schaltfläche **Hinzufügen**.

3.  Im unteren Bereich der Anzeige wird Ihnen kurz ein schwarzes Benachrichtigungsfeld angezeigt.

  	![](./media/mobile-services-android-push-notifications-app-users/mobile-push-icon.png)

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

Nächste Schritte
----------------

Dies bildet den Abschluss der Lernprogramme, welche die Grundlagen der Arbeit mit Pushbenachrichtigungen veranschaulichen. Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-android)
  <br/>Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-android)
  <br/>Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
  <br/>Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.

-   [Verwenden der Android-Clientbibliothek für Mobile Services](/en-us/develop/mobile/how-to-guides/work-with-android-client-library)
  <br/>Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.




<!-- Images. -->
[0]: ./media/mobile-services-android-push-notifications-app-users/mobile-services-selection.png
[1]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-table.png
[2]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-table.png
[3]: ./media/mobile-services-android-push-notifications-app-users/mobile-portal-data-tables-registration.png
[4]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-registration.png
[5]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-push2.png
[6]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-class.png

[27]: ./media/mobile-services-android-push-notifications-app-users/mobile-quickstart-push2-android.png
[28]: ./media/mobile-services-android-push-notifications-app-users/mobile-push-icon.png
