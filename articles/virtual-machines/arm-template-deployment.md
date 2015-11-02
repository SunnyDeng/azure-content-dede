<properties
	pageTitle="Bereitstellen von Azure-Ressourcen mithilfe einer Vorlage | Microsoft Azure"
	description="In diesem Lernprogramm erfahren Sie, wie Sie einige der verfügbaren Clients in der Azure-Ressourcenverwaltungsbibliothek verwenden, um einen virtuellen Computer, ein virtuelles Netzwerk und ein Speicherkontos bereitzustellen."
	services="virtual-machines,virtual-networks,storage"
	documentationCenter=""
	authors="davidmu1"
	manager="timlt"
	editor="tysonn"
	tags="azure-resource-manager"/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="vm-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="davidmu"/>

# Bereitstellen von Azure-Ressourcen mithilfe von .NET-Bibliotheken und einer Vorlage

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]Klassisches Bereitstellungsmodell.


Durch das Verwenden von Ressourcengruppen und Vorlagen, können Sie alle Ressourcen verwalten, die zusammen Ihre Anwendung unterstützen. In diesem Lernprogramm erfahren Sie, wie Sie einige der verfügbaren Clients in der Azure-Ressourcenverwaltungsbibliothek verwenden und eine Vorlage zum Bereitstellen eines virtuellen Computers, virtuellen Netzwerks und Speicherkontos erstellen können.

[AZURE.INCLUDE [free-trial-note](../../includes/free-trial-note.md)]

Zur Durchführung dieses Lernprogramms benötigen Sie außerdem Folgendes:

- [Visual Studio](http://msdn.microsoft.com/library/dd831853.aspx)
- [Azure-Speicherkonto](../storage-create-storage-account.md)
- [Windows Management Framework 3.0 ](http://www.microsoft.com/de-DE/download/details.aspx?id=34595) oder [Windows Management Framework 4.0](http://www.microsoft.com/de-DE/download/details.aspx?id=40855)

[AZURE.INCLUDE [powershell-preview](../../includes/powershell-preview-inline-include.md)]

Die Durchführung dieser Schritte dauert etwa 30 Minuten.

## Schritt 1: Hinzufügen einer Anwendung zu Azure AD und Festlegen von Berechtigungen

Um Azure AD zum Authentifizieren von Anforderungen an den Azure-Ressourcen-Manager zu verwenden, muss dem Standardverzeichnis eine Anwendung hinzugefügt werden. Gehen Sie zum Hinzufügen einer Anwendung folgendermaßen vor:

1. Öffnen Sie eine Azure PowerShell-Eingabeaufforderung, führen Sie dann diesen Befehl aus, und geben Sie die Anmeldeinformationen für Ihr Abonnement nach Aufforderung ein:

	    Login-AzureRmAccount

2. Ersetzen Sie im folgenden Befehl {password} durch das Kennwort, das Sie verwenden möchten, und führen Sie ihn anschließend aus, um die Anwendung zu erstellen:

	    New-AzureRmADApplication -DisplayName "My AD Application 1" -HomePage "https://myapp1.com" -IdentifierUris "https://myapp1.com"  -Password "{password}"

	>[AZURE.NOTE]Notieren Sie die Anwendungs-ID, die nach dem Erstellen der Anwendung zurückgegeben wird, da Sie diese für den nächsten Schritt benötigen. Sie finden die Anwendungs-ID auch im Client-ID-Feld der Anwendung im Active Directory-Bereich des Verwaltungsportals.

3. Ersetzen Sie {application-id} durch die ID, die Sie gerade notiert haben, und erstellen Sie dann den Dienstprinzipal für die Anwendung:

        New-AzureRmADServicePrincipal -ApplicationId {application-id}

4. Legen Sie die Berechtigung zum Verwenden der Anwendung fest:

	    New-AzureRmRoleAssignment -RoleDefinitionName Owner -ServicePrincipalName "https://myapp1.com"

## Schritt 2: Erstellen des Visual Studio-Projekts, der Vorlagendatei und der Parameterdatei

###Erstellen der Vorlagendatei

Mithilfe der Azure-Ressourcen-Manager-Vorlage können Sie die Azure-Ressourcen gemeinsam bereitstellen und verwalten. Sie verwenden hierfür eine JSON-Beschreibung der Ressourcen sowie der zugeordneten Bereitstellungsparameter. Führen Sie in Visual Studio Folgendes aus:

1. Klicken Sie auf **Datei** > **Neu** > **Projekt**.

2. Wählen Sie unter **Vorlagen** > **Visual C#** **Konsolenanwendung** aus, geben Sie den Namen und Speicherort des Projekts ein, und klicken Sie dann auf **OK**.

3. Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen und dann auf **Hinzufügen** > **Neues Element**.

4.	Wählen Sie im Fenster "Neues Element hinzufügen", **Textdatei** aus, geben Sie *VirtualMachineTemplate.json* als Namen ein, und klicken Sie dann auf **Hinzufügen**.

5.	Öffnen Sie die Datei "VirtualMachineTemplate.json", und fügen Sie die öffnenden und schließenden Klammern, das erforderliche Schemaelement und das erforderliche ContentVersion-Element ein:

        {
            "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
            "contentVersion": "1.0.0.0",
        }

6. [Parameter](../resource-group-authoring-templates.md#parameters) sind nicht immer erforderlich, aber sie erleichtern die Vorlagenverwaltung. Sie beschreiben den Typ des Werts, ggf. den Standardwert und möglicherweise die zulässigen Werte des Parameters. Für dieses Lernprogramm werden die Parameter, mit denen ein virtueller Computer, ein Speicherkonto und ein virtuelles Netzwerk erstellt werden, der Vorlage hinzugefügt.

    Fügen Sie das Parameterelement und seine untergeordneten Elemente nach dem ContentVersion-Element hinzu:

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
        }

7.	[Variablen](../resource-group-authoring-templates.md#variables) können in einer Vorlage verwendet werden, um die Werte festzulegen, die sich häufig ändern, oder um die Werte festzulegen, die aus einer Kombination von Parameterwerten erstellt werden müssen.

    Fügen Sie das Variablenelement nach dem Parameterabschnitt hinzu:

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
          "variables": {
            "sourceImageName" : "[concat('/',parameters('subscriptionId'),'/services/images/',parameters('vmSourceImageName'))]",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',parameters('subnet1Name'))]"
          },
        }

8.	[Ressourcen](../resource-group-authoring-templates.md#resources) wie der virtuelle Computer, das virtuelle Netzwerk und das Speicherkonto werden als Nächstes in der Vorlage definiert.

    Fügen Sie nach dem Variablenabschnitt den Ressourcenabschnitt hinzu:

		{
		  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VM.json",
		  "contentVersion": "1.0.0.0",
		  "parameters": {
		    "location": { "type": "String", "defaultValue" : "West US", "allowedValues": [ "West US", "East US" ] },
            "newStorageAccountName": { "type": "string" },
            "storageAccountType": { "type": "string", "defaultValue": "Standard_LRS", "allowedValues": [ "Standard_LRS", "Standard_GRS" ] },
            "publicIPAddressName": { "type": "string" },
            "publicIPAddressType" : { "type" : "string", "defaultValue" : "Dynamic", "allowedValues" : [ "Dynamic" ] },
            "vmStorageAccountContainerName": { "type": "string", "defaultValue": "vhds" },
            "vmName": { "type": "string" },
            "vmSize": { "type": "string", "defaultValue": "Standard_A0", "allowedValues" : [ "Standard_A0", "Standard_A1" ] },
            "vmSourceImageName": { "type": "string" },
            "adminUserName": { "type": "string" },
            "adminPassword": { "type": "securestring" },
            "virtualNetworkName":{ "type" : "string" },
            "addressPrefix": { "type" : "string", "defaultValue" : "10.0.0.0/16" },
            "subnet1Name": { "type" : "string", "defaultValue" : "Subnet-1" },
            "subnet2Name": { "type" : "string", "defaultValue" : "Subnet-2" },
            "subnet1Prefix" : { "type" : "string", "defaultValue" : "10.0.0.0/24" },
            "subnet2Prefix" : { "type" : "string", "defaultValue" : "10.0.1.0/24" },
            "dnsName" : { "type" : "string" },
            "subscriptionId": { "type" : "string" },
            "nicName": { "type" : "string" }
          },
          "variables": {
            "sourceImageName" : "[concat('/',parameters('subscriptionId'),'/services/images/',parameters('vmSourceImageName'))]",
            "vnetID":"[resourceId('Microsoft.Network/virtualNetworks',parameters('virtualNetworkName'))]",
            "subnet1Ref" : "[concat(variables('vnetID'),'/subnets/',parameters('subnet1Name'))]"
          },
          "resources": [ {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Storage/storageAccounts",
            "name": "[parameters('newStorageAccountName')]",
            "location": "[parameters('location')]",
            "properties": { "accountType": "[parameters('storageAccountType')]" }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/publicIPAddresses",
            "name": "[parameters('publicIPAddressName')]",
            "location": "[parameters('location')]",
            "properties": {
              "publicIPAllocationMethod": "[parameters('publicIPAddressType')]",
              "dnsSettings": { "domainNameLabel": "[parameters('dnsName')]" }
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/virtualNetworks",
            "name": "[parameters('virtualNetworkName')]",
            "location": "[parameters('location')]",
            "properties": {
              "addressSpace": { "addressPrefixes": [ "[parameters('addressPrefix')]" ] },
              "subnets": [ {
                "name": "[parameters('subnet1Name')]",
                "properties": { "addressPrefix": "[parameters('subnet1Prefix')]" }
              },
              {
                "name": "[parameters('subnet2Name')]",
                "properties": { "addressPrefix": "[parameters('subnet2Prefix')]" }
              } ]
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Network/networkInterfaces",
            "name": "[parameters('nicName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[concat('Microsoft.Network/publicIPAddresses/', parameters('publicIPAddressName'))]",
              "[concat('Microsoft.Network/virtualNetworks/', parameters('virtualNetworkName'))]"
            ],
            "properties": {
              "ipConfigurations": [ {
                "name": "ipconfig1",
                "properties": {
                  "privateIPAllocationMethod": "Dynamic",
                  "publicIPAddress": {
                    "id": "[resourceId('Microsoft.Network/publicIPAddresses', parameters('publicIPAddressName'))]"
                  },
                  "subnet": { "id": "[variables('subnet1Ref')]" }
                }
              } ]
            }
          },
          {
            "apiVersion": "2014-12-01-preview",
            "type": "Microsoft.Compute/virtualMachines",
            "name": "[parameters('vmName')]",
            "location": "[parameters('location')]",
            "dependsOn": [
              "[concat('Microsoft.Storage/storageAccounts/', parameters('newStorageAccountName'))]",
              "[concat('Microsoft.Network/networkInterfaces/', parameters('nicName'))]"
            ],
            "properties": {
              "hardwareProfile": { "vmSize": "[parameters('vmSize')]" },
              "osProfile": {
                "computername": "[parameters('vmName')]",
                "adminUsername": "[parameters('adminUsername')]",
                "adminPassword": "[parameters('adminPassword')]",
                "windowsProfile": { "provisionVMAgent": "true" }
              },
              "storageProfile": {
                "sourceImage": { "id": "[variables('sourceImageName')]" },
                "destinationVhdsContainer" : "[concat('http://',parameters('newStorageAccountName'),'.blob.core.windows.net/',parameters('vmStorageAccountContainerName'),'/')]"
              },
              "networkProfile": {
                "networkInterfaces" : [ {
                  "id": "[resourceId('Microsoft.Network/networkInterfaces',parameters('nicName'))]"
                } ]
              }
            }
          } ]
        }

9.	Speichern Sie Vorlagendatei, die Sie erstellt haben.

###Erstellen der Parameterdatei

Um Werte für die Ressourcenparameter anzugeben, die in der Vorlage definiert wurden, müssen Sie eine Parameterdatei erstellen, die die Werte enthält und sie mit der Vorlage an den Ressourcen-Manager senden. Führen Sie in Visual Studio Folgendes aus:

1.	Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen und dann auf **Hinzufügen** > **Neues Element**.

2.	Wählen Sie im Fenster "Neues Element hinzufügen", **Textdatei** aus, geben Sie *Parameters.json* als Namen ein, und klicken Sie dann auf **Hinzufügen**.

3.	Öffnen Sie die Datei "parameters.json", und fügen Sie den folgenden JSON-Inhalt hinzu:

        {
          "contentVersion": "1.0.0.0",
          "parameters": {
            "vmName": { "value": "mytestvm1" },
            "newStorageAccountName": { "value": "mytestsa1" },
            "storageAccountType": { "value": "Standard_LRS" },
            "publicIPaddressName": { "value": "mytestip1" },
            "location": { "value": "West US" },
            "vmStorageAccountContainerName": { "value": "vhds" },
            "vmSize": { "value": "Standard_A1" },
            "subscriptionId": { "value": "{subscription-id}" },
            "vmSourceImageName": { "value": "{source-image-name}" },
            "adminUserName": { "value": "mytestacct1" },
            "adminPassword": { "value": "mytestpass1" },
            "virtualNetworkName": { "value": "mytestvn1" },
            "dnsName": { "value": "mytestdns1" },
            "nicName": { "value": "mytestnic1" }
          }
        }

    >[AZURE.NOTE]VHD-Imagenamen ändern sich regelmäßig im Imagekatalog, daher benötigen Sie einen aktuellen Imagenamen, um den virtuellen Computer bereitzustellen. Weitere Informationen hierzu finden Sie unter [Informationen zu Images für virtuelle Computer](https://azure.microsoft.com/de-DE/documentation/articles/virtual-machines-images/). Ersetzen Sie dann {source-image-name} durch den Namen der VHD-Datei, die Sie verwenden möchten. Beispiel: "a699494373c04fc0bc8f2bb1389d6106\_\_Windows-Server-2012-R2-201412.01-en.us-127GB.vhd". Ersetzen Sie {subscription-id} durch die ID Ihres Abonnements.


4.	Speichern Sie Parameterdatei, die Sie erstellt haben.

Der Azure-Ressourcen-Manager greift auf die Vorlagendatei und die Parameterdatei in einem Azure-Speicherkonto zu. Um die Dateien im Speicher abzulegen, führen Sie folgende Schritte aus:

1.	Öffnen Sie den Server-Explorer, und navigieren Sie zu dem Container im Speicherkonto, in dem Sie die Datei platzieren möchten. In diesem Lernprogramm heißt der Container, in dem sich die Vorlage befindet "templates".

2.	Klicken Sie in der rechten, oberen Ecke des Vorlagencontainerbereichs auf das Symbol zum Hochladen von Blobs, navigieren Sie zur Datei "VirtualMachineTemplate.json", die Sie erstellt haben, und klicken Sie dann auf **Öffnen**.

3. Klicken Sie erneut auf das Symbol zum Hochladen von Blobs, navigieren Sie zur Datei "Parameters.json", die Sie erstellt haben, und klicken Sie dann auf **Öffnen**.

##Schritt 3: Installieren der Bibliotheken

NuGet-Pakete sind die einfachste Möglichkeit, um die Bibliotheken zu installieren, die Sie zum Abschluss dieses Lernprogramms benötigen. Sie müssen die Azure-Ressourcenverwaltungsbibliothek und die Azure Active Directory-Authentifizierungsbibliothek installieren. Führen Sie folgende Schritte aus, um diese Bibliotheken in Visual Studio abzurufen:

1.	Klicken Sie im Projektmappen-Explorer mit der rechten Maustaste auf den Projektnamen und anschließend auf **NuGet-Pakete verwalten**.

2.	Geben Sie *Active Directory* in das Suchfeld ein, klicken Sie für das Active Directory-Authentifizierungsbibliothek-Paket auf **Installieren**, und befolgen Sie dann die Anweisungen zum Installieren des Pakets.

3.	Wählen Sie am oberen Rand der Seite **Vorabversion einschließen**. Geben Sie *Azure-Ressourcenverwaltung* in das Suchfeld ein, klicken Sie auf **Installieren** für die Microsoft Azure-Ressourcenverwaltungsbibliotheken, und befolgen Sie dann die Anweisungen zum Installieren des Pakets.

Sie können nun die Bibliotheken verwenden, um Ihre Anwendung zu erstellen.

##Schritt 4: Erstellen der Anmeldeinformationen zum Authentifizieren von Anforderungen

Nachdem nun die Azure Active Directory-Anwendung erstellt und die Authentifizierungsbibliothek installiert wurde, formatieren Sie die Anwendungsinformationen zu Anmeldeinformationen, die zum Authentifizieren von Anforderungen an den Azure-Ressourcen-Manager verwendet werden. Gehen Sie wie folgt vor:

1.	Öffnen Sie die Datei "Program.cs" für das Projekt, das Sie erstellt haben, und fügen Sie die folgenden using-Anweisungen am Anfang der Datei hinzu:

        using Microsoft.Azure;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
        using Microsoft.Azure.Management.Resources;
        using Microsoft.Azure.Management.Resources.Models;

2.	Fügen Sie der Program-Klasse die folgende Methode hinzu, um das Token abzurufen, das zum Erstellen der Anmeldeinformationen benötigt wird:

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

4.	Speichern Sie die Datei "Program.cs".


##Schritt 5: Hinzufügen von Code zum Bereitstellen der Vorlage

Ressourcen werden einer Ressourcengruppe immer aus einer Vorlage bereitgestellt. Verwenden Sie die [ResourceGroup](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.models.resourcegroup.aspx) und [ResourceManagementClient](https://msdn.microsoft.com/library/azure/microsoft.azure.management.resources.resourcemanagementclient.aspx)-Klassen, um die Ressourcengruppe zu erstellen, der die Ressourcen bereitgestellt werden.

1.	Fügen Sie der Program-Klasse die folgende Methode hinzu, um die Ressourcengruppe zu erstellen:

		public async static void CreateResourceGroup(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the resource group...");
		  var resourceGroup = new ResourceGroup { Location = "West US" };
		  using (var resourceManagementClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceManagementClient.ResourceGroups.CreateOrUpdateAsync("mytestrg1", resourceGroup);
		    Console.WriteLine(rgResult.StatusCode);
		  }
		}

2.	Fügen Sie der Main-Methode den folgenden Code hinzu, um die gerade hinzugefügte Methode aufzurufen:

		CreateResourceGroup(credential);
		Console.ReadLine();

3.	Fügen Sie der Program-Klasse die folgende Methode hinzu, um der Ressourcengruppe mithilfe der von Ihnen definierten Vorlage die Ressourcen bereitzustellen:

		public async static void CreateTemplateDeployment(TokenCloudCredentials credential)
		{
		  Console.WriteLine("Creating the template deployment...");
		  var deployment = new Deployment();
          deployment.Properties = new DeploymentProperties
		  {
		    Mode = DeploymentMode.Incremental,
		    TemplateLink = new TemplateLink
		    {
		      Uri = new Uri("https://{storage-account-name}.blob.core.windows.net/templates/VirtualMachineTemplate.json")
			},
			ParametersLink = new ParametersLink
			{
			  Uri = new Uri("https://{storage-account-name}.blob.core.windows.net/templates/Parameters.json")
			}
		  };
		  using (var templateDeploymentClient = new ResourceManagementClient(credential))
		  {
		    var dpResult = await templateDeploymentClient.Deployments.CreateOrUpdateAsync("mytestrg1", "mytestdp1", deployment);
			Console.WriteLine(dpResult.StatusCode);
		  }
		}

	Ersetzen Sie {storage-account-name} durch den Namen des Kontos, in dem Sie die Dateien zuvor abgelegt haben.

4.	Fügen Sie der Main-Methode den folgenden Code hinzu, um die gerade hinzugefügte Methode aufzurufen:

		CreateTemplateDeployment(credential);
		Console.ReadLine();

##Schritt 6: Hinzufügen von Code zum Löschen der Ressourcen

Da in Azure die genutzten Ressourcen in Rechnung gestellt werden, empfiehlt es sich immer, nicht mehr benötigte Ressourcen zu löschen. Sie müssen nicht jede Ressource einzeln aus einer Ressourcengruppe löschen. Sie können auch die Ressourcengruppe löschen, sodass alle darin enthaltenen Ressourcen automatisch gelöscht werden.

1.	Fügen Sie der Program-Klasse die folgende Methode hinzu, um die Ressourcengruppe zu löschen:

		public async static void DeleteResourceGroup(TokenCloudCredentials credential)
		{
		  using (var resourceGroupClient = new ResourceManagementClient(credential))
		  {
		    var rgResult = await resourceGroupClient.ResourceGroups.DeleteAsync("mytestrg1");
			Console.WriteLine(rgResult.StatusCode);
		  }
		}

2.	Fügen Sie der Main-Methode den folgenden Code hinzu, um die gerade hinzugefügte Methode aufzurufen:

		DeleteResourceGroup(credential);
		Console.ReadLine();

##Schritt 7: Ausführen der Konsolenanwendung.

1.	Klicken Sie zum Ausführen der Konsolenanwendung in Visual Studio auf **Starten**, und melden Sie sich dann bei Azure AD mit demselben Benutzernamen und Kennwort an, die Sie für Ihr Abonnement verwenden.

2.	Drücken Sie nach der Rückgabe jedes Statuscodes die **EINGABETASTE**, um jede Ressource zu erstellen. Nachdem der virtuelle Computer erstellt wurde, führen Sie den nächsten Schritt aus, bevor Sie die EINGABETASTE drücken, um alle Ressourcen zu löschen.

	Die vollständige Ausführung dieser Konsolenanwendung von Anfang bis zum Ende sollte etwa 5 Minuten dauern. Bevor Sie die EINGABETASTE drücken, um das Löschen der Ressourcen zu starten, können Sie sich ein paar Minuten Zeit nehmen, um die Erstellung der Ressourcen im Azure-Vorschauportal zu überprüfen, bevor Sie diese löschen.

3. Navigieren Sie im Azure-Vorschauportal zu den Überwachungsprotokollen, um den Status der Ressourcen anzuzeigen:

	![Erstellen einer AD-Anwendung](./media/arm-template-deployment/crpportal.png)

<!---HONumber=Oct15_HO4-->