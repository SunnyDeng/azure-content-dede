<properties
   pageTitle="Problembehandlung beim Bereitstellen mit der REST-API | Microsoft Azure"
   description="Beschreibt, wie Sie mithilfe der Azure Resource Manager-REST-API Probleme in der Resource Manager-Bereitstellung erkennen und beheben können"
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

# Problembehandlung beim Bereitstellen von Ressourcengruppen mit der Azure Resource Manager-REST-API

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure-Befehlszeilenschnittstelle](resource-manager-troubleshoot-deployments-cli.md)
- [REST-API](resource-manager-troubleshoot-deployments-rest.md)

Wenn Sie einen Fehler beim Bereitstellen von Ressourcen in Azure erhalten haben, müssen Sie die aufgetretenen Fehler beheben. Die REST-API bietet Vorgänge, die es Ihnen ermöglichen, die Fehler zu finden und potenzielle Korrekturen zu ermitteln.

Sie können die Fehler in Ihrer Bereitstellung anhand der Überwachungsprotokolle oder der Bereitstellungsvorgänge beheben. In diesem Thema werden beide Methoden veranschaulicht.

Einige Fehler lassen sich vermeiden, indem Sie Ihre Vorlage und die Infrastruktur vor der Bereitstellung überprüfen. Weitere Informationen finden Sie unter [Bereitstellen einer Ressourcengruppe mit einer Azure Resource Manager-Vorlage](resource-group-template-deploy.md).

## Problembehandlung mit der REST-API

1. Stellen Sie Ihre Ressourcen mit dem Vorgang [Erstellen einer Vorlagenbereitstellung](https://msdn.microsoft.com/library/azure/dn790564.aspx) bereit. Um Informationen beizubehalten, die zum Debuggen hilfreich sein könnten, legen Sie die **debugSetting**-Eigenschaft in der JSON-Anforderung auf **requestContent** und/oder **responseContent** fest. 

        PUT https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}
          <common headers>
          {
            "properties": {
              "templateLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/template.json",
                "contentVersion": "1.0.0.0",
              },
              "mode": "Incremental",
              "parametersLink": {
                "uri": "http://mystorageaccount.blob.core.windows.net/templates/parameters.json",
                "contentVersion": "1.0.0.0",      
              },
              "debugSetting": {
                "detailLevel": "requestContent, responseContent"
              }
            }
          }

    Der **debugSetting**-Wert ist standardmäßig auf **none** festgelegt. Prüfen Sie beim Angeben des **debugSetting**-Werts sorgfältig den Informationstyp, den Sie während der Bereitstellung übergeben. Indem Sie Informationen über die Anforderung oder die Antwort protokollieren, machen Sie möglicherweise vertrauliche Daten verfügbar, die durch die Bereitstellungsvorgänge abgerufen werden.

2. Rufen Sie Informationen über eine Bereitstellung mit dem Vorgang [Abrufen von Informationen über eine Vorlagenbereitstellung](https://msdn.microsoft.com/library/azure/dn790565.aspx) ab.

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}?api-version={api-version}

    Beachten Sie bei der Antwort insbesondere die Elemente **provisioningState**, **correlationId** und **error**. **correlationId** wird verwendet, um verknüpfte Ereignisse nachzuverfolgen, und kann hilfreich sein, wenn mit dem technischen Support an einer Problembehandlung gearbeitet wird.
    
        { 
          ...
          "properties": {
            "provisioningState":"Failed",
            "correlationId":"d5062e45-6e9f-4fd3-a0a0-6b2c56b15757",
            ...
            "error":{
              "code":"DeploymentFailed","message":"At least one resource deployment operation failed. Please list deployment operations for details. Please see http://aka.ms/arm-debug for usage details.",
              "details":[{"code":"Conflict","message":"{\r\n  "error": {\r\n    "message": "Conflict",\r\n    "code": "Conflict"\r\n  }\r\n}"}]
            }  
          }
        }

3. Rufen Sie Informationen zu Bereitstellungsvorgängen mit dem Vorgang [Auflisten aller Vorlagenbereitstellungsvorgänge](https://msdn.microsoft.com/library/azure/dn790518.aspx) ab.

        GET https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/{resource-group-name}/providers/microsoft.resources/deployments/{deployment-name}/operations?$skiptoken={skiptoken}&api-version={api-version}

    Die Antwort wird Anforderungs- und/oder Antwortinformationen enthalten, basierend darauf, was Sie während der Bereitstellung in der **debugSetting**-Eigenschaft angegeben haben.
    
        {
          ...
          "properties": 
          {
            ...
            "request":{
              "content":{
                "location":"West US",
                "properties":{
                  "accountType": "Standard_LRS"
                }
              }
            },
            "response":{
              "content":{
                "error":{
                  "message":"Conflict","code":"Conflict"
                }
              }
            }
          }
        }

4. Rufen Sie Ereignisse aus den Überwachungsprotokollen für die Bereitstellung mit dem Vorgang [Auflisten der Verwaltungsereignisse in einem Abonnement](https://msdn.microsoft.com/library/azure/dn931934.aspx) ab.

        GET https://management.azure.com/subscriptions/{subscription-id}/providers/microsoft.insights/eventtypes/management/values?api-version={api-version}&$filter={filter-expression}&$select={comma-separated-property-names}


## Nächste Schritte

- Informationen zum Verwenden der Überwachungsprotokolle zum Überwachen anderer Aktionstypen finden Sie unter [Überwachen von Vorgängen mit Resource Manager](resource-group-audit.md).
- Informationen zum Überprüfen der Bereitstellung vor der Ausführung finden Sie unter [Bereitstellen einer Ressourcengruppe mit einer Azure Resource Manager-Vorlage](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0323_2016-->