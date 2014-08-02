<properties linkid="" urlDisplayName="" pageTitle="Add paging to data (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin iOS app from Mobile Services." metaCanonical="" services="" authors="" solutions="" manager="" editor="" title="Refine Mobile Services queries with paging" documentationCenter="Mobile" />

Verfeinern von Mobile Services-Abfragen mit Paging
==================================================

[Windows Store C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows Store C#")[Windows Store JavaScript](/en-us/develop/mobile/tutorials/add-paging-to-data-js "Windows Store JavaScript")[Windows Phone](/en-us/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone")[iOS](/en-us/develop/mobile/tutorials/add-paging-to-data-ios "iOS")[Android](/en-us/develop/mobile/tutorials/add-paging-to-data-android "Android")[HTML](/en-us/develop/mobile/tutorials/add-paging-to-data-html "HTML")[iOS C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS")[Android C\#](/en-us/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android")

In diesem Thema erfahren Sie, wie Sie die aus Azure Mobile Services an Ihre iOS-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfrageeigenschaften **Skip** und **Take** im Client, um bestimmte "Seiten" von Daten abzufragen.

**Hinweis**

Mobile Services verwendet standardmäßig eine Seitengröße von 50 Elementen pro Antwort, um mobile Clients nicht mit Daten zu überfluten. Durch Angabe der Seitengröße können Sie bis zu 1.000 Elemente in einer Antwort abfragen.

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios). Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zumindest das erste Lernprogramm aus der Daten-Reihe abschließen, [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios).

1.  Öffnen Sie in Xamarin Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-xamarin-ios) geändert haben.

2.  Drücken Sie die Schaltfläche **Run**, um das Projekt zu erstellen und die App zu starten. Geben Sie anschließend einen Text in das Textfeld ein, und klicken Sie auf das Plus-Symbol (**+**).

3.  Wiederholen Sie den vorherigen Schritt mindestens drei Mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält.

4.  Öffnen Sie die Datei **TodoService.cs**, suchen Sie nach der **RefreshDataAsync**-Methode, und ersetzen Sie die LINQ-Abfrage in der `todoTable` durch die folgende Abfrage:

             Items = await todoTable
                             .Where (todoItem => todoItem.Complete == false)
                            .Take(3)
                            .ToListAsync();

        Diese Abfrage gibt die ersten drei Elemente zurück, die nicht als abgeschlossen markiert sind. 

5.  Erstellen und starten Sie die App.

    Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden.

6.  Aktualisieren Sie die LINQ-Abfrage in der **RefreshDataAsync**-Methode noch einmal folgendermaßen:

             Items = await todoTable
                             .Where (todoItem => todoItem.Complete == false)
                             .Skip(3)
                             .Take(3)
                             .ToListAsync();

        Setzen Sie den Wert **Skip** diesmal auf 3. 

        Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

    **Hinweis**

    Dieses Lernprogramm verwendet zur Vereinfachung fest codierte Werte für die **Skip**- und **Take**-Eigenschaften. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren. Alternativ können Sie die **IncludeTotalCount**-Methode aufrufen, um die Gesamtzahl aller Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.

Nächste Schritte
----------------

Dies bildet den Abschluss der Lernprogramm-Reihe über Grundlagen mit Daten in Mobile Services. Weitere Informationen finden Sie im folgenden Mobile Services-Thema:

-   [Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-xamarin-ios)
    Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

