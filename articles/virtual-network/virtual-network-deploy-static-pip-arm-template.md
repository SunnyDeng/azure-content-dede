<properties 
   pageTitle="Bereitstellen einer VM mit einer statischen öffentlichen IP-Adresse mit einer Vorlage im Ressourcen-Manager | Microsoft Azure"
   description="Erfahren Sie, wie Sie VMs mit einer statischen öffentlichen IP-Adresse mit einer Vorlage im Ressourcen-Manager bereitstellen."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="carmonm"
   editor=""
   tags="azure-resource-manager"
/>
<tags  
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="01/08/2016"
   ms.author="telmos" />

# Bereitstellen einer VM mit einer statischen öffentlichen IP-Adresse über eine Vorlage

[AZURE.INCLUDE [virtual-network-deploy-static-pip-arm-selectors-include.md](../../includes/virtual-network-deploy-static-pip-arm-selectors-include.md)]

[AZURE.INCLUDE [virtual-network-deploy-static-pip-intro-include.md](../../includes/virtual-network-deploy-static-pip-intro-include.md)]

[AZURE.INCLUDE [azure-arm-classic-important-include](../../includes/learn-about-deployment-models-rm-include.md)]klassisches Bereitstellungsmodell.

[AZURE.INCLUDE [virtual-network-deploy-static-pip-scenario-include.md](../../includes/virtual-network-deploy-static-pip-scenario-include.md)]

## Öffentliche IP-Ressourcen in einer Vorlagendatei

Sie können die [Beispielvorlage](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json) anzeigen und herunterladen.

Im folgenden Abschnitt wird die Definition der öffentlichen IP-Ressource auf Grundlage des obigen Szenarios beschrieben.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/publicIPAddresses",
        "name": "[variables('webVMSetting').pipName]",
        "location": "[variables('location')]",
        "properties": {
          "publicIPAllocationMethod": "Static"
        },
        "tags": {
          "displayName": "PublicIPAddress - Web"
        }
      },

Beachten Sie die **publicIPAllocationMethod**-Eigenschaft, die auf *Static* festgelegt ist. Diese Eigenschaft kann entweder *Dynamic* (Standardwert) oder *Static* lauten. Durch das Festlegen auf „Static“ wird sichergestellt, dass sich die IP-Adresse für diese öffentliche IP-Adresse nicht ändert.

Der folgende Abschnitt zeigt die Zuordnung der obigen öffentlichen IP zu einer Netzwerkschnittstelle.

      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Network/networkInterfaces",
        "name": "[variables('webVMSetting').nicName]",
        "location": "[variables('location')]",
        "tags": {
          "displayName": "NetworkInterface - Web"
        },
        "dependsOn": [
          "[concat('Microsoft.Network/publicIPAddresses/', variables('webVMSetting').pipName)]",
          "[concat('Microsoft.Network/virtualNetworks/', parameters('vnetName'))]"
        ],
        "properties": {
          "ipConfigurations": [
            {
              "name": "ipconfig1",
              "properties": {
                "privateIPAllocationMethod": "Static",
                "privateIPAddress": "[variables('webVMSetting').ipAddress]",
                "publicIPAddress": {
                  "id": "[resourceId('Microsoft.Network/publicIPAddresses',variables('webVMSetting').pipName)]"
                },
                "subnet": {
                  "id": "[variables('frontEndSubnetRef')]"
                }
              }
            }
          ]
        }
      },

Beachten Sie, dass die **publicIPAddress**-Eigenschaft auf die **Id** einer Ressource mit dem Namen **variables('webVMSetting').pipName** zeigt. Dies ist der Name der oben angegebenen öffentlichen IP-Ressource.

Die obige Netzwerkschnittstelle wird jetzt unter der **networkProfile**-Eigenschaft der zu erstellenden VM aufgelistet.

      "networkProfile": {
        "networkInterfaces": [
          {
            "id": "[resourceId('Microsoft.Network/networkInterfaces', variables('webVMSetting').nicName)]"
          }
        ]
      }

## Bereitstellen der Vorlage per Klick

