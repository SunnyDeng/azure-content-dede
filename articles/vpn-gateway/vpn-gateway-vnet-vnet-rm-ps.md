<properties
   pageTitle="Erstellen einer VNet-zu-VNet-VPN-Gatewayverbindung mit dem Azure Resource Manager und PowerShell für VNets | Microsoft Azure"
   description="Dieser Artikel führt Sie durch das Verbinden virtueller Netzwerke mithilfe von Azure-Ressourcen-Manager und PowerShell."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/04/2016"
   ms.author="cherylmc"/>

# Konfigurieren einer VNet-zu-VNet-Verbindung mit Azure Resource Manager und PowerShell

> [AZURE.SELECTOR]
- [Klassisches Azure-Portal](virtual-networks-configure-vnet-to-vnet-connection.md)
- [PowerShell – Azure Resource Manager](vpn-gateway-vnet-vnet-rm-ps.md)

Dieser Artikel führt Sie durch die Schritte zum Erstellen einer Verbindung zwischen virtuellen Netzwerken mit dem **Resource Manager**-Bereitstellungsmodell und PowerShell. Die virtuellen Netzwerke können sich in derselben oder in unterschiedlichen Regionen befinden und aus demselben oder unterschiedlichen Abonnements stammen.

[AZURE.INCLUDE [vpn-gateway-table-vnet-vnet](../../includes/vpn-gateway-table-vnet-to-vnet-include.md)]

**Informationen zu Azure-Bereitstellungsmodellen**

[AZURE.INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]


## Über VNet-zu-VNet-Verbindungen

Das Verbinden eines virtuellen Netzwerks mit einem anderen virtuellen Netzwerk (VNet-zu-VNet) ähnelt dem Verbinden eines VNet mit einem lokalen Standort. Beide Verbindungstypen verwenden ein Azure VPN Gateway, um einen sicheren Tunnel mit IPSec/IKE bereitzustellen. Die VNets, die Sie verbinden, können sich in verschiedenen Regionen befinden. Oder in verschiedenen Abonnements. Sie können sogar VNet-zu-VNet-Kommunikation mit Konfigurationen für mehrere Standorte kombinieren. Auf diese Weise können Sie Netzwerktopologien einrichten, die standortübergreifende Konnektivität mit Konnektivität zwischen virtuellen Netzwerken kombinieren, wie in der folgenden Abbildung dargestellt:


![Informationen zu Verbindungen](./media/vpn-gateway-vnet-vnet-rm-ps/aboutconnections.png)
 
### Gründe für Verbindungen zwischen virtuellen Netzwerken

Aus den folgenden Gründen empfiehlt sich das Herstellen von Verbindungen zwischen virtuellen Netzwerken:

- **Regionsübergreifende Georedundanz und Geopräsenz**
	- Sie können Ihre eigene Georeplikation oder -synchronisierung mit sicheren Verbindungen einrichten, ohne Endpunkte mit Internetzugriff verwenden zu müssen.
	- Mit Azure Traffic Manager und Load Balancer können Sie eine hoch verfügbare Workload mit Georedundanz über mehrere Azure-Regionen hinweg einrichten. Ein wichtiges Beispiel ist die Einrichtung von SQL Always On mit Verfügbarkeitsgruppen, die sich über mehrere Azure-Regionen erstrecken.

- **Regionale Anwendungen mit mehreren Ebenen und Isolation oder Verwaltungsgrenze**
	- In derselben Region können Sie Anwendungen mit mehreren Ebenen und mehreren virtuellen Netzwerken einrichten, die aufgrund von Isolations- oder Verwaltungsanforderungen miteinander verbunden sind.


### FAQs zu VNet-zu-VNet

- Die virtuellen Netzwerke können sich in der gleichen Azure-Region oder in verschiedenen Azure-Regionen (Standorte) befinden.

- Ein Clouddienst oder Endpunkt mit Lastenausgleich darf auch dann NICHT mehrere virtuelle Netzwerke umfassen, wenn diese verbunden sind.

- Für das Verbinden virtueller Azure-Netzwerke sind keine lokalen VPN-Gateways erforderlich. Diese werden nur benötigt, wenn standortübergreifende Konnektivität erforderlich ist.

