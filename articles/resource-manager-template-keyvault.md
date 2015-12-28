<properties
   pageTitle="Ressourcen-Manager-Vorlage für Schlüsseltresore | Microsoft Azure"
   description="Erläutert das Ressourcen-Manager-Schema für Schlüsseltresore."
   services="azure-resource-manager,key-vault"
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
   ms.date="12/15/2015"
   ms.author="tomfitz"/>

# Vorlagenschema für einen Schlüsseltresor

Erstellt einen Schlüsseltresor.

## Schemaformat

Fügen Sie zum Erstellen eines Schlüsseltresors das folgende Schema im Ressourcenabschnitt der Vorlage hinzu.

    {
        "type": "Microsoft.KeyVault/vaults",
        "apiVersion": "2015-06-01",
        "name": string,
        "location": string,
        "properties": {
            "enabledForDeployment": bool,
            "enabledForTemplateDeployment": bool,
            "enabledForVolumeEncryption": bool,
            "tenantId": string,
            "accessPolicies": [
                {
                    "tenantId": string,
                    "objectId": string,
                    "permissions": {
                        "keys": [ keys permissions ],
                        "secrets": [ secrets permissions ]
                    }
                }
            ],
            "sku": {
                "name": enum,
                "family": "A"
            }
        },
        "resources": [
             child resources
        ]
    }

## Werte

In den folgenden Tabellen sind die Werte beschrieben, die Sie im Schema festlegen müssen.

