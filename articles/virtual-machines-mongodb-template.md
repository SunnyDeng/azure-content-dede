<properties
  pageTitle="Erstellen eines MongoDB-Clusters auf Ubuntu mithilfe einer Ressourcen-Manager-Vorlage"
  description="Erstellen eines MongoDB-Clusters auf Ubuntu mithilfe einer Ressourcen-Manager-Vorlage mit PowerShell oder der Azure-Befehlszeilenschnittstelle"
  services="virtual-machines"
  documentationCenter=""
  authors="karthmut"
  manager="timlt"
  editor="tysonn"/>

<tags
  ms.service="virtual-machines"
  ms.workload="multiple"
  ms.tgt_pltfrm="vm-windows"
  ms.devlang="na"
  ms.topic="article"
  ms.date="04/29/2015"
  ms.author="karthmut"/>

# Erstellen eines MongoDB-Clusters auf Ubuntu mithilfe einer Ressourcen-Manager-Vorlage

MongoDB ist eine Open-Source-Dokumentendatenbank, die hohe Leistung, hohe Verfügbarkeit und automatische Skalierung bereitstellt. MongoDB kann eigenständig oder innerhalb eines Clusters installiert werden und die integrierten Replikationsfunktionen nutzen. In einigen Fällen können Sie mit Replikation die Lesekapazitäten erhöhen. Clients können Lese- und Schreibvorgänge an verschiedene Server senden. Sie können auch Kopien in verschiedenen Datencentern verwalten, um die Positionierung und die Verfügbarkeit von Daten für verteilte Anwendungen zu verbessern. Mit MongoDB bietet die Replikation auch Redundanz und erhöht die Verfügbarkeit von Daten. Durch mehrere Kopien von Daten auf verschiedenen Datenbankservern schützt die Replikation eine Datenbank bei einem Ausfall eines einzelnen Servers. Replikation ermöglicht auch die Wiederherstellung nach Hardwareausfällen und Dienstunterbrechungen. Mit zusätzlichen Kopien der Daten können Sie eine für die Wiederherstellung im Notfall und eine für die Berichterstellung oder Sicherung reservieren.

Zusätzlich zu den verschiedenen Versionen, die bereits in Azure Marketplace verfügbar waren, können Sie jetzt auch auf einfache Weise auf Ubuntu-VMs mit [Azure PowerShell](powershell-install-configure.md) oder der [Azure-Befehlszeilenschnittstelle](xplat-cli.md) mithilfe einer Ressourcen-Manager-Vorlage einen neuen MongoDB-Cluster bereitstellen.

Neu bereitgestellte Cluster auf Basis dieser Vorlage besitzen die im folgenden Diagramm beschriebene Topologie, auch wenn andere Topologien leicht durch das Anpassen der in diesem Artikel gezeigten Vorlage erreicht werden können:

![cluster-architecture](media/virtual-machines-mongodb-template/cluster-architecture.png)

Sie können über einen Parameter die Anzahl von Knoten definieren, die im neuen MongoDB-Cluster bereitgestellt werden, und mit einem weiteren Parameter kann auch eine VM-Instanz (Jumpbox) mit einer öffentlichen IP-Adresse innerhalb des gleichen VNET bereitgestellt werden. So haben Sie die Möglichkeit, Verbindungen aus dem öffentlichen Internet mit dem Cluster herzustellen und jede Art von Verwaltungsaufgaben für diesen Cluster auszuführen. Eine andere Option, die als Parameter bereitsteht, ist die Möglichkeit zum Hinzufügen eines übergeordneten Knotens für die Replikatgruppe. Dies wird i. d. R. empfohlen, wenn diese eine gerade Anzahl von Elementen enthält. Weitere Informationen zu MongoDB-Replikationstopologien und weitere Einzelheiten finden Sie in der offiziellen [MongoDB-Dokumentation](http://docs.mongodb.org/manual/core/replication-introduction/).

Nachdem die Bereitstellung abgeschlossen ist, können Sie mithilfe der konfigurierten DNS-Adresse über den SSH-Port 22 auf die Jumpbox zugreifen.

Bevor wir zu näheren Einzelheiten zum Azure-Ressourcen-Manager und der für diese Bereitstellung verwendeten Vorlage kommen, stellen Sie sicher, dass Azure PowerShell oder die Azure-Befehlszeilenschnittstelle konfiguriert und einsatzbereit ist.

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../includes/xplat-getting-set-up-arm.md)]

## Erstellen eines MongoDB-Clusters mithilfe einer Ressourcen-Manager-Vorlage

Gehen folgendermaßen Sie vor, um einen MongoDB-Cluster mithilfe einer Ressourcen-Manager-Vorlage aus dem GitHub-Vorlagenrepository zu erstellen. Jeder Schritt umfasst Anweisungen für Azure PowerShell und die Azure-Befehlszeilenschnittstelle.

### Schritt 1-a: Herunterladen der Vorlagendateien mithilfe von PowerShell

Erstellen Sie einen lokalen Ordner für die JSON-Vorlage und andere zugehörige Dateien (z. B. "C:\\Azure\\Vorlagen\\MongoDB").

