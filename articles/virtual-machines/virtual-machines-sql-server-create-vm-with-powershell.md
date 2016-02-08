<properties
	pageTitle="Erstellen eines virtuellen Computers mit SQL Server in PowerShell | Microsoft Azure"
	description="Enthält Schritte und PowerShell-Skripts zum Erstellen eines virtuellen Azure-Computers über Images aus dem Katalog von virtuellen Computern mit SQL Server."
	services="virtual-machines"
	documentationCenter="na"
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"
	tags="azure-service-management" />
<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="01/22/2016"
	ms.author="jroth" />

# Erstellen eines virtuellen Computers mit SQL Server in Azure (PowerShell)

> [AZURE.SELECTOR]
- [Classic portal](virtual-machines-provision-sql-server.md)
- [PowerShell](virtual-machines-sql-server-create-vm-with-powershell.md)
- [Azure Resource Manager portal](virtual-machines-sql-server-provision-resource-manager.md)


## Übersicht

Dieser Artikel enthält Schritte zum Erstellen eines virtuellen Computers mit SQL Server in Azure durch Verwenden von PowerShell-Cmdlets.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-classic-include.md)]Ressourcen-Manager-Modell.


## Installieren und Konfigurieren von PowerShell

1. Wenn Sie kein Azure-Konto haben, sollten Sie die Seite [Kostenlose einmonatige Testversion](https://azure.microsoft.com/pricing/free-trial/) besuchen.

2. [Installieren Sie die neuesten Azure PowerShell-Cmdlets](../powershell-install-configure.md/#how-to-install-azure-powershell).

3. [Verbinden Sie PowerShell mit Ihrem Azure-Abonnement](../powershell-install-configure.md/#how-to-connect-to-your-subscription).

## Bestimmen Sie Ihre Azure-Zielregion

Ihr virtueller Computer mit SQL Server wird in einem Clouddienst gehostet, der sich in einer bestimmte Azure-Region befindet. Anhand der folgenden Schritte können Sie Ihre Region, Ihr Speicherkonto und Ihren Clouddienst bestimmen, die für den Rest des Lernprogramms verwendet werden.

1. Bestimmen Sie das Rechenzentrum, das Sie zum Hosten Ihres virtuellen Computers mit SQL Server verwenden möchten. Die folgenden PowerShell-Befehlen zeigen die verfügbaren Regionen ausführlich mit einer Zusammenfassungsliste am Ende an.

		Get-AzureLocation
		(Get-AzureLocation).Name

2.  Sobald Sie Ihren bevorzugten Standort ermittelt haben, legen Sie die Variable **$dcLocation** auf diese Region fest.

		$dcLocation = "<region name>"

## Festlegen Ihres Abonnements und Speicherkontos

1. Bestimmen Sie das Azure-Abonnement, das Sie für den neuen virtuellen Computer verwenden möchten.

		(Get-AzureSubscription).SubscriptionName

1. Weisen Sie Ihr Azure-Zielabonnement der **$subscr**-Variablen zu. Verwenden Sie dann diese Variable, um Ihr aktuelles Azure-Abonnements festzulegen.

		$subscr="<subscription name>"
		Select-AzureSubscription -SubscriptionName $subscr –Current

1. Prüfen Sie danach auf vorhandene Speicherkonten. Das folgende Skript zeigt alle Speicherkonten an, die es in der von Ihnen ausgewählten Region gibt:

		(Get-AzureStorageAccount | where { $_.GeoPrimaryLocation -eq $dcLocation }).StorageAccountName

	>[AZURE.NOTE] Wenn Sie ein neues Speicherkonto benötigen, erstellen Sie zunächst mit dem New-AzureStorageAccount-Befehl einen vollständig aus Kleinbuchstaben bestehenden Speicherkontonamen wie in diesem Beispiel: **New-AzureStorageAccount -StorageAccountName "<storage account name>"-Location $dcLocation**

1. Weisen Sie der **$staccount**-Variablen den Namen des Zielspeicherkontos zu. Verwenden Sie dann **Set-AzureSubscription**, um das Abonnement und das aktuelle Speicherkonto festzulegen.

		$staccount="<storage account name>"
		Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

## Auswählen eines Image für einen virtuellen Computer mit SQL Server

1. Ermitteln Sie im Katalog die Liste der verfügbaren Images für virtuelle Computer mit SQL Server. Jedes dieser Images hat eine **ImageFamily**-Eigenschaft, die mit "SQL" beginnt. Die folgende Abfrage zeigt die Familie der Images an, die für Sie verfügbar sind und in denen SQL Server vorinstalliert ist.

		Get-AzureVMImage | where { $_.ImageFamily -like "SQL*" } | select ImageFamily -Unique | Sort-Object -Property ImageFamily

1. Wenn Sie die Imagefamilie für virtuelle Computer gefunden haben, gibt es möglicherweise mehrere veröffentlichte Images in dieser Familie. Verwenden Sie das folgende Skript, um den Namen des Image zu ermitteln, das für die von Ihnen ausgewählte Imagefamilie das zuletzt veröffentlichte Image für virtuelle Computer ist (z. B. **SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2**):

		$family="<ImageFamily value>"
		$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

		echo "Selected SQL Server image name:"
		echo "   $image"

## Erstellen des virtuellen Computers

Erstellen Sie schließlich den virtuellen Computer PowerShell:

1. Erstellen Sie einen Clouddienst, um den neuen virtuellen Computer zu hosten. Sie können stattdessen auch einen vorhandenen Clouddienst verwenden. Erstellen Sie eine neue Variable **$svcname** mit dem kurzen Namen des Clouddiensts.

		$svcname = "<cloud service name>"
		New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

2. Geben Sie den Namen für den virtuellen Computer sowie dessen Größe an. Weitere Informationen über Größen von virtuellen Computern finden Sie unter [Größen für virtuelle Azure-Computer](virtual-machines-size-specs.md).

		$vmname="<machine name>"
		$vmsize="<Specify a valid machine size>" # see the link to virtual machine sizes
		$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

3. Geben Sie den Namen und das Kennwort des lokalen Administratorkontos an.

		$cred=Get-Credential -Message "Type the name and password of the local administrator account."
		$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

4. Führen Sie das folgende Skript aus, um den virtuellen Computer zu erstellen:

		New-AzureVM –ServiceName $svcname -VMs $vm1

>[AZURE.NOTE] Zusätzliche Erläuterungen sowie Konfigurationsoptionen finden Sie im Abschnitt **Erstellen des Befehlssatzes** in [Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer](virtual-machines-ps-create-preconfigure-windows-vms.md).

## PowerShell-Beispielskript

Das folgende Skript ist ein Beispiel für ein vollständiges Skript, das einen virtuellen **SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2**-Computer erstellt. Wenn Sie dieses Skript verwenden, müssen Sie die anfänglichen Variablen entsprechend den vorherigen Schritten in diesem Thema anpassen.

	# Customize these variables based on your settings and requirements:
	$dcLocation = "East US"
	$subscr="mysubscription"
	$staccount="mystorageaccount"
	$family="SQL Server 2014 SP1 Enterprise on Windows Server 2012 R2"
	$svcname = "mycloudservice"
	$vmname="myvirtualmachine"
	$vmsize="A5"

	# Set the current subscription and storage account
	# Comment out the New-AzureStorageAccount line if the account already exists
	Select-AzureSubscription -SubscriptionName $subscr –Current
	New-AzureStorageAccount -StorageAccountName $staccount -Location $dcLocation
	Set-AzureSubscription -SubscriptionName $subscr -CurrentStorageAccountName $staccount

	# Select the most recent VM image in this image family:
	$image=Get-AzureVMImage | where { $_.ImageFamily -eq $family } | sort PublishedDate -Descending | select -ExpandProperty ImageName -First 1

	# Create the new cloud service; comment out this line if cloud service exists already:
	New-AzureService -ServiceName $svcname -Label $svcname -Location $dcLocation

	# Create the VM config:
	$vm1=New-AzureVMConfig -Name $vmname -InstanceSize $vmsize -ImageName $image

	# Set administrator credentials:
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm1 | Add-AzureProvisioningConfig -Windows -AdminUsername $cred.GetNetworkCredential().Username -Password $cred.GetNetworkCredential().Password

	# Create the SQL Server VM:
	New-AzureVM –ServiceName $svcname -VMs $vm1


## Verbinden mit Remotedesktop

1. Erstellen Sie die RDP-Dateien im Ordner „Dokumente“ des aktuellen Benutzers, damit diese virtuellen Computer gestartet werden können, um die Installation abzuschließen:

		$documentspath = [environment]::getfolderpath("mydocuments")
		Get-AzureRemoteDesktopFile -ServiceName $svcname -Name $vmname -LocalPath "$documentspath\vm1.rdp"

1. Starten Sie die RDP-Datei im Verzeichnis „Dokumente“. Stellen Sie über die früher bereitgestellten Administrator-Anmeldeinformationen (Benutzername und Kennwort) eine Verbindung her (ist Ihr Benutzername z. B. „VMAdmin“, geben Sie „\\VMAdmin“ als Benutzer ein, und stellen Sie das Kennwort bereit).

		.\vm1.rdp

## Konfigurieren des virtuellen Computers mit SQL Server für Remotezugriff

Nachdem Sie sich über Remotedesktop bei dem Computer angemeldet haben, konfigurieren Sie SQL Server entsprechend den Anleitungen unter [Schritte zum Konfigurieren von SQL Server-Konnektivität in einer Azure-VM](virtual-machines-sql-server-connectivity.md#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

## Nächste Schritte

Weitere Anleitungen zur Bereitstellung von virtuellen Computern mit PowerShell finden Sie in der [Dokumentation zu Virtual Machines](virtual-machines-ps-create-preconfigure-windows-vms.md). Weitere Skripts im Zusammenhang mit SQL Server und Storage Premium finden Sie unter [Verwenden von Azure Premium-Speicher mit SQL Server auf virtuellen Computern](virtual-machines-sql-server-use-premium-storage.md).

In vielen Fällen ist der nächste Schritt, die Datenbanken auf diese neue SQL Server-VM zu migrieren. Anleitungen zur Datenbankmigration finden Sie unter [Migrieren einer Datenbank zu SQL Server auf einer Azure-VM](virtual-machines-migrate-onpremises-database.md).

Wenn Sie auch wissen möchten, wie diese Schritte im klassischen Azure-Portal ausgeführt werden, lesen Sie [Bereitstellen eines virtuellen Computers mit SQL Server in Azure](virtual-machines-provision-sql-server.md).

Zusätzlich zu diesen Ressourcen wird empfohlen, dass Sie sich die [anderen Themen im Zusammenhang mit Ausführen von SQL Server auf virtuellen Azure Computern](virtual-machines-sql-server-infrastructure-services.md) ansehen.

<!---HONumber=AcomDC_0128_2016-->