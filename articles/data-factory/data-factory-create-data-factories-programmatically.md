<properties 
	pageTitle="Erstellen, Überwachen und Verwalten von Azure Data Factorys mithilfe des Data Factory SDK" 
	description="Erfahren Sie, wie Sie programmgesteuert erstellen, überwachen und Verwalten von Azure Data-Factorys mithilfe von Data Factory-SDK." 
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
	ms.date="04/14/2015" 
	ms.author="spelluru"/>

# Erstellen, Überwachen und Verwalten von Azure Data Factorys mithilfe des Data Factory .NET SDK
## Übersicht
Sie können Azure Data Factorys mithilfe des Data Factory .NET SDK programmgesteuert erstellen, überwachen und verwalten. Dieser Artikel enthält eine exemplarische Vorgehensweise, die Sie befolgen können, um eine .NET-Beispielkonsolenanwendung zu erstellen, die eine Data Factory erstellt und überwacht. Finden Sie unter [Daten Factory Class Library Reference][adf-class-library-reference] ausführliche Informationen über Data Factory .NET SDK.



## Voraussetzungen

- Visual Studio 2012 oder 2013
- Herunterladen und installieren [Azure .NET SDK][azure-developer-center]
- Herunterladen und Installieren von NuGet-Paketen für Azure Data Factory Entsprechende Anweisungen sind in der exemplarischen Vorgehensweise enthalten.

## Exemplarische Vorgehensweise
1. Erstellen Sie mithilfe von Visual Studio 2012 oder 2013 eine C# .NET-Konsolenanwendung.
	<ol type="a">
	<li>Starten Sie <b>Visual Studio 2012</b> oder <b>Visual Studio 2013</b>.</li>
	<li>Klicken Sie auf <b>Datei</b>, zeigen Sie auf <b>Neu</b>, und klicken Sie auf <b>Projekt</b>.</li> 
	<li>Erweitern Sie <b>Vorlagen</b>, und wählen Sie <b>Visual C#-</b>. In dieser exemplarischen Vorgehensweise verwenden Sie C#, aber Sie können jede .NET-Sprache verwenden.</li> 
	<li>Wählen Sie <b>Konsolenanwendung</b> aus der Liste der Projekttypen auf der rechten Seite.</li>
	<li>Geben Sie <b>DataFactoryAPITestApp</b> für den <b>Namen</b>.</li> 
	<li>Wählen Sie <b>C:\ADFGetStarted</b> für die <b>Speicherort</b>.</li>
	<li>Klicken Sie auf <b>OK</b>, um das Projekt zu erstellen.</li>
</ol>
2. Klicken Sie auf <b>Tools</b>, zeigen Sie auf <b>NuGet-Paket-Manager-</b>, und klicken Sie auf <b>-Paket-Manager-Konsole</b>.
3.	In der <b>-Paket-Manager-Konsole</b>, führen Sie die folgenden Befehle werden nacheinander.</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre
		Install-Package Microsoft.DataFactories.Runtime –Pre
		Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory
6. Fügen Sie die folgenden **AppSetttings** im Abschnitt zu den **App.config** Datei. Diese werden verwendet, indem die Hilfsmethode: **GetAuthorizationHeader**. 

	Ersetzen Sie die Werte für **SubscriptionId** und **ActiveDirectoryTenantId** durch Ihre Abonnement- und Mandanten-ID für Azure. Sie können diese Werte abrufen, indem Sie mit **Get-AzureAccount** von Azure PowerShell (möglicherweise müssen zuerst mithilfe des Add-AzureAccount anmelden).
 
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
6. Fügen Sie die folgenden **mit** Anweisungen mit der Quelldatei ("Program.cs") im Projekt.

		using System.Threading;
		using System.Configuration;
		using System.Collections.ObjectModel;
				
		using Microsoft.Azure.Management.DataFactories;
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.IdentityModel.Clients.ActiveDirectory;
		using Microsoft.Azure; 
