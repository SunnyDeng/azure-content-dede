<properties 
	pageTitle="Ausführen eines U-SQL-Skripts auf Azure Data Lake Analytics in Azure Data Factory" 
	description="Erläutert die Datenverarbeitung durch Ausführen eines U-SQL-Skripts mit einem Azure Data Lake Analytics-Computedienst." 
	services="data-factory" 
	documentationCenter="" 
	authors="spelluru" 
	manager="jhubbard" 
	editor="monicar"/>

<tags 
	ms.service="data-factory" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/19/2016" 
	ms.author="spelluru"/>

# Ausführen eines U-SQL-Skripts auf Azure Data Lake Analytics in Azure Data Factory 
Eine Pipeline in einer Azure Data Factory verarbeitet Daten in verknüpften Speicherdiensten mithilfe verknüpfter Compute Services. Sie enthält eine Abfolge von Aktivitäten, wobei jede Aktivität einen bestimmten Verarbeitungsvorgang ausführt. In diesem Kapitel wird die **U-SQL-Aktivität von Data Lake Analytics** beschrieben, die ein **U-SQL**-Skript auf einem mit **Azure Data Lake Analytics** verknüpften Computedienst ausführt.

> [AZURE.NOTE] 
Vor dem Erstellen einer Pipeline mit U-SQL-Aktivität auf Data Lake Analytics muss ein Azure Data Lake Analytics-Konto erstellt werden. Weitere Informationen zu Azure Data Lake Analytics finden Sie unter [Erste Schritte mit Azure Data Lake Analytics](../data-lake-analytics/data-lake-analytics-get-started-portal.md).
>  
> Im Tutorial [Erstellen Ihrer ersten Pipeline](data-factory-build-your-first-pipeline.md) finden Sie ausführliche Anweisungen zum Erstellen von Data Factorys, verknüpften Diensten, Datasets und Pipelines. Verwenden Sie die JSON-Codeausschnitte mit Data Factory-Editor, Visual Studio oder Azure PowerShell, um die Data Factory-Entitäten zu erstellen.

## Mit Azure Data Lake Analytics verknüpfter Dienst
Sie erstellen einen mit **Azure Data Lake Analytics** verknüpften Dienst, um einen Azure Data Lake Analytics-Computedienst vor Verwendung der Data Lake Analytics-U-SQL-Aktivität mit einer Azure Data Factory zu verknüpfen.

Das folgende Beispiel enthält eine JSON-Definition für einen mit Azure Data Lake Analytics verknüpften Dienst.

	{
	    "name": "AzureDataLakeAnalyticsLinkedService",
	    "properties": {
	        "type": "AzureDataLakeAnalytics",
	        "typeProperties": {
	            "accountName": "adftestaccount",
	            "dataLakeAnalyticsUri": "datalakeanalyticscompute.net",
	            "authorization": "<authcode>",
				"sessionId": "<session ID>", 
	            "subscriptionId": "<subscription id>",
	            "resourceGroupName": "<resource group name>"
	        }
	    }
	}


Die folgende Tabelle enthält Beschreibungen der Eigenschaften, die in der JSON-Definition verwendet werden.

Eigenschaft | Beschreibung | Erforderlich
-------- | ----------- | --------
Typ | Legen Sie die Typeigenschaft auf **AzureDataLakeAnalytics** fest. | Ja
accountName | Name des Azure Data Lake Analytics-Kontos. | Ja
dataLakeAnalyticsUri | URI des Azure Data Lake Analytics-Kontos. | Nein 
authorization | Der Autorisierungscode wird automatisch abgerufen, nachdem Sie im Data Factory-Editor auf die Schaltfläche **Autorisieren** geklickt und die OAuth-Anmeldung abgeschlossen haben. | Ja 
subscriptionId | Azure-Abonnement-ID | Nein (falls nicht angegeben, wird das Abonnement der Data Factory verwendet). 
ResourceGroupName | Azure-Ressourcengruppenname | Nein (falls nicht angegeben, wird die Ressourcengruppe der Data Factory verwendet).
sessionId | Sitzungs-ID aus der OAuth-Autorisierungssitzung. Jede Sitzungs-ID ist eindeutig und darf nur einmal verwendet werden. Sie wird im Data Factory-Editor automatisch generiert. | Ja

Der von Ihnen mithilfe der Schaltfläche **Autorisieren** generierte Autorisierungscode läuft nach einer gewissen Zeit ab. Die Zeiten bis zum Ablaufen der Autorisierungscodes für die verschiedenen Benutzerkonten finden Sie in der folgenden Tabelle. Unter Umständen wird Ihnen nach dem **Ablauf des Tokens** folgende Fehlermeldung angezeigt: „Fehler beim Anmeldevorgang: invalid\_grant – AADSTS70002: Fehler beim Überprüfen der Anmeldeinformationen. AADSTS70008: Die angegebene Zugriffserteilung ist abgelaufen oder wurde widerrufen. Ablaufverfolgungs-ID: d18629e8-af88-43c5-88e3-d8419eb1fca1 Korrelations-ID: fac30a0c-6be6-4e02-8d69-a776d2ffefd7 Zeitstempel: 2015-12-15 21:09:31Z“

 
| Benutzertyp | Läuft ab nach |
| :-------- | :----------- | 
| Benutzer, die NICHT von Azure Active Directory verwaltet werden (@hotmail.com, @live.com usw.) | 12 Stunden |
| Benutzer, die von Azure Active Directory (AAD) verwaltet werden | | 14 Tage nach Ausführung des letzten Slice, wenn seit der letzten Sliceausführung seit 14 Tagen keine Slices basierend auf dem OAuth-basierten verknüpften Dienst ausgeführt wurden. <p>90 Tage, wenn ein Slice basierend auf dem OAuth-basierten verknüpften Dienst mindestens einmal alle 14 Tage ausgeführt wird.</p> |

