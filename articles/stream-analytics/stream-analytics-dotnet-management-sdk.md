<properties 
	pageTitle="Erfahren Sie, wie Sie das Stream Analytics Management .NET SDK verwenden | Microsoft Azure" 
	description="Führen Sie erste Schritte mit dem Stream Analytics Management .NET SDK durch. Erfahren Sie, wie Sie Analyseaufträge einrichten und ausführen: Erstellen Sie ein Projekt, Eingaben, Ausgaben und Transformationen." 
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
	ms.date="08/04/2015" 
	ms.author="jeffstok"/>


# Einrichten und Ausführen von Analyseaufträgen mit dem Azure Stream Analytics Management .NET SDK

Erfahren Sie, wie Sie mit dem Azure Stream Analytics Management .NET SDK Analyseaufträge einrichten und ausführen. Richten Sie ein Projekt ein, erstellen Sie Eingabe- und Ausgabequellen sowie Transformationen, und starten und beenden Sie Aufträge. Für Ihre Analyseaufträge können Sie Daten aus dem Blob-Speicher oder einem Event Hub streamen.

Azure Stream Analytics ist ein vollständig verwalteter Dienst, der eine geringe Latenz, Hochverfügbarkeit und eine skalierbare komplexe Ereignisverarbeitung durch das Streaming von Daten in der Cloud bietet. Stream Analytics ermöglicht Kunden, Streaming-Aufträge zur Analyse von Datenströmen einzurichten und Analysen nahezu in Echtzeit durchzuführen.

Informationen zur .NET API-Referenz finden Sie unter [Stream Analytics Management .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).


## Voraussetzungen
Bevor Sie mit diesem Artikel beginnen können, benötigen Sie Folgendes:

