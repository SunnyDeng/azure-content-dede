<properties
	pageTitle="Erste Schritte mit der Offlinedatensynchronisierung in Mobile Services (iOS) | Microsoft Azure"
	description="Erfahren Sie, wie Sie Azure Mobile Services verwenden, um Offlinedaten in Ihrer iOS-Anwendung zwischenzuspeichern und zu synchronisieren."
	documentationCenter="ios"
	authors="krisragh"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-ios"
	ms.devlang="objective-c"
	ms.topic="article"
	ms.date="07/01/2015"
	ms.author="krisragh;donnam"/>

# Erste Schritte mit der Synchronisierung von Offlinedaten in Mobile Services

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

Die Offlinesynchronisierung ermöglicht Ihnen, Daten in einer mobilen Anwendung anzuzeigen und Daten hinzuzufügen oder zu ändern, auch wenn keine Netzwerkverbindung verfügbar ist. In diesem Lernprogramm erfahren Sie, wie Sie Ihre App so konfigurieren, dass Änderungen automatisch in einer lokalen Offlinedatenbank gespeichert werden und die Daten synchronisiert werden, sobald die App wieder online ist.

Die Offlinesynchronisierung bietet mehrere Vorteile:

* Verbessert die Reaktionsfähigkeit der App durch das lokale Caching von Serverdaten auf dem Gerät
* Macht Apps unempfindlich gegen Unterbrechungen der Netzwerkverbindung
* Ermöglicht das Erstellen und Ändern von Daten selbst bei geringer oder keiner Konnektivität
* Synchronisiert Daten auf mehreren Geräten
* Erkennt Konflikte, wenn derselbe Datensatz von zwei Geräten geändert wird

