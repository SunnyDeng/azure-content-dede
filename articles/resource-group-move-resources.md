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
	ms.date="12/18/2015" 
	ms.author="tomfitz"/>

# Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement

In diesem Thema erfahren Sie, wie Sie Ressourcen aus einer Ressourcengruppe in eine andere Ressourcengruppe verschieben. Sie können die Ressourcen auch in ein neues Abonnement verschieben. Möglicherweise müssen Sie Ressourcen aus Folgenden Gründen verschieben:

1. Zu Abrechnungszwecken muss eine Ressource sich in einem anderen Abonnement befinden.
2. Eine Ressource hat nicht mehr den gleichen Lebenszyklus wie die Ressourcen, mit denen sie zuvor gruppiert wurde. Sie möchten sie in eine neue Ressourcengruppe verschieben, damit Sie diese Ressource separat von anderen Ressourcen verwalten können.
3. Eine Ressource hat jetzt den gleichen Lebenszyklus wie andere Ressourcen in einer anderen Ressourcengruppe. Sie möchten sie in die Ressourcengruppe mit den anderen Ressourcen verschieben, damit Sie sie zusammen verwalten können.

Beim Verschieben einer Ressource sollten Sie einige wichtige Aspekte berücksichtigen:

1. Sie können nicht den Speicherort der Ressource ändern. Wenn Sie ein Ressource verschieben, wird sie nur in eine neue Ressourcengruppe verschoben. Die neue Ressourcengruppe hat möglicherweise einen anderen Speicherort, das heißt jedoch nicht, dass der Speicherort der Ressource geändert wird.
2. Die Zielressourcengruppe sollte nur Ressourcen enthalten, die den gleichen Anwendungslebenszyklus wie die verschobenen Ressourcen haben.
3. Wenn Sie Azure PowerShell oder die Azure-Befehlszeilenschnittstelle verwenden, stellen Sie sicher, dass Sie die neueste Version verwenden. Führen Sie zum Aktualisieren Ihrer Version den Microsoft-Webplattform-Installer aus, und überprüfen Sie, ob eine neue Version verfügbar ist. Weitere Informationen finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md) und [Installieren der Azure-Befehlszeilenschnittstelle](xplat-cli-install.md).
4. Es kann einige Zeit dauern, bis der Verschiebevorgang abgeschlossen ist. Die Eingabeaufforderung wartet so lange, bis der Vorgang abgeschlossen ist.
5. Beim Verschieben von Ressourcen werden die Quellgruppe und die Zielgruppe für die Dauer des Vorgangs gesperrt. Schreib- und Löschvorgänge in den Gruppen werden bis zum Abschluss der Verschiebung blockiert.

## Unterstützte Dienste

Nicht alle Dienste unterstützen derzeit die Möglichkeit, Ressourcen zu verschieben.

Derzeit unterstützen die folgenden Dienste das Verschieben in eine neue Ressourcengruppe und ein neues Abonnement:

- API Management
- Automation
- Batch
- Data Factory
- DocumentDB
- HDInsight-Cluster
- Schlüsseltresor
- Logik-Apps
- Mobile Engagement
- Notification Hubs
- Operational Insights
- Redis-Cache
- Suchen
- SQL-Datenbank
- Web-Apps (es gelten einige [Einschränkungen](app-service-web/app-service-move-resources.md))

Die folgenden Dienste unterstützen das Verschieben in eine neue Ressourcengruppe, jedoch nicht in ein neues Abonnement:

- Virtuelle Computer (Klassisch)
- Storage (Klassisch)

Die folgenden Dienste unterstützen derzeit nicht das Verschieben einer Ressource:

- Virtual Machines
- Virtuelle Netzwerke
- Speicher

Bei der Arbeit mit Web-Apps können Sie nicht nur einen App Services-Plan verschieben. Zum Verschieben von Web-Apps stehen folgende Optionen bereit:

- Verschieben Sie alle Ressourcen aus einer Ressourcengruppe in eine andere Ressourcengruppe, wenn die Zielressourcengruppe noch keine Microsoft.Web-Ressourcen enthält.
- Verschieben Sie Web-Apps in eine andere Ressourcengruppe, behalten Sie jedoch den App Services-Plan in der ursprünglichen Ressourcengruppe bei.

## Verschieben von Ressourcen mithilfe von PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

Verwenden Sie zum Verschieben vorhandener Ressourcen in eine andere Ressourcengruppe oder ein anderes Abonnement den Befehl **Move-AzureRmResource**.

Das erste Beispiel zeigt, wie Sie eine Ressource in eine neue Ressourcengruppe verschieben.

    PS C:\> $resource = Get-AzureRmResource -ResourceName ExampleApp -ResourceGroupName OldRG
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId $resource.ResourceId

Im zweiten Beispiel wird veranschaulicht, wie mehrere Ressourcen in eine neue Ressourcengruppe verschoben werden.

    PS C:\> $webapp = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExampleSite
    PS C:\> $plan = Get-AzureRmResource -ResourceGroupName OldRG -ResourceName ExamplePlan
    PS C:\> Move-AzureRmResource -DestinationResourceGroupName NewRG -ResourceId ($webapp.ResourceId, $plan.ResourceId)

Um Ressourcen in ein neues Abonnement zu verschieben, schließen Sie einen Wert für den **DestinationSubscriptionId**-Parameter ein.

## Verwenden der Azure-Befehlszeilenschnittstelle zum Verschieben von Ressourcen

Verwenden Sie zum Verschieben vorhandener Ressourcen in eine andere Ressourcengruppe oder ein anderes Abonnement den Befehl **azure resource move**. Das folgende Beispiel zeigt, wie Sie ein Redis-Cache in eine neue Ressourcengruppe verschieben. Geben Sie im **-i**-Parameter eine durch Kommas getrennte Liste der zu verschiebenden Ressourcen-IDs an.

    azure resource move -i "/subscriptions/{guid}/resourceGroups/OldRG/providers/Microsoft.Cache/Redis/examplecache" -d "NewRG"
    info:    Executing command resource move
    Move selected resources in OldRG to NewRG? [y/n] y
    + Moving selected resources to NewRG
    info:    resource move command OK

## Verschieben von Ressourcen mithilfe der REST-API

Führen Sie zum Verschieben vorhandener Ressourcen in eine andere Ressourcengruppe oder ein anderes Abonnement Folgendes aus:

    POST https://management.azure.com/subscriptions/{source-subscription-id}/resourcegroups/{source-resource-group-name}/moveResources?api-version={api-version} 

Geben Sie im Anforderungstext die Zielgruppe und die zu verschiebenden Ressourcen an. Weitere Informationen zur REST-Verschiebung finden Sie unter [Verschieben von Ressourcen](https://msdn.microsoft.com/library/azure/mt218710.aspx).

## Nächste Schritte
- [Verwenden von Azure PowerShell mit dem Ressourcen-Manager](./powershell-azure-resource-manager.md)
- [Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit der Azure-Ressourcenverwaltung](./virtual-machines/xplat-cli-azure-resource-manager.md)
- [Verwenden des Azure-Portals zum Verwalten von Ressourcen](azure-portal/resource-group-portal.md)
- [Verwenden von Tags zum Organisieren von Azure-Ressourcen](./resource-group-using-tags.md)

<!---HONumber=AcomDC_1223_2015-->