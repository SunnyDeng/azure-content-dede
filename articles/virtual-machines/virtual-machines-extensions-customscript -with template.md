<properties
   pageTitle="Verwenden der CustomScript-Erweiterung mit Azure-Ressourcen-Manager-Vorlagen"
	description="Automatisieren von Konfigurationsaufgaben für einen virtuellen Azure-Computer mithilfe der CustomScript-Erweiterung mit ARM-Vorlagen"
	services="virtual-machines"
	documentationCenter=""
	authors="kundanap"
	manager="timlt"
	editor=""/>

<tags
   ms.service="virtual-machines"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="infrastructure-services"
	ms.date="07/01/2015"
	ms.author="kundanap"/>

# Verwenden der CustomScript-Erweiterung mit Azure-Ressourcen-Manager-Vorlagen

Dieser Artikel bietet einen Überblick über das Schreiben von Azure-Ressourcen-Manager-Vorlagen mit der CustomScript-Erweiterung zum Starten (Bootstrappen) von Arbeitsauslastungen auf einem virtuellen Linux- oder Windows-Computer.

Eine Übersicht über die CustomScript-Erweiterung finden Sie in dem Artikel <a href="https://azure.microsoft.com/de-de/documentation/articles/virtual-machines-extensions-customscript/" target="_blank">hier</a>.

Seit der Einführung der CustomScript-Erweiterung wird diese häufig verwendet, um Arbeitsauslastungen auf Windows und Linux-Computern zu konfigurieren. Seit der Einführung von Azure-Ressourcen-Manager-Vorlagen können Benutzer nun eine einzelne Vorlage erstellen, die nicht nur den virtuellen Computer bereitstellt, sondern auch die Arbeitsauslastung auf ihm konfiguriert.

## Übersicht über Azure-Ressourcen-Manager-Vorlagen

Eine Azure-Ressourcen-Manager-Vorlage ermöglicht es Ihnen, deklarativ die Azure IaaS-Infrastruktur in der Json-Sprache anzugeben, indem Sie die Abhängigkeiten zwischen Ressourcen definieren. Eine ausführliche Übersicht über Azure-Ressourcen-Manager-Vorlagen finden Sie in den folgenden Artikeln:

<a href="https://azure.microsoft.com/de-de/documentation/articles/resource-group-overview/" target="_blank">Übersicht über Azure Resource Manager</a> <br/> <a href="https://azure.microsoft.com/de-de/documentation/articles/virtual-machines-deploy-rmtemplates-azure-cli/" target="_blank">Bereitstellen und Verwalten von virtuellen Computern mit Azure Resource Manager-Vorlagen und der Azure-CLI</a> <br/> <a href="https://azure.microsoft.com/de-de/documentation/articles/virtual-machines-deploy-rmtemplates-powershell/" target="_blank">Bereitstellen und Verwalten von virtuellen Computern mit Azure Resource Manager-Vorlagen und PowerShell</a>

### Voraussetzungen für das Ausführen der CustomScript-Erweiterung

1. Installieren Sie die neuesten Azure PowerShell-Cmdlets oder Azure-CLI von <a href="http://azure.microsoft.com/downloads" target="_blank">hier</a>.
2. Wenn die Skripts auf einer vorhandenen VM ausgeführt werden, müssen Sie sicherstellen, dass der VM-Agent auf der VM aktiviert ist. Andernfalls folgen Sie den Anweisungen in <a href="https://msdn.microsoft.com/library/azure/dn832621.aspx" target="_blank">diesem Artikel</a>, um einen Agent zu installieren.
3. Laden Sie die Skripts, die Sie auf der VM ausführen möchten, nach Azure Storage hoch. Die Skripts können aus einem einzelnen oder mehreren Speichercontainern stammen.
4. Alternativ können die Skripts in ein Github-Konto hochgeladen werden.
5. Das Skript sollte so geschrieben sein, dass das Eingangsskript, das durch die Erweiterung gestartet wird, weitere Skripts startet.

## Übersicht über ein Verwenden der CustomScript-Erweiterung mit Vorlagen

Zum Bereitstellen mit Vorlagen wird genau die Version der CustomScript-Erweiterung verwendet, die für Azure-Service-Verwaltungs-APIs verfügbar ist. Die Erweiterung unterstützt dieselben Parameter und Szenarios, etwa Hochladen von Dateien in ein Azure Storage-Konto oder in einen Github-Speicherort. Der wesentliche Unterschied bei einer Verwendung von Vorlagen besteht darin, dass die genaue Version der Erweiterung angegeben werden muss im Gegensatz zur Angabe der Version im Format Hauptversion.*.

 Vorlagenausschnitt für die CustomScript-Erweiterung auf einem virtuellen Linux-Computer

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

## Vorlagenausschnitt für die CustomScript-Erweiterung auf einem virtuellen Windows-Computer

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

In den nachstehenden Beispielen finden Sie vollständige Beispiele zum Konfigurieren von Anwendungen auf einem virtuellen Computer mithilfe der CustomScript-Erweiterung.

<a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/" target="_blank">CustomScript-Erweiterung auf einem virtuellen Linux-Computer</a>. </br> <a href="https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/" target="_blank">CustomScript-Erweiterung auf einem virtuellen Windows-Computer</a>.

<!---HONumber=August15_HO9-->