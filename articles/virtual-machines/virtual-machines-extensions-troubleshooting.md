<properties
   pageTitle="Problembehandlung für Fehler bei der Azure-VM-Erweiterung | Microsoft Azure"
   description="Erfahren Sie mehr über die Problembehandlung für Fehler bei der Azure-VM-Erweiterung"
   services="virtual-machines"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Problembehandlung für Fehler bei der Azure-VM-Erweiterung.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-include.md)]Dieser Artikel behandelt die Verwendung des Ressourcen-Manager-Bereitstellungsmodells.

## Übersicht über Azure-Ressourcen-Manager-Vorlagen

Eine Azure-Ressourcen-Manager-Vorlage ermöglicht es Ihnen, die Azure IaaS-Infrastruktur deklarativ in der Json-Sprache anzugeben, indem Sie die Abhängigkeiten zwischen Ressourcen definieren.


Klicken Sie auf den Artikel [Erweiterungsvorlagen erstellen](virtual-machines-extensions-authoring-templates.md), um weitere Informationen zum Erstellen von Vorlagen für die Verwendung von Erweiterungen zu erhalten.

In diesem Artikel erfahren Sie mehr über die Problembehandlung für einige der allgemeinen VM-Erweiterungsfehler.

## Anzeigen des Erweiterungs-Status:
Vorlagen für den Azure-Ressourcen-Manager können von Azure Powershell oder der Azure-Befehlszeilenschnittstelle ausgeführt werden. Sobald die Vorlage ausgeführt wird, kann der Erweiterungsstatus im Azure-Ressourcen-Explorer oder in den Befehlszeilentools angezeigt werden. Hier einige Beispiele:

Azure-Befehlszeilenschnittstelle:

      azure vm get-instance-view

Azure PowerShell:

      Get-AzureVM -ResourceGroupName $RGName -Name $vmName -Status

Hier ist die Beispielausgabe:

      Extensions:  {
      "ExtensionType": "Microsoft.Compute.CustomScriptExtension",
      "Name": "myCustomScriptExtension",
      "SubStatuses": [
        {
          "Code": "ComponentStatus/StdOut/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "    Directory: C:\\temp\\n\\n\\nMode                LastWriteTime     Length Name
              \\n----                -------------     ------ ----                              \\n-a---          9/1/2015   2:03 AM         11
              test.txt                          \\n\\n",
                      "Time": null
          },
        {
          "Code": "ComponentStatus/StdErr/succeeded",
          "DisplayStatus": "Provisioning succeeded",
          "Level": "Info",
          "Message": "",
          "Time": null
        }
    }
  ]

## Problembehandlung bei Erweiterungsfehlern:

### Erneutes Ausführen der Erweiterung auf dem virtuellen Computer:

Wenn Sie mithilfe der CustomScript-Erweiterung Skripts auf dem virtuellen Computer ausführen, könnten ab und an Fehler auftreten, bei denen der virtuelle Computer erfolgreich erstellt wurde, das Skript jedoch fehlgeschlagen ist. Unter diesen Bedingungen ist die empfohlene Vorgehensweise zum Beheben dieses Fehlers das Entfernen der Erweiterung und das erneute Ausführen der Vorlage. Hinweis: In Zukunft wird diese Funktionalität verbessert, damit es nicht mehr notwendig ist, die Erweiterung zu deinstallieren.

#### Entfernen der Erweiterung aus der Azure-Befehlszeilenschnittstelle:

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

Wobei "publsher-name" dem Erweiterungstyp aus der Ausgabe von "azure vm get-instance-view" entspricht und der Name der Name der Erweiterungsressource aus der Vorlage ist.

#### Entfernen der Erweiterung aus Azure PowerShell:

    Remove-AzureVMExtension -ResourceGroupName $RGName -VMName $vmName -Name "myCustomScriptExtension"

Nachdem die Erweiterung entfernt wurde, kann die Vorlage erneut ausgeführt werden, um die Skripts auf dem virtuellen Computer auszuführen.

<!---HONumber=Oct15_HO1-->