<properties linkid="develop-mobile-tutorials-add-paging-to-data-android" urlDisplayName="Add paging to data" pageTitle="Add paging to data (Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Android app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="" solutions="" manager="" editor="" />

Verfeinern von Mobile Services-Abfragen mit Paging
==================================================

[Windows Store C\#](/de-de/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows Store C#")[Windows Store JavaScript](/de-de/develop/mobile/tutorials/add-paging-to-data-js "Windows Store JavaScript")[Windows Phone](/de-de/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone")[iOS](/de-de/develop/mobile/tutorials/add-paging-to-data-ios "iOS")[Android](/de-de/develop/mobile/tutorials/add-paging-to-data-android "Android")[HTML](/de-de/develop/mobile/tutorials/add-paging-to-data-html "HTML")[Xamarin.iOS](/de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android")

In diesem Thema erfahren Sie, wie Sie die aus Azure Mobile Services an Ihre Android-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfragemethoden **Top** und **Skip** im Client, um bestimmte "Seiten" von Daten abzufragen.

**Hinweis**

Mobile Services verwendet standardmäßig eine Seitengröße von 50 Elementen pro Antwort, um mobile Clients nicht mit Daten zu überfluten. Durch Angabe der Seitengröße können Sie bis zu 1.000 Elemente in einer Antwort abfragen.

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-android). Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zumindest das erste Lernprogramm aus der Daten-Reihe abschließen, [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-android).

1.  Öffnen Sie in Eclipse das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-android) erstellt haben.

2.  Klicken Sie im Menü **Run** auf **Run**, um die App zu starten, geben Sie einen Text in das Textfeld ein und klicken Sie auf **Add**.

3.  Wiederholen Sie den vorherigen Schritt mindestens drei mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält.

4.  Ersetzen Sie in ToDoActivity.java die Methode **RefreshTodoItems** durch den folgenden Code:

         private void refreshItemsFromTable() {
             // Diese gefilterte Abfrage gibt die ersten 3 Elemente zurück.
             mToDoTable.where().field("complete").eq(false).top(3)
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

   Diese Abfrage gibt die ersten drei Elemente zurück, die nicht als abgeschlossen markiert sind.

5.  Erstellen und starten Sie die App.

    Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden.

2.  (Optional) Sie können die URI der Anfrage an den mobilen Dienst anzeigen, indem Sie Tools zur Nachrichteninspektion verwenden, wie z. B. Browser-Entwicklertools oder [Fiddler].

	Beachten Sie, dass die "take(3)"-Methode in der Abfrage-URI als "$top=3" übersetzt wurde.

3.  Ersetzen Sie die **RefreshTodoItems**-Methode durch den folgenden Code:

         private void refreshItemsFromTable() {
             // Diese gefilterte Abfrage gibt die ersten 3 Elemente zurück.
             mToDoTable.where().field("complete").eq(false).skip(3).top(3)
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

       Diese Abfrage überspringt die ersten drei Ergebnisse und liefert die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

    **Hinweis**

    Dieses Lernprogramm übergibt zur Vereinfachung fest codierte Werte an die **top**- und **skip**-Methoden. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren. Alternativ können Sie die **includeInlineCount**-Methode aufrufen, um die Gesamtzahl der Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.

4.  (Optional) Sehen Sie sich erneut die URI der Anfrage an den mobilen Dienst an.

	Beachten Sie, dass die "skip(3)"-Methode in der Abfrage-URI als "$skip=3" übersetzt wurde.

Nächste Schritte
----------------

Dies bildet den Abschluss der Lernprogramm-Reihe über Grundlagen mit Daten in Mobile Services. Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit der Authentifizierung](/de-de/develop/mobile/tutorials/get-started-with-users-android)
    Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

-   [Erste Schritte mit Pushbenachrichtigungen](/de-de/develop/mobile/tutorials/get-started-with-push-android)
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.


