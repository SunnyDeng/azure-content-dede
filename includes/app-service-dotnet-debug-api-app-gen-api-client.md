## Generieren eines API-App-Clients 

Die API-App-Tools in Visual Studio erleichtern das Generieren von C#-Code, der Ihre Azure-API-Apps aus Desktop-, Store und mobilen Apps aufruft.

1. Öffnen Sie in Visual Studio die Projektmappe mit der API-App aus dem Thema [Erstellen einer API-App](app-service-dotnet-create-api-app.md). 

2. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe, und wählen Sie **Hinzufügen** > **Neues Projekt**.

	![Neues Projekt hinzufügen](./media/app-service-dotnet-debug-api-app-gen-api-client/01-add-new-project-v3.png)

3. Führen Sie im Dialogfeld **Neues Projekt hinzufügen** die folgenden Schritte aus:

	1. Wählen Sie die Kategorie **Windows Desktop**.
	
	2. Wählen Sie die Projektvorlage **Konsolenanwendung**.
	
	3. Benennen Sie das Projekt.
	
	4. Klicken Sie auf **OK**, um das neue Projekt in Ihrer vorhandenen Projektmappe zu generieren.
	
	![Neues Projekt hinzufügen](./media/app-service-dotnet-debug-api-app-gen-api-client/02-contact-list-console-project-v3.png)

4. Klicken Sie mit der rechten Maustaste auf das neu erstellte Konsolenanwendungsprojekt, und wählen Sie **Hinzufügen** > **Azure API-App-Client**.

	![Neuen Client hinzufügen](./media/app-service-dotnet-debug-api-app-gen-api-client/03-add-azure-api-client-v3.png)
	
5. Führen Sie im Dialogfeld **Microsoft Azure API-App-Client hinzufügen** die folgenden Schritte aus:

	1. Wählen Sie die Option **Herunterladen** aus. 
	
	2. Wählen Sie in der Dropdownliste die API-App aus, die Sie zuvor erstellt haben.
	
	3. Klicken Sie auf **OK**.

	![Bildschirm zur Generierung](./media/app-service-dotnet-debug-api-app-gen-api-client/04-select-the-api-v3.png)

	Der Assistent lädt die API-Metadatendatei herunter und generiert eine typisierte Schnittstelle zum Aufrufen der API-App.

	![Generierungsvorgang wird ausgeführt](./media/app-service-dotnet-debug-api-app-gen-api-client/05-metadata-downloading-v3.png)

	Nachdem die Codegenerierung abgeschlossen ist, wird im Projektmappen-Explorer ein neuer Ordner mit dem Namen der API-App angezeigt. Dieser Ordner enthält den Code, der den Client und die Datenmodelle implementiert.

	![Generierung abgeschlossen](./media/app-service-dotnet-debug-api-app-gen-api-client/06-code-gen-output-v3.png)

6. Öffnen Sie die Datei **Program.cs** aus dem Projektstamm, und ersetzen Sie die Methode **Main** durch den folgenden Code:

		static void Main(string[] args)
	    {
	        var client = new ContactsList();
	
	        // Send GET request.
	        var contacts = client.Contacts.Get();
	        foreach (var c in contacts)
	        {
	            Console.WriteLine("{0}: {1} {2}",
	                c.Id, c.Name, c.EmailAddress);
	        }
	
	        // Send POST request.
			client.Contacts.Post(new Models.Contact
		    {
		        EmailAddress = "lkahn@contoso.com",
		        Name = "Loretta Kahn",
		        Id = 4
		    });
	
	        Console.WriteLine("Finished");
	        Console.ReadLine();
	    }

## Testen des API-App-Clients

Nachdem die API-App codiert wurde, müssen Sie den Code testen.

1. Öffnen Sie den **Projektmappen-Explorer**.

2. Klicken Sie mit der rechten Maustaste auf die Konsolenanwendung, die Sie im vorherigen Abschnitt erstellt haben.

3. Wählen Sie im Kontextmenü der Konsolenanwendung **Debuggen > Neue Instanz starten**.

4. Es sollte sich ein Konsolenfenster öffnen, in dem alle Kontakte angezeigt werden.

	![Ausführen einer Konsolen-App](./media/app-service-dotnet-debug-api-app-gen-api-client/running-console-app.png)

5. Drücken Sie die **EINGABETASTE**, um das Konsolenfenster zu schließen.

<!---HONumber=58-->