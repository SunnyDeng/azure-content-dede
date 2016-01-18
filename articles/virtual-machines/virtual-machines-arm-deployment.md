<properties
	pageTitle="Bereitstellen von Ressourcen mithilfe von .NET-Bibliotheken | Microsoft Azure"
	description="Erfahren Sie, wie mit den Compute-, Storage- und Netzwerkbibliotheken von .NET zum Ressourcen in Microsoft Azure mit dem Ressourcen-Manager erstellen und löschen."
	services="virtual-machines,virtual-network,storage"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/05/2016"
	ms.author="davidmu"/>

# Bereitstellen von Azure-Ressourcen mithilfe der Compute-, Netzwerk- und Speicherbibliotheken von .NET

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.

In diesem Tutorial erfahren Sie, wie Sie die Compute-, Speicher- und Netzwerkbibliotheken von .NET zum Erstellen und Löschen von Ressourcen in Microsoft Azure verwenden. Außerdem erfahren Sie, wie Sie die Anforderungen an den Azure-Ressourcen-Manager mithilfe von Azure Active Directory authentifizieren.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Zur Durchführung dieses Lernprogramms benötigen Sie außerdem Folgendes:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Azure-Speicherkonto](../storage-create-storage-account.md)
- [Windows Management Framework 3.0 ](http://www.microsoft.com/download/details.aspx?id=34595) oder [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Die Durchführung dieser Schritte dauert etwa 30 Minuten.

## Schritt 1: Hinzufügen einer Anwendung zu Azure AD und Festlegen von Berechtigungen

Um Azure AD zum Authentifizieren von Anforderungen an den Azure-Ressourcen-Manager zu verwenden, muss dem Standardverzeichnis eine Anwendung hinzugefügt werden. Weitere Informationen finden Sie unter [Authentifizieren eines Dienstprinzipals mit dem Azure-Ressourcen-Manager](../resource-group-authenticate-service-principal.md).

1. Öffnen Sie eine Azure PowerShell-Eingabeaufforderung, führen Sie diesen Befehl aus, und geben Sie dann bei entsprechender Aufforderung die Anmeldeinformationen für Ihr Abonnement ein:

			Login-AzureRmAccount

2. Ersetzen Sie im folgenden Befehl {password} durch das Kennwort, das Sie verwenden möchten, und führen Sie ihn anschließend aus, um die Anwendung zu erstellen:

			New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE]Notieren Sie die Anwendungs-ID, die nach dem Erstellen der Anwendung zurückgegeben wird, da Sie diese für den nächsten Schritt benötigen. Sie finden die Anwendungs-ID auch im Client-ID-Feld der Anwendung im Active Directory-Bereich des Azure-Portals.

3. Ersetzen Sie {application-id} durch die ID, die Sie gerade notiert haben, und erstellen Sie dann den Dienstprinzipal für die Anwendung:

        New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. Legen Sie die Berechtigung zum Verwenden der Anwendung fest:

	    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Schritt 2: Erstellen eines Visual Studio-Projekts und Installation der Bibliotheken

NuGet-Pakete sind die einfachste Möglichkeit, um die Bibliotheken zu installieren, die Sie zum Abschluss dieses Lernprogramms benötigen. Sie müssen die Azure-Ressourcenverwaltungsbibliothek, die Azure Active Directory-Authentifizierungsbibliothek und die Computerressourcenanbieter-Bibliothek installieren. Führen Sie folgende Schritte aus, um diese Bibliotheken in Visual Studio abzurufen:

1. Klicken Sie auf **Datei** > **Neu** > **Projekt**.

2. Wählen Sie unter **Vorlagen** > **Visual C#** **Konsolenanwendung** aus, geben Sie den Namen und Speicherort des Projekts ein, und klicken Sie dann auf **OK**.

3. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen und anschließend auf **NuGet-Pakete verwalten**.

4. Geben Sie *Active Directory* in das Suchfeld ein, klicken Sie für das Active Directory-Authentifizierungsbibliothek-Paket auf **Installieren**, und befolgen Sie dann die Anweisungen zum Installieren des Pakets.

