## Verarbeiten von Device-to-Cloud (D2C)-Nachrichten

In diesem Abschnitt erstellen Sie eine Windows-Konsolen-App, die D2C-Nachrichten von IoT Hub verarbeitet. IoT Hub macht einen [Event Hubs][Event Hubs Overview]-kompatiblen Endpunkt zum Lesen von D2C-Nachrichten verfügbar. In diesem Lernprogramm wird [EventProcessorHost] zum Verarbeiten dieser Nachrichten in einer Konsolen-App verwendet. Weitere Informationen zum Verarbeiten von Nachrichten von Event Hubs finden Sie im Lernprogramm [Erste Schritte mit Event Hubs].

Die größte Herausforderung beim Implementieren der zuverlässigen Speicherung von Datenpunktnachrichten oder Weiterleiten von interaktiven Nachrichten besteht darin, dass für die Event Hubs-Ereignisverarbeitung der Nachrichtenconsumer seinen Status überprüfen muss. Darüber hinaus sollten Prüfpunkte zur Erzielung eines hohen Durchsatzes beim Lesen von Event Hubs in großen Batches verarbeitet werden. Hierbei besteht die Möglichkeit, dass eine große Zahl von Nachrichten doppelt verarbeitet wird. In diesem Lernprogramm wird veranschaulicht, wie Azure-Speicherschreibvorgänge und Service Bus-Deduplizierungsfenster mit Ereignisprozessorhost-Prüfpunkten synchronisiert werden.

Um Nachrichten zuverlässig in den Azure-Speicher zu schreiben, verwenden wir das Feature [Blockblobs][Azure Block Blobs] des individuellen Block-Commits. Der Ereignisprozessor akkumuliert Nachrichten im Arbeitsspeicher, bis es an der Zeit ist, einen Prüfpunktvorgang auszuführen (also nachdem der akkumulierte Puffer die Größe der maximalen Blockgröße von 4 Mb übersteigt oder nachdem die Service Bus-Deduplizierungszeitfenster abgelaufen sind). Vor dem Setzen von Prüfpunkten wird dann ein Commit für einen neuen Block an den Blob durchgeführt.

Der Ereignisprozessor nutzt Event Hubs-Nachrichten-Offsets als Block-IDs. Dies ermöglicht eine Überprüfung der Deduplizierung vor dem Ausführen eines Commits für den neuen Block an den Speicher, und ein etwaiger Konflikt zwischen einem Block mit durchgeführtem Commit und dem Prüfpunkt wird verhindert.

> [AZURE.NOTE]In diesem Lernprogramm wird ein einzelnes Speicherkonto zum Schreiben aller Nachrichten verwendet, die von IoT Hub abgerufen werden. Informationen zum Treffen der Entscheidung, ob Sie in Ihrer Lösung mehrere Azure-Speicherkonten benötigen, finden Sie unter [Richtlinien zur Azure-Speicherskalierbarkeit].

Um Duplikate bei der Verarbeitung von interaktiven Nachrichten zu vermeiden, nutzen wir die Service Bus-Deduplizierungsfunktion. Indem jede interaktive Nachricht mit einer eindeutigen `MessageId`-Kennzeichnung versehen wird, kann Service Bus sicherstellen, dass im angegebenen Deduplizierungszeitfenster nicht zwei Nachrichten mit der gleichen `MessageId` an die Empfänger übermittelt werden. Diese Deduplizierung sorgt zusammen mit der Abschlusssemantik pro Nachricht von Service Bus-Warteschlangen dafür, dass die zuverlässige Verarbeitung von interaktiven Nachrichten deutlich vereinfacht wird.

Um sicherzustellen, dass keine Nachrichten außerhalb des Deduplizierungsfensters erneut übermittelt werden, synchronisieren wir den Prüfpunktmechanismus des Ereignisprozessorhosts mit dem Deduplizierungsfenster der Service Bus-Warteschlange. Dies wird erreicht, indem ein Prüfpunkt mindestens einmal pro Zeitfenster abläuft (in diesem Lernprogramm ist dies eine Stunde).

