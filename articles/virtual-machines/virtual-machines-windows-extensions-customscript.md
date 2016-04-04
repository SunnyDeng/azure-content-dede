<properties
   pageTitle="Benutzerdefinierte Skripts auf virtuellen Windows-Computern unter Verwendung von Vorlagen | Microsoft Azure"
   description="Automatisieren Sie Konfigurationsaufgaben für virtuelle Windows-Computer mithilfe der benutzerdefinierten Skripterweiterung unter Verwendung von Ressourcen-Manager-Vorlagen."
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
   ms.date="11/01/2015"
   ms.author="kundanap"/>

# Verwenden der benutzerdefinierten Skripterweiterung für virtuelle Windows-Computer mit Azure Resource Manager-Vorlagen

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-extensions-customscript.md).

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Vorlagenbeispiel für einen virtuellen Windows-Computer

Definieren Sie die folgende Ressource im "Resource"-Abschnitt der Vorlage.

       {
       "type": "Microsoft.Compute/virtualMachines/extensions",
       "name": "MyCustomScriptExtension",
       "apiVersion": "2015-05-01-preview",
       "location": "[parameters('location')]",
       "dependsOn": [
           "[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"
       ],
       "properties": {
           "publisher": "Microsoft.Compute",
           "type": "CustomScriptExtension",
           "typeHandlerVersion": "1.4",
           "settings": {
               "fileUris": [
               "http://Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
           ],
           "commandToExecute": "powershell.exe -ExecutionPolicy Unrestricted -File start.ps1"
         }
       }
     }
     
Ersetzen Sie im obigen Beispiel die Datei-URL und den Dateinamen durch Ihre eigenen Einstellungen.

Die erstellte Vorlage können Sie anschließend über Azure PowerShell bereitstellen.

Im nachstehenden Beispiel finden Sie ein vollständiges Beispiel zum Konfigurieren von Anwendungen auf einem virtuellen Computer mithilfe der benutzerdefinierten Skripterweiterung.

* [Benutzerdefinierte Skripterweiterung auf einem virtuellen Windows-Computer](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

<!---HONumber=AcomDC_0323_2016-->