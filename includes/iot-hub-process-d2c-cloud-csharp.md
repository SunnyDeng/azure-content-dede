## Verarbeiten von Device-to-Cloud (D2C)-Nachrichten

In diesem Abschnitt erstellen Sie eine Windows-Konsolen-App, die D2C-Nachrichten von IoT Hub verarbeitet. IoT Hub macht einen [Event Hubs]-kompatiblen Endpunkt verfügbar, der einer Anwendung das Lesen von D2C-Nachrichten ermöglicht. In diesem Lernprogramm wird die [EventProcessorHost]-Klasse zum Verarbeiten dieser Nachrichten in einer Konsolen-App verwendet. Weitere Informationen zum Verarbeiten von Nachrichten von Event Hubs finden Sie im Lernprogramm [Erste Schritte mit Event Hubs].

Die größte Herausforderung beim Implementieren der zuverlässigen Speicherung von Datenpunktnachrichten oder Weiterleiten von interaktiven Nachrichten besteht darin, dass für die Event Hubs-Ereignisverarbeitung der Nachrichtenconsumer seinen Status überprüfen muss. Darüber hinaus sollten Sie beim Lesen von Event Hubs Prüfpunkte für große Batches festlegen, um einen hohen Durchsatz zu erzielen. Hieraus ergibt sich die Möglichkeit einer doppelten Verarbeitung für eine große Zahl von Nachrichten, wenn ein Fehler vorliegt und Sie zum vorherigen Prüfpunkt zurückkehren. In diesem Lernprogramm wird veranschaulicht, wie Azure-Speicherschreibvorgänge und Service Bus-Deduplizierungsfenster mit **EventProcessorHost**-Prüfpunkten synchronisiert werden.

Um Nachrichten zuverlässig in den Azure-Speicher zu schreiben, wird im Beispiel das Feature „Individueller Block-Commit“ von [Blockblobs][Azure Block Blobs] verwendet. Der Ereignisprozessor akkumuliert Nachrichten im Arbeitsspeicher, bis es an der Zeit ist, einen Prüfpunktvorgang auszuführen (z. B. nachdem der akkumulierte Nachrichtenpuffer die maximale Blockgröße von 4 Mb erreicht hat oder nachdem das Service Bus-Deduplizierungszeitfenster abgelaufen ist). Vor dem Setzen von Prüfpunkten führt der Code für den Blob dann einen Commit für einen neuen Block durch.

Der Ereignisprozessor nutzt Event Hubs-Nachrichten-Offsets als Block-IDs. Dies ermöglicht eine Überprüfung der Deduplizierung vor dem Ausführen eines Commits für den neuen Block an den Speicher, und ein etwaiger Konflikt zwischen dem Commit eines Blocks und dem Prüfpunkt wird verhindert.

> [AZURE.NOTE]In diesem Lernprogramm wird ein einzelnes Speicherkonto zum Schreiben aller Nachrichten verwendet, die von IoT Hub abgerufen werden. Informationen zum Treffen der Entscheidung, ob Sie in Ihrer Lösung mehrere Azure-Speicherkonten benötigen, finden Sie unter [Richtlinien zur Azure-Speicherskalierbarkeit].

Die Anwendung nutzt die Service Bus-Deduplizierungsfunktion, um beim Verarbeiten interaktiver Nachrichten Duplikate zu vermeiden. Das simulierte Gerät versieht jede interaktive Nachricht mit einer eindeutigen **MessageId**, damit Service Bus sicherstellen kann, dass im angegebenen Zeitfenster für die Deduplizierung nicht zwei Nachrichten mit der gleichen **MessageId** an die Empfänger übertragen werden. Diese Deduplizierung sorgt zusammen mit der Abschlusssemantik pro Nachricht von Service Bus-Warteschlangen dafür, dass die zuverlässige Verarbeitung von interaktiven Nachrichten vereinfacht wird.

Um sicherzustellen, dass keine Nachrichten außerhalb des Deduplizierungsfensters erneut übermittelt werden, wird der **EventProcessorHost**-Prüfpunktmechanismus im Code mit dem Deduplizierungsfenster der Service Bus-Warteschlange synchronisiert. Hierzu wird mindestens einmal bei jedem Ablauf des Deduplizierungsfensters (in diesem Lernprogramm nach einer Stunde) ein Prüfpunkt erzwungen.

