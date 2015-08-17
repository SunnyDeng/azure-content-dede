<properties
   pageTitle="Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage"
   services="azure-resource-manager"
   description="Verwenden Sie den Azure-Ressourcen-Manager, um eine Anwendung in Azure bereitzustellen. Eine Vorlage ist eine JSON-Datei und kann über das Portal, PowerShell, die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows oder REST verwendet werden."
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
   ms.date="07/24/2015"
   ms.author="tomfitz"/>

# Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage

In diesem Thema wird erläutert, wie Azure-Ressourcen-Manager-Vorlagen verwendet werden, um Ihre Anwendung in Azure bereitzustellen. Es wird gezeigt, wie Sie Ihre Anwendung mithilfe von Azure PowerShell, der Azure-Befehlszeilenschnittstelle, der REST-API oder dem Microsoft Azure-Vorschauportal bereitstellen können.

Mit Azure-Ressourcen-Manager-Vorlagen können Sie schnell und einfach Ihre Anwendungen über eine deklarative JSON-Datei in Azure bereitstellen. In einer JSON-Vorlage können Sie mehrere Dienste bereitstellen, darunter beispielsweise Virtual Machines, Virtual Network, Storage, App Services und Datenbanken. Sie verwenden die gleiche Vorlage, um Ihre Anwendung während jeder Phase des Anwendungslebenszyklus wiederholt und konsistent bereitzustellen.

Zur Vereinfachung der Verwaltung Ihrer Anwendung können Sie alle Ressourcen, die einen gemeinsamen Lebenszyklus aufweisen, in einer einzelnen Ressourcengruppe organisieren. Ressourcengruppen erleichtern Ihnen das gleichzeitige Bereitstellen, Aktualisieren und Löschen aller verwandten Ressourcen. In den meisten Fällen ist eine Ressourcengruppe einer einzelnen Anwendung oder einer Anwendungsebene (bei großen Anwendungen) zugeordnet. Die Ressource, die über eine Vorlage bereitgestellt wird, befindet sich in einer einzelnen Ressourcengruppe, sie kann aber Abhängigkeiten in anderen Ressourcengruppen enthalten.

Sie können innerhalb einer Ressourcengruppe die Ausführung einer Bereitstellung verfolgen und den Status der Bereitstellung und Ausgaben der Vorlagenausführung anzeigen.

Wenn Sie eine Anwendung mit einer Vorlage bereitstellen, können Sie Parameterwerte angeben, um anzupassen, wie die Ressourcen erstellt werden. Sie geben Werte für diese Parameter entweder inline oder in einer Parameterdatei an.

## Konzepte

- Ressourcengruppe - Auflistung von Entitäten mit einem gemeinsam Lebenszyklus
- Ressourcen-Manager-Vorlage - deklarative JSON-Datei, die den Zielstatus einer Bereitstellung definiert
- Bereitstellung - Vorgang, bei dem Ausführung einer Ressourcen-Manager-Vorlage verfolgt wird
- Parameter - Werte, die vom Benutzer angegeben werden, der die Bereitstellung ausführt, um bereitgestellte Ressourcen anzupassen
- Parameterdatei - JSON-Datei, in der Parameternamen und -werte speichert sind 

## Szenarios

Mit Ressourcen-Manager-Vorlagen können Sie folgende Aktionen ausführen:

- Bereitstellen komplexer Anwendungen mit mehreren Ebenen, wie z. B. Microsoft SharePoint
- Konsistentes und wiederholtes Bereitstellen Ihrer Anwendungen
- Unterstützen von Entwicklungs-, Test- und Produktionsumgebungen
- Anzeigen des Status von Bereitstellungen
- Behandeln von Bereitstellungsfehlern mithilfe von Überwachungsprotokollen der Bereitstellung

## Bereitstellen mit dem Vorschauportal

