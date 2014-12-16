<properties urlDisplayName="Handle Conflicts with Offline Data" pageTitle="Behandeln von Konflikten mit Offlinedaten in Mobile Services (Windows Store) | Mobile Dev Center" metaKeywords="" description="Learn how to use Azure Mobile Services handle conflicts when syncing offline data in your Windows Store application" metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Handling conflicts with offline data in Mobile Services" authors="wesmc" manager="dwrede" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/26/2014" ms.author="wesmc" />


# Behandeln von Konflikten bei der Synchronisierung von Offlinedaten in Mobile Services

<div class="dev-center-tutorial-selector sublanding">
<a href="/de-de/documentation/articles/mobile-services-windows-store-dotnet-handling-conflicts-offline-data" title="Windows Store C#" class="current">Windows Store C#</a>
<a href="/de-de/documentation/articles/mobile-services-windows-phone-handling-conflicts-offline-data" title="Windows Phone">Windows Phone</a>
<a href="/de-de/documentation/articles/mobile-services-ios-handling-conflicts-offline-data" title="iOS">iOS</a>
</div>

<div class="dev-onpage-video-clear clearfix">
<div class="dev-onpage-left-content">
<p>In diesem Thema erfahren Sie, wie Sie Daten synchronisieren und Konflikte behandeln können, wenn Sie die Offlinefunktionen von Azure Mobile Services verwenden.</p>
<p>Wenn Sie lieber ein Video zu diesem Thema ansehen möchten, können Sie den Clip auf der rechten Seite auswählen. In diesem werden dieselben Schritte behandelt wie in diesem Lernprogramm.</p>
</div>
<div class="dev-onpage-video-wrapper"><a href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="label">Lernprogramm ansehen</a> <a style="background-image: url('http://video.ch9.ms/ch9/ea1c/ffed2371-4db1-4a8e-8869-80013859ea1c/BuildOfflineAppsAzureMobileServices_220.jpg') !important;" href="http://channel9.msdn.com/Series/Windows-Azure-Mobile-Services/Build-offline-apps-Azure-Mobile-Services" target="_blank" class="dev-onpage-video"><span class="icon">Video abspielen</span></a> <span class="time">14:36:00</span></div>
</div>

In diesem Lernprogramm werden Sie eine Windows Universal C#-Lösung für eine App herunterladen, die die Behandlung von Konflikten bei der Offlinesynchronisierung unterstützt. Sie integrieren einen mobilen Dienst in die App und führen dann die Windows Store 8.1- und Windows Phone 8.1-Clients aus, um einen Synchronisierungskonflikt zu generieren und ihn zu beheben.

Dieses Lernprogramm baut auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Offlinedaten] auf. Bevor Sie mit diesem Lernprogramm beginnen, sollten Sie zunächst [Erste Schritte mit Offlinedaten] abschließen.


In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Herunterladen des Windows Store-App-Projekts] 
2. [Testen der App mit einem mobilen Dienst]
3. [Manuelles Aktualisieren der Daten im Backend zum Erzeugen eines Konflikts]

In diesem Lernprogramm wird Visual Studio 2013 für Windows 8.1 verwendet.


## <a name="download-app"></a>Herunterladen des Beispielprojekts

![][0]

In diesem Lernprogramm wird exemplarisch gezeigt, wie das [Todo Offline-Beispiel für Mobile Services] Synchronisierungskonflikte zwischen dem lokalen Offline-Store und der Mobile Service-Datenbank in Azure behandelt.

1. Laden Sie die Zip-Datei für das [Mobile Services-GitHub-Beispielrepository] herunter, und extrahieren Sie es in ein Arbeitsverzeichnis. 

2. Wenn Sie SQLite für Windows 8.1 und Windows Phone 8.1 nicht bereits wie im Lernprogramm [Erste Schritte mit Offlinedaten] beschrieben installiert haben, installieren Sie beide Laufzeiten.

3. Öffnen Sie in Visual Studio 2013 die Projektmappendatei *mobile-services-samples\TodoOffline\WindowsUniversal\TodoOffline-Universal.sln*. Drücken Sie die Taste **F5**, um das Projekt erneut zu erstellen und auszuführen. Überprüfen Sie, ob die NuGet-Pakete wiederhergestellt wurden und die Verweise korrekt sind.

    >[AZURE.NOTE] Möglicherweise müssen Sie alte Verweise auf die SQLite-Laufzeit löschen und wie im Lernprogramm [Erste Schritte mit Offlinedaten] erwähnt durch den aktualisierten Verweis ersetzen.

4. Geben Sie in der App Text unter **Insert a TodoItem** (TodoItem einfügen) ein, und klicken Sie auf **Save** (Speichern), um todoitems zum lokalen Store hinzuzufügen. Schließen Sie dann die App.

