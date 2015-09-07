<properties
	pageTitle="Aktivieren der Offlinesynchronisierung für Ihre Azure Mobile App (Windows 8.1) | Microsoft Azure"
	description="Erfahren Sie, wie Sie eine mobile Azure-App verwenden, um Offlinedaten in Ihrer Windows Store-Anwendung zwischenzuspeichern und zu synchronisieren."
	documentationCenter="windows"
	authors="wesmc7777"
	manager="dwrede"
	editor=""
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-windows"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="08/22/2015"
	ms.author="wesmc"/>

# Aktivieren der Offlinesynchronisierung für Ihre Windows-App

[AZURE.INCLUDE [app-service-mobile-selector-offline-preview](../../includes/app-service-mobile-selector-offline-preview.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services-preview](../../includes/app-service-mobile-note-mobile-services-preview.md)]

## Übersicht

In diesem Lernprogramm wird das Hinzufügen der Offlineunterstützung zu einer Windows 8.1 Store-App oder Phone-App mithilfe eines Azure Mobile App-Back-Ends erläutert. Offlinesynchronisierung ermöglicht Endbenutzern die Interaktion mit einer mobilen App – Anzeigen, Hinzufügen und Ändern von Daten –, auch wenn keine Netzwerkverbindung vorhanden ist. Änderungen werden in einer lokalen Datenbank gespeichert. Sobald das Gerät wieder online ist, werden diese Änderungen mit dem Remote-Back-End synchronisiert.

In diesem Lernprogramm aktualisieren Sie das Windows 8.1-App-Projekt aus dem Lernprogramm [Erstellen einer Windows-App] zur Unterstützung der Offlinefunktionen Azure Mobile Apps. Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt die Datenzugriffs-Erweiterungspakete hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) (in englischer Sprache).

Weitere Informationen zur Offlinesynchronisierungsfunktion finden Sie im Thema [Offlinedatensynchronisierung in Azure Mobile Apps].

## Anforderungen

Für dieses Lernprogramm ist Folgendes erforderlich:

