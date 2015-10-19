<properties 
   pageTitle="Erstellen von NSGs im ARM-Modus mithilfe von PowerShell | Microsoft Azure"
   description="Hier erhalten Sie Informationen zum Erstellen und Bereitstellen von NSGs im ARM-Modus mit PowerShell."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor="tysonn"
   tags="azure-resource-manager"
/>
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2015"
   ms.author="telmos" />

# Erstellen von NSGs in PowerShell

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das Ressourcen-Manager-Bereitstellungsmodell. Sie haben auch die Möglichkeit, [NSGs im klassischen Bereitstellungsmodell zu erstellen](virtual-networks-create-nsg-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Die folgenden Beispielbefehle für PowerShell setzen voraus, dass bereits eine einfache Umgebung erstellt wurde, die auf dem zuvor beschriebenen Szenario basiert. Wenn Sie die Befehle so ausführen möchten, wie sie in diesem Dokument angezeigt werden, erstellen Sie zunächst die Testumgebung durch Bereitstellen [dieser Vorlage](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd). Klicken Sie auf **In Azure bereitstellen**, ersetzen Sie bei Bedarf die Standardparameterwerte, und befolgen Sie dann die Anweisungen im Portal.

## Erstellen der NSG für das Front-End-Subnetz
Führen Sie zum Erstellen einer NSG mit dem Namen *NSG-FrontEnd* basierend auf dem oben beschriebenen Szenario die folgenden Schritte aus:

1. Wenn Sie Azure PowerShell zuvor noch nicht verwendet haben, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md), und befolgen Sie die komplette Anleitung, um sich bei Azure anzumelden und Ihr Abonnement auszuwählen.
2. Führen Sie an einer Azure PowerShell-Eingabeaufforderung das Cmdlet **Switch-AzureMode** aus, um zum Ressourcen-Manager-Modus zu wechseln, wie unten dargestellt.

		Switch-AzureMode AzureResourceManager
	
	Erwartete Ausgabe:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]Das Cmdlet "Switch-AzureMode" ist demnächst veraltet. In diesem Fall werden alle Ressourcen-Manager-Cmdlets umbenannt.

3. Erstellen Sie eine Sicherheitsregel, die den Zugriff über das Internet auf Port 3389 zulässt.

		$rule1 = New-AzureNetworkSecurityRuleConfig -Name rdp-rule -Description "Allow RDP" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
		    -SourceAddressPrefix Internet -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 3389