Beachten Sie, dass noch keine Verbindung zwischen der App und einem mobilen Dienst besteht, daher erzeugen die Schaltflächen **Push** und **Pull** Ausnahmefehler.




## <a name="test-app"></a>Testen der App mit dem mobilen Dienst

Testen Sie jetzt die App mit Mobile Services.

1. Suchen Sie im Azure-Verwaltungsportal den Anwendungsschlüssel Ihres mobilen Dienstes, indem Sie in der Befehlsleiste in der Registerkarte **Dashboard** auf **Schlüssel verwalten** klicken. Kopieren Sie den **Anwendungsschlüssel**.

2. Öffnen Sie im Projektmappen-Explorer von Visual Studio im Client-Beispielprojekt die Datei "App.xaml.cs". Ändern Sie die Initialisierung von **MobileServiceClient** so, dass die URL und der Anwendungsschlüssel Ihres mobilen Dienstes verwendet werden:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3. Drücken Sie in Visual Studio die Taste**F5**, um die App erneut zu erstellen und auszuführen.

    ![][0]


## <a name="handle-conflict"></a>Aktualisieren der Daten im Backend zum Erzeugen eines Konflikts

In einem realen Szenario würde ein Synchronisierungskonflikt auftreten, wenn eine App Aktualisierungen per Push-Vorgang auf einen Datensatz in der Datenbank überträgt und dann eine andere App versucht, eine Aktualisierung per Push-Vorgang auf denselben Datensatz zu übertragen, jedoch basierend auf einem veralteten Versionsfeld dieses Datensatzes. Wie Sie in [Erste Schritte mit Offlinedaten] gesehen haben, ist die Version-Systemeigenschaft zur Unterstützung der Offlinesynchronisierungsfunktionen erforderlich. Diese Versionsinformationen werden bei jeder Datenbankaktualisierung überprüft. Wenn eine Instanz der App versucht, einen Datensatz mit einer veralteten Version zu aktualisieren, tritt ein Konflikt auf, der als `MobileServicePreconditionFailedException` in der App erfasst wird. Wenn die App die `MobileServicePreconditionFailedException` nicht erfasst, wird eine `MobileServicePushFailedException` ausgelöst, die die Anzahl der gefundenen Synchronisierungsfehler beschreibt.

>[AZURE.NOTE] Um die Synchronisierung der gelöschten Datensätze mit der Offlinedatensynchronisierung zu unterstützen, sollten Sie [Vorläufiges Löschen](/de-de/documentation/articles/mobile-services-using-soft-delete/) aktivieren. Andernfalls müssen Sie manuell Einträge aus dem lokalen Speicher entfernen oder`IMobileServiceSyncTable::PurgeAsync()` aufrufen, um den lokalen Speicher zu löschen.


In den folgenden Schritten wird gezeigt, wie die Windows Phone 8.1- und Windows Store 8.1-Clients gleichzeitig ausgeführt werden, um einen Konflikt auszulösen und anhand des Beispiels zu lösen.

1. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Windows Phone 8.1-Projekt, und klicken Sie auf **Als Startprojekt festlegen**. Drücken Sie dann die Tasten **Ctrl+F5**, um den Windows Phone 8.1-Client ohne Debuggen auszuführen. Sobald der Windows Phone 8.1-Client eingerichtet ist und im Emulator ausgeführt wird, klicken Sie auf die Schaltfläche **Pull**, um den lokalen Store mit dem aktuellen Status der Datenbank zu synchronisieren.
 
    ![][3]
 
   
2. Klicken Sie in Visual Studio mit der rechten Maustaste auf das Windows 8.1-Laufzeitprojekt, und klicken Sie auf **Als Startprojekt festlegen**, um es wieder als Startprojekt festzulegen. Drücken Sie dann **F5**, um es auszuführen. Sobald der Windows Store 8.1-Client eingerichtet ist und ausgeführt wird, klicken Sie auf die Schaltfläche **Pull**, um den lokalen Store mit dem aktuellen Status der Datenbank zu synchronisieren.

    ![][4]
 
3. An diesem Punkt werden beide Clients mit der Datenbank synchronisiert. Die Codes für beide Clients verwenden zudem inkrementelle Synchronisierung, sodass nur nicht abgeschlossene todo-Elemente synchronisiert werden. Abgeschlossene todo-Elemente werden ignoriert. Wählen Sie eines der Elemente aus, und ändern Sie den Text des gleichen Elements in beiden Clients zu unterschiedlichen Werten. Klicken Sie auf die Schaltfläche **Push**, um beide Änderungen mit der Datenbank auf dem Server zu synchronisieren.

    ![][5]

    ![][6]


4. Der Client, für den der Push-Vorgang ausgeführt wurde, findet zuletzt den Konflikt und lässt den Benutzer entscheiden, welcher Wert in die Datenbank übertragen werden soll. Die Ausnahme bietet den richtigen Versionswert, der für die Auflösung des Konflikts verwendet wird.

    ![][7]