Wissen Sie was? Jede Anwendung, die Sie über das [Vorschauportal](https://portal.azure.com/) erstellt haben, wird in einer Azure-Ressourcen-Manager-Vorlage gesichert. Erstellen Sie einfach einen virtuellen Computer, ein virtuelles Netzwerk, ein Speicherkonto, einen App Service der eine Datenbank über das Portal, und Sie können die Vorteile des Azure-Ressourcen-Managers ohne zusätzlichen Aufwand nutzen. Klicken Sie einfach auf das Symbol **Neu**, und Sie können direkt mit der Bereitstellung einer Anwendung über den Azure-Ressourcen-Manager beginnen.

![Neu](./media/resource-group-template-deploy/new.png)

Weitere Informationen zum Verwenden des Portals mit dem Azure-Ressourcen-Manager finden Sie unter [Verwenden des Azure-Vorschauportals zum Verwalten Ihrer Azure-Ressourcen](resource-group-portal.md).


## Bereitstellen mit PowerShell

Wenn Sie Azure PowerShell noch nicht mit dem Ressourcen-Manager verwendet haben, finden Sie unter [Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager](../powershell-azure-resource-manager.md) weitere Informationen.

1. Melden Sie sich bei Ihrem Azure-Konto an. Nach der Eingabe Ihrer Anmeldeinformationen gibt der Befehl die Informationen zu Ihrem Konto zurück.

        PS C:\> Add-AzureAccount

        Id                             Type       ...
        --                             ----    
        someone@example.com            User       ...   

2. Wenn Sie über mehrere Abonnements verfügen, geben Sie die Abonnement-ID ein, die Sie für die Bereitstellung verwenden möchten.

        PS C:\> Select-AzureSubscription -SubscriptionID <YourSubscriptionId>

3. Wechseln Sie zum AzureResourceManager-Modul.

        PS C:\> Switch-AzureMode AzureResourceManager

4. Erstellen Sie eine neue Ressourcengruppe, wenn noch keine vorhanden ist. Geben Sie den Namen der Ressourcengruppe und des gewünschten Speicherorts ein. Es wird eine Zusammenfassung der neuen Ressourcengruppe zurückgegeben.

        PS C:\> New-AzureResourceGroup -Name ExampleResourceGroup -Location "West US"
   
        ResourceGroupName : ExampleResourceGroup
        Location          : westus
        ProvisioningState : Succeeded
        Tags              :
        Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *
        ResourceId        : /subscriptions/######/resourceGroups/ExampleResourceGroup

5. Führen Sie zum Erstellen einer neuen Bereitstellung für die Ressourcengruppe den Befehl **New-AzureResourceGroupDeployment** aus, und geben Sie die erforderlichen Parameter ein. Die Parameter enthalten den Namen der Bereitstellung, den Namen der Ressourcengruppe, den Pfad oder die URL der erstellten Vorlage und alle anderen für Ihr Szenario erforderlichen Parameter.
   
     Sie haben die folgenden Möglichkeiten zum Angeben der Parameterwerte:
   
     - Verwenden Sie Inlineparameter.

            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -myParameterName "parameterValue"

     - Verwenden Sie ein Parameterobjekt.

            PS C:\> $parameters = @{"<ParameterName>"="<Parameter Value>"}
            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterObject $parameters

     - Verwenden Sie eine Parameterdatei. Informationen über die Vorlagendatei finden Sie unter [Parameterdatei](./#parameter-file).

            PS C:\> New-AzureResourceGroupDeployment -Name ExampleDeployment -ResourceGroupName ExampleResourceGroup -TemplateFile <PathOrLinkToTemplate> -TemplateParameterFile <PathOrLinkToParameterFile>

     Wenn die Ressourcengruppe bereitgestellt wurde, wird eine Zusammenfassung der Bereitstellung angezeigt.

          DeploymentName    : ExampleDeployment
          ResourceGroupName : ExampleResourceGroup
          ProvisioningState : Succeeded
          Timestamp         : 4/14/2015 7:00:27 PM
          Mode              : Incremental
          ...

6. Abrufen von Informationen über Fehler bei der Bereitstellung.

        PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed

7. Abrufen detaillierter Informationen über Fehler bei der Bereitstellung.

        PS C:\> Get-AzureResourceGroupLog -ResourceGroup ExampleResourceGroup -Status Failed -DetailedOutput

## Bereitstellen mit der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows

Wenn Sie die Azure-Befehlszeilenschnittstelle noch nicht mit dem Ressourcen-Manager verwendet haben, finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit der Azure-Ressourcenverwaltung](../xplat-cli-azure-resource-manager.md) weitere Informationen.

1. Melden Sie sich bei Ihrem Azure-Konto an. Nach der Eingabe Ihrer Anmeldeinformationen gibt der Befehl das Ergebnis der Anmeldung zurück.

        azure login
  
        ...
        info:    login command OK

2. Wenn Sie über mehrere Abonnements verfügen, geben Sie die Abonnement-ID ein, die Sie für die Bereitstellung verwenden möchten.

        azure account set <YourSubscriptionNameOrId>

3. Wechseln Sie zum Azure-Ressourcen-Manager-Modul. Der neue Modus wird bestätigt.

        azure config mode arm
   
        info:     New mode is arm

4. Erstellen Sie eine neue Ressourcengruppe, wenn noch keine vorhanden ist. Geben Sie den Namen der Ressourcengruppe und des gewünschten Speicherorts ein. Es wird eine Zusammenfassung der neuen Ressourcengruppe zurückgegeben.

        azure group create -n ExampleResourceGroup -l "West US"
   
        info:    Executing command group create
        + Getting resource group ExampleResourceGroup
        + Creating resource group ExampleResourceGroup
        info:    Created resource group ExampleResourceGroup
        data:    Id:                  /subscriptions/####/resourceGroups/ExampleResourceGroup
        data:    Name:                ExampleResourceGroup
        data:    Location:            westus
        data:    Provisioning State:  Succeeded
        data:    Tags:
        data:
        info:    group create command OK

5. Führen Sie zum Erstellen einer neuen Bereitstellung für die Ressourcengruppe den folgenden Befehl aus, und geben Sie die erforderlichen Parameter ein. Die Parameter enthalten den Namen der Bereitstellung, den Namen der Ressourcengruppe, den Pfad oder die URL der erstellten Vorlage und alle anderen für Ihr Szenario erforderlichen Parameter.
   
     Sie haben die folgenden Möglichkeiten zum Angeben der Parameterwerte:

     - Verwenden Sie Inlineparameter und eine lokale Vorlage.

             azure group deployment create -f <PathToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

     - Verwenden Sie Inlineparameter und einen Link zu einer Vorlage.

             azure group deployment create --template-uri <LinkToTemplate> {"ParameterName":"ParameterValue"} -g ExampleResourceGroup -n ExampleDeployment

     - Verwenden Sie eine Parameterdatei. Informationen über die Vorlagendatei finden Sie unter [Parameterdatei](./#parameter-file).
    
             azure group deployment create -f <PathToTemplate> -e <PathToParameterFile> -g ExampleResourceGroup -n ExampleDeployment

     Wenn die Ressourcengruppe bereitgestellt wurde, wird eine Zusammenfassung der Bereitstellung angezeigt.
  
           info:    Executing command group deployment create
           + Initializing template configurations and parameters
           + Creating a deployment
           ...
           info:    group deployment create command OK


6. Abrufen von Informationen über die aktuelle Bereitstellung.

         azure group log show -l ExampleResourceGroup

7. Abrufen detaillierter Informationen über Fehler bei der Bereitstellung.
      
         azure group log show -l -v ExampleResourceGroup

## Bereitstellen mit der REST-API
1. Legen Sie [allgemeine Parameter und Header](https://msdn.microsoft.com/library/azure/8d088ecc-26eb-42e9-8acc-fe929ed33563#bk_common) fest, einschließlich Authentifizierungstoken.
2. Erstellen Sie eine neue Ressourcengruppe, wenn noch keine vorhanden ist. Geben Sie Ihre Abonnement-ID, den Namen der neuen Ressourcengruppe und den Speicherort für Ihre Lösung an. Weitere Informationen finden Sie unter [Erstellen einer Ressourcengruppe](https://msdn.microsoft.com/library/azure/dn790525.aspx).

         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>?api-version=2015-01-01
           <common headers>
           {
             "location": "West US",
             "tags": {
               "tagname1": "tagvalue1"
             }
           }
   
3. Erstellen einer neuen Ressourcengruppenbereitstellung Geben Sie Ihre Abonnement-ID, den Namen der bereitzustellenden Ressourcengruppe, den Namen der Bereitstellung und den Speicherort der Vorlage an. Informationen über die Vorlagendatei finden Sie unter [Parameterdatei](./#parameter-file). Weitere Informationen über die REST-API zum Erstellen einer Ressourcengruppe finden Sie unter [Erstellen einer Vorlagenbereitstellung](https://msdn.microsoft.com/library/azure/dn790564.aspx).
    
         PUT https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
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
                }
              }
            }
   
4. Rufen Sie den Status der Vorlagenbereitstellung ab. Weitere Informationen finden Sie unter [Abrufen von Informationen zu einer Vorlagenbereitstellung](https://msdn.microsoft.com/library/azure/dn790565.aspx).

         GET https://management.azure.com/subscriptions/<YourSubscriptionId>/resourcegroups/<YourResourceGroupName>/providers/Microsoft.Resources/deployments/<YourDeploymentName>?api-version=2015-01-01
           <common headers>

## Parameterdatei

Bei der Verwendung einer Parameterdatei zum Übergeben von Parameterwerten an Ihre Vorlage während der Bereitstellung müssen Sie eine JSON-Datei in einem Format wie im folgenden Beispiel erstellen.

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "webSiteName": {
                "value": "ExampleSite"
            },
            "webSiteHostingPlanName": {
                "value": "DefaultPlan"
            },
            "webSiteLocation": {
                "value": "West US"
            }
       }
    }

## Nächste Schritte
- Ein Beispiel für die Bereitstellung von Ressourcen über die .NET-Clientbibliothek finden Sie unter [Bereitstellen von Ressourcen mithilfe von .NET-Bibliotheken und einer Vorlage](../arm-template-deployment.md).
- Ein ausführliches Beispiel für die Bereitstellung einer Anwendung finden Sie unter [Vorhersagbares Bereitstellen von Microservices in Azure](../app-service-web/app-service-deploy-complex-application-predictably.md).
- Informationen zu den Abschnitten der Azure-Ressourcen-Manager-Vorlage finden Sie unter [Erstellen von Vorlagen](../resource-group-authoring-templates.md).
- Unter [Vorlagenfunktionen](../resource-group-template-functions.md) finden Sie eine Liste der Funktionen, die Sie in einer Azure-Ressourcen-Manager-Vorlage verwenden können.

 

<!---HONumber=August15_HO6-->