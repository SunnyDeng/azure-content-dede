<properties
   pageTitle="Verwenden eines geheimen Schlüssels aus einem Schlüsseltresor mit Ressourcen-Manager-Vorlagen | Microsoft Azure"
   description="Informationen zum Übergeben eines geheimen Schlüssels aus einem Schlüsseltresor als Parameter während der Bereitstellung."
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

# Übergeben sicherer Werte während der Bereitstellung

Wenn Sie während der Bereitstellung einen sicheren Wert (z. B. ein Kennwort) als Parameter übergeben müssen, können Sie diesen Wert als geheimen Schlüssel in einem [Azure-Schlüsseltresor](./key-vault/key-vault-whatis.md) speichern und in anderen Ressourcen-Manager-Vorlagen auf ihn verweisen. In Ihrer Vorlage fügen Sie lediglich einen Verweis auf den geheimen Schlüssel ein, sodass dieser niemals offengelegt wird. Zudem müssen Sie den Wert für den geheimen Schlüssel nicht bei jedem Bereitstellen der Ressourcen manuell eingeben. Sie geben an, welche Benutzer oder Dienstprinzipale auf den geheimen Schlüssel zugreifen können.

> [AZURE.NOTE]Derzeit kann nur über die Azure-Befehlszeilenschnittstelle auf einen geheimen Schlüssel im Schlüsseltresor verwiesen werden. Diese Möglichkeit wird so bald wie möglich auch in Azure PowerShell hinzugefügt werden.

## Bereitstellen eines Schlüsseltresors und eines geheimen Schlüssels

Zum Erstellen eines Schlüsseltresors, auf den in anderen Ressourcen-Manager-Vorlagen verwiesen werden kann, müssen Sie die **enabledForTemplateDeployment**-Eigenschaft auf **true** festlegen und Zugriff für den Benutzer oder Dienstprinzipal erteilen, der die Bereitstellung ausführt, in der auf den geheimen Schlüssel verwiesen wird.

Informationen zum Bereitstellen eines Schlüsseltresors und eines geheimen Schlüssels finden Sie unter [Vorlagenschema für einen Schlüsseltresor](resource-manager-template-keyvault.md) und [Vorlagenschema für einen geheimen Schlüssel in einem Schlüsseltresor](resource-manager-template-keyvault-secret.md).

## Verweisen auf einen geheimen Schlüssel

Sie verweisen auf den geheimen Schlüssel über eine Parameterdatei, die Werte an die Vorlage übergibt. Sie verweisen auf den geheimen Schlüssel, indem Sie den Ressourcenbezeichner des Schlüsseltresors und den Namen des geheimen Schlüssels übergeben.

    "parameters": {
        "adminPassword": {
            "reference": {
                "keyVault": {
                    "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
                }, 
                "secretName": "sqlAdminPassword" 
            } 
        }
    }

Eine vollständige Parameterdatei kann beispielsweise wie folgt aussehen:

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "value": ""
            },
            "sqlsvrAdminLoginPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
                    },
                    "secretName": "adminPassword"
                }
            }
        }
    }

Der Parameter, der den geheimen Schlüssel annimmt, sollte ein Parameter des Typs **securestring** sein. Das folgende Beispiel zeigt die relevanten Abschnitte einer Vorlage auf, die einen SQL-Server bereitstellt, für den ein Administratorkennwort erforderlich ist.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "type": "string",
                "minLength": 4
            },
            "sqlsvrAdminLoginPassword": {
                "type": "securestring"
            },
            ...
        },
        "resources": [
        {
            "name": "[variables('sqlsvrName')]",
            "type": "Microsoft.Sql/servers",
            "location": "[resourceGroup().location]",
            "apiVersion": "2014-04-01-preview",
            "properties": {
                "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
                "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
            },
            ...
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }




## Nächste Schritte

- Allgemeine Informationen zu Schlüsseltresoren finden Sie unter [Erste Schritte mit dem Azure-Schlüsseltresor](./key-vault/key-vault-get-started.md).
- Weitere Informationen zum Bereitstellen von Vorlagen finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](resource-group-template-deploy.md).
- Vollständige Beispiele für Verweise auf geheime Schlüssel finden Sie unter [Key Vault examples](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples) (in englischer Sprache).

<!---HONumber=AcomDC_1217_2015-->