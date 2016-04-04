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
	ms.date="03/07/2016"
	ms.author="tomfitz"/>


# Verwenden von Tags zum Organisieren von Azure-Ressourcen

Mit dem Ressourcen-Manager können Sie Ressourcen durch Anwenden von Tags logisch organisieren. Tags bestehen aus Schlüssel-Wert-Paaren, die Ressourcen mit den Eigenschaften identifizieren, die Sie definieren. Um Ressourcen so zu kennzeichnen, dass sie zur selben Kategorie gehören, wenden Sie das gleiche Tag auf diese Ressourcen an.

Wenn Sie Ressourcen mit einem bestimmten Tag anzeigen, sehen Sie Ressourcen aus allen Ressourcengruppen. Sie sind nicht auf Ressourcen in der gleichen Ressourcengruppe beschränkt, weshalb Sie Ihre Ressourcen unabhängig von Bereitstellungsbeziehungen organisieren können. Tags können besonders hilfreich sein, wenn Sie Ressourcen zur Abrechnung oder Verwaltung organisieren müssen.

Jedes Tag, das Sie einer Ressource oder Ressourcengruppe hinzufügen, wird automatisch der Taxonomie für das Abonnement hinzugefügt. Sie können die Taxonomie für Ihr Abonnement auch vorab mit Tagnamen und -werten füllen, die Sie zukünftig zum Markieren von Ressourcen verwenden möchten.

Jede Ressource oder Ressourcengruppe kann maximal 15 Tags haben. Der Tagname ist auf 512 Zeichen beschränkt und der Tagwert auf 256 Zeichen.

> [AZURE.NOTE] Sie können Tags nur auf Ressourcen anwenden, die Ressourcen-Manager-Vorgänge unterstützen. Wenn Sie eine virtuelle Maschine, ein virtuelles Netzwerk (VNET) oder Speicher über das klassische Bereitstellungsmodell erstellt haben (z. B. über das klassische Azure-Portal oder die Dienstverwaltungs-API), können Sie auf diese Ressource kein Tag anwenden. Sie müssen diese Ressourcen über den Ressourcen-Manager erneut bereitstellen, damit Tags unterstützt werden. Alle anderen Ressourcen unterstützen die Markierung durch Tags.

## Tags in Vorlagen

Um eine Ressource während der Bereitstellung zu markieren, fügen Sie der Ressource, die Sie bereitstellen, einfach das **tags**-Element hinzu, und geben Sie den Namen und den Wert des Tags an. Der Tagname und der Wert müssen nicht bereits in Ihrem Abonnement vorhanden sein. Sie können für jede Ressource bis zu 15 Tags bereitstellen.

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

Derzeit wird die Verarbeitung eines Objekts für die Tagnamen und -werte von Resource Manager nicht unterstützt. Stattdessen können Sie ein Objekt für die Tagwerte übergeben, müssen jedoch dennoch die Tagnamen angeben, wie unten dargestellt.

    {
      "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {
        "tagvalues": {
          "type": "object",
          "defaultValue": {
            "dept": "Finance",
            "project": "Test"
          }
        }
      },
      "resources": [
      {
        "apiVersion": "2015-06-15",
        "type": "Microsoft.Storage/storageAccounts",
        "name": "examplestorage",
        "tags": {
          "dept": "[parameters('tagvalues').dept]",
          "project": "[parameters('tagvalues').project]"
        },
        "location": "[resourceGroup().location]",
        "properties": {
          "accountType": "Standard_LRS"
        }
      }]
    }


## Tags im Portal

Über das Portal können Sie vorhandenen Ressourcen und Ressourcengruppen Tags hinzufügen. Verwenden Sie den Durchsuchenhub, um zu der Ressource oder Ressourcengruppe zu navigieren, die Sie markieren möchten. Klicken Sie dann auf den Tags-Teil im Übersichtsabschnitt oben im Fenster.

![Tags-Bereich im Blatt für Ressourcen oder Ressourcengruppen](./media/resource-group-using-tags/tag-icon.png)

Dies öffnet ein Fenster mit der Liste der Tags, die bereits angewendet wurden. Ist dies der erste Tag, wird die Liste leer sein. Um ein Tag hinzuzufügen, geben Sie einfach einen Namen und Wert an, und drücken Sie die EINGABETASTE. Nachdem Sie einige Tags hinzugefügt haben, sehen Sie die AutoVervollständigen-Optionen basierend auf bereits vorhandenen Tag-Namen und -Werten, um eine konsistente Klassifizierung Ihrer Ressourcen sicherzustellen und häufige Fehler, wie Rechtschreibfehler, zu vermeiden.

