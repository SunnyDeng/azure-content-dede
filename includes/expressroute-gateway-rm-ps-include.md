## Konfigurationsübersicht

Für die Durchführung der Schritte für diese Aufgabe benötigen Sie ein VNet mit den folgenden Eigenschaften. Zusätzliche Einstellungen und Namen werden ebenfalls in dieser Liste beschrieben. Wir verwenden diese Liste nicht direkt in einem der Schritte, obwohl wir Variablen basierend auf den Werten in dieser Liste hinzufügen. Sie können diese Liste als Referenz verwenden und die Werte durch Ihre eigenen Werte ersetzen.

Konfiguration der Referenzliste:
	
- Name des virtuellen Netzwerks = TestVNet
- Adressraum des virtuellen Netzwerks: 192.168.0.0/16
- Ressourcengruppe = TestRG
- Name Subnet1 = FrontEnd 
- Adressraum Subnet1 = 192.168.0.0/16
- Name des Gatewaysubnetzes: GatewaySubnet. Sie müssen ein Gatewaysubnetz immer *GatewaySubnet* nennen.
- Adressraum des Gatewaysubnetzes = 192.168.200.0/26
- Region = USA, Osten
- Name des Gateways = GW
- Name der Gateway-IP = GWIP
- Name der Gateway-IP-Konfiguration = gwipconf
- VPN-Typ = ExpressRoute. Dieser VPN-Typ ist für eine ExpressRoute-Konfiguration erforderlich.
- Name der öffentlichen Gateway-IP = gwpip


## Hinzufügen eines Gateways

1. Verbinden Sie sich mit Ihrem Azure-Abonnement. 

		Login-AzureRmAccount
		Get-AzureRmSubscription 
		Select-AzureRmSubscription -SubscriptionName "Name of subscription"

2. Definieren Sie Ihre Variablen für diese Übung. Für dieses Beispiel werden die Variablen im nachstehenden Beispiel verwendet. Achten Sie darauf, dies so zu bearbeiten, dass es den Einstellungen entspricht, die Sie verwenden möchten.
		
		$RG = "TestRG"
		$Location = "East US"
		$GWName = "GW"
		$GWIPName = "GWIP"
		$GWIPconfName = "gwipconf"
		$VNetName = "TestVNet"

3. Speichern Sie das virtuelle Netzwerkobjekt als Variable.

		$vnet = Get-AzureRmVirtualNetwork -Name $VNetName -ResourceGroupName $RG

4. Fügen Sie Ihrem virtuellen Netzwerk ein Gatewaysubnetz hinzu. Das Gatewaysubnetz muss den Namen GatewaySubnet tragen. Sie möchten ein Gateway erstellen, das /27 oder größer (/26, /25, etc.) ist.
			
		Add-AzureRmVirtualNetworkSubnetConfig -Name GatewaySubnet -VirtualNetwork $vnet -AddressPrefix 192.168.200.0/26

5. Legen Sie die Konfiguration fest.

			Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

6. Speichern Sie das Gatewaysubnetz als Variable.

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -Name 'GatewaySubnet' -VirtualNetwork $vnet

7. Fordern Sie eine öffentliche IP-Adresse an. Die IP-Adresse wird angefordert, bevor das Gateway erstellt wird. Sie können die IP-Adresse, die Sie verwenden möchten, nicht selbst angeben. Sie wird dynamisch zugewiesen. Diese IP-Adresse wird im nächsten Konfigurationsabschnitt verwendet. Die AllocationMethod muss dynamisch sein.

		$pip = New-AzureRmPublicIpAddress -Name gwpip -ResourceGroupName $RG -Location $Location -AllocationMethod Dynamic
		$ipconf = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -Subnet $subnet -PublicIpAddress $pip

8. Erstellen Sie die Konfiguration für Ihr Gateway. Die Gatewaykonfiguration definiert das zu verwendende Subnetz und die zu verwendende öffentliche IP-Adresse. In diesem Schritt geben Sie die Konfiguration an, die beim Erstellen des Gateways verwendet wird. Dieser Schritt erstellt das Gatewayobjekt nicht tatsächlich. Verwenden Sie das folgende Beispiel, um Ihre Gatewaykonfiguration zu erstellen.

		$gwipconfig = New-AzureRmVirtualNetworkGatewayIpConfig -Name $GWIPconfName -SubnetId $subnet.Id -PublicIpAddressId $pip.Id 

9. Erstellen Sie das Gateway. In diesem Schritt ist der **GatewayType** besonders wichtig. Sie müssen den Wert **ExpressRoute** verwenden. Beachten Sie, dass das Erstellen des Gateways nach der Ausführung dieser Cmdlets 20 Minuten oder länger dauern kann.

		New-AzureRmVirtualNetworkGateway -Name $GWName -ResourceGroupName $RG -Location $Location -IpConfigurations $ipconf -GatewayType Expressroute

<!---HONumber=AcomDC_0309_2016-->