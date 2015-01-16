<properties urlDisplayName="Using Offline Data" pageTitle="Verwenden von Offlinedaten in Mobile Services (Windows Store) | Mobile Dev Center" metaKeywords="" description="Erfahren Sie, wie Sie Azure Mobile Services verwenden, um Offlinedaten in Ihrer Windows Store-Anwendung zwischenzuspeichern und zu synchronisieren." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Using offline data sync in Mobile Services" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc" />

# Verwendung der Offlinedatensynchronisierung in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-offline](../includes/mobile-services-selector-offline.md)]


<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>In diesem Lernprogramm erfahren Sie, wie Sie einer universellen Windows-Store-App mit Azure Mobile Services Offlineunterstützung hinzufügen. Über Offlineunterstützung können Sie mit einer lokalen Datenbank interagieren, wenn Sie Ihre App offline nutzen. Sobald Ihre App mit der Back-End-Datenbank online ist, synchronisieren Sie die lokalen Änderungen anhand der Offlinefunktionen. 
</p>
<p>Wenn Sie lieber ein Video zu diesem Thema ansehen möchten, können Sie den Clip auf der rechten Seite auswählen. In diesem werden dieselben Schritte behandelt wie in diesem Lernprogramm.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a> <span class="time">14:36</span></div>
</div>


In diesem Lernprogramm aktualisieren Sie das universelle App-Projekt aus dem Lernprogramm [Erste Schritte mit Mobile Services], um Offlinefunktionen von Azure Mobile Services zu unterstützen. Anschließend fügen Sie Daten in einem Offlineszenario mit getrennter Verbindung hinzu, synchronisieren diese Elemente mit der Onlinedatenbank und melden sich daraufhin beim Azure-Verwaltungsportal an, um Änderungen an den Daten anzuzeigen, die während der Ausführung der Anwendung vorgenommen wurden.


>[WACOM.NOTE] In diesem Lernprogramm wird erläutert, wie Sie Azure Mobile Services verwenden können, um Daten in einer Windows Store-App zu speichern und abzurufen. Falls Sie noch keine Erfahrung mit Mobile Services haben, schließen Sie zunächst das Lernprogramm [Erste Schritte mit Mobile Services] ab.
>
>Sie benötigen ein Azure-Konto, um dieses Lernprogramm abzuschließen. Falls Sie kein Konto besitzen, können Sie sich für eine Azure-Testversion registrieren. So erhalten Sie bis zu 10 kostenlose mobile Dienste, die Sie auch nach Ablauf der Testversion weiter nutzen können. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Kostenlose Azure-Testversion</a>. 
>
>Das ältere Windows Phone 8-Lernprogramm für Visual Studio 2012 steht weiterhin unter [Windows Phone 8-Lernprogramm für Visual Studio 2012] zur Verfügung.


In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Aktualisieren der App für die Unterstützung von Offlinefunktionen]
2. [Testen der App in einem Offlineszenario] 
3. [Aktualisieren der App zur Herstellung einer Verbindung mit dem mobilen Dienst]
4. [Testen der App mit Verbindung zum mobilen Dienst]

Für dieses Lernprogramm ist Folgendes erforderlich:

* Visual Studio 2013 für Windows 8.1
* Abschluss von [Erste Schritte mit Mobile Services].
* [Azure Mobile Services SDK Version 1.3.0-beta2 (oder höher)][Mobile Services SDK Nuget]
* [Azure Mobile Services SQLite Store Version 1.0.0-beta2 (oder höher)][SQLite store nuget]
* SQLite für Windows 8.1

>[AZURE.NOTE]Sie benötigen ein Azure-Konto, um dieses Lernprogramm abzuschließen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter <a href="http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28" target="_blank">Kostenlose Azure-Testversion</a>. 

## <a name="enable-offline-app"></a>Aktualisieren der App für die Unterstützung von Offlinefunktionen

Wenn Sie Ihren mobilen Dienst offline nutzen, ermöglichen Ihnen die Offlinefunktionen von Azure Mobile Services die Interaktion mit einer lokalen Datenbank. Initialisieren Sie `MobileServiceClient.SyncContext` in einem lokalen Speicher, um diese Funktionen in der App zu verwenden. Erstellen Sie dann für die Tabelle einen Verweis über die Schnittstelle `IMobileServiceSyncTable`. In diesem Lernprogramm verwenden wir SQLite als lokalen Speicher.

