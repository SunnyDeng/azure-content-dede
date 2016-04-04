<properties 
    pageTitle="Gekoppelte Service Bus-Namespaces | Microsoft Azure"
    description="Details und Kosten zur Implementierung von gekoppelten Namespaces"
    services="service-bus"
    documentationCenter="na"
    authors="sethmanheim"
    manager="timlt"
    editor="" /> 
<tags 
    ms.service="service-bus"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="03/16/2016"
    ms.author="sethm" />

# Details zur Implementierung von gekoppelten Namespaces und Kostenaspekte

Mit der [PairNamespaceAsync][]-Methode unter Verwendung einer [SendAvailabilityPairedNamespaceOptions][]-Instanz werden sichtbare Aufgaben in Ihrem Namen ausgeführt. Da beim Verwenden des Features Kostenaspekte zu beachten sind, ist es ratsam, sich mit diesen Aufgaben vertraut zu machen. So sind Sie auf das Auftreten dieses Verhaltens vorbereitet. Die API veranlasst in Ihrem Namen das folgende automatische Verhalten:

-   Erstellung von Backlog-Warteschlangen
-   Erstellung eines [MessageSender][]-Objekts, das mit Warteschlangen oder Themen kommuniziert
-   Wenn eine Nachrichtenentität nicht mehr verfügbar ist, werden Ping-Nachrichten an die Entität gesendet, um nach Möglichkeit zu ermitteln, wann die Entität wieder verfügbar sein wird
-   Optionale Erstellung einer Gruppe von „Nachrichtensystemen”, mit denen Nachrichten aus den Backlog-Warteschlangen in die primären Warteschlangen verschoben werden
-   Koordinierung des Schließens bzw. der Fehlerbehandlung der primären und sekundären [MessagingFactory][]-Instanzen

Einfach ausgedrückt funktioniert das Feature wie folgt: Wenn die primäre Entität fehlerfrei ist, werden keine Verhaltensänderungen vorgenommen. Wenn der [FailoverInterval][]-Zeitraum abläuft und die primäre Entität nach einer nicht vorübergehenden [MessagingException][] oder [TimeoutException][] keine erfolgreichen Sendevorgänge erkennt, tritt das folgende Verhalten auf:

1.  Sendevorgänge an die primäre Entität werden deaktiviert, und das System sendet Ping-Nachrichten an die primäre Entität, bis diese erfolgreich zugestellt werden können.

2.  Es wird eine zufällige Backlog-Warteschlange ausgewählt.

3.  [BrokeredMessage][]-Objekte werden an die ausgewählte Backlog-Warteschlange geleitet.

1.  Wenn für einen Sendevorgang an die gewählte Backlog-Warteschlange ein Fehler auftritt, wird diese Warteschlange aus der Rotation herausgenommen und eine neue Warteschlange ausgewählt. Alle Absender der [MessagingFactory][]-Instanz werden über den Fehler informiert.

Die Sequenz ist in den folgenden Abbildungen dargestellt. Zuerst werden vom Absender die Nachrichten gesendet.

![Gekoppelte Namespaces][0]

Wenn das Senden an die primäre Warteschlange nicht erfolgreich ist, beginnt der Absender damit, Nachrichten an eine zufällig ausgewählte Backlog-Warteschlange zu senden. Gleichzeitig wird eine Ping-Aufgabe gestartet.

![Gekoppelte Namespaces][1]

An diesem Punkt befinden sich die Nachrichten weiterhin in der sekundären Warteschlange und wurden nicht an die primäre Warteschlange übermittelt. Sobald die primäre Warteschlange wieder fehlerfrei ist, sollte mindestens ein Prozess den „Siphon“ ausführen. Über den Siphon werden die Nachrichten aus den verschiedenen Backlog-Warteschlangen an die richtigen Zielentitäten (Warteschlangen und Themen) zugestellt.

![Gekoppelte Namespaces][2]

Im weiteren Verlauf dieses Themas wird näher beschrieben, wie die einzelnen Elemente funktionieren.

## Erstellen von Backlog-Warteschlangen

Das [SendAvailabilityPairedNamespaceOptions][]-Objekt, das an die [PairNamespaceAsync][]-Methode übergeben wird, gibt die Anzahl von Backlog-Warteschlangen an, die Sie verwenden möchten. Jede Backlog-Warteschlange wird dann erstellt, und die folgenden Eigenschaften werden explizit festgelegt (alle anderen Werte werden auf die [QueueDescription][]-Standardeinstellungen festgelegt):