Fügen Sie dabei den Namen Ihres lokalen Ordners ein, und führen Sie damit die folgenden Befehle aus:

    $folderName="C:\Azure\Templates\MongoDB"
    $webclient = New-Object System.Net.WebClient
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/azuredeploy.json"
    $filePath = $folderName + "\azuredeploy.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/azuredeploy-parameters.json"
    $filePath = $folderName + "\azuredeploy-parameters.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/arbiter-resources.json"
    $filePath = $folderName + "\arbiter-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/empty-resources.json"
    $filePath = $folderName + "\empty-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/jumpbox-resources.json"
    $filePath = $folderName + "\jumpbox-resources.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D1.json"
    $filePath = $folderName + "\member-resources-D1.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D2.json"
    $filePath = $folderName + "\member-resources-D2.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D3.json"
    $filePath = $folderName + "\member-resources-D3.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D4.json"
    $filePath = $folderName + "\member-resources-D4.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D11.json"
    $filePath = $folderName + "\member-resources-D11.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D12.json"
    $filePath = $folderName + "\member-resources-D12.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D13.json"
    $filePath = $folderName + "\member-resources-D13.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/member-resources-D14.json"
    $filePath = $folderName + "\member-resources-D14.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/mongodb-ubuntu-install.sh"
    $filePath = $folderName + "\mongodb-ubuntu-install.sh"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/metadata.json"
    $filePath = $folderName + "\metadata.json"
    $webclient.DownloadFile($url,$filePath)
    $url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/mongodb-high-availability/shared-resources.json"
    $filePath = $folderName + "\shared-resources.json"
    $webclient.DownloadFile($url,$filePath)  

### Schritt 1-b: Herunterladen der Vorlagendateien mithilfe der Azure-Befehlszeilenschnittstelle

Klonen Sie das komplette Vorlagen-Repository mithilfe eines Git-Clients Ihrer Wahl, zum Beispiel:

    git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Sobald dies abgeschlossen ist, suchen Sie im Verzeichnis "C:\\Azure\\Vorlagen" nach dem Ordner **mongodb-high-availability**.

### Schritt 2 (optional): Erlernen der Vorlagenparameter

Wenn Sie schwierige Lösungen wie einen MongoDB-Cluster bereitstellen, müssen Sie eine Reihe von Konfigurationsparametern für den Umgang mit unterschiedlichen erforderlichen Einstellungen angeben. Durch das Deklarieren dieser Parameter in der Vorlagendefinition ist es möglich, Werte während der Bereitstellung über eine externe Datei oder an der Befehlszeile anzugeben.

Im Abschnitt "parameters" am Anfang der Datei **azuredeploy.json** finden Sie den Parametersatz, der von der Vorlage zum Konfigurieren eines MongoDB-Clusters verlangt wird. Dies ist Beispiel für den Abschnitt "parameters" aus der Datei azuredeploy.json dieser Vorlage:

    "parameters": {
      "adminUsername": {
          "type": "string",
          "metadata": {
            "Description": "Administrator user name used when provisioning virtual machines (which also becomes a system user administrator in MongoDB)"
          }
        },
        "adminPassword": {
          "type": "securestring",
          "metadata": {
            "Description": "Administrator password used when provisioning virtual machines (which is also a password for the system administrator in MongoDB)"
          }
        },
        "storageAccountName": {
          "type": "string",
          "defaultValue": "",
          "metadata": {
            "Description": "Unique namespace for the Storage Account where the Virtual Machine's disks will be placed (this name will be used as a prefix to create one or more storage accounts as per t-shirt size)"
          }
        },
        "region": {
          "type": "string",
          "metadata": {
            "Description": "Location where resources will be provisioned"
          }
        },
        "virtualNetworkName": {
          "type": "string",
          "defaultValue": "mongodbVnet",
          "metadata": {
            "Description": "The arbitrary name of the virtual network provisioned for the MongoDB deployment"
          }
        },
        "subnetName": {
          "type": "string",
          "defaultValue": "mongodbSubnet",
          "metadata": {
            "Description": "Subnet name for the virtual network that resources will be provisioned in to"
          }
        },
        "addressPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/16",
          "metadata": {
            "Description": "The network address space for the virtual network"
          }
        },
        "subnetPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.0/24",
          "metadata": {
            "Description": "The network address space for the virtual subnet"
          }
        },
        "nodeAddressPrefix": {
          "type": "string",
          "defaultValue": "10.0.0.1",
          "metadata": {
            "Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
          }
        },
        "jumpbox": {
          "type": "string",
          "defaultValue": "Disabled",
          "allowedValues": [
          "Enabled",
          "Disabled"
          ],
          "metadata": {
            "Description": "The flag allowing to enable or disable provisioning of the jumpbox VM that can be used to access the MongoDB environment"
          }
        },
        "tshirtSize": {
          "type": "string",
          "defaultValue": "XSmall",
          "allowedValues": [
          "XSmall",
          "Small",
          "Medium",
          "Large",
          "XLarge",
          "XXLarge"
          ],
          "metadata": {
            "Description": "T-shirt size of the MongoDB deployment"
          }
        },
        "osFamily": {
          "type": "string",
          "defaultValue": "Ubuntu",
          "allowedValues": [
          "Ubuntu"
          ],
          "metadata": {
            "Description": "The target OS for the virtual machines running MongoDB"
          }
        },
        "mongodbVersion": {
          "type": "string",
          "defaultValue": "3.0.2",
          "metadata": {
            "Description": "The version of the MongoDB packages to be deployed"
          }
        },
        "replicaSetName": {
          "type": "string",
          "defaultValue": "rs0",
          "metadata": {
            "Description": "The name of the MongoDB replica set"
          }
        },
        "replicaSetKey": {
          "type": "string",
          "metadata": {
            "Description": "The shared secret key for the MongoDB replica set"
          }
        }
      },

