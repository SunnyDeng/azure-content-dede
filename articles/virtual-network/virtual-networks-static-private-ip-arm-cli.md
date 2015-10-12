<properties 
   pageTitle="Einrichten einer statischen privaten IP-Adresse im ARM-Modus über die Befehlszeilenschnittstelle | Microsoft Azure"
   description="Grundlegendes zu statischen IP-Adressen (DIPs) und zur Verwaltung dieser IP-Adressen im ARM-Modus über die Befehlszeilenschnittstelle"
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
   ms.date="09/08/2015"
   ms.author="telmos" />

# Festlegen einer statischen privaten IP-Adresse über die Azure-Befehlszeilenschnittstelle

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das Ressourcen-Manager-Bereitstellungsmodell. Sie können [eine statische private IP-Adresse auch im klassischen Bereitstellungsmodell verwalten](virtual-networks-static-private-ip-classic-cli.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Die folgenden Beispielbefehle für die Azure-Befehlszeilenschnittstelle setzen voraus, dass bereits eine einfache Umgebung erstellt wurde. Wenn Sie die in diesem Dokument aufgeführten Befehle ohne Veränderungen ausführen möchten, erstellen Sie zunächst eine Testumgebung, wie unter [Erstellen eines VNets](virtual-networks-create-vnet-arm-cli.md) beschrieben.

## Angeben einer statischen privaten IP-Adresse beim Erstellen eines virtuellen Computers
Erstellen Sie in einem VNet mit dem Namen *TestVNet* im Subnetz *FrontEnd* einen virtuellen Computer mit dem Namen *DNS01* und der statischen privaten IP-Adresse *192.168.1.101*. Gehen Sie dabei wie folgt vor:

1. Wenn Sie die Azure-Befehlszeilenschnittstelle noch nie verwendet haben, ziehen Sie [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](xplat-cli-install.md) zurate, und folgen Sie den Anweisungen bis zu dem Punkt, an dem Sie Ihr Azure-Konto und Ihr Abonnement auswählen.

2. Führen Sie den Befehl **azure config mode** aus, um in den Ressourcen-Manager-Modus zu wechseln, wie unten dargestellt.

		azure config mode arm

	Erwartete Ausgabe:

		info:    New mode is arm

3. Führen Sie **azure network public-ip create** aus, um eine öffentliche IP-Adresse für den virtuellen Computer zu generieren. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert.

		azure network public-ip create -g TestRG -n TestPIP -l centralus
	
	Erwartete Ausgabe:

		info:    Executing command network public-ip create
		+ Looking up the public ip "TestPIP"
		+ Creating public ip address "TestPIP"
		+ Looking up the public ip "TestPIP"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP
		data:    Name                            : TestPIP
		data:    Type                            : Microsoft.Network/publicIPAddresses
		data:    Location                        : centralus
		data:    Provisioning state              : Succeeded
		data:    Allocation method               : Dynamic
		data:    Idle timeout                    : 4
		info:    network public-ip create command OK

	- **-g (oder --resource-group)**. Name der Ressourcengruppe, in der die öffentliche IP-Adresse erstellt wird.
	- **-n (oder --name)**. Der Name der öffentlichen IP-Adresse.
	- **-l (oder --location)**. Azure-Region, in der die öffentliche IP-Adresse erstellt wird. In diesem Szenario: *centralus*.

3. Führen Sie den Befehl **azure network nic create** aus, um eine Netzwerkkarte mit statischer privater IP-Adresse zu erstellen. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert.

		azure network nic create -g TestRG -n TestNIC -l centralus -a 192.168.1.101 -m TestVNet -k FrontEnd

	Erwartete Ausgabe:

		+ Looking up the network interface "TestNIC"
		+ Looking up the subnet "FrontEnd"
		+ Creating network interface "TestNIC"
		+ Looking up the network interface "TestNIC"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
		data:    Name                            : TestNIC
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : centralus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.1.101
		data:      Private IP Allocation Method  : Static
		data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:
		info:    network nic create command OK

	- **-a (oder --private-ip-address)**. Statische private IP-Adresse für die Netzwerkkarte.
	- **-m (oder --subnet-vnet-name)**. Name des VNets, in dem die Netzwerkkarte erstellt wird.
	- **-k (oder --subnet-name)**. Name des Subnetzes, in dem die Netzwerkkarte erstellt wird.

4. Führen Sie den Befehl **azure vm create** aus, um den virtuellen Computer unter Verwendung der oben erstellten öffentlichen IP-Adresse und Netzwerkkarte zu erstellen. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert.

		azure vm create -g TestRG -n DNS01 -l centralus -y Windows -f TestNIC -i TestPIP -F TestVNet -j FrontEnd -o vnetstorage -q bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2 -u adminuser -p AdminP@ssw0rd

	Erwartete Ausgabe:

		info:    Executing command vm create
		+ Looking up the VM "DNS01"
		info:    Using the VM Size "Standard_A1"
		warn:    The image "bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2" will be used for VM
		info:    The [OS, Data] Disk or image configuration requires storage account
		+ Looking up the storage account vnetstorage
		+ Looking up the NIC "TestNIC"
		info:    Found an existing NIC "TestNIC"
		info:    Found an IP configuration with virtual network subnet id "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "TestNIC"
		info:    Found public ip parameters, trying to setup PublicIP profile
		+ Looking up the public ip "TestPIP"
		info:    Found an existing PublicIP "TestPIP"
		info:    Configuring identified NIC IP configuration with PublicIP "TestPIP"
		+ Updating NIC "TestNIC"
		+ Looking up the NIC "TestNIC"
		+ Creating VM "DNS01"
		info:    vm create command OK

	- **-y (oder --os-type)**. Typ des Betriebssystems für den virtuellen Computer, entweder *Windows* oder *Linux*.
	- **-f (oder --nic-name)**. Name der Netzwerkkarte, die der virtuelle Computer verwenden wird.
	- **-i (oder --public-ip-name)**. Name der öffentlichen IP-Adresse, die der virtuelle Computer verwenden wird.
	- **-F (oder --vnet-name)**. Name des VNets, in dem der virtuelle Computer erstellt wird.
	- **-j (oder --vnet-subnet-name)**. Name des Subnetzes, in dem der virtuelle Computer erstellt wird.

## Abrufen der Informationen zur statischen privaten IP-Adresse für einen virtuellen Computer

Führen Sie den folgenden Befehl über die Azure-Befehlszeilenschnittstelle aus, und sehen Sie sich die Werte für *Private IP alloc-method* und *Private IP address* an, um Informationen zur statischen privaten IP-Adresse des virtuellen Computers zu erhalten, der mit dem obigen Skript erstellt wurde:

	azure vm show -g TestRG -n DNS01

Erwartete Ausgabe:

	info:    Executing command vm show
	+ Looking up the VM "DNS01"
	+ Looking up the NIC "TestNIC"
	+ Looking up the public ip "TestPIP
	data:    Id                              :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
	data:    ProvisioningState               :Succeeded
	data:    Name                            :DNS01
	data:    Location                        :centralus
	data:    Type                            :Microsoft.Compute/virtualMachines
	data:
	data:    Hardware Profile:
	data:      Size                          :Standard_A1
	data:
	data:    Storage Profile:
	data:      Source image:
	data:        Id                          :/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/services/images/bd507d3a70934695bc2128e3e5a255ba__RightImage-Windows-2012R2-x64-v14.2
	data:
	data:      OS Disk:
	data:        OSType                      :Windows
	data:        Name                        :cli08d7bd987a0112a8-os-1441774961355
	data:        Caching                     :ReadWrite
	data:        CreateOption                :FromImage
	data:        Vhd:
	data:          Uri                       :https://vnetstorage2.blob.core.windows.net/vhds/cli08d7bd987a0112a8-os-1441774961355vhd
	data:
	data:    OS Profile:
	data:      Computer Name                 :DNS01
	data:      User Name                     :adminuser
	data:      Windows Configuration:
	data:        Provision VM Agent          :true
	data:        Enable automatic updates    :true
	data:
	data:    Network Profile:
	data:      Network Interfaces:
	data:        Network Interface #1:
	data:          Id                        :/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
	data:          Primary                   :true
	data:          MAC Address               :00-0D-3A-90-1A-A8
	data:          Provisioning State        :Succeeded
	data:          Name                      :TestNIC
	data:          Location                  :centralus
	data:            Private IP alloc-method :Static
	data:            Private IP address      :192.168.1.101
	data:            Public IP address       :40.122.213.159
	info:    vm show command OK

## Entfernen einer statischen privaten IP-Adresse von einem virtuellen Computer
Es ist nicht möglich, über die Azure-Befehlszeilenschnittstelle für den Ressourcen-Manager eine statische private IP-Adresse von einer Netzwerkkarte zu entfernen. Erstellen Sie stattdessen eine neue Netzwerkkarte mit einer dynamischen IP-Adresse, entfernen Sie die vorherige Netzwerkkarte vom virtuellen Computer, und fügen Sie anschließend die neue Netzwerkkarte zum virtuellen Computer hinzu. Zum Ändern der Netzwerkkarte für den in den obigen Befehlen verwendeten virtuellen Computer führen Sie die folgenden Schritte aus.
	
1. Führen Sie den Befehl **azure network nic create** aus, um eine neue Netzwerkkarte mit dynamischer IP-Zuordnung zu erstellen. Wie Sie sehen, muss bei diesem Vorgang keine IP-Adresse angegeben werden.

		azure network nic create -g TestRG -n TestNIC2 -l centralus -m TestVNet -k FrontEnd

	Erwartete Ausgabe:

		info:    Executing command network nic create
		+ Looking up the network interface "TestNIC2"
		+ Looking up the subnet "FrontEnd"
		+ Creating network interface "TestNIC2"
		+ Looking up the network interface "TestNIC2"
		data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
		data:    Name                            : TestNIC2
		data:    Type                            : Microsoft.Network/networkInterfaces
		data:    Location                        : centralus
		data:    Provisioning state              : Succeeded
		data:    Enable IP forwarding            : false
		data:    IP configurations:
		data:      Name                          : NIC-config
		data:      Provisioning state            : Succeeded
		data:      Private IP address            : 192.168.1.6
		data:      Private IP Allocation Method  : Dynamic
		data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
		data:
		info:    network nic create command OK

2. Führen Sie den Befehl **azure vm set** aus, um die vom virtuellen Computer verwendete Netzwerkkarte zu ändern.

		azure vm set -g TestRG -n DNS01 -N TestNIC2

	Erwartete Ausgabe:

		info:    Executing command vm set
		+ Looking up the VM "DNS01"
		+ Looking up the NIC "TestNIC2"
		+ Updating VM "DNS01"
		info:    vm set command OK

3. Führen Sie den Befehl **azure network nic delete** aus, um die alte Netzwerkkarte zu löschen.

		azure network nic delete -g TestRG -n TestNIC --quiet

	Erwartete Ausgabe:

		info:    Executing command network nic delete
		+ Looking up the network interface "TestNIC"
		+ Deleting network interface "TestNIC"
		info:    network nic delete command OK

## Hinzufügen einer statischen privaten IP-Adresse zu einem vorhandenen virtuellen Computer
Führen Sie folgenden Befehl aus, um der Netzwerkkarte des virtuellen Computers, der mit obigem Skript erstellt wurde, eine statische private IP-Adresse hinzuzufügen:

	azure netwrok nic set -g TestRG -n TestNIC2 -a 192.168.1.101

Erwartete Ausgabe:

	info:    Executing command network nic set
	+ Looking up the network interface "TestNIC2"
	+ Updating network interface "TestNIC2"
	+ Looking up the network interface "TestNIC2"
	data:    Id                              : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC2
	data:    Name                            : TestNIC2
	data:    Type                            : Microsoft.Network/networkInterfaces
	data:    Location                        : centralus
	data:    Provisioning state              : Succeeded
	data:    MAC address                     : 00-0D-3A-90-29-25
	data:    Enable IP forwarding            : false
	data:    Virtual machine                 : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMachines/DNS01
	data:    IP configurations:
	data:      Name                          : NIC-config
	data:      Provisioning state            : Succeeded
	data:      Private IP address            : 192.168.1.101
	data:      Private IP Allocation Method  : Static
	data:      Subnet                        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
	data:
	info:    network nic set command OK

## Nächste Schritte

- Weitere Informationen zu [reservierten öffentlichen IP-Adressen](../virtual-networks-reserved-public-ip).
- Weitere Informationen zu [öffentlichen IP-Adressen auf Instanzebene (ILPIP)](../virtual-networks-instance-level-public-ip).
- Lesen Sie die Informationen zu [REST-APIs für reservierte IP-Adressen](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=Oct15_HO1-->