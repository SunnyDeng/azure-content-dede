<properties 
   pageTitle="Bereitstellen von Multi-NIC-VMs mit PowerShell im Ressourcen-Manager | Microsoft Azure"
   description="Erfahren Sie, wie Sie Multi-NIC-VMs mit PowerShell im Ressourcen-Manager bereitstellen"
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
   ms.date="11/20/2015"
   ms.author="telmos" />

#Bereitstellen von Multi-NIC-VMs mit PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-arm-selectors-include.md](../../includes/virtual-network-deploy-multinic-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-network-deploy-multinic-classic-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Da zurzeit VMs mit nur einer Netzwerkschnittstellenkarte (NIC) nicht mit VMs mit mehreren Netzwerkschnittstellenkarten in derselben Ressourcengruppe vorhanden sein können, werden die Back-End-Server in einer anderen Ressourcengruppe implementiert als die anderen Komponenten. In den folgenden Schritten verwendet die Ressourcengruppe *IaaSStory* als Hauptressourcengruppe und *IaaSStory-BackEnd* für die Back-End-Server.

## Voraussetzungen

Bevor Sie die Back-End-Server bereitstellen können, müssen Sie die Hauptressourcengruppe mit den erforderlichen Ressourcen für dieses Szenario bereitstellen. Führen Sie zum Bereitstellen dieser Ressourcen die folgenden Schritte aus:

1. Wechseln Sie zu [azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/11-MultiNIC) (in englischer Sprache).
2. Klicken Sie auf dieser Vorlagenseite rechts neben **Parent Resource group** auf **Deploy to Azure**.
3. Ändern Sie bei Bedarf die Parameterwerte, und führen Sie die folgenden Schritte im Azure-Vorschauportal aus, um die Ressourcengruppe bereitzustellen.

> [AZURE.IMPORTANT]Achten Sie darauf, eindeutige Speicherkontonamen zu verwenden. In Azure können keine doppelten Speicherkontonamen verwendet werden.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Bereitstellen der Back-End-VMs

Die Back-End-VMs sind auf die Erstellung der im Folgenden aufgelisteten Ressourcen angewiesen.

- **Speicherkonto für Datenträger**. Für eine bessere Leistung verwenden die Datenträger auf den Datenbankservern Solid State Drive (SSD)-Technik. Dafür ist ein Storage Premium-Konto erforderlich. Achten Sie darauf, dass der Azure-Speicherort für die Bereitstellung Storage Premium unterstützt.
- **NICs**. Jeder virtuelle Computer hat zwei Netzwerkkarten, eine für den Datenbankzugriff und eine für die Verwaltung.
- **Verfügbarkeitsgruppe**. Alle Datenbankserver werden einer einzigen Verfügbarkeitsgruppe hinzugefügt, damit sichergestellt ist, dass mindestens ein virtueller Computer während der Wartung ausgeführt wird.  

### Schritt 1: Starten des Skripts

Sie können das verwendete PowerShell-Skript ungekürzt [hier](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/arm/multinic.ps1) herunterladen. Gehen Sie folgendermaßen vor, um das Skript an Ihre Arbeitsumgebung anzupassen.

[AZURE.INCLUDE [powershell-preview-include.md](../../includes/powershell-preview-include.md)]

