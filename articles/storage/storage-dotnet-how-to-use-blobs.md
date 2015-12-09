<properties
	pageTitle="Verwenden des Blob-Speichers mit .NET | Microsoft Azure"
	description="Erfahren Sie mehr über den Azure Blob-Speicher, und wie Sie einen Container erstellen und Blob-Inhalt hochladen, herunterladen, auflisten und löschen können."
	services="storage"
	documentationCenter=".net"
	authors="tamram"
	manager="carmonm"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="hero-article" 
	ms.date="12/01/2015"
	ms.author="tamram"/>


# Verwenden des Blob-Speichers mit .NET

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Übersicht

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem Windows Azure Blob-Speicherdienst demonstriert. Die Beispiele sind in C# geschrieben und greifen auf die Azure-Speicherclientbibliothek für .NET zurück. Die Speicherclientbibliothek ist ein SDK, das die Interaktion mit Blobspeicher-REST-APIs vereinfacht. Die in dieser Anleitung behandelten Szenarien umfassen **Hochladen**, **Auflisten**, **Herunterladen** und **Löschen** von Blobs und nehmen etwa eine Stunde in Anspruch. Wenn Sie ein Erste-Schritte-Video sehen möchten, finden Sie dies unter [Einführung in Azure Storage in fünf Minuten](https://azure.microsoft.com/documentation/videos/azure-storage-5-minute-overview/), oder lesen Sie [Erste Schritte mit Azure Storage – Einstieg in fünf Minuten](storage-getting-started-guide.md).

[AZURE.INCLUDE [storage-dotnet-client-library-version-include](../../includes/storage-dotnet-client-library-version-include.md)]

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../../includes/storage-configure-connection-string-include.md)]

## Programmgesteuerter Zugriff auf den Blob-Speicher

[AZURE.INCLUDE [storage-dotnet-obtain-assembly](../../includes/storage-dotnet-obtain-assembly.md)]

### Namespace-Deklarationen
Fügen Sie zu Beginn aller C#-Dateien, in denen Sie programmgesteuert auf Azure-Speicher zugreifen möchten, die folgenden Namespace-Deklarationen hinzu:

    using Microsoft.WindowsAzure;
    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
    using Microsoft.WindowsAzure.Storage.Blob;

Stellen Sie sicher, dass auf die `Microsoft.WindowsAzure.Storage.dll`-Assembly verwiesen wird.

[AZURE.INCLUDE [storage-dotnet-retrieve-conn-string](../../includes/storage-dotnet-retrieve-conn-string.md)]

Der Typ **CloudBlobClient** ermöglicht den Abruf von Objekten, die im Blob-Speicherdienst gespeicherte Container und Blobs darstellen. Mit dem folgenden Code wird unter Rückgriff auf das oben abgerufene Speicherkontoobjekt ein **CloudBlobClient**-Objekt erstellt:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

## Erstellen eines Containers

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Dieses Beispiel zeigt, wie Sie einen Container erstellen, falls er nicht bereits vorhanden ist.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Create the container if it doesn't already exist.
    container.CreateIfNotExists();

Standardmäßig ist der neue Container privat, und Sie müssen Ihren Speicherzugriffsschlüssel angeben, um Blobs aus diesem Container herunterzuladen. Wenn die Dateien im Container für alle verfügbar sein sollen, können Sie den Container mithilfe des folgenden Codes öffentlich machen:

    container.SetPermissions(
        new BlobContainerPermissions { PublicAccess =
 	    BlobContainerPublicAccessType.Blob });

Jede Person im Internet kann Blobs in einem öffentlichen Container anzeigen, Sie können sie jedoch nur bearbeiten oder löschen, wenn Sie über den entsprechenden Zugriffsschlüssel verfügen.

## Hochladen eines Blobs in einen Container

Azure Blob-Speicher unterstützt Blockblobs und Seitenblobs. In den meisten Fällen wird die Verwendung von Blockblobs empfohlen.

Rufen Sie einen Containerverweis ab und verwenden Sie diesen zum Abrufen eines Blockblobverweises, um eine Datei in einen Blockblob hochzuladen. Sobald Sie über einen Blobverweis verfügen, können Sie jeden Datenstrom in diesen hochladen, indem Sie die **UploadFromStream**-Methode aufrufen. Bei diesem Vorgang wird das Blob erstellt, falls es nicht bereits vorhanden ist, oder überschrieben, falls es vorhanden ist.

Im folgenden Beispiel wird gezeigt, wie ein Blob in einen bereits erstellten Container hochgeladen wird.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with contents from a local file.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        blockBlob.UploadFromStream(fileStream);
    }

