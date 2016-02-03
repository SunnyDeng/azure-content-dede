<properties 
   pageTitle="Partitionierte Nachrichtenentitäten | Microsoft Azure"
   description="Es wird beschrieben, wie Nachrichtenentitäten mit mehreren Nachrichtenbrokern partitioniert werden."
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
   ms.date="12/28/2015"
   ms.author="sethm" />

# Partitionierte Nachrichtenentitäten

Für Azure Service Bus werden mehrere Nachrichtenbroker verwendet, um Nachrichten zu verarbeiten, sowie mehrere Nachrichtenspeicher, um Nachrichten zu speichern. Eine herkömmliche Warteschlange oder ein Thema werden von einem einzelnen Nachrichtenbroker verarbeitet und in einem Nachrichtenspeicher gespeichert. Außerdem ermöglicht Service Bus, dass Warteschlangen oder Themen über mehrere Nachrichtenbroker und -speicher partitioniert werden. Dies bedeutet, dass der Gesamtdurchsatz einer partitionierten Warteschlange oder eines Themas nicht mehr durch die Leistung eines einzelnen Nachrichtenbrokers oder Nachrichtenspeichers beschränkt wird. Außerdem führt ein vorübergehender Ausfall eines Nachrichtenspeichers nicht dazu, dass eine partitionierte Warteschlange oder ein Thema nicht verfügbar ist. Partitionierte Warteschlangen und Themen können alle erweiterten Service Bus-Features enthalten, z. B. die Unterstützung von Transaktionen und Sitzungen.

Weitere Informationen zum inneren Aufbau von Service Bus finden Sie im Thema [Service Bus-Architektur][].

## Partitionierte Warteschlangen und Themen

Jede partitionierte Warteschlange bzw. jedes Thema besteht aus mehreren Fragmenten. Jedes Fragment wird in einem anderen Nachrichtenspeicher gespeichert und von einem anderen Nachrichtenbroker verarbeitet. Wenn eine Nachricht an eine partitionierte Warteschlange bzw. ein Thema gesendet wird, weist Service Bus die Nachricht einem der Fragmente zu. Die Auswahl erfolgt nach dem Zufallsprinzip durch Service Bus oder einen Partitionsschlüssel, der vom Absender angegeben werden kann.

Wenn ein Client eine Nachricht von einer partitionierten Warteschlange oder von einem Abonnement oder partitionierten Thema empfangen möchte, fragt Service Bus alle Fragmente auf Nachrichten ab. Anschließend wird die erste Nachricht zurückgegeben, die von einem der Nachrichtenspeicher an den Empfänger zurückgegeben wird. Service Bus speichert die anderen Nachrichten zwischen und gibt sie zurück, wenn zusätzliche Empfangsanforderungen eingehen. Ein empfangender Client ist sich der Partitionierung nicht bewusst. Das Verhalten einer partitionierten Warteschlange oder eines Themas (z. B. „read“, „complete“, „defer“, „deadletter“, „prefetching“) dem Client gegenüber ist mit dem Verhalten einer normalen Entität identisch.

Es fallen keine zusätzlichen Kosten an, wenn eine Nachricht an eine partitionierte Warteschlange oder ein Thema gesendet oder von dort empfangen wird.

## Aktivieren der Partitionierung

Setzen Sie zum Verwenden von partitionierten Warteschlangen und Themen mit Azure Service Bus die Azure SDK-Version 2.2 oder höher ein, oder geben Sie `api-version=2013-10` in Ihren HTTP-Anforderungen an.

Sie können Service Bus-Warteschlangen und -Themen in Größen von 1, 2, 3, 4 oder 5 GB erstellen (die Standardgröße ist 1 GB). Bei aktivierter Partitionierung erstellt Service Bus 16 Partitionen für jedes angegebene GB. Wenn Sie also eine Warteschlange mit einer Größe von 5 GB erstellen, beträgt die maximale Warteschlangengröße bei 16 Partitionen 5*16 = 80 GB. Die maximale Größe der partitionierten Warteschlange oder des partitionierten Themas wird im zugehörigen Eintrag im [klassischen Azure-Portal][] angezeigt.

