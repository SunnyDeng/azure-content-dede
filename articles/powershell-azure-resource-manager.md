<properties 
	pageTitle="Azure PowerShell mit Ressourcen-Manager | Microsoft Azure" 
	description="Einführung in das Verwenden von Azure PowerShell, um mehrere Ressourcen als Ressourcengruppe in Azure bereitzustellen." 
	services="azure-resource-manager" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="azure-resource-manager" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/16/2015" 
	ms.author="tomfitz"/>

# Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)

Mit dem Azure-Ressourcen-Manager wird ein völlig neuer Denkansatz für Azure-Ressourcen eingeführt. Anstatt einzelne Ressourcen zu erstellen und zu verwalten, können Sie sich eine Komplettlösung vorstellen, zum Beispiel einen Blog, eine Fotogalerie, ein SharePoint-Portal oder ein Wiki. Mithilfe eine Vorlage (einer deklarative Darstellung der Lösung) können Sie eine Ressourcengruppe erstellen, die alle Ressourcen enthält, die Sie zum Unterstützen der Lösung brauchen. Anschließend wird diese Ressourcengruppe als logische Einheit verwaltet und bereitgestellt.

In diesem Tutorial erlernen Sie das Verwenden von Azure PowerShell mit dem Azure-Ressourcen-Manager. Sie werden durch den Prozess des Erstellens und Bereitstellens einer Ressourcengruppe für eine unter Azure gehostete Web-App mit einer SQL-Datenbank geleitet, einschließlich aller Ressourcen, die Sie zur Unterstützung benötigen.

## Voraussetzungen

Für dieses Tutorial benötigen Sie Folgendes:

- Ein Azure-Konto
  + Sie können [ein Azure-Konto kostenlos erstellen](/pricing/free-trial/?WT.mc_id=A261C142F): Sie erhalten ein Guthaben, das Sie zum Ausprobieren zahlungspflichtiger Azure-Dienste nutzen können, und Sie können das Konto selbst dann behalten und die kostenlosen Azure-Dienste wie Azure Websites nutzen, wenn das Guthaben aufgebraucht ist. Ihre Kreditkarte wird nur dann belastet, wenn Sie Ihre Einstellungen explizit ändern und mit einer Zahlung einverstanden sind.
  
  + Sie können Ihre [Vorteile für MSDN-Abonnenten aktivieren](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F): Ihr MSDN-Abonnement enthält ein monatliches Guthaben, das Sie für zahlungspflichtige Azure-Dienste nutzen können.
- Azure PowerShell

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