> [AZURE.NOTE]In diesem Lernprogramm wird eine einzelne partitionierte Service Bus-Warteschlange verwendet, um alle interaktiven Nachrichten zu verarbeiten, die von IoT Hub abgerufen werden. Weitere Informationen zur Verwendung von Service Bus-Warteschlangen, um die Skalierbarkeitsanforderungen Ihrer Lösung zu erfüllen, finden Sie unter [Service Bus-Dokumentation].

### Bereitstellen eines Azure-Speicherkontos und einer Service Bus-Warteschlange
Zum Verwenden der [EventProcessorHost]-Klasse müssen Sie über ein Azure-Speicherkonto verfügen, damit von **EventProcessorHost** Prüfpunktinformationen aufgezeichnet werden können. Sie können ein vorhandenes Speicherkonto verwenden oder mithilfe der Anweisungen unter [Informationen zu Azure Storage] ein neues Konto erstellen. Notieren Sie sich die Verbindungszeichenfolge für das Speicherkonto.

Sie benötigen außerdem eine Service Bus-Warteschlange, um die zuverlässige Verarbeitung von interaktiven Nachrichten zu aktivieren. Sie können eine Warteschlange programmgesteuert mit einem Deduplizierungsfenster von einer Stunde erstellen. Dies ist unter [Verwenden von Service Bus-Warteschlangen][Service Bus Queue] beschrieben. Oder führen Sie im [Azure-Portal] die folgenden Schritte aus:

1. Klicken Sie unten links auf **NEU** und dann auf **App Services**, **Service Bus**, **Warteschlange** und **Benutzerdefiniert erstellen**. Geben Sie den Namen **d2ctutorial** ein, wählen Sie eine Region, und verwenden Sie einen vorhandenen Namespace, oder erstellen Sie einen neuen. Wählen Sie auf der nächsten Seite dann **Doppelte Erkennung aktivieren**, und legen Sie **Fenster für Duplikaterkennungsverlauf-Zeitpunkt** auf eine Stunde fest. Klicken Sie anschließend auf das Häkchen, um die Konfiguration der Warteschlange zu speichern.

    ![][30]

2. Klicken Sie in der Liste mit den Service Bus-Warteschlangen auf **d2ctutorial** und dann auf **Konfigurieren**. Erstellen Sie zwei SAS-Richtlinien: eine mit dem Namen **send** und **Send**-Berechtigungen, und eine mit dem Namen **listen** mit **Listen**-Berechtigungen. Klicken Sie unten auf **Speichern**, wenn Sie fertig sind.

    ![][31]

3. Klicken Sie oben auf **Dashboard** und dann unten auf **Verbindungsinformationen**. Notieren Sie sich die beiden Verbindungszeichenfolgen.

    ![][32]

### Erstellen des Ereignisprozessors

1. Klicken Sie in der aktuellen Visual Studio-Projektmappe auf **Datei** und dann auf **Hinzufügen** und **Neues Projekt**, um mit der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Windows-Projekt zu erstellen. Geben Sie dem Projekt den Namen **ProcessDeviceToCloudMessages**.

    ![][10]

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ProcessDeviceToCloudMessages**, und klicken Sie dann auf **NuGet-Pakete verwalten**. Das Dialogfeld **NuGet-Paket-Manager** wird angezeigt.

