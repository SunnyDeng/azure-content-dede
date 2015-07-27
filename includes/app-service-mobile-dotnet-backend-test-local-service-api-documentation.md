
1. Klicken Sie im Projektmappen-Explorer in Visual Studio mit der rechten Maustaste auf das Dienstprojekt, und klicken Sie im Kontextmenü **Debuggen** auf **Neue Instanz starten**.

    Visual Studio öffnet die Standardwebseite für Ihren Dienst. Standardmäßig hostet Visual Studio Ihr mobiles App-Back-End lokal in IIS Express.

2. Klicken Sie mit der rechten Maustaste auf das IIS Express-Symbol in der Windows-Taskleiste, und überprüfen Sie, ob Ihr mobiles App-Back-End gestartet wurde.

	 ![Mobilen Dienst in der Taskleiste überprüfen](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/iis-express-tray.png)

3. Klicken Sie auf der Startseite Ihres .NET-Back-Ends auf **Try it out**.

    Daraufhin wird die API-Dokumentationsseite angezeigt, die Sie zum Testen der mobilen App verwenden können.

	>[AZURE.NOTE]Für den Zugriff auf diese Seite ist keine Authentifizierung erforderlich, wenn die Ausführung lokal erfolgt. Bei Ausführung in Azure müssen Sie den Anwendungsschlüssel als Kennwort (ohne Benutzernamen) angeben, um auf diese Seite zugreifen zu können.

4. Klicken Sie auf den Link **GET tables/TodoItem**.

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-api-documentation-page.png)
   	
	Daraufhin wird die GET-Antwortseite für die API angezeigt.

5. Klicken Sie auf **Ausprobieren** und dann auf **Senden**.
 
	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-todoitems.png)

	Dadurch wird eine GET-Anforderung an das lokale mobile App-Back-End gesendet, die zur Rückgabe aller Zeilen in der TodoItem-Tabelle auffordert. Da die Tabelle vom Initialisierer mit Startwerten belegt wird, werden im Hauptteil der Antwortnachricht zwei TodoItem-Objekte zurückgegeben. Weitere Informationen zu Initialisierern finden Sie unter [Ändern des Datenmodells eines mobilen .NET-Backend-Diensts](../articles/mobile-services-dotnet-backend-how-to-use-code-first-migrations.md).

	![](./media/mobile-services-dotnet-backend-test-local-service-api-documentation/service-try-this-out-get-response.png)

<!---HONumber=July15_HO3-->