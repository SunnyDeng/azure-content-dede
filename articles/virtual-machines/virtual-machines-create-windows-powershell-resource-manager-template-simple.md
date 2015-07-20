<properties
	pageTitle="Erstellen eines virtuellen Windows-Computers mit einer Ressourcen-Manager-Vorlage und PowerShell"
	description="Verwenden Sie eine Ressourcen-Manager-Vorlage und Azure PowerShell, um einen neuen virtuellen Windows-Computer zu erstellen."
	services="virtual-machines"
	documentationCenter=""
	authors="JoeDavies-MSFT"
	manager="timlt"
	editor=""/>

<tags
	ms.service="virtual-machines"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/29/2015"
	ms.author="josephd"/>

# Erstellen eines virtuellen Windows-Computers mit einer Ressourcen-Manager-Vorlage und Azure PowerShell

Sie können ganz einfach einen neuen Windows-basierten virtuellen Computer erstellen, indem Sie eine Ressourcen-Manager-Vorlage und Azure PowerShell verwenden. Von dieser Vorlage wird ein einzelner virtueller Computer unter Windows in einem neuen virtuellen Netzwerk mit einem einzelnen Subnetz in einer neuen Ressourcengruppe erstellt.

![](./media/virtual-machines-create-windows-powershell-resource-manager-template-simple/windowsvm.png)

Bevor es losgeht, befolgen Sie diese Anweisungen, um sicherzustellen, dass Sie Azure, Windows PowerShell und Azure PowerShell konfiguriert und einsatzbereit sind:

[AZURE.INCLUDE [arm-getting-setup-powershell](../../includes/arm-getting-setup-powershell.md)]

## Erstellen eines virtuellen Windows-Computers

Gehen folgendermaßen Sie vor, um einen virtuellen Windows-Computer mit einer Ressourcen-Manager-Vorlage im Github-Vorlagenrepository mit Azure PowerShell zu erstellen.

Führen Sie die Befehle gemäß dem folgenden Beispiel aus, und geben Sie den Azure-Bereitstellungsnamen, den Ressourcengruppenname und den Standort des Azure-Rechenzentrums an. Beachten Sie, dass Sie beim Ausführen des Befehls **New-AzureResourceGroupDeployment** aufgefordert werden, Parameterwerte im Abschnitt "Parameter" der JSON-Datei anzugeben. Wenn Sie alle Parameterwerte angegeben haben, werden die Ressourcengruppe und der virtuelle Computer vom Befehl erstellt.

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/101-simple-windows-vm/azuredeploy.json"
	New-AzureResourceGroup –Name $RGName –Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Folgendes sollte angezeigt werden:

	cmdlet New-AzureResourceGroupDeployment at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	newStorageAccountName: newsaacct
	adminUsername: WinAdmin1
	adminPassword: *********
	dnsNameForPublicIP: contoso
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
	                    newStorageAccountName  String                     newsaacct
	                    adminUsername    String                     WinAdmin1
	                    adminPassword    SecureString
	                    dnsNameForPublicIP  String                     contoso
	                    windowsOSVersion  String                     2012-R2-Datacenter

	Outputs           :

Nun ist ein neuer virtueller Windows-Computer mit dem Namen „MyWindowsVM“ in Ihrer neuen Ressourcengruppe vorhanden.

## Zusätzliche Ressourcen

[Azure Compute-, Netzwerk- und Speicheranbieter unter dem Azure-Ressourcen-Manager](virtual-machines-azurerm-versus-azuresm.md)

[Übersicht über den Azure-Ressourcen-Manager](../resource-group-overview.md)

[Erstellen eines virtuellen Windows-Computers mit dem Azure-Ressourcen-Manager und Azure PowerShell](virtual-machines-create-windows-powershell-resource-manager.md)

[ (Erstellen eines virtuellen Windows-Computer mit PowerShell und Azure Service Manager)](virtual-machines-create-windows-powershell-service-manager.md)

[Dokumentation zu virtuellen Computern](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Installieren und Konfigurieren von Azure PowerShell](../powershell-install-configure.md)

<!---HONumber=July15_HO2-->