| Path | [primärer Namespace]/x-servicebus-transfer/[Index], wobei [Index] ein Wert in [0, BacklogQueueCount) ist |
|----------------------------------------|------------------------------------------------------------------------------------------------------|
| MaxSizeInMegabytes | 5120 |
| MaxDeliveryCount | int.MaxValue |
| DefaultMessageTimeToLive | TimeSpan.MaxValue |
| AutoDeleteOnIdle | TimeSpan.MaxValue |
| LockDuration | 1 Minute |
| EnableDeadLetteringOnMessageExpiration | true |
| EnableBatchedOperations | true |

Die erste Backlog-Warteschlange, die für den Namespace **contoso** erstellt wird, hat beispielsweise den Namen `contoso/x-servicebus-transfer/0`.

Bei der Erstellung der Warteschlangen überprüft der Code zuerst, ob eine Warteschlange bereits vorhanden ist. Wenn die Warteschlange nicht vorhanden ist, wird die Warteschlange erstellt. Vom Code werden „zusätzliche“ Backlog-Warteschlangen nicht bereinigt. Falls die Anwendung mit dem primären Namespace **contoso** beispielsweise fünf Backlog-Warteschlangen anfordert, aber bereits eine Backlog-Warteschlange mit dem Pfad `contoso/x-servicebus-transfer/7` vorhanden ist, existiert diese zusätzliche Backlog-Warteschlange zwar, wird aber nicht verwendet. Im System sind explizit zusätzliche Backlog-Warteschlangen zulässig, die nicht verwendet werden. Als Namespace-Besitzer sind Sie für die Bereinigung aller nicht verwendeten oder unerwünschten Backlog-Warteschlangen verantwortlich. Der Grund für diese Entscheidung ist, dass Service Bus nicht wissen kann, welchen Zweck sämtliche Warteschlangen in Ihrem Namespace haben. Wenn eine Warteschlange mit dem jeweiligen Namen vorhanden ist, die vorausgesetzte [QueueDescription][] aber NICHT erfüllt, gelten in Bezug auf die Änderung des Standardverhaltens zudem Ihre eigenen Gründe. Für Änderungen, die von Ihrem Code an den Backlog-Warteschlangen vorgenommen werden, wird keine Garantie übernommen. Stellen Sie sicher, dass Sie Ihre Änderungen gründlich testen.

## Benutzerdefiniertes MessageSender-Objekt

Beim Senden durchlaufen alle Nachrichten ein internes [MessageSender][]-Objekt, das ein normales Verhalten zeigt, wenn alles funktioniert. Falls es zu Fehlern kommt, wird die Umleitung an die Backlog-Warteschlangen genutzt. Nach dem Empfang eines nicht vorübergehenden Fehlers wird ein Zeitgeber gestartet. Nach einem [TimeSpan][]-Zeitraum, der aus dem [FailoverInterval][]-Eigenschaftswert besteht und in dem keine Nachrichten erfolgreich gesendet werden, wird das Failover aktiviert. An diesem Punkt werden für jede Entität die folgenden Schritte ausgeführt:

- Eine Ping-Aufgabe führt alle [PingPrimaryInterval][]-Vorkommen aus, um zu prüfen, ob die Entität verfügbar ist. Nachdem diese Aufgabe erfolgreich abgeschlossen wurde, beginnt der gesamte Clientcode, von dem die Entität verwendet wird, sofort mit dem Senden neuer Nachrichten an den primären Namespace.

- Zukünftige Anforderungen zum Senden an dieselbe Entität von anderen Absendern führen dazu, dass die [BrokeredMessage][], die zum Vornehmen von Änderungen gesendet wird, in der Backlog-Warteschlange enthalten ist. Durch die Änderung werden einige Eigenschaften aus dem [BrokeredMessage][]-Objekt entfernt und an einem anderen Ort gespeichert. Die folgenden Eigenschaften werden gelöscht und unter einem neuen Alias hinzugefügt, sodass Service Bus und das SDK Nachrichten einheitlich verarbeiten können:

| Alter Eigenschaftenname | Neuer Eigenschaftenname |
|-------------------------|-------------------|
| SessionId | x-ms-sessionid |
| TimeToLive | x-ms-timetolive |
| ScheduledEnqueueTimeUtc | x-ms-path |

Der ursprüngliche Zielpfad wird ebenfalls in der Nachricht als Eigenschaft mit dem Namen „x-ms-path“ gespeichert. Bei diesem Aufbau können Nachrichten für viele Entitäten in einer einzigen Backlog-Warteschlange gemeinsam vorhanden sein. Die Eigenschaften werden durch den Siphon zurückübersetzt.

