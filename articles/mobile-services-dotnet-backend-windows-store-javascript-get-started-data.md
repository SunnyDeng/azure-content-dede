<properties linkid="develop-mobile-tutorials-dotnet-backend-get-started-with-data-javascript-vs2013" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to get started using Mobile Services to leverage data in your Windows Store app." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="wesmc" solutions="" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/23/2014" ms.author="wesmc" />

# Erste Schritte mit Daten in Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

<div class="dev-center-tutorial-subselector">
    <a href="/de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/" title=".NET-Back-End" class="current">.NET-Back-End</a> | 
    <a href="/de-de/documentation/articles/mobile-services-windows-store-javascript-get-started-data/" title="JavaScript-Back-End">JavaScript-Back-End</a>
</div>

In diesem Thema wird gezeigt, wie Sie Azure Mobile Services als Back-End-Datenquelle für eine Windows Store-App nutzen können. In diesem Lernprogramm laden Sie ein Visual Studio 2013-Projekt für eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und zeigen dann die Datenänderungen beim Ausführen der App an.

Der mobile Dienst, den Sie in diesem Lernprogramm erstellen, ist ein mobiler .NET-Back-End-Dienst. Durch das .NET-Back-End können Sie .NET-Sprachen und Visual Studio für die serverseitige Geschäftslogik im mobilen Dienst verwenden und den mobilen Dienst auf Ihrem lokalen Computer ausführen und debuggen. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der JavaScript-Back-End-Version dieses Themas.

> [WACOM.NOTE]Dieses Thema veranschaulicht, wie Azure Mobile Services einem Windows Store-Projekt hinzugefügt werden. Mithilfe von Visual Studio 2013 Tools können Sie denselben mobilen .NET-Back-End-Dienst einem Projekt für eine universelle Windows-App hinzufügen. Weitere Informationen finden Sie in der Lernprogrammversion zu [universellen Windows-Apps][universellen Windows-Apps].

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1.  [Herunterladen des Windows Store-App-Projekts][Herunterladen des Windows Store-App-Projekts]
2.  [Erstellen eines neuen mobilen Dienstes][Erstellen eines neuen mobilen Dienstes]
3.  [Lokales Herunterladen des mobilen Diensts][Lokales Herunterladen des mobilen Diensts]
4.  [Aktualisieren der Windows Store-App zur Verwendung des mobilen Diensts][Aktualisieren der Windows Store-App zur Verwendung des mobilen Diensts]
5.  [Testen der Windows Store-App mit dem lokal gehosteten Dienst][Testen der Windows Store-App mit dem lokal gehosteten Dienst]
6.  [Veröffentlichen des mobilen Diensts in Azure][Veröffentlichen des mobilen Diensts in Azure]
7.  [Testen der Windows Store-App mit dem in Azure gehosteten Dienst][Testen der Windows Store-App mit dem in Azure gehosteten Dienst]

Für dieses Lernprogramm benötigen Sie Folgendes:

-   Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][Kostenlose Azure-Testversion].
-   [Visual Studio Professional 2013][Visual Studio Professional 2013]. Dazu ist eine kostenlose Testversion verfügbar.

## <a name="download-app"></a>Download des GetStartedWithData-Projekts

Dieses Lernprogramm basiert auf der [GetStartedWithMobileServices-App][GetStartedWithMobileServices-App], bei der es sich um ein Windows Store-App-Projekt in Visual Studio 2013 handelt. Die Benutzeroberfläche dieser App und die vom Mobile Services-Schnellstart generierte Benutzeroberfläche sind ähnlich, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1.  Laden Sie die JavaScript-Version der GetStartedWithMobileServices-Beispiel-App von der Website mit den [Codebeispielen für Entwickler][GetStartedWithMobileServices-App] herunter.

2.  Führen Sie Visual Studio 2013 mit Administratorberechtigung aus, indem Sie mit der rechten Maustaste auf Visual Studio klicken und dann **Als Administrator ausführen** auswählen.

3.  Öffnen Sie in Visual Studio 2013 das heruntergeladene Projekt. Erweitern Sie den Ordner "js", und sehen Sie sich die Datei "default.js" an.

    Beachten Sie, dass die hinzugefügten **TodoItem**-Objekte in einem speicherinternen `WinJS.Binding.List`-Element gespeichert werden.

4.  Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

5.  Geben Sie in der App unter **Insert a TodoItem** Text ein, und klicken Sie dann auf **Save**.

    ![][0]

    Der gespeicherte Text wird in der zweiten Spalte unter **Query and update data** angezeigt.

## <a name="create-service"></a>Erstellen eines neuen mobilen Diensts

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-new-service](../includes/mobile-services-dotnet-backend-create-new-service.md)]

