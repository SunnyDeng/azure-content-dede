
1. Klicken Sie in Visual Studio im Projektmappen-Explorer mit der rechten Maustaste auf das Dienstprojekt, und klicken Sie auf **Neue Instanz starten** im Kontextmenü **Debuggen**.

    ![start mobile service project locally](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/vs-start-debug-service-project.png)

    Visual Studio öffnet die Standardwebseite für Ihren Dienst. Standardmäßig hostet Visual Studio Ihren mobilen Dienst lokal in IIS Express.

2. Klicken Sie mit der rechten Maustaste auf das IIS Express-Symbol in der Windows-Taskleiste, und überprüfen Sie, ob Ihr mobiler Dienst gestartet wurde.

	 ![verify the mobile service in the taskbar](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/iis-express-tray.png)

3. Klicken Sie auf der Startseite Ihres mobilen Dienstes auf **Testen Sie es**.

    ![mobile service start up page](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-welcome-page.png)

    Daraufhin wird die API-Dokumentationsseite angezeigt, die Sie zum Testen des mobilen Diensts verwenden können.

	>[AZURE.NOTE]Für den Zugriff auf diese Seite ist keine Authentifizierung erforderlich, wenn die Ausführung lokal erfolgt. Bei Ausführung in Azure müssen Sie den Anwendungsschlüssel als Kennwort (ohne Benutzernamen) angeben, um auf diese Seite zugreifen zu können.

4. Klicken Sie auf den Link **GET-Tabellen/TodoItem**.

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-api-documentation-page.png)
   	
	Daraufhin wird die GET-Antwortseite für die API angezeigt.

5. Klicken Sie auf **Probieren Sie dies aus**, und klicken Sie dann auf **Senden**.
 
	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-todoitems.png)

	Dadurch wird eine GET-Anforderung an den lokalen mobilen Dienst gesendet, die zur Rückgabe aller Zeilen in der TodoItem-Tabelle auffordert. Da die Tabelle vom Initialisierer mit Startwerten belegt wird, werden im Hauptteil der Antwortnachricht zwei TodoItem-Objekte zurückgegeben. Weitere Informationen zu Initialisierern finden Sie unter [Ändern des Datenmodells eines mobilen .NET-Back-End-Dienstes](./de-de/documentation/articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations/).

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-response.png)


<!--HONumber=42-->
