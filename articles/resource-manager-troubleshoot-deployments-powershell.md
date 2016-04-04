<properties
   pageTitle="Problembehandlung bei der Bereitstellung mit PowerShell | Microsoft Azure"
   description="Beschreibt, wie Sie mit Azure PowerShell Probleme in der Resource Manager-Bereitstellung erkennen und beheben können."
   services="azure-resource-manager,virtual-machines"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="timlt"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="03/21/2016"
   ms.author="tomfitz"/>

# Problembehandlung beim Bereitstellen von Ressourcengruppen mit Azure PowerShell

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure-Befehlszeilenschnittstelle](resource-manager-troubleshoot-deployments-cli.md)
- [REST-API](resource-manager-troubleshoot-deployments-rest.md)

Wenn Sie einen Fehler beim Bereitstellen von Ressourcen in Azure erhalten haben, müssen Sie die aufgetretenen Fehler beheben. Azure PowerShell enthält Cmdlets, die es Ihnen ermöglichen, leicht die Fehler zu finden und potenzielle Korrekturen zu ermitteln.

Sie können die Fehler in Ihrer Bereitstellung anhand der Überwachungsprotokolle oder der Bereitstellungsvorgänge beheben. In diesem Thema werden beide Methoden veranschaulicht.

Einige Fehler lassen sich vermeiden, indem Sie Ihre Vorlage und die Infrastruktur vor der Bereitstellung überprüfen. Weitere Informationen finden Sie unter [Bereitstellen einer Ressourcengruppe mit einer Azure Resource Manager-Vorlage](resource-group-template-deploy.md).

## Verwenden von Überwachungsprotokollen zur Problembehandlung

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Gehen Sie wie folgt vor, um Fehler für eine Bereitstellung anzuzeigen:

1. Um Protokolleinträge abzurufen, führen Sie den Befehl **Get-AzureRmLog** aus. Sie können die Parameter **ResourceGroup** und **Status** verwenden, um nur Ereignisse zurückzugeben, die für eine einzelne Ressourcengruppe nicht erfolgreich waren. Wenn Sie keine Start- und Endzeit angeben, werden die Einträge der letzten Stunde zurückgegeben. Führen Sie beispielsweise Folgendes aus, um die nicht erfolgreichen Vorgänge für die letzte Stunde der Ausführung zurückzugeben:

        PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -Status Failed

    Sie können hierbei eine bestimmte Zeitspanne angeben. Im nächsten Beispiel suchen wir nach nicht erfolgreichen Aktionen innerhalb der letzten 14 Tage.

        PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime (Get-Date).AddDays(-14) -Status Failed
      
    Sie können für fehlerhafte Aktionen auch eine genaue Start- und Endzeit festlegen:

        PS C:\> Get-AzureRmLog -ResourceGroup ExampleGroup -StartTime 2015-08-28T06:00 -EndTime 2015-09-10T06:00 -Status Failed

2. Wenn dieser Befehl zu viele Einträge und Eigenschaften zurückgibt, können Sie die Überwachung genauer fokussieren, indem Sie die **Properties**-Eigenschaft abrufen. Hier wird auch der Parameter **DetailedOutput** einbezogen, damit die Fehlermeldungen angezeigt werden.

        PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties
        
        Content
        -------
        {}
        {[statusCode, Conflict], [statusMessage, {"Code":"Conflict","Message":"Website with given name mysite already exists...
        {[statusCode, Conflict], [serviceRequestId, ], [statusMessage, {"Code":"Conflict","Message":"Website with given name...

3. Sie können die Suchergebnisse weiter eingrenzen, indem Sie die Statusmeldung für einen bestimmten Eintrag anzeigen.

        PS C:\> (Get-AzureRmLog -Status Failed -ResourceGroup ExampleGroup -DetailedOutput).Properties[1].Content["statusMessage"] | ConvertFrom-Json
        
        Code       : Conflict
        Message    : Website with given name mysite already exists.
        Target     :
        Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
        Innererror :


## Verwenden von Bereitstellungsvorgängen zur Problembehandlung

1. Verwenden Sie den Befehl **Get-AzureRmResourceGroupDeployment**, um den allgemeinen Status einer Bereitstellung abzurufen. Sie können die Ergebnisse nach den Bereitstellungen filtern, die nicht erfolgreich waren.

        PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup | Where-Object ProvisioningState -eq Failed
        
        DeploymentName    : ExampleDeployment
        ResourceGroupName : ExampleGroup
        ProvisioningState : Failed
        Timestamp         : 8/27/2015 8:03:34 PM
        Mode              : Incremental
        TemplateLink      :
        Parameters        :
        Name             Type                       Value
        ===============  =========================  ==========
        siteName         String                     ExampleSite
        hostingPlanName  String                     ExamplePlan
        siteLocation     String                     West US
        sku              String                     Free
        workerSize       String                     0
        
        Outputs           :

2. Jede Bereitstellung besteht in der Regel aus mehreren Vorgängen. Dabei stellt jeder Vorgang einen Schritt im Bereitstellungsprozess dar. Um einen Bereitstellungsfehler zu ermitteln, benötigen Sie in der Regel Einzelheiten zu den Bereitstellungsvorgängen. Den Status der Vorgänge können Sie mit **Get-AzureRmResourceGroupDeploymentOperation** abrufen.

        PS C:\> Get-AzureRmResourceGroupDeploymentOperation -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment | Format-List
        
        Id          : /subscriptions/{guid}/resourceGroups/ExampleGroup/providers/Microsoft.Resources/deployments/ExampleDeployment/operations/8518B32868A437C8
        OperationId : 8518B32868A437C8
        Properties  : @{ProvisioningOperation=Create; ProvisioningState=Failed; Timestamp=2016-03-16T20:05:37.2638161Z;
                      Duration=PT2.8834832S; TrackingId=192fbfbf-a2e2-40d6-b31d-890861f78ed3; StatusCode=Conflict;
                      StatusMessage=; TargetResource=}

3. Rufen Sie das Objekt **Properties** ab, um weitere Details zum Vorgang zu erhalten.

        PS C:\> (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties
        
        ProvisioningOperation : Create
        ProvisioningState     : Failed
        Timestamp             : 2016-03-16T20:05:37.2638161Z
        Duration              : PT2.8834832S
        TrackingId            : 192fbfbf-a2e2-40d6-b31d-890861f78ed3
        StatusCode            : Conflict
        StatusMessage         : @{Code=Conflict; Message=Website with given name mysite already exists.; Target=;
        Details=System.Object[]; Innererror=}
        TargetResource        : @{Id=/subscriptions/{guid}/resourceGroups/ExampleGroup/providers/
        Microsoft.Web/Sites/mysite; ResourceType=Microsoft.Web/Sites; ResourceName=mysite}

4. Verwenden Sie den folgenden Befehl, um den Fokus auf die Statusmeldung für nicht erfolgreiche Vorgänge zu legen:

        PS C:\> ((Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties | Where-Object ProvisioningState -eq Failed).StatusMessage
        
        Code       : Conflict
        Message    : Website with given name mysite already exists.
        Target     :
        Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
        Innererror :

## Nächste Schritte

- Informationen zur Überwachung anderer Arten von Aktionen anhand der Überwachungsprotokolle finden Sie unter [Überwachen von Vorgängen mit Resource Manager](resource-group-audit.md).
- Informationen zum Überprüfen der Bereitstellung vor der Ausführung finden Sie unter [Bereitstellen einer Ressourcengruppe mit einer Azure Resource Manager-Vorlage](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0323_2016-->