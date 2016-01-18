<properties 
   pageTitle="Automatische Weiterleitung von Service Bus-Messagingentitäten | Microsoft Azure"
   description="Beschreibt, wie eine Warteschlange oder ein Abonnement mit einer anderen Warteschlange oder einem Thema verkettet wird, die bzw. das Teil desselben Dienstnamespaces ist."
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

# Verketten von Service Bus-Entitäten mit automatischer Weiterleitung

Das Feature *Automatische Weiterleitung* ermöglicht es Ihnen, eine Warteschlange oder ein Abonnement mit einer anderen Warteschlange oder einem Thema zu verketten, die bzw. das Teil desselben Dienstnamespaces ist. Wenn die automatische Weiterleitung aktiviert ist, entfernt Service Bus die Nachrichten automatisch, die in der ersten Warteschlange oder dem Abonnement (Quelle) platziert wurden, und fügt sie in die zweite Warteschlange oder das Thema (Ziel) ein. Beachten Sie, dass es weiterhin möglich ist, eine Nachricht direkt an die Zielentität zu senden. Beachten Sie außerdem, dass es nicht möglich, eine Unterwarteschlange, z. B. eine Warteschlange für unzustellbare Nachrichten, mit einer anderen Warteschlange oder einem Thema zu verketten.

## Verwenden der automatischen Weiterleitung

Sie können die automatische Weiterleitung durch Festlegen der Eigenschaften [QueueDescription.ForwardTo][] oder [SubscriptionDescription.ForwardTo][] der Objekte [QueueDescription][] oder [SubscriptionDescription][] für die Quelle aktivieren, wie im folgenden Beispiel veranschaulicht.

```
SubscriptionDescription srcSubscription = new SubscriptionDescription (srcTopic, srcSubscriptionName);
srcSubscription.ForwardTo = destTopic;
namespaceManager.CreateSubscription(srcSubscription));
```

Die Zielentität muss vorhanden sein, wenn die Quellentität erstellt wird. Wenn die Zielentität nicht vorhanden ist, gibt Service Bus eine Ausnahme zurück, wenn die Quellentität erstellt werden soll.

Sie können die automatische Weiterleitung zum Skalieren eines einzelnen Themas verwenden. Service Bus beschränkt die Anzahl von Abonnements für ein bestimmtes Thema. Sie können weitere Abonnements durch Erstellen von Themen der zweiten Ebene aufnehmen. Beachten Sie, dass, selbst wenn Sie nicht durch die Service Bus-Beschränkung für die Anzahl der Abonnements gebunden sind, das Hinzufügen einer zweiten Ebene von Themen den Gesamtdurchsatz Ihres Themas verbessern kann.

![Szenario mit automatischer Weiterleitung][0]

Sie können die automatische Weiterleitung auch verwenden, um Nachrichtenabsender von den Empfängern zu entkoppeln. Betrachten Sie z. B. ein ERP-System, das aus drei Modulen besteht: Auftragsverarbeitung, Bestandsverwaltung und Kundenbeziehungsmanagement. Jedes dieser Module generiert Nachrichten, die in ein entsprechendes Thema aufgenommen werden. Alice und Bob sind Vertriebsmitarbeiter, die an allen Nachrichten interessiert sind, die sich auf ihre Kunden beziehen. Zum Empfangen dieser Nachrichten erstellen Alice und Bob eine persönliche Warteschlange und ein Abonnement für jedes der ERP-Themen, das automatisch alle Nachrichten an ihre Warteschlange weiterleitet.

![Szenario mit automatischer Weiterleitung][1]

Wenn Alice im Urlaub ist, wird ihre persönliche Warteschlange gefüllt, nicht das ERP-Thema. Da in diesem Szenario kein Vertriebsmitarbeiter Nachrichten empfangen hat, erreicht keines der ERP-Themen je das Kontingent.

## Überlegungen zur automatischen Weiterleitung

Wenn die Zielentität viele Nachrichten gesammelt hat und das Kontingent überschreitet oder wenn die Zielentität deaktiviert ist, fügt die Quellentität Nachrichten der Warteschlange für unzustellbare Nachrichten hinzu, bis Speicherplatz im Ziel vorhanden ist (oder die Entität wieder aktiviert wurde). Diese Nachrichten sind weiterhin in der Warteschlange für unzustellbare Nachrichten aktiv, daher müssen Sie sie explizit aus der Warteschlange für unzustellbare Nachrichten empfangen und verarbeiten.

Beim Verketteten einzelner Themen, um ein kombiniertes Thema mit vielen Abonnements zu erhalten, wird empfohlen, dass Sie eine moderate Anzahl von Abonnements für das Thema der ersten Ebene und viele Abonnements für Themen der zweiten Ebene nutzen. Beispiel: Ein Thema der ersten Ebene mit 20 Abonnements, von denen jedes mit einem Thema der zweiten Ebene mit 200 Abonnements verkettet ist, ermöglicht einen höheren Durchsatz als ein Thema der ersten Ebene mit 200 Abonnements, von denen jedes mit einem Thema der zweiten Ebene mit 20 Abonnements verkettet ist.

Service Bus rechnet einen Vorgang für jede weitergeleitete Nachricht ab. Beispiel: Das Senden einer Nachricht an ein Thema mit 20 Abonnements, von denen jedes so konfiguriert ist, dass Nachrichten automatisch an eine andere Warteschlange oder ein Thema weitergeleitet werden, wird als 21 Vorgänge abgerechnet, wenn alle Abonnements der ersten Ebene eine Kopie der Nachricht erhalten.

Um ein Abonnement zu erstellen, das mit einer anderen Warteschlange oder einem Thema verkettet ist, muss der Ersteller des Abonnements über die Berechtigung **Verwalten** für die Quell- und die Zielentität verfügen. Das Senden von Nachrichten an das Quellthema erfordert nur die Berechtigung **Senden** für das Quellthema.

## Nächste Schritte

Detaillierte Informationen über die automatische Weiterleitung finden Sie in den folgenden Themen:

- [SubscriptionDescription.ForwardTo][]
- [QueueDescription][]
- [SubscriptionDescription][]

Weitere Informationen zu Service Bus-Leistungssteigerungen finden Sie unter [Partitionierte Messagingentitäten][].

  [QueueDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.forwardto.aspx
  [SubscriptionDescription.ForwardTo]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.forwardto.aspx
  [QueueDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.aspx
  [SubscriptionDescription]: https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.subscriptiondescription.aspx
  [0]: ./media/service-bus-auto-forwarding/IC628631.gif
  [1]: ./media/service-bus-auto-forwarding/IC628632.gif
  [Partitionierte Messagingentitäten]: service-bus-partitioning.md

<!---HONumber=AcomDC_0107_2016-->