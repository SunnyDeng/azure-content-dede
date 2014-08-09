<properties linkid="develop-mobile-tutorials-get-started-with-data-xamarin-ios" urlDisplayName="Get Started with Data" pageTitle="Get started with data (Xamarin.iOS) - Azure Mobile Services" metaKeywords="Azure Xamarin.iOS data, Azure mobile services data, " description="Learn how to store and access data from your Azure Mobile Services Xamarin.iOS app." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Get started with data in Mobile Services" authors="" />

Erste Schritte mit Daten in Mobile Services
===========================================

[Windows Store C\#](/de-de/develop/mobile/tutorials/get-started-with-data-dotnet "Windows Store C#")[Windows Store JavaScript](/de-de/develop/mobile/tutorials/get-started-with-data-js "Windows Store JavaScript")[Windows Phone](/de-de/develop/mobile/tutorials/get-started-with-data-wp8 "Windows Phone")[iOS](/de-de/develop/mobile/tutorials/get-started-with-data-ios "iOS")[Android](/de-de/develop/mobile/tutorials/get-started-with-data-android "Android")[HTML](/de-de/develop/mobile/tutorials/get-started-with-data-html "HTML")[Xamarin.iOS](/de-de/develop/mobile/tutorials/get-started-with-data-xamarin-ios "Xamarin.iOS")[Xamarin.Android](/de-de/develop/mobile/tutorials/get-started-with-data-xamarin-android "Xamarin.Android")

Dieses Thema beschreibt den Einsatz von Azure Mobile Services für die Nutzung von Daten in Xamarin.iOS-Apps. In diesem Lernprogramm laden Sie eine App herunter, die Daten im Speicher ablegt, einen neuen mobilen Dienst erstellt, den mobilen Dienst mit der App integriert und melden sich anschließend im Azure-Verwaltungsportal an, um die Änderungen an den Daten beim Ausführen der App anzuzeigen.

**Hinweis**

Dieses Lernprogramm vermittelt ein besseres Verständnis davon, wie Sie mit Mobile Services in Azure Daten aus einer Xamarin.iOS-App speichern und abrufen können. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart für mobile Dienste abgenommen werden. Falls Sie noch keine Erfahrung mit mobilen Diensten haben, sollten Sie eventuell zuerst das Lernprogramm [Erste Schritte mit mobilen Diensten](/de-de/develop/mobile/tutorials/get-started-xamarin-ios) abschließen.

In diesem Lernprogramm werden die folgenden grundlegenden Schritte behandelt:

1.  [Herunterladen des Xamarin.iOS-App-Projekts](http://go.microsoft.com/fwlink/p/?LinkId=331302)
2.  [Erstellen des mobilen Diensts](#create-service)
3.  [Erstellen einer Datentabelle als Datenspeicher](#add-table)
4.  [Aktualisieren der App zur Verwendung von mobilen Diensten](#update-app)
5.  [Testen der App mit Mobile Services](#test-app)

Dieses Lernprogramm benötigt [Azure Mobile Services-Komponente](http://components.xamarin.com/view/azure-mobile-services/), [XCode 5.0](https://go.microsoft.com/fwLink/p/?LinkID=266532), [Xamarin.iOS] und iOS 5.0 oder eine neuere Version.

**Hinweis**

Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion](http://www.windowsazure.com/de-de/pricing/free-trial/?WT.mc_id=A643EE910&returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fen-us%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F).

Herunterladen des ProjektsHerunterladen des GetStartedWithData-Projekts
-----------------------------------------------------------------------

Dieses Lernprogramm verwendet die Xamarin.iOS-App [GetStartedWithData](http://go.microsoft.com/fwlink/p/?LinkId=331302). Die Benutzeroberfläche dieser App und die vom Mobile Services-Xamarin.iOS-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1.  Laden Sie die [GetStartedWithData](http://go.microsoft.com/fwlink/p/?LinkId=331302)-Beispiel-App herunter.

2.  Öffnen Sie das heruntergeladene Projekt in Xamarin Studio, und sehen Sie sich die Klasse **TodoService** an.

   	Beachten Sie die **// TODO** -Kommentare. Diese geben die von Ihnen durchzuführenden Schritte an, damit diese App mit dem mobilen Dienst funktionieren kann.

3.  Klicken Sie im Menü **Run** auf **Start Without Debugging**, um die App zu starten.

4.  Geben Sie in der App einen Text in das Textfeld ein. Klicken Sie anschließend auf die Schaltfläche **+**.

   	![][0]  

   	Beachten Sie, dass der gespeicherte Text in der unteren Liste angezeigt wird.

Erstellen des mobilen DienstesErstellen eines neuen mobilen Dienstes im Verwaltungsportal
-----------------------------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

Erstellen einer neuen TabelleFügen Sie eine neue Tabelle zum mobilen Dienst hinzu
---------------------------------------------------------------------------------

Um App-Daten im neuen mobilen Dienst speichern zu können, müssen Sie zuerst eine neue Tabelle in der verknüpften SQL-Datenbankinstanz erstellen.

1.  Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2.  Klicken Sie auf die Registerkarte **Daten** und anschließend auf **+Erstellen**.

   	![][5]

   	Daraufhin wird der Dialog **Neue Tabelle erstellen** geöffnet.

3.  Geben Sie unter **Tabellenname** den Namen *TodoItem* ein und klicken Sie auf das Kontrollkästchen.

   	![](./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png)

   	Daraufhin wird eine neue Speichertabelle **TodoItem** mit den Standardberechtigungen erstellt, d. h. jeder Benutzer der App kann Daten in der Tabelle abrufen und ändern.

    <div class="dev-callout"> 
    <b>Hinweis</b> 
    <p>Der Schnellstart für mobile Dienste verwendet dieselbe Tabelle. Jede Tabelle wird jedoch in einem Schema erstellt, das für einen bestimmten mobilen Dienst gilt. Auf diese Weise werden Datenkollisionen verhindert, wenn mehrere mobile Dienste dieselbe Datenbank verwenden.</p> 
    </div>

1.  Klicken Sie auf die neue Tabelle **TodoItem** und überprüfen Sie, dass keine Datenzeilen vorhanden sind.

2.  Klicken Sie auf die Registerkarte **Columns** und vergewissern Sie sich, dass diese nur eine einzige **id**-Spalte enthält, die automatisch erstellt wurde.

    Dies ist die Mindestanforderung für eine Tabelle in Mobile Services.

    **Hinweis**

    Wenn für Ihren mobilen Dienst das dynamische Schema aktiviert ist, werden neue Spalten immer dann automatisch erstellt, wenn JSON-Objekte über einen Einfüge- oder Aktualisierungsvorgang an den mobilen Dienst gesendet werden.

Sie können den neuen mobilen Dienst nun als Datenspeicher für die App verwenden.

Aktualisieren der AppAktualisieren der App für den Datenzugriff über mobile Dienste
-----------------------------------------------------------------------------------

Ihr mobiler Dienst ist nun bereit und Sie können die App aktualisieren, sodass diese Elemente im mobilen Dienst anstatt in einer lokalen Sammlung speichert.

1.  Falls Ihr Components-Ordner noch keinen **Azure Mobile Service** enthält, können Sie mit der rechten Maustaste auf **Components** klicken, **Get More Components** auswählen und nach **Azure Mobile Services** suchen.

2.  Öffnen Sie in der Lösungsansicht in Xamarin Studio die Klasse **TodoService** und entfernen Sie den Kommentar vor der folgenden `using`-Anweisung:

         using Microsoft.WindowsAzure.MobileServices;

3.  Klicken Sie im Verwaltungsportal auf **Mobile Dienste** und klicken Sie auf den zuvor erstellten mobilen Dienst.

4.  Klicken Sie die Registerkarte **Dashboard** und notieren Sie die **Site-URL**. Klicken Sie anschließend auf **Schlüssel verwalten** und notieren Sie den **Anwendungsschlüssel**.

   	![][8]

5.  Entfernen Sie die Kommentare der folgenden Konstanten in der Klasse **Constants**:

         public const string ApplicationURL = @"AppUrl";
         public const string ApplicationKey = @"AppKey";

6.  Ersetzen Sie **AppUrl** und **AppKey** in den obigen Konstanten durch die Werte aus dem Verwaltungsportal (siehe oben).

7.  Entfernen Sie in der Klasse **TodoService** den Kommentar vor der folgenden Codezeile:

         private MobileServiceClient client;

   	Dadurch wird eine Eigenschaft erstellt, die den MobileServiceClient repräsentiert, welcher eine Verbindung zu dem Dienst herstellt

8.  Entfernen Sie in der Klasse **TodoService** den Kommentar vor der folgenden Codezeile:

         private IMobileServiceTable<TodoItem> todoTable;  

   	Dadurch wird eine Eigenschaftsdarstellung für die Tabelle der mobilen Dienste erstellt.

9.  Entfernen Sie den Kommentar in den folgenden Zeilen im **TodoService**-Konstruktor:

         // TODO:: Kommentar entfernen, um Mobile Services zu verwenden
         client = new MobileServiceClient(Constants.ApplicationURL, Constants.ApplicationKey).WithFilter(this); 
         todoTable = client.GetTable<TodoItem>(); 

    Hierdurch wird eine Instanz des Mobile Services-Client sowie eine Instanz der TodoItem-Tabelle erstellt.

10. Entfernen Sie den Kommentar in den folgenden Zeilen in der **RefreshDataAsync**-Methode in **TodoService**

        // TODO:: Kommentar entfernen, um Mobile Services zu verwenden

    try
        {
			// Dieser Code aktualisiert die Einträge in der Listenansicht, indem die TodoItems-Tabelle abgefragt wird.
			// Die Abfrage schließt abgeschlossene TodoItems heraus
			Items = await todoTable
				.Where (todoItem => todoItem.Complete == false).ToListAsync();
			} catch (MobileServiceInvalidOperationException e)
			{
				Console.Error.WriteLine (@"ERROR {0}", e.Message);
				return null;
			}

    Hierdurch wird eine Abfrage zur Rückgabe sämtlicher Aufgaben gestellt, die noch nicht abgeschlossen wurden.

11. Suchen Sie die Methode **InsertTodoItemAsync** und entfernen Sie den Kommentar in der folgenden Zeile:

        await todoTable.InsertAsync(todoItem);

    Dieser Code sendet eine Einfügeanforderung an den mobilen Dienst.

12. Suchen Sie die Methode **CompleteTodoItemAsync** und entfernen Sie den Kommentar in der folgenden Zeile:

        await todoTable.UpdateAsync(item);

   	Durch diesen Code werden die TodoItems entfernt, nachdem diese als abgeschlossen markiert sind.

Nachdem nun die App für die Verwendung von Mobile Services als Back-End-Speicher aktualisiert ist, können Sie die App mit Mobile Services testen.

Testen der AppTesten der App mit Ihrem neuen mobilen Dienst
-----------------------------------------------------------

1.  Wählen Sie in Xamarin Studio einen Emulator oder ein Gerät für die Bereitstellung aus einem der Combofelder aus und klicken Sie im Menü **Run** auf **Start WithoutDebugging**, um die App zu starten.

   	Hierdurch wird der Azure Mobile Services-Client ausgeführt und mit Xamarin.iOS erstellt, welches Einträge vom mobilen Dienst abfragt.

2.  Geben Sie wie zuvor Text im Textfeld ein, und klicken Sie danach auf die Schaltfläche **+**.

   	Dieser Code schickt ein neues einzufügendes Element an den mobilen Dienst.

3.  Klicken Sie im [Verwaltungsportal](https://manage.windowsazure.com/) auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4.  Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

   	![][9]
  
   	Die **TodoItem**-Tabelle enthält nun Daten, deren ID-Werte vom mobilen Dienst generiert wurden, und die entsprechenden Spalten für die TodoItem-Klasse in der App wurden automatisch in der Tabelle erstellt.

Damit ist das Lernprogramm **Erste Schritte mit Daten** für Xamarin.iOS beendet.

Abgeschlossenes Beispiel abrufen
--------------------------------

Laden Sie das [abgeschlossene Beispielprojekt](http://go.microsoft.com/fwlink/p/?LinkId=331302) herunter. Stellen Sie sicher, dass Sie die Variablen **applicationURL** und **applicationKey** mit Ihren eigenen Azure-Einstellungen aktualisieren.

Nächste Schritte
----------------

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer iOS-App für die Arbeit mit Daten in Mobile Services aufgezeigt.

Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

-   [Prüfen und Ändern von Daten mit Skripten](/de-de/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios)
  <br/>Informationen zur Verwendung von Serverskripts in mobilen Diensten, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

-   [Optimieren von Abfragen mittels Paging](/de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-ios)
  <br/>Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie die folgenden weiteren iOS-Lernprogramme ausprobieren:

-   [Erste Schritte mit der Authentifizierung](/de-de/develop/mobile/tutorials/get-started-with-users-xamarin-ios)
  <br/>Informationen zur Authentifizierung von Benutzern Ihrer App.

-   [Erste Schritte mit Pushbenachrichtigungen](/de-de/develop/mobile/tutorials/get-started-with-push-xamarin-ios)
  <br/>Verschicken Sie mithilfe Ihres mobilen Dienstes eine einfache Pushbenachrichtigung an Ihre App.


<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png




[5]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png
