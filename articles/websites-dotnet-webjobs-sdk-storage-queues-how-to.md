<properties 
	pageTitle="Verwenden von Azure-Warteschlangenspeicher mit dem Webaufträge-SDK" 
	description="Erfahren Sie, wie Sie Azure-Warteschlangenspeicher mit dem Webaufträge-SDK nutzen. Sie können Warteschlangen erstellen und löschen, Warteschlangennachrichten einfügen, einsehen, abrufen und löschen und vieles mehr." 
	services="web-sites, storage" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="web-sites" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="12/15/2014" 
	ms.author="tdykstra"/>

# Verwenden von Azure-Warteschlangenspeicher mit dem Webaufträge-SDK

Diese Anleitungen enthält C#-Codebeispiele, die zeigen, wie das Azure-Webaufträge-SDK, Version 1.x, mit dem Azure-Warteschlangenspeicherdienst verwendet wird.

Im Leitfaden wird davon ausgegangen, dass Sie wissen, [wie ein Webauftragsprojekt in Visual Studio mit Verbindungszeichenfolgen erstellt wird, die auf Ihr Speicherkonto zeigen](../websites-dotnet-webjobs-sdk-get-started/).

Wie in diesem Beispiel zeigen die meisten Codeausschnitte nur Funktionen und nicht den Code, der das  `JobHost`-Objekt erstellt:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## Inhaltsverzeichnis

