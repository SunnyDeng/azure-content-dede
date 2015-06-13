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
	ms.date="06/02/2015" 
	ms.author="spelluru"/>

# Verwenden von benutzerdefinierten Aktivitäten in einer Azure Data Factory-Pipeline
Azure Data Factory unterstützt integrierte Aktivitäten wie z. B. **Kopie Aktivität** und **HDInsight-Aktivität** Pipelines zum Verschieben und Verarbeiten von Daten verwendet werden sollen. Sie können auch eine benutzerdefinierte Aktivität für .NET mit Ihren eigenen Transformation/Verarbeitungslogik erstellen und verwenden Sie die Aktivität in einer Pipeline. Sie können konfigurieren, dass die Aktivität ausgeführt wird, entweder eine **Clusterversionen** Cluster oder eine **Azure Batch** Service.

Dieser Artikel beschreibt, wie Sie eine benutzerdefinierte Aktivität erstellen und in einer Azure Data Factory-Pipeline verwenden. Darüber hinaus bietet er eine ausführliche exemplarische Vorgehensweise mit einer schrittweisen Anleitung zum Erstellen und Verwenden einer benutzerdefinierten Aktivität. Die exemplarische Vorgehensweise verwendet den HDInsight verknüpft. Verwenden der Azure-Batch Dienst stattdessen verknüpft werden, erstellen Sie einen verknüpften Dienst vom Typ **AzureBatchLinkedService** und verwenden sie im Abschnitt "Aktivität" der JSON-Pipeline (** LinkedServiceName **). Finden Sie unter der [Azure Batch verknüpften Dienst](#AzureBatch) Abschnitt, um Informationen zur Verwendung von Azure-Batch mit der benutzerdefinierten Aktivität.

## Voraussetzungen
Laden Sie das aktuelle [NuGet-Paket für Azure Data Factory][nuget-package] und installieren Sie es. Anweisungen finden Sie in der [Exemplarische Vorgehensweise](#SupportedSourcesAndSinks) in diesem Artikel.

## Erstellen einer benutzerdefinierten Aktivität

So erstellen Sie eine benutzerdefinierte Aktivität:
 
1.	Erstellen einer **-Klassenbibliothek** -Projekt in Visual Studio 2013.
3. Fügen Sie die folgenden using-Anweisungen am Anfang der Quelldatei in der Klassenbibliothek ein.
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. Aktualisieren Sie die Klasse implementiert die **IDotNetActivity** Schnittstelle.
	<ol type='a'>
	<li>
		Leiten Sie die Klasse von <b>IDotNetActivity</b>.
		<br/>
		Beispiel: <br/>
		öffentliche Klasse <b>MyDotNetActivity: IDotNetActivity</b>
	</li>

	<li>
		Implementieren der <b>Ausführen</b> Methode <b>IDotNetActivity</b> Schnittstelle
	</li>

</ol>
5. Kompilieren Sie das Projekt.


## Verwenden die benutzerdefinierte Aktivität in einer pipeline
So verwenden Sie die benutzerdefinierte Aktivität in einer Pipeline

1.	**Komprimieren** alle Binärdateien aus dem **"bin\Debug"** oder **Bin\release** Ordner für das Projekt ausgegeben. 
2.	**Hochladen die Zip** Datei als Blob zu Ihrem **Azure Blob-Speicher**. 
3.	Update der **pipeline JSON** finden Sie in der Zip-Datei, benutzerdefinierte Aktivität DLL, die Activity-Klasse und das Blob mit der Zip-Datei in der Pipeline JSON-Datei. In der JSON-Datei:
	<ol type ="a">
	<li><b>Aktivitätstyp</b> sollte festgelegt werden, um <b>DotNetActivity</b>.</li>
	<li><b>AssemblyName</b> ist der Name des Ausgabe-DLL von Visual Studio-Projekt.</li>
	<li><b>EntryPoint</b> gibt die <b>Namespace</b> und <b>Name</b> von der <b>Klasse</b> implementiert die <b>IDotNetActivity</b> Schnittstelle.</li>
	<li><b>PackageLinkedService</b> ist der verknüpfte Dienst, der auf das Blob verweist, die Zip-Datei enthält. </li>
	<li><b>PackageFile</b> gibt den Speicherort und den Namen der Zip-Datei, die in der Azure-Blob-Speicher hochgeladen wurde.</li>
	<li><b>LinkedServiceName</b> ist der Name des verknüpften-Diensts, der einen HDInsight-Cluster verknüpft (on Demand oder eigene) auf eine Daten-Factory. Die benutzerdefinierte Aktivität wird als Nur-Zuordnungs-Auftrag für den angegebenen HDInsight-Cluster ausgeführt.</li>
</ol>**JSON-Ausschnitt**

		"Name": "MyDotNetActivity",
    	"Type": "DotNetActivity",
    	"Inputs": [{"Name": "EmpTableFromBlob"}],
    	"Outputs": [{"Name": "OutputTableForCustom"}],
		"LinkedServiceName": "myhdinsightcluster",
    	"Transformation":
    	{
	    	"AssemblyName": "MyDotNetActivity.dll",
    	    "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",

## Aktualisieren einer benutzerdefinierten Aktivität
Wenn Sie den Code für die benutzerdefinierte Aktivität aktualisieren, erstellen und Hochladen der Zipdatei, die neue Binärdateien in den blobspeicher enthält.

## <a name="walkthrough" /> Exemplarische Vorgehensweise
Diese exemplarische Vorgehensweise bietet schrittweise Anleitungen zum Erstellen einer benutzerdefinierten Aktivität und mithilfe der Aktivität in einer Pipeline Azure Data-Factory. Diese exemplarische Vorgehensweise baut auf dem Lernprogramm aus der [Erste Schritte mit Azure Data Factory][adfgetstarted]. Wenn Sie die benutzerdefinierte Aktivität in Aktion sehen möchten, müssen Sie zuerst das Erste-Schritte-Lernprogramm durchlaufen und diese exemplarischen Vorgehensweise ausführen.

**Voraussetzungen:**


- Lernprogramm aus [Erste Schritte mit Azure Data Factory][adfgetstarted]. Sie müssen das Lernprogramm aus diesem Artikel zuvor in dieser exemplarischen Vorgehensweise abschließen.
- Visual Studio 2012 oder 2013
- Herunterladen und installieren [Azure .NET SDK][azure-developer-center]
- Laden Sie das aktuelle [NuGet-Paket für Azure Data Factory][nuget-package] und installieren Sie es. Entsprechende Anweisungen sind in der exemplarischen Vorgehensweise enthalten.
- Herunterladen und Installieren des NuGet-Pakets für Azure-Speicher. Anweisungen sind in der exemplarischen Vorgehensweise enthalten, daher können Sie diesen Schritt überspringen.

## Schritt 1: Erstellen einer benutzerdefinierten Aktivität

1.	Erstellen Sie ein .NET-Klassenbibliotheksprojekt.
	<ol type="a">
	<li>Starten Sie <b>Visual Studio 2012</b> oder <b>Visual Studio 2013</b>.</li>
	<li>Klicken Sie auf <b>Datei</b>, zeigen Sie auf <b>Neu</b>, und klicken Sie auf <b>Projekt</b>.</li> 
	<li>Erweitern Sie <b>Vorlagen</b>, und wählen Sie <b>Visual C#-</b>. In dieser exemplarischen Vorgehensweise verwenden Sie C#, Sie können jedoch jede .NET-Sprache verwenden, um benutzerdefinierte Aktivität zu entwickeln.</li> 
	<li>Wählen Sie <b>-Klassenbibliothek</b> aus der Liste der Projekttypen auf der rechten Seite.</li>
	<li>Geben Sie <b>MyDotNetActivity</b> für den <b>Namen</b>.</li> 
	<li>Wählen Sie <b>C:\ADFGetStarted</b> für die <b>Speicherort</b>.</li>
	<li>Klicken Sie auf <b>OK</b>, um das Projekt zu erstellen.</li>
</ol>
2.  Klicken Sie auf <b>Tools</b>, zeigen Sie auf <b>NuGet-Paket-Manager-</b>, und klicken Sie auf <b>-Paket-Manager-Konsole</b>.
3.	In der <b>-Paket-Manager-Konsole</b>, führen Sie den folgenden Befehl zum Importieren von <b>Microsoft.Azure.Management.DataFactories</b>. 

		Install-Package Microsoft.Azure.Management.DataFactories –Pre

3.	In der <b>-Paket-Manager-Konsole</b>, führen Sie den folgenden Befehl zum Importieren von <b>Microsoft.DataFactories.Runtime</b>. Ersetzen Sie den Ordner durch den Speicherort, der das heruntergeladene Data Factory NuGet-Paket enthält.

		Install-Package Microsoft.DataFactories.Runtime –Pre

4. Azure Storage NuGet-Paket in dem Projekt zu importieren.

		Install-Package Azure.Storage

5. Fügen Sie die folgenden **mit** Anweisungen zur Quelldatei im Projekt.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. Ändern des Namens der **Namespace**  **MyDotNetActivityNS**.

		namespace MyDotNetActivityNS

7. Ändern Sie den Namen der Klasse, die **MyDotNetActivity** und leiten Sie ihn von der **IDotNetActivity** Schnittstelle wie unten dargestellt.

		public class MyDotNetActivity : IDotNetActivity

8. Implementieren (hinzufügen) der **Ausführen** Methode der **IDotNetActivity** Schnittstelle zu den **MyDotNetActivity** Klasse, und kopieren Sie den folgenden Beispielcode an die Methode.

	Die **InputTables** und **OutputTables** Parameter Eingabe- und Tabellen für die Aktivität darstellen, wie die Namen. Meldungen, die sich bei der Verwendung der **Protokollierung** Objekt in der Protokolldatei, die Sie aus dem Azure-Portal oder mithilfe von Cmdlets herunterladen können. Die **ExtendedProperties** Wörterbuch enthält eine Liste der erweiterten Eigenschaften, die Sie in das JSON-Datei für die Aktivität und deren Werte angeben.

	Im folgenden Beispielcode wird die Anzahl der Zeilen in der Eingabe-Blob und den folgenden Inhalt im Blob Ausgabe erzeugt: Pfad zur BLOBs, die Anzahl der Zeilen in der der Computer, auf dem die Aktivität ausgeführt, aktuelle Datum-/ Uhrzeit wurde-Blob.

        public IDictionary<string, string> Execute(
                    IEnumerable<ResolvedTable> inputTables, 
                    IEnumerable<ResolvedTable> outputTables, 
                    IDictionary<string, string> extendedProperties, 
                    IActivityLogger logger)
        {
            string output = string.Empty;

            logger.Write(TraceEventType.Information, "Before anything...");

            logger.Write(TraceEventType.Information, "Printing dictionary entities if any...");
            foreach (KeyValuePair<string, string> entry in extendedProperties)
            {
                logger.Write(TraceEventType.Information, "<key:{0}> <value:{1}>", entry.Key, entry.Value);
            }

            foreach (ResolvedTable inputTable in inputTables)
            {
                string connectionString = GetConnectionString(inputTable.LinkedService);
                string folderPath = GetFolderPath(inputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Reading blob from: {0}", folderPath);

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
                                        logger.Write(TraceEventType.Information, "First line: [{0}]", line);
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
            }

            foreach (ResolvedTable outputTable in outputTables)
            {
                string connectionString = GetConnectionString(outputTable.LinkedService);
                string folderPath = GetFolderPath(outputTable.Table);

                if (String.IsNullOrEmpty(connectionString) ||
                    String.IsNullOrEmpty(folderPath))
                {
                    continue;
                }

                logger.Write(TraceEventType.Information, "Writing blob to: {0}", folderPath);

                CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
                Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + Guid.NewGuid() + ".csv");

                CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
                outputBlob.UploadText(output);

            }
            return new Dictionary<string, string>();

        }

9. Fügen Sie die folgenden Hilfsmethoden hinzu. Die **Ausführen** -Methode ruft diese Hilfsmethoden. Die **GetConnectionString** Methode die Azure-Speicher-Verbindungszeichenfolge abgerufen und die **GetFolderPath** -Methode ruft die Blob-Speicherort ab.


        private static string GetConnectionString(LinkedService asset)
        {
            AzureStorageLinkedService storageAsset;
            if (asset == null)
            {
                return null;
            }

            storageAsset = asset.Properties as AzureStorageLinkedService;
            if (storageAsset == null)
            {
                return null;
            }

            return storageAsset.ConnectionString;
        }

        private static string GetFolderPath(Table dataArtifact)
        {
            AzureBlobLocation blobLocation;
            if (dataArtifact == null || dataArtifact.Properties == null)
            {
                return null;
            }

            blobLocation = dataArtifact.Properties.Location as AzureBlobLocation;
            if (blobLocation == null)
            {
                return null;
            }

            return blobLocation.FolderPath;
        }
   


10. Kompilieren Sie das Projekt. Klicken Sie auf **Erstellen** aus dem Menü und auf **Projektmappe**.
11. Starten Sie **Windows Explorer**, und navigieren Sie zu **"bin\Debug"** oder **Bin\release** Ordner je nach Typ des Builds.
12. Erstellen Sie eine Zipdatei **MyDotNetActivity.zip** enthalten alle Binärdateien der <project folder>Ordner \bin\Debug.
13. Hochladen **MyDotNetActivity.zip** als Blob in den blobcontainer: **Customactvitycontainer** in Azure blob-Speicher, der **MyBlobStore** verknüpft-Dienst in der **ADFTutorialDataFactory** verwendet. Erstellen Sie den blobcontainer **Blobcustomactivitycontainer** wenn sie nicht bereits vorhanden ist. 


## Schritt 2: Verwenden der benutzerdefinierten Aktivität in einer pipeline
Hier sind die Schritte, die Sie in diesem Schritt ausgeführt werden:

1. Erstellen Sie einen verknüpften Dienst für das HDInsight-Cluster, auf dem die benutzerdefinierte Aktivität als nur-Map-Auftrag ausgeführt wird. 
2. Erstellen Sie eine Ausgabetabelle, die die Pipeline in diesem Beispiel erzeugt wird.
3. Erstellen Sie und führen Sie der Pipeline aus, die die benutzerdefinierte Aktivität verwendet, die Sie in Schritt 1 erstellt haben. 
 
### Erstellen Sie einen verknüpften Dienst für HDInsight-Cluster, die zum Ausführen der benutzerdefinierten Aktivität verwendet werden
Der Azure Data Factory-Dienst unterstützt das Erstellen eines Clusters bei Bedarf und verwendet ihn zum Verarbeiten der Eingabe zum Erzeugen von Ausgabedaten. Sie können auch Ihren eigenen Cluster für den gleichen Zweck verwenden. Wenn Sie den bedarfsgesteuerten HDInsight-Cluster verwenden, wird für jeden Slice ein Cluster erstellt. Bei Verwendung eines eigenen HDInsight-Clusters ist der Cluster sofort zur Sliceverarbeitung bereit. Aus diesem Grund werden bei Verwendung des bedarfsgesteuerten Clusters die Ausgabedaten möglicherweise nicht so schnell angezeigt wie bei der Verwendung Ihres eigenen Clusters.

> [AZURE.NOTE]Wird ausgeführt während der Laufzeit eine Instanz einer Aktivität .NET nur auf einem Arbeitsthread-Knoten im HDInsight-Cluster. Es kann nicht auf mehreren Knoten ausführen skaliert werden. Mehrere Instanzen der Aktivität von .NET können auf verschiedenen Knoten des Clusters HDInsight parallel ausführen.

Wenn Sie erweitert haben die [Erste Schritte mit Azure Data Factory][adfgetstarted] Lernprogramm mit der exemplarischen Vorgehensweise aus [verwenden Pig und Hive Azure Data Factory][hivewalkthrough], können Sie Entstehung dieser verknüpften Dienst zu überspringen und den verknüpften Dienst in der ADFTutorialDataFactory bereits.


#### So verwenden Sie einen bedarfsgesteuerten HDInsight-Cluster

1. In der **Azure Portal**, klicken Sie auf **Autor und Bereitstellung** auf der Startseite des Daten-Factory.
2. Klicken Sie im Editor für die Daten-Factory **neue Compute** aus der Befehlsleiste und wählen Sie **On-Demand-HDInsight-Cluster** aus dem Menü.
2. Gehen Sie in das JSON-Skript: 
	1. Für die **ClusterSize** -Eigenschaft, geben Sie die Größe des HDInsight-Clusters.
	2. Für die **JobsContainer** -Eigenschaft, geben Sie den Namen der Standardcontainer, in denen die Clusterprotokolle gespeichert werden. Geben Sie im Rahmen dieses Lernprogramms **Adfjobscontainer**.
	3. Für die **TimeToLive** -Eigenschaft angeben, wie lange der Kunde im Leerlauf befinden darf, bevor es gelöscht wird. 
	4. Für die **Version** -Eigenschaft, geben Sie die HDInsight-Version, die Sie verwenden möchten. Wenn Sie diese Eigenschaft ausschließen, wird die neueste Version verwendet.  
	5. Für die **LinkedServiceName**, geben Sie **StorageLinkedService** dass Sie erhalten erstellt hätten-Schritte-Lernprogramm. 

			{
		    	"name": "HDInsightOnDemandLinkedService",
				    "properties": {
		    	    "type": "HDInsightOnDemandLinkedService",
		    	    "clusterSize": "4",
		    	    "jobsContainer": "adfjobscontainer",
		    	    "timeToLive": "00:05:00",
		    	    "version": "3.1",
		    	    "linkedServiceName": "StorageLinkedService"
		    	}
			}

2. Klicken Sie auf **Bereitstellen** auf der Befehlsleiste, um den verknüpften Dienst bereitzustellen.
   
#### So verwenden Sie Ihren eigenen HDInsight-Cluster: 

1. In der **Azure Portal**, klicken Sie auf **Autor und Bereitstellung** auf der Startseite des Daten-Factory.
2. In der **Data Factory-Editor**, klicken Sie auf **neue Compute** aus, und wählen Sie die Befehlsleiste **HDInsight-Cluster** aus dem Menü.
2. Gehen Sie in das JSON-Skript: 
	1. Für die **ClusterUri** -Eigenschaft, geben Sie die URL für Ihre HDInsight. Zum Beispiel: https://<clustername>.azurehdinsight.net/     
	2. Für die **Benutzername** -Eigenschaft, geben Sie den Benutzernamen, der Zugriff auf den HDInsight-Cluster verfügt.
	3. Für die **Kennwort** -Eigenschaft, geben Sie das Kennwort für den Benutzer. 
	4. Für die **LinkedServiceName** -Eigenschaft, geben Sie **StorageLinkedService**. Dies ist die verknüpfte Dienst, den Sie in Get-Schritte-Lernprogramm erstellt haben. 

2. Klicken Sie auf **Bereitstellen** auf der Befehlsleiste, um den verknüpften Dienst bereitzustellen.

### Erstellen Sie eine Ausgabetabelle

1. In der **Data Factory-Editor**, klicken Sie auf **Neues Dataset**, und klicken Sie dann auf **Azure Blob-Speicher** in der Befehlsleiste.
2. Ersetzen Sie das Skript JSON im rechten Bereich mit dem folgenden JSON-Skript:

		{
    		"name": "OutputTableForCustom",
    		"properties":
    		{
        		"location": 
        		{
					"type": "AzureBlobLocation",
					"folderPath": "adftutorial/customactivityoutput/{Slice}",
					"partitionedBy": [ { "name": "Slice", "value": { "type": "DateTime", "date": "SliceStart", "format": "yyyyMMddHH" } }],

					"linkedServiceName": "MyBlobStore"
        		},
        		"availability": 
        		{
            		"frequency": "hour",
            		"interval": 1
        		}   
    		}
		}


 	Ausgabespeicherort ist **Adftutorial/Customactivityoutput/JJJJMMTTHH/** JJJJMMTTHH darin, der Jahr, Monat, Datum und Stunde des Segments erzeugt wird. Finden Sie unter [Entwicklerreferenz][adf-developer-reference] Details.

2. Klicken Sie auf **Bereitstellen** auf der Befehlsleiste in der Tabelle bereitstellen.


### Erstellen Sie und führen Sie der Pipeline, die die benutzerdefinierte Aktivität verwendet aus.
   
1. Klicken Sie im Editor für die Daten-Factory **neue Pipeline** auf der Befehlsleiste. Wenn Sie den Befehl nicht angezeigt werden, klicken Sie auf **... (Mit den drei Punkten)** um es anzuzeigen. 
2. Ersetzen Sie die JSON im rechten Bereich mit folgendem Skript JSON. Wenn Sie Ihre eigenen Cluster verwenden möchten, und die Schritte zum Erstellen der **HDInsightLinkedService** -Dienst verknüpft ersetzen **HDInsightOnDemandLinkedService** mit **HDInsightLinkedService** in den folgenden JSON. 

		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyDotNetActivity",
                     	"Type": "DotNetActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "HDInsightLinkedService",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyDotNetActivity.dll",
                            "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
                            "ExtendedProperties":
							{
								"SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))"
							}
                      	},
                        "Policy":
                        {
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

	> [AZURE.NOTE]Ersetzen Sie **StartDateTime** -Wert mit den drei Tage vor dem aktuellen Tag und **EndDateTime** Wert mit dem aktuellen Tag. Ist das StartDateTime und EndDateTime [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601). Zum Beispiel: 2014-10-14T16:32:41Z. Die Ausgabetabelle soll jeden Tag erstellt werden, deshalb werden drei Slices erstellt.

	Beachten Sie Folgendes:

	- Es gibt eine Aktivität im Abschnitt Aktivitäten und ist vom Typ: **DotNetActivity**.
	- Verwenden Sie die gleiche Eingabetabelle **EmpTableFromBlob** von Ihnen erhalten verwendeten Schritte-Lernprogramm.
	- Verwenden Sie eine neue Ausgabetabelle **OutputTableForCustom** die Sie im nächsten Schritt erstellen.
	- **AssemblyName** ist auf den Namen der DLL festlegen: **MyActivities.dll**.
	- **EntryPoint** minFreeThreads auf **MyDotNetActivityNS.MyDotNetActivity**.
	- **PackageLinkedService** minFreeThreads auf **MyBlobStore** erstellt wurde, als Teil des Lernprogramms aus [Erste Schritte mit Azure Data Factory][adfgetstarted]. Dieser Blob-Speicher enthält die ZIP-Datei mit der benutzerdefinierten Aktivität.
	- **PackageFile** minFreeThreads auf **customactivitycontainer/MyDotNetActivity.zip**.
     
4. Klicken Sie auf **Bereitstellen** auf der Befehlsleiste auf die Pipeline bereitstellen.
8. Stellen Sie sicher, dass Ausgabedateien, im Blob-Speicher in erstellt werden der **Adftutorial** Container.

	![Ausgabe von benutzerdefinierten Aktivität][image-data-factory-ouput-from-custom-activity]

9. Wenn Sie die Ausgabedatei öffnen, sollte die Ausgabe ähnlich der folgenden angezeigt werden:
	
	adftutorial/,EMP.txt,2,WORKERNODE0,03/27/2015 19:23:28

	(blob-Speicherort), (Name des Blob) (Anzahl der Zeilen im Blob) (Knoten auf dem die Aktivität ausgeführt wurde), (Datums-/ Zeitstempel)

10.	Verwenden der [Azure Portal][azure-preview-portal] oder Azure-PowerShell-Cmdlets, um Ihre Daten Factory, Pipelines und Datasets zu überwachen. Sehen Sie Nachrichten aus den **ActivityLogger** in den Code für die benutzerdefinierte Aktivität in den Protokollen können Sie auf das Portal oder mithilfe von Cmdlets herunterladen.

	![Herunterladen von Protokollen aus einer benutzerdefinierten Aktivität][image-data-factory-download-logs-from-custom-activity]
   
Finden Sie unter [Erste Schritte mit Azure Data Factory][adfgetstarted] Ausführliche Schritte zum Überwachen von Datasets und Pipelines.
    
## <a name="AzureBatch"></a> Mithilfe der Azure-Batch Dienst verknüpft 
> [AZURE.NOTE]Finden Sie unter [– Technische Übersicht zu Azure Batch][batch-technical-overview] service eine Übersicht über den Azure-Batch und finden Sie unter [Erste Schritte mit der Azure-Batch-Bibliothek für .NET][batch-get-started] mit dem Azure-Batch-Dienst Schnelleinstieg.

Hier sind die allgemeinen Schritte zur Verwendung von Azure Batch verknüpften Service in der exemplarischen Vorgehensweise im vorherigen Abschnitt beschrieben:

1. Ein Batch von Azure-Konto mithilfe der Azure-Verwaltungsportal zu erstellen. Finden Sie unter [– Technische Übersicht zu Azure Batch][batch-create-account] Artikel Anweisungen. Notieren Sie den Azure-Batch und Konto Zugriffsschlüssel. 

	Sie können auch [neu AzureBatchAccount][new-azure-batch-account] Cmdlet ein Batch von Azure-Konto erstellen. Finden Sie unter [mit Azure PowerShell zum Verwalten von Azure-Batch-Konto][azure-batch-blog] ausführliche Anweisungen zur Verwendung dieses Cmdlets. 
2. Erstellen Sie einen Azure-Batch-Pool. Können Sie herunterladen und verwenden die [Azure Batch-Explorertools][batch-explorer] (oder) [Azure Batch-Bibliothek für .NET][batch-net-library] zum Erstellen eines Pools von Azure-Batch. Finden Sie unter [Azure Batch Explorer-Beispiel Exemplarische Vorgehensweise][batch-explorer-walkthrough] schrittweise Anweisungen für die Verwendung von Azure-Batch-Explorer.
	
	Sie können auch [neu AzureBatchPool][new-azure-batch-pool] Cmdlet einen Batch von Azure-Pool erstellen.

2. Erstellen Sie einen Azure Batch verknüpften Dienst mithilfe der folgenden JSON-Vorlage. Data Factory-Editor zeigt eine ähnliche Vorlage zum Einstieg für Sie. Geben Sie einen Batch von Azure-Kontonamen, Schlüssel und Pool-Kontonamen im JSON-Ausschnitt an.

		{
		    "name": "AzureBatchLinkedService",
		    "properties": {
		        "type": "AzureBatchLinkedService",
		        "accountName": "<Azure Batch account name>",
		        "accessKey": "<Azure Batch account key>",
		        "poolName": "<Azure Batch pool name>",
		        "linkedServiceName": "<Specify associated storage linked service reference here>"
		  }
		}

	Finden Sie unter [Azure Batch verknüpften Dienst MSDN-Thema](https://msdn.microsoft.com/library/mt163609.aspx) Beschreibungen dieser Eigenschaften.

2.  Im Data Factory-Editor öffnen, JSON-Definition für die Pipeline, die Sie erstellt, in der exemplarischen Vorgehensweise und ersetzen haben **HDInsightLinkedService** mit **AzureBatchLinkedService**.
3.  Möglicherweise möchten die Start- und Endzeiten für die Pipeline zu ändern, sodass Sie das Szenario mit dem Azure-Batch-Dienst testen können. 
4.  Sie sehen, dass die Azure-Batch Aufgaben im Zusammenhang mit der Verarbeitung die Segmente in der Azure-Batch-Explorer, wie im folgenden Diagramm dargestellt.

	![Azure-Batch-Aufgaben][image-data-factory-azure-batch-tasks]

## Siehe auch

[Azure Factory Datenaktualisierungen: mithilfe von Azure-Batch ausführen ADF benutzerdefinierte .NET Aktivitäten](http://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/).

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-technical-overview.md/#batch-concepts
[batch-technical-overview]: ../batch/batch-technical-overview.md
[batch-get-started]: ../batch/batch-dotnet-get-started.md
[monitor-manage-using-powershell]: data-factory-monitor-manage-using-powershell.md
[use-onpremises-datasources]: data-factory-use-onpremises-datasources.md
[adf-tutorial]: data-factory-tutorial.md
[use-custom-activities]: data-factory-use-custom-activities.md
[use-pig-and-hive-with-data-factory]: data-factory-pig-hive-activities.md
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
[hivewalkthrough]: data-factory-pig-hive-activities.md

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

[image-data-factory-azure-batch-tasks]: ./media/data-factory-use-custom-activities/AzureBatchTasks.png

<!---HONumber=GIT-SubDir--> 