<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-wp8" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Phone app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="09/25/2014" ms.author="wesmc" />

# Erste Schritte mit Daten in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

In diesem Thema wird das Verwenden von Azure Mobile Services als Back-End-Datenquelle für eine Windows Phone (Silverlight)-App demonstriert. In diesem Lernprogramm laden Sie ein Visual Studio-Projekt für eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in die App und zeigen die Datenänderungen beim Ausführen der App an.

Der mobile Dienst, den Sie in diesem Lernprogramm erstellen, unterstützt .NET Runtime in Mobile Services. Auf diese Weise können Sie .NET-Sprachen und Visual Studio für die serverseitige Geschäftslogik im mobilen Dienst verwenden. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der [JavaScript-Backend-Version][JavaScript-Backend-Version] dieses Themas.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Herunterladen des Windows Phone 8-App-Projekts][Herunterladen des Windows Phone 8-App-Projekts]
2.  [Erstellen eines neuen mobilen Dienstes][Erstellen eines neuen mobilen Dienstes]
3.  [Lokales Herunterladen des mobilen Diensts][Lokales Herunterladen des mobilen Diensts]
4.  [Aktualisieren der Windows Phone-App zum Verwenden des mobilen Diensts][Aktualisieren der Windows Phone-App zum Verwenden des mobilen Diensts]
5.  [Testen der Windows Phone-App mit dem lokal gehosteten Dienst][Testen der Windows Phone-App mit dem lokal gehosteten Dienst]
6.  [Veröffentlichen des mobilen Diensts in Azure][Veröffentlichen des mobilen Diensts in Azure]
7.  [Testen der Windows Phone-App mit dem in Azure gehosteten Dienst][Testen der Windows Phone-App mit dem in Azure gehosteten Dienst]

Für dieses Lernprogramm ist Folgendes erforderlich:

-   Visual Studio 2013 und [Windows Phone 8 SDK][Windows Phone 8 SDK] unter Windows 8. Wenn Sie dieses Lernprogramm ausführen, um eine Windows Phone 8.1-App zu erstellen, müssen Sie Visual Studio 2013 Update 2 oder eine höhere Version verwenden.
-   Ein Microsoft Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].
    </p>
    </div>

## <a name="download-app"></a>Download des GetStartedWithData-Projekts

Dieses Lernprogramm baut auf der [GetStartedWithMobileServices-App][GetStartedWithMobileServices-App] auf, bei der es sich um ein Windows Phone Silverlight 8-App-Projekt handelt. Die Benutzeroberfläche dieser App und die vom Mobile Services-Schnellstart generierte Benutzeroberfläche sind vergleichbar, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1.  Laden Sie die C#-Version der GetStartedWithMobileServices-Beispiel-App von der [Website mit den Codebeispielen für Entwickler][GetStartedWithMobileServices-App] herunter.

    ![][0]

    > [WACOM.NOTE]Zum Erstellen einer Windows Phone Silverlght 8.1-App ändern Sie einfach das Zielbetriebssystem im heruntergeladenen Windows Phone Silverlight 8-App-Projekt in Windows Phone 8.1. Zum Erstellen einer Windows Phone Store-App laden Sie die [Windows Phone Store-App-Version][Windows Phone Store-App-Version] des App-Beispielprojekts GetStartedWithData herunter.

2.  Führen Sie Visual Studio mit Administratorberechtigung aus, indem Sie mit der rechten Maustaste auf Visual Studio klicken und dann **Als Administrator ausführen** auswählen.

3.  Öffnen Sie in Visual Studio das heruntergeladene Projekt, und sehen Sie sich die Datei "MainPage.xaml.cs" an.

    Beachten Sie, dass die hinzugefügten **TodoItem**-Objekte in einem speicherinternen **ObservableCollection\<TodoItem\>**-Element gespeichert werden.

4.  Wählen Sie in Visual Studio ein Bereitstellungsziel für die App aus. Sie können die Bereitstellung auf einem Windows Phone-Gerät oder auf einem der Emulatoren durchführen, die im Windows Phone SDK enthalten sind. In diesem Lernprogramm wird die Bereitstellung auf einem Emulator demonstriert.

    ![][1]

5.  Drücken Sie die **F5**-Taste. Dadurch wird die App zum Debuggen erstellt, bereitgestellt und gestartet.

