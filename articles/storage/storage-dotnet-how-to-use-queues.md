<properties
	pageTitle="Verwenden des Warteschlangenspeichers mit .NET | Microsoft Azure"
	description="Erfahren Sie, wie Sie den Warteschlangenspeicher zum Erstellen und Löschen von Warteschlangen sowie zum Einfügen, Einsehen, Abrufen und Löschen von Warteschlangennachrichten verwenden."
	services="storage"
	documentationCenter=".net"
	authors="robinsh"
	manager="carmonm"
	editor="tysonn"/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article"
	ms.date="12/04/2015"
	ms.author="gusapost"/>

# Verwenden des Warteschlangenspeichers mit .NET

[AZURE.INCLUDE [storage-selector-queue-include](../../includes/storage-selector-queue-include.md)]

## Übersicht

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Azure-Warteschlangen-Speicherdienst demonstriert. Die Beispiele sind in C# geschrieben und greifen auf den Azure-Speicherclient für .NET zurück. Zu den Szenarien gehören das **Einfügen**, **Einsehen**, **Abrufen** und **Löschen** von Warteschlangennachrichten sowie das **Erstellen und Löschen von Warteschlangen**.

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-queue-concepts-include](../../includes/storage-queue-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../../includes/storage-configure-connection-string-include.md)]

## Programmgesteuerter Zugriff auf den Warteschlangenspeicher

[AZURE.INCLUDE [storage-dotnet-obtain-assembly](../../includes/storage-dotnet-obtain-assembly.md)]

### Namespace-Deklarationen
Fügen Sie die folgenden Namespace-Deklarationen zum Anfang aller C#-Dateien hinzu, in denen Sie programmgesteuert auf den Azure-Speicher zugreifen möchten:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Queue;

Stellen Sie sicher, dass auf die `Microsoft.WindowsAzure.Storage.dll`-Assembly verwiesen wird.

[AZURE.INCLUDE [storage-dotnet-retrieve-conn-string](../../includes/storage-dotnet-retrieve-conn-string.md)]

## Erstellen einer Warteschlange

Mit einem **CloudQueueClient**-Objekt können Sie Referenzobjekte für Warteschlangen abrufen. Der folgende Code erstellt ein **CloudQueueClient**-Objekt. Der gesamte Code in diesem Leitfaden verwendet eine in der Dienstkonfiguration der Azure-Anwendung gespeicherte Speicherverbindungszeichenfolge. Es gibt auch andere Möglichkeiten zum Erstellen eines **CloudStorageAccount**-Objekts. Ausführliche Informationen dazu finden Sie in der [CloudStorageAccount][]-Dokumentation.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Mithilfe des **queueClient**-Objekts können Sie einen Verweis auf die Warteschlange abrufen, die Sie verwenden möchten. Sie können die Warteschlange erstellen, wenn sie nicht vorhanden ist.

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist
    queue.CreateIfNotExists();

## Einfügen einer Nachricht in eine Warteschlange

Um eine Nachricht in eine vorhandene Warteschlange einzufügen, erstellen Sie zuerst ein neues **CloudQueueMessage**-Objekt. Anschließend rufen Sie die **AddMessage**-Methode auf. Die **CloudQueueMessage** kann entweder aus einer Zeichenfolge (im Format UTF-8) oder aus einem **Byte**-Array erstellt werden. Dieser Code erstellte eine Warteschlange (wenn sie noch nicht vorhanden ist) und fügt die Nachricht "Hello, World" ein:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Create the queue if it doesn't already exist.
    queue.CreateIfNotExists();

    // Create a message and add it to the queue.
    CloudQueueMessage message = new CloudQueueMessage("Hello, World");
    queue.AddMessage(message);

## Einsehen der nächsten Nachricht

Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die Methode **PeekMessage** aufrufen.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Peek at the next message
    CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display message.
	Console.WriteLine(peekedMessage.AsString);

