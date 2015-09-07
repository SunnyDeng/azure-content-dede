<properties
	pageTitle="Redis-Cluster – Ressourcen-Manager-Vorlage"
	description="Erfahren Sie, wie Sie auf einfache Weise auf Ubuntu-VMs mit Azure PowerShell oder der Azure-CLI und einer Ressourcen-Manager-Vorlage einen neuen Redis-Cluster bereitstellen"
	services="virtual-machines"
	documentationCenter=""
	authors="timwieman"
	manager="timlt"
	editor="tysonn"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2015"
	ms.author="twieman"/>

# Bereitstellen von Redis-Clustern mithilfe einer Ressourcen-Manager-Vorlage

Redis ist ein Open-Source-Schlüsselwertcache und -speicher, bei dem Schlüssel Datenstrukturen wie Zeichenfolgen, Hashes, Listen, Sätze und sortierte Sätze enthalten können. Redis unterstützt einen Satz automatischer Vorgänge für diese Datentypen. Mit der Veröffentlichung von Version 3.0 von Redis ist Redis Cluster jetzt in der neuesten stabilen Version von Redis verfügbar. Redis Cluster ist eine verteilte Implementierung von Redis, in der Daten automatisch auf mehreren Knoten von Redis freigegeben werden, sodass Vorgänge fortgesetzt werden können, wenn einige Knoten ausfallen.

Microsoft Azure Redis Cache ist ein dedizierter Redis Cache Service, der von Microsoft verwaltet wird. Es möchten aber nicht alle Microsoft Azure-Kunden Azure Redis Cache verwenden. Einige möchten Redis Cache hinter einem Subnetz innerhalb ihrer eigenen Azure-Bereitstellungen verwenden, während andere auch eigene Redis-Server auf virtuellen Linux-Computern hosten möchten, um alle Redis-Funktionen vollständig nutzen zu können.

In diesem Lernprogramm durchlaufen Sie die Verwendung einer Beispielvorlage des Azure-Ressourcen-Managers zum Bereitstellen eines Redis-Clusters auf Ubuntu-VMs innerhalb eines Subnetzes in einer Ressourcengruppe in Microsoft Azure. Zusätzlich zu Redis-3.0-Clustern unterstützt diese Vorlage auch das Bereitstellen von Redis 2.8 mit Redis Sentinel. Beachten Sie, dass in diesem Lernprogramm die Implementierung von Redis-3.0-Clustern behandelt wird.

Es gibt also keinen öffentlichen IP-Zugriff auf den Redis-Cluster, da dieser hinter einem Subnetz erstellt wird. Als Teil der Bereitstellung kann eine optionale "Jumpbox" bereitgestellt werden. Diese "Jumpbox" ist ein virtueller Ubuntu-Computer, der in einem Subnetz bereitgestellt wird, aber eine öffentliche IP-Adresse mit einer offenen SSH-Port *verfügbar* macht, sodass Sie per SSH darauf zugreifen können. Sie können dann von der "Jumpbox" per SSH auf alle virtuellen Redis-Computer im Subnetz zugreifen.

Diese Vorlage verwendet ein Konzept von "T-Shirt-Größen", um ein "kleines", "mittleres" oder "großes" Redis-Cluster-Setup anzugeben. Wenn die Vorlagensprache des Azure-Ressourcen-Managers eine dynamischere Anpassung der Vorlagengrößen unterstützt, könnte sie zum Angeben der Anzahl der Masterknoten im Redis-Cluster, der untergeordneten Knoten, der Größe der virtuellen Computer usw. geändert werden. Im Moment können Sie die VM-Größe und die Anzahl der Master und Slaves in der Datei "azuredeploy.json" in den Variablen `tshirtSizeSmall`, `tshirtSizeMedium` und `tshirtSizeLarge` erkennen.

Die Redis-Cluster-Vorlage für die T-Shirt-Größe "Medium" erstellt diese Konfiguration:

![cluster-architecture](media/virtual-machines-redis-template/cluster-architecture.png)

Bevor wir zu näheren Einzelheiten zum Azure-Ressourcen-Manager und der für diese Bereitstellung verwendeten Vorlage kommen, stellen Sie sicher, dass Azure PowerShell oder die Azure-Befehlszeilenschnittstelle konfiguriert und einsatzbereit ist.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up-arm](../../includes/xplat-getting-set-up-arm.md)]

## Bereitstellen von Redis-Clustern mithilfe einer Ressourcen-Manager-Vorlage

