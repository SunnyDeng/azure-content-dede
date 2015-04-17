<properties
    pageTitle="Aktivieren der Offlinesynchronisierung für Ihre mobile App (Xamarin iOS)"
    description="Erfahren Sie, wie Sie mobile App Service-Apps verwenden, um Offlinedaten in Ihrer Xamarin iOS-Anwendung zwischenzuspeichern und zu synchronisieren."
    documentationCenter="xamarin"
    authors="lindydonna"
    manager="dwrede"
    editor=""
    services="app-service\mobile"/>

<tags
    ms.service="app-service-mobile"
    ms.workload="mobile"
    ms.tgt_pltfrm="xamarin-ios"
    ms.devlang="dotnet"
    ms.topic="article"
    ms.date="03/26/2015"
    ms.author="donnam"/>

# Aktivieren der Offlinesynchronisierung für Ihre mobile Xamarin iOS-App

<!-- [AZURE.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]
 -->

In diesem Lernprogramm wird die Offlinesynchronisierungsfunktion von Mobile Apps für iOS behandelt. Offlinesynchronisierung ermöglicht Endbenutzern die Interaktion mit einer mobilen App - Anzeigen, Hinzufügen und Ändern von Daten -, auch wenn keine Netzwerkverbindung vorhanden ist. Änderungen werden in einer lokalen Datenbank gespeichert. Sobald das Gerät wieder online ist, werden diese Änderungen mit dem Remotedienst synchronisiert.

Die Offlinesynchronisierung hat eine Reihe potenzieller Nutzen:

* Verbesserte Reaktionsfähigkeit der App durch das lokale Caching von Serverdaten auf dem Gerät
* Macht Apps unempfindlich gegen Unterbrechungen der Netzwerkverbindung
* Endanwender können Daten selbst dann erstellen und ändern, wenn sie keinen Netzwerkzugriff haben, also in Szenarien mit wenig oder keiner Konnektivität
* Daten auf mehreren Geräten synchronisieren und Konflikte erkennen, wenn derselbe Datensatz von zwei Geräte aus geändert wird

Falls Sie noch keine Erfahrung mit Mobile Apps haben, sollten Sie zunächst das Lernprogramm [Erstellen einer Xamarin iOS-App] abschließen.

Für dieses Lernprogramm ist Folgendes erforderlich:

* Visual Studio 2013
* Visual Studio mit [Xamarin-Erweiterung] **oder** [Xamarin Studio] unter OS X

## Überprüfen des Mobile App-Codes für die Synchronisierung

Mit der Mobile App-Offlinesynchronisierung können Endbenutzer mit einer lokalen Datenbank interagieren, wenn das Netzwerk nicht verfügbar ist. Um diese Funktionen in Ihrer App verwenden zu können, initialisieren Sie `MobileServiceClient.SyncContext` in einem lokalen Speicher. Erstellen Sie dann für die Tabelle einen Verweis über die `IMobileServiceSyncTable`-Schnittstelle.
In diesem Abschnitt wird der in `QSTodoService.cs` enthaltene Code für die Offlinesynchronisierung Schritt für Schritt besprochen.

1. Öffnen Sie in Visual Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit mobilen Apps] abgeschlossen haben. Öffnen Sie die Datei `QSTodoService.cs`.

2. Beachten Sie, dass das Elements `todoTable`-Element den Typ `IMobileServiceSyncTable` aufweist. Die Offlinesynchronisierung verwendet anstelle von `IMobileServiceTable` diese Schnittstelle für die Synchronisierungstabelle. Wenn eine Synchronisierungstabelle verwendet wird, werden alle Vorgänge im lokalen Speicher gespeichert und nur mit expliziten Push- und Pullvorgängen mit dem Remotedienst synchronisiert.

    Um einen Verweis auf eine Synchronisierungstabelle abzurufen, verwenden Sie die Methode `GetSyncTable()`. Um die Funktionalität zur Offlinesynchronisierung zu entfernen, verwenden Sie stattdessen `GetTable()`.

3. Bevor Tabellenvorgänge durchgeführt werden können, muss der lokale Speicher initialisiert werden. Dies geschieht in der `InitializeStoreAsync`-Methode:

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }

    Dadurch wird ein lokaler Speicher mithilfe der `MobileServiceSQLiteStore`-Klasse erstellt, die in der Mobile App-SDK bereitgestellt wird. Sie können stattdessen auch eine andere lokale Speicherimplementierung bereitstellen, indem Sie `IMobileServiceLocalStore` implementieren.

    Die `DefineTable`-Methode erstellt eine Tabelle im lokalen Speicher, die mit den Feldern im bereitgestellten Typ übereinstimmt (in diesem Fall `ToDoItem`). Der Typ muss nicht alle Spalten der Remotedatenbank enthalten; es ist möglich, nur eine Teilmenge der Spalten zu speichern.

