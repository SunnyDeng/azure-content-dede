<properties 
	pageTitle="Programmgesteuertes Überwachen von Stream Analytics-Aufträgen | Microsoft Azure" 
	description="Erfahren Sie, wie Sie Stream Analytics-Aufträge, die mit REST-APIs, Azure SDK oder PowerShell erstellt wurden, programmgesteuert überwachen können."
	keywords=".NET-Überwachung, Auftragsüberwachung, Überwachungs-App"
	services="stream-analytics" 
	documentationCenter="" 
	authors="jeffstokes72" 
	manager="paulettm" 
	editor="cgronlun"/>

<tags 
	ms.service="stream-analytics" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="data-services" 
	ms.date="02/04/2016" 
	ms.author="jeffstok"/>


# Programmgesteuertes Überwachen von Stream Analytics-Aufträgen
 Dieser Artikel erläutert, wie die Überwachung für einen Stream Analytics-Auftrag aktiviert wird. Für Stream Analytics-Aufträge, die mit REST-APIs, Azure SDK oder PowerShell erstellt wurden, ist in der Standardeinstellung keine Überwachung aktiviert. Sie können diese manuell im Azure-Portal aktivieren, indem Sie zur Überwachungsseite des Auftrags wechseln und auf die Schaltfläche "Aktivieren" klicken. Sie können diesen Prozess automatisieren, indem Sie die Schritte in diesem Artikel befolgen. Die Überwachungsdaten werden im Azure-Portal auf der Registerkarte "Überwachung" für den Stream Analytics-Auftrag angezeigt. 

![Registerkarte „Aufträge überwachen“](./media/stream-analytics-monitor-jobs/stream-analytics-monitor-jobs-tab.png)

