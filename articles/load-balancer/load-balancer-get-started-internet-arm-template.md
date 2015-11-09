<properties 
   pageTitle="Erstellen eines Lastenausgleichsmoduls mit Internetzugriff in Ressourcen-Manager mithilfe eine Vorlage | Microsoft Azure"
   description="Erfahren Sie, wie Sie ein Lastenausgleichsmodul mit Internetzugriff in Ressourcen-Manager mithilfe einer Vorlage erstellen"
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="10/21/2015"
   ms.author="joaoma" />

#Erstellen eines Lastenausgleichsmoduls mit Internetzugriff mittels einer Vorlage

[AZURE.INCLUDE [load-balancer-get-started-internet-arm-selectors-include.md](../../includes/load-balancer-get-started-internet-arm-selectors-include.md)]

[AZURE.INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]Dieser Artikel gilt für das Ressourcen-Manager-Bereitstellungsmodell.

[AZURE.INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## Bereitstellen der ARM-Vorlage per Klick

In der verfügbaren Beispielvorlage im öffentlichen Repository wird eine Parameterdatei verwendet, die die Standardwerte zum Generieren des oben beschriebenen Szenarios enthält. Klicken Sie zum Bereitstellen dieser Vorlage, öffnen Sie [diesen Link](http://go.microsoft.com/fwlink/?LinkId=544801), klicken Sie auf **In Azure bereitstellen**, ersetzen Sie ggf. die Werte der Standardparameter, und führen Sie die Schritte im Portal aus.

## Bereitstellen der ARM-Vorlage mithilfe von PowerShell

Führen Sie zum Bereitstellen der mithilfe von PowerShell heruntergeladenen ARM-Vorlage die folgenden Schritte aus.

1. Wenn Sie Azure PowerShell noch nie verwendet haben, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md), und befolgen Sie die komplette Anleitung, um sich bei Azure anzumelden und Ihr Abonnement auszuwählen.
2. Führen Sie das Cmdlet **Switch-AzureMode** aus, um zum Ressourcen-Manager-Modus zu wechseln, wie unten dargestellt.

		Switch-AzureMode AzureResourceManager

	Hier ist die erwartete Ausgabe des obigen Befehls:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

	>[AZURE.WARNING]Das Cmdlet "Switch-AzureMode" ist demnächst veraltet. In diesem Fall werden alle Ressourcen-Manager-Cmdlets umbenannt.

3. Führen Sie das Cmdlet **New-AzureResourceGroup** aus, um eine Ressourcengruppe mit der Vorlage zu erstellen.

		New-AzureResourceGroup -Name TestRG -Location uswest `
		    -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-natrules/azuredeploy.json' `
		    -TemplateParameterFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-natrules/azuredeploy.parameters.json'	

## Bereitstellen der ARM-Vorlage mithilfe der Azure-Befehlszeilenschnittstelle

Führen Sie zum Bereitstellen der ARM-Vorlage mithilfe der Azure-Befehlszeilenschnittstelle die unten beschriebenen Schritte aus.

1. Wenn Sie die Azure-Befehlszeilenschnittstelle noch nie verwendet haben, ziehen Sie [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](xplat-cli.md) zurate, und folgen Sie den Anweisungen bis zu dem Punkt, an dem Sie Ihr Azure-Konto und Ihr Abonnement auswählen.
2. Führen Sie den Befehl **azure config mode** aus, um in den Ressourcen-Manager-Modus zu wechseln, wie unten dargestellt.

		azure config mode arm

	Hier ist die erwartete Ausgabe des obigen Befehls:

		info:    New mode is arm

3. Navigieren Sie im Browser zu ****https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-natrules/azuredeploy.parameters.json**, kopieren Sie den Inhalt der JSON-Datei, und fügen Sie ihn in eine neue Datei auf Ihrem Computer ein. Für dieses Szenario kopieren Sie die unten angegebenen Werte in eine Datei mit dem Namen **c:\\udr\\azuredeploy.parameters.json**.
4. Führen Sie das Cmdlet **azure group deployment create** aus, um das neue Lastenausgleichsmodul mithilfe der oben heruntergeladenen und geänderten Vorlage und Parameterdateien bereitzustellen. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert.

		azure group create -n TestRG -l westus -f 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-loadbalancer-natrules/azuredeploy.json' -e 'c:\lb\azuredeploy.parameters.json'

<!---HONumber=Nov15_HO1-->