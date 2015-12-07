<properties pageTitle="Konfigurieren der Tunnelerzwingung für VPN-Gateways mit dem Ressourcen-Manager | Microsoft Azure" description="Sie können in einem virtuellen Netzwerk mit einem standortübergreifenden VPN-Gateway die Umleitung des gesamten Internetdatenverkehrs an Ihren lokalen Standort „erzwingen“. Dieser Artikel gilt für das Ressourcen-Manager-Bereitstellungsmodell. " services="vpn-gateway" documentationCenter="na" authors="cherylmc" manager="carolz" editor="" tags="azure-resource-manager"/>
<tags  
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/17/2015"
   ms.author="cherylmc" />

# Konfigurieren der Tunnelerzwingung mit PowerShell und dem Azure-Ressourcen-Manager

Dieser Artikel bezieht sich auf VNets und VPN-Gateways, die mithilfe des Azure-Ressourcen-Manager-Bereitstellungsmodells erstellt wurden. Informationen zum Konfigurieren der Tunnelerzwingung mithilfe des Bereitstellungsmodells der Dienstverwaltung finden Sie unter [Konfigurieren der Tunnelerzwingung](vpn-gateway-about-forced-tunneling.md).

[AZURE.INCLUDE [vpn-gateway-sm-rm](../../includes/vpn-gateway-sm-rm-include.md)]

## Informationen zur Tunnelerzwingung

Über die Tunnelerzwingung können Sie die Umleitung des gesamten Internetdatenverkehrs an Ihren lokalen Standort "erzwingen". Sie verwenden dazu einen Standort-zu-Standort-VPN-Tunnel für die Kontrolle und Überwachung. Dies ist eine wichtige Sicherheitsvoraussetzung der IT-Richtlinien für die meisten Unternehmen. Ohne die Tunnelerzwingung wird der Internetdatenverkehr Ihrer virtuellen Computer in Azure immer direkt von der Azure-Netzwerkinfrastruktur an das Internet geleitet, ohne dass Sie die Möglichkeit haben, diesen zu überprüfen oder zu überwachen. Nicht autorisierter Zugriff auf das Internet kann potenziell zur Offenlegung von Informationen oder anderen Arten von Sicherheitsverletzungen führen.

Das folgende Diagramm veranschaulicht die Funktionsweise der Tunnelerzwingung.

![Tunnelerzwingung](./media/vpn-gateway-forced-tunneling-rm/forced-tunnel.png)

Im obigen Beispiel wird für das Frontend-Subnetz kein Tunneln erzwungen. Die Workloads im Frontend-Subnetz können weiterhin Kundenanfragen direkt aus dem Internet akzeptieren und darauf reagieren. Für die Subnetze "Midtier" und "Backend" wird das Tunneln erzwungen. Bei allen ausgehenden Verbindungen mit dem Internet aus diesen zwei Subnetzen wird eine Umleitung an einen lokalen Standort über einen der S2S-VPN-Tunnel erzwungen. Dadurch können Sie den Internetzugriff aus Virtual Machines und Cloud Services in Azure einschränken und überprüfen, während die erforderliche mehrschichtige Dienstarchitektur weiterhin in Betrieb bleibt. Sie haben auch die Möglichkeit, das erzwungene Tunneln auf alle virtuellen Netzwerke anzuwenden, wenn in diesen keine Workloads mit Internetverbindung erforderlich sind.

## Anforderungen und Überlegungen

Die Tunnelerzwingung in Azure wird über benutzerdefinierte Routen im virtuellen Netzwerk konfiguriert. Das Umleiten von Datenverkehr an einen lokalen Standort wird als eine Standardroute zum Azure-VPN-Gateway umgesetzt. Weitere Informationen zu benutzerdefiniertem Routing und virtuellen Netzwerken finden Sie unter [Benutzerdefinierte Routen und IP-Weiterleitung](../virtual-network/virtual-networks-udr-overview.md).

- Jedes Subnetz des virtuellen Netzwerks verfügt über eine integrierte Systemroutingtabelle. Die Systemroutingtabelle verfügt über die folgenden drei Gruppen von Routen:

	- **Lokale VNET-Routen:** direkt zu den virtuelle Zielcomputern im gleichen virtuellen Netzwerk
	
	- **Lokale Routen:** zum Azure-VPN-Gateway
	
	- **Standardroute:** direkt zum Internet. Beachten Sie, dass Pakete an private IP-Adressen, die nicht durch die vorherigen beiden Routen abgedeckt sind, verworfen werden.

