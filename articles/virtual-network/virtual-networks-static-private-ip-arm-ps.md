<properties 
   pageTitle="Einrichten einer statischen privaten IP-Adresse im ARM-Modus mithilfe von PowerShell | Microsoft Azure"
   description="Grundlegendes zu statischen IP-Adressen (DIPs) und zur Verwaltung dieser IP-Adressen im ARM-Modus mithilfe von PowerShell"
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

# Einrichten einer statischen privaten IP-Adresse in PowerShell

[AZURE.INCLUDE [virtual-networks-static-private-ip-selectors-arm-include](../../includes/virtual-networks-static-private-ip-selectors-arm-include.md)]

[AZURE.INCLUDE [virtual-networks-static-private-ip-intro-include](../../includes/virtual-networks-static-private-ip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das Ressourcen-Manager-Bereitstellungsmodell. Sie können [eine statische private IP-Adresse auch im klassischen Bereitstellungsmodell verwalten](virtual-networks-static-private-ip-classic-ps.md).

[AZURE.INCLUDE [virtual-networks-static-ip-scenario-include](../../includes/virtual-networks-static-ip-scenario-include.md)]

Die folgenden Beispielbefehle für PowerShell setzen voraus, dass bereits eine einfache Umgebung erstellt wurde, die auf dem zuvor beschriebenen Szenario basiert. Wenn Sie die in diesem Dokument aufgeführten Befehle ohne Veränderungen ausführen möchten, erstellen Sie zunächst eine Testumgebung, wie unter [Erstellen eines VNets](virtual-networks-create-vnet-arm-ps.md) beschrieben.

## Angeben einer statischen privaten IP-Adresse beim Erstellen eines virtuellen Computers
Erstellen Sie in einem VNet mit dem Namen *TestVNet* im Subnetz *FrontEnd* einen virtuellen Computer mit dem Namen *DNS01* und der statischen privaten IP-Adresse *192.168.1.101*. Gehen Sie dabei wie folgt vor:

1. Wenn Sie Azure PowerShell zuvor noch nicht verwendet haben, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md), und befolgen Sie die komplette Anleitung, um sich bei Azure anzumelden und Ihr Abonnement auszuwählen.
2. Führen Sie an einer Azure PowerShell-Eingabeaufforderung das Cmdlet **Switch-AzureMode** aus, um zum Ressourcen-Manager-Modus zu wechseln, wie unten dargestellt.

		Switch-AzureMode AzureResourceManager
	
	Erwartete Ausgabe:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]Das Cmdlet "Switch-AzureMode" ist demnächst veraltet. In diesem Fall werden alle Ressourcen-Manager-Cmdlets umbenannt.
1. Legen Sie die Variablen für das Speicherkonto, den Speicherort, die Ressourcengruppe und die zu verwendenden Anmeldeinformationen fest. Sie müssen einen Benutzernamen und ein Kennwort für den virtuellen Computer eingeben. Die Speichergruppe und die Ressourcengruppe müssen bereits vorhanden sein.

		$stName = "vnetstorage"
		$locName = "Central US"
		$rgName = "TestRG"
	    $cred = Get-Credential -Message "Type the name and password of the local administrator account."

3. Rufen Sie das virtuelle Netzwerk und das Subnetz an, in dem der virtuelle Computer erstellt werden soll.

	    $vnet = Get-AzureVirtualNetwork -ResourceGroupName TestRG -Name TestVNet	
	    $subnet = $vnet.Subnets[0].Id

4. Erstellen Sie, sofern erforderlich, eine öffentliche IP-Adresse für den Zugriff auf den virtuellen Computer aus dem Internet.

		$pip = New-AzurePublicIpAddress -Name TestPIP -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic

5. Erstellen Sie eine Netzwerkkarte, und verwenden Sie dabei die statische private IP-Adresse, die Sie dem virtuellen Computer zuweisen möchten. Stellen Sie sicher, dass die IP-Adresse innerhalb des Subnetzbereichs liegt, zu dem Sie den virtuellen Computer hinzufügen. Dies ist der wesentliche Schritt für diesen Artikel, bei dem Sie die private IP-Adresse als statische IP-Adresse festlegen.

		$nic = New-AzureNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id -PrivateIpAddress 192.168.1.101

6. Erstellen Sie den virtuellen Computer mit der zuvor erstellten Netzwerkkarte.

		$vm = New-AzureVMConfig -VMName DNS01 -VMSize "Standard_A1"
		$vm = Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName DNS01  -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		$vm = Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
		$vm = Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
		$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
		$vm = Set-AzureVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
		New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm 

	Erwartete Ausgabe:

		EndTime             : 9/8/2015 2:32:09 PM -07:00
		Error               : 
		Output              : 
		StartTime           : 9/8/2015 2:27:42 PM -07:00
		Status              : Succeeded
		TrackingOperationId : 249e4cc9-1257-42a7-a2fd-6962243c5b6d
		RequestId           : 8dd60f6d-6453-49c4-8f6a-d3c6356dd2a4
		StatusCode          : OK 


