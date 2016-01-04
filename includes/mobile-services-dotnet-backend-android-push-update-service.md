
1. Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **Controllers** im mobilen Dienstprojekt. Öffnen Sie „TodoItemController.cs“. Fügen Sie am Anfang der Datei die folgenden `using`-Anweisungen hinzu:

		using System;
		using System.Collections.Generic;

2. Ersetzen Sie die `PostTodoItem`-Methode durch den folgenden Code:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            Dictionary<string, string> data = new Dictionary<string, string>()
            {
                { "message", item.Text}
            };
            GooglePushMessage message = new GooglePushMessage(data, TimeSpan.FromHours(1));

            try
            {
                var result = await Services.Push.SendAsync(message);
                Services.Log.Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                Services.Log.Error(ex.Message, null, "Push.SendAsync Error");
            }
            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

    Dieser Code sendet eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags), nachdem ein todo-Eintrag eingefügt wurde. Falls ein Fehler auftritt, wird vom Code ein Fehlerprotokolleintrag hinzugefügt, der auf der Registerkarte **Protokolle** des mobilen Diensts im [klassischen Azure-Portal](https://manage.windowsazure.com/) angezeigt werden kann.

3. Veröffentlichen Sie das Projekt für den mobilen Service erneut auf Azure.

<!---HONumber=AcomDC_1203_2015-->