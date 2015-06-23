<properties 
	pageTitle="Verwenden von Offlinedaten in Mobile Services (Xamarin iOS) | Mobile Dev Center" 
	description="Erfahren Sie, wie Sie Azure Mobile Services verwenden, um Offlinedaten in Ihrer Xamarin iOS-Anwendung zwischenzuspeichern und zu synchronisieren." 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	editor="wesmc" 
	manager="dwrede" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/20/2015" 
	ms.author="donnam"/>

# Verwendung der Offlinedatensynchronisierung in Mobile Services

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

In diesem Thema werden die Offlinesynchronisierungsfunktionen von Azure Mobile Services in der TodoList-Schnellstart-App erläutert. Die Offlinesynchronisierung ermöglicht Ihnen die einfache Erstellung von Apps, die auch dann verwendet werden können, wenn der Benutzer keinen Netzwerkzugriff hat.

Die Offlinesynchronisierung hat eine Reihe potenzieller Nutzen:

* Verbesserte Reaktionsfähigkeit der App durch das lokale Caching von Serverdaten auf dem Gerät
* Macht Apps unempfindlich gegen Unterbrechungen der Netzwerkverbindung
* Endanwender können Daten selbst dann erstellen und ändern, wenn sie keinen Netzwerkzugriff haben, also in Szenarien mit wenig oder keiner Konnektivität
* Daten auf mehreren Geräten synchronisieren und Konflikte erkennen, wenn derselbe Datensatz von zwei Geräte aus geändert wird

>[AZURE.NOTE]Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen können. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Kostenlose Azure-Testversion</a>.
>
> Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Überprüfen des Mobile Services-Synchronisierungscodes]
2. [Aktualisieren des Synchronisierungsverhaltens der App]
3. [Aktualisieren der App zur Herstellung einer Verbindung mit dem mobilen Dienst]

Für dieses Lernprogramm ist Folgendes erforderlich:

* Visual Studio mit der [Xamarin-Erweiterung] **oder** [Xamarin Studio] auf OS X
* XCode 4.5 und iOS 6.0 (oder neuere Versionen) 
* Abschluss des Lernprogramms [Erste Schritte mit Mobile Services]

## <a name="review-offline"></a>Überprüfen des Mobile Services-Synchronisierungscodes

Mit der Offlinesynchronisierung von Azure Mobile Services können Endbenutzer mit einer lokalen Datenbank interagieren, wenn das Netzwerk nicht verfügbar ist. Initialisieren Sie `MobileServiceClient.SyncContext` in einem lokalen Speicher, um diese Funktionen in der App zu verwenden. Erstellen Sie dann über die `IMobileServiceSyncTable`-Schnittstelle einen Verweis für die Tabelle. In diesem Abschnitt wird der in `QSTodoService.cs` enthaltene Code für die Offlinesynchronisierung Schritt für Schritt besprochen.

1. Öffnen Sie in Visual Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit Mobile Services] abgeschlossen haben. Öffnen Sie die Datei `QSTodoService.cs`.

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

    Hiermit wird ein lokaler Speicher mit der `MobileServiceSQLiteStore`-Klasse erstellt, die im Mobile Services SDK bereitgestellt wird. Sie können auch eine andere lokale Speicherimplementierung angeben, indem Sie `IMobileServiceLocalStore` implementieren.

    Die `DefineTable`-Methode erstellt eine Tabelle im lokalen Speicher, die mit den Feldern im bereitgestellten Typ übereinstimmt (in diesem Fall `ToDoItem`). Der Typ muss nicht alle Spalten der Remotedatenbank enthalten; es ist möglich, nur eine Teilmenge der Spalten zu speichern.

    Diese Überladung von `InitializeAsync` verwendet den Standardkonflikthandler, der bei einem Konflikt einen Fehler ausgibt. Informationen zum Bereitstellen eines benutzerdefinierten Konflikthandlers finden Sie im Lernprogramm [Behandeln von Konflikten mit der Offlineunterstützung für Mobile Services].

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

    >[AZURE.NOTE]Um Datensätze aus dem lokalen Speicher des Geräts zu entfernen, wenn sie in der Datenbank des mobilen Diensts gelöscht wurden, aktivieren Sie [Vorläufiges Löschen]. Andernfalls sollte Ihre App in regelmäßigen Abständen `IMobileServiceSyncTable.PurgeAsync()` aufrufen, um den lokalen Speicher zu löschen.

    Beachten Sie, dass die `MobileServicePushFailedException` sowohl für einen Push- als auch für einen Pullvorgang auftreten kann. Im nächsten Lernprogramm, [Behandeln von Konflikten mit der Offlineunterstützung für Mobile Services], wird gezeigt, wie Sie mit solchen synchronisationsbezogenen Ausnahmen umgehen.

