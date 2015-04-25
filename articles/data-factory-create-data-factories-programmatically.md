<properties title="Create, monitor, and manage Azure data factories using Data Factory SDK" pageTitle="Erstellen, Überwachen und Verwalten von Azure Data Factorys mithilfe des Data Factory SDK" description="Erfahren Sie, wie Sie mit Data Factory SDK programmatisch Azure Data Factorys erstellen, überwachen und verwalten." metaKeywords=""  services="data-factory" solutions=""  documentationCenter="" authors="spelluru" manager="jhubbard" editor="monicar" />

<tags ms.service="data-factory" ms.workload="data-services" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="12/08/2014" ms.author="spelluru" />

# Erstellen, Überwachen und Verwalten von Azure Data Factorys mithilfe des Data Factory .NET SDK
Sie können Azure Data Factorys mithilfe des Data Factory .NET SDK programmgesteuert erstellen, überwachen und verwalten. Dieser Artikel enthält eine exemplarische Vorgehensweise, die Sie befolgen können, um eine .NET-Beispielkonsolenanwendung zu erstellen, die eine Data Factory erstellt und überwacht. Weitere Informationen zum Data Factory .NET SDK finden Sie unter [Data Factory-Klassenbibliotheksreferenz][adf-class-library-reference]. 

## Exemplarische Vorgehensweise: Erstellen einer Azure Data Factory mithilfe des Data Factory .NET SDK

**Voraussetzungen:**

- Visual Studio 2012 oder 2013
- Herunterladen und Installieren des [Microsoft Azure .NET SDK][azure-developer-center]
- Herunterladen und Installieren von NuGet-Paketen für Azure Data Factory Entsprechende Anweisungen sind in der exemplarischen Vorgehensweise enthalten.

### Schritt 1: Erstellen einer Azure Data Factory mithilfe des Data Factory .NET SDK
1. Erstellen Sie mithilfe von Visual Studio 2012 oder 2013 eine C# .NET-Konsolenanwendung.
	<ol type="a">
		<li>Starten Sie <b>Visual Studio 2012</b> oder <b>Visual Studio 2013</b>.</li>
		<li>Klicken Sie auf <b>Datei</b>, zeigen Sie auf <b>Neu</b>, und klicken Sie auf <b>Projekt</b>.</li> 
		<li>Erweitern Sie <b>Vorlagen</b>, und wählen Sie <b>Visual C#</b>. In dieser exemplarischen Vorgehensweise verwenden Sie C#, aber Sie können jede .NET-Sprache verwenden.</li> 
		<li>Wählen Sie <b>Konsolenanwendung</b> auf der rechten Seite aus der Liste der Projekttypen aus.</li>
		<li>Geben Sie <b>DataFactoryAPITestApp</b> für <b>Name</b> ein.</li> 
		<li>Wählen Sie <b>C:\ADFGetStarted</b> für <b>Speicherort</b>.</li>
		<li>Klicken Sie auf <b>OK</b> , um das Projekt zu erstellen.</li>
	</ol>
2. Klicken Sie auf <b>Tools</b>, zeigen Sie auf <b>NuGet-Paket-Manager</b>, und klicken Sie auf <b>Paket-Manager-Konsole</b>.
3.	In der <b>Paket-Manager-Konsole</b> führen Sie die folgenden Befehle nacheinander aus</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories -Pre
		Install-Package Microsoft.DataFactories.Runtime -Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
6. Fügen Sie den folgenden **appSetttings**-Abschnitt zur Datei **App.config** hinzu. Diese werden von der Hilfsmethode verwendet: **GetAuthorizationHeader**. 

	Ersetzen Sie die Werte für **SubscriptionId** und **ActiveDirectoryTenantId** durch Ihre Azure-Abonnement- und Mandanten-ID. Sie können diese Werte abrufen, indem Sie **Get-AzureAccount** über Azure PowerShell ausführen (möglicherweise müssen Sie sich zuvor mithilfe von "Add-AzureAccount" anmelden).
 
		<appSettings>
		    <!--CSM Prod related values-->
		    <add key="ActiveDirectoryEndpoint" value="https://login.windows.net/" />
		    <add key="ResourceManagerEndpoint" value="https://management.azure.com/" />
		    <add key="WindowsManagementUri" value="https://management.core.windows.net/" />
		    <add key="AdfClientId" value="1950a258-227b-4e31-a9cf-717495945fc2" />
		    <add key="RedirectUri" value="urn:ietf:wg:oauth:2.0:oob" />
		    <!--Make sure to write your own tenenat id and subscription ID here-->
		    <add key="SubscriptionId" value="49fb6e5f-3098-4fb2-ba2f-6d6eed843a65" />
    		<add key="ActiveDirectoryTenantId" value="37330244-7828-4a28-99b7-c8c3a437c7ac" />
		</appSettings>
