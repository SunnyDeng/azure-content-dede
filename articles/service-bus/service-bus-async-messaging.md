<properties 
   pageTitle="Asynchrones Messaging mit Service Bus | Microsoft Azure"
   description="Beschreibung des asynchronen Brokermessagings mit Service Bus."
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

# Asynchrone Messagingmuster und hohe Verfügbarkeit

Asynchrones Messaging kann auf verschiedenen Arten implementiert werden. Mit Warteschlangen, Themen und Abonnements, die zusammen als Messagingentitäten bezeichnet werden, unterstützt Azure Service Bus Asynchronität über einen Speicher- und Weiterleitungsmechanismus. Im normalen (synchronen) Betrieb senden Sie Nachrichten an Warteschlangen und Themen und erhalten Nachrichten von Warteschlangen und Abonnements. Anwendungen, die Sie schreiben, hängen davon ab, dass diese Entitäten immer verfügbar sind. Wenn sich der Zustand der Entität aufgrund von verschiedenen Umständen ändert, benötigen Sie eine Möglichkeit, eine Entität mit geringerer Funktionalität bereitzustellen, die die meisten Anforderungen erfüllen kann.

Anwendungen verwenden typischerweise asynchrone Messagingmuster, um eine Reihe von Kommunikationsszenarien zu ermöglichen. Sie können Anwendungen erstellen, in denen Clients Nachrichten an Dienste senden können, selbst wenn der Dienst nicht ausgeführt wird. Für Anwendungen mit Kommunikationsspitzen kann eine Warteschlange dabei helfen, die Last durch Bereitstellen eines Puffers für die Kommunikation auszugleichen. Schließlich erhalten Sie einen einfachen und effektiven Lastenausgleich, um Nachrichten auf mehrere Computer zu verteilen.

Um die Verfügbarkeit dieser Entitäten zu gewährleisten, sollten Sie eine Reihe von Möglichkeiten in Betracht ziehen, in denen diese Entitäten für ein langlebiges Messagingsystem als nicht verfügbar erscheinen können. Im Allgemeinen ist die Entität für Anwendungen, die wir schreiben, auf folgende Arten nicht mehr verfügbar:

1.  Nachrichten können nicht gesendet werden.

2.  Nachrichten können nicht empfangen werden.

3.  Entitäten können nicht verwaltet werden (Erstellen, Abrufen, Aktualisieren oder Löschen von Entitäten).

4.  Mit dem Dienst kann keine Verbindung hergestellt werden.

Für jeden dieser Fehler sind verschiedene Fehlermodi vorhanden, durch die eine Anwendung mit geringerer Funktionalität Arbeiten weiterhin erledigen kann. Beispiel: Ein System, das Nachrichten senden, jedoch nicht empfangen kann, kann weiterhin Aufträge von Kunden erhalten, diese Aufträge aber nicht verarbeiten. In diesem Thema werden mögliche Probleme behandelt, die auftreten können, und wie diese Probleme gemindert werden. Mit Service Bus wurde eine Reihe von Lösungen eingeführt, die Sie abonnieren müssen, und in diesem Thema werden auch die Regeln erläutert, die die Verwendung dieser abonnierten Lösungen steuern.

## Zuverlässigkeit in Service Bus

Es gibt mehrere Arten, Probleme mit Nachrichten und Entitäten zu behandeln, und Richtlinien regeln die entsprechende Verwendung dieser Lösungen. Um die Richtlinien zu verstehen, müssen Sie zuerst verstehen, welche Fehler in Service Bus auftreten können. Aufgrund des Entwurfs von Azure-Systemen sind diese Probleme häufig kurzlebig. Auf allgemeiner Ebene treten die folgenden Gründe für Nichtverfügbarkeit auf:

-   Drosselung durch ein externes System, von dem Service Bus abhängt. Die Drosselung ergibt sich durch Interaktionen mit Speicher- und Computeressourcen.

-   Problem für ein System, von dem Service Bus abhängt. Beispielsweise können in einem bestimmten Teil des Speichers Probleme auftreten.

-   Ausfall von Service Bus in einem einzelnen Subsystem. In diesem Fall kann auf dem Computeknoten ein inkonsistenter Zustand auftreten, und er muss neu gestartet werden, wodurch für alle Entitäten, die von dem Knoten verarbeitet werden, ein Lastenausgleich auf andere Knoten ausgeführt wird. Dies kann wiederum für einen kurzen Zeitraum zu einer langsamen Nachrichtenverarbeitung führen.

