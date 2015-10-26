<properties 
	pageTitle="AMQP 1.0-Unterstützung für partitionierte Warteschlangen und Themen von Service Bus | Microsoft Azure" 
	description="Erfahren Sie, wie das Advanced Message Queuing Protocol 1.0 (AMQP) für partitionierte Warteschlangen und Themen von Service Bus verwendet wird." 
	services="service-bus" 
	documentationCenter=".net" 
	authors="hillaryc" 
	manager="hillaryc" 
	editor="hillaryc"/>

<tags 
	ms.service="service-bus" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="hillaryc"/>



# AMQP 1.0-Unterstützung für partitionierte Warteschlangen und Themen von Service Bus 

Azure Service Bus unterstützt jetzt das Advanced Message Queuing Protocol 1.0 (**AMQP**) für **partitionierte Warteschlangen und Themen** von Azure Service Bus.

**AMQP** ist ein Protokoll für Nachrichtenwarteschlangen nach einem offenen Standard, das die Entwicklung plattformübergreifender Anwendungen mit verschiedenen Programmiersprachen ermöglicht. Weitere Informationen über die allgemeine Service Bus-Unterstützung von AMQP finden Sie unter: [Unterstützung für AMQP 1.0 in Service Bus](service-bus-amqp-overview.md).

