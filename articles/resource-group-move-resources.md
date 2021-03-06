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
	ms.date="11/06/2015" 
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
5. Beim Verschieben von Ressourcen werden die Quellgruppe und die Zielgruppe für die Dauer des Vorgangs gesperrt. Schreib- und Löschvorgänge in den Gruppen werden bis zum Abschluss der Verschiebung blockiert.

## Unterstützte Dienste

Nicht alle Dienste unterstützen derzeit die Möglichkeit, Ressourcen zu verschieben.

Derzeit unterstützen die folgenden Dienste das Verschieben in eine neue Ressourcengruppe und ein neues Abonnement:

- API Management
- Azure DocumentDB
- Azure Search
- Azure-Web-Apps (es gelten einige [Einschränkungen](app-service-web/app-service-move-resources.md))
- Data Factory
- Schlüsseltresor
- Mobile Engagement
- Operational Insights
- Redis-Cache
- SQL-Datenbank

Die folgenden Dienste unterstützen das Verschieben in eine neue Ressourcengruppe, jedoch nicht in ein neues Abonnement:

- Virtuelle Computer (Klassisch)
- Storage (Klassisch)

Die folgenden Dienste unterstützen derzeit nicht das Verschieben einer Ressource:

- Virtual Machines
- Virtuelle Netzwerke

Bei der Arbeit mit Web-Apps können Sie nicht nur einen App Services-Plan verschieben. Zum Verschieben von Web-Apps stehen folgende Optionen bereit:

- Verschieben Sie alle Ressourcen aus einer Ressourcengruppe in eine andere Ressourcengruppe, wenn die Zielressourcengruppe noch keine Microsoft.Web-Ressourcen enthält.
- Verschieben Sie Web-Apps in eine andere Ressourcengruppe, behalten Sie jedoch den App Services-Plan in der ursprünglichen Ressourcengruppe bei.

## Verschieben von Ressourcen mithilfe von PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

Verwenden Sie zum Verschieben vorhandener Ressourcen in eine andere Ressourcengruppe oder ein anderes Abonnement den Befehl **Move-AzureRmResource**.

Das erste Beispiel zeigt, wie Sie eine Ressource in eine neue Ressourcengruppe verschieben.

    PS C:\> Move-AzureRmResource -DestinationResourceGroupName TestRG -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OtherExample/providers/Microsoft.ClassicStorage/storageAccounts/examplestorage

Im zweiten Beispiel wird veranschaulicht, wie mehrere Ressourcen in eine neue Ressourcengruppe verschoben werden.

    PS C:\> $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite -ResourceType Microsoft.Web/sites
    PS C:\> $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan -ResourceType Microsoft.Web/serverFarms
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId ($webapp.ResourceId, $plan.ResourceId)

Um Ressourcen in ein neues Abonnement zu verschieben, schließen Sie einen Wert für den **DestinationSubscriptionId**-Parameter ein.

## Verschieben von Ressourcen mithilfe der REST-API

Führen Sie zum Verschieben vorhandener Ressourcen in eine andere Ressourcengruppe oder ein anderes Abonnement Folgendes aus:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

Geben Sie im Anforderungstext die Zielgruppe und die zu verschiebenden Ressourcen an. Weitere Informationen zur REST-Verschiebung finden Sie unter [Verschieben von Ressourcen](https://msdn.microsoft.com/library/azure/mt218710.aspx).

## Nächste Schritte
- [Verwenden von Azure PowerShell mit dem Ressourcen-Manager](./powershell-azure-resource-manager.md)
- [Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit der Azure-Ressourcenverwaltung](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [Verwenden des Azure-Portals zum Verwalten von Ressourcen](azure-portal/resource-group-portal.md)
- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](./resource-group-using-tags.md)

<!---HONumber=Nov15_HO3-->