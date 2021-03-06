<properties
   pageTitle="Erstellen eines virtuellen Netzwerks mit einer Site-to-Site-VPN-Verbindung mit dem Azure-Ressourcen-Manager und PowerShell | Microsoft Azure"
   description="In diesem Artikel werden Sie durch die Erstellung eines VNet mit dem Ressourcen-Manager-Modell und das Herstellen einer dazugehörigen Verbindung mit dem lokalen Netzwerk per Standort-zu-Standort-VPN Gateway-Verbindung geführt. Enthält zusätzliche Schritte zum Ändern der IP-Adresspräfixe für vorhandene lokale Standorte."
   services="vpn-gateway"
   documentationCenter="na"
   authors="cherylmc"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="vpn-gateway"
   ms.devlang="na"
   ms.topic="hero-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/20/2015"
   ms.author="cherylmc"/>

# Erstellen eines virtuellen Netzwerks mit einer Standort-zu-Standort-VPN-Verbindung mit PowerShell

> [AZURE.SELECTOR]
- [Azure Portal](vpn-gateway-site-to-site-create.md)
- [PowerShell - Resource Manager](vpn-gateway-create-site-to-site-rm-powershell.md)

In diesem Artikel werden Sie durch das Erstellen eines virtuellen Netzwerks und das Herstellen einer Standort-zu-Standort-VPN-Verbindung mit Ihrem lokalen Netzwerk mithilfe des Bereitstellungsmodells aus dem Azure-Ressourcen-Manager geführt. Sie können den Artikel für das entsprechende Bereitstellungsmodell und Bereitstellungstool mithilfe der obigen Registerkarten auswählen.

>[AZURE.NOTE]Sie sollten wissen, dass Azure derzeit mit zwei Bereitstellungsmodellen arbeitet: der Bereitstellung mit dem Ressourcen-Manager und der klassischen Bereitstellung. Bevor Sie Ihre Konfiguration beginnen, sollten Sie sicherstellen, dass Sie die Bereitstellungsmodelle und -tools verstehen. Informationen zu den Bereitstellungsmodellen finden Sie unter [Azure-Bereitstellungsmodelle](../azure-classic-rm.md).

## Vorbereitungen

Vergewissern Sie sich vor Beginn der Konfiguration, dass Sie über Folgendes verfügen:

- Ein kompatibles VPN-Gerät (und eine Person, die es konfigurieren kann). Weitere Informationen finden Sie unter [Informationen zu VPN-Geräten](vpn-gateway-about-vpn-devices.md). Wenn Sie sich mit dem Konfigurieren des VPN-Geräts oder mit den IP-Adressbereichen Ihrer lokalen Netzwerkkonfiguration nicht auskennen, müssen Sie sich an eine Person wenden, die Ihnen diese Details liefern kann.

- Eine externe öffentliche IP-Adresse für Ihr VPN-Gerät. Diese IP-Adresse darf sich nicht hinter einer NAT befinden.
	
- Ein Azure-Abonnement. Wenn Sie noch kein Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren oder sich für eine [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/) registrieren.

- Azure PowerShell 0.9.8-Cmdlets. Sie können diese Version aus dem Windows PowerShell-Abschnitt der [Downloadseite](http://azure.microsoft.com/downloads/) herunterladen und installieren. Dieser Artikel wurde für 0.9.8 geschrieben, es ist aber auch möglich, diese Schritte (mit geringfügigen Änderungen an den Cmdlets) mit der Vorschau für PowerShell 1.0 zu verwenden.

**Informationen zu diesen Schritten mit der Vorschau für Azure PowerShell 1.0**

	[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)] 
	


## 1\. Verbinden mit Ihrem Abonnement 


Öffnen Sie die PowerShell-Konsole, und stellen Sie eine Verbindung mit Ihrem Konto her.

**Hinweis:** Die folgenden Anweisungen basieren auf Version 0.9.8 der Azure PowerShell-Cmdlets.

Verwenden Sie das folgende Beispiel, um eine Verbindung herzustellen:

		Add-AzureAccount

Wenn Sie über mehr als ein Abonnement verfügen, können Sie *Select-AzureSubscription* verwenden, um eine Verbindung mit dem gewünschten Abonnement herzustellen.

		Select-AzureSubscription "yoursubscription"

Wechseln Sie als Nächstes in den Azure-Ressourcen-Manager-Modus.
		
		Switch-AzureMode -Name AzureResourceManager

