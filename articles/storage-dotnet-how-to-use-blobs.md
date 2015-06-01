<properties 
	pageTitle="Verwenden des Blob-Speichers mit .NET | Microsoft Azure" 
	description="Erfahren Sie, wie Sie Microsoft Azure Blob-Speicher zum Hochladen, Herunterladen, Auflisten und Löschen von Blob-Inhalten verwenden. Die Beispiele sind in C# geschrieben." 
	services="storage" 
	documentationCenter=".net" 
	authors="tamram" 
	manager="adinah" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/12/2015" 
	ms.author="tamram"/>


# Verwenden des Blob-Speichers mit .NET

[AZURE.INCLUDE [storage-selector-blob-include](../includes/storage-selector-blob-include.md)]

## Übersicht

In diesem Leitfaden wird die Durchführung häufiger Szenarien mit dem
Azure Blob-Speicherdienst demonstriert. Die Beispiele sind in C# geschrieben und greifen auf die Azure-Speicherclientbibliothek für .NET zurück. Die aufgeführten Szenarien umfassen das
**Hochladen**, **Auflisten**, **Herunterladen** und **Löschen** von Blobs.

> [AZURE.NOTE] Diese Anleitung gilt für die Azure .NET Storage Client Library 2.x und höher. Die empfohlene Version für die Storage-Clientbibliothek ist 4.x. Diese Version ist entweder über [NuGet](https://www.nuget.org/packages/WindowsAzure.Storage/) oder als Teil des [Azure-SDK für .NET](/downloads/) erhältlich. Unter [Programmgesteuerter Zugriff auf Blob-Speicher](#programmatically-access-blob-storage) weiter unten finden Sie weitere Informationen zum Download der Storage-Clientbibliothek.

[AZURE.INCLUDE [storage-blob-concepts-include](../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../includes/storage-create-account-include.md)]

[AZURE.INCLUDE [storage-configure-connection-string-include](../includes/storage-configure-connection-string-include.md)]

## Programmgesteuerter Zugriff auf den Blob-Speicher

### Abrufen der Assembly
Wir empfehlen die Verwendung von NuGet zum Abrufen der `Microsoft.WindowsAzure.Storage.dll`-Assembly. Klicken Sie im **Projektmappen-Explorer** mit der rechten Maustaste auf Ihr Projekt, und wählen Sie **NuGet-Pakete verwalten** aus.  Suchen Sie online nach "WindowsAzure.Storage", und klicken Sie dann auf **Installieren**, um das Azure Storage-Paket und die zugehörigen Abhängigkeiten zu installieren.

"Microsoft.WindowsAzure.Storage.dll" ist ebenfalls im Azure-SDK für .NET enthalten, das Sie im <a href="http://azure.microsoft.com/develop/net/#">.NET Developer Center</a> herunterladen können. Die Assembly wird im Verzeichnis `%Program Files%\Microsoft SDKs\Azure.NET SDK<sdk-version>\ref` installiert.

### Namespace-Deklarationen
Fügen Sie zu Beginn aller C#-Dateien, in denen Sie programmgesteuert auf Azure Storage zugreifen möchten, die folgenden Namespacedeklarationen hinzu:

    using Microsoft.WindowsAzure.Storage;
    using Microsoft.WindowsAzure.Storage.Auth;
	using Microsoft.WindowsAzure.Storage.Blob;

Stellen Sie sicher, dass auf die  `Microsoft.WindowsAzure.Storage.dll`-Assembly verwiesen wird.

### Abrufen der Verbindungszeichenfolge
Sie können Ihre Speicherkontoinformationen mit dem Typ **CloudStorageAccount** darstellen. Falls Sie eine 
Azure-Projektvorlage verwenden und/oder über einen Verweis auf 
Microsoft.WindowsAzure.CloudConfigurationManager verfügen, können Sie den Typ **ConfigurationManager** verwenden, um Ihre Verbindungszeichenfolge für den Speicher und die Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen:

    CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
        CloudConfigurationManager.GetSetting("StorageConnectionString"));

