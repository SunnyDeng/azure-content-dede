<properties
	pageTitle="Ressourcen-Manager-Vorlage für Spark unter Ubuntu"
	description="Erfahren Sie, wie Sie auf einfache Weise auf Ubuntu-VMs mit Azure PowerShell oder der Azure-CLI und einer Ressourcen-Manager-Vorlage einen neuen Spark-Cluster bereitstellen"
	services="virtual-machines"
	documentationCenter=""
	authors="paolosalvatori"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows"
	ms.workload="multiple"
	ms.date="05/16/2015"
	ms.author="paolosalvatori"/>

# Spark auf Ubuntu mit einer Ressourcen-Manager-Vorlage

Apache Spark ist eine schnelles Modul zur Verarbeitung von umfangreichen Daten. Spark verfügt über ein erweitertes DAG-Ausführungsmodul, das zyklischen Datenfluss und In-Memory-Computing unterstützt sowie auf verschiedene Datenquellen, einschließlich HDFS, Spark, HBase und S3 zugreifen kann.

Zusätzlich zur Ausführung auf den Cluster-Managern Mesos oder YARN bietet Spark einen einfachen eigenständigen Bereitstellungsmodus. Dieses Lernprogramm führt Sie durch die Verwendung einer Beispielvorlage für Azure-Ressourcen-Manager zum Bereitstellen eines Spark-Clusters auf Ubuntu-VMs über [Azure PowerShell](../powershell-install-configure.md) oder die [Azure-Befehlszeilenschnittstelle](../xplat-cli.md).

Diese Vorlage stellt einen Spark-Cluster auf Ubuntu-VMs bereit. Diese Vorlage stellt außerdem ein Speicherkonto, ein virtuelles Netzwerk, Verfügbarkeitsgruppen, öffentliche IP-Adressen und die für die Installation erforderlichen Netzwerkkarten bereit. Es gibt keinen öffentlichen IP-Zugriff auf den Spark-Cluster, da dieser hinter einem Subnetz erstellt wird. Als Teil der Bereitstellung kann eine optionale "Jumpbox" bereitgestellt werden. Diese "Jumpbox" ist ein virtueller Ubuntu-Computer, der in einem Subnetz bereitgestellt wird, aber eine öffentliche IP-Adresse mit einem offenen SSH-Port *verfügbar* macht, sodass Sie darauf zugreifen können. Sie können dann von der "Jumpbox" per SSH auf alle virtuellen Spark-Computer im Subnetz zugreifen.

Diese Vorlage verwendet ein Konzept von "T-Shirt-Größen", um ein "kleines", "mittleres" oder "großes" Spark-Cluster-Setup anzugeben. Wenn die Vorlagensprache eine dynamischere Anpassung der Vorlagengrößen unterstützt, könnte sie zum Angeben der Anzahl der Masterknoten im Spark-Cluster, der Slaveknoten, der Größe der virtuellen Computer usw. geändert werden. Im Moment können Sie die VM-Größe und die Anzahl der Master und Slaves in der Datei "azuredeploy.json" in den Variablen **tshirtSizeS**, **tshirtSizeM** und **tshirtSizeL** erkennen:

- S: 1 Master, 1 Slave
- M: 1 Master, 4 Slaves
- L: 1 Master, 6 Slaves

Neu bereitgestellte Cluster auf Basis dieser Vorlage besitzen die im folgenden Diagramm beschriebene Topologie, auch wenn andere Topologien leicht durch das Anpassen der in diesem Artikel gezeigten Vorlage erreicht werden können:

![cluster-architecture](media/virtual-machines-spark-template/cluster-architecture.png)

Wie in der Abbildung oben gezeigt, besteht die Bereitstellungstopologie aus den folgenden Elementen:

-	Ein neues Speicherkonto, das den BS-Datenträger neu erstellter virtueller Computer hostet
-	Ein virtuelles Netzwerk mit einem Subnetz Alle von der Vorlage erstellten virtuellen Computer werden in diesem virtuellen Netzwerk bereitgestellt
-	Eine Verfügbarkeitsgruppe für Master-/Slaveknoten
-	Ein Masterknoten in der neu erstellten Verfügbarkeitsgruppe
-	Vier Slaveknoten, die im gleichen virtuellen Subnetz und der gleichen Verfügbarkeitsgruppe ausgeführt werden wie der Masterknoten
-	Eine "Jumpbox"-VM, die sich im gleichen virtuellen Netzwerk und Subnetz befindet und für den Zugriff auf den Cluster verwendet werden kann

Version 3.0.0 von Spark ist die Standardversion. Sie können jede vorgefertigte Binärdatei aus dem Spark-Repository verwenden. Es ist im Skript vorgesehen, die Auskommentierung der Builderstellung aus der Quelle aufzuheben. Jedem Spark-Masterknoten wird eine statische IP-Adresse zugewiesen: 10.0.0.10. Jedem Spark-Slaveknoten wird eine statische IP-Adresse zugewiesen, um die aktuellen Beschränkung zu umgehen, durch die es nicht möglich ist, dynamisch aus der Vorlage heraus eine Liste von IP-Adressen zu erstellen. (Standardmäßig wird dem ersten Knoten die private IP-Adresse 10.0.0.30 zugewiesen, dem zweiten Knoten die IP-Adresse 10.0.0.31 usw.) Zur Überprüfung auf Bereitstellungsfehler wechseln Sie zum neuen Azure-Portal und navigieren zu **Ressourcengruppe** > **Last deployment** > **Check Operation Details**.