Es gibt mehrere Möglichkeiten, eine partitionierte Warteschlange oder ein partitioniertes Thema zu erstellen. Wenn Sie die Warteschlange oder das Thema über Ihre Anwendung erstellen, können Sie die Partitionierung für die Warteschlange oder das Thema aktivieren, indem Sie die [QueueDescription.EnablePartitioning][]- bzw. [TopicDescription.EnablePartitioning][]-Eigenschaft auf **true** festlegen. Diese Eigenschaften müssen bei der Erstellung der Warteschlange oder des Themas festgelegt werden. Es ist nicht möglich, diese Eigenschaften für eine vorhandene Warteschlange oder ein Thema zu ändern. Beispiel:

```
// Create partitioned topic
NamespaceManager ns = NamespaceManager.CreateFromConnectionString(myConnectionString);
TopicDescription td = new TopicDescription(TopicName);
td.EnablePartitioning = true;
ns.CreateTopic(td);
```

Alternativ können Sie eine partitionierte Warteschlange oder ein partitioniertes Thema in Visual Studio oder im [klassischen Azure-Portal][] erstellen. Wenn Sie eine neue Warteschlange oder ein Thema im Portal erstellen, sollten Sie die Option **Partitionierung aktivieren** auf der Registerkarte **Konfigurieren** des Warteschlangen- oder Themenfensters überprüfen. Klicken Sie in Visual Studio im Dialogfeld **Neue Warteschlange** bzw. **Neues Thema** auf das Kontrollkästchen **Partitionierung aktivieren**.

## Verwenden von Partitionsschlüsseln

Wenn eine Nachricht in eine partitionierte Warteschlange oder ein Thema eingereiht wird, wird von Service Bus das Vorhandensein eines Partitionsschlüssels überprüft. Wenn ein Schlüssel gefunden wird, wird das Fragment basierend auf diesem Schlüssel ausgewählt. Falls kein Partitionsschlüssel gefunden wird, wird das Fragment basierend auf einem internen Algorithmus ausgewählt.

### Verwenden eines Partitionsschlüssels

Einige Szenarien, z. B. Sitzungen oder Transaktionen, erfordern es, dass Nachrichten in einem bestimmten Fragment gespeichert werden. Für all diese Szenarien ist die Verwendung eines Partitionsschlüssels erforderlich. Alle Nachrichten, für die der gleiche Partitionsschlüssel verwendet wird, sind demselben Fragment zugewiesen. Wenn das Fragment vorübergehend nicht verfügbar ist, wird von Service Bus ein Fehler zurückgegeben.

Je nach Szenario werden verschiedene Nachrichteneigenschaften als Partitionsschlüssel verwendet:

**SessionId**: Wenn für eine Nachricht die [BrokeredMessage.SessionId][]-Eigenschaft festgelegt ist, verwendet Service Bus diese Eigenschaft als Partitionsschlüssel. Auf diese Weise werden alle Nachrichten, die zu derselben Sitzung gehören, von demselben Nachrichtenbroker verarbeitet. So kann Service Bus sowohl die Nachrichtensortierung als auch die Einheitlichkeit von Sitzungszuständen garantieren.

**PartitionKey**: Wenn für eine Nachricht die [BrokeredMessage.PartitionKey][]-Eigenschaft festgelegt ist, aber nicht die [BrokeredMessage.SessionId][]-Eigenschaft, verwendet Service Bus die [PartitionKey][]-Eigenschaft als Partitionsschlüssel. Wenn für die Nachricht sowohl die [SessionId][]- als auch die [PartitionKey][]-Eigenschaft festgelegt ist, müssen beide Eigenschaften identisch sein. Falls die [PartitionKey][]-Eigenschaft auf einen anderen Wert als die [SessionId][]-Eigenschaft festgelegt ist, gibt Service Bus die Ausnahme **InvalidOperationException** zurück. Die [PartitionKey][]-Eigenschaft sollte verwendet werden, wenn ein Absender nicht sitzungsorientierte Transaktionsnachrichten sendet. Mit dem Partitionsschlüssel wird sichergestellt, dass alle Nachrichten, die innerhalb einer Transaktion gesendet werden, von demselben Nachrichtenbroker verarbeitet werden.

