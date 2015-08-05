<properties 
	pageTitle="Behandeln von Konflikten mit Offlinedaten in Mobile Services (Windows Phone) | Mobile Dev Center" 
	description="Erfahren Sie, wie Sie mithilfe von Azure Mobile Services Konflikte beim Synchronisieren von Offlinedaten in Ihrer Windows Phone-Anwendung behandeln." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	manager="dwrede" 
	editor="" 
	services="mobile-services"/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/15/2015" 
	ms.author="wesmc"/>


# Behandeln von Konflikten bei der Synchronisierung von Offlinedaten in Mobile Services

[AZURE.INCLUDE [mobile-services-selector-offline-conflicts](../../includes/mobile-services-selector-offline-conflicts.md)]

##Übersicht

In diesem Thema erfahren Sie, wie Sie Daten synchronisieren und Konflikte behandeln können, wenn Sie die Offlinefunktionen von Azure Mobile Services verwenden. In diesem Lernprogramm laden Sie eine App herunter, die Offline- und Onlinedaten unterstützt, integrieren den mobilen Dienst mit der App und melden sich anschließend im Azure-Verwaltungsportal an, um die Datenbank beim Ausführen der App anzuzeigen und zu aktualisieren.

Dieses Lernprogramm basiert auf den Schritten und der Beispiel-App aus dem vorherigen Lernprogramm [Erste Schritte mit Offlinedaten]. Bevor Sie mit diesem Lernprogramm beginnen, müssen Sie zunächst [Erste Schritte mit Offlinedaten] abschließen.


##Voraussetzungen

Dieses Lernprogramm erfordert Visual Studio 2012 und das [Windows Phone 8 SDK].


##Herunterladen des Beispielprojekts



Dieses Lernprogramm baut auf dem [Handling conflicts code sample] (Codebeispiel zur Behandlung von Konflikten, in englischer Sprache) auf. Dabei handelt es sich um ein Windows Phone 8-Projekt für Visual Studio 2012. Die Benutzeroberfläche für diese App ähnelt der App im Lernprogramm [Erste Schritte mit Offlinedaten]. Der Unterschied besteht darin, dass für jedes TodoItem eine neue Datumsspalte vorhanden ist.

![][0]


1. Laden Sie die Windows Phone-Version des [Codebeispiels zur Behandlung von Konflikten] herunter. 

2. Installieren Sie [SQLite für Windows Phone 8], falls Sie dies noch nicht getan haben.

3. Öffnen Sie in Visual Studio 2012 das heruntergeladene Projekt. Fügen Sie einen Verweis auf **SQLite für Windows** unter **Windows Phone** > **Erweiterungen** hinzu.

4. Drücken Sie in Visual Studio 2012 die Taste **F5**, um die App zu erstellen und im Debugger auszuführen.
 
5. Geben Sie in der App Text für einige neue TodoItems ein und klicken Sie auf **Speichern**, um diese zu speichern. Sie können auch das Fälligkeitsdatum der todo-Elemente ändern, die Sie hinzufügen.


Beachten Sie, dass noch keine Verbindung zwischen der App und einem mobilen Dienst besteht, daher erzeugen die Schaltflächen **Push** und **Pull** Ausnahmefehler.


##Hinzufügen einer Spalte zum Datenmodell

In diesem Abschnitt aktualisieren Sie die Datenbank für Ihren mobilen Dienst so, dass eine TodoItem-Tabelle mit einer Spalte für das Fälligkeitsdatum enthalten ist. In der App können Sie das Fälligkeitsdatum für ein Element in der Laufzeit ändern, sodass Sie in einem späteren Abschnitt diese Lernprogramms Synchronisierungskonflikte generieren können.

Die `TodoItem`-Klasse im Beispiel ist in "MainPage.xaml.cs" definiert. Beachten Sie, dass die Klasse das folgende Attribut besitzt, das die Synchronisierungsvorgänge auf die Tabelle ausrichtet.

        [DataTable("TodoWithDate")]

Aktualisieren Sie die Datenbank so, dass diese Tabelle enthalten ist.

###<a name="dotnet-backend"></a>Aktualisieren der Datenbank für Mobile Services mit .NET-Backend 

Wenn Sie für Ihren mobilen Dienst das .NET-Backend verwenden, führen Sie diese Schritte aus, um das Schema für die Datenbank zu aktualisieren.

