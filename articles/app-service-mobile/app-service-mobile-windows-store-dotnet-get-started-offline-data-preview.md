<properties
	pageTitle="Verwenden von Offlinedaten mit mobilen Azure-Apps (Windows Store) | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine mobile Azure-App verwenden, um Offlinedaten in Ihrer Windows Store-Anwendung zwischenzuspeichern und zu synchronisieren."
	documentationCenter="windows"
	authors="christopheranderson"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/22/2015"
	ms.author="chrande"/>

# Verwenden der Offlinedatensynchronisierung in mobilen Azure-Apps

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]


In diesem Lernprogramm wird das Hinzufügen der Offlineunterstützung zu einer Windows Universal Store-App mithilfe eines mobilen Azure-App-Back-Ends erläutert. Offlineunterstützung ermöglicht Ihnen die Interaktion mit einer lokalen Datenbank, wenn sich Ihre App in einem Offlineszenario befindet. Sobald Ihre App wieder online und mit der Back-End-Datenbank verbunden ist, synchronisieren Sie die lokalen Änderungen mithilfe der Offlinefunktionen.



In diesem Lernprogramm aktualisieren Sie das Universal App-Projekt aus dem Lernprogramm [Erstellen einer Windows-App] zur Unterstützung der Offlinefunktionen von mobilen Azure-Apps. Anschließend fügen Sie Daten in einem Offlineszenario mit getrennter Verbindung hinzu, synchronisieren diese Elemente mit der Onlinedatenbank und melden sich daraufhin beim Azure-Verwaltungsportal an, um Änderungen an den Daten anzuzeigen, die bei der Ausführung der Anwendung vorgenommen wurden.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Aktualisieren der App für die Unterstützung von Offlinefunktionen]
2. [Aktualisieren des Synchronisierungsverhaltens der App]
3. [Aktualisieren der App zum erneuten Verbinden des mobilen App-Back-Ends]

Für dieses Lernprogramm ist Folgendes erforderlich:

* Visual Studio 2013 für Windows 8.1.
* Durchführung des Lernprogramms [Erstellen einer Windows-App][create a windows app].
* [Azure Mobile Services SDK Version 2.0.0 \(oder höher\)][azure mobile app sdk nuget]
* [Azure Mobile Services SQLite Store Version 1.0.2 \(oder höher\)][sqlite store nuget]
* [SQLite für Windows 8.1](www.sqlite.org/downloads)

>[AZURE.NOTE]Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Kostenlose Azure-Testversion</a>.

##<a name="review"></a>Überprüfen der Konfiguration der Serverprojekts \(optional\)

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-enable-offline-preview](../../includes/app-service-mobile-dotnet-backend-enable-offline-preview.md)]

## <a name="enable-offline-app"></a>Aktualisieren der App für die Unterstützung von Offlinefunktionen

Offlinefunktionen der mobilen Azure-App ermöglichen Ihnen die Interaktion mit einer lokalen Datenbank, wenn Sie den Mobile Service offline verwenden. Um diese Funktionen in der App zu verwenden, initialisieren Sie einen `MobileServiceClient.SyncContext` in einem lokalen Speicher. Erstellen Sie dann über die `IMobileServiceSyncTable`-Schnittstelle einen Verweis für die Tabelle. In diesem Lernprogramm verwenden wir SQLite als lokalen Speicher.

1. Installieren Sie die SQLite-Laufzeit für Windows 8.1 und Windows Phone 8.1.

    * **Windows 8.1-Runtime:** Installieren Sie [SQLite für Windows 8.1].
    * **Windows Phone 8.1:** Installieren Sie [SQLite für Windows Phone 8.1].

    >[AZURE.NOTE]Wenn Sie Internet Explorer verwenden und auf den Link für das Herunterladen von SQLite klicken, werden Sie möglicherweise aufgefordert, die VSIX-Datei als ZIP-Datei herunterzuladen. Speichern Sie die Datei mit der Endung .vsix statt.zip an einem Speicherort auf Ihrer Festplatte. Doppelklicken Sie im Windows-Explorer auf die VSIX-Datei, um die Installation auszuführen.