Dieses Lernprogramm richtet sich an PowerShell-Anfänger. Es wird aber vorausgesetzt, dass Sie die grundlegenden Konzepte verstehen, z. B. Module, Cmdlets und Sitzungen. Weitere Informationen zu Windows PowerShell finden Sie unter [Erste Schritte mit Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

## Was Sie bereitstellen

In diesem Tutorial verwenden Sie Azure PowerShell zur Bereitstellung einer Web-App und SQL-Datenbank. Diese Lösung aus Web-App und SQL-Datenbank besteht jedoch aus verschiedenen Ressourcentypen, die zusammenarbeiten. Es folgen die tatsächlichen Ressourcen, die Sie bereitstellen werden:

- Instanz von SQL Server zum Hosten der Datenbank
- SQL-Datenbank zum Speichern der Daten
- Firewallregeln, um der Web-App das Verbinden mit der Datenbank zu ermöglichen
- App Service-Plan zum Bestimmen der Funktionen und Kosten der Web-App
- Website zum Ausführen der Web-App
- Webkonfiguration zum Speichern der Verbindungszeichenfolge mit der Datenbank 

## Hilfe zu Cmdlets

Um detaillierte Hilfe zu einem Cmdlet aus dem Tutorial zu erhalten, verwenden Sie das Get-Help-Cmdlet.

	Get-Help <cmdlet-name> -Detailed

Geben Sie beispielsweise Folgendes ein, um Hilfe zum Cmdlet „Get-AzureRmResource“ zu erhalten:

	Get-Help Get-AzureRmResource -Detailed

Geben Sie Folgendes ein, um eine Liste der Cmdlets im Modul „Ressourcen“ mit einer Hilfezusammenfassung zu erhalten:

    PS C:\> Get-Command -Module AzureRM.Resources | Get-Help | Format-Table Name, Synopsis

Die Ausgabe sieht etwa wie der folgende Auszug aus:

	Name                                   Synopsis
	----                                   --------
	Find-AzureRmResource                   Searches for resources using the specified parameters.
	Find-AzureRmResourceGroup              Searches for resource group using the specified parameters.
	Get-AzureRmADGroup                     Filters active directory groups.
	Get-AzureRmADGroupMember               Get a group members.
	...

Um die vollständige Hilfe zu einem Cmdlet zu erhalten, geben Sie einen Befehl im folgenden Format ein:

	Get-Help <cmdlet-name> -Full
  
## Anmelden bei Ihrem Azure-Konto

Bevor Sie an Ihrer Lösung arbeiten, müssen Sie sich bei Ihrem Konto anmelden.

Um sich bei Ihrem Azure-Konto anzumelden, verwenden Sie das Cmdlet **Login-AzureRmAccount**. Verwenden Sie in Azure PowerShell-Versionen vor 1.0 Preview den Befehl **Add-AzureAccount**.

    PS C:\> Login-AzureRmAccount

Sie werden vom Cmdlet zur Eingabe der Anmeldeinformationen für Ihr Azure-Konto aufgefordert. Nach dem Anmelden werden Ihre Kontoeinstellungen heruntergeladen, damit sie Azure PowerShell zur Verfügung stehen.

Die Kontoeinstellungen laufen ab. Sie müssen sie also ab und zu aktualisieren. Um die Kontoeinstellungen zu aktualisieren, führen Sie **Login-AzureRmAccount** erneut aus.

>[AZURE.NOTE]Die Ressourcen-Manager-Module erfordert „Login-AzureRmAccount“. Eine Datei mit Veröffentlichungseinstellungen ist nicht ausreichend.

## Abrufen der Speicherorte für Ressourcentypen

Wenn Sie eine Ressource bereitstellen, müssen Sie angeben, wo die Ressource gehostet werden soll. Nicht jede Region unterstützt alle Ressourcentypen. Vor der Bereitstellung Ihrer Web-App und SQL-Datenbank müssen Sie herausfinden, welche Regionen diese Typen unterstützen. Eine Ressourcengruppe kann Ressourcen enthalten, die sich in unterschiedlichen Regionen befinden. Allerdings sollten Sie nach Möglichkeit zum Optimieren der Leistung Ressourcen am gleichen Ort erstellen. Achten Sie besonders darauf, dass sich Ihre Datenbank an demselben Ort wie die App befindet, mit der darauf zugegriffen wird.

Um die Standorte abzurufen, die die einzelnen Ressourcentypen unterstützen, verwenden Sie das Cmdlet **Get-AzureRmResourceProvider**. Lassen Sie uns zunächst prüfen, was dieser Befehl zurückgibt:

    PS C:\> Get-AzureRmResourceProvider -ListAvailable

    ProviderNamespace               RegistrationState ResourceTypes
    -----------------               ----------------- -------------
    Microsoft.ApiManagement         Unregistered      {service, validateServiceName, checkServiceNameAvailability}
    Microsoft.AppService            Registered        {apiapps, appIdentities, gateways, deploymenttemplates...}
    Microsoft.Batch                 Registered        {batchAccounts}
    ...

„ProviderNamespace“ stellt eine Auflistung verwandter Ressourcentypen dar. Diese Namespaces passen in der Regel gut zu den Diensten, die Sie in Azure erstellen möchten. Wenn Sie einen Ressourcenanbieter verwenden möchten, der als **Nicht registriert** aufgeführt ist, können Sie diesen Ressourcenanbieter registrieren, indem Sie das Cmdlet **Register-AzureRmResourceProvider** ausführen und den zu registrierenden Anbieternamespace angeben. In den meisten Fällen ist der Ressourcenanbieter, den Sie in diesem Tutorial verwenden, bereits für Ihr Abonnement registriert.

Sie können weitere Details zu einem Anbieter abrufen, indem Sie diesen Namespace angeben:

    PS C:\> Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql

    ProviderNamespace RegistrationState ResourceTypes                                 Locations
    ----------------- ----------------- -------------                                 ---------
    Microsoft.Sql     Registered        {operations}                                  {East US 2, South Central US, Cent...
    Microsoft.Sql     Registered        {locations}                                   {East US 2, South Central US, Cent...
    Microsoft.Sql     Registered        {locations/capabilities}                      {East US 2, South Central US, Cent...
    ...

Um die Ausgabe auf die unterstützten Standorte für einen bestimmten Typ von Ressource, wie z. B. Websites, zu beschränken, geben Sie Folgendes an:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).Locations
    
Die Ausgabe ähnelt der folgenden:

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
    Central US
    East US 2

Die Standorte, die Sie sehen, sind möglicherweise etwas anders als die vorherigen Ergebnisse. Die Ergebnisse können unterschiedlich sein, da ein Administrator in Ihrer Organisation eine Richtlinie erstellt hat, die begrenzt, welche Regionen in Ihrem Abonnement genutzt werden dürfen. Oder es gelten Einschränkungen im Zusammenhang mit der Steuerpolitik in Ihrem Heimatland.

Lassen Sie uns den gleichen Befehl für die Datenbank ausführen:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql).ResourceTypes | Where-Object ResourceTypeName -eq servers).Locations
    East US 2
    South Central US
    Central US
    North Central US
    West US
    East US
    East Asia
    Southeast Asia
    Japan West
    Japan East
    North Europe
    West Europe
    Brazil South

Es sieht aus, als stünden diese Ressourcen in vielen Regionen zur Verfügung. Zu diesem Thema verwenden wir **USA, Westen**, doch Sie können eine der unterstützten Regionen angeben.

## Erstellen einer Ressourcengruppe

Dieser Abschnitt des Tutorials leitet Sie durch den Prozess des Erstellens einer Ressourcengruppe. Die Ressourcengruppe dient als Container für alle Ressourcen in Ihrer Lösung, die den gleichen Lebenszyklus haben. Später in diesem Tutorial werden Sie die Web-App und SQL-Datenbank in dieser Ressourcengruppe bereitstellen.

Um eine Ressourcengruppe zu erstellen, verwenden Sie das Cmdlet **New-AzureRmResourceGroup**.

Der Befehl verwendet den **Name**-Parameter, um einen Namen für die Ressourcengruppe anzugeben, und den **Location**-Parameter, um ihren Speicherort anzugeben. Basierend auf den Angaben im vorherigen Abschnitt nutzen wir als Standort „USA, Westen“.

    PS C:\> New-AzureRmResourceGroup -Name TestRG1 -Location "West US"
    
    ResourceGroupName : TestRG1
    Location          : westus
    ProvisioningState : Succeeded
    Tags              :
    Permissions       :
                    Actions  NotActions
                    =======  ==========
                    *

    ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1

Ihre Ressourcengruppe wurde erfolgreich erstellt.


## Abrufen verfügbarer API-Versionen für die Ressourcen

Beim Bereitstellen einer Vorlage müssen Sie eine API-Version angeben, die zum Erstellen der Ressource verwendet werden soll. Die verfügbaren API-Versionen entsprechen Version von REST-API-Vorgängen, die vom Ressourcenanbieter freigegeben werden. Sobald Ressourcenanbieter neue Features aktivieren, werden neue Versionen der REST-API veröffentlicht. Deshalb wirkt sich die Version der API, die Sie in Ihrer Vorlage angeben, darauf aus, welche Eigenschaften Ihnen beim Erstellen der Vorlage zur Verfügung stehen. Im Allgemeinen sollten Sie die neueste Version der API wählen, wenn Sie neue Vorlagen erstellen. Für vorhandene Vorlagen können Sie entscheiden, ob Sie weiterhin eine API-Version verwenden möchten, von der Sie wissen, dass Sie Ihre Bereitstellung nicht ändert, oder ob Sie Ihre Vorlage auf die neueste Version aktualisieren möchten, um in den Genuss neuer Features zu kommen.

Dieser Schritt mag verwirrend erscheinen, doch das Ermitteln der API-Versionen, die für Ihre Ressource verfügbar sind, ist nicht schwierig. Sie verwenden hierzu erneut den Befehl **Get-AzureRmResourceProvider**.

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Web).ResourceTypes | Where-Object ResourceTypeName -eq sites).ApiVersions
    2015-08-01
    2015-07-01
    2015-06-01
    2015-05-01
    2015-04-01
    2015-02-01
    2014-11-01
    2014-06-01
    2014-04-01-preview
    2014-04-01

