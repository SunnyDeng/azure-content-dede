<properties 
   pageTitle="Bereitstellen von Multi-NIC-VMs mithilfe von PowerShell im klassischen Bereitstellungsmodell | Microsoft Azure"
   description="Erfahren Sie, wie Sie Multi-NIC-VMs mithilfe von PowerShell im klassischen Bereitstellungsmodell bereitstellen."
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
   ms.date="11/12/2015"
   ms.author="telmos" />

#Bereitstellen von Multi-NIC-VMs (klassisch) mit PowerShell

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-network-deploy-multinic-arm-ps.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Da virtuelle Computer mit nur einer Netzwerkschnittstellenkarte (NIC) zurzeit nicht mit virtuellen Computern mit mehreren Netzwerkschnittstellenkarten im selben Clouddienst vorhanden sein können, werden die Back-End-Server in einem anderen Clouddienst implementiert als die anderen Komponenten im Szenario. In den folgenden Schritten wird der Clouddienst *IaaSStory* für die Hauptressourcen und *IaaSStory-BackEnd* für die Back-End-Server verwendet.

## Voraussetzungen

Bevor Sie die Back-End-Server bereitstellen können, müssen Sie den Hauptclouddienst mit den erforderlichen Ressourcen für dieses Szenario bereitstellen. Sie müssen zumindest ein virtuelles Netzwerk mit einem Subnetz für das Back-End erstellen. Unter [Erstellen eines virtuellen Netzwerks über PowerShell](virtual-networks-create-vnet-classic-ps.md) finden Sie Informationen über das Bereitstellen eines virtuellen Netzwerks.

