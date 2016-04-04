<properties 
   pageTitle="Verwalten von NSGs mit PowerShell im Resource Manager | Microsoft Azure"
   description="Erfahren Sie, wie Sie vorhandene NSGs mit PowerShell im Resource Manager verwalten"
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
   ms.date="03/14/2016"
   ms.author="telmos" />

# Verwalten von NSGs in PowerShell

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Abrufen von Informationen

Sie können Ihre vorhandenen NSGs sehen, Regeln für eine vorhandene NSG abrufen und herausfinden, welchen Ressourcen eine NSG zugeordnet ist.

### Anzeigen vorhandener NSGs
Um alle vorhandenen NSGs in einem Abonnement anzuzeigen, führen Sie das `Get-AzureRmNetworkSecurityGroup`-Cmdlet wie unten dargestellt aus.

	Get-AzureRmNetworkSecurityGroup

Erwartetes Ergebnis:

	Name                 : NSG-BackEnd
	ResourceGroupName    : RG-NSG
	Location             : westus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-BackEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 	                       
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	
	Name                 : NSG-FrontEnd
	ResourceGroupName    : RG-NSG
	Location             : eastus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	                     	
	Name                 : WEB1
	ResourceGroupName    : RG101
	Location             : eastus2
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG101/providers/M
	                       icrosoft.Network/networkSecurityGroups/WEB1
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]


Um die Liste der vorhandenen NSGs in einer bestimmten Ressourcengruppe anzuzeigen, führen Sie das `Get-AzureRmNetworkSecurityGroup`-Cmdlet wie unten dargestellt aus.

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG

Erwartete Ausgabe:

	Name                 : NSG-BackEnd
	ResourceGroupName    : RG-NSG
	Location             : westus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-BackEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 	                       
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
	
	Name                 : NSG-FrontEnd
	ResourceGroupName    : RG-NSG
	Location             : eastus
	Id                   : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/NRP-RG/providers/
	                       Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	Etag                 : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ResourceGuid         : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
	ProvisioningState    : Succeeded
	Tags                 : 
	SecurityRules        : [...]
	DefaultSecurityRules : [...]
	NetworkInterfaces    : [...]
	Subnets              : [...]
		 
### Auflisten aller Regeln für eine NSG

Um die Regeln einer NSG namens **NSG-FrontEnd** anzuzeigen, führen Sie das `Get-AzureRmNetworkSecurityGroup`-Cmdlet wie unten dargestellt aus.

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd | Select SecurityRules -ExpandProperty SecurityRules

Erwartete Ausgabe:
	
	Name                     : rdp-rule
	Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/						   Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp-rule
	Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState        : Succeeded
	Description              : Allow RDP
	Protocol                 : Tcp
	SourcePortRange          : *
	DestinationPortRange     : 3389
	SourceAddressPrefix      : Internet
	DestinationAddressPrefix : *
	Access                   : Allow
	Priority                 : 100
	Direction                : Inbound
	
	Name                     : web-rule
	Id                       : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/						   Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
	Etag                     : W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"
	ProvisioningState        : Succeeded
	Description              : Allow HTTP
	Protocol                 : Tcp
	SourcePortRange          : *
	DestinationPortRange     : 80
	SourceAddressPrefix      : Internet
	DestinationAddressPrefix : *
	Access                   : Allow
	Priority                 : 101
	Direction                : Inbound

>[AZURE.NOTE] Sie können auch `Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name "NSG-FrontEnd" | Select DefaultSecurityRules -ExpandProperty DefaultSecurityRules` verwenden, um die Standardregeln von der **NSG-FrontEnd**-NSG aus aufzulisten.

### Anzeigen von NSG-Zuordnungen

Um anzuzeigen, welchen Ressourcen die **NSG-FrontEnd**-NSG zugeordnet ist, führen Sie das `Get-AzureRmNetworkSecurityGroup`-Cmdlet wie unten dargestellt aus.

	Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd

Suchen Sie, wie unten gezeigt, nach den Eigenschaften **NetworkInterfaces** und **Subnets**:

	NetworkInterfaces    : []
	Subnets              : [
	                         {
	                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
	                           "IpConfigurations": []
	                         }
	                       ]

Im Beispiel oben ist die NSG keinen Netzwerkschnittstellen (NICs) zugeordnet. Sie ist einem Subnetz namens **FrontEnd** zugeordnet.

