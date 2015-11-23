<properties
	pageTitle="Automatisches Skalieren mit VM-Skalierungsgruppen | Microsoft Azure"
	description="Erste Schritte zum Erstellen und Verwalten von VM-Skalierungsgruppen für virtuelle Computer in Azure"
	services="virtual-machines"
	documentationCenter=""
	authors="gbowerman"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="11/11/2015"
	ms.author="guybo"/>

# Automatisches Skalieren von Computern in einer VM-Skalierungsgruppe

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-create-windows-powershell-service-manager.md).


Dieses Dokument ist eine Schnelleinführung in das Erstellen und Verwalten Ihrer ersten Azure-VM-Skalierungsgruppen während der öffentlichen Vorschau.

In dieser Einführung wird davon ausgegangen, dass Sie mit der Bereitstellung von Azure-Ressourcen (z. B. virtuellen Computern und VNETs) mit Azure-Ressourcen-Manager-Vorlagen vertraut sind. Wenn dies nicht der Fall ist, finden Sie in den ersten drei Links im Abschnitt „Ressourcen“ einen Überblick über den Ressourcen-Manager.

## Was sind VM-Skalierungsgruppen, und warum werden sie verwendet?

VM-Skalierungsgruppen sind eine Azure Compute-Ressource, mit der Sie eine Gruppe von identischen virtuellen Computern bereitstellen und verwalten können.

