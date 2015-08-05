<properties
	pageTitle="Erstellen und Verwalten eines virtuellen Windows-Computers in der Dienstverwaltung unter Verwendung von Azure PowerShell"
	description="Verwenden Sie Azure PowerShell, um schnell einen neuen Windows-basierten virtuellen Computer in der Dienstverwaltung zu erstellen und Verwaltungsfunktionen auszuführen."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="kathydav"/>

# Erstellen und Verwalten eines Windows-basierten virtuellen Computers in der Azure-Dienstverwaltung unter Verwendung von Azure PowerShell

Dieser Artikel beschreibt das Erstellen und Verwalten eines virtuellen Windows Azure-Computers in der Dienstverwaltung mithilfe von Azure PowerShell.

[AZURE.INCLUDE [service-management-pointer-to-resource-manager](../../includes/service-management-pointer-to-resource-manager.md)]

- [Bereitstellen und Verwalten von virtuellen Computern mit Azure-Ressourcen-Manager-Vorlagen und PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

## Einrichten von Azure PowerShell

Wenn Sie bereits Azure PowerShell installiert haben, benötigen Sie Version 0.8.0 oder höher. Sie können die von Ihnen installierte Azure Power Shell-Version mit diesem Befehl über die Azure PowerShell-Eingabeaufforderung prüfen:

	Get-Module azure | format-table version

Wenn Sie dies noch nicht getan haben, verwenden Sie die Anweisungen unter [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md), um Azure PowerShell auf Ihrem lokalen Computer zu installieren. Öffnen Sie dann eine Azure PowerShell-Befehlseingabeaufforderung.

Zunächst müssen Sie sich mit dem folgenden Befehl bei Azure anmelden:

	Add-AzureAccount

Geben Sie die E-Mail-Adresse Ihres Azure-Kontos und das Kennwort in das Anmeldedialogfeld von Microsoft Azure ein.

Wenn Sie über mehrere Azure-Abonnements verfügen, müssen Sie als Nächstes Ihr Azure-Abonnement festlegen. Führen Sie den folgenden Befehl aus, um eine Liste Ihrer aktuellen Abonnements anzuzeigen:

	Get-AzureSubscription | sort SubscriptionName | Select SubscriptionName

Ersetzen Sie nun alles innerhalb der Anführungszeichen einschließlich der Zeichen < and > durch den korrekten Abonnementnamen und führen Sie die Befehle aus:

	$subscrName="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscrName –Current

## Erstellen eines virtuellen Computers

Sie benötigen zunächst ein Speicherkonto. Sie können die aktuelle Liste der Speicherkonten mit diesem Befehl anzeigen:

	Get-AzureStorageAccount | sort Label | Select Label

Falls Sie noch keines haben, erstellen Sie ein neues Speicherkonto. Sie müssen einen eindeutigen Namen auswählen, der nur Kleinbuchstaben und Zahlen enthält. Mit diesem Befehl können Sie den Speicherkontonamen auf Eindeutigkeit testen:

	Test-AzureName -Storage <Proposed Storage account name>

Wird von diesem Befehl „False“ zurückgegeben, ist der vorgeschlagene Name eindeutig.

Sie müssen das Azure-Rechenzentrum angeben, wenn Sie ein Speicherkonto erstellen. Führen Sie diesen Befehl aus, um eine Liste der Azure-Rechenzentren zu erhalten:

	Get-AzureLocation | sort Name | Select Name

Mit folgenden Befehlen können Sie nun das Speicherkonto erstellen und festlegen. Geben Sie die Namen des Speicherkontos ein, und ersetzen Sie alles innerhalb der Anführungszeichen, einschließlich der Zeichen < and >.

	$stAccount="<chosen Storage account name>"
	$locName="<Azure location>"
	New-AzureStorageAccount -StorageAccountName $stAccount -Location $locName
	Set-AzureStorageAccount -StorageAccountName $stAccount
	Set-AzureSubscription -SubscriptionName $subscrName -CurrentStorageAccountName $stAccount

Als Nächstes benötigen Sie einen Clouddienst. Wenn kein Clouddienst vorhanden ist, müssen Sie einen erstellen. Sie müssen einen eindeutigen Namen auswählen, der nur Buchstaben, Zahlen und Bindestriche enthält. Das erste und das letzte Zeichen im Feld müssen Buchstaben oder Zahlen sein.

Sie können beispielsweise den Namen TestCS-*UniqueSequence* wählen, wobei *UniqueSequence* eine Abkürzung für Ihre Organisation ist. Wenn Ihre Organisation z. B. Tailspin Toys heißt, können Sie den Clouddienst TestCS-Tailspin nennen.

Die Eindeutigkeit des Namens können Sie mit dem folgenden Azure PowerShell-Befehl überprüfen:

	Test-AzureName -Service <Proposed cloud service name>

Wird von diesem Befehl „False“ zurückgegeben, ist der vorgeschlagene Name eindeutig. Erstellen Sie mit den folgenden Befehlen den Clouddienst:

	$csName="<cloud service name>"
	$locName="<Azure location>"
	New-AzureService -Service $csName -Location $locName

Kopieren Sie als Nächstes den folgenden Azure PowerShell-Befehlssatz in einen Texteditor wie Notepad:

	$vmName="<machine name>"
	$csName="<cloud service name>"
	$locName="<Azure location>"
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	$vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName $image
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password
	New-AzureVM –ServiceName $csName –Location $locName -VMs $vm

Geben Sie im Texteditor den Namen der virtuellen Maschine, den Clouddienstnamen und den Speicherort ein.

Kopieren Sie den Befehlssatz schließlich in die Zwischenablage, und klicken Sie dann mit der rechten Maustaste auf Ihre offene Azure PowerShell-Eingabeaufforderung. Dadurch wird der Befehlssatz als Reihe an Azure PowerShell-Befehlen ausgegeben. Geben Sie den Namen und das Kennwort für das lokale Administratorkonto ein und erstellen Ihren virtuellen Azure-Computer. Hier ist ein Beispiel dafür, wie das Ausführen des Befehlssatzes aussieht:

	PS C:\> $vmName="PSTest"
	PS C:\> $csName=" TestCS-Tailspin"
	PS C:\> $locName="West US"
	PS C:\> $image=Get-AzureVMImage | where { $_.ImageFamily -eq "Windows Server 2012 R2 Datacenter" } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1
	VERBOSE: 3:01:17 PM - Begin Operation: Get-AzureVMImage
	VERBOSE: 3:01:22 PM - Completed Operation: Get-AzureVMImage
	VERBOSE: 3:01:22 PM - Begin Operation: Get-AzureVMImage
	VERBOSE: 3:01:23 PM - Completed Operation: Get-AzureVMImage
	PS C:\> $vm=New-AzureVMConfig -Name $vmName -InstanceSize Medium -ImageName $image
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

## Anzeigen von Informationen zu einem virtuellen Computer
Dies ist eine einfache Aufgabe, die Sie häufig verwenden. Verwenden Sie sie zum Abrufen von Informationen zu einem virtuellen Computer, Ausführen von Aufgaben auf diesem oder Abrufen von Ausgaben zur Speicherung in einer Variablen.

Um Informationen über den virtuellen Computer zu erhalten, führen Sie diesen Befehl aus und ersetzen allen Text in Anführungszeichen einschließlich der < and >-Zeichen:

     Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

Führen Sie zum Speichern der Ausgabe in eine $vm-Variable folgenden Code aus:

    $vm = Get-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Anmelden bei einem virtuellen Computer auf Windows-Basis

Führen Sie diese Befehle aus.

	$svcName="<cloud service name>"
	$vmName="<virtual machine name>"
	$localPath="<drive and folder location to store the downloaded RDP file, example: c:\temp >"
	$localFile=$localPath + "" + $vmname + ".rdp"
	Get-AzureRemoteDesktopFile -ServiceName $svcName -Name $vmName -LocalPath $localFile -Launch

>[AZURE.NOTE]Sie erhalten den Namen des virtuellen Computers und Clouddiensts über die Anzeige des **Get-AzureVM** Befehls.

## Anhalten eines virtuellen Computers

Führen Sie den folgenden Befehl aus:

    Stop-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

>[AZURE.IMPORTANT]Verwenden Sie den **StayProvisioned**-Parameter, um die virtuelle IP-Adresse des Clouddiensts beizubehalten, falls es sich um den letzten virtuellen Computer in diesem Clouddienst handelt. Auch wenn Sie diesen Parameter verwenden, wird der virtuelle Computer in Rechnung gestellt.

## Starten eines virtuellen Computers

Führen Sie den folgenden Befehl aus:

    Start-AzureVM -ServiceName "<cloud service name>" -Name "<virtual machine name>"

## Datenträger anfügen
Diese Aufgabe erfordert einige Schritte. Zunächst verwenden Sie das Cmdlet **Add-AzureDataDisk** zum Hinzufügen des Datenträgers zum $vm-Objekt. Dann verwenden Sie das Cmdlet "Update-AzureVM", um die Konfiguration des virtuellen Computers zu aktualisieren.

Sie müssen auch entscheiden, ob Sie einen neuen Datenträger anfügen oder einen, der Daten enthält. Für einen neuen Datenträger erstellt der Befehl die VHD-Datei und fügt sie im selben Befehl an.

Um einen neuen Datenträger anzufügen, führen Sie den folgenden Befehl aus:

    Add-AzureDataDisk -CreateNew -DiskSizeInGB <disk size> -DiskLabel "<label name>" -LUN <LUN number> -VM $vm | Update-AzureVM

Um einen vorhandenen Datenträger anzufügen, führen Sie den folgenden Befehl aus:

    Add-AzureDataDisk -Import -DiskName "<existing disk name>" -LUN <LUN number> | Update-AzureVM

Führen Sie den folgenden Befehl zum Anfügen von Datenträgern aus einer vorhandenen VHD-Datei im Blob-Speicher aus:

    $diskLoc="https://mystorage.blob.core.windows.net/mycontainer/" + "<existing disk name>" + ".vhd"
	Add-AzureDataDisk -ImportFrom -MediaLocation  $diskLoc -DiskLabel "<label name>" -LUN <LUN number> | Update-AzureVM

## Zusätzliche Ressourcen

[Erstellen eines virtuellen Windows-Computers mit dem Ressourcen-Manager und Azure PowerShell](virtual-machines-create-windows-powershell-resource-manager.md)

[Erstellen eines virtuellen Windows-Computers mit einer Resource Manager-Vorlage und Azure PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple.md)

[Dokumentation zu virtuellen Computern](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md)

[Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer](virtual-machines-ps-create-preconfigure-windows-vms.md)

<!---HONumber=July15_HO4-->