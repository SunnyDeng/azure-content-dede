Es empfiehlt sich, die Länge der Daten, die von Benutzern übermittelt werden, zu überprüfen. In diesem Abschnitt fügen Sie Code zum mobilen Service hinzu, der die Länge von Zeichenfolgendaten überprüft, die an den mobilen Service gesendet werden, und der Zeichenfolgen ablehnt, die zu lang sind, in diesem Fall länger als zehn Zeichen.

1.  Starten Sie Visual Studio mit der Option **Als Administrator ausführen**, und öffnen Sie die Lösung, die das Projekt für den mobilen Service enthält, mit dem Sie sich im Lernprogramm [Erste Schritte][Erste Schritte] oder [Erste Schritte mit Daten][Erste Schritte mit Daten] beschäftigt haben.

2.  Erweitern Sie im Fenster des Projektmappen-Explorers das Aufgabenlisten-Serviceprojekt, und erweitern Sie **Controller**. Öffnen Sie die Datei TodoItemController.cs, die Bestandteil des Projekts für den mobilen Service ist.

    ![][]

3.  Ersetzen Sie die `PostTodoItem`-Methode durch die folgende Methode, die überprüft, ob die Textzeichenfolge nicht länger als zehn Zeichen ist. Für Elemente mit einer Textlänge von über zehn Zeichen gibt die Methode den HTTP-Statuscode 400 Bad Request inklusive einer Beschreibung zurück.

        public async Task<IHttpActionResult> PostTodoItem(TodoItem item)
        {
            if (item.Text.Length > 10)
            {
                return BadRequest("The Item's Text length must not be greater than 10.");
            }
            else
            {
                TodoItem current = await InsertAsync(item);
                return CreatedAtRoute("Tables", new { id = current.Id }, current);
            } 
        }

4.  Klicken Sie mit der rechten Maustaste auf das Serviceprojekt, und klicken Sie dann auf **Build**, um das Projekt für den mobilen Service zu erstellen. Vergewissern Sie sich, dass keine Fehler aufgetreten sind.

    ![][1]

5.  Klicken Sie mit der rechten Maustaste auf das Serviceprojekt, und klicken Sie dann auf **Veröffentlichen**. Veröffentlichen Sie den mobilen Service auf Ihrem Microsoft Azure-Konto mit den zuvor im Lernprogramm [Erste Schritte][Erste Schritte] oder [Erste Schritte mit Daten][Erste Schritte mit Daten] verwendeten Veröffentlichungseinstellungen.

    > [WACOM.NOTE] Alternativ können Sie bei in IIS Express lokal gehostetem Service testen. Weitere Informationen finden Sie im Lernprogramm [Erste Schritte mit Daten][Erste Schritte mit Daten].

    ![][2]

<!-- URLs. -->

  [Erste Schritte]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started/
  [Erste Schritte mit Daten]: /de-de/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-data/
  []: ./media/mobile-services-dotnet-backend-add-validation/mobile-services-open-todoitemcontroller.png
  [1]: ./media/mobile-services-dotnet-backend-add-validation/mobile-services-build-dotnet-service.png
  [2]: ./media/mobile-services-dotnet-backend-add-validation/mobile-services-publish-dotnet-service.png