- Installieren Sie Visual Studio 2012 oder 2013.
- Laden Sie das [Azure .NET SDK](http://azure.microsoft.com/downloads/) herunter, und installieren Sie es. 
- Erstellen Sie in Ihrem Abonnement eine Azure-Ressourcengruppe. Nachfolgend ist ein Azure PowerShell-Beispielskript aufgeführt. Informationen zu Azure PowerShell finden Sie unter [Installieren und Konfigurieren von Azure PowerShell](../install-configure-powershell.md).  


		# Configure the Azure PowerShell session to access Azure Resource Manager
		Switch-AzureMode AzureResourceManager

		# Log in to your Azure account
		Add-AzureAccount

		# Select the Azure subscription you want to use to create the resource group
		Select-AzureSubscription -SubscriptionName <subscription name>

		# Create an Azure resource group	
		New-AzureResourceGroup -Name <YOUR RESORUCE GROUP NAME> -Location <LOCATION>


-	Richten Sie eine Eingabequelle und ein Ausgabeziel ein. Informationen zum Einrichten einer Beispieleingabe und/oder -ausgabe zur Verwendung durch diesen Artikel finden Sie unter [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md).


## Einrichten eines Projekts

Um einen Analyseauftrag zu erstellen, müssen Sie zunächst das Projekt einrichten.

1. Erstellen Sie eine Visual Studio C# .NET-Konsolenanwendung.
2. Führen Sie in der Paket-Manager-Konsole die folgenden Befehle zum Installieren der NuGet-Pakete aus. Das erste ist das Azure Stream Analytics Management .NET SDK. Das zweite ist der Azure Active Directory-Client, der für die Authentifizierung verwendet wird.

		Install-Package Microsoft.Azure.Management.StreamAnalytics
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory

4. Fügen Sie der Datei "App.config" den folgenden **appSettings**-Abschnitt hinzu.

		<appSettings>
		  <!--CSM Prod related values-->
		  <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		  <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		  <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		  <add key="AsaClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		  <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		  <add key="SubscriptionId" value="YOUR AZURE SUBSCRIPTION" />
		  <add key="ActiveDirectoryTenantId" value="YOU TENANT ID" />
		</appSettings>


	Ersetzen Sie die Werte für **SubscriptionId** und **ActiveDirectoryTenantId** durch Ihre Abonnement- und Mandanten-ID für Azure. Sie können diese Werte durch Ausführen des folgenden Azure PowerShell-Cmdlets abrufen:

		Get-AzureAccount

5. Fügen Sie die folgenden **using**-Anweisungen zur Quelldatei (Program.cs) im Projekt hinzu.

		using System;
		using System.Configuration;
		using System.Threading;
		using Microsoft.Azure;
		using Microsoft.Azure.Management.StreamAnalytics;
		using Microsoft.Azure.Management.StreamAnalytics.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;

6.	Fügen Sie eine Authentifizierungshilfsmethode hinzu:

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


## Erstellen eines Stream Analytics-Verwaltungsclients

Mithilfe eines **StreamAnalyticsManagementClient**-Objekts können Sie den Auftrag und die Auftragskomponenten wie Eingabe, Ausgabe und Transformation verwalten.

Fügen Sie den folgenden Code am Anfang der Methode **Main** hinzu:

	string resourceGroupName = "<YOUR AZURE RESOURCE GROUP NAME>";
	string streamAnalyticsJobName = "<YOUR STREAM ANALYTICS JOB NAME>";
	string streamAnalyticsInputName = "<YOUR JOB INPUT NAME>";
	string streamAnalyticsOutputName = "<YOUR JOB OUTPUT NAME>";
	string streamAnalyticsTransformationName = "<YOUR JOB TRANSFORMATION NAME>";
	
	// Get authentication token
	TokenCloudCredentials aadTokenCredentials =
	    new TokenCloudCredentials(
	        ConfigurationManager.AppSettings["SubscriptionId"],
	        GetAuthorizationHeader());
	
	// Create Stream Analytics management client
	StreamAnalyticsManagementClient client = new StreamAnalyticsManagementClient(aadTokenCredentials);

Der Wert der Variablen**resourceGroupName** sollte mit dem Namen der Ressourcengruppe übereinstimmen, die Sie in den Vorbereitungsschritten erstellt oder ausgewählt haben.

In den verbleibenden Abschnitten dieses Artikels wird davon ausgegangen, dass dieser Code am Anfang der Methode**Main** enthalten ist.

## Erstellen eines Stream Analytics-Auftrags

Der folgende Code erstellt einen Stream Analytics-Auftrag unter der Ressourcengruppe, die Sie definiert haben. Sie fügen dem Auftrag später eine Eingabe, Ausgabe und Transformation hinzu.

	// Create a Stream Analytics job
	JobCreateOrUpdateParameters jobCreateParameters = new JobCreateOrUpdateParameters()
	{
	    Job = new Job()
	    {
	        Name = streamAnalyticsJobName,
	        Location = "<LOCATION>",
	        Properties = new JobProperties()
	        {
	            EventsOutOfOrderPolicy = EventsOutOfOrderPolicy.Adjust,
	            Sku = new Sku()
	            {
	                Name = "Standard"
	            }
	        }
	    }
	};
	
	JobCreateOrUpdateResponse jobCreateResponse = client.StreamingJobs.CreateOrUpdate(resourceGroupName, jobCreateParameters);


## Erstellen einer Stream Analytics-Eingabequelle

Der folgende Code erstellt eine Stream Analytics-Eingabequelle mit dem Blob-Eingabequellentyp und mit CSV-Serialisierung. Verwenden Sie zum Erstellen einer Event Hub-Eingabequelle **EventHubStreamInputDataSource** anstelle von **BlobStreamInputDataSource**. In ähnlicher Weise können Sie den Serialisierungstyp der Eingabequelle anpassen.

	// Create a Stream Analytics input source
	InputCreateOrUpdateParameters jobInputCreateParameters = new InputCreateOrUpdateParameters()
	{
	    Input = new Input()
	    {
	        Name = streamAnalyticsInputName,
	        Properties = new StreamInputProperties()
	        {
	            Serialization = new CsvSerialization
	            {
	                Properties = new CsvSerializationProperties
	                {
	                    Encoding = "UTF8",
	                    FieldDelimiter = ","
	                }
	            },
	            DataSource = new BlobStreamInputDataSource
	            {
	                Properties = new BlobStreamInputDataSourceProperties
	                {
	                    StorageAccounts = new StorageAccount[]
	                    {
	                        new StorageAccount()
	                        {
	                            AccountName = "<YOUR STORAGE ACCOUNT NAME>",
	                            AccountKey = "<YOUR STORAGE ACCOUNT KEY>"
	                        }
	                    },
	                    Container = "samples",
	                    PathPattern = ""
	                }
	            }
	        }
	    }
	};
	
	InputCreateOrUpdateResponse inputCreateResponse = 
		client.Inputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobInputCreateParameters);

