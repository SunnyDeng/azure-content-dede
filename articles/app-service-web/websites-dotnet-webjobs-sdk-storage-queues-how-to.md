<properties 
	pageTitle="Verwenden von Azure-Warteschlangenspeicher mit dem Webaufträge-SDK" 
	description="Erfahren Sie, wie Sie Azure-Warteschlangenspeicher mit dem Webaufträge-SDK nutzen. Sie können Warteschlangen erstellen und löschen, Warteschlangennachrichten einfügen, einsehen, abrufen und löschen und vieles mehr." 
	services="app-service\web, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-web" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="04/03/2015" 
	ms.author="tdykstra"/>

# Verwenden von Azure-Warteschlangenspeicher mit dem Webaufträge-SDK

## Übersicht

Diese Anleitungen enthält C#-Codebeispiele, die zeigen, wie das Azure-Webaufträge-SDK, Version 1.x, mit dem Azure-Warteschlangenspeicherdienst verwendet wird.

Das Handbuch wird davon ausgegangen, Sie wissen [wie ein WebJob-Projekt in Visual Studio mit Verbindung erstellt Zeichenfolgen, die auf Ihr Speicherkonto](websites-dotnet-webjobs-sdk-get-started.md).

Die meisten Codeausschnitte zeigen nur Funktionen, die nicht auf den Code, erstellt das `JobHost` Objekt wie im folgenden Beispiel:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
Die Anleitung umfasst die folgenden Themen:

-   [Wie Sie eine Funktion ausgelöst wird, wenn eine warteschlangennachricht empfangen wird](#trigger)
	- Zeichenfolgen-Warteschlangennachrichten
	- POCO-Warteschlangennachrichten
	- Asynchrone Funktionen
	- Typen, mit denen das "QueueTrigger"-Attribut arbeitet
	- Abrufalgorithmus
	- Mehrere Instanzen
	- Parallele Ausführung
	- Abrufen der Warteschlangen-der Warteschlangennchrichtenmetadaten
	- Ordnungsgemäßes Herunterfahren
-   [Gewusst wie: Erstellen Sie eine warteschlangennachricht beim Verarbeiten einer warteschlangennachricht](#createqueue)
	- Zeichenfolgen-Warteschlangennachrichten
	- POCO-Warteschlangennachrichten
	- Erstellen mehrerer Nachrichten oder in asynchronen Funktionen
	- Typen, mit denen das "Queue"-Attribut arbeitet
	- Verwenden von Webaufträge-SDK-Attributen im Hauptteil einer Funktion
-   [Das Lesen und Schreiben von Blobs beim Verarbeiten einer warteschlangennachricht](#blobs)
	- Zeichenfolgen-Warteschlangennachrichten
	- POCO-Warteschlangennachrichten
	- Typen, mit denen das "Blob"-Attribut arbeitet
-   [Nicht verarbeitbare Nachrichten behandelt](#poison)
	- Automatische Behandlung von nicht verarbeitbaren Nachrichten
	- Manuelle Behandlung von nicht verarbeitbaren Nachrichten
-   [Gewusst wie: Festlegen von Konfigurationsoptionen](#config)
	- Festlegen von SDK-Verbindungszeichenfolgen im Code
	- Konfigurieren von "QueueTrigger"-Einstellungen
	- Festlegen von Werten für WebJobs SDK-Konstruktorparametern im Code
-   [Eine Funktion manuell auslösen](#manual)
-   [Gewusst wie: Schreiben von Protokollen](#logs)
-   [Nächste Schritte](#nextsteps)

## <a id="trigger"></a> Wie Sie eine Funktion ausgelöst wird, wenn eine warteschlangennachricht empfangen wird

Um eine Funktion zu schreiben, die das WebJobs SDK aufruft, wenn eine warteschlangennachricht empfangen wird, verwenden Sie die `QueueTrigger` Attribut. Der Attributkonstruktor verwendet einen Zeichenfolgenparameter, der den Namen der Warteschlange angibt. Sie können auch [Legen Sie den Namen der Warteschlange dynamisch](#config).

### Zeichenfolgen-Warteschlangennachrichten

Im folgenden Beispiel enthält die Warteschlange eine Zeichenfolgenmeldung, also `QueueTrigger` gilt für einen Parameter mit dem Namen `logMessage` enthält den Inhalt der warteschlangennachricht. Die Funktion [schreibt eine Protokollmeldung zum Dashboard](#logs).
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

Darüber hinaus `string`, der Parameter ist möglicherweise ein Byte-Array, ein `CloudQueueMessage` -Objekt oder ein POCO, die Sie definieren.

### POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) in die Warteschlange Nachrichten

Im folgenden Beispiel enthält der Warteschlangenmeldung JSON für ein `BlobInformation` Objekt enthält einen `BlobName` Eigenschaft. Das SDK deserialisiert das Objekt automatisch.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Das SDK verwendet die [Newtonsoft.Json NuGet-Paket](http://www.nuget.org/packages/Newtonsoft.Json) zum Serialisieren und Deserialisieren von Meldungen. Wenn Sie Warteschlangennachrichten in einem Programm erstellen, das nicht das Webaufträge-SDK verwendet, können Sie Code wie im folgenden Beispiel zum Erstellen einer POCO-Warteschlangennachricht schreiben, die das SDK analysieren kann.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Asynchrone Funktionen

Die folgenden Async-Funktion [schreibt ein Protokoll zum Dashboard](#logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Async-Funktionen nutzen können ein [Abbruchtoken](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken), wie im folgenden Beispiel dargestellt, die einen Blob kopiert. (Eine Erläuterung der `queueTrigger` Platzhalter finden Sie unter der [Blobs](#blobs) Abschnitt.)

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### <a id="qtattributetypes"></a> Das Attribut QueueTrigger Seriennummern Typen

Sie können `QueueTrigger` mit den folgenden Typen:

* `string`
* Einem als JSON serialisierten POCO-Typ
* `byte[]`
* `CloudQueueMessage`

### <a id="polling"></a> Abruf-Algorithmus

Das SDK implementiert einen zufälligen exponentiellen Backoff-Algorithmus, um die Auswirkungen des Abfragens von Warteschlangen im Leerlauf auf Speichertransaktionskosten zu reduzieren. Wenn eine Nachricht gefunden wird, wartet das SDK zwei Sekunden und prüft dann, ob eine weitere Meldung vorhanden ist. Wenn keine Nachricht gefunden wird, wartet es ca. vier Sekunden vor dem erneuten Versuch. Nach weiteren fehlerhaften Versuchen, eine Warteschlangennachricht abzurufen, erhöht sich die Wartezeit weiterhin, bis die maximale Wartezeit von standardmäßig 1 Minute erreicht wird. [Lässt sich die maximale Wartezeit](#config).

### <a id="instances"></a> Mehrere Instanzen

Wenn Ihre Web-App auf mehreren virtuellen Instanzen läuft, wird ein kontinuierlicher Webauftrag auf allen Computern ausgeführt, und jeder Computer wartet auf Auslöser und versucht, Funktionen auszuführen. In manchen Szenarien kann dies dazu führen, dass manche Funktionen dieselben Daten doppelt verarbeiten. Die Funktionen sollten daher "idempotent" geschrieben sein, d. h. beim wiederholten Aufrufen mit denselben Eingangsdaten dürfen keine duplizierten Resultate entstehen.

### <a id="parallel"></a> Parallele Ausführung

Wenn mehrere Funktionen unterschiedliche Warteschlangen überwachen, ruft das SDK diese parallel auf, wenn Nachrichten gleichzeitig empfangen werden.

Das Gleiche gilt auch, wenn mehrere Nachrichten für eine einzige Warteschlange empfangen werden. Das SDK ruft standardmäßig jeweils einen Batch von 16 Warteschlangennachrichten auf und führt die Funktion, die diese verarbeitet, parallel aus. [Die Batchgröße ist konfigurierbar](#config). Wenn die zu verarbeitende Anzahl die Hälfte der Batchgröße erreicht, ruft das SDK einen weiteren Batch ab und beginnt mit der Verarbeitung dieser Nachrichten. Aus diesem Grund beträgt die maximale Anzahl der pro Funktion verarbeiteten Nachrichten die 1,5-fache Batchgröße. Dieser Grenzwert gilt separat für jede Funktion mit einem `QueueTrigger` Attribut. Wenn Sie keine parallele Ausführung für in einer Warteschlange empfangene Nachrichten wünschen, legen Sie die Batchgröße auf 1 fest.

### <a id="queuemetadata"></a>Abrufen der Warteschlange oder Warteschlange Nachrichtenmetadaten

Sie können die folgenden Nachrichteneigenschaften abrufen, indem Sie Parameter zur Methodensignatur hinzufügen:

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string` QueueTrigger (enthält Meldungstext)
* `string` ID
* `string` popReceipt
* `int` dequeueCount

Wenn Sie direkt mit den Azure-Speicher-API arbeiten möchten, können Sie auch Hinzufügen einer `CloudStorageAccount` Parameter.

Im folgenden Beispiel werden all diese Metadaten in ein INFO-Anwendungsprotokoll geschrieben. In dem Beispiel enthalten sowohl logMessage als auch queueTrigger den Inhalt der Warteschlangennachricht.

		public static void WriteLog([QueueTrigger("logqueue")] string logMessage,
		    DateTimeOffset expirationTime,
		    DateTimeOffset insertionTime,
		    DateTimeOffset nextVisibleTime,
		    string id,
		    string popReceipt,
		    int dequeueCount,
		    string queueTrigger,
		    CloudStorageAccount cloudStorageAccount,
		    TextWriter logger)
		{
		    logger.WriteLine(
		        "logMessage={0}\n" +
			"expirationTime={1}\ninsertionTime={2}\n" +
		        "nextVisibleTime={3}\n" +
		        "id={4}\npopReceipt={5}\ndequeueCount={6}\n" +
		        "queue endpoint={7} queueTrigger={8}",
		        logMessage, expirationTime,
		        insertionTime,
		        nextVisibleTime, id,
		        popReceipt, dequeueCount,
		        cloudStorageAccount.QueueEndpoint,
		        queueTrigger);
		}

Nachfolgend finden Sie ein Beispielprotokoll, das vom Beispielcode geschrieben wurde:

		logMessage=Hello world!
		expirationTime=10/14/2014 10:31:04 PM +00:00
		insertionTime=10/7/2014 10:31:04 PM +00:00
		nextVisibleTime=10/7/2014 10:41:23 PM +00:00
		id=262e49cd-26d3-4303-ae88-33baf8796d91
		popReceipt=AgAAAAMAAAAAAAAAfc9H0n/izwE=
		dequeueCount=1
		queue endpoint=https://contosoads.queue.core.windows.net/
		queueTrigger=Hello world!

### <a id="graceful"></a>Ordnungsgemäßes Herunterfahren

Akzeptiert eine Funktion, die in einem kontinuierlichen WebJob führt eine `CancellationToken` -Parameter der kann das Betriebssystem die Funktion benachrichtigt wird, kurz bevor der WebJob beendet werden. Sie können diese Benachrichtigung verwenden, um sicherzustellen, dass die Funktion nicht auf eine Weise unerwartet beendet wird, die die Daten in einem inkonsistenten Zustand hinterlässt.

Das folgende Beispiel zeigt, wie Sie in einer Funktion nach einer bevorstehenden WebJob-Beendigung suchen.

	public static void GracefulShutdownDemo(
	            [QueueTrigger("inputqueue")] string inputText,
	            TextWriter logger,
	            CancellationToken token)
	{
	    for (int i = 0; i < 100; i++)
	    {
	        if (token.IsCancellationRequested)
	        {
	            logger.WriteLine("Function was cancelled at iteration {0}", i);
	            break;
	        }
	        Thread.Sleep(1000);
	        logger.WriteLine("Normal processing for queue message={0}", inputText);
	    }
	}

**Hinweis:** das Dashboard möglicherweise nicht richtig angezeigt, den Status und die Ausgabe der Funktionen, die beendet wurden.
 
Weitere Informationen finden Sie unter [WebJobs ordnungsgemäßes Herunterfahren](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).

## <a id="createqueue"></a> Gewusst wie: Erstellen Sie eine warteschlangennachricht beim Verarbeiten einer warteschlangennachricht

Um eine Funktion schreiben, die eine neue warteschlangennachricht erstellt, verwenden Sie die `Queue` Attribut. Wie `QueueTrigger`, in den Warteschlangennamen als Zeichenfolge übergeben, oder Sie können [Legen Sie den Namen der Warteschlange dynamisch](#config).

### Zeichenfolgen-Warteschlangennachrichten

Im folgenden nicht asynchronen Beispiel wird eine neue Warteschlangennachricht in der Warteschlange mit dem Namen "outputqueue" mit dem gleichen Inhalt wie die Warteschlangennachricht erstellt, die in der Warteschlange mit dem Namen "inputqueue" empfangen wird. (Für asynchrone Funktionen verwenden `IAsyncCollector<T>` wie weiter unten in diesem Abschnitt gezeigt.)


		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}
  
### POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) in die Warteschlange Nachrichten

Zum Erstellen einer warteschlangennachricht, die eine POCO anstelle einer Zeichenfolge enthält, den POCO-Typ als Output-Parameter zum Übergeben der `Queue` Attributkonstruktor.
 
		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

Das SDK serialisiert das Objekt automatisch an JSON. Es wird immer eine Warteschlangennachricht erstellt, selbst wenn das Objekt Null ist.

### Erstellen mehrerer Nachrichten oder in asynchronen Funktionen

Um mehrere Nachrichten zu erstellen, setzen Sie den Parametertyp für die Ausgabewarteschlange `ICollector<T>` oder `IAsyncCollector<T>`, wie im folgenden Beispiel gezeigt.

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Jede warteschlangennachricht sofort erstellt bei der `Add` wird aufgerufen.

### Typen, mit denen das "Queue"-Attribut arbeitet

Sie können die `Queue` Attribut für die folgenden Parametertypen:

* `out string` (Nachricht in der Warteschlange erstellt, wenn der Parameterwert nicht Null ist, wenn die Funktion beendet wird.)
* `out byte[]` (funktioniert wie `string`) 
* `out CloudQueueMessage` (funktioniert wie `string`) 
* `out POCO` (ein serialisierbarer Typ, erstellt eine Nachricht mit einem null-Objekt, wenn der Parameter null ist, wenn die Funktion beendet)
* `ICollector`
* `IAsyncCollector`
* `CloudQueue` (zum manuellen Erstellen von Nachrichten mithilfe der Azure-Speicher-API direkt)

### <a id="ibinder"></a>Verwenden der WebJobs SDK-Attribute im Hauptteil einer Funktion

Wenn Sie einige Aufgaben in der Funktion vor der Verwendung eines Attributs WebJobs SDK wie z. B. müssen `Queue`, `Blob`, oder `Table`, können Sie die `IBinder` Schnittstelle.

Im folgenden Beispiel wird aus einer Nachricht der Eingabewarteschlange eine neue Nachricht mit dem gleichen Inhalt in einer Ausgabewarteschlange erstellt. Der Name der Ausgabewarteschlangenname wird durch Code im Text der Funktion festgelegt.

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    IBinder binder)
		{
		    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
		    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
		    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
		    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
		}

Die `IBinder` Schnittstelle kann auch verwendet werden, mit der `Table` und `Blob` Attribute.

## <a id="blobs"></a> Das Lesen und Schreiben von Blobs und Tabellen beim Verarbeiten einer warteschlangennachricht

Die `Blob` und `Table` Attribute ermöglichen es Ihnen, lesen und Schreiben von Blobs und Tabellen. Die Beispiele in diesem Abschnitt gelten für BLOBs. Codebeispiele, die zeigen, wie Sie Prozesse auslösen, wenn Blobs erstellt oder aktualisiert werden, finden Sie unter [Verwendung von Azure Blob-Speicher mit dem WebJobs SDK](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md), Codebeispiele, das Lesen und Schreiben von Tabellen finden Sie unter [Verwendung von Azure-Tabellenspeicher mit dem WebJobs SDK](websites-dotnet-webjobs-sdk-storage-tables-how-to.md).

### Zeichenfolge-Warteschlangennachrichten, die BLOB-Vorgänge auslösen

Für eine warteschlangennachricht, die eine Zeichenfolge enthält `queueTrigger` ist ein Platzhalter, Sie in können, der `Blob` des Attributs `blobPath` Parameter, der den Inhalt der Nachricht enthält.

Im folgenden Beispiel wird `Stream` Objekte lesen und Schreiben von Blobs. Die Warteschlangennachricht enthält den Namen eines BLOBs, das sich im "textblobs"-Container befindet. Eine Kopie des Blobs, an deren Namen "-new" angefügt wird, wird im gleichen Container erstellt.

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

Das `Blob` -Attribut Konstruktor nimmt eine `blobPath` -Parameter, der den Container und Blob-Namen angibt. Weitere Informationen zu diesen Platzhalter, finden Sie unter [Verwendung von Azure Blob-Speicher mit dem WebJobs SDK](websites-dotnet-webjobs-sdk-storage-blobs-how-to.md),

Wenn das Attribut wird ein `Stream` Objekt, einen anderen Konstruktorparameter gibt die `FileAccess` Modus als lesen, schreiben oder lesen/schreiben.

Im folgenden Beispiel wird ein `CloudBlockBlob` -Objekt, das ein Blob zu löschen. Die Warteschlangennachricht ist der Name des BLOBs.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### <a id="pocoblobs"></a> POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) in die Warteschlange Nachrichten

Für eine POCO als JSON in der Warteschlangenmeldung gespeichert wird, können Sie verwenden, werden die Namen der Eigenschaften des Objekts in der `Queue` des Attributs `blobPath` Parameter. Sie können auch [Metadaten-Eigenschaftennamen in die Warteschlange](#queuemetadata) als Platzhalter.

Das folgende Beispiel kopiert ein BLOB in ein neues BLOB mit einer anderen Erweiterung. Die warteschlangennachricht ist ein `BlobInformation` -Objekt, das enthält `BlobName` und `BlobNameWithoutExtension` Eigenschaften. Die Eigenschaftsnamen dienen als Platzhalter in den blobpfad für die `Blob` Attribute.
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

Das SDK verwendet die [Newtonsoft.Json NuGet-Paket](http://www.nuget.org/packages/Newtonsoft.Json) zum Serialisieren und Deserialisieren von Meldungen. Wenn Sie Warteschlangennachrichten in einem Programm erstellen, das nicht das Webaufträge-SDK verwendet, können Sie Code wie im folgenden Beispiel zum Erstellen einer POCO-Warteschlangennachricht schreiben, die das SDK analysieren kann.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

Wenn Sie einige Aufgaben in Ihrer Funktion vor dem Binden eines BLOBs auf ein Objekt müssen, können das Attribut im Hauptteil der Funktion [wie oben beschrieben für das Attribut für die Warteschlange](#ibinder).

### <a id="blobattributetypes"></a> Typen, mit denen Sie das Blob-Attribut mit
 
Das `Blob` -Attribut kann mit den folgenden Typen verwendet:

* `Stream` (Lesen Sie oder Schreiben Sie, mit dem FileAccess Konstruktorparameter angegeben)
* `TextReader`
* `TextWriter`
* `string` (Lesen)
* `out string` (schreiben, erstellt ein Blob nur dann, wenn der Parameter ungleich Null ist, gibt die Funktion)
* POCO (Lesen)
* POCO (Schreiben; erstellt immer ein BLOB, erstellt ein NULL-Objekt , wenn der POCO-Parameter bei Rückgabe der Funktion NULL ist)
* `CloudBlobStream` (Schreiben)
* `ICloudBlob` (Lesen oder Schreiben)
* `CloudBlockBlob` (Lesen oder Schreiben) 
* `CloudPageBlob` (Lesen oder Schreiben) 

## <a id="poison"></a> Nicht verarbeitbare Nachrichten behandelt

Nachrichten, deren Inhalt bewirkt, eine Funktion dass nicht, heißen *nicht verarbeitbare Nachrichten*. Wenn bei der Funktion ein Fehler auftritt, wird die Warteschlangennachricht nicht gelöscht, sondern schließlich erneut aufgegriffen, wodurch verursacht wird, dass der Zyklus wiederholt wird. Das SDK kann den Zyklus nach einer begrenzten Anzahl von Iterationen automatisch unterbrechen, oder Sie können dies manuell vornehmen.

### Automatische Behandlung von nicht verarbeitbaren Nachrichten

Das SDK ruft eine Funktion bis zu fünf Mal auf, um eine Warteschlangenachricht zu verarbeiten. Wenn der fünfte Versuch fehlschlägt, wird die Nachricht in eine Warteschlange für nicht verarbeitete Nachrichten verschoben. [Lässt sich die maximale Anzahl von Wiederholungen](#config).

Den Namen der Warteschlange nicht verarbeitbaren *{Originalqueuename}*-beschädigte. Sie können eine Funktion schreiben, um Nachrichten aus der Warteschlange für nicht verarbeitete Nachrichten zu verarbeiten, indem Sie diese protokollieren oder eine Benachrichtigung senden, dass ein manueller Eingriff erforderlich ist.

Im folgenden Beispiel wird die `CopyBlob` Funktion schlägt fehl, wenn eine Warteschlangen-Nachricht den Namen eines BLOB enthält, die nicht vorhanden ist. In diesem Fall wird die Nachricht aus der Warteschlange "copyblobqueue" in die Warteschlange "copyblobqueue-poison" verschoben. Der `ProcessPoisonMessage` meldet dann die nicht verarbeitbare Nachricht.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}
		
		public static void ProcessPoisonMessage(
		    [QueueTrigger("copyblobqueue-poison")] string blobName, TextWriter logger)
		{
		    logger.WriteLine("Failed to copy blob, name=" + blobName);
		}

Die folgende Abbildung zeigt die Konsolenausgabe dieser Funktionen, wenn eine nicht verarbeitbare Nachricht verarbeitet wird.

![Konsolenausgabe für nicht verarbeitbare Nachrichten](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### Manuelle Behandlung von nicht verarbeitbaren Nachrichten

Erhalten Sie die Anzahl der Male eine Nachricht nahm für die Verarbeitung durch Hinzufügen einer `int` Parameter mit dem Namen `dequeueCount` zu Ihrer Funktion. Anschließend können Sie die Häufigkeit des Entfernens aus der Warteschlange im Funktionscode überprüfen und Ihre eigene Behandlung von nicht verarbeitbaren Nachrichten ausführen, wenn die Anzahl einen Schwellenwert überschreitet, wie in dem folgendem Beispiel dargestellt.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
		    TextWriter logger)
		{
		    if (dequeueCount > 3)
		    {
		        logger.WriteLine("Failed to copy blob, name=" + blobName);
		    }
		    else
		    {
		    blobInput.CopyTo(blobOutput, 4096);
		    }
		}

## <a id="config"></a> Gewusst wie: Festlegen von Konfigurationsoptionen

Sie können die `JobHostConfiguration` Geben die folgenden Konfigurationsoptionen festlegen:

* Festlegen der SDK-Verbindungszeichenfolgen im Code
* Konfigurieren Sie `QueueTrigger` Einstellungen wie z. B. die maximale Anzahl aus Warteschlange entfernt.
* Rufen Sie Warteschlangennamen aus der Konfiguration ab.

### <a id="setconnstr"></a>Festlegen Sie SDK-Verbindungszeichenfolgen im Code.

Durch Festlegen der SDK-Verbindungszeichenfolgen im Code können Sie Ihre eigenen Verbindungszeichenfolgennamen in Konfigurationsdateien oder Umgebungsvariablen verwenden, wie im folgenden Beispiel gezeigt.

		static void Main(string[] args)
		{
		    var _storageConn = ConfigurationManager
		        .ConnectionStrings["MyStorageConnection"].ConnectionString;
		
		    var _dashboardConn = ConfigurationManager
		        .ConnectionStrings["MyDashboardConnection"].ConnectionString;
		
		    var _serviceBusConn = ConfigurationManager
		        .ConnectionStrings["MyServiceBusConnection"].ConnectionString;
		
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.StorageConnectionString = _storageConn;
		    config.DashboardConnectionString = _dashboardConn;
		    config.ServiceBusConnectionString = _serviceBusConn;
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueue"></a>QueueTrigger-Einstellungen konfigurieren

Sie können die folgenden Einstellungen konfigurieren, die für die Verarbeitung von Warteschlangennachrichten gelten:

- Die maximale Anzahl von Warteschlangennachrichten, die gleichzeitig abgerufen werden, um parallel ausgeführt zu werden (der Standardwert ist 16).
- Die maximale Anzahl von Wiederholungen, bevor eine Warteschlangennachricht an eine Warteschlange mit beschädigten Nachrichten gesendet wird (der Standardwert ist 5).
- Die maximale Wartezeit bis zum erneuten Abrufen, wenn eine Warteschlange leer ist (der Standardwert ist 1 Minute).

Das folgende Beispiel zeigt, wie diese Einstellungen konfiguriert werden können:

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.Queues.BatchSize = 8;
		    config.Queues.MaxDequeueCount = 4;
		    config.Queues.MaxPollingInterval = TimeSpan.FromSeconds(15);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="setnamesincode"></a>Legen Sie Werte für WebJobs SDK Konstruktorparameter in code

Mitunter möchten Sie einen Warteschlangennamen, einen BLOB-Namen oder Container oder einen Tabellennamen im Code angeben, anstatt ihn hart zu codieren. Sie möchten z. B. Geben Sie den Warteschlangennamen für `QueueTrigger` in einer Datei oder Umgebung Konfigurationsvariable.

Möglich, die durch die Übergabe einer `NameResolver` -Objekts auf den `JobHostConfiguration` Typ. Sie enthalten spezielle Platzhalter, die in Prozentzeichen (%) im Konstruktorparameter WebJobs SDK-Attribut, und Ihre `NameResolver` Code gibt die tatsächlichen Werte anstelle diese Platzhalter verwendet werden.

Angenommen, Sie möchten eine Warteschlange mit dem Namen "logqueuetest" in der Testumgebung und eine Warteschlange mit dem Namen "logqueueprod" in einer Produktionsumgebung verwenden. Anstatt eine hartcodierte Warteschlangennamen, möchten Sie geben Sie den Namen eines Eintrags in der `appSettings` -Auflistung, die den tatsächlichen Warteschlangennamen müssten. Wenn die `appSettings` Logqueue ist, könnte die Funktion wie im folgenden Beispiel aussehen.

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

Ihre `NameResolver` Klasse konnte dann der Name aus der Warteschlange abgerufen `appSettings` wie im folgenden Beispiel gezeigt:

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

Übergeben Sie die `NameResolver` Klasse der `JobHost` -Objekts wie im folgenden Beispiel gezeigt.

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 
**Hinweis:** Warteschlange, Tabellen- und Blob-Namen aufgelöst werden jedes Mal eine Funktion aufgerufen wird, aber die Blob-Containernamen werden nur beim Starten der Anwendung aufgelöst. Blob-Containername kann nicht geändert werden, während der Auftrag ausgeführt wird.

## <a id="manual"></a>Eine Funktion manuell auslösen

Verwenden, um eine Funktion manuell auszulösen, die `Call` oder `CallAsync` Methode für die `JobHost` Objekt und der `NoAutomaticTrigger` Attribut für die Funktion, wie im folgenden Beispiel gezeigt.

		public class Program
		{
		    static void Main(string[] args)
		    {
		        JobHost host = new JobHost();
		        host.Call(typeof(Program).GetMethod("CreateQueueMessage"), new { value = "Hello world!" });
		    }
		
		    [NoAutomaticTrigger]
		    public static void CreateQueueMessage(
		        TextWriter logger, 
		        string value, 
		        [Queue("outputqueue")] out string message)
		    {
		        message = value;
		        logger.WriteLine("Creating queue message: ", message);
		    }
		}

## <a id="logs"></a>Gewusst wie: Schreiben von Protokollen

Das Dashboard zeigt Protokolle an zwei Stellen: auf der Seite für den Webauftrag und auf der Seite für eines bestimmten Webauftragsaufrufs.

![Protokolle auf Webauftragsseiten](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

![Protokolle auf Funktionsaufrufseiten](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Ausgabe Console-Methoden, die in einer Funktion aufgerufen oder in, die `Main()` Methode wird angezeigt, in der Dashboard-Seite für den WebJob, nicht in die Seite für einen bestimmten Methodenaufruf. Die Ausgabe des "TextWriter"-Objekts, die Sie von einem Parameter in Ihrer Methodensignatur erhalten, wird auf der Dashboard-Seite eines bestimmten Methodenaufrufs angezeigt.

Die Konsolenausgabe kann nicht an einen bestimmten Methodenaufruf geknüpft werden, da die Konsole als Singlethread ausgeführt wird, während viele Aufgaben ggf. gleichzeitig ausgeführt werden. Deshalb versieht das SDK jeden Funktionsaufruf mit einem eigenen eindeutigen Protokollschreibobjekt.

Schreiben [Ablaufverfolgungsprotokolle](web-sites-dotnet-troubleshoot-visual-studio.md#logsoverview), verwenden Sie `Console.Out` (erstellt als INFO Protokolle) und `Console.Error` (erstellt von Protokollen, die als Fehler markiert). Eine Alternative ist die Verwendung [Ablaufverfolgung oder TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), bietet ausführlich, Warnung, und die kritischen Ebenen, zusätzlich zu den Informationen und Fehler. Ablaufverfolgungsprotokolle von Anwendungen werden in den Web-App-Protokolldateien, Azure-Tabellen oder Azure-Blobs angezeigt, je nachdem, wie Sie Ihre Azure-Web-App konfigurieren. Wie bei sämtlichen Konsolenausgaben werden die 100 letzten Anwendungsprotokolle auch auf der Seite "Dashboard" für den Webauftrag und nicht auf der Seite für die Funktionsaufruf angezeigt.

Die Konsolenausgabe wird nur im Dashboard angezeigt, wenn das Programm in einem Azure-Webauftrag ausgeführt wird, und nicht, wenn die Anwendung lokal oder in einer anderen Umgebung ausgeführt wird.

Sie können die Protokollierung deaktivieren [durch Festlegen der Dashboard-Verbindungszeichenfolge auf null](#config).

Im folgenden Beispiel sind mehrere Möglichkeiten zum Schreiben von Protokollen dargestellt:

		public static void WriteLog(
		    [QueueTrigger("logqueue")] string logMessage,
		    TextWriter logger)
		{
		    Console.WriteLine("Console.Write - " + logMessage);
		    Console.Out.WriteLine("Console.Out - " + logMessage);
		    Console.Error.WriteLine("Console.Error - " + logMessage);
		    logger.WriteLine("TextWriter - " + logMessage);
		}

Im SDK WebJobs-Dashboard die Ausgabe aus der `TextWriter` Objekt angezeigt wird, wenn Sie auf der Seite für einen bestimmten wechseln-Funktion aufrufen, und klicken Sie auf **Toggle Output**:

![Link zum Aufruf der "Click"-Funktion](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Protokolle auf Funktionsaufrufseiten](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Das WebJobs SDK-Dashboard die letzten 100 Zeilen der Konsole ausgegeben anzeigen einrichten, wenn Sie Seite für die WebJob (nicht für den Aufruf der Funktion zur), und klicken Sie auf **Toggle Output**.
 
![Ausgabe von "Click Toggle"](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

In einem fortlaufenden WebJob werden Anwendungsprotokolle in /data/jobs/continuous/*{webjobname}*/job_log.txt im Dateisystem Web app.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

In einer Azure-blob-das Anwendung Protokolle wie folgt aussehen: 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello World!, 2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello World!, 2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello World!,

Und in einer Azure-Tabelle der `Console.Out` und `Console.Error` Protokolle wie folgt aussehen:

![Informationsprotokoll in Tabelle](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Fehlerprotokoll in Tabelle](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a> Nächste Schritte

In dieser Anleitung wurden Codebeispiele bereitgestellt, in denen veranschaulicht wird, wie häufige Szenarien für das Arbeiten mit Azure-Warteschlangen behandelt werden. Weitere Informationen zur Verwendung von Azure-WebJobs und das WebJobs-SDK finden Sie unter [Azure WebJobs empfohlene Ressourcen](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->