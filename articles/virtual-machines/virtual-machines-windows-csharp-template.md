<properties
	pageTitle="Bereitstellen von Azure-Ressourcen mithilfe einer Vorlage | Microsoft Azure"
	description="In diesem Lernprogramm erfahren Sie, wie Sie einige der verfügbaren Clients in der Azure-Ressourcenverwaltungsbibliothek verwenden, um einen virtuellen Computer, ein virtuelles Netzwerk und ein Speicherkontos bereitzustellen."
	services="virtual-machines-windows,virtual-networks,storage"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="virtual-machines-windows"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/06/2016"
	ms.author="davidmu"/>

# Bereitstellen von Azure-Ressourcen mithilfe von .NET-Bibliotheken und einer Vorlage

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.


Durch das Verwenden von Ressourcengruppen und Vorlagen, können Sie alle Ressourcen verwalten, die zusammen Ihre Anwendung unterstützen. In diesem Lernprogramm erfahren Sie, wie Sie einige der verfügbaren Clients in der Azure-Ressourcenverwaltungsbibliothek verwenden und eine Vorlage zum Bereitstellen eines virtuellen Computers, virtuellen Netzwerks und Speicherkontos erstellen können.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Zur Durchführung dieses Lernprogramms benötigen Sie außerdem Folgendes:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Azure-Speicherkonto](../storage/storage-create-storage-account.md)
- [Windows Management Framework 3.0 ](http://www.microsoft.com/download/details.aspx?id=34595) oder [Windows Management Framework 4.0](http://www.microsoft.com/download/details.aspx?id=40855)

>[AZURE.NOTE] Das Speicherkonto, das Sie an dieser Stelle erstellen, wird zum Speichern der Vorlage verwendet. Ein weiteres Speicherkonto wird erstellt, wenn Sie die Vorlage bereitstellen, die zum Speichern des Datenträgers für den virtuellen Computer verwendet wird. Erstellen Sie in diesem Speicherkonto einen Container mit der Bezeichnung „templates“.

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Die Durchführung dieser Schritte dauert etwa 30 Minuten.

## Schritt 1: Hinzufügen einer Anwendung zu Azure AD und Festlegen von Berechtigungen

Um Azure AD zum Authentifizieren von Anforderungen an den Azure-Ressourcen-Manager zu verwenden, muss dem Standardverzeichnis eine Anwendung hinzugefügt werden. Gehen Sie zum Hinzufügen einer Anwendung folgendermaßen vor:

1. Öffnen Sie eine Azure PowerShell-Eingabeaufforderung, führen Sie dann diesen Befehl aus, und geben Sie die Anmeldeinformationen für Ihr Abonnement nach Aufforderung ein:

			Login-AzureRmAccount

2. Ersetzen Sie im folgenden Befehl {password} durch das Kennwort, das Sie verwenden möchten, und führen Sie ihn anschließend aus, um die Anwendung zu erstellen:

			New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE] Notieren Sie die Anwendungs-ID, die nach dem Erstellen der Anwendung zurückgegeben wird, da Sie diese für den nächsten Schritt benötigen. Sie finden die Anwendungs-ID auch im Client-ID-Feld der Anwendung im Active Directory-Bereich des Azure-Portals.

3. Ersetzen Sie {application-id} durch die ID, die Sie gerade notiert haben, und erstellen Sie dann den Dienstprinzipal für die Anwendung:

			New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. Legen Sie die Berechtigung zum Verwenden der Anwendung fest:

			New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Schritt 2: Erstellen des Visual Studio-Projekts, der Vorlagendatei und der Parameterdatei

### Erstellen der Vorlagendatei

