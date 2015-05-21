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
	ms.date="04/20/2015" 
	ms.author="kathydav"/>

# Erstellen einer Verfügbarkeitsgruppe mithilfe von Vorlagen im Azure-Ressourcen-Manager

Sie können ganz einfach eine Verfügbarkeitsgruppe für einen virtuellen Computer erstellen, indem Sie Azure PowerShell oder die plattformübergreifende Befehlszeilenschnittstelle \(XPlat-CLI\) und eine Ressourcen-Manager-Vorlage verwenden. Von dieser Vorlage wird eine Verfügbarkeitsgruppe erstellt.
 
Stellen Sie zunächst sicher, dass Azure PowerShell und die XPlat-CLI konfiguriert und einsatzbereit sind.

[AZURE.INCLUDE [arm-getting-setup-powershell](../includes/arm-getting-setup-powershell.md)]

[AZURE.INCLUDE [xplat-getting-set-up](../includes/xplat-getting-set-up.md)]


## [Ausführen von Aktionen] mit einer Ressourcen-Manager-Vorlage und Azure PowerShell

Gehen folgendermaßen Sie vor, um mit einer Ressourcen-Manager-Vorlage im Github-Vorlagenrepository mit Azure PowerShell [Aktionen auszuführen].

### Schritt 1: Laden Sie die JSON-Datei herunter.

Bestimmen Sie einen lokalen Ordner als Speicherort für die JSON-Vorlagendateien, und erstellen Sie den Ordner \(z. B. C:\\Azure\\Vorlagen\[Name\]\).

Ersetzen Sie den Ordnernamen, kopieren Sie die folgenden Befehle, und führen Sie sie aus.

	$folderName="<folder name, such as C:\Azure\Templates\[thing]>"
	$webclient = New-Object System.Net.WebClient
	$url = "[Writers: add the URL to the RAW version of the target template in GitHub]"
	$filePath = $folderName + "\azuredeploy.json"
	$webclient.DownloadFile($url,$filePath) 

### Schritt 2 \(optional\): Rufen Sie die Parameter auf.

Wenn Sie mit einer Vorlage [Aktionen ausführen], müssen Sie mehrere Konfigurationsparameter angeben. Um die Parameter anzuzeigen, die Sie für die Vorlage in einer lokalen JSON-Datei angeben müssen, bevor Sie den Befehl zum Erstellen des virtuellen Computers ausführen, öffnen Sie die JSON-Datei in einem Tool oder Texteditor Ihrer Wahl. Suchen Sie oben in der Datei nach dem Bereich „Parameter“. Dort sind die Parameter aufgeführt, die erforderlich sind, damit der virtuelle Computer von der Vorlage konfiguriert werden kann. Dies ist der Bereich **„Parameter“** der Vorlage „azuredeploy.json“:

[Hinweis für Autoren: Fügen Sie den Bereich „Parameter“ der Vorlage „azuredeploy.json“ ein, und formatieren Sie ihn als Code.]

### Schritt 3: Rufen Sie Informationen ab, die [zum Abschließen der Vorlage erforderlich] sind.

[Hinweis für Autoren: Dieser Bereich ist optional, um bei Bedarf Parameterwerte zu sammeln.]

### Schritt 4: [Führen Sie die Aktion] mit der Vorlage aus.

Geben Sie einen Namen für die Azure-Bereitstellung, einen Namen für die Ressourcengruppe, einen Azure-Speicherort sowie den Ordner mit der gespeicherte JSON-Datei an, und führen Sie die folgenden Befehle aus:

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$folderName="<folder name, such as C:\Azure\Templates\[thing]>" 
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Wenn Sie den Befehl **New-AzureResourceGroupDeployment** ausführen, werden Sie aufgefordert, im Bereich **„Parameter“** der JSON-Datei Parameterwerte anzugeben. Danach werden die Ressourcen- und die Verfügbarkeitsgruppe vom Befehl erstellt.

