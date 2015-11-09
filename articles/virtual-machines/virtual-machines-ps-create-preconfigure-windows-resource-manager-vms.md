<properties
	pageTitle="Erstellen und Konfigurieren eines virtuellen Computers | Microsoft Azure"
	description="Sie können einen virtuellen Azure-Computer mit dem PowerShell- und dem Ressourcen-Manager-Bereitstellungsmodell erstellen und konfigurieren."
	services="virtual-machines"
	documentationCenter=""
	authors="cynthn"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="cynthn"/>

# Erstellen und Konfigurieren eines virtuellen Windows-Computers mit dem Ressourcen-Manager und Azure PowerShell

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-ps-create-preconfigure-windows-vms.md).

Diese Schritte zeigen, wie eine Reihe von Azure PowerShell-Befehlen zum Erstellen und Konfigurieren eines virtuellen Azure-Computers erstellt werden. Sie können dieses Bausteinverfahren verwenden, um schnell einen Befehlssatz für einen neuen Windows-basierten virtuellen Computer zu erstellen und eine vorhandene Bereitstellung zu erweitern. Sie können damit auch mehrere Befehlssätze erstellen, mit denen Sie schnell eine benutzerdefinierte professionelle Entwicklungs- und Test-IT-Umgebung aufbauen können.

Diese Schritte folgen einem lückenfüllenden Ansatz zur Erstellung von Azure PowerShell-Befehlssätzen. Dieser Ansatz kann hilfreich sein, wenn Sie noch nicht mit PowerShell gearbeitet haben oder einfach wissen möchten, welche Werte Sie für die erfolgreiche Konfiguration angeben müssen. Wenn Sie ein fortgeschrittener PowerShell-Benutzer sind, können Sie die Befehle verwenden und die Variablen (mit "$" beginnende Zeilen) durch eigene Werte ersetzen.

## Schritt 1: Installieren von Azure PowerShell

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

## Schritt 2: Festlegen des Abonnements

Starten Sie zunächst eine Azure PowerShell-Eingabeaufforderung.

Melden Sie sich in Ihrem Konto an.

	Login-AzureRmAccount

Rufen Sie Ihren Abonnementnamen mit dem folgenden Befehl ab.

	Get-AzureSubscription | Sort SubscriptionName | Select SubscriptionName

Legen Sie Ihr Azure-Abonnement fest. Ersetzen Sie alles in den Anführungszeichen, einschließlich der Zeichen < and >, durch die korrekten Namen.

	$subscr="<subscription name>"
	Select-AzureSubscription -SubscriptionName $subscr –Current


## Schritt 3: Erstellen von Ressourcen

In diesem Abschnitt wird gezeigt, wie jede Ressource für den neuen virtuellen Computer erstellt wird.

### Ressourcengruppe


Virtuelle Computer, die mit dem Ressourcen-Manager-Bereitstellungsmodell erstellt wurden, erfordern eine Ressourcengruppe. Erstellen Sie bei Bedarf eine neue Ressourcengruppe für den neuen virtuellen Computer. Ersetzen Sie alles in den Anführungszeichen, einschließlich der Zeichen < and >, durch die korrekten Namen.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureRmResourceGroup -Name $rgName -Location $locName

Mit diesem Befehl können Sie Ihre vorhandenen Ressourcengruppen auflisten.

	Get-AzureRmResourceGroup | Sort ResourceGroupName | Select ResourceGroupName

Erhalten Sie eine Liste mit Azure-Speicherorten, in denen Sie virtuelle Computer auf Ressourcen-Manager-Basis erstellen können.

	$loc=Get-AzureRmLocation | where { $_.Name –eq "Microsoft.Compute/virtualMachines" }
	$loc.Locations

### Speicherkonto


Virtuelle Computer, die mit dem Ressourcen-Manager-Bereitstellungsmodell erstellt wurden, erfordern ein Speicherkonto auf Basis des Ressourcen-Managers. Erstellen Sie bei Bedarf mit den folgenden Befehlen ein neues Speicherkonto für den neuen virtuellen Computer.

	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	$saName="<storage account name>"
	$saType="<storage account type, specify one: Standard_LRS, Standard_GRS, Standard_RAGRS, or Premium_LRS>"
	New-AzureRmStorageAccount -Name $saName -ResourceGroupName $rgName –Type $saType -Location $locName

Geben Sie einen global eindeutigen Namen für das Speicherkonto an, der nur aus Kleinbuchstaben und Zahlen besteht. Mit diesem Befehl können Sie die vorhandenen Speicherkonten auflisten.

	Get-AzureRmStorageAccount | Sort Name | Select Name

