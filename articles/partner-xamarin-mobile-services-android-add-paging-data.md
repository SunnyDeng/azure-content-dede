<properties linkid="develop-mobile-tutorials-add-paging-to-data-xamarin-android" urlDisplayName="Add paging to data" pageTitle="Add paging to data (Xamarin Android) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin Android app from Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="donnam" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-android" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam" />

# Verfeinern von Mobile Services-Abfragen mit Paging

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/de-de/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-html" title="HTML">HTML</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS">iOS C#</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android" class="current">Android C#</a>
</div>

In diesem Thema erfahren Sie, wie Sie die aus Azure Mobile Services an Ihre Xamarin.Android-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfragemethoden **Take** und **Skip** im Client, um bestimmte "Seiten" von Daten abzufragen.

<div class="dev-callout"><b>Hinweis</b>
<p>Mobile Services verwendet standardm&auml;&szlig;ig eine Seitengr&ouml;&szlig;e von 50 Elementen pro Antwort, um mobile Clients nicht mit Daten zu &uuml;berfluten. Durch Angabe der Seitengr&ouml;&szlig;e k&ouml;nnen Sie bis zu 1.000 Elemente in einer Antwort abfragen.</p>
</div>

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten][Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zumindest das erste Lernprogramm aus der Daten-Reihe abschließen, [Erste Schritte mit Daten][Erste Schritte mit Daten].

1.  Öffnen Sie in Xamarin Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten][Erste Schritte mit Daten] erstellt haben.

2.  Klicken Sie auf **Run**, um die App zu starten, geben Sie einen Text in das Textfeld ein, und klicken Sie auf **Add**.

3.  Wiederholen Sie den vorherigen Schritt mindestens drei Mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält.

4.  Ersetzen Sie in der Datei **TodoActivity.cs** die LING-Abfrage in der **RefreshItemsFromTableAsync**-Methode durch die folgende Abfrage:

        var list = await todoTable.Where(item => item.Complete == false)
                                  .Take(3)
                                  .ToListAsync();

    Diese Abfrage gibt die ersten drei Elemente zurück, die nicht als abgeschlossen markiert sind.

5.  Erstellen und starten Sie die App.

    Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden.

6.  (Optional) Sie können die URI der Anfrage an den mobilen Dienst anzeigen, indem Sie Tools zur Nachrichteninspektion verwenden, wie z. B. Browser-Entwicklertools oder [Fiddler].

    Beachten Sie, dass die `Take(3)`-Methode im Abfrage-URI in die Abfrageoption `$top=3` übersetzt wurde.

7.  Aktualisieren Sie die LINQ-Abfrage in der **RefreshItemsFromTableAsync**-Methode mit der folgenden Abfrage:

            var list = await todoTable.Where(item => item.Complete == false)
                                      .Skip(3)
                                      .Take(3)
                                      .ToListAsync();

    Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

    <div class="dev-callout"><b>Hinweis</b>
<p>Dieses Lernprogramm &uuml;bergibt zur Vereinfachung fest codierte Werte an die <strong>Take</strong>- und <strong>Skip</strong>-Methoden. Tats&auml;chliche Anwendungen k&ouml;nnen &auml;hnliche Abfragen mit einem Pagersteuerelement oder einer &auml;hnlichen Benutzersteuerung ausf&uuml;hren, um zur vorherigen bzw. n&auml;chsten Seite zu navigieren. Alternativ k&ouml;nnen Sie die <strong>IncludeTotalCount</strong>-Methode aufrufen, um die Gesamtzahl der Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.</p>
</div>

8.  (Optional) Sehen Sie sich erneut den URI der Anfrage an den mobilen Service an.

    Beachten Sie, dass die `Skip(3)`-Methode im Abfrage-URI in die Abfrageoption `$skip=3` übersetzt wurde.

## <a name="next-steps"> </a>Nächste Schritte

Dies bildet den Abschluss der Lernprogramm-Reihe über Grundlagen mit Daten in Mobile Services. Weitere Informationen zu Mobile Services:

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.



  [Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-xamarin-android
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-xamarin-android
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-xamarin-android