## Verwalten von Regeln

Sie können einer vorhandenen NSG Regeln hinzufügen, vorhandene Regeln bearbeiten und Regeln entfernen.

### Hinzufügen einer Regel

Um eine Regel hinzuzufügen, die **eingehenden** Verkehr an Port **443** von jedem Computer aus an die **NSG-FrontEnd**-NSG zulässt, führen Sie die folgenden Schritte aus.

1. Führen Sie das `Get-AzureRmNetworkSecurityGroup`-Cmdlet zum Abrufen der vorhandenen NSG aus, und speichern Sie sie in einer Variablen, wie unten dargestellt.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Führen Sie das `Add-AzureRmNetworkSecurityRuleConfig`-Cmdlet wie unten dargestellt aus.

		Add-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule `
		    -Description "Allow HTTPS" `
		    -Access Allow `
		    -Protocol Tcp `
		    -Direction Inbound `
		    -Priority 102 `
		    -SourceAddressPrefix * `
		    -SourcePortRange * `
		    -DestinationAddressPrefix * `
		    -DestinationPortRange 443

3. Um die an der NSG vorgenommenen Änderungen zu speichern, führen Sie das `Set-AzureRmNetworkSecurityGroup`-Cmdlet wie unten dargestellt aus.

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	Die erwartete Ausgabe zeigt nur die Sicherheitsregeln:

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         },
		                         {
		                           "Name": "https-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
		                           "Description": "Allow HTTPS",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "443",
		                           "SourceAddressPrefix": "*",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 102,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]

### Ändern einer Regel

Um die oben erstellte Regel zu ändern, sodass eingehender Verkehr nur aus dem **Internet** zugelassen wird, führen Sie die folgenden Schritte aus.

1. Führen Sie das `Get-AzureRmNetworkSecurityGroup`-Cmdlet zum Abrufen der vorhandenen NSG aus, und speichern Sie sie in einer Variablen, wie unten dargestellt.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Führen Sie das `Set-AzureRmNetworkSecurityRuleConfig`-Cmdlet wie unten dargestellt aus.

		Set-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule `
		    -Description "Allow HTTPS" `
		    -Access Allow `
		    -Protocol Tcp `
		    -Direction Inbound `
		    -Priority 102 `
		    -SourceAddressPrefix * `
		    -SourcePortRange Internet `
		    -DestinationAddressPrefix * `
		    -DestinationPortRange 443

3. Um die an der NSG vorgenommenen Änderungen zu speichern, führen Sie das `Set-AzureRmNetworkSecurityGroup`-Cmdlet wie unten dargestellt aus.

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	Die erwartete Ausgabe zeigt nur die Sicherheitsregeln:

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         },
		                         {
		                           "Name": "https-rule",
		                           "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
		                           "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/https-rule",
		                           "Description": "Allow HTTPS",
		                           "Protocol": "Tcp",
		                           "SourcePortRange": "*",
		                           "DestinationPortRange": "443",
		                           "SourceAddressPrefix": "Internet",
		                           "DestinationAddressPrefix": "*",
		                           "Access": "Allow",
		                           "Priority": 102,
		                           "Direction": "Inbound",
		                           "ProvisioningState": "Succeeded"
		                         }
		                       ]

### Löschen einer Regel

