<properties
   pageTitle="Beispielkonfiguration für Linux-VM-Erweiterungen | Microsoft Azure"
   description="Beispielkonfiguration für das Erstellen von Vorlagen mit Erweiterungen für Linux-VMs"
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
   ms.date="09/01/2015"
   ms.author="kundanap"/>

# Konfigurationsbeispiele für Linux-VM-Erweiterungen

> [AZURE.SELECTOR]
- [PowerShell – Vorlage](virtual-machines-windows-extensions-configuration-samples.md)
- [Befehlszeilenschnittstelle – Vorlage](virtual-machines-linux-extensions-configuration-samples.md)

<br>


Dieser Artikel enthält Beispielkonfigurationen der Azure-VM-Erweiterungen für virtuelle Linux-Computer.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.


Weitere Informationen zu diesen Erweiterungen finden Sie unter [Azure-VM-Erweiterungen](virtual-machines-windows-extensions-features.md).

Weitere Informationen zum Erstellen von Erweiterungsvorlagen finden Sie unter [Erstellen von Erweiterungsvorlagen](virtual-machines-windows-extensions-authoring-templates.md).

Dieser Artikel führt die erwarteten Konfigurationswerte für einige der Linux-Erweiterungen auf.

## Ausschnitt einer Beispielvorlage für VM-Erweiterungen.
Im Folgenden ist ein Ausschnitt einer Vorlage für die Bereitstellung von Erweiterungen gezeigt:

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

Überprüfen Sie vor der Bereitstellung der Erweiterung die aktuelle Erweiterungsversion, und ersetzen Sie den Wert von "typeHandlerVersion" durch die aktuelle Version.

Im weiteren Verlauf des Artikels sind Beispielkonfigurationen für Linux-VM-Erweiterungen aufgeführt.

### CloudLink SecureVM-Agent
          {
            "publisher": "CloudLinkEMC.SecureVM",
            "type": "CloudLinkSecureVMLinuxAgent",
            "typeHandlerVersion": "4.0",
            "settings": {
              "CloudLinkCenter" : "specify valid IP/FQDN to CloudLinkCenter"
            }
          }

### CustomScript-Erweiterung für Linux.
    {
        "publisher": " Microsoft.OSTCExtensions",
        "type": "CustomScriptForLinux",
        "typeHandlerVersion": "1.3",
        "settings": {
            "fileUris": [
                "http: //Yourstorageaccount.blob.core.windows.net/customscriptfiles/start.ps1"
            ],
            "commandToExecute": "powershell.exe-ExecutionPolicyUnrestricted-Filestart.ps1"
        }
    }


### Datadog-Agent
        {
          "publisher": "Datadog.Agent",
          "type": "DatadogLinuxAgent",
          "typeHandlerVersion": "0.4",
          "settings": {
            "api_key" : "API Key from https://app.datadoghq.com/account/settings#api"
          }
        }

### Chef-Agent
        {
          "publisher": "Chef.Bootstrap.WindowsAzure",
          "type": "CentosChefClient|LinuxChefClient",
          "typeHandlerVersion": "1210.12",
          "settings": {
            "validation_key" : " Validation key",
            "client_rb" : "client_rb file",
            "runlist" : "Optional runlist"
          }
        }

### VM-Zugriffserweiterung (Zurücksetzen des Kennworts)
Das aktualisierte Schema finden Sie in der [VMAccessForLinux-Dokumentation](https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess).

        {
          "publisher": "Microsoft.OSTCExtensions",
          "type": "VMAccessForLinux",
          "typeHandlerVersion": "1.2",
          "protectedSettings": {
            "username": "(required, string) the name of the user",
            "password": "(optional, string) the password of the user",
            "reset_ssh": "(optional, boolean) whether or not reset the ssh",
            "ssh_key": "(optional, string) the public key of the user, base64 encoded pem",
            "remove_user": "(optional, string) the user name to remove"
          }
        }

### Betriebssystempatching
Das aktualisierte Schema finden Sie in der [OSPatching-Dokumentation](https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching)

        {
        "publisher": "Microsoft.OSTCExtensions",
        "type": "OSPatchingForLinux",
        "typeHandlerVersion": "2.9",
        "Settings": {
          "disabled": false,
          "stop": false,
          "rebootAfterPatch": "RebootIfNeed|Required|NotRequired|Auto",
          "category": "Important|ImportantAndRecommended",
          "installDuration": "<hr:min>",
          "oneoff": false,
          "intervalOfWeeks": "<number>",
          "dayOfWeek": "Sunday|Monday|Tuesday|Wednesday|Thursday|Friday|Saturday|Everyday",
          "startTime": "<hr:min>",
          "vmStatusTest": {
              "local": false,
              "idleTestScript": "<path_to_idletestscript>",
              "healthyTestScript": "<path_to_healthytestscript>"
          }
        }
        }

### Docker-Erweiterung
Das aktualisierte Schema finden Sie in der [Docker Extension-Dokumentation](https://github.com/Azure/azure-docker-extension/blob/master/README.md#1-configuration-schema)

        {
          "publisher": "Microsoft.Azure.Extensions ",
          "type": "DockerExtension ",
          "typeHandlerVersion": "1.0",
          "Settings": {
            "docker":{
                "port": "2376",
                "options": ["-D", "--dns=8.8.8.8"]
            },
            "compose": {
                "cache" : {
                    "image" : "memcached",
                    "ports" : ["11211:11211"]
                },
                "blog": {
                    "image": "ghost",
                    "ports": ["80:2368"]
                }
            }
            }
        }

        ### Linux Diagnostics Extension
        {
        "storageAccountName": "storage account to receive data",
        "storageAccountKey": "key of the account",
        "perfCfg": [
        {
            "query": "SELECT PercentAvailableMemory, AvailableMemory, UsedMemory ,PercentUsedSwap FROM SCX_MemoryStatisticalInformation",
            "table": "LinuxMemory"
        }
        ],
        "fileCfg": [
        {
            "file": "/var/log/mysql.err",
            "table": "mysqlerr"
        }
        ]
        }

Ersetzen Sie die Versionsnummer in den oben gezeigten Beispielen durch die aktuelle Versionsnummer.

Dies ist eine vollständige VM-Vorlage zum Erstellen eines virtuellen Linux-Computers mit einer Erweiterung:

[Benutzerdefinierte Skriptserweiterung auf einem virtuellen Linux-Computer](https://github.com/Azure/azure-quickstart-templates/blob/b1908e74259da56a92800cace97350af1f1fc32b/mongodb-on-ubuntu/azuredeploy.json/)

<!---HONumber=AcomDC_0323_2016-->