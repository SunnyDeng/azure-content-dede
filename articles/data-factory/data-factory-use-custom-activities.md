<properties 
	pageTitle="Verwenden von benutzerdefinierten Aktivitäten in einer Azure Data Factory-Pipeline"
	description="Erfahren Sie, wie Sie benutzerdefinierte Aktivitäten erstellen und in einer Azure Data Factory-Pipeline verwenden."
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
	ms.date="08/28/2015"
	ms.author="spelluru"/>

# Verwenden von benutzerdefinierten Aktivitäten in einer Azure Data Factory-Pipeline
Azure Data Factory unterstützt integrierte Aktivitäten, z. B. **Kopieraktivität** und **HDInsight-Aktivität**, zum Verschieben und Verarbeiten von Daten in Pipelines. Sie können auch eine benutzerdefinierte .NET-Aktivität mit Ihrer eigenen Transformations-/Verarbeitungslogik erstellen und die Aktivität in einer Pipeline verwenden. Sie können die Aktivität so konfigurieren, dass entweder ein **Azure HDInsight**-Cluster oder ein **Azure Batch**-Dienst ausgeführt wird.

Dieser Artikel beschreibt, wie Sie eine benutzerdefinierte Aktivität erstellen und in einer Azure Data Factory-Pipeline verwenden. Darüber hinaus bietet er eine ausführliche exemplarische Vorgehensweise mit einer schrittweisen Anleitung zum Erstellen und Verwenden einer benutzerdefinierten Aktivität. Für die exemplarische Vorgehensweise wird der mit HDInsight verknüpfte Dienst verwendet. Um stattdessen den mit Azure Batch verknüpften Dienst zu verwenden, erstellen Sie einen verknüpften Dienst vom Typ **AzureBatch** und verwenden ihn im Abschnitt "Aktivität" der JSON-Pipeline (**linkedServiceName**). Im Abschnitt [Mit Azure Batch verknüpfter Dienst](#AzureBatch) finden Sie Informationen zur Verwendung von Azure Batch mit der benutzerdefinierten Aktivität.


## <a name="walkthrough" /> Exemplarische Vorgehensweise
Diese exemplarische Vorgehensweise bietet Schritt-für-Schritt-Anleitungen zum Erstellen einer benutzerdefinierten Aktivität und zur Verwendung der Aktivität in einer Azure Data Factory-Pipeline. Diese exemplarische Vorgehensweise baut auf dem Lernprogramm in [Erste Schritte mit Azure Data Factory][adfgetstarted] auf. Wenn Sie die benutzerdefinierte Aktivität in Aktion sehen möchten, müssen Sie zuerst das Erste-Schritte-Lernprogramm durchlaufen und diese exemplarischen Vorgehensweise ausführen.

**Voraussetzungen:**


- Lernprogramm aus [Erste Schritte mit Azure Data Factory][adfgetstarted]. Sie müssen zuerst das Lernprogramm in diesem Artikel durchlaufen, bevor Sie mit dieser exemplarischen Vorgehensweise fortfahren können.
- Visual Studio 2012 oder 2013
- Herunterladen und Installieren des [Azure .NET SDK][azure-developer-center]
- Laden Sie das aktuelle [NuGet-Paket für Azure Data Factory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories/) herunter, und installieren Sie es. Entsprechende Anweisungen sind in der exemplarischen Vorgehensweise enthalten.
- Herunterladen und Installieren des NuGet-Pakets für Azure-Speicher. Anweisungen sind in der exemplarischen Vorgehensweise enthalten, daher können Sie diesen Schritt überspringen.

## Schritt 1: Erstellen einer benutzerdefinierten Aktivität

1.	Erstellen Sie ein .NET-Klassenbibliotheksprojekt.
	<ol type="a">
	<li>Starten Sie <b>Visual Studio&#160;2012</b> oder <b>Visual Studio&#160;2013</b>.</li>
	<li>Klicken Sie auf <b>Datei</b>, zeigen Sie auf <b>Neu</b>, und klicken Sie auf <b>Projekt</b>.</li> 
	<li>Erweitern Sie <b>Vorlagen</b>, und wählen Sie <b>Visual C#</b> aus. In dieser exemplarischen Vorgehensweise verwenden Sie C#, Sie können jedoch jede .NET-Sprache verwenden, um benutzerdefinierte Aktivität zu entwickeln.</li> 
	<li>Wählen Sie in der Liste mit den Projekttypen auf der rechten Seite den Eintrag <b>Klassenbibliothek</b> aus.</li>
	<li>Geben Sie <b>MyDotNetActivity</b> als <b>Namen</b> ein.</li> 
	<li>Wählen Sie <b>C:\ADFGetStarted</b> als <b>Speicherort</b>.</li>
	<li>Klicken Sie auf <b>OK</b>, um das Projekt zu erstellen.</li>
</ol>
2.  Klicken Sie auf <b>Extras</b>, zeigen Sie auf <b>NuGet-Paket-Manager</b>, und klicken Sie auf <b>Paket-Manager-Konsole</b>.
3.	Führen Sie in der <b>Paket-Manager-Konsole</b> den folgenden Befehl zum Importieren von <b>Microsoft.Azure.Management.DataFactories</b> aus. 

		Install-Package Microsoft.Azure.Management.DataFactories

4. Importieren Sie das Azure Storage NuGet-Paket in das Projekt.

		Install-Package Azure.Storage

5. Fügen Sie die folgenden **using**-Anweisungen der Quelldatei im Projekt hinzu.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Runtime;

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. Ändern Sie den Namen des **Namespace** in **MyDotNetActivityNS**.

		namespace MyDotNetActivityNS

7. Ändern Sie den Namen der Klasse in **MyDotNetActivity**, und leiten Sie ihn von der **IDotNetActivity**-Schnittstelle ab, wie unten dargestellt.

		public class MyDotNetActivity : IDotNetActivity

8. Implementieren bzw. fügen Sie die **Execute**-Methode der **IDotNetActivity**-Schnittstelle der **MyDotNetActivity**-Klasse hinzu, und kopieren Sie den folgenden Beispielcode in die Methode.


	Im folgenden Beispielcode wird die Anzahl der Zeilen im Eingabeblob gezählt und der folgende Inhalt im Ausgabeblob erzeugt: Pfad zum Blob, die Anzahl der Zeilen im Blob, der Computer, auf dem die Aktivität ausgeführt wurde, und das aktuelle Datum samt Uhrzeit.

        public IDictionary<string, string> Execute(IEnumerable<LinkedService> linkedServices, IEnumerable<Table> tables, Activity activity, IActivityLogger logger)
        {
            IDictionary<string, string> extendedProperties = ((DotNetActivity)activity.TypeProperties).ExtendedProperties;

            AzureStorageLinkedService inputLinkedService, outputLinkedService;
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Table inputTable = tables.Single(table => table.Name == activity.Inputs.Single().Name);
            inputLocation = inputTable.Properties.TypeProperties as CustomDataset;

			// using First method instead of Single since we are using the same 
			// Azure Storage linked service for input and output. 
            inputLinkedService = linkedServices.First(linkedService => linkedService.Name == inputTable.Properties.LinkedServiceName).Properties.TypeProperties as AzureStorageLinkedService;


            string output = string.Empty;

            logger.Write("Before anything...");

            logger.Write("Printing dictionary entities if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            string connectionString = GetConnectionString(inputLinkedService);
            string folderPath = GetFolderPath(inputTable);

            logger.Write("Reading blob from: {0}", folderPath);

            CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
            CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

            BlobContinuationToken continuationToken = null;

            do
            {
                BlobResultSegment result = inputClient.ListBlobsSegmented(folderPath,
                                            true,
                                            BlobListingDetails.Metadata,
                                            null,
                                            continuationToken,
                                            null,
                                            null);
                foreach (IListBlobItem listBlobItem in result.Results)
                {
                    CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                    int count = 0;
                    if (inputBlob != null)
                    {
                        using (StreamReader sr = new StreamReader(inputBlob.OpenRead()))
                        {
                            while (!sr.EndOfStream)
                            {
                                string line = sr.ReadLine();
                                if (count == 0)
                                {
                                    logger.Write("First line: [{0}]", line);
                                }
                                count++;
                            }

                        }

                    }
                    output += string.Format(CultureInfo.InvariantCulture,
                                    "{0},{1},{2},{3},{4}\n",
                                    folderPath,
                                    inputBlob.Name,
                                    count,
                                    Environment.MachineName,
                                    DateTime.UtcNow);

                }
                continuationToken = result.ContinuationToken;

            } while (continuationToken != null);

            Table outputTable = tables.Single(table => table.Name == activity.Outputs.Single().Name);
            outputLocation = outputTable.Properties.TypeProperties as AzureBlobDataset;
            outputLinkedService = linkedServices.First(linkedService => linkedService.Name == outputTable.Properties.LinkedServiceName).Properties.TypeProperties as AzureStorageLinkedService;

            connectionString = GetConnectionString(outputLinkedService);
            folderPath = GetFolderPath(outputTable);

            logger.Write("Writing blob to: {0}", folderPath);

            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + Guid.NewGuid() + ".csv");

            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            outputBlob.UploadText(output);

            return new Dictionary<string, string>();

        }

9. Fügen Sie die folgenden Hilfsmethoden hinzu. Die **Execute**-Methode ruft diese Hilfsmethoden auf. Die **GetConnectionString**-Methode ruft die Azure Storage-Verbindungszeichenfolge ab, und die **GetFolderPath**-Methode ruft den Blobspeicherort ab.


        private static string GetConnectionString(AzureStorageLinkedService asset)
        {

            if (asset == null)
            {
                return null;
            }

            return asset.ConnectionString;
        }

        
        private static string GetFolderPath(Table dataArtifact)
        {
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
            if (blobDataset == null)
            {
                return null;
            }

            return blobDataset.FolderPath;
        }
   

10. Kompilieren Sie das Projekt. Klicken Sie im Menü auf **Erstellen** und dann auf **Projektmappe erstellen**.
11. Starten Sie **Windows-Explorer**, und navigieren Sie je nach Buildtyp zum Ordner **bin\\debug** oder **bin\\release**.
12. Erstellen Sie die ZIP-Datei **MyDotNetActivity.zip**, die alle Binärdateien im Ordner "<project folder>\\bin\\Debug" enthält. Sie möchten ggf. die Datei "MyDotNetActivity.pdb" einbeziehen, damit Sie zusätzliche Details wie z. B. die Zeilennummer im Quellcode erhalten, der das Problem bei einem Fehler verursacht hat. 
13. Laden Sie **MyDotNetActivity.zip** als Blob in den Blobcontainer **customactivitycontainer** im Azure-Blobspeicher hoch, den der verknüpfte Dienst **StorageLinkedService** in der **ADFTutorialDataFactory** verwendet. Erstellen Sie den Blobcontainer **customactivitycontainer**, sofern er noch nicht vorhanden ist.

> [AZURE.NOTE]Wenn Sie dieses .NET-Aktivitätsprojekt einer Projektmappe in Visual Studio hinzufügen, die ein Data Factory-Projekt enthält, müssen Sie die letzten beiden Schritte zum Erstellen der ZIP-Datei und zum manuellen Hochladen in den Azure-Blobspeicher nicht durchführen. Beim Veröffentlichen von Data Factory-Entitäten mit Visual Studio werden diese Schritte automatisch durch den Veröffentlichungsprozess ausgeführt. In den Artikeln [Erstellen der ersten Pipeline mit Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) und [Kopieren von Daten aus einem Azure-Blob in Azure SQL](data-factory-get-started-using-vs.md) erfahren Sie mehr über das Erstellen und Veröffentlichen von Data Factory-Entitäten mit Visual Studio.


## Schritt 2: Verwenden der benutzerdefinierten Aktivität in einer Pipeline
Dies sind die einzelnen Schritte, die Sie in diesem Schritt ausführen:

1. Erstellen Sie einen verknüpften Dienst für den HDInsight-Cluster, in dem die benutzerdefinierte Aktivität als reiner Zuordnungsauftrag ausgeführt wird. 
2. Erstellen Sie eine Ausgabetabelle, die die Pipeline in diesem Beispiel erzeugt.
3. Erstellen Sie eine Pipeline, für die die in Schritt 1 erstellte benutzerdefinierte Aktivität verwendet wird, und führen Sie sie aus. 
 
### Erstellen Sie einen verknüpften Dienst für den HDInsight-Cluster, der zum Ausführen der benutzerdefinierten Aktivität verwendet wird.
Der Azure Data Factory-Dienst unterstützt das Erstellen eines Clusters bei Bedarf und verwendet ihn zum Verarbeiten der Eingabe zum Erzeugen von Ausgabedaten. Sie können auch Ihren eigenen Cluster für den gleichen Zweck verwenden. Wenn Sie den bedarfsgesteuerten HDInsight-Cluster verwenden, wird für jeden Slice ein Cluster erstellt. Bei Verwendung eines eigenen HDInsight-Clusters ist der Cluster sofort zur Sliceverarbeitung bereit. Aus diesem Grund werden bei Verwendung des bedarfsgesteuerten Clusters die Ausgabedaten möglicherweise nicht so schnell angezeigt wie bei der Verwendung Ihres eigenen Clusters.

> [AZURE.NOTE]Zur Laufzeit wird eine Instanz einer .NET-Aktivität nur auf einem Workerknoten im HDInsight-Cluster ausgeführt. Eine Skalierung auf mehrere Knoten ist nicht möglich. Mehrere Instanzen der .NET-Aktivität können auf verschiedenen Knoten des HDInsight-Clusters parallel ausgeführt werden.

Wenn Sie das Lernprogramm [Erste Schritte mit Azure Data Factory][adfgetstarted] mit der exemplarischen Vorgehensweise für das [Verwenden von Pig und Hive mit Azure Data Factory][hivewalkthrough] erweitert haben, können Sie die Erstellung dieses verknüpften Diensts überspringen und den verknüpften Dienst verwenden, der bereits in der ADFTutorialDataFactory vorhanden ist.


#### So verwenden Sie einen bedarfsgesteuerten HDInsight-Cluster

1. Klicken Sie im **Azure-Portal** auf der Startseite der Data Factory auf **Erstellen und bereitstellen**.
2. Klicken Sie im Data Factory-Editor auf der Befehlsleiste auf **Neu -> Compute**, und wählen Sie im Menü **On-Demand-HDInsight-Cluster** aus.
2. Führen Sie im JSON-Skript folgende Schritte aus: 
	1. Geben Sie für die Eigenschaft **clusterSize** die Größe des HDInsight-Clusters an.
	2. Geben Sie für die Eigenschaft **jobsContainer** den Namen des Standardcontainers an, in dem die Clusterprotokolle gespeichert werden sollen. Geben Sie im Rahmen dieses Lernprogramms **adfjobscontainer** an.
	3. Geben Sie für die **timeToLive**-Eigenschaft an, wie lange sich der Cluster im Leerlauf befinden darf, bevor er gelöscht wird. 
	4. Geben Sie für die **version**-Eigenschaft die HDInsight-Version an, die Sie verwenden möchten. Wenn Sie diese Eigenschaft ausschließen, wird die neueste Version verwendet.  
	5. Geben Sie für **linkedServiceName** den Dienst **StorageLinkedService** an, den Sie im Lernprogramm "Erste Schritte" erstellt haben. 

		{ "name": "HDInsightOnDemandLinkedService", "properties": { "type": "HDInsightOnDemand", "typeProperties": { "clusterSize": "1", "timeToLive": "00:05:00", "version": "3.1", "linkedServiceName": "StorageLinkedService" } } }

2. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.
   
#### So verwenden Sie Ihren eigenen HDInsight-Cluster: 

1. Klicken Sie im **Azure-Portal** auf der Startseite der Data Factory auf **Erstellen und bereitstellen**.
2. Klicken Sie im **Data Factory-Editor** auf der Befehlsleiste auf **Neu -> Compute**, und wählen Sie im Menü **HDInsight-Cluster** aus.
2. Führen Sie im JSON-Skript folgende Schritte aus: 
	1. Geben Sie für die **clusterUri**-Eigenschaft die URL für Ihr HDInsight ein. Beispiel: https://<clustername>.azurehdinsight.net/     
	2. Geben Sie für die **UserName**-Eigenschaft den Benutzernamen ein, der Zugriff auf den HDInsight-Cluster hat.
	3. Geben Sie für die **Password**-Eigenschaft das Kennwort für das Benutzerkonto ein. 
	4. Geben Sie für die **LinkedServiceName**-Eigenschaft das **StorageLinkedService**-Element ein. Dies ist der verknüpfte Dienst, den Sie im Lernprogramm "Erste Schritte" erstellt haben. 

2. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.

### Erstellen einer Ausgabetabelle

1. Klicken Sie im **Data Factory-Editor** auf **Neues Dataset** und dann auf der Befehlsleiste auf **Azure-Blobspeicher**.
2. Ersetzen Sie das JSON-Skript im rechten Bereich durch das folgende JSON-Skript:

		{
		  "name": "OutputTableForCustom",
		  "properties": {
		    "type": "AzureBlob",
		    "linkedServiceName": "StorageLinkedService",
		    "typeProperties": {
		      "folderPath": "adftutorial/customactivityoutput/{Slice}",
		      "partitionedBy": [
		        {
		          "name": "Slice",
		          "value": {
		            "type": "DateTime",
		            "date": "SliceStart",
		            "format": "yyyyMMddHH"
		          }
		        }
		      ]
		    },
		    "availability": {
		      "frequency": "Hour",
		      "interval": 1
		    }
		  }
		}

 	Ausgabespeicherort ist **adftutorial/customactivityoutput/YYYYMMDDHH/**, wobei YYYYMMDDHH Jahr, Monat, Datum und Stunde der Sliceerstellung angibt. Ausführlichere Informationen finden Sie in der [Entwicklerreferenz][adf-developer-reference].

2. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um die Tabelle bereitzustellen.


### Erstellen Sie eine Pipeline, die die benutzerdefinierte Aktivität verwendet, und führen Sie sie aus.
   
1. Klicken Sie im Data Factory-Editor auf der Befehlsleiste auf die Schaltfläche **Neue Pipeline**. Wenn der Befehl nicht angezeigt wird, klicken Sie auf **... (Auslassungspunkte)**, um ihn anzuzeigen. 
2. Ersetzen Sie das JSON-Skript im rechten Bereich durch das folgende JSON-Skript. Wenn Sie einen eigenen Cluster verwenden möchten und die Schritte zum Erstellen des verknüpften Diensts **HDInsightLinkedService** ausgeführt haben, ersetzen Sie im folgenden JSON-Code **HDInsightOnDemandLinkedService** durch **HDInsightLinkedService**. 
		
		{
		  "name": "ADFTutorialPipelineCustom",
		  "properties": {
		    "description": "Use custom activity",
		    "activities": [
		      {
		        "Name": "MyDotNetActivity",
		        "Type": "DotNetActivity",
		        "Inputs": [
		          {
		            "Name": "EmpTableFromBlob"
		          }
		        ],
		        "Outputs": [
		          {
		            "Name": "OutputTableForCustom"
		          }
		        ],
		        "LinkedServiceName": "HDInsightOnDemandLinkedService",
		        "typeProperties": {
		          "AssemblyName": "MyDotNetActivity.dll",
		          "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
		          "PackageLinkedService": "StorageLinkedService",
		          "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
		          "extendedProperties": {
		            "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
		          }
		        },
		        "Policy": {
		          "Concurrency": 1,
		          "ExecutionPriorityOrder": "OldestFirst",
		          "Retry": 3,
		          "Timeout": "00:30:00",
		          "Delay": "00:00:00"
		        }
		      }
		    ],
		    "start": "2015-02-13T00:00:00Z",
		    "end": "2015-02-14T00:00:00Z",
		    "isPaused": false
		  }
		}

	Ersetzen Sie den Wert **StartDateTime** durch den Zeitpunkt drei Tage vor dem aktuellen Tag und den Wert **EndDateTime** durch den aktuellen Tag. "StartDateTime" und "EndDateTime" müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. Die Ausgabetabelle soll jeden Tag erstellt werden, deshalb werden drei Slices erstellt.

	Beachten Sie Folgendes:

	- Im Abschnitt "activities" gibt es eine Aktivität vom Typ **DotNetActivity**.
	- Verwenden Sie die gleiche Eingabetabelle **EmpTableFromBlob**, die Sie im Lernprogramm "Erste Schritte" verwendet haben.
	- Verwenden Sie eine neue Ausgabetabelle **OutputTableForCustom**, die Sie im nächsten Schritt erstellen.
	- **AssemblyName** wird auf den Namen der DLL (**MyActivities.dll**) festgelegt.
	- **EntryPoint** wird auf **MyDotNetActivityNS.MyDotNetActivity** festgelegt.
	- **PackageLinkedService** wird auf das **MyBlobStore**-Element festgelegt, das als Teil des Lernprogramms [Erste Schritte mit Azure Data Factory][adfgetstarted] erstellt wurde. Dieser Blobspeicher enthält die ZIP-Datei mit der benutzerdefinierten Aktivität.
	- **PackageFile** wird auf **customactivitycontainer/MyCustomActivity.zip** festgelegt.
     
4. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um die Pipeline bereitzustellen.
8. Stellen Sie sicher, dass die Ausgabedateien im Blobspeicher im **adftutorial**-Container generiert werden.

	![Ausgabe der benutzerdefinierten Aktivität][image-data-factory-ouput-from-custom-activity]

9. Wenn Sie die Ausgabedatei öffnen, sollte die Ausgabe ähnlich der folgenden angezeigt werden:
	
	adftutorial/,EMP.txt,2,WORKERNODE0,03/27/2015 19:23:28

	(Blobspeicherort), (Name des Blobs), (Anzahl der Zeilen im Blob) (Knoten, auf dem die Aktivität ausgeführt wurde), (Datum-/Uhrzeitstempel)

10.	Verwenden Sie das [Azure-Portal][azure-preview-portal] oder die Azure PowerShell-Cmdlets zum Überwachen Ihrer Data Factory, Pipelines und Datasets. Sie können die Meldungen vom **ActivityLogger** im Code für die benutzerdefinierte Aktivität in den Protokollen (insbesondere "user-0.log") sehen, die Sie aus dem Portal oder mithilfe von Cmdlets herunterladen können.

	![Herunterladen von Protokollen aus einer benutzerdefinierten Aktivität][image-data-factory-download-logs-from-custom-activity]
	
   
Unter [Überwachen und Verwalten von Pipelines](data-factory-monitor-manage-pipelines.md) finden Sie eine ausführliche Anleitung zum Überwachen von Datasets und Pipelines.

## Aktualisieren einer benutzerdefinierten Aktivität
Wenn Sie den Code für die benutzerdefinierte Aktivität aktualisieren, führen Sie einen Buildvorgang aus. Laden Sie die ZIP-Datei mit den neuen Binärdateien in den Blobspeicher hoch.
    
## <a name="AzureBatch"></a> Verwenden des mit Azure Batch verknüpften Diensts 
> [AZURE.NOTE]Unter [Technische Übersicht zu Azure Batch][batch-technical-overview] finden Sie eine Übersicht über den Azure Batch-Dienst. Unter [Erste Schritte mit der Azure Batch-Bibliothek für .NET][batch-get-started] werden die ersten Schritte mit dem Azure Batch-Dienst gezeigt.

Sie können Ihre benutzerdefinierten .NET-Aktivitäten mithilfe von Azure Batch als eine Compute-Ressource ausführen. Sie müssen Ihren eigenen Azure Batch-Pools erstellen und die Anzahl der virtuellen Computer (VM) zusammen mit anderen Konfigurationen angeben. Azure Batch-Pools bieten Kunden die folgenden Funktionen:

1. Pools erstellen, die von einem bis zu tausenden Kernen enthalten
2. Automatische Skalierung der VM-Anzahl basierend auf einer Formel
3. Unterstützt VMs beliebiger Größe
4. Konfigurierbare Anzahl an Aufgaben pro VM
5. Platzierung einer unbegrenzten Anzahl von Aufgaben in der Warteschlange


Es folgen die allgemeinen Schritte zur Verwendung des mit Azure Batch verknüpften Diensts in der exemplarischen Vorgehensweise, die im vorherigen Abschnitt beschrieben wurde:

1. Erstellen Sie mit dem Azure-Verwaltungsportal ein Azure Batch-Konto. Eine Anleitung hierzu finden Sie unter [Azure Batch – Technische Übersicht][batch-create-account]. Notieren Sie den Azure Batch-Kontonamen und -Kontoschlüssel. 

	Sie können auch das [New-AzureBatchAccount][new-azure-batch-account]-Cmdlet verwenden, um ein Azure Batch-Konto zu erstellen. Ausführliche Anweisungen zur Verwendung dieses Cmdlets finden Sie unter [Verwenden von Azure PowerShell zum Verwalten des Azure Batch-Kontos][azure-batch-blog]. 
2. Erstellen Sie einen Azure Batch-Pool. Sie können zum Erstellen eines Azure Batch-Pools das [Azure Batch-Explorer-Tool][batch-explorer] herunterladen und verwenden (oder) die [Azure Batch-Bibliothek für .NET][batch-net-library]. Unter [Azure Batch-Explorer – Beispiel für eine exemplarische Vorgehensweise][batch-explorer-walkthrough] finden Sie Schritt-für-Schritt-Anweisungen zur Verwendung des Azure Batch-Explorers.
	
	Sie können auch das [New-AzureBatchPool][new-azure-batch-pool]-Cmdlet verwenden, um einen Azure Batch-Pool zu erstellen.

2. Erstellen Sie mithilfe der folgenden JSON-Vorlage einen mit Azure Batch verknüpften Dienst. Der Data Factory-Editor bietet Ihnen als Einstieg eine ähnliche Vorlage. Geben Sie im JSON-Codeausschnitt einen Azure Batch-Kontonamen, -Kontoschlüssel und -Poolnamen an.

		{
		  "name": "AzureBatchLinkedService",
		  "properties": {
		    "type": "AzureBatch",
		    "typeProperties": {
		      "accountName": "<Azure Batch account name>",
		      "accessKey": "<Azure Batch account key>",
		      "poolName": "<Azure Batch pool name>",
		      "linkedServiceName": "<Specify associated storage linked service reference here>"
		    }
		  }
		}

	> [AZURE.NOTE]Erweitern Sie den Namen Ihres Batch-Kontos für die Eigenschaft **accountName** um "**.<Name der Region**". Beispiel: "mybatchaccount.eastus". Außerdem kann, wie nachfolgend gezeigt, auch der batchUri-Endpunkt angegeben werden.

		accountName: "adfteam",
		batchUri: "https://eastus.batch.azure.com",
 
	Im MSDN-Thema [Mit Azure Batch verknüpfter Dienst](https://msdn.microsoft.com/library/mt163609.aspx) finden Sie Beschreibungen dieser Eigenschaften.

2.  Öffnen Sie im Data Factory-Editor die JSON-Definition für die Pipeline, die Sie in der exemplarischen Vorgehensweise erstellt haben, und ersetzen Sie **HDInsightLinkedService** durch **AzureBatchLinkedService**.
3.  Sie können die Start- und Endzeiten für die Pipeline bei Bedarf ändern, damit Sie das Szenario mit dem Azure Batch-Dienst testen können. 
4.  Wie im folgenden Diagramm dargestellt, können Sie im Azure Batch-Explorer die Azure Batch-Aufgaben im Zusammenhang mit der Verarbeitung der Slices sehen.

	![Azure Batch-Aufgaben][image-data-factory-azure-batch-tasks]

> [AZURE.NOTE]Data Factory unterstützt im Gegensatz zu HDInsight für Azure Batch keine bedarfsorientierte Option. Sie können nur Ihren eigenen Azure Batch-Pool in einer Azure Data Factory verwenden.

## Weitere Informationen

[Azure Data Factory-Aktualisierungen: Ausführen von benutzerdefinierten ADF-.NET-Aktivitäten mit Azure Batch](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/).

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-technical-overview.md/#batch-concepts
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[troubleshoot]: data-factory-troubleshoot.md
[data-factory-introduction]: data-factory-introduction.md
[azure-powershell-install]: https://github.com/Azure/azure-sdk-tools/releases


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456

[new-azure-batch-account]: https://msdn.microsoft.com/library/mt125880.aspx
[new-azure-batch-pool]: https://msdn.microsoft.com/library/mt125936.aspx
[azure-batch-blog]: http://blogs.technet.com/b/windowshpc/archive/2014/10/28/using-azure-powershell-to-manage-azure-batch-account.aspx

[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: data-factory-get-started.md
[hivewalkthrough]: data-factory-data-transformation-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png
 

<!---HONumber=September15_HO1-->