Es folgt ein PowerShell-Beispielbefehlssatz für die Vorlage.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$folderName="C:\Azure\Templates\[thing]"
	$templateFile= $folderName + "\azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateFile $templateFile

Die Ausgabe sollte folgendermaßen aussehen:

[Hinweis für Autoren: Fügen Sie bei den ersten Parametern, zu deren Eingabe Sie aufgefordert werden, die PowerShell-Anzeige ein, und ersetzen Sie Folgendes:]

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	dnsNameForPublicIP: 131.107.89.211
	adminUserName: WebAdmin1
	adminPassword: *******
	vmSourceImageName: a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201503.01-en.us-127GB.vhd
	...

Verwenden Sie zum Entfernen dieser Ressourcengruppe und all ihrer Ressourcen \(Speicherkonto, virtueller Computer, virtuelles Netzwerk\) den folgenden Befehl:

	Remove-AzureResourceGroup –Name "<resource group name>"


## [Ausführen von Aktionen] mit einer Ressourcen-Manager-Vorlage und einer XPlat-CLI

Gehen Sie folgendermaßen vor, um mit einer Ressourcen-Manager-Vorlage im Github-Vorlagenrepository mit Azure CLI-Befehlen [Aktionen auszuführen].

### Schritt 1: Laden Sie die JSON-Datei für die Vorlage herunter.

Bestimmen Sie einen lokalen Ordner als Speicherort für die JSON-Vorlagendateien, und erstellen Sie den Ordner \(z. B. C:\\Azure\\Vorlagen\[Name\]\).

Geben Sie den Ordnernamen an, und führen Sie die folgenden Befehle aus:

[XPlat-Befehle zum Herunterladen der Vorlagendatei]

### Schritt 2 \(optional\): Rufen Sie die Parameter der Vorlage auf.

Wenn Sie mit einer Vorlage [Aktionen ausführen], müssen Sie mehrere Konfigurationsparameter angeben. Um die Parameter anzuzeigen, die Sie für die Vorlage in einer lokalen JSON-Datei angeben müssen, bevor Sie den Befehl zum Erstellen des virtuellen Computers ausführen, öffnen Sie die JSON-Datei in einem Tool oder Texteditor Ihrer Wahl. Suchen Sie oben in der Datei nach dem Bereich „Parameter“. Dort sind die Parameter aufgeführt, die erforderlich sind, damit der virtuelle Computer von der Vorlage konfiguriert werden kann. Dies ist der Bereich **„Parameter“** der Vorlage „azuredeploy.json“:

[Hinweis für Autoren: Fügen Sie den Bereich „Parameter“ der Vorlage „azuredeploy.json“ ein, und formatieren Sie ihn als Code.]

### Schritt 3: Rufen Sie Informationen ab, die [zum Abschließen der Vorlage erforderlich] sind.

[Hinweis für Autoren: Dieser Bereich ist optional, um bei Bedarf Parameterwerte zu sammeln.]

### Schritt 4: [Führen Sie die Aktion] mit der Vorlage aus.

Geben Sie erforderliche Informationen an, und führen Sie die folgenden Befehle aus:

[XPlat-Befehle zum Ausführen der Vorlagendatei]

[Erläuterung, wie die Vorlage von XPlat ausgeführt wird]


Es folgt ein XPlat-CLI-Beispielbefehlssatz für die Vorlage.

[XPlat-Beispielbefehl]

Die Ausgabe sollte folgendermaßen aussehen:

[Hinweis für Autoren: Fügen Sie bei den ersten Parametern, zu deren Eingabe Sie aufgefordert werden, die XPlat-Anzeige ein.]


Zum Entfernen dieser Ressourcengruppe und all ihrer Ressourcen \([Elemente in der Ressourcengruppe]\) verwenden Sie den folgenden Befehl:

[XPlat-Befehl]




<!--HONumber=52-->