1. Führen Sie das `Get-AzureRmNetworkSecurityGroup`-Cmdlet zum Abrufen der vorhandenen NSG aus, und speichern Sie sie in einer Variablen, wie unten dargestellt.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Führen Sie das `Remove-AzureRmNetworkSecurityRuleConfig`-Cmdlet wie unten dargestellt aus.

		Remove-AzureRmNetworkSecurityRuleConfig -NetworkSecurityGroup $nsg `
		    -Name https-rule

3. Um die an der NSG vorgenommenen Änderungen zu speichern, führen Sie das `Set-AzureRmNetworkSecurityGroup`-Cmdlet wie unten dargestellt aus.

		Set-AzureRmNetworkSecurityGroup -NetworkSecurityGroup $nsg

	Die erwartete Ausgabe zeigt nur die Sicherheitsregeln. Beachten Sie, dass **https-rule** nicht mehr aufgelistet ist:

		Name                 : NSG-FrontEnd
		...
		SecurityRules        : [
		                         {
		                           "Name": "rdp-rule",
		                           ...
		                         },
		                         {
		                           "Name": "web-rule",
		                           ...
		                         }
		                       ]

## Verwalten von Zuordnungen

Sie können eine NSG Subnetzen und NICs zuordnen. Sie können auch die Zuordnung einer NSG zu jeder beliebigen Ressource, der sie zugeordnet ist, aufheben.

### Zuordnen einer NSG zu einer NIC

Um die **NSG-FrontEnd**-NSG der **TestNICWeb1**-NIC zuzuordnen, führen Sie die folgenden Schritte aus.

1. Führen Sie das `Get-AzureRmNetworkSecurityGroup`-Cmdlet zum Abrufen der vorhandenen NSG aus, und speichern Sie sie in einer Variablen, wie unten dargestellt.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Führen Sie das `Get-AzureRmNetworkInterface`-Cmdlet zum Abrufen der vorhandenen NIC aus, und speichern Sie sie in einer Variablen, wie unten dargestellt.

		$nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
		    -Name TestNICWeb1

3. Setzen Sie die Eigenschaft **NetworkSecurityGroup** der Variablen **NIC** auf den Wert der **NSG**-Variablen, wie unten dargestellt.

		$nic.NetworkSecurityGroup = $nsg

4. Um die an der NIC vorgenommenen Änderungen zu speichern, führen Sie das `Set-AzureRmNetworkInterface`-Cmdlet wie unten dargestellt aus.

		Set-AzureRmNetworkInterface -NetworkInterface $nic

	Die erwartete Ausgabe zeigt nur die Eigenschaft **NetworkSecurityGroup**:

		NetworkSecurityGroup : {
		                         "SecurityRules": [],
		                         "DefaultSecurityRules": [],
		                         "NetworkInterfaces": [],
		                         "Subnets": [],
		                         "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                       }

### Aufheben der Zuordnung einer NSG zu einer NIC

Um die Zuordnung der **NSG-FrontEnd**-NSG zur **TestNICWeb1**-NIC aufzuheben, führen Sie die folgenden Schritte aus.

1. Führen Sie das `Get-AzureRmNetworkSecurityGroup`-Cmdlet zum Abrufen der vorhandenen NSG aus, und speichern Sie sie in einer Variablen, wie unten dargestellt.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

2. Führen Sie das `Get-AzureRmNetworkInterface`-Cmdlet zum Abrufen der vorhandenen NIC aus, und speichern Sie sie in einer Variablen, wie unten dargestellt.

		$nic = Get-AzureRmNetworkInterface -ResourceGroupName RG-NSG `
		    -Name TestNICWeb1

3. Setzen Sie die Eigenschaft **NetworkSecurityGroup** der Variablen **NIC** auf **$null**, wie unten dargestellt.

		$nic.NetworkSecurityGroup = $null

4. Um die an der NIC vorgenommenen Änderungen zu speichern, führen Sie das `Set-AzureRmNetworkInterface`-Cmdlet wie unten dargestellt aus.

		Set-AzureRmNetworkInterface -NetworkInterface $nic

	Die erwartete Ausgabe zeigt nur die Eigenschaft **NetworkSecurityGroup**:

		NetworkSecurityGroup : null

### Aufheben der Zuordnung einer NSG zu einem Subnetz

Um die Zuordnung der **NSG-FrontEnd**-NSG zum **FrontEnd**-Subnetz aufzuheben, führen Sie die folgenden Schritte aus.

1. Führen Sie das `Get-AzureRmVirtualNetwork`-Cmdlet zum Abrufen des vorhandenen VNet aus, und speichern Sie es in einer Variablen, wie unten dargestellt.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
		    -Name TestVNet

2. Führen Sie das `Get-AzureRmVirtualNetworkSubnetConfig`-Cmdlet zum Abrufen des **FrontEnd**-Subnetzes aus, und speichern Sie es in einer Variablen, wie unten dargestellt.

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
		    -Name FrontEnd 

3. Setzen Sie die Eigenschaft **NetworkSecurityGroup** der Variablen **subnet** auf **$null**, wie unten dargestellt.

		$subnet.NetworkSecurityGroup = $null

