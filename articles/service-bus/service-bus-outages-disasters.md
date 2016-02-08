<properties 
   pageTitle="Schützen von Service Bus-Anwendungen vor Ausfällen und Notfällen | Microsoft Azure"
   description="Es werden Verfahren beschrieben, die Sie zum Schützen von Anwendungen vor einem potenziellen Service Bus-Ausfall verwenden können."
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
   ms.date="01/26/2016"
   ms.author="sethm" />

# Bewährte Methoden zum Schützen von Anwendungen vor Service Bus-Ausfällen und Notfällen

Unternehmenswichtige Anwendungen müssen ohne Unterbrechung ausgeführt werden. Dies gilt auch bei ungeplanten Ausfällen oder in Notsituationen. In diesem Thema werden Verfahren beschrieben, mit denen Sie Service Bus-Anwendungen vor einem potenziellen Dienstausfall oder Notfällen schützen können.

Ein Ausfall wird als vorübergehende Nichtverfügbarkeit von Azure Service Bus definiert. Der Ausfall kann nur einige Komponenten von Service Bus betreffen, z. B. einen Nachrichtenspeicher, oder auch das gesamte Rechenzentrum. Nachdem das Problem behoben wurde, ist Service Bus wieder verfügbar. In der Regel führt ein Ausfall nicht zum Verlust von Nachrichten oder anderen Daten. Ein Beispiel für den Ausfall einer Komponente ist die Nichtverfügbarkeit eines bestimmten Nachrichtenspeichers. Beispiele für den Ausfall eines gesamten Rechenzentrums sind ein Stromausfall im Rechenzentrum oder ein fehlerhafter Netzwerkswitch im Rechenzentrum. Ein Ausfall kann einige Minuten oder auch bis zu einigen Tagen dauern.

Ein Notfall wird als dauerhafter Verlust einer Service Bus-Skalierungseinheit oder eines Rechenzentrums definiert. Das Rechenzentrum kann danach wieder zur Verfügung stehen oder auch nicht. Häufig gehen bei einem Notfall einige oder alle Nachrichten oder anderen Daten verloren. Beispiele für Notfälle sind Feuer, Überflutung und Erdbeben.

## Aktuelle Architektur

Service Bus nutzt mehrere Nachrichtenspeicher zum Speichern von Nachrichten, die an Warteschlangen oder Themen gesendet werden. Eine nicht partitionierte Warteschlange bzw. ein Thema ist einem Nachrichtenspeicher zugewiesen. Wenn dieser Nachrichtenspeicher nicht verfügbar ist, treten für alle Vorgänge der Warteschlange oder des Themas Fehler auf.

Alle Service Bus-Nachrichtenentitäten (Warteschlangen, Themen, Relays) befinden sich in einem Dienstnamespace, der einem Rechenzentrum zugeordnet ist. Service Bus ermöglicht keine automatische Georeplikation von Daten, und es ist auch nicht zulässig, dass ein Dienstnamespace über mehrere Rechenzentren verläuft.

## Schutz vor ACS-Ausfällen

Wenn Sie ACS-Anmeldeinformationen verwenden und ACS nicht verfügbar ist, können Clients keine Token mehr abrufen. Clients, die während des ACS-Ausfalls über ein Token verfügen, können Service Bus weiter nutzen, bis die Token ablaufen. Die Standardgültigkeitsdauer von Token beträgt 3 Stunden.

Verwenden Sie zum Schutz vor ACS-Ausfällen Shared Access Signature (SAS)-Token. In diesem Fall führt der Client die Authentifizierung direkt mit Service Bus durch, indem ein selbsterstelltes Token mit einem geheimen Schlüssel signiert wird. Aufrufe von ACS sind dann nicht mehr erforderlich. Weitere Informationen zu SAS-Token finden Sie unter [Service Bus-Authentifizierung][].

## Schützen von Warteschlangen und Themen vor Ausfällen von Nachrichtenspeichern