## <a name="download-the-service-locally"></a>Herunterladen des mobilen Dienstprojekts und Hinzufügen zur Projektmappe

1.  Klicken Sie im [Azure-Verwaltungsportal][Azure-Verwaltungsportal] auf den neuen mobilen Dienst oder auf die Registerkarte mit dem Cloudsymbol, um zur Übersichtsseite zu gelangen.

    ![][1]

2.  Klicken Sie auf die **Windows Store**-Plattform. Erweitern Sie im Abschnitt **Get Started** den Eintrag **Connect an existing Windows Store app**, und klicken Sie auf **Download**, um ein personalisiertes Startprojekt für den mobilen Dienst herunterzuladen.

    ![][2]

3.  Blättern Sie zum Ende des Abschnitts **Get Started** bis zum Schritt **Publish your service to the cloud**. Klicken Sie auf den im Screenshot gezeigten Link, um eine Veröffentlichungsprofildatei für den soeben heruntergeladenen mobilen Dienst herunterzuladen.

    > [WACOM.NOTE] Speichern Sie die Datei an einem sicheren Ort, da sie vertrauliche Daten zu Ihrem Azure-Konto enthält. Sie löschen diese Datei später in diesem Lernprogramm, nachdem Sie den mobilen Dienst veröffentlicht haben.

    ![][3]

4.  Entpacken Sie das personalisierte Dienststartprojekt, das Sie heruntergeladen haben. Kopieren Sie die Ordner aus der ZIP-Datei in dasselbe **JavaScript**-Verzeichnis, in dem sich auch die Lösungsdatei (.sln) aus "Erste Schritte mit Daten" befindet. Dies erleichtert es dem NuGet-Paket-Manager, alle Pakete zu synchronisieren.

    ![][4]

5.  Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf die Lösung für die Windows Store-App aus "Erste Schritte mit Daten". Klicken Sie auf **Hinzufügen** und anschließend auf **Vorhandenes Projekt**.

    ![][5]

6.  Navigieren Sie im Dialogfeld "Add Existing Project" zum Projektordner des mobilen Diensts, den Sie in das **JavaScript**-Verzeichnis verschoben haben. Wählen Sie die C#-Projektdatei (.csproj) im Dienstunterverzeichnis aus. Klicken Sie auf **Öffnen**, um das Projekt zu Ihrer Lösung hinzuzufügen.

    ![][6]

7.  Klicken Sie im Projektmappen-Explorer in Visual Studio mit der rechten Maustaste auf das soeben hinzugefügte Dienstprojekt und anschließend auf **Build**, um sicherzustellen, dass es fehlerfrei erstellt wird. Während des Erstellens muss der NuGet-Paket-Manager möglicherweise einige NuGet-Pakete wiederherstellen, auf die im Projekt verwiesen wird.

    ![][7]

8.  Klicken Sie erneut mit der rechten Maustaste auf das Dienstprojekt. Klicken Sie diesmal im Kontextmenü **Debuggen** auf **Neue Instanz starten**.

    ![][8]

    Visual Studio öffnet die Standardwebseite für Ihren Dienst. Sie können auf **Jetzt testen** klicken, um die Methoden in Ihrem mobilen Dienst von der Standardwebseite aus zu testen.

    ![][9]

    Visual Studio hostet Ihren mobilen Dienst standardmäßig lokal in IIS Express. Klicken Sie zum Anzeigen mit der rechten Maustaste in der Taskleiste auf das IIS Express-Symbol.

    ![][10]

# <a name="update-app"></a>Aktualisieren der Windows Store-App zur Verwendung des mobilen Diensts

In diesem Abschnitt aktualisieren Sie die Windows Store-App, um den mobilen Dienst als Backend-Dienst für die Anwendung zu verwenden.

1.  Klicken Sie im Projektmappen-Explorer in Visual Studio mit der rechten Maustaste auf das Windows Store-App-Projekt, und wählen Sie dann **NuGet-Pakete verwalten** aus.

    ![][11]

2.  Suchen Sie im Dialogfeld "NuGet-Pakete verwalten" den Eintrag **WindowsAzure.MobileServices.WinJS** in der Online-Paketauflistung, und klicken Sie, um das Azure Mobile Services NuGet-Paket zu installieren. Schließen Sie dann das Dialogfeld.

    ![][12]

3.  Suchen Sie im Azure-Verwaltungsportal auf der Übersichtsseite für den mobilen Dienst den Schritt **Connect your app and store data in your service**. Wählen Sie **JavaScript** als ihre Sprache aus, und kopieren Sie den Codeausschnitt zum Erstellen von `MobileServiceClient`.

    ![][13]

