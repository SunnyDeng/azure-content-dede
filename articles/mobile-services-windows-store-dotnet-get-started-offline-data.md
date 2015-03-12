<properties 
	pageTitle="Verwenden von Offlinedaten in Mobile Services (Windows Store) | Mobile Dev Center" 
	description="Erfahren Sie, wie Sie Azure Mobile Services verwenden, um Offlinedaten in Ihrer Windows Store-Anwendung zwischenzuspeichern und zu synchronisieren." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/10/2014" 
	ms.author="wesmc"/>

# Verwenden der Offlinedatensynchronisierung in Mobile Services

[AZURE.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>In diesem Lernprogramm wird das Hinzufügen der Offlineunterstützung zu einer Windows Universal Store-App mithilfe der Azure Mobile Services erläutert. Offlineunterstützung ermöglicht Ihnen die Interaktion mit einer lokalen Datenbank, wenn sich Ihre App in einem Offlineszenario befindet. Sobald Ihre App wieder online und mit der Back-End-Datenbank verbunden ist, synchronisieren Sie die lokalen Änderungen mithilfe der Offlinefunktionen. 
</p>
<p>Wenn Sie lieber ein Video zu diesem Thema ansehen möchten, können Sie den Clip auf der rechten Seite auswählen. In diesem werden dieselben Schritte behandelt wie in diesem Lernprogramm.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">Lernprogramm anzeigen</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Video wiedergeben</span></a> <span class="time">14:36</span></div>
</div>


In diesem Lernprogramm aktualisieren Sie das Universal App-Projekt aus dem Lernprogramm [Erste Schritte mit Mobile Services], um die Offlinefunktionen von Azure Mobile Services zu unterstützen. Anschließend fügen Sie Daten in einem Offlineszenario mit getrennter Verbindung hinzu, synchronisieren diese Elemente mit der Onlinedatenbank und melden sich daraufhin beim Azure-Verwaltungsportal an, um Änderungen an den Daten anzuzeigen, die bei der Ausführung der Anwendung vorgenommen wurden.


>[AZURE.NOTE] In diesem Lernprogramm erfahren Sie, wie Sie Azure Mobile Services verwenden können, um Daten in einer Windows Store-App zu speichern und abzurufen. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie zunächst das Lernprogramm [Erste Schritte mit Mobile Services] abschließen.
>
>Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen können. Einzelheiten finden Sie unter <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Kostenlose Azure-Testversion</a>. 
>
>Das ältere Windows Phone 8-Lernprogramm für Visual Studio 2012 ist hier weiterhin verfügbar: [Windows Phone 8-Lernprogramm für Visual Studio 2012].


In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Aktualisieren der App für die Unterstützung von Offlinefunktionen]
2. [Aktualisieren des Synchronisierungsverhaltens der App] 
3. [Aktualisieren der App zur erneuten Herstellung einer Verbindung mit dem mobilen Dienst]

Für dieses Lernprogramm ist Folgendes erforderlich:

* Visual Studio 2013 für Windows 8.1
* Abschluss des Lernprogramms [Erste Schritte mit Mobile Services].
* [Azure Mobile Services SDK, Version 1.3.0 (oder höher)][Mobile Services SDK Nuget]
* [Azure Mobile Services SQLite Store, Version 1.0.0 (oder höher)][SQLite Store Nuget]
* [SQLite für Windows 8.1](www.sqlite.org/downloads)

>[AZURE.NOTE] Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Kostenlose Azure-Testversion</a>. 

## <a name="enable-offline-app"></a>Aktualisieren der App für die Unterstützung von Offlinefunktionen

Offlinefunktionen von Azure Mobile Services ermöglichen Ihnen die Interaktion mit einer lokalen Datenbank, wenn Sie Ihren mobilen Dienst offline verwenden. Um diese Funktionen in der App zu verwenden, initialisieren Sie einen `MobileServiceClient.SyncContext` in einem lokalen Speicher. Erstellen Sie dann für die Tabelle einen Verweis über die  `IMobileServiceSyncTable`-Schnittstelle. In diesem Lernprogramm verwenden wir SQLite als lokalen Speicher.

>[AZURE.NOTE] Sie können diesen Abschnitt überspringen und einfach das Beispielprojekt, das bereits über Offlineunterstützung verfügt, aus dem GitHub-Beispielrepository für Mobile Services abrufen. Das Beispielprojekt mit aktivierter Offlineunterstützung befindet sich hier: [TodoList-Offlinebeispiel].

