<properties linkid="develop-mobile-tutorials-validate-modify-and-augment-data-android" urlDisplayName="Validate Data - Android" pageTitle="Use server scripts to validate and modify data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to validate and modify data sent using server scripts from your Android app." metaCanonical="" services="" documentationCenter="Mobile" title="Validate and modify data in Mobile Services by using server scripts" authors="" solutions="" manager="" editor="" />

Überprüfen und Ändern von Daten in Mobile Services mithilfe von Serverskripts
=============================================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-android "Xamarin.Android")

In diesem Thema erfahren Sie, wie Sie Serverskripts in Azure Mobile Services nutzen. Serverskripts sind in einem mobilen Dienst registriert und können verwendet werden, um eine Vielzahl an Vorgängen für Daten, die eingefügt und aktualisiert werden, durchzuführen, einschließlich Überprüfen und Ändern. In diesem Lernprogramm definieren und registrieren Sie Serverskripts, die Daten prüfen und ändern. Da sich das Verhalten von serverseitigen Skripts häufig auf den Client auswirkt, werden Sie auch Ihre Android-App aktualisieren, um von diesen neuen Verhalten zu profitieren.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Überprüfung der Zeichenfolgenlänge hinzufügen](#string-length-validation)
2.  [Client zur Unterstützung der Überprüfung aktualisieren](#update-client-validation)
3.  [Zeitstempel beim Einfügen hinzufügen](#add-timestamp)
4.  [Client zum Anzeigen des Zeitstempels aktualisieren](#update-client-timestamp)

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-android). Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-android) abschließen.

Überprüfung hinzufügen
----------------------

Es empfiehlt sich, die Länge der Daten, die von Benutzern übermittelt werden, zu überprüfen. Zunächst registrieren Sie ein Skript, das die Länge von Zeichenfolgendendaten überprüft, die an den mobilen Dienst gesendet werden, und das Zeichenfolgen ablehnt, die zu lang sind, in diesem Fall länger als zehn Zeichen.

1.  Melden Sie sich beim [Azure-Verwaltungsportal](https://manage.windowsazure.com/) an. Klicken Sie auf **Mobile Services** und dann auf Ihre App.

       ![][0]

2.  Klicken Sie auf die Registerkarte **Data**, dann auf die Tabelle **TodoItem**.

       ![][1]

3.  Klicken Sie auf **Skript** und wählen Sie die **Einfügen**-Operation aus.

       ![][2]

4.  Ersetzen Sie das vorhandene Skript durch die folgende Funktion, und klicken Sie dann auf **Save**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be 10 characters or less.');
             } else {
                 request.execute();
             }
         }

    Das Skript überprüft die Länge der Eigenschaft **text** und sendet eine Fehlerantwort, wenn die Länge zehn Zeichen überschreitet. Andernfalls wird die Methode **execute** aufgerufen, um das Einfügen abzuschließen.

    **Hinweis**

    Sie können ein registriertes Skript auf der Registerkarte **Script** entfernen, indem Sie auf **Clear** und dann auf **Save** klicken.

Aktualisieren des Clients
-------------------------

Der mobile Dienst überprüft nun Daten und sendet Fehlerantworten. Sie müssen nun sicherstellen, dass Ihre App die Fehlerantworten aus der Validierung korrekt behandelt.

1.  Öffnen Sie in Eclipse das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-android) erstellt haben.

2.  Suchen Sie in der Datei ToDoActivity.java die Methode **addItem** und ersetzen Sie den Aufruf der createAndShowDialog-Methode durch den folgenden Code:

         createAndShowDialog(exception.getCause().getMessage(), "Error");

    Dieser Code zeigt die vom mobilen Dienst zurückgegebene Fehlermeldung an.

3.  Klicken Sie im Menü **Run** auf **Run**, um die App zu starten, geben Sie einen Text mit mehr als 10 Zeichen in das Textfeld ein und klicken Sie auf **Add**.

  Beachten Sie, dass der Fehler behandelt und die Fehlermeldung für den Benutzer angezeigt wird.

