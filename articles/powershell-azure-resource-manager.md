<properties 
	pageTitle="Verwenden von Windows PowerShell mit dem Ressourcen-Manager" 
	description="Verwenden von Windows PowerShell zum Erstellen einer Ressourcengruppe" 
	services="" 
	documentationCenter="" 
	authors="sdkaczmarek" 
	manager="stevenka" 
	editor="mollybos"/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="12/02/2014" 
	ms.author="stevenka"/>

# Verwenden von Windows PowerShell mit dem Ressourcen-Manager

<div class="dev-center-tutorial-selector sublanding"><a href="/de-de/documentation/articles/powershell-azure-resource-manager.md" title="Windows PowerShell" class="current">Windows PowerShell</a><a href="/de-de/documentation/articles/xplat-cli-azure-resource-manager.md" title="Plattform&uuml;bergreifende Befehlszeilenschnittstelle">Plattform&uuml;bergreifende Befehlszeilenschnittstelle</a></div>

Der Ressourcen-Manager führt eine ganz neue Denkweise zu Azure-Ressourcen ein. Anstatt einzelne Ressourcen zu erstellen und zu verwalten, können Sie sich einen komplexen Dienst vorstellen, zum Beispiel einen Blog, eine Fotogalerie, ein SharePointPortal oder eine Wiki. Sie verwenden eine Vorlage – ein Ressourcenmodell des Dienstes – um eine Ressourcengruppe mit den Ressourcen zu erstellen, die Sie zur Unterstützung des Dienstes benötigen. Dann können Sie diese Ressourcengruppe als logische Einheit verwalten und bereitstellen.

In diesem Lernprogramm erlernen Sie die Verwendung von Windows PowerShell mit dem Ressourcen-Manager für Microsoft Azure. Sie werden durch den Prozess des Erstellens und Bereitstellens einer Ressourcengruppe für eine auf Azure gehostete Website (oder Webanwendung) mit einer SQL-Datenbank geleitet, einschließlich aller Ressourcen, die Sie zur Unterstützung benötigen.

**Geschätzter Zeitaufwand:** 15 Minuten

## Voraussetzungen

Bevor Sie Windows PowerShell mit dem Ressourcen-Manager verwenden können, benötigen Sie Folgendes:

-   Windows PowerShell, Version 3.0 oder 4.0. Zur Ermittlung der Version von Windows PowerShell geben Sie `$PSVersionTable` ein. Stellen Sie sicher, dass der Wert von `PSVersion` 3.0 oder 4.0 lautet. Informationen zum Installieren einer kompatiblen Version finden Sie unter [Windows Management Framework 3.0][Windows Management Framework 3.0] oder [Windows Management Framework 4.0][Windows Management Framework 4.0].

-   Azure PowerShell, Version 0.8.0 oder neuer. Informationen zum Installieren der aktuellen Version und zum Verknüpfen mit dem Azure-Abonnement finden Sie unter [Installieren und Konfigurieren von Windows Azure PowerShell][Installieren und Konfigurieren von Windows Azure PowerShell].

Dieses Lernprogramm richtet sich an Windows PowerShell-Anfänger. Es wird aber vorausgesetzt, dass Sie die grundlegenden Konzepte, wie zum Beispiel Module, Cmdlets und Sitzungen, verstehen. Weitere Informationen zu Windows PowerShell finden Sie unter [Erste Schritte mit Windows PowerShell][Erste Schritte mit Windows PowerShell].

Um detaillierte Hilfe zu einem Cmdlet aus dem Lernprogramm zu erhalten, verwenden Sie das Get-Help-Cmdlet.

    Get-Help <cmdlet-name> -Detailed

Geben Sie beispielsweise Folgendes ein, um Hilde zum Add-AzureAccount-Cmdlet zu erhalten:

    Get-Help Add-AzureAccount -Detailed

## Dieses Lernprogramm umfasst folgende Punkte

-   [Info zu Azure PowerShell-Modulen][Info zu Azure PowerShell-Modulen]
-   [Erstellen einer Ressourcengruppe][Erstellen einer Ressourcengruppe]
-   [Verwalten einer Ressourcengruppe][Verwalten einer Ressourcengruppe]
-   [Problembehandlung für eine Ressourcengruppe][Problembehandlung für eine Ressourcengruppe]
-   [Nächste Schritte][Nächste Schritte]

## <span id="about"></span></a>Info zu Azure PowerShell-Modulen

Ab Version 0.8.0 umfasst die Azure PowerShell-Installation drei Windows PowerShell-Module:

-   **Azure**: Enthält die herkömmlichen Cmdlets zum Verwalten einzelner Ressourcen, zum Beispiel Speicherkonten, Websites, Datenbanken, virtuelle Computer und Media Services. Weitere Informationen finden Sie unter [Azure Service Management Cmdlets][Azure Service Management Cmdlets].

-   **AzureResourceManager**: Enthält Cmdlets für das Erstellen, Verwalten und Bereitstellen der Azure-Ressourcen für eine Ressourcengruppe. Weitere Informationen finden Sie unter [Azure Resource Manager Cmdlets][Azure Resource Manager Cmdlets].

-   **AzureProfile**: Enthält Cmdlets aus beiden Modulen, zum Beispiel Add-AzureAccount, Get-AzureSubscription und Switch-AzureMode. Weitere Informationen finden Sie unter [Azure Profile Cmdlets][Azure Profile Cmdlets].

> [ WACOM.NOTE] Das Azure Resource Manager-Modul befindet sich derzeit in der Vorschau. Möglicherweise stellt es nicht die gleichen Verwaltungsfunktionen wie das Azure-Modul bereit.

Die Azure- und Azure Resource Manager-Module wurden nicht für die Verwendung in der gleichen Windows PowerShell-Sitzung entwickelt. Um das Wechseln zwischen ihnen zu vereinfachen, haben wir dem Azure Profile-Modul das neue Cmdlet **Switch-AzureMode** hinzugefügt.

Wenn Sie Azure PowerShell verwenden, werden die Cmdlets im Azure-Module standardmäßig importiert. Um zum Azure Resource Manager-Modul zu wechseln, verwenden Sie das Switch-AzureMode-Cmdlet. Es entfernt das Azure-Modul aus der Sitzung und importiert das Azure Resource Manager- und das Azure Profile-Modul.

Geben Sie Folgendes ein, um zum AzureResoureManager-Modul zu wechseln:

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Geben Sie Folgendes ein, um zurück zum Azure-Modul zu wechseln:

    PS C:\> Switch-AzureMode -Name AzureServiceManagement

Standardmäßig wirkt sich Switch-AzureMode nur auf die aktuelle Sitzung aus. Damit der Wechsel in allen Windows PowerShell-Sitzungen funktioniert, verwenden Sie den **Global**-Parameter von Switch-AzureMode.

Geben Sie Folgendes ein, um Hilfe zum Switch-AzureMode-Cmdlet zu erhalten: `Get-Help Switch-AzureMode` oder lesen Sie die Informationen unter [Switch-AzureMode][Switch-AzureMode].

Geben Sie Folgendes ein, um eine Liste der Cmdlets im AzureResourceManager-Modul mit einer Hilfezusammenfassung zu erhalten:

    PS C:\> Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis

    Name                                   Synopsis
    ----                                   --------
    Get-AzureLocation                      Gets the Azure data center locations and the resource types that they support
    Get-AzureResource                      Gets Azure resources
    Get-AzureResourceGroup                 Gets Azure resource groups
    Get-AzureResourceGroupDeployment       Gets the deployments in a resource group.
    Get-AzureResourceGroupGalleryTemplate  Gets resource group templates in the gallery
    Get-AzureResourceGroupLog              Gets the deployment log for a resource group
    New-AzureResource                      Creates a new resource in a resource group
    New-AzureResourceGroup                 Creates an Azure resource group and its resources
    New-AzureResourceGroupDeployment       Add an Azure deployment to a resource group.
    Remove-AzureResource                   Deletes a resource
    Remove-AzureResourceGroup              Deletes a resource group.
    Save-AzureResourceGroupGalleryTemplate Saves a gallery template to a JSON file
    Set-AzureResource                      Changes the properties of an Azure resource.
    Stop-AzureResourceGroupDeployment      Cancels a resource group deployment
    Test-AzureResourceGroupTemplate        Detects errors in a resource group template or template parameters

Um die vollständige Hilfe zu einem Cmdlet zu erhalten, geben Sie einen Befehl im folgenden Format ein:

    Get-Help <cmdlet-name> -Full

Beispiel:

    Get-Help Get-AzureLocation -Full

# <span id="create"></span></a> Erstellen einer Ressourcengruppe

Dieser Abschnitt des Lernprogramms leitet Sie durch den Prozess des Erstellens und Bereitstellens einer Ressourcengruppe für eine Website mit einer SQL-Datenbank.

