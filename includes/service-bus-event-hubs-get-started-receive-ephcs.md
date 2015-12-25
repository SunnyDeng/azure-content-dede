## Empfangen von Nachrichten mit EventProcessorHost

[EventProcessorHost][] ist eine .NET-Klasse, die das Empfangen von Ereignissen von Event Hubs durch Verwalten von permanenten Prüfpunkten und parallelen Empfangsvorgängen von diesen Event Hubs vereinfacht. Mit [EventProcessorHost] können Sie Ereignisse selbst dann auf mehrere Empfänger aufteilen, wenn sie in verschiedenen Knoten gehostet werden. In diesem Beispiel wird die Verwendung von [EventProcessorHost] für einen einzelnen Empfänger veranschaulicht. Im Beispiel [Skalieren der Ereignisverarbeitung] wird veranschaulicht, wie [EventProcessorHost] mit mehreren Empfängern verwendet wird.

Um [EventProcessorHost] verwenden zu können, benötigen Sie ein [Azure-Speicherkonto]\:

1. Melden Sie sich beim [klassischen Azure-Portal][] an, und klicken Sie im unteren Teil des Bildschirms auf **NEU**.

2. Klicken Sie auf **Data Services**, dann auf **Speicher** und **Schnellerfassung**, und geben Sie dann einen Namen für das Speicherkonto ein. Wählen Sie die gewünschte Region aus, und klicken Sie dann auf **Speicherkonto erstellen**.

    ![][11]

3. Klicken Sie auf das neu erstellte Speicherkonto und anschließend auf **Zugriffsschlüssel verwalten**:

    ![][12]

    Kopieren Sie den Zugriffsschlüssel für die spätere Verwendung in diesem Tutorial.

4. Erstellen Sie in Visual Studio mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Desktopanwendungsprojekt. Geben Sie dem Projekt den Namen **Receiver**.

    ![][14]

5. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe und anschließend auf **NuGet-Pakete verwalten**.

	Das Dialogfeld **NuGet-Pakete verwalten** wird geöffnet.

6. Suchen Sie nach `Microsoft Azure Service Bus Event Hub - EventProcessorHost`, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.

    ![][13]

	Daraufhin wird das NuGet-Paket [Azure Service Bus Event Hub - EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) mit allen Abhängigkeiten heruntergeladen und installiert, und dem Projekt wird ein Verweis auf das Paket hinzugefügt.

7. Klicken Sie mit der rechten Maustaste auf das Projekt **Receiver**, klicken Sie auf **Hinzufügen** und dann auf **Klasse**. Geben Sie der neuen Klasse den Namen **SimpleEventProcessor**, und klicken Sie auf **OK**, um die Klasse zu erstellen.

8. Fügen Sie die folgenden Anweisungen am Anfang der Datei "SimpleEventProcessor.cs" hinzu:

	```
	using Microsoft.ServiceBus.Messaging;
	using System.Diagnostics;
	using System.Threading.Tasks;
	```

	Ersetzen Sie anschließend den Text der Klasse durch folgenden Code:

	```
    class SimpleEventProcessor : IEventProcessor
	{
	    Stopwatch checkpointStopWatch;

	    async Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
	    {
	        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
	        if (reason == CloseReason.Shutdown)
	        {
	            await context.CheckpointAsync();
	        }
	    }

	    Task IEventProcessor.OpenAsync(PartitionContext context)
	    {
	        Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);
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
    ````

	Diese Klasse wird von **EventProcessorHost** zur Verarbeitung der vom Event Hub empfangenen Ereignisse aufgerufen. Beachten Sie, dass die `SimpleEventProcessor`-Klasse eine Stoppuhr verwendet, um in regelmäßigen Abständen die "checkpoint"-Methode für den **EventProcessorHost**-Kontext aufzurufen. Dadurch wird sichergestellt, dass der Empfänger bei einem Neustart maximal nur die Daten verlieren kann, die in den letzten fünf Minuten verarbeitet wurden.

9. Fügen Sie am Anfang der **Program**-Klasse die folgenden `using`-Anweisungen hinzu:

	```
	using Microsoft.ServiceBus.Messaging;
	using Microsoft.Threading;
	using System.Threading.Tasks;
	```

	Ändern Sie dann wie unten gezeigt die **Program**-Klasse der **Main**-Methode, und ersetzen Sie dabei den Namen und die Verbindungszeichenfolge des Event Hubs sowie das Speicherkonto und den Speicherschlüssel, die Sie in den vorherigen Abschnitten kopiert haben.

    ```
	static void Main(string[] args)
    {
      string eventHubConnectionString = "{event hub connection string}";
      string eventHubName = "{event hub name}";
      string storageAccountName = "{storage account name}";
      string storageAccountKey = "{storage account key}";
      string storageConnectionString = string.Format("DefaultEndpointsProtocol=https;AccountName={0};AccountKey={1}", storageAccountName, storageAccountKey);

      string eventProcessorHostName = Guid.NewGuid().ToString();
      EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, eventHubName, EventHubConsumerGroup.DefaultGroupName, eventHubConnectionString, storageConnectionString);
      Console.WriteLine("Registering EventProcessor...");
      eventProcessorHost.RegisterEventProcessorAsync<SimpleEventProcessor>().Wait();

      Console.WriteLine("Receiving. Press enter key to stop worker.");
      Console.ReadLine();
      eventProcessorHost.UnregisterEventProcessorAsync().Wait();
    }
	````

> [AZURE.NOTE]In diesem Tutorial wird eine einzelne Instanz von [EventProcessorHost][] verwendet. Um den Durchsatz zu erhöhen, sollten Sie mehrere Instanzen von [EventProcessorHost][] ausführen, wie im Beispiel [Skalieren der Ereignisverarbeitung] beschrieben. In diesen Fällen koordinieren sich die verschiedenen automatisch untereinander, um die Last der eingegangenen Ereignisse ausgeglichen zu verteilen. Wenn mehrere Empfänger für jeden Prozess *alle* Ereignisse verarbeiten sollen, müssen Sie das **ConsumerGroup**-Konzept verwenden. Wenn Ereignisse von anderen Computern empfangen werden, kann es hilfreich sein, die [EventProcessorHost][]-Instanzen nach den Computern (oder Rollen) zu benennen, auf denen sie bereitgestellt werden. Weitere Informationen zu diesen Themen finden Sie unter [Event Hubs – Übersicht][] und im [Event Hubs-Programmierhandbuch][].

<!-- Links -->
[Event Hubs – Übersicht]: event-hubs-overview.md
[Event Hubs-Programmierhandbuch]: event-hubs-programming-guide.md
[Skalieren der Ereignisverarbeitung]: https://code.msdn.microsoft.com/Service-Bus-Event-Hub-45f43fc3
[Azure-Speicherkonto]: ../storage/storage-create-storage-account.md
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[klassischen Azure-Portal]: http://manage.windowsazure.com

<!-- Images -->

[11]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp2.png
[12]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp3.png
[13]: ./media/service-bus-event-hubs-getstarted/create-eph-csharp1.png
[14]: ./media/service-bus-event-hubs-getstarted/create-sender-csharp1.png

<!---HONumber=AcomDC_1203_2015-->