1. Ändern Sie die Werte der nachstehenden Variablen basierend auf der im obigen Abschnitt [Voraussetzungen](#Prerequisites) bereitgestellten Ressourcengruppe.

		$existingRGName        = "IaaSStory"
		$location              = "West US"
		$vnetName              = "WTestVNet"
		$backendSubnetName     = "BackEnd"
		$remoteAccessNSGName   = "NSG-RemoteAccess"
		$stdStorageAccountName = "wtestvnetstoragestd"

2. Ändern Sie die Werte der nachstehenden Variablen basierend auf den Werten, die Sie für die Back-End-Bereitstellung verwenden möchten.

		$backendRGName         = "IaaSStory-Backend"
		$prmStorageAccountName = "wtestvnetstorageprm"
		$avSetName             = "ASDB"
		$vmSize                = "Standard_DS3"
		$publisher             = "MicrosoftSQLServer"
		$offer                 = "SQL2014SP1-WS2012R2"
		$sku                   = "Standard"
		$version               = "latest"
		$vmNamePrefix          = "DB"
		$osDiskPrefix          = "osdiskdb"
		$dataDiskPrefix        = "datadisk"
		$nicNamePrefix         = "NICDB"
		$ipAddressPrefix       = "192.168.2."
		$numberOfVMs           = 2

3. Rufen Sie die vorhandenen Ressourcen ab, die für die Bereitstellung erforderlich sind.

		$vnet                  = Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $existingRGName
		$backendSubnet         = $vnet.Subnets|?{$_.Name -eq $backendSubnetName}
		$remoteAccessNSG       = Get-AzureRmNetworkSecurityGroup -Name $remoteAccessNSGName -ResourceGroupName $existingRGName
		$stdStorageAccount     = Get-AzureRmStorageAccount -Name $stdStorageAccountName -ResourceGroupName $existingRGName

### Schritt 2: Erstellen der erforderlichen Ressourcen für die virtuellen Computer

Sie müssen eine neue Ressourcengruppe, ein Speicherkonto für die Datenträger und eine Verfügbarkeitsgruppe für alle virtuellen Computer erstellen. Sie benötigen auch Anmeldeinformationen für die lokalen Administratorkonten der einzelnen virtuellen Computer. Führen Sie die folgenden Schritte aus, um diese Ressourcen zu erstellen.

1. Erstellen Sie eine neue Ressourcengruppe.

		New-AzureRmResourceGroup -Name $backendRGName -Location $location

2. Erstellen Sie ein neues Storage Premium-Konto in der oben erstellten Ressourcengruppe.

		$prmStorageAccount = New-AzureRmStorageAccount -Name $prmStorageAccountName `
			-ResourceGroupName $backendRGName -Type Premium_LRS -Location $location

3. Erstellen Sie eine neue Verfügbarkeitsgruppe.

		$avSet = New-AzureRmAvailabilitySet -Name $avSetName -ResourceGroupName $backendRGName -Location $location

4. Ermitteln Sie Anmeldeinformationen für die lokalen Administratorkonten der einzelnen virtuellen Computer.

		$cred = Get-Credential -Message "Type the name and password for the local administrator account."

### Schritt 3: Erstellen der Netzwerkschnittstellenkarten und Back-End-VMs

Sie müssen mithilfe einer Schleife die gewünschte Anzahl virtueller Computer und auch die erforderlichen NICs und VMs erstellen. Führen Sie zum Erstellen der Netzwerkkarten und virtuellen Computer die folgenden Schritte aus.

1. Starten Sie eine `for`-Schleife, um die Befehle zum Erstellen eines virtuellen Computers und zweier Netzwerkkarten auf Grundlage der Variablen `$numberOfVMs` so oft wie erforderlich zu wiederholen.

		for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Erstellen Sie die Netzwerkkarte für den Datenbankzugriff.
		
		    $nic1Name = $nicNamePrefix + $suffixNumber + "-DA"
		    $ipAddress1 = $ipAddressPrefix + ($suffixNumber + 3)
		    $nic1 = New-AzureRmNetworkInterface -Name $nic1Name -ResourceGroupName $backendRGName `
				-Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress1

3. Erstellen Sie die Netzwerkkarte für den Remotezugriff. Beachten Sie, dass dieser NIC eine NSG zugeordnet ist.

		    $nic2Name = $nicNamePrefix + $suffixNumber + "-RA"
		    $ipAddress2 = $ipAddressPrefix + (53 + $suffixNumber)
		    $nic2 = New-AzureRmNetworkInterface -Name $nic2Name -ResourceGroupName $backendRGName `
				-Location $location -SubnetId $backendSubnet.Id -PrivateIpAddress $ipAddress2 `
				-NetworkSecurityGroupId $remoteAccessNSG.Id

4. Erstellen Sie ein `vmConfig`-Objekt.

		    $vmName = $vmNamePrefix + $suffixNumber
		    $vmConfig = New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avSet.Id

5. Erstellen Sie zwei Datenträger pro virtuellem Computer. Beachten Sie, dass die Datenträger sich in dem zuvor erstellten Storage Premium-Konto befinden.

		    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
		    $data1VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk1Name + ".vhd"
		    Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk1Name -DiskSizeInGB $diskSize `
				-VhdUri $data1VhdUri -CreateOption empty -Lun 0
		
		    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"    
		    $data2VhdUri = $prmStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $dataDisk2Name + ".vhd"
		    Add-AzureRmVMDataDisk -VM $vmConfig -Name $dataDisk2Name -DiskSizeInGB $diskSize `
				-VhdUri $data2VhdUri -CreateOption empty -Lun 1

6. Konfigurieren Sie das Betriebssystem und das Image für den virtuellen Computer.
		    
		    $vmConfig = Set-AzureRmVMOperatingSystem -VM $vmConfig -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
		    $vmConfig = Set-AzureRmVMSourceImage -VM $vmConfig -PublisherName $publisher -Offer $offer -Skus $sku -Version $version

7. Fügen Sie die beiden zuvor erstellten NICs dem `vmConfig`-Objekt hinzu.

		    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic1.Id -Primary
		    $vmConfig = Add-AzureRmVMNetworkInterface -VM $vmConfig -Id $nic2.Id

8. Erstellen Sie den Betriebssystemdatenträger und die VM. Beachten Sie das Zeichen `}` zum Beenden der `for`-Schleife.

		    $osDiskName = $vmName + "-" + $osDiskSuffix
		    $osVhdUri = $stdStorageAccount.PrimaryEndpoints.Blob.ToString() + "vhds/" + $osDiskName + ".vhd"
		    $vmConfig = Set-AzureRmVMOSDisk -VM $vmConfig -Name $osDiskName -VhdUri $osVhdUri -CreateOption fromImage
		    New-AzureRmVM -VM $vmConfig -ResourceGroupName $backendRGName -Location $location
		}