5. In der `QSTodoService`-Klasse wird die `SyncAsync()`-Methode nach den Vorgängen zum Ändern von Daten, `InsertTodoItemAsync()` und `CompleteItemAsync`, aufgerufen. Sie wird auch von `RefreshDataAsync()` aufgerufen, sodass Benutzer die aktuellsten Daten abrufen, wenn sie die Akualisierungsbewegung durchführen. Die App führt außerdem eine Synchronisierung beim Start durch, da `QSTodoListViewController.ViewDidLoad()` einen Aufruf von `RefreshDataAsync()` durchführt.

    Da bei jeder Änderung von Daten `SyncAsync()` aufgerufen wird, setzt diese App voraus, dass der Benutzer beim Bearbeiten von Daten stets online ist. Im nächsten Abschnitt wird die App so aktualisiert, dass Benutzer Daten auch dann bearbeiten können, wenn sie offline sind.

## <a name="update-sync"></a>Aktualisieren des Synchronisierungsverhaltens der App

In diesem Abschnitt ändern Sie die App so, dass sie beim App-Start oder bei Einfüge- und Updatevorgängen keine Synchronisierung durchführt, sondern nur beim Durchführen der Aktualisierungsbewegung. Anschließend trennen Sie die Verbindung zwischen App und mobilem Dienst, um ein Offlineszenario zu simulieren. Wenn Sie Datenelemente hinzufügen, werden diese im lokalen Speicher gespeichert, aber nicht sofort mit dem mobilen Dienst synchronisiert.

1. Öffnen Sie `QSTodoService.cs`. Kommentieren Sie die Aufrufe von `SyncAsync()` in den folgenden Methoden aus:

    - `InsertTodoItemAsync`
    - `CompleteItemAsync`
    - `RefreshAsync`

    Jetzt lädt `RefreshAsync()` nur Daten aus dem lokalen Speicher, stellt aber keine Verbindung mit dem App-Back-End her.

2. Kommentieren Sie in `QSTodoService.cs` die Definitionen der Member `applicationURL` und `applicationKey` aus. Fügen Sie die folgenden Zeilen hinzu, die auf eine ungültige mobile Dienst-URL verweisen:

        const string applicationURL = @"https://your-mobile-service.azure-mobile.xxx/";
        const string applicationKey = @"AppKey";

3. Um sicherzustellen, dass die Daten bei Ausführung der Aktualisierungsbewegung synchronisiert werden, bearbeiten Sie die `QSTodoListViewController.RefreshAsync()`-Methode. Fügen Sie einen Aufruf von `SyncAsync()` hinzu, bevor `RefreshDataAsync()` aufgerufen wird:

        private async Task RefreshAsync ()
        {
            RefreshControl.BeginRefreshing ();

            await todoService.SyncAsync();
            await todoService.RefreshDataAsync (); // add this line

            RefreshControl.EndRefreshing ();

            TableView.ReloadData ();
        }

4. Erstellen Sie die App, und führen Sie sie aus. Fügen Sie einige neue Todo-Elemente hinzu. Die neuen Elemente sind nur im lokalen Speicher vorhanden, bis sie per Push an den mobilen Dienst übertragen werden können. Die Client-App verhält sich so, als ob eine Verbindung zum mobilen Dienst vorhanden wäre, und unterstützt alle Erstellungs-, Lese-, Aktualisierungs- und Löschaktionen (CRUD).

