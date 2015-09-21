<properties
	pageTitle="Erste Schritte mit dem BLOB-Speicher und verbundenen Visual Studio-Diensten (ASP.NET 5) | Microsoft Azure"
	description="Erfahren Sie etwas über die ersten Schritte mit Azure BLOB-Speicher in einem ASP.NET 5-Projekt in Visual Studio, nachdem Sie mithilfe von verbundenen Visual Studio-Diensten ein Speicherkonto erstellt haben."
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
	ms.date="09/03/2015"
	ms.author="patshea123"/>

# Erste Schritte mit Azure-BLOB-Speicher und verbundenen Visual Studio-Diensten (ASP.NET 5)

> [AZURE.SELECTOR]
> - [Getting started](vs-storage-aspnet5-getting-started-blobs.md)
> - [What happened](vs-storage-aspnet5-what-happened.md)

> [AZURE.SELECTOR]
> - [Blobs](vs-storage-aspnet5-getting-started-blobs.md)
> - [Queues](vs-storage-aspnet5-getting-started-queues.md)
> - [Tables](vs-storage-aspnet5-getting-started-tables.md)

##Übersicht

Dieser Artikel beschreibt die ersten Schritte beim Verwenden von Azure-BLOB-Speicher in Visual Studio, nachdem Sie über das Visual Studio-Dialogfeld "Verbundene Dienste hinzufügen" in einem ASP.NET 5-Projekt ein Azure-Speicherkonto erstellt oder auf ein solches Konto verwiesen haben.

Die BLOB-Speicherung in Azure ist ein Dienst zur Speicherung großer Mengen unstrukturierter Daten, auf die von überall auf der Welt über HTTP oder HTTPS zugegriffen werden kann. Ein einzelner BLOB kann eine beliebige Größe aufweisen. BLOBs können Bilder, Audio- und Videodateien, Rohdaten und Dokumentdateien enthalten. Dieser Artikel beschreibt die ersten Schritte mit dem BLOB-Speicher, nachdem Sie über das Visual Studio-Dialogfeld **Verbundene Dienste hinzufügen** ein Azure-Speicherkonto in einem ASP.NET 5-Projekt erstellt haben.

Dateien werden in Ordnern gespeichert, Speicher-BLOBs analog dazu in Containern. Nachdem Sie einen Speicher erstellt haben, erstellen Sie mindestens einen Container in dem Speicher. Beispielsweise können Sie in einem Speicher namens „Notizbuch“ Container im Speicher namens „Bilder“ erstellen, um Bilder zu speichern, und einen anderen namens „Audio“ erstellen, um Audiodateien zu speichern. Nachdem Sie die Container erstellt haben, können Sie einzelne BLOB-Dateien in diese hochladen. Weitere Informationen zum programmgesteuerten Bearbeiten von BLOBs finden Sie unter [Verwenden von BLOB-Speicher aus .NET](storage-dotnet-how-to-use-blobs.md "Verwenden des BLOB-Speichers mit .NET").



##Zugreifen auf BLOB-Container in Code

Für den programmgesteuerten Zugriff auf Blobs in ASP.NET 5-Projekten müssen Sie die folgenden Elemente hinzuzufügen, wenn sie nicht bereits vorhanden sind.

1. Fügen Sie die folgenden Namespace-Deklarationen zum Anfang aller C#-Dateien hinzu, in denen Sie programmgesteuert auf den Azure-Speicher zugreifen möchten:

		using Microsoft.Framework.Configuration;
		using Microsoft.WindowsAzure.Storage;
		using Microsoft.WindowsAzure.Storage.Blob;
		using System.Threading.Tasks;
		using LogLevel = Microsoft.Framework.Logging.LogLevel;

2. Rufen Sie ein **CloudStorageAccount**-Objekt ab, das die Informationen zu Ihrem Speicherkonto enthält. Verwenden Sie den folgenden Code, um Ihre Speicherverbindungszeichenfolge und Speicherkontoinformationen aus der Azure-Dienstkonfiguration abzurufen.

		 CloudStorageAccount storageAccount = CloudStorageAccount.Parse(
		   CloudConfigurationManager.GetSetting("<storage-account-name>_AzureStorageConnectionString"));

    **HINWEIS:** Verwenden Sie den gesamten obigen Code vor dem Code in den folgenden Abschnitten.


3. Verwenden Sie ein **CloudBlobClient**-Objekt zum Abrufen eines **CloudBlobContainer**-Verweises auf einen Container, der in Ihrem Speicherkonto vorhanden ist.

		// Create a blob client.
		CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

        // Get a reference to a container named “mycontainer.”
        CloudBlobContainer container = blobClient.GetContainerReference("mycontainer");



##Erstellen eines Containers in Code

