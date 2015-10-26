<properties
	pageTitle="Hinzufügen von Offlinedatensynchronisierung zur Mobile Services-App für Android | Microsoft Azure"
	description="Erfahren Sie, wie Sie Azure Mobile Services verwenden, um Offlinedaten in Ihrer Android-Anwendung zwischenzuspeichern und zu synchronisieren."
	documentationCenter="android"
	authors="RickSaling"
	manager="dwrede"
	editor=""
	services="mobile-services"/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-android"
	ms.devlang="java"
	ms.topic="article"
	ms.date="10/01/2015"
	ms.author="ricksal"/>

# Hinzufügen von Offlinedatensynchronisierung zur Mobile Services-App für Android

[AZURE.INCLUDE [mobile-services-selector-offline](../../includes/mobile-services-selector-offline.md)]

## Zusammenfassung

Die Netzwerkverbindung mobiler Apps kann verloren gehen, wenn sie in ein Gebiet ohne Service gelangen oder wenn Netzwerkprobleme auftreten. Beispiel: In eine App für die Baubranche, die auf einer entfernten Baustelle verwendet wird, müssen Zeitplandaten eingegeben werden, die später mit Azure synchronisiert werden. Mithilfe der Offlinesynchronisierung in Azure Mobile Services können Sie weiterarbeiten, wenn die Netzwerkverbindung unterbrochen ist. Dies ist für viele mobile Apps unabdingbar. Bei Verwendung der Offlinesynchronisierung arbeiten Sie mit einer lokalen Kopie der Azure SQL Server-Tabelle und synchronisieren in regelmäßigen Abständen Original und Kopie.

In diesem Lernprogramm aktualisieren Sie die App aus dem [Schnellstartlernprogramm "Erste Schritte mit Mobile Services"], um die Offlinesynchronisierung zu aktivieren. Anschließend testen Sie die App, indem Sie Daten offline hinzufügen, diese Elemente mit der Onlinedatenbank synchronisieren und die Änderungen im Azure-Verwaltungsportal überprüfen.

Immer wenn mehrere Änderungen an den Daten vorgenommen werden, können Konflikte auftreten, egal ob Sie offline sind oder eine Verbindung besteht. In einem zukünftigen Lernprogramm wird das Behandeln von Synchronisierungskonflikten untersucht. Dabei wählen Sie die Version der Änderungen aus, die übernommen werden soll. In diesem Lernprogramm gehen wir davon aus, dass keine Synchronisierungskonflikte auftreten und alle von Ihnen an vorhandenen Daten vorgenommenen Änderungen direkt auf den Azure SQL Server angewendet werden.


## Voraussetzungen

[AZURE.INCLUDE [mobile-services-android-prerequisites](../../includes/mobile-services-android-prerequisites.md)]

## Aktualisieren der App für die Unterstützung von Offlinesynchronisierung

Bei der Offlinesynchronisierung wird in eine *Synchronisierungstabelle* geschrieben und aus dieser gelesen (mit der *IMobileServiceSyncTable*-Schnittstelle). Diese ist Teil einer **SQL Light**-Datenbank auf Ihrem Gerät.

Zum Übertragen von Änderungen per Push und Pull zwischen dem Gerät und Azure Mobile Services verwenden Sie einen *Synchronisierungskontext* (*MobileServiceClient.SyncContext*), den Sie mit der lokalen Datenbank initialisieren, in der Sie die Daten lokal speichern.

1. Fügen Sie die Berechtigung zum Überprüfen der Netzwerkverbindung hinzu, indem Sie der Datei *AndroidManifest.xml* folgenden Code hinzufügen:

	    <uses-permission android:name="android.permission.ACCESS_NETWORK_STATE" />


2. Fügen Sie *ToDoActivity.java* die folgenden **Import**-Anweisungen hinzu:

		import java.util.Map;

		import android.widget.Toast;

		import com.microsoft.windowsazure.mobileservices.table.query.Query;
		import com.microsoft.windowsazure.mobileservices.table.sync.MobileServiceSyncContext;
		import com.microsoft.windowsazure.mobileservices.table.sync.MobileServiceSyncTable;
		import com.microsoft.windowsazure.mobileservices.table.sync.localstore.ColumnDataType;
		import com.microsoft.windowsazure.mobileservices.table.sync.localstore.SQLiteLocalStore;

3. Ändern Sie am Anfang der `ToDoActivity`-Klasse die Deklaration der `mToDoTable`-Variable von einer `MobileServiceTable<ToDoItem>`-Klasse in eine `MobileServiceSyncTable<ToDoItem>`-Klasse.

		 private MobileServiceSyncTable<ToDoItem> mToDoTable;

	Dort definieren Sie die Synchronisierungstabelle.

