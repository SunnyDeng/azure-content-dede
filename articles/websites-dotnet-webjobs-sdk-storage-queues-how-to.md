<properties linkid="" pageTitle="Arbeiten mit Azure-Warteschlangenspeicher unter Verwendung des Webauftrags-SDK" metaKeywords="WebJobs SDK, Azure-Warteschlangenspeicher, .NET, C#" description="Learn how to work with Azure queue storage in the WebJobs SDK. Create and delete queues; insert, peek, get, and delete queue messages, and more." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="web-sites,storage" documentationCenter=".NET" title="How to work with Azure queue storage using the WebJobs SDK" authors="tdykstra" manager="wpickett" editor="jimbe" />

<tags ms.service="web-sites" ms.workload="web" ms.tgt_pltfrm="na" ms.devlang="dotnet" ms.topic="article" ms.date="10/13/2014" ms.author="tdykstra" />

# Arbeiten mit Azure-Warteschlangenspeicher unter Verwendung des Webauftrags-SDK

In diesem Leitfaden erfahren Sie, wie Sie C#-Code für allgemeine Szenarien unter Verwendung des
Azure-Warteschlangen-Speicherdiensts und des Azure WebJobs-SDK, Version 1.0.0, schreiben können.

In diesem Leitfaden wird vorausgesetzt, dass Sie bereits[mit dem Webjobs-SDK ](../websites-webjobs-sdk-storage-queues-how-to) und [dem Durchführen einfacher Aufgaben](../websites-dotnet-webjobs-sdk-get-started/) vertraut sind, zum Beispiel Installieren des WebJobs-SDK-NuGet-Pakets, Erstellen von Azure-Speicherkonten und Erstellen von Verbindungszeichenfolgen für das WebJobs-SDK, die auf Ihr Speicherkonto verweisen.

Die meisten Codeausschnitte zeigen nur Funktionen, nicht den Code, der das Objekt "JobHost" wie in diesem Beispiel erstellt:

static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## Inhaltsverzeichnis

