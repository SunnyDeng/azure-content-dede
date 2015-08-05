
<properties 
   pageTitle="Übersicht über internen Lastenausgleich | Microsoft Azure"
   description="Übersicht über den internen Lastenausgleich und seine Funktionen. Funktionsweise eines Lastenausgleichs für Azure und mögliche Szenarios zum Konfigurieren interner Endpunkte"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="05/01/2015"
   ms.author="joaoma" />


# Interner Lastenausgleich (Übersicht)

Der interne Lastenausgleich (ILB) stellt eine Sicherheitsverbesserung gegenüber dem aktuellen Lastenausgleich in Azure dar. Zugriff auf den ILB kann nur über Ressourcen im Clouddienst oder über VPN für den Zugriff auf die Azure-Infrastruktur erfolgen.
			
Die Infrastruktur schränkt den Zugriff ein und erstellt eine Vertrauensgrenze zwischen virtuellen IP-Adressen mit Lastenausgleich und Clouddienst oder einem virtuellen Netzwerk, und sie wird nie direkt einem Internetendpunkt verfügbar gemacht. Dies ermöglicht die Ausführung interner Line-of-Business-Anwendungen in Azure und Zugriff in der Cloud oder lokal.

## Szenarios für den internen Lastenausgleich

Sie können den ILB in zahlreichen neuen Konfigurationen verwenden, darunter die folgenden:

Der interne Azure-Lastenausgleich (ILB) ermöglicht Lastenausgleich zwischen virtuellen Computern in einem Clouddienst oder virtuellen Netzwerk mit regionalem Umfang. Informationen zur Verwendung und Konfiguration virtueller Netzwerke mit regionalem Umfang finden Sie unter [Regionale virtuelle Netzwerke](http://azure.microsoft.com/blog/2014/05/14/regional-virtual-networks/) im Azure-Blog. Vorhandene virtuelle Netzwerke, die für eine Affinitätsgruppe konfiguriert wurden, können kein ILB verwenden.

ILB aktiviert die folgenden neuen Arten des Lastenausgleichs:

- Innerhalb eines Clouddiensts von virtuellen Computern zu einer Gruppe von virtuellen Computern, die sich im selben Clouddienst befinden (siehe Abbildung 1).

- In einem virtuellen Netzwerk von virtuellen Computern im virtuellen Netzwerk zu einer Gruppe von virtuellen Computern im selben Clouddienst des virtuellen Netzwerks (siehe Abbildung 2).

- In einem standortübergreifenden virtuellen Netzwerk von lokalen Computern zu einer Gruppe von virtuellen Computern im selben Clouddienst des virtuellen Netzwerks (siehe Abbildung 3).

Der vorhandene Azure-Lastenausgleich bietet nur Lastenausgleich zwischen internetbasierten Computern und virtuellen Computern in einem Clouddienst. ILB ermöglicht neue Funktionen zum Hosten von virtuellen Computern in Azure.

- Internetanwendungen mit mehreren Ebenen, bei denen die Back-End-Ebenen keine Internetanbindung haben, jedoch Lastenausgleich für Datenverkehr für die mit dem Internet verbundene Ebene erfordern.
- Lastenausgleich für Branchenanwendungen (LOB-Anwendungen), die in Azure gehostet werden, ohne dass zusätzliche Hardware oder Software für den Lastenausgleich erforderlich ist. Einbeziehen von lokalen Servern in die Gruppe der Computer, für deren Datenverkehr Lastenausgleich stattfindet. 
- In den folgenden Abschnitten werden diese Konfigurationen ausführlich beschrieben.

## Anwendungen mit mehreren Ebenen mit Internetanbindung


Die Webebene verfügt über Endpunkte mit Internetanbindung für Internetclients und ist Teil einer Gruppe mit Lastenausgleich. Der Lastenausgleich verteilt den eingehenden Datenverkehr von Webclients für TCP-Port 443 (HTTPS) an den Webserver.

Die Datenbankserver befinden sich hinter einem ILB-Endpunkt, den die Webserver zur Speicherung verwenden. Dieser Datenbankdienst-Endpunkt mit Lastenausgleich, für dessen Datenverkehr Lastenausgleich auf den Datenbankservern in der ILB-Gruppe ausgeführt wird.

Im Bild unten ist die Internetanwendung mit mehreren Ebenen im selben Clouddienst dargestellt.

Abbildung 1

![Interner Lastenausgleich eines einzelnen Clouddiensts](./media/load-balancer-internal-overview/IC736321.png)

Ein anderes mögliches Szenario für eine Anwendung mit mehreren Ebenen ist eine Bereitstellung des ILB in einem anderen Clouddienst als jenem, der für den ILB-Dienst verwendet wird.

Clouddienste mit im gleichen virtuellen Netzwerk haben Zugriff auf den ILB-Endpunkt.

In der folgenden Abbildung ist zu sehen, dass sich die Front-End-Webserver in einem anderen Clouddienst befinden als das Datenbank-Back-End und dass sie einen ILB-Endpunkt im gleichen virtuellen Netzwerk nutzen.

Abbildung 2

![Interner Lastenausgleich zwischen Clouddiensten](./media/load-balancer-internal-overview/IC744147.png)

## Intranet-LOB-Anwendungen

Für Datenverkehr von Clients im lokalen Netzwerk wird in einer Gruppe von LOB-Servern mithilfe der VPN-Verbindung mit dem Azure-Netzwerk Lastenausgleich ausgeführt.

Der Clientcomputer hat Zugriff auf eine IP-Adresse des Azure-VPN-Diensts mit Punkt-zu-Standort-VPN. Er lässt die Verwendung der LOB-Anwendung zu, die hinter dem ILB-Endpunkt gehostet wird.


![Interner Lastenausgleich mit Punkt-zu-Standort-VPN](./media/load-balancer-internal-overview/IC744148.png)

Ein weiteres Szenario für LOB ist ein Standort-zu-Standort-VPN mit dem virtuellen Netzwerk, in dem der ILB-Endpunkt konfiguriert ist. Dies ermöglicht lokalen Netzwerkdatenverkehr zum ILB-Endpunkt.

![Interner Lastenausgleich mit Standort-zu-Standort-VPN](./media/load-balancer-internal-overview/IC744150.png)


## Nächste Schritte

[Erste Schritte zum Konfigurieren des Lastenausgleichs für Internetverbindungen](load-balancer-internet-getstarted.md)

[Erste Schritte zum Konfigurieren des internen Lastenausgleichs](load-balancer-internal-getstarted.md)

[Konfigurieren eines Lastenausgleichs-Verteilungsmodus](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)

 

<!---HONumber=July15_HO4-->