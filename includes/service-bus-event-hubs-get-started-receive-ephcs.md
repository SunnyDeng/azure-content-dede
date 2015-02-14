## Empfangen von Nachrichten mit EventProcessorHost

**EventProcessorHost** ist eine .NET-Klasse, die dem Empfang von Ereignissen von Event Hubs durch die Verwaltung von permanenten Prüfpunkten vereinfacht und gleichzeitig die Ereignis-Hubs empfangen. Mit **EventProcessorHost** können Sie Ereignisse selbst dann auf mehrere Empfänger aufteilen, wenn sie in verschiedenen Knoten gehostet werden. Dieses Beispiel zeigt, wie **EventProcessorHost** für einen einzelnen Empfänger verwendet wird. Im [Beispiel "Skalieren der Ereignisverarbeitung] wird veranschaulicht, wie **EventProcessorHost** mit mehreren Empfängern verwendet wird.

Weitere Informationen zu Event Hubs finden Sie im [Event Hubs-Entwicklerhandbuch].

[EventProcessorHost] ist eine .NET-Klasse, die dem Empfang von Ereignissen von Event Hubs durch die Verwaltung von permanenten Prüfpunkten vereinfacht und gleichzeitig die Ereignis-Hubs empfangen. Mit [EventProcessorHost] können Sie Ereignisse selbst dann auf mehrere Empfänger aufteilen, wenn sie in verschiedenen Knoten gehostet werden. Dieses Beispiel zeigt, wie [EventProcessorHost] für einen einzelnen Empfänger verwendet wird. Im [Beispiel "Skalieren der Ereignisverarbeitung] wird veranschaulicht, wie [EventProcessorHost] mit mehreren Empfängern verwendet wird.

Um [EventProcessorHost] verwenden zu können, müssen Sie ein [Azure-Speicherkonto] besitzen:

1. Melden Sie sich beim [Azure-Verwaltungsportal] an, und klicken Sie dann im unteren Bereich des Bildschirms auf **NEU**.

2. Klicken Sie auf**Data Services**, dann auf **Storage** und **Schnellerfassung**, und geben Sie dann einen Namen für das Speicherkonto ein. Wählen Sie die gewünschte Region aus, und klicken Sie dann auf **Speicherkonto erstellen**.

   	![][11]

3. Klicken Sie auf das neu erstellte Speicherkonto und anschließend auf **Zugriffsschlüssel verwalten**:

   	![][12]

	Kopieren Sie den Zugriffsschlüssel zur späteren Verwendung.

4. Erstellen Sie in Visual Studio mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Desktopanwendungsprojekt. Geben Sie dem Projekt den Namen **Receiver**.

   	![][14]

5. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe und anschließend auf **NuGet-Pakete verwalten**. 

	Das Dialogfeld **NuGet-Pakete verwalten** wird geöffnet.

6. Suchen Sie nach `Microsoft Azure Service Bus Event Hub - EventProcessorHost`, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen. 

	![][13]

	Daraufhin wird das <a href="https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost">NuGet-Paket "Azure Service Bus Event Hub - EventProcessorHost"</a> mit allen Abhängigkeiten heruntergeladen und installiert und dem Projekt ein Verweis auf das Paket hinzugefügt.

7. Erstellen Sie eine neue Klasse namens **SimpleEventProcessor**, und fügen Sie die folgenden Anweisungen am Dateianfang hinzu:

		using Microsoft.ServiceBus.Messaging;
		using System.Diagnostics;
		using System.Threading.Tasks;

	Fügen Sie anschließend den folgenden Code als Text der Klasse ein:

		class SimpleEventProcessor : IEventProcessor
	    {
	        Stopwatch checkpointStopWatch;
	        
	        async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	        {
	            Console.WriteLine(string.Format("Processor Shuting Down.  Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason.ToString()));
	            if (reason == CloseReason.Shutdown)
	            {
	                await context.CheckpointAsync();
	            }
	        }
	
	        Task IEventProcessor.OpenAsync(PartitionContext context)
	        {
	            Console.WriteLine(string.Format("SimpleEventProcessor initialize.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset));
	            this.checkpointStopWatch = new Stopwatch();
	            this.checkpointStopWatch.Start();
	            return Task.FromResult<object>(null);
	        }
	
	        async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
	        {
	            foreach (EventData eventData in messages)
	            {
	                string data = Encoding.UTF8.GetString(eventData.GetBytes());
	                
	                Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
	                    context.Lease.PartitionId, data));
	            }
	
	            //Call checkpoint every 5 minutes, so that worker can resume processing from the 5 minutes back if it restarts.
	            if (this.checkpointStopWatch.Elapsed > TimeSpan.FromMinutes(5)) 
                { 
                    await context.CheckpointAsync(); 
                    this.checkpointStopWatch.Restart(); 
                } 
	        }
	    }

	Diese Klasse wird von **EventProcessorHost** zur Verarbeitung der vom Ereignis-Hub empfangenen Ereignisse aufgerufen. Beachten Sie, dass die Klasse `SimpleEventProcessor` eine Stoppuhr verwendet, um in regelmäßigen Abständen die checkpoint-Methode für den **EventProcessorHost**-Kontext aufzurufen. Dadurch wird sichergestellt, dass der Empfänger bei einem Neustart maximal nur die Daten verlieren kann, die in den letzten fünf Minuten verarbeitet wurden.

8. Fügen Sie am Anfang der **Program**-Klasse die folgenden `using`-Anweisungen hinzu:

		using Microsoft.ServiceBus.Messaging;
		using System.Threading.Tasks;
	
	Fügen Sie dann den folgenden Code in die **Main**-Methode ein, wobei Sie den Ereignis-Hub-Namen, die Verbindungszeichenfolge, das Speicherschlüssel und den Schlüssel, die Sie in den vorigen Abschnitten kopiert haben, einsetzen:

		string eventHubConnectionString = "{event hub connection string}";
        string eventHubName = "{event hub name}";
        string storageAccountName = "{storage account name}";
        string storageAccountKey = "{storage account key}";
        string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}",
                    storageAccountName, storageAccountKey);

        string eventProcessorHostName = Guid.NewGuid().ToString();
        EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
        eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>().Wait();
            
        Console.WriteLine("Receiving. Press enter key to stop worker.");
        Console.ReadLine();

> [AZURE.NOTE] Dieses Lernprogramm verwendet eine einzelne Instanz von [EventProcessorHost]. Um den Durchsatz zu erhöhen, wird empfohlen, dass Sie mehrere Instanzen von [EventProcessorHost] ausführen, wie im Beispiel [Skalieren der Ereignisverarbeitung] gezeigt. In diesen Fällen koordinieren sich die verschiedenen automatisch untereinander, um die Last der eingegangenen Ereignisse ausgeglichen zu verteilen. Wenn mehrere Empfänger für jeden Prozess *all* Ereignisse verarbeiten sollen, müssen Sie das **ConsumerGroup**-Konzept verwenden. Wenn Ereignisse von anderen Computern empfangen werden, kann es hilfreich sein, die [EventProcessorHost]-Instanzen nach den Computern (oder Rollen) zu benennen, auf denen sie bereitgestellt worden sind. Weitere Informationen zu diesen Themen finden Sie unter [Übersicht über Ereignis-Hubs] und [Event Hubs Programming Guide].

<!-- Links -->
[Übersicht über Ereignis-Hubs]: http://msdn.microsoft.com/de-de/library/azure/dn821413.aspx
[Beispiel "Skalieren der Ereignisverarbeitung"]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[Azure-Speicherkonto]: http://azure.microsoft.com/de-de/documentation/articles/storage-create-storage-account/
[EventProcessorHost]: http://msdn.microsoft.com/de-de/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx 

<!-- Images -->

[11]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp3.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png

[Event Hubs-Entwicklerhandbuch]: http://msdn.microsoft.com/de-de/library/azure/dn789972.aspx<!--HONumber=42-->
