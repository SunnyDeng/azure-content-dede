<properties
    pageTitle="HPC und Datenorchestrierung mit Azure Batch und Data Factory"
    description="Beschreibt, wie Sie große Datenmengen in einer Azure Data Factory-Pipeline verarbeiten, indem Sie die parallele Verarbeitung von Azure Batch nutzen."
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
    ms.date="01/20/2016"
    ms.author="spelluru"/>
# HPC und Datenorchestrierung mit Azure Batch und Data Factory

High Performance Computing (HPC) war in der Vergangenheit die Domäne von lokalen Datencentern: Ein Supercomputer verarbeitete Daten, aber es stand nur eine begrenzte Anzahl von physischen Computern zur Verfügung. Der Dienst Azure Batch verändert dies grundlegend, denn er stellt HPC als Service zur Verfügung Bei Azure Batch können Sie so viele Computer konfigurieren, wie Sie benötigen. Batch übernimmt auch das Planen und Koordinieren der Arbeit, sodass Sie sich ganz auf die auszuführenden Algorithmen konzentrieren können. Azure Data Factory ist eine perfekte Ergänzung zu Azure Batch, weil Data Factory die Orchestrierung von Datentransfers vereinfacht. Mit Data Factory können Sie ein regelmäßiges Verschieben von Daten für ETL-Prozesse (Extrahieren, Transformieren und Laden) einrichten, die Daten verarbeiten und anschließend die Ergebnisse in einen permanenten Speicher transferieren. Zum Beispiel können von Sensoren gesammelte Daten (durch Data Factory) an einen temporären Speicherort verschoben werden, wo Batch sie (unter der Kontrolle von Data Factory) verarbeitet und neue Ergebnisse ermittelt. Anschließend verschiebt Data Factory die Ergebnisse in ein finales Repository als Speicherort. Durch diese beiden Hand in Hand arbeitenden Dienste können Sie HPC effizient nutzen, um große Datenmengen nach einem regelmäßigen Zeitplan zu verarbeiten.

Hier stellen wir Ihnen ein komplettes Lösungsbeispiel vor, bei dem umfangreiche Datasets automatisch verschoben und verarbeitet werden. Die Architektur ist für viele Szenarien relevant, z. B. für die Risikomodellierung bei Finanzdienstleistern, für Bildverarbeitung und -rendering und für die Genomanalyse. Architekten und IT-Entscheidungsträger können sich im Diagramm und in den grundlegenden Schritten eine Übersicht verschaffen. Entwickler können den Code als Ausgangspunkt für ihre eigene Implementierung verwenden. Dieser Artikel enthält die gesamte Lösung.

Wenn Sie mit den beiden Diensten noch nicht vertraut sind, sollten Sie sich in der Dokumentation zu [Azure Batch](../batch/batch-api-basics.md) und [Data Factory](data-factory-introduction.md) informieren, bevor Sie sich mit den Schritten der Beispiellösung beschäftigen.

## Architekturdiagramm