Bevor wir zu näheren Einzelheiten zum Azure-Ressourcen-Manager und der für diese Bereitstellung verwendeten Vorlage kommen, stellen Sie sicher, dass Azure PowerShell oder die Azure-Befehlszeilenschnittstelle konfiguriert und einsatzbereit ist.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Erstellen eines Spark-Clusters unter Verwendung einer Ressourcen-Manager-Vorlage

Gehen Sie folgendermaßen vor, um mit Azure PowerShell oder über die Azure-Befehlszeilenschnittstelle einen Spark-Cluster mithilfe einer Ressourcen-Manager-Vorlage aus dem GitHub-Vorlagenrepository zu erstellen.

### Schritt 1-a: Herunterladen der Vorlagendateien mithilfe von Azure PowerShell

Erstellen Sie einen lokalen Ordner für die JSON-Vorlage und andere zugehörige Dateien (z. B. "C:\\Azure\\Vorlagen\\Spark").

Fügen Sie dabei den Namen Ihres lokalen Ordners ein, und führen Sie damit die folgenden Befehle aus:

```powershell
# Define variables
$folderName="C:\Azure\Templates\Spark"
$baseAddress = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/spark-on-ubuntu/"
$webclient = New-Object System.Net.WebClient
$files = $("azuredeploy.json", `
         "azuredeploy-parameters.json", `
         "jumpbox-resources-disabled.json", `
         "jumpbox-resources-enabled.json",
         "metadata.json", `
         "shared-resources.json", `
         "spark-cluster-install.sh", `
         "jumpbox-resources-enabled.json")

# Download files
foreach ($file in $files)
{
    $url = $baseAddress + $file
    $filePath = $folderName + $file
    $webclient.DownloadFile($baseAddress + $file, $folderName + $file)
    Write-Host $url "downloaded to" $filePath
}
```

### Schritt 1-b: Herunterladen der Vorlagendateien mithilfe der Azure-Befehlszeilenschnittstelle

Klonen Sie das komplette Vorlagenrepository mithilfe eines Git-Clients Ihrer Wahl, zum Beispiel:

	git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates

Suchen Sie nach Abschluss des Klonvorgangs unter "C:\\Azure\\Vorlagen" den Ordner **spark-on-ubuntu**.

### Schritt 2 (optional): Erlernen der Vorlagenparameter

Wenn Sie einen Spark-Cluster mit einer Vorlage bereitstellen, müssen Sie eine Reihe von Konfigurationsparametern für den Umgang mit unterschiedlichen erforderlichen Einstellungen angeben. Durch Deklarieren dieser Parameter in der Vorlagendefinition ist es möglich, Werte während der Bereitstellungsausführung über eine externe Datei oder an einer Befehlszeile anzugeben.

Im Abschnitt "parameters" am Anfang der Datei "azuredeploy.json" finden Sie den Parametersatz, der von der Vorlage zum Konfigurieren eines Spark-Clusters verlangt wird. Nachfolgend sehen Sie ein Beispiel für den Abschnitt "parameters" aus der Datei "azuredeploy.json" dieser Vorlage:

```json
"parameters": {
	"adminUsername": {
		"type": "string",
		"metadata": {
			"Description": "Administrator user name used when provisioning virtual machines"
		}
	},
	"adminPassword": {
		"type": "securestring",
		"metadata": {
			"Description": "Administrator password used when provisioning virtual machines"
		}
	},
	"imagePublisher": {
		"type": "string",
		"defaultValue": "Canonical",
		"metadata": {
			"Description": "Image publisher"
		}
	},
	"imageOffer": {
		"type": "string",
		"defaultValue": "UbuntuServer",
		"metadata": {
			"Description": "Image offer"
		}
	},
	"imageSKU": {
		"type": "string",
		"defaultValue": "14.04.2-LTS",
		"metadata": {
			"Description": "Image SKU"
		}
	},
	"storageAccountName": {
		"type": "string",
		"defaultValue": "uniquesparkstoragev12",
		"metadata": {
			"Description": "Unique namespace for the Storage account where the virtual machine's disks will be placed"
		}
	},
	"region": {
		"type": "string",
		"defaultValue": "West US",
		"metadata": {
			"Description": "Location where resources will be provisioned"
		}
	},
	"virtualNetworkName": {
		"type": "string",
		"defaultValue": "myVNETspark",
		"metadata": {
			"Description": "The arbitrary name of the virtual network provisioned for the cluster"
		}
	},
	"dataDiskSize": {
		"type": "int",
		"defaultValue": 100,
		"metadata": {
			"Description": "Size of the data disk attached to Spark nodes (in GB)"
		}
	},
	"addressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/16",
		"metadata": {
			"Description": "The network address space for the virtual network"
		}
	},
	"subnetName": {
		"type": "string",
		"defaultValue": "Subnet-1",
		"metadata": {
			"Description": "Subnet name for the virtual network that resources will be provisioned into"
		}
	},
	"subnetPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.0/24",
		"metadata": {
			"Description": "Address space for the virtual network subnet"
		}
	},
	"sparkVersion": {
		"type": "string",
		"defaultValue": "stable",
		"metadata": {
			"Description": "The version of the Spark package to be deployed on the cluster (or use 'stable' to pull in the latest and greatest)"
		}
	},
	"sparkClusterName": {
		"type": "string",
		"metadata": {
			"Description": "The arbitrary name of the Spark cluster (maps to cluster's configuration file name)"
		}
	},
	"sparkNodeIPAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.1",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"sparkSlaveNodeIPAddressPrefix": {
		"type": "string",
		"defaultValue": "10.0.0.3",
		"metadata": {
			"Description": "The IP address prefix that will be used for constructing a static private IP address for each node in the cluster"
		}
	},
	"jumpbox": {
		"type": "string",
		"defaultValue": "enabled",
		"allowedValues": [
		"enabled",
		"disabled"
		],
		"metadata": {
			"Description": "The flag allowing to enable or disable provisioning of the jump-box VM that can be used to access the Spark nodes"
		}
	},
	"tshirtSize": {
		"type": "string",
		"defaultValue": "S",
		"allowedValues": [
		"S",
		"M",
		"L"
		],
		"metadata": {
			"Description": "T-shirt size of the Spark cluster"
		}
	}
}
```