Gehen Sie folgendermaßen vor, um einen Redis-Cluster mithilfe einer Ressourcen-Manager-Vorlage aus dem GitHub-Vorlagenrepository zu erstellen. Jeder Schritt umfasst Anweisungen für Azure PowerShell und die Azure-Befehlszeilenschnittstelle.

### Schritt 1-a: Herunterladen der Vorlagendateien mithilfe von Azure PowerShell

Erstellen Sie einen lokalen Ordner für die JSON-Vorlage und andere zugehörige Dateien (z. B. "C:\\Azure\\Vorlagen\\RedisCluster").

Fügen Sie dabei den Namen Ihres lokalen Ordners ein, und führen Sie damit die folgenden Befehle aus:

```powershell
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy-parameters.json"
$filePath = $folderName + "\azuredeploy-parameters.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/empty-resources.json"
$filePath = $folderName + "\empty-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/jumpbox-resources.json"
$filePath = $folderName + "\jumpbox-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/node-resources.json"
$filePath = $folderName + "\node-resources.json"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-cluster-install.sh"
$filePath = $folderName + "\redis-cluster-install.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-cluster-setup.sh"
$filePath = $folderName + "\redis-cluster-setup.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/redis-sentinel-startup.sh"
$filePath = $folderName + "\redis-sentinel-startup.sh"
$webclient.DownloadFile($url,$filePath)

$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/shared-resources.json"
$filePath = $folderName + "\shared-resources.json"
$webclient.DownloadFile($url,$filePath)
```

### Schritt 1-b: Herunterladen der Vorlagendateien mithilfe der Azure-Befehlszeilenschnittstelle

Klonen Sie das komplette Vorlagenrepository mithilfe eines Git-Clients Ihrer Wahl, zum Beispiel:

```
git clone https://github.com/Azure/azure-quickstart-templates C:\Azure\Templates
```

Sobald der Klonvorgang abgeschlossen ist, suchen Sie im Verzeichnis "C:\\Azure\\Vorlagen" nach dem Ordner **redis-high-availability**.

### Schritt 2 (optional): Erlernen der Vorlagenparameter

Wenn Sie mit einer Vorlage einen Redis-Cluster erstellen, müssen Sie mehrere Konfigurationsparameter angeben. Um die Parameter anzuzeigen, die Sie für die Vorlage in einer lokalen JSON-Datei angeben müssen, bevor Sie den Befehl zum Erstellen des Redis-Clusters ausführen, öffnen Sie die JSON-Datei in einem Tool oder Texteditor Ihrer Wahl.