<!--     Diese Überladung von `InitializeAsync` verwendet den standardmäßigen Konflikthandler, der bei jedem Konflikt einen Fehler ausgibt. Informationen zum Bereitstellen eines benutzerdefinierten Handlers finden Sie im Lernprogramm [Behandeln von Konflikten mit der Offlineunterstützung für Mobile Services].
 -->
4. Die Methode `SyncAsync` löst die eigentliche Synchronisierung aus:

        public async Task SyncAsync()
        {
            try
            {
                await client.SyncContext.PushAsync();
                await todoTable.PullAsync("allTodoItems", todoTable.CreateQuery()); // query ID is used for incremental sync
            }

            catch (MobileServiceInvalidOperationException e)
            {
                Console.Error.WriteLine(@"Sync Failed: {0}", e.Message);
            }
        }

    Zuerst wird `IMobileServiceSyncContext.PushAsync()` aufgerufen. Diese Methode ist kein Element der Synchronisierungstabelle, sondern ein Element von `IMobileServicesSyncContext`, da sie Änderungen auf alle Tabellen überträgt. Nur Datensätze, die lokal geändert wurden (mit CRUD-Operationen), werden an den Server gesendet.

    Als Nächstes ruft die Methode `IMobileServiceSyncTable.PullAsync()` auf, um Daten per Pullvorgang von einer Tabelle auf dem Server an die App zu übertragen. Beachten Sie, dass ein Pullvorgang immer zuerst einen Pushvorgang ausführt, wenn der Synchronisierungskontext noch ausstehende Änderungen enthält. Dadurch wird sichergestellt, dass alle Tabellen im lokalen Speicher und die Beziehungen konsistent sind. In diesem Fall wurde Push explizit aufgerufen.

    In diesem Beispiel werden alle Datensätze in der `TodoItem`-Remotetabelle abgerufen, Sie können die Datensätze aber auch durch Übergeben einer Abfrage filtern. Der erste Parameter für `PullAsync()` ist eine Abfrage-ID für eine inkrementelle Synchronisierung. Diese verwendet den `UpdatedAt`-Zeitstempel, um nur die Datensätze abzurufen, die seit der letzten Synchronisierung geändert wurden. Die Abfrage-ID muss eine beschreibende Zeichenfolge sein, die für jede logische Abfrage in Ihrer App eindeutig ist. Um die inkrementelle Synchronisierung zu deaktivieren, übergeben Sie `null` als Abfrage-ID. Dadurch werden in jedem Pullvorgang alle Datensätze abgerufen, was potenziell ineffizient ist.

<!--     >[AZURE.NOTE] Um Datensätze aus dem lokalen Speicher des Geräts zu entfernen, wenn sie in der Datenbank des mobilen Diensts gelöscht wurden, aktivieren Sie [Vorläufiges Löschen]. Andernfalls muss die App in regelmäßigen Abständen `IMobileServiceSyncTable.PurgeAsync()` aufrufen, um den lokalen Speicher zu löschen.

    Beachten Sie, dass  `MobileServicePushFailedException` sowohl für einen Push- als auch für einen Pullvorgang verwendet werden kann. Im nächsten Lernprogramm, [Behandeln von Konflikten mit der Offlineunterstützung für Mobile Services], wird gezeigt, wie Sie solche synchronisierungsbezogenen Ausnahmen behandeln.
-->

5. In der Klasse `QSTodoService` wird die Methode `SyncAsync()` nach den Vorgängen zum Ändern von Daten, `InsertTodoItemAsync()` und `CompleteItemAsync`, aufgerufen. Der Aufruf erfolgt auch über `RefreshDataAsync()` aufgerufen, sodass dem Benutzer nach Ausführen der Aktualisierungsbewegung die neuesten Daten angezeigt werden. Die App führt außerdem beim Start eine Synchronisierung durch, da `QSTodoListViewController.ViewDidLoad()` `RefreshDataAsync()` aufruft.

    Da `SyncAsync()` bei jeder Änderung von Daten aufgerufen wird, setzt diese App voraus, dass der Benutzer beim Bearbeiten von Daten stets online ist. Im nächsten Abschnitt wird die App so aktualisiert, dass Benutzer Daten auch dann bearbeiten können, wenn sie offline sind.

## Aktualisieren des Synchronisierungsverhaltens der App

In diesem Abschnitt ändern Sie die App so, dass sie beim App-Start oder bei Einfüge- und Updatevorgängen keine Synchronisierung durchführt, sondern nur beim Durchführen der Aktualisierungsbewegung. Anschließend trennen Sie die Verbindung zwischen App und mobilem Back-End, um ein Offlineszenario zu simulieren. Wenn Sie Datenelemente hinzufügen, werden diese im lokalen Speicher gespeichert, aber nicht sofort mit dem mobilen Back-End synchronisiert.

