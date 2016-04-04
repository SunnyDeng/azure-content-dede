<properties 
	pageTitle="AMQP 1.0-Unterstützung für partitionierte Warteschlangen und Themen von Service Bus | Microsoft Azure" 
	description="Hier erfahren Sie, wie das Advance Message Queuing Protocol 1.0 (AMQP) für partitionierte Warteschlangen und Themen von Service Bus verwendet wird." 
	services="service-bus" 
	documentationCenter=".net" 
	authors="hillaryc" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="service-bus" 
	ms.workload="na" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="03/16/2016" 
	ms.author="hillaryc;sethm"/>

# AMQP 1.0-Unterstützung für partitionierte Warteschlangen und Themen von Service Bus 

Azure Service Bus unterstützt jetzt das Advanced Message Queuing Protocol 1.0 (**AMQP**) für **partitionierte Warteschlangen und Themen** von Service Bus.

**AMQP** ist ein Protokoll für Nachrichtenwarteschlangen nach einem offenen Standard, das die Entwicklung plattformübergreifender Anwendungen mit verschiedenen Programmiersprachen ermöglicht. Allgemeine Informationen zur Unterstützung von AMQP in Service Bus finden Sie unter [Unterstützung für AMQP 1.0 in Service Bus](service-bus-amqp-overview.md).

**Partitionierte Warteschlangen und Themen** werden auch als *partitionierte Entitäten* bezeichnet und bieten höhere Zuverlässigkeit, Verfügbarkeit und einen größeren Durchsatz als konventionelle nicht partitionierte Warteschlangen und Themen. Weitere Informationen zu partitionierten Entitäten finden Sie unter [Partitionierte Messagingentitäten](service-bus-partitioning.md).

Da AMQP 1.0 als Protokoll für die Kommunikation mit partitionierten Warteschlangen und Themen hinzugefügt wurde, können Sie interoperable Anwendungen erstellen, die die höhere Verfügbarkeit, Zuverlässigkeit und den größeren Durchsatz der partitionierten Entitäten von Service Bus nutzen.

## Verwenden von AMQP mit partitionierten Warteschlangen

Warteschlangen sind hilfreich für Szenarien, die vorübergehende Entkopplung, Lastenausgleich, Belastungsausgleich und lose Kopplung erfordern. Bei Verwendung von Warteschlangen senden Herausgeber Nachrichten an die Warteschlange, und Consumer empfangen Nachrichten aus der Warteschlange. Dies gilt für Situationen, in denen eine Nachricht nur einmal empfangen werden kann. Ein gutes Beispiel dafür ist ein Lagersystem, bei dem POS-Terminals (Point-of-Sale) Daten in einer Warteschlange veröffentlichen, anstatt direkt im Lagerverwaltungssystem. Das Lagerverwaltungssystem kann die Daten dann jederzeit nutzen, um die Lagerauffüllung zu verwalten. Dies hat mehrere Vorteile. Beispielsweise muss das Lagerverwaltungssystem nicht ständig online sein. Weitere Informationen zu Service Bus-Warteschlangen finden Sie unter [Erstellen von Anwendungen, die Service Bus-Warteschlangen verwenden](service-bus-create-queues.md).

Eine partitionierte Warteschlange erhöht die Verfügbarkeit, Zuverlässigkeit und den Durchsatz für Anwendungen zusätzlich, da diese Warteschlangen über mehrere Nachrichtenbroker und Messagingspeicher partitioniert werden.

### Erstellen von partitionierten Warteschlangen

Sie können eine partitionierte Warteschlange mit dem [klassischen Azure-Portal][] und dem Service Bus SDK erstellen. Um eine partitionierte Warteschlange zu erstellen, legen Sie die [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx)-Eigenschaft in der [QueueDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx)-Instanz auf **true** fest. Der folgende Code veranschaulicht, wie Sie mit dem Service Bus SDK eine partitionierte Warteschlange erstellen.
 
```
// Create partitioned queue
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var queueDescription = new QueueDescription("myQueue");
queueDescription.EnablePartitioning = true;
nm.CreateQueue(queueDescription);
```

### Senden und Empfangen von Nachrichten mit AMQP

Sie können Nachrichten an eine partitionierte Warteschlange senden und von dieser empfangen, indem Sie AMQP als Protokoll verwenden. Legen Sie hierzu wie im folgenden Codeausschnitt die [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx)-Eigenschaft auf [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) fest.

