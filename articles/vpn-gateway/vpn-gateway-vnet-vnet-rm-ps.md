<properties
   pageTitle="Erstellen einer VNet-zu-VNet-Verbindung mit dem Azure-Ressourcen-Manager und PowerShell | Microsoft Azure"
   description="Dieser Artikel führt Sie durch das Verbinden virtueller Netzwerke mithilfe von Azure-Ressourcen-Manager und PowerShell."
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
   ms.date="10/20/2015"
   ms.author="cherylmc"/>

# Konfigurieren einer VNet-zu-VNet-Verbindung mit dem Azure-Ressourcen-Manager und PowerShell

> [AZURE.SELECTOR]
- [Azure Portal](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell - Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

In diesem Artikel werden Sie unter Verwendung des Ressourcen-Manager-Bereitstellungsmodells durch die Schritte begleitet. Sie können den Artikel für das entsprechende Bereitstellungsmodell und Bereitstellungstool mithilfe der obigen Registerkarten auswählen.

>[AZURE.NOTE]Sie sollten wissen, dass Azure derzeit mit zwei Bereitstellungsmodellen arbeitet: der Bereitstellung mit dem Ressourcen-Manager und der klassischen Bereitstellung. Bevor Sie Ihre Konfiguration beginnen, sollten Sie sicherstellen, dass Sie die Bereitstellungsmodelle und -tools verstehen. Informationen zu den Bereitstellungsmodellen finden Sie unter [Azure-Bereitstellungsmodelle](../azure-classic-rm.md).

Das Verbinden eines virtuellen Netzwerks mit einem anderen virtuellen Netzwerk (VNet-zu-VNet) ähnelt dem Verbinden eines VNet mit einem lokalen Standort. Beide Verbindungstypen verwenden ein VPN-Gateway, um einen sicheren Tunnel mit IPSec/IKE bereitzustellen. Die VNets, die Sie verbinden, können sich in verschiedenen Regionen befinden. Sie können sogar VNet-zu-VNet-Kommunikation mit Konfigurationen für mehrere Standorte kombinieren. Auf diese Weise können Sie Netzwerktopologien einrichten, die standortübergreifende Konnektivität mit Konnektivität zwischen virtuellen Netzwerken kombinieren, wie in der folgenden Abbildung dargestellt:


![VNet-zu-VNet-Konnektivitätsdiagramm](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727360.png)

 
## Gründe für Verbindungen zwischen virtuellen Netzwerken

Aus den folgenden Gründen empfiehlt sich das Herstellen von Verbindungen zwischen virtuellen Netzwerken:

- **Regionsübergreifende Georedundanz und Geopräsenz**
	- Sie können Ihre eigene Georeplikation oder -synchronisierung mit sicheren Verbindungen einrichten, ohne Endgeräte mit Internetzugriff verwenden zu müssen.
	- Mit dem Azure-Load Balancer und Clustertechnologie von Microsoft oder Drittanbietern können Sie Workloads mit hoher Verfügbarkeit mit Georedundanz über mehrere Azure-Regionen hinweg einrichten. Ein wichtiges Beispiel ist die Einrichtung von SQL Always On mit Verfügbarkeitsgruppen, die sich über mehrere Azure-Regionen erstrecken.

- **Regionale Anwendungen mit mehreren Ebenen mit starker Isolierungsgrenze**
	- Innerhalb der gleichen Region können Sie Anwendungen mit mehreren Ebenen mit mehreren verbundenen virtuellen Netzwerken einrichten, die starke Isolierung und sichere Kommunikation zwischen den Ebenen aufweisen.

### Was Sie beachten sollten

In diesem Artikel erstellen Sie schrittweise eine Verbindung zwischen zwei virtuellen Netzwerken: VNet1 und VNet2. Sie müssen mit Netzwerken vertraut sein, um die IP-Adressbereiche zu ersetzen, die mit Ihren Anforderungen an den Netzwerkentwurf kompatibel sind.


![Verbinden von VNet zu VNet](./media/virtual-networks-configure-vnet-to-vnet-connection/IC727361.png)


- Die virtuellen Netzwerke können sich in der gleichen Azure-Region oder in verschiedenen Azure-Regionen (Standorte) befinden.

- Ein Clouddienst oder Endgerät mit Lastenausgleich darf auch dann NICHT mehrere virtuelle Netzwerke umfassen, wenn diese verbunden sind.

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

- Ein Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren oder sich für eine [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/) registrieren.

- Azure PowerShell 0.9.8-Cmdlets. Sie können diese Version aus dem Windows PowerShell-Abschnitt der [Downloadseite](http://azure.microsoft.com/downloads/) herunterladen und installieren. Dieser Artikel wurde für 0.9.8 geschrieben, es ist aber auch möglich, diese Schritte (mit geringfügigen Änderungen an den Cmdlets) mit der Vorschau für PowerShell 1.0 zu verwenden.

**Informationen zu diesen Schritten mit der Vorschau für Azure PowerShell 1.0**

	[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)] 


## 1\. Planen der IP-Adressbereiche


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

## 2\. Verbinden mit Ihrem Abonnement 

Öffnen Sie die PowerShell-Konsole, und stellen Sie eine Verbindung mit Ihrem Konto her. Verwenden Sie das folgende Beispiel, um eine Verbindung herzustellen:

		Add-AzureAccount

Wenn Sie über mehr als ein Abonnement verfügen, können Sie *Select-AzureSubscription* verwenden, um eine Verbindung mit dem gewünschten Abonnement herzustellen.

		Select-AzureSubscription "yoursubscription"

Wechseln Sie als Nächstes in den Azure-Ressourcen-Manager-Modus.
		
		Switch-AzureMode -Name AzureResourceManager

## 3\. Erstellen eines virtuellen Netzwerks


Nutzen Sie das Beispiel unten, um ein virtuelles Netzwerk und ein Gatewaysubnetz zu erstellen. Ersetzen Sie die Werte durch Ihre eigenen. In diesem Beispiel wird VNet1 erstellt. Später wiederholen Sie die Schritte zum Erstellen von VNet2.

Erstellen Sie zunächst eine Ressourcengruppe.

			New-AzureResourceGroup -Name testrg1 -Location 'West US'

Erstellen Sie als Nächstes das virtuelle Netzwerk. Das folgende Beispiel erstellt ein virtuelles Netzwerk namens *VNet1* und zwei Subnetze, eines mit dem Namen *GatewaySubnet* und das andere mit dem Namen *Subnet1*. Es ist wichtig, ein Subnetz speziell mit dem Namen *GatewaySubnet* zu erstellen. Wenn Sie einen anderen Namen wählen, schlägt die Verbindungskonfiguration fehl. Im folgenden Beispiel wird vom Gatewaysubnetz ein /28 verwendet. Sie können auch ein Gatewaysubnetz verwenden, das so klein wie /29 ist. Beachten Sie, dass einige Features (z. B. eine gleichzeitige ExpressRoute/Standort-zu-Standort-Verbindung) ein größeres Gatewaysubnetz von /27 erfordern. Dimensionieren Sie Ihr Gatewaysubnetz also so, dass zusätzliche Features unterstützt werden, die Sie möglicherweise in Zukunft verwenden möchten.

 		$subnet = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.1.0.0/28
		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.1.1.0/28'
		New-AzureVirtualNetwork -Name VNet1 -ResourceGroupName testrg1 -Location 'West US' -AddressPrefix 10.1.0.0/16 -Subnet $subnet, $subnet1

## 4\. Anfordern einer öffentlichen IP-Adresse


Als Nächstes müssen Sie eine öffentliche IP-Adresse anfordern, die dem Gateway zugewiesen wird, das Sie für Ihr VNet erstellen. Sie können keine IP-Adresse angeben, die Sie verwenden möchten, sondern die IP-Adresse wird Ihrem Gateway dynamisch zugewiesen. Diese IP-Adresse wird im nächsten Konfigurationsabschnitt verwendet.

Verwenden Sie das folgenden Beispiel. Die Zuordnungsmethode für diese Adresse muss dynamisch sein.


		$gwpip= New-AzurePublicIpAddress -Name gwpip1 -ResourceGroupName testrg1 -Location 'West US' -AllocationMethod Dynamic

## 5\. Erstellen der Gatewaykonfiguration


Die Gatewaykonfiguration definiert das zu verwendende Subnetz und die zu verwendende öffentliche IP-Adresse. Verwenden Sie das folgende Beispiel, um Ihre Gatewaykonfiguration zu erstellen.


		$vnet = Get-AzureVirtualNetwork -Name VNet1 -ResourceGroupName testrg1
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\. Erstellen des Gateways


In diesem Schritt erstellen Sie das virtuelle Netzwerkgateway für Ihr VNet. VNet-zu-VNet-Konfigurationen erfordern einen routenbasierten VPN-Typ. Das Erstellen eines Gateways kann eine Weile dauern, bitte haben Sie also etwas Geduld.

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg1 -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## 7\. Erstellen von VNet2


Nachdem Sie VNet1 konfiguriert haben, wiederholen Sie die vorherigen Schritte, um VNet2 und das Gateway zu konfigurieren. Nachdem Sie die Konfiguration der beiden VNETs und ihrer jeweiligen Gateways abgeschlossen haben, fahren Sie mit dem Abschnitt **Schritt 8. Verbinden der Gateways** fort.

## 8\. Verbinden der Gateways

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

## Überprüfen der Verbindungen

Zu diesem Zeitpunkt sind die VPN-Verbindungen, die mit dem Ressourcen-Manager erstellt wurden, im Vorschauportal nicht sichtbar. Sie können aber überprüfen, ob Ihre Verbindung erfolgreich war, indem Sie *Get-AzureVirtualNetworkGatewayConnection –Debug* verwenden. In Zukunft wird hierfür ein Cmdlet verfügbar sein, und Sie können Ihre Verbindung dann auch im Vorschauportal anzeigen.

Sie können das folgende Cmdlet-Beispiel verwenden. Ändern Sie die Werte entsprechend allen Verbindungen, die Sie überprüfen möchten. Wählen Sie bei Aufforderung die Option *A* für „Alle ausführen“ aus.

		Get-AzureVirtualNetworkGatewayConnection -Name vnet2connection -ResourceGroupName vnet2vnetrg -Debug 

 Nachdem der Cmdlet-Vorgang abgeschlossen ist, können Sie einen Bildlauf durchführen, um die Werte anzuzeigen. Im Beispiel unten wird der Verbindungsstatus als *Verbunden* angegeben, und Sie sehen die Eingangs- und Ausgangsbytes.

	Body:
	{
	  "name": "Vnet2Connection",
	  "id": "/subscriptions/a932c0e6-b5cb-4e68-b23d-5064372c8a3cdef/resourceGroups/Vnet2VnetRG/providers/Microsoft.Network/connections/Vnet2Connection",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "3c0bc049-860d-46ea-9909-e08098680a8bdef",
	    "virtualNetworkGateway1": {
	      "id": "/subscriptions/a932c0e6-b5cb-4e68-b23d-5064372c8a3cdef/resourceGroups/Vnet2VnetRG/providers/Microsoft.Network/virtualNetworkGateways/Vnet2Gw"
	    },
	    "virtualNetworkGateway2": {
	      "id": "/subscriptions/a932c0e6-b5cb-4e68-b23d-5064372c8a3cdef/resourceGroups/Vnet2VnetRG/providers/Microsoft.Network/virtualNetworkGateways/Vnet1Gw"
	    },
	    "connectionType": "Vnet2Vnet",
	    "routingWeight": 3,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 3359044,
	    "egressBytesTransferred": 4142451
	  }
	} 