Das Diagramm veranschaulicht, (1) wie Data Factory die Datenverschiebung und -verarbeitung koordiniert und (2) wie Azure Batch die Daten parallel verarbeitet. Laden Sie zum einfachen Nachschlagen das Diagramm herunter, und drucken Sie es aus (28 x 43 cm oder A3-Format): [HPC und Datenorchestrierung mit Azure Batch und Data Factory](http://go.microsoft.com/fwlink/?LinkId=717686).

![Diagramm für HPC als Service](./media/data-factory-data-processing-using-batch/image1.png)

Dies sind die grundlegenden Schritte des Prozesses. Die Lösung enthält Code und Erklärungen zum Erstellen der End-to-End-Lösung.

1.  Konfigurieren von Azure mit einem Pool von Computeknoten (VMs). Sie können die Anzahl der Knoten und die Größe der einzelnen Knoten angeben.

2.  Erstellen Sie eine Azure Data Factory-Instanz, die mit Entitäten konfiguriert wird, die Azure Blob-Speicher, Azure Batch-Computedienst, Ein-/Ausgabedaten und einen Workflow bzw. eine Pipeline mit Aktivitäten zum Verschieben und Transformieren von Daten darstellen.

3.  Die Data Factory-Pipeline verfügt über eine benutzerdefinierte .NET-Aktivität, die für die Ausführung in Azure Batch-Knotenpools konfiguriert ist.

4.  Große Mengen von Eingabedaten als Blobs im Azure-Speicher. Daten werden in logische Einheiten (in der Regel nach Zeit) aufgeteilt.

5.  Data Factory kopiert Daten, die parallel zum sekundären Speicherort verarbeitet werden.

6.  Data Factory führt die benutzerdefinierte Aktivität mit dem von Batch zugeordneten Pool aus. Data Factory kann Aktivitäten gleichzeitig ausführen. Jede Aktivität verarbeitet einen Slice von Daten. Die Aufgaben werden im Azure-Speicher gespeichert.

7.  Nachdem alle Ergebnisse abgerufen werden, verschiebt Data Factory die Ergebnisse für die Verteilung über eine App oder zur weiteren Verarbeitung durch andere Tools an einen dritten Speicherort.

## Architekturlösung

Die Lösung zählt, wie häufig ein Suchbegriff („Microsoft“) in Eingabedateien vorkommt, die in einer Zeitreihe organisiert sind. Sie gibt die Anzahl der Ausgabedateien an.

**Zeit**: Wenn Sie mit Azure, Data Factory und Batch vertraut sind und die Voraussetzungen abgeschlossen haben, wird diese Lösung in ca. 1 bis 2 Stunden abgeschlossen.

### Voraussetzungen

1.  **Azure-Abonnement**. Wenn Sie über kein Azure-Abonnement verfügen, können Sie in wenigen Minuten ein kostenloses Testkonto erstellen. Weitere Informationen finden Sie unter [Kostenlose Testversion](https://azure.microsoft.com/pricing/free-trial/).

2.  **Azure-Speicherkonto**. Sie benötigen ein Azure-Speicherkonto zum Speichern der Daten in diesem Tutorial. Falls Sie noch kein Azure-Speicherkonto haben, lesen Sie den Artikel [Erstellen eines Speicherkontos](../storage/storage-create-storage-account.md#create-a-storage-account). Die Beispiellösung verwendet Blob-Speicher.

3.  Erstellen Sie mit dem [Azure-Portal](http://manage.windowsazure.com/) ein **Azure Batch-Konto**. Weitere Informationen finden Sie unter [Erstellen und Verwalten eines Azure Batch-Kontos](../batch/batch-account-create-portal.md) Notieren Sie den Azure Batch-Kontonamen und -Kontoschlüssel. Sie können auch das [New-AzureBatchAccount](https://msdn.microsoft.com/library/mt603749.aspx)-Cmdlet verwenden, um ein Azure Batch-Konto zu erstellen. Unter [Erste Schritte mit Azure Batch-PowerShell-Cmdlets](../batch/batch-powershell-cmdlets-get-started.md) erhalten Sie weitere Einzelheiten zur Verwendung dieses Cmdlets.

    Die Beispiellösung verwendet Azure Batch (indirekt über eine Azure Data Factory-Pipeline) zum parallelen Verarbeiten von Daten in einem Computeknotenpool, einer verwalteten Auflistung von virtuellen Maschinen.

4.  Erstellen eines **Azure Batch-Pools** mit mindestens 2 Computeknoten.

	 Sie können den Quellcode für das [Azure Batch-Explorer-Tool](https://github.com/Azure/azure-batch-samples/tree/master/CSharp/BatchExplorer) herunterladen, kompilieren und zum Erstellen des Pools (**für diese Beispiellösung dringend empfohlen**) verwenden. Sie können den Pool auch mit der [Azure Batch Library für .NET](../batch/batch-dotnet-get-started.md) erstellen. Unter [Azure Batch-Explorer – Beispiel für eine exemplarische Vorgehensweise](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx) finden Sie Schritt-für-Schritt-Anweisungen zur Verwendung des Azure Batch-Explorers. Sie können auch das [New-AzureRmBatchPool](https://msdn.microsoft.com/library/mt628690.aspx)-Cmdlet verwenden, um einen Azure Batch-Pool zu erstellen.

	 Verwenden Sie Batch Explorer, um den Pool mit der folgenden Einstellung zu erstellen:

	-   Geben Sie eine ID für den Pool ein (**Pool-ID**). Notieren Sie die **ID des Pools**. Sie benötigen sie bei der Erstellung der Data Factory-Lösung.

	-   Geben Sie **Windows Server 2012 R2** für die Einstellung **Betriebssystem-Familie** ein.

	-   Geben Sie **2** als Wert für die Einstellung **Max. Aufgaben pro Computeknoten** ein.

	-   Geben Sie **2** als Wert für die Einstellung **Anzahl von Zielzuweisungen** ein.

	 ![](./media/data-factory-data-processing-using-batch/image2.png)

5.  [Azure Storage Explorer 6 (Tool)](https://azurestorageexplorer.codeplex.com/) oder [CloudXplorer](http://clumsyleaf.com/products/cloudxplorer) (von ClumsyLeaf Software). Hierbei handelt es sich um GUI-Tools zum Überprüfen und Ändern der Daten in Ihren Azure-Speicher-Projekten, einschließlich der Protokolle Ihrer in der Cloud gehosteten Anwendungen.

    1.  Erstellen Sie einen Container mit dem Namen **mycontainer** mit privatem Zugriff (kein anonymer Zugriff).

    2.  Wenn Sie **CloudXplorer** verwenden, erstellen Sie Ordner und Unterordner mit der folgenden Struktur:

 		![](./media/data-factory-data-processing-using-batch/image3.png)

		 **inputfolder** (Eingabeordner) und **outputfolder** (Ausgabeordner) sind Ordner in der obersten Ebene von **mycontainer**, und **inputfolder** enthält Unterordner mit Datums-/Zeitstempel (JJJJ-MM-TT-HH).

		 Wenn Sie **Azure Storage Explorer** verwenden, müssen Sie im nächsten Schritt Dateien mit Namen wie inputfolder/2015-11-16-00/file.txt, inputfolder/2015-11-16-01/file.txt usw hochladen. Dadurch werden die Ordner automatisch erstellt.

	3.  Erstellen Sie eine Textdatei **file.txt** auf dem Computer mit Inhalt, der das Schlüsselwort **Microsoft** enthält. Beispiel: „benutzerdefinierte Aktivität testen Microsoft benutzerdefinierte Aktivität testen Microsoft“.

	4.  Laden Sie die Datei in die folgenden Eingangsordner im Azure Blob-Speicher hoch.

		![](./media/data-factory-data-processing-using-batch/image4.png)

	 	Wenn Sie **Azure Storage Explorer** verwenden, laden Sie die Datei **file.txt** auf **mycontainer** hoch. Klicken Sie in der Symbolleiste auf **Kopieren**, um eine Kopie des Blobs zu erstellen. Ändern Sie im Dialogfeld **Kopieren von Blobs** den **Namen des Ziel-Blobs** in **inputfolder/2015-11-16-00/file.txt.** Wiederholen Sie diesen Schritt, um inputfolder/2015-11-16-01/file.txt, inputfolder/2015-11-16-02/file.txt, inputfolder/2015-11-16-03/file.txt, inputfolder/2015-11-16-04/file.txt usw. zu erstellen. Dadurch werden die Ordner automatisch erstellt.

	3.  Erstellen Sie einen anderen Container namens **customactivitycontainer**. Sie können die benutzerdefinierte Aktivität als Zip-Datei in diesen Container hochladen.

6.  **Microsoft Visual Studio 2012 oder höher** (zum Erstellen einer benutzerdefinierten Batchaktivität, die in der Data Factory-Lösung verwendet werden soll).

### Allgemeine Schritte zum Erstellen der Lösung

1.  Erstellen Sie eine benutzerdefinierte Aktivität zum Verwenden in der Data Factory-Lösung. Die benutzerdefinierte Aktivität enthält die Logik für die Datenverarbeitung.

    1.  Erstellen Sie in Visual Studio (oder einem Code-Editor Ihrer Wahl) ein .NET-Klassenbibliotheksprojekt, fügen Sie den Code zum Verarbeiten von Eingabedaten ein und kompilieren Sie das Projekt.

    2.  Zippen Sie alle Binärdateien und die (optionale) PDB-Datei im Ausgabeordner.

    3.  Laden Sie die Zip-Datei in den Azure Blobspeicher hoch.

	Eine ausführliche Anleitung finden Sie im Abschnitt [Erstellen der benutzerdefinierten Aktivität](#_Coding_the_custom).

2.  Erstellen Sie eine Azure Data Factory, die die benutzerdefinierte Aktivität verwendet:

    1.  Erstellen Sie eine Azure Data Factory.

    2.  Erstellen Sie verknüpfte Dienste.

        1.  StorageLinkedService: Stellt die Speicher-Anmeldeinformationen für den Zugriff auf Blobs bereit.

        2.  AzureBatchLinkedService: Gibt Azure Batch als Compute an.

    3.  Erstellen Sie Datasets.

        1.  InputDataset: Gibt die Speichercontainer und den Ordner für die Eingabe-Blobs an.

        2.  OutputDataset: Gibt die Speichercontainer und den Ordner für die Ausgabe-Blobs an.

    4.  Erstellen Sie eine Pipeline, die die benutzerdefinierte Aktivität verwendet.

    5.  Führen Sie die Pipeline aus und testen Sie sie.

    6.  Debuggen Sie die Pipeline.

 	Eine ausführliche Anleitung finden Sie im Abschnitt [Erstellen der Data Factory](#create-the-data-factory).

## Erstellen der benutzerdefinierten Aktivität

Die benutzerdefinierte Aktivität von Data Factory ist das Herzstück dieser Beispiellösung. Die Beispiellösung verwendet Azure Batch, um die benutzerdefinierte Aktivität auszuführen. Informationen zum Erstellen einer benutzerdefinierten Aktivität und zu deren Verwendung in einer Azure Data Factory-Pipeline finden Sie unter [Verwenden benutzerdefinierter Aktivitäten in einer Azure Data Factory-Pipeline](data-factory-use-custom-activities.md).

Um eine benutzerdefinierte .NET-Aktivität zu erstellen, die Sie in einer Azure Data Factory-Pipeline verwenden können, müssen Sie ein **.NET-Klassenbibliotheksprojekt** mit einer Klasse erstellen, die die **IDotNetActivity**-Schnittstelle implementiert. Diese Schnittstelle verfügt lediglich über eine Methode: **Ausführen**. Dies ist die Signatur der Methode:

	public IDictionary<string, string> Execute(
	            IEnumerable<LinkedService> linkedServices,
	            IEnumerable<Dataset> datasets,
	            Activity activity,
	            IActivityLogger logger)

Die Methode verfügt über einige wichtige Komponenten, die Sie kennen müssen.

-   Die Methode akzeptiert vier Parameter:

    1.  **LinkedServices**. Dies ist eine aufzählbare Liste von verknüpften Diensten, die Eingabe- und Ausgabedatenquellen (z. B.: Azure Blob Storage) mit der Data Factory verknüpfen. In diesem Beispiel gibt es nur einen verknüpften Dienst des Typs Azure Storage, der sowohl für die Eingabe als auch Ausgabe verwendet wird.

    2.  **Datasets**. Dies ist eine aufzählbare Liste von Datasets. Sie können diesen Parameter zum Abrufen der Speicherorte und Schemas verwenden, die von den Eingabe- und Ausgabedatasets definiert werden.

    3.  **Aktivität**. Dieser Parameter stellt die aktuelle Compute-Entität dar – in diesem Fall Azure Batch Service.

    4.  **Protokollierungstool** Mit dem Protokollierungstool können Sie Kommentare zum Debuggen schreiben, die dann als „Benutzer“-Protokoll für die Pipeline erscheinen.

-   Die Methode gibt ein Wörterbuch zurück, das zum Verketten benutzerdefinierter Aktivitäten verwendet werden kann. In diesem Beispiel werden wir diese Funktion nicht verwenden.

### Verfahren: Erstellen der benutzerdefinierten Aktivität

1.  Erstellen Sie ein .NET-Klassenbibliotheksprojekt in Visual Studio.

    1.  Starten Sie **Visual Studio 2012**/**2013/2015**.

    2.  Klicken Sie auf **Datei**, zeigen Sie auf **Neu**, und klicken Sie auf **Projekt**.

    3.  Erweitern Sie **Vorlagen**, und wählen Sie **Visual C#** aus. In dieser exemplarischen Vorgehensweise verwenden Sie C#, Sie können jedoch jede .NET-Sprache verwenden, um benutzerdefinierte Aktivität zu entwickeln.

    4.  Wählen Sie in der Liste mit den Projekttypen auf der rechten Seite den Eintrag **Klassenbibliothek** aus.

    5.  Geben Sie **MyDotNetActivity** als **Namen** ein.

    6.  Wählen Sie **C:\\ADF** als **Speicherort**. Erstellen Sie den Ordner **ADF**, wenn er noch nicht vorhanden ist.

    7.  Klicken Sie auf **OK**, um das Projekt zu erstellen.

2.  Klicken Sie auf **Tools**, zeigen Sie auf **NuGet-Paket-Manager**, und klicken Sie auf **Paket-Manager-Konsole**.

3.  Führen Sie in der **Paket-Manager-Konsole** den folgenden Befehl zum Importieren von **Microsoft.Azure.Management.DataFactories** aus.

			Install-Package Microsoft.Azure.Management.DataFactories

4.  Importieren Sie das **Azure Storage** NuGet-Paket in das Projekt. Sie benötigen dieses Paket, da Sie die Blob-Speicher-API verwenden werden.

		Install-Package Azure.Storage

5.  Fügen Sie die folgenden **using**-Anweisungen zur Quelldatei im Projekt hinzu.

		using System.IO;
		using System.Globalization;
		using System.Diagnostics;
		using System.Linq;

		using Microsoft.Azure.Management.DataFactories.Models;
		using Microsoft.Azure.Management.DataFactories.Runtime;

		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;

6.  Ändern Sie den Namen des **Namespace** in **MyDotNetActivityNS**.

		namespace MyDotNetActivityNS

7.  Ändern Sie den Namen der Klasse in **MyDotNetActivity**, und leiten Sie ihn von der **IDotNetActivity**-Schnittstelle ab, wie unten dargestellt.

		public class MyDotNetActivity : IDotNetActivity

8.  Implementieren bzw. fügen Sie die **Execute**-Methode der **IDotNetActivity**-Schnittstelle der **MyDotNetActivity**-Klasse hinzu, und kopieren Sie den folgenden Beispielcode in die Methode. Im Abschnitt [Execute-Methode](#execute-method) erfahren Sie mehr über die in dieser Methode verwendete Logik.

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


9.  Fügen Sie die folgenden Helfermethoden zur Klasse hinzu. Diese Methoden werden von der **Execute**-Methode aufgerufen. Vor allem isoliert die **Calculate**-Methode den Code, der jeden Blob durchläuft.

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


	Die **GetFolderPath**-Methode gibt den Pfad zum Ordner zurück, auf den das Dataset verweist, und die **GetFileName**-Methode gibt den Namen des Blobs oder der Datei zurück, auf das oder die das Dataset verweist.

	    "name": "InputDataset",
	    "properties": {
	        "type": "AzureBlob",
	        "linkedServiceName": "StorageLinkedService",
	        "typeProperties": {
	            "fileName": "file.txt",
	            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",


	Die **Calculate**-Methode berechnet die Anzahl der Instanzen des Schlüsselworts **Microsoft** in den Eingabedateien (Blobs im Ordner). Der Suchbegriff („Microsoft“) ist fest im Code programmiert.

10.  Kompilieren Sie das Projekt. Klicken Sie im Menü auf **Erstellen** und dann auf **Projektmappe erstellen**.

11.  Starten Sie **Windows-Explorer**, und navigieren Sie je nach Buildtyp zum Ordner **bin\\debug** oder **bin\\release**.

12.  Erstellen Sie die ZIP-Datei **MyDotNetActivity.zip**, die alle Binärdateien im Ordner **\\bin\\Debug** enthält. Sie möchten ggf. die Datei **MyDotNetActivity.pdb** einbeziehen, damit Sie zusätzliche Details wie z. B. die Zeilennummer im Quellcode erhalten, wenn dadurch ein Problem verursacht wurde.

	![](./media/data-factory-data-processing-using-batch/image5.png)

13.  Laden Sie **MyDotNetActivity.zip** als Blob in den Blobcontainer **customactivitycontainer** im Azure-Blobspeicher hoch, den der verknüpfte Dienst **StorageLinkedService** in der **ADFTutorialDataFactory** verwendet. Erstellen Sie den Blobcontainer **customactivitycontainer**, sofern er noch nicht vorhanden ist.

### Execute-Methode

Dieser Abschnitt enthält weitere Details und Hinweise zum Code in der Execute-Methode.

1.	Die Elemente zum Durchlaufen der Eingabesammlung finden Sie unter dem Namespace [Microsoft.WindowsAzure.Storage.Blob](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.storage.blob.aspx). Zum Durchlaufen der Blob-Sammlung müssen Sie die **BlobContinuationToken**-Klasse verwenden. Im Wesentlichen müssen Sie eine do-while-Schleife mit dem Token als Mechanismus zum Beenden der Schleife verwenden. Weitere Informationen finden Sie unter [Verwenden von Blob-Speicher aus .NET](../storage/storage-dotnet-how-to-use-blobs.md). Eine einfache Schleife ist hier dargestellt:

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

7.	Der Name der Datei wurde geschrieben und Sie können jetzt die Ausgabezeichenfolge aus der **Calculate**-Methode in ein neues Blob schreiben:

		// Create a new blob and upload the output text.
		CloudBlockBlob outputBlob = new CloudBlockBlob(outputBlobUri, outputStorageAccount.Credentials);
		logger.Write("Writing {0} to the output blob", output);
		outputBlob.UploadText(output);


## Erstellen der Data Factory

Sie haben im Abschnitt [Erstellen der benutzerdefinierten Aktivität](#create-the-custom-activity) eine benutzerdefinierte Aktivität erstellt und die Zip-Datei mit Binärdateien und der PDB-Datei in einen Azure-Blob-Container hochgeladen. In diesem Abschnitt erstellen Sie eine **Azure Data Factory** mit einer **Pipeline**, die die **benutzerdefinierte Aktivität** verwendet.

Das Eingabe-Dataset für die benutzerdefinierte Aktivität stellt die Blobs (Dateien) im Eingabeordner (Mycontainer\\inputfolder) im Blob-Speicher dar. Das Ausgabe-Dataset für die Aktivität stellt die Ausgabeblobs im Ausgabeordner (Mycontainer\\outputfolder) im Blob-Speicher dar.

Legen Sie eine oder mehrere Dateien in die Eingangsordner:

	mycontainer -> inputfolder
		2015-11-16-00
		2015-11-16-01
		2015-11-16-02
		2015-11-16-03
		2015-11-16-04

Legen Sie z. B. eine Datei (file.txt) mit dem folgenden Inhalt in die Ordner.

	test custom activity Microsoft test custom activity Microsoft

Der Eingabeordner entspricht einem Slice in Azure Data Factory, auch wenn der Ordner über zwei oder mehr Dateien verfügt. Wenn jeder Slice von der Pipeline verarbeitet wird, durchläuft die benutzerdefinierte Aktivität alle Blobs im Eingabeordner für diesen Slice.

Sie sehen fünf Ausgabedateien mit dem gleichen Inhalt. Die Ausgabedatei vom Verarbeiten der Datei im Ordner „2015-11-16-00“ wird z. B. den folgenden Inhalt aufweisen:

	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.

Wenn Sie mehrere Dateien (file.txt, file2.txt, file3.txt) mit dem gleichen Inhalt in den Eingabeordner ablegen, sehen Sie den folgenden Inhalt in der Ausgabedatei. Jeder Ordner (2015-11-16-00 usw.) entspricht einem Slice in diesem Beispiel, obwohl der Ordner mehrere Eingabedateien hat.

	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.
	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file2.txt.
	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file3.txt.


Beachten Sie, dass die Ausgabedatei jetzt drei Zeilen enthält, jeweils eine für jede Datei (Blob) in dem Ordner, der dem Slice (2015-11-16-00) zugeordnet ist.

Bei jeder Aktivitätsausführung wird eine Aufgabe erstellt. In diesem Beispiel ist nur eine Aktivität in der Pipeline vorhanden. Wenn ein Slice von der Pipeline verarbeitet wird, führt die benutzerdefinierte Aktivität Azure Batch aus, um den Slice zu verarbeiten. Da es sich um 5 Slices handelt (jedes Segment kann mehrere Blobs oder Dateien haben), werden 5 Aufgaben in Azure Batch erstellt. Wird eine Aufgabe mit Batch ausgeführt, so wird tatsächlich die benutzerdefinierte Aktivität ausgeführt.

Die folgende exemplarische Vorgehensweise enthält weitere Details.

### Schritt 1: Erstellen der Data Factory

1.  Nach der Anmeldung beim [Azure-Portal](https://portal.azure.com/) gehen Sie wie folgt vor:

    1.  Klicken Sie im linken Menü auf **NEU**.

    2.  Klicken Sie auf dem Blatt **Neu** auf **Daten und Analysen**.

    3.  Klicken Sie auf dem Blatt **Datenanalyse** auf **Data Factory**.

2.  Geben Sie auf dem Blatt **Neue Data Factory** als Namen die Zeichenfolge **CustomActivityFactory** ein. Der Name der Azure Data Factory muss global eindeutig sein. Bei Anzeige der Fehlermeldung **Data Factory-Name "CustomActivityFactory" ist nicht verfügbar** ändern Sie den Namen der Data Factory (z. B.in **IhrNameCustomActivityFactory**) und wiederholen den Vorgang.

3.  Klicken Sie auf **RESSOURCENGRUPPENNAME**, und wählen Sie eine vorhandene Ressourcengruppe aus oder erstellen Sie eine neue.

4.  Stellen Sie sicher, dass Sie das richtige Abonnement und die richtige Region zum Erstellen der Data Factory verwenden.

5.  Klicken Sie auf dem Blatt **Neue Data Factory** auf **Erstellen**.

6.  Sie sehen die erstellte Data Factory im **Dashboard** des Azure-Portals.

7.  Nachdem die Data Factory erfolgreich erstellt wurde, sehen Sie die Data Factory-Seite mit dem Inhalt der Data Factory.

 ![](./media/data-factory-data-processing-using-batch/image6.png)

### Schritt 2: Erstellen von verknüpften Diensten

Verknüpfte Dienste verknüpfen Datenspeicher oder Serverdienste mit einer Azure Data Factory. In diesem Schritt verknüpfen Sie Ihr **Azure Storage**-Konto und das **Azure Batch**-Konto mit Ihrer Data Factory.

#### Erstellen des mit Azure Storage verknüpften Diensts

1.  Klicken Sie auf die Kachel **Erstellen und Bereitstellen** auf dem Blatt **DATA FACTORY** für **CustomActivityFactory**. Der Data Factory-Editor wird gestartet.

2.  Klicken Sie in der Befehlsleiste auf **Neuer Datenspeicher** und wählen Sie **Azure Storage**. Das JSON-Skript zum Erstellen eines mit einem Azure-Speicher verknüpften Diensts sollte im Editor angezeigt werden.

    ![](./media/data-factory-data-processing-using-batch/image7.png)

3.  Ersetzen Sie **Kontoname** durch den Namen Ihres Azure-Speicherkontos und **Kontoschlüssel** durch den Zugriffsschlüssel des Azure-Speicherkontos. Informationen zum Abrufen Ihres Speicherzugriffsschlüssels finden Sie unter [Informationen zu Azure-Speicherkonten](../storage/storage-create-storage-account.md#view-copy-and-regenerate-storage-access-keys).

4.  Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.

    ![](./media/data-factory-data-processing-using-batch/image8.png)

#### Verwenden des mit Azure Batch verknüpften Dienstes

In diesem Schritt erstellen Sie einen verknüpften Dienst für Ihr **Azure Batch**-Konto, das zum Ausführen der benutzerdefinierten Data Factory-Aktivität verwendet wird.

1.  Klicken Sie in der Befehlsleiste auf **Neue Computerressource**, und wählen Sie **Azure Batch**. Das JSON-Skript zum Erstellen eines mit Azure Batch verknüpften Diensts wird im Editor angezeigt.

2.  In der JSON-Datei:

    1.  Ersetzen Sie **Kontoname** mit dem Namen Ihres Azure Batch-Kontos.

    2.  Ersetzen Sie **Zugriffsschlüssel** durch den Zugriffsschlüssel des Azure Batch-Kontos.

    3.  Geben Sie die ID des Pools für die **poolName**-Eigenschaft ein**. ** Für diese Eigenschaft können Sie entweder den Poolnamen oder die Pool-ID eingeben.

    4.  Geben Sie die Batch-URI für die JSON-Eigenschaft **batchUri** ein. Die **URL** auf dem Blatt **Azure-Batch-Konto** hat folgendes Format: <Kontoname>.<Region>.batch.azure.com. Für die **batchUri** -Eigenschaft in JSON müssen Sie aus der URL **„Kontoname“ entfernen**. Beispiel: Example: „batchUri“: „https://eastus.batch.azure.com“.

        ![](./media/data-factory-data-processing-using-batch/image9.png)

    5.  Geben Sie für die **linkedServiceName**-Eigenschaft das **StorageLinkedService**-Element ein. Sie haben diesen verknüpften Dienst im vorherigen Schritt erstellt. Dieser Speicher wird als Stagingbereich für Dateien und Protokolle verwendet.

3.  Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um den verknüpften Dienst bereitzustellen.

### Schritt 3: Erstellen von Datasets

In diesem Schritt erstellen Sie Datasets, um die Eingabe- und Ausgabedaten darstellen.

#### Erstellen eines Eingabedatasets

1.  Klicken Sie im Editor für die Data Factory auf der Symbolleiste auf **Neues Dataset** und anschließend im Dropdownmenü auf **Azure-Blobspeicher**.

2.  Ersetzen Sie den JSON-Code im rechten Bereich durch den folgenden JSON-Codeausschnitt:

		{
		    "name": "InputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "folderPath": "mycontainer/inputfolder/{Year}-{Month}-{Day}-{Hour}",
		            "format": {
		                "type": "TextFormat"
		            },
		            "partitionedBy": [
		                {
		                    "name": "Year",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "yyyy"
		                    }
		                },
		                {
		                    "name": "Month",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "MM"
		                    }
		                },
		                {
		                    "name": "Day",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "dd"
		                    }
		                },
		                {
		                    "name": "Hour",
		                    "value": {
		                        "type": "DateTime",
		                        "date": "SliceStart",
		                        "format": "HH"
		                    }
		                }
		            ]
		        },
		        "availability": {
		            "frequency": "Hour",
		            "interval": 1
		        },
		        "external": true,
		        "policy": {}
		    }
		}


	 Sie werden weiter unten in dieser exemplarischen Vorgehensweise eine Pipeline mit der Startzeit 2015-11-16T00:00:00Z und der Endzeit 2015-11-16T05:00:00Z erstellen. Sie ist so eingerichtet, dass Daten **stündlich** erstellt werden, sodass es fünf Eingabe- und Ausgabeslices gibt (zwischen **00**:00:00 und **05**:00:00).

	 Die **Häufigkeit** und das **Intervall** für das Eingabedataset ist auf **Hour** und **1** festgelegt, was bedeutet, dass der Eingabeslice stündlich verfügbar ist.

	 Dies sind die Startzeiten für jeden Slice, die durch die Systemvariable **SliceStart** im obigen JSON-Codeausschnitt dargestellt werden.

	| **Slice** | **Startzeit** |
	|-----------|-------------------------|
	| 1 | 2015-11-16T**00**:00:00 |
	| 2 | 2015-11-16T**01**:00:00 |
	| 3 | 2015-11-16T**02**:00:00 |
	| 4 | 2015-11-16T**03**:00:00 |
	| 5 | 2015-11-16T**04**:00:00 |

	 Der **FolderPath** wird mit dem Jahr, Monat, Tag und der Uhrzeit der Slice-Startzeit berechnet (**SliceStart**). Hier erfahren Sie, wie ein Eingabeordner einem Slice zugeordnet wird.

	| **Slice** | **Startzeit** | **Eingabeordner** |
	|-----------|-------------------------|-------------------|
	| 1 | 2015-11-16T**00**:00:00 | 2015-11-16-**00** |
	| 2 | 2015-11-16T**01**:00:00 | 2015-11-16-**01** |
	| 3 | 2015-11-16T**02**:00:00 | 2015-11-16-**02** |
	| 4 | 2015-11-16T**03**:00:00 | 2015-11-16-**03** |
	| 5 | 2015-11-16T**04**:00:00 | 2015-11-16-**04** |

3.  Klicken Sie in der Symbolleiste auf **Bereitstellen**, um die Tabelle **InputDataset** zu erstellen und bereitzustellen. Vergewissern Sie sich, dass die Meldung **TABELLE ERFOLGREICH ERSTELLT** auf der Titelleiste des Editors angezeigt wird.

#### Erstellen des Ausgabedatasets

In diesem Schritt erstellen Sie ein weiteres Dataset des Typs „Azureblob“, um die Ausgabedaten darzustellen.

1.  Klicken Sie im Editor für die Data Factory auf der Symbolleiste auf **Neues Dataset** und anschließend im Dropdownmenü auf **Azure-Blobspeicher**.

2.  Ersetzen Sie den JSON-Code im rechten Bereich durch den folgenden JSON-Codeausschnitt:

		{
		    "name": "OutputDataset",
		    "properties": {
		        "type": "AzureBlob",
		        "linkedServiceName": "StorageLinkedService",
		        "typeProperties": {
		            "fileName": "{slice}.txt",
		            "folderPath": "mycontainer/outputfolder",
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


 	Eine Ausgabedatei und ein Ausgabe-Blob wird für jeden Eingabeslice generiert. Im Folgenden sehen Sie, wie eine Ausgabedatei für jeden Slice benannt wird. Alle Ausgabedateien werden in einem Ausgabeordner generiert: **mycontainer\\outputfolder**.


	| **Slice** | **Startzeit** | **Ausgabedatei** |
	|-----------|-------------------------|-----------------------|
	| 1 | 2015-11-16T**00**:00:00 | 2015-11-16-**00.txt** |
	| 2 | 2015-11-16T**01**:00:00 | 2015-11-16-**01.txt** |
	| 3 | 2015-11-16T**02**:00:00 | 2015-11-16-**02.txt** |
	| 4 | 2015-11-16T**03**:00:00 | 2015-11-16-**03.txt** |
	| 5 | 2015-11-16T**04**:00:00 | 2015-11-16-**04.txt** |

	 Denken Sie daran, dass alle Dateien in einem Eingabeordner (z. B.: 2015-11-16-00) zu einem Slice mit der Startzeit 2015-11-16-00 gehören. Wenn dieser Slice verarbeitet wird, durchsucht die benutzerdefinierte Aktivität jede Datei und erzeugt eine Zeile in der Ausgabedatei mit der Anzahl der Vorkommnisse des Suchbegriffs („Microsoft“). Wenn drei Dateien im Ordner 2015-11-16-00 vorhanden sind, werden drei Zeilen in der Ausgabedatei erstellt: 2015-11-16-00.txt.

3.  Klicken Sie in der Symbolleiste auf **Bereitstellen**, um **OutputDataset** zu erstellen und bereitzustellen.

### Schritt 4: Erstellen und Ausführen der Pipeline mit der benutzerdefinierten Aktivität

In diesem Schritt erstellen Sie eine Pipeline mit einer Aktivität, der zuvor erstellten benutzerdefinierten Aktivität.

> [AZURE.IMPORTANT] Wenn Sie **file.txt** noch nicht in die Eingabeordner im Blob-Container hochgeladen haben, erledigen Sie dies vor dem Erstellen der Pipelines. Die **IsPaused**-Eigenschaft ist in der JSON-Pipeline auf „False“ festgelegt, sodass die Pipeline sofort ausgeführt wird, da das **Startdatum** Datum in der Vergangenheit liegt.

1.  Klicken Sie im Data Factory-Editor auf der Befehlsleiste auf die Schaltfläche **Neue Pipeline**. Wenn der Befehl nicht angezeigt wird, klicken Sie auf **... (Auslassungspunkte)**, um ihn anzuzeigen.

2.  Ersetzen Sie das JSON-Skript im rechten Bereich durch das folgende JSON-Skript.

		{
			"name": "PipelineCustom",
			"properties": {
				"description": "Use custom activity",
				"activities": [
					{
						"type": "DotNetActivity",
						"typeProperties": {
							"assemblyName": "MyDotNetActivity.dll",
							"entryPoint": "MyDotNetActivityNS.MyDotNetActivity",
							"packageLinkedService": "StorageLinkedService",
							"packageFile": "customactivitycontainer/MyDotNetActivity.zip"
						},
						"inputs": [
							{
								"name": "InputDataset"
							}
						],
						"outputs": [
							{
								"name": "OutputDataset"
							}
						],
						"policy": {
							"timeout": "00:30:00",
							"concurrency": 5,
							"retry": 3
						},
						"scheduler": {
							"frequency": "Hour",
							"interval": 1
						},
						"name": "MyDotNetActivity",
						"linkedServiceName": "AzureBatchLinkedService"
					}
				],
				"start": "2015-11-16T00:00:00Z",
				"end": "2015-11-16T05:00:00Z",
				"isPaused": false
		   }
		}

	Beachten Sie Folgendes:

	-   In der Pipeline gibt es nur eine Aktivität vom Typ **DotNetActivity**.

	-   **AssemblyName** wird auf den Namen der DLL festgelegt: **MyActivities.dll**.

	-   **EntryPoint** wird auf **MyDotNetActivityNS.MyDotNetActivity** festgelegt. Das entspricht im Grunde <Namespace>.<Klassenname> in Ihrem Code.

	-   **PackageLinkedService** ist auf **StorageLinkedService** festgelegt, das auf den Blobspeicher verweist, der die Zip-Datei mit der benutzerdefinierten Aktivität enthält. Wenn Sie andere Azure-Speicherkonten für die Eingabe- und Ausgabedateien und die Zip-Datei mit der benutzerdefinierten Aktivität verwenden, müssen Sie einen weiteren verknüpften Azure Storage-Dienst erstellen. Dieser Artikel setzt voraus, dass Sie das gleiche Azure-Speicherkonto verwenden.

	-   **PackageFile** wird auf **customactivitycontainer/MyCustomActivity.zip** festgelegt. Das entspricht dem Format: <containerforthezip>/<nameofthezip.zip>.

	-   Die benutzerdefinierte Aktivität verwendet **InputDataset** als Eingabe und **OutputDataset** als Ausgabe.

	-   Die Eigenschaft **linkedServiceName** der benutzerdefinierten Aktivität zeigt auf **AzureBatchLinkedService**, das Azure Data Factory mitteilt, dass die benutzerdefinierte Aktivität in einem Azure HDInsight-Cluster ausgeführt werden muss.

	-   Die Einstellung **Parallelität** ist wichtig. Wenn Sie den Standardwert 1 verwenden, auch wenn Sie über 2 oder mehr Computeknoten im Azure Batch-Pool verfügen, werden die Slices nacheinander verarbeitet. Daher haben Sie nicht die Möglichkeit, die parallele Verarbeitung von Azure Batch nutzen. Wenn Sie **Parallelität** auf einen höheren Wert wie z. B. 2 festlegen, können 2 Segmente (was 2 Aufgaben in Azure Batch entspricht) gleichzeitig verarbeitet werden. In diesem Fall werden die virtuellen Maschinen im Azure Batch-Pool verwendet. Legen Sie daher Sie die Parallelitätseigenschaft entsprechend fest.

	-   Nur eine Aufgabe (Slice) wird standardmäßig auf einer virtuellen Maschine zu einem beliebigen Zeitpunkt ausgeführt. Dies liegt daran, dass standardmäßig die **maximalen Aufgaben pro virtueller Maschine** für einen Azure Batch-Pool auf 1 festgelegt ist. Im Rahmen der Voraussetzungen haben Sie einen Pool mit dieser Eigenschaft auf 2 festgelegt, damit die beiden Data Factory-Slices auf einem virtuellen Computer zur gleichen Zeit ausgeführt werden können.


	-   Die Eigenschaft **isPaused** ist standardmäßig auf „false“ festgelegt. Die Pipeline wird in diesem Beispiel sofort ausgeführt, da die Slices in der Vergangenheit starten. Legen Sie diese Eigenschaft auf „true“ fest, um die Pipeline anzuhalten, und legen Sie sie zum Neustart wieder auf „false“ fest.

	-   Die **Start-** und **Endzeiten** werden 5 Stunden auseinander festgelegt und Slices werden stündlich erstellt, sodass insgesamt 5 Slices von der Pipeline erstellt werden.

3.  Klicken Sie auf der Befehlsleiste auf **Bereitstellen**, um die Pipeline bereitzustellen.

### Schritt 5: Testen der Pipeline

In diesem Schritt testen Sie die Pipeline durch Ablegen von Dateien in die Eingangsordner. Beginnen wir mit dem Testen der Pipeline mit einer Datei pro Eingabeordner.

1.  Klicken Sie im Azure-Portal auf dem Blatt „Data Factory“ auf **Diagramm**.

    ![](./media/data-factory-data-processing-using-batch/image10.png)

2.  Doppelklicken Sie in der Diagrammansicht auf das Eingabedataset **inputDataset**.

    ![](./media/data-factory-data-processing-using-batch/image11.png)

3.  Daraufhin sollte das Blatt **InputDataset** mit allen 5 Slices bereit ein. Beachten Sie, dass die **SLICE STARTZEIT** und **SLICE-ENDZEIT** für jeden Slice.

    ![](./media/data-factory-data-processing-using-batch/image12.png)

4.  Klicken Sie in der **Diagrammansicht** jetzt auf **OutputDataset**.

5.  Sie sollten sehen können, dass die fünf Ausgabeslices im Zustand „Bereit“ sind, wenn sie bereits erzeugt wurden.

    ![](./media/data-factory-data-processing-using-batch/image13.png)

6.  Verwenden Sie [Azure Batch-Explorer](http://blogs.technet.com/b/windowshpc/archive/2015/01/20/azure-batch-explorer-sample-walkthrough.aspx), um die **Aufgaben** anzuzeigen, die den **Slices** zugeordnet sind, und herauszufinden, auf welcher virtuellen Maschine jedes Slice ausgeführt wurde. Ein Auftrag mit dem Namen **adf-<poolname>** wird erstellt. Dieser Auftrag wird eine Aufgabe für jeden Slice besitzen. Dieses Beispiel umfasst 5 Slices, also 5 Aufgaben in Azure Batch. Wenn für die **Parallelität** in der Pipeline-JSON in Azure Data Factory **5** festgelegt wird und als **maximale Anzahl an Aufgaben pro virtueller Maschine** auf **2** in einem Azure Batch-Pool mit **2** VMs festgelegt ist, werden die Aufgaben sehr schnell ausgeführt (siehe **erstellte** Zeit).

    ![](./media/data-factory-data-processing-using-batch/image14.png)

7.  Die Ausgabedateien sollten im **outputfolder** von **mycontainer** in Ihrem Azure Blob-Speicher angezeigt werden.

    ![](./media/data-factory-data-processing-using-batch/image15.png)

    Fünf Ausgabedateien (eine für jeden Eingabeslice) sollten angezeigt werden. Jede Ausgabedatei sollte ähnlichen Inhalt wie folgt aufweisen:

    	2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-00/file.txt.

    Das folgende Diagramm veranschaulicht, wie die Data Factory-Slices den Aufgaben in Azure Batch zugeordnet werden. In diesem Beispiel wird ein Slice nur einmal ausgeführt.

    ![](./media/data-factory-data-processing-using-batch/image16.png)

8.  Testen wir nun mehrere Dateien in einem Ordner. Erstellen Sie Dateien: **file2.txt**, **file3.txt**, **file4.txt**, und **file5.txt** mit dem gleichen Inhalt wie file.txt im Ordner: **2015-11-06-01**.

9.  **Löschen** Sie im Ausgabeordner die Ausgabedatei **2015-11-16-01.txt**.

10. Klicken Sie auf dem Blatt **OutputDataset** Blatt mit der rechten Maustaste auf den Slice, für den die **SLICE STARTZEIT** auf **11/16/2015 01:00:00 Uhr** festgelegt ist, und klicken Sie auf **Ausführen**, um den Slice erneut auszuführen/zu verarbeiten. Der Slice verfügt jetzt über 5 Dateien statt 1 Datei.

    ![](./media/data-factory-data-processing-using-batch/image17.png)

11. Wenn der Slice ausgeführt wird und der Status **bereit** lautet, überprüfen Sie den Inhalt der Ausgabedatei für diesen Slice (**2015-11-16-01.txt**) im **outputfolder** von **mycontainer** in Ihrem Blob-Speicher. Für jede Datei des Slice sollte eine Zeile vorhanden sein.

		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file.txt.
		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file2.txt.
		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file3.txt.
		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file4.txt.
		2 occurrences(s) of the search term "Microsoft" were found in the file inputfolder/2015-11-16-01/file5.txt.


    **Hinweis:** Wenn Sie die Ausgabedatei 2015-11-16-01.txt nicht gelöscht haben, bevor Sie es mit 5 Eingabedateien versuchen, sehen Sie eine Zeile aus der vorherigen Sliceausführung und fünf Zeilen aus der aktuellen Sliceausführung. Der Inhalt wird standardmäßig an die Ausgabedatei angefügt, wenn sie bereits vorhanden ist.

### Debuggen der Pipeline

Das Debuggen umfasst einige grundlegende Verfahren:

1.  Wenn der Eingabeslice nicht auf **Bereit** festgelegt ist, stellen Sie sicher, dass die Struktur des Eingabeordners korrekt ist und die Datei file.txt im Eingabeordner vorhanden ist.

    ![](./media/data-factory-data-processing-using-batch/image3.png)

2.  Verwenden Sie in der **Execute**-Methode der benutzerdefinierten Aktivität das **IActivityLogger**-Objekt, um Informationen zu protokollieren, die Ihnen beim Beheben von Problemen helfen. Die protokollierten Nachrichten werden in der Datei user\_0.log angezeigt.

    Klicken Sie auf dem Blatt **OutputDataset** auf den Slice, um das Blatt **DATENSLICE** für diesen Slice anzuzeigen. Die **Aktivitätsausführungen** für diesen Slice werden angezeigt. Sie sollten genau eine Aktivitätsausführung für den Slice sehen. Wenn Sie in der Befehlsleiste auf **Ausführen** klicken, können Sie für den gleichen Slice eine weitere Aktivität ausführen.

    Wenn Sie auf die Aktivitätsausführung klicken, sehen Sie das Blatt **AKTIVITÄTSAUSFÜHRUNG – DETAILS** mit einer Liste der Protokolldateien. Protokollierte Nachrichten werden in der Datei **user\_0.log** angezeigt. Tritt ein Fehler auf, sehen Sie drei Aktivitätsausführungen, da die Anzahl der Wiederholungsversuche in der Pipeline oder Aktivitäts-JSON auf 3 festgelegt ist. Wenn Sie die Aktivitätsausführung starten, sehen Sie die Protokolldateien, die Sie zum Beheben von Fehlern überprüfen können.

    ![](./media/data-factory-data-processing-using-batch/image18.png)

    Klicken Sie in der Liste der Protokolldateien auf die Datei **user-0.log**. Im rechten Bereich werden die Ergebnisse der Verwendung der **IActivityLogger.Write**-Methode angezeigt.

    ![](./media/data-factory-data-processing-using-batch/image19.png)

    Sie sollten außerdem die Datei „system-0.log“ auf alle Systemfehlermeldungen und -Ausnahmen überprüfen.

		Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Loading assembly file MyDotNetActivity...

		Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Creating an instance of MyDotNetActivityNS.MyDotNetActivity from assembly file MyDotNetActivity...

		Trace\_T\_D\_12/6/2015 1:43:35 AM\_T\_D\_\_T\_D\_Verbose\_T\_D\_0\_T\_D\_Executing Module

		Trace\_T\_D\_12/6/2015 1:43:38 AM\_T\_D\_\_T\_D\_Information\_T\_D\_0\_T\_D\_Activity e3817da0-d843-4c5c-85c6-40ba7424dce2 finished successfully

3.  Fügen Sie die **PDB**-Datei in der Zipdatei hinzu, sodass die Fehlerdetails über Informationen wie z. B. die **Aufrufliste** verfügen, wenn ein Fehler auftritt.

4.  Alle Dateien in der Zip-Datei für die benutzerdefinierte Aktivität müssen auf der **obersten Ebene** liegen und dürfen keine Unterordner haben.

    ![](./media/data-factory-data-processing-using-batch/image20.png)

5.  Stellen Sie sicher, dass die richtigen Werte für **AssemblyName** (MyDotNetActivity.dll), **EntryPoint**(MyDotNetActivityNS.MyDotNetActivity), **PackageFile** (customactivitycontainer/Mycustomactivity.ZIP) und **PackageLinkedService** (sollte auf den Azure Blob-Speicher verweisen, der die Zip-Datei enthält) festgelegt sind.

6.  Wenn Sie einen Fehler behoben haben und den Slice erneut verarbeiten wollen, klicken Sie auf dem Blatt **OutputDataset** mit der rechten Maustaste auf den Slice und klicken Sie anschließend auf **Ausführen**.

    ![](./media/data-factory-data-processing-using-batch/image21.png)

    **Hinweis:** sehen Sie einen **Container** im Azure Blob-Speicher **adfjobs**. Dieser Container wird nicht automatisch gelöscht, jedoch können Sie ihn nach dem Testen der Lösung problemlos löschen. Ebenso erstellt die Data Factory-Lösung einen Azure Batch-**Auftrag** mit dem Namen: **adf-< Pool-ID/Name>:job-0000000001**. Sie können diesen Auftrag bei Belieben löschen, nachdem Sie die Lösung getestet haben.

### Erweitern des Beispiels

Sie können dieses Beispiel erweitern, um mehr über Azure Data Factory und Azure Batch-Funktionen zu erfahren. Gehen Sie zum Verarbeiten von Slices in einem anderen Zeitbereich wie folgt vor:

1.  Fügen Sie dem **inputfolder** die folgenden Unterordner hinzu: 2015-11-16-05 2015-11-16-06 201-11-16-07, 2011-11-16-08, 2015-11-16-09 und legen Sie die Eingabedateien in diesen Ordnern ab. Ändern Sie die Endzeit für die Pipeline von 2015-11-16T05:00:00Z auf 2015-11-16T10:00:00Z. Doppelklicken Sie in der **Diagrammansicht** auf das **InputDataset**, und vergewissern Sie sich, dass die Eingabeslices bereit sind. Doppelklicken Sie auf **OuptutDataset**, um den Status der Ausgabeslices anzeigen. Wenn sie bereit sind, überprüfen Sie den Ausgangsordner für die Ausgabedateien.

2.  Erhöhen oder verringern Sie die **Parallelität**-Einstellung, um zu verstehen, wie sie sich auf die Leistung Ihrer Lösung, insbesondere auf die Verarbeitung auf Azure Batch auswirkt. (Siehe Schritt 4: Erstellen und führen Sie die Pipeline aus, um mehr über die **Parallelität**-Einstellung zu erfahren.)

3.  Erstellen Sie einen Pool mit einer höheren/niedrigeren **maximalen Anzahl an Aufgaben pro virtueller Maschine**. Aktualisieren Sie den mit Azure Batch verknüpften Dienst in der Data Factory-Lösung, um den neu erstellten Anwendungspool zu verwenden. (Siehe Schritt 4: Erstellen und führen Sie die Pipeline aus, um mehr über die Einstellung zur **maximalen Anzahl an Aufgaben pro virtueller Maschine** zu erfahren.)

4.  Erstellen Sie einen Azure Batch-Pool mit **automatischer Skalierung**. Das automatische Skalieren von Computeknoten in einem Azure Batch-Pool ist die dynamische Anpassung der Verarbeitungsleistung, die von der Anwendung beansprucht wird. Siehe [Automatisches Skalieren von Computeknoten in einem Azure Batch-Pool](../batch/batch-automatic-scaling.md).

    In der Beispiellösung der **Execute**-Methode ruft die **Calculate** -Methode auf, die einen Eingabedatenslice verarbeiten, um einen Ausgabedatenslice zu erzeugen. Sie können eine eigene Methode erstellen, um Eingabedaten zu verarbeiten und den Calculate-Methodenaufruf in der Execute-Methode durch einen Aufruf Ihrer Methode zu ersetzen.

## Nächste Schritte: Nutzung der Daten

Nachdem Sie Daten verarbeitet haben, können Sie sie mit Online-Tools wie **Microsoft Power BI** nutzen. Hier erfahren Sie mehr über Power BI und die Verwendung in Azure:

-   [Untersuchen eines Datasets in Power BI](https://support.powerbi.com/knowledgebase/articles/475159)

-   [Erste Schritte mit Power BI Desktop.](https://support.powerbi.com/knowledgebase/articles/471664)

-   [Aktualisieren von Daten in Power BI](https://support.powerbi.com/knowledgebase/articles/474669)

-   [Azure und Power BI – grundlegende Übersicht](https://support.powerbi.com/knowledgebase/articles/568614)

## Referenzen

-   [Azure Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

    -   [Einführung in den Azure Data Factory-Dienst](data-factory-introduction.md)

    -   [Erste Schritte mit Azure Data Factory](data-factory-build-your-first-pipeline.md)

    -   [Verwenden von benutzerdefinierten Aktivitäten in einer Azure Data Factory-Pipeline](data-factory-use-custom-activities.md)

-   [Azure Batch](https://azure.microsoft.com/documentation/services/batch/)

    -   [Grundlagen von Azure Batch](../batch/batch-technical-overview.md)

    -   [Übersicht über Azure Batch-Features](../batch/batch-api-basics.md)

    -   [Erstellen und Verwalten eines Azure Batch-Kontos im Azure-Portal](../batch/batch-account-create-portal.md)

    -   [Erste Schritte mit der Azure Batch-Bibliothek für .NET](../batch/batch-dotnet-get-started.md)

<!---HONumber=AcomDC_0128_2016-->