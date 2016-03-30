### Hinzufügen oder Entfernen von Präfixen, wenn Sie noch keine VPN Gateway-Verbindung erstellt haben

- Verwenden Sie das unten angegebene Beispiel, um zusätzliche Adresspräfixe einem lokalen Netzwerkgateway **hinzuzufügen**, das Sie erstellt haben, aber das noch nicht über eine Verbindung per VPN Gateway verfügt.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')


- Verwenden Sie das unten angegebene Beispiel, um ein Adresspräfix aus einem lokalen Netzwerkgateway **zu entfernen**, das noch nicht über eine VPN-Verbindung verfügt. Lassen Sie die Präfixe weg, die Sie nicht mehr benötigen. In diesem Beispiel wird das Präfix 20.0.0.0/24 (aus dem vorherigen Beispiel) nicht mehr benötigt. Daher wird das lokale Netzwerkgateway aktualisiert und das Präfix weggelassen.

		$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
		Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','30.0.0.0/24')

### Hinzufügen oder Entfernen von Präfixen, wenn Sie bereits eine VPN Gateway-Verbindung erstellt haben

Wenn Sie die VPN-Verbindung erstellt haben und die IP-Adresspräfixe Ihres lokalen Netzwerkgateways hinzufügen oder entfernen möchten, müssen Sie die folgenden Schritte der Reihenfolge nach ausführen. Dies führt zu Ausfallzeiten für Ihre VPN-Verbindung, da Sie das Gateway entfernen und neu erstellen müssen. Da Sie aber eine IP-Adresse für die Verbindung angefordert haben, müssen Sie Ihren lokalen VPN-Router nicht neu konfigurieren. Dies ist nur erforderlich, wenn Sie sich dafür entscheiden, die zuvor verwendeten Werte zu ändern.
 
1. Entfernen Sie die Gatewayverbindung. 
2. Ändern Sie die Präfixe für Ihr lokales Netzwerkgateway. 
3. Erstellen Sie eine neue Gatewayverbindung. 

Sie können das folgende Beispiel als Hilfe verwenden.

	$gateway1 = Get-AzureRmVirtualNetworkGateway -Name vnetgw1 -ResourceGroupName testrg
	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg

	Remove-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg

	$local = Get-AzureRmLocalNetworkGateway -Name LocalSite -ResourceGroupName testrg
	Set-AzureRmLocalNetworkGateway -LocalNetworkGateway $local -AddressPrefix @('10.0.0.0/24','20.0.0.0/24','30.0.0.0/24')
	
	New-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Location 'West US' -VirtualNetworkGateway1 $gateway1 -LocalNetworkGateway2 $local -ConnectionType IPsec -RoutingWeight 10 -SharedKey 'abc123'

<!---HONumber=AcomDC_0323_2016-->