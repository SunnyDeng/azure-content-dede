
Wechseln Sie zurück zu Visual Studio, und wählen Sie das Client-App-Projekt mit freigegebenem Code aus (die Bezeichnung lautet z. B. "`<your app name>.Shared`").

1. Erweitern Sie die Datei **App.xaml**, und öffnen Sie dann die Datei **App.xaml.cs**. Suchen Sie die Deklaration des `MobileService`-Members, der mit der localhost-URL initialisiert wurde. Kommentieren Sie diese Deklaration (mit `CTRL+K,CTRL+C`) aus, und heben Sie die Auskommentierung der Deklaration auf (`CTRL+K,CTRL+U`), die mit Ihrem gehosteten Dienst verbunden ist:

        // This MobileServiceClient has been configured to communicate with your local
        // test project for debugging purposes.
        //public static MobileServiceClient MobileService = new MobileServiceClient(
        //    "http://localhost:58454"
        //);

        // This MobileServiceClient has been configured to communicate with the Azure Mobile Service and
        // Azure Gateway using the application key. You're all set to start working with your Mobile Service!
        public static MobileServiceClient MobileService = new MobileServiceClient(
            "https://mymobileapp-code.azurewebsites.net",
            "https://myresourcegroupgateway.azurewebsites.net/Microsoft.Azure.AppService.ApiApps.Gateway",
            "XXXX-APPLICATION-KEY-XXXXX"
        );

2. Drücken Sie F5, um das Projekt neu zu erstellen, und starten Sie die Windows Store-App, die Ihr Standard-Startprojekt sein sollte.

2. Geben Sie in der App einen beschreibenden Text, zum Beispiel *Complete the tutorial*, unter **Insert a TodoItem** ein. Klicken Sie dann auf **Save**.

	![](./media/app-service-mobile-windows-universal-test-app-preview/mobile-quickstart-startup.png)

	Dadurch wird eine POST-Anforderung an das neue, in Azure gehosteten mobile App-Back-End gesendet.

3. Beenden Sie das Debuggen, und ändern Sie das Standardstartprojekt in der universellen Windows-Lösung in der Windows Phone Store-App (klicken Sie mit der rechten Maustaste auf das `<your app name>.WindowsPhone`-Projekt, und klicken Sie auf **Als Startprojekt festlegen**), und drücken Sie erneut F5.

	![](./media/app-service-mobile-windows-universal-test-app-preview/mobile-quickstart-completed-wp8.png)

	Beachten Sie, dass nach dem Starten der Windows-App die im vorhergehenden Schritt gespeicherte Daten aus der mobilen App geladen werden.

<!---HONumber=July15_HO4-->