2. Öffnen Sie in Visual Studio das Projekt, das Sie in [Erstellen einer Windows-App] abgeschlossen haben. Installieren Sie das **WindowsAzure.MobileServices.SQLiteStore**-NuGet-Paket für die Windows 8.1-Laufzeit und für Windows Phone 8.1-Projekte.

    * **Windows 8.1:** Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Windows 8.1-Projekt, und klicken Sie dann auf **NuGet-Pakete verwalten**, um den NuGet-Paket-Manager auszuführen. Suchen Sie nach **SQLiteStore**, um das Paket `WindowsAzure.MobileServices.SQLiteStore` zu installieren.
    * **Windows Phone 8.1:** Klicken Sie mit der rechten Maustaste auf das Windows 8.1-Projekt, und klicken Sie auf **Nuget-Pakete verwalten**, um den NuGet-Paket-Manager auszuführen. Suchen Sie nach **SQLiteStore**, um das Paket `WindowsAzure.MobileServices.SQLiteStore` zu installieren.

    >[AZURE.NOTE]Wenn die Installation einen Verweis auf eine ältere Version von SQLite erstellt, können Sie diesen doppelten Verweis einfach löschen.

3. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise** für Windows 8.1-Runtime- und Windows Phone 8.1-Plattformprojekte, und vergewissern Sie sich, dass ein Verweis auf SQLite vorhanden ist, der sich im Abschnitt **Erweiterungen** befindet.

    ![][1] </br>

    **Windows 8.1-Runtime**

    ![][11] </br>

    **Windows Phone 8.1**

4. Für die SQLite-Laufzeitumgebung muss die Prozessorarchitektur des Projekts, das erstellt wird, zu **x86**, **x64**, oder **ARM** geändert werden. **Beliebige CPU** wird nicht unterstützt. Klicken Sie im Projektmappen-Explorer auf die oberste Projektmappe, und ändern Sie dann den Wert im Dropdownfeld für die Prozessorarchitektur in eine der unterstützten Einstellungen, die Sie testen möchten.

    ![][13]

5. Öffnen Sie im Projektmappen-Explorer im freigegebenen Projekt die Datei "MainPage.cs". Heben Sie die Auskommentierung der folgenden Zeilen mithilfe von Anweisungen am Anfang der Datei auf:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. Kommentieren Sie in "MainPage.cs" die Definition von `todoTable` aus, und heben Sie die Auskommentierung der Definition in der folgenden Zeile auf, die `MobileServicesClient.GetSyncTable()` aufruft:

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. Heben Sie in "MainPage.cs" im mit `Offline sync` markierten Bereich die Auskommentierung der Methoden `InitLocalStoreAsync` und `SyncAsync` auf. Die `InitLocalStoreAsync`-Methode initialisiert den Clientsynchronisierungskontext mit einem SQLite-Store.

        private async Task InitLocalStoreAsync()
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localstore.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }

            await SyncAsync();
        }

        private async Task SyncAsync()
        {
            await App.MobileService.SyncContext.PushAsync();
            await todoTable.PullAsync("todoItems", todoTable.CreateQuery());
        }

8. Heben Sie im `OnNavigatedTo`-Ereignishandler die Auskommentierung des Aufrufs von `InitLocalStoreAsync` auf:

        protected override async void OnNavigatedTo(NavigationEventArgs e)
        {
            await InitLocalStoreAsync(); // offline sync
            await RefreshTodoItems();
        }

