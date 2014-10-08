1.  Öffnen Sie die Datei ToDoActivity.java im Paket-Explorer in Eclipse und fügen Sie die folgenden Import-Anweisungen hinzu.

        import com.microsoft.windowsazure.mobileservices.MobileServiceUser;
        import com.microsoft.windowsazure.mobileservices.MobileServiceAuthenticationProvider;
        import com.microsoft.windowsazure.mobileservices.UserAuthenticationCallback;

2.  Fügen Sie der **ToDoActivity**-Klasse die folgende Methode hinzu:

        private void authenticate() {

            // Login using the Google provider.
            mClient.login(MobileServiceAuthenticationProvider.Google,
                    new UserAuthenticationCallback() {

                        @Override
                        public void onCompleted(MobileServiceUser user,
                                Exception exception, ServiceFilterResponse response) {

                            if (exception == null) {
                                createAndShowDialog(String.format(
                                                "You are now logged in - %1$2s",
                                                user.getUserId()), "Success");
                                createTable();
                            } else {
                                createAndShowDialog("You must log in. Login Required", "Error");
                            }
                        }
                    });
        }

    Diese neue Methode erledigt den Authentifizierungsprozess. Der Benutzer wird mithilfe einer Google-Anmeldung authentifiziert. Ein Dialogfeld mit der ID des authentifizierten Benutzers wird eingeblendet. Ohne erfolgreiche Authentifizierung können Sie nicht fortfahren.

    <div class="dev-callout"><b>Hinweis</b>
<p>Falls Sie einen anderen Identit&auml;tsanbieter als Google verwenden, &auml;ndern Sie den an die <strong>login</strong>-Methode &uuml;bergebenen Wert auf einen der folgenden Werte: <em>MicrosoftAccount</em>, <em>Facebook</em>, <em>Twitter</em> oder <em>windowsazureactivedirectory</em>.</p>
</div>

3.  Fügen Sie in der **onCreate**-Methode die folgende Codezeile im Anschluss an den Code hinzu, der das `MobileServiceClient`-Objekt instanziiert.

        authenticate();

    Dieser Aufruf startet den Authentifizierungsprozess.

4.  Verschieben Sie den verbleibenden Code nach `authenticate();` in der **onCreate**-Methode in eine neue **createTable**-Methode mit dem folgenden Code:

        private void createTable() {

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

5.  Klicken Sie im Menü **Ausführen** auf **Ausführen**, um die App zu starten und sich mit dem Identitätsanbieter Ihrer Wahl anzumelden.

    Wenn Sie sich erfolgreich angemeldet haben, sollte die App fehlerfrei ausgeführt werden, und Sie sollten Mobile Services abfragen und Daten aktualisieren können.


