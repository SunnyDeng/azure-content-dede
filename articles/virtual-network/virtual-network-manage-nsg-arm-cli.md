<properties 
   pageTitle="Verwalten der NSGs mithilfe der Azure-Befehlszeilenschnittstelle in Resource Manager | Microsoft Azure"
   description="Erfahren Sie, wie Sie vorhandene NSGs mithilfe der Azure-Befehlszeilenschnittstelle in Resource Manager verwalten"
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

# Verwalten von NSGs mithilfe der Azure-Befehlszeilenschnittstelle

[AZURE.INCLUDE [virtual-network-manage-arm-selectors-include.md](../../includes/virtual-network-manage-nsg-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-manage-nsg-intro-include.md](../../includes/virtual-network-manage-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.

[AZURE.INCLUDE [virtual-network-manage-nsg-arm-scenario-include.md](../../includes/virtual-network-manage-nsg-arm-scenario-include.md)]

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Abrufen von Informationen

Sie können Ihre vorhandenen NSGs anzeigen lassen, Regeln für eine vorhandene NSG abrufen und herausfinden, welchen Ressourcen eine NSG zugeordnet ist.

### Anzeigen lassen vorhandener NSGs

Um sich die Liste der vorhandenen NSGs in einer bestimmten Ressourcengruppe anzeigen zu lassen, führen Sie den Befehl `azure network nsg list`, wie unten gezeigt, aus.

	azure network nsg list --resource-group RG-NSG

Erwartete Ausgabe:

	info:    Executing command network nsg list
	+ Getting the network security groups
	data:    Name          Location
	data:    ------------  --------
	data:    NSG-BackEnd   westus
	data:    NSG-FrontEnd  westus
	info:    network nsg list command OK
		 
### Auflisten aller Regeln für eine NSG

Um sich die Regeln für eine NSG namens **NSG-FrontEnd** anzeigen zu lassen, führen Sie den Befehl `azure network nsg show`, wie unten gezeigt, aus.

	azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd

Erwartete Ausgabe:
	
	info:    Executing command network nsg show
	+ Looking up the network security group "NSG-FrontEnd"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	data:    Name                            : NSG-FrontEnd
	data:    Type                            : Microsoft.Network/networkSecurityGroups
	data:    Location                        : westus
	data:    Provisioning state              : Succeeded
	data:    Tags                            : displayName=NSG - Front End
	data:    Security group rules:
	data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
	data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
	data:    rdp-rule                       Internet           *            *               3389              Tcp       Inbound    Allow   100
	data:    web-rule                       Internet           *            *               80                Tcp       Inbound    Allow   101
	data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000
	data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001
	data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500
	data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000
	data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001
	data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500
	info:    network nsg show command OK

>[AZURE.NOTE] Sie können auch `azure network nsg rule list --resource-group RG-NSG --nsg-name NSG-FrontEnd` verwenden, um die Regeln von der **NSG-FrontEnd** -NSG aufzulisten.

### Anzeigen lassen von NSG-Zuordnungen

Um sich anzeigen zu lassen, welchen Ressourcen die **NSG-FrontEnd**-NSG zugeordnet ist, führen Sie den Befehl `azure network nsg show`, wie unten gezeigt, aus. Beachten Sie, dass der einzige Unterschied in der Verwendung des **--json**-Parameters liegt.

	azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd --json

Suchen Sie, wie unten gezeigt, nach den Eigenschaften **networkInterfaces** und **subnets**:

	"networkInterfaces": [],
	...
	"subnets": [
		{
			"id": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
		}
	],
	...

Im Beispiel oben ist die NSG keinen Netzwerkschnittstellen (NICs) zugeordnet. Sie ist einem Subnetz namens **FrontEnd** zugeordnet.

## Verwalten von Regeln

Sie können zu einer vorhandenen NSG Regeln hinzufügen, vorhandene Regeln bearbeiten und Regeln entfernen.

### Hinzufügen einer Regel

Um eine Regel hinzuzufügen, die den **eingehenden** Verkehr an den Port **443** von jedem Computer aus an die **NSG-FrontEnd**-NSG zulässt, führen Sie den Befehl `azure network nsg rule create`, wie unten gezeigt, aus.

	azure network nsg rule create --resource-group RG-NSG \
		--nsg-name NSG-FrontEnd \
		--name allow-https \
		--description "Allow access to port 443 for HTTPS" \
		--protocol Tcp \
		--source-address-prefix * \
		--source-port-range * \
		--destination-address-prefix * \
		--destination-port-range 443 \
		--access Allow \
		--priority 102 \
		--direction Inbound		

Erwartete Ausgabe:

	info:    Executing command network nsg rule create
	+ Looking up the network security rule "allow-https"
	+ Creating a network security rule "allow-https"
	+ Looking up the network security group "NSG-FrontEnd"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
	data:    Name                            : allow-https
	data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
	data:    Provisioning state              : Succeeded
	data:    Description                     : Allow access to port 443 for HTTPS
	data:    Source IP                       : *
	data:    Source Port                     : *
	data:    Destination IP                  : *
	data:    Destination Port                : 443
	data:    Protocol                        : Tcp
	data:    Direction                       : Inbound
	data:    Access                          : Allow
	data:    Priority                        : 102
	info:    network nsg rule create command OK

### Ändern einer Regel

Um die oben erstellte Regel zu ändern, um eingehenden Verkehr nur aus dem **Internet** zuzulassen, führen Sie den Befehl `azure network nsg rule set`, wie unten gezeigt, aus.

	azure network nsg rule set --resource-group RG-NSG \
		--nsg-name NSG-FrontEnd \
		--name allow-https \
		--source-address-prefix Internet

Erwartete Ausgabe:

	info:    Executing command network nsg rule set
	+ Looking up the network security group "NSG-FrontEnd"
	+ Setting a network security rule "allow-https"
	+ Looking up the network security group "NSG-FrontEnd"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/allow-https
	data:    Name                            : allow-https
	data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
	data:    Provisioning state              : Succeeded
	data:    Description                     : Allow access to port 443 for HTTPS
	data:    Source IP                       : Internet
	data:    Source Port                     : *
	data:    Destination IP                  : *
	data:    Destination Port                : 443
	data:    Protocol                        : Tcp
	data:    Direction                       : Inbound
	data:    Access                          : Allow
	data:    Priority                        : 102
	info:    network nsg rule set command OK

### Löschen einer Regel

Um die oben erstellte Regel zu löschen, führen Sie den Befehl `azure network nsg rule delete`, wie unten gezeigt, aus.

	azure network nsg rule delete --resource-group RG-NSG \
		--nsg-name NSG-FrontEnd \
		--name allow-https \
		--quiet

>[AZURE.NOTE] Der Parameter **--quiet** stellt sicher, dass Sie die Löschung nicht bestätigen müssen.

Erwartete Ausgabe:

	info:    Executing command network nsg rule delete
	+ Looking up the network security group "NSG-FrontEnd"
	+ Deleting network security rule "allow-https"
	info:    network nsg rule delete command OK

## Verwalten von Zuordnungen

Sie können eine NSG Subnetzen und NICs zuordnen. Sie können die Zuordnung einer NSG zu einer beliebigen Ressource auch aufheben.

### Zuordnen einer NSG zu einer NIC

Um die **NSG-FrontEnd**-NSG der **TestNICWeb1** -NIC zuzuordnen, führen Sie den Befehl `azure network nic set`, wie unten gezeigt, aus.

	azure network nic set --resource-group RG-NSG \
		--name TestNICWeb1 \
		--network-security-group-name NSG-FrontEnd

Erwartete Ausgabe:

	info:    Executing command network nic set
	+ Looking up the network interface "TestNICWeb1"
	+ Looking up the network security group "NSG-FrontEnd"
	+ Updating network interface "TestNICWeb1"
	+ Looking up the network interface "TestNICWeb1"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
	data:    Name                            : TestNICWeb1
	data:    Type                            : Microsoft.Network/networkInterfaces
	data:    Location                        : westus
	data:    Provisioning state              : Succeeded
	data:    MAC address                     : 00-0D-3A-30-A1-F8
	data:    Enable IP forwarding            : false
	data:    Tags                            : displayName=NetworkInterfaces - Web
	data:    Network security group          : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
	data:    Virtual machine                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
	data:    IP configurations:
	data:      Name                          : ipconfig1
	data:      Provisioning state            : Succeeded
	data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
	data:      Private IP address            : 192.168.1.5
	data:      Private IP Allocation Method  : Dynamic
	data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
	data:
	info:    network nic set command OK

### Aufhebung der Zuordnung einer NSG zu einer NIC

Um die Zuordnung der **NSG-FrontEnd**-NSG zur **TestNICWeb1**-NIC aufzuheben, führen Sie den Befehl `azure network nic set`, wie unten gezeigt, aus.

	azure network nic set --resource-group RG-NSG --name TestNICWeb1 --network-security-group-id ""

>[AZURE.NOTE] Beachten Sie, den "" (leer) Wert für den Parameter **network-security-group-id**. So entfernen Sie die Zuordnung zu einer NSG. Die selbe Aktion können Sie nicht mit dem Parameter **Netzwerksicherheitsgruppen-Name** durchführen.

Erwartetes Ergebnis:

	info:    Executing command network nic set
	+ Looking up the network interface "TestNICWeb1"
	+ Updating network interface "TestNICWeb1"
	+ Looking up the network interface "TestNICWeb1"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1
	data:    Name                            : TestNICWeb1
	data:    Type                            : Microsoft.Network/networkInterfaces
	data:    Location                        : westus
	data:    Provisioning state              : Succeeded
	data:    MAC address                     : 00-0D-3A-30-A1-F8
	data:    Enable IP forwarding            : false
	data:    Tags                            : displayName=NetworkInterfaces - Web
	data:    Virtual machine                 : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Compute/virtualMachines/Web1
	data:    IP configurations:
	data:      Name                          : ipconfig1
	data:      Provisioning state            : Succeeded
	data:      Public IP address             : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/publicIPAddresses/TestPIPWeb1
	data:      Private IP address            : 192.168.1.5
	data:      Private IP Allocation Method  : Dynamic
	data:      Subnet                        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
	data:
	info:    network nic set command OK

### Aufhebung der Zuordnung einer NSG zu einem Subnetz

Um die Zuordnung der **NSG-FrontEnd**-NSG zum **FrontEnd**-Subnetz aufzuheben, führen Sie den Befehl `azure network vnet subnet set` wie unten gezeigt, aus.

	azure network vnet subnet set --resource-group RG-NSG \
		--vnet-name TestVNet \
		--name FrontEnd \
		--network-security-group-id ""

Erwartete Ausgabe:

	info:    Executing command network vnet subnet set
	+ Looking up the subnet "FrontEnd"
	+ Setting subnet "FrontEnd"
	+ Looking up the subnet "FrontEnd"
	data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
	data:    Type                            : Microsoft.Network/virtualNetworks/subnets
	data:    ProvisioningState               : Succeeded
	data:    Name                            : FrontEnd
	data:    Address prefix                  : 192.168.1.0/24
	data:    IP configurations:
	data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
	data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
	data:
	info:    network vnet subnet set command OK

### Zuordnen einer NSG zu einem Subnetz

Um die Zuordnung der **NSG-FrontEnd**-NSG zum **FrontEnd**-Subnetz aufzuheben, führen Sie den Befehl `azure network vnet subnet set`, wie unten gezeigt, aus.

	azure network vnet subnet set --resource-group RG-NSG \
		--vnet-name TestVNet \
		--name FrontEnd \
		--network-security-group-name NSG-FronEnd

>[AZURE.NOTE] Der obige Befehl funktioniert nur, weil die **NSG-FrontEnd**-NSG sich in derselben Ressourcengruppe wie das virtuelle Netzwerk **TestVNet** befindet. Falls sich die NSG in einer anderen Ressourcengruppe befindet, müssen Sie stattdessen den Parameter **--network-security-group-id** verwenden und die vollständige ID für die NSG bereitstellen. Sie können die ID abrufen, indem Sie **azure network nsg show --resource-group RG-NSG --name NSG-FrontEnd --json** durchführen und nach der Eigenschaft **id** suchen.

Erwartete Ausgabe:

		info:    Executing command network vnet subnet set
		+ Looking up the subnet "FrontEnd"
		+ Looking up the network security group "NSG-FrontEnd"
		+ Setting subnet "FrontEnd"
		+ Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:    Network security group          : [object Object]
		data:    IP configurations:
		data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ipConfigurations/ipconfig1
		data:      /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/RG-NSG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ipConfigurations/ipconfig1
		data:
		info:    network vnet subnet set command OK

## Löschen einer NSG

Sie können eine NSG nur löschen, wenn sie keiner Ressource zugeordnet ist. Um eine NSG zu löschen, gehen Sie folgendermaßen vor.

1. Um die Ressourcen, die einer NSG zugeordnet sind, zu überprüfen, führen Sie `azure network nsg show` aus, wie in [Anzeigen lassen von NSG-Zuordnungen](#View-NSGs-associations) gezeigt.
2. Falls die NSG NICs zugeordnet ist, führen Sie `azure network nic set` für jede NIC aus, wie in [Aufhebung der Zuordnung einer NSG zu einer NIC](#Dissociate-an-NSG-from-a-NIC) gezeigt. 
3. Falls die NSG einem Subnetz zugeordnet ist, führen Sie `azure network vnet subnet set`, wie in [Aufhebung der Zuordnung einer NSG zu einem Subnetz](#Dissociate-an-NSG-from-a-subnet) gezeigt, für jedes Subnetz aus.
4. Um die NSG zu löschen, führen Sie den Befehl `azure network nsg delete`, wie unten gezeigt, aus.

		azure network nsg delete --resource-group RG-NSG --name NSG-FrontEnd --quiet

	Erwartete Ausgabe:

		info:    Executing command network nsg delete
		+ Looking up the network security group "NSG-FrontEnd"
		+ Deleting network security group "NSG-FrontEnd"
		info:    network nsg delete command OK

## Nächste Schritte

- [Aktivieren der Protokollierung](virtual-network-nsg-manage-log.md) für NSGs

<!---HONumber=AcomDC_0323_2016-->