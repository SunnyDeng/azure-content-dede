<properties 
   pageTitle="Bereitstellen von Multi-NIC-VMs mithilfe der Azure-Befehlszeilenschnittstelle im klassischen Bereitstellungsmodell | Microsoft Azure"
   description="Erfahren Sie, wie Sie Multi-NIC-VMs mithilfe der Azure-Befehlszeilenschnittstelle im klassischen Bereitstellungsmodell bereitstellen."
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

#Bereitstellen von Multi-NIC-VMs (klassisch) mithilfe der Azure-Befehlszeilenschnittstelle

[AZURE.INCLUDE [virtual-network-deploy-multinic-classic-selectors-include.md](../../includes/virtual-network-deploy-multinic-classic-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-multinic-intro-include.md](../../includes/virtual-network-deploy-multinic-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-classic-include.md)] [Resource Manager model](virtual-network-deploy-multinic-arm-cli.md).

[AZURE.INCLUDE [virtual-network-deploy-multinic-scenario-include.md](../../includes/virtual-network-deploy-multinic-scenario-include.md)]

Da virtuelle Computer mit nur einer Netzwerkschnittstellenkarte (NIC) zurzeit nicht mit virtuellen Computern mit mehreren Netzwerkschnittstellenkarten im selben Clouddienst vorhanden sein können, werden die Back-End-Server in einem anderen Clouddienst implementiert als die anderen Komponenten im Szenario. In den folgenden Schritten wird der Clouddienst *IaaSStory* für die Hauptressourcen und *IaaSStory-BackEnd* für die Back-End-Server verwendet.

## Voraussetzungen

Bevor Sie die Back-End-Server bereitstellen können, müssen Sie den Hauptclouddienst mit den erforderlichen Ressourcen für dieses Szenario bereitstellen. Sie müssen zumindest ein virtuelles Netzwerk mit einem Subnetz für das Back-End erstellen. Unter [Erstellen eines virtuellen Netzwerks über die Azure-Befehlszeilenschnittstelle](virtual-networks-create-vnet-classic-cli.md) finden Sie Informationen über das Bereitstellen eines virtuellen Netzwerks.