### Schritt 4: Ausführen des Skripts

Führen Sie das Skript aus, nachdem sie es heruntergeladen und angepasst haben, um die Back-End-VMs mit mehreren Netzwerkkarten zu erstellen.

1. Speichern Sie Ihr Skript, und führen Sie es an der **PowerShell**-Eingabeaufforderung oder in **PowerShell ISE** aus. Anfänglich wird die folgende Ausgabe angezeigt.

		ResourceGroupName : IaaSStory-Backend
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		Permissions       : 
		                    Actions  NotActions
		                    =======  ==========
		                    *                  
		                    
		ResourceId        : /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/IaaSStory-Backend

2. Tragen Sie, wenn Sie nach einigen Minuten dazu aufgefordert werden, die Anmeldeinformationen ein, und klicken Sie auf **OK**. Die folgende Ausgabe stellt einen einzelnen virtuellen Computer dar. Beachten Sie, dass der gesamte Vorgang in etwa 8 Minuten abgeschlossen wurde.

		ResourceGroupName            : 
		Id                           : 
		Name                         : DB2
		Type                         : 
		Location                     : 
		Tags                         : 
		TagsText                     : null
		AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
		AvailabilitySetReferenceText : {
		                                 "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
		                               Microsoft.Compute/availabilitySets/ASDB"
		                               }
		Extensions                   : 
		ExtensionsText               : null
		HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
		HardwareProfileText          : {
		                                 "VirtualMachineSize": "Standard_DS3"
		                               }
		InstanceView                 : 
		InstanceViewText             : null
		NetworkProfile               : 
		NetworkProfileText           : null
		OSProfile                    : 
		OSProfileText                : null
		Plan                         : 
		PlanText                     : null
		ProvisioningState            : 
		StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
		StorageProfileText           : {
		                                 "DataDisks": [
		                                   {
		                                     "Lun": 0,
		                                     "Caching": null,
		                                     "CreateOption": "empty",
		                                     "DiskSizeGB": 127,
		                                     "Name": "DB2-datadisk-1",
		                                     "SourceImage": null,
		                                     "VirtualHardDisk": {
		                                       "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
		                                     }
		                                   }
		                                 ],
		                                 "ImageReference": null,
		                                 "OSDisk": null
		                               }
		DataDiskNames                : {DB2-datadisk-1}
		NetworkInterfaceIDs          : 
		RequestId                    : 
		StatusCode                   : 0
		
		
		ResourceGroupName            : 
		Id                           : 
		Name                         : DB2
		Type                         : 
		Location                     : 
		Tags                         : 
		TagsText                     : null
		AvailabilitySetReference     : Microsoft.Azure.Management.Compute.Models.AvailabilitySetReference
		AvailabilitySetReferenceText : {
		                                 "ReferenceUri": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/IaaSStory-Backend/providers/
		                               Microsoft.Compute/availabilitySets/ASDB"
		                               }
		Extensions                   : 
		ExtensionsText               : null
		HardwareProfile              : Microsoft.Azure.Management.Compute.Models.HardwareProfile
		HardwareProfileText          : {
		                                 "VirtualMachineSize": "Standard_DS3"
		                               }
		InstanceView                 : 
		InstanceViewText             : null
		NetworkProfile               : 
		NetworkProfileText           : null
		OSProfile                    : 
		OSProfileText                : null
		Plan                         : 
		PlanText                     : null
		ProvisioningState            : 
		StorageProfile               : Microsoft.Azure.Management.Compute.Models.StorageProfile
		StorageProfileText           : {
		                                 "DataDisks": [
		                                   {
		                                     "Lun": 0,
		                                     "Caching": null,
		                                     "CreateOption": "empty",
		                                     "DiskSizeGB": 127,
		                                     "Name": "DB2-datadisk-1",
		                                     "SourceImage": null,
		                                     "VirtualHardDisk": {
		                                       "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-1.vhd"
		                                     }
		                                   },
		                                   {
		                                     "Lun": 1,
		                                     "Caching": null,
		                                     "CreateOption": "empty",
		                                     "DiskSizeGB": 127,
		                                     "Name": "DB2-datadisk-2",
		                                     "SourceImage": null,
		                                     "VirtualHardDisk": {
		                                       "Uri": "https://wtestvnetstorageprm.blob.core.windows.net/vhds/DB2-datadisk-2.vhd"
		                                     }
		                                   }
		                                 ],
		                                 "ImageReference": null,
		                                 "OSDisk": null
		                               }
		DataDiskNames                : {DB2-datadisk-1, DB2-datadisk-2}
		NetworkInterfaceIDs          : 
		RequestId                    : 
		StatusCode                   : 0
		
		
		EndTime             : 10/30/2015 9:30:03 AM -08:00
		Error               : 
		Output              : 
		StartTime           : 10/30/2015 9:22:54 AM -08:00
		Status              : Succeeded
		TrackingOperationId : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		RequestId           : xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
		StatusCode          : OK

<!---HONumber=AcomDC_1125_2015-->