## Auflisten der Blobs in einem Container

Um die Blobs in einem Container aufzuführen, müssen Sie zuerst einen Containerverweis abrufen. Danach können Sie mit der **ListBlobs**-Methode des Containers die Blobs und/oder darin befindlichen Verzeichnisse abrufen. Damit Sie auf die zahlreichen Eigenschaften und Methoden für ein zurückgegebenes **IListBlobItem**-Objekt zugreifen können, müssen Sie es in ein **CloudBlockBlob**-, **CloudPageBlob**- oder **CloudBlobDirectory**-Objekt umwandeln. Wenn der Typ unbekannt ist, können Sie eine Typenüberprüfung durchführen, um zu bestimmen, in welchen Typ die Umwandlung erfolgen soll. Der folgende Code zeigt, wie Sie die URI der einzelnen Elemente im `photos`-Container abrufen und ausgeben:

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Retrieve reference to a previously created container.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

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

Wie oben gezeigt, können Sie bei der Benennung von Blobs Pfadinformationen im Namen verwenden. Dadurch entsteht eine virtuelle Verzeichnisstruktur, die Sie wie ein herkömmliches Dateisystem organisieren und durchlaufen können. Beachten Sie, dass nur die Verzeichnisstruktur virtuell ist – die einzigen Ressourcen, die im Blob-Speicher verfügbar sind, sind Container und Blobs. Die Storage-Clientbibliothek bietet jedoch ein **CloudBlobDirectory**-Objekt, das auf ein virtuelles Verzeichnis verweist und das Arbeiten mit Blobs vereinfacht, die auf diese Weise organisiert sind.

Betrachten Sie z. B. den folgenden Satz von Blockblobs in einem Container mit dem Namen `photos`:

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Wenn Sie **ListBlobs** für den Container "photos" aufrufen (wie im obigen Beispiel), wird eine hierarchische Auflistung zurückgegeben. Sie enthält **CloudBlobDirectory**- und **CloudBlockBlob**-Objekte, die jeweils die Verzeichnisse und Blobs im Container darstellen. Die Ausgabe sieht folgendermaßen aus:

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Optional können Sie für den **UseFlatBlobListing**-Parameter der **ListBlobs**-Methode die Option **true** festlegen. In diesem Fall wird jedes Blob im Container als **CloudBlockBlob**-Objekt zurückgegeben. Der Aufruf von **ListBlobs** für die Rückgabe einer flachen Auflistung würde folgendermaßen aussehen:

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}

Die Ergebnisse würden wie folgt aussehen:

	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


## Herunterladen von Blobs

Um Blobs herunterzuladen, rufen Sie zuerst einen Blobverweis ab, und rufen Sie dann die **DownloadToStream**-Methode auf. Im folgenden Beispiel wird die **DownloadToStream**-Methode verwendet, um den Blobinhalt auf ein Datenstromobjekt zu übertragen, das danach in einer lokalen Datei gespeichert werden kann.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "photo1.jpg".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

    // Save blob contents to a file.
    using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
    {
        blockBlob.DownloadToStream(fileStream);
    }

Sie können auch die **DownloadToStream**-Methode verwenden, um den Inhalt eines Blobs als Textzeichenfolge herunterzuladen.

	// Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// Retrieve reference to a blob named "myblob.txt"
	CloudBlockBlob blockBlob2 = container.GetBlockBlobReference("myblob.txt");

	string text;
	using (var memoryStream = new MemoryStream())
	{
		blockBlob2.DownloadToStream(memoryStream);
		text = System.Text.Encoding.UTF8.GetString(memoryStream.ToArray());
	}

## Löschen von Blobs

Um ein Blob zu löschen, rufen Sie zuerst einen Blobverweis ab, und rufen Sie dann die **Delete**-Methode dafür auf.

    // Retrieve storage account from connection string.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

    // Create the blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Retrieve reference to a previously created container.
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // Retrieve reference to a blob named "myblob.txt".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

    // Delete the blob.
    blockBlob.Delete();


## Asynchrones Auflisten von Blobs auf Seiten

Wenn Sie eine große Anzahl von Blobs auflisten oder die Anzahl der Ergebnisse steuern möchten, die in einem Auflistungsvorgang zurückgegeben werden, können Sie Blobs auf Ergebnisseiten auflisten. In diesem Beispiel wird gezeigt, wie Sie Ergebnisse auf Seiten asynchron zurückgeben, sodass die Ausführung nicht durch einen großen Ergebnissatz blockiert wird.

