<properties
   pageTitle="Problembehandlung beim Bereitstellen von Ressourcengruppen in Azure | Microsoft Azure"
   description="Hier werden allgemeine Probleme beim Bereitstellen von Ressourcen beschrieben, die mithilfe des Ressourcen-Manager-Bereitstellungsmodells erstellt wurden. Zudem wird erläutert, wie Sie diese Probleme ermitteln und beheben."
   services="azure-resource-manager,virtual-machines-linux"
   documentationCenter=""
   tags="top-support-issue"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="vm-multiple"
   ms.workload="infrastructure"
   ms.date="01/28/2016"
   ms.author="tomfitz;rasquill"/>

# Problembehandlung beim Bereitstellen von Ressourcengruppen in Azure

Wenn während der Bereitstellung ein Problem auftritt, müssen Sie den Grund dafür ermitteln. Der Ressourcen-Manager bietet zwei Möglichkeiten, um herauszufinden, was passiert ist und warum es passiert ist. Sie können Bereitstellungsbefehle verwenden, um Informationen zu bestimmten Bereitstellungen für eine Ressourcengruppe abzurufen. Alternativ können Sie die Überwachungsprotokolle zum Abrufen von Informationen zu allen Vorgängen in einer Ressourcengruppe verwenden. Mit diesen Informationen können Sie das Problem beheben und die Vorgänge in Ihrer Lösung fortsetzen.

In diesem Thema geht es hauptsächlich um die Verwendung von Bereitstellungsbefehlen für das Beheben von Bereitstellungsfehlern. Weitere Informationen zum Nachverfolgen aller Vorgänge in Ihren Ressourcen mithilfe der Überwachungsprotokolle finden Sie unter [Überwachen von Vorgängen mit dem Ressourcen-Manager](../resource-group-audit.md).

In diesem Thema wird das Abrufen von Informationen zur Problembehandlung mit Azure PowerShell, mit der Azure-Befehlszeilenschnittstelle und der REST-API veranschaulicht. Informationen zum Beheben von Bereitstellungsfehlern mithilfe des Portals finden Sie unter [Verwenden des Azure-Portals zum Verwalten Ihrer Azure-Ressourcen](../azure-portal/resource-group-portal.md).

Lösungen für allgemeine Fehler bei der Verwendung werden ebenfalls in diesem Thema beschrieben.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-rm-include.md)]klassisches Bereitstellungsmodell. Sie können Ressourcengruppen nicht mit dem klassischen Bereitstellungsmodell erstellen.


## Problembehandlung mit PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../../includes/powershell-preview-inline-include.md)]

Sie können den allgemeinen Status einer Bereitstellung mit dem Befehl **Get-AzureRmResourceGroupDeployment** abrufen. Im folgenden Beispiel ist die Bereitstellung fehlgeschlagen.

    PS C:\> Get-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -DeploymentName ExampleDeployment

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

