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
	ms.date="2/10/2015" 
	ms.author="spelluru"/>

# Verwenden von benutzerdefinierten Aktivitäten in einer Azure Data Factory-Pipeline
Azure Data Factory unterstützt integrierte Aktivitäten wie z. B. **Kopieraktivität** und **HDInsight-Aktivität** zum Verschieben und Verarbeiten von Daten in Pipelines. Sie können auch eine benutzerdefinierte Aktivität mit Ihrer eigenen Transformations-/Verarbeitungslogik erstellen und die Aktivität in einer Pipeline verwenden. Die benutzerdefinierte Aktivität wird als Nur-Zuordnungs-Auftrag in auf einem HDInsight-Cluster ausgeführt, daher müssen Sie ein HDInsight-Cluster für die benutzerdefinierte Aktivität in der Pipeline verknüpfen.
 
Dieser Artikel beschreibt, wie Sie eine benutzerdefinierte Aktivität erstellen und in einer Azure Data Factory-Pipeline verwenden. Darüber hinaus bietet er eine ausführliche exemplarische Vorgehensweise mit einer schrittweisen Anleitung zum Erstellen und Verwenden einer benutzerdefinierten Aktivität.

## Erstellen einer benutzerdefinierten Aktivität

So erstellen Sie eine benutzerdefinierte Aktivität:
 
1.	Erstellen Sie ein **Klassenbibliotheksprojekt** in Visual Studio 2013.
2.	Laden Sie ein [NuGet-Paket für Azure Data Factory][nuget-package] herunter und installieren Sie es. Anweisungen zur Installation finden Sie in der exemplarischen Vorgehensweise.
3. Fügen Sie die folgenden using-Anweisungen am Anfang der Quelldatei in der Klassenbibliothek ein.
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 

4. Aktualisieren Sie die Klasse zur Implementierung der **ICustomActivity**-Schnittstelle.
	<ol type='a'>
		<li>
			Leiten Sie die Klasse von  <b>ICustomActivity</b>. ab.
			<br/>
			Beispiel: <br/>
			public class <b>MyCustomActivity : ICustomActivity</b>
		</li>

		<li>
			Implementieren Sie die  <b>Execute</b> -Methode der <b>ICustomActivity</b>-Schnittstelle.
		</li>

	</ol>
5. Kompilieren Sie das Projekt.


## Verwenden der benutzerdefinierten Aktivität in einer Pipeline
So verwenden Sie die benutzerdefinierte Aktivität in einer Pipeline

