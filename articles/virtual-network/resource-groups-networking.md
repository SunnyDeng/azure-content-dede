<properties 
   pageTitle="Übersicht über Anbieter von Netzwerkressourcen| Microsoft Azure"
   description="Erfahren Sie mehr über neue Anbieter von Netzwerkressourcen im Azure-Ressourcen-Manager."
   services="virtual-network"
   documentationCenter="na"
   authors="telmosampaio"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="virtual-network"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="09/14/2015"
   ms.author="telmos" />

# Anbieter von Netzwerkressourcen
Ein zentrales Bedürfnis für den heutigen Geschäftserfolg ist die Fähigkeit, großmaßstäbliche netzwerksensible Anwendungen auf agile, flexible und wiederholbare Weise aufzubauen und zu verwalten. Mit Azure Resource Manager (ARM) können Sie solche Anwendungen als eine einzelne Ressourcensammlung in Ressourcengruppen erstellen. Solche Ressourcen werden durch mehrere Ressourcenanbieter unter ARM verwaltet.

Azure Resource Manager stützt sich auf verschiedene Ressourcenanbieter, um den Zugriff auf Ressourcen zu ermöglichen. Es gibt drei Hauptressourcenanbieter: Netzwerk, Speicher und Compute. In diesem Dokument werden Merkmale und Vorteile des Netzwerkressourcenanbieters wie die Folgenden beschrieben:

- **Metadaten** – Sie können mithilfe von Tags Informationen hinzufügen. Diese Tags können verwendet werden, um die Ressourcenverwendung über Ressourcengruppen und Abonnements hinweg nachzuverfolgen.
- **Bessere Kontrolle Ihres Netzwerks** - Netzwerkressourcen sind lose gekoppelt und Sie können sie granularer kontrollieren. Das bedeutet, dass Sie beim Verwalten der Netzwerkressourcen mehr Flexibilität haben.
- **Schnellere Konfiguration** - Da die Netzwerkressourcen lose gekoppelt sind, können Sie Netzwerkressourcen parallel erstellen und orchestrieren. Das führt zu einer drastisch reduzierten Konfigurationszeit.
- **Rollenbasierte Zugriffssteuerung** - Die rollenbasierte Zugriffssteuerung bietet neben der Möglichkeit, benutzerdefinierte Rollen für eine sichere Verwaltung zu erstellen, auch Standardrollen mit spezifischen Sicherheitsbereichen. 
- **Leichteres Verwalten und Bereitstellen** - Anwendungen können leichter bereitgestellt und verwaltet werden, da Sie einen ganzen Anwendungsstapel als eine einzelne Ressourcensammlung in einer Ressourcengruppe erstellen können. Das Bereitstellen erfolgt schneller, da Sie hierfür lediglich eine Vorlage einer JSON-Nutzlast zur Verfügung stellen müssen.
- **Schnelle Anpassung** - Sie können Vorlagen im deklarativen Stil verwenden, um eine wiederholbare und schnelle Anpassung von Bereitstellungen zu aktivieren. 
- **Wiederholbare Anpassung** - Sie können Vorlagen im deklarativen Stil verwenden, um eine wiederholbare und schnelle Anpassung von Bereitstellungen zu aktivieren.
- **Verwaltungsoberflächen** - Zum Verwalten der Ressourcen können Sie zwischen den folgenden Schnittstellen wählen:
	- REST basierte API
	- PowerShell
	- .NET SDK
	- Node.JS SDK
	- Java-SDK
	- Azure-Befehlszeilenschnittstelle
	- Vorschauportal
	- ARM-Vorlagensprache

## Netzwerkressourcen 
Die Verwaltung von Netzwerkressourcen kann nun unabhängig voneinander erfolgen, anstatt sie alle durch eine einzelne Computerressource (virtueller Computer) zu verwalten. Das stellt ein höheres Maß an Flexibilität und Agilität beim Verfassen einer komplexen und großmaßstäblichen Infrastruktur in einer Ressourcengruppe sicher.

