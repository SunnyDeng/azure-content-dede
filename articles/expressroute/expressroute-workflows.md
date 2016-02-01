<properties
   pageTitle="Schritte zum Konfigurieren einer ExpressRoute-Verbindung | Microsoft Azure"
   description="Diese Seite enthält die Workflows zum Konfigurieren von ExpressRoute-Verbindungen und -Peerings."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carolz"
   editor="" />
<tags
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/21/2016"
   ms.author="cherylmc"/>

# ExpressRoute-Workflows für die Verbindungsbereitstellung und Verbindungszustände
Auf dieser Seite erhalten Sie einen Überblick über die Workflows zur Dienstbereitstellung und Routingkonfiguration.

![](./media/expressroute-workflows/expressroute-circuit-workflow.png)

Die folgende Abbildung und die zugehörigen Schritte zeigen die Aufgaben, die Sie zum vollständigen Bereitstellen einer ExpressRoute-Verbindung ausführen müssen.

1. Verwenden Sie PowerShell, um eine ExpressRoute-Verbindung zu konfigurieren. Ausführlichere Anweisungen finden Sie im Artikel [Erstellen von ExpressRoute-Verbindungen](expressroute-howto-circuit-classic.md).

2. Fordern Sie Konnektivität vom Service Provider an. Dieser Prozess variiert. Wenden Sie sich an Ihren Konnektivitätsanbieter, um weitere Details zum Anfordern der Konnektivität zu erhalten.

3. Stellen Sie sicher, dass die Verbindung erfolgreich eingerichtet wurde, indem Sie den Bereitstellungszustand der ExpressRoute-Verbindung mithilfe von PowerShell überprüfen.

4. Konfigurieren Sie Routingdomänen. Wenn Ihr Konnektivitätsanbieter Layer 3 für Sie verwaltet, konfiguriert er das Routing für Ihre Verbindung. Wenn Ihr Konnektivitätsanbieter nur Layer 2-Dienste anbietet, müssen Sie das Routing gemäß den Richtlinien auf den Seiten [Routinganforderungen](expressroute-routing.md) und [Routingkonfiguration](expressroute-howto-routing-classic.md) konfigurieren.

	-  Privates Azure-Peering aktivieren – Sie müssen dieses Peering aktivieren, um eine Verbindung mit virtuellen Computern/Clouddiensten herzustellen, die in virtuellen Netzwerken bereitgestellt werden.
	-  Öffentliches Azure-Peering aktivieren – sie müssen das öffentliche Azure-Peering aktivieren, wenn Sie eine Verbindung mit Azure-Diensten herstellen möchten, die unter öffentlichen IP-Adressen gehostet werden. Dies ist eine Voraussetzung, damit auf Azure-Ressourcen zugegriffen werden kann, wenn Sie das Standardrouting für privates Azure-Peering aktiviert haben.
	-  Microsoft-Peering aktivieren – Sie müssen das Microsoft-Peering aktivieren, um auf Office 365 und CRM-Onlinedienste zugreifen zu können. 
	
	>[AZURE.IMPORTANT]Wenn Sie das Microsoft-Peering aktivieren, stellen Sie sicher, dass das öffentliche Azure-Peering ebenfalls aktiviert ist, damit auf Azure AD zugegriffen werden kann. Verwenden Sie für die Verbindung mit Microsoft unbedingt einen anderen Proxy/Edge als für das Internet. Wenn Sie denselben Edge für ExpressRoute und das Internet verwenden, führt das zu asymmetrischem Routing und Konnektivitätsausfällen für Ihr Netzwerk.


	![](./media/expressroute-workflows/expressroute-routing-workflow.png)

5. Verknüpfen von virtuellen Netzwerken mit ExpressRoute-Verbindungen – Sie können virtuelle Netzwerke mit Ihrer ExpressRoute-Verbindung verknüpfen. Führen Sie die Schritte zum [Verknüpfen von VNets](expressroute-howto-linkvnet-arm.md) mit Ihrer Verbindung aus. Diese VNets können sich im gleichen Azure-Abonnement wie die ExpressRoute-Verbindung oder in einem anderen Abonnement befinden.


## Bereitstellungszustände von ExpressRoute-Verbindungen

Jede ExpressRoute-Verbindung hat zwei Zustände:

- Bereitstellungszustand des Service Providers
- Status

Der Status gibt den Bereitstellungszustand von Microsoft an. Diese Eigenschaft kann einen der folgenden Zustände aufweisen: *Enabled*, *Enabling* oder *Disabling*. Die ExpressRoute-Verbindung muss sich im aktivierten Zustand befinden, damit Sie sie verwenden können.

Der Bereitstellungszustand des Konnektivitätsanbieters gibt den Zustand aufseiten des Konnektivitätsanbieters an. Mögliche Zustände: *NotProvisioned*, *Provisioning* oder *Provisioned*. Die ExpressRoute-Verbindung muss den Zustand „Provisioned“ aufweisen, damit Sie sie verwenden können.

