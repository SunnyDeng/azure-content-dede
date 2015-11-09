<properties
   pageTitle="Ressourcen-Manager-Vorlage für Ressourcensperren | Microsoft Azure"
   description="Zeigt das Ressourcen-Manager-Schema für Ressourcensperren an."
   services="azure-resource-manager,storage"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="10/25/2015"
   ms.author="tomfitz"/>

# Speicherkonto – Vorlagenschema

Erstellt ein Speicherkonto.

## Schemaformat

Fügen Sie zum Erstellen eines Speicherkontos das folgende Schema im Ressourcenabschnitt der Vorlage hinzu.

    {
        "type": "Microsoft.Storage/storageAccounts",
        "apiVersion": "2015-06-15",
        "name": string,
        "location": string,
        "properties": 
        {
        	"accountType": string
        }
    }

## Werte

In den folgenden Tabellen sind die Werte beschrieben, die Sie im Schema festlegen müssen.

| Name | Typ | Erforderlich | Zulässige Werte | Beschreibung |
| ---- | ---- | -------- | ---------------- | ----------- |
| Typ | enum | Ja | **Microsoft.Storage/storageAccounts** | Der zu erstellende Ressourcentyp |
| "apiVersion": | enum | Ja | **2015-06-15** <br /> **2015-05-01-preview** | Die API-Version zum Erstellen der Ressource. | 
| Name | string | Ja | Zwischen 3 und 24 Zeichen, nur Ziffern und Kleinbuchstaben. | Der Name des zu erstellenden Speicherkontos. Der Name muss in Azure in allen Fällen eindeutig sein. Verwenden Sie die Funktion [uniqueString](resource-group-template-functions.md#uniquestring) mit Ihrer Benennungskonvention wie im folgenden Beispiel. |
| location | string | Ja | Informationen zum Bestimmen gültiger Bereiche finden Sie unter [Unterstützte Regionen](resource-manager-supported-services.md#supported-regions). | Der Bereich zum Hosten des Speicherkontos. |
| Eigenschaften | object | Ja | (siehe unten) | Ein Objekt, das den Typ des zu erstellenden Speicherkontos angibt.

### Objekt „properties“

| Name | Typ | Erforderlich | Zulässige Werte | Beschreibung |
| ---- | ---- | -------- | ---------------- | ----------- |
| accountType | string | Ja | **Standard\_LRS**<br />** Standard\_ZRS **<br />** Standard\_GRS **<br />** Standard\_RAGRS **<br />** Premium\_LRS ** | Typ des Speicherkontos Die zulässigen Werte sind: lokal redundanter Standardspeicher, zonenredundanter Standardspeicher, georedundanter Standardspeicher, georedundanter Standardspeicher mit Lesezugriff und lokal redundanter Premiumspeicher. Informationen zu diesen Kontotypen finden Sie unter [Azure Storage-Replikation](./storage/storage-redundancy.md). |

	
## Beispiele

Im folgenden Beispiel wird ein lokal redundantes Standardspeicherkonto mit einem eindeutigen Namen basierend auf der Ressourcen-Gruppen-Id bereitgestellt.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {},
        "variables": {},
        "resources": [
            {
                "type": "Microsoft.Storage/storageAccounts",
                "apiVersion": "2015-06-15",
                "name": "[concat('storage', uniqueString(resourceGroup().id))]",
		         "location": "[resourceGroup().location]",
        	     "properties": 
        	     {
        		      "accountType": "Standard_LRS"
        	     }
	        }
	    ],
	    "outputs": {}
    }

## Nächste Schritte

- Weitere Informationen zu Speichern finden Sie unter [Einführung in Microsoft Azure Storage](./storage/storage-introduction.md).
- Vorlagen, die ein neues Speicherkonto mit einem virtuellen Computer verwenden, finden Sie z. B. unter [Bereitstellen eines einfachen virtuellen Linux-Computers](https://azure.microsoft.com/documentation/templates/101-simple-linux-vm/) oder [Bereitstellen eines einfachen virtuellen Windows-Computers](https://azure.microsoft.com/documentation/templates/101-simple-windows-vm/).

<!---HONumber=Nov15_HO1-->