* Visual Studio 2013 für Windows 8.1.
* Durchführung des Lernprogramms [Erstellen einer Windows-App][create a windows app].
* [Azure Mobile Services SQLite Store Version 2.0.0-beta2][sqlite store nuget]
* [SQLite für Windows 8.1](http://www.sqlite.org/downloads)

>[AZURE.NOTE]Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Kostenlose Azure-Testversion</a>.

## Aktualisieren der Client-App für die Unterstützung von Offlinefunktionen

Offlinefunktionen der mobilen Azure-App ermöglichen Ihnen die Interaktion mit einer lokalen Datenbank, wenn Sie sich in einem Offlineszenario befinden. Um diese Funktionen in der App zu verwenden, initialisieren Sie einen `MobileServiceClient.SyncContext` in einem lokalen Speicher. Erstellen Sie dann über die `IMobileServiceSyncTable`-Schnittstelle einen Verweis für die Tabelle. In diesem Lernprogramm verwenden wir SQLite als lokalen Speicher.

1. Installieren Sie die SQLite-Laufzeit für Windows 8.1 und Windows Phone 8.1.

    * **Windows 8.1-Runtime:** Installieren Sie [SQLite für Windows 8.1].
    * **Windows Phone 8.1:** Installieren Sie [SQLite für Windows Phone 8.1].

    >[AZURE.NOTE]Wenn Sie Internet Explorer verwenden und auf den Link für das Herunterladen von SQLite klicken, werden Sie möglicherweise aufgefordert, die VSIX-Datei als ZIP-Datei herunterzuladen. Speichern Sie die Datei mit der Endung .vsix statt.zip an einem Speicherort auf Ihrer Festplatte. Doppelklicken Sie im Windows-Explorer auf die VSIX-Datei, um die Installation auszuführen.

2. Öffnen Sie in Visual Studio das Projekt, das Sie in [Erstellen einer Windows-App] abgeschlossen haben. Installieren Sie das **WindowsAzure.MobileServices.SQLiteStore**-NuGet-Paket für die Windows 8.1-Laufzeit und für Windows Phone 8.1-Projekte.

    Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten**, um den NuGet-Paket-Manager auszuführen. Wählen Sie auf der Registerkarte "Online" in der Dropdownliste oben die Option "Vorversion einschließen". Suchen Sie nach **SQLiteStore**, um die Betaversion 2.0.0 von `WindowsAzure.MobileServices.SQLiteStore` zu installieren.

    Dann fügen Sie den Windows Store 8.1- und den Windows Phone 8.1-Projekten den NuGet-Verweis hinzu.

    >[AZURE.NOTE]Wenn die Installation einen zusätzlichen Verweis auf eine andere Version von SQLite erstellt als die von Ihnen installierte, erhalten Sie einen Kompilierungsfehler. Sie sollten den Fehler beheben, indem Sie das Duplikat im Knoten **Verweise** in Ihren Projekten entfernen.

3. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise** für Windows 8.1-Runtime- und Windows Phone 8.1-Plattformprojekte, und vergewissern Sie sich, dass ein Verweis auf SQLite vorhanden ist, der sich im Abschnitt **Erweiterungen** befindet.

    ![][1] </br>

    **Windows 8.1-Runtime**

    ![][11] </br>

    **Windows Phone 8.1**

4. SQLite ist eine systemeigene Bibliothek und setzt voraus, dass Sie über eine plattformspezifische Architektur verfügen, z. B. **x86**, **x64** oder **ARM**. **Beliebige CPU** wird nicht unterstützt. Klicken Sie im Projektmappen-Explorer auf die oberste Projektmappe, und ändern Sie dann den Wert im Dropdownfeld für die Prozessorarchitektur in eine der unterstützten Einstellungen, die Sie testen möchten.

    ![][13]

5. Öffnen Sie im Projektmappen-Explorer im freigegebenen Projekt die Datei "MainPage.cs". Heben Sie die Auskommentierung der folgenden Zeilen mithilfe von Anweisungen am Anfang der Datei auf:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. Kommentieren Sie in der Datei "MainPage.cs" die Codezeile aus, die `todoTable` als `IMobileServiceTable` initialisiert. Entfernen Sie den Kommentar in der Codezeile, die `todoTable` als `IMobileServiceSyncTable` initialisiert:

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>(); // offline sync

7. Heben Sie in "MainPage.cs" im mit `Offline sync` markierten Bereich die Auskommentierung der Methoden `InitLocalStoreAsync` und `SyncAsync` auf. Die `InitLocalStoreAsync`-Methode initialisiert den Clientsynchronisierungskontext mit einem SQLite-Store. In Visual Studio können Sie alle kommentierten Zeilen auswählen und die Tastenkombination **STRG**+**K**+**U** verwenden, um Kommentare zu entfernen.

	Beachten Sie, dass in `SyncAsync` ein Pushvorgang gegen den `MobileServiceClient.SyncContext` anstatt der `IMobileServicesSyncTable` ausgeführt wird. Das liegt daran, dass der Kontext Änderungen vom Client für alle Tabellen verfolgt. Dies ist bei Szenarios erforderlich, in denen Beziehungen zwischen Tabellen bestehen. Weitere Informationen zu diesem Verhalten finden Sie unter [Offlinedatensynchronisierung in Azure Mobile Apps].

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

10. Fügen Sie in der `SyncAsync`-Methode Ausnahmehandler hinzu. In einer Offlinesituation wird eine `MobileServicePushFailedException` mit `PushResult.Status == CancelledByNetworkError` ausgegeben.

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
                errorString = "Push failed because of sync errors. You may be offine.\nMessage: " +
                  ex.Message + "\nPushResult.Status: " + ex.PushResult.Status.ToString();
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

    In diesem `PullAsync`-Beispiel werden alle Datensätze in der Remote-`todoTable` abgerufen, Sie können die Datensätze aber auch durch Übergeben einer Abfrage filtern. Der erste Parameter für `PullAsync` ist eine Abfrage-ID für eine inkrementelle Synchronisierung. Diese verwendet den `UpdatedAt`-Zeitstempel, um nur Datensätze abzurufen, die seit der letzten Synchronisierung geändert wurden. Die Abfrage-ID muss eine beschreibende Zeichenfolge sein, die für jede logische Abfrage in Ihrer Client-Anwendung eindeutig ist. Um die inkrementelle Synchronisierung zu deaktivieren, übergeben Sie als Abfrage-ID `null`. Dadurch werden bei jedem Pullvorgang alle Datensätze abgerufen, was potenziell ineffizient ist.

    Beachten Sie, dass die `MobileServicePushFailedException` sowohl für einen Push- als auch für einen Pullvorgang auftreten kann. Sie kann für einen Pullvorgang auftreten, weil dieser intern einen Pushvorgang ausführt, um sicherzustellen, dass alle Tabellen und Beziehungen konsistent sind.

11. Drücken Sie in Visual Studio die Taste **F5**, um die Client-App erneut zu erstellen und auszuführen. Die App verhält sich genauso wie vor den Offlinesynchronisierungsänderungen, da sie einen Synchronisierungsvorgang bei Einfüge-, Update- und Aktualisierungsvorgängen ausführt. Es wird jedoch eine lokale Datenbank befüllt, die in einem Offlineszenario verwendet werden kann. Jetzt, da die lokale Datenbank befüllt ist, wird im nächsten Abschnitt ein Offlineszenario ausgelöst.

## <a name="update-sync"></a>Aktualisieren des Synchronisierungsverhaltens der Client-App

In diesem Abschnitt ändern Sie die Client-App, um ein Offlineszenario durch Kappen der Verbindung mit dem Azure Mobile App-Back-End zu simulieren. Wenn Sie Datenelemente hinzufügen, informiert Sie der Ausnahmehandler, dass die App im Offlinemodus mit `PushResult.Status == CancelledByNetworkError` ausgeführt wird. Hinzugefügte Elemente werden im lokalen Speicher abgelegt, aber nicht mit dem mobilen App-Back-End synchronisiert, bis Sie wieder online sind und einen erfolgreichen Pushvorgang an das Azure Mobile App-Back-End durchführen.

1. Bearbeiten Sie "App.xaml.cs" im freigegebenen Projekt. Kommentieren Sie die Initialisierung von **MobileServiceClient** aus, und fügen Sie die folgenden Zeilen hinzu, die eine ungültige mobile App-URL verwenden:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-service.azurewebsites.fail",
            "https://your-gateway.azurewebsites.fail",
            ""
        );