Um diesen Fehler zu vermeiden oder zu beheben, müssen Sie bei **Ablauf des Tokens** mithilfe der Schaltfläche **Autorisieren** eine erneute Autorisierung ausführen und den verknüpften Dienst erneut bereitstellen. Sie können auch programmgesteuert Werte für die Eigenschaften **sessionId** und **authorization** generieren. Verwenden Sie dazu den im folgenden Abschnitt bereitgestellten Code.

  
### So generieren Sie programmgesteuert Werte für „sessionId“ und „authorization“ 

    if (linkedService.Properties.TypeProperties is AzureDataLakeStoreLinkedService ||
        linkedService.Properties.TypeProperties is AzureDataLakeAnalyticsLinkedService)
    {
        AuthorizationSessionGetResponse authorizationSession = this.Client.OAuth.Get(this.ResourceGroupName, this.DataFactoryName, linkedService.Properties.Type);

        WindowsFormsWebAuthenticationDialog authenticationDialog = new WindowsFormsWebAuthenticationDialog(null);
        string authorization = authenticationDialog.AuthenticateAAD(authorizationSession.AuthorizationSession.Endpoint, new Uri("urn:ietf:wg:oauth:2.0:oob"));

        AzureDataLakeStoreLinkedService azureDataLakeStoreProperties = linkedService.Properties.TypeProperties as AzureDataLakeStoreLinkedService;
        if (azureDataLakeStoreProperties != null)
        {
            azureDataLakeStoreProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeStoreProperties.Authorization = authorization;
        }

        AzureDataLakeAnalyticsLinkedService azureDataLakeAnalyticsProperties = linkedService.Properties.TypeProperties as AzureDataLakeAnalyticsLinkedService;
        if (azureDataLakeAnalyticsProperties != null)
        {
            azureDataLakeAnalyticsProperties.SessionId = authorizationSession.AuthorizationSession.SessionId;
            azureDataLakeAnalyticsProperties.Authorization = authorization;
        }
    }

