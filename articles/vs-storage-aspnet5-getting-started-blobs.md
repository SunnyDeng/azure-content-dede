<properties 
	pageTitle="Erste Schritte mit Azure Storage" 
	description="" 
	services="storage" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="web" 
	ms.tgt_pltfrm="vs-getting-started" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/10/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Erste Schritte](vs-storage-aspnet5-getting-started-blobs.md)
> - [Was ist passiert?](vs-storage-aspnet5-what-happened.md)

## Erste Schritte mit Azure Storage (ASP.NET 5-Projekte)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Warteschlangen](vs-storage-aspnet5-getting-started-queues.md)
> - [Tabellen](vs-storage-aspnet5-getting-started-tables.md)

Die BLOB-Speicherung in Azure ist ein Dienst zur Speicherung großer Mengen unstrukturierter Daten, auf die von überall auf der Welt über HTTP oder HTTPS zugegriffen werden kann. Ein einzelner BLOB kann eine beliebige Größe aufweisen. BLOBs können Bilder, Audio- und Videodateien, Rohdaten und Dokumentdateien enthalten.

Im ersten Schritt müssen Sie ein Azure-Speicherkonto und dann mindestens einen Container im Speicher erstellen. Sie können z. B. einen Speicher namens "Notizbuch" und dann Container im Speicher namens "Bilder" zum Speichern von Bildern und "Audio" zum Speichern von Audiodateien erstellen. Nachdem Sie die Container erstellt haben, können Sie einzelne BLOB-Dateien in diese hochladen. Weitere Informationen zum programmgesteuerten Bearbeiten von Blobs finden Sie unter [Verwenden von BLOB-Speicher aus .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/ "How to use Blob Storage from .NET").

Für den programmgesteuerten Zugriff auf Blobs in ASP.NET 5-Projekten müssen Sie die folgenden Elemente hinzuzufügen, wenn sie nicht bereits vorhanden sind.

1. Fügen Sie die folgenden Codenamespace-Deklarationen am Anfang aller C#-Dateien hinzu, mit denen Sie programmgesteuert auf Azure Storage zugreifen möchten.

		using Microsoft.Framework.ConfigurationModel;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		using System.Threading.Tasks;

2. Verwenden Sie den folgenden Code, um die Konfigurationseinstellung abzurufen.

		 Configuration = new Configuration()
                .AddJsonFile("config.json")
                .AddEnvironmentVariables();

##### Abrufen der Verbindungszeichenfolge des Speichers
Bevor Sie einen BLOB verwenden können, müssen Sie die Verbindungszeichenfolge für das Speicherkonto abrufen, in dem die BLOBs gespeichert werden. Sie können den Typ **CloudStorageAccount** verwenden, um Ihre Speicherkontoinformationen darzustellen. Wenn Sie ein ASP.NET 5-Projekt verwenden, können Sie die get-Methode des Konfigurationsobjekts verwenden, um Ihre Verbindungszeichenfolge für den Speicher und die Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen. Der folgende Code zeigt dies.

	CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
      config.Get("MicrosoftAzureStorage:<storageAccountName>_AzureStorageConnectionString"));

##### Erstellen eines Containers
Dateien werden in Ordnern gespeichert, Speicher-BLOBs analog dazu in Containern. Sie können ein **CloudBlobClient**-Objekt zum Verweisen auf einen vorhandenen Container verwenden, oder Sie können die Methode CreateCloudBlobClient() zum Erstellen eines neuen Containers aufrufen.

Der folgende Code zeigt, wie ein neuer BLOB-Speichercontainer erstellt wird. Der Code erstellt zuerst ein **BlobClient**-Objekt, damit Sie auf die Funktionen des Objekts zugreifen können, z. B. zum Erstellen eines Speichercontainers. Der Code versucht dann, auf einen Speichercontainer namens "mycontainer" zu verweisen. Wird kein Container mit diesem Namen gefunden, wird der Container erstellt.