| Name | Typ | Erforderlich | Zulässige Werte | Beschreibung |
| ---- | ---- | -------- | ---------------- | ----------- |
| Typ | enum | Ja | **Microsoft.KeyVault/vaults** | Der zu erstellende Ressourcentyp. |
| apiVersion | enum | Ja | **2015-06-01** <br /> **2014-12-19-preview** | Die API-Version zum Erstellen der Ressource. | 
| name | string | Ja | | Der Name des zu erstellenden Schlüsseltresors. Der Name muss in Azure in allen Fällen eindeutig sein. Verwenden Sie die Funktion [uniqueString](resource-group-template-functions.md#uniquestring) mit Ihrer Benennungskonvention wie im folgenden Beispiel. |
| location | Zeichenfolge | Ja | Informationen zum Bestimmen gültiger Bereiche finden Sie unter [Unterstützte Regionen](resource-manager-supported-services.md#supported-regions). | Die Region zum Hosten des Schlüsseltresors. |
| Eigenschaften | Objekt | Ja | ([siehe unten](#properties)) | Ein Objekt, das den Typ des zu erstellenden Schlüsseltresors angibt. |
| angeben | array | Nein | [Geheime Schlüssel im Schlüsseltresor](resource-manager-template-keyvault-secret.md) | Untergeordnete Ressourcen für den Schlüsseltresor. |

<a id="properties" />
### properties-Objekt

| Name | Typ | Erforderlich | Zulässige Werte | Beschreibung |
| ---- | ---- | -------- | ---------------- | ----------- |
| enabledForDeployment | Boolescher Wert | Nein | **true** oder **false** | Gibt an, ob der Tresor für die Bereitstellung virtueller Computer oder die Service Fabric-Bereitstellung aktiviert ist. |
| enabledForTemplateDeployment | Boolescher Wert | Nein | **true** oder **false** | Gibt an, ob der Tresor zur Verwendung in Ressourcen-Manager-Vorlagenbereitstellungen aktiviert ist. Weitere Informationen finden Sie unter [Übergeben sicherer Werte während der Bereitstellung](resource-manager-keyvault-parameter.md). |
| enabledForVolumeEncryption | Boolescher Wert | Nein | **true** oder **false** | Gibt an, ob der Tresor für die Volumeverschlüsselung aktiviert ist. |
| tenantId | Zeichenfolge | Ja | Globally Unique Identifier (GUID) | Die Mandanten-ID für das Abonnement. Diese ID können Sie mit dem PowerShell-Cmdlet **Get-AzureRMSubscription** abrufen. |
| accessPolicies | array | Ja | ([siehe unten](#accesspolicies)) | Ein Array von bis zu 16 Objekten, die Berechtigungen für den Benutzer oder Dienstprinzipal angeben. |
| sku | Objekt | Ja | ([siehe unten](#sku)) | Die SKU für den Schlüsseltresor. |

<a id="accesspolicies" />
### properties.accessPolicies-Objekt

| Name | Typ | Erforderlich | Zulässige Werte | Beschreibung |
| ---- | ---- | -------- | ---------------- | ----------- |
| tenantId | Zeichenfolge | Ja | Globally Unique Identifier (GUID) | Die Mandanten-ID des Azure Active Directory-Mandanten mit der **objectId** in dieser Zugriffsrichtlinie |
| objectId | Zeichenfolge | Ja | Globally Unique Identifier (GUID) | Die Objekt-ID des AAD-Benutzers oder -Dienstprinzipals, der Zugriff auf den Tresor hat. Sie können den Wert über die Cmdlets **Get-AzureRMADUser** oder **Get-AzureRMADServicePrincipal** abrufen. |
| Berechtigungen | Objekt | Ja | ([siehe unten](#permissions)) | Die Berechtigungen, die für diesen Tresor für das Active Directory-Objekt gewährt werden. |

<a id="permissions" />
### properties.accessPolicies.permissions-Objekt

| Name | Typ | Erforderlich | Zulässige Werte | Beschreibung |
| ---- | ---- | -------- | ---------------- | ----------- |
| Schlüssel | array | Ja | Eine durch Trennzeichen getrennte Liste der folgenden Werte:<br />**all**<br />**backup**<br />**create**<br />**decrypt**<br />**delete**<br />**encrypt**<br />**get**<br />**import**<br />**list**<br />**restore**<br />**sign**<br />**unwrapkey**<br/>**update**<br />**verify**<br />**wrapkey** | Die Berechtigungen, die für Schlüssel in diesem Tresor für das Active Directory-Objekt gewährt werden. Dieser Wert muss als Array von zulässigen Werten angegeben werden. |
| secrets | array | Ja | Eine durch Trennzeichen getrennte Liste der folgenden Werte:<br />**all**<br />**delete**<br />**get**<br />**list**<br />**set** | Die Berechtigungen, die für geheime Schlüssel in diesem Tresor für das Active Directory-Objekt gewährt werden. Dieser Wert muss als Array von zulässigen Werten angegeben werden. |

<a id="sku" />
### properties.sku-Objekt

| Name | Typ | Erforderlich | Zulässige Werte | Beschreibung |
| ---- | ---- | -------- | ---------------- | ----------- |
| Name | enum | Ja | **standard**<br />**premium** | Die Dienstebene des zu verwendenden Schlüsseltresors. In der Dienstebene „Standard“ werden geheime Schlüssel und geschützte Schlüssel unterstützt. In der Dienstebene „Premium“ werden zudem HSM-geschützte Schlüssel unterstützt. |
| family | enum | Ja | **EINE DATEI** | Die zu verwendende SKU-Familie. 
 
	
## Beispiele

Im folgenden Beispiel werden ein Schlüsseltresor und ein geheimer Schlüssel bereitgestellt.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "keyVaultName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the vault"
                }
            },
            "tenantId": {
                "type": "string",
                "metadata": {
                   "description": "Tenant Id for the subscription and use assigned access to the vault. Available from the Get-AzureRMSubscription PowerShell cmdlet"
                }
            },
            "objectId": {
                "type": "string",
                "metadata": {
                    "description": "Object Id of the AAD user or service principal that will have access to the vault. Available from the Get-AzureRMADUser or the Get-AzureRMADServicePrincipal cmdlets"
                }
            },
            "keysPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to keys in the vault. Valid values are: all, create, import, update, get, list, delete, backup, restore, encrypt, decrypt, wrapkey, unwrapkey, sign, and verify."
                }
            },
            "secretsPermissions": {
                "type": "array",
                "defaultValue": [ "all" ],
                "metadata": {
                    "description": "Permissions to grant user to secrets in the vault. Valid values are: all, get, set, list, and delete."
                }
            },
            "vaultSku": {
                "type": "string",
                "defaultValue": "Standard",
                "allowedValues": [
                    "Standard",
                    "Premium"
                ],
                "metadata": {
                    "description": "SKU for the vault"
                }
            },
            "enabledForDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for VM or Service Fabric deployment"
                }
            },
            "enabledForTemplateDeployment": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for ARM template deployment"
                }
            },
            "enableVaultForVolumeEncryption": {
                "type": "bool",
                "defaultValue": false,
                "metadata": {
                    "description": "Specifies if the vault is enabled for volume encryption"
                }
            },
            "secretName": {
                "type": "string",
                "metadata": {
                    "description": "Name of the secret to store in the vault"
                }
            },
            "secretValue": {
                "type": "securestring",
                "metadata": {
                    "description": "Value of the secret to store in the vault"
                }
            }
        },
        "resources": [
        {
            "type": "Microsoft.KeyVault/vaults",
            "name": "[parameters('keyVaultName')]",
            "apiVersion": "2015-06-01",
            "location": "[resourceGroup().location]",
            "tags": {
                "displayName": "KeyVault"
            },
            "properties": {
                "enabledForDeployment": "[parameters('enabledForDeployment')]",
                "enabledForTemplateDeployment": "[parameters('enabledForTemplateDeployment')]",
                "enabledForVolumeEncryption": "[parameters('enableVaultForVolumeEncryption')]",
                "tenantId": "[parameters('tenantId')]",
                "accessPolicies": [
                {
                    "tenantId": "[parameters('tenantId')]",
                    "objectId": "[parameters('objectId')]",
                    "permissions": {
                        "keys": "[parameters('keysPermissions')]",
                        "secrets": "[parameters('secretsPermissions')]"
                    }
                }],
                "sku": {
                    "name": "[parameters('vaultSku')]",
                    "family": "A"
                }
            },
            "resources": [
            {
                "type": "secrets",
                "name": "[parameters('secretName')]",
                "apiVersion": "2015-06-01",
                "tags": { "displayName": "secret" },
                "properties": {
                    "value": "[parameters('secretValue')]"
                },
                "dependsOn": [
                    "[concat('Microsoft.KeyVault/vaults/', parameters('keyVaultName'))]"
                ]
            }]
        }]
    }


## Nächste Schritte

- Allgemeine Informationen zu Schlüsseltresoren finden Sie unter [Erste Schritte mit dem Azure-Schlüsseltresor](./key-vault/key-vault-get-started.md).
- Ein Beispiel für das Verweisen auf einen geheimen Schlüssel in einem Schlüsseltresor beim Bereitstellen von Vorlagen finden Sie unter [Übergeben sicherer Werte während der Bereitstellung](resource-manager-keyvault-parameter.md).

<!---HONumber=AcomDC_1217_2015-->