Details zu den im Code verwendeten Data Factory-Klassen finden Sie in den Themen [AzureDataLakeStoreLinkedService-Klasse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakestorelinkedservice.aspx), [AzureDataLakeAnalyticsLinkedService-Klasse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.azuredatalakeanalyticslinkedservice.aspx) und [AuthorizationSessionGetResponse-Klasse](https://msdn.microsoft.com/library/microsoft.azure.management.datafactories.models.authorizationsessiongetresponse.aspx). Sie müssen für die WindowsFormsWebAuthenticationDialog-Klasse einen Verweis hinzufügen auf: „Microsoft.IdentityModel.Clients.ActiveDirectory.WindowsForms.dll“.
 
 
## U-SQL-Aktivität für Data Lake Analytics 

Der folgende JSON-Ausschnitt definiert eine Pipeline mit einer U-SQL-Aktivität für Data Lake Analytics. Die Aktivitätsdefinition verwendet einen Verweis auf den zuvor erstellten mit Azure Data Lake Analytics verknüpften Dienst.
  

	{
    	"name": "ComputeEventsByRegionPipeline",
    	"properties": {
        	"description": "This is a pipeline to compute events for en-gb locale and date less than 2012/02/19.",
        	"activities": 
			[
            	{
            	    "type": "DataLakeAnalyticsU-SQL",
                	"typeProperties": {
                    	"scriptPath": "scripts\\kona\\SearchLogProcessing.txt",
	                    "scriptLinkedService": "StorageLinkedService",
    	                "degreeOfParallelism": 3,
    	                "priority": 100,
    	                "parameters": {
    	                    "in": "/datalake/input/SearchLog.tsv",
    	                    "out": "/datalake/output/Result.tsv"
    	                }
    	            },
    	            "inputs": [
	    				{
	                        "name": "DataLakeTable"
	                    }
	                ],
	                "outputs": 
					[
	                    {
                    	    "name": "EventsByRegionTable"
                    	}
                	],
                	"policy": {
                    	"timeout": "06:00:00",
	                    "concurrency": 1,
    	                "executionPriorityOrder": "NewestFirst",
    	                "retry": 1
    	            },
    	            "scheduler": {
    	                "frequency": "Day",
    	                "interval": 1
    	            },
    	            "name": "EventsByRegion",
    	            "linkedServiceName": "AzureDataLakeAnalyticsLinkedService"
    	        }
    	    ],
    	    "start": "2015-08-08T00:00:00Z",
    	    "end": "2015-08-08T01:00:00Z",
    	    "isPaused": false
    	}
	}


Die folgende Tabelle beschreibt die Namen und Eigenschaften, die für diese Aktivität spezifisch sind.

Eigenschaft | Beschreibung | Erforderlich
:-------- | :----------- | :--------
type | Die type-Eigenschaft muss auf **DataLakeAnalyticsU-SQL** festgelegt sein. | Ja
scriptPath | Der Pfad zum Ordner, der das U-SQL-Skript enthält. | Nein (wenn script verwendet wird)
scriptLinkedService | Verknüpfter Dienst, der den Speicher, der das Skript enthält, mit der Data Factory verknüpft. | Nein (wenn script verwendet wird)
script | Geben Sie ein Inlineskript anstelle von scriptPath und scriptLinkedService an. Beispiel: „script“ : „CREATE DATABASE test“. | Nein (wenn scriptPath and scriptLinkedService verwendet werden)
degreeOfParallelism | Die maximale Anzahl von Knoten, die zum Ausführen des Auftrags gleichzeitig verwendet werden. | Nein
priority | Bestimmt, welche der in der Warteschlange befindlichen Aufträge als erstes ausgeführt werden. Je niedriger die Zahl, desto höher die Priorität. | Nein 
parameters | Parameter für das U-SQL-Skript | Nein 

Die Skriptdefinition finden Sie unter [Skriptdefinition „SearchLogProcessing.txt“](#script-definition).

### Eingabe- und Ausgabedatasets als Beispiel

#### Eingabedataset
In diesem Beispiel befinden sich die Eingabedaten in einem Azure Data Lake-Speicher (Datei SearchLog.tsv im Ordner datalake/input).

	{
    	"name": "DataLakeTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
    	    "linkedServiceName": "AzureDataLakeStoreLinkedService",
    	    "typeProperties": {
    	        "folderPath": "datalake/input/",
    	        "fileName": "SearchLog.tsv",
    	        "format": {
    	            "type": "TextFormat",
    	            "rowDelimiter": "\n",
    	            "columnDelimiter": "\t"
    	        }
    	    },
    	    "availability": {
    	        "frequency": "Day",
    	        "interval": 1
    	    }
    	}
	}	

#### Ausgabedataset
In diesem Beispiel werden die von dem U-SQL-Skript erzeugten Ausgabedaten in einem Azure Data Lake-Speicher abgelegt (im Ordner „datalake/output“).

	{
	    "name": "EventsByRegionTable",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "linkedServiceName": "AzureDataLakeStoreLinkedService",
	        "typeProperties": {
	            "folderPath": "datalake/output/"
	        },
	        "availability": {
	            "frequency": "Day",
	            "interval": 1
	        }
	    }
	}

#### Beispiel: Mit Azure Data Lake-Speicher verknüpfter Dienst
Hier finden Sie die Definition des Beispiels „Mit Azure Data Lake-Speicher verknüpfter Dienst“, der von den oben genannten Eingabe- und Ausgabedatasets verwendet wird.

	{
	    "name": "AzureDataLakeStoreLinkedService",
	    "properties": {
	        "type": "AzureDataLakeStore",
	        "typeProperties": {
	            "dataLakeUri": "https://<accountname>.azuredatalakestore.net/webhdfs/v1",
				"sessionId": "<session ID>",
	            "authorization": "<authorization URL>"
	        }
	    }
	}

Eine Beschreibung der in den oben gezeigten JSON-Ausschnitten zum Thema „Verknüpfte Dienste und Datasets mit Azure Data Lake-Speicher“ verwendeten JSON-Eigenschaften finden Sie unter [Verschieben von Daten zu und von Azure Data Lake-Speicher](data-factory-azure-datalake-connector.md)

### Skriptdefinition

	@searchlog =
	    EXTRACT UserId          int,
	            Start           DateTime,
	            Region          string,
	            Query           string,
	            Duration        int?,
	            Urls            string,
	            ClickedUrls     string
	    FROM @in
	    USING Extractors.Tsv(nullEscape:"#NULL#");
	
	@rs1 =
	    SELECT Start, Region, Duration
	    FROM @searchlog
	WHERE Region == "en-gb";
	
	@rs1 =
	    SELECT Start, Region, Duration
	    FROM @rs1
	    WHERE Start <= DateTime.Parse("2012/02/19");
	
	OUTPUT @rs1   
	    TO @out
	      USING Outputters.Tsv(quoting:false, dateTimeFormat:null);

Die Werte für die Parameter **@in** und **@out** im oben dargestellten U-SQL-Skript werden von ADF dynamisch mithilfe des Abschnitts „parameters“ übergeben. Informationen finden Sie oben in der Pipelinedefinition im Abschnitt „parameters“.

Sie können in Ihrer Pipelinedefinition auch andere Eigenschaften wie etwa „degreeOfParallelism“ oder „priority“ für die Aufträge angeben, die im Azure Data Lake Analytics-Dienst ausgeführt werden.

<!---HONumber=AcomDC_0224_2016-->