2. Drücken Sie **F5**, um die Anwendung zu erstellen und auszuführen. Beachten Sie, dass die Synchronisierung bei der Aktualisierung fehlgeschlagen ist, als die App gestartet wurde.
3. Geben Sie neue "todoitems" ein, und klicken Sie jeweils auf **Speichern**. Der Pushvorgang schlägt jeweils mit einem `PushResult.Status=CancelledByNetworkError` fehl. Die neuen Todo-Elemente existieren nur im lokalen Speicher, bis sie per Push zum mobilen App-Back-End übertragen werden können. 
 
	Sie können den Ausnahmedialog für `PushResult.Status=CancelledByNetworkError` unterdrücken. Die Client-App verhält sich dann so, als ob Sie mit dem mobilen App-Back-End verbunden ist, das alle CRUD-Vorgänge (Erstellen, Lesen, Aktualisieren, Löschen) nahtlos unterstützt.

4. Schließen Sie die App, und starten Sie sie neu, um zu überprüfen, ob die neuen Elemente dauerhaft im lokalen Speicher gespeichert wurden.

5. (Optional) Verwenden Sie Visual Studio zum Anzeigen der Azure SQL-Datenbanktabelle, um festzustellen, ob sich die Daten in der Back-End-Datenbank nicht geändert haben.

   Öffnen Sie den **Server-Explorer** in Visual Studio. Navigieren Sie zu der Datenbank in **Azure** -> **SQL-Datenbanken**. Klicken Sie mit der rechten Maustaste auf Ihre Datenbank, und wählen Sie **In SQL Server-Objekt-Explorer öffnen** aus. Jetzt können nach Ihrer SQL-Datenbanktabelle und seinen Inhalten suchen.