9. Heben Sie die Auskommentierung der drei Aufrufe von `SyncAsync` in den Methoden `InsertTodoItem`, `UpdateCheckedTodoItem` und `ButtonRefresh_Click` auf:

        private async Task InsertTodoItem(TodoItem todoItem)
        {
            await todoTable.InsertAsync(todoItem);
            items.Add(todoItem);

            await SyncAsync(); // offline sync
        }

        private async Task UpdateCheckedTodoItem(TodoItem item)
        {
            await todoTable.UpdateAsync(item);
            items.Remove(item);
            ListItems.Focus(Windows.UI.Xaml.FocusState.Unfocused);

            await SyncAsync(); // offline sync
        }

        private async void ButtonRefresh_Click(object sender, RoutedEventArgs e)
        {
            ButtonRefresh.IsEnabled = false;

            await SyncAsync(); // offline sync
            await RefreshTodoItems();

            ButtonRefresh.IsEnabled = true;
        }

10. Fügen Sie in der `SyncAsync`-Methode Ausnahmehandler hinzu:

        private async Task SyncAsync()
        {
            String errorString = null;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
                await todoTable.PullAsync("todoItems", todoTable.CreateQuery()); // first param is query ID, used for incremental sync
            }

            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " +
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Pull failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Pull again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }
        }

    In diesem Beispiel werden alle Datensätze in der Remote-`todoTable` abgerufen, Sie können die Datensätze aber auch durch Übergeben einer Abfrage filtern. Der erste Parameter für `PullAsync` ist eine Abfrage-ID für eine inkrementelle Synchronisierung. Diese verwendet den `UpdatedAt`-Zeitstempel, um nur Datensätze abzurufen, die seit der letzten Synchronisierung geändert wurden. Die Abfrage-ID muss eine beschreibende Zeichenfolge sein, die für jede logische Abfrage in Ihrer App eindeutig ist. Um die inkrementelle Synchronisierung zu deaktivieren, übergeben Sie als Abfrage-ID `null`. Dadurch werden bei jedem Pullvorgang alle Datensätze abgerufen, was potenziell ineffizient ist.

    Beachten Sie, dass die `MobileServicePushFailedException` sowohl für einen Push- als auch für einen Pullvorgang auftreten kann. Sie kann für einen Pullvorgang auftreten, weil dieser intern einen Pushvorgang ausführt, um sicherzustellen, dass alle Tabellen und Beziehungen konsistent sind.

11. Drücken Sie in Visual Studio die Taste **F5**, um die App erneut zu erstellen und auszuführen. Die App verhält sich genauso wie vor den Offlinesynchronisierungsänderungen, da sie einen Synchronisierungsvorgang bei Einfüge-, Update- und Aktualisierungsvorgängen ausführt.

## <a name="update-sync"></a>Aktualisieren des Synchronisierungsverhaltens der App

In diesem Abschnitt ändern Sie die Anwendung so, dass sie bei Einfüge- und Updatevorgängen nicht synchronisiert wird, sondern nur beim Klicken auf die Schaltfläche **Aktualisieren**. Anschließend trennen Sie die Verbindung zwischen App und mobilem App-Back-End, um ein Offlineszenario zu simulieren. Wenn Sie Datenelemente hinzufügen, werden diese im lokalen Speicher gespeichert, jedoch nicht mit dem mobilen App-Back-End synchronisiert.

1. Öffnen Sie "App.xaml.cs" im freigegebenen Projekt. Bearbeiten Sie die Methoden `InsertTodoItem` und `UpdateCheckedTodoItem`, um die Aufrufe von `SyncAsync` auszukommentieren.

2. Bearbeiten Sie "App.xaml.cs" im freigegebenen Projekt. Kommentieren Sie die Initialisierung von **MobileServiceClient** aus, und fügen Sie die folgenden Zeilen hinzu, die eine ungültige mobile App-URL verwenden:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://<your-mobile-service>-code.azurewebsites.fail",
            "https://<your-mobile-service>gateway.azurewebsites.fail",
            "AppKey"
        );

3. Kommentieren Sie in `InitLocalStoreAsync()` den Aufruf von `SyncAsync()` aus, sodass die App beim Start keine Synchronisierung durchführt.

