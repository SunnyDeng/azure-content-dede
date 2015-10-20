## Erstellen von VNets mithilfe von PowerShell

Führen Sie zum Erstellen von VNets mithilfe von PowerShell die folgenden Schritte aus.

1. Wenn Sie Azure PowerShell noch nie verwendet haben, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md), und befolgen Sie die komplette Anleitung, um sich bei Azure anzumelden und Ihr Abonnement auszuwählen.
	
3. Führen Sie bei Bedarf das Cmdlet **New-AzureRMResourceGroup** aus, um eine neue Ressourcengruppe zu erstellen, wie unten dargestellt. In diesem Szenario erstellen Sie eine Ressourcengruppe mit dem Namen *TestRG*. Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md/#resource-groups).

		New-AzureRMResourceGroup -Name TestRG -Location centralus

	Erwartete Ausgabe:
	
		ResourceGroupName : TestRG
		Location          : centralus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		                    Actions  NotActions
		                    =======  ==========
		                    *
		
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG	

4. Führen Sie das Cmdlet **New-AzureRMVirtualNetwork** aus, um ein neues VNet zu erstellen, wie unten dargestellt.

		New-AzureRMVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
			-AddressPrefix 192.168.0.0/16 -Location centralus	
		
	Erwartete Ausgabe:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": null
		                    }
		NetworkInterfaces : null
		Subnets           : []

5. Führen Sie das Cmdlet **Get-AzureRMVirtualNetwork** aus, um das virtuelle Netzwerkobjekt in einer Variablen zu speichern, wie unten dargestellt.

		$vnet = Get-AzureRMVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
	
	>[AZURE.TIP]Sie können die Schritte 4 und 5 durch Ausführen von **$vnet = New-AzureRMVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus** zusammenfassen.

6. Führen Sie das Cmdlet **Add-AzureRMVirtualNetworkSubnetConfig** aus, um dem neuen VNet ein Subnetz hinzuzufügen, wie unten dargestellt.

		Add-AzureRMVirtualNetworkSubnetConfig -Name FrontEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
		
	Erwartete Ausgabe:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": null
		                    }
		NetworkInterfaces : null
		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": null,
		                        "Id": null,
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": null,
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": null
		                      }
		                    ]

7. Wiederholen Sie Schritt 6 für jedes Subnetz, das Sie erstellen möchten. Der folgende Befehl erstellt das *BackEnd*-Subnetz für dieses Szenario.

		Add-AzureRMVirtualNetworkSubnetConfig -Name BackEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24

8. Obwohl Sie Subnetze erstellen, sind diese derzeit nur in der lokalen Variablen enthalten, mit der das in Schritt 4 erstellte VNet abgerufen wird. Führen Sie zum Speichern der Änderungen in Azure das Cmdlet **Set-AzureRMVirtualNetwork** aus, wie unten dargestellt.

		Set-AzureRMVirtualNetwork -VirtualNetwork $vnet	
		
	Erwartete Ausgabe:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState : Succeeded
		Tags              :
		AddressSpace      : {
		                      "AddressPrefixes": [
		                        "192.168.0.0/16"
		                      ]
		                    }
		DhcpOptions       : {
		                      "DnsServers": []
		                    }
		NetworkInterfaces : null
		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      },
		                      {
		                        "Name": "BackEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
		                        "AddressPrefix": "192.168.2.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]

<!---HONumber=Oct15_HO3-->