Wenn Sie eine Anwendung ohne einen Verweis auf Microsoft.WindowsAzure.CloudConfigurationManager erstellen und die Verbindungszeichenfolge sich, wie oben gezeigt, in `web.config` oder `app.config` befindet, können Sie die Verbindungszeichenfolge mit **ConfigurationManager** abrufen.  Sie müssen einen Verweis auf "System.Configuration.dll" zu Ihrem Projekt und eine andere Namespace-Deklaration dafür hinzufügen:

	using System.Configuration;
	...
	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		ConfigurationManager.ConnectionStrings["StorageConnectionString"].ConnectionString);

Mit dem Typ **CloudBlobClient** können Sie Objekte abrufen, die Container und Blobs darstellen, die im Blobspeicherdienst gespeichert sind. Mit dem folgenden Code wird unter Rückgriff auf das oben abgerufene Speicherkontoobjekt ein **CloudBlobClient**-Objekt erstellt:

    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

### ODataLib-Abhängigkeiten
ODataLib-Abhängigkeiten in der Speicherclientbibliothek für .NET werden durch die ODataLib-Pakete (Version 5.0.2), die über NuGet verfügbar sind, und nicht durch WCF Data Services aufgelöst.  Die ODataLib-Bibliotheken können direkt heruntergeladen werden, oder es wird über Ihr Codeprojekt durch NuGet darauf verwiesen.  Die spezifischen ODataLib-Pakete sind [OData], [Edm] und [Spatial].

## Erstellen eines Containers

Jeder Blob im Azure-Speicher muss sich in einem Container befinden. Dieses Beispiel zeigt, wie Sie einen Container erstellen, falls er nicht bereits vorhanden ist.

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

Azure Blob-Speicher unterstützt Blockblobs und Seitenblobs.  In den meisten Fällen wird die Verwendung von Blockblobs empfohlen.

Rufen Sie einen Containerverweis ab und verwenden Sie diesen zum Abrufen eines Blockblobverweises, um eine Datei in einen Blockblob hochzuladen. Sobald Sie über einen Blob-Verweis verfügen, können Sie jeden Datenstrom in diesen hochladen, indem Sie die Methode **UploadFromStream** aufrufen. Bei diesem Vorgang wird das Blob erstellt, falls es nicht bereits vorhanden ist, oder überschrieben, falls es vorhanden ist. Im folgenden Beispiel wird gezeigt, wie ein Blob in einen bereits erstellten Container hochgeladen wird.

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

Um die Blobs in einem Container aufzuführen, müssen Sie zuerst einen Containerverweis abrufen. Danach können Sie mit der **ListBlobs**-Methode des Containers die Blobs und/oder darin befindlichen Verzeichnisse abrufen. Damit Sie auf die zahlreichen Eigenschaften und Methoden für ein zurückgegebenes **IListBlobItem**-Objekt zugreifen können, müssen Sie es in ein **CloudBlockBlob**-, 
**CloudPageBlob**- oder **CloudBlobDirectory**-Objekt umwandeln.  Wenn der Typ unbekannt ist, können Sie eine Typenüberprüfung durchführen, um zu bestimmen, in welchen Typ die Umwandlung erfolgen soll.  Der folgende Code zeigt, wie Sie die URIs der einzelnen Elemente im `photos`-Container abrufen und ausgeben:

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

Wie oben gezeigt, wird beim Blobdienst ebenfalls das Konzept von Verzeichnissen in Containern angewendet. Auf diese Weise können Sie die Blobs in einer ordnerähnlicheren Struktur organisieren. Betrachten Sie z. B. den folgenden Satz von Blockblobs in einem Container mit dem Namen `photos`:

	photo1.jpg
	2010/architecture/description.txt
	2010/architecture/photo3.jpg
	2010/architecture/photo4.jpg
	2011/architecture/photo5.jpg
	2011/architecture/photo6.jpg
	2011/architecture/description.txt
	2011/photo7.jpg

