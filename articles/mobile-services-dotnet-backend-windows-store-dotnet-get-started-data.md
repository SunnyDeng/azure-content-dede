<properties 
	pageTitle="Erste Schritte mit Daten (Windows Store) | Mobile Dev Center" 
	description="Erfahren Sie mehr über die ersten Schritte bei der Verwendung von Mobile Services zur Nutzung von Daten in Ihrer Windows Store-App." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/23/2014" 
	ms.author="wesmc"/>

# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-center-tutorial-subselector">
	<a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/" title=".NET backend" class="current">.NET-Back-End</a> | 
	<a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/" title="JavaScript backend">JavaScript-Back-End</a>
</div>

In diesem Thema wird gezeigt, wie Sie Azure Mobile Services als Back-End-Datenquelle für eine Windows Store-App nutzen können. In diesem Lernprogramm laden Sie ein Visual Studio 2013-Projekt für eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und zeigen dann die Datenänderungen beim Ausführen der App an.

Bei dem mobilen Dienst, den Sie in diesem Lernprogramm erstellen, handelt es sich um einen mobilen .NET-Back-End-Dienst. Dank des .NET-Back-Ends können Sie .NET-Sprachen und Visual Studio für die serverseitige Geschäftslogik im mobilen Dienst verwenden und Ihren mobilen Dienst auf Ihrem lokalen Computer ausführen und debuggen. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der JavaScript-Backend-Version dieses Themas.

>[AZURE.NOTE]Dieses Thema veranschaulicht, wie Azure Mobile Services einem Windows Store-Projekt hinzugefügt werden. Mithilfe von Visual Studio 2013 Tools können Sie denselben mobilen .NET-Back-End-Dienst einem Projekt für eine universelle Windows-App hinzufügen. Weitere Informationen finden Sie in der Version für [die universelle Windows-App](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data) dieses Lernprogram
	ms.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Herunterladen des Windows Store-App-Projekts]
2. [Erstellen eines neuen mobilen Diensts]
3. [Lokales Herunterladen des mobilen Diensts]
4. [Aktualisieren der Windows Store-App zur Verwendung des mobilen Diensts]
5. [Testen der Windows Store-App mit dem lokal gehosteten Dienst]
6. [Veröffentlichen des mobilen Dienstes in Azure]
7. [Testen der Windows Store-App mit dem in Azure gehosteten Dienst]

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-dotnet-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Dazu ist eine kostenlose Testversion verfügbar.

##<a name="download-app"></a>Download des GetStartedWithData-Projekts

Dieses Lernprogramm baut auf der [GetStartedWithMobileServices-App][Site mit Codebeispielen für Entwickler] auf, bei der es sich um ein Windows Store-App-Projekt in Visual Studio 2013 handelt. Die Benutzeroberfläche dieser App und die vom Mobile Services-Schnellstart generierte Benutzeroberfläche sind vergleichbar, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden. 

1. Laden Sie die C#-Version der GetStartedWithMobileServices-Beispiel-App von der [Website mit den Codebeispielen für Entwickler] herunter. 

2. Führen Sie Visual Studio 2013 mit Administratorberechtigung aus, indem Sie mit der rechten Maustaste auf Visual Studio klicken und dann **Als Administrator ausführen** auswählen.

3. Öffnen Sie in Visual Studio 2013 das heruntergeladene Projekt, und sehen Sie sich die Datei "MainPage.xaml.cs" an.

   	Beachten Sie, dass die hinzugefügten **TodoItem**-Elemente in einem speicherinternen **ObservableCollection&lt;TodoItem>&gt;**-Element gespeichert werden.

4. Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

5. Geben Sie in der App in **TodoItem einfügen** Text ein, und klicken Sie dann auf **Speichern**.

   	![][0]  

   	Beachten Sie, dass der gespeicherte Text in der zweiten Spalte unter **Query and update data** angezeigt wird.

##<a name="create-service"></a>Erstellen eines neuen mobilen Diensts

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

##<a name="download-the-service-locally"></a>Herunterladen des mobilen Dienstprojekts und Hinzufügen zur Projektmappe

1. Klicken Sie im [Azure-Verwaltungsportal] auf den neuen Mobile Service oder auf die Registerkarte mit dem Cloudsymbol, um zur Übersichtsseite zu gelangen.

    ![][2]

2. Klicken Sie auf die **Windows Store**-Plattform. Erweitern Sie im Abschnitt **Get Started** den Eintrag **Connect an existing Windows Store app**, und klicken Sie auf **Download**, um ein personalisiertes Startprojekt für den mobilen Dienst herunterzuladen. 

    ![][3]

3. Blättern Sie zum Ende des Abschnitts **Get Started** bis zum Schritt **Publish your service to the cloud**. Klicken Sie auf den im Screenshot gezeigten Link, um eine Veröffentlichungsprofildatei für den soeben heruntergeladenen mobilen Dienst herunterzuladen. 

    > [AZURE.NOTE] Speichern Sie die Datei an einem sicheren Ort, da sie vertrauliche Daten zu Ihrem Azure-Konto enthält. Sie löschen diese Datei später in diesem Lernprogramm, nachdem Sie den mobilen Dienst veröffentlicht haben. 

    ![][5]


