#####Erstellen einer Warteschlange
Mit einem **CloudQueueClient**-Objekt können Sie Referenzobjekte für Warteschlangen abrufen. Der folgende Code erstellt ein **CloudQueueClient**-Objekt. Der gesamte Code in diesem Thema verwendet eine in der Dienstkonfiguration der Azure-Anwendung gespeicherte Verbindungszeichenfolge für den Speicher. Es gibt auch andere Möglichkeiten zum Erstellen eines **CloudStorageAccount**-Objekts. In der [CloudStorageAccount]-Dokumentation(http://msdn.microsoft.com/library/azure/microsoft.windowsazure.cloudstorageaccount_methods.aspx "CloudStorageAccount") finden Sie Details zu diesem Thema.

	// Create the queue client.
	CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();

Verwenden Sie das **queueClient**-Objekt, um einen Verweis auf die gewünschte Warteschlange abzurufen. Der Code versucht, auf eine Warteschlange namens "myqueue" zu verweisen. Wird keine Warteschlange mit diesem Namen gefunden, wird die Warteschlange erstellt.

	// Get a reference to a queue named "myqueue".
	CloudQueue queue = queueClient.GetQueueReference("myqueue");

	// If the queue isn't already there, then create it.
	queue.CreateIfNotExists();

**HINWEIS:** Verwenden Sie diesen Codeblock vor dem Code in den folgenden Abschnitten.

#####Einfügen einer Nachricht in eine Warteschlange
Um eine Nachricht in eine vorhandene Warteschlange einzufügen, erstellen Sie zuerst ein neues **CloudQueueMessage**-Objekt. Anschließend rufen Sie die AddMessage()-Methode auf. Ein **CloudQueueMessage**-Objekt kann aus einer Zeichenfolge (im UTF-8-Format) oder aus einem Bytearray erstellt werden. Dieser Code erstellt eine Warteschlange (falls noch nicht vorhanden) und fügt die Nachricht 'Hello, World' ein.

	// Create a message and add it to the queue.
	CloudQueueMessage message = new CloudQueueMessage("Hello, World");
	queue.AddMessage(message);

#####Einsehen der nächsten Nachricht
Sie können einen Blick auf die Nachricht am Anfang einer Warteschlange werfen, ohne sie aus der Warteschlange zu entfernen, indem Sie die Methode PeekMessage() aufrufen.

	// Peek at the next message in the queue.
	CloudQueueMessage peekedMessage = queue.PeekMessage();

	// Display the message.
	Console.WriteLine(peekedMessage.AsString);

#####Entfernen der nächsten Nachricht aus der Warteschlange
Ihr Code kann eine Nachricht in zwei Schritten aus der Warteschlange entfernen. 


1. Rufen Sie GetMessage() auf, um die nächste Nachricht in einer Warteschlange abzurufen. Die von GetMessage zurückgegebenen Nachrichten werden für anderen Code, der aus dieser Warteschlange liest, unsichtbar. Standardmäßig bleibt die Nachricht 30 Sekunden lang unsichtbar. 
2.	Wenn Sie die Nachricht endgültig aus der Warteschlange entfernen möchten, rufen Sie DeleteMessage() auf. 

Dieser zweistufige Prozess zum Entfernen von Nachrichten stellt sicher, dass eine andere Codeinstanz dieselbe Nachricht erneut abrufen kann, falls die Verarbeitung aufgrund eines Hardware- oder Softwarefehlers nicht durchgeführt werden kann. Der folgende Code ruft DeleteMessage() direkt nach der Verarbeitung der Nachricht auf.

	// Get the next message in the queue.
	CloudQueueMessage retrievedMessage = queue.GetMessage();

	// Process the message in less than 30 seconds, and then delete the message.
	queue.DeleteMessage(retrievedMessage);

[Weitere Informationen zu Azure Storage](http://azure.microsoft.com/documentation/services/storage/)
Siehe auch [Durchsuchen von Speicherressourcen im Server-Explorer](http://msdn.microsoft.com/library/azure/ff683677.aspx).
<!--HONumber=47-->