-   [Auslösen einer Funktion, wenn eine Warteschlangennachricht empfangen wird](#trigger)
	- Zeichenfolgen-Warteschlangennachrichten
	- POCO-Warteschlangennachrichten
	- Asynchrone Funktionen
	- Typen, mit denen das "QueueTrigger"-Attribut arbeitet
	- Abrufalgorithmus
	- Mehrere Instanzen
	- Parallele Ausführung
	- Abrufen der Warteschlange oder von Metadaten der Warteschlangennachricht
	- Ordnungsgemäßes Herunterfahren
-   [Erstellen einer Warteschlangennachricht, während eine Warteschlangennachricht verarbeitet wird](#createqueue)
	- Zeichenfolgen-Warteschlangennachrichten
	- POCO-Warteschlangennachrichten
	- Erstellen mehrerer Nachrichten oder in asynchronen Funktionen
	- Typen, mit denen das "Queue"-Attribut arbeitet
	- Verwenden von Webaufträge-SDK-Attributen im Hauptteil einer Funktion
-   [Lesen und Schreiben von BLOBs beim Verarbeiten einer Warteschlangennachricht](#blobs)
	- Zeichenfolgen-Warteschlangennachrichten
	- POCO-Warteschlangennachrichten
	- Typen, mit denen das "Blob"-Attribut arbeitet
-   [Behandlung von nicht verarbeitbaren Nachrichten](#poison)
	- Automatische Behandlung von nicht verarbeitbaren Nachrichten
	- Manuelle Behandlung von nicht verarbeitbaren Nachrichten
-   [Festlegen von Konfigurationsoptionen](#config)
	- Festlegen von SDK-Verbindungszeichenfolgen im Code
	- Konfigurieren von "QueueTrigger"-Einstellungen
	- Festlegen von Werten für Konstruktorparameter im Webaufträge-SDK im Code
-   [Manuelles Auslösen einer Funktion](#manual)
-   [Schreiben von Protokollen](#logs)
-   [Nächste Schritte](#nextsteps)

## <a id="trigger"></a>Auslösen einer Funktion, wenn eine Warteschlangennachricht empfangen wird

Zum Schreiben einer Funktion, die das Webaufträge-SDK aufruft, wenn eine Warteschlangennachricht empfangen wird, verwenden Sie das  `QueueTrigger`-Attribut. Der Attributkonstruktor verwendet einen Zeichenfolgenparameter, der den Namen der Warteschlange angibt. Sie können [den Warteschlangennamen auch dynamisch festlegen](#config).

### Zeichenfolgen-Warteschlangennachrichten

Im folgenden Beispiel enthält die Warteschlange eine Zeichenfolgenmeldung, weshalb  `QueueTrigger` auf einen Zeichenfolgenparameter namens  `logMessage` angewendet wird, der den Inhalt der Warteschlangennachricht enthält. Die Funktion [schreibt eine Protokollnachricht an das Dashboard](#logs).
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

Neben  `string` kann der Parameter ein Bytearray, ein  `CloudQueueMessage`-Objekt oder ein POCO sein, das Sie definieren.

### POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))-Warteschlangennachrichten

Im folgenden Beispiel enthält die Warteschlangennachricht JSON für ein  `BlobInformation`-Objekt, das eine  `BlobName`-Eigenschaft enthält. Das SDK deserialisiert das Objekt automatisch.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Das SDK verwendet das [Newtonsoft.Json NuGet-Paket](http://www.nuget.org/packages/Newtonsoft.Json) zum Serialisieren und Deserialisieren von Nachrichten. Wenn Sie Warteschlangennachrichten in einem Programm erstellen, das nicht das Webaufträge-SDK verwendet, können Sie Code wie im folgenden Beispiel zum Erstellen einer POCO-Warteschlangennachricht schreiben, die das SDK analysieren kann. 

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Asynchrone Funktionen

Die folgende asynchrone Funktion [schreibt ein Protokoll an das Dashboard](#logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Async-Funktion können, wie in der folgenden Abbildung dargestellt, über ein [Abbruchtoken ](http://www.asp.net/mvc/overview/performance/using-asynchronous-methods-in-aspnet-mvc-4#CancelToken) verfügen, das ein BLOB kopiert. (Eine Erläuterung des Platzhalters  `queueTrigger` finden Sie im Abschnitt [BLOBS](#blobs) .)

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### <a id="qtattributetypes"></a> Typen, mit denen das "QueueTrigger"-Attribut arbeitet

Sie können  `QueueTrigger` mit den folgenden Typen verwenden:

* `string`
* Einem als JSON serialisierten POCO-Typ
* `byte[]`
* `CloudQueueMessage`

### <a id="polling"></a> Abrufalgorithmus

Das SDK implementiert einen zufälligen exponentiellen Backoff-Algorithmus, um die Auswirkungen des Abfragens von Warteschlangen im Leerlauf auf Speichertransaktionskosten zu reduzieren.  Wenn eine Nachricht gefunden wird, wartet das SDK zwei Sekunden und prüft dann, ob eine weitere Meldung vorhanden ist. Wenn keine Nachricht gefunden wird, wartet es ca. vier Sekunden vor dem erneuten Versuch. Nach weiteren fehlerhaften Versuchen, eine Warteschlangennachricht abzurufen, erhöht sich die Wartezeit weiterhin, bis die maximale Wartezeit von standardmäßig 1 Minute erreicht wird. [Die maximale Wartezeit ist konfigurierbar](#config).

### <a id="instances"></a> Mehrere Instanzen

Falls Ihre Website auf mehreren virtuellen Instanzen läuft, wird ein kontinuierlicher Webauftrag auf allen Computern ausgeführt, und jeder Computer wartet auf Auslöser und versucht, Funktionen auszuführen. In manchen Szenarien kann dies dazu führen, dass manche Funktionen dieselben Daten doppelt verarbeiten. Die Funktionen sollten daher "idempotent" geschrieben sein, d. h. beim wiederholten Aufrufen mit denselben Eingangsdaten dürfen keine duplizierten Resultate entstehen.  

### <a id="parallel"></a> Parallele Ausführung

Wenn mehrere Funktionen unterschiedliche Warteschlangen überwachen, ruft das SDK diese parallel auf, wenn Nachrichten gleichzeitig empfangen werden. 

Das Gleiche gilt auch, wenn mehrere Nachrichten für eine einzige Warteschlange empfangen werden. Das SDK ruft standardmäßig jeweils einen Batch von 16 Warteschlangennachrichten auf und führt die Funktion, die diese verarbeitet, parallel aus. [Die Batchgröße ist konfigurierbar](#config). Wenn die zu verarbeitende Anzahl die Hälfte der Batchgröße erreicht, ruft das SDK einen weiteren Batch ab und beginnt mit der Verarbeitung dieser Nachrichten. Aus diesem Grund beträgt die maximale Anzahl der pro Funktion verarbeiteten Nachrichten die 1,5-fache Batchgröße. Dieser Grenzwert gilt separat für jede Funktion, die ein  `QueueTrigger`-Attribut aufweist. Wenn Sie keine parallele Ausführung für in einer Warteschlange empfangene Nachrichten wünschen, legen Sie die Batchgröße auf 1 fest.

### <a id="queuemetadata"></a>Abrufen der Warteschlange oder von Metadaten der Warteschlangennachricht

Sie können die folgenden Nachrichteneigenschaften abrufen, indem Sie Parameter zur Methodensignatur hinzufügen:

* `DateTimeOffset` expirationTime
* `DateTimeOffset` insertionTime
* `DateTimeOffset` nextVisibleTime
* `string`QueueTrigger (enthält Nachrichtentext)
* `string` id
* `string` popReceipt
* `int` dequeueCount

Wenn Sie direkt mit der Azure-Speicher-API arbeiten möchten, können Sie auch einen  `CloudStorageAccount`-Parameter hinzufügen.

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

Eine Funktion, die in einem fortlaufenden WebJob ausgeführt wird, kann einen  `CancellationToken`-Parameter akzeptieren, über den das Betriebssystem die Möglichkeit hat, die Funktion zu benachrichtigen, wenn der Webauftrag beendet wird. Sie können diese Benachrichtigung verwenden, um sicherzustellen, dass die Funktion nicht auf eine Weise unerwartet beendet wird, die die Daten in einem inkonsistenten Zustand hinterlässt.

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

**Hinweis** Im Dashboard werden Status und Ausgabe von Funktionen, die beendet wurden, möglicherweise nicht ordnungsgemäß angezeigt.
 
Weitere Informationen finden Sie unter [Ordnungsgemäßes Herunterfahren von WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a id="createqueue"></a> Erstellen einer Warteschlangennachricht, während eine Warteschlangennachricht verarbeitet wird

Um eine Funktion schreiben, die eine neue Warteschlangennachricht erstellt, verwenden Sie das  `Queue`-Attribut. Wie bei  `QueueTrigger` übergeben Sie den Warteschlangennamen als Zeichenfolge, oder Sie [legen den Warteschlangennamen dynamisch fest](#config).

### Zeichenfolgen-Warteschlangennachrichten

Im folgenden nicht asynchronen Beispiel wird eine neue Warteschlangennachricht in der Warteschlange mit dem Namen "outputqueue" mit dem gleichen Inhalt wie die Warteschlangennachricht erstellt, die in der Warteschlange mit dem Namen "inputqueue" empfangen wird. (Für asynchrone Funktionen verwenden Sie  `IAsyncCollector<T>`, wie weiter unten in diesem Abschnitt gezeigt.)


		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}
  
### POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))-Warteschlangennachrichten

Zum Erstellen einer Warteschlangennachricht, die ein POCO-Objekt anstelle einer Zeichenfolge enthält, übergeben Sie den POCO-Typs als Ausgabeparameter an den  `Queue`-Attributkonstruktor.
 
		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

Das SDK serialisiert das Objekt automatisch an JSON. Es wird immer eine Warteschlangennachricht erstellt, selbst wenn das Objekt Null ist.

### Erstellen mehrerer Nachrichten oder in asynchronen Funktionen

Um mehrere Nachrichten zu erstellen, legen Sie als Parametertyp für die Ausgabewarteschlange `ICollector<T>` oder  `IAsyncCollector<T>` fest, wie im folgenden Beispiel gezeigt.

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Jede Warteschlangennachricht wird unmittelbar nach dem Aufruf der `Add`-Methode erstellt.

### Typen, mit denen das "Queue"-Attribut arbeitet

Sie können das `Queue`-Attribut für die folgenden Parametertypen verwenden:

* `out string` (erstellt Warteschlangennachricht, wenn der Parameterwert nicht Null ist, sobald die Funktion beendet wird.)
* `out byte[]` (funktioniert wie `string`) 
* `out CloudQueueMessage` (funktioniert wie `string`) 
* `out POCO`(ein serialisierbarer Typ, erstellt eine Nachricht mit einem NULL-Objekt, wenn der Parameter NULL ist, sobald die Funktion beendet)
* `ICollector`
* `IAsyncCollector`
* `CloudQueue` (zum manuellen Erstellen von Nachrichten, die direkt die Azure-Speicher-API verwenden)

### <a id="ibinder"></a>Verwenden von Webaufträge-SDK-Attributen im Hauptteil einer Funktion

Wenn Sie weitere Aufgaben in Ihrer Funktion vor der Verwendung eines Attributs des Webaufträge-SDK erledigen möchten, wie z. B. `Queue`, `Blob` oder `Table`, können Sie die `IBinder`-Schnittstelle verwenden.

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

Die `IBinder`-Schnittstelle kann auch mit den Attributen `Table` und  `Blob` verwendet werden.

## <a id="blobs"></a> Lesen und Schreiben von BLOBs und Tabellen beim Verarbeiten einer Warteschlangennachricht

Mithilfe der Attribute `Blob` und `Table` können Sie BLOBs und Tabellen lesen und schreiben. Die Beispiele in diesem Abschnitt gelten für BLOBs. Codebeispiele, die zeigen, wie Sie Prozesse auslösen, wenn BLOBs erstellt oder aktualisiert werden, finden Sie unter [Verwenden von Azure-BLOB-Speicher mit dem Webaufträge-SDK](../websites-dotnet-webjobs-sdk-storage-blobs-how-to/). Codebeispiele für das Lesen und Schreiben von Tabellen finden Sie unter [Verwenden von Azure-Tabellenspeicher mit dem Webaufträge-SDK](../websites-dotnet-webjobs-sdk-storage-tables-how-to/).

### Zeichenfolge-Warteschlangennachrichten, die BLOB-Vorgänge auslösen

Für eine Warteschlangennachricht, die eine Zeichenfolge enthält, ist  `queueTrigger` ein Platzhalter, der im  `blobPath`-Parameter des  `Blob`-Attributs verwendet werden kann, der den Inhalt der Nachricht enthält. 

Im folgenden Beispiel werden  `Stream`-Objekte zum Lesen und Schreiben von BLOBs verwendet. Die Warteschlangennachricht enthält den Namen eines BLOBs, das sich im "textblobs"-Container befindet. Eine Kopie des Blobs, an deren Namen "-new" angefügt wird, wird im gleichen Container erstellt. 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

Der  `Blob`-Attributkonstruktor verwendet den  `blobPath`-Parameter, der den Container und BLOB-Namen angibt. Weitere Informationen zu diesen Platzhalter finden Sie unter [Verwenden von Azure-BLOB-Speicher mit dem Webaufträge-SDK](../websites-dotnet-webjobs-sdk-storage-blobs-how-to/), .

Wenn das Attribut ein  `Stream`-Objekt anpasst, gibt ein anderer Konstruktorparameter den  `FileAccess`-Modus als Lesen, Schreiben oder Lesen/ Schreiben an. 

Im folgenden Beispiel wird ein  `CloudBlockBlob`-Objekt zum Löschen eines BLOBs verwendet. Die Warteschlangennachricht ist der Name des BLOBs.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### <a id="pocoblobs"></a> POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))-Warteschlangennachrichten

Für POCO-Objekte, die als JSON in der Warteschlangennachricht gespeichert werden, können Sie Platzhalter verwenden, die Eigenschaften des Objekts im  `blobPath`-Parameter des  `Queue`-Attributs benennen. Sie können auch [Eigenschaftennamen von Warteschlangenmetadaten](#queuemetadata) als Platzhalter verwenden. 

Das folgende Beispiel kopiert ein BLOB in ein neues BLOB mit einer anderen Erweiterung. Die Warteschlangennachricht ist ein  `BlobInformation`-Objekt, das die Eigenschaften  `BlobName` und  `BlobNameWithoutExtension` enthält. Die Eigenschaftsnamen dienen als Platzhalter im BLOB-Pfad für die  `Blob`-Attribute. 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

Das SDK verwendet das [Newtonsoft.Json NuGet-Paket](http://www.nuget.org/packages/Newtonsoft.Json) zum Serialisieren und Deserialisieren von Nachrichten. Wenn Sie Warteschlangennachrichten in einem Programm erstellen, das nicht das Webaufträge-SDK verwendet, können Sie Code wie im folgenden Beispiel zum Erstellen einer POCO-Warteschlangennachricht schreiben, die das SDK analysieren kann.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

Wenn Sie an Ihrer Funktion vor dem Binden eines Blobs an ein Objekt einige Änderungen vornehmen müssen, können Sie das Attribut im Text der Funktion verwenden, [wie zuvor für das Queue-Attribut beschrieben](#ibinder).

### <a id="blobattributetypes"></a> Typen, mit denen das "Blob"-Attribut arbeitet
 
Das  `Blob`-Attribut kann mit den folgenden Typen verwendet werden:

* `Stream`(Lesen oder Schreiben, wird mit dem "FileAccess"-Konstruktorparameter angegeben)
* `TextReader`
* `TextWriter`
* `string` (Lesen)
* `out string`(Schreiben, erstellt ein BLOB nur dann, wenn der Parameter bei Rückgabe der Funktion ungleich NULL ist)
* POCO (Lesen)
* POCO (Schreiben; erstellt immer ein BLOB, erstellt ein NULL-Objekt , wenn der POCO-Parameter bei Rückgabe der Funktion NULL ist)
* `CloudBlobStream` (Schreiben)
* `ICloudBlob`(Lesen oder Schreiben)
* `CloudBlockBlob`(Lesen oder Schreiben) 
* `CloudPageBlob`(Lesen oder Schreiben) 

## <a id="poison"></a> Behandlung von nicht verarbeitbaren Nachrichten

Nachrichten, deren Inhalt bewirkt, dass bei einer Funktion ein Fehler auftritt, werden als  *poison messages* (nicht verarbeitbare Nachrichten) bezeichnet. Wenn bei der Funktion ein Fehler auftritt, wird die Warteschlangennachricht nicht gelöscht, sondern schließlich erneut aufgegriffen, wodurch verursacht wird, dass der Zyklus wiederholt wird. Das SDK kann den Zyklus nach einer begrenzten Anzahl von Iterationen automatisch unterbrechen, oder Sie können dies manuell vornehmen.

### Automatische Behandlung von nicht verarbeitbaren Nachrichten

Das SDK ruft eine Funktion bis zu fünf Mal auf, um eine Warteschlangenachricht zu verarbeiten. Wenn der fünfte Versuch fehlschlägt, wird die Nachricht in eine Warteschlange für nicht verarbeitete Nachrichten verschoben. [Die maximale Anzahl von Wiederholungen ist konfigurierbar](#config). 

Die Warteschlange für nicht verarbeitete Nachrichten weist den Namen "*{originalqueuename}*-poison" auf. Sie können eine Funktion schreiben, um Nachrichten aus der Warteschlange für nicht verarbeitete Nachrichten zu verarbeiten, indem Sie diese protokollieren oder eine Benachrichtigung senden, dass ein manueller Eingriff erforderlich ist. 

Im folgenden Beispiel tritt bei der  `CopyBlob`-Funktion ein Fehler auf, wenn eine Warteschlangennachricht den Namen eines BLOBs enthält, das nicht vorhanden ist. In diesem Fall wird die Nachricht aus der Warteschlange "copyblobqueue" in die Warteschlange "copyblobqueue-poison" verschoben. Die  `ProcessPoisonMessage` protokolliert dann die nicht verarbeitbare Nachricht.

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

![Console output for poison message handling](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### Manuelle Behandlung von nicht verarbeitbaren Nachrichten

Sie können die Anzahl der Auswahl einer Nachricht für die Verarbeitung abrufen, indem Sie einen  `int`-Parameter mit dem Namen  `dequeueCount` zu Ihrer Funktion hinzufügen. Anschließend können Sie die Häufigkeit des Entfernens aus der Warteschlange im Funktionscode überprüfen und Ihre eigene Behandlung von nicht verarbeitbaren Nachrichten ausführen, wenn die Anzahl einen Schwellenwert überschreitet, wie in dem folgendem Beispiel dargestellt.

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

## <a id="config"></a> Festlegen von Konfigurationsoptionen

Sie können den Typ  `JobHostConfiguration` verwenden, um die folgenden Konfigurationsoptionen festzulegen:

* Festlegen der SDK-Verbindungszeichenfolgen im Code
* Konfigurieren Sie  `QueueTrigger`-Einstellungen, wie z. B. die maximale Anzahl für das Entfernen aus der Warteschlange.
* Rufen Sie Warteschlangennamen aus der Konfiguration ab.

### <a id="setconnstr"></a>Festlegen von SDK-Verbindungszeichenfolgen im Code

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

### <a id="configqueue"></a>Konfigurieren von "QueueTrigger"-Einstellungen

Sie können die folgenden Einstellungen konfigurieren, die für die Verarbeitung von Warteschlangennachrichten gelten:

- Die maximale Anzahl von Warteschlangennachrichten, die gleichzeitig abgerufen werden, und ausgeführt werden sollen (der Standardwert ist 16).
- Die maximale Anzahl von Wiederholungen, bevor eine Warteschlangennachricht an eine Warteschlange für nicht verarbeitete Nachrichten gesendet wird (der Standardwert ist 5).
- Die maximale Wartezeit vor einem erneuten Abruf, wenn eine Warteschlange leer ist (der Standardwert ist 1 Minute).

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

### <a id="setnamesincode"></a>Festlegen von Werten für Konstruktorparameter im Webaufträge-SDK im Code

Mitunter möchten Sie einen Warteschlangennamen, einen BLOB-Namen oder Container oder einen Tabellennamen im Code angeben, anstatt ihn hart zu codieren. Sie möchten z. B. den Warteschlangennamen für  `QueueTrigger` in einer Datei oder Umgebungsvariablen angeben. 

Möglich wird dies, indem Sie ein  `NameResolver`-Objekts an den  `JobHostConfiguration`-Typ übergeben. Sie fügen in Konstruktorparametern von Webaufträge-SDK-Attributn spezielle Platzhalter hinzu, die von Prozentzeichen (%) umgeben werden, und Ihr  `NameResolver`-Code gibt die tatsächlichen Werte an, die anstelle dieser Platzhalter verwendet werden sollen.

Angenommen, Sie möchten eine Warteschlange mit dem Namen "logqueuetest" in der Testumgebung und eine Warteschlange mit dem Namen "logqueueprod" in einer Produktionsumgebung verwenden. Anstelle eines hartcodierten Warteschlangennamens möchten Sie den Namen eines Eintrags in der Auflistung  `appSettings` angeben, die den tatsächlichen Namen der Warteschlange hätte. Wenn der  `appSettings`-Schlüssel "logqueue" ist, könnte die Funktion wie im folgenden Beispiel aussehen.

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

Die  `NameResolver`-Klasse kann dann den Namen der Warteschlange aus  `appSettings` abrufen, wie im folgenden Beispiel gezeigt:

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

Sie übergeben die  `NameResolver`-Klasse an das  `JobHost`-Objekt, wie im folgenden Beispiel gezeigt.

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 
**Hinweis:** Warteschlangen-, Tabellen- und BLOB-Namen werden bei jedem Aufruf einer Funktion aufgelöst, doch BLOB-Containernamen werden nur beim Starten der Anwendung aufgelöst. Der BLOB-Containername kann nicht geändert werden, während der Auftrag ausgeführt wird.

## <a id="manual"></a>Manuelles Auslösen einer Funktion

Zum manuellen Auslösen einer Funktion verwenden Sie die Methode  `Call` oder  `CallAsync` für das  `JobHost`-Objekt und das  `NoAutomaticTrigger`-Attribut für die Funktion, wie im folgenden Beispiel gezeigt. 

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

## <a id="logs"></a>Schreiben von Protokollen

Das Dashboard zeigt Protokolle an zwei Stellen: auf der Seite für den Webauftrag und auf der Seite für eines bestimmten Webauftragsaufrufs. 

![Logs in WebJob page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Methoden für dieKonsolenausgabe, die Sie in einer Funktion oder in der  `Main()`-Methode aurufen, werden auf der Dashboard-Seite für den Webauftrag und nicht auf den Seite eines bestimmten Methodenaufrufs angezeigt. Die Ausgabe des "TextWriter"-Objekts, die Sie von einem Parameter in Ihrer Methodensignatur erhalten, wird auf der Dashboard-Seite eines bestimmten Methodenaufrufs angezeigt.

Die Konsolenausgabe kann nicht an einen bestimmten Methodenaufruf geknüpft werden, da die Konsole als Singlethread ausgeführt wird, während viele Aufgaben ggf. gleichzeitig ausgeführt werden. Deshalb versieht das SDK jeden Funktionsaufruf mit einem eigenen eindeutigen Protokollschreibobjekt.

Zum Schreiben von [Ablaufverfolgungsprotokollen](../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview) verwenden Sie  `Console.Out` (erstellt als INFO markierte Protokolle) und  `Console.Error` (erstellt von als FEHLER markierte Protokolle). Eine Alternative ist die Verwendung von [Trace oder TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx), zusätzlich zu INFO und FEHLER die Stufen AUSFÜHRLICH, WARNUNG und KRITISCH geboten werden. Ablaufverfolgungsprotokolle von Anwendungen werden in den Website-Protokolldateien, Azure-Tabellen oder Azure-BLOBs angezeigt, je nachdem, wie Sie Ihre Azure-Website konfigurieren. Wie bei sämtlichen Konsolenausgaben werden die 100 letzten Anwendungsprotokolle auch auf der Seite "Dashboard" für den Webauftrag und nicht auf der Seite für die Funktionsaufruf angezeigt. 

Die Konsolenausgabe wird nur im Dashboard angezeigt, wenn das Programm in einem Azure-Webauftrag ausgeführt wird, und nicht, wenn die Anwendung lokal oder in einer anderen Umgebung ausgeführt wird.

Sie können die Protokollierung deaktivieren, indem Sie [die Dashboard-Verbindungszeichenfolge auf Null festlegen](#config).

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

Im Dashboard des Webaufträge-SDK wird die Ausgabe des  `TextWriter`-Objekts angezeigt, wenn Sie zu der Seite für einen bestimmten Funktionsaufruf wechseln und auf **Toggle Output** klicken:

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Im Dashboard des Webaufträge-SDK werden die letzten 100 Zeilen der Konsolenausgabe angezeigt, wenn Sie zur Seite für den Webauftrag (nicht für den Funktionsaufruf) wechseln und auf **Toggle Output** klicken.
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

Bei einem fortlaufenden Webauftrag werden Anwendungsprotokolle in /data/jobs/continuous/*{webjobname}*/job_log.txt im Dateisystem der Website angezeigt.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

Bei einem Azure-BLOB sieht das Anwendungsprotokoll folgendermaßen aus:
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Und bei einer Azure-Tabelle sehen die Protokolle  `Console.Out` und  `Console.Error` wie folgt aus:

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a>Nächste Schritte

In dieser Anleitung wurden Codebeispiele bereitgestellt, in denen veranschaulicht wird, wie häufige Szenarien für das Arbeiten mit Azure-Warteschlangen behandelt werden. Weitere Informationen zur Verwendung von Azure-Webaufträgen und dem Webaufträge-SDK finden Sie unter [Empfohlene Ressourcen für Azure-Webaufträge](http://go.microsoft.com/fwlink/?linkid=390226).




<!--HONumber=42-->
