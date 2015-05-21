<properties 
	pageTitle="Erstellen eines virtuellen Windows-Computers mit PowerShell und Azure Service Manager" 
	description="Verwenden Sie Azure PowerShell, um schnell einen neuen virtuellen Computer für Windows zu erstellen." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="04/29/2015" 
	ms.author="josephd"/>

# Erstellen eines virtuellen Windows-Computers mit PowerShell und Azure Service Manager

Wenn Sie bereits Azure PowerShell installiert haben, benötigen Sie Version 0.8.0 oder höher. Sie können die von Ihnen installierte Azure Power Shell-Version mit diesem Befehl über die Azure PowerShell-Eingabeaufforderung prüfen.

	Get-Module azure | format-table version

Wenn Sie dies noch nicht getan haben, verwenden Sie die Anweisungen unter [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell](install-configure-powershell.md), um Azure PowerShell auf Ihrem lokalen Computer zu installieren. Öffnen Sie dann eine Azure PowerShell-Befehlseingabeaufforderung.

Zunächst müssen Sie sich mit dem folgenden Befehl in Azure anmelden.

	Add-AzureAccount

Geben Sie die E-Mail-Adresse Ihres Azure-Kontos und das Kennwort in das Anmeldedialogfeld von Microsoft Azure ein.

Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie als Nächstes Ihr Azure-Abonnement festlegen. Führen Sie den folgenden Befehl aus, um eine Liste Ihrer aktuellen Abonnements anzuzeigen.

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

Ersetzen Sie nun alles innerhalb der Anführungszeichen einschließlich der Zeichen < and > durch den korrekten Abonnementnamen und führen Sie die Befehle aus.

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

Als Nächstes benötigen Sie ein Speicherkonto. Sie können die aktuelle Liste der Speicherkonten mit diesem Befehl anzeigen.

	Get-AzureStorageAccount | sort Label | Select Label

Falls Sie noch keines haben, erstellen Sie ein neues Speicherkonto. Sie müssen einen eindeutigen Namen auswählen, der nur Kleinbuchstaben und Zahlen enthält. Mit diesem Befehl können Sie den Speicherkontonamen auf Eindeutigkeit testen.

	Test-AzureName -Storage <Proposed storage account name>

Wenn dieser Befehl „False“ zurückgibt, ist der vorgeschlagene Name eindeutig. Sie müssen das Azure-Rechenzentrum angeben, wenn Sie ein Speicherkonto erstellen. Führen Sie diesen Befehl aus, um eine Liste der Azure-Rechenzentren zu erhalten.

	Get-AzureLocation | sort Name | Select Name

Mit folgenden Befehlen erstellen und legen Sie nun das Speicherkonto fest. Geben Sie die Namen des Speicherkontos ein, und ersetzen Sie alles innerhalb der Anführungszeichen, einschließlich der Zeichen < and >.

	$stAccount="<chosen storage account name>"
	$locName="<Azure location>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $locName
	Set-AzureStorageAccount -StorageAccountName $stAccount
	Set-AzureSubscription -SubscriptionName $subscrName -CurrentStorageAccountName $stAccount

Als Nächstes benötigen Sie einen Clouddienst. Wenn kein Clouddienst vorhanden ist, müssen Sie einen erstellen. Sie müssen einen eindeutigen Namen auswählen, der nur Buchstaben, Zahlen und Bindestriche enthält. Das erste und das letzte Zeichen im Feld müssen Buchstaben oder Zahlen sein.

Sie können beispielsweise den Namen TestCS-\*UniqueSequence\* wählen, wobei *UniqueSequence* eine Abkürzung für Ihre Organisation ist. Wenn Ihre Organisation z. B. Tailspin Toys heißt, können Sie den Clouddienst TestCS-Tailspin nennen.

Mit dem folgenden Azure PowerShell-Befehl können Sie den Namen auf seine Eindeutigkeit testen.

	Test-AzureName -Service <Proposed cloud service name>

Wenn dieser Befehl „False“ zurückgibt, ist der vorgeschlagene Name eindeutig. Erstellen Sie dann mit den folgenden Befehlen den Clouddienst:

	$csName="<cloud service name>"
	$locName="<Azure location>"
	New-AzureService -Service $csName -Location $locName

Kopieren Sie als Nächstes den folgenden PowerShell-Befehlssatz in einen Texteditor wie Notepad.

	$vmName="<machine name>"
	$csName="<cloud service name>"
	$locName="<Azure location>"
	$vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	New-AzureVM –ServiceName $csName –Location $locName -VMs $vm

Geben Sie im Texteditor den Namen der virtuellen Maschine, den Clouddienstnamen und den Speicherort ein.

Kopieren Sie den Befehlssatz schließlich in die Zwischenablage, und klicken Sie dann mit der rechten Maustaste auf Ihre offene Azure PowerShell-Eingabeaufforderung. Dadurch wird der Befehlssatz als Reihe an PowerShell-Befehlen ausgegeben. Geben Sie den Namen und das Kennwort für das lokale Administratorkonto ein und erstellen Ihren virtuellen Azure-Computer. Hier ist ein Beispiel dafür, wie das Ausführen des Befehlssatzes aussieht.

	PS C:\> $vmName="PSTest"
	PS C:\> $csName=" TestCS-Tailspin"
	PS C:\> $locName="West US"
	PS C:\> $vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd"
	PS C:\> $cred=Get-Credential -Message "Type the name and password of the local administrator account."
	PS C:\> $vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.
	GetNetworkCredential().Password

	
	AvailabilitySetName               :
	ConfigurationSets                 : PSTest,Microsoft.WindowsAzure.Commands.ServiceManagement.Model.NetworkConfigurationSet}
	DataVirtualHardDisks              : {}
	Label                             : PSTest
	OSVirtualHardDisk                 : Microsoft.WindowsAzure.Commands.ServiceManagement.Model.OSVirtualHardDisk
	RoleName                          : PSTest
	RoleSize                          : Medium
	RoleType                          : PersistentVMRole
	WinRMCertificate                  :
	X509Certificates                  : {}
	NoExportPrivateKey                : False
	NoRDPEndpoint                     : False
	NoSSHEndpoint                     : False
	DefaultWinRmCertificateThumbprint :
	ProvisionGuestAgent               : True
	ResourceExtensionReferences       : {BGInfo}
	DataVirtualHardDisksToBeDeleted   :
	VMImageInput                      :
	
	PS C:\> New-AzureVM -ServiceName $csName -Location $locName -VMs $vm
	VERBOSE: 3:01:46 PM - Begin Operation: New-AzureVM - Create Deployment with VM PSTest
	VERBOSE: 3:02:49 PM - Completed Operation: New-AzureVM - Create Deployment with VM PSTest
	
	OperationDescription                    OperationId                            OperationStatus
	--------------------                    -----------                            --------------
	New-AzureVM                             8072cbd1-4abe-9278-9de2-8826b56e9221   Succeeded
	

## Zusätzliche Ressourcen

[Erstellen eines virtuellen Windows-Computers mit dem Azure Resource Manager und PowerShell](virtual-machines-create-windows-powershell-rm.md)

[Erstellen eines virtuellen Windows-Computers mit einer Resource Manager-Vorlage und PowerShell](virtual-machines-create-windows-powershell-rm-template-simple.md)

[Dokumentation zu virtuellen Maschinen](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Installieren und Konfigurieren von Azure PowerShell](install-configure-powershell.md)

[Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer](virtual-machines-ps-create-preconfigure-windows-vms.md)


<!--HONumber=52-->