>[AZURE.NOTE]Sie können diesen Abschnitt überspringen und einfach das Beispielprojekt abrufen, das bereits über Offlineunterstützung vom GitHub-Beispielrepository für Mobile Services verfügt. Das Beispielprojekt mit aktivierter Offlineunterstützung befindet sich unter [TodoList Offline Sample].


1. Installieren der SQLite-Laufzeit für Windows 8.1 und Windows Phone 8.1. 

    * **Windows 8.1-Laufzeit:** Installieren Sie die SQLite-Laufzeit für Windows 8.1 über den Link für [SQLite für Windows 8.1].
    * **Windows Phone 8,1:** Installieren Sie die SQLite-Laufzeit für Windows Phone 8.1 über den Link für [SQLite für Windows Phone 8.1].

    >[AZURE.NOTE]Wenn Sie Internet Explorer verwenden und auf den Link für das Installieren von SQLite klicken, werden Sie möglicherweise aufgefordert, die VSIX-Datei als Zip-Datei herunterzuladen. Speichern Sie die Datei mit der Endung .vsix statt.zip an einem Speicherort auf Ihrer Festplatte. Doppelklicken Sie im Windows-Explorer auf die VSIX-Datei, um die Installation auszuführen.

2. Öffnen Sie in Visual Studio das Projekt, das Sie im Lernprogramm [Erste Schritte mit Mobile Services] abgeschlossen haben. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf **Verweise** für die Windows 8.1-Laufzeit und Windows Phone 8.1-Plattformprojekte, und fügen Sie SQLite im Abschnitt **Erweiterungen** einen Verweis hinzu. 

    ![][1]
    </br>**Windows 8.1-Laufzeit**

    ![][11]
    </br>**Windows Phone 8,1**

3. Für die SQLite-Laufzeitumgebung muss die Prozessorarchitektur des Projekts, das erstellt wird, in **x86**, **x64** oder **ARM** geändert werden. **Beliebige CPU**  wird nicht unterstützt. Klicken Sie im Projektmappen-Explorer für Visual Studio auf die Projektmappe am Anfang, und ändern Sie das Dropdownfeld "Prozessorarchitektur" auf eine der unterstützten Einstellungen, die Sie testen möchten.

    ![][13]

4. Installieren Sie das  **WindowsAzure.MobileServices.SQLiteStore** NuGet-Paket für die Windows 8.1-Laufzeitumgebung und Windows Phone 8.1-Projekte.

    * **Windows 8.1:**Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Windows 8.1-Projekt und dann auf **NuGet-Pakete verwalten**, um NuGet-Paket-Manager auszuführen. Suchen Sie nach **SQLiteStore** , um das WindowsAzure.MobileServices.SQLiteStore-Paket zu installieren.
    * **Windows Phone 8,1:** Klicken Sie mit der rechten Maustaste auf das Windows Phone 8.1-Projekt und auf **NuGet-Pakete verwalten** , um NuGet-Paket-Manager auszuführen. Suchen Sie nach **SQLiteStore** , um das WindowsAzure.MobileServices.SQLiteStore-Paket zu installieren.     

    >[WACOM.NOTE] Wenn durch die Installation ein Verweis auf eine ältere Version von SQLite erstellt wird, können Sie diesen doppelten Verweis einfach löschen. 

    ![][2]

5. Öffnen Sie im Projektmappen-Explorer für Visual Studio im freigegebenen Projekt die Datei "MainPage.cs". Fügen Sie die folgenden Anweisungen am Anfang der Datei ein.

        using Microsoft.WindowsAzure.MobileServices.SQLiteStore;
        using Microsoft.WindowsAzure.MobileServices.Sync;

6. Ersetzen Sie in der freigegebenen Datei "Mainpage.cs" die Deklaration `todoTable` durch eine Deklaration vom Typ `IMobileServicesSyncTable`, die durch Aufruf von `MobileServicesClient.GetSyncTable()` initialisiert wird.

        //private IMobileServiceTable<TodoItem> todoTable = App.MobileService.GetTable<TodoItem>();
        private IMobileServiceSyncTable<TodoItem> todoTable = App.MobileService.GetSyncTable<TodoItem>();


