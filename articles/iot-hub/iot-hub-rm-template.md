<properties
	pageTitle="Erstellen eines IoT-Hubs mithilfe einer Ressourcen-Manager-Vorlage | Microsoft Azure"
	description="Führen Sie dieses Tutorial für erste Schritte mit Ressourcen-Manager-Vorlagen zum Erstellen eines IoT-Hubs durch."
	services="iot-hub"
	documentationCenter=".net"
	authors="dominicbetts"
	manager="timlt"
	editor=""/>

<tags
     ms.service="iot-hub"
     ms.devlang="dotnet"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="na"
     ms.date="11/23/2015"
     ms.author="dobett"/>

# Tutorial: Erstellen eines IoT-Hubs mithilfe eines C#-Programms

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Einführung

Sie können den Azure-Ressourcen-Manager verwenden, um Azure IoT-Hubs programmgesteuert zu erstellen und zu verwalten. In diesem Tutorial erfahren Sie, wie Sie anhand einer Ressourcen-Manager-Vorlage mit einem C#-Programm einen IoT-Hub erstellen.

> [AZURE.NOTE] Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Ressourcen-Manager und klassische Bereitstellungen](../resource-manager-deployment-model.md). Dieser Artikel behandelt die Verwendung des Ressourcen-Manager-Bereitstellungsmodells.

Zum Durchführen dieses Lernprogramms benötigen Sie Folgendes:

- Microsoft Visual Studio 2015.
- Ein aktives Azure-Konto. <br/>Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][lnk-free-trial].
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] oder höher.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Vorbereiten des Visual Studio-Projekts

1. Erstellen Sie in Visual Studio mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Windows-Projekt. Geben Sie dem Projekt den Namen **CreateIoTHub**.

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **NuGet-Pakete verwalten**.

3. Suchen Sie im NuGet-Paket-Manager nach **Microsoft.Azure.Management.Resources**. Wählen Sie die Version **2.18.11-preview** aus. Klicken Sie auf **Installieren**, dann unter **Änderungen überprüfen** auf **OK**. Klicken Sie anschließend auf **Ich akzeptiere**, um die Lizenzen zu akzeptieren.

4. Suchen Sie im NuGet-Paket-Manager nach **Microsoft.IdentityModel.Clients.ActiveDirectory**. Wählen Sie die Version **2.19.208020213** aus. Klicken Sie auf **Installieren**, dann unter **Änderungen überprüfen** auf **OK**. Klicken Sie anschließend auf **Ich akzeptiere**, um die Lizenz zu akzeptieren.

5. Suchen Sie im NuGet-Paket-Manager nach **Microsoft.Azure.Common**. Wählen Sie die Version **2.1.0** aus. Klicken Sie auf **Installieren**, dann unter **Änderungen überprüfen** auf **OK**. Klicken Sie anschließend auf **Ich akzeptiere**, um die Lizenzen zu akzeptieren.