Hinzufügen eines Zeitstempels
-----------------------------

In der vorherigen Aufgabe wurde eine Einfügung überprüft und entweder akzeptiert oder abgelehnt. Nun aktualisieren Sie die eingefügten Daten mithilfe eines Serverskripts, das eine Zeitstempeleigenschaft zu dem Objekt hinzufügt, bevor es eingefügt wird.

**Hinweis**

Die hier demonstrierte Zeitstempeleigenschaft **createdAt** ist nun redundant. Mobile Services erstellt automatisch eine Systemeigenschaft **\_\_createdAt** für jede Tabelle.

1.  Ersetzen Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf der Registerkarte **Scripts** das aktuelle Skript **Insert** durch die folgende Funktion und klicken Sie dann auf **Save**.

         function insert(item, user, request) {
             if (item.text.length > 10) {
                 request.respond(statusCodes.BAD_REQUEST, 'Text length must be under 10');
             } else {
                 item.createdAt = new Date();
                 request.execute();
             }
         }

    Diese Funktion erweitert das vorherige insert-Skript, indem es eine neue Zeitstempeleigenschaft **createdAt** zum Objekt hinzufügt, bevor es durch den **request**.**execute**-Aufruf eingefügt wird.

    **Hinweis**

    Wenn dieses insert-Skript zum ersten Mal ausgeführt wird, muss das dynamische Schema aktiviert sein. Wenn das dynamische Schema aktiviert ist, fügt Mobile Services automatisch die Spalte **createdAt** in die Tabelle **TodoItem** bei der ersten Ausführung ein. Das dynamische Schema ist standardmäßig für einen neuen Mobile Service aktiviert und sollte deaktiviert werden, bevor die App veröffentlicht wird.

2.  Klicken Sie im Menü **Run** auf **Run**, um die App zu starten, geben Sie einen Text mit weniger als 10 Zeichen in das Textfeld ein und klicken Sie auf **Add**.

       Beachten Sie, dass der neue Zeitstempel nicht in der UI der App angezeigt wird.

3.  Klicken Sie im Verwaltungsportal auf die Registerkarte **Browse** in der Tabelle **todoitem**.

       Beachten Sie, dass es hier nun ein Spalte **createdAt** gibt und das neu eingefügte Element über einen Zeitstempelwert verfügt.

Nun müssen Sie die Android-App aktualisieren, um diese neue Spalte anzuzeigen.

Den Client erneut aktualisieren
-------------------------------

Der Mobile Service-Client ignoriert alle Daten in einer Antwort, die er nicht in Eigenschaften gemäß dem definierten Typ serialisieren kann. Abschließend muss dann der Client aktualisiert werden, um diese neuen Daten anzuzeigen.

1.  Öffnen Sie die Datei ToDoItem.java im Paket-Explorer und fügen Sie die folgende **Import**-Anweisung hinzu:

         import java.util.Date;

2.  Fügen Sie den folgenden Code zu den Definitionen der privaten Felder in der Klasse **TodoItem** hinzu:

         /**
          * Zeitstempel des vom Dienst eingefügten Elements.
          */
         @com.google.gson.annotations.SerializedName("createdAt")
         private Date mCreatedAt;

    **Hinweis**

    Die `SerializedName`-Annotation weist den Client an, die neue Eigenschaft `mCreatedAt` in der App zu der in der TodoItem-Tabelle definierten Spalte `createdAt` zuzuordnen, die einen anderen Namen hat. Durch die Verwendung dieser Annotation kann Ihre App Eigenschaftennamen zu Objekten haben, die von den Spaltennamen in der SQL-Datenbank abweichen. Ohne diese Annotation tritt aufgrund der unterschiedlichen Schreibweisen ein Fehler auf.

