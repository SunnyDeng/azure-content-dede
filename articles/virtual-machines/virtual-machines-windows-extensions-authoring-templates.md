<properties
   pageTitle="Erstellen von Vorlagen mit Erweiterungen für virtuelle Windows-Computer | Microsoft Azure"
   description="Hier finden Sie Informationen zum Erstellen von Azure Resource Manager-Vorlagen mit Erweiterungen für virtuelle Windows-Computer."
   services="virtual-machines-windows"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-windows"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-windows"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Erstellen von Azure Resource Manager-Vorlagen mit Erweiterungen für virtuelle Windows-Computer

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.

[AZURE.INCLUDE [virtual-machines-common-extensions-authoring-templates](../../includes/virtual-machines-common-extensions-authoring-templates.md)]

Führen Sie über Azure PowerShell das folgende Azure PowerShell-Cmdlet aus:

      Get-AzureVMAvailableExtension


Dieses Cmdlet gibt den Herausgebernamen, den Erweiterungsnamen und die Version folgendermaßen zurück:

      Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Diese drei Eigenschaften sind im oben dargestellten Vorlagenausschnitt „publisher“, „type“ und „typeHandlerVersion“ zugeordnet.

>[AZURE.NOTE]Es empfiehlt sich, die aktuelle Erweiterungsversion zu verwenden, um die neuesten Funktionen zu erhalten.

## Identifizieren des Schemas für die Erweiterungskonfigurationsparameter

Im nächsten Schritt zur Erstellung einer Erweiterungsvorlage wird das Format für die Bereitstellung von Konfigurationsparametern festgelegt. Jede Erweiterung unterstützt einen eigenen Satz von Parametern.

Informationen zum Anzeigen einer Beispielkonfiguration für Windows-Erweiterungen finden Sie unter [Beispiele für Windows-Erweiterungen](virtual-machines-windows-extensions-configuration-samples.md).


Eine vollständige Vorlage mit Erweiterungen für virtuelle Computer finden Sie hier:

[Benutzerdefinierte Skripterweiterung auf einem virtuellen Windows-Computer](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)


Die erstellte Vorlage können Sie anschließend über Azure PowerShell bereitstellen.

<!---HONumber=AcomDC_0323_2016-->