Jeder Parameter enthält Details wie Datentyp und zulässige Werte. Dies ermöglicht die Überprüfung von Parametern, die während der Vorlagenausführung im interaktiven Modus (z. B. Azure PowerShell oder Azure-Befehlszeilenschnittstelle) übergeben werden, und ebenso eine Benutzeroberfläche mit automatischer Erkennung, die dynamisch durch das Analysieren der Liste der erforderlichen Parameter und ihrer Beschreibungen erstellt werden kann.

### Schritt 3-a: Bereitstellen eines Spark-Clusters mit einer Vorlage mithilfe von Azure PowerShell

Bereiten Sie eine Parameterdatei für die Bereitstellung vor, indem Sie eine JSON-Datei mit den Laufzeitwerten für alle Parameter erstellen. Diese Datei wird dann als eine Entität an den Bereitstellungsbefehl übergeben. Wenn Sie keine Parameterdatei einfügen, verwendet Azure PowerShell die in der Vorlage angegebenen Standardwerte und fordert Sie auf, die restlichen Werte einzugeben.

Nachfolgend sehen Sie einen Beispielsatz von Parametern aus der Datei "azuredeploy-parameters.json". Beachten Sie, dass Sie gültige Werte für die Parameter **storageAccountName**, **adminUsername** und **adminPassword** bereitstellen und ggf. die anderen Parameter anpassen müssen:

```json
{
  "storageAccountName": {
    "value": "paolosspark"
  },
  "adminUsername": {
    "value": "paolos"
  },
  "adminPassword": {
    "value": "Passw0rd!"
  },
  "region": {
    "value": "West US"
  },
  "virtualNetworkName": {
    "value": "sparkClustVnet"
  },
  "subnetName": {
    "value": "Subnet1"
  },
  "addressPrefix": {
    "value": "10.0.0.0/16"
  },
  "subnetPrefix": {
    "value": "10.0.0.0/24"
  },
  "sparkVersion": {
    "value": "3.0.0"
  },
  "sparkClusterName": {
    "value": "spark-arm-cluster"
  },
  "sparkNodeIPAddressPrefix": {
		"value": "10.0.0.1"
  },
  "sparkSlaveNodeIPAddressPrefix": {
    "value": "10.0.0.3"
  },
  "jumpbox": {
    "value": "enabled"
  },
  "tshirtSize": {
    "value": "M"
  }
}
```
Geben Sie Namen für die Azure-Bereitstellung, die Ressourcengruppe, den Azure-Speicherort sowie den Ordner mit der gespeicherten JSON-Bereitstellungsdatei an. Führen Sie dann diese Befehle aus:

```powershell
$deployName="<deployment name>"
$RGName="<resource group name>"
$locName="<Azure location, such as West US>"
$folderName="<folder name, such as C:\Azure\Templates\Spark>"
$templateFile= $folderName + "\azuredeploy.json"
$templateParameterFile= $folderName + "\azuredeploy-parameters.json"
New-AzureResourceGroup -Name $RGName -Location $locName
New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParamterFile $templateParameterFile -TemplateFile $templateFile
```
> [AZURE.NOTE]**$RGName** muss innerhalb Ihres Abonnements eindeutig sein.

Beim Ausführen des Befehls **New-AzureResourceGroupDeployment** werden damit die Parameterwerte aus der JSON-Parameterdatei extrahiert und die Ausführung der Vorlage entsprechend gestartet. Das Definieren und Verwenden mehrerer Parameterdateien in den verschiedenen Umgebungen (Test, Produktion usw.) fördert die Wiederverwendung von Vorlagen und vereinfacht komplexe Lösungen mit mehreren Umgebungen.

Denken Sie beim Bereitstellen daran, dass ein neues Azure-Speicherkonto erstellt werden muss, daher muss der Name, den Sie als Speicherkontoparameter angeben, eindeutig sein und alle Anforderungen an ein Azure-Speicherkonto erfüllen (nur Kleinbuchstaben und Ziffern).