Jeder Parameter enthält Details wie Datentyp und zulässige Werte. Dies ermöglicht die Überprüfung von Parametern, die während der Vorlagenausführung im interaktiven Modus (z. B. PowerShell oder Azure-CLI) übergeben werden, und ebenso eine Benutzeroberfläche mit automatischer Erkennung, die dynamisch durch das Analysieren der Liste der erforderlichen Parameter und ihrer Beschreibungen erstellt werden könnte.

### Schritt 3-a: Bereitstellen eines MongoDB-Clusters mit einer Vorlage mithilfe von PowerShell

Bereiten Sie eine Parameterdatei für die Bereitstellung vor, indem Sie eine JSON-Datei mit den Laufzeitwerten für alle Parameter erstellen. Diese Datei wird dann als eine Entität an den Bereitstellungsbefehl übergeben. Wenn Sie keine Parameterdatei einfügen, verwendet PowerShell die in der Vorlage angegebenen Standardwerte und fordert Sie auf, die restlichen Werte einzugeben.

Hier ist ein Beispielsatz von Parametern aus der Datei **azuredeploy-parameters.json**:

    {
      "adminUsername": {
          "value": "MongoAdmin"
      },
      "adminPassword": {
          "value": ""
      },
      "storageAccountName": {
          "value": ""
      },
      "region": {
          "value": "West US"
      },
      "virtualNetworkName": {
          "value": "mongodbVnet"
      },
      "subnetName": {
          "value": "mongodbSubnet"
      },
      "addressPrefix": {
          "value": "10.0.0.0/16"
      },
      "subnetPrefix": {
          "value": "10.0.0.0/24"
      },
      "nodeAddressPrefix": {
          "value": "10.0.0.1"
      },
      "jumpbox": {
          "value": "Disabled"
      },
      "tshirtSize": {
          "value": "XSmall"
      },
      "osFamily": {
          "value": "Ubuntu"
      },
      "mongodbVersion": {
          "value": "3.0.2"
      },
      "replicaSetName": {
          "value": "rs0"
      },
      "replicaSetKey":  {
          "value": ""
      }
    }

Geben Sie Namen für die Azure-Bereitstellung, die Ressourcengruppe, den Azure-Speicherort sowie den Ordner mit der gespeicherten JSON-Bereitstellungsdatei an. Führen Sie dann diese Befehle aus:

    $deployName="<deployment name>"
    $RGName="<resource group name>"
    $locName="<Azure location, such as West US>"
    $folderName="<folder name, such as C:\Azure\Templates\MongoDB>"
    $templateFile= $folderName + "\azuredeploy.json"
    $templateParameterFile= $folderName + "\azuredeploy-parameters.json"

    New-AzureResourceGroup –Name $RGName –Location $locName

    New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile

Beim Ausführen des Befehls **New-AzureResourceGroupDeployment** werden damit die Parameterwerte aus der JSON-Parameterdatei extrahiert und die Ausführung der Vorlage entsprechend gestartet. Das Definieren und Verwenden mehrerer Parameterdateien in den verschiedenen Umgebungen (z. B. Test, Produktion usw.) fördert die Wiederverwendung von Vorlagen und vereinfacht komplexe Lösungen mit mehreren Umgebungen.

Denken Sie beim Bereitstellen daran, dass ein neues Azure-Speicherkonto erstellt werden muss, daher muss der Name, den Sie als Speicherkonto-Parameter angeben, eindeutig sein und alle Anforderungen an ein Azure-Speicherkonto erfüllen (nur Kleinbuchstaben und Ziffern).

Während und nach der Bereitstellung können Sie alle Anforderungen überprüfen, die während der Bereitstellung vorgenommen wurden, einschließlich der aufgetretenen Fehler.

