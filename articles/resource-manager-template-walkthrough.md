<properties
   pageTitle="Resource Manager-Vorlage – Exemplarische Vorgehensweise | Microsoft Azure"
   description="Enthält eine exemplarische Schritt-für-Schritt-Vorgehensweise für eine Resource Manager-Vorlage zur Bereitstellung einer grundlegenden Azure-IaaS-Architektur."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/14/2016"
   ms.author="navale;tomfitz"/>
   
# Resource Manager-Vorlage – Exemplarische Vorgehensweise

In diesem Thema werden Sie durch die Schritte zum Erstellen einer Resource Manager-Vorlage geführt. Es wird davon ausgegangen, dass Sie mit den Azure-Diensten vertraut sind, die Sie bereitstellen möchten, aber dass Sie noch nicht wissen, wie Sie diese Infrastruktur in einer Vorlage darstellen sollen. Sie erstellen eine Vorlage, die auf der [Vorlage für zwei VMs mit Load Balancer und Lastenausgleichsregel](https://github.com/Azure/azure-quickstart-templates/tree/master/201-2-vms-loadbalancer-lbrules) in der [Quickstart Gallery](https://github.com/Azure/azure-quickstart-templates) (Schnellstartkatalog) basiert. Die Verfahren können aber auf alle Vorlagen angewendet werden, die Sie erstellen müssen.

Wir sehen uns nun eine häufig verwendete Architektur an:

* Zwei virtuellen Computern, die das gleiche Speicherkonto verwenden und sich in der gleichen Verfügbarkeitsgruppe und im gleichen Subnetz eines virtuellen Netzwerks befinden.
* Einer einzelnen NIC- und VM-IP-Adresse für jeden virtuellen Computer.
* Load Balancer mit Lastenausgleichsregel an Port 80

![Architektur](./media/resource-group-overview/arm_arch.png)

Sie haben entschieden, dass Sie diese Architektur unter Azure bereitstellen möchten, und Sie möchten Resource Manager-Vorlagen verwenden, damit Sie die Architektur leicht erneut bereitstellen können. Sie sind sich aber nicht sicher, wie Sie diese Art von Vorlage erstellen sollen. In diesem Thema wird beschrieben, welche Elemente Sie in die Vorlage einfügen müssen.

Sie können beim Erstellen der Vorlage eine beliebige Art von Editor verwenden. Visual Studio enthält Tools, mit denen die Vorlagenentwicklung vereinfacht wird, aber Sie benötigen Visual Studio für dieses Tutorial nicht. Ein Tutorial zur Verwendung von Visual Studio für die Erstellung einer Bereitstellung mit Web-App und SQL-Datenbank finden Sie unter [Erstellen und Bereitstellen von Azure-Ressourcengruppen mit Visual Studio](vs-azure-tools-resource-groups-deployment-projects-create-deploy.md).

## Erstellen der Resource Manager-Vorlage

Die Vorlage ist eine JSON-Datei, mit der alle von Ihnen bereitgestellten Ressourcen definiert werden. Hiermit können Sie auch Folgendes definieren: Parameter, die während der Bereitstellung angegeben werden, Variablen, die aus anderen Werten und Ausdrücken erstellt werden, sowie Ausgaben der Bereitstellung.

Wir beginnen mit der einfachsten Vorlage:

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {  },
      "variables": {  },
      "resources": [  ],
      "outputs": {  }
    }

Speichern Sie diese Datei als **azuredeploy.json**.

Zunächst beschäftigen wir uns mit dem Abschnitt **resources** und später in diesem Thema dann mit **parameters** und **variables**.

## Speicherkonto
Sie definieren ein Speicherkonto, das von den virtuellen Computern verwendet werden soll. Fügen Sie im Abschnitt **resources** ein Objekt hinzu, mit dem das Speicherkonto wie unten gezeigt definiert wird.

```json
"resources": [
  {
    "type": "Microsoft.Storage/storageAccounts",
    "name": "[parameters('storageAccountName')]",
    "apiVersion": "2015-06-15",
    "location": "[resourceGroup().location]",
    "properties": {
      "accountType": "Standard_LRS"
    }
  }
]
```

