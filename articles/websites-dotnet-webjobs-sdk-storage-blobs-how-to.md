<properties 
	pageTitle="Verwenden von Azure-BLOB-Speicher mit dem Webaufträge-SDK" 
	description="Erfahren Sie, wie Sie Azure-BLOB-Speicher mit dem Webaufträge-SDK nutzen. Auslösen eines Prozesses, wenn ein neues BLOB in einem Container angezeigt wird, und Verarbeiten von  'poison blobs'." 
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

# Verwenden von Azure-BLOB-Speicher mit dem Webaufträge-SDK

Dieser Leitfaden enthält C#-Codebeispiele, die zeigen, wie Sie einen Prozess auslösen, wenn ein Azure-BLOB erstellt oder aktualisiert wird. Die Codebeispiele verwenden das [Webaufträge-SDK](../websites-dotnet-webjobs-sdk/) 1.x.

Codebeispiele, die zeigen, wie BLOBs erstellt werden , finden Sie unter [Verwenden von Azure-Warteschlangenspeicher mit dem Webaufträge-SDK](../websites-dotnet-webjobs-sdk-storage-queues-how-to/). .
		
Im Leitfaden wird davon ausgegangen, dass Sie wissen, [wie ein Webauftragsprojekt in Visual Studio mit Verbindungszeichenfolgen erstellt wird, die auf Ihr Speicherkonto zeigen](../websites-dotnet-webjobs-sdk-get-started/)..

## Inhaltsverzeichnis