7. Aktualisieren Sie in der freigegebenen Datei "MainPage.cs" den Ereignishandler `OnNavigatedTo`, sodass er den Clientsynchronisierungskontext mit einem SQLite-Store initialisiert. Der SQLite-Store wird mit einer Tabelle erstellt, die dem Schema der Tabelle für den mobilen Dienst entspricht. Er muss die **Version**-Systemeigenschaft enthalten, die Sie im nächsten Schritt hinzufügen werden.

        protected async override void OnNavigatedTo(NavigationEventArgs e)
        {
            if (!App.MobileService.SyncContext.IsInitialized)
            {
                var store = new MobileServiceSQLiteStore("localsync.db");
                store.DefineTable<TodoItem>();
                await App.MobileService.SyncContext.InitializeAsync(store);
            }
            await RefreshTodoItems();
        }


8. Erweitern Sie im Projektmappen-Explorer für Visual Studio im freigegebenen Projekt **DataModel**, und öffnen Sie TodoItem.cs. Fügen Sie eine `using`-Anweisung für den MobileServices-Namespace hinzu. Dies ist erforderlich, um das Versionsattribut für die Version-Systemeigenschaft aufzulösen.

        using Microsoft.WindowsAzure.MobileServices;

      Aktualisieren Sie dann die `TodoItem`-Klasse, sodass die Klasse die **Version**-Systemeigenschaft folgendermaßen enthält. Das Tabellenschema muss die **Version**-Systemeigenschaft wie hier gezeigt enthalten, damit die Offlinefunktionen unterstützt werden.

        public class TodoItem
        {
          public string Id { get; set; }
          [JsonProperty(PropertyName = "text")]
          public string Text { get; set; }
          [JsonProperty(PropertyName = "complete")]
          public bool Complete { get; set; }
          [Version]
          public string Version { get; set; }
        }




9. Aktualisieren Sie als nächstes die Benutzeroberfläche für die Windows 8.1- und Windows Phone 8.1-Projekte, sodass Schaltflächen für die Offlinesynchronisierungsvorgänge zwischen der lokalen Offlinedatenbank und der Mobile Services-Datenbank in Azure aufgenommen werden. 

    * **Windows 8.1:** Öffnen Sie im Projektmappen-Explorer für Visual Studio unter dem Windows 8.1-Projekt die Datei "MainPage.xaml". Suchen Sie die Schaltfläche **ButtonRefresh**. Ersetzen Sie dieses Schaltflächenelement mit dem folgenden StackPanel-Element aus Schaltflächen. 

            <StackPanel Orientation="Horizontal">
                <Button Margin="72,0,0,0" Height="44" Name="ButtonRefresh" Click="ButtonRefresh_Click">
                	Refresh
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPull" Click="ButtonPull_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Sync"/>
                    </StackPanel>
                </Button>
                <Button Margin="10,0,0,0" Name="ButtonPush" Click="ButtonPush_Click">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
            </StackPanel>

    * **Windows Phone 8,1:** Öffnen Sie im Projektmappen-Explorer für Visual Studio unter dem Windows Phone 8.1-Projekt die Datei "MainPage.xaml". Suchen Sie die Schaltfläche **ButtonRefresh**. Ersetzen Sie dieses Schaltflächenelement mit dem folgenden StackPanel-Element aus Schaltflächen. 

            <StackPanel Orientation="Horizontal" Grid.Row="3" Grid.ColumnSpan="2">
                <Button Name="ButtonRefresh" Click="ButtonRefresh_Click" Margin="10,0,0,0">
                    Refresh
                </Button>
                <Button Name="ButtonPull" Click="ButtonPull_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Pull</TextBlock>
                        <SymbolIcon Symbol="Sync"/>
                    </StackPanel>
                </Button>
                <Button Name="ButtonPush" Click="ButtonPush_Click" Margin="10,0,0,0">
                    <StackPanel Orientation="Horizontal">
                        <TextBlock Margin="5">Push</TextBlock>
                        <SymbolIcon Symbol="Upload"/>
                    </StackPanel>
                </Button>
        </StackPanel>
        