## 2\. Erstellen eines virtuelles Netzwerks und eines Gatewaysubnetzes

- Wenn Sie bereits über ein virtuelles Netzwerk mit einem Gatewaysubnetz verfügen, können Sie direkt mit **Schritt 3: Hinzufügen Ihres lokalen Standorts** fortfahren. 
- Falls Sie bereits über ein virtuelles Netzwerk verfügen und Sie dem VNet ein Gatewaysubnetz hinzufügen möchten, helfen Ihnen die Informationen unter [Hinzufügen eines Gatewaysubnetzes zu einem VNet](#gatewaysubnet) weiter.

### So erstellen Sie ein virtuelles Netzwerk und ein Gatewaysubnetz

Nutzen Sie das Beispiel unten, um ein virtuelles Netzwerk und ein Gatewaysubnetz zu erstellen. Ersetzen Sie die Werte durch Ihre eigenen.

Erstellen Sie zunächst eine Ressourcengruppe:

	
		New-AzureResourceGroup -Name testrg -Location 'West US'

Erstellen Sie als Nächstes das virtuelle Netzwerk. Stellen Sie sicher, dass sich die angegebenen Adressräume und die Adressräume im lokalen Netzwerk nicht überschneiden.

Im folgenden Beispiel werden ein virtuelles Netzwerk mit dem Namen *testvnet* und zwei Subnetze erstellt: eines mit der Bezeichnung *GatewaySubnet* und das andere mit der Bezeichnung *Subnet1*. Es ist wichtig, ein Subnetz speziell mit dem Namen *GatewaySubnet* zu erstellen. Wenn Sie einen anderen Namen wählen, schlägt die Verbindungskonfiguration fehl.

		$subnet1 = New-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.0.0/28
		$subnet2 = New-AzureVirtualNetworkSubnetConfig -Name 'Subnet1' -AddressPrefix '10.0.1.0/28'
		New-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg -Location 'West US' -AddressPrefix 10.0.0.0/16 -Subnet $subnet1, $subnet2

### <a name="gatewaysubnet"></a>So fügen Sie einem VNet ein Gatewaysubnetz hinzu (optional)

Dieser Schritt ist nur erforderlich, wenn Sie einem VNet ein Gatewaysubnetz hinzufügen müssen.

Wenn Sie bereits über ein vorhandenes virtuelles Netzwerk verfügen und ein Gatewaysubnetz hinzufügen möchten, können Sie Ihr Gatewaysubnetz anhand des folgenden Beispiels erstellen. Achten Sie darauf, dass Sie das Gatewaysubnetz "GatewaySubnet" nennen. Wenn Sie einen anderen Namen verwenden, erstellen Sie zwar ein Subnetz, aber es wird von Azure nicht als Gatewaysubnetz angesehen.

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName testrg -Name testvnet
		Add-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -AddressPrefix 10.0.3.0/28 -VirtualNetwork $vnet

Legen Sie jetzt die Konfiguration fest.

		Set-AzureVirtualNetwork -VirtualNetwork $vnet

## 3\. Hinzufügen Ihres lokalen Standorts

In einem virtuellen Netzwerk ist mit dem *lokalen Standort* in der Regel Ihr lokaler Standort gemeint. Sie müssen diesen Standort benennen, damit Azure darauf verweisen kann.

Sie geben auch das Adressraumpräfix für den lokalen Standort an. Azure verwendet das angegebene Präfix der IP-Adresse, um zu erkennen, welcher Datenverkehr an den lokalen Standort gesendet wird. Dies bedeutet, dass Sie jedes Adresspräfix angeben müssen, das dem lokalen Standort zugeordnet werden soll. Sie können diese Präfixe einfach aktualisieren, wenn sich Ihr lokales Netzwerk ändert.

Beachten Sie beim Verwenden der PowerShell-Beispiele Folgendes:
	
- *GatewayIPAddress* ist die IP-Adresse Ihres lokalen VPN-Geräts. Das VPN-Gerät darf sich nicht hinter einer NAT befinden. 
- *AddressPrefix* ist Ihr lokaler Adressraum.

Gehen Sie wie folgt vor, um einen lokalen Standort mit einem einzelnen Adresspräfix hinzuzufügen:

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix '10.5.51.0/24'

Gehen Sie wie folgt vor, um einen lokalen Standort mit mehreren Adresspräfixen hinzuzufügen:

		New-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg -Location 'West US' -GatewayIpAddress '23.99.221.164' -AddressPrefix @('10.0.0.0/24','20.0.0.0/24')

### So ändern Sie die IP-Adresspräfixe für den lokalen Standort

Es kann vorkommen, dass sich Ihre Präfixe für den lokalen Standort ändern. Die Schritte, die Sie zum Ändern der IP-Adresspräfixe ausführen, richten sich danach, ob Sie eine VPN Gateway-Verbindung erstellt haben. Weitere Informationen finden Sie unter [Ändern von IP-Adresspräfixen für einen lokalen Standort](#to-modify-ip-address-prefixes-for-a-local-site).


## 4\. Anfordern einer öffentlichen IP-Adresse für das Gateway

Als Nächstes müssen Sie eine öffentliche IP-Adresse anfordern, die Ihrem Azure-VNet-VPN-Gateway zugewiesen wird. Dies ist nicht die gleiche IP-Adresse, die dem VPN-Gerät zugewiesen wird. Sie wird stattdessen dem Azure-VPN-Gateway selbst zugewiesen. Sie können nicht die IP-Adresse angeben, die Sie verwenden möchten. Diese wird Ihrem Gateway dynamisch zugewiesen. Sie verwenden diese IP-Adresse bei der Konfiguration des lokalen VPN-Geräts für die Verbindung zum Gateway.

Verwenden Sie das folgende PowerShell-Beispiel. Die Zuordnungsmethode für diese Adresse muss dynamisch sein.

		$gwpip= New-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg -Location 'West US' -AllocationMethod Dynamic

## 5\. Erstellen der Gateway-IP-Adressierung

Die Gatewaykonfiguration definiert das zu verwendende Subnetz und die zu verwendende öffentliche IP-Adresse. Verwenden Sie das folgende Beispiel, um Ihre Gatewaykonfiguration zu erstellen.


		$vnet = Get-AzureVirtualNetwork -Name testvnet -ResourceGroupName testrg
		$subnet = Get-AzureVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet
		$gwipconfig = New-AzureVirtualNetworkGatewayIpConfig -Name gwipconfig1 -SubnetId $subnet.Id -PublicIpAddressId $gwpip.Id 

## 6\. Erstellen des Gateways

In diesem Schritt erstellen Sie das Gateway des virtuellen Netzwerks. Beachten Sie, dass die Erstellung eines Gateways eine Weile dauert.

Verwenden Sie die folgenden Werte:

- Der Gatewaytyp ist *Vpn*.
- „VpnType“ kann routenbasiert* (in einigen Dokumentationen als dynamisches Gateway bezeichnet) oder *richtlinienbasiert* sein (in einigen Dokumentationen als statisches Gateway bezeichnet). Weitere Informationen zu VPN-Gatewaytypen finden Sie unter [Informationen zu VPN-Gateways](vpn-gateway-about-vpngateways.md). 	

		New-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg -Location 'West US' -IpConfigurations $gwipconfig -GatewayType Vpn -VpnType RouteBased

## 7\. Konfigurieren des VPN-Geräts

An diesem Punkt benötigen Sie die öffentliche IP-Adresse des Gateways des virtuellen Netzwerks für die Konfiguration Ihres lokalen VPN-Geräts. Halten Sie mit Ihrem Gerätehersteller für spezifische Informationen zur Konfiguration Rücksprache. Darüber hinaus finden Sie weitere Informationen unter [VPN-Geräte](http://go.microsoft.com/fwlink/p/?linkid=615099).

Um die öffentliche IP-Adresse des Gateways des virtuellen Netzwerks zu ermitteln, verwenden Sie das folgende Beispiel:

	Get-AzurePublicIpAddress -Name gwpip -ResourceGroupName testrg

## 8\. Erstellen der VPN-Verbindung

Erstellen Sie als Nächstes die Site-to-Site-VPN-Verbindung zwischen dem Gateway Ihres virtuellen Netzwerks und Ihrem VPN-Gerät. Achten Sie darauf, dass Sie die Werte durch Ihre eigenen ersetzen. Der gemeinsame Schlüssel muss dem Wert entsprechen, den Sie für Ihre VPN-Gerätekonfiguration verwendet haben.

		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

Die Verbindung wird nach kurzer Zeit hergestellt.

## 9\. Überprüfen einer VPN-Verbindung

Zu diesem Zeitpunkt sind die Standort-zu-Standort-VPN-Verbindungen, die mit dem Ressourcen-Manager erstellt wurden, im Vorschauportal nicht sichtbar. Sie können aber überprüfen, ob Ihre Verbindung erfolgreich war, indem Sie *Get-AzureVirtualNetworkGatewayConnection –Debug* verwenden. In Zukunft wird hierfür ein Cmdlet verfügbar sein, und Sie können Ihre Verbindung dann auch im Vorschauportal anzeigen.

Sie können das folgende Cmdlet-Beispiel verwenden und die Werte so konfigurieren, dass sie Ihren eigenen Werten entsprechen. Wählen Sie bei Aufforderung die Option *A* für „Alle ausführen“ aus.

		Get-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

 Nachdem der Cmdlet-Vorgang abgeschlossen ist, können Sie einen Bildlauf durchführen, um die Werte anzuzeigen. Im Beispiel unten wird der Verbindungsstatus als *Verbunden* angegeben, und Sie sehen die Eingangs- und Ausgangsbytes.

	Body:
	{
	  "name": "localtovon",
	  "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/connections/loca
	ltovon",
	  "properties": {
	    "provisioningState": "Succeeded",
	    "resourceGuid": "1c484f82-23ec-47e2-8cd8-231107450446b",
	    "virtualNetworkGateway1": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworkGa
	teways/vnetgw1"
	    },
	    "localNetworkGateway2": {
	      "id":
	"/subscriptions/086cfaa0-0d1d-4b1c-9455-f8e3da2a0c7789/resourceGroups/testrg/providers/Microsoft.Network/localNetworkGate
	ways/LocalSite"
	    },
	    "connectionType": "IPsec",
	    "routingWeight": 10,
	    "sharedKey": "abc123",
	    "connectionStatus": "Connected",
	    "ingressBytesTransferred": 33509044,
	    "egressBytesTransferred": 4142431
	  }


## So ändern Sie IP-Adresspräfixe für einen lokalen Standort

Verwenden Sie die unten angegebene Anleitung, wenn Sie die Präfixe für Ihren lokalen Standort ändern müssen. Es werden zwei Gruppen von Anweisungen bereitgestellt. Sie richten sich danach, ob Sie Ihre VPN Gateway-Verbindung bereits erstellt haben oder nicht.

### Hinzufügen oder Entfernen von Präfixen ohne VPN Gateway-Verbindung


- Verwenden Sie das unten angegebene Beispiel, um zusätzliche Adresspräfixe einem lokalen Standort **hinzuzufügen**, den Sie erstellt haben, aber der noch nicht über eine VPN-Gatewayverbindung verfügt.

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- Verwenden Sie das unten angegebene Beispiel, um ein Adresspräfix aus einem lokalen Standort **zu entfernen**, der noch nicht über eine VPN-Verbindung verfügt. Lassen Sie die Präfixe weg, die Sie nicht mehr benötigen. In diesem Beispiel wird das Präfix 20.0.0.0/24 (aus dem vorherigen Beispiel) nicht mehr benötigt, daher wird der lokale Standort aktualisiert und das Präfix ausgeschlossen.

		local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Hinzufügen oder Entfernen von Präfixen mit VPN Gateway-Verbindung

Wenn Sie die VPN-Verbindung erstellt haben und die IP-Adresspräfixe Ihres lokalen Standorts hinzufügen oder entfernen möchten, müssen Sie die folgenden Schritte der Reihenfolge nach ausführen. Dies führt zu Ausfallzeiten für Ihre VPN-Verbindung, da Sie das Gateway entfernen und neu erstellen müssen. Da Sie aber eine IP-Adresse für die Verbindung angefordert haben, müssen Sie Ihren lokalen VPN-Router nicht neu konfigurieren. Dies ist nur erforderlich, wenn Sie sich dafür entscheiden, die zuvor verwendeten Werte zu ändern.

 
1. Entfernen Sie die Gatewayverbindung. 
2. Ändern Sie die Präfixe für Ihren lokalen Standort. 
3. Erstellen Sie eine neue Gatewayverbindung. 

Sie können das folgende Beispiel als Hilfe verwenden.


		$gateway1 = Get-AzureVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

		remove-AzureVirtualNetworkGatewayConnection -Name vnetgw1 -ResourceGroupName testrg

		$local = Get-AzureLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	

		New-AzureVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'


## Nächste Schritte

Fügen Sie einen virtuellen Computer zu Ihrem virtuellen Netzwerk hinzu. [Erstellen Sie einen virtuellen Computer](../virtual-machines/virtual-machines-windows-tutorial.md).

<!---HONumber=Oct15_HO4-->