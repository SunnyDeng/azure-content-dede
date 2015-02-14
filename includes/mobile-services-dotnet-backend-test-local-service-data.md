In diesem Abschnitt verwenden Sie Visual Studio, um den mobilen Dienst lokal auf Ihrer Entwicklungsarbeitsstation in IIS Express zu hosten. Anschließend testen Sie die App und den Backend-Dienst.


1. Drücken Sie in Visual Studio auf die Taste F7, oder klicken Sie auf **Projektmappe erstellen** im Menü **Erstellen**, um die Windows Store-App und den mobilen Dienst im Menü zu erstellen. Vergewissern Sie sich im Ausgabefenster von Visual Studio, dass beide Projekte ohne Fehler erstellt werden.

2. Drücken Sie in Visual Studio auf die Taste F5, oder klicken Sie auf **Debuggen starten** im Menü **Debuggen**, um die App auszuführen und den mobilen Dienst lokal in IIS Express zu hosten. 

 
3. Geben Sie einen neuen Text für "todoitem" ein. Klicken Sie dann auf **Speichern**. Daraufhin wird ein neues todoItem in die Datenbank eingefügt, die vom mobilen Dienst, der lokal in IIS Express gehostet wird, erstellt wurde. 

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/new-local-todoitem.png)

4. Klicken Sie auf das Kontrollkästchen für eines der Elemente, um es als abgeschlossen zu markieren.

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/local-item-checked.png)

5. In Visual Studio können Sie die Änderungen in der Datenbank anzeigen, die für den Backend-Dienst erstellt wurde, indem Sie den Server-Explorer öffnen und die Datenverbindungen erweitern. Klicken Sie mit der rechten Maustaste auf die Tabelle "TodoItems" unter **MS_TableConnectionString**, und klicken Sie auf **Tabellendaten anzeigen**.

    ![](./media/mobile-services-dotnet-backend-test-local-service-data/vs-show-local-table-data.png)


<!--HONumber=42-->
