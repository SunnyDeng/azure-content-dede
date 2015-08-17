<properties
	pageTitle="Erste Schritte mit Azure-Blob-Speicher und verbundenen Visual Studio-Diensten"
	description="Erste Schritte mit Azure-Blob-Speicher in einem ASP.NET-Projekt in Visual Studio"
	services="storage"
	documentationCenter=""
	authors="patshea123"
	manager="douge"
	editor="tglee"/>

<tags
	ms.service="storage"
	ms.workload="web"
	ms.tgt_pltfrm="vs-getting-started"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/04/2015"
	ms.author="patshea123"/>

# Erste Schritte mit Azure-Blob-Speicher und verbundenen Visual Studio-Diensten

> [AZURE.SELECTOR]
> - [Getting started](vs-storage-aspnet-getting-started-blobs.md)
> - [What happened](vs-storage-aspnet-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet-getting-started-queues.md)
> - [Tables](vs-storage-aspnet-getting-started-tables.md)

## Übersicht

Dieser Artikel beschreibt, wie die ersten Schritte bei der Verwendung des Azure-Blob-Speichers aussehen, nachdem Sie über das Visual Studio-Dialogfeld **Verbundene Dienste hinzufügen** in einer ASP.NET-App ein Azure-Speicherkonto erstellt oder auf ein solches Konto verwiesen haben. Im Artikel wird erläutert, wie Sie Blob-Container erstellen und andere allgemeine Aufgaben ausführen. Dazu zählen u. a. das Hochladen, Auflisten, Herunterladen und Löschen von Blobs. Die Beispiele sind in C# geschrieben und greifen auf die [Azure-Speicherclientbibliothek für .NET](https://msdn.microsoft.com/library/azure/dn261237.aspx) zurück.

 - Weitere allgemeine Informationen zur Verwendung von Azure-Blob-Speicher finden Sie unter [Verwenden des Blobspeichers mit .NET](storage-dotnet-how-to-use-blobs.md). 
 - Weitere Informationen zu ASP.NET-Projekten finden Sie unter [ASP.NET](http://www.asp.net).


Die BLOB-Speicherung in Azure ist ein Dienst zur Speicherung großer Mengen unstrukturierter Daten, auf die von überall auf der Welt über HTTP oder HTTPS zugegriffen werden kann. Ein einzelner BLOB kann eine beliebige Größe aufweisen. BLOBs können Bilder, Audio- und Videodateien, Rohdaten und Dokumentdateien enthalten.

Dateien werden in Ordnern gespeichert, Speicher-BLOBs analog dazu in Containern. Nachdem Sie ein Speicherkonto erstellt haben, erstellen Sie mindestens einen Container in dem Speicher. Beispielsweise können Sie in einem Speicher namens „Notizbuch“ BLOB-Container im Speicher namens „Bilder“ erstellen, um Bilder zu speichern, und einen anderen namens „Audio“ erstellen, um Audiodateien zu speichern. Nachdem Sie die Container erstellt haben, können Sie einzelne BLOB-Dateien in diese hochladen.




##Zugreifen auf BLOB-Container in Code

Für den programmgesteuerten Zugriff auf Blobs in ASP.NET-Projekten müssen Sie die folgenden Elemente hinzuzufügen, wenn sie nicht bereits vorhanden sind.

1. Fügen Sie die folgenden Codenamespace-Deklarationen am Anfang aller C#-Dateien hinzu, mit denen Sie programmgesteuert auf Azure Storage zugreifen möchten.

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Auth;
		using Microsoft.WindowsAzure.Storage.Blob;


2. Rufen Sie ein `CloudStorageAccount`-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um Ihre Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen.

		CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    > [AZURE.NOTE]Verwenden Sie den gesamten vorherigen Code vor dem Code in den folgenden Abschnitten.

3. Rufen Sie ein `CloudBlobClient`-Objekt ab, um auf einen vorhandenen Container in Ihrem Speicherkonto zu verweisen.

		// Create a blob client.
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

> [AZURE.NOTE]Einige der APIs, die Aufrufe des Azure-Speichers in ASP.NET 5 ausführen, sind asynchron. Unter [Asynchrone Programmierung mit Async und Await](http://msdn.microsoft.com/library/hh191443.aspx) finden Sie weitere Informationen.


## Erstellen eines BLOB-Containers in Code

Sie können auch das `CloudBlobClient`-Objekt zum Erstellen eines Containers in Ihrem Speicherkonto verwenden. Sie müssen dem obigen Code lediglich einen Aufruf von `CreateIfNotExistsAsync` hinzufügen. Dies wird im folgenden Beispiel veranschaulicht.

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();

## Hochladen eines Blobs in einen Container

Azure-Blob-Speicher unterstützt Blockblobs und Seitenblobs. In den meisten Fällen wird die Verwendung von Blockblobs empfohlen.

Rufen Sie einen Containerverweis ab und verwenden Sie diesen zum Abrufen eines Blockblobverweises, um eine Datei in einen Blockblob hochzuladen. Sobald Sie über einen Blobverweis verfügen, können Sie jeden Datenstrom in diesen hochladen, indem Sie die `UploadFromStream`-Methode aufrufen. Bei diesem Vorgang wird das Blob erstellt, falls es nicht bereits vorhanden ist, oder überschrieben, falls es vorhanden ist. Im folgenden Beispiel wird gezeigt, wie ein Blob in einen bereits erstellten Container hochgeladen wird.

    // Get a CloudBlobContainer named 'container' as described in "Access blob containers in code."

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## Auflisten der Blobs in einem Container

Wenn Sie die Blobs in einem Container auflisten möchten, rufen Sie die Blobs und/oder die darin enthaltenen Verzeichnisse mit der `ListBlobs`-Methode ab. Damit Sie auf die zahlreichen Eigenschaften und Methoden für ein zurückgegebenes `IListBlobItem`-Objekt zugreifen können, müssen Sie es in ein `CloudBlockBlob`-,`CloudPageBlob`- oder `CloudBlobDirectory`-Objekt umwandeln. Wenn der Typ unbekannt ist, können Sie eine Typenüberprüfung durchführen, um zu bestimmen, in welchen Typ die Umwandlung erfolgen soll. Der folgende Code zeigt, wie Sie den URI der einzelnen Elemente im `photos`-Container abrufen und ausgeben:

    // Get a CloudBlobContainer named 'container' as described in "Access blob containers in code."

	// Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, false))
	{
		if (item.GetType() == typeof(CloudBlockBlob))
		{
			CloudBlockBlob blob = (CloudBlockBlob)item;

			Console.WriteLine("Block blob of length {0}: {1}", blob.Properties.Length, blob.Uri);

		}
		else if (item.GetType() == typeof(CloudPageBlob))
		{
			CloudPageBlob pageBlob = (CloudPageBlob)item;

			Console.WriteLine("Page blob of length {0}: {1}", pageBlob.Properties.Length, pageBlob.Uri);

		}
		else if (item.GetType() == typeof(CloudBlobDirectory))
		{
			CloudBlobDirectory directory = (CloudBlobDirectory)item;

			Console.WriteLine("Directory: {0}", directory.Uri);
		}
	}

Wie im vorherigen Beispiel gezeigt, wird beim Blob-Dienst ebenfalls das Konzept von Verzeichnissen in Containern angewendet. Auf diese Weise können Sie die Blobs in einer ordnerähnlicheren Struktur organisieren. Betrachten Sie beispielsweise den folgenden Satz von Blockblobs in einem Container mit dem Namen `photos`.

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Wenn Sie `ListBlobs` für den Container „photos“ aufrufen (wie im vorherigen Beispiel gezeigt), enthält die zurückgegebene Sammlung `CloudBlobDirectory`- und `CloudBlockBlob`-Objekte, die die Verzeichnisse und Blobs auf der obersten Ebene darstellen. Das folgende Beispiel zeigt die resultierende Ausgabe.

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Optional können Sie den Parameter `UseFlatBlobListing` der `ListBlobs`-Methode auf `true` festlegen. Daraufhin würde jeder Blob unabhängig vom jeweiligen Verzeichnis als `CloudBlockBlob` zurückgegeben. Im folgenden Beispiel sehen Sie den Aufruf von `ListBlobs`.

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

Und das nächste Beispiel enthält die Ergebnisse.

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg



## Herunterladen von Blobs

Laden Sie Blobs mit der `DownloadToStream`-Methode herunter. Im folgenden Beispiel wird die `DownloadToStream`-Methode verwendet, um den Blobinhalt auf ein Datenstromobjekt zu übertragen, das danach in einer lokalen Datei gespeichert werden kann.

    // Get a CloudBlobContainer named 'container' as described in "Access blob containers in code"

    // Retrieve a reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Sie können auch die `DownloadToStream`-Methode verwenden, um den Inhalt eines Blobs als Textzeichenfolge herunterzuladen.

    // Get a CloudBlobContainer named 'container' as described in "Access blob containers in code"

	// Retrieve a reference to a blob named "myblob.txt"
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

## Löschen von Blobs

Verwenden Sie zum Löschen eines Blobs die `Delete`-Methode.

    // Get a CloudBlobContainer named 'container' as described in "Access blob containers in code"

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## Asynchrones Auflisten von Blobs auf Seiten

Wenn Sie eine große Anzahl von Blobs auflisten oder die Anzahl der Ergebnisse steuern möchten, die in einem Auflistungsvorgang zurückgegeben werden, können Sie Blobs auf Ergebnisseiten auflisten. Im folgenden Beispiel wird gezeigt, wie Sie Ergebnisse auf Seiten asynchron zurückgeben, sodass die Ausführung nicht durch einen großen Ergebnissatz blockiert wird.

Dieses Beispiel verwendet eine einfache Blobauflistung, aber Sie können auch eine hierarchische Auflistung verwenden, indem Sie den Parameter `useFlatBlobListing` der Methode `ListBlobsSegmentedAsync` auf `false` festlegen.

Da die Beispielmethode eine asynchrone Methode aufruft, muss sie mit dem Schlüsselwort `async` eingeleitet werden und ein `Task`-Objekt zurückgeben. Das Schlüsselwort „await“ für die Methode `ListBlobsSegmentedAsync` hält die Ausführung der Beispielmethode an, bis die Auflistung abgeschlossen ist.

    async public static Task ListBlobsSegmentedInFlatListing(CloudBlobContainer container)
    {
        //List blobs to the console window, with paging.
        Console.WriteLine("List blobs in pages:");

        int i = 0;
        BlobContinuationToken continuationToken = null;
        BlobResultSegment resultSegment = null;

        //Call ListBlobsSegmentedAsync and enumerate the result segment returned, while the continuation token is non-null.
        //When the continuation token is null, the last page has been returned and execution can exit the loop.
        do
        {
            //This overload allows control of the page size. You can return all remaining results by passing null for the maxResults parameter,
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync("", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
        while (continuationToken != null);
    }

## Nächste Schritte

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

<!---HONumber=August15_HO6-->