10. Fügen Sie in der freigegebenen Datei "MainPage.cs" Klick-Ereignishandler für die Schaltflächen **Push** und **Pull** hinzu. Speichern Sie anschließend die Datei. 
        
    Beachten Sie, dass die `MobileServicePushFailedException` für Push- und Pull-Vorgänge auftreten kann. Sie kann bei Pull-Vorgängen auftreten, weil dieser intern einen Push-Vorgang durchführt, um sicherzustellen, dass alle Tabellen und Beziehungen synchronisiert sind. Im nächsten Lernprogramm, [Behandeln von Konflikten mit Offlineunterstützung für Mobile Services], wird gezeigt, wie Sie mit diesen synchronisationsbezogenen Ausnahmen umgehen.

    Um die Synchronisierung der gelöschten Datensätze mit der Offlinedatensynchronisierung zu unterstützen, sollten Sie [Vorläufiges Löschen](/de-de/documentation/articles/mobile-services-using-soft-delete/) aktivieren. Andernfalls müssen Sie manuell Einträge aus dem lokalen Speicher entfernen oder`IMobileServiceSyncTable::PurgeAsync()` aufrufen, um den lokalen Speicher zu löschen.

    In diesem Beispiel übergeben wir zur Unterstützung der inkrementellen Synchronisierung eine Abfrage an den `PullAsync`-Methodenaufruf. Dies ist in Fällen hilfreich, in denen Sie nicht die gesamte Tabelle während der Synchronisierung per Pull-Vorgang übertragen möchten. In diesem Szenario machen wir uns keine Gedanken um Textänderungen an den todoitems, wir möchten lediglich abgeschlossene Elemente per Pull-Vorgang übertragen, um sie aus der Aufgabenliste zu entfernen.

        private async void ButtonPull_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Pull is in progress
            // and visibly show the action is in progress. 
            ButtonPull.Focus(FocusState.Programmatic);
            ButtonPull.IsEnabled = false;

            try
            {
                // In this example scenario we are demonstrating incremental sync. We only want to 
                // sync the uncomplete todoitems. If another client updated the text or any other 
                // field after completing the item, it doesn't concern us. So we pass a query to the 
                // PullAsync() method to pull uncomplete todoitems instead of the entire table.
                await todoTable.PullAsync(todoTable.Where(todoItem => todoItem.Complete == false));

                await RefreshTodoItems();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Internal Push operation during pull request failed because of sync errors: " +
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

            ButtonPull.IsEnabled = true; 
        }


        private async void ButtonPush_Click(object sender, Windows.UI.Xaml.RoutedEventArgs e)
        {
            string errorString = null;

            // Prevent extra clicking while Push is in progress
            // and visibly show the action is in progress. 
            ButtonPush.Focus(FocusState.Programmatic);
            ButtonPush.IsEnabled = false;

            try
            {
                await App.MobileService.SyncContext.PushAsync();
            }
            catch (MobileServicePushFailedException ex)
            {
                errorString = "Push failed because of sync errors: " + 
                  ex.PushResult.Errors.Count + " errors, message: " + ex.Message;
            }
            catch (Exception ex)
            {
                errorString = "Push failed: " + ex.Message +
                  "\n\nIf you are still in an offline scenario, " +
                  "you can try your Push again when connected with your Mobile Serice.";
            }

            if (errorString != null)
            {
                MessageDialog d = new MessageDialog(errorString);
                await d.ShowAsync();
            }

            ButtonPush.IsEnabled = true;
        }

11. Erstellen Sie die Projektmappe, und überprüfen Sie, ob in einem der Projekte Buildfehler aufgetreten sind.


## <a name="test-offline-app"></a>Testen der App in einem Offlineszenario

In diesem Abschnitt trennen Sie die Verbindung zwischen App und mobilem Dienst, um ein Offlineszenario darzustellen. Dann fügen Sie einige Datenelemente hinzu, die im lokalen Speicher aufbewahrt werden.

Beachten Sie, dass in diesem Abschnitt keinerlei Verbindung zwischen App und mobilem Dienst bestehen sollte. Daher lösen die Schaltflächen **Push** und **Pull** Ausnahmefehler aus, wenn sie getestet werden.. Im nächsten Abschnitt verbinden Sie diese Client-App erneut mit dem mobilen Dienst, um die **Push**- und **Pull**-Vorgänge zu testen und den Speicher mit der Datenbank des mobilen Dienstes zu synchronisieren.


1. Öffnen Sie im Projektmappen-Explorer von Visual Studio "App.xaml.cs" im freigegebenen Projekt. Ändern Sie die Initialisierung von **MobileServiceClient** in eine ungültige Adresse, indem Sie in der URL "**azure-mobile.net**" durch"**azure-mobile.xxx**" ersetzen. Speichern Sie anschließend die Datei.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.xxx/",
            "AppKey"
        );

