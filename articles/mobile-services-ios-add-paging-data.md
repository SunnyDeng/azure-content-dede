<properties 
	pageTitle="Hinzufügen von Paging für Daten (iOS) | Mobile Developer Center" 
	description="Erfahren Sie, wie Sie mithilfe des Auslagerns die Menge der von Ihrer iOS-App von Mobile Services zurückgegebenen Daten verwalten können." 
	services="mobile-services" 
	documentationCenter="ios" 
	authors="krisragh" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-ios" 
	ms.devlang="objective-c" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="krisragh"/>

# Verfeinern von Mobile Services-Abfragen mit Paging

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

In diesem Thema wird gezeigt, wie Sie die aus Azure Mobile Services an Ihre iOS-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfrageeigenschaften **fetchLimit** und **fetchOffset** im Client, um bestimmte "Seiten" von Daten abzufragen.

> [AZURE.NOTE] Mobile Services verwendet standardmäßig eine Seitengröße von 50 Elementen pro Antwort, um mobile Clients nicht mit Daten zu überfluten. Durch Angabe der Seitengröße können Sie bis zu 1.000 Elemente in einer Antwort abfragen.

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zumindest das erste Lernprogramm aus der Daten-Reihe abschließen, [Erste Schritte mit Daten].

1. Öffnen Sie in Xcode das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten] verändert haben.

2. Drücken Sie die **Run**-Taste (Command + R), um das Projekt zu erstellen und die App zu starten. Geben Sie anschließend einen Text in das Textfeld ein und klicken Sie auf das (**+**)-Symbol.

3. Wiederholen Sie den vorherigen Schritt mindestens drei Mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält.

4. Öffnen Sie die Datei QSTodoService.m, und suchen Sie die folgende Methode:

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

5. Erstellen und starten Sie die App.

    Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden.

7. Ändern Sie die **refreshDataOnSuccess**-Methode, indem Sie die folgende Codezeile suchen:

        query.fetchOffset = 0;

   	Setzen Sie den **query.fetchOffset**-Wert dieses Mal auf 3.

   	Diese Abfrage überspringt die ersten drei Ergebnisse und gibt die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

    > [AZURE.NOTE] Dieses Lernprogramm verwendet zur Vereinfachung fest codierte Werte für die **fetchOffset**- und **fetchLimit**-Eigenschaften. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren. Alternativ können Sie **query.includeTotalCount = YES** verwenden, um die Gesamtzahl aller Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.

## <a name="next-steps"> </a>Nächste Schritte

Dies bildet den Abschluss der Lernprogrammreihe über Grundlagen des Arbeitens mit Daten in Mobile Services. Weitere Informationen finden Sie im folgenden Mobile Services-Thema:

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie, wie Sie Benutzer der App mit einem Windows-Konto authentifizieren.

<!--
* [Erste Schritte mit Pushbenachrichtigungen]
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.
-->

<!-- Anchors. -->

[Nächste Schritte]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-ios
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-ios
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-ios
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-ios

[Verwaltungsportal]: https://manage.windowsazure.com/



<!--HONumber=42-->
