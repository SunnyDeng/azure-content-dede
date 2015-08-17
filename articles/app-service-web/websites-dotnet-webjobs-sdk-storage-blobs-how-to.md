<properties 
	pageTitle="Verwenden von Azure-Blobspeicher mit dem WebJobs-SDK" 
	description="Erfahren Sie, wie Sie Azure-Blobspeicher mit dem WebJobs-SDK nutzen. Lösen Sie einen Prozess aus, wenn einem Container ein neues Blobs hinzugefügt wird, und informieren Sie sich über den Umgang mit nicht verarbeitbaren Blobs." 
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


# Verwenden von Azure-Blobspeicher mit dem WebJobs-SDK

## Übersicht

Dieser Leitfaden enthält C#-Codebeispiele, die zeigen, wie Sie einen Prozess auslösen, wenn ein Azure-Blob erstellt oder aktualisiert wird. In den Codebeispielen wird Version 1.x des [WebJobs-SDK](websites-dotnet-webjobs-sdk.md) verwendet.

Codebeispiele, die zeigen, wie Blobs erstellt werden, finden Sie unter [Verwenden von Azure-Warteschlangenspeicher mit dem WebJobs-SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md).
		
Im Handbuch wird davon ausgegangen, dass Sie wissen, [wie ein Webauftrags-Projekt in Visual Studio mit Verbindungszeichenfolgen erstellt wird, die auf Ihr Speicherkonto verweisen](websites-dotnet-webjobs-sdk-get-started.md).

## <a id="trigger"></a> Auslösen einer Funktion beim Erstellen oder Aktualisieren eines Blobs

Dieser Abschnitt beschreibt die Verwendung des `BlobTrigger`-Attributs.

