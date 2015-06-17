<properties 
	pageTitle="Verwenden von Azure-Blob-Speicher mit dem Webaufträge-SDK" 
	description="Erfahren Sie, wie Sie Azure-BLOB-Speicher mit dem Webaufträge-SDK nutzen. Lösen Sie einen Prozess aus, wenn ein neues Blob in einem Container angezeigt wird, und behandeln Sie &#39;nicht verarbeitbare Blobs&#39;." 
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
	ms.date="06/08/2015" 
	ms.author="tdykstra"/>

# Verwenden von Azure-Blob-Speicher mit dem Webaufträge-SDK

## Übersicht

Dieser Leitfaden enthält C#-Codebeispiele, die zeigen, wie Sie einen Prozess auslösen, wenn ein Azure-BLOB erstellt oder aktualisiert wird. Der Code-Beispiele verwenden [WebJobs SDK](websites-dotnet-webjobs-sdk.md) Version 1.x.

Codebeispiele, die zeigen, wie Blobs erstellt, finden Sie unter [Verwendung von Azure-Warteschlangenspeicher mit dem WebJobs SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md).
		
Das Handbuch wird davon ausgegangen, Sie wissen [wie ein WebJob-Projekt in Visual Studio mit Verbindung erstellt Zeichenfolgen, die auf Ihr Speicherkonto](websites-dotnet-webjobs-sdk-get-started.md).

## <a id="trigger"></a> Eine Funktion auslösen, wenn ein Blob erstellt oder aktualisiert wird

In diesem Abschnitt veranschaulicht, wie die `BlobTrigger` Attribut.

> **Hinweis:** das WebJobs SDK scannt die Protokolldateien, um neue oder geänderte Blobs zu überwachen. Dieser Vorgang ist naturgemäß sehr; eine Funktion kann nicht erst mehrere Minuten oder länger ausgelöst, nachdem das Blob erstellt wird. Wenn Ihre Anwendung Blobs sofort verarbeiten muss, ist die empfohlene Methode eine warteschlangennachricht zu erstellen, wenn Sie das Blob erstellen und Verwenden der [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger) -Attribut statt der `BlobTrigger` Attribut für die Funktion zur Verarbeitung von Blob.

### Einzelner Platzhalter für BLOB-Namen mit Erweiterung  