Eine nicht partitionierte Warteschlange bzw. ein Thema ist einem Nachrichtenspeicher zugewiesen. Wenn dieser Nachrichtenspeicher nicht verfügbar ist, treten für alle Vorgänge der Warteschlange oder des Themas Fehler auf. Eine partitionierte Warteschlange besteht dagegen aus mehreren Fragmenten. Jedes Fragment wird in einem anderen Nachrichtenspeicher gespeichert. Wenn eine Nachricht an eine partitionierte Warteschlange bzw. ein Thema gesendet wird, weist Service Bus die Nachricht einem der Fragmente zu. Wenn der entsprechende Nachrichtenspeicher nicht verfügbar ist, schreibt Service Bus in ein anderes Fragment, falls dies möglich ist. Weitere Informationen zu partitionierten Entitäten finden Sie unter [Partitionierte Messagingentitäten][].

## Schutz vor Ausfällen von Rechenzentren oder Notfällen

Um ein Failover zwischen zwei Rechenzentren zu ermöglichen, können Sie in jedem Rechenzentrum einen Service Bus-Dienstnamespace erstellen. Beispielsweise kann sich der Service Bus-Dienstnamespace **contosoPrimary.servicebus.windows.net** in der Region USA Nord Mitte befinden, während sich **contosoSecondary.servicebus.windows.net** in der Region USA Süd Mitte befindet. Wenn eine Service Bus-Nachrichtenentität bei einem Ausfall des Rechenzentrums verfügbar bleiben muss, können Sie die Entität in beiden Namespaces erstellen.

Weitere Informationen finden Sie im Abschnitt „Ausfall von Service Bus in einem Azure-Rechenzentrum“ unter [Asynchrone Nachrichtenmuster und hohe Verfügbarkeit][].

## Schutz von Relayendpunkten vor Rechenzentrumausfällen oder Notfällen

Die Georeplikation von Relayendpunkten ermöglicht die Verwendung eines Diensts, mit dem ein Relayendpunkt verfügbar gemacht wird, damit er bei Service Bus-Ausfällen zugänglich ist. Zum Erzielen der Georeplikation muss der Dienst zwei Relayendpunkte in unterschiedlichen Namespaces erstellen. Die Namespaces müssen sich in unterschiedlichen Rechenzentren befinden, und die beiden Endpunkte müssen unterschiedliche Namen haben. Ein primärer Endpunkt kann beispielsweise unter **contosoPrimary.servicebus.windows.net/myPrimaryService** erreichbar sein, während der sekundäre Endpunkt unter **contosoSecondary.servicebus.windows.net/mySecondaryService** erreichbar ist.

Der Dienst lauscht dann an beiden Endpunkten, und ein Client kann den Dienst über einen der beiden Endpunkte aufrufen. Eine Clientanwendung wählt ein Relayelement als primären Endpunkt und sendet dessen Anforderung an den aktiven Endpunkt. Wenn der Vorgang mit einem Fehlercode fehlschlägt, wird in diesem Fehler darauf hingewiesen, dass der Relayendpunkt nicht verfügbar ist. Die Anwendung öffnet einen Kanal zum Backup-Endpunkt und gibt die Anforderung neu heraus. An diesem Punkt tauschen der aktive Endpunkt und der Backup-Endpunkt die Rollen: Die Clientanwendung sieht den alten aktiven Endpunkt als neuen Backup-Endpunkt und den alten Backup-Endpunkt als neuen aktiven Endpunkt an. Wenn beide Sendevorgänge fehlschlagen, bleiben die Rollen der beiden Entitäten unverändert, und es wird ein Fehler zurückgegeben.

Im Beispiel [Georeplikation mit Service Bus Relay-Nachrichten][] wird gezeigt, wie Relays repliziert werden.

## Schützen von Warteschlangen und Themen vor Rechenzentrumausfällen und Notfällen

