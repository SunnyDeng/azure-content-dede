<properties
	pageTitle="Übersicht über Service Bus Premium- und Standard-Preisstufen für Messaging | Microsoft Azure"
	description="Servicebus Premium- und Standard-Messaging"
	services="service-bus"
	documentationCenter=".net"
	authors="djrosanova"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="09/03/2015"
	ms.author="sethm"/>

# Servicebus Premium- und Standard-Preisstufe für Messaging 

Service Bus-Brokermessaging, das Messagingentitäten wie Warteschlangen und Themen umfasst, kombiniert Messagingfunktionen für Unternehmen mit umfangreicher Veröffentlichen/Abonnieren-Semantik auf Cloudebene. Service Bus-Brokermessaging wird als Kommunikationsbackbone für zahlreiche komplexe Cloudlösungen verwendet.

Die Einführung der *Premium*-Preisstufe von Service Bus-Messaging ist für allgemeine Kundenanfragen zur Skalierung, Leistung und Verfügbarkeit für unternehmenswichtige Anwendungen vorgesehen. Obwohl die Funktionssätze fast identisch sind, sind diese zwei Stufen von Azure Service Bus-Messaging für verschiedene Anwendungsfälle vorgesehen.

In der folgenden Tabelle sind einige allgemeine Unterschiede hervorgehoben.

| Premium | Standard |
|---------------------------------------|--------------------------------|
| Hoher Durchsatz | Variabler Durchsatz |
| Vorhersagbare Leistung | Variable Latenzzeit |
| Vorhersagbare Preise | Variable Preisgestaltung (nutzungsbasierte Bezahlung) |
| Möglichkeit zur Herauf- und Herunterskalierung der Arbeitslast | N/V |

**Azure Service Bus Premium-Messaging** bietet Ressourcenisolierung auf der CPU- und Arbeitsspeicherebene, sodass die Arbeitslasten der einzelnen Kunden isoliert ausgeführt werden. Dieser Ressourcencontainer wird als *Messaging-Einheit* bezeichnet. Jedem Premium-Namespace wird mindestens eine Messaging-Einheit zugeordnet. Sie können 1, 2 oder 4 Messaging-Einheiten für jeden Service Bus Premium-Namespace erwerben. Eine einzelne Arbeitslast oder Entität kann mehrere Messaging-Einheiten umfassen, und die Anzahl der Einheiten kann beliebig geändert werden. Gebühren werden jedoch im 24-Stunden- bzw. Tagesrhythmus berechnet. Das Ergebnis ist eine vorhersehbare und wiederholbare Leistung Ihrer Service Bus-basierten Lösung.

Diese Leistung ist nicht nur besser vorhersehbar und verfügbar, sondern auch schneller. Azure Service Bus Premium-Messaging basiert auf dem in [Azure Event Hubs](http://azure.microsoft.com/services/event-hubs/) eingeführten Speichermodul. Mit Premium-Messaging wird bei Spitzenleistung eine viel höhere Geschwindigkeit erzielt, als bei der Standard-Stufe.

## Premium-Messaging – technische Unterschiede

Es folgen einige Unterschiede zwischen der Premium- und der Standard-Messagingstufe.

### Partitionierte Entitäten

Partitionierte Entitäten werden beim Premium-Messaging unterstützt, funktionieren allerdings anders als bei den Standard- und Basic-Ebenen des Service Bus-Messaging. Bei Premium-Messaging wird nicht SQL als Datenspeicher verwendet, und im Gegensatz zu einer gemeinsam verwendeten Plattform können Ressourcen nicht mehr gleichzeitig verwendet werden. Daher ist eine Partitionierung nicht erforderlich. Darüber hinaus wurde die Anzahl der Partitionen von 16 bei Standard-Messaging zu zwei Partitionen bei Premium-Messaging geändert. Bei 2 Partitionen ist die Verfügbarkeit sichergestellt, und diese Anzahl eignet sich besser für die Premium-Laufzeitumgebung. Weitere Informationen finden Sie unter [Partitionieren von Nachrichtenentitäten](https://msdn.microsoft.com/library/dn520246.aspx).

### Expressentitäten

Da Premium-Messaging in einer vollständig isolierten Laufzeitumgebung ausgeführt wird, sind keine Expressentitäten mehr erforderlich. Folglich werden Expressentitäten in Premium-Namespaces nicht unterstützt. Weitere Informationen zur Expressfunktion finden Sie in der [Microsoft.ServiceBus.Messaging.QueueDescription.EnableExpress](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.queuedescription.enableexpress.aspx)-Eigenschaft.

## Nächste Schritte

Weitere Informationen zu Service Bus Messaging finden Sie in folgenden Themen.

- [Einführung in Azure Service Bus Premium-Messaging (Blogbeitrag)](http://azure.microsoft.com/blog/introducing-azure-service-bus-premium-messaging/)
- [Einführung in Azure Service Bus Premium-Messaging (Channel9)](https://channel9.msdn.com/Blogs/Subscribe/Introducing-Azure-Service-Bus-Premium-Messaging)
- [Übersicht über Service Bus-Messaging](service-bus-messaging-overview.md)
- [Übersicht über die Architektur von Azure Service Bus](fundamentals-service-bus-hybrid-solutions.md)
- [Verwenden von Service Bus-Warteschlangen](service-bus-dotnet-how-to-use-queues.md)

<!---HONumber=September15_HO1-->