4. Fügen Sie zum Initialisieren des lokalen Speichers in der `onCreate`-Methode nach dem Erstellen der `MobileServiceClient`-Instanz die folgenden Zeilen hinzu:

			// Saves the query which will be used for pulling data
			mPullQuery = mClient.getTable(ToDoItem.class).where().field("complete").eq(false);

			SQLiteLocalStore localStore = new SQLiteLocalStore(mClient.getContext(), "ToDoItem", null, 1);
			SimpleSyncHandler handler = new SimpleSyncHandler();
			MobileServiceSyncContext syncContext = mClient.getSyncContext();

			Map<String, ColumnDataType> tableDefinition = new HashMap<String, ColumnDataType>();
			tableDefinition.put("id", ColumnDataType.String);
			tableDefinition.put("text", ColumnDataType.String);
			tableDefinition.put("complete", ColumnDataType.Boolean);
			tableDefinition.put("__version", ColumnDataType.String);

			localStore.defineTable("ToDoItem", tableDefinition);
			syncContext.initialize(localStore, handler).get();

			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getSyncTable(ToDoItem.class);

5. Fügen Sie nach dem vorangegangenen Code, der sich in einem `try`-Block befindet, nach dem `MalformedURLException`-Code einen zusätzlichen `catch`-Block hinzu:

		} catch (Exception e) {
			Throwable t = e;
			while (t.getCause() != null) {
					t = t.getCause();
				}
			createAndShowDialog(new Exception("Unknown error: " + t.getMessage()), "Error");
		}

6. Fügen Sie die folgende Methode hinzu, um die Netzwerkverbindung zu überprüfen:

		private boolean isNetworkAvailable() {
			ConnectivityManager connectivityManager
					= (ConnectivityManager) getSystemService(Context.CONNECTIVITY_SERVICE);
			NetworkInfo activeNetworkInfo = connectivityManager.getActiveNetworkInfo();
			return activeNetworkInfo != null && activeNetworkInfo.isConnected();
		}


7. Fügen Sie die folgende Methode für die Synchronisierung zwischen dem lokalen *SQL Light*-Speicher und dem Azure SQL Server hinzu:

		public void syncAsync(){
			if (isNetworkAvailable()) {
				new AsyncTask<Void, Void, Void>() {

					@Override
					protected Void doInBackground(Void... params) {
						try {
							mClient.getSyncContext().push().get();
							mToDoTable.pull(mPullQuery).get();

						} catch (Exception exception) {
							createAndShowDialog(exception, "Error");
						}
						return null;
					}
				}.execute();
			} else {
				Toast.makeText(this, "You are not online, re-sync later!" +
						"", Toast.LENGTH_LONG).show();
			}
		}


8. Fügen Sie in der `onCreate`-Methode direkt vor dem Aufruf von `refreshItemsFromTable` folgenden Code als vorletzte Zeile hinzu:

			syncAsync();

	Dadurch wird das Gerät beim Starten mit der Azure-Tabelle synchronisiert. Andernfalls wird der letzte Offlineinhalt des lokalen Speichers angezeigt.



9. Aktualisieren Sie den Code in der `refreshItemsFromTable`-Methode, um diese Abfrage zu verwenden (erste Codezeile im `try`-Block):

		final MobileServiceList<ToDoItem> result = mToDoTable.read(mPullQuery).get();

10. Ersetzen Sie in der `onOptionsItemSelected`-Methode den Inhalt des `if`-Blocks durch folgenden Code:

			syncAsync();
			refreshItemsFromTable();

	Dieser Code wird ausgeführt, wenn Sie in der rechten oberen Ecke auf die Schaltfläche **Aktualisieren** klicken. Dies ist neben dem Synchronisieren beim Starten das wichtigste Verfahren zum Synchronisieren des lokalen Speichers mit Azure. Dies fördert die Batchverarbeitung von Synchronisierungsänderungen und ist ein effizientes Verfahren, da die Übertragung per Pull aus Azure relativ aufwendig ist. Sie können außerdem im Entwurf dieser App das Synchronisieren bei jeder Änderung festlegen, indem Sie den Methoden `addItem` und `checkItem` einen Aufruf von `syncAsync` hinzufügen, falls die App dies erfordert.


## Testen der App

In diesem Abschnitt testen Sie das Verhalten bei aktiviertem WLAN und deaktivieren anschließend WLAN, um ein Offlineszenario zu erzeugen.

Wenn Sie Datenelemente hinzufügen, werden diese im lokalen SQL Light-Speicher gespeichert, jedoch erst mit dem mobilen Dienst synchronisiert, wenn Sie auf die Schaltfläche **Aktualisieren** klicken. Bei anderen Apps müssen Daten möglicherweise zu anderen Zeitpunkten synchronisiert werden, zur Veranschaulichung muss jedoch in diesem Lernprogramm der Benutzer die Synchronisierung explizit anfordern.

