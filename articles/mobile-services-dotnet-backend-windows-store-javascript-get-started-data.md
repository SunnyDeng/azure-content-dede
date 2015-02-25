<properties pageTitle="Erste Schritte mit Daten (Windows Store) | Mobile Dev Center" description="Erfahren Sie mehr über die ersten Schritte bei der Verwendung von Mobile Services zur Nutzung von Daten in Ihrer Windows Store-App." services="mobile-services" documentationCenter="windows" authors="wesmc7777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/27/2014" ms.author="wesmc"/>

# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data-legacy](../includes/mobile-services-selector-get-started-data-legacy.md)]

In diesem Thema wird gezeigt, wie Sie Azure Mobile Services als Back-End-Datenquelle für eine Windows Store-App nutzen können. In diesem Lernprogramm laden Sie ein Visual Studio 2013-Projekt für eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und zeigen dann die Datenänderungen beim Ausführen der App an.

Bei dem mobilen Dienst, den Sie in diesem Lernprogramm erstellen, handelt es sich um einen mobilen .NET-Back-End-Dienst. Dank des .NET-Back-Ends können Sie .NET-Sprachen und Visual Studio für die serverseitige Geschäftslogik im mobilen Dienst verwenden und Ihren mobilen Dienst auf Ihrem lokalen Computer ausführen und debuggen. Informationen zum Erstellen eines mobilen Diensts, dessen serverseitige Geschäftslogik Sie in JavaScript schreiben können, finden Sie in der JavaScript-Backend-Version dieses Themas.

>[AZURE.NOTE]Dieses Thema veranschaulicht, wie Azure Mobile Services einem Windows Store-Projekt hinzugefügt werden. Mithilfe von Visual Studio 2013 Tools können Sie denselben mobilen .NET-Back-End-Dienst einem Projekt für eine universelle Windows-App hinzufügen. Weitere Informationen finden Sie in der Lernprogrammversion zu [universellen Windows-Apps](/de-de/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data).

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Herunterladen des Windows Store-App-Projekts]
2. [Erstellen eines neuen mobilen Diensts]
3. [Lokales Herunterladen des mobilen Diensts]
4. [Aktualisieren der Windows Store-App zur Verwendung des mobilen Diensts]
5. [Testen der Windows Store-App mit dem lokal gehosteten Dienst]
6. [Veröffentlichen des mobilen Dienstes in Azure]
7. [Testen der Windows Store-App mit dem in Azure gehosteten Dienst]

Für dieses Lernprogramm benötigen Sie Folgendes:

* Ein aktives Azure-Konto. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/de-de/pricing/free-trial/?WT.mc_id=A0E0E5C02&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-windows-store-javascript-get-started-data%2F).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=257546" target="_blank">Visual Studio Professional 2013</a>. Dazu ist eine kostenlose Testversion verfügbar.

##<a name="download-app"></a>Download des GetStartedWithData-Projekts

Dieses Lernprogramm baut auf der [GetStartedWithMobileServices-App][Site mit Codebeispielen für Entwickler] auf, bei der es sich um ein Windows Store-App-Projekt in Visual Studio 2013 handelt. Die Benutzeroberfläche dieser App und die vom Mobile Services-Schnellstart generierte Benutzeroberfläche sind vergleichbar, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden. 

1. Laden Sie die JavaScript-Version der GetStartedWithMobileServices-Beispiel-App von der [Website mit den Codebeispielen für Entwickler] herunter. 

2. Führen Sie Visual Studio 2013 mit Administratorberechtigung aus, indem Sie mit der rechten Maustaste auf Visual Studio klicken und dann **Als Administrator ausführen** auswählen.

3. Öffnen Sie in Visual Studio 2013 das heruntergeladene Projekt. Erweitern Sie den Ordner "js", und sehen Sie sich die Datei "default.js" an.

   	Beachten Sie, dass die hinzugefügten **TodoItem**-Objekte in einem speicherinternen  `WinJS.Binding.List`-Element gespeichert werden.

4. Drücken Sie die **F5**-Taste, um das Projekt neu zu erstellen und die App zu starten.

