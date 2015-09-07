<properties 
   pageTitle="Übersicht über die Event Hubs-API"
	description="Eine Zusammenfassung einiger wichtiger .NET-Client-APIs für Event Hubs."
	services="event-hubs"
	documentationCenter="na"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>
<tags 
   ms.service="event-hubs"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="tbd"
	ms.date="07/10/2015"
	ms.author="sethm"/>

# Übersicht über die Event Hubs-API

In diesem Artikel werden einige der wichtigsten .NET-Client-APIs von Event Hubs zusammengefasst. Es gibt zwei Kategorien: Verwaltungs- und Laufzeit-APIs. Runtime-APIs umfassen alle Vorgänge, die zum Senden und Empfangen einer Meldung erforderlich sind. Verwaltungsvorgänge ermöglichen Ihnen das Verwalten des Entitätszustands von Event Hubs durch das Erstellen, Aktualisieren und Löschen von Entitäten.

Überwachungsszenarien umfassen sowohl Verwaltung als auch Laufzeit. Eine ausführliche Referenzdokumentation zu den .NET-APIs finden Sie unter den Referenzen zur [.NET-Klassenbibliothek](https://msdn.microsoft.com/library/jj933431.aspx) und zur [EventProcessorHost-API](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.aspx).

## Verwaltungs-API

Zum Ausführen der folgenden Verwaltungsvorgänge benötigen Sie **Verwaltungsberechtigungen** für den Service Bus-Namespace:

### Erstellen

```
// Create the Event Hub
EventHubDescription ehd = new EventHubDescription(eventHubName);
ehd.PartitionCount = SampleManager.numPartitions;
namespaceManager.CreateEventHubAsync(ehd).Wait();
```

### Aktualisieren

```
EventHubDescription ehd = await namespaceManager.GetEventHubAsync(eventHubName);

// Create a customer SAS rule with Manage permissions
ehd.UserMetadata = "Some updated info";
string ruleName = "myeventhubmanagerule";
string ruleKey = SharedAccessAuthorizationRule.GenerateRandomKey();
ehd.Authorization.Add(new SharedAccessAuthorizationRule(ruleName, ruleKey, new AccessRights[] {AccessRights.Manage, AccessRights.Listen, AccessRights.Send} )); 
namespaceManager.UpdateEventHubAsync(ehd).Wait();
```

### Löschen

```
namespaceManager.DeleteEventHubAsync("event hub name").Wait();
```

## Laufzeit-APIs

### Herausgeber erstellen

```
// EventHubClient model (uses implicit factory instance, so all links on same connection)
EventHubClient eventHubClient = EventHubClient.Create("event hub name");
```

### Meldung veröffentlichen

```
// Create the device/temperature metric
MetricEvent info = new MetricEvent() { DeviceId = random.Next(SampleManager.NumDevices), Temperature = random.Next(100) };
EventData data = new EventData(new byte[10]); // Byte array
EventData data = new EventData(Stream); // Stream 
EventData data = new EventData(info, serializer) //Object and serializer 
    {
       PartitionKey = info.DeviceId.ToString()
    };

// Set user properties if needed
data.Properties.Add("Type", "Telemetry_" + DateTime.Now.ToLongTimeString());

// Send single message async
await client.SendAsync(data);
```

### Consumer erstellen

```
// Create the Event Hub client
EventHubClient eventHubClient = EventHubClient.Create(EventHubName);

// Get the default subscriber group
EventHubSubscriberGroup defaultSubscriberGroup = eventHubClient.GetDefaultSubscriberGroup();

// All messages
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index);

// From one day ago
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index, startingDateTimeUtc:DateTime.Now.AddDays(-1));
                        
// From specific offset, -1 means oldest
EventHubReceiver consumer = await defaultConsumerGroup.CreateReceiverAsync(shardId: index,startingOffset:-1); 
```

### Meldung verarbeiten

```
var message = await consumer.ReceiveAsync();

// Provide a serializer
var info = message.GetBody<Type>(Serializer)
                                    
// Get a byte[]
var info = message.GetBytes(); 
msg = UnicodeEncoding.UTF8.GetString(info);
```

## Ereignisprozessorhost-APIs

Diese APIs bieten Flexibilität für Arbeitsprozesse, die möglicherweise nicht mehr verfügbar sind, jedoch Shards auf verfügbare Worker aufteilen.

```
// Checkpointing is done within the SimpleEventProcessor and on a per-consumerGroup per-partition basis, workers resume from where they last left off.
// Use the EventData.Offset value for checkpointing yourself, this value is unique per partition.
string eventHubConnectionString = System.Configuration.ConfigurationManager.AppSettings["Microsoft.ServiceBus.ConnectionString"];
string blobConnectionString = System.Configuration.ConfigurationManager.AppSettings["AzureStorageConnectionString"]; // Required for checkpoint/state

EventHubDescription eventHubDescription = new EventHubDescription(EventHubName);
EventProcessorHost host = new EventProcessorHost(WorkerName, EventHubName, defaultConsumerGroup.GroupName, eventHubConnectionString, blobConnectionString);
            host.RegisterEventProcessorAsync<SimpleEventProcessor>();

// To close
host.UnregisterEventProcessorAsync().Wait();   
```

Die[IEventProcessor](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.ieventprocessor.aspx)-Schnittstelle wird wie folgt definiert:

```
public class SimpleEventProcessor : IEventProcessor
{
    IDictionary<string, string> map;
    PartitionContext partitionContext;

    public SimpleEventProcessor()
    {
        this.map = new Dictionary<string, string>();
    }

    public Task OpenAsync(PartitionContext context)
    {
        this.partitionContext = context;

        return Task.FromResult<object>(null);
    }

    public async Task ProcessEventsAsync(PartitionContext context, IEnumerable<EventData> messages)
    {
        foreach (EventData message in messages)
        {
            Process messages here
        }
        
        // Checkpoint when appropriate
        await context.CheckpointAsync();

    }


    public async Task CloseAsync(PartitionContext context, CloseReason reason)
    {
        if (reason == CloseReason.Shutdown)
        {
            await context.CheckpointAsync();
        }
    }
}
```

## Nächste Schritte

Weitere Informationen zu Event Hubs-Szenarien finden Sie unter diesen Links:

- [Programmierleitfaden für Event Hubs](event-hubs-programming-guide.md)
- [Übersicht über Event Hubs](event-hubs-overview.md)
- [Codebeispiele für Event Hubs](http://code.msdn.microsoft.com/site/search?query=event hub&f[0].Value=event hub&f[0].Type=SearchText&ac=5)

Die .NET-API-Referenzen sind hier verfügbar:

- [Referenz zu Service Bus und .NET-API für Event Hubs](https://msdn.microsoft.com/library/jj933424.aspx)
- [Referenz zur Ereignisprozessorhost-API](https://msdn.microsoft.com/library/microsoft.servicebus.messaging.eventprocessorhost.aspx)

<!---HONumber=August15_HO9-->