4. Drücken Sie **F5**, um die Anwendung zu erstellen und auszuführen. Geben Sie neue "todoitems" ein, und klicken Sie jeweils auf **Speichern**. Die neuen Todo-Elemente existieren nur im lokalen Speicher, bis sie per Push zum mobilen App-Back-End übertragen werden können. Die Client-App verhält sich so, als ob eine Verbindung zum mobilen App-Back-End vorhanden wäre, und unterstützt alle Erstellungs-, Lese-, Aktualisierungs- und Löschaktionen \(CRUD\).

5. Schließen Sie die App, und starten Sie sie neu, um zu überprüfen, ob die neuen Elemente dauerhaft im lokalen Speicher gespeichert wurden.

## <a name="update-online-app"></a>Aktualisieren der App zum erneuten Verbinden des mobilen App-Back-Ends

In diesem Abschnitt verbinden Sie die App erneut mit dem mobilen App-Back-End. Dies simuliert, dass die App von einem Offlinestatus in einen Onlinestatus mit dem mobilen App-Back-End wechselt. Wenn Sie auf die Schaltfläche "Aktualisieren" klicken, werden Daten mit Ihrem mobilen App-Back-End synchronisiert.

1. Öffnen Sie "App.xaml.cs" im freigegebenen Projekt. Heben Sie die frühere Auskommentierung von `MobileServiceClient` auf, sodass wieder die richtige Back-End-URL für den Mobile App Service, die richtige Gateway-URL und der richtige App-Schlüssel verwendet werden.

2. Drücken Sie die Taste **F5**, um die App erneut zu erstellen und auszuführen. Beachten Sie, dass die Daten genau wie im Offlineszenario aussehen, obwohl die App jetzt mit dem mobilen App-Back-End verbunden ist. Dies liegt daran, dass die App immer mit `IMobileServiceSyncTable` arbeitet und sich diese Tabelle auf den lokalen Speicher bezieht.

3. Melden Sie sich beim Azure-Portal an, und überprüfen Sie die Datenbank für das mobile App-Back-End.

    Wechseln Sie in Visual Studio zu **Server-Explorer** -\> **Azure** -\> **SQL-Datenbanken**. Klicken Sie mit der rechten Maustaste auf Ihre Datenbank, und wählen Sie **In SQL Server-Objekt-Explorer öffnen** aus.

    Beachten Sie, dass die Daten zwischen Datenbank und lokalem Speicher nicht synchronisiert wurden.

4. Klicken Sie in der App auf die Schaltfläche **Aktualisieren**. Daraufhin ruft die App `PushAsync` und `PullAsync` auf. Dieser Pushvorgang sendet die Elemente im lokalen Speicher an den mobilen Dienst und ruft dann neue Daten aus dem mobilen Dienst ab.

    Ein Push-Vorgang wird im `MobileServiceClient.SyncContext` und nicht im `IMobileServicesSyncTable` ausgeführt und überträgt per Push alle Änderungen in alle Tabellen, die mit dem Synchronisierungskontext verknüpft sind. Dies ist bei Szenarios erforderlich, in denen Beziehungen zwischen Tabellen bestehen.

5. Klicken Sie in der App auf das Kontrollkästchen neben einigen Elementen, um sie im lokalen Speicher abzuschließen.

6. Klicken Sie erneut auf die Schaltfläche **Aktualisieren**, um `SyncAsync` aufzurufen. `SyncAsync` ruft Push- und Pullvorgänge auf. In diesem Fall hätten wir den Aufruf von `PushAsync` auch entfernen können. Dies liegt daran, dass ein **Pull-Vorgang immer zuerst einen Push-Vorgang ausführt**. Dadurch wird sichergestellt, dass alle Tabellen im lokalen Speicher und die Beziehungen konsistent bleiben.


##Zusammenfassung