2. Drücken Sie in Visual Studio die Taste **F5**, um die App zu erstellen und auszuführen. Geben Sie neue todoitems ein und klicken Sie jeweils auf **Speichern**. Die neuen Todo-Elemente existieren nur im lokalen Speicher, bis sie per Push auf den mobilen Dienst übertragen werden können. Die Client-App verhält sich so, als ob eine Verbindung zum mobilen Dienst vorhanden wäre, und unterstützt alle Erstellungs-, Lese-, Aktualisierungs- und Löschaktionen (CRUD).

    ![][4]

3. Schließen Sie die App und starten Sie sie erneut, um zu überprüfen, ob die neuen Elemente dauerhaft im lokalen Speicher gespeichert wurden.

## <a name="update-online-app"></a>Aktualisieren der App zur Herstellung einer Verbindung mit dem mobilen Dienst

In diesem Abschnitt verbinden Sie die App erneut mit dem mobilen Dienst. Dies simuliert, dass die App von einem Offlinestatus in einen Onlinestatus mit dem mobilen Dienst wechselt.


1. Öffnen Sie im Projektmappen-Explorer von Visual Studio "App.xaml.cs" im freigegebenen Projekt. Ändern Sie die Initialisierung von **MobileServiceClient** zurück zur korrekten Adresse, indem Sie in der URL "**azure-mobile.xxx**" durch "**azure-mobile.net**" ersetzen. Speichern Sie anschließend die Datei.

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );


## <a name="test-online-app"></a>Testen der App mit Verbindung zum mobilen Dienst


In diesem Abschnitt testen Sie die Push- und Pull-Vorgänge zur Synchronisierung des lokalen Speichers mit der Datenbank des mobilen Dienstes. Sie können den Windows Store 8.1-Client oder den Windows Phone 8.1-Client testen. Die Momentaufnahmen unten zeigen den Windows Store 8.1-Client. 

1. Drücken Sie in Visual Studio die Taste **F5**, um die App neu zu erstellen und auszuführen. Beachten Sie, dass die Daten genau wie im Offlineszenario aussehen, obwohl die App jetzt mit dem mobilen Dienst verbunden ist. Dies liegt daran, dass die App immer mit der `IMobileServiceSyncTable` arbeitet, die sich auf den lokalen Speicher bezieht.

    ![][4]

2. Melden Sie sich beim Azure-Verwaltungsportal an, und zeigen Sie die Datenbank für Ihren mobilen Dienst an. Wenn Ihr Dienst das JavaScript-Back-End für mobile Dienste verwendet, können Sie die Daten in der Registerkarte **Daten** des mobilen Dienstes durchsuchen. Wenn Sie das .NET-Back-End für Ihren mobilen Dienst verwenden, können Sie in der SQL-Azure-Erweiterung auf die Schaltfläche **Verwalten** für Ihre Datenbank klicken, um eine Abfrage der Tabelle auszuführen.

    Beachten Sie, dass die Daten zwischen Datenbank und lokalem Speicher nicht synchronisiert wurden.

    ![][6]

3. Klicken Sie in der App auf die Schaltfläche  **Push**. Daraufhin ruft die App `MobileServiceClient.SyncContext.PushAsync` auf. Dieser Push-Vorgang führt dazu, dass die Datenbank des mobilen Dienstes die Daten aus dem Speicher erhält. Der lokale Speicher erhält jedoch nicht die Elemente aus der Datenbank des mobilen Diensts.

    Ein Push-Vorgang wird aus `MobileServiceClient.SyncContext` statt `IMobileServicesSyncTable` ausgeführt und überträgt per Push alle Änderungen in alle Tabellen, die mit dem Synchronisierungskontext verknüpft sind. Dies ist für Szenarien erforderlich, in denen Beziehungen zwischen Tabellen bestehen.

    ![][7]

4. Aktivieren Sie in der App die Kontrollkästchen für einige Elemente, um sie im lokalen Speicher abzuschließen. 

    ![][8]

5. Klicken Sie dieses Mal in der App auf die Schaltfläche **Pull**. Die App ruft `IMobileServiceSyncTable.PullAsync()` und `RefreshTodoItems` auf.  Beachten Sie, dass die Daten aus der Datenbank des mobilen Dienstes per Pull-Vorgang in den lokalen Speicher übertragen wurden und in der App angezeigt werden. Beachten Sie jedoch auch, dass alle Daten im lokalen Speicher ebenfalls per Push-Vorgang in die Datenbank des mobilen Dienstes übertragen wurden. Dies liegt daran, dass ein **Pull-Vorgang immer zuerst einen Push-Vorgang ausführt**. Dadurch wird sichergestellt, dass alle Tabellen im lokalen Speicher zusammen mit Beziehungen synchron bleiben.
 
    ![][9]

    ![][10] 
  