> [AZURE.NOTE]In diesem Lernprogramm wird eine einzelne partitionierte Service Bus-Warteschlange verwendet, um alle interaktiven Nachrichten zu verarbeiten, die von IoT Hub abgerufen werden. Weitere Informationen zur Verwendung von Service Bus-Warteschlangen je nach den Anforderungen Ihrer Lösung finden Sie in der [Service Bus-Dokumentation].

### Bereitstellen eines Azure-Speicherkontos und einer Service Bus-Warteschlange
Um [EventProcessorHost] verwenden zu können, benötigen Sie ein Azure-Speicherkonto. Sie können ein vorhandenes Konto verwenden oder mithilfe der Anweisungen unter [Informationen zu Azure Storage] ein neues Konto erstellen. Notieren Sie die Verbindungszeichenfolge für das Speicherkonto.

Sie benötigen außerdem eine Service Bus-Warteschlange, um die zuverlässige Verarbeitung von interaktiven Nachrichten zu aktivieren. Sie können eine Warteschlange programmgesteuert mit einem Deduplizierungsfenster von einer Stunde erstellen. Dies ist unter [Verwenden von Service Bus-Warteschlangen][Service Bus Queue] beschrieben, oder verwenden Sie das [Azure-Verwaltungsportal], und führen Sie die folgenden Schritte aus:

1. Klicken Sie in der Ecke unten links auf **NEU** und dann nacheinander auf **App Services**, **Service Bus**, **Warteschlange** und **Benutzerdefiniert**. Wählen Sie den Namen **d2ctutorial** aus, und wählen Sie anschließend ein Deduplizierungsfenster von einer Stunde aus.

    ![][30]

2. Klicken Sie auf den Namen der Warteschlange (**d2ctutorial**) und dann auf **Konfigurieren**, und erstellen Sie zwei SAS-Richtlinien: eine mit dem Namen **send** mit Berechtigungen zum **Senden**, und eine mit dem Namen **listen** mit Berechtigungen zum **Lauschen**. Klicken Sie unten auf **Speichern**, wenn Sie fertig sind.

    ![][31]

3. Klicken Sie oben auf **Dashboard** und dann unten auf **Verbindungsinformationen**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

    ![][32]

### Erstellen des Ereignisprozessors

1. Klicken Sie in der aktuellen Visual Studio-Lösung auf **Datei -> Hinzufügen -> Projekt**, um mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Desktopanwendungsprojekt zu erstellen. Geben Sie dem Projekt den Namen **ProcessDeviceToCloudMessages**.

    ![][10]

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe und anschließend auf **NuGet-Pakete verwalten**.

    Das Dialogfeld **NuGet-Pakete verwalten** wird geöffnet.

