<properties
	pageTitle="Aktivieren der Offlinesynchronisierung für Ihre mobile Azure-App (Android)"
	description="Erfahren Sie, wie Sie mobile App Service-Apps verwenden, um Offlinedaten in Ihrer Android-Anwendung zwischenzuspeichern und zu synchronisieren."
	documentationCenter="android"
	authors="lindydonna"
	manager="dwrede"
	services="app-service\mobile"/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="12/01/2015"
	ms.author="donnam"/>

# Aktivieren der Offlinesynchronisierung für Ihre mobile Android-App

[AZURE.INCLUDE [app-service-mobile-selector-offline](../../includes/app-service-mobile-selector-offline.md)]&nbsp;[AZURE.INCLUDE [app-service-mobile-note-mobile-services](../../includes/app-service-mobile-note-mobile-services.md)]

## Übersicht

In diesem Tutorial wird die Offlinesynchronisierungsfunktion von Azure Mobile Apps für Android behandelt. Die Offlinesynchronisierung ermöglicht Endbenutzern die Interaktion mit einer mobilen App (Anzeigen, Hinzufügen und Ändern von Daten), auch wenn keine Netzwerkverbindung vorhanden ist. Änderungen werden in einer lokalen Datenbank gespeichert. Sobald das Gerät wieder online ist, werden diese Änderungen mit dem Remote-Back-End synchronisiert.

Falls Sie noch keine Erfahrung mit Azure Mobile Apps haben, sollten Sie zunächst das Tutorial [Erstellen einer Android-App] abschließen. Wenn Sie das heruntergeladene Schnellstart-Serverprojekt nicht verwenden, müssen Sie Ihrem Projekt die Datenzugriffs-Erweiterungspakete hinzufügen. Weitere Informationen zu Servererweiterungspaketen finden Sie unter [Work with the .NET backend server SDK for Azure Mobile Apps](app-service-mobile-dotnet-backend-how-to-use-server-sdk.md) (in englischer Sprache).

Weitere Informationen zur Offlinesynchronisierungsfunktion finden Sie im Thema [Offlinedatensynchronisierung in Azure Mobile Apps].

## Aktualisieren der App für die Unterstützung von Offlinesynchronisierung

Bei der Offlinesynchronisierung wird in eine *Synchronisierungstabelle* geschrieben und aus dieser gelesen (mit der *IMobileServiceSyncTable*-Schnittstelle). Diese ist Teil einer **SQLite**-Datenbank auf Ihrem Gerät.

Zum Übertragen von Änderungen per Push und Pull zwischen dem Gerät und Azure Mobile Services verwenden Sie einen *Synchronisierungskontext* (*MobileServiceClient.SyncContext*), den Sie mit der lokalen Datenbank initialisieren, in der Sie die Daten lokal speichern.

1. Kommentieren Sie in `TodoActivity.java` die vorhandene Definition von `mToDoTable` und entfernen Sie den Kommentar für die Version der Synchronisierungstabelle:
    
	    private MobileServiceSyncTable<ToDoItem> mToDoTable;

2. Kommentieren Sie in der `onCreate`-Methode die vorhandene Initialisierung `mToDoTable` und entfernen Sie den Kommentar für diese Definition:

        mToDoTable = mClient.getSyncTable("ToDoItem", ToDoItem.class);

3. Kommentieren Sie in `refreshItemsFromTable` die Definition von `results` und heben Sie den Kommentar für diese Definition auf:

		// Offline Sync
		final List<ToDoItem> results = refreshItemsFromMobileServiceTableSyncTable();

4. Kommentieren Sie die Definition von `refreshItemsFromMobileServiceTable`.

5. Enfernen Sie den Kommentar der Definition von `refreshItemsFromMobileServiceTableSyncTable`:

	    private List<ToDoItem> refreshItemsFromMobileServiceTableSyncTable() throws ExecutionException, InterruptedException {
	        //sync the data
	        sync().get();
	        Query query = QueryOperations.field("complete").
	                eq(val(false));
	        return mToDoTable.read(query).get();
	    }