4. Entpacken Sie das personalisierte Dienststartprojekt, das Sie heruntergeladen haben. Kopieren Sie die Ordner aus der ZIP-Datei in das gleiche **C#**-Verzeichnis, in dem sich die Lösungsdatei "Erste Schritte mit Daten" (.sln) befindet. Dies erleichtert es dem NuGet-Paket-Manager, alle Pakete zu synchronisieren.

    ![][26]

5. Klicken Sie dann im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf Ihre Lösung für die Windows Store-App "Erste Schritte mit Daten". Klicken Sie auf **Hinzufügen** und anschließend auf **Vorhandenes Projekt**.

    ![][4]

6. Navigieren Sie im Dialogfeld "Vorhandenes Projekt hinzufügen" zum Projektordner mit dem mobilen Dienst, den Sie in das **C#**-Verzeichnis verschoben haben. Wählen Sie die C#-Projektdatei (.csproj) im Dienstunterverzeichnis aus. Klicken Sie auf **Öffnen**, um das Projekt zu Ihrer Lösung hinzuzufügen.

    ![][6]

7. Klicken Sie im Projektmappen-Explorer in Visual Studio mit der rechten Maustaste auf das soeben hinzugefügte Dienstprojekt und anschließend auf **Build**, um sicherzustellen, dass es fehlerfrei erstellt wird. Während des Erstellens muss der NuGet-Paket-Manager möglicherweise einige NuGet-Pakete wiederherstellen, auf die im Projekt verwiesen wird.

    ![][20]

8. Klicken Sie erneut mit der rechten Maustaste auf das Dienstprojekt. Klicken Sie diesmal im Kontextmenü **Debuggen** auf **Neue Instanz starten**.

    ![][21]

    Visual Studio öffnet die Standardwebseite für Ihren Dienst. Sie können auf **Jetzt testen** klicken, um die Methoden in Ihrem mobilen Dienst von der Standardwebseite aus zu testen.

    ![][22]

    Visual Studio hostet Ihren mobilen Dienst standardmäßig lokal in IIS Express. Klicken Sie zum Anzeigen mit der rechten Maustaste in der Taskleiste auf das IIS Express-Symbol.

    ![][23]

#<a name="update-app"></a>Aktualisieren der Windows Store-App zur Verwendung des mobilen Diensts

In diesem Abschnitt aktualisieren Sie die Windows Store-App, um den mobilen Dienst als Backend-Dienst für die Anwendung zu verwenden.


1. Klicken Sie im Projektmappen-Explorer in Visual Studio mit der rechten Maustaste auf das Windows Store-App-Projekt, und wählen Sie dann **NuGet-Pakete verwalten** aus.

    ![][7]

2. Suchen Sie im Dialogfeld "NuGet-Pakete verwalten" nach **WindowsAzure.MobileServices** in der Sammlung der Onlinepakete, und klicken Sie, um das Azure Mobile Services Nuget-Paket zu installieren. Schließen Sie dann das Dialogfeld.

    ![][8]

3. Suchen Sie zurück im Azure-Verwaltungsportal den Schritt **Verbinden der App und Speichern von Daten im Dienst**. Vergewissern Sie sich, dass **C#** als Sprache ausgewählt ist. Kopieren Sie den Codeausschnitt, der die `MobileServiceClient`-Verbindung erstellt.

    ![][9]

4. Öffnen Sie "App.xaml.cs" in Visual Studio. Fügen Sie den Codeausschnitt am Anfang der `App`-Klassendefinition ein. Fügen Sie ebenfalls die folgende `using`-Anweisung am Anfang der Datei hinzu, und speichern Sie die Datei.

		using Microsoft.WindowsAzure.MobileServices;

    ![][10]


5. Öffnen Sie "MainPage.xaml.cs" in Visual Studio. Fügen Sie folgende using-Anweisung hinzu: 

		using Microsoft.WindowsAzure.MobileServices;

6. Ersetzen Sie in Visual Studio in der Datei "MainPage.xaml.cs" die`MainPage` -Klassendefinition mit der folgenden Definition, und speichern Sie die Datei. 

    Dieser Code verwendet das Mobile Services SDK, um der App das Speichern der Daten in einer Tabelle zu ermöglichen, die vom Dienst bereitgestellt wird, anstatt lokal im Arbeitsspeicher gespeichert zu werden. Die drei Hauptmethoden sind `InsertTodoItem`, `RefreshTodoItems` und `UpdateCheckedTodoItem`. Diese drei Methoden ermöglichen das asynchrone Einfügen, Abfragen und Aktualisieren der Datensammlung mit einer Tabelle in Azure.

        public sealed partial class MainPage : Page
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
                ite
	ms.Add(todoItem);
            }
            private async void RefreshTodoItems()
            {
                items = await todoTable.ToCollectionAsync(); 
                ListIte
	ms.ItemsSource = items;
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
                var todoItem = new TodoItem { Text = TextInput.Text };
                InsertTodoItem(todoItem);
            }
            private void CheckBoxComplete_Checked(object sender, RoutedEventArgs e)
            {
                CheckBox cb = (CheckBox)sender;
                TodoItem item = cb.DataContext as TodoItem;
                UpdateCheckedTodoItem(item);
            }
            protected override void OnNavigatedTo(NavigationEventArgs e)
            {
                RefreshTodoItems();
            }
        }


