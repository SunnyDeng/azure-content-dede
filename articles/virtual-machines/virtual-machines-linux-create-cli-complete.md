<properties
   pageTitle="Erstellen einer Linux-VM von Grund auf mit der Azure-Befehlszeilenschnittstelle | Microsoft Azure"
   description="Erfahren Sie, wie Sie die Komponenten Linux-VM, Speicher, Virtual Network und Subnetz, NIC, öffentliche IP-Adresse und Netzwerksicherheitsgruppe von Grund auf erstellen, indem Sie die Azure-Befehlszeilenschnittstelle verwenden."
   services="virtual-machines-linux"
   documentationCenter="virtual-machines"
   authors="vlivech"
   manager="squillace"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure"
   ms.date="02/10/2016"
   ms.author="v-livech"/>

# Erstellen einer Linux-VM von Grund auf mit der Azure-Befehlszeilenschnittstelle


## Zielsetzung

- Bereitstellen einer Ressourcengruppe
- Bereitstellen eines Speicherkontos
- Bereitstellen eines Virtual Network und Subnetzes
- Konfigurieren einer Netzwerksicherheitsgruppe und eingehender Regeln
- Zuweisen einer öffentlichen IP zur Netzwerkschnittstellenkarte
- Zuweisen der NSG zur Netzwerkschnittstellenkarte
- Bereitstellen einer Ubuntu 14.04 LTS-VM

## Voraussetzungen