Alle virtuellen Computer in einer VM-Skalierungsgruppe sind gleich konfiguriert. Dadurch ermöglichen VM-Skalierungsgruppen eine echte automatische Skalierung. Eine Vorabbereitstellung der virtuellen Computer ist dabei nicht erforderlich. Das erleichtert die Erstellung leistungsfähiger Dienste, die auf große Rechenleistung, Big Data und Workloads in Containern sowie auf beliebige Anwendungen ausgerichtet sind, für die Computeressourcen hoch- und herunterskaliert werden müssen. Skalierungsvorgänge werden implizit über Fehler- und Updatedomänen ausgeglichen. Eine Einführung in das Thema „VM-Skalierungsgruppen“ finden Sie in der [Ankündigung im Azure-Blog](https://azure.microsoft.com/blog/azure-vm-scale-sets-public-preview) (in englischer Sprache).

## Erstellen und Verwenden von VM-Skalierungsgruppen

VM-Skalierungsgruppen können mithilfe von JSON-Vorlagen und REST-Aufrufen definiert und bereitgestellt werden, ganz genauso wie einzelne virtuelle Azure-Ressourcen-Manager-Computer. Es können daher alle Standardbereitstellungsmethoden aus dem Azure-Ressourcen-Manager verwendet werden. Das wird in diesem Dokument anhand einiger Beispiele gezeigt.

Für einzelne Integrationsbereiche bei den VM-Skalierungsgruppen, z. B. für die Unterstützung imperativer Befehle in Skript- und Programmiersprachen und für die vollständige Integration ins Portal, laufen noch Entwicklungsarbeiten. Die Ergebnisse werden während der Vorschau Schritt für Schritt veröffentlicht.

## Bereitstellen und Verwalten von VM-Skalierungsgruppen mit dem Azure-Portal

Anfangs können Sie im Azure-Portal noch keine VM-Skalierungsgruppen vollständig neu erstellen. Wir arbeiten zurzeit an der Portalunterstützung. In der ersten Phase können Sie im Portal nur die Skalierungsgruppen sehen, die Sie zuvor auf der Basis von Vorlagen oder mit einem Skript erstellt haben. „Portal“ bezieht sich in allen Fällen auf das neue Azure-Portal: [https://portal.azure.com/](https://portal.azure.com/).

Sie können jedoch die Möglichkeiten des Portals zum Bereitstellen von Vorlagen nutzen, um beliebige Ressourcen bereitzustellen, darunter auch Skalierungsgruppen. Recht unkompliziert lässt sich eine einfache Skalierungsgruppe bereitstellen, indem Sie einen Link wie den folgenden verwenden:

_https://portal.azure.com/#create/Microsoft.Template/uri/<link to VM Scale Set JSON template>_

Eine Reihe von Beispielvorlagen für VM-Skalierungsgruppen (VM Scale Sets, VMSS) finden Sie im GitHub-Repository für Azure-Schnellstartvorlagen unter [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates). Suchen Sie dort nach Vorlagen mit _vmss_ im Titel.

In den Readme-Dateien für diese Vorlagen finden Sie eine Schaltfläche wie diese, die eine Verknüpfung mit dem Bereitstellungsfeature des Portals herstellt.

Zum Bereitstellen der Skalierungsgruppe klicken Sie auf diese Schaltfläche und geben dann alle Parameter an, die im Portal erforderlich sind. Hinweis: Wenn Sie sich nicht sicher sind, ob eine Ressource Großbuchstaben oder eine gemischte Schreibung in Groß- und Kleinbuchstaben unterstützt, ist es sicherer, für die Parameterwerte nur Kleinbuchstaben zu verwenden.

**Überwachen einer VM-Skalierungsgruppenressource im Portal und mit dem Ressourcen-Explorer**

Nach der Bereitstellung wird die betreffende Skalierungsgruppe im Portal in einer einfachen Ansicht angezeigt. Während der Vorschau werden jedoch anfangs keine detaillierten Eigenschaften angezeigt, und Sie können auch keinen Drilldown für die einzelnen virtuellen Computer in der VM-Skalierungsgruppe ausführen.

Bis die Integration in das Portal vollständig umgesetzt ist, empfehlen wir, die VM-Skalierungsgruppen mit dem **Azure-Ressourcen-Explorer** anzuzeigen: [https://resources.azure.com](https://resources.azure.com). Bei VM-Skalierungsgruppen handelt es sich um Ressourcen unter „Microsoft.Compute“. Sie können sie daher anzeigen, indem Sie auf dieser Website die folgenden Links öffnen:

Abonnements -> Ihr Abonnement -> resourceGroups -> providers -> Microsoft.Compute -> virtualMachineScaleSets -> Ihre VM-Skalierungsgruppe -> usw.

## Bereitstellen und Verwalten von VM-Skalierungsgruppen mit PowerShell

Mit jeder aktuellen Azure PowerShell-Version können Sie Vorlagen für VM-Skalierungsgruppen bereitstellen und Ressourcen abfragen.

**Wichtiger Hinweis:** Die folgenden Beispiele beziehen sich auf [Azure PowerShell 1.0](https://azure.microsoft.com/blog/azps-1-0-pre/). In dieser Version wurde das Präfix _AzureRm_ für viele Befehle eingeführt. Wenn Sie eine frühere Version von PowerShell verwenden, z. B. Version 0.9.8 oder niedriger, entfernen Sie **Rm** aus den Beispielbefehlen. In den Beispielen wird außerdem davon ausgegangen, dass Sie in Ihrer PowerShell-Umgebung bereits eine Anmeldeverbindung mit Azure eingerichtet haben (_Login-AzureRmAccount_).

Beispiele:

&#35; **Erstellen einer Ressourcengruppe**

New-AzureRmResourcegroup -name myrg -location 'Southeast Asia'

&#35; **Erstellen einer VM-Skalierungsgruppe mit 2 virtuellen Computern**

New-AzureRmResourceGroupDeployment -name dep1 -vmSSName myvmss -instanceCount 3 -ResourceGroupName myrg -TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

Sie werden zur Eingabe aller fehlenden Parameter aufgefordert (wie „location“, „username“, „password“ in diesem Beispiel).

&#35; **Abrufen von Details zur VM-Skalierungsgruppe**

Get-AzureRmResource -name myvmss -ResourceGroupName myrg -ResourceType Microsoft.Compute/virtualMachineScaleSets -ApiVersion 2015-06-15

&#35; oder verwenden Sie zum Anzeigen von mehr Details einen senkrechten Strich („Pipe“): | ConvertTo-Json -Depth 10

&#35; oder fügen Sie zum Anzeigen von noch mehr Details dem Befehl „–debug“ hinzu.

&#35; Hochskalieren oder Herunterskalieren

New-AzureRmResourceGroupDeployment -name dep2 -vmSSName myvmss -instanceCount 2 -ResourceGroupName myrg –TemplateUri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json)

&#35; **Entfernen einer Skalierungsgruppe**

&#35; Einfach: Entfernen der gesamten Ressourcengruppe und ihres gesamten Inhalts:

Remove-AzureRmResourceGroup -Name myrg

&#35; Genau: Entfernen einer spezifischen Ressource:

Remove-AzureRmResource -Name myvmss -ResourceGroupName myrg -ApiVersion 2015-06-15 -ResourceType Microsoft.Compute/virtualMachineScaleSets

### Imperative PowerShell-Befehle für Skalierungsgruppen

Ein bevorstehender Build von Azure PowerShell wird eine Reihe von imperativen Befehlen enthalten, damit VM-Skalierungsgruppen ohne Vorlagen erstellt und verwaltet werden können. Dieser Build steht als Vorschau hier zur Verfügung: [https://github.com/AzureRT/azure-powershell/releases](https://github.com/AzureRT/azure-powershell/releases). Beispiele für die Verwendung der imperativen API finden Sie hier:

[https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1](https://github.com/huangpf/azure-powershell/blob/vmss/src/ResourceManager/Compute/Commands.Compute.Test/ScenarioTests/VirtualMachineScaleSetTests.ps1)

## Bereitstellen und Verwalten von VM-Skalierungsgruppen über die Azure-Befehlszeilenschnittstelle

Mit jeder aktuellen Version der Azure-Befehlszeilenschnittstelle können Sie Vorlagen für VM-Skalierungsgruppen bereitstellen und Ressourcen abfragen.

Am einfachsten lässt sich die Befehlszeilenschnittstelle aus einem Docker-Container installieren. Informationen zur Installation finden Sie unter: [https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/](https://azure.microsoft.com/blog/run-azure-cli-as-a-docker-container-avoid-installation-and-setup/)

Informationen zur Verwendung der Befehlszeilenschnittstelle finden Sie unter: [https://azure.microsoft.com/documentation/articles/xplat-cli/](https://azure.microsoft.com/documentation/articles/xplat-cli/)

### Beispiele für die Nutzung der Befehlszeilenschnittstelle bei VM-Skalierungsgruppen:

&#35; **Erstellen einer Ressourcengruppe**

azure group create myrg "Southeast Asia"

&#35; **Erstellen einer VM-Skalierungsgruppe**

azure group deployment create -g myrg -n dep2 --template-uri [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json)

&#35; Dieser Befehl sollte dynamisch nach Parametern fragen, oder Sie können sie auch als Argumente angeben.

&#35; **Abrufen von Details zur Skalierungsgruppe**

azure resource show -n vmssname -r Microsoft.Compute/virtualMachineScaleSets -o 2015-06-15 -g myrg

&#35; oder um mehr Details zu erhalten:

azure resource show –n vmssname –r Microsoft.Compute/virtualMachineScaleSets –o 2015-06-15 –g myrg –json –vv

Ein bevorstehender Build der Azure-Befehlszeilenschnittstelle wird imperative Befehle enthalten, mit denen Sie VM-Skalierungsgruppen ohne Vorlagen direkt bereitstellen und verwalten und auch Vorgänge an den virtuellen Computern in VM-Skalierungsgruppen ausführen können. Sie können diesen Build hier verfolgen: [https://github.com/AzureRT/azure-xplat-cli/releases/](https://github.com/AzureRT/azure-xplat-cli/releases/)

## Vorlagen für VM-Skalierungsgruppen

Dieser Abschnitt beschäftigt sich detailliert mit einer einfachen Azure-Vorlage zum Erstellen einer VM-Skalierungsgruppe und vergleicht diese Vorlage mit den Vorlagen, die zum Erstellen von virtuellen Computern mit nur einer Instanz verwendet werden. Es gibt auch ein anschauliches Video mit einer Analyse einer VM-Skalierungsgruppenvorlage: [https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player)

### Anatomie einer Vorlage

Im Folgenden analysieren wir eine einfache Vorlage, die eine VM-Skalierungsgruppe mit virtuellen Ubuntu-Computern erstellt. In diesem Beispiel werden auch die Ressourcen erstellt, von denen eine VM-Skalierungsgruppe abhängig ist, also z. B. VNET, Speicherkonto usw. Sie finden dieses Beispiel hier: [https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json](https://raw.githubusercontent.com/gbowerman/azure-myriad/master/vmss-ubuntu-vnet-storage.json). – Beachten Sie jedoch, dass es für dieses „Hello World“-Beispiel keine direkte Möglichkeit gibt, von außerhalb Ihres VNETs eine Verbindung mit den virtuellen Computern in der VM-Skalierungsgruppe herzustellen, weil den virtuellen Computern interne IP-Adressen zugewiesen sind. Im Abschnitt „Szenarien“ und in den Beispielen unter [Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates) finden Sie Wege, wie Sie von außen mit Load Balancern oder Jumpboxes eine Verbindung herstellen können.

### Vorlagenheader

Geben das Schema und die Inhaltsversion an:

{

   "$schema": "http://schema.management.azure.com/schemas/2015-01-01-preview/deploymentTemplate.json",

   "contentVersion": "1.0.0.0",

### Parameters

Wie bei jeder anderen Vorlage in Azure-Ressourcen-Manager (ARM) definiert dieser Abschnitt die Parameter, die während der Bereitstellung angegeben werden. Dazu zählen in diesem Beispiel der Name der VM-Skalierungsgruppe, die Anzahl der für den Start gewünschten VM-Instanzen und eine eindeutige Zeichenfolge, die beim Erstellen von Speicherkonten verwendet wird. (Verwenden Sie beim Eingeben von Werten für Objekte wie Speicherkonten immer Kleinbuchstaben, wenn Sie nicht ganz genau wissen, wie die Groß- und Kleinschreibung behandelt wird.)

````
 "parameters": {

    "vmSSName": {

      "type": "string",

      "metadata": {

        "description": "Scale Set name, also used in this template as a base for naming resources (hence limited less than 10 characters)."

      },

      "maxLength": 10

    },

    "instanceCount": {

      "type": "int",

      "metadata": {

        "description": "Number of VM instances"

      },

      "maxValue": 100

    },

    "adminUsername": {

      "type": "string",

      "metadata": {

        "description": "Admin username on all VMs."

      }

    },

    "adminPassword": {

      "type": "securestring",

      "metadata": {

        "description": "Admin password on all VMs."

      }

    }

  },
````

### Variables

Eine Standardkomponente von ARM-Vorlagen, in der die Variablen definiert werden, auf die Sie später in der Vorlage verweisen. In diesem Beispiel verwenden wir Variablen, um das gewünschte Betriebssystem zu definieren und um einige Netzwerkkonfigurationsdetails, Speichereinstellungen und den Speicherort anzugeben. Für den Speicherort („location“) verwenden wir die Funktion _location()_, um ihn aus der Ressourcengruppe zu übernehmen, in der die Bereitstellung erfolgt.

Beachten Sie, dass für ein Speicherkontopräfix ein Array aus eindeutigen Zeichenfolgen definiert wird. Dies wird im Abschnitt „Storage“ erklärt.

````
  "variables": {

    "apiVersion": "2015-06-15",

    "newStorageAccountSuffix": "[concat(parameters('vmssName'), 'sa')]",

    "uniqueStringArray": [

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '0')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '1')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '2')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '3')))]",

      "[concat(uniqueString(concat(resourceGroup().id, deployment().name, variables('newStorageAccountSuffix'), '4')))]"

    ],

    "vmSize": "Standard\_A1",

    "vhdContainerName": "[concat(parameters('vmssName'), 'vhd')]",

    "osDiskName": "[concat(parameters('vmssName'), 'osdisk')]",

    "virtualNetworkName": "[concat(parameters('vmssName'), 'vnet')]",

    "subnetName": "[concat(parameters('vmssName'), 'subnet')]",

    "nicName": "[concat(parameters('vmssName'), 'nic')]",

    "ipConfigName": "[concat(parameters('vmssName'), 'ipconfig')]",

    "addressPrefix": "10.0.0.0/16",

    "subnetPrefix": "10.0.0.0/24",

    "storageAccountType": "Standard\_LRS",

    "location": "[resourceGroup().location]",

    "osType": {

      "publisher": "Canonical",

      "offer": "UbuntuServer",

      "sku": "15.10",

      "version": "latest"

    },

    "imageReference": "[variables('osType')]"

  },
````

### Resources

In diesem Abschnitt werden die einzelnen Ressourcentypen definiert.

````
   "resources": [
````


**Storage.** Beim Erstellen einer VM-Skalierungsgruppe geben Sie ein Array von Speicherkonten an. Die Betriebssystem-Datenträger für die VM-Instanzen werden dann gleichmäßig auf die einzelnen Konten verteilt. In Zukunft wird bei den VM-Skalierungsgruppen ein Ansatz mit verwalteten Datenträgern umgesetzt. Dann müssen Sie Speicherkonten nicht mehr selbst verwalten und können anstelle dessen lediglich die Speichereigenschaften als Teil der Skalierungsgruppendefinition beschreiben. Derzeit müssen Sie aber vorab so viele Speicherkonten erstellen, wie sie benötigen. Wir empfehlen, dass Sie 5 Speicherkonten erstellen. Das reicht bequem für bis zu 100 virtuelle Computer in einer Skalierungsgruppe aus (das aktuelle Maximum).

Wenn Sie eine Gruppe von Speicherkonten erstellen, werden diese über die Partitionen in einem Speicherstempel verteilt. Dabei hängt das Verteilungsschema von den ersten Buchstaben im Namen des Speicherkontos ab. Aus diesem Grund sowie zur Leistungsoptimierung wird empfohlen, dass der Name jedes zu erstellenden Speicherkontos mit einem anderen Buchstaben des Alphabets beginnt und dass die gewählten Buchstaben im Alphabet weit auseinander liegen. Sie können diese Namen manuell vergeben. In diesem Beispiel wird jedoch die uniqueString()-Funktion verwendet, um eine pseudozufällige Verteilung zu erreichen:

````
    {

      "type": "Microsoft.Storage/storageAccounts",

      "name": "[concat(variables('uniqueStringArray')[copyIndex()], variables('newStorageAccountSuffix'))]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "copy": {

        "name": "storageLoop",

        "count": 5

      },

      "properties": {

        "accountType": "[variables('storageAccountType')]"

      }

    },
````

**Virtual Network.** Erstellen Sie ein VNET. Beachten Sie, dass Sie mehrere Skalierungsgruppen und auch einzelne virtuelle Computer in demselben VNET platzieren können.

````
    {

      "type": "Microsoft.Network/virtualNetworks",

      "name": "[variables('virtualNetworkName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

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
````

### Die virtualMachineScaleSets-Ressource

Die _virtualMachineScaleSets_-Ressource ist in vieler Hinsicht der _virtualMachines_-Ressource ähnlich. Beide werden vom Microsoft.Compute-Ressourcenanbieter zur Verfügung gestellt, und sie befinden sich auf derselben Ebene. Der Hauptunterschied besteht darin, dass Sie mit einem _capacity_-Wert angeben, wie viele virtuelle Computer Sie erstellen, und dass alles hier Festgelegte für alle virtuellen Computer in der VM-Skalierungsgruppe gilt.

Beachten Sie, dass der _dependsOn_-Abschnitt auf die oben erstellten Speicherkonten und das erstellte VNET verweist und dass „capacity“ auf den _instanceCount_-Parameter verweist.

````
    {

      "type": "Microsoft.Compute/virtualMachineScaleSets",

      "name": "[parameters('vmssName')]",

      "location": "[variables('location')]",

      "apiVersion": "[variables('apiVersion')]",

      "dependsOn": [

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Storage/storageAccounts/', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'))]",

        "[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"

      ],

      "sku": {

        "name": "[variables('vmSize')]",

        "tier": "Standard",

        "capacity": "[parameters('instanceCount')]"

      },
````

**Properties**

VM-Skalierungsgruppen haben eine upgradePolicy-Einstellung. Künftige Versionen werden segmentweise Aktualisierungen unterstützen (z. B. zum Ändern der Konfiguration für 20 % der virtuellen Computer zu einem bestimmten Zeitpunkt). Derzeit kann „upgradePolicy“ aber nur auf „manual“ oder „automatic“ festgelegt werden. „Manual“ bedeutet, dass nach dem Ändern und erneuten Bereitstellen einer Vorlage die Änderungen nur wirksam werden, wenn neue virtuelle Computer erstellt oder Erweiterungen aktualisiert werden, z. B. beim Erhöhen von _capacity_. „Automatic“ bedeutet, dass alle virtuellen Computer auf einen Schlag aktualisiert werden, verbunden mit einem Neustart für alle. Im Allgemeinen ist „Manual“ die sicherere Vorgehensweise. Sie können virtuelle Computer je nach Bedarf einzeln löschen und erneut bereitstellen, um graduelle Aktualisierungen vorzunehmen.

````
      "properties": {

         "upgradePolicy": {

         "mode": "Manual"

        },
````

**Properties -> virtualMachineProfile**

Hier können Sie auf die oben erstellten Speicherkonten als Container für virtuelle Computer verweisen. Sie müssen die eigentlichen Container nicht vorab erstellen, lediglich die Konten müssen vorab erstellt werden.

````
        "virtualMachineProfile": {

          "storageProfile": {

            "osDisk": {

              "vhdContainers": [

                "[concat('https://', variables('uniqueStringArray')[0], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[1], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[2], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[3], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]",

                "[concat('https://', variables('uniqueStringArray')[4], variables('newStorageAccountSuffix'), '.blob.core.windows.net/', variables('vhdContainerName'))]"

              ],

              "name": "[variables('osDiskName')]",

              "caching": "ReadOnly",

              "createOption": "FromImage"

            },

            "imageReference": "[variables('imageReference')]"

          },
````

**Properties -> osProfile**

Ein Unterschied zwischen VM-Skalierungsgruppen und einzelnen virtuellen Computern (VMs, Virtual Machines) besteht darin, dass der Computername ein Präfix ist. VM-Instanzen in der VM-Skalierungsgruppe werden mit Namen wie myvm\_0, myvm\_1 usw. erstellt.

````
          "osProfile": {

            "computerNamePrefix": "[parameters('vmSSName')]",

            "adminUsername": "[parameters('adminUsername')]",

            "adminPassword": "[parameters('adminPassword')]"

          },
````

**Properties -> networkProfile**

Bedenken Sie beim Definieren des Netzwerkprofils für eine VM-Skalierungsgruppe (VMSS, VM Scale Set), dass sowohl die NIC-Konfiguration (Network Interface Card) als auch die IP-Konfiguration einen Namen hat. Die NIC-Konfiguration verfügt über eine Einstellung „_primary_“, und die Subnetz-ID verweist zurück auf die Subnetzressource, die oben als Teil des VNETs erstellt wurde.

````
          "networkProfile": {

            "networkInterfaceConfigurations": [

              {

                "name": "[variables('nicName')]",

                "properties": {

                  "primary": "true",

                  "ipConfigurations": [

                    {

                      "name": "[variables('ipConfigName')]",

                      "properties": {

                        "subnet": {

                          "id": "[concat('/subscriptions/', subscription().subscriptionId,'/resourceGroups/', resourceGroup().name, '/providers/Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'), '/subnets/', variables('subnetName'))]"

                        }

                      }

                    }

                  ]

                }

              }

            ]

          }
````

**Properties -> extensionProfile**

Für das einfache Beispiel oben war kein Erweiterungsprofil nötig. In der folgenden Vorlage können Sie ein Erweiterungsprofil in Aktion beobachten. Hier wird Apache und PHP mit der CustomScript-Erweiterung bereitgestellt: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale). – Beachten Sie, dass die Netzwerk-IP-Eigenschaften in diesem Beispiel auch auf einen Load Balancer verweisen. Load Balancer werden ausführlicher im Abschnitt „Szenarien für VM-Skalierungsgruppen“ beschrieben.

## Szenarien für VM-Skalierungsgruppen

Dieser Abschnitt beschäftigt sich mit einigen typischen Szenarien für VM-Skalierungsgruppen und Beispielvorlagen. Derzeit sind dafür noch Vorlagen erforderlich, aber Szenarien werden künftig in das Portal integriert. Auch einige auf höherer Ebene angesiedelte Azure-Dienste (wie Batch, Service Fabric und Azure Container Service) werden diese Szenarien verwenden.

## Zugriff mit RDP oder SSH auf VM-Skalierungsgruppeninstanzen

Eine VM-Skalierungsgruppe wird in einem VNET erstellt, und den einzelnen virtuellen Computern werden keine öffentlichen IP-Adressen zugeordnet. Das ist gut so, weil Sie im Allgemeinen den Aufwand und die Verwaltungsarbeiten vermeiden möchten, die mit dem Zuordnen von separaten IP-Adressen zu allen zustandslosen Ressourcen in Ihrem Computenetz verbunden sind. Außerdem können Sie über andere Ressourcen in Ihrem VNET, die über öffentliche IP-Adressen verfügen, leicht eine Verbindung mit diesen virtuellen Computern herstellen, z. B. über Load Balancer oder eigenständige virtuelle Computer.

Zwei einfache Methoden zum Herstellen einer Verbindung mit virtuellen Computern in VM-Skalierungsgruppen werden im Folgenden beschrieben:

### Herstellen einer Verbindung mit virtuellen Computern mithilfe von NAT-Regeln

Sie können eine öffentliche IP-Adresse erstellen, sie einem Load Balancer zuweisen und NAT-Regeln (Network Address Translation) für eingehenden Datenverkehr definieren, die einen Port auf der IP-Adresse zu einem Port auf dem virtuellen Computer in der VM-Skalierungsgruppe zuordnen. Beispiel:

Public IP->Port 50000 -> vmss\_0->Port 22 Public IP->Port 50001 -> vmss\_1->Port 22 Public IP->Port 50002-> vmss\_2->Port 22

Hier ist ein Beispiel für das Erstellen einer VM-Skalierungsgruppe, bei der NAT-Regeln verwendet werden, um eine SSH-Verbindung (Secure Shell) zu jedem virtuellen Computer in der Skalierungsgruppe über eine einzige öffentlichen IP-Adresse zu ermöglichen: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-linux-nat)

Hier ist ein Beispiel, bei dem dasselbe mit RDP (Remote Desktop Protocol) und Windows erreicht wird: [https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-windows-nat)

### Herstellen einer Verbindung mit virtuellen Computern mithilfe einer „Jumpbox“

Wenn Sie eine VM-Skalierungsgruppe und einen eigenständigen virtuellen Computer in demselben VNET erstellen, können der eigenständige virtuelle Computer und die virtuellen Computer in der VM-Skalierungsgruppe eine Verbindung miteinander herstellen, indem sie ihre internen IP-Adressen verwenden, die durch das VNET/Subnetz definiert sind.

Wenn Sie eine öffentliche IP-Adresse erstellen und sie dem eigenständigen virtuellen Computer zuweisen, können Sie mit RDP oder SSH eine Verbindung mit dem eigenständigen virtuellen Computer herstellen. Anschließend können Sie von diesem Computer aus eine Verbindung mit den VM-Skalierungsgruppeninstanzen herstellen. Vielleicht erkennen Sie an dieser Stelle, dass eine einfache VM-Skalierungsgruppe inhärent sicherer ist als ein einfacher eigenständiger virtueller Computer in Standardkonfiguration mit einer öffentlichen IP-Adresse.

Beispielhaft für diese Herangehensweise erstellt die folgende Vorlage einen einfachen Mesos-Cluster mit einem eigenständigen virtuellen Mastercomputer, der einen Cluster von virtuellen Computern verwaltet, die zu einer VM-Skalierungsgruppe gehören: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json)

## Roundrobin-Lastenausgleich für VM-Skalierungsgruppeninstanzen

Wenn Sie eine Arbeitslast mit einem Roundrobin-Ansatz an einen Computecluster aus virtuellen Computern übergeben möchten, können Sie einen Azure Load Balancer mit entsprechenden Lastenausgleichsregeln konfigurieren. Außerdem können Sie Tests definieren, um zu überprüfen, ob Ihre Anwendung ordnungsgemäß ausgeführt wird. Diese Tests pingen die Ports mit einem festgelegten Protokoll in vorgegebenen Zeitabständen und mit einem angegebenen Anforderungspfad.

Im folgenden Beispiel wird eine VM-Skalierungsgruppe mit virtuellen Computern erstellt, auf denen ein IIS-Webserver ausgeführt wird. Dabei wird die Last für die einzelnen virtuellen Computer mit einem Load Balancer ausgeglichen. Im dem Beispiel wird HTTP verwendet, um auf jedem virtuellen Computer eine spezifische URL zu pingen: [https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json](https://github.com/gbowerman/azure-myriad/blob/master/vmss-win-iis-vnet-storage-lb.json). – Schauen Sie sich hier besonders den Ressourcentyp „Microsoft.Network/loadBalancers“ sowie „networkProfile“ und „extensionProfile“ in „virtualMachineScaleSet“ an.

## VM-Skalierungsgruppeninstanzen mit Azure Autoscale

Wenn Sie die Anzahl der Instanzen (_capacity_) in der VM-Skalierungsgruppe abhängig von der CPU-/Speicher-/Datenträgerauslastung oder abhängig von anderen Ereignissen variieren möchten, können Sie im Microsoft.Insights-Ressourcenanbieter auf eine große Gruppe von Einstellungen für die automatische Skalierung zurückgreifen. Informieren Sie sich über die verfügbaren Einstellungen in der Insights-REST-Dokumentation: [https://msdn.microsoft.com/library/azure/dn931953.aspx](https://msdn.microsoft.com/library/azure/dn931953.aspx).

Die automatische Skalierung von Insights wird direkt in die VM-Skalierungsgruppen integriert. Um sie einzurichten, definieren Sie einen Ressourcentyp „Microsoft.Insights/autoscaleSettings“, der über einen _targetResourceUri_ und einen _metricResourceUri_ verfügt, der auf die Skalierungsgruppe zurückverweist. Beim Definieren einer Skalierungsgruppe schließen Sie ein _extensionProfile_ ein, das den Azure-Diagnose-Agent (WAD) unter Windows oder die Linux-Diagnoseerweiterung (LDE) unter Linux installiert. Hier ist ein Linux-Beispiel, das bis zu einem vordefinierten Grenzwert virtuelle Computer hinzufügt, wenn die CPU-Auslastung bei einer Zeitgranularität von 1 Minute und einem Stichprobenzeitraum von 5 Minuten größer als 50 % ist:

[https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale](https://github.com/Azure/azure-quickstart-templates/tree/master/201-vmss-lapstack-autoscale).

Zukünftig wird die automatische Skalierung für VM-Skalierungsgruppen auch direkt in das Azure-Portal integriert.

## Bereitstellen einer VM-Skalierungsgruppe als Computecluster in einem PaaS-Cluster-Manager

VM-Skalierungsgruppen werden mitunter als Workerrolle der nächsten Generation beschrieben. Diese Beschreibung ist zwar zutreffend, aber sie lädt zu Verwechslungen zwischen den Features von Skalierungsgruppen und PaaS v1-Workerrollen ein.

In einem bestimmten Sinn stellen VM-Skalierungsgruppen tatsächlich eine „Workerrolle“ oder eine Workerressource bereit: Sie bieten eine generalisierte Computeressource, die von Plattformen und Laufzeitumgebungen unabhängig ist, sich anpassen lässt und in die Azure-Ressourcen-Manager-IaaS integriert ist.

Eine PaaS v1-Workerrolle ist zwar im Hinblick auf die Unterstützung für Plattformen/Laufzeitumgebungen eingeschränkt (nur für Images der Windows-Plattform), aber sie bietet auch Dienste wie VIP-Austausch, konfigurierbare Upgradeeinstellungen und Einstellungen für laufzeitumgebungs- und anwendungsspezifische Bereitstellungen, die entweder _noch_ nicht in VM-Skalierungsgruppen zur Verfügung stehen oder die durch auf höherer Ebene angesiedelte PaaS-Dienste wie Service Fabric umgesetzt werden. Vor diesem Hintergrund können Sie VM-Skalierungsgruppen als eine Infrastruktur betrachten, die PaaS unterstützt. Das heißt: PaaS-Lösungen wie Service Fabric oder Cluster-Manager wie Mesos können auf VM-Skalierungsgruppen aufsetzen, um eine skalierbare Compute-Ebene zu bilden.

Hier ist ein Beispiel für einen Mesos-Cluster, der eine VM-Skalierungsgruppe und einen eigenständigen virtuellen Computer in demselben VNET bereitstellt. Der eigenständige virtuelle Computer ist ein Mesos-Master, und die VM-Skalierungsgruppe stellt eine Gruppe von Slaveknoten dar: [https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json](https://github.com/gbowerman/azure-myriad/blob/master/mesos-vmss-simple-cluster.json). Bei künftigen Versionen des [Azure Container Service](https://azure.microsoft.com/blog/azure-container-service-now-and-the-future/) werden komplexere und besser geschützte Versionen dieses auf VM-Skalierungsgruppen beruhenden Szenarien umgesetzt.

## Hochskalieren und Herunterskalieren einer VM-Skalierungsgruppe

Wenn Sie die Anzahl der virtuellen Computer in einer VM-Skalierungsgruppe erhöhen oder verringern möchten, ändern Sie einfach die _capacity_-Eigenschaft und stellen die Vorlage erneut bereit. Diese Einfachheit erleichtert es, eine benutzerdefinierte Skalierungsschicht zu schreiben, falls Sie benutzerdefinierte Skalierungsereignisse definieren möchten, die von der automatischen Skalierung in Azure nicht unterstützt werden.

Wenn Sie eine Vorlage erneut bereitstellen, um die Kapazität zu erhöhen, können Sie dafür eine viel kleinere Vorlage definieren, die nur die SKU und die geänderte Kapazität enthält. Ein entsprechendes Beispiel finden Sie hier: [https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-scale-in-or-out/azuredeploy.json](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vmss-linux-nat/azuredeploy.json).

## Leistungsfähigkeit von VM-Skalierungsgruppen und Leitlinien zum Skalieren

- Erstellen Sie während der öffentlichen Vorschau höchstens 500 virtuelle Computer in mehreren VM-Skalierungsgruppen gleichzeitig. Dieses Limit wird in Zukunft erhöht.
- Planen Sie mit höchstens 40 virtuellen Computern pro Speicherkonto.
- Nutzen Sie für die Namen der Speicherkonten verschiedene und im Alphabet möglichst weit auseinanderliegende Buchstaben. Die Beispielvorlagen für VM-Skalierungsgruppen (VMSS, Virtual Machine Scale Sets) in den [Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates/) veranschaulichen die empfohlene Vorgehensweise.
- Wenn Sie benutzerdefinierte virtuelle Computer verwenden, sollten Sie mit höchstens 40 virtuellen Computern pro VM-Skalierungsgruppe in einem einzelnen Speicherkonto planen. Sie müssen das Image vorab in das Speicherkonto kopieren, bevor Sie mit der Bereitstellung der VM-Skalierungsgruppe beginnen können. Weitere Informationen finden Sie in den FAQs.
- Planen Sie mit höchstens 2048 virtuellen Computern pro VNET. Dieses Limit wird in Zukunft erhöht.
- Die Anzahl der virtuellen Computer, die Sie erstellen können, wird durch Ihr Computekernkontigent in einer beliebigen Region begrenzt. Unter Umständen müssen Sie sich an den Kundensupport wenden und das Limit im Computekontingent auch dann erhöhen lassen, wenn Sie heute über ein hohes Limit an Kernen verfügen, die für Clouddienste oder IaaS v1 genutzt werden können. Sie können Ihr Kontingent mit dem folgenden Befehl der Azure-Befehlszeilenschnittstelle abfragen: _azure vm list-usage_. Oder Sie verwenden dazu den folgenden PowerShell-Befehl: _Get-AzureRmVMUsage_ (falls Sie eine Version von PowerShell vor 1.0 nutzen, verwenden Sie _Get-AzureVMUsage_).


## Häufig gestellte Fragen (FAQ) zu VM-Skalierungsgruppen

**F: Wie viele virtuelle Computer können in eine VM-Skalierungsgruppe aufgenommen werden?**

A: 100, sofern Sie Plattformimages verwenden, die über mehrere Speicherkonten verteilt werden können. Wenn Sie benutzerdefinierte Images verwenden, bis zu 40, weil benutzerdefinierte Images während der Vorschau nur in einem einzelnen Speicherkonto platziert werden können.

**Welche anderen Ressourceneinschränkungen bestehen für VM-Skalierungsgruppen?**

A: Es gelten die bestehenden Azure-Diensteinschränkungen: [https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/](https://azure.microsoft.com/documentation/articles/azure-subscription-service-limits/)

Außerdem dürfen während der Vorschauperiode höchstens 500 virtuelle Computer in mehreren Skalierungsgruppen pro Region erstellt werden.

**F: Werden Datenträger innerhalb von VM-Skalierungsgruppen unterstützt?**

A: Nicht in der ersten Version. Zum Speichern von Daten haben Sie folgende Möglichkeiten:

- Azure-Dateien (SMB-freigegebene Laufwerke)

- Betriebssystem-Laufwerk

- Temporäres Laufwerk (lokal, nicht durch Azure Storage gesichert)

- Externer Datendienst (z. B. Remote-DB, Azure-Tabellen, Azure-Blobs)

**F: In welchen Azure-Regionen werden VM-Skalierungsgruppen unterstützt?**

A: VM-Skalierungsgruppen werden in allen Regionen unterstützt, die den Azure-Ressourcen-Manager unterstützen.

**F: Wie erstellen Sie eine VM-Skalierungsgruppe mit einem benutzerdefinierten Image?**

A: Lassen Sie die vhdContainers-Eigenschaft leer, wie z. B hier:

````
"storageProfile": {
   "osDisk": {
      "name": "vmssosdisk",
      "caching": "ReadOnly",
      "createOption": "FromImage",
      "image": {
         "uri": [https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd](https://mycustomimage.blob.core.windows.net/system/Microsoft.Compute/Images/mytemplates/template-osDisk.vhd)
     },
     "osType": "Windows"
  }
},
````

**F: Welche virtuellen Computer werden entfernt, wenn ich meine Kapazität für VM-Skalierungsgruppen mit „capacity“ von 20 auf 15 verringere?**

A: Die letzten 5 virtuellen Computer (mit den größten Indizes) werden entfernt.

**F: Was passiert, wenn ich dann die Kapazität von 15 auf 18 erhöhe?**

Wenn Sie auf 18 erhöhen, werden virtuelle Computer mit den Indizes 15, 16 und 17 erstellt. In beiden Fällen werden die virtuellen Computer über Fehlerdomänen (FDs) und Updatedomänen (UDs) ausgeglichen.

**F: Kann ich beim Verwenden mehrerer Erweiterungen in einer VM-Skalierungsgruppe eine Ausführungsreihenfolge erzwingen?**

A: Nicht direkt. Bei der customScript-Erweiterung kann das Skript jedoch warten, bis eine andere Erweiterung abgeschlossen ist (z. B. durch Überwachung des Erweiterungsprotokolls – siehe [https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install\_lap.sh](https://github.com/Azure/azure-quickstart-templates/blob/master/201-vmss-lapstack-autoscale/install_lap.sh)).

**F: Funktionieren VM-Skalierungsgruppen mit Azure-Verfügbarkeitsgruppen?**

A: Ja. Eine Skalierungsgruppe ist eine implizite Verfügbarkeitsgruppe mit 3 Fehlerdomänen (FDs) und 5 Updatedomänen (UDs). Unter virtualMachineProfile brauchen Sie nichts zu konfigurieren. In künftigen Versionen werden Skalierungsgruppen wahrscheinlich mehrere Mandanten umspannen, aber derzeit entspricht eine Skalierungsgruppe einer einzigen Verfügbarkeitsgruppe.

## Nächste Schritte

Während der Vorschau für VM-Skalierungsgruppen wird die Dokumentation schrittweise erweitert, und es werden mehr Integrationsfunktionen verfügbar gemacht, z. B. für das Portal und die automatische Skalierung.

Ihr Feedback ist für uns sehr wichtig, denn wir möchten den Dienst so gestalten, dass er die gewünschten Funktionen bietet. Bitte teilen Sie uns darum mit, was gut funktioniert, was nicht so gut funktioniert und was verbessert werden kann. Senden Sie Ihr Feedback an [vmssfeedback@microsoft.com](mailto:vmssfeedback@microsoft.com).

## Ressourcen

| **Thema** | **Link** |
| --- | --- |
| Überblick über den Azure-Ressourcen-Manager | [https://azure.microsoft.com/documentation/articles/resource-group-overview/](https://azure.microsoft.com/documentation/articles/resource-group-overview/) |
| Azure-Ressourcen-Manager-Vorlagen | [https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/](https://azure.microsoft.com/documentation/articles/resource-group-authoring-templates/) |
| Funktionen von Azure-Ressourcen-Manager-Vorlagen | [https://azure.microsoft.com/documentation/articles/resource-group-template-functions/](https://azure.microsoft.com/documentation/articles/resource-group-template-functions/) |
| Beispielvorlagen (GitHub) | [https://github.com/Azure/azure-quickstart-templates](https://github.com/Azure/azure-quickstart-templates) |
| Leitfaden zur REST-API für VM-Skalierungsgruppen | [https://msdn.microsoft.com/library/mt589023.aspx](https://msdn.microsoft.com/library/mt589023.aspx) |
| Videos zu VM-Skalierungsgruppen | [https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) [https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman) [https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player](https://channel9.msdn.com/Blogs/Windows-Azure/VM-Scale-Set-Template-Dissection/player) |
| Einstellungen für automatische Skalierung (MSDN) | [https://msdn.microsoft.com/library/azure/dn931953.aspx](https://msdn.microsoft.com/library/azure/dn931953.aspx) |
| Problembehandlung beim Bereitstellen von Ressourcengruppen in Azure | [https://azure.microsoft.com/documentation/articles/resource-group-deploy-debug/](https://azure.microsoft.com/documentation/articles/resource-group-deploy-debug/) |

<!---HONumber=Nov15_HO3-->