<properties
   pageTitle="Erstellen eines virtuellen Netzwerks mit einer Site-to-Site-VPN-Verbindung mit dem Azure-Ressourcen-Manager und PowerShell | Microsoft Azure"
   description="Erstellen einer Site-to-Site-VPN-Verbindung von Ihrem virtuellen Netzwerk auf Ihren lokalen Standort mit dem Azure-Ressourcen-Manager und PowerShell"
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/28/2015"
   ms.author="cherylmc"/>

# Erstellen eines virtuellen Netzwerks mit einer Site-to-Site-VPN-Verbindung mit dem Azure-Ressourcen-Manager und PowerShell

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)


Dieses Thema führt Sie durch das Erstellen eines virtuellen Netzwerks mit dem Azure-Ressourcen-Manager und einer Site-to-Site-VPN-Verbindung mit Ihrem lokalen Netzwerk.

Azure verfügt derzeit über zwei Bereitstellungsmodelle: das klassische Bereitstellungsmodell und das Azure-Ressourcen-Manager-Bereitstellungsmodell. Die Site-to-Site-Einrichtung unterscheidet sich je nach Modell, das für die Bereitstellung Ihres virtuellen Netzwerks verwendet wurde. Diese Anweisungen gelten für den Ressourcen-Manager. Wenn Sie eine Site-to-Site-VPN-Gatewayverbindung mit dem klassischen Bereitstellungsmodell erstellen möchten, finden Sie Informationen dazu unter [Erstellen einer Site-to-Site-VPN-Verbindung im Verwaltungsportal](vpn-gateway-site-to-site-create.md).


## Voraussetzungen

Bevor Sie beginnen, stellen Sie sicher, dass Sie über Folgendes verfügen:

- Ein kompatibles VPN-Gerät \(und eine Person, die es konfigurieren kann\). Weitere Informationen finden Sie unter [Informationen zu VPN-Geräten](vpn-gateway-vpn-devices.md).
- Eine externe öffentliche IP-Adresse für Ihr VPN-Gerät. Diese IP-Adresse darf sich nicht hinter einer NAT befinden.
- Die neueste Version von Azure PowerShell-Cmdlets. Sie können die neueste Version aus dem Abschnitt „Windows PowerShell“ der [Downloadseite](http://azure.microsoft.com/downloads/) herunterladen und installieren. 
- Ein Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren oder sich für eine [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/) registrieren.
	

## Verbinden mit Ihrem Abonnement 


Öffnen Sie Ihre PowerShell-Konsole und wechseln Sie in den Azure-Ressourcen-Manager-Modus. Verwenden Sie das folgende Beispiel, um eine Verbindung herzustellen:

		Add-AzureAccount

Führen Sie „Select-AzureSubscription“ aus, um mit dem Abonnement eine Verbindung herzustellen, mit dem Sie arbeiten möchten.

		Select-AzureSubscription "yoursubscription"

Als Nächstes wechseln Sie in den ARM-Modus. Dadurch wechseln Sie den Modus, mit dem Sie die ARM-Cmdlets verwenden können.

		Switch-AzureMode -Name AzureResourceManager


## Erstellen eines virtuelles Netzwerks und eines Gatewaysubnetzes

- Wenn Sie bereits über ein virtuelles Netzwerk mit einem Gatewaysubnetz verfügen, springen Sie direkt weiter zu [Hinzufügen Ihrer lokalen Website](#add-your-local-site). 
- Wenn Sie über ein virtuelles Netzwerk verfügen und Sie dem VNet ein Gatewaysubnetz hinzufügen möchten, finden Sie Informationen dazu unter [Hinzufügen eines Gatewaysubnetzes zu einem VNet](#gatewaysubnet).

### So erstellen Sie ein virtuelles Netzwerk und ein Gatewaysubnetz

Nutzen Sie das Beispiel unten, um ein virtuelles Netzwerk und ein Gatewaysubnetz zu erstellen. Ersetzen Sie die Werte durch Ihre eigenen.

Erstellen Sie zunächst eine Ressourcengruppe:

	
		New-AzureResourceGroup -Name testrg -Location 'West US'

Erstellen Sie als Nächstes das virtuelle Netzwerk. Das folgende Beispiel erstellt ein virtuelles Netzwerk mit dem Namen *Testvnet* und zwei Subnetze, eines mit der Bezeichnung *GatewaySubnet* und das andere mit der Bezeichnung *Subnet1*. Es ist wichtig, ein Subnetz speziell mit dem Namen *GatewaySubnet* zu erstellen. Wenn Sie einen anderen Namen wählen, schlägt die Verbindungskonfiguration fehl.

		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzurevirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2


### <a name="gatewaysubnet"></a>Hinzufügen eines Gatewaysubnetzes zu einem VNet

Wenn Sie bereits über ein vorhandenes virtuelles Netzwerk verfügen und ein Gatewaysubnetz hinzufügen möchten, können Sie Ihr Gatewaysubnetz anhand des folgenden Beispiels erstellen. Achten Sie darauf, dass Sie das Gatewaysubnetz "GatewaySubnet" nennen. Wenn Sie einen anderen Namen wählen, wird die VPN-Konfiguration nicht ordnungsgemäß funktionieren.


	
		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
		Set-AzureVirtualNetwork -VirtualNetwork $vnet

## Hinzufügen Ihres lokalen Standorts

In einem virtuellen Netzwerk verweist der *lokale Standort* in der Regel auf Ihren lokalen Standort. Sie müssen diesen Standort benennen, damit Azure darauf verweisen kann.

Sie geben auch das Adressraumpräfix für den lokalen Standort an. Azure verwendet das angegebene Präfix der IP-Adresse, um zu erkennen, welcher Datenverkehr an den lokalen Standort gesendet wird. Dies bedeutet, dass Sie jedes Adresspräfix angeben müssen, das dem lokalen Standort zugeordnet werden soll. Sie können diese Präfixe einfach aktualisieren, wenn sich Ihr lokales Netzwerk ändert. Verwenden Sie die PowerShell-Beispiele unten, um einen lokalen Standort anzugeben.

	
- *GatewayIPAddress* ist die IP-Adresse des lokalen VPN-Geräts. Das VPN-Gerät darf sich nicht hinter einer NAT befinden. 
- *AddressPrefix* ist Ihr lokaler Adressraum.

Verwenden Sie folgendes Beispiel, um einen lokalen Standort hinzuzufügen:

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

## Anfordern einer öffentlichen IP-Adresse für das VNet-Gateway

Als Nächstes müssen Sie eine öffentliche IP-Adresse anfordern, die Ihrem Azure-VNet-VPN-Gateway zugewiesen wird. Dies ist nicht die gleiche IP-Adresse, die dem VPN-Gerät zugewiesen wird. Sie wird stattdessen dem Azure-VPN-Gateway selbst zugewiesen. Sie können nicht die IP-Adresse angeben, die Sie verwenden möchten. Diese wird Ihrem Gateway dynamisch zugewiesen. Sie verwenden diese IP-Adresse bei der Konfiguration des lokalen VPN-Geräts für die Verbindung zum Gateway.

Verwenden Sie das folgende PowerShell-Beispiel. Die Zuordnungsmethode für diese Adresse muss dynamisch sein.

		$gwpip= New-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## Erstellen der Gateway-IP-Adressierung

Die Gatewaykonfiguration definiert das zu verwendende Subnetz und die zu verwendende öffentliche IP-Adresse. Verwenden Sie das folgende Beispiel, um Ihre Gatewaykonfiguration zu erstellen.


		$vnet = Get-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 


## Erstellen des Gateways

In diesem Schritt erstellen Sie das Gateway des virtuellen Netzwerks. Verwenden Sie die folgenden Werte:

- Der Gatewaytyp ist *Vpn*.
- Der „VpnType“ kann routenbasiert\* \(in einigen Dokumentationen als ein dynamisches Gateway bezeichnet\) oder *richtlinienbasiert* sein \(in einigen Dokumentationen als statisches Gateway bezeichnet\). Weitere Informationen zu VPN-Gatewaytypen finden Sie unter [Informationen zu VPN-Gateways](vpn-gateway-about-vpngateways.md). 	

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased


## Konfigurieren des VPN-Geräts

An diesem Punkt benötigen Sie die öffentliche IP-Adresse des Gateways des virtuellen Netzwerks für die Konfiguration Ihres lokalen VPN-Geräts. Halten Sie mit Ihrem Gerätehersteller für spezifische Informationen zur Konfiguration Rücksprache. Darüber hinaus finden Sie weitere Informationen unter [VPN-Geräte](http://go.microsoft.com/fwlink/p/?linkid=615099).

Um die öffentliche IP-Adresse des Gateways des virtuellen Netzwerks zu ermitteln, verwenden Sie das folgende Beispiel:

	Get-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg

## Erstellen der VPN-Verbindung

Erstellen Sie als Nächstes die Site-to-Site-VPN-Verbindung zwischen dem Gateway Ihres virtuellen Netzwerks und Ihrem VPN-Gerät. Achten Sie darauf, dass Sie die Werte durch Ihre eigenen ersetzen. Der gemeinsame Schlüssel muss dem Wert entsprechen, den Sie für Ihre VPN-Gerätekonfiguration verwendet haben.

		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Nach einigen Minuten sollte die Verbindung hergestellt sein. Zu diesem Zeitpunkt sind die Site-to-Site-VPN-Verbindungen, die mit dem Ressourcen-Manager erstellt wurden, nicht im Portal sichtbar.


## Nächste Schritte

Fügen Sie einen virtuellen Computer zu Ihrem virtuellen Netzwerk hinzu. [Erstellen eines virtuellen Computers](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=July15_HO5-->