Um Resilienz in Bezug auf Rechenzentrumausfälle bei Verwendung des Brokermessaging zu erzielen, werden von Service Bus zwei Ansätze unterstützt: *aktive* und *passive* Replikation. Bei beiden Ansätzen können Sie die Erstellung in beiden Namespaces durchführen, wenn eine Warteschlange oder ein Thema bei einem Rechenzentrumausfall verfügbar bleiben muss. Beide Entitäten können den gleichen Namen haben. Eine primäre Warteschlange kann beispielsweise unter **contosoPrimary.servicebus.windows.net/myQueue** erreichbar sein, während die sekundäre Warteschlange unter **contosoSecondary.servicebus.windows.net/myQueue** erreichbar ist.

Falls die Anwendung keine permanente Kommunikation vom Absender zum Empfänger erfordert, kann die Anwendung eine dauerhafte clientseitige Warteschlange implementieren, um Nachrichtenverlust zu verhindern und den Absender gegen vorübergehende Service Bus-Fehler abzuschirmen.

## Aktive Replikation

Bei der aktiven Replikation werden Entitäten in beiden Dienstnamespaces für jeden Vorgang verwendet. Von allen Clients, die eine Nachricht senden, werden jeweils zwei Exemplare derselben Nachricht gesendet. Die erste Kopie wird an die primäre Entität gesendet (z. B. **contosoPrimary.servicebus.windows.net/sales**), und die zweite Kopie der Nachricht wird an die sekundäre Entität gesendet (z. B. **contosoSecondary.servicebus.windows.net/sales**).

Ein Client empfängt Nachrichten aus beiden Warteschlangen. Der Empfänger verarbeitet die erste Kopie einer Nachricht, und die zweite Kopie wird unterdrückt. Damit doppelte Nachrichten unterdrückt werden können, muss der Absender jede Nachricht mit einem eindeutigen Bezeichner versehen. Beide Kopien der Nachricht müssen mit dem gleichen Bezeichner gekennzeichnet werden. Sie können die Eigenschaften [BrokeredMessage.MessageId][] oder [BrokeredMessage.Label][] oder auch eine benutzerdefinierte Eigenschaft zum Kennzeichnen der Nachricht verwenden. Der Empfänger muss eine Liste der Nachrichten vorhalten, die er bereits empfangen hat.

Im Beispiel [Georeplikation mit Service Bus-Brokernachrichten][] wird die aktive Replikation von Nachrichtenentitäten veranschaulicht.

> [AZURE.NOTE] Beim Ansatz mit der aktiven Replikation wird die Anzahl von Vorgängen verdoppelt. Daher kann dieser Ansatz zu höheren Kosten führen.

## Passive Replikation

Wenn kein Fehler vorliegt, wird bei der passiven Replikation nur eine der beiden Nachrichtenentitäten verwendet. Ein Client sendet die Nachricht an die aktive Entität. Wenn der Vorgang auf der aktiven Entität mit einem Fehlercode fehlschlägt und angegeben wird, dass das Rechenzentrum, von dem die aktive Entität gehostet wird, ggf. nicht verfügbar ist, sendet der Client eine Kopie der Nachricht an die Backup-Entität. An diesem Punkt tauschen die aktive Entität und die Backup-Entität die Rollen: Der sendende Client sieht die alte aktive Entität als neue Backup-Entität und die alte Backup-Entität als neue aktive Entität an. Wenn beide Sendevorgänge fehlschlagen, bleiben die Rollen der beiden Entitäten unverändert, und es wird ein Fehler zurückgegeben.

Ein Client empfängt Nachrichten aus beiden Warteschlangen. Da die Möglichkeit besteht, dass der Empfänger zwei Kopien derselben Nachricht erhält, muss der Empfänger doppelte Nachrichten unterdrücken. Sie können Duplikate hierbei genauso unterdrücken, wie dies für die aktive Replikation beschrieben wurde.

Im Allgemeinen ist die passive Replikation wirtschaftlicher als die aktive Replikation, da in den meisten Fällen nur ein Vorgang ausgeführt wird. Latenz, Durchsatz und Kosten sind identisch mit dem Szenario ohne Replikation.

Bei Verwendung der passiven Replikation können Nachrichten in den folgenden Szenarien verloren gehen oder doppelt empfangen werden:

-   **Nachrichtenverzögerung oder -verlust**: Angenommen, der Absender hat die Nachricht „m1“ an die primäre Warteschlange gesendet, und die Warteschlange ist dann nicht mehr verfügbar, bevor der Empfänger „m1“ erhält. Der Absender sendet die nachfolgende Nachricht „m2“ an die sekundäre Warteschlange. Wenn die primäre Warteschlange vorübergehend nicht verfügbar ist, erhält der Empfänger „m1“, wenn die Warteschlange wieder verfügbar ist. Bei einer Notfallsituation kommt „m1“ unter Umständen niemals beim Empfänger an.

-   **Doppelter Empfang**: Angenommen, der Absender sendet eine Nachricht „m“ an die primäre Warteschlange. Service Bus verarbeitet „m“, aber es kann keine Antwort gesendet werden. Nach dem Timeout des Sendevorgangs sendet der Absender eine identische Kopie von „m“ an die sekundäre Warteschlange. Wenn der Empfänger die erste Kopie von „m“ empfangen kann, bevor die primäre Warteschlange nicht mehr verfügbar ist, erhält der Empfänger nahezu gleichzeitig beide Kopien von „m“. Falls der Empfänger die erste Kopie von „m“ nicht erhält, bevor die primäre Warteschlange nicht mehr verfügbar ist, erhält der Empfänger zuerst nur die zweite Kopie von „m“. Anschließend erhält er aber eine zweite Kopie von „m“, wenn die primäre Warteschlange verfügbar wird.

Im Beispiel [Georeplikation mit Service Bus-Brokernachrichten][] wird die passive Replikation von Nachrichtenentitäten veranschaulicht.

## Dauerhafte clientseitige Warteschlange

Wenn die Anwendung die Nichtverfügbarkeit einer Service Bus-Entität tolerieren kann, aber keine Nachrichten verloren gehen dürfen, kann der Absender eine dauerhafte clientseitige Warteschlange verwenden. Darin werden alle Nachrichten lokal gespeichert, die nicht an Service Bus gesendet werden können. Nachdem die Service Bus-Entität wieder verfügbar ist, werden alle gepufferten Nachrichten an diese Entität gesendet. Im Beispiel [Dauerhafter Nachrichtenabsender][] wird eine Warteschlange dieser Art mithilfe von MSMQ implementiert. Alternativ können die Nachrichten auch auf die lokale Festplatte geschrieben werden.

Bei einer dauerhaften clientseitigen Warteschlange wird die Nachrichtenreihenfolge beibehalten, und die Clientanwendung wird vor Ausnahmen geschützt, falls die Service Bus-Entität nicht verfügbar ist. Hierbei können einfache und verteilte Transaktionen verwendet werden.

## Nächste Schritte

Weitere Informationen zur Notfallwiederherstellung finden Sie in diesen Artikeln:

- [Geschäftskontinuität in Azure SQL-Datenbank][]
- [Azure-Geschäftskontinuität – Technische Anleitung][]

  [Service Bus-Authentifizierung]: service-bus-authentication-and-authorization.md
  [Partitionierte Messagingentitäten]: service-bus-partitioning.md
  [Asynchrone Nachrichtenmuster und hohe Verfügbarkeit]: service-bus-async-messaging.md
  [Georeplikation mit Service Bus Relay-Nachrichten]: http://code.msdn.microsoft.com/Geo-replication-with-16dbfecd
  [BrokeredMessage.MessageId]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.messageid.aspx
  [BrokeredMessage.Label]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.label.aspx
  [Georeplikation mit Service Bus-Brokernachrichten]: http://code.msdn.microsoft.com/Geo-replication-with-f5688664
  [Dauerhafter Nachrichtenabsender]: http://code.msdn.microsoft.com/Service-Bus-Durable-Sender-0763230d
  [Geschäftskontinuität in Azure SQL-Datenbank]: ../sql-database/sql-database-business-continuity.md
  [Azure-Geschäftskontinuität – Technische Anleitung]: https://msdn.microsoft.com/library/azure/hh873027.aspx

<!---HONumber=AcomDC_0128_2016-->