6.  Geben Sie in der App Text in das Textfeld ein. Klicken Sie dann auf **Save**, um einige Elemente im Arbeitsspeicher der App zu speichern.

    ![][2]

    Beachten Sie, dass der Text für jedes `TodoItem` unterhalb der Aktualisierungsschaltfläche zusammen mit einem Kontrollkästchen angezeigt wird, das es Ihnen ermöglicht, das Element als abgeschlossen zu markieren.

## <a name="create-service"></a><span class="short-header">Erstellen eines neuen mobilen Diensts</span>Erstellen eines neuen mobilen Diensts

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service-locally"></a><span class="short-header">Lokales Herunterladen des Diensts</span>Herunterladen des mobilen Dienstprojekts und Hinzufügen zur Lösung

1.  Klicken Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] auf den neuen mobilen Dienst oder auf die Registerkarte mit dem Cloudsymbol, um zur Übersichtsseite zu gelangen.

    ![][3]

2.  Klicken Sie auf die Plattform **Windows Phone 8**. Erweitern Sie im Abschnitt **Get Started** die Option **Connect an existing Windows Phone 8 app**, und klicken Sie auf die Schaltfläche **Download**, um ein personalisiertes Startprojekt für den mobilen Dienst herunterzuladen.

    ![][4]

3.  Klicken Sie in diesem Abschnitt auch auf den Link, der im Screenshot unten gezeigt wird, um eine Veröffentlichungsprofildatei für den gerade heruntergeladenen mobilen Dienst herunterzuladen.

    > [WACOM.NOTE] Speichern Sie die Datei an einem sicheren Ort, da sie vertrauliche Daten zu Ihrem Azure-Konto enthält. Sie löschen diese Datei später in diesem Lernprogramm, nachdem Sie den mobilen Dienst veröffentlicht haben.

    ![][5]

4.  Entpacken Sie das personalisierte Dienststartprojekt, das Sie heruntergeladen haben. Kopieren Sie die Ordner aus der ZIP-Datei in das gleiche **C#**-Verzeichnis, in dem sich die Lösungsdatei "Erste Schritte mit Daten" (.sln) befindet. Dies erleichtert es dem NuGet-Paket-Manager, alle Pakete zu synchronisieren.

    ![][6]

5.  Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf Ihre Lösung für die Windows Store-App "Erste Schritte mit Daten". Klicken Sie auf **Hinzufügen** und anschließend auf **Vorhandenes Projekt**.

    ![][7]

6.  Navigieren Sie im Dialogfeld "Vorhandenes Projekt hinzufügen" zum Projektordner mit dem mobilen Dienst, den Sie in das **C#**-Verzeichnis verschoben haben. Wählen Sie die C#-Projektdatei (.csproj) im Dienstunterverzeichnis aus. Klicken Sie auf **Öffnen**, um das Projekt zu Ihrer Lösung hinzuzufügen.

    ![][8]

7.  Klicken Sie im Projektmappen-Explorer in Visual Studio mit der rechten Maustaste auf das soeben hinzugefügte Dienstprojekt und anschließend auf **Build**, um sicherzustellen, dass es fehlerfrei erstellt wird. Während des Erstellens muss der NuGet-Paket-Manager möglicherweise einige NuGet-Pakete wiederherstellen, auf die im Projekt verwiesen wird.

    ![][9]

8.  Klicken Sie erneut mit der rechten Maustaste auf das Dienstprojekt. Klicken Sie diesmal im Kontextmenü **Debuggen** auf **Neue Instanz starten**.

    ![][10]

    Visual Studio öffnet die Standardwebseite für Ihren Dienst. Sie können auf **Jetzt testen** klicken, um die Methoden in Ihrem mobilen Dienst von der Standardwebseite aus zu testen.

    ![][11]

    Visual Studio hostet Ihren mobilen Dienst standardmäßig lokal in IIS Express. Klicken Sie zum Anzeigen mit der rechten Maustaste in der Taskleiste auf das IIS Express-Symbol.

    ![][12]

## <a name="update-app"></a>Aktualisieren der Windows Phone-App zum Verwenden des mobilen Diensts

In diesem Abschnitt aktualisieren Sie die Windows Phone-App für die Verwendung des mobilen Dienstes als Back-End-Dienst für die Anwendung.

