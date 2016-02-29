<properties
	pageTitle="Erstellen eines IoT-Hubs mithilfe der REST-API | Microsoft Azure"
	description="Führen Sie dieses Tutorial für erste Schritte mit der REST-API zum Erstellen eines IoT-Hubs durch."
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
     ms.date="02/12/2016"
     ms.author="dobett"/>

# Tutorial: Erstellen eines IoT-Hubs mithilfe eines C#-Programms und der REST-API

[AZURE.INCLUDE [iot-hub-resource-manager-selector](../../includes/iot-hub-resource-manager-selector.md)]

## Einführung

Sie können die [REST-API für IoT-Hub-Ressourcenanbieter][lnk-rest-api] verwenden, um Azure IoT-Hubs programmgesteuert zu erstellen und zu verwalten. In diesem Tutorial erfahren Sie, wie Sie mithilfe des REST-API für IoT-Hub-Ressourcenanbieter mit einem C#-Programm einen IoT-Hub erstellen.

> [AZURE.NOTE] Azure verfügt über zwei verschiedene Bereitstellungsmodelle für das Erstellen und Verwenden von Ressourcen: [Ressourcen-Manager und klassische Bereitstellungen](../resource-manager-deployment-model.md). Dieser Artikel behandelt die Verwendung des Ressourcen-Manager-Bereitstellungsmodells.

Zum Durchführen dieses Lernprogramms benötigen Sie Folgendes:

- Microsoft Visual Studio 2015.
- Ein aktives Azure-Konto. <br/>Wenn Sie noch kein Konto haben, können Sie in nur wenigen Minuten ein kostenloses Testkonto erstellen. Einzelheiten finden Sie unter [Kostenlose Azure-Testversion][lnk-free-trial].
- [Microsoft Azure PowerShell 1.0][lnk-powershell-install] oder höher.

[AZURE.INCLUDE [iot-hub-prepare-resource-manager](../../includes/iot-hub-prepare-resource-manager.md)]

## Vorbereiten des Visual Studio-Projekts

1. Erstellen Sie in Visual Studio mithilfe der Projektvorlage **Konsolenanwendung** ein neues Visual C#-Windows-Projekt. Geben Sie dem Projekt den Namen **CreateIoTHubREST**.

2. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf das Projekt, und klicken Sie dann auf **NuGet-Pakete verwalten**.

3. Markieren Sie im NuGet-Paket-Manager die Option **Vorabversion einschließen**, und suchen Sie nach **Microsoft.Azure.Management.Resources**. Klicken Sie auf **Installieren**, dann unter **Änderungen überprüfen** auf **OK**. Klicken Sie anschließend auf **Ich akzeptiere**, um die Lizenzen zu akzeptieren.

4. Suchen Sie im NuGet-Paket-Manager nach **Microsoft.IdentityModel.Clients.ActiveDirectory**. Klicken Sie auf **Installieren**, dann unter **Änderungen überprüfen** auf **OK**. Klicken Sie anschließend auf **Ich akzeptiere**, um die Lizenz zu akzeptieren.

6. Ersetzen Sie in „Program.cs“ die vorhandenen **using**-Anweisungen durch folgenden Text:

    ```
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Text;
    using Microsoft.Azure.Management.Resources;
    using Microsoft.Azure.Management.Resources.Models;
    using Microsoft.IdentityModel.Clients.ActiveDirectory;
    using Newtonsoft.Json;
    using Microsoft.Rest;
    using System.Linq;
    using System.Threading;
    using Newtonsoft.Json;
    ```
    
7. Fügen Sie in „Program.cs“ die folgenden statischen Variablen ein, mit denen die Platzhalterwerte ersetzt werden. Weiter oben in diesem Tutorial haben Sie sich die Werte für **ApplicationId**, **SubscriptionId**, **TenantId** und **Password** notiert. **Ressourcengruppenname** ist der Name der Ressourcengruppe, die beim Erstellen des IoT-Hubs verwendet wird – diese Gruppe kann neu oder bereits vorhanden sein. **IoT Hub name** ist der Name des zu erstellenden IoT-Hubs, beispielsweise **MyIoTHub**. **Bereitstellungsname** ist ein Name für die Bereitstellung, wie z. B. **Deployment\_01**.

    ```
    static string applicationId = "{Your ApplicationId}";
    static string subscriptionId = "{Your SubscriptionId";
    static string tenantId = "{Your TenantId}";
    static string password = "{Your application Password}";
    
    static string rgName = "{Resource group name}";
    static string iotHubName = "{IoT Hub name}";
    ```

[AZURE.INCLUDE [iot-hub-get-access-token](../../includes/iot-hub-get-access-token.md)]

## Verwenden der REST-API zum Erstellen eines IoT-Hubs

Verwenden Sie die [IoT-Hub REST-API][lnk-rest-api], um einen neuen IoT-Hub in der Ressourcengruppe zu erstellen. Sie können die REST-API auch verwenden, um Änderungen an einem vorhandenen IoT-Hub vorzunehmen.

1. Fügen Sie "Program.cs" die folgende Methode hinzu:
    
    ```
    static void CreateIoTHub(string token)
    {
        
    }
    ```