4.  Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **js**, und öffnen Sie "default.js". Fügen Sie den kopierten Codeausschnitt in den `app.onactivated`-Ereignishandler direkt vor der Definition der `todoItems`-Variablen ein. Beachten Sie, dass der Codeausschnitt einen auskommentierten Konstruktor für die Verbindung mit dem mobilen Dienst in Azure mithilfe des Anwendungsschlüssels enthält. Das Kommentarzeichen wird in einem späteren Schritt entfernt.

    ![][14]

5.  Ersetzen Sie in "default.js" den Rest des Codes für den `app.onactiviated`-Ereignishandler durch den folgenden Code, der `todoItems` und die Vorgänge definiert, die wir mit dem mobilen Dienst testen möchten. Speichern Sie anschließend die Datei.

    Dieser Code verwendet das Mobile Services SDK für JavaScript, um der App das Speichern der Daten in einer Tabelle zu ermöglichen, die vom Dienst bereitgestellt wird, statt lokal im Arbeitsspeicher. Die drei Hauptmethoden sind `insertTodoItem`, `refreshTodoItems` und `updateCheckedTodoItem`. Diese drei Methoden ermöglichen das asynchrone Einfügen, Abfragen und Aktualisieren der Datensammlung mit einer Tabelle in Azure.

            var todoTable = client.getTable('TodoItem');
            var todoItems = new WinJS.Binding.List();
            var insertTodoItem = function (todoItem) {
                // This code inserts a new TodoItem into the database. 
                // Mobile Services assigns each item an id and the 
                // item is added to the Binding List
                todoTable.insert(todoItem).done(function (item) {
                    todoItems.push(item);
                });
            };
            var refreshTodoItems = function () {
                // This code refreshes the entries in the list view 
                // by querying the TodoItems table.
                todoTable.where()
                    .read()
                    .done(function (results) {
                        todoItems = new WinJS.Binding.List(results);
                        listItems.winControl.itemDataSource = todoItems.dataSource;
                    });
            };
            var updateCheckedTodoItem = function (todoItem) {
                // This code updates the database. 
                // When the MobileService responds, 
                // the item is removed from the list 
                todoTable.update(todoItem).done(function (item) {
                    todoItems.splice(todoItems.indexOf(item), 1);
                });
            };
            buttonSave.addEventListener("click", function () {
                insertTodoItem({
                    text: textInput.value,
                    complete: false
                });
            });
            buttonRefresh.addEventListener("click", function () {
                refreshTodoItems();
            });
            listItems.addEventListener("change", function (eventArgs) {
                var todoItem = eventArgs.target.dataContext.backingData;
                todoItem.complete = eventArgs.target.checked;
                updateCheckedTodoItem(todoItem);
            });
            refreshTodoItems();

6.  Öffnen Sie im Projektmappen-Explorer in Visual Studio die Datei "default.html". Fügen Sie oben in der Datei eine neue WinJS-Skriptreferenz für MobileServices.js ein. Speichern Sie anschließend die Datei.

        <script type="text/javascript" src="/js/MobileServices.js"></script>

    ![][15]

## <a name="test-locally-hosted"></a>Testen der Windows Store-App mit dem lokal gehosteten Dienst

[WACOM.INCLUDE [mobile-services-dotnet-backend-test-local-service-data](../includes/mobile-services-dotnet-backend-test-local-service-data.md)]

## <a name="publish-mobile-service"></a>Veröffentlichen des mobilen Diensts in Azure

[WACOM.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]

## <a name="test-azure-hosted"></a>Testen des mobilen in Azure veröffentlichten Diensts

1.  Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **js**, und öffnen Sie "default.js". Kommentieren Sie den Code aus, der den `MobileServiceClient` erstellt, der mit dem lokal gehosteten mobilen Dienst verbunden wird. Heben Sie die Auskommentierung für den Code auf, der den `MobileServiceClient` erstellt, der mit dem Dienst in Azure verbunden wird. Speichern Sie die Änderungen an der Datei.

        //var client = new WindowsAzure.MobileServiceClient(
        //          "http://localhost:59226"
        //);
        // Use this constructor instead after publishing to the cloud
        var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.preview.azure-mobile-preview.net/",
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");

2.  Drücken Sie in Visual Studio die Taste F5, oder klicken Sie im Debuggen-Menü auf **Debugging starten**. Daraufhin wird die Windows Store-App mit den vorhergehenden Änderungen neu erstellt, bevor die App ausgeführt wird, um eine Verbindung mit dem mobilen Dienst herzustellen, welcher remote in Azure gehostet wird.

    ![][16]

