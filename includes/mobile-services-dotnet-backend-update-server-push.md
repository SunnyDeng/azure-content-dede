1.  Erweitern Sie im Projektmappen-Explorer in Visual Studio den Ordner **Controllers** im mobilen Dienstprojekt. Öffnen Sie die Datei "TodoItemController.cs", und aktualisieren Sie die `PostTodoItem`-Methodendefinition mithilfe des folgenden Codes:

         public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
         {
             TodoItem current = await InsertAsync(item);
             WindowsPushMessage message = new WindowsPushMessage();
             message.XmlPayload = @"<
         xml version=""1.0"" encoding=""utf-8""
         >" +
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