5. Schließen Sie die App und starten Sie sie erneut, um zu überprüfen, ob die neuen Elemente dauerhaft im lokalen Speicher gespeichert wurden.

## <a name="update-online-app"></a>Aktualisieren der App zur Herstellung einer Verbindung mit dem mobilen Dienst

In diesem Abschnitt verbinden Sie die App erneut mit dem mobilen Dienst. Dies simuliert, dass die App von einem Offlinestatus in einen Onlinestatus mit dem mobilen Dienst wechselt. Wenn Sie die Aktualisierungsbewegung durchführen, werden Daten mit Ihrem mobilen Dienst synchronisiert.

1. Öffnen Sie `QSTodoService.cs`. Entfernen Sie die ungültige mobile Dienst-URL, und fügen Sie wieder die richtige URL und den richtigen App-Schlüssel hinzu.

2. Erstellen Sie die App erneut, und führen Sie sie aus. Beachten Sie, dass die Daten genau wie im Offlineszenario aussehen, obwohl die App jetzt mit dem mobilen Dienst verbunden ist. Dies liegt daran, dass die App immer die `IMobileServiceSyncTable` verwendet, die auf den lokalen Speicher verweist.

3. Melden Sie sich beim Azure-Verwaltungsportal an, und zeigen Sie die Datenbank für Ihren mobilen Dienst an. Wenn Ihr Dienst das JavaScript-Back-End verwendet, können Sie die Daten auf der Registerkarte **Daten** des mobilen Diensts durchsuchen.

    Wenn Sie das .NET-Back-End für Ihren mobilen Dienst verwenden, wechseln Sie in Visual Studio zu **Server-Explorer** -> **Azure** -> **SQL-Datenbanken**. Klicken Sie mit der rechten Maustaste auf Ihre Datenbank, und wählen Sie **In SQL Server-Objekt-Explorer öffnen** aus.

    Beachten Sie, dass die Daten zwischen Datenbank und lokalem Speicher *nicht* synchronisiert wurden.

4. Führen Sie in der App die Aktualisierungsbewegung aus, indem Sie die Elementliste nach unten ziehen. Dies bewirkt, dass die App `RefreshDataAsync()` aufruft, wodurch wiederum `SyncAsync()` aufgerufen wird. Hierdurch werden die Push- und Pullvorgänge ausgeführt, wobei zunächst die Elemente im lokalen Speicher an den mobilen Dienst gesendet und dann die neuen Daten aus dem Dienst abgerufen werden.

5. Überprüfen Sie die Datenbank für Ihren mobilen Dienst, um zu bestätigen, dass die Änderungen synchronisiert wurden.

## Zusammenfassung

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../../includes/mobile-services-offline-summary-csharp.md)]

## Nächste Schritte

* [Behandeln von Konflikten mit der Offlineunterstützung für Mobile Services]

* [Verwenden des Clients der Xamarin-Komponente für Azure Mobile Services]

<!-- Anchors. -->
[Überprüfen des Mobile Services-Synchronisierungscodes]: #review-offline
[Aktualisieren des Synchronisierungsverhaltens der App]: #update-sync
[Aktualisieren der App zur Herstellung einer Verbindung mit dem mobilen Dienst]: #update-online-app

<!-- Images -->

<!-- URLs. -->
[Behandeln von Konflikten mit der Offlineunterstützung für Mobile Services]: ../mobile-services-xamarin-ios-handling-conflicts-offline-data.md
[Get started with data]: mobile-services-ios-get-started-data.md
[Erste Schritte mit Mobile Services]: mobile-services-ios-get-started.md
[Verwenden des Clients der Xamarin-Komponente für Azure Mobile Services]: partner-xamarin-mobile-services-how-to-use-client-library.md
[Vorläufiges Löschen]: mobile-services-using-soft-delete.md

[Xamarin Studio]: http://xamarin.com/download
[Xamarin-Erweiterung]: http://xamarin.com/visual-studio

<!--HONumber=54--> 