Mithilfe der Azure-Ressourcen-Manager-Vorlage können Sie die Azure-Ressourcen gemeinsam bereitstellen und verwalten. Sie verwenden hierfür eine JSON-Beschreibung der Ressourcen sowie der zugeordneten Bereitstellungsparameter. Die Vorlage, die Sie in diesem Tutorial erstellen, ähnelt einer Vorlage aus dem Vorlagenkatalog. Weitere Informationen finden Sie unter [Bereitstellen eines einfachen virtuellen Windows-Computers in der Region „USA, Westen“](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

Führen Sie in Visual Studio Folgendes aus:

1. Klicken Sie auf **Datei** > **Neu** > **Projekt**.

2. Wählen Sie unter **Vorlagen** > **Visual C#** **Konsolenanwendung** aus, geben Sie den Namen und Speicherort des Projekts ein, und klicken Sie dann auf **OK**.

3. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen und dann auf **Hinzufügen** > **Neues Element**.

4. Wählen Sie im Fenster "Neues Element hinzufügen", **Textdatei** aus, geben Sie *VirtualMachineTemplate.json* als Namen ein, und klicken Sie dann auf **Hinzufügen**.

5. Öffnen Sie die Datei "VirtualMachineTemplate.json", und fügen Sie die öffnenden und schließenden Klammern, das erforderliche Schemaelement und das erforderliche ContentVersion-Element ein:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
	}
	```

6. [Parameter](../resource-group-authoring-templates.md#parameters) sind nicht immer erforderlich, aber sie erleichtern die Vorlagenverwaltung. Sie beschreiben den Typ des Werts, ggf. den Standardwert und möglicherweise die zulässigen Werte des Parameters. Für dieses Lernprogramm werden die Parameter, mit denen ein virtueller Computer, ein Speicherkonto und ein virtuelles Netzwerk erstellt werden, der Vorlage hinzugefügt.

    Fügen Sie das Parameterelement und seine untergeordneten Elemente nach dem ContentVersion-Element hinzu:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUserName": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": [ "2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter" ]
			}
		},
 	}
	```

7. [Variablen](../resource-group-authoring-templates.md#variables) können in einer Vorlage verwendet werden, um die Werte festzulegen, die sich häufig ändern, oder um die Werte festzulegen, die aus einer Kombination von Parameterwerten erstellt werden müssen.

    Fügen Sie das Variablenelement nach dem Parameterabschnitt hinzu:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUsername": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": ["2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter"]
			}
		},
		"variables": {
			"location": "West US",
			"imagePublisher": "MicrosoftWindowsServer",
			"imageOffer": "WindowsServer",
			"OSDiskName": "osdiskforwindowssimple",
			"nicName": "myVMnic1",
			"addressPrefix": "10.0.0.0/16",
			"subnetName": "Subnet",
			"subnetPrefix": "10.0.0.0/24",
			"storageAccountType": "Standard_LRS",
			"publicIPAddressName": "myPublicIP",
			"publicIPAddressType": "Dynamic",
			"vmStorageAccountContainerName": "vhds",
			"vmName": "MyWindowsVM",
			"vmSize": "Standard_A2",
			"virtualNetworkName": "MyVNET",
			"vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
			"subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]"
		},
	}
	```

8. [Ressourcen](../resource-group-authoring-templates.md#resources) wie der virtuelle Computer, das virtuelle Netzwerk und das Speicherkonto werden als Nächstes in der Vorlage definiert.

    Fügen Sie nach dem Variablenabschnitt den Ressourcenabschnitt hinzu:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "type": "string" },
			"adminUsername": { "type": "string" },
			"adminPassword": { "type": "securestring" },
			"dnsNameForPublicIP": { "type": "string" },
			"windowsOSVersion": {
				"type": "string",
				"defaultValue": "2012-R2-Datacenter",
				"allowedValues": [ "2008-R2-SP1", "2012-Datacenter", "2012-R2-Datacenter"]
			}
		},
		"variables": {
			"location": "West US",
			"imagePublisher": "MicrosoftWindowsServer",
			"imageOffer": "WindowsServer",
			"OSDiskName": "osdiskforwindowssimple",
			"nicName": "myVMnic1",
			"addressPrefix": "10.0.0.0/16",
			"subnetName": "Subnet",
			"subnetPrefix": "10.0.0.0/24",
			"storageAccountType": "Standard_LRS",
			"publicIPAddressName": "myPublicIP",
			"publicIPAddressType": "Dynamic",
			"vmStorageAccountContainerName": "vhds",
			"vmName": "MyWindowsVM",
			"vmSize": "Standard_A2",
			"virtualNetworkName": "MyVNET",
			"vnetID":"[resourceId('Microsoft.Network/virtualNetworks',variables('virtualNetworkName'))]",
			"subnet1Ref": "[concat(variables('vnetID'),'/subnets/',variables('subnet1Name'))]"
		},
		"resources": [
			{
  			"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Storage/storageAccounts",
				"name": "[parameters('newStorageAccountName')]",
				"location": "[variables('location')]",
				"properties": {
					"accountType": "[variables('storageAccountType')]"
				}
			},
			{
				"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Network/publicIPAddresses",
				"name": "[variables('publicIPAddressName')]",
				"location": "[variables('location')]",
				"properties": {
					"publicIPAllocationMethod": "[variables('publicIPAddressType')]",
					"dnsSettings": {
						"domainNameLabel": "[parameters('dnsNameForPublicIP')]"
					}
				}
			},
			{
				"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Network/virtualNetworks",
				"name": "[variables('virtualNetworkName')]",
				"location": "[variables('location')]",
				"properties": {
					"addressSpace": {
						"addressPrefixes": [ "[variables('addressPrefix')]" ]
					},
					"subnets": [ {
						"name": "[variables('subnetName')]",
						"properties": {
							"addressPrefix": "[variables('subnetPrefix')]"
						}
					} ]
				}
			},
			{
				"apiVersion": "2015-05-01-preview",
				"type": "Microsoft.Network/networkInterfaces",
				"name": "[variables('nicName')]",
				"location": "[variables('location')]",
				"dependsOn": [
					"[concat('Microsoft.Network/publicIPAddresses/', variables('publicIPAddressName'))]",
					"[concat('Microsoft.Network/virtualNetworks/', variables('virtualNetworkName'))]"
				],
				"properties": {
					"ipConfigurations": [ {
						"name": "ipconfig1",
						"properties": {
							"privateIPAllocationMethod": "Dynamic",
							"publicIPAddress": {
								"id": "[resourceId('Microsoft.Network/publicIPAddresses', variables('publicIPAddressName'))]"
							},
							"subnet": {
								"id": "[variables('subnetRef')]"
							}
						}
					} ]
				}
			},
			{
				"apiVersion": "2015-06-15",
				"type": "Microsoft.Compute/virtualMachines",
				"name": "[variables('vmName')]",
				"location": "[variables('location')]",
				"dependsOn": [
					"[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
					"[concat('Microsoft.Network/networkInterfaces/', variables('nicName'))]"
				],
				"properties": {
					"hardwareProfile": {
						"vmSize": "[variables('vmSize')]"
					},
					"osProfile": {
						"computerName": "[variables('vmName')]",
						"adminUsername": "[parameters('adminUsername')]",
						"adminPassword": "[parameters('adminPassword')]",
					},
					"storageProfile": {
						"imageReference": {
							"publisher": "[variables('imagePublisher')]",
							"offer": "[variables('imageOffer')]",
							"sku": "[parameters('windowsOSVersion')]",
							"version" : "latest"
						},
						"osDisk": {
							"name": "osdisk",
							"vhd": {
								"uri": "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',variables('vmStorageAccountContainerName'),'/',variables('OSDiskName'),'.vhd')]"
							},
							"caching": "ReadWrite",
							"createOption": "FromImage"
						}
					},
					"networkProfile": {
						"networkInterfaces" : [ {
							"id": "[resourceId('Microsoft.Network/networkInterfaces',variables('nicName'))]"
						} ]
					}
				}
			} ]
		}
		```

