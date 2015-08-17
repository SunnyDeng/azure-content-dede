<properties
	pageTitle="Hinzufügen von Mobile Services zu einer vorhandenen Anwendung (Xamarin.iOS) – Azure Mobile Services"
	description="Erfahren Sie, wie Sie Daten von Ihrer Azure Mobile Services Xamarin.iOS-App speichern und darauf zugreifen."
	documentationCenter="xamarin"
	authors="ggailey777"
	manager="dwrede"
	services="mobile-services"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-xamarin-ios"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="04/24/2015"
	ms.author="ggailey777"/>

# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

Dieses Thema beschreibt den Einsatz von Azure Mobile Services für die Nutzung von Daten in Xamarin.iOS-Apps. In diesem Lernprogramm laden Sie eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.

> [AZURE.NOTE]Dieses Lernprogramm vermittelt ein besseres Verständnis davon, wie Sie mit Mobile Services in Azure Daten aus einer Xamarin.iOS-App speichern und abrufen können. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart für mobile Dienste abgenommen werden. Falls Sie noch keine Erfahrung mit mobilen Diensten haben, sollten Sie eventuell zuerst das Lernprogramm [Erste Schritte mit mobilen Diensten](/develop/mobile/tutorials/get-started-xamarin-ios) abschließen.

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Herunterladen des Xamarin.iOS-App-Projekts][GitHub]
2. [Erstellen des mobilen Diensts]
3. [Hinzufügen von Datentabellen als Datenspeicher]
4. [Aktualisieren von Apps zur Verwendung von Mobile Services]
5. [Testen von Apps mit Mobile Services]

Dieses Lernprogramm benötigt die [Azure Mobile Services]-Komponente, [XCode 6.0][Install Xcode], [Xamarin.iOS] und iOS 7.0 oder eine neuere Version.

> [AZURE.IMPORTANT]Um dieses Lernprogramm abzuschließen, benötigen Sie ein Azure-Konto. Wenn Sie über kein Konto verfügen, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Ausführliche Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F"%20target="_blank).

## <a name="download-app"></a>Herunterladen des GetStartedWithData-Projekts

Dieses Lernprogramm verwendet die Xamarin.iOS-App [GetStartedWithData][GitHub]. Die Benutzeroberfläche dieser App und die vom Mobile Services-Xamarin.iOS-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden.

1. Laden Sie die [GetStartedWithData][GitHub]-Beispiel-App herunter.

2. Öffnen Sie das heruntergeladene Projekt in Xamarin Studio, und sehen Sie sich die Klasse **TodoService** an.

   	Beachten Sie, dass mehrere **// TODO**-Kommentare vorhanden sind. Diese geben die von Ihnen durchzuführenden Schritte an, damit diese App mit dem mobilen Dienst funktionieren kann.

3. Klicken Sie im Menü **Run** auf **Start Without Debugging**, um die App zu starten.

4. Geben Sie in der App einen Text in das Textfeld ein. Klicken Sie anschließend auf die Schaltfläche **+**.

   	![][0]

   	Beachten Sie, dass der gespeicherte Text in der unteren Liste angezeigt wird.

## <a name="create-service"></a>Erstellen neuer mobiler Dienste im Verwaltungsportal

[AZURE.INCLUDE [mobile-services-create-new-service-data](../../includes/mobile-services-create-new-service-data.md)]

## <a name="add-table"></a>Hinzufügen neuer Tabellen zum mobilen Dienst

Um App-Daten im neuen mobilen Dienst speichern zu können, müssen Sie zuerst eine neue Tabelle in der verknüpften SQL-Datenbankinstanz erstellen.

1. Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie auf die Registerkarte **Daten** und dann auf **+Erstellen**.

   	![][5]

   	Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

3. Geben Sie unter **Tabellenname** den Namen _TodoItem_ ein, und klicken Sie auf den Häkchenknopf.

  	![][6]

  	Daraufhin wird eine neue Speichertabelle **TodoItem** mit den Standardberechtigungen erstellt, d. h. jeder Benutzer der App kann Daten in der Tabelle abrufen und ändern.

    > [AZURE.NOTE]Der gleiche Tabellenname wird in Mobile Services-Quickstart verwendet. Jede Tabelle wird jedoch in einem Schema erstellt, das für einen bestimmten mobilen Dienst gilt. Dadurch soll verhindert werden, dass es zu Datenkollisionen kommt, wenn mehrere mobile Dienste dieselbe Datenbank verwenden.

