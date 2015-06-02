<properties 
	pageTitle="Debuggen einer API-App in Azure App Service" 
	description="Erfahren Sie, wie Sie mithilfe von Visual Studio eine API-App in Azure App Service debuggen." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# Debuggen einer API-App in Azure App Service 

## Übersicht

In diesem Lernprogramm debuggen Sie ASP.NET-Web-API-Code, während dieser in einer [API-App](app-service-api-apps-why-best-platform.md) in [Azure App Service](app-service-value-prop-what-is.md) ausgeführt wird. Im Lernprogramm wird die API-App verwendet, die Sie in den vorherigen Lernprogrammen in dieser Reihe [erstellt](app-service-dotnet-create-api-app.md) und [bereitgestellt](app-service-dotnet-deploy-api-app.md) haben.

Sie beginnen damit, über die Funktion **API-App-Client** von Visual Studio Clientcode zu generieren, der die bereitgestellte API-App aufruft. Anschließend debuggen Sie gleichzeitig die Client-App und die API-App, wobei die API-App live in der Cloud ausgeführt wird.

## Generieren eines API-App-Clients 

Die API-App-Tools in Visual Studio erleichtern das Generieren von C#-Code, der Ihre Azure-API-Apps aus Desktop-, Store und mobilen Apps aufruft.

1. Öffnen Sie in Visual Studio die Projektmappe mit der API-App aus dem Thema [Erstellen einer API-App](app-service-dotnet-create-api-app.md). 

2. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf die Projektmappe, und wählen Sie **Hinzufügen** > **Neues Projekt**.

	![Neues Projekt hinzufügen](./media/app-service-dotnet-remotely-debug-api-app/01-add-new-project-v3.png)

3. Führen Sie im Dialogfeld **Neues Projekt hinzufügen** die folgenden Schritte aus:

	1. Wählen Sie die Kategorie **Windows Desktop**.
	
	2. Wählen Sie die Projektvorlage **Konsolenanwendung**.
	
	3. Benennen Sie das Projekt.
	
	4. Klicken Sie auf **OK**, um das neue Projekt in Ihrer vorhandenen Projektmappe zu generieren.
	
	![Neues Projekt hinzufügen](./media/app-service-dotnet-remotely-debug-api-app/02-contact-list-console-project-v3.png)

4. Klicken Sie mit der rechten Maustaste auf das neu erstellte Konsolenanwendungsprojekt, und wählen Sie **Hinzufügen** > **Azure API-App-Client**.

	![Neuen Client hinzufügen](./media/app-service-dotnet-remotely-debug-api-app/03-add-azure-api-client-v3.png)
	
5. Führen Sie im Dialogfeld **Microsoft Azure API-App-Client hinzufügen** die folgenden Schritte aus:

	1. Wählen Sie die Option **Herunterladen** aus. 
	
	2. Wählen Sie in der Dropdownliste die API-App aus, die Sie zuvor erstellt haben.
	
	3. Klicken Sie auf **OK**.

	![Bildschirm zur Generierung](./media/app-service-dotnet-remotely-debug-api-app/04-select-the-api-v3.png)

	Der Assistent lädt die API-Metadatendatei herunter und generiert eine typisierte Schnittstelle zum Aufrufen der API-App.

	![Generierungsvorgang wird ausgeführt](./media/app-service-dotnet-remotely-debug-api-app/05-metadata-downloading-v3.png)

	Nachdem die Codegenerierung abgeschlossen ist, wird im Projektmappen-Explorer ein neuer Ordner mit dem Namen der API-App angezeigt. Dieser Ordner enthält den Code, der den Client und die Datenmodelle implementiert.

	![Generierung abgeschlossen](./media/app-service-dotnet-remotely-debug-api-app/06-code-gen-output-v3.png)

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

Nachdem die API-App codiert wurde, müssen Sie den Code im Browser testen.

1. Öffnen Sie den **Projektmappen-Explorer**.

2. Klicken Sie mit der rechten Maustaste auf die Konsolenanwendung, die Sie im vorherigen Abschnitt erstellt haben.

