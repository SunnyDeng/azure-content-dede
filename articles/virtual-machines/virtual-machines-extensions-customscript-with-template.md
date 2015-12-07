<properties
   pageTitle="Benutzerdefinierte Skripts auf virtuellen Computern unter Verwendung von Vorlagen | Microsoft Azure"
   description="Automatisieren Sie Konfigurationsaufgaben für virtuelle Computer unter Windows und Linux Azure mithilfe der benutzerdefinierten Skripterweiterung unter Verwendung von Ressourcen-Manager-Vorlagen."
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure-services"
   ms.date="11/01/2015"
   ms.author="kundanap"/>

# Verwenden der benutzerdefinierten Skripterweiterung mit Azure-Ressourcen-Manager-Vorlagen

Dieser Artikel bietet einen Überblick über das Schreiben von Azure-Ressourcen-Manager-Vorlagen mit der benutzerdefinierten Skripterweiterung zum Starten (Bootstrappen) von Workloads auf einem virtuellen Linux- oder Windows-Computer.

Eine Übersicht über die benutzerdefinierte Skripterweiterung finden Sie in dem Artikel [hier](virtual-machines-extensions-customscript.md).

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)] [classic deployment model](virtual-machines-extensions-customscript.md).

Seit der Einführung der benutzerdefinierten Skripterweiterung wird diese häufig verwendet, um Workloads auf Windows und Linux-Computern zu konfigurieren. Seit der Einführung von Azure-Ressourcen-Manager-Vorlagen können Benutzer nun eine einzelne Vorlage erstellen, die nicht nur den virtuellen Computer bereitstellt, sondern auch den Workload auf ihm konfiguriert.

## Übersicht über Azure-Ressourcen-Manager-Vorlagen

Eine Azure-Ressourcen-Manager-Vorlage ermöglicht es Ihnen, deklarativ die Azure IaaS-Infrastruktur in der Json-Sprache anzugeben, indem Sie die Abhängigkeiten zwischen Ressourcen definieren. Eine ausführliche Übersicht über Azure-Ressourcen-Manager-Vorlagen finden Sie in den folgenden Artikeln:

- [Übersicht über Ressourcengruppen](../resource-group-overview)
- [Bereitstellen von Vorlagen mit der Azure-Befehlszeilenschnittstelle](virtual-machines-deploy-rmtemplates-azure-cli)
- [Bereitstellen von Vorlagen mit Azure PowerShell](virtual-machines-deploy-rmtemplates-powershell)

### Voraussetzungen für das Ausführen der benutzerdefinierten Skripterweiterung

1. Installieren Sie die neuesten Azure PowerShell-Cmdlets oder die Azure-Befehlszeilenschnittstelle von [hier](http://azure.microsoft.com/downloads).
2. Wenn die Skripts auf einem vorhandenen virtuellen Computer ausgeführt werden, müssen Sie sicherstellen, dass der VM-Agent auf diesem aktiviert ist. Andernfalls folgen Sie [diesen Anweisungen](virtual-machines-extensions-install), um einen Agent zu installieren.
3. Laden Sie die Skripts, die Sie auf der VM ausführen möchten, nach Azure Storage hoch. Die Skripts können aus einem einzelnen oder mehreren Speichercontainern stammen.
4. Alternativ können die Skripts in ein Github-Konto hochgeladen werden.
5. Das Skript sollte so geschrieben sein, dass das Eingangsskript, das durch die Erweiterung gestartet wird, weitere Skripts startet.

## Übersicht über das Verwenden der benutzerdefinierten Skripterweiterung mit Vorlagen

Zum Bereitstellen mit Vorlagen wird genau die Version der CustomScript-Erweiterung verwendet, die für Azure-Service-Verwaltungs-APIs verfügbar ist. Die Erweiterung unterstützt dieselben Parameter und Szenarios, etwa Hochladen von Dateien in ein Azure Storage-Konto oder in einen Github-Speicherort. Der wesentliche Unterschied bei einer Verwendung von Vorlagen besteht darin, dass die genaue Version der Erweiterung angegeben werden muss im Gegensatz zur Angabe der Version im Format Hauptversion.*.

 Vorlagenausschnitt für die benutzerdefinierte Skripterweiterung auf einem virtuellen Linux-Computer

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

## Vorlagenausschnitt für die benutzerdefinierte Skripterweiterung auf einem virtuellen Windows-Computer

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

Ersetzen Sie in den obigen Beispielen die Datei-URL und den Dateinamen durch Ihre eigenen Einstellungen.

Nachdem Sie die Vorlage geschrieben haben, können Sie sie über die Azure-CLI oder über Azure Powershell bereitstellen.

In den nachstehenden Beispielen finden Sie vollständige Beispiele zum Konfigurieren von Anwendungen auf einem virtuellen Computer mithilfe der benutzerdefinierten Skripterweiterung.

<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/" target="_blank">Benutzerdefinierte Skripterweiterung auf einem virtuellen Linux-Computer</a> </br> <a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/" target="_blank">Benutzerdefinierte Skripterweiterung auf einem virtuellen Windows-Computer</a>

<!---HONumber=AcomDC_1125_2015-->