Sie können den **CloudBlobClient** auch zum Erstellen eines Containers in Ihrem Speicherkonto verwenden. Dazu müssen Sie lediglich wie im folgenden Code einen Aufruf von **CreateIfNotExistsAsync** hinzufügen:

	// Create a blob client.
    CloudBlobClient blobClient = storageAccount.CreateCloudBlobClient();

    // Get a reference to a container named “my-new-container.”
    CloudBlobContainer container = blobClient.GetContainerReference("my-new-container");

    // If “mycontainer” doesn’t exist, create it.
    await container.CreateIfNotExistsAsync();


**HINWEIS:** Die APIs, die Aufrufe zum Azure-Speicher in ASP.NET 5 ausführen, sind asynchron. Unter [Asynchrone Programmierung mit Async und Await](http://msdn.microsoft.com/library/hh191443.aspx) finden Sie weitere Informationen. Im folgenden Code wird die Programmierung mit Async-Methoden angenommen.

Um die Dateien im Container für alle Benutzer zur Verfügung zu stellen, können Sie den Container mithilfe des folgenden Codes als öffentlich festlegen.

	await container.SetPermissionsAsync(new BlobContainerPermissions
    {
        PublicAccess = BlobContainerPublicAccessType.Blob
    });

##Hochladen eines Blobs in einen Container

Rufen Sie einen Containerverweis ab, und verwenden Sie diesen dann zum Abrufen eines BLOB-Verweises, um eine BLOB-Datei in einen Container hochzuladen. Sobald Sie über einen BLOB-Verweis verfügen, können Sie jeden Datenstrom in diesen hochladen, indem Sie die **UploadFromStreamAsync()**-Methode aufrufen. Dieser Vorgang erstellt den BLOB, wenn dieser noch nicht vorhanden ist, oder überschreibt ihn, wenn er vorhanden ist. Im folgenden Beispiel wird gezeigt, wie ein Blob in einen bereits erstellten Container hochgeladen wird.

	// Get a reference to a blob named "myblob".
    CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob");

    // Create or overwrite the "myblob" blob with the contents of a local file
    // named “myfile”.
    using (var fileStream = System.IO.File.OpenRead(@"path\myfile"))
    {
        await blockBlob.UploadFromStreamAsync(fileStream);
    }

##Auflisten der Blobs in einem Container
Um die Blobs in einem Container aufzuführen, müssen Sie zuerst einen Containerverweis abrufen. Anschließend können Sie die **ListBlobsSegmentedAsync()**-Methode des Containers aufrufen, um die darin enthaltenen BLOBs und/oder Verzeichnisse abzurufen. Damit Sie auf die zahlreichen Eigenschaften und Methoden für ein zurückgegebenes **IListBlobItem**-Objekt zugreifen können, müssen Sie es in ein **CloudBlockBlob**-, **CloudPageBlob**- oder **CloudBlobDirectory**-Objekt umwandeln. Wenn der BLOB-Typ unbekannt ist, können Sie eine Typüberprüfung ausführen, um zu bestimmen, in welchen Typ die Umwandlung erfolgen soll. Im folgenden Code wird gezeigt, wie der URI der einzelnen Elemente in einem Container abgerufen und ausgegeben wird:

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

Es gibt auch andere Möglichkeiten, den Inhalt eines BLOB-Containers aufzulisten. Weitere Informationen finden Sie unter [Verwenden von BLOB-Speicher aus .NET](storage-dotnet-how-to-use-blobs.md#list-the-blobs-in-a-container).

##Herunterladen eines Blobs
Wenn Sie einen BLOB herunterladen möchten, rufen Sie zuerst einen Verweis auf den BLOB ab, und rufen Sie dann die **DownloadToStreamAsync()**-Methode auf. Im folgenden Beispiel wird die **DownloadToStreamAsync()**-Methode verwendet, um den Inhalt des BLOBs in ein Datenstromobjekt zu übertragen, das anschließend als eine lokale Datei gespeichert werden kann.

	// Get a reference to a blob named "photo1.jpg".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("photo1.jpg");

	// Save the blob contents to a file named “myfile”.
	using (var fileStream = System.IO.File.OpenWrite(@"path\myfile"))
	{
    	await blockBlob.DownloadToStreamAsync(fileStream);
	}

Es gibt auch andere Möglichkeiten zum Speichern von BLOBs als Dateien. Weitere Informationen finden Sie unter [Verwenden von BLOB-Speicher aus .NET](storage-dotnet-how-to-use-blobs.md/#download-blobs).

##Löschen eines Blobs
Wenn Sie einen BLOB löschen möchten, rufen Sie zuerst einen Verweis auf den BLOB ab, und rufen Sie dann die **DeleteAsync()**-Methode auf.

	// Get a reference to a blob named "myblob.txt".
	CloudBlockBlob blockBlob = container.GetBlockBlobReference("myblob.txt");

	// Delete the blob.
	await blockBlob.DeleteAsync();

## Nächste Schritte

[AZURE.INCLUDE [vs-storage-dotnet-blobs-next-steps](../../includes/vs-storage-dotnet-blobs-next-steps.md)]

<!---HONumber=Sept15_HO2-->