4. Erstellen Sie eine Sicherheitsregel, die den Zugriff über das Internet auf Port 80 zulässt.

		$rule2 = New-AzureNetworkSecurityRuleConfig -Name web-rule -Description "Allow HTTP" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 101 `
		    -SourceAddressPrefix Internet -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 80

5. Fügen Sie die oben erstellten Regeln zu einer neuen NSG mit dem Namen **NSG-FrontEnd** hinzu.

		New-AzureNetworkSecurityGroup -ResourceGroupName TestRG -Location westus -Name "NSG-FrontEnd" `
			-SecurityRules $rule1,$rule2

	Erwartete Ausgabe (Standardregeln werden nicht angezeigt):

		Name                 : NSG-FrontEnd
		ResourceGroupName    : TestRG
		Location             : westus
		Id                   : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroup
		                       s/NSG-FrontEnd
		Etag                 : W/"6e0d0b50-5b92-4a21-9517-aa7ee8d3b398"
		ProvisioningState    : Succeeded
		Tags                 : 
		SecurityRules        : [	
		                         {
		                           "Name": "rdp-rule",
		                           "Etag": "W/"6e0d0b50-5b92-4a21-9517-aa7ee8d3b398"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSe
		                       curityGroups/NSG-FrontEnd/securityRules/rdp-rule",
		                           "Description": "Allow RDP",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "3389",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 100,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         },
		                         {
		                           "Name": "web-rule",
		                           "Etag": "W/"6e0d0b50-5b92-4a21-9517-aa7ee8d3b398"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSe
		                       curityGroups/NSG-FrontEnd/securityRules/web-rule",
		                           "Description": "Allow HTTP",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "80",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 101,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]
		DefaultSecurityRules : [ ... ]
		NetworkInterfaces    : []
		Subnets              : []		

6. Ordnen Sie die oben erstellte NSG dem Subnetz *FrontEnd* zu.

		$vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet
		Set-AzureVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name FrontEnd `
			-AddressPrefix 192.168.1.0/24 -NetworkSecurityGroup $nsg

	Die erwartete Ausgabe zeigt nur die Einstellungen für das Subnetz *FrontEnd*. Beachten Sie den Wert für die **NetworkSecurityGroup** Eigenschaft:

		Subnets           : [
		                      {
		                        "Name": "FrontEnd",
		                        "Etag": "W/"42bc8afe-f843-4a09-9c5f-d294c6338a4f"",
		                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
		                        "AddressPrefix": "192.168.1.0/24",
		                        "IpConfigurations": [
		                          {
		                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
		                          },
		                          {
		                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
		                          }
		                        ],
		                        "NetworkSecurityGroup": {
		                          "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        },
		                        "RouteTable": null,
		                        "ProvisioningState": "Succeeded"
		                      }

## Erstellen der NSG für das Back-End-Subnetz
Führen Sie zum Erstellen einer NSG mit dem Namen *NSG-BackEnd* basierend auf dem oben beschriebenen Szenario die folgenden Schritte aus.

1. Erstellen Sie eine Sicherheitsregel, die den Zugriff aus dem Front-End-Subnetz auf Port 1433 zulässt (von SQL Server verwendeter Standardport).

		$rule1 = New-AzureNetworkSecurityRuleConfig -Name frontend-rule -Description "Allow FE subnet" `
		    -Access Allow -Protocol Tcp -Direction Inbound -Priority 100 `
		    -SourceAddressPrefix 192.168.1.0/24 -SourcePortRange * `
		    -DestinationAddressPrefix * -DestinationPortRange 1433

4. Erstellen Sie eine Sicherheitsregel, die den Zugriff auf das Internet blockiert.

		$rule2 = New-AzureNetworkSecurityRuleConfig -Name web-rule -Description "Block Internet" `
		    -Access Deny -Protocol * -Direction Outbound -Priority 200 `
		    -SourceAddressPrefix * -SourcePortRange * `
		    -DestinationAddressPrefix Internet -DestinationPortRange *

5. Fügen Sie die oben erstellten Regeln zu einer neuen NSG mit dem Namen **NSG-BackEnd** hinzu.

		New-AzureNetworkSecurityGroup -ResourceGroupName TestRG -Location westus `-Name "NSG-BackEnd" `
			-SecurityRules $rule1,$rule2

	Erwartete Ausgabe (Standardregeln werden nicht angezeigt):

		Name                 : NSG-BackEnd
		ResourceGroupName    : TestRG
		Location             : westus
		Id                   : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroup
		                       s/NSG-BackEnd
		Etag                 : W/"3ca4eb9f-03a7-4e72-be25-6c066c0d8928"
		ProvisioningState    : Succeeded
		Tags                 : 
		SecurityRules        : [
		                         {
		                           "Name": "frontend-rule",
		                           "Etag": "W/"3ca4eb9f-03a7-4e72-be25-6c066c0d8928"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/frontend-rule",
		                           "Description": "Allow FE subnet",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "1433",
		                           "SourceAddressPrefix": "192.168.1.0/24",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 100,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         },
		                         {
		                           "Name": "web-rule",
		                           "Etag": "W/"3ca4eb9f-03a7-4e72-be25-6c066c0d8928"",
		                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-BackEnd/securityRules/web-rule",
		                           "Description": "Block Internet",
		                           "Protocol": "*",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "*",
		                           "SourceAddressPrefix": "*",
		                           "DestinationAddressPrefix": "Internet",
		                           "Access": "Deny",
		                           "Priority": 200,
		                           "Direction": "Outbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]
		DefaultSecurityRules : [...]
		NetworkInterfaces    : []
		Subnets              : []		

6. Ordnen Sie die oben erstellte NSG dem Subnetz *BackEnd* zu.

		Set-AzureVirtualNetworkSubnetConfig -VirtualNetwork $vnet -Name BackEnd `
			-AddressPrefix 192.168.2.0/24 -NetworkSecurityGroup $nsg

	Die erwartete Ausgabe zeigt nur die Einstellungen für das Subnetz *BackEnd*. Beachten Sie den Wert für die **NetworkSecurityGroup** Eigenschaft:

		Subnets           : [
                      {
                        "Name": "BackEnd",
                        "Etag": "W/"42bc8afe-f843-4a09-9c5f-d294c6338a4f"",
                        "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/BackEnd",
                        "AddressPrefix": "192.168.2.0/24",
                        "IpConfigurations": [
                          {
                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL1/ipConfigurations/ipconfig1"
                          },
                          {
                            "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL2/ipConfigurations/ipconfig1"
                          }
                        ],
                        "NetworkSecurityGroup": {
                          "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
                        },
                        "RouteTable": null,
                        "ProvisioningState": "Succeeded"
                      }

<!---HONumber=Oct15_HO2-->