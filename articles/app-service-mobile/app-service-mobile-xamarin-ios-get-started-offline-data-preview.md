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
	ms.date="07/01/2015"
    ms.author="donnam"/>

# Aktivieren der Offlinesynchronisierung für Ihre mobile Xamarin iOS-App

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]

In diesem Lernprogramm wird die Offlinesynchronisierungsfunktion von Mobile Apps für iOS behandelt. Offlinesynchronisierung ermöglicht Endbenutzern die Interaktion mit einer mobilen App – Anzeigen, Hinzufügen und Ändern von Daten –, auch wenn keine Netzwerkverbindung vorhanden ist. Änderungen werden in einer lokalen Datenbank gespeichert. Sobald das Gerät wieder online ist, werden diese Änderungen mit dem Remotedienst synchronisiert.

Die Offlinesynchronisierung hat eine Reihe potenzieller Nutzen:

* Verbesserte Reaktionsfähigkeit der App durch das lokale Caching von Serverdaten auf dem Gerät
* Macht Apps unempfindlich gegen Unterbrechungen der Netzwerkverbindung
* Endanwender können Daten selbst dann erstellen und ändern, wenn sie keinen Netzwerkzugriff haben, also in Szenarien mit wenig oder keiner Konnektivität
* Daten auf mehreren Geräten synchronisieren und Konflikte erkennen, wenn derselbe Datensatz von zwei Geräte aus geändert wird

Falls Sie noch keine Erfahrung mit Mobile Apps haben, sollten Sie zunächst das Lernprogramm [Erstellen einer Xamarin iOS-App] abschließen.

Für dieses Lernprogramm ist Folgendes erforderlich:

* Visual Studio 2013
* Visual Studio mit [Xamarin-Erweiterung] **oder** [Xamarin Studio] unter OS X

##<a name="review"></a>Überprüfen der Konfiguration der Serverprojekts (optional)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-offline-preview](../../includes/app-service-mobile-dotnet-backend-enable-offline-preview.md)]

## Überprüfen des Mobile App-Codes für die Synchronisierung

Mit der Mobile App-Offlinesynchronisierung können Endbenutzer mit einer lokalen Datenbank interagieren, wenn das Netzwerk nicht verfügbar ist. Initialisieren Sie `MobileServiceClient.SyncContext` in einem lokalen Speicher, um diese Funktionen in der App zu verwenden. Erstellen Sie dann über die `IMobileServiceSyncTable`-Schnittstelle einen Verweis für die Tabelle. In diesem Abschnitt wird der in `QSTodoService.cs` enthaltene Code für die Offlinesynchronisierung Schritt für Schritt besprochen.

1. Öffnen Sie in Visual Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit Mobile Apps] abgeschlossen haben. Öffnen Sie die Datei `QSTodoService.cs`.

2. Beachten Sie, dass der `todoTable`-Member den Typ `IMobileServiceSyncTable` hat. Bei der Offlinesynchronisierung wird diese Synchronisierungstabellen-Schnittstelle anstelle von `IMobileServiceTable` verwendet. Wenn eine Synchronisierungstabelle verwendet wird, werden alle Vorgänge im lokalen Speicher gespeichert und nur mit expliziten Push- und Pullvorgängen mit dem Remotedienst synchronisiert.

    Zum Abrufen eines Verweises auf eine Synchronisierungstabelle wird die `GetSyncTable()`-Methode verwendet. Um die Funktionalität zur Offlinesynchronisierung zu entfernen, verwenden Sie stattdessen `GetTable()`.