In der verfügbaren Beispielvorlage im öffentlichen Repository wird eine Parameterdatei verwendet, die die Standardwerte zum Generieren des oben beschriebenen Szenarios enthält. Öffnen Sie zum Bereitstellen dieser Vorlage per Klick [diesen Link](https://github.com/Azure/azure-quickstart-templates/tree/master/IaaS-Story/03-Static-public-IP), klicken Sie auf **Deploy to Azure**, ersetzen Sie ggf. die Werte der Standardparameter, und führen Sie die Schritte im Portal aus.

## Bereitstellen der Vorlage mithilfe von PowerShell

Führen Sie zum Bereitstellen der mithilfe von PowerShell heruntergeladenen Vorlage die unten beschriebenen Schritte aus.

1. Falls Sie Azure PowerShell noch nicht verwendet haben, helfen Ihnen die Informationen unter [Gewusst wie: Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md) weiter. Führen Sie die Schritte 1 bis 3 aus.

2. Führen Sie in einer PowerShell-Konsole das **New-AzureRmResourceGroup**-Cmdlet aus, um bei Bedarf eine neue Ressourcengruppe zu erstellen. Fahren Sie mit Schritt 3 fort, falls Sie bereits eine Ressourcengruppe erstellt haben.

		New-AzureRmResourceGroup -Name StaticPublicIP -Location westus

	Erwartete Ausgabe:

		ResourceGroupName : StaticPublicIP
		Location          : westus
		ProvisioningState : Succeeded
		Tags              : 
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/StaticPublicIP

3. Führen Sie in einer PowerShell-Konsole das **New-AzureRmResourceGroupDeployment**-Cmdlet aus, um die Vorlage bereitzustellen.

		New-AzureRmResourceGroupDeployment -Name DeployVM -ResourceGroupName PIPTEST `
		    -TemplateUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json `
		    -TemplateParameterUri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json

	Erwartete Ausgabe:

		DeploymentName    : DeployVM
		ResourceGroupName : PIPTEST
		ProvisioningState : Succeeded
		Timestamp         : 1/8/2016 7:04:44 PM
		Mode              : Incremental
		TemplateLink      : 
		                    Uri            : https://raw.githubusercontent.com/Azure/azure-quickstart-templates/mas
		                    ter/IaaS-Story/03-Static-public-IP/azuredeploy.json
		                    ContentVersion : 1.0.0.0
		                    
		Parameters        : 
		                    Name                      Type                       Value     
		                    ========================  =========================  ==========
		                    vnetName                  String                     WTestVNet 
		                    vnetPrefix                String                     192.168.0.0/16
		                    frontEndSubnetName        String                     FrontEnd  
		                    frontEndSubnetPrefix      String                     192.168.1.0/24
		                    storageAccountNamePrefix  String                     iaasestd  
		                    stdStorageType            String                     Standard_LRS
		                    osType                    String                     Windows   
		                    adminUsername             String                     adminUser 
		                    adminPassword             SecureString                         
		                    
		Outputs           : 

## Bereitstellen der Vorlage mithilfe der Azure-Befehlszeilenschnittstelle

Führen Sie zum Bereitstellen der Vorlage mithilfe der Azure-Befehlszeilenschnittstelle die unten beschriebenen Schritte aus.

1. Wenn Sie die Azure-Befehlszeilenschnittstelle noch nie verwendet haben, ziehen Sie [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](xplat-cli.md) zurate, und folgen Sie den Anweisungen bis zu dem Punkt, an dem Sie Ihr Azure-Konto und Ihr Abonnement auswählen.
2. Führen Sie den Befehl **azure config mode** aus, um in den Ressourcen-Manager-Modus zu wechseln, wie unten dargestellt.

		azure config mode arm

	Hier ist die erwartete Ausgabe des obigen Befehls:

		info:    New mode is arm

3. Öffnen Sie die [Parameterdatei](https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.parameters.json), und speichern Sie den Inhalt dieser Datei in einer Datei auf Ihrem Computer. In diesem Beispiel haben wir die Parameterdatei in *parameters.json* gespeichert.

4. Führen Sie das Cmdlet **azure group deployment create** aus, um das neue VNet mithilfe der oben heruntergeladenen und geänderten Vorlage und Parameterdateien bereitzustellen. In der nach der Ausgabe angezeigten Liste werden die verwendeten Parameter erläutert.

		azure group create -n PIPTEST2 -l westus --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/IaaS-Story/03-Static-public-IP/azuredeploy.json -e parameters.json

	Erwartete Ausgabe:

		info:    Executing command group create
		+ Getting resource group PIPTEST2
		+ Creating resource group PIPTEST2
		info:    Created resource group PIPTEST2
		+ Initializing template configurations and parameters
		+ Creating a deployment
		info:    Created template deployment "azuredeploy"
		data:    Id:                  /subscriptions/628dad04-b5d1-4f10-b3a4-dc61d88cf97c/resourceGroups/PIPTEST2
		data:    Name:                PIPTEST2
		data:    Location:            westus
		data:    Provisioning State:  Succeeded
		data:    Tags: null
		data:
		info:    group create command OK

<!---HONumber=AcomDC_0128_2016-->