[AZURE.INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## Bereitstellen der Back-End-VMs

Die Back-End-VMs sind auf die Erstellung der im Folgenden aufgelisteten Ressourcen angewiesen.

- **Speicherkonto für Datenträger**. Für eine bessere Leistung verwenden die Datenträger auf den Datenbankservern Solid State Drive (SSD)-Technologie. Dafür ist ein Premium-Speicherkonto erforderlich. Achten Sie darauf, dass der Azure-Speicherort für die Bereitstellung Storage Premium unterstützt.
- **NICs**. Jeder virtuelle Computer hat zwei Netzwerkkarten, eine für den Datenbankzugriff und eine für die Verwaltung.
- **Verfügbarkeitsgruppe**. Alle Datenbankserver werden einer einzigen Verfügbarkeitsgruppe hinzugefügt, damit sichergestellt ist, dass mindestens ein virtueller Computer während der Wartung ausgeführt wird. 

### Schritt 1: Starten des Skripts

Sie können das verwendete Bash-Skript ungekürzt [hier](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/11-MultiNIC/classic/multinic.sh) herunterladen. Gehen Sie folgendermaßen vor, um das Skript an Ihre Arbeitsumgebung anzupassen.

1. Ändern Sie die Werte der nachstehenden Variablen basierend auf der im obigen Abschnitt [Voraussetzungen](#Prerequisites) bereitgestellten Ressourcengruppe.

		location="useast2"
		vnetName="WTestVNet"
		backendSubnetName="BackEnd"

2. Ändern Sie die Werte der nachstehenden Variablen basierend auf den Werten, die Sie für die Back-End-Bereitstellung verwenden möchten.

		backendCSName="IaaSStory-Backend"
		prmStorageAccountName="iaasstoryprmstorage"
		image="0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1"
		avSetName="ASDB"
		vmSize="Standard_DS3"
		diskSize=127
		vmNamePrefix="DB"
		osDiskName="osdiskdb"
		dataDiskPrefix="db"
		dataDiskName="datadisk"
		ipAddressPrefix="192.168.2."
		username='adminuser'
		password='adminP@ssw0rd'
		numberOfVMs=2

### Schritt 2: Erstellen der erforderlichen Ressourcen für Ihre virtuellen Computer

1. Erstellen Sie einen neuen Clouddienst für alle Back-End-VMs. Beachten Sie, dass die `$backendCSName`-Variable für den Ressourcengruppennamen und `$location` für die Azure-Region verwendet wird

		azure service create --serviceName $backendCSName \
		    --location $location

2. Erstellen Sie ein Premium-Speicherkonto für das Betriebssystem und die Datenträger, die von den virtuellen Computern verwendet werden sollen.

		azure storage account create $prmStorageAccountName \
		    --location $location \
		    --type PLRS 

### Schritt 3: Erstellen von virtuellen Computern mit mehreren Netzwerkschnittstellenkarten

1. Erstellen Sie mithilfe einer Schleife mehrere virtuelle Computer auf Grundlage der `numberOfVMs`-Variablen.

		for ((suffixNumber=1;suffixNumber<=numberOfVMs;suffixNumber++));
		do

2. Geben Sie für jeden virtuellen Computer den Namen und die IP-Adresse der beiden Netzwerkkarten an.

		    nic1Name=$vmNamePrefix$suffixNumber-DA
		    x=$((suffixNumber+3))
		    ipAddress1=$ipAddressPrefix$x
		
		    nic2Name=$vmNamePrefix$suffixNumber-RA
		    x=$((suffixNumber+53))
		    ipAddress2=$ipAddressPrefix$x

4. Erstellen Sie den virtuellen Computer. Beachten Sie, dass der `--nic-config`-Parameter verwendet wird, der eine Liste aller Netzwerkkarten mit Namen, Subnetz und IP-Adresse enthält.

		    azure vm create $backendCSName $image $username $password \
		        --connect $backendCSName \
		        --vm-name $vmNamePrefix$suffixNumber \
		        --vm-size $vmSize \
		        --availability-set $avSetName \
		        --blob-url $prmStorageAccountName.blob.core.windows.net/vhds/$osDiskName$suffixNumber.vhd \
		        --virtual-network-name $vnetName \
		        --subnet-names $backendSubnetName \
		        --nic-config $nic1Name:$backendSubnetName:$ipAddress1::,$nic2Name:$backendSubnetName:$ipAddress2::

5. Erstellen Sie für jeden virtuellen Computer zwei Datenträger.

		    azure vm disk attach-new $vmNamePrefix$suffixNumber \
		        $diskSize \
		        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-1.vhd
		
		    azure vm disk attach-new $vmNamePrefix$suffixNumber \
		        $diskSize \
		        vhds/$dataDiskPrefix$suffixNumber$dataDiskName-2.vhd
		done

### Schritt 4: Ausführen des Skripts

Führen Sie das Skript aus, nachdem sie es heruntergeladen und angepasst haben, um die Back-End-VMs mit mehreren Netzwerkkarten zu erstellen.

1. Speichern Sie Ihr Skript, und führen Sie es im **Bash**-Terminal aus. Anfänglich wird die folgende Ausgabe angezeigt.

		info:    Executing command service create
		info:    Creating cloud service
		data:    Cloud service name IaaSStory-Backend
		info:    service create command OK
		info:    Executing command storage account create
		info:    Creating storage account
		info:    storage account create command OK
		info:    Executing command vm create
		info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
		info:    Looking up virtual network
		info:    Looking up cloud service
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Creating VM

2. Nach einigen Minuten wird die Ausführung beendet, und Sie sehen den im Folgenden gezeigten Rest der Ausgabe.

		info:    OK
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm create
		info:    Looking up image 0b11de9248dd4d87b18621318e037d37__RightImage-Ubuntu-14.04-x64-v14.2.1
		info:    Looking up virtual network
		info:    Looking up cloud service
		info:    Getting cloud service properties
		info:    Looking up deployment
		info:    Creating VM
		info:    OK
		info:    vm create command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK
		info:    Executing command vm disk attach-new
		info:    Getting virtual machines
		info:    Adding Data-Disk
		info:    vm disk attach-new command OK

<!---HONumber=Nov15_HO4-->