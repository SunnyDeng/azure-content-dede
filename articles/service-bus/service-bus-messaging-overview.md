<properties
	pageTitle="Übersicht über Service Bus Messaging - Azure"
	description="Service Bus Messaging: Flexible Datenübermittlung in der Cloud"
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
	ms.date="07/02/2015"
	ms.author="sethm"/>


# Service Bus Messaging: Flexible Datenübermittlung in der Cloud

Service Bus Messaging ist ein zuverlässiger Dienst für die Übermittlung von Informationen. Der Zweck dieses Dienstes ist es, die Kommunikation zu erleichtern. Wenn zwei oder mehr Parteien Informationen austauschen möchten, benötigen sie einen Kommunikationsmechanismus. Service Bus Messaging ist ein Kommunikationsmechanismus, der auf einem Broker oder Drittanbieter basiert. Der Dienst funktioniert ähnlich wie ein Postdienst in der realen Welt. Über Postdienste lassen sich verschiedene Arten von Briefen und Paketen mit unterschiedlichen Übermittlungsgarantien überall in der Welt versenden.

Bei Azure Service Bus Messaging geht es, ähnlich wie bei einem Postdienst, der Briefe zustellt, um eine flexible Übermittlung von Informationen zwischen Absender und Empfänger. Der Messagingdienst stellt sicher, dass die Information übermittelt wird, selbst wenn Absender und Empfänger niemals gleichzeitig online oder zum exakt gleichen Zeitpunkt verfügbar sind. Messaging entspricht also dem Senden eines Briefes. Eine Kommunikation ohne Broker dagegen lässt sich eher mit einem Telefonanruf vergleichen (zumindest Anrufen in früheren Zeiten, als es noch keine Anklopffunktion und Anruferkennung gab - diese Funktionen entsprechen eher der brokerbasierten Nachrichtenübermittlung).

Der Absender der Nachricht kann auch eine Vielzahl von Übermittlungsmerkmalen anfordern, wie z. B. Transaktionen, Duplikaterkennung, Ablaufdatum und -uhrzeit und Batchverarbeitung. Auch diese Muster weisen Analogien mit einem Postdienst auf: wiederholte Übermittlung, erforderliche Signatur, Adressänderung oder Rückruf.

Service Bus Messaging verfügt über zwei separate Funktionen: Warteschlangen und Themen. Beide Nachrichtenfunktionen unterstützen alle oben genannten Konzepte - und noch vieles mehr. Der Hauptunterschied besteht darin, dass Themen Funktionen zum Veröffentlichen/Abonnieren unterstützen, die für anspruchsvolle inhaltsbasierte Routing- und Übermittlungslogik verwendet werden können, wie z. B. zum Senden an mehrere Empfänger.

## Nächste Schritte

Weitere Informationen zu Service Bus Messaging finden Sie in folgenden Themen.

- [Übersicht über die Architektur von Azure Service Bus](fundamentals-service-bus-hybrid-solutions.md)

- [Verwenden von Service Bus-Warteschlangen](service-bus-dotnet-how-to-use-queues.md)

- [Verwenden von Service Bus-Themen](service-bus-dotnet-how-to-use-topics-subscriptions.md)
 

<!---HONumber=August15_HO6-->