- Azure-Konto
  - [Hier erhalten Sie eine kostenlose Testversion.](https://azure.microsoft.com/pricing/free-trial/) 
  - [Azure-Portal](https://portal.azure.com)
- JSON-Analysetool: In diesem Beispiel wird [jq](https://stedolan.github.io/jq/) verwendet.


## Einführung

In diesem Artikel wird eine Bereitstellung erstellt, die einer Clouddienstbereitstellung mit einer Linux-VM in einem VNET-Subnetz ähnelt. Sie werden Befehl für Befehl durch die gesamte grundlegende Bereitstellung geführt, bis Sie über eine funktionierende, sichere Linux-VM verfügen, mit der Sie über das Internet von jedem Ort aus eine Verbindung herstellen können.

Im Laufe dieses Vorgangs lernen Sie die Abhängigkeitshierarchie kennen, die Sie mit dem Resource Manager-Bereitstellungsmodell erhalten, sowie die damit verbundene hohe Leistungsfähigkeit. Nachdem Sie über den Aufbau des Systems Bescheid wissen, können Sie das System viel schneller neu erstellen, indem Sie direktere Befehle der Azure-Befehlszeilenschnittstelle verwenden. ([Hier](insertlinktonewdoc) finden Sie Informationen zu einer sehr ähnlichen Bereitstellung, bei der der Befehl `azure vm quick-create` genutzt wird.) Sie können sich auch darüber informieren, wie Sie vollständige Netzwerk- und Anwendungsbereitstellungen entwerfen und automatisieren und mit [Azure Resource Manager-Vorlagen](../resource-group-authoring-templates.md) aktualisieren. Wenn Sie sehen, wie die Teile Ihrer Bereitstellung zusammenpassen, wird die Erstellung von Vorlagen für deren Automatisierung vereinfacht.

Wir erstellen ein einfaches Netzwerk mit einer VM, die für die Entwicklung und einfache Computevorgänge gut geeignet ist, und wir beschreiben dabei jeweils die einzelnen Schritte. Anschließend können Sie sich mit komplexeren Netzwerken und Bereitstellungen beschäftigen.

## Schnellbefehle

_In diesem Abschnitt zu den Schnellbefehlen werden mehrere Beispiele mit Namen verwendet, die Sie je nach Bedarf durch Ihre eigenen Einstellungen ersetzen sollten._

```bash
# Create the Resource Group
azure group create TestRG westeurope

# Create the Storage Account
azure storage account create \  
--location westeurope \
--resource-group TestRG \
--type GRS \
computeteststore

# Verify the RG using the JSON parser
azure group show testrg --json | jq '.'

# Create the Virtual Network
azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope

# Verify the RG
azure group show testrg --json | jq '.'

# Create the Subnet
azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24

# Verify the VNet and Subnet
azure network vnet show testrg testvnet --json | jq '.'

# Create the NIC
azure network nic create -g TestRG -n TestNIC -l westeurope -a 192.168.1.101 -m TestVNet -k FrontEnd

# Verify the NIC
azure network nic show testrg testnic --json | jq '.'

# Create the NSG
azure network nsg create testrg testnsg westeurope

# Add an inbound rule for the NSG
azure network nsg rule create --protocol tcp --direction inbound --priority 1000  --destination-port-range 22 --access allow testrg testnsg testnsgrule

# Creat the Public facing NIC
azure network public-ip create -d testsubdomain testrg testpip westeurope

# Verify the NIC
azure network public-ip show testrg testpip --json | jq '.'

# Associate the Public IP to the NIC
azure network nic set --public-ip-name test pip

# Bind the NSG to the NIC
azure network nic set --network-security-group-name testnsg testrg testnic

# Create the Linux VM
azure vm create \            
    --resource-group testrg \
    --name testvm \
    --location westeurope \
    --os-type linux \
    --nic-name testnic \
    --vnet-name testvnet \
    --vnet-subnet-name FrontEnd \
    --storage-account-name computeteststore \
    --image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
    --ssh-publickey-file ~/.ssh/id_rsa.pub \
    --admin-username ops
    
# Verify everything built
azure vm show testrg testvm 

```


## Ausführliche exemplarische Vorgehensweise

### Erstellen einer Ressourcengruppe und Auswählen von Standorten für die Bereitstellung 

Azure-Ressourcengruppen sind logische Bereitstellungsentitäten, die Konfigurationsdaten und andere Metadaten enthalten, um die logische Verwaltung von Ressourcenbereitstellungen zu ermöglichen.

    azure group create TestRG westeurope                        
    info:    Executing command group create
    + Getting resource group TestRG
    + Creating resource group TestRG
    info:    Created resource group TestRG
    data:    Id:                  /subscriptions/<yoursub>/resourceGroups/TestRG
    data:    Name:                TestRG
    data:    Location:            westeurope
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:
    info:    group create command OK

### Speicherkonto erstellen 

Sie benötigen unter anderem Speicherkonten für Ihre VM-Datenträger und für alle zusätzlichen Datenträger, die Sie hinzufügen möchten. Anders ausgedrückt: Sie erstellen Speicherkonten normalerweise immer direkt nach der Erstellung von Ressourcengruppen.

Hier verwenden wir den Befehl `azure storage account create` und übergeben den Speicherort des Kontos, die Ressourcengruppe, mit der es gesteuert wird, und den Typ der gewünschten Speicherunterstützung.

    azure storage account create \  
    --location westeurope \
    --resource-group TestRG \
    --type GRS \
    computeteststore
    info:    Executing command storage account create
    + Creating storage account
    info:    storage account create command OK
    rasquill•~/workspace/keygen» azure group show testrg 
    info:    Executing command group show
    + Listing resource groups
    + Listing resources for the group
    data:    Id:                  /subscriptions/<guid>/resourceGroups/TestRG
    data:    Name:                TestRG
    data:    Location:            westeurope
    data:    Provisioning State:  Succeeded
    data:    Tags: null
    data:    Resources:
    data:
    data:      Id      : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore
    data:      Name    : computeteststore
    data:      Type    : storageAccounts
    data:      Location: westeurope
    data:      Tags    :
    data:
    data:    Permissions:
    data:      Actions: *
    data:      NotActions:
    data:
    info:    group show command OK
    
Wir nutzen das Tool [jq](https://stedolan.github.io/jq/) (Sie können **jsawk** oder eine andere bevorzugte Sprachbibliothek für die JSON-Analyse verwenden) zusammen mit der Option `--json` der Azure-Befehlszeilenschnittstelle, um unsere Ressourcengruppe mit dem Befehl `azure group show` zu untersuchen.

    azure group show testrg --json | jq '.'                                                                                        
    {
      "tags": {},
      "id": "/subscriptions/<guid>/resourceGroups/TestRG",
      "name": "TestRG",
      "provisioningState": "Succeeded",
      "location": "westeurope",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
          "name": "computeteststore",
          "type": "storageAccounts",
          "location": "westeurope",
          "tags": null
        }
      ],
      "permissions": [
        {
          "actions": [
            "*"
          ],
          "notActions": []
        }
      ]
    }

Zum Untersuchen des Speicherkontos mit der Befehlszeilenschnittstelle müssen Sie zuerst die Kontonamen und Schlüssel festlegen, indem Sie eine Variante des unten angegebenen Befehls verwenden. Ersetzen Sie den Namen des Speicherkontos in diesem Artikel durch den Namen Ihres Speicherkontos.

        AZURE_STORAGE_CONNECTION_STRING="$(azure storage account connectionstring show computeteststore --resource-group testrg --json | jq -r '.string')"

Sie können die Speicherinformationen dann leicht anzeigen:

        azure storage container list 
        info:    Executing command storage container list
        + Getting storage containers
        data:    Name  Public-Access  Last-Modified
        data:    ----  -------------  -----------------------------
        data:    vhds  Off            Sun, 27 Sep 2015 19:03:54 GMT
        info:    storage container list command OK

### Erstellen des Virtual Network und des Subnetzes 

Sie müssen ein Azure Virtual Network und ein Subnetz erstellen, in dem Sie die VM installieren können.

    azure network vnet create -g TestRG -n TestVNet -a 192.168.0.0/16 -l westeurope
    info:    Executing command network vnet create
    + Looking up virtual network "TestVNet"
    + Creating virtual network "TestVNet"
    + Loading virtual network state
    data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet
    data:    Name                            : TestVNet
    data:    Type                            : Microsoft.Network/virtualNetworks
    data:    Location                        : westeurope
    data:    ProvisioningState               : Succeeded
    data:    Address prefixes:
    data:      192.168.0.0/16
    info:    network vnet create command OK

Wir sehen uns wieder an, wie wir die Ressourcen erstellen, indem wir die Option „--json“ von `azure group show` und **jq** verwenden. Wir verfügen nun über eine `storageAccounts`-Ressource und eine `virtualNetworks`-Ressource.

    azure group show testrg --json | jq '.'
    {
      "tags": {},
      "id": "/subscriptions/<guid>/resourceGroups/TestRG",
      "name": "TestRG",
      "provisioningState": "Succeeded",
      "location": "westeurope",
      "properties": {
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
          "name": "TestVNet",
          "type": "virtualNetworks",
          "location": "westeurope",
          "tags": null
        },
        {
          "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
          "name": "computeteststore",
          "type": "storageAccounts",
          "location": "westeurope",
          "tags": null
        }
      ],
      "permissions": [
        {
          "actions": [
            "*"
          ],
          "notActions": []
        }
      ]
    }

Als Nächstes erstellen wir ein Subnetz im virtuellen Netzwerk `TestVnet`, in dem die VM bereitgestellt wird. Wir verwenden den Befehl `azure network vnet subnet create` zusammen mit den Ressourcen, die wir bereits erstellt haben: die Ressourcengruppe `TestRG` und das virtuelle Netzwerk `TestVNet`. Außerdem fügen wir den Subnetznamen `FrontEnd` und das Subnetzadressen-Präfix `192.168.1.0/24` wie folgt hinzu:

    azure network vnet subnet create -g TestRG -e TestVNet -n FrontEnd -a 192.168.1.0/24
    info:    Executing command network vnet subnet create
    + Looking up the subnet "FrontEnd"
    + Creating subnet "FrontEnd"
    + Looking up the subnet "FrontEnd"
    data:    Id                              : /subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
    data:    Type                            : Microsoft.Network/virtualNetworks/subnets
    data:    ProvisioningState               : Succeeded
    data:    Name                            : FrontEnd
    data:    Address prefix                  : 192.168.1.0/24
    data:
    info:    network vnet subnet create command OK
  
Da das Subnetz logisch im virtuellen Netzwerk angeordnet ist, ermitteln wir die Subnetzinformationen mit einem etwas anderen Befehl: `azure network vnet show`. Die JSON-Ausgabe wird aber weiterhin mit **jq** untersucht.

    azure network vnet show testrg testvnet --json | jq '.'
    {
      "subnets": [
        {
          "ipConfigurations": [],
          "addressPrefix": "192.168.1.0/24",
          "provisioningState": "Succeeded",
          "name": "FrontEnd",
          "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
          "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
        }
      ],
      "tags": {},
      "addressSpace": {
        "addressPrefixes": [
          "192.168.0.0/16"
        ]
      },
      "dhcpOptions": {
        "dnsServers": []
      },
      "provisioningState": "Succeeded",
      "etag": "W/"974f3e2c-028e-4b35-832b-a4b16ad25eb6"",
      "id": "/subscriptions/<guid>/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
      "name": "TestVNet",
      "location": "westeurope"
    }

### Erstellen einer Netzwerkschnittstellenkarte zur Verwendung mit der Linux-VM

Netzwerkschnittstellen sind auch programmgesteuert verfügbar, und Sie können Regeln für ihre Verwendung anwenden und mehr als eine verwenden.

        azure network nic create -g TestRG -n TestNIC -l westeurope -a 192.168.1.101 -m TestVNet -k FrontEnd
        info:    Executing command network nic create
        + Looking up the network interface "TestNIC"
        + Looking up the subnet "FrontEnd"
        + Creating network interface "TestNIC"
        + Looking up the network interface "TestNIC"
        data:    Id                              : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC
        data:    Name                            : TestNIC
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westeurope
        data:    Provisioning state              : Succeeded
        data:    Enable IP forwarding            : false
        data:    IP configurations:
        data:      Name                          : NIC-config
        data:      Provisioning state            : Succeeded
        data:      Private IP address            : 192.168.1.101
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd
        data:
        info:    network nic create command OK

Da die NIC-Ressource sowohl einer VM als auch einer Netzwerksicherheitsgruppe zugeordnet ist, wird sie als eine Ressource der obersten Ebene angezeigt, wenn Sie Ihre Ressourcengruppe `TestRG` untersuchen:

        azure group show testrg --json | jq '.'
        {
        "tags": {},
        "id": "/subscriptions/guid/resourceGroups/TestRG",
        "name": "TestRG",
        "provisioningState": "Succeeded",
        "location": "westeurope",
        "properties": {
            "provisioningState": "Succeeded"
        },
        "resources": [
            {
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
            "name": "TestNIC",
            "type": "networkInterfaces",
            "location": "westeurope",
            "tags": null
            },
            {
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
            "name": "TestVNet",
            "type": "virtualNetworks",
            "location": "westeurope",
            "tags": null
            },
            {
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
            "name": "computeteststore",
            "type": "storageAccounts",
            "location": "westeurope",
            "tags": null
            }
        ],
        "permissions": [
            {
            "actions": [
                "*"
            ],
            "notActions": []
            }
        ]
        }

Sie können die Details anzeigen, indem Sie die Ressource direkt mit dem Befehl `azure network nic show` untersuchen.

        azure network nic show testrg testnic --json | jq '.'
        {
        "ipConfigurations": [
            {
            "loadBalancerBackendAddressPools": [],
            "loadBalancerInboundNatRules": [],
            "privateIpAddress": "192.168.1.101",
            "privateIpAllocationMethod": "Static",
            "subnet": {
                "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd"
            },
            "provisioningState": "Succeeded",
            "name": "NIC-config",
            "etag": "W/"4d29b1ca-0207-458c-b258-f298e6fc450f"",
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC/ipConfigurations/NIC-config"
            }
        ],
        "tags": {},
        "dnsSettings": {
            "appliedDnsServers": [],
            "dnsServers": []
        },
        "enableIPForwarding": false,
        "provisioningState": "Succeeded",
        "etag": "W/"4d29b1ca-0207-458c-b258-f298e6fc450f"",
        "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
        "name": "TestNIC",
        "location": "westeurope"
        }

### Erstellen der Netzwerksicherheitsgruppe und der Regeln

Als Nächstes erstellen wir eine Netzwerksicherheitsgruppe (NSG) und die eingehenden Regeln, mit denen der Zugriff auf die Netzwerkschnittstellenkarte gesteuert wird.

        azure network nsg create testrg testnsg westeurope

Wir fügen die eingehende Regel für die NSG hinzu, um eingehende Verbindungen für Port 22 zuzulassen (für die SSH-Unterstützung):

        azure network nsg rule create --protocol tcp --direction inbound --priority 1000  --destination-port-range 22 --access allow testrg testnsg testnsgrule

> [AZURE.NOTE] Die eingehende Regel ist ein Filter für eingehende Netzwerkverbindungen. In diesem Beispiel binden wir die NSG an die virtuelle Netzwerkschnittstellenkarte (NIC) der VM. Dies bedeutet, dass alle Anforderungen an Port 22 über die NIC auf unserer VM übergeben werden. Da dies eine Regel für eine Netzwerkverbindung ist – und kein Endpunkt wie bei klassischen Bereitstellungen –, müssen Sie zum Öffnen eines Ports für `--source-port-range` die Einstellung „*“ (Standardwert) beibehalten. Dann können eingehende Anforderungen von **allen** anfordernden Ports akzeptiert werden, die normalerweise dynamisch sind.

### Erstellen der öffentlichen IP-Adresse (PIP)

Jetzt erstellen wir die öffentliche IP-Adresse (PIP), mit der Sie eine Verbindung mit Ihrer VM über das Internet herstellen können, indem Sie den Befehl `azure network public-ip create` verwenden. Da die Standardeinstellung eine dynamische Adresse ist, erstellen wir in der Domäne **cloudapp.azure.com** mit der Option `-d testsubdomain` einen benannten DNS-Eintrag.

        azure network public-ip create -d testsubdomain testrg testpip westeurope
        info:    Executing command network public-ip create
        + Looking up the public ip "testpip"
        + Creating public ip address "testpip"
        + Looking up the public ip "testpip"
        data:    Id                              : /subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip
        data:    Name                            : testpip
        data:    Type                            : Microsoft.Network/publicIPAddresses
        data:    Location                        : westeurope
        data:    Provisioning state              : Succeeded
        data:    Allocation method               : Dynamic
        data:    Idle timeout                    : 4
        data:    Domain name label               : testsubdomain
        data:    FQDN                            : testsubdomain.westeurope.cloudapp.azure.com
        info:    network public-ip create command OK

Dies ist außerdem eine Ressource der obersten Ebene, die Sie mit `azure group show` anzeigen können.

        azure group show testrg --json | jq '.'
        {
        "tags": {},
        "id": "/subscriptions/guid/resourceGroups/TestRG",
        "name": "TestRG",
        "provisioningState": "Succeeded",
        "location": "westeurope",
        "properties": {
            "provisioningState": "Succeeded"
        },
        "resources": [
            {
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/TestNIC",
            "name": "TestNIC",
            "type": "networkInterfaces",
            "location": "westeurope",
            "tags": null
            },
            {
            "id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
            "name": "testpip",
            "type": "publicIPAddresses",
            "location": "westeurope",
            "tags": null
            },
            {
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet",
            "name": "TestVNet",
            "type": "virtualNetworks",
            "location": "westeurope",
            "tags": null
            },
            {
            "id": "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Storage/storageAccounts/computeteststore",
            "name": "computeteststore",
            "type": "storageAccounts",
            "location": "westeurope",
            "tags": null
            }
        ],
        "permissions": [
            {
            "actions": [
                "*"
            ],
            "notActions": []
            }
        ]
        }

Wie immer können Sie auch auf weitere Ressourcendetails zugreifen, z.B. den vollqualifizierten Domänennamen (FQDN) der Unterdomäne, indem Sie den umfassenderen Befehl `azure network public-ip show` verwenden. Beachten Sie, dass die öffentliche IP-Adressressource logisch zugeordnet wurde, aber noch keine spezielle Adresse zugewiesen wurde. Hierfür benötigen Sie eine VM, die wir noch nicht erstellt haben.

        azure network public-ip show testrg testpip --json | jq '.'
        {
        "tags": {},
        "publicIpAllocationMethod": "Dynamic",
        "dnsSettings": {
            "domainNameLabel": "testsubdomain",
            "fqdn": "testsubdomain.westeurope.cloudapp.azure.com"
        },
        "idleTimeoutInMinutes": 4,
        "provisioningState": "Succeeded",
        "etag": "W/"c63154b3-1130-49b9-a887-877d74d5ebc5"",
        "id": "/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/publicIPAddresses/testpip",
        "name": "testpip",
        "location": "westeurope"
        }

### Zuordnen der öffentlichen IP-Adresse und der Netzwerksicherheitsgruppe zur NIC

        azure network nic set --public-ip-name test pip

Binden Sie die NSG an die NIC:

        azure network nic set --network-security-group-name testnsg testrg testnic

### Erstellen der Linux-VM

Sie haben die Speicher- und Netzwerkressourcen zur Unterstützung einer über das Internet erreichbaren VM erstellt. Wir erstellen jetzt diese VM und schützen sie mit einem SSH-Schlüssel ohne Kennwort. In diesem Fall erstellen wir eine Ubuntu-VM basierend auf dem aktuellen LTS-Stand. Wir ermitteln diese Imageinformationen per `azure vm image list`. Dies ist unter [Suchen nach Azure VM-Images](virtual-machines-linux-cli-ps-findimage.md) beschrieben. Wir haben ein Image mit dem Befehl `azure vm image list westeurope canonical | grep LTS` ausgewählt. Hier verwenden wir `canonical:UbuntuServer:14.04.3-LTS:14.04.201509080`, aber für das letzte Feld übergeben wir `latest`, damit wir in Zukunft immer den aktuellen Build erhalten (verwendete Zeichenfolge: `canonical:UbuntuServer:14.04.3-LTS:latest`).

> [AZURE.NOTE] Der nächste Schritt ist allen Benutzern vertraut, die mit **ssh-keygen -t rsa -b 2048** bereits ein SSH RSA-Paar aus einem öffentlichen und einem privaten Schlüssel unter Linux oder Macintosh erstellt haben. Falls in Ihrem Verzeichnis `~/.ssh` keine Zertifikatschlüsselpaare enthalten sind, besteht eine Möglichkeit darin, sie zu erstellen: <br /> 1. Automatisch mit der Option `azure vm create --generate-ssh-keys` 2. Manuell anhand der [Anweisungen für die eigene Erstellung](virtual-machines-linux-ssh-from-linux.md) <br /> Alternativ dazu können Sie die Optionen `azure vm create --admin-username --admin-password` nutzen, um die normalerweise weniger sichere Methode mit Benutzername und Kennwort für die Authentifizierung Ihrer SSH-Verbindungen zu verwenden, nachdem die VM erstellt wurde.

Wir erstellen die VM, indem wir alle Ressourcen und Informationen mit dem Befehl `azure vm create` zusammenfassen.

        azure vm create \            
        --resource-group testrg \
        --name testvm \
        --location westeurope \
        --os-type linux \
        --nic-name testnic \
        --vnet-name testvnet \
        --vnet-subnet-name FrontEnd \
        --storage-account-name computeteststore \
        --image-urn canonical:UbuntuServer:14.04.3-LTS:latest \
        --ssh-publickey-file ~/.ssh/id_rsa.pub \
        --admin-username ops
        info:    Executing command vm create
        + Looking up the VM "testvm"
        info:    Verifying the public key SSH file: /Users/user/.ssh/id_rsa.pub
        info:    Using the VM Size "Standard_A1"
        info:    The [OS, Data] Disk or image configuration requires storage account
        + Looking up the storage account computeteststore
        + Looking up the NIC "testnic"
        info:    Found an existing NIC "testnic"
        info:    Found an IP configuration with virtual network subnet id "/subscriptions/guid/resourceGroups/TestRG/providers/Microsoft.Network/virtualNetworks/TestVNet/subnets/FrontEnd" in the NIC "testnic"
        info:    This NIC IP configuration has a public ip already configured "/subscriptions/guid/resourcegroups/testrg/providers/microsoft.network/publicipaddresses/testpip", any public ip parameters if provided, will be ignored.
        + Creating VM "testvm"
        info:    vm create command OK

Anschließend können Sie mit Ihren standardmäßigen SSH-Schlüsseln eine Verbindung mit der VM herstellen.

        ssh ops@testsubdomain.westeurope.cloudapp.azure.com           
        The authenticity of host 'testsubdomain.westeurope.cloudapp.azure.com (XX.XXX.XX.XXX)' can't be established.
        RSA key fingerprint is b6:a4:7g:4b:cb:cd:76:87:63:2d:84:83:ac:12:2d:cd.
        Are you sure you want to continue connecting (yes/no)? yes
        Warning: Permanently added 'testsubdomain.westeurope.cloudapp.azure.com,XX.XXX.XX.XXX' (RSA) to the list of known hosts.
        Welcome to Ubuntu 14.04.3 LTS (GNU/Linux 3.19.0-28-generic x86_64)
        
        * Documentation:  https://help.ubuntu.com/
        
        System information as of Mon Sep 28 18:45:02 UTC 2015
        
        System load: 0.64              Memory usage: 5%   Processes:       81
        Usage of /:  45.3% of 1.94GB   Swap usage:   0%   Users logged in: 0
        
        Graph this data and manage this system at:
            https://landscape.canonical.com/
        
        Get cloud support with Ubuntu Advantage Cloud Guest:
            http://www.ubuntu.com/business/services/cloud
        
        0 packages can be updated.
        0 updates are security updates.
        
        
        
        The programs included with the Ubuntu system are free software;
        the exact distribution terms for each program are described in the
        individual files in /usr/share/doc/*/copyright.
        
        Ubuntu comes with ABSOLUTELY NO WARRANTY, to the extent permitted by
        applicable law.
        
        ops@testvm:~$

Außerdem können Sie jetzt den Befehl `azure vm show testrg testvm` einsetzen, um die erstellten Elemente zu untersuchen. Sie verfügen nun über eine ausgeführte Ubuntu-VM in Azure, an der Sie sich nur mit Ihrem SSH-Schlüsselpaar anmelden können. Kennwörter sind deaktiviert.

        azure vm show testrg testvm 
        info:    Executing command vm show
        + Looking up the VM "testvm"
        + Looking up the NIC "testnic"
        + Looking up the public ip "testpip"
        data:    Id                              :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Compute/virtualMachines/testvm
        data:    ProvisioningState               :Succeeded
        data:    Name                            :testvm
        data:    Location                        :westeurope
        data:    FQDN                            :testsubdomain.westeurope.cloudapp.azure.com
        data:    Type                            :Microsoft.Compute/virtualMachines
        data:
        data:    Hardware Profile:
        data:      Size                          :Standard_A1
        data:
        data:    Storage Profile:
        data:      Image reference:
        data:        Publisher                   :canonical
        data:        Offer                       :UbuntuServer
        data:        Sku                         :14.04.3-LTS
        data:        Version                     :latest
        data:
        data:      OS Disk:
        data:        OSType                      :Linux
        data:        Name                        :cli4eecdddc349d6015-os-1443465824206
        data:        Caching                     :ReadWrite
        data:        CreateOption                :FromImage
        data:        Vhd:
        data:          Uri                       :https://computeteststore.blob.core.windows.net/vhds/cli4eecdddc349d6015-os-1443465824206.vhd
        data:
        data:    OS Profile:
        data:      Computer Name                 :testvm
        data:      User Name                     :ops
        data:      Linux Configuration:
        data:        Disable Password Auth       :true
        data:        SSH Public Keys:
        data:          Public Key #1:
        data:            Path                    :/home/ops/.ssh/authorized_keys
        data:            Key                     :MIIBrTCCAZigAwIBAgIBATALBgkqhkiG9w0BAQUwADAiGA8yMDE1MDkyODE4MzM0
        <snip>
        data:
        data:    Network Profile:
        data:      Network Interfaces:
        data:        Network Interface #1:
        data:          Id                        :/subscriptions/guid/resourceGroups/testrg/providers/Microsoft.Network/networkInterfaces/testnic
        data:          Primary                   :true
        data:          MAC Address               :00-0D-3A-21-8E-AE
        data:          Provisioning State        :Succeeded
        data:          Name                      :testnic
        data:          Location                  :westeurope
        data:            Private IP alloc-method :Dynamic
        data:            Private IP address      :192.168.1.101
        data:            Public IP address       :40.115.48.189
        data:            FQDN                    :testsubdomain.westeurope.cloudapp.azure.com
        data:
        data:    Diagnostics Instance View:
        info:    vm show command OK

### Nächste Schritte

Sie können jetzt mit mehreren Netzwerkkomponenten und VMs starten.

<!---HONumber=AcomDC_0323_2016-->