3. Suchen Sie nach **WindowsAzure.ServiceBus**. klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen. Daraufhin wird das [Azure Service Bus-NuGet-Paket](https://www.nuget.org/packages/WindowsAzure.ServiceBus) mit allen Abhängigkeiten heruntergeladen und installiert, und dem Projekt wird ein Verweis auf das Paket hinzugefügt.

4. Suchen Sie nach **Microsoft Azure Service Bus Event Hub – EventProcessorHost**, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen. Daraufhin wird das NuGet-Paket [Azure Service Bus Event Hub - EventProcessorHost](https://www.nuget.org/packages/Microsoft.Azure.ServiceBus.EventProcessorHost) mit allen Abhängigkeiten heruntergeladen und installiert, und dem Projekt wird ein Verweis auf das Paket hinzugefügt.

5. Klicken Sie mit der rechten Maustaste auf das Projekt **ProcessDeviceToCloudMessages**, klicken Sie auf **Hinzufügen**, und klicken Sie dann auf **Klasse**. Geben Sie der neuen Klasse den Namen **StoreEventProcessor**, und klicken Sie auf **OK**, um die Klasse zu erstellen.

6. Fügen Sie die folgenden Anweisungen am Anfang der Datei "StoreEventProcessor.cs" hinzu:

    ```
    using System.IO;
    using System.Diagnostics;
    using System.Security.Cryptography;
    using Microsoft.ServiceBus.Messaging;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Blob;
    ```

7. Ersetzen Sie den Text der Klasse durch folgenden Code:

    ```
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
        blobContainer.CreateIfNotExists();
        queueClient = QueueClient.CreateFromConnectionString(ServiceBusConnectionString, "d2ctutorial");
      }

      Task IEventProcessor.CloseAsync(PartitionContext context, CloseReason reason)
      {
        Console.WriteLine("Processor Shutting Down. Partition '{0}', Reason: '{1}'.", context.Lease.PartitionId, reason);
        return Task.FromResult<object>(null);
      }

      Task IEventProcessor.OpenAsync(PartitionContext context)
      {
        Console.WriteLine("StoreEventProcessor initialized.  Partition: '{0}', Offset: '{1}'", context.Lease.PartitionId, context.Lease.Offset);

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
    ```

    Die **EventProcessorHost**-Klasse ruft diese Klasse auf, um von IoT Hub empfangene D2C-Nachrichten zu verarbeiten. Mit dem Code in dieser Klasse wird die Logik zum zuverlässigen Speichern von Nachrichten in einem Blobcontainer implementiert, und interaktive Nachrichten werden an die Service Bus-Warteschlange weitergeleitet. Die **OpenAsync**-Methode initialisiert die **currentBlockInitOffset**-Variable, mit der der aktuelle Offset der ersten Nachricht nachverfolgt wird, die von diesem Ereignisprozessor gelesen wird. Denken Sie daran, dass jeder Prozessor für eine bestimmte Partition verantwortlich ist.
    
    Mit der **ProcessEventsAsync**-Methode wird ein Nachrichtenbatch von IoT Hub empfangen und wie folgt verarbeitet: Interaktive Nachrichten werden an die Service Bus-Warteschlange gesendet, und Datenpunkt-Nachrichten werden an den Arbeitsspeicherpuffer mit dem Namen **toAppend** angehängt. Falls der Arbeitsspeicherpuffer das Blocklimit von 4 Mb erreicht oder die Service Bus-Deduplizierungszeitfenster seit dem letzten Prüfpunkt abgelaufen sind (in diesem Lernprogramm eine Stunde), wird ein Prüfpunkt ausgelöst.

    Mit der **AppendAndCheckpoint**-Methode wird zuerst eine „blockId“ für den anzufügenden Block generiert. Für den Azure-Speicher ist es erforderlich, dass alle Block-IDs die gleiche Länge haben. Die Methode füllt den Offset daher mit führenden Nullen auf: `currentBlockInitOffset.ToString("0000000000000000000000000")`. Wenn ein Block mit dieser ID im Blob bereits vorhanden ist, überschreibt die Methode ihn mit dem aktuellen Inhalt des Puffers.

    > [AZURE.NOTE]Um den Code zu vereinfachen, wird in diesem Lernprogramm eine einzelne Blob-Datei pro Partition zum Speichern von Nachrichten verwendet. Für eine echte Lösung würde das „File Rolling“ implementiert werden, indem zusätzliche Dateien erstellt werden, wenn sie eine bestimmte Größe erreichen (ein Azure-Blockblob kann maximal 195 Gb groß sein) oder wenn ein bestimmter Zeitraum verstrichen ist.

8. Fügen Sie am Anfang der **Program**-Klasse die folgenden **using**-Anweisungen hinzu:

    ```
    using Microsoft.ServiceBus.Messaging;
    ```

9. Ändern Sie die **Main**-Methode wie unten gezeigt in der **Program**-Klasse, und ersetzen Sie die IoT Hub-Verbindungszeichenfolge **iothubowner** (aus dem Lernprogramm [Erste Schritte mit IoT Hub]), die Speicherverbindungszeichenfolge und die Service Bus-Verbindungszeichenfolge durch **Send**-Berechtigungen für die Warteschlange mit dem Namen **d2ctutorial**:

    ```
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
    ```
    
    > [AZURE.NOTE]Der Einfachheit halber wird in diesem Lernprogramm eine einzelne Instanz der [EventProcessorHost]-Klasse verwendet. Weitere Informationen finden Sie im [Programmierleitfaden für Event Hubs].

## Empfangen von interaktiven Nachrichten
In diesem Abschnitt schreiben Sie eine Windows-Konsolen-App, die interaktive Nachrichten aus der Service Bus-Warteschlange empfängt. Weitere Informationen zum Aufbau einer Lösung mit Service Bus finden Sie im Artikel zum [Erstellen von Anwendungen mit mehreren Ebenen mit Service Bus][].

1. Erstellen Sie in der aktuellen Visual Studio-Projektmappe mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Windows-Projekt. Geben Sie dem Projekt den Namen **ProcessD2CInteractiveMessages**.

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt **ProcessD2CInteractiveMessages**, und klicken Sie dann auf **NuGet-Pakete verwalten**. Das Fenster **NuGet-Paket-Manager** wird angezeigt.

3. Suchen Sie nach **WindowsAzure.ServiceBus**, klicken Sie auf **Installieren**, und akzeptieren Sie die Nutzungsbedingungen. Daraufhin wird [Azure Service Bus](https://www.nuget.org/packages/WindowsAzure.ServiceBus) mit allen Abhängigkeiten heruntergeladen, installiert und mit einem Verweis versehen.

4. Fügen Sie die folgende **using**-Anweisung am Anfang der Datei **Program.cs** hinzu:

    ```
    using System.IO;
    using Microsoft.ServiceBus.Messaging;
    ```

5. Fügen Sie zuletzt der **Main**-Methode die folgenden Zeilen hinzu, und ersetzen Sie die Verbindungszeichenfolge durch Berechtigungen zum **Lauschen** für die Warteschlange mit dem Namen **d2ctutorial**:

    ```
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
    ```

<!-- Links -->
[Informationen zu Azure Storage]: ../storage/storage-create-storage-account.md#create-a-storage-account
[Azure IoT - Service SDK NuGet package]: https://www.nuget.org/packages/Microsoft.Azure.Devices/
[Erste Schritte mit Event Hubs]: ../event-hubs/event-hubs-csharp-ephcs-getstarted.md
[IoT Hub Developer Guide - Identity Registry]: iot-hub-devguide.md#identityregistry
[Richtlinien zur Azure-Speicherskalierbarkeit]: ../storage/storage-scalability-targets.md
[Azure Block Blobs]: https://msdn.microsoft.com/library/azure/ee691964.aspx
[Event Hubs]: ../event-hubs/event-hubs-overview.md
[Scaled out event processing]: https://code.msdn.microsoft.com/windowsazure/Service-Bus-Event-Hub-45f43fc3
[EventProcessorHost]: http://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.eventprocessorhost(v=azure.95).aspx
[Programmierleitfaden für Event Hubs]: ../event-hubs/event-hubs-programming-guide.md
[Azure preview portal]: https://portal.azure.com/
[Transient Fault Handling]: https://msdn.microsoft.com/library/hh680901(v=pandp.50).aspx
[Azure-Portal]: https://manage.windowsazure.com/
[Service Bus Queue]: ../service-bus/service-bus-dotnet-how-to-use-queues.md
[Erstellen von Anwendungen mit mehreren Ebenen mit Service Bus]: ../service-bus/service-bus-dotnet-multi-tier-app-using-service-bus-queues.md
[Erste Schritte mit IoT Hub]: iot-hub-csharp-csharp-getstarted.md
[Service Bus-Dokumentation]: https://azure.microsoft.com/documentation/services/service-bus/

<!-- Images -->
[10]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp1.png
[12]: ./media/iot-hub-process-d2c-cloud-csharp/create-identity-csharp3.png

[20]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage1.png
[21]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage2.png
[22]: ./media/iot-hub-process-d2c-cloud-csharp/create-storage3.png

[30]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue2.png
[31]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue3.png
[32]: ./media/iot-hub-process-d2c-cloud-csharp/createqueue4.png

<!---HONumber=AcomDC_0107_2016-->