6. Fügen Sie folgenden Code, der eine Instanz erstellt **DataPipelineManagementClient** -Klasse auf, um die **Main** Methode. Sie verwenden dieses Objekt, um eine Data Factory, einen verknüpften Dienst, Eingabe- und Ausgabetabellen sowie eine Pipeline zu erstellen. Zudem verwenden Sie dieses Objekt, um Datenslices einer Tabelle zur Laufzeit zu überwachen.    

        // create data pipeline management client
        string resourceGroupName = "ADF";
        string dataFactoryName = "APITutorialFactory";

        TokenCloudCredentials aadTokenCredentials =
            new TokenCloudCredentials(
                ConfigurationManager.AppSettings["SubscriptionId"],
                GetAuthorizationHeader());

        Uri resourceManagerUri = new Uri(ConfigurationManager.AppSettings["ResourceManagerEndpoint"]);

        DataPipelineManagementClient client = new DataPipelineManagementClient(aadTokenCredentials, resourceManagerUri);
7. Fügen Sie den folgenden Code, erstellt eine **Daten Factory** an die **Main** Methode.

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
8. Fügen Sie den folgenden Code, erstellt eine **Service verknüpft** an die **Main** Methode.
	> [AZURE.NOTE]**Kontoname****Kontoschlüssel****ConnectionString** 

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
9. Fügen Sie den folgenden Code zur **Eingabe- und Tabellen** an die **Main** Methode. 

	Beachten Sie, dass die **FolderPath** für den Wert des Eingaben BLOBs **Adftutorial /**  **Adftutorial** ist der Name des Containers im Blob-Speicher. Wenn dieser Container nicht in Ihren Azure-Blob-Speicher vorhanden ist, erstellen Sie einen Container mit diesem Namen: **Adftutorial** und Hochladen eine Textdatei auf den Container.
	
	Beachten Sie, dass der Ordnerpfad für die Ausgabe-Blob wird an: **Adftutorial/Apifactoryoutput / {Slice}** in denen **Slice** wird dynamisch berechnet, basierend auf dem Wert der **SliceStart** (start Datum und Uhrzeit der einzelnen Segmente).

 
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
10. Fügen Sie den folgenden code, **erstellt und aktiviert eine Pipeline** an die **Main** Methode. Diese Pipeline hat eine **CopyActivity** übernimmt **BlobSource** als Quelle und **BlobSink** als eine Senke. 

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

11. Fügen Sie die folgende Hilfsmethode, mit der **Main** -Methode, um die **Programm** Klasse. Diese Methode wird ein Dialogfeld, das Sie bereitstellen, kann **Benutzername** und **Kennwort** mit denen Sie in Azure-Portal anmelden.
 
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
 
13. Fügen Sie den folgenden Code in die **Main** -Methode, um den Status des einen Datenslice der Ausgabetabelle abzurufen. Es gibt nur ein Slice in diesem Beispiel erwartet.
 
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

14. Fügen Sie folgenden Code aus, um Details für ein Segment Datenslice zu auszuführen, erhalten die **Main** Methode.

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

15. Erstellen Sie die Konsolenanwendung. Klicken Sie auf **Erstellen** im Menü, und klicken Sie auf **Projektmappe**.
16. Vergewissern Sie sich, dass sich mindestens eine Datei im "adftutorial"-Container im Azure Blob-Speicher befindet. Ist dies nicht der Fall, erstellen Sie die Datei "Emp.txt" mit folgendem Inhalt im Editor, und laden Sie sie anschließend in den "adftutorial"-Container hoch.

        John, Doe
		Jane, Doe
	 
17. Führen Sie das Beispiel, indem Sie auf **Debuggen** -> **Debuggen starten** im Menü auf.
18. Verwenden Sie das Azure Preview Portal zu überprüfen, ob die Factory Daten: **APITutorialFactory** wird mit der folgenden Elemente erstellt: 
	- Dienst verknüpft: **LinkedService_AzureStorage** 
	- Tabellen: **TableBlobSource** und **TableBlobDestination**.
	- Pipeline: **PipelineBlobSample** 
18. Stellen Sie sicher, dass eine Ausgabedatei, in erstellt wird der **Apifactoryoutput** Ordner in den **Adftutorial** Container.


## Siehe auch

Artikel | Beschreibung
------ | ---------------
[Azure Data Factory-Entwicklerreferenz][developer-reference] | Entwicklerreferenz hat die umfassendes Referenzmaterial für die Klassenbibliothek von .NET, Cmdlets, JSON-Skripts, Funktionen usw.... 


[data-factory-introduction]: data-factory-introduction.md
[adf-getstarted]: data-factory-get-started.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
 
[adf-class-library-reference]: http://go.microsoft.com/fwlink/?LinkID=521877
[azure-developer-center]: http://azure.microsoft.com/downloads/

<!---HONumber=GIT-SubDir--> 