Wie Sie sehen können, wurde diese API häufig aktualisiert. In der Regel sind die gleichen API-Versionsnummern für alle Ressourcen in einem Ressourcenanbieter verfügbar. Die einzige Ausnahme ist, wenn eine Ressource hinzugefügt oder irgendwann entfernt wurde. Es wird davon ausgegangen, dass die gleichen API-Versionen für die Ressource „serverFarms“ verfügbar sind. Sie können jedoch für jede Ressource eine weitere Überprüfung vornehmen, von der Sie meinen, dass es eine andere Liste verfügbarer API-Versionen gibt.

Für die Datenbank wird Folgendes angezeigt:

    PS C:\> ((Get-AzureRmResourceProvider -ProviderNamespace Microsoft.Sql).ResourceTypes | Where-Object ResourceTypeName -eq servers/databases).ApiVersions
    2014-04-01-preview
    2014-04-01 

## Erstellen der Vorlage

In diesem Thema wird nicht gezeigt, wie Sie eine Vorlage erstellen, und nicht die Struktur der Vorlage erläutert. Weitere Informationen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](resource-group-authoring-templates.md). Nachstehend finden Sie die Vorlage, die Sie bereitstellen werden. Beachten Sie, dass die Vorlage die API-Versionen verwendet, die Sie im vorherigen Abschnitt abgerufen haben. Um sicherzustellen, dass sich alle Ressourcen in der gleichen Region befinden, verwenden wir den Vorlagenausdruck **resourceGroup().location**, um den Standort der Ressourcengruppe zu nutzen.