![Markieren von Ressourcen mit Name-Wert-Paaren](./media/resource-group-using-tags/tag-resources.png)

Um die Taxonomie von Tags im Portal anzuzeigen, verwenden Sie den Durchsuchenhub, um alles anzuzeigen, und wählen Sie dann Tags aus.

![Suchen von Markierungen über den Browse-Hub](./media/resource-group-using-tags/browse-tags.png)

Fixieren Sie die wichtigsten Tags für den schnellen Zugriff auf dem Startboard, und Sie können loslegen. Viel Spaß!

![Anheften von Markierungen am das Startmenü](./media/resource-group-using-tags/pin-tags.png)

## Tags und PowerShell

Tags sind direkt in Ressourcen und Ressourcengruppen vorhanden. Um festzustellen, welche Markierungen bereits angewendet wurden, können wir einfach eine Ressource oder Ressourcengruppe mit **Get-AzureRmResource** oder **Get-AzureRmResourceGroup** abrufen. Beginnen wir mit einer Ressourcengruppe.

    PS C:\> Get-AzureRmResourceGroup -Name tag-demo-group

    ResourceGroupName : tag-demo-group
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
                    Name         Value
                    ===========  ==========
                    Dept         Finance
                    Environment  Production


Dieses Cmdlet gibt mehrere Teile der Metadaten für die Ressourcengruppe zurück, einschließlich welche Tags ggf. angewendet wurden.

Beim Abrufen von Metadaten für eine Ressource werden die Tags nicht direkt angezeigt. Unten können Sie sehen, dass die Tags nur als Hashtable-Objekt angezeigt werden.

    PS C:\> Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group

    Name              : tfsqlserver
    ResourceId        : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    ResourceName      : tfsqlserver
    ResourceType      : Microsoft.Sql/servers
    Kind              : v12.0
    ResourceGroupName : tag-demo-group
    Location          : westus
    SubscriptionId    : {guid}
    Tags              : {System.Collections.Hashtable}

Die eigentlichen Tags können Sie durch Abrufen der **Tags**-Eigenschaft anzeigen.

    PS C:\> (Get-AzureRmResource -ResourceName tfsqlserver -ResourceGroupName tag-demo-group).Tags | %{ $_.Name + ": " + $_.Value }
    Dept: Finance
    Environment: Production

Anstatt die Tags für eine bestimmte Ressourcengruppe oder Ressource anzuzeigen, möchten Sie wahrscheinlich häufig alle Ressourcen oder Ressourcengruppen abrufen, die ein bestimmtes Tag und einen bestimmten Wert aufweisen. Um Ressourcengruppen mit einem bestimmten Tag abzurufen, verwenden Sie das Cmdlet **Find-AzureRmResourceGroup** mit dem Parameter **-Tag**.

    PS C:\> Find-AzureRmResourceGroup -Tag @{ Name="Dept"; Value="Finance" } | %{ $_.Name }
    tag-demo-group
    web-demo-group

Verwenden Sie zum Abrufen aller Ressourcen mit einem bestimmten Tag und Wert das Cmdlet **Find-AzureRmResource**.

    PS C:\> Find-AzureRmResource -TagName Dept -TagValue Finance | %{ $_.ResourceName }
    tfsqlserver
    tfsqldatabase

Um einer Ressourcengruppe, die über keine Tags verfügt, ein Tag hinzuzufügen, verwenden Sie einfach den Befehl **Set-AzureRmResourceGroup**, und geben Sie ein Tag-Objekt an.

    PS C:\> Set-AzureRmResourceGroup -Name test-group -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} )

    ResourceGroupName : test-group
    Location          : southcentralus
    ProvisioningState : Succeeded
    Tags              :
                    Name          Value
                    =======       =====
                    Dept          IT
                    Environment   Test
                    
Mit dem Befehl **SetAzureRmResource** können Sie einer Ressource, die über keine Tags verfügt, Tags hinzufügen.

    PS C:\> Set-AzureRmResource -Tag @( @{ Name="Dept"; Value="IT" }, @{ Name="Environment"; Value="Test"} ) -ResourceId /subscriptions/{guid}/resourceGroups/test-group/providers/Microsoft.Web/sites/examplemobileapp

