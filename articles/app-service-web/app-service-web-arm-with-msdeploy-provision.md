<properties
	pageTitle="Bereitstellen einer Web-App per MSDeploy mit Hostname und SSL-Zertifikat"
	description="Verwenden einer Azure-Ressourcen-Manager-Vorlage zum Bereitstellen einer Web-App mit MSDeploy und Festlegen eines benutzerdefinierten Hostnamens und eines SSL-Zertifikats"
	services="app-service\web"
	documentationCenter=""
	authors="jodehavi"
	/>

<tags
	ms.service="app-service-web"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/26/2016"
	ms.author="john.dehavilland"/>

# Bereitstellen einer Web-App mit MSDeploy, benutzerdefinierten Hostnamen und SSL-Zertifikat

In diesem Leitfaden werden Sie durch das Erstellen einer End-to-End-Bereitstellung für eine Azure-Web-App, das Verwenden von MSDeploy und das Hinzufügen eines benutzerdefinierten Hostnamens sowie eines SSL-Zertifikats zur ARM-Vorlage geführt.

Weitere Informationen zum Erstellen von Vorlagen finden Sie unter [Erstellen von Azure-Ressourcen-Manager-Vorlagen](../resource-group-authoring-templates.md).

###Erstellen der Beispielanwendung

Sie stellen eine ASP.NET-Webanwendung bereit. Der erste Schritt ist die Erstellung einer einfachen Webanwendung. (Sie können auch eine vorhandene nutzen und diesen Schritt überspringen.)

Öffnen Sie Visual Studio 2015, und wählen Sie „Datei“ > „Neues Projekt“. Wählen Sie im angezeigten Dialogfeld die Option „Web“ > „ASP.NET-Webanwendung“. Wählen Sie unter „Vorlagen“ die Option „Web“ und dann die MVC-Vorlage. Legen Sie _Authentifizierungsart ändern_ auf _Keine Authentifizierung_ fest. Das Ziel hierbei ist, die Beispielanwendung so einfach wie möglich zu gestalten.

An diesem Punkt verfügen Sie über eine grundlegende ASP.Net-Web-App, die für die Verwendung im Rahmen Ihres Bereitstellungsprozesses bereit ist.

###Erstellen des MSDeploy-Pakets

Im nächsten Schritt erstellen Sie das Paket zum Bereitstellen der Web-App für Azure. Speichern Sie hierzu Ihr Projekt, und führen Sie dann Folgendes über die Befehlszeile aus:

	msbuild yourwebapp.csproj /t:Package /p:PackageLocation="path\to\package.zip"

Im Ordner „PackageLocation“ wird ein gezipptes Paket erstellt. Die Anwendung kann jetzt bereitgestellt werden. Hierfür können Sie eine Azure-Ressourcen-Manager-Vorlage erstellen.

###Erstellen einer ARM-Vorlage
Zunächst beginnen wir mit einer grundlegenden ARM-Vorlage, mit der eine Webanwendung und ein Hostingplan erstellt werden (Parameter und Variablen werden aus Platzgründen nicht angezeigt).

	{
		"name": "[parameters('appServicePlanName')]",
		"type": "Microsoft.Web/serverfarms",
		"location": "[resourceGroup().location]",
		"apiVersion": "2014-06-01",
		"dependsOn": [ ],
		"tags": {
		    "displayName": "appServicePlan"
		},
		"properties": {
		    "name": "[parameters('appServicePlanName')]",
		    "sku": "[parameters('appServicePlanSKU')]",
		    "workerSize": "[parameters('appServicePlanWorkerSize')]",
		    "numberOfWorkers": 1
		}
	},
	{
		"name": "[variables('webAppName')]",
		"type": "Microsoft.Web/sites",
		"location": "[resourceGroup().location]",
		"apiVersion": "2015-08-01",
		"dependsOn": [
		    "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
		],
		"tags": {
		    "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
		    "displayName": "webApp"
		},
		"properties": {
		    "name": "[variables('webAppName')]",
		    "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
		}
	}