**MessageId**: Wenn für die Warteschlange bzw. das Thema die [QueueDescription.RequiresDuplicateDetection][]-Eigenschaft auf **true** festgelegt ist und die [BrokeredMessage.SessionId][]- oder die [BrokeredMessage.PartitionKey][]-Eigenschaft nicht festgelegt wurde, dient die [BrokeredMessage.MessageId][]-Eigenschaft als Partitionsschlüssel. (Beachten Sie, dass die Microsoft .NET- und AMQP-Bibliotheken automatisch eine Nachrichten-ID zuweisen, wenn dies von der sendenden Anwendung nicht übernommen wird.) In diesem Fall werden alle Kopien einer Nachricht von demselben Nachrichtenbroker verarbeitet. So kann Service Bus doppelte Nachrichten erkennen und verwerfen. Wenn die [QueueDescription.RequiresDuplicateDetection][]-Eigenschaft nicht auf **true** festgelegt ist, sieht Service Bus die [MessageId][]-Eigenschaft nicht als Partitionsschlüssel an.

### Keine Verwendung eines Partitionsschlüssels

Wenn kein Partitionsschlüssel vorhanden ist, verteilt Service Bus Nachrichten gleichmäßig nacheinander auf alle Fragmente der partitionierten Warteschlange bzw. des Themas. Wenn das ausgewählte Fragment nicht verfügbar ist, weist Service Bus die Nachricht einem anderen Fragment zu. Auf diese Weise kann der Sendevorgang erfolgreich durchgeführt werden, obwohl ein Nachrichtenspeicher vorübergehend nicht verfügbar ist.

Um Service Bus ausreichend Zeit zum Einreihen der Nachricht in ein anderes Fragment zu geben, muss der [MessagingFactorySettings.OperationTimeout][]-Wert des Clients, der die Nachricht sendet, größer als 15 Sekunden sein. Es wird empfohlen, die [OperationTimeout][]-Eigenschaft auf den Standardwert von 60 Sekunden festzulegen.

Beachten Sie, dass ein Partitionsschlüssel eine Nachricht an ein bestimmtes Fragment „anheftet“. Wenn der Nachrichtenspeicher, der dieses Fragment enthält, nicht verfügbar ist, wird von Service Bus ein Fehler zurückgegeben. Wenn kein Partitionsschlüssel vorhanden ist, kann Service Bus ein anderes Fragment auswählen, damit der Vorgang erfolgreich ist. Aus diesem Grund wird empfohlen, nur dann einen Partitionsschlüssel anzugeben, wenn dies erforderlich ist.

## Weiterführende Themen: Verwenden von Transaktionen mit partitionierten Entitäten

Nachrichten, die als Teil einer Transaktion gesendet werden, müssen einen Partitionsschlüssel angeben. Dies kann eine der folgenden Eigenschaften sein: [BrokeredMessage.SessionId][], [BrokeredMessage.PartitionKey][] oder [BrokeredMessage.MessageId][]. Für alle Nachrichten, die als Teil derselben Transaktion gesendet werden, muss derselbe Partitionsschlüssel angegeben werden. Wenn Sie versuchen, eine Nachricht innerhalb einer Transaktion ohne Partitionsschlüssel zu senden, wird von Service Bus die Ausnahme **InvalidOperationException** zurückgegeben. Wenn Sie versuchen, mehrere Nachrichten mit verschiedenen Partitionsschlüsseln innerhalb derselben Transaktion zu senden, gibt Service Bus die Ausnahme **InvalidOperationException** zurück. Beispiel:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.PartitionKey = "myPartitionKey";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

Wenn Eigenschaften, die als Partitionsschlüssel dienen, festgelegt werden, heftet Service Bus die Nachricht an ein bestimmtes Fragment an. Dieses Verhalten tritt unabhängig davon auf, ob eine Transaktion verwendet wird. Es wird empfohlen, keinen Partitionsschlüssel anzugeben, wenn dies nicht erforderlich ist.

