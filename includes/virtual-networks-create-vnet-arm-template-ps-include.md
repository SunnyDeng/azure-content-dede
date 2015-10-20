## Bereitstellen der ARM-Vorlage mithilfe von PowerShell

Führen Sie zum Bereitstellen der mithilfe von PowerShell heruntergeladenen ARM-Vorlage die folgenden Schritte aus.

1. Wenn Sie Azure PowerShell noch nie verwendet haben, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md), und befolgen Sie die komplette Anleitung, um sich bei Azure anzumelden und Ihr Abonnement auszuwählen.

3. Führen Sie bei Bedarf das Cmdlet **New-AzureRMResourceGroup** aus, um eine neue Ressourcengruppe zu erstellen. Mit dem folgenden Befehl wird eine Ressourcengruppe mit dem Namen *TestRG* in der Azure-Region *USA, Mitte* erstellt. Weitere Informationen zu Ressourcengruppen finden Sie unter [Übersicht über den Azure-Ressourcen-Manager.](resource-group-overview.md)

		New-AzureRMResourceGroup -Name TestRG -Location centralus
		
	Hier ist die erwartete Ausgabe des obigen Befehls:

		ResourceGroupName : TestRG
		Location          : centralus
		ProvisioningState : Succeeded
		Tags              :
		Permissions       :
		                    Actions  NotActions
		                    =======  ==========
		                    *
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

4. Führen Sie das Cmdlet **New-AzureRMResourceGroupDeployment** aus, um das neue VNet mithilfe der zuvor heruntergeladenen und geänderten Vorlage und Parameterdateien bereitzustellen.

		New-AzureRMResourceGroupDeployment -Name TestVNetDeployment -ResourceGroupName TestRG `
			-TemplateFile C:\ARM\azuredeploy.json -TemplateParameterFile C:\ARM\azuredeploy-parameters.json
			
	Hier ist die erwartete Ausgabe des obigen Befehls:
		
		DeploymentName    : TestVNetDeployment
		ResourceGroupName : TestRG
		ProvisioningState : Succeeded
		Timestamp         : 8/14/2015 9:40:00 PM
		Mode              : Incremental
		TemplateLink      :
		Parameters        :
		                    Name             Type                       Value
		                    ===============  =========================  ==========
		                    location         String                     Central US
		                    vnetName         String                     TestVNet
		                    addressPrefix    String                     192.168.0.0/16
		                    subnet1Prefix    String                     192.168.1.0/24
		                    subnet1Name      String                     FrontEnd
		                    subnet2Prefix    String                     192.168.2.0/24
		                    subnet2Name      String                     BackEnd
		
		Outputs           :

5. Führen Sie das Cmdlet **Get-AzureRMVirtualNetwork** aus, um die Eigenschaften des neuen VNet anzuzeigen, wie unten dargestellt.


		Get-AzureRMVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
		
	Hier ist die erwartete Ausgabe des obigen Befehls:
		
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