6. (Optional) Verwenden Sie ein REST-Tool wie Fiddler oder Postman, um Ihr mobiles Back-End mit einer GET-Abfrage in Form von `https://your-mobile-app-backend-name.azurewebsites.net/tables/TodoItem` abzufragen. 

## <a name="update-online-app"></a>Aktualisieren der App zum erneuten Verbinden des mobilen App-Back-Ends

In diesem Abschnitt verbinden Sie die App erneut mit dem mobilen App-Back-End. Dies simuliert, dass die App von einem Offlinestatus in einen Onlinestatus mit dem mobilen App-Back-End wechselt. Beim ersten Ausführen der Anwendung ruft der `OnNavigatedTo`-Ereignishandler `InitLocalStoreAsync` auf. Dadurch wird wiederum `SyncAsync` aufgerufen, um den lokalen Speicher mit der Back-End-Datenbank zu synchronisieren. Daher versucht die App, die Synchronisierung beim Start auszuführen.

1. Öffnen Sie "App.xaml.cs" im freigegebenen Projekt. Heben Sie die Auskommentierung der vorherigen Initialisierung von `MobileServiceClient` auf, um wieder die richtige mobile App-URL und die richtige Gateway-URL zu verwenden.

2. Drücken Sie die Taste **F5**, um die App erneut zu erstellen und auszuführen. Die App synchronisiert die lokalen Änderungen mit dem Azure Mobile App-Back-End mithilfe von Push- und Pullvorgängen, sobald der `OnNavigatedTo`-Ereignishandler ausgeführt wird.

3. (Optional) Zeigen Sie die aktualisierten Daten mithilfe von SQL Server-Objekt-Explorer oder einem REST-Tool wie Fiddler an. Beachten Sie, dass die Daten zwischen der Azure Mobile App-Back-End-Datenbank und dem lokalen Speicher synchronisiert wurden.

4. Klicken Sie in der App auf das Kontrollkästchen neben einigen Elementen, um sie im lokalen Speicher abzuschließen.

  `UpdateCheckedTodoItem` ruft `SyncAsync` auf, um jedes Element vollständig mit dem Mobile App-Back-End zu synchronisieren. `SyncAsync` ruft sowohl Push- als auch Pullvorgänge auf. Sie sollten jedoch beachten, dass **immer dann, wenn Sie einen Pullvorgang gegen eine Tabelle ausführen, die der Client geändert hat, zunächst immer automatisch ein Pushvorgang im Clientsynchronisierungskontext ausgeführt wird**. Dadurch wird sichergestellt, dass alle Tabellen im lokalen Speicher und die Beziehungen konsistent bleiben. In diesem Fall hätte der Aufruf von `PushAsync` entfernt werden können, da er automatisch bei der Ausführung eines Pullvorgangs ausgeführt wird. Dieses Verhalten kann zu einem unerwarteten Pushvorgang führen, wenn Sie sich dessen nicht bewusst sind. Weitere Informationen zu diesem Verhalten finden Sie unter [Offlinedatensynchronisierung in Azure Mobile Apps].


##Zusammenfassung

Um die Offlinefunktionen mobiler Dienste zu unterstützen, haben Sie die Schnittstelle der Tabelle `IMobileServiceSyncTable` verwendet und `MobileServiceClient.SyncContext` mit einem lokalen Speicher initialisiert. In diesem Fall war der lokale Speicher eine SQLite-Datenbank.

Die normalen CRUD-Operationen für mobile Dienste funktionieren so, als ob die App immer noch verbunden wäre. Alle Operationen erfolgen jedoch nur im lokalen Speicher.

Zur Synchronisierung des lokalen Speichers mit dem Server wurden die Methoden `IMobileServiceSyncTable.PullAsync` und `MobileServiceClient.SyncContext.PushAsync` verwendet.

