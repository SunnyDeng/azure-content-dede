<properties 
	pageTitle="Erste Schritte mit Azure Storage" 
	description="Erste Schritte mit Azure-Warteschlangenspeicher in einem Cloud-Dienstprojekt in Visual Studio" 
	services="storage" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor="tglee"/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/22/2015" 
	ms.author="patshea123"/>

# Erste Schritte mit Azure Storage \(Cloud Service-Projekte\)

> [AZURE.SELECTOR]
> - [Getting Started](vs-storage-cloud-services-getting-started-queues.md)
> - [What Happened](vs-storage-cloud-services-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-cloud-services-getting-started-blobs.md)
> - [Queues](vs-storage-cloud-services-getting-started-queues.md)
> - [Tables](vs-storage-cloud-services-getting-started-tables.md)

Der Azure-Warteschlangenspeicher ist ein Dienst zum Speichern einer großen Anzahl von Nachrichten, auf die weltweit mit authentifizierten Aufrufen mithilfe von HTTP oder HTTPS zugegriffen werden kann. Eine einzelne Warteschlangennachricht kann bis zu 64 KB groß sein, und eine Warteschlange kann Millionen von Nachrichten enthalten. Deren Anzahl ist nur durch die Kapazität des Speicherkontos begrenzt.

Dieser Artikel beschreibt, wie die ersten Schritte des Verwendens von Azure-Tabellenspeicher in Visual Studio aussehen, nachdem Sie über das Visual Studio-Dialogfeld **Verbundene Dienste hinzufügen** in einem Clouddienst-Projekt ein Azure-Speicherkonto erstellt oder auf ein solches Konto verwiesen haben. Bei einem **Verbundene Dienste hinzufügen**-Vorgang werden die entsprechenden NuGet-Pakete installiert, um auf Azure-Speicher in Ihrem Projekt zuzugreifen, und wird die Verbindungszeichenfolge für das Speicherkonto in Ihren Konfigurationsdateien des Projekts hinzugefügt.

Damit Sie beginnen können, müssen Sie zuerst eine Azure-Warteschlage in Ihrem Speicherkonto erstellen. Es wird gezeigt, wie Sie aus Visual Studio **Server-Explorer** eine Warteschlange erstellen. Möglicherweise bevorzugen Sie es, eine Warteschlange in Code zu erstellen. Auch dies wird gezeigt.

Außerdem wird gezeigt, wie Sie grundlegende Warteschlangenvorgänge, etwa Hinzufügen, Ändern, Lesen und Entfernen von Warteschlangennachrichten ausführen. Die Beispiele sind in C\# geschrieben und greifen auf die Azure-Speicherclientbibliothek für .NET zurück. Weitere Informationen zu ASP.NET finden Sie unter [ASP.NET](http://www.asp.net).

##Erstellen einer Warteschlange im Server-Explorer
[AZURE.INCLUDE [vs-create-queue-in-server-explorer](../../includes/vs-create-queue-in-server-explorer.md)]

##Zugriff auf Warteschlangen in Code

Um auf Warteschlangen in ASP.NET-Projekten zuzugreifen, müssen Sie die folgenden Elemente zu jeder C\#-Quelldatei hinzufügen, in der auf Azure-Warteschlangenspeicher zugegriffen wird.

1. Vergewissern Sie sich, dass die Namespace-Deklarationen am Anfang der C\#-Datei diese `using`-Anweisungen enthalten.

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Queue;

2. Rufen Sie ein **CloudStorageAccount**-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um Ihre Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage account name>_AzureStorageConnectionString"));

3. Rufen Sie ein **CloudQueueClient**-Objekt ab, um auf die Warteschlangenobjekte in Ihrem Speicherkonto zu verweisen.

	    // Create the table client.
    	CloudQuecClient queueClient = storageAccount.CreateCloudTableClient();

4. Abrufen eines **CloudQueue**-Objekts, das auf eine bestimmte Warteschlange verweist.

    	// Get a reference to a table named "messageQueue"
	    CloudTable messageQueue = queueClient.GetQueueReference("messageQueue");


**HINWEIS:** Verwenden Sie den gesamten obigen Code vor dem Code in den folgenden Beispielen.

###Erstellen einer Warteschlange in Code

Um die Azure-Warteschlange in Code statt mit Visual Studio **Server-Explorer** zu erstellen, fügen Sie einfach einen Aufruf von `CreateIfNotExists()` hinzu.

	// Create the CloudTable if it does not exist
	queue.CreateIfNotExists();

##Hinzufügen von Nachrichten zu einer Warteschlange

Wenn Sie eine Nachricht in eine vorhandene Warteschlange einfügen möchten, erstellen Sie zuerst ein neues **CloudQueueMessage**-Objekt, und rufen Sie dann die „AddMessage\(\)“-Methode auf.

