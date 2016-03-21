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
	ms.date="01/05/2016"
	ms.author="spelluru"/>

# Verwenden von benutzerdefinierten Aktivitäten in einer Azure Data Factory-Pipeline
Azure Data Factory unterstützt integrierte Aktivitäten, z. B. **Kopieraktivität** und **HDInsight-Aktivität**, zum Verschieben und Verarbeiten von Daten in Pipelines. Sie können auch eine benutzerdefinierte .NET-Aktivität mit Ihrer eigenen Transformations-/Verarbeitungslogik erstellen und die Aktivität in einer Pipeline verwenden. Sie können die Aktivität so konfigurieren, dass entweder ein **Azure HDInsight**-Cluster oder ein **Azure Batch**-Dienst ausgeführt wird.

Dieser Artikel beschreibt, wie Sie eine benutzerdefinierte Aktivität erstellen und in einer Azure Data Factory-Pipeline verwenden. Darüber hinaus bietet er eine ausführliche exemplarische Vorgehensweise mit einer schrittweisen Anleitung zum Erstellen und Verwenden einer benutzerdefinierten Aktivität. Für die exemplarische Vorgehensweise wird der mit HDInsight verknüpfte Dienst verwendet. Um stattdessen den mit Azure Batch verknüpften Dienst zu verwenden, erstellen Sie einen verknüpften Dienst vom Typ **AzureBatch** und verwenden ihn im Abschnitt "Aktivität" der JSON-Pipeline (**linkedServiceName**). Im Abschnitt [Mit Azure Batch verknüpfter Dienst](#AzureBatch) finden Sie Informationen zur Verwendung von Azure Batch mit der benutzerdefinierten Aktivität.


## <a name="walkthrough" /> Exemplarische Vorgehensweise
Diese exemplarische Vorgehensweise bietet Schritt-für-Schritt-Anleitungen zum Erstellen einer benutzerdefinierten Aktivität und zur Verwendung der Aktivität in einer Azure Data Factory-Pipeline. Diese exemplarische Vorgehensweise baut auf dem Lernprogramm in [Erste Schritte mit Azure Data Factory][adfgetstarted] auf. Wenn Sie die benutzerdefinierte Aktivität in Aktion sehen möchten, müssen Sie zuerst das Erste-Schritte-Lernprogramm durchlaufen und diese exemplarischen Vorgehensweise ausführen.

### Voraussetzungen


- Lernprogramm aus [Erste Schritte mit Azure Data Factory][adfgetstarted]. Sie müssen zuerst das Lernprogramm in diesem Artikel durchlaufen, bevor Sie mit dieser exemplarischen Vorgehensweise fortfahren können.
- Visual Studio 2012 oder 2013
- Herunterladen und Installieren des [Azure .NET SDK][azure-developer-center]
- Laden Sie das aktuelle [NuGet-Paket für Azure Data Factory](https://www.nuget.org/packages/Microsoft.Azure.Management.DataFactories/) herunter, und installieren Sie es. Entsprechende Anweisungen sind in der exemplarischen Vorgehensweise enthalten.
- Herunterladen und Installieren des NuGet-Pakets für Azure-Speicher. Anweisungen sind in der exemplarischen Vorgehensweise enthalten, daher können Sie diesen Schritt überspringen.

### Allgemeine Schritte 
1.	**Erstellen Sie eine benutzerdefinierte Aktivität** in der Data Factory-Lösung. Die benutzerdefinierte Aktivität enthält die Logik für die Datenverarbeitung. 
	1.	Erstellen Sie in Visual Studio (oder einem Code-Editor Ihrer Wahl) ein .NET-Klassenbibliotheksprojekt, fügen Sie den Code zum Verarbeiten von Eingabedaten ein und kompilieren Sie das Projekt.	
	2.	Zippen Sie alle Binärdateien und die (optionale) PDB-Datei im Ausgabeordner.	
	3.	Laden Sie die Zip-Datei in den Azure Blobspeicher hoch. Eine ausführliche Anleitung finden Sie im Abschnitt „Benutzerdefinierte Aktivität erstellen“. 
2. **Erstellen Sie eine Azure Data Factory, die die benutzerdefinierte Aktivität verwendet**:
	1. Erstellen Sie eine Azure Data Factory.
	2. Erstellen Sie verknüpfte Dienste.
		1. StorageLinkedService: Stellt die Speicher-Anmeldeinformationen für den Zugriff auf Blobs bereit.
		2. HDInsightLinkedService: Gibt Azure HDInsight als Compute-Entität an.
	3. Erstellen Sie Datasets.
		1. InputDataset: Gibt die Speichercontainer und den Ordner für die Eingabe-Blobs an.
		1. OutputDataset: Gibt die Speichercontainer und den Ordner für die Ausgabe-Blobs an.
	2. Erstellen Sie eine Pipeline, die die benutzerdefinierte Aktivität verwendet.
	3. Führen Sie die Pipeline aus und testen Sie sie.
	4. Debuggen Sie die Pipeline.

## Erstellen der benutzerdefinierten Aktivität
Um eine benutzerdefinierte .NET-Aktivität zu erstellen, die Sie in einer Azure Data Factory-Pipeline benutzen können, müssen Sie ein **.NET-Klassenbibliotheksprojekt** mit einer Klasse erstellen, die die **IDotNetActivity**-Schnittstelle implementiert. Diese Schnittstelle verfügt lediglich über eine Methode: Execute. Dies ist die Signatur der Methode:

	public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices, 
            IEnumerable<Dataset> datasets, 
            Activity activity, 
            IActivityLogger logger)
        
Die Methode verfügt über einige wichtige Komponenten, die Sie kennen müssen.

- Die Methode akzeptiert vier Parameter:
	- **LinkedServices**. Dies ist eine aufzählbare Liste von verknüpften Diensten, die Eingabe- und Ausgabedatenquellen (z. B.: Azure Blob Storage) mit der Data Factory verknüpfen. In diesem Beispiel gibt es nur einen verknüpften Dienst des Typs Azure Storage, der sowohl für die Eingabe als auch Ausgabe verwendet wird. 
	- **Datasets**. Dies ist eine aufzählbare Liste von Datasets. Sie können diesen Parameter zum Abrufen der Speicherorte und Schemas verwenden, die von den Eingabe- und Ausgabedatasets definiert werden.
	- **Aktivität**: Dieser Parameter stellt die aktuelle Compute-Entität dar – in diesem Fall Azure HDInsight.
	- **Protokollierungstool**: Mit dem Protokollierungstool können Sie Kommentare zum Debuggen schreiben, die dann als „Benutzer“-Protokoll für die Pipeline erscheinen. 

- Die Methode gibt ein Wörterbuch zurück, das zum Verketten benutzerdefinierter Aktivitäten verwendet werden kann. In diesem Beispiel werden wir diese Funktion nicht verwenden.

### Vorgehensweise: 
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
		using System.Linq;

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

		/// <summary>
        /// Execute method is the only method of IDotNetActivity interface you must implement. 
        /// In this sample, the method invokes the Calculate method to perform the core logic.  
		/// </summary>

        public IDictionary<string, string> Execute(
            IEnumerable<LinkedService> linkedServices,
            IEnumerable<Dataset> datasets,
            Activity activity,
            IActivityLogger logger)
        {
			// to get extended properties (for example: SliceStart)
			DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
            string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

			// to log all extended properties			
			IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
			logger.Write("Logging extended properties if any...");
			foreach (KeyValuePair<string, string> entry in extendedProperties)
			{
				logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
			}
		

            // declare types for input and output data stores
            AzureStorageLinkedService inputLinkedService;

            // declare dataset types
            CustomDataset inputLocation;
            AzureBlobDataset outputLocation;

            Dataset inputDataset = datasets.Single(dataset => dataset.Name == activity.Inputs.Single().Name);
            inputLocation = inputDataset.Properties.TypeProperties as CustomDataset;

            foreach (LinkedService ls in linkedServices)
                logger.Write("linkedService.Name {0}", ls.Name);

            // using First method instead of Single since we are using the same 
            // Azure Storage linked service for input and output. 
            inputLinkedService = linkedServices.First(
                linkedService =>
                linkedService.Name ==
                inputDataset.Properties.LinkedServiceName).Properties.TypeProperties
                as AzureStorageLinkedService;

            string connectionString = inputLinkedService.ConnectionString; // To create an input storage client.
            string folderPath = GetFolderPath(inputDataset);
            string output = string.Empty; // for use later.

            // create storage client for input. Pass the connection string.
            CloudStorageAccount inputStorageAccount = CloudStorageAccount.Parse(connectionString);
            CloudBlobClient inputClient = inputStorageAccount.CreateCloudBlobClient();

            // initialize the continuation token before using it in the do-while loop.
            BlobContinuationToken continuationToken = null;
            do
            {   // get the list of input blobs from the input storage client object.
                BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
                                         true,
                                         BlobListingDetails.Metadata,
                                         null,
                                         continuationToken,
                                         null,
                                         null);
                
                // Calculate method returns the number of occurrences of 
                // the search term (“Microsoft”) in each blob associated
       			// with the data slice. 
        		// 
        	    // definition of the method is shown in the next step.
 
                output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");

            } while (continuationToken != null);

            // get the output dataset using the name of the dataset matched to a name in the Activity output collection.
            Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);
            // convert to blob location object.
            outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

            folderPath = GetFolderPath(outputDataset);

            logger.Write("Writing blob to the folder: {0}", folderPath);

            // create a storage object for the output blob.
            CloudStorageAccount outputStorageAccount = CloudStorageAccount.Parse(connectionString);
            // write the name of the file. 
            Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

            logger.Write("output blob URI: {0}", outputBlobUri.ToString());
            // create a new blob and upload the output text.
            CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
            logger.Write("Writing {0} to the output blob", output);
            outputBlob.UploadText(output);

            // return a new Dictionary object (unused in this code).
            return new Dictionary<string, string>();
        }

