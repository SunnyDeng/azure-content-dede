<properties 
	pageTitle="Verwenden von Azure Service Bus mit dem Webaufträge-SDK" 
	description="Erfahren Sie mehr über die Verwendung von Azure Service Bus mit dem Webaufträge-SDK." 
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

# Verwenden von Azure Service Bus mit dem Webaufträge-SDK

Dieser Leitfaden enthält C#-Codebeispiele, die zeigen, wie Sie einen Prozess auslösen, wenn ein Azure-BLOB erstellt oder aktualisiert wird. Die Codebeispiele verwenden das [Webaufträge-SDK](../websites-dotnet-webjobs-sdk/) 1.x.

Im Leitfaden wird davon ausgegangen, dass Sie wissen, [wie ein Webauftragsprojekt in Visual Studio mit Verbindungszeichenfolgen erstellt wird, die auf Ihr Speicherkonto zeigen](../websites-dotnet-webjobs-sdk-get-started/).

Die Codeausschnitte zeigen nur Funktionen, nicht den Code, mit dem das `JobHost`-Objekt in diesem Beispiel erstellt wird:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## Inhaltsverzeichnis

-   [Voraussetzungen](#prerequisites)
-   [Auslösen einer Funktion, wenn eine Warteschlangennachricht empfangen wird](#trigger)
-   [Erstellen von Warteschlangennachrichten](#create)
-   [Arbeiten mit Service Bus-Themen](#topics)
-   [Verwandte Themen im Artikel zu Speicherwarteschlangen](#queues)
-   [Nächste Schritte](#nextsteps)

## <a id="prerequisites"></a>Voraussetzungen

Für die Arbeit mit Service Bus müssen Sie neben den weiteren Webaufträge-SDK-Paketen das NuGet-Paket [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) installieren. 

Sie müssen außerdem zusätzlich zu den Speicherverbindungszeichenfolgen die Verbindungszeichenfolge "AzureWebJobsServiceBus" festlegen.

## <a id="trigger"></a>Auslösen einer Funktion, wenn eine Service Bus-Warteschlangennachricht empfangen wird

Zum Schreiben einer Funktion, die bei Empfang einer Warteschlangennachricht  das Webaufträge-SDK aufruft, verwenden Sie das Attribut `ServiceBusTrigger`. Der Attributkonstruktor verwendet einen Parameter, der den Namen der abzufragenden Warteschlange angibt.

### Funktionsweise von "ServicebusTrigger"

Das SDK empfängt eine Nachricht im `PeekLock`-Modus und ruft bei erfolgreicher Ausführung der Funktion `Complete` für die Nachricht auf. Ist die Ausführung nicht erfolgreich, wird `Abandon` aufgerufen. Wenn die Funktion länger als im `PeekLock`-Timeout angegeben ausgeführt wird, wird die Sperre automatisch erneuert.

Service Bus verwaltet eine eigene Warteschlange für nicht verarbeitete Nachrichten. Deshalb ist weder eine Steuerung noch eine Konfiguration über das Webaufträge-SDK möglich. 

### Zeichenfolgen-Warteschlangennachrichten

Das folgende Codebeispiel liest eine Warteschlangennachricht, die eine Zeichenfolge enthält, und schreibt diese in das Webaufträge-SDK-Dashboard.

		public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
		    TextWriter logger)
		{
		    logger.WriteLine(message);
		}

**Hinweis:** Wenn Sie die Warteschlangennachricht in einer Anwendung ohne Verwendung des Webaufträge-SDK schreiben, müssen Sie [BrokeredMessage.ContentType](http://msdn.microsoft.com/de-de/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) auf "text/plain" festlegen.

### POCO-Warteschlangennachricht

Das SDK führt eine automatische Deserialisierung der Warteschlangennachricht durch, die für einen POCO-Typ [(Plain Old CLR Object](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object)) JSON enthält. Das folgende Codebeispiel liest eine Warteschlangennachricht, die ein  `BlobInformation`-Objekt mit einer  `BlobName`-Eigenschaft enthält:

		public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
		    TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Codebeispiele zur Verwendung von POCO-Eigenschaften zur Arbeit mit BLOBs und Tabellen in derselben Funktion finden Sie in der [Speicherwarteschlangenversion von diesem Artikel](../websites-dotnet-webjobs-sdk-storage-queues-how-to/#pocoblobs).

### Für ServiceBusTrigger verwendbare Typen

Neben `string` und POCO-Typen können Sie das `ServiceBusTrigger`-Attribut mit einem Bytearray oder einem `BrokeredMessage`-Objekt verwenden.

## <a id="create"></a> Erstellen von Service Bus-Warteschlangennachrichten

Zum Schreiben einer Funktion, die eine neue Warteschlangennachricht erstellt, verwenden Sie das  `ServiceBus`-Attribut und übergeben den Warteschlangennamen an den Attributkonstruktor. 


### Erstellen einer einzelnen Warteschlangennachricht in einer nicht asynchronen Funktion

Im folgenden Codebeispiel wird ein Ausgabeparameter zum Erstellen einer neuen Nachricht in der Warteschlange "outputqueue" verwendet. Diese hat den gleichen Inhalt wie die Nachricht, die in der Warteschlange "inputqueue" empfangen wird.

		public static void CreateQueueMessage(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] out string outputQueueMessage)
		{
		    outputQueueMessage = queueMessage;
		}

Der Ausgabeparameter zum Erstellen einer einzelnen Warteschlangennachricht kann einen beliebigen dieser Typen verwenden:

* `string`
* `byte[]`
* `BrokeredMessage`
* Ein serialisierbarer POCO-Typ, den Sie definieren. Wird automatisch als JSON serialisiert.

Für Parameter vom POCO-Typ wird bei Beendigung der Funktion immer eine Warteschlangennachricht erstellt. Wenn der Parameter "null" ist, erstellt das SDK eine Warteschlangennachricht, die "null" zurückgibt, wenn die Nachricht empfangen und deserialisiert wird. Bei anderen Typen wird keine Warteschlangennachricht erstellt, wenn der Parameter "null" ist.

### Erstellen von mehreren Warteschlangennachrichten in asynchronen Funktionen

Verwenden Sie zum Erstellen mehrerer Nachrichten das Attribut `ServiceBus` mit `ICollector<T>` oder `IAsyncCollector<T>`, wie im folgenden Codebeispiel gezeigt:

		public static void CreateQueueMessages(
		    [ServiceBusTrigger("inputqueue")] string queueMessage,
		    [ServiceBus("outputqueue")] ICollector<string> outputQueueMessage,
		    TextWriter logger)
		{
		    logger.WriteLine("Creating 2 messages in outputqueue");
		    outputQueueMessage.Add(queueMessage + "1");
		    outputQueueMessage.Add(queueMessage + "2");
		}

Jede Warteschlangennachricht wird unmittelbar nach dem Aufruf der `Add`-Methode erstellt.

## <a id="topics"></a>Arbeiten mit Service Bus-Themen

Zum Schreiben einer Funktion, die das SDK aufruft, wenn eine Nachricht zu einem Service Bus-Thema empfangen wird, verwenden Sie das `ServiceBusTrigger`-Attribut mit dem Konstruktor, der Themen- und Abonnementname enthält, wie im folgenden Codebeispiel gezeigt:

		public static void WriteLog([ServiceBusTrigger("outputtopic","subscription1")] string message,
		    TextWriter logger)
		{
		    logger.WriteLine("Topic message: " + message);
		}

Zum Erstellen einer Nachricht zu einem Thema verwenden Sie das `ServiceBus`-Attribut mit einem Themennamen ebenso wie bei Verwendung eines Warteschlangennamens.

## <a id="queues"></a>Verwandte Themen im Artikel zu Speicherwarteschlangen

Informationen zu Webaufträge-SDK-Szenarien, die nicht spezifisch für Service Bus sind, finden Sie unter [Verwenden von Azure-Warteschlangenspeicher mit dem Webaufträge-SDK](../websites-dotnet-webjobs-sdk-storage-queues-how-to/). 

In diesem Artikel werden unter anderem diese Themen behandelt:

* Asynchrone Funktionen
* Mehrere Instanzen
* Ordnungsgemäßes Herunterfahren
* Verwenden von Webaufträge-SDK-Attributen im Hauptteil einer Funktion
* Festlegen der SDK-Verbindungszeichenfolgen im Code
* Festlegen von Werten für Webaufträge-SDK-Konstruktorparameter im Code
* Manuelles Auslösen einer Funktion
* Schreiben von Protokollen

## <a id="nextsteps"></a> Nächste Schritte

In diesem Leitfaden wurden Codebeispiele bereitgestellt, die veranschaulichen, wie gängige Szenarien für die Arbeit mit Azure Service Bus behandelt werden. Weitere Informationen zur Verwendung von Azure-Webaufträgen und dem Webaufträge-SDK finden Sie unter [Empfohlene Ressourcen für Azure-Webaufträge](http://go.microsoft.com/fwlink/?linkid=390226).




<!--HONumber=42-->