1.  Klicken Sie im Projektmappen-Explorer in Visual Studio mit der rechten Maustaste auf das Windows Phone-App-Projekt. Klicken Sie dann auf **NuGet-Pakete verwalten**.

    ![][13]

2.  Suchen Sie im Dialogfeld "NuGet-Pakete verwalten" nach **WindowsAzure.MobileServices** in der Sammlung der Onlinepakete, und klicken Sie, um das Azure Mobile Services Nuget-Paket zu installieren. Schließen Sie dann das Dialogfeld.

    ![][14]

3.  Suchen Sie im Azure-Verwaltungsportal den Schritt **Connect your app and store data in your service**. Kopieren Sie den Codeausschnitt, der die `MobileServiceClient`-Verbindung erstellt.

    ![][15]

4.  Öffnen Sie "App.xaml.cs" in Visual Studio. Fügen Sie den Codeausschnitt am Anfang der `App`-Klassendefinition ein. Fügen Sie ebenfalls die folgende `using`-Anweisung am Anfang der Datei hinzu, und speichern Sie die Datei.

        using Microsoft.WindowsAzure.MobileServices;

    ![][16]

5.  Öffnen Sie "MainPage.xaml.cs" in Visual Studio. Fügen Sie die using-Anweisung oben in der Datei an:

        using Microsoft.WindowsAzure.MobileServices;

6.  Ersetzen Sie in "MainPage.xaml.cs" in Visual Studio die `MainPage`-Klassendefinition mit der folgenden Definition, und speichern Sie die Datei.

    Dieser Code verwendet das Mobile Services SDK, um der App das Speichern der Daten in einer Tabelle zu ermöglichen, die vom Dienst bereitgestellt wird, anstatt lokal im Arbeitsspeicher gespeichert zu werden. Die drei Hauptmethoden sind `InsertTodoItem`, `RefreshTodoItems` und `UpdateCheckedTodoItem`. Diese drei Methoden ermöglichen Ihnen das asynchrone Einfügen, Abfragen und Aktualisieren Ihrer Datensammlung in einer Tabelle in Azure.

        public sealed partial class MainPage : PhoneApplicationPage
        {
            private MobileServiceCollection<TodoItem, TodoItem> items;
            private IMobileServiceTable<TodoItem> todoTable = 
                App.MobileService.GetTable<TodoItem>();            
            public MainPage()
            {
                this.InitializeComponent();
            }
            private async void InsertTodoItem(TodoItem todoItem)
            {
                await todoTable.InsertAsync(todoItem); 
                items.Add(todoItem);
            }
            private async void RefreshTodoItems()
            {
                items = await todoTable 
                    .ToCollectionAsync(); 
                ListItems.ItemsSource = items;
            }
            private async void UpdateCheckedTodoItem(TodoItem item)
            {
                await todoTable.UpdateAsync(item);      
            }
            private void ButtonRefresh_Click(object sender, RoutedEventArgs e)
            {
                RefreshTodoItems();
            }
            private void ButtonSave_Click(object sender, RoutedEventArgs e)
            {
                var todoItem = new TodoItem { Text = InputText.Text };
                InsertTodoItem(todoItem);
            }
            private void CheckBoxComplete_Checked(object sender, RoutedEventArgs e)
            {
                CheckBox cb = (CheckBox)sender;
                TodoItem item = cb.DataContext as TodoItem;
                item.Complete = (bool)cb.IsChecked;
                UpdateCheckedTodoItem(item);
            }
            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
                RefreshTodoItems();
            }
        }

## <a name="test-locally-hosted"></a>Testen der Windows Phone-App mit dem lokal gehosteten Dienst

</h2>
In diesem Abschnitt verwenden Sie Visual Studio zum lokalen Testen der App und des mobilen Dienstes auf der Entwicklungsarbeitsstation. Um den mobilen Dienst, der lokal in IIS Express gehostet wird, von einem Windows Phone-Gerät oder Windows Phone-Emulator aus zu testen, müssen Sie IIS Express und die Arbeitsstation so konfigurieren, dass Verbindungen zur IP-Adresse und zum Port der Arbeitsstation zulässig sind. Windows Phone-Geräte und -Emulatoren stellen Verbindungen als nicht lokale Netzwerkclients her.

#### Konfigurieren von IIS Express zum Zulassen von Remoteverbindungen