## Prüfen des Codes zur Behebung von Synchronisierungskonflikten

Um die Offlinefunktionen für Mobile Services verwenden zu können, müssen Sie die Versionsspalte in die lokale Datenbank und das Datenübertragungsobjekt aufnehmen. Dies geschieht durch Aktualisieren des folgenden Elements der Klasse "TodoItem":

        [Version]
        public string Version { get; set; }

Die Spalte `__version` wird in der `OnNavigatedTo()`-Methode in die lokale Datenbank aufgenommen, wenn die `TodoItem`-Klasse zur Festlegung des lokalen Store verwendet wird.

Erstellen Sie eine Klasse, die `IMobileServiceSyncHandler` implementiert, um Offlinesynchronisierungskonflikte in Ihrem Code zu beheben. Übergeben Sie ein Objekt dieses Typs im Aufruf an `MobileServiceClient.SyncContext.InitializeAsync()`. Dies geschieht auch in der `OnNavigatedTo()`-Methode des Beispiels.

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

Die Klasse `SyncHandler` in **SyncHandler.cs** implementiert `IMobileServiceSyncHandler`. Die Methode `ExecuteTableOperationAsync` wird aufgerufen, wenn die Push-Vorgänge an den Server gesendet werden. Wenn ein Ausnahmefehler des Typs `MobileServicePreconditionFailedException` ausgegeben wird, bedeutet dies, dass ein Konflikt zwischen der lokalen und der Remote-Version eines Elements besteht.

Um Konflikte zugunsten des lokalen Elements zu lösen, führen Sie den Vorgang einfach erneut aus. Wenn ein Konflikt aufgetreten ist, wird die Version des lokalen Elements so aktualisiert, dass sie mit der Serverversion übereinstimmt. Wenn Sie den Vorgang erneut ausführen, werden also die Serveränderungen mit den lokalen Änderungen überschrieben:

    await operation.ExecuteAsync(); 

Um Konflikte zugunsten des Serverelements zu lösen, verlassen Sie einfach `ExecuteTableOperationAsync`. Die lokale Version des Objekts wird verworfen und durch den Wert vom Server ersetzt.

Um den Push-Vorgang zu beenden (die Änderungen in der Warteschlange jedoch zu erhalten), verwenden Sie die Methode `AbortPush()`:

    operation.AbortPush();

Dadurch wird der aktuelle Push-Vorgang beendet. Es werden jedoch alle ausstehenden Änderungen beibehalten, einschließlich des aktuellen Vorgangs, wenn `AbortPush` von `ExecuteTableOperationAsync` aufgerufen wird. Wenn `PushAsync()` das nächste Mal aufgerufen wird, werden diese Änderungen an den Server gesendet. 

Wenn ein Push-Vorgang abgebrochen wird, gibt `PushAsync` eine `MobileServicePushFailedException` aus, und die Ausnahmeeigenschaft `PushResult.Status` hat den Wert `MobileServicePushStatus.CancelledByOperation`. 


<!-- Anchors. -->
[Herunterladen des Windows Store-App-Projekts]: #download-app
[Erstellen des mobilen Dienstes]: #create-service
[Hinzufügen einer Spalte mit Fälligkeitsdatum zur Datenbank]: #add-column
[Aktualisieren der Datenbank für mobile Dienste mit .NET-Back-End]: #dotnet-backend  
[Aktualisieren der Datenbank für mobile Dienste mit JavaScript]: #javascript-backend
[Testen der App mit einem mobilen Dienst]: #test-app
[Manuelles Aktualisieren der Daten im Backend zum Erzeugen eines Konflikts]: #handle-conflict
[Nächste Schritte]:#next-steps

<!-- Images -->
[0]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/javascript-backend-database.png
[2]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/dotnet-backend-database.png
[3]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/wp81-view.png
[4]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/win81-view.png
[5]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/wp81-edit-text.png
[6]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/win81-edit-text.png
[7]: ./media/mobile-services-windows-store-dotnet-handling-conflicts-offline-data/conflict.png




<!-- URLs -->
[Codebeispiel für das Behandeln von Konflikten]: http://go.microsoft.com/fwlink/?LinkId=394787
[Erste Schritte mit Mobile Services]: /de-de/documentation/articles/mobile-services-windows-store-get-started/
[Erste Schritte mit Offlinedaten]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-get-started-offline-data
[SQLite für Windows 8.1]: http://go.microsoft.com/fwlink/?LinkId=394776
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Behandeln von Datenbankkonflikten]: /de-de/documentation/articles/mobile-services-windows-store-dotnet-handle-database-conflicts/#test-app
[Mobile Services-Github-Beispielrepository]: http://go.microsoft.com/fwlink/?LinkId=512865
[Todo Offline-Beispiel für Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=512866
