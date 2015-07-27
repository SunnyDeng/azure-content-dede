<properties 
	pageTitle="Bereitstellen eines WordPress-Servers auf Ubuntu mit einer Azure-Ressourcen-Manager-Vorlage" 
	description="Mit einer Ressourcen-Manager-Vorlage und dem Azure-Vorschauportal, Azure PowerShell oder der Azure-Befehlszeilenschnittstelle können Sie ganz einfach einen einzelnen WordPress-Server mit Ubuntu bereitstellen." 
	services="virtual-machines" 
	documentationCenter="" 
	authors="JoeDavies-MSFT" 
	manager="timlt" 
	editor=""
	tags="azure-resource-manager"/>

<tags 
	ms.service="virtual-machines" 
	ms.workload="infrastructure-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/29/2015" 
	ms.author="josephd"/>

# Bereitstellen eines WordPress-Servers auf Ubuntu mit einer Azure-Ressourcen-Manager-Vorlage

Folgen Sie den Anweisungen in diesem Artikel, um mithilfe einer Ressourcen-Manager-Vorlage einen WordPress-Server unter Ubuntu bereitzustellen. Mit dieser Vorlage wird ein einzelner virtueller Computer in einem neuen virtuellen Netzwerk erstellt.

![](./media/virtual-machines-workload-template-wordpress/one-server-wordpress.png)

Sie können die Vorlage mit dem Azure-Vorschauportal, mit Azure PowerShell oder der Azure-Befehlszeilenschnittstelle ausführen.
 
## Azure-Vorschauportal

Wenn Sie diesen Workload mit einer Ressourcen-Manager-Vorlage und dem Azure-Vorschauportal bereitstellen möchten, klicken Sie [hier](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2Fwordpress-single-vm-ubuntu%2Fazuredeploy.json).

![](./media/virtual-machines-workload-template-wordpress/azure-portal-template.png)
 
1.	Klicken Sie im Bereich **Vorlage** auf **Speichern**.
2.	Klicken Sie auf **Parameter**. Geben Sie im Bereich **Parameter** neue Werte ein, wählen Sie aus den zulässigen Werten aus, oder übernehmen Sie die Standardwerte. Klicken Sie dann auf **OK**.
3.	Klicken Sie bei Bedarf auf **Abonnement**, und wählen Sie das richtige Azure-Abonnement aus.
4.	Klicken Sie auf **Ressourcengruppe**, und wählen Sie eine vorhandene Ressourcengruppe aus. Sie können auch auf **Oder neu erstellen** klicken, um einen neuen Trigger für diesen Workload zu erstellen.
5.	Sofern erforderlich, klicken Sie auf **Ressourcengruppenstandort**, und wählen Sie den richtigen Azure-Standort aus.
6.	Klicken Sie ggf. auf "Rechtliche Bedingungen", um die Nutzungsbedingungen für die Vorlage zu lesen.
7.	Klicken Sie auf **Erstellen**.

Je nach Vorlage kann es einige Zeit dauern, bis der Workload in Azure erstellt ist. Nach Abschluss dieses Vorgangs verfügen Sie über einen neuen WordPress-Server, der in der vorhandenen oder neuen Ressourcengruppe unter Ubuntu ausgeführt wird.

## Azure PowerShell

Stellen Sie zunächst sicher, dass die richtige Version von Azure PowerShell installiert ist, dass Sie angemeldet sind und zum neuen Ressourcen-Manager-Modus gewechselt sind. Nähere Informationen finden Sie [hier](virtual-machines-deploy-rmtemplates-powershell.md#setting-up-powershell-for-resource-manager-templates).

Geben Sie einen Namen für die Azure-Bereitstellung, einen Namen für die neue Ressourcengruppe und den Standort eines Azure-Rechenzentrums in die folgenden Befehle ein. Ersetzen Sie alles innerhalb der Anführungszeichen, einschließlich der Zeichen < and >.

	$deployName="<deployment name>"
	$RGName="<resource group name>"
	$locName="<Azure location, such as West US>"
	$templateURI="https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/wordpress-single-vm-ubuntu/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Beispiel:

	$deployName="TestDeployment"
	$RGName="TestRG"
	$locname="West US"
	$templateURI="https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/wordpress-single-vm-ubuntu/azuredeploy.json"
	New-AzureResourceGroup -Name $RGName -Location $locName
	New-AzureResourceGroupDeployment -Name $deployName -ResourceGroupName $RGName -TemplateUri $templateURI

Führen Sie anschließend den Befehlsblock in der Azure-PowerShell-Eingabeaufforderung aus.

Beim Ausführen des Befehls **New-AzureResourceGroupDeployment** werden Sie aufgefordert, die Werte für eine Reihe von Parametern anzugeben. Sobald Sie alle Parameterwerte angegeben haben, werden die virtuellen Computer von **New-AzureResourceGroupDeployment** erstellt und konfiguriert.

Wenn die Vorlagenausführung abgeschlossen ist, verfügen Sie über einen neuen WordPress-Server, der in der neuen Ressourcengruppe unter Ubuntu ausgeführt wird.

## Azure-Befehlszeilenschnittstelle

Stellen Sie zunächst sicher, dass die richtige Version von Azure-Befehlszeilenschnittstelle installiert ist, dass Sie angemeldet sind und zum neuen Ressourcen-Manager-Modus gewechselt sind. Nähere Informationen finden Sie [hier](virtual-machines-deploy-rmtemplates-azure-cli.md#getting-ready).

Erstellen Sie zunächst eine neue Ressourcengruppe. Geben Sie mit dem folgenden Befehl den Namen der Gruppe und den Standort des Azure-Rechenzentrums für die Bereitstellung an.

	azure group create <group name> <location>

Als Nächstes verwenden Sie den folgenden Befehl, um den Namen der neuen Ressourcengruppe und den Namen einer Azure-Bereitstellung anzugeben.

	azure group deployment create --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/wordpress-single-vm-ubuntu/azuredeploy.json <group name> <deployment name>

Beispiel:

	azure group create wordpress eastus2
	azure group deployment create --template-uri https://raw.githubusercontent.com/azure/azure-quickstart-templates/master/wordpress-single-vm-ubuntu/azuredeploy.json wordpress wpdevtest

Beim Ausführen des Befehls **azure group deployment create** werden Sie aufgefordert, die Werte für eine Reihe von Parametern anzugeben. Sobald Sie alle Parameterwerte angegeben haben, werden die virtuellen Computer von Azure erstellt und konfiguriert.

Wenn die Vorlagenausführung abgeschlossen ist, verfügen Sie über einen neuen WordPress-Server, der in der neuen Ressourcengruppe unter Ubuntu ausgeführt wird.

## Zusätzliche Ressourcen

[Bereitstellen und Verwalten von virtuellen Computern mit Azure-Ressourcen-Manager-Vorlagen und PowerShell](virtual-machines-deploy-rmtemplates-powershell.md)

[Azure Compute-, Network- and Storage-Anbieter unter dem Azure-Ressourcen-Manager](virtual-machines-azurerm-versus-azuresm.md)

[Übersicht über den Azure Resource Manager](../resource-group-overview.md)

[Bereitstellen und Verwalten von virtuellen Computern mit Azure Resource Manager-Vorlagen und der Azure-CLI](virtual-machines-deploy-rmtemplates-azure-cli.md)

[Dokumentation zu virtuellen Computern](http://azure.microsoft.com/documentation/services/virtual-machines/)

[Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md)

<!---HONumber=July15_HO2-->