Tags werden als Ganzes aktualisiert. Wenn Sie einer Ressource, die bereits markiert wurde, ein Tag hinzufügen, müssen Sie zum Speichern ein Array mit allen Tags verwenden, die Sie beibehalten möchten. Hierzu können Sie zunächst die vorhandenen Tags auswählen, dieser Gruppe ein neues Tag hinzufügen und dann alle Tags erneut anwenden.

    PS C:\> $tags = (Get-AzureRmResourceGroup -Name tag-demo).Tags
    PS C:\> $tags += @{Name="status";Value="approved"}
    PS C:\> Set-AzureRmResourceGroup -Name test-group -Tag $tags

Um ein Tag oder mehrere Tags zu entfernen, speichern Sie einfach das Array ohne die Tags, die Sie entfernen möchten.

Der Prozess ist für Ressourcen der gleiche, Sie verwenden jedoch die Cmdlets **Get-AzureRmResource** und **Set-AzureRmResource**.

Verwenden Sie das Cmdlet **Get-AzureRmTag**, um mithilfe von PowerShell eine Liste aller Markierungen innerhalb eines Abonnements abzurufen.

    PS C:/> Get-AzureRmTag
    Name                      Count
    ----                      ------
    env                       8
    project                   1

Möglicherweise sehen Sie Tags, die mit "hidden-" und "link:" beginnen. Hierbei handelt es sich um interne Tags, die Sie ignorieren und nicht ändern sollten.

Verwenden Sie das Cmdlet **New-AzureRmTag**, um der Taxonomie neue Markierungen hinzuzufügen. Diese Tags werden in die AutoVervollständigen-Funktion eingeschlossen, obwohl sie noch nicht auf Ressourcen oder Ressourcengruppen angewendet wurden. Um einen Markierungsnamen/Markierungswert zu entfernen, entfernen Sie zuerst die Markierung aus allen Ressourcen, mit denen es möglicherweise verwendet wird, und entfernen Sie es dann mit dem Cmdlet **Remove-AzureRmTag** aus der Taxonomie.

## Tags und Azure-Befehlszeilenschnittstelle

Tags sind direkt in Ressourcen und Ressourcengruppen vorhanden. Um festzustellen, welche Tags bereits angewendet werden, kann mit **azure group show** einfach eine Ressourcengruppe mit den zugehörigen Ressourcen abgerufen werden.

    azure group show -n tag-demo-group
    info:    Executing command group show
    + Listing resource groups
    + Listing resources for the group
    data:    Id:                  /subscriptions/{guid}/resourceGroups/tag-demo-group
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production
    data:    Resources:
    data:
    data:      Id      : /subscriptions/{guid}/resourceGroups/tag-demo-group/providers/Microsoft.Sql/servers/tfsqlserver
    data:      Name    : tfsqlserver
    data:      Type    : servers
    data:      Location: eastus2
    data:      Tags    : Dept=Finance;Environment=Production
    ...