## Verwenden von Sitzungen mit partitionierten Entitäten

Um eine Transaktionsnachricht an ein sitzungsorientiertes Thema bzw. eine Warteschlange zu senden, muss für die Nachricht die [BrokeredMessage.SessionId][]-Eigenschaft festgelegt sein. Wenn die [BrokeredMessage.PartitionKey][]-Eigenschaft ebenfalls angegeben wird, muss diese mit der [SessionId][]-Eigenschaft identisch sein. Falls sie sich unterscheiden, gibt Service Bus die Ausnahme **InvalidOperationException** aus.

Im Gegensatz zu normalen (nicht partitionierten) Warteschlangen oder Themen ist es nicht möglich, eine einzelne Transaktion zu verwenden, um mehrere Nachrichten an verschiedene Sitzungen zu senden. Wenn Sie dies versuchen, gibt Service Bus die Ausnahme **InvalidOperationException** zurück. Beispiel:

```
CommittableTransaction committableTransaction = new CommittableTransaction();
using (TransactionScope ts = new TransactionScope(committableTransaction))
{
    BrokeredMessage msg = new BrokeredMessage("This is a message");
    msg.SessionId = "mySession";
    messageSender.Send(msg); 
    ts.Complete();
}
committableTransaction.Commit();
```

## Automatische Nachrichtenweiterleitung mit partitionierten Entitäten

Azure Service Bus unterstützt die automatische Nachrichtenweiterleitung von, an oder zwischen partitionierten Entitäten. Legen Sie zum Aktivieren der automatischen Nachrichtenweiterleitung die [QueueDescription.ForwardTo][]-Eigenschaft für die Quellwarteschlange oder das Abonnement fest. Wenn für die Nachricht ein Partitionsschlüssel ([SessionId][], [PartitionKey][] oder [MessageId][]) angegeben wird, wird er für die Zielentität verwendet.

## Einschränkungen partitionierter Entitäten

In der aktuellen Implementierung gelten bei Service Bus die folgenden Einschränkungen für partitionierte Warteschlangen und Themen:

-   Partitionierte Warteschlangen und Themen sind per SBMP oder HTTP/HTTPS sowie mit AMQP verfügbar.

-   Für partitionierte Warteschlangen und Themen wird nicht das Senden von Nachrichten unterstützt, die unterschiedlichen Sitzungen einer einzelnen Transaktion angehören.

-   Service Bus erlaubt derzeit bis zu 100 partitionierte Warteschlangen oder Themen pro Namespace. Jede partitionierte Warteschlange bzw. jedes partitionierte Thema wird in das zulässige Kontingent von 10.000 Entitäten pro Namespace eingerechnet.

-   Partitionierte Warteschlangen und Themen werden für die Versionen 1.0 und 1.1 von Service Bus für Windows Server nicht unterstützt.

## Nächste Schritte

Weitere Informationen zur Partitionierung von Messagingentitäten finden Sie in der Beschreibung der [AMQP 1.0-Unterstützung für partitionierte Service Bus-Warteschlangen und -Themen][].

  [Service Bus-Architektur]: service-bus-architecture.md
  [klassischen Azure-Portal]: http://manage.windowsazure.com
  [QueueDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enablepartitioning.aspx
  [TopicDescription.EnablePartitioning]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicdescription.enablepartitioning.aspx
  [BrokeredMessage.SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [BrokeredMessage.PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [SessionId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.sessionid.aspx
  [PartitionKey]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.partitionkey.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [QueueDescription.RequiresDuplicateDetection]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.requiresduplicatedetection.aspx
  [MessagingFactorySettings.OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [OperationTimeout]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout.aspx
  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [AMQP 1.0-Unterstützung für partitionierte Service Bus-Warteschlangen und -Themen]: service-bus-partitioned-queues-and-topics-amqp-overview.md

<!---HONumber=AcomDC_0107_2016-->