**Partitionierte Warteschlangen und Themen** werden auch als partitionierte Entitäten bezeichnet und bieten höhere Zuverlässigkeit, Verfügbarkeit und einen größeren Durchsatz als konventionelle nicht partitionierte Warteschlangen und Themen. Weitere Informationen zu partitionierten Entitäten finden Sie unter: [Partitionieren von Messagingentitäten](https://msdn.microsoft.com/library/azure/dn520246.aspx).

Durch das Hinzufügen von AMQP 1.0 als Protokoll zur Kommunikation mit partitionierten Warteschlangen und Themen können Kunden interoperable Anwendungen erstellen, die die höhere Verfügbarkeit, Zuverlässigkeit und den größeren Durchsatz der partitionierten Entitäten von Service Bus nutzen.

### Verwenden von AMQP mit partitionierten Warteschlangen

Warteschlangen sind hilfreich für Szenarien, die vorübergehende Entkopplung, Lastenausgleich, Belastungsausgleich und lose Kopplung erfordern. In einer Warteschlange senden Herausgeber Nachrichten an die Warteschlange, und Consumer empfangen Nachrichten aus der Warteschlange. Dabei kann eine Nachricht nur einmal empfangen werden. Ein klassisches Beispiel dafür ist ein Lagersystem, bei dem POS-Terminals (Point-of-Sale) Daten in einer Warteschlange statt direkt im Lagerverwaltungssystem veröffentlichen. Das Lagerverwaltungssystem kann dann die Daten jederzeit nutzen, um die Lagerauffüllung zu verwalten. Dies hat mehrere Vorteile, beispielsweise muss das Lagerverwaltungssystem nicht ständig online sein. Weitere Informationen zu Service Bus-Warteschlangen finden Sie hier: [Erstellen von Anwendungen, die Service Bus-Warteschlangen verwenden](https://msdn.microsoft.com/library/azure/hh689723.aspx)

Eine partitionierte Warteschlange würde die Verfügbarkeit, Zuverlässigkeit und den Durchsatz für Anwendungen weiter erhöhen, da diese Warteschlangen über mehrere Nachrichtenbroker und Messagingspeicher partitioniert werden.

#### Erstellen partitionierter Warteschlangen

Eine partitionierte Warteschlange kann über das Azure-Portal und das Service Bus-SDK erstellt werden. Um eine partitionierte Warteschlange zu erstellen, muss die EnablePartitioning-Eigenschaft in der QueueDescription-Instanz auf "true" festgelegt werden. Der folgende Codeausschnitt veranschaulicht das Erstellen mit dem Service Bus-SDK.
 
	// Create partitioned queue
	var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
	var queueDescription = new QueueDescription("myQueue");
	queueDescription.EnablePartitioning = true;
	nm.CreateQueue(queueDescription);

#### Senden und Empfangen von Nachrichten mithilfe von AMQP

Das Senden von Nachrichten an eine partitionierte Warteschlange und das Empfangen von Nachrichten von einer partitionierten Warteschlange mithilfe von AMQP als Protokoll wird durch Festlegen von "TransportType" auf "TransportType.Amqp" erreicht, wie im folgenden Codeausschnitt gezeigt.

	// Sending and receiving messages to and from a Queue
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


### Verwenden von AMQP mit partitionierten Themen

Ähnlich wie Warteschlangen sind Themen hilfreich für Szenarien, die vorübergehende Entkopplung, Lastenausgleich, Belastungsausgleich und lose Kopplung erfordern. Anders als Warteschlangen können Themen eine Kopie der gleichen Nachricht an mehrere Abonnenten weiterleiten. In einem Thema senden Herausgeber Nachrichten an das Thema, und Consumer erhalten Nachrichten von Abonnements. Im Beispiel des Lagersystems würden POS-Terminals Daten für das Thema veröffentlichen. Das Lagerverwaltungssystem würde dann Nachrichten aus einem Abonnement empfangen. Darüber hinaus kann ein Überwachungssystem dieselbe Nachricht aus einem anderen Abonnement erhalten. Weitere Informationen zu Service Bus-Themen finden Sie hier: [Erstellen von Anwendungen, die Service Bus-Themen und -Abonnements verwenden](https://msdn.microsoft.com/library/azure/hh699844.aspx)

Ein partitioniertes Thema würde die Verfügbarkeit, Zuverlässigkeit und den Durchsatz für Anwendungen weiter erhöhen, da diese Themen und ihre Abonnements über mehrere Nachrichtenbroker und Messagingspeicher partitioniert werden.

#### Erstellen partitionierter Themen

Ein partitioniertes Thema kann über das Azure-Portal und das Service Bus-SDK erstellt werden. Um ein partitioniertes Thema zu erstellen, muss die EnablePartitioning-Eigenschaft in der TopicDescription-Instanz auf "true" festgelegt werden. Der folgende Codeausschnitt veranschaulicht das Erstellen mit dem Service Bus-SDK.
	
	// Create partitioned topic
	var nm = NamespaceManager.CreateFromConnectionString(myConnectionString);
	var topicDescription = new TopicDescription("myTopic");
	topicDescription.EnablePartitioning = true;
	nm.CreateTopic(topicDescription);

	var subscriptionDescription = new SubscriptionDescription("myTopic", "mySubscription");
	nm.CreateSubscription(subscriptionDescription);

#### Senden und Empfangen von Nachrichten mithilfe von AMQP

Das Senden von Nachrichten an ein partitioniertes Thema und das Empfangen von Nachrichten von einem Abonnement eines partitionierten Themas mithilfe von AMQP als Protokoll wird durch Festlegen von "TransportType" auf "TransportType.Amqp" erreicht, wie im folgenden Codeausschnitt gezeigt.

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


## Referenzen

*    [Entitäten beim Partionierungsmessaging](https://msdn.microsoft.com/library/azure/dn520246.aspx)
*    [OASIS Advanced Message Queuing Protocol (AMQP) Version 1.0](http://docs.oasis-open.org/amqp/core/v1.0/os/amqp-core-complete-v1.0-os.pdf)
*    [Unterstützung für AMQP 1.0 in Service Bus](service-bus-amqp-overview.md)
*    [Entwicklerhandbuch für Service Bus AMQP]("https://msdn.microsoft.com/library/azure/jj841071.aspx")
*    [Verwenden der JMS (Java Message Service)-API mit Service Bus und AMQP 1.0](service-bus-java-how-to-use-jms-api-amqp.md)
*    [Verwenden von AMQP 1.0 mit der .NET-Programmierschnittstelle für Service Bus](service-bus-dotnet-advanced-message-queuing.md)

<!---HONumber=Oct15_HO3-->