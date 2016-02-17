<properties
	pageTitle="Verwenden von Tags zum Organisieren von Azure-Ressourcen | Microsoft Azure"
	description="Zeigt, wie Sie Tags zum Organisieren von Ressourcen für die Abrechnung und Verwaltung anwenden können."
	services="azure-resource-manager"
	documentationCenter=""
	authors="tfitzmac"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="azure-resource-manager"
	ms.workload="multiple"
	ms.tgt_pltfrm="AzurePortal"
	ms.devlang="na"
	ms.topic="article"
	ms.date="12/02/2015"
	ms.author="tomfitz"/>


# Verwenden von Tags zum Organisieren von Azure-Ressourcen

Mit dem Ressourcen-Manager können Sie Ressourcen durch Anwenden von Tags logisch organisieren. Tags bestehen aus Schlüssel-Wert-Paaren, die Ressourcen mit den Eigenschaften identifizieren, die Sie definieren. Um Ressourcen so zu kennzeichnen, dass sie zur selben Kategorie gehören, wenden Sie das gleiche Tag auf diese Ressourcen an.

Wenn Sie Ressourcen mit einem bestimmten Tag anzeigen, sehen Sie Ressourcen aus allen Ressourcengruppen. Sie sind nicht auf Ressourcen in der gleichen Ressourcengruppe beschränkt, weshalb Sie Ihre Ressourcen unabhängig von Bereitstellungsbeziehungen organisieren können. Tags können besonders hilfreich sein, wenn Sie Ressourcen zur Abrechnung oder Verwaltung organisieren müssen.

Jedes Tag, das Sie einer Ressource oder Ressourcengruppe hinzufügen, wird automatisch der Taxonomie für das Abonnement hinzugefügt. Sie können die Taxonomie für Ihr Abonnement auch vorab mit Tagnamen und -werten füllen, die Sie zukünftig zum Markieren von Ressourcen verwenden möchten.

Jede Ressource oder Ressourcengruppe kann maximal 15 Tags haben. Der Tagname ist auf 512 Zeichen beschränkt und der Tagwert auf 256 Zeichen.

> [AZURE.NOTE] Sie können Tags nur auf Ressourcen anwenden, die Ressourcen-Manager-Vorgänge unterstützen. Wenn Sie eine virtuelle Maschine, ein virtuelles Netzwerk (VNET) oder Speicher über das klassische Bereitstellungsmodell erstellt haben (z. B. über das klassische Azure-Portal oder die Dienstverwaltungs-API), können Sie auf diese Ressource kein Tag anwenden. Sie müssen diese Ressourcen über den Ressourcen-Manager erneut bereitstellen, damit Tags unterstützt werden. Alle anderen Ressourcen unterstützen die Markierung durch Tags.

## Tags in Vorlagen

Sie können Ressourcen während der Bereitstellung auf einfache Weise Tags hinzufügen. Fügen Sie der Ressource, die Sie bereitstellen, einfach das Element **Tags** hinzu, und geben Sie den Tagnamen und den Wert an. Der Tagname und der Wert müssen nicht bereits in Ihrem Abonnement vorhanden sein. Sie können für jede Ressource bis zu 15 Tags bereitstellen.

Das folgende Beispiel zeigt ein Speicherkonto mit einem Tag.

    "resources": [
        {
            "type": "Microsoft.Storage/storageAccounts",
            "apiVersion": "2015-06-15",
            "name": "[concat('storage', uniqueString(resourceGroup().id))]",
            "location": "[resourceGroup().location]",
            "tags": {
                "dept": "Finance"
            },
            "properties": 
            {
                "accountType": "Standard_LRS"
            }
        }
    ]

## Tags im Portal

Das Markieren von Ressourcen und Ressourcengruppen im Portal ist einfach. Verwenden Sie den Durchsuchenhub, um zu der Ressource oder Ressourcengruppe zu navigieren, die Sie markieren möchten. Klicken Sie dann auf den Tags-Teil im Übersichtsabschnitt oben im Fenster.

![Tags-Bereich im Blatt für Ressourcen oder Ressourcengruppen](./media/resource-group-using-tags/tag-icon.png)

Dies öffnet ein Fenster mit der Liste der Tags, die bereits angewendet wurden. Ist dies der erste Tag, wird die Liste leer sein. Um ein Tag hinzuzufügen, geben Sie einfach einen Namen und Wert an, und drücken Sie die EINGABETASTE. Nachdem Sie einige Tags hinzugefügt haben, sehen Sie die AutoVervollständigen-Optionen basierend auf bereits vorhandenen Tag-Namen und -Werten, um eine konsistente Klassifizierung Ihrer Ressourcen sicherzustellen und häufige Fehler, wie Rechtschreibfehler, zu vermeiden.