Wenn Sie **ListBlobs** für den Container 'photos' aufrufen (wie im obigen Beispiel), dann enthält die zurückgegebene Sammlung **CloudBlobDirectory**- und **CloudBlockBlob**-Objekte, die die Verzeichnisse und Blobs auf der obersten Ebene darstellen. Die Ausgabe würde folgendermaßen aussehen:

	Directory: https://<accountname>.blob.core.windows.net/photos/2010/
	Directory: https://<accountname>.blob.core.windows.net/photos/2011/
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg


Optional können Sie den Parameter **UseFlatBlobListing** der Methode **ListBlobs** auf 
**true** setzen.. Daraufhin würde jedes Blob unabhängig vom jeweiligen Verzeichnis als **CloudBlockBlob**
zurückgegeben.  Der Aufruf für **ListBlobs** sähe folgendermaßen aus::

    // Loop over items within the container and output the length and URI.
	foreach (IListBlobItem item in container.ListBlobs(null, true))
	{
	   ...
	}
 Das Ergebnis sähe folgendermaßen aus:

	Block blob of length 4:: https://<accountname>.blob.core.windows.net/photos/2010/architecture/description.txt
	Block blob of length 314618: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo3.jpg
	Block blob of length 522713: https://<accountname>.blob.core.windows.net/photos/2010/architecture/photo4.jpg
	Block blob of length 4: https://<accountname>.blob.core.windows.net/photos/2011/architecture/description.txt
	Block blob of length 419048: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo5.jpg
	Block blob of length 506388: https://<accountname>.blob.core.windows.net/photos/2011/architecture/photo6.jpg
	Block blob of length 399751: https://<accountname>.blob.core.windows.net/photos/2011/photo7.jpg
	Block blob of length 505623: https://<accountname>.blob.core.windows.net/photos/photo1.jpg

Weitere Informationen finden Sie unter [CloudBlobContainer.ListBlobs][].

## Herunterladen von Blobs

Um Blobs herunterzuladen, rufen Sie zuerst einen Blobverweis ab, und rufen Sie dann die **DownloadToStream**-Methode auf. Im folgenden
Beispiel wird die **DownloadToStream**-Methode zum Übertragen der Blob-Inhalte
auf ein Datenstromobjekt verwendet, das danach in einer lokalen Datei gespeichert werden kann.

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

Zum Löschen eines Blobs rufen Sie einen Blobverweis ab und rufen dann die
**Delete**-Methode auf.

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

Dieses Beispiel verwendet eine einfache Blob-Auflistung, aber Sie können auch eine hierarchische Auflistung verwenden, indem Sie den Parameter  `useFlatBlobListing` der Methode **ListBlobsSegmentedAsync** auf  `false` festlegen.

Da die Beispielmethode eine asynchrone Methode aufruft, muss sie mit dem Schlüsselwort  `async` eingeleitet werden und ein **Task**-Objekt zurückgeben. Das Schlüsselwort "await" für die Methode **ListBlobsSegmentedAsync** hält die Ausführung der Beispielmethode an, bis die Auflistung abgeschlossen ist.

    async public static Task ListBlobsSegmentedInFlatListing()
    {
        // Retrieve storage account from connection string.
        CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
            CloudConfigurationManager.GetSetting("StorageConnectionString"));

        // Create the blob client.
        CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Retrieve reference to a previously created container.
        CloudBlobContainer container = blobClient.GetContainerReference("myblobs");

        //List blobs in pages.
        Console.WriteLine("List blobs in pages:");

        //List blobs with a paging size of 10, for the purposes of the example. 
		//The first call does not include the continuation token.
        BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(
                "", true, BlobListingDetails.All, 10, null, null, null);

        //Enumerate the result segment returned.
        int i = 0;
        if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
        foreach (var blobItem in resultSegment.Results)
        {
            Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
        }
        Console.WriteLine();

        //Get the continuation token, if there are additional pages of results.
        BlobContinuationToken continuationToken = resultSegment.ContinuationToken;

        //Check whether there are more results and list them in pages of 10 while a continuation token is returned.
        while (continuationToken != null)
        {
            //This overload allows control of the page size. 
			//You can return all remaining results by passing null for the maxResults parameter, 
            //or by calling a different overload.
            resultSegment = await container.ListBlobsSegmentedAsync(
					"", true, BlobListingDetails.All, 10, continuationToken, null, null);
            if (resultSegment.Results.Count<IListBlobItem>() > 0) { Console.WriteLine("Page {0}:", ++i); }
            foreach (var blobItem in resultSegment.Results)
            {
                Console.WriteLine("\t{0}", blobItem.StorageUri.PrimaryUri);
            }
            Console.WriteLine();

            //Get the next continuation token.
            continuationToken = resultSegment.ContinuationToken;
        }
    }