Eingabequellen, gleichgültig ob vom Blob-Speicher oder einem Event Hub, sind an einen bestimmten Auftrag gebunden. Um die gleiche Eingabequelle für verschiedene Aufträge zu verwenden, müssen Sie die Methode erneut aufrufen und einen anderen Auftragsnamen angeben.


## Testen einer Stream Analytics-Eingabequelle

Die Methode **TestConnection** überprüft, ob der Stream Analytics-Auftrag sich mit der Eingabequelle verbinden kann. Ferner werden andere Aspekte getestet, die für der Eingabequelle spezifisch sind. Beispielsweise überprüft die Methode in der Blob-Eingabequelle, die Sie in einem früheren Schritt erstellt haben, ob das Paar aus dem Namen des Storage-Kontos und dem Schlüssel verwendet werden kann, um eine Verbindung mit dem Storage-Konto herzustellen. Zudem wird überprüft, ob der angegebene Container vorhanden ist.

	// Test input source connection
	DataSourceTestConnectionResponse inputTestResponse = 
		client.Inputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsInputName);

## Erstellen eines Stream Analytics-Ausgabeziels

Das Erstellen eines Ausgabeziels ähnelt stark dem Erstellen einer Stream Analytics-Eingabequelle. Genau wie Eingabequellen sind Ausgabeziele an einen bestimmten Auftrag gebunden. Um dasselbe Ausgabeziel für verschiedene Aufträge zu verwenden, müssen Sie die Methode erneut aufrufen und einen anderen Auftragsnamen angeben.

Der folgende Code erstellt ein Ausgabeziel (Azure SQL-Datenbank). Sie können den Datentyp des Ausgabeziels und/oder den Serialisierungstyp anpassen.

	// Create a Stream Analytics output target
	OutputCreateOrUpdateParameters jobOutputCreateParameters = new OutputCreateOrUpdateParameters()
	{
	    Output = new Output()
	    {
	        Name = streamAnalyticsOutputName,
	        Properties = new OutputProperties()
	        {
	            DataSource = new SqlAzureOutputDataSource()
	            {
	                Properties = new SqlAzureOutputDataSourceProperties()
	                {
	                    Server = "<YOUR DATABASE SERVER NAME>",
	                    Database = "<YOUR DATABASE NAME>",
	                    User = "<YOUR DATABASE LOGIN>",
	                    Password = "<YOUR DATABASE LOGIN PASSWORD>",
	                    Table = "<YOUR DATABASE TABLE NAME>"
	                }
	            }
	        }
	    }
	};
	
	OutputCreateOrUpdateResponse outputCreateResponse = 
		client.Outputs.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, jobOutputCreateParameters);

## Testen eines Stream Analytics-Ausgabeziels

Ein Stream Analytics-Ausgabeziel verfügt auch über die Methode **TestConnection** zum Testen von Verbindungen.

	// Test output target connection
	DataSourceTestConnectionResponse outputTestResponse = 
		client.Outputs.TestConnection(resourceGroupName, streamAnalyticsJobName, streamAnalyticsOutputName);

## Erstellen einer Stream Analytics-Transformation

Der folgende Code erstellt eine Stream Analytics-Transformation mit der Abfrage "select * from Input" und gibt an, dass eine Streamingeinheit für den Stream Analytics-Auftrag zuordnet werden soll. Weitere Informationen zum Anpassen von Streamingeinheiten finden Sie unter [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md).


	// Create a Stream Analytics transformation
	TransformationCreateOrUpdateParameters transformationCreateParameters = new TransformationCreateOrUpdateParameters()
	{
	    Transformation = new Transformation()
	    {
	        Name = streamAnalyticsTransformationName,
	        Properties = new TransformationProperties()
	        {
	            StreamingUnits = 1,
	            Query = "select * from Input"
	        }
	    }
	};
	
	var transformationCreateResp = 
		client.Transformations.CreateOrUpdate(resourceGroupName, streamAnalyticsJobName, transformationCreateParameters);

