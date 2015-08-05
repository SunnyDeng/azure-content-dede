<properties
	pageTitle="Erstellen und Vorkonfigurieren eines virtuellen Windows-Computers mit dem Ressourcen-Manager und Azure PowerShell"
	description="Erfahren Sie, wie Sie Azure PowerShell zum Erstellen und Vorabkonfigurieren virtueller Computer auf Windows- und Ressourcen-Manager-Basis verwenden."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="kathydav"/>

# Erstellen und Vorkonfigurieren eines virtuellen Windows-Computers mit dem Ressourcen-Manager und Azure PowerShell

Diese Schritte zeigen, wie Sie einen Befehlssatz im Ressourcen-Manager-Modus von Azure PowerShell erstellen, mit dem ein Windows-basierter virtueller Azure-Computer erstellt und vorab konfiguriert wird. Sie können dieses Bausteinverfahren verwenden, um schnell einen Befehlssatz für einen neuen Windows-basierten virtuellen Computer zu erstellen und eine vorhandene Bereitstellung zu erweitern. Sie können damit auch mehrere Befehlssätze erstellen, mit denen Sie schnell eine benutzerdefinierte professionelle Entwicklungs- und Test-IT-Umgebung aufbauen können.

Diese Schritte folgen einem lückenfüllenden Ansatz zur Erstellung von Azure PowerShell-Befehlssätzen. Dieser Ansatz kann hilfreich sein, wenn Sie noch nicht mit PowerShell gearbeitet haben oder einfach wissen möchten, welche Werte Sie für die erfolgreiche Konfiguration angeben müssen. Wenn Sie ein fortgeschrittener PowerShell-Benutzer sind, können Sie die Befehle verwenden und die Variablen (mit "$" beginnende Zeilen) durch eigene Werte ersetzen.

[AZURE.INCLUDE [resource-manager-pointer-to-service-management](../../includes/resource-manager-pointer-to-service-management.md)]

- [Verwenden von Azure PowerShell zum Erstellen und Vorabkonfigurieren Windows-basierter virtueller Computer](virtual-machines-ps-create-preconfigure-windows-vms.md)

## Schritt 1: Installieren von Azure PowerShell

Ebenfalls müssen Sie über die Azure PowerShell-Version 0.9.0 oder eine aktuellere verfügen. Falls Azure PowerShell noch nicht installiert und konfiguriert ist, klicken Sie für die Anleitung bitte [hier](powershell-install-configure.md).

Sie können die von Ihnen installierte Azure Power Shell-Version mit diesem Befehl über die Azure PowerShell-Eingabeaufforderung prüfen.

	Get-Module azure | format-table version

Beispiel:

	Version
	-------
	0.9.0

Falls Sie nicht über Version 0.9.0 oder eine aktuellere verfügen, müssen Sie Azure PowerShell mithilfe der Systemsteuerung "Programme und Funktionen" entfernen und anschließend die aktuellste Version installieren. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).

## Schritt 2: Festlegen des Abonnements

Öffnen Sie zunächst eine Azure PowerShell-Eingabeaufforderung.

Legen Sie Ihr Azure-Abonnement fest, indem Sie diese Befehle in der Azure PowerShell-Eingabeaufforderung ausführen. Ersetzen Sie alles in den Anführungszeichen, einschließlich der Zeichen < and >, durch die korrekten Namen.

	$subscr="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current

Sie erhalten den richtigen Abonnementnamen über die Anzeige dieses Befehls.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Wechseln Sie in Azure PowerShell jetzt in den Ressourcen-Manager-Modus.

	Switch-AzureMode AzureResourceManager

## Schritt 3: Erstellen der erforderlichen Ressourcen

Virtuelle Computer auf Ressourcen-Manager-Basis benötigen eine Ressourcengruppe. Erstellen Sie bei Bedarf mit den folgenden Befehlen eine neue Ressourcengruppe für den neuen virtuellen Computer. Ersetzen Sie alles in den Anführungszeichen, einschließlich der Zeichen < and >, durch die korrekten Namen.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureResourceGroup -Name $rgName -Location $locName

Um einen eindeutigen Namen für die Ressourcengruppe zu finden, verwenden Sie diesen Befehl zum Auflisten der vorhandenen Ressourcengruppen.

	Get-AzureResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Verwenden Sie diese Befehle, um die Azure-Speicherorte aufzulisten, in denen Sie virtuelle Computer auf Ressourcen-Manager-Basis erstellen können.

	$loc=Get-AzureLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

Virtuelle Computer auf Ressourcen-Manager-Basis benötigen ein Speicherkonto auf Ressourcen-Manager-Basis. Erstellen Sie bei Bedarf mit den folgenden Befehlen ein neues Speicherkonto für den neuen virtuellen Computer.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	$saName="<storage account name>"
	$saType="<storage account type, specify one: Standard_LRS, Standard_GRS, Standard_RAGRS, or Premium_LRS>"
	New-AzureStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