9. Speichern Sie Vorlagendatei, die Sie erstellt haben.

### Erstellen der Parameterdatei

Um Werte für die Ressourcenparameter anzugeben, die in der Vorlage definiert wurden, müssen Sie eine Parameterdatei erstellen, die die Werte enthält und sie mit der Vorlage an den Ressourcen-Manager senden. Führen Sie in Visual Studio Folgendes aus:

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen und dann auf **Hinzufügen** > **Neues Element**.

2. Wählen Sie im Fenster "Neues Element hinzufügen", **Textdatei** aus, geben Sie *Parameters.json* als Namen ein, und klicken Sie dann auf **Hinzufügen**.

3. Öffnen Sie die Datei "parameters.json", und fügen Sie den folgenden JSON-Inhalt hinzu:

	```
	{
		"$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json",
		"contentVersion": "1.0.0.0",
		"parameters": {
			"newStorageAccountName": { "value": "mytestsa1" },
			"adminUserName": { "value": "mytestacct1" },
			"adminPassword": { "value": "mytestpass1" },
			"dnsNameForPublicIP": { "value": "mytestdns1" }
		}
	}
	```

    >[AZURE.NOTE] In diesem Tutorial wird ein virtueller Computer erstellt, auf dem eine Version des Betriebssystems Windows Server ausgeführt wird. Weitere Informationen zur Auswahl von anderen Images finden Sie unter [Navigieren zwischen und Aussuchen von Images virtueller Azure-Computer mit Windows PowerShell und der Azure-Befehlszeilenschnittstelle](virtual-machines-linux-cli-ps-findimage.md).


4. Speichern Sie Parameterdatei, die Sie erstellt haben.

### Hochladen der Dateien

Der Azure-Ressourcen-Manager greift auf die Vorlagendatei und die Parameterdatei in einem Azure-Speicherkonto zu. Um die Dateien im ersten von Ihnen erstellten Speicher abzulegen, führen Sie folgende Schritte aus:

1. Öffnen Sie den Server-Explorer, und navigieren Sie zu dem Container im Speicherkonto, in dem Sie die Datei platzieren möchten. In diesem Lernprogramm heißt der Container, in dem sich die Vorlage befindet "templates".

2. Klicken Sie in der rechten, oberen Ecke des Vorlagencontainerbereichs auf das Symbol zum Hochladen von Blobs, navigieren Sie zur Datei "VirtualMachineTemplate.json", die Sie erstellt haben, und klicken Sie dann auf **Öffnen**.

3. Klicken Sie erneut auf das Symbol zum Hochladen von Blobs, navigieren Sie zur Datei "Parameters.json", die Sie erstellt haben, und klicken Sie dann auf **Öffnen**.

## Schritt 3: Installieren der Bibliotheken

NuGet-Pakete sind die einfachste Möglichkeit, um die Bibliotheken zu installieren, die Sie zum Abschluss dieses Lernprogramms benötigen. Sie müssen die Azure-Ressourcenverwaltungsbibliothek und die Azure Active Directory-Authentifizierungsbibliothek installieren. Führen Sie folgende Schritte aus, um diese Bibliotheken in Visual Studio abzurufen:

1. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen und anschließend auf **NuGet-Pakete verwalten**.

2. Geben Sie *Active Directory* in das Suchfeld ein, klicken Sie für das Active Directory-Authentifizierungsbibliothek-Paket auf **Installieren**, und befolgen Sie dann die Anweisungen zum Installieren des Pakets.

3. Wählen Sie am oberen Rand der Seite **Vorabversion einschließen** aus. Geben Sie *Microsoft.Azure.Management.Resources* in das Suchfeld ein, klicken Sie für die Microsoft Azure-Ressourcenverwaltungsbibliotheken auf **Installieren**, und befolgen Sie dann die Anweisungen zum Installieren des Pakets.

Sie können nun die Bibliotheken verwenden, um Ihre Anwendung zu erstellen.

##Schritt 4: Erstellen der Anmeldeinformationen zum Authentifizieren von Anforderungen

Nachdem nun die Azure Active Directory-Anwendung erstellt und die Authentifizierungsbibliothek installiert wurde, formatieren Sie die Anwendungsinformationen zu Anmeldeinformationen, die zum Authentifizieren von Anforderungen an den Azure-Ressourcen-Manager verwendet werden. Gehen Sie wie folgt vor:

1. Öffnen Sie die Datei "Program.cs" für das Projekt, das Sie erstellt haben, und fügen Sie die folgenden using-Anweisungen am Anfang der Datei hinzu:

	```
	using Microsoft.Azure;
	using Microsoft.IdentityModel.Clients.ActiveDirectory;
	using Microsoft.Azure.Management.Resources;
	using Microsoft.Azure.Management.Resources.Models;
	using Microsoft.Rest;
	```

2.	Fügen Sie der Program-Klasse die folgende Methode hinzu, um das Token abzurufen, das zum Erstellen der Anmeldeinformationen benötigt wird:

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

3. Fügen Sie der Main-Methode in der Datei "Program.cs" den folgenden Code hinzu, um die Anmeldeinformationen zu erstellen:

	```
	var token = GetAuthorizationHeader();
	var credential = new TokenCredentials(token);
	```

4. Speichern Sie die Datei "Program.cs".


## Schritt 5: Hinzufügen von Code zum Bereitstellen der Vorlage

Ressourcen werden einer Ressourcengruppe immer aus einer Vorlage bereitgestellt. Verwenden Sie die [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) und [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx)-Klassen, um die Ressourcengruppe zu erstellen, der die Ressourcen bereitgestellt werden.

