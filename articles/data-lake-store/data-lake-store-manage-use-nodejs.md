<properties 
   pageTitle="Verwalten von Azure Data Lake-Speicher mithilfe des Azure SDK für Node.js | Azure" 
   description="Erfahren Sie mehr über das Verwalten von Data Lake-Speicher und dem Dateisystem." 
   services="data-lake-store" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-store"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/11/2015"
   ms.author="jgao"/>

# Verwalten von Azure Data Lake-Speicher mithilfe des Azure SDK für Node.js

> [AZURE.SELECTOR]
- [Using Portal](data-lake-store-get-started-portal.md)
- [Using PowerShell](data-lake-store-get-started-powershell.md)
- [Using .NET SDK](data-lake-store-get-started-net-sdk.md)
- [Using Azure CLI](data-lake-store-get-started-cli.md)
- [Using Node.js](data-lake-store-manage-use-nodejs.md)


Das Azure ADK für Node.js kann für die Verwaltung von Azure Data Lake-Speicherkonten und des Dateisystems verwendet werden:

- Kontoverwaltung: Erstellen, Abrufen, Auflisten, Aktualisieren und Löschen.
- Dateisystemverwaltung: Erstellen, Abrufen, Hochladen, Anfügen, Herunterladen, Lesen, Löschen, Auflisten.

**Voraussetzungen**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
- **Ein Azure Data Lake-Speicherkonto**. Lesen Sie [Erste Schritte mit Azure Data Lake-Speicher mithilfe des Azure-Portals](data-lake-store-get-started-portal.md), um ein Konto zu erstellen.
- **Einen Dienstprinzipal mit Berechtigungen zum Zugriff auf das Data Lake Analytics-Konto**. Weitere Informationen finden Sie unter [Authentifizieren eines Dienstprinzipals mit dem Azure-Ressourcen-Manager](resource-group-authenticate-service-principal.md).

## Installieren des SDKs

Gehen Sie folgendermaßen vor, um das SDK zu installieren:

1. Installieren Sie [Node.js](https://nodejs.org/).
2. Führen Sie die folgenden Befehle an einer Eingabeaufforderung, im Terminal oder in einem Bash-Fenster aus:

		npm install async
		npm install adal-node
		npm install azure-common
		npm install azure-arm-datalake-store
	
## Ein Node.js-Beispiel

Im folgenden Beispiel wird eine Datei in einem Data Lake-Speicher erstellt und Daten an diese angefügt.

	var async = require('async');
	var adalNode = require('adal-node');
	var azureCommon = require('azure-common');
	var azureDataLakeStore = require('azure-arm-datalake-store');
	
	var resourceUri = 'https://management.core.windows.net/';
	var loginUri = 'https://login.windows.net/'
	
	var clientId = 'application_id_(guid)';
	var clientSecret = 'application_password';
	
	var tenantId = 'aad_tenant_id';
	var subscriptionId = 'azure_subscription_id';
	var resourceGroup = 'adls_resourcegroup_name';
	
	var accountName = 'adls_account_name';
	
	var context = new adalNode.AuthenticationContext(loginUri+tenantId);
	
	var client;
	var response;
	
	var destinationFilePath = '/newFileName.txt';
	var content = 'desired file contents';
	
	async.series([
		function (next) {
			context.acquireTokenWithClientCredentials(resourceUri, clientId, clientSecret, function(err, result){
				if (err) throw err;
				response = result;
				next();
			});
		},
		function (next) {
			var credentials = new azureCommon.TokenCloudCredentials({
				subscriptionId : subscriptionId,
				authorizationScheme : response.tokenType,
				token : response.accessToken
			});
		
			client = azureDataLakeStore.createDataLakeStoreFileSystemManagementClient(credentials, 'azuredatalakestore.net');
	
			next();
		},
		function (next) {
			client.fileSystem.directCreate(destinationFilePath, accountName, content, function(err, result){
				if (err) throw err;
			});
		}
	]);


##Weitere Informationen 

- [Azure SDK für Node.js](http://azure.github.io/azure-sdk-for-node/)
- [Verwalten von Azure Data Lake Analytics mithilfe von Node.js](data-lake-analytics-use-nodejs.md)

<!---HONumber=AcomDC_1217_2015-->