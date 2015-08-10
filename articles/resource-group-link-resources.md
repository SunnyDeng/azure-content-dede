<properties 
	pageTitle="Verknüpfen von Ressourcen im Azure-Ressourcen-Manager" 
	description="Erstellen Sie in Azure-Ressourcen-Manager einen Link zwischen Ressourcen in verschiedenen Ressourcengruppen." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="07/16/2015" 
	ms.author="tomfitz"/>

# Verknüpfen von Ressourcen im Azure-Ressourcen-Manager

Nach der Bereitstellung können Sie die Beziehungen oder Links zwischen Ressourcen abfragen. Abhängigkeiten informieren die Bereitstellung, aber dieser Lebenszyklus endet mit der Bereitstellung. Nach Abschluss der Bereitstellung gibt es keine identifizierte Beziehung zwischen abhängigen Ressourcen.

Stattdessen bietet Azure-Ressourcen-Manager ein neues Feature namens Ressourcenlinks, um Beziehungen zwischen Ressourcen herzustellen und abzufragen. Sie können bestimmen, welche Ressourcen mit oder welche Ressourcen aus einer Ressource verknüpft sind.

Der Bereich für einen Ressourcenlink kann ein Abonnement, eine Ressourcengruppe oder eine bestimmte Ressource sein. Dies bedeutet, dass Ressourcenlinks Beziehungen dokumentieren können, die sich über Ressourcengruppen hinweg erstrecken. Wenn Sie mit der Zerlegung Ihrer Projektmappe in mehrere Vorlagen und mehrere Ressourcengruppen beginnen, kann sich ein Mechanismus zur Identifizierung dieser Ressourcenlinks als nützlich erweisen. Beispielsweise ist es üblich, dass eine Datenbank mit einem eigenen Lebenszyklus sich in einer Ressourcengruppe und eine App mit einem anderen Lebenszyklus in einer anderen Ressourcengruppe befindet. Die App stellt eine Verbindung zur Datenbank her, sodass es einen Link zwischen den Ressourcen in verschiedenen Ressourcengruppen gibt.

Alle verknüpften Ressourcen müssen zum selben Abonnement gehören. Jede Ressource kann mit 50 anderen Ressourcen verknüpft werden. Wenn verknüpfte Ressourcen gelöscht oder verschoben werden, muss der Besitzer des Links den verbleibenden Link bereinigen.

## Verknüpfen in Vorlagen

Das folgende Beispiel zeigt eine Vorlage, die eine Ressource vom Typ "Microsoft.AppService/apiapps" mit einem Satz von unidirektionalen Beziehungen zu einer Website, einem Benachrichtigungshub und zu SQL-Datenbanken erstellt.

    {
        "$schema": "http://schemas.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "$system": {
                "type": "Object"
            }
        },
        "resources": [
            {
                "apiVersion": "2014-11-01",
                "type": "Microsoft.Web/sites",
                "name": "[parameters('$system').siteName]",
                "location": "[parameters('$system').location]",
                "resources": [
                    {
                        "apiVersion": "2014-11-01",
                        "name": "appsettings",
                        "type": "config",
                        "dependsOn": [
                            "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        ],
                        "properties": {
                            "MS_MobileServiceName": "[parameters('$system').apiAppName]",
                            "MS_NotificationHubName": "[variables('notificationHubName')]",
                            "MS_NotificationHubConnectionString": "[listkeys(resourceId('Microsoft.NotificationHubs/namespaces/notificationHubs/authorizationRules', variables('notificationHubNamespace'), variables('notificationHubName'), 'DefaultFullSharedAccessSignature'), '2014-09-01').primaryConnectionString]"
                        }
                    }
                ]
            },
            {
                "apiVersion": "[parameters('$system').apiVersion]",
                "type": "Microsoft.AppService/apiapps",
                "name": "[parameters('$system').apiAppName]",
                "properties": {
                    "accessLevel": "PublicAnonymous"
                },
                "resources": [
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-codesite",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]",
                            "[resourceId('Microsoft.Web/Sites', variables('userSiteName'))]"
                        ],
                        "properties": {
                            "targetId": "[resourceId('Microsoft.Web/sites', variables('userSiteName'))]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-notificationhub",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]",
                            "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        ],
                        "properties": {
                            "targetId": "[resourceId('Microsoft.NotificationHubs/namespaces/NotificationHubs', variables('notificationHubNamespace'), variables('notificationHubName'))]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-sqlserver",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]"
                        ],
                        "properties": {
                            "targetId": "[concat('/subscriptions/', parameters('userDatabase').subscriptionId, '/resourcegroups/', parameters('userDatabase').resourceGroupName, '/providers/Microsoft.Sql/servers/', parameters('userDatabase').serverName)]"
                        }
                    },
                    {
                        "apiVersion": "2015-01-01",
                        "type": "providers/links",
                        "name": "Microsoft.Resources/mobile-sqldb",
                        "dependsOn": [
                            "[resourceId('Microsoft.AppService/apiapps', parameters('$system').apiAppName)]"
                        ],
                        "properties": {
                            "targetId": "[concat('/subscriptions/', parameters('userDatabase').subscriptionId, '/resourcegroups/', parameters('userDatabase').resourceGroupName, '/providers/Microsoft.Sql/servers/', parameters('userDatabase').serverName, '/databases/', parameters('userDatabase').databaseName)]"
                        }
                    }
                ]
            }
        ]
    }

## Verknüpfen mit der REST-API

Um einen Link zwischen bereitgestellten Ressourcen zu definieren, führen Sie die folgenden Schritte aus:

    PUT https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/providers/Microsoft.Resources/links/{link-name}?api-version={api-version}

Ersetzen Sie {subscription-id} durch Ihre Abonnement-ID. Ersetzen Sie {resource-group}, {provider-namespace}, {resource-type} und {resource-name} durch die Werte, die die erste Ressource im Link identifizieren. Ersetzen Sie {link-name} durch den Namen des zu erstellenden Links. Verwenden Sie "api-version" 2015-01-01.

Schließen Sie in die Anforderung ein Objekt ein, das die zweite Ressource im Link definiert:

    {
        "name": "{new-link-name}",
        "properties": {
            "targetId": "subscriptions/{subscription-id}/resourceGroups/{resource-group}/providers/{provider-namespace}/{resource-type}/{resource-name}/",
            "notes": "{link-description}",
        }
    }

Das properties-Element enthält den Bezeichner für die zweite Ressource.

Weitere Beispiele, wie z. B. das Abrufen von Informationen zu Links, finden Sie unter [Verknüpfte Ressourcen](https://msdn.microsoft.com/library/azure/mt238499.aspx).

## Nächste Schritte

- Sie können Ihre Ressourcen auch mithilfe von Tags organisieren. Informationen zum Markieren von Ressourcen mit Tags finden Sie unter [Verwenden von Tags zum Organisieren von Azure-Ressourcen](resource-group-using-tags.md).
- Eine Beschreibung der Vorgehensweise zum Erstellen von Vorlagen und Definieren der bereitzustellenden Ressourcen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](resource-group-authoring-templates.md).

<!---HONumber=July15_HO5-->