1. Öffnen Sie das Projekt für den mobilen Dienst mit .NET-Backend in Visual Studio.
2. Erweitern Sie im Projektmappen-Explorer von Visual Studio in Ihrem Dienstprojekt den Ordner **Models**, und öffnen Sie "ToDoItem.cs". Fügen Sie die `DueDate`-Eigenschaft wie folgt hinzu.

          public class TodoItem : EntityData
          {
            public string Text { get; set; }
            public bool Complete { get; set; }
            public System.DateTime DueDate { get; set; }
          }


3. Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **App_Start**, und öffnen Sie die Datei "WebApiConfig.cs".

    Beachten Sie in der Datei "WebApiConfig.cs", dass die Standard-Datenbankinitialisiererklasse von der `DropCreateDatabaseIfModelChanges`-Klasse abgeleitet wird. Dies bedeutet, dass jede Änderung am Modell dazu führt, dass die Tabelle gelöscht und dem neuen Modell entsprechend erneut erstellt wird. Die Daten in der Tabelle gehen also verloren, und es wird ein erneutes Seeding für die Tabelle ausgeführt. Ändern Sie die "Seed"-Methode des Datenbankinitialisierers so, dass die `Seed()`-Initialisierungsfunktion wie folgt aussieht, um die neue "DueDate"-Spalte zu initialisieren. Speichern Sie die Datei "WebApiConfig.cs".

    >[AZURE.NOTE]Bei Verwendung des Standard-Datenbankinitialisierers löscht Entity Framework die Datenbank und erstellt sie erneut, sobald es eine Datenmodelländerung in der Code First-Modelldefinition erkennt. Um eine Datenmodelländerung durchzuführen und bestehende Daten in der Datenbank beizubehalten, müssen Sie Code First-Migrationen verwenden. Weitere Informationen finden Sie unter [Verwenden von Code First-Migrationen zur Aktualisierung des Datenmodells](mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).


        new TodoItem { Id = "1", Text = "First item", Complete = false, DueDate = DateTime.Today },
        new TodoItem { Id = "2", Text = "Second item", Complete = false, DueDate = DateTime.Today },

          

4. Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **Controllers**, und öffnen Sie die Datei "ToDoItemController.cs". Benennen Sie die `TodoItemController`-Klasse in `TodoWithDateController` um. Dadurch ändern Sie den REST-Endpunkt für Tabellenoperationen.

        public class TodoWithDateController : TableController<TodoItem>
    

5. Klicken Sie im Projektmappen-Explorer von Visual Studio mit der rechten Maustaste auf das Projekt für den mobilen Dienst mit .NET-Backend, und klicken Sie auf **Veröffentlichen**, um Ihre Änderungen zu veröffentlichen.


### <a name="javascript-backend"></a>Aktualisieren der Datenbank für Mobile Services mit JavaScript-Back-End

Für mobile Dienste mit JavaScript-Backend fügen Sie eine neue Tabelle mit dem Namen **TodoWithDate** hinzu. Führen Sie die folgenden Schritte aus, um die Tabelle **TodoWithDate** für mobile Dienste mit JavaScript-Backend hinzuzufügen.

  1. Melden Sie sich beim [Azure-Verwaltungsportal] an. 

  2. Navigieren Sie zur Registerkarte **Daten** des mobilen Dienstes.

  3. Klicken Sie unten auf der Seite auf **Erstellen**, und erstellen Sie eine neue Tabelle mit dem Namen **TodoWithDate**.


##Testen der App mit dem mobilen Dienst

Testen Sie jetzt die App mit Mobile Services.

1. Suchen Sie im Azure-Verwaltungsportal den Anwendungsschlüssel Ihres mobilen Dienstes, indem Sie in der Befehlsleiste in der Registerkarte **Dashboard** auf **Schlüssel verwalten** klicken. Kopieren Sie den **Anwendungsschlüssel**.

2. Öffnen Sie im Projektmappen-Explorer von Visual Studio im Client-Beispielprojekt die Datei "App.xaml.cs". Ändern Sie die Initialisierung von **MobileServiceClient** so, dass die URL und der Anwendungsschlüssel Ihres mobilen Dienstes verwendet werden:

         public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://your-mobile-service.azure-mobile.net/",
            "Your AppKey"
        );

3. Drücken Sie in Visual Studio die Taste **F5**, um die App zu erstellen und auszuführen.

