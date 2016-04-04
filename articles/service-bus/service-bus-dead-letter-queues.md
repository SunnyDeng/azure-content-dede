<properties 
    pageTitle="Service Bus-Warteschlangen für unzustellbare Nachrichten | Microsoft Azure" 
    description="Übersicht über Azure Service Bus-Warteschlangen für unzustellbare Nachrichten" 
    services="service-bus" 
    documentationCenter=".net" 
    authors="sethmanheim" 
    manager="timlt" 
    editor=""/>

<tags
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na" 
    ms.date="03/16/2016"
    ms.author="clemensv;sethm"/>

# Übersicht über Service Bus-Warteschlangen für unzustellbare Nachrichten

Service Bus-Warteschlangen und Subscriptions für Topics bieten eine sekundäre Unterwarteschlange, die als *Warteschlange für unzustellbare Nachrichten* bezeichnet wird. Die Warteschlange für unzustellbare Nachrichten muss nicht explizit erstellt und kann nicht gelöscht oder anderweitig unabhängig von der Hauptentität verwaltet werden.

Die Warteschlange für unzustellbare Nachrichten dient zum Speichern von Nachrichten, die an keinen Empfänger übermittelt oder einfach nicht verarbeitet werden können. Nachrichten können dann aus dieser Warteschlange entfernt und überprüft werden. Eine Anwendung kann mithilfe eines Operators Probleme beheben und die Nachricht erneut senden, den Fehler protokollieren und/oder korrigierende Maßnahmen ergreifen.

Im Hinblick auf API und Protokoll ähnelt die Warteschlange für unzustellbare Nachrichten weitgehend anderen Warteschlangen, mit der Ausnahme, dass Nachrichten nur durch die übergeordnete Entität über den Vorgang für unzustellbare Nachrichten gesendet werden können. Darüber hinaus ist die Gültigkeitsdauer nicht relevant, und eine Nachricht kann nicht von einer Warteschlange für unzustellbare Nachrichten aus als unzustellbar gekennzeichnet werden. Die Warteschlange für unzustellbare Nachrichten bietet vollständige Unterstützung für die Peek/Lock-Übermittlung und Transaktionsvorgänge.

Beachten Sie, dass die Warteschlange für unzustellbare Nachrichten nicht automatisch bereinigt wird. Nachrichten verbleiben in dieser Warteschlange, bis Sie sie explizit daraus abrufen und für die unzustellbare Nachricht [Complete()](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.completeasync.aspx) aufrufen.

## Verschieben von Nachrichten in die Warteschlange für unzustellbare Nachrichten

Es gibt verschiedene Aktivitäten in Service Bus, durch welche Nachrichten aus dem Messagingmodul selbst per Push in die Warteschlange für unzustellbare Nachrichten übertragen werden. Eine Anwendung kann Nachrichten auch explizit per Push in die Warteschlange für unzustellbare Nachrichten übertragen.

