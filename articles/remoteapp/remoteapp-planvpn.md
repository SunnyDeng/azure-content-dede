<properties
    pageTitle="Planen des virtuellen Netzwerks für eine Azure RemoteApp-Sammlung | Microsoft Azure"
    description="Erfahren Sie, wie Sie ein virtuelles Netzwerk für eine Azure RemoteApp-Sammlung planen."
    services="remoteapp"
    documentationCenter="" 
    authors="mghosh1616"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="compute"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/11/2015"
    ms.author="elizapo" />

# Planen des virtuellen Netzwerks für Azure RemoteApp

Dieses Dokument beschreibt, wie Sie Ihr virtuelles Azure-Netzwerk (VNET) und das Subnetz für Azure RemoteApp einrichten. Falls Sie noch nicht mit virtuellen Azure-Netzwerken vertraut sind: Dies ist eine Funktion, mit der Sie Ihre Netzwerkinfrastruktur für die Cloud virtualisieren und Hybridlösungen mit Azure und Ihren lokalen Ressourcen erstellen können. Weitere Informationen dazu finden Sie [hier](virtual-networks-overview.md).

Wenn Sie Sicherheitsrichtlinien für eingehenden und ausgehenden Datenverkehr in dem virtuellen Netzwerk definieren möchten, in dem Sie Azure RemoteApp bereitstellen, empfiehlt es sich dringend, ein Subnetz für Azure RemoteApp zu erstellen, das von Ihren anderen Bereitstellungen im virtuellen Azure-Netzwerk getrennt ist. Weitere Informationen zum Definieren von Sicherheitsrichtlinien in einem Subnetz Ihres virtuellen Azure-Netzwerks erhalten Sie unter [Was ist eine Netzwerksicherheitsgruppe (NSG)?](virtual-networks-nsg.md).

## Arten von Azure RemoteApp-Sammlungen in virtuellen Azure-Netzwerken

Die folgende Abbildung zeigt die beiden verschiedenen Optionen für Sammlungen, die Sie für ein virtuelles Netzwerk einsetzen können.

### Azure RemoteApp-Cloudsammlung mit VNET

 ![Azure RemoteApp – Cloudsammlung mit einem VNET](./media/remoteapp-planvpn/ra-cloudvpn.png)

Diese Abbildung zeigt eine Azure RemoteApp-Sammlung, bei der alle Ressourcen, auf die die RemoteApp-Sitzungshosts zugreifen müssen, in Azure bereitgestellt werden. Sie können sich im gleichen VNET wie das RemoteApp-VNET oder in einem anderen VNET in Azure befinden.

### Azure RemoteApp-Hybridsammlung mit VNET

![Azure RemoteApp – Hybridsammlung mit einem VNET](./media/remoteapp-planvpn/ra-hybridvpn.png)

Diese Abbildung zeigt eine Azure RemoteApp-Sammlung, bei der einige der Ressourcen, auf die die RemoteApp-Sitzungshosts zugreifen müssen, lokal bereitgestellt werden. Das RemoteApp-VNET ist über Azure-Hybridtechnologien wie Site-to-Site-VPN oder ExpressRoute mit dem lokalen Netzwerk verknüpft.


## Wie das System funktioniert

Azure RemoteApp stellt virtuelle Azure-Computer (mit Ihrem hochgeladenen Image) im Hintergrund in dem Subnetz des virtuellen Netzwerks bereit, das Sie während der Bereitstellung ausgewählt haben. Wenn Sie sich für eine Hybridsammlung entschieden haben, wird versucht, den FQDN des Domänencontrollers, den Sie im Bereitstellungsprozess eingegeben haben, mit dem DNS-Server aufzulösen, der im virtuellen Netzwerk angegeben ist. Wenn Sie eine Verbindung mit einem vorhandenen virtuellen Netzwerk herstellen, stellen Sie sicher, dass Sie die erforderlichen Ports in den Netzwerksicherheitsgruppen in Ihrem Azure RemoteApp-Subnetz verfügbar machen.

Es empfiehlt sich, ein [Subnetz in ausreichender Größe für Azure RemoteApp](remoteapp-vnetsizing.md) zu verwenden. Das größte von Azure Virtual Network unterstützt Subnetz ist /8 (gemäß CIDR-Subnetzdefinitionen). Ihr Subnetz sollte groß genug sein, um allen virtuellen Azure RemoteApp-Computern Platz zu bieten, wenn Sie aufgrund einer höheren Anzahl von Benutzern, die auf die Apps zugreifen, Ihre Lösung zentral hochskalieren müssen.

Folgende Aspekte müssen Sie im Subnetz Ihres virtuellen Netzwerks aktivieren:

2.	Ausgehender Datenverkehr aus dem Subnetz sollte im Portbereich 10101-10175 zur Kommunikation mit einem der internen Azure RemoteApp-Dienste zugelassen werden.
3.	Ausgehender Datenverkehr sollte über Port 443 eine Verbindung aus dem Subnetz mit Azure Storage herstellen können.
4.	Wenn Sie Active Directory in Azure hosten, stellen Sie sicher, dass jeder virtuelle Computer innerhalb des Subnetzes, das in Ihrem virtuellen Netzwerk Azure RemoteApp vorbehalten ist, eine Verbindung mit diesem Domänencontroller herstellen kann. Der DNS-Server im virtuellen Netzwerk sollte in der Lage sein, den FQDN dieses Domänencontrollers aufzulösen.


## Virtuelles Netzwerk mit Tunnelerzwingung

[Tunnelerzwingung](vpn-gateway-about-forced-tunneling.md) wird jetzt für alle neuen Azure RemoteApp-Sammlungen unterstützt. Die Migration einer vorhandenen Sammlung zur Unterstützung der Tunnelerzwingung wird zurzeit nicht unterstützt. Sie müssen alle vorhandenen Sammlungen löschen, die das mit Azure RemoteApp verknüpfte VNET nutzen, und ein neues virtuelles Netzwerk erstellen, um die Tunnelerzwingung für Ihre Sammlungen zu aktivieren.

<!---HONumber=Oct15_HO3-->