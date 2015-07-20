<properties
	pageTitle="Erstellen einer Verfügbarkeitsgruppe mithilfe von Vorlagen im Azure-Ressourcen-Manager"
	description="Hierin wird beschrieben, wie die Vorlage für die Verfügbarkeitsgruppe mit Vorlagensyntax verwendet wird."
	services="virtual-machines"
	documentationCenter=""
	authors="KBDAzure"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="05/04/2015"
	ms.author="kathydav"/>

# Erstellen einer Verfügbarkeitsgruppe mithilfe von Vorlagen im Azure-Ressourcen-Manager

Sie können mit Azure PowerShell oder der Azure-Befehlszeilenschnittstelle (CLI) und einer Ressourcen-Manager-Vorlage auf einfache Weise eine Verfügbarkeitsgruppe für einen virtuellen Computer erstellen. Von dieser Vorlage wird eine Verfügbarkeitsgruppe erstellt.

Bevor Sie beginnen, stellen Sie sicher, dass Azure, PowerShell und Azure-CLI konfiguriert und einsatzbereit sind.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up](../../includes/xplat-getting-set-up.md)]


## Erstellen einer Verfügbarkeitsgruppe mithilfe einer Ressourcen-Manager-Vorlage

Gehen Sie wie folgt vor, um mithilfe einer Resource Manager-Vorlage im Github-Vorlagenrepository mit Azure PowerShell eine Verfügbarkeitsgruppe für einen virtuellen Computer zu erstellen.

### Schritt 1: Laden Sie die JSON-Datei herunter.

Bestimmen Sie einen lokalen Ordner als Speicherort für die JSON-Vorlagendateien, und erstellen Sie den Ordner (z. B. C:\\Azure\\Vorlagen\\Verfügbarkeit).

Ersetzen Sie den Ordnernamen, kopieren Sie die folgenden Befehle, und führen Sie sie aus.

	$folderName="<folder name, such as C:\Azure\Templates\availability>"
	$webclient = New-Object System.Net.WebClient
	$url = "https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-2-FDs-no-resource-loops/azuredeploy.json"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath)

### Schritt 2: Erfassen Sie die Details für die erforderlichen Parameter.

Wenn Sie eine Vorlage verwenden, müssen Sie Details wie den Speicherort und den Gruppennamen angeben. Um herauszufinden, welche Parameter für eine Vorlage erforderlich sind, führen Sie einen der folgenden Schritte aus:

- Die Liste der Parameter finden Sie [hier](http://azure.microsoft.com/documentation/templates/201-2-vms-2-FDs-no-resource-loops/).
- Öffnen Sie die JSON-Datei in einem Tool oder Texteditor Ihrer Wahl. Suchen Sie oben in der Datei nach dem Bereich „Parameter“. Dort sind die Parameter aufgeführt, die erforderlich sind, damit der virtuelle Computer von der Vorlage konfiguriert werden kann.

Halten Sie die für die Eingabe erforderlichen Informationen bereit. Wenn Sie den Befehl zum Bereitstellen der Vorlage ausführen, werden Sie nach diesen Informationen gefragt.

### Schritt 3: Erstellen Sie die Verfügbarkeitsgruppe.

In den folgenden Abschnitte wird erläutert, wie Sie dafür Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden.

### Verwenden von Azure PowerShell

Geben Sie einen Namen für die Azure-Bereitstellung, einen Namen für die Ressourcengruppe, einen Azure-Speicherort sowie den Ordner mit der gespeicherte JSON-Datei an, und führen Sie die folgenden Befehle aus:

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\availability>"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Wenn Sie den Befehl **New-AzureResourceGroupDeployment** ausführen, werden Sie aufgefordert, im Abschnitt **„parameters“** der JSON-Datei Parameterwerte anzugeben. Danach werden die Ressourcen- und die Verfügbarkeitsgruppe vom Befehl erstellt.

Es folgt ein PowerShell-Beispielbefehlssatz für die Vorlage.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$folderName="C:\Azure\Templates[thing]"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Die Ausgabe sollte folgendermaßen aussehen:

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	dnsNameForPublicIP: 131.107.89.211
	adminUserName: WebAdmin1
	adminPassword: *******
	vmSourceImageName: a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd
	...

Verwenden Sie zum Entfernen dieser Ressourcengruppe und all ihrer Ressourcen (Speicherkonto, virtueller Computer, virtuelles Netzwerk) den folgenden Befehl:

	Remove-AzureResourceGroup –Name "<resource group name>"


## Verwenden der Azure-Befehlszeilenschnittstelle

Gehen Sie wie folgt vor, um die Verfügbarkeitsgruppe mithilfe einer Ressourcen-Manager-Vorlage im Github-Vorlagenrepository mit einem Azure-Befehlszeilenschnittstellen-Befehl zu erstellen.

	azure group deployment create <my-resource-group> <my-deployment-name> --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/201-2-vms-2-FDs-no-resource-loops/azuredeploy.json

<!---HONumber=July15_HO2-->