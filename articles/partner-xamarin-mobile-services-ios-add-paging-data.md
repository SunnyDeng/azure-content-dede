<properties linkid="develop-mobile-tutorials-add-paging-to-data-xamarin-ios" urlDisplayName="" pageTitle="Add paging to data (Xamarin iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Xamarin iOS app from Mobile Services." metaCanonical="" services="" authors="donnam" solutions="" manager="dwrede" editor="" title="Refine Mobile Services queries with paging" documentationCenter="Mobile" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-xamarin-ios" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="donnam"></tags>

# Verfeinern von Mobile Services-Abfragen mit Paging

<div class="dev-center-tutorial-selector sublanding"> 
    <a href="/de-de/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS">iOS</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-android" title="Android">Android</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-html" title="HTML">HTML</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">iOS C#</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android">Android C#</a>
</div>

In diesem Thema erfahren Sie, wie Sie die aus Azure Mobile Services an Ihre iOS-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfrageeigenschaften **Skip** und **Take** im Client, um bestimmte "Seiten" von Daten abzufragen.

<div class="dev-callout"><b>Hinweis</b>
<p>Mobile Services verwendet standardm&auml;&szlig;ig eine Seitengr&ouml;&szlig;e von 50 Elementen pro Antwort, um mobile Clients nicht mit Daten zu &uuml;berfluten. Durch Angabe der Seitengr&ouml;&szlig;e k&ouml;nnen Sie bis zu 1.000 Elemente in einer Antwort abfragen.</p>
</div>

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten][Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zumindest das erste Lernprogramm aus der Daten-Reihe abschließen, [Erste Schritte mit Daten][Erste Schritte mit Daten].

1.  Öffnen Sie in Xamarin Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten][Erste Schritte mit Daten] geändert haben.

2.  Drücken Sie die Schaltfläche **Run**, um das Projekt zu erstellen und die App zu starten. Geben Sie anschließend einen Text in das Textfeld ein, und klicken Sie auf das Plus-Symbol (**+**).

3.  Wiederholen Sie den vorherigen Schritt mindestens drei Mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält.

4.  Öffnen Sie die Datei **TodoService.cs**, suchen Sie nach der **RefreshDataAsync**-Methode, und ersetzen Sie die LINQ-Abfrage in `todoTable` durch die folgende Abfrage:

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

    Setzen Sie den **Skip**-Wert dieses Mal auf 3.

    Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

    <div class="dev-callout"><b>Hinweis</b>
<p>Dieses Lernprogramm verwendet zur Vereinfachung fest codierte Werte f&uuml;r die <strong>Skip</strong>- und <strong>Take</strong>-Eigenschaften. Tats&auml;chliche Anwendungen k&ouml;nnen &auml;hnliche Abfragen mit einem Pagersteuerelement oder einer &auml;hnlichen Benutzersteuerung ausf&uuml;hren, um zur vorherigen bzw. n&auml;chsten Seite zu navigieren. Alternativ k&ouml;nnen Sie die <strong>IncludeTotalCount</strong>-Methode aufrufen, um die Gesamtzahl aller Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.</p>
</div>

## <a name="next-steps"> </a>Nächste Schritte

Dies bildet den Abschluss der Lernprogramm-Reihe über Grundlagen mit Daten in Mobile Services. Weitere Informationen finden Sie im folgenden Mobile Services-Thema:

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

<!-- * [Get started with push notifications]    <br/>Learn how to send a very basic push notification to your app. --> <!-- Anchors. --> <!-- Images. --> <!-- URLs. -->

  [Windows Store C#]: /de-de/develop/mobile/tutorials/add-paging-to-data-dotnet "Windows Store C#"
  [Windows Store JavaScript]: /de-de/develop/mobile/tutorials/add-paging-to-data-js "Windows Store JavaScript"
  [Windows Phone]: /de-de/develop/mobile/tutorials/add-paging-to-data-wp8 "Windows Phone"
  [iOS]: /de-de/develop/mobile/tutorials/add-paging-to-data-ios "iOS"
  [Android]: /de-de/develop/mobile/tutorials/add-paging-to-data-android "Android"
  [HTML]: /de-de/develop/mobile/tutorials/add-paging-to-data-html "HTML"
  [iOS C#]: /de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-ios "Xamarin.iOS"
  [Android C#]: /de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-android "Xamarin.Android"
  [Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-xamarin-ios
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-xamarin-ios
