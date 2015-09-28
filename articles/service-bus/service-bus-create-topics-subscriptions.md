<properties 
   pageTitle="Erstellen von Anwendungen, die Service Bus-Themen und -Abonnements verwenden | Microsoft Azure"
   description="Einführung in die Funktionen zum Veröffentlichen/Abonnieren von Service Bus-Themen und -Abonnements."
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="09/16/2015"
   ms.author="sethm" />

# Erstellen von Anwendungen, die Service Bus-Themen und -Abonnements verwenden

Azure Service Bus unterstützt einen Satz cloudbasierter, nachrichtenorientierter Middlewaretechnologien, darunter zuverlässiges Message Queuing und dauerhaftes Veröffentlichungs-/Abonnementmessaging. Dieser Artikel baut auf den Informationen des Artikels [Erstellen von Anwendungen, die Service Bus-Warteschlangen verwenden](service-bus-create-queues.md) auf und enthält eine Einführung in die Funktionen zum Veröffentlichen/Abonnieren, die von Service Bus-Themen bereitgestellt werden.

## Weiterentwickeln des Einzelhandelsszenarios

In diesem Artikel wird das Einzelhandelsszenario fortgeführt, das bereits unter [Erstellen von Anwendungen, die Service Bus-Warteschlangen verwenden](service-bus-create-queues.md) verwendet wurde. Wie Sie sich sicherlich erinnern, müssen Verkaufsdaten aus einzelnen Point-of-Sale (POS)-Terminals an ein Lagerverwaltungssystem weitergeleitet werden, in dem anhand dieser Daten festgestellt wird, ob Bestände aufgefüllt werden müssen. Jedes POS-Terminal meldet seine Verkaufsdaten durch Senden von Nachrichten an die **DataCollectionQueue**-Warteschlange, in der die Nachrichten verbleiben, bis sie vom Lagerverwaltungssystem empfangen werden (siehe Abbildung):

![Service-Bus1](./media/service-bus-create-topics-subscriptions/IC657161.gif)

Zur Weiterentwicklung dieses Szenarios wurde dem System eine neue Anforderung hinzugefügt: Der Ladenbesitzer möchte die Möglichkeit haben, die Geschäftsentwicklung in Echtzeit zu überwachen.

Damit diese Anforderung umgesetzt werden kann, muss das System den Datenstrom der Verkaufsdaten "überwachen". Nach wie vor soll jede von den POS-Terminals gesendete Nachricht an das Lagerverwaltungssystem gesendet werden. Von jeder Nachricht soll jedoch auch eine Kopie erstellt werden, die dazu verwendet werden kann, dem Ladenbesitzer die Dashboardansicht zu präsentieren.

In einem solchen Fall, bei dem jede Nachricht von mehreren Komponenten genutzt werden soll, können Sie Service Bus-*Themen* verwenden. Themen umfassen ein Veröffentlichungs-und Abonnementmuster, mit dem jede veröffentlichte Nachricht für die Abonnements zur Verfügung gestellt wird, die für das entsprechende Thema registriert sind. Im Gegensatz dazu wird bei Warteschlangen jede Nachricht von einem einzigen Consumer empfangen.

Nachrichten werden auf die gleiche Weise an ein Thema gesendet wie an eine Warteschlange. Die Nachrichten werden jedoch nicht direkt vom jeweiligen Thema, sondern stattdessen von Abonnements empfangen. Sie können sich ein Abonnement für ein Thema als virtuelle Warteschlange vorstellen, die Kopien der Nachrichten empfängt, die an das Thema gesendet wurden. Nachrichten werden von einem Abonnement auf die gleiche Weise empfangen wie von einer Warteschlange.

Zurück zum Einzelhandelsszenario: Die Warteschlange wird durch ein Thema ersetzt. Zudem wird ein Abonnement hinzugefügt, das vom Lagerverwaltungssystem verwendet wird. Das System sieht nun folgendermaßen aus:

![Service-Bus2](./media/service-bus-create-topics-subscriptions/IC657165.gif)