Für diese Aufgabe müssen Sie kein Experte für Azure, SQL, Websites oder Ressourcenverwaltung sein. Die Vorlagen stellen ein Modell der Ressourcengruppe mit allen Ressourcen bereit, die Sie wahrscheinlich benötigen. Und da Windows PowerShell zum Automatisieren der Aufgaben verwendet wird, können Sie diesen Prozess als Modell für die Erstellung von Skripts für umfangreiche Aufgaben verwenden.

## Schritt 1: Wechseln zu Azure Resource Manager

1.  Starten Sie Windows PowerShell. Sie können ein beliebiges Hostprogramm verwenden, zum Beispiel die Windows PowerShell-Konsole oder Windows PowerShell ISE.

2.  Verwenden Sie das **Switch-AzureMode**-Cmdlet, um die Cmdlets in die AzureResourceManager- und AzureProfile-Module zu importieren.

	`PS C:\>Switch-AzureMode AzureResourceManager`

3.  Um der Windows PowerShell-Sitzung Ihr Azure-Konto hinzuzufügen, verwenden Sie das **Add-AzureAccount**-Cmdlet.

    `PS C:\> Add-AzureAccount`

Das Cmdlet fordert Sie zur Eingabe einer E-Mail-Adresse und eines Kennworts auf. Dann lädt es Ihre Kontoeinstellungen herunter, damit sie für Windows PowerShell zu Verfügung stehen.

Die Kontoeinstellungen laufen ab. Sie müssen sie also ab und zu aktualisieren. Um die Kontoeinstellungen zu aktualisieren, führen Sie **Add-AzureAccount** erneut aus.

> [ WACOM.NOTE] Das AzureResourceManager-Modul erfordert Add-AzureAccount. Eine Datei mit Veröffentlichungseinstellungen ist nicht ausreichend.

## Schritt 2: Auswählen einer Galerievorlage

Es gibt mehrere Methoden zum Erstellen einer Ressourcengruppe samt Ressourcen. Die einfachste besteht aber im Verwenden einer Ressourcengruppenvorlage. Eine *Ressourcengruppenvorlage* ist eine JSON-Zeichenfolge, die die Ressource in einer Ressourcengruppe definiert. Die Zeichenfolge umfasst Platzhalter, die als "Parameter" bezeichnet werden, für benutzerdefinierte Werte, wie zum Beispiel Namen und Größen.

Azure enthält eine Galerie mit Ressourcengruppenvorlagen. Außerdem können Sie eigene Vorlagen von Grund auf oder durch Bearbeiten einer Galerievorlage erstellen. In diesem Lernprogramm wird eine Galerievorlage verwendet.

Um eine Vorlage in der Azure-Ressourcengruppen-Vorlagengalerie zu suchen, verwenden Sie das **Get-AzureResourceGroupGalleryTemplate**-Cmdlet.

Geben Sie an der Windows Powershell-Eingabeaufforderung Folgendes ein:

    PS C:\> Get-AzureResourceGroupGalleryTemplate

Das Cmdlet gibt eine Liste der Galerievorlagen mit Herausgeber- und Identitätseigenschaften zurück. Sie verwenden Sie **Identity**-Eigenschaft, um die Vorlage in den Befehlen zu identifizieren.

    Publisher       Identity
    ---------       --------
    Ghost           Ghost.Ghost.0.1.0-preview1
    Joomla          Joomla.Joomla.0.1.0-preview1
    Microsoft       Microsoft.ASPNETEmptySite.0.1.0-preview1
    Microsoft       Microsoft.ASPNETstarterSite.0.1.0-preview1
    Microsoft       Microsoft.Bakery.0.1.0-preview1
    Microsoft       Microsoft.Boilerplate.0.1.0-preview1
    ...

Tipp: Drücken Sie die NACH-OBEN-TASTE, um den letzten Befehl erneut aufzurufen.

Die Vorlage "Microsoft.WebSiteSQLDatabase.0.1.0-preview1" sieht interessant aus. Um weitere Informationen zu einer Galerievorlage zu erhalten, verwenden Sie den **Identity**-Parameter. Der Wert des Identity-Parameters ist die Identität der Vorlage.

    PS C:\> Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.1.0-preview1

Das Cmdlet gibt ein Objekt mit viel mehr Informationen zur Vorlage zurück, unter anderem eine Beschreibung.

    <p>Windows Azure Websites offers secure and flexible development, 
    deployment and scaling options for any sized web application. Leverage 
    your existing tools to create and deploy applications without the hassle 
    of managing infrastructure.</p>

