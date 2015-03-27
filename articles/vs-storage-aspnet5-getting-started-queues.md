<properties 
	pageTitle="Erste Schritte mit Azure Storage" 
	description="Erste Schritte mit Azure-Warteschlangenspeicher in einem ASP.NET 5-Projekt in Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/02/2015" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Erste Schritte](/documentation/articles/vs-storage-aspnet5-getting-started-queues/)
> - [Was ist passiert?](/documentation/articles/vs-storage-aspnet5-what-happened/)

## Erste Schritte mit Azure Storage (ASP.NET 5-Projekte)

> [AZURE.SELECTOR]
> - [Blobs](/documentation/articles/vs-storage-aspnet5-getting-started-blobs/)
> - [Warteschlangen](/documentation/articles/vs-storage-aspnet5-getting-started-queues/)
> - [Tabellen](/documentation/articles/vs-storage-aspnet5-getting-started-tables/)

Der Azure-Warteschlangenspeicher ist ein Dienst zum Speichern einer großen Anzahl von Nachrichten, auf die weltweit mit authentifizierten Aufrufen mithilfe von HTTP oder HTTPS zugegriffen werden kann. Eine einzelne Warteschlangennachricht kann bis zu 64 KB groß sein, und eine Warteschlange kann Millionen von Nachrichten enthalten. Deren Anzahl ist nur durch die Kapazität des Speicherkontos begrenzt. Weitere Informationen finden Sie unter [Verwenden des Warteschlangenspeichers in .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-queues/ "How to use Queue Storage from .NET").

Für den programmgesteuerten Zugriff auf Warteschlangen in ASP.NET 5-Projekten müssen Sie die folgenden Elemente hinzuzufügen, wenn sie nicht bereits vorhanden sind.

1. Fügen Sie die folgenden Codenamespace-Deklarationen am Anfang aller C#-Dateien hinzu, mit denen Sie programmgesteuert auf Azure Storage zugreifen möchten.

		using Microsoft.Framework.ConfigurationModel;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;
		using System.Threading.Tasks;

2. Verwenden Sie den folgenden Code, um die Konfigurationseinstellung abzurufen.

		 Configuration = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

#####Abrufen der Verbindungszeichenfolge des Speichers
Bevor Sie eine Warteschlange verwenden können, müssen Sie die Verbindungszeichenfolge für das Speicherkonto abrufen, in dem die Warteschlangen gespeichert werden. Sie können den Typ **CloudStorageAccount** verwenden, um Ihre Speicherkontoinformationen darzustellen. Wenn Sie ein ASP.NET 5-Projekt verwenden, können Sie die get-Methode des Konfigurationsobjekts verwenden, um Ihre Verbindungszeichenfolge für den Speicher und die Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen. Der folgende Code zeigt dies.

**HINWEIS:** Die APIs, welche die Aufrufe an Azure Storage in ASP.NET 5 ausführen, sind asynchron. Weitere Informationen finden Sie unter [Asynchrone Programmierung mit Async und Await](http://msdn.microsoft.com/library/hh191443.aspx). Im folgenden Code wird davon ausgegangen, dass Async-Programmierungsmethoden verwendet werden.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

#####Erstellen einer Warteschlange
Mit einem **CloudQueueClient**-Objekt können Sie Referenzobjekte für Warteschlangen abrufen. Der folgende Code erstellt ein **CloudQueueClient**-Objekt. Der gesamte Code in diesem Thema verwendet eine in der Dienstkonfiguration der Azure-Anwendung gespeicherte Verbindungszeichenfolge für den Speicher. Es gibt auch andere Möglichkeiten zum Erstellen eines **CloudStorageAccount**-Objekts. Ausführliche Informationen dazu finden Sie in der [CloudStorageAccount](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx "CloudStorageAccount")-Dokumentation.

**HINWEIS:** Die APIs, welche die Aufrufe an Azure Storage in ASP.NET 5 ausführen, sind asynchron. Weitere Informationen finden Sie unter [Asynchrone Programmierung mit Async und Await](http://msdn.microsoft.com/library/hh191443.aspx). Im folgenden Code wird davon ausgegangen, dass Async-Programmierungsmethoden verwendet werden.

	// Create the queue client.
	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Verwenden Sie das **queueClient**-Objekt, um einen Verweis auf die gewünschte Warteschlange abzurufen. Der Code versucht, auf eine Warteschlange namens "myqueue" zu verweisen. Wird keine Warteschlange mit diesem Namen gefunden, wird die Warteschlange erstellt.

	// Get a reference to a queue named "myqueue".
	CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// If the queue isn't already there, then create it.
	await queue.CreateIfNotExistsAsync();

**HINWEIS:** Verwenden Sie all diesen Code vor dem Code in den folgenden Abschnitten.

#####Einfügen einer Nachricht in eine Warteschlange
Um eine Nachricht in eine vorhandene Warteschlange einzufügen, erstellen Sie zuerst ein neues **CloudQueueMessage**-Objekt. Rufen Sie dann die Methode AddMessageAsync() auf. Ein **CloudQueueMessage**-Objekt kann aus einer Zeichenfolge (im UTF-8-Format) oder aus einem Bytearray erstellt werden. Dieser Code erstellte eine Warteschlange (falls noch nicht vorhanden) und fügt die Nachricht "Hello, World" ein:

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	await queue.AddMessageAsync(message);

#####Einsehen der nächsten Nachricht
Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die Methode PeekMessageAsync() aufrufen.

	// Peek at the next message in the queue.
	CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display the message.
	CloudQueueMessage peekedMessage = await queue.PeekMessageAsync();

#####Entfernen der nächsten Nachricht aus der Warteschlange
Ihr Code kann eine Nachricht in zwei Schritten aus der Warteschlange entfernen. 


1. Rufen Sie GetMessageAsync() auf, um die nächste Nachricht in einer Warteschlange abzurufen. Eine von GetMessageAsync() zurückgegebene Nachricht ist für anderen Code nicht mehr sichtbar, der Nachrichten aus dieser Warteschlange liest. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. 
2.	Wenn Sie die Nachricht endgültig aus der Warteschlange entfernen möchten, rufen Sie DeleteMessageAsync() auf. 

Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers nicht durchgeführt werden kann. Der folgende Code ruft DeleteMessageAsync() direkt nach der Verarbeitung der Nachricht auf.

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();

	// Process the message in less than 30 seconds, and then delete the message.
	await queue.DeleteMessageAsync(retrievedMessage);

[Weitere Informationen zu Azure Storage](http://azure.microsoft.com/documentation/services/storage/)
Siehe auch [Durchsuchen von Speicherressourcen im Server-Explorer](http://msdn.microsoft.com/library/azure/ff683677.aspx) und [ASP.NET 5](http://www.asp.net/vnext).

<!--HONumber=47-->
