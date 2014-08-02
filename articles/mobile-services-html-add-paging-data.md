<properties linkid="develop-mobile-tutorials-add-paging-to-data-html" urlDisplayName="Add paging to data (HTML5)" pageTitle="Add paging to data (HTML 5) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your HTML app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

Verfeinern von Mobile Services-Abfragen mit Paging
==================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/add-paging-to-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/add-paging-to-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/add-paging-to-data-html "HTML")[Xamarin.iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android")

In diesem Thema erfahren Sie, wie Sie die aus Azure Mobile Services an Ihre HTML-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfragemethoden **Take** und **Skip** im Client, um bestimmte "Seiten" von Daten abzufragen.

**Hinweis**

Mobile Services verwendet standardmäßig eine Seitengröße von 50 Elementen pro Antwort, um mobile Clients nicht mit Daten zu überfluten. Durch Angabe der Seitengröße können Sie bis zu 1.000 Elemente in einer Antwort abfragen.

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-html). Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zumindest das erste Lernprogramm aus der Daten-Reihe abschließen, [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-html).

1.  Führen Sie eine der folgenden Befehlsdateien aus dem **server** Unterordner des Projekts aus, das Sie geändert haben, als Sie das Lernprogramm [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-html) abgeschlossen haben.

    -   **launch-windows** (Windows-Computer)
    -   **launch-mac.command** (Mac OS X-Computer)
    -   **launch-linux.sh** (Linux-Computer)

    **Hinweis**

    Bei einem Windows-Computer geben Sie "R" ein, wenn Sie von PowerShell zur Bestätigung aufgefordert werden, dass Sie das Skript ausführen möchten. Ihr Webbrowser rät Ihnen unter Umständen von einer Ausführung des Skripts ab, weil es vom Internet heruntergeladenen wurde. In diesem Fall müssen Sie darauf bestehen, dass der Browser mit dem Laden des Skripts fortfährt.

    Daraufhin wird ein Webserver auf Ihrem lokalen Computer zum Hosten der App gestartet.

2.  Öffnen Sie <http://localhost:8000/> in einem Webbrowser, geben Sie Text unter **Add new task** ein und klicken Sie auf **Add**.

3.  Wiederholen Sie den vorherigen Schritt mindestens drei mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält.

4.  Ersetzen Sie in app.js die vorhandene Definition der `query` Variable in der **refreshTodoItems** Methode durch den folgenden Code:

         var query = todoItemTable.where({ complete: false }).take(3);

  Diese Abfrage gibt die ersten drei Elemente zurück, die nicht als abgeschlossen markiert sind.

1.  Aktualisieren Sie die Seite in Ihrem Webbrowser.

  Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden.

1.  (Optional) Sie können die URI der Anfrage an den mobilen Dienst anzeigen, indem Sie    Tools zur Nachrichteninspektion verwenden, wie z. B. Browser-Entwicklertools oder [Fiddler].

      Beachten Sie, dass die **take(3)** Methode in der Abfrage-URI als **$top=3** übersetzt wurde.

2.  Ersetzen Sie die Abfrage durch den folgenden Code:

         var query = todoItemTable.where({ complete: false }).skip(3).take(3);

3.  Aktualisieren Sie die Seite in Ihrem Webbrowser.

      Diese Abfrage überspringt die ersten drei Ergebnisse und liefert die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

    **Hinweis**

    Dieses Lernprogramm übergibt zur Vereinfachung fest codierte Werte an die **Take** und **Skip** Methoden. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren. Alternativ können Sie die **includeTotalCount** Methode aufrufen, um die Gesamtzahl der Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.

4.  (Optional) Sehen Sie sich erneut die URI der Anfrage an den mobilen Dienst an.

      Beachten Sie, dass die **skip(3)** Methode in der Abfrage-URI als **$skip=3** übersetzt wurde.

Nächste Schritte
----------------

Dies bildet den Abschluss der Lernprogramm-Reihe über Grundlagen mit Daten in Mobile Services. Lernen Sie unter [Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-html), wie Sie Benutzer Ihrer App authentifizieren können. Weitere Informationen zur Verwendung von mobilen Diensten mit HTML/JavaScript finden Sie unter [Mobile Services HTML/JavaScript How-to Conceptual Reference](/en-us/develop/mobile/how-to-guides/work-with-html-js-client)

