<properties 
	pageTitle="Erste Schritte mit Daten (Xamarin.iOS) - Azure Mobile Services" 
	description="Erfahren Sie, wie Sie Daten von Ihrer Azure Mobile Services Xamarin.iOS-App speichern und darauf zugreifen." 
	documentationCenter="xamarin" 
	authors="lindydonna" 
	manager="dwrede" 
	services="mobile-services" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-xamarin-ios" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="09/24/2014" 
	ms.author="donnam"/>

# Hinzufügen von Mobile Services zu einer vorhandenen App

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../includes/mobile-services-selector-get-started-data.md)]

Dieses Thema beschreibt den Einsatz von Azure Mobile Services für die Nutzung von Daten in Xamarin.iOS-Apps. In diesem Lernprogramm laden Sie eine App herunter, die Daten im Arbeitsspeicher speichert, erstellen einen neuen mobilen Dienst, integrieren den mobilen Dienst in eine App und melden sich dann beim Azure-Verwaltungsportal an, um Datenänderungen beim Ausführen der App anzuzeigen.

> [AZURE.NOTE] Dieses Lernprogramm vermittelt ein besseres Verständnis davon, wie Sie mit Mobile Services in Azure Daten aus einer Xamarin.iOS-App speichern und abrufen können. Dieses Thema behandelt viele der Schritte, die Ihnen im Schnellstart für mobile Dienste abgenommen werden. Falls Sie noch keine Erfahrung mit Mobile Services haben, sollten Sie eventuell zuerst das Lernprogramm [Erste Schritte mit Mobile Services](/de-de/develop/mobile/tutorials/get-started-xamarin-ios) abschließen

In diesem Lernprogramm werden die grundlegenden Schritte erläutert:

1. [Herunterladen des Xamarin.iOS-App-Projekts][GitHub] 
2. [Erstellen des mobilen Diensts]
3. [Hinzufügen einer Datentabelle als Speicher]
4. [Aktualisieren der App zur Verwendung von Mobile Services]
5. [Testen der App mit Mobile Services]

Dieses Lernprogramm erfordert [Azure Mobile Services-Komponente], [XCode 5.0][XCode installieren], [Xamarin.iOS] und iOS 5.0 oder eine höhere Version.

> [AZURE.IMPORTANT] Sie benötigen ein Azure-Konto, um dieses Lernprogramm auszuführen. Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenlose Azure-Testversion](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A643EE910&amp;returnurl=http%3A%2F%2Fwww.windowsazure.com%2Fde-de%2Fdevelop%2Fmobile%2Ftutorials%2Fget-started-with-data-xamarin-ios%2F"%20target="_blank).

<h2><a name="download-app"></a>Download des GetStartedWithData-Projekts</h2>

Dieses Lernprogramm baut auf der [GetStartedWithData-App][GitHub] auf, bei der es sich um eine Xamarin.iOS-App handelt. Die Benutzeroberfläche dieser App und die vom Mobile Services-Xamarin.iOS-Schnellstart generierte Benutzeroberfläche sind identisch, mit der Ausnahme, dass hinzugefügte Einträge lokal im Arbeitsspeicher gespeichert werden. 

1. Herunterladen der [GetStartedWithData-App][GitHub]. 

2. Öffnen Sie das heruntergeladene Projekt in Xamarin Studio, und sehen Sie sich die Klasse **TodoService** an.

   	Beachten Sie, dass mehrere **// TODO**-Kommentare vorhanden sind. Diese geben die von Ihnen durchzuführenden Schritte an, damit diese App mit dem mobilen Dienst funktionieren kann.

3. Klicken Sie im Menü **Ausführen** auf **Starten ohne Debugging**, um die App zu starten.

4. Geben Sie in der App einen Text in das Textfeld ein, und klicken Sie anschließend auf die Schaltfläche **+**.

   	![][0]  

   	Beachten Sie, dass der gespeicherte Text in der unteren Liste angezeigt wird.

<h2><a name="create-service"></a>Erstellen eines neuen mobilen Diensts im Verwaltungsportal</h2>

[AZURE.INCLUDE [mobile-services-create-new-service-data](../includes/mobile-services-create-new-service-data.md)]

<h2><a name="add-table"></a>Hinzufügen einer neuen Tabelle zum mobilen Dienst</h2>

Um App-Daten im neuen mobilen Dienst speichern zu können, müssen Sie zuerst eine neue Tabelle in der verknüpften SQL-Datenbankinstanz erstellen.

1. Klicken Sie im Verwaltungsportal auf **Mobile Services** und anschließend auf den mobilen Dienst, den Sie gerade erstellt haben.

2. Klicken Sie auf die Registerkarte **Daten** und anschließend auf **+Erstellen**.

   	![][5]

   	Das Dialogfeld **Neue Tabelle erstellen** wird angezeigt.

