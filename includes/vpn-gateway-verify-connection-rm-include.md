### So überprüfen Sie Ihre Verbindung mithilfe des Azure-Portals

Sie können eine VPN-Verbindung im Azure-Portal überprüfen, indem Sie zu **Virtuelle Netzwerkgateways** navigieren, ***auf den Gatewaynamen klicken*** und dann **Einstellungen** und schließlich **Verbindungen** auswählen. Wenn Sie den Namen der Verbindung auswählen, können Sie weitere Informationen auf dem Blatt **Verbindung** anzeigen.

### So überprüfen Sie Ihre Verbindung mithilfe der PowerShell

Sie können auch überprüfen, ob Ihre Verbindung erfolgreich war, indem Sie *Get-AzureRmVirtualNetworkGatewayConnection –Debug* verwenden. In nächster Zukunft stellen wir hierfür ein Cmdlet bereit. Sie können das folgende Cmdlet-Beispiel verwenden und die Werte so konfigurieren, dass sie Ihren eigenen Werten entsprechen. Wählen Sie bei Aufforderung die Option *A* für „Alle ausführen“ aus.

	Get-AzureRmVirtualNetworkGatewayConnection -Name localtovon -ResourceGroupName testrg -Debug

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

<!---HONumber=AcomDC_0107_2016-->