##Zusammenfassung

Um die Offlinefunktionen mobiler Dienste zu unterstützen, haben Sie die Schnittstelle `IMobileServiceSyncTable` verwendet und `MobileServiceClient.SyncContext` mit einem lokalen Speicher initialisiert. In diesem Fall war der lokale Speicher eine SQLite-Datenbank.

Die normalen CRUD-Operationen für mobile Dienste funktionieren so, als ob die App immer noch verbunden wäre. Alle Operationen erfolgen jedoch nur im lokalen Speicher.

Zur Synchronisierung des lokalen Speichers mit dem Server haben Sie die Methoden `IMobileServiceSyncTable.PullAsync` und `MobileServiceClient.SyncContext.PushAsync` verwendet.

*  Dann haben Sie `IMobileServiceSyncContext.PushAsync()` aufgerufen, um die Änderungen per Push-Vorgang auf den Server zu übertragen. Diese Methode ist ein Element von `IMobileServicesSyncContext` statt der Synchronisierungstabelle, da Änderungen per Push-Vorgang auf alle Tabellen übertragen werden:

    Nur Datensätze, die lokal geändert wurden (mit CRUD-Operationen), werden an den Server gesendet.
   
* Um Daten per Pull-Vorgang von einer Tabelle auf dem Server in die App zu übertragen, haben Sie `IMobileServiceSyncTable.PullAsync` aufgerufen.

    Ein Pull-Vorgang führt immer zuerst einen Push-Vorgang aus. Dadurch wird sichergestellt, dass alle Tabellen im lokalen Speicher zusammen mit Beziehungen synchron bleiben.

    Es gibt außerdem Überladungen von `PullAsync()`, mit denen eine Abfrage zur Unterstützung der inkrementellen Synchronisierung spezifiziert werden kann. Wenn eine Abfrage nicht übergeben wird, überträgt `PullAsync()` alle Zeilen in der entsprechenden Tabelle (oder Abfrage) per Pull-Vorgang. Sie können die Abfrage übergeben, um nur nach den Änderungen zu filtern, mit denen Ihre App synchronisiert werden muss.


## Nächste Schritte

* [Behandeln von Konflikten mit Offlineunterstützung für Mobile Services]

<!-- Anchors. -->
[Aktualisieren der App für die Unterstützung von Offlinefunktionen]: #enable-offline-app
[Testen der App in einem Offlineszenario]: #test-offline-app
[Aktualisieren der App zur Herstellung einer Verbindung mit dem mobilen Dienst]: #update-online-app
[Testen der App mit Verbindung zum mobilen Dienst]: #test-online-app
[Nächste Schritte]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-get-started-data-vs2013/mobile-todoitem-data-browse.png
[1]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-reference-sqlite-dialog.png
[2]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitestore-nuget.png
[3]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-sqlitepcl-nuget.png
[4]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-offline-app-run1.png
[5]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run1.png
[6]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse.png
[7]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse2.png
[8]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run2.png
[9]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-online-app-run3.png
[10]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-data-browse3.png
[11]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/mobile-services-add-wp81-reference-sqlite-dialog.png
[12]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/new-synchandler-class.png
[13]: ./media/mobile-services-windows-store-dotnet-get-started-offline-data/cpu-architecture.png


<!-- URLs. -->
[Behandeln von Konflikten mit Offlineunterstützung für Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/ 
[TodoList-Offlinebeispiel]: http://go.microsoft.com/fwlink/?LinkId=394777
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started/#create-new-service
[Erste Schritte]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-get-started/
[SQLite für Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[SQLite für Windows Phone 8.1]: http://go.microsoft.com/fwlink/?LinkId=397953
[Windows Phone 8-Lernprogramm für Visual Studio 2012]: /de-de/documentation/articles/mobile-services-windows-phone-get-started-offline-data/


[Mobile Services SDK Nuget]: http://www.nuget.org/packages/WindowsAzure.MobileServices/1.3.0-beta2
[SQLite Store NuGet]: http://www.nuget.org/packages/WindowsAzure.MobileServices.SQLiteStore/1.0.0-beta2
