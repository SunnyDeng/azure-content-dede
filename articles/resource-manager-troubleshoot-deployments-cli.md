<properties
   pageTitle="Problembehandlung bei der Bereitstellung mit der Azure-Befehlszeilenschnittstelle | Microsoft Azure"
   description="Beschreibt, wie Sie mit der Azure-Befehlszeilenschnittstelle Probleme in der Resource Manager-Bereitstellung erkennen und beheben können."
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

# Problembehandlung beim Bereitstellen von Ressourcengruppen mit der Azure-Befehlszeilenschnittstelle

> [AZURE.SELECTOR]
- [Portal](resource-manager-troubleshoot-deployments-portal.md)
- [PowerShell](resource-manager-troubleshoot-deployments-powershell.md)
- [Azure-Befehlszeilenschnittstelle](resource-manager-troubleshoot-deployments-cli.md)
- [REST-API](resource-manager-troubleshoot-deployments-rest.md)

Wenn Sie einen Fehler beim Bereitstellen von Ressourcen in Azure erhalten haben, müssen Sie die aufgetretenen Fehler beheben. Die Azure-Befehlszeilenschnittstelle enthält Befehle, die es Ihnen ermöglichen, die Fehler zu finden und potenzielle Korrekturen zu ermitteln.

Sie können die Fehler in Ihrer Bereitstellung anhand der Überwachungsprotokolle oder der Bereitstellungsvorgänge beheben. In diesem Thema werden beide Methoden veranschaulicht.

Einige Fehler lassen sich vermeiden, indem Sie Ihre Vorlage und die Infrastruktur vor der Bereitstellung überprüfen. Weitere Informationen finden Sie unter [Bereitstellen einer Ressourcengruppe mit einer Azure Resource Manager-Vorlage](resource-group-template-deploy.md).

## Verwenden von Überwachungsprotokollen zur Problembehandlung

[AZURE.INCLUDE [resource-manager-audit-limitations](../includes/resource-manager-audit-limitations.md)]

Gehen Sie wie folgt vor, um Fehler für eine Bereitstellung anzuzeigen:

1. Führen Sie zum Anzeigen der Überwachungsprotokolle den Befehl **azure group log show** aus. Sie können die Option **--last-deployment** angeben, um nur das Protokoll der letzten Bereitstellung abzurufen.

        azure group log show ExampleGroup --last-deployment

2. Mit dem Befehl **azure group log show** können zahlreiche Informationen abgerufen werden. Bei der Problembehandlung konzentrieren Sie sich in der Regel auf fehlgeschlagene Vorgänge. Im folgenden Skript werden die Option **--json** und das JSON-Hilfsprogramm [jq](https://stedolan.github.io/jq/) verwendet, um das Protokoll nach Bereitstellungsfehlern zu durchsuchen.

        azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'
        
        {
        "claims": {
          "aud": "https://management.core.windows.net/",
          "iss": "https://sts.windows.net/<guid>/",
          "iat": "1442510510",
          "nbf": "1442510510",
          "exp": "1442514410",
          "ver": "1.0",
          "http://schemas.microsoft.com/identity/claims/tenantid": "{guid}",
          "http://schemas.microsoft.com/identity/claims/objectidentifier": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
          "puid": "XXXXXXXXXXXXXXXX",
          "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",
          "altsecid": "1:example.com:XXXXXXXXXXX",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string="">",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
          "name": "Tom FitzMacken",
          "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
          "groups": "{guid}",
          "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
          "wids": "{guid}",
          "appid": "{guid}",
          "appidacr": "0",
          "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
          "http://schemas.microsoft.com/claims/authnclassreference": "1",
          "ipaddr": "000.000.000.000"
        },
        "properties": {
          "statusCode": "Conflict",
          "statusMessage": "{"Code":"Conflict","Message":"Website with given name mysite already exists.","Target":null,"Details":[{"Message":"Website with given name
            mysite already exists."},{"Code":"Conflict"},{"ErrorEntity":{"Code":"Conflict","Message":"Website with given name mysite already exists.","ExtendedCode":
            "54001","MessageTemplate":"Website with given name {0} already exists.","Parameters":["mysite"],"InnerErrors":null}}],"Innererror":null}"
        },
        ...

    **properties** enthält Informationen in JSON zum fehlgeschlagenen Vorgang.

    Mit den Optionen **--verbose** und **-vv** können Sie weitere Informationen aus dem Protokoll anzeigen. Mit der Option **--verbose** können Sie die einzelnen Schritte der Vorgänge in `stdout` anzeigen. Den vollständigen Anforderungsverlauf zeigen Sie mit der Option **-vv** an. Die Mitteilungen liefern häufig wertvolle Hinweise zur Ursache von Fehlern.

3. Verwenden Sie den folgenden Befehl, um den Fokus auf die Statusmeldung für fehlerhafte Einträge zu legen:

        azure group log show ExampleGroup --json | jq -r ".[] | select(.status.value == "Failed") | .properties.statusMessage"


## Verwenden von Bereitstellungsvorgängen zur Problembehandlung

1. Rufen Sie den allgemeinen Status einer Bereitstellung mit dem Befehl **azure group deployment show** ab. Im folgenden Beispiel ist die Bereitstellung fehlgeschlagen.

        azure group deployment show --resource-group ExampleGroup --name ExampleDeployment
        
        info:    Executing command group deployment show
        + Getting deployments
        data:    DeploymentName     : ExampleDeployment
        data:    ResourceGroupName  : ExampleGroup
        data:    ProvisioningState  : Failed
        data:    Timestamp          : 2015-08-27T20:03:34.9178576Z
        data:    Mode               : Incremental
        data:    Name             Type    Value
        data:    ---------------  ------  ------------
        data:    siteName         String  ExampleSite
        data:    hostingPlanName  String  ExamplePlan
        data:    siteLocation     String  West US
        data:    sku              String  Free
        data:    workerSize       String  0
        info:    group deployment show command OK

2. Verwenden Sie Folgendes, um die Meldung für die fehlerhaften Vorgänge einer Bereitstellung anzuzeigen:

        azure group deployment operation list --resource-group ExampleGroup --name ExampleDeployment --json  | jq ".[] | select(.properties.provisioningState == "Failed") | .properties.statusMessage.Message"


## Nächste Schritte

- Informationen zur Überwachung anderer Arten von Aktionen anhand der Überwachungsprotokolle finden Sie unter [Überwachen von Vorgängen mit Resource Manager](resource-group-audit.md).
- Informationen zum Überprüfen der Bereitstellung vor der Ausführung finden Sie unter [Bereitstellen einer Ressourcengruppe mit einer Azure Resource Manager-Vorlage](resource-group-template-deploy.md).

<!---HONumber=AcomDC_0323_2016-->