-   Ausfall von Service Bus in einem Azure-Datencenter. Dies ist der klassische „schwerwiegende Fehler“, durch den das System für viele Minuten oder einige Stunden nicht erreichbar ist.

> [AZURE.NOTE] Mit dem Begriff **Speicher** kann Azure-Speicher und SQL Azure gemeint sein.

Service Bus enthält eine Reihe von Entschärfungen für diese Probleme. In den folgenden Abschnitten werden die einzelnen Probleme und die entsprechenden Lösungen beschrieben.

### Drosselung

Mit Service Bus ermöglicht die Drosselung eine kooperative Nachrichtenratenverwaltung. Jeder einzelne Service Bus-Knoten enthält viele Entitäten. Jede dieser Entitäten stellt die Anforderungen an das System in Bezug auf CPU, Arbeitsspeicher, Speicher und andere Facets. Wenn für diese Facets eine Nutzung ermittelt wird, die definierte Schwellenwerte überschreitet, kann Service Bus eine bestimmte Anforderung verweigern. Der Aufrufer empfängt [ServerBusyException][] und führt nach 10 Sekunden einen Wiederholungsversuch durch.

Als Lösung muss der Code den Fehler lesen und alle Wiederholungsversuche für die Nachricht für mindestens 10 Sekunden anhalten. Da der Fehler auf Teilen der Kundenanwendung auftreten kann, wird erwartet, dass die Wiederholungslogik auf allen Teilen unabhängig ausgeführt wird. Der Code kann die Wahrscheinlichkeit der Drosselung durch Aktivierung der Partitionierung für eine Warteschlange oder ein Thema reduzieren.

### Problem für eine Azure-Abhängigkeit

Bei anderen Komponenten in Azure können gelegentlich Dienstprobleme auftreten. Beispielsweise kann bei der Aktualisierung eines Systems, das Service Bus verwendet, das System vorübergehend eine geringere Funktionalität aufweisen. Um derartige Probleme zu umgehen, untersucht und implementiert Service Bus regelmäßig Lösungen. Bei diesen Lösungen treten Nebeneffekte auf. Beispielsweise implementiert Service Bus zur Behandlung vorübergehender Speicherprobleme ein System, mit dem Nachrichtensendevorgänge einheitlich arbeiten können. Aufgrund der Natur der Lösung kann es bis zu 15 Minuten dauern, bis eine gesendete Nachricht in der betroffenen Warteschlange oder im Abonnement angezeigt wird und für einen Empfangsvorgang bereit ist. Im Allgemeinen tritt dieses Problem bei den meisten Entitäten nicht auf. Allerdings ist angesichts der Anzahl von Entitäten in Service Bus in Azure diese Lösung manchmal für eine kleine Teilmenge der Service Bus-Kunden erforderlich.

### Ausfall von Service Bus in einem einzelnen Subsystem

Bei jeder Anwendung können Umstände dazu führen, dass eine interne Komponente von Service Bus nicht mehr konsistent ist. Wenn Service Bus dies erkennt, werden Daten von der Anwendung erfasst, um die Diagnose des Problems zu unterstützen. Nachdem die Daten erfasst wurden, wird die Anwendung neu gestartet, um zu versuchen, einen konsistenten Status wiederherzustellen. Dieser Prozess geschieht ziemlich schnell und führt dazu, dass eine Entität für einige Minuten nicht verfügbar zu sein scheint, obwohl die normalen Ausfallzeiten viel kürzer sind.

In diesen Fällen generiert die Clientanwendung eine Ausnahme des Typs [System.TimeoutException][] oder [MessagingException][]. Service Bus .NET SDK enthält eine Lösung zur Behebung dieses Problems in Form einer automatisierten Clientwiederholungslogik. Wenn der Wiederholungszeitraum beendet ist und die Nachricht nicht zugestellt wurde, können Sie die Untersuchung mit anderen Features wie [gekoppelten Namespaces][] durchführen. Für gekoppelte Namespaces geltende Einschränkungen werden im Artikel [Details zur Implementierung von gekoppelten Namespaces und Kostenaspekte](service-bus-paired-namespaces.md) behandelt.

### Ausfall von Service Bus in einem Azure-Datencenter