1. Fügen Sie der Program-Klasse die folgende Methode hinzu, um die Ressourcengruppe zu erstellen:

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
		Console.WriteLine(rgResult.StatusCode.Properties.ProvisioningState);
	}
	```

2. Fügen Sie der Main-Methode den folgenden Code hinzu, um die gerade hinzugefügte Methode aufzurufen:

	```
	CreateResourceGroup(
		credential,
		"{group-name}",
		"{subscription-id}",
		"{location}");
		Console.ReadLine();
	```

	Ersetzen Sie {group-name} durch den Namen, der für die Ressourcengruppe verwendet werden soll. Ersetzen Sie {subscription-id} durch Ihre Abonnement-ID. Sie können diese abrufen, indem Sie „Get-AzureSubscription“ ausführen. Ersetzen Sie {location} durch die Region, in der die Ressource erstellt werden soll, z. B. „USA, Westen“.

3. Fügen Sie der Program-Klasse die folgende Methode hinzu, um der Ressourcengruppe mithilfe der von Ihnen definierten Vorlage die Ressourcen bereitzustellen:

	```
	public static void CreateTemplateDeployment(
		TokenCredentials credential,
		string groupName,
		string storageName,
		string deploymentName,
		string subscriptionId)
	{
		Console.WriteLine("Creating the template deployment...");
		var deployment = new Deployment();
		deployment.Properties = new DeploymentProperties
		{
			Mode = DeploymentMode.Incremental,
			TemplateLink = new TemplateLink
			{
				Uri = "https://{storage-name}.blob.core.windows.net/templates/VirtualMachineTemplate.json"
			},
			ParametersLink = new ParametersLink
			{
				Uri = "https://{storage-name}.blob.core.windows.net/templates/Parameters.json"
			}
		};
		var resourceManagementClient = new ResourceManagementClient(credential);
		resourceManagementClient.SubscriptionId = subscriptionId;
		var dpResult = resourceManagementClient.Deployments.CreateOrUpdate(
			groupName,
			deploymentName,
			deployment);
		Console.WriteLine(dpResult.Properties.ProvisioningState);
	}
	```

	Ersetzen Sie {storage-name} durch den Namen des Kontos, in dem Sie die Dateien zuvor abgelegt haben.

4. Fügen Sie der Main-Methode den folgenden Code hinzu, um die gerade hinzugefügte Methode aufzurufen:

	```
	CreateTemplateDeployment(
		credential,
		"{group-name}",
		"{storage-name}",
		"{deployment-name}",
		"{subscription-id}");
	Console.ReadLine();
	```

    Ersetzen Sie {group-name} durch den Namen der von Ihnen erstellten Ressourcengruppe. Ersetzen Sie {storage-name} durch den Namen des Speicherkontos, in dem Sie die Vorlagendateien abgelegt haben. Ersetzen Sie {deployment-name} durch den Namen, der den Bereitstellungssatz der Ressourcen bezeichnen soll. Ersetzen Sie {subscription-id} durch Ihre Abonnement-ID. Sie können diese abrufen, indem Sie „Get-AzureSubscription“ ausführen.

##Schritt 6: Hinzufügen von Code zum Löschen der Ressourcen

Da in Azure die genutzten Ressourcen in Rechnung gestellt werden, empfiehlt es sich immer, nicht mehr benötigte Ressourcen zu löschen. Sie müssen nicht jede Ressource einzeln aus einer Ressourcengruppe löschen. Sie können auch die Ressourcengruppe löschen, sodass alle darin enthaltenen Ressourcen automatisch gelöscht werden.

1.	Fügen Sie der Program-Klasse die folgende Methode hinzu, um die Ressourcengruppe zu löschen:

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

2.	Fügen Sie der Main-Methode den folgenden Code hinzu, um die gerade hinzugefügte Methode aufzurufen:

	```
	DeleteResourceGroup(
		credential,
		"{group-name}",
		"{subscription-id}");
	Console.ReadLine();
	```
    Ersetzen Sie {group-name} durch den Namen der von Ihnen erstellten Ressourcengruppe. Ersetzen Sie {subscription-id} durch Ihre Abonnement-ID. Sie können diese abrufen, indem Sie „Get-AzureSubscription“ ausführen.

##Schritt 7: Ausführen der Konsolenanwendung.

1.	Klicken Sie zum Ausführen der Konsolenanwendung in Visual Studio auf **Starten**, und melden Sie sich dann bei Azure AD mit demselben Benutzernamen und Kennwort an, die Sie für Ihr Abonnement verwenden.

2.	Nachdem der Status „Akzeptiert“ angezeigt wird, drücken Sie die **EINGABETASTE**.

	Die vollständige Ausführung dieser Konsolenanwendung von Anfang bis zum Ende sollte etwa 5 Minuten dauern. Bevor Sie die EINGABETASTE drücken, um das Löschen der Ressourcen zu starten, können Sie sich ein paar Minuten Zeit nehmen, um die Erstellung der Ressourcen im Azure-Portal zu überprüfen, bevor Sie diese löschen.

3. Navigieren Sie im Azure-Portal zu den Überwachungsprotokollen, um den Status der Ressourcen anzuzeigen:

	![Erstellen einer AD-Anwendung](./media/virtual-machines-windows-csharp-template/crpportal.png)

<!---HONumber=AcomDC_0323_2016-->