- VNet-zu-VNet unterstützt das Verbinden virtueller Netzwerke. Nicht unterstützt hingegen werden Verbindungen virtueller Computer oder Clouddienste, die sich NICHT in einem virtuellen Netzwerk befinden.

- VNet-zu-VNet erfordert Azure-VPN Gateways mit routenbasierten VPN-Typen (früher als „dynamisches Routing“ bezeichnet).

- Virtuelle Netzwerkverbindungen können gleichzeitig mit VPNs mit mehreren Standorten und maximal 10 (Standardgateways) bzw. 30 (Hochleistungsgateways) VPN-Tunneln für ein VPN Gateway des virtuellen Netzwerks verwendet werden, das Verbindungen mit anderen virtuellen Netzwerken oder lokalen Standorten herstellt.

- Die Adressräume der virtuellen Netzwerke und der lokalen Netzwerkstandorte dürfen sich nicht überschneiden. Wenn sich Adressräume überlappen, tritt bei der Erstellung von VNet-zu-VNet-Verbindungen ein Fehler auf.

- Redundante Tunnel zwischen einem Paar virtueller Netzwerke werden nicht unterstützt.

- Alle VPN-Tunnel des virtuellen Netzwerks verwenden die verfügbare Bandbreite auf dem Azure-VPN-Gateway und die gleiche SLA für die Verfügbarkeit des VPN-Gateways in Azure gemeinsam.

- VNet-zu-VNet-Datenverkehr wird über das Microsoft-Netzwerk übertragen, nicht über das Internet.