Wechseln Sie dazu in das [Azure-Portal](https://portal.azure.com), und gehen Sie folgendermaßen vor:

- Klicken Sie auf der linken Navigationsleiste auf "Durchsuchen", scrollen Sie nach unten, und klicken Sie auf "Ressourcengruppen".
- Nach dem Klicken auf die Ressourcengruppe, die Sie gerade erstellt haben, wird das Blatt "Ressourcengruppe" angezeigt.
- Durch Klicken auf das Balkendiagramm "Ereignisse" im Bereich "Überwachung" des Blattes "Ressourcengruppe" werden die Ereignisse für die Bereitstellung angezeigt:
- Durch Klicken auf die einzelnen Ereignisse können Sie sich die Details jedes einzelnen Vorgangs, der von der Vorlage angestoßen wurde, noch näher ansehen.

Verwenden Sie nach den Tests zum Entfernen dieser Ressourcengruppe und all ihrer Ressourcen (Speicherkonto, virtueller Computer, virtuelles Netzwerk) den folgenden Befehl:

    Remove-AzureResourceGroup –Name "<resource group name>" -Force

### Schritt 3-a: Bereitstellen eines MongoDB-Clusters mit einer Vorlage mithilfe der Azure-Befehlszeilenschnittstelle

Zum Bereitstellen eines MongoDB-Clusters über die Azure-CLI erstellen Sie zuerst eine Ressourcengruppe durch Angabe eines Namens und eines Speicherorts:

    azure group create mdbc "West US"

Übergeben Sie diesen Namen für die Ressourcengruppe, den Speicherort der JSON-Vorlagendatei und den Speicherort der Parameterdatei (siehe Abschnitt oben zur PowerShell) an den folgenden Befehl:

    azure group deployment create mdbc -f .\azuredeploy.json -e .\azuredeploy-parameters.json

Sie können den Status der einzelnen Ressourcenbereitstellungen mit dem folgenden Befehl überprüfen:

    azure group deployment list mdbc

## Überblick über die MongoDB-Vorlagenstruktur und -Dateianordnung

Um eine stabile und wiederverwendbare Ressourcen-Manager-Vorlage zu erstellen, ist es erforderlich, genau über eine Reihe komplexer und zusammenhängender Aufgaben während der Bereitstellung einer komplexen Lösung wie MongoDB nachzudenken. Durch die Nutzung von ARM-Funktionen zum **Verknüpfen von Vorlagen** und **Ressourcenschleifen** und die zusätzliche Skriptausführung über zugehörige Erweiterungen ist es möglich, einen modularen Ansatz zu implementieren, der bei nahezu allen komplexen vorlagenbasierten Bereitstellungen wiederverwendet werden kann.

In diesem Diagramm werden die Beziehungen zwischen allen Dateien beschrieben, die für diese Bereitstellung von GitHub heruntergeladen werden:

![mongodb-files](media/virtual-machines-mongodb-template/mongodb-files.png)

Dieser Abschnitt führt Sie schrittweise durch die Struktur der Datei **azuredeploy.json** für den MongoDB-Cluster.

### Abschnitt "parameters"

Im Abschnitt "parameters" von **azuredeploy.json** werden veränderbare Parameter angegeben, die in dieser Vorlage verwendet werden. Die oben beschriebene Datei **azuredeploy-parameters.json** wird während der Vorlagenausführung zum Übergeben der Werte in den Abschnitt "parameters" von "azuredeploy.json" verwendet.

### Abschnitt "variables"

Im Abschnitt "variables" sind Variablen angegeben, die in dieser Vorlage verwendet werden können. Er enthält eine Reihe von Feldern (JSON-Datentypen oder -Fragmente), die zum Zeitpunkt der Ausführung auf Konstanten oder berechnete Werte festgelegt werden. Dies ist der Abschnitt "variables" für diese MongoDB-Vorlage:

    "variables": {
          "_comment0": "/* T-shirt sizes may vary for different reasons, and some customers may want to modify these - so feel free to go ahead and define your favorite t-shirts */",
          "tshirtSizeXSmall": {
              "vmSizeMember": "Standard_D1",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 1,
              "totalMemberCount": 2,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D1.json')]",
              "storageAccountCount": 1,
              "dataDiskSize": 100
          },
          "tshirtSizeSmall": {
              "vmSizeMember": "Standard_D1",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 2,
              "totalMemberCount": 3,
              "arbiter": "Disabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D1.json')]",
              "storageAccountCount": 1,
              "dataDiskSize": 100
          },
          "tshirtSizeMedium": {
              "vmSizeMember": "Standard_D2",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 3,
              "totalMemberCount": 4,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
              "storageAccountCount": 2,
              "dataDiskSize": 250
          },
          "tshirtSizeLarge": {
              "vmSizeMember": "Standard_D2",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 7,
              "totalMemberCount": 8,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
              "storageAccountCount": 4,
              "dataDiskSize": 250
          },
          "tshirtSizeXLarge": {
              "vmSizeMember": "Standard_D3",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 7,
              "totalMemberCount": 8,
              "arbiter": "Enabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D3.json')]",
              "storageAccountCount": 4,
              "dataDiskSize": 500
          },
          "tshirtSizeXXLarge": {
              "vmSizeMember": "Standard_D3",
              "vmSizeArbiter": "Standard_A1",
              "numberOfMembers": 15,
              "totalMemberCount": 16,
              "arbiter": "Disabled",
              "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D3.json')]",
              "storageAccountCount": 8,
              "dataDiskSize": 500
          },
          "osFamilyUbuntu": {
              "osName": "ubuntu",
              "installerBaseUrl": "http://repo.mongodb.org/apt/ubuntu",
              "installerPackages": "mongodb-org",
              "imagePublisher": "Canonical",
              "imageOffer": "UbuntuServer",
              "imageSKU": "14.04.2-LTS"
          },
          "vmStorageAccountContainerName": "vhd-mongodb",
          "vmStorageAccountDomain": ".blob.core.windows.net",
          "vnetID": "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "sharedScriptUrl": "[concat('https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/shared_scripts/', variables('osFamilySpec').osName, '/')]",
          "scriptUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-high-availability/",
          "templateBaseUrl": "[variables('scriptUrl')]",
          "jumpboxTemplateEnabled": "jumpbox-resources.json",
          "jumpboxTemplateDisabled": "empty-resources.json",
          "arbiterTemplateEnabled": "arbiter-resources.json",
          "arbiterTemplateDisabled": "empty-resources.json",
          "sharedTemplateUrl": "[concat(variables('templateBaseUrl'), 'shared-resources.json')]",
          "jumpboxTemplateUrl": "[concat(variables('templateBaseUrl'), variables(concat('jumpboxTemplate', parameters('jumpbox'))))]",
          "arbiterTemplateUrl": "[concat(variables('templateBaseUrl'), variables(concat('arbiterTemplate', variables('clusterSpec').arbiter)))]",
          "commonSettings": {
              "availabilitySetName": "mongodbAvailSet",
              "region": "[parameters('region')]"
          },
          "storageSettings": {
              "vhdStorageAccountName": "[parameters('storageAccountName')]",
              "vhdContainerName": "[variables('vmStorageAccountContainerName')]",
              "destinationVhdsContainer": "[concat('https://', parameters('storageAccountName'), variables('vmStorageAccountDomain'), '/', variables('vmStorageAccountContainerName'), '/')]",
              "storageAccountCount": "[variables('clusterSpec').storageAccountCount]"
          },
          "networkSettings": {
              "virtualNetworkName": "[parameters('virtualNetworkName')]",
              "addressPrefix": "[parameters('addressPrefix')]",
              "subnetName": "[parameters('subnetName')]",
              "subnetPrefix": "[parameters('subnetPrefix')]",
              "subnetRef": "[concat(variables('vnetID'), '/subnets/', parameters('subnetName'))]",
              "machineIpPrefix": "[parameters('nodeAddressPrefix')]"
          },
          "machineSettings": {
              "adminUsername": "[parameters('adminUsername')]",
              "adminPassword": "[parameters('adminPassword')]",
              "machineNamePrefix": "mongodb-",
              "osImageReference": {
                  "publisher": "[variables('osFamilySpec').imagePublisher]",
                  "offer": "[variables('osFamilySpec').imageOffer]",
                  "sku": "[variables('osFamilySpec').imageSKU]",
                  "version": "latest"
              }
          },
          "clusterSpec": "[variables(concat('tshirtSize', parameters('tshirtSize')))]",
          "osFamilySpec": "[variables(concat('osFamily', parameters('osFamily')))]",
          "installCommand": "[concat('bash mongodb-', variables('osFamilySpec').osName, '-install.sh', ' -i ', variables('osFamilySpec').installerBaseUrl, ' -b ', variables('osFamilySpec').installerPackages, ' -r ', parameters('replicaSetName'), ' -k ', parameters('replicaSetKey'), ' -u ', parameters('adminUsername'), ' -p ', parameters('adminPassword'), ' -x ', variables('networkSettings').machineIpPrefix, ' -n ', variables('clusterSpec').totalMemberCount)]",
          "vmScripts": {
              "scriptsToDownload": [
                  "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
                  "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
              ],
              "regularNodeInstallCommand": "[variables('installCommand')]",
              "lastNodeInstallCommand": "[concat(variables('installCommand'), ' -l')]",
              "arbiterNodeInstallCommand": "[concat(variables('installCommand'), ' -a')]"
          },
          "_comment1": "/* The list of values below helps partition VM disks across multiple storage accounts to achieve a better throughput */",
          "_comment2": "/* Feel free to modify the default allocations if you are comfortable and understand what you are doing */",
          "storageAccountForXSmall_0": "0",
          "storageAccountForXSmall_1": "0",
          "storageAccountForSmall_0": "0",
          "storageAccountForSmall_1": "0",
          "storageAccountForSmall_2": "0",
          "storageAccountForMedium_0": "0",
          "storageAccountForMedium_1": "0",
          "storageAccountForMedium_2": "0",
          "storageAccountForMedium_3": "0",
          "storageAccountForLarge_0": "0",
          "storageAccountForLarge_1": "1",
          "storageAccountForLarge_2": "2",
          "storageAccountForLarge_3": "3",
          "storageAccountForLarge_4": "0",
          "storageAccountForLarge_5": "1",
          "storageAccountForLarge_6": "2",
          "storageAccountForLarge_7": "3",
          "storageAccountForXLarge_0": "0",
          "storageAccountForXLarge_1": "1",
          "storageAccountForXLarge_2": "2",
          "storageAccountForXLarge_3": "3",
          "storageAccountForXLarge_4": "0",
          "storageAccountForXLarge_5": "1",
          "storageAccountForXLarge_6": "2",
          "storageAccountForXLarge_7": "3",
          "storageAccountForXXLarge_0": "0",
          "storageAccountForXXLarge_1": "1",
          "storageAccountForXXLarge_2": "2",
          "storageAccountForXXLarge_3": "3",
          "storageAccountForXXLarge_4": "4",
          "storageAccountForXXLarge_5": "5",
          "storageAccountForXXLarge_6": "6",
          "storageAccountForXXLarge_7": "7",
          "storageAccountForXXLarge_8": "0",
          "storageAccountForXXLarge_9": "1",
          "storageAccountForXXLarge_10": "2",
          "storageAccountForXXLarge_11": "3",
          "storageAccountForXXLarge_12": "4",
          "storageAccountForXXLarge_13": "5",
          "storageAccountForXXLarge_14": "6",
          "storageAccountForXXLarge_15": "7"
      },

Wenn Sie sich dieses Beispiel im Detail ansehen, erkennen Sie zwei verschiedene Ansätze. Im ersten Fragment ist die "osFamilyUbuntu"-Variable auf ein JSON-Element mit sechs Schlüssel-Wert-Paaren festgelegt:

    "osFamilyUbuntu": {
      "osName": "ubuntu",
      "installerBaseUrl": "http://repo.mongodb.org/apt/ubuntu",
      "installerPackages": "mongodb-org",
      "imagePublisher": "Canonical",
      "imageOffer": "UbuntuServer",
      "imageSKU": "14.04.2-LTS"
    },

Im zweiten Fragment ist die "vmScripts"-Variable einem JSON-Array zugeordnet, in dem einzelne Elemente zur Laufzeit mithilfe einer Vorlagensprachenfunktion ("concat") und dem Wert einer anderen Variablen plus Zeichenfolgenkonstanten berechnet werden:

    "vmScripts": {
      "scriptsToDownload": [
      "[concat(variables('scriptUrl'), 'mongodb-', variables('osFamilySpec').osName, '-install.sh')]",
      "[concat(variables('sharedScriptUrl'), 'vm-disk-utils-0.1.sh')]"
    ],

Ein wichtiges Konzept in dieser Vorlage ist die Art, wie verschiedene "T-Shirt-Größen" für MongoDB-Cluster definiert sind. Beim Betrachten einer dieser "tshirtSizeXXXX"-Variablen können Sie erkennen, dass sie wichtige Merkmale für die Bereitstellung eines Clusters beschreibt. Sehen wir uns als Beispiel die mittlere Größe an:

    "tshirtSizeMedium": {
      "vmSizeMember": "Standard_D2",
      "vmSizeArbiter": "Standard_A1",
      "numberOfMembers": 3,
      "totalMemberCount": 4,
      "arbiter": "Enabled",
      "vmTemplate": "[concat(variables('templateBaseUrl'), 'member-resources-D2.json')]",
      "storageAccountCount": 2,
      "dataDiskSize": 250
    },

Ein "Medium"-MongoDB-Cluster verwendet D2 als VM-Größe für die drei MongoDB-Knoten, auf denen Daten gehostet werden, sowie eine vierte A1-VM als Arbiter für die Replikation. Die entsprechende untergeordnete Vorlage, die zum Bereitstellen von Datenknoten aufgerufen wird, ist **member-resources-D2.json**. Die Datendateien (250 GB) werden in zwei Speicherkonten gespeichert. Diese Variablen werden im Abschnitt "resources" zum Orchestrieren der Knotenbereitstellungen und für andere Aufgaben verwendet.

### Abschnitt "resources"

Im Abschnitt **"resources"** geschieht am meisten. Schauen Sie sich diesen Abschnitt sorgfältig an. Sie können sofort zwei verschiedene Fälle identifizieren: Der erste ist ein definiertes Element des Typs `Microsoft.Resources/deployments`, was im Grunde den Aufruf einer geschachtelten Bereitstellung innerhalb der Hauptbereitstellung bedeutet. Durch das Element "templateLink" (und die zugehörige Versionseigenschaft) kann eine verknüpfte Vorlagendatei bestimmt werden, die zur Übergabe eines Parametersatzes als Eingabe aufgerufen wird, wie Sie in diesem Fragment sehen:

    {
      "name": "shared-resources",
      "type": "Microsoft.Resources/deployments",
      "apiVersion": "2015-01-01",
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('sharedTemplateUrl')]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "commonSettings": {
            "value": "[variables('commonSettings')]"
          },
          "storageSettings": {
            "value": "[variables('storageSettings')]"
          },
          "networkSettings": {
            "value": "[variables('networkSettings')]"
          }
        }
      }
    },