Eine konzeptionelle Ansicht einer Beispielbereitstellung mit einer Multi-Tier-Anwendung ist nachstehend dargestellt. Alle angezeigten Ressourcen wie Netzwerkkarten, öffentliche IP-Adressen und virtuelle Computer können unabhängig voneinander verwaltet werden.

![Netzwerkressourcen-Modell](./media/resource-groups-networking/Figure2.png)

Jede Ressource enthält einen allgemeinen und einen spezifischen Satz von Eigenschaften. Allgemeine Eigenschaften:

|Eigenschaft|Beschreibung|Beispielwerte|
|---|---|---|
|**name**|Der eindeutige Name der Ressource. Für jeden Ressourcentyp gelten eigene Einschränkungen für die Benennung.|PIP01, VM01, NIC01|
|**location**|Die Azure-Region, in der sich die Ressource befindet.|westus, eastus|
|**id**|Die eindeutige URI-basierte Kennung.|/subscriptions/<subGUID>/resourceGroups/TestRG/providers/Microsoft.Network/publicIPAddresses/TestPIP|

Die spezifischen Eigenschaften der Ressourcen finden Sie in den folgenden Abschnitten.

[AZURE.INCLUDE [virtual-networks-nrp-pip-include](../../includes/virtual-networks-nrp-pip-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vnet-include](../../includes/virtual-networks-nrp-vnet-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nic-include](../../includes/virtual-networks-nrp-nic-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-nsg-include](../../includes/virtual-networks-nrp-nsg-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-lb-include](../../includes/virtual-networks-nrp-lb-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-appgw-include](../../includes/virtual-networks-nrp-lb-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-vpn-include](../../includes/virtual-networks-nrp-vpn-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-dns-include](../../includes/virtual-networks-nrp-dns-include.md)]

[AZURE.INCLUDE [virtual-networks-nrp-tm-include](../../includes/virtual-networks-nrp-tm-include.md)]

## Verwaltungsschnittstellen
Azure-Netzwerkressourcen können über verschiedene Schnittstellen verwaltet werden. In diesem Dokument liegt der Schwerpunkt auf zwei dieser Schnittstellen: der REST-API und Vorlagen.

### REST-API 
Wie zuvor erwähnt können Netzwerkressourcen über eine Vielzahl an Schnittstellen verwaltet werden, einschließlich REST API, .NET SDK, Node.JS SDK, Java SDK, PowerShell, CLI, Azure Portal und Vorlagen.

Die Rest APIs entsprechen der HTTP 1.1 Protokollspezifikation. Die allgemeine URI-Struktur der API ist nachstehend dargestellt:

	https://management.azure.com/subscriptions/{subscription-id}/providers/{resource-provider-namespace}/locations/{region-location}/register?api-version={api-version}

Die Parameter in Klammern stellen die folgenden Elemente dar:

- **subscription-id** - Ihre Azure-Abonnement-ID.
- **resource-provider-namespace** - Namespace für den genutzten Anbieter. Der Wert für den Anbieter der Netzwerkressourcen ist *Microsoft.Network*.
- **region-name** - Der Name der Azure-Region.

Die folgenden HTTP-Methoden werden bei Aufrufen der REST API unterstützt:

- **PUT** - wird verwendet, um eine Ressource eines bestimmten Typs zu erstellen, eine Ressourceneigenschaft zu ändern oder eine Zuordnung zwischen Ressourcen zu ändern. 
- **GET** - Wird verwendet, um Informationen für eine bereitgestellte Ressource zu ermitteln.
- **DELETE** - Wird verwendet, um eine bestehende Ressource zu löschen.