Um die Offlinefunktionen mobiler Dienste zu unterstützen, haben Sie die Schnittstelle der Tabelle `IMobileServiceSyncTable` verwendet und `MobileServiceClient.SyncContext` mit einem lokalen Speicher initialisiert. In diesem Fall war der lokale Speicher eine SQLite-Datenbank.

Die normalen CRUD-Operationen für mobile Dienste funktionieren so, als ob die App immer noch verbunden wäre. Alle Operationen erfolgen jedoch nur im lokalen Speicher.

Zur Synchronisierung des lokalen Speichers mit dem Server haben Sie die Methoden `IMobileServiceSyncTable.PullAsync` und `MobileServiceClient.SyncContext.PushAsync` verwendet.

*  Anschließend haben Sie `IMobileServiceSyncContext.PushAsync()` aufgerufen, um die Änderungen per Push-Vorgang auf den Server zu übertragen. Diese Methode ist ein Element von `IMobileServicesSyncContext` und nicht der Synchronisierungstabelle, da Änderungen per Push auf alle Tabellen übertragen werden.

    Nur Datensätze, die lokal geändert wurden \(mit CRUD-Operationen\), werden an den Server gesendet.

* Um Daten per Pull-Vorgang von einer Tabelle auf dem Server in die App zu übertragen, haben Sie `IMobileServiceSyncTable.PullAsync` aufgerufen.

    Ein Pull-Vorgang führt immer zuerst einen Push-Vorgang aus. Dadurch wird sichergestellt, dass alle Tabellen im lokalen Speicher und die Beziehungen konsistent bleiben.

    Es gibt auch Überladungen von `PullAsync()`, die es ermöglichen, eine Abfrage zu bestimmen, um die Daten zu filtern, die auf dem Client gespeichert sind. Wenn eine Abfrage nicht weitergegeben wird, bezieht `PullAsync()` alle Zeilen in der entsprechenden Tabelle \(oder Abfrage\). Sie können die Abfrage weitergeben, um nur die Änderungen zu filtern, mit denen Ihre App synchronisiert werden soll.

* Um die inkrementelle Synchronisierung zu aktivieren, übergeben Sie eine Abfrage-ID an `PullAsync()`. Die Abfrage-ID wird verwendet, um den zuletzt aktualisierten Zeitstempel aus den Ergebnissen des letzten Pullvorgangs zu speichern. Die Abfrage-ID muss eine beschreibende Zeichenfolge sein, die für jede logische Abfrage in Ihrer App eindeutig ist. Wenn die Abfrage einen Parameter aufweist, muss derselbe Parameterwert Teil der Abfrage-ID sein.

    Wenn Sie beispielsweise nach Benutzer-ID filtern, muss er Teil der Abfrage-ID sein:

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    Wenn Sie die inkrementelle Synchronisierung deaktivieren möchten, übergeben Sie `null` als Abfrage-ID. In diesem Fall werden alle Datensätze bei jedem Aufruf von `PullAsync` abgerufen, was möglicherweise ineffizient ist.

* Ihre App sollte in regelmäßigen Abständen `IMobileServiceSyncTable.PurgeAsync()` aufrufen, um den lokalen Speicher zu löschen.


<!-- Anchors. -->
[Aktualisieren der App für die Unterstützung von Offlinefunktionen]: #enable-offline-app
[Aktualisieren des Synchronisierungsverhaltens der App]: #update-sync
[Aktualisieren der App zum erneuten Verbinden des mobilen App-Back-Ends]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/cpu-architecture.png


<!-- URLs. -->
[todolist offline sample]: http://go.microsoft.com/fwlink/?LinkId=394777
[create a windows app]: /documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/
[Erstellen einer Windows-App]: /documentation/articles/app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview/
[SQLite für Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite für Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953

[azure mobile app sdk nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/2.0.0-beta
[sqlite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.2
 

<!---HONumber=August15_HO7-->