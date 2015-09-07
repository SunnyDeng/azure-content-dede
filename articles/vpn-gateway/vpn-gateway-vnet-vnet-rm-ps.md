<properties
   pageTitle="Erstellen einer VNet-zu-VNet-Verbindung mit dem Azure-Ressourcen-Manager und PowerShell | Microsoft Azure"
	description="Dieser Artikel führt Sie durch das Verbinden virtueller Netzwerke mithilfe von Azure-Ressourcen-Manager und PowerShell."
	services="vpn-gateway"
	documentationCenter="na"
	authors="cherylmc"
	manager="carolz"
	editor=""/>

<tags
   ms.service="vpn-gateway"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="08/20/2015"
	ms.author="cherylmc"/>

# Konfigurieren einer VNet-zu-VNet-Verbindung mit dem Azure-Ressourcen-Manager und PowerShell

> [AZURE.SELECTOR]
- [Azure Portal](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)


Das Verbinden eines virtuellen Netzwerks mit einem anderen virtuellen Netzwerk (VNet-zu-VNet) ähnelt dem Verbinden eines VNet mit einem lokalen Standort. Beide Verbindungstypen verwenden ein VPN-Gateway, um einen sicheren Tunnel mit IPSec/IKE bereitzustellen. Die VNets, die Sie verbinden, können sich in verschiedenen Regionen befinden. Sie können sogar VNet-zu-VNet-Kommunikation mit Konfigurationen für mehrere Standorte kombinieren. Auf diese Weise können Sie Netzwerktopologien einrichten, die standortübergreifende Konnektivität mit Konnektivität zwischen virtuellen Netzwerken kombinieren, wie in der folgenden Abbildung dargestellt:


![VNet-zu-VNet-Konnektivitätsdiagramm](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727360.png)

 

>[AZURE.NOTE]Azure verfügt derzeit über zwei Bereitstellungsmodelle: das klassische Bereitstellungsmodell und das Azure-Ressourcen-Manager-Bereitstellungsmodell. Die Konfigurations-Cmdlets und die Schritte der beiden Bereitstellungsmethoden unterscheiden sich. Dieses Thema führt Sie durch die Erstellung der virtuellen Netzwerke mit dem Azure-Ressourcen-Manager-Modus. Wenn Sie eine VNet-zu-VNet-Verbindung mit dem klassischen Bereitstellungsmodus erstellen möchten, lesen Sie [Konfigurieren einer VNet-zu-VNet-Verbindung mit dem Azure-Portal](virtual-networks-configure-vnet-to-vnet-connection.md). Wenn Sie ein im klassischen Bereitstellungsmodus erstelltes virtuelles Netzwerk mit einem virtuellen Netzwerk verbinden möchten, das im Azure-Ressourcen-Manager erstellt wurde, finden Sie weitere Informationen unter [Herstellen einer Verbindung zwischen klassischen VNets und neuen VNets](../virtual-network/virtual-networks-arm-asm-s2s.md).

## Gründe für Verbindungen zwischen virtuellen Netzwerken

Aus den folgenden Gründen empfiehlt sich das Herstellen von Verbindungen zwischen virtuellen Netzwerken:

- **Regionsübergreifende Georedundanz und Geopräsenz**
	- Sie können Ihre eigene Georeplikation oder -synchronisierung mit sicheren Verbindungen einrichten, ohne Endpunkte mit Internetzugriff verwenden zu müssen.
	- Mit dem Azure-Load Balancer und Clustertechnologie von Microsoft oder Drittanbietern können Sie Workloads mit hoher Verfügbarkeit mit Georedundanz über mehrere Azure-Regionen hinweg einrichten. Ein wichtiges Beispiel ist die Einrichtung von SQL Always On mit Verfügbarkeitsgruppen, die sich über mehrere Azure-Regionen erstrecken.

- **Regionale Anwendungen mit mehreren Ebenen mit starker Isolierungsgrenze**
	- Innerhalb der gleichen Region können Sie Anwendungen mit mehreren Ebenen mit mehreren verbundenen virtuellen Netzwerken einrichten, die starke Isolierung und sichere Kommunikation zwischen den Ebenen aufweisen.



### Was Sie beachten sollten

