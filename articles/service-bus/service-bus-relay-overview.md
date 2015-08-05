<properties
	pageTitle="Service Bus-Relaymessaging"
	description="Übersicht über Service Bus Relay"
	services="service-bus"
	documentationCenter=".net"
	authors="sethmanheim"
	manager="timlt"
	editor=""/>

<tags
	ms.service="service-bus"
	ms.workload="tbd"
	ms.tgt_pltfrm="na"
	ms.devlang="multiple"
	ms.topic="article"
	ms.date="06/04/2015"
	ms.author="sethm"/>


# Service Bus-Relaymessaging

Die zentrale Komponente von Service Bus ist ein zentralisierter (aber hinsichtlich der Leistung hochgradig ausgeglichener) Relaydienst, der Ihnen die Erstellung von Hybridanwendungen ermöglicht, die sowohl in einem Azure-Rechenzentrum als auch in Ihrer eigenen lokalen Unternehmensumgebung ausgeführt werden. Der Relaydienst unterstützt eine Vielzahl verschiedener Transportprotokolle und Webdienststandards. Dies schließt SOAP, WS-* und sogar REST ein. Der Service Bus-Relaydienst unterstützt Ihre Hybridanwendungen, indem er Ihnen die Möglichkeit bietet, WCF-Dienste (Windows Communication Foundation), die sich in einem Unternehmensnetzwerk befinden, sicher in der öffentlichen Cloud bereitzustellen, ohne dass eine Firewallverbindung geöffnet werden muss oder intrusive Änderungen an der unternehmensinternen Netzwerkinfrastruktur erforderlich werden.

![Relaykonzepte](./media/service-bus-relay-overview/sb-relay-01.png)

Der Relaydienst unterstützt herkömmliches unidirektionales Messaging, Anforderungs-/Antwortmessaging und Peer-to-Peer-Messaging. Er unterstützt außerdem Ereignisverteilung im Internetumfang, um Veröffentlichen-/Abonnieren-Szenarios und bidirektionale Socketkommunikation für erhöhte Punkt-Effizienz zu ermöglichen.

Im Relaymessaging stellt ein lokaler Dienst eine Verbindung zu dem Relaydienst über einen ausgehenden Port her und erstellt einen bidirektionalen Socket für die mit einer bestimmten Rendezvous-Adresse gekoppelte Kommunikation. Der Client kann dann mit dem lokalen Dienst durch das Senden von Nachrichten an den Relaydienst unter Verwendung der Rendezvousadresse kommunizieren. Der Relaydienst leitet dann Nachrichten an den lokalen Dienst über den bidirektionalen, bereits vorhandenen Socket weiter. Der Client benötigt keine direkte Verbindung zum lokalen Dienst, muss nicht wissen, wo der Dienst angesiedelt ist, und für den lokalen Dienst sind keine geöffneten eingehenden Ports in der Firewall notwendig.

Sie initiieren die Verbindung zwischen dem lokalen Dienst und dem Relaydienst mithilfe einer Sammlung von WCF-"Relay"-Bindungen. Im Prinzip werden die Relaybindungen neuen Transportbindungselementen zugeordnet, die entwickelt wurden, um WCF-Kanalkomponenten zu erstellen, die mit Service Bus in der Cloud integriert werden.

## Nächste Schritte

Weitere Informationen zu Service Bus Relay finden Sie in folgenden Themen.

- [Übersicht über die Architektur von Azure Service Bus](fundamentals-service-bus-hybrid-solutions.md)

- [Verwenden des Service Bus Relay-Diensts](service-bus-dotnet-how-to-use-relay.md)

 

<!---HONumber=July15_HO4-->