4. Geben Sie wie zuvor Text im Textfeld ein, und klicken Sie danach auf die Schaltfläche **Speichern**, um die neuen Elemente zu speichern. So werden die Daten in der lokalen Synchronisierungstabelle, jedoch nicht auf dem Server gespeichert.

    ![][0]

5. Um den aktuellen Status der Datenbank anzuzeigen, melden Sie sich beim [Azure-Verwaltungsportal] an, klicken Sie auf **Mobile Services**. Klicken Sie dann auf Ihren mobilen Dienst.

  * Wenn Sie für Ihren mobilen Dienst das JavaScript-Backend verwenden, klicken Sie auf die Registerkarte **Daten**, und klicken Sie dann auf die Tabelle **TodoWithDate**. Klicken Sie auf **Durchsuchen**, um anzuzeigen, dass die Tabelle immer noch leer ist, da die Änderungen noch nicht per Push-Vorgang von der App auf den Server übertragen wurden.

        ![][1]

  *  Wenn Sie für Ihren mobilen Dienst das .NET-Backend verwenden, klicken Sie auf die Registerkarte **Konfigurieren**, und klicken Sie dann auf die SQL-Datenbank. Klicken Sie unten auf **Verwalten**, um sich beim SQL-Azure-Verwaltungsportal anzumelden, die Datenbank anzuzeigen und die folgende SQL-Abfrage auszuführen.
    
            SELECT * FROM todolist.todowithdate

        ![][2]

   	 

7. Klicken Sie dann in der App auf **Push**.

8. Klicken Sie im Verwaltungsportal in der Tabelle **TodoItem** auf **Aktualisieren**. Jetzt sollten die Daten angezeigt werden, die Sie in der App eingegeben haben.

   	![][3]

9. Lassen Sie **Emulator WVGA 512 MB** für den nächsten Abschnitt laufen. Sie werden die App nun in zwei Emulatoren ausführen, um einen Konflikt zu generieren.

##Aktualisieren der Daten im Backend zum Erzeugen eines Konflikts

In einem realen Szenario würde ein Synchronisierungskonflikt auftreten, wenn eine App Aktualisierungen per Push-Vorgang auf einen Datensatz in der Datenbank überträgt und dann eine andere App versucht, eine Änderung per Push-Vorgang auf denselben Datensatz zu übertragen, jedoch basierend auf einem veralteten Versionsfeld dieses Datensatzes. Wenn eine Instanz der App versucht, denselben Datensatz zu aktualisieren, ohne den aktualisierten Datensatz per Pull-Vorgang abzurufen, tritt ein Konflikt auf. Dieser wird in der App als `MobileServicePreconditionFailedException` erfasst.

In diesem Abschnitt führen Sie zwei Instanzen der App gleichzeitig aus, um einen Konflikt zu generieren.


1. Falls **Emulator WVGA 512 MB** nicht mehr läuft, drücken Sie **STRG+F5**, um den Emulator neu zu starten.

2. Ändern Sie das Ausgabegerät in Visual Studio zu **Emulator WVGA** und führen Sie eine zweite Instanz der App im neuen Emulator aus, indem Sie **F5** drücken.
 
    ![][5]
 
   
3. Klicken Sie in der zweiten Instanz der App auf **Pull**, um den lokalen Speicher mit der Datenbank des mobilen Diensts zu synchronisieren. Beide Instanzen der App sollten nun dieselben Daten enthalten.
 
    ![][6]

4. Markieren Sie in der zweiten Instanz der App das Kontrollkästchen, um eines der Elemente zu vervollständigen und klicken Sie auf **Push**, um Ihre Änderung in die Remotedatenbank zu übertragen. Im folgenden Screenshot wurde **Pick up James** vervollständigt, um anzugeben, dass James bereits abgeholt wurde. Die erste Instanz der App enthält nun einen veralteten Datensatz.

    ![][9]

5. Ändern Sie die Daten des veralteten Datensatzes in der ersten Instanz der App und klicken Sie auf **Push**, um zu versuchen, die Remotedatenbank mit dem veralteten Datensatz zu aktualisieren. Im folgenden Screenshot haben wir eingegeben, dass James am **5.10.2014** abgeholt werden soll.

    ![][7]