Um zu testen, ob ein gewählter Speicherkontenname global eindeutig ist, müssen Sie den Befehl **Test-AzureName** ausführen.

	Test-AzureName -Storage <Proposed storage account name>

Wenn der Befehl "Test-AzureName" als Ergebnis "False" zurückgibt, ist der vorgeschlagene Name eindeutig.


### Öffentliche Bezeichnung für die Domäne


Mit dem Ressourcen-Manager-Bereitstellungsmodell erstellte virtuelle Computer können eine öffentliche Bezeichnung für die Domäne verwenden. Die Bezeichnung darf nur Buchstaben, Zahlen und Bindestriche enthalten. Das erste und das letzte Zeichen müssen Buchstaben oder Zahlen sein.

Um zu testen, ob eine gewählte Domänennamensbezeichnung global eindeutig ist, verwenden Sie diese Befehle.

	$domName="<domain name label to test>"
	$loc="<short name of an Azure location, for example, for West US, the short name is westus>"
	Test-AzureRmDnsAvailability -DomainQualifiedName $domName -Location $loc

Wenn DNSNameAvailability "True" ist, ist der vorgeschlagene Name global eindeutig.

### Verfügbarkeitsgruppe


Erstellen Sie bei Bedarf mit den folgenden Befehlen eine neue Verfügbarkeitsgruppe für den neuen virtuellen Computer.

	$avName="<availability set name>"
	$rgName="<resource group name>"
	$locName="<location name, such as West US>"
	New-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName -Location $locName

Verwenden Sie diesen Befehl zum Auflisten der vorhandenen Verfügbarkeitsgruppen.

	Get-AzureRmAvailabilitySet –ResourceGroupName $rgName | Sort Name | Select Name

### NAT-Regeln

Virtuelle Computer auf Ressourcen-Manager-Basis können mit eingehenden NAT-Regeln zum Zulassen von eingehendem Datenverkehr aus dem Internet konfiguriert und in eine Gruppe mit Lastenausgleich platziert werden. In beiden Fällen müssen Sie eine Load Balancer-Instanz und andere Einstellungen angeben. Weitere Informationen finden Sie unter [Erstellen eines Load Balancers mit dem Azure-Ressourcen-Manager](../load-balancer/load-balancer-arm-powershell.md).

Virtuelle Computer, die mit dem Ressourcen-Manager-Bereitstellungsmodell erstellt wurden, erfordern ein virtuelles Netzwerk mit dem Ressourcen-Manager. Erstellen Sie bei Bedarf ein neues virtuelles Netzwerk auf Ressourcen-Manager-Basis mit mindestens einem Subnetz für den neuen virtuellen Computer. Hier ist ein Beispiel für ein neues virtuelles Netzwerk namens **TestNet** mit zwei Subnetzen namens **frontendSubnet** und **backendSubnet**.

	$rgName="LOBServers"
	$locName="West US"
	$frontendSubnet=New-AzureRmVirtualNetworkSubnetConfig -Name frontendSubnet -AddressPrefix 10.0.1.0/24
	$backendSubnet=New-AzureRmVirtualNetworkSubnetConfig -Name backendSubnet -AddressPrefix 10.0.2.0/24
	New-AzureRmVirtualNetwork -Name TestNet -ResourceGroupName $rgName -Location $locName -AddressPrefix 10.0.0.0/16 -Subnet $frontendSubnet,$backendSubnet

Mit diesen Befehlen können Sie die vorhandenen virtuellen Netzwerke auflisten.

	$rgName="<resource group name>"
	Get-AzureRmVirtualNetwork -ResourceGroupName $rgName | Sort Name | Select Name

## Schritt 4: Erstellen des Befehlssatzes

Öffnen Sie eine neue Instanz eines Texteditors Ihrer Wahl oder die PowerShell Integrated Scripting Environment (ISE), und kopieren Sie die folgenden Zeilen, um den Befehlssatz zu starten. Geben Sie den Namen der Ressourcengruppe, den Azure-Standort und das Speicherkonto für den neuen virtuellen Computer an. Ersetzen Sie alles in den Anführungszeichen, einschließlich der Zeichen < and >, durch die korrekten Namen.

	$rgName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$saName="<storage account name>"

