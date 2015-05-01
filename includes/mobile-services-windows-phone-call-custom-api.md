##<a name="update-app"></a>Aktualisieren der App zum Aufruf der benutzerdefinierten API

1. Öffnen Sie die Datei "MainPage.xaml" in Ihrem Schnellstartprojekt in Visual Studio, suchen Sie die **Schaltfläche** Element mit dem Namen `ButtonRefresh`, und ersetzen Sie es durch folgenden XAML-Code: 

        <StackPanel Grid.Row="3" Grid.ColumnSpan="2" Orientation="Horizontal">
            <Button Width="225" Name="ButtonRefresh" 
                Click="ButtonRefresh_Click">Refresh</Button>
            <Button Width="225"  Name="ButtonCompleteAll" 
                Click="ButtonCompleteAll_Click">Complete All</Button>
        </StackPanel>

	Auf diese Weise wird eine neue Schaltfläche zur Seite hinzugefügt. 

2. Öffnen Sie die Codedatei MainPage.xaml.cs, und fügen Sie den folgenden Klassendefinitionscode hinzu:

	    public class MarkAllResult
	    {
	        public int Count { get; set; }
	    }

	Diese Klasse dient zur Aufnahme des von der benutzerdefinierten API zurückgegebenen Zeilenanzahlwerts. 

3. Suchen Sie die **RefreshTodoItems**-Methode in der **MainPage**-Klasse, und stellen Sie sicher, dass  `query` definiert ist, indem Sie die folgende **Where**-Methode verwenden:

        .Where(todoItem => todoItem.Complete == false)

	Hierdurch werden die Elemente so gefiltert, dass abgeschlossene Elemente von der Abfrage nicht zurückgegeben werden.

3. Fügen Sie In der **MainPage**-Klasse die folgende Methode hinzu:

		private async void ButtonCompleteAll_Click(object sender, RoutedEventArgs e)
		{
		    string message;
		    try
		    {
		        // Asynchronously call the custom API using the POST method. 
		        var result = await App.MobileService
		            .InvokeApiAsync<MarkAllResult>("completeAll", 
		            System.Net.Http.HttpMethod.Post, null);
		        message =  result.Count + " item(s) marked as complete.";
		        RefreshTodoItems();
		    }
		    catch (MobileServiceInvalidOperationException ex)
		    {
		        message = ex.Message;                
		    }
		
		    MessageBox.Show(message);  
		}

	Diese Methode verarbeitet das **Click**-Ereignis für die neue Schaltfläche. Die **InvokeApiAsync**-Methode wird beim Client aufgerufen, der eine Anfrage an die neue benutzerdefinierte API sendet. Das von der benutzerdefinierten API erhaltene Ergebnis wird in einem Meldungsdialogfeld angezeigt.

## <a name="test-app"></a>Testen der App

1. Drücken Sie in Visual Studio die **F5**-Taste, um das Projekt neu zu erstellen, und starten Sie die App.

2. Geben Sie in der App einen Text in **Insert a TodoItem** ein, und geben Sie dann **Speichern** ein.

3. Wiederholen Sie den vorherigen Schritt, bis Sie der Liste mehrere todo-Einträge hinzugefügt haben.

4. Klicken Sie auf die **Alle abgeschlossen**-Schaltfläche.

  	![](./media/mobile-services-windows-phone-call-custom-api/mobile-custom-api-windows-phone-completed.png)

	Ein Meldungsfeld wird angezeigt, das die Zahl der als abgeschlossen markierten Elemente angibt, und die gefilterte Abfrage wird erneut ausgeführt, wodurch alle Elemente aus der Liste gelöscht werden.


<!--HONumber=52-->