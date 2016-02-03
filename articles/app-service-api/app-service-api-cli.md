<properties
	pageTitle="Die Azure-Befehlszeilenschnittstelle und API-Apps | Microsoft Azure"
	description="Hier erfahren Sie, wie Sie die Microsoft Azure-Befehlszeilenschnittstelle (CLI) für Mac, Linux und Windows mit Azure API-Apps verwenden."
	editor="jimbe"
	manager="wpickett"
	documentationCenter=""
	authors="tdykstra"
	services="app-service\api"/>

<tags
	ms.service="app-service-api"
	ms.workload="web"
	ms.tgt_pltfrm="command-line-interface"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Die Azure-Befehlszeilenschnittstelle (CLI) und API-Apps

In diesem Artikel erfahren Sie, wie Sie API-Apps in Azure App Service mithilfe der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows erstellen, verwalten und löschen.

## Voraussetzungen

In diesem Artikel wird davon ausgegangen, dass Sie die Azure-Befehlszeilenschnittstelle installiert haben und wissen, wie Sie damit allgemeine Aufgaben ausführen. Eine Einführung zur Befehlszeilenschnittstelle finden Sie unter [Installieren und Konfigurieren der Azure-Befehlszeilenschnittstelle](../xplat-cli-install.md).

> [AZURE.NOTE]Die Anweisungen für das [Herstellen einer Verbindung mit einem Azure-Abonnement](../xplat-cli-connect.md) enthalten zwei Alternativen: Anmelden mit einem Geschäfts- oder Schulkonto und Herunterladen einer *.publishsettings*-Datei. Bei API-Apps kann die Authentifizierung mithilfe der Datei *.publishsettings* nicht verwendet werden. Dies liegt daran, dass Sie den Ressourcenverwaltungsmodus (im nächsten Abschnitt vorgestellt) für die Verwendung von API-Apps nutzen müssen. Die Authentifizierung mithilfe der Datei *.publishsettings* funktioniert nicht mit dem Ressourcen-Manager.

## Aktivieren des Ressourcenverwaltungsmodus

Die Befehlszeilenschnittstelle kann im [Service Management (ASM)](../virtual-machines/virtual-machines-command-line-tools.md)-Modus oder im [Ressourcen-Manager (ARM)](../xplat-cli-azure-resource-manager.md)-Modus verwendet werden. Für API-Apps müssen Sie den Ressourcen-Manager-Modus verwenden. Der `arm`-Modus ist nicht standardmäßig aktiviert. Aktivieren Sie ihn mithilfe des Befehls `config mode arm`.

	azure config mode arm

## Auflisten der Befehle, die für das Arbeiten mit API-Apps zur Verfügung stehen

Um alle derzeit für das Arbeiten mit API-Apps verfügbaren Befehle anzuzeigen, führen Sie den Befehl `apiapp` aus.

	azure apiapp

## Auflisten aller API-Apps in einem Abonnement oder einer Ressourcengruppe

Führen Sie zum Auflisten aller API-Apps in Ihrem Abonnement den Befehl `apiapp list` ohne Parameter aus.

	azure apiapp list

Die Liste enthält die Ressourcengruppe, den Namen der API-App, die Paket-ID und die URL der einzelnen API-Apps.

	info:    Executing command apiapp list
	info:    Listing ApiApps
	data:    Resource Group  Name           Package Id        Url                                                                       
	data:    --------------  -------------  ----------------  --------------------------------------------------------------------------
	data:    mygroup         SimpleDropbox  Microsoft.ApiApp  https://microsoft-apiappf1bbba377c6d4aa1f03146cadd6.azurewebsites.net
	info:    apiapp list command OK

Um die Liste auf eine bestimmte Ressourcengruppe zu beschränken, fügen Sie den Parameter für die Gruppe (`-g`) hinzu.

	azure apiapp list -g <resource group name>

Beispiel:

	azure apiapp list -g mygroup

Wenn Sie der Liste Informationen zur API-App-Version und Zugriffsebene hinzufügen möchten, verwenden Sie den Parameter für Details (`-d`).

	azure apiapp list -d

