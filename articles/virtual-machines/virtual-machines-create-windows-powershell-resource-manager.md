<properties
	pageTitle="Erstellen eines virtuellen Windows-Computers mit dem Azure Resource Manager und PowerShell"
	description="Verwenden Sie den Resource Management-Modus von Azure PowerShell, um ganz leicht einen neuen virtuellen Windows-Computer zu erstellen."
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="06/02/2015"
	ms.author="davidmu"/>

# Erstellen eines virtuellen Windows-Computers mit dem Azure Resource Manager und PowerShell

Dieses Thema beschreibt das schnelle Erstellen und Verwalten eines virtuellen Windows Azure-Computers mithilfe von Azure-Ressourcen-Manager und PowerShell.

[AZURE.INCLUDE [resource-manager-pointer-to-service-management](../../includes/resource-manager-pointer-to-service-management.md)]

- [Erstellen eines virtuellen Windows-Computers mit PowerShell und Azure-Dienstverwaltung](virtual-machines-create-windows-powershell-service-manager.md)

## Erstellen des virtuellen Windows-Computers

Wenn Sie Azure PowerShell bereits installiert haben, benötigen Sie Version 0.9.0 oder höher. Sie können die von Ihnen installierte Azure Power Shell-Version mit diesem Befehl über die Azure PowerShell-Eingabeaufforderung prüfen.

	Get-Module azure | format-table version

Wenn Sie dies noch nicht getan haben oder die installierte Version von Azure PowerShell aktualisieren müssen, verwenden Sie die Anweisungen unter [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell](install-configure-powershell.md), um Azure PowerShell auf Ihrem lokalen Computer zu installieren. Öffnen Sie dann eine Azure PowerShell-Befehlseingabeaufforderung.

Zunächst müssen Sie sich mit dem folgenden Befehl in Azure anmelden.

	Add-AzureAccount

Geben Sie die E-Mail-Adresse Ihres Azure-Kontos und das Kennwort in das Anmeldedialogfeld von Microsoft Azure ein.

Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie als Nächstes Ihr Azure-Abonnement festlegen. Führen Sie den folgenden Befehl aus, um eine Liste Ihrer aktuellen Abonnements anzuzeigen.

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

Ersetzen Sie nun alles innerhalb der Anführungszeichen einschließlich der Zeichen < and > durch den korrekten Abonnementnamen und führen Sie die Befehle aus.

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

Als Nächstes müssen Sie ein Speicherkonto erstellen. Sie müssen einen eindeutigen Namen auswählen, der nur Kleinbuchstaben und Zahlen enthält. Mit diesem Befehl können Sie den Speicherkontonamen auf Eindeutigkeit testen.

	Test-AzureName -Storage <Proposed storage account name>

Wenn dieser Befehl „False“ zurückgibt, ist der vorgeschlagene Name eindeutig.

Sie müssen das Azure-Rechenzentrum angeben. Führen Sie diesen Befehl aus, um eine Liste der Azure-Rechenzentren zu erhalten.

	Get-AzureLocation | sort Name | Select Name

Als Nächstes müssen Sie den Modus von Azure PowerShell auf Resource Manager wechseln. Führen Sie diesen Befehl aus.

	Switch-AzureMode AzureResourceManager

Kopieren Sie nun den folgenden PowerShell-Befehlssatz in einen Texteditor wie Notepad. Geben Sie Ihr gewähltes Speicherkonto und den Ort ein, und ersetzen Sie alles innerhalb der Anführungszeichen, einschließlich der Zeichen < and >.

	$stName="<chosen storage account name>"
	$locName="<chosen Azure location name>"
	$rgName="TestRG"
	New-AzureResourceGroup -Name $rgName -Location $locName
	$storageAcc=New-AzureStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
	$singleSubnet=New-AzureVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
	$vnet=New-AzurevirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
	$pip = New-AzurePublicIpAddress -Name TestNIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	$cred = Get-Credential -Message "Type the name and password of the local administrator account."
	$vm = New-AzureVMConfig -VMName WindowsVM -VMSize "Standard_A1"
	$vm = Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm = Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	$vm = Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	$osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/WindowsVMosDisk.vhd"
	$vm = Set-AzureVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

Kopieren Sie obigen Befehlssatz in die Zwischenablage, und klicken Sie dann mit der rechten Maustaste auf Ihre offene Azure PowerShell-Eingabeaufforderung. Dadurch wird der Befehlssatz als Reihe an PowerShell-Befehlen ausgegeben. Geben Sie den Namen und das Kennwort für das lokale Administratorkonto ein und erstellen Ihren virtuellen Azure-Computer.

Es erscheint beispielsweise Folgendes:

	PS C:\> $stName="contosost"
	PS C:\> $locName="West US"
	PS C:\> $rgName="TestRG"
	PS C:\> New-AzureResourceGroup -Name $rgName -Location $locName
	VERBOSE: 12:45:15 PM - Created resource group 'TestRG' in location 'westus'


	ResourceGroupName : TestRG
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
	                    Actions  NotActions
	                    =======  ==========
	                    *

	ResourceId        : /subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/TestRG


	PS C:\> $storageAcc=New-AzureStorageAccount -ResourceGroupName $rgName -Name $stName -Type "Standard_GRS" -Location $locName
	PS C:\> $singleSubnet=New-AzureVirtualNetworkSubnetConfig -Name singleSubnet -AddressPrefix 10.0.0.0/24
	PS C:\> $vnet=New-AzurevirtualNetwork -Name TestNet3 -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $singleSubnet
	PS C:\> $pip = New-AzurePublicIpAddress -Name TestNIC -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	PS C:\> $nic = New-AzureNetworkInterface -Name TestNIC -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[0].Id -PublicIpAddressId $pip.Id
	PS C:\> $cred = Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:\> $vm = New-AzureVMConfig -VMName WindowsVM -VMSize "Standard_A1"
	PS C:\> $vm = Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName MyWindowsVM -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	PS C:\> $vm = Set-AzureVMSourceImage -VM $vm -PublisherName MicrosoftWindowsServer -Offer WindowsServer -Skus 2012-R2-Datacenter -Version "latest"
	PS C:\> $vm = Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id
	PS C:\> $osDiskUri = $storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/MyWindowsVMosDisk.vhd"
	PS C:\> $vm = Set-AzureVMOSDisk -VM $vm -Name "windowsvmosdisk" -VhdUri $osDiskUri -CreateOption fromImage
	PS C:\> New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm


	EndTime             : 4/28/2015 1:00:05 PM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 12:52:52 PM -07:00
	Status              : Succeeded
	TrackingOperationId : 45035a90-ea12-4e1e-87e7-2a5e9ed12c93
	RequestId           : 98c7b4fb-b26e-4a58-b17a-b0983d896aae
	StatusCode          : OK

## Zusätzliche Ressourcen

[Azure Compute-, Network- and Storage-Anbieter unter dem Azure-Ressourcen-Manager](virtual-machines-azurerm-versus-azuresm.md)

[Übersicht über den Azure Resource Manager](resource-group-overview.md)

[Erstellen eines virtuellen Windows-Computers mit einer Resource Manager-Vorlage und PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple.md)

[Erstellen eines virtuellen Windows-Computers mit PowerShell und Azure-Dienstverwaltung](virtual-machines-create-windows-powershell-service-manager.md)

[Dokumentation zu virtuellen Computern](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Installieren und Konfigurieren von Azure PowerShell](install-configure-powershell.md)

<!---HONumber=July15_HO4-->