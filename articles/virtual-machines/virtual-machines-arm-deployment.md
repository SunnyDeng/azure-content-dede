<properties
	pageTitle="Bereitstellen von Azure-Ressourcen mithilfe der Computing-, Netzwerk- und Speicherbibliotheken von .NET"
	description="Erfahren Sie, wie Sie einige der verfügbaren Clients in den Computing-, Netzwerk- und Speicherbibliotheken von .NET zum Erstellen und Löschen von Ressourcen in Microsoft Azure verwenden."
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
	ms.date="07/28/2015"
	ms.author="davidmu"/>

# Bereitstellen von Azure-Ressourcen mithilfe der Computing-, Netzwerk- und Speicherbibliotheken von .NET

In diesem Lernprogramm erfahren Sie, wie Sie einige der verfügbaren Clients in den Computing-, Netzwerk- und Speicherbibliotheken von .NET zum Erstellen und Löschen von Ressourcen in Microsoft Azure verwenden. Außerdem erfahren Sie, wie Sie die Anforderungen an den Azure-Ressourcen-Manager mithilfe von Azure Active Directory authentifizieren.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Zur Durchführung dieses Lernprogramms benötigen Sie außerdem Folgendes:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Azure-Speicherkonto](../storage-create-storage-account.md)
- [Windows Management Framework 3.0 ](http://www.microsoft.com/en-us/download/details.aspx?id=34595) oder [Windows Management Framework 4.0](http://www.microsoft.com/en-us/download/details.aspx?id=40855)
- [Azure PowerShell](../install-configure-powershell.md)

Die Durchführung dieser Schritte dauert etwa 30 Minuten.

## Schritt 1: Hinzufügen einer Anwendung zu Azure AD und Festlegen von Berechtigungen

Um Azure AD zum Authentifizieren von Anforderungen an den Azure-Ressourcen-Manager zu verwenden, muss dem Standardverzeichnis eine Anwendung hinzugefügt werden. Gehen Sie zum Hinzufügen einer Anwendung folgendermaßen vor:

1. Öffnen Sie eine Azure PowerShell-Eingabeaufforderung, und führen Sie dann diesen Befehl aus:

        Switch-AzureMode –Name AzureResourceManager

2. Legen Sie das Azure-Konto fest, das Sie für dieses Lernprogramm verwenden möchten. Führen Sie diesen Befehl aus, und geben Sie die Anmeldeinformationen für Ihr Abonnement nach Aufforderung ein:

	    Add-AzureAccount

3. Ersetzen Sie im folgenden Befehl {password} durch das Kennwort, das Sie verwenden möchten, und führen Sie ihn anschließend aus, um die Anwendung zu erstellen:

	    New-AzureADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

4. Notieren Sie den ApplicationId-Wert in der Antwort aus dem vorherigen Schritt. Sie werden ihn später im Lernprogramm benötigen:

	![Erstellen einer AD-Anwendung](./media/virtual-machines-arm-deployment/azureapplicationid.png)

	>[AZURE.NOTE]Sie finden die Anwendungs-ID auch im Client-ID-Feld der Anwendung im Verwaltungsportal.

5. Ersetzen Sie {application-id} durch die ID, die Sie gerade notiert haben, und erstellen Sie dann den Dienstprinzipal für die Anwendung:

        New-AzureADServicePrincipal -ApplicationId {application-id} 

6. Legen Sie die Berechtigung zum Verwenden der Anwendung fest:

	    New-AzureRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Schritt 2: Erstellen eines Visual Studio-Projekts und Installation der Bibliotheken

NuGet-Pakete sind die einfachste Möglichkeit, um die Bibliotheken zu installieren, die Sie zum Abschluss dieses Lernprogramms benötigen. Sie müssen die Azure-Ressourcenverwaltungsbibliothek, die Azure Active Directory-Authentifizierungsbibliothek und die Computerressourcenanbieter-Bibliothek installieren. Führen Sie folgende Schritte aus, um diese Bibliotheken in Visual Studio abzurufen:

1. Klicken Sie auf **Datei** > **Neu** > **Projekt**.

2. Wählen Sie unter **Vorlagen** > **Visual C#** **Konsolenanwendung** aus, geben Sie den Namen und Speicherort des Projekts ein, und klicken Sie dann auf **OK**.

3. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen und anschließend auf **NuGet-Pakete verwalten**.

4. Geben Sie *Active Directory* in das Suchfeld ein, klicken Sie für das Active Directory-Authentifizierungsbibliothek-Paket auf **Installieren**, und befolgen Sie dann die Anweisungen zum Installieren des Pakets.

5. Wählen Sie am oberen Rand der Seite **Vorabversion einschließen** aus. Geben Sie *Azure Compute* in das Suchfeld ein, klicken Sie für die Computing-Bibliotheken von .NET auf **Installieren**, und befolgen Sie dann die Anweisungen zum Installieren des Pakets.

6. Geben Sie *Azure-Netzwerk* in das Suchfeld ein, klicken Sie für die Netzwerkbibliotheken von .NET auf **Installieren**, und befolgen Sie dann die Anweisungen zum Installieren des Pakets.

7. Geben Sie *Azure-Speicher* in das Suchfeld ein, klicken Sie für die Speicherbibliotheken von .NET auf **Installieren**, und befolgen Sie dann die Anweisungen zum Installieren des Pakets.

8. Geben Sie *Azure-Ressourcenverwaltung* in das Suchfeld ein, und klicken Sie für die Ressourcenverwaltungsbibliotheken auf **Installieren**.

Sie können nun die Bibliotheken verwenden, um Ihre Anwendung zu erstellen.

## Schritt 3: Erstellen der Anmeldeinformationen zum Authentifizieren von Anforderungen

Nachdem die Azure Active Directory-Anwendung erstellt und die Authentifizierungsbibliothek installiert wurde, formatieren Sie nun die Anwendungsinformationen zu Anmeldeinformationen, die zum Authentifizieren von Anforderungen an den Azure-Ressourcen-Manager verwendet werden. Gehen Sie wie folgt vor:

1.	Öffnen Sie die Datei "Program.cs" für das Projekt, das Sie erstellt haben, und fügen Sie die folgenden using-Anweisungen am Anfang der Datei hinzu:

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


2. Fügen Sie der Program-Klasse die folgende Methode hinzu, um das Token abzurufen, das zum Erstellen der Anmeldeinformationen benötigt wird:

		private static string GetAuthorizationHeader()
        {
          ClientCredential cc = new ClientCredential("{application-id}", "{password}");
            var context = new AuthenticationContext("https://login.windows.net/{tenant-id}");
            var result = context.AcquireToken("https://management.azure.com/", cc);
          
          if (result == null)
          {
            throw new InvalidOperationException("Failed to obtain the JWT token");
          }

          string token = result.AccessToken;

          return token;
        }

	Ersetzen Sie {application-id} durch die Anwendungs-ID, die Sie zuvor notiert haben, {password} durch das Kennwort, das Sie für die AD-Anwendung gewählt haben, und {tenant-id} durch die Mandanten-ID für Ihr Abonnement. Sie erhalten die Mandanten-ID durch Ausführen von Get-AzureSubscription.

3.	Fügen Sie der Main-Methode in der Datei "Program.cs" den folgenden Code hinzu, um die Anmeldeinformationen zu erstellen:

		var token = GetAuthorizationHeader();
		var credential = new TokenCloudCredentials("{subscription-id}", token);

	Ersetzen Sie {subscription-id} durch Ihre Abonnement-ID.

4.	Speichern Sie die Datei "Program.cs".

## Schritt 4: Hinzufügen von Code zum Registrieren des Anbieters und Erstellen von Ressourcen

### Registrieren Sie die Anbieter, und erstellen Sie eine Ressourcengruppe.

Ressourcen werden einer Ressourcengruppe immer bereitgestellt. Verwenden Sie die Klassen [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) und [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx), um die Ressourcengruppe zu erstellen, der die Ressourcen bereitgestellt werden.

1.	Fügen Sie der Program-Klasse die folgende Methode hinzu, um die Ressourcengruppe zu erstellen:

		public async static void CreateResourceGroup(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the resource group...");
		  
          using (var resourceManagementClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync("mytestrg1", new ResourceGroup { Location = "West US" });
            Console.WriteLine(rgResult.StatusCode);
            var rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Storage");
            Console.WriteLine(rpResult.StatusCode);
            rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Network");
            Console.WriteLine(rpResult.StatusCode);
            rpResult = await resourceManagementClient.Providers.RegisterAsync("Microsoft.Compute");
            Console.WriteLine(rpResult.StatusCode);
		  }
		}

2.	Fügen Sie der Main-Methode den folgenden Code hinzu, um die gerade hinzugefügte Methode aufzurufen:

		CreateResourceGroup(credential);
		Console.ReadLine();

###Erstellen Sie ein Speicherkonto.

Ein Speicherkonto wird benötigt, um die für den virtuellen Computer erstellte virtuelle Festplattendatei zu speichern.

1.	Fügen Sie der Program-Klasse die folgende Methode hinzu, um das Speicherkonto zu erstellen:

		public async static void CreateStorageAccount(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the storage account...");
          
          using (var storageManagementClient = new StorageManagementClient(credential))
          {
            var saResult = await storageManagementClient.StorageAccounts.CreateAsync(
              "mytestrg1",
              "mytestsa1",
              new StorageAccountCreateParameters()
              { AccountType = AccountType.StandardLRS, Location = "West US" } );
            Console.WriteLine(saResult.StatusCode);
          }
        }

3.	Fügen Sie der Main-Methode den folgenden Code hinzu, um die gerade hinzugefügte Methode aufzurufen:
		
		CreateStorageAccount(credential);
		Console.ReadLine();

###Erstellen eines virtuellen Netzwerks

Ein virtueller Computer ist am produktivsten, wenn er einem virtuellen Netzwerk hinzugefügt wird.

1.	Fügen Sie der Program-Klasse die folgende Methode hinzu, um ein Subnetz, eine öffentliche IP-Adresse und ein virtuelles Netzwerk zu erstellen:

		public async static void CreateNetwork(TokenCloudCredentials credential)
        {
          Console.WriteLine("Creating the virtual network...");
          using (var networkClient = new NetworkResourceProviderClient(credential))
          {
            var subnet = new Subnet
            {
              Name = "mytestsn1",
              AddressPrefix = "10.0.0.0/24"
            };

            var vnResult = await networkClient.VirtualNetworks.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestvn1",
              new VirtualNetwork
              {
                Location = "West US",
                AddressSpace = new AddressSpace { AddressPrefixes = new List<string> { "10.0.0.0/16" } },
                Subnets = new List<Subnet> { subnet }
              });
            Console.WriteLine(vnResult.StatusCode);

            var subnetResponse = await networkClient.Subnets.GetAsync(
              "mytestrg1",
              "mytestvn1",
              "mytestsn1"
            );

            Console.WriteLine("Creating the public ip...");
            vnResult = await networkClient.PublicIpAddresses.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestip1",
              new PublicIpAddress
              {
                Location = "West US",
                PublicIpAllocationMethod = "Dynamic"
              });
            Console.WriteLine(vnResult.StatusCode);

            var pubipResponse = await networkClient.PublicIpAddresses.GetAsync("mytestrg1", "mytestip1");

            Console.WriteLine("Updating the network with the nic...");
            vnResult = await networkClient.NetworkInterfaces.CreateOrUpdateAsync(
              "mytestrg1",
              "mytestnic1",
              new NetworkInterface
              {
                Name = "mytestnic1",
                Location = "West US",
                IpConfigurations = new List<NetworkInterfaceIpConfiguration>
                {
                  new NetworkInterfaceIpConfiguration
                  {
                    Name = "nicconfig1",
                    PublicIpAddress = new ResourceId
                    {
                      Id = pubipResponse.PublicIpAddress.Id
                    },
                    Subnet = new ResourceId
                    {
                      Id = subnetResponse.Subnet.Id
                    }
                  }
                }
              });
            Console.WriteLine(vnResult.StatusCode);
          }
        }