Um nur die Tags für die Ressourcengruppe abzurufen, verwenden Sie ein JSON-Dienstprogramm, z. B. [jq](http://stedolan.github.io/jq/download/).

    azure group show -n tag-demo-group --json | jq ".tags"
    {
      "Dept": "Finance",
      "Environment": "Production" 
    }

Mit **azure resource show** können Sie die Tags für eine bestimmte Ressource anzeigen.

    azure resource show -g tag-demo-group -n tfsqlserver -r Microsoft.Sql/servers -o 2014-04-01-preview --json | jq ".tags"
    {
      "Dept": "Finance",
      "Environment": "Production"
    }
    
Sie können alle Ressourcen mit einem bestimmten Tag und Wert abrufen (siehe folgende Darstellung).

    azure resource list --json | jq ".[] | select(.tags.Dept == "Finance") | .name"
    "tfsqlserver"
    "tfsqlserver/tfsqldata"

Tags werden als Ganzes aktualisiert. Wenn Sie einer Ressource, die bereits markiert wurde, ein Tag hinzufügen, müssen Sie alle vorhandenen Tags abrufen, die Sie beibehalten möchten. Verwenden Sie **azure group set** zum Festlegen von Tagwerten für eine Ressourcengruppe, und geben Sie alle Tags für die Ressourcengruppe an.

    azure group set -n tag-demo-group -t Dept=Finance;Environment=Production;Project=Upgrade
    info:    Executing command group set
    ...
    data:    Name:                tag-demo-group
    data:    Location:            westus
    data:    Provisioning State:  Succeeded
    data:    Tags: Dept=Finance;Environment=Production;Project=Upgrade
    ...
    
Sie können die vorhandenen Tags in Ihrem Abonnement mit **azure tag list** auflisten und mit **azure tag create** ein neues Tag hinzufügen. Um ein Tag aus der Taxonomie für Ihr Abonnement zu entfernen, entfernen Sie das Tag zunächst aus allen Ressourcen, mit denen es verwendet werden kann, und entfernen Sie es dann mit **azure tag delete**.

## Tags und REST-API

Das Vorschauportal und PowerShell verwenden im Hintergrund die [Ressourcen-Manager-REST-API](https://msdn.microsoft.com/library/azure/dn848368.aspx). Wenn Sie das Tagging in eine andere Umgebung integrieren müssen, können Sie Tags mit GET für die Ressourcen-ID abrufen und die Tags mit einem PATCH-Aufruf aktualisieren.


## Tags und Abrechnung

Abrechnungsdaten können für unterstützte Dienste mithilfe von Tags gruppiert werden. So können Sie beispielsweise mithilfe von [in den Azure-Ressourcen-Manager integrierten virtuellen Computern](./virtual-machines/virtual-machines-windows-compare-deployment-models.md) Tags definieren und anwenden, um die Abrechnung für virtuelle Computer zu organisieren. Wenn Sie mehrere virtuelle Computer für verschiedene Organisationen betreiben, können Sie die Nutzung mithilfe von Tags nach Kostenstelle gruppieren. Mit Tags können Sie auch Kosten nach Laufzeitumgebung kategorisieren (beispielsweise zur Abrechnung der Nutzung virtueller Computer in der Produktionsumgebung).

Informationen zu Tags können über die [Azure-Ressourcennutzungs- und die RateCard-API](billing-usage-rate-card-overview.md) oder aus der Nutzungsdatei im CSV-Format abgerufen werden, die Sie aus dem [Azure-Kontenportal](https://account.windowsazure.com/) oder aus dem [EA-Portal](https://ea.azure.com) herunterladen können. Weitere Informationen zum programmgesteuerten Zugriff auf Abrechnungsinformationen finden Sie unter [Gewinnen von Einblicken in den Ressourcenverbrauch unter Microsoft Azure](billing-usage-rate-card-overview.md). Hinweise zu REST-API-Vorgängen finden Sie unter [Azure Billing REST API Reference (Preview)](https://msdn.microsoft.com/library/azure/1ea5b323-54bb-423d-916f-190de96c6a3c) (in englischer Sprache).

Wenn Sie die CSV-Nutzungsdatei für Dienste herunterladen, die die Verwendung von Tags für die Abrechnung unterstützen, sind die Tags in der Spalte **Tags** enthalten. Ausführlichere Informationen finden Sie unter [Informationen zu Ihrer Rechnung für Microsoft Azure](billing-understand-your-bill.md).

![Anzeigen von Tags in der Abrechnung](./media/resource-group-using-tags/billing_csv.png)

## Nächste Schritte

- Sie können mithilfe benutzerdefinierter Richtlinien Einschränkungen und Konventionen für Ihr Abonnement festlegen. Die Richtlinie, die Sie definieren, kann erfordern, dass ein bestimmtes Tag für alle Ressourcen festgelegt wird. Weitere Informationen finden Sie unter [Verwenden von Richtlinien für Ressourcenverwaltung und Zugriffssteuerung](resource-manager-policy.md).
- Eine Einführung zur Verwendung von Azure PowerShell für das Bereitstellen von Ressourcen finden Sie unter [Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager](./powershell-azure-resource-manager.md).
- Eine Einführung zur Verwendung der Azure-Befehlszeilenschnittstelle für das Bereitstellen von Ressourcen finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit der Azure-Ressourcenverwaltung](./xplat-cli-azure-resource-manager.md).
- Eine Einführung zum Verwenden des Portals finden Sie unter [Verwenden des Azure-Portals zum Verwalten Ihrer Azure-Ressourcen](./azure-portal/resource-group-portal.md).  

<!---HONumber=AcomDC_0323_2016-->