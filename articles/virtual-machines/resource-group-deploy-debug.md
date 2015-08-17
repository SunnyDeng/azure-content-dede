<properties
   pageTitle="Problembehebung beim Bereitstellen von Ressourcengruppen in Azure"
   description="Beschreibt häufige Probleme mit dem Bereitstellen von Ressourcen in Azure und zeigt, wie das Azure Portal, die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows (Azure CLI) und PowerShell genutzt werden können, um Bereitstellungen zu überprüfen und Problem zu erkennen."
   services="virtual-machines"
   documentationCenter=""
   authors="squillace"
   manager="timlt"
   editor=""/>

<tags
   ms.service="virtual-machines"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="command-line-interface"
   ms.workload="infrastructure"
   ms.date="04/25/2015"
   ms.author="rasquill"/>

# Problembehebung beim Bereitstellen von Ressourcengruppen in Azure

Zwar lassen sich durch vorherige Überprüfung verschiedener Aspekte Bereitstellungsfehler wesentlich leichter vermeiden, doch schlagen Bereitstellungen aus verschiedenen Gründen fehl. Dieses Dokument beschreibt Tools und Vorgänge zum Vermeiden einfacher Fehler, Herunterladen von Vorlagendateien und Überprüfen von Bereitstellungsprotokollen. Ebenfalls werden die wichtigsten Bereiche besprochen, die bei der Fehlerüberprüfung von Bereitstellungsprotokollen zu berücksichtigen sind.

## Nützliche Tools für die Interaktion mit Azure
Das Modul AzureResourceManager beinhaltet Cmdlets, mit denen Sie bei der Arbeit mit Ihren Azure-Ressourcen in der Befehlszeile Tools sammeln können, die Ihre Arbeit erleichtern. Vorlagen für Azure-Ressourcengruppen sind JSON-Dokumente, und die Azure-Ressourcen-Management-API akzeptiert und gibt JSON zurück. Das heißt JSON-Analysetools zählen zu den ersten Instrumenten, die Sie für leichteres Navigieren durch Informationen zu Ihren Ressourcen sowie für das Entwerfen von oder die Interaktion mit Vorlagen und Parameterdateien von Vorlagen verwenden werden.