Sowohl die Anforderung als auch die Antwort entsprechen einem JSON-Nutzlast-Format. Weitere Informationen finden Sie unter [Azure Resource Management APIs](https://msdn.microsoft.com/library/azure/dn948464.aspx).

### ARM-Vorlagensprache
Neben dem unerlässlichen Verwalten von Ressourcen (über APIs oder SDK) können Sie auch einen deklarativen Programmierstil verwenden, um Netzwerkressourcen mithilfe der ARM-Vorlagensprache zu erstellen und zu verwalten.

Nachstehend finden Sie eine Beispieldarstellung einer Vorlage –

	{
	  "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
	  "contentVersion": "<version-number-of-template>",
	  "parameters": { <parameter-definitions-of-template> },
	  "variables": { <variable-definitions-of-template> },
	  "resources": [ { <definition-of-resource-to-deploy> } ],
	  "outputs": { <output-of-template> }    
	}

Die Vorlage ist überwiegend eine JSON-Beschreibung der Ressourcen und die Instanzwerte sind über Parameter eingefügt. Das untenstehende Beispiel kann zum Erstellen eines virtuellen Netzwerks mit zwei Subnetzen verwendet werden.

	{
	    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/VNET.json",
	    "contentVersion": "1.0.0.0",
	    "parameters" : {
	      "location": {
	        "type": "String",
	        "allowedValues": ["East US", "West US", "West Europe", "East Asia", "South East Asia"],
	        "metadata" : {
	          "Description" : "Deployment location"
	        }
	      },
	      "virtualNetworkName":{
	        "type" : "string",
	        "defaultValue":"myVNET",
	        "metadata" : {
	          "Description" : "VNET name"
	        }
	      },
	      "addressPrefix":{
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/16",
	        "metadata" : {
	          "Description" : "Address prefix"
	        }
	
	      },
	      "subnet1Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-1",
	        "metadata" : {
	          "Description" : "Subnet 1 Name"
	        }
	      },
	      "subnet2Name": {
	        "type" : "string",
	        "defaultValue" : "Subnet-2",
	        "metadata" : {
	          "Description" : "Subnet 2 name"
	        }
	      },
	      "subnet1Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.0.0/24",
	        "metadata" : {
	          "Description" : "Subnet 1 Prefix"
	        }
	      },
	      "subnet2Prefix" : {
	        "type" : "string",
	        "defaultValue" : "10.0.1.0/24",
	        "metadata" : {
	          "Description" : "Subnet 2 Prefix"
	        }
	      }
	    },
	    "resources": [
	    {
	      "apiVersion": "2015-05-01-preview",
	      "type": "Microsoft.Network/virtualNetworks",
	      "name": "[parameters('virtualNetworkName')]",
	      "location": "[parameters('location')]",
	      "properties": {
	        "addressSpace": {
	          "addressPrefixes": [
	            "[parameters('addressPrefix')]"
	          ]
	        },
	        "subnets": [
	          {
	            "name": "[parameters('subnet1Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet1Prefix')]"
	            }
	          },
	          {
	            "name": "[parameters('subnet2Name')]",
	            "properties" : {
	              "addressPrefix": "[parameters('subnet2Prefix')]"
	            }
	          }
	        ]
	      }
	    }
	    ]
	}

Bei Verwendung einer Vorlage besteht die Option, die Parameterwerte manuell bereitzustellen. Alternativ können Sie eine Parameterdatei nutzen. Das untenstehende Beispiel zeigt einen möglichen Satz von Parameterwerten, die mit der oben abgebildeten Vorlage zu verwenden sind:

	{
	  "location": {
	      "value": "East US"
	  },
	  "virtualNetworkName": {
	      "value": "VNET1"
	  },
	  "subnet1Name": {
	      "value": "Subnet1"
	  },
	  "subnet2Name": {
	      "value": "Subnet2"
	  },
	  "addressPrefix": {
	      "value": "192.168.0.0/16"
	  },
	  "subnet1Prefix": {
	      "value": "192.168.1.0/24"
	  },
	  "subnet2Prefix": {
	      "value": "192.168.2.0/24"
	  }
	}


Die Hauptvorteile des Verwenden von Vorlagen sind:

- Sie können in einem deklarativen Stil eine komplexe Infrastruktur in einer Ressourcengruppe erstellen. Die Orchestrierung der Ressourcenerstellung, einschließlich der Verwaltung von Abhängigkeiten, wird von ARM bearbeitet. 
- Die Infrastruktur kann auf wiederholbare Weise regionsübergreifend oder innerhalb einer Region erstellt werden, indem einfach die Parameter geändert werden. 
- Der deklarative Stil führt zu einer kürzeren Durchlaufzeit beim Erstellen der Vorlagen und Einführen der Infrastruktur. 

Beispielvorlagen finden Sie unter [Azure Schnellstart-Vorlagen](https://github.com/Azure/azure-quickstart-templates).

Weitere Informationen zur ARM-Vorlagensprache finden Sie unter [Azure Resource Manager-Vorlagensprache](../resource-group-authoring-templates.md).

Die oben abgebildete Vorlage nutzt das virtuelle Netzwerk und Subnetzressourcen. Wie unten aufgeführt, gibt es weitere Netzwerkressourcen, die Sie nutzen können:

### Verwenden einer Vorlage

Sie können Dienste in Azure über eine Vorlage bereitstellen, indem Sie PowerShell oder AzureCLI verwenden oder indem Sie die Bereitstellung von GitHub per Klick ausführen. Führen Sie zum Bereitstellen von Diensten über eine Vorlage in GitHub die folgenden Schritte aus:

1. Öffnen Sie die Datei "template3" von GitHub. Öffnen Sie beispielsweise [Virtual network with two subnets](https://github.com/Azure/azure-quickstart-templates/tree/master/101-virtual-network).
2. Klicken Sie auf **Deploy to Azure**, und melden Sie sich dann mit Ihren Anmeldeinformationen im Azure-Portal an.
3. Überprüfen Sie die Vorlage, und klicken Sie dann auf **Speichern**.
4. Klicken Sie auf **Parameter bearbeiten**, und wählen Sie einen Speicherort aus, z. B. *USA, Westen* für VNet und Subnetze.
5. Ändern Sie ggf. die Parameter **ADDRESSPREFIX** und **SUBNETPREFIX**, und klicken Sie dann auf **OK**.
6. Klicken Sie auf **Ressourcengruppe auswählen**, und klicken Sie dann auf die Ressourcengruppe, die VNet und den Subnetzen hinzugefügt werden soll. Alternativ können Sie eine neue Ressourcengruppe erstellen, indem Sie auf **Oder neu erstellen** klicken.
3. Klicken Sie auf **Erstellen**. Beachten Sie die Kachel mit dem Text **Bereitstellung der Vorlagenbereitstellung**. Wenn die Bereitstellung abgeschlossen ist, wird ein Bildschirm wie der folgende angezeigt.

![Bereitstellung von Beispielvorlagen](./media/resource-groups-networking/Figure6.png)


## Weitere Informationen

[Azure Netzwerk-API-Verweise](https://msdn.microsoft.com/library/azure/dn948464.aspx)

[Azure PowerShell-Verweis für Netzwerke](https://msdn.microsoft.com/library/azure/mt163510.aspx)

[Azure Resource Manager-Vorlagensprache](../resource-group-authoring-templates.md)

[Azure-Netzwerk – Häufig verwendete Vorlagen](https://github.com/Azure/azure-quickstart-templates)

[Anbieter von Computerressourcen](../virtual-machines-azurerm-versus-azuresm)

[Azure Resource Manager-Übersicht](../resource-group-overview)

[Rollenbasierte Zugriffssteuerung im Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn906885.aspx)

[Verwenden von Tags im Azure Resource Manager](https://msdn.microsoft.com/library/azure/dn848368.aspx)

[Bereitstellungen von Vorlagen](https://msdn.microsoft.com/library/azure/dn790549.aspx)

<!---HONumber=Sept15_HO4-->