Beachten Sie auch den Abschnitt für Parameter. Dieser Abschnitt definiert Werte, die Sie angeben können, wenn die Ressourcen bereitgestellt werden. Sie verwenden diese Werte später im Tutorial.

Sie können die Vorlage kopieren und als JSON-Datei lokal speichern. In diesem Tutorial wird davon ausgegangen, dass sie unter „C:\\Azure\\Templates\\azuredeploy.json“ gespeichert wurde. Doch Sie können sie an einen beliebigen Ort und mit dem Namen speichern, der für Ihre Anforderungen geeignet ist.

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "hostingPlanName": {
                "type": "string"
            },
            "serverName": {
                "type": "string"
            },
            "databaseName": {
                "type": "string"
            },
            "administratorLogin": {
                "type": "string"
            },
            "administratorLoginPassword": {
                "type": "securestring"
            }
        },
        "variables": {
            "siteName": "[concat('ExampleSite', uniqueString(resourceGroup().id))]"
        },
        "resources": [
            {
                "name": "[parameters('serverName')]",
                "type": "Microsoft.Sql/servers",
                "location": "[resourceGroup().location]",
                "apiVersion": "2014-04-01",
                "properties": {
                    "administratorLogin": "[parameters('administratorLogin')]",
                    "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
                    "version": "12.0"
                },
                "resources": [
                    {
                        "name": "[parameters('databaseName')]",
                        "type": "databases",
                        "location": "[resourceGroup().location]",
                        "apiVersion": "2014-04-01",
                        "dependsOn": [
                            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                        ],
                        "properties": {
                            "edition": "Basic",
                            "collation": "SQL_Latin1_General_CP1_CI_AS",
                            "maxSizeBytes": "1073741824",
                            "requestedServiceObjectiveName": "Basic"
                        }
                    },
                    {
                        "name": "AllowAllWindowsAzureIps",
                        "type": "firewallrules",
                        "location": "[resourceGroup().location]",
                        "apiVersion": "2014-04-01",
                        "dependsOn": [
                            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                        ],
                        "properties": {
                            "endIpAddress": "0.0.0.0",
                            "startIpAddress": "0.0.0.0"
                        }
                    }
                ]
            },
            {
                "apiVersion": "2015-08-01",
                "type": "Microsoft.Web/serverfarms",
                "name": "[parameters('hostingPlanName')]",
                "location": "[resourceGroup().location]",
                "sku": {
                    "tier": "Free",
                    "name": "f1",
                    "capacity": 0
                },
                "properties": {
                    "numberOfWorkers": 1
                }
            },
            {
                "apiVersion": "2015-08-01",
                "name": "[variables('siteName')]",
                "type": "Microsoft.Web/sites",
                "location": "[resourceGroup().location]",
                "dependsOn": [
                    "[concat('Microsoft.Web/serverFarms/', parameters('hostingPlanName'))]"
                ],
                "properties": {
                    "serverFarmId": "[parameters('hostingPlanName')]"
                },
                "resources": [
                    {
                        "name": "web",
                        "type": "config",
                        "apiVersion": "2015-08-01",
                        "dependsOn": [
                            "[concat('Microsoft.Web/Sites/', variables('siteName'))]"
                        ],
                        "properties": {
                            "connectionStrings": [
                                {
                                    "ConnectionString": "[concat('Data Source=tcp:', reference(concat('Microsoft.Sql/servers/', parameters('serverName'))).fullyQualifiedDomainName, ',1433;Initial Catalog=', parameters('databaseName'), ';User Id=', parameters('administratorLogin'), '@', parameters('serverName'), ';Password=', parameters('administratorLoginPassword'), ';')]",
                                    "Name": "DefaultConnection",
                                    "Type": 2
                                }
                            ]
                        }
                    }
                ]
            }
        ]
    }