1. Installieren Sie die SQLite-Laufzeit für Windows 8.1 und Windows Phone 8.1. 

    * **Windows 8.1-Runtime:** Installieren Sie [SQLite für Windows 8.1].
    * **Windows Phone 8.1:** Installieren Sie [SQLite für Windows Phone 8.1].

    >[AZURE.NOTE] Wenn Sie Internet Explorer verwenden und auf den Link für das Herunterladen von SQLite klicken, werden Sie möglicherweise aufgefordert, die VSIX-Datei als ZIP-Datei herunterzuladen. Speichern Sie die Datei mit der Endung .vsix statt.zip an einem Speicherort auf Ihrer Festplatte. Doppelklicken Sie im Windows-Explorer auf die VSIX-Datei, um die Installation auszuführen.

2. Öffnen Sie in Visual Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit Mobile Services] abgeschlossen haben. Installieren Sie das **WindowsAzure.MobileServices.SQLiteStore** NuGet-Paket für die Windows 8.1-Laufzeit und für Windows Phone 8.1-Projekte.

    * **Windows 8.1:** Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Windows 8.1-Projekt, und klicken Sie auf **Nuget-Pakete verwalten**, um NuGet-Paket-Manager auszuführen. Suchen Sie nach **SQLiteStore**, um das `WindowsAzure.MobileServices.SQLiteStore`-Paket zu installieren.
    * **Windows Phone 8.1:** Klicken Sie mit der rechten Maustaste auf das Windows Phone 8.1-Projekt, und klicken Sie auf **Nuget-Pakete verwalten**, um NuGet-Paket-Manager auszuführen. Suchen Sie nach **SQLiteStore**, um das `WindowsAzure.MobileServices.SQLiteStore`-Paket zu installieren.

    >[AZURE.NOTE] Wenn die Installation einen Verweis auf eine ältere Version von SQLite erstellt, können Sie diesen doppelten Verweis einfach löschen. 

    ![][2]

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise** für Windows 8.1-Runtime- und Windows Phone 8.1-Plattformprojekte, und vergewissern Sie sich, dass ein Verweis auf SQLite vorhanden ist, der sich im Abschnitt **Erweiterungen** befindet. 

    ![][1]
    </br>

    **Windows 8.1-Laufzeit**

    ![][11]
    </br>

    **Windows Phone 8.1**

3. Für die SQLite-Laufzeitumgebung muss die Prozessorarchitektur des Projekts, das erstellt wird, in **x86**, **x64** oder **ARM** geändert werden. **Beliebige CPU** wird nicht unterstützt. Klicken Sie im Projektmappen-Explorer auf die oberste Projektmappe, und ändern Sie dann den Wert im Dropdownfeld für die Prozessorarchitektur in eine der unterstützten Einstellungen, die Sie testen möchten.

    ![][13]

5. Öffnen Sie im Projektmappen-Explorer im freigegebenen Projekt die Datei "MainPage.cs". Heben Sie die Auskommentierung der folgenden Zeilen mithilfe von Anweisungen am Anfang der Datei auf:

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;  // offline sync
        using Microsoft.WindowsAzure.MobileServices.Sync;         // offline sync

6. Kommentieren Sie in "MainPage.cs" die Definition von `todoTable` aus, und heben Sie die Auskommentierung der Definition in der folgenden Zeile auf, die `MobileServicesClient.GetSyncTable()`aufruft:

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
            ite
	ms.Add(todoItem);

            await SyncAsync(); // offline sync
        }

        private async Task UpdateCheckedTodoItem(TodoItem item)
        {
            await todoTable.UpdateAsync(item);
            ite
	ms.Remove(item);
            ListIte
	ms.Focus(Windows.UI.Xaml.FocusState.Unfocused);

            await SyncAsync(); // offline sync
        }

        private async void ButtonRefresh_Click(object sender, RoutedEventArgs e)
        {
            ButtonRefresh.IsEnabled = false;

            await SyncAsync(); // offline sync
            await RefreshTodoItems();

            ButtonRefresh.IsEnabled = true;
        }