Während die Nachricht vom Broker verschoben wird, werden ihr zwei Eigenschaften hinzugefügt, wenn der Broker die interne Version der [DeadLetter](https://msdn.microsoft.com/library/azure/hh291941.aspx)-Methode für die Nachricht aufruft: `DeadLetterReason` und `DeadLetterErrorDescription`.

Anwendungen können ihre eigenen Codes für die `DeadLetterReason`-Eigenschaft definieren, aber das System legt die folgenden Werte fest.

| Bedingung | DeadLetterReason | DeadLetterErrorDescription |
|---------------------------------------------------------------------------------------------------------------------------------------|-----------------------------|----------------------------------------------------------------------------------|
| Always | HeaderSizeExceeded | Das Größenkontingent für diesen Datenstrom wurde überschritten. |
| !TopicDescription.<br />EnableFilteringMessagesBeforePublishing und SubscriptionDescription.<br />EnableDeadLetteringOnFilterEvaluationExceptions | exception.GetType().Name | exception.Message |
| EnableDeadLetteringOnMessageExpiration | TTLExpiredException | Die Nachricht ist abgelaufen und wurde in die Warteschlange für unzustellbare Nachrichten verschoben. |
| SubscriptionDescription.RequiresSession | Sitzungs-ID ist NULL. | Die für die Sitzung aktivierte Entität erlaubt keine Nachricht, deren Sitzungsbezeichner null ist. |
| !dead letter queue | MaxTransferHopCountExceeded | Null |
| Explizites Markieren von Nachrichten als unzustellbar durch Anwendung | Gemäß Anwendung | Gemäß Anwendung |

## Überschreiten von MaxDeliveryCount

Warteschlangen und Subscriptions enthalten eine Eigenschaft [QueueDescription.MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx) bzw. [SubscriptionDescription.MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.maxdeliverycount.aspx); der jeweilige Standardwert ist 10. Jedes Mal, wenn eine Nachricht unter einer Sperre übermittelt wurde ([ReceiveMode.PeekLock](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.receivemode.aspx)), aber entweder explizit abgebrochen wurde oder die Sperre abgelaufen ist, wird der Wert [BrokeredMessage.DeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.deliverycount.aspx) für die Nachricht erhöht. Wenn [DeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.deliverycount.aspx) den Wert [MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx) überschreitet, wird die Nachricht in die Warteschlange für unzustellbare Nachrichten verschoben, und der Ursachencode `MaxDeliveryCountExceeded` wird angegeben.

Dieses Verhalten kann nicht deaktiviert werden, aber Sie können [MaxDeliveryCount](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.maxdeliverycount.aspx) auf eine sehr hohe Zahl festlegen.

## Überschreiten von TimeToLive

Wenn die Eigenschaft [QueueDescription.EnableDeadLetteringOnMessageExpiration](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enabledeadletteringonmessageexpiration.aspx) bzw. [SubscriptionDescription.EnableDeadLetteringOnMessageExpiration](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.enabledeadletteringonmessageexpiration.aspx) auf **true** festgelegt ist (die Standardeinstellung lautet **false**), werden alle ablaufenden Nachrichten in die Warteschlange für unzustellbare Nachrichten verschoben, und der Ursachencode `TTLExpiredException` wird angegeben.

Beachten Sie, dass abgelaufene Nachrichten nur bereinigt und daher in die Warteschlange für unzustellbare Nachrichten verschoben werden, wenn mindestens ein aktiver Empfänger per Pull aus der Hauptwarteschlange oder der Subscription abruft. Dieses Verhalten ist beabsichtigt.

## Fehler beim Verarbeiten von Regeln für Subscriptions

Wenn die [SubscriptionDescription.EnableDeadLetteringOnFilterEvaluationExceptions](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.enabledeadletteringonfilterevaluationexceptions.aspx)-Eigenschaft für eine Subscription aktiviert ist, werden Fehler, die während der Ausführung der SQL-Filterregel einer Subscription auftreten, zusammen mit der betreffenden Nachricht in der Warteschlange für unzustellbare Nachrichten erfasst.

## Unzustellbare Nachrichten auf Anwendungsebene

Zusätzlich zu den vom System bereitgestellten Features für unzustellbare Nachrichten können Anwendungen die Warteschlange für unzustellbare Nachrichten verwenden, um nicht annehmbare Nachrichten explizit abzulehnen. Dazu zählen beispielsweise Nachrichten, die aufgrund eines beliebigen Systemproblems nicht ordnungsgemäß verarbeitet werden können, Nachrichten mit fehlerhaften Nutzlasten oder Nachrichten, bei denen Authentifizierungsfehler auftreten, wenn ein Sicherheitsschema auf Nachrichtenebene verwendet wird.

## Beispiel

Der folgende Codeausschnitt erstellt einen Nachrichtenempfänger. In der Nachrichtenempfangsschleife für die Hauptwarteschlange ruft der Code die Nachricht mit [Receive(TimeSpan.Zero)](https://msdn.microsoft.com/library/azure/dn130350.aspx) ab. Dadurch wird der Broker aufgefordert, sofort alle bereitstehenden Nachrichten bzw. kein Ergebnis zurückzugeben. Wenn der Code eine Nachricht empfängt, wird diese sofort abgebrochen und `DeliveryCount` wird erhöht. Sobald das System die Nachricht in die Warteschlange für unzustellbare Nachrichten verschiebt, ist die Hauptwarteschlange leer und die Schleife wird beendet, da [ReceiveAsync](https://msdn.microsoft.com/library/azure/dn130350.aspx) den Wert **null** zurückgibt.

```
var receiver = await receiverFactory.CreateMessageReceiverAsync(queueName, ReceiveMode.PeekLock);
while(true)
{
    var msg = await receiver.ReceiveAsync(TimeSpan.Zero);
    if (msg != null)
    {
        Console.WriteLine("Picked up message; DeliveryCount {0}", msg.DeliveryCount);
        await msg.AbandonAsync();
    }
    else
    {
        break;
    }
}
```

## Nächste Schritte

Weitere Informationen zu Service Bus-Warteschlangen finden Sie in den folgenden Artikeln:

- [Vergleich von Azure-Warteschlangen und Service Bus-Warteschlangen](service-bus-azure-and-service-bus-queues-compared-contrasted.md)
- Verwenden von [Service Bus-Warteschlangen](service-bus-dotnet-how-to-use-queues.md)

<!---HONumber=AcomDC_0323_2016-->