## Bereitstellen der Vorlage

Sie haben Ihre Ressourcengruppe und Ihre Vorlage, weshalb Sie bereit sind, die in Ihrer Vorlage definierte Infrastruktur in der Ressourcengruppe bereitzustellen. Sie stellen Ressourcen mit dem Cmdlet **New-AzureRmResourceGroupDeployment** bereit. Die grundlegende Syntax sieht wie folgt aus:

    PS C:\> New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -TemplateFile c:\Azure\Templates\azuredeploy.json

Sie geben die Ressourcengruppe und den Speicherort der Vorlage an. Wenn Ihre Vorlage nicht lokal ist, können Sie mithilfe des „TemplateUri“-Parameters einen URI für die Vorlage angeben.

###Dynamische Vorlagenparameter

Wenn Sie mit PowerShell vertraut sind, wissen Sie, dass Sie die für ein Cmdlet verfügbaren Parameter durchlaufen können, indem Sie ein Minuszeichen (-) eingeben und die TAB-TASTE drücken. Dieselbe Funktionalität gilt auch für Parameter, die Sie in der Vorlage definieren. Sobald Sie den Vorlagennamen eingeben, ruft das Cmdlet die Vorlage ab, analysiert sie und fügt die Vorlagenparameter dynamisch zum Befehl hinzu. Auf diese Weise wird das Angeben der Vorlagenparameterwerte stark vereinfacht. Wenn Sie einen erforderlichen Parameterwert vergessen haben, fordert PowerShell Sie zur Angabe des Werts auf.

Es folgt der vollständige Befehl mit Parametern. Sie können Ihre eigenen Werte für die Namen der Ressourcen angeben.

    PS C:\> New-AzureRmResourceGroupDeployment -ResourceGroupName TestRG1 -TemplateFile c:\Azure\Templates\azuredeploy.json -hostingPlanName freeplanwest -serverName exampleserver -databaseName exampledata -administratorLogin exampleadmin

Wenn Sie den Befehl eingeben, werden Sie zur Angabe des fehlenden obligatorischen **administratorLoginPassword**-Parameters aufgefordert. Wenn Sie das Kennwort eingeben, wird der Wert der sicheren Zeichenfolge ausgeblendet. Durch diese Strategie wird das Risiko der Angabe eines Kennworts in einfachem Text vermieden.

    cmdlet New-AzureRmResourceGroupDeployment at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: ********

Der Befehl wird ausgeführt und gibt Meldungen zurück, während die Ressourcen erstellt werden. Schließlich wird das Ergebnis der Bereitstellung angezeigt.

    DeploymentName    : azuredeploy
    ResourceGroupName : TestRG1
    ProvisioningState : Succeeded
    Timestamp         : 10/16/2015 12:55:50 AM
    Mode              : Incremental
    TemplateLink      :
    Parameters        :
                    Name             Type                       Value
                    ===============  =========================  ==========
                    hostingPlanName  String                     freeplanwest
                    serverName       String                     exampleserver
                    databaseName     String                     exampledata
                    administratorLogin  String                  exampleadmin
                    administratorLoginPassword  SecureString

    Outputs           :

