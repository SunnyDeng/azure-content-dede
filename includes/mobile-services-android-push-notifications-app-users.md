
Als Nächstes müssen Sie den Zeitpunkt für die Benachrichtigungsregistrierung ändern, um sicherzustellen, dass der Benutzer vor dem Registrierungsversuch authentifiziert wird.


1. Öffnen Sie die Datei "ToDoActivity.java" im Projektexplorer in Android Studio, und suchen Sie die `onCreate`-Methode. Verschieben Sie folgenden Code aus der `onCreate`-Methode an den Anfang der `createTable`-Methode.

        NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);

     Die `createTable`-Methode wird nach Abschluss der `authenticate`-Methode aufgerufen. Die gesamte `createTable`-Methode sollte etwa wie folgt aussehen.

        private void createTable() {
        
            NotificationsManager.handleNotifications(this, SENDER_ID, MyHandler.class);
        
            // Get the Mobile Service Table instance to use
            mToDoTable = mClient.getTable(ToDoItem.class);
            
            mTextNewToDo = (EditText) findViewById(R.id.textNewToDo);
            
            // Create an adapter to bind the items with the view
            mAdapter = new ToDoItemAdapter(this, R.layout.row_list_to_do);
            ListView listViewToDo = (ListView) findViewById(R.id.listViewToDo);
            listViewToDo.setAdapter(mAdapter);
            
            // Load the items from the Mobile Service
            refreshItemsFromTable();
        }	



<!--HONumber=52-->