<properties 
   pageTitle="Steuern des Routings und Verwenden virtueller Geräte im Resource Manager mithilfe von PowerShell | Microsoft Azure"
   description="Erfahren Sie, wie Sie im Resource Manager mithilfe von PowerShell das Routing steuern und virtuelle Geräte verwenden."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2016"
   ms.author="telmos" />

#Erstellen von benutzerdefinierten Routen (UDR) im Resource Manager mit PowerShell

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das Ressourcen-Manager-Bereitstellungsmodell. Sie haben auch die Möglichkeit, [benutzerdefinierte Routen im klassischen Bereitstellungsmodell zu erstellen](virtual-network-create-udr-classic-ps.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Die folgenden Beispielbefehle für PowerShell setzen voraus, dass bereits eine einfache Umgebung erstellt wurde, die auf dem zuvor beschriebenen Szenario basiert. Wenn Sie die Befehle so ausführen möchten, wie sie in diesem Dokument angezeigt werden, erstellen Sie zunächst die Testumgebung durch Bereitstellen [dieser Vorlage](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before). Klicken Sie auf **In Azure bereitstellen**, ersetzen Sie bei Bedarf die Standardparameterwerte, und befolgen Sie dann die Anweisungen im Portal.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Erstellen der benutzerdefinierten Route für das Front-End-Subnetz
Führen Sie zum Erstellen der Routingtabelle und der für das Front-End-Subnetz erforderlichen Route anhand des oben beschriebenen Szenarios die folgenden Schritte aus.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

3. Erstellen Sie eine Route, um den gesamten an das Back-End-Subnetz (192.168.2.0/24) gerichteten Datenverkehr an das virtuelle Gerät **FW1** (192.168.0.4) umzuleiten.

		$route = New-AzureRmRouteConfig -Name RouteToBackEnd `
		    -AddressPrefix 192.168.2.0/24 -NextHopType VirtualAppliance `
		    -NextHopIpAddress 192.168.0.4

4. Erstellen Sie in der Region **USA, Westen** eine Routingtabelle mit dem Namen **UDR-FrontEnd**, die die oben erstellte Route enthält.

		$routeTable = New-AzureRmRouteTable -ResourceGroupName TestRG -Location westus `
		    -Name UDR-FrontEnd -Route $route

5. Erstellen Sie eine Variable, die das VNet mit dem Subnetz enthält. In diesem Szenario hat das VNET den Namen **TestVNet**.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName TestRG -Name TestVNet

6. Ordnen Sie die oben erstellte Routingtabelle dem Subnetz **FrontEnd** zu.
		
		Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
			-AddressPrefix 192.168.1.0/24 -RouteTable $routeTable

>[AZURE.WARNING] Die Ausgabe des obigen Befehls zeigt den Inhalt des Konfigurationsobjekts des virtuellen Netzwerks an, das nur auf dem Computer vorhanden ist, auf dem PowerShell ausgeführt wird. Zum Speichern der Änderungen in Azure müssen Sie das Cmdlet **Set-AzureVirtualNetwork** ausführen.

7. Speichern Sie die neue Subnetzkonfiguration in Azure.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	Erwartete Ausgabe:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : westus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState : Succeeded
		Tags              : 
		                    Name         Value
		                    ===========  =====
		                    displayName  VNet 
		                    
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
								...,
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [
		                          {
		                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConfigurations/ipconfig1"
		                          },
		                          {
		                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConfigurations/ipconfig1"
		                          }
		                        ],
		                        "NetworkSecurityGroup": {
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        },
		                        "RouteTable": {
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-FrontEnd"
		                        },
		                        "ProvisioningState": "Succeeded"
		                      },
								...
		                    ]	

## Erstellen der benutzerdefinierten Route für das Back-End-Subnetz
Führen Sie zum Erstellen der Routingtabelle und der für das Back-End-Subnetz erforderlichen Route anhand des oben beschriebenen Szenarios die folgenden Schritte aus.

1. Erstellen Sie eine Route, um den gesamten an das Front-End-Subnetz (192.168.1.0/24) gerichteten Datenverkehr an das virtuelle Gerät **FW1** (192.168.0.4) umzuleiten.

		$route = New-AzureRmRouteConfig -Name RouteToFrontEnd `
		    -AddressPrefix 192.168.1.0/24 -NextHopType VirtualAppliance `
		    -NextHopIpAddress 192.168.0.4

4. Erstellen Sie in der Region **USA, Westen** eine Routingtabelle mit dem Namen **UDR-BackEnd**, die die oben erstellte Route enthält.

		$routeTable = New-AzureRmRouteTable -ResourceGroupName TestRG -Location westus `
		    -Name UDR-BackEnd -Route $route

5. Ordnen Sie die oben erstellte Routingtabelle dem Subnetz **BackEnd** zu.

		Set-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd `
			-AddressPrefix 192.168.2.0/24 -RouteTable $routeTable

7. Speichern Sie die neue Subnetzkonfiguration in Azure.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	Erwartete Ausgabe:

		Name              : TestVNet
		ResourceGroupName : TestRG
		Location          : westus
		Id                : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
		Etag              : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState : Succeeded
		Tags              : 
		                    Name         Value
		                    ===========  =====
		                    displayName  VNet 
		                    
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
		                      ...,
		                      {
		                        "Name": "BackEnd",
		                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
		                        "AddressPrefix": "192.168.2.0/24",
		                        "IpConfigurations": [
		                          {
		                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL2/ipConfigurations/ipconfig1"
		                          },
		                          {
		                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICSQL1/ipConfigurations/ipconfig1"
		                          }
		                        ],
		                        "NetworkSecurityGroup": {
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BacEnd"
		                        },
		                        "RouteTable": {
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/routeTables/UDR-BackEnd"
		                        },
		                        "ProvisioningState": "Succeeded"
		                      }
		                    ]

## Aktivieren der IP-Weiterleitung auf "FW1"
Führen Sie zum Aktivieren der IP-Weiterleitung in der von **FW1** verwendeten Netzwerkkarte die folgenden Schritte aus.

1. Erstellen Sie eine Variable mit den Einstellungen für die von "FW1" verwendete Netzwerkkarte. In diesem Szenario hat die Netzwerkkarte die Bezeichnung **NICFW1**.

		$nicfw1 = Get-AzureRmNetworkInterface -ResourceGroupName TestRG -Name NICFW1

2. Aktivieren Sie IP-Weiterleitung, und speichern Sie die Netzwerkkarteneinstellungen.

		$nicfw1.EnableIPForwarding = 1
		Set-AzureRmNetworkInterface -NetworkInterface $nicfw1

	Erwartete Ausgabe:

		Name                 : NICFW1
		ResourceGroupName    : TestRG
		Location             : westus
		Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1
		Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
		ProvisioningState    : Succeeded
		Tags                 : 
		                       Name         Value                  
		                       ===========  =======================
		                       displayName  NetworkInterfaces - DMZ
		                       
		VirtualMachine       : {
		                         "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/FW1"
		                       }
		IpConfigurations     : [
		                         {
		                           "Name": "ipconfig1",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICFW1/ipConfigurations/ipconfig1",
		                           "PrivateIpAddress": "192.168.0.4",
		                           "PrivateIpAllocationMethod": "Static",
		                           "Subnet": {
		                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/DMZ"
		                           },
		                           "PublicIpAddress": {
		                             "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/PIPFW1"
		                           },
		                           "LoadBalancerBackendAddressPools": [],
		                           "LoadBalancerInboundNatRules": [],
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]
		DnsSettings          : {
		                         "DnsServers": [],
		                         "AppliedDnsServers": [],
		                         "InternalDnsNameLabel": null,
		                         "InternalFqdn": null
		                       }
		EnableIPForwarding   : True
		NetworkSecurityGroup : null
		Primary              : True

<!---HONumber=AcomDC_0224_2016-->