Für das benutzerdefinierte [MessageSender][]-Objekt können Probleme auftreten, wenn Nachrichten in die Nähe das Grenzwerts von 256 KB kommen und das Failover aktiviert wird. Das benutzerdefinierte [MessageSender][]-Objekt speichert Nachrichten für alle Warteschlangen und Themen zusammen in den Backlog-Warteschlangen. Bei diesem Objekt werden Nachrichten von vielen primären Elementen in den Backlog-Warteschlangen gemischt. Zum Behandeln des Lastenausgleichs für viele Clients, die einander nicht kennen, wählt das SDK zufällig eine Backlog-Warteschlange für jedes [QueueClient][]- oder [TopicClient][]-Element, das Sie im Code erstellen.

## Ping-Nachrichten

Eine Ping-Nachricht ist eine leere [BrokeredMessage][], bei der die [ContentType][]-Eigenschaft auf „application/vnd.ms-servicebus-ping“ festgelegt ist und die über einen [TimeToLive][]-Wert von 1 Sekunde verfügt. Diese Ping-Nachricht verfügt in Service Bus über ein besonderes Merkmal: Der Server stellt keine Ping-Nachricht zu, wenn ein Aufrufer eine [BrokeredMessage][] anfordert. Sie müssen also nicht lernen, wie Sie diese Nachrichten empfangen und ignorieren. An jede Entität (eindeutige Warteschlange oder Thema) pro [MessagingFactory][]-Instanz pro Client wird eine Ping-Nachricht gesendet, wenn diese als nicht verfügbar angesehen werden. Dies geschieht standardmäßig einmal pro Minute. Ping-Nachrichten gelten als reguläre Service Bus-Nachrichten und können Kosten für Bandbreite und Nachrichtentransport verursachen. Sobald die Clients erkennen, dass das System verfügbar ist, werden die Nachrichten gestoppt.

## Siphon

Mindestens ein ausführbares Programm in der Anwendung sollte den Siphon aktiv ausführen. Der Siphon führt einen langen Abfrage- und Empfangsvorgang durch, der 15 Minuten dauert. Wenn alle Entitäten verfügbar sind und Sie über zehn Backlog-Warteschlangen verfügen, ruft die Anwendung, die den Siphon hostet, den Empfangsvorgang 40-mal pro Stunde, 960-mal pro Tag und 28.800-mal in 30 Tagen auf. Wenn der Siphon aktiv Nachrichten aus der Backlog-Warteschlange in die primäre Warteschlange verschiebt, fallen für jede Nachricht die folgenden Gebühren an (Standardgebühren für Nachrichtengröße und Bandbreite gelten in allen Phasen):

1.  Senden an die Backlog-Warteschlange

2.  Empfangen von der Backlog-Warteschlange

3.  Senden an die primäre Warteschlange

4.  Empfangen von der primären Warteschlange

## Schließ-/Fehlerverhalten

Innerhalb einer Anwendung, die den Siphon hostet, wird in folgenden Fällen der Siphon aktiviert: Sobald für das primäre oder sekundäre [MessagingFactory][]-Element ein Fehler auftritt oder sobald es geschlossen wird, ohne dass für das Partnerelement auch einer dieser Vorgänge eintritt, und dieser Zustand vom Siphon erkannt wird. Wenn das andere [MessagingFactory][]-Element nicht innerhalb von fünf Sekunden geschlossen wird, generiert der Siphon für das noch geöffnete [MessagingFactory][]-Element einen Fehler.

## Nächste Schritte

Ausführliche Informationen zum asynchronen Service Bus-Messaging finden Sie unter [Asynchrone Messagingmuster und hohe Verfügbarkeit][].

  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [SendAvailabilityPairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [MessageSender]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagesender.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [FailoverInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.failoverinterval.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [TimeoutException]: https://msdn.microsoft.com/library/azure/system.timeoutexception.aspx
  [BrokeredMessage]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [TimeSpan]: https://msdn.microsoft.com/library/azure/system.timespan.aspx
  [PingPrimaryInterval]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.pingprimaryinterval.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [ContentType]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.contenttype.aspx
  [TimeToLive]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.brokeredmessage.timetolive.aspx
  [Asynchrone Messagingmuster und hohe Verfügbarkeit]: service-bus-async-messaging.md
  [0]: ./media/service-bus-paired-namespaces/IC673405.png
  [1]: ./media/service-bus-paired-namespaces/IC673406.png
  [2]: ./media/service-bus-paired-namespaces/IC673407.png

<!---HONumber=AcomDC_0323_2016-->