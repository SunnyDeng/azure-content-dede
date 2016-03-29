<properties
   pageTitle="Resource Manager SDK für .NET| Microsoft Azure"
   description="Enthält eine Übersicht über die Resource Manager .NET SDK-Authentifizierung und Beispiele zur Verwendung."
   services="azure-resource-manager"
   documentationCenter="na"
   authors="navalev"
   manager=""
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/10/2016"
   ms.author="navale;tomfitz;"/>

# Azure Resource Manager SDK für .NET  
Azure Resource Manager (ARM) Preview SDKs sind für mehrere Sprachen und Plattformen verfügbar. Jede dieser Sprachimplementierungen ist über die entsprechenden Ökosystem-Paket-Manager und GitHub verfügbar.

Der Code in diesen SDKs wird jeweils über [Azure RESTful-API-Spezifikationen](https://github.com/azure/azure-rest-api-specs) generiert. Dies sind Open-Source-Spezifikationen, die auf der Swagger v2-Spezifikation basieren. Der SDK-Code ist Code, der über ein Open-Source-Projekt mit dem Namen [AutoRest](https://github.com/azure/autorest) generiert wird. AutoRest transformiert diese RESTful-API-Spezifikationen in Clientbibliotheken in mehreren Sprachen. Falls Sie Aspekte des generierten Codes in den SDKs verbessern möchten, können Sie den gesamten Satz der Tools zum Erstellen der SDKs verwenden. Sie sind frei zugänglich und verfügbar und basieren auf dem gängigen API-Spezifikationsformat.

Azure SDK für .NET wird als Gruppe von NuGet-Paketen bereitgestellt, mit denen Sie die meisten APIs aufrufen können, die von Azure Resource Manager verfügbar gemacht werden. Wenn das SDK die erforderliche Funktionalität nicht verfügbar macht, können Sie das SDK leicht mit normalen Aufrufen der ARM-REST-API im Hintergrund kombinieren.

In dieser Dokumentation sollen nicht alle Aspekte des Azure SDK für .NET, von Azure ARM-APIs oder von Visual Studio beschrieben werden, sondern sie soll Ihnen als schnelle Starthilfe dienen.

Ein vollständiges Beispielprojekt als Download, aus dem alle unten angegebenen Codeausschnitte stammen, finden Sie [hier](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net).

## Authentifizierung
Die Authentifizierung für ARM wird per Azure Active Directory (AD) durchgeführt. Zum Herstellen einer Verbindung mit einer API müssen Sie sich zuerst gegenüber Azure AD authentifizieren, um ein Authentifizierungstoken zu erhalten, das Sie für jede Anforderung übergeben können. Um dieses Token zu erhalten, müssen Sie zuerst eine so genannte Azure AD-Anwendung und einen Dienstprinzipal erstellen, der für die Anmeldung verwendet wird. Eine Schritt-für-Schritt-Anleitung finden Sie unter [Erstellen einer Azure AD-Anwendung und eines Dienstprinzipals](./resource-group-create-service-principal-portal.md).

Nach dem Erstellen des Dienstprinzipals sollten Sie über Folgendes verfügen:
* Client-ID (GUID)
* Geheimer Clientschlüssel (Zeichenfolge)
* Mandanten-ID (GUID) oder Domänenname (Zeichenfolge)

### Empfangen des Zugriffstokens (AccessToken) per Code
Das Authentifizierungstoken kann mit den unten angegebenen Codezeilen leicht beschafft werden. Hierfür übergeben Sie lediglich Ihre Azure AD-Mandanten-ID, Ihre Azure AD-Anwendungsclient-ID und den geheimen Clientschlüssel der Azure AD-Anwendung. Speichern Sie das Token für mehrere Anforderungen, da es standardmäßig eine Stunde lang gültig ist.

```csharp
private static AuthenticationResult GetAccessToken(string tenantId, string clientId, string clientSecret)
{
    Console.WriteLine("Aquiring Access Token from Azure AD");
    AuthenticationContext authContext = new AuthenticationContext
        ("https://login.windows.net/" /* AAD URI */
            + $"{tenantId}.onmicrosoft.com" /* Tenant ID or AAD domain */);

    var credential = new ClientCredential(clientId, clientSecret);

    AuthenticationResult token = authContext.AcquireToken("https://management.azure.com/", credential);

    Console.WriteLine($"Token: {token.AccessToken}");
    return token;
}
```

### Abfragen von Azure-Abonnements, die an die authentifizierte Anwendung angefügt sind
Einer der ersten Schritte, den Sie ausführen sollten, ist die Abfrage, welche Azure-Abonnements der gerade authentifizierten Anwendung zugeordnet sind. Ab jetzt ist es obligatorisch, die Abonnement-ID für das gewünschte Abonnement für jeden durchgeführten API-Aufruf zu übergeben.

Im unten angegebenen Beispielcode werden Azure-APIs direkt mit der REST-API abgefragt, also nicht mit Features im Azure SDK für .NET.

```csharp
async private static Task<List<string>> GetSubscriptionsAsync(string token)
{
    Console.WriteLine("Querying for subscriptions");
    const string apiVersion = "2015-01-01";

    var client = new HttpClient();
    client.BaseAddress = new Uri("https://management.azure.com/");
    client.DefaultRequestHeaders.Authorization = new AuthenticationHeaderValue("Bearer", token);
    client.DefaultRequestHeaders.Accept.Add(new MediaTypeWithQualityHeaderValue("application/json"));

    var response = await client.GetAsync($"subscriptions?api-version={apiVersion}");

    var jsonResponse = response.Content.AsString();

    var subscriptionIds = new List<string>();
    dynamic json = JsonConvert.DeserializeObject(jsonResponse);

    for (int i = 0; i < json.value.Count; i++)
    {
        subscriptionIds.Add(json.value[i].subscriptionId.Value);
    }

    Console.WriteLine($"Found {subscriptionIds.Count} subscription(s)");
    return subscriptionIds;
}
```

Beachten Sie, dass wir von Azure eine JSON-Antwort erhalten, aus der wir dann die Abonnement-IDs extrahieren, um eine Liste mit IDs zurückzugeben. Bei allen nachfolgenden Aufrufen von Azure ARM-APIs in dieser Dokumentation wird nur eine einzelne Azure-Abonnement-ID verwendet. Wenn der Anwendung also mehrere Abonnements zugeordnet sind, müssen Sie nur das richtige Abonnement auswählen und jeweils als Parameter übergeben.

Ab hier wird für jeden Aufruf über die Azure-APIs das Azure SDK für .NET verwendet, sodass der Code etwas anders aussieht.

### Umschließen des Tokens als TokenCredentials-Objekt
Für alle nachfolgenden API-Aufrufe ist das Token erforderlich, das Sie von Azure AD im Format eines TokenCredentials-Objekts erhalten haben. Ein Objekt dieser Art kann leicht erstellt werden, indem einfach das unformatierte Token als Parameter an den Konstruktor der Klasse übergeben wird.

```csharp
var credentials = new TokenCredentials(token);
```

## Erstellen einer Ressourcengruppe
In Azure dreht sich alles um die Ressourcengruppen. Deshalb beginnen wir, indem wir eine Ressourcengruppe erstellen. Allgemeine Ressourcen und Ressourcengruppen werden mit dem *ResourceManagementClient*-Element behandelt. Für die folgenden spezielleren Verwaltungsclients die wir verwenden werden, müssen Sie Ihre Anmeldeinformationen und eine Abonnement-ID eingeben, um das gewünschte Abonnement festzulegen.

```csharp
private static async Task<ResourceGroup> CreateResourceGroupAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location)
{
    Console.WriteLine($"Creating Resource Group {resourceGroup}");
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await resourceClient.ResourceGroups.CreateOrUpdateAsync(resourceGroup,
        new ResourceGroup
        {
            Location = location
        });
}
```

## Manuelles Erstellen von Ressourcen oder Verwenden von Vorlagen
Es gibt verschiedene Arten der Interaktion mit den Azure Resource Manager-APIs, aber die beiden Hauptmethoden sind:

* Manuelles Interagieren durch das manuelle Aufrufen spezieller Ressourcenanbieter oder
* Verwenden einer Azure Resource Manager-Vorlage (ARM-Vorlage)

Die Verwendung von ARM-Vorlagen hat folgende Vorteile:

* Sie geben deklarativ an, wie das Endergebnis aussehen soll, und nicht, wie es erreicht werden soll.
* Sie müssen die parallele Ausführung Ihrer Bereitstellungen nicht manuell durchführen, sondern ARM übernimmt diese Aufgabe für Sie.
* Sie müssen nicht C# oder eine andere Sprache erlernen, um eine ARM-Vorlage bereitzustellen (obwohl Sie jede Sprache verwenden können, um eine Bereitstellung mit Vorlage zu starten).
* Die domänenspezifische Sprache (DSL), die in den Vorlagen verwendet wird, wird mit JSON erstellt und ist für alle Benutzer leicht verständlich, die bereits mit JSON gearbeitet haben.

Trotz dieser vielen Vorteile der Vorlagen zeigen wir Ihnen zuerst, wie Sie die APIs manuell aufrufen.

### Erstellen eines virtuellen Computers – Stück für Stück
Wir verfügen also über unser Abonnement und unsere Ressourcengruppe. Wenn wir einen virtuellen Computer bereitstellen möchten, müssen wir ermitteln, aus welchen Teilen ein virtueller Computer eigentlich besteht. Dabei wird klar, dass es sich nicht nur um wenige Teile handelt:

* Ein oder viele Speicherkonten zum Speichern persistenter Datenträger
* Eine oder viele öffentliche IP-Adressen (PIP) für die Erreichbarkeit über das Internet (enthält einen DNS-Namen)
* Ein oder mehrere virtuelle Netzwerke (VNET) für die interne Kommunikation zwischen Ressourcen
* Eine oder viele Netzwerkschnittstellenkarten (NIC) für die Kommunikation der VM
* Einer oder viele virtuelle Computer (VM) zum Ausführen der Software

Ein weiterer interessanter Punkt ist, dass einige dieser Ressourcen parallel erstellt werden können, während dies für andere Ressourcen nicht möglich ist. Beispiel:

* NICs, abhängig von PIP und VNET
* VMs, abhängig von NICs und Speicherkonten

Sie müssen sicherstellen, dass Sie nicht versuchen, Ressourcen zu instanziieren, bevor die erforderlichen Abhängigkeiten erstellt wurden. Im vollständigen [Beispiel](https://github.com/dx-ted-emea/Azure-Resource-Manager-Documentation/tree/master/ARM/SDKs/Samples/Net) für diese Dokumentation wird veranschaulicht, wie Sie Ressourcen auf effiziente Weise parallel erstellen und die Erstellung gleichzeitig nachverfolgen können.

#### Erstellen des Speicherkontos
Sie benötigen ein Speicherkonto zum Speichern der virtuellen Festplatten (VHDs) für den virtuellen Computer. Wenn Sie über ein vorhandenes Speicherkonto verfügen, können Sie es für mehrere VMs verwenden. Beachten Sie aber, dass Sie die Last auf mehrere Speicherkonten verteilen sollten, damit es nicht zu Engpässen kommt. Bedenken Sie auch, dass der Typ des Speicherkontos und dessen Standort eine Einschränkung der auswählbaren VM-Größe ergeben. Der Grund ist, dass nicht alle VM-Größen in allen Regionen bzw. für alle Speicherkontentypen verfügbar sind.

```csharp
private static async Task<StorageAccount> CreateStorageAccountAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, AccountType accountType = AccountType.StandardLRS)
{
    Console.WriteLine("Creating Storage Account");
    var storageClient = new StorageManagementClient(credentials) { SubscriptionId = subscriptionId };
    return await storageClient.StorageAccounts.CreateAsync(resourceGroup, storageAccountName,
        new StorageAccountCreateParameters
        {
            Location = location,
            AccountType = accountType,
        });
}
```

#### Erstellen der öffentlichen IP-Adresse (PIP)
Über die öffentliche IP-Adresse sind Ihre Ressourcen in Azure über das Internet zugänglich. Zusammen mit der IP-Adresse wird Ihnen auch ein vollqualifizierter Domänenname (FQDN) zugewiesen, der Ihnen den Zugriff erleichtert.

```csharp
private static Task<PublicIPAddress> CreatePublicIPAddressAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string pipAddressName, string pipDnsName)
{
    Console.WriteLine("Creating Public IP");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createPipTask = networkClient.PublicIPAddresses.CreateOrUpdateAsync(resourceGroup, pipAddressName,
        new PublicIPAddress
        {
            Location = location,
            DnsSettings = new PublicIPAddressDnsSettings { DomainNameLabel = pipDnsName },
            PublicIPAllocationMethod = "Dynamic" // This sample doesn't support Static IP Addresses but could be extended to do so
        });

    return createPipTask;
}
```

#### Erstellen des virtuellen Netzwerks (VNET)
Alle virtuellen Computer, die mit den ARM-APIs erstellt werden, müssen Teil eines virtuellen Netzwerks sein. Dies gilt auch, wenn der virtuelle Computer der einzige darin enthaltene Computer ist. Das virtuelle Netzwerk muss mindestens ein Subnetz enthalten, aber Sie können auch viele Subnetze verwenden, um Ihre Ressourcen auf mehrere Subnetze aufzuteilen und so zu schützen.

```csharp
private static Task<VirtualNetwork> CreateVirtualNetworkAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string vNetName, string vNetAddressPrefix, Subnet[] subnets)
{
    Console.WriteLine("Creating Virtual Network");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createVNetTask = networkClient.VirtualNetworks.CreateOrUpdateAsync(resourceGroup, vNetName,
        new VirtualNetwork
        {
            Location = location,
            AddressSpace = new AddressSpace(new[] { vNetAddressPrefix }),
            Subnets = subnets
        });

    return createVNetTask;
}
```

#### Erstellen der Netzwerkschnittstellenkarte (NIC)
Über die Netzwerkschnittstellenkarte (NIC) wird die VM mit dem virtuellen Netzwerk verbunden, in dem sie sich befindet. Eine VM kann über viele NICs verfügen und somit mehreren virtuellen Netzwerken zugeordnet werden. In diesem Beispiel wird davon ausgegangen, dass Sie die VMs nur einem VNET zuordnen.

```csharp
private static Task<NetworkInterface> CreateNetworkInterfaceAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string nicName, string nicIPConfigName, PublicIPAddress pip, Subnet subnet)
{
    Console.WriteLine("Creating Network Interface");
    var networkClient = new NetworkManagementClient(credentials) { SubscriptionId = subscriptionId };
    var createNicTask = networkClient.NetworkInterfaces.CreateOrUpdateAsync(resourceGroup, nicName,
        new NetworkInterface()
        {
            Location = location,
            IpConfigurations = new[] {
                new NetworkInterfaceIPConfiguration
                {
                    Name = nicIPConfigName,
                    PrivateIPAllocationMethod = "Dynamic",
                    PublicIPAddress = pip,
                    Subnet = subnet
                }
            }
        });

    return createNicTask;
}
```

#### Erstellen des virtuellen Computers
Jetzt können wir den eigentlichen virtuellen Computer erstellen. Die VM ist direkt oder indirekt von allen oben erstellten Ressourcen abhängig. Sie müssen also auf die Fertigstellung bzw. Betriebsbereitschaft aller obigen Komponenten warten, bevor Sie versuchen, eine VM bereitzustellen. Die VM ist die Ressource der obigen Ressourcen, deren Bereitstellung am längsten dauert. Sie sollten also damit rechnen, dass die Anwendung eine Zeit lang warten muss, bis dieser Vorgang abgeschlossen ist.

```csharp
private static async Task<VirtualMachine> CreateVirtualMachineAsync(TokenCredentials credentials, string subscriptionId, string resourceGroup, string location, string storageAccountName, string vmName, string vmSize, string vmAdminUsername, string vmAdminPassword, string vmImagePublisher, string vmImageOffer, string vmImageSku, string vmImageVersion, string vmOSDiskName, string nicId)
{
    Console.WriteLine("Creating Virtual Machine (this may take a while)");
    var computeClient = new ComputeManagementClient(credentials) { SubscriptionId = subscriptionId };
    var vm = await computeClient.VirtualMachines.CreateOrUpdateAsync(resourceGroup, vmName,
        new VirtualMachine
        {
            Location = location,
            HardwareProfile = new HardwareProfile(vmSize),
            OsProfile = new OSProfile(vmName, vmAdminUsername, vmAdminPassword),
            StorageProfile = new StorageProfile(
                new ImageReference
                {
                    Publisher = vmImagePublisher,
                    Offer = vmImageOffer,
                    Sku = vmImageSku,
                    Version = vmImageVersion
                },
                new OSDisk
                {
                    Name = vmOSDiskName,
                    Vhd = new VirtualHardDisk($"http://{storageAccountName}.blob.core.windows.net/vhds/{vmOSDiskName}.vhd"),
                    Caching = "ReadWrite",
                    CreateOption = "FromImage"
                }),
            NetworkProfile = new NetworkProfile(
                new[] { new NetworkInterfaceReference { Id = nicId } }),
            DiagnosticsProfile = new DiagnosticsProfile(
                new BootDiagnostics
                {
                    Enabled = true,
                    StorageUri = $"http://{storageAccountName}.blob.core.windows.net"
                })
        });

    return vm;
}
```

### Verwenden einer Bereitstellung mit Vorlage
Eine ausführliche Anleitung zur Bereitstellung einer Vorlage finden Sie unter [Bereitstellen von Azure-Ressourcen mithilfe von .NET-Bibliotheken und einer Vorlage](./arm-template-deployment/#step-4-create-the-credentials-that-are-used-to-authenticate-requests).

Zusammenfassung: Das Bereitstellen einer Vorlage ist viel einfacher als das manuelle Bereitstellen der Ressourcen. Die Vorgehensweise wird im Code unten veranschaulicht, indem auf die URIs verwiesen wird, unter denen sich die Vorlage und die Parameterdatei befinden.

```csharp
private static async Task<DeploymentExtended> CreateTemplatedDeployment(TokenCredentials credentials, string subscriptionId, string resourceGroup, string templateUri, string parametersUri)
{
    var resourceClient = new ResourceManagementClient(credentials) { SubscriptionId = subscriptionId };

    return await resourceClient.Deployments.BeginCreateOrUpdateAsync(resourceGroup, "mytemplateddeployment", new Deployment(
        new DeploymentProperties()
        {
            Mode = DeploymentMode.Incremental,
            TemplateLink = new TemplateLink(templateUri),
            ParametersLink = new ParametersLink(parametersUri)
        }));

}
```


 
   

<!---HONumber=AcomDC_0316_2016-->