3.  Geben Sie neue "todoitems" ein, und klicken Sie jeweils auf **Speichern**. Klicken Sie auf die Kontrollkästchen für einige der neuen Elemente, um sie abzuschließen. Jedes neue todoItem wird in der SQL-Datenbank, die Sie zuvor für Ihren mobilen Dienst im Azure-Verwaltungsportal konfiguriert haben, gespeichert und aktualisiert.

    ![][17]

    Sie können die App neu starten, um zu sehen, dass die Änderungen in die Datenbank in Azure übernommen wurden. Außerdem können Sie die Datenbank im Azure-Verwaltungsportal oder im Visual Studio SQL Server-Objekt-Explorer überprüfen. In den nächsten beiden Schritten wird das Azure-Verwaltungsportal verwendet, um die Änderungen in der Datenbank anzuzeigen.

4.  Klicken Sie im Azure-Verwaltungsportal auf die Option zum Verwalten der Datenbank, die mit Ihrem mobilen Dienst verknüpft ist.

    ![][18]

5.  Führen Sie im Verwaltungsportal eine Abfrage aus, um die von der Windows Store-App vorgenommenen Änderungen anzuzeigen. Ihre Abfrage sieht wie folgt aus, nur dass Sie Ihren Datenbanknamen anstelle von `todolist` verwenden.

        SELECT * FROM [todolist].[todoitems]

    ![][19]

Damit ist das Lernprogramm **Erste Schritte mit Daten** beendet.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer Windows Store-App für die Arbeit mit Daten in Mobile Services gezeigt. Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Prüfen und Ändern von Daten mit Skripten][Prüfen und Ändern von Daten mit Skripten]
    Informationen zur Verwendung von Serverskripten in Mobile Services, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging][Optimieren von Abfragen mittels Paging]
    Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie eines der folgenden Lernprogramme ausprobieren:

-   [Erste Schritte mit der Authentifizierung][Erste Schritte mit der Authentifizierung]
    Informationen zur Authentifizierung von Benutzern Ihrer App.

-   [Erste Schritte mit Pushbenachrichtigungen][Erste Schritte mit Pushbenachrichtigungen]
    Informationen zum Senden einer einfachen Pushbenachrichtigung an Ihre App.

-   [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz][Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]
    Lernen Sie mehr über die Verwendung von Mobile Services mit HTML und JavaScript.



  [mobile-services-selector-get-started-data-legacy]: ../includes/mobile-services-selector-get-started-data-legacy.md
  [universellen Windows-Apps]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
  [Herunterladen des Windows Store-App-Projekts]: #download-app
  [Erstellen eines neuen mobilen Dienstes]: #create-service
  [Lokales Herunterladen des mobilen Diensts]: #download-the-service-locally
  [Aktualisieren der Windows Store-App zur Verwendung des mobilen Diensts]: #update-app
  [Testen der Windows Store-App mit dem lokal gehosteten Dienst]: #test-locally-hosted
  [Veröffentlichen des mobilen Diensts in Azure]: #publish-mobile-service
  [Testen der Windows Store-App mit dem in Azure gehosteten Dienst]: #test-azure-hosted
  [Kostenlose Azure-Testversion]: http://azure.microsoft.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-javascript-get-started-data%2F
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=257546
  [GetStartedWithMobileServices-App]: http://go.microsoft.com/fwlink/p/?LinkId=328660
  [0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/app-view.png
  [mobile-services-dotnet-backend-create-new-service]: ../includes/mobile-services-dotnet-backend-create-new-service.md
  [Azure-Verwaltungsportal]: https://manage.windowsazure.com/
  [1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-service-overview-page.png
  [2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-service-project.png
  [3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-publishing-profile.png
  [4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-service-and-packages-folder.png
  [5]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-service-project-to-solution.png
  [6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-existing-project-dialog.png
  [7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-service-project.png
  [8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-start-debug-service-project.png
  [9]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/service-welcome-page.png
  [10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/iis-express-tray.png
  [11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-manage-nuget-packages.png
  [12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-nuget-packages.png
  [13]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-mobileserviceclient-snippet.png
  [14]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-pasted-mobileserviceclient.png
  [15]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-mobileservices-script-reference.png
  [mobile-services-dotnet-backend-test-local-service-data]: ../includes/mobile-services-dotnet-backend-test-local-service-data.md
  [mobile-services-dotnet-backend-publish-service]: ../includes/mobile-services-dotnet-backend-publish-service.md
  [16]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-run-solution.png
  [17]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/azure-items.png
  [18]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-sql-azure-database.png
  [19]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/sql-azure-query.png
  [Prüfen und Ändern von Daten mit Skripten]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-js
  [Optimieren von Abfragen mittels Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-js
  [Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
  [Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
  [Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-html-how-to-use-client-library/