> **Hinweis:** Das WebJobs-SDK durchsucht Protokolldateien nach neuen oder geänderten Blobs. Dieser Vorgang ist naturgemäß recht langsam. Eine Funktion wird unter Umständen erst mehrere Minuten nach der Bloberstellung (oder noch später) ausgelöst. Wenn Ihre Anwendung Blobs sofort verarbeiten muss, empfiehlt es sich, zusammen mit dem Blob eine Warteschlangennachricht zu erstellen, und für die Funktion, die das Blob verarbeitet, anstelle des `BlobTrigger`-Attributs das [QueueTrigger](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#trigger)-Attribut zu verwenden.

### Einzelner Platzhalter für Blobnamen mit Erweiterung  

Das folgende Codebeispiel kopiert Text-Blobs aus dem Container *input* in den Container *output*:

		public static void CopyBlob([BlobTrigger("input/{name}")] TextReader input,
		    [Blob("output/{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

Der Attributkonstruktor verwendet einen Zeichenfolgenparameter, der den Containernamen und einen Platzhalter für den Blobnamen angibt. Wenn in diesem Beispiel im Container *input* ein Blob mit dem Namen *Blob1.txt* erstellt wird, erstellt die Funktion ein Blob mit dem Namen *Blob1.txt* im Container *output*.

Sie können mit dem Platzhalter für Blobnamen ein Namensmuster angeben, wie im folgenden Codebeispiel gezeigt:

		public static void CopyBlob([BlobTrigger("input/original-{name}")] TextReader input,
		    [Blob("output/copy-{name}")] out string output)
		{
		    output = input.ReadToEnd();
		}

Dieser Code kopiert nur Blob, deren Namen mit "original-" beginnt. So wird beispielsweise *original-Blob1.txt* aus dem Container *input* als *copy-Blob1.txt* in den Container *output* kopiert.

Wenn Sie ein Namensmuster für Blobnamen angeben müssen, die geschweifte Klammern enthalten, verdoppeln Sie die geschweiften Klammern. Wenn Sie beispielsweise Blob im Container *images* suchen, deren Namen wie folgt lauten:

		{20140101}-soundfile.mp3

verwenden Sie dies für das Muster:

		images/{{20140101}}-{name}

In diesem Beispiel lautet der Platzhalterwert *name* wie folgt: *soundfile.mp3*.

### Separate Platzhalter für Blobnamen und Erweiterung

Im folgenden Beispiel ändert sich die Dateierweiterung, wenn Blob aus dem Container *input* in den Container *output* kopiert werden. Der Code protokolliert die Erweiterung des Blobs vom Typ *input* und legt die Erweiterung des Blobs vom Typ *output* auf *.txt* fest.

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

## <a id="types"></a> Typen, die Sie an Blobs binden können

Sie können das `BlobTrigger`-Attribut für die folgenden Typen verwenden:

* `string`
* `TextReader`
* `Stream`
* `ICloudBlob`
* `CloudBlockBlob`
* `CloudPageBlob`
* Andere von [ICloudBlobStreamBinder](#icbsb) deserialisierte Typen 

Wenn Sie direkt mit dem Azure-Speicherkonto arbeiten möchten, können Sie der Methodensignatur auch einen `CloudStorageAccount`-Parameter hinzufügen.

## <a id="string"></a> Abrufen von Blobtextinhalt durch Bindung an eine Zeichenfolge

Wenn Textblobs erwartet werden, kann `BlobTrigger` auf einen `string`-Parameter angewendet werden. Im folgenden Beispiel wird ein Textblob an einen `string`-Parameter mit dem Namen `logMessage` gebunden. Die Funktion verwendet diesen Parameter, um den Inhalt des Blobs in das Dashboard des WebJobs-SDK zu schreiben.
 
		public static void WriteLog([BlobTrigger("input/{name}")] string logMessage,
		    string name, 
		    TextWriter logger)
		{
		     logger.WriteLine("Blob name: {0}", name);
		     logger.WriteLine("Content:");
		     logger.WriteLine(logMessage);
		}

## <a id="icbsb"></a> Abrufen serialisierter Blobinhalte mithilfe von ICloudBlobStreamBinder

Im folgenden Codebeispiel wird eine Klasse verwendet, die `ICloudBlobStreamBinder` implementiert, um das `BlobTrigger`-Attribut für das Binden eines Blobs an den `WebImage`-Typ zu aktivieren.

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

Der `WebImage`-Bindungscode wird in einer `WebImageBinder`-Klasse bereitgestellt, die von `ICloudBlobStreamBinder` abgeleitet ist.

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

## <a id="poison"></a> Behandlung von nicht verarbeitbaren Blobs

Wenn bei einer `BlobTrigger`-Funktion ein Fehler auftritt, wird sie für den Fall, dass es sich um einen vorübergehenden Fehler handelt, erneut aufgerufen. Wenn der Inhalt des Blobs den Fehler verursacht hat, misslingt die Funktion bei jedem Versuch, das Blob zu verarbeiten. Standardmäßig ruft das SDK eine Funktion bis zu 5 Mal für ein angegebenes Blob auf. Ist auch der fünfte Versuch nicht erfolgreich, fügt das SDK einer Warteschlange namens *webjobs-blobtrigger-poison* eine Nachricht hinzu.

Die maximale Anzahl von Wiederholungen ist konfigurierbar. Für die Verarbeitung nicht verarbeitbarer Blobs und der dazugehörigen Nachrichtenwarteschlange wird die gleiche [MaxDequeueCount](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#configqueue)-Einstellung verwendet.

Die Warteschlangennachricht für nicht verarbeitbare Blobs ist ein JSON-Objekt, das die folgenden Eigenschaften enthält:

* FunctionId (im Format *{WebJob-Name}*.Functions.*{Funktionsname}*; Beispiel: WebJob1.Functions.CopyBlob)
* BlobType ("BlockBlob" oder "PageBlob")
* ContainerName
* BlobName
* ETag (eine Blobversions-ID. Beispiel: 0x8D1DC6E70A277EF)

Im folgenden Codebeispiel enthält die `CopyBlob`-Funktion Code, der bewirkt, dass bei jedem Aufruf ein Fehler auftritt. Nachdem das SDK die Funktion mit der maximalen Anzahl von Wiederholungen aufgerufen hat, wird in der Warteschlange für nicht verarbeitbare Blobs eine Nachricht erstellt. Diese Nachricht wird von der `LogPoisonBlob`-Funktion verarbeitet.

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

Das SDK deserialisiert die JSON-Nachricht automatisch. Hier sehen Sie die `PoisonBlobMessage`-Klasse:

		public class PoisonBlobMessage
		{
		    public string FunctionId { get; set; }
		    public string BlobType { get; set; }
		    public string ContainerName { get; set; }
		    public string BlobName { get; set; }
		    public string ETag { get; set; }
		}

### <a id="polling"></a> Blobabrufalgorithmus

Das WebJobs-SDK durchsucht beim Start der Anwendung alle vom `BlobTrigger`-Attribut angegebenen Container. Bei einem großen Speicherkonto kann diese Überprüfung durchaus länger dauern. Daher kann etwas Zeit vergehen, bis neue Blobs gefunden und `BlobTrigger`-Funktionen ausgeführt werden.

Um neue oder geänderte Blobs nach dem Start der Anwendung zu erkennen, liest das SDK in regelmäßigen Abständen die Blobspeicherprotokolle. Die Blobprotokolle werden gepuffert und nur etwa alle 10 Minuten physisch geschrieben. Daher kann, nachdem ein Blob erstellt oder aktualisiert wurde, möglicherweise eine erhebliche Verzögerung eintreten, ehe die entsprechende `BlobTrigger`-Funktion ausgeführt wird.

Für Blobs, die Sie mit dem `Blob`-Attribut erstellen, gibt es eine Ausnahme. Wenn das WebJobs-SDK ein neues Blob erstellt, übergibt es dieses sofort an alle entsprechenden `BlobTrigger`-Funktionen. Wenn Sie also eine Kette von Ein- und Ausgaben von Blobs haben, kann das SDK sie effizient verarbeiten. Wenn es Ihnen bei Ihren Blobverarbeitungsfunktionen für anderweitig erstellte oder aktualisierte Blobs allerdings auf eine geringe Latenz ankommt, empfiehlt sich die Verwendung von `QueueTrigger` anstelle von `BlobTrigger`.

### <a id="receipts"></a> Blobbelege

Das WebJobs-SDK stellt sicher, dass `BlobTrigger`-Funktionen für ein neues oder aktualisiertes Blob nicht mehrmals aufgerufen werden. Zu diesem Zweck wird mittels Verwaltung der *Blobbelege* bestimmt, ob eine bestimmte Blobversion verarbeitet wurde.

Blobbelege werden in einem Container mit dem Namen *azure-webjobs-hosts* in dem Azure-Speicherkonto gespeichert, das in der Verbindungszeichenfolge "AzureWebJobsStorage" angegeben ist. Ein Blobbeleg enthält die folgenden Informationen:

* Die Funktion, die für das Blob aufgerufen wurde ("*{WebJob-Name}*.Functions.*{Funktionsname}*"; Beispiel: "WebJob1.Functions.CopyBlob")
* Der Containername
* Blobtyp ("BlockBlob" oder "PageBlob")
* Blobname
* ETag (eine Blobversions-ID. Beispiel: 0x8D1DC6E70A277EF)

Wenn Sie eine erneute Verarbeitung eines Blobs erzwingen möchten, können Sie den Blobbeleg für dieses Blob manuell aus dem Container *azure-webjobs-hosts* löschen.

## <a id="queues"></a>Verwandte Themen aus dem Artikel zu Warteschlangen

Informationen zur Handhabung der Blobverarbeitung, die durch eine Warteschlangennachricht ausgelöst wird, oder zu Szenarien für das WebJobs-SDK, die sich nicht speziell auf die Blobverarbeitung beziehen, finden Sie unter [Verwenden von Azure-Warteschlangenspeicher mit dem WebJobs-SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

In diesem Artikel werden u. a. die folgenden Themen behandelt:

* Asynchrone Funktionen
* Mehrere Instanzen
* Ordnungsgemäßes Herunterfahren
* Verwenden von WebJobs-SDK-Attributen im Hauptteil einer Funktion
* Festlegen der SDK-Verbindungszeichenfolgen im Code
* Festlegen von Werten für WebJobs SDK-Konstruktorparametern im Code
* Konfigurieren von `MaxDequeueCount` für die Behandlung nicht verarbeitbarer Blobs
* Manuelles Auslösen einer Funktion
* Schreiben von Protokollen

## <a id="nextsteps"></a> Nächste Schritte

In dieser Anleitung wurden Codebeispiele bereitgestellt, in denen veranschaulicht wird, wie häufige Szenarien für das Arbeiten mit Azure-Blobs behandelt werden. Weitere Informationen zur Verwendung von Azure WebJobs und dem WebJobs-SDK finden Sie unter [Empfohlene Ressourcen für Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=August15_HO6-->