6. Fügen Sie die folgenden **using**-Anweisungen zur Quelldatei (Program.cs) im Projekt hinzu.

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
				
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.WindowsAzure; 
6. Fügen Sie folgenden Code, der eine Instanz der **DataPipelineManagementClient**-Klasse erstellt, zur Methode **Main** hinzu. Sie verwenden dieses Objekt, um eine Data Factory, einen verknüpften Dienst, Eingabe- und Ausgabetabellen sowie eine Pipeline zu erstellen. Zudem verwenden Sie dieses Objekt, um Datenslices einer Tabelle zur Laufzeit zu überwachen.    

        // create data pipeline management client
        string resourceGroupName = "ADF";
        string dataFactoryName = "APITutorialFactory";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataPipelineManagementClient client = new DataPipelineManagementClient(aadTokenCredentials, resourceManagerUri);
7. Add the following code that creates a **data factory** to the **Main** method.

        // create a data factory
        Console.WriteLine("Creating a data factory");
        client.DataFactories.CreateOrUpdate(resourceGroupName,
            new DataFactoryCreateOrUpdateParameters()
            {
                DataFactory = new DataFactory()
                {
                    Name = dataFactoryName,
                    Location = "westus",
                    Properties = new DataFactoryProperties() { }
                }
            }
        );
8. Fügen Sie den folgenden Code, der einen **verknüpften Dienst** erstellt, zur Methode **Main** hinzu. 
	> [WACOM.NOTE] Verwenden Sie **Kontoname** und **Kontoschlüssel** Ihres Azure-Speicherkontos für **ConnectionString**. 

		// create a linked service
        Console.WriteLine("Creating a linked service");
        client.LinkedServices.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new LinkedServiceCreateOrUpdateParameters()
            {
                LinkedService = new LinkedService()
                {
                    Name = "LinkedService-AzureStorage",
                    Properties = new AzureStorageLinkedService()
                    {
                        ConnectionString = "DefaultEndpointsProtocol=https;AccountName=<account name>;AccountKey=account key",
                    }
                }
            }
        );