Suchen Sie oben in der Datei nach dem Abschnitt "parameters". Dort sind die Parameter aufgeführt, die erforderlich sind, damit der Redis-Cluster von der Vorlage konfiguriert werden kann. Dies ist der Abschnitt "parameters" der Vorlage "azuredeploy.json":

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
	"storageAccountName": {
		"type": "string",
		"defaultValue": "",
		"metadata": {
			"Description": "Unique namespace for the Storage account where the virtual machine's disks will be placed"
		}
	},
	"location": {
		"type": "string",
		"metadata": {
			"Description": "Location where resources will be provisioned"
		}
	},
	"virtualNetworkName": {
		"type": "string",
		"defaultValue": "redisVirtNet",
		"metadata": {
			"Description": "The arbitrary name of the virtual network provisioned for the Redis cluster"
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
		"defaultValue": "redisSubnet1",
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
			"Description": "The flag allowing to enable or disable provisioning of the jump-box VM that can be used to access the Redis nodes"
		}
	},
	"tshirtSize": {
		"type": "string",
		"defaultValue": "Small",
		"allowedValues": [
			"Small",
			"Medium",
			"Large"
		],
		"metadata": {
			"Description": "T-shirt size of the Redis deployment"
		}
	},
	"redisVersion": {
		"type": "string",
		"defaultValue": "stable",
		"metadata": {
			"Description": "The version of the Redis package to be deployed on the cluster (or use 'stable' to pull in the latest and greatest)"
		}
	},
	"redisClusterName": {
		"type": "string",
		"defaultValue": "redis-cluster",
		"metadata": {
			"Description": "The arbitrary name of the Redis cluster"
		}
	}
},
```

Jeder Parameter enthält Details wie Datentyp und zulässige Werte. Dies ermöglicht die Überprüfung von Parametern, die während der Vorlagenausführung im interaktiven Modus (z. B. Azure PowerShell oder Azure-Befehlszeilenschnittstelle) übergeben werden, und ebenso eine Benutzeroberfläche mit automatischer Erkennung, die dynamisch durch das Analysieren der Liste der erforderlichen Parameter und ihrer Beschreibungen erstellt werden kann.

### Schritt 3-a: Bereitstellen eines Redis-Clusters mit einer Vorlage mithilfe von Azure PowerShell

Bereiten Sie eine Parameterdatei für die Bereitstellung vor, indem Sie eine JSON-Datei mit den Laufzeitwerten für alle Parameter erstellen. Diese Datei wird dann als eine Entität an den Bereitstellungsbefehl übergeben. Wenn Sie keine Parameterdatei einfügen, verwendet Azure PowerShell die in der Vorlage angegebenen Standardwerte und fordert Sie auf, die restlichen Werte einzugeben.

Hier ist ein Beispiel, das Sie in der Datei "azuredeploy-parameters.json" finden können: Beachten Sie, dass Sie gültige Werte für die Parameter `storageAccountName`, `adminUsername` und `adminPassword` bereitstellen und ggf. die anderen Parameter anpassen müssen:

```json
{
	"storageAccountName": {
		"value": "redisdeploy1"
	},
	"adminUsername": {
		"value": ""
	},
	"adminPassword": {
		"value": ""
	},
	"location": {
		"value": "West US"
	},
	"virtualNetworkName": {
		"value": "redisClustVnet"
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
	"nodeAddressPrefix": {
		"value": "10.0.0.1"
	},
	"redisVersion": {
		"value": "3.0.0"
	},
	"redisClusterName": {
		"value": "redis-arm-cluster"
	},
	"jumpbox": {
		"value": "Enabled"
	},
	"tshirtSize":  {
		"value": "Small"
	}
}
```

>[AZURE.NOTE]Der `storageAccountName`-Parameter muss ein nicht vorhandener, eindeutiger Speicherkontoname sein, der die Benennungsanforderungen für ein Microsoft Azure Storage-Konto (nur Kleinbuchstaben und Ziffern) erfüllt. Dieses Speicherkonto wird im Rahmen des Bereitstellungsprozesses erstellt.

Geben Sie Namen für die Azure-Bereitstellung, die Ressourcengruppe, den Azure-Speicherort sowie den Ordner mit der gespeicherten JSON-Datei an. Führen Sie dann diese Befehle aus:

```powershell
$deployName="<deployment name>, such as TestDeployment"
$RGName="<resource group name>, such as TestRG"
$locName="<Azure location, such as West US>"
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$templateFile= $folderName + "\azuredeploy.json"
$templateParameterFile= $folderName + "\azuredeploy-parameters.json"

New-AzureResourceGroup –Name $RGName –Location $locName

New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
```

>[AZURE.NOTE]`$RGName` muss innerhalb Ihres Abonnements eindeutig sein.

Beim Ausführen des **New-AzureResourceGroupDeployment**-Befehls werden damit die Parameterwerte aus der JSON-Parameterdatei ("azuredeploy-parameters.json") extrahiert und die Ausführung der Vorlage entsprechend gestartet. Das Definieren und Verwenden mehrerer Parameterdateien in den verschiedenen Umgebungen (Test, Produktion usw.) fördert die Wiederverwendung von Vorlagen und vereinfacht komplexe Lösungen mit mehreren Umgebungen.

Denken Sie beim Bereitstellen daran, dass ein neues Azure-Speicherkonto erstellt werden muss, daher muss der Name, den Sie als Speicherkontoparameter angeben, eindeutig sein und alle Anforderungen an ein Azure-Speicherkonto erfüllen (nur Kleinbuchstaben und Ziffern).

Während der Bereitstellung wird etwa Folgendes angezeigt:

	PS C:\> New-AzureResourceGroup –Name $RGName –Location $locName

	ResourceGroupName : TestRG
	Location          : westus
	ProvisioningState : Succeeded
	Tags              :
	Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

	ResourceId        : /subscriptions/1234abc1-abc1-1234-12a1-ab1ab12345ab/resourceGroups/TestRG

	PS C:\> New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateParameterFile $templateParameterFile -TemplateFile $templateFile
	VERBOSE: 2:39:10 PM - Template is valid.
	VERBOSE: 2:39:14 PM - Create template deployment 'TestDeployment'.
	VERBOSE: 2:39:25 PM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is running
	VERBOSE: 2:40:04 PM - Resource Microsoft.Resources/deployments 'node-resources0' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'node-resources1' provisioning status is running
	VERBOSE: 2:40:05 PM - Resource Microsoft.Resources/deployments 'shared-resources' provisioning status is succeeded
	VERBOSE: 2:42:23 PM - Resource Microsoft.Resources/deployments 'jumpbox-resources' provisioning status is succeeded
	VERBOSE: 2:47:44 PM - Resource Microsoft.Resources/deployments 'node-resources1' provisioning status is succeeded
	VERBOSE: 2:47:57 PM - Resource Microsoft.Resources/deployments 'node-resources0' provisioning status is succeeded
	VERBOSE: 2:48:01 PM - Resource Microsoft.Resources/deployments 'lastnode-resources' provisioning status is running
	VERBOSE: 2:58:24 PM - Resource Microsoft.Resources/deployments 'lastnode-resources' provisioning status is succeeded

	DeploymentName    : TestDeployment
	ResourceGroupName : TestRG
	ProvisioningState : Succeeded
	Timestamp         : 4/28/2015 7:58:23 PM
	Mode              : Incremental
	TemplateLink      :
	Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    adminUsername    String                     myadmin
                    adminPassword    SecureString
                    storageAccountName  String                     myuniqstgacct87
                    location         String                     West US
                    virtualNetworkName  String                     redisClustVnet
                    addressPrefix    String                     10.0.0.0/16
                    subnetName       String                     Subnet1
                    subnetPrefix     String                     10.0.0.0/24
                    nodeAddressPrefix  String                     10.0.0.1
                    jumpbox          String                     Enabled
                    tshirtSize       String                     Small
                    redisVersion     String                     3.0.0
                    redisClusterName  String                     redis-arm-cluster

	Outputs           :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    installCommand   String                     bash redis-cluster-install.sh -n redis-arm-cluster -v
                    3.0.0 -c 3 -m 3 -s 0 -p 10.0.0.1
                    setupCommand     String                     bash redis-cluster-install.sh -n redis-arm-cluster -v
                    3.0.0 -c 3 -m 3 -s 0 -p 10.0.0.1 -l
	...

Während und nach der Bereitstellung können Sie alle Anforderungen überprüfen, die während der Bereitstellung vorgenommen wurden, einschließlich der aufgetretenen Fehler.

Wechseln Sie dazu in das [Azure-Portal](https://portal.azure.com), und gehen Sie folgendermaßen vor:

- Klicken Sie auf der linken Navigationsleiste auf **Durchsuchen**, scrollen Sie nach unten, und klicken Sie auf **Ressourcengruppen**.
- Wählen Sie die gerade erstellte Ressourcengruppe aus, um das Blatt "Ressourcengruppe" anzuzeigen.
- Wählen Sie im Abschnitt **Überwachung** das Balkendiagramm "Ereignisse" aus. Dadurch werden die Ereignisse für die Bereitstellung angezeigt.
- Durch Klicken auf die einzelnen Ereignisse können Sie sich die Details jedes Vorgangs, der von der Vorlage angestoßen wurde, noch näher ansehen.

Wenn Sie diese Ressourcengruppe und all ihre Ressourcen (Speicherkonto, virtueller Computer und virtuelles Netzwerk) nach dem Testen entfernen möchten, verwenden Sie den folgenden Befehl:

```powershell
Remove-AzureResourceGroup –Name "<resource group name>"
```

### Schritt 3-a: Bereitstellen eines Redis-Clusters mit einer Vorlage mithilfe der Azure-Befehlszeilenschnittstelle

Zum Bereitstellen eines Redis-Clusters über die Azure-Befehlszeilenschnittstelle erstellen Sie zuerst eine Ressourcengruppe durch Angabe eines Namens und eines Speicherorts:

```powershell
azure group create TestRG "West US"
```

Übergeben Sie diesen Namen für die Ressourcengruppe, den Speicherort der JSON-Vorlagendatei und den Speicherort der Parameterdatei (siehe Abschnitt oben zu Azure PowerShell) an den folgenden Befehl:

```powershell
azure group deployment create TestRG -f .\azuredeploy.json -e .\azuredeploy-parameters.json
```

Sie können den Status der einzelnen Ressourcenbereitstellungen mit dem folgenden Befehl überprüfen:

```powershell
azure group deployment list TestRG
```

## Überblick über die Redis-Vorlagenstruktur und -Dateianordnung

Um eine stabile und wiederverwendbare Ressourcen-Manager-Vorlage zu erstellen, ist es erforderlich, genau über eine Reihe komplexer und zusammenhängender Aufgaben während der Bereitstellung einer komplexen Lösung wie eines Redis-Clusters nachzudenken. Durch die Nutzung von Ressourcen-Manager-Funktionen zum Verknüpfen von Vorlagen, Ressourcenschleifen und die Skriptausführung über zugehörige Erweiterungen ist es möglich, einen modularen Ansatz zu implementieren, der bei nahezu allen komplexen vorlagenbasierten Bereitstellungen wiederverwendet werden kann.

In diesem Diagramm werden die Beziehungen zwischen allen Dateien beschrieben, die für diese Bereitstellung von GitHub heruntergeladen werden:

![redis-files](media/virtual-machines-redis-template/redis-files.png)

Dieser Abschnitt führt Sie schrittweise durch die Struktur der Vorlage "azuredeploy.json" für den Redis-Cluster.

Wenn Sie keine Kopie der Vorlagendatei heruntergeladen haben, legen Sie einen lokalen Ordner als Speicherort für die Datei fest, und erstellen Sie ihn (z. B. "C:\\Azure\\Vorlagen\\RedisCluster"). Geben Sie den Ordnernamen an, und führen Sie die folgenden Befehle aus:

```powershell
$folderName="<folder name, such as C:\Azure\Templates\RedisCluster>"
$webclient = New-Object System.Net.WebClient
$url = "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json"
$filePath = $folderName + "\azuredeploy.json"
$webclient.DownloadFile($url,$filePath)
```

Öffnen Sie die Vorlage "azuredeploy.json" in einem Texteditor oder Tool Ihrer Wahl. Im Folgenden werden die Struktur der Vorlagendatei und der Zweck der einzelnen Abschnitte beschrieben. Sie können den Inhalt dieser Vorlage [hier](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/redis-high-availability/azuredeploy.json) auch in Ihrem Browser anzeigen.

### Abschnitt "parameters"

Wir haben bereits die Rolle der Datei "azuredeploy-parameters.json" erwähnt, die zum Übergeben eines bestimmten Satzes von Parameterwerten während der Vorlagenausführung verwendet wird. Im Abschnitt "parameters" von azuredeploy.json werden Parameter angegeben, die zur Eingabe von Daten in diese Vorlage verwendet werden.

Im Folgenden finden Sie ein Beispiel für einen Parameter für die "T-Shirt-Größe":

```json
"tshirtSize": {
	"type": "string",
	"defaultValue": "Small",
	"allowedValues": [
		"Small",
		"Medium",
		"Large"
	],
	"metadata": {
		"Description": "T-shirt size of the Redis deployment"
	}
},
```

>[AZURE.NOTE]Beachten Sie, dass `defaultValue` sowie `allowedValues` angegeben werden können.

### Abschnitt "Variablen"

Im Abschnitt "variables" sind Variablen angegeben, die in dieser Vorlage verwendet werden können. Er enthält im Prinzip eine Reihe von Feldern (JSON-Datentypen oder -Fragmente), die zum Zeitpunkt der Ausführung auf Konstanten oder berechnete Werte festgelegt werden. Hier finden Sie einige Beispiele, die von einfachen bis zu komplexeren reichen:

```json
"vmStorageAccountContainerName": "vhd-redis",
"vmStorageAccountDomain": ".blob.core.windows.net",
"vnetID": "[resourceId('Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
...
"machineSettings": {
	"adminUsername": "[parameters('adminUsername')]",
	"machineNamePrefix": "redisnode-",
	"osImageReference": {
		"publisher": "[variables('osFamilyUbuntu').imagePublisher]",
		"offer": "[variables('osFamilyUbuntu').imageOffer]",
		"sku": "[variables('osFamilyUbuntu').imageSKU]",
		"version": "latest"
	}
},
...
"vmScripts": {
	"scriptsToDownload": [
		"[concat(variables('scriptUrl'), 'redis-cluster-install.sh')]",
		"[concat(variables('scriptUrl'), 'redis-cluster-setup.sh')]",
		"[concat(variables('scriptUrl'), 'redis-sentinel-startup.sh')]"
	],
	"installCommand": "[concat('bash ', variables('installCommand'))]",
	"setupCommand": "[concat('bash ', variables('installCommand'), ' -l')]"
}
```

Die Variablen `vmStorageAccountContainerName` und `vmStorageAccountDomain` sind Beispiele für einfache Name-Wert-Variablen. `vnetID` ist ein Beispiel für eine Variable, die zur Laufzeit mit den Funktionen `resourceId` und `parameters` berechnet wird. `machineSettings` baut noch weitergehend auf diesen Konzepten auf, indem das JSON-Objekt `osImageReference` in der `machineSettings`-Variablen geschachtelt wird. `vmScripts` enthält das JSON-Array `scriptsToDownload`, das zur Laufzeit mithilfe der Funktionen `concat` und `variables` berechnet wird.

Wenn Sie die Größe der Redis-Clusterbereitstellung anpassen möchten, können Sie die Eigenschaften der Variablen `tshirtSizeSmall`, `tshirtSizeMedium` und `tshirtSizeLarge` in der Vorlage "azuredeploy.json" ändern.

```json
"tshirtSizeSmall": {
	"vmSizeMember": "Standard_A1",
	"numberOfMasters": 3,
	"numberOfSlaves": 0,
	"totalMemberCount": 3,
	"totalMemberCountExcludingLast": 2,
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
"tshirtSizeMedium": {
	"vmSizeMember": "Standard_A2",
	"numberOfMasters": 3,
	"numberOfSlaves": 3,
	"totalMemberCount": 6,
	"totalMemberCountExcludingLast": 5,
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
"tshirtSizeLarge": {
	"vmSizeMember": "Standard_A5",
	"numberOfMasters": 3,
	"numberOfSlaves": 6,
	"totalMemberCount": 9,
	"totalMemberCountExcludingLast": 8,
	"arbiter": "Enabled",
	"vmTemplate": "[concat(variables('templateBaseUrl'), 'node-resources.json')]"
},
```

Hinweis: Die Eigenschaften `totalMemberCountExcludingLast` und `totalMemberCount` sind erforderlich, da die Vorlagensprache derzeit keine Math-Vorgänge umfasst.

Weitere Informationen zur Vorlagensprache finden Sie im MSDN unter [Vorlagensprache des Azure-Ressourcen-Managers](../resource-group-authoring-templates.md).

### Abschnitt "Ressourcen"

Im Abschnitt "resources" geschieht am meisten. Wenn Sie diesen Abschnitt genau überprüfen, erkennen Sie sofort zwei unterschiedliche Fälle. Der erste ist ein vom Typ `Microsoft.Resources/deployments` definiertes Element, das im Wesentlichen eine geschachtelte Bereitstellung innerhalb der Hauptbereitstellung aufruft. Der zweite ist die `templateLink`-Eigenschaft (und die zugehörige `contentVersion`-Eigenschaft), die es möglich macht, eine verknüpfte Vorlagendatei anzugeben, die unter Übergabe eines Satzes von Parametern als Eingabe aufgerufen wird. Diese sind in diesem Vorlagenfragment zu sehen:

```json
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
```

Im ersten Beispiel wird deutlich, wie die Datei "azuredeploy.json" in diesem Szenario als Mechanismus zur Orchestrierung organisiert wurde, indem eine Anzahl anderer Vorlagendateien aufgerufen wird. Jede Datei ist jeweils für einen Teil der erforderlichen Bereitstellungsaufgaben verantwortlich.

Insbesondere folgende verknüpfte Vorlagen werden für diese Bereitstellung eingesetzt:

- **shared-resource.json**: enthält die Definition aller Ressourcen, die in der Bereitstellung gemeinsam genutzt werden. Beispiele hierfür sind Speicherkonten, die zum Speichern der Betriebssystemdatenträger, virtuellen Netzwerke und Verfügbarkeitsgruppen eines virtuellen Computers verwendet werden.
- **jumpbox-resources.json**: stellt die "Jumpbox"-VM und alle zugehörigen Ressourcen wie Netzwerkschnittstelle, öffentliche IP-Adresse und den Eingabeendpunkt für SSH in der Umgebung bereit.
- **nodes-resources.json**: stellt alle Redis-Clusterknoten-VMs und verbundene Ressourcen (Netzwerkkarten, private IP-Adressen usw.) bereit. Diese Vorlage stellt auch VM-Erweiterungen bereit (benutzerdefinierte Skripts für Linux) und ruft ein Bash-Skript auf, um Redis auf jedem Knoten physisch zu installieren und einzurichten. Das aufzurufende Skript wird im `machineSettings`-Parameter der `commandToExecute`-Eigenschaft an diese Vorlage übergeben. Alle bis auf einen Redis-Clusterknoten können parallel bereitgestellt und per Skript eingerichtet werden. Ein Knoten muss bis zum Ende zurückgehalten werden, da das Setup des Redis-Clusters nur auf einem Knoten ausgeführt werden kann, und zwar nachdem alle Knoten den Redis-Server ausführen. Deshalb wird das auszuführende Skript an diese Vorlage übergeben. Der letzte Knoten muss mit einem etwas anderen Skript ausgeführt werden, mit dem nicht nur der Redis-Server installiert, sondern auch der Redis-Cluster eingerichtet wird.

Sehen wir uns nun die *Verwendung* der letzten Vorlage, "node-resources.json", genauer an, da diese aus Sicht der Vorlagenentwicklung am interessantesten ist. Ein wichtiges hervorzuhebendes Konzept ist die Möglichkeit, mehrere Kopien eines einzelnen Ressourcentyps mit einer einzigen Vorlagendatei bereitzustellen und für jede Instanz eindeutige Werte für die erforderlichen Einstellungen festzulegen. Dieses Konzept ist als **Ressourcenschleife** bekannt.

Wenn "node-resources.json" in der Hauptdatei "azuredeploy.json" aufgerufen wird, erfolgt dieser Aufruf aus einer Ressource, die das `copy`-Element zum Erstellen einer Schleife verwendet. Eine Ressource, die das `copy`-Element verwendet, "kopiert" sich selbst in der im `count`-Parameter des `copy`-Elements angegebenen Anzahl. Für alle Einstellungen, in denen es erforderlich ist, eindeutige Werte zwischen verschiedenen Instanzen der bereitgestellten Ressource anzugeben, kann die **copyindex()**-Funktion verwendet werden, um einen numerischen Wert zu erhalten, der den aktuellen Index in der bestimmten Ressourcenschleifenerstellung angibt. Im folgenden Fragment aus "azuredeploy.json" können Sie sehen, wie dieses Konzept auf die Erstellung mehrerer VMs für Redis-Clusterknoten angewendet wird:

```json
{
    "type": "Microsoft.Resources/deployments",
    "name": "[concat('node-resources', copyindex())]",
    "apiVersion": "2015-01-01",
    "dependsOn": [
        "[concat('Microsoft.Resources/deployments/', 'shared-resources')]"
    ],
    "copy": {
        "name": "memberNodesLoop",
        "count": "[variables('clusterSpec').totalMemberCountExcludingLast]"
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
                "value": "[variables('storageSettings')]"
            },
            "networkSettings": {
                "value": "[variables('networkSettings')]"
            },
            "machineSettings": {
                "value": {
                    "adminUsername": "[variables('machineSettings').adminUsername]",
                    "machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
                    "osImageReference": "[variables('machineSettings').osImageReference]",
                    "vmSize": "[variables('clusterSpec').vmSizeMember]",
                    "machineIndex": "[copyindex()]",
                    "vmScripts": "[variables('vmScripts').scriptsToDownload]",
                    "commandToExecute": "[concat(variables('vmScripts').installCommand, ' -i ', copyindex())]"
                }
            },
            "adminPassword": {
                "value": "[parameters('adminPassword')]"
            }
        }
    }
},
```

Ein weiteres wichtiges Konzept bei der Ressourcenerstellung ist die Möglichkeit, Abhängigkeiten und Vorränge zwischen Ressourcen anzugeben, wie Sie am JSON-Array `dependsOn` sehen können. In dieser speziellen Vorlage sehen Sie, dass die Redis-Clusterknoten von den zuerst erstellten freigegebenen Ressourcen abhängig sind.

Wie bereits erwähnt, darf der letzte Knoten erst bereitgestellt werden, wenn auf allen anderen Knoten im Redis-Cluster der Redis Server bereitgestellt wurde und ausgeführt wird. Dies erfolgt in "azuredeploy.json" über die Ressource `lastnode-resources`, die von der `copy`-Schleife `memberNodesLoop` aus dem Vorlagenausschnitt oben abhängt. Nach Abschluss der Bereitstellung von `memberNodesLoop` kann `lastnode-resources` bereitgestellt werden:

```json
{
	"name": "lastnode-resources",
	"type": "Microsoft.Resources/deployments",
	"apiVersion": "2015-01-01",
	"dependsOn": [
		"memberNodesLoop"
	],
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
				"value": "[variables('storageSettings')]"
			},
			"networkSettings": {
				"value": "[variables('networkSettings')]"
			},
			"machineSettings": {
				"value": {
					"adminUsername": "[variables('machineSettings').adminUsername]",
					"machineNamePrefix": "[variables('machineSettings').machineNamePrefix]",
					"osImageReference": "[variables('machineSettings').osImageReference]",
					"vmSize": "[variables('clusterSpec').vmSizeMember]",
					"machineIndex": "[variables('clusterSpec').totalMemberCountExcludingLast]",
					"vmScripts": "[variables('vmScripts').scriptsToDownload]",
					"commandToExecute": "[concat(variables('vmScripts').setupCommand, ' -i ', variables('clusterSpec').totalMemberCountExcludingLast)]"
				}
			},
			"adminPassword": {
				"value": "[parameters('adminPassword')]"
			}
		}
	}
}
```

Beachten Sie, wie die `lastnode-resources`-Ressource eine etwas andere `machineSettings.commandToExecute` an die verknüpfte Vorlage übergibt. Dies ist erforderlich, da für den letzten Knoten neben der Installation des Redis-Servers ein Skript zum Einrichten des Redis-Clusters aufgerufen werden muss (dies ist nur einmal erforderlich, wenn alle Redis-Server ausgeführt werden).

Ein weiteres interessantes Fragment ist das im Zusammenhang mit den VM-Erweiterungen `CustomScriptForLinux`. Diese werden als gesonderter Ressourcentyp mit einer Abhängigkeit auf jedem Clusterknoten installiert. In diesem Fall dient dies zum Installieren und Einrichten von Redis auf jedem VM-Knoten. Sehen wir uns einen Ausschnitt aus der Vorlage "node-resources.json" an, die diese verwendet:

```json
{
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "[concat('vmMember', parameters('machineSettings').machineIndex, '/installscript')]",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('commonSettings').location]",
    "dependsOn": [
        "[concat('Microsoft.Compute/virtualMachines/', 'vmMember', parameters('machineSettings').machineIndex)]"
    ],
    "properties": {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.2",
        "settings": {
            "fileUris": "[parameters('machineSettings').vmScripts]",
            "commandToExecute": "[parameters('machineSettings').commandToExecute]"
        }
    }
}
```

Sie sehen, dass diese Ressource von der Ressourcen-VM abhängig ist, die bereits bereitgestellt wurde (`Microsoft.Compute/virtualMachines/vmMember<X>`, wobei `<X>` der Parameter `machineSettings.machineIndex`, d. h. der Index des virtuellen Computers ist, der mithilfe der **copyindex()**-Funktion an dieses Skript übergeben wurde).

Indem Sie sich mit den anderen Dateien in dieser Bereitstellung vertraut machen, werden Sie alle Details und bewährten Methoden zum Organisieren und Orchestrieren komplexer Bereitstellungsstrategien für Lösungen mit mehreren Knoten verstehen, basierend auf sämtlichen Technologien und unter Nutzung von Azure-Ressourcen-Manager-Vorlagen. Obwohl nicht zwingend erforderlich, ist das Strukturieren der Vorlagendateien eine empfohlene Vorgehensweise, wie im folgenden Diagramm verdeutlicht:

![redis-template-structure](media/virtual-machines-redis-template/redis-template-structure.png)

Im Wesentlichen empfiehlt dieser Ansatz:

- Definieren Sie die Kernvorlagendatei als zentrale Orchestrierungsstelle für alle spezifischen Bereitstellungsaktivitäten, indem Sie Vorlagen nutzen, die auf Aufrufe von untergeordneten Vorlagenausführungen verweisen.
- Erstellen Sie eine spezifische Vorlagendatei, die alle Ressourcen bereitstellt, die gemeinsam von allen anderen speziellen Bereitstellungsaufgaben (Speicherkonten, Konfiguration von virtuellen Netzwerken usw.) gemeinsam genutzt werden. Diese kann häufig zwischen Bereitstellungen wiederverwendet werden, die ähnliche Anforderungen im Hinblick auf die allgemeine Infrastruktur haben.
- Schließen Sie optionale Ressourcenvorlagen ein, um ressourcenspezifische Anforderungen zu erkennen.
- Erstellen Sie für identische Mitglieder einer Ressourcengruppe (Knoten in einem Cluster usw.) spezielle Vorlagen, die die Ressourcenschleife nutzen, um für mehrere Instanzen eindeutige Eigenschaften bereitzustellen.
- Für alle Nachbereitstellungsaufgaben (Produktinstallation, Konfigurationen usw.) nutzen Sie die Bereitstellung von Skripterweiterungen, und erstellen Sie Skripts, die für die einzelnen Technologien spezifisch sind.

Weitere Informationen finden Sie unter [Vorlagensprache des Azure-Ressourcen-Managers](../resource-group-authoring-templates.md).

<!---HONumber=August15_HO9-->