3. Wählen Sie im Kontextmenü der Konsolenanwendung **Debuggen > Neue Instanz starten**.

4. Es sollte sich ein Konsolenfenster öffnen, in dem alle Kontakte angezeigt werden.

	![Ausführen einer Konsolen-App](./media/app-service-dotnet-remotely-debug-api-app/running-console-app.png)

5. Drücken Sie die **EINGABETASTE**, um das Konsolenfenster zu schließen.

## Debuggen der API-App 

Nachdem wir die API-App und den zugehörigen Client codiert und getestet haben, führen wir ein Debugging durch.

1. Wählen Sie im Visual Studio-Menü **Ansicht** die Option **Server-Explorer**. 

2. Erweitern Sie im **Server-Explorer** den Knoten **Azure > App Service**.

3. Suchen Sie die Ressourcengruppe, die Sie beim Bereitstellen Ihrer API-App erstellt haben.

4. Klicken Sie unterhalb der Ressourcengruppe mit der rechten Maustaste auf den Knoten für die API-App, und wählen Sie **Debugger anfügen**.

	![Anfügen des Debuggers](./media/app-service-dotnet-remotely-debug-api-app/08-attach-debugger-v3.png)

	Der Remotedebugger versucht, eine Verbindung herzustellen. In einigen Fällen müssen Sie erneut auf **Debugger anfügen** klicken, um eine Verbindung herzustellen. Versuchen Sie es daher ggf. noch einmal.

	![Anfügen des Debuggers](./media/app-service-dotnet-remotely-debug-api-app/09-attaching-v3.png)

16. Nachdem die Verbindung hergestellt wurde, öffnen Sie die Datei **ContactsController.cs** im API-App-Projekt, und fügen Sie Haltepunkte bei den Methoden `Get` und `Post` hinzu. Sie werden eventuell zunächst nicht als aktiv angezeigt. Wenn jedoch der Remotedebugger angefügt ist, sind Sie zum Debuggen bereit.

	![Anwenden von Haltepunkten auf Controller](./media/app-service-dotnet-remotely-debug-api-app/10-breakpoints-v3.png)

17. Klicken Sie zum Debuggen im **Projektmappen-Explorer** mit der rechten Maustaste auf die Konsolen-App, und wählen Sie **Debuggen** > **Neue Instanz starten**. Jetzt können Sie die API-App remote und die Client-App lokal debuggen und den gesamten Datenfluss überwachen.

	Der folgende Screenshot zeigt den Debugger, wenn er den Haltepunkt für die `Post`-Methode erreicht. Sie sehen, dass die Kontaktdaten vom Client in ein stark typisiertes `Contact`-Objekt deserialisiert wurden.

	![Debuggen des lokalen Clients für API](./media/app-service-dotnet-remotely-debug-api-app/12-debugging-live-v3.png)

## Nächste Schritte

Durch das Remotedebuggen für API-Apps können Sie einfacher sehen, wie Ihr Code in Azure App Service ausgeführt wird. Umfangreiche Diagnose- und Debugdaten stehen Ihnen direkt in der Visual Studio-IDE für Ihre remote ausgeführten Azure API-Apps zur Verfügung.

App Service-API-Apps sind App Service-Web-Apps, die über zusätzliche Features zum Hosten von Webdiensten verfügen, sodass Sie dieselben Debugging- und Problembehandlungstools für API-Apps verwenden können, die Sie für Web-Apps nutzen. Weitere Informationen finden Sie unter [Problembehandlung einer Web-App in Azure App Service mithilfe von Visual Studio](web-sites-dotnet-troubleshoot-visual-studio.md).

Die im Rahmen dieser Lernprogrammreihe erstellte API-App ist öffentlich verfügbar und kann durch beliebige Benutzer aufgerufen werden. Weitere Informationen zum Schutz der API-App, sodass nur authentifizierte darauf zugreifen können, finden Sie unter [Schützen einer API-App: Hinzufügen der Authentifizierung über Azure Active Directory oder ein soziales Netzwerk](app-service-api-dotnet-add-authentication.md).

<!--HONumber=54-->