Diese Vorlage scheint für uns geeignet zu sein. Speichern Sie sie auf der Festplatte und sehen Sie sie sich näher an.

## Schritt 3: Untersuchen der Vorlage

Speichern Sie die Vorlage in einer JSON-Datei auf der Festplatte. Dieser Schritt ist nicht erforderlich, erleichtert aber das Anzeigen der Vorlage. Um die Vorlage zu speichern, verwenden Sie das **Save-AzureResourceGroupGalleryTemplate**-Cmdlet. Verwenden Sie den **Identity**-Parameter, um die Vorlage anzugeben, und den **Path**-Parameter, um einen Pfad auf der Festplatte anzugeben.

"Save-AzureResourceGroupGalleryTemplate" speichert die Vorlage und gibt den Pfad und den Dateinamen der JSON-Vorlagendatei zurück.

    PS C:\> Save-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 -Path D:\Azure\Templates

    Path
    ----
    D:\Azure\Templates\Microsoft.WebSite.0.1.0-preview1.json

Sie können die Vorlagendatei in einem Texteditor anzeigen, zum Beispiel in Editor. Jede Vorlage hatte einen Abschnitt **resources** und einen Abschnitt **parameters**.

Im Abschnitt **resources** der Vorlagen werden die Ressourcen aufgelistet, die von der Vorlage erstellt werden. Diese Vorlage erstellt einen SQL-Datenbankserver und eine SQL-Datenbank, eine Serverfarm und eine Website sowie mehrere Verwaltungseinstellungen.

Die Definition der einzelnen Ressource umfasst ihre Eigenschaften, zum Beispiel Name, Typ und Speicherort sowie Parameter für benutzerdefinierte Werte. In diesem Abschnitt der Vorlage wird beispielsweise die SQL-Datenbank definiert. Er enthält Parameter für den Datenbanknamen ([parameters('databaseName')]), den Speicherort des Datenbankservers [parameters('serverLocation')] und die Sortierungseigenschaft [parameters('collation')].

        {
          "name": "[parameters('databaseName')]",
          "type": "databases",
          "location": "[parameters('serverLocation')]",
          "apiVersion": "2.0",
          "dependsOn": [
            "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
          ],
          "properties": {
            "edition": "Web",
            "collation": "[parameters('collation')]",
            "maxSizeBytes": "1073741824"
          }
        },

Der Abschnitt **parameters** der Vorlage ist eine Sammlung der Parameter, die in allen Ressourcen definiert werden. Er umfasst die databaseName-, serverLocation- und collation-Eigenschaften.

    "parameters": {
    ...    

    "serverLocation": {
      "type": "string"
    }, 
    ...

    "databaseName": {
      "type": "string"
    },
    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    }

Einige Parameter haben einen Standardwert. Wenn Sie die Vorlage verwenden, müssen Sie keine Werte für diese Parameter angeben. Wenn Sie keinen Wert angeben, wird der Standardwert verwendet.

    "collation": {
          "type": "string",
          "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
        }

Wenn Parameter aufgezählte Werte haben, werden die gültigen Werte mit dem Parameter aufgelistet. Der **sku**-Parameter kann beispielsweise die Werte "Free", "Shared", "Basic" oder "Standard" annehmen. Wenn Sie keinen Wert für den **sku**-Parameter angeben, wird der Standardwert "Free" verwendet.

    "sku": {
      "type": "string",
      "allowedValues": [
        "Free",
        "Shared",
        "Basic",
        "Standard"
      ],
      "defaultValue": "Free"
    },

Beachten Sie, dass der **administratorLoginPassword**-Parameter eine sichere Zeichenfolge verwendet, keinen einfachen Text. Wenn Sie einen Wert für eine sichere Zeichenfolge angeben, wird der Wert ausgeblendet.

    "administratorLoginPassword": {
      "type": "securestring"
    },

Die Vorlage kann fast verwendet werden, aber zuvor müssen Sie die Speicherorte der einzelnen Ressourcen suchen.

## Schritt 4: Abrufen der Speicherorte für Ressourcentypen

Die meisten Vorlagen fordern Sie auf, einen Speicherort für jede einzelne Ressource in einer Ressourcengruppe anzugeben. Jede Ressource befindet sich in einem Azure-Rechenzentrum, aber nicht jedes Azure-Rechenzentrum unterstützt jeden Ressourcentyp.