## Ändern des Inhalts von Nachrichten in der Warteschlange

Sie können den Inhalt einer Nachricht vor Ort in der Warteschlange ändern. Wenn die Nachricht eine Arbeitsaufgabe darstellt, können Sie diese Funktion verwenden, um den Status der Aufgabe zu aktualisieren. Mit dem folgenden Code wird die Warteschlangennachricht mit neuem Inhalt aktualisiert und das Sichtbarkeits-Zeitlimit um weitere 60 Sekunden verlängert. Dadurch wird der mit der Nachricht verknüpfte Arbeitsstatus gespeichert, und der Client erhält eine weitere Minute zur Bearbeitung der Nachricht. Sie können diese Technik verwenden, um Workflows mit mehreren Schritten in Warteschlangennachrichten zu verfolgen, ohne von vorn beginnen zu müssen, wenn ein Verarbeitungsschritt aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. In der Regel behalten Sie auch die Anzahl der Wiederholungen bei, und wenn die Nachricht mehr als *n* Mal wiederholt wurde, wird sie gelöscht. Dies verhindert, dass eine Nachricht bei jeder Verarbeitung einen Anwendungsfehler auslöst.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Get the message from the queue and update the message contents.
    CloudQueueMessage message = queue.GetMessage();
    message.SetMessageContent("Updated contents.");
    queue.UpdateMessage(message,
        TimeSpan.FromSeconds(60.0),  // Make it visible for another 60 seconds.
        MessageUpdateFields.Content | MessageUpdateFields.Visibility);

## Entfernen der nächsten Nachricht

Dieser Code entfernt eine Nachricht in zwei Schritten aus der Warteschlange. Wenn Sie **GetMessage** aufrufen, wird die nächste Nachricht aus der Warteschlange abgerufen. Die für **GetMessage** zurückgegebene Nachricht ist für andere Codes nicht mehr sichtbar, die Nachrichten aus dieser Warteschlange lesen. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. Um die Nachricht endgültig aus der Warteschlange zu entfernen, müssen Sie außerdem **DeleteMessage** aufrufen. Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers fehlschlägt. Der Code ruft **DeleteMessage** direkt nach der Verarbeitung der Nachricht auf.

    // Retrieve storage account from connection string
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Get the next message
    CloudQueueMessage retrievedMessage = queue.GetMessage();

    //Process the message in less than 30 seconds, and then delete the message
    queue.DeleteMessage(retrievedMessage);

## Verwenden des Async-Await-Musters mit allgemeinen Warteschlangenspeicher-APIs