Als Nächstes müssen Sie die Web-App-Ressource ändern, damit eine geschachtelte MSDeploy-Ressource genutzt werden kann. Hiermit können Sie auf das bereits erstellte Paket verweisen und für den Azure-Ressourcen-Manager die Verwendung von MSDeploy angeben, um das Paket für die Azure-Web-App bereitzustellen. Unten ist die Ressource „Microsoft.Web/sites“ mit der geschachtelten MSDeploy-Ressource dargestellt:

    {
        "name": "[variables('webAppName')]",
        "type": "Microsoft.Web/sites",
        "location": "[resourceGroup().location]",
        "apiVersion": "2015-08-01",
        "dependsOn": [
            "[concat('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        ],
        "tags": {
            "[concat('hidden-related:', resourceGroup().id, '/providers/Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]": "Resource",
            "displayName": "webApp"
        },
        "properties": {
            "name": "[variables('webAppName')]",
            "serverFarmId": "[resourceId('Microsoft.Web/serverfarms/', parameters('appServicePlanName'))]"
        },
        "resources": [
            {
                "name": "MSDeploy",
                "type": "extensions",
                "location": "[resourceGroup().location]",
                "apiVersion": "2015-08-01",
                "dependsOn": [
                    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
                ],
                "tags": {
                    "displayName": "webDeploy"
                },
                "properties": {
                    "packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]",
                    "dbType": "None",
                    "connectionString": "",
                    "setParameters": {
                        "IIS Web Application Name": "[variables('webAppName')]"
                    }
                }
            }
        ]
    }

Sie sehen, dass für die MSDeploy-Ressource eine **packageUri**-Eigenschaft verwendet wird, die wie folgt definiert ist:

	"packageUri": "[concat(parameters('_artifactsLocation'), '/', parameters('webDeployPackageFolder'), '/', parameters('webDeployPackageFileName'), parameters('_artifactsLocationSasToken'))]"

Für diese **packageUri** wird der Speicherkonto-URI verwendet, der auf das Speicherkonto verweist, in das Sie das gezippte Paket hochladen. Der Azure-Ressourcen-Manager nutzt [Shared Access Signatures](../storage/storage-dotnet-shared-access-signature-part-1.md) zum lokalen Abrufen des Pakets aus dem Speicherkonto, wenn Sie die Vorlage bereitstellen. Dieser Prozess ist per PowerShell-Skript automatisiert, mit dem das Paket hochgeladen und die API für die Azure-Verwaltung aufgerufen wird, um die erforderlichen Schlüssel zu erstellen und als Parameter an die Vorlage zu übergeben (*\_artifactsLocation* und *\_artifactsLocationSasToken*). Sie müssen Parameter für den Ordner und den Dateinamen des Speicherorts definieren, an das das Paket im Speichercontainer hochgeladen wird.

Als Nächstes müssen Sie eine weitere geschachtelte Ressource hinzufügen, um die Hostnamenbindungen zum Verwenden einer benutzerdefinierten Domäne einzurichten. Zuerst müssen Sie sicherstellen, dass Sie im Besitz des Hostnamens sind, und diesen so einrichten, dass dies von Azure überprüft werden kann. Weitere Informationen finden Sie unter [Konfigurieren eines benutzerdefinierten Domänennamens in Azure App Service](web-sites-custom-domain-name.md). Danach können Sie der Vorlage im Abschnitt mit der Ressource „Microsoft.Web/sites“ Folgendes hinzufügen:

	{
		"apiVersion": "2015-08-01",
		"type": "hostNameBindings",
		"name": "www.yourcustomdomain.com",
		"dependsOn": [
		    "[concat('Microsoft.Web/sites/', variables('webAppName'))]"
		],
		"properties": {
		    "domainId": null,
		    "hostNameType": "Verified",
		    "siteName": "variables('webAppName')"
		}
	}

Im letzten Schritt müssen Sie eine weitere Ressource der obersten Ebene hinzufügen: Microsoft.Web/certificates. Diese Ressource enthält das SSL-Zertifikat und befindet sich auf derselben Ebene wie Ihre Web-App und der Hostingplan.

	{
	    "name": "[parameters('certificateName')]",
	    "apiVersion": "2014-04-01",
	    "type": "Microsoft.Web/certificates",
	    "location": "[resourceGroup().location]",
	    "properties": {
	        "pfxBlob": "pfx base64 blob",
	        "password": "some pass"
	    }
	}

Sie müssen über ein gültiges SSL-Zertifikat verfügen, um diese Ressource einzurichten. Nachdem Sie im Besitz des gültigen Zertifikats sind, müssen Sie die pfx-Bytes als base64-Zeichenfolge extrahieren. Eine Möglichkeit zum Extrahieren dieser Daten ist die Verwendung des folgenden PowerShell-Befehls:

	$fileContentBytes = get-content 'C:\path\to\cert.pfx' -Encoding Byte

	[System.Convert]::ToBase64String($fileContentBytes) | Out-File 'pfx-bytes.txt'

Sie können dies dann als Parameter an Ihre ARM-Bereitstellungsvorlage übergeben.

Die ARM-Vorlage ist jetzt fertig.

###Vorlage bereitstellen

Mit den abschließenden Schritten werden alle Elemente zu einer vollständigen End-to-End-Bereitstellung zusammengefasst. Um die Bereitstellung einfacher zu gestalten, können Sie das PowerShell-Skript **Deploy-AzureResourceGroup.ps1** nutzen, das beim Erstellen eines Azure-Ressourcengruppenprojekts in Visual Studio erstellt wird. Es ist hilfreich für das Hochladen von erforderlichen Artefakten für die Vorlage. Hierfür müssen Sie vorher ein Speicherkonto erstellt haben, das Sie verwenden möchten. In diesem Beispiel habe ich ein freigegebenes Speicherkonto erstellt, in das „package.zip“ hochgeladen werden kann. Im Skript wird AzCopy verwendet, um das Paket in das Speicherkonto hochzuladen. Sie übergeben den Speicherort Ihres Artefaktordners. Über das Skript werden dann automatisch alle Dateien in diesem Verzeichnis in den benannten Speichercontainer hochgeladen. Nach dem Aufrufen von „Deploy-AzureResourceGroup.ps1“ müssen Sie die SSL-Bindungen aktualisieren, um den benutzerdefinierten Hostnamen Ihrem SSL-Zertifikat zuzuordnen.

Der folgende PowerShell-Code zeigt die vollständige Bereitstellung mit dem Aufruf von „Deploy-AzureResourceGroup.ps1“:

	#Set resource group name
	$rgName = "Name-of-resource-group"

	#call deploy-azureresourcegroup script to deploy web app

	.\Deploy-AzureResourceGroup.ps1 -ResourceGroupLocation "East US" `
									-ResourceGroupName $rgName `
									-UploadArtifacts "container-name" `
									-StorageAccountName "name-of-storage-acct-for-package" `
									-StorageAccountResourceGroupName "resource-group-name-storage-acct" `
									-TemplateFile "web-app-deploy.json" `
									-TemplateParametersFile "web-app-deploy-parameters.json" `
									-ArtifactStagingDirectory "C:\path\to\packagefolder"

	#update web app to bind ssl certificate to hostname. This has to be done after creation above.

	$cert = Get-PfxCertificate -FilePath C:\path\to\certificate.pfx

	$ar = Get-AzureRmResource -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -ApiVersion 2014-11-01

	$props = $ar.Properties

	$props.HostNameSslStates[2].'SslState' = 1
	$props.HostNameSslStates[2].'thumbprint' = $cert.Thumbprint
	$props.hostNameSslStates[2].'toUpdate' = $true

	Set-AzureRmResource -ApiVersion 2014-11-01 -Name nameofwebsite -ResourceGroupName $rgName -ResourceType Microsoft.Web/sites -PropertyObject $props

An diesem Punkt sollte Ihre Anwendung bereitgestellt werden, und Sie sollten über https://www.yourcustomdomain.com darauf zugreifen können.

<!---HONumber=AcomDC_0302_2016-->