## Abrufen der Informationen zur statischen privaten IP-Adresse für einen virtuellen Computer
Führen Sie den folgenden PowerShell-Befehl aus, und sehen Sie sich die Werte für *PrivateIpAddress* und *PrivateIpAllocationMethod* an, um Informationen zur statischen privaten IP-Adresse des virtuellen Computers zu erhalten, der mit dem obigen Skript erstellt wurde:

	Get-AzureNetworkInterface -Name TestNIC -ResourceGroupName TestRG

Erwartete Ausgabe:

	Name                 : TestNIC
	ResourceGroupName    : TestRG
	Location             : centralus
	Id                   : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/Te
	                       stNIC
	Etag                 : W/"ee300945-3bb1-4eda-bc90-3a6d65fad60a"
	ProvisioningState    : Succeeded
	Tags                 : 
	VirtualMachine       : {
	                         "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMach
	                       ines/DNS01"
	                       }
	IpConfigurations     : [
	                         {
	                           "Name": "ipconfig1",
	                           "Etag": "W/"ee300945-3bb1-4eda-bc90-3a6d65fad60a"",
	                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkIn
	                       terfaces/TestNIC/ipConfigurations/ipconfig1",
	                           "PrivateIpAddress": "192.168.1.101",
	                           "PrivateIpAllocationMethod": "Static",
	                           "Subnet": {
	                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtual
	                       Networks/TestVNet/subnets/FrontEnd"
	                           },
	                           "PublicIpAddress": {
	                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicI
	                       PAddresses/TestPIP"
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
	EnableIPForwarding   : False
	NetworkSecurityGroup : null
	Primary              : True

## Entfernen einer statischen privaten IP-Adresse von einem virtuellen Computer
Führen Sie die folgenden PowerShell-Befehle aus, um die statische private IP-Adresse zu entfernen, die dem virtuellen Computer im obigen Skript hinzugefügt wurde:
	
	$nic=Get-AzureNetworkInterface -Name TestNIC -ResourceGroupName TestRG
	$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Dynamic"
	Set-AzureNetworkInterface -NetworkInterface $nic

Erwartete Ausgabe:

	Name                 : TestNIC
	ResourceGroupName    : TestRG
	Location             : centralus
	Id                   : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/Te
	                       stNIC
	Etag                 : W/"45ccb5a5-934e-485f-b2fd-e4a014131032"
	ProvisioningState    : Succeeded
	Tags                 : 
	VirtualMachine       : {
	                         "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Compute/virtualMach
	                       ines/WindowsVM"
	                       }
	IpConfigurations     : [
	                         {
	                           "Name": "ipconfig1",
	                           "Etag": "W/"45ccb5a5-934e-485f-b2fd-e4a014131032"",
	                           "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/networkIn
	                       terfaces/TestNIC/ipConfigurations/ipconfig1",
	                           "PrivateIpAddress": "192.168.1.101",
	                           "PrivateIpAllocationMethod": "Dynamic",
	                           "Subnet": {
	                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/virtual
	                       Networks/TestVNet/subnets/FrontEnd"
	                           },
	                           "PublicIpAddress": {
	                             "Id": "/subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/TestRG/providers/Microsoft.Network/publicI
	                       PAddresses/TestPIP"
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
	EnableIPForwarding   : False
	NetworkSecurityGroup : null
	Primary              : True

## Hinzufügen einer statischen privaten IP-Adresse zu einem vorhandenen virtuellen Computer
Führen Sie folgenden Befehl aus, um dem virtuellen Computer, der mit dem obigen Skript erstellt wurde, eine statische private IP-Adresse hinzuzufügen:

	$nic=Get-AzureNetworkInterface -Name TestNIC -ResourceGroupName TestRG
	$nic.IpConfigurations[0].PrivateIpAllocationMethod = "Static"
	$nic.IpConfigurations[0].PrivateIpAddress = "192.168.1.101"
	Set-AzureNetworkInterface -NetworkInterface $nic

## Nächste Schritte

- Weitere Informationen zu [reservierten öffentlichen IP-Adressen](../virtual-networks-reserved-public-ip).
- Weitere Informationen zu [öffentlichen IP-Adressen auf Instanzebene (ILPIP)](../virtual-networks-instance-level-public-ip).
- Lesen Sie die Informationen zu [REST-APIs für reservierte IP-Adressen](https://msdn.microsoft.com/library/azure/dn722420.aspx).

<!---HONumber=Sept15_HO3-->