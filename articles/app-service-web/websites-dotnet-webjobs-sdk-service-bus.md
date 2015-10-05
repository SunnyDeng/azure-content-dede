<properties 
	pageTitle="Verwenden von Azure Service Bus mit dem WebJobs-SDK" 
	description="Erfahren Sie mehr über die Verwendung von Azure Service Bus-Warteschlangen und -Themen mit dem WebJobs-SDK." 
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
	ms.date="09/22/2015" 
	ms.author="tdykstra"/>

# Verwenden von Azure Service Bus mit dem WebJobs-SDK

## Übersicht

Dieser Leitfaden enthält C#-Codebeispiele, die zeigen, wie Sie einen Prozess auslösen, wenn ein Azure-Blob erstellt oder aktualisiert wird. In den Codebeispielen wird Version 1.x des [WebJobs-SDK](websites-dotnet-webjobs-sdk.md) verwendet.

Im Handbuch wird davon ausgegangen, dass Sie wissen, [wie ein WebJobs-Projekt in Visual Studio mit Verbindungszeichenfolgen erstellt wird, die auf Ihr Speicherkonto verweisen](websites-dotnet-webjobs-sdk-get-started.md).

Die Codeausschnitte zeigen nur Funktionen, nicht den Code, mit dem das `JobHost`-Objekt in diesem Beispiel erstellt wird:

		static void Main(string[] args)
		{
		    JobHost host = new JobHost();
		    host.RunAndBlock();
		}
		
## <a id="prerequisites"></a> Voraussetzungen