![Markieren von Ressourcen mit Name-Wert-Paaren](./media/resource-group-using-tags/tag-resources.png)

Um die Taxonomie von Tags im Portal anzuzeigen, verwenden Sie den Durchsuchenhub, um alles anzuzeigen, und wählen Sie dann Tags aus.

![Suchen von Markierungen über den Browse-Hub](./media/resource-group-using-tags/browse-tags.png)

Fixieren Sie die wichtigsten Tags für den schnellen Zugriff auf dem Startboard, und Sie können loslegen. Viel Spaß!

![Anheften von Markierungen am das Startmenü](./media/resource-group-using-tags/pin-tags.png)

## Markieren mit PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

Tags sind direkt in Ressourcen und Ressourcengruppen vorhanden. Um festzustellen, welche Markierungen bereits angewendet wurden, können wir einfach eine Ressource oder Ressourcengruppe mit **Get-AzureRmResource** oder **Get-AzureRmResourceGroup** abrufen. Beginnen wir mit einer Ressourcengruppe.

    PS C:\> Get-AzureRmResourceGroup tag-demo

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

    Resources         :
                    Name                             Type                                  Location
                    ===============================  ====================================  ==============
                    CPUHigh ExamplePlan              microsoft.insights/alertrules         eastus
                    ForbiddenRequests tag-demo-site  microsoft.insights/alertrules         eastus
                    LongHttpQueue ExamplePlan        microsoft.insights/alertrules         eastus
                    ServerErrors tag-demo-site       microsoft.insights/alertrules         eastus
                    ExamplePlan-tag-demo             microsoft.insights/autoscalesettings  eastus
                    tag-demo-site                    microsoft.insights/components         centralus
                    ExamplePlan                      Microsoft.Web/serverFarms             southcentralus
                    tag-demo-site                    Microsoft.Web/sites                   southcentralus


Dieses Cmdlet gibt mehrere Teile der Metadaten für die Ressourcengruppe zurück, einschließlich welche Tags ggf. angewendet wurden. Um eine Ressourcengruppe zu markieren, verwenden Sie einfach den Befehl **Set-AzureRmResourceGroup**, und geben Sie einen Namen und Wert für das Tag an.

    PS C:\> Set-AzureRmResourceGroup tag-demo -Tag @( @{ Name="project"; Value="tags" }, @{ Name="env"; Value="demo"} )

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name     Value
                    =======  =====
                    project  tags
                    env      demo

Tags werden als Ganzes aktualisiert. Wenn Sie einer Ressource, die bereits markiert wurde, ein Tag hinzufügen, müssen Sie zum Speichern ein Array mit allen Tags verwenden, die Sie beibehalten möchten. Hierzu können Sie zunächst die vorhandenen Tags auswählen und dann ein neues hinzufügen.

    PS C:\> $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    PS C:\> $tags += @{Name="status";Value="approved"}
    PS C:\> Set-AzureRmResourceGroup tag-demo -Tag $tags

    ResourceGroupName : tag-demo
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name     Value
                    =======  ========
                    project  tags
                    env      demo
                    status   approved


Um ein Tag oder mehrere Tags zu entfernen, speichern Sie einfach das Array ohne die Tags, die Sie entfernen möchten.

Der Prozess ist für Ressourcen der gleiche, Sie verwenden jedoch die Cmdlets **Get-AzureRmResource** und **Set-AzureRmResource**.

Um Ressourcengruppen mit einem bestimmten Tag abzurufen, verwenden Sie das Cmdlet **Find-AzureRmResourceGroup** mit dem Parameter **-Tag**.

    PS C:\> Find-AzureRmResourceGroup -Tag @{ Name="env"; Value="demo" } | %{ $_.ResourceGroupName }
    rbacdemo-group
    tag-demo

Für niedrigere Azure PowerShell-Versionen als 1.0 verwenden Sie die folgenden Befehle, um Ressourcen mit einem bestimmten Tag abzurufen.

    PS C:\> Get-AzureResourceGroup -Tag @{ Name="env"; Value="demo" } | %{ $_.ResourceGroupName }
    rbacdemo-group
    tag-demo
    PS C:\> Get-AzureResource -Tag @{ Name="env"; Value="demo" } | %{ $_.Name }
    rbacdemo-web
    rbacdemo-docdb
    ...    