4. Um die am Subnetz vorgenommenen Änderungen zu speichern, führen Sie das `Set-AzureRmVirtualNetwork`-Cmdlet wie unten dargestellt aus.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	Die erwartete Ausgabe zeigt nur die Eigenschaften des **FrontEnd**-Subnetzes. Beachten Sie, dass keine Eigenschaft für **NetworkSecurityGroup** vorhanden ist:

			...
			Subnets           : [
			                      {
			                        "Name": "FrontEnd",
			                        "Etag": "W/"xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx"",
			                        "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd",
			                        "AddressPrefix": "192.168.1.0/24",
			                        "IpConfigurations": [
			                          {
			                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1"
			                          },
			                          {
			                            "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1"
			                          }
			                        ],
			                        "ProvisioningState": "Succeeded"
			                      },
									...
			                    ]

### Zuordnen einer NSG zu einem Subnetz

Um die **NSG-FrontEnd**-NSG erneut dem **FrontEnd**-Subnetz zuzuordnen, führen Sie die folgenden Schritte aus.

1. Führen Sie das `Get-AzureRmVirtualNetwork`-Cmdlet zum Abrufen des vorhandenen VNet aus, und speichern Sie es in einer Variablen, wie unten dargestellt.

		$vnet = Get-AzureRmVirtualNetwork -ResourceGroupName RG-NSG `
		    -Name TestVNet

2. Führen Sie das `Get-AzureRmVirtualNetworkSubnetConfig`-Cmdlet zum Abrufen des **FrontEnd**-Subnetzes aus, und speichern Sie es in einer Variablen, wie unten dargestellt.

		$subnet = Get-AzureRmVirtualNetworkSubnetConfig -VirtualNetwork $vnet `
		    -Name FrontEnd 

1. Führen Sie das `Get-AzureRmNetworkSecurityGroup`-Cmdlet zum Abrufen der vorhandenen NSG aus, und speichern Sie sie in einer Variablen, wie unten dargestellt.

		$nsg = Get-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG `
		    -Name NSG-FrontEnd

3. Setzen Sie die Eigenschaft **NetworkSecurityGroup** der Variablen **subnet** auf **$null**, wie unten dargestellt.

		$subnet.NetworkSecurityGroup = $nsg

4. Um die am Subnetz vorgenommenen Änderungen zu speichern, führen Sie das `Set-AzureRmVirtualNetwork`-Cmdlet wie unten dargestellt aus.

		Set-AzureRmVirtualNetwork -VirtualNetwork $vnet

	Die erwartete Ausgabe zeigt nur die Eigenschaft **NetworkSecurityGroup** des **FrontEnd**-Subnetzes:

		...
		"NetworkSecurityGroup": {
		                          "SecurityRules": [],
		                          "DefaultSecurityRules": [],
		                          "NetworkInterfaces": [],
		                          "Subnets": [],
		                          "Id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd"
		                        }
		...

## Löschen einer NSG

Sie können eine NSG nur löschen, wenn sie keiner Ressource zugeordnet ist. Um eine NSG zu löschen, gehen Sie folgendermaßen vor.

1. Um die Ressourcen, die einer NSG zugeordnet sind, zu überprüfen, führen Sie `azure network nsg show` aus, wie in [Anzeigen von NSG-Zuordnungen](#View-NSGs-associations) gezeigt.
2. Falls die NSG einer NIC zugeordnet ist, führen Sie `azure network nic set`, wie in [Aufheben der Zuordnung einer NSG zu einer NIC](#Dissociate-an-NSG-from-a-NIC) gezeigt, für jede NIC aus. 
3. Falls die NSG einem Subnetz zugeordnet ist, führen Sie `azure network vnet subnet set`, wie in [Aufheben der Zuordnung einer NSG zu einem Subnetz](#Dissociate-an-NSG-from-a-subnet) gezeigt, für jedes Subnetz aus.
4. Um die NSG zu löschen, führen Sie das `Remove-AzureRmNetworkSecurityGroup`-Cmdlet wie unten gezeigt aus.

		Remove-AzureRmNetworkSecurityGroup -ResourceGroupName RG-NSG -Name NSG-FrontEnd -Force

	>[AZURE.NOTE] Der Parameter **-Force** stellt sicher, dass Sie die Löschung nicht bestätigen müssen.

## Nächste Schritte

- [Aktivieren der Protokollierung](virtual-network-nsg-manage-log.md) für NSGs.

<!---HONumber=AcomDC_0323_2016-->