Der wahrscheinlichste Grund für einen Ausfall in einem Azure-Datencenter ist eine fehlgeschlagene Aktualisierungsbereitstellung von Service Bus oder eines abhängigen Systems. Mit zunehmender Reife der Plattform verringert sich die Wahrscheinlichkeit für diese Art von Ausfall. Ein Datencenterausfall kann auch aus den folgenden Gründen auftreten:

-   Stromausfall (Stromversorgung und Stromerzeugung sind nicht mehr vorhanden).
-   Konnektivität (Unterbrechung der Internetverbindung zwischen den Clients und Azure).

In beiden Fällen verursacht ein vom Menschen gemachter Notfall das Problem. Um dieses Problem zu umgehen und sicherzustellen, dass Sie weiterhin Nachrichten senden können, können Sie mithilfe [gekoppelter Namespaces][] Nachrichten an einen zweiten Standort senden, während der primäre Standort wieder in einen fehlerfreien Zustand versetzt wird. Weitere Informationen finden Sie unter [Bewährte Methoden zum Schützen von Anwendungen vor Service Bus-Ausfällen und Notfällen][].

## Gekoppelte Namespaces

Das Feature für [gekoppelte Namespaces][] unterstützt Szenarien, in denen eine Service Bus-Entität oder -Bereitstellung in einem Datencenter nicht mehr zur Verfügung steht. Zwar tritt dieses Ereignis nur selten auf, verteilte Systeme müssen jedoch immer vorbereitet sein, den ungünstigsten Fall zu bewältigen. In der Regel tritt dieses Ereignis auf, weil sich bei einem Element, von dem Service Bus abhängt, ein vorübergehendes Problem ergeben hat. Um die Verfügbarkeit der Anwendung bei einem Ausfall zu gewährleisten, können Service Bus-Benutzer zwei separate Namespaces verwenden, vorzugsweise in getrennten Datencentern, die ihre Messagingentitäten hosten. Im restlichen Teil dieses Abschnitts wird die folgende Terminologie verwendet:

-   Primärer Namespace: Der Namespace, mit dem die Anwendung für Sende- und Empfangsvorgänge interagiert.

-   Sekundärer Namespace: Der Namespace, der als Ersatz für den primären Namespace fungiert. Die Anwendungslogik interagiert mit diesem Namespace nicht.

-   Failoverintervall: Die Zeitspanne, für die normale Fehler akzeptiert werden, bevor die Anwendung vom primären Namespace zum sekundären Namespace wechselt.

Gekoppelte Namespaces unterstützen *Sendeverfügbarkeit*. Die Sendeverfügbarkeit sorgt dafür, dass die Möglichkeit zum Senden von Nachrichten erhalten bleibt. Um die Sendeverfügbarkeit nutzen zu können, muss Ihre Anwendung die folgenden Anforderungen erfüllen:

1.  Nachrichten werden nur vom primären Namespace empfangen.

2.  An eine bestimmte Warteschlange oder ein bestimmtes Thema gesendete Nachrichten können ungeordnet ankommen.

3.  Wenn Ihre Anwendung Sitzungen verwendet, können Nachrichten in einer Sitzung ungeordnet ankommen. Dies entspricht nicht der normalen Funktionalität von Sitzungen. Es bedeutet, dass Ihre Anwendung Sitzungen verwendet, um Nachrichten logisch zu gruppieren. Der Sitzungszustand wird nur für den primären Namespace verwaltet.

4.  Nachrichten in einer Sitzung können ungeordnet ankommen. Dies entspricht nicht der normalen Funktionalität von Sitzungen. Es bedeutet, dass Ihre Anwendung Sitzungen verwendet, um Nachrichten logisch zu gruppieren.

5.  Der Sitzungszustand wird nur für den primären Namespace verwaltet.

6.  Die primäre Warteschlange kann online geschaltet werden und damit beginnen, Nachrichten zu akzeptieren, bevor die sekundäre Warteschlange alle Nachrichten an die primäre Warteschlange übermittelt.

In den folgenden Abschnitten werden die APIs und deren Implementierung beschrieben, und Beispielcode wird gezeigt, in dem das Feature verwendet wird. Beachten Sie, dass dieses Feature Auswirkungen auf die Abrechnung hat.

### MessagingFactory.PairNamespaceAsync-API

Das Feature für gekoppelte Namespaces führt die Methode [PairNamespaceAsync][] für die Klasse [Microsoft.ServiceBus.Messaging.MessagingFactory][] ein:

```
public Task PairNamespaceAsync(PairedNamespaceOptions options);
```

Bei Abschluss der Aufgabe ist die Namespacekopplung ebenfalls beendet und kann für beliebige Elemente vom Typ [MessageReceiver][], [QueueClient][] oder [TopicClient][] verwendet werden, die mit der Instanz [MessagingFactory][] erstellt wurden. [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions][] ist die Basisklasse für die unterschiedlichen Typen von Kopplung, die mit einem Objekt vom Typ [MessagingFactory][] verfügbar sind. Derzeit heißt die einzige abgeleitete Klasse [SendAvailabilityPairedNamespaceOptions][], die Anforderungen an die Sendeverfügbarkeit implementiert. [SendAvailabilityPairedNamespaceOptions][] verfügt über einen Satz von Konstruktoren, die aufeinander aufbauen. Wenn Sie den Konstruktor mit den meisten Parametern betrachten, können Sie das Verhalten der anderen Konstruktoren verstehen.

```
public SendAvailabilityPairedNamespaceOptions(
    NamespaceManager secondaryNamespaceManager,
    MessagingFactory messagingFactory,
    int backlogQueueCount,
    TimeSpan failoverInterval,
    bool enableSyphon)
```

Diese Parameter haben folgende Bedeutung:

-   *secondaryNamespaceManager*: Eine initialisierte [NamespaceManager][]-Instanz für den sekundären Namespace, die die [PairNamespaceAsync][]-Methode zum Einrichten von sekundären Namespaces verwenden kann. Der Namespace-Manager wird verwendet, um die Liste der Warteschlangen im Namespace abzurufen und sicherzustellen, dass die erforderlichen Backlogwarteschlangen vorhanden sind. Wenn diese Warteschlangen nicht vorhanden sind, werden sie erstellt. [NamespaceManager][] muss in der Lage sein, ein Token mit dem Anspruch **Verwalten** zu erstellen.

-   *messagingFactory*: Die [MessagingFactory][]-Instanz für den sekundären Namespace. Das Objekt [MessagingFactory][] dient zum Senden und, falls die Eigenschaft [EnableSyphon][] auf **true** festgelegt ist, zum Empfangen von Nachrichten aus den Backlogwarteschlangen.

-   *backlogQueueCount*: Die Anzahl der zu erstellenden Backlogwarteschlangen. Dieser Wert muss mindestens 1 sein. Beim Senden von Nachrichten an den Backlog wird eine dieser Warteschlangen zufällig ausgewählt. Wenn Sie den Wert auf 1 festlegen, kann immer nur eine einzige Warteschlange verwendet werden. Wenn dies geschieht und die einzige Backlogwarteschlange einen Fehler generiert, kann der Client keine andere Backlogwarteschlange verwenden und Ihre Nachricht möglicherweise nicht senden. Es wird empfohlen, diesen Wert auf einen größeren Wert und standardmäßig auf 10 festzulegen. Sie können diesen Wert in einen höheren oder niedrigeren Wert ändern, abhängig von der Datenmenge, die Ihre Anwendung pro Tag sendet. Jede Backlogwarteschlange kann bis zu 5 GB an Nachrichten enthalten.

-   *failoverInterval*: Die Zeitspanne, während der Sie Fehler im primären Namespace akzeptieren, bevor eine einzelne Entität zum sekundären Namespace wechselt. Failover treten pro Entität auf. Entitäten in einem einzelnen Namespace befinden sich häufig auf verschiedenen Knoten in Service Bus. Ein Fehler in einer Entität impliziert keinen Fehler in einer anderen. Legen Sie diesen Wert auf [System.TimeSpan.Zero][] fest, damit ein Failover auf den sekundären Namespace unmittelbar nach dem ersten, dauerhaften Fehler erfolgt. Fehler, die den Failovertimer auslösen, sind alle [MessagingException][]-Elemente, bei denen die [IsTransient][]- Eigenschaft "false" ist, oder [System.TimeoutException][]. Andere Ausnahmen wie [UnauthorizedAccessException][] verursachen kein Failover, da sie angeben, dass der Client nicht ordnungsgemäß konfiguriert ist. [ServerBusyException][] verursacht kein Failover, da das richtige Muster ist, 10 Sekunden zu warten und dann die Nachricht erneut zu senden.

