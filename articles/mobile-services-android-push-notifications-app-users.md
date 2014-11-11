<properties linkid="develop-mobile-tutorials-push-notifications-to-users-android" urlDisplayName="" pageTitle="Push notifications to users (Android ) | Mobile Dev Center" metaKeywords="" description="Learn how to use Mobile Services to push notifications to users of your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Push notifications to users by using Mobile Services" authors="ricksal" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="ricksal" />

# Pushbenachrichtigungen an Benutzer mithilfe von Mobile Services

<div class="dev-center-tutorial-selector sublanding">
    <a href="/de-de/develop/mobile/tutorials/push-notifications-to-users-wp8" title="Windows Phone">Windows Phone</a><a href="/de-de/develop/mobile/tutorials/push-notifications-to-users-ios" title="iOS">iOS</a><a href="/de-de/develop/mobile/tutorials/push-notifications-to-users-android" title="Android" class="current">Android</a>
</div>

<div class="dev-onpage-left-content">
<p>In diesem Thema wird das <a href="/de-de/develop/mobile/tutorials/get-started-with-push-android">vorherige Lernprogramm zu Pushbenachrichtigungen</a> weiter ausgebaut, indem eine neue Tabelle hinzugef&uuml;gt wird. In dieser werden Registrierungs-URIs f&uuml;r Google Cloud Messaging (GCM) gespeichert, welche dann zum Versenden von Pushbenachrichtigungen an mehrere Benutzer der Android-App verwendet werden k&ouml;nnen. In diesem Lernprogramm wird eine einzige Aktualisierung jedes Mal Pushbenachrichtigungen an alle registrierten Ger&auml;te generieren, wenn etwas in die ToDoList-Tabelle eingef&uuml;gt wird. Im vorherigen Lernprogramm wurde nur eine Benachrichtigung an das Ger&auml;t versendet, das die Einf&uuml;gung vorgenommen hat.</p>
</div>

Dieses Lernprogramm zeigt Ihnen die entsprechenden Schritte zur Aktualisierung von Pushbenachrichtigungen in Ihrer App:

1.  [Erstellen der Registrations-Tabelle][Erstellen der Registrations-Tabelle]
2.  [Aktualisieren Ihrer App][Aktualisieren Ihrer App]
3.  [Aktualisieren von Serverskripts][Aktualisieren von Serverskripts]
4.  [Verifizieren des Pushbenachrichtigungs-Verhaltens][Verifizieren des Pushbenachrichtigungs-Verhaltens]

Dieses Lernprogramm baut auf dem Mobile Services-Schnellstart auf und führt das vorherige Lernprogram [Erste Schritte mit Pushbenachrichtigungen][vorherige Lernprogramm zu Pushbenachrichtigungen] weiter fort. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Pushbenachrichtigungen][vorherige Lernprogramm zu Pushbenachrichtigungen] abschließen.

## <a name="create-table"></a>Erstellen einer neuen Tabelle

1.  Melden Sie sich beim [Azure-Verwaltungsportal][Azure-Verwaltungsportal] an. Klicken Sie auf **Mobile Services** und dann auf Ihre App.

    ![][0]

2.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Erstellen**.

    ![][1]

    Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

3.  Behalten Sie die Standardeinstellung **Jeder mit dem Anwendungsschlüssel** für alle Berechtigungen bei, geben Sie *Registration* in das Feld **Tabellenname** ein, und klicken Sie dann auf den Häkchenknopf.

    ![][2]

    Dadurch wird die Tabelle **Registration** erstellt, in der die Registration-URIs gespeichert werden, über die Pushbenachrichtigungen getrennt von Elementdaten gesendet werden.

Als Nächstes modifizieren Sie die Pushbenachrichtigungs-App zur Speicherung von Registrierungsdaten in dieser neuen Tabelle anstatt in der Tabelle **TodoItem**.

## <a name="update-app"></a>Aktualisieren Ihrer App

1.  Klicken Sie in Eclipse im Paket-Explorer mit der rechten Maustaste auf das Paket (unter dem Knoten `src`), klicken Sie auf **Neu** und dann auf **Klasse**.