Diese Konfiguration entspricht in der Ausführung dem vorherigen warteschlangenbasierten Entwurf. Das heißt, die an das Thema gesendeten Nachrichten werden an das Abonnement **Inventory** weitergeleitet, aus dem das **Lagerverwaltungssystem** die Nachrichten abruft.

Damit das Management-Dashboard unterstützt wird, muss wie in der Abbildung gezeigt ein zweites Abonnement für das Thema erstellt werden:

![Service-Bus3](./media/service-bus-create-topics-subscriptions/IC657166.gif)

Bei dieser Konfiguration wird jede Nachricht von den POS-Terminals sowohl dem Abonnement **Dashboard** als auch dem Abonnement **Inventory** zur Verfügung gestellt.

## Anwendungscode

Unter [Erstellen von Anwendungen, die Service Bus-Warteschlangen verwenden](service-bus-create-queues.md) wird beschrieben, wie Sie sich für ein Service Bus-Konto anmelden und einen Dienstnamespace erstellen. Zur Verwendung eines Service Bus-Namespaces muss eine Anwendung auf die Service Bus-Assembly verweisen, d. h. auf "Microsoft.ServiceBus.dll". Die einfachste Möglichkeit zum Verweisen auf Service Bus-Abhängigkeiten besteht darin, das [NuGet-Paket](https://www.nuget.org/packages/WindowsAzure.ServiceBus/) "Service Bus" zu installieren. Die Assembly ist auch Bestandteil des Azure SDK und kann auf der [Azure SDK-Downloadseite](http://azure.microsoft.com/downloads/) heruntergeladen werden.

### Erstellen des Themas und der Abonnements

Verwaltungsvorgänge für Service Bus-Nachrichtenentitäten (Warteschlangen und Veröffentlichen/Abonnieren von Themen) werden über die [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)-Klasse ausgeführt. Für die Erstellung einer [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)-Instanz für einen bestimmten Dienstnamespace werden gültige Anmeldeinformationen benötigt. Service Bus verwendet ein [SAS (Shared Access Signature)](service-bus-sas-overview.md)-basiertes Sicherheitsmodell. Die [TokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.aspx)-Klasse stellt einen Sicherheitstokenanbieter mit integrierten Factorymethoden dar, die einige bekannte Tokenanbieter zurückgeben. Wir verwenden die [CreateSharedAccessSignatureTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.tokenprovider.createsharedaccesssignaturetokenprovider.aspx)-Methode, um die SAS-Anmeldeinformationen zu speichern. Die [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)-Instanz wird dann mit der Basisadresse des Service Bus-Namespaces und dem Tokenanbieter erstellt.

Die [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)-Klasse enthält Methoden zum Erstellen, Aufzählen und Löschen von Nachrichtenentitäten. Mit dem folgenden Code wird gezeigt, wie die [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)-Instanz erstellt und zum Erstellen des Themas **DataCollectionTopic** verwendet wird.

```
Uri uri = ServiceBusEnvironment.CreateServiceUri("sb", "test-blog", string.Empty);
string name = "RootManageSharedAccessKey";
string key = "abcdefghijklmopqrstuvwxyz";
     
TokenProvider tokenProvider = TokenProvider.CreateSharedAccessSignatureTokenProvider(name, key);
NamespaceManager namespaceManager = new NamespaceManager(uri, tokenProvider);
 
namespaceManager.CreateTopic("DataCollectionTopic");
```

Beachten Sie die Überladungen der [CreateTopic](https://msdn.microsoft.com/library/azure/hh293080.aspx)-Methode, mit denen die Eigenschaften des Themas festgelegt werden können. So können Sie beispielsweise den Standardwert für die Gültigkeitsdauer (TTL) für an das Thema gesendete Nachrichten festlegen. Fügen Sie als Nächstes die Abonnements **Inventory** und **Dashboard** hinzu.

```
namespaceManager.CreateSubscription("DataCollectionTopic", "Inventory");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard");
```

### Senden von Nachrichten an das Thema

Für Vorgänge zur Laufzeit für Service Bus-Entitäten, z. B. Senden und Empfangen von Nachrichten, muss eine Anwendung zunächst ein [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx)-Objekt erstellen. Ähnlich wie die [NamespaceManager](https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx)-Klasse wird die [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx)-Instanz aus der Basisadresse des Dienstnamespaces und dem Tokenanbieter erstellt.

```
MessagingFactory factory = MessagingFactory.Create(uri, tokenProvider);
```

Die Nachrichten, die an die Service Bus-Themen gesendet und von diesen empfangen werden, sind Instanzen der [BrokeredMessage](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx)-Klasse. Diese Klasse besteht aus einem Satz von Standardeigenschaften (z. B. [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx) und [TimeToLive](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx)), einem Wörterbuch, in dem Anwendungseigenschaften enthalten sind, und einem Bestand beliebiger Anwendungsdaten. Eine Anwendung kann durch Übergeben eines beliebigen serialisierbaren Objekts den Bestand festlegen (im folgenden Beispiel wird ein **SalesData**-Objekt übergeben, das die Verkaufsdaten vom POS-Terminal darstellt), der mithilfe von [DataContractSerializer](https://msdn.microsoft.com/library/azure/system.runtime.serialization.datacontractserializer.aspx) das Objekt serialisiert. Alternativ kann ein [Stream](https://msdn.microsoft.com/library/azure/system.io.stream.aspx)-Objekt angegeben werden.

```
BrokeredMessage bm = new BrokeredMessage(salesData);
bm.Label = "SalesReport";
bm.Properties["StoreName"] = "Redmond";
bm.Properties["MachineID"] = "POS_1";
```

Die einfachste Möglichkeit zum Senden von Nachrichten an das Thema ist die Verwendung von [CreateMessageSender](https://msdn.microsoft.com/library/azure/hh322659.aspx) zum Erstellen eines [MessageSender](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx)-Objekts direkt aus der [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx)-Instanz.

```
MessageSender sender = factory.CreateMessageSender("DataCollectionQueue");
sender.Send(bm);
```

### Empfangen von Nachrichten aus einem Abonnement

Wie beim Verwenden von Warteschlangen können Sie zum Empfangen von Nachrichten aus einem Abonnement ein [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx)-Objekt verwenden, das Sie unter Verwendung von [CreateMessageReceiver](https://msdn.microsoft.com/library/azure/hh322642.aspx) direkt aus [MessagingFactory](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx) erstellen. Sie können einen der beiden Empfangsmodi (**ReceiveAndDelete** und **PeekLock**) verwenden. Die entsprechenden Informationen finden Sie unter [Erstellen von Anwendungen, die Service Bus-Warteschlangen verwenden](service-bus-create-queues.md).

Wenn Sie ein [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx)-Objekt für Abonnements erstellen, hat der *entityPath*-Parameter das Format `topicPath/subscriptions/subscriptionName`. Zum Erstellen einer [MessageReceiver](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx)-Instanz für das Abonnement **Inventory** des Themas **DataCollectionTopic** muss *entityPath* daher auf `DataCollectionTopic/subscriptions/Inventory` festgelegt werden. Der Code sieht wie folgt aus:

```
MessageReceiver receiver = factory.CreateMessageReceiver("DataCollectionTopic/subscriptions/Inventory");
BrokeredMessage receivedMessage = receiver.Receive();
try
{
    ProcessMessage(receivedMessage);
    receivedMessage.Complete();
}
catch (Exception e)
{
    receivedMessage.Abandon();
}
```

## Abonnementfilter

Bislang werden alle Nachrichten, die an das Thema gesendet wurden, allen registrierten Abonnements zur Verfügung gestellt. Der Kernpunkt lautet "zur Verfügung gestellt". Auch wenn für Service Bus-Abonnements alle Nachrichten angezeigt werden, die an das Thema gesendet wurden, können Sie nur eine Teilmenge dieser Nachrichten in die virtuelle Abonnementwarteschlange kopieren. Dies erfolgt mithilfe von *Abonnementfiltern*. Wenn Sie ein Abonnement erstellen, können Sie einen Filterausdruck angeben, der das Format eines SQL92-Prädikats hat und auf die Eigenschaften der Nachricht angewendet wird, und zwar sowohl auf die Systemeigenschaften (z. B. [Label](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx)) als auch auf die Anwendungseigenschaften (z. B. **StoreName** aus dem vorherigen Beispiel).

Das Szenario soll erweitert werden, um dies zu veranschaulichen. Dazu wird dem Einzelhandelsszenario ein zweites Geschäft hinzugefügt. Weiterhin müssen die Verkaufsdaten von allen POS-Terminals aus beiden Geschäften an das zentrale Lagerverwaltungssystem weitergeleitet werden, aber ein Geschäftsführer, der mit dem Dashboardtool arbeitet, ist nur an der Geschäftsentwicklung des zweiten Ladens interessiert. Dies können Sie durch Filterung der Abonnements erreichen. Wenn die POS-Terminals Nachrichten veröffentlichen, wird die **StoreName**-Eigenschaft der Anwendung für die Nachrichten festgelegt. Bei zwei Läden, beispielsweise **Redmond** und **Seattle**, stempeln die POS-Terminals im Laden in Redmond ihre Verkaufsdatennachrichten in **StoreName** mit dem Namen **Redmond**, während die POS-Terminals im Laden in Seattle für **StoreName** den Namen **Seattle** verwenden. Der Geschäftsführer des Ladens in Redmond möchte nur Daten von seinen POS-Terminals sehen. Das System sieht nun wie folgt aus:

![Service-Bus4](./media/service-bus-create-topics-subscriptions/IC657167.gif)

Zum Einrichten dieser Weiterleitung erstellen Sie das Abonnement **Dashboard** wie folgt:

```
SqlFilter dashboardFilter = new SqlFilter("StoreName = 'Redmond'");
namespaceManager.CreateSubscription("DataCollectionTopic", "Dashboard", dashboardFilter);
```

Mit diesem Abonnementfilter werden nur Nachrichten, deren **StoreName**-Eigenschaft auf **Redmond** festgelegt ist, in die virtuelle Warteschlange für das Abonnement **Dashboard** kopiert. Mit der Abonnementfilterung eröffnen sich jedoch auch noch weitere Möglichkeiten: Für Anwendungen können mehrere Filterregeln pro Abonnement festgelegt werden. Zudem können die Eigenschaften einer Nachricht geändert werden, während diese in die virtuelle Warteschlange eines Abonnements weitergeleitet wird.

## Zusammenfassung

Alle Gründe, die für die Verwendung von Warteschlangen sprechen und die unter [Erstellen von Anwendungen, die Service Bus-Warteschlangen verwenden](service-bus-create-queues.md) beschrieben sind, gelten auch für die Verwendung von Themen, insbesondere folgende Gründe:

- Zeitliche Entkopplung: Producer (Sender von Nachrichten) und Consumer (Empfänger von Nachrichten) müssen nicht gleichzeitig online sein.

- Belastungsausgleich: Lastspitzen werden von dem Thema geglättet, sodass der Consumer anstatt der Spitzenlast nur die durchschnittliche Last verarbeiten können muss.

- Lastenausgleich: Ähnlich wie bei einer Warteschlange können Sie mehrere Consumer festlegen, die ein einzelnes Abonnement überwachen, wobei jede Nachricht nur an einen der Consumer gesendet wird und so die Last ausgeglichen wird.

- Lose Kopplung: Sie können das Messagingnetzwerk ohne Auswirkung auf die vorhandenen Endpunkte erweitern, z. B. Hinzufügen von Abonnements zu einem Thema oder Ändern von Filtern eines Themas, damit neue Consumer berücksichtigt werden können.

## Nächste Schritte

Informationen zur Verwendung von Warteschlangen im POS-Einzelhandelsszenario finden Sie unter [Erstellen von Anwendungen, die Service Bus-Themen und -Abonnements verwenden](service-bus-create-topics-subscriptions.md).

<!---HONumber=Sept15_HO3-->