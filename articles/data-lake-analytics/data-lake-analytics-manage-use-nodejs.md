<properties 
   pageTitle="Verwalten von Azure Data Lake Analytics mithilfe des Azure SDK für Node.js | Azure" 
   description="Hier erfahren Sie, wie Sie mithilfe des Azure SDK für Node.js Data Lake Analytics-Konten, -Datenquellen, -Aufträge und -Benutzer verwalten." 
   services="data-lake-analytics" 
   documentationCenter="" 
   authors="mumian" 
   manager="paulettm" 
   editor="cgronlun"/>
 
<tags
   ms.service="data-lake-analytics"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data" 
   ms.date="12/11/2015"
   ms.author="jgao"/>

# Verwalten von Azure Data Lake Analytics mithilfe des Azure SDK für Node.js


[AZURE.INCLUDE [manage-selector](../../includes/data-lake-analytics-selector-manage.md)]

Das Azure ADK für Node.js kann für die Verwaltung von Azure Data Lake Analytics-Konten, Aufträgen und Katalogen verwendet werden. Wenn Sie das Verwaltungsthema für andere Tools anzeigen möchten, klicken Sie weiter oben auf die gewünschte Registerkarte.

**Voraussetzungen**

Bevor Sie mit diesem Lernprogramm beginnen können, benötigen Sie Folgendes:

- **Ein Azure-Abonnement**. Siehe [Kostenlose Azure-Testversion](https://azure.microsoft.com/pricing/free-trial/).
- **Ein Azure Data Lake Analytics-Konto**. Informationen zum Erstellen eines Kontos finden Sie unter [Erste Schritte mit Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-get-started-portal.md).
- **Einen Dienstprinzipal mit Berechtigungen zum Zugriff auf das Data Lake Analytics-Konto**. Weitere Informationen finden Sie unter [Authentifizieren eines Dienstprinzipals mit dem Azure-Ressourcen-Manager](resource-group-authenticate-service-principal.md).

## Installieren des SDKs

Gehen Sie folgendermaßen vor, um das SDK zu installieren:

1. Installieren Sie [Node.js](https://nodejs.org/).
2. Führen Sie die folgenden Befehle an einer Eingabeaufforderung, im Terminal oder in einem Bash-Fenster aus:

		npm install async
		npm install adal-node
		npm install azure-common
		npm install azure-arm-datalake-analytics
	
## Ein Node.js-Beispiel

Im folgenden Beispiel wird die Auftragsliste für ein bestimmtes Azure Data Lake Analytics-Konto abgerufen.

	var async = require('async');
	var adalNode = require('adal-node');
	var azureCommon = require('azure-common');
	var azureDataLakeAnalytics = require('azure-arm-datalake-analytics');
	
	var resourceUri = 'https://management.core.windows.net/';
	var loginUri = 'https://login.windows.net/'
	
	var clientId = 'application_id_(guid)';
	var clientSecret = 'application_password';
	
	var tenantId = 'aad_tenant_id';
	var subscriptionId = 'azure_subscription_id';
	var resourceGroup = 'adla_resourcegroup_name';
	
	var accountName = 'adla_account_name';
	
	var context = new adalNode.AuthenticationContext(loginUri+tenantId);
	
	var client;
	var response;
	
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
		
			client = azureDataLakeAnalytics.createDataLakeAnalyticsJobManagementClient(credentials, 'azuredatalakeanalytics.net');
	
			next();
		},
		function (next) {
			client.jobs.list(resourceGroup, accountName, function(err, result){
				if (err) throw err;
				console.log(result);
			});
		}
	]);


##Weitere Informationen 

- [Azure SDK für Node.js](http://azure.github.io/azure-sdk-for-node/)
- [Übersicht über Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
- [Erste Schritte mit Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-get-started-portal.md)
- [Verwalten von Azure Data Lake Analytics mithilfe des Azure-Portals](data-lake-analytics-use-portal.md)
- [Überwachen und Problembehandeln von Azure Data Lake Analytics-Aufträgen mithilfe des Azure-Portals](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)

<!---HONumber=AcomDC_1217_2015-->