Ein **CloudQueueMessage**-Objekt kann aus einer Zeichenfolge \(im UTF-8-Format\) oder aus einem Bytearray erstellt werden.

Hier ist ein Beispiel, dass die Nachricht "Hello, World" eingefügt.

	// Get a reference to the **CloudQueue** object named 'messageQueue' as described in "Access a queue in code"

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	messageQueue.AddMessage(message);

##Lesen von Nachrichten in einer Warteschlange

Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die Methode PeekMessage\(\) aufrufen.

    // Get a reference to the **CloudQueue** object named 'messageQueue' as described in "Access a queue in code"

	// Peek at the next message
    CloudQueueMessage peekedMessage = messageQueue.PeekMessage();

##Lesen und Entfernen von Nachrichten in einer Warteschlange

Ihr Code kann eine Nachricht in zwei Schritten aus der Warteschlange entfernen. 1. Rufen Sie GetMessage\(\) auf, um die nächste Nachricht in einer Warteschlange abzurufen. eine von GetMessage\(\) zurückgegebene Nachricht ist für anderen Code nicht mehr sichtbar, der Nachrichten aus dieser Warteschlange liest. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. 2. Wenn Sie die Nachricht endgültig aus der Warteschlange entfernen möchten, rufen Sie „DeleteMessage1\(\)“ auf.

Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Der folgende Code ruft DeleteMessage\(\) direkt nach der Verarbeitung der Nachricht auf.

	// Get a reference to the **CloudQueue** object named 'messageQueue' as described in "Access a queue in code"

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = messageQueue.GetMessage();

	// Process the message in less than 30 seconds

  	// Then delete the message.
	await messageQueue.DeleteMessage(retrievedMessage);


## Nutzen zusätzlicher Optionen für das Entfernen von Nachrichten aus der Warteschlange

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können. Erstens können Sie einen Nachrichtenstapel abrufen \(bis zu 32\). Zweitens können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt. Im folgenden Codebeispiel wird **GetMessages** verwendet, um 20 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer **foreach**-Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt. Beachten Sie, dass die 5 Minuten für alle Nachrichten gleichzeitig beginnen, sodass 5 Minuten nach dem Aufruf von **GetMessages** alle Nachrichten, die nicht gelöscht wurden, wieder sichtbar werden.

    // Get a reference to the **CloudQueue** object named 'messageQueue' as described in "Access a queue in code"

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    foreach (CloudQueueMessage message in queue.GetMessages(20, TimeSpan.FromMinutes(5)))
    {
        // Process all messages in less than 5 minutes, deleting each message after processing.
        queue.DeleteMessage(message);
    }

## Abrufen der Warteschlangenlänge

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die Methode **FetchAttributes** fordert den Warteschlangendienst auf, die Warteschlangenattribute einschließlich der Nachrichtenanzahl abzurufen. Die Eigenschaft **ApproximateMethodCount** gibt den letzten von der Methode **FetchAttributes** abgerufenen Wert zurück, ohne den Warteschlangendienst aufzurufen.

    // Get a reference to the **CloudQueue** object named 'messageQueue' as described in "Access a queue in code"

	// Fetch the queue attributes.
	messageQueue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = messageQueue.ApproximateMessageCount;

	// Display number of messages.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## Verwenden des Async-Await-Musters mit allgemeinen Warteschlangen-APIs

In diesem Beispiel wird veranschaulicht, wie das Async-Await-Muster mit allgemeinen Warteschlangen-API verwendet wird. Im Beispiel werden jeweils die asynchronen Versionen der angegebenen Methoden aufgerufen, was am Postfix Async der einzelnen Methoden erkennbar ist. Wenn eine asynchrone Methode verwendet wird, hält das Async-Await-Muster die lokale Ausführung an, bis der Aufruf abgeschlossen ist. Durch dieses Verhalten kann der aktuelle Thread eine andere Aktion ausführen, wodurch Leistungsengpässe vermieden werden und die allgemeine Reaktionsfähigkeit der Anwendung verbessert wird. Weitere Informationen zur Verwendung des Async-Await-Musters in .NET finden Sie unter [Async und Await \(C\# und Visual Basic\)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Get a reference to the **CloudQueue** object named 'messageQueue' as described in "Access a queue in code"

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await messageQueue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await messageQueue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await messageQueue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");
## Löschen einer Warteschlange

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode **Delete** für das Warteschlangenobjekt auf.

    // Get a reference to the **CloudQueue** object named 'messageQueue' as described in "Access a queue in code"

    // Delete the queue.
    messageQueue.Delete();



##Nächste Schritte

[AZURE.INCLUDE [vs-storage-dotnet-queues-next-steps](../../includes/vs-storage-dotnet-queues-next-steps.md)]
			

<!---HONumber=July15_HO5-->