4. Klicken Sie auf die neue Tabelle **TodoItem** und überprüfen Sie, dass keine Datenzeilen vorhanden sind.

5. Klicken Sie auf die Registerkarte **Columns** und vergewissern Sie sich, dass diese nur eine einzige **id**-Spalte enthält, die automatisch erstellt wurde.

	  Dies ist die Mindestanforderung für eine Tabelle in Mobile Services.

    > [AZURE.NOTE]Wenn für Ihren mobilen Dienst das dynamische Schema aktiviert ist, werden neue Spalten immer dann automatisch erstellt, wenn JSON-Objekte über einen Einfüge- oder Aktualisierungsvorgang an den mobilen Dienst gesendet werden.

Sie können den neuen mobilen Dienst nun als Datenspeicher für die App verwenden.

## <a name="update-app"></a>Aktualisieren der App für den Datenzugriff über mobile Dienste

Ihr mobiler Dienst ist nun bereit, und Sie können die App aktualisieren, sodass Elemente in Mobile Services anstatt in einer lokalen Sammlung gespeichert werden.

1. Falls Ihr Components-Ordner noch keinen **Azure Mobile Service** enthält, können Sie mit der rechten Maustaste auf **Components** klicken, **Get More Components** auswählen und nach **Azure Mobile Services** suchen.

2. Öffnen Sie in der Projektmappenansicht in Xamarin Studio die **TodoService**-Klasse, und heben Sie die Auskommentierung der folgenden `using`-Anweisung auf:

        using Microsoft.WindowsAzure.MobileServices;

3. Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

4. Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich die **Site URL**. Klicken Sie anschließend auf **Schlüssel verwalten**, und notieren Sie sich den **Anwendungsschlüssel**.

   	![][8]

5. Entfernen Sie die Kommentare der folgenden Konstanten in der Klasse **Constants**:

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";

6. Ersetzen Sie **AppUrl** und **AppKey** in den obigen Konstanten durch die Werte aus dem Verwaltungsportal (siehe oben).

7. Entfernen Sie in der Klasse **TodoService** den Kommentar vor der folgenden Codezeile:

        private MobileServiceClient client;

   	Dadurch wird eine Eigenschaft erstellt, die den MobileServiceClient repräsentiert, welcher eine Verbindung zu dem Dienst herstellt.

8. Entfernen Sie in der Klasse **TodoService** den Kommentar vor der folgenden Codezeile:

        private IMobileServiceTable<TodoItem> todoTable;  

   	Dadurch wird eine Eigenschaftsdarstellung für die Tabelle der mobilen Dienste erstellt.

9. Entfernen Sie den Kommentar in den folgenden Zeilen im **TodoService**-Konstruktor:

		// TODO:: Uncomment these lines to use Mobile Services
		client = new MobileServiceClient(Constants.ApplicationURL, Constants.ApplicationKey).WithFilter(this);
		todoTable = client.GetTable<TodoItem>();

    Hierdurch wird eine Instanz des Mobile Services-Client sowie eine Instanz der TodoItem-Tabelle erstellt.

10. Entfernen Sie den Kommentar in den folgenden Zeilen in der **RefreshDataAsync**-Methode in **TodoService**

			// TODO:: Uncomment these lines to use Mobile Services
			try
	    {
				// This code refreshes the entries in the list view by querying the TodoItems table.
				// The query excludes completed TodoItems
				Items = await todoTable
					.Where (todoItem => todoItem.Complete == false).ToListAsync();
			}
	    catch (MobileServiceInvalidOperationException e)
	    {
				Console.Error.WriteLine (@"ERROR {0}", e.Message);
				return null;
			}

    Hierdurch wird eine Abfrage zur Rückgabe sämtlicher Aufgaben gestellt, die noch nicht abgeschlossen wurden.