Verwenden Sie das Cmdlet **Get-AzureRmTag**, um mithilfe von PowerShell eine Liste aller Markierungen innerhalb eines Abonnements abzurufen.

    PS C:/> Get-AzureRmTag
    Name                      Count
    ----                      ------
    env                       8
    project                   1

Möglicherweise sehen Sie Tags, die mit "hidden-" und "link:" beginnen. Hierbei handelt es sich um interne Tags, die Sie ignorieren und nicht ändern sollten.

Verwenden Sie das Cmdlet **New-AzureRmTag**, um der Taxonomie neue Markierungen hinzuzufügen. Diese Tags werden in die AutoVervollständigen-Funktion eingeschlossen, obwohl sie noch nicht auf Ressourcen oder Ressourcengruppen angewendet wurden. Um einen Markierungsnamen/Markierungswert zu entfernen, entfernen Sie zuerst die Markierung aus allen Ressourcen, mit denen es möglicherweise verwendet wird, und entfernen Sie es dann mit dem Cmdlet **Remove-AzureRmTag** aus der Taxonomie.

## Markieren durch Tags mit der REST-API

Das Vorschauportal und PowerShell verwenden im Hintergrund die [Ressourcen-Manager-REST-API](https://msdn.microsoft.com/library/azure/dn848368.aspx). Wenn Sie das Tagging in eine andere Umgebung integrieren müssen, können Sie Tags mit GET für die Ressourcen-ID abrufen und die Tags mit einem PATCH-Aufruf aktualisieren.


## Tags und Abrechnung

Abrechnungsdaten können für unterstützte Dienste mithilfe von Tags gruppiert werden. So können Sie beispielsweise mithilfe von [in den Azure-Ressourcen-Manager integrierten virtuellen Computern](./virtual-machines/virtual-machines-azurerm-versus-azuresm.md) Tags definieren und anwenden, um die Abrechnung für virtuelle Computer zu organisieren. Wenn Sie mehrere virtuelle Computer für verschiedene Organisationen betreiben, können Sie die Nutzung mithilfe von Tags nach Kostenstelle gruppieren. Mit Tags können Sie auch Kosten nach Laufzeitumgebung kategorisieren (beispielsweise zur Abrechnung der Nutzung virtueller Computer in der Produktionsumgebung).

Informationen zu Tags können über die [Azure-Ressourcennutzungs- und die RateCard-API](billing-usage-rate-card-overview.md) oder aus der Nutzungsdatei im CSV-Format abgerufen werden, die Sie aus dem [Azure-Kontenportal](https://account.windowsazure.com/) oder aus dem [EA-Portal](https://ea.azure.com) herunterladen können. Weitere Informationen zum programmgesteuerten Zugriff auf Abrechnungsinformationen finden Sie unter [Gewinnen von Einblicken in den Ressourcenverbrauch unter Microsoft Azure](billing-usage-rate-card-overview.md). Hinweise zu REST-API-Vorgängen finden Sie unter [Azure Billing REST API Reference (Preview)](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (in englischer Sprache).

Wenn Sie die CSV-Nutzungsdatei für Dienste herunterladen, die die Verwendung von Tags für die Abrechnung unterstützen, sind die Tags in der Spalte **Tags** enthalten. Ausführlichere Informationen finden Sie unter [Informationen zu Ihrer Rechnung für Microsoft Azure](billing-understand-your-bill.md).

![Anzeigen von Tags in der Abrechnung](./media/resource-group-using-tags/billing_csv.png)

## Nächste Schritte

- Sie können mithilfe benutzerdefinierter Richtlinien Einschränkungen und Konventionen für Ihr Abonnement festlegen. Die Richtlinie, die Sie definieren, kann erfordern, dass ein bestimmtes Tag für alle Ressourcen festgelegt wird. Weitere Informationen finden Sie unter [Verwenden von Richtlinien für Ressourcenverwaltung und Zugriffssteuerung](resource-manager-policy.md).
- Eine Einführung zur Verwendung von Azure PowerShell für das Bereitstellen von Ressourcen finden Sie unter [Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager](./powershell-azure-resource-manager.md).
- Eine Einführung zur Verwendung der Azure-Befehlszeilenschnittstelle für das Bereitstellen von Ressourcen finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit der Azure-Ressourcenverwaltung](./xplat-cli-azure-resource-manager.md).
- Eine Einführung zum Verwenden des Portals finden Sie unter [Verwenden des Azure-Portals zum Verwalten Ihrer Azure-Ressourcen](./azure-portal/resource-group-portal.md).  

<!---HONumber=AcomDC_0204_2016-->