Wählen Sie einen Speicherort aus, der den Ressourcentyp unterstützt. Die Ressourcen in einer Ressourcengruppe müssen sich nicht am gleichen Speicherort und nicht am gleichen Speicherort wie die Ressourcengruppe oder das Abonnement befinden.

Um die Speicherorte abzurufen, die die einzelnen Ressourcentypen unterstützen, verwenden Sie das **Get-AzureLocation**-Cmdlet. Es folgt ein Auszug der Ausgabe. (Diese Ausgabe kann sich von Ihrer unterscheiden. Die Details ändern sich wahrscheinlich mit der Zeit.)

    Name                                 Locations
    ----                                 ---------
    ResourceGroup                        East Asia, South East Asia, East US, West US, North Central US,
                                         South Central US, Central US, North Europe, West Europe

    Microsoft.Sql/servers/databases      Brazil South, Central US, East Asia, East US, East US 2, Japan
                                         East, Japan West, North Central US, North Europe, South Central US,
                                         Southeast Asia, West Europe, West US

    Microsoft.Web/serverFarms            Brazil South, East Asia, East US, Japan East, Japan West, North
                                         Central US, North Europe, West Europe, West US

    Microsoft.Web/sites                  Brazil South, East Asia, East US, Japan East, Japan West, North
                                         Central US, North Europe, West Europe, West US

Jetzt haben Sie die Informationen, die Sie zum Erstellen der Ressourcengruppe benötigen.

## Schritt 5: Erstellen einer Ressourcengruppe

In diesem Schritt verwenden Sie die Ressourcengruppenvorlage zum Erstellen der Ressourcengruppe. Öffnen Sie als Referenz die JSON-Datei "Microsoft.WebSiteSQLDatabase.0.1.0-preview1" auf der Festplatte.

Um eine Ressourcengruppe zu erstellen, verwenden Sie das **New-AzureResourceGroup**-Cmdlet.

Der Befehl verwendet den **Name**-Parameter, um einen Namen für die Ressourcengruppe anzugeben, und den **Location**-Parameter, um ihren Speicherort anzugeben. Verwenden Sie die Ausgabe von **Get-AzureLocation**, um einen Speicherort für die Ressourcengruppe auszuwählen. Um die Galerievorlage anzugeben, wird der **GalleryTemplateIdentity**-Parameter verwendet.

    PS C:\> New-AzureResourceGroup ` 
            -Name TestRG1 `
            -Location "East Asia" `
            -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 `
            ....

Sobald Sie den Vorlagennamen eingeben, ruft New-AzureResourceGroup die Vorlage ab, analysiert sie und fügt die Vorlagenparameter dynamisch zum Befehl hinzu. Auf diese Weise wird das Angeben der Vorlagenparameterwerte stark vereinfacht. Wenn Sie einen erforderlichen Parameterwert vergessen haben, fordert Sie Windows PowerShell zur Angabe des Werts auf.

**Dynamische Vorlagenparameter**

Um die Parameter abzurufen, geben Sie zur Angabe eines Parameternamens ein Minuszeichen (-) ein, und drücken Sie dann die TAB-Taste. Sie können auch die ersten Buchstaben eines Parameternamens eingeben, zum Beispiel siteName, und dann die TAB-Taste drücken.

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -si<TAB>

Windows PowerShell vervollständigt den Parameternamen. Um durch die Parameternamen zu schalten, drücken Sie wiederholt die TAB-Taste.

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName 

Geben Sie einen Namen für die Website ein, und wiederholen Sie den TAB-Prozess für jeden einzelnen Parameter. Die Parameter mit Standardwert sind optional. Um den Standardwert zu akzeptieren, geben Sie für den Befehl keinen Parameter an.

Wenn ein Vorlagenparameter über aufgezählte Werte verfügt, wie zum Beispiel der sku-Parameter in dieser Vorlage, drücken Sie die TAB-Taste, um durch die Parameterwerte zu schalten.

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName TestSite -sku <TAB>

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName TestSite -sku Free<TAB>

        PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 
        -siteName TestSite -sku Basic<TAB>

