<properties 
   pageTitle="Steuern des Routings und Verwenden virtueller Geräte im Ressourcen-Manager mit der Azure-Befehlszeilenschnittstelle | Microsoft Azure"
   description="Sie erfahren, wie Sie das Routing für virtuelle Geräte mit der Azure-Befehlszeilenschnittstelle steuern und diese verwenden."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/30/2015"
   ms.author="telmos" />

#Erstellen benutzerdefinierter Routen (UDR) in der Azure-Befehlszeilenschnittstelle

[AZURE.INCLUDE [virtual-network-create-udr-arm-selectors-include.md](../../includes/virtual-network-create-udr-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das Ressourcen-Manager-Bereitstellungsmodell. Sie haben auch die Möglichkeit, [benutzerdefinierte Routen im klassischen Bereitstellungsmodell zu erstellen](virtual-network-create-udr-classic-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Die folgenden Beispielbefehle für die Azure-Befehlszeilenschnittstelle setzen voraus, dass bereits eine einfache Umgebung erstellt wurde, die auf dem obigen Szenario basiert. Wenn Sie die Befehle so ausführen möchten, wie sie in diesem Dokument angezeigt werden, erstellen Sie zunächst die Testumgebung durch Bereitstellen [dieser Vorlage](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before). Klicken Sie auf **In Azure bereitstellen**, ersetzen Sie bei Bedarf die Standardparameterwerte, und befolgen Sie dann die Anweisungen im Portal.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Erstellen der benutzerdefinierten Route für das Front-End-Subnetz
Führen Sie zum Erstellen der Routingtabelle und der für das Front-End-Subnetz erforderlichen Route anhand des oben beschriebenen Szenarios die folgenden Schritte aus.

3. Führen Sie den Befehl **`azure network route-table create`** aus, um eine Routingtabelle für das Front-End-Subnetz zu erstellen.

		azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest

	Ausgabe:

		info:    Executing command network route-table create
		info:    Looking up route table "UDR-FrontEnd"
		info:    Creating route table "UDR-FrontEnd"
		info:    Looking up route table "UDR-FrontEnd"
		data:    Id                              : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG/providers/Microsoft.Network/
		routeTables/UDR-FrontEnd
		data:    Name                            : UDR-FrontEnd
		data:    Type                            : Microsoft.Network/routeTables
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		info:    network route-table create command OK

	Parameter: - **-g (oder --resource-group)**. Name der Ressourcengruppe, in der die NSG erstellt wird. In diesem Szenario *TestRG*. - **-l (oder --location)**. Azure-Region, in der die neue NSG erstellt wird. In diesem Szenario *westus*. - **-n (oder --name)**. Name der neuen NSG. In diesem Szenario *NSG-FrontEnd*.

4. Führen Sie den Befehl **`azure network route-table route create`** aus, um in der oben erstellten Routingtabelle eine Route zu erstellen, die sämtlichen an das Back-End-Subnetz (192.168.2.0/24) gerichteten Datenverkehr an den virtuellen Computer **FW1** (192.168.0.4) umleitet.

		azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4

	Ausgabe:

		info:    Executing command network route-table route create
		info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
		info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
		info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		routeTables/UDR-FrontEnd/routes/RouteToBackEnd
		data:    Name                            : RouteToBackEnd
		data:    Provisioning state              : Succeeded
		data:    Next hop type                   : VirtualAppliance
		data:    Next hop IP address             : 192.168.0.4
		data:    Address prefix                  : 192.168.2.0/24
		info:    network route-table route create command OK

	Parameter: - **-r (oder --route-table-name)**. Der Name der Routingtabelle, der die Route hinzugefügt wird. In diesem Szenario *UDR-FrontEnd*. - **-a (oder --address-prefix)**. Das Adresspräfix für das Zielsubnetz der Pakete. In diesem Szenario *192.168.2.0/24*. - **-y (oder --next-hop-type)**. Der Typ des Zielobjekts für den Datenverkehr. Mögliche Werte sind *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* oder *None*. - **-p (oder --next-hop-ip-address**). Die IP-Adresse für den nächsten Hop. In diesem Szenario *192.168.0.4*.

5. Führen Sie den Befehl **`azure network vnet subnet set`** aus, um die oben erstellte Routingtabelle dem Subnetz **FrontEnd** zuzuordnen.

		azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd

	Ausgabe:

		info:    Executing command network vnet subnet set
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up route table "UDR-FrontEnd"
		info:    Setting subnet "FrontEnd"
		info:    Looking up the subnet "FrontEnd"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/FrontEnd
		data:    Type                            : Microsoft.Network/virtualNetworks/subnets
		data:    ProvisioningState               : Succeeded
		data:    Name                            : FrontEnd
		data:    Address prefix                  : 192.168.1.0/24
		data:    Network security group          : [object Object]
		data:    Route Table                     : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		routeTables/UDR-FrontEnd
		data:    IP configurations:
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
		igurations/ipconfig1
		data:      /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
		igurations/ipconfig1
		data:    
		info:    network vnet subnet set command OK

	Parameter: - **-t (oder --vnet-name)**. Name des VNet mit dem Subnetz. In diesem Szenario *TestVNet*.
 
## Erstellen der benutzerdefinierten Route für das Back-End-Subnetz
Führen Sie zum Erstellen der Routingtabelle und der für das Back-End-Subnetz erforderlichen Route anhand des oben beschriebenen Szenarios die folgenden Schritte aus.

1. Führen Sie den Befehl **`azure network route-table create`** aus, um eine Routingtabelle für das Back-End-Subnetz zu erstellen.

		azure network route-table create -g TestRG -n UDR-BackEnd -l westus

4. Führen Sie den Befehl **`azure network route-table route create`** aus, um in der oben erstellten Routingtabelle eine Route zu erstellen, die sämtlichen an das Front-End-Subnetz (192.168.1.0/24) gerichteten Datenverkehr an den virtuellen Computer **FW1** (192.168.0.4) umleitet.

		azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4

5. Führen Sie den Befehl **`azure network vnet subnet set`** aus, um die oben erstellte Routingtabelle dem Subnetz **BackEnd** zuzuordnen.

		azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd

## Aktivieren der IP-Weiterleitung auf „FW1“
Führen Sie zum Aktivieren der IP-Weiterleitung in der von **FW1** verwendeten Netzwerkkarte die folgenden Schritte aus.

1. Führen Sie den Befehl **`azure network nic show`** aus, und achten Sie auf den Wert für die **Aktivierung der IP-Weiterleitung**. Er sollte auf *false* festgelegt sein.

		azure network nic show -g TestRG -n NICFW1

	Ausgabe:
		
		info:    Executing command network nic show
		info:    Looking up the network interface "NICFW1"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkInterfaces/NICFW1
		data:    Name                            : NICFW1
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    MAC address                     : 00-0D-3A-30-95-B3
		data:    Enable IP forwarding            : false
		data:    Tags                            : displayName=NetworkInterfaces - DMZ
		data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
		virtualMachines/FW1
		data:    IP configurations:
		data:      Name                          : ipconfig1
		data:      Provisioning state            : Succeeded
		data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		publicIPAddresses/PIPFW1
		data:      Private IP address            : 192.168.0.4
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/DMZ
		data:    
		info:    network nic show command OK

2. Führen Sie den Befehl **`azure network nic set`** aus, um die IP-Weiterleitung zu aktivieren.

		azure network nic set -g TestRG -n NICFW1 -f true

	Ausgabe:

		info:    Executing command network nic set
		info:    Looking up the network interface "NICFW1"
		info:    Updating network interface "NICFW1"
		info:    Looking up the network interface "NICFW1"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		networkInterfaces/NICFW1
		data:    Name                            : NICFW1
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : westus
		data:    Provisioning state              : Succeeded
		data:    MAC address                     : 00-0D-3A-30-95-B3
		data:    Enable IP forwarding            : true
		data:    Tags                            : displayName=NetworkInterfaces - DMZ
		data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/
		virtualMachines/FW1
		data:    IP configurations:
		data:      Name                          : ipconfig1
		data:      Provisioning state            : Succeeded
		data:      Public IP address             : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		publicIPAddresses/PIPFW1
		data:      Private IP address            : 192.168.0.4
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/
		virtualNetworks/TestVNet/subnets/DMZ
		data:    
		info:    network nic set command OK

	Parameter:

	- **-f (oder --enable-ip-forwarding)**. *true* oder *false*.

<!---HONumber=Oct15_HO2-->