9. Fügen Sie den folgenden Code, der **Eingabe- und Ausgabetabellen** erstellt, zur Methode **Main** hinzu. 

	Beachten Sie, dass für den **FolderPath** für das Eingabe-Blob der Wert **adftutorial/** festgelegt ist, wobei **adftutorial** den Namen des Containers im Blob-Speicher darstellt. Wenn dieser Container nicht in Ihrem Azure Blob-Speicher enthalten ist, erstellen Sie einen Container mit diesem Namen: **adftutorial**, und laden Sie dann eine Textdatei in den Container hoch.
	
	Beachten Sie, dass für "FolderPath" für das Ausgabe-Blob Folgendes festgelegt ist: **adftutorial/apifactoryoutput/{Slice}**, wobei **Slice** dynamisch auf Basis des Werts von **SliceStart** (Startdatum und -zeit für jeden Slice) berechnet wird.  

 
        // create input and output tables
        Console.WriteLine("Creating input and output tables");
        string Table_Source = "TableBlobSource";
        string Table_Destination = "TableBlobDestination";

        client.Tables.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new TableCreateOrUpdateParameters()
            {
                Table = new Table()
                {
                    Name = Table_Source,
                    Properties = new TableProperties()
                    {
                        Location = new AzureBlobLocation()
                        {
                            FolderPath = "adftutorial/",
                            LinkedServiceName = "LinkedService-AzureStorage",
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                            WaitOnExternal = new WaitOnExternal()
                        },

                        Policy = new Policy()
                        {
                            Validation = new ValidationPolicy()
                            {
                                MinimumRows = 1
                            }
                        }
                    }
                }
            });

        client.Tables.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new TableCreateOrUpdateParameters()
            {
                Table = new Table()
                {
                    Name = Table_Destination,
                    Properties = new TableProperties()
                    {
                        Location = new AzureBlobLocation()
                        {
                            FolderPath = "adftutorial/apifactoryoutput/{Slice}",
                            PartitionedBy = new Collection<Partition>()
                            {
                                new Partition()
                                {
                                    Name = "Slice",
                                    Value = new DateTimePartitionValue()
                                    {
                                        Date = "SliceStart",
                                        Format = "yyyyMMdd-HH"
                                    }
                                }
                            },
                            LinkedServiceName = "LinkedService-AzureStorage",
                        },

                        Availability = new Availability()
                        {
                            Frequency = SchedulePeriod.Hour,
                            Interval = 1,
                        },
                    }
                }
            });
10. Fügen Sie den folgenden Code, der eine **Pipeline erstellt und aktiviert**, zur Methode **Main** hinzu. Diese Pipeline verfügt über ein **CopyActivity**, das **BlobSource** als Quelle und **BlobSink** als Senke übernimmt. 

        // create a pipeline
        Console.WriteLine("Creating a pipeline");
        DateTime PipelineActivePeriodStartTime = new DateTime(2014, 8, 9, 0, 0, 0, 0, DateTimeKind.Utc);
        DateTime PipelineActivePeriodEndTime = PipelineActivePeriodStartTime.AddMinutes(60);
        string PipelineName = "PipelineBlobSample";

        client.Pipelines.CreateOrUpdate(resourceGroupName, dataFactoryName,
            new PipelineCreateOrUpdateParameters()
            {
                Pipeline = new Pipeline()
                {
                    Name = PipelineName,
                    Properties = new PipelineProperties()
                    {
                        Description = "Demo Pipeline for data transfer between blobs",

                        // Initial value for pipeline's active period. With this, you won't need to set slice status
                        Start = PipelineActivePeriodStartTime,
                        End = PipelineActivePeriodEndTime,

                        Activities = new List<BaseActivity>()
                        {
                            new CopyActivity()
                            {
                                Name = "BlobToBlob",
                                Inputs = new List<ActivityInput>()
                                {
                                    new ActivityInput()
                                    {
                                        Name = Table_Source,
                                    }
                                },
                        
                                Outputs = new List<ActivityOutput>()
                                {
                                    new ActivityOutput()
                                    {
                                        Name = Table_Destination, 
                                    }
                                },
                     
                                Transformation = new CopyActivityProperties()
                                {
                                    Source = new BlobSource()
                                    {
                                        BlobColumnSeparators = ",",
                                    },
                            
                                    Sink = new BlobSink()
                                    {
                                        WriteBatchSize = 10000,
                                        WriteBatchTimeout = TimeSpan.FromMinutes(10)
                                    },

                                },

                                Policy = new ActivityPolicy()
                                {
                                    ExecutionPriorityOrder = ExecutionPriorityOrder.NewestFirst,
                                    Concurrency = 1,
                                    Retry = 2,
                                    Timeout = TimeSpan.FromMinutes(10),
                                }
                            }

                        },
                    }
                }
            });

11. Fügen Sie die folgende Hilfsmethode, die von der Methode **Main** verwendet wird, zur **Program**-Klasse hinzu. Diese Methode öffnet ein Dialogfeld, in dem Sie den **Benutzernamen** und das **Kennwort** bereitstellen können, mit denen Sie sich beim Azure-Portal anmelden. 
 
		public static string GetAuthorizationHeader()
        {
            AuthenticationResult result = null;
            var thread = new Thread(() =>
            {
                try
                {
                    var context = new AuthenticationContext(ConfigurationManager.AppSettings["ActiveDirectoryEndpoint"] + ConfigurationManager.AppSettings["ActiveDirectoryTenantId"]);

                    result = context.AcquireToken(
                        resource: ConfigurationManager.AppSettings["WindowsManagementUri"],
                        clientId: ConfigurationManager.AppSettings["AdfClientId"],
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
 
13. Fügen Sie den folgenden Code zur Methode **Main** hinzu, um den Status eines Datenslice der Ausgabetabelle abzurufen. Es gibt nur ein Slice in diesem Beispiel erwartet.   
 
        // Pulling status within a timeout threshold
        DateTime start = DateTime.Now;
        bool done = false;

        while (DateTime.Now - start < TimeSpan.FromMinutes(5) && !done)
        {
            Console.WriteLine("Pulling the slice status");
            // wait before the next status check
            Thread.Sleep(1000 * 12);

            var datalistResponse = client.DataSlices.List(resourceGroupName, dataFactoryName, Table_Destination,
                PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString(),
                PipelineActivePeriodEndTime.ConvertToISO8601DateTimeString());

            foreach (DataSlice slice in datalistResponse.DataSlices)
            {
                if (slice.Status == DataSliceStatus.Failed || slice.Status == DataSliceStatus.Ready)
                {
                    Console.WriteLine("Slice execution is done with status: {0}", slice.Status);
                    done = true;
                    break;
                }
                else
                {
                    Console.WriteLine("Slice status is: {0}", slice.Status);
                }
            }
        }

14. Fügen Sie den folgenden Code zur Methode **Main** hinzu, um Ausführungsdetails für ein Datenslice abzurufen.

        Console.WriteLine("Getting run details of a data slice");

        var datasliceRunListResponse = client.DataSliceRuns.List(resourceGroupName, dataFactoryName, Table_Destination,
                PipelineActivePeriodStartTime.ConvertToISO8601DateTimeString());

        foreach (DataSliceRun run in datasliceRunListResponse.DataSliceRuns)
        {
            Console.WriteLine("Status: \t\t{0}", run.Status);
            Console.WriteLine("DataSliceStart: \t{0}", run.DataSliceStart);
            Console.WriteLine("DataSliceEnd: \t\t{0}", run.DataSliceEnd);
            Console.WriteLine("ActivityId: \t\t{0}", run.ActivityName);
            Console.WriteLine("ProcessingStartTime: \t{0}", run.ProcessingStartTime);
            Console.WriteLine("ProcessingEndTime: \t{0}", run.ProcessingEndTime);
            Console.WriteLine("ErrorMessage: \t{0}", run.ErrorMessage);
        }

        Console.WriteLine("\nPress any key to exit.");
        Console.ReadKey();
    }

15. Erstellen Sie die Konsolenanwendung. Klicken Sie im Menü auf **Erstellen**, und klicken Sie dann auf **Projektmappe erstellen**.
16. Vergewissern Sie sich, dass sich mindestens eine Datei im "adftutorial"-Container im Azure Blob-Speicher befindet. Ist dies nicht der Fall, erstellen Sie die Datei "Emp.txt" mit folgendem Inhalt im Editor, und laden Sie sie anschließend in den "adftutorial"-Container hoch.

        John, Doe
		Jane, Doe
	 
17. Führen Sie das Beispiel aus, indem Sie im Menü auf **Debuggen** -> **Debuggen starten** klicken.
18. Verwenden Sie das Azure-Vorschauportal, um Folgendes für die Data Factory zu überprüfen: **APITutorialFactory** wird mit folgenden Artefakten erstellt: 
	- Verknüpfter Dienst: **LinkedService_AzureStorage** 
	- Tabellen: **TableBlobSource** und **TableBlobDestination**.
	- Pipeline: **PipelineBlobSample** 
18. Stellen Sie sicher, dass im Ordner **apifactoryoutput** im **adftutorial**-Container eine Ausgabedatei erstellt wird.


## Siehe auch

Artikel | Beschreibung
------ | ---------------
[Einführung in Azure Data Factory][data-factory-introduction] | In diesem Artikel werden der Azure Data Factory-Dienst, Azure Data Factory-Konzepte, der bereitgestellte Nutzen und unterstützte Szenarien erläutert.
[Erste Schritte mit Azure Data Factory][adf-getstarted] | Dieser Artikel bietet ein umfassendes Lernprogramm, in dem Sie erfahren, wie Sie eine Beispiel-Data Factory für Azure erstellen, die Daten aus einem Azure-BLOB in eine Azure SQL-Datenbank kopiert.
[Aktivieren von Pipelines zum Arbeiten mit lokalen Daten][use-onpremises-datasources] | Dieser Artikel enthält eine exemplarische Vorgehensweise zum Kopieren von Daten aus einer lokalen SQL Server-Datenbank in einen Azure-BLOB.
[Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Data Factory][adf-tutorial] | In diesem Artikel wird in einer umfassenden exemplarischen Vorgehensweise die Implementierung eines realen Szenarios mithilfe von Azure Data Factory veranschaulicht, um Einblicke aus Protokolldateien zu gewinnen.
[Verwenden von benutzerdefinierten Aktivitäten in einer Data Factory][use-custom-activities] | Dieser Artikel enthält eine exemplarische Vorgehensweise mit schrittweisen Anleitungen zum Erstellen einer benutzerdefinierten Aktivität und deren Verwendung in einer Pipeline.
[Azure Data Factory-Entwicklerreferenz][developer-reference] | Die Entwicklerreferenz enthält umfassende Referenzinformationen für Cmdlets, JSON-Skripts, Funktionen usw. 


[data-factory-introduction]: ../data-factory-introduction
[adf-getstarted]: ../data-factory-get-started
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/de-de/downloads/

<!--HONumber=35.2-->
