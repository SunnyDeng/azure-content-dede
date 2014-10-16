###### Erstellen eines Containers

Dateien werden in Ordnern gespeichert, Speicher-BLOBs analog dazu in Containern.

- Mithilfe des **CloudBlobClient**-Objekts können Sie einen Verweis auf den Container abrufen, den Sie verwenden möchten.

- Sie können die Methode **CreateCloudBlobClient()** aufrufen, um bei Bedarf einen Container zu erstellen.


Der folgende Code zeigt, wie ein BLOB-Speichercontainer erstellt wird. Der Code erstellt ein **BlobClient**-Objekt, damit Sie auf die Funktionen des Objekts zugreifen können, z. B. zum Erstellen eines Speichercontainers. Der Code versucht dann, auf einen Speichercontainer namens "mycontainer" zu verweisen. Wird kein Container mit diesem Namen gefunden, wird der Container erstellt.

	// Create a blob client.
	CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

	// Get a reference to a container named “mycontainer.”
	CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

	// If “mycontainer” doesn’t exist, create it.
	container.CreateIfNotExists();

**HINWEIS:** Verwenden Sie diesen Codeblock vor dem Code in den folgenden Abschnitten.

###### Hochladen eines Blobs in einen Container
Rufen Sie einen Containerverweis ab, und verwenden Sie diesen dann zum Abrufen eines BLOB-Verweises, um eine BLOB-Datei in einen Container hochzuladen. Sobald Sie über einen BLOB-Verweis verfügen, können Sie jeden Datenstrom in diesen hochladen, indem Sie die Methode UploadFromStream aufrufen. Dieser Vorgang erstellt den BLOB, wenn dieser noch nicht vorhanden ist, oder überschreibt ihn, wenn er vorhanden ist. Im folgenden Beispiel wird gezeigt, wie ein Blob in einen bereits erstellten Container hochgeladen wird.

	// Create or overwrite the "myblob" blob with the contents of a local file
	// named “myfile”.
	using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
	{
    	blockBlob.UploadFromStream(fileStream);
	}

###### Auflisten der Blobs in einem Container
Um die Blobs in einem Container aufzuführen, müssen Sie zuerst einen Containerverweis abrufen. Anschließend können Sie die Methode **ListBlobs()** des Containers aufrufen, um die darin enthaltenen BLOBs und/oder Verzeichnisse abzurufen. Damit Sie auf die zahlreichen Eigenschaften und Methoden für ein zurückgegebenes **IListBlobItem**-Objekt zugreifen können, müssen Sie es in ein **CloudBlockBlob**-, **CloudPageBlob**- oder **CloudBlobDirectory**-Objekt umwandeln. Wenn der BLOB-Typ unbekannt ist, können Sie eine Typüberprüfung ausführen, um zu bestimmen, in welchen Typ die Umwandlung erfolgen soll. Der folgende Code zeigt, wie der URI der einzelnen Elemente in einem Container namens "photos" abgerufen und ausgegeben wird.

	// Get a reference to a previously created container.
	CloudBlobContainer container = blobClient.GetContainerReference("photos");

	// Loop through items in the container and output the length and URI for each 
	// item.
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

Es gibt auch andere Möglichkeiten, den Inhalt eines BLOB-Containers aufzulisten. Weitere Informationen finden Sie unter [Verwenden von BLOB-Speicher aus .NET][Verwenden von BLOB-Speicher aus .NET].

###### Herunterladen eines Blobs
Wenn Sie einen BLOB herunterladen möchten, rufen Sie zuerst einen Verweis auf den BLOB ab, und rufen Sie dann die Methode **DownloadToStream()** auf. Im folgenden Beispiel wird die Methode **DownloadToStream()** verwendet, um den Inhalt des BLOBs in ein Datenstromobjekt zu übertragen, das anschließend als eine lokale Datei gespeichert werden kann.

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named “myfile”.
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	blockBlob.DownloadToStream(fileStream);
	}

Es gibt auch andere Möglichkeiten zum Speichern von BLOBs als Dateien. Weitere Informationen finden Sie unter [Verwenden von BLOB-Speicher aus .NET][1].

###### Löschen eines Blobs
Wenn Sie einen BLOB löschen möchten, rufen Sie zuerst einen Verweis auf den BLOB ab, und rufen Sie dann die Methode **Delete()** für den Verweis auf.

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	blockBlob.Delete();

[Verwenden von BLOB-Speicher aus .NET]: http://azure.microsoft.com/de-de/documentation/articles/storage-dotnet-how-to-use-blobs/#list-blob
[1]: http://azure.microsoft.com/de-de/documentation/articles/storage-dotnet-how-to-use-blobs/#download-blobs