11. Suchen Sie die Methode **InsertTodoItemAsync** und entfernen Sie den Kommentar in der folgenden Zeile:

		await todoTable.InsertAsync(todoItem);

    Dieser Code sendet eine Einfügeanforderung an den mobilen Dienst.

13. Suchen Sie die Methode **CompleteTodoItemAsync** und entfernen Sie den Kommentar in der folgenden Zeile:

		await todoTable.UpdateAsync(item);

   	Durch diesen Code werden die TodoItems entfernt, nachdem diese als abgeschlossen markiert sind.

Nachdem nun die App für die Verwendung von Mobile Services als Back-End-Speicher aktualisiert ist, können Sie die App mit Mobile Services testen.

## <a name="test-app"></a>Testen der App mit dem neuen mobilen Dienst

1. Wählen Sie in Xamarin Studio einen Emulator oder ein Gerät für die Bereitstellung aus einem der Combofelder aus und klicken Sie im Menü **Run** auf **Start WithoutDebugging**, um die App zu starten.

   	Hierdurch wird der Azure Mobile Services-Client ausgeführt und mit Xamarin.iOS erstellt, welches Einträge vom mobilen Dienst abfragt.

2. Geben Sie wie zuvor Text im Textfeld ein, und klicken Sie danach auf die Schaltfläche **+**.

   	Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3. Klicken Sie im [Verwaltungsportal] auf **Mobile Dienste** und dann auf Ihren mobilen Dienst.

4. Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

   	![][9]

   	Die **TodoItem**-Tabelle enthält nun Daten mit von Mobile Services generierten ID-Werten, und der Tabelle wurden automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt.

Damit ist das Lernprogramm **Erste Schritte mit Daten** für Xamarin.iOS beendet.

## Abgeschlossenes Beispiel abrufen
Laden Sie das [abgeschlossene Beispielprojekt] herunter. Stellen Sie sicher, dass Sie die Variablen **applicationURL** und **applicationKey** mit Ihren eigenen Azure-Einstellungen aktualisieren.

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer iOS-App für die Arbeit mit Daten in Mobile Services aufgezeigt.

Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

* [Prüfen und Ändern von Daten mit Skripten] <br/>Informationen zur Verwendung von Serverskripts in mobilen Diensten, um von Ihrer App gesendete Daten zu prüfen und zu ändern.

* [Optimieren von Abfragen mittels Paging] <br/>Informationen zur Verwendung von Paging in Abfragen, um die in einer einzelnen Anforderung behandelte Datenmenge zu steuern.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie die folgenden weiteren iOS-Lernprogramme ausprobieren:

* [Erste Schritte mit der Authentifizierung] <br/>Informationen zur Authentifizierung von Benutzern Ihrer App.

* [Erste Schritte mit Pushbenachrichtigungen] <br/>Verschicken Sie mithilfe Ihres mobilen Dienstes eine einfache Pushbenachrichtigung an Ihre App.

<!-- Anchors. -->

[Get the Windows Store app]: #download-app
[Erstellen des mobilen Diensts]: #create-service
[Hinzufügen von Datentabellen als Datenspeicher]: #add-table
[Aktualisieren von Apps zur Verwendung von Mobile Services]: #update-app
[Testen von Apps mit Mobile Services]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png




[5]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png

<!-- URLs. TODO:: update download link, github link, and completed example project with new Xamarin.iOs projects -->
[Prüfen und Ändern von Daten mit Skripten]: /develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
[Optimieren von Abfragen mittels Paging]: /develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Get started with Mobile Services]: /develop/mobile/tutorials/get-started-xamarin-ios
[Get started with data]: /develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Erste Schritte mit der Authentifizierung]: /develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Erste Schritte mit Pushbenachrichtigungen]: /develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Azure Management Portal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[Install Xcode]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[GitHub repo]: http://go.microsoft.com/fwlink/p/?LinkId=268784
[Azure Mobile Services]: http://components.xamarin.com/view/azure-mobile-services/

[abgeschlossene Beispielprojekt]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[Xamarin.iOS]: http://xamarin.com/download
 

<!---HONumber=August15_HO6-->