Es folgt ein Beispiel eines New-AzureResourceGroup-Befehls, der nur die erforderlichen Vorlagenparameter und den allgemeinen **Verbose**-Parameter angibt. Beachten Sie, dass **administratorLoginPassword** ausgelassen wird. (Das Hochkomma (\`) ist das Zeilenfortsetzungszeichen von Windows PowerShell.)

    PS C:\> New-AzureResourceGroup 
    -Name TestRG `
    -Location "East Asia" `
    -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.1.0-preview1 `
    -siteName TestSite `
    -hostingPlanName TestPlan `
    -siteLocation "North Europe" `
    -serverName testserver `
    -serverLocation "West US" `
    -administratorLogin Admin01 `
    -databaseName TestDB `
    -Verbose

Wenn Sie den Befehl eingeben, werden Sie zur Angabe des fehlenden obligatorischen **administratorLoginPassword**-Parameters aufgefordert. Wenn Sie das Kennwort eingeben, wird der Wert der sicheren Zeichenfolge ausgeblendet. Durch diese Strategie wird das Risiko der Angabe eines Kennworts in einfachem Text vermieden.

    cmdlet New-AzureResourceGroup at command pipeline position 1
    Supply values for the following parameters:
    (Type !? for Help.)
    administratorLoginPassword: **********

**New-AzureResourcGroup** gibt die Ressourcengruppe zurück, die erstellt und bereitgestellt wurde. Es folgt die Ausgabe des Befehls, einschließlich der ausführlichen Ausgabe.

    VERBOSE: 3:47:30 PM - Create resource group 'TestRG' in location 'East Asia'
    VERBOSE: 3:47:30 PM - Template is valid.
    VERBOSE: 3:47:31 PM - Create template deployment 'Microsoft.WebSiteSQLDatabase.0.1.0-preview1'
    using template https://gallerystoreprodch.blob.core.windows.net/prod-microsoft-windowsazure-gallery/8D6B920B-10F4-4B5A-B3DA-9D398FBCF3EE.PUBLICGALLERYITEMS.MICROSOFT.WEBSITESQLDATABASE.0.1.0-PREVIEW1/DeploymentTemplates/Website_NewHostingPlan_SQL_NewDB-Default.json.
    VERBOSE: 3:47:43 PM - Resource Microsoft.Sql/servers 'testserver' provisioning status is succeeded
    VERBOSE: 3:47:43 PM - Resource Microsoft.Web/serverFarms 'TestPlan' provisioning status is
    succeeded
    VERBOSE: 3:47:47 PM - Resource Microsoft.Sql/servers/databases 'testserver/TestDB' provisioning status is succeeded
    VERBOSE: 3:47:47 PM - Resource microsoft.insights/autoscalesettings 'TestPlan-TestRG'
    provisioning status is succeeded
    VERBOSE: 3:47:47 PM - Resource Microsoft.Sql/servers/firewallrules
    'testserver/AllowAllWindowsAzureIps' provisioning status is succeeded
    VERBOSE: 3:47:50 PM - Resource Microsoft.Web/Sites 'TestSite' provisioning status is succeeded
    VERBOSE: 3:47:54 PM - Resource Microsoft.Web/Sites/config 'TestSite/web' provisioning status is succeeded
    VERBOSE: 3:47:54 PM - Resource microsoft.insights/alertrules 'ServerErrors-TestSite' provisioning
    status is succeeded
    VERBOSE: 3:47:57 PM - Resource microsoft.insights/components 'TestSite' provisioning status is succeeded


    ResourceGroupName : TestRG
    Location          : eastasia
    ProvisioningState : Succeeded
    Resources         :
                    Name                   Type                                  Location
                    =====================  ====================================  =========
                    ServerErrors-TestSite  microsoft.insights/alertrules         eastus
                    TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
                    TestSite               microsoft.insights/components         centralus
                    testserver             Microsoft.Sql/servers                 westus
                    TestDB                 Microsoft.Sql/servers/databases       westus
                    TestPlan               Microsoft.Web/serverFarms             westus
                    TestSite               Microsoft.Web/sites                   westus

In wenigen Schritten haben Sie die Ressourcen, die für eine komplexe Website erforderlich sind, erstellt und bereitgestellt.
Die Galerievorlage hat fast alle Informationen, die für diese Aufgabe erforderlich waren, zur Verfügung gestellt.
Außerdem kann die Aufgabe problemlos automatisiert werden.

# <span id="manage"></span></a> Verwalten einer Ressourcengruppe

Nach dem Erstellen einer Ressourcengruppe können Sie die Cmdlets im AzureResourceManager-Modul verwenden, um die Ressourcengruppe zu verwalten, zu ändern, um Ressourcen hinzuzufügen und daraus zu entfernen.

-   Um die Ressourcengruppen in Ihrem Abonnement abzurufen, verwenden Sie das **Get-AzureResourceGroup**-Cmdlet:

        PS C:>Get-AzureResourceGroup

        ResourceGroupName : TestRG
        Location          : eastasia
        ProvisioningState : Succeeded
        Resources         :
                        Name                   Type                                  Location
                        =====================  ====================================  =========
                        ServerErrors-TestSite  microsoft.insights/alertrules         eastus
                        TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
                        TestSite               microsoft.insights/components         centralus
                        testserver             Microsoft.Sql/servers                 westus
                        TestDB                 Microsoft.Sql/servers/databases       westus
                        TestPlan               Microsoft.Web/serverFarms             westus
                        TestSite               Microsoft.Web/sites                   westus

-   Um die Ressourcen in der Ressourcengruppe abzurufen, verwenden Sie das **GetAzureResource**-Cmdlet und den zugehörigen ResourceGroupName-Parameter. Ohne Parameter ruft Get-AzureResource alle Ressourcen in Ihrem Azure-Abonnement ab.

        PS C:\> Get-AzureResource -ResourceGroupName TestRG

        Name                   ResourceType                          Location
        ----                   ------------                          --------
        ServerErrors-TestSite  microsoft.insights/alertrules         eastus
        TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
        TestSite               microsoft.insights/components         centralus
        testserver             Microsoft.Sql/servers                 westus
        TestDB                 Microsoft.Sql/servers/databases       westus
        TestPlan               Microsoft.Web/serverFarms             westus
        TestSite               Microsoft.Web/sites                   westus

-   Um eine Ressource zur Ressourcengruppe hinzuzufügen, verwenden Sie das **New-AzureResource**-Cmdlet. Dieser Befehl fügt der TestRG-Ressourcengruppe eine neue Website hinzu. Dieser Befehl ist etwas komplexer, da er keine Vorlage verwendet.

        PS C:\>New-AzureResource -Name TestSite2 `
        -Location "North Europe" `
        -ResourceGroupName TestRG `
        -ResourceType "Microsoft.Web/sites" `
        -ApiVersion 2004-04-01 `
        -PropertyObject @{"name" = "TestSite2"; "siteMode"= "Limited"; "computeMode" = "Shared"}

-   Um der Ressourcengruppe eine neue vorlagenbasierte Bereitstellung hinzuzufügen, verwenden Sie den **New-AzureResourceGroupDeployment**-Befehl.

        PS C:\>New-AzureResourceGroupDeployment ` 
        -ResourceGroupName TestRG `
        -GalleryTemplateIdentity Microsoft.WebSite.0.1.0-preview1 `
        -siteName TestWeb2 `
        -hostingPlanName TestDeploy2 `
        -siteMode Limited `
        -computeMode Dedicated `
        -siteLocation "North Europe" `
        -subscriptionID "9b14a38b-4b93-4554-8bb0-3cefb47abcde" `
        -resourceGroup TestRG

-   Um eine Ressource aus der Ressourcengruppe zu löschen, verwenden Sie das **Remove-AzureResource**-Cmdlet. Dieses Cmdlet löscht die Ressource, aber nicht die Ressourcengruppe.

    Dieser Befehl entfernt die TestSite2-Website aus der TestRG-Ressourcengruppe.

        Remove-AzureResource -Name TestSite2 `
            -Location "North Europe" `
            -ResourceGroupName TestRG `
            -ResourceType "Microsoft.Web/sites" `
            -ApiVersion 2004-04-01

-   Um eine Ressourcengruppe zu löschen, verwenden Sie das **Remove-AzureResourceGroup**-Cmdlet. Dieses Cmdlet löscht die Ressourcengruppe und ihre Ressourcen.

        PS C:\ps-test> Remove-AzureResourceGroup -Name TestRG

        Confirm
        Are you sure you want to remove resource group 'TestRG'
        [Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y

# <span id="troubleshoot"></span></a> Problembehandlung einer Ressourcengruppe

Wenn Sie mit den Cmdlets in den AzureResourceManager-Modulen experimentieren, treten wahrscheinlich Fehler auf. Verwenden Sie die Tipps in diesem Abschnitt, um sie zu lösen.

## Vermeiden von Fehlern

Das AzureResourceManager-Modul umfasst Cmdlets, mit denen Sie Fehler vermeiden können.

-   **Get-AzureLocation**: Dieses Cmdlet ruft die Speicherorte ab, die die einzelnen Ressourcentypen unterstützen. Bevor Sie einen Speicherort für eine Ressource eingeben, verwenden Sie dieses Cmdlet, um zu überprüfen, ob der Speicherort den Ressourcentyp unterstützt.

-   **Test-AzureResourceGroupTemplate**: Testen Sie die Vorlage und die Vorlagenparameter, bevor Sie sie verwenden. Geben Sie eine benutzerdefinierte oder eine Galerievorlage und die Vorlagenparameterwerte ein, die Sie verwenden möchten. Dieses Cmdlet testet, ob die Vorlage intern konsistent ist und ob die Parameterwerte der Vorlage entsprechen.

## Beheben von Fehlern

-   **Get-AzureResourceGroupLog**: Dieses Cmdlet ruft die Einträge im Protokoll für die einzelnen Bereitstellungen der Ressourcengruppe ab. Falls ein Fehler auftritt, sollten Sie zunächst die Bereitstellungsprotokolle untersuchen.

-   **Verbose und Debug**: Die Cmdlets im AzureResourceManager-Modul rufen REST-APIs auf, die die eigentliche Arbeit durchführen. Um die Meldungen anzuzeigen, die von den APIs zurückgegeben werden, legen Sie die $DebugPreference-Variable auf "Continue" fest und verwenden in Ihren Befehlen den allgemeinen Verbose-Parameter. Die Mitteilungen liefern häufig wertvolle Hinweise zur Ursache von Fehlern.

-   **Your Azure credentials have not been set up or have expired**: Um die Anmeldeinformationen in Ihrer Windows PowerShell-Sitzung zu aktualisieren, verwenden Sie das Add-AzureAccount-Cmdlet. Die Anmeldeinformationen in einer Datei mit Veröffentlichungseinstellungen sind für die Cmdlets im AzureResourceManager-Modul nicht ausreichend.

# <span id="next"></span></a>Nächste Schritte

So erhalten Sie weitere Informationen zum Verwenden von Windows PowerShell mit dem Ressourcen-Manager:

-   [Azure Resource Manager Cmdlets][1]: Informationen zur Verwendung der Cmdlets im AzureResourceManager-Module
-   [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen][Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen]: Informationen zum Erstellen und Verwalten von Ressourcengruppen im Azure-Verwaltungsportal
-   [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager][Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager]: Informationen zum Erstellen und Verwalten von Ressourcengruppen mit Befehlszeilentools, die auf vielen Betriebssystemplattformen einsetzbar sind
-   [Azure-Blog][Azure-Blog]: Informationen zu neuen Funktionen in Azure
-   [Windows PowerShell-Blog][Windows PowerShell-Blog]: Informationen zu neuen Funktionen in Windows PowerShell
-   ["Hey, Scripting Guy!"- Blog]["Hey, Scripting Guy!"- Blog]: Praktische Tipps und Tricks aus der Windows PowerShell-Community

  [Windows Management Framework 3.0]: http://www.microsoft.com/de-de/download/details.aspx?id=34595
  [Windows Management Framework 4.0]: http://www.microsoft.com/de-de/download/details.aspx?id=40855
  [Installieren und Konfigurieren von Windows Azure PowerShell]: http://azure.microsoft.com/documentation/articles/install-configure-powershell/
  [Erste Schritte mit Windows PowerShell]: http://technet.microsoft.com/library/hh857337.aspx
  [Info zu Azure PowerShell-Modulen]: #about
  [Erstellen einer Ressourcengruppe]: #create
  [Verwalten einer Ressourcengruppe]: #manage
  [Problembehandlung für eine Ressourcengruppe]: #troubleshoot
  [Nächste Schritte]: #next
  [Azure Service Management Cmdlets]: http://msdn.microsoft.com/library/jj152841.aspx
  [Azure Resource Manager Cmdlets]: http://go.microsoft.com/fwlink/?LinkID=394765
  [Azure Profile Cmdlets]: http://go.microsoft.com/fwlink/?LinkID=394766
  [Switch-AzureMode]: http://go.microsoft.com/fwlink/?LinkID=394398
  [Verwenden von Ressourcengruppen zum Verwalten von Azure-Ressourcen]: http://azure.microsoft.com/documentation/articles/azure-preview-portal-using-resource-groups
  [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager]: http://azure.microsoft.com/documentation/articles/xplat-cli-azure-resource-manager/
  [Azure-Blog]: http://blogs.msdn.com/windowsazure
  [Windows PowerShell-Blog]: http://blogs.msdn.com/powershell
  ["Hey, Scripting Guy!"- Blog]: http://blogs.technet.com/b/heyscriptingguy/

<!--HONumber=46--> 