2.	Fügen Sie der Main-Methode den folgenden Code hinzu, um die gerade hinzugefügte Methode aufzurufen:

		CreateNetwork(credential);
		Console.ReadLine();

###Erstellen eines virtuellen Computers

Nachdem Sie alle unterstützenden Ressourcen erstellt haben, können Sie nun einen virtuellen Computer erstellen.

1.	Fügen Sie der Program-Klasse die folgende Methode hinzu, um den virtuellen Computer zu erstellen:

		public async static void CreateVirtualMachine(TokenCloudCredentials credential)
        {
          using (var computeClient = new ComputeManagementClient(credential))
          {
            Console.WriteLine("Creating the availability set...");
            var avSetResponse = await computeClient.AvailabilitySets.CreateOrUpdateAsync(
              "mytestrg1",
              new AvailabilitySet
              {
                Name = "mytestav1",
                Location = "West US"
              } );
            Console.WriteLine(avSetResponse.StatusCode);
                
            var networkClient = new NetworkResourceProviderClient(credential);
            var nicResponse = await networkClient.NetworkInterfaces.GetAsync("mytestrg1", "mytestnic1");

            Console.WriteLine("Creating the virtual machine...");
            var putVMResponse = await computeClient.VirtualMachines.CreateOrUpdateAsync(
              "mytestrg1",
              new VirtualMachine
              {
                Name = "mytestvm1",
                Location = "West US",
                AvailabilitySetReference = new AvailabilitySetReference
                {
                  ReferenceUri = avSetResponse.AvailabilitySet.Id
                },
                HardwareProfile = new HardwareProfile
                {
                  VirtualMachineSize = "Standard_A0"
                },
                OSProfile = new OSProfile
                {
                  AdminUsername = "mytestuser1",
                  AdminPassword = "Mytestpass1",
                  ComputerName = "mytestsv1",
                  WindowsConfiguration = new WindowsConfiguration
                  {
                    ProvisionVMAgent = true
                  }
                },
                NetworkProfile = new NetworkProfile
                {
                  NetworkInterfaces = new List<NetworkInterfaceReference>
                  {
                    new NetworkInterfaceReference
                    {
                      ReferenceUri = nicResponse.NetworkInterface.Id
                    }
                  }
                },
                StorageProfile = new StorageProfile
                {
                  SourceImage = new SourceImageReference
                  {
                    ReferenceUri = "/{subscription-id}/services/images/a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201502.01-en.us-127GB.vhd"
                  },
                  OSDisk = new OSDisk
                  {
                    Name = "myosdisk1",
                    CreateOption = "FromImage",
                    VirtualHardDisk = new VirtualHardDisk 
                    {
                      Uri = "http://mytestsa1.blob.core.windows.net/vhds/myosdisk1.vhd"
                    }
                  }
                }
              } );
            Console.WriteLine(putVMResponse.StatusCode);
          }
        }

	>[AZURE.NOTE]VHD-Imagenamen ändern sich regelmäßig im Imagekatalog, daher benötigen Sie einen aktuellen Imagenamen, um den virtuellen Computer bereitzustellen. Weitere Informationen hierzu finden Sie unter [Verwalten von Images mithilfe von Windows PowerShell](https://msdn.microsoft.com/library/azure/dn790330.aspx). Ersetzen Sie dann {source-image-name} durch den Namen der VHD-Datei, die Sie verwenden möchten. Beispiel: "a699494373c04fc0bc8f2bb1389d6106__Windows-Server-2012-R2-201411.01-en.us-127GB.vhd".

	Ersetzen Sie {subscription-id} durch Ihre Abonnement-ID.

2.	Fügen Sie der Main-Methode den folgenden Code hinzu, um die gerade hinzugefügte Methode aufzurufen:

		CreateVirtualMachine(credential);
        Console.ReadLine();

##Schritt 5: Hinzufügen von Code zum Löschen der Ressourcen

Da in Azure die genutzten Ressourcen in Rechnung gestellt werden, empfiehlt es sich grundsätzlich, nicht mehr benötigte Ressourcen zu löschen. Wenn Sie die virtuellen Computer und alle unterstützenden Ressourcen löschen möchten, müssen Sie lediglich die Ressourcengruppe löschen.

1.	Fügen Sie der Program-Klasse die folgende Methode hinzu, um die Ressourcengruppe zu löschen:

		public async static void DeleteResourceGroup(TokenCloudCredentials credential)
        {
          Console.WriteLine("Deleting resource group...");
          using (var resourceGroupClient = new ResourceManagementClient(credential))
          {
            var rgResult = await resourceGroupClient.ResourceGroups.DeleteAsync("mytestrg1");
            Console.WriteLine(rgResult.StatusCode);
          }
        }

2.	Fügen Sie der Main-Methode den folgenden Code hinzu, um die gerade hinzugefügte Methode aufzurufen:

		DeleteResourceGroup(credential);
        Console.ReadLine();

##Schritt 6: Ausführen der Konsolenanwendung

1.	Klicken Sie zum Ausführen der Konsolenanwendung in Visual Studio auf **Starten**, und melden Sie sich dann bei Azure AD mit demselben Benutzernamen und Kennwort an, die Sie für Ihr Abonnement verwenden.

2.	Drücken Sie nach der Rückgabe jedes Statuscodes die **EINGABETASTE**, um jede Ressource zu erstellen. Nachdem der virtuelle Computer erstellt wurde, führen Sie den nächsten Schritt aus, bevor Sie die EINGABETASTE drücken, um alle Ressourcen zu löschen.

	Die vollständige Ausführung dieser Konsolenanwendung von Anfang bis zum Ende sollte etwa 5 Minuten dauern. Bevor Sie die EINGABETASTE drücken, um das Löschen der Ressourcen zu starten, können Sie sich ein paar Minuten Zeit nehmen, um die Erstellung der Ressourcen im Azure-Vorschauportal zu überprüfen, bevor Sie diese löschen.

3. Navigieren Sie im Azure-Vorschauportal zu den Überwachungsprotokollen, um den Status der Ressourcen anzuzeigen:

	![Erstellen einer AD-Anwendung](./media/virtual-machines-arm-deployment/crpportal.png)

<!----HONumber=July15_HO5-->