10. Fügen Sie Ausnahmehandler in der `SyncAsync`-Methode hinzu:

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

    In diesem Beispiel werden alle Datensätze in der Remote- `todoTable` abgerufen, Sie können die Datensätze aber auch durch Übergeben einer Abfrage filtern. Der erste Parameter für `PullAsync` ist eine Abfrage-ID für eine inkrementelle Synchronisierung, bei der der `UpdatedAt`-Zeitstempel verwendet wird, um nur die Datensätze abzurufen, die seit der letzten Synchronisierung geändert wurden. Die Abfrage-ID muss eine beschreibende Zeichenfolge sein, die für jede logische Abfrage in Ihrer App eindeutig ist. Um die inkrementelle Synchronisierung zu deaktivieren, übergeben Sie `null` als Abfrage-ID. Dadurch werden in jedem Pullvorgang alle Datensätze abgerufen, was potenziell ineffizient ist.

    >[AZURE.NOTE] * Um Datensätze aus dem lokalen Speicher des Geräts zu entfernen, wenn sie in der Datenbank des mobilen Diensts gelöscht wurden, aktivieren Sie [Vorläufiges Löschen]. Andernfalls sollte Ihre App in regelmäßigen Abständen `IMobileServiceSyncTable.PurgeAsync()` aufrufen, um den lokalen Speicher zu löschen.

    Beachten Sie, dass die  `MobileServicePushFailedException` sowohl für einen Push- als auch für einen Pullvorgang auftreten kann. Sie kann für einen Pullvorgang auftreten, weil dieser intern einen Pushvorgang ausführt, um sicherzustellen, dass alle Tabellen und Beziehungen konsistent sind. Im nächsten Lernprogramm, [Behandeln von Konflikten mit der Offlineunterstützung für Mobile Services], wird gezeigt, wie Sie solche synchronisierungsbezogenen Ausnahmen behandeln.

11. Drücken Sie in Visual Studio die Taste **F5**, um die App erneut zu erstellen und auszuführen. Die App verhält sich genauso wie vor den Offlinesynchronisierungsänderungen, da sie einen Synchronisierungsvorgang bei Einfüge-, Update- und Aktualisierungsvorgängen ausführt.

## <a name="update-sync"></a>Aktualisieren des Synchronisierungsverhaltens der App 

In diesem Abschnitt ändern Sie die Anwendung so, dass sie bei Einfüge- und Updatevorgängen nicht synchronisiert wird, sondern nur beim Klicken auf die Schaltfläche **Aktualisieren**. Anschließend trennen Sie die Verbindung zwischen App und mobilem Dienst, um ein Offlineszenario zu simulieren. Wenn Sie Datenelemente hinzufügen, werden diese im lokalen Speicher gespeichert, jedoch nicht mit dem mobilen Dienst synchronisiert.

1. Öffnen Sie "App.xaml.cs" im freigegebenen Projekt. Bearbeiten Sie die Methoden `InsertTodoItem` und `UpdateCheckedTodoItem`, um die Aufrufe von `SyncAsync` auszukommentieren.

2. Bearbeiten Sie "App.xaml.cs" im freigegebenen Projekt. Kommentieren Sie die Initialisierung von **MobileServiceClient** aus, und fügen Sie die folgenden Zeilen hinzu, die eine ungültige mobile Dienst-URL verwenden:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

3. Kommentieren Sie in `InitLocalStoreAsync()` den Aufruf von `SyncAsync()` aus, sodass die App beim Start keine Synchronisierung durchführt.

4. Drücken Sie **F5**, um die Anwendung zu erstellen und auszuführen. Geben Sie neue "todoitems" ein, und klicken Sie für jedes auf **Speichern**. Die neuen Todo-Elemente existieren nur im lokalen Speicher, bis sie per Push auf den mobilen Dienst übertragen werden können. Die Client-App verhält sich so, als ob eine Verbindung zum mobilen Dienst vorhanden wäre, und unterstützt alle Erstellungs-, Lese-, Aktualisierungs- und Löschaktionen (CRUD).

5. Schließen Sie die App und starten Sie sie erneut, um zu überprüfen, ob die neuen Elemente dauerhaft im lokalen Speicher gespeichert wurden.

## <a name="update-online-app"></a>Aktualisieren der App zur erneuten Herstellung einer Verbindung mit dem mobilen Dienst

In diesem Abschnitt verbinden Sie die App erneut mit dem mobilen Dienst. Dies simuliert, dass die App von einem Offlinestatus in einen Onlinestatus mit dem mobilen Dienst wechselt. Wenn Sie auf die Schaltfläche "Aktualisieren" klicken, werden Daten mit Ihrem mobilen Dienst synchronisiert.