Geben Sie einen global eindeutigen Namen für das Speicherkonto an, der nur aus Kleinbuchstaben und Zahlen besteht. Mit diesem Befehl können Sie die vorhandenen Speicherkonten auflisten.

	Get-AzureStorageAccount | Sort Name | Select Name

Um zu testen, ob ein gewählter Speicherkontenname global eindeutig ist, müssen Sie den Befehl **Test-AzureName** im Azure-Dienstverwaltungsmodus von PowerShell ausführen. Verwenden Sie diese Befehle.

	Switch-AzureMode AzureServiceManagement
	Test-AzureName -Storage <Proposed storage account name>

Wenn der Befehl "Test-AzureName" als Ergebnis "False" zurückgibt, ist der vorgeschlagene Name eindeutig. Wenn Sie einen eindeutigen Namen ermittelt haben, wechseln Sie mit dem folgenden Befehl wieder in den Ressourcen-Manager-Modus von Azure PowerShell.

	Switch-AzureMode AzureResourceManager

Virtuelle Computer auf Ressourcen-Manager-Basis können eine Namensbezeichnung der öffentlichen Domäne verwenden, die nur Buchstaben, Zahlen und Bindestriche enthalten kann. Das erste und das letzte Zeichen im Feld müssen Buchstaben oder Zahlen sein.

Um zu testen, ob eine gewählte Domänennamensbezeichnung global eindeutig ist, verwenden Sie diese Befehle.

	$domName="<domain name label to test>"
	$loc="<short name of an Azure location, for example, for West US, the short name is westus>"
	Get-AzureCheckDnsAvailability -DomainQualifiedName $domName -Location $loc

Wenn DNSNameAvailability "True" ist, ist der vorgeschlagene Name global eindeutig.

Virtuelle Computer auf Ressourcen-Manager-Basis können in eine Verfügbarkeitsgruppe auf Ressourcen-Manager-Basis eingefügt werden. Erstellen Sie bei Bedarf mit den folgenden Befehlen eine neue Verfügbarkeitsgruppe für den neuen virtuellen Computer.

	$avName="<availability set name>"
	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

Verwenden Sie diesen Befehl zum Auflisten der vorhandenen Verfügbarkeitsgruppen.

	Get-AzureAvailabilitySet –ResourceGroupName $rgName | Sort Name | Select Name

Virtuelle Computer auf Ressourcen-Manager-Basis benötigen ein virtuelles Netzwerk auf Ressourcen-Manager-Basis. Erstellen Sie bei Bedarf ein neues virtuelles Netzwerk auf Ressourcen-Manager-Basis mit mindestens einem Subnetz für den neuen virtuellen Computer. Hier ist ein Beispiel für ein neues virtuelles Netzwerk mit zwei Subnetzen namens frontendSubnet und backendSubnet.

	$rgName="LOBServers"
	$locName="West US"
	$frontendSubnet=New-AzureVirtualNetworkSubnetConfig -Name frontendSubnet -AddressPrefix 10.0.1.0/24
	$backendSubnet=New-AzureVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix 10.0.2.0/24
	New-AzurevirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $frontendSubnet,$backendSubnet

Mit diesen Befehlen können Sie die vorhandenen virtuellen Netzwerke auflisten.

	$rgName="<resource group name>"
	Get-AzureVirtualNetwork -ResourceGroupName $rgName | Sort Name | Select Name

## Schritt 4: Erstellen des Befehlssatzes

Öffnen Sie eine neue Instanz eines Texteditors Ihrer Wahl oder die PowerShell Integrated Scripting Environment (ISE), und kopieren Sie die folgenden Zeilen, um den Befehlssatz zu starten. Geben Sie den Namen der Ressourcengruppe, den Azure-Standort und das Speicherkonto für den neuen virtuellen Computer an. Ersetzen Sie alles in den Anführungszeichen, einschließlich der Zeichen < and >, durch die korrekten Namen.

	Switch-AzureMode AzureResourceManager
	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"

Sie müssen den Namen eines virtuelles Netzwerks auf Ressourcen-Manager-Basis und die Indexnummer eines Subnetzes im virtuellen Netzwerk angeben. Verwenden Sie diese Befehle, um die Subnetze für ein virtuelles Netzwerk aufzulisten.

	$rgName="<resource group name>"
	$vnetName="<virtual network name>"
	Get-AzureVirtualNetwork -Name $vnetName -ResourceGroupName $rgName | Select Subnets