Für die Arbeit mit Service Bus müssen Sie neben den weiteren WebJobs-SDK-Paketen das NuGet-Paket [Microsoft.Azure.WebJobs.ServiceBus](https://www.nuget.org/packages/Microsoft.Azure.WebJobs.ServiceBus/) installieren.

Sie müssen außerdem zusätzlich zu den Speicherverbindungszeichenfolgen die Verbindungszeichenfolge "AzureWebJobsServiceBus" festlegen. Sie führen dies im Abschnitt `connectionStrings` der Datei "App.config" aus, wie im folgenden Beispiel gezeigt:

		<connectionStrings>
		    <add name="AzureWebJobsDashboard" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
		    <add name="AzureWebJobsStorage" connectionString="DefaultEndpointsProtocol=https;AccountName=[accountname];AccountKey=[accesskey]"/>
		    <add name="AzureWebJobsServiceBus" connectionString="Endpoint=sb://[yourServiceNamespace].servicebus.windows.net/;SharedAccessKeyName=RootManageSharedAccessKey;SharedAccessKey=[yourKey]"/>
		</connectionStrings>

Ein Beispielprojekt, das die Einstellung für die Service Bus-Verbindungszeichenfolge in der Datei "App.config" enthält, finden Sie im [Service Bus-Beispiel](https://github.com/Azure/azure-webjobs-sdk-samples/tree/master/BasicSamples/ServiceBus).

Die Verbindungszeichenfolgen können auch in der Azure-Laufzeitumgebung festgelegt werden, die dann die „App.config“-Einstellungen überschreibt, wenn der WebJob in Azure ausgeführt wird. Weitere Informationen finden Sie unter [Erste Schritte mit dem WebJobs-SDK](websites-dotnet-webjobs-sdk-get-started.md#configure-the-web-app-to-use-your-azure-sql-database-and-storage-account).

## <a id="trigger"></a> Auslösen einer Funktion, wenn eine Service Bus-Warteschlangennachricht empfangen wird

Zum Schreiben einer Funktion, die das WebJobs-SDK aufruft, wenn eine Warteschlangennachricht empfangen wird, verwenden Sie das `ServiceBusTrigger`-Attribut. Der Attributkonstruktor verwendet einen Parameter, der den Namen der abzufragenden Warteschlange angibt.

### Funktionsweise von "ServicebusTrigger"

Das SDK empfängt eine Nachricht im `PeekLock`-Modus und ruft bei erfolgreicher Ausführung der Funktion `Complete` für die Nachricht auf. Ist die Ausführung nicht erfolgreich, wird `Abandon` aufgerufen. Wenn die Funktion länger als im `PeekLock`-Timeout angegeben ausgeführt wird, wird die Sperre automatisch erneuert.

Service Bus verwaltet eine eigene Warteschlange für nicht verarbeitete Nachrichten. Deshalb ist weder eine Steuerung noch eine Konfiguration über das WebJobs-SDK möglich.

### Zeichenfolgen-Warteschlangennachrichten

Das folgende Codebeispiel liest eine Warteschlangennachricht, die eine Zeichenfolge enthält, und schreibt diese in das WebJobs-SDK-Dashboard.

		public static void ProcessQueueMessage([ServiceBusTrigger("inputqueue")] string message, 
		    TextWriter logger)
		{
		    logger.WriteLine(message);
		}

**Hinweis:** Wenn Sie die Warteschlangennachrichten in einer Anwendung erstellen, die das WebJobs-SDK nicht verwendet, legen Sie [BrokeredMessage.ContentType](http://msdn.microsoft.com/library/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx) auf "text/plain" fest.

### POCO-Warteschlangennachricht

Das SDK führt eine automatische Deserialisierung der Warteschlangennachricht durch, die für einen POCO-Typ [(Plain Old CLR Object)](http://en.wikipedia.org/wiki/Plain_Old_CLR_Object) JSON enthält. Das folgende Codebeispiel liest eine Warteschlangennachricht, die ein `BlobInformation`-Objekt mit einer `BlobName`-Eigenschaft enthält:

		public static void WriteLogPOCO([ServiceBusTrigger("inputqueue")] BlobInformation blobInfo,
		    TextWriter logger)
		{
		    logger.WriteLine("Queue message refers to blob: " + blobInfo.BlobName);
		}

Codebeispiele, die zeigen, wie POCO-Eigenschaften zum Arbeiten mit Blobs und Tabellen in derselben Funktion verwendet werden, finden Sie in der [Artikelversion zu Speicherwarteschlangen](websites-dotnet-webjobs-sdk-storage-queues-how-to.md#pocoblobs).

Wenn Ihr Code zum Erstellen der Warteschlangennachricht nicht das WebJobs-SDK verwendet, verwenden Sie ähnlichen Code wie im folgenden Beispiel:

		var client = QueueClient.CreateFromConnectionString(ConfigurationManager.ConnectionStrings["AzureWebJobsServiceBus"].ConnectionString, "blobadded");
		BlobInformation blobInformation = new BlobInformation () ;
		var message = new BrokeredMessage(blobInformation);
		client.Send(message);

### Für "ServiceBusTrigger" verwendbare Typen

Neben `string` und POCO-Typen können Sie das `ServiceBusTrigger`-Attribut mit einem Bytearray oder einem `BrokeredMessage`-Objekt verwenden.

## <a id="create"></a> Erstellen von Service Bus-Warteschlangennachrichten

Zum Schreiben einer Funktion, die eine neue Warteschlangennachricht erstellt, verwenden Sie das `ServiceBus`-Attribut und übergeben den Warteschlangennamen an den Attributkonstruktor.


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

Verwenden Sie zum Erstellen mehrerer Nachrichten das `ServiceBus`-Attribut mit `ICollector<T>` oder `IAsyncCollector<T>`, wie im folgenden Codebeispiel gezeigt:

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

## <a id="queues"></a>Verwandte Themen, die im Artikel zu Speicherwarteschlangen behandelt werden

Informationen zu WebJobs-SDK-Szenarien, die nicht spezifisch für Service Bus sind, finden Sie unter [Verwenden des Azure-Warteschlangenspeichers mit dem WebJobs-SDK](websites-dotnet-webjobs-sdk-storage-queues-how-to.md).

In diesem Artikel werden u. a. die folgenden Themen behandelt:

* Asynchrone Funktionen
* Mehrere Instanzen
* Ordnungsgemäßes Herunterfahren
* Verwenden von WebJobs-SDK-Attributen im Hauptteil einer Funktion
* Festlegen der SDK-Verbindungszeichenfolgen im Code
* Festlegen von Werten für WebJobs SDK-Konstruktorparametern im Code
* Manuelles Auslösen einer Funktion
* Schreiben von Protokollen

## <a id="nextsteps"></a> Nächste Schritte

In dieser Anleitung wurde anhand von Codebeispielen veranschaulicht, wie häufige Szenarien für das Arbeiten mit Azure Service Bus behandelt werden. Weitere Informationen zur Verwendung von Azure WebJobs und dem WebJobs-SDK finden Sie unter [Empfohlene Ressourcen für Azure WebJobs](http://go.microsoft.com/fwlink/?linkid=390226).
 

<!---HONumber=Sept15_HO4-->