Während der Bereitstellung wird etwa Folgendes angezeigt:

```powershell
PS C:\> New-AzureResourceGroup -Name $RGName -Location $locName

ResourceGroupName : SparkResourceGroup
Location          : westus
ProvisioningState : Succeeded
Tags              :
Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

ResourceId        : /subscriptions/2018abc3-dbd9-4437-81a8-bb3cf56138ed/resourceGroups/sparkresourcegroup

PS C:\> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
VERBOSE: 10:08:28 AM - Template is valid.
VERBOSE: 10:08:28 AM - Create template deployment 'SparkTestDeployment'.
VERBOSE: 10:08:37 AM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is running
VERBOSE: 10:09:11 AM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is succeeded
VERBOSE: 10:09:13 AM - Resource Microsoft.Network/networkInterfaces 'nicsl0' provisioning status is succeeded
VERBOSE: 10:09:16 AM - Resource Microsoft.Network/networkInterfaces 'nic0' provisioning status is succeeded
VERBOSE: 10:09:16 AM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is running
VERBOSE: 10:09:24 AM - Resource Microsoft.Compute/virtualMachines 'mastervm0' provisioning status is running
VERBOSE: 10:11:04 AM - Resource Microsoft.Compute/virtualMachines/extensions 'mastervm0/installsparkmaster'
provisioning status is running
VERBOSE: 10:11:04 AM - Resource Microsoft.Compute/virtualMachines 'mastervm0' provisioning status is succeeded
VERBOSE: 10:11:11 AM - Resource Microsoft.Compute/virtualMachines 'slavevm0' provisioning status is running
VERBOSE: 10:11:42 AM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is succeeded
VERBOSE: 10:13:10 AM - Resource Microsoft.Compute/virtualMachines 'slavevm0' provisioning status is succeeded
VERBOSE: 10:13:15 AM - Resource Microsoft.Compute/virtualMachines/extensions 'slavevm0/installsparkslave' provisioning
status is running
VERBOSE: 10:16:58 AM - Resource Microsoft.Compute/virtualMachines/extensions 'mastervm0/installsparkmaster'
provisioning status is succeeded
VERBOSE: 10:19:05 AM - Resource Microsoft.Compute/virtualMachines/extensions 'slavevm0/installsparkslave' provisioning
status is succeeded


DeploymentName    : SparkTestDeployment
ResourceGroupName : SparkResourceGroup
ProvisioningState : Succeeded
Timestamp         : 5/5/2015 5:19:05 PM
Mode              : Incremental
TemplateLink      :
Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     Paolos
                    adminPassword    SecureString
                    imagePublisher   String                     Canonical
                    imageOffer       String                     UbuntuServer
                    imageSKU         String                     14.04.2-LTS
                    storageAccountName  String                  paolosspark
                    region           String                     West US
                    virtualNetworkName  String                  sparkClustVnet
                    addressPrefix    String                     10.0.0.0/16
										subnetName       String                     Subnet1
                    subnetPrefix     String                     10.0.0.0/24
                    sparkVersion     String                     3.0.0
                    sparkClusterName  String                    spark-arm-cluster
                    sparkNodeIPAddressPrefix  String            10.0.0.1
                    sparkSlaveNodeIPAddressPrefix  String       10.0.0.3
                    jumpbox          String                     enabled
                    tshirtSize       String                     M
```

Während und nach der Bereitstellung können Sie alle Anforderungen überprüfen, die während der Bereitstellung vorgenommen wurden, einschließlich der aufgetretenen Fehler.