1. Öffnen Sie `QSTodoService.cs`. Kommentieren Sie die Aufrufe von `SyncAsync()` in den folgenden Methoden aus:

    - `InsertTodoItemAsync`
    - `CompleteItemAsync`
    - `RefreshAsync`

    Jetzt lädt `RefreshAsync()` nur Daten aus dem lokalen Speicher, stellt aber keine Verbindung mit dem App-Back-End her.

2. Ändern Sie in `QSTodoService.cs` die Definition von `applicationURL`, um auf einen ungültigen URI für die mobile App zu verweisen:

        const string applicationURL = @"https://your-service.azurewebsites.xxx/"; // invalid URI

3. Um sicherzustellen, dass die Daten bei Ausführung der Aktualisierungsbewegung synchronisiert werden, bearbeiten Sie die Methode `QSTodoListViewController.RefreshAsync()`. Fügen Sie einen Aufruf von `SyncAsync()` hinzu, bevor `RefreshDataAsync()` aufgerufen wird:

        private async Task RefreshAsync ()
        {
            RefreshControl.BeginRefreshing ();

            await todoService.SyncAsync();
            await todoService.RefreshDataAsync (); // add this line

            RefreshControl.EndRefreshing ();

            TableView.ReloadData ();
        }

4. Erstellen Sie die App, und führen Sie sie aus. Fügen Sie einige neue Todo-Elemente hinzu. Diese neuen Elemente sind nur im lokalen Speicher vorhanden, bis sie per Push an das mobile Back-End übertragen werden können. Die Client-App verhält sich so, als ob eine Verbindung mit dem mobilen Back-End vorhanden wäre, und unterstützt alle Operationen zum Erstellen, Lesen, Aktualisieren und Löschen (CRUD-Vorgänge).

5. Schließen Sie die App und starten Sie sie erneut, um zu überprüfen, ob die neuen Elemente dauerhaft im lokalen Speicher gespeichert wurden.

## Aktualisieren der App, um erneut eine Verbindung mit dem mobilen Back-End herzustellen

In diesem Abschnitt stellen Sie erneut eine Verbindung zwischen der App und dem mobilen Back-End her, um zu simulieren, dass die App wieder im Onlinezustand ist. Wenn Sie die Aktualisierungsbewegung durchführen, werden Daten mit Ihrem mobilen Back-End synchronisiert.

1. Öffnen Sie `QSTodoService.cs`. Entfernen Sie die ungültige URL für die mobile App, und fügen Sie die richtige URL und den App-Schlüssel ein.

2. Erstellen Sie die App erneut, und führen Sie sie aus. Beachten Sie, dass die Daten nicht geändert wurden, obwohl die App jetzt mit dem mobilen Back-End verbunden ist. Dies liegt daran, dass die App `IMobileServiceSyncTable` verwendet, die auf den lokalen Speicher verweist.

3. Stellen Sie eine Verbindung mit der SQL-Datenbank für Ihr Back-End her, um die Daten anzuzeigen, die gespeichert wurden. Wechseln Sie in Visual Studio zu **Server-Explorer** -> **Azure** -> **SQL-Datenbanken**. Klicken Sie mit der rechten Maustaste auf Ihre Datenbank, und wählen Sie **In SQL Server-Objekt-Explorer öffnen** aus.

    Beachten Sie, dass die Daten zwischen Datenbank und lokalem Speicher *not*synchronisiert wurden.

4. Führen Sie in der App die Aktualisierungsbewegung aus, indem Sie die Elementliste nach unten ziehen. Dies bewirkt, dass die App `RefreshDataAsync()` aufruft, wodurch wiederum `SyncAsync()` aufgerufen wird. Hierdurch werden die Push- und Pullvorgänge ausgeführt, wobei zunächst die Elemente im lokalen Speicher an das mobile Back-End gesendet und dann die neuen Daten aus dem Back-End abgerufen werden.

5. Aktualisieren Sie die Datenbankansicht, und vergewissern Sie sich, dass die Änderungen synchronisiert wurden.

<!-- ##Zusammenfassung

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../includes/mobile-services-offline-summary-csharp.md)]

## Nächste Schritte

* [Behandeln von Konflikten mit der Offlineunterstützung für Mobile Services]

* [Verwenden des Clients der Xamarin-Komponente für Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[Erstellen einer Xamarin iOS-App]: app-service-mobile-dotnet-backend-xamarin-ios-get-started.md

[Verwenden des Clients der Xamarin-Komponente für Azure Mobile Services]: partner-xamarin-mobile-services-how-to-use-client-library.md

[Xamarin Studio]: http://xamarin.com/download
[Xamarin-Erweiterung]: http://xamarin.com/visual-studio

<!--HONumber=49-->