5. Geben Sie in der App unter **TodoItem einfügen** Text ein, und klicken Sie dann auf **Speichern**.

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


4. Entpacken Sie das personalisierte Dienststartprojekt, das Sie heruntergeladen haben. Kopieren Sie die Ordner aus der ZIP-Datei in dasselbe **JavaScript**-Verzeichnis, in dem sich auch die Lösungsdatei (.sln) aus "Erste Schritte mit Daten" befindet. Dies erleichtert es dem NuGet-Paket-Manager, alle Pakete zu synchronisieren. 

    ![][26]

5. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf die Lösung für die Windows Store-App aus "Erste Schritte mit Daten". Klicken Sie auf **Hinzufügen** und anschließend auf **Vorhandenes Projekt**.

    ![][4]

6. Navigieren Sie im Dialogfeld "Vorhandenes Projekt hinzufügen" zum Projektordner des mobilen Diensts, den Sie in das **JavaScript**-Verzeichnis verschoben haben. Wählen Sie die C#-Projektdatei (.csproj) im Dienstunterverzeichnis aus. Klicken Sie auf **Öffnen**, um das Projekt zu Ihrer Lösung hinzuzufügen.

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

2. Suchen Sie im Dialogfeld "NuGet-Pakete verwalten" den Eintrag **WindowsAzure.MobileServices.WinJS** in der Onlinepaketauflistung, und klicken Sie, um das Azure Mobile Services NuGet-Paket zu installieren. Schließen Sie dann das Dialogfeld.

    ![][8]

3. Suchen Sie zurück im Azure-Verwaltungsportal auf der Übersichtsseite für den mobilen Dienst den Schritt **Verbinden der App und Speichern von Daten im Dienst**. Wählen Sie **JavaScript** als ihre Sprache aus, und kopieren Sie den Codeausschnitt zum Erstellen von  `MobileServiceClient`.

    ![][9]

4. Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **js**, und öffnen Sie die Datei "default.js". Fügen Sie den kopierten Codeausschnitt im  `app.onactivated`-Ereignishandler direkt vor der Definition für die  `todoItems`-Variable ein. Beachten Sie, dass der Codeausschnitt einen auskommentierten Konstruktor für die Verbindung mit dem mobilen Dienst in Azure mithilfe des Anwendungsschlüssels enthält. Das Kommentarzeichen wird in einem späteren Schritt entfernt.

    ![][10]


5. Ersetzen Sie in "default.js" den Rest des Codes für den  `app.onactiviated`-Ereignishandler durch den folgenden Code, der  `todoItems` und die Vorgänge definiert, die wir mit dem mobilen Dienst testen möchten. Speichern Sie anschließend die Datei.

    Dieser Code verwendet das Mobile Services SDK für JavaScript, um der App das Speichern der Daten in einer Tabelle zu ermöglichen, die vom Dienst bereitgestellt wird, statt lokal im Arbeitsspeicher. Die drei Hauptmethoden sind  `insertTodoItem`,  `refreshTodoItems` und  `updateCheckedTodoItem`. Diese drei Methoden ermöglichen das asynchrone Einfügen, Abfragen und Aktualisieren der Datensammlung mit einer Tabelle in Azure.

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

6. Öffnen Sie im Projektmappen-Explorer in Visual Studio die Datei "default.html". Fügen Sie am Anfang der Datei eine neue WinJS-Skriptreferenz für MobileServices.js ein. Speichern Sie anschließend die Datei.

        <script type="text/javascript" src="/js/MobileServices.js"></script>

    ![][19]

##<a name="test-locally-hosted"></a>Testen der Windows Store-App mit dem lokal gehosteten Dienst

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service-data](../includes/mobile-services-dotnet-backend-test-local-service-data.md)]

##<a name="publish-mobile-service"></a>Veröffentlichen des mobilen Dienstes in Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../includes/mobile-services-dotnet-backend-publish-service.md)]


##<a name="test-azure-hosted"></a>Testen des mobilen in Azure veröffentlichten Diensts

1. Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **js**, und öffnen Sie die Datei "default.js".  Kommentieren Sie den Code aus, der den  `MobileServiceClient` erstellt, der mit dem lokal gehosteten mobilen Dienst verbunden wird. Heben Sie die Auskommentierung für den Code auf, der den  `MobileServiceClient` erstellt, der mit dem Dienst in Azure verbunden wird. Speichern Sie die Änderungen an der Datei.

        //var client = new WindowsAzure.MobileServiceClient(
        //          "http://localhost:59226"
        //);	
        // Use this constructor instead after publishing to the cloud
        var client = new WindowsAzure.MobileServiceClient(
            "https://todolist.preview.azure-mobile-preview.net/",
            "XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX");


2. Drücken Sie in Visual Studio die Taste F5, oder klicken Sie im Debuggen-Menü auf **Debugging starten**. Daraufhin wird die Windows Store-App mit den vorhergehenden Änderungen neu erstellt, bevor die App ausgeführt wird, um eine Verbindung mit dem mobilen Dienst herzustellen, welcher remote in Azure gehostet wird. 

    ![][12]


3. Geben Sie neue todoitems ein, und klicken Sie jeweils auf **Speichern**. Klicken Sie auf die Kontrollkästchen für einige der neuen Elemente, um sie abzuschließen. Jedes neue todoItem wird in der SQL-Datenbank, die Sie zuvor für Ihren mobilen Dienst im Azure-Verwaltungsportal konfiguriert haben, gespeichert und aktualisiert. 

    ![][16]

    Sie können die App neu starten, um zu sehen, dass die Änderungen in die Datenbank in Azure übernommen wurden. Außerdem können Sie die Datenbank im Azure-Verwaltungsportal oder im Visual Studio SQL Server-Objekt-Explorer überprüfen. In den nächsten beiden Schritten wird das Azure-Verwaltungsportal verwendet, um die Änderungen in der Datenbank anzuzeigen.

4. Klicken Sie im Azure-Verwaltungsportal auf die Option zum Verwalten der Datenbank, die mit Ihrem mobilen Dienst verknüpft ist.

    ![][17]

5. Führen Sie im Verwaltungsportal eine Abfrage aus, um die von der Windows Store-App vorgenommenen Änderungen anzuzeigen. Ihre Abfrage sieht wie folgt aus, nur dass Sie Ihren Datenbanknamen anstelle von  `todolist` verwenden.

        SELECT * FROM [todolist].[todoitems]	

    ![][18]

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
  <br/>Erfahren Sie mehr über die Verwendung von Mobile Services mit HTML und JavaScript.
  
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
[0]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/app-view.png
[1]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-data-sample-download-javascript-vs13.png
[2]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/mobile-service-overview-page.png
[3]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-service-project.png
[4]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-service-project-to-solution.png
[5]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/download-publishing-profile.png
[6]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/add-existing-project-dialog.png
[7]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-manage-nuget-packages.png
[8]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-nuget-packages.png
[9]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-mobileserviceclient-snippet.png
[10]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-pasted-mobileserviceclient.png
[11]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-solution.png
[12]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-run-solution.png

[16]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/azure-items.png
[17]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/manage-sql-azure-database.png
[18]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/sql-azure-query.png
[19]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-mobileservices-script-reference.png
[20]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-build-service-project.png
[21]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/vs-start-debug-service-project.png
[22]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/service-welcome-page.png
[23]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/iis-express-tray.png

[26]: ./media/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/copy-service-and-packages-folder.png

<!-- URLs. -->
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-js
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-js
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started/
[Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
[Erste Schritte mit der Authentifizierung]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/


[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Website mit Codebeispielen für Entwickler]:  http://go.microsoft.com/fwlink/p/?LinkId=328660
[Mobile Services .NET-Anleitungen: Konzeptionelle Referenz]: /de-de/documentation/articles/mobile-services-html-how-to-use-client-library/
[MobileServiceClient-Klasse]: http://go.microsoft.com/fwlink/p/?LinkId=302030
  

<!--HONumber=42-->