Wechseln Sie dazu in das [Azure-Portal](https://portal.azure.com), und gehen Sie folgendermaßen vor:

- Klicken Sie auf der linken Navigationsleiste auf **Durchsuchen**, scrollen Sie nach unten, und klicken Sie auf **Ressourcengruppen**.
- Klicken Sie auf die gerade erstellte Ressourcengruppe, um das Blatt "Ressourcengruppe" anzuzeigen.
- Durch Klicken auf das Balkendiagramm **Ereignisse** im Bereich **Überwachung** des Blattes "Ressourcengruppe" werden die Ereignisse für die Bereitstellung angezeigt.
- Durch Klicken auf die einzelnen Ereignisse können Sie sich die Details jedes einzelnen Vorgangs, der von der Vorlage angestoßen wurde, noch näher ansehen.

![portal-events](media/virtual-machines-spark-template/portal-events.png)

Verwenden Sie nach den Tests zum Entfernen dieser Ressourcengruppe und all ihrer Ressourcen (Speicherkonto, virtueller Computer, virtuelles Netzwerk) den folgenden Befehl:

```powershell
Remove-AzureResourceGroup -Name "<resource group name>" -Force
```

### Schritt 3-a: Bereitstellen eines Spark-Clusters mit einer Vorlage mithilfe der Azure-Befehlszeilenschnittstelle

Zum Bereitstellen eines Spark-Clusters über die Azure-CLI erstellen Sie zuerst eine Ressourcengruppe durch Angabe eines Namens und eines Speicherorts:

	azure group create SparkResourceGroup "West US"

Übergeben Sie diesen Namen für die Ressourcengruppe, den Speicherort der JSON-Vorlagendatei und den Speicherort der Parameterdatei (siehe Abschnitt oben zu Azure PowerShell) an den folgenden Befehl:

	azure group deployment create SparkResourceGroup -f .\azuredeploy.json -e .\azuredeploy-parameters.json

Sie können den Status der einzelnen Ressourcenbereitstellungen mit dem folgenden Befehl überprüfen:

	azure group deployment list SparkResourceGroup

## Übersicht über die Spark-Vorlagenstruktur und -Dateianordnung

Um eine stabile und wiederverwendbare Ressourcen-Manager-Vorlage zu erstellen, ist es erforderlich, genau über eine Reihe komplexer und zusammenhängender Aufgaben während der Bereitstellung einer komplexen Lösung wie Spark nachzudenken. Durch die Nutzung von Resource Manager zum Verknüpfen von Vorlagen und Ressourcenschleifen und die zusätzliche Skriptausführung über zugehörige Erweiterungen ist es möglich, einen modularen Ansatz zu implementieren, der bei nahezu allen komplexen vorlagenbasierten Bereitstellungen wiederverwendet werden kann.

In diesem Diagramm werden die Beziehungen zwischen allen Dateien beschrieben, die für diese Bereitstellung von GitHub heruntergeladen werden:

![spark-files](media/virtual-machines-spark-template/spark-files.png)

Dieser Abschnitt führt Sie schrittweise durch die Struktur der Datei "azuredeploy.json" für den Spark-Cluster.

Wenn Sie nicht bereits eine Kopie der Vorlagendatei heruntergeladen haben, legen Sie einen lokalen Ordner als Speicherort für die Datei fest, und erstellen Sie ihn (z. B. "C:\\Azure\\Vorlagen\\Spark"). Geben Sie den Ordnernamen an, und führen Sie die folgenden Befehle aus:

```powershell
$folderName="<folder name, such as C:\Azure\Templates\Spark>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)
```

Öffnen Sie die Vorlage "azuredeploy.json" in einem Texteditor oder Tool Ihrer Wahl. Im Folgenden werden die Struktur der Vorlagendatei und der Zweck der einzelnen Abschnitte beschrieben. Sie können den Inhalt dieser Vorlage [hier](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/azuredeploy.json) auch in Ihrem Browser anzeigen.

### Abschnitt "parameters"

Im Abschnitt "parameters" von "azuredeploy.json" werden veränderbare Parameter angegeben, die in dieser Vorlage verwendet werden. Die oben genannte Datei "azuredeploy-parameters.json" wird während der Vorlagenausführung zum Übergeben der Werte in den Abschnitt "parameters" von "azuredeploy.json" verwendet.

Im Folgenden finden Sie ein Beispiel für einen Parameter für die "T-Shirt-Größe":

```json
"tshirtSize": {
    "type": "string",
    "defaultValue": "S",
    "allowedValues": [
        "S",
        "M",
        "L"
    ],
    "metadata": {
        "Description": "T-shirt size of the Spark deployment"
    }
},
```

> [AZURE.NOTE]Beachten Sie, dass ein **DefaultValue** und **AllowedValues** angegeben werden können.

### Abschnitt "Variablen"

Im Abschnitt "variables" sind Variablen angegeben, die in dieser Vorlage verwendet werden können. Er enthält eine Reihe von Feldern (JSON-Datentypen oder -Fragmente), die zum Zeitpunkt der Ausführung auf Konstanten oder berechnete Werte festgelegt werden. Hier finden Sie einige Beispiele, die von einfachen bis zu komplexeren reichen:

```json
"variables": {
	"vmStorageAccountContainerName": "vhd",
	"vnetID": "[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
	"subnetRef": "[concat(variables('vnetID'),'/subnets/',parameters('subnetName'))]",
	"computerNamePrefix": "sparknode",
	"scriptUrl": "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/spark-on-ubuntu/",
	"templateUrl": "[variables('scriptUrl')]",
	"sharedTemplateName": "shared-resources",
	"jumpboxTemplateName": "jumpbox-resources-",
	"tshirtSizeS": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 1,
		"vmSize": "Standard_A2"
	},
	"tshirtSizeM": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 4,
		"vmSize": "Standard_A4"
	},
	"tshirtSizeL": {
		"numberOfMasterInstances": 1,
		"numberOfSlavesInstances" : 6,
		"vmSize": "Standard_A7"
	},
	"numberOfMasterInstances": "[variables(concat('tshirtSize', parameters('tshirtSize'))).numberOfMasterInstances]",
	"numberOfSlavesInstances": "[variables(concat('tshirtSize', parameters('tshirtSize'))).numberOfSlavesInstances]",
	"vmSize": "[variables(concat('tshirtSize', parameters('tshirtSize'))).vmSize]"
},
```

Die Variable **vmStorageAccountContainerName** ist ein Beispiel für eine einfache Name-/Wert-Variable. **vnetID** ist ein Beispiel für eine Variable, die zur Laufzeit mit den Funktionen **resourceId** und **parameters** berechnet wird. Die Werte der **numberOfMasterInstances**- und **vmSize**-Variablen werden zur Laufzeit mithilfe der Funktionen **concat**, **variables** und **parameters** berechnet.

Wenn Sie die Größe der Spark-Clusterbereitstellung anpassen möchten, können Sie die Eigenschaften der Variablen **tshirtSizeS**, **tshirtSizeM** und **tshirtSizeL** in der Vorlage "azuredeploy.json" ändern.

Weitere Informationen zur Vorlagensprache finden Sie im MSDN unter [Vorlagensprache des Azure-Ressourcen-Managers](https://msdn.microsoft.com/library/azure/dn835138.aspx).


### Abschnitt "Ressourcen"

Im Abschnitt "resources" geschieht am meisten. Wenn Sie diesen Abschnitt genau überprüfen, erkennen Sie sofort zwei unterschiedliche Fälle. Der erste ist ein vom Typ `Microsoft.Resources/deployments` definiertes Element, das im Wesentlichen eine geschachtelte Bereitstellung innerhalb der Hauptbereitstellung aufruft. Der zweite ist die **templateLink**-Eigenschaft (und die zugehörige **contentVersion**-Eigenschaft), die es möglich macht, eine verknüpfte Vorlagendatei anzugeben, die unter Übergabe eines Satzes von Parametern als Eingabe aufgerufen wird. Diese sind in diesem Vorlagenfragment zu sehen:

```json
"resources": [
{
	"name": "shared-resources",
	"type": "Microsoft.Resources/deployments",
	"apiVersion": "2015-01-01",
	"properties": {
		"mode": "Incremental",
		"templateLink": {
			"uri": "[concat(variables('templateUrl'), variables('sharedTemplateName'), '.json')]",
			"contentVersion": "1.0.0.0"
		},
		"parameters": {
			"region": {
				"value": "[parameters('region')]"
			},
			"storageAccountName": {
				"value": "[parameters('storageAccountName')]"
			},
			"virtualNetworkName": {
				"value": "[parameters('virtualNetworkName')]"
			},
			"addressPrefix": {
				"value": "[parameters('addressPrefix')]"
			},
			"subnetName": {
				"value": "[parameters('subnetName')]"
			},
			"subnetPrefix": {
				"value": "[parameters('subnetPrefix')]"
			}
		}
	}
},
```

Im ersten Beispiel wird deutlich, wie die Datei "azuredeploy.json" in diesem Szenario als Mechanismus zur Orchestrierung organisiert wurde, indem eine Anzahl anderer Vorlagendateien aufgerufen wird. Jede Datei ist jeweils für einen Teil der erforderlichen Bereitstellungsaufgaben verantwortlich.

Insbesondere folgende verknüpfte Vorlagen werden für diese Bereitstellung eingesetzt:

-	**shared-resource.json**: enthält die Definition aller Ressourcen, die in der Bereitstellung gemeinsam genutzt werden. Beispiele hierfür sind Speicherkonten, die zum Speichern der Betriebssystem-Datenträger und virtuellen Netzwerke eines virtuellen Computers verwendet werden.
-	**jumpbox-resources-enabled.json**: stellt die "Jumpbox"-VM und alle zugehörigen Ressourcen wie Netzwerkschnittstelle, öffentliche IP-Adresse und den Eingabeendpunkt für SSH in der Umgebung bereit.

Nach dem Aufrufen dieser beiden Vorlagen stellt "azuredeploy.json" alle Spark-Clusterknoten-VMs und verbundenen Ressourcen bereit (Netzwerkadapter, private IP-Adressen usw.). Diese Vorlage stellt auch VM-Erweiterungen bereit (benutzerdefinierte Skripts für Linux) und ruft ein Bash-Skript ("spark-cluster-install.sh") auf, um Spark auf jedem Knoten physisch zu installieren und einzurichten.

Sehen wir uns nun die *Verwendung* der letzten Vorlage, "azuredeploy.json", genauer an, da diese aus Sicht der Vorlagenentwicklung am interessantesten ist. Ein wichtiges hervorzuhebendes Konzept ist die Möglichkeit, mehrere Kopien eines einzelnen Ressourcentyps mit einer einzigen Vorlagendatei bereitzustellen und für jede Instanz eindeutige Werte für die erforderlichen Einstellungen festzulegen. Dieses Konzept ist als **Ressourcenschleife** bekannt.

Eine Ressource, die das **copy**-Element verwendet, "kopiert" sich selbst in der im **count**-Parameter des **copy**-Elements angegebenen Anzahl. Für alle Einstellungen, in denen es erforderlich ist, eindeutige Werte zwischen verschiedenen Instanzen der bereitgestellten Ressource anzugeben, kann die **copyindex()**-Funktion verwendet werden, um einen numerischen Wert zu erhalten, der den aktuellen Index in der bestimmten Ressourcenschleifenerstellung angibt. Das folgende Fragment aus "azuredeploy.json" zeigt, wie dieses Konzept auf die Erstellung mehrerer Netzwerkadapter, VMs und VM-Erweiterungen für den Spark-Cluster angewendet wird:

```json
{
	"apiVersion": "2015-05-01-preview",
	"type": "Microsoft.Network/networkInterfaces",
	"name": "[concat('nic', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "nicLoop",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
	],
	"properties": {
		"ipConfigurations": [
		{
			"name": "ipconfig1",
			"properties": {
				"privateIPAllocationMethod": "Static",
				"privateIPAddress": "[concat(parameters('sparkNodeIPAddressPrefix'), copyindex())]",
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
	"type": "Microsoft.Network/networkInterfaces",
	"name": "[concat('nicsl', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "nicslLoop",
		"count": "[variables('numberOfSlavesInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
	],
	"properties": {
		"ipConfigurations": [
		{
			"name": "ipconfig1",
			"properties": {
				"privateIPAllocationMethod": "Static",
				"privateIPAddress": "[concat(parameters('sparkSlaveNodeIPAddressPrefix'), copyindex())]",
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
	"name": "[concat('mastervm', copyindex())]",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineLoopMaster",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
	"[concat('Microsoft.Resources/deployments/', 'shared-resources')]",
	"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
	],
	"properties": {
		"availabilitySet": {
			"id": "[resourceId('Microsoft.Compute/availabilitySets', 'sparkCluserAS')]"
		},
		"hardwareProfile": {
			"vmSize": "[variables('vmSize')]"
		},
		"osProfile": {
			"computername": "[concat(variables('computerNamePrefix'), copyIndex())]",
			"adminUsername": "[parameters('adminUsername')]",
			"adminPassword": "[parameters('adminPassword')]",
			"linuxConfiguration": {
				"disablePasswordAuthentication": "false"
			}
		},
		"storageProfile": {
			"imageReference": {
				"publisher": "[parameters('imagePublisher')]",
				"offer": "[parameters('imageOffer')]",
				"sku" : "[parameters('imageSKU')]",
				"version":"latest"
			},
			"osDisk" : {
				"name": "osdisk",
				"vhd": {
					"uri": "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/','osdisk', copyindex() ,'.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
				{
					"id": "[resourceId('Microsoft.Network/networkInterfaces',concat('nic', copyindex()))]"
				}
				]
			}
		}
	},
	{
		"apiVersion": "2015-05-01-preview",
		"type": "Microsoft.Compute/virtualMachines",
		"name": "[concat('slavevm', copyindex())]",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineLoop",
			"count": "[variables('numberOfSlavesInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Resources/deployments/', 'shared-resources')]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]",
		"virtualMachineLoopMaster"
		],
		"properties": {
			"availabilitySet": {
				"id": "[resourceId('Microsoft.Compute/availabilitySets', 'sparkCluserAS')]"
			},
			"hardwareProfile": {
				"vmSize": "[variables('vmSize')]"
			},
			"osProfile": {
				"computername": "[concat(variables('computerNamePrefix'),'sl', copyIndex())]",
				"adminUsername": "[parameters('adminUsername')]",
				"adminPassword": "[parameters('adminPassword')]",
				"linuxConfiguration": {
					"disablePasswordAuthentication": "false"
				}
			},
			"storageProfile": {
				"imageReference": {
					"publisher": "[parameters('imagePublisher')]",
					"offer": "[parameters('imageOffer')]",
					"sku" : "[parameters('imageSKU')]",
					"version":"latest"
				},
				"osDisk" : {
					"name": "osdisksl",
					"vhd": {
						"uri": "[concat('https://',parameters('storageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/','osdisksl', copyindex() ,'.vhd')]"
					},
					"caching": "ReadWrite",
					"createOption": "FromImage"
				}
			},
			"networkProfile": {
				"networkInterfaces": [
				{
					"id": "[resourceId('Microsoft.Network/networkInterfaces',concat('nicsl', copyindex()))]"
				}
				]
			}
		}
	},
	{
		"type": "Microsoft.Compute/virtualMachines/extensions",
		"name": "[concat('mastervm', copyindex(), '/installsparkmaster')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineExtensionsLoopMaster",
			"count": "[variables('numberOfMasterInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'mastervm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
		],
		"properties": {
			"publisher": "Microsoft.OSTCExtensions",
			"type": "CustomScriptForLinux",
			"typeHandlerVersion": "1.2",
			"settings": {
				"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
				],
				"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -d ', reference('nic0').ipConfigurations[0].properties.privateIPAddress,' -s ',variables('numberOfSlavesInstances'),' -m ', ' 1 ')]"
			}
		}
	},
	{
		"type": "Microsoft.Compute/virtualMachines/extensions",
		"name": "[concat('slavevm', copyindex(), '/installsparkslave')]",
		"apiVersion": "2015-05-01-preview",
		"location": "[parameters('region')]",
		"copy": {
			"name": "virtualMachineExtensionsLoopSlave",
			"count": "[variables('numberOfSlavesInstances')]"
		},
		"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'slavevm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]"
		],
		"properties": {
			"publisher": "Microsoft.OSTCExtensions",
			"type": "CustomScriptForLinux",
			"typeHandlerVersion": "1.2",
			"settings": {
				"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
				],
				"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -m ', ' 0 ')]"
			}
		}
	}
```

Ein weiteres wichtiges Konzept bei der Ressourcenerstellung ist die Möglichkeit, Abhängigkeiten und Vorränge zwischen Ressourcen anzugeben, wie Sie am JSON-Array **dependsOn** sehen können. In dieser speziellen Vorlage sehen Sie, dass die Spark-Clusterknoten von den zuerst erstellten gemeinsame Ressourcen und **networkInterfaces**-Ressourcen abhängig sind.

Ein weiteres interessantes Fragment ist das im Zusammenhang mit den VM-Erweiterungen **CustomScriptForLinux**. Diese werden als gesonderter Ressourcentyp mit einer Abhängigkeit auf jedem Clusterknoten installiert. In diesem Fall dient dies zum Installieren und Einrichten von Spark auf jedem VM-Knoten. Sehen wir uns einen Ausschnitt aus der Vorlage "azuredeploy.json" an, die diese verwendet:

```json
{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat('mastervm', copyindex(), '/installsparkmaster')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineExtensionsLoopMaster",
		"count": "[variables('numberOfMasterInstances')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'mastervm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nic', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
			],
			"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -d ', reference('nic0').ipConfigurations[0].properties.privateIPAddress,' -s ',variables('numberOfSlavesInstances'),' -m ', ' 1 ')]"
		}
	}
},
{
	"type": "Microsoft.Compute/virtualMachines/extensions",
	"name": "[concat('slavevm', copyindex(), '/installsparkslave')]",
	"apiVersion": "2015-05-01-preview",
	"location": "[parameters('region')]",
	"copy": {
		"name": "virtualMachineExtensionsLoopSlave",
		"count": "[variables('numberOfSlavesInstances')]"
	},
	"dependsOn": [
		"[concat('Microsoft.Compute/virtualMachines/', 'slavevm', copyindex())]",
		"[concat('Microsoft.Network/networkInterfaces/', 'nicsl', copyindex())]"
	],
	"properties": {
		"publisher": "Microsoft.OSTCExtensions",
		"type": "CustomScriptForLinux",
		"typeHandlerVersion": "1.2",
		"settings": {
			"fileUris": [
				"[concat(variables('scriptUrl'), 'spark-cluster-install.sh')]"
			],
			"commandToExecute": "[concat('bash spark-cluster-install.sh -k ',parameters('sparkVersion'),' -m ', ' 0 ')]"
		}
	}
}
```

Beachten Sie, dass die Erweiterung für die Master- und Slaveknotenressourcen verschiedene Befehle ausführt, die in der Eigenschaft **commandToExecute** im Rahmen des Bereitstellungsprozesses definiert wurden.

Wenn Sie den JSON-Ausschnitt der aktuellen VM-Erweiterung betrachten, sehen Sie sich, dass diese Ressource von der VM-Ressource und deren Netzwerkschnittstelle abhängt. Das bedeutet, dass diese beiden Ressourcen bereits vor dem Bereitstellen und Ausführen der VM-Erweiterung bereitgestellt werden müssen. Beachten Sie auch die Verwendung der Funktion **copyindex()** zum Wiederholen dieses Schritts für jede Slave-VM.

Indem Sie sich mit den anderen Dateien in dieser Bereitstellung vertraut machen, werden Sie alle Details und bewährten Methoden zum Organisieren und Orchestrieren komplexer Bereitstellungsstrategien für Lösungen mit mehreren Knoten verstehen, basierend auf sämtlichen Technologien und unter Nutzung von Azure-Ressourcen-Manager-Vorlagen. Obwohl nicht zwingend erforderlich, ist das Strukturieren der Vorlagendateien eine empfohlene Vorgehensweise, wie im folgenden Diagramm verdeutlicht:

![spark-template-structure](media/virtual-machines-spark-template/spark-template-structure.png)

Im Wesentlichen empfiehlt dieser Ansatz:

-	Definieren Sie die Kernvorlagendatei als zentrale Orchestrierungsstelle für alle spezifischen Bereitstellungsaktivitäten, indem Sie Vorlagen nutzen, die auf Aufrufe von untergeordneten Vorlagenausführungen verweisen.
-	Erstellen Sie eine spezifische Vorlagendatei, die alle Ressourcen bereitstellt, die gemeinsam von allen anderen speziellen Bereitstellungsaufgaben (Speicherkonten, Konfiguration von virtuellen Netzwerken usw.) gemeinsam genutzt werden. Diese kann häufig zwischen Bereitstellungen wiederverwendet werden, die ähnliche Anforderungen im Hinblick auf die allgemeine Infrastruktur haben.
-	Schließen Sie optionale Ressourcenvorlagen ein, um ressourcenspezifische Anforderungen zu erkennen.
-	Erstellen Sie für identische Mitglieder einer Ressourcengruppe (Knoten in einem Cluster usw.) spezielle Vorlagen, die die Ressourcenschleife nutzen, um für mehrere Instanzen eindeutige Eigenschaften bereitzustellen.
-	Für alle Nachbereitstellungsaufgaben (Produktinstallation, Konfigurationen usw.) nutzen Sie die Bereitstellung von Skripterweiterungen, und erstellen Sie Skripts, die für die einzelnen Technologien spezifisch sind.

Weitere Informationen finden Sie unter [Vorlagensprache des Azure-Ressourcen-Managers](https://msdn.microsoft.com/library/azure/dn835138.aspx).

## Nächste Schritte

Weitere Details finden Sie unter [Bereitstellen einer Vorlage](../resource-group-template-deploy.md).

Entdecken Sie weitere [Anwendungsframeworks](virtual-machines-app-frameworks.md).

[Problembehandlung bei Vorlagenbereitstellungen](resource-group-deploy-debug.md).

<!---HONumber=August15_HO6-->