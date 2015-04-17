<properties 
	pageTitle="Remotedebuggen einer API-App in Azure App Service" 
	description="Verwenden von Visual Studio zum Remotedebuggen einer API-App in Azure App Service." 
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

# Remotedebuggen einer API-App in Azure App Service 

## Übersicht

Die Remotedebugfunktionen von Visual Studio wurden erweitert und unterstützen jetzt auch API-Apps in Azure App Service. So können Sie die vertrauten Debugtools verwenden, um die Ausführung Ihres Codes in Azure zu beobachten. In diesem Thema wird veranschaulicht, wie Sie mit dem **API-App-Client** von Visual Studio Clientcode generieren, der die bereitgestellte API-App aufruft. Anschließend debuggen Sie gleichzeitig die Client-App und die API-App, wobei die API-App live in der Cloud ausgeführt wird.

Dieses ist das letzte von drei Lernprogrammen:

1. In [Erstellen einer API-App](app-service-dotnet-create-api-app.md) haben Sie ein API-App-Projekt erstellt. 
* In [Bereitstellen einer API-App](app-service-dotnet-deploy-api-app.md) haben Sie die API-App in Ihrem Azure-Abonnement bereitgestellt.
* In diesem Lernprogramm verwenden Sie Visual Studio zum Remotedebuggen des Codes, während er in Azure ausgeführt wird.

## Generieren eines API-App-Clients 

Die API-App-Tools in Visual Studio erleichtern das Generieren von C#-Code, der Ihre Azure-API-Apps von Desktop-, Store- und mobilen Apps aufruft. 

Öffnen Sie in Visual Studio die Projektmappe mit der API-Anwendung aus dem [ersten](app-service-dotnet-create-api-app.md) Lernprogramm. Klicken Sie mit der rechten Maustaste auf die Projektmappe, und wählen Sie **Hinzufügen** > **Neues Projekt**.

![Add a new project](./media/app-service-dotnet-remotely-debug-api-app/01-add-new-project-v3.png)

Wählen Sie die Kategorie **Windows-Desktop** und die Projektvorlage **Konsolenanwendung** aus.

![Add a new project](./media/app-service-dotnet-remotely-debug-api-app/02-contact-list-console-project-v3.png)

Klicken Sie mit der rechten Maustaste auf das Konsolenanwendungsprojekt, und wählen Sie **Hinzufügen** > **Azure-API-App-Client**. 

![Add a new Client](./media/app-service-dotnet-remotely-debug-api-app/03-add-azure-api-client-v3.png)
	
Wählen Sie im Dialogfeld die Option **Herunterladen** aus. Wählen Sie in der Dropdownliste die API-App aus, die Sie zuvor erstellt haben. Klicken Sie auf **OK**. 

![Generation Screen](./media/app-service-dotnet-remotely-debug-api-app/04-select-the-api-v3.png)

Der Assistent lädt die API-Metadatendatei herunter und generiert eine typisierte Schnittstelle zum Aufrufen der API-App.

![Generation Happening](./media/app-service-dotnet-remotely-debug-api-app/05-metadata-downloading-v3.png)

Nachdem die Codegenerierung abgeschlossen ist, wird im Projektmappen-Explorer ein neuer Ordner mit dem Namen der API-App angezeigt. Dieser Ordner enthält den Code, der den Client und die Datenmodelle implementiert. 

![Generation Complete](./media/app-service-dotnet-remotely-debug-api-app/06-code-gen-output-v3.png)

Öffnen Sie die Datei **Program.cs** aus dem Projektstamm, und ersetzen Sie die Methode **Main** durch den folgenden Code: 

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

Wählen Sie im Menü **Ansicht** die Option **Server-Explorer**. Erweitern Sie im Fenster des Server-Explorers den Knoten *Azure > App Service**. Suchen Sie die Ressourcengruppe, die Sie beim Bereitstellen Ihrer API-App erstellt haben. Klicken Sie mit der rechten Maustaste auf den Knoten für die API-App, und wählen Sie **Debugger anfügen**. 

![Attaching debugger](./media/app-service-dotnet-remotely-debug-api-app/08-attach-debugger-v3.png)

Der Remotedebugger versucht, eine Verbindung herzustellen. In einigen Fällen müssen Sie erneut auf **Debugger anfügen** klicken, um eine Verbindung herzustellen. Versuchen Sie es daher ggf. noch einmal.

![Attaching debugger](./media/app-service-dotnet-remotely-debug-api-app/09-attaching-v3.png)

Nachdem die Verbindung hergestellt wurde, öffnen Sie die Datei **ContactsController.cs** im API-App-Projekt, und fügen Sie Haltepunkte bei den Methoden `Get` und `Post` hinzu. Sie werden eventuell zunächst nicht als aktiv angezeigt. Wenn jedoch der Remotedebugger angefügt ist, sind Sie zum Debuggen bereit. 

![Applying breakpoints to controller](./media/app-service-dotnet-remotely-debug-api-app/10-breakpoints-v3.png)

Klicken Sie zum Debuggen mit der rechten Maustaste in die Konsolen-App im Projektmappen-Explorer, und wählen Sie **Debuggen** > **Neue Instanz starten**. Jetzt können Sie die API-App remote und die Client-App lokal debuggen und den gesamten Datenfluss überwachen. 

Der folgende Screenshot zeigt den Debugger, wenn er den Haltepunkt für die `Post`-Methode erreicht. Sie sehen, dass die Kontaktdaten vom Client in ein stark typisiertes `Contact`-Objekt deserialisiert wurde. 

![Debugging the local client to hit the API](./media/app-service-dotnet-remotely-debug-api-app/12-debugging-live-v3.png)

## Zusammenfassung

Durch das Remotedebuggen für API-Apps können Sie einfacher anzeigen, wie Ihr Code in Azure App Service ausgeführt wird. Umfangreiche Diagnose- und Debugdaten stehen Ihnen direkt in der Visual Studio-IDE für Ihre remote ausgeführten Azure-API-Apps zur Verfügung.


<!--HONumber=49-->