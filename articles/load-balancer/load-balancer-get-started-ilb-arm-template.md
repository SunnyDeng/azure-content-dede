<properties 
   pageTitle="Erstellen eines internen Lastenausgleichs im Ressourcen-Manager mithilfe einer Vorlage | Microsoft Azure"
   description="Hier erfahren Sie, wie Sie im Ressourcen-Manager mithilfe einer Vorlage einen internen Lastenausgleich erstellen."
   services="load-balancer"
   documentationCenter="na"
   authors="joaoma"
   manager="carolz"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="load-balancer"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/09/2015"
   ms.author="joaoma" />

# Erste Schritte zum Erstellen eines internen Lastenausgleichs mithilfe einer Vorlage

[AZURE.INCLUDE [load-balancer-get-started-ilb-arm-selectors-include.md](../../includes/load-balancer-get-started-ilb-arm-selectors-include.md)]<BR>[AZURE.INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](load-balancer-get-started-ilb-classic-ps.md).

[AZURE.INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

## Bereitstellen der Vorlage per Klick

In der verfügbaren Beispielvorlage im öffentlichen Repository wird eine Parameterdatei verwendet, die die Standardwerte zum Generieren des oben beschriebenen Szenarios enthält. Öffnen Sie zum Bereitstellen dieser Vorlage per Klick [diesen Link]((https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer), klicken Sie auf **In Azure bereitstellen**, ersetzen Sie ggf. die Werte der Standardparameter, und führen Sie die Schritte im Portal aus.

## Bereitstellen der Vorlage mithilfe von PowerShell

Führen Sie zum Bereitstellen der mithilfe von PowerShell heruntergeladenen Vorlage die unten beschriebenen Schritte aus.

1. Wenn Sie Azure PowerShell noch nie verwendet haben, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md), und befolgen Sie die komplette Anleitung, um sich bei Azure anzumelden und Ihr Abonnement auszuwählen.
2. Führen Sie das Cmdlet **Switch-AzureMode** aus, um zum Ressourcen-Manager-Modus zu wechseln, wie unten dargestellt.

		Switch-AzureMode AzureResourceManager

	Hier ist die erwartete Ausgabe des obigen Befehls:

		WARNING: The Switch-AzureMode cmdlet is deprecated and will be removed in a future release.

>[AZURE.WARNING]Das Cmdlet "Switch-AzureMode" ist demnächst veraltet. In diesem Fall werden alle Ressourcen-Manager-Cmdlets umbenannt.

3\. Laden Sie die [Parameter](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.parameters.json)-Datei herunter und speichern Sie sie auf Ihrem lokalen Datenträger.<BR> 4. Bearbeiten Sie die Datei, und speichern Sie sie.<BR> 5. Führen Sie das Cmdlet **New-AzureResourceGroup** aus, um eine Ressourcengruppe mit der Vorlage zu erstellen.


		New-AzureResourceGroup -Name TestRG -Location westus `
		    -TemplateFile 'https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json' `
		    -TemplateParameterFile 'C:\temp\azuredeploy.parameters.json'
	
                
		
## Bereitstellen der Vorlage mithilfe der Azure-Befehlszeilenschnittstelle

Führen Sie zum Bereitstellen der Vorlage mithilfe der Azure-Befehlszeilenschnittstelle die unten beschriebenen Schritte aus.

1. Wenn Sie die Azure-Befehlszeilenschnittstelle noch nie verwendet haben, ziehen Sie [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](xplat-cli.md) zurate, und folgen Sie den Anweisungen bis zu dem Punkt, an dem Sie Ihr Azure-Konto und Ihr Abonnement auswählen.
2. Führen Sie den Befehl **azure config mode** aus, um in den Ressourcen-Manager-Modus zu wechseln, wie unten dargestellt.

		azure config mode arm

	Hier ist die erwartete Ausgabe des obigen Befehls:

		info:    New mode is arm

3. Öffnen Sie die [Parameterdatei](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.parameters.json), und speichern Sie den Inhalt dieser Datei in einer Datei auf Ihrem Computer. In diesem Beispiel haben wir die Parameterdatei in *parameters.json* gespeichert.

4. Führen Sie das Cmdlet **azure group deployment create** aus, um das neue VNet mithilfe der oben heruntergeladenen und geänderten Vorlage und Parameterdateien bereitzustellen. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert.

		azure group create -n TestRG -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/201-2-vms-internal-load-balancer/azuredeploy.json -e parameters.json



## Nächste Schritte

[Konfigurieren Sie einen Lastenausgleichs-Verteilungsmodus mithilfe der Quell-IP-Affinität](load-balancer-distribution-mode.md)

[Konfigurieren von TCP-Leerlauftimeout-Einstellungen für den Lastenausgleich](load-balancer-tcp-idle-timeout.md)

<!---HONumber=AcomDC_1203_2015-->