[WACOM.INCLUDE [mobile-services-how-to-configure-iis-express](../includes/mobile-services-how-to-configure-iis-express.md)]

#### Testen der App mit dem mobilen Dienst in IIS Express

1.  Öffnen Sie die Datei "App.xaml.cs" in Visual Studio, und kommentieren Sie die `MobileService`-Definition aus, die Sie kürzlich in die Datei eingefügt haben. Fügen Sie eine neue Definition hinzu, um die Verbindung basierend auf der IP-Adresse und dem Port herzustellen, die Sie auf der Arbeitsstation konfiguriert haben. Speichern Sie anschließend die Datei. Ihr Code sollte ähnlich dem folgenden aussehen.

        public static MobileServiceClient MobileService = new MobileServiceClient(
            "http://192.168.111.11:54321");

        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "https://todolist.azure-mobile.net/",
        //    "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
        //);        

2.  Drücken Sie in Visual Studio die F7-Taste oder klicken Sie auf **Projektmappe erstellen** im Menü "Build", um die Windows Phone-App und den mobilen Dienst zu erstellen. Vergewissern Sie sich im Ausgabefenster von Visual Studio, dass beide Projekte ohne Fehler erstellt werden.

    ![][17]

3.  Drücken Sie in Visual Studio die Taste F5, oder klicken Sie im Debuggen-Menü auf **Debugging starten**, um die App auszuführen und den mobilen Dienst lokal in IIS Express zu hosten.

    > [WACOM.NOTE] Stellen Sie sicher, dass Sie Visual Studio mit der Option **Als Administrator ausführen** ausgeführt haben. Andernfalls lädt IIS Express Ihre Änderungen an "applicationhost.config" möglicherweise nicht.

    ![][18]

4.  Geben Sie einen neuen Text für "todoitem" ein. Klicken Sie anschließend auf **Save**. Daraufhin wird ein neues todoItem in die Datenbank eingefügt, die vom mobilen Dienst, der lokal in IIS Express gehostet wird, erstellt wurde. Klicken Sie auf das Kontrollkästchen für eines der Elemente, um es als abgeschlossen zu markieren.

    ![][19]

5.  Halten Sie in Visual Studio das Debuggen der App an. Sie können die Änderungen in der Datenbank, die für den Back-End-Dienst erstellt wurde, anzeigen, indem Sie den Server-Explorer öffnen und die Datenverbindungen erweitern. Klicken Sie in der TodoItems-Tabelle mit der rechten Maustaste unter **MS\_TableConnectionString**, und klicken Sie dann auf **Show Table Data**.

    ![][20]

6.  Sobald Sie Ihre Tests mit dem lokal gehosteten mobilen Dienst abgeschlossen haben, löschen Sie die Windows Firewall-Regel, die Sie zum Öffnen des Ports auf der Arbeitsstation erstellt haben.

## <a name="publish-mobile-service"></a>Veröffentlichen des mobilen Diensts in Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="test-azure-hosted"></a>Testen des mobilen in Azure veröffentlichten Diensts