5. Wählen Sie am oberen Rand der Seite **Vorabversion einschließen** aus. Geben Sie *Microsoft.Azure.Management.Compute* in das Suchfeld ein, klicken Sie für die Compute-Bibliotheken von .NET auf **Installieren**, und befolgen Sie dann die Anweisungen zum Installieren des Pakets.

6. Geben Sie *Microsoft.Azure.Management.Network* in das Suchfeld ein, klicken Sie für die Netzwerkbibliotheken von .NET auf **Installieren**, und befolgen Sie dann die Anweisungen zum Installieren des Pakets.

7. Geben Sie *Microsoft.Azure.Management.Storage* in das Suchfeld ein, klicken Sie für die Speicherbibliotheken von .NET auf **Installieren**, und befolgen Sie dann die Anweisungen zum Installieren des Pakets.

8. Geben Sie *Microsoft.Azure.Management.Resources* in das Suchfeld ein, und klicken Sie für die Ressourcenverwaltungsbibliotheken auf **Installieren**.

Sie können nun die Bibliotheken verwenden, um Ihre Anwendung zu erstellen.

## Schritt 3: Erstellen der Anmeldeinformationen zum Authentifizieren von Anforderungen

Nachdem die Azure Active Directory-Anwendung erstellt und die Authentifizierungsbibliothek installiert wurde, formatieren Sie nun die Anwendungsinformationen zu Anmeldeinformationen, die zum Authentifizieren von Anforderungen an den Azure-Ressourcen-Manager verwendet werden.

1. Öffnen Sie die Datei "Program.cs" für das Projekt, das Sie erstellt haben, und fügen Sie die folgenden using-Anweisungen am Anfang der Datei hinzu:

	```
	using Microsoft.Azure;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.Azure.Management.Storage;
	using Microsoft.Azure.Management.Storage.Models;
	using Microsoft.Azure.Management.Network;
	using Microsoft.Azure.Management.Network.Models;
	using Microsoft.Azure.Management.Compute;
	using Microsoft.Azure.Management.Compute.Models;
	using Microsoft.Rest;
	```

2. Fügen Sie der Program-Klasse die folgende Methode hinzu, um das Token abzurufen, das zum Erstellen der Anmeldeinformationen benötigt wird:

	```
	private static string GetAuthorizationHeader()
	{
		ClientCredential cc = new ClientCredential("{application-id}", "{password}");
		var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
		var result = context.AcquireTokenAsync("https://management.azure.com/", cc);

		if (result == null)
		{
			throw new InvalidOperationException("Failed to obtain the JWT token");
		}

		string token = result.AccessToken;

		return token;
	}
	```

	Ersetzen Sie {application-id} durch die Anwendungs-ID, die Sie zuvor notiert haben, {password} durch das Kennwort, das Sie für die AD-Anwendung gewählt haben, und {tenant-id} durch die Mandanten-ID für Ihr Abonnement. Sie erhalten die Mandanten-ID durch Ausführen von Get-AzureSubscription.

3. Fügen Sie der Main-Methode in der Datei „Program.cs“ den folgenden Code hinzu, um die Anmeldeinformationen zu erstellen:

	```
	var token = GetAuthorizationHeader();
	var credential = new TokenCredentials(token);
	```

5. Speichern Sie die Datei "Program.cs".

## Schritt 4: Hinzufügen von Code zum Registrieren des Anbieters und Erstellen von Ressourcen

### Registrieren Sie die Anbieter, und erstellen Sie eine Ressourcengruppe.

Alle Ressourcen müssen in einer Ressourcengruppe enthalten sein. Bevor Sie Ressourcen zu einer Gruppe hinzufügen können, muss Ihr Abonnement bei Ressourcenanbietern registriert werden.

1. Fügen Sie der Main-Methode der Program-Klasse Variablen hinzu, um die Namen, die Sie für die Ressourcen verwenden möchten, den Speicherort der Ressourcen (z. B. „USA, Westen“), Administratorkontoinformationen und Ihre Abonnement-ID anzugeben:

	```
	var groupName = "{resource-group-name}";
	var ipName = "{public-ip-name}";
	var avSetName = "{availability-set-name}";
	var nicName = "{network-interface-name}";
	var storageName = "{storage-account-name}";
	var vmName = "{virtual-machine-name};  
	var vnetName = "{vnet-name}";
	var subnetName = "{subnet-name}";
	var adminName = "{administrator-account-name}";
	var adminPassword = "{administrator-account-password};
	var location = "{location}";
	var subscriptionId = "{subsciption-id}";
	```

   Ersetzen Sie alle Platzhalter in Klammern durch die Namen, die Sie verwenden möchten. Sie erhalten die Abonnement-ID durch Ausführen von Get-AzureSubscription.