Sie müssen den Namen eines virtuelles Netzwerks auf Ressourcen-Manager-Basis und die Indexnummer eines Subnetzes im virtuellen Netzwerk angeben. Verwenden Sie diese Befehle, um die Subnetze für ein virtuelles Netzwerk aufzulisten.

	$rgName="<resource group name>"
	$vnetName="<virtual network name>"
	Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName | Select Subnets

Der Subnetzindex stellt die Anzahl der Subnetze in der Anzeige dieses Befehls dar, nacheinander von links nach rechts und beginnend bei 0 nummeriert.

Zu diesem Beispiel:

	PS C:\> Get-AzureRmVirtualNetwork -Name TestNet -ResourceGroupName LOBServers | Select Subnets

	Subnets
	-------
	{frontendSubnet, backendSubnet}

Der Subnetzindex für das frontendSubnet ist 0, und der Subnetzindex für das backendSubnet ist 1.

Kopieren Sie diese Zeilen in den Befehlssatz, und geben Sie einen vorhandenen virtuellen Netzwerknamen sowie den Subnetzindex für den virtuellen Computer an.

	$vnetName="<name of an existing virtual network>"
	$subnetIndex=<index of the subnet on which to create the NIC for the virtual machine>
	$vnet=Get-AzureRmVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

Als Nächstes erstellen Sie eine Netzwerkschnittstellenkarte (NIC). Kopieren Sie eine der folgenden Optionen in den Befehlssatz, und geben Sie die erforderlichen Informationen ein.

### Option 1: Geben Sie einen NIC-Namen an und weisen eine öffentliche IP-Adresse zu.

Kopieren Sie die folgenden Zeilen in den Befehlssatz, und geben Sie den Namen für die Netzwerkkarte an.

	$nicName="<name of the NIC of the VM>"
	$pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

### Option 2: Geben Sie einen NIC-Namen und die DNS-Domänennamensbezeichnung an.

Kopieren Sie die folgenden Zeilen in den Befehlssatz, und geben Sie den Namen für die Netzwerkkarte sowie die global eindeutige Domänennamensbezeichnung an.

	$nicName="<name of the NIC of the VM>"
	$domName="<domain name label>"
	$pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

### Option 3: Geben Sie einen NIC-Namen an und weisen eine statische, private IP-Adresse zu.

Kopieren Sie die folgenden Zeilen in den Befehlssatz, und geben Sie den Namen für die Netzwerkkarte an.

	$nicName="<name of the NIC of the VM>"
	$staticIP="<available static IP address on the subnet>"
	$pip = New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -Location $locName -AllocationMethod Dynamic
	$nic = New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id -PrivateIpAddress $staticIP

### Option 4: Geben Sie einen NIC-Namen und eine Load Balancer-Instanz für eine eingehende NAT-Regel an.

Zum Erstellen einer Netzwerkkarte und Hinzufügen der Karte zu einer Load Balancer-Instanz für eine eingehende NAT-Regel benötigen Sie Folgendes:

- Den Namen einer zuvor erstellten Load Balancer-Instanz, die über eine eingehende NAT-Regel für den Datenverkehr verfügt, der an den virtuellen Computer weitergeleitet wird.
- Die Indexnummer des Back-End-Adresspools der Load Balancer-Instanz zum Zuweisen auf die NIC.
- Die Indexnummer der eingehenden NAT-Regel zum Zuweisen auf die NIC.

Informationen zum Erstellen einer Lastenausgleichsmodul-Instanz mit eingehenden NAT-Regeln finden Sie unter [Erstellen eines Lastenausgleichsmoduls mit dem Azure-Ressourcen-Manager](../load-balancer/load-balancer-arm-powershell.md).

Kopieren Sie diese Zeilen in den Befehlssatz, und geben Sie die erforderlichen Namen und Indexnummern an.

	$nicName="<name of the NIC of the VM>"
	$lbName="<name of the load balancer instance>"
	$bePoolIndex=<index of the back end pool, starting at 0>
	$natRuleIndex=<index of the inbound NAT rule, starting at 0>
	$lb=Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgName
	$nic=New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -Subnet $vnet.Subnets[$subnetIndex].Id -LoadBalancerBackendAddressPool $lb.BackendAddressPools[$bePoolIndex] -LoadBalancerInboundNatRule $lb.InboundNatRules[$natRuleIndex]

Die „$nicName“-Zeichenfolge muss für die Ressourcengruppe eindeutig sein. Eine bewährte Methode ist die Integration des Namens des virtuellen Computers in der Zeichenfolge, wie z. B. "LOB07-NIC".

### Option 5: Geben Sie einen NIC-Namen und eine Load Balancer-Instanz für eine Gruppe mit Lastenausgleich an.