**HINWEIS:** Die APIs, welche die Aufrufe für den Azure-Speicher in ASP.NET 5 ausführen, sind asynchron. Weitere Informationen finden Sie unter [Asynchrone Programmierung mit Async und Await](http://msdn.microsoft.com/library/hh191443.aspx). Im folgenden Code wird davon ausgegangen, dass Async-Programmierungsmethoden verwendet werden.

	// Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named "mycontainer."
    CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");

    // If "mycontainer" doesn't exist, create it.
    await container.CreateIfNotExistsAsync();    

Standardmäßig ist der neue Container privat, und Sie müssen Ihren Speicherzugriffsschlüssel angeben, um Blobs aus diesem Container herunterzuladen. Wenn Sie die Dateien im Container für alle Benutzer zur Verfügung stellen möchten, können Sie den Container mithilfe des folgenden Codes als öffentlich festlegen.

	await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

**HINWEIS:** Verwenden Sie zudem all diesen Code vor dem Code in den folgenden Abschnitten.

##### Hochladen eines Blobs in einen Container
Rufen Sie einen Containerverweis ab, und verwenden Sie diesen dann zum Abrufen eines BLOB-Verweises, um eine BLOB-Datei in einen Container hochzuladen. Sobald Sie über einen BLOB-Verweis verfügen, können Sie jeden Datenstrom in diesen hochladen, indem Sie die Methode **UploadFromStreamAsync()** aufrufen. Dieser Vorgang erstellt den BLOB, wenn dieser noch nicht vorhanden ist, oder überschreibt ihn, wenn er vorhanden ist. Im folgenden Beispiel wird gezeigt, wie ein Blob in einen bereits erstellten Container hochgeladen wird.

	// Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");            

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named "myfile".
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

##### Auflisten der Blobs in einem Container
Um die Blobs in einem Container aufzuführen, müssen Sie zuerst einen Containerverweis abrufen. Anschließend können Sie die Methode **ListBlobsSegmentedAsync()** des Containers aufrufen, um die darin enthaltenen BLOBs und/oder Verzeichnisse abzurufen. Damit Sie auf die zahlreichen Eigenschaften und Methoden für ein zurückgegebenes **IListBlobItem**-Objekt zugreifen können, müssen Sie es in ein **CloudBlockBlob**-, **CloudPageBlob**- oder **CloudBlobDirectory**-Objekt umwandeln. Wenn der BLOB-Typ unbekannt ist, können Sie eine Typüberprüfung ausführen, um zu bestimmen, in welchen Typ die Umwandlung erfolgen soll. Im folgenden Code wird gezeigt, wie der URI der einzelnen Elemente in einem Container abgerufen und ausgegeben wird:

	BlobContinuationToken token = null;
        do
        {
            BlobResultSegment resultSegment = await container.ListBlobsSegmentedAsync(token);
            token = resultSegment.ContinuationToken;

            foreach (IListBlobItem item in resultSegment.Results)
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
        } while (token != null);

Es gibt auch andere Möglichkeiten, den Inhalt eines BLOB-Containers aufzulisten. Weitere Informationen finden Sie unter [Verwenden von BLOB-Speicher aus .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#list-blob).

##### Herunterladen eines Blobs
Wenn Sie einen BLOB herunterladen möchten, rufen Sie zuerst einen Verweis auf den BLOB ab, und rufen Sie dann die Methode **DownloadToStreamAsync()** auf. Im folgenden Beispiel wird die Methode **DownloadToStreamAsync()** verwendet, um den Inhalt des BLOBs in ein Datenstromobjekt zu übertragen, das anschließend als eine lokale Datei gespeichert werden kann.

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named "myfile".
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	await blockBlob.DownloadToStreamAsync(fileStream);
	}

Es gibt auch andere Möglichkeiten zum Speichern von BLOBs als Dateien. Weitere Informationen finden Sie unter [Verwenden von BLOB-Speicher aus .NET](http://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-blobs/#download-blobs).

##### Löschen eines Blobs
Wenn Sie einen BLOB löschen möchten, rufen Sie zuerst einen Verweis auf den BLOB ab, und rufen Sie dann die Methode **DeleteAsync()** auf.

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	await blockBlob.DeleteAsync();

[Weitere Informationen zu Azure Storage](http://azure.microsoft.com/documentation/services/storage/)
Weitere Informationen finden Sie unter [Durchsuchen von Speicherressourcen mit Server-Explorer](http://msdn.microsoft.com/library/azure/ff683677.aspx) und [ASP.NET 5](http://www.asp.net/vnext).
\<!--HONumber=42-->