-  In diesem Verfahren werden benutzerdefinierte Routen (User Defined Routes, UDR) verwendet, um eine Routingtabelle zu erstellen, damit eine Standardroute hinzugefügt wird. Anschließend verknüpfen Sie die Routingtabelle mit den VNet-Subnetzen, um die Tunnelerzwingung in diesen Subnetzen zu aktivieren.

- Die Tunnelerzwingung muss einem VNet zugeordnet werden, das über ein routenbasiertes VPN-Gateway verfügt. Sie müssen einen "Standardstandort" unter den standortübergreifenden lokalen Standorten auswählen, der mit dem virtuellen Netzwerk verbunden ist.

- Beachten Sie, dass ExpressRoute-Tunnelerzwingung über diesen Mechanismus nicht konfiguriert werden kann. Sie wird stattdessen durch Anfordern einer Standardroute über die ExpressRoute-BGP-Peeringsitzungen aktiviert. Weitere Informationen finden Sie unter der [ExpressRoute-Dokumentation](https://azure.microsoft.com/documentation/services/expressroute/).

## Konfigurieren der Tunnelerzwingung

### Konfigurationsübersicht

Das folgende Verfahren hilft Ihnen dabei, eine Ressourcengruppe und ein VNet zu erstellen. Sie erstellen dann ein VPN-Gateway und konfigurieren die Tunnelerzwingung.

Im Beispiel verfügt das virtuelle Netzwerk "MultiTier-VNet" über drei Subnetze: *Frontend*, *Midtier* und *Backend*, die vier standortübergreifende Verbindungen aufweisen: *DefaultSiteHQ* und drei *Verzweigungen*. Mit den Verfahrensschritten wird festgelegt, dass *DefaultSiteHQ* die Standard-Standortverbindung für die Tunnelerzwingung ist und dass die Subnetze *Midtier* und *Backend* die Tunnelerzwingung verwenden müssen.

	
### Vorbereitungen

Vergewissern Sie sich vor Beginn der Konfiguration, dass Sie über Folgendes verfügen:

- Ein Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren oder sich für eine [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/) registrieren.

- Die neueste Version der Azure PowerShell-Cmdlets mit dem Webplattform-Installer. Sie können die neueste Version von [Webplattform-Installer](http://aka.ms/webpi-azps/) herunterladen und installieren. Diese Dokumentation wurde für PowerShell 1.0 oder höher geschrieben. Für diese Konfiguration erforderliche Cmdlets sind in früheren Versionen nicht vorhanden. Weitere Informationen zu PowerShell 1.0 finden Sie unter [Azure PowerShell 1.0 – Vorschau](https://azure.microsoft.com/blog/azps-1-0-pre/).

- Wenn Sie mit der Verwendung des Azure-Ressourcen-Managers und mit PowerShell nicht vertraut sind, finden Sie [in diesem Artikel](../articles/powershell-azure-resource-manager.md) weitere Informationen.

### Konfigurationsschritte

1. Melden Sie sich in der PowerShell-Konsole bei Ihrem Azure-Konto an. Sie werden von diesem Cmdlet zur Eingabe der Anmeldeinformationen für Ihr Azure-Konto aufgefordert. Nach dem Anmelden werden Ihre Kontoeinstellungen heruntergeladen, damit sie Azure PowerShell zur Verfügung stehen.

		Login-AzureRmAccount 

2. Rufen Sie eine Liste Ihrer Azure-Abonnements ab.

		Get-AzureRmSubscription

2. Geben Sie den Namen Ihres Azure-Abonnements an.

		Get-AzureRmSubscription -SubscriptionName "YourSubscriptionName" | Select-AzureRmSubscription
		
3. Erstellen Sie eine Ressourcengruppe.

		New-AzureRmResourceGroup -Name "ForcedTunneling" -Location "North Europe"

4. Erstellen Sie ein virtuelles Netzwerks, und geben Sie Subnetze an.

		$s1 = New-AzureRmVirtualNetworkSubnetConfig -Name "Frontend" -AddressPrefix "10.1.0.0/24"
		$s2 = New-AzureRmVirtualNetworkSubnetConfig -Name "Midtier" -AddressPrefix "10.1.1.0/24"
		$s3 = New-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -AddressPrefix "10.1.2.0/24"
		$s4 = New-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -AddressPrefix "10.1.200.0/28"
		$vnet = New-AzureRmVirtualNetwork -Name "MultiTier-VNet" -Location "North Europe" -ResourceGroupName "ForcedTunneling" -AddressPrefix "10.1.0.0/16" -Subnet $s1,$s2,$s3,$s4

5. Erstellen Sie die lokalen Netzwerkgateways.

		$lng1 = New-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.111" -AddressPrefix "192.168.1.0/24"
		$lng2 = New-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.112" -AddressPrefix "192.168.2.0/24"
		$lng3 = New-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.113" -AddressPrefix "192.168.3.0/24"
		$lng4 = New-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -GatewayIpAddress "111.111.111.114" -AddressPrefix "192.168.4.0/24"
		
6. Erstellen Sie die Routingtabelle und die Routingregel.

		New-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" –Location "North Europe"
		$rt = Get-AzureRmRouteTable –Name "MyRouteTable" -ResourceGroupName "ForcedTunneling" 
		Add-AzureRmRouteConfig -Name "DefaultRoute" -AddressPrefix "0.0.0.0/0" -NextHopType VirtualNetworkGateway -RouteTable $rt
		Set-AzureRmRouteTable -RouteTable $rt


6. Ordnen Sie die Routingtabelle den Subnetzen „Midtier“ und „Backend“ zu.

		$vnet = Get-AzureRmVirtualNetwork -Name "MultiTier-Vnet" -ResourceGroupName "ForcedTunneling"
		Set-AzureRmVirtualNetworkSubnetConfig -Name "MidTier" -VirtualNetwork $vnet -AddressPrefix "10.1.1.0/24" -RouteTable $rt
		Set-AzureRmVirtualNetworkSubnetConfig -Name "Backend" -VirtualNetwork $vnet -AddressPrefix "10.1.2.0/24" -RouteTable $rt
		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

9. Erstellen Sie das Gateway mit einem Standardstandort. Dieser Schritt dauert einige Zeit, manchmal 20 Minuten oder länger, weil Sie das Gateway erstellen und konfigurieren. Obwohl nur wenige Cmdlets in der Konsole angezeigt werden, passiert eine Menge im Hintergrund. Beachten Sie, dass „GatewayDefaultSite“ der Cmdlet-Parameter ist, durch den die Konfiguration des erzwungenen Routings funktioniert, Sie sollten ihn also verwenden. Er ist nur in PowerShell 1.0 oder höher verfügbar.

		$pip = New-AzureRmPublicIpAddress -Name "GatewayIP" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -AllocationMethod Dynamic
		$gwsubnet = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet
		$ipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name "gwIpConfig" -SubnetId $gwsubnet.Id -PublicIpAddressId $pip.Id
		New-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -IpConfigurations $ipconfig -GatewayType Vpn -VpnType RouteBased -GatewayDefaultSite $lng1 -EnableBgp $false

10. Richten Sie die Site-to-Site-VPN-Verbindungen ein.

		$gateway = Get-AzureRmVirtualNetworkGateway -Name "Gateway1" -ResourceGroupName "ForcedTunneling"
		$lng1 = Get-AzureRmLocalNetworkGateway -Name "DefaultSiteHQ" -ResourceGroupName "ForcedTunneling" 
		$lng2 = Get-AzureRmLocalNetworkGateway -Name "Branch1" -ResourceGroupName "ForcedTunneling" 
		$lng3 = Get-AzureRmLocalNetworkGateway -Name "Branch2" -ResourceGroupName "ForcedTunneling" 
		$lng4 = Get-AzureRmLocalNetworkGateway -Name "Branch3" -ResourceGroupName "ForcedTunneling" 

		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng1 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection2" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng2 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection3" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng3 -ConnectionType IPsec -SharedKey "preSharedKey"
		New-AzureRmVirtualNetworkGatewayConnection -Name "Connection4" -ResourceGroupName "ForcedTunneling" -Location "North Europe" -VirtualNetworkGateway1 $gateway -LocalNetworkGateway2 $lng4 -ConnectionType IPsec -SharedKey "preSharedKey"

		Get-AzureRmVirtualNetworkGatewayConnection -Name "Connection1" -ResourceGroupName "ForcedTunneling"
		

<!---HONumber=AcomDC_1125_2015-->