Im ersten Beispiel wird deutlich, wie die Datei **azuredeploy.json** in diesem Szenario als Mechanismus zur Orchestrierung organisiert wurde, indem eine Anzahl anderer Vorlagendateien aufgerufen wird, wobei jede jeweils für einen Teil der erforderlichen Bereitstellungsaufgaben verantwortlich ist.

Insbesondere folgende verknüpfte Vorlagen werden für diese Bereitstellung eingesetzt:

-	**shared-resource.json**: enthält die Definition aller Ressourcen, die in der Bereitstellung gemeinsam genutzt werden. Beispiele hierfür sind Speicherkonten, die zum Speichern der Datenträger auf dem Betriebssystem und den virtuellen Netzwerken des virtuellen Computers verwendet werden.
-	**jumpbox-resources.json**: ist bei Aktivierung für die Bereitstellung aller Ressourcen im Zusammenhang mit der Jumpbox-VM verantwortlich, der mit einer öffentlichen IP-Adresse für den Zugriff auf den MongoDB-Cluster aus einem öffentlichen Netzwerk.
-	**arbiter-resources.json**: Beim Aktivieren stellt diese Vorlage einen Arbiter-Member im MongoDB-Cluster bereit. Ein Arbiter enthält keine Daten, er wird aber verwendet, wenn eine Replikatgruppe eine gerade Anzahl von Knoten enthält, um die primäre Auswahl zu verwalten.
-	**member-resources-Dx.json**: Diese Ressourcenvorlagen stellen die eigentlichen MongoDB-Knoten bereit. Es wird basierend auf der definierten T-Shirt-Größe eine bestimmte Datei ausgewählt, wobei sich die einzelnen Dateien nur durch die Anzahl der angefügten Datenträger für jeden Knoten unterscheiden.
-	**mongodb-ubuntu-install.sh**: eine Bash-Skriptdatei, die von der CustomScriptForLinux-Erweiterung auf jedem Knoten im Cluster aufgerufen wird. Sie ist verantwortlich für das Bereitstellen und Formatieren von Datenträgern und das Installieren von MongoDB-Bits auf dem Knoten.

