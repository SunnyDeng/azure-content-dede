<properties
   pageTitle="Hinzufügen eines VPN-Gateways eines virtuellen Netzwerks für ExpressRoute mit dem Ressourcen-Manager und PowerShell | Microsoft Azure"
   description="In diesem Artikel wird beschrieben, wie Sie einem bereits erstellten Ressourcen-Manager-VNet für ExpressRoute ein VPN-Gateway hinzufügen."
   documentationCenter="na"
   services="expressroute"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags 
   ms.service="expressroute"
   ms.devlang="na"
   ms.topic="article" 
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services" 
   ms.date="02/26/2016"
   ms.author="cherylmc"/>

# Fügen Sie einem Ressourcen-Manager-VNet ein VPN-Gateway für eine ExpressRoute-Konfiguration hinzu. 

In diesem Artikel werden die Schritte für das Hinzufügen eines Gatewaysubnetzes, und das Erstellen eines VPN-Gateways für ein bereits bestehendes VNet beschrieben. Die Schritte für diese Konfiguration gelten speziell für VNets, die unter Verwendung des **Ressourcen-Manager-Bereitstellungsmodells** erstellt wurden, und die in einer ExpressRoute-Konfiguration verwendet werden. Weitere Informationen über die Erstellung von Gateways für VNets unter Verwendung des klassischen Bereitstellungsmodells erhalten Sie unter [Configure a virtual network for ExpressRoute using the classic portal](expressroute-howto-vnet-portal-classic.md) (Konfigurieren eines virtuellen Netzwerks für ExpressRoute unter Verwendung des klassischen Portals).

## Vorbereitungen

Stellen Sie sicher, dass Sie die für diese Konfiguration erforderlichen Azure PowerShell-Cmdlets installiert haben (1.0.2 oder höher). Wenn Sie die Cmdlets noch nicht installiert haben, müssen Sie dies tun, bevor Sie mit der Konfiguration beginnen. Weitere Informationen zum Installieren von Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).


[AZURE.INCLUDE [expressroute-gateway-rm-ps](../../includes/expressroute-gateway-rm-ps-include.md)]

## Erstellung des Gateways überprüfen

Verwenden Sie den folgenden Befehl, um zu überprüfen, ob das Gateway erstellt wurde.

	Get-AzureRmVirtualNetworkGateway -ResourceGroupName $RG
	
## Nächste Schritte

Nachdem Sie das VPN-Gateway erstellt haben, können Sie Ihr VNet mit einer ExpressRoute-Verbindung verknüpfen. Weitere Informationen finden Sie unter [Link a Virtual Network to an ExpressRoute circuit](expressroute-howto-linkvnet-arm.md) (Verknüpfen eines virtuellen Netzwerks mit einer ExpressRoute-Verbindung).

<!---HONumber=AcomDC_0309_2016-->