Der Subnetzindex stellt die Anzahl der Subnetze in der Anzeige dieses Befehls dar, nacheinander von links nach rechts und beginnend bei 0 nummeriert.

Zu diesem Beispiel:

	PS C:\> Get-AzureVirtualNetwork -Name TestNet -ResourceGroupName LOBServers | Select Subnets

	Subnets
	-------
	{frontendSubnet, backendSubnet}

Der Subnetzindex für das frontendSubnet ist 0, und der Subnetzindex für das backendSubnet ist 1.

Kopieren Sie diese Zeilen in den Befehlssatz, und geben Sie einen vorhandenen virtuellen Netzwerknamen sowie den Subnetzindex für den virtuellen Computer an.

	$vnetName="<name of an existing virtual network>"
	$subnetIndex=<index of the subnet on which to create the NIC for the virtual machine>
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName

Im nächsten Schritt erstellen Sie eine Netzwerkschnittstellenkarte (NIC), fordern eine öffentliche IP-Adresse an und weisen dieser optional eine DNS-Domänennamensbezeichnung zu. Kopieren Sie eine der beiden folgenden Optionen in den Befehlssatz, und geben Sie den NIC-Namen und die DNS-Domänennamensbezeichnung ein.

Option 1: Geben Sie einen Namen für die Netzwerkkarte an.

Kopieren Sie diese Zeilen in den Befehlssatz, und geben Sie den Namen für die Netzwerkkarte an.

	$nicName="<name of the NIC of the VM>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

Option 2: Geben Sie einen Namen für die Netzwerkkarte und eine DNS-Domänennamensbezeichnung an.

Kopieren Sie diese Zeilen in den Befehlssatz, und geben Sie den Namen für die Netzwerkkarte sowie die global eindeutige Domänennamensbezeichnung an. Beim Erstellen von virtuellen Computern im Dienstverwaltungsmodus von Azure PowerShell schließt Azure diese Schritte automatisch ab.

	$nicName="<name of the NIC of the VM>"
	$domName="<domain name label>"
	$pip = New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

Als Nächstes erstellen Sie ein lokales Objekt für den virtuellen Computer und fügen es optional einer Verfügbarkeitsgruppe hinzu. Kopieren Sie eine der beiden folgenden Optionen in den Befehlssatz, und geben Sie den Namen, die Größe und den Verfügbarkeitsgruppennamen ein.

Option 1: Geben Sie einen Namen für den virtuellen Computer und die Größe an.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize

Verwenden Sie diese Befehle, um die möglichen Werte für die Größenzeichenfolge des virtuellen Computers für Option 1 zu bestimmen.

	$locName="<Azure location of your resource group>"
	Get-AzureVMSize -Location $locName | Select Name

Option 2: Geben Sie einen Namen für den virtuellen Computer sowie die Größe an, und fügen Sie diesen einer Verfügbarkeitsgruppe hinzu.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$avName="<availability set name>"
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

Verwenden Sie diese Befehle, um die möglichen Werte für die Größenzeichenfolge des virtuellen Computers für Option 2 zu bestimmen.

	$rgName="<resource group name>"
	$avName="<availability set name>"
	Get-AzureVMSize -ResourceGroupName $rgName -AvailabilitySetName $avName | Select Name

> [AZURE.NOTE]Derzeit können Sie mit dem Ressourcen-Manager einen virtuellen Computer nur bei der Erstellung zu einer Verfügbarkeitsgruppe hinzufügen.

Um dem virtuellen Computer einen zusätzlichen Datenträger hinzuzufügen, kopieren Sie diese Zeilen in den Befehlssatz, und geben Sie die Datenträgereinstellungen an.

	$diskSize=<size of the disk in GB>
	$diskLabel="<the label on the disk>"
	$diskName="<name identifier for the disk in Azure storage, such as 21050529-DISK02>"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty

Als Nächstes müssen Sie Herausgeber, Angebot und SKU des virtuellen Computerimages ermitteln. Im Folgenden finden Sie eine Tabelle mit häufig verwendeten Windows-basierten Images.

|Herausgebername | Angebotsname | SKU-Name
|:---------------------------------|:-------------------------------------------|:---------------------------------|
|MicrosoftWindowsServer | Windows Server | 2008-R2-SP1 |
|MicrosoftWindowsServer | Windows Server | 2012-Datacenter |
|MicrosoftWindowsServer | Windows Server | 2012-R2-Datacenter |
|MicrosoftDynamicsNAV | DynamicsNAV | 2015 |
|MicrosoftSharePoint | MicrosoftSharePointServer | 2013 |
|MicrosoftSQLServer | SQL 2014 WS2012R2 | Enterprise-Optimized-for-DW |
|MicrosoftSQLServer | SQL 2014 WS2012R2 | Enterprise-Optimized-for-OLTP |
|MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials |
|MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 |