Jede Bereitstellung besteht in der Regel aus mehreren Vorgängen. Dabei stellt jeder Vorgang einen Schritt im Bereitstellungsprozess dar. Um einen Bereitstellungsfehler zu ermitteln, benötigen Sie in der Regel Einzelheiten zu den Bereitstellungsvorgängen. Den Status der Vorgänge können Sie mit **Get-AzureRmResourceGroupDeploymentOperation** abrufen.

    PS C:\> Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup
    Id                        OperationId          Properties         
    -----------               ----------           -------------
    /subscriptions/xxxxx...   347A111792B648D8     @{ProvisioningState=Failed; Timestam...
    /subscriptions/xxxxx...   699776735EFC3D15     @{ProvisioningState=Succeeded; Times...

Es werden zwei Vorgänge in der Bereitstellung angezeigt. Für einen Vorgang lautet der Bereitstellungszustand „Failed“, für den anderen Vorgang „Succeeded“.

Sie können die Statusmeldung mit dem folgenden Befehl abrufen:

    PS C:\> (Get-AzureRmResourceGroupDeploymentOperation -DeploymentName ExampleDeployment -ResourceGroupName ExampleGroup).Properties.StatusMessage

    Code       : Conflict
    Message    : Website with given name mysite already exists.
    Target     :
    Details    : {@{Message=Website with given name mysite already exists.}, @{Code=Conflict}, @{ErrorEntity=}}
    Innererror :

## Problembehandlung mit der Azure-Befehlszeilenschnittstelle

Sie können den allgemeinen Status einer Bereitstellung mit dem Befehl **azure group deployment show** abrufen. Im folgenden Beispiel ist die Bereitstellung fehlgeschlagen.

    azure group deployment show ExampleGroup ExampleDeployment

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


Weitere Informationen zu den Gründen einer fehlgeschlagenen Bereitstellung finden Sie in den Überwachungsprotokollen. Führen Sie zum Anzeigen der Überwachungsprotokolle den Befehl **azure group log show** aus. Sie können die Option **--last-deployment** angeben, um nur das Protokoll der letzten Bereitstellung abzurufen.

    azure group log show ExampleGroup --last-deployment

Mit dem Befehl **azure group log show** können zahlreiche Informationen abgerufen werden. Bei der Problembehandlung konzentrieren Sie sich in der Regel auf fehlgeschlagene Vorgänge. Im folgenden Skript werden die Option **--json** und **jq** verwendet, um das Protokoll nach Bereitstellungsfehlern zu durchsuchen. Informationen zu Tools wie **jq** finden Sie unter [Nützliche Tools für die Interaktion mit Azure](#useful-tools-to-interact-with-azure)

    azure group log show ExampleGroup --json | jq '.[] | select(.status.value == "Failed")'

    {
      "claims": {
        "aud": "https://management.core.windows.net/",
        "iss": "https://sts.windows.net/<guid>/",
        "iat": "1442510510",
        "nbf": "1442510510",
        "exp": "1442514410",
        "ver": "1.0",
        "http://schemas.microsoft.com/identity/claims/tenantid": "<guid>",
        "http://schemas.microsoft.com/identity/claims/objectidentifier": "<guid>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress": "someone@example.com",
        "puid": "XXXXXXXXXXXXXXXX",
        "http://schemas.microsoft.com/identity/claims/identityprovider": "example.com",

        "altsecid": "1:example.com:XXXXXXXXXXX",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/nameidentifier": "<hash string>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname": "Tom",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname": "FitzMacken",
        "name": "Tom FitzMacken",
        "http://schemas.microsoft.com/claims/authnmethodsreferences": "pwd",
        "groups": "<guid>",
        "http://schemas.xmlsoap.org/ws/2005/05/identity/claims/name": "example.com#someone@example.com",
        "wids": "<guid>",
        "appid": "<guid>",
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

## Problembehandlung mit der REST-API

Die Ressourcen-Manager-REST-API stellt URIs zum Abrufen von Informationen zu einer Bereitstellung und zu den Bereitstellungsvorgängen sowie Details zu einem bestimmten Vorgang bereit. Ausführliche Beschreibungen dieser Befehle finden Sie in folgenden Themen:

- [Abrufen von Informationen zu einer Vorlagenbereitstellung](https://msdn.microsoft.com/library/azure/dn790565.aspx)
- [Auflisten aller Vorlagenbereitstellungsvorgänge](https://msdn.microsoft.com/library/azure/dn790518.aspx)
- [Abrufen von Informationen zu einem Vorlagenbereitstellungsvorgang](https://msdn.microsoft.com/library/azure/dn790519.aspx)


## Aktualisieren abgelaufener Anmeldeinformationen

Bei der Bereitstellung tritt ein Fehler auf, wenn Ihre Azure-Anmeldedaten abgelaufen sind oder Sie sich nicht bei Ihrem Azure-Konto angemeldet haben. Ihre Anmeldeinformationen können ablaufen, wenn die Sitzung zu lange geöffnet ist. Sie können Ihre Anmeldeinformationen mit den folgenden Optionen aktualisieren:

- Verwenden Sie bei PowerShell das Cmdlet **Login-AzureRmAccount**. Die Anmeldeinformationen in einer Datei mit Veröffentlichungseinstellungen sind für die Cmdlets im AzureResourceManager-Modul nicht ausreichend.
- Verwenden Sie bei der Azure-Befehlszeilenschnittstelle **azure login**. Stellen Sie für Hilfe mit Authentifizierungsfehlern sicher, dass Sie die [Azure-Befehlszeilenschnittstelle korrekt konfiguriert](../xplat-cli-connect.md) haben.

## Überprüfen des Formats von Vorlagen und Parametern

Liegt die Vorlage oder der Parameter im falschen Format vor, tritt bei der Bereitstellung ein Fehler auf. Vor dem Ausführen einer Bereitstellung können Sie die Gültigkeit Ihrer Vorlagen und Parameter testen.

### PowerShell

Verwenden Sie bei PowerShell den Befehl **Test-AzureRmResourceGroupDeployment**.

    PS C:\> Test-AzureRmResourceGroupDeployment -ResourceGroupName ExampleGroup -TemplateFile c:\Azure\Templates\azuredeploy.json -TemplateParameterFile c:\Azure\Templates\azuredeploy.parameters.json
    VERBOSE: 12:55:32 PM - Template is valid.

### Azure-Befehlszeilenschnittstelle

Verwenden Sie bei der Azure-Befehlszeilenschnittstelle den Befehl **azure group template validate <resource group>**

Das folgende Beispiel zeigt, wie Sie eine Vorlage und alle erforderlichen Parameter zu überprüfen. Die Azure-Befehlszeilenschnittstelle fordert Sie zur Eingabe der erforderlichen Parameterwerte auf.

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

### REST-API

Informationen zur REST-API finden Sie unter [Überprüfen einer Vorlagenbereitstellung](https://msdn.microsoft.com/library/azure/dn790547.aspx).

## Überprüfen, welche Standorte die Ressource unterstützen

Verwenden Sie beim Angeben eines Standorts für eine Ressource einen von der Ressource unterstützten Standort. Bevor Sie einen Standort für eine Ressource eingeben, überprüfen Sie mit einem der folgenden Befehle, ob der Standort den Ressourcentyp unterstützt.

### PowerShell

Bei PowerShell-Versionen vor 1.0 können Sie die vollständige Liste der Ressourcen und Standorte mit dem Befehl **Get-AzureLocation** anzeigen.

    PS C:\> Get-AzureLocation

    Name                                    Locations                               LocationsString
    ----                                    ---------                               ---------------
    ResourceGroup                           {East Asia, South East Asia, East US... East Asia, South East Asia, East US,...
    Microsoft.ApiManagement/service         {Central US, East US, East US 2, Nor... Central US, East US, East US 2, Nort...
    Microsoft.AppService/apiapps            {East US, West US, South Central US,... East US, West US, South Central US, ...
    ...

Sie können folgendermaßen einen bestimmten Ressourcentyp angeben:

    PS C:\> Get-AzureLocation | Where-Object Name -eq "Microsoft.Compute/virtualMachines" | Format-Table Name, LocationsString -Wrap

    Name                                                        LocationsString
    ----                                                        ---------------
    Microsoft.Compute/virtualMachines                           East US, East US 2, West US, Central US, South Central US,
                                                                North Europe, West Europe, East Asia, Southeast Asia,
                                                                Japan East, Japan West

Verwenden Sie bei PowerShell 1.0 den Befehl **Get-AzureRmResourceProvider**, um unterstützte Standorte abzurufen.

    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web

    ProviderNamespace RegistrationState ResourceTypes               Locations
    ----------------- ----------------- -------------               ---------
    Microsoft.Web     Registered        {sites/extensions}          {Brazil South, ...
    Microsoft.Web     Registered        {sites/slots/extensions}    {Brazil South, ...
    Microsoft.Web     Registered        {sites/instances}           {Brazil South, ...
    ...

Sie können folgendermaßen einen bestimmten Ressourcentyp angeben:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations

    Brazil South
    East Asia
    East US
    Japan East
    Japan West
    North Central US
    North Europe
    South Central US
    West Europe
    West US
    Southeast Asia

### Azure-Befehlszeilenschnittstelle

Verwenden Sie bei der Azure-Befehlszeilenschnittstelle den Befehl **azure location list**. Da die Liste der Speicherorte lang sein kann und es viele Anbieter gibt, können Sie Tools verwenden, um Anbieter und Speicherorte zu überprüfen, bevor Sie einen Speicherort nutzen, der noch nicht zur Verfügung steht. Das folgende Skript ermittelt mithilfe von **jq** die Standorte, an denen der Ressourcenanbieter für virtuelle Azure-Computer verfügbar ist.

    azure location list --json | jq '.[] | select(.name == "Microsoft.Compute/virtualMachines")'
    {
      "name": "Microsoft.Compute/virtualMachines",
      "location": "East US,East US 2,West US,Central US,South Central US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West"
    }

### REST-API

Informationen zur REST-API finden Sie unter [Abrufen von Informationen zu einem Ressourcenanbieter](https://msdn.microsoft.com/library/azure/dn790534.aspx).

## Erstellen eindeutiger Ressourcennamen

Bei einigen Ressourcen, insbesondere Speicherkonten, Datenbankservern und Websites, müssen Sie einen im gesamten Azure-Dienst eindeutigen Namen für die Ressource angeben. Derzeit gibt es keine Möglichkeit, einen Namen auf seine Eindeutigkeit zu testen. Es wird empfohlen, eine Benennungskonvention zu verwenden, die wahrscheinlich nicht von anderen Organisationen verwendet wird.

## Probleme mit Authentifizierung, Abonnement, Rolle und Kontingent

Es gibt ein oder mehrere mögliche Probleme, die eine erfolgreiche Bereitstellung mit Authentifizierung, Autorisierung und Azure Active Directory verhindern. Unabhängig davon, wie Sie Ihre Azure-Ressourcengruppen verwalten, muss die von Ihnen zur Anmeldung in Ihrem Konto verwendete Identität ein Azure Active Directory-Objekt sein. Diese Identität kann ein Geschäfts- oder Schulkonto sein, das Sie erstellt haben oder Ihnen zugewiesen wurde, oder Sie können einen Dienstprinzipal für Anwendungen erstellen.

Doch mit Azure Active Directory können Sie oder Ihr Verwalter sehr genau kontrollieren, welche Identitäten auf welche Ressourcen Zugriff haben. Falls Ihre Bereitstellungen nicht erfolgreich sind, überprüfen Sie die eigentlichen Anfragen auf Anzeichen von Authentifizierungs- oder Autorisierungsproblemen sowie die Bereitstellungsprotokolle für Ihre Ressourcengruppen. Dabei stellen Sie unter Umständen fest, dass Sie nicht für alle Ressourcen über Berechtigungen verfügen. Mithilfe der Azure-Befehlszeilenschnittstelle können Sie über die `azure ad` Befehle Azure Active Directory-Mandanten und -Benutzer überprüfen. (Eine vollständige Liste mit den Befehlen der Azure-Befehlszeilenschnittstelle finden Sie unter [Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit dem Azure-Ressourcen-Manager](azure-cli-arm-commands.md).)

Probleme können auch auftreten, wenn eine Bereitstellung ein Standardkontingent erreicht (etwa für eine Ressourcengruppe, ein Abonnement, ein Konto oder Ähnliches). Vergewissern Sie sich, dass Sie über die für eine einwandfreie Bereitstellung benötigten Ressourcen verfügen. Die vollständigen Kontingentinformationen finden Sie unter [Grenzwerte, Kontingente und Einschränkungen für Azure-Abonnements und -Dienste](../azure-subscription-service-limits.md).

Um Ihre Abonnementkontingente für Kerne zu untersuchen, verwenden Sie den Befehl `azure vm list-usage` in der Azure-Befehlszeilenschnittstelle und das Cmdlet **Get-AzureRmVMUsage** in PowerShell. Im Folgenden wird der Befehl in der Azure-CLI gezeigt und veranschaulicht, dass das Kernkontingent für ein kostenloses Testkonto 4 ist:

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

> [AZURE.NOTE] Denken Sie daran, dass für Ressourcengruppen das Kontingent für jede einzelne Region und nicht für das gesamte Abonnement gilt. Wenn Sie 30 Kerne in der Region "USA, Westen" bereitstellen möchten, müssen Sie 30 Ressourcen-Manager-Kerne für "USA, Westen" anfordern. Wenn Sie 30 Kerne in allen Regionen, auf die Sie Zugriff haben, bereitstellen möchten, müssen Sie 30 Ressourcen-Manager-Kerne in allen Regionen anfordern.
<!-- -->
Um genaue Angaben zu Kernen zu machen, können Sie z.B. die Regionen angeben, für die Sie den entsprechenden Kontingentbetrag anfordern sollten, indem Sie den folgenden Befehl verwenden, der für die JSON-Analyse an **jq** weitergereicht wird.
<!-- -->
        azure provider show Microsoft.Compute --json | jq '.resourceTypes[] | select(.name == "virtualMachines") | { name,apiVersions, locations}'
        {
          "name": "virtualMachines",
          "apiVersions": [
            "2015-05-01-preview",
            "2014-12-01-preview"
          ],
          "locations": [
            "East US",
            "West US",
            "West Europe",
            "East Asia",
            "Southeast Asia"
          ]
        }


## Überprüfen der Ressourcenanbieterregistrierung

Ressourcen werden von Ressourcenanbietern verwaltet, und ein Konto oder Abonnement kann für die Verwendung eines bestimmten Anbieters konfiguriert werden. Falls Ihre Aktivierung die Nutzung eines bestimmten Anbieters vorsieht, muss dieser auch für die Nutzung registriert sein. Die meisten Anbieter werden automatisch über das Azure-Portal oder die verwendete Befehlszeilenschnittstelle registriert.

### PowerShell

Verwenden Sie zum Abrufen einer Liste der Ressourcenanbieter und Ihres Registrierungsstatus **Get-AzureProvider** für PowerShell-Versionen vor 1.0.

    PS C:\> Get-AzureProvider

    ProviderNamespace                       RegistrationState                       ResourceTypes
    -----------------                       -----------------                       -------------
    Microsoft.AppService                    Registered                              {apiapps, appIdentities, gateways, d...
    Microsoft.Batch                         Registered                              {batchAccounts}
    microsoft.cache                         Registered                              {Redis, checkNameAvailability, opera...
    ...

Für die Registrierung eines Anbieters verwenden Sie **Register-AzureProvider**.

Verwenden Sie bei Powershell 1.0 **Get-AzureRmResourceProvider**.

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}

Für die Registrierung eines Anbieters verwenden Sie **Register-AzureRmResourceProvider**.

### Azure-Befehlszeilenschnittstelle

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

Um weitere Informationen zu Anbietern (einschließlich regionaler Verfügbarkeit) zu erhalten, geben Sie `azure provider list --json` ein. Durch folgende Vorgehensweise wird nur das erste Element in der Liste zur Ansicht angezeigt:

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

### REST-API

Informationen zum Registrierungsstatus finden Sie unter [Abrufen von Informationen zu einem Ressourcenanbieter](https://msdn.microsoft.com/library/azure/dn790534.aspx).

Informationen zum Registrieren eines Anbieters finden Sie unter [Registrieren eines Abonnements bei einem Ressourcenanbieter](https://msdn.microsoft.com/library/azure/dn790548.aspx).


## Grundlegendes zur erfolgreichen Bereitstellung benutzerdefinierter Vorlagen

Wenn Sie selbst erstellte Vorlagen verwenden, ist es wichtig zu verstehen, dass das Azure-Ressourcen-Manager-System die erfolgreiche Bereitstellung meldet, wenn alle Anbieter erfolgreich von der Bereitstellung zurückgegeben werden. Das bedeutet, dass alle Ihre Vorlagenelemente zur Nutzung bereitgestellt wurden.

Es bedeutet jedoch nicht zwangsläufig, dass Ihre Ressourcengruppe aktiv ist und für Benutzer bereitsteht. Zum Beispiel erfordern die meisten Bereitstellungen, dass die Bereitstellung Upgrades herunterlädt, auf andere Ressourcen ohne Vorlagen wartet oder komplexe Skripte oder andere ausführbare Aktivitäten installiert, die Azure nicht kennt, da es sich um keine Aktivität handelt, die ein Anbieter überwacht. In diesen Fällen kann einige Zeit vergehen, bis Ihre Ressourcen tatsächlich nutzungsbereit sind. Daher sollten Sie davon ausgehen, dass der Bereitstellungsstatus einige Zeit bevor Ihre Bereitstellung genutzt werden kann, erfolgreich ist.

Sie können jedoch Azure daran hindern, einen erfolgreichen Bereitstellungsstatus zu melden, indem Sie ein benutzerdefiniertes Skript für Ihre benutzerdefinierte Vorlage erstellen -- beispielsweise mithilfe der [CustomerScriptExtension](https://azure.microsoft.com/blog/2014/08/20/automate-linux-vm-customization-tasks-using-customscript-extension/) -- das weiß, wie die gesamte Bereitstellung für eine systemweite Einsatzbereitschaft zu überwachen ist und nur erfolgreich zurückkehrt, wenn Benutzer mit der gesamten Bereitstellung interagieren können. Wenn Sie sicherstellen möchten, dass Ihre Erweiterung die letzte ist, die ausgeführt wird, verwenden Sie die **dependsOn**-Eigenschaft in Ihrer Vorlage. Ein Beispiel dazu finden Sie unter [Erstellen einer Vorlagenbereitstellung](https://msdn.microsoft.com/library/azure/dn790564.aspx).

## Nützliche Tools für die Interaktion mit Azure
Wenn Sie mit Ihren Azure-Ressourcen über die Befehlszeile arbeiten, können Sie Tools verwenden, Sie bei Ihrer Arbeit unterstützen. Vorlagen für Azure-Ressourcengruppen sind JSON-Dokumente, und die Azure-Ressourcen-Manager-API akzeptiert JSON und gibt JSON zurück. JSON-Analysetools zählen also zu den ersten Instrumenten, die die Navigation durch Informationen zu Ihren Ressourcen sowie die Gestaltung von und die Interaktion mit Vorlagen und Parameterdateien von Vorlagen erleichtern.

### Mac-, Linux- und Windows-Tools
Wenn Sie die Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows verwenden, sind Sie wahrscheinlich mit den Standarddownloadtools wie **[curl](http://curl.haxx.se/)** und **[wget](https://www.gnu.org/software/wget/)** oder **[Resty](https://github.com/beders/Resty)** sowie mit JSON-Dienstprogrammen wie **[jq](http://stedolan.github.io/jq/download/)**, **[jsawk](https://github.com/micha/jsawk)** und Sprachbibliotheken vertraut, die JSON verarbeiten können. (Viele dieser Tools verfügen auch über Ports für Windows – beispielsweise [wget](http://gnuwin32.sourceforge.net/packages/wget.htm). Tatsächlich gibt es verschiedene Arten, um Linux und andere Open Source-Tools auch unter Windows auszuführen.)

Dieses Thema beinhaltet einige Azure Befehlszeilenschnittstelle-Befehle, die Sie mit **jq** verwenden können, um auf effizientere Weise genau die Informationen zu erhalten, die Sie wünschen. Sie sollten für ein besseres Verständnis Ihres Azure-Ressourceneinsatzes ein Ihnen vertrautes Toolset wählen.

### PowerShell

PowerShell verfügt über mehrere Basisbefehle, um die gleichen Prozeduren auszuführen.

- Verwenden Sie das Cmdlet **[Invole-WebRequest](https://technet.microsoft.com/library/hh849901%28v=wps.640%29)**, um Dateien wie Ressourcengruppen-Vorlagen oder JSON-Parameterdateien herunterzuladen.
- Verwenden Sie das Cmdlet **[ConvertFrom-Json](https://technet.microsoft.com/library/hh849898%28v=wps.640%29.aspx)**, um eine JSON-Zeichenfolge in ein benutzerdefiniertes Objekt zu konvertieren ([PSCustomObject](https://msdn.microsoft.com/library/windows/desktop/system.management.automation.pscustomobject%28v=vs.85%29.aspx)), das für jedes Feld in der JSON-Zeichenfolge eine Eigenschaft hat.


## Nächste Schritte

Informationen zur Vorlagenerstellung finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md) sowie im [Repository mit Azure-Schnellstartvorlagen](https://github.com/Azure/azure-quickstart-templates), das Beispiele für die Bereitstellung enthält. Ein Beispiel für die **dependsOn**-Eigenschaft finden Sie unter [Erstellen eines virtuellen Computers mit mehreren Netzwerkschnittstellenkarten und RDP-Zugriff](https://github.com/Azure/azure-quickstart-templates/tree/master/201-1-vm-loadbalancer-2-nics).

<!--Image references-->

<!--Reference style links - using these makes the source content way more readable than using inline links-->

<!---HONumber=AcomDC_0323_2016-->