3. Bevor Tabellenvorgänge durchgeführt werden können, muss der lokale Speicher initialisiert werden. Dies erfolgt in der `InitializeStoreAsync`-Methode:

        public async Task InitializeStoreAsync()
        {
            var store = new MobileServiceSQLiteStore(localDbPath);
            store.DefineTable<ToDoItem>();

            // Uses the default conflict handler, which fails on conflict
            await client.SyncContext.InitializeAsync(store);
        }

    Dadurch wird ein lokaler Speicher mithilfe der `MobileServiceSQLiteStore`-Klasse erstellt, die in der Mobile App-SDK bereitgestellt wird. Sie können auch eine andere lokale Speicherimplementierung angeben, indem Sie `IMobileServiceLocalStore` implementieren.

    Die `DefineTable`-Methode erstellt eine Tabelle im lokalen Speicher, die mit den Feldern im bereitgestellten Typ übereinstimmt (in diesem Fall `ToDoItem`). Der Typ muss nicht alle Spalten der Remotedatenbank enthalten; es ist möglich, nur eine Teilmenge der Spalten zu speichern.

<!--     This overload of `InitializeAsync` uses the default conflict handler, which fails whenever there is a conflict. To provide a custom conflict handler, see the tutorial [Handling conflicts with offline support for Mobile Services].
 -->
4. Die `SyncAsync`-Methode löst die eigentliche Synchronisierung aus:

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

    Zunächst wird `IMobileServiceSyncContext.PushAsync()` aufgerufen. Diese Methode ist ein Member von `IMobileServicesSyncContext` und nicht der Synchronisierungstabelle, da Änderungen per Push auf alle Tabellen übertragen werden. Nur Datensätze, die lokal geändert wurden (mit CRUD-Operationen), werden an den Server gesendet.

    Als Nächstes ruft die Methode `IMobileServiceSyncTable.PullAsync()` auf, um Daten mit einem Pullvorgang aus einer Tabelle auf dem Server an die App zu übertragen. Beachten Sie, dass ein Pullvorgang immer zuerst einen Pushvorgang ausführt, wenn der Synchronisierungskontext noch ausstehende Änderungen enthält. Dadurch wird sichergestellt, dass alle Tabellen im lokalen Speicher und die Beziehungen konsistent sind. In diesem Fall wurde Push explizit aufgerufen.

    In diesem Beispiel werden alle Datensätze in der `TodoItem`-Remotetabelle abgerufen, Sie können die Datensätze aber auch durch Übergeben einer Abfrage filtern. Der erste Parameter für `PullAsync()` ist eine Abfrage-ID für eine inkrementelle Synchronisierung. Diese verwendet den `UpdatedAt`-Zeitstempel, um nur die Datensätze abzurufen, die seit der letzten Synchronisierung geändert wurden. Die Abfrage-ID muss eine beschreibende Zeichenfolge sein, die für jede logische Abfrage in Ihrer App eindeutig ist. Um die inkrementelle Synchronisierung zu deaktivieren, übergeben Sie als Abfrage-ID `null`. Dadurch werden in jedem Pullvorgang alle Datensätze abgerufen, was potenziell ineffizient ist.

<!--     >[AZURE.NOTE] Um Einträge vom lokalen Speicher des Geräts zu entfernen, wenn sie in der Datenbank Ihres mobilen Diensts gelöscht wurden, aktivieren Sie die [vorläufige Löschung]. Andernfalls ruft Ihre App in bestimmten Zeitabständen `ImobileServiceSyncTable.PurgeAsync()` auf, um den lokalen Speicher zu bereinigen.

    Beachten Sie, dass der Fehler `MobileServicePushFailedException` sowohl bei einem Push- als auch einem Pull-Vorgang auftreten kann. Das nächste Lernprogramm [Behandeln von Konflikten mit Offline-Support für Mobile Services] zeigt Ihnen, wie Sie mit Ausnahmen im Zusammenhang mit der Synchronisation umgehen können.
-->

5. In der `QSTodoService`-Klasse wird die `SyncAsync()`-Methode nach den Vorgängen zum Ändern von Daten, `InsertTodoItemAsync()` und `CompleteItemAsync`, aufgerufen. Sie wird auch von `RefreshDataAsync()` aufgerufen, sodass Benutzer die aktuellsten Daten abrufen, wenn sie die Akualisierungsbewegung durchführen. Die App führt außerdem eine Synchronisierung beim Start durch, da `QSTodoListViewController.ViewDidLoad()` einen Aufruf von `RefreshDataAsync()` durchführt.

    Da bei jeder Änderung von Daten `SyncAsync()` aufgerufen wird, setzt diese App voraus, dass der Benutzer beim Bearbeiten von Daten stets online ist. Im nächsten Abschnitt wird die App so aktualisiert, dass Benutzer Daten auch dann bearbeiten können, wenn sie offline sind.