> [AZURE.NOTE]Um dieses Lernprogramm abzuschließen, benötigen Sie ein Azure-Konto. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie [kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen können](http://azure.microsoft.com/pricing/details/mobile-services/). Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28 target="\_blank").

Dieses Lernprogramm baut auf dem Schnellstartlernprogramm [Erste Schritte mit Mobile Services] auf, das Sie zuerst abschließen müssen. Sehen sich zunächst den Code im Zusammenhang mit der Offlinesynchronisierung aus dem Schnellstart an.

## <a name="review-sync"></a>Überprüfen von Mobile Services-Synchronisierungscode

Mit der Offlinesynchronisierung von Azure Mobile Services können Endbenutzer mit einer lokalen Datenbank interagieren, wenn das Netzwerk nicht verfügbar ist. Um diese Funktionen in Ihrer App zu verwenden, initialisieren Sie den Synchronisierungskontext von `MSClient`, und verweisen Sie auf einen lokalen Datenspeicher. Erstellen Sie dann über die `MSSyncTable`-Schnittstelle einen Verweis für die Tabelle.

* Beachten Sie in **QSTodoService.m**, dass der Typ des Mitglieds `syncTable` `MSSyncTable` ist. Die Offlinesynchronisierung verwendet diesen Typ anstelle von `MSTable`. Wenn eine Synchronisierungstabelle verwendet wird, werden alle Vorgänge im lokalen Speicher gespeichert und nur mit expliziten Push- und Pullvorgängen mit dem Remotedienst synchronisiert.

```
		@property (nonatomic, strong)   MSSyncTable *syncTable;
```

Um einen Verweis auf eine Synchronisierungstabelle zu erstellen, verwenden Sie die `syncTableWithName`-Methode. Zum Entfernen der Offlinesynchronisierung verwenden Sie stattdessen `tableWithName`.

* In **QSTodoService.m** wird vor der Durchführung von Tabellenvorgänge der lokale Speicher in `QSTodoService.init` initialisiert:

```
		MSCoreDataStore *store = [[MSCoreDataStore alloc] initWithManagedObjectContext:context];
		self.client.syncContext = [[MSSyncContext alloc] initWithDelegate:nil dataSource:store callback:nil];
```

Hierdurch wird ein lokaler Speicher mit der `MSCoreDataStore`-Schnittstelle erstellt. Sie können stattdessen auch einen anderen lokalen Speicher bereitstellen, indem Sie das `MSSyncContextDataSource`-Protokoll implementieren.

Der erste Parameter für `initWithDelegate` gibt einen Konflikthandler an, aber da wir `nil` weitergegeben haben, erhalten wir den Standardkonflikthandler, der bei jedem Konflikt einen Fehler ausgibt Details zur Implementierung eines benutzerdefinierten Konflikthandlers finden Sie unter [Behandeln von Konflikten mit der Offlineunterstützung für Mobile Services].

* In **QSTodoService.m** überträgt `syncData` zuerst neue Änderungen und ruft dann `pullData` zum Abrufen von Daten aus den Remotedienst auf. In `syncData` rufen wir zuerst `pushWithCompletion` im Synchronisierungskontext auf. Diese Methode ist ein Mitglied von `MSSyncContext` (statt der Synchronisierungstabelle selbst), da sie Änderungen auf alle Tabellen überträgt. Nur Datensätze, die lokal geändert wurden (mit creation-, update- oder delete-Vorgängen), werden an den Server gesendet. Am Ende von `syncData` rufen wir die Hilfsfunktion `pullData` auf.

In diesem Beispiel ist der Pushvorgang nicht unbedingt notwendig. Wenn im Synchronisierungskontext der Tabelle, die einen Pushvorgang durchführt, Änderungen ausstehen, wird durch den Pullvorgang immer zuerst ein Pushvorgang ausgeführt. Wenn Sie mehr als eine Synchronisierungstabelle haben, rufen Sie Push explizit auf, um Konsistenz zwischen den Tabellen zu wahren.

```
      -(void)syncData:(QSCompletionBlock)completion
      {
          // push all changes in the sync context, then pull new data
          [self.client.syncContext pushWithCompletion:^(NSError *error) {
              [self logErrorIfNotNil:error];
              [self pullData:completion];
          }];
      }

```

* Als nächstes werden in **QSTodoService.m** mit `pullData` neue Daten abgerufen, die einer Abfrage entsprechen. `pullData` ruft `MSSyncTable.pullWithQuery` auf, um Remotedaten abzurufen und lokal zu speichern. `pullWithQuery` ermöglicht Ihnen zudem, eine Abfrage zum Filtern der Datensätze anzugeben, die Sie abrufen möchten. In diesem Beispiel ruft die Abfrage einfach alle Datensätze in der Remotetabelle `TodoItem` ab.

Der zweite Parameter für `pullWithQuery` ist eine Abfrage-ID, für die _inkrementelle Synchronisierung_. Die inkrementelle Synchronisierung ruft nur die Datensätze ab, die seit der letzten Synchronisierung geändert wurden. Dafür wird der Zeitstempel `UpdatedAt` (im lokalen Speicher `ms_updatedAt`) des Datensatzes verwendet. Die Abfrage-ID ist eine beschreibende Zeichenfolge sein, die für jede logische Abfrage in Ihrer App eindeutig ist. Um die inkrementelle Synchronisierung zu deaktivieren, übergeben Sie als Abfrage-ID `nil`. Dies ist ineffizient, da hierdurch alle Datensätze bei jedem Pullvorgang abgerufen werden.

```
      -(void)pullData:(QSCompletionBlock)completion
      {
          MSQuery *query = [self.syncTable query];

          // Pulls data from the remote server into the local table.
          // We're pulling all items and filtering in the view
          // query ID is used for incremental sync
          [self.syncTable pullWithQuery:query queryId:@"allTodoItems" completion:^(NSError *error) {
              [self logErrorIfNotNil:error];

              // Let the caller know that we have finished
              if (completion != nil) {
                  dispatch_async(dispatch_get_main_queue(), completion);
              }
          }];
      }
```


>[AZURE.NOTE]Um Datensätze aus dem lokalen Speicher des Geräts zu entfernen, wenn sie in der Datenbank des mobilen Diensts gelöscht wurden, aktivieren Sie [Vorläufiges Löschen]. Andernfalls sollte Ihre App in regelmäßigen Abständen `MSSyncTable.purgeWithQuery` aufrufen, um den lokalen Speicher zu löschen.


* In **QSTodoService.m** rufen die Methoden `addItem` und `completeItem` nach dem Ändern von Daten `syncData` auf. In **QSTodoListViewController.m** ruft die Methode `refresh` auch `syncData` auf, damit in der GUI bei jeder Aktualisierung und jedem Start die neuesten Daten angezeigt werden (`init` ruft `refresh` auf).

Da die App jedes Mal `syncData` aufruft, wenn Sie Daten ändern, setzt die App voraus, dass Sie online sind, wenn Sie Daten in der App bearbeiten.

## <a name="review-core-data"></a>Überprüfen des Core-Datenmodells

Bei Verwendung des Core-Offlinedatenspeichers müssen Sie bestimmte Tabellen und Felder in Ihrem Datenmodell definieren. Die Beispiel-App enthält bereits ein Datenmodell mit dem richtigen Format. In diesem Abschnitt werden die Tabellen und ihre Verwendung erläutert.

- Öffnen Sie **QSDataModel.xcdatamodeld**. Es sind vier Tabellen definiert – drei, die durch das SDK verwendet werden, und eine Tabelle für die Todo-Elemente selbst:

      * MS\_TableOperations: Zum Nachverfolgen von Elementen, die mit dem Server synchronisiert werden müssen.
      * MS\_TableOperationErrors: Zum Nachverfolgen von Fehlern, die während der Offlinesynchronisierung auftreten.
      * MS\_TableConfig: Zum Nachverfolgen der letzten aktualisierten Zeit für die letzte Synchronisierung für alle Pullvorgänge.
      * TodoItem: Zum Speichern von TODO-Elementen. Die Systemspalten **ms\_createdAt**, **ms\_updatedAt** und **ms\_version** sind optionale Systemeigenschaften.

>[AZURE.NOTE]Das Mobile Services-SDK reserviert Spaltennamen, die mit "**`ms_`**" beginnen. Verwenden Sie dieses Präfix ausschließlich für Systemspalten. Andernfalls ändern sich die Spaltennamen bei Verwendung des Remotedienstes.

- Zur Verwendung der Funktion zur Offlinesynchronisierung müssen Sie die Systemtabellen definieren, wie unten dargestellt.

    ### Systemtabellen

    #### MS\_TableOperations

    | Attribut | Typ |
    |-------------- |   ------    |
    | ID (erforderlich) | Integer 64 |
    | itemId | String |
    | Eigenschaften | Binärdaten |
    | Tabelle | String |
    | tableKind | Integer 16 |

    #### MS\_TableOperationErrors

    | Attribut | Typ |
    |-------------- | ----------  |
    | ID (erforderlich) | String |
    | operationId | Integer 64 |
    | Eigenschaften | Binärdaten |
    | tableKind | Integer 16 |

    #### MS\_TableConfig


    | Attribut | Typ |
    |-------------- | ----------  |
    | ID (erforderlich) | String |
    | key | String |
    | keyType | Integer 64 |
    | Tabelle | String |
    | value | String |

    ### Datentabelle

    #### TodoItem

    | Attribut | Typ | Hinweis | 
    |-------------- |  ------ | -------------------------------------------------------|
    | ID (erforderlich) | String | Primärschlüssel im Remotespeicher (erforderlich) |
    | complete | Boolean | Todo-Elementfeld |
    | Text | String | Todo-Elementfeld |
    | ms\_createdAt | Date | (optional) Zuordnung zur \_\_createdAt-Systemeigenschaft | | ms\_updatedAt | Datum | (optional) Zuordnung zur \_\_updatedAt-Systemeigenschaft | | ms\_version | String | (optional) Zum Erkennen von Konflikten, Zuordnung zu \_\_version |



## <a name="setup-sync"></a>Ändern des Synchronisierungsverhaltens der App

In diesem Abschnitt ändern Sie die Anwendung, sodass beim Starten der App oder beim Einfügen und Aktualisieren von Elementen keine Synchronisierung erfolgt, sondern nur, wenn die Aktualisierungsbewegung durchgeführt wird.

* Ändern Sie in **QSTodoListViewController.m**, die `viewDidLoad`-Methode, um den Aufruf von `[self refresh]` am Ende der Methode zu entfernen. Die Daten werden jetzt beim Starten der App nicht mit dem Server synchronisiert, sondern stattdessen nur lokal gespeichert.

* Ändern Sie in **QSTodoService.m** die Definition von `addItem`, sodass nach dem Einfügen des Elements keine Synchronisierung erfolgt. Entfernen Sie den `self syncData`-Block, und ersetzen Sie ihn durch Folgendes:

```
        if (completion != nil) {
            dispatch_async(dispatch_get_main_queue(), completion);
        }
```

* Entfernen Sie ebenfalls in **QSTodoService.m** die Definition von `completeItem`, entfernen Sie den Block für `self syncData`, und ersetzen Sie ihn durch Folgendes:

```
        if (completion != nil) {
            dispatch_async(dispatch_get_main_queue(), completion);
        }
```

## <a name="test-app"></a>Test-App

In diesem Abschnitt werden Sie im Simulator Wi-Fi deaktivieren, um ein Offlineszenario zu erzeugen. Wenn Sie Datenelemente hinzufügen, werden diese im lokalen Core-Datenspeicher vorgehalten, jedoch nicht mit dem mobilen Dienst synchronisiert.

1. Deaktivieren Sie die Internetverbindung auf Ihrem Macintosh. Das alleinige Deaktivieren des WLAN im iOS-Simulator hat unter Umständen keinerlei Auswirkung, da der Simulator die Internetverbindung des Macintosh-Hostcomputers nutzt. Sie sollten daher die Internetverbindung des Computers trennen. So wird ein Offlineszenario simuliert.

2. Fügen Sie einige TODO-Elemente hinzu, oder schließen Sie einige Elemente ab. Beenden Sie den Simulator (oder Erzwingen Sie das Schließen der App), und starten Sie ihn neu. Stellen Sie sicher, dass die Änderungen erhalten geblieben sind. Beachten Sie, dass die Datenelemente weiterhin angezeigt werden, da sie im lokalen Core-Datenspeicher gespeichert sind.

3. Zeigen Sie den Inhalt der TodoItem-Remotetabelle an. Stellen Sie sicher, dass die neuen Elemente _nicht_ auf dem Server synchronisiert wurden.

   - Klicken Sie für das JavaScript-Back-End auf dem Verwaltungsportal auf die Registerkarte "Daten", um den Inhalt der `TodoItem`-Tabelle anzuzeigen.
   - Zeigen Sie für das .NET-Back-End den Inhalt der Tabelle entweder mit einem SQL-Tool wie SQL Server Management Studio oder einen REST-Client wie Fiddler oder Postman an.

4. Aktivieren Sie Wi-Fi im iOS-Simulator. Führen Sie als Nächstes die Aktualisierungsbewegung aus, indem Sie die Elementliste nach unten ziehen. Eine Synchronisierungsanzeige und der Hinweis „Syncing...“ werden angezeigt.

5. Zeigen Sie die TodoItem-Daten erneut an. Es sollten jetzt die neuen und geänderten TodoItems angezeigt werden.

## Zusammenfassung

Um die Offlinefunktionen mobiler Dienste zu unterstützen, wurde die `MSSyncTable`-Schnittstelle verwendet und `MSClient.syncContext` mit einem lokalen Speicher initialisiert. In diesem Fall war der lokale Speicher eine auf Core-Daten basierende Datenbank.

Wenn Sie einen lokalen Core-Datenspeicher verwenden, definieren Sie mehrere Tabellen mit [korrekten Systemeigenschaften][Review the Core Data model]. Die normalen Operationen für mobile Dienste funktionieren so, als ob die App immer noch verbunden wäre. Alle Operationen erfolgen jedoch nur im lokalen Speicher.

Um den lokalen Speicher mit dem Server zu synchronisieren, wurden `MSSyncTable.pullWithQuery` und `MSClient.syncContext.pushWithCompletion` verwendet:

		* To push changes to the server, you called `pushWithCompletion`. This method is in `MSSyncContext` instead of the sync table because it will push changes across all tables. Only records that are modified in some way locally (through CUD operations) are be sent to the server.

		* To pull data from a table on the server to the app, you called `MSSyncTable.pullWithQuery`. A pull always issues a push first. This is to ensure all tables in the local store along with relationships remain consistent. `pullWithQuery` can also be used to filter the data that is stored on the client, by customizing the `query` parameter.

## Nächste Schritte

* [Behandeln von Konflikten mit der Offlineunterstützung für Mobile Services]

* [Vorläufiges Löschen in Mobile Services][Soft Delete]

## Zusätzliche Ressourcen

* [Cloud Cover: Offlinesynchronisierung in Azure Mobile Services]

* [Azure Friday: Offlinefähige Apps in Azure Mobile Services] (Hinweis: Demos sind für Windows, Funktionserläuterungen gelten jedoch für alle Plattformen)

<!-- URLs. -->

[Get the sample app]: #get-app
[Review the Core Data model]: #review-core-data
[Review the Mobile Services sync code]: #review-sync
[Change the sync behavior of the app]: #setup-sync
[Test the app]: #test-app

[core-data-1]: ./media/mobile-services-ios-get-started-offline-data/core-data-1.png
[core-data-2]: ./media/mobile-services-ios-get-started-offline-data/core-data-2.png
[core-data-3]: ./media/mobile-services-ios-get-started-offline-data/core-data-3.png
[defining-core-data-main-screen]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-main-screen.png
[defining-core-data-model-editor]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-model-editor.png
[defining-core-data-tableoperationerrors-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperationerrors-entity.png
[defining-core-data-tableoperations-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableoperations-entity.png
[defining-core-data-tableconfig-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-tableconfig-entity.png
[defining-core-data-todoitem-entity]: ./media/mobile-services-ios-get-started-offline-data/defining-core-data-todoitem-entity.png
[update-framework-1]: ./media/mobile-services-ios-get-started-offline-data/update-framework-1.png
[update-framework-2]: ./media/mobile-services-ios-get-started-offline-data/update-framework-2.png

[Core Data Model Editor Help]: https://developer.apple.com/library/mac/recipes/xcode_help-core_data_modeling_tool/Articles/about_cd_modeling_tool.html
[Creating an Outlet Connection]: https://developer.apple.com/library/mac/recipes/xcode_help-interface_builder/articles-connections_bindings/CreatingOutlet.html
[Build a User Interface]: https://developer.apple.com/library/mac/documentation/ToolsLanguages/Conceptual/Xcode_Overview/Edit_User_Interfaces/edit_user_interface.html
[Adding a Segue Between Scenes in a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardSegue.html#//apple_ref/doc/uid/TP40014225-CH25-SW1
[Adding a Scene to a Storyboard]: https://developer.apple.com/library/ios/recipes/xcode_help-IB_storyboard/chapters/StoryboardScene.html

[Core Data]: https://developer.apple.com/library/ios/documentation/Cocoa/Conceptual/CoreData/cdProgrammingGuide.html
[Download the preview SDK here]: http://aka.ms/Gc6fex
[How to use the Mobile Services client library for iOS]: mobile-services-ios-how-to-use-client-library.md
[Offline iOS Sample]: https://github.com/Azure/mobile-services-samples/tree/master/TodoOffline/iOS/blog20140611
[Mobile Services sample repository on GitHub]: https://github.com/Azure/mobile-services-samples


[Get started with Mobile Services]: mobile-services-ios-get-started.md
[Get started with data]: mobile-services-ios-get-started-data.md
[Behandeln von Konflikten mit der Offlineunterstützung für Mobile Services]: mobile-services-ios-handling-conflicts-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md
[Vorläufiges Löschen]: mobile-services-using-soft-delete.md

[Cloud Cover: Offlinesynchronisierung in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offlinefähige Apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

[Erste Schritte mit Mobile Services]: mobile-services-ios-get-started.md

<!---HONumber=August15_HO7-->