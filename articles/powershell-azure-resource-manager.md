<properties 
	pageTitle="Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager" 
	description="Verwenden Sie Azure PowerShell, um mehrere Ressourcen als Ressourcengruppe für Azure bereitzustellen." 
	services="" 
	documentationCenter="" 
	authors="tfitzmac" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="multiple" 
	ms.workload="multiple" 
	ms.tgt_pltfrm="powershell" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2015" 
	ms.author="tomfitz"/>

# Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager

> [AZURE.SELECTOR]
- [Azure PowerShell](powershell-azure-resource-manager.md)
- [Azure CLI](xplat-cli-azure-resource-manager.md)

Mit dem Azure-Ressourcen-Manager wird ein völlig neuer Denkansatz für Azure-Ressourcen eingeführt. Anstatt einzelne Ressourcen zu erstellen und zu verwalten, können Sie sich einen komplexen Dienst vorstellen, zum Beispiel einen Blog, eine Fotogalerie, ein SharePointPortal oder eine Wiki. Sie verwenden eine Vorlage – ein Ressourcenmodell des Dienstes – um eine Ressourcengruppe mit den Ressourcen zu erstellen, die Sie zur Unterstützung des Dienstes benötigen. Dann können Sie diese Ressourcengruppe als logische Einheit verwalten und bereitstellen.

In diesem Lernprogramm erlernen Sie die Verwendung von Azure PowerShell mit dem Azure-Ressourcen-Manager für Microsoft Azure. Sie werden durch den Prozess des Erstellens und Bereitstellens einer Ressourcengruppe für eine unter Azure gehostete Web-App mit einer SQL-Datenbank geleitet, einschließlich aller Ressourcen, die Sie zur Unterstützung benötigen.

## Voraussetzungen

Zum Durchführen dieses Lernprogramms benötigen Sie Azure PowerShell Version 0.8.0 oder höher. Um die neueste Version zu installieren und sie Ihrem Azure-Abonnement zuzuordnen, lesen Sie [Installieren und Konfigurieren von Azure PowerShell](powershell-install-configure.md).