3. Geben Sie unter **Tabellenname** "_TodoItem_" ein, und klicken Sie auf das Kontrollkästchen.

  	![][6]

  	Daraufhin wird eine neue Speichertabelle **TodoItem** mit den Standardberechtigungen erstellt, d. h. jeder Benutzer der App kann auf die Daten in der Tabelle zugreifen und diese ändern. 

    > [AZURE.NOTE] Der gleiche Tabellenname wird in Mobile Services-Quickstart verwendet. Jede Tabelle wird jedoch in einem Schema erstellt, das für einen bestimmten mobilen Dienst gilt. Dadurch soll verhindert werden, dass es zu Datenkollisionen kommt, wenn mehrere mobile Dienste dieselbe Datenbank verwenden.

4. Klicken Sie auf die neue Tabelle **TodoItem**, und überprüfen Sie, dass keine Datenzeilen vorhanden sind.

5. Klicken Sie auf die Registerkarte **Spalten**, und vergewissern Sie sich, dass diese nur eine einzige **id**-Spalte enthält, die automatisch erstellt wurde.

	  Dies ist die Mindestanforderung für eine Tabelle in Mobile Services. 

    > [AZURE.NOTE] Wenn für Ihren mobilen Dienst das dynamische Schema aktiviert ist, werden neue Spalten immer dann automatisch erstellt, wenn JSON-Objekte über einen Einfüge- oder Aktualisierungsvorgang an den mobilen Dienst gesendet werden.

Sie können den neuen mobilen Dienst nun als Datenspeicher für die App verwenden.

<h2><a name="update-app"></a>Aktualisieren der App für den Datenzugriff über mobile Dienste</h2>

Ihr mobiler Dienst ist nun bereit und Sie können die App aktualisieren, sodass dieser Elemente im mobilen Dienst anstatt in einer lokalen Sammlung speichert. 

1. Falls Ihr Components-Ordner noch keinen Eintrag **Azure Mobile Services** enthält, können Sie mit der rechten Maustaste auf **Components** klicken, **Weitere Komponenten abrufen** auswählen und nach **Azure Mobile Services** suchen.

2. Öffnen Sie in der Projektmappenansicht in Xamarin Studio die Klasse **TodoService**, und heben Sie die Auskommentierung der folgenden `using`-Anweisung auf:

        using Microsoft.WindowsAzure.MobileServices;
               
3. Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

4. Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich die **Site URL**. Klicken Sie anschließend auf **Schlüssel verwalten**, und notieren Sie sich den **Anwendungsschlüssel**.

   	![][8]

5. Heben Sie die Auskommentierung der folgenden Konstanten in der Klasse **Constants** auf:

        public const string ApplicationURL = @"AppUrl";
        public const string ApplicationKey = @"AppKey";

6. Ersetzen Sie **AppUrl** und **AppKey** in den obigen Konstanten durch die Werte aus dem Verwaltungsportal (siehe oben).

7. Heben Sie in der Klasse **TodoService** die Auskommentierung der folgenden Codezeile auf:

        private MobileServiceClient client;

   	Dadurch wird eine Eigenschaft erstellt, die den MobileServiceClient repräsentiert, welcher eine Verbindung zu dem Dienst herstellt.

8. Heben Sie in der Klasse **TodoService** die Auskommentierung der folgenden Codezeile auf:

        private IMobileServiceTable<TodoItem> todoTable;  

   	Dadurch wird eine Eigenschaftsdarstellung für die Tabelle der mobilen Dienste erstellt.

9. Heben Sie im Konstruktor **TodoService** die Auskommentierung der folgenden Codezeilen auf:

		// TODO:: Uncomment these lines to use Mobile Services
		client = new MobileServiceClient(Constants.ApplicationURL, Constants.ApplicationKey).WithFilter(this); 
		todoTable = client.GetTable<TodoItem>(); 

    Hierdurch wird eine Instanz des Mobile Services-Client sowie eine Instanz der TodoItem-Tabelle erstellt.

10. Heben Sie in der **RefreshDataAsync**-Methode in **TodoService** die Auskommentierung der folgenden Codezeilen auf:

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

11. Suchen Sie die Methode **InsertTodoItemAsync**, und heben Sie die Auskommentierung der folgenden Zeile auf:

		await todoTable.InsertAsync(todoItem);
		
    Dieser Code sendet eine Einfügeanforderung an den mobilen Dienst.

13. Suchen Sie die Methode **CompleteItemAsync**, und heben Sie die Auskommentierung der folgenden Zeile auf:

		await todoTable.UpdateAsync(item);
		
   	Durch diesen Code werden die TodoItems entfernt, nachdem diese als abgeschlossen markiert sind. 

Nachdem nun die App für die Verwendung von Mobile Services als Back-End-Speicher aktualisiert ist, können Sie die App mit Mobile Services testen.

<h2><a name="test-app"></a>Testen der App mit dem neuen mobilen Dienst</h2>

