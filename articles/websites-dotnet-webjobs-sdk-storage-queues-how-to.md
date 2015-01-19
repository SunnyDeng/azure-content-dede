<properties linkid="" pageTitle="Arbeiten mit dem Azure-Warteschlangenspeicher mithilfe des WebJobs SDKs" metaKeywords="WebJobs SDK Azure Warteschlangenspeicher .NET C#" description="Informationen zum Arbeiten mit Azure-Warteschlangenspeicher im WebJobs-SDK. Erstellen und Löschen von Warteschlangen. Einfügen, Einsehen, Abrufen und Löschen von Warteschlangen-Nachrichten und vieles mehr." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="web-sites,storage" documentationCenter=".NET" title="How to work with Azure queue storage using the WebJobs SDK" authors="tdykstra" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="tdykstra" />

# Arbeiten mit Azure-Warteschlangenspeicher mithilfe des WebJobs SDKs

In diesem Leitfaden wird gezeigt, wie C#-Code für häufige Szenarien mithilfe des
Azure-Warteschlangenspeicherdiensts und des Azure WebJobs SDKs, Version 1.0.0, geschrieben wird.

Es wird vorausgesetzt, dass Sie bereits wissen, [was das WebJobs SDK ist](../websites-webjobs-sdk-storage-queues-how-to) und [grundlegende Aufgaben ausführen können],(../websites-dotnet-webjobs-sdk-get-started/)z. B. das NuGet-Paket des WebJobs SDKs installieren, ein Azure-Speicherkonto erstellen und Verbindungszeichenfolgen für das WebJobs SDK erstellen, die auf Ihr Speicherkonto zeigen.

Wie in diesem Beispiel zeigen die meisten Codeausschnitte nur Funktionen und nicht den Code, der das JobHost-Objekt erstellt:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## Inhaltsverzeichnis