In diesem Artikel erstellen Sie schrittweise eine Verbindung zwischen zwei virtuellen Netzwerken: VNet1 und VNet2. Sie müssen mit Netzwerken vertraut sein, um die IP-Adressbereiche zu ersetzen, die mit Ihren Anforderungen an den Netzwerkentwurf kompatibel sind.


![Verbinden von VNet zu VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727361.png)



- Die virtuellen Netzwerke können sich in der gleichen Azure-Region oder in verschiedenen Azure-Regionen (Standorte) befinden.

- Ein Clouddienst oder Endpunkt mit Lastenausgleich darf auch dann NICHT mehrere virtuelle Netzwerke umfassen, wenn diese verbunden sind.

- Für das Verbinden virtueller Azure-Netzwerke sind keine lokalen VPN-Gateways erforderlich. Diese werden nur benötigt, wenn standortübergreifende Konnektivität erforderlich ist.

- VNet-zu-VNet unterstützt das Verbinden virtueller Netzwerke. Nicht unterstützt hingegen werden Verbindungen virtueller Computer oder Clouddienste, die sich NICHT in einem virtuellen Netzwerk befinden.

- VNet-zu-VNet erfordert Azure-VPN-Gateways mit routenbasierten VPN-Typen (früher als "VPNs mit dynamischem Routing" bezeichnet).

- Virtuelle Netzwerkverbindungen können gleichzeitig mit VPNs mit mehreren Standorten und maximal 10 VPN-Tunneln für ein VPN-Gateway des virtuellen Netzwerks verwendet werden, das Verbindungen mit anderen virtuellen Netzwerken oder lokalen Standorten herstellt.

- Die Adressräume der virtuellen Netzwerke und der lokalen Netzwerkstandorte dürfen sich nicht überschneiden. Wenn sich Adressbereiche überschneiden, tritt beim Erstellen eines virtuellen Netzwerks ein Fehler auf.

- Redundante Tunnel zwischen einem Paar virtueller Netzwerke werden nicht unterstützt.

- Alle VPN-Tunnel des virtuellen Netzwerks verwenden die verfügbare Bandbreite auf dem Azure-VPN-Gateway und die gleiche SLA für die Verfügbarkeit des VPN-Gateways in Azure gemeinsam.

- VNet-zu-VNet-Datenverkehr wird über den Azure-Backbone übertragen.


## Vorbereitungen


Bevor Sie beginnen, stellen Sie sicher, dass Sie über Folgendes verfügen:


- Die neueste Version von Azure PowerShell-Cmdlets. Sie können die neueste Version aus dem Abschnitt "Windows PowerShell" der [Downloadseite](http://azure.microsoft.com/downloads/) herunterladen und installieren. 
- Ein Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren oder sich für eine [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/) registrieren.
- Wenn Sie Ihre virtuellen Netzwerke bereits erstellt haben, lesen Sie [Herstellen der Verbindung mit vorhandenen VNets](#connecting-existing-vnets).
	


Das Erstellen und Konfigurieren einer VNet-zu-VNet-Verbindung umfasst mehrere Schritte. Konfigurieren Sie jeden Abschnitt in der unten aufgeführten Reihenfolge:


1. [Planen der IP-Adressbereiche](#plan-your-ip-address-ranges)
2. [Verbinden mit Ihrem Abonnement](#connect-to-your-subscription)
3. [Erstellen eines virtuellen Netzwerks](#create-a-virtual-network)
4. [Anfordern einer öffentlichen IP-Adresse für das Gateway](#request-a-public-ip-address)
5. [Erstellen der Gatewaykonfiguration](#create-the-gateway-configuration)
6. [Erstellen des Gateways](#create-the-gateway)
7. [Wiederholen Sie die Schritte, um VNet2 zu konfigurieren](#create-vnet2)
8. [Verbinden der VPN-Gateways](#connect-the-gateways)


## Planen der IP-Adressbereiche

### Schritt 1

Eine wichtige Entscheidung betrifft die Bereiche, die Sie zum Konfigurieren Ihrer Netzwerkkonfiguration verwenden. Aus der Perspektive von VNet1 ist VNet2 nur eine weitere VPN-Verbindung, die in der Azure-Plattform definiert ist. Aus der Sicht von VNet2 ist VNet1 nur eine weitere VPN-Verbindung. Denken Sie daran, dass Sie sicherstellen müssen, dass keiner der VNet-Bereiche oder der Bereiche des lokalen Netzwerks Überschneidungen aufweist.


Mit den folgenden Schritten werden zwei virtuelle Netzwerke zusammen mit ihren jeweiligen Gatewaysubnetzen und Konfigurationen erstellt. Anschließend wird eine VPN-Gatewayverbindung zwischen den zwei VNets erstellt.

Verwenden Sie für diese Übung die folgenden Werte für die VNets:

Werte für VNet1:

- Name des virtuellen Netzwerks = VNet1
- Ressourcengruppe = testrg1
- Adressraum = 10.1.0.0/16 
- Region = US West
- GatewaySubnet = 10.1.0.0/28
- Subnet1 = 10.1.1.0/28

Werte für VNet2:

- Name des virtuellen Netzwerks = VNet2
- Ressourcengruppe = testrg2
- Adressraum = 10.2.0.0/16
- Region = Japan East
- GatewaySubnet = 10.2.0.0/28
- Subnet1 = 10.2.1.0/28

## Verbinden mit Ihrem Abonnement 

### Schritt 2


Öffnen Sie Ihre PowerShell-Konsole und wechseln Sie in den Azure-Ressourcen-Manager-Modus. Verwenden Sie das folgende Beispiel, um eine Verbindung herzustellen:

		Add-AzureAccount

Führen Sie „Select-AzureSubscription“ aus, um mit dem Abonnement eine Verbindung herzustellen, mit dem Sie arbeiten möchten.

		Select-AzureSubscription "yoursubscription"

Als Nächstes wechseln Sie in den ARM-Modus. Dadurch wechseln Sie den Modus, mit dem Sie die ARM-Cmdlets verwenden können.

		Switch-AzureMode -Name AzureResourceManager

## Erstellen eines virtuellen Netzwerks

### Schritt 3


Nutzen Sie das Beispiel unten, um ein virtuelles Netzwerk und ein Gatewaysubnetz zu erstellen. Ersetzen Sie die Werte durch Ihre eigenen. In diesem Beispiel wird VNet1 erstellt. Später wiederholen Sie die Schritte zum Erstellen von VNet2.

Erstellen Sie zunächst eine Ressourcengruppe.

			New-AzureResourceGroup -Name testrg1 -Location 'West US'

Erstellen Sie als Nächstes das virtuelle Netzwerk. Das folgende Beispiel erstellt ein virtuelles Netzwerk namens *VNet1* und zwei Subnetze, eines mit dem Namen *GatewaySubnet* und das andere mit dem Namen *Subnet1*. Es ist wichtig, ein Subnetz speziell mit dem Namen *GatewaySubnet* zu erstellen. Wenn Sie einen anderen Namen wählen, schlägt die Verbindungskonfiguration fehl. Im folgenden Beispiel wird vom Gatewaysubnetz ein /28 verwendet. Sie können auch ein Gatewaysubnetz verwenden, das so klein wie /29 ist. Beachten Sie, dass einige Features (z. B. eine gleichzeitige ExpressRoute/Standort-zu-Standort-Verbindung) ein größeres Gatewaysubnetz von /27 erfordern. Dimensionieren Sie Ihr Gatewaysubnetz also so, dass zusätzliche Features unterstützt werden, die Sie möglicherweise in Zukunft verwenden möchten.

 		$subnet = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.0.0/28
		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.1.1.0/28'
		New-AzurevirtualNetwork -Name VNet1 -ResourceGroupName testrg1 -Location 'West US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet, $subnet1

## Anfordern einer öffentlichen IP-Adresse

### Schritt 4

Als Nächstes müssen Sie eine öffentliche IP-Adresse anfordern, die dem Gateway zugewiesen wird, das Sie für Ihr VNet erstellen. Sie können keine IP-Adresse angeben, die Sie verwenden möchten, sondern die IP-Adresse wird Ihrem Gateway dynamisch zugewiesen. Diese IP-Adresse wird im nächsten Konfigurationsabschnitt verwendet.

Verwenden Sie das folgenden Beispiel. Die Zuordnungsmethode für diese Adresse muss dynamisch sein.


		$gwpip= New-AzurePublicIpAddress -Name gwpip1 -ResourceGroupName testrg1 -Location 'West US' -AllocationMethod Dynamic


## Erstellen der Gatewaykonfiguration

### Schritt 5

Die Gatewaykonfiguration definiert das zu verwendende Subnetz und die zu verwendende öffentliche IP-Adresse. Verwenden Sie das folgende Beispiel, um Ihre Gatewaykonfiguration zu erstellen.


		$vnet = Get-AzureVirtualNetwork -Name VNet1 -ResourceGroupName testrg1
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 


## Erstellen des Gateways

### Schritt 6

In diesem Schritt erstellen Sie das virtuelle Netzwerkgateway für Ihr VNet. VNet-zu-VNet-Konfigurationen erfordern einen routenbasierten VPN-Typ. Das Erstellen eines Gateways kann eine Weile dauern, bitte haben Sie also etwas Geduld.

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1 -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## Erstellen von VNet2

### Schritt 7

Nachdem Sie VNet1 konfiguriert haben, wiederholen Sie die vorherigen Schritte, um VNet2 und das Gateway zu konfigurieren. Nachdem Sie die Konfiguration der beiden VNets und ihrer jeweiligen Gateways abgeschlossen haben, fahren Sie mit dem Abschnitt [Verbinden der Gateways](#connect-the-gateways) fort.


## Verbinden der Gateways

### Schritt 8

In diesem Schritt erstellen Sie die VPN-Gatewayverbindungen zwischen den beiden virtuellen Netzwerkgateways. In den Beispielen wird auf einen gemeinsam verwendeten Schlüssel verwiesen. Sie können eigene Werte für den gemeinsam verwendeten Schlüssel verwenden. Wichtig ist dabei, dass der gemeinsam verwendete Schlüssel für beide Konfigurationen übereinstimmen muss.

Beachten Sie, dass das Erstellen von Verbindungen einige Zeit in Anspruch nehmen kann.

**Für VNet1-zu-VNet2**
    
    $vnetgw1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1
    $vnetgw2 = Get-AzureVirtualNetworkGateway -Name vnetgw2 -ResourceGroupName testrg2
    
    New-AzureVirtualNetworkGatewayConnection -Name conn1 -ResourceGroupName testrg1 -VirtualNetworkGateway1 $vnetgw1 -VirtualNetworkGateway2 $vnetgw2 -Location 'West US' -ConnectionType Vnet2Vnet -SharedKey 'abc123'


**Für VNet2-zu-VNet1**
    
    $vnetgw1 = Get-AzureVirtualNetworkGateway -Name vnetgw2 -ResourceGroupName testrg2
    $vnetgw2 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1
    
    New-AzureVirtualNetworkGatewayConnection -Name conn2 -ResourceGroupName testrg2 -VirtualNetworkGateway1 $vnetgw1 -VirtualNetworkGateway2 $vnetgw2 -Location 'Japan East' -ConnectionType Vnet2Vnet -SharedKey 'abc123'
    


Nach einigen Minuten sollte die Verbindung hergestellt sein. Beachten Sie, dass Gateways und Verbindungen, die mit dem Azure-Ressourcen-Manager erstellt wurden, im Vorschauportal derzeit nicht sichtbar sind.

## Herstellen der Verbindung mit vorhandenen VNets

Wenn Sie bereits virtuelle Netzwerke im Azure-Ressourcen-Manager-Modus erstellt haben und eine Verbindung mit diesen herstellen möchten, überprüfen Sie Folgendes:

- Sie verfügen über ein Gatewaysubnetz von mindestens /29 oder größer für jedes VNet.
- Die Adressbereiche für Ihre virtuellen Netzwerke überschneiden sich nicht.

Wenn Sie Ihren VNets Gatewaysubnetze hinzugefügt müssen, verwenden Sie das Beispiel unten, und ersetzen die Werte durch eigene. Achten Sie darauf, dass Sie das Gatewaysubnetz "GatewaySubnet" nennen. Wenn Sie einen anderen Namen wählen, wird die VPN-Konfiguration nicht ordnungsgemäß funktionieren.

	
		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
		Set-AzureVirtualNetwork -VirtualNetwork $vnet

Nachdem Sie sich vergewissert haben, dass Ihre Gatewaysubnetze richtig konfiguriert sind, fahren Sie mit den Schritten unter [Anfordern einer öffentlichen IP-Adresse](#request-a-public-ip-address) fort.

## Nächste Schritte

Sie können Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. [Erstellen eines virtuellen Computers](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=August15_HO9-->