6. Wenn Sie auf **Push** klicken, um die Änderung zu übernehmen, wird ein Dialogfeld mit der Meldung angezeigt, dass ein Konflikt erkannt wurde. Sie werden gefragt, wie der Konflikt gelöst werden soll. Wählen Sie eine der beiden Optionen, um dem Konflikt zu lösen.

    Im folgenden Szenario wurde James bereits abgeholt. Daher macht es keinen Sinn mehr, ihn am **10.5.2014** abzuholen. Wenn Sie die Option **Serverversion verwenden** auswählen, wird die erste Instanz der App mit dem geänderten Datensatz vom Server aktualisiert.

    ![][8]

##Prüfen des Codes zur Behebung von Synchronisierungskonflikten

Sie müssen eine Versionsspalte in die lokale Datenbank und das Datenübertragungsobjekt einfügen, damit die Offlinefunktion Konflikte erkennt. Die `TodoItem`-Klasse enthält den folgenden Member:

        [Version]
        public string Version { get; set; }

Die Spalte `__version` ist außerdem in der lokalen Datenbank festgelegt, in der `OnNavigatedTo()`-Methode.

Zum Beheben von Offlinesynchronisierungskonflikten in Ihrem Code erstellen Sie eine Klasse, die `IMobileServiceSyncHandler` implementiert. Übergeben Sie ein Objekt dieses Typs an den Aufruf an `InitializeAsync`:

     await App.MobileService.SyncContext.InitializeAsync(store, new SyncHandler(App.MobileService));

Die `SyncHandler`-Klasse in **MainPage.xaml.cs** implementiert `IMobileServiceSyncHandler`. Die `ExecuteTableOperationAsync`-Methode wird aufgerufen, wenn die Pushvorgänge an den Server gesendet werden. Wenn eine Ausnahme vom Typ `MobileServicePreconditionFailedException` ausgelöst wird, bedeutet dies, dass ein Konflikt zwischen der lokalen und der Remoteversion eines Elements besteht.

Um Konflikte zugunsten des lokalen Elements zu lösen, führen Sie den Vorgang einfach erneut aus. Wenn ein Konflikt aufgetreten ist, wird die Version des lokalen Elements so aktualisiert, dass sie mit der Serverversion übereinstimmt. Wenn Sie den Vorgang erneut ausführen, werden also die Serveränderungen mit den lokalen Änderungen überschrieben:

    await operation.ExecuteAsync(); 

Um Konflikte zugunsten des Serverelements zu lösen, verlassen Sie einfach `ExecuteTableOperationAsync`. Die lokale Version des Objekts wird verworfen und durch den Wert vom Server ersetzt.

Um den Pushvorgang zu beenden (die Änderungen in der Warteschlange jedoch zu erhalten), verwenden Sie die `AbortPush()`-Methode:

    operation.AbortPush();

Dadurch wird der aktuelle Pushvorgang beendet. Es werden jedoch alle ausstehenden Änderungen beibehalten, einschließlich des aktuellen Vorgangs, wenn `AbortPush` von `ExecuteTableOperationAsync` aufgerufen wird. Wenn `PushAsync()` das nächste Mal aufgerufen wird, werden diese Änderungen an den Server gesendet.

Wenn ein Pushvorgang abgebrochen wird, löst `PushAsync` eine `MobileServicePushFailedException` aus, und die Ausnahmeeigenschaft `PushResult.Status` hat den Wert `MobileServicePushStatus.CancelledByOperation`.




<!-- Images -->
[0]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-handling-conflicts-app-run1.png
[1]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-empty.png
[2]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-empty-sql.png
[3]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/mobile-services-todowithdate-push1.png
[5]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/vs-emulator-wvga.png
[6]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-synced.png
[7]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-date-change.png
[8]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-conflict-detected.png
[9]: ./media/mobile-services-windows-phone-handling-conflicts-offline-data/two-emulators-item-completed.png



<!-- URLs -->
[Handling conflicts code sample]: http://go.microsoft.com/fwlink/?LinkId=398257
[Codebeispiels zur Behandlung von Konflikten]: http://go.microsoft.com/fwlink/?LinkId=398257
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md
[Erste Schritte mit Offlinedaten]: mobile-services-windows-phone-get-started-offline-data.md
[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Windows Phone 8 SDK]: http://go.microsoft.com/fwlink/p/?linkid=268374
[SQLite für Windows Phone 8]: http://go.microsoft.com/fwlink/?LinkId=397953
[Get started with data]: mobile-services-windows-phone-get-started-data.md
 

<!---HONumber=July15_HO4-->