Die `list`-Ausgabe mit den zusätzlichen Feldern sieht wie im folgenden Beispiel aus:

	info:    Executing command apiapp list
	info:    Listing ApiApps
	data:    Resource Group  Name           Package Id     Version  Auth                 Url                                                                       
	data:    --------------  -------------  -------------  -------  -------------------  --------------------------------------------------------------------------
	data:    mygroup         SimpleDropbox  SimpleDropbox  1.0.0    PublicAuthenticated  https://microsoft-apiappf1bbba377cbd2a3aa1f03146c6.azurewebsites.net
	info:    apiapp list command OK

### Auflisten von Details zu einer API-App

Verwenden Sie zum Auflisten von Details zu einer API-App den Befehl `apiapp show` mit den Parametern für die Gruppe (`-g`) und den API-App-Namen (`-n`).

	azure apiapp show -g <resource group name> -n <API app name>

Beispiel:

	azure apiapp show -g mygroup -n SimpleDropbox

Die Ausgabe sieht wie folgt aus:

	info:    Executing command apiapp show
	info:    Getting ApiApp
	data:    Name:              SimpleDropbox
	data:    Location:          West US
	data:    Resource Group:    mygroup
	data:    Package Id:        SimpleDropbox
	data:    Package Version:   1.0.0
	data:    Update Policy:     Disabled
	data:    Access Level:      PublicAuthenticated
	data:    Hosting site name: microsoft-apiappf1bbba377c6d4bd2a36cadd6
	data:    Gateway name:      SD1aeb4ae60b7cb4f3d966dfa43b6607f30
	info:    apiapp show command OK

## Erstellen einer API-App

