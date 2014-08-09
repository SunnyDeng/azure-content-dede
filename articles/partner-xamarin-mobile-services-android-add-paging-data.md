<properties linkid="develop-mobile-tutorials-add-paging-to-data-xamarin-android" urlDisplayName="Add paging to data" pageTitle="Add paging to data (Xamarin Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin Android app from Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="" />

Verfeinern von Mobile Services-Abfragen mit Paging
==================================================

[Windows Store C\#](/de-de/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows Store C#")[Windows Store JavaScript](/de-de/develop/mobile/tutorials/add-paging-to-data-js "Windows Store JavaScript")[Windows Phone](/de-de/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone")[iOS](/de-de/develop/mobile/tutorials/add-paging-to-data-ios "iOS")[Android](/de-de/develop/mobile/tutorials/add-paging-to-data-android "Android")[HTML](/de-de/develop/mobile/tutorials/add-paging-to-data-html "HTML")[iOS C\#](/de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS")[Android C\#](/de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android")

In diesem Thema erfahren Sie, wie Sie die aus Azure Mobile Services an Ihre Xamarin.Android-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die **Take** und **Skip**-Abfragemethoden im Client, um bestimmte "Seiten" von Daten abzufragen.

**Hinweis**

Mobile Services verwendet standardmäßig eine Seitengröße von 50 Elementen pro Antwort, um mobile Clients nicht mit Daten zu überfluten. Durch Angabe der Seitengröße können Sie bis zu 1.000 Elemente in einer Antwort abfragen.

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-xamarin-android). Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zumindest das erste Lernprogramm aus der Daten-Reihe abschließen, [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-xamarin-android).

1.  Öffnen Sie in Xamarin Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten](/de-de/develop/mobile/tutorials/get-started-with-data-xamarin-android) erstellt haben.

2.  Klicken Sie auf **Run**, um die App zu starten, geben Sie einen Text in das Textfeld ein, und klicken Sie auf **Add**.

3.  Wiederholen Sie den vorherigen Schritt mindestens drei Mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält.

4.  Ersetzen Sie in der Datei **TodoActivity.cs** die LING-Abfrage in der **RefreshItemsFromTableAsync**-Methode durch die folgende Abfrage:

         var list = await todoTable.Where(item => item.Complete == false)
                                   .Take(3)
                                   .ToListAsync();


	Diese Abfrage gibt die ersten drei Elemente zurück, die nicht als abgeschlossen markiert sind.

5.  Erstellen und starten Sie die App.

    Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden.

6.  (Optional) Sie können den URI der Anfrage an den mobilen Dienst anzeigen, indem Sie Tools zur Nachrichteninspektion verwenden, wie z. B. Browser-Entwicklertools oder [Fiddler].

        Beachten Sie, dass die "Take(3)"-Methode im Abfrage-URI als "$top=3" übersetzt wurde.

7.  Aktualisieren Sie die LINQ-Abfrage in der **RefreshItemsFromTableAsync**-Methode mit der folgenden Abfrage:

             var list = await todoTable.Where(item => item.Complete == false)
                                       .Skip(3)
                                       .Take(3)
                                      .ToListAsync();

	Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

    **Hinweis**

    Dieses Lernprogramm übergibt zur Vereinfachung fest codierte Werte an die **Take**- und **Skip**-Methoden. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren. Alternativ können Sie die **IncludeTotalCount**-Methode aufrufen, um die Gesamtzahl der Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.

8.  (Optional) Sehen Sie sich erneut den URI der Anfrage an den mobilen Dienst an.

	Beachten Sie, dass die "Skip(3)"-Methode in der Abfrage-URI als "$skip=3" übersetzt wurde.

Nächste Schritte
----------------

Dies bildet den Abschluss der Lernprogramm-Reihe über Grundlagen mit Daten in Mobile Services. Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit der Authentifizierung](/de-de/develop/mobile/tutorials/get-started-with-users-xamarin-android)
    Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

-   [Erste Schritte mit Pushbenachrichtigungen](/de-de/develop/mobile/tutorials/get-started-with-push-xamarin-android)
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.