1. Öffnen Sie "App.xaml.cs" im freigegebenen Projekt. Heben Sie die Auskommentierung der vorherigen Initialisierung von `MobileServiceClient` auf, um wieder die richtige mobile Dienst-URL und den richtigen App-Schlüssel hinzuzufügen.

2. Drücken Sie die Taste **F5**, um die App erneut zu erstellen und auszuführen. Beachten Sie, dass die Daten genau wie im Offlineszenario aussehen, obwohl die App jetzt mit dem mobilen Dienst verbunden ist. Dies liegt daran, dass die App immer mit der `IMobileServiceSyncTable` arbeitet, die auf den lokalen Speicher verweist.

3. Melden Sie sich beim Azure-Verwaltungsportal an, und zeigen Sie die Datenbank für Ihren mobilen Dienst an. Wenn Ihr Dienst das JavaScript-Back-End für Mobile Services verwendet, können Sie die Daten auf der Registerkarte **Daten** mobilen Diensts durchsuchen. 

    Wenn Sie das .NET-Back-End für Ihren mobilen Dienst verwenden, wechseln Sie in Visual Studio zu **Server-Explorer** -> **Azure** -> **SQL-Datenbanken**. Klicken Sie mit der rechten Maustaste auf Ihre Datenbank, und wählen Sie **In SQL Server-Objekt-Explorer öffnen** aus.

    Beachten Sie, dass die Daten zwischen Datenbank und lokalem Speicher nicht synchronisiert wurden.

    ![][6]

4. Klicken Sie in der App auf die Schaltfläche **Aktualisieren**. Daraufhin ruft die App  `PushAsync` und `PullAsync` auf. Dieser Pushvorgang sendet die Elemente im lokalen Speicher an den mobilen Dienst und ruft dann neue Daten aus dem mobilen Dienst ab.

    Ein Pushvorgang wird aus dem `MobileServiceClient.SyncContext` statt aus der `IMobileServicesSyncTable` ausgeführt und überträgt per Push alle Änderungen an alle Tabellen, die dem Synchronisierungskontext zugeordnet sind. Dies ist bei Szenarios erforderlich, in denen Beziehungen zwischen Tabellen bestehen.

    ![][7]

5. Klicken Sie in der App auf das Kontrollkästchen neben einigen Elementen, um sie im lokalen Speicher abzuschließen. 

    ![][8]

6. Klicken Sie erneut auf die Schaltfläche **Aktualisieren**, um `SyncAsync` aufzurufen. `SyncAsync` ruft Push- und Pullvorgänge auf, in diesem Fall hätten wird den Aufruf von `PushAsync` auch entfernen können. Dies liegt daran, dass ein **Pull-Vorgang immer zuerst einen Push-Vorgang ausführt**. Dadurch wird sichergestellt, dass alle Tabellen im lokalen Speicher und die Beziehungen konsistent bleiben.

    ![][10] 
  

##Zusammenfassung

[AZURE.INCLUDE [mobile-services-offline-summary-csharp](../includes/mobile-services-offline-summary-csharp.md)]

## Nächste Schritte

* [Behandeln von Konflikten mit der Offlineunterstützung für Mobile Services]

* [Verwenden von vorläufigem Löschen in Mobile Services][Vorläufiges Löschen]

<!-- Anchors. -->
[Aktualisieren der App für die Unterstützung von Offlinefunktionen]: #enable-offline-app
[Aktualisieren des Synchronisierungsverhaltens der App]: #update-sync
[Aktualisieren der App zur erneuten Herstellung einer Verbindung mit dem mobilen Dienst]: #update-online-app
[Nächste Schritte]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
[1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png
[11]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-wp81-reference-sqlite-dialog.png
[12]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/new-synchandler-class.png
[13]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Behandeln von Konflikten mit der Offlineunterstützung für Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/ 
[Offlinebeispiel für TodoList]: http://go.microsoft.com/fwlink/?LinkId=394777
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service
[Erste Schritte]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-get-started/
[SQLite für Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite für Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953
[Windows Phone 8-Lernprogramm für Visual Studio 2012]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-offline-data/
[Vorläufiges Löschen]: /de-de/documentation/articles/mobile-services-using-soft-delete/


[Mobile Services SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0
[SQLite Store Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0


<!--HONumber=42-->