## Nächste Schritte

Nachdem Sie sich nun mit den Grundlagen des Blob-Speichers vertraut gemacht haben, folgen Sie diesen Links, um zu erfahren, wie komplexere Speicheraufgaben ausgeführt werden.
<ul>
<li>Vollständige Informationen zu verfügbaren APIs finden Sie in der Blobdienst-Referenzdokumentation:
  <ul>
    <li><a href="http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409">Referenz zur Storage-Clientbibliothek für .NET</a>
    </li>
    <li><a href="http://msdn.microsoft.com/library/azure/dd179355">REST-API-Referenz</a></li>
  </ul>
</li>
<li>Weitere Informationen zu fortgeschrittenen Aufgaben mit Azure Storage finden Sie unter <a href="http://msdn.microsoft.com/library/azure/gg433040.aspx">Speichern von und Zugreifen auf Daten in Azure</a>.</li>
<li>Erfahren Sie, wie Sie mithilfe des <a href="../websites-dotnet-webjobs-sdk/">Azure WebJobs SDK den geschriebenen Code so vereinfachen, dass er mit Azure-Speicher funktioniert.</li>
<li>Weitere Informationen zu zusätzlichen Optionen für das Speichern von Daten in Azure finden Sie in den anderen Featureleitfäden.
  <ul>
    <li>Verwenden Sie den <a href="/documentation/articles/storage-dotnet-how-to-use-tables/">Tabellenspeicher</a> zum Speichern strukturierter Daten.</li>
    <li>Verwenden Sie den <a href="/documentation/articles/storage-dotnet-how-to-use-queues/">Warteschlangenspeicher</a> zum Speichern unstrukturierter Daten.</li>
    <li>Verwenden Sie eine <a href="/documentation/articles/sql-database-dotnet-how-to-use/">SQL-Datenbank</a> zum Speichern relationaler Daten.</li>
  </ul>
</li>
</ul>

  [Blob5]: ./media/storage-dotnet-how-to-use-blobs/blob5.png
  [Blob6]: ./media/storage-dotnet-how-to-use-blobs/blob6.png
  [Blob7]: ./media/storage-dotnet-how-to-use-blobs/blob7.png
  [Blob8]: ./media/storage-dotnet-how-to-use-blobs/blob8.png
  [Blob9]: ./media/storage-dotnet-how-to-use-blobs/blob9.png
  
  [Speichern von und Zugreifen auf Daten in Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
  [Azure Storage-Teamblog]: http://blogs.msdn.com/b/windowsazurestorage/
  [Konfigurieren von Verbindungszeichenfolgen]: http://msdn.microsoft.com/library/azure/ee758697.aspx
  [Referenz zur .NET-Clientbibliothek]: http://go.microsoft.com/fwlink/?LinkID=390731&clcid=0x409
  [REST-API-Referenz]: http://msdn.microsoft.com/library/azure/dd179355
  [OData]: http://nuget.org/packages/Microsoft.Data.OData/5.0.2
  [Edm]: http://nuget.org/packages/Microsoft.Data.Edm/5.0.2
  [Spatial]: http://nuget.org/packages/System.Spatial/5.0.2

<!--HONumber=49--> 

<!--HONumber=49-->