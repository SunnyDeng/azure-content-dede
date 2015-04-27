<properties 
	pageTitle="Verwenden von Azure Service Bus mit dem Webauftrags-SDK" 
	description="Erfahren Sie mehr über die Verwendung von Azure Service Bus-Warteschlangen und -Themen mit dem Webauftrags-SDK." 
	services="web-sites, service-bus" 
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

# Verwenden von Azure Service Bus mit dem Webauftrags-SDK

## Übersicht

Dieser Leitfaden enthält C#-Codebeispiele, die zeigen, wie Sie einen Prozess auslösen, wenn ein Azure-BLOB erstellt oder aktualisiert wird. In den Codebeispielen wird Version 1.x des [Webauftrags-SDK](websites-dotnet-webjobs-sdk.md) verwendet.

Im Handbuch wird davon ausgegangen, dass Sie wissen, [wie ein Webauftrags-Projekt in Visual Studio mit Verbindungszeichenfolgen erstellt wird, die auf Ihr Speicherkonto zeigen](websites-dotnet-webjobs-sdk-get-started.md).

Die Codeausschnitte zeigen nur Funktionen, nicht den Code, der das Objekt `JobHost` erstellt Objekt, wie das folgende Beispiel zeigt:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## <a id="prerequisites"></a> Voraussetzungen

Um mit Service Bus zu arbeiten, müssen Sie das NuGet-Paket [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) zusätzlich zu den anderen Webauftrags-SDK-Paketen installieren. 

Außerdem müssen Sie zusätzlich zu den Speicherverbindungszeichenfolgen die Verbindungszeichenfolge "AzureWebJobsServiceBus" festlegen.

## <a id="trigger"></a> Auslösen einer Funktion, wenn eine Service Bus-Warteschlangennachricht empfangen wird

Um eine Funktion zu schreiben, die das Webauftrags-SDK aufruft, wenn eine Warteschlangennachricht empfangen wird, verwenden Sie das Attribut `ServiceBusTrigger`. Der Attributkonstruktor verwendet einen Parameter, der den Namen der abzufragenden Warteschlange angibt.

### Funktionsweise von "ServicebusTrigger"

Das SDK empfängt eine Nachricht im `PeekLock` -Modus und ruft `Complete` für die Meldung auf, wenn die Funktion erfolgreich abgeschlossen wird, oder ruft `Abandon` auf, wenn die Funktion fehlschlägt. Wenn das Ausführen der Funktion länger dauert als das `PeekLock`-Zeitlimit, wird die Sperre automatisch erneuert.

Service Bus verwaltet eine eigene Warteschlange für nicht verarbeitete Nachrichten. Deshalb ist weder eine Steuerung noch eine Konfiguration über das Webauftrags-SDK möglich. 

### Zeichenfolgen-Warteschlangennachrichten

Das folgende Codebeispiel liest eine Warteschlangennachricht, die eine Zeichenfolge enthält, und schreibt diese in das Webauftrags-SDK-Dashboard.

		public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
		    TextWriter logger)
		{
		    logger.WriteLine(message);
		}

**Hinweis:** Wenn Sie die Warteschlangennachrichten in einer Anwendung erstellen, die das WebJobs-SDK nicht verwendet, legen Sie [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) auf "text/plain" fest.

### POCO-Warteschlangennachricht

Das SDK deserialisiert automatisch Warteschlangennachrichten mit JSON für einen POCO [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object))-Typ. Das folgende Codebeispiel liest eine Warteschlangennachricht, die ein `BlobInformation`-Objekt mit einer `BlobName`-Eigenschaft enthält:

		public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
		    TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Codebeispiele, die zeigen, wie POCO-Eigenschaften zum Arbeiten mit Blobs und Tabellen in derselben Funktion verwendet werden, finden Sie in der [Speicher-Warteschlangen-Version dieses Artikels](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#pocoblobs).

### Für ServiceBusTrigger verwendbare Typen

Neben `string`- und POCO-  Typen, können Sie das Attribut `ServiceBusTrigger` mit einem Bytearray oder einem `BrokeredMessage`-Objekt verwenden.

## <a id="create"></a> Erstellen von Service Bus-Warteschlangennachrichten

Um eine Funktion zu schreiben, die eine neue Warteschlangennachricht erstellt, verwenden Sie das Attribut `ServiceBus`, und übergeben Sie den Namen der Warteschlange an den Konstruktor des Attributs. 


### Erstellen einer einzelnen Warteschlangennachricht in einer nicht asynchronen Funktion

Im folgenden Codebeispiel wird ein Ausgabeparameter zum Erstellen einer neuen Nachricht in der Warteschlange "outputqueue" verwendet. Diese hat den gleichen Inhalt wie die Nachricht, die in der Warteschlange "inputqueue" empfangen wird.

		public static void CreateQueueMessage(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] out string outputQueueMessage)
		{
		    outputQueueMessage = queueMessage;
		}