[AZURE.INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## Bereitstellen der Back-End-VMs

Die Back-End-VMs sind auf die Erstellung der im Folgenden aufgelisteten Ressourcen angewiesen.

- **Back-End-Subnetz**. Die Datenbankserver gehören zum Aufteilen des Datenverkehrs einem separaten Subnetz an. Das folgende Skript erwartet dieses Subnetz in einem Vnet namens *WTestVnet*.
- **Speicherkonto für Datenträger**. Für eine bessere Leistung verwenden die Datenträger auf den Datenbankservern Solid State Drive (SSD)-Technologie. Dafür ist ein Premium-Speicherkonto erforderlich. Achten Sie darauf, dass der Azure-Speicherort für die Bereitstellung Storage Premium unterstützt.
- **Verfügbarkeitsgruppe**. Alle Datenbankserver werden einer einzigen Verfügbarkeitsgruppe hinzugefügt, damit sichergestellt ist, dass mindestens ein virtueller Computer während der Wartung ausgeführt wird. 

### Schritt 1: Starten des Skripts

Sie können das verwendete PowerShell-Skript ungekürzt [hier](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/multinic.ps1) herunterladen. Gehen Sie folgendermaßen vor, um das Skript an Ihre Arbeitsumgebung anzupassen.

1. Ändern Sie die Werte der nachstehenden Variablen basierend auf der im obigen Abschnitt [Voraussetzungen](#Prerequisites) bereitgestellten Ressourcengruppe.

		$location              = "West US"
		$vnetName              = "WTestVNet"
		$backendSubnetName     = "BackEnd"

2. Ändern Sie die Werte der nachstehenden Variablen basierend auf den Werten, die Sie für die Back-End-Bereitstellung verwenden möchten.

		$backendCSName         = "IaaSStory-Backend"
		$prmStorageAccountName = "iaasstoryprmstorage"
		$avSetName             = "ASDB"
		$vmSize                = "Standard_DS3"
		$diskSize              = 127
		$vmNamePrefix          = "DB"
		$dataDiskSuffix        = "datadisk"
		$ipAddressPrefix       = "192.168.2."
		$numberOfVMs           = 2

### Schritt 2: Erstellen der erforderlichen Ressourcen für Ihre virtuellen Computer

Sie müssen einen neuen Clouddienst und ein Speicherkonto für die Datenträger für alle virtuellen Computer erstellen. Sie müssen zudem ein Abbild und ein lokales Administratorkonto für die virtuellen Computer angeben. Führen Sie die folgenden Schritte aus, um diese Ressourcen zu erstellen.

1. Erstellen Sie einen neuen Clouddienst.

		New-AzureService -ServiceName $backendCSName -Location $location

2. Erstellen Sie ein Premium-Speicherkonto.

		New-AzureStorageAccount -StorageAccountName $prmStorageAccountName `
		    -Location $location `
		    -Type Premium_LRS

3. Legen Sie das zuvor erstellte Speicherkonto als aktuelles Speicherkonto für Ihr Abonnement fest.

		$subscription = Get-AzureSubscription `
		    | where {$_.IsCurrent -eq $true}  
		Set-AzureSubscription -SubscriptionName $subscription.SubscriptionName `
		    -CurrentStorageAccountName $prmStorageAccountName

4. Wählen Sie ein Abbild für den virtuellen Computer aus.

		$image = Get-AzureVMImage `
		    | where{$_.ImageFamily -eq "SQL Server 2014 RTM Web on Windows Server 2012 R2"} `
		    | sort PublishedDate -Descending `
		    | select -ExpandProperty ImageName -First 1

5. Legen Sie die Anmeldeinformationen des lokalen Administratorkontos fest.

		$cred = Get-Credential -Message "Enter username and password for local admin account"

### Schritt 3: Erstellen von virtuellen Computern

Sie müssen die gewünschte Anzahl an virtuellen Computern mithilfe einer Schleife erstellen. Erstellen Sie die erforderlichen Netzwerkkarten und virtuellen Computer innerhalb der Schleife. Führen Sie zum Erstellen der Netzwerkkarten und virtuellen Computer die folgenden Schritte aus.

1. Starten Sie eine `for`-Schleife, um die Befehle zum Erstellen eines virtuellen Computers und zweier Netzwerkkarten auf Grundlage der `$numberOfVMs`-Variablen so oft wie erforderlich zu wiederholen.

		for ($suffixNumber = 1; $suffixNumber -le $numberOfVMs; $suffixNumber++){

2. Erstellen Sie ein `VMConfig`-Objekt, mit dem Abbild, Größe und Verfügbarkeitsgruppe für den virtuellen Computer festgelegt werden.

		    $vmName = $vmNamePrefix + $suffixNumber
		    $vmConfig = New-AzureVMConfig -Name $vmName `
		                    -ImageName $image `
		                    -InstanceSize $vmSize `
		                    -AvailabilitySetName $avSetName  

3. Bereitstellen des virtuellen Computers als Windows-VM.

		    Add-AzureProvisioningConfig -VM $vmConfig -Windows `
		        -AdminUsername $cred.UserName `
		        -Password $cred.Password

4. Legen Sie die Standard-Netzwerkkarte fest, und weisen Sie ihr eine statische IP-Adresse zu.

		    Set-AzureSubnet -SubnetNames $backendSubnetName -VM $vmConfig
		    Set-AzureStaticVNetIP -IPAddress ($ipAddressPrefix+$suffixNumber+3) -VM $vmConfig

5. Fügen Sie für jeden virtuellen Computer eine zweite Netzwerkkarte hinzu.

		    Add-AzureNetworkInterfaceConfig -Name ("RemoteAccessNIC"+$suffixNumber) `
		        -SubnetName $backendSubnetName `
		        -StaticVNetIPAddress ($ipAddressPrefix+(53+$suffixNumber)) `
		        -VM $vmConfig 

6. Erstellen Sie für jeden virtuellen Computer zwei Datenträger.

		    $dataDisk1Name = $vmName + "-" + $dataDiskSuffix + "-1"    
		    Add-AzureDataDisk -CreateNew -VM $vmConfig `
		        -DiskSizeInGB $diskSize `
		        -DiskLabel $dataDisk1Name `
		        -LUN 0       
		
		    $dataDisk2Name = $vmName + "-" + $dataDiskSuffix + "-2"   
		    Add-AzureDataDisk -CreateNew -VM $vmConfig `
		        -DiskSizeInGB $diskSize `
		        -DiskLabel $dataDisk2Name `
		        -LUN 1

7. Erstellen Sie die virtuellen Computer, und beenden Sie die Schleife.

		    New-AzureVM -VM $vmConfig `
		        -ServiceName $backendCSName `
		        -Location $location `
		        -VNetName $vnetName
		}

### Schritt 4: Ausführen des Skripts

Führen Sie das Skript aus, nachdem sie es heruntergeladen und angepasst haben, um die Back-End-VMs mit mehreren Netzwerkkarten zu erstellen.

1. Speichern Sie Ihr Skript und führen Sie es von der **PowerShell**-Eingabeaufforderung oder in **PowerShell ISE** aus. Anfänglich wird die folgende Ausgabe angezeigt.

		OperationDescription    OperationId                          OperationStatus
		--------------------    -----------                          ---------------
		New-AzureService        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
		New-AzureStorageAccount xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded      
		                                                                            
		WARNING: No deployment found in service: 'IaaSStory-Backend'.

2. Tragen Sie bei der Aufforderung die Anmeldeinformationen ein, und klicken Sie auf **OK**. Die nachfolgende Ausgabe wird angezeigt.

		New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded
		New-AzureVM             xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx Succeeded 

<!---HONumber=Nov15_HO4-->