Sie fragen sich vielleicht, woher diese Eigenschaften und Werte kommen. Die Eigenschaften **type**, **name**, **apiVersion** und **location** sind Standardelemente, die für alle Ressourcentypen verfügbar sind. Informationen zu den allgemeinen Elementen finden Sie unter [Ressourcen](../resource-group-authoring-templates/#resources). Sie legen **name** auf einen Parameterwert fest, den Sie während der Bereitstellung übergeben. Sie legen **location** als den Speicherort fest, der von der Ressourcengruppe verwendet wird. In den folgenden Abschnitten wird beschrieben, wie Sie die Elemente **type** und **apiVersion** bestimmen.

Der Abschnitt **properties** enthält alle Eigenschaften, die für einen bestimmten Ressourcentyp eindeutig sind. Die Werte, die Sie in diesem Abschnitt angeben, stimmen genau mit dem PUT-Vorgang in der REST-API für die Erstellung dieses Ressourcentyps überein. Beim Erstellen eines Speicherkontos müssen Sie einen **accountType** angeben. Beachten Sie bei der REST-API zum [Erstellen eines Speicherkontos](https://msdn.microsoft.com/library/azure/mt163564.aspx), dass der Abschnitt „properties“ des REST-Vorgangs auch eine **accountType**-Eigenschaft enthält und dass die zulässigen Werte dokumentiert sind. In diesem Beispiel wird der Kontotyp auf **Standard\_LRS** festgelegt, aber Sie können auch einen anderen Wert angeben oder es für Benutzer zulassen, den Kontotyp als Parameter zu übergeben.

## Verfügbarkeitsgruppe
Fügen Sie nach der Definition für das Speicherkonto einen verfügbaren Satz für die virtuellen Computer hinzu. In diesem Fall sind keine zusätzlichen Eigenschaften erforderlich, sodass die Definition relativ einfach ist. Den vollständigen Abschnitt „properties“ finden Sie unter [Erstellen einer Verfügbarkeitsgruppe](https://msdn.microsoft.com/library/azure/mt163607.aspx). Dies ist hilfreich, falls Sie die Werte für die Updatedomänenanzahl und Fehlerdomänenanzahl definieren möchten.

```json
{
   "type": "Microsoft.Compute/availabilitySets",
   "name": "[variables('availabilitySetName')]",
   "apiVersion": "2015-06-15",
   "location": "[resourceGroup().location]",
   "properties": {}
}
```

Beachten Sie, dass **name** auf den Wert einer Variablen festgelegt ist. Für diese Vorlage wird der Name der Verfügbarkeitsgruppe an einigen Stellen benötigt. Sie können die Vorlage mit weniger Aufwand warten, indem Sie diesen Wert einmal definieren und dann an mehreren Orten verwenden.

Der Wert, den Sie für **type** angeben, enthält sowohl den Ressourcenanbieter als auch den Ressourcentyp. Für Verfügbarkeitsgruppen lautet der Ressourcenanbieter **Microsoft.Compute** und der Ressourcentyp **availabilitySets**. Sie können die Liste mit den verfügbaren Ressourcenanbietern abrufen, indem Sie den folgenden PowerShell-Befehl ausführen:

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

Falls Sie die Azure-Befehlszeilenschnittstelle verwenden, können Sie den folgenden Befehl ausführen:

    azure provider list

Da Sie die Erstellung in diesem Thema mit Speicherkonten, virtuellen Computern und virtuellen Netzwerken durchführen, arbeiten Sie mit folgenden Elementen:

- Microsoft.Storage
- Microsoft.Compute
- Microsoft.Network

Führen Sie den folgenden PowerShell-Befehl aus, um die Ressourcentypen für einen bestimmten Anbieter anzuzeigen:

    PS C:\> (Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Compute).ResourceTypes

Für die Azure-Befehlszeilenschnittstelle gibt der folgende Befehl die verfügbaren Typen im JSON-Format zurück und speichert sie in einer Datei.

    azure provider show Microsoft.Compute --json > c:\temp.json

**availabilitySets** sollte als einer der Typen unter **Microsoft.Compute** angezeigt werden. Der vollständige Name des Typs lautet **Microsoft.Compute/availabilitySets**. Sie können den Ressourcentypnamen für alle Ressourcen der Vorlage bestimmen.

## Öffentliche IP
Definieren Sie eine öffentliche IP-Adresse. Sehen Sie hier wieder unter [Erstellen oder Aktualisieren einer öffentlichen IP-Adresse](https://msdn.microsoft.com/library/azure/mt163590.aspx) nach, um die festzulegenden Eigenschaften zu ermitteln.

```json
{
  "apiVersion": "2015-06-15",
  "type": "Microsoft.Network/publicIPAddresses",
  "name": "[parameters('publicIPAddressName')]",
  "location": "[resourceGroup().location]",
  "properties": {
    "publicIPAllocationMethod": "Dynamic",
    "dnsSettings": {
      "domainNameLabel": "[parameters('dnsNameforLBIP')]"
    }
  }
}
```

Die Zuordnungsmethode ist auf **Dynamic** festgelegt, aber Sie können sie auch auf den gewünschten Wert festlegen oder die Verwendung eines Parameterwerts zulassen. Sie haben für die Benutzer der Vorlage zugelassen, einen Wert für die Domänennamenbezeichnung zu übergeben.

Als Nächstes sehen wir uns an, wie Sie die **apiVersion** bestimmen. Es ist einfach so, dass der von Ihnen angegebene Wert mit der Version der REST-API übereinstimmt, die Sie beim Erstellen der Ressource verwenden möchten. Sie können diesen Ressourcentyp also in der Dokumentation zur REST-API nachschlagen. Sie können aber auch den folgenden PowerShell-Befehl für einen bestimmten Typ ausführen.

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Network).ResourceTypes | Where-Object ResourceTypeName -eq publicIPAddresses).ApiVersions

Hiermit werden folgende Werte zurückgegeben:

    2015-06-15
    2015-05-01-preview
    2014-12-01-preview

Führen Sie zum Anzeigen der API-Versionen mit der Azure-Befehlszeilenschnittstelle den Befehl **azure provider show** aus, der bereits gezeigt wurde.

Wählen Sie beim Erstellen einer neuen Vorlage die aktuelle API-Version aus.

## Virtuelles Netzwerk und Subnetz
Erstellen Sie ein virtuelles Netzwerk mit einem Subnetz. Die Eigenschaften, die festgelegt werden müssen, finden Sie unter [Erstellen oder Aktualisieren eines virtuellen Netzwerks](https://msdn.microsoft.com/library/azure/mt163661.aspx).

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/virtualNetworks",
   "name": "[parameters('vnetName')]",
   "location": "[resourceGroup().location]",
   "properties": {
     "addressSpace": {
       "addressPrefixes": [
         "10.0.0.0/16"
       ]
     },
     "subnets": [
       {
         "name": "[variables('subnetName')]",
         "properties": {
           "addressPrefix": "10.0.0.0/24"
         }
       }
     ]
   }
}
```

## Lastenausgleichsmodul
Sie erstellen jetzt ein extern ausgerichtetes Lastenausgleichsmodul (Load Balancer). Da für dieses Lastenausgleichsmodul die öffentliche IP-Adresse verwendet wird, müssen Sie im Abschnitt **dependsOn** eine Abhängigkeit von der öffentlichen IP-Adresse deklarieren. Dies bedeutet, dass der Lastenausgleich erst bereitgestellt wird, nachdem die Bereitstellung der öffentlichen IP-Adresse abgeschlossen ist. Wenn Sie diese Abhängigkeit nicht definieren, erhalten Sie einen Fehler. Resource Manager versucht dann, die Ressourcen parallel bereitzustellen und das Lastenausgleichsmodul auf eine öffentliche IP-Adresse festzulegen, die noch nicht vorhanden ist.

Außerdem erstellen Sie einen Back-End-Adresspool, einige eingehende NAT-Regeln für RDP von VMs und eine Lastenausgleichsregel mit einer TCP-Überprüfung an Port 80 in dieser Ressourcendefinition. Alle Eigenschaften sind unter [Erstellen oder Aktualisieren eines Lastenausgleichs](https://msdn.microsoft.com/library/azure/mt163574.aspx) angegeben.

```json
{
   "apiVersion": "2015-06-15",
   "name": "[parameters('lbName')]",
   "type": "Microsoft.Network/loadBalancers",
   "location": "[resourceGroup().location]",
   "dependsOn": [
     "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]"
   ],
   "properties": {
     "frontendIPConfigurations": [
       {
         "name": "LoadBalancerFrontEnd",
         "properties": {
           "publicIPAddress": {
             "id": "[variables('publicIPAddressID')]"
           }
         }
       }
     ],
     "backendAddressPools": [
       {
         "name": "BackendPool1"
       }
     ],
     "inboundNatRules": [
       {
         "name": "RDP-VM0",
         "properties": {
           "frontendIPConfiguration": {
             "id": "[variables('frontEndIPConfigID')]"
           },
           "protocol": "tcp",
           "frontendPort": 50001,
           "backendPort": 3389,
           "enableFloatingIP": false
         }
       },
       {
         "name": "RDP-VM1",
         "properties": {
           "frontendIPConfiguration": {
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
           "intervalInSeconds": 5,
           "numberOfProbes": 2
         }
       }
     ]
   }
}
```

## Netzwerkschnittstelle
Sie erstellen zwei Netzwerkschnittstellen, und zwar eine für jeden virtuellen Computer. Anstatt für die Netzwerkschnittstellen doppelte Einträge einfügen zu müssen, können Sie die [copyIndex()-Funktion](resource-group-create-multiple.md) verwenden, um die Kopierschleife (als nicLoop bezeichnet) zu durchlaufen und die Zahl der Netzwerkschnittstellen wie in `numberOfInstances`-Variablen definiert zu erstellen. Die Netzwerkschnittstelle ist von der Erstellung des virtuellen Netzwerks und des Lastenausgleichsmoduls abhängig. Hierbei wird das Subnetz verwendet, das bei der Erstellung des virtuellen Netzwerks definiert wird, sowie die Lastenausgleichsmodul-ID für die Konfiguration des Lastenausgleich-Adresspools und der eingehenden NAT-Regeln. Alle Eigenschaften finden Sie unter [Erstellen oder Aktualisieren einer Netzwerkschnittstellenkarte](https://msdn.microsoft.com/library/azure/mt163668.aspx).

```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Network/networkInterfaces",
   "name": "[concat(parameters('nicNamePrefix'), copyindex())]",
   "location": "[resourceGroup().location]",
   "copy": {
     "name": "nicLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "dependsOn": [
     "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]",
     "[concat('Microsoft.Network/loadBalancers/', parameters('lbName'))]"
   ],
   "properties": {
     "ipConfigurations": [
       {
         "name": "ipconfig1",
         "properties": {
           "privateIPAllocationMethod": "Dynamic",
           "subnet": {
             "id": "[variables('subnetRef')]"
           },
           "loadBalancerBackendAddressPools": [
             {
               "id": "[concat(variables('lbID'), '/backendAddressPools/BackendPool1')]"
             }
           ],
           "loadBalancerInboundNatRules": [
             {
               "id": "[concat(variables('lbID'),'/inboundNatRules/RDP-VM', copyindex())]"
             }
           ]
         }
       }
     ]
   }
}
```

## Virtual Machine
Sie erstellen zwei virtuelle Computer, indem Sie die copyIndex()-Funktion verwenden, wie dies bei der Erstellung der [Netzwerkschnittstellen](#network-interface) der Fall war. Die VM-Erstellung hängt vom Speicherkonto, der Netzwerkschnittstelle und der Verfügbarkeitsgruppe ab. Diese VM wird aus einem Marketplace-Image erstellt, wie dies in der `storageProfile`-Eigenschaft definiert ist. `imageReference` wird verwendet, um für das Image Herausgeber, Angebot, SKU und Version zu definieren. Zuletzt wird ein Diagnoseprofil konfiguriert, um die Diagnose für die VM zu ermöglichen.

Befolgen Sie die Anleitung im Artikel [VM-Suche](./virtual-machines/resource-groups-vm-searching.md), um nach den relevanten Eigenschaften für ein Marketplace-Image zu suchen. Für Images, die von Drittanbietern veröffentlicht werden, müssen Sie eine andere Eigenschaft mit dem Namen `plan` angeben. Ein Beispiel hierfür finden Sie in [dieser Vorlage](https://github.com/Azure/azure-quickstart-templates/tree/master/checkpoint-single-nic) im Schnellstartkatalog.


```json
{
   "apiVersion": "2015-06-15",
   "type": "Microsoft.Compute/virtualMachines",
   "name": "[concat(parameters('vmNamePrefix'), copyindex())]",
   "copy": {
     "name": "virtualMachineLoop",
     "count": "[variables('numberOfInstances')]"
   },
   "location": "[resourceGroup().location]",
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
       "computerName": "[concat(parameters('vmNamePrefix'), copyIndex())]",
       "adminUsername": "[parameters('adminUsername')]",
       "adminPassword": "[parameters('adminPassword')]"
     },
     "storageProfile": {
       "imageReference": {
         "publisher": "[parameters('imagePublisher')]",
         "offer": "[parameters('imageOffer')]",
         "sku": "[parameters('imageSKU')]",
         "version": "latest"
       },
       "osDisk": {
         "name": "osdisk",
         "vhd": {
           "uri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net/vhds/','osdisk', copyindex(), '.vhd')]"
         },
         "caching": "ReadWrite",
         "createOption": "FromImage"
       }
     },
     "networkProfile": {
       "networkInterfaces": [
         {
           "id": "[resourceId('Microsoft.Network/networkInterfaces',concat(parameters('nicNamePrefix'),copyindex()))]"
         }
       ]
     },
     "diagnosticsProfile": {
       "bootDiagnostics": {
          "enabled": "true",
          "storageUri": "[concat('http://',parameters('storageAccountName'),'.blob.core.windows.net')]"
       }
     }
}
```

Sie haben das Definieren der Ressourcen für die Vorlage abgeschlossen.

## Parameter

Definieren Sie im Abschnitt „parameters“ die Werte, die beim Bereitstellen der Vorlage angegeben werden können. Definieren Sie nur Parameter für Werte, die während der Bereitstellung ggf. variiert werden sollen. Sie können einen Standardwert für einen Parameter angeben, der verwendet wird, falls während der Bereitstellung kein Wert angegeben wird. Sie können auch die zulässigen Werte definieren, wie dies für den Parameter **imageSKU** dargestellt ist.

```
"parameters": {
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
    "vmNamePrefix": {
      "type": "string",
      "defaultValue": "myVM",
      "metadata": {
        "description": "Prefix to use for VM names"
      }
    },
    "imagePublisher": {
      "type": "string",
      "defaultValue": "MicrosoftWindowsServer",
      "metadata": {
        "description": "Image Publisher"
      }
    },
    "imageOffer": {
      "type": "string",
      "defaultValue": "WindowsServer",
      "metadata": {
        "description": "Image Offer"
      }
    },
    "imageSKU": {
      "type": "string",
      "defaultValue": "2012-R2-Datacenter",
      "allowedValues": [
        "2008-R2-SP1",
        "2012-Datacenter",
        "2012-R2-Datacenter"
      ],
      "metadata": {
        "description": "Image SKU"
      }
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
      "defaultValue": "Standard_D1",
      "metadata": {
        "description": "Size of the VM"
      }
    }
  }
```

## Variablen

Im Abschnitt „variables“ können Sie Werte definieren, die an mehr als einer Stelle in Ihrer Vorlage verwendet werden, oder Werte, die aus anderen Ausdrücken oder Variablen erstellt werden. Variablen werden häufig verwendet, um die Syntax der Vorlage zu vereinfachen.

```
"variables": {
    "availabilitySetName": "myAvSet",
    "subnetName": "Subnet-1",
    "vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('vnetName'))]",
    "subnetRef": "[concat(variables('vnetID'),'/subnets/',variables ('subnetName'))]",
    "publicIPAddressID": "[resourceId('Microsoft.Network/publicIPAddresses',parameters('publicIPAddressName'))]",
    "numberOfInstances": 2,
    "lbID": "[resourceId('Microsoft.Network/loadBalancers',parameters('lbName'))]",
    "frontEndIPConfigID": "[concat(variables('lbID'),'/frontendIPConfigurations/LoadBalancerFrontEnd')]",
    "lbPoolID": "[concat(variables('lbID'),'/backendAddressPools/BackendPool1')]",
    "lbProbeID": "[concat(variables('lbID'),'/probes/tcpProbe')]"
  }
```



## Nächste Schritte

Sie haben die Erstellung Ihrer Vorlage abgeschlossen, und sie ist fertig für die Bereitstellung.

- Weitere Informationen zur Struktur einer Vorlage finden Sie unter [Erstellen von Azure Resource Manager-Vorlagen](resource-group-authoring-templates.md).
- Informationen zur Bereitstellung einer Vorlage finden Sie unter [Bereitstellen einer Ressourcengruppe mit einer Azure Resource Manager-Vorlage](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0323_2016-->