In diesem Beispiel wird veranschaulicht, wie das Async-Await-Muster mit allgemeinen Warteschlangenspeicher-APIs verwendet wird. Im Beispiel werden jeweils die asynchronen Versionen der angegebenen Methoden aufgerufen, wie am Suffix *Async* der einzelnen Methoden erkennbar. Wenn eine asynchrone Methode verwendet wird, hält das Async-Await-Muster die lokale Ausführung an, bis der Aufruf abgeschlossen ist. Durch dieses Verhalten kann der aktuelle Thread eine andere Aktion ausführen, wodurch Leistungsengpässe vermieden werden und die allgemeine Reaktionsfähigkeit der Anwendung verbessert wird. Weitere Informationen zur Verwendung des Async-Await-Musters in .NET finden Sie unter [Async und Await (C# und Visual Basic)](https://msdn.microsoft.com/library/hh191443.aspx)

    // Create the queue if it doesn't already exist
    if(await queue.CreateIfNotExistsAsync())
    {
        Console.WriteLine("Queue '{0}' Created", queue.Name);
    }
    else
    {
        Console.WriteLine("Queue '{0}' Exists", queue.Name);
    }

    // Create a message to put in the queue
    CloudQueueMessage cloudQueueMessage = new CloudQueueMessage("My message");

    // Async enqueue the message
    await queue.AddMessageAsync(cloudQueueMessage);
    Console.WriteLine("Message added");

    // Async dequeue the message
    CloudQueueMessage retrievedMessage = await queue.GetMessageAsync();
    Console.WriteLine("Retrieved message with content '{0}'", retrievedMessage.AsString);

    // Async delete the message
    await queue.DeleteMessageAsync(retrievedMessage);
    Console.WriteLine("Deleted message");

## Nutzen zusätzlicher Optionen für das Entfernen von Nachrichten aus der Warteschlange

Es gibt zwei Möglichkeiten, wie Sie das Abrufen von Nachrichten aus der Warteschlange anpassen können. Erstens können Sie einen Nachrichtenstapel abrufen (bis zu 32). Zweitens können Sie das Unsichtbarkeits-Zeitlimit verkürzen oder verlängern, sodass der Code mehr oder weniger Zeit zur vollständigen Verarbeitung jeder Nachricht benötigt. Im folgenden Codebeispiel wird **GetMessages** verwendet, um 20 Nachrichten mit einem Aufruf abzurufen. Anschließend wird jede Nachricht mithilfe einer **foreach**-Schleife verarbeitet. Außerdem wird das Unsichtbarkeits-Zeitlimit auf fünf Minuten pro Nachricht festgelegt. Beachten Sie, dass die 5 Minuten für alle Nachrichten gleichzeitig beginnen, sodass 5 Minuten nach dem Aufruf von **GetMessages** alle Nachrichten, die nicht gelöscht wurden, wieder sichtbar werden.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

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

Sie können die Anzahl der Nachrichten in einer Warteschlange schätzen lassen. Die Methode **FetchAttributes** fordert den Warteschlangendienst auf, die Warteschlangenattribute einschließlich der Nachrichtenanzahl abzurufen. Die Eigenschaft **ApproximateMessageCount** gibt den letzten von der Methode **FetchAttributes** abgerufenen Wert zurück, ohne den Warteschlangendienst aufzurufen.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// Fetch the queue attributes.
	queue.FetchAttributes();

    // Retrieve the cached approximate message count.
    int? cachedMessageCount = queue.ApproximateMessageCount;

	// Display number of messages.
	Console.WriteLine("Number of messages in queue: " + cachedMessageCount);

## Löschen einer Warteschlange

Zum Löschen einer Warteschlange und aller darin enthaltenen Nachrichten rufen Sie die Methode **Delete** für das Warteschlangenobjekt auf.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the queue client.
    CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

    // Retrieve a reference to a queue.
    CloudQueue queue = queueClient.GetQueueReference("myqueue");

    // Delete the queue.
    queue.Delete();

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des Warteschlangenspeichers vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.

- Vollständige Informationen zu verfügbaren APIs finden Sie in der Warteschlangendienst-Referenzdokumentation:
    - [Referenz zur Storage-Clientbibliothek für .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
    - [REST-API-Referenz](http://msdn.microsoft.com/library/azure/dd179355)
- Erfahren Sie, wie Sie mithilfe des [Azure WebJobs SDK](../websites-dotnet-webjobs-sdk/) den geschriebenen Code so vereinfachen, dass er mit Azure Storage funktioniert.
- Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.
    - Verwenden Sie [Tabellenspeicher](storage-dotnet-how-to-use-tables.md) zum Speichern strukturierter Daten.
    - Verwenden Sie [Blobspeicher](storage-dotnet-how-to-use-blobs.md) zum Speichern unstrukturierter Daten.
    - Verwenden Sie eine [SQL-Datenbank](sql-database-dotnet-how-to-use.md) zum Speichern relationaler Daten.

  [Download and install the Azure SDK for .NET]: /develop/net/
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [Creating a Azure Project in Visual Studio]: http://msdn.microsoft.com/library/azure/ee405487.aspx
  [CloudStorageAccount]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx
  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

<!---HONumber=AcomDC_0114_2016-->