<properties 
	pageTitle="Verwenden von Azure Service Bus mit dem Webaufträge-SDK" 
	description="Erfahren Sie mehr über die Verwendung von Azure Service Bus-Warteschlangen und -Themen mit dem Webauftrags-SDK." 
	services="app-service\web, service-bus" 
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

# Verwenden von Azure Service Bus mit dem Webaufträge-SDK

## Übersicht

Dieser Leitfaden enthält C#-Codebeispiele, die zeigen, wie Sie einen Prozess auslösen, wenn ein Azure-BLOB erstellt oder aktualisiert wird. Der Code-Beispiele verwenden [WebJobs SDK](websites-dotnet-webjobs-sdk.md) Version 1.x.

Das Handbuch wird davon ausgegangen, Sie wissen [wie ein WebJob-Projekt in Visual Studio mit Verbindung erstellt Zeichenfolgen, die auf Ihr Speicherkonto](websites-dotnet-webjobs-sdk-get-started.md).

Codeausschnitte anzeigen nur Funktionen, die nicht auf den Code, erstellt das `JobHost` Objekt wie im folgenden Beispiel:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## <a id="prerequisites"></a> Voraussetzungen

Service Bus arbeiten, Sie installieren müssen, die [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) NuGet-Paket zusätzlich zu den anderen WebJobs SDK-Paketen.

Sie müssen außerdem zusätzlich zu den Speicherverbindungszeichenfolgen die Verbindungszeichenfolge "AzureWebJobsServiceBus" festlegen. Hierzu können Sie der `connectionStrings` Abschnitt der Web.config-Datei, wie im folgenden Beispiel gezeigt:

		<connectionStrings>
		    <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
		    <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
		    <add name="AzureWebJobsServiceBus" connectionString="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[yourKey]"/>
		</connectionStrings>

Ein Beispielprojekt, finden Sie unter [Service Bus-Beispiel](https://github.com/Azure/azure-webjobs-sdk-samples/tree/master/BasicSamples/ServiceBus). Weitere Informationen finden Sie unter [Erste Schritte mit dem WebJobs SDK](websites-dotnet-webjobs-sdk-get-started.md).

## <a id="trigger"></a> Eine Funktion auslösen, wenn eine Service Bus-Warteschlangen-Nachricht empfangen wird

Um eine Funktion zu schreiben, die das WebJobs SDK aufruft, wenn eine warteschlangennachricht empfangen wird, verwenden Sie die `ServiceBusTrigger` Attribut. Der Attributkonstruktor verwendet einen Parameter, der den Namen der abzufragenden Warteschlange angibt.

### Funktionsweise von "ServicebusTrigger"

Das SDK empfängt eine Nachricht im `PeekLock` Modus und ruft `Complete` für die Meldung, wenn die Funktion erfolgreich abgeschlossen wurde oder die Aufrufe `Abandon` wenn die Funktion fehlschlägt. Wenn die Funktion länger ausgeführt als wird die `PeekLock` Timeout der Sperre wird automatisch erneuert.

Service Bus verwaltet eine eigene Warteschlange für nicht verarbeitete Nachrichten. Deshalb ist weder eine Steuerung noch eine Konfiguration über das Webaufträge-SDK möglich.

### Zeichenfolgen-Warteschlangennachrichten

Das folgende Codebeispiel liest eine Warteschlangennachricht, die eine Zeichenfolge enthält, und schreibt diese in das Webaufträge-SDK-Dashboard.

		public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
		    TextWriter logger)
		{
		    logger.WriteLine(message);
		}

**Hinweis:** wenn Sie die Warteschlangennachrichten in einer Anwendung, die das WebJobs SDK verwendet erstellen, stellen Sie sicher, dass [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) auf "Text/Plain".

### POCO-Warteschlangennachricht

