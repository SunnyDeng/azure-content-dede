<properties
   pageTitle="Problembehandlung bei Fehlern im Zusammenhang mit Erweiterungen für virtuelle Linux-Computer | Microsoft Azure"
   description="Erfahren Sie mehr über die Problembehandlung für Fehler bei Azure-Erweiterungen für virtuelle Linux-Computer."
   services="virtual-machines-linux"
   documentationCenter=""
   authors="kundanap"
   manager="timlt"
   editor=""
   tags="top-support-issue,azure-resource-manager"/>

<tags
   ms.service="virtual-machines-linux"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-linux"
   ms.workload="infrastructure-services"
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Problembehandlung für Fehler bei Azure-Erweiterungen für virtuelle Linux-Computer

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.

[AZURE.INCLUDE [virtual-machines-common-extensions-troubleshoot](../../includes/virtual-machines-common-extensions-troubleshoot.md)]

## Anzeigen des Erweiterungsstatus
Vorlagen für den Azure Resource Manager können von der Azure-Befehlszeilenschnittstelle ausgeführt werden. Sobald die Vorlage ausgeführt wird, kann der Erweiterungsstatus im Azure-Ressourcen-Explorer oder in den Befehlszeilentools angezeigt werden.

Beispiel:

Azure-Befehlszeilenschnittstelle:

      azure vm get-instance-view


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

### Erneutes Ausführen der Erweiterung auf dem virtuellen Computer

Wenn Sie mithilfe der benutzerdefinierten Skripterweiterung Skripts auf dem virtuellen Computer ausführen, könnten ab und an Fehler auftreten, bei denen der virtuelle Computer erfolgreich erstellt wurde, das Skript jedoch fehlgeschlagen ist. Unter diesen Bedingungen ist die empfohlene Vorgehensweise zum Beheben dieses Fehlers das Entfernen der Erweiterung und das erneute Ausführen der Vorlage. Hinweis: In Zukunft wird diese Funktionalität verbessert, damit es nicht mehr notwendig ist, die Erweiterung zu deinstallieren.

#### Entfernen der Erweiterung aus der Azure-Befehlszeilenschnittstelle

      azure vm extension set --resource-group "KPRG1" --vm-name "kundanapdemo" --publisher-name "Microsoft.Compute.CustomScriptExtension" --name "myCustomScriptExtension" --version 1.4 --uninstall

Wobei "publsher-name" dem Erweiterungstyp aus der Ausgabe von "azure vm get-instance-view" entspricht und der Name der Name der Erweiterungsressource aus der Vorlage ist.

Nachdem die Erweiterung entfernt wurde, kann die Vorlage erneut ausgeführt werden, um die Skripts auf dem virtuellen Computer auszuführen.

<!---HONumber=AcomDC_0323_2016-->