Das folgende Codebeispiel kopiert Text-Blobs, die in angezeigt werden die *input* Container der *Ausgabe* Container:

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("output/{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

Der Attributkonstruktor verwendet einen Zeichenfolgenparameter, der den Containernamen und einen Platzhalter für den BLOB-Namen angibt. In diesem Beispiel, wenn ein Blob mit dem Namen *Blob1.txt* wird erstellt, der *input* Container, die Funktion erstellt ein Blob mit dem Namen *Blob1.txt* in den *Ausgabe* Container.

Sie können mit dem BLOB-Namenplatzhalter ein Namensmuster angeben, wie im folgenden Codebeispiel gezeigt:

		public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
		    [Blob("output/copy-{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

Dieser Code kopiert nur BLOBs, deren Namen mit "original-" beginnt. Beispielsweise *Original-Blob1.txt* in den *input* Container kopiert *Kopie Blob1.txt* in die *Ausgabe* Container.

Wenn Sie ein Namensmuster für BLOB-Namen angeben müssen, die geschweifte Klammern enthalten, verdoppeln Sie die geschweiften Klammern. Z. B. zum Suchen von Blobs in der *Bilder* Container, deren Namen wie folgt:

		{20140101}-soundfile.mp3

verwenden Sie dies für das Muster:

		images/{{20140101}}-{name}

Im Beispiel ist die *Name* Platzhalterwert wäre *soundfile.mp3*.

### Separate Platzhalter für BLOB-Namen und Erweiterung

Im folgenden Beispiel ändert die Dateierweiterung kopiert Blobs, die in angezeigt werden die *input* Container der *Ausgabe* Container. Der Code protokolliert die Erweiterung der der *input* blob und legt die Erweiterung der der *Ausgabe* auf BLOB- *.txt*.

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

## <a id="types"></a> Typen, mit denen Sie eine an Blobs Bindung

Sie können die `BlobTrigger` Attribut für die folgenden Typen:

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* Andere Typen von deserialisiert [ICloudBlobStreamBinder](#icbsb) 

Wenn Sie direkt mit der Azure-Speicherkonto arbeiten möchten, können Sie auch Hinzufügen einer `CloudStorageAccount` Parameter der Methodensignatur.

## <a id="string"></a> Abrufen von Blob-Textinhalt durch Bindung in eine Zeichenfolge

Wenn Text Blobs erwartet werden, `BlobTrigger` angewendet werden können, um eine `string` Parameter. Im folgenden Beispiel bindet einen Text-Blob, das eine `string` Parameter mit dem Namen `logMessage`. Die Funktion verwendet diesen Parameter, um den Inhalt des BLOBs in das Dashboard des Webaufträge-SDK zu schreiben.
 
		public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
		    string name, 
		    TextWriter logger)
		{
		     logger.WriteLine("Blob name: {0}", name);
		     logger.WriteLine("Content:");
		     logger.WriteLine(logMessage);
		}

## <a id="icbsb"></a> Abrufen von BLOB-Inhalt mithilfe von ICloudBlobStreamBinder serialisiert

Im folgenden Codebeispiel wird eine Klasse, die implementiert `ICloudBlobStreamBinder` Aktivieren der `BlobTrigger` Attribut ein Blob zum Binden der `WebImage` Typ.

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

Die `WebImage` Binden von Code finden Sie im eine `WebImageBinder` von abgeleitete Klasse `ICloudBlobStreamBinder`.

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

## <a id="poison"></a> Behandeln von nicht verarbeitbaren blobs

Wenn eine `BlobTrigger` Funktion fehlschlägt, SDK wird auch für den Fall, dass der Fehler durch einen vorübergehenden Fehler verursacht wurde. Wenn der Inhalt des BLOBs den Fehler verursacht hat, missling die Funktion bei jedem Versuch, das BLOB zu verarbeiten. Standardmäßig ruft das SDK eine Funktion bis zu 5 Mal für ein angegebenes BLOB auf. Wenn die fünfte, ein Fehler auftritt versuchen, fügt das SDK eine Nachricht an eine Warteschlange mit dem Namen *Webjobs-Blobtrigger-Poison*.

Die maximale Anzahl von Wiederholungen ist konfigurierbar. Die gleiche [MaxDequeueCount](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue) Einstellung für beschädigte Blob Handhabung und Behandlung der Warteschlange mit unzustellbaren Nachrichten verwendet wird.

Die Warteschlangennachricht für nicht verarbeitbare BLOBs ist ein JSON-Objekt, das die folgenden Eigenschaften enthält:

* FunctionId (im Format *{WebJob-Name}*. Functions.* {Funktionsname} *, z. B.: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" or "PageBlob")
* ContainerName
* BlobName
* ETag (einem BLOB-Versions-ID, z. B.: "0x8D1DC6E70A277EF")

Im folgenden Codebeispiel die `CopyBlob` Funktion enthält Code, der bewirkt, dass es nicht jedes Mal, wenn sie aufgerufen wird. Nachdem Sie das SDK für die maximale Anzahl von Wiederholungen wird aufgerufen, eine Nachricht in der Warteschlange für unzustellbare Blob erstellt und diese Nachricht wird vom die `LogPoisonBlob` Funktion.

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

Das SDK deserialisiert die JSON-Nachricht automatisch. Hier wird die `PoisonBlobMessage` Klasse:

		public class PoisonBlobMessage
		{
		    public string FunctionId { get; set; }
		    public string BlobType { get; set; }
		    public string ContainerName { get; set; }
		    public string BlobName { get; set; }
		    public string ETag { get; set; }
		}

### <a id="polling"></a> BLOB-Abruf-Algorithmus

Das WebJobs SDK scannt alle Container, die vom angegebenen `BlobTrigger` Attribute beim Starten der Anwendung. In einem großen Speicherkonto diese Überprüfung etwas dauern kann, damit es eine Weile sein kann, bevor neue Blobs befinden und `BlobTrigger` Funktionen ausgeführt werden.

Um neue oder geänderte BLOBs nach dem Start der Anwendung zu erkennen, liest das SDK in regelmäßigen Abständen die BLOB-Speicherprotokolle. Die Blob-Protokolle werden gepuffert und nur alle 10 Minuten physisch geschrieben oder daher also möglicherweise erhebliche Verzögerung eintreten, nachdem ein Blob erstellt oder, bevor das entsprechende aktualisiert `BlobTrigger` Funktion ausführt.

Es ist eine Ausnahme für Blobs, die Sie, indem Sie mithilfe Erstellen der `Blob` Attribut. Wenn das WebJobs SDK ein neues Blob erstellt wird, übergibt er das neue Blob sofort an alle übereinstimmenden `BlobTrigger` Funktionen. Wenn Sie also eine Kette von BLOB-Ein- und Ausgaben haben, kann das SDK sie effizient verarbeiten. Aber wenn niedrigen Latenz unter Ihrem Blob Verarbeitungsfunktionen für Blobs, die erstellt oder anderweitig aktualisiert werden sollen, empfiehlt sich `QueueTrigger` statt `BlobTrigger`.

### <a id="receipts"></a> BLOB-Zugänge

Das WebJobs SDK stellt sicher, dass keine `BlobTrigger` -Funktion ruft für dieses neue oder aktualisierte Blob mehr als einmal aufgerufen. Dies geschieht durch das Verwalten von *blob-Empfangsbestätigungen* um zu bestimmen, ob eine angegebenes Blob-Version verarbeitet wurde.

BLOB-Empfangsbestätigungen befinden sich in einem Container mit dem Namen *Azure-Webjobs-Hosts* in den Azure-Speicher-Konto durch die Verbindungszeichenfolge AzureWebJobsStorage angegeben. Ein BLOB-Zugang hat die folgende Informationen:

* Die Funktion, die für das Blob aufgerufen wurde ("* {WebJob-Name} *. Functions.* {Funktionsname} * ", z. B.:"WebJob1.Functions.CopyBlob")
* Der Containername
* Der BLOB-Typ ("BlockBlob" oder "PageBlob")
* Der BLOB-Name
* Das ETag (einem BLOB-Versions-ID, z. B.: "0x8D1DC6E70A277EF")

Wenn Sie eine erneute Verarbeitung erfordern ein Blob zu erzwingen möchten, können Sie manuell löschen, auf die Blob-Empfangsbestätigung für das Blob aus dem *Azure-Webjobs-Hosts* Container.

## <a id="queues"></a>Verwandte Themen durch den Warteschlangen-Artikel

Informationen zur Handhabung der Blob-Verarbeitung durch eine warteschlangennachricht ausgelöst oder für WebJobs SDK Szenarien, die nicht spezifisch für die blob-Verarbeitung finden Sie in [Verwendung von Azure-Warteschlangenspeicher mit dem WebJobs SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

In diesem Artikel werden u. a. die folgenden Themen behandelt:

* Asynchrone Funktionen
* Mehrere Instanzen
* Ordnungsgemäßes Herunterfahren
* Verwenden von Webaufträge-SDK-Attributen im Hauptteil einer Funktion
* Festlegen der SDK-Verbindungszeichenfolgen im Code
* Festlegen von Werten für WebJobs SDK-Konstruktorparametern im Code
* Konfigurieren Sie `MaxDequeueCount` zum Behandeln von nicht verarbeitbaren Blob.
* Manuelles Auslösen einer Funktion
* Schreiben von Protokollen

## <a id="nextsteps"></a> Nächste Schritte

In dieser Anleitung wurden Codebeispiele bereitgestellt, in denen veranschaulicht wird, wie häufige Szenarien für das Arbeiten mit Azure-BLOBs behandelt werden. Weitere Informationen zur Verwendung von Azure-WebJobs und das WebJobs-SDK finden Sie unter [Azure WebJobs empfohlene Ressourcen](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->