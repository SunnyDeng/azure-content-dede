<properties
   pageTitle="Beispielkonfiguration für Azure-VM-Erweiterungen | Microsoft Azure"
	description="Beispielkonfiguration für das Erstellen von Vorlagen mit Erweiterungen"
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

# Konfigurationsbeispiele für Linux-VM-Erweiterungen in Azure.

Dieser Artikel enthält Beispielkonfigurationen der Azure-VM-Erweiterungen für Azure-IaaS-Linux-VMs. Klicken Sie <a href="https://msdn.microsoft.com/de-DE/library/azure/dn606311.aspx" target="_blank">hier</a>, um mehr über diese Erweiterungen zu erfahren.

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
Das aktualisierte Schema finden Sie <a href="https://github.com/Azure/azure-linux-extensions/tree/master/VMAccess" target="_blank">hier</a>.

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
Das aktualisierte Schema finden Sie <a href="https://github.com/Azure/azure-linux-extensions/tree/master/OSPatching" target="_blank">hier</a>.

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
Das aktualisierte Schema finden Sie <a href="https://github.com/Azure/azure-docker-extension/blob/master/README.md#1-configuration-schema" target="_blank">hier</a>.

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
Ersetzen Sie die Versionsnummer in den oben gezeigten Beispielen durch die aktuelle Versionsnummer.

<!---HONumber=September15_HO1-->