<properties 
	pageTitle="Verschieben von Ressourcen in eine neue Ressourcengruppe" 
	description="Verwenden Sie Azure PowerShell oder die REST-API, um Ressourcen in eine neue Ressourcengruppe für den Azure-Ressourcen-Manager zu verschieben." 
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
	ms.date="07/14/2015" 
	ms.author="tomfitz"/>

# Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement

In diesem Thema erfahren Sie, wie Sie Ressourcen aus einer Ressourcengruppe in eine andere Ressourcengruppe verschieben. Sie können die Ressourcen auch in ein neues Abonnement verschieben. Möglicherweise müssen Sie Ressourcen aus Folgenden Gründen verschieben:

1. Zu Abrechnungszwecken muss eine Ressource sich in einem anderen Abonnement befinden.
2. Eine Ressource hat nicht mehr den gleichen Lebenszyklus wie die Ressourcen, mit denen sie zuvor gruppiert wurde. Sie möchten sie in eine neue Ressourcengruppe verschieben, damit Sie diese Ressource separat von anderen Ressourcen verwalten können.
3. Eine Ressource hat jetzt den gleichen Lebenszyklus wie andere Ressourcen in einer anderen Ressourcengruppe. Sie möchten sie in die Ressourcengruppe mit den anderen Ressourcen verschieben, damit Sie sie zusammen verwalten können.

Beim Verschieben einer Ressource sollten Sie einige wichtige Aspekte berücksichtigen:

1. Sie können nicht den Speicherort der Ressource ändern. Wenn Sie ein Ressource verschieben, wird sie nur in eine neue Ressourcengruppe verschoben. Die neue Ressourcengruppe hat möglicherweise einen anderen Speicherort, das heißt jedoch nicht, dass der Speicherort der Ressource geändert wird.
2. Die Zielressourcengruppe sollte nur Ressourcen enthalten, die den gleichen Anwendungslebenszyklus wie die verschobenen Ressourcen haben.
3. Wenn Sie Azure PowerShell verwenden, stellen Sie sicher, dass Sie die neueste Version verwenden. Der **Move-AzureResource**-Befehl wird regelmäßig aktualisiert. Führen Sie zum Aktualisieren Ihrer Version den Microsoft-Webplattform-Installer aus, und überprüfen Sie, ob eine neue Version verfügbar ist. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).
4. Es kann einige Zeit dauern, bis der Verschiebevorgang abgeschlossen ist. Die PowerShell-Eingabeaufforderung wartet so lange, bis der Vorgang abgeschlossen ist.

## Unterstützte Dienste

Nicht alle Dienste unterstützen derzeit die Möglichkeit, Ressourcen zu verschieben.

Derzeit unterstützen die folgenden Dienste das Verschieben in eine neue Ressourcengruppe und ein neues Abonnement:

- API Management
- Azure Search
- Data Factory
- Schlüsseltresor
- Mobile Engagement
- Operational Insights
- Redis-Cache

Die folgenden Dienste unterstützen das Verschieben in eine neue Ressourcengruppe, jedoch nicht in ein neues Abonnement:

- Compute (Klassisch)
- Storage (Klassisch)

Die folgenden Dienste unterstützen derzeit nicht das Verschieben einer Ressource:

- Virtuelle Netzwerke

## Verschieben von Ressourcen mithilfe von PowerShell

Verwenden Sie zum Verschieben vorhandener Ressourcen in eine andere Ressourcengruppe oder ein anderes Abonnement den **Move-AzureResource**-Befehl.

Das erste Beispiel zeigt, wie Sie eine Ressource in eine neue Ressourcengruppe verschieben.

    PS C:\> Move-AzureResource -DestinationResourceGroupName TestRG -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OtherExample/providers/Microsoft.ClassicStorage/storageAccounts/examplestorage

Im zweiten Beispiel wird veranschaulicht, wie mehrere Ressourcen in eine neue Ressourcengruppe verschoben werden.

    PS C:\> $webapp = Get-AzureResource -ResourceGroupName OldRG -ResourceName ExampleSite -ResourceType Microsoft.Web/sites
    PS C:\> $plan = Get-AzureResource -ResourceGroupName OldRG -ResourceName ExamplePlan -ResourceType Microsoft.Web/serverFarms
    PS C:\> Move-AzureResource -DestinationResourceGroupName NewRG -ResourceId ($webapp.ResourceId, $plan.ResourceId)

Um Ressourcen in ein neues Abonnement zu verschieben, schließen Sie einen Wert für den **DestinationSubscriptionId**-Parameter ein.

## Verschieben von Ressourcen mithilfe der REST-API

Führen Sie zum Verschieben vorhandener Ressourcen in eine andere Ressourcengruppe oder ein anderes Abonnement Folgendes aus:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

Ersetzen Sie **{source-subscription-Id}** und **{source-resource-group-name}** mit dem Abonnement und der Ressourcengruppe, in der derzeit die Ressourcen enthalten sind, die Sie verschieben möchten. Verwenden Sie für {api-version} **2015-01-01**.

Schließen Sie in die Anforderung ein JSON-Objekt ein, das die Zielressourcengruppe und die Ressourcen definiert, die Sie verschieben möchten.

    {
        "targetResourceGroup": "/subscriptions/{target-subscription-id}/resourceGroups/{target-resource-group-name}", "resources": [
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}",
            "/subscriptions/{source-id}/resourceGroups/{source-group-name}/providers/{provider-namespace}/{type}/{name}"
        ]
    }

## Nächste Schritte
- [Verwenden von Azure PowerShell mit dem Ressourcen-Manager](./powershell-azure-resource-manager.md)
- [Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit der Azure-Ressourcenverwaltung](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [Verwenden des Azure-Portals zum Verwalten von Ressourcen](azure-portal/resource-group-portal.md)
- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](./resource-group-using-tags.md)

<!---HONumber=July15_HO4-->