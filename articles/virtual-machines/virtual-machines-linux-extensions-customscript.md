<properties
   pageTitle="Benutzerdefinierte Skripts auf virtuellen Linux-Computern unter Verwendung von Vorlagen | Microsoft Azure"
   description="Automatisieren Sie Konfigurationsaufgaben für virtuelle Linux-Computer mithilfe der benutzerdefinierten Skripterweiterung unter Verwendung von Ressourcen-Manager-Vorlagen."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="11/01/2015"
   ms.author="kundanap"/>

# Verwenden der benutzerdefinierten Skripterweiterung für virtuelle Linux-Computer mit Azure Resource Manager-Vorlagen

Dieser Artikel bietet einen Überblick über das Schreiben von Azure Resource Manager-Vorlagen mit der benutzerdefinierten Skripterweiterung zum Starten (Bootstrappen) von Workloads auf einem virtuellen Linux-Computer.


[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-windows-classic-extensions-customscript.md).

[AZURE.INCLUDE [virtual-machines-common-extensions-customscript](../../includes/virtual-machines-common-extensions-customscript.md)]

## Vorlagenbeispiel für einen virtuellen Linux-Computer

Definieren Sie die folgende Erweiterungsressource im "Resource"-Abschnitt der Vorlage.

      {
    "type": "Microsoft.Compute/virtualMachines/extensions",
    "name": "MyCustomScriptExtension",
    "apiVersion": "2015-05-01-preview",
    "location": "[parameters('location')]",
    "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
    "properties":
    {
      "publisher": "Microsoft.OSTCExtensions",
      "type": "CustomScriptForLinux",
      "typeHandlerVersion": "1.2",
      "settings": {
      "fileUris": [ "https: //raw.githubusercontent.com/Azure/azure-quickstart-templates/master/mongodb-on-ubuntu/mongo-install-ubuntu.sh                        ],
      "commandToExecute": "shmongo-install-ubuntu.sh"
      }
    }
    }
    
Ersetzen Sie im obigen Beispiel die Datei-URL und den Dateinamen durch Ihre eigenen Einstellungen.

Die erstellte Vorlage können Sie anschließend über die Azure-Befehlszeilenschnittstelle bereitstellen.

Im nachstehenden Beispiel finden Sie ein vollständiges Beispiel zum Konfigurieren von Anwendungen auf einem virtuellen Computer mithilfe der benutzerdefinierten Skripterweiterung.

* [Benutzerdefinierte Skripterweiterung auf einem virtuellen Linux-Computer](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

<!---HONumber=AcomDC_0323_2016-->