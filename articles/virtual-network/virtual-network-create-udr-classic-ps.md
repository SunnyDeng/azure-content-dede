<properties 
   pageTitle="Steuern des Routings und Verwenden virtueller Geräte mithilfe von PowerShell im klassischen Bereitstellungsmodell | Microsoft Azure"
   description="Erfahren Sie, wie Sie das Routing in VNets mithilfe von PowerShell im klassischen Bereitstellungsmodell steuern."
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
   ms.date="02/02/2016"
   ms.author="telmos" />

#Steuern des Routings und Verwenden virtueller Geräte (klassisch) mithilfe von PowerShell

[AZURE.INCLUDE [virtual-network-create-udr-classic-selectors-include.md](../../includes/virtual-network-create-udr-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das klassische Bereitstellungsmodell.

[AZURE.INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Die folgenden Beispielbefehle für Azure PowerShell setzen voraus, dass bereits eine einfache Umgebung erstellt wurde, die auf dem zuvor beschriebenen Szenario basiert. Wenn Sie die in diesem Dokument aufgeführten Befehle ohne Veränderungen ausführen möchten, erstellen Sie zunächst die unter [Erstellen eines VNets (klassisch) mithilfe von PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md) beschriebene Umgebung.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Erstellen der benutzerdefinierten Route für das Front-End-Subnetz
Führen Sie zum Erstellen der Routingtabelle und der für das Front-End-Subnetz erforderlichen Route anhand des oben beschriebenen Szenarios die folgenden Schritte aus.

3. Führen Sie das Cmdlet **`New-AzureRouteTable`** aus, um eine Routingtabelle für das Front-End-Subnetz zu erstellen.

		New-AzureRouteTable -Name UDR-FrontEnd `
			-Location uswest `
			-Label "Route table for front end subnet"

	Ausgabe:

		Name         Location   Label                          
		----         --------   -----                          
		UDR-FrontEnd West US    Route table for front end subnet

4. Führen Sie das Cmdlet **`Set-AzureRoute`** aus, um in der oben erstellten Routingtabelle eine Route zu erstellen, die sämtlichen an das Back-End-Subnetz (192.168.2.0/24) gerichteten Datenverkehr an den virtuellen Computer **FW1** (192.168.0.4) umleitet.
	
		Get-AzureRouteTable UDR-FrontEnd `
			|Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
			-NextHopType VirtualAppliance `
			-NextHopIpAddress 192.168.0.4

	Ausgabe:

		Name     : UDR-FrontEnd
		Location : West US
		Label    : Route table for frontend subnet
		Routes   : 
		           Name                 Address Prefix    Next hop type        Next hop IP address
		           ----                 --------------    -------------        -------------------
		           RouteToBackEnd       192.168.2.0/24    VirtualAppliance     192.168.0.4  

5. Führen Sie das Cmdlet **`Set-AzureSubnetRouteTable`** aus, um die oben erstellte Routingtabelle dem Subnetz **FrontEnd** zuzuordnen.

		Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
			-SubnetName FrontEnd `
			-RouteTableName UDR-FrontEnd
 
## Erstellen der benutzerdefinierten Route für das Back-End-Subnetz
Führen Sie zum Erstellen der Routingtabelle und der für das Back-End-Subnetz erforderlichen Route anhand des oben beschriebenen Szenarios die folgenden Schritte aus.

3. Führen Sie das Cmdlet **`New-AzureRouteTable`** aus, um eine Routingtabelle für das Back-End-Subnetz zu erstellen.

		New-AzureRouteTable -Name UDR-BackEnd `
			-Location uswest `
			-Label "Route table for back end subnet"

4. Führen Sie das Cmdlet **`Set-AzureRoute`** aus, um in der oben erstellten Routingtabelle eine Route zu erstellen, die sämtlichen an das Front-End-Subnetz (192.168.1.0/24) gerichteten Datenverkehr an den virtuellen Computer **FW1** (192.168.0.4) umleitet.

		Get-AzureRouteTable UDR-BackEnd `
			|Set-AzureRoute -RouteName RouteToFrontEnd -AddressPrefix 192.168.1.0/24 `
			-NextHopType VirtualAppliance `
			-NextHopIpAddress 192.168.0.4

5. Führen Sie das Cmdlet **`Set-AzureSubnetRouteTable`** aus, um die oben erstellte Routingtabelle dem Subnetz **BackEnd** zuzuordnen.

		Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
			-SubnetName FrontEnd `
			-RouteTableName UDR-FrontEnd

## Aktivieren der IP-Weiterleitung auf dem virtuellen Computer „FW1“
Führen Sie zum Aktivieren der IP-Weiterleitung auf dem virtuellen Computer "FW1" die folgenden Schritte aus.

1. Führen Sie das Cmdlet **`Get-AzureIPForwarding`** aus, um den Status der IP-Weiterleitung zu überprüfen.

		Get-AzureVM -Name FW1 -ServiceName TestRGFW `
			| Get-AzureIPForwarding

	Ausgabe:

		Disabled

2. Führen Sie das Cmdlet **`Set-AzureIPForwarding`** aus, um die IP-Weiterleitung für den virtuellen Computer *FW1* zu aktivieren.

		Get-AzureVM -Name FW1 -ServiceName TestRGFW `
			| Set-AzureIPForwarding -Enable

<!---HONumber=AcomDC_0211_2016-->