Dieses Lernprogramm richtet sich an PowerShell-Anfänger. Es wird aber vorausgesetzt, dass Sie die grundlegenden Konzepte verstehen, z. B. Module, Cmdlets und Sitzungen. Weitere Informationen zu Windows PowerShell finden Sie unter [Erste Schritte mit Windows PowerShell](http://technet.microsoft.com/library/hh857337.aspx).

Um detaillierte Hilfe zu einem Cmdlet aus dem Lernprogramm zu erhalten, verwenden Sie das Get-Help-Cmdlet.

	Get-Help <cmdlet-name> -Detailed

Geben Sie beispielsweise Folgendes ein, um Hilfe zum Add-AzureAccount-Cmdlet zu erhalten:

	Get-Help Add-AzureAccount -Detailed

## Info zu Azure PowerShell-Modulen
Ab Version 0.8.0 umfasst die Azure PowerShell-Installation mehr als ein PowerShell-Modul. Sie müssen sich explizit entscheiden, ob Sie die Befehle verwenden, die im Azure-Modul verfügbar sind, oder die Befehle, die im Azure-Ressourcen-Manager-Modul verfügbar sind. Um das Wechseln zwischen ihnen zu vereinfachen, haben wir dem Azure Profile-Modul das neue Cmdlet **Switch-AzureMode** hinzugefügt.

Wenn Sie Azure PowerShell verwenden, werden die Cmdlets im Azure-Module standardmäßig importiert. Um zum Azure Resource Manager-Modul zu wechseln, verwenden Sie das Switch-AzureMode-Cmdlet. Es entfernt das Azure-Modul aus der Sitzung und importiert das Azure Resource Manager- und das Azure Profile-Modul.

Geben Sie Folgendes ein, um zum AzureResoureManager-Modul zu wechseln:

    PS C:\> Switch-AzureMode -Name AzureResourceManager

Geben Sie Folgendes ein, um zurück zum Azure-Modul zu wechseln:

    PS C:\> Switch-AzureMode -Name AzureServiceManagement

Standardmäßig wirkt sich Switch-AzureMode nur auf die aktuelle Sitzung aus. Damit der Wechsel in allen PowerShell-Sitzungen funktioniert, verwenden Sie den **Global**-Parameter von Switch-AzureMode.

Wenn Sie Hilfe zum Switch-AzureMode-Cmdlet benötigen, können Sie `Get-Help Switch-AzureMode` eingeben oder auf [Switch-AzureMode](http://go.microsoft.com/fwlink/?LinkID=394398) zugreifen.
  
Geben Sie Folgendes ein, um eine Liste der Cmdlets im AzureResourceManager-Modul mit einer Hilfezusammenfassung zu erhalten:

    PS C:\> Get-Command -Module AzureResourceManager | Get-Help | Format-Table Name, Synopsis

Die Ausgabe sieht etwa wie der folgende Auszug aus:

	Name                                   Synopsis
	----                                   --------
	Add-AlertRule                          Adds or updates an alert rule of either metric, event, o...
	Add-AzureAccount                       Adds the Azure account to Windows PowerShell
	Add-AzureEnvironment                   Creates an Azure environment
	Add-AzureKeyVaultKey                   Creates a key in a vault or imports a key into a vault.
        ...

Um die vollständige Hilfe zu einem Cmdlet zu erhalten, geben Sie einen Befehl im folgenden Format ein:

	Get-Help <cmdlet-name> -Full

Beispiel:

	Get-Help Get-AzureLocation -Full

Eine vollständige Liste der Befehle des Azure-Ressourcen-Managers finden Sie unter [Azure-Ressourcen-Manager-Cmdlets](http://go.microsoft.com/fwlink/?LinkID=394765).
  
## Erstellen einer Ressourcengruppe

Dieser Abschnitt des Lernprogramms leitet Sie durch den Prozess des Erstellens und Bereitstellens einer Ressourcengruppe für eine Web-App mit einer SQL-Datenbank.

Für diese Aufgabe müssen Sie kein Experte für Azure, SQL, Web-Apps oder Ressourcenverwaltung sein. Die Vorlagen stellen ein Modell der Ressourcengruppe mit allen Ressourcen bereit, die Sie wahrscheinlich benötigen. Und da Windows PowerShell zum Automatisieren der Aufgaben verwendet wird, können Sie diesen Prozess als Modell für die Erstellung von Skripts für umfangreiche Aufgaben verwenden.

### Schritt 1: Wechseln zum Azure-Ressourcen-Manager 
1. Starten Sie PowerShell. Sie können ein beliebiges Hostprogramm verwenden, zum Beispiel die Azure PowerShell-Konsole oder Windows PowerShell ISE.

2. Verwenden Sie das **Switch-AzureMode**-Cmdlet, um die Cmdlets in die AzureResourceManager- und AzureProfile-Module zu importieren.

        PS C:\> Switch-AzureMode AzureResourceManager

3. Um der Windows PowerShell-Sitzung Ihr Azure-Konto hinzuzufügen, verwenden Sie das **Add-AzureAccount**-Cmdlet.

        PS C:\> Add-AzureAccount

Sie werden vom Cmdlet zur Eingabe der Anmeldeinformationen für Ihr Azure-Konto aufgefordert. Nach dem Anmelden werden Ihre Kontoeinstellungen heruntergeladen, damit sie für Windows PowerShell zur Verfügung stehen.

Die Kontoeinstellungen laufen ab. Sie müssen sie also ab und zu aktualisieren. Um die Kontoeinstellungen zu aktualisieren, führen Sie **Add-AzureAccount** erneut aus.

>[AZURE.NOTE]Das AzureResourceManager-Modul erfordert Add-AzureAccount. Eine Datei mit Veröffentlichungseinstellungen ist nicht ausreichend.

### Schritt 2: Auswählen einer Katalogvorlage

Es gibt mehrere Methoden zum Erstellen einer Ressourcengruppe samt Ressourcen. Die einfachste besteht aber im Verwenden einer Ressourcengruppenvorlage. Eine *Ressourcengruppenvorlage* ist eine JSON-Zeichenfolge, die die Ressource in einer Ressourcengruppe definiert. Die Zeichenfolge umfasst Platzhalter, die als "Parameter" bezeichnet werden, für benutzerdefinierte Werte, wie zum Beispiel Namen und Größen.

Azure enthält eine Galerie mit Ressourcengruppenvorlagen. Außerdem können Sie eigene Vorlagen von Grund auf oder durch Bearbeiten einer Galerievorlage erstellen. In diesem Lernprogramm wird eine Galerievorlage verwendet.

Wenn Sie alle Vorlagen des Katalogs mit den Azure-Ressourcengruppenvorlagen anzeigen möchten, können Sie das **Get-AzureResourceGroupGalleryTemplate**-Cmdlet verwenden. Mit diesem Befehl wird aber eine große Zahl von Vorlagen zurückgegeben. Um eine überschaubare Zahl von Vorlagen anzuzeigen, geben Sie einen publisher-Parameter an.

Geben Sie an der PowerShell-Eingabeaufforderung Folgendes ein:
    
    PS C:\> Get-AzureResourceGroupGalleryTemplate -Publisher Microsoft

Das Cmdlet gibt eine Liste der Katalogvorlagen mit Microsoft als Herausgeber zurück. Sie verwenden die **Identity**-Eigenschaft, um die Vorlage in den Befehlen zu identifizieren.

Die Vorlage „Microsoft.WebSiteSQLDatabase.0.2.6-preview“ sieht interessant aus. Wenn Sie den Befehl ausführen, kann die Version der Vorlage leicht abweichen, da eine neue Version veröffentlicht wurde. Verwenden Sie die aktuelle Version der Vorlage. Um weitere Informationen zu einer Galerievorlage zu erhalten, verwenden Sie den **Identity**-Parameter. Der Wert des Identity-Parameters ist die Identität der Vorlage.

    PS C:\> Get-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview

Das Cmdlet gibt ein Objekt mit viel mehr Informationen zur Vorlage zurück, unter anderem eine Zusammenfassung und eine Beschreibung.

Diese Vorlage scheint für uns geeignet zu sein. Speichern Sie sie auf der Festplatte und sehen Sie sie sich näher an.

### Schritt 3: Untersuchen der Vorlage

Speichern Sie die Vorlage in einer JSON-Datei auf der Festplatte. Dieser Schritt ist nicht erforderlich, erleichtert aber das Anzeigen der Vorlage. Um die Vorlage zu speichern, verwenden Sie das **Save-AzureResourceGroupGalleryTemplate**-Cmdlet. Verwenden Sie den **Identity**-Parameter, um die Vorlage anzugeben, und den **Path**-Parameter, um einen Pfad auf der Festplatte anzugeben.

"Save-AzureResourceGroupGalleryTemplate" speichert die Vorlage und gibt den Pfad und den Dateinamen der JSON-Vorlagendatei zurück.

	PS C:\> Save-AzureResourceGroupGalleryTemplate -Identity Microsoft.WebSiteSQLDatabase.0.2.6-preview -Path C:\Azure\Templates\New_WebSite_And_Database.json

	Path
	----
	C:\Azure\Templates\New_WebSite_And_Database.json


Sie können die Vorlagendatei in einem Texteditor anzeigen, zum Beispiel in Editor. Jede Vorlage verfügt über einen Abschnitt **parameters** und einen Abschnitt **resources**.

Der Abschnitt **parameters** der Vorlage ist eine Sammlung der Parameter, die in allen Ressourcen definiert werden. Darin sind Eigenschaftswerte enthalten, die Sie beim Einrichten der Ressourcengruppe bereitstellen können.

    "parameters": {
      "siteName": {
        "type": "string"
      },
      "hostingPlanName": {
        "type": "string"
      },
      "siteLocation": {
        "type": "string"
      },
      ...
    }

Einige Parameter haben einen Standardwert. Wenn Sie die Vorlage verwenden, müssen Sie keine Werte für diese Parameter angeben. Wenn Sie keinen Wert angeben, wird der Standardwert verwendet.

    "collation": {
      "type": "string",
      "defaultValue": "SQL_Latin1_General_CP1_CI_AS"
    },

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
          "edition": "[parameters('edition')]",
          "collation": "[parameters('collation')]",
          "maxSizeBytes": "[parameters('maxSizeBytes')]",
          "requestedServiceObjectiveId": "[parameters('requestedServiceObjectiveId')]"
        }
    },


Die Vorlage kann fast verwendet werden, aber zuvor müssen Sie die Speicherorte der einzelnen Ressourcen suchen.

### Schritt 4: Abrufen der Speicherorte für Ressourcentypen

Die meisten Vorlagen fordern Sie auf, einen Speicherort für jede einzelne Ressource in einer Ressourcengruppe anzugeben. Jede Ressource befindet sich in einem Azure-Rechenzentrum, aber nicht jedes Azure-Rechenzentrum unterstützt jeden Ressourcentyp.

Wählen Sie einen Speicherort aus, der den Ressourcentyp unterstützt. Sie müssen nicht alle Ressourcen in einer Ressourcengruppe am gleichen Ort erstellen. Falls es möglich ist, ist das Erstellen von Ressourcen an demselben Ort aber ratsam, um die Leistung zu optimieren. Achten Sie besonders darauf, dass sich Ihre Datenbank an demselben Ort wie die App befindet, mit der darauf zugegriffen wird.

Um die Speicherorte abzurufen, die die einzelnen Ressourcentypen unterstützen, verwenden Sie das **Get-AzureLocation**-Cmdlet. Verwenden Sie Folgendes, um Ihre Ausgabe auf einen bestimmten Typ von Ressource zu beschränken, z. B. ResourceGroup:

    Get-AzureLocation | Where-Object Name -eq "ResourceGroup" | Format-Table Name, LocationsString -Wrap

Die Ausgabe ähnelt der folgenden Ausgabe:

    Name                                 LocationsString
    ----                                 ---------------
    ResourceGroup                        East Asia, South East Asia, East US, West US, North
                                         Central US, South Central US, Central US, North Europe,
                                         West Europe

Jetzt haben Sie die Informationen, die Sie zum Erstellen der Ressourcengruppe benötigen.

### Schritt 5: Erstellen einer Ressourcengruppe
 
In diesem Schritt verwenden Sie die Ressourcengruppenvorlage zum Erstellen der Ressourcengruppe. Sie können zur Referenz die Datei „New_WebSite_And_Database.json“ auf dem Datenträger öffnen und dies mitverfolgen. Die Vorlagendatei kann sehr hilfreich dabei sein, die zu übergebenden Parameterwerte zu ermitteln, z. B. die richtige ApiVersion für eine Ressource.

Um eine Ressourcengruppe zu erstellen, verwenden Sie das **New-AzureResourceGroup**-Cmdlet.

Der Befehl verwendet den **Name**-Parameter, um einen Namen für die Ressourcengruppe anzugeben, und den **Location**-Parameter, um ihren Speicherort anzugeben. Verwenden Sie die Ausgabe von **Get-AzureLocation**, um einen Speicherort für die Ressourcengruppe auszuwählen. Um die Galerievorlage anzugeben, wird der **GalleryTemplateIdentity**-Parameter verwendet.

	PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview
            ....

Sobald Sie den Vorlagennamen eingeben, ruft New-AzureResourceGroup die Vorlage ab, analysiert sie und fügt die Vorlagenparameter dynamisch zum Befehl hinzu. Auf diese Weise wird das Angeben der Vorlagenparameterwerte stark vereinfacht. Wenn Sie einen erforderlichen Parameterwert vergessen haben, fordert Sie Windows PowerShell zur Angabe des Werts auf.

**Dynamische Vorlagenparameter**

Um die Parameter abzurufen, geben Sie zur Angabe eines Parameternamens ein Minuszeichen (-) ein, und drücken Sie dann die TAB-Taste. Sie können auch die ersten Buchstaben eines Parameternamens eingeben, zum Beispiel siteName, und dann die TAB-Taste drücken.

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -si<TAB>

PowerShell vervollständigt den Parameternamen. Um durch die Parameternamen zu schalten, drücken Sie wiederholt die TAB-Taste.

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName 

Geben Sie einen Namen für die Website ein, und wiederholen Sie den TAB-Prozess für jeden einzelnen Parameter. Die Parameter mit Standardwert sind optional. Um den Standardwert zu akzeptieren, geben Sie für den Befehl keinen Parameter an.

Wenn ein Vorlagenparameter über aufgezählte Werte verfügt, wie zum Beispiel der sku-Parameter in dieser Vorlage, drücken Sie die TAB-Taste, um durch die Parameterwerte zu schalten.

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku <TAB>

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Basic<TAB>

    PS C:\> New-AzureResourceGroup -Name TestRG1 -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -sku Free<TAB>

Es folgt ein Beispiel eines New-AzureResourceGroup-Befehls, der nur die erforderlichen Vorlagenparameter und den allgemeinen **Verbose**-Parameter angibt. Beachten Sie, dass **administratorLoginPassword** ausgelassen wird.

	PS C:\> New-AzureResourceGroup -Name TestRG -Location "East Asia" -GalleryTemplateIdentity Microsoft.WebSiteSQLDatabase.0.2.6-preview -siteName TestSite -hostingPlanName TestPlan -siteLocation "East Asia" -serverName testserver -serverLocation "East Asia" -administratorLogin Admin01 -databaseName TestDB -Verbose

Wenn Sie den Befehl eingeben, werden Sie zur Angabe des fehlenden obligatorischen **administratorLoginPassword**-Parameters aufgefordert. Wenn Sie das Kennwort eingeben, wird der Wert der sicheren Zeichenfolge ausgeblendet. Durch diese Strategie wird das Risiko der Angabe eines Kennworts in einfachem Text vermieden.

	cmdlet New-AzureResourceGroup at command pipeline position 1
	Supply values for the following parameters:
	(Type !? for Help.)
	administratorLoginPassword: **********

**New-AzureResourceGroup** gibt die Ressourcengruppe zurück, die erstellt und bereitgestellt wurde.

In wenigen Schritten haben Sie die Ressourcen, die für eine komplexe Website erforderlich sind, erstellt und bereitgestellt. Die Galerievorlage hat fast alle Informationen, die für diese Aufgabe erforderlich waren, bereitgestellt. Außerdem kann die Aufgabe problemlos automatisiert werden.

## Abrufen von Informationen zu Ihren Ressourcengruppen

Nach dem Erstellen einer Ressourcengruppe können Sie die Cmdlets im AzureResourceManager-Modul verwenden, um Ihre Ressourcengruppen zu verwalten.

- Um alle Ressourcengruppen in Ihrem Abonnement abzurufen, verwenden Sie das **Get-AzureResourceGroup**-Cmdlet:

		PS C:\>Get-AzureResourceGroup

		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG
		
		...

- Um die Ressourcen in der Ressourcengruppe abzurufen, verwenden Sie das **GetAzureResource**-Cmdlet und den zugehörigen ResourceGroupName-Parameter. Ohne Parameter ruft Get-AzureResource alle Ressourcen in Ihrem Azure-Abonnement ab.

		PS C:\> Get-AzureResource -ResourceGroupName TestRG
		
		ResourceGroupName : TestRG
		Location          : eastasia
		ProvisioningState : Succeeded
		Tags              :
		
		Resources         :
				Name                   Type                          Location
				----                   ------------                  --------
				ServerErrors-TestSite  microsoft.insights/alertrules         eastasia
	        	TestPlan-TestRG        microsoft.insights/autoscalesettings  eastus
	        	TestSite               microsoft.insights/components         centralus
	         	testserver             Microsoft.Sql/servers                 eastasia
	        	TestDB                 Microsoft.Sql/servers/databases       eastasia
	        	TestPlan               Microsoft.Web/serverFarms             eastasia
	        	TestSite               Microsoft.Web/sites                   eastasia
		ResourceId        : /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/TestRG

## Hinzufügen zu einer Ressourcengruppe

- Um eine Ressource zur Ressourcengruppe hinzuzufügen, verwenden Sie das **New-AzureResource**-Cmdlet. Dieser Befehl fügt der TestRG-Ressourcengruppe eine neue Website hinzu. Dieser Befehl ist etwas komplexer, da er keine Vorlage verwendet. 

        PS C:\>New-AzureResource -Name TestSite2 -Location "North Europe" -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01 -PropertyObject @{"name" = "TestSite2"; "siteMode"= "Limited"; "computeMode" = "Shared"}

- Um der Ressourcengruppe eine neue vorlagenbasierte Bereitstellung hinzuzufügen, verwenden Sie den **New-AzureResourceGroupDeployment**-Befehl.

		PS C:\>New-AzureResourceGroupDeployment ` 
		-ResourceGroupName TestRG `
		-GalleryTemplateIdentity Microsoft.WebSite.0.2.6-preview `
		-siteName TestWeb2 `
		-hostingPlanName TestDeploy2 `
		-siteLocation "North Europe" 

## Verschieben einer Ressource

- Verwenden Sie zum Verschieben einer vorhandenen Ressource in die Ressourcengruppe den Befehl **Move-AzureResource**.

		PS C:\> Move-AzureResource -DestinationResourceGroupName TestRG -ResourceId /subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/OtherExample/providers/Microsoft.ClassicStorage/storageAccounts/examplestorage

## Löschen einer Ressourcengruppe

- Um eine Ressource aus der Ressourcengruppe zu löschen, verwenden Sie das **Remove-AzureResource**-Cmdlet. Dieses Cmdlet löscht die Ressource, aber nicht die Ressourcengruppe.

	Dieser Befehl entfernt die TestSite2-Website aus der TestRG-Ressourcengruppe.

		Remove-AzureResource -Name TestSite2 -ResourceGroupName TestRG -ResourceType "Microsoft.Web/sites" -ApiVersion 2014-06-01

- Um eine Ressourcengruppe zu löschen, verwenden Sie das **Remove-AzureResourceGroup**-Cmdlet. Dieses Cmdlet löscht die Ressourcengruppe und ihre Ressourcen.

		PS C:\ps-test> Remove-AzureResourceGroup -Name TestRG
		
		Confirm
		Are you sure you want to remove resource group 'TestRG'
		[Y] Yes  [N] No  [S] Suspend  [?] Help (default is "Y"): Y


## Problembehandlung für eine Ressourcengruppe
Wenn Sie mit den Cmdlets in den AzureResourceManager-Modulen experimentieren, treten wahrscheinlich Fehler auf. Verwenden Sie die Tipps in diesem Abschnitt, um sie zu lösen.

### Vermeiden von Fehlern

Das AzureResourceManager-Modul umfasst Cmdlets, mit denen Sie Fehler vermeiden können.


- **Get-AzureLocation**: Dieses Cmdlet ruft die Speicherorte ab, die die einzelnen Ressourcentypen unterstützen. Bevor Sie einen Speicherort für eine Ressource eingeben, verwenden Sie dieses Cmdlet, um zu überprüfen, ob der Speicherort den Ressourcentyp unterstützt.


- **Test-AzureResourceGroupTemplate**: Testen Sie Ihre Vorlage und Vorlagenparameter, bevor Sie sie benutzen. Geben Sie eine benutzerdefinierte oder eine Galerievorlage und die Vorlagenparameterwerte ein, die Sie verwenden möchten. Dieses Cmdlet testet, ob die Vorlage intern konsistent ist und ob die Parameterwerte der Vorlage entsprechen.



### Beheben von Fehlern

- **Get-AzureResourceGroupLog**: Dieses Cmdlet ruft die Einträge im Protokoll für die einzelnen Bereitstellungen der Ressourcengruppe ab. Falls ein Fehler auftritt, sollten Sie zunächst die Bereitstellungsprotokolle untersuchen. 

- **Verbose und Debug**: Die Cmdlets im AzureResourceManager-Modul rufen REST-APIs auf, die die eigentliche Arbeit durchführen. Um die Meldungen anzuzeigen, die von den APIs zurückgegeben werden, legen Sie die $DebugPreference-Variable auf "Continue" fest und verwenden in Ihren Befehlen den allgemeinen Verbose-Parameter. Die Mitteilungen liefern häufig wertvolle Hinweise zur Ursache von Fehlern.

- **Ihre Azure-Anmeldeinformationen wurden nicht eingestellt oder sind abgelaufen**: Verwenden Sie das Cmdlet Add-AzureAccount, um die Anmeldeinformationen während Ihrer Windows PowerShell-Sitzung zu aktualisieren. Die Anmeldeinformationen in einer Datei mit Veröffentlichungseinstellungen sind für die Cmdlets im AzureResourceManager-Modul nicht ausreichend.


## Nächste Schritte
Erste Schritte

- [Übersicht über den Azure Resource Manager](./resource-group-overview.md)
- [Verwenden der plattformübergreifenden Azure-Befehlszeilenschnittstelle mit dem Ressourcen-Manager](./xplat-cli-azure-resource-manager.md)
- [Using the Azure Portal to manage your Azure resources](./resource-group-portal.md) (Verwenden des Azure-Portals zum Verwalten Ihrer Azure-Ressourcen)

Erstellen und Bereitstellen von Anwendungen

- [Authoring Azure Resource Manager Templates](./resource-group-authoring-templates.md) (Erstellen von Vorlagen im Azure-Ressourcen-Manager)
- [Deploy an application with Azure Resource Manager Template](./resource-group-template-deploy.md) (Bereitstellen einer Anwendung mit einer Vorlage im Azure-Ressourcen-Manager)
- [Vorhersagbares Bereitstellen einer komplexen Anwendung in Azure](app-service-web/app-service-deploy-complex-application-predictably.md)
- [Problembehandlung beim Bereitstellen von Ressourcengruppen in Azure](./resource-group-deploy-debug.md)
- [Azure Resource Manager Template Functions](./resource-group-template-functions.md) (Vorlagenfunktionen im Azure-Ressourcen-Manager)
- [Erweiterte Vorlagenvorgänge](./resource-group-advanced-template.md)

Organisieren von Ressourcen

- [Using tags to organize your Azure resources](./resource-group-using-tags.md) (Verwenden von Tags zum Organisieren von Azure-Ressourcen)

Zugriffsverwaltung und -überwachung

- [Verwalten und Überwachen des Ressourcen-Zugriffs](./resource-group-rbac.md)
- [Authentifizieren eines Dienstprinzipals mit Azure-Ressourcen-Manager](./resource-group-authenticate-service-principal.md)
- [Erstellen neuer Azure-Dienstprinzipale mit dem Azure-Portal](./resource-group-create-service-principal-portal.md)

<!---HONumber=July15_HO1-->