Zum Erstellen einer Netzwerkkarte und Hinzufügen der Karte zu einer Load Balancer-Instanz für eine Gruppe mit Lastenausgleich benötigen Sie Folgendes:

- Den Namen einer zuvor erstellten Load Balancer-Instanz, die über eine Regel für den eingehenden Datenverkehr verfügt.
- Die Indexnummer des Back-End-Adresspools der Load Balancer-Instanz zum Zuweisen auf die NIC.

Informationen zum Erstellen einer Lastenausgleichsmodul-Instanz mit Regeln für Datenverkehr mit Lastenausgleich finden Sie unter [Erstellen eines Lastenausgleichsmoduls mit dem Azure-Ressourcen-Manager](../load-balancer/load-balancer-arm-powershell.md).

Kopieren Sie diese Zeilen in den Befehlssatz, und geben Sie die erforderlichen Namen und Indexnummern an.

	$nicName="<name of the NIC of the VM>"
	$lbName="<name of the load balancer instance>"
	$bePoolIndex=<index of the back end pool, starting at 0>
	$lb=Get-AzureRmLoadBalancer -Name $lbName -ResourceGroupName $rgName
	$nic=New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -Subnet $vnet.Subnets[$subnetIndex].Id -LoadBalancerBackendAddressPool $lb.BackendAddressPools[$bePoolIndex]

Als Nächstes erstellen Sie ein lokales Objekt für den virtuellen Computer und fügen es optional einer Verfügbarkeitsgruppe hinzu. Kopieren Sie eine der beiden folgenden Optionen in den Befehlssatz, und geben Sie den Namen, die Größe und den Verfügbarkeitsgruppennamen ein.

Option 1: Geben Sie einen Namen für den virtuellen Computer und die Größe an.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$vm=New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize

Verwenden Sie diese Befehle, um die möglichen Werte für die Größenzeichenfolge des virtuellen Computers für Option 1 zu bestimmen.

	$locName="<Azure location of your resource group>"
	Get-AzureRmVMSize -Location $locName | Select Name

Option 2: Geben Sie einen Namen für den virtuellen Computer sowie die Größe an, und fügen Sie diesen einer Verfügbarkeitsgruppe hinzu.

	$vmName="<VM name>"
	$vmSize="<VM size string>"
	$avName="<availability set name>"
	$avSet=Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

Verwenden Sie diese Befehle, um die möglichen Werte für die Größenzeichenfolge des virtuellen Computers für Option 2 zu bestimmen.

	$rgName="<resource group name>"
	$avName="<availability set name>"
	Get-AzureRmVMSize -ResourceGroupName $rgName -AvailabilitySetName $avName | Select Name

> [AZURE.NOTE]Derzeit können Sie mit dem Ressourcen-Manager einen virtuellen Computer nur bei der Erstellung zu einer Verfügbarkeitsgruppe hinzufügen.

Um dem virtuellen Computer einen zusätzlichen Datenträger hinzuzufügen, kopieren Sie diese Zeilen in den Befehlssatz, und geben Sie die Datenträgereinstellungen an.

	$diskSize=<size of the disk in GB>
	$diskLabel="<the label on the disk>"
	$diskName="<name identifier for the disk in Azure storage, such as 21050529-DISK02>"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureRmVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI  -CreateOption empty

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
	$vm=Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRmVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

Schließlich kopieren Sie diese Befehle in den Befehlssatz, und geben den Namensbezeichner für den Betriebssystem-Datenträger für den virtuellen Computer ein.

	$diskName="<name identifier for the disk in Azure storage, such as OSDisk>"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $diskName  + ".vhd"
	$vm=Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Schritt 5: Ausführen des Befehlssatzes

Überprüfen Sie den in Schritt 4 erstellten Azure PowerShell-Befehlssatz in einem Texteditor oder der PowerShell ISE. Stellen Sie sicher, dass Sie alle Variablen angegeben haben und diese die richtigen Werte aufweisen. Stellen Sie außerdem sicher, dass Sie alle < and > entfernt haben.

Wenn Sie Ihre Befehle in einem Texteditor verwenden, kopieren Sie den Befehlssatz in die Zwischenablage, und klicken Sie dann mit der rechten Maustaste auf Ihre Azure PowerShell-Eingabeaufforderung. Dies sendet den Befehlssatz als Serie von PowerShell-Befehlen und erstellt den virtuellen Azure-Computer. Führen Sie alternativ den Befehlssatz in Azure PowerShell ISE aus.

