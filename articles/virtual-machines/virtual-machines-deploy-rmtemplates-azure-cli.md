<properties 
	pageTitle="Bereitstellen und Verwalten von virtuellen Azure-Computern mit Resource Manager-Vorlagen und der Azure-CLI für Mac, Linux und Windows" 
	description="Stellen Sie ganz einfach den am häufigsten verwendeten Konfigurationssatz für virtuelle Azure-Computer bereit und verwalten Sie sie mithilfe von Resource Manager-Vorlagen und der Azure CLI." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="squillace" 
	manager="timlt" 
	editor=""/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="05/11/2015" 
	ms.author="rasquill"/>

# Bereitstellen und Verwalten von virtuellen Computern mit Azure Resource Manager-Vorlagen und der Azure-CLI

In diesem Artikel wird gezeigt, wie mit Azure Resource Manager-Vorlagen und der Azure-Befehlszeilenschnittstelle (Azure-CLI) die folgenden allgemeinen Aufgaben zum Bereitstellen und Verwalten von Azure Virtual Machines automatisiert werden. Weitere Vorlagen finden Sie unter [Azure-Schnellstartvorlagen](http://azure.microsoft.com/documentation/templates/) und [App-Frameworks](virtual-machines-app-frameworks.md).

- [Schnelles Erstellen eines virtuellen Computers in Azure](#quick-create-a-vm-in-azure)
- [Bereitstellen eines virtuellen Computers in Azure aus einer Vorlage](#deploy-a-vm-in-azure-from-a-template)
- [Erstellen eines virtuellen Computers von einem benutzerdefinierten Image](#create-a-custom-vm-image) 
- [Bereitstellen eines virtuellen Computers mit virtuellem Netzwerk und Lastenausgleichsmodul](#deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer)
- [Entfernen einer Ressourcengruppe](#remove-a-resource-group)
- [Anzeigen des Protokolls für die Ressourcengruppenbereitstellung](#show-the-log-for-a-resource-group-deployment)
- [Anzeigen von Informationen zu einem virtuellen Computer](#display-information-about-a-virtual-machine)
- [Anmelden bei einem auf Linux basierten virtuellen Computer](#log-on-to-a-linux-based-virtual-machine)
- [Beenden eines virtuellen Computers](#stop-a-virtual-machine)
- [Starten eines virtuellen Computers](#start-a-virtual-machine)
- [Anfügen eines Datenträgers](#attach-a-data-disk)

## Vorbereitung

Vor der Verwendung der Azure-Befehlszeilenschnittstelle mit Azure Ressourcengruppen benötigen Sie die richtige Version der Azure-CLI und eine Arbeits- oder Schul-ID (auch als "Organisations-ID" bezeichnet).

### Aktualisieren Ihrer Azure-CLI auf Version 0.9.0 oder höher

Geben Sie `azure --version` ein, um zu prüfen, ob Sie bereits Version 0.9.0 installiert haben.

	azure --version
    0.9.0 (node: 0.10.25)

Wenn Ihre Version nicht 0.9.0 oder höher ist, müssen Sie entweder die [Azure-CLI installieren](../xplat-cli-install.md) oder sie mithilfe eines der systemeigenen Installer oder über eine **Npm** durch Eingabe von `npm update -g azure-cli` aktualisieren.

Sie können die Azure-CLI mit dem folgenden [Docker-Image](https://registry.hub.docker.com/u/microsoft/azure-cli/) auch als Docker-Container ausführen. Führen Sie von einem Docker-Host den folgenden Befehl aus:

	docker run -it microsoft/azure-cli

### Festlegen Ihres Azure-Kontos und -Abonnements

Wenn Sie noch kein Azure-Abonnement aber ein MDSN-Abonnement haben, können Sie Ihre [MSDN-Abonnentenvorteile](http://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/) aktivieren. Oder Sie registrieren sich für eine [kostenlose Testversion](http://azure.microsoft.com/pricing/free-trial/).

Sie benötigen ein Arbeits- oder Schulkonto, um Azure Resource Management-Vorlagen zu verwenden. Wenn Sie eins besitzen, geben Sie `azure login` ein sowie Ihren Benutzernamen und das Kennwort. Sie sollten sich erfolgreich anmelden können.

> [AZURE.NOTE]Wenn Sie keins besitzen, wird Ihnen die Fehlermeldung angezeigt, dass Sie eine andere Art von Konto benötigen. Informationen zum Erstellen eines Kontos auf der Basis Ihres aktuellen Azure-Kontos finden Sie unter [Erstellen einer Arbeits- oder Schulidentität in Azure Active Directory](resource-group-create-work-id-from-personal.md).

Ihr Konto kann über mehrere Abonnements verfügen. Sie können Ihre Abonnements durch Eingabe von `azure account list` auflisten, was etwa wie folgt aussieht:

    azure account list
    info:    Executing command account list
    data:    Name                              Id                                    Tenandt Id                            Current
    data:    --------------------------------  ------------------------------------  ------------------------------------  -------
    data:    Contoso Admin                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  true   
    data:    Fabrikam dev                      xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
    data:    Fabrikam test                     xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
    data:    Contoso production                xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx  false  
    
Sie können das aktuelle Azure-Abonnement durch Eingabe von

	azure account set <subscription name or ID> true

mit dem Abonnementnamen oder der ID festlegen, die über die Ressourcen verfügt, die Sie verwalten möchten.

### Wechseln in den Azure CLI-Ressourcengruppenmodus

Standardmäßig beginnt die Azure-CLI im Verwaltungsdienst-Modus (**Asm**-Modus). Typ

	azure config mode arm

um in den Ressourcengruppen-Modus zu wechseln.

> [AZURE.NOTE]Sie können zum Standardbefehlssatz wechseln, indem Sie `azure config mode asm` eingeben.

## Grundlegendes zu Azure-Ressourcenvorlagen und -Ressourcengruppen

Die meisten Anwendungen sind aus einer Kombination aus verschiedenen Ressourcentypen (z. B. als eine oder mehrere VMs und Speicherkonten, SQL-Datenbank, virtuelles Netzwerk oder ein Content Delivery Network oder *CDN*) erstellt. Die standardmäßige Azure-Dienstverwaltungs-API und das klassische Azure-Portal stellten diese Elemente mit dem Dienst-Ansatz dar. Das erfordert das Bereitstellen und Verwalten jedes einzelnen Dienstes individuell (wenn dies nicht andere Tools übernehmen), und nicht als eine einzige logische Einheit zur Bereitstellung.

*Azure Resource Manager-Vorlagen* ermöglichen es Ihnen, diese verschiedenen Ressourcen als eine logische Bereitstellungseinheit auf deklarative Weise bereitzustellen und zu verwalten. Anstatt Azure imperativisch mitzuteilen, wie ein Befehl nach dem anderen bereitgestellt wird, beschreiben Sie die gesamte Bereitstellung in einer JSON-Datei – alle Ressourcen und zugeordnete Konfigurations- und Bereitstellungsparameter – und geben an Azure weiter, dass diese Ressourcen als eine Gruppe bereitgestellt werden sollen.

Sie können dann den gesamten Lebenszyklus der Gruppenressourcen mithilfe von Ressourcenverwaltungsbefehlen in Azure CLI verwalten:

- Alle Ressourcen in der Gruppe auf einmal beenden, starten oder löschen. 
- Role-Based Access Control (RBAC)-Regeln zum Sperren von Sicherheitsberechtigungen. 
- Vorgänge zu überwachen. 
- Ressourcen mit zusätzlichen Metadaten zur besseren Nachverfolgung auszuzeichnen. 

Erfahren Sie mehr über Azure-Ressourcengruppen und was diese für Sie tun können [hier](../resource-group-overview.md). Wenn Sie das Erstellen von Vorlagen interessiert, sehen Sie in [Azure Resource Manager-Vorlagen erstellen](../resource-group-authoring-templates.md) nach.

## <a id="quick-create-a-vm-in-azure"></a>AUFGABE: schnelles Erstellen eines virtuellen Computers in Azure

Manchmal wissen Sie, welches Image Sie benötigen, Sie benötigen eine VM von dem Image in diesem Moment und die Infrastruktur ist Ihnen dabei egal – vielleicht müssen Sie etwas auf einer sauberen VM testen. Dann verwenden Sie den Befehl `azure vm quick-create` und übergehen die zum Erstellen eines virtuellen Computers und seiner Infrastruktur erforderlichen Argumente.

Erstellen Sie zunächst die Ressourcengruppe.

    azure group create coreos-quick westus
    info:    Executing command group create
    + Getting resource group coreos-quick                                          
    + Creating resource group coreos-quick                                         
    info:    Created resource group coreos-quick
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick
    data:    Name:                coreos-quick
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: 
    data:    
    info:    group create command OK
    

Zweitens benötigen Sie ein Image. Um ein Image mit der Azure CLI zu finden, gehen Sie zu [Navigieren und Aussuchen von Azure VM-Datenträgerabbildern mit PowerShell und der Azure-Befehlszeilenschnittstelle](resource-groups-vm-searching.md). Doch für diesen Schnellstart finden Sie hier eine kurze Liste beliebter Images. Für diese Schnellerfassung verwenden wir das CoreOSs Stable-Image.

> [AZURE.NOTE]Sie können für "ComputeImageVersion" sowohl in der Vorlagensprache als auch in der Azure-Befehlszeilenschnittstelle auch einfach "latest" als Parameter angeben. Dies ermöglicht es Ihnen, immer die neueste gepatchte Version des Image zu verwenden, ohne Ihre Skripts oder Vorlagen ändern zu müssen. Dies wird nachfolgend gezeigt.

| PublisherName | Angebot | Sku | Version |
|:---------------------------------|:-------------------------------------------|:---------------------------------|:--------------------|
| OpenLogic | CentOS | 7 | 7.0.201503 |
| OpenLogic | CentOS | 7.1 | 7.1.201504 |
| CoreOS | CoreOS | Beta | 647.0.0 |
| CoreOS | CoreOS | Stable | 633.1.0 |
| MicrosoftDynamicsNAV | DynamicsNAV | 2015 | 8.0.40459 |
| MicrosoftSharePoint | MicrosoftSharePointServer | 2013 | 1.0.0 |
| msopentech | Oracle-Database-12c-Weblogic-Server-12c | Standard | 1.0.0 |
| msopentech | Oracle-Database-12c-Weblogic-Server-12c | Enterprise | 1.0.0 |
| MicrosoftSQLServer | SQL 2014 WS2012R2 | Enterprise-Optimized-for-DW | 12.0.2430 |
| MicrosoftSQLServer | SQL 2014 WS2012R2 | Enterprise-Optimized-for-OLTP | 12.0.2430 |
| Canonical | UbuntuServer | 12.04.5-LTS | 12.04.201504230 |
| Canonical | UbuntuServer | 14.04.2-LTS | 14.04.201503090 |
| MicrosoftWindowsServer | Windows Server | 2012-Datacenter | 3.0.201503 |
| MicrosoftWindowsServer | Windows Server | 2012-R2-Datacenter | 4.0.201503 |
| MicrosoftWindowsServer | Windows Server | Windows-Server-Technical-Preview | 5.0.201504 |
| MicrosoftWindowsServerEssentials | WindowsServerEssentials | WindowsServerEssentials | 1.0.141204 |
| MicrosoftWindowsServerHPCPack | WindowsServerHPCPack | 2012R2 | 4.3.4665 |

Erstellen Sie Ihren virtuellen Computer einfach durch Eingabe von `azure vm quick-create command`, sodass er für Eingabeaufforderungen bereit ist. Das sollte ungefähr so aussehen.

    azure vm quick-create 
    info:    Executing command vm quick-create
    Resource group name: coreos-quick
    Virtual machine name: coreos
    Location name: westus
    Operating system Type [Windows, Linux]: linux
    ImageURN (format: "publisherName:offer:skus:version"): coreos:coreos:stable:latest
    User name: ops
    Password: *********
    Confirm password: *********
    + Looking up the VM "coreos"                                                   
    info:    Using the VM Size "Standard_A1"
    info:    The [OS, Data] Disk or image configuration requires storage account
    + Retrieving storage accounts                                                  
    info:    Could not find any storage accounts in the region "westus", trying to create new one
    + Creating storage account "cli9fd3fce49e9a9b3d14302" in "westus"              
    + Looking up the storage account cli9fd3fce49e9a9b3d14302                      
    + Looking up the NIC "coreo-westu-1430261891570-nic"                           
    info:    An nic with given name "coreo-westu-1430261891570-nic" not found, creating a new one
    + Looking up the virtual network "coreo-westu-1430261891570-vnet"              
    info:    Preparing to create new virtual network and subnet
    / Creating a new virtual network "coreo-westu-1430261891570-vnet" [address prefix: "10.0.0.0/16"] with subnet "coreo-westu-1430261891570-sne+" [address prefix: "10.0.1.0/24"]
    + Looking up the virtual network "coreo-westu-1430261891570-vnet"              
    + Looking up the subnet "coreo-westu-1430261891570-snet" under the virtual network "coreo-westu-1430261891570-vnet"
    info:    Found public ip parameters, trying to setup PublicIP profile
    + Looking up the public ip "coreo-westu-1430261891570-pip"                     
    info:    PublicIP with given name "coreo-westu-1430261891570-pip" not found, creating a new one
    + Creating public ip "coreo-westu-1430261891570-pip"                           
    + Looking up the public ip "coreo-westu-1430261891570-pip"                     
    + Creating NIC "coreo-westu-1430261891570-nic"                                 
    + Looking up the NIC "coreo-westu-1430261891570-nic"                           
    + Creating VM "coreos"                                                         
    + Looking up the VM "coreos"                                                   
    + Looking up the NIC "coreo-westu-1430261891570-nic"                           
    + Looking up the public ip "coreo-westu-1430261891570-pip"                     
    data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick/providers/Microsoft.Compute/virtualMachines/coreos
    data:    ProvisioningState               :Succeeded
    data:    Name                            :coreos
    data:    Location                        :westus
    data:    FQDN                            :coreo-westu-1430261891570-pip.westus.cloudapp.azure.com
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:    
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:    
    data:    Storage Profile:
    data:      Image reference:
    data:        Publisher                   :coreos
    data:        Offer                       :coreos
    data:        Sku                         :stable
    data:        Version                     :633.1.0
    data:    
    data:      OS Disk:
    data:        OSType                      :Linux
    data:        Name                        :cli9fd3fce49e9a9b3d-os-1430261892283
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :https://cli9fd3fce49e9a9b3d14302.blob.core.windows.net/vhds/cli9fd3fce49e9a9b3d-os-1430261892283.vhd
    data:    
    data:    OS Profile:
    data:      Computer Name                 :coreos
    data:      User Name                     :ops
    data:      Linux Configuration:
    data:        Disable Password Auth       :false
    data:    
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/coreos-quick/providers/Microsoft.Network/networkInterfaces/coreo-westu-1430261891570-nic
    data:          Primary                   :true
    data:          MAC Address               :00-0D-3A-30-72-E3
    data:          Provisioning State        :Succeeded
    data:          Name                      :coreo-westu-1430261891570-nic
    data:          Location                  :westus
    data:            Private IP alloc-method :Dynamic
    data:            Private IP address      :10.0.1.4
    data:            Public IP address       :104.40.24.124
    data:            FQDN                    :coreo-westu-1430261891570-pip.westus.cloudapp.azure.com
    info:    vm quick-create command OK
    
Und los geht's mit Ihrem neuen virtuellen Computer.

## <a id="deploy-a-vm-in-azure-from-a-template"></a>AUFGABE: Bereitstellen eines virtuellen Computers in Azure aus einer Vorlage

Verwenden Sie die Anweisungen in den folgenden Abschnitten zum Bereitstellen einer neuen Azure-VM mithilfe einer Vorlage mit der Azure-Befehlszeilenschnittstelle. Diese Vorlage erstellt einen einzelnen virtuellen Computer in einem neuen virtuellen Netzwerk mit nur einem Subnetz und ermöglicht Ihnen im Gegensatz zu `azure vm quick-create` die genaue Beschreibung Ihrer Anforderungen, ohne Fehler zu wiederholen. Durch die Vorlage wird Folgendes erstellt:

![](./media/virtual-machines-deploy-rmtemplates-azure-cli/new-vm.png)
 
### Schritt 1: Untersuchen der JSON-Datei für die Vorlagenparameter

Dies ist der Inhalt der JSON-Datei für die Vorlage. (Die Vorlage befindet sich auch [hier](https://github.com/Azure/azure-quickstart-templates/blob/master/101-simple-linux-vm/azuredeploy.json) in GitHub.)

Vorlagen sind flexibel, und der Entwickler hat sich möglicherweise entschieden, viele Parameter zur Verfügung zu stellen oder nur einige wenige, indem eine Vorlage erstellt wurde, die stärker festgelegt ist. Um die Informationen zu sammeln, müssen Sie die Vorlage als Parameter übergeben, die Vorlagendatei öffnen (unten in diesem Thema finden Sie ein Vorlagen-Inline), und die **Parameter**werte untersuchen.

In diesem Fall fragt die untere Vorlage nach:

- einem eindeutigen Speicherkontonamen
- einem Administratorbenutzernamen für den virtuellen Computer
- einem Kennwort
- einem Domänennamen, den Externe verwenden können
- und akzeptiert die Versionsnummer eines Ubuntu-Servers – jedoch nur eine von einer Liste. 

Sobald Sie diese Werte eingegeben haben, können Sie eine Gruppe erstellen und die Vorlage in Ihrem Azure-Abonnement bereitstellen.

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
                   "Description": "User name for the Virtual Machine."
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
            "ubuntuOSVersion": {
                "type": "string",
                "defaultValue": "14.10",
                "allowedValues": [
                    "12.04.2-LTS",
                    "12.04.3-LTS",
                    "12.04.4-LTS",
                    "12.04.5-LTS",
    				"12.10",
                    "14.04.2-LTS",
                    "14.10",
                    "15.04"
                ],
                "metadata": {
                    "Description": "The Ubuntu version for the VM. This will pick a fully patched image of this given Ubuntu version. Allowed values: 12.04.2-LTS, 12.04.3-LTS, 12.04.4-LTS, 12.04.5-LTS, 12.10, 14.04.2-LTS, 14.10, 15.04."
                }
            }
        },
        "variables": {
            "location": "West US",
            "imagePublisher": "Canonical", 
            "imageOffer": "UbuntuServer", 
            "OSDiskName": "osdiskforlinuxsimple",
            "nicName": "myVMNic",
            "addressPrefix": "10.0.0.0/16", 
            "subnetName": "Subnet",
            "subnetPrefix": "10.0.0.0/24",
            "storageAccountType": "Standard_LRS",
            "publicIPAddressName": "myPublicIP",
            "publicIPAddressType": "Dynamic",
            "vmStorageAccountContainerName": "vhds",
            "vmName": "MyUbuntuVM",
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
                            "sku" : "[parameters('ubuntuOSVersion')]",
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
  
### Schritt 2: Erstellen Sie den virtuellen Computer mit der Vorlage

Sobald Sie die Parameterwerte bereit haben, müssen Sie für die Vorlagenbereitstellung eine Ressourcengruppe erstellen und dann die Vorlage bereitstellen.

Geben Sie zum Erstellen der Ressourcengruppe `azure group create <group name> <location>` mit dem Namen der gewünschten Gruppe ein und den Ort des Rechenzentrums, in das Sie bereitstellen möchten. Dies geschieht schnell:

    azure group create myResourceGroup westus
    info:    Executing command group create
    + Getting resource group myResourceGroup                                       
    + Creating resource group myResourceGroup                                      
    info:    Created resource group myResourceGroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroup
    data:    Name:                myResourceGroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: 
    data:    
    info:    group create command OK
    

Erstellen Sie nun die Bereitstellung, rufen Sie `azure group deployment create` auf und übergeben Sie:

- die Vorlagendatei (im Fall, dass Sie die oben genannte JSON-Vorlage in einer lokalen Datei gespeichert haben) 
- eine Uri-Vorlage (im Fall, dass Sie auf die Datei in Github oder einigen anderen Webadressen zeigen möchten)
- die Ressourcengruppe, in die Sie bereitstellen möchten
- und einen optionalen Bereitstellungsnamen. 

Sie werden aufgefordert, Parameterwerte im Abschnitt **Parameter** der JSON-Datei anzugeben. Wenn Sie alle Parameterwerte angegeben haben, beginnt die Bereitstellung.

Beispiel:

    azure group deployment create --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json myResourceGroup firstDeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    newStorageAccountName: storageaccount
    adminUsername: ops
    adminPassword: password
    dnsNameForPublicIP: newdomainname
    
Sie erhalten den folgenden Informationstyp:

    + Initializing template configurations and parameters                          
    + Creating a deployment                                                        
    info:    Created template deployment "firstDeployment"
    + Registering providers                                                        
    info:    Registering provider microsoft.storage
    info:    Registering provider microsoft.network
    info:    Registering provider microsoft.compute
    + Waiting for deployment to complete                                           
    data:    DeploymentName     : firstDeployment
    data:    ResourceGroupName  : myResourceGroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-04-28T07:53:55.1828878Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-linux-vm/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                   Type          Value        
    data:    ---------------------  ------------  -------------
    data:    newStorageAccountName  String        storageaccount
    data:    adminUsername          String        ops          
    data:    adminPassword          SecureString  undefined    
    data:    dnsNameForPublicIP     String        newdomainname   
    data:    ubuntuOSVersion        String        14.10        
    info:    group deployment create command OK
    


## <a id="create-a-custom-vm-image"></a>AUFGABE: Erstellen eines benutzerdefinierten VM-Image

Sie haben oben die grundlegende Verwendung von Vorlagen gesehen, sodass wir ähnliche Anweisungen zum Erstellen eines benutzerdefinierten virtuellen Computers aus einer spezifischen VHD-Datei in Azure mit einer Azure CLI-Vorlage verwenden können. Der Unterschied ist hier, dass diese Vorlage eine einzelne virtuelle Maschine von einer angegebenen virtuellen Festplatte (VHD) erstellt.

### Schritt 1: Untersuchen der JSON-Datei für die Vorlage

Hier sind die Inhalte der JSON-Datei für die Vorlage, die in diesem Abschnitt als Beispiel verwendet werden. Sie finden die Vorlage selbst aber immer [hier](https://raw.githubusercontent.com/azurermtemplates/azurermtemplates/master/101-vm-from-user-image/azuredeploy.json).

Auch hier müssen Sie die Werte finden, die Sie für die Parameter eingeben, die keine Standardwerte besitzen. Beim Ausführen des Befehls `azure group deployment create` fordert die Azure-CLI Sie dazu auf, diese Werte eingeben.

    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
        "contentVersion": "1.0.0.0",
        "parameters" : {
            "userImageStorageAccountName": {
                "type": "string",
                "defaultValue" : "userImageStorageAccountName"
            },
            "userImageStorageContainerName" : {
                "type" : "string",
                "defaultValue" : "userImageStorageContainerName"
            },
            "userImageVhdName" : {
                "type" : "string",
                "defaultValue" : "userImageVhdName"
            },
            "dnsNameForPublicIP" : {
                "type" : "string",
                "defaultValue": "uniqueDnsNameForPublicIP"
            },
            "adminUserName": {
                "type": "string"
            },
            "adminPassword": {
                "type": "securestring"
            },
            "osType" : {
                "type" : "string",
                "allowedValues" : [
                    "windows",
                    "linux"
                ]
            },
            "subscriptionId":{
                "type" : "string"
            },
            "location": {
                "type": "String",
                "defaultValue" : "West US"
            },
            "vmSize": {
                "type": "string",
                "defaultValue": "Standard_A2"
            },
            "publicIPAddressName": {
                "type": "string",
                "defaultValue" : "myPublicIP"
            },
            "vmName": {
                "type": "string",
                "defaultValue" : "myVM"
            },
            "virtualNetworkName":{
                "type" : "string",
                "defaultValue" : "myVNET"
            },
            "nicName":{
                "type" : "string",
                "defaultValue":"myNIC"
            }
        },
        "variables": {
            "addressPrefix":"10.0.0.0/16",
            "subnet1Name": "Subnet-1",
            "subnet2Name": "Subnet-2",
            "subnet1Prefix" : "10.0.0.0/24",
            "subnet2Prefix" : "10.0.1.0/24",
            "publicIPAddressType" : "Dynamic",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]",
            "userImageName" : "[concat('http://',parameters('userImageStorageAccountName'),'.blob.core.windows.net/',parameters('userImageStorageContainerName'),'/',parameters('userImageVhdName'))]",
            "osDiskVhdName" : "[concat('http://',parameters('userImageStorageAccountName'),'.blob.core.windows.net/',parameters('userImageStorageContainerName'),'/',parameters('vmName'),'osDisk.vhd')]"
        },
        "resources": [
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
                "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                "dnsSettings": {
                    "domainNameLabel": "[parameters('dnsNameForPublicIP')]"
                }
            }
        },
        {
          "apiVersion": "2014-12-01-preview",
          "type": "Microsoft.Network/virtualNetworks",
          "name": "[parameters('virtualNetworkName')]",
          "location": "[parameters('location')]",
          "properties": {
            "addressSpace": {
              "addressPrefixes": [
                "[variables('addressPrefix')]"
              ]
            },
            "subnets": [
              {
                "name": "[variables('subnet1Name')]",
                "properties" : {
                    "addressPrefix": "[variables('subnet1Prefix')]"
                }
              },
              {
                "name": "[variables('subnet2Name')]",
                "properties" : {
                    "addressPrefix": "[variables('subnet2Prefix')]"
                }
              }
            ]
          }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
                "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
                "ipConfigurations": [
                {
                    "name": "ipconfig1",
                    "properties": {
                        "privateIPAllocationMethod": "Dynamic",
                        "publicIPAddress": {
                            "id": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]"
                        },
                        "subnet": {
                            "id": "[variables('subnet1Ref')]"
                        }
                    }
                }
                ]
            }
        },
        {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
                "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
                "hardwareProfile": {
                    "vmSize": "[parameters('vmSize')]"
                },
                "osProfile": {
                    "computername": "[parameters('vmName')]",
                    "adminUsername": "[parameters('adminUsername')]",
                    "adminPassword": "[parameters('adminPassword')]"
                },
                "storageProfile": {
                    "osDisk" : {
                        "name" : "[concat(parameters('vmName'),'-osDisk')]",
                        "osType" : "[parameters('osType')]",
                        "caching" : "ReadWrite",
                        "image" : {
                            "uri" : "[variables('userImageName')]"
                        },
                        "vhd" : {
                            "uri" : "[variables('osDiskVhdName')]"
                        }
                    }
                },
                "networkProfile": {
                    "networkInterfaces" : [
                    {
                        "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                    }
                    ]
                }
            }
        }
        ]
    }

### Schritt 2: Abrufen der virtuellen Festplatte

Natürlich benötigen Sie dafür eine VHD-Datei. Sie können entweder eine bereits vorhandene Datei in Azure verwenden oder eine hochladen.

Informationen zu einer Windows-basierten virtuellen Maschine finden Sie in [Erstellen und Hochladen einer Windows Server-VHD in Azure](virtual-machines-create-upload-vhd-windows-server.md).

Informationen zu einer Linux-basierten virtuellen Maschine finden Sie in [Erstellen und Hochladen einer Linux-VHD in Azure](virtual-machines-linux-create-upload-vhd.md).

### Schritt 3: Erstellen Sie den virtuellen Computer mit der Vorlage

Sie nun können einen neuen virtuellen Computer basierend auf der VHD-Datei erstellen. Erstellen Sie eine Gruppe zum Bereitstellen mit `azure group create <location>`:

    azure group create myResourceGroupUser eastus
    info:    Executing command group create
    + Getting resource group myResourceGroupUser                                            
    + Creating resource group myResourceGroupUser                                           
    info:    Created resource group myResourceGroupUser
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/myResourceGroupUser
    data:    Name:                myResourceGroupUser
    data:    Location:            eastus
    data:    Provisioning State:  Succeeded
    data:    Tags: 
    data:    
    info:    group create command OK
    
Erstellen Sie dann die Bereitstellung mithilfe der Option `--template-uri`, um die Vorlage direkt aufzurufen (oder verwenden Sie die Option `--template-file` für eine lokal gespeicherte Datei). Beachten Sie, dass Sie nur einige Dinge eingeben können, da in der Vorlage Standardwerte angegeben sind. Wenn Sie die Vorlage in verschiedenen Speicherorten bereitstellen, treten möglicherweise einige Namenskonflikte mit den Standardwerten auf (insbesondere dem von Ihnen erstellten DNS-Namen).

    azure group deployment create \
    > --template-uri https://raw.githubusercontent.com/azurermtemplates/azurermtemplates/master/101-vm-from-user-image/azuredeploy.json \
    > myResourceGroup \
    > customVhdDeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    adminUserName: ops
    adminPassword: password
    osType: linux
    subscriptionId: xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
        
Die Ausgabe sollte wie folgt angezeigt werden:

    + Initializing template configurations and parameters                          
    + Creating a deployment                                                        
    info:    Created template deployment "customVhdDeployment"
    + Registering providers                                                        
    info:    Registering provider microsoft.network
    info:    Registering provider microsoft.compute
    + Waiting for deployment to complete                                           
    error:   Deployment provisioning state was not successful
    data:    DeploymentName     : customVhdDeployment
    data:    ResourceGroupName  : myResourceGroupUser
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-04-28T14:55:48.0963829Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/azurermtemplates/azurermtemplates/master/101-vm-from-user-image/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                           Type          Value                               
    data:    -----------------------------  ------------  ------------------------------------
    data:    userImageStorageAccountName    String        userImageStorageAccountName         
    data:    userImageStorageContainerName  String        userImageStorageContainerName       
    data:    userImageVhdName               String        userImageVhdName                    
    data:    dnsNameForPublicIP             String        uniqueDnsNameForPublicIP            
    data:    adminUserName                  String        ops                                 
    data:    adminPassword                  SecureString  undefined                           
    data:    osType                         String        linux                               
    data:    subscriptionId                 String        xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    data:    location                       String        West US                             
    data:    vmSize                         String        Standard_A2                         
    data:    publicIPAddressName            String        myPublicIP                          
    data:    vmName                         String        myVM                                
    data:    virtualNetworkName             String        myVNET                              
    data:    nicName                        String        myNIC                               
    info:    group deployment create command OK
    

## <a id="deploy-a-multi-vm-application-that-uses-a-virtual-network-and-an-external-load-balancer"></a>AUFGABE: Bereitstellen einer Multi-VM-Anwendung, die ein virtuelles Netzwerk und einen externen Lastenausgleich verwendet

Mit dieser Vorlage können Sie 2 virtuelle Computer unter einem Lastenausgleich erstellen und eine Lastenausgleichsregel auf Port 80 konfigurieren. Diese Vorlage stellt außerdem Speicherkonto, Virtual Network, öffentliche IP-Adresse, Verfügbarkeitsgruppe und Netzwerkschnittstellen bereit.

![](./media/virtual-machines-deploy-rmtemplates-azure-cli/multivmextlb.png)
 
Folgen Sie diesen Anweisungen zum Bereitstellen einer Multi-VM-Anwendung, die ein virtuelles Netzwerk und einen Lastenausgleich mit einer Resource Manager-Vorlage im Github-Vorlagenrepository mithilfe von Azure PowerShell-Befehlen verwendet.

### Schritt 1: Untersuchen der JSON-Datei für die Vorlage.

Dies ist der Inhalt der JSON-Datei für die Vorlage. Wenn Sie die neueste Version möchten, diese finden Sie [hier](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json). In diesem Thema wird der Switch `--template-uri` verwendet, um die Vorlage aufzurufen, aber Sie können auch den Switch `--template-file` nehmen, um eine lokale Version zu übergeben.


    {
        "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "location": {
                "type": "string",
                "metadata": {
                  "description": "Location of resources"
                }
            },
            "storageAccountName": {
                "type": "string",
                "metadata": {
                  "description": "Name of storage account"
                }
            },
            "adminUsername": {
                "type": "string",
                "metadata": {
                  "description": "Admin username"
                }
            },
            "adminPassword": {
                "type": "securestring",
                "metadata": {
                  "description": "Admin password"
                }
            },
            "dnsNameforLBIP": {
                "type": "string",
                "metadata": {
                  "description": "DNS for Load Balancer IP"
                }
            },
            "backendPort": {
                "type": "int",
                "defaultValue": 3389,
                "metadata": {
                  "description": "Backend port"
                }
            },
            "vmNamePrefix": {
                "type": "string",
                "defaultValue": "myVM",
                "metadata": {
                  "description": "Prefix to use for VM names"
                }
            },
            "vmSourceImageName": {
                "type": "string",
                "defaultValue": "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201412.01-en.us-127GB.vhd"
            },
            "lbName": {
                "type": "string",
                "defaultValue": "myLB",
                "metadata": {
                  "description": "Load Balancer name"
                }
            },
            "nicNamePrefix": {
                "type": "string",
                "defaultValue": "nic",
                "metadata": {
                  "description": "Network Interface name prefix"
                }
            },
            "publicIPAddressName": {
                "type": "string",
                "defaultValue": "myPublicIP",
                "metadata": {
                  "description": "Public IP Name"
                }
            },
            "vnetName": {
                "type": "string",
                "defaultValue": "myVNET",
                "metadata": {
                  "description": "VNET name"
                }
            },
            "vmSize": {
                "type": "string",
                "defaultValue": "Standard_A1",
                "metadata": {
                  "description": "Size of the VM"
                }
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
                "apiVersion": "2015-05-01-preview",
                "location": "[parameters('location')]",
                "properties": {
                    "accountType": "[variables('storageAccountType')]"
                }
            },
            {
                "type": "Microsoft.Compute/availabilitySets",
                "name": "[variables('availabilitySetName')]",
                "apiVersion": "2015-05-01-preview",
                "location": "[parameters('location')]",
                "properties": { }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/publicIPAddresses",
                "name": "[parameters('publicIPAddressName')]",
                "location": "[parameters('location')]",
                "properties": {
                    "publicIPAllocationMethod": "[variables('publicIPAddressType')]",
                    "dnsSettings": {
                        "domainNameLabel": "[parameters('dnsNameforLBIP')]"
                    }
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Network/virtualNetworks",
                "name": "[parameters('vnetName')]",
                "location": "[parameters('location')]",
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
                "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
                "location": "[parameters('location')]",
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
                            },
                            "loadBalancerBackendAddressPools": [
                                {
                                    "id": "[concat('Microsoft.Network/loadBalancers/',parameters('lbName'),'/backendAddressPools/LBBE')]"
                                }
                            ],
                            "loadBalancerInboundNatRules": [
                                {
                                    "id": "[concat('Microsoft.Network/loadBalancers/',parameters('lbName'),'/inboundNatRule/RDP-VM', copyindex())]"
                                }
                            ]
    
    
                        }
                    ]
    
                }
            },
            {
                "apiVersion": "2015-05-01-preview",
                "name": "[parameters('lbName')]",
                "type": "Microsoft.Network/loadBalancers",
                "location": "[parameters('location')]",
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
                            "name": "LBBE"
    
                        }
                    ],
                    "inboundNatRules": [
                        {
                            "name": "RDP-VM1",
                            "properties": {
                                "frontendIPConfiguration":
                                    {
                                        "id": "[variables('frontEndIPConfigID')]"
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
                                "frontendIPConfiguration":
                                    {
                                        "id": "[variables('frontEndIPConfigID')]"
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
                                "frontendIPConfiguration": {
                                    "id": "[variables('frontEndIPConfigID')]"
                                },
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
                "apiVersion": "2015-05-01-preview",
                "type": "Microsoft.Compute/virtualMachines",
                "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
                "copy": {
                    "name": "virtualMachineLoop",
                    "count": "[variables('numberOfInstances')]"
                },
                "location": "[parameters('location')]",
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

Erstellen Sie eine Ressourcengruppe für die Vorlage mit `azure group create <location>`, und erstellen Sie dann eine Bereitstellung in dieser Ressourcengruppe mit `azure group deployment create`. Übergeben Sie die Ressourcengruppe, einen Bereitstellungsnamen, und beantworten Sie die Eingabeaufforderungen für Parameter in der Vorlage, die nicht über Standardwerte verfügen.


    azure group create lbgroup westus
    info:    Executing command group create
    + Getting resource group lbgroup                                               
    + Creating resource group lbgroup                                              
    info:    Created resource group lbgroup
    data:    Id:                  /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/lbgroup
    data:    Name:                lbgroup
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: 
    data:    
    info:    group create command OK
    

Verwenden Sie jetzt den Befehl `azure group deployment create` und die Option `--template-uri`, um die Vorlage bereitzustellen. Halten Sie Ihre Parameterwerte bereit, wenn Sie wie folgt aufgefordert werden.

    azure group deployment create \
    > --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json \
    > lbgroup \
    > newdeployment
    info:    Executing command group deployment create
    info:    Supply values for the following parameters
    location: westus
    newStorageAccountName: storagename
    adminUsername: ops
    adminPassword: password
    dnsNameforLBIP: lbdomainname
    + Initializing template configurations and parameters                          
    + Creating a deployment                                                        
    info:    Created template deployment "newdeployment"
    + Registering providers                                                        
    info:    Registering provider microsoft.storage
    info:    Registering provider microsoft.compute
    info:    Registering provider microsoft.network
    + Waiting for deployment to complete                                           
    data:    DeploymentName     : newdeployment
    data:    ResourceGroupName  : lbgroup
    data:    ProvisioningState  : Succeeded
    data:    Timestamp          : 2015-04-28T20:58:40.1678876Z
    data:    Mode               : Incremental
    data:    TemplateLink       : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json
    data:    ContentVersion     : 1.0.0.0
    data:    Name                   Type          Value                 
    data:    ---------------------  ------------  ----------------------
    data:    location               String        westus                
    data:    newStorageAccountName  String        storagename         
    data:    adminUsername          String        ops                   
    data:    adminPassword          SecureString  undefined             
    data:    dnsNameforLBIP         String        lbdomainname         
    data:    backendPort            Int           3389                  
    data:    vmNamePrefix           String        myVM                  
    data:    imagePublisher         String        MicrosoftWindowsServer
    data:    imageOffer             String        WindowsServer         
    data:    imageSKU               String        2012-R2-Datacenter    
    data:    lbName                 String        myLB                  
    data:    nicNamePrefix          String        nic                   
    data:    publicIPAddressName    String        myPublicIP            
    data:    vnetName               String        myVNET                
    data:    vmSize                 String        Standard_A1           
    info:    group deployment create command OK
    
Beachten Sie, dass diese Vorlage ein Windows Server-Image bereitstellt. Allerdings kann es leicht durch ein beliebiges Linux-Image ersetzt werden. Sie möchten ein Docker-Cluster über mehrere Regionen erstellen? [Das ist kein Problem.](http://azure.microsoft.com/documentation/templates/201-discover-private-ip-dynamically/)

## <a id="remove-a-resource-group"></a>AUFGABE: Entfernen einer Ressourcengruppe

Beachten Sie, dass Sie zu einer Ressourcengruppe erneut bereitstellen können. Aber wenn Sie damit fertig sind, löschen Sie sie mit `azure group delete <group name>`.

    azure group delete myResourceGroup
    info:    Executing command group delete
    Delete resource group myResourceGroup? [y/n] y
    + Deleting resource group myResourceGroup                                               
    info:    group delete command OK
    
## <a id="show-the-log-for-a-resource-group-deployment"></a>AUFGABE: Anzeigen des Protokolls für die Ressourcengruppenbereitstellung

Dieses tritt häufig beim Erstellen oder Verwenden von Vorlagen auf. Der Aufruf zur Anzeige von Bereitstellungsprotokollen für eine Gruppe ist `azure group log show <groupname>`. Es werden eine Menge Informationen angezeigt, die für das Verständnis hilfreich sind, warum etwas passiert ist oder auch nicht. (Weitere Informationen zur Problembehandlung für Ihre Bereitstellungen sowie andere Informationen zu Problemen finden Sie unter [Problembehandlung Ressourcengruppen-Bereitstellungen in Azure](resource-group-deploy-debug.md).)

Auf bestimmte Ziel-Fehler z. B. können Sie Tools wie **Jq** verwenden, um Dinge genauer abzufragen. Zum Beispiel, welche individuellen Fehler Sie korrigieren müssen. Im folgenden Beispiel wird **Jq** zum Analysieren eines Bereitstellungsprotokolls für die **Lbgroup** verwendet, um nach Fehlern zu suchen.

    azure group log show lbgroup -l --json | jq '.[] | select(.status.value == "Failed") | .properties' 

Sie können sehr schnell ermitteln, welche Fehler aufgetreten sind, sie beheben und es erneut versuchen. Im folgenden Fall wurden zwei virtuelle Computer gleichzeitig von der Vorlage erstellt, was eine Sperre auf die VHD-Datei verursachte. (Nach dem Ändern der Vorlage funktionierte die Bereitstellung schnell.)

    {
      "statusCode": "Conflict",
      "statusMessage": "{"status":"Failed","error":{"code":"ResourceDeploymentFailure","message":"The resource operation completed with terminal provisioning state 'Failed'.","details":[{"code":"AcquireDiskLeaseFailed","message":"Failed to acquire lease while creating disk 'osdisk' using blob with URI http://storage.blob.core.windows.net/vhds/osdisk.vhd."}]}}"
    }
    

## <a id="display-information-about-a-virtual-machine"></a>AUFGABE: Anzeigen von Informationen zu einem virtuellen Computer

Informationen zu spezifischen VMs in Ihrer Ressourcengruppe finden Sie mithilfe von `azure vm show <groupname> <vmname> command`. Möglicherweise müssen Sie die virtuellen Computer in einer Gruppe zuerst auflisten, wenn Sie mehr als einen verwenden, und zwar mithilfe von `azure vm list <groupname>`.

    azure vm list zoo
    info:    Executing command vm list
    + Getting virtual machines                                                     
    data:    Name   ProvisioningState  Location  Size       
    data:    -----  -----------------  --------  -----------
    data:    myVM0  Succeeded          westus    Standard_A1
    data:    myVM1  Failed             westus    Standard_A1
    
und dann schlagen Sie in myVM1 nach:

    azure vm show zoo myVM1
    info:    Executing command vm show
    + Looking up the VM "myVM1"                                                    
    + Looking up the NIC "nic1"                                                    
    data:    Id                              :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Compute/virtualMachines/myVM1
    data:    ProvisioningState               :Failed
    data:    Name                            :myVM1
    data:    Location                        :westus
    data:    Type                            :Microsoft.Compute/virtualMachines
    data:    
    data:    Hardware Profile:
    data:      Size                          :Standard_A1
    data:    
    data:    Storage Profile:
    data:      Image reference:
    data:        Publisher                   :MicrosoftWindowsServer
    data:        Offer                       :WindowsServer
    data:        Sku                         :2012-R2-Datacenter
    data:        Version                     :latest
    data:    
    data:      OS Disk:
    data:        OSType                      :Windows
    data:        Name                        :osdisk
    data:        Caching                     :ReadWrite
    data:        CreateOption                :FromImage
    data:        Vhd:
    data:          Uri                       :http://zoostorageralph.blob.core.windows.net/vhds/osdisk.vhd
    data:    
    data:    OS Profile:
    data:      Computer Name                 :myVM1
    data:      User Name                     :ops
    data:      Windows Configuration:
    data:        Provision VM Agent          :true
    data:        Enable automatic updates    :true
    data:    
    data:    Network Profile:
    data:      Network Interfaces:
    data:        Network Interface #1:
    data:          Id                        :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Network/networkInterfaces/nic1
    data:          Primary                   :false
    data:          Provisioning State        :Succeeded
    data:          Name                      :nic1
    data:          Location                  :westus
    data:            Private IP alloc-method :Dynamic
    data:            Private IP address      :10.0.0.5
    data:    
    data:    AvailabilitySet:
    data:      Id                            :/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/zoo/providers/Microsoft.Compute/availabilitySets/MYAVSET
    info:    vm show command OK
    

> [AZURE.NOTE]Wenn Sie die Ausgaben der Konsolenbefehle programmgesteuert speichern und bearbeiten möchten, sollten Sie möglicherweise ein JSON-Analysetool verwenden, z. B. **[jq](https://github.com/stedolan/jq)**, **[jsawk](https://github.com/micha/jsawk)** oder für die Aufgabe geeignete Sprachbibliotheken.

## <a id="log-on-to-a-linux-based-virtual-machine"></a>AUFGABE: Anmelden bei einem virtuellen Computer auf Linux-Basis

In der Regel sind Linux-Computer über SSH verbunden. Weitere Informationen finden Sie unter [Verwenden von SSH mit Linux auf Azure](virtual-machines-linux-use-ssh-key.md).

## <a id="stop-a-virtual-machine"></a>AUFGABE: Anhalten eines virtuellen Computers

Führen Sie den folgenden Befehl aus:

    azure vm stop <group name> <virtual machine name>

>[AZURE.IMPORTANT]Verwenden Sie diesen Parameter, um die virtuelle IP-Adresse des Clouddiensts beizubehalten, falls es sich um den letzten virtuellen Computer in diesem Clouddienst handelt. <br><br> Wenn Sie diesen Parameter verwenden, wird der virtuelle Computer dennoch in Rechnung gestellt.

## <a id="start-a-virtual-machine"></a>AUFGABE: Starten eines virtuellen Computers

Führen Sie den folgenden Befehl aus:

    azure vm start <group name> <virtual machine name>

## <a id="attach-a-data-disk"></a>AUFGABE: Anfügen eines Datenträgers

Sie müssen auch entscheiden, ob Sie einen neuen Datenträger anfügen oder einen, der Daten enthält. Für einen neuen Datenträger erstellt der Befehl die VHD-Datei und fügt sie im selben Befehl an.

Um einen neuen Datenträger anzufügen, führen Sie den folgenden Befehl aus:

     azure vm disk attach-new <resource-group> <vm-name> <size-in-gb> 

Um einen vorhandenen Datenträger anzufügen, führen Sie den folgenden Befehl aus:

    azure vm disk attach <resource-group> <vm-name> [vhd-url]
    
Anschließend müssen Sie den Datenträger wie gewohnt in Linux (oder Windows) einbinden.


## Nächste Schritte

Viele weitere Verwendungsbeispiele für die Azure-Befehlszeilenschnittstelle mit dem **arm**-Modus finden Sie unter [Verwenden der Microsoft Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit Azure Resource Manager](xplat-cli-azure-resource-manager.md). Weitere Informationen über die Konzepte und Azure-Ressourcen erhalten Sie unter [Übersicht über Azure Resource Manager](../resource-group-overview.md).

Weitere Vorlagen finden Sie unter [Azure-Schnellstartvorlagen](http://azure.microsoft.com/documentation/templates/) und [App-Frameworks](virtual-machines-app-frameworks.md).








 

<!---HONumber=July15_HO2-->