## Aktualisieren des Synchronisierungsverhaltens der App

In diesem Abschnitt ändern Sie die App so, dass sie beim App-Start oder bei Einfüge- und Updatevorgängen keine Synchronisierung durchführt, sondern nur beim Durchführen der Aktualisierungsbewegung. Anschließend trennen Sie die Verbindung zwischen App und mobilem Back-End, um ein Offlineszenario zu simulieren. Wenn Sie Datenelemente hinzufügen, werden diese im lokalen Speicher gespeichert, aber nicht sofort mit dem mobilen Back-End synchronisiert.

1. Öffnen Sie `QSTodoService.cs`. Kommentieren Sie die Aufrufe von `SyncAsync()` in den folgenden Methoden aus:

    - `InsertTodoItemAsync`
    - `CompleteItemAsync`
    - `RefreshAsync`

    Jetzt lädt `RefreshAsync()` nur Daten aus dem lokalen Speicher, stellt aber keine Verbindung mit dem App-Back-End her.

2. Ändern Sie in `QSTodoService.cs` die Definition von `applicationURL`, um auf einen ungültigen URI für die mobile App zu verweisen:

        const string applicationURL = @"https://your-service.azurewebsites.xxx/"; // invalid URI

3. Um sicherzustellen, dass die Daten bei Ausführung der Aktualisierungsbewegung synchronisiert werden, bearbeiten Sie die `QSTodoListViewController.RefreshAsync()`-Methode. Fügen Sie einen Aufruf von `SyncAsync()` hinzu, bevor `RefreshDataAsync()` aufgerufen wird:

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

2. Erstellen Sie die App erneut, und führen Sie sie aus. Beachten Sie, dass die Daten nicht geändert wurden, obwohl die App jetzt mit dem mobilen Back-End verbunden ist. Dies liegt daran, dass die App immer die `IMobileServiceSyncTable` verwendet, die auf den lokalen Speicher verweist.

3. Stellen Sie eine Verbindung mit der SQL-Datenbank für Ihr Back-End her, um die Daten anzuzeigen, die gespeichert wurden. Wechseln Sie in Visual Studio zu **Server-Explorer** -> **Azure** -> **SQL-Datenbanken**. Klicken Sie mit der rechten Maustaste auf Ihre Datenbank, und wählen Sie **In SQL Server-Objekt-Explorer öffnen** aus.

    Beachten Sie, dass die Daten zwischen Datenbank und lokalem Speicher *nicht* synchronisiert wurden.

4. Führen Sie in der App die Aktualisierungsbewegung aus, indem Sie die Elementliste nach unten ziehen. Dies bewirkt, dass die App `RefreshDataAsync()` aufruft, wodurch wiederum `SyncAsync()` aufgerufen wird. Hierdurch werden die Push- und Pullvorgänge ausgeführt, wobei zunächst die Elemente im lokalen Speicher an das mobile Back-End gesendet und dann die neuen Daten aus dem Back-End abgerufen werden.

5. Aktualisieren Sie die Datenbankansicht, und vergewissern Sie sich, dass die Änderungen synchronisiert wurden.

<!-- ##Summary

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Next steps

* [Handling conflicts with offline support for Mobile Services]

* [How to use the Xamarin Component client for Azure Mobile Services]
 -->

<!-- Images -->

<!-- URLs. -->
[Erstellen einer Xamarin iOS-App]: ../app-service-mobile-dotnet-backend-xamarin-ios-get-started.md

[How to use the Xamarin Component client for Azure Mobile Services]: ../partner-xamarin-mobile-services-how-to-use-client-library.md

[Xamarin Studio]: http://xamarin.com/download
[Xamarin-Erweiterung]: http://xamarin.com/visual-studio
 

<!---HONumber=July15_HO4-->