2.  Geben Sie für **Name** `Registration` ein, und klicken Sie dann auf **Fertig stellen**.

    ![][3]

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

7.  Suchen Sie die `mClient`-Eigenschaft, und ersetzen Sie diese durch folgenden Code:

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

        // Create a new Registration
        Registration registration = new Registration();
        registration.setHandle(gcmRegistrationId);

        // Insert the new Registration
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

## <a name="update-scripts"></a>Aktualisieren von Serverskripts

1.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Daten** und dann auf die Tabelle **Registration**.

    ![][4]

2.  Klicken Sie unter **Registration** auf die Registerkarte **Skript**, und wählen Sie **Einfügen** aus.

    ![][5]

    Auf diese Weise wird die Funktion angezeigt, die aufgerufen wird, wenn in der Tabelle **Registration** eine Einfügung vorgenommen wird.

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

    Dieses Skript überprüft die Tabelle **Registration** auf eine bereits vorhandene Registrierung mit demselben URI. Die Einfügung wird nur vorgenommen, wenn keine übereinstimmende Registrierung gefunden wurde. Dadurch werden doppelte Registration-Datensätze verhindert.

4.  Klicken Sie auf **TodoItem**, dann auf **Script**, und wählen Sie **Insert** aus.

    ![][6]

5.  Ersetzen Sie die Einfügefunktion durch den folgenden Code. Klicken Sie dann auf **Speichern**:

        function insert(item, user, request) {
            request.execute({
                success: function() {
                    // Write to the response and then send the notification in the background
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
                            }, error: function(error) {
                                console.log('Error sending push notification: ', error);
                            }
                        });
                    });
                }
            });
        }

    }

    Dieses Einfügeskript sendet eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags) an alle Registrierungen, die in der **Registration**-Tabelle gespeichert sind.

## <a name="test-app"></a>Testen der App

1.  Klicken Sie im Eclipse-Menü **Ausführen** auf **Ausführen**, um die Anwendung auszuführen.

2.  Geben Sie in der App einen sinnvollen Text ein, beispielsweise *Eine neue Mobile Services-Aufgabe hinzufügen*, und klicken Sie dann auf die Schaltfläche **Hinzufügen**.

3.  Im unteren Bereich der Anzeige wird Ihnen kurz ein schwarzes Benachrichtigungsfeld angezeigt.

    ![][7]

<!--7. Tap on the icon and swipe down to display the notification, which appears in the graphic below.      ![][27]-->

Sie haben dieses Lernprogramm erfolgreich abgeschlossen.

## Nächste Schritte

Dies bildet den Abschluss der Lernprogramme, welche die Grundlagen der Arbeit mit Pushbenachrichtigungen veranschaulichen. Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit Daten][Erste Schritte mit Daten]
    Informationen zum Speichern und Abfragen von Daten mit Mobile Services.

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

-   [Mobile Services: Serverskriptreferenz][Mobile Services: Serverskriptreferenz]
    Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.

-   [Verwenden der Android-Clientbibliothek für Mobile Services][Verwenden der Android-Clientbibliothek für Mobile Services]
    Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.



  [vorherige Lernprogramm zu Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-android
  [Erstellen der Registrations-Tabelle]: #create-table
  [Aktualisieren Ihrer App]: #update-app
  [Aktualisieren von Serverskripts]: #update-scripts
  [Verifizieren des Pushbenachrichtigungs-Verhaltens]: #test-app
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [0]: ./media/mobile-services-android-push-notifications-app-users/mobile-services-selection.png
  [1]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-table.png
  [2]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-table.png
  [3]: ./media/mobile-services-android-push-notifications-app-users/mobile-create-registration-class.png
  [4]: ./media/mobile-services-android-push-notifications-app-users/mobile-portal-data-tables-registration.png
  [5]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-registration.png
  [6]: ./media/mobile-services-android-push-notifications-app-users/mobile-insert-script-push2.png
  [7]: ./media/mobile-services-android-push-notifications-app-users/mobile-push-icon.png
  [Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-android
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-android
  [Mobile Services: Serverskriptreferenz]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Verwenden der Android-Clientbibliothek für Mobile Services]: /de-de/develop/mobile/how-to-guides/work-with-android-client-library