2. Fügen Sie der Program-Klasse die folgende Methode hinzu, um die Ressourcengruppe zu erstellen:

	```
	public static void CreateResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the resource group...");
		var resourceManagementClient = new ResourceManagementClient(credential);
		resourceManagementClient.SubscriptionId = subscriptionId;
		var resourceGroup = new ResourceGroup {
			Location = location
		};
		var rgResult = resourceManagementClient.ResourceGroups.CreateOrUpdate(groupName, resourceGroup);
		Console.WriteLine(rgResult.Properties.ProvisioningState);

		var rpResult = resourceManagementClient.Providers.Register("Microsoft.Storage");
		Console.WriteLine(rpResult.RegistrationState);
		rpResult = resourceManagementClient.Providers.Register("Microsoft.Network");
		Console.WriteLine(rpResult.RegistrationState);
		rpResult = resourceManagementClient.Providers.Register("Microsoft.Compute");
		Console.WriteLine(rpResult.RegistrationState);
	}
	```

3. Fügen Sie der Main-Methode den folgenden Code hinzu, um die gerade hinzugefügte Methode aufzurufen:

	```
	CreateResourceGroup(
		credential,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Erstellen Sie ein Speicherkonto.

Ein Speicherkonto wird benötigt, um die für den virtuellen Computer erstellte virtuelle Festplattendatei zu speichern.

1. Fügen Sie der Program-Klasse die folgende Methode hinzu, um das Speicherkonto zu erstellen:

	```
	public static void CreateStorageAccount(
		TokenCredentials credential,         
		string storageName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the storage account...");
		var storageManagementClient = new StorageManagementClient(credential);
		storageManagementClient.SubscriptionId = subscriptionId;
		var saResult = storageManagementClient.StorageAccounts.Create(
			groupName,
			storageName,
			new StorageAccountCreateParameters()
			{
				AccountType = AccountType.StandardLRS,
				Location = location
			}
		);
		Console.WriteLine(saResult.ProvisioningState);
	}
	```

2. Fügen Sie der Main-Methode der Program-Klasse den folgenden Code hinzu, um die gerade hinzugefügte Methode aufzurufen:

	```
	CreateStorageAccount(
		credential,
		storageName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Erstellen der öffentlichen IP-Adresse

Eine öffentliche IP-Adresse ist für die Kommunikation mit der virtuellen Maschine erforderlich.

1. Fügen Sie der Program-Klasse die folgende Methode hinzu, um die öffentliche IP-Adresse zu erstellen:

	```
	public static void CreatePublicIPAddress(
		TokenCredentials credential,
		string ipName,  
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the public ip...");
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;
		var ipResult = networkManagementClient.PublicIPAddresses.CreateOrUpdate(
			groupName,
			ipName,
			new PublicIPAddress
			{
				Location = location,
				PublicIPAllocationMethod = "Dynamic"
			}
		);
		Console.WriteLine(ipResult.ProvisioningState);
	}
	```

2. Fügen Sie der Main-Methode der Program-Klasse den folgenden Code hinzu, um die gerade hinzugefügte Methode aufzurufen:

	```
	CreatePublicIPAddress(
		credential,
		ipName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Erstellen des virtuellen Netzwerks

Eine virtuelle Maschine, die mit dem Ressourcen-Manager-Bereitstellungsmodell erstellt wurde, muss sich in einem virtuellen Netzwerk befinden.

1. Fügen Sie der Program-Klasse die folgende Methode hinzu, um ein Subnetz und ein virtuelles Netzwerk zu erstellen:

	```
	public static void CreateNetwork(
		TokenCredentials credential,
		string vnetName,
		string subnetName,
		string nicName,
		string ipName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the virtual network...");
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;

		var subnet = new Subnet
		{
			Name = subnetName,
			AddressPrefix = "10.0.0.0/24"
		};

		var address = new AddressSpace {
			AddressPrefixes = new List<string> { "10.0.0.0/16" }
		};

		var vnResult = networkManagementClient.VirtualNetworks.CreateOrUpdate(
			groupName,
			vnetName,
			new VirtualNetwork
			{
				Location = location,
				AddressSpace = address,
				Subnets = new List<Subnet> { subnet }
			}
		);
		Console.WriteLine(vnResult.ProvisioningState);

		var subnetResponse = networkManagementClient.Subnets.Get(
			groupName,
			vnetName,
			subnetName
		);

		var pubipResponse = networkManagementClient.PublicIPAddresses.Get(groupName, ipName);

		Console.WriteLine("Updating the network with the nic...");
		var nicResult = networkManagementClient.NetworkInterfaces.CreateOrUpdate(
			groupName,
			nicName,
			new NetworkInterface
			{
				Location = location,
				IpConfigurations = new List<NetworkInterfaceIPConfiguration>
				{
					new NetworkInterfaceIPConfiguration
					{
						Name = "nicConfig1",
						PublicIPAddress = pubipResponse,
						Subnet = subnetResponse
					}
				}
			}
		);
		Console.WriteLine(vnResult.ProvisioningState);
	}
	```

2. Fügen Sie der Main-Methode der Program-Klasse den folgenden Code hinzu, um die gerade hinzugefügte Methode aufzurufen:

	```
	CreateNetwork(
		credential,
		vnetName,
		subnetName,
		nicName,
		ipName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Verfügbarkeitsgruppe erstellen

Verfügbarkeitsgruppen erleichtern die Verwaltung der Wartung der virtuellen Maschinen, die von Ihrer Anwendung verwendet werden.

1. Fügen Sie der Program-Klasse die folgende Methode hinzu, um die Verfügbarkeitsgruppe zu erstellen:

	```
	public static void CreateAvailabilitySet(
		TokenCredentials credential,
		string avsetName,
		string groupName,
		string subscriptionId,
		string location)
	{
		Console.WriteLine("Creating the availability set...");
		var computeManagementClient = new ComputeManagementClient(credential);
		computeManagementClient.SubscriptionId = subscriptionId;
		var avResult = computeManagementClient.AvailabilitySets.CreateOrUpdate(
			groupName,
			avsetName,
			new AvailabilitySet()
			{
				Location = location
			}
		);
	}
	```

2. Fügen Sie der Main-Methode der Program-Klasse den folgenden Code hinzu, um die gerade hinzugefügte Methode aufzurufen:

	```
	CreateAvailabilitySet(
		credential,
		avsetName,
		groupName,
		subscriptionId,
		location);
	Console.ReadLine();
	```

### Erstellen eines virtuellen Computers

Nachdem Sie alle unterstützenden Ressourcen erstellt haben, können Sie nun einen virtuellen Computer erstellen.

1. Fügen Sie der Program-Klasse die folgende Methode hinzu, um die virtuelle Maschine zu erstellen:

	```
	public static void CreateVirtualMachine(
		TokenCredentials credential,
		string vmName,
		string groupName,
		string nicName,
		string avsetName,
		string storageName,
		string adminName,
		string adminPassword,
		string subscriptionId,
		string location)
	{
		var networkManagementClient = new NetworkManagementClient(credential);
		networkManagementClient.SubscriptionId = subscriptionId;
		var nic = networkManagementClient.NetworkInterfaces.Get(groupName, nicName);

		var computeManagementClient = new ComputeManagementClient(credential);
		computeManagementClient.SubscriptionId = subscriptionId;
		var avSet = computeManagementClient.AvailabilitySets.Get(groupName, avsetName);

		Console.WriteLine("Creating the virtual machine...");
		var vm = computeManagementClient.VirtualMachines.CreateOrUpdate(
			groupName,
			vmName,
			new VirtualMachine
			{
				Location = location,
				AvailabilitySet = new Microsoft.Azure.Management.Compute.Models.SubResource
				{
					Id = avSet.Id
				},
				HardwareProfile = new HardwareProfile
				{
					VmSize = "Standard_A0"
				},
				OsProfile = new OSProfile
				{
					AdminUsername = adminName,
					AdminPassword = adminPassword,
					ComputerName = vmName,
					WindowsConfiguration = new WindowsConfiguration
					{
						ProvisionVMAgent = true
					}
				},
				NetworkProfile = new NetworkProfile
				{
					NetworkInterfaces = new List<NetworkInterfaceReference>
					{
						new NetworkInterfaceReference { Id = nic.Id }
					}
				},
				StorageProfile = new StorageProfile
				{
					ImageReference = new ImageReference
					{
						Publisher = "MicrosoftWindowsServer",
						Offer = "WindowsServer",
						Sku = "2012-R2-Datacenter",
						Version = "latest"
					},
					OsDisk = new OSDisk
					{
						Name = "mytestod1",
						CreateOption = "FromImage",
						Vhd = new VirtualHardDisk
						{
							Uri = "http://" + storageName + ".blob.core.windows.net/vhds/mytestod1.vhd"
						}
					}
				}
			}
		);
		Console.WriteLine(vm.ProvisioningState);
	}
	```

	>[AZURE.NOTE]In diesem Tutorial wird eine virtuelle Maschine erstellt, auf dem eine Version des Windows Server-Betriebssystems ausgeführt wird. Weitere Informationen zur Auswahl von anderen Images finden Sie unter [Navigieren zwischen und Auswählen von Images für virtuelle Azure-Maschinen mit Windows PowerShell und der Azure-Befehlszeilenschnittstelle](resource-groups-vm-searching.md).

2. Fügen Sie der Main-Methode den folgenden Code hinzu, um die gerade hinzugefügte Methode aufzurufen:

	```
	CreateVirtualMachine(
		credential,
		vmName,
		groupName,
		nicName,
		avsetName,
		storageName,
		adminName,
		adminPassword,
		subscriptionId,
		location);
	Console.ReadLine();
	```

##Schritt 5: Hinzufügen von Code zum Löschen der Ressourcen

Da in Azure die genutzten Ressourcen in Rechnung gestellt werden, empfiehlt es sich grundsätzlich, nicht mehr benötigte Ressourcen zu löschen. Wenn Sie die virtuellen Computer und alle unterstützenden Ressourcen löschen möchten, müssen Sie lediglich die Ressourcengruppe löschen.

1. Fügen Sie der Program-Klasse die folgende Methode hinzu, um die Ressourcengruppe zu löschen:

	```
	public static void DeleteResourceGroup(
		TokenCredentials credential,
		string groupName,
		string subscriptionId)
	{
		Console.WriteLine("Deleting resource group...");
		var resourceGroupClient = new ResourceManagementClient(credential);
		resourceGroupClient.ResourceGroups.DeleteAsync(groupName);
	}
	```

2. Fügen Sie der Main-Methode den folgenden Code hinzu, um die gerade hinzugefügte Methode aufzurufen:

	```
	DeleteResourceGroup(
		credential,
		groupName,
		subscriptionId);
	Console.ReadLine();
	```

## Schritt 6: Ausführen der Konsolenanwendung

1. Klicken Sie zum Ausführen der Konsolenanwendung in Visual Studio auf **Starten**, und melden Sie sich dann bei Azure AD mit demselben Benutzernamen und Kennwort an, die Sie für Ihr Abonnement verwenden.

2. Drücken Sie nach der Rückgabe jedes Statuscodes die **EINGABETASTE**, um jede Ressource zu erstellen. Nachdem der virtuelle Computer erstellt wurde, führen Sie den nächsten Schritt aus, bevor Sie die EINGABETASTE drücken, um alle Ressourcen zu löschen.

	Die vollständige Ausführung dieser Konsolenanwendung von Anfang bis zum Ende sollte etwa 5 Minuten dauern. Bevor Sie die EINGABETASTE drücken, um das Löschen der Ressourcen zu starten, können Sie sich ein paar Minuten Zeit nehmen, um die Erstellung der Ressourcen im Azure-Portal zu überprüfen, bevor Sie diese löschen.

3. Navigieren Sie im Azure-Portal zu den Überwachungsprotokollen, um den Status der Ressourcen anzuzeigen:

	![Erstellen einer AD-Anwendung](./media/virtual-machines-arm-deployment/crpportal.png)

<!---HONumber=AcomDC_0107_2016-->