1.  Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **Controllers** im mobilen Dienstprojekt. Öffnen Sie die Datei "TodoItemController.cs", und aktualisieren Sie die `PostTodoItem`-Methodendefinition mithilfe des folgenden Codes:

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            TodoItem current = await InsertAsync(item);

            // Create a WNS native toast.
            WindowsPushMessage message = new WindowsPushMessage();

            // Define the XML paylod for a WNS native toast notification 
            // that contains the text of the inserted item.
            message.XmlPayload = @"<?xml version=""1.0"" encoding=""utf-8""?>" +
                                 @"<toast><visual><binding template=""ToastText01"">" +
                                 @"<text id=""1"">" + item.Text + @"</text>" +
                                 @"</binding></visual></toast>";
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

    Dieser Code sendet eine Pushbenachrichtigung (mit dem Text des eingefügten Eintrags), nachdem ein todo-Eintrag eingefügt wurde. Falls ein Fehler auftritt, wird vom Code ein Fehlerprotokolleintrag hinzugefügt, der auf der Registerkarte **Protokolle** des mobilen Diensts im Verwaltungsportal angezeigt werden kann.

    > [WACOM.NOTE] Sie können Vorlagenbenachrichtigungen verwenden, um eine einzelne Pushbenachrichtigung an Clients auf mehreren Plattformen zu senden. Informationen finden Sie unter [Unterstützen mehrerer Geräteplattformen durch einen einzelnen mobilen Dienst][Unterstützen mehrerer Geräteplattformen durch einen einzelnen mobilen Dienst].

2.  das Projekt für den mobilen Service erneut auf Azure veröffentlichen.

  [Unterstützen mehrerer Geräteplattformen durch einen einzelnen mobilen Dienst]: /de-de/documentation/articles/mobile-services-how-to-use-multiple-clients-single-service/#push