Für das Bereitstellen eines MongoDB-Clusters ist eine bestimmte Logik erforderlich, um eine Replikatgruppe ordnungsgemäß einrichten zu können. Die genaue Abfolge während der Bereitstellung lautet wie folgt:

BEREITSTELLEN DER DATENMEMBER (parallel) = > BEREITSTELLEN DES LETZTEN DATENMEMBERS = > (optional) BEREITSTELLEN EINES ARBITERS

In dieser Abfolge erfolgt die Bereitstellung mehrerer Datenknoten parallel – mit Ausnahme des letzten Knotens. Hier werden der Cluster gebildet und eine neue Replikatgruppe bereitgestellt, deshalb müssen alle vorherigen Knoten zuvor in Betrieb genommen werden. Im letzten Schritt wird der optionale Arbiter-Knoten (nur für T-Shirt-Größen, bei denen dies erforderlich ist) bereitgestellt.

In der Hauptvorlage (azuredeploy.json) können Sie sehen, wie diese Logik implementiert wird, beginnend mit allen Datenmembern:

    {
      "type": "Microsoft.Resources/deployments",
      "name": "[concat('member-resources', copyindex())]",
      "apiVersion": "2015-01-01",
      "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
      ],
      "copy": {
        "name": "memberNodesLoop",
        "count": "[variables('clusterSpec').numberOfMembers]"
      },
      "properties": {
        "mode": "Incremental",
        "templateLink": {
          "uri": "[variables('clusterSpec').vmTemplate]",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "commonSettings": {
            "value": "[variables('commonSettings')]"
          },
          "storageSettings": {
            "value": {
              "vhdStorageAccountName": "[concat(variables('storageSettings').vhdStorageAccountName, variables(concat('storageAccountFor', parameters('tshirtSize'), '_', copyindex())))]",
                              "vhdContainerName": "[variables('storageSettings').vhdContainerName]",
                              "destinationVhdsContainer": "[concat('https://', variables('storageSettings').vhdStorageAccountName, variables(concat('storageAccountFor', parameters('tshirtSize'), '_', copyindex())), variables('vmStorageAccountDomain'), '/', variables('storageSettings').vhdContainerName, '/')]"
            }
          },
          "networkSettings": {
            "value": "[variables('networkSettings')]"
          },
          "machineSettings": {
            "value": {
              "adminUsername": "[variables('machineSettings').adminUsername]",
                              "adminPassword": "[variables('machineSettings').adminPassword]",
                              "machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
                              "osImageReference": "[variables('machineSettings').osImageReference]",
                              "vmSize": "[variables('clusterSpec').vmSizeMember]",
                              "dataDiskSize": "[variables('clusterSpec').dataDiskSize]",
                              "machineIndex": "[copyindex()]",
                              "vmScripts": "[variables('vmScripts').scriptsToDownload]",
                              "commandToExecute": "[variables('vmScripts').regularNodeInstallCommand]"
            }
          }
        }
      }
    },

