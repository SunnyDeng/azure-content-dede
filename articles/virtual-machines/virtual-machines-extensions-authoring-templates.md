<properties
   pageTitle="Erstellen von Vorlagen mit Azure-VM-Erweiterungen | Microsoft Azure"
   description="Weitere Informationen zum Erstellen von Vorlagen mit Erweiterungen"
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
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Erstellen von Azure-Ressourcen-Manager-Vorlagen mit VM-Erweiterungen

## Übersicht über Azure-Ressourcen-Manager-Vorlagen

Eine Azure-Ressourcen-Manager-Vorlage ermöglicht es Ihnen, die Azure IaaS-Infrastruktur deklarativ in der Json-Sprache anzugeben, indem Sie die Abhängigkeiten zwischen Ressourcen definieren. Eine ausführliche Übersicht über Azure-Ressourcen-Manager-Vorlagen finden Sie in den folgenden Artikeln:

[Übersicht über Ressourcengruppen](../resource-group-overview.md)

## Ausschnitt einer Beispielvorlage für VM-Erweiterungen.
Bereitstellen der VM-Erweiterung als Teil der Azure-Ressourcen-Manager-Vorlage erfordert das deklarative Festlegen der Konfiguration für die Erweiterung in der Vorlage. Dies ist das Format für die Angabe der Konfiguration für die Erweiterung.

      {
      "type": "Microsoft.Compute/virtualMachines/extensions",
      "name": "MyExtension",
      "apiVersion": "2015-05-01-preview",
      "location": "[parameters('location')]",
      "dependsOn": ["[concat('Microsoft.Compute/virtualMachines/',parameters('vmName'))]"],
      "properties":
      {
      "publisher": "Publisher Namespace",
      "type": "extension Name",
      "typeHandlerVersion": "extension version",
      "settings": {
      // Extension specific configuration goes in here.
      }
      }
      }

Wie Sie oben sehen können, enthält die Erweiterungsvorlage zwei Hauptabschnitte:

1. Name der Erweiterung, Herausgeber und Version.
2. Konfiguration der Erweiterung.

## Identifizieren des Herausgebers, des Typs und des typeHandlerVersion-Elements für eine beliebige Erweiterung

Azure VM-Erweiterungen werden von Microsoft und vertrauenswürdigen Drittanbietern veröffentlicht. Jede Erweiterung wird eindeutig durch Herausgeber, Typ und typeHandlerVersion-Element identifiziert. Diese können wie folgt bestimmt werden:

Führen Sie über Azure PowerShell das folgende Azure PowerShell-Cmdlet aus:

      Get-AzureVMAvailableExtension

Führen Sie über die Azure-Befehlszeilenschnittstelle das folgende Azure PowerShell-Cmdlet aus:

      Azure VM Extension list

Dieses Cmdlet gibt den Herausgebernamen, den Erweiterungsnamen und die Version zurück:

       Publisher                   : Microsoft.Azure.Extensions  
      ExtensionName               : DockerExtension
      Version                     : 1.0

Diese drei Eigenschaften sind im oben dargestellten Vorlagenausschnitt „publisher“, „type“ und „typeHandlerVersion“ zugeordnet. Hinweis: Es wird empfohlen, die neueste Erweiterungsversion zu verwenden, um aktuellste Funktionen zu erhalten.

## Identifizieren des Schemas für die Erweiterungskonfigurationsparameter

Im nächsten Schritt bei der Erstellung der Erweiterungsvorlage wird das Format für die Bereitstellung von Konfigurationsparametern festgelegt. Jede Erweiterung unterstützt einen eigenen Satz von Parametern.

Um eine Beispielkonfiguration für Windows-Erweiterungen anzuzeigen, klicken Sie auf die Dokumentation [Beispiele für Windows-Erweiterungen](virtual-machines-extensions-configuration-samples-windows.md).

Um eine Beispielkonfiguration für Linux-Erweiterungen anzuzeigen, klicken Sie auf die Dokumentation [Beispiele für Linux-Erweiterungen](virtual-machines-extensions-configuration-samples-linux.md).

Eine vollständige Vorlage mit VM-Erweiterungen finden Sie in der folgenden Dokumentation zu Vorlagen für virtuelle Computer.

[Benutzerdefinierte Skripterweiterung auf einem virtuellen Windows-Computer](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/201-list-storage-keys-windows-vm/azuredeploy.json/)

[Benutzerdefinierte Skriptserweiterung auf einem virtuellen Linux-Computer](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

Nachdem Sie die Vorlage geschrieben haben, können Sie sie über die Azure-Befehlszeilenschnittstelle oder über Azure Powershell bereitstellen.

<!---HONumber=Sept15_HO2-->