9. Fügen Sie die folgenden Hilfsmethoden hinzu. Die **Execute**-Methode ruft diese Hilfsmethoden auf. Die **GetConnectionString**-Methode ruft die Azure Storage-Verbindungszeichenfolge ab, und die **GetFolderPath**-Methode ruft den Blobspeicherort ab. Vor allem isoliert die **Calculate**-Methode den Code, der jeden Blob durchläuft.

        /// <summary>
        /// Gets the folderPath value from the input/output dataset.   
		/// </summary>

		private static string GetFolderPath(Dataset dataArtifact)
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

        /// <summary>
        /// Gets the fileName value from the input/output dataset.   
        /// </summary>

        private static string GetFileName(Dataset dataArtifact)
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

            return blobDataset.FileName;
        }

        /// <summary>
        /// Iterates through each blob (file) in the folder, counts the number of instances of search term in the file, 
        /// and prepares the output text that will be written to the output blob. 
        /// </summary>

        public static string Calculate(BlobResultSegment Bresult, IActivityLogger logger, string folderPath, ref BlobContinuationToken token, string searchTerm)
        {
            string output = string.Empty;
            logger.Write("number of blobs found: {0}", Bresult.Results.Count<IListBlobItem>());
            foreach (IListBlobItem listBlobItem in Bresult.Results)
            {
                CloudBlockBlob inputBlob = listBlobItem as CloudBlockBlob;
                if ((inputBlob != null) && (inputBlob.Name.IndexOf("$$$.$$$") == -1))
                {
                    string blobText = inputBlob.DownloadText(Encoding.ASCII, null, null, null);
                    logger.Write("input blob text: {0}", blobText);
                    string[] source = blobText.Split(new char[] { '.', '?', '!', ' ', ';', ':', ',' }, StringSplitOptions.RemoveEmptyEntries);
                    var matchQuery = from word in source
                                     where word.ToLowerInvariant() == searchTerm.ToLowerInvariant()
                                     select word;
                    int wordCount = matchQuery.Count();
                    output += string.Format("{0} occurrences(s) of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);
                }
            }
            return output;
        }

	Die „GetFolderPath“-Methode gibt den Pfad zum Ordner zurück, auf den das Dataset verweist, und die „GetFileName“-Methode gibt den Namen des Blobs oder der Datei zurück, auf das oder die das Dataset verweist.
	
		    "name": "InputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "file.txt",
		            "folderPath": "mycontainer/inputfolder/",
	
	Die „Calculate“-Methode berechnet die Anzahl der Instanzen des Schlüsselworts „Microsoft“ in den Eingabedateien (Blobs im Ordner). Der Suchbegriff („Microsoft“) ist fest im Code programmiert.

10. Kompilieren Sie das Projekt. Klicken Sie im Menü auf **Erstellen** und dann auf **Projektmappe erstellen**.
11. Starten Sie **Windows-Explorer**, und navigieren Sie je nach Buildtyp zum Ordner **bin\\debug** oder **bin\\release**.
12. Erstellen Sie die ZIP-Datei **MyDotNetActivity.zip**, die alle Binärdateien im Ordner "<project folder>\\bin\\Debug" enthält. Sie möchten ggf. die Datei **MyDotNetActivity.pdb** einbeziehen, damit Sie zusätzliche Details wie z. B. die Zeilennummer im Quellcode erhalten, der das Problem bei einem Fehler verursacht hat.

	![Binäre Ausgabedateien](./media/data-factory-use-custom-activities/Binaries.png)
13. Laden Sie **MyDotNetActivity.zip** als Blob in den Blobcontainer **customactivitycontainer** im Azure-Blobspeicher hoch, den der verknüpfte Dienst **StorageLinkedService** in der **ADFTutorialDataFactory** verwendet. Erstellen Sie den Blobcontainer **customactivitycontainer**, sofern er noch nicht vorhanden ist.

> [AZURE.NOTE] Wenn Sie dieses .NET-Aktivitätsprojekt einer Projektmappe in Visual Studio hinzufügen, die ein Data Factory-Projekt enthält, müssen Sie die letzten beiden Schritte zum Erstellen der ZIP-Datei und zum manuellen Hochladen in den Azure-Blobspeicher nicht durchführen. Beim Veröffentlichen von Data Factory-Entitäten mit Visual Studio werden diese Schritte automatisch durch den Veröffentlichungsprozess ausgeführt. In den Artikeln [Erstellen der ersten Pipeline mit Visual Studio](data-factory-build-your-first-pipeline-using-vs.md) und [Kopieren von Daten aus einem Azure-Blob in Azure SQL](data-factory-get-started-using-vs.md) erfahren Sie mehr über das Erstellen und Veröffentlichen von Data Factory-Entitäten mit Visual Studio.

### Execute-Methode

Dieser Abschnitt enthält weitere Details und Hinweise zum Code in der **Execute**-Methode.
 
1. Die Elemente zum Durchlaufen der Eingabesammlung finden Sie unter dem Namespace [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx). Zum Durchlaufen der Blob-Sammlung müssen Sie die **BlobContinuationToken**-Klasse verwenden. Im Wesentlichen müssen Sie eine do-while-Schleife mit dem Token als Mechanismus zum Beenden der Schleife verwenden. Weitere Informationen finden Sie unter [Verwenden von Blob-Speicher aus .NET](../storage/storage-dotnet-how-to-use-blobs.md). Eine einfache Schleife ist hier dargestellt:

		// Initialize the continuation token.
		BlobContinuationToken continuationToken = null;
		do
		{   
			// Get the list of input blobs from the input storage client object.
			BlobResultSegment blobList = inputClient.ListBlobsSegmented(folderPath,
    					              true,
    	                              BlobListingDetails.Metadata,
    	                              null,
    	                              continuationToken,
    	                              null,
    	                              null);
			// Return a string derived from parsing each blob.
    		output = Calculate(blobList, logger, folderPath, ref continuationToken, "Microsoft");
		} while (continuationToken != null);

	Einzelheiten finden Sie in der Dokumentation für die [ListBlobsSegmented](https://msdn.microsoft.com/library/jj717596.aspx)-Methode.

2.	Der Code für die Verwendung durch den Satz von Blobs geht logischerweise in die do-while-Schleife ein. In der **Execute**-Methode übergibt die do-while-Schleife die Liste der Blobs an eine Methode namens **Calculate**. Die Methode gibt eine Zeichenfolgenvariable mit dem Namen **output** zurück, die das Ergebnis des Durchlaufens aller Blobs im Segment ist.

	Sie gibt die Anzahl der Vorkommnisse des Suchbegriffs (**Microsoft**) im Blob zurück, die an die **Calculate**-Methode übergeben wurden.

			output += string.Format("{0} occurrences of the search term "{1}" were found in the file {2}.\r\n", wordCount, searchTerm, inputBlob.Name);

3.	Sobald die **Calculate**-Methode mit der Arbeit fertig ist, muss sie in ein neues Blob geschrieben werden. Also kann für jeden Satz an Blobs, die verarbeitet werden, ein neues Blob mit den Ergebnissen geschrieben werden. Um ein neues Blob zu schreiben, müssen Sie zunächst das Ausgabedataset suchen.

			// Get the output dataset using the name of the dataset matched to a name in the Activity output collection.
			Dataset outputDataset = datasets.Single(dataset => dataset.Name == activity.Outputs.Single().Name);

			// Convert to blob location object.
			outputLocation = outputDataset.Properties.TypeProperties as AzureBlobDataset;

4.	Der Code ruft auch eine Hilfsmethode auf: **GetFolderPath** zum Abrufen des Ordnerpfads (der Name des Speichercontainers).
 
			folderPath = GetFolderPath(outputDataset);

	**GetFolderPath** wandelt das DataSet-Objekt in ein AzureBlobDataSet-Objekt um, das über eine Eigenschaft namens „FolderPath“ verfügt.
			
			AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;
			
			return blobDataset.FolderPath;

5.	Der Code ruft die **GetFileName**-Methode zum Abrufen des Dateinamens (Blob-Name) auf. Der Code ähnelt dem obigen Code zum Abrufen des Ordnerpfads.

			AzureBlobDataset blobDataset = dataArtifact.Properties.TypeProperties as AzureBlobDataset;

			return blobDataset.FileName;

6.	Der Name der Datei wird geschrieben, indem ein neues URI-Objekt erstellt wird. Der URI-Konstruktor verwendet die **BlobEndpoint**-Eigenschaft, um den Containernamen zurückzugeben. Ordnerpfad und Dateiname werden hinzugefügt, um den URI für den Ausgabe-Blob zu erstellen.

			// Write the name of the file. 
			Uri outputBlobUri = new Uri(outputStorageAccount.BlobEndpoint, folderPath + "/" + GetFileName(outputDataset));

7.	Der Name der Datei wurde geschrieben und Sie können jetzt die Ausgabezeichenfolge aus der Calculate-Methode in ein neues Blob schreiben:

			// Create a new blob and upload the output text.
			CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
			logger.Write("Writing {0} to the output blob", output);
			outputBlob.UploadText(output);

## Erstellen der Data Factory

Sie haben im Abschnitt **Erstellen der benutzerdefinierten Aktivität** eine benutzerdefinierte Aktivität erstellt und die Zip-Datei mit Binärdateien und der PDB-Datei in einen Azure-Blob-Container hochgeladen. In diesem Abschnitt erstellen Sie eine **Azure Data Factory** mit einer **Pipeline**, die die **benutzerdefinierte Aktivität** verwendet.
 
Das Eingabe-Dataset für die benutzerdefinierte Aktivität stellt die Blobs (Dateien) im Eingabeordner (Mycontainer\\inputfolder) im Blob-Speicher dar. Das Ausgabe-Dataset für die Aktivität stellt die Ausgabeblobs im Ausgabeordner (Mycontainer\\outputfolder) im Blob-Speicher dar.

Erstellen Sie eine Datei mit dem Namen „File.txt“ mit dem folgenden Inhalt und laden Sie sie in „Mycontainer\\inputfolder“ hoch (Mycontainer ist der Name des Azure Blob-Containers und Inputfolder ist der Name des Ordners in diesem Container).

	test custom activity Microsoft test custom activity Microsoft

Der Eingabeordner entspricht einem Slice in Azure Data Factory, auch wenn der Ordner über zwei oder mehr Dateien verfügt. Wenn jeder Slice von der Pipeline verarbeitet wird, durchläuft die benutzerdefinierte Aktivität alle Blobs im Eingabeordner für diesen Slice.

Sie sehen eine Ausgabedatei im Ordner Mycontainer\\output mit einer oder mehr Zeilen (identisch mit der Anzahl an Blobs im Eingabeordner):
 
	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.


Dies sind die einzelnen Schritte, die Sie in diesem Abschnitt ausführen:

1. Erstellen einer **Data Factory**
2. Erstellen Sie **verknüpfte Dienste** für das HDInsight-Cluster, auf dem die benutzerdefinierte Aktivität als reiner Zuordnungsauftrag ausgeführt wird und den Azure Storage, der die Eingabe- und Ausgabe-Blobs enthält. 
2. Erstellen Sie Eingabe- und Ausgabe-**Datasets**, die die Eingabe und Ausgabe der benutzerdefinierten Aktivität darstellen. 
3. Erstellen Sie eine **Pipeline**, die die benutzerdefinierte Aktivität verwendet, und führen Sie sie aus.

### Schritt 1: Erstellen der Data Factory

1.	Nach der Anmeldung beim Azure-Portal gehen Sie wie folgt vor:
	1.	Klicken Sie im linken Menü auf **NEU**.
	2.	Klicken Sie auf dem Blatt **Neu** auf **Daten und Analysen**.
	3.	Klicken Sie auf dem Blatt **Datenanalyse** auf **Data Factory**.
2.	Geben Sie auf dem Blatt **Neue Data Factory** als Namen die Zeichenfolge **CustomActivityFactory** ein. Der Name der Azure Data Factory muss global eindeutig sein. Bei Anzeige der Fehlermeldung **Data Factory-Name "CustomActivityFactory" ist nicht verfügbar** ändern Sie den Namen der Data Factory (z. B.in **IhrNameCustomActivityFactory**) und wiederholen den Vorgang.
3.	Klicken Sie auf **Name der Ressourcengruppe** und wählen eine vorhandene Ressourcengruppe aus oder erstellen eine neue. 
4.	Stellen Sie sicher, dass Sie das richtige **Abonnement** und die richtige **Region** verwenden, in dem die Data Factory erstellt werden soll. 
5.	Klicken Sie auf dem Blatt **Neue Data Factory** auf **Erstellen**.
6.	Sie sehen die erstellte Data Factory im **Dashboard** des Azure-Portals.
7.	Nachdem die Data Factory erfolgreich erstellt wurde, sehen Sie das Data Factory-Blatt mit dem Inhalt der Data Factory.

### Schritt 2: Erstellen von verknüpften Diensten

Verknüpfte Dienste verknüpfen Datenspeicher oder Serverdienste mit einer Azure Data Factory. In diesem Schritt verknüpfen Sie Ihr Azure Storage-Konto und das Azure HDInsight-Cluster mit Ihrer Data Factory.

#### Erstellen des mit Azure Storage verknüpften Diensts

1.	Klicken Sie auf die Kachel **Erstellen und Bereitstellen** auf dem Blatt **DATA FACTORY** für **CustomActivityFactory**. Der Data Factory-Editor wird gestartet.
2.	Klicken Sie in der Befehlsleiste auf **Neuer Datenspeicher** und wählen Sie **Azure Storage**. Das JSON-Skript zum Erstellen eines mit einem Azure-Speicher verknüpften Diensts sollte im Editor angezeigt werden.
3.	Ersetzen Sie **Kontoname** durch den Namen Ihres Azure-Speicherkontos und **Kontoschlüssel** durch den Zugriffsschlüssel des Azure-Speicherkontos. Informationen zum Abrufen Ihres Speicherzugriffsschlüssels finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).
4.	Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.


#### Erstellen des mit Azure-HDInsight verknüpften Diensts 
Der Azure Data Factory-Dienst unterstützt das Erstellen eines Clusters bei Bedarf und verwendet ihn zum Verarbeiten der Eingabe zum Erzeugen von Ausgabedaten. Sie können auch Ihren eigenen Cluster für den gleichen Zweck verwenden. Wenn Sie den bedarfsgesteuerten HDInsight-Cluster verwenden, wird für jeden Slice ein Cluster erstellt. Bei Verwendung eines eigenen HDInsight-Clusters ist der Cluster sofort zur Sliceverarbeitung bereit. Aus diesem Grund werden bei Verwendung des bedarfsgesteuerten Clusters die Ausgabedaten möglicherweise nicht so schnell angezeigt wie bei der Verwendung Ihres eigenen Clusters.

> [AZURE.NOTE] Zur Laufzeit wird eine Instanz einer .NET-Aktivität nur auf einem Workerknoten im HDInsight-Cluster ausgeführt. Eine Skalierung auf mehrere Knoten ist nicht möglich. Mehrere Instanzen der .NET-Aktivität können auf verschiedenen Knoten des HDInsight-Clusters parallel ausgeführt werden.

Wenn Sie das Lernprogramm [Erste Schritte mit Azure Data Factory][adfgetstarted] mit der exemplarischen Vorgehensweise für das [Verwenden von Pig und Hive mit Azure Data Factory][hivewalkthrough] erweitert haben, können Sie die Erstellung dieses verknüpften Diensts überspringen und den verknüpften Dienst verwenden, der bereits in der ADFTutorialDataFactory vorhanden ist.


##### So verwenden Sie einen bedarfsgesteuerten HDInsight-Cluster

1. Klicken Sie im **Azure-Portal** auf der Startseite der Data Factory auf **Erstellen und bereitstellen**.
2. Klicken Sie im Data Factory-Editor auf der Befehlsleiste auf **Neu -> Compute**, und wählen Sie im Menü **On-Demand-HDInsight-Cluster** aus.
2. Führen Sie im JSON-Skript folgende Schritte aus:
	1. Geben Sie für die Eigenschaft **clusterSize** die Größe des HDInsight-Clusters an.
	3. Geben Sie für die **timeToLive**-Eigenschaft an, wie lange sich der Cluster im Leerlauf befinden darf, bevor er gelöscht wird.
	4. Geben Sie für die **version**-Eigenschaft die HDInsight-Version an, die Sie verwenden möchten. Wenn Sie diese Eigenschaft ausschließen, wird die neueste Version verwendet.  
	5. Geben Sie für **linkedServiceName** den Dienst **StorageLinkedService** an, den Sie im Lernprogramm "Erste Schritte" erstellt haben.

			{
			  "name": "HDInsightOnDemandLinkedService",
			  "properties": {
			    "type": "HDInsightOnDemand",
			    "typeProperties": {
			      "clusterSize": "1",
			      "timeToLive": "00:05:00",
			      "version": "3.2",
			      "linkedServiceName": "StorageLinkedService"
			    }
			  }
			}

2. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.

##### So verwenden Sie Ihren eigenen HDInsight-Cluster:

1. Klicken Sie im **Azure-Portal** auf der Startseite der Data Factory auf **Erstellen und bereitstellen**.
2. Klicken Sie im **Data Factory-Editor** auf der Befehlsleiste auf **Neu -> Compute**, und wählen Sie im Menü **HDInsight-Cluster** aus.
2. Führen Sie im JSON-Skript folgende Schritte aus:
	1. Geben Sie für die **clusterUri**-Eigenschaft die URL für Ihr HDInsight ein. Beispiel: https://<clustername>.azurehdinsight.net/     
	2. Geben Sie für die **UserName**-Eigenschaft den Benutzernamen ein, der Zugriff auf den HDInsight-Cluster hat.
	3. Geben Sie für die **Password**-Eigenschaft das Kennwort für das Benutzerkonto ein.
	4. Geben Sie für die **LinkedServiceName**-Eigenschaft das **StorageLinkedService**-Element ein. Dies ist der verknüpfte Dienst, den Sie im Lernprogramm "Erste Schritte" erstellt haben.

2. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.

### Schritt 3: Erstellen von Datasets
In diesem Schritt erstellen Sie Datasets, um die Eingabe- und Ausgabedaten darstellen.

#### Erstellen eines Eingabedatasets
1.	Klicken Sie im Editor für die Data Factory auf der Symbolleiste auf **Neues Dataset** und anschließend im Dropdownmenü auf **Azure-Blobspeicher**.
2.	Ersetzen Sie den JSON-Code im rechten Bereich durch den folgenden JSON-Codeausschnitt:

			{
			    "name": "InputDataset",
			    "properties": {
			        "type": "AzureBlob",
			        "linkedServiceName": "StorageLinkedService",
			        "typeProperties": {
			            "folderPath": "adftutorial/customactivityinput/",
			            "format": {
			                "type": "TextFormat"
			            }
			        },
			        "availability": {
			            "frequency": "Hour",
			            "interval": 1
			        },
			        "external": true,
			        "policy": {}
			    }
			}

	Sie werden weiter unten in dieser exemplarischen Vorgehensweise eine Pipeline mit der Startzeit 2015-11-16T00:00:00Z und der Endzeit 2015-11-16T05:00:00Z erstellen. Sie ist so eingerichtet, dass Daten stündlich erstellt werden, sodass es fünf Eingabe- und Ausgabeslices gibt (zwischen **00**:00:00 und **05**:00:00).

	Die **Häufigkeit** und das **Intervall** für das Eingabedataset ist auf **Hour** und **1** festgelegt, was bedeutet, dass der Eingabeslice stündlich verfügbar ist. In diesem Beispiel nehmen wir die gleiche Datei (file.txt) im Eingabeordner.

	Dies sind die Startzeiten für jeden Slice, die durch die Systemvariable „SliceStart“ im obigen JSON-Codeausschnitt dargestellt werden.

	
3.	Klicken Sie in der Symbolleiste auf **Bereitstellen**, um **InputDataset** bereitzustellen. Vergewissern Sie sich, dass die Meldung **TABELLE ERFOLGREICH ERSTELLT** auf der Titelleiste des Editors angezeigt wird.


#### Erstellen einer Ausgabetabelle

1. Klicken Sie im **Data Factory-Editor** auf **Neues Dataset** und dann auf der Befehlsleiste auf **Azure-Blobspeicher**.
2. Ersetzen Sie das JSON-Skript im rechten Bereich durch das folgende JSON-Skript:

		{
		    "name": "OutputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "{slice}.txt",
		            "folderPath": "adftutorial/customactivityoutput",
		            "partitionedBy": [
		                {
		                    "name": "slice",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "yyyy-MM-dd-HH"
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

	Eine Ausgabedatei und ein Ausgabe-Blob wird für jeden Eingabeslice generiert. Im Folgenden sehen Sie, wie eine Ausgabedatei für jeden Slice benannt wird. Alle Ausgabedateien werden in einem Ausgabeordner generiert: **adftutorial\\customactivityoutput**.

	| Slice | Startzeit | Ausgabedatei |
	| :---- | :--------- | :---------- | 
	| 1 | 2015-11-16T00:00:00 | 2015-11-16-00.txt |
	| 2 | 2015-11-16T01:00:00 | 2015-11-16-01.txt |
	| 3 | 2015-11-16T02:00:00 | 2015-11-16-02.txt |
	| 4 | 2015-11-16T03:00:00 | 2015-11-16-03.txt |
	| 5 | 2015-11-16T04:00:00 | 2015-11-16-04.txt |

	Denken Sie daran, dass alle Dateien in einem Eingabeordner zu einem Slice mit den oben genannten Startzeiten gehören. Wenn dieser Slice verarbeitet wird, durchsucht die benutzerdefinierte Aktivität jede Datei und erzeugt eine Zeile in der Ausgabedatei mit der Anzahl der Vorkommnisse des Suchbegriffs („Microsoft“). Wenn im Eingabeordner drei Dateien vorhanden sind, gibt es drei Zeilen in der Ausgabedatei für jeden stündlichen Slice: 2015-11-16-00.txt 2015-11-16:01:00:00.txt und so weiter.


2. Klicken Sie in der Befehlsleiste auf **Bereitstellen**, um das **OutputDataset** bereitzustellen.


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
		            "Name": "InputDataset"
		          }
		        ],
		        "Outputs": [
		          {
		            "Name": "OutputDataset"
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
    		"start": "2015-11-16T00:00:00Z",
			"end": "2015-11-16T05:00:00Z",
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
	- **PackageLinkedService** ist auf **StorageLinkedService** festgelegt, das auf den Blobspeicher verweist, der die Zip-Datei mit der benutzerdefinierten Aktivität enthält. Wenn Sie andere Azure-Speicherkonten für die Eingabe- und Ausgabedateien und die Zip-Datei mit der benutzerdefinierten Aktivität verwenden, müssen Sie einen weiteren verknüpften Azure Storage-Dienst erstellen. Dieser Artikel setzt voraus, dass Sie das gleiche Azure Storage-Konto verwenden.
	- **PackageFile** wird auf **customactivitycontainer/MyCustomActivity.zip** festgelegt. Das entspricht dem Format: containerforthezip/nameofthezip.zip.
	- Die benutzerdefinierte Aktivität verwendet **InputDataset** als Eingabe und **OutputDataset** als Ausgabe.
	- Die Eigenschaft „LinkedServiceName“ der benutzerdefinierten Aktivität zeigt auf **HDInsightLinkedService**, das Azure Data Factory mitteilt, dass die benutzerdefinierte Aktivität in einem Azure HDInsight-Cluster ausgeführt werden muss.
	- Die Eigenschaft **IsPaused** ist standardmäßig auf **false** festgelegt. Die Pipeline wird in diesem Beispiel sofort ausgeführt, da die Slices in der Vergangenheit starten. Legen Sie diese Eigenschaft auf „true“ fest, um die Pipeline anzuhalten, und legen Sie sie zum Neustart wieder auf „false“ fest. 
	- Die **Start-** und **Endzeiten** werden **5** Stunden auseinander festgelegt und Slices werden stündlich erstellt, sodass insgesamt fünf Slices von der Pipeline erstellt werden. 


4. Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um die Pipeline bereitzustellen.

### Überwachen der Pipeline
 
8. Klicken Sie im Azure-Portal auf dem Blatt „Data Factory“ auf **Diagramm**.
	
	![Kachel "Diagramm"](./media/data-factory-use-custom-activities/DataFactoryBlade.png)
 
9. Klicken Sie in der Diagrammansicht jetzt auf „OutputDataset“.
 
	![Diagrammansicht](./media/data-factory-use-custom-activities/diagram.png)

10. Sie sollten sehen können, dass die fünf Ausgabeslices im Zustand „Bereit“ sind, wenn sie bereits erzeugt wurden.

	![Ausgabeslices](./media/data-factory-use-custom-activities/OutputSlices.png)
	
12. Stellen Sie sicher, dass die Ausgabedateien im Blobspeicher im **adftutorial**-Container generiert werden.

	![Ausgabe der benutzerdefinierten Aktivität][image-data-factory-ouput-from-custom-activity]

9. Wenn Sie die Ausgabedatei öffnen, sollte die Ausgabe ähnlich der folgenden angezeigt werden:

	Es wurden zwei Vorkommen für den Suchbegriff „Microsoft“ in der Datei inputfolder/2015-11-16-00/file.txt gefunden.

10.	Verwenden Sie das [Azure-Portal][azure-preview-portal] oder die Azure PowerShell-Cmdlets zum Überwachen Ihrer Data Factory, Pipelines und Datasets. Sie können die Meldungen vom **ActivityLogger** im Code für die benutzerdefinierte Aktivität in den Protokollen (insbesondere "user-0.log") sehen, die Sie aus dem Portal oder mithilfe von Cmdlets herunterladen können.

	![Herunterladen von Protokollen aus einer benutzerdefinierten Aktivität][image-data-factory-download-logs-from-custom-activity]


Unter [Überwachen und Verwalten von Pipelines](data-factory-monitor-manage-pipelines.md) finden Sie eine ausführliche Anleitung zum Überwachen von Datasets und Pipelines.

### Debuggen der Pipeline
Das Debuggen umfasst einige grundlegende Verfahren:

1.	Wenn der Eingabeslice nicht auf **Bereit** festgelegt ist, stellen Sie sicher, dass die Struktur des Eingabeordners korrekt ist und die Datei file.txt im Eingabeordner vorhanden ist.
2.	Verwenden Sie in der **Execute**-Methode der benutzerdefinierten Aktivität das **IActivityLogger**-Objekt, um Informationen zu protokollieren, die Ihnen beim Beheben von Problemen helfen. Die protokollierten Nachrichten werden in der Datei user\_0.log angezeigt. 

	Klicken Sie auf dem Blatt **OutputDataset** auf den Slice, um das Blatt **DATENSLICE** für diesen Slice anzuzeigen. Die **Aktivitätsausführungen** für diesen Slice werden angezeigt. Sie sollten genau eine Aktivitätsausführung für den Slice sehen. Wenn Sie in der Befehlsleiste auf „Ausführen“ klicken, können Sie für den gleichen Slice eine weitere Aktivität ausführen.

	Wenn Sie auf die Aktivitätsausführung klicken, sehen Sie das Blatt **Aktivitätsausführung – Details** mit einer Liste der Protokolldateien. Protokollierte Nachrichten werden in der Datei user\_0.log angezeigt. Tritt ein Fehler auf, sehen Sie drei Aktivitätsausführungen, da die Anzahl der Wiederholungsversuche in der Pipeline oder Aktivitäts-JSON auf 3 festgelegt ist. Wenn Sie die Aktivitätsausführung starten, sehen Sie die Protokolldateien, die Sie zum Beheben von Fehlern überprüfen können.

	Klicken Sie in der Liste der Protokolldateien auf die Datei **user-0.log**. Im rechten Bereich werden die Ergebnisse der Verwendung der **IActivityLogger.Write**-Methode angezeigt.

	Sie sollten außerdem die Datei **system-0.log** auf alle Systemfehlermeldungen und -ausnahmen überprüfen.

3.	Fügen Sie die **PDB**-Datei in der Zipdatei hinzu, sodass die Fehlerdetails über Informationen wie z. B. die **Aufrufliste** verfügen, wenn ein Fehler auftritt.
4.	Alle Dateien in der Zip-Datei für die benutzerdefinierte Aktivität müssen auf der **obersten Ebene** abliegen und dürfen keine Unterordner haben.
5.	Stellen Sie sicher, dass **AssemblyName** (MyDotNetActivity.dll), **EntryPoint**(MyDotNetActivityNS.MyDotNetActivity), **PackageFile** (customactivitycontainer/Mycustomactivity.ZIP), und **PackageLinkedService** (sollte auf den Azure Blob-Speicher verweisen, der die Zip-Datei enthält) auf die richtigen Werte festgelegt sind. 
6.	Wenn Sie einen Fehler behoben haben und den Slice erneut verarbeiten wollen, klicken Sie auf dem Blatt **OutputDataset** mit der rechten Maustaste auf den Slice und klicken Sie anschließend auf **Ausführen**. 


## Aktualisieren der benutzerdefinierten Aktivität
Wenn Sie den Code für die benutzerdefinierte Aktivität aktualisieren, führen Sie einen Buildvorgang aus. Laden Sie die ZIP-Datei mit den neuen Binärdateien in den Blobspeicher hoch.

## Zugreifen auf erweiterte Eigenschaften
Sie können erweiterte Eigenschaften in der Aktivität JSON wie folgt deklarieren:

	"typeProperties": {
	  "AssemblyName": "MyDotNetActivity.dll",
	  "EntryPoint": "MyDotNetActivityNS.MyDotNetActivity",
	  "PackageLinkedService": "StorageLinkedService",
	  "PackageFile": "customactivitycontainer/MyDotNetActivity.zip",
	  "extendedProperties": {
	    "SliceStart": "$$Text.Format('{0:yyyyMMddHH-mm}', Time.AddMinutes(SliceStart, 0))",
		"DataFactoryName": "CustomActivityFactory"
	  }
	},

Im obigen Beispiel gibt es zwei erweiterte Eigenschaften: **SliceStart** und **DataFactoryName**. Der Wert für „SliceStart“ basiert auf der SliceStart-Systemvariablen. Eine Liste der unterstützten Systemvariablen finden Sie unter [Systemvariablen](data-factory-scheduling-and-execution.md#data-factory-system-variables). Der Wert für „DataFactoryName“ ist hartcodiert und auf „CustomActivityFactory“ festgelegt.

Um auf diese erweiterten Eigenschaften in der **Execute**-Methode zuzugreifen, verwenden Sie Code wie den folgenden:

	// to get extended properties (for example: SliceStart)
	DotNetActivity dotNetActivity = (DotNetActivity)activity.TypeProperties;
	string sliceStartString = dotNetActivity.ExtendedProperties["SliceStart"];

	// to log all extended properties                               
    IDictionary<string, string> extendedProperties = dotNetActivity.ExtendedProperties;
    logger.Write("Logging extended properties if any...");
    foreach (KeyValuePair<string, string> entry in extendedProperties)
    {
    	logger.Write("<key:{0}> <value:{1}>", entry.Key, entry.Value);
	}


## <a name="AzureBatch"></a> Verwenden des verknüpften Azure Batch-Dienstes
> [AZURE.NOTE] Unter [Grundlagen zu Azure Batch][batch-technical-overview] finden Sie eine Übersicht über den Azure Batch-Dienst. Unter [Erste Schritte mit der Azure Batch-Bibliothek für .NET][batch-get-started] werden die ersten Schritte mit dem Azure Batch-Dienst gezeigt.

Sie können Ihre benutzerdefinierten .NET-Aktivitäten mithilfe von Azure Batch als eine Compute-Ressource ausführen. Sie müssen Ihren eigenen Azure Batch-Pools erstellen und die Anzahl der virtuellen Computer (VM) zusammen mit anderen Konfigurationen angeben. Azure Batch-Pools bieten Kunden die folgenden Funktionen:

1. Pools erstellen, die von einem bis zu tausenden Kernen enthalten
2. Automatische Skalierung der VM-Anzahl basierend auf einer Formel
3. Unterstützt VMs beliebiger Größe
4. Konfigurierbare Anzahl an Aufgaben pro VM
5. Platzierung einer unbegrenzten Anzahl von Aufgaben in der Warteschlange


Es folgen die allgemeinen Schritte zur Verwendung des mit Azure Batch verknüpften Diensts in der exemplarischen Vorgehensweise, die im vorherigen Abschnitt beschrieben wurde:

1. Erstellen Sie mit dem [Azure-Portal](http://manage.windowsazure.com) ein Azure Batch-Konto. Anweisungen finden Sie im Artikel [Erstellen und Verwalten eines Azure Batch-Kontos][batch-create-account]. Notieren Sie den Azure Batch-Kontonamen und -Kontoschlüssel.

	Sie können auch das [New-AzureBatchAccount][new-azure-batch-account]-Cmdlet verwenden, um ein Azure Batch-Konto zu erstellen. Ausführliche Anweisungen zur Verwendung dieses Cmdlets finden Sie unter [Verwenden von Azure PowerShell zum Verwalten des Azure Batch-Kontos][azure-batch-blog].
2. Erstellen Sie einen Azure Batch-Pool. Sie können zum Erstellen eines Azure Batch-Pools das [Azure Batch-Explorer-Tool][batch-explorer] herunterladen und verwenden (oder) die [Azure Batch-Bibliothek für .NET][batch-net-library]. Unter [Azure Batch-Explorer – Beispiel für eine exemplarische Vorgehensweise][batch-explorer-walkthrough] finden Sie Schritt-für-Schritt-Anweisungen zur Verwendung des Azure Batch-Explorers.

	Sie können auch das [New-AzureRmBatchPool](https://msdn.microsoft.com/library/mt628690.aspx)-Cmdlet verwenden, um einen Azure Batch-Pool zu erstellen.

	Sie können den Azure Batch-Pool mit mindestens zwei Compute-Knoten erstellen, sodass Slices parallel verarbeitet werden. Wenn Sie Batch Explorer verwenden:

	- Geben Sie eine ID für den Pool ein (**Pool-ID**). Beachten Sie die **ID des Pools**; Sie benötigen sie bei der Erstellung der Data Factory-Projektmappe. 
	- Geben Sie **Windows Server 2012 R2** für die Einstellung „Betriebssystem-Familie“ ein.
	- Geben Sie **2** als Wert für die Einstellung **Max. Aufgaben pro Serverknoten** ein.
	- Geben Sie **2** als Wert für die Einstellung **Anzahl von Zielzuweisungen** ein. 

	Der Data Factory-Dienst erstellt im Azure-Batch einen Auftrag mit dem Namen: adf-<pool name>:job-xxx. Bei jeder Aktivitätsausführung eines Slices wird eine Aufgabe erstellt. Wenn zehn Slices zur Verarbeitung bereitstehen, werden zehn Aufgaben in diesem Auftrag erstellt. Sie können mehrere Slices parallel ausführen, wenn Sie über mehrere Compute-Knoten im Pool verfügen. Sie können auch mehrere Slices auf dem gleichen Compute-Knoten ausführen, wenn die maximale Anzahl der Aufgaben pro Compute-Knoten auf mehr als 1 festgelegt ist.
	
	![Batch Explorer-Aufgaben](./media/data-factory-use-custom-activities/BatchExplorerTasks.png)

	![Data Factory und Batch](./media/data-factory-use-custom-activities/DataFactoryAndBatch.png)

2. Erstellen Sie mithilfe der folgenden JSON-Vorlage einen mit Azure Batch verknüpften Dienst. Der Data Factory-Editor bietet Ihnen als Einstieg eine ähnliche Vorlage. Geben Sie im JSON-Codeausschnitt einen Azure Batch-Kontonamen, -Kontoschlüssel und -Poolnamen an.

		{
		  "name": "AzureBatchLinkedService",
		  "properties": {
		    "type": "AzureBatch",
		    "typeProperties": {
		      "accountName": "<Azure Batch account name>",
			  "batchUri": "https://<region>.batch.azure.com",
		      "accessKey": "<Azure Batch account key>",
		      "poolName": "<Azure Batch pool name>",
		      "linkedServiceName": "<Specify associated storage linked service reference here>"
		    }
		  }
		}

	> [AZURE.IMPORTANT] Die **URL** auf dem Blatt **Azure-Batch-Konto** hat folgendes Format: Kontoname.Region.Batch.azure.com. Für die **batchUri**-Eigenschaft in JSON müssen Sie in der URL **"Kontoname." entfernen** den **Kontoname**n für die JSON-Eigenschaft **accountName** verwenden.

	Für die **poolName**-Eigenschaft können Sie auch die ID des Pools anstelle des Namens des Pools angeben.

	Im MSDN-Thema [Mit Azure Batch verknüpfter Dienst](https://msdn.microsoft.com/library/mt163609.aspx) finden Sie Beschreibungen dieser Eigenschaften.

2.  Öffnen Sie im Data Factory-Editor die JSON-Definition für die Pipeline, die Sie in der exemplarischen Vorgehensweise erstellt haben, und ersetzen Sie **HDInsightLinkedService** durch **AzureBatchLinkedService**.
3.  Sie können die Start- und Endzeiten für die Pipeline bei Bedarf ändern, damit Sie das Szenario mit dem Azure Batch-Dienst testen können.
4.  Wie im folgenden Diagramm dargestellt, können Sie im Azure Batch-Explorer die Azure Batch-Aufgaben im Zusammenhang mit der Verarbeitung der Slices sehen.

	![Azure Batch-Aufgaben][image-data-factory-azure-batch-tasks]

> [AZURE.NOTE] Data Factory unterstützt im Gegensatz zu HDInsight für Azure Batch keine bedarfsorientierte Option. Sie können nur Ihren eigenen Azure Batch-Pool in einer Azure Data Factory verwenden.

## Weitere Informationen

[Azure Data Factory-Aktualisierungen: Ausführen von benutzerdefinierten ADF-.NET-Aktivitäten mit Azure Batch](https://azure.microsoft.com/blog/2015/05/01/azure-data-factory-updates-execute-adf-custom-net-activities-using-azure-batch/).

[batch-net-library]: ../batch/batch-dotnet-get-started.md
[batch-explorer]: https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer
[batch-explorer-walkthrough]: http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx
[batch-create-account]: ../batch/batch-account-create-portal.md
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

<!---HONumber=AcomDC_0309_2016-->