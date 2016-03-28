<properties
	pageTitle="Erstellen eines IoT-Hubs mithilfe einer ARM-Vorlage und PowerShell | Microsoft Azure"
	description="Nutzen Sie dieses Tutorial für erste Schritte mit Resource Manager-Vorlagen zum Erstellen eines IoT-Hubs mit PowerShell."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="multiple"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="02/12/2016"
     ms.author="dobett"/>

# Erstellen eines IoT Hubs mit PowerShell

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Einführung

Sie können den Azure Resource Manager (ARM) verwenden, um Azure IoT-Hubs programmgesteuert zu erstellen und zu verwalten. In diesem Tutorial erfahren Sie, wie Sie anhand einer Resource Manager-Vorlage mit PowerShell einen IoT Hub erstellen.

> [AZURE.NOTE] Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Ressourcen-Manager und klassische Bereitstellungen](../resource-manager-deployment-model.md). Dieser Artikel behandelt die Verwendung des Ressourcen-Manager-Bereitstellungsmodells.

Zum Durchführen dieses Lernprogramms benötigen Sie Folgendes:

- Ein aktives Azure-Konto. <br/>Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][lnk-free-trial].
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] oder höher.

> [AZURE.TIP] Der Artikel [Verwenden von Azure PowerShell mit dem Azure Resource Manager][lnk-powershell-arm] enthält weitere Informationen zum Verwenden von PowerShell-Skripts und ARM-Vorlagen zum Erstellen von Azure-Ressourcen.

## Verbinden mit Ihrem Azure-Abonnement

Geben Sie in einer PowerShell-Befehlszeile den folgenden Befehl zur Anmeldung bei Ihrem Azure-Abonnement ein:

```
Login-AzureRmAccount
```

Mit den folgenden Befehlen können Sie ermitteln, wo Sie einen IoT-Hub und die derzeit unterstützten API-Versionen bereitstellen können:

```
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).Locations
((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Devices).ResourceTypes | Where-Object ResourceTypeName -eq IoTHubs).ApiVersions
```

Erstellen Sie mit dem folgenden Befehl an einem Speicherort, der IoT Hub unterstützt, eine Ressourcengruppe, die Ihren IoT-Hub enthalten soll. Dieses Beispiel erstellt eine Ressourcengruppe namens **MyIoTRG1**:

```
New-AzureRmResourceGroup -Name MyIoTRG1 -Location "East US"
```

## Einsenden einer Vorlage zum Erstellen eines IoT-Hubs

Verwenden Sie eine JSON-Vorlage, um einen neuen IoT-Hub in der Ressourcengruppe zu erstellen. Sie können auch eine Vorlage verwenden, um Änderungen an einem vorhandenen IoT-Hub vorzunehmen.

1. Verwenden Sie einen Texteditor zum Erstellen einer ARM-Vorlage namens **template.json** mit der folgenden Ressourcendefinition, um einen neuen Standard-IoT-Hub zu erstellen. In diesem Beispiel wird der IoT-Hub in der Region **USA, Osten** hinzugefügt und die API-Version **2016-02-03** verwendet. Diese Vorlage erwartet auch, dass Sie den IoT-Hub-Namen als Parameter **hubName** übergeben.

    ```
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "hubName": {
          "type": "string"
        }
      },
      "resources": [
      {
        "apiVersion": "2016-02-03",
        "type": "Microsoft.Devices/IotHubs",
        "name": "[parameters('hubName')]",
        "location": "East US",
        "sku": {
          "name": "S1",
          "tier": "Standard",
          "capacity": 1
        },
        "properties": {
          "location": "East US"
        }
      }
      ],
      "outputs": {
        "hubKeys": {
          "value": "[listKeys(resourceId('Microsoft.Devices/IotHubs', parameters('hubName')), '2016-02-03')]",
          "type": "object"
        }
      }
    }
    ```

2. Speichern Sie die Vorlagendatei auf Ihrem lokalen Computer. In diesem Beispiel wird davon ausgegangen, dass Sie sie in dem Ordner **c:\\templates** speichern.

3. Führen Sie den folgenden Befehl zum Bereitstellen Ihres neuen IoT-Hubs aus, der den Namen des IoT-Hubs als Parameter übergibt. In diesem Beispiel ist der Name des IoT-Hubs **myiothub**:

    ```
    New-AzureRmResourceGroupDeployment -ResourceGroupName MyIoTRG1 -TemplateFile C:\templates\template.json -hubName myiothub
    ```

4. Die Ausgabe zeigt die Schlüssel für den IoT-Hub an, den Sie erstellt haben.

5. Das Hinzufügen des neuen IoT-Hubs durch die Anwendung lässt sich durch Anzeigen der Ressourcenliste im [Portal][lnk-azure-portal] oder mithilfe des PowerShell-Cmdlets **Get-AzureRmResource** überprüfen.

> [AZURE.NOTE] Diese Beispielanwendung fügt einen für Sie kostenpflichtigen S1-Standard-IoT-Hub hinzu. Sie können den IoT-Hub nach Abschluss des Beispiels über das [Portal][lnk-azure-portal] oder mithilfe des PowerShell-Cmdlets **Remove-AzureRmResource** löschen.

## Nächste Schritte

Nachdem Sie nun einen IoT-Hub mithilfe einer ARM-Vorlage mit PowerShell bereitgestellt haben, möchten Sie vielleicht mehr wissen:

- Informieren Sie sich über die Funktionen der [IoT Hub-Ressourcenanbieter-REST-API][lnk-rest-api].
- Weitere Informationen zu den Fähigkeiten des Azure-Ressourcen-Manager finden Sie unter [Übersicht über den Azure-Ressourcen-Manager][lnk-azure-rm-overview].

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md
[lnk-powershell-arm]: ../powershell-azure-resource-manager.md

<!---HONumber=AcomDC_0316_2016-->