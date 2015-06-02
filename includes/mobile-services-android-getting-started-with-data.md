Ihr mobiler Dienst ist nun bereit, und Sie können die App aktualisieren, sodass Elemente in Mobile Services anstatt in einer lokalen Sammlung gespeichert werden.

1. Stellen Sie sicher, dass die folgenden Zeilen im **dependencies**-Tag in der Datei *build.gradle (Modul-App)* vorhanden sind. Wenn dies nicht der Fall ist, fügen Sie sie hinzu. Dadurch werden Verweise auf das Mobile Services Android Client SDK hinzugefügt.

		compile 'com.android.support:support-v4:21.0.3'
    	compile 'com.google.code.gson:gson:2.2.2'
	    compile 'com.google.guava:guava:18.0'
	    compile 'com.microsoft.azure:azure-mobile-services-android-sdk:2.0.2-beta'


2. Jetzt erstellen Sie das Projekt neu, indem Sie auf** Sync Project with Gradle Files** klicken.

3. Öffnen Sie die AndroidManifest.xml-Datei und fügen Sie die folgende Zeile hinzu, um der App den Zugriff auf mobile Dienste in Azure zu ermöglichen.

		<uses-permission android:name="android.permission.INTERNET" />


6. Öffnen Sie die AndroidManifest.xml-Datei und fügen Sie die folgende Zeile hinzu, um der App den Zugriff auf mobile Dienste in Azure zu ermöglichen.

		<uses-permission android:name="android.permission.INTERNET" />

5. Öffnen Sie im Projektexplorer die Datei "TodoActivity.java", die sich im Ordner **GetStartedWithData = > app = > src = > java** befindet, und entfernen Sie die Kommentarzeichen vor den folgenden Codezeilen:



		import java.net.MalformedURLException;
		import android.os.AsyncTask;
		import com.google.common.util.concurrent.FutureCallback;
		import com.google.common.util.concurrent.Futures;
		import com.google.common.util.concurrent.ListenableFuture;
		import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
		import com.microsoft.windowsazure.mobileservices.MobileServiceList;
		import com.microsoft.windowsazure.mobileservices.http.NextServiceFilterCallback;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilter;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilterRequest;
		import com.microsoft.windowsazure.mobileservices.http.ServiceFilterResponse;
		import com.microsoft.windowsazure.mobileservices.table.MobileServiceTable;

 
5. Kommentieren Sie die folgenden Zeilen:

		import java.util.ArrayList;
		import java.util.List;

6. Wir werden die aktuell verwendete speicherinterne Liste entfernen und durch einen mobilen Dienst ersetzen. Kommentieren Sie in der Klasse **ToDoActivity** die folgenden Codezeilen, in denen die **toDoItemList**-Liste definiert wird.

		public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

7. Speichern Sie die Datei, und daraufhin zeigt das Projekt Buildfehler an. Suchen Sie nach den verbleibenden drei Stellen, an denen die Variable `toDoItemList` verwendet wird, und stellen Sie die entsprechenden Abschnitte Kommentarzeichen voran. Nun haben Sie die speicherinterne Liste vollständig entfernt.

8. Wir werden nun den mobilen Dienst hinzufügen. Entfernen Sie die Kommentare in den folgenden Codezeilen:

		private MobileServiceClient mClient;
		private private MobileServiceTable<ToDoItem> mToDoTable;

9. Suchen Sie die* ProgressFilter*-Klasse am Ende der Datei und entfernen Sie die Kommentarzeichen. Diese Klasse zeigt einen Hinweis an, wenn der *MobileServiceClient* Netzwerkvorgänge ausführt.


10. Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

11. Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich die **Site URL**. Klicken Sie anschließend auf **Schlüssel verwalten**, und notieren Sie sich den **Anwendungsschlüssel**.

   	![](./media/download-android-sample-code/mobile-dashboard-tab.png)

  	Sie benötigen diese Werte beim Zugriff auf den mobilen Dienst von Ihrem App-Code aus.

12. Entfernen Sie in der Methode **onCreate** die Kommentare in den folgenden Codezeilen, in denen die **MobileServiceClient**-Variable definiert wird:

		try {
		// Create the Mobile Service Client instance, using the provided
		// Mobile Service URL and key
			mClient = new MobileServiceClient(
					"MobileServiceUrl",
					"AppKey", 
					this).withFilter(new ProgressFilter());

			// Get the Mobile Service Table instance to use
			mToDoTable = mClient.getTable(ToDoItem.class);
		} catch (MalformedURLException e) {
			createAndShowDialog(new Exception("There was an error creating the Mobile Service. Verify the URL"), "Error");
		}

  	Dieser Code erstellt eine neue *MobileServiceClient*-Instanz, die für den Zugriff auf Ihren mobilen Dienst verwendet wird. Außerdem wird die *MobileServiceTable*-Instanz für die Proxy-Datenspeicherung im mobilen Dienst erstellt.

13. Ersetzen Sie im obigen Code `MobileServiceUrl` und `AppKey` durch die URL und den Anwendungsschlüssel des mobilen Dienstes.



14. Entfernen Sie die Kommentare in diesen Zeilen in der **checkItem**-Methode:

	    new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.update(item).get();
	                runOnUiThread(new Runnable() {
	                    public void run() {
	                        if (item.isComplete()) {
	                            mAdapter.remove(item);
	                        }
	                        refreshItemsFromTable();
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();

   	Dieser Code schickt eine Elementaktualisierung an den mobilen Dienst und entfernt markierte Elemente aus dem Adapter.
    
15. Entfernen Sie die Kommentare in diesen Zeilen in der **addItem**-Methode:
	
		// Insert the new item
		new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                mToDoTable.insert(item).get();
	                if (!item.isComplete()) {
	                    runOnUiThread(new Runnable() {
	                        public void run() {
	                            mAdapter.add(item);
	                        }
	                    });
	                }
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();
		

  	Dieser Code erstellt ein neues Element und fügt es in die Tabelle im mobilen Dienst ein.

16. Entfernen Sie die Kommentare in diesen Zeilen in der **refreshItemsFromTable**-Methode:

		// Get the items that weren't marked as completed and add them in the adapter
	    new AsyncTask<Void, Void, Void>() {
	        @Override
	        protected Void doInBackground(Void... params) {
	            try {
	                final MobileServiceList<ToDoItem> result = mToDoTable.where().field("complete").eq(false).execute().get();
	                runOnUiThread(new Runnable() {
	                    @Override
	                    public void run() {
	                        mAdapter.clear();

	                        for (ToDoItem item : result) {
	                            mAdapter.add(item);
	                        }
	                    }
	                });
	            } catch (Exception exception) {
	                createAndShowDialog(exception, "Error");
	            }
	            return null;
	        }
	    }.execute();

	Dieser Code fragt den mobilen Dienst ab und gibt alle Elemente zurück, die nicht als abgeschlossen markiert sind. Die Elemente werden für die Bindung zum Adapter hinzugefügt.
		

<!-- URLs. -->
[Mobile Services Android SDK]: http://aka.ms/Iajk6q
<!--HONumber=54-->