1.	**Komprimieren** Sie alle Binärdateien aus den Ausgabeordnern **bin\debug** oder **bin\release** für das Projekt. 
2.	**Laden Sie die ZIP-Datei** als Blob in den **Azure Blob-Speicher** hoch. 
3.	Aktualisieren Sie die **Pipeline JSON-Datei**, um auf die ZIP-Datei, die benutzerdefinierte Aktivitäts-DLL, die Activity-Klasse und den Blob, der die ZIP-Datei in der Pipeline-JSON enthält, zu verweisen. In der JSON-Datei:
	<ol type ="a">
		<li><b>Activity type</b> sollte auf <b>CustomActivity</b> festgelegt werden.</li>
		<li><b>AssemblyName</b> ist der Name der Ausgabe-DLL vom Visual Studio-Projekt.</li>
		<li><b>EntryPoint</b> gibt den <b>Namespace</b> und den <b>Namen</b> der <b>Klasse</b>, die die <b>ICustomActivity</b>-Schnittstelle implementiert, an.</li>
		<li><b>PackageLinkedService</b> ist der verknüpfte Dienst, der auf den Blob verweist, welcher die ZIP-Datei enthält. </li>
		<li><b>PackageFile</b> gibt den Speicherort und den Namen der ZIP-Datei, die in den Azure Blob-Speicher hochgeladen wurde, an.</li>
		<li><b>LinkedServiceName</b> ist der Name des verknüpften Diensts, der einen HDInsight-Cluster (bedarfsgesteuert oder eigener) mit einer Data Factory verknüpft. Die benutzerdefinierte Aktivität wird als Nur-Zuordnungs-Auftrag für den angegebenen HDInsight-Cluster ausgeführt.</li>
	</ol>

	

	**Ausschnitt aus einem JSON-Beispiel**

		"Name": "MyCustomActivity",
    	"Type": "CustomActivity",
    	"Inputs": [{"Name": "EmpTableFromBlob"}],
    	"Outputs": [{"Name": "OutputTableForCustom"}],
		"LinkedServiceName": "myhdinsightcluster",
    	"Transformation":
    	{
	    	"AssemblyName": "MyCustomActivity.dll",
    	    "EntryPoint": "MyCustomActivityNS.MyCustomActivity",
    	    "PackageLinkedService": "MyBlobStore",
    	    "PackageFile": "customactivitycontainer/MyCustomActivity.zip",

## So aktualisieren Sie die benutzerdefinierte Aktivität
Wenn Sie den Code für die benutzerdefinierte Aktivität aktualisieren, erstellen und die ZIP-Datei, welche die neuen Binärdateien enthält, in den Blob-Speicher hochladen, müssen Sie die vorhandene Pipeline überschreiben, die die benutzerdefinierte Aktivität verwendet, indem Sie **New-AzureDataFactoryPipeline** ausführen. Beim Erstellen einer Pipeline, die die benutzerdefinierte Aktivität verwendet, wird die ZIP-Datei aus dem angegebenen Blob-Speicher in einen Azure Data Factory-Container im Blob-Speicher, der an den HDInsight-Cluster angehängt ist, kopiert. Dies geschieht nur bei der Erstellung der Pipeline. Aus diesem Grund müssen Sie die Pipeline neu erstellen, wenn Sie die benutzerdefinierte Aktivität aktualisieren. 

Die folgende exemplarische Vorgehensweise bietet schrittweise Anleitungen zum Erstellen einer benutzerdefinierten Aktivität und zur Verwendung der Aktivität in einer Azure Data Factory-Pipeline. Diese exemplarische Vorgehensweise baut auf dem Lernprogramm von [Erste Schritte mit Azure Data Factory][adfgetstarted] auf. Wenn Sie die benutzerdefinierte Aktivität in Aktion sehen möchten, müssen Sie zuerst das Erste-Schritte-Lernprogramm durchlaufen und diese exemplarischen Vorgehensweise ausführen. 

## Exemplarische Vorgehensweise
**Voraussetzungen:**


- Lernprogramm [Erste Schritte mit Azure Data Factory][adfgetstarted]. Sie müssen zuerst das Lernprogramm aus diesem Artikel abschließen, bevor Sie mit dieser exemplarischen Vorgehensweise fortfahren können.
- Visual Studio 2012 oder 2013
- Herunterladen und Installieren von [Windows Azure .NET SDK][azure-developer-center]
- Herunterladen von [NuGet-Paketen für Azure Data Factory][nuget-package]
- Herunterladen und Installieren des NuGet-Pakets für Azure-Speicher. Anweisungen sind in der exemplarischen Vorgehensweise enthalten, daher können Sie diesen Schritt überspringen.

### Schritt 1: Erstellen einer benutzerdefinierten Aktivität

1.	Erstellen Sie ein .NET-Klassenbibliotheksprojekt.
	<ol type="a">
		<li>Starten Sie <b>Visual Studio 2012</b> oder <b>Visual Studio 2013</b>.</li>
		<li>Klicken Sie auf <b>Datei</b>, auf <b>Neu</b> und anschließend auf <b>Projekt</b>.</li> 
		<li>Erweitern Sie <b>Vorlagen</b>, und wählen Sie <b>Visual C#</b>. In dieser exemplarischen Vorgehensweise verwenden Sie C#, Sie können jedoch jede .NET-Sprache verwenden, um benutzerdefinierte Aktivität zu entwickeln.</li> 
		<li>Wählen Sie die <b>Klassenbibliothek</b> aus der Liste der Projekttypen auf der rechten Seite.</li>
		<li>Geben Sie <b>MyCustomActivity</b> als <b>Name</b> ein.</li> 
		<li>Wählen Sie <b>C:\ADFGetStarted</b> als <b>Speicherort</b>.</li>
		<li>Klicken Sie auf <b>OK</b>, um das Projekt zu erstellen.</li>
	</ol>
2.  Klicken Sie auf <b>Extras</b>, zeigen Sie auf <b>NuGet-Paketmanager</b>, und klicken Sie auf <b>Paket-Manager-Konsole</b>.
3.	Führen Sie in der <b>Paket-Manager-Konsole</b> den folgenden Befehl zum Importieren der zuvor heruntergeladenen <b>Microsoft.Azure.Management.DataFactories</b> aus. Ersetzen Sie den Ordner durch den Speicherort, der das heruntergeladene Data Factory NuGet-Paket enthält.

		Install-Package Microsoft.Azure.Management.DataFactories -Source d:\packages -Pre

3.	Führen Sie in der <b>Paket-Manager-Konsole</b> den folgenden Befehl zum Importieren von <b>Microsoft.DataFactories.Runtime</b> aus. Ersetzen Sie den Ordner durch den Speicherort, der das heruntergeladene Data Factory NuGet-Paket enthält.

		Install-Package Microsoft.DataFactories.Runtime -Source d:\packages -Pre

4. Importieren Sie das Windows Azure Storage NuGet-Paket in das Projekt.

		Install-Package WindowsAzure.Storage

5. Fügen Sie die folgenden **using**-Anweisungen zur Quelldatei im Projekt hinzu.

		using System.IO;
		using System.Globalization;
	
		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.DataFactories.Runtime; 
	
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
  
6. Ändern Sie den Namen des **Namespace** in **MyCustomActivityNS**.

		namespace MyCustomActivityNS

7. Ändern Sie den Namen der Klasse in **MyCustomActivity** und leiten Sie ihn von der **ICustomActivity**-Schnittstelle ab, wie unten dargestellt.

		public class MyCustomActivity : ICustomActivity

8. Implementieren Sie bzw. fügen sie die **Execute**-Methode der **ICustomActivity**-Schnittstelle zur **MyCustomActivity**-Klasse hinzu, und kopieren Sie den folgenden Beispielcode in die Methode. 

	Die Parameter **InputTables** und **OutputTables** stellen Eingabe- und Ausgabetabellen für die Aktivität dar. Sie können protokollierte Meldungen unter Verwendung des **logger**-Objekts in der Protokolldatei anzeigen, das Sie aus dem Azure-Portal herunterladen können, oder mithilfe von Cmdlets. Das **extendedProperties**-Wörterbuch enthält die Liste der erweiterten Eigenschaften und deren Werte, die Sie in der JSON-Datei für die Aktivität angeben. 

	Im folgenden Beispielcode wird die Anzahl der Zeilen im Eingabe-Blob gezählt und der folgende Inhalt im Ausgabe-Blob erzeugt: Pfad zum Blob, die Anzahl der Zeilen im Blob, der Computer, auf dem die Aktivität ausgeführt wurde, aktuelles Datum/Uhrzeit.

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
										"{0},{1},{2},{3}\n", 
										folderPath, 
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

9. Fügen Sie die folgenden Hilfsmethoden hinzu. Die **Execute**-Methode ruft diese Hilfsmethoden auf. Die **GetConnectionString**-Methode ruft die Azure-Speicher-Verbindungszeichenfolge ab, und die **GetFolderPath**-Methode ruft den Blob-Speicherort ab. 


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
   


10. Kompilieren Sie das Projekt. Klicken Sie im Menü auf **Erstellen**, und klicken Sie dann auf **Projektmappe erstellen**.
11. Starten Sie **Windows Explorer**, und navigieren Sie zum Ordner **bin\debug** oder **bin\release**, je nach Build-Typ.
12. Erstellen Sie eine ZIP-Datei **MyCustomActivity.zip**, die alle Binärdateien im Ordner <Projektordner>\bin\Debug enthält.
	![zip output binaries][image-data-factory-zip-output-binaries]
13. Laden Sie **MyCustomActivity.zip** als Blob in den Blob-Container hoch: **customactvitycontainer** im Azure Blob-Speicher, der den verknüpften Dienst **MyBlobStore** in **ADFTutorialDataFactory** verwendet.  Erstellen Sie den Blob-Container **blobcustomactivitycontainer**, falls er nicht bereits vorhanden ist. 
    ![upload zip to blob][image-data-factory-upload-zip-to-blob]

### Erstellen eines verknüpften Diensts für   HDInsight-Cluster, die zum Ausführen der benutzerdefinierten Aktivität verwendet werden
Der Azure Data Factory-Dienst unterstützt das Erstellen eines Clusters bei Bedarf und verwendet ihn zum Verarbeiten der Eingabe zum Erzeugen von Ausgabedaten. Sie können auch Ihren eigenen Cluster für den gleichen Zweck verwenden. Wenn Sie den bedarfsgesteuerten HDInsight-Cluster verwenden, wird für jeden Slice ein Cluster erstellt. Bei Verwendung eines eigenen HDInsight-Clusters ist der Cluster sofort zur Sliceverarbeitung bereit. Aus diesem Grund werden bei Verwendung des bedarfsgesteuerten Clusters die Ausgabedaten möglicherweise nicht so schnell angezeigt wie bei der Verwendung Ihres eigenen Clusters. Für dieses Beispiel verwenden wir einen bedarfsgesteuerten Cluster. 

> [WACOM.NOTE] Wenn Sie das Lernprogramm [Erste Schritte mit Azure Data Factory][adfgetstarted] mit der exemplarischen Vorgehensweise für [Verwenden von Pig und Hive mit Azure Data Factory][hivewalkthrough] erweitert haben, können Sie die Erstellung dieses verknüpften Diensts überspringen und den verknüpften Dienst verwenden, den Sie bereits in der ADFTutorialDataFactory haben. 

#### So verwenden Sie einen bedarfsgesteuerten HDInsight-Cluster

1. Erstellen Sie eine JSON-Datei mit dem Namen **HDInsightOnDemandCluster.json** mit folgendem Inhalt, und speichern Sie sie im Ordner **C:\ADFGetStarted\Custom**.


		{
    		"name": "HDInsightOnDemandCluster",
    		"properties": 
    		{
        		"type": "HDInsightOnDemandLinkedService",
				"clusterSize": 4,
        		"jobsContainer": "adftutorialjobscontainer",
        		"timeToLive": "00:05:00",
        		"linkedServiceName": "MyBlobStore"
    		}
		}

2. Starten Sie **Azure PowerShell**, und führen Sie den folgenden Befehl aus, um in den **AzureResourceManager**-Modus zu wechseln. Die Azure Data Factory-Cmdlets sind im **AzureResourceManager-Modus** verfügbar.

         switch-azuremode AzureResourceManager
		

3. Wechseln Sie zum Ordner **C:\ADFGetstarted\Custom**.
4. Führen Sie den folgenden Befehl zum Erstellen des verknüpften Diensts für den bedarfsgesteuerten HDInsight-Cluster aus.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\HDInsightOnDemandCluster.json
  
   
#### So verwenden Sie Ihren eigenen HDInsight-Cluster: 

1. Erstellen Sie eine JSON-Datei mit dem Namen **MyHDInsightCluster.json** mit folgendem Inhalt, und speichern Sie sie im Ordner **C:\ADFGetStarted\Custom**. Ersetzen Sie den Clusternamen, den Benutzernamen und das Kennwort durch geeignete Werte, bevor Sie die JSON-Datei speichern.  

		{
   			"Name": "MyHDInsightCluster",
    		"Properties": 
			{
        		"Type": "HDInsightBYOCLinkedService",
	        	"ClusterUri": "https://<clustername>.azurehdinsight.net/",
    	    	"UserName": "<username>",
    	    	"Password": "<password>",
    	    	"LinkedServiceName": "MyBlobStore"
    		}
		}

2. Starten Sie **Azure PowerShell**, und führen Sie den folgenden Befehl aus, um in den **AzureResourceManager**-Modus zu wechseln. Die Azure Data Factory-Cmdlets sind im **AzureResourceManager-Modus** verfügbar.

         switch-azuremode AzureResourceManager
		

3. Wechseln Sie zum Ordner **C:\ADFGetstarted\Custom**.
4. Führen Sie den folgenden Befehl zum Erstellen des verknüpften Diensts für den bedarfsgesteuerten HDInsight-Cluster aus.
 
		New-AzureDataFactoryLinkedService -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -File .\MyHDInsightCluster.json



### Schritt 2: Verwenden der benutzerdefinierten Aktivität in einer Pipeline
Wir erweitern das Lernprogramm von [Erste Schritte mit Azure Data Factory][adfgetstarted], um eine weitere Pipeline zum Testen dieser benutzerdefinierte Aktivität zu erstellen.

#### Erstellen eines verknüpften Diensts für den HDInsight-Cluster zur Ausführung einer benutzerdefinierten Aktivität


1.	Erstellen Sie JSON für die Pipeline wie im folgenden Beispiel dargestellt, und speichern Sie es als **ADFTutorialPipelineCustom.json** im Ordner **C:\ADFGetStarted\Custom**. Ändern Sie den Namen des **LinkedServiceName** in den Namen des HDInsight-Clusters (**HDInsightOnDemandCluster** oder **MyHDInsightCluster**).


		{
    		"name": "ADFTutorialPipelineCustom",
    		"properties":
    		{
        		"description" : "Use custom activity",
        		"activities":
        		[
					{
                		"Name": "MyCustomActivity",
                     	"Type": "CustomActivity",
                     	"Inputs": [{"Name": "EmpTableFromBlob"}],
                     	"Outputs": [{"Name": "OutputTableForCustom"}],
						"LinkedServiceName": "MyHDInsightCluster",
                     	"Transformation":
                     	{
                        	"AssemblyName": "MyCustomActivity.dll",
                            "EntryPoint": "MyCustomActivityNS.MyCustomActivity",
                            "PackageLinkedService": "MyBlobStore",
                            "PackageFile": "customactivitycontainer/MyCustomActivity.zip",
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
				]
			}
		}

	Beachten Sie Folgendes: 

	- Es gibt eine Aktivität im activities-Abschnitt vom Typ: **CustomActivity**.
	- Verwenden Sie die gleiche Eingabetabelle **EmpTableFromBlob**, die Sie im Erste-Schritte-Lernprogramm verwendet haben.
	- Verwenden Sie eine neue Ausgabetabelle **OutputTableForCustom**, die Sie im nächsten Schritt erstellen.
	- **AssemblyName** wird auf den Namen der DLL festgelegt: **MyActivities.dll**.
	- **EntryPoint** wird auf **MyCustomActivityNS.MyCustomActivity** festgelegt.
	- **PackageLinkedService** wird auf **MyBlobStore** festgelegt, das als Teil des Lernprogramms [Erste Schritte mit Azure Data Factory][adfgetstarted] erstellt wurde. Dieser Blob-Speicher enthält die ZIP-Datei mit der benutzerdefinierten Aktivität.
	- **PackageFile** wird auf **customactivitycontainer/MyCustomActivity.zip** festgelegt.
     

4. Erstellen Sie eine JSON-Datei für die Ausgabetabelle (**OutputTableForCustom**, auf die die Pipeline-JSON verweist), und speichern Sie sie als C:\ADFGetStarted\Custom\OutputTableForCustom.json.

		

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

 	Ausgabespeicherort ist **adftutorial/customactivityoutput/YYYYMMDDHH/**, wobei YYYYMMDDHH Jahr, Monat, Datum und Stunde der Sliceerstellung angibt. Ausführlichere Informationen finden Sie in der [Entwicklerreferenz][adf-developer-reference]. 

5. Führen Sie den folgenden Befehl aus, um **die Ausgabetabelle** in **ADFTutorialDataFactory** zu erstellen.
		
		

		New-AzureDataFactoryTable  -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\Custom\OutputTableForCustom.json -ResourceGroupName ADFTutorialResourceGroup


6. Führen Sie nun den folgenden Befehl zum **Erstellen der Pipeline** aus. Sie haben die Pipeline-JSON-Datei in einem früheren Schritt erstellt.

		New-AzureDataFactoryPipeline  -DataFactoryName ADFTutorialDataFactory -File C:\ADFGetStarted\Custom\ADFTutorialPipelineCustom.json -ResourceGroupName ADFTutorialResourceGroup



7. Führen Sie den folgenden PowerShell-Befehl zum **Festlegen des aktiven Zeitraums** für die von Ihnen erstellte Pipeline aus.

		Set-AzureDataFactoryPipelineActivePeriod -ResourceGroupName ADFTutorialResourceGroup -DataFactoryName ADFTutorialDataFactory -StartDateTime 2014-09-29 -EndDateTime 2014-09-30 -Name ADFTutorialPipelineCustom

	> [WACOM.NOTE] Ersetzen Sie den **StartDateTime**-Wert durch den aktuellen Tag und den **EndDateTime**-Wert durch den nächsten Tag. StartDateTime und EndDateTime sind UTC-Zeiten und müssen im [ISO-Format](http://en.wikipedia.org/wiki/ISO_8601) angegeben werden. Beispiel: 2014-10-14T16:32:41Z. **EndDateTime** ist optional, wird aber in diesem Lernprogramm verwendet. 
	> Wenn Sie keinen **EndDateTime**-Wert angeben, wird er als "**StartDateTime + 48 Stunden**" berechnet. Um die Pipeline auf unbestimmte Zeit auszuführen, geben Sie **9/9/9999** als **EndDateTime** an.



8. Stellen Sie sicher, dass die Ausgabedateien im Blob-Speicher im **adftutorial**-Container generiert werden.

	![output from custom activity][image-data-factory-ouput-from-custom-activity]

9. Wenn Sie die Ausgabedatei öffnen, sollte die Ausgabe ähnlich der folgenden angezeigt werden:
	
	adftutorial/input,2,WORKERNODE0,10/07/2014 18:34:33 

	(Blob-Speicherort), (Anzahl der Zeilen im Blob) (Knoten, auf dem die Aktivität ausgeführt wurde), (Datums-/Zeitstempel)

10.	Verwenden Sie das [Azure-Portal][azure-preview-portal] oder die Azure PowerShell-Cmdlets zum Überwachen Ihrer Data Factory, Pipelines und Datasets. Sie können die Meldungen vom **ActivityLogger** im Code für die benutzerdefinierte Aktivität in den Protokollen sehen, die Sie aus dem Portal oder mithilfe von Cmdlets herunterladen können.

	![download logs from custom activity][image-data-factory-download-logs-from-custom-activity]
   
Eine ausführliche Anleitung zum Überwachen von Datasets und Pipelines finden Sie in [Erste Schritte mit Azure Data Factory][adfgetstarted].      
    
## Weitere Informationen

Artikel | Beschreibung
------ | ---------------
[Aktivieren von Pipelines zum Arbeiten mit lokalen Daten][use-onpremises-datasources] | Dieser Artikel enthält eine exemplarische Vorgehensweise zum Kopieren von Daten aus einer lokalen SQL Server-Datenbank in einen Azure-Blob.
[Verwenden von Pig und Hive mit Data Factory][use-pig-and-hive-with-data-factory] | Dieser Artikel hat eine exemplarische Vorgehensweise, die zeigt, wie Sie HDInsight-Aktivitäten zum Ausführen eines Hive/Pig-Skripts zur Verarbeitung der Eingabedaten in Ausgabedaten zu erzeugen. 
[Lernprogramm: Verschieben und Verarbeiten von Protokolldateien mit Data Factory][adf-tutorial] | In diesem Artikel wird in einer umfassenden exemplarischen Vorgehensweise die Implementierung eines realen Szenarios mithilfe von Azure Data Factory veranschaulicht, um Einblicke aus Protokolldateien zu gewinnen
[Verwenden von benutzerdefinierte Aktivitäten in Data Factory][use-custom-activities] | Dieser Artikel bietet eine exemplarische Vorgehensweise mit einer schrittweisen Anleitung zum Erstellen einer benutzerdefinierten Aktivität und ihrer Verwendung in einer Pipeline. 
[Überwachen und Verwalten von Azure Data Factory mit PowerShell][monitor-manage-using-powershell] | In diesem Artikel wird beschrieben, wie eine Azure Data Factory mithilfe von Azure PowerShell-Cmdlets überwacht werden kann. Sie können die Beispiele in diesem Artikel mit ADFTutorialDataFactory ausprobieren.
[Problembehandlung bei Data Factory][troubleshoot] | In diesem Artikel wird beschrieben, wie Sie Azure Data Factory-Probleme beheben. Sie können die exemplarische Vorgehensweise in diesem Artikel mit ADFTutorialDataFactory ausprobieren, indem Sie einen Fehler einbauen (die Tabelle in der Azure SQL-Datenbank löschen). 
[Azure Data Factory Entwicklerreferenz][developer-reference] | Die Entwicklerreferenz enthält umfassendes Referenzmaterial für Cmdlets, JSON-Skripts, Funktionen usw... 


[monitor-manage-using-powershell]: ../data-factory-monitor-manage-using-powershell
[use-onpremises-datasources]: ../data-factory-use-onpremises-datasources
[adf-tutorial]: ../data-factory-tutorial
[use-custom-activities]: ../data-factory-use-custom-activities
[use-pig-and-hive-with-data-factory]: ../data-factory-pig-hive-activities
[troubleshoot]: ../data-factory-troubleshoot
[data-factory-introduction]: ../data-factory-introduction


[developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[cmdlet-reference]: http://go.microsoft.com/fwlink/?LinkId=517456




[nuget-package]: http://go.microsoft.com/fwlink/?LinkId=517478
[azure-developer-center]: http://azure.microsoft.com/develop/net/
[adf-developer-reference]: http://go.microsoft.com/fwlink/?LinkId=516908
[azure-preview-portal]: https://portal.azure.com/

[adfgetstarted]: ../data-factory-get-started
[hivewalkthrough]: ../data-factory-pig-hive-activities

[image-data-factory-zip-output-binaries]: ./media/data-factory-use-custom-activities/ZipOuputBinaries.png

[image-data-factory-upload-zip-to-blob]: ./media/data-factory-use-custom-activities/UploadZipToBlob.png

[image-data-factory-ouput-from-custom-activity]: ./media/data-factory-use-custom-activities/OutputFilesFromCustomActivity.png

[image-data-factory-download-logs-from-custom-activity]: ./media/data-factory-use-custom-activities/DownloadLogsFromCustomActivity.png

<!--HONumber=46--> 