Ein wichtiges hervorzuhebendes Konzept ist die Möglichkeit, mehrere Kopien eines einzelnen Ressourcentyps bereitzustellen und für jede Instanz eindeutige Werte für die erforderlichen Einstellungen festzulegen. Dieses Konzept ist als **Ressourcenschleife** bekannt.

Im oben gezeigten Fragment wird ein Parameter (Anzahl der im Cluster bereitgestellten Knoten) verwendet, um eine Variable ("numberOfMembers") festzulegen, die dann an das **copy**-Element übergeben wird, um eine Anzahl (Schleife) von untergeordneten Bereitstellungen auszulösen. Jede dieser Bereitstellungen führt zur Instanziierung der Vorlage für jeden Member im Cluster. Damit Sie alle Einstellungen vornehmen können, in denen es erforderlich ist, eindeutige Werte zwischen verschiedenen Instanzen anzugeben, kann die **copyindex()**-Funktion verwendet werden, um einen numerischen Wert zu erhalten, der den aktuellen Index in der jeweiligen Ressourcenschleifenerstellung angibt.

Ein weiteres wichtiges Konzept bei der Ressourcenerstellung ist die Möglichkeit, Abhängigkeiten und Vorränge zwischen Ressourcen anzugeben, wie Sie am JSON-Array **dependsOn** sehen können. In dieser speziellen Vorlage hängt die Bereitstellung der einzelnen Knoten von der vorherigen erfolgreichen Bereitstellung der **gemeinsam genutzten Ressourcen** ab.