Wenn Sie auf diese Schaltfläche klicken, wird eine neue Hintergrundaufgabe gestartet. Zunächst werden alle am lokalen Speicher vorgenommenen Änderungen mithilfe des Synchronisierungskontexts per Push übertragen, und dann werden alle geänderten Daten aus Azure mithilfe von Pull in die lokale Tabelle übertragen.


### Onlinetest

Testen Sie die folgenden Szenarien:

1. Fügen Sie auf dem Gerät einige neue Elemente hinzu.
2. Vergewissern Sie sich, dass die Elemente nicht im Portal angezeigt werden.
3. Klicken Sie dann auf **Aktualisieren**, und überprüfen Sie, ob sie anschließend angezeigt werden.
4. Ändern Sie im Portal ein Element, oder fügen Sie ein Element hinzu, klicken Sie dann auf **Aktualisieren**, und überprüfen Sie, ob die Änderungen auf dem Gerät angezeigt werden.

### Offlinetest

<!-- Now if you run the app and tap the refresh button, you should see all the items from the server. At that point you should be able to turn off the networking from the device by placing it in *Airplane Mode*, and continue making changes – the app will work just fine. When it’s time to sync the changes to the server, turn the network back on, and tap the **Refresh** button again.

One thing which is important to point out: if there are pending changes in the local store, a pull operation will first push those changes to the server (so that if there are changes in the same row, the push operation will fail and the application has an opportunity to handle the conflicts appropriately). That means that the push call in the code above isn’t necessarily required, but I think it’s always a good practice to be explicit about what the code is doing.
-->

1. Schalten Sie das Gerät oder den Simulator in den *Flugzeugmodus*. Dies erzeugt ein Offlineszenario.

2. Fügen Sie einige *TODO*-Elemente hinzu, oder markieren Sie einige Elemente als abgeschlossen. Schalten Sie das Gerät oder den Simulator aus (oder erzwingen Sie das Schließen der App), und starten Sie das Gerät bzw. den Simulator neu. Vergewissern Sie sich, dass die Änderungen auf dem Gerät beibehalten wurden, da sie im lokalen SQL Light-Speicher gespeichert sind.

3. Zeigen Sie den Inhalt der Azure-Tabelle *TodoItem* an. Stellen Sie sicher, dass die neuen Elemente _nicht_ auf dem Server synchronisiert wurden:

   - Klicken Sie für das JavaScript-Back-End auf dem Verwaltungsportal auf die Registerkarte "Daten", um den Inhalt der `TodoItem`-Tabelle anzuzeigen.
   - Zeigen Sie für das .NET-Back-End den Inhalt der Tabelle entweder mit einem SQL-Tool wie *SQL Server Management Studio* oder einen REST-Client wie *Fiddler* oder *Postman* an.

4. Aktivieren Sie auf dem Gerät oder Simulator WLAN. Klicken Sie anschließend auf die Schaltfläche **Aktualisieren**.

5. Zeigen Sie die TodoItem-Daten erneut im Azure-Portal an. Es sollten jetzt die neuen und geänderten TodoItems angezeigt werden.


## Nächste Schritte

* [Vorläufiges Löschen in Mobile Services][Soft Delete]

## Zusätzliche Ressourcen

* [Cloud Cover: Offlinesynchronisierung in Azure Mobile Services]

* [Azure Friday: Offlinefähige Apps in Azure Mobile Services] (Hinweis: Demos sind für Windows, Funktionserläuterungen gelten jedoch für alle Plattformen)


<!-- URLs. -->

[Get the sample app]: #get-app
[Review the Core Data model]: #review-core-data
[Review the Mobile Services sync code]: #review-sync
[Change the sync behavior of the app]: #setup-sync
[Test the app]: #test-app


[Mobile Services sample repository on GitHub]: https://github.com/Azure/mobile-services-samples


[Get started with Mobile Services]: mobile-services-android-get-started.md
[Handling Conflicts with Offline Support for Mobile Services]: mobile-services-android-handling-conflicts-offline-data.md
[Soft Delete]: mobile-services-using-soft-delete.md

[Cloud Cover: Offlinesynchronisierung in Azure Mobile Services]: http://channel9.msdn.com/Shows/Cloud+Cover/Episode-155-Offline-Storage-with-Donna-Malayeri
[Azure Friday: Offlinefähige Apps in Azure Mobile Services]: http://azure.microsoft.com/documentation/videos/azure-mobile-services-offline-enabled-apps-with-donna-malayeri/

[Schnellstartlernprogramm "Erste Schritte mit Mobile Services"]: mobile-services-android-get-started.md

<!---HONumber=Oct15_HO3-->