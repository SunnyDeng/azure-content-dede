<properties
	pageTitle="Verwalten von Azure-Ressourcen-Manager-VMs | Microsoft Azure"
	description="Verwalten von virtuellen Computern mit Azure-Ressourcen-Manager, Vorlagen und PowerShell"
	services="virtual-machines"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor=""
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="10/08/2015"
	ms.author="davidmu"/>

# Verwalten von virtuellen Computern mit Azure-Ressourcen-Manager und PowerShell

> [AZURE.SELECTOR]
- [Preview Portal](virtual-machines-windows-tutorial.md)
- [PowerShell - Windows](virtual-machines-deploy-rmtemplates-powershell.md)
- [Azure CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)

Die Verwendung von Azure PowerShell und Ressourcen-Manager-Vorlagen bietet große Flexibilität beim Verwalten von Ressourcen in Microsoft Azure. Über die in diesem Artikel beschriebenen Aufgaben können Sie VM-Ressourcen erstellen und verwalten.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-tutorial-classic-portal.md).

Für diese Aufgaben wird eine Ressourcen-Manager-Vorlage und PowerShell verwendet:

- [Erstellen eines virtuellen Computers](#windowsvm)
- [Erstellen eines virtuellen Computers mit einem spezialisierten Datenträger](#customvm)
- [Erstellen von mehreren virtuellen Computern in einem virtuellen Netzwerk mit externem Lastenausgleich](#multivm)

Für diese Aufgaben wird ausschließlich PowerShell verwendet:

- [Entfernen einer Ressourcengruppe](#removerg)
- [Anzeigen von Informationen zu einem virtuellen Computer](#displayvm)
- [Starten eines virtuellen Computers](#start)
- [Beenden eines virtuellen Computers](#stop)
- [Neustarten eines virtuellen Computers](#restart)
- [Löschen eines virtuellen Computers](#delete)

Stellen Sie vor dem Loslegen sicher, dass Azure PowerShell bereit ist.

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]



## Azure-Ressourcen-Manager-Vorlagen und -Ressourcengruppen

In diesem Artikel wird u. a. gezeigt, wie Sie Azure-Ressourcen-Manager-Vorlagen und PowerShell für die automatische Bereitstellung und Verwaltung von virtuellen Azure-Computern verwenden.

Die meisten Anwendungen, die in Microsoft Azure ausgeführt werden, bestehen aus einer Kombination von verschiedenen Cloudressourcentypen (z. B. einem oder mehreren virtuellen Computern und Speicherkonten, einer SQL-Datenbank oder einem virtuellen Netzwerk). Mithilfe von Azure-Ressourcen-Manager-Vorlagen können Sie die unterschiedlichen Ressourcen gemeinsam verwalten. Sie verwenden hierfür eine JSON-Beschreibung der Ressourcen sowie der zugeordneten Konfigurations- und Bereitstellungsparameter.

Nachdem Sie eine JSON-basierte Ressourcenvorlage definiert haben, können Sie diese mit einem PowerShell-Befehl verwenden, um die definierten Ressourcen in Azure bereitzustellen. Sie können diese Befehle entweder separat in der PowerShell-Befehlsshell ausführen oder in ein Skript integrieren, das eine zusätzliche Automatisierungslogik enthält.

Die Ressourcen, die Sie mithilfe von Azure-Ressourcen-Manager-Vorlagen erstellen, werden entweder in einer neuen oder einer vorhandenen *Azure-Ressourcengruppe* bereitgestellt. Eine Ressourcengruppe ermöglicht es Ihnen, mehrere bereitgestellte Ressourcen gemeinsam als eine logische Gruppe zu verwalten. Auf diese Weise können Sie den gesamten Lebenszyklus der Gruppe/Anwendung verwalten.

Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](resource-group-authoring-templates.md).

### Erstellen einer Ressourcengruppe

Für Aufgaben, mit denen eine Ressource erstellt wird, ist eine Ressourcengruppe erforderlich. Erstellen Sie diese gegebenenfalls, wenn Sie noch nicht über eine Ressourcengruppe verfügen.

Ersetzen Sie im folgenden Befehl *resource group name* durch den Namen der neuen Ressourcengruppe und *Azure location* durch den Standort des Azure-Datencenters, in dem die Ressource erstellt werden soll, und führen Sie den Befehl aus:

	New-AzureRmResourceGroup -Name "resource group name" -Location "Azure location"

## <a id="windowsvm"></a>AUFGABE: Erstellen eines virtuellen Computers

Für diese Aufgabe wird eine Vorlage aus dem Vorlagenkatalog verwendet. Weitere Informationen zu dieser Vorlage finden Sie unter [Bereitstellen eines einfachen virtuellen Windows-Computers in der Region "USA, Westen"](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

![](./media/virtual-machines-deploy-rmtemplates-powershell/windowsvm.png)

Ersetzen Sie im folgenden Befehl *deployment name* durch den Namen der Bereitstellung und *resource group name* durch den Namen der vorhandenen Ressourcengruppe, und führen Sie den Befehl aus:

	New-AzureRmResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"

Hier sehen Sie ein Beispiel:

	New-AzureRmResourceGroupDeployment -Name "TestDeployment" -ResourceGroupName "TestRG" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"

Sie werden aufgefordert, im Abschnitt **parameters** der JSON-Datei Parameterwerte anzugeben.

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso

Folgendes sollte angezeigt werden:

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
	                    newStorageAccountName  String                     saacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso9875
	                    windowsOSVersion  String                     2012-R2-Datacenter

	Outputs           :

Das folgende Video veranschaulicht diese Aufgabe:

[AZURE.VIDEO deploy-a-windows-virtual-machine-with-azure-resource-manager-templates-and-powershell]

## <a id="customvm"></a>AUFGABE: Erstellen eines virtuellen Computers mit einem spezialisierten Datenträger

Für diese Aufgabe wird eine Vorlage aus dem Vorlagenkatalog verwendet. Weitere Informationen zu dieser Vorlage finden Sie unter [Erstellen eines virtuellen Computers anhand eines spezialisierten VHD-Datenträgers](https://azure.microsoft.com/documentation/templates/201-vm-from-specialized-vhd/).

Ersetzen Sie im folgenden Befehl *deployment name* durch den Namen der Bereitstellung und *resource group name* durch den Namen der vorhandenen Ressourcengruppe, und führen Sie den Befehl aus:

	New-AzureRmResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"

Hier sehen Sie ein Beispiel:

	New-AzureRmResourceGroupDeployment -Name "TestDeployment" -ResourceGroupName "TestRG" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-vm-from-specialized-vhd/azuredeploy.json"

Sie werden aufgefordert, im Abschnitt **parameters** der JSON-Datei Parameterwerte anzugeben.

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	osDiskVhdUri: http://saacct.blob.core.windows.net/vhds/osdiskforwindows.vhd
	osType: windows
	location: West US
	vmSize: Standard_A3
	...

> [AZURE.NOTE]Im oben gezeigten Beispiel wird eine VHD-Datei verwendet, die im Speicherkonto "saacct" vorhanden ist. Der Name des Datenträgers wurde als Parameter an die Vorlage übergeben.

Das folgende Video veranschaulicht diese Aufgabe:

[AZURE.VIDEO create-a-custom-virtual-machine-image-in-azure-resource-manager-with-powershell]

## <a id="multivm"></a>AUFGABE: Erstellen von mehreren virtuellen Computern in einem virtuellen Netzwerk mit externem Lastenausgleich

Für diese Aufgabe wird eine Vorlage aus dem Vorlagenkatalog verwendet. Weitere Informationen zu dieser Vorlage finden Sie unter [Erstellen eines virtuellen Computers anhand eines spezialisierten VHD-Datenträgers](https://azure.microsoft.com/documentation/templates/201-2-vms-loadbalancer-lbrules/).

![](./media/virtual-machines-deploy-rmtemplates-powershell/multivmextlb.png)

Ersetzen Sie im folgenden Befehl *deployment name* durch den Namen der Bereitstellung und *resource group name* durch den Namen der vorhandenen Ressourcengruppe, und führen Sie den Befehl aus:

	New-AzureRmResourceGroupDeployment -Name "deployment name" -ResourceGroupName "resource group name" -TemplateUri "https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-lbrules/azuredeploy.json"

Sie werden aufgefordert, im Abschnitt **parameters** der JSON-Datei Parameterwerte anzugeben.

	cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: saTest
	adminUserName: WebAdmin1
	adminPassword: *******
	dnsNameforLBIP: web07
	backendPort: 80
	vmNamePrefix: WEBFARM
	...

Das folgende Video veranschaulicht diese Aufgabe:

[AZURE.VIDEO deploy-multi-vm-app-with-a-virtual-network-and-load-balancer-in-azure-resource-manager]

## <a id="removerg"></a>AUFGABE: Entfernen einer Ressourcengruppe

Ersetzen Sie im folgenden Befehl *resource group name* durch den Namen der Ressourcengruppe, die entfernt werden soll, und führen Sie den Befehl aus:

	Remove-AzureRmResourceGroup  -Name "resource group name"

> [AZURE.NOTE]Mit dem Parameter **- Force** können Sie die Bestätigungsaufforderung überspringen.

Wenn Sie den Parameter "-Force" nicht verwendet haben, werden Sie aufgefordert, den Vorgang zu bestätigen:

	Confirm
	Are you sure you want to remove resource group 'BuildRG'
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Das folgende Video veranschaulicht diese Aufgabe:

[AZURE.VIDEO removing-a-resource-group-in-azure]

## <a id="displayvm"></a>AUFGABE: Anzeigen von Informationen zu einem virtuellen Computer

Ersetzen Sie im folgenden Befehl *resource group name* durch den Namen der Ressourcengruppe, die den virtuellen Computer enthält, und *VM name* durch den Namen des Computers, und führen Sie den Befehl aus:

	Get-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Folgendes sollte angezeigt werden:

	AvailabilitySetReference : null
	Extensions               : []
	HardwareProfile          : {
	                             "VirtualMachineSize": "Standard_D1"
	                           }
	Id                       : /subscriptions/{subscription-id}/resourceGroups/BuildRG/providers/Microso
	                           ft.Compute/virtualMachines/MyWindowsVM
	InstanceView             : null
	Location                 : westus
	Name                     : MyWindowsVM
	NetworkProfile           : {
	                             "NetworkInterfaces": [
	                               {
	                                 "Primary": null,
	                                 "ReferenceUri": "/subscriptions/{subscription-id}/resourceGroups/Bu
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
	                                 "Uri": "http://saacct.blob.core.windows.net/vhds/osdiskforwindowssimple.vhd"
	                               }
	                             },
	                             "SourceImage": null
	                           }
	Tags                     : {}
	Type                     : Microsoft.Compute/virtualMachines

Das folgende Video veranschaulicht diese Aufgabe:

[AZURE.VIDEO displaying-information-about-a-virtual-machine-in-microsoft-azure-with-powershell]

## <a id="start"></a>AUFGABE: Starten eines virtuellen Computers

Ersetzen Sie im folgenden Befehl *resource group name* durch den Namen der Ressourcengruppe, die den virtuellen Computer enthält, und *VM name* durch den Namen des Computers, und führen Sie den Befehl aus:

	Start-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Folgendes sollte angezeigt werden:

	EndTime             : 4/28/2015 11:11:41 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:10:35 AM -07:00
	Status              : Succeeded
	TrackingOperationId : e1705973-d266-467e-8655-920016145347
	RequestId           : aac41de1-b85d-4429-9a3d-040b922d2e6d
	StatusCode          : OK

Das folgende Video veranschaulicht diese Aufgabe:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="stop"></a>AUFGABE: Beenden eines virtuellen Computers

Ersetzen Sie im folgenden Befehl *resource group name* durch den Namen der Ressourcengruppe, die den virtuellen Computer enthält, und *VM name* durch den Namen des Computers, und führen Sie den Befehl aus:

	Stop-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Sie werden aufgefordert, den Vorgang zu bestätigen:

	Virtual machine stopping operation
	This cmdlet will stop the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Folgendes sollte angezeigt werden:

	EndTime             : 4/28/2015 11:09:08 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:06:55 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 0c94dc74-c553-412c-a187-108bdb29657e
	RequestId           : 5cc9ddba-0643-4b5e-82b6-287b321394ee
	StatusCode          : OK

Das folgende Video veranschaulicht diese Aufgabe:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="restart"></a>AUFGABE: Neustarten eines virtuellen Computers

Ersetzen Sie im folgenden Befehl *resource group name* durch den Namen der Ressourcengruppe, die den virtuellen Computer enthält, und *VM name* durch den Namen des Computers, und führen Sie den Befehl aus:

	Restart-AzureRmVM -ResourceGroupName "resource group name" -Name "VM name"

Folgendes sollte angezeigt werden:

	EndTime             : 4/28/2015 11:16:26 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:16:25 AM -07:00
	Status              : Succeeded
	TrackingOperationId : 390571e0-c804-43ce-88c5-f98e0feb588e
	RequestId           : 7dac33e3-0164-4a08-be33-96205284cb0b
	StatusCode          : OK

Das folgende Video veranschaulicht diese Aufgabe:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## <a id="delete"></a>AUFGABE: Löschen eines virtuellen Computers

Ersetzen Sie im folgenden Befehl *resource group name* durch den Namen der Ressourcengruppe, die den virtuellen Computer enthält, und *VM name* durch den Namen des Computers, und führen Sie den Befehl aus:

	Remove-AzureRmVM -ResourceGroupName "resource group name" –Name "VM name"

> [AZURE.NOTE]Mit dem Parameter **- Force** können Sie die Bestätigungsaufforderung überspringen.

Wenn Sie den Parameter "-Force" nicht verwendet haben, werden Sie aufgefordert, den Vorgang zu bestätigen:

	Virtual machine removal operation
	This cmdlet will remove the specified virtual machine. Do you want to continue?
	[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"):

Folgendes sollte angezeigt werden:

	EndTime             : 4/28/2015 11:21:55 AM -07:00
	Error               :
	Output              :
	StartTime           : 4/28/2015 11:20:13 AM -07:00
	Status              : Succeeded
	TrackingOperationId : f74fad9e-f6bc-46ae-82b1-bfad3952aa44
	RequestId           : 6a30d2e0-63ca-43cf-975b-058631e048e7
	StatusCode          : OK

Das folgende Video veranschaulicht diese Aufgabe:

[AZURE.VIDEO start-stop-restart-and-delete-vms-in-microsoft-azure-with-powershell]

## Zusätzliche Ressourcen
[Azure-Schnellstartvorlagen](http://azure.microsoft.com/documentation/templates/) und [App-Frameworks](virtual-machines-app-frameworks.md)

[Azure Compute-, Network- und Storage-Anbieter unter dem Azure-Ressourcen-Manager](virtual-machines-azurerm-versus-azuresm.md)

[Übersicht über den Azure-Ressourcen-Manager](resource-group-overview.md)

[Dokumentation zu virtuellen Computern](http://azure.microsoft.com/documentation/services/virtual-machines/)

<!---HONumber=Oct15_HO3-->