## Herstellen der Verbindung mit vorhandenen VNets

Wenn Sie bereits virtuelle Netzwerke im Azure-Ressourcen-Manager-Modus erstellt haben und eine Verbindung mit diesen herstellen möchten, überprüfen Sie Folgendes:

- Sie verfügen über ein Gatewaysubnetz von mindestens /29 oder größer für jedes VNet.
- Die Adressbereiche für Ihre virtuellen Netzwerke überschneiden sich nicht.

Wenn Sie Ihren VNets Gatewaysubnetze hinzugefügt müssen, verwenden Sie das Beispiel unten, und ersetzen die Werte durch eigene. Achten Sie darauf, dass Sie das Gatewaysubnetz "GatewaySubnet" nennen. Wenn Sie einen anderen Namen wählen, wird die VPN-Konfiguration nicht ordnungsgemäß funktionieren.

	
		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet
		Set-AzureVirtualNetwork -VirtualNetwork $vnet

Nachdem Sie sich vergewissert haben, dass Ihre Gatewaysubnetze richtig konfiguriert sind, fahren Sie mit den Schritten unter **Schritt 4. Anfordern einer öffentlichen IP-Adresse**fort, und führen Sie die Anweisungen aus.


## Nächste Schritte

Sie können Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. [Erstellen Sie einen virtuellen Computer](../virtual-machines/virtual-machines-windows-tutorial.md).

Weitere Informationen zu VPN-Gateways finden Sie unter [Häufig gestellte Fragen zum VPN-Gateway](vpn-gateway-faq.md).

<!---HONumber=Oct15_HO4-->