3. Suchen Sie nach `WindowsAzure.ServiceBus`, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.

    Daraufhin wird das [Azure Service Bus-NuGet-Paket](https://www.nuget.org/packages/WindowsAzure.ServiceBus) mit allen Abhängigkeiten heruntergeladen und installiert, und dem Projekt wird ein Verweis auf das Paket hinzugefügt.

4. Suchen Sie nach `Microsoft Azure Service Bus Event Hub - EventProcessorHost`, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.

    Daraufhin wird das NuGet-Paket [Azure Service Bus Event Hub – EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) mit allen Abhängigkeiten heruntergeladen und installiert, und dem Projekt wird ein Verweis auf das Paket hinzugefügt.

5. Klicken Sie mit der rechten Maustaste auf das Projekt **ProcessDeviceToCloudMessages**, klicken Sie auf **Hinzufügen**, und klicken Sie dann auf **Klasse**. Geben Sie der neuen Klasse den Namen **StoreEventProcessor**, und klicken Sie auf **OK**, um die Klasse zu erstellen.

6. Fügen Sie die folgenden Anweisungen am Anfang der Datei "SimpleEventProcessor.cs" hinzu:

        using System.IO;
        using System.Diagnostics;
        using System.Security.Cryptography;
        using Microsoft.ServiceBus.Messaging;
        using Microsoft.WindowsAzure.Storage;
        using Microsoft.WindowsAzure.Storage.Blob;

    Ersetzen Sie anschließend den Text der Klasse durch folgenden Code:

        class StoreEventProcessor : IEventProcessor
        {
            private const int MAX_BLOCK_SIZE = 4 * 1024 * 1024;
            public static string StorageConnectionString;
            public static string ServiceBusConnectionString;

            private CloudBlobClient blobClient;
            private CloudBlobContainer blobContainer;
            private QueueClient queueClient;

            private long currentBlockInitOffset;
            private MemoryStream toAppend = new MemoryStream(MAX_BLOCK_SIZE);

            private Stopwatch stopwatch;
            private TimeSpan MAX_CHECKPOINT_TIME = TimeSpan.FromHours(1);

            public StoreEventProcessor()
            {
                var storageAccount = CloudStorageAccount.Parse(StorageConnectionString);
                blobClient = storageAccount.CreateCloudBlobClient();
                blobContainer = blobClient.GetContainerReference("d2ctutorial");
                queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString, "d2ctutorial");
            }

            Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
            {
                Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
                return Task.FromResult<object>(null);
            }

            Task IEventProcessor.OpenAsync(PartitionContext context)
            {
                Console.WriteLine("SimpleEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

                if (!long.TryParse(context.Lease.Offset, out currentBlockInitOffset))
                {
                    currentBlockInitOffset = 0;
                }
                stopwatch = new Stopwatch();
                stopwatch.Start();

                return Task.FromResult<object>(null);
            }

            async Task IEventProcessor.ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
            {
                foreach (EventData eventData in messages)
                {
                    byte[] data = eventData.GetBytes();

                    if (eventData.Properties.ContainsKey("messageType") && (string) eventData.Properties["messageType"] == "interactive")
                    {
                        var messageId = (string) eventData.SystemProperties["message-id"];

                        var queueMessage = new BrokeredMessage(new MemoryStream(data));
                        queueMessage.MessageId = messageId;
                        queueMessage.Properties["messageType"] = "interactive";
                        await queueClient.SendAsync(queueMessage);

                        WriteHighlightedMessage(string.Format("Received interactive message: {0}", messageId));
                        continue;
                    }
                    
                    if (toAppend.Length + data.Length > MAX_BLOCK_SIZE || stopwatch.Elapsed > MAX_CHECKPOINT_TIME)
                    {
                        await AppendAndCheckpoint(context);
                    }
                    await toAppend.WriteAsync(data, 0, data.Length);

                    Console.WriteLine(string.Format("Message received.  Partition: '{0}', Data: '{1}'",
                        context.Lease.PartitionId, Encoding.UTF8.GetString(data)));
                }
            }

            private async Task AppendAndCheckpoint(PartitionContext context)
            {
                var blockIdString = String.Format("startSeq:{0}", currentBlockInitOffset.ToString("0000000000000000000000000"));
                var blockId = Convert.ToBase64String(ASCIIEncoding.ASCII.GetBytes(blockIdString));
                toAppend.Seek(0, SeekOrigin.Begin);
                byte[] md5 = MD5.Create().ComputeHash(toAppend);
                toAppend.Seek(0, SeekOrigin.Begin);

                var blobName = String.Format("iothubd2c_{0}", context.Lease.PartitionId);
                var currentBlob = blobContainer.GetBlockBlobReference(blobName);

                if (await currentBlob.ExistsAsync())
                {
                    await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
                    var blockList = await currentBlob.DownloadBlockListAsync();
                    var newBlockList = new List<string>(blockList.Select(b => b.Name));

                    if (newBlockList.Count() > 0 && newBlockList.Last() != blockId)
                    {
                        newBlockList.Add(blockId);
                        WriteHighlightedMessage(String.Format("Appending block id: {0} to blob: {1}", blockIdString, currentBlob.Name));
                    }
                    else
                    {
                        WriteHighlightedMessage(String.Format("Overwriting block id: {0}", blockIdString));
                    }
                    await currentBlob.PutBlockListAsync(newBlockList);
                }
                else
                {
                    await currentBlob.PutBlockAsync(blockId, toAppend, Convert.ToBase64String(md5));
                    var newBlockList = new List<string>();
                    newBlockList.Add(blockId);
                    await currentBlob.PutBlockListAsync(newBlockList);

                    WriteHighlightedMessage(String.Format("Created new blob", currentBlob.Name));
                }

                toAppend.Dispose();
                toAppend = new MemoryStream(MAX_BLOCK_SIZE);

                // checkpoint.
                await context.CheckpointAsync();
                WriteHighlightedMessage(String.Format("Checkpointed partition: {0}", context.Lease.PartitionId));

                currentBlockInitOffset = long.Parse(context.Lease.Offset);
                stopwatch.Restart();
            }

            private void WriteHighlightedMessage(string message)
            {
                Console.ForegroundColor = ConsoleColor.Yellow;
                Console.WriteLine(message);
                Console.ResetColor();
            }
        }

    Diese Klasse wird von **EventProcessorHost** aufgerufen, um von IoT Hub empfangene D2C-Nachrichten zu verarbeiten. Die Hauptlogik für das zuverlässige Speichern von Nachrichten in einem Blob-Container und das Weiterleiten von interaktiven Nachrichten an die Service Bus-Warteschlange wird implementiert. Die `OpenAsync()`-Methode initialisiert die `currentBlockInitOffset`-Variable, mit der der aktuelle Offset der ersten Nachricht nachverfolgt wird, die von diesem Ereignisprozessor gelesen wird (beachten Sie, dass jeder Prozessor für eine einzelne Partition verantwortlich ist).

    Die `ProcessEventsAsync()`-Methode empfängt einen Batch mit Nachrichten von IoT Hub, die dann wie folgt verarbeitet werden. Interaktive Nachrichten werden an die Service Bus-Warteschlange gesendet, während Datenpunktnachrichten an das memoryBuffer-Element mit dem Namen `toAppend` angefügt werden. Falls der Arbeitsspeicherpuffer das Blocklimit erreicht (4 Mb) oder die Service Bus-Deduplizierungszeitfenster seit dem letzten Prüfpunkt abgelaufen sind (in diesem Lernprogramm eine Stunde), wird ein Prüfpunkt ausgelöst.

    Die `AppendAndCheckpoint()`-Methode generiert zuerst eine blockId für den Block, der angefügt werden soll. Da es für Azure-Speicher erforderlich ist, dass alle Block-IDs die gleiche Länge haben, wird der Offset mit führenden Nullen (`currentBlockInitOffset.ToString("0000000000000000000000000")`) aufgefüllt. Wenn ein Block mit dieser ID im Blob bereits vorhanden ist, überschreibt die Methode ihn mit dem aktuellen Block.

> [AZURE.NOTE]Um den Code zu vereinfachen, wird in diesem Lernprogramm eine einzelne Blob-Datei pro Partition zum Speichern von Nachrichten verwendet. Für eine echte Lösung würde das „File Rolling“ implementiert werden, indem zusätzliche Dateien erstellt werden, wenn sie eine bestimmte Größe erreichen (ein Azure-Blob kann höchstens 195 Gb aufweisen) oder wenn ein bestimmter Zeitraum verstrichen ist (z. B. `fileName_{partitionId}_{date-time}`).

7. Fügen Sie am Anfang der **Program**-Klasse die folgenden `using`-Anweisungen hinzu:

        using Microsoft.ServiceBus.Messaging;

    Ändern Sie anschließend die **Main**-Methode wie unten gezeigt in die **Program**-Klasse, und ersetzen Sie die IoT Hub-Verbindungszeichenfolge **iothubowner** (aus dem Lernprogramm [Erste Schritte mit IoT Hub]), die Speicherverbindungszeichenfolge und die Service Bus-Verbindungszeichenfolge durch **Sende**berechtigungen für die Warteschlange mit dem Namen **d2ctutorial**:

        static void Main(string[] args)
        {
            string iotHubConnectionString = "{iot hub connection string}";
            string iotHubD2cEndpoint = "messages/events";
            StoreEventProcessor.StorageConnectionString = "{storage connection string}";
            StoreEventProcessor.ServiceBusConnectionString = "{service bus send connection string}";

            string eventProcessorHostName = Guid.NewGuid().ToString();
            EventProcessorHost eventProcessorHost = new EventProcessorHost(eventProcessorHostName, iotHubD2cEndpoint, EventHubConsumerGroup.DefaultGroupName, iotHubConnectionString, StoreEventProcessor.StorageConnectionString, "messages-events");
            Console.WriteLine("Registering EventProcessor...");
            eventProcessorHost.RegisterEventProcessorAsync<StoreEventProcessor>().Wait();

            Console.WriteLine("Receiving. Press enter key to stop worker.");
            Console.ReadLine();
            eventProcessorHost.UnregisterEventProcessorAsync().Wait();
        }

> [AZURE.NOTE]In diesem Lernprogramm wird der Einfachheit halber eine einzelne Instanz von [EventProcessorHost] verwendet. Weitere Informationen zur Verarbeitung von D2C-Nachrichten finden Sie im [Programmierleitfaden für Event Hubs] und im Lernprogramm [Verarbeiten von D2C-Nachrichten].

## Empfangen von interaktiven Nachrichten
In diesem Abschnitt schreiben Sie eine Windows-Konsolen-App, die interaktive Nachrichten aus der Service Bus-Warteschlange empfängt. Weitere Informationen zum Aufbau einer Lösung mit Service Bus finden Sie unter [Erstellen von Anwendungen mit mehreren Ebenen mit Service Bus].

1. Erstellen Sie in der aktuellen Visual Studio-Projektmappe mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Desktop-App-Projekt. Geben Sie dem Projekt den Namen **ProcessD2cInteractiveMessages**.

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf die Projektmappe, und klicken Sie dann auf **NuGet-Pakete verwalten...**

    Daraufhin wird das Fenster „NuGet-Pakete verwalten“ angezeigt.

3. Suchen Sie nach `WindowsAzure.Service Bus`, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen.

    Daraufhin wird [Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) mit allen Abhängigkeiten heruntergeladen, installiert und mit einem Verweis versehen.

4. Fügen Sie am Anfang der Datei **Program.cs** die folgenden `using`-Anweisungen hinzu:

        using System.IO;
        using Microsoft.ServiceBus.Messaging;

5. Fügen Sie zuletzt der **Main**-Methode die folgenden Zeilen hinzu, und ersetzen Sie die Verbindungszeichenfolge durch Berechtigungen zum **Lauschen** für die Warteschlange mit dem Namen **d2ctutorial**:

        Console.WriteLine("Process D2C Interactive Messages app\n");

        string connectionString = "{service bus listen connection string}";
        QueueClient Client = QueueClient.CreateFromConnectionString(connectionString, "d2ctutorial");

        OnMessageOptions options = new OnMessageOptions();
        options.AutoComplete = false;
        options.AutoRenewTimeout = TimeSpan.FromMinutes(1);

        Client.OnMessage((message) =>
        {
            try
            {
                var bodyStream = message.GetBody<Stream>();
                bodyStream.Position = 0;
                var bodyAsString = new StreamReader(bodyStream, Encoding.ASCII).ReadToEnd();

                Console.WriteLine("Received message: {0} messageId: {1}", bodyAsString, message.MessageId);

                message.Complete();
            }
            catch (Exception)
            {
                message.Abandon();
            }
        }, options);

        Console.WriteLine("Receiving interactive messages from SB queue...");
        Console.WriteLine("Press any key to exit.");
        Console.ReadLine();

<!-- Links -->

[Informationen zu Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account

[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/

[Erste Schritte mit Event Hubs]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry

[Richtlinien zur Azure-Speicherskalierbarkeit]: ../storage/storage-scalability-targets.md

[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx

[Service Bus-Dokumentation]: ../service-bus/service-bus-dotnet-how-to-use-queues.md

[Event Hubs Overview]: ../event-hubs/event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3

[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx

[Programmierleitfaden für Event Hubs]: ../event-hubs/event-hubs-programming-guide.md

[Azure Preview Portal]: https://portal.azure.com/

[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx

[Azure-Verwaltungsportal]: https://manage.windowsazure.com/

[Service Bus Queue]: ../service-bus/service-bus-dotnet-how-to-use-queues.md


<!-- Images -->
[10]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-getstarted-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-getstarted-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-getstarted-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-getstarted-cloud-csharp/create-storage3.png

[30]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue2.png
[31]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue3.png
[32]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue4.png

<!---HONumber=Oct15_HO1-->