2. Fügen Sie den folgenden Code der Methode **CreateIoTHub** hinzu, um ein **HttpClient**-Objekt mit einem Authentifizierungstoken in den Headern zu erstellen:

    ```
    HttpClient client = new HttpClient();
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    ```

3. Fügen Sie den folgenden Code der **CreateIoTHub**-Methode hinzu, um den zu erstellenden IoT-Hub zu beschreiben und eine JSON-Darstellung zu erzeugen.

    ```
    var description = new
    {
      name = iotHubName,
      location = "East US",
      sku = new
      {
        name = "S1",
        tier = "Standard",
        capacity = 1
      }
    };
    
    var json = JsonConvert.SerializeObject(description, Formatting.Indented);
    ```

4. Fügen Sie den folgenden Code der Methode **CreateIoTHub** hinzu, um die REST-Anforderung an Azure zu übermitteln, die Antwort zu überprüfen und die URL abzurufen, die Sie zum Überwachen des Status der Bereitstellungsaufgabe verwenden können:

    ```
    var content = new StringContent(JsonConvert.SerializeObject(description), Encoding.UTF8, "application/json");
    var requestUri = string.Format("https://management.azure.com/subscriptions/{0}/resourcegroups/{1}/providers/Microsoft.devices/IotHubs/{2}?api-version=2016-02-03", subscriptionId, rgName, iotHubName);
    var result = client.PutAsync(requestUri, content).Result;
      
    if (!result.IsSuccessStatusCode)
    {
      Console.WriteLine("Failed {0}", result.Content.ReadAsStringAsync().Result);
      return;
    }
    
    var asyncStatusUri = result.Headers.GetValues("Azure-AsyncOperation").First();
    ```

5. Fügen Sie den folgenden Code am Ende der Methode **CreateIoTHub** hinzu, um die im vorherigen Schritt abgerufene Adresse **asyncStatusUri** zu verwenden, um zu warten, bis die Bereitstellung abgeschlossen ist:

    ```
    string body;
    do
    {
      Thread.Sleep(10000);
      HttpResponseMessage deploymentstatus = client.GetAsync(asyncStatusUri).Result;
      body = deploymentstatus.Content.ReadAsStringAsync().Result;
    } while (body == "{"Status":"Running"}");
    ```

6. Fügen Sie den folgenden Code am Ende der Methode **CreateIoTHub** hinzu, um die Schlüssel des IoT-Hubs abzurufen, den Sie erstellt haben, und sie auf der Konsole auszugeben:

    ```
    var listKeysUri = string.Format("https://management.azure.com/subscriptions/{0}/resourceGroups/{1}/providers/Microsoft.Devices/IotHubs/{2}/IoTHubKeys/listkeys?api-version=2015-08-15-preview", subscriptionId, rgName, iotHubName);
    var keysresults = client.PostAsync(listKeysUri, null).Result;
    
    Console.WriteLine("Keys: {0}", keysresults.Content.ReadAsStringAsync().Result);
    ```
    
## Fertigstellen und Ausführen der Anwendung

Sie können die Anwendung jetzt durch Aufrufen der Methode **CreateIoTHub** fertig stellen und das Projekt dann erstellen und ausführen.

1. Fügen Sie den folgenden Code am Ende der **Main**-Methode hinzu:

    ```
    CreateIoTHub(token.AccessToken);
    Console.ReadLine();
    ```
    
2. Klicken Sie auf **Erstellen** und auf **Projektmappe erstellen**. Beheben Sie alle Fehler.

3. Klicken Sie auf **Debuggen** und dann auf **Debuggen starten**, um die Anwendung auszuführen. Es kann mehrere Minuten dauern, bis die Bereitstellung abgeschlossen ist.

4. Das Hinzufügen des neuen IoT-Hubs durch die Anwendung lässt sich durch Anzeigen der Ressourcenliste im [Portal][lnk-azure-portal] oder mithilfe des PowerShell-Cmdlets **Get-AzureRmResource** überprüfen.

> [AZURE.NOTE] Diese Beispielanwendung fügt einen für Sie kostenpflichtigen S1-Standard-IoT-Hub hinzu. Sie können den IoT-Hub nach Abschluss des Beispiels über das [Portal][lnk-azure-portal] oder mithilfe des PowerShell-Cmdlets **Remove-AzureRmResource** löschen.

## Nächste Schritte

Nachdem Sie nun einen IoT-Hub mit der REST-API bereitgestellt haben, möchten Sie vielleicht mehr wissen:

- Erkunden Sie die weiteren Funktionen in der [IoT-Hub Ressource Anbieter REST-API-Referenz][lnk-rest-api].
- Weitere Informationen zu den Fähigkeiten des Azure-Ressourcen-Manager finden Sie unter [Übersicht über den Azure-Ressourcen-Manager][lnk-azure-rm-overview].

<!-- Links -->
[lnk-free-trial]: https://azure.microsoft.com/pricing/free-trial/
[lnk-azure-portal]: https://portal.azure.com/
[lnk-powershell-install]: ../powershell-install-configure.md
[lnk-rest-api]: https://msdn.microsoft.com/library/mt589014.aspx
[lnk-azure-rm-overview]: ../resource-group-overview.md

<!---HONumber=AcomDC_0218_2016-->