Angeschlossene Datenträger werden als Teil der Aktivitäten zur Vorbereitung der Knoten formatiert, die durch das Ausführen der Skriptdatei **mongodb-ubuntu-install.sh** ausgelöst werden. In dieser Datei finden Sie tatsächlich eine Instanz dieses Aufrufs:

    bash ./vm-disk-utils-0.1.sh -b $DATA_DISKS -s

**vm-disk-utils-0.1.sh** ist im Ordner **shared_scripts\\ubuntu** im GitHub-Repository "azure-quickstart-templates" enthalten und bietet nützliche Funktionen für die Bereitstellung von Datenträgern, die Formatierung und das Striping, die jedes Mal wiederverwendet werden können, wenn Sie ähnliche Aufgaben im Rahmen der Vorlagenerstellung ausführen müssen.

Ein weiteres interessantes Fragment ist das im Zusammenhang mit den VM-Erweiterungen CustomScriptForLinux. Diese werden als eigener Ressourcentyp mit einer Abhängigkeit in jeder Bereitstellungsvorlage für Clusterknoten installiert. Sie finden dieses Fragment am Ende jeder **member-resources-Dx.json**-Datei:

    {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "[concat('vmMember', parameters('machineSettings').machineIndex, '/installmongodb')]",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('commonSettings').region]",
      "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', 'vmMember', parameters('machineSettings').machineIndex)]"
      ],
      "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
          "fileUris": "[parameters('machineSettings').vmScripts]", "commandToExecute":"[parameters('machineSettings').commandToExecute]"
        }
      }
    }

Indem Sie sich mit den anderen Dateien in dieser Bereitstellung vertraut machen, werden Sie alle Details und bewährten Methoden zum Organisieren und Orchestrieren komplexer Bereitstellungsstrategien für Lösungen mit mehreren Knoten verstehen, basierend auf sämtlichen Technologien und unter Nutzung von Azure-Ressourcen-Manager-Vorlagen. Obwohl nicht zwingend erforderlich, ist das Strukturieren der Vorlagendateien eine empfohlene Vorgehensweise, wie im folgenden Diagramm verdeutlicht:

![mongodb-template-structure](media/virtual-machines-mongodb-template/mongodb-template-structure.png)

Im Wesentlichen empfiehlt dieser Ansatz:

-	Definieren Sie die Kernvorlagendatei als zentrale Orchestrierungsstelle für alle spezifischen Bereitstellungsaktivitäten, indem Sie Vorlagen nutzen, die auf Aufrufe von untergeordneten Vorlagenausführungen verweisen.
-	Erstellen Sie eine spezifische Vorlagendatei, die alle Ressourcen bereitstellt, die von allen anderen speziellen Bereitstellungsaufgaben gemeinsam genutzt werden (z. B. Speicherkonten, VNET-Konfiguration usw.). Diese kann häufig zwischen Bereitstellungen wiederverwendet werden, die ähnliche Anforderungen im Hinblick auf die allgemeine Infrastruktur haben.
-	Schließen Sie optionale Ressourcenvorlagen ein, um ressourcenspezifische Anforderungen zu erkennen.
-	Erstellen Sie für identische Mitglieder einer Ressourcengruppe (Knoten in einem Cluster usw.) spezielle Vorlagen, die die Ressourcenschleife nutzen, um für mehrere Instanzen eindeutige Eigenschaften bereitzustellen.
-	Für alle Nachbereitstellungsaufgaben (z. B. Produktinstallation, Konfigurationen usw.) nutzen Sie die Bereitstellung von Skripterweiterungen, und erstellen Sie Skripts, die für die einzelnen Technologien spezifisch sind.

Weitere Informationen finden Sie unter [Vorlagensprache des Azure-Ressourcen-Managers](https://msdn.microsoft.com/library/azure/dn835138.aspx).

<!--HONumber=54-->