6. Enfernen Sie den Kommentar der Definition von `sync`:

	    private AsyncTask<Void, Void, Void> sync() {
	        AsyncTask<Void, Void, Void> task = new AsyncTask<Void, Void, Void>(){
	            @Override
	            protected Void doInBackground(Void... params) {
	                try {
	                    MobileServiceSyncContext syncContext = mClient.getSyncContext();
	                    syncContext.push().get();
	                    mToDoTable.pull(null).get();
	                } catch (final Exception e) {
	                    createAndShowDialogFromTask(e, "Error");
	                }
	                return null;
	            }
	        };
	        return runAsyncTask(task);
	    }

## Testen der App

In diesem Abschnitt testen Sie das Verhalten bei aktiviertem WLAN und deaktivieren anschließend WLAN, um ein Offlineszenario zu erzeugen.

Wenn Sie Datenelemente hinzufügen, werden diese im lokalen SQLite-Speicher gespeichert, jedoch erst mit dem mobilen Dienst synchronisiert, wenn Sie auf die Schaltfläche **Aktualisieren** klicken. Bei anderen Apps müssen Daten möglicherweise zu anderen Zeitpunkten synchronisiert werden, zur Veranschaulichung muss jedoch in diesem Lernprogramm der Benutzer die Synchronisierung explizit anfordern.

Wenn Sie auf diese Schaltfläche klicken, wird eine neue Hintergrundaufgabe gestartet. Zunächst werden alle am lokalen Speicher vorgenommenen Änderungen mithilfe des Synchronisierungskontexts per Push übertragen, und dann werden alle geänderten Daten aus Azure mithilfe von Pull in die lokale Tabelle übertragen.

### Offlinetest

1. Schalten Sie das Gerät oder den Simulator in den *Flugzeugmodus*. Dies erzeugt ein Offlineszenario.

2. Fügen Sie einige *TODO*-Elemente hinzu, oder markieren Sie einige Elemente als abgeschlossen. Schalten Sie das Gerät oder den Simulator aus (oder erzwingen Sie das Schließen der App), und starten Sie das Gerät bzw. den Simulator neu. Vergewissern Sie sich, dass die Änderungen auf dem Gerät beibehalten wurden, da sie im lokalen SQLite-Speicher gespeichert sind.

3. Zeigen Sie die Inhalte der Azure-*TodoItem*-Tabelle entweder mit einem SQL-Tool wie *SQL Server Management Studio* oder einen REST-Client wie *Fiddler* oder *Postman* an. Stellen Sie sicher, dass die neuen Elemente _nicht_ auf dem Server synchronisiert wurden.

   	+ Ein Node.js-Back-End finden Sie im [Azure-Portal](https://portal.azure.com/). Dort klicken Sie in Ihrem mobilen App-Back-End auf **Easy Tables** > **TodoItem**, um den Inhalt der `TodoItem`-Tabelle anzuzeigen.
   	+ Zeigen Sie für einen .NET-Back-End den Inhalt der Tabelle entweder mit einem SQL-Tool wie *SQL Server Management Studio* oder einen REST-Client wie *Fiddler* oder *Postman* an.

4. Aktivieren Sie auf dem Gerät oder Simulator WLAN. Klicken Sie anschließend auf die Schaltfläche **Aktualisieren**.

5. Zeigen Sie die TodoItem-Daten erneut im Azure-Portal an. Es sollten jetzt die neuen und geänderten TodoItems angezeigt werden.

## Zusätzliche Ressourcen

* [Offlinedatensynchronisierung in Azure Mobile Apps]

* [Cloud Cover: Offlinesynchronisierung in Azure Mobile Services] (Hinweis: Im Video geht es zwar um Mobile Services, aber die Offlinesynchronisierung in Azure Mobile Apps funktioniert auf ähnliche Weise.)


<!-- URLs. -->

[Offlinedatensynchronisierung in Azure Mobile Apps]: ../app-service-mobile-offline-data-sync.md

[Erstellen einer Android-App]: ../app-service-mobile-android-get-started.md

[Cloud Cover: Offlinesynchronisierung in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offline-enabled apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

<!---HONumber=AcomDC_1203_2015-->