Wenn Sie diese Informationen zum Erstellen zusätzlicher virtueller Computer wieder verwenden möchten, können Sie diesen Befehlssatz als PowerShell-Skriptdatei (*.ps1) speichern.

## Beispiel

Ich brauche einen PowerShell-Befehlssatz, um einen zusätzlichen virtuellen Computer für eine Line-of-Business-Webserver-Workload zu erstellen, die:

- sich in der vorhandenen LOBServers-Ressourcengruppe befindet
- das Windows Server 2012 R2 Datacenter-Image verwendet
- den Namen LOB07 hat und sich in der vorhandenen WEB\_AS-Verfügbarkeitsgruppe befindet
- über eine Netzwerkkarte mit einer öffentlichen IP-Adresse im FrontEnd-Subnetz (Subnetzindex 0) des vorhandenen virtuellen AZDatacenter-Netzwerks verfügt
- einen zusätzlichen Datenträger mit 200 GB aufweist

Hier finden Sie den Azure PowerShell-Befehlssatz zum Erstellen dieses virtuellen Computers.

	# Set values for existing resource group and storage account names
	$rgName="LOBServers"
	$locName="West US"
	$saName="contosolobserverssa"

	# Set the existing virtual network and subnet index
	$vnetName="AZDatacenter"
	$subnetIndex=0
	$vnet=Get-AzureRMVirtualNetwork -Name $vnetName -ResourceGroupName $rgName

	# Create the NIC
	$nicName="LOB07-NIC"
	$domName="contoso-vm-lob07"
	$pip=New-AzureRmPublicIpAddress -Name $nicName -ResourceGroupName $rgName -DomainNameLabel $domName -Location $locName -AllocationMethod Dynamic
	$nic=New-AzureRmNetworkInterface -Name $nicName -ResourceGroupName $rgName -Location $locName -SubnetId $vnet.Subnets[$subnetIndex].Id -PublicIpAddressId $pip.Id

	# Specify the name, size, and existing availability set
	$vmName="LOB07"
	$vmSize="Standard_A3"
	$avName="WEB_AS"
	$avSet=Get-AzureRmAvailabilitySet –Name $avName –ResourceGroupName $rgName
	$vm=New-AzureRmVMConfig -VMName $vmName -VMSize $vmSize -AvailabilitySetId $avset.Id

	# Add a 200 GB additional data disk
	$diskSize=200
	$diskLabel="APPStorage"
	$diskName="21050529-DISK02"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$vhdURI=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	Add-AzureRmVMDataDisk -VM $vm -Name $diskLabel -DiskSizeInGB $diskSize -VhdUri $vhdURI -CreateOption empty

	# Specify the image and local administrator account, and then add the NIC
	$pubName="MicrosoftWindowsServer"
	$offerName="WindowsServer"
	$skuName="2012-R2-Datacenter"
	$cred=Get-Credential -Message "Type the name and password of the local administrator account."
	$vm=Set-AzureRmVMOperatingSystem -VM $vm -Windows -ComputerName $vmName -Credential $cred -ProvisionVMAgent -EnableAutoUpdate
	$vm=Set-AzureRmVMSourceImage -VM $vm -PublisherName $pubName -Offer $offerName -Skus $skuName -Version "latest"
	$vm=Add-AzureRmVMNetworkInterface -VM $vm -Id $nic.Id

	# Specify the OS disk name and create the VM
	$diskName="OSDisk"
	$storageAcc=Get-AzureRmStorageAccount -ResourceGroupName $rgName -Name $saName
	$osDiskUri=$storageAcc.PrimaryEndpoints.Blob.ToString() + "vhds/" + $vmName + $diskName  + ".vhd"
	$vm=Set-AzureRmVMOSDisk -VM $vm -Name $diskName -VhdUri $osDiskUri -CreateOption fromImage
	New-AzureRmVM -ResourceGroupName $rgName -Location $locName -VM $vm

## Zusätzliche Ressourcen

[Azure Compute-, Network- and Storage-Anbieter unter dem Azure-Ressourcen-Manager](virtual-machines-azurerm-versus-azuresm.md)

[Übersicht über den Azure Resource Manager](../resource-group-overview.md)

[Bereitstellen und Verwalten von virtuellen Azure-Computern mit Ressourcen-Manager-Vorlagen und PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

[Erstellen eines virtuellen Windows-Computers mit einer Resource Manager-Vorlage und PowerShell](virtual-machines-create-windows-powershell-resource-manager-template-simple)

[Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md)

<!---HONumber=Nov15_HO1-->