1. Wählen Sie in Xamarin Studio einen Emulator oder ein Gerät für die Bereitstellung aus einem der primären Kombinationsfelder aus, und klicken Sie im Menü **Ausführen** auf **Starten ohne Debugging**, um die App zu starten.

   	Hierdurch wird der Azure Mobile Services-Client ausgeführt und mit Xamarin.iOS erstellt, welches Einträge vom mobilen Dienst abfragt.

2. Geben Sie wie zuvor Text in das Textfeld ein, und klicken Sie danach auf die Schaltfläche **+**.

   	Auf diese Weise wird ein neuer Eintrag an den mobilen Service gesendet.

3. Klicken Sie im [Verwaltungsportal] auf **Mobile Services** und dann auf Ihren mobilen Dienst.

4. Klicken Sie auf die Registerkarte **Daten** und dann auf **Durchsuchen**.

   	![][9]
  
   	Beachten Sie, dass die **TodoItem**-Tabelle nun Daten mit von Mobile Services generierten ID-Werten enthält, und dass der Tabelle automatisch Spalten entsprechend der TodoItem-Klasse der App hinzugefügt wurden.

Damit ist das Lernprogramm **Erste Schritte mit Daten** für Xamarin.iOS abgeschlossen.

## Abgeschlossenes Beispiel abrufen
Laden Sie das [abgeschlossene Beispielprojekt] herunter. Stellen Sie sicher, dass Sie die Variablen **applicationURL** und **applicationKey** mit Ihren eigenen Azure-Einstellungen aktualisieren. 

## <a name="next-steps"> </a>Nächste Schritte

In diesem Lernprogramm wurden die Grundlagen der Aktivierung einer iOS-App für die Arbeit mit Daten in Mobile Services aufgezeigt. 

Als Nächstes können Sie eines der folgenden Lernprogramme ausführen, das auf der GetStartedWithData-App aufbaut, die Sie in diesem Lernprogramm erstellt haben:

* [Überprüfen und Ändern von Daten mit Skripts]
  <br/>Erfahren Sie mehr über die Verwendung von Serverskripts in Mobile Services, um die von Ihrer App gesendeten Daten zu prüfen und zu ändern.

* [Optimieren von Abfragen mit Paging]
  <br/>Erfahren Sie, wie Sie Paging in Abfragen zum Steuern der in einer einzelnen Anforderung verarbeiteten Datenmengen verwenden können.

Wenn Sie die Datenreihe abgeschlossen haben, können Sie die folgenden weiteren iOS-Lernprogramme ausprobieren:

* [Erste Schritte mit der Authentifizierung]
	<br/>Erfahren Sie, wie Benutzer in Ihrer App authentifiziert werden.

* [Erste Schritte mit Pushbenachrichtigungen]
  <br/>Erfahren Sie, wie Sie eine ganz einfache Pushbenachrichtigung mit Mobile Services an Ihre App senden können.

<!-- Anchors. -->

[Abrufen der Windows Store-App]: #download-app
[Erstellen des mobilen Diensts]: #create-service
[Hinzufügen einer Datentabelle als Speicher]: #add-table
[Aktualisieren der App zur Verwendung von Mobile Services]: #update-app
[Testen der App mit Mobile Services]: #test-app
[Nächste Schritte]:#next-steps

<!-- Images. -->
[0]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-quickstart-startup-ios.png




[5]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-data-tab-empty.png
[6]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-create-todoitem-table.png
[8]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-dashboard-tab.png
[9]: ./media/partner-xamarin-mobile-services-ios-get-started-data/mobile-todoitem-data-browse.png

<!-- URLs. TODO:: update download link, github link, and completed example project with new Xamarin.iOs projects -->
[Überprüfen und Ändern von Daten mit Skripts]: /de-de/develop/mobile/tutorials/validate-modify-and-augment-data-xamarin-ios
[Optimieren von Abfragen mit Paging]: /de-de/develop/mobile/tutorials/add-paging-to-data-xamarin-ios
[Erste Schritte mit Mobile Services]: /de-de/develop/mobile/tutorials/get-started-xamarin-ios
[Erste Schritte mit Daten]: /de-de/develop/mobile/tutorials/get-started-with-data-xamarin-ios
[Erste Schritte mit der Authentifizierung]: /de-de/develop/mobile/tutorials/get-started-with-users-xamarin-ios
[Erste Schritte mit Pushbenachrichtigungen]: /de-de/develop/mobile/tutorials/get-started-with-push-xamarin-ios

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/
[Verwaltungsportal]: https://manage.windowsazure.com/
[XCode installieren]: https://go.microsoft.com/fwLink/p/?LinkID=266532
[Mobile Services iOS SDK]: https://go.microsoft.com/fwLink/p/?LinkID=266533
[GitHub]: http://go.microsoft.com/fwlink/p/?LinkId=331302
[GitHub-Repository]: http://go.microsoft.com/fwlink/p/?LinkId=268784
[Azure Mobile Services-Komponente]: http://components.xamarin.com/view/azure-mobile-services/

[abgeschlossene Beispielprojekt]: http://go.microsoft.com/fwlink/p/?LinkId=331302




<!--HONumber=42-->
