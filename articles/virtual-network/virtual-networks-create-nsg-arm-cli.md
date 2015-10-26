<properties 
   pageTitle="Erstellen von NSGs im ARM-Modus über die Azure-Befehlszeilenschnittstelle | Microsoft Azure"
   description="Informationen zum Erstellen und Bereitstellen von NSGs im ARM-Modus über die Azure-Befehlszeilenschnittstelle"
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
   ms.date="09/15/2015"
   ms.author="telmos" />

# Erstellen von NSGs in der Azure-Befehlszeilenschnittstelle

[AZURE.INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das Ressourcen-Manager-Bereitstellungsmodell. Sie haben auch die Möglichkeit, [NSGs im klassischen Bereitstellungsmodell zu erstellen](virtual-networks-create-nsg-classic-cli.md).

[AZURE.INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Die folgenden Beispielbefehle für die Azure-Befehlszeilenschnittstelle setzen voraus, dass bereits eine einfache Umgebung erstellt wurde, die auf dem obigen Szenario basiert. Wenn Sie die Befehle so ausführen möchten, wie sie in diesem Dokument angezeigt werden, erstellen Sie zunächst die Testumgebung durch Bereitstellen [dieser Vorlage](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd). Klicken Sie auf **Deploy to Azure**, ersetzen Sie bei Bedarf die Standardparameterwerte, und befolgen Sie dann die Anweisungen im Portal.

## Erstellen der NSG für das Front-End-Subnetz
Führen Sie zum Erstellen einer NSG mit dem Namen *NSG-FrontEnd* basierend auf dem oben beschriebenen Szenario die folgenden Schritte aus.

1. Wenn Sie die Azure-Befehlszeilenschnittstelle noch nie verwendet haben, ziehen Sie [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](xplat-cli-install.md) zurate, und folgen Sie den Anweisungen bis zu dem Punkt, an dem Sie Ihr Azure-Konto und Ihr Abonnement auswählen.

2. Führen Sie den Befehl **azure config mode** aus, um in den Ressourcen-Manager-Modus zu wechseln, wie unten dargestellt.

		azure config mode arm

	Erwartete Ausgabe:

		info:    New mode is arm

3. Führen Sie den Befehl **azure network nsg create** aus, um eine NSG zu erstellen.

		azure network nsg create -g TestRG -l westus -n NSG-FrontEnd

	Erwartete Ausgabe:

		info:    Executing command network nsg create
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Creating a network security group "NSG-FrontEnd"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd
		data:    Name                            : NSG-FrontEnd
		data:    Type                            : Microsoft.Network/networkSecurityGroups
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Security group rules:
		data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
		data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
		data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000   
		data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001   
		data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500   
		data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000   
		data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001   
		data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500   
		info:    network nsg create command OK

	Parameter:
	- **-g (oder --resource-group)**. Name der Ressourcengruppe, in der die NSG erstellt wird. In diesem Szenario *TestRG*.
	- **-l (oder --location)**. Azure-Region, in der die neue NSG erstellt wird. In diesem Szenario *westus*. 
	- **-n (oder --name)**. Name der neuen NSG. In diesem Szenario *NSG-FrontEnd*.

4. Führen Sie den Befehl **azure network nsg rule create** aus, um eine Regel zu erstellen, die den Zugriff auf Port 3389 (RDP) über das Internet zulässt.

		azure network nsg rule create -g TestRG -a NSG-FrontEnd -n rdp-rule -c Allow -p Tcp -r Inbound -y 100 -f Internet -o * -e * -u 3389

	Erwartete Ausgabe:

		info:    Executing command network nsg rule create
		warn:    Using default direction: Inbound
		info:    Looking up the network security rule "rdp-rule"
		info:    Creating a network security rule "rdp-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkSecurityGroups/NSG-FrontEnd/securityRules/rdp
		-rule
		data:    Name                            : rdp-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : Internet
		data:    Source Port                     : *
		data:    Destination IP                  : *
		data:    Destination Port                : 3389
		data:    Protocol                        : Tcp
		data:    Direction                       : Inbound
		data:    Access                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK

	Parameter:

	- **-a (oder --nsg-name)**. Name der NSG, in der die Regel erstellt wird. In diesem Szenario *NSG-FrontEnd*.
	- **-n (oder --name)**. Name der neuen Regel. In diesem Szenario *rdp-rule*.
	- **-c (oder --access)**. Zugriffsebene für die Regel (Deny oder Allow).
	- **-p (oder --protocol)**. Protokoll (Tcp, Udp oder *) für die Regel.
- **-r (oder --direction)**. Richtung der Verbindung (Inbound oder Outbound).
	- **-y (oder --priority)**. Priorität für die Regel.
	- **-f (oder --source-address-prefix)**. Quelladresspräfix in CIDR oder Verwendung von Standardtags.
	- **-o (oder --source-port-range)**. Quellport oder Portbereich.
	- **-e (oder --destination-address-prefix)**. Zieladresspräfix in CIDR oder Verwendung von Standardtags.
	- **-u (oder --destination-port-range)**. Zielport oder Portbereich.	

5. Führen Sie den Befehl **azure network nsg rule create** aus, um eine Regel zu erstellen, die den Zugriff auf Port 80 (HTTP) über das Internet zulässt.

		azure network nsg rule create -g TestRG -a NSG-FrontEnd -n web-rule -c Allow -p Tcp -r Inbound -y 200 -f Internet -o * -e * -u 80

	Erwartete Ausgabe:

		info:    Executing command network nsg rule create
		info:    Looking up the network security rule "web-rule"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-FrontEnd/securityRules/web-rule
		data:    Name                            : web-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : Internet
		data:    Source Port                     : *
		data:    Destination IP                  : *
		data:    Destination Port                : 80
		data:    Protocol                        : Tcp
		data:    Direction                       : Inbound
		data:    Access                          : Allow
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. Führen Sie den Befehl **azure network vnet subnet set** aus, um die NSG mit dem Front-End-Subnetz zu verbinden.

		azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -o NSG-FrontEnd

	Erwartete Ausgabe:

		info:    Executing command network vnet subnet set
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up the network security group "NSG-FrontEnd"
		info:    Setting subnet "FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:    Network security group          : [object Object]
		data:    IP configurations:
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb2/ip
		Configurations/ipconfig1
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICWeb1/ip
		Configurations/ipconfig1
		data:    
		info:    network vnet subnet set command OK

## Erstellen der NSG für das Back-End-Subnetz
Führen Sie zum Erstellen einer NSG mit dem Namen *NSG-BackEnd* basierend auf dem oben beschriebenen Szenario die folgenden Schritte aus.

3. Führen Sie den Befehl **azure network nsg create** aus, um eine NSG zu erstellen.

		azure network nsg create -g TestRG -l westus -n NSG-BackEnd

	Erwartete Ausgabe:

		info:    Executing command network nsg create
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Creating a network security group "NSG-BackEnd"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-BackEnd
		data:    Name                            : NSG-BackEnd
		data:    Type                            : Microsoft.Network/networkSecurityGroups
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    Security group rules:
		data:    Name                           Source IP          Source Port  Destination IP  Destination Port  Protocol  Direction  Access  Priority
		data:    -----------------------------  -----------------  -----------  --------------  ----------------  --------  ---------  ------  --------
		data:    AllowVnetInBound               VirtualNetwork     *            VirtualNetwork  *                 *         Inbound    Allow   65000   
		data:    AllowAzureLoadBalancerInBound  AzureLoadBalancer  *            *               *                 *         Inbound    Allow   65001   
		data:    DenyAllInBound                 *                  *            *               *                 *         Inbound    Deny    65500   
		data:    AllowVnetOutBound              VirtualNetwork     *            VirtualNetwork  *                 *         Outbound   Allow   65000   
		data:    AllowInternetOutBound          *                  *            Internet        *                 *         Outbound   Allow   65001   
		data:    DenyAllOutBound                *                  *            *               *                 *         Outbound   Deny    65500   
		info:    network nsg create command OK

4. Führen Sie den Befehl **azure network nsg rule create** aus, um eine Regel zu erstellen, die den Zugriff auf Port 1433 (SQL) über das Front-End-Subnetz zulässt.

		azure network nsg rule create -g TestRG -a NSG-BackEnd -n sql-rule -c Allow -p Tcp -r Inbound -y 100 -f 192.168.1.0/24 -o * -e * -u 1433

	Erwartete Ausgabe:

		info:    Executing command network nsg rule create
		info:    Looking up the network security rule "sql-rule"
		info:    Creating a network security rule "sql-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-BackEnd/securityRules/sql-rule
		data:    Name                            : sql-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : 192.168.1.0/24
		data:    Source Port                     : *
		data:    Destination IP                  : *
		data:    Destination Port                : 1433
		data:    Protocol                        : Tcp
		data:    Direction                       : Inbound
		data:    Access                          : Allow
		data:    Priority                        : 100
		info:    network nsg rule create command OK

5. Führen Sie den Befehl **azure network nsg rule create** aus, um eine Regel zu erstellen, die den Zugriff über das Internet verweigert.

		azure network nsg rule create -g TestRG -a NSG-BackEnd -n web-rule -c Deny -p * -r Outbound -y 200 -f * -o * -e Internet -u *

	Erwartete Ausgabe:

		info:    Executing command network nsg rule create
		info:    Looking up the network security rule "web-rule"
		info:    Creating a network security rule "web-rule"
		info:    Looking up the network security group "NSG-BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkSecurityGroups/NSG-BackEnd/securityRules/web-rule
		data:    Name                            : web-rule
		data:    Type                            : Microsoft.Network/networkSecurityGroups/securityRules
		data:    Provisioning state              : Succeeded
		data:    Source IP                       : *
		data:    Source Port                     : *
		data:    Destination IP                  : Internet
		data:    Destination Port                : *
		data:    Protocol                        : *
		data:    Direction                       : Outbound
		data:    Access                          : Deny
		data:    Priority                        : 200
		info:    network nsg rule create command OK

6. Führen Sie den Befehl **azure network vnet subnet set** aus, um die NSG mit dem Back-End-Subnetz zu verbinden.

		azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -o NSG-BackEnd

	Erwartete Ausgabe:

		info:    Executing command network vnet subnet set
		info:    Looking up the subnet "BackEnd"
		info:    Looking up the network security group "NSG-BackEnd"
		info:    Setting subnet "BackEnd"
		info:    Looking up the subnet "BackEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/BackEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : BackEnd
		data:    Address prefix                  : 192.168.2.0/24
		data:    Network security group          : [object Object]
		data:    IP configurations:
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL1/ip
		Configurations/ipconfig1
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNICSQL2/ip
		Configurations/ipconfig1
		data:    
		info:    network vnet subnet set command OK

<!---HONumber=Oct15_HO3-->