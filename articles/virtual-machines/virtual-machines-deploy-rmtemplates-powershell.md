<properties 
	pageTitle="Bereitstellen und Verwalten von virtuellen Azure-Computern mit Resource Manager-Vorlagen und PowerShell" 
	description="Stellen Sie ganz einfach den am häufigsten verwendeten Konfigurationssatz für virtuelle Azure-Computer bereit und verwalten Sie sie mithilfe von Resource Manager-Vorlagen und PowerShell." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/19/2015" 
	ms.author="josephd"/>

# Bereitstellen und Verwalten von virtuellen Computern mit Azure Resource Manager-Vorlagen und PowerShell

In diesem Artikel wird gezeigt, wie mit Azure-Ressourcen-Manager-Vorlagen und PowerShell allgemeine Aufgaben zum Bereitstellen und Verwalten von Azure Virtual Machines automatisiert werden. Weitere Vorlagen finden Sie unter [Azure-Schnellstartvorlagen](http://azure.microsoft.com/documentation/templates/) und [App-Frameworks](virtual-machines-app-frameworks.md).

- [Bereitstellen einer Windows-VM](#windowsvm)
- [Erstellen eines benutzerdefinierten VM-Image](#customvm)
- [Bereitstellen einer Multi-VM-Anwendung, die ein virtuelles Netzwerk und ein externes Lastenausgleichsmodul verwendet](#multivm)
- [Aktualisieren eines virtuellen Computers mit einer Ressourcen-Manager-Vorlage](#updatevm)
- [Entfernen einer Ressourcengruppe](#removerg)
- [Anmelden bei einem virtuellen Computer](#logon)
- [Anzeigen von Informationen zu einem virtuellen Computer](#displayvm)
- [Starten eines virtuellen Computers](#start)
- [Beenden eines virtuellen Computers](#stop)
- [Neustarten eines virtuellen Computers](#restart)
- [Löschen eines virtuellen Computers](#delete)

Stellen Sie vor dem Loslegen sicher, dass Azure PowerShell bereit ist.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## Grundlegendes zu Azure-Ressourcen-Manager-Vorlagen und -Ressourcengruppen

Die meisten Anwendungen, die in Microsoft Azure bereitgestellt und ausgeführt werden, bestehen aus einer Kombination von verschiedenen Cloudressourcentypen (z. B. einer oder mehreren VMs und Speicherkonten, einer SQL-Datenbank oder einem virtuellen Netzwerk). Mithilfe von Azure Resource Manager-Vorlagen können Sie die unterschiedlichen Ressourcen gemeinsam bereitstellen und verwalten. Sie verwenden hierfür eine JSON-Beschreibung der Ressourcen sowie der zugeordneten Konfigurations- und Bereitstellungsparameter.

Sobald Sie eine JSON-basierte Ressourcenvorlage definiert haben, können Sie sie ausführen und die darin definierten Ressourcen in Azure mithilfe eines PowerShell-Befehls bereitstellen. Sie können diese Befehle entweder eigenständig in der PowerShell-Befehlsshell oder in einem Skript ausführen, das eine zusätzliche Automatisierungslogik enthält.

Die Ressourcen, die Sie mithilfe von Azure Resource Manager-Vorlagen erstellen, werden entweder in einer neuen oder einer vorhandenen Azure-Ressourcengruppe bereitgestellt. Eine *Azure-Ressourcengruppe* ermöglicht Ihnen, mehrere bereitgestellte Ressourcen zusammen als eine logische Gruppe zu verwalten. Daher können Sie den gesamten Lebenszyklus der Gruppe/Anwendung verwalten und Verwaltungs-APIs bereitstellen, die es Ihnen ermöglichen:

- alle Ressourcen in der Gruppe auf einmal zu beenden, starten oder löschen. 
- Role-Based Access Control (RBAC)-Regeln zum Sperren von Sicherheitsberechtigungen. 
- Vorgänge zu überwachen. 
- Ressourcen mit zusätzlichen Metadaten zur besseren Nachverfolgung auszuzeichnen. 

Weitere Informationen zur Ressourcenverwaltung in Azure finden Sie [hier](virtual-machines-azurerm-versus-azuresm.md). Wenn Sie das Erstellen von Vorlagen interessiert, sehen Sie in [Azure Resource Manager-Vorlagen erstellen](../resource-group-authoring-templates.md) nach.

## <a id="windowsvm"></a>AUFGABE: Bereitstellen einer Windows-VM

Verwenden Sie die Anweisungen in diesem Abschnitt, um eine neue Azure-VM mit einer Resource Manager-Vorlage und Azure PowerShell bereitzustellen. Mit dieser Vorlage wird ein einzelner virtueller Computer in einem neuen virtuellen Netzwerk mit einem einzelnen Subnetz erstellt.

![](./media/virtual-machines-deploy-rmtemplates-powershell/windowsvm.png)
 
Gehen folgendermaßen Sie vor, um einen Windows-VM mithilfe einer Ressourcen-Manager-Vorlage im Github-Vorlagenrepository mit Azure PowerShell zu erstellen.

### Schritt 1: Untersuchen der JSON-Datei für die Vorlage.

Dies ist der Inhalt der JSON-Datei für die Vorlage.

	{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "newStorageAccountName": {
            "type": "string",
            "metadata": {
                "Description": "Unique DNS Name for the Storage Account where the Virtual Machine's disks will be placed."
            }
        },
        "adminUsername": {
            "type": "string",
            "metadata": {
               "Description": "Username for the Virtual Machine."
            }
        },
        "adminPassword": {
            "type": "securestring",
            "metadata": {
                "Description": "Password for the Virtual Machine."
            }
        },
        "dnsNameForPublicIP": {
            "type": "string",
            "metadata": {
                  "Description": "Unique DNS Name for the Public IP used to access the Virtual Machine."
            }
        },
        "windowsOSVersion": {
            "type": "string",
            "defaultValue": "2012-R2-Datacenter",
            "allowedValues": [
                "2008-R2-SP1", 
                "2012-Datacenter", 
                "2012-R2-Datacenter", 
                "Windows-Server-Technical-Preview"
            ],
            "metadata": {
                "Description": "The Windows version for the VM. This will pick a fully patched image of this given Windows version. Allowed values: 2008-R2-SP1, 2012-Datacenter, 2012-R2-Datacenter, Windows-Server-Technical-Preview."
            }
        }
    },
    "variables": {
        "location": "West US",
        "imagePublisher": "MicrosoftWindowsServer", 
        "imageOffer": "WindowsServer", 
        "OSDiskName": "osdiskforwindowssimple",
        "nicName": "myVMNic",
        "addressPrefix": "10.0.0.0/16", 
        "subnetName": "Subnet",
        "subnetPrefix": "10.0.0.0/24",
        "storageAccountType": "Standard_LRS",
        "publicIPAddressName": "myPublicIP",
        "publicIPAddressType": "Dynamic",
        "vmStorageAccountContainerName": "vhds",
        "vmName": "MyWindowsVM",
        "vmSize": "Standard_D1",
        "virtualNetworkName": "MyVNET",        
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
        "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables('subnetName'))]"
    },    
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('newStorageAccountName')]",
            "apiVersion": "2015-05-01-preview",
            "location": "[variables('location')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[variables('publicIPAddressName')]",
            "location": "[variables('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[variables('virtualNetworkName')]",
            "location": "[variables('location')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[variables('nicName')]",
            "location": "[variables('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "publicIPAddress": {
                                "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('publicIPAddressName'))]"
                            },
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2015-05-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[variables('vmName')]",
            "location": "[variables('location')]",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[variables('vmSize')]"
                },
                "osProfile": {
                    "computername": "[variables('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "imageReference": {
                        "publisher": "[variables('imagePublisher')]",
                        "offer": "[variables('imageOffer')]",
                        "sku" : "[parameters('windowsOSVersion')]",
                        "version":"latest"
                    },
                   "osDisk" : {
                        "name": "osdisk",
                        "vhd": {
                            "uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
                        },
                        "caching": "ReadWrite",
                        "createOption": "FromImage"
                    }
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
                        }
                    ]
                }
            }
        }
    ]
	} 


### Schritt 2: Erstellen Sie den virtuellen Computer mit der Vorlage.

Geben Sie einen Azure-Bereitstellungsnamen, einen Ressourcengruppennamen und den Standort des Azure-Rechenzentrums ein. Führen Sie anschließend diese Befehle aus.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Beim Ausführen des Befehls **New-AzureResourceGroupDeployment** werden Sie aufgefordert, Parameterwerte im Abschnitt „Parameter“ der JSON-Datei anzugeben. Wenn Sie alle erforderlichen Parameterwerte angegeben haben, werden die Ressourcengruppe und der virtuelle Computer vom Befehl erstellt.

Beispiel:

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Folgendes sollte angezeigt werden:

	cmdlet New-AzureResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: newsaacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso
	VERBOSE: 10:56:59 AM - Template is valid.
	VERBOSE: 10:56:59 AM - Create template deployment 'TestDeployment'.
	VERBOSE: 10:57:08 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 10:57:11 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is running
	VERBOSE: 10:57:11 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is running
	VERBOSE: 10:57:38 AM - Resource Microsoft.Storage/storageAccounts 'newsaacct' provisioning status is succeeded
	VERBOSE: 10:57:40 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 10:57:45 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 10:57:45 AM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 11:01:59 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded
	
	
	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 6:02:13 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    	Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     newsaacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso9875
	                    windowsOSVersion  String                     2012-R2-Datacenter
	
	Outputs           :

Nun ist ein neuer virtueller Windows-Computer mit dem Namen „MyWindowsVM“ in Ihrer neuen Ressourcengruppe vorhanden.

## <a id="customvm"></a>AUFGABE: Erstellen eines benutzerdefinierten VM-Image

Verwenden Sie die Anweisungen in diesem Abschnitt, um ein benutzerdefiniertes VM-Image in Azure mit einer Resource Manager-Vorlage mit Azure PowerShell zu erstellen. Diese Vorlage erstellt eine einzelne virtuelle Maschine von einer angegebenen virtuellen Festplatte (VHD).

### Schritt 1: Untersuchen der JSON-Datei für die Vorlage.

Dies ist der Inhalt der JSON-Datei für die Vorlage.

	{
	    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	    "contentVersion": "1.0.0.0",
	    "parameters": {
	        "osDiskVhdUri": {
	            "type": "string",
	            "metadata": {
	                "Description": "Uri of the existing VHD"
	            }
	        },
	        "osType": {
	            "type": "string",
	            "allowedValues": [
	                "windows",
	                "linux"
	            ],
	            "metadata": {
	                "Description": "Type of OS on the existing vhd"
	            }
	        },
	        "location": {
	            "type": "String",
	            "defaultValue": "West US",
	            "metadata": {
	                "Description": "Location to create the VM in"
	            }
	        },
	        "vmSize": {
	            "type": "string",
	            "defaultValue": "Standard_A2",
	            "metadata": {
	                "Description": "Size of the VM"
	            }
	        },
	        "vmName": {
	            "type": "string",
	            "defaultValue": "myVM",
	            "metadata": {
	                "Description": "Name of the VM"
	            }
	        },
	        "nicName": {
	            "type": "string",
	            "defaultValue": "myNIC",
	            "metadata": {
	                "Description": "NIC to attach the new VM to"
	            }
	        }
	    },
	    "resources": [{
	        "apiVersion": "2014-12-01-preview",
	        "type": "Microsoft.Compute/virtualMachines",
	        "name": "[parameters('vmName')]",
	        "location": "[parameters('location')]",
	        "properties": {
	            "hardwareProfile": {
	                "vmSize": "[parameters('vmSize')]"
	            },
	            "storageProfile": {
	                "osDisk": {
	                    "name": "[concat(parameters('vmName'),'-osDisk')]",
	                    "osType": "[parameters('osType')]",
	                    "caching": "ReadWrite",
	                    "vhd": {
	                        "uri": "[parameters('osDiskVhdUri')]"
	                    }
	                }
	            },
	            "networkProfile": {
	                "networkInterfaces": [{
	                    "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
	                }]
	            }
	        }
	    }]
	}

### Schritt 2: Abrufen der virtuellen Festplatte.

Informationen zu einer Windows-basierten virtuellen Maschine finden Sie in [Erstellen und Hochladen einer Windows Server-VHD in Azure](virtual-machines-create-upload-vhd-windows-server.md).

Informationen zu einer Linux-basierten virtuellen Maschine finden Sie in [Erstellen und Hochladen einer Linux-VHD in Azure](virtual-machines-linux-create-upload-vhd.md).

### Schritt 3: Erstellen Sie den virtuellen Computer mit der Vorlage.

Um eine neue virtuelle Maschine basierend auf der virtuellen Festplatte zu erstellen, ersetzen Sie die Elemente innerhalb der "< >" durch Ihre spezifischen Informationen. Führen Sie anschließend die folgenden Befehle aus:

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Sie werden aufgefordert, Parameterwerte im Abschnitt „Parameter“ der JSON-Datei anzugeben. Wenn Sie alle Parameterwerte angegeben haben, erstellt der Azure Resource Manager die Ressourcengruppe und den virtuellen Computer.

Beispiel:

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI


Sie erhalten den folgenden Informationstyp:

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	osDiskVhdUri: http://saacct.blob.core.windows.net/vhds/osdiskforwindows.vhd
	osType: windows
	location: West US
	vmSize: Standard_A3
	...

## <a id="multivm"></a>AUFGABE: Bereitstellen einer Multi-VM-Anwendung, die ein virtuelles Netzwerk und einen externen Lastenausgleich verwendet

Verwenden Sie die Anweisungen in den folgenden Abschnitten zum Bereitstellen einer Multi-VM-Anwendung, die ein virtuelles Netzwerk und einen Lastenausgleich mit einer Resource Manager-Vorlage mit Azure PowerShell verwendet. Diese Vorlage erstellt zwei virtuelle Computer in einem neuen virtuellen Netzwerk mit nur einem Subnetz im neuen Cloud-Dienst und fügt sie einem externen Lastenausgleichssatz für eingehenden Datenverkehr an TCP-Port 80 hinzu.

![](./media/virtual-machines-deploy-rmtemplates-powershell/multivmextlb.png)

Folgen Sie diesen Anweisungen zum Bereitstellen einer Multi-VM-Anwendung, die ein virtuelles Netzwerk und einen Lastenausgleich mit einer Resource Manager-Vorlage im Github-Vorlagenrepository mithilfe von Azure PowerShell-Befehlen verwendet.

### Schritt 1: Untersuchen der JSON-Datei für die Vorlage.

Dies ist der Inhalt der JSON-Datei für die Vorlage.

	{
	"$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	    "contentVersion": "1.0.0.0",
    "parameters": {
        "region": {
            "type": "string"
        },
        "storageAccountName": {
            "type": "string",
            "defaultValue": "uniqueStorageAccountName"
        },
        "adminUsername": {
            "type": "string"
        },
        "adminPassword": {
            "type": "securestring"
        },
        "dnsNameforLBIP": {
            "type": "string",
            "defaultValue": "uniqueDnsNameforLBIP"
        },
        "backendPort": {
            "type": "int",
            "defaultValue": 3389
        },
        "vmNamePrefix": {
            "type": "string",
            "defaultValue": "myVM"
        },
        "vmSourceImageName": {
            "type": "string",
            "defaultValue": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd"
        },
        "lbName": {
            "type": "string",
            "defaultValue": "myLB"
        },
        "nicNamePrefix": {
            "type": "string",
            "defaultValue": "nic"
        },
        "publicIPAddressName": {
            "type": "string",
            "defaultValue": "myPublicIP"
        },
        "vnetName": {
            "type": "string",
            "defaultValue": "myVNET"
        },
        "vmSize": {
            "type": "string",
            "defaultValue": "Standard_A1",
            "allowedValues": [
                "Standard_A0",
                "Standard_A1",
                "Standard_A2",
                "Standard_A3",
                "Standard_A4"
            ]
        }
    },
    "variables": {
        "storageAccountType": "Standard_LRS",
        "vmStorageAccountContainerName": "vhds",
        "availabilitySetName": "myAvSet",
        "addressPrefix": "10.0.0.0/16",
        "subnetName": "Subnet-1",
        "subnetPrefix": "10.0.0.0/24",
        "publicIPAddressType": "Dynamic",
        "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
        "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
        "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
        "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
        "numberOfInstances": 2,
        "nicId1": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 0))]",
        "nicId2": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'), 1))]",
        "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LBFE')]",
        "backEndIPConfigID1": "[concat(variables('nicId1'),'/ipConfigurations/ipconfig1')]",
        "backEndIPConfigID2": "[concat(variables('nicId2'),'/ipConfigurations/ipconfig1')]",
        "sourceImageName": "[concat('/', subscription().subscriptionId,'/services/images/',parameters('vmSourceImageName'))]",
        "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/LBBE')]",
        "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
    },
    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('storageAccountName')]",
            "apiVersion": "2014-12-01-preview",
            "location": "[parameters('region')]",
            "properties": {
                "accountType": "[variables('storageAccountType')]"
            }
        },
        {
            "type": "Microsoft.Compute/availabilitySets",
            "name": "[variables('availabilitySetName')]",
            "apiVersion": "2014-12-01-preview",
            "location": "[parameters('region')]",
            "properties": {}
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('region')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameforLBIP')]"
                }
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('vnetName')]",
            "location": "[parameters('region')]",
            "properties": {
                "addressSpace": {
                    "addressPrefixes": [
                        "[variables('addressPrefix')]"
                    ]
                },
                "subnets": [
                    {
                        "name": "[variables('subnetName')]",
                        "properties": {
                            "addressPrefix": "[variables('subnetPrefix')]"
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
            "location": "[parameters('region')]",
            "copy": {
                "name": "nicLoop",
                "count": "[variables('numberOfInstances')]"
            },
            "dependsOn": [
                "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                    {
                        "name": "ipconfig1",
                        "properties": {
                            "privateIPAllocationMethod": "Dynamic",
                            "subnet": {
                                "id": "[variables('subnetRef')]"
                            }
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "name": "[parameters('lbName')]",
            "type": "Microsoft.Network/loadBalancers",
            "location": "[parameters('region')]",
            "dependsOn": [
                "nicLoop",
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
            ],
            "properties": {
                "frontendIPConfigurations": [
                    {
                        "name": "LBFE",
                        "properties": {
                            "publicIPAddress": {
                                "id": "[variables('publicIPAddressID')]"
                            }
                        }
                    }
                ],
                "backendAddressPools": [
                    {
                        "name": "LBBE",
                        "properties": {
                            "backendIPConfigurations": [
                                {
                                    "id": "[variables('backEndIPConfigID1')]"
                                },
                                {
                                    "id": "[variables('backEndIPConfigID2')]"
                                }
                            ]
                        }
                    }
                ],
                "inboundNatRules": [
                    {
                        "name": "RDP-VM1",
                        "properties": {
                            "frontendIPConfigurations": [
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                }
                            ],
                            "backendIPConfiguration": {
                                "id": "[variables('backEndIPConfigID1')]"
                            },
                            "protocol": "tcp",
                            "frontendPort": 50001,
                            "backendPort": 3389,
                            "enableFloatingIP": false
                        }
                    },
                    {
                        "name": "RDP-VM2",
                        "properties": {
                            "frontendIPConfigurations": [
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                }
                            ],
                            "backendIPConfiguration": {
                                "id": "[variables('backEndIPConfigID2')]"
                            },
                            "protocol": "tcp",
                            "frontendPort": 50002,
                            "backendPort": 3389,
                            "enableFloatingIP": false
                        }
                    }
                ],
                "loadBalancingRules": [
                    {
                        "name": "LBRule",
                        "properties": {
                            "frontendIPConfigurations": [
                                {
                                    "id": "[variables('frontEndIPConfigID')]"
                                }
                            ],
                            "backendAddressPool": {
                                "id": "[variables('lbPoolID')]"
                            },
                            "protocol": "tcp",
                            "frontendPort": 80,
                            "backendPort": 80,
                            "enableFloatingIP": false,
                            "idleTimeoutInMinutes": 5,
                            "probe": {
                                "id": "[variables('lbProbeID')]"
                            }
                        }
                    }
                ],
                "probes": [
                    {
                        "name": "tcpProbe",
                        "properties": {
                            "protocol": "tcp",
                            "port": 80,
                            "intervalInSeconds": "5",
                            "numberOfProbes": "2"
                        }
                    }
                ]
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
            "copy": {
                "name": "virtualMachineLoop",
                "count": "[variables('numberOfInstances')]"
            },
            "location": "[parameters('region')]",
            "dependsOn": [
                "[concat('Microsoft.Storage/storageAccounts/', parameters('storageAccountName'))]",
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicNamePrefix'), copyindex())]",
                "[concat('Microsoft.Compute/availabilitySets/', variables('availabilitySetName'))]"
            ],
            "properties": {
                "availabilitySet": {
                    "id": "[resourceId('Microsoft.Compute/availabilitySets',variables('availabilitySetName'))]"
                },
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[concat(parameters('vmNamePrefix'), copyIndex())]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "sourceImage": {
                        "id": "[variables('sourceImageName')]"
                    },
                    "destinationVhdsContainer": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/')]"
                },
                "networkProfile": {
                    "networkInterfaces": [
                        {
                            "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
                        }
                    ]
                }
            }
        }
    ]
	}


### Schritt 2: Erstellen der Bereitstellung mit der Vorlage.

Geben Sie einen Azure-Bereitstellungsnamen, einen Ressourcengruppennamen und den Azure-Standort ein. Führen Sie anschließend die folgenden Befehle aus:

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Beim Ausführen des Befehls New-AzureResourceGroupDeployment werden Sie aufgefordert, die Parameterwerte der JSON-Datei anzugeben. Wenn Sie alle Parameterwerte angegeben haben, werden die Ressourcengruppe und die Bereitstellung vom Befehl erstellt.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Die Ausgabe sollte folgendermaßen aussehen:

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	adminUserName: WebAdmin1
	adminPassword: *******
	dnsNameforLBIP: web07
	backendPort: 80
	vmNamePrefix: WEBFARM
	...

## <a id="updatevm"></a>AUFGABE: Aktualisieren eines virtuellen Computers mit einer Ressourcen-Manager-Vorlage

Im Folgenden finden Sie ein Beispiel für die Änderung einer JSON-Vorlagendatei zum Aktualisieren der Konfigurationsdatei eines virtuellen Computers, der mit einer Ressourcen-Manager-Vorlage bereitgestellt wurde. In diesem Beispiel erstellen Sie einen virtuellen Windows-Computer und aktualisieren diesen dann so, dass die Symantec Endpoint Protection-Erweiterung installiert wird.

### Schritt 1: Erstellen des virtuellen Computers mit einer Vorlage

Erstellen Sie bei Bedarf einen Ordner auf Ihrem Computer zum Speichern der Vorlage. Geben Sie den Ordnernamen ein, und führen Sie diese Azure PowerShell-Befehle aus.

	$myFolder="<your folder path, such as C:\azure\templates\CreateVM>"
	$webClient=New-Object System.Net.WebClient
	$url="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	$filePath=$myFolder + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)
	$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.parameters.json"
	$filePath = $myFolder + "\azuredeploy.parameters.json"
	$webclient.DownloadFile($url,$filePath)

Öffnen Sie in dem Ordner die Datei "azuredeploy.parameters.json" in einem Texteditor, geben Sie Werte für die vier Parameter ein, und speichern Sie dann die Datei.

Geben Sie einen neuen Bereitstellungsnamen, einen neuen Ressourcengruppennamen und den Azure-Standort ein. Führen Sie anschließend die folgenden Befehle aus:

	$deployName="<name for the new deployment>"
	$RGName="<name for the new Resource Group>"
	$locName="<an Azure location, such as West US>"
	cd $myFolder
	Switch-AzureMode AzureResourceManager
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json

Die Ausgabe sollte folgendermaßen aussehen:

	PS C:\azure\templates\windowsvm> $deployName="winvmexttest"
	PS C:\azure\templates\windowsvm> $RGName="winvmexttest"
	PS C:\azure\templates\windowsvm> $locname="West US"
	PS C:\azure\templates\windowsvm> New-AzureResourceGroup -Name $RGName -Location $locName
	VERBOSE: 11:22:02 AM - Created resource group 'winvmexttest' in location 'westus'
	
	
	ResourceGroupName : winvmexttest
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
	                    Actions  NotActions
	                    =======  ==========
	                    *
	
	ResourceId        : /subscriptions/a58ce54a-c262-460f-b8ef-fe36e6d5f5ec/resourceGroups/winvmexttest

	PS C:\azure\templates\windowsvm> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -Template
	File azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
	VERBOSE: 11:22:05 AM - Template is valid.
	VERBOSE: 11:22:05 AM - Create template deployment 'winvmexttest'.
	VERBOSE: 11:22:14 AM - Resource Microsoft.Storage/storageAccounts 'contososa' provisioning status is running
	VERBOSE: 11:22:21 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is running
	VERBOSE: 11:22:21 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is running
	VERBOSE: 11:22:37 AM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 11:22:39 AM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 11:22:41 AM - Resource Microsoft.Storage/storageAccounts 'contososa' provisioning status is succeeded
	VERBOSE: 11:22:43 AM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 11:22:52 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 11:26:36 AM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded
	
	DeploymentName    : winvmexttest
	ResourceGroupName : winvmexttest
	ProvisioningState : Succeeded
	Timestamp         : 6/3/2015 6:26:38 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	                    Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     contososa
	                    adminUsername    String                     admin0987
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contosovm
	                    windowsOSVersion  String                     2012-R2-Datacenter
	
	Outputs           :

Stellen Sie dann über das Azure-Vorschauportal eine Verbindung mit dem virtuellen Computer her (**Durchsuchen > Virtuelle Computer (v2) >** *VM-Name* **> Verbinden**).

Geben Sie auf dem Startbildschirm **Symantec** ein. Sie werden feststellen, dass die Komponenten von Symantec Endpoint Protection nicht installiert sind (keine Ergebnisse mit "Symantec" im Titel).

Schließen Sie die Remotedesktopverbindung.

### Schritt 2: Ändern der Datei "azuredeploy.json" zum Hinzufügen der Symantec Endpoint Protection-Erweiterung

Öffnen Sie im Ordner die Datei "azuredeploy.json" mit einem beliebigen Texteditor. Fügen Sie im Abschnitt **variables** die folgende Zeile direkt nach der Zeile hinzu, in der die PublicIPAddressType-Variable definiert wird:

	"vmExtensionName" : "SymantecExtension",

Fügen Sie im Abschnitt **resources** den folgenden neuen Abschnitt unmittelbar vor der Zeile mit der letzten eckigen, nach links geöffneten Klammer "]" hinzu:

	       {
	         "type": "Microsoft.Compute/virtualMachines/extensions",
	        "name": "[concat(variables('vmName'),'/', variables('vmExtensionName'))]",
	        "apiVersion": "2014-12-01-preview",
	        "location": "[variables('location')]",
	        "dependsOn": [
	            "[concat('Microsoft.Compute/virtualMachines/', variables('vmName'))]"
	        ],
	        "properties": {
	            "publisher": "Symantec",
	            "type": "SymantecEndpointProtection",
	            "typeHandlerVersion": "12.1",
	            "settings": null,
	            "protectedSettings": null
	        }
	    }

Speichern Sie die Datei "azuredeploy.json" mit diesen neuen Änderungen. Stellen Sie mithilfe dieses Befehls sicher, dass die Änderungen ordnungsgemäß vorgenommen wurden.

	Test-AzureResourceGroupTemplate -ResourceGroupName $RGName -TemplateFile azuredeploy.json

Wenn Sie die Änderungen ordnungsgemäß vorgenommen haben, sollte Folgendes angezeigt werden.

	Template is valid.

Wenn diese Meldung nicht angezeigt wird, analysieren Sie die Fehlermeldung, um die Ursache des Fehlers festzustellen.

### Schritt 3: Ausführen der geänderten Vorlage zum Hinzufügen der Symantec Endpoint Protection-Erweiterung

Führen Sie diesen Befehl in der Azure PowerShell-Eingabeaufforderung aus.

	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json

Die Ausgabe sollte folgendermaßen aussehen:

	PS C:\azure\templates\winvmext> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateF	ile azuredeploy.json -TemplateParameterFile azuredeploy.parameters.json
	VERBOSE: 12:49:42 PM - Template is valid.
	VERBOSE: 12:49:42 PM - Create template deployment 'winvmexttest'.
	VERBOSE: 12:49:45 PM - Resource Microsoft.Network/publicIPAddresses 'myPublicIP' provisioning status is succeeded
	VERBOSE: 12:49:45 PM - Resource Microsoft.Network/virtualNetworks 'MyVNET' provisioning status is succeeded
	VERBOSE: 12:49:47 PM - Resource Microsoft.Storage/storageAccounts 'contososa' provisioning status is succeeded
	VERBOSE: 12:49:49 PM - Resource Microsoft.Network/networkInterfaces 'myVMNic' provisioning status is succeeded
	VERBOSE: 12:49:51 PM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is running
	VERBOSE: 12:50:08 PM - Resource Microsoft.Compute/virtualMachines 'MyWindowsVM' provisioning status is succeeded
	VERBOSE: 12:50:15 PM - Resource Microsoft.Compute/virtualMachines/extensions 'MyWindowsVM/SymantecExtension'	provisioning status is running
	VERBOSE: 12:53:07 PM - Resource Microsoft.Compute/virtualMachines/extensions 'MyWindowsVM/SymantecExtension' provisioning status is succeeded
	
	
	DeploymentName    : winvmexttest
	ResourceGroupName : winvmexttest
	ProvisioningState : Succeeded
	Timestamp         : 6/3/2015 7:53:07 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
	                    Name             Type                       Value
	                    ===============  =========================  ==========
	                    newStorageAccountName  String                     contososa
	                    adminUsername    String                     admin0987
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contosovm
	                    windowsOSVersion  String                     2012-R2-Datacenter
	
	Outputs           :

Stellen Sie über das Azure-Vorschauportal eine Verbindung mit dem virtuellen Computer her (**Durchsuchen > Virtuelle Computer (v2) >** *VM-Name* **> Verbinden**).

Geben Sie auf dem Startbildschirm **Symantec** ein. Die Anzeige sollte der Folgenden entsprechen, die angibt, dass die Symantec Endpoint Protection-Erweiterung jetzt installiert ist.

![](./media/virtual-machines-deploy-rmtemplates-powershell/SymantecExt.png)

## <a id="removerg"></a>AUFGABE: Entfernen einer Ressourcengruppe

Sie können jede von Ihnen erstellte Ressourcengruppe mit dem Befehl **Remove-AzureResourceGroup** entfernen. Ersetzen Sie alles innerhalb der Anführungszeichen einschließlich der Zeichen < and > durch den korrekten Namen.

	Remove-AzureResourceGroup  -Name "<resource group name>"

Informationen werden wie folgt angezeigt:

	Confirm
	Are you sure you want to remove resource group 'BuildRG'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

## <a id="logon"></a>AUFGABE: Anmelden bei einem virtuellen Windows-Computer

Klicken Sie im [Azure-Vorschauportal](https://portal.azure.com/) auf **Alle durchsuchen > Virtuelle Computer (v2) >** *VM-Name* **> Verbinden**.

Bei der Aufforderung zum Öffnen oder Speichern einer RDP-Datei klicken Sie auf **Öffnen** und dann auf **Verbinden**. Geben Sie die Anmeldeinformationen eines gültigen Kontos ein, und klicken Sie dann auf **OK**.

Wenn Sie gefragt werden, ob trotz der Zertifikatfehler eine Verbindung hergestellt werden soll, klicken Sie auf **Ja**.

## <a id="displayvm"></a>AUFGABE: Anzeigen von Informationen zu einem virtuellen Computer

Erhalten Sie Informationen zu einem virtuellen Computer mithilfe des Befehls **Get-AzureVM**. Dieser Befehl gibt ein VM-Objekt zurück, das mithilfe verschiedener anderer Cmdlets manipuliert werden kann, um den Status der VM zu aktualisieren. Ersetzen Sie alles in den Anführungszeichen, einschließlich der Zeichen < and >, durch die korrekten Namen.

	Get-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

Folgende Informationen zu Ihrem virtuellen Computer werden angezeigt:

	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          : {
	                             "VirtualMachineSize": "Standard_D1"
	                           }
	Id                       : /subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/BuildRG/providers/Microso
	                           ft.Compute/virtualMachines/MyWindowsVM
	InstanceView             : null
	Location                 : westus
	Name                     : MyWindowsVM
	NetworkProfile           : {
	                             "NetworkInterfaces": [
	                               {
	                                 "Primary": null,
	                                 "ReferenceUri": "/subscriptions/fd92919d-eeca-4f5b-840a-e45c6770d92e/resourceGroups/Bu
	                           ildRG/providers/Microsoft.Network/networkInterfaces/myVMNic"
	                               }
	                             ]
	                           }
	OSProfile                : {
	                             "AdminPassword": null,
	                             "AdminUsername": "WinAdmin1",
	                             "ComputerName": "MyWindowsVM",
	                             "CustomData": null,
	                             "LinuxConfiguration": null,
	                             "Secrets": [],
	                             "WindowsConfiguration": {
	                               "AdditionalUnattendContents": [],
	                               "EnableAutomaticUpdates": true,
	                               "ProvisionVMAgent": true,
	                               "TimeZone": null,
	                               "WinRMConfiguration": null
	                             }
	                           }
	Plan                     : null
	ProvisioningState        : Succeeded
	StorageProfile           : {
	                             "DataDisks": [],
	                             "ImageReference": {
	                               "Offer": "WindowsServer",
	                               "Publisher": "MicrosoftWindowsServer",
	                               "Sku": "2012-R2-Datacenter",
	                               "Version": "latest"
	                             },
	                             "OSDisk": {
	                               "OperatingSystemType": "Windows",
	                               "Caching": "ReadWrite",
	                               "CreateOption": "FromImage",
	                               "Name": "osdisk",
	                               "SourceImage": null,
	                               "VirtualHardDisk": {
	                                 "Uri": "http://buildsaacct.blob.core.windows.net/vhds/osdiskforwindowssimple.vhd"
	                               }
	                             },
	                             "SourceImage": null
	                           }
	Tags                     : {}
	Type                     : Microsoft.Compute/virtualMachines


## <a id="start"></a>AUFGABE: Starten eines virtuellen Computers

Starten Sie einen virtuellen Computer mithilfe des Befehls **Start-AzureVM**. Ersetzen Sie alles in den Anführungszeichen, einschließlich der Zeichen < and >, durch die korrekten Namen.

	Start-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

Informationen werden wie folgt angezeigt:

	EndTime             : 4/28/2015 11:11:41 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	Status              : Succeeded
	TrackingOperationId : e1705973-d266-467e-8655-920016145347
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	StatusCode          : OK

## <a id="stop"></a>AUFGABE: Beenden eines virtuellen Computers

Beenden Sie einen virtuellen Computer mithilfe des Befehls **Stop-AzureVM**. Ersetzen Sie alles in den Anführungszeichen, einschließlich der Zeichen < and >, durch die korrekten Namen.

	Stop-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

Informationen werden wie folgt angezeigt:

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
	
	
	EndTime             : 4/28/2015 11:09:08 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:06:55 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 0c94dc74-c553-412c-a187-108bdb29657e
	RequestId           : 5cc9ddba-0643-4b5e-82b6-287b321394ee
	StatusCode          : OK

## <a id=restart"></a>AUFGABE: Neustarten eines virtuellen Computers

Starten Sie einen virtuellen Computer mithilfe des Befehls **Restart-AzureVM** neu. Ersetzen Sie alles innerhalb der Anführungszeichen einschließlich der Zeichen < and > durch den korrekten Namen.

	Restart-AzureVM -ResourceGroupName "<resource group name>" -Name "<VM name>"

Informationen werden wie folgt angezeigt:

	EndTime             : 4/28/2015 11:16:26 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:16:25 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 390571e0-c804-43ce-88c5-f98e0feb588e
	RequestId           : 7dac33e3-0164-4a08-be33-96205284cb0b
	StatusCode          : OK

## <a id=delete"></a>AUFGABE: Löschen eines virtuellen Computers

Löschen Sie einen virtuellen Computer mithilfe des Befehls **Remove-AzureVM**. Ersetzen Sie alles innerhalb der Anführungszeichen einschließlich der Zeichen < and > durch den korrekten Namen. Mit dem Parameter **- Force** können Sie die Bestätigungsaufforderung überspringen.

	Remove-AzureVM -ResourceGroupName "<resource group name>" –Name "<VM name>"

Informationen werden wie folgt angezeigt:

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):
	
	
	EndTime             : 4/28/2015 11:21:55 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:20:13 AM -07:00
	Status              : Succeeded
	TrackingOperationId : f74fad9e-f6bc-46ae-82b1-bfad3952aa44
	RequestId           : 6a30d2e0-63ca-43cf-975b-058631e048e7
	StatusCode          : OK

## Zusätzliche Ressourcen

[Azure Compute-, Network- and Storage-Anbieter unter dem Azure-Ressourcen-Manager](virtual-machines-azurerm-versus-azuresm.md)

[Übersicht über den Azure Resource Manager](../resource-group-overview.md)

[Bereitstellen und Verwalten von virtuellen Computern mit Azure Resource Manager-Vorlagen und der Azure-CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)

[Dokumentation zu virtuellen Computern](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md)

<!---HONumber=July15_HO2-->