Wie Eingabe und Ausgabe ist auch eine Transformation an den jeweiligen Streaming Analytics-Auftrag gebunden, unter dem Sie erstellt wurde.

## Starten eines Stream Analytics-Auftrags
Nach dem Erstellen eines Stream Analytics-Auftrags und seiner Eingabe(n), Ausgabe(n) und Transformation können Sie den Auftrag durch Aufrufen der Methode **Start** starten.

Der folgende Beispielcode startet einen Stream Analytics-Auftrag mit der benutzerdefinierten Ausgabestartzeit "12. Dezember 2012, 12:12:12 UTC":

	// Start a Stream Analytics job
	JobStartParameters jobStartParameters = new JobStartParameters
	{
	    OutputStartMode = OutputStartMode.CustomTime,
	    OutputStartTime = new DateTime(2012, 12, 12, 0, 0, 0, DateTimeKind.Utc)
	};
	
	LongRunningOperationResponse jobStartResponse = client.StreamingJobs.Start(resourceGroupName, streamAnalyticsJobName, jobStartParameters);



## Beenden eines Stream Analytics-Auftrags
Sie können einen laufenden Stream Analytics-Auftrag beenden, indem Sie die Methode **Stop** aufrufen.

	// Stop a Stream Analytics job
	LongRunningOperationResponse jobStopResponse = client.StreamingJobs.Stop(resourceGroupName, streamAnalyticsJobName);

## Löschen eines Stream Analytics-Auftrags
Die Methode **Delete** löscht den Auftrag sowie die zugrunde liegenden Unterressourcen, einschließlich Eingabe(n), Ausgabe(n) und Transformation des Auftrags.

	// Delete a Stream Analytics job
	LongRunningOperationResponse jobDeleteResponse = client.StreamingJobs.Delete(resourceGroupName, streamAnalyticsJobName);


## Support
Um Hilfe zu erhalten, nutzen Sie unser [Azure Stream Analytics-Forum](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).


## Nächste Schritte

Sie haben nun die Grundlagen der Verwendung eines .NET SDK zum Erstellen und Ausführen von Analyseaufträgen kennengelernt. Weitere Informationen finden Sie in den folgenden Artikeln:

- [Einführung in Azure Stream Analytics](stream-analytics-introduction.md)
- [Erste Schritte mit Azure Stream Analytics](stream-analytics-get-started.md)
- [Skalieren von Azure Stream Analytics-Aufträgen](stream-analytics-scale-jobs.md)
- [Verwenden des Azure Stream Analytics Management .NET SDK](https://msdn.microsoft.com/library/azure/dn889315.aspx).
- [Stream Analytics Query Language Reference (in englischer Sprache)](https://msdn.microsoft.com/library/azure/dn834998.aspx)
- [Referenz zur Azure Stream Analytics-Verwaltungs-REST-API](https://msdn.microsoft.com/library/azure/dn835031.aspx)


<!--Image references-->
[5]: ./media/markdown-template-for-new-articles/octocats.png
[6]: ./media/markdown-template-for-new-articles/pretty49.png
[7]: ./media/markdown-template-for-new-articles/channel-9.png


<!--Link references-->
[azure.blob.storage]: http://azure.microsoft.com/documentation/services/storage/
[azure.blob.storage.use]: http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/

[azure.event.hubs]: http://azure.microsoft.com/services/event-hubs/
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.forum]: http://go.microsoft.com/fwlink/?LinkId=512151

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-get-started.md
[stream.analytics.developer.guide]: ../stream-analytics-developer-guide.md
[stream.analytics.scale.jobs]: stream-analytics-scale-jobs.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301
 

<!---HONumber=August15_HO6-->