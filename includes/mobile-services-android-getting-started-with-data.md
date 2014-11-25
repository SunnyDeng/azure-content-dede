Ihr mobiler Dienst ist nun bereit und Sie können die App aktualisieren, sodass dieser Elemente im mobilen Dienst anstatt in einer lokalen Sammlung speichert.

1.  Falls Sie das [Android-SDK für mobile Dienste][Android-SDK für mobile Dienste] noch nicht haben, laden Sie es jetzt herunter und entpacken Sie die komprimierten Dateien.

2.  Kopieren Sie die `.jar`-Dateien aus dem `mobileservices`-Ordner des SDK in den `libs`-Ordner des GetStartedWithData-Projekts.

3.  Klicken Sie im Paket-Explorer in Eclipse mit der rechten Maustaste auf den `libs`-Ordner, und klicken Sie auf **Aktualisieren**, um die kopierten jar-Dateien anzuzeigen.

    Damit wird das SDK für mobile Dienste zum Arbeitsbereich hinzugefügt.

4.  Öffnen Sie die AndroidManifest.xml-Datei und fügen Sie die folgende Zeile hinzu, um der App den Zugriff auf mobile Dienste in Azure zu ermöglichen.

        <uses-permission android:name="android.permission.INTERNET" />

5.  Öffnen Sie im Paket-Explorer die Datei TodoActivity.java im Paket com.example.getstartedwithdata und entfernen Sie die Kommentare in den folgenden Codezeilen:

        import com.microsoft.windowsazure.mobileservices.MobileServiceClient;
        import com.microsoft.windowsazure.mobileservices.MobileServiceTable;
        import com.microsoft.windowsazure.mobileservices.NextServiceFilterCallback;
        import com.microsoft.windowsazure.mobileservices.ServiceFilter;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterRequest;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponse;
        import com.microsoft.windowsazure.mobileservices.ServiceFilterResponseCallback;
        import com.microsoft.windowsazure.mobileservices.TableOperationCallback;
        import com.microsoft.windowsazure.mobileservices.TableQueryCallback;

        import java.net.MalformedURLException;

6.  Wir werden die aktuell verwendete speicherinterne Liste entfernen und durch einen mobilen Dienst ersetzen. Kommentieren Sie in der Klasse **ToDoActivity** die folgenden Codezeilen, in denen die **toDoItemList**-Liste definiert wird.

        public List<ToDoItem> toDoItemList = new ArrayList<ToDoItem>();

7.  Speichern Sie die Datei, und daraufhin zeigt das Projekt Buildfehler an. Suchen Sie nach den verbleibenden drei Orten, an denen die Variable `toDoItemList` verwendet wird, und kommentieren Sie die entsprechenden Abschnitte aus. Entfernen Sie zudem `import java.util.ArrayList`. Nun haben Sie die speicherinterne Liste vollständig entfernt.

8.  Wir werden nun den mobilen Dienst hinzufügen. Entfernen Sie die Kommentare in den folgenden Codezeilen:

        private MobileServiceClient mClient;
        private private MobileServiceTable<ToDoItem> mToDoTable;

9.  Suchen Sie die ProgressFilter-Klasse am Ende der Datei und entfernen Sie deren Kommentare ebenfalls. Diese Klasse zeigt einen Hinweis an, wenn der MobileServiceClient Netzwerkoperationen ausführt.

10. Klicken Sie im Verwaltungsportal auf **Mobile Services** und dann auf den mobilen Dienst, den Sie gerade erstellt haben.

11. Klicken Sie auf die Registerkarte **Dashboard**, und notieren Sie sich die **Site URL**. Klicken Sie anschließend auf **Schlüssel verwalten**, und notieren Sie sich den **Anwendungsschlüssel**.

    ![][0]

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

    Dieser Code erstellt eine neue MobileServiceClient-Instanz, die für den Zugriff auf Ihren mobilen Dienst verwendet wird. Außerdem wird die MobileServiceTable-Instanz für die Proxy-Datenspeicherung im mobilen Dienst erstellt.

13. Ersetzen Sie im obigen Code `MobileServiceUrl` und `AppKey` durch die URL und den Anwendungsschlüssel Ihres mobilen Dienstes, in dieser Reihenfolge.

14. Entfernen Sie die Kommentare in diesen Zeilen in der **checkItem**-Methode:

        mToDoTable.update(item, new TableOperationCallback<ToDoItem>() {    
            public void onCompleted(ToDoItem entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    if (entity.isComplete()) {
                        mAdapter.remove(entity);
                    }
                } else {
                    createAndShowDialog(exception, "Error");    
                }
            }
        });

    Dieser Code schickt eine Elementaktualisierung an den mobilen Dienst und entfernt markierte Elemente aus dem Adapter.

15. Entfernen Sie die Kommentare in diesen Zeilen in der **addItem**-Methode:

        mToDoTable.insert(item, new TableOperationCallback<ToDoItem>() {

            public void onCompleted(ToDoItem entity, Exception exception,
                    ServiceFilterResponse response) {
                if(exception == null){
                    if (!entity.isComplete()) {
                        mAdapter.add(entity);
                    }
                } else {
                    createAndShowDialog(exception, "Error");
                }               
            }
        });

    Dieser Code erstellt ein neues Element und fügt es in die Tabelle im mobilen Dienst ein.

16. Entfernen Sie die Kommentare in diesen Zeilen in der **refreshItemsFromTable**-Methode:

        mToDoTable.where().field("complete").eq(false)
        .execute(new TableQueryCallback<ToDoItem>() {
             public void onCompleted(List<ToDoItem> result, 
                     int count, Exception exception, 
                     ServiceFilterResponse response) {

                        if(exception == null){
                            mAdapter.clear();

                            for (ToDoItem item : result) {
                                mAdapter.add(item);
                            }
                        } else {
                            createAndShowDialog(exception, "Error");
                        }
                    }
                }); 

    Dieser Code fragt den mobilen Dienst ab und gibt alle Elemente zurück, die nicht als abgeschlossen markiert sind. Die Elemente werden für die Bindung zum Adapter hinzugefügt.



  [Android-SDK für mobile Dienste]: http://go.microsoft.com/fwlink/p/?LinkID=280126
  [0]: ./media/download-android-sample-code/mobile-dashboard-tab.png
