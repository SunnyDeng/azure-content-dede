## <a name="update-app"></a>Aktualisieren der App zum Aufruf der benutzerdefinierten API

1.  Wir fügen neben der vorhandenen Schaltfläche eine Schaltfläche namens "Complete All" ein und verschieben beide Schaltflächen um eine Zeile nach unten. Öffnen Sie in Eclipse die Datei *res\\layout\\activity\_to\_do.xml* in Ihrem Schnellstartprojekt, suchen Sie das Element **LinearLayout**, das das Element **Button** namens `buttonAddToDo` enthält. Kopieren Sie das **LinearLayout**, und fügen Sie es direkt nach dem ursprünglichen ein. Löschen Sie das Element **Button** vom ersten **LinearLayout**.

2.  Im zweiten **LinearLayout** löschen Sie das Element **EditText**, und fügen Sie den folgenden Code direkt nach dem vorhandenen **Button**-Element hinzu:

        <Button
            android:id="@+id/buttonCompleteItem"
            android:layout_width="wrap_content"
            android:layout_height="wrap_content"
            android:onClick="completeItem"
            android:text="@string/complete_button_text" />

    Dadurch wird auf der Seite in einer separaten Zeile neben der vorhandenen Schaltfläche eine neue Schaltfläche hinzugefügt.

3.  Das zweite **LinearLayout** sieht nun so aus:

         <LinearLayout
            android:layout_width="match_parent" 
            android:layout_height="wrap_content" 
            android:background="#71BCFA"
            android:padding="6dip"  >
            <Button
                android:id="@+id/buttonAddToDo"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="addItem"
                android:text="@string/add_button_text" />
            <Button
                android:id="@+id/buttonCompleteItem"
                android:layout_width="wrap_content"
                android:layout_height="wrap_content"
                android:onClick="completeItem"
                android:text="@string/complete_button_text" />
        </LinearLayout>

4.  Öffnen Sie die Datei res\\values\\string.xml file, und fügen Sie die folgende Codezeile hinzu:

        <string name="complete_button_text">Complete All</string>

5.  Klicken Sie im Paket-Explorer mit der rechten Maustaste auf den Projektnamen im *src*-Ordner (`com.example.{your projects name}`), wählen Sie **Neu** und dann **Klasse**. Im Dialog geben Sie **MarkAllResult** in das Klassennamensfeld ein, wählen Sie OK, und ersetzen Sie die resultierende Klassendefinition durch den folgenden Code:

        import com.google.gson.annotations.SerializedName;

        public class MarkAllResult {
            @SerializedName("count")
            public int mCount;

            public int getCount() {
                return mCount;
            }

            public void setCount(int count) {
                    this.mCount = count;
            }
        }

    Diese Klasse dient zur Aufnahme des von der benutzerdefinierten API zurückgegebenen Zeilenanzahlwerts.

6.  Suchen Sie die **refreshItemsFromTable**-Methode in der Datei **ToDoActivity.java**, und stellen Sie sicher, dass die erste Codezeile wie folgt beginnt:

        mToDoTable.where().field("complete").eq(false)

    Hierdurch werden die Elemente so gefiltert, dass abgeschlossene Elemente von der Abfrage nicht zurückgegeben werden.

7.  Vergewissern Sie sich, dass **ToDoActivity.java** ApiOperationCallback im Importblock am Anfang der Datei importiert:

    import com.microsoft.windowsazure.mobileservices.ApiOperationCallback;

8.  Fügen Sie in der Datei **ToDoActivity.java** die folgende Methode hinzu:

        public void completeItem(View view) {
            mClient.invokeApi("completeAll", MarkAllResult.class, new ApiOperationCallback<MarkAllResult>() {
                @Override
                public void onCompleted(MarkAllResult result, Exception error, ServiceFilterResponse response) {
                    if (error != null) {
                        createAndShowDialog(error, "Error");
                    } else {
                        createAndShowDialog(result.getCount() + " item(s) marked as complete.", "Completed Items");
                        refreshItemsFromTable();
                    }
                }
            });
        }

    Diese Methode verarbeitet das **Click**-Ereignis für die neue Schaltfläche. Die **invokeApi**-Methode wird beim Client aufgerufen, der eine POST-Anfrage an die neue benutzerdefinierte API sendet. Das von der benutzerdefinierten API zurückgegebene Ergebnis wird in einem Meldungsdialogfeld angezeigt, ebenso wie jegliche Fehler.

## Testen der App

1.  Klicken Sie im Menü **Ausführen** auf **Ausführen**, um das Projekt im Android-Emulator zu starten.

    Dadurch wird Ihre mit dem Android-SDK erstellte App ausgeführt, die mithilfe der Clientbibliothek Elemente aus Ihrem mobilen Dienst abfragt.

2.  Geben Sie in der App in **Insert a TodoItem** Text ein, und klicken Sie dann auf **Add**.

3.  Wiederholen Sie den vorherigen Schritt, bis Sie der Liste mehrere todo-Einträge hinzugefügt haben.

4.  Klicken Sie auf die Schaltfläche **Complete All**.

    ![][0]

    Ein Meldungsdialogfeld wird angezeigt, das die Zahl der als abgeschlossen markierten Elemente angibt, und die gefilterte Abfrage wird erneut ausgeführt, um alle Elemente aus der Liste zu löschen.

  [0]: ./media/mobile-services-android-call-custom-api/mobile-custom-api-android-completed.png