## Voraussetzungen
Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- Visual Studio 2012 oder 2013.
- Laden Sie das [Azure .NET SDK](https://azure.microsoft.com/downloads/) herunter, und installieren Sie es.
- Ein vorhandener Stream Analytics-Auftrag, für den die Überwachung aktiviert werden muss.

## Einrichten eines Projekts

1.	Erstellen Sie eine Visual Studio C# .NET-Konsolenanwendung.
2.	Führen Sie in der Paket-Manager-Konsole die folgenden Befehle zum Installieren der NuGet-Pakete aus. Das erste ist das Azure Stream Analytics Management .NET SDK. Das zweite ist das Azure Insights SDK, mit dem die Überwachung aktiviert wird. Das letzte ist der Azure Active Directory-Client, der für die Authentifizierung verwendet wird.

    ```
    Install-Package Microsoft.Azure.Management.StreamAnalytics
    Install-Package Microsoft.Azure.Insights -Pre
    Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
    ```

3.	Fügen Sie der Datei "App.config" den folgenden appSettings-Abschnitt hinzu.

    ```
    <appSettings>
    	<!--CSM Prod related values-->
    	<add key="ResourceGroupName" value="RESOURCE GROUP NAME" />
    	<add key="JobName" value="YOUR JOB NAME" />
    	<add key="StorageAccountName" value="YOUR STORAGE ACCOUNT"/>
    	<add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
    	<add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
    	<add key="WindowsManagementUri" value="https://management.core.windows.net/" />
    	<add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
    	<add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
    	<add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION ID" />
    	<add key="ActiveDirectoryTenantId" value="YOUR TENANT ID" />
    </appSettings>
	```
Ersetzen Sie die Werte für *SubscriptionId* und *ActiveDirectoryTenantId* durch Ihre Abonnement- und Mandanten-ID für Azure. Sie können diese Werte durch Ausführen des folgenden PowerShell-Cmdlets abrufen:

    ```
    Get-AzureAccount
    ```
4.	Fügen Sie die folgenden using-Anweisungen zur Quelldatei (Program.cs) im Projekt hinzu. 

    ```
        using System;
        using System.Configuration;
        using System.Threading;
        using Microsoft.Azure;
        using Microsoft.Azure.Management.Insights;
        using Microsoft.Azure.Management.Insights.Models;
        using Microsoft.Azure.Management.StreamAnalytics;
        using Microsoft.Azure.Management.StreamAnalytics.Models;
        using Microsoft.IdentityModel.Clients.ActiveDirectory;
    ```
5.	Fügen Sie eine Authentifizierungshilfsmethode hinzu.

        public static string GetAuthorizationHeader()
        	{
        		AuthenticationResult result = null;
        		var thread = new Thread(() =>
        		{
        			try
        			{
            			var context = new AuthenticationContext(
                			ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] +
                			ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

            			result = context.AcquireToken(
                			resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                			clientId: ConfigurationManager.AppSettings["AsaClientId"],
                			redirectUri: new Uri(ConfigurationManager.AppSettings["RedirectUri"]),
                			promptBehavior: PromptBehavior.Always);
        			}
        			catch (Exception threadEx)
        			{
            			Console.WriteLine(threadEx.Message);
        			}
    			});

    			thread.SetApartmentState(ApartmentState.STA);
    			thread.Name = "AcquireTokenThread";
    			thread.Start();
    			thread.Join();

    			if (result != null)
    			{
        			return result.AccessToken;
    			}

    			throw new InvalidOperationException("Failed to acquire token");
        }

## Erstellen von Verwaltungsclients
Mit dem folgenden Code werden die erforderlichen Variablen und Verwaltungsclients eingerichtet.

    string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
    string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";

    // Get authentication token
    TokenCloudCredentials aadTokenCredentials =
    	new TokenCloudCredentials(
    		ConfigurationManager.AppSettings["SubscriptionId"],
    		GetAuthorizationHeader());

    Uri resourceManagerUri = new
    Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

    // Create Stream Analytics and Insights management client
    StreamAnalyticsManagementClient streamAnalyticsClient = new
    StreamAnalyticsManagementClient(aadTokenCredentials, resourceManagerUri);
    InsightsManagementClient insightsClient = new
    InsightsManagementClient(aadTokenCredentials, resourceManagerUri);

## Aktivieren der Überwachung für einen vorhandenen Stream Analytics-Auftrag

Mit dem folgenden Code wird die Überwachung für einen **vorhandenen** Stream Analytics-Auftrag aktiviert. Der erste Teil des Codes führt eine GET-Anforderung an den Stream Analytics-Dienst aus, um Daten zum jeweiligen Stream Analytics-Auftrag abzurufen. Hierbei wird die (mit der GET-Anforderung abgerufene) "Id"-Eigenschaft als Parameter für die Put-Methode in der zweiten Hälfte des Codes verwendet, die eine PUT-Anforderung an den Insights-Dienst sendet, um die Überwachung des Stream Analytics-Auftrags zu aktivieren.

> [AZURE.WARNING]
Wenn Sie die Überwachung bereits für einen anderen Stream Analytics-Auftrag aktiviert haben, entweder über das Azure-Porta oder programmgesteuert über den folgenden Code, **wird empfohlen, dass Sie den gleichen Speicherkontonamen angeben wie bei der zuvor aktivierten Überwachung.**
> 
> Das Speicherkonto ist mit der Region verknüpft, in der Sie Ihren Stream Analytics-Auftrag erstellt haben, und nicht mit dem Auftrag selbst.
> 
> Alle Stream Analytics-Aufträge (und alle anderen Azure-Ressourcen) in derselben Region nutzen dieses Speicherkonto gemeinsam, um Überwachungsdaten zu speichern. Wenn Sie ein anderes Speicherkonto angeben, führt dies möglicherweise zu unbeabsichtigten Nebenwirkungen auf die Überwachung Ihrer anderen Stream Analytics-Aufträge und/oder anderen Azure-Ressourcen.
> 
> Der Speicherkontoname, der zum Ersetzen von ```“<YOUR STORAGE ACCOUNT NAME>”``` unten verwendet wird, sollte sich auf ein Speicherkonto beziehen, das zum gleichen Abonnement gehört wie der Stream Analytics-Auftrag, für den Sie die Überwachung aktivieren.

    // Get an existing Stream Analytics job
    JobGetParameters jobGetParameters = new JobGetParameters()
    {
    	PropertiesToExpand = "inputs,transformation,outputs"
    };
    JobGetResponse jobGetResponse = streamAnalyticsClient.StreamingJobs.Get(resourceGroupName, streamAnalyticsJobName, jobGetParameters);

    // Enable monitoring
    ServiceDiagnosticSettingsPutParameters insightPutParameters = new ServiceDiagnosticSettingsPutParameters()
    {
    		Properties = new ServiceDiagnosticSettings()
    		{
        		StorageAccountName = "<YOUR STORAGE ACCOUNT NAME>"
    		}
    };
    insightsClient.ServiceDiagnosticSettingsOperations.Put(jobGetResponse.Job.Id, insightPutParameters);



## Support
Um Hilfe zu erhalten, nutzen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/de-DE/home?forum=AzureStreamAnalytics).


## Nächste Schritte

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)
 

<!---HONumber=AcomDC_0204_2016-->