### Mögliche Zustände einer ExpressRoute-Verbindung

In diesem Abschnitt sind die möglichen Zustände für eine ExpressRoute-Verbindung aufgeführt.

#### Zum Zeitpunkt der Erstellung:

Die ExpressRoute-Verbindung weist den unten beschriebenen Zustand auf, wenn Sie das PowerShell-Cmdlet zum Erstellen der ExpressRoute-Verbindung ausführen.

	ServiceProviderProvisioningState : NotProvisioned
	Status                           : Enabled


#### Der Konnektivitätsanbieter stellt die Verbindung gerade bereit:

Die ExpressRoute-Verbindung weist den unten beschriebenen Zustand auf, wenn Sie den Dienstschlüssel an den Konnektivitätsanbieter übergeben und der Anbieter den Bereitstellungsprozess gestartet hat.

	ServiceProviderProvisioningState : Provisioning
	Status                           : Enabled


#### Der Konnektivitätsanbieter hat die Bereitstellung abgeschlossen:

Die ExpressRoute-Verbindung weist den unten beschriebenen Zustand auf, sobald der Konnektivitätsanbieter die Bereitstellung abgeschlossen hat.

	ServiceProviderProvisioningState : Provisioned
	Status                           : Enabled

Die Verbindung kann nur im Zustand „Provisioned“ und „Enabled“ verwendet werden. Wenn Sie einen Layer 2-Anbieter nutzen, können Sie das Routing für Ihre Verbindung nur konfigurieren, wenn sie sich in diesem Zustand befindet.

#### Die Bereitstellung wird zuerst aufseiten von Microsoft initiiert:

Die ExpressRoute-Verbindung weist den unten beschriebenen Zustand auf, sobald Sie das PowerShell-Cmdlet zum Löschen der ExpressRoute-Verbindung ausführen.

	ServiceProviderProvisioningState : Provisioned
	Status                           : Disabling

Sie müssen sich an Ihren Konnektivitätsanbieter wenden, wenn Sie die Bereitstellung der ExpressRoute-Verbindung aufheben möchten. **Wichtig:** Microsoft erhebt weiterhin Gebühren für die Verbindung, bis Sie mithilfe des PowerShell-Cmdlets die Bereitstellung der Verbindung aufheben.

#### Die Aufhebung der Bereitstellung wird aufseiten des Service Providers initiiert:

Wenn Sie den Service Provider aufgefordert haben, zuerst die Bereitstellung der ExpressRoute-Verbindung aufzuheben, weist die Verbindung den unten beschriebenen Zustand auf, wenn der Service Provider den Prozess zur Bereitstellungsaufhebung abgeschlossen hat.


	ServiceProviderProvisioningState : NotProvisioned
	Status                           : Enabled

Sie können sie bei Bedarf erneut aktivieren oder PowerShell-Cmdlets ausführen, um die Verbindung zu löschen. **Wichtig:** Microsoft erhebt weiterhin Gebühren für die Verbindung, bis Sie mithilfe des PowerShell-Cmdlets die Bereitstellung der Verbindung aufheben.


## Konfigurationszustand der Routingsitzung

Der BGP-Bereitstellungszustand gibt an, ob die BGP-Sitzung auf dem Microsoft-Edge aktiviert wurde. Der Zustand muss aktiviert sein, damit Sie das Peering verwenden können.

Insbesondere beim Microsoft-Peering sollte der BGP-Sitzungszustand überprüft werden. Zusätzlich zum BGP-Bereitstellungszustand gibt es einen weiteren Zustand: *advertised public prefixes state*. Der Zustand der angekündigten öffentlichen Präfixe muss *configured* lauten, damit die BGP-Sitzung aktiv ist und das Routing durchgängig funktioniert.

Wenn der Zustand der angekündigten öffentlichen Präfixe *validation needed* lautet, ist die BGP-Sitzung nicht aktiviert, da die angekündigten Präfixe keiner AS-Nummer in einer der Routingregistrierungen entsprachen.

>[AZURE.IMPORTANT]Wenn der Zustand der angekündigten öffentlichen Präfixe *manual validation* lautet, müssen Sie ein Supportticket beim [Microsoft-Support](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) öffnen, nachweisen, dass Sie der Besitzer der angekündigten IP-Adressen sind, und die zugeordnete autonome Systemnummer angeben.


## Nächste Schritte

- Konfigurieren Sie Ihre ExpressRoute-Verbindung.

	- [Erstellen einer ExpressRoute-Verbindung](expressroute-howto-circuit-arm.md)
	- [Konfigurieren des Routings](expressroute-howto-routing-arm.md)
	- [Verknüpfen eines VNet mit einer ExpressRoute-Verbindung](expressroute-howto-linkvnet-arm.md)

<!---HONumber=AcomDC_0121_2016-->