3.  Fügen Sie die folgenden Methoden zur ToDoItem-Klasse hinzu, um die neue mCreatedAt-Eigenschaft abzurufen und zu ändern:

         /**
          * Setzt den Zeitstempel.
          * 
          * @param date
          *            timestamp to set
          */
         public final void setCreatedAt(Date date) {
             mCreatedAt = date;
         }
            
         /**
          * Gibt den Zeitstempel zurück.
          */
         public Date getCreatedAt() {
             return mCreatedAt;
         }

4.  Öffnen Sie die Datei ToDoItemAdapter.java im Paket-Explorer und fügen Sie die folgende **Import**-Anweisung hinzu:

         import java.text.DateFormat;

5.  Fügen Sie in der GetView-Methode den folgenden Code hinzu:

         String createdAtText = "";
         if (currentItem.getCreatedAt() != null){
             DateFormat formatter = DateFormat.getDateInstance(DateFormat.SHORT);
             createdAtText = formatter.format(currentItem.getCreatedAt());
         }

        Erzeugt eine formatierte Datums-Zeichenfolge, wenn ein Zeitstempelwert existiert. 

6.  Suchen Sie den Code `checkBox.setText(currentItem.getText());` und ersetzen Sie diese Codezeile durch den folgenden Code:

         checkBox.setText(currentItem.getText() + " " + createdAtText);

    Dieser Code hängt den Zeitstempelwert zur Anzeige an das Element an.

7.  Klicken Sie im Menü **Run** auf **Run**, um die App zu starten.

       Beachten Sie, dass der Zeitstempel für eingefügte Elemente nur angezeigt wird, nachdem Sie das insert-Skript aktualisiert haben.

8.  Ersetzen Sie die vorhandene Methode **RefreshItemsFromTable** durch den folgenden Code:

         private void refreshItemsFromTable() {
                
             mToDoTable.where().field("complete").eq(false).and().field("createdAt").ne((String)null)
                     .execute(new TableQueryCallback<ToDoItem>() {

                         public void onCompleted(List<ToDoItem> result, int count,
                                 Exception exception, ServiceFilterResponse response) {
                             if (exception == null) {
                                 mAdapter.clear();

                                 for (ToDoItem item : result) {
                                     mAdapter.add(item);
                                 }

                             } else {
                                 createAndShowDialog(exception, "Error");
                             }
                         }
                     });
         }

        Diese Methode aktualisiert die Abfrage und filtert auch Elemente heraus, die keinen Zeitstempelwert haben.

9.  Klicken Sie im Menü **Run** auf **Run**, um die App zu starten.

       Beachten Sie, dass alle Elemente, die ohne Zeitstempelwert erstellt wurden, nicht mehr in der UI angezeigt werden.

Sie haben dieses Lernprogramm zum Arbeiten mit Daten abgeschlossen.

Nächste Schritte
----------------

Da Sie dieses Lernprogramm nun abgeschlossen haben, können Sie mit dem letzten Lernprogramm der Datenreihe fortfahren: [Optimieren von Abfragen mit Paging](/en-us/develop/mobile/tutorials/add-paging-to-data-android).

Serverskripts werden auch zum Autorisieren von Benutzern und zum Senden von Pushbenachrichtigungen verwendet. Weitere Informationen finden Sie in den folgenden Lernprogrammen:

-   [Autorisieren von Benutzern mit Skripts](/en-us/develop/mobile/tutorials/authorize-users-in-scripts-android)
    Erfahren Sie, wie Sie Daten basierend auf der ID eines authentifizierten Benutzers filtern.

-   [Erste Schritte mit Pushbenachrichtigungen](/en-us/develop/mobile/tutorials/get-started-with-push-android)
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services: Serverskriptreferenz](http://go.microsoft.com/fwlink/?LinkId=262293)
    Lernen Sie mehr über das Registrieren und Verwenden von Serverskripts.


<!-- Images. -->
[0]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-services-selection.png
[1]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-portal-data-tables.png
[2]: ./media/mobile-services-android-validate-modify-data-server-scripts/mobile-insert-script-users.png