### Mac-, Linux- und Windows-Tools
Wenn Sie die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows verwenden, sind Sie wahrscheinlich mit den Standard-Downloadtools wie **[curl](http://curl.haxx.se/)** und **[wget](https://www.gnu.org/software/wget/)** oder **[Resty](https://github.com/beders/Resty)** vertraut, sowie mit JSON-Dienstprogrammen wie **[jq](http://stedolan.github.io/jq/download/)**, **[jsawk](https://github.com/micha/jsawk)** und Sprachbibliotheken, die JSON verarbeiten können. (Viele dieser Tools verfügen auch über Ports für Windows, wie z.B. [wget](http://gnuwin32.sourceforge.net/packages/wget.htm). Tatsächlich gibt es verschiedene Arten, um Linux und andere Open Source-Tools auch in Windows auszuführen.)

Dieses Thema beinhaltet einige Azure Befehlszeilenschnittstelle-Befehle, die Sie mit **jq** verwenden können, um auf effizientere Weise genau die Informationen zu erhalten, die Sie wünschen. Sie sollten für ein besseres Verständnis Ihres Azure-Ressourceneinsatzes ein Ihnen vertrautes Toolset wählen.

### Windows PowerShell

Windows PowerShell verfügt über mehrere Basisbefehle, um die gleichen Prozeduren auszuführen.

- Verwenden Sie das Cmdlet **[Invole-WebRequest](https://technet.microsoft.com/library/hh849901%28v=wps.640%29)**, um Dateien wie Ressourcengruppen-Vorlagen oder JSON-Parameterdateien herunterzuladen.
- Verwenden Sie das Cmdlet **[ConvertFrom-Json](https://technet.microsoft.com/library/hh849898%28v=wps.640%29.aspx)**, um eine JSON-Zeichenfolge in ein benutzerdefiniertes Objekt zu konvertieren ([PSCustomObject](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx)), das für jedes Feld in der JSON-Zeichenfolge eine Eigenschaft hat.

## Vermeiden von Fehlern in der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows

Die Azure-Befehlszeilenschnittstelle verfügt über mehrere Befehle, um Fehler zu vermeiden und zu erkennen, was bei ihrem Auftreten fehlgeschlagen ist.

- **azure location list**. Dieser Befehl ruft die Speicherorte auf, die jeden Ressourcentyp unterstützen, wie z.B. der Anbieter für virtuelle Computer. Bevor Sie einen Speicherort für eine Ressource eingeben, verwenden Sie diesen Befehl, um zu überprüfen, ob der Speicherort den Ressourcentyp unterstützt.

    Da die Liste der Speicherorte lang sein kann und es viele Anbieter gibt, können Sie Tools verwenden, um Anbieter und Speicherorte zu überprüfen, bevor Sie einen Speicherort nutzen, der noch nicht zur Verfügung steht. Das folgende Skript nutzt **jq**, um die Speicherorte zu entdecken, an denen der Ressourcenanbieter für virtuelle Azure-Computer verfügbar ist. ()

        azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
        {
          "name": "Microsoft.Compute/virtualMachines",
          "location": "East US,West US,West Europe,East Asia,Southeast Asia,North Europe"
        }

- **azure group template validate<resource group>**. Mit diesem Befehl können Sie Ihre Vorlagen und die Vorlagenparameter überprüfen, bevor Sie sie verwenden. Geben Sie eine benutzerdefinierte oder eine Galerievorlage und die Vorlagenparameterwerte ein, die Sie verwenden möchten.

    Das folgende Beispiel zeigt, wie Sie eine Vorlage und jegliche erforderliche Parameter überprüfen können. Die Azure-Befehlszeilenschnittstelle fordert Sie auf, die erforderlichen Parameterwerte einzugeben.

        azure group template validate \
        > --template-uri "https://contoso.com/templates/azuredeploy.json" \
        > resource-group
        info:    Executing command group template validate
        info:    Supply values for the following parameters
        adminUserName: UserName
        adminPassword: PassWord
        + Initializing template configurations and parameters
        + Validating the template
        info:    group template validate command OK

## Informationserwerb zum Beheben von Bereitstellungsproblemen mit der Azure-Befehlszeilenschnittstelle

- **azure group log show<resource group>**: Dieser Befehl ruft die Einträge im Protokoll für die einzelnen Bereitstellungen der Ressourcengruppen ab. Falls ein Fehler auftritt, sollten Sie zunächst die Bereitstellungsprotokolle untersuchen.

        info:    Executing command group log show
        info:    Getting group logs
        data:    ----------
        data:    EventId:              <guid>
        data:    Authorization:
        data:                          action: Microsoft.Network/networkInterfaces/write
        data:                          role:   Subscription Admin
        data:                          scope:  /subscriptions/xxxxxxxxxxx/resourcegroups/templates/
                                               providers/Microsoft.Network/
                                               networkInterfaces/myNic
        data:    ResourceUri:          /subscriptions/xxxxxxxxxxxx/resourcegroups/templates/providers/
                                       Microsoft.Network/networkInterfaces/myNic
        data:    SubscriptionId:       <guid>
        data:    EventTimestamp (UTC): Wed Apr 22 2015 05:53:31 GMT+0000 (UTC)
        data:    OperationName:        Microsoft.Network/networkInterfaces/write
        data:    OperationId:          <guid>
        data:    Status:               Started
        data:    SubStatus:
        data:    Caller:
        data:    CorrelationId:        <guid>
        data:    Description:
        data:    HttpRequest:          clientRequestId: <guid>
                                       clientIpAddress: 000.000.00.000
                                       method:          PUT

        data:    Level:                Informational
        data:    ResourceGroup:        templates
        data:    ResourceProvider:     Microsoft.Network
        data:    EventSource:          Microsoft Resources
        data:    Properties:           requestbody: {"location":"West US","properties
                                       ":{"ipConfigurations":[{"
                                       name":"ipconfig1","properties":{"
                                       privateIPAllocationMethod

                                       ":"Dynamic","publicIPAddress":{"id":"/
                                       subscriptions/
                                       <guid>/
                                       resourceGroups/
                                       templates/providers/Microsoft.Network/
                                       publicIPAddresses/
                                       myPublicIP"},"subnet":{"idThe AzureResourceManager module includes cmdlets that ":"/subscriptions/
                                       <guid>/resourceGroups/templates/
                                       providers/
                                       Microsoft.Network/virtualNetworks/myVNET/subnets/
                                       Subnet-1
                                       "}}}]}}

Verwenden der Option**--Last-Deployment**, um nur das Protokoll für die aktuelle Bereitstellung abzurufen. Im folgenden Skript wird die Option **--Json** und **Jq** verwendet, um das Protokoll auf Fehler bei der Bereitstellung zu durchsuchen.

        azure group log show templates --json | jq '.[] | select(.status.value == "Failed")'

        {
          "claims": {
            "aud": "https://management.core.windows.net/",
            "iss": "https://sts.windows.net/<guid>/",
            "iat": "1429678549",
            "nbf": "1429678549",
            "exp": "1429682449",
            "ver": "1.0",
            "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
            "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn": "ahmet@contoso.onmicrosoft.com",
            "puid": "XXXXXXXXXXXXXX",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "ahmet",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "",
            "name": "Friendly Name",
            "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
            "groups": "<guid>",
            "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "ahmet@contoso.onmicrosoft.com",
            "appid": "<guid>",
            "appidacr": "0",
            "http://schemas.microsoft.com/identity/claims/scope": "user_impersonation",
            "http://schemas.microsoft.com/claims/authnclassreference": "1"
          },
          "properties": {},
          "authorization": {
            "action": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "role": "Subscription Admin",
            "scope": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1"
          },
          "eventChannels": "Operation",
          "eventDataId": "<guid>",
          "correlationId": "<guid>",
          "eventName": {
            "value": "EndRequest",
            "localizedValue": "End request"
          },
          "eventSource": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "level": "Error",
          "resourceGroupName": "templates",
          "resourceProviderName": {
            "value": "Microsoft.Resources",
            "localizedValue": "Microsoft Resources"
          },
          "resourceUri": "/subscriptions/<guid>/resourcegroups/templates/deployments/basic-vm-version-0.1",
          "operationId": "<guid>",
          "operationName": {
            "value": "Microsoft.Resources/subscriptions/resourcegroups/deployments/write",
            "localizedValue": "Update deployment"
          },
          "status": {
            "value": "Failed",
            "localizedValue": "Failed"
          },
          "subStatus": {},
          "eventTimestamp": "2015-04-22T05:53:40.8150293Z",
          "submissionTimestamp": "2015-04-22T05:54:00.6728843Z",10037FFE8E80BB65
          "subscriptionId": "<guid>"
        }


- **--verbose und -vv-Optionen**: Nutzen Sie die **--verbose**-Option, um den ausführlichen Modus einzustellen und so die Schritte der einzelnen Vorgänge anzuzeigen `stdout`. Nutzen Sie für einen vollständigen Anforderungsverlauf einschließlich der Schritte, die **--verbose** aktiviert, die **-vv**-Option. Die Mitteilungen liefern häufig wertvolle Hinweise zur Ursache von Fehlern.

- **Ihre Azure-Anmeldeinformationen wurden nicht eingestellt oder sind abgelaufen**: Geben Sie einfach `azure login` ein, um die Anmeldeinformationen zu aktualisieren. Stellen Sie für Hilfe mit Authentifizierungsfehlern sicher, dass Sie die [Azure-Befehlszeilenschnittstelle korrekt konfiguriert](../xplat-cli-connect.md) haben.

## Fehlervermeidung in Windows PowerShell

Das AzureResourceManager-Modul umfasst Cmdlets, mit denen Sie Fehler vermeiden können.


- **Get-AzureLocation**: Dieses Cmdlet ruft die Speicherorte ab, die die einzelnen Ressourcentypen unterstützen. Bevor Sie einen Speicherort für eine Ressource eingeben, verwenden Sie dieses Cmdlet, um zu überprüfen, ob der Speicherort den Ressourcentyp unterstützt.


- **Test-AzureResourceGroupTemplate**: Testen Sie Ihre Vorlage und Vorlagenparameter, bevor Sie sie benutzen. Geben Sie eine benutzerdefinierte oder eine Galerievorlage und die Vorlagenparameterwerte ein, die Sie verwenden möchten. Dieses Cmdlet testet, ob die Vorlage intern konsistent ist und ob die Parameterwerte der Vorlage entsprechen.

## Informationserwerb zur Behebung von Bereitstellungsproblemen in Windows PowerShell

- **Get-AzureResourceGroupLog**: Dieses Cmdlet ruft die Einträge im Protokoll für die einzelnen Bereitstellungen der Ressourcengruppe ab. Falls ein Fehler auftritt, sollten Sie zunächst die Bereitstellungsprotokolle untersuchen.

- **Verbose und Debug**: Die Cmdlets im AzureResourceManager-Modul rufen REST-APIs auf, die die eigentliche Arbeit durchführen. Um die Meldungen anzuzeigen, die von den APIs zurückgegeben werden, legen Sie die $DebugPreference-Variable auf "Continue" fest und verwenden in Ihren Befehlen den allgemeinen Verbose-Parameter. Die Mitteilungen liefern häufig wertvolle Hinweise zur Ursache von Fehlern.

- **Ihre Azure-Anmeldeinformationen wurden nicht eingestellt oder sind abgelaufen**: Verwenden Sie das Cmdlet Add-AzureAccount, um die Anmeldeinformationen während Ihrer Windows PowerShell-Sitzung zu aktualisieren. Die Anmeldeinformationen in einer Datei mit Veröffentlichungseinstellungen sind für die Cmdlets im AzureResourceManager-Modul nicht ausreichend.

## Probleme mit Authentifizierung, Abonnement- Rollen und Kontingent

Es gibt ein oder mehrere mögliche Probleme, die eine erfolgreiche Bereitstellung mit Authentifizierung, Autorisierung und Azure Active Directory verhindern. Unabhängig davon, wie Sie Ihre Azure-Ressourcengruppen verwalten, muss die Identität, die Sie nutzen, um sich bei Ihrem Konto anzumelden, entweder Azure Active Directory-Objekten oder Dienstprinzipalen entsprechen, die auch Geschäfts- oder Schulkonten oder Organisations-ID genannt werden.

Doch mit Azure Active Directory können Sie oder Ihr Verwalter sehr genau kontrollieren, welche Identitäten auf welche Ressourcen Zugriff haben. Falls Ihre Bereitstellungen fehlschlagen, überprüfen Sie die eigentlichen Anfragen auf Anzeichen von Authentifizierungs- oder Autorisierungsproblemen sowie die Bereitstellungsprotokolle für Ihre Ressourcengruppen. Sie werden feststellen, dass Sie zwar über Berechtigungen für manche Ressourcen verfügen, doch für andere nicht. Mithilfe der Azure-Befehlszeilenschnittstelle können Sie über die `azure ad` Befehle Azure Active Directory-Mandanten und -Benutzer überprüfen. (Eine vollständige Liste der Befehle der Azure-Befehlszeilenschnittstelle finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit Azure Resource Management](azure-cli-arm-commands.md).)

Probleme können auch auftreten, wenn eine Bereitstellung ein Standardkontingent erreicht, wie für Ressourcengruppe, Abonnements, Konten oder andere Bereiche. Vergewissern Sie sich, dass Sie über die für eine einwandfreie Bereitstellung benötigten Ressourcen verfügen. Die vollständigen Kontingentinformationen finden Sie unter [Azure-Abonnement und Service-Grenzen, Kontingente und Einschränkungen](../azure-subscription-service-limits.md).

Um die Kontingente Ihres eigenen Abonnements auf Kerne zu untersuchen, verwenden Sie den Befehl `azure vm list-usage` in der Azure-CLI und den `Get-AzureVMUsage`-Cmdlet in PowerShell. Im Folgenden wird der Befehl in der Azure-CLI gezeigt und veranschaulicht, dass das Kernkontingent für ein kostenloses Testkonto 4 ist:

    azure vm list-usage
    info:    Executing command vm list-usage
    Location: westus
    data:    Name   Unit   CurrentValue  Limit
    data:    -----  -----  ------------  -----
    data:    Cores  Count  0             4    
    info:    vm list-usage command OK

Wenn Sie versuchen, eine Vorlage bereitzustellen, die mehr als vier Kerne in der Region "Westen der USA" des oben aufgeführten Abonnements erstellt, wird eine Bereitstellungs-Fehlermeldung angezeigt, die etwa wie folgt aussieht (im Portal oder durch die Untersuchung der Bereitstellungsprotokolle):

    statusCode:Conflict
    serviceRequestId:xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx
    statusMessage:{"error":{"code":"OperationNotAllowed","message":"Operation results in exceeding quota limits of Core. Maximum allowed: 4, Current in use: 4, Additional requested: 2."}}

In diesen Fällen sollten Sie zum Portal navigieren und ein Supportproblem einreichen, um Ihr Kontingent für die Region, in der Sie diese bereitstellen möchten, zu erhöhen.

> [AZURE.NOTE]Denken Sie daran, dass für Ressourcengruppen das Kontingent für jede einzelne Region und nicht für das gesamte Abonnement gilt. Wenn Sie 30 Kerne in der Region "Westen der USA" bereitstellen müssen, müssen Sie 30 Ressourcenmanagementkerne in "Westen der USA" anfordern. Wenn Sie 30 Kerne in den Regionen, auf die Sie Zugriff haben, bereitstellen möchten, sollten Sie 30 Ressourcenmanagementkerne in allen Regionen anfordern. <!-- --> Um genaue Angaben zu Kernen zu machen, können Sie z. B. die Regionen angeben, für die Sie den entsprechenden Kontingentbetrag anfordern sollten, indem Sie den folgenden Befehl verwenden, der an **jq** für die Analyse von json weitergereicht wird. <!-- --> Azure Anbieter zeigen Microsoft.Compute --json | jq '.resourceTypes | select(.name == "virtualMachines") | { name,apiVersions, locations}' { "name": "virtualMachines", "apiVersions": [ "2015-05-01-preview", "2014-12-01-preview" ], "locations": [ "East US", "West US", "West Europe", "East Asia", "Southeast Asia" ] } an.
     

## Probleme mit der Azure-Befehlszeilenschnittstelle und PowerShell-Modus

Es kann sein, dass Azure-Ressourcen, die mithilfe der Serviceverwaltungs-API oder dem klassischen Portal bereitgestellt wurden, bei Verwendung der Ressourcenverwaltungs-API oder dem Azure-Portal nicht sichtbar sind. Es ist wichtig, Ressourcen unter Verwendung der gleichen Verwaltungs-API oder dem gleichen Portal zu verwalten, die/das Sie zum Erstellen verwendet haben. Ist eine Ressource verschwunden, überprüfen Sie Ihre Verfügbarkeit mithilfe der anderen Verwaltungs-API oder dem anderen Portal.

## Registrierungsprobleme beim Azure-Ressourcenanbieter

Ressourcen werden von Ressourcenanbietern verwaltet und ein Konto oder ein Abonnement kann aktiviert werden, um einen bestimmten Anbieter zu nutzen. Falls Ihre Aktivierung die Nutzung eines bestimmten Anbieters vorsieht, muss dieser auch für die Nutzung registriert sein. Die meisten Anbieter sind automatisch über das Azure-Portal oder die Befehlszeilenschnittstelle, die Sie verwenden, zur Nutzung registriert. Das gilt jedoch nicht für alle.

Um mithilfe der Azure-Befehlszeilenschnittstelle zu sehen, ob der Anbieter zur Nutzung registriert ist, verwenden Sie den `azure provider list` Befehl (es folgt ein gekürztes Beispiel für eine Ausgabe).

        azure provider list
        info:    Executing command provider list
        + Getting ARM registered providers
        data:    Namespace                        Registered
        data:    -------------------------------  -------------
        data:    Microsoft.Compute                Registered
        data:    Microsoft.Network                Registered  
        data:    Microsoft.Storage                Registered
        data:    microsoft.visualstudio           Registered
        data:    Microsoft.Authorization          Registered
        data:    Microsoft.Automation             NotRegistered
        data:    Microsoft.Backup                 NotRegistered
        data:    Microsoft.BizTalkServices        NotRegistered
        data:    Microsoft.Features               Registered
        data:    Microsoft.Search                 NotRegistered
        data:    Microsoft.ServiceBus             NotRegistered
        data:    Microsoft.Sql                    Registered
        info:    provider list command OK

Um weitere Informationen zu Anbietern einschließlich ihrer regionalen Verfügbarkeit zu erhalten, geben Sie `azure provider list --json` ein. Durch folgende Vorgehensweise wird nur das erste Element in der Liste zur Ansicht angezeigt:

        azure provider list --json | jq '.[0]'
        {
          "resourceTypes": [
            {
              "apiVersions": [
                "2014-02-14"
              ],
              "locations": [
                "North Central US",
                "East US",
                "West US",
                "North Europe",
                "West Europe",
                "East Asia"
              ],
              "properties": {},
              "name": "service"
            }
          ],
          "id": "/subscriptions/<guid>/providers/Microsoft.ApiManagement",
          "namespace": "Microsoft.ApiManagement",
          "registrationState": "Registered"
        }


Falls ein Anbieter registriert werden muss, verwenden Sie den `azure provider register <namespace>` Befehl, der den *Namespace*-Wert der vorhergehenden Liste nutzt.

## Verstehen einer erfolgreichen Bereitstellung für benutzerdefinierte Vorlagen

Wenn Sie selbsterstellte Vorlagen verwenden, ist es wichtig zu verstehen, dass das Azure-Resource-Management-System die erfolgreiche Bereitstellung meldet, wenn alle Anbieter erfolgreich von der Bereitstellung zurückkehren. Das bedeutet, dass alle Ihre Vorlagenelemente zur Nutzung bereitgestellt wurden.

Es bedeutet jedoch zwangsläufig nicht, dass Ihre Ressourcengruppe **aktiv ist und für Benutzer bereit steht**. Zum Beispiel erfordern die meisten Bereitstellungen, dass die Bereitstellung Upgrades herunterlädt, auf andere Ressourcen ohne Vorlagen wartet oder komplexe Skripte oder andere ausführbare Aktivitäten installiert, die Azure nicht kennt, da es sich um keine Aktivität handelt, die ein Anbieter überwacht. In diesen Fällen kann einige Zeit vergehen, bis Ihre Ressourcen tatsächlich nutzungsbereit sind. Daher sollten Sie davon ausgehen, dass der Bereitstellungsstatus einige Zeit bevor Ihre Bereitstellung genutzt werden kann, erfolgreich ist.

Sie können jedoch Azure daran hindern, einen erfolgreichen Bereitstellungsstatus zu melden, indem Sie ein benutzerdefiniertes Skript für Ihre benutzerdefinierte Vorlage erstellen -- beispielsweise mithilfe der [CustomerScriptExtension](http://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/) -- das weiß, wie die gesamte Bereitstellung für eine systemweite Einsatzbereitschaft zu überwachen ist und nur erfolgreich zurückkehrt, wenn Benutzer mit der gesamten Bereitstellung interagieren können. Wenn Sie sicherstellen möchten, dass Ihre Erweiterung die letzte ist, die ausgeführt wird, verwenden Sie die **dependsOn**-Eigenschaft in Ihrer Vorlage. Ein Beispiel ist hier [hier](https://msdn.microsoft.com/library/azure/dn790564.aspx) zu sehen.

## Zusammenführen von Vorlagen

Zuweilen ist es notwendig, zwei Vorlagen zusammenzuführen oder anhand einer übergeordneten Vorlage eine neue untergeordnete einzuführen. Das kann durch Nutzung einer Bereitstellungsressource innerhalb der Mastervorlage geschehen, um eine untergeordnete Vorlage bereitzustellen.


    {
            "name": "instance01",
            "type": "Microsoft.Resources/deployments",
            "apiVersion": "2015-01-01",
            "properties": {
                "mode": "Incremental",
                "templateLink": {
                    "uri": "https://mystore.blob.windows.net/azurermtemplates/my-child-template.json",
                    "contentVersion": "1.0.0.0"
                },
                "parameters": {
                    "storageAccountName": { "value": "[variables('stgAcctName1')]" },
                    "adminUsername": { "value": "[parameters('adminUsername')]" },
                    "adminPassword": { "value": "[parameters('adminPassword')]" }
                }
            }
    }


## Kreuzen von Ressourcengruppen

Es kann sein, dass Sie des Öfteren eine Ressource von außerhalb der aktuellen Ressourcengruppe, in der eine Vorlage bereitgestellt wird, benutzen möchten. In diesem Fall wird am häufigsten ein Speicherkonto oder ein virtuelles Netzwerk in einer anderen Ressourcengruppe verwendet. Das wird oft benötigt, sodass die Löschung der Ressourcengruppe, die die virtuellen Computer enthält, nicht zur Löschung der vhd-Blobs oder eines VNets führt, das von mehreren Ressourcengruppen genutzt wird. Das folgende Beispiel zeigt, wie eine Ressource einer externen Ressourcengruppe leicht genutzt werden kann:


    {
      "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json",
      "contentVersion": "1.0.0.0",
      "parameters": {
          "virtualNetworkName": {
              "type": "string"
          },
          "virtualNetworkResourceGroup": {
              "type": "string"
          },
          "subnet1Name": {
              "type": "string"
          },
          "nicName": {
              "type": "string"
          }
      },
      "variables": {
          "vnetID": "[resourceId(parameters('virtualNetworkResourceGroup'), 'Microsoft.Network/virtualNetworks', parameters('virtualNetworkName'))]",
          "subnet1Ref": "[concat(variables('vnetID'),'/subnets/', parameters('subnet1Name'))]"
      },
      "resources": [
      {
          "apiVersion": "2015-05-01-preview",
          "type": "Microsoft.Network/networkInterfaces",
          "name": "[parameters('nicName')]",
          "location": "[parameters('location')]",
          "properties": {
              "ipConfigurations": [{
                  "name": "ipconfig1",
                  "properties": {
                      "privateIPAllocationMethod": "Dynamic",
                      "subnet": {
                          "id": "[variables('subnet1Ref')]"
                      }
                  }
              }]
           }
      }]

    }

## Nächste Schritte

Um erfolgreich Vorlagen zu erstellen, lesen Sie bitte [Erstellen von Azure Resource Manager-Vorlagen](../resource-group-authoring-templates.md) und scrollen Sie durch die [AzureRMTemplates Repository](https://github.com/azurermtemplates/azurermtemplates) für bereitstellbare Beispiele. Ein Beispiel der **dependsOn**-Eigenschaft ist die [Lastenausgleich-Vorlage mit eingehender NAT-Regel](https://github.com/azurermtemplates/azurermtemplates/blob/master/101-create-internal-loadbalancer/azuredeploy.json).

<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png
[8]: ./media/markdown-template-for-new-articles/copytemplate.png

<!--Reference style links - using these makes the source content way more readable than using inline links-->
[gog]: http://google.com/
[yah]: http://search.yahoo.com/
[msn]: http://search.msn.com/
 

<!---HONumber=August15_HO6-->