- VNet-zu-VNet-Datenverkehr innerhalb derselben Region ist in beiden Richtungen kostenlos. Ausgehender regionsübergreifender VNet-zu-VNet-Datenverkehr wird zu den Raten für die ausgehende Datenübertragung zwischen VNets basierend auf den Quellregionen berechnet. Ausführliche Informationen hierzu finden Sie auf der [Seite mit den Preisen](https://azure.microsoft.com/pricing/details/vpn-gateway/).


## Welche Schritte soll ich ausführen?

Dieser Artikel enthält verschiedene Schrittfolgen, die für mit dem Resource Manager-Bereitstellungsmodell erstellte VNets gelten. Die VNet-zu-VNet-Konfigurationsschritte richten sich danach, ob sich die VNets in demselben Abonnement oder in unterschiedlichen Abonnements befinden.

![Beide Verbindungen](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)


In Bezug auf die Konfigurationsschritte besteht der wichtigste Unterschied zwischen den beiden Optionen darin, ob Sie alle Ressourcen des virtuellen Netzwerks und des Gateways innerhalb derselben PowerShell-Sitzung erstellen und konfigurieren können. Dieses Dokument enthält die jeweils erforderlichen Schritte. Die obige Grafik zeigt sowohl eine VNet-zu-VNet-Verbindung für dasselbe Abonnement als auch eine abonnementübergreifende VNet-zu-VNet-Verbindung.


- [VNets in demselben Abonnement](#samesub)
- [VNets in unterschiedlichen Abonnements](#difsub)


## <a name ="samesub"/>Verbinden von VNets in demselben Abonnement

Diese Konfiguration gilt für virtuelle Netzwerke, die sich unter demselben Abonnement befinden. Dies ist im folgenden Diagramm dargestellt:

![VNet2VNet, gleiches Abonnement](./media/vpn-gateway-vnet-vnet-rm-ps/samesubscription.png)

### Voraussetzungen

- Stellen Sie sicher, dass Sie über ein Azure-Abonnement verfügen. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren oder sich für ein [kostenloses Konto](https://azure.microsoft.com/pricing/free-trial/) registrieren.
	
- Sie müssen die PowerShell-Cmdlets für Azure-Ressourcen-Manager installieren. Weitere Informationen zur Installation der PowerShell-Cmdlets finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md).

### <a name ="Step1"/>Schritt 1: Planen der IP-Adressbereiche


Eine wichtige Entscheidung betrifft die Bereiche, die Sie zum Konfigurieren Ihrer Netzwerkkonfiguration verwenden. Denken Sie daran, dass Sie sicherstellen müssen, dass keiner der VNet-Bereiche oder der Bereiche des lokalen Netzwerks Überschneidungen aufweist.

Mit den folgenden Schritten werden zwei virtuelle Netzwerke zusammen mit ihren jeweiligen Gatewaysubnetzen und Konfigurationen erstellt. Anschließend wird eine VPN-Gatewayverbindung zwischen den zwei VNets erstellt.

Verwenden Sie für diese Übung die folgenden Werte für die VNets:

**Werte für TestVNet1:**

- VNet-Name: TestVNet1
- Ressourcengruppe: TestRG1
- Standort: USA, Osten
- TestVNet1: 10.11.0.0/16 & 10.12.0.0/16
- FrontEnd: 10.11.0.0/24
- BackEnd: 10.12.0.0/24
- GatewaySubnet: 10.12.255.0/27
- DNS-Server: 8.8.8.8
- GatewayName: VNet1GW
- Öffentliche IP-Adresse: VNet1GWIP
- VPNType: RouteBased
- Verbindung (1to4): VNet1toVNet4
- Verbindung (1to5): VNet1toVNet5
- ConnectionType: VNet2VNet


**Werte für TestVNet4:**

- VNet-Name: TestVNet4
- TestVNet2: 10.41.0.0/16 & 10.42.0.0/16
- FrontEnd: 10.41.0.0/24
- BackEnd: 10.42.0.0/24
- GatewaySubnet: 10.42.255.0.0/27
- Ressourcengruppe: TestRG4
- Standort: USA, Westen
- DNS-Server: 8.8.8.8
- GatewayName: VNet4GW
- Öffentliche IP-Adresse: VNet4GWIP
- VPNType: RouteBased
- Verbindung: VNet4toVNet1
- ConnectionType: VNet2VNet



### <a name ="Step2"/>Schritt 2: Erstellen und Konfigurieren von TestVNet1

1. Deklarieren von Variablen

	Bei dieser Übung beginnen wir mit dem Deklarieren der Variablen. Im folgenden Beispiel werden die Variablen mit den Werten für diese Übung deklariert. Achten Sie darauf, dass Sie die Werte durch Ihre eigenen Werte ersetzen, wenn Sie die Konfiguration für die Produktion durchführen. Sie können diese Variablen verwenden, wenn Sie die Schritte durchgehen, um sich mit dieser Art der Konfiguration vertraut zu machen. Ändern Sie die Variablen, kopieren Sie sie, und fügen Sie sie in die PowerShell-Konsole ein.

		$Sub1          = "Replace_With_Your_Subcription_Name"
		$RG1           = "TestRG1"
		$Location1     = "East US"
		$VNetName1     = "TestVNet1"
		$FESubName1    = "FrontEnd"
		$BESubName1    = "Backend"
		$GWSubName1    = "GatewaySubnet"
		$VNetPrefix11  = "10.11.0.0/16"
		$VNetPrefix12  = "10.12.0.0/16"
		$FESubPrefix1  = "10.11.0.0/24"
		$BESubPrefix1  = "10.12.0.0/24"
		$GWSubPrefix1  = "10.12.255.0/27"
		$DNS1          = "8.8.8.8"
		$GWName1       = "VNet1GW"
		$GWIPName1     = "VNet1GWIP"
		$GWIPconfName1 = "gwipconf1"
		$Connection14  = "VNet1toVNet4"
		$Connection15  = "VNet1toVNet5"

2. Verbinden mit Abonnement 1

	Stellen Sie sicher, dass Sie in den PowerShell-Modus wechseln, um die Ressourcen-Manager-Cmdlets zu verwenden. Weitere Informationen finden Sie unter [Verwenden von Windows PowerShell mit Resource Manager](../powershell-azure-resource-manager.md).

	Öffnen Sie die PowerShell-Konsole, und stellen Sie eine Verbindung mit Ihrem Konto her. Verwenden Sie das folgende Beispiel, um eine Verbindung herzustellen:

		Login-AzureRmAccount

	Überprüfen Sie die Abonnements für das Konto.

		Get-AzureRmSubscription 

	Geben Sie das Abonnement an, das Sie verwenden möchten.

		Select-AzureRmSubscription -SubscriptionName $Sub1

3. Erstellen einer neuen Ressourcengruppe

		New-AzureRmResourceGroup -Name $RG1 -Location $Location1

4. Erstellen der Subnetzkonfigurationen für TestVNet1

	Im folgenden Beispiel wird ein virtuelles Netzwerk mit dem Namen TestVNet1 und drei Subnetzen erstellt. Diese haben die Namen „GatewaySubnet“, „FrontEnd“ und „Backend“. Beim Ersetzen der Werte ist es wichtig, dass Sie Ihrem Gatewaysubnetz immer den Namen „GatewaySubnet“ geben. Wenn Sie einen anderen Namen verwenden, tritt beim Erstellen des Gateways ein Fehler auf.

	Im folgenden Beispiel wird vom Gatewaysubnetz ein „/27“ verwendet. Sie können zwar aus technischer Sicht auch ein Gatewaysubnetz erstellen, indem Sie ein kleines Subnetz der Größe /29 verwenden, aber dies ist nicht zu empfehlen. Wir raten Ihnen vorsichtshalber zu größeren Subnetzen, z. B. /27 oder /26, falls Sie vorhandene oder zukünftige Konfigurationen nutzen möchten, für die unter Umständen ein größeres Gatewaysubnetz erforderlich ist.


		$fesub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName1 -AddressPrefix $FESubPrefix1
		$besub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName1 -AddressPrefix $BESubPrefix1
		$gwsub1 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName1 -AddressPrefix $GWSubPrefix1


5. Erstellen von TestVNet1

		New-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1 -Location $Location1 -AddressPrefix $VNetPrefix11,$VNetPrefix12 -Subnet $fesub1,$besub1,$gwsub1

6. Anfordern einer öffentlichen IP-Adresse

	Als Nächstes müssen Sie eine öffentliche IP-Adresse anfordern, die dem Gateway zugewiesen wird, das Sie für Ihr VNet erstellen. Sie können keine IP-Adresse angeben, die Sie verwenden möchten, sondern die IP-Adresse wird Ihrem Gateway dynamisch zugewiesen. Diese IP-Adresse wird im nächsten Konfigurationsabschnitt verwendet. Sehen Sie sich das Beispiel unten an. Die Zuordnungsmethode für diese Adresse muss dynamisch sein.

		$gwpip1    = New-AzureRmPublicIpAddress -Name $GWIPName1 -ResourceGroupName $RG1 -Location $Location1 -AllocationMethod Dynamic

7. Erstellen der Gatewaykonfiguration

	Die Gatewaykonfiguration definiert das zu verwendende Subnetz und die zu verwendende öffentliche IP-Adresse. Verwenden Sie das folgende Beispiel, um Ihre Gatewaykonfiguration zu erstellen.

		$vnet1     = Get-AzureRmVirtualNetwork -Name $VNetName1 -ResourceGroupName $RG1
		$subnet1   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet1
		$gwipconf1 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName1 -Subnet $subnet1 -PublicIpAddress $gwpip1

8. Erstellen des Gateways für TestVNet1

	In diesem Schritt erstellen Sie das virtuelle Netzwerkgateway für TestVNet1. VNet-zu-VNet-Konfigurationen erfordern einen routenbasierten VPN-Typ. Das Erstellen eines Gateways kann einige Zeit dauern (30 Minuten oder mehr).

		New-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1 -Location $Location1 -IpConfigurations $gwipconf1 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Schritt 3: Erstellen und Konfigurieren von TestVNet4

Nachdem Sie TestVNet1 konfiguriert haben, wiederholen Sie die Schritte zum Erstellen von TestVNet4. Führen Sie die unten angegebenen Schritte aus, und ersetzen Sie die Werte bei Bedarf durch Ihre eigenen Werte. Sie können diesen Schritt in derselben PowerShell-Sitzung ausführen, da hierfür dasselbe Abonnement gilt.

1. Deklarieren von Variablen

	Achten Sie darauf, dass Sie die Werte durch die Werte ersetzen, die Sie für die Konfiguration verwenden möchten.

		$RG4           = "TestRG4"
		$Location4     = "West US"
		$VnetName4     = "TestVNet4"
		$FESubName4    = "FrontEnd"
		$BESubName4    = "Backend"
		$GWSubName4    = "GatewaySubnet"
		$VnetPrefix41  = "10.41.0.0/16"
		$VnetPrefix42  = "10.42.0.0/16"
		$FESubPrefix4  = "10.41.0.0/24"
		$BESubPrefix4  = "10.42.0.0/24"
		$GWSubPrefix4  = "10.42.255.0/27"
		$DNS4          = "8.8.8.8"
		$GWName4       = "VNet4GW"
		$GWIPName4     = "VNet4GWIP"
		$GWIPconfName4 = "gwipconf4"
		$Connection41  = "VNet4toVNet1"

	Stellen Sie vor dem Fortfahren sicher, dass die Verbindung mit Abonnement 1 noch besteht.

2. Erstellen einer neuen Ressourcengruppe

		New-AzureRmResourceGroup -Name $RG4 -Location $Location4

3. Erstellen der Subnetzkonfigurationen für TestVNet4

		$fesub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName4 -AddressPrefix $FESubPrefix4
		$besub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName4 -AddressPrefix $BESubPrefix4
		$gwsub4 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName4 -AddressPrefix $GWSubPrefix4

4. Erstellen von TestVNet4

		New-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4 -Location $Location4 -AddressPrefix $VnetPrefix41,$VnetPrefix42 -Subnet $fesub4,$besub4,$gwsub4

5. Anfordern einer öffentlichen IP-Adresse

		$gwpip4    = New-AzureRmPublicIpAddress -Name $GWIPName4 -ResourceGroupName $RG4 -Location $Location4 -AllocationMethod Dynamic

6. Erstellen der Gatewaykonfiguration

		$vnet4     = Get-AzureRmVirtualNetwork -Name $VnetName4 -ResourceGroupName $RG4
		$subnet4   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet4
		$gwipconf4 = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName4 -Subnet $subnet4 -PublicIpAddress $gwpip4

7. Erstellen des TestVNet4-Gateways

		New-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4 -Location $Location4 -IpConfigurations $gwipconf4 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Schritt 4: Verbinden der Gateways

1. Abrufen von beiden Gateways des virtuellen Netzwerks

	Da sich beide Gateways in demselben Abonnement befinden, kann dieser Schritt in diesem Beispiel in derselben PowerShell-Sitzung durchgeführt werden.

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1
		$vnet4gw = Get-AzureRmVirtualNetworkGateway -Name $GWName4 -ResourceGroupName $RG4


2. Erstellen der Verbindung von TestVNet1 mit TestVNet4

	In diesem Schritt erstellen Sie die Verbindung von TestVNet1 zu TestVNet4. In den Beispielen wird auf einen gemeinsam verwendeten Schlüssel verwiesen. Sie können eigene Werte für den gemeinsam verwendeten Schlüssel verwenden. Wichtig ist dabei, dass der gemeinsam verwendete Schlüssel für beide Verbindungen übereinstimmen muss. Das Erstellen einer Verbindung kann etwas dauern.

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection14 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet4gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

3. Erstellen der Verbindung von TestVNet4 mit TestVNet1

	Dieser Schritt ähnelt dem obigen Schritt, aber hier erstellen Sie jetzt die Verbindung von TestVNet4 mit TestVNet1. Stellen Sie sicher, dass die freigegebenen Schlüssel übereinstimmen.

		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection41 -ResourceGroupName $RG4 -VirtualNetworkGateway1 $vnet4gw -VirtualNetworkGateway2 $vnet1gw -Location $Location4 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

	Nach einigen Minuten sollte die Verbindung hergestellt werden.

## <a name ="Verify"/>Überprüfen einer VNet-zu-VNet-Verbindung

In den Beispielen unten wird verdeutlicht, wie Sie Ihre Verbindung überprüfen. Achten Sie darauf, die Werte für Ihre Umgebung anzupassen.

### So überprüfen Sie Ihre Verbindung mithilfe des Azure-Portals

Sie können eine VPN-Verbindung im Azure-Portal überprüfen, indem Sie zu **Virtuelle Netzwerkgateways** navigieren, **auf den Gatewaynamen klicken** und dann **Einstellungen** und schließlich **Verbindungen** auswählen. Wenn Sie den Namen der Verbindung auswählen, können Sie weitere Informationen auf dem Blatt **Verbindung** anzeigen.


### So überprüfen Sie Ihre Verbindung mithilfe der PowerShell

Sie können auch überprüfen, ob Ihre Verbindung erfolgreich war, indem Sie *Get-AzureRmVirtualNetworkGatewayConnection –Debug* verwenden. Sie können das folgende Beispiel verwenden und die Werte so ändern, dass sie Ihren eigenen Werten entsprechen. Wählen Sie bei Aufforderung die Option „A“ für „Alle ausführen“ aus.

	Get-AzureRmVirtualNetworkGatewayConnection -Name $Connection1 -ResourceGroupName $RG1 -Debug

Nachdem der Cmdlet-Vorgang abgeschlossen ist, können Sie einen Bildlauf durchführen, um die Werte anzuzeigen. In der PowerShell-Ausgabe des Beispiels unten wird der Verbindungsstatus als *Verbunden* angegeben, und Sie sehen die Eingangs- und Ausgangsbytes.



	AuthorizationKey           :
	VirtualNetworkGateway1     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
	VirtualNetworkGateway2     : Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
	LocalNetworkGateway2       :
	Peer                       :
	ConnectionType             : Vnet2Vnet
	RoutingWeight              : 0
	SharedKey                  : AzureA1b2C3
	ConnectionStatus           : Connected
	EgressBytesTransferred     : 1376
	IngressBytesTransferred    : 1232
	ProvisioningState          : Succeeded
	VirtualNetworkGateway1Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW"
	VirtualNetworkGateway2Text : "/subscriptions/<SubscriptionID>/resourceGroups/TestRG4/providers/Microsoft.Network/virtualNetworkGateways/VNet4GW"
	LocalNetworkGateway2Text   :
	PeerText                   :
	ResourceGroupName          : TestRG1
	Location                   : eastus
	ResourceGuid               : 173489d1-37e2-482c-b8b8-6ca69fc3e069
	Tag                        : {}
	TagsTable                  :
	Name                       : VNet1toVNet4
	Id                         : /subscriptions/<SubscriptionID>/resourceGroups/TestRG1/providers/Micr osoft.Network/connections/VNet1toVNet4

## <a name ="difsub"/>Verbinden von VNets aus unterschiedlichen Abonnements

Mit den unten angegebenen Konfigurationsschritten wird eine weitere VNet-zu-VNet-Verbindung hinzugefügt, um TestVNet1 mit TestVNet5 zu verbinden, also mit einem VNet aus einem anderen Abonnement. Der Unterschied besteht hierbei darin, dass ein Teil der Konfigurationsschritte in einer separaten PowerShell-Sitzung im Kontext des zweiten Abonnements ausgeführt werden muss. Dies gilt besonders, wenn die beiden Abonnements unterschiedlichen Organisationen gehören. Wenn Sie die Schritte unten ausführen, ergibt sich die Konfiguration, die im folgenden Diagramm dargestellt ist:

![VNet2VNet, unterschiedliche Abonnements](./media/vpn-gateway-vnet-vnet-rm-ps/differentsubscription.png)

Die Anleitung unten ist eine Fortsetzung der vorherigen obigen Schritte. Sie müssen [Schritt 1](#Step1) und [Schritt 2](#Step2) ausführen, um TestVNet1 und das VPN Gateway für TestVNet1 zu erstellen und zu konfigurieren. Wenn Sie nur die Verbindung von VNets in unterschiedlichen Abonnements planen, müssen Sie sich mit den Schritten 3 und 4 aus der vorherigen Übung nicht befassen und können unten mit Schritt 5 fortfahren.

### Schritt 5: Überprüfen der zusätzlichen IP-Adressbereiche

Es ist wichtig sicherzustellen, dass sich der IP-Adressbereich des neuen virtuellen Netzwerks (TestVNet5) nicht mit einem Ihrer VNet-Bereiche oder Gatewaybereiche des lokalen Netzwerks überlappt.

In diesem Beispiel können die virtuellen Netzwerke unterschiedlichen Organisationen gehören. Bei dieser Übung können Sie für TestVNet5 die folgenden Werte verwenden:

**Werte für TestVNet5:**

- VNet-Name: TestVNet5
- Ressourcengruppe: TestRG5
- Standort: Japan, Osten
- TestVNet5: 10.51.0.0/16 & 10.52.0.0/16
- FrontEnd: 10.51.0.0/24
- BackEnd: 10.52.0.0/24
- GatewaySubnet: 10.52.255.0.0/27
- DNS-Server: 8.8.8.8
- GatewayName: VNet5GW
- Öffentliche IP-Adresse: VNet5GWIP
- VPNType: RouteBased
- Verbindung: VNet5toVNet1
- ConnectionType: VNet2VNet

**Weitere Werte für TestVNet1:**

- Verbindung: VNet1toVNet5


### Schritt 6: Erstellen und Konfigurieren von TestVNet5

Dieser Schritt muss im Kontext des neuen Abonnements ausgeführt werden. Dieser Teil kann vom Administrator in einer anderen Organisation ausgeführt werden, in deren Besitz sich das Abonnement befindet.

1. Deklarieren von Variablen

	Achten Sie darauf, dass Sie die Werte durch die Werte ersetzen, die Sie für die Konfiguration verwenden möchten.

		$Sub5          = "Replace_With_the_New_Subcription_Name"
		$RG5           = "TestRG5"
		$Location5     = "Japan East"
		$VnetName5     = "TestVNet5"
		$FESubName5    = "FrontEnd"
		$BESubName5    = "Backend"
		$GWSubName5    = "GatewaySubnet"
		$VnetPrefix51  = "10.51.0.0/16"
		$VnetPrefix52  = "10.52.0.0/16"
		$FESubPrefix5  = "10.51.0.0/24"
		$BESubPrefix5  = "10.52.0.0/24"
		$GWSubPrefix5  = "10.52.255.0/27"
		$DNS5          = "8.8.8.8"
		$GWName5       = "VNet5GW"
		$GWIPName5     = "VNet5GWIP"
		$GWIPconfName5 = "gwipconf5"
		$Connection51  = "VNet5toVNet1"

2. Verbinden mit Abonnement 5

	Öffnen Sie die PowerShell-Konsole, und stellen Sie eine Verbindung mit Ihrem Konto her. Verwenden Sie das folgende Beispiel, um eine Verbindung herzustellen:

		Login-AzureRmAccount

	Überprüfen Sie die Abonnements für das Konto.

		Get-AzureRmSubscription 

	Geben Sie das Abonnement an, das Sie verwenden möchten.

		Select-AzureRmSubscription -SubscriptionName $Sub5

3. Erstellen einer neuen Ressourcengruppe

		New-AzureRmResourceGroup -Name $RG5 -Location $Location5

4. Erstellen der Subnetzkonfigurationen für TestVNet4
	
		$fesub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $FESubName5 -AddressPrefix $FESubPrefix5
		$besub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $BESubName5 -AddressPrefix $BESubPrefix5
		$gwsub5 = New-AzureRmVirtualNetworkSubnetConfig -Name $GWSubName5 -AddressPrefix $GWSubPrefix5

5. Erstellen von TestVNet5

		New-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5 -Location $Location5 -AddressPrefix $VnetPrefix51,$VnetPrefix52 -Subnet $fesub5,$besub5,$gwsub5

6. Anfordern einer öffentlichen IP-Adresse

		$gwpip5    = New-AzureRmPublicIpAddress -Name $GWIPName5 -ResourceGroupName $RG5 -Location $Location5 -AllocationMethod Dynamic


7. Erstellen der Gatewaykonfiguration

		$vnet5     = Get-AzureRmVirtualNetwork -Name $VnetName5 -ResourceGroupName $RG5
		$subnet5   = Get-AzureRmVirtualNetworkSubnetConfig -Name "GatewaySubnet" -VirtualNetwork $vnet5
		$gwipconf5 = New-AzureRmVirtualNetworkGatewayIpConfig 

8. Erstellen des TestVNet5-Gateways

		New-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5 -Location $Location5 -IpConfigurations $gwipconf5 -GatewayType Vpn -VpnType RouteBased -GatewaySku Standard

### Schritt 7: Verbinden der Gateways

Da sich die Gateways in diesem Beispiel in unterschiedlichen Abonnements befinden, haben wir diesen Schritt in zwei PowerShell-Sitzungen mit den Bezeichnungen [Abonnement 1] und [Abonnement 5] aufgeteilt.

1. **[Abonnement 1]** Abrufen des Gateways für das virtuelle Netzwerk für Abonnement 1

	Stellen Sie sicher, dass Sie sich an „Abonnement 1“ anmelden und die Verbindung damit herstellen.

		$vnet1gw = Get-AzureRmVirtualNetworkGateway -Name $GWName1 -ResourceGroupName $RG1

	Kopieren Sie die Ausgabe der folgenden Elemente, und senden Sie diese per E-Mail oder mit einem anderen Verfahren an den Administrator von Abonnement 5.

		$vnet1gw.Name
		$vnet1gw.Id

	Diese beiden Elemente weisen Werte auf, die den Werten in der folgenden Beispielausgabe ähneln:

		PS D:> $vnet1gw.Name
		VNet1GW
		PS D:> $vnet1gw.Id
		/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroupsTestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW

2. **[Abonnement 5]** Abrufen des Gateways für das virtuelle Netzwerk für Abonnement 5

	Stellen Sie sicher, dass Sie sich an „Abonnement 5“ anmelden und die Verbindung damit herstellen.

		$vnet5gw = Get-AzureRmVirtualNetworkGateway -Name $GWName5 -ResourceGroupName $RG5

	Kopieren Sie die Ausgabe der folgenden Elemente, und senden Sie diese per E-Mail oder mit einem anderen Verfahren an den Administrator von Abonnement 1.

		$vnet5gw.Name
		$vnet5gw.Id

	Diese beiden Elemente weisen Werte auf, die den Werten in der folgenden Beispielausgabe ähneln:

		PS C:\> $vnet5gw.Name
		VNet5GW
		PS C:\> $vnet5gw.Id
		/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW

3. **[Abonnement 1]** Erstellen der Verbindung von TestVNet1 mit TestVNet5

	In diesem Schritt erstellen Sie die Verbindung von TestVNet1 zu TestVNet5. Der Unterschied hierbei ist, dass $vnet5gw nicht direkt abgerufen werden kann, da sich dieses Element in einem anderen Abonnement befindet. Sie müssen ein neues PowerShell-Objekt mit den Werten erstellen, die von Abonnement 1 in den obigen Schritten bereitgestellt werden. Ersetzen Sie Name, ID und freigegebenen Schlüssel durch Ihre eigenen Werte. Wichtig ist dabei, dass der gemeinsam verwendete Schlüssel für beide Verbindungen übereinstimmen muss. Das Erstellen einer Verbindung kann etwas dauern.

	Stellen Sie sicher, dass Sie eine Verbindung mit Abonnement 1 herstellen.
	
		$vnet5gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet5gw.Name = "VNet5GW"
		$vnet5gw.Id   = "/subscriptions/66c8e4f1-ecd6-47ed-9de7-7e530de23994/resourceGroups/TestRG5/providers/Microsoft.Network/virtualNetworkGateways/VNet5GW"
		$Connection15 = "VNet1toVNet5"
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection15 -ResourceGroupName $RG1 -VirtualNetworkGateway1 $vnet1gw -VirtualNetworkGateway2 $vnet5gw -Location $Location1 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

4. **[Abonnement 5]** Erstellen der Verbindung von TestVNet5 mit TestVNet1

	Dieser Schritt ähnelt dem obigen Schritt, aber hier erstellen Sie jetzt die Verbindung von TestVNet5 mit TestVNet1. Auch hier gilt der gleiche Prozess zur Erstellung eines PowerShell-Objekts basierend auf den Werten, die aus Abonnement 1 abgerufen wurden. Stellen Sie in diesem Schritt sicher, dass die freigegebenen Schlüssel übereinstimmen.

	Stellen Sie sicher, dass Sie eine Verbindung mit Abonnement 5 herstellen.

		$vnet1gw = New-Object Microsoft.Azure.Commands.Network.Models.PSVirtualNetworkGateway
		$vnet1gw.Name = "VNet1GW"
		$vnet1gw.Id   = "/subscriptions/b636ca99-6f88-4df4-a7c3-2f8dc4545509/resourceGroups/TestRG1/providers/Microsoft.Network/virtualNetworkGateways/VNet1GW "
		New-AzureRmVirtualNetworkGatewayConnection -Name $Connection51 -ResourceGroupName $RG5 -VirtualNetworkGateway1 $vnet5gw -VirtualNetworkGateway2 $vnet1gw -Location $Location5 -ConnectionType Vnet2Vnet -SharedKey 'AzureA1b2C3'

5. Überprüfen der Verbindung

	Nach Abschluss dieser Schritte können Sie die Verbindung überprüfen, indem Sie die Methoden unter [Überprüfen einer VNet-zu-VNet-Verbindung](#Verify) verwenden.

## Nächste Schritte

Sobald die Verbindung hergestellt ist, können Sie Ihren virtuellen Netzwerken virtuelle Computer hinzufügen. Für diese Schritte finden Sie Informationen unter [Erstellen eines virtuellen Computers](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=AcomDC_0309_2016-->