In wenigen Schritten haben Sie die Ressourcen, die für eine komplexe Website erforderlich sind, erstellt und bereitgestellt.

## Abrufen von Informationen zu Ihren Ressourcengruppen

Nach dem Erstellen einer Ressourcengruppe können Sie die Cmdlets im Ressourcen-Manager-Modul verwenden, um Ihre Ressourcengruppen zu verwalten.

- Um alle Ressourcengruppen in Ihrem Abonnement abzurufen, verwenden Sie das Cmdlet **Get-AzureRmResourceGroup**:

		PS C:\>Get-AzureRmResourceGroup

		ResourceGroupName : TestRG
		Location          : westus
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG
		
		...

- Um die Ressourcen in der Ressourcengruppe abzurufen, verwenden Sie das Cmdlet **Get-AzureRmResource** und den zugehörigen „ResourceGroupName“-Parameter. Ohne Parameter ruft „Get-AzureRmResource“ alle Ressourcen in Ihrem Azure-Abonnement ab.

		PS C:\> Get-AzureRmResource -ResourceGroupName TestRG1
		
		Name              : exampleserver
                ResourceId        : /subscriptions/{guid}/resourceGroups/TestRG1/providers/Microsoft.Sql/servers/tfserver10
                ResourceName      : exampleserver
                ResourceType      : Microsoft.Sql/servers
                Kind              : v12.0
                ResourceGroupName : TestRG1
                Location          : westus
                SubscriptionId    : {guid}
                
                ...
	        

## Hinzufügen zu einer Ressourcengruppe

Um eine Ressource zur Ressourcengruppe hinzuzufügen, verwenden Sie das Cmdlet **New-AzureRmResource**. Das Hinzufügen einer Ressource auf diese Weise kann jedoch künftig für Verwirrung sorgen, da die neue Ressource nicht in Ihrer Vorlage vorhanden ist. Wenn Sie die alte Vorlage erneut bereitgestellt hätten, würden Sie eine unvollständige Lösung bereitstellen. Wenn Sie häufig bereitstellen, ist es einfacher und zuverlässiger, die neue Ressource Ihrer Vorlage hinzuzufügen und Sie erneut bereitzustellen.

## Verschieben einer Ressource

Sie können vorhandene Ressourcen in eine neue Ressourcengruppe verschieben. Beispiele finden Sie unter [Verschieben von Ressourcen in eine neue Ressourcengruppe oder ein neues Abonnement](resource-group-move-resources.md).

## Löschen einer Ressourcengruppe

- Um eine Ressource aus der Ressourcengruppe zu löschen, verwenden Sie das Cmdlet **Remove-AzureRmResource**. Dieses Cmdlet löscht die Ressource, aber nicht die Ressourcengruppe.

	Dieser Befehl entfernt die Website „TestSite“ aus der Ressourcengruppe „TestRG“.

		Remove-AzureRmResource -Name TestSite -ResourceGroupName TestRG1 -ResourceType "Microsoft.Web/sites" -ApiVersion 2015-08-01

- Um eine Ressourcengruppe zu löschen, verwenden Sie das Cmdlet **Remove-AzureRmResourceGroup**. Dieses Cmdlet löscht die Ressourcengruppe und ihre Ressourcen.

		PS C:\> Remove-AzureRmResourceGroup -Name TestRG1
		
		Confirm
		Are you sure you want to remove resource group 'TestRG1'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y



## Nächste Schritte

- Weitere Informationen zum Erstellen von Ressourcen-Manager-Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](./resource-group-authoring-templates.md).
- Informationen zum Bereitstellen von Vorlagen finden Sie unter [Bereitstellen einer Anwendung mit einer Azure-Ressourcen-Manager-Vorlage](./resource-group-template-deploy.md).
- Ein ausführliches Beispiel für das Bereitstellen eines Projekts finden Sie unter [Vorhersagbares Bereitstellen von Microservices in Azure](app-service-web/app-service-deploy-complex-application-predictably.md).
- Weitere Informationen zur Problembehandlung bei einer nicht erfolgreichen Bereitstellung finden Sie unter [Problembehandlung bei Bereitstellungen von Ressourcengruppen in Azure](./virtual-machines/resource-group-deploy-debug.md).

<!---HONumber=Oct15_HO4-->