-   [Auslösen einer Funktion, wenn eine Warteschlangennachricht empfangen wird](#trigger)
	- Zeichenfolgen-Warteschlangennachrichten
	- POCO-Objekt-Warteschlangennachrichten
	- Asynchrone Funktionen
	- Abfragealgorithmus
	- Parallele Ausführung
	- Abrufen der Warteschlange oder von Metadaten der Warteschlangennachricht
	- Ordnungsgemäßes Herunterfahren
-   [Erstellen einer Warteschlangennachricht, während eine Warteschlangennachricht verarbeitet wird](#createqueue)
	- Zeichenfolgen-Warteschlangennachrichten
	- POCO-Objekt-Warteschlangennachrichten
	- Erstellen mehrerer Nachrichten
	- Verwenden von Warteschlangenattributen im Hauptteil einer Funktion
-   [Lesen und Schreiben von Blobs beim Verarbeiten einer Warteschlangennachricht](#blobs)
	- Zeichenfolgen-Warteschlangennachrichten
	- POCO-Objekt-Warteschlangennachrichten
	- Verwenden von Blob-Attributen im Hauptteil einer Funktion
-   [Behandlung von nicht verarbeitbaren Nachrichten](#poison)
	- Automatische Behandlung von nicht verarbeitbaren Nachrichten
	- Manuelle Behandlung von nicht verarbeitbaren Nachrichten
-   [Festlegen von Konfigurationsoptionen](#config)
	- Festlegen von SDK-Verbindungszeichenfolgen im Code
	- Konfigurieren von QueueTrigger-Einstellungen
	- Abrufen von Warteschlangennamen aus der Konfiguration
-   [Manuelles Auslösen einer Funktion](#manual)
-   [Schreiben von Protokollen](#logs)
-   [Nächste Schritte](#nextsteps)

## <a id="trigger"></a> Auslösen einer Funktion, wenn eine Warteschlangennachricht empfangen wird

Um eine Funktion zu schreiben, die das SDK bei Empfang einer Warteschlangenachricht aufruft, verwenden Sie das QueueTrigger-Attribut mit einer Zeichenfolge oder einem POCO-Parameter, je nachdem, was in der Nachricht enthalten ist. Der Attributkonstruktor verwendet einen Zeichenfolgenparameter, der den Namen der Warteschlange angibt. Sie können [den Warteschlangennamen auch dynamisch festlegen](#config).

Falls Ihre Website auf mehreren virtuellen Computern läuft, wird der WebJob auf allen Computern ausgeführt, und jeder Computer wartet auf Auslöser und versucht, Funktionen auszuführen. In manchen Szenarien kann dies dazu führen, dass manche Funktionen dieselben Daten doppelt verarbeiten. Die Funktionen sollten daher "idempotent" geschrieben sein, d. h. beim wiederholten Aufrufen mit denselben Eingangsdaten dürfen keine duplizierten Resultate entstehen.  

### Zeichenfolgen-Warteschlangennachrichten

Im folgenden Beispiel enthält die Warteschlange eine Zeichenfolgenmeldung, "QueueTrigger" wird daher auf einen Zeichenfolgenparameter namens "logMessage" angewendet, der den Inhalt der Warteschlangennachricht enthält. Die Funktion [schreibt eine Protokollmeldung an das Dashboard](#logs).
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

Neben "string" kann der Parameter ein Bytearray, ein CloudQueueMessage-Objekt oder ein POCO-Objekt sein, das Sie definieren.

### POCO-Objekt-Warteschlangennachrichten

Im folgenden Beispiel enthält die Warteschlangennachricht JSON für ein BlobInformation-Objekt, das eine BlobName-Eigenschaft enthält. Das SDK deserialisiert das Objekt automatisch.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Im folgenden Beispiel wird veranschaulicht, wie eine POCO-Warteschlangennachricht ohne Verwendung des WebJobs SDKs erstellt wird, die das SDK analysieren kann. Das SDK verwendet das [Newtonsoft.Json NuGet-Paket](http://www.nuget.org/packages/Newtonsoft.Json) zum Serialisieren und Deserialisieren von Meldungen.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Asynchrone Funktionen

Die folgende asynchrone Funktion [schreibt ein Protokoll an das Dashboard](#logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Async-Funktion können, wie in der folgenden Abbildung dargestellt, über ein Abbruchtoken verfügen, das einen Blob kopiert. (Eine Erläuterung des Platzhalters 'QueueTrigger' finden Sie im Abschnitt [Blobs](#blobs) .)

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### Abfragealgorithmus

Das SDK implementiert einen zufälligen exponentiellen Backoff-Algorithmus, um die Auswirkungen des Abfragens von Warteschlangen im Leerlauf auf Speichertransaktionskosten zu reduzieren.  Wenn eine Nachricht gefunden wird, wartet das SDK zwei Sekunden und prüft dann, ob eine weitere Meldung vorhanden ist. Wenn keine Nachricht gefunden wird, wartet es ca. vier Sekunden vor dem erneuten Versuch. Nach weiteren fehlerhaften Versuchen, eine Warteschlangennachricht abzurufen, erhöht sich die Wartezeit weiterhin, bis die maximale Wartezeit von standardmäßig 1 Minute erreicht wird. [Die maximale Wartezeit ist konfigurierbar](#config).

### Parallele Ausführung

Wenn mehrere Funktionen unterschiedliche Warteschlangen überwachen, ruft das SDK diese parallel auf, wenn Nachrichten gleichzeitig empfangen werden. 

Das Gleiche gilt auch, wenn mehrere Nachrichten für eine einzige Warteschlange empfangen werden. Das SDK ruft standardmäßig jeweils einen Batch von 16 Warteschlangennachrichten auf und führt die Funktion, die diese verarbeitet, parallel aus. [Die Batchgröße ist konfigurierbar](#config). Wenn die zu verarbeitende Anzahl die Hälfte der Batchgröße erreicht, ruft das SDK einen weiteren Batch ab und beginnt mit der Verarbeitung dieser Nachrichten. Aus diesem Grund beträgt die maximale Anzahl der pro Funktion verarbeiteten Nachrichten die 1,5-fache Batchgröße. Dieser Grenzwert gilt separat für jede Funktion, die ein QueueTrigger-Attribut aufweist. Wenn Sie keine parallele Ausführung für in einer Warteschlange empfangene Nachrichten wünschen, legen Sie die Batchgröße auf 1 fest.

### <a id="queuemetadata"></a>Abrufen der Warteschlange oder von Metadaten der Warteschlangennachricht

Sie können die folgenden Nachrichteneigenschaften abrufen, indem Sie Parameter zur Methodensignatur hinzufügen:

* 'DateTimeOffset' expirationTime
* 'DateTimeOffset' insertionTime
* 'DateTimeOffset' nextVisibleTime
* 'string' queueTrigger (enthält Nachrichtentext)
* 'string' id
* 'string' popReceipt
* 'int' dequeueCount

Wenn Sie direkt mit der Azure-Speicher-API arbeiten möchten, können Sie auch einen CloudStorageAccount-Parameter hinzufügen.

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

Eine Funktion, die in einem fortlaufenden WebJob ausgeführt wird, kann einen CancellationToken-Parameter akzeptieren, über den das Betriebssystem die Möglichkeit hat, die Funktion zu benachrichtigen, wenn der WebJob beendet wird. Sie können diese Benachrichtigung verwenden, um sicherzustellen, dass die Funktion nicht auf eine Weise unerwartet beendet wird, die die Daten in einem inkonsistenten Zustand hinterlässt.

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

**Hinweis:** Im Dashboard werden Status und Ausgabe von Funktionen, die beendet wurden, möglicherweise nicht korrekt angezeigt.
 
Weitere Informationen finden Sie unter [Ordnungsgemäßes Herunterfahren von WebJobs](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR).   

## <a id="createqueue"></a> Erstellen einer Warteschlangennachricht, während eine Warteschlangennachricht verarbeitet wird

Um eine Funktion schreiben, die eine neue Warteschlangennachricht erstellt, verwenden Sie das Queue-Attribut in einer Ausgabezeichenfolge oder einem POCO-Parameter. Wie bei "QueueTrigger" übergeben Sie den Warteschlangennamen als Zeichenfolge, oder Sie [legen den Warteschlangennamen dynamisch fest](#config).

### Zeichenfolgen-Warteschlangennachrichten

Im folgenden Beispiel wird eine neue Warteschlangennachricht in der Warteschlange mit dem Namen "outputqueue" mit dem gleichen Inhalt wie die Warteschlangennachricht erstellt, die in der Warteschlange mit dem Namen "inputqueue" empfangen wird.

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}

Wenn der Ausgabeparametertyp einer der folgenden Typen ist und das Objekt nicht Null ist, wenn die Funktion beendet wird, erstellt das SDK eine Warteschlangennachricht:

* 'string' 
* 'byte[]'
* Ein serialisierbarer POCO-Typ, den Sie definieren
* 'CloudQueueMessage'

Informationen zum Erstellen mehrerer Nachrichten finden Sie unter **Erstellen mehrerer Nachrichten** weiter unten in diesem Abschnitt.

Wenn Sie Nachrichten manuell senden möchten, können Sie auch 'CloudQueue' als Ausgabeparametertyp verwenden.

### POCO-Objekt-Warteschlangennachrichten

Zum Erstellen einer Warteschlangennachricht, die ein POCO-Objekt anstelle einer Zeichenfolge enthält, übergeben Sie den POCO-Typs als Ausgabeparameter an den Queue-Attributkonstruktor. 

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

Das SDK serialisiert das Objekt automatisch an JSON. Es wird immer eine Warteschlangennachricht erstellt, selbst wenn das Objekt Null ist.

### Erstellen mehrerer Nachrichten

Um mehrere Nachrichten zu erstellen, legen Sie den Parametertyp für die Ausgabewarteschlange auf 'ICollector<T>' oder 'IAsyncCollector' fest, wie im folgenden Beispiel gezeigt.

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Jede Warteschlangennachricht wird unmittelbar nach dem Aufruf der Add-Methode erstellt.

### <a id="queuebody"></a>Verwenden von Warteschlangenattributen im Hauptteil einer Funktion

Wenn Sie an Ihrer Funktion vor Verwendung des Queue-Attributs zum Erstellen einer Warteschlangennachricht einige Änderungen vornehmen müssen, können die IBinder-Schnittstelle verwenden, um ein CloudQueue-Objekt abzurufen, über das Sie direkt mit einer Warteschlange arbeiten können. 

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

## <a id="blobs"></a> Lesen und Schreiben von Blobs und Tabellen beim Verarbeiten einer Warteschlangennachricht

Mithilfe der Attribute 'Blob' und 'Table' können Sie Blobs und Tabellen lesen und schreiben. Die Beispiele in diesem Abschnitt gelten für Blobs.

Zu den Typen, mit denen das Blob-Attribut verwendet werden kann, gehören 'Stream', 'TextReader', 'TextWriter' und 'CloudBlockBlob'. Der Attributkonstruktor verwendet einen blobPath-Parameter, der den Container und Blob-Namen angibt. Wenn das Attribut ein Stream-Objekt ergänzt, gibt ein anderer Konstruktorparameter den Modus 'FileAccess' als Lesen, Schreiben oder Lese-/Schreibzugriff an. Wenn Sie an Ihrer Funktion vor dem Binden eines Blobs an ein Objekt einige Änderungen vornehmen müssen, können Sie das Attribut im Text der Funktion verwenden, [wie zuvor für das Queue-Attribut beschrieben](#queuebody).

### Zeichenfolgen-Warteschlangennachrichten

Für eine Warteschlangennachricht, die eine Zeichenfolge enthält, ist "queueTrigger" ein Platzhalter, der im blobPath-Parameter des blob-Attributs verwendet werden kann, der den Inhalt der Nachricht enthält. 

Im folgenden Beispiel werden Stream-Objekte zum Lesen und Schreiben von Blobs verwendet. Die Warteschlangennachricht enthält den Namen eines Blobs, der sich im textblobs-Container befindet. Eine Kopie des Blobs, an deren Namen "-new" angefügt wird, wird im gleichen Container erstellt. 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

Im folgenden Beispiel wird ein CloudBlockBlob-Objekt zum Löschen eines Blobs verwendet.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### POCO-Objekt-Warteschlangennachrichten

Für POCO-Objekte, die als JSON in der Warteschlangenmeldung gespeichert werden, können Sie Platzhalter verwenden, die Eigenschaften des Objekts im blobPath-Parameter des Queue-Attributs benennen. Sie können auch [Eigenschaftennamen von Warteschlangenmetadaten](#queuemetadata) als Platzhalter verwenden. 

Im folgenden Beispiel wird ein Blob in ein neues Blob mit einer anderen Erweiterung kopiert, wobei die Eigenschaften des BlobInformation-Objekts verwendet werden, um die Namen der Eingabe- und Ausgabeblobs anzugeben. 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

Im folgenden Beispiel wird veranschaulicht, wie eine POCO-Warteschlangennachricht ohne Verwendung des WebJobs SDKs erstellt wird, die das SDK analysieren kann. Das SDK verwendet das [Newtonsoft.Json NuGet-Paket](http://www.nuget.org/packages/Newtonsoft.Json) zum Serialisieren und Deserialisieren von Meldungen.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

## <a id="poison"></a> Behandlung von nicht verarbeitbaren Nachrichten

Nachrichten, deren Inhalt bewirkt, dass bei einer Funktion ein Fehler auftritt, werden als *nicht verarbeitbare Nachrichten* bezeichnet. Wenn bei der Funktion ein Fehler auftritt, wird die Warteschlangennachricht nicht gelöscht, sondern schließlich erneut aufgegriffen, wodurch verursacht wird, dass der Zyklus wiederholt wird. Das SDK kann den Zyklus nach einer begrenzten Anzahl von Iterationen automatisch unterbrechen, oder Sie können dies manuell vornehmen.

### Automatische Behandlung von nicht verarbeitbaren Nachrichten

Das SDK ruft eine Funktion bis zu fünf Mal auf, um eine Warteschlangenachricht zu verarbeiten. Wenn der fünfte Versuch fehlschlägt, wird die Nachricht in eine Warteschlange für nicht verarbeitete Nachrichten verschoben. [Die maximale Anzahl von Wiederholungen ist konfigurierbar](#config). 

Die Warteschlange für nicht verarbeitete Nachrichten weist den Namen *{UrsprünglicherWarteschlangenname}*-poison auf. Sie können eine Funktion schreiben, um Nachrichten aus der Warteschlange für nicht verarbeitete Nachrichten zu verarbeiten, indem Sie diese protokollieren oder eine Benachrichtigung senden, dass ein manueller Eingriff erforderlich ist. 

Im folgenden Beispiel tritt bei der CopyBlob-Funktion ein Fehler auf, wenn eine Warteschlangennachricht den Namen eines Blobs enthält, der nicht vorhanden ist. In diesem Fall wird die Nachricht aus der Warteschlange "copyblobqueue" in die Warteschlange "copyblobqueue-poison" verschoben. Die 'ProcessPoisonMessage' protokolliert dann die nicht verarbeitbare Nachricht.

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

Sie können die Anzahl von Übernahmen einer Nachricht zur Verarbeitung abrufen, indem Sie einen int-Parameter mit dem Namen "deQueueCount" zu Ihrer Funktion hinzufügen. Anschließend können Sie die Häufigkeit des Entfernens aus der Warteschlange im Funktionscode überprüfen und Ihre eigene Behandlung von nicht verarbeitbaren Nachrichten ausführen, wenn die Anzahl einen Schwellenwert überschreitet, wie in dem folgendem Beispiel dargestellt.

		public static void CopyBlob(
		    [QueueTrigger("copyblobqueue")] string blobName, int dequeueCount,
		    [Blob("textblobs/{queueTrigger}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new", FileAccess.Write)] Stream blobOutput,
		    TextWriter logger)
		{
		    if (dequeueCount > 5)
		    {
		        logger.WriteLine("Failed to copy blob, name=" + blobName);
		    }
		    else
		    {
		    blobInput.CopyTo(blobOutput, 4096);
		    }
		}

Damit dieser Code erwartungsgemäß funktioniert, müssten Sie die maximale Anzahl von Wiederholungen für die automatische Behandlung nicht verarbeitbarer Nachrichten erhöhen, oder die Anzahl für das Entfernen aus der Warteschlange würde würde in diesem Beispiel nie größer als 5 werden.

## <a id="config"></a> Festlegen von Konfigurationsoptionen

Sie können den Typ 'JobHostConfiguration' verwenden, um die folgenden Konfigurationsoptionen festzulegen:

* Legen Sie die SDK-Verbindungszeichenfolgen im Code fest.
* Konfigurieren Sie QueueTrigger-Einstellungen, wie z. B. die maximale Anzahl für das Entfernen aus der Warteschlange.
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

### <a id="configqueue"></a>Konfigurieren von Warteschlangeneinstellungen

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
		    config.Queues.MaxPollingInterval = TimeSpan.FromMinutes(10);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueuenames"></a>Abrufen von Warteschlangennamen aus der Konfiguration

Mit dem JobHostConfiguration-Typ können Sie ein NameResolver-Objekt übergeben, das den Namen der Warteschlange an das SDK für die Attribute 'QueueTrigger' und 'Queue' bereitstellt.

Angenommen, Sie möchten eine Warteschlange mit dem Namen "logqueuetest" in der Testumgebung und eine Warteschlange mit dem Namen "logqueueprod" in einer Produktionsumgebung verwenden. Anstelle eines hartcodierten Warteschlangennamens möchten Sie den Namen eines Eintrags in der Auflistung "appSettings" angeben, die den tatsächlichen Namen der Warteschlange hätte. Wenn der appSettings-Schlüssel "logqueue" ist, könnte die Funktion wie im folgenden Beispiel aussehen.

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

Die NameResolver-Klasse könnte dann den Warteschlangennamen von "appSettings" abrufen, wie im folgenden Beispiel dargestellt:

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

Sie übergeben die NameResolver-Klasse im JobHost-Objekt, wie im folgenden Beispiel dargestellt.

		static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 

## <a id="manual"></a>Manuelles Auslösen einer Funktion

Um eine Funktion manuell auszulösen, verwenden Sie die Call- oder CallAsync-Methode im JobHost-Objekt und das NoAutomaticTrigger-Attribut für die Funktion, wie im folgenden Beispiel gezeigt. 

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

Um Protokolle zu schreiben, die auf der WebJobs-Dashboard-Seite angezeigt werden und mit einem bestimmten Funktionsaufruf verknüpft sind, verwenden Sie ein TextWriter-Objekt, das Sie aus einem Parameter in der Methodensignatur erhalten.

Zum Schreiben von [Ablaufverfolgungsprotokollen](../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview)verwenden Sie "Console.Out" (erstellt Protokolle, die als INFO gekennzeichnet sind) und "Console.Error" (erstellt Protokolle, die als ERROR markiert sind). Eine Alternative besteht darin, ["Trace" oder "TraceSource"](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx) zu verwenden.

Anwendungsprotokolle werden in den Website-Protokolldateien, in Azure-Tabellen oder in Azure-Blobs angezeigt, je nachdem, wie Sie Ihre Azure-Website konfigurieren. Die letzten 100 Anwendungsprotokolle werden auch im Dashboard angezeigt, wenn die Anwendung in einem Azure-WebJob ausgeführt wird. (von einem Programm, das lokal oder in einer anderen Umgebung ausgeführt wird, werden keine Anwendungsprotokolle im Dashboard angezeigt.)   

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

Im Dashboard des WebJobs SDKs wird die Ausgabe des TextWriter-Objekts angezeigt, wenn Sie zu der Seite für einen bestimmten Funktionsaufruf wechseln und auf **Toggle Output** klicken:

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Im Dashboard des WebJobs SDKs werden die letzten 100 Zeilen von Anwendungsprotokollen angezeigt, wenn Sie zu der Seite für den WebJob (nicht für den Funktionsaufruf) wechseln und auf **Toggle Output** klicken.
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

In einem fortlaufenden WebJob werden Anwendungsprotokolle in "/data/jobs/continuous/*{webjobname}*/job_log.txt" im Dateisystem der Website angezeigt.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

In einem Azure-Blob sieht das Anwendungsprotokoll folgendermaßen aus:
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Und in einer Azure-Tabelle sehen die Protokolle "Console.Out" und "Console.Error" wie folgt aus:

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a> Nächste Schritte

In diesem Thema wurden Codebeispiele bereitgestellt, in denen veranschaulicht wird, wie häufige Szenarios für das Arbeiten mit Azure-Warteschlangen behandelt werden. Weitere Informationen zur Verwendung von Azure WebJobs und WebJobs SDK, finden Sie unter [Empfohlene Ressourcen für Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).

<!--HONumber=35.2-->
