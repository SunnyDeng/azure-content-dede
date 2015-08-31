1. Erweitern Sie im **Projektmappen-Explorer** von Visual Studio den Ordner **Controller** im Projekt für das mobile Back-End. Öffnen Sie **TodoItemController.cs**. Fügen Sie am Anfang der Datei die folgenden `using`-Anweisungen hinzu:

        using Microsoft.Azure.Mobile.Server.Notifications;


2. Ersetzen Sie die `PostTodoItem`-Methode durch den folgenden Code:
        
        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            HttpConfiguration config = this.Configuration;

            TemplatePushMessage message = new TemplatePushMessage();
            message.Add("message", item.Text);

            try
            {
                var client = new PushClient(config);
                var result = await client.SendAsync(message);

                config.Services.GetTraceWriter().Info(result.State.ToString());
            }
            catch (System.Exception ex)
            {
                config.Services.GetTraceWriter().Error(ex.Message, null, "Push.SendAsync Error");
            }

            return CreatedAtRoute("Tables", new { id = current.Id }, current);
        }

<!---HONumber=August15_HO8-->