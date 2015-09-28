## Erstellen von VNets mithilfe von PowerShell

Führen Sie zum Erstellen von VNets mithilfe von PowerShell die folgenden Schritte aus.

1. Wenn Sie Azure PowerShell noch nie verwendet haben, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md), und befolgen Sie die komplette Anleitung, um sich bei Azure anzumelden und Ihr Abonnement auszuwählen.
2. Führen Sie an einer Azure PowerShell-Eingabeaufforderung das Cmdlet **Switch-AzureMode** aus, um zum Ressourcen-Manager-Modus zu wechseln, wie unten dargestellt.

		Switch-AzureMode AzureResourceManager
	
	Erwartete Ausgabe:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]Das Cmdlet "Switch-AzureMode" ist demnächst veraltet. In diesem Fall werden alle Ressourcen-Manager-Cmdlets umbenannt.
	
3. Führen Sie bei Bedarf das Cmdlet **New-AzureResourceGroup** aus, um eine neue Ressourcengruppe zu erstellen, wie unten dargestellt. In diesem Szenario erstellen Sie eine Ressourcengruppe mit dem Namen *TestRG*. Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md/#resource-groups).

		New-AzureResourceGroup -Name TestRG -Location centralus

	Erwartete Ausgabe:
	
		ResourceGroupName : TestRG
		Location          : centralus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		                    Actions  NotActions
		                    =======  ==========
		                    *
		
		ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG	

4. Führen Sie das Cmdlet **New-AzureVirtualNetwork** aus, um ein neues VNet zu erstellen, wie unten dargestellt.

		New-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet `
			-AddressPrefix 192.168.0.0/16 -Location centralus	
		
	Erwartete Ausgabe:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"5b89894f-db7f-4634-9870-f9b97e209510"
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

5. Führen Sie das Cmdlet **Get-AzureVirtualNetwork** aus, um das virtuelle Netzwerkobjekt in einer Variablen zu speichern, wie unten dargestellt.

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
	
	>[AZURE.TIP]Sie können die Schritte 4 und 5 durch Ausführen von **$vnet = New-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet -AddressPrefix 192.168.0.0/16 -Location centralus** zusammenfassen.

6. Führen Sie das Cmdlet **Add-AzureVirtualNetworkSubnetConfig** aus, um dem neuen VNet ein Subnetz hinzuzufügen, wie unten dargestellt.

		Add-AzureVirtualNetworkSubnetConfig -Name FrontEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.1.0/24
		
	Erwartete Ausgabe:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"5b89894f-db7f-4634-9870-f9b97e209510"
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

		Add-AzureVirtualNetworkSubnetConfig -Name BackEnd `
			-VirtualNetwork $vnet -AddressPrefix 192.168.2.0/24

8. Obwohl Sie Subnetze erstellen, sind diese derzeit nur in der lokalen Variablen enthalten, mit der das in Schritt 4 erstellte VNet abgerufen wird. Führen Sie zum Speichern der Änderungen in Azure das Cmdlet **Set-AzureVirtualNetwork** aus, wie unten dargestellt.

		Set-AzureVirtualNetwork -VirtualNetwork $vnet	
		
	Erwartete Ausgabe:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : centralus
		Id                : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"2d3496d8-2b85-4238-bde2-377fe660aa4a"
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
		                        "Etag": "W/"2d3496d8-2b85-4238-bde2-377fe660aa4a"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      },
		                      {
		                        "Name": "BackEnd",
		                        "Etag": "W/"2d3496d8-2b85-4238-bde2-377fe660aa4a"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
		                        "AddressPrefix": "192.168.2.0/24",
		                        "IpConfigurations": [],
		                        "NetworkSecurityGroup": null,
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]

<!---HONumber=Sept15_HO3-->