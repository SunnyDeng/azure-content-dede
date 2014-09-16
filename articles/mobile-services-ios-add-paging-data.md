<properties linkid="develop-mobile-tutorials-add-paging-to-data-ios" urlDisplayName="Add paging to data" pageTitle="Add paging to data (iOS) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your iOS app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="" solutions="" manager="" editor="" />

Verfeinern von Mobile Services-Abfragen mit Paging
==================================================

> [AZURE.SELECTOR-LIST (Platform | Backend )] 
> - [(Windows Store C\# | .NET)](mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data.md) 
> - [(Windows Store C\# | JavaScript)](mobile-services-windows-store-dotnet-add-paging-data.md) 
> - [(Windows Store JavaScript | .NET)](mobile-services-dotnet-backend-windows-store-javascript-add-paging-data.md) 
> - [(Windows Store JavaScript | JavaScript)](mobile-services-windows-store-javascript-add-paging-data.md) 
> - [(Windows Phone | .NET)](mobile-services-dotnet-backend-windows-phone-add-paging-data) 
> - [(Windows Phone | JavaScript)](mobile-services-windows-phone-add-paging-data) 
> - [(iOS | JavaScript)](mobile-services-ios-add-paging-data) 
> - [(Android | JavaScript)](mobile-services-android-add-paging-data) 
> - [(HTML | .NET)](mobile-services-html-add-paging-data) 
> - [(Xamarin iOS | .NET)](partner-xamarin-mobile-services-ios-add-paging-data) 
> - [(Xamarin Android | .NET)](partner-xamarin-mobile-services-android-add-paging-data)

In diesem Thema erfahren Sie, wie Sie die aus Azure Mobile Services an Ihre iOS-App zurückgegebene Datenmenge mithilfe von Paging steuern können. Dieses Lernprogramm verwendet die Abfrageeigenschaften **fetchLimit** und **fetchOffset** im Client, um bestimmte "Seiten" von Daten abzufragen.

<div class="dev-callout"><b>Hinweis</b>
<p>
Mobile Services verwendet standardmäßig eine Seitengröße von 50 Elementen pro Antwort, um mobile Clients nicht mit Daten zu überfluten. Durch Angabe der Seitengröße können Sie bis zu 1.000 Elemente in einer Antwort abfragen.</p></div>

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-ios). Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zumindest das erste Lernprogramm aus der Daten-Reihe abschließen, [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-ios).

1.  Öffnen Sie in Xcode das Projekt, das Sie im Lernprogramm [Erste Schritte mit Daten](/en-us/develop/mobile/tutorials/get-started-with-data-ios) verändert haben.

2.  Drücken Sie die **Run** Taste (Command + R), um das Projekt zu erstellen und die App zu starten. Geben Sie anschließend einen Text in das Textfeld ein und klicken Sie auf das (**+**) Symbol.

3.  Wiederholen Sie den vorherigen Schritt mindestens drei mal, sodass Ihre TodoItem-Tabelle mehr als drei Elemente enthält.

4.  Öffnen Sie die Datei QSTodoService.m und suchen Sie die folgende Methode:

         - (void)refreshDataOnSuccess:(QSCompletionBlock)completion

      Ersetzen Sie den Text der Methode durch den folgenden Code. 

         // Prädikat erstellen, das Einträge mit complete=false findet
         NSPredicate * predicate = [NSPredicate predicateWithFormat:@"complete == NO"];

         // Abrufen der MSQuery-Instanz von MSTable's mit dem soeben erstellten Prädikat.
         MSQuery * query = [self.table queryWithPredicate:predicate];
            
         query.includeTotalCount = YES; // Gesamtzahl der Elemente abfragen

         // Mit dem ersten Element beginnen und nur drei Element abrufen
         query.fetchOffset = 0;
         query.fetchLimit = 3;

         // MSQuery-Instanz direkt aufrufen, anstelle der MSTable-Hilfsmethoden.
         [query readWithCompletion:^(NSArray *results, NSInteger totalCount, NSError *error) {
                
             [self logErrorIfNotNil:error];
             if (!error)
             {
                 // Gesamtzahl protokollieren.
                 NSLog(@"Total item count: %@",[NSString stringWithFormat:@"%zd", (ssize_t) totalCount]);            
             }
            
             items = [results mutableCopy];
            
             // Aufrufer benachrichtigen, dass die Verarbeitung abgeschlossen ist
             completion();
         }];

      Diese Abfrage gibt die ersten drei Elemente zurück, die nicht als abgeschlossen markiert sind. 

5.  Erstellen und starten Sie die App.

    Beachten Sie, dass nur die ersten drei Ergebnisse aus der TodoItem-Tabelle angezeigt werden.

6.  Ändern Sie die **refreshDataOnSuccess** Methode, indem Sie die folgende Codezeile suchen:

         query.fetchOffset = 0;

      Setzen Sie den Wert von **query.fetchOffset** nun auf 3. 

      Diese Abfrage überspringt die ersten drei Ergebnisse und liefert die folgenden drei zurück. Dies ist die zweite "Seite" der Daten für eine Seitengröße von drei Elementen.

    <div class="dev-callout"><b>Hinweis</b>
	<p>
    Dieses Lernprogramm verwendet zur Vereinfachung fest codierte Werte für die **fetchOffset** und **fetchLimit** Eigenschaften. Tatsächliche Anwendungen können ähnliche Abfragen mit einem Pagersteuerelement oder einer ähnlichen Benutzersteuerung ausführen, um zur vorherigen bzw. nächsten Seite zu navigieren. Alternativ können Sie **query.includeTotalCount = YES** verwenden, um die Gesamtzahl aller Elemente auf dem Server zusammen mit den Paging-Daten abzurufen.</p></div>

Nächste Schritte
----------------

Dies bildet den Abschluss der Lernprogramm-Reihe über Grundlagen mit Daten in Mobile Services. Weitere Informationen finden Sie im folgenden Mobile Services-Thema:

-   [Erste Schritte mit der Authentifizierung](/en-us/develop/mobile/tutorials/get-started-with-users-ios)
    <br/>Lernen Sie, wie Benutzer Ihrer App mit einem Windows-Konto authentifiziert werden.