-   [Auslösen einer Funktion, wenn eine Warteschlangennachricht empfangen wird](#trigger)
	- Warteschlangennachrichten mit Zeichenfolge
	- Warteschlangennachrichten für POCO-Objekt
	- Async-Funktion
	- Abrufalgorithmus
	- Parallele Ausführung
	- Abrufen der Warteschlangen-der Warteschlangennchrichtenmetadaten
	- Ordnungsgemäßes Herunterfahren
-   [Erstellen einer Warteschlangennachricht beim Verarbeiten einer Warteschlangennachricht](#createqueue)
	- Warteschlangennachrichten mit Zeichenfolge
	- Warteschlangennachrichten für POCO-Objekt
	- Erstellen mehrerer Nachrichten
	- Verwenden der Warteschlangenattribute im Hauptteil einer Funktion
-   [Gewusst wie: Lesen und Bearbeiten von Blobs beim Verarbeiten einer Warteschlangennachricht](#blobs)
	- Warteschlangennachrichten mit Zeichenfolge
	- Warteschlangennachrichten für POCO-Objekt
	- Verwenden von Blob-Attribute im Hauptteil einer Funktion
-   [Behandeln von beschädigten Nachrichten](#poison)
	- Automatische Handhabung von beschädigten Nachrichten
	- Manuelle Handhabung von beschädigten Nachrichten
-   [Gewusst wie: Festlegen von Konfigurationsoptionen](#config)
	- Festlegen von SDK-Verbindungszeichenfolgen im Code
	- Konfigurieren von Einstellungen für QueueTrigger
	- Abrufen von Wartschlangennamen aus der Konfiguration
-   [Manuelles Auslösen einer Funktion](#manual)
-   [Gewusst wie: Schreiben von Protokollen](#logs)
-   [Nächste Schritte](#nextsteps)

## <a id="trigger"></a> Auslösen einer Funktion, wenn eine Warteschlangennachricht empfangen wird

Um eine Funktion zu schreiben, die das SDK aufruft, wenn eine Warteschlangennachricht empfangen wird, verwenden Sie das Attribut 'QueueTrigger' mit einer Zeichenfolge oder die POCO-Parameter, je nachdem, was Sie vom Abrufen der Nachricht erwarten. Der Attributkonstruktor hat einen Zeichenfolgenparameter, der den Namen der abzurufenden Warteschlange angibt. Sie können auch den [Namen der Warteschlange dynamisch festlegen](#config).

Falls Ihre Website auf mehreren VMs läuft, wird der WebJob auf allen Computern ausgeführt, und jeder Computer wartet auf Auslöser und versucht, Funktionen auszuführen. In manchen Szenarien kann dies dazu führen, dass manche Funktionen dieselben Daten doppelt verarbeiten. Die Funktionen sollten daher "idempotent" geschrieben sein, d. h. beim wiederholten Aufrufen mit denselben Eingangsdaten dürfen keine duplizierten Resultate entstehen.  

### Warteschlangennachrichten mit Zeichenfolge

Im folgenden Beispiel enthält die Warteschlange eine Zeichenfolgenmeldung, damit "QueueTrigger" auf einen Parameter mit dem Namen "LogMessage" angewendet wird, der den Inhalt der Warteschlangennachricht enthält. Die Funktion [schreibt eine Protokollmeldung an das Dashboard](#logs).
 

		public static void ProcessQueueMessage([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    logger.WriteLine(logMessage);
		}

Neben "String" kann der Parameter Folgendes sein: ein Byte-Array, ein "CloudQueueMessage"-Objekt oder ein POCO-Objekt, das Sie definieren.

### Warteschlangennachrichten für POCO-Objekt

Im folgenden Beispiel enthält die Warteschlangenmeldung JSON für ein "BlobInformation"-Objekt, das eine Eigenschaft "BlobName" enthält. Das SDK deserialisiert automatisch das Objekt.

		public static void WriteLogPOCO([QueueTrigger("logqueue")] BlobInformation blobInfo, TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Im folgenden Beispiel wird veranschaulicht, wie Sie eine POCO-Warteschlangenmeldung ohne das WebJobs-SDK erstellen, die das SDK analysieren kann. Das SDK verwendet das [Newtonsoft.Json-NuGet-Paket](http://www.nuget.org/packages/Newtonsoft.Json) zum Serialisieren und Deserialisieren von Meldungen.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "log.txt" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

### Async-Funktion

Die folgende async-Funktion [schreibt ein Protokoll an das Dashboard](#logs).

		public async static Task ProcessQueueMessageAsync([QueueTrigger("logqueue")] string logMessage, TextWriter logger)
		{
		    await logger.WriteLineAsync(logMessage);
		}

Async-Funktion können ein Abbruchtoken in Anspruch nehmen, wie im folgenden Beispiel dargestellt, das einen Blob kopiert. (Eine Erläuterung des Platzhalters "QueueTrigger" finden Sie im Abschnitt [Blobs](#blobs)).

		public async static Task ProcessQueueMessageAsyncCancellationToken(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput,
		    CancellationToken token)
		{
		    await blobInput.CopyToAsync(blobOutput, 4096, token);
		}

### Abrufalgorithmus

Das SDK implementiert einen zufälligen exponentiellen Backoff-Algorithmus, um die Auswirkungen im Leerlauf des Warteschlangenabrufs auf die Speichertransaktionskosten zu reduzieren.  Wenn eine Nachricht gefunden wird, wartet das SDK zwei Sekunden und prüft dann, ob eine andere Meldung vorhanden ist. Wenn keine Nachricht gefunden wird, wartet es ungefähr vier Sekunden vor dem erneuten Versuch. Nach aufeinander folgenden fehlgeschlagenen Versuchen, eine Warteschlangennachricht abzurufen, erhöht sich die Wartezeit immer mehr, bis die maximale Wartezeit, standardmäßig eine Minute, erreicht ist. [Die maximale Wartezeit ist konfigurierbar](#config).

### Parallele Ausführung

Wenn mehrere Funktionen verschiedene Warteschlangen überwachen, ruft das SDK sie parallel auf, wenn Nachrichten gleichzeitig empfangen werden. 

Das gilt auch, wenn mehrere Nachrichten für eine einzelne Warteschlange empfangen werden. Standardmäßig ruft das SDK einen Batch von 16 Warteschlangennachrichten gleichzeitig ab und führt die Funktion, die sie verarbeitet, parallel aus. [Die Batchgröße ist konfigurierbar](#config). Wenn schon die Hälfte der Batchgröße verarbeitet wurde, ruft das SDK ruft einen weiteren Batch auf und startet die Verarbeitung dieser Nachrichten. Aus diesem Grund ist die maximale Anzahl der pro Funktion verarbeiteten Nachrichten anderthalb Mal die Batchgröße. Dieser Grenzwert gilt separat für jede Funktion, die über ein "QueueTrigger"-Attribut verfügt. Wenn Sie die parallele Ausführung für Nachrichten in einer Warteschlange nicht durchführen möchten, können Sie die Batchgröße auf 1 festlegen.

### <a id="queuemetadata"></a>Abrufen der Warteschlangen-der Warteschlangennchrichtenmetadaten

Die folgenden Nachrichteneigenschaften erhalten Sie durch Hinzufügen von Parametern zur Methodensignatur:

* 'DateTimeOffset' expirationTime
* 'DateTimeOffset' insertionTime
* 'DateTimeOffset' nextVisibleTime
* 'string' queueTrigger (contains message text)
* 'string' id
* 'string' popReceipt
* 'int' dequeueCount

Wenn Sie direkt mit der Azure-Speicher-API arbeiten möchten, können Sie auch einen "CloudStorageAccount"-Parameter hinzufügen.

Im folgenden Beispiel werden alle diese Metadaten in ein INFO-Anwendungsprotokoll geschrieben. Im Beispiel enthalten LogMessage und QueueTrigger den Inhalt der Nachricht der Warteschlange.

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
		        "logMessage={0}\n"
				nexpirationTime={1}\ninsertionTime={2}\n" +
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

Hier ist ein Beispielprotokoll, das durch den Beispielcode geschrieben wird:

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

Eine Funktion, die in einem fortlaufenden WebJob ausgeführt wird kann einen "CancellationToken"-Parameter annehmen, der es dem Betriebssystem ermöglicht, die Funktion zu benachrichtigen, bevor der WebJob beendet wird. Sie können diese Benachrichtigung verwenden, um sicherzustellen, dass die Funktion nicht auf eine Weise unerwartet beendet wird, die Daten in einem inkonsistenten Zustand belässt.

Das folgende Beispiel zeigt, wie Sie auf die bevorstehende WebJob-Beendigung in einer Funktion prüfen.

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

**Hinweis:** Das Dashboard zeigt möglicherweise nicht korrekt den Status und die Ausgabe der Funktionen an, die beendet wurden.
 
Weitere Informationen finden Sie unter [WebJobs Graceful Shutdown](http://blog.amitapple.com/post/2014/05/webjobs-graceful-shutdown/#.VCt1GXl0wpR) (Ordnungsgemäßes Herunterfahren bei WebJobs, in englischer Sprache).   

## <a id="createqueue"></a> Erstellen einer Warteschlangennachricht beim Verarbeiten einer Warteschlangennachricht

Um eine Funktion schreiben, die eine neue Warteschlangennachricht erstellt, verwenden Sie das Attribut "Queue" bei einer Ausgabezeichenfolge oder einem POCO-Parameter. Wie "QueueTrigger" übergeben Sie den Warteschlangennamen als Zeichenfolge oder Sie können den [Namen der Warteschlange dynamisch festlegen](#config).

### Warteschlangennachrichten mit Zeichenfolge

Das folgende Beispiel erstellt eine neue Warteschlangennachricht in der Warteschlange mit dem Namen "Outputqueue" mit dem gleichen Inhalt wie die Warteschlangenmeldung in der Warteschlange mit dem Namen "Inputqueue".

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] out string outputQueueMessage )
		{
		    outputQueueMessage = queueMessage;
		}

Wenn der Ausgabeparametertyp einer der folgenden Typen und das Objekt nicht null ist, wenn die Funktion beendet wird, erstellt das SDK eine Warteschlangennachricht:

* 'string' 
* 'byte[]'
* Ein serialisierbarer POCO-Typ, den Sie definieren.
* 'CloudQueueMessage'

Zum Erstellen mehrerer Nachrichten finden Sie Informationen unter **Erstellen mehrerer Nachrichten** weiter unten in diesem Abschnitt.

Wenn Sie Nachrichten manuell senden möchten, können Sie auch "CloudQueue" als Output-Parametertyp verwenden.

### Warteschlangennachrichten für POCO-Objekt

Übergeben Sie zum Erstellen einer Warteschlangennachricht, die ein POCO-Objekt anstelle einer Zeichenfolge enthält, den POCO-Typ als Ausgabeparameter an den Konstruktor des "Queue"-Attributs. 

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] BlobInformation blobInfoInput,
		    [Queue("outputqueue")] out BlobInformation blobInfoOutput )
		{
		    blobInfoOutput = blobInfoInput;
		}

Das SDK serialisiert automatisch das Objekt in JSON. Eine Warteschlangen-Nachricht wird immer erstellt, selbst wenn das Objekt null ist.

### Erstellen mehrerer Nachrichten

Um mehrere Nachrichten zu erstellen, wählen Sie den Parametertyp '"ICollector<T>" oder "IAsyncCollector" für die Ausgabewarteschlange, wie im folgenden Beispiel gezeigt.

		public static void CreateQueueMessages(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    [Queue("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Alle Warteschlangennachrichten werden sofort erstellt, wenn die Methode "Add" aufgerufen wird.

### <a id="queuebody"></a>Verwenden der Warteschlangenattribute im Hauptteil einer Funktion

Wenn Sie Ihre Funktion vor der Verwendung des "Queue"-Attributs zum Erstellen einer Warteschlangennachricht ändern müssen, können Sie die"IBinder"-Schnittstelle verwenden, um ein "CloudQueue"-Objekt abzurufen, das Ihnen ermöglicht, direkt mit einer Warteschlange zu arbeiten. 

Im folgende Beispiel wird anhand einet Nachricht für die Eingabewarteschlange eine neue Nachricht mit dem gleichen Inhalt in einer Ausgabewarteschlange erstellt. Der Ausgabewarteschlangenname wird durch Code im Text der Funktion festgelegt.

		public static void CreateQueueMessage(
		    [QueueTrigger("inputqueue")] string queueMessage,
		    IBinder binder)
		{
		    string outputQueueName = "outputqueue" + DateTime.Now.Month.ToString();
		    QueueAttribute queueAttribute = new QueueAttribute(outputQueueName);
		    CloudQueue outputQueue = binder.Bind<CloudQueue>(queueAttribute);
		    outputQueue.AddMessage(new CloudQueueMessage(queueMessage));
		}

## <a id="blobs"></a> Lesen und Bearbeiten von Blobs beim Verarbeiten einer Warteschlangennachricht

Die Attribute "Blob" und "Table" ermöglichen Ihnen das Lesen und Schreiben von Blobs und Tabellen. Die Beispiele in diesem Abschnitt gelten für Blobs.

Zu den Typen, mit denen das Attribut "Blob" verwendet werden kann gehören "Stream", "TextReader", "TextWriter" und "CloudBlockBlob". Der Attributkonstruktor verwendet einen "BlobPath"-Parameter, der den Container- und Blob-Namen angibt. Wenn das Attribut ein "Stream"-Objekt ergänzt, gibt ein anderer Konstruktorparameter den Modus "FileAccess" als Lesezugriff, Schreibzugriff oder Lese-/Schreibzugriff an. Wenn Sie Ihre Funktion vor dem Binden eines Blobs an ein Objekt ändern müssen, können Sie das Attribut im Hauptteil der Funktion verwenden, [wie oben beschrieben für das "Queue"-Attribut beschrieben](#queuebody).

### Warteschlangennachrichten mit Zeichenfolge

Für eine Warteschlangennachricht, die eine Zeichenfolge enthält, ist "QueueTrigger" ein Platzhalter, der im "BlobPath"-Parameter des "Blob"-Attributs verwendet werden kann, der den Inhalt der Nachricht enthält. 

Im folgenden Beispiel werden "Stream"-Objekte zum Lesen und Schreiben von Blobs verwendet. Die Warteschlangennachricht enthält den Namen eines Blobs im Container "Textblobs". Eine Kopie des Blobs mit "-new" am Ende des Namens wird im selben Container erstellt. 

		public static void ProcessQueueMessage(
		    [QueueTrigger("blobcopyqueue")] string blobName, 
		    [Blob("textblobs/{queueTrigger}",FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{queueTrigger}-new",FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

Im folgenden Beispiel wird ein "CloudBlockBlob"-Objekt verwendet, um einen Blob zu löschen.

		public static void DeleteBlob(
		    [QueueTrigger("deleteblobqueue")] string blobName,
		    [Blob("textblobs/{queueTrigger}")] CloudBlockBlob blobToDelete)
		{
		    blobToDelete.Delete();
		}

### Warteschlangennachrichten für POCO-Objekt

Für POCO-Objekte, die als JSON in der Warteschlangenmeldung gespeichert werden, können Sie Platzhalter verwenden, die Eigenschaften des "BlobPath"-Parameters des "Queue"-Attributs nennen. Sie können auch [Metadateneigenschaftennamen der Warteschlange als Platzhalter](#queuemetadata) verwenden. 

Das folgende Beispiel kopiert einen Blob in einen neuen Blob mit einer anderen Erweiterung, mithilfe der Eigenschaften des Objekts "BlobInformation", um die Namen der Eingabe- und Ausgabe-Blobs anzugeben. 
 
		public static void CopyBlobPOCO(
		    [QueueTrigger("copyblobqueue")] BlobInformation blobInfo,
		    [Blob("textblobs/{BlobName}", FileAccess.Read)] Stream blobInput,
		    [Blob("textblobs/{BlobNameWithoutExtension}.txt", FileAccess.Write)] Stream blobOutput)
		{
		    blobInput.CopyTo(blobOutput, 4096);
		}

Im folgenden Beispiel wird veranschaulicht, wie Sie eine POCO-Warteschlangenmeldung ohne das WebJobs-SDK erstellen, die das SDK analysieren kann. Das SDK verwendet das [Newtonsoft.Json-NuGet-Paket](http://www.nuget.org/packages/Newtonsoft.Json) zum Serialisieren und Deserialisieren von Meldungen.

		BlobInformation blobInfo = new BlobInformation() { BlobName = "boot.log", BlobNameWithoutExtension = "boot" };
		var queueMessage = new CloudQueueMessage(JsonConvert.SerializeObject(blobInfo));
		logQueue.AddMessage(queueMessage);

## <a id="poison"></a> Behandeln von beschädigten Nachrichten

Nachrichten, deren Inhalt bewirkt, dass eine Funktion nicht ausgeführt werden kann, heißen *beschädigte Nachrichten*. Wenn die Funktion fehlschlägt, wird die Warteschlangennachricht nicht gelöscht und schließlich erneut abgeholt, was verursacht, dass der Zyklus wiederholt wird. Das SDK kann automatisch den Zyklus nach einer begrenzten Anzahl von Wiederholungen unterbrechen oder Sie können dies manuell vornehmen.

### Automatische Handhabung von beschädigten Nachrichten

Das SDK ruft eine Funktion bis zu fünf Mal auf, um eine Warteschlangennachricht zu verarbeiten. Wenn der fünfte Versuch fehlschlägt, wird die Nachricht an eine Warteschlange mit beschädigten Nachrichten verschoben. [Die maximale Anzahl möglicher Versuche ist konfigurierbar](#config). 

Die beschädigte Warteschlange erhält den Namen*{originalqueuename}*-poison. Sie können eine Funktion so schreiben, dass Nachrichten aus der Warteschlange für beschädigte Nachrichten verarbeitet werden, indem sie protokolliert werden, oder durch das Senden einer Benachrichtigung, dass manuelles Eingreifen erforderlich ist. 

Im folgende Beispiel schlägt die "CopyBlob"-Funktion fehl, wenn eine Warteschlangennachricht den Namen eines Blobs enthält, der nicht vorhanden ist. In diesem Fall wird die Nachricht aus der Warteschlange "copyblobqueue" in die Warteschlange "copyblobqueue-poison" verschoben. "ProcessPoisonMessage" protokolliert dann die beschädigte Nachricht.

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

Die folgende Abbildung zeigt die Konsolenausgabe dieser Funktionen, wenn eine beschädigte Nachricht verarbeitet wird.

![Console output for poison message handling](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/poison.png)

### Manuelle Handhabung von beschädigten Nachrichten

Sie erhalten die Anzahl der Male, die eine Nachricht für die Verarbeitung übernommen wurde, indem Sie einen "Int"-Parameter mit dem Namen "dequeueCount" zu Ihrer Funktion hinzufügen. Anschließend können Sie "dequeue count" im Code überprüfen und Ihre eigene Behandlung beschädigter Nachrichten durchführen, beim Überschreiten eines Schwellenwerts der Anzahl, wie im folgenden Beispiel gezeigt.

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

Damit dieser Code erwartungsgemäß funktioniert, müssen Sie die maximale Anzahl von Wiederholungen für automatische Behandlung beschädigter Nachrichten erhöhen oder die Häufigkeit wird in diesem Beispiel nie größer als 5 sein.

## <a id="config"></a> Gewusst wie: Festlegen von Konfigurationsoptionen

Den Typ "JobHostConfiguration" können Sie verwenden, um die folgenden Konfigurationsoptionen festzulegen:

* Festlegen der SDK-Verbindungszeichenfolgen im Code.
* Konfigurieren von "QueueTrigger"-Einstellungen wie z. B. die maximale Anzahl der Entfernungen aus der Warteschlange.
* Abrufen von Namen aus der Konfiguration.

### <a id="setconnstr"></a>Festlegen von SDK-Verbindungszeichenfolgen im Code

Das Festlegen der SDK-Verbindungszeichenfolgen im Code ermöglicht es Ihnen, eigene Namen für Verbindungseichenfolgen in Konfigurationsdateien oder Umgebungsvariablen zu verwenden, wie im folgenden Beispiel gezeigt.

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

Sie können die folgenden Einstellungen konfigurieren, die für die Verarbeitung der Warteschlangennachrichten gelten:

- Die maximale Anzahl von Warteschlangennachrichten, die gleichzeitig abgerufen werden, um parallel ausgeführt zu werden (der Standardwert ist 16).
- Die maximale Anzahl von Wiederholungen, bevor eine Warteschlangennachricht an eine Warteschlange mit beschädigten Nachrichten gesendet wird (der Standardwert ist 5).
- Die maximale Wartezeit bis zum erneuten Abrufen, wenn eine Warteschlange leer ist (der Standardwert ist 1 Minute).

Das folgende Beispiel zeigt, wie Sie diese Einstellungen konfigurieren:

static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.Queues.BatchSize = 8;
		    config.Queues.MaxDequeueCount = 4;
		    config.Queues.MaxPollingInterval = TimeSpan.FromMinutes(10);
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}

### <a id="configqueuenames"></a>Abrufen von Wartschlangennamen aus der Konfiguration

Mit dem Typ "JobHostConfiguration" können Sie ein "NameResolver"-Objekt übergeben, das den Namen der Warteschlange im SDK für die Attribute "QueueTrigger" und "Queue" bereitstellt.

Angenommen, Sie möchten eine Warteschlange mit dem Namen "logqueuetest" in der Testumgebung und eine namens "logqueueprod" in einer Produktionsumgebung verwenden. Anstatt eines hartcodierten Warteschlangennamens möchten Sie den Namen eines Eintrags in der Auflistung "appSettings" angeben, der den tatsächlichen Namen der Warteschlange hätte. Wenn der "appSettings"-Schlüssel "logqueue" ist, könnte die Funktion wie im folgenden Beispiel aussehen.

		public static void WriteLog([QueueTrigger("%logqueue%")] string logMessage)
		{
		    Console.WriteLine(logMessage);
		}

Die Klasse "NameResolver" kann damm den Namen der Warteschlange aus "appSettings" abrufen, wie im folgenden Beispiel gezeigt:

		public class QueueNameResolver : INameResolver
		{
		    public string Resolve(string name)
		    {
		        return ConfigurationManager.AppSettings[name].ToString();
		    }
		}

Sie übergeben die Klasse "NameResolver" an das "JobHost"-Objekt, wie im folgenden Beispiel gezeigt.

static void Main(string[] args)
		{
		    JobHostConfiguration config = new JobHostConfiguration();
		    config.NameResolver = new QueueNameResolver();
		    JobHost host = new JobHost(config);
		    host.RunAndBlock();
		}
 

## <a id="manual"></a>Manuelles Auslösen einer Funktion

Um eine Funktion manuell auszulösen, verwenden Sie Methode "Call" oder "CallAsync" für das Objekt "JobHost" und das "NoAutomaticTrigger"-Attribut für die Funktion, wie im folgenden Beispiel gezeigt. 

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

Verwenden Sie zum Schreiben von Protokollen, die in der WebJobs-Dashboard-Seite mit einer bestimmten Funktion verknüpft sind, ein "TextWriter"-Objekt, das Sie aus einem Parameter in der Methodensignatur erhalten.

Verwenden Sie zum Schreiben von [Ablaufverfolgungsprotokollen für die Anwendung](../web-sites-dotnet-troubleshoot-visual-studio/#logsoverview) "Console.Out"(erstellt als INFO markierte Protokolle) und "Console.Error" (erstellt als ERROR markiert Protokolle). Eine Alternative ist die Verwendung der [Trace oder TraceSource](http://blogs.msdn.com/b/mcsuksoldev/archive/2014/09/04/adding-trace-to-azure-web-sites-and-web-jobs.aspx).

Anwendungsprotokolle werden in den Website-Protokolldateien, Azure-Tabellen oder Azure-Blobs angezeigt, abhängig von der Konfiguration der Azure-Website. Die letzten 100 Anwendungsprotokolle werden auch im Dashboard angezeigt, wenn die Anwendung in einem Azure-WebJob ausgeführt wird. (Keine Anwendungsprotokolle werden im Dashboard aus einem Programm angezeigt, das lokal oder in einer anderen Umgebung ausgeführt wird.)   

Sie können die Protokollierung deaktivieren, indem Sie [die Dashboard-Verbindungszeichenfolge auf null festlegen](#config).

Im folgende Beispiel werden mehrere Möglichkeiten veranschaulicht, Protokolle zu schreiben:

		public static void WriteLog(
		    [QueueTrigger("logqueue")] string logMessage,
		    TextWriter logger)
		{
		    Console.WriteLine("Console.Write - " + logMessage);
		    Console.Out.WriteLine("Console.Out - " + logMessage);
		    Console.Error.WriteLine("Console.Error - " + logMessage);
		    logger.WriteLine("TextWriter - " + logMessage);
		}

Im WebJobs-SDK-Dashboard wird die Ausgabe aus dem Objekt "TextWriter" angezeigt, wenn Sie die Seite für einen bestimmten Funktionsaufruf anzeigen und auf **Toggle Output** klicken:

![Click function invocation link](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardinvocations.png)

![Logs in function invocation page](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardlogs.png)

Im WebJobs SDK-Dashboard werden die letzten 100 Zeilen der Anwendungsprotokolle angezeigt, wenn Sie die Seite für den WebJob (nicht für den Aufruf der Funktion) aufrufen und auf **Toggle Output** klicken.
 
![Click Toggle Output](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/dashboardapplogs.png)

In einem fortlaufenden WebJob werden Anwendungsprotokolle in /data/jobs/continuous/*{webjobname}*/job_log.txt im Dateisystem der Website angezeigt.

		[09/26/2014 21:01:13 > 491e54: INFO] Console.Write - Hello world!
		[09/26/2014 21:01:13 > 491e54: ERR ] Console.Error - Hello world!
		[09/26/2014 21:01:13 > 491e54: INFO] Console.Out - Hello world!

In einem Azure-Blob sehen die Anwendungsprotokolle wie folgt aus:
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738373502,0,17404,17,Console.Write - Hello world!,
		2014-09-26T21:01:13,Error,contosoadsnew,491e54,635473620738373502,0,17404,19,Console.Error - Hello world!,
		2014-09-26T21:01:13,Information,contosoadsnew,491e54,635473620738529920,0,17404,17,Console.Out - Hello world!,

Und in einer Azure-Tabelle sehen "Console.Out"- und "Console.Error"-Protokolle wie folgt aus:

![Info log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableinfo.png)

![Error log in table](./media/websites-dotnet-webjobs-sdk-storage-queues-how-to/tableerror.png)

## <a id="nextsteps"></a> Nächste Schritte

In diesem Thema wurden Codebeispiele bereitgestellt, die veranschaulichen, wie Sie häufige Szenarios für die Arbeit mit Azure-Warteschlangen behandeln können. Weitere Informationen zu Azure-WebJobs und zum WebJobs SDK finden Sie unter [Azure WebJobs - Empfohlene Ressourcen](http://go.microsoft.com/fwlink/?linkid=390226).