Wenn das benötigte Image des virtuellen Computers nicht aufgeführt ist, folgen Sie diesen [Anweisungen](resource-groups-vm-searching.md#powershell), um Herausgeber, Angebot und SKU-Namen zu ermitteln.

Kopieren Sie diese Befehle in den Befehlssatz, und geben Sie Herausgeber, Angebot und SKU-Namen ein.

	$pubName="<Image publisher name>"
	$offerName="<Image offer name>"
	$skuName="<Image SKU name>"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id

Schließlich kopieren Sie diese Befehle in den Befehlssatz, und geben den Namensbezeichner für den Betriebssystem-Datenträger für den virtuellen Computer ein.

	$diskName="<name identifier for the disk in Azure storage, such as OSDisk>"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Schritt 5: Ausführen des Befehlssatzes

Überprüfen Sie den Azure PowerShell-Befehl in einem Texteditor, bestehend aus mehreren Blöcken von Befehlen aus Schritt 4. Stellen Sie sicher, dass Sie alle erforderlichen Variablen angegeben haben und diese die richtigen Werte aufweisen. Stellen Sie außerdem sicher, dass Sie alle < and > entfernt haben.

Wenn Sie Ihre Befehle in einem Texteditor verwenden, kopieren Sie den Befehlssatz in die Zwischenablage, und klicken Sie dann mit der rechten Maustaste auf Ihre offene Azure PowerShell-Eingabeaufforderung. Dies gibt den Befehlssatz als Serie von PowerShell-Befehlen aus und erstellt den virtuellen Azure-Computer. Führen Sie alternativ den Befehlssatz in Azure PowerShell ISE aus.

Wenn Sie diesen virtuellen Computer erneut oder einen ähnlichen virtuellen Computer erstellen, können Sie diesen Befehlssatz als PowerShell-Skriptdatei (*.PS1) speichern.

## Beispiel

Ich brauche einen PowerShell-Befehlssatz, um einen zusätzlichen virtuellen Computer für eine Line-of-Business-Webserver-Workload zu erstellen, die:

- sich in der vorhandenen LOBServers-Ressourcengruppe befindet
- das Windows Server 2012 R2 Datacenter-Image verwendet
- den Namen LOB07 hat und sich in der vorhandenen WEB_AS-Verfügbarkeitsgruppe befindet
- über eine Netzwerkkarte mit einer öffentlichen IP-Adresse im FrontEnd-Subnetz (Subnetzindex 0) des vorhandenen virtuellen AZDatacenter-Netzwerks verfügt
- einen zusätzlichen Datenträger mit 200 GB aufweist

Hier finden Sie den entsprechenden Azure PowerShell-Befehlssatz zum Erstellen dieses virtuellen Computers auf Grundlage der in Schritt 4 beschriebenen Verfahren.

	# Switch to the Resource Manager mode
	Switch-AzureMode AzureResourceManager

	# Set values for existing resource group and storage account names
	$rgName="LOBServers"
	$locName="West US"
	$saName="contosoLOBServersSA"

	# Set the existing virtual network and subnet index
	$vnetName="AZDatacenter"
	$subnetIndex=0
	$vnet=Get-AzurevirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	# Create the NIC
	$nicName="AzureInterface"
	$domName="contoso-vm-lob07"
	$pip=New-AzurePublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

	# Specify the name, size, and existing availability set
	$vmName="LOB07"
	$vmSize="Standard_A3"
	$avName="WEB_AS"
	$avSet=Get-AzureAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

	# Add a 200 GB additional data disk
	$diskSize=200
	$diskLabel="APPStorage"
	$diskName="21050529-DISK02"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI -CreateOption empty

	# Specify the image and local administrator account, and then add the NIC
	$pubName="MicrosoftWindowsServer"
	$offerName="WindowsServer"
	$skuName="2012-R2-Datacenter"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureVMNetworkInterface -VM $vm -Id $nic.Id

	# Specify the OS disk name and create the VM
	$diskName="OSDisk"
	$storageAcc=Get-AzureStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	$vm=Set-AzureVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Zusätzliche Ressourcen

[Azure Compute-, Network- and Storage-Anbieter unter dem Azure-Ressourcen-Manager](virtual-machines-azurerm-versus-azuresm.md)

[Übersicht über den Azure Resource Manager](resource-group-overview.md)

[Bereitstellen und Verwalten von virtuellen Azure-Computern mit Ressourcen-Manager-Vorlagen und PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

[Erstellen eines virtuellen Windows-Computers mit einer Resource Manager-Vorlage und PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple)

[Installieren und Konfigurieren von Azure PowerShell](install-configure-powershell.md)

<!---HONumber=July15_HO4-->