6. Ersetzen Sie in „Program.cs“ die vorhandenen **using**-Anweisungen durch folgenden Text:

    ```
    using System;
    using System.IO;
    using System.Net;
    using System.Net.Http.Headers;
    using Microsoft.Azure;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
    
7. Fügen Sie in „Program.cs“ die folgenden statischen Variablen ein, mit denen die Platzhalterwerte ersetzt werden. Weiter oben in diesem Tutorial haben Sie sich die Werte für **ApplicationId**, **SubscriptionId**, **TenantId** und **Password** notiert. **Ressourcengruppenname** ist der Name der Ressourcengruppe, die beim Erstellen des IoT-Hubs verwendet wird – diese Gruppe kann neu oder bereits vorhanden sein. **IoT Hub name** ist der Name des zu erstellenden IoT-Hubs, beispielsweise **MyIoTHub**. **Bereitstellungsname** ist ein Name für die Bereitstellung, wie z. B. **Deployment\_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name}";
    static string deploymentName = "{Deployment name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## Einsenden einer Vorlage zum Erstellen eines IoT-Hubs

Verwenden Sie eine JSON-Vorlage, um einen neuen IoT-Hub in der Ressourcengruppe zu erstellen. Sie können auch eine Vorlage verwenden, um Änderungen an einem vorhandenen IoT-Hub vorzunehmen.

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **Hinzufügen** und **Neues Element**. Fügen Sie dem Projekt eine neue JSON-Datei namens **template.json** hinzu.

2. Wählen Sie im Projektmappen-Explorer die Datei **template.json** aus, und legen Sie dann unter **Eigenschaften** die Einstellung **In Ausgabeverzeichnis kopieren** auf **Immer kopieren** fest.

3. Ersetzen Sie den Inhalt von **template.json** mit der folgenden Ressourcendefinition, um der Region **East US** einen neuen Standard-IoT-Hub hinzuzufügen:

    ```
    {
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
    "contentVersion": "1.0.0.0",

    "resources": [
      {
        "apiVersion": "2015-08-15-preview",
        "type": "Microsoft.Devices/Iothubs",
        "name": "[IotHubName]",
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
    ]
    }
    ```

4. Fügen Sie "Program.cs" die folgende Methode hinzu:
    
    ```
    static bool CreateIoTHub(ResourceManagementClient client)
    {
        
    }
    ```

5. Fügen Sie der **CreateIoTHub**-Methode den folgenden Code hinzu, um die Vorlagendatei zu laden, den Namen des IoT-Hubs hinzuzufügen und die Vorlage an den Azure-Ressourcen-Manager zu übermitteln:

    ```
    string template = File.ReadAllText("template.json");
    template = template.Replace("[IotHubName]", iotHubName);
    var createResponse = client.Deployments.CreateOrUpdateAsync(
      rgName,
      deploymentName,
      new Deployment()
      {
        Properties = new DeploymentProperties
        {
          Mode = DeploymentMode.Incremental,
          Template = template
        }
      }).Result;
    ```

6. Fügen Sie der **CreateIoTHub**-Methode den folgenden Code hinzu, damit gewartet wird, bis die Bereitstellung erfolgreich abgeschlossen wurde:

    ```
    string state = createResponse.Deployment.Properties.ProvisioningState;
    while (state != "Succeeded" && state != "Failed")
    {
      var getResponse = client.Deployments.GetAsync(
        rgName,
        deploymentName).Result;

      state = getResponse.Deployment.Properties.ProvisioningState;
      Console.WriteLine("Deployment state: {0}", state);
    }

    if (state != "Succeeded")
    {
      Console.WriteLine("Failed to create iothub");
      return false;
    }
    return true;
    ```

[AZURE.INCLUDE [iot-hub-retrieve-keys](../../includes/iot-hub-retrieve-keys.md)]

## Fertigstellen und Ausführen der Anwendung

Sie können die Anwendung jetzt durch Aufrufen der Methoden **CreateIoTHub** und **ShowIoTHubKeys** fertigstellen und das Projekt dann erstellen und ausführen.

1. Fügen Sie den folgenden Code am Ende der **Main**-Methode hinzu:

    ```
    if (CreateIoTHub(client))
        ShowIoTHubKeys(client, token.AccessToken);
    Console.ReadLine();
    ```
    
2. Klicken Sie auf **Erstellen** und auf **Projektmappe erstellen**. Beheben Sie alle Fehler.

3. Klicken Sie auf **Debuggen** und dann auf **Debuggen starten**, um die Anwendung auszuführen. Es kann mehrere Minuten dauern, bis die Bereitstellung abgeschlossen ist.

4. Das Hinzufügen des neuen IoT-Hubs durch die Anwendung lässt sich durch Anzeigen der Ressourcenliste im [Portal][lnk-azure-portal] oder mithilfe des PowerShell-Cmdlets **Get-AzureRmResource** überprüfen.

> [AZURE.NOTE] Diese Beispielanwendung fügt einen für Sie kostenpflichtigen S1-Standard-IoT-Hub hinzu. Sie können den IoT-Hub nach Abschluss des Beispiels über das [Portal][lnk-azure-portal] oder mithilfe des PowerShell-Cmdlets **Remove-AzureRmResource** löschen.

## Nächste Schritte

- Erkunden Sie die weiteren Funktionen in der [IoT-Hub Ressource Anbieter REST-API-Referenz][lnk-rest-api].
- Weitere Informationen zu den Fähigkeiten des Azure-Ressourcen-Manager finden Sie unter [Übersicht über den Azure-Ressourcen-Manager][lnk-azure-rm-overview].

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: https://azure.microsoft.com/de-DE/blog/azps-1-0-pre/
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: https://azure.microsoft.com/documentation/articles/resource-group-overview/

<!---HONumber=AcomDC_0204_2016-->