*  Anschließend haben Sie `IMobileServiceSyncContext.PushAsync()` aufgerufen, um die Änderungen per Push-Vorgang auf den Server zu übertragen. Diese Methode ist ein Element von `IMobileServicesSyncContext` und nicht der Synchronisierungstabelle, da Änderungen per Push auf alle Tabellen übertragen werden.

    Nur Datensätze, die lokal geändert wurden (mit CRUD-Operationen), werden an den Server gesendet.

* Um Daten per Pull-Vorgang von einer Tabelle auf dem Server in die App zu übertragen, haben Sie `IMobileServiceSyncTable.PullAsync` aufgerufen.

    Ein Pullvorgang gibt immer zuerst einen Pushvorgang im Synchronisierungskontext aus, wenn der Clientsynchronisierungskontext Änderungen in dieser Tabelle entdeckt hat. Dadurch wird sichergestellt, dass alle Tabellen im lokalen Speicher und die Beziehungen konsistent bleiben.

    Es gibt auch Überladungen von `PullAsync()`, die es ermöglichen, eine Abfrage zu bestimmen, um die Daten zu filtern, die auf dem Client gespeichert sind. Wenn eine Abfrage nicht weitergegeben wird, bezieht `PullAsync()` alle Zeilen in der entsprechenden Tabelle (oder Abfrage). Sie können die Abfrage weitergeben, um nur die Änderungen zu filtern, mit denen Ihre App synchronisiert werden soll.

* Um die inkrementelle Synchronisierung zu aktivieren, übergeben Sie eine Abfrage-ID an `PullAsync()`. Die Abfrage-ID wird verwendet, um den zuletzt aktualisierten Zeitstempel aus den Ergebnissen des letzten Pullvorgangs zu speichern. Die Abfrage-ID muss eine beschreibende Zeichenfolge sein, die für jede logische Abfrage in Ihrer App eindeutig ist. Wenn die Abfrage einen Parameter aufweist, kann derselbe Parameterwert Teil der Abfrage-ID sein.

    Wenn Sie beispielsweise nach der Benutzer-ID filtern, könnte Ihre eindeutige Abfrage-ID so aussehen:

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    Wenn Sie die inkrementelle Synchronisierung deaktivieren möchten, übergeben Sie `null` als Abfrage-ID. In diesem Fall werden alle Datensätze bei jedem Aufruf von `PullAsync` abgerufen, was möglicherweise ineffizient ist.

* Ihre App sollte in regelmäßigen Abständen `IMobileServiceSyncTable.PurgeAsync()` aufrufen, um den lokalen Speicher zu löschen.


## Zusätzliche Ressourcen

* [Offlinedatensynchronisierung in Azure Mobile Apps]

* [Cloud Cover: Offlinesynchronisierung in Azure Mobile Services] (Hinweis: Im Video geht es zwar um Mobile Services, aber die Offlinesynchronisierung in Azure Mobile Apps funktioniert auf ähnliche Weise.)

* [Azure Friday: Offlinefähige Apps in Azure Mobile Services]

<!-- Anchors. -->
[Update the app to support offline features]: #enable-offline-app
[Update the sync behavior of the app]: #update-sync
[Update the app to reconnect your Mobile Apps backend]: #update-online-app
[Next Steps]: #next-steps

<!-- Images -->
[1]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-reference-sqlite-dialog.png
[11]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/app-service-mobile-add-wp81-reference-sqlite-dialog.png
[13]: ./media/app-service-mobile-windows-store-dotnet-get-started-offline-data-preview/cpu-architecture.png


<!-- URLs. -->
[Offlinedatensynchronisierung in Azure Mobile Apps]: ../app-service-mobile-offline-data-sync-preview.md
[Synchronisierung von Offlinedaten in Azure Mobile Apps]: ../app-service-mobile-offline-data-sync-preview.md
[create a windows app]: ../app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md
[Erstellen einer Windows-App]: ../app-service-mobile-dotnet-backend-windows-store-dotnet-get-started-preview.md
[SQLite für Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite für Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953

[azure mobile app sdk nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/2.0.0-beta
[sqlite store nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/2.0.0-beta
 
[Cloud Cover: Offlinesynchronisierung in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offlinefähige Apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=August15_HO9-->