-   [Auslösen einer Funktion, wenn ein BLOB erstellt oder aktualisiert wird](#trigger)
	- Einzelner Platzhalter für BLOB-Namen mit Erweiterung
	- Separate Platzhalter für BLOB-Namen und Erweiterung 
-   [Typen, mit denen BlobTrigger zusammenarbeitet](#types)
-   [Abrufen von BLOB-Textinhalt durch Bindung an eine Zeichenfolge](#string)
-   [Abrufen serialisierter BLOB-Inhalte mithilfe von ICloudBlobStreamBinder](#icbsb)
-   [Behandlung von nicht verarbeitbaren BLOBs](#poison)
-   [BLOB-Abrufalgorithmus](#polling)
-   [BLOB-Zugänge](#receipts)
-   [Verwandte in den Artikeln zu Warteschlangen behandelte Themen](#queues)
-   [Nächste Schritte](#nextsteps)

## <a id="trigger"></a>Auslösen einer Funktion, wenn ein BLOB erstellt oder aktualisiert wird

Dieser Abschnitt beschreibt die Verwendung des  `BlobTrigger`-Attributs. 

### Einzelner Platzhalter für BLOB-Namen mit Erweiterung  

Das folgende Codebeispiel kopiert Text-BLOBs im Container  *input* in den Container  *output*:

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("output/{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

Der Attributkonstruktor verwendet einen Zeichenfolgenparameter, der den Containernamen und einen Platzhalter für den BLOB-Namen angibt. Wenn in diesem Beispiel ein BLOB mit dem Namen  *Blob1.txt* im Container  *input* erstellt wird, erstellt die Funktion ein BLOB mit dem Namen  *Blob1.txt* im Container  *output*. 

Sie können mit dem BLOB-Namenplatzhalter ein Namensmuster angeben, wie im folgenden Codebeispiel gezeigt:

		public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
		    [Blob("output/copy-{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

Dieser Code kopiert nur BLOBs, deren Namen mit "original-" beginnt. Beispielsweise wird  *original-Blob1.txt* im Container  *input* in  *copy-Blob1.txt* im Container  *output* kopiert.

Wenn Sie ein Namensmuster für BLOB-Namen angeben müssen, die geschweifte Klammern enthalten, verdoppeln Sie die geschweiften Klammern. Wenn Sie z. B BLOBs im Container  *images* suchen, deren Namen wie folgt lauten:

		{20140101}-soundfile.mp3

verwenden Sie dies für das Muster:

		images/{{20140101}}-{name}

Bei diesem Beispiel ist  *soundfile.mp3* der Platzhalterwert  *name*. 

### Separate Platzhalter für BLOB-Namen und Erweiterung

Im folgenden Beispiel ändert sich die Dateierweiterung, wenn BLOBs die im Container  *input* in den Container  *output* kopiert werden. Der Code protokolliert die Erweiterung des  *input*-BLOBs und legt die Erweiterung des  *output*-BLOBs auf *.txt* fest.

		public static void CopyBlobToTxtFile([BlobTrigger("input/{name}.{ext}")] TextReader input,
		    [Blob("output/{name}.txt")] out string output,
		    string name,
		    string ext,
		    TextWriter logger)
		{
		    logger.WriteLine("Blob name:" + name);
		    logger.WriteLine("Blob extension:" + ext);
		    output = input.ReadToEnd();
		}

## <a id="types"></a>Typen, die Sie an BLOBs binden können

Sie können das  `BlobTrigger`-Attribut für die folgenden Typen verwenden:

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* Andere von [ICloudBlobStreamBinder] deserialisierte Typen(#icbsb) 

Wenn Sie direkt mit dem Azure-Speicherkonto arbeiten möchten, können Sie auch der Methodensignatur einen  `CloudStorageAccount`-Parameter hinzufügen.

## <a id="string"></a>Abrufen von BLOB-Textinhalt durch Bindung an eine Zeichenfolge

Wenn Text-BLOBs erwartet werden, kann  `BlobTrigger` auf einen  `string`-Parameter angewendet werden. Im folgenden Beispiel wird ein Text-BLOB an den  `string`-Parameter mit dem Namen `logMessage` gebunden. Die Funktion verwendet diesen Parameter, um den Inhalt des BLOBs in das Dashboard des Webaufträge-SDK zu schreiben. 
 
		public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
		    string name, 
		    TextWriter logger)
		{
		     logger.WriteLine("Blob name: {0}", name);
		     logger.WriteLine("Content:");
		     logger.WriteLine(logMessage);
		}

## <a id="icbsb"></a>Abrufen serialisierter BLOB-Inhalte mithilfe von ICloudBlobStreamBinder

Im folgenden Codebeispiel wird eine Klasse verwendet, die  `ICloudBlobStreamBinder` implementiert, um das  `BlobTrigger`-Attribut für das Binden eines BLOBs an den  `WebImage`-Typ zu aktivieren.

		public static void WaterMark(
		    [BlobTrigger("images3/{name}")] WebImage input,
		    [Blob("images3-watermarked/{name}")] out WebImage output)
		{
		    output = input.AddTextWatermark("WebJobs SDK", 
		        horizontalAlign: "Center", verticalAlign: "Middle",
		        fontSize: 48, opacity: 50);
		}
		public static void Resize(
		    [BlobTrigger("images3-watermarked/{name}")] WebImage input,
		    [Blob("images3-resized/{name}")] out WebImage output)
		{
		    var width = 180;
		    var height = Convert.ToInt32(input.Height * 180 / input.Width);
		    output = input.Resize(width, height);
		}

Der  `WebImage`-Bindungscode wird in einer  `WebImageBinder`-Klasse bereitgestellt, die von  `ICloudBlobStreamBinder` abgeleitet ist.

		public class WebImageBinder : ICloudBlobStreamBinder<WebImage>
		{
		    public Task<WebImage> ReadFromStreamAsync(Stream input, 
		        System.Threading.CancellationToken cancellationToken)
		    {
		        return Task.FromResult<WebImage>(new WebImage(input));
		    }
		    public Task WriteToStreamAsync(WebImage value, Stream output,
		        System.Threading.CancellationToken cancellationToken)
		    {
		        var bytes = value.GetBytes();
		        return output.WriteAsync(bytes, 0, bytes.Length, cancellationToken);
		    }
		}

## <a id="poison"></a>Behandlung von nicht verarbeitbaren BLOBs

Wenn eine  `BlobTrigger`-Funktion misslingt, wird sie vom SDK für den Fall erneut aufgerufen, das der Fehler durch einen vorübergehenden Fehler verursacht wurde. Wenn der Inhalt des BLOBs den Fehler verursacht hat, missling die Funktion bei jedem Versuch, das BLOB zu verarbeiten. Standardmäßig ruft das SDK eine Funktion bis zu 5 Mal für ein angegebenes BLOB auf. Wenn der fünfte Versuch missling, fügt das SDK eine Nachricht einer Warteschlange mit dem Namen  *webjobs-blobtrigger-poison* hinzu.

Die maximale Anzahl von Wiederholungen ist konfigurierbar. Für die Verarbeitung nicht verarbeitbarer BLOBs und der dazugehörigen Nachrichtenwarteschlange wird die gleiche [MaxDequeueCount](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#configqueue)-Einstellung verwendet. 

Die Warteschlangennachricht für nicht verarbeitbare BLOBs ist ein JSON-Objekt, das die folgenden Eigenschaften enthält:

* FunctionId (im Format *{WebJob name}*.Functions.*{Function name}*. Beispiel: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" or "PageBlob")
* ContainerName
* BlobName
* ETag (eine BLOB-Versions-ID. Beispiel: "0x8D1DC6E70A277EF")

Im folgenden Codebeispiel enthält die  `CopyBlob`-Funktion Code, der bewirkt, dass sie bei jedem Aufruf misslingt. Nachdem Sie das SDK die Funktion mit der maximalen Anzahl von Wiederholungen aufgerufen hat, wird eine Nachricht in der Warteschlange für nicht verarbeitbare BLOBs erstellt. Diese Nachricht wird vom der  `LogPoisonBlob`-Funktion verarbeitet. 

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("textblobs/output-{name}")] out string output)
		{
		    throw new Exception("Exception for testing poison blob handling");
		    output = input.ReadToEnd();
		}
		
		public static void LogPoisonBlob(
		[QueueTrigger("webjobs-blobtrigger-poison")] PoisonBlobMessage message,
		    TextWriter logger)
		{
		    logger.WriteLine("FunctionId: {0}", message.FunctionId);
		    logger.WriteLine("BlobType: {0}", message.BlobType);
		    logger.WriteLine("ContainerName: {0}", message.ContainerName);
		    logger.WriteLine("BlobName: {0}", message.BlobName);
		    logger.WriteLine("ETag: {0}", message.ETag);
		}

Das SDK deserialisiert die JSON-Nachricht automatisch. Hier ist die  `PoisonBlobMessage`-Klasse: 

		public class PoisonBlobMessage
		{
		    public string FunctionId { get; set; }
		    public string BlobType { get; set; }
		    public string ContainerName { get; set; }
		    public string BlobName { get; set; }
		    public string ETag { get; set; }
		}

### <a id="polling"></a>BLOB-Abrufalgorithmus

Das WebaufträgeSDK durchsucht beim Starten der Anwendung alle vom  `BlobTrigger`-Attribut angegebenen Container. Bei einem großen Speicherkonto kann diese Überprüfung etwas Zeit benötigen, sodass es eine Weile dauern kann, bevor neue BLOBs gefunden und  `BlobTrigger`-Funktionen ausgeführt werden.

Um neue oder geänderte BLOBs nach dem Start der Anwendung zu erkennen, liest das SDK in regelmäßigen Abständen die BLOB-Speicherprotokolle. Die BLOB-Protokolle werden gepuffert und nur ca. alle 10 Minuten physisch geschrieben. Daher kann, nachdem ein BLOB erstellt oder aktualisiert wurde,  möglicherweise eine erhebliche Verzögerung eintreten, ehe die entsprechende  `BlobTrigger`-Funktion ausgeführt wird. 

Es ist eine Ausnahme für BLOBs, die Sie mit dem `Blob`-Attribut erstellen können. Wenn das Webaufträge-SDK ein neues BLOB erstellt, übergibt es das neue BLOB sofort an alle übereinstimmenden  `BlobTrigger`-Funktionen. Wenn Sie also eine Kette von BLOB-Ein- und Ausgaben haben, kann das SDK sie effizient verarbeiten. Aber wenn Sie für die Ausführung Ihrer BLOB-Verarbeitungsfunktionen eine geringe Latenz für BLOBs wünschen, die anderweitig erstellt oder aktualisiert werden, sollten Sie  `QueueTrigger` statt  `BlobTrigger` verwenden.

### <a id="receipts"></a>BLOB-Zugänge

Das Webaufträge-SDK stellt sicher, dass keine  `BlobTrigger` -Funktion mehr als einmal für dasselbe neue oder aktualisierte BLOB aufgerufen wird. Dies geschieht durch das Verwalten von  *blob receipts*, um zu bestimmen, ob eine angegebene BLOB-Version verarbeitet wurde.

BLOB-Zugänge werden in einem Container mit dem Namen  *azure-webjobs-hosts* im Azure-Speicherkonto gespeichert, der durch die Verbindungszeichenfolge "AzureWebJobsStorage" angegeben wird. Ein BLOB-Zugang hat die folgende Informationen:

* Die Funktion, die für das BLOB aufgerufen wurde ("*{WebJob name}*.Functions.*{Function name}*". Beispiel: "WebJob1.Functions.CopyBlob")
* Der Containername
* Der BLOB-Typ ("BlockBlob" oder "PageBlob")
* Der BLOB-Name
* ETag (eine BLOB-Versions-ID. Beispiel: "0x8D1DC6E70A277EF")

Wenn Sie eine erneute Verarbeitung eines BLOBs erzwingen möchten, können Sie den BLOB-Zugang für diesen Blob manuell aus dem Container  *azure-webjobs-hosts* löschen.

## <a id="queues"></a>Verwandte in den Artikeln zu Warteschlangen behandelte Themen

Informationen zur Handhabung der BLOB-Verarbeitung, die durch eine Warteschlangennachricht ausgelöst wird, oder zu Szenarien für das Webaufträge-SDK, die nicht spezifisch für die BLOB-Verarbeitung sind, finden Sie unter [Verwenden von Azure-Warteschlangenspeicher mit dem Webaufträge-SDK](../websites-dotnet-webjobs-sdk-storage-queues-how-to/). .

In diesem Artikel werden u. a. die folgenden Themen behandelt:

* Asynchrone Funktionen
* Mehrere Instanzen
* Ordnungsgemäßes Herunterfahren
* Verwenden von Webaufträge-SDK-Attributen im Hauptteil einer Funktion
* Festlegen der SDK-Verbindungszeichenfolgen im Code
* Festlegen von Werten für Konstruktorparameter im Webaufträge-SDK im Code
* Konfigurieren von  `MaxDequeueCount` für die Behandlung nicht verarbeitbarer BLOBs
* Manuelles Auslösen einer Funktion
* Schreiben von Protokollen

## <a id="nextsteps"></a>Nächste Schritte

In dieser Anleitung wurden Codebeispiele bereitgestellt, in denen veranschaulicht wird, wie häufige Szenarien für das Arbeiten mit Azure-BLOBs behandelt werden. Weitere Informationen zur Verwendung von Azure-Webaufträgen und dem Webaufträge-SDK finden Sie unter [Empfohlene Ressourcen für Azure-Webaufträge](http://go.microsoft.com/fwlink/?linkid=390226).




<!--HONumber=42-->