1.  Öffnen Sie "App.xaml.cs" in Visual Studio. Kommentieren Sie den Code aus, der den `MobileServiceClient` erstellt, der mit dem lokal gehosteten mobilen Dienst verbunden wird. Heben Sie die Auskommentierung für den Code auf, der den `MobileServiceClient` erstellt, der mit dem Dienst in Azure verbunden wird. Speichern Sie die Änderungen an der Datei.

        sealed partial class App : Application
        {
            //public static MobileServiceClient MobileService = new MobileServiceClient(
            //          "http://192.168.111.11:54321");

            // Use this constructor instead after publishing to the cloud
            public static MobileServiceClient MobileService = new MobileServiceClient(
                 "https://todolist.azure-mobile.net/",
                 "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
            );        
            ....

2.  Drücken Sie in Visual Studio die Taste F5, oder klicken Sie im Debuggen-Menü auf **Debugging starten**. Dadurch wird die App mit der vorherigen Änderung neu erstellt, bevor die App ausgeführt wird, um eine Verbindung zum remote in Azure gehosteten mobilen Dienst herzustellen.

    ![][18]

3.  Geben Sie neue "todoitems" ein, und klicken Sie jeweils auf **Speichern**. Klicken Sie auf die Kontrollkästchen für einige der neuen Elemente, um sie abzuschließen. Jedes neue todoItem wird in der SQL-Datenbank, die Sie zuvor für Ihren mobilen Dienst im Azure-Verwaltungsportal konfiguriert haben, gespeichert und aktualisiert.

    ![][21]

    Sie können die App neu starten, um zu sehen, dass die Änderungen in die Datenbank in Azure übernommen wurden. Außerdem können Sie die Datenbank im Azure-Verwaltungsportal oder im Visual Studio SQL Server-Objekt-Explorer überprüfen. In den nächsten beiden Schritten wird das Azure-Verwaltungsportal verwendet, um die Änderungen in der Datenbank anzuzeigen.

4.  Klicken Sie im Azure-Verwaltungsportal auf die Option zum Verwalten der Datenbank, die mit Ihrem mobilen Dienst verknüpft ist.

    ![][22]

5.  Führen Sie im Verwaltungsportal eine Abfrage aus, um die von der App vorgenommenen Änderungen anzuzeigen. Ihre Abfrage sieht wie folgt aus, nur dass Sie Ihren Datenbanknamen anstelle von `todolist` verwenden.

        SELECT * FROM [todolist].[todoitems]

    ![][23]

Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

## <a name="next-steps"> </a>Nächste Schritte

Dieses Lernprogramm hat die Grundlagen der Zusammenarbeit einer Windows Phone 8-App mit Daten in Mobile Services, die mit der .Net-Laufzeit erstellt werden, demonstriert. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Prüfen und Ändern von Daten mit Skripten][Prüfen und Ändern von Daten mit Skripten]
    Informationen zur Verwendung von Serverskripten in Mobile Services, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging][Optimieren von Abfragen mittels Paging]
    Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie eines der folgenden Lernprogramme ausprobieren:

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App.

<!-- * [Get started with push notifications]    <br/>Learn how to send a very basic push notification to your app. -->

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz][Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
    Lernen Sie mehr über die Verwendung von Mobile Services mit .NET.



  [JavaScript-Backend-Version]: /de-de/develop/mobile/tutorials/get-started-with-data-wp8
  [Herunterladen des Windows Phone 8-App-Projekts]: #download-app
  [Erstellen eines neuen mobilen Dienstes]: #create-service
  [Lokales Herunterladen des mobilen Diensts]: #download-the-service-locally
  [Aktualisieren der Windows Phone-App zum Verwenden des mobilen Diensts]: #update-app
  [Testen der Windows Phone-App mit dem lokal gehosteten Dienst]: #test-locally-hosted
  [Veröffentlichen des mobilen Diensts in Azure]: #publish-mobile-service
  [Testen der Windows Phone-App mit dem in Azure gehosteten Dienst]: #test-azure-hosted
  [Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?linkid=268374
  [Kostenlose Azure-Testversion]: http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=AE564AB28&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F
  [GetStartedWithMobileServices-App]: http://go.microsoft.com/fwlink/p/?linkid=271146
  [0]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/mobile-data-sample-download-wp8-vs12.png
  [Windows Phone Store-App-Version]: http://go.microsoft.com/fwlink/p/?LinkId=397372
  [1]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-deployment-target.png
  [2]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/app-view.png
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [3]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/mobile-service-overview-page.png
  [4]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/download-service-project.png
  [5]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/download-publishing-profile.png
  [6]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/copy-service-and-packages-folder.png
  [7]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/add-service-project-to-solution.png
  [8]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/add-existing-project-dialog.png
  [9]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-build-service-project.png
  [10]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-start-debug-service-project.png
  [11]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/service-welcome-page.png
  [12]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/iis-express-tray.png
  [13]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-manage-nuget-packages.png
  [14]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/manage-nuget-packages.png
  [15]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/copy-mobileserviceclient-snippet.png
  [16]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-pasted-mobileserviceclient.png
  [17]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-build-solution.png
  [18]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-run-solution.png
  [19]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/local-item-checked.png
  [20]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/vs-show-local-table-data.png
  [21]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/azure-items.png
  [22]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/manage-sql-azure-database.png
  [23]: ./media/mobile-services-dotnet-backend-windows-phone-get-started-data/sql-azure-query.png
  [Prüfen und Ändern von Daten mit Skripten]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-wp8
  [Optimieren von Abfragen mittels Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-wp8
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-users/
  [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/develop/mobile/how-to-guides/work-with-net-client-library