-   *enableSyphon*: Gibt an, dass diese bestimmte Kopplung auch Nachrichten vom sekundären Namespace zurück an den primären Namespace übertragen soll. In der Regel sollte für Anwendungen, die Nachrichten senden, dieser Wert auf **false** festgelegt werden. Für Anwendungen, die Nachrichten empfangen, sollte dieser Wert auf **true** festgelegt werden. Der Grund dafür ist, dass häufig weniger Nachrichtenempfänger als Nachrichtenabsender vorhanden sind. Abhängig von der Anzahl der Empfänger können Sie eine einzelne Anwendungsinstanz für die Übertragung verwenden. Die Verwendung vieler Empfänger hat Auswirkungen auf die Abrechnung für jede Backlogwarteschlange.

Um den Code zu verwenden, erstellen Sie eine primäre [MessagingFactory][]-Instanz, eine sekundäre [MessagingFactory][]-Instanz, eine sekundäre [NamespaceManager][]-Instanz und eine [SendAvailabilityPairedNamespaceOptions][]-Instanz. Der Aufruf kann einfach wie folgt aussehen:

```
SendAvailabilityPairedNamespaceOptions sendAvailabilityOptions = new SendAvailabilityPairedNamespaceOptions(secondaryNamespaceManager, secondary);
primary.PairNamespaceAsync(sendAvailabilityOptions).Wait();
```

Wenn die von der [PairNamespaceAsync][]-Methode zurückgegebene Aufgabe abgeschlossen wird, ist alles eingerichtet und einsatzbereit. Bevor die Aufgabe zurückgegeben wird, haben Sie möglicherweise nicht alle Hintergrundaufgaben abgeschlossen, die für das Funktionieren der Kopplung erforderlich sind. Daher sollten Sie nicht damit beginnen, Nachrichten zu senden, bis die Aufgabe zurückgegeben wird. Bei einem Fehler wie ungültigen Anmeldeinformationen oder wenn Backlogwarteschlangen nicht erstellt werden können, werden Ausnahmen ausgelöst, sobald die Aufgabe abgeschlossen ist. Sobald die Aufgabe zurückgegeben wird, überprüfen Sie, ob die Warteschlangen gefunden oder erstellt wurden, indem Sie die [BacklogQueueCount][]-Eigenschaft Ihrer [SendAvailabilityPairedNamespaceOptions][]-Instanz untersuchen. Dieser Vorgang sieht für den obigen Code folgendermaßen aus:

```
if (sendAvailabilityOptions.BacklogQueueCount < 1)
{
    // Handle case where no queues were created.
}
```

## Nächste Schritte

Nachdem Sie nun mit den Grundlagen des asynchronen Messagings in Service Bus vertraut sind, können Sie sich ausführlicher mit [gekoppelten Namespaces][] beschäftigen.

  [ServerBusyException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.serverbusyexception.aspx
  [System.TimeoutException]: https://msdn.microsoft.com/library/system.timeoutexception.aspx
  [MessagingException]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.aspx
  [Bewährte Methoden zum Schützen von Anwendungen vor Service Bus-Ausfällen und Notfällen]: service-bus-outages-disasters.md
  [Microsoft.ServiceBus.Messaging.MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [MessageReceiver]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagereceiver.aspx
  [QueueClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queueclient.aspx
  [TopicClient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.topicclient.aspx
  [Microsoft.ServiceBus.Messaging.PairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.pairednamespaceoptions.aspx
  [MessagingFactory]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.aspx
  [SendAvailabilityPairedNamespaceOptions]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.aspx
  [NamespaceManager]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.namespacemanager.aspx
  [PairNamespaceAsync]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingfactory.pairnamespaceasync.aspx
  [EnableSyphon]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.enablesyphon.aspx
  [System.TimeSpan.Zero]: https://msdn.microsoft.com/library/azure/system.timespan.zero.aspx
  [IsTransient]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.messagingexception.istransient.aspx
  [UnauthorizedAccessException]: https://msdn.microsoft.com/library/azure/system.unauthorizedaccessexception.aspx
  [BacklogQueueCount]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sendavailabilitypairednamespaceoptions.backlogqueuecount.aspx
  [gekoppelte Namespaces]: service-bus-paired-namespaces.md
  [gekoppelten Namespaces]: service-bus-paired-namespaces.md
  [gekoppelter Namespaces]: service-bus-paired-namespaces.md

<!---HONumber=AcomDC_0323_2016-->