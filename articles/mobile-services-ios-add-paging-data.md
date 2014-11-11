<properties linkid="develop-mobile-tutorials-add-paging-to-data-ios" urlDisplayName="Add paging to data" pageTitle="Add paging to data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your iOS app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="krisragh" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-ios" ms.devlang="objective-c" ms.topic="article" ms.date="01/01/1900" ms.author="krisragh" />

# Verfeinern von Mobile Services-Abfragen mit Paging

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-dotnet" title="Windows Store C#">Windows Store C#</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-js" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-wp8" title="Windows Phone">Windows Phone</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-ios" title="iOS" class="current">iOS</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-android" title="Android" class="current">Android</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-html" title="HTML" class="current">HTML</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-ios" title="Xamarin.iOS" class="current">Xamarin.iOS</a><a href="/de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-android" title="Xamarin.Android">Xamarin.Android</a></div>

In diesem Thema erfahren Sie, wie Sie die aus Azure Mobile Services an Ihre iOS-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfrageeigenschaften **fetchLimit** und **fetchOffset** im Client, um bestimmte "Seiten" von Daten abzufragen.

<div class="dev-callout"><b>Hinweis</b>
<p>Mobile Services verwendet standardm&auml;&szlig;ig eine Seitengr&ouml;&szlig;e von 50 Elementen pro Antwort, um mobile Clients nicht mit Daten zu &uuml;berfluten. Durch Angabe der Seitengr&ouml;&szlig;e k&ouml;nnen Sie bis zu 1.000 Elemente in einer Antwort abfragen.</p>
</div>

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten][Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zumindest das erste Lernprogramm aus der Daten-Reihe abschließen, [Erste Schritte mit Daten][Erste Schritte mit Daten].

1.  Öffnen Sie in Xcode das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten][Erste Schritte mit Daten] verändert haben.

2.  Drücken Sie die **Run**-Taste (Command + R), um das Projekt zu erstellen und die App zu starten. Geben Sie anschließend einen Text in das Textfeld ein und klicken Sie auf das (**+**)-Symbol.

3.  Wiederholen Sie den vorherigen Schritt mindestens drei Mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält.

4.  Öffnen Sie die Datei QSTodoService.m und suchen Sie die folgende Methode:

        - (void)refreshDataOnSuccess:(QSCompletionBlock)completion

    Ersetzen Sie den gesamten Methodentext durch den folgenden Code.

        // Create a predicate that finds active items in which complete is false
        NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

        // Retrieve the MSTable's MSQuery instance with the predicate you just created.
        MSQuery * query = [self.table queryWithPredicate:predicate];

        query.includeTotalCount = YES; // Request the total item count

        // Start with the first item, and retrieve only three items
        query.fetchOffset = 0;
        query.fetchLimit = 3;

        // Invoke the MSQuery instance directly, rather than using the MSTable helper methods.
        [query readWithCompletion:^(NSArray *results, NSInteger totalCount, NSError *error) {

            [self logErrorIfNotNil:error];
            if (!error)
            {
                // Log total count.
                NSLog(@"Total item count: %@",[NSString stringWithFormat:@"%zd", (ssize_t) totalCount]);            
            }

            items = [results mutableCopy];

            // Let the caller know that we finished
            completion();
        }];

    Diese Abfrage gibt die ersten drei Elemente zurück, die nicht als abgeschlossen markiert sind.

5.  Erstellen und starten Sie die App.

    Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden.

6.  Ändern Sie die **refreshDataOnSuccess**-Methode, indem Sie die folgende Codezeile suchen:

        query.fetchOffset = 0;

    Setzen Sie den **query.fetchOffset**-Wert dieses Mal auf 3.

    Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

    <div class="dev-callout"><b>Hinweis</b>
<p>Dieses Lernprogramm verwendet zur Vereinfachung fest codierte Werte f&uuml;r die <strong>fetchOffset</strong>- und <strong>fetchLimit</strong>-Eigenschaften. Tats&auml;chliche Anwendungen k&ouml;nnen &auml;hnliche Abfragen mit einem Pagersteuerelement oder einer &auml;hnlichen Benutzersteuerung ausf&uuml;hren, um zur vorherigen bzw. n&auml;chsten Seite zu navigieren. Alternativ k&ouml;nnen Sie **query.includeTotalCount = YES** verwenden, um die Gesamtzahl aller Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.</p>
</div>

## <a name="next-steps"> </a>Nächste Schritte

Dies bildet den Abschluss der Lernprogramm-Reihe über Grundlagen mit Daten in Mobile Services. Weitere Informationen finden Sie im folgenden Mobile Services-Thema:

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

<!-- * [Get started with push notifications]    <br/>Learn how to send a very basic push notification to your app. --> 

  [Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-ios
  [Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-ios