Der Ausgabeparameter zum Erstellen einer einzelnen Warteschlangennachricht kann einen der folgenden Typen verwenden:

* `Zeichenfolge`
* `Byte[]`
* `BrokeredMessage`
* Ein serialisierbarer POCO-Typ, den Sie definieren. Wird automatisch als JSON serialisiert.

Für Parameter vom POCO-Typ wird bei Beendigung der Funktion immer eine Warteschlangennachricht erstellt. Wenn der Parameter "null" ist, erstellt das SDK eine Warteschlangennachricht, die "null" zurückgibt, wenn die Nachricht empfangen und deserialisiert wird. Bei anderen Typen wird keine Warteschlangennachricht erstellt, wenn der Parameter "null" ist.

### Erstellen von mehreren Warteschlangennachrichten in asynchronen Funktionen

Um mehrere Nachrichten zu erstellen, verwenden Sie das Attribut `ServiceBus` mit `ICollector<T>` oder `IAsyncCollector<T>`, wie im folgenden Codebeispiel gezeigt:

		public static void CreateQueueMessages(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Jede Warteschlangennachricht wird erstellt, sobald die Methode  `Add` aufgerufen wird.

## <a id="topics"></a>Arbeiten mit Service Bus-Themen

Um eine Funktion zu schreiben, die das SDK aufruft, wenn eine Nachricht zu einem Service Bus-Thema empfangen wird, verwenden Sie das Attribut `ServiceBusTrigger` mit dem Konstruktor, der den Namen des Themas und den Abonnementnamen annimmt, wie im folgenden Codebeispiel gezeigt:

		public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
		    TextWriter logger)
		{
		    logger.WriteLine("Topic message: " + message);
		}

Um eine Nachricht zu einem Thema zu erstellen, verwenden Sie das Attribut `ServiceBus` mit einem Themanamen auf die gleiche Weise wie mit einem Warteschlangennamen.

## <a id="queues"></a>Verwandte Themen, die vom Artikel zu Speicher-Warteschlangen behandelt werden

Informationen zu Webauftrags-SDK-Szenarien, die nicht spezifisch für Service Bus sind, finden Sie unter [Arbeiten mit Azure-Warteschlangenspeicher mithilfe des WebJobs SDKs](websites-dotnet-webjobs-sdk-storage-queues-how-to.md). 

In diesem Artikel werden die folgenden Themen behandelt:

* Asynchrone Funktionen
* Mehrere Instanzen
* Ordnungsgemäßes Herunterfahren
* Verwenden von Webauftrags-SDK-Attributen im Hauptteil einer Funktion
* Festlegen der SDK-Verbindungszeichenfolgen im Code
* Festlegen von Werten für WebJobs SDK-Konstruktorparametern im Code
* Manuelles Auslösen einer Funktion
* Schreiben von Protokollen

## <a id="nextsteps"></a> Nächste Schritte

In dieser Anleitung wurden Codebeispiele bereitgestellt, die veranschaulichen, wie gängige Szenarien für das Arbeiten mit Azure Service Bus bewältigt werden. Weitere Informationen zur Verwendung von Azure-WebJobs und des Webauftrags-SDKs finden Sie unter [Empfohlene Ressourcen für Azure-Webaufträge](http://go.microsoft.com/fwlink/?linkid=390226).

<!--HONumber=45--> 
