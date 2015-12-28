<properties 
   pageTitle="Steuern des Routings und Verwenden virtueller Geräte mithilfe der Azure-Befehlszeilenschnittstelle im klassischen Bereitstellungsmodell | Microsoft Azure"
   description="Erfahren Sie, wie Sie das Routing in VNets mithilfe der Azure-Befehlszeilenschnittstelle im klassischen Bereitstellungsmodell steuern."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-service-management"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/11/2015"
   ms.author="telmos" />

#Steuern des Routings und Verwenden virtueller Geräte (klassisch) mithilfe der Azure-Befehlszeilenschnittstelle

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das klassische Bereitstellungsmodell. Sie können auch [im Ressourcen-Manager-Bereitstellungsmodell das Routing steuern und virtuelle Geräte verwenden](virtual-network-create-udr-arm-cli.md).

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Die folgenden Beispielbefehle für die Azure-Befehlszeilenschnittstelle setzen voraus, dass bereits eine einfache Umgebung erstellt wurde, die auf dem obigen Szenario basiert. Wenn Sie die in diesem Dokument aufgeführten Befehle ohne Veränderungen ausführen möchten, erstellen Sie zunächst die unter [Erstellen eines VNets (klassisch) mithilfe der Azure-Befehlszeilenschnittstelle](virtual-networks-create-vnet-classic-cli.md) beschriebene Umgebung.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Erstellen der benutzerdefinierten Route für das Front-End-Subnetz
Führen Sie zum Erstellen der Routingtabelle und der für das Front-End-Subnetz erforderlichen Route anhand des oben beschriebenen Szenarios die folgenden Schritte aus.

1. Führen Sie **`azure config mode`** aus, um in den klassischen Modus zu wechseln.

		azure config mode asm

	Ausgabe:

		info:    New mode is asm

3. Führen Sie den Befehl **`azure network route-table create`** aus, um eine Routingtabelle für das Front-End-Subnetz zu erstellen.

		azure network route-table create -n UDR-FrontEnd -l uswest

	Ausgabe:

		info:    Executing command network route-table create
		info:    Creating route table "UDR-FrontEnd"
		info:    Getting route table "UDR-FrontEnd"
		data:    Name                            : UDR-FrontEnd
		data:    Location                        : West US
		info:    network route-table create command OK

	Parameter: - **-l (oder --location)**. Azure-Region, in der die neue NSG erstellt wird. In diesem Szenario *westus*. - **-n (oder --name)**. Name der neuen NSG. In diesem Szenario *NSG-FrontEnd*.

4. Führen Sie den Befehl **`azure network route-table route set`** aus, um in der oben erstellten Routingtabelle eine Route zu erstellen, die sämtlichen an das Back-End-Subnetz (192.168.2.0/24) gerichteten Datenverkehr an den virtuellen Computer **FW1** (192.168.0.4) umleitet.

		azure network route-table route set -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -t VirtualAppliance -p 192.168.0.4

	Ausgabe:

		info:    Executing command network route-table route set
		info:    Getting route table "UDR-FrontEnd"
		info:    Setting route "RouteToBackEnd" in a route table "UDR-FrontEnd"
		info:    network route-table route set command OK

	Parameter: - **-r (oder --route-table-name)**. Der Name der Routingtabelle, der die Route hinzugefügt wird. In diesem Szenario *UDR-FrontEnd*. - **-a (oder --address-prefix)**. Das Adresspräfix für das Zielsubnetz der Pakete. In diesem Szenario *192.168.2.0/24*. - **-t (oder --next-hop-type)**. Der Typ des Zielobjekts für den Datenverkehr. Mögliche Werte sind *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* oder *None*. - **-p (oder --next-hop-ip-address**). Die IP-Adresse für den nächsten Hop. In diesem Szenario *192.168.0.4*.

5. Führen Sie den Befehl **`azure network vnet subnet route-table add`** aus, um die oben erstellte Routingtabelle dem Subnetz **FrontEnd** zuzuordnen.

		azure network vnet subnet route-table add -t TestVNet -n FrontEnd -r UDR-FrontEnd

	Ausgabe:

		info:    Executing command network vnet subnet route-table add
		info:    Looking up the subnet "FrontEnd"
		info:    Looking up network configuration
		info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
		info:    Associating route table "UDR-FrontEnd" and subnet "FrontEnd"
		info:    Looking up network gateway route tables in virtual network "TestVNet" subnet "FrontEnd"
		data:    Route table name                : UDR-FrontEnd
		data:      Location                      : West US
		data:      Routes:
		info:    network vnet subnet route-table add command OK	

	Parameter: - **-t (oder --vnet-name)**. Name des VNets mit dem Subnetz. In diesem Szenario *TestVNet*. - **-n (oder --subnet-name**. Der Name des Subnetzes, dem die Routingtabelle hinzugefügt wird. In diesem Szenario *FrontEnd*.
 
## Erstellen der benutzerdefinierten Route für das Back-End-Subnetz
Führen Sie zum Erstellen der Routingtabelle und der für das Back-End-Subnetz erforderlichen Route anhand des oben beschriebenen Szenarios die folgenden Schritte aus.

3. Führen Sie den Befehl **`azure network route-table create`** aus, um eine Routingtabelle für das Back-End-Subnetz zu erstellen.

		azure network route-table create -n UDR-BackEnd -l uswest

4. Führen Sie den Befehl **`azure network route-table route set`** aus, um in der oben erstellten Routingtabelle eine Route zu erstellen, die sämtlichen an das Front-End-Subnetz (192.168.1.0/24) gerichteten Datenverkehr an den virtuellen Computer **FW1** (192.168.0.4) umleitet.

		azure network route-table route set -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -t VirtualAppliance -p 192.168.0.4

5. Führen Sie den Befehl **`azure network vnet subnet route-table add`** aus, um die oben erstellte Routingtabelle dem Subnetz **BackEnd** zuzuordnen.

		azure network vnet subnet route-table add -t TestVNet -n BackEnd -r UDR-BackEnd

<!---HONumber=AcomDC_1217_2015-->