Dieses Beispiel verwendet eine einfache Blob-Auflistung, aber Sie können auch eine hierarchische Auflistung verwenden, indem Sie den Parameter `useFlatBlobListing` der Methode **ListBlobsSegmentedAsync** auf `false` festlegen.

Da die Beispielmethode eine asynchrone Methode aufruft, muss sie mit dem Schlüsselwort `async` eingeleitet werden und ein **Task**-Objekt zurückgeben. Das Schlüsselwort "await" für die Methode **ListBlobsSegmentedAsync** hält die Ausführung der Beispielmethode an, bis die Auflistung abgeschlossen ist.

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

## Beschreiben eines Anfügeblobs

Ein Anfügeblob ist eine neue Art von Blob, die mit Version 5.x der Azure-Speicher-Clientbibliothek für .NET eingeführt wird. Anfügeblobs sind für Anfügevorgäng wie die Protokollierung optimiert. Ein Anfügeblob besteht wie ein Blockblob aus Blöcken. Allerdings ist es bei einem Anfügeblob so, dass ein neuer Block immer ans Ende des Blobs angefügt wird. Das Aktualisieren oder Löschen eines vorhandenen Blocks ist in einem Anfügeblob nicht möglich. Anders als bei Blockblobs sind die Block-IDs sind für Anfügeblobs nicht verfügbar.
 
In einem Anfügeblob kann jeder Block unterschiedlich groß sein, bis maximal 4 MB. Insgesamt können bis zu 50.000 Blöcke enthalten sein. Die maximale Größe eines Anfügeblobs ist deshalb etwas mehr als 195 GB (4 MB X 50.000 Blöcke).

Das folgende Beispiel erstellt ein neues Anfügeblob und fügt einige Daten hinzu, um eine einfache Protokollierung zu simulieren.

    //Parse the connection string for the storage account.
    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        Microsoft.Azure.CloudConfigurationManager.GetSetting("StorageConnectionString"));

    //Create service client for credentialed access to the Blob service.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    //Get a reference to a container.
    CloudBlobContainer container = blobClient.GetContainerReference("my-append-blobs");

    //Create the container if it does not already exist. 
    container.CreateIfNotExists();

    //Get a reference to an append blob.
    CloudAppendBlob appendBlob = container.GetAppendBlobReference("append-blob.log");

    //Create the append blob. Note that if the blob already exists, the CreateOrReplace() method will overwrite it.
    //You can check whether the blob exists to avoid overwriting it by using CloudAppendBlob.Exists().
    appendBlob.CreateOrReplace();

    int numBlocks = 10;

    //Generate an array of random bytes.
    Random rnd = new Random();
    byte[] bytes = new byte[numBlocks];
    rnd.NextBytes(bytes);
        
    //Simulate a logging operation by writing text data and byte data to the end of the append blob.
    for (int i = 0; i < numBlocks; i++)
    {
        appendBlob.AppendText(String.Format("Timestamp: {0:u} \tLog Entry: {1}{2}",
            DateTime.UtcNow, bytes[i], Environment.NewLine));
    }

    //Read the append blob to the console window.
    Console.WriteLine(appendBlob.DownloadText());

Weitere Informationen zu den Unterschieden zwischen den drei Arten von Blobs finden Sie unter [Grundlegendes zu Blockblobs, Seitenblobs und Anfügeblobs](https://msdn.microsoft.com/library/azure/ee691964.aspx).

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen von Blobspeichern vertraut gemacht haben, lesen Sie die folgenden Artikel, um mehr zu erfahren.

### Blobspeicher-Referenzdokumentation

- [Referenz zur Storage-Clientbibliothek für .NET](http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409)
- [REST-API-Referenz](http://msdn.microsoft.com/library/azure/dd179355)

### Zusätzliche Featurehandbücher

- [Erste Schritte mit Tabellenspeicher für .NET](storage-dotnet-how-to-use-tables.md)
- [Erste Schritte mit Warteschlangenspeicher für .NET](storage-dotnet-how-to-use-queues.md)
- [Erste Schritte mit Dateispeicher für .NET](storage-dotnet-how-to-use-files.md)
- [Verwenden einer SQL-Datenbank zum Speichern relationaler Daten](../sql-database/articles/sql-database-dotnet-how-to-use.md)
- [Verwenden von Azure-Blobspeicher mit dem WebJobs-SDK](../app-service-web/websites-dotnet-webjobs-sdk-storage-blobs-how-to.md)

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png

  [Azure Storage Team Blog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Configuring Connection Strings]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [.NET client library reference]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST API reference]: http://msdn.microsoft.com/library/azure/dd179355
 

<!---HONumber=AcomDC_1203_2015-->