Deserialisiert das SDK automatisch eine warteschlangennachricht mit JSON für ein POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) Typ. Das folgende Codebeispiel liest eine warteschlangennachricht, enthält ein `BlobInformation` Objekt mit einer `BlobName` Eigenschaft:

		public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
		    TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Codebeispiele veranschaulichen die POCO-Eigenschaften zum Arbeiten mit Blobs und Tabellen in derselben Funktion zu verwenden, finden Sie unter der [Speicher-Warteschlangen-Version dieses Artikels](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#pocoblobs).

### Für ServiceBusTrigger verwendbare Typen

Neben `string` und POCO-Typen können Sie die `ServiceBusTrigger` Attribut mit einem Bytearray oder ein `BrokeredMessage` Objekt.

## <a id="create"></a> Vorgehensweise: Erstellen von Service Bus-Warteschlangennachrichten

Eine Funktion schreiben, die eine neue Warteschlange Nachricht Verwendung erstellt die `ServiceBus` Attribut, und übergeben Sie den Namen der Warteschlange an den Konstruktor des Attributs.


### Erstellen einer einzelnen Warteschlangennachricht in einer nicht asynchronen Funktion

Im folgenden Codebeispiel wird ein Ausgabeparameter zum Erstellen einer neuen Nachricht in der Warteschlange "outputqueue" verwendet. Diese hat den gleichen Inhalt wie die Nachricht, die in der Warteschlange "inputqueue" empfangen wird.

		public static void CreateQueueMessage(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] out string outputQueueMessage)
		{
		    outputQueueMessage = queueMessage;
		}

Der Ausgabeparameter zum Erstellen einer einzelnen Warteschlangennachricht kann einen der folgenden Typen verwenden:

* `string`
* `byte[]`
* `BrokeredMessage`
* Ein serialisierbarer POCO-Typ, den Sie definieren. Wird automatisch als JSON serialisiert.

Für Parameter vom POCO-Typ wird bei Beendigung der Funktion immer eine Warteschlangennachricht erstellt. Wenn der Parameter "null" ist, erstellt das SDK eine Warteschlangennachricht, die "null" zurückgibt, wenn die Nachricht empfangen und deserialisiert wird. Bei anderen Typen wird keine Warteschlangennachricht erstellt, wenn der Parameter "null" ist.

### Erstellen von mehreren Warteschlangennachrichten in asynchronen Funktionen

Um mehrere Nachrichten zu erstellen, verwenden Sie das `ServiceBus` -Attribut mit `ICollector<T>` oder `IAsyncCollector<T>`, wie im folgenden Codebeispiel gezeigt:

		public static void CreateQueueMessages(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Jede warteschlangennachricht sofort erstellt bei der `Add` wird aufgerufen.

## <a id="topics"></a>Gewusst wie: Arbeiten mit Service Bus-Themen

Um eine Funktion, die das SDK aufruft, wenn eine Nachricht, zu einem Service Bus-Thema empfangen wird zu schreiben, verwenden Sie die `ServiceBusTrigger` Attribut mit dem Konstruktor, dem Namen des Themas und Abonnementnamen, wie im folgenden Codebeispiel gezeigt:

		public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
		    TextWriter logger)
		{
		    logger.WriteLine("Topic message: " + message);
		}

Um eine Nachricht zu einem bestimmten Thema zu erstellen, verwenden Sie die `ServiceBus` Attribut mit einem Themanamen die gleiche Weise auch mit einem Warteschlangennamen verwendet werden.

## <a id="queues"></a>Verwandte Themen behandelt, die vom Speicher-Warteschlangen Gewusst-wie-Artikel

Informationen zu WebJobs SDK-Szenarien, die nicht spezifisch für Service Bus finden Sie unter [Verwendung von Azure-Warteschlangenspeicher mit dem WebJobs SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

In diesem Artikel werden u. a. die folgenden Themen behandelt:

* Asynchrone Funktionen
* Mehrere Instanzen
* Ordnungsgemäßes Herunterfahren
* Verwenden von Webaufträge-SDK-Attributen im Hauptteil einer Funktion
* Festlegen der SDK-Verbindungszeichenfolgen im Code
* Festlegen von Werten für WebJobs SDK-Konstruktorparametern im Code
* Manuelles Auslösen einer Funktion
* Schreiben von Protokollen

## <a id="nextsteps"></a> Nächste Schritte

Dieses Handbuch wurde Codebeispiele bereitgestellt, die veranschaulichen, wie häufige Szenarios für die Arbeit mit Azure Service Bus zu behandeln. Weitere Informationen zur Verwendung von Azure-WebJobs und das WebJobs-SDK finden Sie unter [Azure WebJobs empfohlene Ressourcen](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=GIT-SubDir_Tue_AM_dede-->