##<a name="test-locally-hosted"></a>Testen der Windows Store-App mit dem lokal gehosteten Dienst

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-data](../includes/mobile-services-dotnet-backend-test-local-service-data.md)]

##<a name="publish-mobile-service"></a>Veröffentlichen des mobilen Dienstes in Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="test-azure-hosted"></a>Testen des mobilen in Azure veröffentlichten Diensts

1. Öffnen Sie "App.xaml.cs" in Visual Studio.  Kommentieren Sie den Code aus, der den `MobileServiceClient` erstellt, welcher mit dem lokal gehosteten mobilen Dienst verbunden wird. Heben Sie die Auskommentierung für den Code auf, der den `MobileServiceClient` erstellt, der mit dem Dienst in Azure verbunden wird. Speichern Sie die Änderungen an der Datei.

        sealed partial class App : Application
        {
            //public static MobileServiceClient MobileService = new MobileServiceClient(
            //          "http://localhost:59226"
            //);
            // Use this constructor instead after publishing to the cloud
            public static MobileServiceClient MobileService = new MobileServiceClient(
                 "https://todolist.preview.azure-mobile-preview.net/",
                 "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX"
            );        
            ....


2. Drücken Sie in Visual Studio die Taste F5, oder klicken Sie im Debuggen-Menü auf **Debugging starten**. Daraufhin wird die Windows Store-App mit den vorhergehenden Änderungen neu erstellt, bevor die App ausgeführt wird, um eine Verbindung mit dem mobilen Dienst herzustellen, welcher remote in Azure gehostet wird. 


3. Geben Sie neue todoitems ein, und klicken Sie jeweils auf **Speichern**. Klicken Sie auf die Kontrollkästchen für einige der neuen Elemente, um sie abzuschließen. Jedes neue todoItem wird in der SQL-Datenbank, die Sie zuvor für Ihren mobilen Dienst im Azure-Verwaltungsportal konfiguriert haben, gespeichert und aktualisiert. 

    ![][16]

    Sie können die App neu starten, um zu sehen, dass die Änderungen in die Datenbank in Azure übernommen wurden. 

##<a name="view-stored-data"></a>Anzeigen der in der SQL-Datenbank gespeicherten Daten

[AZURE.INCLUDE [mobile-services-dotnet-backend-view-sql-data](../includes/mobile-services-dotnet-backend-view-sql-data.md)]

Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Arbeit mit Daten in Mobile Services gezeigt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

* [Überprüfen und Ändern von Daten mit Skripts]
  <br/>Erfahren Sie mehr über die Verwendung von Serverskripts in Mobile Services, um die von Ihrer App gesendeten Daten zu prüfen und zu ändern.

* [Optimieren von Abfragen mit Paging]
  <br/>Erfahren Sie, wie Sie Paging in Abfragen zum Steuern der in einer einzelnen Anforderung verarbeiteten Datenmengen verwenden können.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie eines der folgenden Lernprogramme ausprobieren:

* [Erste Schritte mit der Authentifizierung]
  <br/>Erfahren Sie, wie Benutzer in Ihrer App authentifiziert werden.

* [Erste Schritte mit Pushbenachrichtigungen]
  <br/>Informationen über das Versenden einer grundlegenden Pushbenachrichtigung an die App.

* [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
  <br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit .NET.
  
<!-- Anchors. -->

[Herunterladen des Windows Store-App-Projekts]: #download-app
[Erstellen eines neuen mobilen Diensts]: #create-service
[Lokales Herunterladen des mobilen Diensts]: #download-the-service-locally
[Aktualisieren der Windows Store-App zur Verwendung des mobilen Diensts]: #update-app
[Testen der Windows Store-App mit dem lokal gehosteten Dienst]: #test-locally-hosted
[Veröffentlichen des mobilen Dienstes in Azure]: #publish-mobile-service
[Testen der Windows Store-App mit dem in Azure gehosteten Dienst]: #test-azure-hosted
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-data-sample-download-dotnet-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-run-solution.png
[16]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/azure-ite
	ms.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/sql-azure-query.png

[20]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/copy-service-and-packages-folder.png


<!-- URLs. -->
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-dotnet
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-dotnet
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push/

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Website mit Codebeispielen für Entwickler]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
[MobileServiceClient-Klasse]: http://go.microsoft.com/fwlink/p/?LinkId=302030
 


<!--HONumber=42-->