```
// Sending and receiving messages to and from a queue
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
var queueClient = QueueClient.CreateFromConnectionString(amqpConnectionString, "myQueue");

BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
queueClient.Send(message);

var receivedMessage = queueClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## Verwenden von AMQP mit partitionierten Themen

Themen liegt ein ähnliches Konzept zugrunde wie Warteschlangen, allerdings können Themen eine Kopie der gleichen Nachricht an mehrere Abonnenten weiterleiten. Bei einem Thema senden Herausgeber Nachrichten an das Thema, und Consumer erhalten Nachrichten über *Abonnements*. Im Szenario mit dem Lagersystem veröffentlichen POS-Terminals Daten für das Thema. Das Lagerverwaltungssystem empfängt dann Nachrichten von einem Abonnement. Darüber hinaus kann ein Überwachungssystem dieselbe Nachricht aus einem anderen Abonnement erhalten. Weitere Informationen zu Service Bus-Themen und Abonnements finden Sie unter [Erstellen von Anwendungen, die Service Bus-Themen und -Abonnements verwenden](service-bus-create-topics-subscriptions.md).

Ein partitioniertes Thema erhöht die Verfügbarkeit, Zuverlässigkeit und den Durchsatz für Anwendungen zusätzlich, da diese Themen und ihre Abonnements über mehrere Nachrichtenbroker und Messagingspeicher partitioniert werden.

### Erstellen von partitionierten Themen

Partitionierte Themen können mit dem [klassischen Azure-Portal][] und mit dem Service Bus SDK erstellt werden. Legen Sie die [EnablePartitioning](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx)-Eigenschaft in der [TopicDescription](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.aspx)-Instanz auf **true** fest, um ein partitioniertes Thema zu erstellen. Der folgende Code veranschaulicht, wie Sie mit dem Service Bus SDK ein partitioniertes Thema erstellen.
	
```
// Create partitioned topic
var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
var topicDescription = new TopicDescription("myTopic");
topicDescription.EnablePartitioning = true;
nm.CreateTopic(topicDescription);

var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
nm.CreateSubscription(subscriptionDescription);
```

### Senden und Empfangen von Nachrichten mit AMQP

Sie können Nachrichten an ein partitioniertes Themenabonnement senden und von diesem empfangen, indem Sie AMQP als Protokoll verwenden. Legen Sie hierzu wie im folgenden Code die Eigenschaft [TransportType](https://msdn.microsoft.com/library/azure/microsoft.servicebus.servicebusconnectionstringbuilder.transporttype.aspx) auf [TransportType.Amqp](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.transporttype.aspx) fest.

```
// Sending and receiving messages to a topic and from a subscription
var myConnectionStringBuilder = new ServiceBusConnectionStringBuilder(myConnectionString);
myConnectionStringBuilder.TransportType = TransportType.Amqp;
string amqpConnectionString = myConnectionStringBuilder.ToString();
	
var topicClient = TopicClient.CreateFromConnectionString(amqpConnectionString, "myTopic");
BrokeredMessage message = new BrokeredMessage("Hello AMQP");
Console.WriteLine("Sending message {0}...", message.MessageId);
topicClient.Send(message);
	
var subcriptionClient = SubscriptionClient.CreateFromConnectionString(amqpConnectionString, "myTopic", "mySubscription");
var receivedMessage = subcriptionClient.Receive();
Console.WriteLine("Received message: {0}", receivedMessage.GetBody<string>());
receivedMessage.Complete();
```

## Nächste Schritte

Weitere Informationen zu partitionierten Messagingentitäten finden Sie in den folgenden Abschnitten:

*    [Partitionierte Nachrichtenentitäten](service-bus-partitioning.md)
*    [OASIS Advanced Message Queuing Protocol (AMQP) Version 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Unterstützung für AMQP 1.0 in Service Bus](service-bus-amqp-overview.md)
*    [Verwenden der JMS (Java Message Service)-API mit Service Bus und AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Verwenden von AMQP 1.0 mit der .NET-Programmierschnittstelle für Service Bus](service-bus-dotnet-advanced-message-queuing.md)

[klassischen Azure-Portal]: http://manage.windowsazure.com

<!---HONumber=AcomDC_0323_2016-->