Es gibt zwei Möglichkeiten zur Erstellung von API-Apps: Verwenden Sie imperative CLI-Befehle, um einzelne Azure-Ressourcen zu erstellen. Sie können auch deklarative Syntax in einer Vorlage definieren, um alle erforderlichen Ressourcen zusammen festzulegen, und diese Vorlage dann mit einem CLI-Befehl bereitstellen. Informationen zum deklarativen Ansatz finden Sie unter [Nächste Schritte](#next-steps).

Führen Sie zum Erstellen einer API-App mithilfe von imperativen Befehlen die folgenden Schritte aus:

1. Auswählen eines zulässigen Standorts
1. Erstellen oder Suchen einer zu verwendenden Ressourcengruppe
2. Erstellen oder Suchen eines zu verwendenden App Service-Plans
4. Erstellen der API-App

### Auswählen eines zulässigen Standorts

Wenn Sie eine Ressourcengruppe erstellen, müssen Sie einen Standort angeben. Hier sind einige für API-Apps zulässige Standorte aufgeführt:

* USA (Ost)
* USA (West)
* USA (Mitte/Süden)
* Nordeuropa
* Ostasien
* Japan Ost
* Westeuropa
* Südostasien
* Japan (Westen)
* USA (Mitte/Norden)
* USA (Mitte)
* Brasilien Süd
* USA (Ost) 2

Rufen Sie die vollständige, aktuelle Liste der Orte mithilfe des Befehls `location list` ab. Sehen Sie dann in der Zeile des Ressourcenanbieters für `Microsoft.AppService/apiapps` nach.

	azure location list

Hier sehen Sie eine Beispielausgabe des `location list`-Befehls.

	info:    Executing command location list
	info:    Getting Resource Providers
	data:    Name                                                                Location                                                                                                                                                   
	data:    ------------------------------------------------------------------  -----------------------------------------------------------------------------------------------------------------------------------------------------------
	data:    Microsoft.ApiManagement/service                                     East US,North Central US,South Central US,West US,North Europe,West Europe,East Asia,Southeast Asia,Japan East,Japan West,Brazil South                     
	data:    Microsoft.AppService/apiapps                                        East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
	data:    Microsoft.AppService/appIdentities                                  East US,West US,South Central US,North Europe,East Asia,Japan East,West Europe,Southeast Asia,Japan West,North Central US,Central US,Brazil South,East US 2
	info:    location list command OK

### Erstellen oder Suchen einer zu verwendenden Ressourcengruppe

Verwenden Sie zum Erstellen einer Ressourcengruppe den Befehl `group create` mit den Parametern für Name (`n`) und Standort (`l`).

	azure group create -n <name> -l <location>

Beispiel:

	azure group create -n "mygroup" -l "West US"

Suchen Sie eine vorhandene Ressourcengruppe mit dem Befehl `group list`, und wählen Sie eine Ressourcengruppe an einem für API-Apps zulässigen Standort.

	azure group list

### Erstellen oder Suchen eines zu verwendenden App Service-Plans

Verwenden Sie zum Erstellen eines App Service-Plans den Befehl `resource create` und den Parameter für den Ressourcentyp (`-r`), um anzugeben, dass Sie einen App Service-Plan erstellen möchten.

	azure resource create -g <resource group> -n <app service plan name> -r "Microsoft.Web/serverfarms" -l <location> -o <api version> -p "{"sku": {"tier": "<pricing tier>"}, "numberOfWorkers" : <number of workers>, "workerSize": "<worker size>"}"
	
Beispiel:

	azure resource create -g mygroup -n myplan -r "Microsoft.Web/serverfarms" -l "West US" -o "2015-06-01" -p "{"sku": {"tier": "Standard"}, "numberOfWorkers" : 1, "workerSize": "Small"}"

Die JSON-Zeichenfolge für den Parameter `properties`(`-p`) ist aufgrund aktueller Änderungen an der REST-API erforderlich. In Zukunft ist der Parameter `-p` optional.

Der Beispielbefehl gibt die neueste API-Version zum Zeitpunkt der Erstellung dieses Artikels an. Um zu überprüfen, ob eine neuere Version verfügbar ist, verwenden Sie den Befehl `provider show`. Sehen Sie im `apiVersions`-Array für das `sites`-Objekt im `resourceTypes`-Array nach.

	azure provider show -n Microsoft.Web --json
   
Hier sehen Sie ein Beispiel für das `sites`-Objekt in der Befehlsausgabe.

	{
	  "resourceTypes": [
	    {
	      "apiVersions": [
	        "2015-06-01",
	        "2015-05-01",
	        "2015-04-01",
	        "2014-04-01"
	      ],
	      "locations": [
	        "East Asia",
	        "East US",
	        "Japan East",
	        "Japan West",
	        "North Europe",
	        "West Europe",
	        "West US",
	        "Southeast Asia",
	        "Central US",
	        "East US 2"
	      ],
	      "properties": {},
	      "name": "sites"
	    }

Verwenden Sie zum Auflisten vorhandener App Service-Pläne den Befehl `resource list`, und geben Sie mithilfe des Parameters `-r` App Service-Plan als Ressourcentyp an.

	azure resource list -r Microsoft.Web/serverfarms

Die Ausgabe sieht wie folgt aus:

	info:    Executing command resource list
	info:    Listing resources
	data:    Id                                                                                                                                           Name             Resource Group          Type                       Parent  Location  Tags
	data:    -------------------------------------------------------------------------------------------------------------------------------------------  ---------------  ----------------------  -------------------------  ------  --------  ----
	data:    /subscriptions/aeb4ae60-b7cb-4f3d-966d-fa43b6607f30/resourceGroups/ContosoAdsGroup/providers/Microsoft.Web/serverFarms/ContosoAdsPlan        ContosoAdsPlan   ContosoAdsGroup         Microsoft.Web/serverFarms          westus    null
	info:    resource list command OK

### Erstellen einer leeren (benutzerdefinierten) API-App

Verwenden Sie zum Erstellen einer leeren API-App (d. h. einer App, für die Sie selbst den Code schreiben) den Befehl `apiapp create`, und geben Sie das NuGet-Paket `Microsoft.ApiApp` (Parameter `-u`) an.

	azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u Microsoft.ApiApp

Beispiel:

	azure apiapp create -g mygroup -n newapiapp -p myplan -u Microsoft.ApiApp

Die Ausgabe gibt Aufschluss über den Status der API-App-Erstellung.

	info:    Executing command apiapp create
	info:    Checking resource group and app service plan
	info:    Getting package metadata
	info:    Creating deployment
	info:    Waiting for deployment completion
	info:    Deployment started:
	data:    Subscription Id: aeb4ae60-b7cb-4f3d-966d-fa43b6607f30
	data:    Resource Group:  mygroup
	data:    Deployment Name: AppServiceDeployment_f67fa710-d565-43cf-8c9c-
	                          d515dd2eb903
	data:    Correlation Id:  a7894287-c585-46d5-96a4-feac4b2f48c6
	data:    Timestamp:       2015-07-21T23:20:12.8858274Z
	data:    
	data:    Operation           State         Status        Resource                                          
	data:    ------------------- ------------- ------------- --------------------------------------------------
	data:    E8D88DA720375394    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a
	data:    FC31834D2E73D10E    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/providers/Microsoft.Resources/links/apiApp
	data:    C5B48DAF91306BB4    Succeeded     OK            /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.Web/sites/Microsoft-ApiApp33056917d6e34238b2606d71caf73b6a/siteextensions/Microsoft.ApiApp
	data:    F4B943428026A1B2    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp
	data:    4B3A935892415369    Succeeded     Created       /subscriptions/aeb4ae60-b7cb-4f3d-aeb4ae60-b6607f30/resourceGroups/mygroup/providers/Microsoft.AppService/apiapps/newapiapp/providers/Microsoft.Resources/links/apiAppSite
	info:    Deployment complete with status: Succeeded
	info:    apiapp create command OK

#### Erstellen einer API-App über den Marketplace

Verwenden Sie zum Abrufen einer Liste der im Marketplace verfügbaren API-App-Pakete den Befehl `group template list`, und geben Sie die API-Apps mithilfe des Parameters für die Kategorie (`-c`) an.

	azure group template list -c apiapps

Die Ausgabe sieht wie folgt aus:

	info:    Executing command group template list
	info:    Listing gallery resource group templates
	data:    Publisher      Name                                                  
	data:    -------------  ------------------------------------------------------
	data:    Microsoft      Microsoft.Template.1.0.1                              
	data:    microsoft_com  microsoft_com.MicrosoftSharePointConnector.0.2.2      
	data:    microsoft_com  microsoft_com.FTPConnector.0.2.2                      
	info:    group template list command OK

Verwenden Sie zum Erstellen einer API-App über den Marketplace den Befehl `apiapap create`, und geben Sie den Namen der zu erstellenden API-App mithilfe des Parameters für das NuGet-Paket (`-u`) an.

	azure apiapp create -g <resource group name> -n <API app name> -p <app service plan name> -u <marketplace name>

Der mittlere Abschnitt des Marketplace-Namens stellt den Wert für den `-u`-Parameter dar. Für „microsoft\_com.MicrosoftSqlConnector.0.2.2“ sieht der Befehl beispielsweise wie folgt aus:

	azure apiapp create -g mygroup -n mysqlconnector -p myplan -u MicrosoftSqlConnector
	
Wenn alle API-App-Vorlagenparameter erforderlich sind, etwa Servernamen und Verbindungszeichenfolgen für SQL-Connector, werden Sie zur Eingabe der erforderlichen Daten aufgefordert. Sie können die Parameterwerte mithilfe von `--parameters` oder `--parameters-file` übergeben. Weitere Informationen zu Parameterdateien finden Sie unter [Bereitstellen einer API-App mit einem neuen Gateway](app-service-api-arm-new-gateway-provision.md).

## Nächste Schritte

In diesem Artikel wurde erläutert, wie Sie unter Verwendung einzelner Azure CLI-Befehle mit benutzerdefinierten API-Apps oder über Marketplace-Vorlagen erstellten API-Apps arbeiten. Informationen zur Verwendung benutzerdefinierter Vorlagen zum Automatisieren der API-App-Erstellung finden Sie in folgenden Ressourcen:

* [Bereitstellen einer API-App mit einem vorhandenen Gateway](app-service-api-arm-existing-gateway-provision.md)
* [Bereitstellen einer API-App mit einem neuen Gateway](app-service-api-arm-new-gateway-provision.md)

Weitere Informationen zur Verwendung von Azure-Befehlszeilenprogrammen mit dem Azure-Ressourcen-Manager finden Sie in folgenden Ressourcen:
 
* [Verwenden der Azure-Befehlszeilenschnittstelle für Mac, Linux und Windows mit der Azure-Ressourcenverwaltung](../xplat-cli-azure-resource-manager.md)
* [Verwenden von Windows PowerShell mit dem Azure-Ressourcen-Manager](../powershell-azure-resource-manager.md)
 

<!---HONumber=AcomDC_0114_2016-->